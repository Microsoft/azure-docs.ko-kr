---
title: Azure Data Factory를 사용하여 SAP Business Warehouse에서 데이터 이동 | Microsoft Docs
description: Azure Data Factory를 사용하여 SAP Business Warehouse에서 데이터를 이동하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 1823c2ec28b342d41371eb6677e0330d7f885087
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61258661"
---
# <a name="move-data-from-sap-business-warehouse-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SAP Business Warehouse에서 데이터 이동
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](data-factory-sap-business-warehouse-connector.md)
> * [버전 2(현재 버전)](../connector-sap-business-warehouse.md)

> [!NOTE]
> 이 문서의 내용은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 SAP Business Warehouse 커넥터](../connector-sap-business-warehouse.md)를 참조하세요.


이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 온-프레미스 SAP BW(Business Warehouse)에서 데이터를 이동하는 방법을 설명합니다. 이 문서는 복사 작업을 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 작업](data-factory-data-movement-activities.md) 문서를 기반으로 합니다.

온-프레미스 SAP Business Warehouse 데이터 저장소의 데이터를 지원되는 싱크 데이터 저장소로 복사할 수 있습니다. 복사 작업의 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 테이블을 참조하세요. 현재 데이터 팩터리는 다른 데이터 저장소에서 SAP Business Warehouse로 데이터를 이동하는 것은 지원하지 않고 SAP Business Warehouse에서 다른 데이터 저장소로 데이터를 이동하는 것만 지원합니다. 

## <a name="supported-versions-and-installation"></a>지원되는 버전 및 설치
이 커넥터는 SAP Business Warehouse 버전 7.x를 지원합니다. MDX 쿼리를 사용하여 InfoCubes 및 QueryCubes(BEx 쿼리 포함)에서 데이터를 복사하도록 지원합니다.

SAP BW 인스턴스에 대한 연결을 사용하도록 설정하려면 다음 구성 요소를 설치합니다.
- **데이터 관리 게이트웨이**: Data Factory 서비스는 데이터 관리 게이트웨이라는 구성 요소를 사용하여 온-프레미스 데이터 저장소(SAP Business Warehouse 포함)에 연결을 지원합니다. 데이터 관리 게이트웨이 및 게이트웨이 설정에 대한 단계별 지침을 알아보려면 [온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요. 게이트웨이는 SAP Business Warehouse가 Azure IaaS 가상 머신(VM)에 호스팅되더라도 필요합니다. 게이트웨이를 데이터베이스에 연결할 수 있는 한 데이터 저장소와 동일한 VM 또는 다른 VM에 게이트웨이를 설치할 수 있습니다.
- 게이트웨이 컴퓨터의 **SAP NetWeaver 라이브러리**. SAP Netweaver 라이브러리는 SAP 관리자를 통해 구하거나 [SAP 소프트웨어 다운로드 센터](https://support.sap.com/swdc)에서 바로 구할 수 있습니다. 최신 버전에 대한 다운로드 위치를 찾으려면 **SAP Note #1025361**을 검색합니다. SAP NetWeaver 라이브러리(32비트 또는 64비트)의 아키텍처가 게이트웨이 설치와 일치하는지 확인합니다. 그런 다음 SAP Note에 따라 SAP NetWeaver RFC SDK에 포함된 모든 파일을 설치합니다. SAP NetWeaver 라이브러리는 SAP Client Tools 설치에도 포함됩니다.

> [!TIP]
> NetWeaver RFC SDK에서 추출된 dll을 system32 폴더에 넣습니다.

## <a name="getting-started"></a>시작
여러 도구/API를 사용하여 온-프레미스 Cassandra 데이터 저장소의 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다. 

- 파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사**를 사용하는 것입니다. 단계별 지침은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습을 볼 수 있습니다. 
- 또한 다음 도구를 사용하여 파이프라인을 만들 수 있습니다. **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 및 **REST API** 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요. 

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스**를 만듭니다.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트**를 만듭니다. 
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인**을 만듭니다. 

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다.  온-프레미스 SAP Business Warehouse의 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의에 대한 샘플은 이 문서의 [JSON의 예: SAP Business Warehouse에서 Azure Blob에 데이터 복사](#json-example-copy-data-from-sap-business-warehouse-to-azure-blob)를 참조하세요. 

다음 섹션에서는 SAP BW 데이터 저장소에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 테이블은 SAP Business Warehouse(BW) 연결된 서비스에 특정의 JSON 요소에 대한 설명을 제공합니다.

자산 | 설명 | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
서버 | SAP BW 인스턴스가 상주하는 서버의 이름. | 문자열 | 예
systemNumber | SAP BW 시스템의 시스템 번호. | 문자열로 표현되는 두 자리 10진수. | 예
clientId | SAP W 시스템에 있는 클라이언트의 클라이언트 ID. | 문자열로 표현되는 세 자리 10진수. | 예
사용자 이름 | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름 | 문자열 | 예
암호 | 사용자에 대한 암호입니다. | 문자열 | 예
gatewayName | Data Factory 서비스가 온-프레미스 SAP BW 인스턴스에 연결하는 데 사용해야 하는 게이트웨이의 이름. | 문자열 | 예
encryptedCredential | 암호화된 자격 증명 문자열. | 문자열 | 아닙니다.

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **RelationalTable** 형식의 SAP BW 데이터 세트에 대해 지원되는 type별 속성은 없습니다. 


## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

활동 복사의 원본이 **RelationalSource**(SAP BW 포함) 형식인 경우 typeProperties 섹션에서 다음과 같은 속성을 사용할 수 있습니다.

| 자산 | 설명 | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| 쿼리 | SAP BW 인스턴스에서 데이터를 읽을 MDX 쿼리를 지정합니다. | MDX 쿼리. | 예 |


## <a name="json-example-copy-data-from-sap-business-warehouse-to-azure-blob"></a>JSON 예제: SAP Business Warehouse에서 Azure Blob에 데이터 복사
다음 예제에서는 [Azure 포털](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) 또는 [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)을 사용하여 파이프라인을 만드는 데 사용할 수 있는 샘플 JSON 정의를 제공합니다. 이 샘플은 온-프레미스 SAP Business Warehouse에서 Azure Blob Storage로 데이터를 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 **여기** 에 설명한 싱크로 [직접](data-factory-data-movement-activities.md#supported-data-stores-and-formats) 데이터를 복사할 수 있습니다.  

> [!IMPORTANT]
> 이 샘플은 JSON 코드 조각을 제공합니다. 데이터 팩터리를 만들기 위한 단계별 지침은 포함하지 않습니다. 단계별 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하세요.

이 샘플에는 다음 데이터 팩터리 엔터티가 있습니다.

1. [SapBw](#linked-service-properties) 형식의 연결된 서비스
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
3. [RelationalTable](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
5. [RelationalSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)

샘플은 SAP Business Warehouse 인스턴스에서 Azure Blob으로 매시간 데이터를 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

첫 번째 단계로 데이터 관리 게이트웨이를 설정합니다. 해당 지침은 [온-프레미스 위치와 클라우드 간에 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서에 나와 있습니다.

### <a name="sap-business-warehouse-linked-service"></a>SAP Business Warehouse 연결된 서비스
이 연결된 서비스는 SAP BW 인스턴스를 데이터 팩터리에 연결합니다. type 속성은 **SapBw**로 설정됩니다. typeProperties 섹션은 SAP BW 인스턴스에 대한 연결 정보를 제공합니다. 

```json
{
    "name": "SapBwLinkedService",
    "properties":
    {
        "type": "SapBw",
        "typeProperties":
        {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Azure Storage 연결된 서비스
이 연결된 서비스는 Azure Storage 계정을 데이터 팩터리에 연결합니다. type 속성은 **AzureStorage**로 설정됩니다. typeProperties 섹션은 Azure Storage 계정에 대한 연결 정보를 제공합니다.

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

### <a name="sap-bw-input-dataset"></a>SAP BW 입력 데이터 세트
이 데이터 세트는 SAP Business Warehouse 데이터 세트를 정의합니다. Data Factory 데이터 세트의 type은 **RelationalTable**로 설정합니다. 현재 SAP BW 데이터 세트에 대한 type별 속성은 지정하지 않습니다. 활동 복사 정의의 쿼리는 SAP BW 인스턴스에서 읽을 데이터를 지정합니다. 

external 속성을 true로 설정하면 테이블이 데이터 팩터리의 외부에 있으며 데이터 팩터리의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

frequency 및 interval 속성은 일정을 정의합니다. 이런 경우 SAP BW 인스턴스에서 매시간 데이터를 읽습니다. 

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```



### <a name="azure-blob-output-dataset"></a>Azure Blob 출력 데이터 세트
이 데이터 세트는 출력 Azure Blob 데이터 세트를 정의합니다. type 속성은 AzureBlob으로 설정됩니다. typeProperties 섹션은 SAP BW 인스턴스에서 복사한 데이터가 저장되는 위치를 제공합니다. 데이터는 1시간마다 새 Blob에 기록됩니다(frequency: hour, interval: 1). Blob에 대한 폴더 경로는 처리 중인 조각의 시작 시간에 기반하여 동적으로 평가됩니다. 폴더 경로는 시작 시간에서 연도, 월, 일 및 시간 부분을 사용합니다.

```json
{
    "name": "AzureBlobDataSet",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/sapbw/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
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
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="pipeline-with-copy-activity"></a>복사 작업을 포함하는 파이프라인
파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **RelationalSource**로 설정되고(SAP BW 원본의 경우) **sink** 형식은 **BlobSink**로 설정됩니다. **query** 속성에 지정된 쿼리는 과거 한 시간에서 복사할 데이터를 선택합니다.

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "RelationalSource",
                        "query": "<MDX query for SAP BW>"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "SapBwDataset"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobDataSet"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SapBwToBlob"
            }
        ],
        "start": "2017-03-01T18:00:00Z",
        "end": "2017-03-01T19:00:00Z"
    }
}
```



### <a name="type-mapping-for-sap-bw"></a>SAP BW에 대한 형식 매핑
[데이터 이동 활동](data-factory-data-movement-activities.md) 문서에서 설명한 것처럼 복사 작업은 다음 2단계 접근 방법 사용하여 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

SAP BW의 데이터를 이동하는 경우 SAP BW 형식에서 .NET 형식으로 다음 매핑이 사용됩니다.

ABAP 사전의 데이터 형식 | .NET 데이터 형식
-------------------------------- | --------------
ACCP |  Int
CHAR | String
CLNT | String
CURR | Decimal
CUKY | String
DEC | Decimal
FLTP | Double
INT1 | Byte
INT2 | Int16
INT4 | Int
LANG | String
LCHR | String
LRAW | Byte[]
PREC | Int16
QUAN | Decimal
RAW | Byte[]
RAWSTRING | Byte[]
STRING | String
단위 | String
DATS | String
NUMC | String
TIMS | String

> [!NOTE]
> 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하려면 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.


## <a name="map-source-to-sink-columns"></a>원본을 싱크 열로 매핑
원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하는 방법은 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="repeatable-read-from-relational-sources"></a>관계형 원본에서 반복 가능한 읽기
관계형 데이터 저장소에서 데이터를 복사할 때는 의도치 않는 결과를 방지하기 위해 반복성을 염두에 두어야 합니다. Azure Data Factory에서는 조각을 수동으로 다시 실행할 수 있습니다. 또한 오류가 발생하면 조각을 다시 실행하도록 데이터 세트에 대한 재시도 정책을 구성할 수 있습니다. 어느 쪽이든 조각이 재실행되는 경우 조각이 실행되는 횟수에 관계없이 같은 데이터를 읽어야 합니다. [관계형 원본에서 반복 가능한 읽기](data-factory-repeatable-copy.md#repeatable-read-from-relational-sources)를 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.
