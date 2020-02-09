---
title: Azure Data Factory의 이진 형식
description: 이 항목에서는 Azure Data Factory에서 이진 형식을 처리 하는 방법에 대해 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: jingwang
ms.openlocfilehash: 8ebb4f0d1a06a7bf29dc46cd696b6acfd2527095
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927387"
---
# <a name="binary-format-in-azure-data-factory"></a>Azure Data Factory의 이진 형식

다음 커넥터에 대해 이진 형식이 지원 됩니다. [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [SFTP](connector-sftp.md).

[복사 작업](copy-activity-overview.md), [GetMetadata 작업](control-flow-get-metadata-activity.md)또는 [삭제 작업](delete-activity.md)에서 이진 데이터 집합을 사용할 수 있습니다. 이진 데이터 집합을 사용 하는 경우 ADF는 파일 콘텐츠를 구문 분석 하지 않고 그대로 처리 합니다. 

>[!NOTE]
>복사 작업에서 이진 데이터 집합을 사용 하는 경우 이진 데이터 집합에서 이진 데이터 집합 으로만 복사할 수 있습니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 이진 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 자산         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **Binary**로 설정 되어야 합니다. | yes      |
| location         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 `location`에서 고유한 위치 유형 및 지원 되는 속성이 있습니다. **커넥터 문서-> 데이터 집합 속성 섹션에서 세부 정보를 참조 하세요**. | yes      |
| 압축 | 파일 압축을 구성 하는 속성의 그룹입니다. 작업 실행 중 압축/압축 해제를 수행 하려는 경우이 섹션을 구성 합니다. | 아닙니다. |
| type | 이진 파일을 읽고 쓰는 데 사용 되는 압축 코덱입니다. <br>허용 되는 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**입니다. 를 사용 하 여 파일을 저장할 수 있습니다.<br>참고 복사 작업을 사용 하 여 ZipDeflate 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때 파일은 폴더로 추출 됩니다. `<path specified in dataset>/<folder named as source zip file>/`. | 아닙니다.       |
| level | 압축 비율입니다. 복사 작업 싱크에서 데이터 집합을 사용 하는 경우에 적용 됩니다.<br>허용 되는 값은 **최적** 또는 **가장 빠릅니다**.<br>- **가장 빠름:** 압축 작업은 결과 파일이 최적으로 압축 되지 않은 경우에도 최대한 빨리 완료 되어야 합니다.<br>- **최적**: 작업을 완료 하는 데 시간이 더 오래 걸리는 경우에도 압축 작업을 최적으로 압축 해야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | 아닙니다.       |

다음은 Azure Blob Storage에서 이진 데이터 집합의 예입니다.

```json
{
    "name": "BinaryDataset",
    "properties": {
        "type": "Binary",
        "linkedServiceName": {
            "referenceName": "<Azure Blob Storage linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "container": "containername",
                "folderPath": "folder/subfolder",
            },
            "compression": {
                "type": "ZipDeflate"
            }
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 이진 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

>[!NOTE]
>복사 작업에서 이진 데이터 집합을 사용 하는 경우 이진 데이터 집합에서 이진 데이터 집합 으로만 복사할 수 있습니다.

### <a name="binary-as-source"></a>소스로 이진

복사 작업 ***\*원본\**** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성을 **Binarysource**로 설정 해야 합니다. | yes      |
| storeSettings | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings`에서 고유한 지원 읽기 설정이 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아닙니다.       |

### <a name="binary-as-sink"></a>싱크로 이진 파일

복사 작업 ***\*싱크\**** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **Binarysink**로 설정 되어야 합니다. | yes      |
| storeSettings | 데이터 저장소에 데이터를 쓰는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터의 `storeSettings`에는 자체 지원 되는 쓰기 설정이 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아닙니다.       |

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
- [작업 삭제](delete-activity.md)
