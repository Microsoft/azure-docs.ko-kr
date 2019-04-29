---
title: 작업 영역 만들기
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning 서비스 작업 영역을 만들려면 Azure portal, SDK, 템플릿 또는 CLI를 사용 합니다. 이 작업 영역은 Azure Machine Learning 서비스를 사용할 때 만든 모든 아티팩트를 작업할 수 있는 중앙된 위치를 제공 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.author: sgilley
author: sdgilley
ms.date: 04/19/2019
ms.openlocfilehash: ca43a6cff6a32a30d93e42f6a6624439b2fe83a7
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766814"
---
# <a name="create-an-azure-machine-learning-service-workspace"></a>Azure Machine Learning 서비스 작업 영역 만들기

Azure Machine Learning 서비스를 사용 하려면 필요는 [ **Azure Machine Learning 서비스 작업 영역**](concept-azure-machine-learning-architecture.md#workspace)합니다.  이 작업 영역은 서비스에 대 한 최상위 수준 리소스 이며 만든 모든 아티팩트를 작업할 수 있는 중앙된 위치를 제공 합니다. 

이 문서에서는 이러한 방법 중 하나를 사용 하 여 작업 영역을 만드는 방법 알아보기: 
* 합니다 [Azure portal](#portal) 인터페이스
* [Azure Machine Learning Python for SDK](#sdk)
* Azure Resource Manager 템플릿
* [Azure 기계 학습 CLI](#cli)

다른 자습서 및 방법 문서에 필수 구성 요소로 여기의 단계를 사용 하 여 만든 작업 영역을 사용할 수 있습니다.

로컬 Python 환경에서 자동화 된 기계 학습을 설정 하는 스크립트를 사용 하려는 경우 참조 하는 [Azure/MachineLearningNotebooks GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning) 지침은.  

작업 영역을 만든 다음 Azure 리소스를 자동으로 (지역적으로 사용 가능한 경우) 추가 됩니다.
 
- [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)
- [Azure Storage](https://azure.microsoft.com/services/storage/)
- [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) 
- [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)

>[!Note]
>다른 Azure 서비스와 마찬가지로, 특정 한도 및 할당량이 Machine Learning과 관련돼 있습니다. [할당량 및 더 많은 요청 방법에 대해 알아보세요.](how-to-manage-quotas.md)


## <a name="prerequisites"></a>필수 조건
작업 영역을 만들려면 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 시작하기 전에 체험 계정을 만듭니다. [Azure Machine Learning Service의 평가판 또는 유료 버전](https://aka.ms/AMLFree)을 지금 사용해 보세요.

## <a name="portal"></a> Azure portal

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

만든 방법에 관계 없이 작업 영역을 볼 수 있습니다 합니다 [Azure portal](https://portal.azure.com/)합니다.  참조 [작업 영역을 보려면](how-to-manage-workspace.md#view) 세부 정보에 대 한 합니다.

## <a name="sdk"></a> Python SDK

Python SDK를 사용 하 여 작업 영역을 만듭니다. 먼저 SDK를 설치 해야 합니다.

> [!IMPORTANT]
> Azure 데이터 과학 Virtual Machine 또는 Azure Databricks를 사용 하는 경우에 SDK의 설치를 건너뜁니다.
> * 2018년 9월 27일 이후에 만들어진 Azure Data Science Virtual Machine은 Python SDK가 미리 설치된 상태로 제공됩니다. 로 시작 하 고 설치를 건너뜁니다 [SDK를 사용 하 여 작업 영역 만들기](#sdk-create)합니다.
> * Azure Databricks 환경에서 [Databricks 설치 단계](how-to-configure-environment.md#azure-databricks)를 대신 사용합니다.

>[!NOTE]
> 이러한 지침을 사용 하 여 설치 하 고 로컬 컴퓨터에서 SDK를 사용 합니다. 원격 가상 컴퓨터에 Jupyter를 사용 하려면 데스크톱 또는 터미널 세션 X 원격을 설정 합니다.

SDK를 설치하기 전에, 격리된 Python 환경을 만드는 것이 좋습니다. 이 문서에서 [Miniconda](https://docs.conda.io/en/latest/miniconda.html)를 사용한다해도 설치된 전체 [Anaconda](https://www.anaconda.com/) 또는 [Python virtualenv](https://virtualenv.pypa.io/en/stable/)를 사용할 수도 있습니다.

이 문서의 지침에는 빠른 시작 및 자습서 notebook을 실행 하는 데 필요한 모든 패키지 설치 됩니다.  다른 샘플 Notebook에서는 추가 구성 요소를 설치해야 할 수도 있습니다.  이러한 구성 요소에 대한 자세한 내용은 [Python용 Azure Machine Learning SDK 설치](https://docs.microsoft.com/python/api/overview/azure/ml/install)를 참조하세요.

### <a name="install-miniconda"></a>Miniconda 설치

[Miniconda를 다운로드하여 설치합니다](https://docs.conda.io/en/latest/miniconda.html). Python 3.7 버전을 선택하여 설치합니다. Python 2.x 버전을 선택하지 마세요.  

### <a name="create-an-isolated-python-environment"></a>격리된 Python 환경 만들기

1. Anaconda 프롬프트를 열고 라는 새 conda 환경을 만듭니다 *나열한* 3.6.5 Python을 설치 합니다. Azure Machine Learning SDK는 Python 3.5.2 이상에서 작동하지만, 자동화된 기계 학습 구성 요소는 Python 3.7에서 완벽하게 작동하지 않습니다.  구성 요소 및 패키지를 다운로드하는 동안에 환경을 만드는 데 몇 분 정도 걸립니다. 

    ```shell
    conda create -n myenv python=3.6.5
    ```

1. 환경을 활성화합니다.

    ```shell
    conda activate myenv
    ```

1. 환경별 ipython 커널을 활성화합니다.

    ```shell
    conda install notebook ipykernel
    ```

    그런 다음, 커널을 만듭니다.

    ```shell
    ipython kernel install --user
    ```

### <a name="install-the-sdk"></a>SDK 설치

1. 활성화된 conda 환경에서 Jupyter Notebook 기능을 사용하여 Machine Learning SDK의 핵심 구성 요소를 설치합니다. 머신 구성에 따라 설치를 완료하는 데 몇 분이 걸립니다.

    ```shell
    pip install --upgrade azureml-sdk[notebooks]
    ```

1. 이 환경을 Azure Machine Learning 자습서에 사용하려면 다음 패키지를 설치합니다.

    ```shell
    conda install -y cython matplotlib pandas
    ```

1. 이 환경을 Azure Machine Learning 자습서에 사용하려면 자동화된 기계 학습 구성 요소를 설치합니다.

    ```shell
    pip install --upgrade azureml-sdk[automl]
    ```

> [!IMPORTANT]
> 일부 명령줄 도구에서는 다음과 같이 따옴표를 추가해야 할 수도 있습니다.
> *  'azureml-sdk[notebooks]'
> * 'azureml-sdk[automl]'
>

### <a name='sdk-create'></a> SDK를 사용 하 여 작업 영역 만들기

Python SDK를 사용하여 Jupyter Notebook에서 작업 영역을 만듭니다.

1. 빠른 시작 및 자습서에 사용하려는 디렉터리를 만들거나 cd합니다.

1. Jupyter Notebook을 시작하려면 다음 명령을 입력합니다.

    ```shell
    jupyter notebook
    ```

1. 브라우저 창에서 기본 `Python 3` 커널을 사용하여 새 Notebook을 만듭니다. 

1. SDK 버전을 표시하려면 Notebook 셀에 다음 Python 코드를 입력한 다음, 실행합니다.

   [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=import)]

1. [Azure Portal의 구독 목록](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)에서 `<azure-subscription-id>` 매개 변수에 대한 값을 찾습니다. 역할이 소유자 또는 기여자인 구독을 사용합니다. 역할에 대 한 자세한 내용은 참조 하세요. [Azure Machine Learning 작업 영역에 대 한 액세스 관리](how-to-assign-roles.md) 문서.

   ```python
   from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
                         subscription_id='<azure-subscription-id>', 
                         resource_group='myresourcegroup',
                         create_resource_group=True,
                         location='eastus2' 
                        )
   ```

   코드를 실행하는 경우 Azure 계정에 로그인하라는 메시지가 표시될 수 있습니다. 로그인하면 인증 토큰이 로컬로 캐시됩니다.

1. 연결된 스토리지, 컨테이너 레지스트리, 키 자격 증명 모음 등의 작업 영역 세부 정보를 확인하려면 다음 코드를 입력합니다.

    [!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=getDetails)]


### <a name="write-a-configuration-file"></a>구성 파일 작성

구성 파일에 있는 작업 영역의 세부 정보를 현재 디렉터리에 저장합니다. 이 파일 이라고 *.azureml/config.json*합니다.  

나중에 이 작업 영역 구성 파일을 사용하여 동일한 작업 영역을 간편하게 로드할 수 있습니다. 다른 전자 필기장과 같은 디렉터리 또는 코드를 사용 하 여 하위 디렉터리에서 스크립트를 사용 하 여 로드할 수 있습니다 `ws=Workspace.from_config()` 합니다. 

[!code-python[](~/aml-sdk-samples/ignore/doc-qa/quickstart-create-workspace-with-python/quickstart.py?name=writeConfig)]

이 `write_config()` API 호출은 현재 디렉터리에 구성 파일을 만듭니다. *config.json* 파일에는 다음 항목이 포함되어 있습니다.

```json
{
    "subscription_id": "<azure-subscription-id>",
    "resource_group": "myresourcegroup",
    "workspace_name": "myworkspace"
}
```

> [!TIP]
> Python 스크립트 또는 다른 디렉터리에 있는 Jupyter Notebook에서 작업 영역을 사용 하려면 해당 디렉터리에이 파일을 복사 합니다. 파일을 동일한 디렉터리에 하위 디렉터리에 있을 수 있습니다 *.azureml*, 또는 부모 디렉터리입니다.

## <a name="resource-manager-template"></a>Resource manager 템플릿

템플릿을 사용 하 여 작업 영역을 만들려면 참조 [템플릿을 사용 하 여 Azure Machine Learning 서비스 작업 영역 만들기](how-to-create-workspace-template.md)

<a name="cli"></a>
## <a name="command-line-interface"></a>명령줄 인터페이스

CLI를 사용 하 여 작업 영역 만들기를 참조 하세요 [CLI 확장을 사용 하 여 Azure Machine Learning 서비스에 대 한](reference-azure-machine-learning-cli.md)합니다.

## <a name="clean-up-resources"></a>리소스 정리 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

* 만든 방법에 관계 없이 작업 영역을 볼 수 있습니다 합니다 [Azure portal](https://portal.azure.com/)합니다.  참조 [작업 영역을 보려면](how-to-manage-workspace.md#view) 세부 정보에 대 한 합니다.

* 이러한 빠른 시작 및 자습서를 사용 하 여 작업 영역을 사용해 보세요.

    * 빠른 시작: [Jupyter notebook을 클라우드에서 실행할](quickstart-run-cloud-notebook.md)합니다.
    * 빠른 시작: [사용자 고유의 서버에서 Jupyter notebook을 실행](quickstart-run-local-notebook.md)합니다.
    * 두 부분으로 이루어진 자습서의 경우: [학습](tutorial-train-models-with-aml.md) 하 고 [배포](tutorial-deploy-models-with-aml.md) 는 이미지 분류 모드입니다.
    * 두 부분으로 이루어진 자습서의 경우: [데이터 준비](tutorial-data-prep.md) 하 고 [자동화 된 machine learning을 사용](tutorial-auto-train-models.md) 회귀 모델을 작성 합니다.

* 하는 방법에 자세히 알아보려면 [개발 환경을 구성](how-to-configure-environment.md)합니다.

* 에 대 한 자세한 정보는 [Python 용 Azure Machine Learning SDK](https://aka.ms/aml-sdk)합니다.
