---
title: 포함 파일
description: 포함 파일
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761725"
---
### <a name="the-data-model-of-the-schema"></a>스키마의 데이터 모델

| 필드 | 데이터 형식 | Description |
| ---- | ---- | ---- |
| **AdditionalData** | 동적 | 경고 개수, 책갈피 수, 주석 수, 경고 제품 이름 및 전술 |
| **AlertIds** | 동적 | 인시던트가 생성 된 경고 |
| **책갈피 Id** | 동적 | 책갈피를 추가한 엔터티 |
| **분류** | 문자열 | 인시던트 종료 분류 |
| **ClassificationComment** | 문자열 | 인시던트 종료 분류 주석 |
| **ClassificationReason** | 문자열 | 문제를 닫은 분류 이유 |
| **Closed 시간** | Datetime | 인시던트가 마지막으로 종결 된 시간에 대 한 타임 스탬프 (UTC)입니다. |
| **설명** | 동적 | 인시던트 설명 |
| **CreatedTime** | Datetime | 인시던트가 생성 된 시간에 대 한 타임 스탬프 (UTC) |
| **설명** | string | 문제 설명 |
| **FirstActivityTime** | Datetime | 첫 번째 이벤트 시간 |
| **FirstModifiedTime** | Datetime | 인시던트가 처음 수정 된 시간에 대 한 타임 스탬프 (UTC) |
| **인시던트 이름** | 문자열 | 내부 GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | 문자열 | 인시던트에 연결 |
| **레이블** | 동적 | 태그들 |
| **LastActivityTime** | Datetime | 마지막 이벤트 시간 |
| **LastModifiedTime** | Datetime | 인시던트가 마지막으로 수정 된 시간에 대 한 타임 스탬프 (UTC)입니다. <br>(현재 레코드에서 설명 하는 수정 내용) |
| **ModifiedBy** | 문자열 | 인시던트를 수정한 사용자 또는 시스템 |
| **소유자** | 동적 |  |
| **RelatedAnalyticRuleIds** | 동적 | 인시던트의 경고가 트리거된 규칙 |
| **심각도** | 문자열 | 인시던트의 심각도 (높음/보통/낮음/정보) |
| **SourceSystem** | 문자열 | 상수 (' Azure ') |
| **상태** | 문자열 |  |
| **TenantId** | 문자열 |  |
| **TimeGenerated** | Datetime | 현재 레코드를 만든 시간에 대 한 타임 스탬프 (UTC)입니다. <br>(인시던트를 수정할 때) |
| **제목** | 문자열 | 
| **형식** | 문자열 | 상수 (' SecurityIncident ') |
|
