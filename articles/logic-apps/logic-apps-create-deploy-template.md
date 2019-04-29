---
title: Azure Logic Apps용 배포 템플릿 만들기 | Microsoft Docs
description: 논리 앱 배포를 위한 Azure Resource Manager 템플릿 만들기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 85928ec6-d7cb-488e-926e-2e5db89508ee
ms.date: 10/18/2016
ms.openlocfilehash: 624539557b0bf57e9d919a3a46337f1cf93a4f07
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128603"
---
# <a name="create-azure-resource-manager-templates-for-deploying-logic-apps"></a>논리 앱 배포를 위한 Azure Resource Manager 템플릿 만들기

논리 앱을 만들 때 논리 앱 정의를 확장할 수 있습니다는 [Azure Resource Manager 템플릿을](../azure-resource-manager/resource-group-overview.md)합니다. 이 템플릿은 리소스를 정의 하 여 배포를 자동화 하는 데 사용할 수 있습니다 하 고 배포 및 통해 매개 변수 값을 제공 하는 데 사용 하려는 매개 변수를 [매개 변수 파일](../azure-resource-manager/resource-group-template-deploy.md#parameter-files)합니다.
이런 방식으로 논리 앱을 더 쉽게 배포할 수 있으며 어떤 환경이 나 Azure 리소스 그룹에 있습니다. 

Azure Logic Apps는 제공을 [미리 만들어진된 논리 앱 Azure Resource Manager 템플릿을](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json) 는 다시 사용할 수 있습니다, logic apps 만들기에 대 한 뿐만 아니라 뿐만 아니라 리소스 및 배포에 사용할 매개 변수를 정의 합니다. 이 템플릿을 자체 비즈니스 시나리오에 사용하거나 요구 사항에 맞게 사용자 지정할 수 있습니다. 에 대해 자세히 알아보세요 [Resource Manager 템플릿 구조 및 구문](../azure-resource-manager/resource-group-authoring-templates.md)합니다. JSON 구문 및 속성의 경우 [Microsoft.Logic 리소스 종류](/azure/templates/microsoft.logic/allversions)를 참조하세요.

Azure Resource Manager 템플릿에 대 한 자세한 내용은 다음이 문서를 참조 합니다.

* [Azure 리소스 관리자 템플릿 작성](../azure-resource-manager/resource-group-authoring-templates.md)
* [클라우드 일관성에 대 한 Azure Resource Manager 템플릿 개발](../azure-resource-manager/templates-cloud-consistency.md)

## <a name="logic-app-structure"></a>논리 앱 구조

"코드 보기"와 같은 도구를 사용 하 여 논리 앱 정의 "디자이너 보기"에서 전환 하 여 볼 수 있는 이러한 기본 섹션이 [Azure Resource Explorer](http://resources.azure.com)합니다. 논리 앱 정의 개체 JSON (Javascript Notation)을 사용 하므로 JSON 구문 및 속성에 대 한 자세한 내용은 참조 하십시오 [Microsoft.Logic 리소스 종류](/azure/templates/microsoft.logic/allversions)합니다.

* **논리 앱 리소스**: 가격 책정 계획 및 워크플로 정의 논리 앱의 위치 또는 지역 같은 정보를 설명 합니다.

* **워크플로 정의**: 논리 앱 트리거 및 작업 및 Azure Logic Apps 서비스에서 워크플로 실행 하는 방법을 설명 합니다. 코드 보기에서의 워크플로 정의 찾을 수 있습니다는 `definition` 섹션입니다.

* **연결**: 논리 앱에서 관리 되는 커넥터를 사용 하는 경우는 `$connections` 섹션 논리 앱 및 다른 시스템이 나 연결 문자열 및 액세스 토큰 등의 서비스 간에 이러한 연결에 대 한 메타 데이터를 안전 하 게 저장 하는 다른 리소스를 참조 합니다. 논리 앱 정의 내에서 이러한 연결에 대 한 참조, 논리 앱 정의 내에서 표시 `parameters` 섹션입니다.

Azure 리소스 그룹 배포를 사용 하 여 사용할 수 있는 논리 앱 템플릿을 만들려면 리소스를 정의 하 고 필요에 따라 매개 변수화 해야 합니다. 예를 들어, 개발, 테스트 및 프로덕션 환경에 배포하는 경우 각 환경에서 SQL Database에 대해 다른 연결 문자열을 사용할 수 있습니다.
또는, 다른 구독이나 리소스 그룹 내에 배포할 수 있습니다.

## <a name="create-logic-app-deployment-templates"></a>논리 앱 배포 템플릿 만들기

유효한 논리 앱 배포 템플릿 만들기에 사용 되는 가장 쉬운 방법은에 대 한 Visual Studio 확장에 대 한 Visual Studio 및 Azure Logic Apps 도구를 사용 합니다. Azure portal에서 Visual Studio로 논리 앱을 다운로드 하 여 다른 Azure 구독 및 위치를 사용 하 여 사용할 수 있는 올바른 배포 템플릿을 가져올 수 있습니다. 또한 논리 앱을 자동으로 다운로드 템플릿에 포함 된 논리 앱 정의 매개 변수화 합니다.
Visual Studio에서 논리 앱 만들기 및 관리 하는 방법에 대 한 자세한 내용은 참조 하세요. [Visual Studio를 사용 하 여 논리 앱을 만들려면](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md) 하 고 [Visual Studio를 사용 하 여 logic apps 관리](../logic-apps/manage-logic-apps-with-visual-studio.md)합니다.

Visual Studio 또는 수동으로 만들고 템플릿에 필요한 매개 변수를이 항목의 지침에 따라 이외의 사용할 수도 있습니다는 [논리 앱 템플릿 만들기에 대 한 PowerShell 모듈](https://github.com/jeffhollan/LogicAppTemplateCreator)합니다. 이 오픈 소스 모듈에는 먼저 논리 앱과 논리 앱을 사용 하는 모든 연결 평가 합니다. 그러면 모듈 배포에 필요한 매개 변수와 함께 템플릿 리소스를 생성합니다. 예를 들어, Azure Service Bus 큐에서 메시지를 받고 Azure SQL database에 데이터를 추가 하는 논리 앱이 있다고 가정 합니다. 모든 오케스트레이션 논리를 유지 하 고 배포 시 이러한 값을 설정할 수 있도록 SQL 및 Service Bus 연결 문자열을 매개 변수화 하는 모듈 도구.

> [!IMPORTANT]
> 연결 논리 앱과 같은 Azure 리소스 그룹에 있어야 합니다.
> 모든 Azure 테 넌 트 및 구독 액세스 토큰을 사용 하 여 모듈을 사용 하는 PowerShell 모듈에 대 한 합니다 [Azure Resource Manager 클라이언트 도구](https://github.com/projectkudu/ARMClient)합니다. 자세한 내용은 참조 [Azure Resource Manager 클라이언트 도구에 대 한 문서](https://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) ARMClient에에서 자세히 설명 합니다.

### <a name="install-powershell-module-for-logic-app-templates"></a>논리 앱 템플릿 용 PowerShell 모듈 설치

모듈을 설치 하는 가장 쉬운 방법은 대 한 합니다 [PowerShell 갤러리](https://www.powershellgallery.com/packages/LogicAppTemplate/0.1),이 명령을 사용 하 여:

`Install-Module -Name LogicAppTemplate`

PowerShell 모듈을 수동으로 설치할 수 있습니다.

1. 최신 다운로드 [논리 앱 템플릿 작성기](https://github.com/jeffhollan/LogicAppTemplateCreator/releases)합니다.

1. 일반적으로 PowerShell 모듈 폴더에 폴더를 추출 `%UserProfile%\Documents\WindowsPowerShell\Modules`합니다.

### <a name="generate-logic-app-template---powershell"></a>논리 앱 템플릿 PowerShell 생성

PowerShell을 설치한 후 다음 명령을 사용하여 템플릿을 생성합니다.

`armclient token $SubscriptionId | Get-LogicAppTemplate -LogicApp MyApp -ResourceGroup MyRG -SubscriptionId $SubscriptionId -Verbose | Out-File C:\template.json`

`$SubscriptionId` 은(는) Azure 구독 ID입니다. 이 줄은 ARMClient를 통해 액세스 토큰을 가져온 다음 PowerShell 스크립트로 실행시키고, JSON 파일로 템플릿을 만듭니다.

## <a name="parameters-in-logic-app-templates"></a>논리 앱 템플릿에 매개 변수

논리 앱 템플릿을 만든 후 추가 수 있으며 모든 필요한 매개 변수를 편집할 수 있습니다. 서식 파일에 둘 이상의 `parameters` 섹션 예를 들어: 

* 논리 앱 워크플로 정의는 자체 [ `parameters` 섹션](../logic-apps/logic-apps-workflow-definition-language.md#parameters) 논리 앱 배포 시 입력을 수락 하기 위해 사용 하는 모든 매개 변수를 정의할 수 있습니다.

* Resource Manager 템플릿에서는 자체 [ `parameters` 구역](../azure-resource-manager/resource-group-authoring-templates.md#parameters)논리 앱에서 별도 `parameters` 섹션입니다. 예를 들면 다음과 같습니다.

  [!INCLUDE [logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

예를 들어, Azure 함수 또는 중첩 된 논리 앱 워크플로 나타내는 리소스 ID를 참조 하는 논리 앱 정의 및 해당 리소스 ID를 단일 배포로 논리 앱과 함께 배포. 추가할 수 있습니다 템플릿에서 리소스 ID는 해당 ID를 매개 변수화 이와 동일한 방법을 사용 하 여 사용자 지정 Api 또는 OpenAPI 끝점에 대 한 참조 (이전의 "Swagger") 각 Azure 리소스 그룹을 사용 하 여 배포 하려는 합니다.

배포 템플릿에서 매개 변수를 사용 하는 경우 Logic Apps 디자이너 이러한 매개 변수를 올바르게 표시할 수 있도록이 지침을 따르세요.

* 이러한 트리거 및 작업에만 매개 변수를 사용 합니다.

  * Azure Functions 앱
  * 중첩 또는 자식 논리 앱 워크플로
  * API Management 호출
  * API 연결 런타임 URL
  * API 연결 경로

* 매개 변수를 정의할 때 사용 하 여 기본값을 제공 하 고 있는지 확인 합니다 `defaultValue` 예를 들어 속성 값:

  ```json
  "parameters": {
     "IntegrationAccount": {
        "type":"string",
        "minLength": 1,
        "defaultValue": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource=group-name>/providers/Microsoft.Logic/integrationAccounts/<integration-account-name>"
     }
  },
  ```

* 을 보호 하거나 템플릿에서 중요 한 정보를 숨기려면 매개 변수를 보호 합니다. 에 대해 자세히 알아보세요 [보안된 매개 변수를 사용 하는 방법을](../logic-apps/logic-apps-securing-a-logic-app.md#secure-parameters-workflow)합니다.

Azure Service Bus "메시지 보내기" 작업을 매개 변수화 하는 방법을 보여 주는 예제는 다음과 같습니다.

```json
"Send_message": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['servicebus']['connectionId']"
         },
         // If the `host.runtimeUrl` property appears in your template, 
         // you can remove this property, which is optional, for example:
         "runtimeUrl": {}
      },
      "method": "POST",
      "path": "[concat('/@{encodeURIComponent(''', parameters('<Azure-Service-Bus-queue-name>'), ''')}/messages')]",
      "body": {
         "ContentData": "@{base64(triggerBody())}"
      },
      "queries": {
         "systemProperties": "None"
      }
   },
   "runAfter": {}
},
```

### <a name="reference-dependent-resources"></a>종속 리소스 참조

논리 앱에서 종속 리소스에 대 한 참조에 필요한 경우 사용할 수 있습니다 [Azure Resource Manager 템플릿 함수](../azure-resource-manager/resource-group-template-functions.md) 논리 앱의 배포 템플릿에서 합니다. 예를 들어, Azure 함수 또는 파트너, 규약 및 논리 앱과 함께 배포 하려는 다른 아티팩트에 대 한 정의 사용 하 여 통합 계정 참조 하도록 논리 앱에 만든다고 가정 합니다.
와 같은 Resource Manager 템플릿 함수를 사용할 수 있습니다 `parameters`, `variables`를 `resourceId`, `concat`등입니다.

이러한 매개 변수를 정의 하 여 Azure 함수에 대 한 리소스 ID를 바꿀 수는 방법을 보여 주는 예제는 다음과 같습니다.

``` json
"parameters": {
   "<Azure-function-name>": {
      "type": "string",
      "minLength": 1,
      "defaultValue": "<Azure-function-name>"
   }
},
```

Azure 함수를 참조할 때 이러한 매개 변수를 사용 하는 방법을 다음과 같습니다.

```json
"MyFunction": {
   "type": "Function",
   "inputs": {
      "body": {},
      "function": {
         "id":"[resourceid('Microsoft.Web/sites/functions','<Azure-Functions-app-name>', parameters('<Azure-function-name>'))]"
      }
   },
   "runAfter": {}
},
```

## <a name="add-logic-app-to-resource-group-project"></a>논리 앱 리소스 그룹 프로젝트에 추가

기존 Azure 리소스 그룹 프로젝트에 있는 경우 JSON 개요 창을 사용 하 여 해당 프로젝트에 논리 앱을 추가할 수 있습니다. 이전에 만든 앱과 함께 다른 논리 앱을 추가할 수도 있습니다.

1. 솔루션 탐색기에서 `<template>.json` 파일을 엽니다.

2. **뷰** 메뉴에서 **기타 Windows** > **JSON 개요**합니다.

3. 템플릿 파일에 리소스를 추가 하려면 **리소스 추가** JSON 개요 창의 맨 위에 있는 합니다. 또는 JSON 개요 창에서 **리소스**를 마우스 오른쪽 단추로 클릭하고 **새 리소스 추가**를 선택합니다.

   ![JSON 개요 창](./media/logic-apps-create-deploy-template/jsonoutline.png)

4. **리소스 추가** 대화 상자에서 **Logic App**을 찾고 선택합니다. 논리 앱의 이름을 지정하고 **추가**를 선택합니다.

   ![리소스 추가](./media/logic-apps-create-deploy-template/addresource.png)

## <a name="get-support"></a>지원 받기

질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [논리 앱 템플릿 배포](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md)
