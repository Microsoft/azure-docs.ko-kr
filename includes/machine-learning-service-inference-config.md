---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: f500df6a592769928470d22468ff2fdff18293a5
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88748800"
---
`inferenceconfig.json` 문서의 항목은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스에 대한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대한 매개 변수 간의 매핑에 대해 설명합니다.

| JSON 엔터티 | 메서드 매개 변수 | 설명 |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 이미지에 대해 실행할 코드를 포함하는 로컬 파일의 경로입니다. |
| `sourceDirectory` | `source_directory` | 선택 사항입니다. 이미지를 만드는 모든 파일이 포함된 폴더의 경로입니다. 이 폴더 또는 하위 폴더에 있는 모든 파일에 쉽게 액세스할 수 있습니다. 로컬 머신에서 웹 서비스에 대한 종속성으로 전체 폴더를 업로드할 수 있습니다. 참고: entry_script, conda_file 및 extra_docker_file_steps 경로는 source_directory 경로에 대한 상대 경로입니다. |
| `environment` | `environment` | 선택 사항입니다.  Azure Machine Learning [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)|

유추 구성 파일에서 Azure Machine Learning [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)의 전체 사양을 포함할 수 있습니다. 이 환경이 작업 영역에 없는 경우 Azure Machine Learning에서 만듭니다. 그렇지 않으면 필요한 경우 Azure Machine Learning에서 환경을 업데이트합니다. 다음 JSON은 예제입니다.

```json
{
    "entryScript": "score.py",
    "environment": {
        "docker": {
            "arguments": [],
            "baseDockerfile": null,
            "baseImage": "mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04",
            "enabled": false,
            "sharedVolumes": true,
            "shmSize": null
        },
        "environmentVariables": {
            "EXAMPLE_ENV_VAR": "EXAMPLE_VALUE"
        },
        "name": "my-deploy-env",
        "python": {
            "baseCondaEnvironment": null,
            "condaDependencies": {
                "channels": [
                    "conda-forge"
                ],
                "dependencies": [
                    "python=3.6.2",
                    {
                        "pip": [
                            "azureml-defaults",
                            "azureml-telemetry",
                            "scikit-learn==0.22.1",
                            "inference-schema[numpy-support]"
                        ]
                    }
                ],
                "name": "project_environment"
            },
            "condaDependenciesFile": null,
            "interpreterPath": "python",
            "userManagedDependencies": false
        },
        "version": "1"
    }
}
```

구분된 CLI 매개 변수에서 기존 Azure Machine Learning [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)을 사용하고 유추 구성 파일에서 "환경" 키를 제거할 수도 있습니다. 환경 이름에 -e를 사용하고 환경 버전에 --ev를 사용합니다. --ev를 지정하지 않으면 최신 버전이 사용됩니다. 다음은 유추 구성 파일의 예제입니다.

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

다음 명령은 이전 유추 구성 파일(myInferenceConfig.json)을 사용하여 모델을 배포하는 방법을 보여 줍니다. 

또한 기존 Azure Machine Learning [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py)(AzureML-Minimal)의 최신 버전을 사용합니다.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
