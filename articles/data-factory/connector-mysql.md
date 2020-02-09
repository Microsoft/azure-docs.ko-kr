---
title: Azure Data Factory를 사용하여 MySQL에서 데이터 복사
description: MySQL 데이터베이스에서 싱크로 지원되는 데이터 저장소로 데이터를 복사할 수 있게 해주는 Azure Data Factory의 MySQL 커넥터에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 03f8bffafe9ebfd95d439f920a5e00be27810c96
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444236"
---
# <a name="copy-data-from-mysql-using-azure-data-factory"></a>Azure Data Factory를 사용하여 MySQL에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-onprem-mysql-connector.md)
> * [현재 버전](connector-mysql.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 MySQL 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!NOTE]
>[Azure Database for MySQL](../mysql/overview.md) 서비스에서 데이터를 복사 하려면 특수 한 [Azure Database for MySQL 커넥터](connector-azure-database-for-mysql.md)를 사용 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 MySQL 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

MySQL 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 MySQL 커넥터는 MySQL **버전 5.6 및 5.7**을 지원합니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

Integration Runtime은 버전 3.7부터 시작하는 기본 제공 MySQL 드라이버를 제공하므로 수동으로 드라이버를 설치할 필요가 없습니다.

3\.7 이전 버전의 자체 호스팅 IR에서는 Integration Runtime 머신에 [Microsoft Windows용 MySQL 커넥터/Net](https://dev.mysql.com/downloads/connector/net/) 버전 6.6.5~6.10.7을 설치해야 합니다. 이 32 비트 드라이버는 64 비트 IR과 호환 됩니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 MySQL 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

MySQL 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **MySql**로 설정해야 합니다. | 예 |
| connectionString | Azure Database for MySQL 인스턴스에 연결하는 데 필요한 정보를 지정합니다.<br/> Azure Key Vault에 암호를 넣고, 연결 문자열에서 `password` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [전제 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

일반적인 연결 문자열은 `Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>`입니다. 사례에 따라 다음과 같은 더 많은 속성을 설정할 수 있습니다.

| 속성 | Description | 옵션 | 필수 |
|:--- |:--- |:--- |:--- |
| SSLMode | 이 옵션은 MySQL에 연결할 때 드라이버에서 SSL 암호화 및 확인을 사용하는지 여부를 지정합니다. 예: `SSLMode=<0/1/2/3/4>`| 사용 안 함(0) / 기본 설정(1) **(기본값)** / 필요(2) / VERIFY_CA(3) / VERIFY_IDENTITY(4) | 아닙니다. |
| UseSystemTrustStore | 이 옵션은 시스템 신뢰 저장소 또는 지정된 PEM 파일의 CA 인증서를 사용할지 여부를 지정합니다. 예: `UseSystemTrustStore=<0/1>;`| 사용(1) / 사용 안 함(0) **(기본값)** | 아닙니다. |

**예:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 암호 저장**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

다음 페이로드에서 MySQL 연결된 서비스를 사용하는 경우, 있는 그대로도 지원되지만 앞으로 새 옵션을 사용하는 것이 좋습니다.

**이전 페이로드:**

```json
{
    "name": "MySQLLinkedService",
    "properties": {
        "type": "MySql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 MySQL 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

MySQL에서 데이터를 복사 하는 데 지원 되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **Mysqltable** 로 설정 해야 합니다. | 예 |
| tableName | MySQL 데이터베이스의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "MySQLDataset",
    "properties":
    {
        "type": "MySqlTable",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<MySQL linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

형식화 된 데이터 집합 `RelationalTable` 사용 하 고 있는 경우에는 계속 해 서 새 데이터 집합을 사용 하는 것이 좋습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 MySQL 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="mysql-as-source"></a>MySQL을 원본으로

MySQL에서 데이터를 복사 하려면 복사 작업 **원본** 섹션에서 다음 속성을 지원 합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **Mysqlsource** 로 설정 해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromMySQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MySQL input dataset name>",
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
                "type": "MySqlSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

형식화 된 원본 `RelationalSource` 사용 하 고 있는 경우에는 계속 해 서 새 항목을 사용 하는 것이 좋습니다.

## <a name="data-type-mapping-for-mysql"></a>MySQL에 대한 데이터 형식 매핑

MySQL에서 데이터를 복사하는 경우 MySQL 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| MySQL 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| `bigint` |`Int64` |
| `bigint unsigned` |`Decimal` |
| `bit(1)` |`Boolean` |
| `bit(M), M>1`|`Byte[]`|
| `blob` |`Byte[]` |
| `bool` |`Int16` |
| `char` |`String` |
| `date` |`Datetime` |
| `datetime` |`Datetime` |
| `decimal` |`Decimal, String` |
| `double` |`Double` |
| `double precision` |`Double` |
| `enum` |`String` |
| `float` |`Single` |
| `int` |`Int32` |
| `int unsigned` |`Int64`|
| `integer` |`Int32` |
| `integer unsigned` |`Int64` |
| `long varbinary` |`Byte[]` |
| `long varchar` |`String` |
| `longblob` |`Byte[]` |
| `longtext` |`String` |
| `mediumblob` |`Byte[]` |
| `mediumint` |`Int32` |
| `mediumint unsigned` |`Int64` |
| `mediumtext` |`String` |
| `numeric` |`Decimal` |
| `real` |`Double` |
| `set` |`String` |
| `smallint` |`Int16` |
| `smallint unsigned` |`Int32` |
| `text` |`String` |
| `time` |`TimeSpan` |
| `timestamp` |`Datetime` |
| `tinyblob` |`Byte[]` |
| `tinyint` |`Int16` |
| `tinyint unsigned` |`Int16` |
| `tinytext` |`String` |
| `varchar` |`String` |
| `year` |`Int` |


## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
