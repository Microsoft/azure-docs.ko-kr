---
title: REST를 사용 하 여 ML 리소스 관리
titleSuffix: Azure Machine Learning
description: REST Api를 사용 하 여 Azure ML 리소스를 만들고, 실행 하 고, 삭제 하는 방법
author: lobrien
ms.author: laobri
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 01/31/2020
ms.openlocfilehash: 6d6e7d564722d1c2ad4713dd1d39e7cba5ed0605
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964957"
---
# <a name="create-run-and-delete-azure-ml-resources-using-rest"></a>REST를 사용 하 여 Azure ML 리소스 만들기, 실행 및 삭제

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Azure ML 리소스를 관리 하는 방법에는 여러 가지가 있습니다. [포털](https://portal.azure.com/), [명령줄 인터페이스](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)또는 [Python SDK](https://docs.microsoft.com/python/api/overview/azureml-sdk/?view=azure-ml-py)를 사용할 수 있습니다. 또는 REST API를 선택할 수 있습니다. REST API는 표준 방식으로 HTTP 동사를 사용 하 여 리소스를 만들고, 검색 하 고, 업데이트 하 고, 삭제 합니다. REST API는 HTTP 요청을 수행할 수 있는 모든 언어 또는 도구와 함께 작동 합니다. REST의 간단한 구조를 사용 하면 스크립팅 환경 및 MLOps 자동화에 적합 합니다. 

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 권한 부여 토큰 검색
> * 서비스 주체 인증을 사용 하 여 올바른 형식의 REST 요청 만들기
> * GET 요청을 사용 하 여 Azure ML의 계층 구조 리소스에 대 한 정보 검색
> * PUT 및 POST 요청을 사용 하 여 리소스 만들기 및 수정
> * DELETE 요청을 사용 하 여 리소스 정리 
> * 키 기반 권한 부여를 사용 하 여 배포 된 모델 점수 매기기

## <a name="prerequisites"></a>필수 조건

- 관리 권한이 있는 **Azure 구독** . 이러한 구독이 없는 경우 [무료 또는 유료 개인 구독](https://aka.ms/AMLFree) 을 사용해 보세요.
- [Azure Machine Learning 작업 영역](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace)
- 관리 REST 요청은 서비스 주체 인증을 사용 합니다. [Azure Machine Learning 리소스 및 워크플로에 대 한 인증 설정](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication) 의 단계에 따라 작업 영역에서 서비스 주체를 만듭니다.
- **말아 넘기기** 유틸리티입니다. **말아 넘기기** 프로그램은 [Linux 용 Windows 하위 시스템](https://aka.ms/wslinstall/) 또는 UNIX 배포에서 사용할 수 있습니다. PowerShell에서 **말아 넘기기** 는 **호출 WebRequest** 의 별칭이 며 `curl -d "key=val" -X POST uri` `Invoke-WebRequest -Body "key=val" -Method POST -Uri uri`됩니다. 

## <a name="retrieve-a-service-principal-authentication-token"></a>서비스 주체 인증 토큰 검색

관리 REST 요청은 OAuth2 암시적 흐름으로 인증 됩니다. 이 인증 흐름은 구독의 서비스 사용자가 제공 하는 토큰을 사용 합니다. 이 토큰을 검색 하려면 다음이 필요 합니다.

- 사용자의 테넌트 ID (구독이 속한 조직을 식별 하는)
- 클라이언트 ID (생성 된 토큰과 연결 됨)
- 클라이언트 암호 (보호 해야 함)

[Azure Machine Learning 리소스 및 워크플로에 대 한 인증 설정](https://docs.microsoft.com/azure/machine-learning/how-to-setup-authentication#set-up-service-principal-authentication)에 설명 된 대로 서비스 사용자 만들기에 대 한 응답의 값이 있어야 합니다. 회사 구독을 사용 하는 경우 서비스 주체를 만들 수 있는 권한이 없는 것일 수 있습니다. 이 경우 [무료 또는 유료 개인 구독](https://aka.ms/AMLFree)중 하나를 사용 해야 합니다.

토큰을 검색 하려면:

1. 터미널 창 열기
1. 명령줄에 다음 코드를 입력 합니다.
1. `{your-tenant-id}`, `{your-client-id}`및 `{your-client-secret}`에 대 한 고유한 값을 대체 합니다. 이 문서 전체에서 중괄호로 묶은 문자열은 적절 한 값으로 바꿔야 하는 변수입니다.
1. 명령 실행

```bash
curl -X POST https://login.microsoftonline.com/{your-tenant-id}/oauth2/token \
-d "grant_type=client_credentials&resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id={your-client-id}&client_secret={your-client-secret}" \
```

응답은 1 시간 동안 유효한 액세스 토큰을 제공 해야 합니다.

```json
{
    "token_type": "Bearer",
    "expires_in": "3599",
    "ext_expires_in": "3599",
    "expires_on": "1578523094",
    "not_before": "1578519194",
    "resource": "https://management.azure.com/",
    "access_token": "your-access-token"
}
```

토큰을 기록해 둡니다 .이 토큰을 사용 하 여 모든 후속 관리 요청을 인증할 수 있습니다. 모든 요청에 권한 부여 헤더를 설정 하 여이 작업을 수행 합니다.

```bash
curl -h "Authentication: Bearer {your-access-token}" ...more args...
```

토큰을 추가 하기 전에 단일 공백을 포함 하는 문자열 "전달자"로 시작 하는 값을 확인 합니다.

## <a name="get-a-list-of-resource-groups-associated-with-your-subscription"></a>구독과 연결 된 리소스 그룹 목록 가져오기

구독과 연결 된 리소스 그룹 목록을 검색 하려면 다음을 실행 합니다.

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups?api-version=2019-11-01 -H "Authorization:Bearer {your-access-token}"
```

Azure 전체에서 많은 REST Api가 게시 됩니다. 각 서비스 공급자는 자체의 주기에서 API를 업데이트 하지만 기존 프로그램을 중단 하지 않습니다. 서비스 공급자는 `api-version` 인수를 사용 하 여 호환성을 보장 합니다. `api-version` 인수는 서비스 마다 다릅니다. 예를 들어 Machine Learning 서비스의 경우 현재 API 버전이 `2019-11-01`됩니다. 저장소 계정의 경우 `2019-06-01`됩니다. 키 자격 증명 모음의 경우 `2019-09-01`됩니다. 모든 REST 호출은 `api-version` 인수를 예상 값으로 설정 해야 합니다. API가 계속 진화 하더라도 지정 된 버전의 구문 및 의미 체계를 사용할 수 있습니다. `api-version` 인수 없이 공급자에 게 요청을 보내는 경우 응답에는 지원 되는 값의 사람이 읽을 수 있는 목록이 포함 됩니다. 

위의 호출로 인해 다음과 같은 형식의 JSON 응답이 압축 됩니다. 

```json
{
    "value": [
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG1",
            "name": "RG1",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "westus2",
            "properties": {
                "provisioningState": "Succeeded"
            }
        },
        {
            "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/RG2",
            "name": "RG2",
            "type": "Microsoft.Resources/resourceGroups",
            "location": "eastus",
            "properties": {
                "provisioningState": "Succeeded"
            }
        }
    ]
}
```


## <a name="drill-down-into-workspaces-and-their-resources"></a>작업 영역 및 해당 리소스로 드릴 다운

리소스 그룹의 작업 영역 집합을 검색 하려면 `{your-subscription-id}`, `{your-resource-group}`및 `{your-access-token}`를 대체 하 여 다음을 실행 합니다. 

```
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

여기서는 작업 영역에 대 한 세부 정보를 나열 하는 목록이 포함 된 JSON 목록을 받게 됩니다.

```json
{
    "id": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourceGroups/DeepLearningResourceGroup/providers/Microsoft.MachineLearningServices/workspaces/my-workspace",
    "name": "my-workspace",
    "type": "Microsoft.MachineLearningServices/workspaces",
    "location": "centralus",
    "tags": {},
    "etag": null,
    "properties": {
        "friendlyName": "",
        "description": "",
        "creationTime": "2020-01-03T19:56:09.7588299+00:00",
        "storageAccount": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.storage/storageaccounts/myworkspace0275623111",
        "containerRegistry": null,
        "keyVault": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.keyvault/vaults/myworkspace2525649324",
        "applicationInsights": "/subscriptions/12345abc-abbc-1b2b-1234-57ab575a5a5a/resourcegroups/DeepLearningResourceGroup/providers/microsoft.insights/components/myworkspace2053523719",
        "hbiWorkspace": false,
        "workspaceId": "cba12345-abab-abab-abab-ababab123456",
        "subscriptionState": null,
        "subscriptionStatusChangeTimeStampUtc": null,
        "discoveryUrl": "https://centralus.experiments.azureml.net/discovery"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "abcdef1-abab-1234-1234-abababab123456",
        "tenantId": "1fedcba-abab-1234-1234-abababab123456"
    },
    "sku": {
        "name": "Basic",
        "tier": "Basic"
    }
}
```

작업 영역 내에서 리소스를 사용 하려면 일반 **management.azure.com** 서버에서 작업 영역의 위치와 관련 된 REST API 서버로 전환 합니다. 위의 JSON 응답에서 `discoveryUrl` 키의 값을 확인 합니다. 해당 URL을 가져오면 다음과 같은 응답을 받게 됩니다.

```json
{
  "api": "https://centralus.api.azureml.ms",
  "catalog": "https://catalog.cortanaanalytics.com",
  "experimentation": "https://centralus.experiments.azureml.net",
  "gallery": "https://gallery.cortanaintelligence.com/project",
  "history": "https://centralus.experiments.azureml.net",
  "hyperdrive": "https://centralus.experiments.azureml.net",
  "labeling": "https://centralus.experiments.azureml.net",
  "modelmanagement": "https://centralus.modelmanagement.azureml.net",
  "pipelines": "https://centralus.aether.ms",
  "studiocoreservices": "https://centralus.studioservice.azureml.com"
}
```

`api` 응답의 값은 추가 요청에 사용할 서버의 URL입니다. 예를 들어 실험을 나열 하려면 다음 명령을 보냅니다. `regional-api-server`를 `api` 응답 값 (예를 들어 `centralus.api.azureml.ms`)으로 바꿉니다. 또한 `your-subscription-id`, `your-resource-group`, `your-workspace-name`및 `your-access-token`를 평소와 같이 바꿉니다.

```bash
curl https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

마찬가지로 작업 영역에서 등록 된 모델을 검색 하려면 다음을 보냅니다.

```bash
curl https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

모델을 나열 하는 동안 경로가 `history/v1.0`로 시작 되는 실험을 나열 하는 것은 `modelmanagement/v1.0`으로 시작 합니다. REST API는 각각 고유한 경로를 가진 여러 작업 그룹으로 구분 됩니다. 아래 링크의 API 참조 문서에는 다양 한 작업에 대 한 작업, 매개 변수 및 응답 코드가 나열 되어 있습니다.

|영역|경로|참조|
|-|-|-|
|Artifacts|아티팩트/v 2.0/|[REST API 참조](https://docs.microsoft.com/rest/api/azureml/artifacts)|
|데이터 저장소|데이터 저장소/v 1.0/|[REST API 참조](https://docs.microsoft.com/rest/api/azureml/datastores)|
|하이퍼 매개 변수 튜닝|hyperdrive/v 1.0/|[REST API 참조](https://docs.microsoft.com/rest/api/azureml/hyperparametertuning)|
|모델|modelmanagement/v 1.0/|[REST API 참조](https://docs.microsoft.com/rest/api/azureml/modelsanddeployments/mlmodels)|
|실행 기록|실행/v 1.0/및 기록/v 1.0/|[REST API 참조](https://docs.microsoft.com/rest/api/azureml/runs)|

의 일반적인 패턴을 사용 하 여 REST API를 탐색할 수 있습니다.

|URL 구성 요소|예|
|-|-|
| https://| |
| 지역-api-서버/ | centralus.api.azureml.ms/ |
| 작업-경로/ | 기록/v 1.0/ |
| 구독/{구독 id}/ | subscription/abcde123-abab-1234-0123456789abc/ |
| resourceGroups/{리소스 그룹}/ | resourceGroups/MyResourceGroup/ |
| 공급자/작업-공급자/ | 공급자/MachineLearningServices/ |
| 공급자-리소스 경로/ | workspace/MLWorkspace/MyWorkspace/FirstExperiment/실행/1/ |
| 작업-엔드포인트/ | 아티팩트/메타 데이터/ |


## <a name="create-and-modify-resources-using-put-and-post-requests"></a>PUT 및 POST 요청을 사용 하 여 리소스 만들기 및 수정

GET 동사를 사용 하 여 리소스를 검색 하는 것 외에도 REST API는 ML 솔루션을 학습, 배포 및 모니터링 하는 데 필요한 모든 리소스를 만들 수 있도록 지원 합니다. 

ML 모델을 학습 하 고 실행 하려면 계산 리소스가 필요 합니다. 다음을 사용 하 여 작업 영역의 계산 리소스를 나열할 수 있습니다. 

```bash
curl https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes?api-version=2019-11-01 \
-H "Authorization:Bearer {your-access-token}"
```

명명 된 계산 리소스를 만들거나 덮어쓰려면 PUT 요청을 사용 합니다. 다음에서는 `your-subscription-id`, `your-resource-group`, `your-workspace-name`및 `your-access-token`에 대 한 친숙 한 대체 방법 외에도 `your-compute-name`, `location`, `vmSize`, `vmPriority`, `scaleSettings`, `adminUserName`에 대 한 `adminUserPassword`및 값을 대체 합니다. [Machine Learning 컴퓨팅-SDK 참조 만들기 또는 업데이트](https://docs.microsoft.com/rest/api/azureml/workspacesandcomputes/machinelearningcompute/createorupdate)에서 참조에 지정 된 대로 다음 명령은 30 분 후에 규모를 축소 하는 전용 단일 노드 Standard_D1 (기본 CPU 계산 리소스)를 만듭니다.

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/computes/{your-compute-name}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{your-azure-location}",
    "properties": {
        "computeType": "AmlCompute",
        "properties": {
            "vmSize": "Standard_D1",
            "vmPriority": "Dedicated",
            "scaleSettings": {
                "maxNodeCount": 1,
                "minNodeCount": 0,
                "nodeIdleTimeBeforeScaleDown": "PT30M"
            }
        }
    },
    "userAccountCredentials": {
        "adminUserName": "{adminUserName}",
        "adminUserPassword": "{adminUserPassword}"
    }
}'
```

> [!Note]
> Windows 터미널에서 JSON 데이터를 보낼 때 큰따옴표 기호를 이스케이프 해야 할 수 있습니다. 즉, `"location"`와 같은 텍스트가 `\"location\"`됩니다. 

요청에 성공 하면 `201 Created` 응답이 수신 되지만이 응답은 단순히 프로 비전 프로세스가 시작 되었음을 의미 합니다. 성공적으로 완료 되었는지 확인 하려면 폴링을 수행 하거나 포털을 사용 해야 합니다.

### <a name="create-an-experimental-run"></a>실험적 실행 만들기

실험 내에서 실행을 시작 하려면 학습 스크립트 및 관련 파일을 포함 하는 zip 폴더와 실행 정의 JSON 파일이 필요 합니다. Zip 폴더의 루트 디렉터리에는 Python 항목 파일이 있어야 합니다. 예를 들어 다음과 같은 간단한 Python 프로그램을 **열차**라는 폴더에 압축 합니다.

```python
# hello.py
# Entry file for run
print("Hello, REST!")
```

이 다음 코드 조각을 정의로 저장 합니다 **. json**. "스크립트" 값이 방금 압축 한 Python 파일의 이름과 일치 하는지 확인 합니다. "대상" 값이 사용 가능한 계산 리소스의 이름과 일치 하는지 확인 합니다. 

```json
{
    "Configuration":{  
       "Script":"hello.py",
       "Arguments":[  
          "234"
       ],
       "SourceDirectoryDataStore":null,
       "Framework":"Python",
       "Communicator":"None",
       "Target":"cpu-compute",
       "MaxRunDurationSeconds":1200,
       "NodeCount":1,
       "Environment":{  
          "Python":{  
             "InterpreterPath":"python",
             "UserManagedDependencies":false,
             "CondaDependencies":{  
                "name":"project_environment",
                "dependencies":[  
                   "python=3.6.2",
                   {  
                      "pip":[  
                         "azureml-defaults"
                      ]
                   }
                ]
             }
          },
          "Docker":{  
             "BaseImage":"mcr.microsoft.com/azureml/base:intelmpi2018.3-ubuntu16.04"
          }
      },
       "History":{  
          "OutputCollection":true
       }
    }
}
```

`multipart/form-data` 콘텐츠를 사용 하 여 서버에 다음 파일을 게시 합니다.

```bash
curl https://{regional-api-server}/execution/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-name}/startrun?api-version=2019-11-01 \
  -X POST \
  -H "Content-Type: multipart/form-data" \
  -H "Authorization:Bearer {your-access-token}" \
  -F projectZipFile=@train.zip \
  -F runDefinitionFile=@runDefinition.json
```

POST 요청이 성공 하면 생성 된 실행의 식별자가 포함 된 응답 본문과 함께 `200 OK` 상태가 생성 됩니다.

```json
{
  "runId": "my-first-experiment_1579642222877"
}
```

이제 친숙 한 나머지 패턴을 사용 하 여 실행을 모니터링할 수 있습니다.

```bash
curl 'https://{regional-api-server}/history/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/experiments/{your-experiment-names}/runs/{your-run-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}'
```

### <a name="delete-resources-you-no-longer-need"></a>더 이상 필요 하지 않은 리소스를 삭제 합니다.

일부 리소스는 삭제 동사를 지원 합니다. 삭제 사용 사례에 대 한 REST API 커밋하기 전에 [API 참조](https://docs.microsoft.com/rest/api/azureml/) 를 확인 하세요. 예를 들어 모델을 삭제 하려면 다음을 사용할 수 있습니다.

```bash
curl
  -X DELETE \
'https://{regional-api-server}/modelmanagement/v1.0/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.MachineLearningServices/workspaces/{your-workspace-name}/models/{your-model-id}?api-version=2019-11-01' \
  -H 'Authorization:Bearer {your-access-token}' 
```

## <a name="use-rest-to-score-a-deployed-model"></a>REST를 사용 하 여 배포 된 모델 점수 매기기

서비스 사용자로 인증 되도록 모델을 배포할 수 있지만 대부분의 클라이언트 연결 배포는 키 기반 인증을 사용 합니다. 스튜디오의 **엔드포인트** 탭에 있는 배포 페이지에서 적절 한 키를 찾을 수 있습니다. 동일한 위치에 엔드포인트의 점수 매기기 URI가 표시 됩니다. 모델의 입력은 `data`이라는 JSON 배열로 모델링 되어야 합니다.

```bash
curl 'https://{scoring-uri}' \
 -H 'Authorization:Bearer {your-key}' \
 -H 'Content-Type: application/json' \
  -d '{ "data" : [ {model-specific-data-structure} ] }
```

## <a name="create-a-workspace-using-rest"></a>REST를 사용 하 여 작업 영역 만들기 

모든 Azure ML 작업 영역은 다른 4 개의 Azure 리소스 (관리를 사용 하도록 설정 된 컨테이너 레지스트리, 주요 자격 증명 모음, Application Insights 리소스 및 저장소 계정)에 종속 됩니다. 이러한 리소스가 있어야 작업 영역을 만들 수 있습니다. 이러한 각 리소스를 만드는 방법에 대 한 자세한 내용은 REST API 참조를 참조 하세요.

작업 영역을 만들려면 다음과 비슷한 호출을 `management.azure.com`합니다. 이 호출에서는 많은 수의 변수를 설정 해야 하지만이 문서에서 설명 하는 다른 호출과 구조적으로 동일 합니다. 

```bash
curl -X PUT \
  'https://management.azure.com/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.MachineLearningServices/workspaces/{your-new-workspace-name}?api-version=2019-11-01' \
  -H 'Authorization: Bearer {your-access-token}' \
  -H 'Content-Type: application/json' \
  -d '{
    "location": "{desired-region}",
    "properties": {
        "friendlyName" : "{your-workspace-friendly-name}",
        "description" : "{your-workspace-description}",
        "containerRegistry" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.ContainerRegistry/registries/{your-registry-name}",
        keyVault" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}\
/providers/Microsoft.Keyvault/vaults/{your-keyvault-name}",
        "applicationInsights" : "subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.insights/components/{your-application-insights-name}",
        "storageAccount" : "/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/\
providers/Microsoft.Storage/storageAccounts/{your-storage-account-name}"
    },
    "identity" : {
        "type" : "systemAssigned"
    }
}'
```

`202 Accepted` 응답을 수신 하 고 반환 된 헤더에 `Location` URI를 제공 해야 합니다. 종속 리소스 중 하나에 문제가 있는 경우 (예를 들어 컨테이너 레지스트리에서 관리자 액세스를 사용 하도록 설정 하지 않은 경우) 유용한 디버깅 정보를 포함 하 여 배포에 대 한 정보에 대 한이 URI를 가져올 수 있습니다. 

## <a name="next-steps"></a>다음 단계

- 전체 [AzureML REST API 참조](https://docs.microsoft.com/rest/api/azureml/)를 살펴봅니다.
- Studio & Designer를 사용 하 여 [디자이너 (미리 보기)로 자동차 가격을 예측](https://docs.microsoft.com/azure/machine-learning/tutorial-designer-automobile-price-train-score)하는 방법을 알아봅니다.
- [Jupyter 노트북을 사용 하 여 Azure Machine Learning을](https://docs.microsoft.com/azure//machine-learning/samples-notebooks)탐색 합니다.
