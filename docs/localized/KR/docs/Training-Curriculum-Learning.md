# 커리큘럼 러닝을 통한 에이전트 학습

## 샘플 환경

에이전트가 목적지가 도달하기 위해 높은 벽을 뛰어넘어야 되는 환경을 생각해보겠습니다. 이 문제를 해결하기 위해서 에이전트는 랜덤하게 움직이는 행동을 취하는 것부터 학습을 시작할 것입니다. 하지만 랜덤한 행동만으로 에이전트가 벽을 뛰어넘고 목적지에 도달하는 것은 아주 드물게 일어나거나 아예 발생하지 않을 수 있습니다. 만약 장애물이 없는 환경에서 에이전트가 골로 이동해야 하는 것과 같은 더 간단한 환경에서 학습을 시작한다면 에이전트는 쉽게 문제를 해결할 수 있을 것입니다. 이런 쉬운 문제부터 벽의 크기를 조금씩 늘려가면서 문제의 난이도를 조금씩 더해갑니다. 이렇게 학습을 수행하면 에이전트가 쉬운 문제부터 순차적으로 해결해나가면서 처음에 이야기했던 높은 벽의 문제도 해결을 할 수 있게 됩니다. 우리는 이런 환경을 ML-Agents 툴킷 0.2에 **Wall Jump**라는 이름의 환경으로 추가하였습니다. 

![Wall](images/curriculum.png)

_커리큘럼 학습의 시나리오는 목적지까지 가는 경로에 존재하는 벽 장애물의 높이를 점차적으로 증가시키면서 에이전트를 학습하는 것 입니다._

아래의 그림과 같은 두개의 학습 곡선을 살펴보겠습니다. 각각은 동일한 학습 파라미터를 통해 PPO 알고리즘으로 학습한 에이전트의 시간에 따른 보상을 나타냅니다. 두 그래프의 차이점은 한 에이전트는 벽이 처음부터 높은 상태로 학습을 수행하였고 다른 에이전트는 커리큘럼 학습을 통해 학습을 수행했다는 것입니다. 그래프를 통해 볼 수 있듯이 커리큘럼을 사용하지 않은 에이전트의 경우 커리큘럼을 사용한 에이전트에 비해 학습 성능이 많이 떨어지는 것을 확인할 수 있습니다. 이를 통해 우리는 잘 만들어진 커리큘럼 환경에서 학습된 에이전트는 굉장히 어려운 문제의 경우에도 잘 해결한다는 것을 살펴볼 수 있습니다.  

![Log](images/curriculum_progress.png)

## 사용법

환경의 각 브레인은 고유한 커리큘럼을 가집니다. 이런 커리큘럼들을 우리는 메타커리큘럼(metacurriculum)이라고 부릅니다. 메타커리큘럼은 다른 브레인들이 동일한 환경내에서 다른 커리큘럼들을 따르도록 할 수 있습니다. 

### 메타커리큘럼 정의

먼저 커리큘럼 학습을 사용할 환경을 위해 `config/curricula/` 내부에 폴더를 생성합니다. 예를 들어, 만약 우리가 Wall Jump를 위한 메타커리큘럼을 만들었으면 `config/curricula/wall-jump/`를 생성합니다. 우리는 커리큘럼들을 이 폴더 안에 넣을 것입니다. 

### 커리큘럼 정의

커리큘럼을 정의하기 위해서 먼저 환경의 어떤 파라미터들을 변경시킬지 결정합니다. Wall Jump 환경의 경우 벽의 높이를 다양하게 변경할 것입니다. 이를 위해 우리는 씬의 아카데미 오브젝트에서 `리셋 파라미터 (Reset Parameter)`를 사용할 것입니다. 이 리셋 파라미터는 파이썬 API를 통해 변경이 가능한 값입니다. 이 값을 직접 조절하기보다는 JSON 파일을 생성하여 커리큘럼의 구조를 설명합니다. 이것의 내부에서 우리는 트레이닝 스텝의 진행률이나 에이전트가 받은 평균 보상 등을 이용하여 어떤 시점에서 벽의 높이를 조절할지 정의할 수 있습니다. 아래는 Wall Jump 환경에서 BigWallBrain을 위한 커리큘럼의 예시입니다. 

```json
{
    "measure" : "progress",
    "thresholds" : [0.1, 0.3, 0.5],
    "min_lesson_length" : 100,
    "signal_smoothing" : true,
    "parameters" :
    {
        "big_wall_min_height" : [0.0, 4.0, 6.0, 8.0],
        "big_wall_max_height" : [4.0, 7.0, 8.0, 8.0]
    }
}
```

* `measure` - 학습 진행 상황을 측정하기 위한 대상
  * `reward` - 획득한 보상을 사용
  * `progress` - 현재 스텝 / 최대 스텝의 비율을 사용
* `thresholds` (실수 배열 (float array)) - 커리큘럼 학습 레벨을 올리기 위한 `measure` 값의 기준 
* `min_lesson_length` (정수) - 커리큘럼 학습 레벨이 변경되기 전 수행해야하는 에피소드의 최소값. 만약 `measure` 가  `reward`로 설정된 경우 마지막 `min_lesson_length` 의 평균 누적 보상을 이용하여 학습 레벨을 변경할지 결정한다. 이 값은 음수가 아니어야한다.

  __중요!__: 기준값과 비교되는 평균 보상은 콘솔에 기록된 평균 보상과 다릅니다. 예를 들어 만약 `min_lesson_length` 이 `100`이면 지난 `100` 에피소드의 평균 누적 보상이 현재 기준값을 넘은 경우에 학습 레벨을 증가시킵니다. 콘솔에 기록되는 평균 보상의 경우 [trainer configuration 파일](Training-ML-Agents.md#training-config-file)의 `summary_freq` 파라미터에 의해 기록됩니다. 
* `signal_smoothing` (true/false) - 현재 진행률의 값과 이전 진행률의 값 사이에 가중치를 부여할지 여부를 결정합니다. 

  * 만약 `true` 인 경우, 가중치는 0.75 (현재 진행률) 0.25 (이전 진행률).
* `parameters` (dictionary 의 key:문자열, value:실수 배열) - 제어할 아카데미의 리셋 파라미터 (reset parameters)에 해당합니다. 각 배열의 길이는 기준값의 수보다 하나 더 커야합니다. 

커리큘럼이 정의되었으면 우리는 리셋 파라미터 (reset parameter)를 사용하여 Agent의 `AgentReset()` 함수로부터 환경을  정의하고 변경할 수 있습니다. [WallJumpAgent.cs](https://github.com/Unity-Technologies/ml-agents/blob/master/UnitySDK/Assets/ML-Agents/Examples/WallJump/Scripts/WallJumpAgent.cs)를 예시로 참고해주세요. 만약 아카데미의 __Max Steps__가 어떤 양수로 정의되지 않은 경우 환경은 절대 리셋되지 않습니다. 환경을 리셋하기 위해서 아카데미는 반드시 리셋되어야합니다. 

우리는 이 파일을 우리의 metacurriculum 폴더에 해당하는 브레인의 이름으로 저장을 할 것입니다. 예를 들어 Wall Jump 환경에서는 BigWallBrain과 SmallWallBrain이라는 두개의 브레인이 있습니다. 만약 우리가 BigWallBrain을 위한 커리큘럼을 정의하는 경우 우리는 `config/curricula/wall-jump/`에  `BigWallBrain.json` 을 저장할 것입니다. 
.

### 커리큘럼을 이용한 학습

메타커리큘럼과 커리큘럼들이 정의되었으면 우리는 `–curriculum` 플래그를 이용하여 메타커리큘럼 폴더를 지정하고 `mlagents-learn` 을 실행할 수 있으며 이를 통해 커리큘럼 학습을 이용하여 PPO 알고리즘을 학습할 수 있습니다. 예를 들어 Wall Jump 환경에서 커리큘럼 학습을 이용하여 에이전트를 학습하는 경우 우리는 다음의 명령을 실행하면 됩니다. 

```sh
mlagents-learn config/trainer_config.yaml --curriculum=config/curricula/wall-jump/ --run-id=wall-jump-curriculum --train
```

우리는 현재 커리큘럼 학습 레벨과 학습 진행 상황을 텐서보드를 이용해 살펴볼 수 있습니다. 



## 한글 번역

해당 문서의 한글 번역은 [민규식 (Kyushik Min)]([https://github.com/Kyushik](https://github.com/Kyushik))에 의해 진행되었습니다. 내용상 오류나 오탈자가 있는 경우 kyushikmin@gmail.com 으로 연락주시면 감사드리겠습니다.