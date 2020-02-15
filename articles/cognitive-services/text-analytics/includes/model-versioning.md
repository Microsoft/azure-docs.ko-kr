---
title: 모델 버전 관리
titleSuffix: Azure Cognitive Services
description: V3 엔드포인트에서 모델 버전 지정
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/06/2020
ms.author: aahi
ms.openlocfilehash: 4cd8d0901ce23fb227bb6919dee18f5aa3d063ed
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77089072"
---
Text Analytics API 버전 3을 사용하면 데이터에 적합한 최신 모델 버전을 선택할 수 있습니다. 선택적 `model-version` 매개 변수를 사용하여 요청에 필요한 모델 버전을 선택합니다. 이 매개 변수를 지정하지 않으면 API에서 기본적으로 안정적인 최신 버전인 `latest`를 설정합니다. 모든 요청에서 최신 모델 버전을 사용할 수 있지만 각 버전에서는 일부 기능만 업데이트됩니다. 아래 표에는 각 모델 버전에서 업데이트된 기능이 나와 있습니다.

| 모델 버전           | 업데이트된 기능         | 최신 버전의 기능           |
|-------------------------|--------------------------|--------------------------|
| `2020-02-01`            | 엔터티 인식                      | 엔터티 인식                      |
| `2019-10-01`            | 엔터티 인식, 감정 분석  | 언어 감지, 핵심 구 추출, 감정 분석|


V3 엔드포인트의 각 응답에는 사용된 모델 버전을 지정하는 `model-version` 필드가 포함됩니다.

```json
{
    "documents": […]
    "errors": []
    "model-version": "2019-10-01"
}
```
이러한 모델 버전의 업데이트에 대한 자세한 내용은 [새로운 기능](../whats-new.md)을 참조하세요.
