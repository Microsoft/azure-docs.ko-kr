---
title: Office 365 사용자에 연결
description: Azure Logic Apps를 사용 하 여 Office 365 사용자 프로필에서 프로필을 가져오고 관리 하는 작업 및 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666859"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 Office 365 사용자의 프로필 가져오기 및 관리

Office 365 사용자에 연결하여 프로필 가져오기, 사용자 검색 등을 수행합니다. Office 365 사용자를 사용하여 다음을 수행할 수 있습니다.

* Office 365 사용자에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 부하 직원 가져오기, 관리자의 사용자 프로필 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 사용자의 부하 직원을 가져온 다음 이 정보를 사용하여 SQL Azure 데이터베이스를 업데이트합니다. 

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-a-connection-to-office-365-users"></a>Office 365 사용자에 대한 연결 만들기

논리 앱에이 커넥터를 추가할 때 Azure Logic Apps 사용자 계정에 연결할 수 있도록 Office 365 사용자 계정에 로그인 해야 합니다.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

연결을 만든 후 사용자 ID 등의 Office 365 사용자 속성을 입력합니다. 이 아티클의 **REST API 참조** 에서는 이러한 속성에 대해 설명합니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

커넥터의 Swagger 설명에서 설명 하는 트리거, 작업 및 제한에 대한 자세한 기술 정보는 [커넥터의 참조 페이지를 참조](/connectors/officeusers/)하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](apis-list.md)에 대해 알아봅니다.