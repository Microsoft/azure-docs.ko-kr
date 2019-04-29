---
title: 복사 작업의 스키마 매핑 | Microsoft 문서
description: Azure Data Factory의 복사 작업에서 데이터를 복사할 때 원본 데이터의 스키마와 데이터 형식을 싱크 데이터에 매핑하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 99798b35419ec9574c99aaba42803fbeeb1555f1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60615633"
---
# <a name="schema-mapping-in-copy-activity"></a>복사 작업의 스키마 매핑
이 문서에서는 Azure Data Factory 복사 작업에서 데이터 복사를 실행할 때 원본 데이터의 스키마 매핑과 데이터 형식을 싱크 데이터에 매핑하는 방법을 설명합니다.

## <a name="column-mapping"></a>열 매핑

열 매핑은 테이블 형식 데이터 간에 데이터를 복사할 때 적용됩니다. 기본적으로 복사 작업은 [명시적 열 매핑](#explicit-column-mapping)을 구성하지 않은 한 **열 이름을 기준으로 원본 데이터를 싱크에 매핑합니다**. 보다 구체적으로 복사 작업:

1. 원본에서 데이터를 읽고 원본 스키마를 결정합니다.

    * 메타데이터가 있는 데이터베이스/파일(Avro/ORC/Parquet/헤더 있는 텍스트)처럼 데이터 저장소/파일 형식으로 미리 정의된 데이터 원본의 경우, 원본 스키마는 쿼리 결과나 파일 메타데이터에서 추출됩니다.
    * Azure Table/Cosmos DB처럼 유연한 스키마가 있는 데이터 원본의 경우 원본 스키마는 쿼리 결과에서 유추됩니다. 데이터 세트에서 "structure"를 구성하여 이를 덮어쓸 수 있습니다.
    * 헤더 없는 텍스트 파일의 경우 기본 열 이름은 "Prop_0", "Prop_1" 패턴으로 생성되며 데이터 세트에서 "structure"를 구성하여 이를 덮어쓸 수 있습니다.
    * Dynamics 원본의 경우 데이터 세트 “structure” 섹션에서 스키마 정보를 제공해야 합니다.

2. 지정된 경우 명시적 열 매핑을 적용합니다.

3. 싱크에 데이터 쓰기

    * 미리 정의된 스키마가 있는 데이터 저장소의 경우 같은 이름을 갖는 열에 데이터를 씁니다.
    * 고정된 스키마 없는 데이터 저장소 및 파일 형식의 경우 열 이름/메타데이터를 원본 스키마를 기준으로 생성합니다.

### <a name="explicit-column-mapping"></a>명시적 열 매핑

복사 작업의 **typeProperties** 섹션에 **columnMappings**를 지정하여 명시적 열 매핑을 수행할 수 있습니다. 이 시나리오에서는 "structure" 섹션이 입력과 출력 데이터 세트 모두에 필요합니다. 열 매핑은 **원본 데이터 세트 "structure"의 전체 열 또는 하위 집합을 싱크 데이터 세트 "structure"** 의 모든 열에 매핑하는 것을 지원합니다. 다음은 예외가 발생하는 오류 조건입니다.

* 원본 데이터 저장소 쿼리 결과에 입력 데이터 세트 "structure" 섹션에서 지정한 열 이름이 없습니다.
* 싱크 데이터 저장소(미리 정의된 스키마가 있는 경우)에 출력 데이터 세트 "structure" 섹션에서 지정한 열 이름이 없습니다.
* 매핑에서 지정한 것보다 싱크 데이터 세트의 "structure"에 열이 더 많거나 적습니다.
* 중복 매핑

#### <a name="explicit-column-mapping-example"></a>명시적 열 매핑 예제

이 예제에서 입력 테이블에는 구조가 있고 그 구조가 온-프레미스 SQL 데이터베이스에 있는 테이블을 가리킵니다.

```json
{
    "name": "SqlServerInput",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "SqlServerTable",
        "linkedServiceName": {
            "referenceName": "SqlServerLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

이 예제에서 출력 테이블에는 구조가 있고 그 구조가 Azure SQL Database에 있는 테이블을 가리킵니다.

```json
{
    "name": "AzureSqlOutput",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "AzureSqlLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

다음 JSON은 파이프라인으로 복사 작업을 정의합니다. 원본의 열과 싱크(**columnMappings**)의 열 간 매핑은 **Translator** 속성을 사용하여 이루어집니다.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "SqlServerInput",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "AzureSqlOutput",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "SqlSource" },
        "sink": { "type": "SqlSink" },
        "translator":
        {
            "type": "TabularTranslator",
            "columnMappings": 
            {
                "UserId": "MyUserId",
                "Group": "MyGroup",
                "Name": "MyName"
            }
        }
    }
}
```

`"columnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"` 구문을 사용하여 열 매핑을 지정하면 있는 그대로 지원됩니다.

**열 매핑 흐름:**

![열 매핑 흐름 ](./media/copy-activity-schema-and-type-mapping/column-mapping-sample.png)

## <a name="schema-mapping"></a>스키마 매핑

매핑 스키마는 MongoDB/REST에서 텍스트 파일로, 그리고 SQL에서 MongoDB용 Azure Cosmos DB API로 복사할 때와 같이 계층형 데이터와 테이블형 데이터 간에 데이터를 복사할 때 적용됩니다. 다음 속성은 복사 작업 `translator` 섹션에서 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 변환기의 type 속성은 **TabularTranslator**로 설정해야 합니다. | 예 |
| schemaMapping | 매핑 관계를 나타내는 키-값 쌍의 컬렉션인 **쪽 싱크 원본 쪽에서**합니다.<br/>- **키:** 원본을 나타냅니다. 에 대 한 **테이블 형식 원본**에 대 한 데이터 집합 구조에 정의 된 대로 열 이름을 지정 **계층적 원본**, 각 필드를 추출 하 고 지도 대 한 JSON 경로 식을 지정 합니다.<br/>- **값:** 싱크를 나타냅니다. 에 대 한 **tabular 싱크**에 대 한 데이터 집합 구조에 정의 된 대로 열 이름을 지정 **계층적 싱크**, 각 필드를 추출 하 고 지도 대 한 JSON 경로 식을 지정 합니다. <br/> 루트 개체 아래에 있는 필드에 대 한 계층적 데이터의 경우 JSON 경로가 루트 $;로 시작 으로 선택 된 배열 내부의 필드 `collectionReference` 배열 요소에서 속성을 JSON 경로가 시작 됩니다.  | 예 |
| collectionReference | 동일한 패턴으로 **배열 필드 내부**의 개체에서 데이터를 반복 및 추출하고 행별 개체별로 변환하려면 교차 적용하도록 해당 배열의 JSON 경로를 지정합니다. 이 속성은 계층적 데이터가 원본인 경우에만 지원됩니다. | 아닙니다. |

**예: MongoDB에서 SQL로 복사:**

예를 들어 MongoDB 문서에 다음과 같은 콘텐츠가 들어 있는 경우: 

```json
{
    "id": {
        "$oid": "592e07800000000000000000"
    },
    "number": "01",
    "date": "20170122",
    "orders": [
        {
            "prod": "p1",
            "price": 23
        },
        {
            "prod": "p2",
            "price": 13
        },
        {
            "prod": "p3",
            "price": 231
        }
    ],
    "city": [ { "name": "Seattle" } ]
}
```

그리고 *(order_pd 및 order_price)* 배열 내부의 데이터를 평면화하고 일반적인 루트 정보 *(number, date 및 city)* 로 크로스 조인하여 Azure SQL 테이블에 데이터를 다음 형식으로 복사하려는 경우:

| orderNumber | orderDate | order_pd | order_price | city |
| --- | --- | --- | --- | --- |
| 01 | 20170122 | P1 | 23 | 시애틀 |
| 01 | 20170122 | P2 | 13 | 시애틀 |
| 01 | 20170122 | P3 | 231 | 시애틀 |

다음 복사 작업 JSON 샘플처럼 스키마-매핑 규칙을 구성합니다.

```json
{
    "name": "CopyFromMongoDBToSqlAzure",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "SqlSink"
        },
        "translator": {
            "type": "TabularTranslator",
            "schemaMapping": {
                "orderNumber": "$.number", 
                "orderDate": "$.date", 
                "order_pd": "prod", 
                "order_price": "price",
                "city": " $.city[0].name"
            },
            "collectionReference":  "$.orders"
        }
    }
}
```

## <a name="data-type-mapping"></a>데이터 형식 매핑

복사 작업에서는 다음 2단계 방식으로 원본 형식을 싱크 형식에 매핑합니다.

1. 원시 원본 형식을 Azure Data Factory 중간 데이터 형식으로 변환
2. Azure Data Factory 중간 데이터 형식을 원시 싱크 형식으로 변환

각 커넥터 항목의 "데이터 형식 매핑" 섹션에서 원시 형식과 중간 형식 간의 매핑을 확인할 수 있습니다.

### <a name="supported-data-types"></a>지원되는 데이터 원본

Data Factory는 다음과 같은 중간 데이터 형식을 지원합니다. [데이터 세트 구조](concepts-datasets-linked-services.md#dataset-structure) 구성에서 형식 정보를 구성할 때 아래 값을 지정할 수 있습니다.

* Byte[]
* Boolean
* DateTime
* Datetimeoffset
* Decimal
* Double
* Guid
* Int16
* Int32
* Int64
* Single
* String
* Timespan

### <a name="explicit-data-type-conversion"></a>명시적 데이터 형식 변환

데이터를 고정 스키마가 있는 데이터 저장소에 복사할 때(예: SQL Server/Oracle) 동ㅇ리한 열의 원본과 싱크 형식이 다르면 명시적 형식 변환을 원본 측에서 정의해야 합니다.

* CSV/Avro 등의 파일 원본의 경우 전체 열 목록이 있는 원본 구조를 통해 형식 변환을 선언해야 합니다(원본 측 열 이름 및 싱크 측 형식).
* 관계형 원본(예: SQL/Oracle)의 경우 형식 변환은 쿼리 문에서의 명시적 형식 캐스팅을 통해 이루어져야 합니다.

## <a name="when-to-specify-dataset-structure"></a>데이터 세트 "structure"를 지정하는 경우

아래 시나리오에서 데이터 세트의 “structure”가 필요합니다.

* 복사 중 파일 원본에 대해 [명시적 데이터 형식 변환](#explicit-data-type-conversion) 적용(입력 데이터 세트)
* 복사 중 [명시적 열 매핑](#explicit-column-mapping) 적용(입력 및 출력 데이터 세트 모두)
* Dynamics 365/CRM 원본에서 복사(입력 데이터 세트)
* 원본이 JSON 파일이 아닐 때 Cosmos DB를 중첩 개체에 복사(출력 데이터 세트)

아래 시나리오에서는 데이터 세트의 “structure”가 필요합니다.

* 헤더 없는 텍스트 파일에서 복사(입력 데이터 세트). 해당하는 싱크 열과 일치하는 텍스트 파일의 열 이름을 지정하여 명시적 열 매핑을 피할 수 있습니다.
* Azure Table/Cosmos DB 등, 유연한 스키마가 있는 데이터 저장소에서 복사하면(입력 데이터 세트) 각각의 작업 실행 중에 맨 위 행을 기준으로 복사 작업이 스키마를 유추하는 것이 아니라 원하는 데이터(열)가 복사되게 보장할 수 있습니다.


## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 내결함성](copy-activity-fault-tolerance.md)
- [복사 작업 성능](copy-activity-performance.md)
