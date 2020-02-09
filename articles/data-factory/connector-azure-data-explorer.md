---
title: Azure 데이터 탐색기 간에 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Azure Data Explorer로/에서 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: orspodek
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/26/2019
ms.openlocfilehash: 4cc315b91b5dbedcb22091149ca37061ff956efa
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74913422"
---
# <a name="copy-data-to-or-from-azure-data-explorer-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 Azure 데이터 탐색기 간에 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 [Azure 데이터 탐색기](../data-explorer/data-explorer-overview.md)간에 데이터를 복사 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공 하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

>[!TIP]
>일반적인 Azure Data Factory 및 Azure 데이터 탐색기 통합에 대한 자세한 내용은 [azure 데이터 탐색기를 Azure Data Factory와 통합](../data-explorer/data-factory-integration.md)을 통해 자세히 알아보세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 Azure 데이터 탐색기 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

지원되는 모든 원본 데이터 저장소에서 Azure Data Explorer로 데이터를 복사할 수 있습니다. 또한 Azure Data Explorer에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업에서 원본 또는 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조 하세요.

>[!NOTE]
>자체 호스팅 integration runtime을 사용 하 여 온-프레미스 데이터 저장소를 통해 Azure 데이터 탐색기 간에 데이터를 복사 하는 기능은 3.14 이상 버전에서 지원 됩니다.

Azure 데이터 탐색기 커넥터를 사용 하 여 다음을 수행할 수 있습니다.

* **서비스 주체**와 함께 Azure AD(Azure Active Directory) 애플리케이션 토큰 인증을 사용하여 데이터를 복사합니다.
* 원본의 경우 KQL(Kusto) 쿼리를 사용하여 데이터를 검색합니다.
* 싱크의 경우 대상 테이블에 데이터를 추가합니다.

## <a name="getting-started"></a>시작

>[!TIP]
>Azure 데이터 탐색기 커넥터에 대한 연습은 azure [데이터 탐색기에서/로 데이터 복사 Azure Data Factory를 사용 하 여](../data-explorer/data-factory-load-data.md) [데이터베이스에서 Azure 데이터 탐색기로 대량 복사](../data-explorer/data-factory-template.md)를 참조 하세요.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Azure Data Explorer 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Azure 데이터 탐색기 커넥터는 서비스 주체 인증을 사용 합니다. 다음 단계를 수행 하 여 서비스 주체를 가져오고 사용 권한을 부여 합니다.

1. [AZURE AD 테넌트에 응용 프로그램 등록](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant)의 단계를 수행 하 여 Azure Active Directory에 응용 프로그램 엔터티를 등록 합니다. 연결된 서비스를 정의하는 데 사용되므로 다음 값을 적어둡니다.

    - 애플리케이션 UI
    - 애플리케이션 키
    - 테넌트 ID

2. Azure 데이터 탐색기에서 서비스 주체에 게 올바른 권한을 부여 합니다. 역할 및 사용 권한과 사용 권한 관리에 대한 자세한 내용은 [Azure 데이터 탐색기 데이터베이스 사용 권한 관리](../data-explorer/manage-database-permissions.md) 를 참조 하세요. 일반적으로 다음을 수행 해야 합니다.

    - **원본으로**데이터베이스에 적어도 **데이터베이스 뷰어** 역할을 부여 합니다.
    - **싱크로**데이터베이스에 적어도 **데이터베이스 수집기** 역할을 부여 합니다.

>[!NOTE]
>Data Factory UI를 사용 하 여 작성 하는 경우 로그인 사용자 계정은 Azure 데이터 탐색기 클러스터, 데이터베이스 및 테이블을 나열 하는 데 사용 됩니다. 이러한 작업에 대한 권한이 없는 경우 수동으로 이름을 입력 합니다.

Azure 데이터 탐색기 연결 된 서비스에 대해 지원 되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **Type** 속성은 **azuredataexplorer**로 설정 되어야 합니다. | yes |
| endpoint | `https://<clusterName>.<regionName>.kusto.windows.net` 형식의 Azure Data Explorer 클러스터의 엔드포인트 URL입니다. | yes |
| database | 데이터베이스의 이름입니다. | yes |
| tenant | 애플리케이션이 있는 테넌트 정보(도메인 이름 또는 테넌트 ID)를 지정합니다. 이를 [Kusto 연결 문자열](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)에 "Authority ID" 라고 합니다. Azure Portal의 오른쪽 위 모서리에 마우스 포인터를 올려 검색 합니다. | yes |
| servicePrincipalId | 애플리케이션의 클라이언트 ID를 지정합니다. 이를 [Kusto 연결 문자열](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)에서 "AAD 응용 프로그램 클라이언트 ID" 라고 합니다. | yes |
| servicePrincipalKey | 애플리케이션의 키를 지정합니다. 이를 [Kusto 연결 문자열](https://docs.microsoft.com/azure/kusto/api/connection-strings/kusto#application-authentication-properties)에서 "AAD 응용 프로그램 키" 라고 합니다. 이 필드를 **SecureString** 으로 표시 하 여 Data Factory에 안전 하 게 저장 하거나 [Azure Key Vault에 저장 된 보안 데이터를 참조](store-credentials-in-key-vault.md)합니다. | yes |

**연결 된 서비스 속성 예:**

```json
{
    "name": "AzureDataExplorerLinkedService",
    "properties": {
        "type": "AzureDataExplorer",
        "typeProperties": {
            "endpoint": "https://<clusterName>.<regionName>.kusto.windows.net ",
            "database": "<database name>",
            "tenant": "<tenant name/id e.g. microsoft.onmicrosoft.com>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 집합을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [Azure Data Factory의 데이터 집합](concepts-datasets-linked-services.md)을 참조 하세요. 이 섹션에서는 Azure 데이터 탐색기 데이터 집합에서 지 원하는 속성을 나열 합니다.

Azure Data Explorer에 데이터를 복사하려면 데이터 세트의 형식 속성을 **AzureDataExplorerTable**로 설정합니다.

다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | **Type** 속성은 **AzureDataExplorerTable**로 설정 해야 합니다. | yes |
| 테이블 | 연결된 서비스가 참조하는 테이블의 이름입니다. | 싱크의 경우 예이며, 원본의 경우 아니오입니다. |

**데이터 집합 속성 예:**

```json
{
   "name": "AzureDataExplorerDataset",
    "properties": {
        "type": "AzureDataExplorerTable",
        "typeProperties": {
            "table": "<table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Azure Data Explorer linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

활동을 정의 하는 데 사용할 수 있는 섹션 및 속성의 전체 목록은 [Azure Data Factory의 파이프라인 및 활동](concepts-pipelines-activities.md)을 참조 하세요. 이 섹션에서는 Azure 데이터 탐색기 원본 및 싱크에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="azure-data-explorer-as-source"></a>Azure Data Explorer가 원본인 경우

Azure Data Explorer에서 데이터를 복사하려면 복사 작업 원본의 **형식** 속성을 **AzureDataExplorerSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 **type** 속성은 **AzureDataExplorerSource** 로 설정 해야 합니다. | yes |
| 쿼리 | [KQL 형식](/azure/kusto/query/)으로 제공되는 읽기 전용 요청입니다. 사용자 지정 KQL 쿼리를 참조로 사용합니다. | yes |
| queryTimeout | 쿼리 요청 시간이 초과 되기 전의 대기 시간입니다. 기본값은 10 분 (00:10:00);입니다. 허용 되는 최대 값은 1 시간 (01:00:00)입니다. | 아닙니다. |
| noTruncation | 반환 된 결과 집합을 잘라낼 지 여부를 나타냅니다. 기본적으로 50만 레코드 또는 64 메가바이트 (MB) 후에 결과가 잘립니다. 자르기는 활동의 올바른 동작을 보장 하는 데 강력히 권장 됩니다. |아닙니다. |

>[!NOTE]
>기본적으로 Azure 데이터 탐색기 원본의 크기 제한은 50만 레코드 또는 64 MB입니다. 잘림 없이 모든 레코드를 검색 하려면 쿼리의 시작 부분에 `set notruncation;` 지정할 수 있습니다. 자세한 내용은 [쿼리 제한](https://docs.microsoft.com/azure/kusto/concepts/querylimits)을 참조 하세요.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "AzureDataExplorerSource",
                "query": "TestTable1 | take 10",
                "queryTimeout": "00:10:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        },
        "inputs": [
            {
                "referenceName": "<Azure Data Explorer input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

### <a name="azure-data-explorer-as-sink"></a>Azure Data Explorer가 싱크인 경우

Azure Data Explorer로 데이터를 복사하려면 복사 작업 원본의 형식 속성을 **AzureDataExplorerSink**로 설정합니다. 복사 작업 **sink** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 싱크의 **type** 속성은 **AzureDataExplorerSink**로 설정 해야 합니다. | yes |
| ingestionMappingName | Kusto 테이블에서 미리 생성 된 [매핑의](/azure/kusto/management/mappings#csv-mapping) 이름입니다. 원본에서 Azure 데이터 탐색기로 (CSV/JSON/Avro 형식을 포함 하 여 지원 되는 [모든 원본 저장소 및 형식](copy-activity-overview.md#supported-data-stores-and-formats)에 적용 됨) 열을 매핑하려면 복사 작업 [열 매핑](copy-activity-schema-and-type-mapping.md) (이름으로 암시적으로 또는 구성 된 경우 명시적으로) 및/또는 Azure 데이터 탐색기 매핑을 사용할 수 있습니다. | 아닙니다. |

**예제:**

```json
"activities":[
    {
        "name": "CopyToAzureDataExplorer",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureDataExplorerSink",
                "ingestionMappingName": "<optional Azure Data Explorer mapping name>"
            }
        },
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure Data Explorer output dataset name>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용은 [조회 작업](control-flow-lookup-activity.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조 하세요.

* [Azure Data Factory에서 Azure 데이터 탐색기로 데이터를 복사](/azure/data-explorer/data-factory-load-data)하는 방법에 대해 자세히 알아보세요.
