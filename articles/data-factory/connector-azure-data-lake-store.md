---
title: Azure Data Lake Storage Gen1 간 데이터 복사
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
ms.date: 12/12/2019
ms.openlocfilehash: 4dae0d10f103710a0e6039127c5c1cacb63c03c4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893098"
---
# <a name="copy-data-to-or-from-azure-data-lake-storage-gen1-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure Data Lake Storage Gen1 간에 데이터 복사

> [!div class="op_single_selector" title1="사용 중인 Azure Data Factory의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-azure-datalake-connector.md)
> * [현재 버전](connector-azure-data-lake-store.md)

이 문서에서는 Azure Data Lake Storage Gen1 간에 데이터를 복사 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure Data Lake Storage Gen1 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md) 
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [작업 삭제](delete-activity.md)

특히이 커넥터를 사용 하 여 다음을 수행할 수 있습니다.

- 다음 인증 방법 중 하나를 사용 하 여 파일을 복사 합니다. 서비스 주체 또는 Azure 리소스에 대한 관리 되는 id입니다.
- 파일을 있는 그대로 복사 하거나 [지원 되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs.md)을 사용 하 여 파일을 구문 분석 하거나 생성 합니다.
- Azure Data Lake Storage Gen2에 복사할 때 [acl을 유지](#preserve-acls-to-data-lake-storage-gen2) 합니다.

> [!IMPORTANT]
> 자체 호스팅 통합 런타임을 사용 하 여 데이터를 복사 하는 경우 포트 443에 대한 아웃 바운드 트래픽을 `<ADLS account name>.azuredatalakestore.net` 하 고 `login.microsoftonline.com/<tenant>/oauth2/token` 하도록 회사 방화벽을 구성 합니다. 후자는 액세스 토큰을 가져오기 위해 통합 런타임이 통신해야 하는 Azure 보안 토큰 서비스입니다.

## <a name="get-started"></a>시작하기

> [!TIP]
> Azure Data Lake Store 커넥터를 사용 하는 방법에 대한 연습은 [Azure Data Lake Store에 데이터 로드](load-azure-data-lake-store.md)를 참조 하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Data Lake Store에 한정 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대한 정보를 제공 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Data Lake Store 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | `type` 속성은 **AzureDataLakeStore**로 설정해야 합니다. | 예 |
| dataLakeStoreUri | Azure Data Lake Store 계정에 대한 정보. 이 정보는 `https://[accountname].azuredatalakestore.net/webhdfs/v1` 또는 `adl://[accountname].azuredatalakestore.net/` 형식 중 하나를 사용합니다. | 예 |
| subscriptionId | Data Lake Store 계정이 속하는 Azure 구독 ID입니다. | 싱크에 필요 |
| resourceGroupName | Data Lake Store 계정이 속하는 Azure 리소스 그룹 이름입니다. | 싱크에 필요 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 데이터 저장소가 개인 네트워크에 있는 경우 Azure integration runtime 또는 자체 호스팅 integration runtime을 사용할 수 있습니다. 이 속성을 지정 하지 않으면 기본 Azure 통합 런타임이 사용 됩니다. |아닙니다. |

### <a name="use-service-principal-authentication"></a>서비스 주체 인증 사용

서비스 주체 인증을 사용 하려면 다음 단계를 수행 합니다.

1. Azure Active Directory에 응용 프로그램 엔터티를 등록 하 고 Data Lake Store에 대한 액세스 권한을 부여 합니다. 자세한 단계는 [서비스 간 인증](../data-lake-store/data-lake-store-authenticate-using-active-directory.md)을 참조하세요. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. 서비스 주체에 게 적절 한 권한을 부여 합니다. [Azure Data Lake Storage Gen1의 Access control](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)Data Lake Storage Gen1에서 사용 권한이 작동 하는 방법에 대한 예제를 참조 하십시오.

    - **원본으로**: **데이터 탐색기** > **액세스**에서 복사할 파일에 대한 **읽기** 권한과 함께 루트를 비롯 한 모든 업스트림 폴더에 대해 최소 **실행** 권한을 부여 합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. 계정 수준 액세스 제어 (IAM)에 대한 요구 사항은 없습니다.
    - **Sink**: **데이터 탐색기** > **Access**에서 싱크 폴더에 대한 **쓰기** 권한과 함께 루트를 비롯 한 모든 업스트림 폴더에 대해 최소 **실행** 권한을 부여 합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. Azure integration runtime을 사용 하 여 복사 하는 경우 (원본 및 싱크가 모두 클라우드에 있는 경우) IAM에서 Data Lake Store 지역을 Data Factory 검색할 수 있도록 적어도 **판독기** 역할을 부여 합니다. 이 IAM 역할을 방지하려면 Data Lake Store의 위치에 명시적으로 [Azure Integration Runtime을 만듭니다](create-azure-integration-runtime.md#create-azure-ir). 예를 들어 Data Lake Store 유럽 서부에 있는 경우 위치를 "유럽 서부"로 설정 하 여 Azure integration runtime을 만듭니다. 다음 예제와 같이 Data Lake Store 연결 된 서비스에서 연결 합니다.

다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 `SecureString`으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 응용 프로그램이 있는 테넌트 정보 (예: 도메인 이름 또는 테넌트 ID)를 지정 합니다. Azure Portal의 오른쪽 위 모서리에 마우스를 이동하여 검색할 수 있습니다. | 예 |

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

### <a name="managed-identity"></a> Azure 리소스 인증에 관리 ID 사용

특정 데이터 팩터리를 나타내는 [Azure 리소스용 관리 ID](data-factory-service-identity.md)와 데이터 팩터리를 연결할 수 있습니다. 사용자 고유의 서비스 주체를 사용 하는 것과 마찬가지로이 관리 되는 id를 Data Lake Store 인증에 직접 사용할 수 있습니다. 이 지정된 팩터리는 Data Lake Store 간에 데이터에 액세스하고 복사할 수 있습니다.

Azure 리소스 인증에 관리 되는 id를 사용 하려면 다음 단계를 수행 합니다.

1. 팩터리와 함께 생성 된 "서비스 Id 응용 프로그램 ID"의 값을 복사 하 여 [데이터 팩터리 관리 id 정보를 검색 합니다](data-factory-service-identity.md#retrieve-managed-identity) .

2. Data Lake Store에 대한 관리 id 액세스 권한을 부여 합니다. [Azure Data Lake Storage Gen1의 Access control](../data-lake-store/data-lake-store-access-control.md#common-scenarios-related-to-permissions)Data Lake Storage Gen1에서 사용 권한이 작동 하는 방법에 대한 예제를 참조 하십시오.

    - **원본으로**: **데이터 탐색기** > **액세스**에서 복사할 파일에 대한 **읽기** 권한과 함께 루트를 비롯 한 모든 업스트림 폴더에 대해 최소 **실행** 권한을 부여 합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. 계정 수준 액세스 제어 (IAM)에 대한 요구 사항은 없습니다.
    - **Sink**: **데이터 탐색기** > **Access**에서 싱크 폴더에 대한 **쓰기** 권한과 함께 루트를 비롯 한 모든 업스트림 폴더에 대해 최소 **실행** 권한을 부여 합니다. **이 폴더 및 모든 하위 폴더**에 재귀적으로 추가하고 **액세스 권한 및 기본 권한 항목**으로 추가하도록 선택할 수 있습니다. Azure integration runtime을 사용 하 여 복사 하는 경우 (원본 및 싱크가 모두 클라우드에 있는 경우) IAM에서 Data Lake Store 지역을 Data Factory 검색할 수 있도록 적어도 **판독기** 역할을 부여 합니다. 이 IAM 역할을 방지하려면 Data Lake Store의 위치에 명시적으로 [Azure Integration Runtime을 만듭니다](create-azure-integration-runtime.md#create-azure-ir). 다음 예제와 같이 Data Lake Store 연결 된 서비스에서 연결 합니다.

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

다음 속성은 형식 기반 데이터 집합의 `location` 설정에서 Azure Data Lake Store Gen1에 대해 지원 됩니다.

| 속성   | Description                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합의 `location`에 있는 type 속성은 **AzureDataLakeStoreLocation**로 설정 해야 합니다. | 예      |
| folderPath | 폴더의 경로입니다. 와일드 카드를 사용 하 여 폴더를 필터링 하려면이 설정을 건너뛰고 활동 원본 설정에서 지정 합니다. | 아닙니다.       |
| fileName   | 지정 된 folderPath의 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정을 건너뛰고 작업 원본 설정에서 지정 합니다. | 아닙니다.       |

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

다음 속성은 형식 기반 복사 원본의 `storeSettings` 설정에서 Azure Data Lake Store Gen1에 대해 지원 됩니다.

| 속성                 | Description                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | `storeSettings` 아래의 type 속성은 **AzureDataLakeStoreReadSettings**로 설정 해야 합니다. | 예                                           |
| recursive                | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. Recursive가 true로 설정 되 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다.                                            |
| wildcardFolderPath       | 원본 폴더를 필터링 할 와일드 카드 문자가 포함 된 폴더 경로입니다. <br>허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 `?` (0 개 또는 단일 문자와 일치)입니다. 실제 폴더 이름에 와일드 카드 또는이 이스케이프 문자가 포함 된 경우 `^`를 사용 하 여 이스케이프 합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 아닙니다.                                            |
| wildcardFileName         | 소스 파일을 필터링 하기 위해 지정 된 folderPath/wildcardFolderPath 아래의 와일드 카드 문자가 포함 된 파일 이름입니다. <br>허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 `?` (0 개 또는 단일 문자와 일치)입니다. 실제 폴더 이름에 와일드 카드 또는이 이스케이프 문자가 포함 된 경우 `^`를 사용 하 여 이스케이프 합니다. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | `fileName` 데이터 집합에 지정 되지 않은 경우에는 예입니다. |
| modifiedDatetimeStart    | 마지막으로 수정한 특성을 기반으로 하는 파일 필터입니다. 파일은 마지막으로 수정한 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd`사이의 시간 범위 내에 있는 경우 선택 됩니다. 시간은 "2018-12-01T05:00:00Z" 형식으로 UTC 표준 시간대에 적용 됩니다. <br> 속성은 NULL 일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다. `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 된 것입니다. `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 된 것입니다. | 아닙니다.                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 아닙니다.                                            |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다.                                            |

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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

다음은 형식 기반 복사 싱크의 `storeSettings` 설정에서 Azure Data Lake Store Gen1에 대해 지원 되는 속성입니다.

| 속성                 | Description                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | `storeSettings` 아래의 type 속성은 **AzureDataLakeStoreWriteSettings**로 설정 해야 합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 아닙니다.       |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다.       |

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

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 와일드카드 필터가 있는 폴더 경로 및 파일 이름의 결과 동작에 대해 설명합니다.

| folderPath | fileName | recursive | 원본 폴더 구조 및 필터 결과(**굵게** 표시된 파일이 검색됨)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (비어 있고 기본값 사용) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (비어 있고 기본값 사용) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="examples-of-behavior-of-the-copy-operation"></a>복사 작업의 동작 예제

이 섹션에서는 다양한 `recursive` 및 `copyBehavior` 값 조합에 대한 복사 작업의 결과 동작을 설명합니다.

| recursive | copyBehavior | 원본 폴더 구조 | 결과 대상 |
|:--- |:--- |:--- |:--- |
| true |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상이 다음과 같이 원본 폴더와 동일한 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5. |
| true |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File3에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File4에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File5에 대해 자동 생성된 이름 |
| true |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다. <br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 + File3 + File4 + File5.txt 콘텐츠는 자동으로 생성 된 파일 이름으로 하나의 파일로 병합 됩니다. |
| false |preserveHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>File3, File4 및 File5.txt를 사용 하는 Subfolder1는 선택 되지 않습니다. |
| false |flattenHierarchy | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1에 대해 자동 생성된 이름<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2에 대해 자동 생성된 이름<br/><br/>File3, File4 및 File5.txt를 사용 하는 Subfolder1는 선택 되지 않습니다. |
| false |mergeFiles | Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 | Folder1 대상 폴더가 다음과 같은 구조로 만들어집니다.<br/><br/>Folder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1 + File2 콘텐츠가 자동 생성 된 파일 이름을 가진 하나의 파일로 병합 됩니다. File1에 대해 자동 생성된 이름<br/><br/>File3, File4 및 File5.txt를 사용 하는 Subfolder1는 선택 되지 않습니다. |

## <a name="preserve-acls-to-data-lake-storage-gen2"></a>Data Lake Storage Gen2에 Acl 유지

>[!TIP]
>Azure Data Lake Storage Gen1에서 일반적인 방법으로 데이터를 Gen2에 복사 하려면 연습 및 모범 사례에 대한 [Azure Data Factory를 사용 하 여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사](load-azure-data-lake-storage-gen2-from-gen1.md) 를 참조 하세요.

Data Lake Storage Gen1에서 Data Lake Storage Gen2로 업그레이드 하는 경우 데이터 파일과 함께 Acl (액세스 제어 목록)을 복제 하려면 [Data Lake Storage Gen1에서 Acl 유지](copy-activity-preserve-metadata.md#preserve-acls)를 참조 하세요.

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

매핑 데이터 흐름에서 데이터를 변환할 때 JSON, Avro, 구분 기호로 분리 된 텍스트 또는 Parquet 형식으로 Azure Data Lake Storage Gen1에서 파일을 읽고 쓸 수 있습니다. 자세한 내용은 데이터 흐름 매핑 기능에서 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md) 을 참조 하세요.

### <a name="source-transformation"></a>원본 변환

원본 변환에서 Azure Data Lake Storage Gen1의 컨테이너, 폴더 또는 개별 파일을 읽을 수 있습니다. **원본 옵션** 탭에서 파일을 읽는 방법을 관리할 수 있습니다. 

![원본 옵션](media/data-flow/sourceOptions1.png "원본 옵션")

**와일드 카드 경로:** 와일드 카드 패턴을 사용 하면 ADF가 일치 하는 각 폴더와 파일을 단일 원본 변환에서 반복 하도록 지시할 수 있습니다. 단일 흐름 내에서 여러 파일을 처리 하는 효과적인 방법입니다. 기존 와일드 카드 패턴을 가리킬 때 표시 되는 + 기호를 사용 하 여 와일드 카드 일치 패턴을 여러 개 추가 합니다.

소스 컨테이너에서 패턴과 일치 하는 일련의 파일을 선택 합니다. 데이터 집합에는 컨테이너만 지정할 수 있습니다. 따라서 와일드 카드 경로에도 루트 폴더의 폴더 경로가 포함 되어야 합니다.

와일드 카드 예제:

* ```*```는 모든 문자 집합을 나타냅니다.
* 재귀 디렉터리 중첩을 나타내는 ```**```
* ```?``` 한 문자를 대체 합니다.
* ```[]```는 대괄호 안에 있는 하나 이상의 문자를 찾습니다.

* />/> 아래에 있는 모든 csv 파일을 ```/data/sales/**/*.csv``` 가져옵니다.
* ```/data/sales/20??/**``` 20 세기의 모든 파일을 가져옵니다.
* 12 월 2004에 X 또는 Y 접두사가 2 자리 숫자로 시작 하는 모든 csv 파일을 ```/data/sales/2004/*/12/[XY]1?.csv``` 가져옵니다.

**파티션 루트 경로:** 파일 원본에 ```key=value``` 형식으로 분할 된 폴더가 있는 경우 (예: year = 2019) 해당 파티션 폴더 트리의 최상위 수준을 데이터 흐름 데이터 스트림의 열 이름에 할당할 수 있습니다.

먼저 분할 된 폴더와 읽으려는 리프 파일의 모든 경로를 포함 하도록 와일드 카드를 설정 합니다.

![파티션 원본 파일 설정](media/data-flow/partfile2.png "파티션 파일 설정")

파티션 루트 경로 설정을 사용 하 여 폴더 구조의 최상위 수준을 정의 합니다. 데이터 미리 보기를 통해 데이터의 내용을 볼 때 ADF가 각 폴더 수준에 있는 확인 된 파티션을 추가 하는 것을 볼 수 있습니다.

![파티션 루트 경로](media/data-flow/partfile1.png "파티션 루트 경로 미리 보기")

**파일 목록:** 이 파일은 파일 집합입니다. 처리할 상대 경로 파일 목록이 포함 된 텍스트 파일을 만듭니다. 이 텍스트 파일을 가리킵니다.

**저장할 열 파일 이름:** 원본 파일의 이름을 데이터의 열에 저장 합니다. 여기에 새 열 이름을 입력 하 여 파일 이름 문자열을 저장 합니다.

**완료 후:** 데이터 흐름이 실행 된 후 원본 파일을 사용 하 여 아무 작업도 수행 하지 않도록 선택 하거나 원본 파일을 삭제 하거나 원본 파일을 이동 합니다. 이동 경로는 상대 경로입니다.

원본 파일을 다른 위치로 처리 후에 이동 하려면 먼저 파일 작업에 대해 "이동"을 선택 합니다. 그런 다음 "from" 디렉터리를 설정 합니다. 경로에 와일드 카드를 사용 하지 않는 경우 "보낸 사용자" 설정은 원본 폴더와 같은 폴더에 있습니다.

와일드 카드가 있는 원본 경로를 사용 하는 경우 구문은 다음과 같습니다.

```/data/sales/20??/**/*.csv```

다음으로 "시작"을 지정할 수 있습니다.

```/data/sales```

및 "to"로

```/backup/priorSales```

이 경우/cv/ps 아래에서 소스인 모든 파일이/backup/priorSales.로 이동 됩니다.

> [!NOTE]
> 파이프라인에서 데이터 흐름 실행 작업을 사용 하는 파이프라인 실행 (파이프라인 디버그 또는 실행 실행)에서 데이터 흐름을 시작 하는 경우에만 파일 작업이 실행 됩니다. 파일 작업은 데이터 흐름 디버그 모드에서 실행 *되지* 않습니다.

**마지막으로 수정한 사람 필터링:** 마지막으로 수정 된 날짜 범위를 지정 하 여 처리 하는 파일을 필터링 할 수 있습니다. 모든 날짜/시간은 UTC 시간입니다. 

### <a name="sink-properties"></a>싱크 속성

싱크 변환에서 Azure Data Lake Storage Gen1의 컨테이너 또는 폴더에 쓸 수 있습니다. **설정** 탭에서 파일을 작성 하는 방법을 관리할 수 있습니다.

![싱크 옵션](media/data-flow/file-sink-settings.png "싱크 옵션")

**폴더 지우기:** 데이터를 쓰기 전에 대상 폴더를 지우도록 할지 여부를 결정 합니다.

**파일 이름 옵션:** 대상 폴더에서 대상 파일의 이름을 지정 하는 방법을 결정 합니다. 파일 이름 옵션은 다음과 같습니다.
   * **기본값**: SPARK에서 파트 기본값을 기준으로 파일의 이름을 지정할 수 있습니다.
   * **패턴**: 파티션당 출력 파일을 열거 하는 패턴을 입력 합니다. 예를 들어, **대출 [n] .csv** 는 loans1, loans2 등을 만듭니다.
   * **파티션당**: 파티션당 하나의 파일 이름을 입력 합니다.
   * **열의 데이터로**출력: 출력 파일을 열의 값으로 설정 합니다. 경로는 대상 폴더가 아니라 데이터 집합 컨테이너를 기준으로 합니다.
   * **단일 파일로 출력**: 분할 된 출력 파일을 단일 명명 된 파일로 결합 합니다. 경로는 데이터 집합 폴더에 대한 상대 경로입니다. Te merge 작업은 노드 크기에 따라 실패할 수 있습니다. 이 옵션은 대량 데이터 집합에는 권장 되지 않습니다.

**모두 인용:** 모든 값을 따옴표로 묶을 지 여부를 결정 합니다.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 활동 속성

속성에 대한 자세한 내용을 보려면 [GetMetadata 활동](control-flow-get-metadata-activity.md) 을 확인 하세요. 

## <a name="delete-activity-properties"></a>작업 속성 삭제

속성에 대한 자세한 내용을 보려면 [삭제 작업](delete-activity.md) 을 선택 합니다.

## <a name="legacy-models"></a>레거시 모델

>[!NOTE]
>다음 모델은 이전 버전과의 호환성을 위해 그대로 계속 지원 됩니다. 앞의 섹션에서 설명한 새 모델을 사용 하는 것이 좋습니다. 그러면 ADF 제작 UI가 새 모델을 생성 하도록 전환 됩니다.

### <a name="legacy-dataset-model"></a>레거시 데이터 집합 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **AzureDataLakeStoreFile**로 설정 해야 합니다. |예 |
| folderPath | Data Lake Store의 폴더 경로입니다. 지정하지 않으면 루트를 가리킵니다. <br/><br/>와일드 카드 필터가 지원 됩니다. 허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 `?` (0 개 또는 단일 문자와 일치)입니다. 실제 폴더 이름에 와일드 카드 또는이 이스케이프 문자가 포함 된 경우 `^`를 사용 하 여 이스케이프 합니다. <br/><br/>예: rootfolder/하위 폴더/. 더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. |아닙니다. |
| fileName | 지정 된 "folderPath" 아래에 있는 파일에 대한 이름 또는 와일드 카드 필터입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>필터의 경우 허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 `?` (0 개 또는 단일 문자와 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>실제 파일 이름에 와일드 카드 또는이 이스케이프 문자가 포함 된 경우 `^`를 사용 하 여 이스케이프 합니다.<br/><br/>FileName이 출력 데이터 집합에 대해 지정 되지 않고 **preserveHierarchy** 가 활동 싱크에 지정 되지 않은 경우 복사 작업은 다음 패턴으로 파일 이름을 자동으로 생성 합니다. "*Data. [ 작업 실행 ID입니다. [GUID if FlattenHierarchy]입니다. [구성 된 경우 형식]. [압축이 구성 된 경우]* "(예:" 0a405f8a-93ff-4c6f-b3be-f69616f1df7a. release.tar.gz "). 쿼리 대신 테이블 이름을 사용 하 여 테이블 형식 원본에서 복사 하는 경우 이름 패턴은 " *[table name]. [ format]. [압축이 구성 된 경우]* "(예:" MyTable "). |아닙니다. |
| modifiedDatetimeStart | 마지막으로 수정한 특성을 기반으로 하는 파일 필터입니다. 파일은 마지막으로 수정한 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd`사이의 시간 범위 내에 있는 경우 선택 됩니다. 시간은 "2018-12-01T05:00:00Z" 형식으로 UTC 표준 시간대에 적용 됩니다. <br/><br/> 대용량 파일을 사용 하 여 파일을 필터링 하려는 경우이 설정을 사용 하면 데이터 이동의 전반적인 성능이 영향을 받습니다. <br/><br/> 속성은 NULL 일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다. `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 된 것입니다. `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 된 것입니다.| 아닙니다. |
| modifiedDatetimeEnd | 마지막으로 수정한 특성을 기반으로 하는 파일 필터입니다. 파일은 마지막으로 수정한 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd`사이의 시간 범위 내에 있는 경우 선택 됩니다. 시간은 "2018-12-01T05:00:00Z" 형식으로 UTC 표준 시간대에 적용 됩니다. <br/><br/> 대용량 파일을 사용 하 여 파일을 필터링 하려는 경우이 설정을 사용 하면 데이터 이동의 전반적인 성능이 영향을 받습니다. <br/><br/> 속성은 NULL 일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다. `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 된 것입니다. `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 된 것입니다.| 아닙니다. |
| format | 파일 기반 저장소 (이진 복사) 간에 파일을 있는 그대로 복사 하려는 경우 입력 및 출력 데이터 집합 정의 둘 다에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식으로 파일을 구문 분석하거나 생성하려면 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** 및 **ParquetFormat** 파일 형식 유형이 지원됩니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |아닙니다. |

>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 **folderPath**만을 지정합니다.<br>특정 이름의 단일 파일을 복사 하려면 폴더 부분과 파일 **이름으로 파일** 이름으로 **folderPath** 를 지정 합니다.<br>폴더 아래에 있는 파일의 하위 집합을 복사 하려면 와일드 카드 필터를 사용 하 여 폴더 파트 및 **파일 이름** 으로 **folderPath** 를 지정 합니다. 

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

### <a name="legacy-copy-activity-source-model"></a>레거시 복사 활동 원본 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 `type` 속성은 **AzureDataLakeStoreSource**로 설정 해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. `recursive`이 true로 설정 되 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다. 허용되는 값은 **true**(기본값) 및 **false**입니다. | 아닙니다. |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다. |

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

### <a name="legacy-copy-activity-sink-model"></a>레거시 복사 활동 싱크 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 `type` 속성은 **AzureDataLakeStoreSink**로 설정 해야 합니다. |예 |
| copyBehavior | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않은 경우 파일 이름이 자동으로 생성됩니다. | 아닙니다. |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결 하기 위한 연결 수입니다. 데이터 저장소에 대한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 아닙니다. |

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
