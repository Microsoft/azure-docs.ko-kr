---
title: AWS Marketplace에서 데이터 복사
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 아마존 마켓플레이스 웹 서비스에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: db4173cdaf18d827c6a3c047777f7d5e67d79b84
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74931129"
---
# <a name="copy-data-from-amazon-marketplace-web-service-using-azure-data-factory"></a>Azure Data Factory를 사용하여 아마존 마켓플레이스 웹 서비스에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 아마존 마켓플레이스 웹 서비스에서 데이터를 복사하는 방법에 대해 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

이 Amazon Marketplace 웹 서비스 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

아마존 마켓플레이스 웹 서비스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 연결을 사용하는 기본 제공 드라이버를 제공합니다. 따라서 이 커넥터를 사용하여 드라이버를 수동으로 설치하지 않아도 됩니다.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 아마존 마켓플레이스 웹 서비스 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 아마존 마켓플레이스 웹 서비스 연결된 서비스에 대해 지원되는 속성입니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | type 속성은 **AmazonMWS**로 설정해야 합니다. | yes |
| endpoint | Amazon MWS 서버의 엔드포인트(즉, mws.amazonservices.com)입니다.  | yes |
| marketplaceID | 데이터를 검색하려는 Amazon Marketplace ID입니다. 여러 Marketplace ID에서 데이터를 검색하려면 쉼표(`,`)로 구분하세요. 즉, A2EUQ1WTGCTBG2입니다.  | yes |
| sellerID | Amazon 판매자 ID입니다.  | yes |
| mwsAuthToken | Amazon MWS 인증 토큰입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | yes |
| accessKeyId | 데이터 액세스에 사용되는 액세스 키 ID입니다.  | yes |
| secretKey | 데이터 액세스에 사용되는 비밀 키입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | yes |
| useEncryptedEndpoints | 데이터 원본 엔드포인트가 HTTPS를 사용하여 암호화되는지 여부를 지정합니다. 기본값은 true입니다.  | 아닙니다. |
| useHostVerification | SSL을 통해 연결할 때 서버 인증서의 호스트 이름이 서버의 호스트 이름과 일치하도록 할지 여부를 지정합니다. 기본값은 true입니다.  | 아닙니다. |
| usePeerVerification | SSL을 통해 연결할 때 서버의 ID를 확인할지 여부를 지정합니다. 기본값은 true입니다.  | 아닙니다. |

**예제:**

```json
{
    "name": "AmazonMWSLinkedService",
    "properties": {
        "type": "AmazonMWS",
        "typeProperties": {
            "endpoint" : "mws.amazonservices.com",
            "marketplaceID" : "A2EUQ1WTGCTBG2",
            "sellerID" : "<sellerID>",
            "mwsAuthToken": {
                 "type": "SecureString",
                 "value": "<mwsAuthToken>"
            },
            "accessKeyId" : "<accessKeyId>",
            "secretKey": {
                 "type": "SecureString",
                 "value": "<secretKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 아마존 마켓플레이스 웹 서비스 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

아마존 마켓플레이스 웹 서비스에서 데이터를 복사하려면 데이터 세트의 type 속성을 **AmazonMWSObject**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 집합의 type 속성은 **AmazonMWSObject** 로 설정 해야 합니다. | yes |
| tableName | 테이블 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "AmazonMWSDataset",
    "properties": {
        "type": "AmazonMWSObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<AmazonMWS linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}

```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 아마존 마켓플레이스 웹 서비스 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="amazon-mws-as-source"></a>Amazon MWS as source

아마존 마켓플레이스 웹 서비스에서 데이터를 복사하려면 복사 작업의 원본 형식을 **AmazonMWSSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성은 **AmazonMWSSource**로 설정해야 합니다. | yes |
| 쿼리 | 사용자 지정 SQL 쿼리를 사용하여 데이터를 읽습니다. 예: `"SELECT * FROM Orders where  Amazon_Order_Id = 'xx'"`. | 아니요(데이터 세트의 "tableName"이 지정된 경우) |

**예제:**

```json
"activities":[
    {
        "name": "CopyFromAmazonMWS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<AmazonMWS input dataset name>",
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
                "type": "AmazonMWSSource",
                "query": "SELECT * FROM Orders where Amazon_Order_Id = 'xx'"
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
