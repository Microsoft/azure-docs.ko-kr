---
title: Azure Data Factory를 사용하여 SAP ECC에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 활동을 사용하여 데이터를 SAP ECC에서 지원되는 싱크 데이터 저장소로 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 04/26/2018
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: d86264b632daa09a899fae28e73e117b16322617
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128120"
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SAP ECC에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 활동을 사용하여 SAP ECC(SAP Enterprise Central Component)에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

SAP ECC에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 SAP ECC 커넥터는 다음을 지원합니다.

- SAP NetWeaver 7.0 버전 이상의 SAP ECC에서 데이터를 복사합니다. 
- SAP ECC OData 서비스(예: SAP 테이블/뷰, BAPI, 데이터 추출기 등)에서 공개된 모든 개체의 데이터 또는 상대 어댑터를 통해 OData로 받을 수 있는 SAP PI로 전송된 데이터/IDOC를 복사합니다.
- 기본 인증을 사용하여 데이터를 복사합니다.

## <a name="prerequisites"></a>필수 조건

일반적으로 SAP ECC는 SAP 게이트웨이를 통해 OData 서비스를 통해 엔터티를 공개합니다. 이 SAP ECC 커넥터를 사용하려면 다음을 수행해야 합니다.

- **SAP 게이트웨이 설정**. SAP NetWeaver 버전이 7.4 이상인 서버에는 SAP 게이트웨이가 이미 설치되어 있습니다. 그렇지 않으면 OData 서비스를 통해 SAP ECC 데이터를 공개하기 전에 포함된 게이트웨이 또는 게이트웨이 허브를 설치해야 합니다. [설치 가이드](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm)에서 SAP 게이트웨이를 설치하는 방법을 알아봅니다.

- **SAP OData 서비스 활성화 및 구성**. 수초 내에 TCODE SICF를 통해 OData 서비스를 활성화할 수 있습니다. 공개되어야 하는 개체를 구성할 수도 있습니다. 다음은 [단계별 지침](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/) 샘플입니다.

## <a name="getting-started"></a>시작

.NET SDK, Python SDK, Azure PowerShell, REST API 또는 Azure Resource Manager 템플릿을 사용하여 복사 작업으로 파이프라인을 만들 수 있습니다. 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](quickstart-create-data-factory-dot-net.md)를 참조하세요.

다음 섹션에서는 SAP ECC 커넥터와 관련된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP ECC 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성을 다음으로 설정해야 합니다. **SapEcc** | 예 |
| URL | SAP ECC OData 서비스의 URL입니다. | 예 |
| username | SAP ECC에 연결하는 데 사용되는 사용자 이름입니다. | 아닙니다. |
| 암호 | SAP ECC에 연결하는 데 사용되는 일반 텍스트 암호입니다. | 아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

**예제:**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 SAP ECC 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

SAP ECC에서 데이터를 복사하려면 데이터 세트의 type 속성을 **SapEccResource**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 경로 | SAP ECC OData 엔터티의 경로입니다. | 예 |

**예제**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SAP ECC 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sap-ecc-as-source"></a>SAP ECC를 원본으로 설정

SAP ECC에서 데이터를 복사하려면 복사 활동의 원본 형식을 **SapEccSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **SapEccSource** | 예 |
| 쿼리 | 데이터를 필터링하는 OData 쿼리 옵션입니다. 예제: "$select=Name,Description&$top=10".<br/><br/>SAP ECC 커넥터는 결합된 URL(연결된 서비스에 지정된 URL)/(데이터 세트에 지정된 경로)?(복사 활동 원본에 지정된 쿼리)의 데이터를 복사합니다. [OData URL 구성 요소](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)를 참조하세요. | 아닙니다. |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-ecc"></a>SAP ECC에 대한 데이터 형식 매핑

SAP ECC에서 데이터를 복사하는 경우 SAP ECC 데이터에 대한 OData 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로의 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| OData 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| Edm.Binary | String |
| Edm.Boolean | Bool |
| Edm.Byte | String |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | String |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> 복합 데이터 형식은 이제 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
