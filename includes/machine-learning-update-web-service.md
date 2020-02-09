---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/10/2019
ms.author: larryfr
ms.openlocfilehash: 469d87a828df19ca30260cada9dcea43859be9e0
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75901770"
---
웹 서비스를 업데이트 하려면 `update` 메서드를 사용 합니다. 새 모델, 새 항목 스크립트 또는 유추 구성에서 지정할 수 있는 새 종속성을 사용 하도록 웹 서비스를 업데이트할 수 있습니다. 자세한 내용은 [웹 서비스](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.webservice.webservice?view=azure-ml-py#update--args-)에 대한 설명서를 참조 하십시오.

> [!IMPORTANT]
> 모델의 새 버전을 만들 때 사용 하려는 각 서비스를 수동으로 업데이트 해야 합니다.
>
> SDK를 사용 하 여 Azure Machine Learning 디자이너에서 게시 된 웹 서비스를 업데이트할 수 없습니다.

**SDK 사용**

다음 코드에서는 SDK를 사용 하 여 웹 서비스에 대한 모델, 환경 및 입력 스크립트를 업데이트 하는 방법을 보여 줍니다.

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
> 새 항목 스크립트나 환경을 사용 하도록 서비스를 업데이트 하려면 [유추 구성 파일](/azure/machine-learning/service/reference-azure-machine-learning-cli#inference-configuration-schema) 을 만들고 `ic` 매개 변수를 사용 하 여 지정 합니다.

자세한 내용은 [az ml service update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/service?view=azure-cli-latest#ext-azure-cli-ml-az-ml-service-update) 설명서를 참조 하세요.