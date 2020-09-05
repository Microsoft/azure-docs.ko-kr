---
title: 포함 파일
description: 포함 파일
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 02/14/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: 8901f107ca2af609059670a0b31184080849d2b6
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "87374840"
---
pattern.any 엔터티를 사용하면 엔터티의 표현이 발화의 나머지 부분에서 엔터티의 끝을 확인하기 어렵게 만드는 자유 형식 데이터를 찾을 수 있습니다.

인적 자원 앱은 직원이 회사 양식을 찾는 데 도움이 됩니다.

|발화|
|--|
|Where is **HRF-123456**?|
|Who authored **HRF-123234**?|
|**HRF-456098** is published in French?|

그러나 각 양식은 `Request relocation from employee new to the company 2018 version 5`과 같이 친숙한 이름 뿐만 아니라 이전 표에서 사용된 적절한 형식의 이름도 갖습니다.

친숙한 양식 이름을 갖는 발언은 다음과 같습니다.

|발화|
|--|
|Where is **Request relocation from employee new to the company 2018 version 5**?|
|Who authored **"Request relocation from employee new to the company 2018 version 5"** ?|
|**Request relocation from employee new to the company 2018 version 5** is published in French?|

다양한 길이에는 LUIS에서 엔터티가 끝나는 위치를 혼동할 수 있는 단어가 포함됩니다. 패턴에서 Pattern.any 엔터티를 사용하면 LUIS가 양식 이름을 올바르게 추출하도록 양식 이름의 시작 및 끝을 지정할 수 있습니다.

|템플릿 발화 예제|
|--|
|Where is {FormName}[?]|
|Who authored {FormName}[?]|
|{FormName} is published in French[?]|

Pattern.any [참조](../reference-entity-pattern-any.md) 정보 검토