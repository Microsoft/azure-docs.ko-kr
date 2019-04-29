---
title: Azure Data Factory를 사용하여 ODBC 원본에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 OData 원본에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/19/2018
ms.author: jingwang
ms.openlocfilehash: f14c8f8ef9f0e59ac35dd7346bf37cc07f2cfb19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60711461"
---
# <a name="copy-data-from-and-to-odbc-data-stores-using-azure-data-factory"></a>Azure Data Factory를 사용하여 ODBC 데이터 저장소 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-odbc-connector.md)
> * [현재 버전](connector-odbc.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 ODBC 데이터 저장소 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

ODBC 원본에서 지원되는 싱크 데이터 저장소로 또는 지원되는 원본 데이터 저장소에서 ODBC 싱크로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 ODBC 커넥터는 **Basic** 또는 **Anonymous** 인증을 사용하여 **ODBC 호환 데이터 저장소** 간의 데이터 복사를 지원합니다.

## <a name="prerequisites"></a>필수 조건

ODBC 커넥터를 사용하려면 다음을 수행해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.
- 통합 런타임 컴퓨터에 데이터 저장소에 대한 ODBC 드라이버를 설치합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 ODBC 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

ODBC 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성을 다음으로 설정해야 합니다. **Odbc** | 예 |
| connectionString | 자격 증명 부분을 제외한 연결 문자열입니다. `"Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;"`와 같은 패턴으로 연결 문자열을 지정하거나 `"DSN=<name of the DSN on IR machine>;"`을 통해 Integration Runtime 컴퓨터에 설정한 시스템 DSN(데이터 원본 이름)을 사용할 수 있습니다(이에 따라 연결된 서비스에서 자격 증명 부분도 계속 지정해야 함).<br>이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다.| 예 |
| authenticationType | ODBC 데이터 저장소에 연결하는 데 사용되는 인증 형식입니다.<br/>허용되는 값은 다음과 같습니다. **기본** 및 **익명**. | 예 |
| userName | 기본 인증을 사용하는 경우 사용자 이름을 지정합니다. | 아닙니다. |
| 암호 | userName에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 아닙니다. |
| 자격 증명 | 드라이버 관련 속성 값 형식에 지정된 연결 문자열의 액세스 자격 증명 부분입니다. 예: `"RefreshToken=<secret refresh token>;"`. 이 필드를 SecureString으로 표시합니다. | 아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예제 1: 기본 인증 사용**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
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

**예제 2: 익명 인증 사용**

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<connection string>"
            },
            "authenticationType": "Anonymous",
            "credential": {
                "type": "SecureString",
                "value": "RefreshToken=<secret refresh token>;"
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 세트 문서를 참조하세요. 이 섹션에서는 ODBC 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

ODBC 호환 데이터 저장소 간에 데이터를 복사하려면 데이터 세트의 type 속성을 **RelationalTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **RelationalTable** | 예 |
| tableName | ODBC 데이터 저장소에 있는 테이블의 이름입니다. | 원본: 아니요(작업 원본에서 "query"가 지정된 경우)<br/>싱크: 예 |

**예제**

```json
{
    "name": "ODBCDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<ODBC linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 ODBC 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="odbc-as-source"></a>ODBC를 원본으로

ODBC 호환 데이터 저장소에서 데이터를 복사하려면 복사 작업의 원본 형식을 **RelationalSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **RelationalSource** | 예 |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ODBC input dataset name>",
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
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="odbc-as-sink"></a>ODBC를 싱크로

ODBC 호환 데이터 저장소에 데이터를 복사하려면 복사 작업의 싱크 형식을 **OdbcSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 싱크의 type 속성을 다음으로 설정해야 합니다. **OdbcSink** | 예 |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예제: “00:30:00”(30분) |아닙니다. |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 0 - 자동 검색됨) |
| preCopyScript |각 실행 시 데이터 저장소에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. |아닙니다. |

> [!NOTE]
> "WriteBatchSize"의 경우 설정하지 않으면(자동 검색됨) 복사 작업이 먼저 드라이버의 배치 작업 지원 여부를 검색하여 지원할 경우 10000으로 설정하거나 지원하지 않을 경우 1로 설정합니다. 0이 아닌 값을 명시적으로 설정하면 복사 작업이 값을 부여하고 드라이버가 배치 작업을 지원하지 않는 경우 런타임 시 실패합니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyToODBC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ODBC output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OdbcSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

## <a name="ibm-informix-source"></a>IBM Informix 원본

일반 ODBC 커넥터를 사용하여 IBM Informix 데이터베이스에서 데이터를 복사할 수 있습니다.

데이터 저장소에 액세스할 수 있는 컴퓨터에서 자체 호스팅 통합 런타임을 설정합니다. Integration Runtime은 Informix용 ODBC 드라이버를 사용하여 데이터 저장소에 연결합니다. 따라서 같은 컴퓨터에 아직 설치되지 않은 경우 해당 드라이버를 설치합니다. 예를 들어 "IBM INFORMIX ODBC 드라이버(64비트)"를 사용할 수 있습니다. 자세한 내용은 [필수 조건](#prerequisites) 섹션을 참조하세요.

Data Factory 솔루션에서 Informix 원본을 사용하기 전에 [연결 문제 해결](#troubleshoot-connectivity-issues) 섹션의 지침을 사용하여 Integration Runtime에서 데이터 저장소에 연결할 수 있는지 확인합니다.

다음 예제에서와 같이 IBM Informix 데이터 저장소를 Azure data factory에 연결할 ODBC 연결 된 서비스를 만듭니다.

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "<Informix connection string or DSN>"
            },
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

복사 작업에서 ODBC 데이터 저장소를 원본/싱크 데이터 저장소로 사용하는 데 대한 자세한 개요를 보려면 문서를 처음부터 읽어보세요.

## <a name="microsoft-access-source"></a>Microsoft Access 원본

일반 ODBC 커넥터를 사용하여 Microsoft Access 데이터베이스에서 데이터를 복사할 수 있습니다.

데이터 저장소에 액세스할 수 있는 컴퓨터에서 자체 호스팅 통합 런타임을 설정합니다. Integration Runtime은 Microsoft Access용 ODBC 드라이버를 사용하여 데이터 저장소에 연결합니다. 따라서 같은 컴퓨터에 아직 설치되지 않은 경우 해당 드라이버를 설치합니다. 자세한 내용은 [필수 조건](#prerequisites) 섹션을 참조하세요.

Data Factory 솔루션에서 Microsoft Access 원본을 사용하기 전에 [연결 문제 해결](#troubleshoot-connectivity-issues) 섹션의 지침을 사용하여 Integration Runtime에서 데이터 저장소에 연결할 수 있는지 확인합니다.

다음 예제와 같이 Microsoft Access 데이터베이스를 Azure Data Factory에 연결하는 ODBC 연결된 서비스를 만듭니다.

```json
{
    "name": "MicrosoftAccessLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={Microsoft Access Driver (*.mdb, *.accdb)};Dbq=<path to your DB file e.g. C:\\mydatabase.accdb>;"
            },
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

복사 작업에서 ODBC 데이터 저장소를 원본/싱크 데이터 저장소로 사용하는 데 대한 자세한 개요를 보려면 문서를 처음부터 읽어보세요.

## <a name="sap-hana-sink"></a>SAP HANA 싱크

>[!NOTE]
>SAP HANA 데이터 저장소에서 데이터를 복사하려면 네이티브 [SAP HANA 커넥터](connector-sap-hana.md)를 참조하세요. SAP HANA로 데이터를 복사하려면 이 지침에 따라 ODBC 커넥터를 사용하세요. 따라서 형식이 다른 SAP HANA 커넥터 및 ODBC 커넥터에 대한 연결된 서비스는 재사용할 수 없습니다.
>

일반 ODBC 커넥터를 사용하여 SAP HANA 데이터베이스에 데이터를 복사할 수 있습니다.

데이터 저장소에 액세스할 수 있는 컴퓨터에서 자체 호스팅 통합 런타임을 설정합니다. 통합 런타임은 SAP HANA용 ODBC 드라이버를 사용하여 데이터 저장소에 연결합니다. 따라서 같은 컴퓨터에 아직 설치되지 않은 경우 해당 드라이버를 설치합니다. 자세한 내용은 [필수 조건](#prerequisites) 섹션을 참조하세요.

Data Factory 솔루션에서 SAP HANA 싱크를 사용하기 전에 [연결 문제 해결](#troubleshoot-connectivity-issues) 섹션의 지침을 사용하여 Integration Runtime에서 데이터 저장소에 연결할 수 있는지 확인합니다.

다음 예제와 같이 SAP HANA 데이터 저장소를 Azure Data Factory에 연결하는 ODBC 연결된 서비스를 만듭니다.

```json
{
    "name": "SAPHANAViaODBCLinkedService",
    "properties": {
        "type": "Odbc",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Driver={HDBODBC};servernode=<HANA server>.clouddatahub-int.net:30015"
            },
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

복사 작업에서 ODBC 데이터 저장소를 원본/싱크 데이터 저장소로 사용하는 데 대한 자세한 개요를 보려면 문서를 처음부터 읽어보세요.

## <a name="troubleshoot-connectivity-issues"></a>연결 문제 해결

연결 문제를 해결하려면 **통합 런타임 구성 관리자**의 **진단** 탭을 사용합니다.

1. **통합 런타임 구성 관리자**를 시작합니다.
2. **진단** 탭으로 전환합니다.
3. "연결 테스트" 섹션에서 데이터 저장소(연결된 서비스)의 **형식**을 선택합니다.
4. 데이터 저장소에 연결하는 데 사용되는 **연결 문자열**을 지정하고 **인증**을 선택하고 **사용자 이름**, **암호** 및/또는 **자격 증명**을 입력합니다.
5. **연결 테스트**를 클릭하여 데이터 저장소에 대한 연결을 테스트합니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
