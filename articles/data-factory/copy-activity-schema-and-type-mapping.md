---
title: 복사 작업의 스키마 매핑
description: Azure Data Factory의 복사 작업에서 데이터를 복사할 때 원본 데이터의 스키마와 데이터 형식을 싱크 데이터에 매핑하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 04/29/2019
ms.author: jingwang
ms.openlocfilehash: 2c637346aae72a238963607f6f5d23910684265c
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74921988"
---
# <a name="schema-mapping-in-copy-activity"></a>복사 작업의 스키마 매핑

이 문서에서는 Azure Data Factory 복사 작업에서 데이터 복사를 실행할 때 원본 데이터에서 싱크 데이터로의 스키마 매핑 및 데이터 형식 매핑을 수행 하는 방법을 설명 합니다.

## <a name="schema-mapping"></a>스키마 매핑

열 매핑은 원본에서 싱크로 데이터를 복사할 때 적용 됩니다. 기본적으로 복사 작업은 **원본 데이터를 열 이름으로 싱크에 매핑합니다**. [명시적 매핑을](#explicit-mapping) 지정 하 여 필요에 따라 열 매핑을 사용자 지정할 수 있습니다. 보다 구체적으로 복사 작업:

1. 원본에서 데이터를 읽고 원본 스키마를 결정합니다.
2. 기본 열 매핑을 사용 하 여 이름을 기준으로 열을 매핑하거나 지정 된 경우 명시적 열 매핑을 적용 합니다.
3. 싱크에 데이터 쓰기

### <a name="explicit-mapping"></a>명시적 매핑

복사 활동-> `translator` -> `mappings` 속성에 매핑할 열을 지정할 수 있습니다. 다음 예제에서는 분리 된 텍스트에서 Azure SQL Database로 데이터를 복사 하는 파이프라인의 복사 작업을 정의 합니다.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [{
        "referenceName": "DelimitedTextInput",
        "type": "DatasetReference"
    }],
    "outputs": [{
        "referenceName": "AzureSqlOutput",
        "type": "DatasetReference"
    }],
    "typeProperties": {
        "source": { "type": "DelimitedTextSource" },
        "sink": { "type": "SqlSink" },
        "translator": {
            "type": "TabularTranslator",
            "mappings": [
                {
                    "source": {
                        "name": "UserId",
                        "type": "Guid"
                    },
                    "sink": {
                        "name": "MyUserId"
                    }
                }, 
                {
                    "source": {
                        "name": "Name",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyName"
                    }
                }, 
                {
                    "source": {
                        "name": "Group",
                        "type": "String"
                    },
                    "sink": {
                        "name": "MyGroup"
                    }
                }
            ]
        }
    }
}
```

다음 속성은 `source` 및 `sink`를 사용 하는 `translator` -> `mappings` > 개체에서 지원 됩니다.

| 자산 | 설명                                                  | 필수 |
| -------- | ------------------------------------------------------------ | -------- |
| 이름     | 원본 또는 싱크 열의 이름입니다.                           | yes      |
| ordinal  | 열 인덱스입니다. 1로 시작 합니다. <br>머리글 줄 없이 분리 된 텍스트를 사용 하는 경우 적용 하 고 필요 합니다. | 아닙니다.       |
| 경로     | 추출 하거나 매핑할 각 필드에 대한 JSON 경로 식입니다. 계층 구조 데이터 (예: MongoDB/REST)에 적용 됩니다.<br>루트 개체 아래에 있는 필드의 경우 JSON 경로는 root $로 시작 합니다. `collectionReference` 속성에서 선택한 배열 내의 필드에 대해 JSON 경로는 배열 요소에서 시작 합니다. | 아닙니다.       |
| type     | 원본 또는 싱크 열의 중간 데이터 형식을 Data Factory 합니다. | 아닙니다.       |
| culture  | 원본 또는 싱크 열의 문화권입니다. <br>형식이 `Datetime` 또는 `Datetimeoffset`경우 적용 됩니다. 기본값은 `en-us`입니다. | 아닙니다.       |
| format   | 형식이 `Datetime` 또는 `Datetimeoffset`때 사용할 서식 문자열입니다. 날짜/시간 형식을 지정하는 방법은 [사용자 지정 날짜 및 시간 형식 문자열](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)을 참조하세요. | 아닙니다.       |

다음 속성은 `source` 및 `sink`있는 개체 외에도 `translator` -> `mappings`에서 지원 됩니다.

| 자산            | 설명                                                  | 필수 |
| ------------------- | ------------------------------------------------------------ | -------- |
| collectionReference | 계층 구조 데이터 (예: MongoDB/REST가 원본) 인 경우에만 지원 됩니다.<br>동일한 패턴으로 **배열 필드 내부**의 개체에서 데이터를 반복 및 추출하고 행별 개체별로 변환하려면 교차 적용하도록 해당 배열의 JSON 경로를 지정합니다. | 아닙니다.       |

### <a name="alternative-column-mapping"></a>대체 열 매핑

복사 작업-> `translator` -> `columnMappings`를 지정 하 여 테이블 형식 데이터를 매핑할 수 있습니다. 이 경우 입력 및 출력 데이터 집합 모두에 "structure" 섹션이 필요 합니다. 열 매핑은 **원본 데이터 세트 "structure"의 전체 열 또는 하위 집합을 싱크 데이터 세트 "structure"** 의 모든 열에 매핑하는 것을 지원합니다. 다음은 예외가 발생하는 오류 조건입니다.

* 원본 데이터 저장소 쿼리 결과에 입력 데이터 세트 "structure" 섹션에서 지정한 열 이름이 없습니다.
* 싱크 데이터 저장소(미리 정의된 스키마가 있는 경우)에 출력 데이터 세트 "structure" 섹션에서 지정한 열 이름이 없습니다.
* 매핑에서 지정한 것보다 싱크 데이터 세트의 "structure"에 열이 더 많거나 적습니다.
* 중복 매핑

다음 예의 입력 데이터 집합에는 구조가 있으며이는 온-프레미스 Oracle 데이터베이스의 테이블을 가리킵니다.

```json
{
    "name": "OracleDataset",
    "properties": {
        "structure":
         [
            { "name": "UserId"},
            { "name": "Name"},
            { "name": "Group"}
         ],
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "OracleLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTable"
        }
    }
}
```

이 샘플에서 출력 데이터 집합은 구조를 포함 하 고 Salesfoce의 테이블을 가리킵니다.

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "structure":
        [
            { "name": "MyUserId"},
            { "name": "MyName" },
            { "name": "MyGroup"}
        ],
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "SalesforceLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SinkTable"
        }
    }
}
```

다음 JSON은 파이프라인으로 복사 작업을 정의합니다. 원본에서 열은 **변환기** -> **columnmappings** 속성을 사용 하 여 싱크의 열에 매핑됩니다.

```json
{
    "name": "CopyActivity",
    "type": "Copy",
    "inputs": [
        {
            "referenceName": "OracleDataset",
            "type": "DatasetReference"
        }
    ],
    "outputs": [
        {
            "referenceName": "SalesforceDataset",
            "type": "DatasetReference"
        }
    ],
    "typeProperties":    {
        "source": { "type": "OracleSource" },
        "sink": { "type": "SalesforceSink" },
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

### <a name="alternative-schema-mapping"></a>대체 스키마 매핑

복사 작업-> `translator` -> `schemaMapping`를 지정 하 여 계층적 데이터 및 테이블 형식 데이터 사이에 매핑할 수 있습니다. 예를 들어 MongoDB/REST에서 텍스트 파일로 복사 하 고 Oracle에서 Azure Cosmos DB의 MongoDB API로 복사 합니다. 다음 속성은 복사 작업 `translator` 섹션에서 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 변환기의 type 속성은 **TabularTranslator** 로 설정 해야 합니다. | yes |
| schemaMapping | **소스 쪽에서 싱크 쪽으로의**매핑 관계를 나타내는 키-값 쌍의 컬렉션입니다.<br/>- **Key:** 원본을 나타냅니다. **테이블 형식 원본의**경우 데이터 집합 구조에 정의 된 열 이름을 지정 합니다. **계층 구조 원본**에서 추출 및 매핑할 각 필드에 대한 JSON 경로 식을 지정 합니다.<br>- **Value:** 싱크를 나타냅니다. **테이블 형식 싱크에**대해 데이터 집합 구조에 정의 된 열 이름을 지정 합니다. **계층적 싱크에**대해 추출 및 매핑할 각 필드에 대한 JSON 경로 식을 지정 합니다. <br>계층적 데이터의 경우 루트 개체 아래의 필드에 대해 JSON 경로는 root $로 시작 합니다. `collectionReference` 속성에서 선택한 배열 내의 필드에 대해 JSON 경로는 배열 요소에서 시작 합니다.  | yes |
| collectionReference | 동일한 패턴으로 **배열 필드 내부**의 개체에서 데이터를 반복 및 추출하고 행별 개체별로 변환하려면 교차 적용하도록 해당 배열의 JSON 경로를 지정합니다. 이 속성은 계층적 데이터가 원본인 경우에만 지원됩니다. | 아닙니다. |

**예: MongoDB에서 Oracle로 복사 합니다.**

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
    "name": "CopyFromMongoDBToOracle",
    "type": "Copy",
    "typeProperties": {
        "source": {
            "type": "MongoDbV2Source"
        },
        "sink": {
            "type": "OracleSink"
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

Data Factory는 다음과 같은 중간 데이터 형식을 지원 합니다. [데이터 집합 구조](concepts-datasets-linked-services.md#dataset-structure-or-schema) 구성에서 형식 정보를 구성할 때 아래 값을 지정할 수 있습니다.

* Byte[]
* Boolean
* DateTime
* Datetimeoffset
* 10진수
* DOUBLE
* GUID
* Int16
* Int32
* Int64
* 단일
* string
* Timespan

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
