---
title: Azure Logic Apps에서 RSS 피드에 연결 | Microsoft Docs
description: Azure Logic Apps를 사용하여 RSS 피드를 모니터링하고 관리하는 작업 및 워크플로 자동화
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: a10a6277-ed29-4e68-a881-ccdad6fd0ad8
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 01573871700bbeeb653ce3efdbf6c6aca88fd454
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104887"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 RSS 피드 관리

Azure Logic Apps 및 RSS 커넥터를 통해 모든 RSS 피드에 대한 자동화된 작업 및 워크플로를 만들 수 있습니다. 예를 들어:

* RSS 피드 항목이 게시될 때 모니터링합니다.
* 모든 RSS 피드 항목을 나열합니다.

Really Simple Syndication이라고도 하는 RSS(Rich Site Summary)는 웹 배포를 위한 인기 있는 형식으로서, 블로그 게시물 및 뉴스 헤드라인 같이 자주 업데이트되는 콘텐츠를 게시하는 데 사용됩니다. 많은 콘텐츠 게시자는 사용자가 해당 콘텐츠를 구독할 수 있도록 RSS 피드를 제공합니다. 

RSS 트리거를 사용하여 RSS 피드에서 응답을 가져오고 출력을 다른 작업에 사용할 수 있게 할 수 있습니다. 논리 앱에서 RSS 동작을 사용하여 RSS 피드에서 작업을 수행할 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* RSS 피드의 URL

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* RSS 피드에 액세스하려는 논리 앱입니다. RSS 트리거를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). RSS 동작을 사용하려면, 예를 들어 **되풀이** 트리거 같은 다른 트리거를 통해 논리 앱을 시작합니다.

## <a name="connect-to-an-rss-feed"></a>RSS 피드에 연결

1. [Azure Portal](https://portal.azure.com)에 로그인하고 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 경로를 선택합니다. 

   * 빈 논리 앱의 경우 검색 상자에서 필터로 "rss"를 입력합니다. 트리거 목록에서 원하는 트리거를 선택합니다. 

     또는

   * 기존 논리 앱의 경우 작업을 추가하려는 단계에서 **새 단계**를 선택합니다. 검색 상자에서 필터로 "rss"를 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

1. 선택한 트리거 또는 작업에 대해 필요한 세부 정보를 제공하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/rss/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.