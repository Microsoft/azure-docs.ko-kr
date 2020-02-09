---
title: Azure Data Factory를 사용 하 여 DB2에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 DB2에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: jingwang
ms.openlocfilehash: 3d3a1704b75de53bf65012329fba5f8522adff3a
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/14/2020
ms.locfileid: "75941759"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 DB2에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-onprem-db2-connector.md)
> * [현재 버전](connector-db2.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 DB2 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 DB2 데이터베이스 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

DB2 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히, 이 DB2 커넥터는 DRDA(분산 관계형 데이터베이스 아키텍처) SQLAM(SQL Access Manager) 버전 9, 10 및 11과 함께 다음 IBM DB2 플랫폼 버전을 지원합니다.

* Z/OS 용 IBM DB2 12.1
* z/OS용 IBM DB2 11.1
* z/OS용 IBM DB2 10.1
* i용 IBM DB2 7.3
* i용 IBM DB2 7.2
* i용 IBM DB2 7.1
* LUW용 IBM DB2 11
* LUW용 IBM DB2 10.5
* LUW용 IBM DB2 10.1

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

통합 런타임은 기본 제공 DB2 드라이버를 제공하므로 DB2에서 데이터를 복사할 때 수동으로 드라이버를 설치할 필요가 없습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 DB2 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

DB2 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **Db2**로 설정해야 합니다. | 예 |
| 서버 |DB2 서버의 이름입니다. 콜론으로 구분된 서버 이름 뒤에 포트 번호를 지정할 수 있습니다(예: `server:port`). |예 |
| 데이터베이스 |DB2 데이터베이스의 이름입니다. |예 |
| authenticationType |DB2 데이터베이스에 연결하는 데 사용되는 인증 형식입니다.<br/>허용되는 값은 **Basic**입니다. |예 |
| 사용자 이름 |DB2 데이터베이스에 연결할 사용자 이름을 지정합니다. |예 |
| password |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| packageCollection | 데이터베이스를 쿼리할 때 ADF에서 필요한 패키지를 자동으로 생성 하는 위치를 지정 합니다. | 아닙니다. |
| certificateCommonName | SSL(Secure Sockets Layer) (SSL) 또는 TLS (전송 계층 보안) 암호화를 사용 하는 경우 인증서 일반 이름에 값을 입력 해야 합니다. | 아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [전제 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

> [!TIP]
> `The package corresponding to an SQL statement execution request was not found. SQLSTATE=51002 SQLCODE=-805`설명 하는 오류 메시지가 표시 되는 경우 사용자에 게 필요한 패키지가 만들어지지 않습니다. 기본적으로 ADF는 DB2에 연결 하는 데 사용한 사용자로 이름이 지정 된 패키지를 만들려고 시도 합니다. 데이터베이스를 쿼리할 때 ADF에서 필요한 패키지를 만들 위치를 지정 하려면 패키지 컬렉션 속성을 지정 합니다.

**예:**

```json
{
    "name": "Db2LinkedService",
    "properties": {
        "type": "Db2",
        "typeProperties": {
            "server": "<servername:port>",
            "database": "<dbname>",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 DB2 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

DB2에서 데이터를 복사 하려면 다음 속성이 지원 됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **Db2Table** 로 설정 해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| 테이블 | 테이블 이름입니다. |아니요(작업 원본에서 "query"가 지정된 경우)  |
| tableName | 스키마가 있는 테이블의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원 됩니다. 새 워크 로드에 `schema` 및 `table`를 사용 합니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "Db2Table",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

형식화 된 데이터 집합 `RelationalTable` 사용 하 고 있는 경우에는 계속 해 서 새 데이터 집합을 사용 하는 것이 좋습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 DB2 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="db2-as-source"></a>DB2를 원본으로

DB2에서 데이터를 복사 하기 위해 복사 작업 **원본** 섹션에서 다음 속성이 지원 됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **Db2Source** 로 설정 해야 합니다. | 예 |
| Query | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromDB2",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<DB2 input dataset name>",
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
                "type": "Db2Source",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

형식화 된 원본 `RelationalSource` 사용 하 고 있는 경우에는 계속 해 서 새 항목을 사용 하는 것이 좋습니다.

## <a name="data-type-mapping-for-db2"></a>DB2에 대한 데이터 형식 매핑

DB2에서 데이터를 복사하는 경우 DB2 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| DB2 데이터베이스 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| BigInt |Int64 |
| 이진 |Byte[] |
| Blob |Byte[] |
| Char |String |
| Clob |String |
| 날짜 |DateTime |
| DB2DynArray |String |
| DbClob |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| 정수 |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| 숫자 |Decimal |
| Real |단일 |
| SmallInt |Int16 |
| 시간 |TimeSpan |
| 타임스탬프 |DateTime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
