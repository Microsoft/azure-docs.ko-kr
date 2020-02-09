---
title: 매핑 데이터 흐름의 필터 변환
description: Azure Data Factory 매핑 데이터 흐름에서 필터 변환을 사용 하 여 행을 필터링 합니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: 84b935d1646ea4f13d5926fc68797fd8efd03dea
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930365"
---
# <a name="filter-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 필터 변환

필터 변환은 조건에 따라 행 필터링을 허용 합니다. 출력 스트림에는 필터링 조건과 일치 하는 모든 행이 포함 됩니다. 필터 변환은 SQL의 WHERE 절과 유사 합니다.

## <a name="configuration"></a>구성

데이터 흐름 식 작성기를 사용 하 여 필터 조건에 대한 식을 입력 합니다. 식 작성기를 열려면 파란색 상자를 클릭 합니다. 필터 조건은 부울 유형 이어야 합니다. 식을 만드는 방법에 대한 자세한 내용은 [식 작성기](concepts-data-flow-expression-builder.md) 설명서를 참조 하세요.

![필터 변환](media/data-flow/filter1.png "필터 변환")

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    filter(
        <conditionalExpression>
    ) ~> <filterTransformationName>
```

### <a name="example"></a>예제

아래 예제는 들어오는 스트림 `CleanData`를 사용 하는 `FilterBefore1960` 라는 필터 변환입니다. 필터 조건은 `year <= 1960`식입니다.

Data Factory UX에서이 변환은 아래 이미지와 같습니다.

![필터 변환](media/data-flow/filter1.png "필터 변환")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
CleanData
    filter(
        year <= 1960
    ) ~> FilterBefore1960

```

## <a name="next-steps"></a>다음 단계

[Select 변환](data-flow-select.md) 으로 열 필터링
