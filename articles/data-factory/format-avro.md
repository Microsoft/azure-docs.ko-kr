---
title: Azure Data Factory의 Avro 형식
description: 이 항목에서는 Azure Data Factory에서 Avro 형식을 처리 하는 방법에 대해 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 9e962a0e76cdc0d51a87df3c33927c34db991fc7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927405"
---
# <a name="avro-format-in-azure-data-factory"></a>Azure Data Factory의 Avro 형식

**Avro 파일을 구문 분석 하거나 데이터를 avro 형식으로 기록**하려는 경우이 문서를 따릅니다. 

Avro 형식은 [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)및 [SFTP](connector-sftp.md)커넥터에 대해 지원 됩니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Avro 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 자산         | 설명                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **Avro**로 설정 되어야 합니다. | yes      |
| location         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 `location`에서 고유한 위치 유형 및 지원 되는 속성이 있습니다. **커넥터 문서-> 데이터 집합 속성 섹션에서 세부 정보를 참조 하세요**. | yes      |
| avroCompressionCodec | Avro 파일에 쓸 때 사용할 압축 코덱입니다. Avro 파일에서 읽을 때 파일 메타 데이터를 기반으로 압축 코덱을 자동으로 결정 Data Factory.<br>지원 되는 형식은 "**none**" (기본값), "**deflate**", "**snappy**"입니다. | 아닙니다.       |

> [!NOTE]
> Avro 파일에는 열 이름에 공백이 지원 되지 않습니다.

다음은 Azure Blob Storage의 Avro 데이터 집합 예입니다.

```json
{
    "name": "AvroDataset",
    "properties": {
        "type": "Avro",
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
            },
            "avroCompressionCodec": "snappy"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Avro 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="avro-as-source"></a>Avro를 원본으로

복사 작업 ***\*원본\**** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **Avrosource**로 설정 해야 합니다. | yes      |
| storeSettings | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings`에서 고유한 지원 읽기 설정이 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아닙니다.       |

### <a name="avro-as-sink"></a>Avro로 Avro

복사 작업 ***\*싱크\**** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 자산      | 설명                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **Avrosink**로 설정 해야 합니다. | yes      |
| storeSettings | 데이터 저장소에 데이터를 쓰는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터의 `storeSettings`에는 자체 지원 되는 쓰기 설정이 있습니다. **커넥터 문서-> 복사 작업 속성 섹션에서 세부 정보를 참조 하세요**. | 아닙니다.       |

## <a name="data-type-support"></a>데이터 형식 지원

Avro [복합 데이터 형식은](https://avro.apache.org/docs/current/spec.html#schema_complex) 지원 되지 않습니다 (레코드, 열거형, 배열, 맵, 공용 구조체 및 고정).

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
