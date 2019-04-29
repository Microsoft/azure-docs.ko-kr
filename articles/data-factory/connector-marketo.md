---
title: Azure Data Factory를 사용한 Marketo에서 데이터 복사(미리 보기) | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Marketo에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: d6d6517a85997265021573b2f9d481c81283c216
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61400460"
---
# <a name="copy-data-from-marketo-using-azure-data-factory-preview"></a>Azure Data Factory를 사용하여 Marketo에서 데이터 복사(미리 보기)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Marketo에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

> [!IMPORTANT]
> 이 커넥터는 현재 미리 보기로 제공되고 있습니다. 사용해 보고 피드백을 제공할 수 있습니다. 솔루션의 미리 보기 커넥터에 종속성을 적용하려면 [Azure 지원](https://azure.microsoft.com/support/)에 문의하세요.

## <a name="supported-capabilities"></a>지원되는 기능

Marketo에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 연결을 사용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하여 드라이버를 수동으로 설치하지 않아도 됩니다.

>[!NOTE]
>이 Marketo 커넥터는 Marketo REST API를 기반으로 합니다. Marketo에는 서버 쪽에 [동시 요청 제한](https://developers.marketo.com/rest-api/)이 있습니다. "REST API를 사용하려고 시도하는 동안 오류 발생: 최대 속도 제한 '100'에서 '20'초 초과(606)" 또는 "REST API를 사용하려고 시도하는 동안 오류 발생: 동시 액세스 제한 '10'에 도달(615)" 오류가 발생하면 동시 복사 작업 실행을 줄여서 서비스에 대한 요청 수를 줄이는 것이 좋습니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Marketo 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 Marketo 연결된 서비스에 대해 지원되는 속성입니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성을 다음으로 설정해야 합니다. **Marketo** | 예 |
| endpoint | Marketo 서버의 엔드포인트입니다. (즉, 123-ABC-321.mktorest.com)  | 예 |
| clientId | Marketo 서비스의 클라이언트 ID입니다.  | 예 |
| clientSecret | Marketo 서비스의 클라이언트 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| useEncryptedEndpoints | 데이터 원본 엔드포인트가 HTTPS를 사용하여 암호화되는지 여부를 지정합니다. 기본값은 true입니다.  | 아닙니다. |
| useHostVerification | SSL을 통해 연결할 때 서버 인증서의 호스트 이름이 서버의 호스트 이름과 일치하도록 할지 여부를 지정합니다. 기본값은 true입니다.  | 아닙니다. |
| usePeerVerification | SSL을 통해 연결할 때 서버의 ID를 확인할지 여부를 지정합니다. 기본값은 true입니다.  | 아닙니다. |

**예제:**

```json
{
    "name": "MarketoLinkedService",
    "properties": {
        "type": "Marketo",
        "typeProperties": {
            "endpoint" : "123-ABC-321.mktorest.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                "type": "SecureString",
                "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Marketo 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Marketo에서 데이터를 복사하려면 데이터 세트의 type 속성을 **MarketoObject**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **MarketoObject** | 예 |
| tableName | 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "MarketoDataset",
    "properties": {
        "type": "MarketoObject",
        "linkedServiceName": {
            "referenceName": "<Marketo linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Marketo 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="marketo-as-source"></a>Marketo를 원본으로

MarketoSource에서 데이터를 복사하려면 복사 작업의 원본 형식을 **MarketoSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **MarketoSource** | 예 |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM Activitiy_Types"` | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromMarketo",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Marketo input dataset name>",
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
                "type": "MarketoSource",
                "query": "SELECT top 1000 * FROM Activitiy_Types"
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
