---
title: REST API를 사용하여 IoT Edge의 Azure Stream Analytics에 대한 CI/CD 구현
description: REST API를 사용하여 Azure Stream Analytics에 대한 지속적인 통합 및 배포 파이프라인을 구현하는 방법에 알아봅니다.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/04/2018
ms.openlocfilehash: 40beb620e037061b189762a51e3c29d0fd251b27
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61362079"
---
# <a name="implement-cicd-for-stream-analytics-on-iot-edge-using-apis"></a>API를 사용하여 IoT Edge의 Stream Analytics에 대한 CI/CD 구현

REST API를 사용하여 Azure Stream Analytics 작업에 대한 지속적인 통합 및 배포를 사용할 수 있습니다. 이 문서에서는 API를 사용하는 예제 및 사용하는 방법을 제공합니다. REST API는 Azure Cloud Shell에서 지원되지 않습니다.

## <a name="call-apis-from-different-environments"></a>다른 환경에서 API 호출

REST API는 Linux 및 Windows 모두에서 호출할 수 있습니다. 다음 명령에는 API 호출에 대한 적절한 구문을 보여줍니다. 특정 API 사용량은 문서의 뒷부분에 나오는 섹션에서 설명합니다.

### <a name="linux"></a>Linux

Linux의 경우 `Curl` 또는 `Wget` 명령을 사용할 수 있습니다.

```bash
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” { <url> }   
```

```bash
wget -q -O- --{ <method> }-data="<request body>”--header=Content-Type:application/json --auth-no-challenge --http-user="<Admin>" --http-password="<password>" <url>
```
 
### <a name="windows"></a>Windows

Windows의 경우 PowerShell을 사용합니다. 

```powershell 
$user = "<username>" 
$pass = "<password>" 
$encodedCreds = [Convert]::ToBase64String([Text.Encoding]::ASCII.GetBytes(("{0}:{1}" -f $user,$pass))) 
$basicAuthValue = "Basic $encodedCreds" 
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]" 
$headers.Add("Content-Type", 'application/json') 
$headers.Add("Authorization", $basicAuthValue) 
$content = "<request body>" 
$response = Invoke-RestMethod <url>-Method <method> -Body $content -Headers $Headers 
echo $response 
```
 
## <a name="create-an-asa-job-on-edge"></a>Edge에서 ASA 작업 만들기 
 
Stream Analytics 작업을 만들려면 Stream Analytics API를 사용하여 PUT 메서드를 호출합니다.

|방법|요청 URL|
|------|-----------|
|PUT|https://management.azure.com/subscriptions/{**subscription-id**}/resourcegroups/{**resource-group-name**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**job-name**}?api-version=2017-04-01-preview|
 
**curl**을 사용하는 명령 예제입니다.

```curl
curl -u { <username:password> }  -H "Content-Type: application/json" -X { <method> } -d "{ <request body>}” https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}?api-version=2017-04-01-preview  
``` 
 
JSON에서 요청 본문의 예제입니다.

```json
{ 
  "location": "West US", 
  "tags": { "key": "value", "ms-suppressjobstatusmetrics": "true" }, 
  "sku": {  
      "name": "Standard" 
    }, 
  "properties": { 
    "sku": {  
      "name": "standard" 
    }, 
       "eventsLateArrivalMaxDelayInSeconds": 1, 
       "jobType": "edge", 
    "inputs": [ 
      { 
        "name": "{inputname}", 
        "properties": { 
         "type": "stream", 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ], 
    "transformation": { 
      "name": "{queryName}", 
      "properties": { 
        "query": "{query}" 
      } 
    }, 
    "package": { 
      "storageAccount" : { 
        "accountName": "{blobstorageaccountname}", 
        "accountKey": "{blobstorageaccountkey}" 
      }, 
      "container": "{blobcontaine}]" 
    }, 
    "outputs": [ 
      { 
        "name": "{outputname}", 
        "properties": { 
          "serialization": { 
            "type": "JSON", 
            "properties": { 
              "fieldDelimiter": ",", 
              "encoding": "UTF8" 
            } 
          }, 
          "datasource": { 
            "type": "GatewayMessageBus", 
            "properties": { 
            } 
          } 
        } 
      } 
    ] 
  } 
} 
```
 
자세한 내용은 [API 설명서](/rest/api/streamanalytics/stream-analytics-job)를 참조하세요.  
 
## <a name="publish-edge-package"></a>Edge 패키지 게시 
 
IoT Edge에서 Stream Analytics 작업을 게시하려면 Edge Package Publish API를 사용하여 POST 메서드를 호출합니다.

|방법|요청 URL|
|------|-----------|
|POST|https://management.azure.com/subscriptions/{**subscriptionid**}/resourceGroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/streamingjobs/{**jobname**}/publishedgepackage?api-version=2017-04-01-preview|

작업이 성공적으로 게시될 때까지 이 비동기 작업은 202 상태를 반환합니다. 위치 응답 헤더는 프로세스의 상태를 가져오는 데 사용하는 URI를 포함합니다. 프로세스가 실행 중인 동안 위치 헤더의 URI에 대한 호출은 202 상태를 반환합니다. 프로세스가 끝났을 때 위치 헤더의 URI는 200 상태를 반환합니다. 

**curl**을 사용하여 Edge 패키지 게시 호출의 예제입니다. 

```bash
curl -d -X POST https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{jobname}/publishedgepackage?api-version=2017-04-01-preview
```
 
POST 호출을 수행한 후 빈 본문을 사용하여 응답을 예상해야 합니다. 응답의 HEAD에 있는 URL을 살펴보고 추가로 사용하기 위해 기록합니다.
 
응답의 HEAD에서 URL의 예제입니다.

```
https://management.azure.com/subscriptions/{**subscriptionid**}/resourcegroups/{**resourcegroupname**}/providers/Microsoft.StreamAnalytics/StreamingJobs/{**resourcename**}/OperationResults/023a4d68-ffaf-4e16-8414-cb6f2e14fe23?api-version=2017-04-01-preview 
```
응답의 HEAD에 찾은 URL을 사용하여 API 호출을 수행하려면 다음 명령을 실행하기 전에 1~2분 정도 기다립니다. 200 응답을 얻지 못한 경우 명령을 다시 시도합니다.
 
**curl**을 사용하여 반환된 URL로 API 호출을 만드는 예제입니다.

```bash
curl -d –X GET https://management.azure.com/subscriptions/{subscriptionid}/resourceGroups/{resourcegroupname}/providers/Microsoft.StreamAnalytics/streamingjobs/{resourcename}/publishedgepackage?api-version=2017-04-01-preview 
```

응답에는 Edge 배포 스크립트에 추가하는 데 필요한 정보가 포함됩니다. 아래 예제는 배포 매니페스트에서 수집해야 할 정보와 추가할 위치를 보여줍니다.
 
성공적으로 게시한 후 샘플 응답 본문입니다.

```json
{ 
  edgePackageUrl : null 
  error : null 
  manifest : "{"supportedPlatforms":[{"os":"linux","arch":"amd64","features":[]},{"os":"linux","arch":"arm","features":[]},{"os":"windows","arch":"amd64","features":[]}],"schemaVersion":"2","name":"{jobname}","version":"1.0.0.0","type":"docker","settings":{"image":"{imageurl}","createOptions":null},"endpoints":{"inputs":["],"outputs":["{outputnames}"]},"twin":{"contentType":"assignments","content":{"properties.desired":{"ASAJobInfo":"{asajobsasurl}","ASAJobResourceId":"{asajobresourceid}","ASAJobEtag":"{etag}",”PublishTimeStamp”:”{publishtimestamp}”}}}}" 
  status : "Succeeded" 
} 
```

배포 매니페스트의 샘플입니다. 

```json
{ 
  "modulesContent": { 
    "$edgeAgent": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "runtime": { 
          "type": "docker", 
          "settings": { 
            "minDockerVersion": "v1.25", 
            "loggingOptions": "", 
            "registryCredentials": {} 
          } 
        }, 
        "systemModules": { 
          "edgeAgent": { 
            "type": "docker", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0", 
              "createOptions": "{}" 
            } 
          }, 
          "edgeHub": { 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0", 
              "createOptions": "{}" 
            } 
          } 
        }, 
        "modules": { 
          "<asajobname>": { 
            "version": "1.0", 
            "type": "docker", 
            "status": "running", 
            "restartPolicy": "always", 
            "settings": { 
              "image": "<settings.image>", 
              "createOptions": "<settings.createOptions>" 
            } 
            "version": "<version>", 
             "env": { 
              "PlanId": { 
               "value": "stream-analytics-on-iot-edge" 
          } 
        } 
      } 
    }, 
    "$edgeHub": { 
      "properties.desired": { 
        "schemaVersion": "1.0", 
        "routes": { 
            "route": "FROM /* INTO $upstream" 
        }, 
        "storeAndForwardConfiguration": { 
          "timeToLiveSecs": 7200 
        } 
      } 
    }, 
    "<asajobname>": { 
      "properties.desired": {<twin.content.properties.desired>} 
    } 
  } 
} 
```

배포 매니페스트의 구성 후 배포에 대한 [Azure CLI를 사용하여 Azure IoT Edge 모듈 배포](../iot-edge/how-to-deploy-modules-cli.md)를 참조하세요.


## <a name="next-steps"></a>다음 단계 
 
* [IoT Edge의 Azure Stream Analytics](stream-analytics-edge.md)
* [IoT Edge의 ASA 자습서](https://docs.microsoft.com/azure/iot-edge/tutorial-deploy-stream-analytics)
* [Visual Studio 도구를 사용하여 Stream Analytics Edge 작업 개발](stream-analytics-tools-for-visual-studio-edge-jobs.md)
