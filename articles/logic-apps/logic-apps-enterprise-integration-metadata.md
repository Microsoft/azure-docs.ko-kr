---
title: 통합 계정 아티팩트 메타데이터 관리 - Azure Logic Apps | Microsoft Docs
description: 엔터프라이즈 통합 팩을 사용하여 Azure Logic Apps의 통합 계정에서 아티팩트 메타데이터 추가 또는 가져오기
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.date: 01/17/2019
ms.openlocfilehash: 5ebdf45bec4e7cfceb75354af40c7a21c22c6eef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60846204"
---
# <a name="manage-artifact-metadata-in-integration-accounts-with-azure-logic-apps-and-enterprise-integration-pack"></a>Azure Logic Apps 및 엔터프라이즈 통합 팩을 사용하여 통합 계정에서 아티팩트 메타데이터 관리

통합 계정에서 아티팩트에 대한 사용자 지정 메타데이터를 정의하고 사용할 논리 앱에 대한 런타임 동안 해당 메타데이터를 가져올 수 있습니다. 예를 들어 파트너, 규약, 스키마 및 맵 등의 아티팩트에 대해 메타데이터를 제공할 수 있습니다. 모두 키-값 쌍을 사용해서 메타데이터를 저장합니다. 

## <a name="prerequisites"></a>필수 조건

* Azure 구독. 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">Azure 체험 계정에 등록</a>합니다.

* 메타데이터를 추가하려는 아티팩트를 포함하는 기본 [통합 계정](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md), 예를 들어 다음과 같습니다. 

  * [파트너](logic-apps-enterprise-integration-partners.md)
  * [규약](logic-apps-enterprise-integration-agreements.md)
  * [스키마](logic-apps-enterprise-integration-schemas.md)
  * [Map](logic-apps-enterprise-integration-maps.md)

* 통합 계정에 연결된 논리 앱 및 사용하려는 아티팩트 메타데이터 논리 앱이 아직 연결되지 않은 경우 [논리 앱을 통합 계정에 연결하는 방법](logic-apps-enterprise-integration-create-integration-account.md#link-account)을 알아봅니다. 

  논리 앱이 아직 없는 경우 [논리 앱을 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)을 알아봅니다. 
  아티팩트 메타데이터 관리에 사용하려는 트리거 및 작업을 추가합니다. 또는 시도해 보려면 **요청** 또는 **HTTP**와 같은 트리거를 논리 앱에 추가합니다.

## <a name="add-metadata-to-artifacts"></a>아티팩트에 메타데이터 추가

1. Azure 계정 자격 증명을 사용하여 <a href="https://portal.azure.com" target="_blank">Azure Portal</a>에 로그인합니다. 통합 계정을 찾고 엽니다.

1. 메타데이터를 추가하려는 아티팩트를 선택하고, **편집**을 선택합니다. 해당 아티팩트에 대한 메타데이터 정보를 입력합니다. 예를 들면 다음과 같습니다.

   ![메타데이터 입력](media/logic-apps-enterprise-integration-metadata/add-partner-metadata.png)

1. 작업을 완료하면 **확인**을 선택합니다.

1. 통합 계정에 대한 JSON(JavaScript Object Notation) 정의에서 이 메타데이터를 보려면 JSON 편집기가 열리도록 **JSON으로 편집**을 선택합니다. 

   ![파트너 메타데이터에 대한 JSON](media/logic-apps-enterprise-integration-metadata/partner-metadata.png)

## <a name="get-artifact-metadata"></a>아티팩트 메타데이터 가져오기

1. Azure Portal에서 원하는 통합 계정에 연결된 논리 앱을 엽니다. 

1. 논리 앱 디자이너에서 트리거 또는 워크플로의 마지막 작업에서 메타데이터를 가져오는 단계를 추가하는 경우 **새 단계** > **작업 추가**를 선택합니다. 

1. 검색 상자에 "통합 계정"을 입력합니다. 검색 상자에서 **모두**를 선택합니다. 작업 목록에서 다음 작업을 선택합니다. **통합 계정 아티팩트 조회 - 통합 계정**

   !["통합 계정 아티팩트 조회" 선택](media/logic-apps-enterprise-integration-metadata/integration-account-artifact-lookup.png)

1. 찾으려는 아티팩트에 대해 다음 정보를 제공합니다.

   | 자산 | 필수 | Value | 설명 | 
   |----------|---------|-------|-------------| 
   | **아티팩트 형식** | 예 | **스키마**, **맵**, **파트너**, **규약** 또는 사용자 지정 형식 | 원하는 아티팩트에 대한 형식 | 
   | **아티팩트 이름** | 예 | <*artifact-name*> | 원하는 아티팩트에 대한 이름 | 
   ||| 

   예를 들어 거래 파트너 아티팩트에 대한 메타데이터를 가져오려고 한다고 가정합니다.

   ![아티팩트 유형 선택 및 아티팩트 이름 입력](media/logic-apps-enterprise-integration-metadata/artifact-lookup-information.png)

1. 해당 메타데이터를 처리하기 위해 원하는 작업을 추가합니다. 예를 들면 다음과 같습니다.

   1. **통합 계정 아티팩트 조회** 작업에서 **다음 단계**를 선택하고, **작업 추가**를 선택합니다. 

   1. 검색 상자에 "http"를 입력합니다. 검색 상자에서 **기본 제공 항목**을 선택하고, 이 작업을 선택합니다. **HTTP - HTTP**

      ![HTTP 작업 추가](media/logic-apps-enterprise-integration-metadata/http-action.png)

   1. 관리하려는 아티팩트 메타데이터에 대한 정보를 제공합니다. 

      예를 들어 이 항목의 앞부분에 추가된 `routingUrl` 메타데이터를 가져오려고 한다고 가정합니다. 지정할 수 있는 속성 값은 다음과 같습니다. 

      | 자산 | 필수 | Value | 설명 | 
      |----------|----------|-------|-------------| 
      | **메서드** | 예 | <*operation-to-run*> | 아티팩트에서 실행할 HTTP 작업입니다. 예를 들어 이 HTTP 작업은 **GET** 메서드를 사용합니다. | 
      | **URI** | 예 | <*metadata-location*> | 검색한 아티팩트에서 `routingUrl` 메타데이터 값에 액세스하기 위해 식을 사용할 수 있습니다. 예를 들면 다음과 같습니다. <p>`@{outputs('Integration_Account_Artifact_Lookup')['properties']['metadata']['routingUrl']}` | 
      | **헤더** | 아닙니다. | <*header-values*> | HTTP 작업에 전달하려는 트리거의 모든 헤더 출력입니다. 예를 들어 트리거의 `headers` 속성 값을 전달하기 위해 식을 사용할 수 있습니다. 예를 들면 다음과 같습니다. <p>`@triggeroutputs()['headers']` | 
      | **본문** | 아닙니다. | <*body-content*> | HTTP 작업의 `body` 속성을 통해 전달하려는 다른 콘텐츠입니다. 이 예제에서는 아티팩트의 `properties` 값을 HTTP 작업으로 전달합니다. <p>1. 동적 콘텐츠 목록이 표시되도록 **본문** 속성 내부를 클릭합니다. 속성이 표시되지 않는 경우 **자세히 보기**를 선택합니다. <br>2. 동적 콘텐츠 목록의 **통합 계정 아티팩트 조회**에서 **속성**을 선택합니다. | 
      |||| 

      예를 들면 다음과 같습니다.

      ![HTTP 작업에 대한 값 및 식 지정](media/logic-apps-enterprise-integration-metadata/add-http-action-values.png)

   1. HTTP 작업에 대해 제공한 정보를 확인하려면 논리 앱의 JSON 정의를 봅니다. 논리 앱 디자이너 도구 모음에서 앱의 JSON 정의가 표시되도록 **코드 보기**를 선택합니다. 예를 들면 다음과 같습니다.

      ![논리 앱 JSON 정의](media/logic-apps-enterprise-integration-metadata/finished-logic-app-definition.png)

      논리 앱 디자이너로 다시 전환하면 이제 사용한 모든 식이 확인된 것으로 표시됩니다. 예를 들면 다음과 같습니다.

      ![논리 앱 디자이너에서 확인된 식](media/logic-apps-enterprise-integration-metadata/resolved-expressions.png)

## <a name="next-steps"></a>다음 단계

* [규약에 대한 자세한 정보](logic-apps-enterprise-integration-agreements.md)
