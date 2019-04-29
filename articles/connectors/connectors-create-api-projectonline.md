---
title: Azure Logic Apps에서 Project Online에 연결 | Microsoft Docs
description: Azure Logic Apps를 사용하여 Project Online 프로젝트, 작업 및 리소스를 모니터링하고 만들고 관리하는 워크플로 자동화
services: logic-apps
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.suite: integration
ms.topic: article
ms.assetid: 40ce621e-4925-4653-93bb-71ab9abcbdf1
tags: connectors
ms.date: 08/24/2018
ms.openlocfilehash: 663363d05c1875d22a0ecc0478abcf7e0ec89c99
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105633"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용하여 Project Online 프로젝트, 작업 및 리소스 관리

Azure Logic Apps 및 Project Online 커넥터를 사용하면 Office 365를 통해 Project Online에서 프로젝트, 작업 및 리소스에 대한 자동화된 작업 및 워크플로를 만들 수 있습니다. 워크플로를 통해 이러한 작업 및 다른 작업을 수행할 수 있습니다. 예를 들어:

* 새 프로젝트, 작업 또는 리소스를 만들 때 모니터링합니다. 또는 새 프로젝트를 게시할 때 모니터링합니다.
* 새 프로젝트, 작업 또는 리소스를 만듭니다.
* 기존 프로젝트 또는 작업을 나열합니다.
* 프로젝트를 체크 아웃, 체크 인 및 게시합니다.

Project Online을 사용하면 강력한 프로젝트 관리 기능을 제공하여 어디서든지 거의 모든 디바이스에서 프로젝트와 프로젝트 포트폴리오 투자를 계획하고, 우선 순위를 정하며, 관리할 수 있습니다. Project Online 트리거를 사용하여 Project Online에서 응답을 가져오고 출력을 다른 작업에 사용할 수 있게 할 수 있습니다. 논리 앱에서 작업을 사용하여 Project Online에서 다양한 작업을 수행할 수 있습니다. 논리 앱을 처음 접하는 경우 [Azure Logic Apps란?](../logic-apps/logic-apps-overview.md)을 검토합니다.

## <a name="prerequisites"></a>필수 조건

* Azure 구독. Azure 구독이 없는 경우 <a href="https://azure.microsoft.com/free/" target="_blank">체험 Azure 계정에 등록</a>합니다. 

* [Office 365 계정](https://www.office.com/)을 통해 사용할 수 있는 Project Online, 

* [논리 앱 만드는 방법](../logic-apps/quickstart-create-first-logic-app-workflow.md)에 관한 기본 지식

* Project Online 데이터에 액세스하려는 논리 앱입니다. Project Online 트리거를 시작하려면 [빈 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md). Project Online 작업을 사용하려면, 예를 들어 **되풀이** 트리거 같은 다른 트리거를 통해 논리 앱을 시작합니다.

## <a name="connect-to-project-online"></a>Project Online에 연결

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. [Azure Portal](https://portal.azure.com)에 로그인하고 아직 열리지 않은 경우 Logic App Designer에서 논리 앱을 엽니다.

1. 경로를 선택합니다. 

   * 빈 논리 앱의 경우 검색 상자에서 필터로 "Project Online"을 입력합니다. 
   트리거 목록에서 원하는 트리거를 선택합니다. 

     또는

   * 기존 논리 앱의 경우 작업을 추가하려는 단계에서 **새 단계**를 선택합니다. 검색 상자에서 필터로 "Project Online"을 입력합니다. 작업 목록에서 원하는 작업을 선택합니다.

1. Project Online에 로그인하라는 메시지가 표시된 경우 지금 로그인하세요.

   자격 증명을 통해 Project Online에 대한 연결을 만들고 데이터에 액세스하는 권한이 논리 앱에 부여됩니다.

1. 선택한 트리거 또는 작업에 대해 필요한 세부 정보를 제공하고 논리 앱의 워크플로를 계속 빌드합니다.

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI(이전의 Swagger) 설명서에 설명된 트리거, 작업 및 제한에 대한 기술 정보는 커넥터의 [참조 페이지](/connectors/projectonline/)를 검토하세요.

## <a name="get-support"></a>지원 받기

* 질문이 있는 경우 [Azure Logic Apps 포럼](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps)을 방문해 보세요.
* 기능 아이디어를 제출하거나 투표하려면 [Logic Apps 사용자 의견 사이트](https://aka.ms/logicapps-wish)를 방문하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.