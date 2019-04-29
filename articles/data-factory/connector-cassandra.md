---
title: Azure Data Factory를 사용하여 Cassandra에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인에서 복사 작업을 사용하여 Cassandra에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
ms.openlocfilehash: 743dad6032547f8f535543413adff416efb56ac0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640092"
---
# <a name="copy-data-from-cassandra-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Cassandra에서 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-onprem-cassandra-connector.md)
> * [현재 버전](connector-cassandra.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Cassandra 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Cassandra 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Cassandra 커넥터는 다음을 지원합니다.

- Cassandra **버전 2.x 및 3.x**.
- **Basic** 또는 **Anonymous** 인증을 사용하여 데이터를 복사합니다.

>[!NOTE]
>자체 호스팅 Integration Runtime에서 활동 실행의 경우 Cassandra 3.x는 IR 버전 3.7 이상에서 지원됩니다.

## <a name="prerequisites"></a>필수 조건

공개적으로 액세스할 수 없는 Cassandra 데이터베이스에서 데이터를 복사하려면 자체 호스팅 통합 런타임을 설정해야 합니다. 자세한 내용은 [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md) 문서를 참조하세요. 통합 런타임은 기본 제공 Cassandra 드라이버를 제공하므로 Cassandra 간에 데이터를 복사할 때 수동으로 드라이버를 설치할 필요가 없습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Cassandra 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Cassandra 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 |type 속성을 다음으로 설정해야 합니다. **Cassandra** |예 |
| host |Cassandra 서버에 대한 하나 이상의 IP 주소 또는 호스트 이름.<br/>모든 서버에 동시에 연결하려면 쉼표로 구분된 IP 주소 또는 호스트 이름 목록을 지정합니다. |예 |
| 포트 |Cassandra 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. |아니요(기본값: 9042) |
| authenticationType | Cassandra 데이터베이스에 연결하는 데 사용되는 인증 형식입니다.<br/>허용되는 값은 다음과 같습니다. **기본** 및 **익명**. |예 |
| 사용자 이름 |사용자 계정의 사용자 이름을 지정합니다. |예. authenticationType은 Basic으로 설정됩니다. |
| 암호 |사용자 계정으로 password를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예. authenticationType은 Basic으로 설정됩니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

>[!NOTE]
>현재 연결에 SSL을 사용한 Cassandra 연결은 지원되지 않습니다.

**예제:**

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "Cassandra",
        "typeProperties": {
            "host": "<host>",
            "authenticationType": "Basic",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 세트 문서를 참조하세요. 이 섹션에서는 Cassandra 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Cassandra에서 데이터를 복사하려면 데이터 세트의 type 속성을 **CassandraTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **CassandraTable** | 예 |
| keyspace |Cassandra 데이터베이스의 키스페이스 또는 스키마의 이름입니다. |아니요("CassandraSource"에 대해 "query"가 지정되지 않은 경우) |
| tableName |Cassandra 데이터베이스에 있는 테이블의 이름입니다. |아니요("CassandraSource"에 대해 "query"가 지정되지 않은 경우) |

**예제:**

```json
{
    "name": "CassandraDataset",
    "properties": {
        "type": "CassandraTable",
        "linkedServiceName": {
            "referenceName": "<Cassandra linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "keySpace": "<keyspace name>",
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성


작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Cassandra 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="cassandra-as-source"></a>Cassandra를 원본으로

Cassandra에서 데이터를 복사하려면 복사 작업의 원본 형식을 **CassandraSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **CassandraSource** | 예 |
| 쿼리 |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. SQL-92 쿼리 또는 CQL 쿼리입니다. [CQL 참조](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)를 참조하세요. <br/><br/>SQL 쿼리를 사용할 경우 **keyspace name.table name** 을 지정하여 쿼리하려는 테이블을 나타냅니다. |아니요(데이터 세트의 "tableName" 및 "keyspace"가 정의된 경우). |
| consistencyLevel |일관성 수준은 클라이언트 애플리케이션에 데이터를 반환하기 전에 읽기 요청에 응답해야 하는 복제본 수를 지정합니다. Cassandra는 데이터의 지정된 수의 복제본이 읽기 요청을 충족하는지 확인합니다. 자세한 내용은 [데이터 일관성 구성](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) 을 참조하세요.<br/><br/>허용되는 값은 다음과 같습니다. **ONE**, **TWO**, **THREE**, **QUORUM**, **ALL**, **LOCAL_QUORUM**, **EACH_QUORUM** 및 **LOCAL_ONE**. |아니요(기본값: `ONE`) |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromCassandra",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cassandra input dataset name>",
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
                "type": "CassandraSource",
                "query": "select id, firstname, lastname from mykeyspace.mytable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-cassandra"></a>Cassandra에 대한 데이터 형식 매핑

Cassandra에서 데이터를 복사하는 경우 Cassandra 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Cassandra 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |BOOLEAN |
| DECIMAL |Decimal |
| DOUBLE |DOUBLE |
| FLOAT |Single |
| INET |String |
| INT |Int32 |
| TEXT |String |
| TIMESTAMP |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |DECIMAL |

> [!NOTE]
> 컬렉션 형식(맵, 집합, 목록 등)에 대해서는 [가상 테이블을 사용하여 Cassandra 컬렉션 형식으로 작업](#work-with-collections-using-virtual-table) 섹션을 참조하세요.
>
> 사용자 정의 형식은 지원되지 않습니다.
>
> 이진 열의 길이와 문자열 열 길이는 4000보다 클 수 없습니다.
>

## <a name="work-with-collections-using-virtual-table"></a>가상 테이블을 사용하여 컬렉션으로 작업

Azure Data Factory는 기본 제공 ODBC 드라이버를 사용하여 Cassandra 데이터베이스에 연결하고 데이터를 복사합니다. 맵, 집합 및 목록을 포함하는 컬렉션 형식의 경우 드라이버는 데이터를 해당 가상 테이블로 다시 정규화합니다. 특히, 테이블에 컬렉션 열이 포함되어 있으면 드라이버는 다음 가상 테이블을 생성합니다.

* **기본 테이블**: 컬렉션 열을 제외하고 실제 테이블과 동일한 데이터가 포함되어 있습니다. 기본 테이블은 나타내는 실제 테이블과 동일한 이름을 사용합니다.
* **가상 테이블** : 컬렉션 열에 대해 생성되며, 중첩된 데이터를 확장합니다. 컬렉션을 나타내는 가상 테이블 이름은 실제 테이블의 이름, 구분 기호 "*vt*" 및 열 이름을 사용하여 지정합니다.

가상 테이블은 실제 테이블의 데이터를 나타내며, 드라이버가 정규화되지 않은 데이터에 액세스할 수 있도록 합니다. 자세한 내용은 예제 섹션을 참조하세요. 가상 테이블을 쿼리 및 조인하여 Cassandra 컬렉션의 콘텐츠에 액세스할 수 있습니다.

### <a name="example"></a>예

예를 들어 다음 "ExampleTable"은 "pk_int"라는 정수 기본 키 열, value라는 텍스트 열, 목록 열, 맵 열, 집합 열("StringSet")을 포함하는 Cassandra 데이터베이스 테이블입니다.

| pk_int | 값 | 나열 | Map | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"sample value 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"sample value 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

드라이버는 이 단일 테이블을 나타내는 여러 개의 가상 테이블을 생성합니다. 가상 테이블의 외래 키 열은 실제 테이블의 기본 키 열을 참조하고, 가상 테이블 행에 해당하는 실제 테이블 행을 나타냅니다.

첫 번째 가상 테이블은 다음 테이블에 표시된 "ExampleTable"이라는 기본 테이블입니다. 

| pk_int | 값 |
| --- | --- |
| 1 |"sample value 1" |
| 3 |"sample value 3" |

기본 테이블에는 컬렉션을 제외하고 원래 데이터베이스 테이블과 동일한 데이터가 포함됩니다. 컬렉션은 테이블에서 생략된 후 다른 가상 테이블에서 확장됩니다.

다음 표에서는 List, Map 및 StringSet 열의 데이터를 다시 정규화하는 가상 테이블을 보여 줍니다. 이름이 "_index" 또는 "_key"로 끝나는 열은 원본 목록이나 맵 내의 데이터 위치를 나타냅니다. 이름이 "_value"로 끝나는 열에는 컬렉션에서 확장된 데이터가 포함됩니다.

**테이블 "ExampleTable_vt_List":**

| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

**테이블 "ExampleTable_vt_Map":**

| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

**테이블 "ExampleTable_vt_StringSet":**

| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |b |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
