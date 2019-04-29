---
title: Azure Data Factory에서 메타데이터 가져오기 작업 | Microsoft Docs
description: Data Factory 파이프라인에서 GetMetadata 작업을 사용 하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: jingwang
ms.openlocfilehash: 78f63b4f46fe5479d4d0fd5849ad80536d8a137c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61346912"
---
# <a name="get-metadata-activity-in-azure-data-factory"></a>Azure Data Factory에서 메타데이터 가져오기 작업

GetMetadata 작업은 Azure Data Factory에서 모든 데이터의 **메타데이터**를 검색하는 데 사용할 수 있습니다. 이 작업은 다음과 같은 시나리오에서 사용할 수 있습니다.

- 모든 데이터의 메타데이터 정보 유효성 검사
- 데이터가 준비되거나 사용할 수 있을 때 파이프라인 트리거

제어 흐름에서 다음 기능을 사용할 수 있습니다.

- GetMetadata 작업의 출력은 유효성 검사를 수행할 조건식에 사용할 수 있습니다.
- Do-Until 루프를 통해 조건이 충족되면 파이프라인을 트리거할 수 있습니다.

## <a name="supported-capabilities"></a>지원되는 기능

GetMetadata 작업은 데이터 세트를 필수 입력으로 사용하고, 작업 출력으로 사용할 수 있는 메타데이터 정보를 출력합니다. 현재 검색 가능한 해당 메타데이터가 있는 다음 커넥터가 지원되며 지원되는 최대 메타데이터 크기는 최대 **1MB**입니다.

>[!NOTE]
>자체 호스팅 Integration Runtime에서 GetMetadata 작업을 실행하면 최신 기능이 3.6 버전 이상에서 지원됩니다. 

### <a name="supported-connectors"></a>지원되는 커넥터

**파일 저장소:**

| 커넥터/메타데이터 | itemName<br>(파일/폴더) | itemType<br>(파일/폴더) | size<br>(파일) | created<br>(파일/폴더) | lastModified<br>(파일/폴더) |childItems<br>(폴더) |contentMD5<br>(파일) | structure<br/>(파일) | columnCount<br>(파일) | exists<br>(파일/폴더) |
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Amazon S3 | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Google Cloud Storage | √/√ | √/√ | √ | x/x | √/√* | √ | x | √ | √ | √/√* |
| Azure Blob | √/√ | √/√ | √ | x/x | √/√* | √ | √ | √ | √ | √/√ |
| Azure Data Lake Storage Gen1 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure Data Lake Storage Gen2 | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| Azure File Storage | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| 파일 시스템 | √/√ | √/√ | √ | √/√ | √/√ | √ | x | √ | √ | √/√ |
| SFTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |
| FTP | √/√ | √/√ | √ | x/x | √/√ | √ | x | √ | √ | √/√ |

- Amazon S3 및 Google Sloud Storage 합니다 `lastModified` 버킷 및 키가 있지만 가상이 아닌 폴더에 적용 됩니다; 및 `exists` 버킷 및 키 접두사 또는 안 가상 폴더에 적용 됩니다.
- Azure Blob의 경우, `lastModified`는 컨테이너와 Blob에 적용되고 가상 폴더에는 적용되지 않습니다.

**관계형 데이터베이스:**

| 커넥터/메타데이터 | structure | columnCount | exists |
|:--- |:--- |:--- |:--- |
| Azure SQL Database | √ | √ | √ |
| Azure SQL Database Managed Instance | √ | √ | √ |
| Azure SQL Data Warehouse | √ | √ | √ |
| SQL Server | √ | √ | √ |

### <a name="metadata-options"></a>메타데이터 옵션

GetMetadata 작업 필드 목록에 지정하여 검색할 수 있는 메타데이터 유형은 다음과 같습니다.

| 메타데이터 유형 | 설명 |
|:--- |:--- |
| itemName | 파일 또는 폴더의 이름입니다. |
| itemType | 파일 또는 폴더의 형식입니다. 출력 값은 `File` 또는 `Folder`입니다. |
| size | 파일의 크기(바이트)입니다. 파일에만 적용됩니다. |
| created | 파일 또는 폴더를 만든 날짜/시간입니다. |
| lastModified | 파일 또는 폴더를 마지막으로 수정한 날짜/시간입니다. |
| childItems | 지정된 폴더 내에 있는 하위 폴더 및 파일의 목록입니다. 폴더에만 적용됩니다. 출력 값은 각 하위 항목에 대한 이름 및 형식의 목록입니다. |
| contentMD5 | 파일의 MD5입니다. 파일에만 적용됩니다. |
| structure | 파일 또는 관계형 데이터베이스 테이블 내의 데이터 구조입니다. 출력 값은 열 이름과 열 형식의 목록입니다. |
| columnCount | 파일 또는 관계형 테이블 내의 열 수입니다. |
| exists| 파일/폴더/테이블이 있는지의 여부입니다. GetaMetadata 필드 목록에 "exists"가 지정되어 있으면 항목(파일/폴더/테이블)이 없는 경우에도 작업이 실패하지 않습니다. 대신 출력에 `exists: false`가 반환됩니다. |

>[!TIP]
>파일/폴더/테이블이 있는지 여부를 확인하려면 GetMetadata 작업 필드 목록에 `exists`를 지정한 다음, 작업 출력에서 `exists: true/false` 결과를 확인할 수 있습니다. 필드 목록에 `exists`가 구성되어 있지 않으면 개체를 찾을 수 없을 때 GetMetadata 작업이 실패합니다.

## <a name="syntax"></a>구문

**GetMetadata 작업:**

```json
{
    "name": "MyActivity",
    "type": "GetMetadata",
    "typeProperties": {
        "fieldList" : ["size", "lastModified", "structure"],
        "dataset": {
            "referenceName": "MyDataset",
            "type": "DatasetReference"
        }
    }
}
```

**데이터 세트:**

```json
{
    "name": "MyDataset",
    "properties": {
    "type": "AzureBlob",
        "linkedService": {
            "referenceName": "StorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath":"container/folder",
            "filename": "file.json",
            "format":{
                "type":"JsonFormat"
            }
        }
    }
}
```

## <a name="type-properties"></a>형식 속성

현재 GetMetadata 작업은 다음 유형의 메타데이터 정보를 가져올 수 있습니다.

자산 | 설명 | 필수
-------- | ----------- | --------
fieldList | 필요한 메타데이터 정보의 유형을 나열합니다. 지원되는 메타데이터에 대한 자세한 내용은 [메타데이터 옵션](#metadata-options) 섹션을 참조하세요. | 예 
데이터 세트 | GetMetadata 작업 시 메타데이터 작업을 검색할 참조 데이터 세트입니다. 지원되는 커넥터에 대한 [지원되는 기능](#supported-capabilities) 섹션을 참조하고, 데이터 세트 구문 세부 정보에 대한 커넥터 항목을 참조하세요. | 예

## <a name="sample-output"></a>샘플 출력

GetMetadata 결과는 작업 출력에 표시됩니다. 다음은 필드 목록에서 참조로 선택된 완전한 메타데이터 옵션이 있는 두 개의 샘플입니다. 결과를 후속 작업에 사용하려면 `@{activity('MyGetMetadataActivity').output.itemName}` 패턴을 사용합니다.

### <a name="get-a-files-metadata"></a>파일의 메타데이터 가져오기

```json
{
  "exists": true,
  "itemName": "test.csv",
  "itemType": "File",
  "size": 104857600,
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "contentMD5": "cMauY+Kz5zDm3eWa9VpoyQ==",
  "structure": [
    {
        "name": "id",
        "type": "Int64"
    },
    {
        "name": "name",
        "type": "String"
    }
  ],
  "columnCount": 2
}
```

### <a name="get-a-folders-metadata"></a>폴더의 메타데이터 가져오기

```json
{
  "exists": true,
  "itemName": "testFolder",
  "itemType": "Folder",
  "lastModified": "2017-02-23T06:17:09Z",
  "created": "2017-02-23T06:17:09Z",
  "childItems": [
    {
      "name": "test.avro",
      "type": "File"
    },
    {
      "name": "folder hello",
      "type": "Folder"
    }
  ]
}
```

## <a name="next-steps"></a>다음 단계
Data Factory에서 지원하는 다른 제어 흐름 작업을 참조하세요. 

- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
