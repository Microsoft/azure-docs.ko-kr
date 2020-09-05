---
title: 데이터 세트
description: Data Factory의 데이터 세트에 대해 알아봅니다. 데이터 세트는 입/출력 데이터를 나타냅니다.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2020
ms.openlocfilehash: 747d780b8f679adf66810bdcdf6e9b263e8d241c
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923758"
---
# <a name="datasets-in-azure-data-factory"></a>Azure 데이터 팩터리의 데이터 세트
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-create-datasets.md)
> * [현재 버전](concepts-datasets-linked-services.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


이 문서에서는 데이터 세트의 정의, 데이터 세트를 JSON 형식으로 정의하는 방법, Azure Data Factory 파이프라인에서 데이터 세트를 사용하는 방법에 대해 설명합니다.

Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

## <a name="overview"></a>개요
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. **파이프라인**은 함께 하나의 작업을 수행하는 **활동**의 논리적 그룹화입니다. 파이프라인의 활동은 데이터에 수행할 작업을 정의합니다. 이제 **데이터 세트**는 입력 및 출력으로 **활동**에서 사용하려는 데이터를 단순히 가리키거나 참조하는 데이터의 명명된 뷰입니다. 데이터 세트는 테이블, 파일, 폴더, 문서를 비롯한 다양한 데이터 저장소 내의 데이터를 식별합니다. 예를 들어 Azure Blob 데이터 세트은 활동에서 데이터를 읽어들여야 하는 Blob Storage의 Blob 컨테이너와 폴더를 지정합니다.

데이터 세트를 만들기 전에 [**연결된 서비스**](concepts-linked-services.md)를 만들어 데이터 저장소를 Data Factory에 연결해야 합니다. 연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 데이터 세트는 연결된 데이터 저장소 내의 데이터 구조를 나타내고, 연결된 서비스는 데이터 원본에 연결을 정의한다고 생각하시면 됩니다. 예를 들어 Azure Storage 연결된 서비스는 스토리지 계정을 데이터 팩터리에 연결합니다. Azure Blob 데이터 세트는 Blob 컨테이너 및 처리할 입력 Blob이 포함된 Azure Storage 계정 내의 폴더를 나타냅니다.

샘플 시나리오는 다음과 같습니다. Blob 저장소에서 SQL Database로 데이터를 복사 하려면 두 개의 연결 된 서비스 Azure Blob Storage와 Azure SQL Database를 만듭니다. 그런 다음 두 개의 데이터 집합을 만듭니다. 구분 된 텍스트 데이터 집합은 (Azure Blob Storage 연결 된 서비스를 참조 하 고, 텍스트 파일은 원본으로 가정), Azure SQL 테이블 데이터 집합 (Azure SQL Database 연결 된 서비스를 나타냄)을 만듭니다. Azure Blob Storage 및 Azure SQL Database 연결 된 서비스에는 런타임에를 사용 하 여 Azure Storage 및 Azure SQL Database에 연결 하는 데 사용 Data Factory는 연결 문자열이 포함 되어 있습니다. 구분 기호로 분리 된 텍스트 데이터 집합은 blob 저장소의 입력 blob을 포함 하는 blob 컨테이너 및 blob 폴더를 형식 관련 설정과 함께 지정 합니다. Azure SQL Table 데이터 세트는 데이터를 복사할 Azure SQL Database의 SQL 테이블을 지정합니다.

다음 다이어그램에서는 Data Factory의 파이프라인, 활동, 데이터 세트 및 연결된 서비스 간의 관계를 보여 줍니다.

![파이프라인, 활동, 데이터 세트, 연결된 서비스 간 관계](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>데이터 세트 JSON
Data Factory의 데이터 세트는 다음과 같이 JSON 형식으로 정의됩니다.

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: DelimitedText, AzureSqlTable etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema":[

        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
다음 표에서는 위의 JSON에서 속성을 설명합니다.

속성 | Description | 필수 |
-------- | ----------- | -------- |
name | 데이터 세트의 이름입니다. [Azure Data Factory - 이름 지정 규칙](naming-rules.md)을 참조하세요. |  예 |
type | 데이터 세트의 형식입니다. Data Factory에서 지 원하는 형식 (예: DelimitedText, AzureSqlTable) 중 하나를 지정 합니다. <br/><br/>자세한 내용은 [데이터 세트 형식](#dataset-type)을 참조하세요. | 예 |
스키마 | 데이터 집합의 스키마는 실제 데이터 형식과 모양을 나타냅니다. | 예 |
typeProperties | 형식 속성은 각 형식 마다 다릅니다. 지원되는 형식 및 해당 속성에 대한 자세한 내용은 [데이터 세트 형식](#dataset-type)을 참조하세요. | 예 |

데이터 집합의 스키마를 가져올 때 **스키마 가져오기** 단추를 선택 하 고 원본 또는 로컬 파일에서 가져오기를 선택 합니다. 대부분의 경우 소스에서 직접 스키마를 가져옵니다. 그러나 로컬 스키마 파일(Parquet 파일 또는 헤더가 포함된 CSV)이 이미 있는 경우에는 해당 파일의 스키마를 기반으로 Data Factory를 지정할 수 있습니다.

복사 작업에서 데이터 집합은 원본 및 싱크에 사용 됩니다. 데이터 집합에 정의 된 스키마는 참조로 선택 사항입니다. 원본과 싱크 간에 열/필드 매핑을 적용 하려면 [스키마 및 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조 하세요.

데이터 흐름에서 데이터 세트는 소스 및 싱크 변환에 사용됩니다. 데이터 세트는 기본 데이터 스키마를 정의합니다. 데이터에 스키마가 없는 경우 소스 및 싱크에 대해 스키마 드리프트를 사용할 수 있습니다. 데이터 세트의 메타데이터가 소스 변환에서 소스 ‘프로젝션’으로 나타납니다. 소스 변환의 프로젝션은 정의된 이름 및 형식을 사용하여 데이터 흐름 데이터를 나타냅니다.

## <a name="dataset-type"></a>데이터 세트 형식

Azure Data Factory는 사용 하는 데이터 저장소에 따라 다양 한 유형의 데이터 집합을 지원 합니다. [커넥터 개요](connector-overview.md) 문서에서 Data Factory 지원 되는 데이터 저장소 목록을 찾을 수 있습니다. 데이터 저장소를 클릭 하 여 연결 된 서비스 및 데이터 집합을 만드는 방법을 알아보세요.

예를 들어, 구분 기호로 분리 된 텍스트 데이터 집합의 경우 데이터 집합 형식은 다음 JSON 샘플과 같이 **DelimitedText** 로 설정 됩니다.

```json
{
    "name": "DelimitedTextInput",
    "properties": {
        "linkedServiceName": {
            "referenceName": "AzureBlobStorage",
            "type": "LinkedServiceReference"
        },
        "annotations": [],
        "type": "DelimitedText",
        "typeProperties": {
            "location": {
                "type": "AzureBlobStorageLocation",
                "fileName": "input.log",
                "folderPath": "inputdata",
                "container": "adfgetstarted"
            },
            "columnDelimiter": ",",
            "escapeChar": "\\",
            "quoteChar": "\""
        },
        "schema": []
    }
}
```

## <a name="create-datasets"></a>데이터 세트 만들기
[.NET API](quickstart-create-data-factory-dot-net.md), [PowerShell](quickstart-create-data-factory-powershell.md), [REST API](quickstart-create-data-factory-rest-api.md), Azure Resource Manager 템플릿 및 Azure Portal 등의 도구 또는 SDK 중 하나를 사용하여 데이터 세트를 만들 수 있습니다.

## <a name="current-version-vs-version-1-datasets"></a>현재 버전 및 버전 1 데이터 세트

Data Factory 및 Data Factory 버전 1 데이터 세트 사이에 몇 가지 차이점은 다음과 같습니다.

- 외부 속성은 현재 버전에서 지원되지 않습니다. [트리거](concepts-pipeline-execution-triggers.md)로 대체됩니다.
- 정책 및 가용성 속성은 현재 버전에서 지원되지 않습니다. 파이프라인에 대한 시작 시간은 [트리거](concepts-pipeline-execution-triggers.md)에 따라 달라집니다.
- 범위가 지정된 데이터 세트(파이프라인에서 정의된 데이터 세트)는 현재 버전에서 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
다음 도구 또는 SDK 중 하나를 사용하여 파이프라인 및 데이터 세트를 만들기 위한 단계별 지침은 다음 자습서를 참조하세요.

- [빠른 시작: .NET을 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-dot-net.md)
- [빠른 시작: PowerShell을 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-powershell.md)
- [빠른 시작: REST API를 사용하여 데이터 팩터리 만들기](quickstart-create-data-factory-rest-api.md)
- [빠른 시작: Azure Portal을 사용하여 Data Factory 만들기](quickstart-create-data-factory-portal.md)
