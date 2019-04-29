---
title: Python 개발 환경 설정
titleSuffix: Azure Machine Learning service
description: Azure Machine Learning Service 작업 시 개발 환경을 구성하는 방법을 알아봅니다. 이 문서에서는 Conda 환경을 사용하고, 구성 파일을 만들고, Jupyter Notebooks, Azure Notebooks, Azure Databricks, IDE, 코드 편집기 및 Data Science Virtual Machine을 구성하는 방법을 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 02/24/2019
ms.custom: seodec18
ms.openlocfilehash: 720f984feb5675281510962d4ebee63f638d696d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819968"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Azure Machine Learning용 개발 환경 구성

이 문서에서는 Azure Machine Learning Service에서 작동하는 개발 환경을 구성하는 방법을 알아봅니다. Azure Machine Learning Service는 플랫폼에 관계 없이 사용할 수 있습니다.

개발 환경에 대 한 유일한 요구 사항은 Python 3, Anaconda (예: 격리 된 환경의 경우) 및 Azure Machine Learning 작업 영역 정보를 포함 하는 구성 파일입니다.

이 문서에서는 다음과 같은 환경 및 도구를 중점적으로 다룹니다.

* Azure Notebooks: Azure 클라우드에 호스트되는 Jupyter Notebook 서비스입니다. Azure Machine Learning SDK가 이미 설치되어 있으므로 시작하기에 가장 쉬운 방법입니다.

* [DSVM(Data Science Virtual Machine)](#dsvm) 데이터 과학 작업용으로 설계되고 CPU 전용 VM 인스턴스 또는 GPU 기반 인스턴스에 배포할 수 있는 Azure 클라우드에 미리 구성된 개발 또는 실험 환경입니다. Python 3, Conda, Jupyter Notebook 및 Azure Machine Learning SDK가 이미 설치되어 있습니다. VM은 기계 학습 솔루션 개발에 자주 사용되는 기계 학습 및 딥 러닝 프레임워크, 도구, 편집기가 함께 제공됩니다. Azure 플랫폼에서 가장 완성도 높은 기계 학습용 개발 환경입니다.

* [Jupyter Notebook](#jupyter): 이미 Jupyter Notebook을 사용 중인 경우 SDK를 통해 몇 가지 요소를 추가로 설치해야 합니다.

* [Visual Studio Code](#vscode): Visual Studio Code를 사용하는 경우 몇 가지 유용한 확장 프로그램을 설치할 수 있습니다.

* [Azure Databricks](#aml-databricks): Apache Spark 기반의 인기 있는 데이터 분석 플랫폼입니다. 모델을 배포할 수 있도록 클러스터로 Azure Machine Learning SDK를 가져오는 방법을 알아봅니다.

* [Azure Notebooks](#aznotebooks): Azure 클라우드에 호스트되는 Jupyter Notebook 서비스입니다. 또한 쉽게 Azure Machine Learning SDK가 이미 설치 되어 있으므로 시작 합니다.  

이미 Python 3 환경을 갖고 있거나 SDK를 설치하는 데 필요한 기본 단계만 원하는 경우 [로컬 컴퓨터](#local) 섹션을 참조하세요.

## <a name="prerequisites"></a>필수 조건

- Azure Machine Learning 서비스 작업 영역. 참조 된 작업 영역을 만들려면 [Azure Machine Learning 서비스 작업 영역 만들기](setup-create-workspace.md)합니다.

시작 하려면 필요한 모든 작업 영역은 사용자 [Azure Notebooks](#aznotebooks), [DSVM](#dsvm), 또는 [Azure Databricks](#aml-databricks)합니다.

에 대 한 SDK 환경을 설치 하 여 [로컬 컴퓨터](#local), [Jupyter Notebook 서버](#jupyter) 또는 [Visual Studio Code](#vscode) 해야:

- 중 하나는 [Anaconda](https://www.anaconda.com/download/) 하거나 [Miniconda](https://conda.io/miniconda.html) 패키지 관리자입니다.

- Linux 또는 Mac OS에서는 bash 셸이 필요합니다.

    > [!TIP]
    > Linux 또는 Mac OS에서 bash가 아닌 셸을 사용하는 경우(예: zsh) 일부 명령을 실행할 때 오류가 발생할 수 있습니다. 이 문제를 해결하려면 `bash` 명령을 사용하여 새 bash 셸을 시작하고 거기서 명령을 실행하세요.

- Windows에서는 명령 프롬프트 또는 Anaconda 프롬프트(Anaconda 및 Miniconda를 통해 설치한)가 필요합니다.

## <a id="aznotebooks"></a>Azure Notebooks

[Azure Notebooks](https://notebooks.azure.com)(미리 보기)는 Azure 클라우드의 대화형 개발 환경입니다. Azure Machine Learning 개발 시작 하기 쉬운 것입니다.

* Azure Machine Learning SDK가 이미 설치되어 있습니다.
* Azure Portal에서 Azure Machine Learning Service 작업 영역을 만든 후에는 단추를 클릭하여 작업 영역과 함께 작동하도록 자동으로 Azure Notebook 환경을 구성할 수 있습니다.

사용 된 [Azure portal](https://portal.azure.com) Azure notebooks 시작 하려면.  작업 영역을 열고 들어오고 합니다 **개요** 섹션에서 **Azure Notebooks 시작**합니다.

기본적으로 Azure Notebooks는 메모리 4GB, 데이터 1GB로 제한되는 무료 서비스 계층을 사용합니다. 하지만 Data Science Virtual Machine 인스턴스를 Azure Notebooks 프로젝트에 연결하여 이 제한을 제거할 수 있습니다. 자세한 내용은 [Azure Notebooks 프로젝트 관리 및 구성 - 컴퓨팅 계층](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier)을 참조하세요.    


## <a id="dsvm"></a>Data Science Virtual Machine

DSVM는 사용자 지정된 VM(가상 머신) 이미지입니다. 데이터 과학 작업을 위해 설계되었으며 다음 요소가 미리 구성되어 있습니다.

  - TensorFlow, PyTorch, Scikit-learn, XGBoost 및 Azure Machine Learning SDK 같은 패키지
  - Spark Standalone 및 Drill 같은 인기 있는 데이터 과학 도구
  - Azure CLI, AzCopy 및 Storage 탐색기 같은 Azure 도구
  - Visual Studio Code 및 PyCharm 같은 IDE(통합 개발 환경)
  - Jupyter Notebook 서버

Azure Machine Learning SDK는 DSVM의 Ubuntu 또는 Windows 버전에서 작동합니다. 하지만 컴퓨팅 대상으로 DSVM도 사용할 계획인 경우 Ubuntu만 지원됩니다.

개별 환경으로 DSVM을 사용하려면 다음 단계를 수행합니다.

1. 다음 환경 중 하나에서 DSVM을 만듭니다.

    * Azure Portal:

        * [Ubuntu Data Science Virtual Machine 만들기](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Windows Data Science Virtual Machine 만들기](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Azure CLI:

        > [!IMPORTANT]
        > * Azure CLI를 사용하는 경우 먼저 `az login` 명령을 사용하여 Azure 구독에 로그인해야 합니다.
        >
        > * 이 단계의 명령을 사용하는 경우 리소스 그룹 이름, VM 이름, 사용자 이름 및 암호를 제공해야 합니다.

        * Ubuntu Data Science Virtual Machine을 만들려면 다음 명령을 사용합니다.

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Windows Data Science Virtual Machine을 만들려면 다음 명령을 사용합니다.

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Azure Machine Learning SDK가 DSVM에 이미 설치되어 있습니다. SDK가 포함된 Conda 환경을 사용하려면 다음 명령 중 하나를 사용합니다.

    * Ubuntu DSVM:

        ```shell
        conda activate py36
        ```

    * Windows DSVM:

        ```shell
        conda activate AzureML
        ```

1. SDK에 액세스할 수 있는지 확인하고 버전을 검사하려면 다음 Python 코드를 사용합니다.

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Azure Machine Learning Service 작업 영역을 사용하도록 DSVM을 구성하려면 [작업 영역 구성 파일 만들기](#workspace) 섹션을 참조하세요.

자세한 내용은 [Data Science Virtual Machine](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/)를 참조하세요.

## <a id="local"></a>로컬 컴퓨터

로컬 컴퓨터 (이 필요할 수도 있습니다 원격 가상 컴퓨터)를 사용 하는 Anaconda 환경을 만들고 다음을 수행 하 여 SDK 설치 합니다.

1. 다운로드 및 설치 [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3.7 버전) 아직 없는 경우.

1. Anaconda 프롬프트를 열고 다음 명령을 사용 하 여 환경을 만듭니다.

    환경을 만들려면 다음 명령을 실행 합니다.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    그런 다음 환경을 활성화 합니다.

    ```shell
    conda activate myenv
    ```

    이 예제에서는 3.6.5, python을 사용 하 여 환경을 만들지만 모든 특정 subversions를 선택할 수 있습니다. 특정 주 버전 (3.5 이상 권장)를 사용 하 여 SDK 호환성을 보장할 수 있습니다 하 고 오류가 발생할 경우 Anaconda 환경에서 다른 버전/subversion 하려는 것이 좋습니다. 구성 요소 및 패키지를 다운로드하는 동안에 환경을 만드는 데 몇 분 정도 걸립니다.

1. 환경별 ipython 커널을 사용할 수 있도록 새로운 환경에서 다음 명령을 실행 합니다. 이렇게 하면 필요한 커널 및 패키지 가져오기 동작 Anaconda 환경 내에서 Jupyter Notebook과 함께 작업 하는 경우:

    ```shell
    conda install notebook ipykernel
    ```

    커널을 만들려면 다음 명령을 실행 합니다.

    ```shell
    ipython kernel install --user
    ```

1. 다음 명령을 사용 하 여 패키지를 설치 합니다.

    이 명령은 notebook 및 automl 추가 기능을 사용 하 여 기본 Azure Machine Learning SDK를 설치합니다. `automl` 추가는 대규모 설치 및 자동화 된 기계 학습 실험을 실행 하지 않으려는 경우 대괄호를 제거할 수 있습니다. `automl` 추가 Azure Machine Learning 데이터 준비 SDK 종속성으로 기본적으로 포함 되어 있습니다.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    이 명령을 사용 하 여 자체적으로 Azure Machine Learning 데이터 준비 SDK를 설치 합니다.

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > PyYAML을 제거할 수 없다는 메시지가 표시되면 다음 명령을 대신 사용합니다.
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   SDK를 설치 하는 데 몇 분 정도 걸립니다. 참조 된 [설치 가이드](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) 설치 옵션에 대 한 자세한 내용은 합니다.

1. 기계 학습 실험에 대 한 다른 패키지를 설치 합니다.

    다음 명령 중 하나를 사용 하 여 바꾸고  *\<새 패키지 >* 설치 하려는 패키지를 사용 하 여 합니다. 통해 패키지를 설치 `conda install` 패키지 (새 채널 Anaconda 클라우드에 추가할 수 있습니다)는 현재 채널의 일부인 것을 요구 합니다.

    ```shell
    conda install <new package>
    ```

    통해 패키지를 설치할 수는 또는 `pip`합니다.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Jupyter Notebook

Jupyter Notebook은 [Jupyter 프로젝트](https://jupyter.org/)의 일부입니다. 라이브 코드를 설명 테스트 및 그래픽과 혼합하는 문서를 만드는 대화형 코딩 환경을 제공합니다. Jupyter Notebook은 코드 섹션의 출력을 문서에 저장할 수 있으므로 다른 사람과 결과를 공유하는 좋은 방법이기도 합니다. Jupyter Notebook은 다양한 플랫폼에 설치할 수 있습니다.

절차는 [로컬 컴퓨터](#local) 섹션 Anaconda 환경에서 Jupyter Notebook을 실행 하는 데 필요한 구성 요소를 설치 합니다. Jupyter Notebook 환경에서 이러한 구성 요소를 사용하려면 다음 단계를 수행합니다.

1. Anaconda 프롬프트를 열고 환경을 활성화 합니다.

    ```shell
    conda activate myenv
    ```

1. 다음 명령 사용 하 여 Jupyter Notebook 서버를 시작 합니다.

    ```shell
    jupyter notebook
    ```

1. Jupyter Notebook에서 SDK를 사용할 수 있는지를 확인 하려면 만들기를 **새로 만들기** notebook을 선택 **Python 3** 커널 및 notebook 셀에서 다음 명령을 실행:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. 모듈을 가져오는 동안 문제가 발생할 경우 수신 된 `ModuleNotFoundError`, Jupyter 커널을 Notebook 셀에서 다음 코드를 실행 하 여 사용자 환경에 대 한 올바른 경로에 연결 되어 있는지 확인 합니다.

    ```python
    import sys
    sys.path
    ```

1. Azure Machine Learning Service 작업 영역을 사용하도록 Jupyter Notebook을 구성하려면 [작업 영역 구성 파일 만들기](#workspace) 섹션으로 이동하세요.

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code는 크로스 플랫폼 코드 편집기입니다. 로컬 Python 3와 Python을 지원하기 위해 설치되는 Conda를 사용하지만, AI를 사용하기 위한 추가 도구를 제공합니다. 코드 편집기 내에서 Conda 환경을 선택할 수 있는 지원도 제공합니다.

개발에 Visual Studio Code를 사용하려면 다음을 수행합니다.

1. Python 개발에 Visual Studio Code를 사용하는 방법은 [VSCode에서 Python 시작](https://code.visualstudio.com/docs/python/python-tutorial)을 참조하세요.

1. Conda 환경을 선택하려면 VS Code를 열고, Ctrl-Shift-P(Linux 및 Windows) 또는 Command-Shift-P(Mac)를 선택합니다.
    __명령 팔레트__가 열립니다.

1. __Python: 인터프리터 선택__을 입력한 다음, Conda 환경을 선택합니다.

1. SDK를 사용할 수 있는지 확인하려면 다음 코드가 포함된 새 Python 파일(.py)을 만들고 실행합니다.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Visual Studio Code용 Azure Machine Learning 확장을 설치하려면 [AI용 도구](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)를 참조하세요.

    자세한 내용은 [Visual Studio Code용 Azure Machine Learning 사용](how-to-vscode-tools.md)을 참조하세요.

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks는 Azure 클라우드의 Apache Spark 기반 환경입니다. CPU 또는 GPU 기반된 계산 클러스터를 사용 하 여 공동 작업 기반 전자 필기장 환경을 제공 합니다.

Azure Machine Learning 서비스를 사용 하 여 Azure Databricks 작동 방식:
+ Spark MLlib를 사용 하 여 모델을 학습 하 고를 ACI/AKS에서 Azure Databricks 내에서 모델을 배포할 수 있습니다. 
+ 사용할 수도 있습니다 [machine learning 자동화 된](concept-automated-ml.md) Azure Databricks를 사용 하 여 특별 한 Azure ML SDK의 기능입니다.
+ Azure Databricks를 사용 하 여에서 계산 대상으로는 [Azure Machine Learning 파이프라인](concept-ml-pipelines.md)합니다. 

### <a name="set-up-your-databricks-cluster"></a>Databricks 클러스터 설정

만들기는 [Databricks 클러스터](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)합니다. 일부 설정은 Databricks에서 학습 하는 자동화 된 컴퓨터에 대 한 SDK를 설치 하는 경우에 적용 됩니다.
**클러스터를 만드는 데 몇 분 정도 걸립니다.**

이러한 설정을 사용 합니다.

| 설정 |적용 대상| 값 |
|----|---|---|
| 클러스터 이름 |항상| yourclustername |
| Databricks 런타임 |항상| 모든 비 ML 런타임(비 ML 4.x, 5.x) |
| Python 버전 |항상| 3 |
| 작업자 |항상| 2 이상 |
| 작업자 노드 VM 유형 <br>(최대 동시 반복 여부 결정) |자동화된 ML<br>만| 메모리 최적화 VM 기본 설정 |
| 자동 크기 조정 사용 |자동화된 ML<br>만| 선택 취소 |

계속 진행하기 전에 클러스터가 실행될 때까지 기다립니다.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Databricks 라이브러리로 올바른 SDK를 설치 합니다.
클러스터 실행 되 면 [라이브러리를 만드는](https://docs.databricks.com/user-guide/libraries.html#create-a-library) 적절 한 Azure Machine Learning SDK 패키지를 클러스터에 연결 합니다. 

1. 선택할 **하나만** 옵션 (다른 SDK가 설치 되지 않습니다 지원 됨)

   |SDK&nbsp;package&nbsp;extras|원본|PyPi&nbsp;Name&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Databricks에 대 한| Python 업로드 Egg 또는 PyPI | azureml-sdk[databricks]|
   |-사용 하 여-Databricks에 대 한<br> 자동화 된 기계 학습 기능| Python 업로드 Egg 또는 PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > 다른 SDK extras 없습니다를 설치할 수 있습니다. 위의 옵션 [databricks] 또는 [automl_databricks] 하나만 선택 합니다.

   * 선택 하지 마세요 **모든 클러스터를 자동으로 연결할**합니다.
   * 선택 **연결** 클러스터 이름 옆에 있습니다.

1. 오류 상태에 변경 될 때까지 모니터링 **연결 된**를 몇 분 정도 걸립니다.  이 단계가 실패 하면 다음을 확인 합니다. 

   클러스터를 다시 시작 합니다.
   1. 왼쪽 창에서 **클러스터**를 선택합니다.
   1. 테이블에서 클러스터 이름을 선택합니다.
   1. **라이브러리** 탭에서 **다시 시작**을 선택합니다.
      
   또한 고려 합니다.
   + Automl config를 사용 하는 경우에 Azure Databricks는 다음 매개 변수를 추가 하세요.
        1. ```max_concurrent_iterations``` 클러스터의 작업자 노드 수에 기반 합니다. 
        2. ```spark_context=sc``` 기본 spark 컨텍스트입니다에 기반 합니다. 
   + 또는 이전 SDK 버전을 사용 하는 경우 클러스터의 설치 된 라이브러리에서 선택을 취소 하 고 휴지통으로 이동 합니다. 새 SDK 버전을 설치하고 클러스터를 다시 시작합니다. 그래도 문제가 있으면 클러스트를 분리했다가 다시 연결합니다.

설치 성공 하면 가져온된 라이브러리는 다음 중 하나 같이 표시 됩니다.
   
Databricks에 대 한 SDK **_없이_** machine learning 자동화 된 ![Databricks에 대 한 Azure Machine Learning SDK](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Databricks에 대 한 SDK **WITH** machine learning 자동화 된 ![SDK를 사용 하 여 Databricks에서 설치 하는 기계 학습 자동화](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>탐색 시작

사용해 보세요.
+ 다운로드 합니다 [notebook 보관 파일](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) Azure Databricks/Azure Machine Learning SDK에 대 한 및 [보관 파일을 가져올](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) Databricks 클러스터에 있습니다.  
  다양 한 샘플 노트북은 사용 가능한 **만 [이러한 샘플 notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) Azure Databricks를 사용 하 여 작동 합니다.**
  
+ 에 대해 알아봅니다 하는 방법 [교육 계산으로 Databricks를 사용 하 여 파이프라인 만들기](how-to-create-your-first-pipeline.md)합니다.

## <a id="workspace"></a>작업 영역 구성 파일 만들기

작업 영역 구성 파일은 Azure Machine Learning Service 작업 영역과 통신하는 방법을 SDK에 알려주는 JSON 파일입니다. 파일 이름은 *config.json*이고 형식은 다음과 같습니다.

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

이 JSON 파일은 Python 스크립트 또는 Jupyter Notebook을 포함하는 디렉터리 구조 내에 있어야 합니다. 동일한 디렉터리를 라는 하위 디렉터리에 수 *.azureml*, 또는 부모 디렉터리입니다.

코드에서 이 파일을 사용하려면 `ws=Workspace.from_config()`를 사용합니다. 이 코드는 파일의 정보를 로드하고 작업 영역에 연결합니다.

다음과 같은 세 가지 방법으로 구성 파일을 만들 수 있습니다.

* **단계를 따릅니다 [Azure Machine Learning 서비스 작업 영역을 만듭니다](setup-create-workspace.md#sdk)**: *config.json* 파일은 Azure Notebooks 라이브러리에 만들어집니다. 이 파일은 작업 영역에 대한 구성 정보를 포함합니다. *config.json* 파일을 다른 개발 환경으로 다운로드 또는 복사할 수 있습니다.

* **수동으로 파일 만들기**: 이 방법을 선택하는 경우 텍스트 편집기를 사용합니다. [Azure Portal](https://portal.azure.com)에서 작업 영역을 방문하여 구성 파일의 값을 찾을 수 있습니다. 작업 영역 이름, 리소스 그룹 및 구독 ID 값을 복사하여 구성 파일에 사용합니다.

     ![Azure portal](./media/how-to-configure-environment/configure.png)

* **프로그래밍 방식으로 파일 만들기**: 다음 코드 조각에서는 구독 ID, 리소스 그룹 및 작업 영역 이름을 입력하여 작업 영역에 연결합니다. 그런 다음, 작업 영역 구성을 파일에 저장합니다.

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    이 코드에 구성 파일에 작성 된 *.azureml/config.json* 파일입니다.


## <a name="next-steps"></a>다음 단계

- MNIST 데이터 세트를 사용하여 Azure Machine Learning에서 [모델 학습](tutorial-train-models-with-aml.md)
- [Python용 Azure Machine Learning SDK](https://aka.ms/aml-sdk) 참조 보기
- [Azure Machine Learning Data Prep SDK](https://aka.ms/data-prep-sdk)에 대해 알아보기
