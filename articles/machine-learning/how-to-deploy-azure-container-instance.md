---
title: Azure Container Instances에 모델을 배포 하는 방법
titleSuffix: Azure Machine Learning
description: Azure Container Instances를 사용 하 여 Azure Machine Learning 모델을 웹 서비스로 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: jordane
author: jpe316
ms.reviewer: larryfr
ms.date: 06/12/2020
ms.openlocfilehash: cbba0dd5341ad148831ac3b1f94685bf2beddd5a
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855270"
---
# <a name="deploy-a-model-to-azure-container-instances"></a>Azure Container Instances에 모델 배포
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure Machine Learning를 사용 하 여 ACI (Azure Container Instances에서 웹 서비스로 모델을 배포 하는 방법을 알아봅니다. 다음 조건 중 하나에 해당 하는 경우 Azure Container Instances를 사용 합니다.

- 모델을 빠르게 배포하고 유효성을 검사해야 합니다. ACI 컨테이너를 미리 만들 필요는 없습니다. 배포 프로세스의 일부로 생성 됩니다.
- 개발 중인 모델을 테스트합니다. 

ACI의 할당량 및 지역 가용성에 대 한 자세한 내용은 [Azure Container Instances 문서에 대 한 할당량 및 지역 가용성](https://docs.microsoft.com/azure/container-instances/container-instances-quotas) 을 참조 하세요.

> [!IMPORTANT]
> 웹 서비스로 배포 하기 전에 로컬로 디버그 하는 것이 좋습니다. 자세한 내용은 [로컬로 디버그](https://docs.microsoft.com/azure/machine-learning/how-to-troubleshoot-deployment#debug-locally) 를 참조 하세요.
>
> 또한 Azure Machine Learning - [로컬 Notebook에 배포](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/deploy-to-local)를 참조할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Machine Learning 작업 영역 자세한 내용은 [Azure Machine Learning 작업 영역 만들기](how-to-manage-workspace.md)를 참조 하세요.

- 작업 영역에 등록 된 machine learning 모델입니다. 등록 된 모델이 없는 경우 모델을 배포 하 [는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

- Machine Learning 서비스, [Azure Machine Learning PYTHON SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)또는 [Azure Machine Learning Visual Studio Code 확장](tutorial-setup-vscode-extension.md) [에 대 한 Azure CLI 확장](reference-azure-machine-learning-cli.md)입니다.

- 이 문서의 __Python__ 코드 조각에서는 다음 변수가 설정 되었다고 가정 합니다.

    * `ws` -작업 영역으로 설정 합니다.
    * `model` -등록 된 모델로 설정 합니다.
    * `inference_config` -모델에 대 한 유추 구성으로 설정 합니다.

    이러한 변수를 설정 하는 방법에 대 한 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

- 이 문서의 __CLI__ 코드 조각은 문서를 만든 것으로 가정 합니다 `inferenceconfig.json` . 이 문서를 만드는 방법에 대 한 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

## <a name="deploy-to-aci"></a>ACI에 배포

Azure Container Instances에 모델을 배포 하려면 필요한 계산 리소스를 설명 하는 __배포 구성을__ 만듭니다. 예를 들면 코어 수와 메모리입니다. 모델 및 웹 서비스를 호스트 하는 데 필요한 환경을 설명 하는 __유추 구성__도 필요 합니다. 유추 구성을 만드는 방법에 대 한 자세한 내용은 [모델을 배포 하는 방법 및 위치](how-to-deploy-and-where.md)를 참조 하세요.

> [!NOTE]
> * ACI는 크기가 1gb 미만 인 작은 모델에만 적합 합니다. 
> * AKS 단일 노드를 사용 하 여 더 큰 모델을 개발-테스트 하는 것이 좋습니다.
> * 배포할 모델 수는 배포 당 1000 모델 (컨테이너 당)으로 제한 됩니다. 

### <a name="using-the-sdk"></a>SDK 사용

```python
from azureml.core.webservice import AciWebservice, Webservice
from azureml.core.model import Model

deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)
service = Model.deploy(ws, "aciservice", [model], inference_config, deployment_config)
service.wait_for_deployment(show_output = True)
print(service.state)
```

이 예제에 사용 된 클래스, 메서드 및 매개 변수에 대 한 자세한 내용은 다음 참조 문서를 참조 하세요.

* [AciWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aciwebservice?view=azure-ml-py#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-)
* [모델. 배포](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.model?view=azure-ml-py#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)
* [웹 서비스. wait_for_deployment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice%28class%29?view=azure-ml-py#wait-for-deployment-show-output-false-)

### <a name="using-the-cli"></a>CLI 사용

CLI를 사용 하 여 배포 하려면 다음 명령을 사용 합니다. `mymodel:1`를 등록 된 모델의 이름 및 버전으로 바꿉니다. `myservice`이 서비스를 제공할 이름으로 대체 합니다.

```azurecli-interactive
az ml model deploy -m mymodel:1 -n myservice -ic inferenceconfig.json -dc deploymentconfig.json
```

[!INCLUDE [deploymentconfig](../../includes/machine-learning-service-aci-deploy-config.md)]

자세한 내용은 [az ml model deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy) reference를 참조 하세요. 

## <a name="using-vs-code"></a>VS Code 사용

[VS Code를 사용 하 여 모델 배포](tutorial-train-deploy-image-classification-model-vscode.md#deploy-the-model)를 참조 하세요.

> [!IMPORTANT]
> 미리 테스트할 ACI 컨테이너를 만들 필요가 없습니다. ACI 컨테이너는 필요에 따라 만들어집니다.


## <a name="next-steps"></a>다음 단계

* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [배포 문제 해결](how-to-troubleshoot-deployment.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 ML 모델 사용](how-to-consume-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
