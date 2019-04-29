---
title: Azure Logic Apps에서 SMTP에 연결 | Microsoft Docs
description: Azure Logic Apps를 사용하여 SMTP(Simple Mail Transfer Protocol) 계정을 통해 이메일을 전송하는 작업 및 워크플로 자동화
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: d4141c08-88d7-4e59-a757-c06d0dc74300
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 78b1eb6272fa97ef392e97723454d29cf56bb4bf
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62106153"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Azure Logic Apps를 사용하여 SMTP 계정에서 이메일 보내기

Azure Logic Apps 및 SMTP(Simple Mail Transfer Protocol) 커넥터를 사용하여 SMTP 계정에서 이메일을 전송하는 자동화된 작업 및 워크플로를 만들 수 있습니다. 또한 다른 작업에서 SMTP 작업의 출력을 사용하도록 할 수 있습니다. 예를 들어 SMTP에서 이메일을 보낸 후 Slack 커넥터를 사용하여 Slack 팀에 알릴 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* SMTP 계정 및 사용자 자격 증명

  자격 증명을 통해 SMTP 계정에 대한 연결을 만들고 액세스하는 권한이 논리 앱에 부여됩니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* SMTP 계정에 액세스하려는 논리 앱입니다. SMTP 작업을 사용하려면 Salesforce 계정이 있는 경우 Salesforce 트리거와 같은 트리거를 사용하여 논리 앱을 시작합니다.

  예를 들어 **레코드가 만들어지는 경우** Salesforce 트리거를 사용하여 논리 앱을 시작할 수 있습니다. 
  이 트리거는 잠재 고객과 같은 새 레코드가 Salesforce에서 만들어질 때마다 발생합니다. 
  그런 다음, SMTP **이메일 보내기** 작업을 사용하여 이 트리거를 따를 수 있습니다. 이런 방식으로 새 레코드가 만들어질 때 논리 앱은 새 레코드에 대한 SMTP 계정에서 이메일을 보냅니다.

## <a name="connect-to-smtp"></a>SMTP에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. SMTP 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 

   단계 사이에서 작업을 추가하려면 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
   표시되는 더하기 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.

1. 검색 상자에서 필터로 "smtp"를 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

1. 메시지가 표시되면 이 연결 정보를 제공합니다.

   | 자산 | 필수 | 설명 |
   |----------|----------|-------------|
   | **연결 이름** | 예 | SMTP 서버에 대한 연결의 이름 | 
   | **SMTP 서버 주소** | 예 | SMTP 서버에 대한 주소 | 
   | **사용자 이름** | 예 | SMTP 계정에 대한 사용자 이름 | 
   | **암호** | 예 | SMTP 계정에 대한 암호 | 
   | **SMTP 서버 포트** | 아닙니다. | 사용하려는 SMTP 서버의 특정 포트 | 
   | **SSL 사용?** | 아닙니다. | SSL 암호화를 설정하거나 해제합니다. | 
   |||| 

1. 선택한 작업에 필요한 정보를 입력합니다. 

1. 논리 앱을 저장하거나 논리 앱의 워크플로 빌드를 계속합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/smtpconnector/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.