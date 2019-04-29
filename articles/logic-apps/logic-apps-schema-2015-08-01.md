---
title: 2015년 8월 1일 미리 보기의 스키마 업데이트 - Azure Logic Apps | Microsoft Docs
description: Azure Logic Apps의 논리 앱 정의에 대한 2015-08-01-preview 스키마 버전 업데이트
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 0d03a4d4-e8a8-4c81-aed5-bfd2a28c7f0c
ms.topic: article
ms.date: 05/31/2016
ms.openlocfilehash: 92f522c72f69218e55b1ee4cfff74511a30288b0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60553762"
---
# <a name="schema-updates-for-azure-logic-apps---august-1-2015-preview"></a>Azure Logic Apps에 대한 스키마 업데이트 - 2015년 8월 1일 미리 보기

이 스키마 및 Azure Logic Apps에 대한 API 버전에는 논리 앱의 안정성 및 사용 편의성을 개선하는 향상된 주요 기능이 포함됩니다.

* **APIApp** 작업 형식의 이름은 이제 [**APIConnection**](#api-connections)입니다.
* **Repeat** 작업 이름은 이제 [**Foreach**](#foreach)입니다.
* [**HTTP 수신기** API 앱](#http-listener)은 더 이상 필요하지 않습니다.
* 하위 워크플로 호출에 [새 스키마](#child-workflows)를 사용합니다.

<a name="api-connections"></a>

## <a name="move-to-api-connections"></a>API 연결로 이동

가장 큰 변화는 API를 사용하기 위해 더 이상 API Apps를 Azure 구독에 배포하지 않아도 된다는 점입니다. API를 사용하는 방법은 다음과 같습니다.

* 관리되는 API
* 사용자 지정 Web API

이러한 각 방식은 관리 및 호스팅 모델이 다르므로 약간 다르게 처리됩니다. 이 모델의 장점은 사용자의 Azure 리소스 그룹에 배포되는 리소스로 더 이상 제약되지 않는다는 점입니다. 

### <a name="managed-apis"></a>관리되는 API

Microsoft는 사용자를 대신해서 Office 365, Salesforce, Twitter, FTP 등의 몇 가지 API를 관리합니다. 관리되는 일부 API를 Bing Translate처럼 있는 그대로 사용할 수도 있지만, 구성 즉 “연결”이 필요한 경우도 있습니다.

예를 들어, Office 365를 사용하는 경우 Office 365 로그인 토큰을 포함하는 연결을 만들어야 합니다. 이 토큰은 논리 앱에서 항상 Office 365 API를 호출할 수 있도록 안전하게 저장되어 새로 고침됩니다. SQL이나 FTP 서버에 연결하려는 경우에는 연결 문자열이 있는 연결을 만들어야 합니다. 

이 정의에서 이러한 작업을 `APIConnection`이라고 합니다. 다음은 전자 메일을 보내도록 Office 365를 호출하는 연결에 대한 예입니다.

``` json
{
   "actions": {
      "Send_an_email": {
         "type": "ApiConnection",
         "inputs": {
            "host": {
               "api": {
                  "runtimeUrl": "https://msmanaged-na.azure-apim.net/apim/office365"
               },
               "connection": {
                  "name": "@parameters('$connections')['shared_office365']['connectionId']"
               }
            },
            "method": "POST",
            "body": {
               "Subject": "Reminder",
               "Body": "Don't forget!",
               "To": "me@contoso.com"
            },
            "path": "/Mail"
         }
      }
   }
}
```

`host` 개체는 API 연결에 고유한 입력의 일부로, `api` 및 `connection`의 두 부분을 포함합니다. `api` 개체는 관리 API가 호스팅되는 런타임 URL을 지정합니다. 다음 메서드를 호출하여 사용 가능한 모든 관리 API를 볼 수 있습니다.

```text
GET https://management.azure.com/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/locations/<location>/managedApis?api-version=2015-08-01-preview
```

API를 사용하는 경우 API에 *연결 매개 변수*가 정의되어 있거나 그렇지 않을 수 있습니다. 따라서 API에서 이 매개 변수를 정의하지 않는 경우에는 연결이 필요 없습니다. API에서 이 매개 변수를 정의하는 경우에는 지정된 이름으로 연결을 만들어야 합니다.  
그런 다음, 이 이름을 `host` 개체 내의 `connection` 개체에서 참조합니다. 리소스 그룹에서 연결을 만들려면 다음 메서드를 호출합니다.

```text
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/connections/<name>?api-version=2015-08-01-preview
```

다음 본문을 포함합니다.

``` json
{
   "properties": {
      "api": {
         "id": "/subscriptions/<Azure-subscription-ID>/providers/Microsoft.Web/managedApis/azureblob"
      },
      "parameterValues": {
         "accountName": "<Azure-storage-account-name-with-different-parameters-for-each-API>"
      }
   },
   "location": "<logic-app-location>"
}
```

### <a name="deploy-managed-apis-in-an-azure-resource-manager-template"></a>Azure Resource Manager 템플릿에서 관리 API 배포

대화형 로그인이 필요하지 않은 경우 Resource Manager 템플릿을 사용하여 전체 앱을 만들 수 있습니다.
로그인이 필요한 경우에도 Resource Manager 템플릿을 사용할 수 있지만, Azure Portal을 통해 연결에 권한을 부여해야 합니다. 

``` json
"resources": [ {
   "apiVersion": "2015-08-01-preview",
   "name": "azureblob",
   "type": "Microsoft.Web/connections",
   "location": "[resourceGroup().location]",
   "properties": {
      "api": {
         "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
      },
      "parameterValues": {
         "accountName": "[parameters('storageAccountName')]",
         "accessKey": "[parameters('storageAccountKey')]"
      }
    },
},
{
   "type": "Microsoft.Logic/workflows",
   "apiVersion": "2015-08-01-preview",
   "name": "[parameters('logicAppName')]",
   "location": "[resourceGroup().location]",
   "dependsOn": ["[resourceId('Microsoft.Web/connections', 'azureblob')]"],
   "properties": {
      "sku": {
         "name": "[parameters('sku')]",
         "plan": {
            "id": "[concat(resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('svcPlanName'))]"
         }
      },
      "parameters": {
         "$connections": {
             "value": {
                  "azureblob": {
                     "connectionId": "[concat(resourceGroup().id,'/providers/Microsoft.Web/connections/azureblob')]",
                     "connectionName": "azureblob",
                     "id": "[concat(subscription().id,'/providers/Microsoft.Web/locations/westus/managedApis/azureblob')]"
                  }
             }
         }
      },
      "definition": {
         "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "type": "Object",
            "$connections": {
               "defaultValue": {},
 
            }
         },
         "triggers": {
            "Recurrence": {
               "type": "Recurrence",
               "recurrence": {
                  "frequency": "Day",
                  "interval": 1
               }
            }
         },
         "actions": {
            "Create_file": {
               "type": "ApiConnection",
               "inputs": {
                  "host": {
                     "api": {
                        "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/azureblob"
                     },
                     "connection": {
                       "name": "@parameters('$connections')['azureblob']['connectionId']"
                     }
                  },
                  "method": "POST",
                  "queries": {
                     "folderPath": "[concat('/', parameters('containerName'))]",
                     "name": "helloworld.txt"
                  },
                  "body": "@decodeDataUri('data:, Hello+world!')",
                  "path": "/datasets/default/files"
               },
               "conditions": []
            }
         },
         "outputs": {}
      }
   }
} ]
```

이 예제에서 해당 연결은 리소스 그룹에서 작동하는 리소스일 뿐임을 알 수 있습니다. 연결은 구독에서 사용할 수 있는 관리 API를 참조합니다.

### <a name="your-custom-web-apis"></a>사용자 지정 Web API

Microsoft 관리 API가 아닌 사용자 고유의 API를 사용하는 경우 기본 제공 **HTTP** 작업을 사용하여 API를 호출합니다. API에 대한 Swagger 엔드포인트를 제공해야 합니다. 이 엔드포인트는 Logic App Designer에서 API의 입력 및 출력을 표시하는 데 도움이 됩니다. Swagger 엔드포인트가 없으면 디자이너에서 입력 및 출력을 불투명 JSON 개체로만 표시할 수 있습니다.

다음은 새 `metadata.apiDefinitionUrl` 속성을 보여 주는 예입니다.

``` json
"actions": {
   "mycustomAPI": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://mysite.azurewebsites.net/api/apidef/"  
      },
      "inputs": {
         "uri": "https://mysite.azurewebsites.net/api/getsomedata",
         "method": "GET"
      }
   }
}
```

Azure App Service에서 Web API를 호스트하는 경우 Web API는 디자이너에서 사용 가능한 작업 목록에 자동으로 표시됩니다. 그렇지 않으면 URL에 직접 붙여 넣어야 합니다. Swagger에서 지원하는 어떤 방법으로든 API 자체를 보호할 수 있지만 Swagger 엔드포인트를 논리 앱 디자이너에서 사용할 수 있게 하려면 엔드포인트를 인증하지 않아야 합니다.

### <a name="call-deployed-api-apps-with-2015-08-01-preview"></a>2015-08-01-preview로 배포된 API 앱 호출

이전에 API 앱을 배포한 경우, **HTTP** 작업을 통해 앱을 호출할 수 있습니다.
예를 들어 Dropbox를 사용하여 파일을 나열하는 경우 **2014-12-01-preview** 스키마 버전 정의에 다음과 같을 수 있습니다.

``` json
"definition": {
   "$schema": "https://schema.management.azure.com/schemas/2016-06-01/Microsoft.Logic.json",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token": {
         "defaultValue": "eyJ0eX...wCn90",
         "type": "String",
         "metadata": {
            "token": {
               "name": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token"
            }
         }
      }
    },
    "actions": {
       "dropboxconnector": {
          "type": "ApiApp",
          "inputs": {
             "apiVersion": "2015-01-14",
             "host": {
                "id": "/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector",
                "gateway": "https://avdemo.azurewebsites.net"
             },
             "operation": "ListFiles",
             "parameters": {
                "FolderPath": "/myfolder"
             },
             "authentication": {
                "type": "Raw",
                "scheme": "Zumo",
                "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
             }
          }
       }
    }
}
```

이제 다음과 같이 유사한 HTTP 작업을 빌드하고 논리 앱 정의의 `parameters` 섹션을 변경되지 않은 상태로 유지할 수 있습니다.

``` json
"actions": {
   "dropboxconnector": {
      "type": "Http",
      "metadata": {
         "apiDefinitionUrl": "https://avdemo.azurewebsites.net/api/service/apidef/dropboxconnector/?api-version=2015-01-14&format=swagger-2.0-standard"  
      },
      "inputs": {
         "uri": "https://avdemo.azurewebsites.net/api/service/invoke/dropboxconnector/ListFiles?api-version=2015-01-14",
         "method": "POST",
         "body": {
            "FolderPath": "/myfolder"
         },
         "authentication": {
            "type": "Raw",
            "scheme": "Zumo",
            "parameter": "@parameters('/subscriptions/<Azure-subscription-ID>/resourcegroups/avdemo/providers/Microsoft.AppService/apiapps/dropboxconnector/token')"
         }
      }
   }
}
```

이러한 속성을 하나씩 연습합니다.

| 작업 속성 | 설명 |
| --- | --- |
| `type` | `Http`다음 위치 대신`APIapp` |
| `metadata.apiDefinitionUrl` | 논리 앱 디자이너에서 이 작업을 사용하려는 경우 다음에서 생성되는 메타데이터 엔드포인트를 포함합니다. `{api app host.gateway}/api/service/apidef/{last segment of the api app host.id}/?api-version=2015-01-14&format=swagger-2.0-standard` |
| `inputs.uri` | 생성된 위치: `{api app host.gateway}/api/service/invoke/{last segment of the api app host.id}/{api app operation}?api-version=2015-01-14` |
| `inputs.method` | 항상 `POST` |
| `inputs.body` | API 앱 매개 변수와 동일 |
| `inputs.authentication` | API 앱 인증과 동일 |

이 방법은 모든 API App 작업에 대해 작동합니다. 하지만 이러한 이전 API Apps는 더 이상 지원되지 않습니다. 따라서 이전의 다른 두 옵션 중 하나로 전환해야 합니다(관리 API 또는 사용자 지정 Web API 호스팅).

<a name="foreach"></a>

## <a name="renamed-repeat-to-foreach"></a>'repeat'가 'foreach'로 이름 변경됨

이전 스키마 버전에서 **Repeat** 작업 이름이 혼동을 주며 **Repeat**가 실제로 각각의 루프에 해당되는지 제대로 포착되지 않는다는 의견을 많이 받았습니다. 그래서 이름을 `repeat`에서 `foreach`로 변경했습니다. 이전에는 다음 예와 같이 작업을 작성했습니다.

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "repeat": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{repeatItem()}"
      }
   }
}
```

이제는 다음 버전을 작성하게 됩니다.

``` json
"actions": {
   "pingBing": {
      "type": "Http",
      "foreach": "@range(0,2)",
      "inputs": {
         "method": "GET",
         "uri": "https://www.bing.com/search?q=@{item()}"
      }
   }
}
```

또한 현재 반복 중에 루프가 처리 중인 항목을 참조한 `repeatItem()` 함수는 이름이 `item()`으로 바뀌었습니다. 

### <a name="reference-outputs-from-foreach"></a>'foreach'의 참조 출력

간소화를 위해 `foreach` 작업의 출력은 이제 더 이상 `repeatItems`라는 개체에 래핑되지 않습니다. 이와 같이 변경되면서 함수 `repeatItem()`, `repeatBody()` 및 `repeatOutputs()`가 제거되었습니다.

따라서 이전 `repeat` 예를 사용하면 출력이 다음과 같습니다.

``` json
"repeatItems": [ {
   "name": "pingBing",
   "inputs": {
      "uri": "https://www.bing.com/search?q=0",
      "method": "GET"
   },
   "outputs": {
      "headers": { },
      "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
   },
   "status": "Succeeded"
} ]
```

이제는 대신 다음과 같은 출력을 얻게 됩니다.

``` json
[ {
   "name": "pingBing",
      "inputs": {
         "uri": "https://www.bing.com/search?q=0",
         "method": "GET"
      },
      "outputs": {
         "headers": { },
         "body": "<!DOCTYPE html><html lang=\"en\" xml:lang=\"en\" xmlns=\"http://www.w3.org/1999/xhtml\" xmlns:Web=\"https://schemas.live.com/Web/\">...</html>"
      },
      "status": "Succeeded"
} ]
```

이전에는 이러한 출력을 참조할 때 작업에서 `body`를 얻으려면 다음을 수행했습니다.

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "repeat": "@outputs('pingBing').repeatItems",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@repeatItem().outputs.body"
      }
   }
}
```

이제는 다음 버전을 사용하면 됩니다.

``` json
"actions": {
   "secondAction": {
      "type": "Http",
      "foreach": "@outputs('pingBing')",
      "inputs": {
         "method": "POST",
         "uri": "https://www.example.com",
         "body": "@item().outputs.body"
      }
   }
}
```

<a name="http-listener"></a>

## <a name="native-http-listener"></a>네이티브 HTTP 수신기

이제 HTTP 수신기 기능이 기본 제공되므로 HTTP 수신기 API 앱을 배포할 필요가 없습니다. 자세한 내용은 [논리 앱 엔드포인트를 호출 가능한 상태로 만드는 방법](../logic-apps/logic-apps-http-endpoint.md)을 참조하세요. 

이러한 변경 때문에 Logic Apps는 필요한 경우 엔드포인트를 가져오는 `@listCallbackURL()` 함수로 `@accessKeys()` 함수를 대체합니다. 또한 이제 논리 앱에서 트리거를 하나 이상 정의해야 합니다. 워크플로를 `/run`하려는 경우 `Manual`, `ApiConnectionWebhook` 또는 `HttpWebhook` 트리거 유형 중 하나를 사용해야 합니다.

<a name="child-workflows"></a>

## <a name="call-child-workflows"></a>하위 워크플로 호출

이전에는 하위 워크플로를 호출하려면 해당 워크플로로 이동하여 액세스 토큰을 가져온 후 해당 하위 워크플로를 호출하려는 논리 앱 정의에 토큰을 붙여넣어야 했습니다. 이 스키마를 사용할 경우 Logic Apps 엔진은 런타임에 하위 워크플로에 대한 SAS를 자동으로 생성합니다. 따라서 정의에 어떠한 비밀도 붙여넣을 필요가 없습니다. 다음은 예제입니다.

``` json
"myNestedWorkflow": {
   "type": "Workflow",
   "inputs": {
      "host": {
         "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Logic/myWorkflow001",
         "triggerName": "myEndpointTrigger"
      },
      "queries": {
         "extrafield": "specialValue"
      },
      "headers": {
         "x-ms-date": "@utcnow()",
         "Content-type": "application/json"
      },
      "body": {
         "contentFieldOne": "value100",
         "anotherField": 10.001
      }
   },
   "conditions": []
}
```

또한 하위 워크플로가 수신 요청에 대한 모든 권한을 갖습니다. 따라서 `queries` 섹션 및 `headers` 개체에 매개 변수를 전달할 수 있습니다. 전체 `body` 섹션을 완전히 정의할 수도 있습니다.

마지막으로, 하위 워크플로에 이러한 필수 변경 내용이 포함됩니다. 이전에는 하위 워크플로를 직접 호출할 수 있었지만 이제 상위에서 호출할 워크플로에 트리거 엔드포인트를 정의해야 합니다. 일반적으로 `Manual` 유형의 트리거를 추가한 후 상위 정의에 해당 트리거를 사용합니다. 항상 사용자가 호출하는 트리거를 지정해야 하므로 특히 `host` 속성에 `triggerName`이 있습니다.

## <a name="other-changes"></a>기타 변경 내용

### <a name="new-queries-property"></a>새 'queries' 속성

이제 모든 작업 유형에서 `queries`라는 새 입력을 지원합니다. 이 입력은 문자열을 직접 조합할 필요가 없는 구조화된 개체일 수 있습니다.

### <a name="renamed-parse-function-to-json"></a>'parse()' 함수 이름이 'json()'으로 변경되었습니다.

이후 콘텐츠 형식에 대한 `parse()` 함수의 이름이 이제 `json()` 함수로 변경되었습니다.

## <a name="enterprise-integration-apis"></a>엔터프라이즈 통합 API

이 스키마는 AS2와 같은 엔터프라이즈 통합 API에 대한 관리 버전을 아직 지원하지 않습니다. 하지만 HTTP 작업을 통해 기존에 배포된 BizTalk API를 사용할 수 있습니다. 자세한 내용은 [통합 로드맵](https://www.zdnet.com/article/microsoft-outlines-its-cloud-and-server-integration-roadmap-for-2016/)의 "이미 배포된 API 앱 사용"을 참조하세요. 
