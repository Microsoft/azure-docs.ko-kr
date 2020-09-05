---
title: 포함 파일
description: 포함 파일
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 08/07/2020
ms.author: dacoulte
ms.openlocfilehash: 9fcaca3f89217f649eb970ec70514a5735222782
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854103"
---
Azure Policy의 각 개체 형식에 대한 최대 수가 있습니다. _범위_ 항목은 구독 또는 [관리 그룹](../articles/governance/management-groups/overview.md)을 의미합니다.

| Where | 대상 | 최대 수 |
|---|---|---|
| 범위 | 정책 정의 | 500 |
| 범위 | 이니셔티브 정의 | 200 |
| 테넌트 | 이니셔티브 정의 | 2,500 |
| 범위 | 정책 또는 이니셔티브 할당 | 200 |
| 정책 정의 | 매개 변수 | 20 |
| 이니셔티브 정의 | 정책 | 1000 |
| 이니셔티브 정의 | 매개 변수 | 100 |
| 정책 또는 이니셔티브 할당 | 제외(notScopes) | 400 |
| 정책 규칙 | 중첩된 조건부 | 512 |
| 수정 작업 | 리소스 | 500 |
