---
title: Azure Data Factory를 사용 하 여 Google Cloud Storage에서 데이터 복사
description: Azure Data Factory를 사용하여 Google Cloud Storage에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: jingwang
ms.openlocfilehash: 73db78fd6b50b6d8c7cec3c528eabcc1210f3207
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89177978"
---
# <a name="copy-data-from-google-cloud-storage-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Google Cloud Storage에서 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 GCS(Google Cloud Storage)에서 데이터를 복사하는 방법을 간략하게 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Google Cloud Storage 커넥터는 다음 작업에 대해 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [삭제 작업](delete-activity.md)

특히이 Google Cloud Storage 커넥터는 파일을 있는 그대로 복사 하거나 [지원 되는 파일 형식 및 압축 코덱을](supported-file-formats-and-compression-codecs.md)사용 하 여 파일을 구문 분석할 수 있도록 지원 합니다. 이는 GC의 S3 호환 상호 운용성을 활용 합니다.

## <a name="prerequisites"></a>사전 요구 사항

Google Cloud Storage 계정에는 다음 설정이 필요 합니다.

1. Google Cloud Storage 계정에 대한 상호 운용성 사용
2. 대상 GC 버킷에 복사 하려는 데이터가 포함 된 기본 프로젝트를 설정 합니다.
3. GCP에서 클라우드 IAM을 사용 하 여 서비스 계정을 만들고 적절 한 수준의 권한을 정의 합니다. 
4. 이 서비스 계정에 대 한 액세스 키를 생성 합니다.

![Google Cloud Storage에 대한 액세스 키 검색](media/connector-google-cloud-storage/google-storage-cloud-settings.png)

## <a name="required-permissions"></a>필요한 사용 권한

Google Cloud Storage에서 데이터를 복사하려면 필요한 권한을 부여해야 합니다. 서비스 계정에 정의 된 권한에는 `storage.buckets.get` `storage.buckets.list` `storage.objects.get` 개체 작업에 대 한, 또는가 포함 될 수 있습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

다음 섹션에서는 Google Cloud Storage에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Google Cloud Storage 연결 된 서비스에 대해 지원 되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **Type** 속성은 **GoogleCloudStorage**로 설정 해야 합니다. | 예 |
| accessKeyId | 비밀 액세스 키의 ID입니다. 액세스 키와 비밀을 찾으려면 [필수 구성 요소](#prerequisites)를 참조하세요. |예 |
| secretAccessKey | 비밀 액세스 키 자체입니다. 이 필드를 **SecureString** 으로 표시 하 여 Data Factory에 안전 하 게 저장 하거나 [Azure Key Vault에 저장 된 암호를 참조](store-credentials-in-key-vault.md)합니다. |예 |
| serviceUrl | 사용자 지정 GCS 엔드포인트를 `https://storage.googleapis.com` 로 지정합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure integration runtime 또는 자체 호스팅 integration runtime (데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 이 속성이 지정 되지 않은 경우 서비스는 기본 Azure integration runtime을 사용 합니다. |예 |

예를 들면 다음과 같습니다.

```json
{
    "name": "GoogleCloudStorageLinkedService",
    "properties": {
        "type": "GoogleCloudStorage",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
            },
            "serviceUrl": "https://storage.googleapis.com"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

서식 기반 데이터 집합의 설정에서 Google Cloud Storage에 대해 지원 되는 속성은 다음과 `location` 같습니다.

| 속성   | Description                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합의 아래에 있는 **type** 속성은 `location` **GoogleCloudStorageLocation**로 설정 해야 합니다. | 예      |
| bucketName | GCS 버킷 이름입니다.                                          | 예      |
| folderPath | 지정된 버킷 아래의 폴더 경로입니다. 와일드 카드를 사용 하 여 폴더를 필터링 하려면이 설정을 건너뛰고 작업 원본 설정에서 지정 합니다. | 예       |
| fileName   | 지정 된 버킷 및 폴더 경로 아래에 있는 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정을 건너뛰고 작업 원본 설정에서 지정 합니다. | 예       |

**예:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Google Cloud Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "GoogleCloudStorageLocation",
                "bucketName": "bucketname",
                "folderPath": "folder/subfolder"
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Google Cloud Storage 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="google-cloud-storage-as-a-source-type"></a>Google Cloud Storage를 원본 유형으로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

다음은 `storeSettings` 형식 기반 복사 원본에서 설정 아래의 Google Cloud Storage에 대해 지원 되는 속성입니다.

| 속성                 | Description                                                  | 필수                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | 아래의 **type** 속성은 `storeSettings` **GoogleCloudStorageReadSettings**로 설정 해야 합니다. | 예                                                         |
| ***복사할 파일 찾기:*** |  |  |
| 옵션 1: 고정 경로<br> | 지정된 버킷 또는 데이터 세트에 지정된 폴더/파일 경로에서 복사합니다. 버킷 또는 폴더의 모든 파일을 복사 하려면을 `wildcardFileName` 로 지정 `*` 합니다. |  |
| 옵션 2: GCS 접두사<br>- 접두사 | 원본 GC 파일을 필터링 하기 위해 데이터 집합에 구성 된 지정 된 버킷의 GC 키 이름에 대 한 접두사입니다. 이름이로 시작 하는 GC 키 `bucket_in_dataset/this_prefix` 가 선택 됩니다. 이는 GC의 서비스 쪽 필터를 활용 하 여 와일드 카드 필터 보다 더 나은 성능을 제공 합니다. | 예 |
| 옵션 3: 와일드카드<br>- wildcardFolderPath | 원본 폴더를 필터링 하기 위해 데이터 집합에 구성 된 지정 된 버킷 아래에 와일드 카드 문자가 있는 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. `^`폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예                                            |
| 옵션 3: 와일드카드<br>- wildcardFileName | 소스 파일을 필터링 하기 위해 지정 된 버킷 및 폴더 경로 (또는 와일드 카드 폴더 경로) 아래에 와일드 카드 문자가 있는 파일 이름입니다. <br>허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. `^`폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다.  더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예 |
| 옵션 3: 파일 목록<br>- fileListPath | 지정된 파일 집합을 복사하도록 지정합니다. 복사할 파일 목록이 포함 된 텍스트 파일을 가리키고, 데이터 집합에 구성 된 경로에 대 한 상대 경로인 한 줄에 하나씩 파일을 표시 합니다.<br/>이 옵션을 사용 하는 경우 데이터 집합에서 파일 이름을 지정 하지 마십시오. [파일 목록 예](#file-list-examples)에서 더 많은 예를 참조하세요. |예 |
| ***추가 설정:*** |  | |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. **Recursive** 를 **true** 로 설정 하 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다. <br>허용되는 값은 **true**(기본값) 및 **false**입니다.<br>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. |예 |
| deleteFilesAfterCompletion | 대상 저장소로 이동한 후에 소스 저장소에서 이진 파일을 삭제할지 여부를 나타냅니다. 파일 삭제는 파일 단위 이므로 복사 작업에 실패 하면 일부 파일이 이미 대상에 복사 되 고 원본에서 삭제 된 것을 확인할 수 있습니다. 반면 다른 파일은 원본 저장소에 남아 있습니다. <br/>이 속성은 이진 복사 시나리오 에서만 유효 합니다. 여기에서 데이터 원본 저장소는 Blob, ADLS Gen1, ADLS Gen2, S3, Google 클라우드 저장소, 파일, Azure 파일, SFTP 또는 FTP입니다. 기본값은 false입니다. |예 |
| modifiedDatetimeStart    | 파일은 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 날짜입니다. <br>마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br> 속성은 **NULL**일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 됩니다.<br/>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. | 예                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 예                                                          |
| Enable파티션 검색 | 분할 된 파일의 경우 파일 경로에서 파티션을 구문 분석할 지 여부를 지정 하 고 추가 원본 열로 추가 합니다.<br/>허용 되는 값은 **false** (기본값) 및 **true**입니다. | 예                                            |
| 파티션 (partitionRootPath) | 파티션 검색을 사용 하는 경우 분할 된 폴더를 데이터 열로 읽도록 절대 루트 경로를 지정 합니다.<br/><br/>지정 되지 않은 경우 기본적으로<br/>-원본에 있는 파일 또는 데이터 집합의 파일 경로를 사용 하는 경우 파티션 루트 경로는 데이터 집합에서 구성 된 경로입니다.<br/>-와일드 카드 폴더 필터를 사용 하는 경우 파티션 루트 경로는 첫 번째 와일드 카드 앞의 하위 경로입니다.<br/><br/>예를 들어 데이터 집합의 경로를 "root/folder/year = 2020/month = 08/day = 27"로 구성 한다고 가정 합니다.<br/>-파티션 루트 경로를 "root/folder/year = 2020"으로 지정 하는 경우 복사 작업은 파일 내의 열 외에도 각각 두 개의 열을 생성 하 `month` 고 `day` 값을 "08" 및 "27"로 생성 합니다.<br/>-파티션 루트 경로를 지정 하지 않으면 추가 열이 생성 되지 않습니다. | 예                                            |
| maxConcurrentConnections | 저장소에 대 한 동시 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 예                                                          |

**예:**

```json
"activities":[
    {
        "name": "CopyFromGoogleCloudStorage",
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
                    "type": "GoogleCloudStorageReadSettings",
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

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 와일드카드 필터가 있는 폴더 경로 및 파일 이름의 결과 동작에 대해 설명합니다.

| bucket | key | recursive | 원본 폴더 구조 및 필터 결과(굵게 표시된 파일이 검색됨)|
|:--- |:--- |:--- |:--- |
| bucket | `Folder*/*` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | false | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| bucket | `Folder*/*.csv` | true | bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>파일 목록 예

이 섹션에서는 복사 작업 원본에서 파일 목록 경로를 사용 하는 경우의 결과 동작을 설명 합니다.

다음 원본 폴더 구조가 있고 파일을 굵게 표시 한다고 가정 합니다.

| 샘플 원본 구조                                      | FileListToCopy.txt의 콘텐츠                             | Data Factory 구성                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;메타데이터<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **데이터 세트에서:**<br>- 버킷: `bucket`<br>- 폴더 경로: `FolderA`<br><br>**복사 작업 원본:**<br>- 파일 목록 경로: `bucket/Metadata/FileListToCopy.txt` <br><br>파일 목록 경로는 데이터 집합에 구성 된 경로에 대 한 상대 경로를 사용 하 여 복사 하려는 파일 목록이 포함 된 동일한 데이터 저장소의 텍스트 파일을 한 줄에 하나씩 가리킵니다. |

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대 한 자세한 내용을 보려면 [GetMetadata 활동](control-flow-get-metadata-activity.md)을 확인 하세요. 

## <a name="delete-activity-properties"></a>삭제 작업 속성

속성에 대 한 자세한 내용을 보려면 [삭제 작업](delete-activity.md)을 선택 합니다.

## <a name="legacy-models"></a>레거시 모델

Amazon S3 커넥터를 사용 하 여 Google Cloud Storage에서 데이터를 복사 하는 경우 이전 버전과의 호환성을 위해 계속 지원 됩니다. 앞에서 설명한 새 모델을 사용 하는 것이 좋습니다. Data Factory authoring UI가 새 모델을 생성 하도록 전환 되었습니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조 하세요.
