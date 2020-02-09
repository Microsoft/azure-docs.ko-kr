---
title: SAP Cloud for Customer 간에 데이터 복사
description: Data Factory를 사용하여 SAP Cloud for Customer에서 지원되는 싱크 데이터 저장소로 데이터를 복사하거나 지원되는 원본 데이터 저장소에서 SAP Cloud for Customer로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: 0b0352632491dbfc46ed4819653c6cb902eee4ce
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74923714"
---
# <a name="copy-data-from-sap-cloud-for-customer-c4c-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SAP Cloud for Customer(C4C) 간에 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SAP Cloud for Customer(C4C) 간에 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!TIP]
>SAP 데이터 통합 시나리오에 대한 ADF의 전반적인 지원에 대한 자세한 내용은 [Azure Data Factory 백서를 사용 하 여 sap 데이터 통합](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) 에서 자세한 소개, comparsion 및 지침을 참조 하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 SAP Cloud for Customer 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

SAP Cloud for Customer에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사하거나 지원되는 모든 원본 데이터 저장소에서 SAP Cloud for Customer로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 커넥터를 사용하면 Azure Data Factory에서 SAP Cloud for Sales, SAP Cloud for Service 및 SAP Cloud for Social Engagement 솔루션을 포함한 SAP Cloud for Customer 간에 데이터를 복사할 수 있습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP Cloud for Customer 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 SAP Cloud for Customer 연결된 서비스에 대해 지원되는 속성입니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **SapCloudForCustomer**로 설정해야 합니다. | yes |
| URL | SAP C4C OData 서비스의 URL입니다. | yes |
| username | SAP C4C에 연결할 사용자 이름을 지정합니다. | yes |
| 암호 | username에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. | 원본에는 아니요이고 싱크에는 예입니다 |

>[!IMPORTANT]
>SAP Cloud for Customer로 데이터를 복사하려면 다음 예제와 같이 SAP Cloud for Customer 근처 위치를 사용하여 명시적으로 [Azure IR을 만들고](create-azure-integration-runtime.md#create-azure-ir) 연결된 서비스에서 연결합니다.

**예제:**

```json
{
    "name": "SAPC4CLinkedService",
    "properties": {
        "type": "SapCloudForCustomer",
        "typeProperties": {
            "url": "https://<tenantname>.crm.ondemand.com/sap/c4c/odata/v1/c4codata/" ,
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 SAP Cloud for Customer 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

SAP Cloud for Customer에서 데이터를 복사하려면 데이터 세트의 type 속성을 **SapCloudForCustomerResource**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성은 **SapCloudForCustomerResource**로 설정해야 합니다. |yes |
| 경로 | SAP C4C OData 엔터티의 경로를 지정합니다. |yes |

**예제:**

```json
{
    "name": "SAPC4CDataset",
    "properties": {
        "type": "SapCloudForCustomerResource",
        "typeProperties": {
            "path": "<path e.g. LeadCollection>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP C4C linked service>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SAP Cloud for Customer 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sap-c4c-as-source"></a>SAP C4C를 원본으로 설정

SAP Cloud for Customer에서 데이터를 복사하려면 복사 작업의 원본 형식을 **SapCloudForCustomerSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **SapCloudForCustomerSource**로 설정해야 합니다.  | yes |
| 쿼리 | 데이터를 읽을 사용자 지정 OData 쿼리를 지정합니다. | 아닙니다. |

특정 날짜에 대한 데이터를 가져오는 샘플 쿼리: `"query": "$filter=CreatedOn ge datetimeoffset'2017-07-31T10:02:06.4202620Z' and CreatedOn le datetimeoffset'2017-08-01T10:02:06.4202620Z'"`

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSAPC4C",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP C4C input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SapCloudForCustomerSource",
                "query": "<custom query e.g. $top=10>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sap-c4c-as-sink"></a>SAP C4C를 싱크로 설정

SAP Cloud for Customer로 데이터를 복사하려면 복사 작업의 싱크 형식을 **SapCloudForCustomerSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **SapCloudForCustomerSink**로 설정해야 합니다.  | yes |
| writeBehavior | 작업의 쓰기 동작입니다. “Insert”, “Update”가 될 수 있습니다. | 아닙니다. 기본값은 “Insert”입니다. |
| writeBatchSize | 쓰기 작업의 일괄 처리 크기입니다. 최상의 성능을 얻기 위한 일괄 처리 크기는 테이블이나 서버에 따라 다를 수 있습니다. | 아닙니다. 기본값은 10입니다. |

**예제:**

```json
"activities":[
    {
        "name": "CopyToSapC4c",
        "type": "Copy",
        "inputs": [{
            "type": "DatasetReference",
            "referenceName": "<dataset type>"
        }],
        "outputs": [{
            "type": "DatasetReference",
            "referenceName": "SapC4cDataset"
        }],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SapCloudForCustomerSink",
                "writeBehavior": "Insert",
                "writeBatchSize": 30
            },
            "parallelCopies": 10,
            "dataIntegrationUnits": 4,
            "enableSkipIncompatibleRow": true,
            "redirectIncompatibleRowSettings": {
                "linkedServiceName": {
                    "referenceName": "ErrorLogBlobLinkedService",
                    "type": "LinkedServiceReference"
                },
                "path": "incompatiblerows"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-cloud-for-customer"></a>SAP Cloud for Customer의 데이터 형식 매핑

SAP Cloud for Customer에서 데이터를 복사하는 경우 SAP Cloud for Customer 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP C4C OData 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | 10진수 |
| Edm.Double | DOUBLE |
| Edm.Single | 단일 |
| Edm.Guid | GUID |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | string |
| Edm.Time | timespan |
| Edm.DateTimeOffset | DateTimeOffset |


## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
