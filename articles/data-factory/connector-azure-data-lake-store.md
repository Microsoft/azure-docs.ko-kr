---
title: Data Factory를 사용하여 Azure Data Lake Storage Gen1 간에 데이터 복사 | Microsoft Docs
description: Data Factory를 사용하여 지원되는 원본 데이터 저장소에서 Azure Data Lake Store로 또는 Data Lake Store에서 지원되는 싱크 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: jingwang
ms.openlocfilehash: 433824c4e375cf1ce7d7a6fe16730044628ccab1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61001629"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1 간에 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-azure-datalake-connector.md)
> * [현재 버전](connector-azure-data-lake-store.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Data Lake Storage Gen1(이전의 Azure Data Lake Store) 간에 데이터를 복사하는 방법을 간략하게 설명합니다. [복사 활동 개요](copy-activity-overview.md)를 기반으로 빌드됩니다.

## <a name="supported-capabilities"></a>지원되는 기능

모든 지원되는 원본 데이터 저장소에서 Azure Data Lake Store로 데이터를 복사하거나 Azure Data Lake Store에서 모든 지원되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 Azure Data Lake Store 커넥터는 다음을 지원합니다.

- 인증 방법 **서비스 주체** 또는 **Azure 리소스 관리 ID** 중 하나를 사용하여 파일을 복사합니다.
- 파일을 있는 그대로 복사하거나 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 파일을 구문 분석하거나 생성합니다.

> [!IMPORTANT]
> 자체 호스팅 통합 런타임을 사용하여 데이터를 복사하는 경우 포트 443에서 `<ADLS account name>.azuredatalakestore.net` 및 `login.microsoftonline.com/<tenant>/oauth2/token`에 대한 아웃바운드 트래픽을 허용하도록 회사 방화벽을 구성합니다. 후자는 액세스 토큰을 가져오기 위해 통합 런타임이 통신해야 하는 Azure 보안 토큰 서비스입니다.

## <a name="get-started"></a>시작하기

> [!TIP]
> Azure Data Lake Store 커넥터를 사용하는 연습을 보려면 [Azure Data Lake Store에 데이터 로드](load-azure-data-lake-store.md)를 참조하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Data Lake Store에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Data Lake Store 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | `type` 속성은 **AzureDataLakeStore**로 설정해야 합니다. | 예 |
| dataLakeStoreUri | Azure Data Lake Store 계정에 대한 정보. 이 정보는 `https://[accountname].azuredatalakestore.net/webhdfs/v1` 또는 `adl://[accountname].azuredatalakestore.net/` 형식 중 하나를 사용합니다. | 예 |
| subscriptionId | Data Lake Store 계정이 속하는 Azure 구독 ID입니다. | 싱크에 필요 |
| resourceGroupName | Data Lake Store 계정이 속하는 Azure 리소스 그룹 이름입니다. | 싱크에 필요 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 통합 런타임을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 이 속성을 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

### <a name="use-service-principal-authentication"></a>서비스 주체 인증 사용

서비스 주체 인증을 사용하려면 Azure Active Directory에서 애플리케이션 엔터티를 등록한 후 이 엔터티에 Data Lake Store 액세스 권한을 부여합니다. 자세한 단계는 [서비스 간 인증](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)을 참조하세요. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

- 애플리케이션 UI
- 애플리케이션 키
- 테넌트 ID

>[!IMPORTANT]
> Data Lake Store에서 서비스 주체에게 적절한 권한을 부여해야 합니다.
>- **원본으로**: **데이터 탐색기** > **액세스**에서 **읽기 + 실행** 이상의 권한을 부여하여 폴더 및 하위 폴더의 파일을 나열하고 복사합니다. 또는 단일 파일을 복사할 수 있는 **읽기** 권한을 부여할 수 있습니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. 계정 수준 액세스 제어(IAM)가 필요하지 않습니다.
>- **싱크로**: **데이터 탐색기** > **액세스**에서 **쓰기 + 실행** 이상의 권한을 부여하여 폴더에 하위 항목을 만듭니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. Azure Integration Runtime을 사용하여 복사하는 경우(원본과 싱크가 모두 클라우드에 있음) Data Factory가 Data Lake Store의 지역을 검색할 수 있도록 IAM에서 **읽기 권한자** 이상의 역할을 부여합니다. 이 IAM 역할을 방지하려면 Data Lake Store의 위치에 명시적으로 [Azure Integration Runtime을 만듭니다](create-azure-integration-runtime.md#create-azure-ir). 다음 예제와 같이 Data Lake Store 연결된 서비스로 연결합니다.

>[!NOTE]
>폴더를 루트부터 나열하려면 **“실행” 권한을 가진 루트 수준에서** 부여할 서비스 주체의 권한을 설정해야 합니다. 다음을 사용하는 경우에도 적용됩니다.
>- **데이터 복사 도구** - 복사 파이프라인을 작성합니다.
>- **Data Factory UI** - 연결을 테스트하고 작성 중에 폴더를 탐색합니다.
>루트 수준 권한 부여에 문제가 있다면 작성 하는 동안 연결 테스트 및 입력된 경로 수동으로 건너뛸 수 있습니다. 복사 작업으로 복사 될 파일에 적절 한 권한이 있는 서비스 주체 부여는 계속 작동 합니다.

다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 `SecureString`으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 예 |

**예제:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a> Azure 리소스 인증에 관리 ID 사용

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 고유한 서비스 주체를 사용 하 여 Data Lake Store 인증에 대 한 직접 관리 되는이 id를 사용할 수 있습니다. 이 지정된 팩터리는 Data Lake Store 간에 데이터에 액세스하고 복사할 수 있습니다.

Azure 리소스 인증에 관리 ID를 사용하려면

1. [Data factory 관리 id 정보를 검색할](data-factory-service-identity.md#retrieve-managed-identity) 를 "서비스 Id 응용 프로그램 ID"의 팩터리와 함께 생성 된 값을 복사 하 여 합니다.
2. 이러한 정보에 따라 서비스 주체에서 수행한 동일한 방식으로 Data Lake Store로 관리 되는 id 액세스를 부여 합니다.

>[!IMPORTANT]
> Data Lake Store에서 data factory 관리 identity 적절 한 권한을 부여 해야 합니다.
>- **원본으로**: **데이터 탐색기** > **액세스**에서 **읽기 + 실행** 이상의 권한을 부여하여 폴더 및 하위 폴더의 파일을 나열하고 복사합니다. 또는 단일 파일을 복사할 수 있는 **읽기** 권한을 부여할 수 있습니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. 계정 수준 액세스 제어(IAM)가 필요하지 않습니다.
>- **싱크로**: **데이터 탐색기** > **액세스**에서 **쓰기 + 실행** 이상의 권한을 부여하여 폴더에 하위 항목을 만듭니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. Azure Integration Runtime을 사용하여 복사하는 경우(원본과 싱크가 모두 클라우드에 있음) Data Factory가 Data Lake Store의 지역을 검색할 수 있도록 IAM에서 **읽기 권한자** 이상의 역할을 부여합니다. 이 IAM 역할을 방지하려면 Data Lake Store의 위치에 명시적으로 [Azure Integration Runtime을 만듭니다](create-azure-integration-runtime.md#create-azure-ir). 다음 예제와 같이 Data Lake Store 연결된 서비스로 연결합니다.

>[!NOTE]
>목록에 폴더를 루트에서 시작, 관리 되는 id에 부여 된 사용 권한을 설정 해야 합니다 **"실행" 권한 사용 하 여 루트 수준**합니다. 다음을 사용하는 경우에도 적용됩니다.
>- **데이터 복사 도구** - 복사 파이프라인을 작성합니다.
>- **Data Factory UI** - 연결을 테스트하고 작성 중에 폴더를 탐색합니다.
>루트 수준 권한 부여에 문제가 있다면 작성 하는 동안 연결 테스트 및 입력된 경로 수동으로 건너뛸 수 있습니다. 복사 작업으로 관리 되는 id에 복사할 파일에서 적절 한 권한이 있는 부여는 계속 작동 합니다.

Azure Data Factory에서 연결된 서비스의 일반 Data Lake Store 정보 이외의 속성을 지정할 필요가 없습니다.

**예제:**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

Azure Data Lake Store 간에 데이터를 복사하려면 데이터 세트의 `type` 속성을 **AzureDataLakeStoreFile**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **AzureDataLakeStoreFile**로 설정해야 합니다. |예 |
| folderPath | Data Lake Store의 폴더 경로입니다. 지정하지 않으면 루트를 가리킵니다. <br/><br/>와일드카드 필터가 지원되며, 허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br/><br/>예: rootfolder/subfolder/(더 많은 예제는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples) 참조) |아닙니다. |
| fileName | 지정된 "folderPath" 아래의 파일에 대한 **이름 또는 와일드 카드 필터**입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>필터에 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>`^`을 사용하여 실제 파일 이름 내에 와일드카드 또는 이 이스케이프 문자가 있는 경우 이스케이프합니다.<br/><br/>fileName이 출력 데이터 세트에 대해 지정되지 않고 **preserveHierarchy**가 작업 싱크에 지정되지 않으면, 복사 작업은 다음과 같은 패턴으로 파일 이름을 자동으로 생성합니다. “*Data.[activity run id GUID].[GUID if FlattenHierarchy].[format if configured].[compression if configured]*”. 예: “Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz”. 쿼리 대신 테이블 이름을 사용하여 테이블 형식 원본에서 복사하면, 이름 패턴이 “’[테이블 이름].[형식].[구성된 경우 압축]’”입니다. 예: "MyTable.csv". |아닙니다. |
| modifiedDatetimeStart | 특성에 기반한 파일 필터링: 마지막으로 수정한 날짜 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 아닙니다. |
| modifiedDatetimeEnd | 특성에 기반한 파일 필터링: 마지막으로 수정한 날짜 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 아닙니다. |
| format | 파일 기반 저장소(이진 복사) 간에 **파일을 있는 그대로 복사**하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석하거나 생성하려는 경우, 지원되는 파일 형식 유형은 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**입니다. 이 값 중 하나로 서식에서 **type** 속성을 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs.md#text-format), [Json 형식](supported-file-formats-and-compression-codecs.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>지원되는 수준은 **최적** 및 **가장 빠름**입니다. |아닙니다. |


>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 **folderPath**만을 지정합니다.<br>특정 이름의 단일 파일을 복사하려면 폴더 부분으로 **folderPath** 및 파일 이름으로 **fileName**을 지정합니다.<br>폴더 아래에서 파일의 하위 집합을 복사하려면 폴더 부분으로 **folderPath** 및 와일드카드 필터로 **fileName**을 지정합니다. 

**예제:**

```json
{
    "name": "ADLSDataset",
    "properties": {
        "type": "AzureDataLakeStoreFile",
        "linkedServiceName":{
            "referenceName": "<ADLS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "datalake/myfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 이 섹션에서는 Azure Data Lake Store 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store를 원본으로

Data Lake Store에서 데이터를 복사하려면 복사 작업의 원본 형식을 **AzureDataLakeStoreSource**로 설정합니다. 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 `type` 속성을 **AzureDataLakeStoreSource**로 설정해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. `recursive`를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다. |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS input dataset name>",
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
                "type": "AzureDataLakeStoreSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store를 싱크로

Data Lake Store에 데이터를 복사하려면 복사 작업의 싱크 형식을 **AzureDataLakeStoreSink**로 설정합니다. **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 싱크의 `type` 속성을 **AzureDataLakeStoreSink**로 설정해야 합니다. |예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용되는 값은 다음과 같습니다.<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 병합되는 파일 이름은 지정된 파일/Blob 이름이 적용됩니다. 그렇지 않은 경우 파일 이름이 자동으로 생성됩니다. | 아닙니다. |

**예제:**

```json
"activities":[
    {
        "name": "CopyToADLS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataLakeStoreSink",
                "copyBehavior": "PreserveHierarchy"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 와일드카드 필터가 있는 폴더 경로 및 파일 이름의 결과 동작에 대해 설명합니다.

| folderPath | fileName | recursive | 원본 폴더 구조 및 필터 결과(**굵게** 표시된 파일이 검색됨)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (비어 있음, 기본값 사용) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (비어 있음, 기본값 사용) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>복사 작업의 동작 예제

이 섹션에서는 다양한 `recursive` 및 `copyBehavior` 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상이 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동으로 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 파일 내용이 자동 생성된 파일 이름을 사용하는 파일 하나로 병합됩니다. File1에 대해 자동으로 생성된 이름<br/><br/>File3, File4, File5를 가진 Subfolder1은 선택되지 않습니다. |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
