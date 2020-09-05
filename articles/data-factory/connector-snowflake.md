---
title: 눈송이에서 데이터 복사 및 변환
description: Data Factory를 사용 하 여 눈송이에서 데이터를 복사 하 고 변환 하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/28/2020
ms.openlocfilehash: fa8bb310d6a088db92b3dfd8eb6d2f584e9ffab7
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181887"
---
# <a name="copy-and-transform-data-in-snowflake-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 눈송이에서 데이터 복사 및 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 눈송이에서 눈송이로 데이터를 복사 하 고 데이터 흐름을 사용 하 여 눈송이에서 데이터를 변환 하는 방법을 설명 합니다. Data Factory에 대 한 자세한 내용은 [소개 문서](introduction.md)를 참조 하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 눈송이 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 행렬](copy-activity-overview.md) 테이블이 포함 된 [복사 작업](copy-activity-overview.md)
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

복사 작업의 경우이 눈송이 커넥터는 다음과 같은 기능을 지원 합니다.

- 눈송이의 [copy를 [location] 명령으로](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) 활용 하는 눈송이의 데이터를 최상의 성능을 얻기 위해 복사 합니다.
- 눈송이의 [copy to [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) 명령을 사용 하 여 최상의 성능을 얻기 위해 데이터를 눈송이로 복사 합니다. Azure의 눈송이를 지원 합니다. 

Azure Synapse Analytics 작업 영역을 사용 하는 경우 눈송이를 싱크로 지원 하지 않습니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 눈송이 커넥터에 한정 된 Data Factory 엔터티를 정의 하는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 눈송이 연결 된 서비스에 대해 지원 되는 속성입니다.

| 속성         | Description                                                  | 필수 |
| :--------------- | :----------------------------------------------------------- | :------- |
| type             | Type 속성은 **눈송이**로 설정 되어야 합니다.              | 예      |
| connectionString | 눈송이 인스턴스에 연결 하는 데 필요한 정보를 지정 합니다. Azure Key Vault에 암호나 전체 연결 문자열을 배치 하도록 선택할 수 있습니다. 자세한 내용은 테이블 아래의 예를 참조 하 고 [Azure Key Vault 문서의 저장소 자격 증명](store-credentials-in-key-vault.md) 을 참조 하세요.<br><br>일반적인 설정은 다음과 같습니다.<br>- **계정 이름:** 눈송이 계정 (지역 및 클라우드 플랫폼을 식별 하는 추가 세그먼트 포함)의  [전체 계정 이름](https://docs.snowflake.net/manuals/user-guide/connecting.html#your-snowflake-account-name) (예: xy12345)입니다.<br/>- **사용자 이름:** 연결에 대 한 사용자의 로그인 이름입니다.<br>- **암호:** 사용자에 대 한 암호입니다.<br>- **데이터베이스:** 연결 되 면 사용할 기본 데이터베이스입니다. 지정 된 역할에 권한이 있는 기존 데이터베이스 여야 합니다.<br>- **웨어하우스:** 연결 된 후 사용할 가상 웨어하우스입니다. 지정 된 역할에 권한이 있는 기존 웨어하우스로 지정 해야 합니다.<br>- **역할:** 눈송이 세션에서 사용할 기본 액세스 제어 역할입니다. 지정 된 역할은 지정 된 사용자에 게 이미 할당 된 기존 역할 이어야 합니다. 기본 역할은 PUBLIC입니다. | 예      |
| connectVia       | 데이터 저장소에 연결 하는 데 사용 되는 [통합 런타임](concepts-integration-runtime.md) 입니다. Azure integration runtime 또는 자체 호스팅 integration runtime (데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 지정 하지 않으면 기본 Azure integration runtime을 사용 합니다. | 예       |

**예:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&password=<password>&db=<database>&warehouse=<warehouse>&role=<myRole>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Azure Key Vault의 암호:**

```json
{
    "name": "SnowflakeLinkedService",
    "properties": {
        "type": "Snowflake",
        "typeProperties": {
            "connectionString": "jdbc:snowflake://<accountname>.snowflakecomputing.com/?user=<username>&db=<database>&warehouse=<warehouse>&role=<myRole>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>",
                    "type": "LinkedServiceReference"
                }, 
                "secretName": "<secretName>"
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

눈송이 데이터 집합에 대해 지원 되는 속성은 다음과 같습니다.

| 속성  | Description                                                  | 필수                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | 데이터 집합의 type 속성은 **SnowflakeTable**로 설정 해야 합니다. | 예                         |
| 스키마 | 스키마의 이름입니다. ADF에서는 스키마 이름이 대/소문자를 구분 합니다. |원본에 대해 아니요, 싱크에 대해 예  |
| 테이블 | 테이블/뷰의 이름입니다. ADF에서 테이블 이름은 대/소문자를 구분 합니다. |원본에 대해 아니요, 싱크에 대해 예  |

**예:**

```json
{
    "name": "SnowflakeDataset",
    "properties": {
        "type": "SnowflakeTable",
        "typeProperties": {
            "schema": "<Schema name for your Snowflake database>",
            "table": "<Table name for your Snowflake database>"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "linkedServiceName": {
            "referenceName": "<name of linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 눈송이 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="snowflake-as-the-source"></a>원본으로 서의 눈송이

눈송이 커넥터는 눈송이의 [COPY to [location]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html) 명령을 활용 하 여 최상의 성능을 구현 합니다.

싱크 데이터 저장소 및 형식이 눈송이 복사 명령에 의해 기본적으로 지원 되는 경우 복사 작업을 사용 하 여 눈송이에서 싱크로 직접 복사할 수 있습니다. 자세한 내용은 [눈송이에서 직접 복사](#direct-copy-from-snowflake)를 참조 하세요. 그렇지 않으면 눈송이에서 기본 제공 [준비 복사본](#staged-copy-from-snowflake)을 사용 합니다.

눈송이에서 데이터를 복사 하려면 복사 작업 **원본** 섹션에서 다음 속성이 지원 됩니다.

| 속성                     | Description                                                  | 필수 |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | 복사 작업 원본의 type 속성은 **SnowflakeSource**로 설정 해야 합니다. | 예      |
| Query          | 눈송이에서 데이터를 읽는 SQL 쿼리를 지정 합니다. 스키마 이름, 테이블 및 열에 소문자가 포함 된 경우 쿼리의 개체 식별자 (예:)를 인용 합니다 `select * from "schema"."myTable"` .<br>저장 프로시저 실행은 지원 되지 않습니다. | 예       |
| exportSettings | 눈송이에서 데이터를 검색 하는 데 사용 되는 고급 설정입니다. COPY into 명령에서 지원 되는 항목을 구성 하 여 문이 호출 될 때 Data Factory 전달 하 게 됩니다. | 예       |
| ***에서 `exportSettings` 다음을 수행 합니다.*** |  |  |
| 형식 | **SnowflakeExportCopyCommand**로 설정 된 내보내기 명령의 유형입니다. | 예 |
| additionalCopyOptions | 키-값 쌍의 사전으로 제공 되는 추가 복사 옵션입니다. 예: MAX_FILE_SIZE, 덮어쓰기 자세한 내용은 [눈송이 복사 옵션](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#copy-options-copyoptions)을 참조 하세요. | 예 |
| additionalFormatOptions | 키-값 쌍의 사전으로 명령을 복사 하기 위해 제공 되는 추가 파일 형식 옵션입니다. 예: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. 자세한 내용은 [눈송이 형식 유형 옵션](https://docs.snowflake.com/en/sql-reference/sql/copy-into-location.html#format-type-options-formattypeoptions)을 참조 하세요. | 예 |

#### <a name="direct-copy-from-snowflake"></a>눈송이에서 직접 복사

싱크 데이터 저장소 및 형식이이 섹션에 설명 된 조건을 충족 하는 경우 복사 작업을 사용 하 여 눈송이에서 싱크로 직접 복사할 수 있습니다. Data Factory는 설정을 확인 하 고 다음 조건이 충족 되지 않으면 복사 작업 실행에 실패 합니다.

- **싱크 연결 된 서비스** 는 **공유 액세스 서명** 인증을 사용 하는 [**Azure Blob storage**](connector-azure-blob-storage.md) 입니다.

- **싱크 데이터 형식은** 다음과 같은 구성 **으로 Parquet**, **구분 된 텍스트**또는 **JSON** 입니다.

    - **Parquet** 형식의 경우 압축 코덱은 **None**, **Snappy**또는 **Lzo**입니다.
    - **구분 기호로 분리 된 텍스트** 형식:
        - `rowDelimiter` 는 **\r\n**또는 모든 단일 문자입니다.
        - `compression`압축, **gzip**, **bzip2**또는 **deflate**일 수 **없습니다**.
        - `encodingName`는 기본값으로 남아 있거나 **utf-8**로 설정됩니다.
        - `quoteChar` 는 **큰따옴표**, **작은따옴표**또는 **빈 문자열** (따옴표 문자 없음)입니다.
    - **JSON** 형식의 경우 직접 복사는 원본 눈송이 테이블 또는 쿼리 결과도 단일 열을 가지 며이 열의 데이터 형식은 **VARIANT**, **OBJECT**또는 **배열인**경우에만 지원 합니다.
        - `compression`압축, **gzip**, **bzip2**또는 **deflate**일 수 **없습니다**.
        - `encodingName`는 기본값으로 남아 있거나 **utf-8**로 설정됩니다.
        - `filePattern` 복사 활동 싱크에서 기본값 또는 **Setofobjects**로 설정 됩니다.

- 복사 활동 원본에서 `additionalColumns` 가 지정 되지 않았습니다.
- 열 매핑이 지정 되지 않았습니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MYTABLE",
                "exportSettings": {
                    "type": "SnowflakeExportCopyCommand",
                    "additionalCopyOptions": {
                        "MAX_FILE_SIZE": "64000000",
                        "OVERWRITE": true
                    },
                    "additionalFormatOptions": {
                        "DATE_FORMAT": "'MM/DD/YYYY'"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="staged-copy-from-snowflake"></a>눈송이에서 준비 된 복사

싱크 데이터 저장소 또는 형식이 눈송이 복사 명령과 기본적으로 호환 되지 않는 경우 마지막 섹션에 설명 된 대로 중간 Azure Blob 저장소 인스턴스를 사용 하 여 기본 제공 준비 된 복사본을 사용 하도록 설정 합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. Data Factory은 눈송이에서 준비 저장소로 데이터를 내보낸 다음 데이터를 싱크에 복사 하 고 마지막으로 준비 저장소에서 임시 데이터를 정리 합니다. 준비를 사용 하 여 데이터 복사에 대 한 자세한 내용은 [준비 된 복사](copy-activity-performance-features.md#staged-copy) 를 참조 하세요.

이 기능을 사용 하려면 Azure storage 계정을 중간 준비로 참조 하는 [Azure Blob 저장소 연결 된 서비스](connector-azure-blob-storage.md#linked-service-properties) 를 만듭니다. 그런 다음 `enableStaging` `stagingSettings` 복사 작업에서 및 속성을 지정 합니다.

> [!NOTE]
> 스테이징 Azure Blob 저장소 연결 된 서비스는 눈송이 복사 명령에 필요한 공유 액세스 서명 인증을 사용 해야 합니다. 

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Snowflake input dataset name>",
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
                "type": "SnowflakeSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

### <a name="snowflake-as-sink"></a>눈송이를 싱크로

눈송이 커넥터는 눈송이의 [COPY to [table]](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html) 명령을 활용 하 여 최상의 성능을 구현 합니다. Azure의 눈송이에 데이터를 쓰는 것을 지원 합니다.

원본 데이터 저장소 및 형식이 눈송이 복사 명령에 의해 기본적으로 지원 되는 경우 복사 작업을 사용 하 여 원본에서 눈송이로 직접 복사할 수 있습니다. 자세한 내용은 [눈송이로 직접 복사](#direct-copy-to-snowflake)를 참조 하세요. 그렇지 않으면 눈송이에 기본 제공 [준비 복사본을](#staged-copy-to-snowflake)사용 합니다.

데이터를 눈송이로 복사 하기 위해 복사 작업 **싱크** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성          | Description                                                  | 필수                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | 복사 작업 싱크의 type 속성은 **SnowflakeSink**로 설정 됩니다. | 예                                           |
| preCopyScript     | 각 실행 시 눈송이에 데이터를 쓰기 전에 실행할 복사 작업에 대 한 SQL 쿼리를 지정 합니다. 이 속성을 사용하여 미리 로드된 데이터를 정리합니다. | 예                                            |
| importSettings | 데이터를 눈송이에 쓰는 데 사용 되는 고급 설정입니다. COPY into 명령에서 지원 되는 항목을 구성 하 여 문이 호출 될 때 Data Factory 전달 하 게 됩니다. | 예 |
| ***에서 `importSettings` 다음을 수행 합니다.*** |                                                              |  |
| 형식 | **SnowflakeImportCopyCommand**로 설정 된 가져오기 명령의 유형입니다. | 예 |
| additionalCopyOptions | 키-값 쌍의 사전으로 제공 되는 추가 복사 옵션입니다. 예: ON_ERROR, FORCE, LOAD_UNCERTAIN_FILES. 자세한 내용은 [눈송이 복사 옵션](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#copy-options-copyoptions)을 참조 하세요. | 예 |
| additionalFormatOptions | 키-값 쌍의 사전으로 제공 된 복사 명령에 제공 되는 추가 파일 형식 옵션입니다. 예: DATE_FORMAT, TIME_FORMAT, TIMESTAMP_FORMAT. 자세한 내용은 [눈송이 형식 유형 옵션](https://docs.snowflake.com/en/sql-reference/sql/copy-into-table.html#format-type-options-formattypeoptions)을 참조 하세요. | 예 |

#### <a name="direct-copy-to-snowflake"></a>눈송이로 직접 복사

원본 데이터 저장소 및 형식이이 섹션에 설명 된 조건을 충족 하는 경우 복사 작업을 사용 하 여 원본에서 눈송이로 직접 복사할 수 있습니다. Azure Data Factory는 설정을 확인 하 고 다음 조건이 충족 되지 않으면 복사 작업 실행에 실패 합니다.

- **원본 연결 된 서비스** 는 **공유 액세스 서명** 인증을 사용 하는 [**Azure Blob storage**](connector-azure-blob-storage.md) 입니다.

- **원본 데이터 형식은** 다음과 같은 구성을 사용 하는 **Parquet**, **구분 된 텍스트**또는 **JSON** 입니다.

    - **Parquet** 형식의 경우 압축 코덱은 **None**또는 **Snappy**입니다.

    - **구분 기호로 분리 된 텍스트** 형식:
        - `rowDelimiter` 는 **\r\n**또는 모든 단일 문자입니다. 행 구분 기호가 "\r\n"이 아닌 경우 `firstRowAsHeader` **false**여야 하 고 `skipLineCount` 가 지정 되지 않습니다.
        - `compression`압축, **gzip**, **bzip2**또는 **deflate**일 수 **없습니다**.
        - `encodingName` 기본값은 "UTF-8", "UTF-16", "UTF-16", "32 UTF-8", "UTF-32BE", "BIG5", "EUC-JP", "EUC-KR", "GB18030", "ISO-2022-JP", "ISO-2022-KR", "로 설정 됩니다. ISO-8859-1", "ISO-8859-2", "ISO-8859-5", "iso-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "WINDOWS-1250", "WINDOWS-1251", "WINDOWS-1252", "WINDOWS-1253", "WINDOWS-1254", "WINDOWS-1255".
        - `quoteChar` 는 **큰따옴표**, **작은따옴표**또는 **빈 문자열** (따옴표 문자 없음)입니다.
    - **JSON** 형식의 경우 직접 복사는 싱크 눈송이 테이블에 단일 열만 있고이 열의 데이터 형식이 **VARIANT**, **OBJECT**또는 **배열인**경우에만 지원 합니다.
        - `compression`압축, **gzip**, **bzip2**또는 **deflate**일 수 **없습니다**.
        - `encodingName`는 기본값으로 남아 있거나 **utf-8**로 설정됩니다.
        - 열 매핑이 지정 되지 않았습니다.

- 복사 작업 원본에서: 

   -  `additionalColumns`을(를) 지정하지 않았습니다.
   - 원본이 폴더인 경우 `recursive` 는 true로 설정 됩니다.
   - `prefix`, `modifiedDateTimeStart`, `modifiedDateTimeEnd` 및 `enablePartitionDiscovery`는 지정되지 않습니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink",
                "importSettings": {
                    "type": "SnowflakeImportCopyCommand",
                    "copyOptions": {
                        "FORCE": "TRUE",
                        "ON_ERROR": "SKIP_FILE",
                    },
                    "fileFormatOptions": {
                        "DATE_FORMAT": "YYYY-MM-DD",
                    }
                }
            }
        }
    }
]
```

#### <a name="staged-copy-to-snowflake"></a>눈송이로 준비 된 복사

싱크 데이터 저장소 또는 형식이 눈송이 복사 명령과 기본적으로 호환 되지 않는 경우 마지막 섹션에 설명 된 대로 중간 Azure Blob 저장소 인스턴스를 사용 하 여 기본 제공 준비 된 복사본을 사용 하도록 설정 합니다. 준비된 복사 기능을 사용할 경우, 처리량도 향상됩니다. Data Factory은 눈송이의 데이터 형식 요구 사항을 충족 하도록 데이터를 자동으로 변환 합니다. 그런 다음 복사 명령을 호출 하 여 데이터를 눈송이로 로드 합니다. 마지막으로, Blob Storage에서 임시 데이터를 정리합니다. 준비를 사용 하 여 데이터 복사에 대 한 자세한 내용은 [준비 된 복사](copy-activity-performance-features.md#staged-copy) 를 참조 하세요.

이 기능을 사용 하려면 Azure storage 계정을 중간 준비로 참조 하는 [Azure Blob 저장소 연결 된 서비스](connector-azure-blob-storage.md#linked-service-properties) 를 만듭니다. 그런 다음 `enableStaging` `stagingSettings` 복사 작업에서 및 속성을 지정 합니다.

> [!NOTE]
> 스테이징 Azure Blob 저장소 연결 된 서비스는 눈송이 복사 명령에 필요한 공유 액세스 서명 인증을 사용 해야 합니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyToSnowflake",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Snowflake output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SnowflakeSink"
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                },
                "path": "mystagingpath"
            }
        }
    }
]
```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

매핑 데이터 흐름에서 데이터를 변환할 때 눈송이에서 테이블을 읽고 쓸 수 있습니다. 자세한 내용은 매핑 데이터 흐름에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md)을 참조하세요. 눈송이 데이터 집합 또는 [인라인 데이터 집합](data-flow-source.md#inline-datasets) 을 원본 및 싱크 형식으로 사용 하도록 선택할 수 있습니다.

### <a name="source-transformation"></a>원본 변환

다음 표에서는 눈송이 원본에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다. 커넥터는 눈송이 [내부 데이터 전송을](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)활용 합니다.

| 이름 | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| 테이블 | 테이블을 입력으로 선택 하는 경우 데이터 흐름은 인라인 데이터 집합을 사용할 때 눈송이 데이터 집합 또는 원본 옵션에 지정 된 테이블의 모든 데이터를 가져옵니다. | 예 | String | *(인라인 데이터 집합에만 해당)*<br>tableName<br>schemaName |
| 쿼리 | 쿼리를 입력으로 선택 하는 경우 눈송이에서 데이터를 인출 하는 쿼리를 입력 합니다. 이 설정은 데이터 집합에서 선택한 테이블을 재정의 합니다.<br>스키마 이름, 테이블 및 열에 소문자가 포함 된 경우 쿼리의 개체 식별자 (예:)를 인용 합니다 `select * from "schema"."myTable"` . | 예 | String | Query |

#### <a name="snowflake-source-script-examples"></a>눈송이 원본 스크립트 예제

눈송이 데이터 집합을 원본 유형으로 사용 하는 경우 연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    query: 'select * from MYTABLE',
    format: 'query') ~> SnowflakeSource
```

인라인 데이터 집합을 사용 하는 경우 연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'query',
    query: 'select * from MYTABLE',
    store: 'snowflake') ~> SnowflakeSource
```

### <a name="sink-transformation"></a>싱크 변환

아래 표에는 눈송이 싱크에 의해 지원 되는 속성이 나와 있습니다. 이러한 속성은 **설정** 탭에서 편집할 수 있습니다. 인라인 데이터 집합을 사용 하는 경우 [데이터 집합 속성](#dataset-properties) 섹션에 설명 된 속성과 동일한 추가 설정이 표시 됩니다. 커넥터는 눈송이 [내부 데이터 전송을](https://docs.snowflake.com/en/user-guide/spark-connector-overview.html#internal-data-transfer)활용 합니다.

| 이름 | Description | 필수 | 허용되는 값 | 데이터 흐름 스크립트 속성 |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update 메서드 | 눈송이 대상에서 허용 되는 작업을 지정 합니다.<br>행을 업데이트, upsert 또는 삭제 하려면 해당 작업에 대 한 행의 태그를 변경 하는 [행 변환이](data-flow-alter-row.md) 필요 합니다. | 예 | `true` 또는 `false` | 삭제할 <br/>삽입 가능한 <br/>있는 <br/>upsertable |
| 키 열 | 업데이트, upsert 및 삭제의 경우 변경할 행을 결정하기 위해 키 열을 설정해야 합니다. | 예 | 배열 | 키 |
| 테이블 작업 | 쓰기 전에 대상 테이블에서 모든 행을 다시 만들지 또는 제거할지를 결정 합니다.<br>- **없음**: 테이블에 대 한 작업이 수행 되지 않습니다.<br>- **다시 만들기**: 테이블이 삭제 되 고 다시 생성 됩니다. 동적으로 새 테이블을 만드는 경우 필요합니다.<br>- **Truncate**: 대상 테이블의 모든 행이 제거 됩니다. | 예 | `true` 또는 `false` | 다시<br/>truncate |

#### <a name="snowflake-sink-script-examples"></a>눈송이 싱크 스크립트 예제

눈송이 데이터 집합을 싱크 유형으로 사용 하는 경우 연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:true,
    insertable:true,
    updateable:true,
    upsertable:false,
    keys:['movieId'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

인라인 데이터 집합을 사용 하는 경우 연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    format: 'table',
    tableName: 'table',
    schemaName: 'schema',
    deletable: true,
    insertable: true,
    updateable: true,
    upsertable: false,
    store: 'snowflake',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SnowflakeSink
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대 한 자세한 내용은 [조회 작업](control-flow-lookup-activity.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Data Factory 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조 하세요.
