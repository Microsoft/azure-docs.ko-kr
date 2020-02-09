---
title: 보안 작업 & Microsoft Graph 보안 통합 및 관리
description: Microsoft Graph Security &를 사용 하 여 앱의 위협 방지, 검색 및 응답을 개선 Azure Logic Apps
services: logic-apps
ms.suite: integration
author: preetikr
ms.author: preetikr
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 12/12/2019
tags: connectors
ms.openlocfilehash: f9aa88934d67d98fce43763c6c8fac7c384d765d
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76313793"
---
# <a name="improve-threat-protection-by-integrating-security-operations-with-microsoft-graph-security--azure-logic-apps"></a>Microsoft Graph Security 및 Azure Logic Apps와 보안 작업을 통합하여 위협 방지 향상

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Microsoft Graph Security](https://docs.microsoft.com/graph/security-concept-overview) 커넥터를 사용하면 Microsoft 보안 제품, 서비스 및 파트너를 통합하는 자동화된 워크플로를 만들어 앱이 위협을 탐지 및 방지하고 이에 대응하는 방식을 향상할 수 있습니다. 예를 들어 Microsoft Graph Security 엔터티(예: 경고)를 모니터링하고 관리하는 [Azure Security Center 플레이북](../security-center/security-center-playbooks.md)을 만들 수 있습니다. Microsoft Graph 보안 커넥터에서 지 원하는 몇 가지 시나리오는 다음과 같습니다.

* 쿼리 또는 경고 ID를 기준으로 경고를 가져옵니다. 예를 들어 높은 심각도 경고를 포함하는 목록을 가져올 수 있습니다.

* 경고를 업데이트합니다. 예를 들어 경고 할당을 업데이트하거나, 경고에 주석을 추가하거나, 경고에 태그를 지정할 수 있습니다.

* [경고 구독(웹후크)](https://docs.microsoft.com/graph/api/resources/webhooks)을 만들어 경고가 생성되거나 변경된 시기를 모니터링합니다.

* 경고 구독을 관리합니다. 예를 들어 활성 구독을 가져오거나, 구독의 만료 시간을 확장하거나, 구독을 삭제할 수 있습니다.

논리 앱의 워크플로는 Microsoft Graph Security 커넥터에서 응답을 가져오고 해당 결과물을 워크플로의 다른 작업에서 사용하도록 제공할 수 있습니다. 워크플로의 다른 작업에서 Microsoft Graph Security 커넥터 작업의 결과물을 사용하게 할 수도 있습니다. 예를 들어 Microsoft Graph Security 커넥터를 통해 높은 심각도 경고를 가져오는 경우 Outlook 커넥터를 사용하여 해당 경고를 메일 메시지로 보낼 수 있습니다. 

Microsoft Graph Security에 대한 자세한 내용은 [Microsoft Graph 보안 API 개요](https://aka.ms/graphsecuritydocs)를 참조하세요. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다. Microsoft Flow 또는 PowerApps를 찾고 있는 경우 [흐름 이란?](https://flow.microsoft.com/) 또는 [powerapps 란?](https://powerapps.microsoft.com/) 을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* Microsoft Graph Security 커넥터를 사용하려면 [Microsoft Graph Security 인증 요구 사항](https://aka.ms/graphsecurityauth)의 일부인 ‘명시적으로 제공된’ Azure AD(Active Directory) 테넌트 관리자 동의가 있어야 합니다. 이 동의에는 [Azure Portal](https://portal.azure.com)에서 확인할 수 있는 Microsoft Graph Security 커넥터의 애플리케이션 ID 및 이름이 필요합니다.

  | 속성 | 값 |
  |----------|-------|
  | **애플리케이션 이름** | `MicrosoftGraphSecurityConnector` |
  | **애플리케이션 ID** | `c4829704-0edc-4c3d-a347-7c4a67586f3c` |
  |||

  커넥터에 대한 동의를 부여 하려면 Azure AD 테넌트 관리자가 다음 단계를 따를 수 있습니다.

  * [Azure AD 애플리케이션에 대한 테넌트 관리자 동의를 부여](../active-directory/develop/v2-permissions-and-consent.md)합니다.

  * 논리 앱을 처음 실행하는 동안 앱이 [애플리케이션 동의 환경](../active-directory/develop/application-consent-experience.md)을 통해 Azure AD 테넌트 관리자의 동의를 요청할 수 있습니다.
   
* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Microsoft Graph Security 엔터티(예: 경고)에 액세스하려는 논리 앱. Microsoft Graph 보안 트리거를 사용 하려면 빈 논리 앱이 필요 합니다. Microsoft Graph 보안 작업을 사용 하려면 시나리오에 적절 한 트리거로 시작 하는 논리 앱이 필요 합니다.

## <a name="connect-to-microsoft-graph-security"></a>Microsoft Graph Security에 연결 

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 빈 논리 앱의 경우 Microsoft Graph 보안 작업을 추가 하기 전에 트리거 및 원하는 다른 작업을 추가 합니다.

   또는

   기존 논리 앱의 경우 Microsoft Graph 보안 작업을 추가 하려는 마지막 단계에서 **새 단계**를 선택 합니다.

   또는

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 표시 되는 더하기 기호 (+)를 선택 하 고 **작업 추가**를 선택 합니다.

1. 검색 상자에 “microsoft graph security”를 필터로 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

1. Microsoft Graph Security 자격 증명으로 로그인합니다.

1. 선택한 작업에 대해 필요한 세부 정보를 입력하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="add-triggers"></a>트리거 추가

Azure Logic Apps에서 모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 [트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts)를 통해 시작되어야 합니다. 트리거가 발생할 때마다 Logic Apps 엔진은 논리 앱 인스턴스를 만들고 앱의 워크플로를 실행 하기 시작 합니다.

> [!NOTE] 
> 트리거가 발생 하면 트리거는 모든 새 경고를 처리 합니다. 경고가 수신 되지 않으면 트리거 실행을 건너뜁니다. 다음 트리거 폴링은 트리거의 속성에 지정한 되풀이 간격을 기준으로 발생합니다.

이 예제에서는 새 경고가 앱에 전송 될 때 논리 앱 워크플로를 시작 하는 방법을 보여 줍니다.

1.  Azure Portal 또는 Visual Studio에서 논리 앱 디자이너를 여는 빈 논리 앱을 만듭니다. 이 예에서는 Azure Portal을 사용합니다.

1.  디자이너의 검색 상자에 "microsoft graph security"를 필터로 입력 합니다. 트리거 목록에서 다음 트리거: **모든 새 경고** 를 선택 합니다.

1.  트리거에서 모니터링 하려는 경고에 대한 정보를 제공 합니다. 추가 속성을 보려면 **새 매개 변수 추가** 목록을 열고 매개 변수를 선택 하 여 해당 속성을 트리거에 추가 합니다.

   | 속성 | Property(JSON) | 필수 | 유형 | Description |
   |----------|-----------------|----------|------|-------------|
   | **간격** | `interval` | 예 | 정수 | 빈도에 따라 워크플로가 얼마나 자주 실행되는지를 설명하는 양의 정수입니다. 다음은 최소 및 최대 간격입니다. <p><p>- 월: 1-16개월 <br>- 일: 1-500일 <br>- 시간: 1-12,000시간 <br>- 분: 1-72,000분 <br>- 초: 1-9,999,999초 <p>예를 들어 간격이 6이고 빈도가 “월”이면 되풀이 간격은 6개월마다입니다. |
   | **빈도** | `frequency` | 예 | String | 되풀이 시간 단위: **초**, **분**, **시간**, **일**, **주** 또는 **월** |
   | **표준 시간대** | `timeZone` | 아닙니다. | String | 이 트리거는 [UTC 오프셋](https://en.wikipedia.org/wiki/UTC_offset)을 허용하지 않으므로 시작 시간을 지정할 때만 적용됩니다. 적용하려는 표준 시간대를 선택합니다. |
   | **시작 시간** | `startTime` | 아닙니다. | String | 시작 날짜와 시간을 다음 형식으로 지정 합니다. <p><p>표준 시간대를 선택하는 경우 YYYY-MM-DDThh:mm:ss <p>또는 <p>표준 시간대를 선택하지 않은 경우 YYYY-MM-DDThh:mm:ssZ <p>예를 들어 오후 2:00에 2017 년 9 월 18 일을 원하는 경우 "2017-09-18T14:00:00"을 지정 하 고 태평양 표준시와 같은 표준 시간대를 선택 합니다. 또는 표준 시간대 없이 “2017-09-18T14:00:00Z”를 지정합니다. <p>**참고:** 이 시작 시간은 미래의 최대 49 년 이며 utc [오프셋](https://en.wikipedia.org/wiki/UTC_offset)없이 [utc 날짜/시간 형식의](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) [ISO 8601 날짜 시간 사양을](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) 따라야 합니다. 표준 시간대를 선택하지 않으면 공백 없이 맨 끝에 문자 “Z”를 추가해야 합니다. 이 “Z”는 해당 [항해 시간](https://en.wikipedia.org/wiki/Nautical_time)을 나타냅니다. <p>단순 일정의 경우 시작 시간은 첫 번째 발생이지만 복잡한 일정의 경우 트리거는 시작 시간보다 더 일찍 발생하지 않습니다. [*시작 날짜 및 시간을 사용할 수 있는 방법에는 무엇이 있나요?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   ||||||

1.  작업이 완료 되 면 디자이너 도구 모음에서 **저장**을 선택 합니다.

1.  이제 트리거 결과와 함께 수행하려는 작업에 대한 논리 앱에 하나 이상의 작업을 계속해서 추가합니다.

## <a name="add-actions"></a>작업 추가

Microsoft Graph Security 커넥터에서 사용 가능한 다양한 작업의 사용 방법에 대한 세부 정보는 다음과 같습니다.

### <a name="manage-alerts"></a>경고 관리

가장 최근 결과를 필터링, 정렬 또는 가져오려면 [Microsoft Graph에서 지원하는 ODATA 쿼리 매개 변수](https://docs.microsoft.com/graph/query-parameters)만을 제공합니다. 전체 기준 URL이나 HTTP 작업(예: `https://graph.microsoft.com/v1.0/security/alerts`, `GET` 또는 `PATCH` 작업)을 ‘지정하지 마세요’. 높은 심각도 경고 목록을 원하는 경우 **경고 가져오기** 작업의 매개 변수를 표시하는 특정 예제는 다음과 같습니다.

`Filter alerts value as Severity eq 'high'`

이 커넥터에서 사용할 수 있는 쿼리에 대한 자세한 내용은 [Microsoft Graph Security 경고 참조 문서](https://docs.microsoft.com/graph/api/alert-list)를 참조하세요. 이 커넥터를 사용하여 향상된 환경을 빌드하려면 커넥터가 지원하는 [스키마 속성 경고](https://docs.microsoft.com/graph/api/resources/alert)를 자세히 알아보세요.

| 실행력 | Description |
|--------|-------------|
| **경고 가져오기** | 하나 이상의 [경고 속성](https://docs.microsoft.com/graph/api/resources/alert)을 기준으로 필터링된 경고를 가져옵니다. 예: <p>`Provider eq 'Azure Security Center' or 'Palo Alto Networks'` | 
| **ID로 경고 가져오기** | 경고 ID를 기준으로 특정 경고를 가져옵니다. | 
| **경고 업데이트** | 경고 ID를 기준으로 특정 경고를 업데이트합니다. <p>요청에 필수 속성과 편집 가능한 속성을 전달하려면 [경고의 편집 가능한 속성](https://docs.microsoft.com/graph/api/alert-update)을 참조하세요. 예를 들어 조사할 수 있도록 보안 분석가에게 경고를 할당하려면 경고의 **할당 대상** 속성을 업데이트할 수 있습니다. |
|||

### <a name="manage-alert-subscriptions"></a>경고 구독 관리

Microsoft Graph는 [구독](https://docs.microsoft.com/graph/api/resources/subscription) 또는 [웹후크](https://docs.microsoft.com/graph/api/resources/webhooks)를 지원합니다. 구독을 가져오거나, 업데이트 또는 삭제하려면 [Microsoft Graph에서 지원하는 ODATA 쿼리 매개 변수](https://docs.microsoft.com/graph/query-parameters)를 Microsoft Graph 엔터티 구문에 제공하고 `security/alerts` 및 ODATA 쿼리를 차례로 포함합니다. 기준 URL(예: `https://graph.microsoft.com/v1.0`)을 ‘포함하지 마세요’. 대신, 다음 예제의 형식을 사용합니다.

`security/alerts?$filter=status eq 'New'`

| 실행력 | Description |
|--------|-------------|
| **구독 만들기** | 변경 내용에 대해 알리는 [구독을 만듭니다](https://docs.microsoft.com/graph/api/subscription-post-subscriptions). 원하는 특정 경고 유형에 대해 이 구독을 필터링할 수 있습니다. 예를 들어 높은 심각도 경고에 대해 알리는 구독을 만들 수 있습니다. |
| **활성 구독 가져오기** | [만료되지 않은 구독을 가져옵니다](https://docs.microsoft.com/graph/api/subscription-list). | 
| **구독 업데이트** | 구독 ID를 입력하여 [구독을 업데이트](https://docs.microsoft.com/graph/api/subscription-update)합니다. 예를 들어 구독을 연장하려면 구독의 `expirationDateTime` 속성을 업데이트할 수 있습니다. | 
| **구독 삭제** | 구독 ID를 입력하여 [구독을 삭제](https://docs.microsoft.com/graph/api/subscription-delete)합니다. | 
||| 

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](https://aka.ms/graphsecurityconnectorreference)를 검토하세요.

## <a name="next-steps"></a>다음 단계

다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
