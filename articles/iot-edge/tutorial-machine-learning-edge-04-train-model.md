---
title: '자습서: 모델 학습 및 배포 - Azure IoT Edge의 Machine Learning'
description: 이 자습서에서는 Azure Machine Learning을 사용하여 기계 학습 모델을 학습한 다음, Azure IoT Edge 모듈로 배포할 수 있는 컨테이너 이미지로 모델을 패키지화합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 3/24/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: cfb778a1a632dc17a9f50c7ea05debed0edb4fb6
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660250"
---
# <a name="tutorial-train-and-deploy-an-azure-machine-learning-model"></a>자습서: Azure Machine Learning 모델 학습 및 배포

> [!NOTE]
> 이 문서는 IoT Edge에서 Azure Machine Learning을 사용하는 방법에 대한 자습서 시리즈의 일부입니다. 이 문서로 바로 이동한 경우에는 학습 효과를 극대화할 수 있도록 시리즈의 [첫 번째 문서](tutorial-machine-learning-edge-01-intro.md)부터 시작하는 것이 좋습니다.

이 문서에서는 다음 작업을 수행합니다.

* Azure Notebooks를 사용하여 기계 학습 모델을 학습시킵니다.
* 학습된 모델을 컨테이너 이미지로 패키징합니다.
* 컨테이너 이미지를 Azure IoT Edge 모듈로 배포합니다.

Azure Notebooks는 기계 학습 모델을 실험, 학습 및 배포하는 데 사용되는 기본 블록인 Azure Machine Learning 작업 영역을 활용합니다.

이 문서의 단계는 일반적으로 데이터 과학자가 수행할 수 있습니다.

## <a name="set-up-azure-notebooks"></a>Azure Notebooks 설정

Azure Notebooks를 사용하여 2개의 Jupyter Notebook 및 지원 파일을 호스팅합니다. 여기에서는 Azure Notebooks 프로젝트를 만들고 구성합니다. Jupyter 및/또는 Azure Notebooks를 사용한 적이 없는 경우, 다음과 같은 소개 문서를 참조하세요.

* **빠른 시작:** [Notebook 만들기 및 공유](../notebooks/quickstart-create-share-jupyter-notebook.md)
* **자습서:** [Python을 사용하여 Jupyter Notebook을 만들고 실행](../notebooks/tutorial-create-run-jupyter-notebook.md)

Azure Notebooks를 사용하면 연습 환경을 일관되게 유지할 수 있습니다.

> [!NOTE]
> 일단 설치하면, Azure Notebooks 서비스를 원하는 머신에서 액세스할 수 있습니다. 설치하는 동안, 필요한 파일이 모두 있는 개발 VM을 사용해야 합니다.

### <a name="create-an-azure-notebooks-account"></a>Azure Notebooks 계정 만들기

Azure Notebooks를 사용하려면 계정을 만들어야 합니다. Azure Notebook 계정은 Azure 구독에서 독립적입니다.

1. [Azure Notebooks](https://notebooks.azure.com)로 이동합니다.

1. 페이지의 오른쪽 위에서 **로그인**을 클릭합니다.

1. 회사 또는 학교 계정(Azure Active Directory)이나 개인 계정(Microsoft 계정)으로 로그인합니다.

1. Azure Notebooks를 전에 사용한 적이 없으면, Azure Notebooks 앱에 액세스 권한을 부여하라는 메시지가 표시됩니다.

1. Azure Notebooks에 대한 사용자 ID를 만듭니다.

### <a name="upload-jupyter-notebook-files"></a>Jupyter Notebook 파일 업로드

샘플 Notebook 파일을 새 Azure Notebooks 프로젝트에 업로드합니다.

1. 새 계정의 사용자 페이지 맨 위에 있는 메뉴 모음에서 **내 프로젝트**를 선택합니다.

1. **+** 단추를 선택하여 새 프로젝트를 추가합니다.

1. **새 프로젝트 만들기** 대화 상자에서 **프로젝트 이름**을 제공합니다. 

1. 프로젝트를 공개하거나 추가 정보가 있을 필요는 없으므로 **공개** 및 **추가 정보**를 선택 취소된 상태로 둡니다.

1. **만들기**를 선택합니다.

1. **업로드**(위쪽 화살표 아이콘)와 **컴퓨터에서**를 차례로 선택합니다.

1. **파일 선택**을 선택합니다.

1. **C:\source\IoTEdgeAndMlSample\AzureNotebooks**로 이동합니다. 목록에서 모든 파일을 선택하고 **열기**를 클릭합니다.

1. **이 파일의 콘텐츠를 신뢰합니다** 확인란을 선택합니다.

1. **업로드**를 선택하여 업로드를 시작하고 프로세스가 완료되면 **완료**를 선택합니다.

### <a name="azure-notebook-files"></a>Azure Notebook 파일

Azure Notebooks 프로젝트에 업로드한 파일을 검토해 보겠습니다. 자습서의 이 부분에 나오는 활동은 몇 가지 지원 파일을 사용하는 2개의 Notebook 파일에 적용됩니다.

* **01-turbofan\_regression.ipynb:** 이 Notebook에서는 Machine Learning Service 작업 영역을 사용하여 기계 학습 실험을 생성 및 실행합니다. 이 Notebook은 대략적으로 다음 단계를 수행합니다.

  1. 디바이스 도구에서 생성한 Azure Storage 계정에서 데이터를 다운로드합니다.
  1. 데이터를 검색하고 준비한 다음, 데이터를 사용하여 분류자 모델을 학습시킵니다.
  1. 테스트 데이터 세트(Test\_FD003.txt)를 사용하여 실험에서 모델을 평가합니다.
  1. Machine Learning Service 작업 영역에 최상의 분류자 모델을 게시합니다.

* **02-turbofan\_deploy\_model.ipynb:** 이 Notebook은 이전 Notebook에서 만든 모델을 사용하여 Azure IoT Edge 디바이스에 배포할 준비가 된 컨테이너 이미지를 만듭니다. 이 Notebook은 다음 단계를 수행합니다.

  1. 모델에 대한 채점 스크립트를 만듭니다.
  1. Machine Learning Service 작업 영역에 저장된 분류자 모델을 사용하여 컨테이너 이미지를 생성합니다.
  1. 이미지를 Azure Container Instance에 웹 서비스로 배포합니다.
  1. 웹 서비스를 사용하여 모델과 이미지가 예상대로 작동하는지 확인합니다. 검사된 이미지는 이 자습서의 [사용자 지정 IoT Edge 모듈 만들기 및 배포](tutorial-machine-learning-edge-06-custom-modules.md) 부분에서 IoT Edge 디바이스에 배포됩니다.

* **Test\_FD003.txt:** 이 파일은 학습된 분류자의 유효성을 검사할 때 테스트 세트로 사용할 데이터를 포함합니다. 단순성을 위해, 원래 컨테스트에 제공된 테스트 데이터가 테스트 세트로 사용됩니다.

* **RUL\_FD003.txt:** 이 파일은 Test\_FD003.txt 파일에 있는 각 디바이스의 마지막 주기에 대한 RUL(잔여 수명)을 포함합니다. 데이터에 대한 자세한 설명은 C:\\source\\IoTEdgeAndMlSample\\data\\Turbofan에서 readme.txt 및 Damage Propagation Modeling.pdf 파일을 참조하세요.

* **Utils.py:** 데이터 작업을 위한 일련의 Python 유틸리티 함수를 포함합니다. 첫 번째 Notebook에는 함수에 대한 자세한 설명이 있습니다.

* **README.md:** Notebook 사용을 설명하는 추가 정보입니다.  

## <a name="run-azure-notebooks"></a>Azure Notebooks 실행

이제 프로젝트를 만들었으므로 Notebook을 실행할 수 있습니다. 

1. 프로젝트 페이지에서 **01-turbofan\_regression.ipynb**를 선택합니다.

    ![실행할 첫 번째 Notebook 선택](media/tutorial-machine-learning-edge-04-train-model/select-turbofan-regression-notebook.png)

1. Notebook이 **신뢰할 수 없음**으로 나열되면 Notebook의 오른쪽 위에서 **신뢰할 수 없음** 위젯을 클릭합니다. 대화 상자가 열리면, **신뢰**를 선택합니다.

1. 최상의 결과를 위해 각 셀에 대한 설명서를 읽고 개별적으로 실행합니다. 도구 모음에서 **실행**을 선택합니다. 나중에 여러 셀을 실행하는 것이 편리한 것을 알 수 있습니다. 업그레이드 및 사용 중단 경고는 무시해도 됩니다.

    셀이 실행 중이면 대괄호 사이에 별표가 표시됩니다([\*]). 셀 작업이 완료되면 별표가 숫자로 바뀌고 관련된 출력이 나타납니다. Notebook의 셀은 순차적으로 작성되며 한 번에 하나만 실행할 수 있습니다.

    또한 **셀** 메뉴의 실행 옵션을 사용할 수도 있습니다 `Ctrl` + `Enter`를 사용하여 셀을 실행할 수 있고, `Shift` + `Enter`를 사용하여 셀을 실행하고 다음 셀로 이동할 수 있습니다.

    > [!TIP]
    > 일관된 셀 작업을 위해 브라우저의 여러 탭에서 동일한 노트북을 실행하지 마세요.

1. **글로벌 속성 설정** 지침을 따르는 셀에서 Azure 구독, 설정 및 리소스에 값을 채웁니다. 그런 다음, 셀을 실행합니다.

    ![Notebook에서 글로벌 속성 설정](media/tutorial-machine-learning-edge-04-train-model/set-global-properties.png)

1. **Workspace details**(작업 영역 세부 정보) 이전의 셀에서, 실행 한 후 인증을 위해 로그인하도록 지시하는 링크를 찾습니다.

    ![디바이스 인증을 위한 로그인 프롬프트](media/tutorial-machine-learning-edge-04-train-model/sign-in-prompt.png)

    링크를 열고 지정된 코드를 입력합니다. 이 로그인 절차는 Microsoft Azure 플랫폼 간 명령줄 인터페이스를 사용하여 Azure 리소스에 액세스하는 Jupyter Notebook을 인증합니다.  

    ![디바이스 확인 시 애플리케이션 인증](media/tutorial-machine-learning-edge-04-train-model/cross-platform-cli.png)

1. **Explore the results**(결과 탐색) 이전 셀에서 실행 ID의 값을 복사하고 **Reconstitute a run**(실행 재구성) 다음의 셀에 실행 ID에 대한 값을 붙여넣습니다.

   ![셀 사이 실행 ID 복사](media/tutorial-machine-learning-edge-04-train-model/automl-id.png)

1. 노트북의 나머지 셀을 실행합니다.

1. 노트북을 저장하고 프로젝트 페이지로 돌아갑니다.

1. **02-turbofan\_deploy\_model.ipynb**를 열고 각 셀을 실행합니다. **작업 영역 구성** 다음에 나오는 셀에서 인증하려면 로그인해야 합니다.

1. 노트북을 저장하고 프로젝트 페이지로 돌아갑니다.

### <a name="verify-success"></a>성공 확인

Notebook이 성공적으로 완료되었는지 확인하려면 몇 가지 항목이 만들어졌는지 확인합니다.

1. Azure Notebooks 프로젝트 페이지에서 마침표로 시작하는 항목 이름이 표시되도록 **숨긴 항목 표시**를 선택합니다.

1. 다음 파일이 생성되었는지 확인합니다.

    | 파일 | Description |
    | --- | --- |
    | ./aml_config/.azureml/config.json | Azure Machine Learning 작업 영역을 만드는 데 사용되는 구성 파일입니다. |
    | ./aml_config/model_config.json | Azure의 **turbofanDemo** Machine Learning 작업 영역에 모델을 배포하는 데 필요한 구성 파일입니다. |
    | myenv.yml| 배포된 Machine Learning 모델의 종속성에 대한 정보를 제공합니다.|

1. 다음 Azure 리소스가 생성되었는지 확인합니다. 일부 리소스 이름에는 임의의 문자가 추가됩니다.

    | Azure 리소스 | 속성 |
    | --- | --- |
    | Machine Learning 작업 영역 | turborfanDemo |
    | Container Registry | turbofandemoxxxxxxxx |
    | Application Insights | turbofaninsightxxxxxxxx |
    | Key Vault | turbofankeyvaultbxxxxxxxx |
    | 스토리지 | turbofanstoragexxxxxxxxx |

### <a name="debugging"></a>디버깅

디버깅을 위해 노트북에 Python 문(예: `print()` 명령)을 삽입하여 값을 표시할 수 있습니다. 정의되지 않은 변수 또는 개체가 표시되는 경우 처음으로 선언되거나 인스턴스화된 셀을 실행합니다.

노트북을 다시 실행해야 하는 경우 이전에 만든 파일과 Azure 리소스를 삭제해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Notebooks에서 실행되는 2개의 Jupyter Notebook을 통해 turbofan 디바이스의 데이터를 사용하여 RUL(잔여 수명) 분류자를 학습하고, 분류자를 모델로 저장하고, 컨테이너 이미지를 만들고, 이미지를 웹 서비스로 배포하여 테스트했습니다.

계속해서 다음 문서를 진행하면 IoT Edge 디바이스를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [IoT Edge 디바이스 구성](tutorial-machine-learning-edge-05-configure-edge-device.md)
