---
title: 검색 인덱스로 데이터 복사
description: Azure Data Factory 파이프라인에서 복사 작업을 사용하여 Azure Search 인덱스에 데이터를 푸시하거나 복사하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/13/2019
ms.openlocfilehash: 418026d5569cd7e4a7c5239f99650833b1b9514d
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892931"
---
# <a name="copy-data-to-an-azure-cognitive-search-index-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure Cognitive Search 인덱스에 데이터 복사

> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-azure-search-connector.md)
> * [현재 버전](connector-azure-search.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 Azure Cognitive Search 인덱스로 데이터를 복사 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

지원 되는 모든 원본 데이터 저장소의 데이터를 검색 인덱스로 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Cognitive Search 커넥터에 한정 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Cognitive Search 연결 된 서비스에 대해 지원 되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **AzureSearch**로 설정되어야 합니다. | 예 |
| url | 검색 서비스에 대한 URL입니다. | 예 |
| key | 검색 서비스에 대한 관리 키입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 프라이빗 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

> [!IMPORTANT]
> 클라우드 데이터 저장소에서 검색 인덱스로 데이터를 복사 하는 경우 Azure Cognitive Search 연결 된 서비스에서에 명시적 지역이 있는 Azure Integration Runtime을 참조 해야 합니다. 검색 서비스가 있는 영역으로 영역을 설정 합니다. [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)에서 자세히 알아봅니다.

**예:**

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": {
                "type": "SecureString",
                "value": "<AdminKey>"
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Azure Cognitive Search 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

Azure Cognitive Search에 데이터를 복사 하기 위해 지원 되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **AzureSearchIndex**로 설정해야 합니다. | 예 |
| indexName | 검색 인덱스의 이름입니다. Data Factory는 인덱스를 만들지 않습니다. 인덱스는 Azure Cognitive Search에 있어야 합니다. | 예 |

**예:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "typeProperties" : {
            "indexName": "products"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Cognitive Search linked service name>",
            "type": "LinkedServiceReference"
        }
   }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Cognitive Search 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="azure-cognitive-search-as-sink"></a>싱크로 Azure Cognitive Search

Azure Cognitive Search에 데이터를 복사 하려면 복사 작업의 원본 형식을 **Azuresearchindexsink**로 설정 합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 **AzureSearchIndexSink**로 설정해야 합니다. | 예 |
| writeBehavior | 문서가 인덱스에 이미 있는 경우 병합할지 또는 바꿀지를 지정합니다. [WriteBehavior 속성](#writebehavior-property)을 참조하세요.<br/><br/>허용되는 값은 **Merge**(기본값) 및 **Upload**입니다. | 아닙니다. |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달할 때 검색 인덱스에 데이터를 업로드 합니다. 자세한 내용은 [WriteBatchSize 속성](#writebatchsize-property)을 참조하세요.<br/><br/>허용되는 값은 정수 1~1,000이고 기본값은 1,000입니다. | 아닙니다. |

### <a name="writebehavior-property"></a>WriteBehavior 속성

데이터를 쓸 때 AzureSearchSink가 삽입됩니다. 즉, 문서를 작성할 때 문서 키가 검색 인덱스에 이미 있는 경우 Azure Cognitive Search는 충돌 예외를 throw 하는 대신 기존 문서를 업데이트 합니다.

AzureSearchSink는 AzureSearch SDK를 사용하여 다음 두 가지 삽입 동작을 제공합니다.

- **병합**: 새 문서의 모든 열을 기존 문서와 결합합니다. 새 문서에서 null 값을 가진 열의 경우 기존 문서의 값이 유지됩니다.
- **업로드**: 새 문서가 기존 문서를 대체합니다. 새 문서에 지정되지 않은 열의 경우 기본 문서에 null이 아닌 값이 있는지 여부에 상관없이 값이 null로 설정됩니다.

기본 동작은 **병합**입니다.

### <a name="writebatchsize-property"></a>writeBatchSize 속성

Azure Cognitive Search 서비스는 문서를 일괄 처리로 작성할 수 있도록 지원 합니다. 일괄 처리는 1~1,000개의 동작을 포함할 수 있습니다. 하나의 동작에서 하나의 문서를 처리하여 업로드/병합 작업을 수행합니다.

**예:**

```json
"activities":[
    {
        "name": "CopyToAzureSearch",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Cognitive Search output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSearchIndexSink",
                "writeBehavior": "Merge"
            }
        }
    }
]
```

## <a name="data-type-support"></a>데이터 형식 지원

다음 표에서는 Azure Cognitive Search 데이터 형식이 지원 되는지 여부를 지정 합니다.

| Azure Cognitive Search 데이터 형식 | Azure Cognitive Search 싱크에서 지원 됨 |
| ---------------------- | ------------------------------ |
| String | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| 부울 | Y |
| DataTimeOffset | Y |
| 문자열 배열 | N |
| GeographyPoint | N |

현재 다른 데이터 형식 (예: ComplexType)은 지원 되지 않습니다. Azure Cognitive Search 지원 되는 데이터 형식에 대한 전체 목록은 [지원 되는 데이터 형식 (Azure Cognitive Search)](https://docs.microsoft.com/rest/api/searchservice/supported-data-types)을 참조 하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
