---
title: Office 365 Outlook 연결
description: Azure Logic Apps를 사용 하 여 Office 365 Outlook에서 전자 메일, 연락처 및 일정을 관리 하는 작업 및 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/08/2020
tags: connectors
ms.openlocfilehash: b0f2b8b9c369fdb42c7e0e7f77fc090424ae3729
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732707"
---
# <a name="manage-email-contacts-and-calendars-in-office-365-outlook-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 Office 365 Outlook에서 전자 메일, 연락처 및 일정 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [office 365 Outlook 커넥터](/connectors/office365connector/)를 사용 하 여 논리 앱을 빌드하여 office 365 계정을 관리 하는 자동화 된 작업 및 워크플로를 만들 수 있습니다. 예를 들어 다음과 같은 작업을 자동화할 수 있습니다.

* 전자 메일을 가져오고 보내고 회신 합니다. 
* 일정에서 모임 일정을 예약 합니다.
* 연락처를 추가 하 고 편집 합니다. 

트리거를 사용 하 여 워크플로를 시작할 수 있습니다. 예를 들어 새 전자 메일이 도착 하는 경우, 일정 항목이 업데이트 될 때 또는 Salesforce와 같은 차이점 서비스에서 이벤트가 발생 하는 경우를 들 수 있습니다. 트리거 이벤트에 응답 하는 동작 (예: 전자 메일 보내기 또는 새 일정 이벤트 만들기)을 사용할 수 있습니다. 

> [!NOTE]
> @outlook.com 또는 @hotmail.com 계정에 대한 작업을 자동화 하려면 [Outlook.com 커넥터](../connectors/connectors-create-api-outlook.md)를 사용 합니다.

## <a name="prerequisites"></a>필수 조건

* [Office 365 계정](https://www.office.com/)

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* Office 365 Outlook 계정에 액세스 하려는 논리 앱입니다. Office 365 Outlook 트리거를 사용 하 여 워크플로를 시작 하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 있어야 합니다. 워크플로에 Office 365 Outlook 작업을 추가 하려면 논리 앱에 이미 트리거가 있어야 합니다.

## <a name="add-a-trigger"></a>트리거 추가

[트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts) 는 논리 앱에서 워크플로를 시작 하는 이벤트입니다. 이 예제 논리 앱은 지정 된 간격 및 빈도에 따라 전자 메일 계정에서 업데이트 된 일정 이벤트를 확인 하는 "폴링" 트리거를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)에서 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에서 필터로 `office 365 outlook`을 입력합니다. 이 예제에서는 **예정 된 이벤트가 곧 시작 될 때를**선택 합니다.
   
   ![트리거를 선택 하 여 논리 앱 시작](./media/connectors-create-api-office365-outlook/office365-trigger.png)

1. 로그인 하 라는 메시지가 표시 되 면 논리 앱이 계정에 연결할 수 있도록 Office 365 자격 증명을 제공 합니다. 그렇지 않고 연결이 이미 있는 경우 트리거의 속성에 대한 정보를 제공 합니다.

   이 예에서는 트리거가 확인 하는 달력을 선택 합니다. 예를 들면 다음과 같습니다.

   ![트리거의 속성 구성](./media/connectors-create-api-office365-outlook/select-calendar.png)

1. 트리거에서 **빈도** 및 **간격** 값을 설정 합니다. **표준 시간대**와 같은 사용 가능한 다른 트리거 속성을 추가 하려면 **새 매개 변수 추가** 목록에서 해당 속성을 선택 합니다.

   예를 들어 트리거가 15 분 마다 일정을 확인 하도록 하려면 **Frequency** 를 **Minute**로 설정 하 고 **Interval** 을 `15`로 설정 합니다. 

   ![트리거의 빈도 및 간격 설정](./media/connectors-create-api-office365-outlook/calendar-settings.png)

1. 디자이너 도구 모음에서 **저장**을 선택합니다.

이제 트리거가 발생 한 후 실행 되는 작업을 추가 합니다. 예를 들어 일정 이벤트가 15 분 내에 시작 될 때 텍스트를 전송 하는 Twilio **메시지 보내기** 작업을 추가할 수 있습니다.

## <a name="add-an-action"></a>작업 추가

[작업](../logic-apps/logic-apps-overview.md#logic-app-concepts) 은 논리 앱의 워크플로에서 실행 되는 작업입니다. 이 예제 논리 앱은 Office 365 Outlook에서 새 연락처를 만듭니다. 다른 트리거 또는 작업의 출력을 사용 하 여 연락처를 만들 수 있습니다. 예를 들어, **레코드를 만들 때**논리 앱에서 Dynamics 365 트리거를 사용 한다고 가정 합니다. Office 365 Outlook **연락처 만들기** 작업을 추가 하 고 SalesForce 트리거의 출력을 사용 하 여 새 연락처를 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 워크플로의 마지막 단계로 동작을 추가 하려면 **새 단계**를 선택 합니다. 

   단계 사이에 작업을 추가 하려면 해당 단계 사이의 화살표 위로 포인터를 이동 합니다. 표시 되는 더하기 기호 ( **+** )를 선택 하 고 **작업 추가**를 선택 합니다.

1. 검색 상자에서 필터로 `office 365 outlook`을 입력합니다. 이 예제에서는 **연락처 만들기**를 선택 합니다.

   ![논리 앱에서 실행할 작업을 선택 합니다.](./media/connectors-create-api-office365-outlook/office365-actions.png) 

1. 로그인 하 라는 메시지가 표시 되 면 논리 앱이 계정에 연결할 수 있도록 Office 365 자격 증명을 제공 합니다. 그렇지 않고 연결이 이미 존재 하는 경우 작업의 속성에 대한 정보를 제공 합니다.

   이 예제에서는 작업에서 새 연락처를 만드는 연락처 폴더를 선택 합니다. 예를 들면 다음과 같습니다.

   ![작업 속성 구성](./media/connectors-create-api-office365-outlook/select-contacts-folder.png)

   사용 가능한 다른 작업 속성을 추가 하려면 **새 매개 변수 추가** 목록에서 해당 속성을 선택 합니다.

1. 디자이너 도구 모음에서 **저장**을 선택합니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

커넥터의 Swagger 파일에 설명 된 대로 트리거, 작업 및 제한에 대한 자세한 기술 정보는 [커넥터의 참조 페이지](/connectors/office365connector/)를 참조 하세요. 

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
