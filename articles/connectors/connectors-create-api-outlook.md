---
title: Outlook.com에 연결
description: Azure Logic Apps를 사용 하 여 Outlook.com의 메일, 일정 및 연락처를 관리 하는 작업 및 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/18/2016
tags: connectors
ms.openlocfilehash: 8d3b180b6f1e9dc4ec4b09dd81786cc81e8588da
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/07/2020
ms.locfileid: "75707189"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 Outlook.com의 메일, 일정 및 연락처 관리

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) 및 [Outlook.com 커넥터](/connectors/outlook/)를 사용 하 여 논리 앱을 빌드하여 @outlook.com 또는 @hotmail.com 계정을 관리 하는 자동화 된 작업 및 워크플로를 만들 수 있습니다. 예를 들어 다음과 같은 작업을 자동화할 수 있습니다.

* 전자 메일을 가져오고 보내고 회신 합니다.
* 일정에서 모임 일정을 예약 합니다.
* 연락처를 추가 하 고 편집 합니다.

트리거를 사용 하 여 워크플로를 시작할 수 있습니다. 예를 들어 새 전자 메일이 도착 하는 경우, 일정 항목이 업데이트 될 때 또는 차이점 서비스에서 이벤트가 발생 하는 경우를 들 수 있습니다. 트리거 이벤트에 응답 하는 동작 (예: 전자 메일 보내기 또는 새 일정 이벤트 만들기)을 사용할 수 있습니다.

> [!NOTE]
> @fabrikam.onmicrosoft.com와 같은 Microsoft 작업 계정에 대한 작업을 자동화 하려면 [Office 365 Outlook 커넥터](../connectors/connectors-create-api-office365-outlook.md)를 사용 합니다.

## <a name="prerequisites"></a>필수 조건

* [Outlook.com 계정](https://outlook.live.com/owa/)

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다. 

* Outlook.com 계정에 액세스하려는 논리 앱입니다. Outlook.com 트리거를 사용 하 여 워크플로를 시작 하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 있어야 합니다. 워크플로에 Outlook.com 작업을 추가 하려면 논리 앱에 트리거가 이미 있어야 합니다.

## <a name="add-a-trigger"></a>트리거 추가

[트리거](../logic-apps/logic-apps-overview.md#logic-app-concepts) 는 논리 앱에서 워크플로를 시작 하는 이벤트입니다. 이 예제 논리 앱은 지정 된 간격 및 빈도에 따라 전자 메일 계정에서 새 전자 메일을 확인 하는 "폴링" 트리거를 사용 합니다.

1. [Azure Portal](https://portal.azure.com)에서 논리 앱 디자이너에서 빈 논리 앱을 엽니다.

1. 검색 상자에 “outlook.com”을 필터로 입력합니다. 이 예에서는 **새 전자 메일이 도착할 때**를 선택 합니다.

1. 로그인 하 라는 메시지가 표시 되 면 논리 앱이 계정에 연결할 수 있도록 Outlook.com 자격 증명을 제공 합니다. 그렇지 않고 연결이 이미 있는 경우 트리거 속성에 대한 정보를 제공 합니다.

1. 트리거에서 **빈도** 및 **간격** 값을 설정 합니다.

   예를 들어 트리거가 15 분 마다 폴링 되도록 하려면 **빈도** 를 **분**으로 설정 하 고 **간격** 을 **15**로 설정 합니다.

1. 디자이너 도구 모음에서 **저장**을 선택 합니다. 그러면 논리 앱이 저장 됩니다.

트리거에 응답 하려면 다른 작업을 추가 합니다. 예를 들어 전자 메일이 도착할 때 텍스트를 전송 하는 Twilio **메시지 보내기** 작업을 추가할 수 있습니다.

## <a name="add-an-action"></a>작업 추가

[작업](../logic-apps/logic-apps-overview.md#logic-app-concepts) 은 논리 앱의 워크플로에서 실행 되는 작업입니다. 이 예제 논리 앱은 Outlook.com 계정에서 전자 메일을 보냅니다. 다른 트리거의 출력을 사용 하 여 작업을 채울 수 있습니다. 예를 들어, **개체를 만들 때** 논리 앱에서 SalesForce를 사용 한다고 가정 합니다. Outlook.com **전자 메일 보내기** 작업을 추가 하 고 SalesForce 트리거의 출력을 전자 메일에서 사용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)의 Logic Apps 디자이너에서 논리 앱을 엽니다.

1. 워크플로의 마지막 단계로 동작을 추가 하려면 **새 단계**를 선택 합니다. 

   단계 사이에 작업을 추가 하려면 해당 단계 사이의 화살표 위로 포인터를 이동 합니다. 표시 되는 더하기 기호 ( **+** )를 선택 하 고 **작업 추가**를 선택 합니다.

1. 검색 상자에 "outlook.com"를 필터로 입력 합니다. 이 예에서는 **전자 메일 보내기**를 선택 합니다. 

1. 로그인 하 라는 메시지가 표시 되 면 논리 앱이 계정에 연결할 수 있도록 Outlook.com 자격 증명을 제공 합니다. 그렇지 않고 연결이 이미 있는 경우 작업 속성에 대한 정보를 제공 합니다.

1. 디자이너 도구 모음에서 **저장**을 선택 합니다. 그러면 논리 앱이 저장 됩니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에서 설명한 것처럼 트리거, 작업 및 제한과 같은 기술 세부 정보는 [커넥터의 참조 페이지](/connectors/outlook/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
