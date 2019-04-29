---
title: Azure Cosmos DB 간 데이터 이동 | Microsoft Docs
description: Azure Data Factory를 사용하여 Azure Cosmos DB 컬렉션 간 데이터를 이동하는 방법에 대해 알아봅니다.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: c9297b71-1bb4-4b29-ba3c-4cf1f5575fac
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bda3df3ce869d7717f572f72c38472e7eae4a0ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60567217"
---
# <a name="move-data-to-and-from-azure-cosmos-db-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Cosmos DB 간 데이터 이동
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](data-factory-azure-documentdb-connector.md)
> * [버전 2(현재 버전)](../connector-azure-cosmos-db.md)

> [!NOTE]
> 이 아티클은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우 [V2의 Azure Cosmos DB 커넥터](../connector-azure-cosmos-db.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Cosmos DB(SQL API) 간에 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

모든 지원되는 원본 데이터 저장소에서 Azure Cosmos DB로 또는 Azure Cosmos DB에서 모든 지원되는 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 테이블을 참조하세요.

> [!IMPORTANT]
> Azure Cosmos DB 커넥터만 SQL API를 지원합니다.

JSON 파일 또는 다른 Cosmos DB 컬렉션으로/에서 있는 그대로 데이터를 복사하려면 [JSON 문서 Import/Export](#importexport-json-documents)를 참조하세요.

## <a name="getting-started"></a>시작
다른 도구/API를 사용하여 Azure Cosmos DB 간에 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사**를 사용하는 것입니다. 단계별 지침은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습을 볼 수 있습니다.

또한 다음 도구를 사용하여 파이프라인을 만들 수 있습니다. **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 및 **REST API** 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스**를 만듭니다.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트**를 만듭니다.
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인**을 만듭니다.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다. 다른 곳에서 Cosmos DB로 또는 그 반대로 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의가 포함된 샘플은 이 문서의 [JSON 예제](#json-examples) 섹션을 참조하세요.

다음 섹션에서는 Cosmos DB에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 테이블은 Azure Cosmos DB 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| **속성** | **설명** | **필수** |
| --- | --- | --- |
| 형식 |type 속성을 다음으로 설정해야 합니다. **DocumentDb** |예 |
| connectionString |Azure Cosmos DB 데이터베이스에 연결하는 데 필요한 정보를 지정합니다. |예 |

예제:

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

typeProperties 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **DocumentDbCollection** 데이터 세트 형식의 데이터 세트에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| **속성** | **설명** | **필수** |
| --- | --- | --- |
| collectionName |Cosmos DB 문서 컬렉션의 이름입니다. |예 |

예제:

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
### <a name="schema-by-data-factory"></a>Data Factory에서의 스키마
Azure Cosmos DB와 같은 스키마 없는 데이터 저장소의 경우 Data Factory 서비스는 다음 방법 중 하나로 스키마를 유추합니다.

1. 데이터 세트 정의에서 **구조** 속성을 사용하여 데이터의 구조를 지정하는 경우 Data Factory 서비스는 이 구조를 스키마로 인식합니다. 이 경우 행에 열의 값이 포함되어 있지 않으면 null 값이 제공됩니다.
2. 데이터 세트 정의에서 **구조** 속성을 사용하여 데이터의 구조를 지정하지 않는 경우 Data Factory 서비스는 데이터의 첫 번째 행을 사용하여 스키마를 유추합니다. 이 경우 첫 번째 행에 전체 스키마가 포함되어 있지 않으면 일부 열이 복사 작업의 결과에서 누락됩니다.

따라서 스키마 없는 데이터 원본에 대한 모범 사례는 **구조** 속성을 사용하여 데이터의 구조를 지정하는 것입니다.

## <a name="copy-activity-properties"></a>복사 작업 속성
작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

> [!NOTE]
> 복사 작업은 하나의 입력을 가지고 하나의 출력을 생성합니다.

반면 작업의 typeProperties 섹션에서 사용할 수 있는 속성은 각 작업 형식에 따라 다르며 복사 작업의 경우 속성은 원본 및 싱크의 형식에 따라 다릅니다.

원본이 **DocumentDbCollectionSource** 형식인 복사 작업의 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| **속성** | **설명** | **허용되는 값** | **필수** |
| --- | --- | --- | --- |
| 쿼리 |데이터를 읽는 쿼리를 지정합니다. |Azure Cosmos DB에서 지원하는 쿼리 문자열입니다. <br/><br/>예제: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |아닙니다. <br/><br/>지정하지 않는 경우 실행되는 SQL 문: `select <columns defined in structure> from mycollection` |
| nestingSeparator |문서가 중첩됨을 나타내는 특수 문자 |모든 character입니다. <br/><br/>Azure Cosmos DB는 중첩된 구조를 허용하는 JSON 문서용 NoSQL 저장소입니다. Azure 데이터 팩터리를 사용하면 nestingSeparator, 즉 위 예에서 "."를 통해 계층 구조를 표시할 수 있습니다. 테이블 정의에서 "Name.First", "Name.Middle" 및 "Name.Last"에 따르면 구분 기호를 사용하여 복사 작업이 3개의 자식 요소(처음, 중간 및 마지막)가 있는 "Name" 개체를 생성합니다. |아닙니다. |

**DocumentDbCollectionSink**는 다음 속성을 지원합니다.

| **속성** | **설명** | **허용되는 값** | **필수** |
| --- | --- | --- | --- |
| nestingSeparator |중첩된 해당 문서를 나타내는 원본 열 이름에 특수 문자가 필요합니다. <br/><br/>위의 예에서 출력 테이블의 `Name.First`는 Cosmos DB 문서에서 다음 JSON 구조를 생성합니다.<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |중첩 수준을 구분하는데 사용되는 문자입니다.<br/><br/>기본값은 `.`.(점)입니다. |중첩 수준을 구분하는데 사용되는 문자입니다. <br/><br/>기본값은 `.`.(점)입니다. |
| writeBatchSize |문서를 작성하는 Azure Cosmos DB 서비스에 대한 병렬 요청 수입니다.<br/><br/>이 속성을 사용하여 Cosmos DB 간 데이터를 복사하는 경우 성능을 미세 조정할 수 있습니다. Cosmos DB에 더 많은 병렬 요청이 전송되기 때문에 writeBatchSize 증가하는 경우 더 나은 성능을 기대할 수 있습니다. 하지만 오류 메시지: “요청 빈도가 높습니다."를 발생시킬 수 있는 제한을 방지해야 합니다.<br/><br/>제한은 문서의 크기, 문서에서 용어의 수, 대상 컬렉션의 인덱싱 정책 등 여러 가지 요인으로 결정됩니다. 복사 작업의 경우 더 나은 컬렉션(예: S3)을 사용하여 사용할 수 있는 처리량(2,500개의 요청 단위/초)을 보유할 수 있습니다. |정수  |아니요(기본값: 5) |
| writeBatchTimeout |시간이 초과 되기 전에 완료하려는 작업을 위한 대기 시간입니다. |timespan<br/><br/> 예제: “00:30:00”(30분) |아닙니다. |

## <a name="importexport-json-documents"></a>JSON 문서 Import/Export
이 Cosmos DB 커넥터를 사용하여 다음을 쉽게 수행할 수 있습니다.

* Azure Blob, Azure Data Lake, 온-프레미스 파일 시스템 또는 기타 Azure Data Factory에서 지원하는 파일 기반 저장소 등 다양한 원본에서 Cosmos DB로 JSON 문서를 가져오기
* 다양 한 파일 기반 저장소에 Cosmos DB 컬렉션에서 JSON 문서를 내보냅니다.
* 두 Cosmos DB 컬렉션 간에 데이터를 있는 그대로 마이그레이션

이러한 스키마 독립적 복사를 수행하려면
* 복사 마법사를 사용할 경우 **"JSON 파일 또는 Cosmos DB 컬렉션으로 있는 그대로 내보내기"** 옵션을 선택합니다.
* JSON 편집을 사용할 때 복사 작업에서 Cosmos DB 데이터 세트에서 "structure" 섹션을 지정하지 않고 Cosmos DB 원본/싱크에 대한 "nestingSeparator" 속성도 지정하지 않아야 합니다. JSON 파일에서 가져오거나 이 파일로 내보내려면 파일 저장소 데이터 세트에서 형식을 "JsonFormat"으로, 구성을 "filePattern"으로 지정한 후 나머지 형식 설정을 건너뜁니다. 자세한 내용은 [JSON 형식](data-factory-supported-file-and-compression-formats.md#json-format) 섹션을 참조하세요.

## <a name="json-examples"></a>JSON 예
다음 예에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. Azure Cosmos DB 및 Azure Blob Storage 간에 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 임의의 원본에서 **여기**에 설명한 싱크로 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.

## <a name="example-copy-data-from-azure-cosmos-db-to-azure-blob"></a>예제: Azure Cosmos DB에서 Azure Blob으로 데이터 복사
아래 샘플은 다음을 보여줍니다.

1. [DocumentDb](#linked-service-properties)형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)형식의 연결된 서비스
3. [DocumentDbCollection](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
5. [DocumentDbCollectionSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

이 샘플에서는 Azure Cosmos DB에서 Azure Blob으로 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

**Azure Cosmos DB 연결된 서비스:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
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
**Azure 문서 DB 입력 데이터 세트:**

예제는 Azure Cosmos DB 데이터베이스에 **사람**이라는 컬렉션이 있다고 가정합니다.

"외부":"true" 설정 및 externalData 정책 지정은 Azure 데이터 팩터리 서비스가 테이블이 데이터 팩터리의 외부에 있으며 데이터 공장의 활동에 의해 생성되지 않는다는 점을 알립니다.

```JSON
{
  "name": "PersonCosmosDbTable",
  "properties": {
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Azure Blob 출력 데이터 세트:**

데이터는 시간 세분성으로 특정 날짜와 시간을 반영하는 blob에 대한 경로가 있는 새 blob로 매시간 복사됩니다.

```JSON
{
  "name": "PersonBlobTableOut",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
Cosmos DB 데이터베이스의 사용자 컬렉션에서 샘플 JSON 문서:

```JSON
{
  "PersonId": 2,
  "Name": {
    "First": "Jane",
    "Middle": "",
    "Last": "Doe"
  }
}
```
Cosmos DB는 계층적 JSON 문서에 대한 구문과 같이 SQL을 사용하여 쿼리 문서를 지원합니다.

예제:

```sql
SELECT Person.PersonId, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person
```

다음 파이프라인은 Azure Cosmos DB 데이터베이스의 Person 컬렉션에서 Azure blob에 데이터를 복사합니다. 복사 작업의 부분인 입력 및 출력 데이터 세트가 지정되었습니다.

```JSON
{
  "name": "DocDbToBlobPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "DocumentDbCollectionSource",
            "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
            "nestingSeparator": "."
          },
          "sink": {
            "type": "BlobSink",
            "blobWriterAddHeader": true,
            "writeBatchSize": 1000,
            "writeBatchTimeout": "00:00:59"
          }
        },
        "inputs": [
          {
            "name": "PersonCosmosDbTable"
          }
        ],
        "outputs": [
          {
            "name": "PersonBlobTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromDocDbToBlob"
      }
    ],
    "start": "2015-04-01T00:00:00Z",
    "end": "2015-04-02T00:00:00Z"
  }
}
```
## <a name="example-copy-data-from-azure-blob-to-azure-cosmos-db"></a>예제: Azure Blob에서 Azure Cosmos DB로 데이터 복사
아래 샘플은 다음을 보여줍니다.

1. DocumentDb 형식의 연결된 서비스.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
3. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
4. DocumentDbCollection 형식의 출력 [데이터 세트](data-factory-create-datasets.md).
5. [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) 및 DocumentDbCollectionSink를 사용하는 복사 작업이 있는 [파이프라인](data-factory-create-pipelines.md).

이 샘플은 Azure Blob에서 Azure Cosmos DB로 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

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
**Azure Cosmos DB 연결된 서비스:**

```JSON
{
  "name": "CosmosDbLinkedService",
  "properties": {
    "type": "DocumentDb",
    "typeProperties": {
      "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
    }
  }
}
```
**Azure Blob 입력 데이터 세트:**

```JSON
{
  "name": "PersonBlobTableIn",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "FirstName",
        "type": "String"
      },
      {
        "name": "MiddleName",
        "type": "String"
      },
      {
        "name": "LastName",
        "type": "String"
      }
    ],
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "fileName": "input.csv",
      "folderPath": "docdb",
      "format": {
        "type": "TextFormat",
        "columnDelimiter": ",",
        "nullValue": "NULL"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
**Azure Cosmos DB 출력 데이터 세트:**

샘플은 "Person"이라는 컬렉션에 데이터를 복사합니다.

```JSON
{
  "name": "PersonCosmosDbTableOut",
  "properties": {
    "structure": [
      {
        "name": "Id",
        "type": "Int"
      },
      {
        "name": "Name.First",
        "type": "String"
      },
      {
        "name": "Name.Middle",
        "type": "String"
      },
      {
        "name": "Name.Last",
        "type": "String"
      }
    ],
    "type": "DocumentDbCollection",
    "linkedServiceName": "CosmosDbLinkedService",
    "typeProperties": {
      "collectionName": "Person"
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```
다음 파이프라인은 Azure Blob에서 Cosmos DB의 사용자 컬렉션에 데이터를 복사합니다. 복사 작업의 부분인 입력 및 출력 데이터 세트가 지정되었습니다.

```JSON
{
  "name": "BlobToDocDbPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "DocumentDbCollectionSink",
            "nestingSeparator": ".",
            "writeBatchSize": 2,
            "writeBatchTimeout": "00:00:00"
          },
          "translator": {
              "type": "TabularTranslator",
              "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
          }
        },
        "inputs": [
          {
            "name": "PersonBlobTableIn"
          }
        ],
        "outputs": [
          {
            "name": "PersonCosmosDbTableOut"
          }
        ],
        "policy": {
          "concurrency": 1
        },
        "name": "CopyFromBlobToDocDb"
      }
    ],
    "start": "2015-04-14T00:00:00Z",
    "end": "2015-04-15T00:00:00Z"
  }
}
```
샘플 blob 입력이 인 경우

```
1,John,,Doe
```
Cosmos DB에서 출력 JSON은 다음과 같습니다.

```JSON
{
  "Id": 1,
  "Name": {
    "First": "John",
    "Middle": null,
    "Last": "Doe"
  },
  "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
}
```
Azure Cosmos DB는 중첩된 구조를 허용하는 JSON 문서용 NoSQL 저장소입니다. Azure 데이터 팩터리를 사용하면 **nestingSeparator** 즉, 이 예에서 "."를 통해 계층 구조를 표시할 수 있습니다. 테이블 정의에서 "Name.First", "Name.Middle" 및 "Name.Last"에 따르면 구분 기호를 사용하여 복사 작업이 3개의 자식 요소(처음, 중간 및 마지막)가 있는 "Name" 개체를 생성합니다.

## <a name="appendix"></a>부록
1. **질문:** 복사 작업은 기존 레코드의 업데이트를 지원합니까?

    **대답:** 아니요.
2. **질문:** Azure Cosmos DB로 복사 재시도는 이미 복사된 레코드를 어떻게 처리하나요?

    **대답:** 레코드에 "ID" 필드가 있고 복사 작업이 동일한 ID를 가진 레코드를 삽입하려고 시도하는 경우 복사 작업에서 오류가 발생합니다.
3. **질문:** Data Factory는 [범위 또는 해시 기반 데이터 분할](../../cosmos-db/sql-api-partition-data.md)을 지원합니까?

    **대답:** 아니요.
4. **질문:** 하나의 테이블에 대해 둘 이상의 Azure Cosmos DB 컬렉션을 지정할 수 있습니까?

    **대답:** 아니요. 이 경우 하나의 컬렉션만 지정할 수 있습니다.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.
