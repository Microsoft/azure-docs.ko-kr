---
title: Azure Data Factory를 사용 하 여 IBM Informix 원본에서 데이터 복사
description: Azure Data Factory 파이프라인에서 복사 작업을 사용 하 여 IBM Informix 원본에서 지원 되는 싱크 데이터 저장소로 데이터를 복사 하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: cb4b81f7c5e219c520078ecf34eba3f5e98da684
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/11/2020
ms.locfileid: "75892585"
---
# <a name="copy-data-from-and-to-ibm-informix-data-stores-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 IBM Informix 데이터 저장소 간 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 IBM Informix 데이터 저장소에서 데이터를 복사 하는 방법을 설명 합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Informix 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

Informix 원본에서 지원 되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 Informix 커넥터를 사용 하려면 다음을 수행 해야 합니다.

- 자체 호스팅 Integration Runtime을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.
- Integration Runtime 컴퓨터에서 데이터 저장소에 대한 Informix ODBC 드라이버를 설치 합니다. 예를 들어 "IBM INFORMIX Informix DRIVER (64 비트)" 드라이버를 사용할 수 있습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Informix 커넥터에 한정 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Informix 연결 된 서비스에 대해 지원 되는 속성입니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | Type 속성은 **Informix** 로 설정 해야 합니다. | 예 |
| connectionString | 자격 증명 부분을 제외한 ODBC 연결 문자열입니다. 연결 문자열을 지정 하거나 Integration Runtime 컴퓨터에서 설정한 시스템 DSN (데이터 원본 이름)을 사용할 수 있습니다 .이에 따라 연결 된 서비스에서 자격 증명 부분을 계속 지정 해야 합니다. <br> Azure Key Vault에 암호를 입력 하 고 `password` 구성을 연결 문자열 외부로 끌어올 수도 있습니다. 자세한 내용은 [Azure Key Vault 에 자격 증명 저장](store-credentials-in-key-vault.md) 을 참조 하세요.| 예 |
| authenticationType | Informix 데이터 저장소에 연결 하는 데 사용 되는 인증 유형입니다.<br/>허용되는 값은 **Basic** 및 **Anonymous**입니다. | 예 |
| userName | 기본 인증을 사용하는 경우 사용자 이름을 지정합니다. | 아닙니다. |
| password | userName에 지정한 사용자 계정의 암호를 지정합니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 아닙니다. |
| 자격 증명(credential) | 드라이버 관련 속성 값 형식에 지정된 연결 문자열의 액세스 자격 증명 부분입니다. 이 필드를 SecureString으로 표시합니다. | 아닙니다. |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예 |

**예:**

```json
{
    "name": "InformixLinkedService",
    "properties": {
        "type": "Informix",
        "typeProperties": {
            "connectionString": "<Informix connection string or DSN>",
            "authenticationType": "Basic",
            "userName": "<username>",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Informix 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

Informix에서 데이터를 복사 하기 위해 지원 되는 속성은 다음과 같습니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **InformixTable** 로 설정 해야 합니다. | 예 |
| tableName | Informix에 있는 테이블의 이름입니다. | 원본: 아니요(작업 원본에서 "query"가 지정된 경우)<br/>싱크: 예 |

**예제**

```json
{
    "name": "InformixDataset",
    "properties": {
        "type": "InformixTable",
        "linkedServiceName": {
            "referenceName": "<Informix linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "<table name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Informix 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="informix-as-source"></a>원본으로 Informix

Informix에서 데이터를 복사 하기 위해 복사 작업 **원본** 섹션에서 다음 속성이 지원 됩니다.

| 속성 | Description | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **InformixSource** 로 설정 해야 합니다. | 예 |
| Query | 사용자 지정 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예:**

```json
"activities":[
    {
        "name": "CopyFromInformix",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Informix input dataset name>",
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
                "type": "InformixSource",
                "query": "SELECT * FROM MyTable"
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
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
