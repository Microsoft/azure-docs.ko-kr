---
title: Azure Data Factory의 데이터 세트 및 연결된 서비스 | Microsoft Docs
description: Data Factory의 데이터 세트 및 연결된 서비스에 대해 알아봅니다. 연결된 서비스는 계산/데이터 저장소를 데이터 팩터리에 연결합니다. 데이터 세트는 입/출력 데이터를 나타냅니다.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: shlo
ms.openlocfilehash: 9e5da96cb02e681c83bd707fc038117050712ccf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262049"
---
# <a name="datasets-and-linked-services-in-azure-data-factory"></a>Azure Data Factory의 데이터 세트 및 연결된 서비스
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-create-datasets.md)
> * [현재 버전](concepts-datasets-linked-services.md)

이 문서에서는 데이터 세트의 정의, 데이터 세트를 JSON 형식으로 정의하는 방법, Azure Data Factory 파이프라인에서 데이터 세트를 사용하는 방법에 대해 설명합니다.

Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

## <a name="overview"></a>개요
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. **파이프라인**은 함께 하나의 작업을 수행하는 **활동**의 논리적 그룹화입니다. 파이프라인의 활동은 데이터에 수행할 작업을 정의합니다. 예를 들어 복사 활동을 사용하여 온-프레미스 SQL Server에서 Azure Blob Storage로 데이터를 복사할 수 있습니다. 그런 다음 Azure HDInsight 클러스터에서 Hive 스크립트를 실행하는 Hive 활동을 사용하여 출력 데이터를 생성하도록 Blob Storage의 데이터를 처리합니다. 마지막으로 두 번째 복사 활동을 사용하여 BI(비즈니스 인텔리전스) 보고 솔루션의 기반이 되는 Azure SQL Data Warehouse로 출력 데이터를 복사합니다. 파이프라인 및 활동에 대한 자세한 내용은 Azure Data Factory의 [파이프라인 및 활동](concepts-pipelines-activities.md) 문서를 참조하세요.

이제 **데이터 세트**는 입력 및 출력으로 **활동**에서 사용하려는 데이터를 단순히 가리키거나 참조하는 데이터의 명명된 뷰입니다. 데이터 세트는 테이블, 파일, 폴더, 문서를 비롯한 다양한 데이터 저장소 내의 데이터를 식별합니다. 예를 들어 Azure Blob 데이터 집합은 활동에서 데이터를 읽어들여야 하는 Blob Storage의 Blob 컨테이너와 폴더를 지정합니다.

데이터 세트를 만들기 전에 **연결된 서비스**를 만들어 데이터 저장소를 데이터 팩터리에 연결해야 합니다. 연결된 서비스는 Data Factory에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 데이터 세트는 연결된 데이터 저장소 내의 데이터 구조를 나타내고, 연결된 서비스는 데이터 원본에 연결을 정의한다고 생각하시면 됩니다. 예를 들어 Azure Storage 연결된 서비스는 스토리지 계정을 데이터 팩터리에 연결합니다. Azure Blob 데이터 세트는 Blob 컨테이너 및 처리할 입력 Blob이 포함된 Azure 저장소 계정 내의 폴더를 나타냅니다.

샘플 시나리오는 다음과 같습니다. Blob Storage에서 SQL Database로 데이터를 복사하려면 두 개의 연결된 서비스, 즉, Azure Storage 및 Azure SQL Database를 만듭니다. 그런 다음, 두 개의 데이터 세트, 즉 Azure Blob 데이터 세트(Azure Storage 연결된 서비스 참조), Azure SQL 테이블 데이터 세트(Azure SQL Database 연결된 서비스 참조)를 만듭니다. Azure Storage 및 Azure SQL Database 연결된 서비스에는 Data Factory가 런타임에 Azure Storage 및 Azure SQL Database 각각에 연결하는 데 사용하는 연결 문자열이 포함되어 있습니다. Azure Blob 데이터 집합은 Blob Storage에 입력 Blob을 포함하는 Blob 컨테이너와 Blob 폴더를 지정합니다. Azure SQL 테이블 데이터 세트는 데이터를 복사할 Azure SQL Database의 SQL 테이블을 지정합니다.

다음 다이어그램에서는 Data Factory의 파이프라인, 활동, 데이터 세트 및 연결된 서비스 간의 관계를 보여 줍니다.

![파이프라인, 활동, 데이터 세트, 연결된 서비스 간 관계](media/concepts-datasets-linked-services/relationship-between-data-factory-entities.png)

## <a name="linked-service-json"></a>연결된 서비스 JSON
Data Factory의 연결된 서비스는 다음과 같이 JSON 형식으로 정의됩니다.

```json
{
    "name": "<Name of the linked service>",
    "properties": {
        "type": "<Type of the linked service>",
        "typeProperties": {
              "<data store or compute-specific type properties>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

다음 표에서는 위의 JSON에서 속성을 설명합니다.

자산 | 설명 | 필수 |
-------- | ----------- | -------- |
이름 | 연결된 서비스의 이름입니다. [Azure Data Factory - 이름 지정 규칙](naming-rules.md)을 참조하세요. |  예 |
형식 | 연결된 서비스의 형식입니다. 예를 들면 다음과 같습니다. AzureStorage(데이터 저장소) 또는 AzureBatch(컴퓨팅). typeProperties에 대한 설명을 참조하세요. | 예 |
typeProperties | 형식 속성은 각 데이터 저장소 또는 계산에 대해 다릅니다. <br/><br/> 지원되는 데이터 저장소 형식 및 해당 형식 속성은 [데이터 세트 형식](#dataset-type) 표를 참조하세요. 데이터 저장소 관련 형식 속성에 대해 알아보려면 데이터 저장소 커넥터 문서로 이동하세요. <br/><br/> 지원되는 계산 형식 및 해당 형식 속성은 [컴퓨팅 연결 서비스](compute-linked-services.md)를 참조하세요. | 예 |
connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 아닙니다.

## <a name="linked-service-example"></a>연결된 서비스 예제
다음의 연결된 서비스는 Azure Storage 연결된 서비스입니다. 형식은 AzureStorage로 설정됩니다. Azure Storage 연결된 서비스의 형식 속성에는 연결 문자열이 포함됩니다. Data Factory 서비스는 이 연결 문자열을 사용하여 런타임에 데이터 저장소에 연결합니다.

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-json"></a>데이터 세트 JSON
Data Factory의 데이터 세트는 다음과 같이 JSON 형식으로 정의됩니다.

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
이름 | 데이터 세트의 이름입니다. [Azure Data Factory - 이름 지정 규칙](naming-rules.md)을 참조하세요. |  예 |
형식 | 데이터 세트의 형식입니다. Data Factory에서 지원하는 형식(예: AzureBlob, AzureSqlTable) 중 하나를 지정합니다. <br/><br/>자세한 내용은 [데이터 세트 형식](#dataset-type)을 참조하세요. | 예 |
structure | 데이터 세트의 스키마입니다. 자세한 내용은 [데이터 세트 구조](#dataset-structure)를 참조하세요. | 아닙니다. |
typeProperties | 형식 속성은 형식마다 다릅니다(예: Azure Blob, Azure SQL 테이블). 지원되는 형식 및 해당 속성에 대한 자세한 내용은 [데이터 세트 형식](#dataset-type)을 참조하세요. | 예 |

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
사용하는 데이터 저장소에 따라 다양한 유형의 데이터 세트가 있습니다. Data Factory에서 지원하는 데이터 저장소 목록은 다음 표를 참조하세요. 해당 데이터 저장소에 대해 연결된 서비스 및 데이터 세트를 만드는 방법을 알아보려면 데이터 저장소를 클릭합니다.

[!INCLUDE [data-factory-v2-supported-data-stores](../../includes/data-factory-v2-supported-data-stores.md)]

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
## <a name="dataset-structure"></a>데이터 세트 구조
**structure** 섹션은 선택 사항입니다. 열의 이름 및 데이터 형식 컬렉션을 포함하여 데이터 세트의 스키마를 정의합니다. 형식을 변환하고 열을 원본에서 대상으로 매핑하는 데 사용되는 형식 정보를 structure 섹션에 제공합니다.

structure의 각 열에는 다음과 같은 속성이 포함됩니다.

자산 | 설명 | 필수
-------- | ----------- | --------
이름 | 열의 이름입니다. | 예
형식 | 열의 데이터 형식입니다. Data Factory는 다음과 같은 중간 데이터 형식을 허용되는 값으로 지원합니다. **Int16, Int32, Int64, Single, Double, Decimal, Byte[], Boolean, String, Guid, Datetime, Datetimeoffset 및 Timespan** | 아닙니다.
culture | type이 `Datetime` 또는 `Datetimeoffset` .NET 형식일 때 사용할 .NET 기반 culture(문화권)입니다. 기본값은 `en-us`입니다. | 아닙니다.
format | type이 `Datetime` 또는 `Datetimeoffset` .NET 형식일 때 사용할 format(서식) 문자열입니다. 날짜/시간 형식을 지정하는 방법은 [사용자 지정 날짜 및 시간 형식 문자열](https://docs.microsoft.com/dotnet/standard/base-types/custom-date-and-time-format-strings)을 참조하세요. | 아닙니다.

### <a name="example"></a>예
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

### <a name="guidance"></a>지침

다음 지침은 structure 정보를 포함할 시기 및 **structure** 섹션에 포함할 내용을 결정하는 데 도움이 됩니다. [스키마 및 형식 매핑](copy-activity-schema-and-type-mapping.md)에서 데이터 팩터리에서 원본 데이터를 싱크로 매핑하는 방법 및 구조 정보를 지정하는 시기에 대해 알아봅니다.

- **강력한 스키마 데이터 원본의 경우** 원본 열을 싱크 열에 매핑하고 해당 이름이 동일하지 않은 경우에만 structure 섹션을 지정합니다. 이러한 종류의 구조화된 데이터 원본은 데이터 스키마 및 형식 정보를 데이터 자체와 함께 저장합니다. 구조화된 데이터 원본의 예로 SQL Server, Oracle 및 Azure SQL Database가 있습니다.<br/><br/>구조화된 데이터 원본에 대해 형식 정보를 이미 사용할 수 있으므로 "structure" 섹션을 포함할 때 형식 정보를 포함하면 안됩니다.
- **Blob Storage의 텍스트 파일과 같은 no/weak 스키마 데이터 원본의 경우** 데이터 집합이 복사 활동에 대한 입력인 경우 구조를 포함하고 원본 데이터 집합의 데이터 형식을 싱크의 네이티브 형식으로 변환해야 합니다. 또한 원본 열을 싱크 열로 매핑하려고 할 때 구조를 포함합니다.

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
- 빠른 시작: Azure Portal을 사용하여 데이터 팩터리 만들기
