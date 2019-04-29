---
title: Azure Logic Apps에서 Twilio에 연결 | Microsoft Docs
description: Azure Logic Apps를 사용하여 Twilio 계정을 통해 글로벌 SMS, MMS 및 IP 메시지를 관리하는 작업 및 워크플로 자동화
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: fab52236c701f10c8e8e23ac398362ca4583ea06
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104904"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Twilio에서 메시지 관리

Azure Logic Apps 및 Twilio 커넥터를 사용하면 글로벌 SMS, MMS 및 IP 메시지를 포함한 Twilio의 메시지를 가져오기, 전송 및 나열하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 이러한 작업을 사용하여 Twilio 계정으로 작업을 수행할 수 있습니다. 또한 다른 작업에서 Twilio 작업의 출력을 사용하도록 할 수 있습니다. 예를 들어 새 메시지가 도착하면 Slack 커넥터를 사용하여 메시지 콘텐츠를 보낼 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* [Twilio](https://www.twilio.com/)에서: 

  * Twilio 대시보드에서 찾을 수 있는 Twilio 계정 ID 및 [인증 토큰](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them)

    자격 증명을 통해 논리 앱에서 Twilio 계정에 대한 연결을 만들고 액세스하는 권한이 논리 앱에 부여됩니다. 
    Twilio 체험 계정을 사용하는 경우 *확인된* 전화 번호로만 SMS를 보낼 수 있습니다.

  * SMS를 보낼 수 있다고 확인된 Twilio 전화 번호

  * SMS를 받을 수 있다고 확인된 Twilio 전화 번호

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Twilio 계정에 액세스하려는 논리 앱입니다. Twilio 동작을 사용하려면 예를 들어 **되풀이** 트리거 같은 다른 트리거를 통해 논리 앱을 시작합니다.

## <a name="connect-to-twilio"></a>Twilio에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 경로를 선택합니다. 

     * 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 

       또는

     * 작업을 추가하려는 단계 사이에서, 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
     표시되는 더하기 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.
     
       검색 상자에서 필터로 “twilio”를 입력합니다. 
       작업 목록에서 원하는 작업을 선택합니다.

1. 연결에 필요한 세부 정보를 입력한 다음, **만들기**를 선택합니다.

   * 연결에 사용할 이름
   * Twilio 계정 ID 
   * Twilio 액세스 (인증) 토큰

1. 선택한 작업에 대해 필요한 세부 정보를 입력하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/twilio/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.