---
title: Azure Data Factory를 사용 하 여 HDFS에서 데이터 복사
description: Azure Data Factory 파이프라인에서 복사 작업을 사용 하 여 클라우드 또는 온-프레미스 HDFS 원본에서 지원 되는 싱크 데이터 저장소로 데이터를 복사 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jingwang
ms.openlocfilehash: 562acfe1ae96f7f88b72945846bcb49c0cc1f216
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2020
ms.locfileid: "89179541"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 HDFS 서버에서 데이터 복사

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스의 버전을 선택 합니다."]
> * [버전 1](v1/data-factory-hdfs-connector.md)
> * [현재 버전](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 HDFS (Hadoop 분산 파일 시스템) 서버에서 데이터를 복사 하는 방법을 설명 합니다. Azure Data Factory에 대해 자세히 알아보려면 [소개 문서](introduction.md)를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

HDFS 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본 및 싱크 행렬이](copy-activity-overview.md) 포함 된 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

특히 HDFS 커넥터는 다음을 지원 합니다.

- *Windows* (Kerberos) 또는 *익명* 인증을 사용 하 여 파일을 복사 합니다.
- *Webhdfs* 프로토콜 또는 *기본 제공 distcp* 지원을 사용 하 여 파일을 복사 합니다.
- 파일을 있는 그대로 복사 하거나 [지원 되는 파일 형식 및 압축 코덱을](supported-file-formats-and-compression-codecs.md)사용 하 여 파일을 구문 분석 하거나 생성 합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> 통합 런타임에서 Hadoop 클러스터의 *모든* [이름 노드 서버]: [이름 노드 포트] 및 [데이터 노드 서버]: [데이터 노드 포트]에 액세스할 수 있는지 확인 합니다. 기본 [이름 노드 포트]는 50070이 고 기본값 [데이터 노드 포트]는 50075입니다.

## <a name="get-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 HDFS에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

HDFS 연결 된 서비스에 대해 지원 되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | *유형* 속성은 *Hdfs*로 설정 되어야 합니다. | 예 |
| url |HDFS에 대 한 URL입니다. |예 |
| authenticationType | 허용 되는 값은 *Anonymous* 또는 *Windows*입니다. <br><br> 온-프레미스 환경을 설정 하려면 [HDFS 커넥터에 Kerberos 인증 사용](#use-kerberos-authentication-for-the-hdfs-connector) 섹션을 참조 하세요. |예 |
| userName |Windows 인증에 대 한 사용자 이름입니다. Kerberos 인증의 경우 ** \<username> @ \<domain> .com**을 지정 합니다. |예 (Windows 인증의 경우) |
| password |Windows 인증에 대 한 암호입니다. 이 필드를 SecureString으로 표시 하 여 데이터 팩터리에 안전 하 게 저장 하거나 [Azure key vault에 저장 된 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예(Windows 인증에 대한) |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 자세히 알아보려면 [필수 조건](#prerequisites) 섹션을 참조 하세요. 통합 런타임이 지정 되지 않은 경우 서비스는 기본 Azure Integration Runtime를 사용 합니다. |예 |

**예제: 익명 인증 사용**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Windows 인증 사용**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
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

데이터 집합을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [Azure Data Factory의 데이터 집합](concepts-datasets-linked-services.md)을 참조 하세요. 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

다음은 `location` 형식 기반 데이터 집합의 설정에서 HDFS에 대해 지원 되는 속성입니다.

| 속성   | Description                                                  | 필수 |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | 데이터 집합의 아래에 있는 *type* 속성은 `location` *HdfsLocation*로 설정 해야 합니다. | 예      |
| folderPath | 폴더에 대 한 경로입니다. 와일드 카드를 사용 하 여 폴더를 필터링 하려면이 설정을 건너뛰고 활동 원본 설정에서 경로를 지정 합니다. | 예       |
| fileName   | 지정 된 folderPath의 파일 이름입니다. 와일드 카드를 사용 하 여 파일을 필터링 하려는 경우이 설정을 건너뛰고 활동 원본 설정에서 파일 이름을 지정 합니다. | 예       |

**예:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
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

활동을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [Azure Data Factory의 파이프라인 및 활동](concepts-pipelines-activities.md)을 참조 하세요. 이 섹션에서는 HDFS 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="hdfs-as-source"></a>원본으로 HDFS

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

다음은 `storeSettings` 형식 기반 복사 원본의 설정에서 HDFS에 대해 지원 되는 속성입니다.

| 속성                 | Description                                                  | 필수                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | 아래의 *type* 속성은 `storeSettings` **HdfsReadSettings**로 설정 해야 합니다. | 예                                           |
| ***복사할 파일 찾기*** |  |  |
| 옵션 1: 정적 경로<br> | 데이터 집합에 지정 된 폴더 또는 파일 경로에서 복사 합니다. 폴더의 모든 파일을 복사하려면 `wildcardFileName`을 `*`로 지정합니다. |  |
| 옵션 2: 와일드카드<br>- wildcardFolderPath | 원본 폴더를 필터링할 와일드카드 문자가 포함된 폴더 경로입니다. <br>허용되는 와일드카드는 `*`(문자 0자 이상 일치) 및 `?`(문자 0자 또는 1자 일치)입니다. `^`실제 폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다. <br>더 많은 예제는 [폴더 및 파일 필터 예](#folder-and-file-filter-examples)를 참조 하세요. | 예                                            |
| 옵션 2: 와일드카드<br>- wildcardFileName | 소스 파일을 필터링 하기 위해 지정 된 folderPath/wildcardFolderPath 아래의 와일드 카드 문자가 포함 된 파일 이름입니다. <br>허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 `?` (0 또는 1 개의 문자와 일치)입니다. `^` 실제 폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다.  더 많은 예제는 [폴더 및 파일 필터 예](#folder-and-file-filter-examples)를 참조 하세요. | 예 |
| 옵션 3: 파일 목록<br>- fileListPath | 지정 된 파일 집합을 복사 함을 나타냅니다. 복사할 파일 목록이 포함 된 텍스트 파일을 가리킵니다 (데이터 집합에 구성 된 경로에 대 한 상대 경로를 사용 하 여 한 줄에 하나씩 파일).<br/>이 옵션을 사용 하는 경우 데이터 집합에서 파일 이름을 지정 하지 마십시오. 더 많은 예제는 [파일 목록 예](#file-list-examples)를 참조 하세요. |예 |
| ***추가 설정*** |  | |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. `recursive`이 *true* 로 설정 되 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다. <br>허용되는 값은 *true*(기본값) 및 *false*입니다.<br>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. |예 |
| modifiedDatetimeStart    | 파일이 *마지막으로 수정*된 특성을 기준으로 필터링 됩니다. <br>마지막으로 수정한 시간이의 범위 내에 있는 경우 파일이 선택 됩니다 `modifiedDatetimeStart` `modifiedDatetimeEnd` . 시간은 *2018-12-01T05:00:00z*형식으로 UTC 표준 시간대에 적용 됩니다. <br> 속성은 NULL 일 수 있습니다. 즉, 데이터 집합에 파일 특성 필터가 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 되어 있음을 의미 합니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 되어 있음을 의미 합니다.<br/>`fileListPath`를 구성하는 경우에는 이 속성이 적용되지 않습니다. | 예                                            |
| Enable파티션 검색 | 분할 된 파일의 경우 파일 경로에서 파티션을 구문 분석할 지 여부를 지정 하 고 추가 원본 열로 추가 합니다.<br/>허용 되는 값은 **false** (기본값) 및 **true**입니다. | 예                                            |
| 파티션 (partitionRootPath) | 파티션 검색을 사용 하는 경우 분할 된 폴더를 데이터 열로 읽도록 절대 루트 경로를 지정 합니다.<br/><br/>지정 되지 않은 경우 기본적으로<br/>-원본에 있는 파일 또는 데이터 집합의 파일 경로를 사용 하는 경우 파티션 루트 경로는 데이터 집합에서 구성 된 경로입니다.<br/>-와일드 카드 폴더 필터를 사용 하는 경우 파티션 루트 경로는 첫 번째 와일드 카드 앞의 하위 경로입니다.<br/><br/>예를 들어 데이터 집합의 경로를 "root/folder/year = 2020/month = 08/day = 27"로 구성 한다고 가정 합니다.<br/>-파티션 루트 경로를 "root/folder/year = 2020"으로 지정 하는 경우 복사 작업은 파일 내의 열 외에도 각각 두 개의 열을 생성 하 `month` 고 `day` 값을 "08" 및 "27"로 생성 합니다.<br/>-파티션 루트 경로를 지정 하지 않으면 추가 열이 생성 되지 않습니다. | 예                                            |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결할 수 있는 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만 값을 지정 합니다. | 예                                            |
| ***DistCp 설정*** |  | |
| distcpSettings | HDFS DistCp를 사용할 때 사용할 속성 그룹입니다. | 예 |
| resourceManagerEndpoint | YARN (또 다른 리소스 Negotiator 끝점 | 예, DistCp를 사용 하는 경우 |
| tempScriptPath | 임시 DistCp 명령 스크립트를 저장 하는 데 사용 되는 폴더 경로입니다. 스크립트 파일은 Data Factory에서 생성 되며 복사 작업이 완료 된 후 제거 됩니다. | 예, DistCp를 사용 하는 경우 |
| distcpOptions | DistCp 명령에 제공된 추가 옵션입니다. | 예 |

**예:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
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
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
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

### <a name="folder-and-file-filter-examples"></a>폴더 및 파일 필터 예제

이 섹션에서는 폴더 경로 및 파일 이름을 포함 하는 와일드 카드 필터를 사용 하는 경우의 결과 동작을 설명 합니다.

| folderPath | fileName             | recursive | 원본 폴더 구조 및 필터 결과(**굵게** 표시된 파일이 검색됨) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (비어 있음, 기본값 사용) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | (비어 있음, 기본값 사용) | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5.csv<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>AnotherFolderB<br/>&nbsp;&nbsp;&nbsp;&nbsp;File6.csv |

### <a name="file-list-examples"></a>파일 목록 예

이 섹션에서는 복사 작업 원본에서 파일 목록 경로를 사용 하 여 발생 하는 동작에 대해 설명 합니다. 원본 폴더 구조가 다음과 같이 굵게 표시 된 파일을 복사 하 려 한다고 가정 합니다.

| 샘플 원본 구조                                      | FileListToCopy.txt의 콘텐츠                             | Azure Data Factory 구성                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Subfolder1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**File5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;메타데이터<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | File1.csv<br>Subfolder1/File3.csv<br>Subfolder1/File5.csv | **데이터 집합에서 다음을 수행 합니다.**<br>- 폴더 경로: `root/FolderA`<br><br>**복사 작업 원본에서:**<br>- 파일 목록 경로: `root/Metadata/FileListToCopy.txt` <br><br>파일 목록 경로는 복사 하려는 파일 목록이 포함 된 동일한 데이터 저장소의 텍스트 파일을 가리킵니다 (데이터 집합에 구성 된 경로에 대 한 상대 경로를 사용 하 여 한 줄에 하나씩 파일 하나). |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>DistCp를 사용하여 HDFS에서 데이터 복사

[Distcp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) 는 hadoop 클러스터에서 분산 된 복사본을 수행 하기 위한 hadoop 기본 명령줄 도구입니다. DistCp에서 명령을 실행 하면 먼저 복사 될 모든 파일을 나열 하 고 Hadoop 클러스터에서 여러 Map 작업을 만듭니다. 각 맵 작업은 원본에서 싱크로 이진 복사를 수행 합니다.

복사 작업은 DistCp를 사용 하 여 Azure Blob storage ( [준비 된 복사](copy-activity-performance.md)포함) 또는 azure data lake store에 있는 그대로 파일을 복사 하도록 지원 합니다. 이 경우 DistCp는 자체 호스팅 통합 런타임에서 실행 되는 대신 클러스터의 기능을 활용할 수 있습니다. DistCp를 사용 하면 특히 클러스터가 매우 강력한 경우 더 나은 복사 처리량이 제공 됩니다. 데이터 팩터리의 구성에 따라 복사 작업은 자동으로 DistCp 명령을 생성 하 고,이를 Hadoop 클러스터에 제출 하 고, 복사 상태를 모니터링 합니다.

### <a name="prerequisites"></a>필수 구성 요소

DistCp를 사용 하 여 HDFS에서 Azure Blob storage (준비 된 복사 포함) 또는 Azure data lake store로 파일을 복사 하려면 Hadoop 클러스터가 다음 요구 사항을 충족 하는지 확인 합니다.

* MapReduce 및 YARN 서비스를 사용할 수 있습니다.  
* YARN 버전은 2.5 이상입니다.  
* HDFS 서버는 **Azure Blob storage** 또는 **Azure Data Lake Store (ADLS Gen1)** 의 대상 데이터 저장소와 통합 됩니다. 

    - Azure Blob 파일 시스템은 Hadoop 2.7부터 기본적으로 지원됩니다. Hadoop 환경 구성에서 JAR 경로를 지정 해야 합니다.
    - Azure Data Lake Store 파일 시스템은 Hadoop 3.0.0-alpha1부터 패키지됩니다. Hadoop 클러스터 버전이 해당 버전 보다 이전 버전인 경우 [여기](https://hadoop.apache.org/releases.html)에서 클러스터로 AZURE DATA LAKE STORE 관련 JAR 패키지 (azure-datalake-store)를 수동으로 가져오고 Hadoop 환경 구성에서 JAR 파일 경로를 지정 해야 합니다.

* HDFS에서 임시 폴더를 준비합니다. 이 임시 폴더는 DistCp shell 스크립트를 저장 하는 데 사용 되므로 KB 수준 공간을 차지 합니다.
* HDFS 연결 된 서비스에 제공 된 사용자 계정에 다음 권한이 있는지 확인 합니다.
   * YARN에서 응용 프로그램을 제출 합니다.
   * Temp 폴더 아래에 하위 폴더와 읽기/쓰기 파일을 만듭니다.

### <a name="configurations"></a>구성

DistCp 관련 구성 및 예제는 [HDFS as 원본](#hdfs-as-source) 섹션으로 이동 합니다.

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>HDFS 커넥터에 Kerberos 인증 사용

HDFS 커넥터에 Kerberos 인증을 사용 하도록 온-프레미스 환경을 설정 하는 두 가지 옵션이 있습니다. 사용자의 상황에 적합 한 항목을 선택할 수 있습니다.
* 옵션 1: [Kerberos 영역에 자체 호스팅 integration runtime 컴퓨터 가입](#kerberos-join-realm)
* 옵션 2: [Windows 도메인과 Kerberos 영역 간의 상호 신뢰 사용](#kerberos-mutual-trust)

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>옵션 1: Kerberos 영역에 자체 호스팅 integration runtime 컴퓨터 가입

#### <a name="requirements"></a>요구 사항

* 자체 호스팅 통합 런타임 컴퓨터는 Kerberos 영역에 가입 해야 하 고 Windows 도메인에 가입할 수 없습니다.

#### <a name="how-to-configure"></a>구성 방법

**자체 호스팅 통합 런타임 컴퓨터에서 다음을 수행 합니다.**

1.  Ksetup 유틸리티를 실행하여 KDC(Kerberos 키 배포 센터) 서버 및 영역을 구성합니다.

    Kerberos 영역은 Windows 도메인과 다르므로 컴퓨터를 작업 그룹의 구성원으로 구성 해야 합니다. 다음 명령을 실행 하 여 Kerberos 영역을 설정 하 고 KDC 서버를 추가 하 여이 구성을 달성할 수 있습니다. *REALM.COM* 을 자신의 영역 이름으로 바꿉니다.

    ```console
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    이러한 명령을 실행 한 후 컴퓨터를 다시 시작 합니다.

2.  명령을 사용 하 여 구성을 확인 `Ksetup` 합니다. 출력은 다음과 같아야 합니다.

    ```output
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**데이터 팩터리에:**

* HDFS 데이터 원본에 연결 하기 위해 Kerberos 사용자 이름 및 암호와 함께 Windows 인증을 사용 하 여 HDFS 커넥터를 구성 합니다. 구성 세부 정보는 [HDFS 연결 된 서비스 속성](#linked-service-properties) 섹션을 참조 하세요.

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>옵션 2: Windows 도메인과 Kerberos 영역 간의 상호 신뢰 사용

#### <a name="requirements"></a>요구 사항

*   자체 호스팅 통합 런타임 컴퓨터는 Windows 도메인에 가입 해야 합니다.
*   도메인 컨트롤러의 설정을 업데이트할 수 있는 권한이 필요합니다.

#### <a name="how-to-configure"></a>구성 방법

> [!NOTE]
> 다음 자습서에서 REALM.COM 및 AD.COM를 사용자 고유의 영역 이름 및 도메인 컨트롤러로 바꿉니다.

**KDC 서버에서 다음을 수행합니다.**

1. *Krb5.conf* 파일에서 kdc 구성을 편집 하 여 다음 구성 템플릿을 참조 하 여 Kdc가 Windows 도메인을 신뢰 하도록 합니다. 기본적으로 이 구성은 */etc/krb5.conf*에 있습니다.

   ```config
   [logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log
            
   [libdefaults]
    default_realm = REALM.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h
    renew_lifetime = 7d
    forwardable = true
            
   [realms]
    REALM.COM = {
     kdc = node.REALM.COM
     admin_server = node.REALM.COM
    }
   AD.COM = {
    kdc = windc.ad.com
    admin_server = windc.ad.com
   }
            
   [domain_realm]
    .REALM.COM = REALM.COM
    REALM.COM = REALM.COM
    .ad.com = AD.COM
    ad.com = AD.COM
            
   [capaths]
    AD.COM = {
     REALM.COM = .
    }
    ```

   파일을 구성한 후 KDC 서비스를 다시 시작 합니다.

2. 다음 명령을 사용 하 여 KDC 서버에서 *krbtgt/ \@ AD.COM* 라는 보안 주체를 준비 합니다.

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. *hadoop.security.auth_to_local* HDFS 서비스 구성 파일에 `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//`를 추가합니다.

**도메인 컨트롤러에서 다음을 수행합니다.**

1.  다음 명령을 실행 `Ksetup` 하 여 영역 항목을 추가 합니다.

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Windows 도메인과 Kerberos 영역 간의 신뢰를 설정합니다. [password]는 보안 주체 *krbtgt/REALM.COM\@AD.COM*의 암호입니다.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Kerberos에서 사용 되는 암호화 알고리즘을 선택 합니다.

    a. **서버 관리자**  >  **그룹 정책 관리**  >  **도메인**  >  **그룹 정책 개체**  >  **기본 또는 활성 도메인 정책**을 선택한 다음 **편집**을 선택 합니다.

    b. **그룹 정책 관리 편집기** 창에서 **컴퓨터 구성**  >  **정책**  >  **Windows 설정**  >  **보안 설정**  >  **로컬 정책**  >  **보안 옵션**을 선택 하 고 **네트워크 보안 구성: Kerberos에 허용 된 암호화 유형 구성**을 선택 합니다.

    c. KDC 서버에 연결할 때 사용할 암호화 알고리즘을 선택 합니다. 모든 옵션을 선택할 수 있습니다.

    !["네트워크 보안: Kerberos에 허용 된 암호화 유형 구성" 창 스크린샷](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. 명령을 사용 `Ksetup` 하 여 지정 된 영역에서 사용할 암호화 알고리즘을 지정 합니다.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Windows 도메인에서 Kerberos 주체를 사용할 수 있도록 도메인 계정과 Kerberos 주체 간의 매핑을 만듭니다.

    a. **Administrative tools**  >  **사용자 및 컴퓨터 Active Directory**관리 도구를 선택 합니다.

    b. **보기** > **고급 기능**을 선택하여 고급 기능을 구성합니다.

    c. **고급 기능** 창에서 매핑을 만들 계정을 마우스 오른쪽 단추로 클릭 하 고 **이름 매핑** 창에서 **Kerberos 이름** 탭을 선택 합니다.

    d. 영역에서 보안 주체를 추가합니다.

       !["보안 Id 매핑" 창](media/connector-hdfs/map-security-identity.png)

**자체 호스팅 통합 런타임 컴퓨터에서 다음을 수행 합니다.**

* 다음 명령을 실행 `Ksetup` 하 여 영역 항목을 추가 합니다.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**데이터 팩터리에:**

* 도메인 계정 또는 Kerberos 보안 주체와 함께 Windows 인증을 사용 하 여 HDFS 커넥터를 구성 하 여 HDFS 데이터 원본에 연결 합니다. 구성 세부 정보는 [HDFS 연결 된 서비스 속성](#linked-service-properties) 섹션을 참조 하세요.

## <a name="lookup-activity-properties"></a>조회 작업 속성

조회 작업 속성에 대 한 자세한 내용은 [Azure Data Factory에서 조회 작업](control-flow-lookup-activity.md)을 참조 하세요.

## <a name="legacy-models"></a>레거시 모델

>[!NOTE]
>다음 모델은 이전 버전과의 호환성을 위해 계속 지원 됩니다. Azure Data Factory 작성 UI가 새 모델을 생성 하도록 전환 되었으므로 앞에서 설명한 새 모델을 사용 하는 것이 좋습니다.

### <a name="legacy-dataset-model"></a>레거시 데이터 세트 모델

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 *type* 속성은 파일 *공유* 로 설정 해야 합니다. |예 |
| folderPath | 폴더에 대 한 경로입니다. 와일드 카드 필터를 사용할 수 있습니다. 허용 되는 와일드 카드는 `*` (0 개 이상의 문자와 일치) 및 (0 개 이상의 문자 `?` 일치)입니다. `^` 실제 파일 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다. <br/><br/>예: rootfolder/subfolder/(더 많은 예제는 [폴더 및 파일 필터 예제](#folder-and-file-filter-examples) 참조) |예 |
| fileName |  지정 된 "folderPath" 아래에 있는 파일에 대 한 이름 또는 와일드 카드 필터입니다. 이 속성의 값을 지정하지 않으면 데이터 세트는 폴더에 있는 모든 파일을 가리킵니다. <br/><br/>필터의 경우 허용 되는 와일드 카드는 `*` (0 개 이상의 문자를 검색)이 고 `?` (0 개 이상의 단일 문자와 일치)입니다.<br/>- 예 1: `"fileName": "*.csv"`<br/>- 예 2: `"fileName": "???20180427.txt"`<br/>`^`실제 폴더 이름에 와일드 카드나이 이스케이프 문자가 있으면를 사용 하 여를 이스케이프 합니다. |예 |
| modifiedDatetimeStart | 파일이 *마지막으로 수정*된 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 시간이의 범위 내에 있는 경우 파일이 선택 됩니다 `modifiedDatetimeStart` `modifiedDatetimeEnd` . 시간은 *2018-12-01T05:00:00Z*형식으로 UTC 표준 시간대에 적용 됩니다. <br/><br/> 많은 수의 파일에 파일 필터를 적용 하려는 경우이 설정을 사용 하 여 데이터 이동의 전반적인 성능에 영향을 줄 수 있습니다. <br/><br/> 속성은 NULL 일 수 있습니다. 즉, 데이터 집합에 파일 특성 필터가 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 되어 있음을 의미 합니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 되어 있음을 의미 합니다.| 예 |
| modifiedDatetimeEnd | 파일이 *마지막으로 수정*된 특성을 기준으로 필터링 됩니다. 마지막으로 수정한 시간이의 범위 내에 있는 경우 파일이 선택 됩니다 `modifiedDatetimeStart` `modifiedDatetimeEnd` . 시간은 *2018-12-01T05:00:00Z*형식으로 UTC 표준 시간대에 적용 됩니다. <br/><br/> 많은 수의 파일에 파일 필터를 적용 하려는 경우이 설정을 사용 하 여 데이터 이동의 전반적인 성능에 영향을 줄 수 있습니다. <br/><br/> 속성은 NULL 일 수 있습니다. 즉, 데이터 집합에 파일 특성 필터가 적용 되지 않습니다.  `modifiedDatetimeStart`에 datetime 값이 있지만 `modifiedDatetimeEnd` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 크거나 같은 파일이 선택 되어 있음을 의미 합니다.  `modifiedDatetimeEnd`에 datetime 값이 있지만 `modifiedDatetimeStart` 가 NULL 인 경우 마지막으로 수정 된 특성이 datetime 값 보다 작은 파일이 선택 되어 있음을 의미 합니다.| 예 |
| format | 파일 기반 저장소(이진 복사본) 간에 파일을 있는 그대로 복사하려는 경우 입력 및 출력 데이터 세트 정의 둘 다에서 format 섹션을 건너뜁니다.<br/><br/>특정 형식의 파일을 구문 분석하려는 경우, 지원되는 파일 형식 유형은 *TextFormat*, *JsonFormat*, *AvroFormat*, *OrcFormat*, *ParquetFormat*입니다. 이 값 중 하나로 서식에서 *type* 속성을 설정합니다. 자세한 내용은 [텍스트 형식](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON 형식](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro 형식](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ORC 형식](supported-file-formats-and-compression-codecs-legacy.md#orc-format) 및 [Parquet 형식](supported-file-formats-and-compression-codecs-legacy.md#parquet-format) 섹션을 참조하세요. |아니요(이진 복사 시나리오에만 해당) |
| 압축 | 데이터에 대한 압축 유형 및 수준을 지정합니다. 자세한 내용은 [지원되는 파일 형식 및 압축 코덱](supported-file-formats-and-compression-codecs-legacy.md#compression-support)을 참조하세요.<br/>지원 되는 형식은 *Gzip*, *Deflate*, *Bzip2*및 *ZipDeflate*입니다.<br/>지원되는 수준은 *최적* 및 *가장 빠름*입니다. |예 |

>[!TIP]
>폴더 아래에서 모든 파일을 복사하려면 **folderPath**만을 지정합니다.<br>지정 된 이름의 단일 파일을 복사 하려면 폴더에 **folderPath** **를 지정 하 고 파일** 이름에 파일 이름을 지정 합니다.<br>폴더 아래에서 파일의 하위 집합을 복사하려면 폴더 부분으로 **folderPath** 및 와일드카드 필터로 **fileName**을 지정합니다.

**예:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
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
| type | 복사 작업 원본의 *type* 속성은 *HdfsSource*로 설정 해야 합니다. |예 |
| recursive | 하위 폴더 또는 지정된 폴더에서만 데이터를 재귀적으로 읽을지 여부를 나타냅니다. Recursive를 *true* 로 설정 하 고 싱크가 파일 기반 저장소 인 경우 빈 폴더 또는 하위 폴더가 싱크에 복사 되거나 생성 되지 않습니다.<br/>허용되는 값은 *true*(기본값) 및 *false*입니다. | 예 |
| distcpSettings | HDFS DistCp를 사용 하는 경우 속성 그룹입니다. | 예 |
| resourceManagerEndpoint | YARN 리소스 관리자 끝점 | 예, DistCp를 사용 하는 경우 |
| tempScriptPath | 임시 DistCp 명령 스크립트를 저장 하는 데 사용 되는 폴더 경로입니다. 스크립트 파일은 Data Factory에서 생성 되며 복사 작업이 완료 된 후 제거 됩니다. | 예, DistCp를 사용 하는 경우 |
| distcpOptions | DistCp 명령에 대 한 추가 옵션이 제공 됩니다. | 예 |
| maxConcurrentConnections | 저장소 저장소에 동시에 연결할 수 있는 연결 수입니다. 데이터 저장소에 대 한 동시 연결 수를 제한 하려는 경우에만 값을 지정 합니다. | 예 |

**예: DistCp를 사용 하 여 복사 작업의 HDFS 원본**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조 하세요.
