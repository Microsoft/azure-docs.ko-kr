---
title: 데이터 흐름 매핑의 집계 변환
description: 매핑 데이터 흐름 집계 변환을 사용 하 여 Azure Data Factory에서 대규모로 데이터를 집계 하는 방법에 대해 알아봅니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 74b96bf2cac0de7c57e496c637f2e3ef549eb61f
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930467"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 집계 변환 

집계 변환은 데이터 스트림에서 열의 집계를 정의 합니다. 식 작성기를 사용 하면 SUM, MIN, MAX 및 COUNT와 같은 여러 유형의 집계를 기존 또는 계산 열로 그룹화 하 여 정의할 수 있습니다.

## <a name="group-by"></a>Group By

기존 열을 선택 하거나 집계에 대해 group by 절로 사용할 새 계산 열을 만듭니다. 기존 열을 사용 하려면 드롭다운에서 해당 열을 선택 합니다. 새 계산 열을 만들려면 절 위로 마우스를 이동 하 고 **계산 열**을 클릭 합니다. [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)가 열립니다. 계산 열을 만든 후에는 **이름** 필드에 출력 열 이름을 입력 합니다. 추가 group by 절을 추가 하려는 경우 기존 절을 마우스로 가리키고 더하기 아이콘을 클릭 합니다.

![설정 별 집계 변환 그룹](media/data-flow/agg.png "설정 별 집계 변환 그룹")

Group by 절은 집계 변환에서 선택 사항입니다.

## <a name="aggregate-column"></a>집계 열 

집계 **탭으로 이동 하 여 집계** 식을 작성 합니다. 기존 열을 집계로 덮어쓰거나 새 이름을 사용 하 여 새 필드를 만들 수 있습니다. 열 이름 선택기 옆의 오른쪽 상자에 집계 식이 입력 됩니다. 식을 편집 하려면 입력란을 클릭 하 여 식 작성기를 엽니다. 집계를 더 추가 하려면 기존 식 위로 마우스를 이동 하 고 더하기 아이콘을 클릭 하 여 새 집계 열 또는 [열 패턴](concepts-data-flow-column-pattern.md)을 만듭니다.

각 집계 식에는 집계 함수가 하나 이상 포함 되어야 합니다.

![집계 변환 집계 설정](media/data-flow/agg2.png "집계 변환 집계 설정")


> [!NOTE]
> 디버그 모드에서 식 작성기는 집계 함수를 사용 하 여 데이터 미리 보기를 생성할 수 없습니다. 집계 변환에 대한 데이터 미리 보기를 보려면 식 작성기를 닫고 ' 데이터 미리 보기 ' 탭을 통해 데이터를 확인 합니다.

## <a name="reconnect-rows-and-columns"></a>행 및 열 다시 연결

집계 변환은 SQL 집계 select 쿼리와 유사 합니다. Group by 절 또는 집계 함수에 포함 되지 않은 열은 집계 변환의 출력으로 이동 하지 않습니다. 집계 된 출력에 다른 열을 포함 하려는 경우 다음 방법 중 하나를 수행 합니다.

* `last()` 또는 `first()`와 같은 집계 함수를 사용 하 여 추가 열을 포함 합니다.
* [자체 조인 패턴](https://mssqldude.wordpress.com/2018/12/20/adf-data-flows-self-join/)을 사용 하 여 열을 출력 스트림에 다시 조인 합니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    aggregate(
           groupBy(
                <groupByColumnName> = <groupByExpression1>,
                <groupByExpression2>
               ),
           <aggregateColumn1> = <aggregateExpression1>,
           <aggregateColumn2> = <aggregateExpression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <aggregateTransformationName>
```

### <a name="example"></a>예제

아래 예제에서는 `MoviesYear` 들어오는 스트림을 사용 하 여 열 `year`행을 그룹화 합니다. 이 변환은 열 `Rating`의 평균으로 계산 되는 집계 열 `avgrating`를 만듭니다. 이 집계 변환의 이름은 `AvgComedyRatingsByYear`입니다.

Data Factory UX에서이 변환은 아래 이미지와 같습니다.

![예제 별 그룹화](media/data-flow/agg-script1.png "예제 별 그룹화")

![집계 예제](media/data-flow/agg-script2.png "집계 예제")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
MoviesYear aggregate(
                groupBy(year),
                avgrating = avg(toInteger(Rating))
            ) ~> AvgComedyRatingByYear
```

## <a name="next-steps"></a>다음 단계

* [창 변환을](data-flow-window.md) 사용 하 여 창 기반 집계 정의
