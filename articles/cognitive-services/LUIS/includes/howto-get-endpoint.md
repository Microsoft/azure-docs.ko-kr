---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.date: 05/06/2020
ms.subservice: language-understanding
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 01d102de24185eba789e041d8534d6fdce8c260b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "87374731"
---
**관리** 섹션(오른쪽 위 메뉴)의 **Azure Resources** 페이지(왼쪽 메뉴)에서 **예제 쿼리** URL을 복사한 다음, 새 브라우저 탭에 붙여넣습니다.

엔드포인트 URL은 APP-ID 및 앱 KEY-ID를 대체하는 고유한 사용자 지정 하위 도메인, 앱 ID 및 엔드포인트 키를 사용하여 다음과 같은 형식으로 표시됩니다.

```console
https://YOUR-CUSTOM-SUBDOMAIN.api.cognitive.microsoft.com/luis/prediction/v3.0/apps/APP-ID/slots/production/predict?subscription-key=KEY-ID&verbose=true&show-all-intents=true&log=true&query=YOUR_QUERY_HERE
```