---
title: Outlook.com에 연결 - Azure Logic Apps | Microsoft Docs
description: Outlook.com REST API 및 Azure Logic Apps를 사용하여 이메일, 일정 및 연락처 관리
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
manager: jeconnoc
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.topic: article
ms.date: 08/18/2016
ms.reviewer: klam, LADocs
ms.suite: integration
ms.openlocfilehash: fd6836451a73551487b8f97903594154a2efc894
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105805"
---
# <a name="manage-email-calendars-and-contacts-in-outlookcom-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Outlook.com에서 이메일, 일정 및 연락처 관리

이 문서에서는 Box 커넥터가 있는 논리 앱에서 Outlook.com 계정을 만들고 관리하는 방법을 보여 줍니다. 이러한 방식으로 다음과 같이 Outlook.com 계정에 대한 작업 및 워크플로를 자동화하는 논리 앱을 만들 수 있습니다.

* 이메일 보내기 
* 회의 예약
* 연락처 추가 

논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토하세요.

## <a name="prerequisites"></a>필수 조건

* [Outlook.com 계정](https://outlook.live.com/owa/)

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* Outlook.com 계정에 액세스하려는 논리 앱입니다. Outlook 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](../logic-apps/quickstart-create-first-logic-app-workflow.md)이 필요합니다. 

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

## <a name="connect-to-outlookcom"></a>Outlook.com에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Connect to Outlook.com](../../includes/connectors-create-api-outlook.md)]

## <a name="connector-reference"></a>커넥터 참조

커넥터의 Swagger 파일에서 설명한 것처럼 트리거, 작업 및 제한과 같은 기술 세부 정보는 [커넥터의 참조 페이지](/connectors/outlook/)를 참조하세요. 

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.