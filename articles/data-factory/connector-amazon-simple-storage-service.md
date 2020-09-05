---
title: Amazon S3(Simple Storage Service)에서 데이터 복사
description: Azure Data Factory를 사용하여 Amazon S3(단순 저장 서비스)에서 지원되는 싱크 데이터 스토리지로 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 584aafccf146f3f261a7d375ecb57dcde18d03d5
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89182547"
---
# <a name="copy-data-from-amazon-simple-storage-service-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Amazon Simple Storage 서비스에서 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
>
> * [버전 1](v1/data-factory-amazon-simple-storage-service-connector.md)
> * [현재 버전](connector-amazon-simple-storage-service.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Amazon S3(Simple Storage Service)에서 데이터를 복사하는 방법을 설명합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

>[!TIP]
>Amazon s 3에서 Azure Storage로 데이터 마이그레이션 시나리오에 대 한 자세한 내용은 [Azure Data Factory를 사용 하 여 amazon s 3의 데이터를 Azure Storage로 마이그레이션](data-migration-guidance-s3-azure-storage.md)을 참조 하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Amazon S3 커넥터는 다음과 같은 작업에 지원됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [삭제 작업](delete-activity.md)

특히이 Amazon S3 커넥터는 파일을 있는 그대로 복사 하거나 [지원 되는 파일 형식 및 압축 코덱을](supported-file-formats-and-compression-codecs.md)사용 하 여 파일을 구문 분석할 수 있도록 지원 합니다. [복사 중에 파일 메타 데이터를 유지](#preserve-metadata-during-copy)하도록 선택할 수도 있습니다. 커넥터는 [AWS Signature 버전 4](https://docs.aws.amazon.com/general/latest/gr/signature-version-4.html)를 사용하여 S3에 요청을 인증합니다.

>[!TIP]
>이 Amazon S3 커넥터를 사용 하 여 [Google Cloud storage](connector-google-cloud-storage.md)와 같은 *S3 호환 저장소 공급자*의 데이터를 복사할 수 있습니다. 연결된 서비스 구성에서 해당 서비스 URL을 지정합니다.

## <a name="required-permissions"></a>필요한 사용 권한

Amazon s 3에서 데이터를 복사 하려면 다음과 같은 사용 권한이 부여 되었는지 확인 합니다.

- **는** `s3:GetObject` `s3:GetObjectVersion` Amazon S3 개체 작업에 대 한 복사 작업 실행: 및입니다.
- **Data Factory GUI 제작의 경우** `s3:ListAllMyBuckets` 및 `s3:ListBucket` / `s3:GetBucketLocation` Amazon S3 버킷 작업의 경우입니다. 연결 테스트 및 파일 경로 검색과 같은 작업에도 사용 권한이 필요 합니다. 이러한 권한을 부여 하지 않으려면 연결 된 서비스 만들기 페이지에서 연결 테스트를 건너뛰고 데이터 집합 설정에서 직접 경로를 지정 합니다.

Amazon S3 사용 권한의 전체 목록은 AWS 사이트의 [정책에서 사용 권한 지정](https://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html) 을 참조 하세요.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)] 

다음 섹션에서는 Amazon S3에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Amazon S3 연결 된 서비스에 대해 지원 되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **Type** 속성은 **AmazonS3**로 설정 해야 합니다. | 예 |
| accessKeyId | 비밀 액세스 키의 ID입니다. |예 |
| secretAccessKey | 비밀 액세스 키 자체입니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| serviceUrl | 공식 Amazon S3 서비스 이외의 S3 호환 저장소 공급자에서 데이터를 복사 하는 경우 사용자 지정 S3 끝점을 지정 합니다. 예를 들어 Google Cloud Storage에서 데이터를 복사하려면 `https://storage.googleapis.com`을 지정합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure integration runtime 또는 자체 호스팅 integration runtime (데이터 저장소가 개인 네트워크에 있는 경우)을 사용할 수 있습니다. 이 속성이 지정 되지 않은 경우 서비스는 기본 Azure integration runtime을 사용 합니다. |예 |

이 커넥터에는 Amazon s 3에서 데이터를 복사 하기 위해 AWS Id 및 액세스 관리 (IAM) 계정에 대 한 액세스 키가 필요 합니다. [임시 보안 자격 증명](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp.html) 은 현재 지원 되지 않습니다.

>[!TIP]
>공식 Amazon S3 서비스 이외의 S3 호환 저장소에서 데이터를 복사 하는 경우 사용자 지정 S3 서비스 URL을 지정 합니다.

예를 들면 다음과 같습니다.

```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AmazonS3",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": {
                "type": "SecureString",
                "value": "<secret access key>"
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

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 데이터 집합의 설정에서 Amazon S3에 대해 지원 되는 속성은 `location` 다음과 같습니다.

| 속성   | Description                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합의 아래에 있는 **type** 속성은 `location` **AmazonS3Location**로 설정 해야 합니다. | 예      |
| bucketName | S3 버킷 이름입니다.                                          | 예      |
| folderPath | 지정 된 버킷 아래에 있는 폴더의 경로입니다. 와일드 카드를 사용 하 여 폴더를 필터링 하려면이 설정을 건너뛰고 활동 원본 설정에서 지정 합니다. | 예       |
| fileName   | 지정 된 버킷 및 폴더 경로 아래에 있는 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정을 건너뛰고 활동 원본 설정에서 지정 합니다. | 예       |
| 버전 | S3 버전 관리를 사용하도록 설정하면 S3 개체의 버전입니다. 지정 하지 않으면 최신 버전이 인출 됩니다. |예 |

**예:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AmazonS3Location",
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

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Amazon S3 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="amazon-s3-as-a-source-type"></a>Amazon S3을 원본 유형으로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 복사 원본의 설정에서 Amazon S3에 대해 지원 되는 속성은 `storeSettings` 다음과 같습니다.

| 속성                 | Description                                                  | 필수                                                    |
| ------------------------ | ------------------------------------------------------------ | ----------------------------------------------------------- |
| type                     | 아래의 **type** 속성은 `storeSettings` **AmazonS3ReadSettings**로 설정 해야 합니다. | 예                                                         |
| ***복사할 파일 찾기:*** |  |  |
| 옵션 1: 고정 경로<br> | 지정된 버킷 또는 데이터 세트에 지정된 폴더/파일 경로에서 복사합니다. 버킷 또는 폴더의 모든 파일을 복사 하려면을 `wildcardFileName` 로 지정 `*` 합니다. |  |
| 옵션 2: S3 접두사<br>- 접두사 | 원본 S3 파일을 필터링 하기 위해 데이터 집합에 구성 된 지정 된 버킷의 S3 키 이름에 대 한 접두사입니다. 이름이로 시작 하는 S3 키 `bucket_in_dataset/this_prefix` 가 선택 됩니다. S3's 서비스 쪽 필터를 활용 하 여 와일드 카드 필터 보다 더 나은 성능을 제공 합니다. | 예 |
| 옵션 3: 와일드카드<br>- wildcardFolderPath | 원본 폴더를 필터링 하기 위해 데이터 집합에 구성 된 지정 된 버킷 아래에 와일드 카드 문자가 있는 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. `^`폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다. <br>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예                                            |
| 옵션 3: 와일드카드<br>- wildcardFileName | 소스 파일을 필터링 하기 위해 지정 된 버킷 및 폴더 경로 (또는 와일드 카드 폴더 경로) 아래에 와일드 카드 문자가 있는 파일 이름입니다. <br>허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. `^`폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다.  더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. | 예 |
| 옵션 4: 파일 목록<br>- fileListPath | 지정된 파일 집합을 복사하도록 지정합니다. 복사할 파일 목록이 포함 된 텍스트 파일을 가리키고, 데이터 집합에 구성 된 경로에 대 한 상대 경로인 한 줄에 하나씩 파일을 표시 합니다.<br/>이 옵션을 사용 하는 경우 데이터 집합에서 파일 이름을 지정 하지 마십시오. [파일 목록 예](#file-list-examples)에서 더 많은 예를 참조하세요. |예 |
| ***추가 설정:*** |  | |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. **Recursive** 를 **true** 로 설정 하 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다. <br>허용되는 값은 **true**(기본값) 및 **false**입니다.<br>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. |예 |
| deleteFilesAfterCompletion | 대상 저장소로 이동한 후에 소스 저장소에서 이진 파일을 삭제할지 여부를 나타냅니다. 파일 삭제는 파일 단위 이므로 복사 작업에 실패 하면 일부 파일이 이미 대상에 복사 되 고 원본에서 삭제 된 것을 확인할 수 있습니다. 반면 다른 파일은 원본 저장소에 남아 있습니다. <br/>이 속성은 이진 복사 시나리오 에서만 유효 합니다. 여기에서 데이터 원본 저장소는 Blob, ADLS Gen1, ADLS Gen2, S3, Google 클라우드 저장소, 파일, Azure 파일, SFTP 또는 FTP입니다. 기본값은 false입니다. |예 |
| modifiedDatetimeStart    | 파일은 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 날짜입니다. <br>마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 "2018-12-01T05:00:00Z" 형식의 UTC 표준 시간대에 적용 됩니다. <br> 속성은 **NULL**일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 됩니다.<br/>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. | 예                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 예                                                          |
| Enable파티션 검색 | 분할 된 파일의 경우 파일 경로에서 파티션을 구문 분석할 지 여부를 지정 하 고 추가 원본 열로 추가 합니다.<br/>허용 되는 값은 **false** (기본값) 및 **true**입니다. | 예                                            |
| 파티션 (partitionRootPath) | 파티션 검색을 사용 하는 경우 분할 된 폴더를 데이터 열로 읽도록 절대 루트 경로를 지정 합니다.<br/><br/>지정 되지 않은 경우 기본적으로<br/>-원본에 있는 파일 또는 데이터 집합의 파일 경로를 사용 하는 경우 파티션 루트 경로는 데이터 집합에서 구성 된 경로입니다.<br/>-와일드 카드 폴더 필터를 사용 하는 경우 파티션 루트 경로는 첫 번째 와일드 카드 앞의 하위 경로입니다.<br/>-접두사를 사용 하는 경우 파티션 루트 경로는 마지막 "/" 앞의 하위 경로입니다. <br/><br/>예를 들어 데이터 집합의 경로를 "root/folder/year = 2020/month = 08/day = 27"로 구성 한다고 가정 합니다.<br/>-파티션 루트 경로를 "root/folder/year = 2020"으로 지정 하는 경우 복사 작업은 파일 내의 열 외에도 각각 두 개의 열을 생성 하 `month` 고 `day` 값을 "08" 및 "27"로 생성 합니다.<br/>-파티션 루트 경로를 지정 하지 않으면 추가 열이 생성 되지 않습니다. | 예                                            |
| maxConcurrentConnections | 데이터 저장소에 대 한 동시 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 예                                                          |

**예:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
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
                    "type": "AmazonS3ReadSettings",
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
| bucket<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;메타데이터<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **데이터 세트에서:**<br>- 버킷: `bucket`<br>- 폴더 경로: `FolderA`<br><br>**복사 활동 원본에서:**<br>- 파일 목록 경로: `bucket/Metadata/FileListToCopy.txt` <br><br>파일 목록 경로는 데이터 집합에 구성 된 경로에 대 한 상대 경로를 사용 하 여 복사 하려는 파일 목록이 포함 된 동일한 데이터 저장소의 텍스트 파일을 한 줄에 하나씩 가리킵니다. |

## <a name="preserve-metadata-during-copy"></a>복사하는 동안 메타데이터 유지

Amazon s 3에서 Azure Data Lake Storage Gen2 또는 Azure Blob Storage로 파일을 복사 하는 경우 데이터와 함께 파일 메타 데이터를 유지 하도록 선택할 수 있습니다. [메타데이터 유지](copy-activity-preserve-metadata.md#preserve-metadata)에서 자세히 알아보세요.

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

속성에 대 한 자세한 내용을 보려면 [GetMetadata 활동](control-flow-get-metadata-activity.md)을 확인 하세요. 

## <a name="delete-activity-properties"></a>삭제 작업 속성

속성에 대 한 자세한 내용을 보려면 [삭제 작업](delete-activity.md)을 선택 합니다.

## <a name="legacy-models"></a>레거시 모델

>[!NOTE]
>다음 모델은 이전 버전과의 호환성을 위해 계속 지원 됩니다. 앞에서 설명한 새 모델을 사용 하는 것이 좋습니다. Data Factory authoring UI가 새 모델을 생성 하도록 전환 되었습니다.

### <a name="legacy-dataset-model"></a>레거시 데이터 세트 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 **type** 속성은 **AmazonS3Object**로 설정 해야 합니다. |예 |
| bucketName | S3 버킷 이름입니다. 와일드 카드 필터는 지원 되지 않습니다. |복사 또는 조회 작업의 경우 예, GetMetadata 작업의 경우 아니요 |
| key | 지정 된 버킷 아래에 있는 S3 개체 키의 이름 또는 와일드 카드 필터입니다. **Prefix** 속성이 지정 되지 않은 경우에만 적용 됩니다. <br/><br/>와일드 카드 필터는 폴더 부분과 파일 이름 부분 모두에 대해 지원 됩니다. 허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다.<br/>- 예 1: `"key": "rootfolder/subfolder/*.csv"`<br/>- 예 2: `"key": "rootfolder/subfolder/???20180427.txt"`<br/>더 많은 예는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples)를 참조하세요. `^`실제 폴더 또는 파일 이름에 와일드 카드 또는이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다. |예 |
| 접두사 | S3 개체 키에 대한 접두사입니다. 이 접두사로 시작하는 키를 가진 개체가 선택됩니다. **키** 속성이 지정 되지 않은 경우에만 적용 됩니다. |예 |
| 버전 | S3 버전 관리를 사용하도록 설정하면 S3 개체의 버전입니다. 버전을 지정 하지 않으면 최신 버전이 인출 됩니다. |예 |
| modifiedDatetimeStart | 파일은 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 날짜입니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 이 설정을 사용 하면 대량의 파일을 필터링 하려는 경우 데이터 이동의 전반적인 성능에 영향을 줍니다. <br/><br/> 속성은 **NULL**일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 NULL 이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 됩니다.| 예 |
| modifiedDatetimeEnd | 파일은 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 날짜입니다. 마지막 수정 시간이 `modifiedDatetimeStart`와 `modifiedDatetimeEnd` 사이의 시간 범위 내에 있으면 파일이 선택됩니다. 시간은 UTC 표준 시간대에 "2018-12-01T05:00:00Z" 형식으로 적용됩니다. <br/><br/> 이 설정을 사용 하면 대량의 파일을 필터링 하려는 경우 데이터 이동의 전반적인 성능에 영향을 줍니다. <br/><br/> 속성은 **NULL**일 수 있습니다. 즉, 파일 특성 필터가 데이터 집합에 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 됩니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 **NULL**이면 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 됩니다.| 예 |
| format | 파일 기반 저장소(이진 복사) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석하거나 생성하려는 경우, 지원되는 파일 형식 유형은 **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat**, **ParquetFormat**입니다. **format**의 **type** 속성을 이 값 중 하나로 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원되는 형식은 **GZip**, **Deflate**, **BZip2** 및 **ZipDeflate**입니다.<br/>**Optimal** 및 **Fastest** 수준이 지원됩니다. |예 |

>[!TIP]
>폴더 아래의 모든 파일을 복사 하려면 버킷 및 폴더 부분의 **접두사** 에 대해 **bucketName** 를 지정 합니다.
>
>지정 된 이름의 단일 파일을 복사 하려면 폴더의 버킷 및 **키** 에 **bucketName** 을 지정 하 고 파일 이름에 파일 이름을 추가 합니다.
>
>폴더 아래에 있는 파일의 하위 집합을 복사 하려면 폴더에 대 한 버킷 및 **키** 에 **bucketName** 를 지정 하 고 와일드 카드 필터를 추가 합니다.

**예제: 접두사 사용**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3Object",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "prefix": "testFolder/test",
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

**예제: 키 및 버전 사용(선택 사항)**

```json
{
    "name": "AmazonS3Dataset",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": {
            "referenceName": "<Amazon S3 linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "bucketName": "testbucket",
            "key": "testFolder/testfile.csv.gz",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
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

### <a name="legacy-source-model-for-the-copy-activity"></a>복사 작업에 대 한 레거시 원본 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성은 **FileSystemSource**로 설정 해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. **Recursive** 를 **true** 로 설정 하 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다.<br/>허용되는 값은 **true**(기본값) 및 **false**입니다. | 예 |
| maxConcurrentConnections | 데이터 저장소에 동시에 연결할 수 있는 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만를 지정 합니다. | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyFromAmazonS3",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon S3 input dataset name>",
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
                "type": "FileSystemSource",
                "recursive": true
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조 하세요.
