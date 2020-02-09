---
title: Azure Data Factory를 사용 하 여 Oracle 간 데이터 복사
description: Data Factory를 사용 하 여 지원 되는 원본 저장소에서 Oracle 데이터베이스로 데이터를 복사 하거나 Oracle에서 지원 되는 싱크 저장소로 데이터를 복사 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: 736cf03b58ec09b291c91857177a32c7dad89c6a
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892052"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Oracle 간 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-onprem-oracle-connector.md)
> * [현재 버전](connector-oracle.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 Oracle 데이터베이스 간에 데이터를 복사 하는 방법을 설명 합니다. [복사 작업 개요](copy-activity-overview.md)를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Oracle 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Oracle 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 지원되는 모든 원본 데이터 저장소의 데이터를 Oracle에 복사할 수도 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히이 Oracle 커넥터는 다음을 지원 합니다.

- Oracle 데이터베이스의 다음 버전:
    - Oracle 18c R1 (18.1) 이상
    - Oracle 12c R1 (12.1) 이상
    - Oracle 11g R1 (11.1) 이상
    - Oracle 10g R1 (10.1) 이상
    - Oracle 9i R2 (9.2) 이상
    - Oracle 8i R3 (8.1.7) 이상
    - Oracle Database Cloud Exadata Service
- 기본 또는 OID 인증을 사용 하 여 데이터 복사
- Oracle 원본에서의 병렬 복사 자세한 내용은 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조 하세요.

> [!Note]
> Oracle 프록시 서버는 지원되지 않습니다.

## <a name="prerequisites"></a>필수 조건

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)] 

통합 런타임은 기본 제공 Oracle 드라이버를 제공합니다. 따라서 Oracle 데이터 복사 작업에는 드라이버를 수동으로 설치할 필요가 없습니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Oracle 커넥터에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Oracle 연결 된 서비스는 다음 속성을 지원 합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **Oracle**로 설정해야 합니다. | 예 |
| connectionString | Oracle 데이터베이스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. <br/>Azure Key Vault에 암호를 입력 하 고 `password` 구성을 연결 문자열 외부로 끌어올 수도 있습니다. 자세한 내용은 다음 샘플을 참조 하 고 [Azure Key Vault에 자격 증명을 저장](store-credentials-in-key-vault.md) 하세요. <br><br>**지원되는 연결 유형**: 데이터베이스를 식별하기 위해 **Oracle SID** 또는 **Oracle 서비스 이름**을 사용할 수 있습니다.<br>- SID를 사용하는 경우: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- 서비스 이름을 사용하는 경우: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>고급 Oracle 기본 연결 옵션의 경우 Tnsnames에 항목을 추가 하도록 선택할 수 있습니다 [. ](http://www.orafaq.com/wiki/Tnsnames.ora)Oracle 서버에 있는 ORA 파일을 선택 하 고 ADF oracle 연결 된 서비스에서 Oracle 서비스 이름 연결 유형을 사용 하도록 선택 하 고 해당 하는 서비스 이름을 구성 합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. [전제 조건](#prerequisites) 섹션에서 자세히 알아보세요. 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |아닙니다. |

>[!TIP]
>"ORA-01025: UPI 매개 변수가 범위를 벗어났습니다" 라는 오류 메시지가 표시 되 고 Oracle 버전이 8i 인 경우 연결 문자열에 `WireProtocolMode=1`를 추가 합니다. 그런 다음, 다시 시도하세요.

경우에 따라 연결 문자열에서 설정할 수 있는 추가 연결 속성이 있습니다.

| 속성 | Description | 허용되는 값 |
|:--- |:--- |:--- |
| ArraySize |단일 네트워크 왕복에서 커넥터가 인출할 수 있는 바이트 수입니다. 예: `ArraySize=‭10485760‬`.<br/><br/>값이 클수록 네트워크를 통해 데이터를 인출 하는 횟수가 줄어들어 처리량이 증가 합니다. 값이 작을수록 서버에서 데이터를 전송할 때까지 대기 하는 지연 시간이 줄어들기 때문에 응답 시간이 증가 합니다. | 1에서 4294967296 사이의 정수입니다 (4gb). 기본값은 `60000`입니다. 값 1은 바이트 수를 정의 하지 않지만 정확히 하나의 데이터 행에 대한 공간 할당을 나타냅니다. |

Oracle 연결에서 암호화를 사용하도록 설정하려면 다음 두 가지 옵션이 있습니다.

-   3DES ( **TRIPLE DES) 및 AES(Advanced Encryption Standard) (AES)** 를 사용 하려면 oracle 서버 쪽에서 Oracle Advanced SECURITY (OAS)로 이동 하 여 암호화 설정을 구성 합니다. 자세한 내용은이 [Oracle 설명서](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759)를 참조 하세요. Oracle ADF (응용 프로그램 개발 프레임 워크) 커넥터는 Oracle에 대한 연결을 설정할 때 OAS에서 구성한 암호화 방법을 자동으로 협상 합니다.

-   **SSL**을 사용 하려면:

    1.  SSL 인증서 정보를 가져옵니다. SSL 인증서의 DER (Distinguished Encoding Rules)로 인코딩된 인증서 정보를 가져오고, 출력을 저장 합니다 (-----Begin Certificate ... End Certificate -----)을 텍스트 파일로 저장합니다.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **예:** DERcert .cer에서 인증서 정보를 추출한 다음 해당 출력을 cert .txt에 저장 합니다.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  `keystore` 또는 `truststore`를 빌드합니다. 다음 명령은 암호를 사용 하거나 사용 하지 않고 `truststore` 파일을 PKCS 12 형식으로 만듭니다.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **예:** 암호를 사용 하 여 MyTrustStoreFile 이라는 PKCS12 `truststore` 파일을 만듭니다.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  자체 호스팅 IR 컴퓨터에 `truststore` 파일을 저장 합니다. 예를 들어 파일을 C:\mytrustfilefilefilefilefilefilefile.
    4.  Azure Data Factory에서 `EncryptionMethod=1` 및 해당 `TrustStore`/`TrustStorePassword`값을 사용 하 여 Oracle 연결 문자열을 구성 합니다. `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`)을 입력합니다.

**예:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
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
## <a name="dataset-properties"></a>데이터 세트 속성

이 섹션에는 Oracle 데이터 세트에서 지원하는 속성의 목록을 제공합니다. 데이터 집합을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md)을 참조 하세요. 

Oracle 간에 데이터를 복사 하려면 데이터 집합의 type 속성을 `OracleTable`로 설정 합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성을 `OracleTable`으로 설정 해야 합니다. | 예 |
| 스키마 | 스키마의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| 테이블 | 테이블/뷰의 이름입니다. |원본에는 아니요이고 싱크에는 예입니다  |
| tableName | 스키마가 포함 된 테이블/뷰의 이름입니다. 이 속성은 이전 버전과의 호환성을 위해 지원 됩니다. 새 워크 로드의 경우 `schema` 및 `table`를 사용 합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

**예:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

이 섹션에서는 Oracle 원본 및 싱크에서 지원하는 속성 목록을 제공합니다. 작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="oracle-as-source"></a>Oracle을 원본으로

>[!TIP]
>데이터 분할을 사용 하 여 Oracle에서 데이터를 효율적으로 로드 하려면 [oracle의 병렬 복사](#parallel-copy-from-oracle)에서 자세히 알아보세요.

Oracle에서 데이터를 복사 하려면 복사 작업의 원본 형식을 `OracleSource`로 설정 합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 `OracleSource`으로 설정 해야 합니다. | 예 |
| oracleReaderQuery | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예제는 `"SELECT * FROM MyTable"`입니다.<br>분할 된 로드를 사용 하도록 설정 하는 경우 쿼리에 해당 하는 기본 제공 파티션 매개 변수를 후크 해야 합니다. 예제는 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조 하세요. | 아닙니다. |
| partitionOptions | Oracle에서 데이터를 로드 하는 데 사용 되는 데이터 분할 옵션을 지정 합니다. <br>허용 되는 값은 **None** (기본값), **PhysicalPartitionsOfTable** 및 **dynamicrange**입니다.<br>파티션 옵션을 사용 하도록 설정 하는 경우 (즉, `None`), Oracle 데이터베이스에서 데이터를 동시에 로드 하는 병렬 처리 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 설정에 의해 제어 됩니다. | 아닙니다. |
| partitionSettings | 데이터 분할에 대한 설정 그룹을 지정 합니다. <br>파티션 옵션이 `None`되지 않은 경우에 적용 됩니다. | 아닙니다. |
| partitionNames | 복사 해야 하는 실제 파티션 목록입니다. <br>파티션 옵션이 `PhysicalPartitionsOfTable`경우에 적용 됩니다. 쿼리를 사용 하 여 원본 데이터를 검색 하는 경우 WHERE 절에 `?AdfTabularPartitionName` 후크 됩니다. 예제는 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조 하세요. | 아닙니다. |
| partitionColumnName | 병렬 복사를 위해 범위 분할에서 사용할 원본 열의 이름을 **정수 형식으로** 지정 합니다. 지정 하지 않으면 테이블의 기본 키가 자동으로 검색 되 고 파티션 열로 사용 됩니다. <br>파티션 옵션이 `DynamicRange`경우에 적용 됩니다. 쿼리를 사용 하 여 원본 데이터를 검색 하는 경우 WHERE 절에 `?AdfRangePartitionColumnName` 후크 됩니다. 예제는 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조 하세요. | 아닙니다. |
| partitionUpperBound | 데이터를 복사할 파티션 열의 최대값입니다. <br>파티션 옵션이 `DynamicRange`경우에 적용 됩니다. 쿼리를 사용 하 여 원본 데이터를 검색 하는 경우 WHERE 절에 `?AdfRangePartitionUpbound` 후크 됩니다. 예제는 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조 하세요. | 아닙니다. |
| partitionLowerBound | 데이터를 복사할 파티션 열의 최소값입니다. <br>파티션 옵션이 `DynamicRange`경우에 적용 됩니다. 쿼리를 사용 하 여 원본 데이터를 검색 하는 경우 WHERE 절에 `?AdfRangePartitionLowbound` 후크 됩니다. 예제는 [Oracle에서 병렬 복사](#parallel-copy-from-oracle) 섹션을 참조 하세요. | 아닙니다. |

**예: 파티션이 없는 기본 쿼리를 사용 하 여 데이터 복사**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="oracle-as-sink"></a>Oracle을 싱크로

Oracle에 데이터를 복사 하려면 복사 작업의 싱크 형식을 `OracleSink`로 설정 합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 type 속성을 `OracleSink`설정 해야 합니다. | 예 |
| writeBatchSize | 버퍼 크기가 `writeBatchSize`에 도달 하면 SQL 테이블에 데이터를 삽입 합니다.<br/>허용되는 값은 정수(행 수)입니다. |아니요(기본값: 10,000) |
| writeBatchTimeout | 시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다.<br/>허용되는 값은 시간 범위입니다. 예를 들어 "00:30:00"(30분)입니다. | 아닙니다. |
| preCopyScript | 각 실행 시 Oracle에 데이터를 쓰기 전에 실행할 복사 작업에 대한 SQL 쿼리를 지정 합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리할 수 있습니다. | 아닙니다. |

**예:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Oracle의 병렬 복사

Data Factory Oracle 커넥터는 Oracle의 데이터를 병렬로 복사 하기 위한 기본 제공 데이터 분할을 제공 합니다. 복사 작업의 **원본** 탭에서 데이터 분할 옵션을 찾을 수 있습니다.

![파티션 옵션의 스크린샷](./media/connector-oracle/connector-oracle-partition-options.png)

분할 된 복사를 사용 하도록 설정 하면 Data Factory Oracle 원본에 대해 병렬 쿼리를 실행 하 여 파티션으로 데이터를 로드 합니다. 병렬 수준은 복사 작업의 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 설정에 의해 제어 됩니다. 예를 들어 `parallelCopies`를 4로 설정 하는 경우 Data Factory는 지정 된 파티션 옵션과 설정을 기반으로 4 개의 쿼리를 동시에 생성 하 고 실행 하며 각 쿼리는 Oracle 데이터베이스에서 데이터의 일부를 검색 합니다.

특히 Oracle 데이터베이스에서 대량의 데이터를 로드 하는 경우 데이터 분할으로 병렬 복사를 사용 하도록 설정 하는 것이 좋습니다. 다음은 다양 한 시나리오에 권장 되는 구성입니다. 파일 기반 데이터 저장소로 데이터를 복사 하는 경우 폴더에 여러 파일 (폴더 이름만 지정)로 기록 하는 것이 좋습니다 .이 경우에는 단일 파일에 쓰는 것 보다 성능이 좋습니다.

| 시나리오                                                     | 추천 설정                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| 실제 파티션이 있는 초대형 테이블에서 전체 로드          | **파티션 옵션**: 테이블의 실제 파티션입니다. <br><br/>실행 중에는 Data Factory에서 실제 파티션을 자동으로 검색 하 고 파티션으로 데이터를 복사 합니다. |
| 데이터 분할을 위한 정수 열을 포함 하는 동시에 실제 파티션이 없는 많은 테이블에서 전체 로드 | **파티션 옵션**: 동적 범위 파티션.<br>**파티션 열**: 데이터를 분할 하는 데 사용 되는 열을 지정 합니다. 지정 하지 않으면 기본 키 열이 사용 됩니다. |
| 실제 파티션에 사용자 지정 쿼리를 사용 하 여 많은 양의 데이터를 로드 합니다. | **파티션 옵션**: 테이블의 실제 파티션입니다.<br>**쿼리**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**파티션 이름**: 데이터를 복사할 파티션 이름을 지정 합니다. 지정 하지 않으면 Data Factory Oracle 데이터 집합에서 지정한 테이블의 실제 파티션을 자동으로 검색 합니다.<br><br>실행 중 Data Factory는 `?AdfTabularPartitionName`를 실제 파티션 이름으로 바꾸고 Oracle로 보냅니다. |
| 데이터 분할을 위한 정수 열을 포함 하 여 실제 파티션이 없는 사용자 지정 쿼리를 사용 하 여 많은 양의 데이터를 로드 합니다. | **파티션 옵션**: 동적 범위 파티션.<br>**쿼리**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**파티션 열**: 데이터를 분할 하는 데 사용 되는 열을 지정 합니다. 정수 데이터 형식의 열에 대해 분할할 수 있습니다.<br>**분할 상한** 및 **파티션**하 한: 파티션 열에 대해 필터링 하 여 하 한 및 상한 간에만 데이터를 검색 하도록 지정 합니다.<br><br>실행 하는 동안 Data Factory `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`및 `?AdfRangePartitionLowbound`를 각 파티션에 대한 실제 열 이름과 값 범위로 바꾸고 Oracle로 보냅니다. <br>예를 들어 파티션 열 "ID"가 하한값을 1로 설정 하 고 상한이 80로 설정 된 경우 병렬 복사를 4로 설정 하면 Data Factory 4 개의 파티션으로 데이터를 검색 합니다. 해당 Id는 [1, 20], [21, 40], [41, 60] 및 [61, 80] 사이에 각각 있습니다. |

**예: 실제 파티션이 있는 쿼리**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**예: 동적 범위 파티션이 있는 쿼리**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Oracle에 대한 데이터 형식 매핑

Oracle에서 Oracle로 데이터를 복사 하는 경우 다음 매핑이 적용 됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Oracle 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(Oracle 10g 이상에서만 지원됨) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| INTEGER |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| timestamp |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |수 |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 데이터 형식 INTERVAL YEAR TO MONTH 및 INTERVAL DAY TO SECOND는 지원되지 않습니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="next-steps"></a>다음 단계
Data Factory에서 복사 활동을 통해 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
