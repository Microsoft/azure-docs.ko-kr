---
title: Azure Data Factory ORC 형식
description: 이 항목에서는 Azure Data Factory에서 ORC 형식을 처리 하는 방법을 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: jingwang
ms.openlocfilehash: 5f83e9bbcdfffdd9b19786012295ff5643116551
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927377"
---
# <a name="orc-format-in-azure-data-factory"></a>Azure Data Factory ORC 형식

**ORC 파일을 구문 분석 하거나 데이터를 ORC 형식으로 기록**하려는 경우이 문서를 따릅니다. 

ORC 형식은 다음 커넥터에 대해 지원 됩니다. [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [SFTP](connector-sftp.md).

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 ORC 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 자산         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **Orc**로 설정 해야 합니다. | yes      |
| location         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 `location`에서 고유한 위치 유형 및 지원 되는 속성이 있습니다. **커넥터 문서-> 데이터 집합 속성 섹션에서 세부 정보를 참조 하세요**. | yes      |

다음은 Azure Blob Storage ORC 데이터 집합의 예입니다.

```json
{
    "name": "ORCDataset",
    "properties": {
        "type": "Orc",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            }
        }
    }
}
```

다음 사항에 유의하세요.

* 복합 데이터 형식(구조체, 매핑, 목록, 공용 구조체)은 지원되지 않습니다.
* 열 이름에는 공백이 지원되지 않습니다.
* ORC 파일에는 3개의 [압축 관련 옵션](https://hortonworks.com/blog/orcfile-in-hdp-2-better-compression-better-performance/)(NONE, ZLIB, SNAPPY)이 있습니다. Data Factory에서는 이러한 압축 형식으로 된 데이터를 ORC 파일에서 읽을 수 있습니다. 메타데이터에 있는 압축 코덱을 사용하여 데이터를 읽습니다. 그러나 ORC 파일에 쓸 때 Data Factory는 ORC에 대한 기본값인 ZLIB를 선택합니다. 현재 이 동작을 재정의할 수 있는 옵션은 없습니다.

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 ORC 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="orc-as-source"></a>원본으로 ORC

복사 작업 ***\*원본\**** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **Orcsource**로 설정 해야 합니다. | yes      |
| storeSettings | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings`에서 고유한 지원 읽기 설정이 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아닙니다.       |

### <a name="orc-as-sink"></a>ORC

복사 작업 ***\*싱크\**** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **Orcsink**로 설정 해야 합니다. | yes      |
| storeSettings | 데이터 저장소에 데이터를 쓰는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터의 `storeSettings`에는 자체 지원 되는 쓰기 설정이 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아닙니다.       |

## <a name="using-self-hosted-integration-runtime"></a>자체 호스팅 Integration Runtime 사용

> [!IMPORTANT]
> 자체 호스팅 Integration Runtime에 권한을 부여한 복사(예: 온-프레미스 및 클라우드 데이터 저장소 간)의 경우 ORC 파일을 **있는 그대로** 복사하지 않으면 IR 머신에 **64비트 JRE(Java Runtime Environment) 8 또는 OpenJDK**를 설치해야 합니다. 자세한 내용은 다음 단락을 참조하세요.

자체 호스팅 IR에서 ORC 파일 직렬화/역직렬화를 사용하여 실행되는 복사의 경우 ADF는 먼저 JRE에 대한 *`(SOFTWARE\JavaSoft\Java Runtime Environment\{Current Version}\JavaHome)`* 레지스트리를 검사하고, 없는 경우 OpenJDK에 대한 *`JAVA_HOME`* 시스템 변수를 검사하여 Java 런타임을 찾습니다.

- **JRE를 사용 하려면**: 64 비트 IR에 64 비트 JRE가 필요 합니다. [여기](https://go.microsoft.com/fwlink/?LinkId=808605)서 찾을 수 있습니다.
- **OpenJDK 사용**: IR 버전 3.13부터 지원됩니다. 다른 모든 필수 OpenJDK 어셈블리와 함께 jvm.dll을 자체 호스팅 IR 머신으로 패키지하고, 이에 따라 JAVA_HOME 시스템 환경 변수를 설정합니다.

> [!TIP]
> 자체 호스팅 Integration Runtime를 사용 하 여 데이터를 ORC 형식으로 복사 하 고 "java를 호출할 때 오류가 발생 했습니다. **OutOfMemoryError: java 힙 공간**"을 사용 하는 경우 자체 호스팅 IR을 호스트 하는 컴퓨터에 `_JAVA_OPTIONS` 환경 변수를 추가 하 여 JVM의 최소/최대 힙 크기를 조정 하 여 이러한 복사본을 강화 한 후 파이프라인을 다시 실행할 수 있습니다.

![자체 호스팅 IR에서 JVM 힙 크기 설정](./media/supported-file-formats-and-compression-codecs/set-jvm-heap-size-on-selfhosted-ir.png)

예: 변수 `_JAVA_OPTIONS`를 `-Xms256m -Xmx16g` 값으로 설정합니다. 플래그 `Xms`는 JVM(Java Virtual Machine)의 초기 메모리 할당 풀을 지정하고, `Xmx`는 최대 메모리 할당 풀을 지정합니다. 즉, JVM은 `Xms`의 메모리 양으로 시작하고 최대 `Xmx`의 메모리 양을 사용할 수 있음을 의미합니다. 기본적으로 ADF는 최소 64MB 및 최대 1G를 사용합니다.

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
