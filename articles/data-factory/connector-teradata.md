---
title: Azure Data Factory를 사용 하 여 Teradata 유리한에서 데이터 복사
description: Data Factory 서비스의 Teradata 커넥터를 사용 하 여 Teradata 유리한에서 Data Factory 지원 되는 데이터 저장소에 싱크로 데이터를 복사할 수 있습니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 5a41d5653de0d8a9f674009904756892ac343609
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930912"
---
# <a name="copy-data-from-teradata-vantage-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Teradata 유리한에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
>
> * [버전 1](v1/data-factory-onprem-teradata-connector.md)
> * [현재 버전](connector-teradata.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 Teradata 유리한에서 데이터를 복사 하는 방법을 설명 합니다. [복사 작업 개요](copy-activity-overview.md)를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Teradata 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Teradata 유리한에서 지원 되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Teradata 커넥터는 다음을 지원합니다.

- Teradata **버전 14.10, 15.0, 15.10, 16.0, 16.10 및 16.20**입니다.
- **기본** 또는 **Windows** 인증을 사용 하 여 데이터 복사
- Teradata 원본에서의 병렬 복사 자세한 내용은 [Teradata에서 Parallel 복사](#parallel-copy-from-teradata) 섹션을 참조 하세요.

> [!NOTE]
>
> 자체 호스팅 integration runtime v 3.18의 릴리스 후에는 Teradata 커넥터가 업그레이드 Azure Data Factory. 이전 Teradata 커넥터를 사용 하는 기존 작업은 계속 지원 됩니다. 그러나 새 워크 로드의 경우 새 워크 로드를 사용 하는 것이 좋습니다. 새 경로에는 연결 된 서비스, 데이터 집합 및 복사 원본의 다른 집합이 필요 합니다. 구성 세부 정보는 다음에 나오는 해당 섹션을 참조 하세요.

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration runtime은 버전 3.18에서 시작 하는 기본 제공 Teradata 드라이버를 제공 합니다. 드라이버를 수동으로 설치 하지 않아도 됩니다. 이 드라이버를 사용 하려면 C++ 자체 호스팅 통합 런타임 컴퓨터에 "Visual 재배포할 2012 업데이트 4"가 필요 합니다. 아직 설치 하지 않은 경우 [여기](https://www.microsoft.com/en-sg/download/details.aspx?id=30679)에서 다운로드 합니다.

3\.18 이전의 자체 호스팅 통합 런타임 버전의 경우 integration runtime 컴퓨터에 [Teradata 용 .net Data Provider](https://go.microsoft.com/fwlink/?LinkId=278886)버전 14 이상을 설치 합니다. 

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Teradata 커넥터와 관련 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Teradata 연결 된 서비스는 다음 속성을 지원 합니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | Type 속성은 **Teradata**로 설정 되어야 합니다. | yes |
| connectionString | Teradata 인스턴스에 연결 하는 데 필요한 정보를 지정 합니다. 다음 샘플을 참조하세요.<br/>Azure Key Vault에 암호를 입력 하 고 `password` 구성을 연결 문자열 외부로 끌어올 수도 있습니다. 자세한 내용은 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 을 참조 하세요. | yes |
| username | Teradata에 연결할 사용자 이름을 지정 합니다. Windows 인증을 사용 하는 경우 적용 됩니다. | 아닙니다. |
| 암호 | 사용자 이름에 대해 지정한 사용자 계정의 암호를 지정 합니다. [Azure Key Vault에 저장 된 비밀을 참조](store-credentials-in-key-vault.md)하도록 선택할 수도 있습니다. <br>Windows 인증을 사용 하거나 기본 인증을 위해 Key Vault의 암호를 참조 하는 경우에 적용 됩니다. | 아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [전제 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |yes |

경우에 따라 연결 문자열에서 설정할 수 있는 추가 연결 속성이 있습니다.

| 자산 | 설명 | 기본값 |
|:--- |:--- |:--- |
| CharacterSet | 세션에 사용할 문자 집합입니다. 예: `CharacterSet=UTF16`.<br><br/>이 값은 사용자 정의 문자 집합 또는 미리 정의 된 다음 문자 집합 중 하나일 수 있습니다. <br/>-ASCII<br/>-UTF8<br/>-UTF16<br/>-LATIN1252_0A<br/>-LATIN9_0A<br/>-LATIN1_0A<br/>-Shift-jis (Windows, DOS 호환, KANJISJIS_0S)<br/>-EUC (Unix 호환, KANJIEC_0U)<br/>-IBM 메인프레임 (KANJIEBCDIC5035_0I)<br/>-KANJI932_1S0<br/>-BIG5 (TCHBIG5_1R0)<br/>-GB (SCHGB2312_1T0)<br/>-SCHINESE936_6R0<br/>-TCHINESE950_8R0<br/>-NetworkKorean (HANGULKSC5601_2R4)<br/>-HANGUL949_7R0<br/>-ARABIC1256_6A0<br/>-CYRILLIC1251_2A0<br/>-HEBREW1255_5A0<br/>-LATIN1250_1A0<br/>-LATIN1254_7A0<br/>-LATIN1258_8A0<br/>-THAI874_4A0 | 기본값은 `ASCII`입니다. |
| MaxRespSize |SQL 요청에 대한 응답 버퍼의 최대 크기 (kb)입니다 (Kb). 예: `MaxRespSize=‭10485760‬`.<br/><br/>Teradata 데이터베이스 버전 16.00 이상에서는 최대 값이 7361536입니다. 이전 버전을 사용 하는 연결의 경우 최대값은 1048576입니다. | 기본값은 `65536`입니다. |

**기본 인증을 사용 하는 예제**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>;Uid=<username>;Pwd=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Windows 인증 사용 예**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "connectionString": "DBCName=<server>",
            "username": "<username>",
            "password": "<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

> [!NOTE]
>
> 다음 페이로드는 계속 지원 됩니다. 그러나 앞으로 새 항목을 사용 해야 합니다.

**이전 페이로드:**

```json
{
    "name": "TeradataLinkedService",
    "properties": {
        "type": "Teradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<Basic/Windows>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

이 섹션에서는 Teradata 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다. 데이터 집합을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md)을 참조 하세요.

Teradata에서 데이터를 복사 하려면 다음 속성이 지원 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성을 `TeradataTable`으로 설정 해야 합니다. | yes |
| database | Teradata 인스턴스의 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |
| 테이블 | Teradata 인스턴스의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "TeradataTable",
        "typeProperties": {},
        "schema": [],        
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

> [!NOTE]
>
> `RelationalTable` 형식 데이터 집합은 계속 지원 됩니다. 그러나 새 데이터 집합을 사용 하는 것이 좋습니다.

**이전 페이로드:**

```json
{
    "name": "TeradataDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Teradata linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

이 섹션에서는 Teradata 원본에서 지원하는 속성의 목록을 제공합니다. 작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="teradata-as-source"></a>Teradata를 원본으로

>[!TIP]
>데이터 분할을 사용 하 여 Teradata에서 데이터를 효율적으로 로드 하려면 [teradata의 Parallel copy](#parallel-copy-from-teradata) 섹션에서 자세히 알아보세요.

Teradata에서 데이터를 복사 하려면 복사 작업 **원본** 섹션에서 다음 속성을 지원 합니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 `TeradataSource`으로 설정 해야 합니다. | yes |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예는 `"SELECT * FROM MyTable"`입니다.<br>분할 된 로드를 사용 하도록 설정 하는 경우 쿼리에 해당 하는 기본 제공 파티션 매개 변수를 후크 해야 합니다. 예제는 [Teradata에서 Parallel 복사](#parallel-copy-from-teradata) 섹션을 참조 하세요. | 아니요 (데이터 집합의 테이블이 지정 된 경우) |
| partitionOptions | Teradata에서 데이터를 로드 하는 데 사용 되는 데이터 분할 옵션을 지정 합니다. <br>허용 값은 **None** (기본값), **Hash** 및 **dynamicrange**입니다.<br>파티션 옵션을 사용 하도록 설정 하는 경우 (즉, `None`) Teradata에서 데이터를 동시에 로드 하는 병렬 처리 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 설정에 의해 제어 됩니다. | 아닙니다. |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정 합니다. <br>Partition 옵션을 `None`하지 않으면 적용 합니다. | 아닙니다. |
| partitionColumnName | 병렬 복사를 위해 범위 파티션 또는 해시 파티션에서 사용할 원본 열의 이름을 지정 합니다. 지정 하지 않으면 테이블의 주 인덱스가 자동으로 검색 되 고 파티션 열로 사용 됩니다. <br>파티션 옵션이 `Hash` 또는 `DynamicRange`경우 적용 됩니다. 쿼리를 사용 하 여 원본 데이터를 검색 하는 경우 WHERE 절에 `?AdfHashPartitionCondition` 또는 `?AdfRangePartitionColumnName`를 후크합니다. [Teradata의 Parallel copy](#parallel-copy-from-teradata) 섹션에서 예제를 참조 하세요. | 아닙니다. |
| partitionUpperBound | 데이터를 복사할 파티션 열의 최대값입니다. <br>Partition 옵션을 `DynamicRange`경우 적용 합니다. 쿼리를 사용 하 여 원본 데이터를 검색 하는 경우 WHERE 절에 `?AdfRangePartitionUpbound` 후크 합니다. 예제는 [Teradata에서 Parallel 복사](#parallel-copy-from-teradata) 섹션을 참조 하세요. | 아닙니다. |
| partitionLowerBound | 데이터를 복사할 파티션 열의 최소값입니다. <br>파티션 옵션이 `DynamicRange`경우에 적용 됩니다. 쿼리를 사용 하 여 원본 데이터를 검색 하는 경우 WHERE 절에 `?AdfRangePartitionLowbound` 후크 됩니다. 예제는 [Teradata에서 Parallel 복사](#parallel-copy-from-teradata) 섹션을 참조 하세요. | 아닙니다. |

> [!NOTE]
>
> `RelationalSource` 형식 복사 원본은 여전히 지원 되지만 Teradata (partition options)에서 새로 기본 제공 되는 병렬 로드를 지원 하지 않습니다. 그러나 새 데이터 집합을 사용 하는 것이 좋습니다.

**예: 파티션이 없는 기본 쿼리를 사용 하 여 데이터 복사**

```json
"activities":[
    {
        "name": "CopyFromTeradata",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Teradata input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "TeradataSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-teradata"></a>Teradata의 병렬 복사

Data Factory Teradata 커넥터는 Teradata에서 병렬로 데이터를 복사 하는 기본 제공 데이터 분할을 제공 합니다. 복사 작업의 **원본** 테이블에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-teradata/connector-teradata-partition-options.png)

분할 된 복사를 사용 하도록 설정 하면 Data Factory는 Teradata 원본에 대해 병렬 쿼리를 실행 하 여 파티션당 데이터를 로드 합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 설정에 의해 제어 됩니다. 예를 들어 `parallelCopies`를 4로 설정 하는 경우 Data Factory는 지정 된 파티션 옵션과 설정에 따라 4 개의 쿼리를 동시에 생성 하 고 실행 하며 각 쿼리는 Teradata에서 데이터의 일부를 검색 합니다.

특히 Teradata에서 많은 양의 데이터를 로드 하는 경우 데이터 분할으로 병렬 복사를 사용 하도록 설정 하는 것이 좋습니다. 다음은 다양 한 시나리오에 권장 되는 구성입니다. 파일 기반 데이터 저장소로 데이터를 복사 하는 경우 폴더에 여러 파일 (폴더 이름만 지정)로 기록 하는 것이 좋습니다 .이 경우에는 단일 파일에 쓰는 것 보다 성능이 좋습니다.

| 시나리오                                                     | 추천 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 많은 테이블에서 전체 로드                                   | **파티션 옵션**: Hash. <br><br/>실행 중에 Data Factory는 PK 열을 자동으로 검색 하 고, 해시를 적용 하 고, 데이터를 파티션으로 복사 합니다. |
| 사용자 지정 쿼리를 사용 하 여 많은 양의 데이터를 로드 합니다.                 | **파티션 옵션**: Hash.<br>**쿼리**: `SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>`.<br>**파티션 열**: 해시 파티션 적용에 사용 되는 열을 지정 합니다. 지정 하지 않으면 Data Factory는 Teradata 데이터 집합에서 지정한 테이블의 PK 열을 자동으로 검색 합니다.<br><br>실행 중 Data Factory는 `?AdfHashPartitionCondition` 해시 파티션 논리로 바꾸고 Teradata로 보냅니다. |
| 범위 분할에 대해 균등 하 게 분산 된 값이 있는 정수 열이 있는 사용자 지정 쿼리를 사용 하 여 많은 양의 데이터를 로드 합니다. | **파티션 옵션**: 동적 범위 파티션.<br>**쿼리**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할 하는 데 사용 되는 열을 지정 합니다. 정수 데이터 형식의 열에 대해 분할할 수 있습니다.<br>**분할 상한** 및 **파티션**하 한: 파티션 열에 대해 필터링 하 여 하 한 범위와 상한 사이 에서만 데이터를 검색 하도록 하려면를 지정 합니다.<br><br>실행 하는 동안 Data Factory `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`및 `?AdfRangePartitionLowbound`를 각 파티션에 대한 실제 열 이름과 값 범위로 바꾸고 Teradata로 보냅니다. <br>예를 들어 하 한으로 설정 된 파티션 열 "ID"가 1로 설정 되 고 상한이 80로 설정 된 경우 병렬 복사를 4로 설정 하면 Data Factory 4 개의 파티션으로 데이터를 검색 합니다. 해당 Id는 [1, 20], [21, 40], [41, 60] 및 [61, 80] 사이에 각각 있습니다. |

**예: 해시 파티션이 있는 쿼리**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfHashPartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "Hash",
    "partitionSettings": {
        "partitionColumnName": "<hash_partition_column_name>"
    }
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "TeradataSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-teradata"></a>Teradata에 대한 데이터 형식 매핑

Teradata에서 데이터를 복사 하는 경우 다음 매핑이 적용 됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Teradata 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| BigInt |Int64 |
| Blob |Byte[] |
| Byte |Byte[] |
| ByteInt |Int16 |
| Char |string |
| Clob |string |
| 날짜 |DateTime |
| 10진수 |10진수 |
| DOUBLE |DOUBLE |
| Graphic |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| 정수 |Int32 |
| Interval Day |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Day To Hour |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Day To Minute |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Day To Second |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Hour |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Hour To Minute |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Hour To Second |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Minute |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Minute To Second |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Month |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Second |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Year |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Interval Year To Month |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| 수 |DOUBLE |
| 기간 (Date) |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| 기간 (시간) |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| 기간 (표준 시간대가 포함 된 시간) |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| Period (Timestamp) |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| 기간 (표준 시간대를 사용 하는 타임 스탬프) |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| SmallInt |Int16 |
| 시간 |timespan |
| Time With Time Zone |timespan |
| Timestamp |DateTime |
| Timestamp With Time Zone |DateTime |
| VarByte |Byte[] |
| VarChar |string |
| VarGraphic |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |
| xml |지원되지 않습니다. 원본 쿼리에서 명시적 캐스트를 적용 합니다. |


## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.


## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
