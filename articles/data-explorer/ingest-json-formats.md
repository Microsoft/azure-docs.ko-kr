---
title: JSON 형식의 데이터를 Azure 데이터 탐색기에 수집
description: JSON 형식의 데이터를 Azure 데이터 탐색기에 수집 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: kerend
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d293b76e004d693813a074cb8551a86cb3c0bec2
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76772343"
---
# <a name="ingest-json-formatted-sample-data-into-azure-data-explorer"></a>JSON 형식의 샘플 데이터를 Azure 데이터 탐색기 수집

이 문서에서는 JSON 형식의 데이터를 Azure 데이터 탐색기 데이터베이스에 수집 하는 방법을 보여 줍니다. 원시 및 매핑된 JSON의 간단한 예제를 시작 하 고, 여러 줄로 된 JSON을 계속 진행 하 고, 배열 및 사전을 포함 하는 더 복잡 한 JSON 스키마를 다룰 수 있습니다.  이 예에서는 KQL (Kusto query language), C#또는 Python을 사용 하 여 JSON 형식의 데이터를 수집 하는 프로세스를 자세히 설명 합니다. Kusto 쿼리 언어 `ingest` 제어 명령은 엔진 엔드포인트에 직접 실행 됩니다. 프로덕션 시나리오에서 수집은 클라이언트 라이브러리 또는 데이터 연결을 사용 하 여 데이터 관리 서비스에 대해 실행 됩니다. Azure [데이터 탐색기 Python 라이브러리를 사용 하 여 데이터 수집](/azure/data-explorer/python-ingest-data) 을 읽고 [AZURE 데이터 탐색기 .NET Standard SDK를 사용 하 여 데이터를 수집 하 여](/azure/data-explorer/net-standard-ingest-data) 이러한 클라이언트 라이브러리와의 수집 데이터에 대한 연습을 진행 합니다.

## <a name="prerequisites"></a>필수 조건

[테스트 클러스터 및 데이터베이스](create-cluster-database-portal.md)

## <a name="the-json-format"></a>JSON 형식

Azure 데이터 탐색기는 다음과 같은 두 가지 JSON 파일 형식을 지원 합니다.
* `json`: 줄로 구분 된 JSON. 입력 데이터의 각 줄에는 정확히 하나의 JSON 레코드가 있습니다.
* `multijson`: 여러 줄로 이루어진 JSON. 파서는 줄 구분 기호를 무시 하 고 이전 위치에서 유효한 JSON 끝 까지의 레코드를 읽습니다.

### <a name="ingest-and-map-json-formatted-data"></a>JSON 형식의 데이터 수집 및 매핑

JSON 형식 데이터를 수집 하려면 수집 [속성](/azure/kusto/management/data-ingestion/index#ingestion-properties)을 사용 하 여 *형식을* 지정 해야 합니다. JSON 데이터를 수집 하려면 [매핑이](/azure/kusto/management/mappings)필요 하며,이는 json 원본 항목을 대상 열에 매핑합니다. 수집 데이터를 사용 하는 경우 미리 정의 된 `jsonMappingReference` 수집 속성을 사용 하거나 `jsonMapping`수집 속성을 지정 합니다. 이 문서에서는 수집에 사용 되는 테이블에 미리 정의 된 `jsonMappingReference` 수집 속성을 사용 합니다. 아래 예제에서는 JSON 레코드를 단일 열 테이블에 원시 데이터로 수집 하 여 시작 합니다. 그런 다음 매핑을 사용 하 여 각 속성을 매핑된 열에 수집 합니다. 

### <a name="simple-json-example"></a>간단한 JSON 예

다음 예는 단순 구조를 포함 하는 간단한 JSON입니다. 데이터에는 여러 장치에서 수집 된 온도 및 습도 정보가 있습니다. 각 레코드에는 ID 및 타임 스탬프가 표시 됩니다.

```json
{
    "timestamp": "2019-05-02 15:23:50.0369439",
    "deviceId": "2945c8aa-f13e-4c48-4473-b81440bb5ca2",
    "messageId": "7f316225-839a-4593-92b5-1812949279b3",
    "temperature": 31.0301639051317,
    "humidity": 62.0791099602725
}
```

## <a name="ingest-raw-json-records"></a>원시 JSON 레코드 수집 

이 예에서는 JSON 레코드를 원시 데이터로 단일 열 테이블에 수집 합니다. 데이터 조작, 쿼리 사용 및 업데이트 정책은 데이터가 수집 된 후에 수행 됩니다.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

Kusto 쿼리 언어를 사용 하 여 원시 JSON 형식으로 데이터를 수집 합니다.

1. [https://dataexplorer.azure.com](https://dataexplorer.azure.com)에 로그인합니다.

1. **클러스터 추가**를 선택합니다.

1. **클러스터 추가** 대화 상자에 `https://<ClusterName>.<Region>.kusto.windows.net/` 형식으로 클러스터 URL을 입력하고 **추가**를 선택합니다.

1. 다음 명령을 붙여넣고 **실행** 을 선택 하 여 테이블을 만듭니다.

    ```Kusto
    .create table RawEvents (Event: dynamic)
    ```

    이 쿼리는 [동적](/azure/kusto/query/scalar-data-types/dynamic) 데이터 형식의 단일 `Event` 열이 있는 테이블을 만듭니다.

1. JSON 매핑을 만듭니다.

    ```Kusto
    .create table RawEvents ingestion json mapping 'RawEventMapping' '[{"column":"Event","path":"$"}]'
    ```

    이 명령은 매핑을 만들고 JSON 루트 경로 `$`를 `Event` 열에 매핑합니다.

1. 데이터를 `RawEvents` 테이블로 수집 합니다.

    ```Kusto
    .ingest into table RawEvents h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=RawEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

원시 C# JSON 형식으로 데이터를 수집 하는 데 사용 합니다.

1. `RawEvents` 테이블을 만듭니다.

    ```C#
    var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
    var kustoConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder);

    var table = "RawEvents";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Events", "System.Object"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. JSON 매핑을 만듭니다.
    
    ```C#
    var tableMapping = "RawEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                new JsonColumnMapping {ColumnName = "Events", JsonPath = "$"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```
    이 명령은 매핑을 만들고 JSON 루트 경로 `$`를 `Event` 열에 매핑합니다.

1. 데이터를 `RawEvents` 테이블로 수집 합니다.

    ```C#
    var ingestUri = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var ingestConnectionStringBuilder =
        new KustoConnectionStringBuilder(ingestUri)
        {
            FederatedSecurity = true,
            InitialCatalog = database,
            UserID = user,
            Password = password,
            Authority = tenantId
        };
    var ingestClient = KustoIngestFactory.CreateQueuedIngestClient(ingestConnectionStringBuilder);
    
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

> [!NOTE]
> 데이터는 [일괄 처리 정책](/azure/kusto/concepts/batchingpolicy)에 따라 집계 되므로 몇 분의 대기 시간이 발생 합니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

Python을 사용 하 여 원시 JSON 형식으로 데이터를 수집 합니다.

1. `RawEvents` 테이블을 만듭니다.

    ```Python
    KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(KUSTO_URI, AAD_TENANT_ID)
    KUSTO_CLIENT = KustoClient(KCSB_DATA)
    TABLE = "RawEvents"

    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Events: dynamic)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. JSON 매핑을 만듭니다.

    ```Python
    MAPPING = "RawEventMapping"
    CREATE_MAPPING_COMMAND = ".create table " + TABLE + " ingestion json mapping '" + MAPPING + """' '[{"column":"Event","path":"$"}]'"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 데이터를 `RawEvents` 테이블로 수집 합니다.

    ```Python
    INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
    KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(INGEST_URI, AAD_TENANT_ID)
    INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    > [!NOTE]
    > 데이터는 [일괄 처리 정책](/azure/kusto/concepts/batchingpolicy)에 따라 집계 되므로 몇 분의 대기 시간이 발생 합니다.

---

## <a name="ingest-mapped-json-records"></a>매핑된 JSON 레코드 수집

이 예제에서는 JSON 레코드 데이터를 수집 합니다. 각 JSON 속성은 테이블의 단일 열에 매핑됩니다. 

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. JSON 입력 데이터와 유사한 스키마를 사용 하 여 새 테이블을 만듭니다. 다음 모든 예제 및 수집 명령에이 테이블을 사용 합니다. 

    ```Kusto
    .create table Events (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)
    ```

1. JSON 매핑을 만듭니다.

    ```Kusto
    .create table Events ingestion json mapping 'FlatEventMapping' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'
    ```

    테이블 스키마에 정의 된 대로이 매핑에서 `timestamp` 항목은 `datetime` 데이터 형식으로 `Time` 열로 수집 됩니다.

1. 데이터를 `Events` 테이블로 수집 합니다.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=json, jsonMappingReference=FlatEventMapping)
    ```

    ' Simple. json ' 파일에는 몇 줄로 구분 된 JSON 레코드가 있습니다. 형식은 `json`이며 수집 명령에 사용 된 매핑은 사용자가 만든 `FlatEventMapping`입니다.

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. JSON 입력 데이터와 유사한 스키마를 사용 하 여 새 테이블을 만듭니다. 다음 모든 예제 및 수집 명령에이 테이블을 사용 합니다. 

    ```C#
    var table = "Events";
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            table,
            new[]
            {
                Tuple.Create("Time", "System.DateTime"),
                Tuple.Create("Device", "System.String"),
                Tuple.Create("MessageId", "System.String"),
                Tuple.Create("Temperature", "System.Double"),
                Tuple.Create("Humidity", "System.Double"),
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. JSON 매핑을 만듭니다.

    ```C#
    var tableMapping = "FlatEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.timestamp"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.deviceId"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.messageId"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.temperature"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.humidity"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

    테이블 스키마에 정의 된 대로이 매핑에서 `timestamp` 항목은 `datetime` 데이터 형식으로 `Time` 열로 수집 됩니다.    

1. 데이터를 `Events` 테이블로 수집 합니다.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.json,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

    ' Simple. json ' 파일에는 몇 줄로 구분 된 JSON 레코드가 있습니다. 형식은 `json`이며 수집 명령에 사용 된 매핑은 사용자가 만든 `FlatEventMapping`입니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. JSON 입력 데이터와 유사한 스키마를 사용 하 여 새 테이블을 만듭니다. 다음 모든 예제 및 수집 명령에이 테이블을 사용 합니다. 

    ```Python
    TABLE = "RawEvents"
    CREATE_TABLE_COMMAND = ".create table " + TABLE + " (Time: datetime, Device: string, MessageId: string, Temperature: double, Humidity: double)"
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_TABLE_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. JSON 매핑을 만듭니다.

    ```Python
    MAPPING = "FlatEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.timestamp"},{"column":"Device","path":"$.deviceId"},{"column":"MessageId","path":"$.messageId"},{"column":"Temperature","path":"$.temperature"},{"column":"Humidity","path":"$.humidity"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 데이터를 `Events` 테이블로 수집 합니다.

    ```Python
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/simple.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.json, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

    ' Simple. json ' 파일에는 몇 줄로 구분 된 JSON 레코드가 있습니다. 형식은 `json`이며 수집 명령에 사용 된 매핑은 사용자가 만든 `FlatEventMapping`입니다.    
---

## <a name="ingest-multi-lined-json-records"></a>여러 줄로 이루어진 JSON 레코드 수집

이 예제에서는 여러 줄로 이루어진 JSON 레코드를 수집 합니다. 각 JSON 속성은 테이블의 단일 열에 매핑됩니다. ' Multilined ' 파일에는 몇 가지 들여쓰기 된 JSON 레코드가 있습니다. 형식 `multijson` JSON 구조에 따라 레코드를 읽도록 엔진에 지시 합니다.

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

데이터를 `Events` 테이블로 수집 합니다.

```Kusto
.ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=FlatEventMapping)
```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

데이터를 `Events` 테이블로 수집 합니다.

```C#
var tableMapping = "FlatEventMapping";
var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
var properties =
    new KustoQueuedIngestionProperties(database, table)
    {
        Format = DataSourceFormat.multijson,
        IngestionMappingReference = tableMapping
    };

ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
```

# <a name="pythontabpython"></a>[Python](#tab/python)

데이터를 `Events` 테이블로 수집 합니다.

```Python
MAPPING = "FlatEventMapping"
BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/multilined.JSON?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
```

---

## <a name="ingest-json-records-containing-arrays"></a>배열을 포함 하는 JSON 레코드 수집

배열 데이터 형식은 정렬된 값의 컬렉션입니다. JSON 배열의 수집은 [업데이트 정책](/azure/kusto/management/update-policy)에 의해 수행 됩니다. JSON은 중간 테이블에 있는 그대로 수집 됩니다. 업데이트 정책은 `RawEvents` 테이블에서 미리 정의 된 함수를 실행 하 여 결과를 대상 테이블로 reingesting 합니다. 다음 구조를 사용 하 여 데이터를 수집 합니다.

```json
{
    "records": 
    [
        {
            "timestamp": "2019-05-02 15:23:50.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "7f316225-839a-4593-92b5-1812949279b3",
            "temperature": 31.0301639051317,
            "humidity": 62.0791099602725
        },
        {
            "timestamp": "2019-05-02 15:23:51.0000000",
            "deviceId": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71",
            "messageId": "57de2821-7581-40e4-861e-ea3bde102364",
            "temperature": 33.7529423105311,
            "humidity": 75.4787976739364
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. `mv-expand` 연산자를 사용 하 여 컬렉션의 각 값이 개별 행을 받도록 `records` 컬렉션을 확장 하는 `update policy` 함수를 만듭니다. 테이블 `RawEvents`을 원본 테이블로 사용 하 고 대상 테이블로 `Events` 합니다.

    ```Kusto
    .create function EventRecordsExpand() {
        RawEvents
        | mv-expand records = Event
        | project
            Time = todatetime(records["timestamp"]),
            Device = tostring(records["deviceId"]),
            MessageId = tostring(records["messageId"]),
            Temperature = todouble(records["temperature"]),
            Humidity = todouble(records["humidity"])
    }
    ```

1. 함수에서 받은 스키마는 대상 테이블의 스키마와 일치 해야 합니다. `getschema` 연산자를 사용 하 여 스키마를 검토 합니다.

    ```Kusto
    EventRecordsExpand() | getschema
    ```

1. 대상 테이블에 업데이트 정책을 추가 합니다. 이 정책은 `RawEvents` 중간 테이블에서 새로 수집 데이터에 대한 쿼리를 자동으로 실행 하 고 결과를 `Events` 테이블에 수집 합니다. 중간 테이블을 유지 하지 않으려면 0 보존 정책을 정의 합니다.

    ```Kusto
    .alter table Events policy update @'[{"Source": "RawEvents", "Query": "EventRecordsExpand()", "IsEnabled": "True"}]'
    ```

1. 데이터를 `RawEvents` 테이블로 수집 합니다.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=RawEventMapping)
    ```

1. `Events` 테이블의 데이터를 검토 합니다.

    ```Kusto
    Events
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. `mv-expand` 연산자를 사용 하 여 컬렉션의 각 값이 개별 행을 받도록 `records` 컬렉션을 확장 하는 update 함수를 만듭니다. 테이블 `RawEvents`을 원본 테이블로 사용 하 고 대상 테이블로 `Events` 합니다.   

    ```C#
    var command =
        CslCommandGenerator.GenerateCreateFunctionCommand(
            "EventRecordsExpand",
            "UpdateFunctions",
            string.Empty,
            null,
            @"RawEvents
                | mv-expand records = Event
                | project
                    Time = todatetime(records['timestamp']),
                    Device = tostring(records['deviceId']),
                    MessageId = tostring(records['messageId']),
                    Temperature = todouble(records['temperature']),
                    Humidity = todouble(records['humidity'])",
            ifNotExists: false);

    kustoClient.ExecuteControlCommand(command);
    ```

    > [!NOTE]
    > 함수에서 받은 스키마는 대상 테이블의 스키마와 일치 해야 합니다.

1. 대상 테이블에 업데이트 정책을 추가 합니다. 이 정책은 `RawEvents` 중간 테이블에서 새로 수집 데이터에 대한 쿼리를 자동으로 실행 하 고 해당 결과를 `Events` 테이블에 수집 합니다. 중간 테이블을 유지 하지 않으려면 0 보존 정책을 정의 합니다.

    ```C#
    var command =
        ".alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]";

    kustoClient.ExecuteControlCommand(command);
    ```

1. 데이터를 `RawEvents` 테이블로 수집 합니다.

    ```C#
    var table = "RawEvents";
    var tableMapping = "RawEventMapping";
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```
    
1. `Events` 테이블의 데이터를 검토 합니다.

# <a name="pythontabpython"></a>[Python](#tab/python)

1. `mv-expand` 연산자를 사용 하 여 컬렉션의 각 값이 개별 행을 받도록 `records` 컬렉션을 확장 하는 update 함수를 만듭니다. 테이블 `RawEvents`을 원본 테이블로 사용 하 고 대상 테이블로 `Events` 합니다.   

    ```Python
    CREATE_FUNCTION_COMMAND = 
        '''.create function EventRecordsExpand() { 
            RawEvents
            | mv-expand records = Event
            | project
                Time = todatetime(records["timestamp"]),
                Device = tostring(records["deviceId"]),
                MessageId = tostring(records["messageId"]),
                Temperature = todouble(records["temperature"]),
                Humidity = todouble(records["humidity"])
            }'''
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_FUNCTION_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

    > [!NOTE]
    > 함수가 받은 스키마는 대상 테이블의 스키마와 일치 해야 합니다.

1. 대상 테이블에 업데이트 정책을 추가 합니다. 이 정책은 `RawEvents` 중간 테이블에서 새로 수집 데이터에 대한 쿼리를 자동으로 실행 하 고 해당 결과를 `Events` 테이블에 수집 합니다. 중간 테이블을 유지 하지 않으려면 0 보존 정책을 정의 합니다.

    ```Python
    CREATE_UPDATE_POLICY_COMMAND = 
        """.alter table Events policy update @'[{'Source': 'RawEvents', 'Query': 'EventRecordsExpand()', 'IsEnabled': 'True'}]"""
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_UPDATE_POLICY_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 데이터를 `RawEvents` 테이블로 수집 합니다.

    ```Python
    TABLE = "RawEvents"
    MAPPING = "RawEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/array.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

1. `Events` 테이블의 데이터를 검토 합니다.

---    

## <a name="ingest-json-records-containing-dictionaries"></a>사전을 포함 하는 JSON 레코드 수집

사전 구조적 JSON에는 키-값 쌍이 포함 되어 있습니다. Json 레코드는 `JsonPath`논리 식을 사용 하 여 수집 매핑을 거칩니다. 다음 구조를 사용 하 여 데이터를 수집할 수 있습니다.

```json
{
    "event": 
    [
        {
            "Key": "timestamp",
            "Value": "2019-05-02 15:23:50.0000000"
        },
        {
            "Key": "deviceId",
            "Value": "ddbc1bf5-096f-42c0-a771-bc3dca77ac71"
        },
        {
            "Key": "messageId",
            "Value": "7f316225-839a-4593-92b5-1812949279b3"
        },
        {
            "Key": "temperature",
            "Value": 31.0301639051317
        },
        {
            "Key": "humidity",
            "Value": 62.0791099602725
        }
    ]
}
```

# <a name="kqltabkusto-query-language"></a>[KQL](#tab/kusto-query-language)

1. JSON 매핑을 만듭니다.

    ```Kusto
    .create table Events ingestion json mapping 'KeyValueEventMapping' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'
    ```

1. 데이터를 `Events` 테이블로 수집 합니다.

    ```Kusto
    .ingest into table Events h'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (format=multijson, jsonMappingReference=KeyValueEventMapping)
    ```

# <a name="ctabc-sharp"></a>[C#](#tab/c-sharp)

1. JSON 매핑을 만듭니다.

    ```C#
    var tableName = "Events";
    var tableMapping = "KeyValueEventMapping";
    var command =
        CslCommandGenerator.GenerateTableJsonMappingCreateCommand(
            tableName,
            tableMapping,
            new[]
            {
                        new JsonColumnMapping {ColumnName = "Time", JsonPath = "$.event[?(@.Key == 'timestamp')]"},
                        new JsonColumnMapping {ColumnName = "Device", JsonPath = "$.event[?(@.Key == 'deviceId')]"},
                        new JsonColumnMapping {ColumnName = "MessageId", JsonPath = "$.event[?(@.Key == 'messageId')]"},
                        new JsonColumnMapping {ColumnName = "Temperature", JsonPath = "$.event[?(@.Key == 'temperature')]"},
                        new JsonColumnMapping {ColumnName = "Humidity", JsonPath = "$.event[?(@.Key == 'humidity')]"},
            });

    kustoClient.ExecuteControlCommand(command);
    ```

1. 데이터를 `Events` 테이블로 수집 합니다.

    ```C#
    var blobPath = "https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
    var properties =
        new KustoQueuedIngestionProperties(database, table)
        {
            Format = DataSourceFormat.multijson,
            IngestionMappingReference = tableMapping
        };

    ingestClient.IngestFromSingleBlob(blobPath, deleteSourceOnSuccess: false, ingestionProperties: properties);
    ```

# <a name="pythontabpython"></a>[Python](#tab/python)

1. JSON 매핑을 만듭니다.

    ```Python
    MAPPING = "KeyValueEventMapping"
    CREATE_MAPPING_COMMAND = ".create table Events ingestion json mapping '" + MAPPING + """' '[{"column":"Time","path":"$.event[?(@.Key == 'timestamp')]"},{"column":"Device","path":"$.event[?(@.Key == 'deviceId')]"},{"column":"MessageId","path":"$.event[?(@.Key == 'messageId')]"},{"column":"Temperature","path":"$.event[?(@.Key == 'temperature')]"},{"column":"Humidity","path":"$.event[?(@.Key == 'humidity')]"}]'""" 
    RESPONSE = KUSTO_CLIENT.execute_mgmt(DATABASE, CREATE_MAPPING_COMMAND)
    dataframe_from_result_table(RESPONSE.primary_results[0])
    ```

1. 데이터를 `Events` 테이블로 수집 합니다.

     ```Python
    MAPPING = "KeyValueEventMapping"
    BLOB_PATH = 'https://kustosamplefiles.blob.core.windows.net/jsonsamplefiles/dictionary.json?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D'
    INGESTION_PROPERTIES = IngestionProperties(database=DATABASE, table=TABLE, dataFormat=DataFormat.multijson, mappingReference=MAPPING)u
    BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
    INGESTION_CLIENT.ingest_from_blob(
        BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)
    ```

---    

## <a name="next-steps"></a>다음 단계

* [데이터 수집 개요](ingest-data-overview.md)
* [쿼리 작성](write-queries.md)