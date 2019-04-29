---
title: Azure Logic Apps에서 Wunderlist에 연결 | Microsoft Docs
description: Azure Logic Apps를 사용하여 Wunderlist 계정에서 목록, 작업, 알림 등을 모니터링하고 관리하는 작업 및 워크플로 자동화
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: e4773ecf-3ad3-44b4-a1b5-ee5f58baeadd
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: e3570ab1227ca388ac62bffdc74bb68b1ddc41d1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105669"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Wunderlist 모니터링 및 관리

Azure Logic Apps 및 Wunderlist 커넥터를 사용하면 다음과 같이 다른 작업들과 함께 Wunderlist 계정에서 할 일 목록, 작업, 알림 등을 모니터링하고 관리하는 자동화된 작업 및 워크플로를 만들 수 있습니다.

* 새 작업이 만들어질 때, 작업이 만료될 때 또는 미리 알림이 발생할 때 모니터링합니다.
* 목록, 정보, 작업, 하위 작업 등을 만들고 관리합니다.
* 미리 알림을 설정합니다.
* 목록, 작업, 하위 작업, 알림, 파일, 정보, 설명 등을 가져옵니다.

[Wunderlist](https://www.wunderlist.com/)는 어디서든 디바이스에서 사용자 프로젝트, 할 일 목록 및 작업을 계획, 관리 및 완료하는 데 유용한 서비스입니다. 트리거를 사용하여 Wunderlist 계정에서 응답을 가져오고 다른 작업에서 출력을 사용하도록 할 수 있습니다. Wunderlist 계정을 사용하여 작업을 수행하는 작업을 사용할 수 있습니다. 또한 다른 작업에서 Wunderlist 작업의 출력을 사용하도록 할 수 있습니다. 예를 들어, 새 작업이 만료되면 Slack 커넥터를 사용하여 메시지를 게시할 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* Wunderlist 계정 및 사용자 자격 증명

   자격 증명을 통해 Wunderlist 계정에 대한 연결을 만들고 액세스하는 권한이 논리 앱에 부여됩니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Yammer 계정에 액세스하려는 논리 앱입니다. Wunderlist 트리거를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). 예를 들어 Wunderlist 동작을 사용하려면 **되풀이** 트리거 같은 다른 트리거를 통해 논리 앱을 시작합니다.

## <a name="connect-to-wunderlist"></a>Wunderlist에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 경로를 선택합니다. 

   * 빈 논리 앱의 경우 검색 상자에서 필터로 “wunderlist”를 입력합니다. 
   트리거 목록에서 원하는 트리거를 선택합니다. 

     또는

   * 기존 논리 앱의 경우: 
   
     * 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 

       또는

     * 작업을 추가하려는 단계 사이에서, 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
     표시되는 더하기 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.
     
       검색 상자에서 필터로 “wunderlist”를 입력합니다. 
       작업 목록에서 원하는 작업을 선택합니다.

1. Wunderlist에 로그인하라는 메시지가 표시되면 로그인합니다. 그러면 액세스를 허용할 수 있습니다.

1. 선택한 트리거 또는 작업에 대해 필요한 세부 정보를 제공하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/wunderlist/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.