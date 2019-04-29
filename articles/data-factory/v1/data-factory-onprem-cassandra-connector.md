---
title: Data Factory를 사용하여 Cassandra에서 데이터 이동 | Microsoft Docs
description: Azure Data Factory를 사용하여 온-프레미스 Cassandra 데이터베이스에서 데이터를 이동하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 085cc312-42ca-4f43-aa35-535b35a102d5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0a3adbd082c68121e762fd03c2221a0c800f0bc5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60823983"
---
# <a name="move-data-from-an-on-premises-cassandra-database-using-azure-data-factory"></a>Azure Data Factory를 사용하여 온-프레미스 Cassandra 데이터베이스에서 데이터 이동
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](data-factory-onprem-cassandra-connector.md)
> * [버전 2(현재 버전)](../connector-cassandra.md)

> [!NOTE]
> 이 문서는 Data Factory 버전 1에 적용됩니다. Data Factory 서비스 현재 버전을 사용 중인 경우 [V2의 Cassandra 커넥터](../connector-cassandra.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 온-프레미스 Cassandra 데이터베이스에서 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

온-프레미스 Cassandra 데이터 저장소의 데이터를 지원되는 싱크 데이터 저장소로 복사할 수 있습니다. 복사 작업의 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 테이블을 참조하세요. 현재 데이터 팩터리는 다른 데이터 저장소에서 Cassandra 데이터 저장소로 데이터 이동이 아닌 Cassandra 데이터 저장소에서 다른 데이터 저장소로 데이터 이동만을 지원합니다.

## <a name="supported-versions"></a>지원되는 버전
Cassandra 커넥터는 Cassandra 2.x 및 3.x 버전을 지원합니다. 자체 호스팅 Integration Runtime에서 활동 실행의 경우 Cassandra 3.x는 IR 버전 3.7 이상에서 지원됩니다.

## <a name="prerequisites"></a>필수 조건
Azure Data Factory 서비스가 온-프레미스 Cassandra 데이터베이스에 연결할 수 있도록 하려면 데이터베이스와 리소스 경쟁을 피하려면 데이터 관리 게이트웨이를 데이터베이스를 호스트하는 컴퓨터와 동일한 컴퓨터 또는 별도의 컴퓨터에 설치해야 합니다. 데이터 관리 게이트웨이는 온-프레미스 데이터 원본을 클라우드 서비스에 안전하게 관리되는 방식으로 연결하는 구성 요소입니다. 데이터 관리 게이트웨이에 대한 세부 정보는 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하세요. 데이터 이동을 위한 데이터 파이프라인 및 게이트웨이 설정에 대한 단계별 지침은 [온-프레미스에서 클라우드로 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

데이터베이스가 클라우드(예: Azure IaaS VM)에서 호스팅되는 경우에도 게이트웨이를 사용하여 Cassandra 데이터베이스에 연결해야 합니다. 게이트웨이를 데이터베이스에 연결할 수 있는 한, 데이터베이스를 호스팅하는 동일한 VM 또는 별도의 VM에 게이트웨이를 포함할 수 있습니다.

게이트웨이를 설치하면 Cassandra 데이터베이스에 연결하는 데 사용되는 Microsoft Cassandra ODBC 드라이버가 자동으로 설치됩니다. 따라서 Cassandra 데이터베이스에서 데이터를 복사할 때 게이트웨이 컴퓨터에 드라이버를 수동으로 설치하지 않아도 됩니다.

> [!NOTE]
> 연결/게이트웨이 관련 문제 해결에 대한 팁은 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshooting-gateway-issues) 을 참조하세요.

## <a name="getting-started"></a>시작
여러 도구/API를 사용하여 온-프레미스 Cassandra 데이터 저장소의 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다.

- 파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사**를 사용하는 것입니다. 단계별 지침은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습을 볼 수 있습니다.
- 또한 다음 도구를 사용하여 파이프라인을 만들 수 있습니다. **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 및 **REST API** 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스**를 만듭니다.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트**를 만듭니다.
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인**을 만듭니다.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다. 온-프레미스 Cassandra 데이터 저장소의 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의에 대한 샘플은 이 문서의 [JSON의 예: Cassandra에서 Azure Blob으로 데이터 복사](#json-example-copy-data-from-cassandra-to-azure-blob) 섹션을 참조하세요.

다음 섹션에서는 Cassandra 데이터 저장소에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 표에서는 Cassandra 연결된 서비스와 관련된 JSON 요소에 대한 설명을 제공합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 형식 |type 속성을 다음으로 설정해야 합니다. **OnPremisesCassandra** |예 |
| host |Cassandra 서버에 대한 하나 이상의 IP 주소 또는 호스트 이름.<br/><br/>모든 서버에 동시에 연결하려면 쉼표로 구분된 IP 주소 또는 호스트 이름 목록을 지정합니다. |예 |
| 포트 |Cassandra 서버가 클라이언트 연결을 수신하는 데 사용하는 TCP 포트입니다. |아니요. 기본값: 9042 |
| authenticationType |Basic 또는 Anonymous |예 |
| 사용자 이름 |사용자 계정의 사용자 이름을 지정합니다. |예. authenticationType은 Basic으로 설정됩니다. |
| 암호 |사용자 계정으로 password를 지정합니다. |예. authenticationType은 Basic으로 설정됩니다. |
| gatewayName |온-프레미스 Cassandra 데이터베이스에 연결하는 데 사용되는 게이트웨이 이름입니다. |예 |
| encryptedCredential |게이트웨이에 의해 암호화된 자격 증명입니다. |아닙니다. |

>[!NOTE]
>현재 연결에 SSL을 사용한 Cassandra 연결은 지원되지 않습니다.

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **CassandraTable** 데이터 세트 형식의 데이터 세트에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| keyspace |Cassandra 데이터베이스의 키스페이스 또는 스키마의 이름입니다. |예(**CassandraSource**의 **query**가 정의되지 않은 경우) |
| tableName |Cassandra 데이터베이스에 있는 테이블의 이름입니다. |예(**CassandraSource**의 **query**가 정의되지 않은 경우) |

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

원본이 **CassandraSource**형식인 경우 typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 자산 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| 쿼리 |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL-92 쿼리 또는 CQL 쿼리입니다. [CQL 참조](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html)를 참조하세요. <br/><br/>SQL 쿼리를 사용할 경우 **keyspace name.table name** 을 지정하여 쿼리하려는 테이블을 나타냅니다. |아니요(데이터 세트의 tableName 및 keyspace가 정의된 경우) |
| consistencyLevel |일관성 수준은 클라이언트 애플리케이션에 데이터를 반환하기 전에 읽기 요청에 응답해야 하는 복제본 수를 지정합니다. Cassandra는 데이터의 지정된 수의 복제본이 읽기 요청을 충족하는지 확인합니다. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. 자세한 내용은 [데이터 일관성 구성](https://docs.datastax.com/en/cassandra/2.1/cassandra/dml/dml_config_consistency_c.html) 을 참조하세요. |아니요. 기본값은 ONE입니다. |

## <a name="json-example-copy-data-from-cassandra-to-azure-blob"></a>JSON 예제: Cassandra에서 Azure Blob으로 데이터 복사
다음 예제에서는 [Azure Portal](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. 온-프레미스 Cassandra 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [여기](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 에 설명한 싱크로 데이터를 복사할 수 있습니다.

> [!IMPORTANT]
> 이 샘플은 JSON 코드 조각을 제공합니다. 데이터 팩터리를 만들기 위한 단계별 지침은 포함하지 않습니다. 단계별 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

* [OnPremisesCassandra](#linked-service-properties)형식의 연결된 서비스입니다.
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)형식의 연결된 서비스
* [CassandraTable](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
* [CassandraSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

**Cassandra 연결된 서비스:**

이 예제에서는 **Cassandra** 연결된 서비스를 사용합니다. 이 연결된 서비스에서 지원하는 속성 목록은 [Cassandra 연결된 서비스](#linked-service-properties) 섹션을 참조하세요.

```json
{
    "name": "CassandraLinkedService",
    "properties":
    {
        "type": "OnPremisesCassandra",
        "typeProperties":
        {
            "authenticationType": "Basic",
            "host": "mycassandraserver",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "mygateway"
        }
    }
}
```

**Azure Storage 연결된 서비스:**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
```

**Cassandra 입력 데이터 세트:**

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "mykeyspace"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

**external**을 **true**로 설정하면 데이터 세트가 Data Factory의 외부에 있고 Data Factory의 활동으로 생성되지 않는다고 Data Factory 서비스에 전달됩니다.

**Azure Blob 출력 데이터 세트:**

데이터는 1시간마다 새 blob에 기록됩니다(frequency: hour, interval: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/fromcassandra"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Cassandra 소스 및 Blob 싱크를 사용하는 파이프라인의 복사 작업:**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **CassandraSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다.

RelationalSource에서 지원하는 속성 목록은 [RelationalSource 형식 속성](#copy-activity-properties) 을 참조하세요.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[
        {
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "CassandraInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"

                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```

### <a name="type-mapping-for-cassandra"></a>Cassandra에 대한 형식 매핑
| Cassandra 형식 | .NET 기반 형식 |
| --- | --- |
| ASCII |String |
| BIGINT |Int64 |
| BLOB |Byte[] |
| BOOLEAN |BOOLEAN |
| DECIMAL |Decimal |
| DOUBLE |DOUBLE |
| FLOAT |Single |
| INET |String |
| INT |Int32 |
| TEXT |String |
| TIMESTAMP |DateTime |
| TIMEUUID |Guid |
| UUID |Guid |
| VARCHAR |String |
| VARINT |DECIMAL |

> [!NOTE]
> 컬렉션 형식(맵, 집합, 목록 등)에 대해서는 [가상 테이블을 사용하여 Cassandra 컬렉션 형식으로 작업](#work-with-collections-using-virtual-table) 섹션을 참조하세요.
>
> 사용자 정의 형식은 지원되지 않습니다.
>
> 이진 열의 길이와 문자열 열 길이는 4000보다 클 수 없습니다.
>
>

## <a name="work-with-collections-using-virtual-table"></a>가상 테이블을 사용하여 컬렉션으로 작업
Azure Data Factory는 기본 제공 ODBC 드라이버를 사용하여 Cassandra 데이터베이스에 연결하고 데이터를 복사합니다. 맵, 집합 및 목록을 포함하는 컬렉션 형식의 경우 드라이버는 데이터를 해당 가상 테이블로 다시 정규화합니다. 특히, 테이블에 컬렉션 열이 포함되어 있으면 드라이버는 다음 가상 테이블을 생성합니다.

* **기본 테이블**: 컬렉션 열을 제외하고 실제 테이블과 동일한 데이터가 포함되어 있습니다. 기본 테이블은 나타내는 실제 테이블과 동일한 이름을 사용합니다.
* **가상 테이블** : 컬렉션 열에 대해 생성되며, 중첩된 데이터를 확장합니다. 컬렉션을 나타내는 가상 테이블 이름은 실제 테이블의 이름, 구분 기호 "*vt*" 및 열 이름을 사용하여 지정합니다.

가상 테이블은 실제 테이블의 데이터를 나타내며, 드라이버가 정규화되지 않은 데이터에 액세스할 수 있도록 합니다. 자세한 내용은 예제 섹션을 참조하세요. 가상 테이블을 쿼리 및 조인하여 Cassandra 컬렉션의 콘텐츠에 액세스할 수 있습니다.

[복사 마법사](data-factory-data-movement-activities.md#create-a-pipeline-with-copy-activity)를 사용하여 가상 테이블을 비롯한 Cassandra 데이터베이스의 테이블 목록을 표시하고 내부의 데이터를 미리 볼 수 있습니다. 또한 복사 마법사에서 쿼리를 생성하고 결과가 유효한지 확인할 수도 있습니다.

### <a name="example"></a>예
예를 들어 다음 "ExampleTable"은 "pk_int"라는 정수 기본 키 열, value라는 텍스트 열, 목록 열, 맵 열, 집합 열("StringSet")을 포함하는 Cassandra 데이터베이스 테이블입니다.

| pk_int | 값 | 나열 | Map | StringSet |
| --- | --- | --- | --- | --- |
| 1 |"sample value 1" |["1", "2", "3"] |{"S1": "a", "S2": "b"} |{"A", "B", "C"} |
| 3 |"sample value 3" |["100", "101", "102", "105"] |{"S1": "t"} |{"A", "E"} |

드라이버는 이 단일 테이블을 나타내는 여러 개의 가상 테이블을 생성합니다. 가상 테이블의 외래 키 열은 실제 테이블의 기본 키 열을 참조하고, 가상 테이블 행에 해당하는 실제 테이블 행을 나타냅니다.

첫 번째 가상 테이블은 다음 테이블에 표시된 "ExampleTable"이라는 기본 테이블입니다. 기본 테이블에는 컬렉션을 제외하고 원래 데이터베이스 테이블과 동일한 데이터가 포함됩니다. 컬렉션은 테이블에서 생략된 후 다른 가상 테이블에서 확장됩니다.

| pk_int | 값 |
| --- | --- |
| 1 |"sample value 1" |
| 3 |"sample value 3" |

다음 표에서는 List, Map 및 StringSet 열의 데이터를 다시 정규화하는 가상 테이블을 보여 줍니다. 이름이 “_index” 또는 “_key”로 끝나는 열은 원본 목록이나 맵 내의 데이터 위치를 나타냅니다. 이름이 "_value"로 끝나는 열에는 컬렉션에서 확장된 데이터가 포함됩니다.

#### <a name="table-exampletablevtlist"></a>테이블 "ExampleTable_vt_List":
| pk_int | List_index | List_value |
| --- | --- | --- |
| 1 |0 |1 |
| 1 |1 |2 |
| 1 |2 |3 |
| 3 |0 |100 |
| 3 |1 |101 |
| 3 |2 |102 |
| 3 |3 |103 |

#### <a name="table-exampletablevtmap"></a>테이블 “ExampleTable_vt_Map”:
| pk_int | Map_key | Map_value |
| --- | --- | --- |
| 1 |S1 |A |
| 1 |S2 |b |
| 3 |S1 |t |

#### <a name="table-exampletablevtstringset"></a>테이블 “ExampleTable_vt_StringSet”:
| pk_int | StringSet_value |
| --- | --- |
| 1 |A |
| 1 |b |
| 1 |C |
| 3 |A |
| 3 |E |

## <a name="map-source-to-sink-columns"></a>원본을 싱크 열로 매핑
원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하는 방법은 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="repeatable-read-from-relational-sources"></a>관계형 원본에서 반복 가능한 읽기
관계형 데이터 저장소에서 데이터를 복사할 때는 의도치 않는 결과를 방지하기 위해 반복성을 염두에 두어야 합니다. Azure Data Factory에서는 조각을 수동으로 다시 실행할 수 있습니다. 또한 오류가 발생하면 조각을 다시 실행하도록 데이터 세트에 대한 재시도 정책을 구성할 수 있습니다. 어느 쪽이든 조각이 재실행되는 경우 조각이 실행되는 횟수에 관계없이 같은 데이터를 읽어야 합니다. [관계형 원본에서 반복 가능한 읽기](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)를 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.
