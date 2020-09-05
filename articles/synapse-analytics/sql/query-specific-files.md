---
title: 쿼리에서 파일 메타데이터 사용
description: OPENROWSET 함수는 파일 이름 및/또는 폴더 경로를 기반으로 데이터를 필터링하거나 분석하기 위해 쿼리에서 사용되는 모든 파일에 대한 파일 및 경로 정보를 제공합니다.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: sql
ms.date: 05/20/2020
ms.author: v-stazar
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 04a308805dd69c4d9245d287a391f1e7fa7d50f2
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055569"
---
# <a name="use-file-metadata-in-queries"></a>쿼리에서 파일 메타데이터 사용

[쿼리 폴더 및 여러 파일](query-folders-multiple-csv-files.md) 문서에 설명 된 대로 SQL 주문형은 여러 파일 및 폴더의 주소를 지정할 수 있습니다. 이 문서에서는 쿼리에서 파일 및 폴더 이름에 대한 메타데이터 정보를 사용하는 방법에 대해 알아봅니다.

경우에 따라 결과 집합의 특정 행과 상관 관계가 있는 파일 또는 폴더 원본을 알아야 할 수도 있습니다.

`filepath` 및 `filename` 함수를 사용하여 결과 집합의 파일 이름 및/또는 경로를 반환할 수 있습니다. 또는 파일 이름 및/또는 폴더 경로를 기반으로 데이터를 필터링하는 데 사용할 수 있습니다. 이러한 함수는 구문 섹션 [filename 함수](query-data-storage.md#filename-function) 및 [filepath 함수](query-data-storage.md#filepath-function)에 설명되어 있습니다. 아래에서 샘플을 따라 간단한 설명을 찾을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

첫 번째 단계는 스토리지 계정을 참조하는 데이터 원본을 통해 **데이터베이스를 만드는** 것입니다. 그런 다음, 해당 데이터베이스에서 [설치 스크립트](https://github.com/Azure-Samples/Synapse/blob/master/SQL/Samples/LdwSample/SampleDB.sql)를 실행하여 개체를 초기화합니다. 이 설치 스크립트는 이러한 샘플에서 사용되는 데이터 원본, 데이터베이스 범위 자격 증명 및 외부 파일 형식을 만듭니다.

## <a name="functions"></a>Functions

### <a name="filename"></a>파일 이름

이 함수는 행이 제공되는 파일 이름을 반환합니다.

다음 샘플에서는 2017년 마지막 3개월 동안의 NYC Yellow Taxi 데이터 파일을 읽고 파일당 탑승 수를 반환합니다. 쿼리의 OPENROWSET 부분은 읽을 파일을 지정합니다.

```sql
SELECT
    nyc.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM  
    OPENROWSET(
        BULK 'parquet/taxi/year=2017/month=9/*.parquet',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT='PARQUET'
    ) nyc
GROUP BY nyc.filename();
```

다음 예제에서는 WHERE 절에서 *filename()* 을 사용하여 읽을 파일을 필터링하는 방법을 보여줍니다. 쿼리의 OPENROWSET 부분에 있는 전체 폴더에 액세스하고 WHERE 절의 파일을 필터링합니다.

결과는 이전 예제와 동일합니다.

```sql
SELECT
    r.filename() AS [filename]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
    BULK 'csv/taxi/yellow_tripdata_2017-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2) 
        WITH (C1 varchar(200) ) AS [r]
WHERE
    r.filename() IN ('yellow_tripdata_2017-10.csv', 'yellow_tripdata_2017-11.csv', 'yellow_tripdata_2017-12.csv')
GROUP BY
    r.filename()
ORDER BY
    [filename];
```

### <a name="filepath"></a>Filepath

filepath 함수는 전체 또는 부분 경로를 반환합니다.

- 매개 변수 없이 호출되면 행이 제공되는 전체 파일 경로를 반환합니다.
- 매개 변수를 사용하여 호출하면 매개 변수에 지정된 위치에 있는 와일드카드와 일치하는 경로의 일부를 반환합니다. 예를 들어 매개 변수 값 1은 첫 번째 와일드카드와 일치하는 경로의 일부를 반환합니다.

다음 샘플에서는 2017년 마지막 3개월 동안의 NYC Yellow Taxi 데이터 파일을 읽습니다. 파일 경로당 탑승 수를 반환합니다. 쿼리의 OPENROWSET 부분은 읽을 파일을 지정합니다.

```sql
SELECT
    r.filepath() AS filepath
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_2017-1*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',
        FIRSTROW = 2
    )
    WITH (
        vendor_id INT
    ) AS [r]
GROUP BY
    r.filepath()
ORDER BY
    filepath;
```

다음 예제에서는 WHERE 절에서 *filepath()* 를 사용하여 읽을 파일을 필터링하는 방법을 보여줍니다.

쿼리의 OPENROWSET 부분에 있는 와일드카드를 사용하고 WHERE 절의 파일을 필터링할 수 있습니다. 결과는 이전 예제와 동일합니다.

```sql
SELECT
    r.filepath() AS filepath
    ,r.filepath(1) AS [year]
    ,r.filepath(2) AS [month]
    ,COUNT_BIG(*) AS [rows]
FROM OPENROWSET(
        BULK 'csv/taxi/yellow_tripdata_*-*.csv',
        DATA_SOURCE = 'SqlOnDemandDemo',
        FORMAT = 'CSV',
        PARSER_VERSION = '2.0',        
        FIRSTROW = 2
    )
WITH (
    vendor_id INT
) AS [r]
WHERE
    r.filepath(1) IN ('2017')
    AND r.filepath(2) IN ('10', '11', '12')
GROUP BY
    r.filepath()
    ,r.filepath(1)
    ,r.filepath(2)
ORDER BY
    filepath;
```

## <a name="next-steps"></a>다음 단계

다음 문서에서는 [Parquet 파일을 쿼리](query-parquet-files.md)하는 방법에 대해 알아봅니다.
