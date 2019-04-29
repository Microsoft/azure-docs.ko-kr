---
title: Azure SQL Database 간 데이터 복사 | Microsoft Docs
description: Azure Data Factory를 사용하여 Azure SQL Database 간에 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 484f735b-8464-40ba-a9fc-820e6553159e
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: a7789f9a3f3da46305a9d8cd7cda24019658f2ad
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567268"
---
# <a name="copy-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure SQL Database 간 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](data-factory-azure-sql-connector.md)
> * [버전 2(현재 버전)](../connector-azure-sql-database.md)

> [!NOTE]
> 이 문서의 내용은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 Azure SQL Database 커넥터](../connector-azure-sql-database.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure SQL Database 간에 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오
**Azure SQL Database**에서 다음 데이터 저장소로 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

다음 데이터 저장소에서 **Azure SQL Database**로 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="supported-authentication-type"></a>지원되는 인증 유형
Azure SQL Database 커넥터는 기본 인증을 지원합니다.

## <a name="getting-started"></a>시작
다른 도구/API를 사용하여 Azure SQL Database 간에 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사**를 사용하는 것입니다. 단계별 지침은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습을 볼 수 있습니다.

또한 다음 도구를 사용하여 파이프라인을 만들 수 있습니다. **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 및 **REST API** 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. **데이터 팩터리**를 만듭니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다.
2. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스**를 만듭니다. 예를 들어 Azure Blob 스토리지에서 Azure SQL 데이터베이스로 데이터를 복사하는 경우 Azure Storage 계정 및 Azure SQL 데이터베이스를 데이터 팩터리에 연결하는 두 개의 연결된 서비스를 만듭니다. Azure SQL Database와 관련된 연결된 서비스 속성은 [연결된 서비스 속성](#linked-service-properties) 섹션을 참조하세요.
3. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트**를 만듭니다. 마지막 단계에서 설명한 예제에서는 입력 데이터가 포함된 BLOB 컨테이너 및 폴더를 지정하는 데이터 세트를 만듭니다. 그리고 Blob Storage에서 복사한 데이터를 포함하는 Azure SQL 데이터베이스의 SQL 테이블을 지정하는 또 다른 데이터 세트를 만듭니다. Azure Data Lake Store와 관련된 데이터 세트 속성은 [데이터 세트 속성](#dataset-properties) 섹션을 참조하세요.
4. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인**을 만듭니다. 앞에서 언급한 예에서는 BlobSource를 원본으로, SqlSink를 복사 작업의 싱크로 사용합니다. 마찬가지로, Azure SQL Database에서 Azure Blob Storage로 복사하는 경우 복사 작업에 SqlSource 및 BlobSink를 사용합니다. Azure SQL Database와 관련된 복사 작업 속성은 [복사 작업 속성](#copy-activity-properties) 섹션을 참조하세요. 원본 또는 싱크로 데이터 저장소를 사용하는 방법에 대 한 자세한 내용을 보려면 데이터 저장소에 대한 이전 섹션의 링크를 클릭하세요.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다. 다른 곳에서 Azure SQL Database로 또는 그 반대로 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의가 포함된 샘플은 이 문서의 [JSON 샘플](#json-examples-for-copying-data-to-and-from-sql-database) 섹션을 참조하세요.

다음 섹션에서는 Azure SQL Database에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
Azure SQL 데이터베이스를 데이터 팩터리에 연결하는 Azure SQL 연결된 서비스입니다. 다음 테이블은 Azure SQL 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 형식 |type 속성을 다음으로 설정해야 합니다. **AzureSqlDatabase** |예 |
| connectionString |Azure SQL Database 인스턴스에 연결하는 데 필요한 정보를 connectionString 속성에 대해 지정합니다. 기본 인증만 지원됩니다. |예 |

> [!IMPORTANT]
> 데이터베이스 서버인 [Azure SQL Database 방화벽](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure)을 구성하여 [Azure 서비스가 서버에 액세스할 수 있도록](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) 해야 합니다. 또한 데이터 팩터리 게이트웨이를 사용하여 온-프레미스 데이터 원본을 포함한 Azure 외부에서 Azure SQL 데이터베이스로 데이터를 복사하는 경우 데이터를 Azure SQL Database로 보내는 컴퓨터에 대한 적절한 IP 주소 범위를 구성합니다.

## <a name="dataset-properties"></a>데이터 세트 속성
Azure SQL 데이터베이스에서 입력 또는 출력 데이터를 표시할 데이터 세트를 지정하려면 데이터 세트의 형식 속성을 다음으로 설정합니다. **AzureSqlTable**. 데이터 세트의 **linkedServiceName** 속성을 Azure SQL 연결된 서비스의 이름으로 설정합니다.

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

typeProperties 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **AzureSqlTable** 데이터 세트 형식의 데이터 세트에 대한 **typeProperties** 섹션에는 다음 속성이 있습니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 Azure SQL Database 인스턴스에서 테이블 또는 보기의 이름입니다. |예 |

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

> [!NOTE]
> 복사 작업은 하나의 입력을 가지고 하나의 출력을 생성합니다.

반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

Azure SQL 데이터베이스에서 데이터를 이동하는 경우 복사 작업의 원본 유형을 **SqlSource**로 설정합니다. 마찬가지로 Azure SQL 데이터베이스로 데이터를 이동하는 경우 복사 작업의 싱크 유형을 **SqlSink**로 설정합니다. 이 섹션에서는 SqlSource 및 SqlSink에서 지원되는 속성의 목록을 제공합니다.

### <a name="sqlsource"></a>SqlSource
원본이 **SqlSource** 형식인 복사 작업의 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 자산 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| SqlReaderQuery |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예: `select * from MyTable`. |아니오 |
| sqlReaderStoredProcedureName |원본 테이블에서 데이터를 읽는 저장 프로시저의 이름입니다. |저장 프로시저의 이름입니다. 마지막 SQL 문은 저장 프로시저의 SELECT 문이어야 합니다. |아니오 |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다. |이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아닙니다. |

**sqlReaderQuery**가 SqlSource에 지정되면 복사 작업은 데이터를 가져오는 Azure SQL Database 원본에 대해 이 쿼리를 실행합니다. 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).

sqlReaderQuery 또는 sqlReaderStoredProcedureName 중 하나를 지정하지 않으면 JSON 데이터 세트의 구조 섹션에 정의된 열은 쿼리(`select column1, column2 from mytable`)를 빌드하는 데 사용되어 Azure SQL Database에 대해 실행합니다. 데이터 세트 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

> [!NOTE]
> **sqlReaderStoredProcedureName**을 사용하는 경우에도 데이터 세트 JSON에서 **tableName** 속성 값을 지정해야 합니다. 그러나 이 테이블에 대해 수행되는 유효성 검사는 없습니다.
>
>

### <a name="sqlsource-example"></a>SqlSource 예제

```JSON
"source": {
    "type": "SqlSource",
    "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
    "storedProcedureParameters": {
        "stringData": { "value": "str3" },
        "identifier": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
    }
}
```

**저장 프로시저 정의:**

```SQL
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
    select *
    from dbo.UnitTestSrcTable
    where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="sqlsink"></a>파이프라인
**SqlSink** 는 다음 속성을 지원합니다.

| 자산 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. |timespan<br/><br/> 예제: “00:30:00”(30분) |아닙니다. |
| writeBatchSize |버퍼 크기가 writeBatchSize에 도달하는 경우 SQL 테이블에 데이터 삽입 |정수(행 수) |아니요(기본값: 10000) |
| sqlWriterCleanupScript |특정 조각의 데이터를 정리하기 위해 복사 활동에 대해 실행할 쿼리를 지정합니다. 자세한 내용은 [반복 가능한 복사](#repeatable-copy)를 참조하세요. |쿼리 문입니다. |아닙니다. |
| sliceIdentifierColumnName |자동 생성된 조각 식별자를 입력할 복사 활동의 열 이름을 지정합니다. 이 식별자는 복사 활동을 다시 실행할 때 특정 조각의 데이터를 정리하는 데 사용됩니다. 자세한 내용은 [반복 가능한 복사](#repeatable-copy)를 참조하세요. |이진(32) 데이터 형식이 있는 열의 열 이름입니다. |아닙니다. |
| sqlWriterStoredProcedureName |원본 데이터를 대상 테이블에 적용하는 방법(예: 사용자 고유의 비즈니스 논리를 사용하여 upsert 또는 transform 수행)을 정의하는 저장 프로시저의 이름입니다. <br/><br/>이 저장 프로시저는 **배치마다 호출**됩니다. 한 번만 실행되고 원본 데이터와 아무런 관련이 없는 작업(예: 삭제/자르기)을 수행하려는 경우 `sqlWriterCleanupScript` 속성을 사용합니다. |저장 프로시저의 이름입니다. |아닙니다. |
| storedProcedureParameters |저장 프로시저에 대한 매개 변수입니다. |이름/값 쌍입니다. 매개 변수의 이름 및 대소문자와, 저장 프로시저 매개변수의 이름 및 대소문자와 일치해야 합니다. |아닙니다. |
| sqlWriterTableType |저장 프로시저에 사용할 테이블 형식 이름을 지정합니다. 복사 작업을 사용하면 이 테이블 형식으로 임시 테이블에서 사용할 수 있는 데이터를 이동시킵니다. 그러면 저장 프로시저 코드가 복사되는 데이터를 기존 데이터와 병합할 수 있습니다. |테이블 유형 이름 |아닙니다. |

#### <a name="sqlsink-example"></a>SqlSink 예제

```JSON
"sink": {
    "type": "SqlSink",
    "writeBatchSize": 1000000,
    "writeBatchTimeout": "00:05:00",
    "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
    "sqlWriterTableType": "CopyTestTableType",
    "storedProcedureParameters": {
        "identifier": { "value": "1", "type": "Int" },
        "stringData": { "value": "str1" },
        "decimalData": { "value": "1", "type": "Decimal" }
    }
}
```

## <a name="json-examples-for-copying-data-to-and-from-sql-database"></a>SQL Database로/에서 데이터를 복사하는 JSON 예제
다음 예에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. Azure Blob Storage 및 Azure SQL Database 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure 데이터 팩터리의 복사 작업을 사용하여 임의의 원본에서 **여기**에 설명한 싱크로 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.

### <a name="example-copy-data-from-azure-sql-database-to-azure-blob"></a>예제: Azure SQL Database에서 Azure Blob으로 데이터 복사
샘플이 다음 데이터 팩터리 엔터티를 정의합니다.

1. [AzureSqlDatabase](#linked-service-properties)형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
3. [AzureSqlTable](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
4. [Azure Blob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
5. [SqlSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Azure SQL 데이터베이스의 테이블에서 Blob으로 (매시간, 매일 등) 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL Database 연결된 서비스:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
연결된 서비스에서 지원하는 속성의 목록은 Azure SQL 연결된 서비스 섹션을 참조하세요.

**Azure Blob Storage 연결된 서비스:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
연결된 서비스에서 지원하는 속성의 목록은 [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) 문서를 참조하세요.


**Azure SQL 입력 데이터 세트:**

샘플은 Azure SQL에서 만든 테이블 "MyTable"에 시계열 데이터에 대한 "timestampcolumn"라는 열이 포함되었다고 가정합니다.

"external": "true"로 설정하면 데이터 세트가 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Azure Data Factory 서비스에 전달됩니다.

```JSON
{
  "name": "AzureSqlInput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyTable"
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

데이터 세트 형식에서 지원하는 속성의 목록은 Azure SQL 데이터 세트 형식 속성 섹션을 참조하세요.

**Azure Blob 출력 데이터 세트:**

데이터는 1시간마다 새 blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
데이터 세트 형식에서 지원하는 속성의 목록은 [Azure Blob 데이터 세트 형식 속성](data-factory-azure-blob-connector.md#dataset-properties) 섹션을 참조하세요.

**SQL 원본 및 Blob 싱크를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **SqlSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다. **SqlReaderQuery** 속성에 지정된 SQL 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline for copy activity",
    "activities":[
      {
        "name": "AzureSQLtoBlob",
        "description": "copy activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureSQLInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "SqlSource",
            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
위의 예에서는 SqlSource에 대해 **sqlReaderQuery** 가 지정됩니다. 복사 작업은 데이터를 가져오는 Azure SQL Database 원본에 대해 이 쿼리를 실행합니다. 또는 **sqlReaderStoredProcedureName** 및 **storedProcedureParameters**를 지정하여 저장 프로시저를 지정할 수 있습니다(저장 프로시저가 매개 변수를 사용하는 경우).

sqlReaderQuery 또는 sqlReaderStoredProcedureName 중 하나를 지정하지 않으면 JSON 데이터 세트의 구조 섹션에 정의된 열은 쿼리를 작성하는 데 사용되어 Azure SQL Database에 대해 실행합니다. 예: `select column1, column2 from mytable` 데이터 세트 정의에 구조가 없는 경우 모든 열은 테이블에서 선택됩니다.

SqlSource 및 BlobSink에서 지원하는 속성 목록은 [Sql 원본](#sqlsource) 섹션 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 참조하세요.

### <a name="example-copy-data-from-azure-blob-to-azure-sql-database"></a>예제: Azure Blob에서 Azure SQL Database로 데이터 복사
샘플이 다음 데이터 팩터리 엔터티를 정의합니다.

1. [AzureSqlDatabase](#linked-service-properties)형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
4. [AzureSqlTable](#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)입니다.
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 및 [SqlSink](#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Azure blob에서 Azure SQL 데이터베이스의 테이블로 매시간 시계열 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure SQL 연결된 서비스:**

```JSON
{
  "name": "AzureSqlLinkedService",
  "properties": {
    "type": "AzureSqlDatabase",
    "typeProperties": {
      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
    }
  }
}
```
연결된 서비스에서 지원하는 속성의 목록은 Azure SQL 연결된 서비스 섹션을 참조하세요.

**Azure Blob Storage 연결된 서비스:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
연결된 서비스에서 지원하는 속성의 목록은 [Azure Blob](data-factory-azure-blob-connector.md#azure-storage-linked-service) 문서를 참조하세요.


**Azure Blob 입력 데이터 세트:**

데이터는 1시간마다 새 blob에서 선택됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 연도, 월 및 일 일부 시작 시간을 사용하고 파일 이름은 시작 시간의 시간 부분을 사용합니다. "external": "true" 설정은 데이터 팩터리 서비스에 이 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 작업에 의해 생성되지 않는다는 점을 알려줍니다.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
      "fileName": "{Hour}.csv",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "rowDelimiter": "\n"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```
데이터 세트 형식에서 지원하는 속성의 목록은 [Azure Blob 데이터 세트 형식 속성](data-factory-azure-blob-connector.md#dataset-properties) 섹션을 참조하세요.

**Azure SQL Database 출력 데이터 세트:**

샘플은 Azure SQL의 "MyTable"이라는 테이블에 데이터를 복사합니다. Blob CSV 파일을 포함하려 하면 같은 수의 열을 사용하여 Azure SQL의 테이블을 만듭니다. 새 행은 매시간 테이블에 추가됩니다.

```JSON
{
  "name": "AzureSqlOutput",
  "properties": {
    "type": "AzureSqlTable",
    "linkedServiceName": "AzureSqlLinkedService",
    "typeProperties": {
      "tableName": "MyOutputTable"
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
데이터 세트 형식에서 지원하는 속성의 목록은 Azure SQL 데이터 세트 형식 속성 섹션을 참조하세요.

**Blob 원본 및 SQL 싱크를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **원본** 형식은 **BlobSource**로 설정되고 **싱크** 형식은 **SqlSink**로 설정됩니다.

```JSON
{
  "name":"SamplePipeline",
  "properties":{
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[
      {
        "name": "AzureBlobtoSQL",
        "description": "Copy Activity",
        "type": "Copy",
        "inputs": [
          {
            "name": "AzureBlobInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureSqlOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource",
            "blobColumnSeparators": ","
          },
          "sink": {
            "type": "SqlSink"
          }
        },
        "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ]
  }
}
```
SqlSink 및 BlobSource에서 지원하는 속성 목록은 [Sql 싱크](#sqlsink) 섹션 및 [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties)를 참조하세요.

## <a name="identity-columns-in-the-target-database"></a>대상 데이터베이스의 ID 열
이 섹션에서는 ID 열이 없는 원본 테이블에서 ID 열이 있는 대상 테이블로 데이터를 복사하는 예제를 제공합니다.

**원본 테이블:**

```SQL
create table dbo.SourceTbl
(
    name varchar(100),
    age int
)
```
**대상 테이블:**

```SQL
create table dbo.TargetTbl
(
    identifier int identity(1,1),
    name varchar(100),
    age int
)
```
대상 테이블에 ID 열이 있는지 확인합니다.

**원본 데이터 세트 JSON 정의**

```JSON
{
    "name": "SampleSource",
    "properties": {
        "type": " SqlServerTable",
        "linkedServiceName": "TestIdentitySQL",
        "typeProperties": {
            "tableName": "SourceTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```
**대상 데이터 세트 JSON 정의**

```JSON
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": "TestIdentitySQLSource",
        "typeProperties": {
            "tableName": "TargetTbl"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": false,
        "policy": {}
    }
}
```

원본 테이블과 대상 테이블의 스키마가 서로 다릅니다(대상에 ID가 포함된 추가 열이 있음). 이 시나리오에서는 ID 열을 포함하지 않는 대상 데이터 세트 정의에서 **structure** 속성을 지정해야 합니다.

## <a name="invoke-stored-procedure-from-sql-sink"></a>SQL 싱크에서 저장된 프로시저 호출
파이프라인의 복사 작업에서, SQL 싱크에서 저장된 프로시저를 호출하는 예를 보려면 [복사 작업에서 SQL 싱크에 대한 저장 프로시저 호출](data-factory-invoke-stored-procedure-from-copy-activity.md) 문서를 참조하세요.

## <a name="type-mapping-for-azure-sql-database"></a>Azure SQL Database에 대한 형식 매핑
[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법을 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

Azure SQL Database 간에 데이터를 이동할 때는 다음과 같은 매핑이 SQL 형식에서 .NET 형식으로 또는 그 반대로 사용됩니다. 매핑은 ADO.NET에 대한 SQL Server 데이터 형식 매핑과 같습니다.

| SQL Server 데이터베이스 엔진 형식 | .NET Framework 형식 |
| --- | --- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| DateTime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| Image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

## <a name="map-source-to-sink-columns"></a>원본을 싱크 열로 매핑
원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하는 방법은 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="repeatable-copy"></a>반복 가능한 복사
SQL Server 데이터베이스로 데이터를 복사할 때 복사 작업은 기본적으로 싱크 테이블에 데이터를 추가합니다. 그 대신 UPSERT를 수행하려면 [SqlSink에 반복 가능한 쓰기](data-factory-repeatable-copy.md#repeatable-write-to-sqlsink) 문서를 참조하세요.

관계형 데이터 저장소에서 데이터를 복사할 때는 의도치 않는 결과를 방지하기 위해 반복성을 염두에 두어야 합니다. Azure Data Factory에서는 조각을 수동으로 다시 실행할 수 있습니다. 또한 오류가 발생하면 조각을 다시 실행하도록 데이터 세트에 대한 재시도 정책을 구성할 수 있습니다. 어느 쪽이든 조각이 재실행되는 경우 조각이 실행되는 횟수에 관계없이 같은 데이터를 읽어야 합니다. [관계형 원본에서 반복 가능한 읽기](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)를 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.
