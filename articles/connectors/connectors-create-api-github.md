---
title: GitHub 리포지토리 액세스, 모니터링 및 관리
description: Azure Logic Apps를 사용 하 여 자동화 된 워크플로를 만들어 GitHub 이벤트를 모니터링 하 고 GitHub 리포지토리를 관리 합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75378452"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 GitHub 리포지토리 모니터링 및 관리

GitHub는 Git의 배포된 버전 관리와 SCM(소스 코드 관리) 기능 및 다른 기능을 제공하는 웹 기반 리포지토리 호스팅 서비스입니다.

GitHub 커넥터를 시작하려면 [먼저 논리 앱을 만듭니다](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>GitHub에 대한 연결 만들기

논리 앱에서 GitHub 커넥터를 사용하려면 먼저 *연결*을 만들고, 다음 속성에 대한 세부 정보를 제공해야 합니다. 

| 속성 | 필수 | Description | 
| -------- | -------- | ----------- | 
| 토큰 | 예 | GitHub 자격 증명을 제공합니다. |

연결을 만든 후에 이 작업을 실행하고 이 문서에서 설명하는 트리거를 수신 대기할 수 있습니다.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>커넥터 참조

커넥터의 OpenAPI (이전의 Swagger) 설명에서 설명 하는 트리거, 작업 및 제한에 대한 자세한 기술 정보는 [커넥터의 참조 페이지를 참조](/connectors/github/)하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](../connectors/apis-list.md)에 대해 알아봅니다.