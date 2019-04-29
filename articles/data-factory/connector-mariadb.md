---
title: Azure Data Factory를 사용하여 MariaDB에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 MariaDB에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 02/01/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: f99a96f1b886f9f426c5dac64ac852368544475a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61401272"
---
# <a name="copy-data-from-mariadb-using-azure-data-factory"></a>Azure Data Factory를 사용하여 MariaDB에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 MariaDB에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

MariaDB에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 연결을 사용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하여 드라이버를 수동으로 설치하지 않아도 됩니다.

이 커넥터는 현재 10.0~10.2 버전의 MariaDB를 지원합니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 MariaDB 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 MariaDB 연결된 서비스에 대해 지원되는 속성입니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성을 다음으로 설정해야 합니다. **MariaDB** | 예 |
| connectionString | MariaDB에 연결할 ODBC 연결 문자열입니다. <br/>이 필드를 SecureString으로 표시하여 Data Factory에서 안전하게 저장합니다. Azure Key Vault에 암호를 넣고, 연결 문자열에서 `pwd` 구성을 끌어올 수도 있습니다. 자세한 내용은 다음 샘플 및 [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md) 문서를 참조하세요. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. 자체 호스팅 Integration Runtime 또는 Azure Integration Runtime을 사용할 수 있습니다(데이터 저장소를 공개적으로 액세스할 수 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

**예제:**

```json
{
    "name": "MariaDBLinkedService",
    "properties": {
        "type": "MariaDB",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=<host>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**예제: Azure Key Vault에 암호 저장**

```json
{
    "name": "MariaDBLinkedService",
    "properties": {
        "type": "MariaDB",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<host>;Port=<port>;Database=<database>;UID=<user name>;"
            },
            "pwd": { 
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 MariaDB 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

MariaDB에서 데이터를 복사하려면 데이터 세트의 type 속성을 **MariaDBTable**로 설정합니다. 이 형식의 데이터 세트에는 추가적인 형식별 속성이 없습니다.

**예제**

```json
{
    "name": "MariaDBDataset",
    "properties": {
        "type": "MariaDBTable",
        "linkedServiceName": {
            "referenceName": "<MariaDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 MariaDB 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="mariadb-as-source"></a>MariaDB를 원본으로

MariaDB에서 데이터를 복사하려면 복사 작업의 원본 형식을 **MariaDBSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **MariaDBSource** | 예 |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM MyTable"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromMariaDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MariaDB input dataset name>",
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
                "type": "MariaDBSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
