---
title: Data Factory를 사용하여 다른 곳에서 Oracle로 또는 그 반대로 데이터 복사 | Microsoft Docs
description: Azure Data Factory를 사용하여 다른 곳에서 온-프레미스 Oracle 데이터베이스로 또는 그 반대로 데이터를 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 3c20aa95-a8a1-4aae-9180-a6a16d64a109
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 4ff7f92d1d13966be5d17f37210bef961f64faf2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61462414"
---
# <a name="copy-data-to-or-from-oracle-on-premises-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 다른 곳에서 Oracle 온-프레미스로 또는 그 반대로 데이터 복사

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](data-factory-onprem-oracle-connector.md)
> * [버전 2(현재 버전)](../connector-oracle.md)

> [!NOTE]
> 이 문서의 내용은 Azure Data Factory 버전 1에 적용됩니다. 현재 버전의 Azure Data Factory 서비스를 사용 중인 경우, [V2의 Oracle 커넥터](../connector-oracle.md)를 참조하세요.


이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 온-프레미스 Oracle 데이터베이스에서 다른 곳으로 또는 그 반대로 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md)을 기반으로 합니다.

## <a name="supported-scenarios"></a>지원되는 시나리오

*Oracle 데이터베이스에서* 다음 데이터 저장소로 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sink](../../../includes/data-factory-supported-sinks.md)]

다음 데이터 저장소에서 *Oracle 데이터베이스로* 데이터를 복사할 수 있습니다.

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

## <a name="prerequisites"></a>필수 조건

Data Factory는 데이터 관리 게이트웨이를 사용하여 온-프레미스 Oracle 원본에 연결하도록 지원합니다. 데이터 관리 게이트웨이에 대해 자세히 알아보려면 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)를 참조하세요. 데이터 이동을 위해 데이터 파이프라인에서 게이트웨이를 설정하는 방법에 대한 단계별 지침은 [온-프레미스에서 클라우드로 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.

게이트웨이는 Oracle이 Azure IaaS(Infrastructure as a Service) VM에 호스트되더라도 필요합니다. 게이트웨이를 데이터베이스에 연결할 수 있는 한 데이터 저장소와 동일한 IaaS VM 또는 다른 VM에 게이트웨이를 설치할 수 있습니다.

> [!NOTE]
> 연결 및 게이트웨이 관련 문제 해결을 위한 팁은 [게이트웨이 문제 해결](data-factory-data-management-gateway.md#troubleshooting-gateway-issues)을 참조하세요.

## <a name="supported-versions-and-installation"></a>지원되는 버전 및 설치

이 Oracle 커넥터는 다음 두 가지 버전의 드라이버를 지원합니다.

- **Oracle용 Microsoft 드라이버(권장)**: 데이터 관리 게이트웨이 버전 2.7부터 Oracle용 Microsoft 드라이버가 게이트웨이와 함께 자동으로 설치됩니다. Oracle에 대한 연결을 설정하기 위해 드라이버를 설치하거나 업데이트할 필요가 없습니다. 또한 이 드라이버를 사용하면 더 나은 복사 성능을 경험할 수 있습니다. 아래 Oracle 데이터베이스 버전이 지원됩니다.
  - Oracle 12c R1(12.1)
  - Oracle 11g R1, R2(11.1, 11.2)
  - Oracle 10g R1, R2(10.1, 10.2)
  - Oracle 9i R1, R2(9.0.1, 9.2)
  - Oracle 8i R3(8.1.7)

    > [!NOTE]
    > Oracle 프록시 서버는 지원되지 않습니다.

    > [!IMPORTANT]
    > 현재 Oracle용 Microsoft 드라이버는 Oracle에서의 데이터 복사만 지원합니다. 드라이버는 Oracle에 쓰기는 지원하지 않습니다. 데이터 관리 게이트웨이 **진단** 탭의 테스트 연결 기능은 이 드라이버를 지원하지 않습니다. 대신 복사 마법사를 사용하여 연결의 유효성을 검사할 수 있습니다.
    >

- **.NET용 Oracle Data Provider**: Oracle Data Provider를 사용하여 Oracle에서 다른 곳으로 또는 그 반대로 데이터를 복사하도록 선택할 수 있습니다. 이 구성 요소는 [Windows용 Oracle Data Access Components](https://www.oracle.com/technetwork/topics/dotnet/downloads/)에 포함됩니다. 게이트웨이가 설치되어 있는 머신에 관련 버전(32비트 또는 64비트)을 설치합니다. [Oracle Data Provider .NET 12.1](https://docs.oracle.com/database/121/ODPNT/InstallSystemRequirements.htm#ODPNT149)은 Oracle Database 10g 릴리스 2 이상 버전에 액세스할 수 있습니다.

    **XCopy 설치**를 선택하는 경우 readme.htm 파일에 설명된 단계를 완료합니다. UI가 있는 설치 관리자를 선택하는 것이 좋습니다(XCopy 설치 관리자 제외).

    공급자를 설치한 후 서비스 애플릿 또는 데이터 관리 게이트웨이 구성 관리자를 사용하여 머신에서 데이터 관리 게이트웨이 호스트 서비스를 다시 시작합니다.

복사 마법사를 사용하여 복사 파이프라인을 작성하는 경우 드라이버 형식은 자동으로 결정됩니다. 게이트웨이 버전이 2.7 이전이거나 싱크로 Oracle을 선택한 경우 외에는 Microsoft 드라이버가 기본적으로 사용됩니다.

## <a name="get-started"></a>시작하기

복사 작업이 있는 파이프라인을 만들 수 있습니다. 파이프라인은 다른 도구 또는 API를 사용하여 다른 곳에서 온-프레미스 Oracle 데이터베이스로 또는 그 반대로 데이터를 이동합니다.

파이프라인을 만드는 가장 쉬운 방법은 복사 마법사를 사용하는 것입니다. 단계별 지침은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습을 볼 수 있습니다.

또한 **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 또는 **REST API**를 포함한 도구를 사용하여 파이프라인을 만들 수 있습니다. 복사 작업이 포함된 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만듭니다.

1. **데이터 팩터리**를 만듭니다. 데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다.
2. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스**를 만듭니다. 예를 들어 Oracle 데이터베이스에서 Azure Blob Storage로 데이터를 복사하는 경우 Oracle 데이터베이스 및 Azure 스토리지 계정을 데이터 팩터리에 연결하는 두 개의 연결된 서비스를 만듭니다. Oracle과 관련된 연결된 서비스 속성은 [연결된 서비스 속성](#linked-service-properties)을 참조하세요.
3. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트**를 만듭니다. 이전 단계의 예제에서 입력 데이터가 포함된 Oracle 데이터베이스의 테이블을 지정하는 데이터 세트를 만듭니다. Oracle 데이터베이스에서 복사된 데이터가 저장되는 폴더 및 Blob 컨테이너를 지정하는 또 다른 데이터 세트를 만듭니다. Oracle과 관련된 데이터 세트 속성은 [데이터 세트 속성](#dataset-properties)을 참조하세요.
4. 입력과 출력으로 각각의 데이터 세트를 사용하는 복사 작업이 포함된 **파이프라인**을 만듭니다. 이전 예제에서는 **OracleSource**를 원본으로, **BlobSink**를 복사 작업의 싱크로 사용합니다. 마찬가지로, Azure Blob Storage에서 Oracle 데이터베이스로 복사하는 경우 복사 작업에 **BlobSource** 및 **OracleSink**를 사용합니다. Oracle 데이터베이스와 관련된 복사 작업 속성은 [복사 작업 속성](#copy-activity-properties)을 참조하세요. 원본 또는 싱크로 데이터 저장소를 사용하는 방법에 대한 자세한 내용은 이전 섹션의 데이터 저장소에 대한 링크를 선택하세요.

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구 또는 API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다. 다른 곳에서 온-프레미스 Oracle 데이터베이스로 또는 그 반대로 데이터를 복사하는 데 사용하는 Data Factory 엔터티에 대한 JSON 정의가 포함된 샘플은 JSON 예제를 참조하세요.

다음 섹션에서는 Data Factory 엔터티를 정의하는 데 사용하는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음 테이블은 Oracle 연결 서비스에 해당하는 JSON 요소에 대해 설명합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 형식 |**type** 속성은 **OnPremisesOracle**로 설정해야 합니다. |예 |
| driverType | Oracle 데이터베이스에서 다른 곳으로 또는 그 반대로 데이터를 복사하는 데 사용할 드라이버를 지정합니다. 허용되는 값은 **Microsoft** 및 **ODP**(기본값)입니다. 드라이버 세부 정보에 대해서는 [지원되는 버전 및 설치](#supported-versions-and-installation)를 참조하세요. | 아닙니다. |
| connectionString | **connectionString** 속성에 대한 Oracle 데이터베이스 인스턴스에 연결하는 데 필요한 정보를 지정합니다. | 예 |
| gatewayName | 온-프레미스 Oracle 서버에 연결하는 데 사용되는 게이트웨이 이름입니다. |예 |

**예제: Microsoft 드라이버 사용**

> [!TIP]
> "ORA-01025: UPI parameter out of range"(ORA-01025: UPI 매개 변수가 범위를 벗어남) 오류가 표시되고 Oracle이 8i 버전인 경우 연결 문자열에 `WireProtocolMode=1`을 추가하고 다시 시도합니다.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**예제: ODP 드라이버 사용**

허용되는 형식을 알아보려면 [.NET ODP용 Oracle Data Provider](https://www.connectionstrings.com/oracle-data-provider-for-net-odp-net/)를 참조하세요.

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>))); User Id=<user name>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md)를 참조하세요.

구조, 가용성 및 정책과 같은 데이터 세트 JSON 파일의 섹션은 모든 데이터 세트 형식(예: Oracle, Azure Blob Storage 및 Azure Table Storage)에 대해 유사합니다.

**typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **OracleTable** 형식의 데이터 집합에 대한 **typeProperties** 섹션에는 다음 속성이 있습니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| tableName |연결된 서비스가 참조하는 Oracle 데이터베이스에 있는 테이블의 이름입니다. |아니요(**oracleReaderQuery** 또는 **OracleSource**가 지정된 경우) |

## <a name="copy-activity-properties"></a>복사 활동 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md)를 참조하세요.

이름, 설명, 입력 및 출력 테이블, 정책과 같은 속성은 모든 형식의 작업에 사용할 수 있습니다.

> [!NOTE]
> 복사 작업은 하나의 입력만 사용하며 하나의 출력만 생성합니다.

작업의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 작업 유형에 대해 다릅니다. 복사 작업 속성은 원본 및 싱크의 형식에 따라 달라집니다.

### <a name="oraclesource"></a>oracleReaderQuery

원본이 **OracleSource** 형식인 복사 작업의 경우 **typeProperties** 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 자산 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| oracleReaderQuery |사용자 지정 쿼리를 사용하여 데이터를 읽습니다. |SQL 쿼리 문자열. 예를 들어 “select \* from **MyTable**”입니다. <br/><br/>지정하지 않은 경우 “select \* from **MyTable**” SQL 문이 실행됩니다. |아닙니다.<br />(**dataset**의 **tableName**이 지정된 경우) |

### <a name="oraclesink"></a>파이프라인

**OracleSink** 는 다음 속성을 지원합니다.

| 자산 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| writeBatchTimeout |시간이 초과되기 전에 완료하려는 배치 삽입 작업을 위한 대기 시간입니다. |**timespan**<br/><br/> 예제: 00:30:00(30분) |아닙니다. |
| writeBatchSize |버퍼 크기가 **writeBatchSize** 값에 도달하면 SQL 테이블에 데이터를 삽입합니다. |정수(행 수) |아니요(기본값: 100) |
| sqlWriterCleanupScript |특정 조각의 데이터를 정리하기 위해 복사 작업에 대해 실행할 쿼리를 지정합니다. |쿼리 문입니다. |아닙니다. |
| sliceIdentifierColumnName |자동으로 생성된 조각 식별자를 사용하여 채울 복사 작업에 대한 열 이름을 지정합니다. 다시 실행할 때 특정 조각의 데이터를 정리하는 데 **sliceIdentifierColumnName**에 대한 값이 사용됩니다. |**이진(32)** 데이터 형식이 있는 열의 열 이름입니다. |아닙니다. |

## <a name="json-examples-for-copying-data-to-and-from-the-oracle-database"></a>다른 곳에서 Oracle 데이터베이스로 또는 그 반대로 데이터를 복사하는 JSON 예제

다음 예제에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공하며, 샘플은 Oracle 데이터베이스에서 다른 곳으로 또는 그 반대로, 다른 곳에서 Azure Blob Storage로 또는 그 반대로 데이터를 복사하는 방법을 보여줍니다. 단, 데이터는 Azure Data Factory의 복사 작업을 사용하여 [지원되는 데이터 저장소 및 형식](data-factory-data-movement-activities.md#supported-data-stores-and-formats)에 나열된 싱크로 직접 복사할 수 있습니다.

**예제: Oracle에서 Azure Blob Storage로 데이터 복사**

샘플에 포함된 Data Factory 엔터티는 다음과 같습니다.

* [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)형식의 연결된 서비스
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
* [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
* [OracleSource](data-factory-onprem-oracle-connector.md#copy-activity-properties)를 원본으로, [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 싱크로 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md).

샘플은 온-프레미스 Oracle 데이터베이스의 테이블에서 blob에 매시간 데이터를 복사합니다. 샘플에 사용되는 다양한 속성에 대한 자세한 내용은 샘플 다음에 나오는 섹션을 참조하세요.

**Oracle 연결된 서비스**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString":"Host=<host>;Port=<port>;Sid=<service ID>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**비디오: Linux에서 Azure File Storage 사용**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Oracle 입력 데이터 집합**

샘플은 Oracle에 **MyTable**이란 테이블을 만들었다고 가정합니다. 시계열 데이터에 대한 **timestampcolumn**이라는 열을 포함합니다.

**external** 설정: **true**는 데이터 집합이 Data Factory의 외부에 있으며 Data Factory의 작업에 의해 생성되지 않는다는 사실을 Data Factory 서비스에 알립니다.

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2014-02-27T12:00:00",
            "frequency": "Hour"
        },
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

**Azure Blob 출력 데이터 세트**

데이터는 1시간마다 새 blob에 기록됩니다(**frequency**: **hour**, **interval**: **1**). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간의 년, 월, 일 및 시 부분을 사용합니다.

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t",
                "rowDelimiter": "\n"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**복사 작업이 포함된 파이프라인**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성되고 매시간 실행하도록 예약된 복사 작업을 포함합니다. 파이프라인 JSON 정의에서 **원본** 형식은 **OracleSource**로 설정되고 **싱크** 형식은 **BlobSink**로 설정됩니다. **oracleReaderQuery** 속성을 사용하여 지정한 SQL 쿼리는 과거 시간에서 복사할 데이터를 선택합니다.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for a copy activity",
        "activities":[
            {
                "name": "OracletoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": " OracleInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "OracleSource",
                        "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

**예제: Azure Blob Storage에서 Oracle로 데이터 복사**

이 샘플은 Azure Blob Storage에서 온-프레미스 Oracle 데이터베이스로 데이터를 복사하는 방법을 보여줍니다. 하지만 Azure Data Factory의 복사 작업을 사용하면 데이터를 [지원되는 데이터 저장소 및 형식](data-factory-data-movement-activities.md#supported-data-stores-and-formats)에 나열된 원본 중 하나에서 *직접* 복사할 수 있습니다.

샘플에 포함된 Data Factory 엔터티는 다음과 같습니다.

* [OnPremisesOracle](data-factory-onprem-oracle-connector.md#linked-service-properties)형식의 연결된 서비스
* [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
* [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
* [OracleTable](data-factory-onprem-oracle-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md).
* [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties)를 소스로, [OracleSink](data-factory-onprem-oracle-connector.md#copy-activity-properties)를 싱크로 사용하는 복사 작업이 포함된 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 Blob에서 온-프레미스 Oracle 데이터베이스의 테이블로 매시간 데이터를 복사합니다. 샘플에 사용되는 다양한 속성에 대한 자세한 내용은 샘플 다음에 나오는 섹션을 참조하세요.

**Oracle 연결된 서비스**

```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "connectionString": "Data Source=(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=<host name>)(PORT=<port number>))(CONNECT_DATA=(SERVICE_NAME=<service ID>)));
            User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

**비디오: Linux에서 Azure File Storage 사용**

```json
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

**Azure Blob 입력 데이터 세트**

데이터는 1시간마다 새 blob에서 선택됩니다(**frequency**: **hour**, **interval**: **1**). Blob에 대한 폴더 경로 및 파일 이름은 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간의 년, 월 및 일 부분을 사용합니다. 파일 이름은 시작 시간의 시 부분을 사용합니다. **external** 설정: **true**는 이 테이블이 Data Factory의 외부에 있으며 Data Factory의 작업에 의해 생성되지 않음을 Data Factory 서비스에 알립니다.

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                }
            ],
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            }
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        },
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

**Oracle 출력 데이터 세트**

샘플은 Oracle에 **MyTable**이란 테이블을 만들었다고 가정합니다. Blob CSV 파일을 포함하려면 같은 수의 열을 사용하여 Oracle에 테이블을 만듭니다. 새 행은 매시간 테이블에 추가됩니다.

```json
{
    "name": "OracleOutput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Day",
            "interval": "1"
        }
    }
}
```

**복사 작업이 포함된 파이프라인**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성되고 매시간 실행하도록 예약된 복사 작업을 포함합니다. 파이프라인 JSON 정의에서 **원본** 형식은 **BlobSource**로 설정되고 **싱크** 형식은 **OracleSink**로 설정됩니다.

```json
{
    "name":"SamplePipeline",
    "properties":{
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-05T19:00:00",
        "description":"pipeline with a copy activity",
        "activities":[
            {
                "name": "AzureBlobtoOracle",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "OracleOutput"
                    }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "OracleSink"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
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


## <a name="troubleshooting-tips"></a>문제 해결 팁

### <a name="problem-1-net-framework-data-provider"></a>문제 1: .NET Framework Data Provider

**오류 메시지**

    Copy activity met invalid parameters: 'UnknownParameterName', Detailed message: Unable to find the requested .NET Framework Data Provider. It may not be installed.

**가능한 원인**

* Oracle용 .NET Framework Data Provider가 설치되지 않았습니다.
* Oracle용 .NET Framework Data Provider가 .NET Framework 2.0에 설치되었으며 .NET Framework 4.0 폴더에서 찾을 수 없습니다.

**해결 방법**

* Oracle용 .NET Provider를 설치하지 않은 경우 [해당 항목을 설치](https://www.oracle.com/technetwork/topics/dotnet/downloads/)한 다음, 시나리오를 다시 시도합니다.
* 공급자를 설치한 후에도 오류 메시지가 표시되면 다음 단계를 완료합니다.
    1. <시스템 디스크\>:\Windows\Microsoft.NET\Framework64\v2.0.50727\CONFIG\machine.config 폴더에서 .NET 2.0에 대한 머신 구성 파일을 엽니다.
    2. **.NET용 Oracle Data Provider**를 검색합니다. **system.data** > **DbProviderFactories**: `<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />`에서 다음 샘플에서와 같이 항목을 찾을 수 있습니다
* 이 항목을 .NET 4.0 폴더 즉, <시스템 디스크\>:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config의 machine.config 파일에 복사합니다. 그런 다음, 버전을 4.xxx.x.x로 변경합니다.
* **gacutil /i[공급자 경로]** 를 실행하여 GAC(글로벌 어셈블리 캐시)에 <ODP.NET 설치된 경로\>\11.2.0\client_1\odp.net\bin\4\Oracle.DataAccess.dll을 설치합니다.

### <a name="problem-2-datetime-formatting"></a>문제 2: 날짜/시간 서식

**오류 메시지**

    Message=Operation failed in Oracle Database with the following error: 'ORA-01861: literal does not match format string'.,Source=,''Type=Oracle.DataAccess.Client.OracleException,Message=ORA-01861: literal does not match format string,Source=Oracle Data Provider for .NET,'.

**해결 방법**

Oracle 데이터베이스에서 날짜가 구성된 방식에 따라 복사 작업의 쿼리 문자열을 조정해야 합니다. 다음은 **to_date** 함수를 사용한 예입니다.

    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= to_date(\\'{0:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') AND timestampcolumn < to_date(\\'{1:MM-dd-yyyy HH:mm}\\',\\'MM/DD/YYYY HH24:MI\\') ', WindowStart, WindowEnd)"


## <a name="type-mapping-for-oracle"></a>Oracle에 대한 형식 매핑

[데이터 이동 작업](data-factory-data-movement-activities.md)에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법을 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환합니다.
2. .NET 형식에서 네이티브 싱크 형식으로 변환합니다.

Oracle에서 데이터를 이동할 때 Oracle 데이터 형식에서 .NET 형식으로, 그리고 그 반대의 경우 다음 매핑을 사용합니다.

| Oracle 데이터 형식 | .NET Framework 데이터 형식 |
| --- | --- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(Microsoft 드라이버를 사용하는 경우 Oracle 10g 이상 버전에서만 지원됩니다.) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| INTEGER |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| INTERVAL YEAR TO MONTH |Int32 |
| INTERVAL DAY TO SECOND |timespan |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, 문자열(전체 자릿수의 경우 > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |DateTime |
| TIMESTAMP WITH TIME ZONE |DateTime |
| UNSIGNED INTEGER |NUMBER |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> 데이터 형식 **INTERVAL YEAR TO MONTH** 및 **INTERVAL DAY TO SECOND**는 Microsoft 드라이버를 사용할 때 지원되지 않습니다.

## <a name="map-source-to-sink-columns"></a>원본을 싱크 열로 매핑

원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하는 것에 관해 자세히 알아보려면 [Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="repeatable-read-from-relational-sources"></a>관계형 원본에서 반복 가능한 읽기

관계형 데이터 저장소에서 데이터를 복사할 때 의도하지 않은 결과를 방지하기 위해 반복성을 명심해야 합니다. Azure Data Factory에서는 조각을 수동으로 다시 실행할 수 있습니다. 또한 오류가 발생하면 조각을 다시 실행하도록 데이터 세트에 대한 재시도 정책을 구성할 수 있습니다. 어느 쪽이든 조각이 재실행되는 경우 조각이 실행되는 횟수에 관계없이 같은 데이터를 읽어야 합니다. 자세한 내용은 [관계형 원본에서 반복 가능한 읽기](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)를 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝

Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요인에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요. 최적화하는 다양한 방법에 대해 알아볼 수 있습니다.
