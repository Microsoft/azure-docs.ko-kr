---
title: Azure Database for PostgreSQL 간 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용 하 여 Azure Database for PostgreSQL 간에 데이터를 복사 하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/16/2019
ms.openlocfilehash: 67d59e3f733efe5a248e6763f46402302496d437
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75444383"
---
# <a name="copy-data-to-and-from-azure-database-for-postgresql-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure Database for PostgreSQL 간에 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업 기능을 사용 하 여 Azure Database for PostgreSQL에서 데이터를 복사 하는 방법을 설명 합니다. 복사 작업에 대한 일반적인 개요를 제공 하는 [Azure Data Factory 아티클의 복사 작업](copy-activity-overview.md) 을 기반으로 합니다.

이 커넥터는 [Azure Database for PostgreSQL 서비스](../postgresql/overview.md)에 대해 특수화 되어 있습니다. 온-프레미스 또는 클라우드에 있는 일반 PostgreSQL 데이터베이스에서 데이터를 복사 하려면 [PostgreSQL 커넥터](connector-postgresql.md)를 사용 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Database for PostgreSQL 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Azure Database for PostgreSQL에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 또는 지원 되는 모든 원본 데이터 저장소에서 Azure Database for PostgreSQL로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조 하세요.

Azure Data Factory는 연결을 허용하는 기본 제공 드라이버를 제공합니다. 따라서이 커넥터를 사용 하기 위해 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Database for PostgreSQL 커넥터에 한정 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Database for PostgreSQL 연결 된 서비스에 대해 지원 되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | Type 속성은 **AzurePostgreSql**로 설정 해야 합니다. | 예 |
| connectionString | Azure Database for PostgreSQL에 연결하는 ODBC 연결 문자열입니다.<br/>Azure Key Vault에 암호를 입력 하 고 `password` 구성을 연결 문자열 외부로 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 을 참조 하세요. | 예 |
| connectVia | 이 속성은 데이터 저장소에 연결 하는 데 사용할 [통합 런타임을](concepts-integration-runtime.md) 나타냅니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

일반적인 연결 문자열은 `Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>`입니다. 다음은 사용자의 사례에 따라 설정할 수 있는 추가 속성입니다.

| 속성 | Description | 옵션 | 필수 |
|:--- |:--- |:--- |:--- |
| EncryptionMethod(EM)| 드라이버와 데이터베이스 서버 간에 전송되는 데이터를 암호화하기 위해 드라이버에서 사용하는 메서드입니다. 예: `EncryptionMethod=<0/1/6>;`| 0(암호화 없음) **(기본값)** / 1(SSL) / 6(RequestSSL) | 아닙니다. |
| ValidateServerCertificate(VSC) | SSL 암호화를 사용 하는 경우 드라이버가 데이터베이스 서버에서 보낸 인증서의 유효성을 검사 하는지 여부를 결정 합니다 (암호화 방법 = 1). 예: `ValidateServerCertificate=<0/1>;`| 0(사용 안 함) **(기본값)** / 1(사용) | 아닙니다. |

**예**:

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;Password=<Password>"
        }
    }
}
```

**예**:

***Azure Key Vault에 암호 저장***

```json
{
    "name": "AzurePostgreSqlLinkedService",
    "properties": {
        "type": "AzurePostgreSql",
        "typeProperties": {
            "connectionString": "Server=<server>.postgres.database.azure.com;Database=<database>;Port=<port>;UID=<username>;",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 집합을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [Azure Data Factory의 데이터 집합](concepts-datasets-linked-services.md)을 참조 하세요. 이 섹션에서는 데이터 집합에서 지 원하는 Azure Database for PostgreSQL 속성의 목록을 제공 합니다.

Azure Database for PostgreSQL에서 데이터를 복사하려면 데이터 세트의 type 속성을 **AzurePostgreSqlTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **AzurePostgreSqlTable** 로 설정 해야 합니다. | 예 |
| tableName | 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예**:

```json
{
    "name": "AzurePostgreSqlDataset",
    "properties": {
        "type": "AzurePostgreSqlTable",
        "linkedServiceName": {
            "referenceName": "<AzurePostgreSql linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

활동을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [Azure Data Factory의 파이프라인 및 활동](concepts-pipelines-activities.md)을 참조 하세요. 이 섹션에서는 Azure Database for PostgreSQL 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="azure-database-for-postgresql-as-source"></a>Azure Database for PostgreSQL을 원본으로

Azure Database for PostgreSQL에서 데이터를 복사하려면 복사 작업의 원본 형식을 **AzurePostgreSqlSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **AzurePostgreSqlSource** 로 설정 해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요 (데이터 집합의 tableName 속성이 지정 된 경우) |

**예**:

```json
"activities":[
    {
        "name": "CopyFromAzurePostgreSql",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AzurePostgreSql input dataset name>",
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
                "type": "AzurePostgreSqlSource",
                "query": "<custom query e.g. SELECT * FROM MyTable>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-database-for-postgresql-as-sink"></a>싱크로 Azure Database for PostgreSQL

Azure Database for PostgreSQL에 데이터를 복사 하려면 복사 작업 **싱크** 섹션에서 다음 속성을 지원 합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성은 **AzurePostgreSQLSink**로 설정 해야 합니다. | 예 |
| preCopyScript | 각 실행에서 Azure Database for PostgreSQL에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정 합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. | 아닙니다. |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달 하면 Azure Database for PostgreSQL 테이블에 데이터를 삽입 합니다.<br>허용 되는 값은 행 수를 나타내는 정수입니다. | 아니요(기본값: 10,000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br>허용 되는 값은 Timespan 문자열입니다. 예를 들어 "00:30:00"(30분)입니다. | 아니요 (기본값은 00:00:30) |

**예**:

```json
"activities":[
    {
        "name": "CopyToAzureDatabaseForPostgreSQL",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure PostgreSQL output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzurePostgreSQLSink",
                "preCopyScript": "<custom SQL script>",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용은 [Azure Data Factory에서 조회 작업](control-flow-lookup-activity.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
