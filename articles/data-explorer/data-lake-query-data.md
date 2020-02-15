---
title: Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터 쿼리
description: Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터를 쿼리 하는 방법을 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77161752"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터 쿼리

Azure Data Lake Storage은 빅 데이터 분석을 위한 확장성이 뛰어나고 경제적인 data Lake 솔루션입니다. 고성능 파일 시스템의 강력한 기능을 광범위 하 고 경제적으로 결합 하 여 정보에 대 한 시간을 단축할 수 있습니다. Data Lake Storage Gen2는 Azure Blob Storage 기능을 확장 하며 분석 워크 로드에 최적화 되어 있습니다.
 
Azure 데이터 탐색기는 Azure Blob Storage 및 Azure Data Lake Storage (Gen1 및 Gen2)와 통합 되어, Lake의 데이터에 대해 빠르고, 캐시 되 고, 인덱싱된 액세스를 제공 합니다. Azure 데이터 탐색기에 사전 수집 하지 않고 lake에서 데이터를 분석 하 고 쿼리할 수 있습니다. 수집 및 uningested native lake 데이터를 동시에 쿼리할 수도 있습니다.  

> [!TIP]
> 최고의 쿼리 성능을 위해서는 Azure 데이터 탐색기에 데이터를 수집 해야 합니다. 이전 수집을 사용 하지 않고 외부 데이터를 쿼리 하는 기능은 기록 데이터 또는 거의 쿼리하지 않는 데이터에만 사용 해야 합니다. 최상의 결과를 위해 [lake에서 쿼리 성능을 최적화](#optimize-your-query-performance) 하세요.
 

## <a name="create-an-external-table"></a>외부 테이블 만들기

 > [!NOTE]
 > 현재 지원 되는 저장소 계정은 Azure Blob Storage 또는 Azure Data Lake Storage (Gen1 및 Gen2)입니다.

1. `.create external table` 명령을 사용 하 여 Azure 데이터 탐색기에서 외부 테이블을 만듭니다. `.show`, `.drop`및 `.alter`와 같은 추가 외부 테이블 명령은 [외부 테이블 명령](/azure/kusto/management/externaltables)에 설명 되어 있습니다.

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * 보다 세분화 된 분할을 통해 성능이 향상 됩니다. 예를 들어 일별 파티션이 있는 외부 테이블에 대 한 쿼리는 월별 분할 된 테이블이 있는 쿼리 보다 성능이 더 우수 합니다.
    > * 파티션이 있는 외부 테이블을 정의 하는 경우 저장소 구조가 동일할 것으로 예상 됩니다.
예를 들어 테이블이 yyyy/MM/dd 형식의 DateTime 파티션으로 정의 된 경우 (기본값) URI 저장소 파일 경로는 *container1/yyyy/mm/dd/all_exported_blobs*이어야 합니다. 
    > * 외부 테이블이 datetime 열에 의해 분할 된 경우에는 쿼리에서 닫힌 범위에 대 한 시간 필터를 항상 포함 합니다. 예를 들어 쿼리 `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)`는이 (열린 범위) 1 `ArchivedProducts | where Timestamp > ago(1h)`) 보다 잘 수행 되어야 합니다. 
    > * [지원 되](ingest-data-overview.md#supported-data-formats) 는 모든 수집 형식은 외부 테이블을 사용 하 여 쿼리할 수 있습니다.

1. 외부 테이블은 웹 UI의 왼쪽 창에 표시 됩니다.

    ![웹 UI의 외부 테이블](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Json 형식의 외부 테이블 만들기

Json 형식의 외부 테이블을 만들 수 있습니다. 자세한 내용은 [외부 테이블 명령](/azure/kusto/management/externaltables) 을 참조 하세요.

1. `.create external table` 명령을 사용 하 여 *Externaltablejson*이라는 테이블을 만듭니다.

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Json 형식에는 아래와 같이 열에 대 한 매핑을 만드는 두 번째 단계가 있습니다. 다음 쿼리에서 *mappingName*이라는 특정 json 매핑을 만듭니다.

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>외부 테이블 사용 권한
 
* 데이터베이스 사용자는 외부 테이블을 만들 수 있습니다. 테이블 작성자가 자동으로 테이블 관리자가 됩니다.
* 클러스터, 데이터베이스 또는 테이블 관리자는 기존 테이블을 편집할 수 있습니다.
* 모든 데이터베이스 사용자 또는 읽기 권한자는 외부 테이블을 쿼리할 수 있습니다.
 
## <a name="query-an-external-table"></a>외부 테이블 쿼리
 
외부 테이블을 쿼리하려면 `external_table()` 함수를 사용 하 고 테이블 이름을 함수 인수로 제공 합니다. 쿼리의 나머지 부분은 표준 Kusto 쿼리 언어입니다.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Intellisense는 현재 외부 테이블 쿼리에서 지원 되지 않습니다.

### <a name="query-an-external-table-with-json-format"></a>Json 형식의 외부 테이블 쿼리

Json 형식의 외부 테이블을 쿼리하려면 `external_table()` 함수를 사용 하 고 테이블 이름과 매핑 이름을 모두 함수 인수로 제공 합니다. 아래 쿼리에서 *mappingName* 을 지정 하지 않으면 이전에 만든 매핑이 사용 됩니다.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>외부 및 수집 데이터를 함께 쿼리

동일한 쿼리 내에서 외부 테이블과 수집 데이터 테이블을 모두 쿼리할 수 있습니다. Azure 데이터 탐색기, SQL server 또는 다른 원본에서 추가 데이터를 사용 하 여 외부 테이블을 [`join`](/azure/kusto/query/joinoperator) 하거나 [`union`](/azure/kusto/query/unionoperator) 있습니다. [`let( ) statement`](/azure/kusto/query/letstatement) 를 사용 하 여 외부 테이블 참조에 약식 이름을 할당할 수 있습니다.

아래 예제에서 *제품은* 수집 데이터 테이블이 고 *ArchivedProducts* 는 Azure Data Lake Storage Gen2 데이터를 포함 하는 외부 테이블입니다.

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>도움말 클러스터의 *TaxiRides* 외부 테이블 쿼리

*TaxiRides* 샘플 데이터 집합에는 [NYC Taxi 및 리무진 위원회](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)의 뉴욕 도시 taxi 데이터가 포함 되어 있습니다.

### <a name="create-external-table-taxirides"></a>Create external table *TaxiRides* 

> [!NOTE]
> 이 섹션에서는 *도움말* 클러스터에서 *TaxiRides* 외부 테이블을 만드는 데 사용 되는 쿼리를 보여 줍니다. 이 테이블은 이미 생성 되었으므로이 섹션을 건너뛰고 [쿼리 *TaxiRides* 외부 테이블 데이터](#query-taxirides-external-table-data)를 수행할 수 있습니다. 

1. 다음 쿼리는 도움말 클러스터에서 외부 테이블 *TaxiRides* 을 만드는 데 사용 되었습니다. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. 결과 테이블은 *도움말* 클러스터에 생성 됩니다.

    ![TaxiRides 외부 테이블](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Query *TaxiRides* 외부 테이블 데이터 

[https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) 에 로그인 하 여 *TaxiRides* 외부 테이블을 쿼리 합니다. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>분할 하지 않고 *TaxiRides* 외부 테이블 쿼리

외부 테이블 *TaxiRides* 에서 [이 쿼리를 실행](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) 하 여 전체 데이터 집합에서 각 요일의 탑승을 나타냅니다. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

이 쿼리는 가장 바쁜 요일을 보여 줍니다. 데이터가 분할 되지 않으므로이 쿼리는 결과를 반환 하는 데 시간이 오래 걸릴 수 있습니다 (최대 몇 분).

![분할 되지 않은 쿼리 렌더링](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>분할이 포함 된 TaxiRides 외부 테이블 쿼리 

2017 년 1 월에 사용 되는 taxi cab 형식 (노랑 또는 녹색)을 보여 주는 외부 테이블 *TaxiRides* 에서 [이 쿼리를 실행](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) 합니다. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

이 쿼리에서는 쿼리 시간 및 성능을 최적화 하는 분할을 사용 합니다. 분할 된 열 (pickup_datetime)에 대 한 쿼리를 필터링 하 고 몇 초 내에 결과를 반환 합니다.

![분할 된 쿼리 렌더링](media/data-lake-query-data/taxirides-with-partition.png)
  
외부 테이블 *TaxiRides* 실행 되는 추가 쿼리를 작성 하 고 데이터에 대해 자세히 알아볼 수 있습니다. 

## <a name="optimize-your-query-performance"></a>쿼리 성능 최적화

외부 데이터를 쿼리 하기 위한 다음 모범 사례를 사용 하 여 lake에서 쿼리 성능을 최적화 하세요. 
 
### <a name="data-format"></a>데이터 형식
 
다음 이후 분석 쿼리에 대해 칼럼 형식 형식을 사용 합니다.
* 쿼리와 관련 된 열만 읽을 수 있습니다. 
* 열 인코딩 기술을 통해 데이터 크기를 크게 줄일 수 있습니다.  
Azure 데이터 탐색기은 Parquet 및 ORC 칼럼 형식 형식을 지원 합니다. Parquet 형식은 구현에 최적화 되어 있으므로 제안 됩니다. 
 
### <a name="azure-region"></a>Azure 지역 
 
외부 데이터가 Azure 데이터 탐색기 클러스터와 동일한 Azure 지역에 상주 하는지 확인 합니다. 이렇게 하면 비용 및 데이터 인출 시간이 줄어듭니다.
 
### <a name="file-size"></a>파일 크기
 
최적의 파일 크기는 파일당 수백 메가바이트 (최대 1gb)입니다. 느린 파일 열거 프로세스 및 열 형식의 제한 된 사용과 같이 불필요 한 오버 헤드가 필요한 작은 파일은 많이 사용 하지 않습니다. 파일 수는 Azure 데이터 탐색기 클러스터의 CPU 코어 수보다 커야 합니다. 
 
### <a name="compression"></a>압축
 
원격 저장소에서 인출 되는 데이터의 양을 줄이려면 압축을 사용 합니다. Parquet format의 경우 열 그룹을 개별적으로 압축 하는 내부 Parquet 압축 메커니즘을 사용 합니다. 따라서 별도로 읽을 수 있습니다. 압축 메커니즘 사용의 유효성을 검사 하려면 파일의<filename>이름을 "release.tar.gz. parquet" 또는<filename>"snappy. parquet"와는 달리 "<filename>. parquet"와 대조 하 여 확인 합니다. 
 
### <a name="partitioning"></a>분할
 
쿼리가 관련이 없는 경로를 건너뛸 수 있도록 하는 "폴더" 파티션을 사용 하 여 데이터를 구성 합니다. 분할을 계획할 때 타임 스탬프 또는 테 넌 트 ID와 같은 쿼리의 파일 크기 및 일반 필터를 고려 합니다.
 
### <a name="vm-size"></a>VM 크기
 
더 많은 코어와 더 높은 네트워크 처리량을 포함 하는 VM Sku를 선택 합니다 (메모리는 중요 하지 않음). 자세한 내용은 [Azure 데이터 탐색기 클러스터에 올바른 VM SKU 선택](manage-cluster-choose-sku.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure 데이터 탐색기를 사용 하 여 Azure Data Lake에서 데이터를 쿼리 합니다. 쿼리를 [작성](write-queries.md) 하 고 데이터에서 추가 정보를 도출 하는 방법에 대해 알아봅니다.
