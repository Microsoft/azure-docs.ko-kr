---
title: Azure Data Factory를 사용하여 Search 인덱스에 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인에서 복사 작업을 사용하여 Azure Search 인덱스에 데이터를 푸시하거나 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 1c8cbcd2e5f137b1e8381dcce164ae9a4b87e804
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60998475"
---
# <a name="copy-data-to-an-azure-search-index-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Search 인덱스에 데이터 복사

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-azure-search-connector.md)
> * [현재 버전](connector-azure-search.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Azure Search 인덱스로 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

모든 지원되는 원본 데이터 저장소에서 Azure Search 인덱스로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Search 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure Search 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성을 다음으로 설정해야 합니다. **AzureSearch** | 예 |
| url | Azure Search 서비스의 URL입니다. | 예 |
| key | Azure Search 서비스의 관리자 키입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

> [!IMPORTANT]
> 연결 된 서비스, 클라우드 데이터 저장소에서 Azure Search에서 Azure Search 인덱스로 데이터를 복사 하는 경우 Azure Integration Runtime connactVia의 명시적 영역으로 참조 해야 합니다. Azure Search가 있는 지역을 설정합니다. [Azure Integration Runtime](concepts-integration-runtime.md#azure-integration-runtime)에서 자세히 알아봅니다.

**예제:**

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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 세트 문서를 참조하세요. 이 섹션에서는 Azure Search 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Azure Search에 데이터를 복사하려면 데이터 세트의 type 속성을 **RelationalTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **AzureSearchIndex** | 예 |
| indexName | Azure Search 인덱스의 이름입니다. Data Factory는 인덱스를 만들지 않습니다. Azure Search에는 인덱스가 있어야 합니다. | 예 |

**예제:**

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": {
            "referenceName": "<Azure Search linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties" : {
            "indexName": "products"
        }
   }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Azure Search 원본에서 지원하는 속성 목록을 제공합니다.

### <a name="azure-search-as-sink"></a>Azure Search를 싱크로

Azure Search에 데이터를 복사하려면 복사 작업의 원본 형식을 **AzureSearchIndexSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **AzureSearchIndexSink** | 예 |
| writeBehavior | 문서가 인덱스에 이미 있는 경우 병합할지 또는 바꿀지를 지정합니다. [WriteBehavior 속성](#writebehavior-property)을 참조하세요.<br/><br/>허용되는 값은 다음과 같습니다. **Merge**(기본값) 및 **Upload**. | 아닙니다. |
| writeBatchSize | 버퍼 크기가 writeBatchSize에 도달한 경우 Azure Search 인덱스에 데이터를 업로드합니다. 자세한 내용은 [WriteBatchSize 속성](#writebatchsize-property)을 참조하세요.<br/><br/>허용되는 값은 정수 1~1,000이고 기본값은 1,000입니다. | 아닙니다. |

### <a name="writebehavior-property"></a>WriteBehavior 속성

데이터를 쓸 때 AzureSearchSink가 삽입됩니다. 즉, 문서를 작성할 때 문서 키가 Azure Search 인덱스가 이미 있는 경우 Azure Search는 충돌 예외를 throw하지 않는 대신 기존 문서를 업데이트합니다.

AzureSearchSink는 AzureSearch SDK를 사용하여 다음 두 가지 삽입 동작을 제공합니다.

- **병합**: 새 문서의 모든 열을 기존 문서와 결합합니다. 새 문서에서 null 값을 가진 열의 경우 기존 문서의 값이 유지됩니다.
- **업로드**: 새 문서가 기존 문서를 대체합니다. 새 문서에 지정되지 않은 열의 경우 기본 문서에 null이 아닌 값이 있는지 여부에 상관없이 값이 null로 설정됩니다.

기본 동작은 **병합**입니다.

### <a name="writebatchsize-property"></a>writeBatchSize 속성

Azure Search 서비스는 일괄 처리로 문서 작성을 지원합니다. 일괄 처리는 1~1,000개의 동작을 포함할 수 있습니다. 하나의 동작에서 하나의 문서를 처리하여 업로드/병합 작업을 수행합니다.

**예제:**

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
                "referenceName": "<Azure Search output dataset name>",
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

### <a name="data-type-support"></a>데이터 형식 지원

다음 표에서는 Azure Search 데이터 형식이 지원되는지 여부를 지정합니다.

| Azure Search 데이터 형식 | Azure Search 싱크에서 지원됨 |
| ---------------------- | ------------------------------ |
| 문자열 | Y |
| Int32 | Y |
| Int64 | Y |
| Double | Y |
| BOOLEAN | Y |
| DataTimeOffset | Y |
| String Array | N |
| GeographyPoint | N |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
