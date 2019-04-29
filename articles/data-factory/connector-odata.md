---
title: Azure Data Factory를 사용하여 OData 원본에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 OData 원본에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: jingwang
ms.openlocfilehash: eeab0311519d7e3410ddb625c1801101ad63ad18
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400117"
---
# <a name="copy-data-from-an-odata-source-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 OData 원본에서 데이터 복사

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-odata-connector.md)
> * [현재 버전](connector-odata.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 OData 원본에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

OData 소스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

특히 이 OData 커넥터는 다음을 지원합니다.

- OData 버전 3.0 및 4.0
- 인증 방법인 **익명**, **기본**, **Windows**, **AAD 서비스 주체** 및 **Azure 리소스의 관리 ID** 중 하나를 사용하여 데이터 복사

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 OData 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

OData 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | **형식** 속성은 **OData**로 설정해야 합니다. |예 |
| URL | OData 서비스의 루트 URL입니다. |예 |
| authenticationType | OData 원본에 연결하는 데 사용되는 인증 형식입니다. 허용되는 값은 **Anonymous**, **Basic**, **Windows**, **AadServicePrincipal** 및 **ManagedServiceIdentity**입니다. 사용자 기반 OAuth는 지원되지 않습니다. | 예 |
| userName | Basic 또는 Windows 인증을 사용할 경우 **userName**을 지정합니다. | 아닙니다. |
| 암호 | **userName**에 지정한 사용자 계정의 **password**를 지정합니다. 이 필드를 **SecureString** 형식으로 표시하여 Data Factory에서 안전하게 저장합니다. 또한 [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)할 수도 있습니다. | 아닙니다. |
| servicePrincipalId | Azure Active Directory 애플리케이션의 클라이언트 ID를 지정합니다. | 아닙니다. |
| aadServicePrincipalCredentialType | 서비스 주체 인증에 사용할 자격 증명 유형을 지정합니다. 허용되는 값은 `ServicePrincipalKey` 또는 `ServicePrincipalCert`입니다. | 아닙니다. |
| servicePrincipalKey | Azure Active Directory 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 아닙니다. |
| servicePrincipalEmbeddedCert | Azure Active Directory에 등록된 애플리케이션의 base64로 인코딩된 인증서를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 아닙니다. |
| servicePrincipalEmbeddedCertPassword | 인증서가 암호로 보호되는 경우 인증서의 암호를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다.  | 아닙니다.|
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 아닙니다. |
| aadResourceId | 권한 부여를 요청하는 AAD 리소스를 지정합니다.| 아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 데이터 저장소가 개인 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 선택할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |아닙니다. |

**예제 1: 익명 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "https://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제 2: 기본 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Basic",
            "userName": "<user name>",
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

**예제 3: Windows 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "Windows",
            "userName": "<domain>\\<user>",
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

**예제 4: 서비스 주체 키 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalKey",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

**예제 5: 서비스 주체 인증서 인증 사용**

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of OData source>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "aadServicePrincipalCredentialType": "ServicePrincipalCert",
            "servicePrincipalEmbeddedCert": { 
                "type": "SecureString", 
                "value": "<base64 encoded string of (.pfx) certificate data>"
            },
            "servicePrincipalEmbeddedCertPassword": { 
                "type": "SecureString", 
                "value": "<password of your certificate>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource e.g. https://tenant.sharepoint.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

이 섹션에서는 OData 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. 

OData에서 데이터를 복사하려면 데이터 세트의 **type** 속성을 **ODataResource**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 데이터 세트의 **type** 속성을 **ODataResource**로 설정해야 합니다. | 예 |
| 경로 | OData 리소스에 대한 경로입니다. | 예 |

**예제**

```json
{
    "name": "ODataDataset",
    "properties":
    {
        "type": "ODataResource",
        "linkedServiceName": {
            "referenceName": "<OData linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "path": "Products"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 OData 원본에서 지원하는 속성의 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="odata-as-source"></a>OData를 원본으로

OData에서 데이터를 복사하려면 복사 작업의 **source** 형식을 **RelationalSource**로 설정합니다. 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 복사 작업 원본의 **type** 속성을 **RelationalSource**로 설정해야 합니다. | 예 |
| 쿼리 | 데이터 필터링에 대한 OData 쿼리 옵션입니다. 예: `"?$select=Name,Description&$top=5"`.<br/><br/>**참고**: OData 커넥터가 결합된 URL(`[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`)에서 데이터를 복사합니다. 자세한 내용은 [OData URL 구성 요소](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)를 참조하세요. | 아닙니다. |

**예제**

```json
"activities":[
    {
        "name": "CopyFromOData",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<OData input dataset name>",
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
                "type": "RelationalSource",
                "query": "?$select=Name,Description&$top=5"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-odata"></a>OData에 대한 데이터 형식 매핑

OData에서 데이터를 복사하는 경우 OData 데이터 형식과 Azure Data Factory 중간 데이터 형식 사이에서 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법을 알아보려면 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| OData 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| Edm.Binary | Byte[] |
| Edm.Boolean | Bool |
| Edm.Byte | Byte[] |
| Edm.DateTime | DateTime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | Guid |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | String |
| Edm.Time | TimeSpan |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> OData 복합 데이터 형식(예: **Object**)은 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md##supported-data-stores-and-formats)을 참조하세요.