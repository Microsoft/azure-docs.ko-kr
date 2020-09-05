---
title: Azure Data Lake Storage Gen1에(서) 데이터 복사
description: Data Factory를 사용하여 지원되는 원본 데이터 저장소에서 Azure Data Lake Store로 또는 Data Lake Store에서 지원되는 싱크 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/31/2020
ms.openlocfilehash: ed1b64ff1156548ec04900b2303b68b6980565ae
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182540"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에(서) 데이터 복사

> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory 버전을 선택하세요."]
>
> * [버전 1](v1/data-factory-azure-datalake-connector.md)
> * [현재 버전](connector-azure-data-lake-store.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Lake Storage Gen1에(서) 데이터를 복사하는 방법을 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Data Lake Storage Gen1 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md) 
- [매핑 데이터 흐름](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [삭제 작업](delete-activity.md)

특히, 이 커넥터를 사용하면 다음을 수행할 수 있습니다.

- Azure 리소스에 대한 관리 ID 또는 서비스 주체 중 한 가지 인증 방법을 사용하여 파일을 복사합니다.
- 파일을 있는 그대로 복사하거나, [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용하여 파일을 구문 분석 또는 생성합니다.
- Azure Data Lake Storage Gen2에 복사할 때 [ACL을 유지](#preserve-acls-to-data-lake-storage-gen2)합니다.

> [!IMPORTANT]
> 자체 호스팅 통합 런타임을 사용하여 데이터를 복사하는 경우 포트 443에서 `<ADLS account name>.azuredatalakestore.net` 및 `login.microsoftonline.com/<tenant>/oauth2/token`에 대한 아웃바운드 트래픽을 허용하도록 회사 방화벽을 구성합니다. 후자는 액세스 토큰을 가져오기 위해 통합 런타임이 통신해야 하는 Azure 보안 토큰 서비스입니다.

## <a name="get-started"></a>시작하기

> [!TIP]
> Azure Data Lake Store 커넥터를 사용하는 방법에 대한 연습을 보려면 [Azure Data Lake Store에 데이터 로드](load-azure-data-lake-store.md)를 참조하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Data Lake Store에 지정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대한 정보를 제공합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Data Lake Store 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | `type` 속성은 **AzureDataLakeStore**로 설정해야 합니다. | 예 |
| dataLakeStoreUri | Azure Data Lake Store 계정에 대한 정보. 이 정보는 `https://[accountname].azuredatalakestore.net/webhdfs/v1` 또는 `adl://[accountname].azuredatalakestore.net/` 형식 중 하나를 사용합니다. | 예 |
| subscriptionId | Data Lake Store 계정이 속하는 Azure 구독 ID입니다. | 싱크에 필요 |
| resourceGroupName | Data Lake Store 계정이 속하는 Azure 리소스 그룹 이름입니다. | 싱크에 필요 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 데이터 저장소가 개인 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 사용할 수 있습니다. 이 속성을 지정하지 않으면 기본 Azure 통합 런타임이 사용됩니다. |예 |

### <a name="use-service-principal-authentication"></a>서비스 주체 인증 사용

서비스 주체 인증을 사용하려면 다음 단계를 수행합니다.

1. Azure Active Directory에서 애플리케이션 엔터티를 등록한 후 이 엔터티에 Data Lake Store 액세스 권한을 부여합니다. 자세한 단계는 [서비스 간 인증](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)을 참조하세요. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 서비스 주체에게 적절한 사용 권한을 부여합니다. [Azure Data Lake Storage Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)에서 Data Lake Storage Gen1의 권한 작동 방식에 대한 예제를 참조하세요.

    - **원본으로**: **데이터 탐색기** > **액세스**에서 복사할 파일에 대한 **읽기** 권한과 함께 루트를 포함한 모든 업스트림 폴더에 대해 최소한 **실행** 권한을 부여합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. 계정 수준 액세스 제어(IAM)가 필요하지 않습니다.
    - **싱크로**: **데이터 탐색기** > **액세스**에서 싱크 폴더에 대한 **쓰기** 권한과 함께 루트를 포함한 모든 업스트림 폴더에 대해 최소한 **실행** 권한을 부여합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다.

다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 `SecureString`으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 애플리케이션이 상주하는 테넌트 정보(예: 도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 예 |
| azureCloudType | 서비스 주체 인증의 경우 Azure Active Directory 응용 프로그램이 등록 된 Azure 클라우드 환경의 유형을 지정 합니다. <br/> 허용 되는 값은 **Azurepublic**, **azurepublic**, **azureus정부**및 **AzureGermany**입니다. 기본적으로 데이터 팩터리의 클라우드 환경이 사용 됩니다. | 예 |

**예:**

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

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Azure 리소스 인증에 관리 ID 사용

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 고유한 서비스 주체를 사용하는 것과 유사하게 Data Lake Store 인증에 이 관리 ID를 직접 사용할 수 있습니다. 이 지정된 팩터리는 Data Lake Store 간에 데이터에 액세스하고 복사할 수 있습니다.

Azure 리소스 인증을 위해 관리 ID를 사용하려면 다음 단계를 따릅니다.

1. 팩터리와 함께 생성된 "서비스 ID 애플리케이션 ID" 값을 복사하여 [데이터 팩터리 관리 ID 정보를 검색](data-factory-service-identity.md#retrieve-managed-identity)합니다.

2. 관리 ID에 Data Lake Store에 대한 액세스 권한을 부여합니다. [Azure Data Lake Storage Gen1의 액세스 제어](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)에서 Data Lake Storage Gen1의 권한 작동 방식에 대한 예제를 참조하세요.

    - **원본으로**: **데이터 탐색기** > **액세스**에서 복사할 파일에 대한 **읽기** 권한과 함께 루트를 포함한 모든 업스트림 폴더에 대해 최소한 **실행** 권한을 부여합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. 계정 수준 액세스 제어(IAM)가 필요하지 않습니다.
    - **싱크로**: **데이터 탐색기** > **액세스**에서 싱크 폴더에 대한 **쓰기** 권한과 함께 루트를 포함한 모든 업스트림 폴더에 대해 최소한 **실행** 권한을 부여합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다.

Azure Data Factory에서 연결된 서비스의 일반 Data Lake Store 정보 이외의 속성을 지정할 필요가 없습니다.

**예:**

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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 데이터 세트의 `location` 설정에서 Azure Data Lake Store Gen1에 지원되는 속성은 다음과 같습니다.

| 속성   | Description                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 세트의 `location`에 있는 type 속성은 **AzureDataLakeStoreLocation**으로 설정되어야 합니다. | 예      |
| folderPath | 폴더의 경로입니다. 와일드카드를 사용하여 폴더를 필터링하려면 이 설정을 건너뛰고 작업 원본 설정에서 지정합니다. | 예       |
| fileName   | 지정된 folderPath의 파일 이름입니다. 와일드카드를 사용하여 파일을 필터링하려면 이 설정을 건너뛰고 작업 원본 설정에서 지정합니다. | 예       |

**예:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<ADLS Gen1 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureDataLakeStoreLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 이 섹션에서는 Azure Data Lake Store 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-data-lake-store-as-source"></a>Azure Data Lake Store를 원본으로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 복사 원본의 `storeSettings` 설정에서 Azure Data Lake Store Gen1에 지원되는 속성은 다음과 같습니다.

| 속성                 | Description                                                  | 필수                                     |
| ------------------------ | ------------------------------------------------------------ | -------------------------------------------- |
| type                     | `storeSettings`의 type 속성은 **AzureDataLakeStoreReadSettings**로 설정되어야 합니다. | 예                                          |
| ***복사할 파일 찾기:*** |  |  |
| 옵션 1: 정적 경로<br> | 데이터 세트에 지정된 폴더/파일 경로에서 복사합니다. 폴더의 모든 파일을 복사하려면 `wildcardFileName`을 `*`로 지정합니다. |  |
| 옵션 2: 이름 범위<br>-listAfter | 이름이이 값 뒤에 있는 폴더/파일을 사전순으로 검색 합니다 (제외). ADLS Gen1에 대 한 서비스 쪽 필터를 활용 하 여 와일드 카드 필터 보다 더 나은 성능을 제공 합니다. <br/>데이터 팩터리가 데이터 집합에 정의 된 경로에이 필터를 적용 하 고 하나의 엔터티 수준만 지원 됩니다. [이름 범위 필터 예](#name-range-filter-examples)의 추가 예제를 참조 하세요. | 예 |
| 옵션 2: 이름 범위<br/>-listBefore | 이름이이 값 앞에 있는 폴더/파일 (포함)을 검색 합니다. ADLS Gen1에 대 한 서비스 쪽 필터를 활용 하 여 와일드 카드 필터 보다 더 나은 성능을 제공 합니다.<br>데이터 팩터리가 데이터 집합에 정의 된 경로에이 필터를 적용 하 고 하나의 엔터티 수준만 지원 됩니다. [이름 범위 필터 예](#name-range-filter-examples)의 추가 예제를 참조 하세요. | 예 |
| 옵션 3: 와일드카드<br>- wildcardFolderPath | 원본 폴더를 필터링할 와일드카드 문자가 포함된 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예                                            |
| 옵션 3: 와일드카드<br>- wildcardFileName | 원본 파일을 필터링하기 위해 지정된 folderPath/wildcardFolderPath 아래의 와일드카드 문자가 포함된 파일 이름입니다. <br>허용되는 와일드카드는 `*`(0개 이상의 문자 일치) 및 `?`(0-1개의 문자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다.  더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예 |
| 옵션 4: 파일 목록<br>- fileListPath | 지정된 파일 집합을 복사하도록 지정합니다. 복사할 파일 목록이 포함 된 텍스트 파일을 가리키고, 데이터 집합에 구성 된 경로에 대 한 상대 경로인 한 줄에 하나씩 파일을 표시 합니다.<br/>이 옵션을 사용하는 경우 데이터 세트에서 파일 이름을 지정하지 마세요. [파일 목록 예](#file-list-examples)에서 더 많은 예를 참조하세요. |예 |
| ***추가 설정:*** |  | |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다. <br>허용되는 값은 **true**(기본값) 및 **false**입니다.<br>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. |예 |
| deleteFilesAfterCompletion | 대상 저장소로 이동한 후에 소스 저장소에서 이진 파일을 삭제할지 여부를 나타냅니다. 파일 삭제는 파일 단위 이므로 복사 작업에 실패 하면 일부 파일이 이미 대상에 복사 되 고 원본에서 삭제 된 것을 확인할 수 있습니다. 반면 다른 파일은 원본 저장소에 남아 있습니다. <br/>이 속성은 이진 복사 시나리오 에서만 유효 합니다. 여기에서 데이터 원본 저장소는 Blob, ADLS Gen1, ADLS Gen2, S3, Google 클라우드 저장소, 파일, Azure 파일, SFTP 또는 FTP입니다. 기본값은 false입니다. |예 |
| modifiedDatetimeStart    | 특성에 기반한 파일 필터링: 마지막으로 수정한 날짜 <br>마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br> 속성은 NULL 일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.<br/>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. | 예                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 예                                           |
| Enable파티션 검색 | 분할 된 파일의 경우 파일 경로에서 파티션을 구문 분석할 지 여부를 지정 하 고 추가 원본 열로 추가 합니다.<br/>허용 되는 값은 **false** (기본값) 및 **true**입니다. | 예                                            |
| 파티션 (partitionRootPath) | 파티션 검색을 사용 하는 경우 분할 된 폴더를 데이터 열로 읽도록 절대 루트 경로를 지정 합니다.<br/><br/>지정 되지 않은 경우 기본적으로<br/>-원본에 있는 파일 또는 데이터 집합의 파일 경로를 사용 하는 경우 파티션 루트 경로는 데이터 집합에서 구성 된 경로입니다.<br/>-와일드 카드 폴더 필터를 사용 하는 경우 파티션 루트 경로는 첫 번째 와일드 카드 앞의 하위 경로입니다.<br/><br/>예를 들어 데이터 집합의 경로를 "root/folder/year = 2020/month = 08/day = 27"로 구성 한다고 가정 합니다.<br/>-파티션 루트 경로를 "root/folder/year = 2020"으로 지정 하는 경우 복사 작업은 파일 내의 열 외에도 각각 두 개의 열을 생성 하 `month` 고 `day` 값을 "08" 및 "27"로 생성 합니다.<br/>-파티션 루트 경로를 지정 하지 않으면 추가 열이 생성 되지 않습니다. | 예                                            |
| maxConcurrentConnections | 스토리지 저장소에 동시에 연결할 수 있는 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한하려는 경우에만 지정합니다. | 예                                           |

**예:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
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
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true,
                    "wildcardFolderPath": "myfolder*A",
                    "wildcardFileName": "*.csv"
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-data-lake-store-as-sink"></a>Azure Data Lake Store를 싱크로

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

형식 기반 복사 싱크의 `storeSettings` 설정에서 Azure Data Lake Store Gen1에 지원되는 속성은 다음과 같습니다.

| 속성                 | Description                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings`의 type 속성은 **AzureDataLakeStoreWriteSettings**로 설정되어야 합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 예       |
| expiryDateTime | 작성된 파일의 만료 시간을 지정합니다. 시간은 UTC 표준 시간, "2020-03-01T08:00:00Z" 형식으로 적용됩니다. 기본적으로 NULL이며, 작성된 파일이 만료되지 않음을 의미합니다. | 예 |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결할 수 있는 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한하려는 경우에만 지정합니다. | 예       |

**예:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Parquet output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "ParquetSink",
                "storeSettings":{
                    "type": "AzureDataLakeStoreWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```
### <a name="name-range-filter-examples"></a>이름 범위 필터 예

이 섹션에서는 이름 범위 필터의 결과 동작을 설명 합니다.

| 샘플 원본 구조 | ADF 구성 | 결과 |
|:--- |:--- |:--- |
|root<br/>&nbsp;&nbsp;&nbsp;&nbsp;은<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;bx.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;c<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file4.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;cx.csv| **데이터 세트:**<br>- 폴더 경로: `root`<br><br>**복사 작업 원본:**<br>-다음 이후에 나열: `a`<br>-앞에 나열: `b`| 그러면 다음 파일이 복사 됩니다.<br><br>root<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file2.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;ax.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;b<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;file3.csv |

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 와일드카드 필터가 있는 폴더 경로 및 파일 이름의 결과 동작에 대해 설명합니다.

| folderPath | fileName | recursive | 원본 폴더 구조 및 필터 결과(**굵게** 표시된 파일이 검색됨)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (비어 있음, 기본값 사용) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (비어 있음, 기본값 사용) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>파일 목록 예

이 섹션에서는 복사 작업 원본에서 파일 목록 경로를 사용하는 경우의 결과 동작을 설명합니다.

원본 폴더 구조가 다음과 같고 굵게 표시된 파일을 복사하려는 것으로 가정합니다.

| 샘플 원본 구조                                      | FileListToCopy.txt의 콘텐츠                             | ADF 구성                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;메타데이터<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **데이터 세트:**<br>- 폴더 경로: `root/FolderA`<br><br>**복사 작업 원본:**<br>- 파일 목록 경로: `root/Metadata/FileListToCopy.txt` <br><br>파일 목록 경로는 복사하려는 파일 목록이 포함된 동일한 데이터 저장소의 텍스트 파일을 가리키며, 데이터 세트에 구성된 경로의 상대 경로를 사용하여 한 줄에 하나의 파일을 가리킵니다. |

### <a name="examples-of-behavior-of-the-copy-operation"></a>복사 작업의 동작 예제

이 섹션에서는 다양한 `recursive` 및 `copyBehavior` 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상이 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1, File2, File3, File4 및 File5의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4, File5가 포함된 Subfolder1은 선택되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4, File5가 포함된 Subfolder1은 선택되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1과 File2의 내용이 자동 생성된 파일 이름이 있는 하나의 파일로 병합됩니다. File1에 대해 자동 생성된 이름<br/><br/>File3, File4, File5가 포함된 Subfolder1은 선택되지 않습니다. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Data Lake Storage Gen2에 ACL 유지

>[!TIP]
>일반적으로 Azure Data Lake Storage Gen1의 데이터를 Gen2에 복사하려면 연습 및 모범 사례에서 [Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1의 데이터를 Gen2에 복사](load-azure-data-lake-storage-gen2-from-gen1.md)를 참조하세요.

Data Lake Storage Gen1에서 Data Lake Storage Gen2로 업그레이드할 때 데이터 파일과 함께 ACL(액세스 제어 목록)을 복제하려면 [Data Lake Storage Gen1의 ACL 유지](copy-activity-preserve-metadata.md#preserve-acls)를 참조하세요.

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

데이터 흐름 매핑에서 데이터를 변환 하는 경우 다음 형식의 Azure Data Lake Storage Gen1에서 파일을 읽고 쓸 수 있습니다.
* [Avro](format-avro.md#mapping-data-flow-properties)
* [구분된 텍스트](format-delimited-text.md#mapping-data-flow-properties)
* [Excel](format-excel.md#mapping-data-flow-properties)
* [JSON](format-json.md#mapping-data-flow-properties)
* [Parquet](format-parquet.md#mapping-data-flow-properties)

서식 지정 설정은 해당 형식에 대 한 설명서에 있습니다. 자세한 내용은 데이터 흐름 매핑에서 데이터 흐름 매핑 및 [싱크 변환](data-flow-sink.md) [의 원본 변환](data-flow-source.md) 을 참조 하세요.

### <a name="source-transformation"></a>원본 변환

원본 변환에서 Azure Data Lake Storage Gen1의 컨테이너, 폴더 또는 개별 파일을 읽을 수 있습니다. **원본 옵션** 탭을 사용하여 파일을 읽는 방법을 관리할 수 있습니다. 

![원본 옵션](media/data-flow/sourceOptions1.png "원본 옵션")

**와일드카드 경로:** 와일드카드 패턴을 사용하면 ADF가 일치하는 각 폴더와 파일을 단일 원본 변환에서 반복하도록 지시합니다. 이는 단일 흐름 내에서 여러 파일을 처리하는 효과적인 방법입니다. 기존 와일드카드 패턴을 마우스로 가리키면 표시되는 + 기호를 사용하여 와일드카드 일치 패턴을 여러 개 추가합니다.

원본 컨테이너에서 패턴과 일치하는 일련의 파일을 선택합니다. 데이터 세트에는 컨테이너만 지정할 수 있습니다. 따라서 와일드카드 경로에는 루트 폴더의 폴더 경로도 포함되어야 합니다.

와일드카드 예제:

* ```*``` 모든 문자 집합 나타내기
* ```**``` 재귀 디렉터리 중첩 나타내기
* ```?``` 한 문자 바꾸기
* ```[]``` 괄호 안에 있는 하나 이상의 문자와 일치시키기

* ```/data/sales/**/*.csv``` /data/sales에서 csv 파일 모두 가져오기
* ```/data/sales/20??/**/``` 20세기의 파일 모두 가져오기
* ```/data/sales/*/*/*.csv``` /data/sales에서 csv 파일을 두 가지 수준으로 가져오기
* ```/data/sales/2004/*/12/[XY]1?.csv``` X 또는 Y 접두사가 두 자리 숫자로 시작하는 2004년 12월 자 csv 파일 모두 가져오기

**파티션 루트 경로:** 파일 원본에 ```key=value``` 형식으로 분할된 폴더(예: year=2019)가 있는 경우 해당 파티션 폴더 트리의 최상위 수준을 데이터 흐름 데이터 스트림의 열 이름에 할당할 수 있습니다.

먼저, 분할된 폴더와 읽으려는 리프 파일이 있는 경로를 모두 포함하도록 와일드카드를 설정합니다.

![파티션 원본 파일 설정](media/data-flow/partfile2.png "파티션 파일 설정")

파티션 루트 경로 설정을 사용하여 폴더 구조의 최상위 수준을 정의합니다. 데이터 미리 보기를 통해 데이터 콘텐츠를 볼 때 ADF가 각 폴더 수준에서 찾은 확인된 파티션을 추가함을 확인할 수 있습니다.

![파티션 루트 경로](media/data-flow/partfile1.png "파티션 루트 경로 미리 보기")

**파일 목록:** 파일 집합입니다. 처리할 상대 경로 파일 목록이 포함된 텍스트 파일을 만듭니다. 이 텍스트 파일을 가리킵니다.

**파일 이름을 저장할 열:** 원본 파일의 이름을 데이터 열에 저장합니다. 파일 이름 문자열을 저장하려면 여기에 새 열 이름을 입력합니다.

**완료 후 기간(일):** 데이터 흐름이 실행된 후 원본 파일을 사용해 아무 작업도 수행하지 않도록 선택하거나, 원본 파일을 삭제 또는 이동합니다. 이동 경로는 상대 경로입니다.

원본 파일을 다른 위치에 후처리로 이동하려면 먼저 파일 작업에서 "이동"을 선택합니다. 그런 다음, "원본" 디렉터리를 설정합니다. 경로에 와일드카드를 사용하지 않을 경우 "원본" 설정은 원본 폴더와 같은 폴더에 있습니다.

와일드카드가 있는 원본 경로를 사용하는 경우 구문은 다음과 같습니다.

```/data/sales/20??/**/*.csv```

"원본"은 다음과 같이 지정할 수 있습니다.

```/data/sales```

"대상"은 다음과 같이 지정할 수 있습니다.

```/backup/priorSales```

이 경우 /data/sale 아래에 원본이 있는 모든 파일이 /backup/priorSales로 이동됩니다.

> [!NOTE]
> 파이프라인에서 Data Flow 실행 작업을 사용하는 파이프라인 실행(파이프라인 디버그 또는 실행)에서 데이터 흐름을 시작하는 경우에만 파일 작업이 실행됩니다. Data Flow 디버그 모드에서는 파일 작업이 실행되지 *않습니다.*

**마지막으로 수정한 사람 필터링:** 마지막으로 수정된 날짜 범위를 지정하여 처리하는 파일을 필터링할 수 있습니다. 모든 날짜-시간은 UTC 기준입니다. 

### <a name="sink-properties"></a>싱크 속성

싱크 변환에서 Azure Data Lake Storage Gen1의 컨테이너 또는 폴더에 쓸 수 있습니다. **설정** 탭을 사용하여 파일을 작성하는 방법을 관리할 수 있습니다.

![싱크 옵션](media/data-flow/file-sink-settings.png "싱크 옵션")

**폴더 선택 취소:** 데이터를 쓰기 전에 대상 폴더를 지울 것인지 여부를 결정합니다.

**파일 이름 옵션:** 대상 폴더에서 대상 파일의 이름을 지정하는 방법을 결정합니다. 파일 이름 옵션은 다음과 같습니다.
   * **기본값**: Spark에서 PART 기본값에 따라 파일 이름을 지정하도록 허용합니다.
   * **패턴**: 파티션별로 출력 파일을 열거하는 패턴을 입력합니다. 예를 들어, **loans[n].csv**에서 loans1.csv, loans2.csv 등을 만듭니다.
   * **파티션 기준**: 파티션별 파일 이름을 입력합니다.
   * **열의 데이터로**: 출력 파일을 열 값으로 설정합니다. 경로는 대상 폴더가 아닌 데이터 세트 컨테이너를 기준으로 합니다. 데이터 세트에 폴더 경로가 있는 경우 재정의됩니다.
   * **단일 파일로 출력**: 분할된 출력 파일을 명명된 단일 파일로 결합합니다. 경로는 데이터 세트 폴더를 기준으로 합니다. 병합 작업은 노드 크기에 따라 실패할 수 있습니다. 이 옵션은 대용량의 데이터 세트에는 권장되지 않습니다.

**모두 인용:** 모든 값을 따옴표로 묶을 것인지 여부를 결정합니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 작업](control-flow-get-metadata-activity.md)을 확인하세요. 

## <a name="delete-activity-properties"></a>삭제 작업 속성

속성에 대한 자세한 내용을 보려면 [삭제 작업](delete-activity.md)을 확인하세요.

## <a name="legacy-models"></a>레거시 모델

>[!NOTE]
>다음 모델은 이전 버전과의 호환성을 위해 그대로 계속 지원됩니다. 앞의 섹션에서 설명한 새 모델을 사용하는 것이 좋습니다. 그러면 ADF 작성 UI가 새 모델을 생성하도록 전환됩니다.

### <a name="legacy-dataset-model"></a>레거시 데이터 세트 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **AzureDataLakeStoreFile**로 설정해야 합니다. |예 |
| folderPath | Data Lake Store의 폴더 경로입니다. 지정하지 않으면 루트를 가리킵니다. <br/><br/>와일드카드 필터는 지원되지 않습니다. 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. 실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`를 사용하여 이스케이프합니다. <br/><br/>예: rootfolder/subfolder/ 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. |예 |
| fileName | 지정된 "folderPath" 아래의 파일에 대한 이름 또는 와일드카드 필터입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>필터의 경우 와일드카드에는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)가 허용됩니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>실제 파일 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`를 사용하여 이스케이프합니다.<br/><br/>fileName이 출력 데이터 세트에 대해 지정되지 않고 **preserveHierarchy**가 작업 싱크에 지정되지 않으면, 복사 작업은 다음과 같은 패턴으로 파일 이름을 자동으로 생성합니다. "*Data.[활동 실행 ID GUID].[FlattenHierarchy인 경우 GUID].[구성된 경우 형식].[구성된 경우 압축compression if configured]* ", 예: "Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.gz". 쿼리 대신 테이블 이름을 사용하여 테이블 형식 원본에서 복사하는 경우 이름 패턴은 " *[테이블 이름].[형식].[구성된 경우 압축]* "(예: "MyTable.csv")입니다. |예 |
| modifiedDatetimeStart | 마지막으로 수정한 특성에 따라 파일을 필터링합니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd`의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 대용량 파일을 사용하여 파일을 필터링하려는 경우 이 설정을 사용하면 데이터 이동의 전반적인 성능이 영향을 받습니다. <br/><br/> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다. `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다. `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 예 |
| modifiedDatetimeEnd | 마지막으로 수정한 특성에 따라 파일을 필터링합니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd`의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 대용량 파일을 사용하여 파일을 필터링하려는 경우 이 설정을 사용하면 데이터 이동의 전반적인 성능이 영향을 받습니다. <br/><br/> 속성은 NULL일 수 있습니다. 이 경우 파일 특성 필터가 데이터 세트에 적용되지 않습니다. `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 크거나 같은 파일이 선택됩니다. `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart`가 NULL이면, 마지막으로 수정된 특성이 datetime 값보다 작은 파일이 선택됩니다.| 예 |
| format | 파일 기반 저장소(이진 복사) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석하거나 생성하려는 경우, 지원되는 파일 형식 유형은 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat**입니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |예 |

>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 **folderPath**만을 지정합니다.<br>특정 이름의 단일 파일을 복사하려면 폴더 부분으로 **folderPath** 및 파일 이름으로 **fileName**을 지정합니다.<br>폴더 아래에서 파일의 하위 집합을 복사하려면 폴더 부분으로 **folderPath**를, 와일드카드 필터로 **fileName**을 지정합니다. 

**예:**

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

### <a name="legacy-copy-activity-source-model"></a>레거시 복사 작업 원본 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 `type` 속성을 **AzureDataLakeStoreSource**로 설정해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. `recursive`를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 예 |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결할 수 있는 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한하려는 경우에만 지정합니다. | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyFromADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<ADLS Gen1 input dataset name>",
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

### <a name="legacy-copy-activity-sink-model"></a>레거시 복사 작업 싱크 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 `type` 속성을 **AzureDataLakeStoreSink**로 설정해야 합니다. |예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않은 경우 파일 이름이 자동으로 생성됩니다. | 예 |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결할 수 있는 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한하려는 경우에만 지정합니다. | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyToADLSGen1",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<ADLS Gen1 output dataset name>",
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

## <a name="next-steps"></a>다음 단계

Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
