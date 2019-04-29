---
title: Azure Data Factory를 사용하여 MongoDB에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Mongo DB에서 지원되는 싱크 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 12/20/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: ca6040bb74839f30a2f1b13297f6037f05240c67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400443"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Azure Data Factory를 사용하여 MongoDB에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 MongoDB 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!IMPORTANT]
>ADF는 더 나은 네이티브 MongoDB 지원을 제공하는 이 새 버전의 MongoDB 커넥터를 릴리스합니다. 이전 버전과 호환성에 대해 있는 그대로 지원되는 솔루션에서 이전 버전의 MongoDB 커넥터를 사용하는 경우 [MongoDB 커넥터(레거시)](connector-mongodb-legacy.md) 문서를 참조하세요.

## <a name="supported-capabilities"></a>지원되는 기능

MongoDB 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 MongoDB 커넥터는 **버전 3.4까지** 지원합니다.

## <a name="prerequisites"></a>필수 조건

공개적으로 액세스할 수 없는 MongoDB 데이터베이스에서 데이터를 복사하려면 자체 호스팅 통합 런타임을 설정해야 합니다. 자세한 내용은 [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md) 문서를 참조하세요.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 MongoDB 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

MongoDB 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 |type 속성을 다음으로 설정해야 합니다. **MongoDbV2** |예 |
| connectionString |MongoDB 연결 문자열을 지정합니다(예: `mongodb://[username:password@]host[:port][/[database][?options]]`). 자세한 내용은 [연결 문자열에 대한 MongoDB 설명서](https://docs.mongodb.com/manual/reference/connection-string/)를 참조하세요. <br/><br />이 필드를 **SecureString** 형식으로 표시하여 Data Factory에서 안전하게 저장합니다. [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. |예 |
| 데이터베이스 | 액세스하려는 데이터베이스 이름입니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

**예제:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDbV2",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "mongodb://[username:password@]host[:port][/[database][?options]]"
            },
            "database": "myDatabase"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. MongoDB 데이터 세트에 대해 다음 속성을 지원합니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **MongoDbV2Collection** | 예 |
| collectionName |MongoDB 데이터베이스에 있는 컬렉션의 이름입니다. |예 |

**예제:**

```json
{
    "name": "MongoDbDataset",
    "properties": {
        "type": "MongoDbV2Collection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 MongoDB 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="mongodb-as-source"></a>MongoDB를 원본으로

복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **MongoDbV2Source** | 예 |
| filter | 쿼리 연산자를 사용하여 선택 영역 필터를 지정합니다. 컬렉션의 모든 문서를 반환하려면 이 매개 변수를 생략하거나 빈 문서({})를 전달합니다. | 아닙니다. |
| cursorMethods.project | 프로젝션에 대한 문서에서 반환할 필드를 지정합니다. 일치하는 문서에서 모든 필드를 반환하려면 이 매개 변수를 생략합니다. | 아닙니다. |
| cursorMethods.sort | 쿼리가 일치하는 문서를 반환하는 순서를 지정합니다. [cursor.sort()](https://docs.mongodb.com/manual/reference/method/cursor.sort/#cursor.sort)를 참조하세요. | 아닙니다. |
| cursorMethods.limit | 서버에서 반환하는 문서의 최대 수를 지정합니다. [cursor.limit()](https://docs.mongodb.com/manual/reference/method/cursor.limit/#cursor.limit)를 참조하세요.  | 아닙니다. |
| cursorMethods.skip | MongoDB가 결과를 반환하기 시작하는 위치에서 건너뛸 문서 수를 지정합니다. [cursor.skip()](https://docs.mongodb.com/manual/reference/method/cursor.skip/#cursor.skip)을 참조하세요. | 아닙니다. |
| batchSize | MongoDB 인스턴스의 응답을 각각 일괄 처리로 반환할 문서 수를 지정합니다. 대부분의 경우 일괄 처리 크기를 수정해도 사용자 또는 애플리케이션에 영향이 없습니다. Cosmos DB는 각 일괄 처리가 문서 크기의 batchSize 수의 합인 40MB를 초과할 수 없도록 제한하므로 문서 크기가 대규모인 경우 이 값을 줄입니다. | 아닙니다.<br/>(기본값은 **100**) |

>[!TIP]
>ADF는 **Strict 모드**로 BSON 문서를 사용하는 것을 지원합니다. 필터 쿼리가 Shell 모드가 아닌 Strict 모드에 있는지 확인합니다. 자세한 설명은 [MongoDB 설명서](https://docs.mongodb.com/manual/reference/mongodb-extended-json/index.html)를 참조하세요.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
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
                "type": "MongoDbV2Source",
                "filter": "{datetimeData: {$gte: ISODate(\"2018-12-11T00:00:00.000Z\"),$lt: ISODate(\"2018-12-12T00:00:00.000Z\")}, _id: ObjectId(\"5acd7c3d0000000000000000\") }",
                "cursorMethods": {
                    "project": "{ _id : 1, name : 1, age: 1, datetimeData: 1 }",
                    "sort": "{ age : 1 }",
                    "skip": 3,
                    "limit": 3
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="export-json-documents-as-is"></a>JSON 문서를 있는 그대로 내보내기

이 MongoDB 커넥터를 사용하면 JSON 문서를 있는 그대로 MongoDB 컬렉션에서 다양한 파일 기반 저장소로 또는 Azure Cosmos DB로 내보낼 수 있습니다. 이러한 스키마 독립적 복사를 완수하려면 데이터 세트 및 복사 작업의 스키마 매핑에서 "structure"(*스키마*라고도 함) 섹션을 건너뜁니다.

## <a name="schema-mapping"></a>스키마 매핑

데이터를 MongoDB에서 테이블 형식 싱크로 복사하려면 [스키마 매핑](copy-activity-schema-and-type-mapping.md#schema-mapping)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
