---
title: Office 365 사용자에 연결 - Azure Logic Apps | Microsoft Docs
description: Office 365 사용자 REST API 및 Azure Logic Apps을 사용하여 사용자 프로필 관리
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 08/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 3865fbc4fbc39da0860218565b0a8956b2dad8ee
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105874"
---
# <a name="get-started-with-the-office-365-users-connector"></a>Office 365 사용자 커넥터 시작
Office 365 사용자에 연결하여 프로필 가져오기, 사용자 검색 등을 수행합니다. Office 365 사용자를 사용하여 다음을 수행할 수 있습니다.

* Office 365 사용자에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 부하 직원 가져오기, 관리자의 사용자 프로필 가져오기 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 사용자의 부하 직원을 가져온 다음 이 정보를 사용하여 SQL Azure 데이터베이스를 업데이트합니다. 

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-a-connection-to-office-365-users"></a>Office 365 사용자에 대한 연결 만들기
논리 앱에 이 커넥터를 추가할 때 Office 365 사용자 계정에 로그인하고 논리 앱을 계정에 연결해야 합니다.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

연결을 만든 후 사용자 ID 등의 Office 365 사용자 속성을 입력합니다. 이 아티클의 **REST API 참조** 에서는 이러한 속성에 대해 설명합니다.

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/officeusers/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.