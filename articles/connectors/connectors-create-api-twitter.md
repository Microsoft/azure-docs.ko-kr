---
title: Azure Logic Apps에서 Twitter에 연결 | Microsoft Docs
description: Azure Logic Apps를 사용하여 Twitter 계정에서 트윗을 모니터링 및 관리하고, 팔로워, 자신이 팔로우하는 사용자, 다른 사용자, 타임라인 등에 대한 데이터를 가져오는 작업 및 워크플로 자동화
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.assetid: 8bce2183-544d-4668-a2dc-9a62c152d9fa
ms.topic: article
tags: connectors
ms.date: 08/25/2018
ms.openlocfilehash: 0fbd89202796cb4543dbecbeee605c9b87cc9d05
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104989"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Twitter 모니터링 및 관리

Azure Logic Apps 및 Twitter 커넥터를 사용하면 다음과 같이 다른 작업과 함께 트윗, 팔로워, 사용자 및 팔로우하는 사용자, 타임라인 등과 같이 Twitter에서 관심 있는 데이터를 모니터링하고 관리하는 자동화된 작업 및 워크플로를 만들 수 있습니다.

* 트윗을 모니터링, 게시 및 검색합니다.
* 팔로워, 팔로우하는 사용자, 타임라인 등과 같은 데이터를 가져옵니다.

트리거를 사용하여 Twitter 계정에서 응답을 가져오고 다른 작업에서 출력을 사용하도록 할 수 있습니다. Twitter 계정을 사용하여 작업을 수행하는 작업을 사용할 수 있습니다. 또한 다른 작업에서 Twitter 작업의 출력을 사용하도록 할 수 있습니다. 예를 들어 특정 해시태그가 있는 새 트윗이 표시되면 Slack 커넥터를 사용하여 메시지를 보낼 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* Twitter 계정 및 사용자 자격 증명

   자격 증명을 통해 Twitter 계정에 대한 연결을 만들고 액세스하는 권한이 논리 앱에 부여됩니다.

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Twitter 계정에 액세스하려는 논리 앱입니다. Twitter 트리거를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). Twitter 동작을 사용하려면 예를 들어 **되풀이** 트리거 같은 다른 트리거를 통해 논리 앱을 시작합니다.

## <a name="connect-to-twitter"></a>Twitter에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고, 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 경로를 선택합니다. 

   * 빈 논리 앱의 경우 검색 상자에서 필터로 “twitter”를 입력합니다. 
   트리거 목록에서 원하는 트리거를 선택합니다. 

     또는

   * 기존 논리 앱의 경우: 
   
     * 작업을 추가하려는 마지막 단계에서 **새 단계**를 선택합니다. 

       또는

     * 작업을 추가하려는 단계 사이에서, 단계 사이에 있는 화살표 위로 포인터를 이동합니다. 
     표시되는 더하기 기호(**+**)를 선택한 다음, **작업 추가**를 선택합니다.
     
       검색 상자에서 필터로 “twitter”를 입력합니다. 
       작업 목록에서 원하는 작업을 선택합니다.

1. Twitter에 로그인하라는 메시지가 표시되면 로그인합니다. 그러면 논리 앱에 대한 액세스 권한을 부여할 수 있습니다.

1. 선택한 트리거 또는 작업에 대해 필요한 세부 정보를 제공하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="examples"></a>예

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Twitter 트리거: 새 트윗이 게시될 때

이 트리거는 예를 들어 #Seattle이란 해시태그가 있는 새 트윗을 트리거에서 감지하면 논리 앱 워크플로를 시작합니다. 따라서 예를 들어 이러한 트윗을 찾으면 Dropbox 커넥터를 사용하여 Dropbox 계정과 같은 저장소에 트윗의 콘텐츠가 포함된 파일을 추가할 수 있습니다. 

필요에 따라 지정된 최소 팔로워가 있는 사용자의 트윗만 가져오도록 조건을 포함시킬 수 있습니다.

**엔터프라이즈 예제**: 회사에 대 한 트 윗을 모니터링 하 고 SQL database에 트 윗의 콘텐츠를 업로드 하려면이 트리거를 사용할 수 있습니다.

### <a name="twitter-action-post-a-tweet"></a>Twitter 작업: 트윗 게시

이 작업은 트윗을 게시하되, 앞에서 설명한 트리거에서 찾은 트윗의 콘텐츠만 포함하도록 작업을 사용자가 설정할 수 있습니다. 

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/twitterconnector/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.
