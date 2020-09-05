---
title: Azure Data Factory의 Excel 형식
description: 이 항목에서는 Azure Data Factory에서 Excel 형식을 처리 하는 방법을 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: jingwang
ms.openlocfilehash: dd5e116f0c6844abeffc27820da03462c6e1cbbc
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718206"
---
# <a name="excel-format-in-azure-data-factory"></a>Azure Data Factory의 Excel 형식
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

**Excel 파일을 구문 분석**하려는 경우이 문서를 따릅니다. Azure Data Factory ".xls" 및 ".xlsx"를 모두 지원 합니다.

Excel 형식은 [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)및 [SFTP](connector-sftp.md)커넥터에 대해 지원 됩니다. 원본으로 지원 되며 싱크로는 지원 되지 않습니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Excel 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 속성         | Description                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **Excel**로 설정 되어야 합니다.   | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는의 고유한 위치 유형 및 지원 되는 속성이 있습니다 `location` . | 예      |
| sheetName        | 데이터를 읽을 Excel 워크시트 이름입니다.                       | 예      |
| range            | 선택적 데이터를 찾기 위한 지정 된 워크시트의 셀 범위 (예:<br>-지정 하지 않음: 비어 있지 않은 첫 번째 행과 열의 테이블로 전체 워크시트를 읽습니다.<br>- `A3`: 지정 된 셀에서 시작 하 여 테이블을 읽고 아래의 모든 행과 오른쪽에 있는 모든 열을 동적으로 검색 합니다.<br>- `A3:H5`:이 고정 범위를 테이블로 읽습니다.<br>- `A3:A3`:이 단일 셀을 읽습니다. | 아니요       |
| firstRowAsHeader | 지정 된 워크시트/범위의 첫 번째 행을 열 이름의 머리글 줄로 처리할지 여부를 지정 합니다.<br>허용 되는 값은 **true** 및 **false** (기본값)입니다. | 아니요       |
| nullValue        | Null 값의 문자열 표현을 지정 합니다. <br>기본값은 **빈 문자열**입니다. | 아니요       |
| 압축 | 파일 압축을 구성 하는 속성의 그룹입니다. 작업 실행 중 압축/압축 해제를 수행 하려는 경우이 섹션을 구성 합니다. | 예 |
| type<br/>(*아래 `compression` *) | JSON 파일을 읽고 쓰는 데 사용 되는 압축 코덱입니다. <br>허용 되는 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**또는 **lz4**입니다. 를 사용 하 여 파일을 저장할 수 있습니다. 기본값은 압축 되지 않습니다.<br>**참고** 현재 복사 작업은 "snappy" & "lz4"을 지원 하지 않으며 매핑 데이터 흐름은 "ZipDeflate"를 지원 하지 않습니다.<br>**참고** 복사 작업을 사용 하 여 **ZipDeflate** 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때 파일은 폴더에 추출 됩니다 `<path specified in dataset>/<folder named as source zip file>/` . | 아니요.  |
| 수준<br/>(*아래 `compression` *) | 압축 비율입니다. <br>허용 되는 값은 **최적** 또는 **가장 빠릅니다**.<br>- **가장 빠름:** 압축 작업은 결과 파일이 최적으로 압축 되지 않은 경우에도 최대한 빨리 완료 되어야 합니다.<br>- **최적**: 작업을 완료 하는 데 시간이 더 오래 걸리는 경우에도 압축 작업을 최적으로 압축 해야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | 아니요       |

다음은 Azure Blob Storage에 대 한 Excel 데이터 집합의 예입니다.

```json
{
    "name": "ExcelDataset",
    "properties": {
        "type": "Excel",
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
            "sheetName": "MyWorksheet",
            "range": "A3:H5",
            "firstRowAsHeader": true
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Excel 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="excel-as-source"></a>원본으로 Excel 

복사 작업 *** \* 원본 \* *** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | 복사 작업 원본의 type 속성은 **S\source**로 설정 해야 합니다. | 예      |
| 나이 설정 | 데이터 저장소에서 데이터를 읽는 방법에 대 한 속성 그룹입니다. 각 파일 기반 커넥터에는의 고유한 지원 읽기 설정이 `storeSettings` 있습니다. | 아니요       |

```json
"activities": [
    {
        "name": "CopyFromExcel",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "ExcelSource",
                "storeSettings": {
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            ...
        }
        ...
    }
]
```

## <a name="mapping-data-flow-properties"></a>매핑 데이터 흐름 속성

데이터 흐름 매핑에서는 [Azure Blob Storage](connector-azure-blob-storage.md#mapping-data-flow-properties), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties)및 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties)데이터 저장소에서 Excel 형식을 읽을 수 있습니다. Excel 데이터 집합을 사용 하거나 [인라인 데이터 집합](data-flow-source.md#inline-datasets)을 사용 하 여 excel 파일을 가리킬 수 있습니다.

### <a name="source-properties"></a>원본 속성

다음 표에서는 Excel 원본에서 지 원하는 속성을 나열 합니다. 이러한 속성은 **원본 옵션** 탭에서 편집할 수 있습니다. 인라인 데이터 집합을 사용 하는 경우 [데이터 집합 속성](#dataset-properties) 섹션에 설명 된 속성과 동일한 추가 파일 설정이 표시 됩니다.

| 이름                      | Description                                                  | 필수 | 허용되는 값                                            | 데이터 흐름 스크립트 속성         |
| ------------------------- | ------------------------------------------------------------ | -------- | --------------------------------------------------------- | --------------------------------- |
| 와일드 카드 경로           | 와일드 카드 경로와 일치 하는 모든 파일이 처리 됩니다. 데이터 집합에 설정 된 폴더 및 파일 경로를 재정의 합니다. | no       | String[]                                                  | wildcardPaths                     |
| 파티션 루트 경로       | 분할 된 파일 데이터의 경우 분할 된 폴더를 열로 읽기 위해 파티션 루트 경로를 입력할 수 있습니다. | 아니요       | String                                                    | 파티션 (partitionRootPath)                 |
| 파일 목록             | 소스에서 처리할 파일을 나열 하는 텍스트 파일을 가리키고 있는지 여부 | no       | `true` 또는 `false`                                         | fileList                          |
| 파일 이름을 저장할 열 | 원본 파일 이름 및 경로를 사용 하 여 새 열을 만듭니다.       | no       | String                                                    | rowUrlColumn                      |
| 완료 후          | 처리 후 파일을 삭제 하거나 이동 합니다. 컨테이너 루트에서 파일 경로가 시작 됩니다. | 아니요       | 삭제: `true` 또는 `false` <br> 옮기고 `['<from>', '<to>']` | purgeFiles <br> moveFiles         |
| 마지막으로 수정한 사람 필터링   | 마지막으로 변경 된 시간에 따라 파일을 필터링 하도록 선택 | no       | 타임스탬프                                                 | modifiedAfter <br> modifiedBefore |

### <a name="source-example"></a>원본 예제

아래 그림은 데이터 집합 모드를 사용 하 여 데이터 흐름을 매핑하는 Excel 원본 구성의 예입니다.

![Excel 원본](media/data-flow/excel-source.png)

연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    wildcardPaths:['*.xls']) ~> ExcelSource
```

인라인 데이터 집합을 사용 하는 경우 데이터 흐름 매핑에 다음 원본 옵션이 표시 됩니다.

![Excel 원본 인라인 데이터 집합](media/data-flow/excel-source-inline-dataset.png)

연결 된 데이터 흐름 스크립트는 다음과 같습니다.

```
source(allowSchemaDrift: true,
    validateSchema: false,
    format: 'excel',
    fileSystem: 'container',
    folderPath: 'path',
    fileName: 'sample.xls',
    sheetName: 'worksheet',
    firstRowAsHeader: true) ~> ExcelSourceInlineDataset
```

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
