# 기초 가이드

이 가이드는 유니티 예제 환경(3D Ball)에서 어떻게 모델을 학습시키는지, 학습된 모델을 어떻게 사용하는지 보여드립니다.

만약 [유니티 엔진](https://unity3d.com/kr/unity)에 익숙하지 않다면, 유니티의 기본 개념을 학습하기 위해 [Roll-a-ball튜토리얼](https://unity3d.com/kr/learn/tutorials/s/roll-ball-tutorial)을 추천드립니다.

## 유니티에서 ML-Agents Toolkit 설정

유니티에서 ML-Agents toolkit 을 사용하기 위해, 먼저 몇 가지 설정을 변경해야 합니다.

1. 유니티 실행
2. 프로젝트 다이얼로그 창 맨위의 **Open** 옵션을 선택하여 파일 다이얼로그를 여십시오.
3. 파일 다이얼로그에서, ML-Agents toolkit 프로젝트 안의 `UnitySDK` 폴더를 선택하고
   **Open**을 클릭하십시오.
4. **Edit** > **Project Settings** > **Player** 을 클릭하십시오.
5. 타겟으로 하는 **각각의** 플랫폼마다 (**PC, Mac과 Linux Standalone**,
   **iOS** 또는 **Android**):
    1. **Other Settings** 섹션을 확장하십시오.
    2. **Scripting Runtime Version** 을 **Experimental (.NET 4.6**
       **Equivalent or .NET 4.x Equivalent)** 으로 선택하십시오
6. **File** > **Save Project** 을 클릭하십시오.

## 학습된 모델 실행

에이전트를 위한 학습된 모델을 가지고 (`.nn` 파일) [Unity Inference Engine](Unity-Inference-Engine.md) 를 통해 유니티에서 모델을 실행시킵니다.
이 섹션에서는 3D 볼 예제를 위해 학습된 모델을 사용할 것입니다.

1. **Project** 창에서, `Assets/ML-Agents/Examples/3DBall/Scenes` 폴더로 들어가 `3DBall` 씬(scene) 파일을 엽니다.
2. **Project** 창에서, `Assets/ML-Agents/Examples/3DBall/Prefabs` 폴더로 들어갑니다. 
   `Game` 을 확장하고  `Platform` 프리팹을 클릭하십시오. **Inspector** 창에서 `Platform` 프리팹을 확인할 수 있습니다.
   
   **참고**: `3DBall` 씬 안의 platform들은 `Platform` 프리팹을 사용하여 생성되었습니다. 12개의 platform을 각각 업데이트하는 대신에 `Platform` 프리팹을 사용할 수 있습니다.
   
   ![Platform Prefab](images/platform_prefab.png)
   
3. **Project** 창에서, `Assets/ML-Agents/Examples/3DBall/Brains` 에 위치해있는  **3DBallLearning**  의 brain 을  **Inspector** 창에 있는 `Ball 3D Agent (Script)` 컴포넌트 안의  `Brain` 프로퍼티로 드래그 하십시오 .
   
   ![3dball learning brain](images/3dball_learning_brain.png)
   
4. **Hierarchy** 창에 있는 `Game` 아래에 있는 각각의 `Platform` 은 **3DBallLearning** 을 `Brain`으로 가지고 있습니다. __참고__ : 씬 계층(Scene Hierarchy) 안에 있는 탐색 바를 사용하여 여러개의 게임 오브젝트를 한번에 선택하여 수정할 수 있습니다. 
5. **Project** 창에서, `Assets/ML-Agents/Examples/3DBall/Brains`에 위치한 **3DBallLearning** Brain 을 클릭하십시오. **Inspector** 창에서 프로퍼티들을 볼 수 있을 것입니다.
6. **Project** 창에서, `Assets/ML-Agents/Examples/3DBall/TFModels` 폴더를 여십시오. 
7.  `Assets/ML-Agents/Examples/3DBall/TFModels` 폴더의 `3DBallLearning` 모델 파일을  **Inspector** 창의 **3DBallLearning** Brain 의  **Model**  필드로 드래그 하십시오. __참고:__ 모든 Brain은 `Model` 프로퍼티의 텐서플로우 모델로  `3DBallLearning` 을  가지고 있어야 합니다.
8. 이 모델(CPU 또는 GPU)을 사용하기 위해 **InferenceDevice** 을 선택하십시오. 
   _참고: 대부분의 ML-Agents toolkit 생성 모델에서 CPU가 빠릅니다_
9. **Play** 버튼을 누르면 학습된 모델을 사용하는 platform 들이 공에 균형을 맞추는 것을 볼 수 있습니다.
   

![Running a pre-trained model](images/running-a-pretrained-model.gif)

## Basics Jupyter Notebook 사용

The `notebooks/getting-started.ipynb` [Jupyter notebook](Background-Jupyter.md) 에는 Python API 기능에 대한 간단한 설명이 포함되어 있습니다. 또한 환경이 올바르게 설정되어있는지 확인하기 위한 테스트를 제공합니다.
`Basics` 에서, 만약 [실행파일을 이용](Learning-Environment-Executable.md)하고 싶다면 `env_name` 을 **유니티 실행파일 이름**으로 설정 하고, 유니티 에디터를 통해 현재 씬과 상호작용 하고싶을 경우 `None`으로 설정하십시오 . 

환경을 구축하거나 에디터내에서 상호작용 하기전에 유니티 에디터의 **Hierarchy**창에서  `Ball3DAcademy` 를 선택하고 `Ball 3D Academy` 컴포넌트 아래에 있는 `Control` 체크박스가 체크되어있는지 확인하십시오.

더 많은 정보와 문서는 [Python API](Python-API.md) 페이지에서 제공합니다.

## 강화 학습을 통한 Brain 학습

### 학습을 위한 환경 설정

학습을 위한 환경 설정을 위해, 어떤 에이전트가 학습에 기여하고 있고 어떤 Brain을 학습할 것인지 정해야합니다. `Learning Brain` 을 통해서만 학습을 진행할 수 있습니다.

1. 각 platform 에이전트는 할당된  `Learning Brain` 가 필요합니다. 이 예제에서는, 프리팹을 사용하여 platform 에이전트를 생성합니다. 각 platform의 모든 brain을 한번에 업데이트 하기 위해, platform 에이전트 프리팹을 업데이트 하기만 하면 됩니다. **Project** 창에서,  `Assets/ML-Agents/Examples/3DBall/Prefabs` 폴더를 여십시오. `Game` 을 확장하고 `Platform` 프리팹을 클릭하십시오.   **Inspector** 창에 `Platform` 프리팹이 보일 것입니다. **Project** 창에서,  `Assets/ML-Agents/Examples/3DBall/Brains` 에 위치한 **3DBallLearning** Brain을  **Inspector** 창의   `Ball 3D Agent (Script)` 컴포넌트 아래에 있는 `Brain` 프로퍼티로 드래그 하십시오.

   **참고**: 유니티 프리팹 시스템은 씬안에 있는 모든 에이전트 인스턴스를 수정할 것입니다. 만약 에이전트가 프리팹과 자동으로 동기화 되는 것을 원치 않는다면 **Inspector**창의 상단에서 Revert 버튼을 누르십시오.

2. **Hierarchy** 창에서, `Ball3DAcademy`를 선택하십시오.
3. **Project** 창에서, `Assets/ML-Agents/Examples/3DBall/Brains` 폴더를 열고 **3DBallLearning** Brain 을  **Inspector** 창의  `Ball3DAcademy`  안의  `Braodcast Hub`  아래에 있는 `Brains` 프로퍼티로 드래그 하십시오.  학습을 위해, `Control` 체크박스가 선택되어있는지 확인하십시오.

   **참고:** Brain을 에이전트에게 할당(Brain을 에이전트의 `Brain` 프로퍼티로 드래그) 하는 것은 Brain이 에이전트의 의사를 결정을 한다는 것을 의미합니다. 반면에 Brain을 Broadcast Hub로 드래그 하는 것은 파이썬 프로세스에 Brain을 노출 시키는 것을 의미합니다. The `Control` 체크 박스는 파이썬에 노출되는것 뿐만 아니라 파이썬 프로세스(학습을 위해 필요한)에 의해 Brain이 제어됨을 의미 합니다. 

![Set Brain to External](images/mlagents-SetBrainToTrain.png)

### 환경 학습

1. command 또는 윈도우 터미널 창을 여십시오.
2. ML-Agents toolkit 저장소가 복제되있는 폴더로 가십시오.
   **참고**: 만약 디폴트 [설치](Installation.md)를 따라했다면, 어느 디렉토리에서든 `mlagents-learn` 를 실행시킬 수 있어야합니다.
3. `mlagents-learn <trainer-config-path> --run-id=<run-identifier> --train` 를 실행하십시오
   설명:
   
    - `<trainer-config-path>` 는 트레이너 환경 설정의 상대/절대 경로입니다.  `MLAgentsSDK`에 포함된 예제 환경에서 사용된 기본 값은  `config/trainer_config.yaml`에서 확인할 수 있습니다.
    - `<run-identifier>` 다른 학습 실행 결과를 구분하기 위해 사용되는 문자열 입니다.
    - `--train` 은 `mlagents-learn` 가 학습 세션(추론이 아닌)을 실행하도록 지시합니다. 
4. 만약 ML-Agents 저장소를 복제했다면, 다음과 같이 간단히 실행할 수 있습니다.

      ```sh
      mlagents-learn config/trainer_config.yaml --run-id=firstRun --train
      ```

5. _"Start training by pressing the Play button in the Unity Editor"_  메세지가 화면에 표시될 때, 에디터에서 :arrow_forward: 버튼을 눌러 학습을 시작할 수 있습니다.
   
   **참고**: 또는, 편집기 대신 실행파일을 사용하여 학습을 할 수 있습니다. [이 페이지](Learning-Environment-Executable.md) 를 참고하여 빌드 방법과 실행파일을 사용하기 위한 방법을 확인하십시오. 

```console
ml-agents$ mlagents-learn config/trainer_config.yaml --run-id=first-run --train


                        ▄▄▄▓▓▓▓
                   ╓▓▓▓▓▓▓█▓▓▓▓▓
              ,▄▄▄m▀▀▀'  ,▓▓▓▀▓▓▄                           ▓▓▓  ▓▓▌
            ▄▓▓▓▀'      ▄▓▓▀  ▓▓▓      ▄▄     ▄▄ ,▄▄ ▄▄▄▄   ,▄▄ ▄▓▓▌▄ ▄▄▄    ,▄▄
          ▄▓▓▓▀        ▄▓▓▀   ▐▓▓▌     ▓▓▌   ▐▓▓ ▐▓▓▓▀▀▀▓▓▌ ▓▓▓ ▀▓▓▌▀ ^▓▓▌  ╒▓▓▌
        ▄▓▓▓▓▓▄▄▄▄▄▄▄▄▓▓▓      ▓▀      ▓▓▌   ▐▓▓ ▐▓▓    ▓▓▓ ▓▓▓  ▓▓▌   ▐▓▓▄ ▓▓▌
        ▀▓▓▓▓▀▀▀▀▀▀▀▀▀▀▓▓▄     ▓▓      ▓▓▌   ▐▓▓ ▐▓▓    ▓▓▓ ▓▓▓  ▓▓▌    ▐▓▓▐▓▓
          ^█▓▓▓        ▀▓▓▄   ▐▓▓▌     ▓▓▓▓▄▓▓▓▓ ▐▓▓    ▓▓▓ ▓▓▓  ▓▓▓▄    ▓▓▓▓`
            '▀▓▓▓▄      ^▓▓▓  ▓▓▓       └▀▀▀▀ ▀▀ ^▀▀    `▀▀ `▀▀   '▀▀    ▐▓▓▌
               ▀▀▀▀▓▄▄▄   ▓▓▓▓▓▓,                                      ▓▓▓▓▀
                   `▀█▓▓▓▓▓▓▓▓▓▌
                        ¬`▀▀▀█▓


INFO:mlagents.learn:{'--curriculum': 'None',
 '--docker-target-name': 'Empty',
 '--env': 'None',
 '--help': False,
 '--keep-checkpoints': '5',
 '--lesson': '0',
 '--load': False,
 '--no-graphics': False,
 '--num-runs': '1',
 '--run-id': 'first-run',
 '--save-freq': '50000',
 '--seed': '-1',
 '--slow': False,
 '--train': True,
 '--worker-id': '0',
 '<trainer-config-path>': 'config/trainer_config.yaml'}
INFO:mlagents.envs:Start training by pressing the Play button in the Unity Editor.
```

**참고**: 아나콘다를 사용 중이라면, ml-agents 환경을 먼저 활성화 시키는 것을 잊지 마십시오.

`mlagents-learn` 올바르게 실행되고 학습이 시작되었다면 다음과 같은 화면을 볼 수 있습니다:

```console
INFO:mlagents.envs:
'Ball3DAcademy' started successfully!
Unity Academy name: Ball3DAcademy
        Number of Brains: 1
        Number of Training Brains : 1
        Reset Parameters :

Unity brain name: 3DBallLearning
        Number of Visual Observations (per agent): 0
        Vector Observation space size (per agent): 8
        Number of stacked Vector Observation: 1
        Vector Action space type: continuous
        Vector Action space size (per agent): [2]
        Vector Action descriptions: ,
INFO:mlagents.envs:Hyperparameters for the PPO Trainer of brain 3DBallLearning:
        batch_size:          64
        beta:                0.001
        buffer_size:         12000
        epsilon:             0.2
        gamma:               0.995
        hidden_units:        128
        lambd:               0.99
        learning_rate:       0.0003
        max_steps:           5.0e4
        normalize:           True
        num_epoch:           3
        num_layers:          2
        time_horizon:        1000
        sequence_length:     64
        summary_freq:        1000
        use_recurrent:       False
        summary_path:        ./summaries/first-run-0
        memory_size:         256
        use_curiosity:       False
        curiosity_strength:  0.01
        curiosity_enc_size:  128
        model_path:	./models/first-run-0/3DBallLearning
INFO:mlagents.trainers: first-run-0: 3DBallLearning: Step: 1000. Mean Reward: 1.242. Std of Reward: 0.746. Training.
INFO:mlagents.trainers: first-run-0: 3DBallLearning: Step: 2000. Mean Reward: 1.319. Std of Reward: 0.693. Training.
INFO:mlagents.trainers: first-run-0: 3DBallLearning: Step: 3000. Mean Reward: 1.804. Std of Reward: 1.056. Training.
INFO:mlagents.trainers: first-run-0: 3DBallLearning: Step: 4000. Mean Reward: 2.151. Std of Reward: 1.432. Training.
INFO:mlagents.trainers: first-run-0: 3DBallLearning: Step: 5000. Mean Reward: 3.175. Std of Reward: 2.250. Training.
INFO:mlagents.trainers: first-run-0: 3DBallLearning: Step: 6000. Mean Reward: 4.898. Std of Reward: 4.019. Training.
INFO:mlagents.trainers: first-run-0: 3DBallLearning: Step: 7000. Mean Reward: 6.716. Std of Reward: 5.125. Training.
INFO:mlagents.trainers: first-run-0: 3DBallLearning: Step: 8000. Mean Reward: 12.124. Std of Reward: 11.929. Training.
INFO:mlagents.trainers: first-run-0: 3DBallLearning: Step: 9000. Mean Reward: 18.151. Std of Reward: 16.871. Training.
INFO:mlagents.trainers: first-run-0: 3DBallLearning: Step: 10000. Mean Reward: 27.284. Std of Reward: 28.667. Training.
```

### 학습 후

Ctrl+C 를 눌러 학습을 중단할 수 있으며 학습된 모델은`models/<run-identifier>/<brain_name>.nn` 에 있을 것입니다. `<brain_name>` 은 모델에 해당하는 Brain 이름 입니다.
(**Note:** Windows 에서 학습을 이른 시간에 종료했을 때 모델을 저장하는 과정에서 오류가 발생할 수 있는 버그가 있으므로 Step이 trainer_config.yaml에서 설정한 max_steps 파라미터에 도달할 때까지 기다리는것을 권장합니다.) 파일은 모델의 최신 체크포인트와 일치합니다. [위에](#running-a-pre-trained-model) 설명된 절차와 유사한 아래 절차를 통해 훈련된 모델을 학습 Brain에 포함시킬 수 있습니다.

1. 모델 파일을 `UnitySDK/Assets/ML-Agents/Examples/3DBall/TFModels/` 로 옮기십시오.
2. 유니티 에디터를 열고 위에 설명된 **3DBall** 씬을 선택하십시오.
3. 씬 hierarchy로부터 **3DBallLearning** Learning Brain 을 선택하십시오.
4. 에디터의 Project 창으로 부터 `<brain_name>.nn` 파일을 **3DBallLearning** 인스펙터 창의 **Model** placeholder 로 드래그 하십시오.
5. 씬 안의 Ball3DAcademy 를 선택하고 Control를 끄십시오, 각 platform의 brain은 제어를 되찾습니다.
6. 에디터의 상단의 :arrow_forward: 버튼을 누르십시오.

## 다음 단계

- the ML-Agents toolkit 에 대한 더 많은 정보 뿐만아니라 도움이 되는 배경 정보는 [ML-Agents Toolkit 개요](ML-Agents-Overview.md) 페이지를 확인하십시오.
- 3D 밸런스 볼 환경에 대한 더욱 자세한 설명은 [시작하기](Getting-Started-with-Balance-Ball.md) 페이지를 참고하십시오.
- 자신만의 학습 환경을 만들기 위한 소개 "Hello World" 를 보고싶다면 [새 학습 환경 만들기](Learning-Environment-Create-New.md) 페이지를 확인하십시오.
- YouTube 비디오 튜토리얼 시리즈를 보고싶다면 [기계 학습 에이전트 플레이리스트](https://www.youtube.com/playlist?list=PLX2vGYjWbI0R08eWQkO7nQkGiicHAX7IX) 페이지를 확인하십시오.
  
  ## 한글 번역

해당 문서의 한글 번역은 [장현준 (Hyeonjun Jang)]([https://github.com/janghyeonjun](https://github.com/janghyeonjun))에 의해 진행되었습니다. 
내용상 오류나 오탈자가 있는 경우 totok682@naver.com 으로 연락주시면 감사드리겠습니다.