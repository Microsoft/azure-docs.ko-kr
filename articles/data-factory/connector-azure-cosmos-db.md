---
title: Data Factory를 사용하여 Azure Cosmos DB(SQL API) 간 데이터 복사 | Microsoft Docs
description: Data Factory를 사용하여 지원되는 원본 데이터 저장소에서 Azure Cosmos DB(SQL API)로 또는 Azure Cosmos DB(SQL API)에서 지원되는 싱크 저장소로 데이터를 복사하는 방법을 알아봅니다.
services: data-factory, cosmosdb
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: multiple
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: eca5e4cc96996c35e7c2181746cdb3de2e5a602c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61259520"
---
# <a name="copy-data-to-or-from-azure-cosmos-db-sql-api-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Cosmos DB(SQL API) 간 데이터 복사

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-azure-documentdb-connector.md)
> * [현재 버전](connector-azure-cosmos-db.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Cosmos DB(SQL API) 간 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 기반으로 합니다.

>[!NOTE]
>이 커넥터는 Cosmos DB SQL API 간 데이터 복사만을 지원합니다. MongoDB API의 경우 [Azure Cosmos DB의 API for MongoDB용 커넥터](connector-azure-cosmos-db-mongodb-api.md)를 참조하세요. 다른 API 형식은 이제 지원되지 않습니다.

## <a name="supported-capabilities"></a>지원되는 기능

Azure Cosmos DB(SQL API)에서 모든 지원되는 싱크 데이터 저장소로 또는 모든 지원되는 원본 데이터 저장소에서 Azure Cosmos DB(SQL API)로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

Azure Cosmos DB(SQL API) 커넥터를 사용하여 다음을 수행할 수 있습니다.

- Azure Cosmos DB [SQL API](https://docs.microsoft.com/azure/cosmos-db/documentdb-introduction) 간에 데이터를 복사합니다.
- **insert** 또는 **upsert**로 Azure Cosmos DB에 씁니다.
- JSON 문서를 있는 그대로 가져오고 내보내거나 데이터를 테이블 형식 데이터 세트 간에 복사합니다. 예로는 SQL 데이터베이스 및 CSV 파일이 있습니다. JSON 파일 간 또는 다른 Azure Cosmos DB 컬렉션 간에 문서를 있는 그대로 복사하려면 JSON 문서 가져오기 또는 내보내기를 참조하세요.

Data Factory는 Azure Cosmos DB에 쓸 때 최상의 성능을 제공하기 위해 [Azure Cosmos DB 대량 실행기 라이브러리](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)와 통합됩니다.

> [!TIP]
> [데이터 마이그레이션 동영상](https://youtu.be/5-SRNiC_qOU)에서는 Azure Blob Storage에서 Azure Cosmos DB로 데이터를 복사하는 단계를 안내합니다. 이 동영상에서는 또한 일반적으로 Azure Cosmos DB에 데이터를 수집하기 위한 성능 조정 고려 사항도 설명합니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Cosmos DB(SQL API)에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Cosmos DB(SQL API) 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | **type** 속성은 **CosmosDb**로 설정해야 합니다. | 예 |
| connectionString |Azure Cosmos DB 데이터베이스에 연결하는 데 필요한 정보를 지정합니다.<br />**참고**: 다음 예제에 표시된 대로 연결 문자열에 데이터베이스 정보를 지정해야 합니다. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. Azure Key Vault에 계정 키를 넣고, 연결 문자열에서 `accountKey` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 데이터 저장소가 개인 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 사용할 수 있습니다. 이 속성을 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |아닙니다. |

**예제**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 계정 키 저장**

```json
{
    "name": "CosmosDbSQLAPILinkedService",
    "properties": {
        "type": "CosmosDb",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "AccountEndpoint=<EndpointUrl>;Database=<Database>"
            },
            "accountKey": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

이 섹션에서는 Azure Cosmos DB(SQL API) 데이터 세트에서 지원하는 속성 목록을 제공합니다. 

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. 

Azure Cosmos DB(SQL API) 간 데이터를 복사하려면 데이터 세트의 **type** 속성을 **DocumentDbCollection**으로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 세트의 **type** 속성을 **DocumentDbCollection**으로 설정해야 합니다. |예 |
| collectionName |Azure Cosmos DB 문서 컬렉션의 이름입니다. |예 |

**예제**

```json
{
    "name": "CosmosDbSQLAPIDataset",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName":{
            "referenceName": "<Azure Cosmos DB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<collection name>"
        }
    }
}
```

### <a name="schema-by-data-factory"></a>Data Factory에서의 스키마

Azure Cosmos DB와 같은 스키마 없는 데이터 저장소의 경우 복사 작업은 다음 목록에 설명한 방법 중 하나로 스키마를 유추합니다. [JSON 문서를 있는 그대로 가져오기/내보내기](#import-or-export-json-documents)하려는 경우가 아닌 한 **구조** 섹션에서 데이터의 구조를 지정하는 것이 좋습니다.

* 데이터 세트 정의에서 **structure** 속성을 사용하여 데이터 구조를 지정하는 경우 Data Factory는 이 구조를 스키마로 인식합니다. 

    행에 열 값이 포함되어 있지 않으면 열 값을 위해 null 값이 제공됩니다.
* 데이터 세트 정의에서 **structure** 속성을 사용하여 데이터의 구조를 지정하지 않는 경우 Data Factory 서비스는 데이터의 첫 번째 행을 사용하여 스키마를 유추합니다. 

    첫 번째 행에 전체 스키마가 포함되어 있지 않으면 일부 열이 복사 작업의 결과에서 누락됩니다.

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 Azure Cosmos DB(SQL API) 원본 및 싱크에서 지원하는 속성 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요.

### <a name="azure-cosmos-db-sql-api-as-source"></a>Azure Cosmos DB(SQL API) 원본

Azure Cosmos DB(SQL API)에서 데이터를 복사하려면 복사 작업의 **source** 형식을 **DocumentDbCollectionSource**로 설정합니다. 

복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 **type** 속성을 **DocumentDbCollectionSource**로 설정해야 합니다. |예 |
| 쿼리 |데이터를 읽는 Azure Cosmos DB 쿼리를 지정합니다.<br/><br/>예제:<br /> `SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |아닙니다. <br/><br/>지정하지 않는 경우 실행되는 SQL 문: `select <columns defined in structure> from mycollection` |
| nestingSeparator |문서가 중첩되고 결과 집합을 평면화하는 방법을 나타내는 특수 문자입니다.<br/><br/>예를 들어, Azure Cosmos DB 쿼리가 중첩된 결과 `"Name": {"First": "John"}`을 반환하는 경우 복사 작업은 **nestedSeparator** 값이 **.**(점)인 경우 “John”이라는 값으로 `Name.First`로 열 이름을 식별합니다. |아닙니다.<br />(기본값: **.** (점)) |

**예제**

```json
"activities":[
    {
        "name": "CopyFromCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Cosmos DB SQL API input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT c.BusinessEntityID, c.Name.First AS FirstName, c.Name.Middle AS MiddleName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\""
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="azure-cosmos-db-sql-api-as-sink"></a>Azure Cosmos DB(SQL API) 싱크

Azure Cosmos DB(SQL API)로 데이터를 복사하려면 복사 작업의 **sink** 형식을 **DocumentDbCollectionSink**로 설정합니다. 

복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 싱크의 **type** 속성을 **DocumentDbCollectionSink**로 설정해야 합니다. |예 |
| writeBehavior |Azure Cosmos DB에 데이터를 쓰는 방법을 설명합니다. 허용되는 값은 **insert** 및 **upsert**입니다.<br/><br/>**upsert**의 동작은 동일한 ID의 문서가 이미 존재하는 경우 문서를 바꾸는 것이며, 존재하지 않는 경우 문서를 삽입하는 것입니다.<br /><br />**참고**: ID가 원래 문서 또는 열 매핑에 지정되지 않은 경우 Data Factory는 문서에 대한 ID를 자동으로 생성합니다. 즉, **upsert**가 예상대로 작동하려면 문서에 ID가 있는지 확인해야 합니다. |아닙니다.<br />(기본값: **insert**) |
| writeBatchSize | Data Factory는 [Azure Cosmos DB 대량 실행기 라이브러리](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)를 사용하여 Azure Cosmos DB에 데이터를 씁니다. **writeBatchSize** 속성은 ADF가 라이브러리에 제공하는 문서의 크기를 제어합니다. 성능을 개선하기 위해 **writeBatchSize**에 대한 값을 늘리고 문서 크기가 커지는 경우 값을 줄이도록 시도할 수 있습니다. 아래 팁을 참조하세요. |아닙니다.<br />(기본값: **10,000**) |
| nestingSeparator |중첩된 해당 문서를 나타내는 **source** 열 이름에 특수 문자가 필요합니다. <br/><br/>예를 들어, 출력 데이터 세트 구조에서 `Name.First`는 **nestedSeparator**가 **.** (점)인 경우 Cosmos DB 문서에서 다음 JSON 구조를 생성합니다. `"Name": {"First": "[value maps to this column from source]"}`  |아닙니다.<br />(기본값: **.** (점)) |

>[!TIP]
>Cosmos DB는 단일 요청의 크기를 2MB로 제한합니다. 수식은 요청 크기 = 단일 문서 크기 * 쓰기 일괄 처리 크기입니다. **"요청 크기가 너무 큽니다."** 라는 오류가 발생하는 경우 **복사 싱크 구성에서 `writeBatchSize` 값**을 줄입니다.

**예제**

```json
"activities":[
    {
        "name": "CopyToCosmosDBSQLAPI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Document DB output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DocumentDbCollectionSink",
                "writeBehavior": "upsert"
            }
        }
    }
]
```

## <a name="import-or-export-json-documents"></a>JSON 문서 가져오기 또는 내보내기

이 Azure Cosmos DB(SQL API) 커넥터를 사용하여 손쉽게 다음을 수행할 수 있습니다.

* Azure Blob Storage, Azure Data Lake Store 및 기타 Azure Data Factory에서 지원하는 파일 기반 저장소 등 다양한 원본에서 Azure Cosmos DB로 JSON 문서 가져오기
* Azure Cosmos DB 컬렉션에서 다양한 파일 기반 저장소로 JSON 문서 내보내기
* 두 Azure Cosmos DB 컬렉션 간에 문서를 있는 그대로 복사

스키마 중립적 복사를 수행하려면 다음을 수행합니다.

* 데이터 복사 도구를 사용하는 경우, **JSON 파일 또는 Cosmos DB 컬렉션으로 있는 그대로 내보내기** 옵션을 선택합니다.
* 작업 작성을 사용하는 경우 Azure Cosmos DB 데이터 세트에서 **structure**(schema라고도 함) 섹션을 지정하지 마세요. 또한 Azure Cosmos DB 원본 또는 복사 작업의 싱크에서 **nestingSeparator** 속성을 지정하지 마세요. JSON 파일에서 가져오거나 파일로 내보낼 때 [JSON 형식](supported-file-formats-and-compression-codecs.md#json-format) 섹션의 설명대로 해당 파일 저장소 데이터 세트에서 **format** 형식을 **JsonFormat**으로 지정하고 **filePattern**을 구성합니다. 그런 다음 **structure** 섹션을 지정하지 않고 나머지 형식 설정을 건너뜁니다.

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
