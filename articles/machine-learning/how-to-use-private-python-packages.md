---
title: 프라이빗 Python 패키지 사용
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 환경에서 안전 하 게 개인 Python 패키지에 액세스 합니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 314f6a45bf688125e79f0b8ce0099a8326b339dc
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958153"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Azure Machine Learning에서 전용 Python 패키지 사용
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

이 문서에서는 Azure Machine Learning 내에서 안전 하 게 개인 Python 패키지를 사용 하는 방법에 대해 알아봅니다. 개인 Python 패키지에 대 한 사용 사례는 다음과 같습니다.

 * 공개적으로 공유 하지 않으려는 개인 패키지를 개발 했습니다.
 * 엔터프라이즈 방화벽 내에 저장 된 패키지의 큐 레이트 리포지토리를 사용 하려고 합니다.

권장 되는 방법은 단일 Azure Machine Learning 작업 영역에 대 한 패키지를 몇 개 사용 하는지 아니면 조직 내의 모든 작업 영역에 대 한 전체 패키지 리포지토리를 사용 하는지에 따라 달라 집니다.

[환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment) 클래스를 통해 개인 패키지를 사용 합니다. 환경 내에서 개인용 패키지를 포함 하 여 사용할 Python 패키지를 선언 합니다. 일반적인 Azure Machine Learning 환경에 대해 알아보려면 [환경을 사용 하는 방법](how-to-use-environments.md)을 참조 하세요. 

## <a name="prerequisites"></a>필수 구성 요소

 * [Python 용 AZURE MACHINE LEARNING SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)
 * [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>개발 및 테스트에 적은 수의 패키지 사용

단일 작업 영역에 대 한 소수의 개인 패키지의 경우 정적 메서드를 사용 [`Environment.add_private_pip_wheel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py#add-private-pip-wheel-workspace--file-path--exist-ok-false-) 합니다. 이 접근 방식을 사용 하면 작업 영역에 개인 패키지를 신속 하 게 추가할 수 있으며 개발 및 테스트 목적으로 적합 합니다.

로컬 휠 파일의 파일 경로 인수를 가리키고 ```add_private_pip_wheel``` 명령을 실행 합니다. 이 명령은 작업 영역 내에서 패키지의 위치를 추적 하는 데 사용 되는 URL을 반환 합니다. 저장소 URL을 캡처하고 메서드를 전달 `add_pip_package()` 합니다.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

내부적으로 Azure Machine Learning 서비스는 보안 SAS URL로 URL을 대체 하므로 휠 파일은 개인 및 안전 하 게 유지 됩니다.

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>Azure DevOps 피드의 패키지 리포지토리 사용

기계 학습 응용 프로그램에 대 한 Python 패키지를 적극적으로 개발 하는 경우 Azure DevOps 리포지토리에서 아티팩트로 호스팅하고 피드로 게시할 수 있습니다. 이 접근 방식을 사용 하면 Azure Machine Learning 작업 영역를 사용 하 여 패키지를 빌드하기 위한 DevOps 워크플로를 통합할 수 있습니다. Azure DevOps를 사용 하 여 Python 피드를 설정 하는 방법을 알아보려면 [Azure Artifacts에서 Python 패키지 시작](https://docs.microsoft.com/azure/devops/artifacts/quickstarts/python-packages?view=azure-devops) 을 참조 하세요.

이 방법은 개인용 액세스 토큰을 사용 하 여 리포지토리에 대해 인증 합니다. 동일한 접근 방식은 개인 GitHub 리포지토리와 같이 토큰 기반 인증을 사용 하는 다른 리포지토리에 적용할 수 있습니다. 

 1. Azure DevOps 인스턴스에 대해 [PAT (개인용 액세스 토큰)를 만듭니다](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=azure-devops&tabs=preview-page#create-a-pat) . 토큰의 범위를 __패키징을 > 읽기__로 설정 합니다. 

 2. [작업 영역. set_connection](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#set-connection-name--category--target--authtype--value-) 메서드를 사용 하 여 Azure DEVOPS URL 및 PAT를 작업 영역 속성으로 추가 합니다.

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Azure Machine Learning 환경을 만들고 피드에서 Python 패키지를 추가 합니다.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

이제 환경을 학습 실행 또는 웹 서비스 끝점 배포에 사용할 준비가 되었습니다. 환경을 구축할 때 Azure Machine Learning 서비스는 PAT를 사용 하 여 일치 하는 기준 URL로 피드에 대해 인증 합니다.

## <a name="use-a-repository-of-packages-from-private-storage"></a>개인 저장소의 패키지 리포지토리 사용

조직의 방화벽 내에 있는 Azure storage 계정에서 패키지를 사용할 수 있습니다. 저장소 계정은 큐 레이트 패키지 집합 또는 공개적으로 사용 가능한 패키지의 내부 미러를 보유할 수 있습니다.

이러한 개인 저장소를 설정 하려면:

1. [가상 네트워크 (VNet) 내에 작업 영역을 저장](how-to-enable-virtual-network.md)합니다.
1. 저장소 계정을 만들고 [공용 액세스를 허용](https://docs.microsoft.com/azure/storage/common/storage-network-security)하지 않습니다.
1. 저장소 계정 내의 컨테이너에 사용할 Python 패키지를 저장 합니다. 
1. [작업 영역 VNet에서 저장소 계정 액세스 허용](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-a-virtual-network)
1. [VNet 뒤에 작업 영역에 대 한 Azure Container Registry (ACR)를 저장](how-to-enable-virtual-network.md#azure-container-registry)합니다.

    > [!IMPORTANT]
    > 개인 패키지 리포지토리를 사용 하 여 모델을 학습 하거나 배포 하려면이 단계를 완료 해야 합니다.

이러한 구성을 완료 한 후에는 Azure blob storage에서 전체 URL로 Azure Machine Learning 환경 정의의 패키지를 참조할 수 있습니다.

## <a name="next-steps"></a>다음 단계

 * [Azure Machine Learning의 엔터프라이즈 보안](concept-enterprise-security.md) 에 대해 자세히 알아보세요.
