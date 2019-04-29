---
title: Azure Data Factory를 사용하여 Amazon Redshift에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory를 사용하여 Amazon Redshift에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 9e1dde57dc1903e87704bd55fb0b942b7cc349e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61262319"
---
# <a name="copy-data-from-amazon-redshift-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Amazon Redshift에서 데이터 복사
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](v1/data-factory-amazon-redshift-connector.md)
> * [현재 버전](connector-amazon-redshift.md)


이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 Amazon Redshift에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Amazon Redshift에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히, 이 Amazon Redshift 커넥터는 쿼리 또는 기본 제공 Redshift UNLOAD 지원을 사용하여 Redshift에서 데이터 검색을 지원합니다.

> [!TIP]
> Redshift에서 많은 양의 데이터를 복사할 때 최상의 성능을 위해 Amazon S3을 통해 기본 제공 Redshift UNLOAD를 사용하는 것이 좋습니다. 자세한 내용은 [UNLOAD를 사용하여 Amazon Redshift에서 데이터 복사](#use-unload-to-copy-data-from-amazon-redshift)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

* [자체 호스팅 통합 런타임](create-self-hosted-integration-runtime.md)을 사용하여 데이터를 온-프레미스 데이터 저장소로 복사하는 경우 통합 런타임에(컴퓨터의 IP 주소 사용) Amazon Redshift 클러스터에 대한 액세스 권한을 부여합니다. 자세한 내용은 [클러스터에 대한 액세스 권한 부여](https://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) 를 참조하세요.
* Azure 데이터 저장소에 데이터를 복사하는 경우, Azure 데이터 센터에서 사용하는 IP 주소 및 SQL 범위를 계산하기 위해 [Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 참조하세요.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 Amazon Redshift 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

Amazon Redshift 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성을 다음으로 설정해야 합니다. **AmazonRedshift** | 예 |
| 서버 |Amazon Redshift 서버의 IP 주소 또는 호스트 이름입니다. |예 |
| 포트 |Amazon Redshift 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트 수입니다. |아니요(기본값: 5439) |
| 데이터베이스 |Amazon Redshift 데이터베이스의 이름입니다. |예 |
| 사용자 이름 |데이터베이스에 대한 액세스 권한이 있는 사용자의 이름입니다. |예 |
| 암호 |사용자 계정의 password입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. |예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. Azure Integration Runtime 또는 자체 호스팅 Integration Runtime을 사용할 수 있습니다(데이터 저장소가 개인 네트워크에 있는 경우). 지정하지 않으면 기본 Azure Integration Runtime을 사용합니다. |아닙니다. |

**예제:**

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties":
    {
        "type": "AmazonRedshift",
        "typeProperties":
        {
            "server": "<server name>",
            "database": "<database name>",
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

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 데이터 세트 문서를 참조하세요. 이 섹션에서는 Amazon Redshift 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

Amazon Redshift에서 데이터를 복사하려면 데이터 세트의 type 속성을 **RelationalTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 속성 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 다음으로 설정해야 합니다. **RelationalTable** | 예 |
| tableName | Amazon Redshift에서 테이블의 이름입니다. | 아니요(작업 원본에서 "query"가 지정된 경우) |

**예제**

```json
{
    "name": "AmazonRedshiftDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Amazon Redshift linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 Amazon Redshift 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="amazon-redshift-as-source"></a>원본으로 Amazon Redshift

Amazon Redshift에서 데이터를 복사하려면 복사 작업의 원본 형식을 **AmazonRedshiftSource**로 설정합니다. 복사 작업 **source** 섹션에서 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 복사 작업 원본의 type 속성을 다음으로 설정해야 합니다. **AmazonRedshiftSource** | 예 |
| 쿼리 |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. 예: select * from MyTable. |아니요(데이터 세트의 "tableName"이 지정된 경우) |
| redshiftUnloadSettings | Amazon Redshift UNLOAD를 사용하는 경우 속성 그룹입니다. | 아닙니다. |
| s3LinkedServiceName | “AmazonS3” 형식의 연결된 서비스 이름을 지정하여 중간 저장소로 사용하려는 Amazon S3을 참조합니다. | 예(UNLOAD를 사용하는 경우) |
| bucketName | 중간 데이터를 저장할 S3 버킷을 지정합니다. 제공되지 않은 경우 Data Factory 서비스는 자동으로 생성합니다.  | 예(UNLOAD를 사용하는 경우) |

**예제: UNLOAD를 사용하여 복사 작업에서 Amazon Redshift 원본**

```json
"source": {
    "type": "AmazonRedshiftSource",
    "query": "<SQL query>",
    "redshiftUnloadSettings": {
        "s3LinkedServiceName": {
            "referenceName": "<Amazon S3 linked service>",
            "type": "LinkedServiceReference"
        },
        "bucketName": "bucketForUnload"
    }
}
```

다음 섹션에서 UNLOAD를 사용하여 Amazon Redshift에서 효율적으로 데이터를 복사하는 방법에 대해 알아봅니다.

## <a name="use-unload-to-copy-data-from-amazon-redshift"></a>UNLOAD를 사용하여 Amazon Redshift에서 데이터 복사

[UNLOAD](https://docs.aws.amazon.com/redshift/latest/dg/r_UNLOAD.html)는 쿼리의 결과를 Amazon S3(Amazon Simple Storage Service)의 하나 이상의 파일에 언로드할 수 있는 Amazon Redshift에서 제공하는 메커니즘입니다. Redshift에서 큰 데이터 집합을 복사하기 위해 Amazon에서 권장하는 방법입니다.

**예: UNLOAD, 단계적 복사 및 PolyBase를 사용하여 Amazon Redshift에서 Azure SQL Data Warehouse로 데이터 복사**

이 샘플 사용 사례의 경우 복사 작업은 "redshiftUnloadSettings"에 구성된 대로 Amazon Redshift에서 Amazon S3으로 데이터를 언로드한 다음 "stagingSettings"에 지정된 대로 Amazon S3에서 Azure Blob으로 데이터를 복사하고, 마지막으로 PolyBase를 사용하여 SQL Data Warehouse로 데이터를 로드합니다. 모든 중간 형식은 복사 작업에서 제대로 처리됩니다.

![Redshift에서 SQL DW로 복사 워크플로](media/copy-data-from-amazon-redshift/redshift-to-sql-dw-copy-workflow.png)

```json
"activities":[
    {
        "name": "CopyFromAmazonRedshiftToSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "AmazonRedshiftDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AmazonRedshiftSource",
                "query": "select * from MyTable",
                "redshiftUnloadSettings": {
                    "s3LinkedServiceName": {
                        "referenceName": "AmazonS3LinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "bucketName": "bucketForUnload"
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": "AzureStorageLinkedService",
                "path": "adfstagingcopydata"
            },
            "dataIntegrationUnits": 32
        }
    }
]
```

## <a name="data-type-mapping-for-amazon-redshift"></a>Amazon Redshift에 대한 데이터 형식 매핑

Amazon Redshift에서 데이터를 복사하는 경우 Amazon Redshift 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| Amazon Redshift 데이터 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| BIGINT |Int64 |
| BOOLEAN |String |
| CHAR |String |
| DATE |DateTime |
| DECIMAL |Decimal |
| double precision |Double |
| INTEGER |Int32 |
| Real |Single |
| SmallInt |Int16 |
| TEXT |String |
| TIMESTAMP |DateTime |
| VARCHAR |String |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md##supported-data-stores-and-formats)를 참조하세요.
