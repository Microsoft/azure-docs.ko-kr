---
title: Dynamics AX에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Dynamics AX에서 지원되는 싱크 데이터 스토리지로 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.date: 08/01/2019
ms.openlocfilehash: 4acad5e2de55211b6c4492513f331c36286ed852
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892791"
---
# <a name="copy-data-from-dynamics-ax-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Dynamics AX에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Dynamics AX 원본에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [Azure Data Factory의 복사 작업](copy-activity-overview.md)을 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Dynamics AX 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Dynamics AX에서 지원되는 모든 싱크 데이터 스토리지로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소의 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

특히 이 Dynamics AX 커넥터는 **서비스 주체 인증**에 **OData 프로토콜**을 사용하여 Dynamics AX에서 데이터 복사를 지원합니다.

>[!TIP]
>또한 이 커넥터를 사용하여 **Dynamics 365 Finance and Operations**에서 데이터를 복사할 수도 있습니다. Dynamics 365의 [OData 지원](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/odata) 및 [인증 방법](https://docs.microsoft.com/dynamics365/unified-operations/dev-itpro/data-entities/services-home-page#authentication)을 참조하세요.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Dynamics AX 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="prerequisites"></a>필수 조건

서비스 주체 인증을 사용하려면 다음 단계를 수행합니다.

1. [Azure AD 테넌트에 애플리케이션 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)에 따라 Azure AD(Azure Active Directory)에 애플리케이션 항목을 등록합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. Dynamics AX로 이동한 다음, 이 서비스 주체에 Dynamics AX에 액세스할 수 있는 적절한 권한을 부여합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Dynamics AX 연결된 서비스에 다음 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | **형식** 속성은 **DynamicsAX**로 설정해야 합니다. |예 |
| url | Dynamics AX(또는 Dynamics 365 Finance and Operations) 인스턴스 OData 엔드포인트입니다. |예 |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. | 예 |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이 필드를 **SecureString**으로 표시하여 Data Factory에 안전하게 저장하거나, [Azure Key Vault에 저장된 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. Azure Portal의 오른쪽 위 모서리를 마우스로 가리켜 검색합니다. | 예 |
| aadResourceId | 권한 부여를 요청하는 AAD 리소스를 지정합니다. 예를 들어 동적 URL가 `https://sampledynamics.sandbox.operations.dynamics.com/data/`이면 그에 해당하는 AAD 리소스는 일반적으로 `https://sampledynamics.sandbox.operations.dynamics.com`입니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 데이터 저장소가 프라이빗 네트워크에 있는 경우, 자체 호스팅 통합 런타임을 선택할 수 있습니다. 지정하지 않으면 기본 Azure Integration Runtime이 사용됩니다. |아닙니다. |

**예제**

```json
{
    "name": "DynamicsAXLinkedService",
    "properties": {
        "type": "DynamicsAX",
        "typeProperties": {
            "url": "<Dynamics AX instance OData endpoint>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource, e.g. https://sampledynamics.sandbox.operations.dynamics.com>"
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}

```

## <a name="dataset-properties"></a>데이터 세트 속성

이 섹션에서는 Dynamics AX 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)를 참조하세요. 

Dynamics AX에서 데이터를 복사하려면 데이터 세트의 **type** 속성을 **DynamicsAXResource**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 **type** 속성을 **DynamicsAXResource**로 설정해야 합니다. | 예 |
| 경로 | Dynamics AX OData 엔터티의 경로입니다. | 예 |

**예제**

```json
{
    "name": "DynamicsAXResourceDataset",
    "properties": {
        "type": "DynamicsAXResource",
        "typeProperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Dynamics AX linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 활동 속성

이 섹션에서는 Dynamics AX 원본에서 지원하는 속성의 목록을 제공합니다.

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조하세요. 

### <a name="dynamics-ax-as-source"></a>원본으로 Dynamics AX

Dynamics AX에서 데이터를 복사하려면 복사 작업의 **source** 형식을 **DynamicsAXSource**로 설정합니다. 복사 작업 **source** 섹션에서 지원되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성을 **DynamicsAXSource**로 설정해야 합니다. | 예 |
| Query | 데이터 필터링에 대한 OData 쿼리 옵션입니다. 예: `"?$select=Name,Description&$top=5"`.<br/><br/>**참고**: 커넥터가 결합된 URL(`[URL specified in linked service]/[path specified in dataset][query specified in copy activity source]`)에서 데이터를 복사합니다. 자세한 내용은 [OData URL 구성 요소](https://www.odata.org/documentation/odata-version-3-0/url-conventions/)를 참조하세요. | 아닙니다. |

**예제**

```json
"activities":[
    {
        "name": "CopyFromDynamicsAX",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics AX input dataset name>",
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
                "type": "DynamicsAXSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.
