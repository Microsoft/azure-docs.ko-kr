---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/28/2020
ms.author: larryfr
ms.openlocfilehash: 6970732f53ffa99849e20f279c8bdf4160c30a0a
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76845160"
---
`inferenceconfig.json` 문서의 항목은 [InferenceConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.model.inferenceconfig?view=azure-ml-py) 클래스에 대한 매개 변수에 매핑됩니다. 다음 표에서는 JSON 문서의 엔터티 및 메서드에 대한 매개 변수의 매핑에 대해 설명 합니다.

| JSON 엔터티 | 메서드 매개 변수 | Description |
| ----- | ----- | ----- |
| `entryScript` | `entry_script` | 이미지에 대해 실행할 코드를 포함 하는 로컬 파일의 경로입니다. |
| `runtime` | `runtime` | (선택 사항) 이미지에 사용할 런타임입니다. 지원 되는 런타임은 `spark-py` 및 `python`입니다. `environment` 설정 된 경우이 항목은 무시 됩니다. |
| `condaFile` | `conda_file` | (선택 사항) 이미지에 사용할 Conda 환경 정의가 포함 된 로컬 파일의 경로입니다.  `environment` 설정 된 경우이 항목은 무시 됩니다. |
| `extraDockerFileSteps` | `extra_docker_file_steps` | (선택 사항) 이미지를 설정할 때 실행할 추가 Docker 단계가 포함 된 로컬 파일의 경로입니다.  `environment` 설정 된 경우이 항목은 무시 됩니다.|
| `sourceDirectory` | `source_directory` | (선택 사항) 이미지를 만드는 모든 파일이 포함 된 폴더의 경로입니다 .이 폴더 또는 하위 폴더에 있는 모든 파일에 쉽게 액세스할 수 있습니다. 로컬 컴퓨터에서 웹 서비스에 대한 종속성으로 전체 폴더를 업로드할 수 있습니다. 참고: entry_script, conda_file 및 extra_docker_file_steps 경로는 source_directory 경로에 대한 상대 경로입니다. |
| `enableGpu` | `enable_gpu` | (선택 사항) 이미지에서 GPU 지원을 사용할지 여부를 지정 합니다. GPU 이미지는 Azure Container Instances와 같은 Azure 서비스에서 사용 해야 합니다. 예를 들어 계산, Azure Virtual Machines 및 Azure Kubernetes 서비스를 Azure Machine Learning 합니다. 기본값은 False입니다. `environment` 설정 된 경우이 항목은 무시 됩니다.|
| `baseImage` | `base_image` | (선택 사항) 기본 이미지로 사용 되는 사용자 지정 이미지입니다. 기본 이미지를 제공 하지 않는 경우 이미지는 제공 된 런타임 매개 변수를 기반으로 합니다. `environment` 설정 된 경우이 항목은 무시 됩니다. |
| `baseImageRegistry` | `base_image_registry` | (선택 사항) 기본 이미지를 포함 하는 이미지 레지스트리 `environment` 설정 된 경우이 항목은 무시 됩니다.|
| `cudaVersion` | `cuda_version` | (선택 사항) GPU 지원이 필요한 이미지에 대해 설치 하는 VERDA의 버전입니다. Azure 서비스에서 GPU 이미지를 사용 해야 합니다. 예를 들어 Azure Container Instances, Azure Machine Learning Compute, Azure Virtual Machines 및 Azure Kubernetes Service입니다. 지원 되는 버전은 9.0, 9.1 및 10.0입니다. `enable_gpu` 설정 된 경우 기본값은 9.1입니다. `environment` 설정 된 경우이 항목은 무시 됩니다. |
| `description` | `description` | 이미지에 대한 설명입니다. `environment` 설정 된 경우이 항목은 무시 됩니다.  |
| `environment` | `environment` | (선택 사항)  Azure Machine Learning [환경](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py).|

다음 JSON은 CLI와 함께 사용 하기 위한 유추 구성의 예입니다.

```json
{
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
}
```

유추 구성 파일에 Azure Machine Learning [환경의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 전체 사양을 포함할 수 있습니다. 이 환경이 작업 영역에 없는 경우 Azure Machine Learning 만들 수 있습니다. 그렇지 않으면 필요한 경우 Azure Machine Learning에서 환경을 업데이트 합니다. 다음 JSON은 예입니다.

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
                            "scikit-learn",
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

또한 기존 Azure Machine Learning [환경을](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 분리 된 CLI 매개 변수로 사용 하 고 유추 구성 파일에서 "환경" 키를 제거할 수 있습니다. 환경 이름에-e를 사용 하 고 환경 버전에--ev을 사용 합니다. --Ev를 지정 하지 않으면 최신 버전이 사용 됩니다. 다음은 유추 구성 파일의 예입니다.

```json
{
    "entryScript": "score.py",
    "sourceDirectory": null
}
```

다음 명령은 이전 유추 구성 파일 (myInferenceConfig)을 사용 하 여 모델을 배포 하는 방법을 보여 줍니다. 

또한 기존 Azure Machine Learning [환경의](https://docs.microsoft.com/python/api/azureml-core/azureml.core.environment.environment?view=azure-ml-py) 최신 버전 (AzureML-최소)을 사용 합니다.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic myInferenceConfig.json -e AzureML-Minimal --dc deploymentconfig.json
```
