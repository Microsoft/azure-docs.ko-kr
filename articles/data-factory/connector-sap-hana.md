---
title: SAP HANA에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 SAP HANA에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: 97c277eadbd1b425c50b10d15172c13e17e20eb3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926199"
---
# <a name="copy-data-from-sap-hana-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SAP HANA에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-sap-hana-connector.md)
> * [현재 버전](connector-sap-hana.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SAP HANA 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!TIP]
>SAP 데이터 통합 시나리오에 대한 ADF의 전반적인 지원에 대한 자세한 내용은 [Azure Data Factory 백서를 사용 하 여 sap 데이터 통합](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) 에서 자세한 소개, comparsion 및 지침을 참조 하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 SAP HANA 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

SAP HANA 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 테이블을 참조하세요.

특히 이 SAP HANA 커넥터는 다음을 지원합니다.

- SAP HANA 데이터베이스의 모든 버전에서 데이터 복사
- **HANA 정보 모델** (예: 분석 및 계산 뷰)에서 데이터를 복사 하 고 **행/열 테이블**을 복사 합니다.
- **Basic** 또는 **Windows** 인증을 사용한 데이터 복사

> [!TIP]
> SAP HANA 데이터 저장소**로** 데이터를 복사하려면 일반 ODBC 커넥터를 사용합니다. 자세한 내용은 [SAP HANA 싱크](connector-odbc.md#sap-hana-sink)를 참조하세요. 따라서 형식이 다른 SAP HANA 커넥터 및 ODBC 커넥터에 대한 연결된 서비스는 재사용할 수 없습니다.

## <a name="prerequisites"></a>전제 조건

이 SAP HANA 커넥터를 사용하려면 다음을 수행해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.
- Integration Runtime 컴퓨터에 SAP HANA ODBC 드라이버를 설치합니다. SAP HANA ODBC 드라이버는 [SAP 소프트웨어 다운로드 센터](https://support.sap.com/swdc)에서 다운로드할 수 있습니다. **SAP HANA CLIENT for Windows**라는 키워드를 사용하여 검색합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP HANA 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP HANA 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **SapHana**로 설정해야 합니다. | yes |
| connectionString | **기본 인증** 또는 **Windows 인증**을 사용 하 여 SAP HANA에 연결 하는 데 필요한 정보를 지정 합니다. 다음 샘플을 참조하세요.<br>연결 문자열에서 서버/포트는 필수 (기본 포트는 30015)이 고, 기본 인증을 사용 하는 경우 사용자 이름 및 암호는 필수입니다. 고급 설정에 대한 자세한 내용은 [SAP HANA ODBC 연결 속성](<https://help.sap.com/viewer/0eec0d68141541d1b07893a39944924e/2.0.02/en-US/7cab593774474f2f8db335710b2f5c50.html>) 을 참조 하세요.<br/>Azure Key Vault에 암호를 입력 하 고 연결 문자열에서 암호 구성을 끌어올 수도 있습니다. 자세한 내용은 [Azure Key Vault 문서의 자격 증명 저장](store-credentials-in-key-vault.md) 을 참조 하세요. | yes |
| userName | Windows 인증을 사용 하는 경우 사용자 이름을 지정 합니다. 예: `user@domain.com` | 아닙니다. |
| 암호 | 사용자 계정으로 password를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |yes |

**예: 기본 인증 사용**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;UID=<userName>;PWD=<Password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예: Windows 인증 사용**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "connectionString": "SERVERNODE=<server>:<port (optional)>;",
            "userName": "<username>", 
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

다음 페이로드를 사용 하 여 SAP HANA 연결 된 서비스를 사용 하는 경우에는 계속 해 서 새 항목을 사용 하는 것이 좋습니다.

**예제:**

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server>:<port (optional)>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에는 SAP HANA 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

SAP HANA에서 데이터를 복사 하기 위해 지원 되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **SapHanaTable** 로 설정 해야 합니다. | yes |
| schema | SAP HANA 데이터베이스의 스키마 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |
| 테이블 | SAP HANA 데이터베이스에 있는 테이블의 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제:**

```json
{
    "name": "SAPHANADataset",
    "properties": {
        "type": "SapHanaTable",
        "typeProperties": {
            "schema": "<schema name>",
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP HANA linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

형식화 된 데이터 집합 `RelationalTable` 사용 하 고 있는 경우에는 계속 해 서 새 데이터 집합을 사용 하는 것이 좋습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에는 SAP HANA 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sap-hana-as-source"></a>SAP HANA를 원본으로

SAP HANA에서 데이터를 복사 하려면 복사 작업 **원본** 섹션에서 다음 속성을 지원 합니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **SapHanaSource** 로 설정 해야 합니다. | yes |
| 쿼리 | SAP HANA 인스턴스에서 데이터를 읽을 SQL 쿼리를 지정합니다. | yes |
| packetSize | 데이터를 여러 블록으로 분할 하는 네트워크 패킷 크기 (Kb)를 지정 합니다. 복사할 데이터가 많은 경우 패킷 크기를 늘리면 대부분의 경우 SAP HANA에서 읽기 속도를 높일 수 있습니다. 패킷 크기를 조정할 때 성능 테스트를 수행 하는 것이 좋습니다. | 아닙니다.<br>기본값은 2048 (2MB)입니다. |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSAPHANA",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP HANA input dataset name>",
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
                "type": "SapHanaSource",
                "query": "<SQL query for SAP HANA>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

형식화 된 복사 원본 `RelationalSource` 사용 하 고 있는 경우에는 그대로 계속 사용할 수 있지만 새 항목을 사용 하는 것이 좋습니다.

## <a name="data-type-mapping-for-sap-hana"></a>SAP HANA에 대한 데이터 형식 매핑

SAP HANA에서 데이터를 복사하는 경우 SAP HANA 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP HANA 데이터 형식 | Data Factory 중간 데이터 형식 |
| ------------------ | ------------------------------ |
| ALPHANUM           | string                         |
| BIGINT             | Int64                          |
| BINARY             | Byte[]                         |
| BINTEXT            | string                         |
| BLOB               | Byte[]                         |
| BOOL               | Byte                           |
| CLOB               | string                         |
| DATE               | DateTime                       |
| DECIMAL            | 10진수                        |
| DOUBLE             | DOUBLE                         |
| FLOAT              | DOUBLE                         |
| INTEGER            | Int32                          |
| NCLOB              | string                         |
| NVARCHAR           | string                         |
| Real               | 단일                         |
| SECONDDATE         | DateTime                       |
| SHORTTEXT          | string                         |
| SMALLDECIMAL       | 10진수                        |
| SmallInt           | Int16                          |
| STGEOMETRYTYPE     | Byte[]                         |
| STPOINTTYPE        | Byte[]                         |
| TEXT               | string                         |
| TIME               | timespan                       |
| TINYINT            | Byte                           |
| VARCHAR            | string                         |
| TIMESTAMP          | DateTime                       |
| VARBINARY          | Byte[]                         |

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
