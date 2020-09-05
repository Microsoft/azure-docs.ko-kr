---
title: SFTP 서버 간에 데이터 복사
description: Azure Data Factory를 사용 하 여 SFTP 서버에서 데이터를 복사 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/28/2020
ms.openlocfilehash: e8067f2aa485202412f0f0b6101eafb7768b222a
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181904"
---
# <a name="copy-data-from-and-to-the-sftp-server-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 SFTP 서버에서 데이터 복사

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-sftp-connector.md)
> * [현재 버전](connector-sftp.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 보안 FTP (SFTP) 서버에서 데이터를 복사 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

SFTP 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원되는 원본/싱크 매트릭스](copy-activity-overview.md)를 사용한 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [작업 삭제](delete-activity.md)

특히 SFTP 커넥터는 다음을 지원 합니다.

- *Basic* 또는 *SshPublicKey* 인증을 사용 하 여 SFTP 서버에서 파일을 복사 합니다.
- 파일을 있는 그대로 복사 하거나 [지원 되는 파일 형식 및 압축 코덱을](supported-file-formats-and-compression-codecs.md)사용 하 여 파일을 구문 분석 하거나 생성 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SFTP에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SFTP 연결 된 서비스에 대해 지원 되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | Type 속성은 *Sftp*로 설정 되어야 합니다. |예 |
| host | SFTP 서버의 이름 또는 IP 주소입니다. |예 |
| 포트 | SFTP 서버가 수신 대기 하는 포트입니다.<br/>허용 되는 값은 정수 이며 기본값은 *22*입니다. |예 |
| skipHostKeyValidation | 호스트 키 유효성 검사를 건너뛸지 여부를 지정합니다.<br/>허용 되는 값은 *true* 및 *false* (기본값)입니다.  | 예 |
| hostKeyFingerprint | 호스트 키의 지문을 지정 합니다. | 예 ("skipHostKeyValidation"이 false로 설정 된 경우)  |
| authenticationType | 인증 유형을 지정합니다.<br/>허용 되는 값은 *Basic* 및 *SshPublicKey*입니다. 자세한 속성은 [기본 인증 사용](#use-basic-authentication) 섹션을 참조 하세요. JSON 예제는 [SSH 공개 키 인증 사용](#use-ssh-public-key-authentication) 섹션을 참조 하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 자세히 알아보려면 [필수 조건](#prerequisites) 섹션을 참조 하세요. 통합 런타임이 지정 되지 않은 경우 서비스는 기본 Azure Integration Runtime를 사용 합니다. |예 |

### <a name="use-basic-authentication"></a>기본 인증 사용

기본 인증을 사용 하려면 *authenticationType* 속성을 *기본*으로 설정 하 고 이전 섹션에서 소개한 SFTP 커넥터 일반 속성 외에 다음 속성을 지정 합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| userName | SFTP 서버에 대 한 액세스 권한이 있는 사용자입니다. |예 |
| password | 사용자 (사용자 이름)의 암호입니다. 이 필드를 SecureString으로 표시 하 여 데이터 팩터리에 안전 하 게 저장 하거나 [Azure key vault에 저장 된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |

**예:**

```json
{
    "name": "SftpLinkedService",
    "type": "linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "authenticationType": "Basic",
            "userName": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-ssh-public-key-authentication"></a>SSH 공개 키 인증 사용

SSH 공개 키 인증을 사용하려면 “authenticationType” 속성을 **SshPublicKey**로 설정하고, 마지막 섹션에서 소개한 SFTP 커넥터 일반 속성 외에 다음 속성을 지정합니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| userName | SFTP 서버에 대 한 액세스 권한이 있는 사용자입니다. |예 |
| privateKeyPath | 통합 런타임에서 액세스할 수 있는 개인 키 파일의 절대 경로를 지정 합니다. 이는 자체 호스팅 형식의 integration runtime이 "connectVia"에 지정 된 경우에만 적용 됩니다. | `privateKeyPath` 또는 `privateKeyContent`를 지정합니다.  |
| privateKeyContent | Base64 인코딩된 SSH 프라이빗 키 콘텐츠입니다. SSH 프라이빗 키가 OpenSSH 형식이어야 합니다. 이 필드를 SecureString으로 표시 하 여 데이터 팩터리에 안전 하 게 저장 하거나 [Azure key vault에 저장 된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | `privateKeyPath` 또는 `privateKeyContent`를 지정합니다. |
| passPhrase | 키 파일이 나 키 내용이 암호문에 의해 보호 되는 경우 개인 키의 암호를 해독 하기 위한 암호문 또는 암호를 지정 합니다. 이 필드를 SecureString으로 표시 하 여 데이터 팩터리에 안전 하 게 저장 하거나 [Azure key vault에 저장 된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 (개인 키 파일 또는 키 콘텐츠가 전달 구에 의해 보호 되는 경우) |

> [!NOTE]
> SFTP 커넥터는 RSA/DSA OpenSSH 키를 지원 합니다. 키 파일 콘텐츠가 "-----BEGIN [RSA/DSA] 개인 키-----"로 시작 하는지 확인 합니다. 개인 키 파일이 PPK 서식 파일인 경우 PuTTY 도구를 사용 하 여 PPK에서 OpenSSH 형식으로 변환 합니다. 

**예제 1: 프라이빗 키 filePath를 사용한 SshPublicKey 인증**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 2: 프라이빗 키 콘텐츠를 사용한 SshPublicKey 인증**

```json
{
    "name": "SftpLinkedService",
    "type": "Linkedservices",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<sftp server>",
            "port": 22,
            "skipHostKeyValidation": true,
            "authenticationType": "SshPublicKey",
            "userName": "<username>",
            "privateKeyContent": {
                "type": "SecureString",
                "value": "<base64 string of the private key content>"
            },
            "passPhrase": {
                "type": "SecureString",
                "value": "<pass phrase>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 집합을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md) 문서를 참조 하세요. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

형식 기반 데이터 집합의 설정에서 SFTP에 대해 지원 되는 속성은 다음과 `location` 같습니다.

| 속성   | Description                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합의 아래에 있는 *type* 속성은 `location` *SftpLocation*로 설정 해야 합니다. | 예      |
| folderPath | 폴더에 대 한 경로입니다. 와일드 카드를 사용 하 여 폴더를 필터링 하려면이 설정을 건너뛰고 활동 원본 설정에서 경로를 지정 합니다. | 예       |
| fileName   | 지정 된 folderPath의 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정을 건너뛰고 활동 원본 설정에서 파일 이름을 지정 합니다. | 예       |

**예:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "SftpLocation",
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

활동을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조 하세요. 이 섹션에서는 SFTP 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="sftp-as-source"></a>SFTP를 원본으로

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

다음은 `storeSettings` 형식 기반 복사 원본의 설정에서 SFTP에 대해 지원 되는 속성입니다.

| 속성                 | Description                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 아래의 *type* 속성은 `storeSettings` *Sftpreadsettings*로 설정 되어야 합니다. | 예                                           |
| ***복사할 파일 찾기*** |  |  |
| 옵션 1: 정적 경로<br> | 데이터 집합에 지정 된 폴더/파일 경로에서 복사 합니다. 폴더의 모든 파일을 복사하려면 `wildcardFileName`을 `*`로 지정합니다. |  |
| 옵션 2: 와일드카드<br>- wildcardFolderPath | 원본 폴더를 필터링할 와일드카드 문자가 포함된 폴더 경로입니다. <br>허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 (0 개 이상의 문자 `?` 일치)입니다. `^` 실제 폴더 이름에 와일드 카드 또는 내부 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다. <br>더 많은 예제는 [폴더 및 파일 필터 예](#folder-and-file-filter-examples)를 참조 하세요. | 예                                            |
| 옵션 2: 와일드카드<br>- wildcardFileName | 소스 파일을 필터링 하기 위해 지정 된 folderPath/wildcardFolderPath 아래의 와일드 카드 문자가 포함 된 파일 이름입니다. <br>허용 되는 와일드 카드는 `*` (0 개 이상의 문자를 포함) 및 (0 개 이상의 문자 `?` 일치)입니다. `^` 실제 폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다.  더 많은 예제는 [폴더 및 파일 필터 예](#folder-and-file-filter-examples)를 참조 하세요. | 예 |
| 옵션 3: 파일 목록<br>- fileListPath | 지정 된 파일 집합을 복사 함을 나타냅니다. 복사할 파일 목록이 포함 된 텍스트 파일을 가리킵니다 (데이터 집합에 구성 된 경로에 대 한 상대 경로를 사용 하 여 한 줄에 하나씩 파일).<br/>이 옵션을 사용 하는 경우 데이터 집합에서 파일 이름을 지정 하지 마세요. 더 많은 예제는 [파일 목록 예](#file-list-examples)를 참조 하세요. |예 |
| ***추가 설정*** |  | |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. recursive를 true로 설정하고 싱크가 파일 기반 저장소인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사되거나 만들어지지 않습니다. <br>허용되는 값은 *true*(기본값) 및 *false*입니다.<br>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. |예 |
| deleteFilesAfterCompletion | 대상 저장소로 이동한 후에 소스 저장소에서 이진 파일을 삭제할지 여부를 나타냅니다. 파일 삭제는 파일 단위 이므로 복사 작업에 실패 하면 일부 파일이 이미 대상에 복사 되 고 원본에서 삭제 된 것을 확인할 수 있습니다. 반면 다른 파일은 원본 저장소에 남아 있습니다. <br/>이 속성은 이진 복사 시나리오 에서만 유효 합니다. 여기에서 데이터 원본 저장소는 Blob, ADLS Gen1, ADLS Gen2, S3, Google 클라우드 저장소, 파일, Azure 파일, SFTP 또는 FTP입니다. 기본값은 false입니다. |예 |
| modifiedDatetimeStart    | 파일이 *마지막으로 수정*된 특성을 기준으로 필터링 됩니다. <br>마지막으로 수정한 시간이의 범위 내에 있는 경우 파일이 선택 됩니다 `modifiedDatetimeStart` `modifiedDatetimeEnd` . 시간은 *2018-12-01T05:00:00z*형식으로 UTC 표준 시간대에 적용 됩니다. <br> 속성은 NULL 일 수 있습니다. 즉, 데이터 집합에 파일 특성 필터가 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 되어 있음을 의미 합니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 되어 있음을 의미 합니다.<br/>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. | 예                                            |
| modifiedDatetimeEnd      | 위와 동일합니다.                                               | 예                                            |
| Enable파티션 검색 | 분할 된 파일의 경우 파일 경로에서 파티션을 구문 분석할 지 여부를 지정 하 고 추가 원본 열로 추가 합니다.<br/>허용 되는 값은 **false** (기본값) 및 **true**입니다. | 예                                            |
| 파티션 (partitionRootPath) | 파티션 검색을 사용 하는 경우 분할 된 폴더를 데이터 열로 읽도록 절대 루트 경로를 지정 합니다.<br/><br/>지정 되지 않은 경우 기본적으로<br/>-원본에 있는 파일 또는 데이터 집합의 파일 경로를 사용 하는 경우 파티션 루트 경로는 데이터 집합에서 구성 된 경로입니다.<br/>-와일드 카드 폴더 필터를 사용 하는 경우 파티션 루트 경로는 첫 번째 와일드 카드 앞의 하위 경로입니다.<br/><br/>예를 들어 데이터 집합의 경로를 "root/folder/year = 2020/month = 08/day = 27"로 구성 한다고 가정 합니다.<br/>-파티션 루트 경로를 "root/folder/year = 2020"으로 지정 하는 경우 복사 작업은 파일 내의 열 외에도 각각 두 개의 열을 생성 하 `month` 고 `day` 값을 "08" 및 "27"로 생성 합니다.<br/>-파티션 루트 경로를 지정 하지 않으면 추가 열이 생성 되지 않습니다. | 예                                            |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결할 수 있는 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만 값을 지정 합니다. | 예                                            |

**예:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
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
                    "type": "SftpReadSettings",
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

### <a name="sftp-as-a-sink"></a>SFTP를 싱크로

[!INCLUDE [data-factory-v2-file-sink-formats](../../includes/data-factory-v2-file-sink-formats.md)]

형식 기반 복사 싱크의 설정에서 SFTP에 대해 지원 되는 속성은 다음과 `storeSettings` 같습니다.

| 속성                 | Description                                                  | 필수 |
| ------------------------ | ------------------------------------------------------------ | -------- |
| type                     | 아래의 *type* 속성 `storeSettings` 을 *sftpwritesettings*로 설정 해야 합니다. | 예      |
| copyBehavior             | 원본이 파일 기반 데이터 저장소의 파일인 경우 복사 동작을 정의합니다.<br/><br/>허용된 값은<br/><b>- PreserveHierarchy(기본값)</b>: 대상 폴더에서 파일 계층 구조를 유지합니다. 원본 폴더의 원본 파일 상대 경로는 대상 폴더의 대상 파일 상대 경로와 동일합니다.<br/><b>- FlattenHierarchy</b>: 원본 폴더의 모든 파일이 대상 폴더의 첫 번째 수준에 있습니다. 대상 파일은 자동 생성된 이름을 갖습니다. <br/><b>- MergeFiles</b>: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일 이름이 지정된 경우 병합되는 파일 이름은 지정된 이름입니다. 그렇지 않으면 자동 생성되는 파일 이름이 적용됩니다. | 예       |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결할 수 있는 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만 값을 지정 합니다. | 예       |
| useTempFileRename | 임시 파일에 업로드 하 고 이름을 바꾸거나 대상 폴더 또는 파일 위치에 직접 쓸지 여부를 나타냅니다. 기본적으로 Azure Data Factory는 먼저 임시 파일에 쓴 다음 업로드가 완료 되 면 이름을 바꿉니다. 이 시퀀스는 (1) 다른 프로세스가 동일한 파일에 쓰는 경우 손상 된 파일을 발생 시킬 수 있는 충돌을 방지 하 고 (2) 전송 중에 파일의 원래 버전이 존재 하는지 확인 하는 데 도움이 됩니다. SFTP 서버에서 이름 바꾸기 작업을 지원 하지 않는 경우이 옵션을 사용 하지 않도록 설정 하 고 대상 파일에 동시 쓰기가 수행 되지 않도록 합니다. 자세한 내용은이 표 끝에 있는 문제 해결 팁을 참조 하십시오. | 아니요. 기본값은 *True*입니다. |
| operationTimeout | SFTP 서버에 대한 각각의 쓰기 요청이 시간 초과되기까지의 대기 시간입니다. 기본값은60분(01:00:00)입니다.|예 |

>[!TIP]
>SFTP에 데이터를 쓸 때 "UserErrorSftpPathNotFound", "UserErrorSftpPermissionDenied" 또는 "SftpOperationFail" 오류가 표시 되 고 사용 하는 SFTP 사용자 *에 게 적절* 한 권한이 있는 경우 sftp 서버 지원 파일 이름 바꾸기 작업이 작동 하는지 확인 합니다. 그렇지 않으면 임시 파일 () **을 사용 하 여 업로드** 를 사용 하지 않도록 설정 `useTempFileRename` 하 고 다시 시도 하세요. 이 속성에 대해 자세히 알아보려면 위의 표를 참조 하세요. 복사 작업에 자체 호스팅 통합 런타임을 사용 하는 경우 버전 4.6 이상을 사용 해야 합니다.

**예:**

```json
"activities":[
    {
        "name": "CopyToSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
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
                "type": "<source type>"
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings":{
                    "type": "SftpWriteSettings",
                    "copyBehavior": "PreserveHierarchy"
                }
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 폴더 경로 및 파일 이름에 와일드 카드 필터를 사용 하 여 발생 하는 동작에 대해 설명 합니다.

| folderPath | fileName | recursive | 원본 폴더 구조 및 필터 결과(**굵게** 표시된 파일이 검색됨)|
|:--- |:--- |:--- |:--- |
| `Folder*` | (비어 있음, 기본값 사용) | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | (비어 있음, 기본값 사용) | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | false | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*` | `*.csv` | true | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>파일 목록 예

다음 표에서는 복사 활동 원본에서 파일 목록 경로를 사용 하 여 발생 하는 동작을 설명 합니다. 원본 폴더 구조가 다음과 같이 굵게 표시 된 파일을 복사 하 려 한다고 가정 합니다.

| 샘플 원본 구조                                      | FileListToCopy.txt의 콘텐츠                             | Azure Data Factory 구성                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;메타데이터<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **데이터 집합에서 다음을 수행 합니다.**<br>- 폴더 경로: `root/FolderA`<br><br>**복사 작업 원본에서:**<br>- 파일 목록 경로: `root/Metadata/FileListToCopy.txt` <br><br>파일 목록 경로는 복사 하려는 파일 목록이 포함 된 동일한 데이터 저장소의 텍스트 파일을 가리킵니다 (데이터 집합에 구성 된 경로에 대 한 상대 경로를 사용 하 여 한 줄에 하나씩 파일 하나). |

## <a name="lookup-activity-properties"></a>조회 작업 속성

조회 작업 속성에 대 한 자세한 내용은 [Azure Data Factory에서 조회 작업](control-flow-lookup-activity.md)을 참조 하세요.

## <a name="getmetadata-activity-properties"></a>GetMetadata 작업 속성

GetMetadata 활동 속성에 대 한 자세한 내용은 [Azure Data Factory GetMetadata 활동](control-flow-get-metadata-activity.md)을 참조 하세요. 

## <a name="delete-activity-properties"></a>삭제 작업 속성

Delete 작업 속성에 대 한 자세한 내용은 [Azure Data Factory에서 삭제 작업](delete-activity.md)을 참조 하세요.

## <a name="legacy-models"></a>레거시 모델

>[!NOTE]
>다음 모델은 이전 버전과의 호환성을 위해 계속 지원 됩니다. Azure Data Factory 작성 UI가 새 모델을 생성 하도록 전환 되었으므로 앞에서 설명한 새 모델을 사용 하는 것이 좋습니다.

### <a name="legacy-dataset-model"></a>레거시 데이터 세트 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 *type* 속성은 파일 *공유*로 설정 해야 합니다. |예 |
| folderPath | 폴더에 대 한 경로입니다. 와일드 카드 필터를 사용할 수 있습니다. 허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 (0 개 이상의 문자 `?` 일치)입니다. `^` 실제 파일 이름에 와일드 카드 또는 내부 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다. <br/><br/>예: rootfolder/subfolder/(더 많은 예제는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples) 참조) |예 |
| fileName |  지정 된 "folderPath" 아래에 있는 파일에 대 한 **이름 또는 와일드 카드 필터** 입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>필터의 경우 허용 되는 와일드 카드는 `*` (0 개 이상의 문자를 검색)이 고 `?` (0 개 이상의 단일 문자와 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>실제 폴더 이름에 와일드카드 또는 이 이스케이프 문자가 있는 경우 `^`을 사용하여 이스케이프합니다. |예 |
| modifiedDatetimeStart | 파일이 *마지막으로 수정*된 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 시간이의 범위 내에 있는 경우 파일이 선택 됩니다 `modifiedDatetimeStart` `modifiedDatetimeEnd` . 시간은 *2018-12-01T05:00:00Z*형식의 UTC 표준 시간대에 적용 됩니다. <br/><br/> 많은 수의 파일에서 파일을 필터링 하려는 경우에는이 설정을 사용 하 여 데이터 이동의 전반적인 성능에 영향을 줍니다. <br/><br/> 속성은 NULL 일 수 있습니다. 즉, 데이터 집합에 파일 특성 필터가 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 되어 있음을 의미 합니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 되어 있음을 의미 합니다.| 예 |
| modifiedDatetimeEnd | 파일이 *마지막으로 수정*된 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 시간이의 범위 내에 있는 경우 파일이 선택 됩니다 `modifiedDatetimeStart` `modifiedDatetimeEnd` . 시간은 *2018-12-01T05:00:00Z*형식의 UTC 표준 시간대에 적용 됩니다. <br/><br/> 많은 수의 파일에서 파일을 필터링 하려는 경우에는이 설정을 사용 하 여 데이터 이동의 전반적인 성능에 영향을 줍니다. <br/><br/> 속성은 NULL 일 수 있습니다. 즉, 데이터 집합에 파일 특성 필터가 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 되어 있음을 의미 합니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 되어 있음을 의미 합니다.| 예 |
| format | 파일 기반 저장소(이진 복사) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 형식 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석 하려면 *TextFormat*, *JsonFormat*, *avroformat*, *orcformat*및 *ParquetFormat*와 같은 파일 형식 형식이 지원 됩니다. 이 값 중 하나로 서식에서 *type* 속성을 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [Json 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [Orc 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format)및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조 하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원되는 형식은 *GZip*, *Deflate*, *BZip2* 및 *ZipDeflate*입니다.<br/>*Optimal* 및 *Fastest* 수준이 지원됩니다. |예 |

>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 *folderPath*만을 지정합니다.<br>지정 된 이름의 단일 파일을 복사 하려면 폴더에 *folderPath* 를 지정 *하 고 파일 이름에 파일* 이름을 지정 합니다.<br>폴더 아래에 있는 파일의 하위 집합을 복사 하려면 와일드 카드 필터를 사용 하 여 폴더 파트 및 *파일 이름* 으로 *folderPath* 를 지정 합니다.

>[!NOTE]
>파일 필터에 *fileFilter* 속성을 사용 하는 경우이 속성은 여전히 그대로 지원 되지만 지금은 *파일 이름* 에 추가 된 새 필터 기능을 사용 하는 것이 좋습니다.

**예제:**

```json
{
    "name": "SFTPDataset",
    "type": "Datasets",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<SFTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
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
| type | 복사 작업 원본의 *type* 속성은 *FileSystemSource* 로 설정 해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. Recursive가 *true* 로 설정 되 고 싱크가 파일 기반 저장소 인 경우 빈 폴더와 하위 폴더는 싱크에 복사 되거나 생성 되지 않습니다.<br/>허용 되는 값은 *true* (기본값) 및 *false* 입니다. | 예 |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결할 수 있는 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만 숫자를 지정 합니다. | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyFromSFTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SFTP input dataset name>",
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
Azure Data Factory 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조 하세요.
