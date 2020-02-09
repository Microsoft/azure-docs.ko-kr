---
title: Azure Data Factory에서 분리 된 텍스트 형식
description: 이 항목에서는 Azure Data Factory에서 구분 된 텍스트 형식을 처리 하는 방법에 대해 설명 합니다.
author: linda33wj
manager: shwang
ms.reviewer: craigg
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: jingwang
ms.openlocfilehash: f2e70a7b900ad918cda05ce34204e2de1e6e67ef
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830193"
---
# <a name="delimited-text-format-in-azure-data-factory"></a>Azure Data Factory에서 분리 된 텍스트 형식

**구분 된 텍스트 파일을 구문 분석 하거나 데이터를 구분 된 텍스트 형식으로 쓰려면**이 문서를 따르세요. 

구분 기호로 분리 된 텍스트 형식은 [Amazon S3](connector-amazon-simple-storage-service.md), [azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [파일 시스템](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md)및 [SFTP](connector-sftp.md)커넥터에 대해 지원 됩니다.

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 구분 기호로 분리 된 텍스트 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

| 속성         | Description                                                  | 필수 |
| ---------------- | ------------------------------------------------------------ | -------- |
| type             | 데이터 집합의 type 속성은 **DelimitedText**로 설정 해야 합니다. | 예      |
| 위치         | 파일의 위치 설정입니다. 각 파일 기반 커넥터에는 `location`에서 고유한 위치 유형 및 지원 되는 속성이 있습니다.  | 예      |
| columnDelimiter  | 파일에서 열을 구분 하는 데 사용 되는 문자입니다. 현재 다중 문자 구분 기호는 데이터 흐름을 매핑하는 경우에만 지원 되 고 복사 작업은 지원 되지 않습니다. <br>기본값은 **쉼표 `,`** 입니다. 열 구분 기호가 빈 문자열 (구분 기호 없음)로 정의 된 경우 전체 줄은 단일 열로 사용 됩니다. | 아닙니다.       |
| rowDelimiter     | 파일에서 행을 구분 하는 데 사용 되는 단일 문자 또는 "\r\n"입니다.<br>기본값은 **읽기의 경우 ["\r\n", "\r", "\n"]** , **"\n" 또는 "\r\n"** 의 값 중 하나는 각각 데이터 흐름 및 복사 작업을 매핑하는 것입니다. <br>`rowDelimiter` 구분 기호 없음 (빈 문자열)으로 설정 된 경우에는 `columnDelimiter`를 구분 기호 (빈 문자열)로 설정 하지 않아야 합니다 .이는 전체 콘텐츠를 단일 값으로 처리 하는 것을 의미 합니다. | 아닙니다.       |
| quoteChar        | 열 구분 기호를 포함 하는 경우 열 값을 따옴표로 묶을 단일 문자입니다. <br>기본값은 `"`**큰따옴표** 입니다. <br>매핑 데이터 흐름의 경우 `quoteChar`는 빈 문자열일 수 없습니다. <br>복사 활동의 경우 `quoteChar`가 빈 문자열로 정의 되 면 따옴표 문자 및 열 값이 따옴표로 묶여 있지 않으며 `escapeChar`를 사용 하 여 열 구분 기호 및 자체를 이스케이프 합니다. | 아닙니다.       |
| escapeChar       | 따옴표 붙은 값 안에서 따옴표를 이스케이프할 단일 문자입니다.<br>기본값은 **백슬래시 `\`** 입니다. <br>매핑 데이터 흐름의 경우 `escapeChar`는 빈 문자열일 수 없습니다. <br/>복사 활동의 경우 `escapeChar` 빈 문자열로 정의 된 경우에도 `quoteChar` 빈 문자열로 설정 해야 합니다 .이 경우 모든 열 값에 구분 기호가 포함 되지 않아야 합니다. | 아닙니다.       |
| firstRowAsHeader | 첫 번째 행을 열의 이름을 가진 머리글 줄로 처리할지 여부를 지정 합니다.<br>허용 되는 값은 **true** 및 **false** (기본값)입니다. | 아닙니다.       |
| nullValue        | Null 값의 문자열 표현을 지정 합니다. <br>기본값은 **빈 문자열**입니다. | 아닙니다.       |
| encodingName     | 테스트 파일을 읽고 쓰는 데 사용 되는 인코딩 형식입니다. <br>허용 되는 값은 다음과 같습니다. "UTF-8", "UTF-16", "UTF-16 be", "UTF-32", "UTF-16-32BE", "US-ASCII", "UTF-7", "BIG5", "EUC-JP", "EUC-KR", "GB2312", "GB18030", "조합", "CP875", "", "CP866", "IBM00858", "IBM037", "IBM273", "IBM437", "IBM500", " IBM737", "IBM775", "IBM850", "IBM852", "IBM855", "IBM857", "IBM860", "IBM861", "IBM863", "IBM864", "IBM865", "IBM869", "IBM870", "IBM01140", "IBM01141", "IBM01142", "IBM01143", "IBM01144", "IBM01145", "IBM01146", "IBM01147", "IBM01148", "IBM01149" , "ISO-2022-JP", "ISO-2022-KR", "ISO-8859-1", "ISO-8859-2", "ISO-8859-3", "ISO-8859-4", "ISO-8859-5", "ISO-8859-6", "ISO-8859-7", "ISO-8859-8", "ISO-8859-9", "ISO-8859-13", "ISO-8859-15", "WINDOWS-874", "WINDOWS-1250", "WINDOWS-1251", " WINDOWS-1252 "," WINDOWS-1253 "," WINDOWS-1254 "," WINDOWS-1255 "," WINDOWS-1256 "," WINDOWS-1257 "," WINDOWS-1258 "입니다.<br>참고 매핑 데이터 흐름은 UTF-7 인코딩을 지원 하지 않습니다. | 아닙니다.       |
| compressionCodec | 텍스트 파일을 읽고 쓰는 데 사용 되는 압축 코덱입니다. <br>허용 되는 값은 **bzip2**, **gzip**, **deflate**, **ZipDeflate**, **snappy**또는 **lz4**입니다. 를 사용 하 여 파일을 저장할 수 있습니다. <br>참고 현재 복사 작업은 "snappy" & "lz4"을 지원 하지 않으며 매핑 데이터 흐름은 "ZipDeflate"를 지원 하지 않습니다. <br>참고 복사 작업을 사용 하 여 ZipDeflate 파일의 압축을 풀고 파일 기반 싱크 데이터 저장소에 쓸 때 파일은 폴더로 추출 됩니다. `<path specified in dataset>/<folder named as source zip file>/`. | 아닙니다.       |
| compressionLevel | 압축 비율입니다. <br>허용 되는 값은 **최적** 또는 **가장 빠릅니다**.<br>- **가장 빠름:** 압축 작업은 결과 파일이 최적으로 압축 되지 않은 경우에도 최대한 빨리 완료 되어야 합니다.<br>- **최적**: 작업을 완료 하는 데 시간이 더 오래 걸리는 경우에도 압축 작업을 최적으로 압축 해야 합니다. 자세한 내용은 [압축 수준](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) 항목을 참조하세요. | 아닙니다.       |

다음은 Azure Blob Storage에서 분리 된 텍스트 데이터 집합의 예입니다.

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
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
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 구분 기호로 분리 된 텍스트 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="delimited-text-as-source"></a>원본으로 구분 된 텍스트 

복사 작업 ***\*원본\**** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성       | Description                                                  | 필수 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 복사 작업 원본의 type 속성은 **DelimitedTextSource**로 설정 해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **구분 된 텍스트 읽기 설정** 표를 참조 하세요. | 아닙니다.       |
| storeSettings  | 데이터 저장소에서 데이터를 읽는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터에는 `storeSettings`에서 고유한 지원 읽기 설정이 있습니다. | 아닙니다.       |

`formatSettings`아래의 지원 되는 **구분 된 텍스트 읽기 설정** :

| 속성      | Description                                                  | 필수 |
| ------------- | ------------------------------------------------------------ | -------- |
| type          | FormatSettings의 형식은 **DelimitedTextReadSettings**로 설정 해야 합니다. | 예      |
| skipLineCount | 입력 파일에서 데이터를 읽을 때 건너뛸 **비어 있지 않은** 행의 수를 나타냅니다. <br>skipLineCount와 firstRowAsHeader가 모두 지정되면 먼저 줄을 건너뛴 다음, 입력 파일에서 헤더 정보를 읽습니다. | 아닙니다.       |

### <a name="delimited-text-as-sink"></a>싱크로 분리 된 텍스트

복사 작업 ***\*싱크\**** 섹션에서 지원 되는 속성은 다음과 같습니다.

| 속성       | Description                                                  | 필수 |
| -------------- | ------------------------------------------------------------ | -------- |
| type           | 복사 작업 원본의 type 속성은 **DelimitedTextSink**로 설정 해야 합니다. | 예      |
| formatSettings | 속성 그룹입니다. 아래의 **구분 된 텍스트 쓰기 설정** 표를 참조 하세요. |          |
| storeSettings  | 데이터 저장소에 데이터를 쓰는 방법에 대한 속성 그룹입니다. 각 파일 기반 커넥터의 `storeSettings`에는 자체 지원 되는 쓰기 설정이 있습니다.  | 아닙니다.       |

`formatSettings`에서 지원 되는 **구분 된 텍스트 쓰기 설정** :

| 속성      | Description                                                  | 필수                                              |
| ------------- | ------------------------------------------------------------ | ----------------------------------------------------- |
| type          | FormatSettings의 형식은 **DelimitedTextWriteSettings**로 설정 해야 합니다. | 예                                                   |
| fileExtension | 출력 파일의 이름 (예: `.csv`, `.txt`)을 사용 하는 데 사용 되는 파일 확장명입니다. `fileName`를 출력 DelimitedText 데이터 집합에 지정 하지 않은 경우에는 지정 해야 합니다. 출력 데이터 집합에서 파일 이름이 구성 되 면 싱크 파일 이름으로 사용 되 고 파일 확장명 설정이 무시 됩니다.  | 출력 데이터 집합에 파일 이름이 지정 되지 않은 경우 예 |

## <a name="mapping-data-flow-properties"></a>데이터 흐름 속성 매핑

데이터 흐름 매핑의 [원본 변환](data-flow-source.md) 및 [싱크 변환](data-flow-sink.md) 에서 자세한 정보를 알아보세요.

## <a name="next-steps"></a>다음 단계

- [복사 작업 개요](copy-activity-overview.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [조회 작업](control-flow-lookup-activity.md)
- [GetMetadata 작업](control-flow-get-metadata-activity.md)
