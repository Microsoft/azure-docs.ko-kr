---
title: Google Drive에 연결
description: Azure Logic Apps를 사용 하 여 Google 드라이브의 파일을 만들고 관리 하는 워크플로 자동화
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 155a579438747ca7de67eba6449b8a0191e86e2c
ms.sourcegitcommit: ff9688050000593146b509a5da18fbf64e24fbeb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75666842"
---
# <a name="create-and-manage-files-for-google-drive-by-using-azure-logic-apps"></a>Azure Logic Apps를 사용 하 여 Google Drive의 파일 만들기 및 관리

Google 드라이브에 연결하여 파일 만들기, 행 가져오기 등의 작업을 수행합니다. Google 드라이브를 사용하면 다음과 같은 작업을 수행할 수 있습니다. 

* 검색에서 가져온 데이터를 기반으로 비즈니스 흐름을 빌드합니다. 
* 이미지 검색, 뉴스 검색 등의 작업을 사용합니다. 이러한 작업을 사용하여 응답을 가져오고 출력을 다른 작업에 사용할 수 있도록 설정합니다. 예를 들어 비디오를 검색한 다음 Twitter를 사용하여 Twitter 피드에 해당 비디오를 게시할 수 있습니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-the-connection-to-google-drive"></a>Google 드라이브에 대한 연결 만들기

논리 앱에 이 커넥터를 추가할 때 Google 드라이브에 연결할 권한을 논리 앱에 부여해야 합니다.

> [!INCLUDE [Steps to create a connection to googledrive](../../includes/connectors-create-api-googledrive.md)]

연결을 만든 후에 폴더 경로 또는 파일 이름 등의 Google 드라이브 속성을 입력합니다. 

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

커넥터의 Swagger 설명에서 설명 하는 트리거, 작업 및 제한에 대한 자세한 기술 정보는 [커넥터의 참조 페이지를 참조](/connectors/googledrive/)하세요.

## <a name="next-steps"></a>다음 단계

* 다른 [Logic Apps 커넥터](apis-list.md)에 대해 알아봅니다.
