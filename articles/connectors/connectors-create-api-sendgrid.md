---
title: Azure Logic Apps에서 SendGrid에 연결 | Microsoft Docs
description: Azure Logic Apps를 사용하여 SendGrid에서 이메일을 보내고 메일 목록을 관리하는 작업 및 워크플로 자동화
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.topic: article
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 7eecd3908883b195b52755d03e70872afe9180bb
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105737"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 SendGrid에서 이메일을 보내고 메일 그룹 관리

Azure Logic Apps 및 SendGrid 커넥터를 사용하여 이메일을 보내고 받는 사람 목록을 관리하는 자동화된 작업 및 워크플로 만들 수 있습니다. 예를 들어:

* 이메일을 보냅니다.
* 받는 사람을 목록에 추가합니다.
* 글로벌 비표시 오류(Suppression)를 가져오고, 추가하고, 관리합니다.

이러한 작업을 수행하려면 논리 앱에서 SendGrid 작업을 사용할 수 있습니다. SendGrid 작업의 출력을 사용하기 위한 다른 작업도 있을 수 있습니다. 

이 커넥터에서는 논리 앱을 시작하고 **되풀이** 트리거 같은 별도 트리거를 사용하도록 작업만 제공합니다. 예를 들어, 받는 사람을 목록에 정기적으로 추가하는 경우 Office 365 Outlook 커넥터 또는 Outlook.com 커넥터를 사용하여 받는 사람 및 목록에 대한 이메일을 보낼 수 있습니다.
논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* [SendGrid 계정](https://www.sendgrid.com/) 및 [SendGrid API 키](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   API 키는 연결을 만들고 SendGrid 계정에 액세스하는 권한을 논리 앱에 부여합니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* SendGrid 계정에 액세스하려는 논리 앱입니다. SendGrid 동작을 사용하려면, 예를 들어 **되풀이** 트리거 같은 다른 트리거를 통해 논리 앱을 시작합니다.

## <a name="connect-to-sendgrid"></a>SendGrid에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 경로를 선택합니다. 

   * 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 

     또는

   * 작업을 추가하려는 단계 사이에서, 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
   표시되는 더하기 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.

1. 검색 상자에 필터로 "sendgrid"를 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

1. 연결 이름을 입력합니다. 

1. SendGrid API 키를 입력한 다음, **만들기**를 선택합니다.

1. 선택한 작업에 대해 필요한 세부 정보를 입력하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/sendgrid/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.