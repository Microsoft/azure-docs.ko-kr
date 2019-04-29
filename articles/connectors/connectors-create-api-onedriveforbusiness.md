---
title: 비즈니스용 OneDrive에 연결 - Azure Logic Apps | Microsoft Docs
description: 비즈니스용 OneDrive REST API 및 Azure Logic Apps를 사용하여 파일 업로드 및 관리
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
ms.openlocfilehash: 87a162338cc67a3f2d2fc425bbcbefe2976661e5
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104921"
---
# <a name="get-started-with-the-onedrive-for-business-connector"></a>비즈니스용 OneDrive 커넥터 시작
비즈니스용 OneDrive에 연결하여 파일을 관리합니다. 파일에 대해 업로드, 업데이트, 가져오기 및 삭제와 같은 다양한 작업을 수행할 수 있습니다.

이제 논리 앱을 만들어 시작할 수 있습니다. [논리 앱 만들기](../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

## <a name="create-a-connection-to-onedrive-for-business"></a>비즈니스용 OneDrive에 대한 연결 만들기
비즈니스용 OneDrive로 논리 앱을 만들려면 먼저 **연결**을 만든 후에 다음 속성에 대한 세부 정보를 제공해야 합니다.

| 자산 | 필수 | 설명 |
| --- | --- | --- |
| 토큰 |예 |비즈니스용 OneDrive 자격 증명 제공 |

연결을 만든 후에 사용하여 작업을 실행하고 이 문서에 설명된 트리거에 대한 수신을 대기할 수 있습니다.

> [!INCLUDE [Steps to create a connection to OneDrive for Business](../../includes/connectors-create-api-onedriveforbusiness.md)]
> 

## <a name="connector-specific-details"></a>커넥터 관련 세부 정보

[커넥터 세부 정보](/connectors/onedriveforbusinessconnector/)에서 swagger에 정의된 모든 트리거 및 작업과 제한 사항도 확인할 수 있습니다.

## <a name="more-connectors"></a>추가 커넥터
[API 목록](apis-list.md)으로 돌아갑니다.