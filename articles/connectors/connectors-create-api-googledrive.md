---
title: Google 드라이브에 연결 - Azure Logic Apps | Microsoft Docs
description: Google 드라이브 REST API 및 Azure Logic Apps을 사용하여 파일 만들기 및 관리
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 11/07/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 43bd5248f1bb80c71a85935c585deac6152be78b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62105092"
---
# <a name="get-started-with-the-google-drive-connector"></a>Google 드라이브 커넥터 시작
Google 드라이브에 연결하여 파일 만들기, 행 가져오기 등의 작업을 수행합니다. Google 드라이브를 사용하면 다음과 같은 작업을 수행할 수 있습니다. 

* 검색에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 이미지 검색, 뉴스 검색 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 비디오를 검색한 다음 Twitter를 사용하여 Twitter 피드에 해당 비디오를 게시할 수 있습니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-the-connection-to-google-drive"></a>Google 드라이브에 대한 연결 만들기
논리 앱에 이 커넥터를 추가할 때 Google 드라이브에 연결할 권한을 논리 앱에 부여해야 합니다.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]
> 
> 

연결을 만든 후에 폴더 경로 또는 파일 이름 등의 Google 드라이브 속성을 입력합니다. 

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/googledrive/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.
