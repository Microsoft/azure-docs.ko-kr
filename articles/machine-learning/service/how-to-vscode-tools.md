---
title: Visual Studio Code 사용
titleSuffix: Azure Machine Learning service
description: Visual Studio Code용 Azure Machine Learning을 설치하고 Azure Machine Learning에서 간단한 실험을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: bbeb0dbbd5e9c919eda4b298dc5bee31965e9bac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60818597"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Visual Studio Code용 Azure Machine Learning 시작

이 문서에서는 **Visual Studio Code용 Azure Machine Learning** 확장을 사용하여 VS Code(Visual Studio Code)에서 Azure Machine Learning 서비스로 기계 학습 및 딥 러닝 모델을 학습하고 배포하는 방법을 알아봅니다.

Azure Machine Learning 서비스는 로컬 및 원격 계산 대상에 실험을 실행 하기 위한 지원을 제공 합니다. 모든 실험에서 여러 다른 기법, 하이퍼 매개 변수 등을 반복적으로 시도하기 위해 여러 실행을 추적할 수 있습니다. Azure Machine Learning을 사용하여 사용자 지정 메트릭 및 실험 실행을 추적하여 데이터 과학을 재현하고 감사하도록 할 수 있습니다.

또한 테스트 및 프로덕션 요구에 맞게 이러한 모델을 배포할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

+ Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

+ Visual Studio Code를 설치해야 합니다. 데스크톱에서 실행되는 간단하지만 효율적인 소스 코드 편집기인 VS Code는 Python 등을 기본적으로 지원합니다.  [VS Code 설치 방법에 대해 알아보세요](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Python 3.5 이상을 설치합니다](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>VS Code용 Azure Machine Learning 확장 설치

**Azure Machine Learning** 확장을 설치할 때 인터넷에 액세스할 수 있으면 두 개의 확장이 추가로 자동 설치됩니다. 이 두 확장은 [Azure 계정](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 확장 및 [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) 확장입니다.

Azure Machine Learning을 사용하려면 VS Code를 Python IDE로 전환해야 합니다. [Visual Studio Code에서 Python](https://code.visualstudio.com/docs/languages/python)을 사용하려면 Azure Machine Learning 확장과 함께 자동으로 설치되는 Microsoft Python 확장이 필요합니다. VS Code를 효율적인 IDE로 전환하는 이 확장은 다양한 Python 인터프리터가 포함된 모든 운영 체제에서 작동합니다. 이 확장은 VS Code의 모든 기능을 활용하여 자동 완성과 IntelliSense, Lint, 디버깅, 단위 테스트 기능은 물론 가상 환경과 conda 환경을 비롯한 Python 환경 간을 손쉽게 전환하는 기능도 제공합니다. Python 코드 편집/실행/디버깅 연습을 제공하는 [Python Hello World 자습서](https://code.visualstudio.com/docs/python/python-tutorial)를 참조하세요.

**Azure Machine Learning 확장을 설치하려면**

1. VS Code를 시작합니다.

1. 브라우저에서 [Visual Studio Code용 Azure Machine Learning(미리 보기)](https://aka.ms/vscodetoolsforai) 확장으로 이동합니다.

1. 웹 페이지에서 **설치**를 클릭합니다. 

1. 확장 탭에서 **설치**를 클릭합니다.

1. 확장에 대 한 VS Code에서 시작 탭이 열립니다 하 고 (아래 그림의 빨간색 상자에 설명 되어 있음) Azure 기호 작업 표시줄에 추가 됩니다.

   ![Visual Studio Code 작업 막대의 Azure 아이콘](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. 대화 상자에서 **로그인**을 클릭하고 화면의 메시지에 따라 Azure에 인증합니다. 
   
   VS Code용 Azure Machine Learning 확장과 함께 설치되는 Azure 계정 확장을 통해 Azure 계정에 인증할 수 있습니다. 명령 목록은 [Azure 계정 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) 페이지를 참조하세요.

> [!Tip] 
> [VS Code용 IntelliCode 확장(미리 보기)](https://go.microsoft.com/fwlink/?linkid=2006060)을 확인해 보세요. IntelliCode는 현재 코드 컨텍스트를 기준으로 하여 가장 관련성이 높은 자동 완성을 유추하는 기능과 같은 Python의 IntelliSense용 AI 지원 기능 집합을 제공합니다.

## <a name="azure-ml-sdk-installation"></a>Azure ML SDK 설치

1. Python 3.5 이상이 설치되어 있으며 VS Code에서 인식되는지 확인합니다. 지금 Python을 설치하는 경우 VS Code를 다시 시작한 후에 https://code.visualstudio.com/docs/python/python-tutorial의 지침에 따라 Python 인터프리터를 선택합니다.

1. 통합 터미널 창에서 사용할 Python 인터프리터를 지정합니다. **Enter** 키를 눌러 기본 Python 인터프리터를 사용할 수도 있습니다.

   ![인터프리터 선택](./media/vscode-tools-for-ai/python.png)

1. 창의 오른쪽 아래 모서리에 Azure ML SDK가 자동으로 설치된다는 알림이 표시됩니다.    개인 로컬 Python 환경에 Azure Machine Learning 서비스를 사용 하 여 작업에 대 한 필수 Visual Studio Code 구성 요소가 생성 됩니다.

   ![Python용 Azure Machine Learning SDK 설치](./media/vscode-tools-for-ai/runtimedependencies.png)

## <a name="get-started-with-azure-machine-learning"></a>Azure Machine Learning 시작

VS Code를 사용하여 기계 학습 모델 학습 및 배포를 시작하려면 클라우드에서 모델과 리소스를 포함할 [Azure Machine Learning Service 작업 영역](concept-azure-machine-learning-architecture.md#workspace)을 만들어야 합니다. 작업 영역을 만들고 이 작업 영역에 첫 번째 실험을 만드는 방법은 다음과 같습니다.

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

   [![설치](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Azure 구독을 마우스 오른쪽 단추로 클릭하고 **작업 영역 만들기**를 선택합니다. 목록이 표시됩니다. 애니메이션 이미지에서 구독 이름은 ‘평가판’이고 작업 영역은 ‘TeamWorkspace’입니다. 

1. 목록에서 기존 리소스 그룹을 선택하거나 명령 팔레트에서 마법사를 사용하여 새 그룹을 만듭니다.

1. 필드에 새 작업 영역의 고유하고 명확한 이름을 입력합니다. 스크린샷에서 작업 영역 이름은 ‘TeamWorkspace’입니다.

1. Enter 키를 누르면 새 작업 영역이 생성됩니다. 이 작업 영역은 트리에서 구독 이름 아래에 표시됩니다.

1. 실험 노드를 마우스 오른쪽 단추로 클릭하고 컨텍스트 메뉴에서 **실험 만들기**를 선택합니다.  실험은 Azure Machine Learning을 사용하는 실행을 추적합니다.

1. 필드에 실험 이름을 입력합니다. 스크린샷에서 실험 이름은 ‘MNIST’입니다.
 
1. Enter 키를 누르면 새 실험이 생성됩니다. 이 실험은 트리에서 작업 영역 이름 아래에 표시됩니다.

1. 작업 영역에서 실험을 마우스 오른쪽 단추로 클릭하고 '활성 실험으로 설정'을 선택할 수 있습니다. **'활성'** 실험은 현재 사용 중인 실험이며, VS Code의 열린 폴더가 클라우드의 이 실험에 연결됩니다. 이 폴더에는 로컬 Python 스크립트가 포함됩니다.

   이제 각 실험이 실행되어 모든 주요 메트릭이 실험 기록에 저장되며, 학습시키는 모델이 Azure Machine Learning에 자동으로 업로드되어 실험 메트릭 및 로그와 함께 저장됩니다.

   [![VS Code에서 폴더 연결](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)


## <a name="create-and-manage-compute-targets"></a>계산 대상 만들기 및 관리

VS Code용 Azure Machine Learning을 사용하여 데이터를 준비하고, 모델을 학습하고, 로컬 및 원격 컴퓨팅 대상에 배포할 수 있습니다.

이 확장은 Azure Machine Learning을 위한 몇 가지 다른 원격 계산 대상을 지원합니다. Azure Machine Learning에 대해 [지원되는 전체 계산 대상 목록](how-to-set-up-training-targets.md)을 참조하세요.

### <a name="create-compute-targets-for-azure-machine-learning-in-vs-code"></a>VS Code에서 Azure Machine Learning의 계산 대상 만들기

**계산 대상을 만들려면**

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

2. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 애니메이션 이미지에서 구독 이름은 'Free Trial'이고 작업 영역은 'TeamWorkspace'입니다. 

3. 작업 영역 노드에서 **계산** 노드를 마우스 오른쪽 단추로 클릭하고 **계산 만들기**를 선택합니다.

4. 목록에서 계산 대상 유형을 선택합니다. 

5. 명령 팔레트에서 Virtual Machine 크기를 선택합니다.

6. 명령 팔레트에서 필드에 컴퓨팅 대상의 이름을 입력합니다. 

7. 새 탭에서 열리는 JSON 구성 파일의 고급 속성을 지정합니다. 최대 노드 수와 같은 속성을 지정할 수 있습니다.

8. 컴퓨팅 대상 구성을 마치면 화면의 오른쪽 하단 모서리에 있는 **제출**을 클릭합니다.

Azure Machine Learning 컴퓨팅(AMLCompute)을 만드는 예제는 다음과 같습니다. [![VS Code에서 AML 컴퓨팅 만들기](./media/vscode-tools-for-ai/CreateARemoteCompute.gif)](./media/vscode-tools-for-ai/CreateARemoteCompute.gif#lightbox)

#### <a name="the-run-configuration-file"></a>‘실행 구성’ 파일

VS Code 확장은 로컬 컴퓨터의 **로컬** 및 **docker** 환경의 로컬 컴퓨팅 대상 및 실행 구성을 자동으로 만듭니다. 실행된 구성 파일을 연결 된 계산 대상 노드 아래에 있습니다. 

## <a name="train-and-tune-models"></a>모델 학습 및 조정

VS Code용 Azure Machine Learning(미리 보기)을 사용하여 코드를 신속하게 반복하고, 단계별로 실행 및 디버그하고, 선택한 소스 코드 제어 솔루션을 사용할 수 있습니다. 

**Azure Machine Learning을 사용하여 실험을 로컬에서 실행하려면**

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 

1. 작업 영역 노드에서 **컴퓨팅** 노드를 확장하고 사용하려는 컴퓨팅의 **실행 구성**을 마우스 오른쪽 단추로 클릭합니다. 

1. **실험 실행**을 선택합니다.

1. 파일 탐색기에서 실행할 스크립트를 선택합니다. 

1. **실험 실행 보기**를 클릭하여 통합된 Azure Machine Learning 포털에서 실행을 모니터링하고 학습된 모델을 확인합니다.

로컬에서 실험을 실행하는 예제는 다음과 같습니다. [![로컬에서 실험 실행](./media/vscode-tools-for-ai/RunExperimentLocally.gif)](./media/vscode-tools-for-ai/RunExperimentLocally.gif#lightbox)

### <a name="use-remote-computes-for-experiments-in-vs-code"></a>VS Code에서 실험을 위해 원격 계산 사용

학습 시 원격 계산 대상을 사용하려면 실행 구성 파일을 만들어야 합니다. 이 파일은 Azure Machine Learning에 실험 실행 위치뿐만 아니라 환경 준비 방법도 알려줍니다.

#### <a name="the-conda-dependencies-file"></a>Conda 종속성 파일

기본적으로 새 conda 환경이 만들어지고, 설치 종속성이 관리됩니다. 그러나 `aml_config/conda_dependencies.yml` 파일에서 종속성 및 버전을 지정해야 합니다. 

다음은 기본 ‘aml_config/conda_dependencies.yml’의 일부입니다. 예를 들어 아래와 같이 'tensorflow=1.12.0'을 지정할 수 있습니다. 종속성 버전을 지정하지 않으면 최신 버전이 사용됩니다.  
구성 파일에서 추가 종속성을 추가할 수 있습니다.

```yaml
# The dependencies defined in this file will be automatically provisioned for runs with userManagedDependencies=False.

name: project_environment
dependencies:
  # The python interpreter version.

  # Currently Azure ML only supports 3.5.2 and later.

- python=3.6.2
- tensorflow=1.12.0

- pip:
    # Required packages for AzureML execution, history, and data preparation.

  - --index-url https://azuremlsdktestpypi.azureedge.net/sdk-release/Preview/E7501C02541B433786111FE8E140CAA1
  - --extra-index-url https://pypi.python.org/simple
  - azureml-defaults

```

**원격 컴퓨팅 대상에서 Azure Machine Learning을 사용하여 실험을 실행하려면**

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다. 

1. 편집기 창에서 Python 스크립트를 마우스 오른쪽 단추로 클릭하고 **AML: Azure에서 실험으로 실행**을 선택합니다. 

1. 명령 팔레트에서 컴퓨팅 대상을 선택합니다. 

1. 명령 팔레트에서 필드에 실행 구성 이름을 입력합니다. 

1. conda_dependencies.yml 파일을 편집하여 실험의 런타임 종속 항목을 지정한 후 화면의 오른쪽 하단 모서리에서 **제출**을 클릭합니다. 

1. **실험 실행 보기**를 클릭하여 통합된 Azure Machine Learning 포털에서 실행을 모니터링하고 학습된 모델을 확인합니다.

원격 컴퓨팅 대상에서 실험을 실행하는 예제는 다음과 같습니다. [![원격 대상에서 실험 실행](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif)](./media/vscode-tools-for-ai/runningOnARemoteTarget.gif#lightbox)


## <a name="deploy-and-manage-models"></a>모델 배포 및 관리
Azure Machine Learning을 사용하여 클라우드 및 에지에서 Machine Learning 모델을 배포 및 관리할 수 있습니다. 

### <a name="register-your-model-to-azure-machine-learning-from-vs-code"></a>VS Code에서 Azure Machine Learning에 모델 등록

모델을 학습했으므로 작업 영역에서 등록할 수 있습니다.
등록한 모델을 추적 및 배포할 수 있습니다.

**모델을 등록하려면**

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다.

1. 작업 영역 노드에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **모델 등록**을 선택합니다.

1. 명령 팔레트에서 필드에 모델 이름을 입력합니다. 

1. 목록에서 **모델 파일**(단일 모델용) 및 **모델 폴더**(Tensorflow와 같은 여러 파일을 포함하는 모델용) 중 어떤 항목을 업로드할지 선택합니다. 

1. 폴더 또는 파일을 선택합니다.

1. 모델 속성 구성을 마치면 화면의 오른쪽 하단 모서리에 있는 **제출**을 클릭합니다. 

AML에 모델을 등록하는 예제는 다음과 같습니다. [![AML에 모델 등록](./media/vscode-tools-for-ai/RegisteringAModel.gif)](./media/vscode-tools-for-ai/RegisteringAModel.gif#lightbox)


### <a name="deploy-your-service-from-vs-code"></a>VS Code에서 서비스 배포

VS Code를 사용하여 웹 서비스를 다음에 배포할 수 있습니다.
+ ACI(Azure Container Instance): 테스트용
+ AKS(azure Kubernetes Service): 프로덕션용 

ACI 컨테이너가 즉석에서 만들어지므로 테스트할 컨테이너를 미리 만들 필요가 없습니다. 그러나 AKS 클러스터는 미리 구성해야 합니다. 

일반적인 [Azure Machine Learning 웹 서비스 배포](how-to-deploy-and-where.md)에 대해 자세히 알아봅니다.

**웹 서비스를 배포하려면**

1. Visual Studio Code 작업 막대에서 Azure 아이콘을 클릭합니다. Azure: Machine Learning 사이드바가 나타납니다.

1. 트리 보기에서 Azure 구독 및 Azure Machine Learning 서비스 작업 영역을 확장합니다.

1. 작업 영역 노드에서 **모델** 노드를 확장합니다.

1. 배포하려는 모델을 마우스 오른쪽 단추로 클릭하고 상황에 맞는 메뉴에서 **등록된 모델에서 서비스 배포** 명령을 선택합니다.

1. 명령 팔레트에서 배포하려는 컴퓨팅 대상을 목록에서 선택합니다. 

1. 명령 팔레트에서 필드에 이 서비스의 이름을 입력합니다.  

1. 명령 팔레트에서 키보드의 Enter 키를 눌러 스크립트 파일을 찾아 선택합니다.

1. 명령 팔레트에서 키보드의 Enter 키를 눌러 conda 종속성 파일을 찾아 선택합니다.

1. 서비스 속성 구성을 마치면 화면의 오른쪽 하단 모서리에 있는 **제출**을 클릭하여 배포합니다. 이 서비스 속성 파일에서 사용하려는 로컬 Docker 파일 또는 schema.json 파일을 지정할 수 있습니다.

이제 웹 서비스가 배포됩니다.

웹 서비스를 배포하는 예제는 다음과 같습니다. [![웹 서비스 배포](./media/vscode-tools-for-ai/CreatingAnImage.gif)](./media/vscode-tools-for-ai/CreatingAnImage.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>바로 가기 키 사용

대다수 VS Code 기능과 마찬가지로 VS Code의 Azure Machine Learning 기능도 키보드에서 액세스할 수 있습니다. 기억해야 할 가장 중요한 키 조합은 명령 팔레트를 표시하는 Ctrl+Shift+P입니다. 명령 팔레트에서는 가장 흔히 수행하는 작업의 바로 가기 키를 포함하여 VS Code의 모든 기능에 액세스할 수 있습니다.

[![VS Code용 Azure Machine Learning의 바로 가기 키](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>다음 단계

VS Code 외부에서 Machine Learning을 사용한 학습을 연습하려면 [자습서: Azure Machine Learning을 사용하여 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.

로컬로 코드를 편집, 실행 및 디버그하는 방법을 연습하려면 [Python Hello World 자습서](https://code.visualstudio.com/docs/python/python-tutorial)를 참조하세요.
