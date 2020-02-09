---
title: 데이터 세트
description: Data Factory의 데이터 집합에 대해 알아봅니다. 데이터 세트는 입/출력 데이터를 나타냅니다.
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
ms.date: 04/25/2019
ms.openlocfilehash: 878ad98b118fa02a6659584ac60e3343a948cd20
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928487"
---
# <a name="datasets-in-azure-data-factory"></a>Azure 데이터 팩터리의 데이터 세트
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-create-datasets.md)
> * [현재 버전](concepts-datasets-linked-services.md)

이 문서에서는 데이터 세트의 정의, 데이터 세트를 JSON 형식으로 정의하는 방법, Azure Data Factory 파이프라인에서 데이터 세트를 사용하는 방법에 대해 설명합니다.

Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

## <a name="overview"></a>개요
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. **파이프라인**은 함께 하나의 작업을 수행하는 **활동**의 논리적 그룹화입니다. 파이프라인의 활동은 데이터에 수행할 작업을 정의합니다. 이제 **데이터 세트**는 입력 및 출력으로 **활동**에서 사용하려는 데이터를 단순히 가리키거나 참조하는 데이터의 명명된 뷰입니다. 데이터 세트는 테이블, 파일, 폴더, 문서를 비롯한 다양한 데이터 저장소 내의 데이터를 식별합니다. 예를 들어 Azure Blob 데이터 세트은 활동에서 데이터를 읽어들여야 하는 Blob Storage의 Blob 컨테이너와 폴더를 지정합니다.

데이터 집합을 만들기 전에 [**연결 된 서비스**](concepts-linked-services.md) 를 만들어 데이터 저장소를 데이터 팩터리에 연결 해야 합니다. 연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 데이터 세트는 연결된 데이터 저장소 내의 데이터 구조를 나타내고, 연결된 서비스는 데이터 원본에 연결을 정의한다고 생각하시면 됩니다. 예를 들어 Azure Storage 연결된 서비스는 스토리지 계정을 데이터 팩터리에 연결합니다. Azure Blob 데이터 세트는 Blob 컨테이너 및 처리할 입력 Blob이 포함된 Azure Storage 계정 내의 폴더를 나타냅니다.

샘플 시나리오는 다음과 같습니다. 데이터베이스를 Blob 스토리지에서 SQL 데이터베이스로 복사하려면 두 개의 연결된 서비스, 즉 Azure Storage 및 Azure SQL Database를 만듭니다. 그런 다음, 두 개의 데이터 세트, 즉 Azure Blob 데이터 세트(Azure Storage 연결된 서비스 참조), Azure SQL 테이블 데이터 세트(Azure SQL Database 연결된 서비스 참조)를 만듭니다. Azure Storage 및 Azure SQL Database 연결된 서비스에는 Data Factory가 런타임에 Azure Storage 및 Azure SQL Database 각각에 연결하는 데 사용하는 연결 문자열이 포함되어 있습니다. Azure Blob 데이터 세트은 Blob Storage에 입력 Blob을 포함하는 Blob 컨테이너와 Blob 폴더를 지정합니다. Azure SQL 테이블 데이터 세트는 데이터를 복사할 Azure SQL Database의 SQL 테이블을 지정합니다.

다음 다이어그램에서는 Data Factory의 파이프라인, 활동, 데이터 세트 및 연결된 서비스 간의 관계를 보여 줍니다.

![파이프라인, 활동, 데이터 세트, 연결된 서비스 간 관계](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)


## <a name="dataset-json"></a>데이터 세트 JSON
Data Factory의 데이터 집합은 다음과 같은 JSON 형식으로 정의 됩니다.

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```
다음 표에서는 위의 JSON에서 속성을 설명합니다.

자산 | 설명 | 필수 |
-------- | ----------- | -------- |
이름 | 데이터 세트의 이름입니다. [Azure Data Factory - 이름 지정 규칙](naming-rules.md)을 참조하세요. |  yes |
type | 데이터 세트의 형식입니다. Data Factory에서 지원하는 형식(예: AzureBlob, AzureSqlTable) 중 하나를 지정합니다. <br/><br/>자세한 내용은 [데이터 세트 형식](#dataset-type)을 참조하세요. | yes |
structure | 데이터 세트의 스키마입니다. 자세한 내용은 [데이터 집합 스키마](#dataset-structure-or-schema)를 참조 하세요. | 아닙니다. |
typeProperties | 형식 속성은 각 형식마다 다릅니다(예: Azure Blob, Azure SQL 테이블). 지원되는 형식 및 해당 속성에 대한 자세한 내용은 [데이터 세트 형식](#dataset-type)을 참조하세요. | yes |

### <a name="data-flow-compatible-dataset"></a>데이터 흐름 호환 데이터 집합



[데이터 흐름이](concepts-data-flow-overview.md) 호환 되는 데이터 집합 형식 목록은 [지원 되는 데이터 집합 형식](#dataset-type) 을 참조 하세요. 데이터 흐름에 대해 호환 되는 데이터 집합에는 변환에 대한 세분화 된 데이터 집합 정의가 필요 합니다. 따라서 JSON 정의는 약간 다릅니다. _구조_ 속성 대신 데이터 흐름에 호환 되는 데이터 집합에는 _스키마_ 속성이 있습니다.

데이터 흐름에서 데이터 집합은 원본 및 싱크 변환에 사용 됩니다. 데이터 집합은 기본 데이터 스키마를 정의 합니다. 데이터에 스키마가 없는 경우 원본 및 싱크에 대해 스키마 드리프트를 사용할 수 있습니다. 데이터 집합의 스키마는 실제 데이터 형식과 모양을 나타냅니다.

데이터 집합에서 스키마를 정의 하면 관련 된 연결 된 서비스에서 관련 데이터 형식, 데이터 형식, 파일 위치 및 연결 정보를 얻을 수 있습니다. 데이터 집합의 메타 데이터는 원본 변환에서 원본 *프로젝션*으로 나타납니다. 원본 변환의 프로젝션은 정의 된 이름 및 형식을 사용 하 여 데이터 흐름 데이터를 나타냅니다.

데이터 흐름 데이터 집합의 스키마를 가져올 때 **스키마 가져오기** 단추를 선택 하 고 원본 또는 로컬 파일에서 가져오기를 선택 합니다. 대부분의 경우 원본에서 직접 스키마를 가져옵니다. 그러나 로컬 스키마 파일 (Parquet 파일 또는 헤더가 포함 된 CSV)이 이미 있는 경우에는 해당 파일의 스키마를 기반으로 Data Factory를 지시할 수 있습니다.


```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "linkedServiceName": {
                "referenceName": "<name of linked service>",
                "type": "LinkedServiceReference",
        },
        "schema": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        }
    }
}
```

다음 표에서는 위의 JSON에서 속성을 설명합니다.

자산 | 설명 | 필수 |
-------- | ----------- | -------- |
이름 | 데이터 세트의 이름입니다. [Azure Data Factory - 이름 지정 규칙](naming-rules.md)을 참조하세요. |  yes |
type | 데이터 세트의 형식입니다. Data Factory에서 지원하는 형식(예: AzureBlob, AzureSqlTable) 중 하나를 지정합니다. <br/><br/>자세한 내용은 [데이터 세트 형식](#dataset-type)을 참조하세요. | yes |
schema | 데이터 세트의 스키마입니다. 자세한 내용은 [데이터 흐름 호환 데이터 집합](#dataset-type)을 참조 하세요. | 아닙니다. |
typeProperties | 형식 속성은 각 형식마다 다릅니다(예: Azure Blob, Azure SQL 테이블). 지원되는 형식 및 해당 속성에 대한 자세한 내용은 [데이터 세트 형식](#dataset-type)을 참조하세요. | yes |


## <a name="dataset-example"></a>데이터 세트 예제
다음 예제에서 데이터 세트는 SQL 데이터베이스에서 MyTable이라는 테이블을 나타냅니다.

```json
{
    "name": "DatasetSample",
    "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": {
                "referenceName": "MyAzureSqlLinkedService",
                "type": "LinkedServiceReference",
        },
        "typeProperties":
        {
            "tableName": "MyTable"
        },
    }
}

```
다음 사항에 유의하세요.

- type을 AzureSQLTable로 설정합니다.
- AzureSqlTable 형식과 관련된 tableName 형식 속성은 MyTable로 설정됩니다.
- linkedServiceName은 다음 JSON 코드 조각으로 정의된 AzureSqlDatabase 형식의 연결된 서비스를 가리킵니다.

## <a name="dataset-type"></a>데이터 세트 형식
사용하는 데이터 저장소에 따라 다양한 유형의 데이터 세트가 있습니다. [커넥터 개요](connector-overview.md) 문서에서 Data Factory 하 여 지원 되는 저장 된 데이터 목록을 찾을 수 있습니다. 해당 데이터 저장소에 대해 연결된 서비스 및 데이터 세트를 만드는 방법을 알아보려면 데이터 저장소를 클릭합니다.

이전 섹션의 예제에서 데이터 세트의 형식은 **AzureSqlTable**로 설정되어 있습니다. 마찬가지로 Azure Blob 데이터 세트의 경우 데이터 세트의 형식이 다음 JSON과 같이 **AzureBlob**으로 설정됩니다.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": {
                "referenceName": "MyAzureStorageLinkedService",
                "type": "LinkedServiceReference",
        },

        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        }
    }
}
```

## <a name="dataset-structure-or-schema"></a>데이터 집합 구조 또는 스키마
**구조** 섹션 또는 **스키마** (데이터 흐름 호환) 섹션 데이터 집합은 선택 사항입니다. 열의 이름 및 데이터 형식 컬렉션을 포함하여 데이터 세트의 스키마를 정의합니다. 형식을 변환하고 열을 원본에서 대상으로 매핑하는 데 사용되는 형식 정보를 structure 섹션에 제공합니다.

structure의 각 열에는 다음과 같은 속성이 포함됩니다.

자산 | 설명 | 필수
-------- | ----------- | --------
이름 | 열의 이름입니다. | yes
type | 열의 데이터 형식입니다. Data Factory는 **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset 및 Timespan** 값을 허용하는 중간 데이터 형식을 지원합니다. | 아닙니다.
culture | type이 `Datetime` 또는 `Datetimeoffset` .NET 형식일 때 사용할 .NET 기반 culture(문화권)입니다. 기본값은 `en-us`입니다. | 아닙니다.
format | type이 `Datetime` 또는 `Datetimeoffset` .NET 형식일 때 사용할 format(서식) 문자열입니다. 날짜/시간 형식을 지정하는 방법은 [사용자 지정 날짜 및 시간 형식 문자열](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)을 참조하세요. | 아닙니다.

### <a name="example"></a>예제
다음 예제에서는 원본 Blob 데이터가 CSV 형식이며 userid, name 및 lastlogindate의 세 열을 포함한다고 가정합니다. 요일에 축약된 프랑스 이름을 사용하여 사용자 지정 날짜/시간 형식을 사용하는 Int64, String 및 Datetime 형식입니다.

다음과 같이 열에 대한 형식 정의와 함께 Blob 데이터 세트 구조를 정의합니다.

```json
"structure":
[
    { "name": "userid", "type": "Int64"},
    { "name": "name", "type": "String"},
    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
]
```

### <a name="guidance"></a>인도

다음 지침은 structure 정보를 포함할 시기 및 **structure** 섹션에 포함할 내용을 결정하는 데 도움이 됩니다. [스키마 및 형식 매핑](copy-activity-schema-and-type-mapping.md)에서 데이터 팩터리에서 원본 데이터를 싱크로 매핑하는 방법 및 구조 정보를 지정하는 시기에 대해 알아봅니다.

- **강력한 스키마 데이터 원본의 경우** 원본 열을 싱크 열에 매핑하고 해당 이름이 동일하지 않은 경우에만 structure 섹션을 지정합니다. 이러한 종류의 구조화된 데이터 원본은 데이터 스키마 및 형식 정보를 데이터 자체와 함께 저장합니다. 구조화된 데이터 원본의 예로 SQL Server, Oracle 및 Azure SQL Database가 있습니다.<br/><br/>구조화된 데이터 원본에 대해 형식 정보를 이미 사용할 수 있으므로 "structure" 섹션을 포함할 때 형식 정보를 포함하면 안됩니다.
- **Blob Storage의 텍스트 파일과 같은 no/weak 스키마 데이터 원본의 경우** 데이터 세트이 복사 활동에 대한 입력인 경우 구조를 포함하고 원본 데이터 세트의 데이터 형식을 싱크의 네이티브 형식으로 변환해야 합니다. 또한 원본 열을 싱크 열로 매핑하려고 할 때 구조를 포함합니다.

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
- [빠른 시작: Azure Portal을 사용 하 여 데이터 팩터리 만들기](quickstart-create-data-factory-portal.md)
