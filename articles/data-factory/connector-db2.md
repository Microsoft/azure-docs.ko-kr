---
title: Azure Data Factory를 사용하여 DB2에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 DB2에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: jingwang
ms.openlocfilehash: 4bf4c5c8339c8c56d91737fa1ff62f55b9c38696
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60786376"
---
# <a name="copy-data-from-db2-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 DB2에서 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-onprem-db2-connector.md)
> * [현재 버전](connector-db2.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 DB2 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

DB2 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히, 이 DB2 커넥터는 DRDA(분산 관계형 데이터베이스 아키텍처) SQLAM(SQL Access Manager) 버전 9, 10 및 11과 함께 다음 IBM DB2 플랫폼 버전을 지원합니다.

* z/OS용 IBM DB2 11.1
* z/OS용 IBM DB2 10.1
* i용 IBM DB2 7.2
* i용 IBM DB2 7.1
* LUW용 IBM DB2 11
* LUW용 IBM DB2 10.5
* LUW용 IBM DB2 10.1

> [!TIP]
> "SQL 문 실행 요청에 해당하는 패키지가 없습니다. SQLSTATE=51002 SQLCODE=-805"라는 오류 메시지가 수신되면 이러한 OS에서 필요한 패키지가 일반 사용자용으로 생성되지 않기 때문입니다. DB2 서버 유형에 따라 다음 지침을 수행합니다.
> - i용 DB2(AS400): 고급 사용자가 복사 작업을 사용하기 전에 로그인 사용자의 컬렉션을 만들 수 있습니다. 명령: `create collection <username>`
> - z/OS 또는 LUW용 DB2: 고급 권장 계정 사용 - 패키지 권한 및 BIND, BINDADD, GRANT EXECUTE TO PUBLIC 권한이 있는 고급 사용자 또는 관리자 - 복사 작업을 한 번 실행하기 위해 복사 중에 필요한 패키지가 자동으로 생성됩니다. 그 후에는 다시 일반 사용자로 전환하여 이후 복사 실행을 수행할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

공개적으로 액세스할 수 없는 DB2 데이터베이스에서 데이터 복사를 사용하려면 자체 호스팅 통합 런타임을 설정해야 합니다. 자체 호스팅 통합 런타임에 대한 자세한 내용은 [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md) 문서를 참조하세요. 통합 런타임은 기본 제공 DB2 드라이버를 제공하므로 DB2에서 데이터를 복사할 때 수동으로 드라이버를 설치할 필요가 없습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 DB2 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

DB2 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성을 다음으로 설정해야 합니다. **Db2** | 예 |
| 서버 |DB2 서버의 이름입니다. 콜론으로 구분된 서버 이름 뒤에 포트 번호를 지정할 수 있습니다(예: `server:port`). |예 |
| 데이터베이스 |DB2 데이터베이스의 이름입니다. |예 |
| authenticationType |DB2 데이터베이스에 연결하는 데 사용되는 인증 형식입니다.<br/>허용되는 값은 다음과 같습니다. **Basic**. |예 |
| username |DB2 데이터베이스에 연결할 사용자 이름을 지정합니다. |예 |
| 암호 |사용자 이름에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

**예제:**

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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 세트 문서를 참조하세요. 이 섹션에서는 DB2 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

DB2에서 데이터를 복사하려면 데이터 세트의 type 속성을 **RelationalTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **RelationalTable** | 예 |
| tableName | DB2 데이터베이스의 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "DB2Dataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<DB2 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 DB2 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="db2-as-source"></a>DB2를 원본으로

DB2에서 데이터를 복사하려면 복사 작업의 원본 형식을 **RelationalSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **RelationalSource** | 예 |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예제:**

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
                "type": "RelationalSource",
                "query": "SELECT * FROM \"DB2ADMIN\".\"Customers\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-db2"></a>DB2에 대한 데이터 형식 매핑

DB2에서 데이터를 복사하는 경우 DB2 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| DB2 데이터베이스 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| BigInt |Int64 |
| 이진 |Byte[] |
| Blob |Byte[] |
| Char |String |
| Clob |String |
| Date |DateTime |
| DB2DynArray |String |
| DbClob |String |
| Decimal |Decimal |
| DecimalFloat |Decimal |
| Double |Double |
| Float |Double |
| Graphic |String |
| 정수  |Int32 |
| LongVarBinary |Byte[] |
| LongVarChar |String |
| LongVarGraphic |String |
| Numeric |Decimal |
| Real |Single |
| SmallInt |Int16 |
| Time |TimeSpan |
| 타임 스탬프 |DateTime |
| VarBinary |Byte[] |
| VarChar |String |
| VarGraphic |String |
| Xml |Byte[] |


## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
