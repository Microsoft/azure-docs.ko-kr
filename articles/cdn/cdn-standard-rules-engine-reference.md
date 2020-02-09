---
title: Azure CDN에 대한 표준 규칙 엔진 참조 | Microsoft Docs
description: Azure Content Delivery Network (Azure CDN)에 대한 표준 규칙 엔진의 일치 조건 및 작업에 대한 참조 설명서입니다.
services: cdn
author: mdgattuso
ms.service: azure-cdn
ms.topic: article
ms.date: 11/01/2019
ms.author: magattus
ms.openlocfilehash: aa401150ee7a0f02e809ad702b8247e18081c8a3
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74171556"
---
# <a name="standard-rules-engine-reference-for-azure-cdn"></a>Azure CDN에 대한 표준 규칙 엔진 참조

Azure Content Delivery Network (Azure CDN)에 대한 [표준 규칙 엔진](cdn-standard-rules-engine.md) 에서 규칙은 하나 이상의 일치 조건 및 동작으로 구성 됩니다. 이 문서에서는 Azure CDN에 대한 표준 규칙 엔진에서 사용할 수 있는 일치 조건 및 기능에 대해 자세히 설명 합니다.

규칙 엔진은 표준 Azure CDN에서 특정 유형의 요청을 처리 하는 방법에 대한 최종 권한으로 설계 되었습니다.

**규칙의 일반적인 용도**는 다음과 같습니다.

- 사용자 지정 캐시 정책을 재정의하거나 정의합니다.
- 요청을 리디렉션합니다.
- HTTP 요청 및 응답 헤더를 수정 합니다.

## <a name="terminology"></a>용어

규칙 엔진에서 규칙을 정의 하려면 [일치 조건](cdn-standard-rules-engine-match-conditions.md) 및 [작업](cdn-standard-rules-engine-actions.md)을 설정 합니다.

 ![Azure CDN 규칙 구조](./media/cdn-standard-rules-engine-reference/cdn-rules-structure.png)

각 규칙에는 최대 4 개의 일치 조건 및 세 개의 작업이 있을 수 있습니다. 각 Azure CDN 엔드포인트은 최대 5 개의 규칙을 포함할 수 있습니다. 

Azure CDN 엔드포인트에 대한 현재 5 개 규칙 제한에 포함 된 기본 *전역 규칙*입니다. 전역 규칙에 일치 하는 조건이 없으며 전역 규칙에 정의 된 동작이 항상 트리거됩니다.

## <a name="syntax"></a>구문

규칙에서 특수 문자를 처리 하는 방법은 서로 다른 일치 조건 및 작업에서 텍스트 값을 처리 하는 방법에 따라 달라 집니다. 일치 조건 또는 작업은 다음 방법 중 하나로 텍스트를 해석할 수 있습니다.

- [리터럴 값](#literal-values)
- [와일드 카드 값](#wildcard-values)


### <a name="literal-values"></a>리터럴 값

리터럴 값으로 해석 되는 텍스트는 규칙에서 일치 해야 하는 값의 일부로 *% 기호를 제외한* 모든 특수 문자를 처리 합니다. 예를 들어 `'*'`로 설정 된 리터럴 일치 조건은 정확한 값 `'*'` 발견 된 경우에만 충족 됩니다.

백분율 기호는 URL 인코딩을 나타내는 데 사용 됩니다 (예: `%20`).

### <a name="wildcard-values"></a>와일드카드 값

와일드 카드 값으로 해석 되는 텍스트는 특수 문자에 추가적인 의미를 할당 합니다. 다음 표에서는 표준 규칙 엔진에서 특정 특수 문자를 해석 하는 방법을 설명 합니다.

문자 | 설명
----------|------------
\ | 백슬래시는 이 테이블에 지정된 문자를 이스케이프하는 데 사용됩니다. 백슬래시는 이스케이프해야 하는 특수 문자 바로 앞에 지정되어야 합니다. 예를 들어 다음 구문은 별표를 이스케이프합니다.`\*`
% | 백분율 기호는 URL 인코딩을 나타내는 데 사용 됩니다 (예: `%20`).
\* | 별표는 하나 이상의 문자를 나타내는 와일드카드입니다.
공간 | 공백 문자는 지정 된 값 또는 패턴 중 하나에서 일치 조건을 만족할 수 있음을 나타냅니다.
작은따옴표 | 작은따옴표는 특별 한 의미가 없습니다. 그러나 작은따옴표 집합은 값을 리터럴 값으로 처리 해야 함을 나타냅니다. 작은따옴표는 다음과 같은 방법으로 사용할 수 있습니다.<ul><li>지정 된 값이 비교 값의 부분과 일치할 때마다 일치 조건을 충족 시킬 수 있도록 합니다.  예를 들어 `'ma'`는 다음 문자열 중 하나와 일치합니다. <ul><li>/business/**ma**rathon/asset.htm</li><li>**ma**p.gif</li><li>/business/template.**ma**p</li></ul><li>특수 문자를 리터럴 문자로 지정할 수 있습니다. 예를 들어 공백 문자를 작은따옴표 (`' '` 또는 `'<sample value>'`) 집합으로 묶어 리터럴 공백 문자를 지정할 수 있습니다.</li><li>빈 값을 지정할 수 있도록 하려면입니다. 작은따옴표 ( **' '** ) 집합을 지정 하 여 빈 값을 지정 합니다.</li></ul>**중요**:<br /><ul><li>지정 된 값이 와일드 카드를 포함 하지 않으면 값이 자동으로 리터럴 값으로 간주 됩니다. 리터럴 값에 대한 작은따옴표 집합을 지정할 필요가 없습니다.</li><li>이 테이블의 다른 문자를 이스케이프 하는 데 백슬래시가 사용 되지 않는 경우에는 백슬래시를 작은따옴표 집합에 지정할 때 무시 됩니다.</li><li>특수 문자를 리터럴 문자로 지정 하는 또 다른 방법은 백슬래시 (`\`)를 사용 하 여 이스케이프 하는 것입니다.</li></ul>

## <a name="next-steps"></a>다음 단계

- [표준 규칙 엔진의 일치 조건](cdn-standard-rules-engine-match-conditions.md)
- [표준 규칙 엔진의 작업](cdn-standard-rules-engine-actions.md)
- [표준 규칙 엔진을 사용 하 여 HTTPS 적용](cdn-standard-rules-engine.md)
- [Azure CDN 개요](cdn-overview.md)
