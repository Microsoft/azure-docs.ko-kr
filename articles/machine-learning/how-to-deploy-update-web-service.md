---
title: 배포 된 웹 서비스 업데이트
author: gvashishtha
ms.service: machine-learning
ms.topic: conceptual
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 0c2811b6bed3d02a9689f3b9e49a4c3888dff6c4
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935571"
---
# <a name="update-a-deployed-web-service"></a>배포 된 웹 서비스 업데이트

이 문서에서는 Azure Machine Learning를 사용 하 여 배포 된 웹 서비스를 배포 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서에서는 Azure Machine Learning를 사용 하 여 웹 서비스를 이미 배포 했다고 가정 합니다. 웹 서비스를 배포 하는 방법을 알아야 하는 경우 [다음 단계를 수행](how-to-deploy-and-where.md)합니다.

## <a name="update-web-service"></a>웹 서비스 업데이트

웹 서비스를 업데이트 하려면 메서드를 사용 `update` 합니다. 새 모델, 새 항목 스크립트 또는 유추 구성에서 지정할 수 있는 새 종속성을 사용 하도록 웹 서비스를 업데이트할 수 있습니다. 자세한 내용은 [웹 서비스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)에 대 한 설명서를 참조 하십시오.

[AKS Service Update 메서드를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.akswebservice?view=azure-ml-py#update-image-none--autoscale-enabled-none--autoscale-min-replicas-none--autoscale-max-replicas-none--autoscale-refresh-seconds-none--autoscale-target-utilization-none--collect-model-data-none--auth-enabled-none--cpu-cores-none--memory-gb-none--enable-app-insights-none--scoring-timeout-ms-none--replica-max-concurrent-requests-none--max-request-wait-time-none--num-replicas-none--tags-none--properties-none--description-none--models-none--inference-config-none--gpu-cores-none--period-seconds-none--initial-delay-seconds-none--timeout-seconds-none--success-threshold-none--failure-threshold-none--namespace-none--token-auth-enabled-none-) 참조 하세요.

[ACI 서비스 업데이트 메서드를](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice?view=azure-ml-py#update-image-none--tags-none--properties-none--description-none--auth-enabled-none--ssl-enabled-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--enable-app-insights-none--models-none--inference-config-none-) 참조 하세요.

> [!IMPORTANT]
> 모델의 새 버전을 만들 때 사용 하려는 각 서비스를 수동으로 업데이트 해야 합니다.
>
> SDK를 사용 하 여 Azure Machine Learning 디자이너에서 게시 된 웹 서비스를 업데이트할 수 없습니다.

**SDK 사용**

다음 코드에서는 SDK를 사용 하 여 웹 서비스에 대 한 모델, 환경 및 입력 스크립트를 업데이트 하는 방법을 보여 줍니다.

```python
from azureml.core import Environment
from azureml.core.webservice import Webservice
from azureml.core.model import Model, InferenceConfig

# Register new model.
new_model = Model.register(model_path="outputs/sklearn_mnist_model.pkl",
                           model_name="sklearn_mnist",
                           tags={"key": "0.1"},
                           description="test",
                           workspace=ws)

# Use version 3 of the environment.
deploy_env = Environment.get(workspace=ws,name="myenv",version="3")
inference_config = InferenceConfig(entry_script="score.py",
                                   environment=deploy_env)

service_name = 'myservice'
# Retrieve existing service.
service = Webservice(name=service_name, workspace=ws)



# Update to new model(s).
service.update(models=[new_model], inference_config=inference_config)
service.wait_for_deployment(show_output=True)
print(service.state)
print(service.get_logs())
```

**CLI 사용**

ML CLI를 사용 하 여 웹 서비스를 업데이트할 수도 있습니다. 다음 예에서는 새 모델을 등록 한 다음 새 모델을 사용 하도록 웹 서비스를 업데이트 하는 방법을 보여 줍니다.

```azurecli
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --output-metadata-file modelinfo.json
az ml service update -n myservice --model-metadata-file modelinfo.json
```

> [!TIP]
> 이 예제에서는 JSON 문서를 사용 하 여 등록 명령의 모델 정보를 update 명령에 전달 합니다.
>
> 새 항목 스크립트나 환경을 사용 하도록 서비스를 업데이트 하려면 [유추 구성 파일](/azure/machine-learning/reference-azure-machine-learning-cli#inference-configuration-schema) 을 만들고 매개 변수를 사용 하 여 지정 `ic` 합니다.

자세한 내용은 [az ml service update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [실패 한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용 하는 클라이언트 응용 프로그램 만들기](how-to-consume-web-service.md)
* [사용자 지정 Docker 이미지를 사용 하 여 모델을 배포 하는 방법](how-to-deploy-custom-docker-image.md)
* [TLS를 사용하여 Azure Machine Learning을 통해 웹 서비스 보호](how-to-secure-web-service.md)
* [Application Insights를 사용하여 Azure Machine Learning 모델 모니터링](how-to-enable-app-insights.md)
* [프로덕션 환경에서 모델용 데이터 수집](how-to-enable-data-collection.md)
* [모델 배포에 대 한 이벤트 경고 및 트리거 만들기](how-to-use-event-grid.md)
