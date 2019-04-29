---
title: Azure Logic Apps에 대한 액세스 보호 | Microsoft Docs
description: 트리거, 입력 및 출력, 매개 변수 및 기타 서비스를 포함하여 Azure Logic Apps에 대한 보안 추가
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
ms.assetid: 9fab1050-cfbc-4a8b-b1b3-5531bee92856
ms.topic: article
ms.date: 02/05/2019
ms.openlocfilehash: 31b18e2dfbad1f572b5c9b0ae59f5e076db7cde2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60995697"
---
# <a name="secure-access-in-azure-logic-apps"></a>Azure Logic Apps에서 액세스 보호

액세스를 보호할 수 있는 논리 앱의 요소는 다음과 같습니다.

* [요청 또는 웹후크 트리거](#secure-triggers)
* 논리 앱 [관리, 편집 또는 보기 등의 작업](#secure-operations)
* 논리 앱 실행 기록의 [입력 및 출력](#secure-run-history)
* [작업 매개 변수 및 입력](#secure-action-parameters)
* 논리 앱에서 [요청을 가져오는 서비스](#secure-requests)

<a name="secure-triggers"></a>

## <a name="secure-access-to-request-triggers"></a>요청 트리거에 대한 보안 액세스

논리 앱이 HTTP 요청 기반 트리거(예: [요청](../connectors/connectors-native-reqres.md) 또는 [웹후크](../connectors/connectors-native-webhook.md) 트리거)를 사용할 경우 권한 있는 클라이언트만 논리 앱을 시작할 수 있도록 액세스를 제한할 수 있습니다. 논리 앱에서 받은 모든 요청은 SSL(Secure Sockets Layer) 프로토콜을 사용하여 암호화되고 보안이 유지됩니다. 이 트리거 형식에 대한 액세스를 보호할 수 있는 몇 가지 방법은 다음과 같습니다.

* [공유 액세스 서명 생성](#sas)
* [받는 IP 주소 제한](#restrict-incoming-ip-addresses)
* [Azure Active Directory, OAuth 또는 기타 보안 추가](#add-authentication)

<a name="sas"></a>

### <a name="generate-shared-access-signatures"></a>공유 액세스 서명 생성

논리 앱에 대한 모든 요청 엔드포인트는 엔드포인트 URL에 [SAS(공유 액세스 서명)](../storage/common/storage-dotnet-shared-access-signature-part-1.md))를 포함합니다. 각 URL은 `sp`, `sv` 및 `sig` 쿼리 매개 변수를 포함합니다.

* `sp`는 사용이 허용된 HTTP 메서드에 매핑되는 권한을 지정합니다.
* `sv`는 서명을 생성하는 데 사용되는 버전을 지정합니다.
* `sig`는 트리거에 대한 액세스 인증에 사용됩니다.

서명은 모든 URL 경로 및 속성에 관한 비밀 액세스 키를 포함한 SHA256 알고리즘을 사용하여 생성됩니다. 비밀 키는 절대 노출되거나 공개되지 않으며 논리 앱과 함께 저장됩니다. 논리 앱은 암호 키로 만들어진 유효한 서명을 포함하는 트리거에 권한을 부여합니다. 

공유 액세스 서명으로 액세스를 보호하는 방법에 대한 자세한 내용은 다음과 같습니다.

* [액세스 키 다시 생성](#access-keys)
* [만료 콜백 URL 만들기](#expiring-urls)
* [주 또는 보조 키를 사용하여 URL 만들기](#primary-secondary-key)

<a name="access-keys"></a>

#### <a name="regenerate-access-keys"></a>액세스 키 다시 생성

언제든지 새 보안 액세스 키를 다시 생성하려면 REST API 또는 Azure Portal을 사용합니다. 이전 키를 사용하는 이전에 생성된 모든 현재 URL은 무효화되고 논리 앱을 트리거하기 위한 권한이 더 이상 부여되지 않습니다. 다시 생성한 후 검색한 URL은 새 액세스 키로 서명됩니다.

1. Azure Portal에서 다시 생성하려는 키가 있는 논리 앱을 엽니다.

1. 논리 앱 메뉴의 **설정**에서 **액세스 키**를 선택합니다.

1. 다시 생성하려는 키를 선택하고 프로세스를 완료합니다.

<a name="expiring-urls"></a>

#### <a name="create-callback-urls-with-expiration-dates"></a>만료 날짜가 있는 콜백 URL 만들기

트리거 요청 기반 엔드포인트의 URL을 다른 상대방과 공유하는 경우 필요에 따라 특정 키와 만료 날짜를 지정한 콜백 URL을 생성할 수 있습니다. 그런 후 키를 원활하게 키를 롤하거나, 논리 앱을 트리거하기 위한 액세스 권한을 특정 시간 범위로 제한할 수 있습니다. 다음과 같이 [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)를 사용하여 URL의 만료 날짜를 지정할 수 있습니다.

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

본문에 JSON 날짜 문자열을 사용하여 `NotAfter` 속성을 포함합니다. 이 속성은 `NotAfter` 날짜 및 시간까지만 유효한 콜백 URL을 반환합니다.

<a name="primary-secondary-key"></a>

#### <a name="create-urls-with-primary-or-secondary-secret-key"></a>기본 또는 보조 비밀 키로 URL 만들기

요청 기반 트리거의 콜백 URL을 생성하거나 나열하면, URL에 서명하는 데 사용할 키를 지정할 수도 있습니다. 다음과 같이 [Logic Apps REST API](https://docs.microsoft.com/rest/api/logic/workflowtriggers)을 사용하여 특정 키로 서명된 URL을 생성할 수 있습니다.

``` http
POST 
/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/workflows/{workflowName}/triggers/{triggerName}/listCallbackUrl?api-version=2016-06-01
```

본문에서는 속성 `KeyType`을 `Primary` 또는 `Secondary`로서 포함합니다. 이 속성은 지정된 보안 키로 서명된 URL을 반환합니다.

<a name="restrict-incoming-ip"></a>

### <a name="restrict-incoming-ip-addresses"></a>받는 IP 주소 제한

공유 액세스 서명에 따라, 논리 앱을 호출할 수 있는 특정 클라이언트를 제한할 수 있습니다.  
예를 들어 Azure API Management를 통해 요청 엔드포인트를 관리하는 경우 API Management 인스턴스 IP 주소의 요청만 수락하도록 논리 앱을 제한할 수 있습니다. 

#### <a name="set-ip-ranges---azure-portal"></a>IP 범위 설정 - Azure Portal

Azure Portal에서 이 제한을 설정하려면 다음과 같이 논리 앱의 설정으로 이동합니다. 

1. Azure Portal의 Logic Apps 디자이너에서 논리 앱을 엽니다. 

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정**을 선택합니다.

1. **액세서 제어 구성** > 
**허용된 인바운드 IP 주소**에서 **특정 IP 범위**를 선택합니다.

1. **트리거에 대한 IP 범위**에서 트리거가 허용하는 IP 주소 범위를 지정합니다. 올바른 IP 범위는 *x.x.x.x/x* 또는 *x.x.x.x-x.x.x.x* 형식을 사용합니다. 

논리 앱이 중첩된 논리 앱만 실행하도록 하려면 **허용된 인바운드 IP 주소** 목록에서 **다른 Logic Apps만**을 선택합니다. 이 옵션은 빈 배열을 논리 앱 리소스에 쓰므로 Logic Apps 서비스(부모 논리 앱)의 호출만 중첩된 논리 앱을 트리거할 수 있습니다.

> [!NOTE]
> IP 주소에 관계 없이 Azure REST API 또는 API Management를 통해 `/triggers/{triggerName}/run`을 사용하여 요청 기반 트리거가 있는 논리 앱을 계속 실행할 수 있습니다. 그렇지만 이 시나리오에는 여전히 Azure REST API에 대한 인증이 필요하며 모든 이벤트는 Azure Audit Log에 나타납니다. 그에 따라 액세스 제어 정책을 설정해야 합니다.

#### <a name="set-ip-ranges---logic-app-deployment-template"></a>IP 범위 설정 - 논리 앱 배포 템플릿

[Azure Resource Manager 배포 템플릿](../logic-apps/logic-apps-create-deploy-template.md)을 사용하여 논리 앱 배포를 자동화하는 경우 다음과 같이 해당 템플릿에서 IP 범위를 설정할 수 있습니다.

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "triggers": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="add-authentication"></a>

### <a name="add-azure-active-directory-oauth-or-other-security"></a>Azure Active Directory, OAuth 또는 기타 보안 추가

논리 앱에 다른 권한 부여 프로토콜을 추가하려면 [Azure API Management](https://azure.microsoft.com/services/api-management/)를 사용하는 것이 좋습니다. 이 서비스는 엔드포인트에 다양한 모니터링, 보안, 정책 및 모든 설명서를 제공하고 논리 앱을 API로 제공하는 기능을 제공합니다. API Management는 논리 앱에 대한 공용 또는 개인 엔드포인트를 노출할 수 있으며 이러한 엔드포인트는 Azure Active Directory, OAuth, 인증서 또는 기타 보안 표준을 사용할 수 있습니다. API Management는 요청을 받으면 논리 앱에 요청을 보내고 필요한 변환 또는 제한을 수행합니다. API Management에서만 논리 앱을 트리거하도록 하려면 논리 앱의 들어오는 IP 범위 설정을 사용할 수 있습니다. 

<a name="secure-operations"></a>

## <a name="secure-access-to-logic-app-operations"></a>논리 앱 작업에 대한 액세스 보안 유지

특정 사용자 또는 그룹만 논리 앱에서 작업을 실행할 수 있도록 하려면 관리, 편집 및 보기 등의 작업에 대한 액세스를 제한할 수 있습니다. Logic Apps는 [Azure RBAC(역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)를 지원합니다. 이 기능을 사용하여 기본 제공 역할을 사용자 지정하거나 다음과 같은 구독의 멤버에 할당할 수 있습니다.

* [논리 앱 참가자](../role-based-access-control/built-in-roles.md#logic-app-contributor): 사용자는 논리 앱을 보고, 편집하고, 업데이트할 수 있습니다. 

* [논리 앱 운영자](../role-based-access-control/built-in-roles.md#logic-app-operator): 사용자는 논리 앱 및 실행 기록을 보고, 논리 앱을 사용하도록 사용하지 않도록 설정할 수 있습니다. 이 역할은 논리 앱을 편집하거나 업데이트할 수 없습니다.

다른 사람이 논리 앱을 변경하거나 삭제하지 못하게 하려면 [Azure 리소스 잠금](../azure-resource-manager/resource-group-lock-resources.md)을 사용할 수 있습니다. 이 기능을 사용하여 다른 사람이 프로덕션 리소스를 변경하거나 삭제하지 못하도록 할 수 있습니다.

<a name="secure-run-history"></a>

## <a name="secure-access-to-logic-app-run-history"></a>논리 앱 실행 기록에 대한 액세스 보안 유지

이전 논리 앱 실행에서 입력 또는 출력으로 전달된 콘텐츠를 보호하려면 특정 IP 주소 범위에 대한 액세스를 제한할 수 있습니다. 이 기능은 보다 강력한 액세스 제어를 제공합니다. 전송 중이거나 미사용 상태인 논리 앱 실행의 모든 데이터는 암호화됩니다. 논리 앱의 실행 기록을 요청할 경우 Logic Apps는 해당 요청을 인증하고 논리 앱 워크플로의 요청 및 응답에서 발생하는 입력 및 출력에 대한 링크를 제공합니다. 특정 IP 주소의 요청만 해당 콘텐츠를 반환하도록 이러한 링크를 보호할 수 있습니다. 예를 들어 아무도 입력 및 출력에 액세스할 수 없도록 `0.0.0.0-0.0.0.0`과 같은 IP 주소를 지정할 수도 있습니다. 관리자 권한이 있는 사용자만 이러한 제한을 제거하고, 논리 앱 콘텐츠에 “Just-In-Time” 방식으로 액세스하도록 할 수 있습니다.

### <a name="set-ip-ranges---azure-portal"></a>IP 범위 설정 - Azure Portal

Azure Portal에서 이 제한을 설정하려면 다음과 같이 논리 앱의 설정으로 이동합니다.

1. Azure Portal의 Logic Apps 디자이너에서 논리 앱을 엽니다. 

1. 논리 앱의 메뉴에 있는 **설정** 아래에서 **워크플로 설정**을 선택합니다.

1. **액세스 제어 구성** > 
   **허용된 인바운드 IP 주소**에서 **특정 IP 범위**를 선택합니다.

1. **콘텐츠의 IP 범위**에서 입력 및 출력의 콘텐츠에 액세스할 수 있는 IP 주소 범위를 지정합니다. 
   올바른 IP 범위는 *x.x.x.x/x* 또는 *x.x.x.x-x.x.x.x* 형식을 사용합니다. 

### <a name="set-ip-ranges---logic-app-deployment-template"></a>IP 범위 설정 - 논리 앱 배포 템플릿

[Azure Resource Manager 배포 템플릿](../logic-apps/logic-apps-create-deploy-template.md)을 사용하여 논리 앱 배포를 자동화하는 경우 다음과 같이 해당 템플릿에서 IP 범위를 설정할 수 있습니다.

``` json
{
   "properties": {
      "definition": {},
      "parameters": {},
      "accessControl": {
         "contents": {
            "allowedCallerIpAddresses": [
               {
                  "addressRange": "192.168.12.0/23"
               },
               {
                  "addressRange": "2001:0db8::/64"
               }
            ]
         }
      }
   },
   "type": "Microsoft.Logic/workflows",
}
```

<a name="secure-action-parameters"></a>

## <a name="secure-action-parameters-and-inputs"></a>작업 매개 변수 및 입력 보안 유지

다양한 환경에서 배포를 수행할 때 논리 앱 워크플로 정의의 특정 요소를 매개 변수화할 수 있습니다. 이러한 방식을 통해 사용하는 환경을 기반으로 입력을 제공하고 중요한 정보를 보호할 수 있습니다. 예를 들어 [Azure Active Directory](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)를 사용하여 HTTP 작업을 인증하는 경우 인증에 사용되는 클라이언트 ID 및 클라이언트 비밀을 허용하는 매개 변수를 정의하고 보호합니다. 이러한 매개 변수의 경우 논리 앱 정의에 고유의 `parameters` 섹션이 있습니다.
런타임 동안 매개 변수 값에 액세스하려면 [워크플로 정의 언어](https://aka.ms/logicappsdocs)에 제공되는 `@parameters('parameterName')` 식을 사용하면 됩니다. 

논리 앱을 편집하거나 실행 기록을 볼 때 숨기고 싶은 매개 변수 및 값을 보호하려면 `securestring` 형식의 매개 변수를 정의하고 필요에 따라 인코딩을 사용하면 됩니다. 이 형식의 매개 변수는 리소스 정의와 함께 반환되지 않으며, 배포 후 리소스를 볼 때 액세스할 수 없습니다.

> [!NOTE]
> 요청의 헤더 또는 본문에서 매개 변수를 사용하면 논리 앱의 실행 기록과 나가는 HTTP 요청에 액세스하는 동안 해당 매개 변수를 볼 수 있습니다. 그에 따라 콘텐츠 액세스 정책도 설정해야 합니다.
> 권한 부여 헤더는 입력 또는 출력을 통해 볼 수 없습니다. 따라서 권한 부여 헤더에 비밀이 사용되면 해당 비밀을 검색할 수 없습니다.

논리 앱 정의의 매개 변수를 보호하는 방법에 대한 자세한 내용은 이 페이지 뒷부분의 [논리 앱 정의의 매개 변수 보호](#secure-parameters-workflow)를 참조하세요.

[Azure Resource Manager 배포 템플릿](../azure-resource-manager/resource-group-authoring-templates.md#parameters)을 사용하여 배포를 자동화하는 경우 보호되는 매개 변수를 템플릿에 사용할 수 있습니다. 예를 들어 논리 앱을 만들 때 매개 변수를 사용하여 KeyVault 비밀을 가져올 수 있습니다. 배포 템플릿 정의에는 논리 앱의 `parameters` 섹션과 별개인 고유의 `parameters` 섹션이 있습니다. 배포 템플릿의 매개 변수를 보호하는 방법에 대한 자세한 내용은 이 페이지 뒷부분의 [배포 템플릿의 매개 변수 보호](#secure-parameters-deployment-template)를 참조하세요.

<a name="secure-parameters-workflow"></a>

### <a name="secure-parameters-in-logic-app-definitions"></a>논리 앱 정의의 매개 변수 보호

논리 앱 워크플로 정의의 중요한 정보를 보호하려면 논리 앱을 저장한 후 정보가 보이지 않도록 보호되는 매개 변수를 사용합니다. 예를 들어 HTTP 작업 정의에 `Basic` 인증을 사용한다고 가정해 봅니다. 이 예제에는 작업 정의에 대한 매개 변수를 정의하는 `parameters` 섹션과 `username` 및 `password` 매개 변수 값을 수락하는 `authentication` 섹션이 포함되어 있습니다. 이러한 매개 변수의 값을 제공하려면 다음과 같은 별도의 매개 변수 파일을 사용하면 됩니다.

```json
"definition": {
   "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
   "actions": {
      "HTTP": {
         "type": "Http",
         "inputs": {
            "method": "GET",
            "uri": "https://www.microsoft.com",
            "authentication": {
               "type": "Basic",
               "username": "@parameters('usernameParam')",
               "password": "@parameters('passwordParam')"
            }
         },
         "runAfter": {}
      }
   },
   "parameters": {
      "passwordParam": {
         "type": "securestring"
      },
      "userNameParam": {
         "type": "securestring"
      }
   },
   "triggers": {
      "manual": {
         "type": "Request",
         "kind": "Http",
         "inputs": {
            "schema": {}
         }
      }
   },
   "contentVersion": "1.0.0.0",
   "outputs": {}
}
```

비밀을 사용하는 경우 배포 시 [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md)를 사용하여 비밀을 가져올 수 있습니다.

<a name="secure-parameters-deployment-template"></a>

### <a name="secure-parameters-in-azure-resource-manager-deployment-templates"></a>Azure Resource Manager 배포 템플릿의 매개 변수 보호

이 예제에서는 `securestring` 형식의 런타임 매개 변수를 여러 개 사용하는 Resource Manager 배포 템플릿을 보여줍니다.

* `armTemplatePasswordParam`: 논리 앱 정의의 `logicAppWfParam` 매개 변수에 대한 입력

* `logicAppWfParam`: 기본 인증을 사용하는 HTTP 작업에 대한 입력

이 예제에는 논리 앱의 워크플로 정의에 속하는 내부 `parameters` 섹션과 배포 템플릿에 속하는 외부 `parameters` 섹션이 포함되어 있습니다. 이러한 매개 변수의 환경 변수를 지정하려면 별도의 매개 변수 파일을 사용하면 됩니다. 

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "logicAppName": {
         "type": "string",
         "minLength": 1,
         "maxLength": 80,
         "metadata": {
            "description": "Name of the Logic App."
         }
      },
      "armTemplatePasswordParam": {
         "type": "securestring"
      },
      "logicAppLocation": {
         "type": "string",
         "defaultValue": "[resourceGroup().location]",
         "allowedValues": [
            "[resourceGroup().location]",
            "eastasia",
            "southeastasia",
            "centralus",
            "eastus",
            "eastus2",
            "westus",
            "northcentralus",
            "southcentralus",
            "northeurope",
            "westeurope",
            "japanwest",
            "japaneast",
            "brazilsouth",
            "australiaeast",
            "australiasoutheast",
            "southindia",
            "centralindia",
            "westindia",
            "canadacentral",
            "canadaeast",
            "uksouth",
            "ukwest",
            "westcentralus",
            "westus2"
         ],
         "metadata": {
            "description": "Location of the Logic App."
         }
      }
   },
   "variables": {},
   "resources": [
      {
         "name": "[parameters('logicAppName')]",
         "type": "Microsoft.Logic/workflows",
         "location": "[parameters('logicAppLocation')]",
         "tags": {
            "displayName": "LogicApp"
         },
         "apiVersion": "2016-06-01",
         "properties": {
            "definition": {
               "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-0601/workflowdefinition.json#",
               "actions": {
                  "HTTP": {
                     "type": "Http",
                     "inputs": {
                        "method": "GET",
                        "uri": "https://www.microsoft.com",
                        "authentication": {
                           "type": "Basic",
                           "username": "@parameters('usernameParam')",
                           "password": "@parameters('logicAppWfParam')"
                        }
                     },
                  "runAfter": {}
                  }
               },
               "parameters": {
                  "logicAppWfParam": {
                     "type": "securestring"
                  },
                  "userNameParam": {
                     "type": "securestring"
                  }
               },
               "triggers": {
                  "manual": {
                     "type": "Request",
                     "kind": "Http",
                     "inputs": {
                        "schema": {}
                     }
                  }
               },
               "contentVersion": "1.0.0.0",
               "outputs": {}
            },
            "parameters": {
               "logicAppWfParam": {
                  "value": "[parameters('armTemplatePasswordParam')]"
               }
            }
         }
      }
   ],
   "outputs": {}
}
```

비밀을 사용하는 경우 배포 시 [Azure Resource Manager KeyVault](../azure-resource-manager/resource-manager-keyvault-parameter.md)를 사용하여 비밀을 가져올 수 있습니다.

<a name="secure-requests"></a>

## <a name="secure-access-to-services-receiving-requests"></a>요청을 수신하는 서비스에 대한 액세스 보안 유지

논리 앱이 액세스 권한을 요구하고 요청을 전송하는 엔드포인트의 보안을 유지할 수 있는 몇 가지 방법이 있습니다.

### <a name="add-authentication-on-outbound-requests"></a>아웃바운드 요청에 대한 인증 추가

HTTP, HTTP + Swagger(개방형 API) 또는 웹후크 동작으로 작업할 경우 논리 앱이 보낸 요청에 인증을 추가할 수 있습니다. 예를 들어 기본 인증, 인증서 인증 또는 Azure Active Directory 인증을 사용할 수 있습니다. 자세한 내용은 [트리거 또는 작업 인증](../logic-apps/logic-apps-workflow-actions-triggers.md#connector-authentication)을 참조하세요.

### <a name="restrict-access-to-logic-app-ip-addresses"></a>논리 앱 IP 주소에 대한 액세스 제한

논리 앱의 모든 호출은 지역을 기준으로 하는 지정된 특정 IP 주소에서 시작됩니다. 이러한 IP 주소의 요청만 수락하는 필터링을 추가할 수 있습니다. 이러한 IP 주소에 대해서는 [Azure Logic Apps에 대한 제한 및 구성 정보](logic-apps-limits-and-config.md#configuration)을 참조하세요.

### <a name="secure-on-premises-connectivity"></a>온-프레미스 연결 보안 유지

Azure Logic Apps는 안전하고 신뢰할 수 있는 온-프레미스 통신을 위해 이러한 서비스와의 통합을 제공합니다.

#### <a name="on-premises-data-gateway"></a>온-프레미스 데이터 게이트웨이

Azure Logic Apps의 많은 관리형 커넥터는 파일 시스템, SQL, SharePoint, DB2 등의 온-프레미스 시스템으로의 보안 연결을 제공합니다. 게이트웨이는 Azure Service Bus를 통해 암호화된 채널에서 온-프레미스 원본의 데이터를 보냅니다. 보안으로 시작하는 모든 트래픽은 게이트웨이 에이전트에서 트래픽을 아웃바운드합니다. [온-프레미스 데이터 게이트웨이 작동 방식](logic-apps-gateway-install.md#gateway-cloud-service)을 알아보세요.

#### <a name="azure-api-management"></a>Azure API Management

[Azure API Management](https://azure.microsoft.com/services/api-management/)는 보안 프록시를 위한 사이트 간 가상 네트워크 및 ExpressRoute 통합과 온-프레미스 시스템에 대한 통신 등의 온-프레미스 연결 옵션을 제공합니다. Logic App Designer의 논리 앱 워크플로에서 API Management가 제공하는 API를 선택하여 온-프레미스 시스템에 빠르게 액세스할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [배포 템플릿 만들기](logic-apps-create-deploy-template.md)  
* [예외 처리](logic-apps-exception-handling.md)  
* [논리 앱 모니터링](logic-apps-monitor-your-logic-apps.md)  
* [논리 앱 오류 및 문제 진단](logic-apps-diagnosing-failures.md)  
