---
title: 매핑 데이터 흐름의 조인 변환
description: Azure Data Factory 매핑 데이터 흐름에서 조인 변환을 사용 하 여 두 데이터 소스의 데이터를 결합 합니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 10149c6eb06e6d2994233aa365f237e6d9330c48
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75644762"
---
# <a name="join-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 조인 변환

조인 변환을 사용 하 여 매핑 데이터 흐름에서 두 원본 또는 스트림의 데이터를 결합할 수 있습니다. 출력 스트림은 조인 조건에 따라 일치 하는 두 소스의 모든 열을 포함 합니다. 

## <a name="join-types"></a>조인 유형

데이터 흐름 매핑은 현재 5 개의 서로 다른 조인 형식을 지원 합니다.

### <a name="inner-join"></a>내부 조인

내부 조인은 두 테이블에 일치 하는 값을 가진 행만 출력 합니다.

### <a name="left-outer"></a>왼쪽 우선 외부

왼쪽 우선 외부 조인은 왼쪽 스트림의 모든 행과 오른쪽 스트림의 일치 레코드를 반환 합니다. 왼쪽 스트림의 행에 일치 하는 항목이 없으면 오른쪽 스트림의 출력 열이 NULL로 설정 됩니다. 출력은 내부 조인에서 반환 되는 행과 왼쪽 스트림의 일치 하지 않는 행을 더한 값입니다.

> [!NOTE]
> 데이터 흐름에서 사용 하는 Spark 엔진은 조인 조건에서 때때로 카티션 곱을 가능 하 게 합니다. 이 경우 사용자 지정 크로스 조인으로 전환 하 고 조인 조건을 수동으로 입력할 수 있습니다. 이로 인해 실행 엔진이 관계 양쪽의 모든 행을 계산 하 고 행을 필터링 해야 하는 경우 데이터 흐름의 성능이 저하 될 수 있습니다.

### <a name="right-outer"></a>오른쪽 우선 외부

오른쪽 우선 외부 조인은 오른쪽 스트림의 모든 행과 일치 하는 레코드를 왼쪽 스트림에서 반환 합니다. 오른쪽 스트림의 행에 일치 하는 항목이 없으면 왼쪽 스트림의 출력 열이 NULL로 설정 됩니다. 출력은 내부 조인에서 반환 되는 행과 오른쪽 스트림의 일치 하지 않는 행을 더한 값입니다.

### <a name="full-outer"></a>완전 외부

Full outer join은 양쪽의 모든 열과 행을 일치 하지 않는 열에 대한 NULL 값으로 출력 합니다.

### <a name="custom-cross-join"></a>사용자 지정 크로스 조인

크로스 조인은 조건에 따라 두 스트림의 교차곱을 출력 합니다. 같지 않은 조건을 사용 하는 경우 사용자 지정 식을 크로스 조인 조건으로 지정 합니다. 출력 스트림은 조인 조건을 충족 하는 모든 행이 됩니다.

비 동등 조인과 ```OR``` 조건에이 조인 유형을 사용할 수 있습니다.

전체 카티션 곱을 명시적으로 생성 하려면 조인 앞에 있는 두 개의 독립 스트림에서 파생 열 변환을 사용 하 여 일치 시킬 가상 키를 만들어야 합니다. 예를 들어 ```SyntheticKey``` 이라는 각 스트림의 파생 열에 새 열을 만들고 ```1```같도록 설정 합니다. 그런 다음 사용자 지정 조인 식으로 ```a.SyntheticKey == b.SyntheticKey```를 사용 합니다.

> [!NOTE]
> 사용자 지정 크로스 조인에서 왼쪽 및 오른쪽 관계의 양쪽에 있는 하나 이상의 열을 포함 해야 합니다. 각 측의 열 대신 정적 값을 사용 하 여 크로스 조인을 실행 하면 전체 데이터 집합이 전체 검색 되어 데이터 흐름이 제대로 수행 되지 않습니다.

## <a name="configuration"></a>구성

1. **오른쪽 스트림** 드롭다운에서 조인 하는 데이터 스트림을 선택 합니다.
1. **조인 유형** 선택
1. 조인 조건에 대해 일치 시킬 키 열을 선택 합니다. 기본적으로 데이터 흐름은 각 스트림의 한 열이 같은지 검색 합니다. 계산 된 값을 통해 비교 하려면 열 드롭다운을 마우스로 가리키고 **계산 열**을 선택 합니다.

![조인 변환](media/data-flow/join.png "Join")

## <a name="optimizing-join-performance"></a>조인 성능 최적화

SSIS와 같은 도구의 병합 조인과 달리 조인 변환은 필수 병합 조인 작업이 아닙니다. 조인 키는 정렬할 필요가 없습니다. 조인 작업은 Spark의 최적 조인 작업 (브로드캐스트 또는 맵 쪽 조인)을 기반으로 수행 됩니다.

![조인 변환 최적화](media/data-flow/joinoptimize.png "조인 최적화")

데이터 스트림 중 하나 또는 둘 모두가 작업자 노드 메모리에 맞는 경우 최적화 탭에서 **브로드캐스트** 를 사용 하도록 설정 하 여 성능을 최적화 합니다. 또한 작업자 당 메모리에 더 잘 맞도록 조인 작업에 대한 데이터를 다시 분할할 수도 있습니다.

## <a name="self-join"></a>셀프 조인

데이터 스트림을 자체 조인 하려면 select 변환이 있는 기존 스트림에 별칭을 추가 합니다. 변환 옆에 있는 더하기 아이콘을 클릭 하 고 **새 분기**를 선택 하 여 새 분기를 만듭니다. 원본 스트림에 별칭을 추가 하려면 선택 변환을 추가 합니다. 조인 변환을 추가 하 고 원본 스트림을 **왼쪽 스트림으로** 선택 하 고 선택 변환을 **오른쪽 스트림으로**선택 합니다.

![자체 조인](media/data-flow/selfjoin.png "자체 조인")

## <a name="testing-join-conditions"></a>조인 조건 테스트

디버그 모드에서 데이터 미리 보기를 사용 하 여 조인 변환을 테스트할 때 작은 알려진 데이터 집합을 사용 합니다. 대량 데이터 집합에서 행을 샘플링 하는 경우 테스트를 위해 읽을 행과 키를 예측할 수 없습니다. 결과는 명확 하지 않습니다. 즉, 조인 조건이 일치 하는 항목을 반환 하지 않을 수 있습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>내부 조인 예제

아래 예제는 왼쪽 스트림 `TripData` 및 오른쪽 스트림 `TripFare`을 사용 하는 `JoinMatchedData` 라는 조인 변환입니다.  조인 조건은 각 스트림의 `hack_license`, `medallion`, `vendor_id`및 `pickup_datetime` 열이 일치 하는 경우 true를 반환 하는 식 `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}`입니다. `joinType`이 `'inner'`인 경우 `broadcast` `'left'`값을 가질 수 있도록 왼쪽 스트림에만 브로드캐스팅을 사용 하도록 설정 하 고 있습니다.

Data Factory UX에서이 변환은 아래 이미지와 같습니다.

![조인 예제](media/data-flow/join-script1.png "조인 예제")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>사용자 지정 크로스 조인 예제

아래 예제는 왼쪽 스트림 `LeftStream` 및 오른쪽 스트림 `RightStream`을 사용 하는 `JoiningColumns` 라는 조인 변환입니다. 이 변환은 열 `leftstreamcolumn`이 `rightstreamcolumn`열 보다 큰 모든 행을 결합 하 여 두 개의 스트림과 조인을 사용 합니다. `joinType`이 `cross`인 경우 브로드캐스트를 사용할 수 없으며 값 `'none'``broadcast` 있습니다.

Data Factory UX에서이 변환은 아래 이미지와 같습니다.

![조인 예제](media/data-flow/join-script2.png "조인 예제")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>다음 단계

데이터를 조인한 후 [파생 열](data-flow-derived-column.md) 을 만들고 데이터를 대상 데이터 저장소로 [싱크](data-flow-sink.md) 합니다.
