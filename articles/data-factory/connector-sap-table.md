---
title: SAP 테이블에서 데이터 복사
description: Azure Data Factory 파이프라인에서 복사 작업을 사용 하 여 SAP 테이블에서 지원 되는 싱크 데이터 저장소로 데이터를 복사 하는 방법에 대해 알아봅니다.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/02/2019
ms.openlocfilehash: fd363f7b685db5e309827a0c5e635264e676b388
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74926188"
---
# <a name="copy-data-from-an-sap-table-by-using-azure-data-factory"></a>Azure Data Factory를 사용 하 여 SAP 테이블에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용 하 여 SAP 테이블에서 데이터를 복사 하는 방법을 설명 합니다. 자세한 내용은 [복사 작업 개요](copy-activity-overview.md)를 참조 하세요.

>[!TIP]
>SAP 데이터 통합 시나리오에 대한 ADF의 전반적인 지원에 대한 자세한 내용은 [Azure Data Factory 백서를 사용 하 여 sap 데이터 통합](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) 에서 자세한 소개, comparsion 및 지침을 참조 하세요.

## <a name="supported-capabilities"></a>지원되는 기능

이 SAP table 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

SAP 테이블에서 지원 되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본 또는 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조 하세요.

특히이 SAP 테이블 커넥터는 다음을 지원 합니다.

- 다음의 SAP 테이블에서 데이터 복사:

  - Sap ERP Central 구성 요소 (SAP ECC) 버전 7.01 이상 (2015 이후에 릴리스된 최근 SAP 지원 패키지 스택)
  - SAP Business Warehouse (SAP BW) 버전 7.01 이상 (2015 이후 출시 된 최근 SAP 지원 패키지 스택)
  - SAP S/4HANA.
  - SAP Business Suite 버전 7.01 이상 (2015 이후에 출시 된 최근 SAP 지원 패키지 스택)의 기타 제품.

- SAP 투명 테이블, 풀링된 테이블, 클러스터형 테이블 및 뷰 모두에서 데이터를 복사 합니다.
- SNC가 구성 된 경우 기본 인증 또는 SNC (Secure Network Communications)를 사용 하 여 데이터를 복사 합니다.
- SAP 응용 프로그램 서버 또는 SAP 메시지 서버에 연결 하는 중입니다.

## <a name="prerequisites"></a>전제 조건

이 SAP 테이블 커넥터를 사용 하려면 다음을 수행 해야 합니다.

- 자체 호스팅 통합 런타임 (버전 3.17 이상)을 설정 합니다. 자세한 내용은 [자체 호스팅 통합 런타임 만들기 및 구성](create-self-hosted-integration-runtime.md)을 참조 하세요.

- SAP의 웹 사이트에서 [Microsoft .NET 3.0 용 64 비트 Sap 커넥터](https://support.sap.com/en/product/connectors/msnet.html) 를 다운로드 하 고 자체 호스팅 통합 런타임 컴퓨터에 설치 합니다. 설치 하는 동안 **선택적 설정 단계** 창에서 **GAC에 어셈블리 설치** 옵션을 선택 했는지 확인 합니다.

  ![.NET 용 SAP Connector 설치](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory SAP 테이블 커넥터에서 사용 되는 SAP 사용자에 게는 다음 사용 권한이 있어야 합니다.

  - RFC (원격 함수 호출) 대상을 사용 하기 위한 권한 부여입니다.
  - S_SDSAUTH 권한 부여 개체의 실행 작업에 대한 사용 권한입니다.

## <a name="get-started"></a>시작하기

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP 테이블 커넥터와 관련 된 Data Factory 엔터티를 정의 하는 데 사용 되는 속성에 대해 자세히 설명 합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

다음은 SAP BW 열려 있는 허브 연결 된 서비스에 대해 지원 되는 속성입니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| `type` | 합니다 `type` 속성으로 설정 되어 있어야 `SapTable`합니다. | yes |
| `server` | SAP 인스턴스가 있는 서버의 이름입니다.<br/>를 사용 하 여 SAP 응용 프로그램 서버에 연결 합니다. | 아닙니다. |
| `systemNumber` | SAP 시스템의 시스템 번호입니다.<br/>를 사용 하 여 SAP 응용 프로그램 서버에 연결 합니다.<br/>허용 되는 값: 문자열로 표현 되는 두 자리 10 진수입니다. | 아닙니다. |
| `messageServer` | SAP 메시지 서버의 호스트 이름입니다.<br/>를 사용 하 여 SAP 메시지 서버에 연결 합니다. | 아닙니다. |
| `messageServerService` | 메시지 서버의 서비스 이름 또는 포트 번호입니다.<br/>를 사용 하 여 SAP 메시지 서버에 연결 합니다. | 아닙니다. |
| `systemId` | 테이블이 있는 SAP 시스템의 ID입니다.<br/>를 사용 하 여 SAP 메시지 서버에 연결 합니다. | 아닙니다. |
| `logonGroup` | SAP 시스템에 대한 로그온 그룹입니다.<br/>를 사용 하 여 SAP 메시지 서버에 연결 합니다. | 아닙니다. |
| `clientId` | SAP 시스템의 클라이언트 ID입니다.<br/>허용 되는 값: 문자열로 표현 되는 세 자리 10 진수입니다. | yes |
| `language` | SAP 시스템에서 사용 하는 언어입니다.<br/>기본값은 `EN`입니다.| 아닙니다. |
| `userName` | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름입니다. | yes |
| `password` | 이 사용자에 대한 암호입니다. 이 필드를 `SecureString` 형식으로 표시 하 여 Data Factory에 안전 하 게 저장 하거나 [Azure Key Vault에 저장 된 암호를 참조](store-credentials-in-key-vault.md)합니다. | yes |
| `sncMode` | 테이블이 있는 SAP 서버에 액세스 하기 위한 SNC 활성화 표시기입니다.<br/>SNC를 사용 하 여 SAP 서버에 연결 하려는 경우에 사용 합니다.<br/>허용 되는 값은 `0` (해제, 기본값) 또는 `1` (설정)입니다. | 아닙니다. |
| `sncMyName` | 테이블이 있는 SAP 서버에 액세스 하기 위한 개시자의 SNC 이름입니다.<br/>`sncMode`가 on 인 경우 적용 됩니다. | 아닙니다. |
| `sncPartnerName` | 테이블이 있는 SAP 서버에 액세스 하기 위한 통신 파트너의 SNC 이름입니다.<br/>`sncMode`가 on 인 경우 적용 됩니다. | 아닙니다. |
| `sncLibraryPath` | 테이블이 있는 SAP 서버에 액세스할 외부 보안 제품의 라이브러리입니다.<br/>`sncMode`가 on 인 경우 적용 됩니다. | 아닙니다. |
| `sncQop` | 적용할 보호 수준의 SNC입니다.<br/>`sncMode`가 On 인 경우 적용 됩니다. <br/>허용 되는 값은 `1` (인증), `2` (무결성), `3` (개인 정보), `8` (기본값), `9` (최대)입니다. | 아닙니다. |
| `connectVia` | 데이터 저장소에 연결하는 데 사용할 [통합 런타임](concepts-integration-runtime.md)입니다. 앞서 설명한 [필수 구성 요소](#prerequisites)에서 설명한 대로 자체 호스팅 통합 런타임이 필요 합니다. |yes |

**예 1: SAP 응용 프로그램 서버에 연결**

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-2-connect-to-an-sap-message-server"></a>예 2: SAP 메시지 서버에 연결

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "messageServer": "<message server name>",
            "messageServerService": "<service name or port>",
            "systemId": "<system ID>",
            "logonGroup": "<logon group>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            }
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="example-3-connect-by-using-snc"></a>예 3: SNC를 사용 하 여 연결

```json
{
    "name": "SapTableLinkedService",
    "properties": {
        "type": "SapTable",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client ID>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
            },
            "sncMode": 1,
            "sncMyName": "<SNC myname>",
            "sncPartnerName": "<SNC partner name>",
            "sncLibraryPath": "<SNC library path>",
            "sncQop": "8"
        },
        "connectVia": {
            "referenceName": "<name of integration runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 집합 정의에 대한 섹션 및 속성의 전체 목록은 [데이터 집합](concepts-datasets-linked-services.md)을 참조 하세요. 다음 섹션에서는 SAP 테이블 데이터 집합에서 지 원하는 속성의 목록을 제공 합니다.

SAP BW 연결 된 허브 연결 된 서비스에서 데이터를 복사 하려면 다음 속성이 지원 됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| `type` | 합니다 `type` 속성으로 설정 되어 있어야 `SapTableResource`합니다. | yes |
| `tableName` | 데이터를 복사할 SAP 테이블의 이름입니다. | yes |

### <a name="example"></a>예제

```json
{
    "name": "SAPTableDataset",
    "properties": {
        "type": "SapTableResource",
        "typeProperties": {
            "tableName": "<SAP table name>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP table linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 대한 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md)을 참조 하세요. 다음 섹션에서는 SAP 테이블 원본에서 지 원하는 속성의 목록을 제공 합니다.

### <a name="sap-table-as-source"></a>SAP 테이블을 원본으로

SAP 테이블에서 데이터를 복사 하기 위해 지원 되는 속성은 다음과 같습니다.

| 자산                         | 설명                                                  | 필수 |
| :------------------------------- | :----------------------------------------------------------- | :------- |
| `type`                             | 합니다 `type` 속성으로 설정 되어 있어야 `SapTableSource`합니다.         | yes      |
| `rowCount`                         | 검색할 행의 수입니다.                              | 아닙니다.       |
| `rfcTableFields`                   | SAP 테이블에서 복사할 필드 (열)입니다. 예: `column0, column1` | 아닙니다.       |
| `rfcTableOptions`                  | SAP 테이블의 행을 필터링 하는 옵션입니다. 예: `COLUMN0 EQ 'SOMEVALUE'` 이 문서의 뒷부분에 나오는 SAP 쿼리 연산자 표를 참조 하세요. | 아닙니다.       |
| `customRfcReadTableFunctionModule` | SAP 테이블에서 데이터를 읽는 데 사용할 수 있는 사용자 지정 RFC 함수 모듈입니다.<br>사용자 지정 RFC 함수 모듈을 사용 하 여 SAP 시스템에서 데이터를 검색 하 여 Data Factory로 반환 하는 방법을 정의할 수 있습니다. 사용자 지정 함수 모듈에는 Data Factory에서 사용 되는 기본 인터페이스인 `/SAPDS/RFC_READ_TABLE2`와 유사한 인터페이스 (가져오기, 내보내기, 테이블)가 구현 되어 있어야 합니다. | 아닙니다.       |
| `partitionOption`                  | SAP 테이블에서 읽을 파티션 메커니즘입니다. 지원되는 옵션은 다음과 같습니다. <ul><li>`None`</li><li>`PartitionOnInt` (`0000012345`와 같이 왼쪽에서 안쪽 여백이 0 인 법선 정수 또는 정수 값)</li><li>`PartitionOnCalendarYear` ("YYYY" 형식의 4 자리)</li><li>`PartitionOnCalendarMonth` ("YYYYMM" 형식의 6 자리 숫자)</li><li>`PartitionOnCalendarDate` ("YYYYMMDD" 형식의 8 진수)</li></ul> | 아닙니다.       |
| `partitionColumnName`              | 데이터를 분할 하는 데 사용 되는 열의 이름입니다.                | 아닙니다.       |
| `partitionUpperBound`              | 분할을 계속 하는 데 사용 되는 `partitionColumnName`에 지정 된 열의 최대값입니다. | 아닙니다.       |
| `partitionLowerBound`              | 분할을 계속 하는 데 사용 되는 `partitionColumnName`에 지정 된 열의 최소값입니다. | 아닙니다.       |
| `maxPartitionsNumber`              | 데이터를 분할할 최대 파티션 수입니다.     | 아닙니다.       |

>[!TIP]
>SAP 테이블에 몇 억 개의 행과 같은 많은 양의 데이터가 있는 경우 `partitionOption` 및 `partitionSetting`를 사용 하 여 데이터를 더 작은 파티션으로 분할 합니다. 이 경우 파티션당 데이터를 읽고, 각 데이터 파티션은 단일 RFC 호출을 통해 SAP 서버에서 검색 됩니다.<br/>
<br/>
>예를 들어 `partitionOnInt`로 `partitionOption`을 수행 하면 각 파티션의 행 수가 다음 수식으로 계산 됩니다. (총 행 수는 `partitionUpperBound`와 `partitionLowerBound`)/`maxPartitionsNumber`입니다.<br/>
<br/>
>복사 속도를 높이기 위해 데이터 파티션을 병렬로 로드 하려면 복사 작업의 [`parallelCopies`](copy-activity-performance.md#parallel-copy) 설정에 따라 병렬 수준이 제어 됩니다. 예를 들어 `parallelCopies`를 4로 설정 하는 경우 Data Factory는 지정 된 파티션 옵션과 설정을 기반으로 4 개의 쿼리를 동시에 생성 하 고 실행 하며 각 쿼리는 SAP 테이블에서 데이터의 일부를 검색 합니다. `parallelCopies` 속성 값의 배수를 `maxPartitionsNumber` 하는 것이 좋습니다. 파일 기반 데이터 저장소로 데이터를 복사 하는 경우에는 폴더에 여러 파일로 기록 (폴더 이름만 지정) 하는 것이 좋습니다 .이 경우에는 단일 파일에 쓰는 것 보다 성능이 좋습니다.

`rfcTableOptions`에서 다음과 같은 일반적인 SAP 쿼리 연산자를 사용 하 여 행을 필터링 할 수 있습니다.

| 연산자 | 설명 |
| :------- | :------- |
| `EQ` | 다음과 같음 |
| `NE` | 다음과 같지 않음 |
| `LT` | 보다 작음 |
| `LE` | 다음보다 작거나 같음 |
| `GT` | 보다 큼 |
| `GE` | 다음보다 크거나 같음 |
| `LIKE` | `LIKE 'Emma%'`와 마찬가지로 |

### <a name="example"></a>예제

```json
"activities":[
    {
        "name": "CopyFromSAPTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP table input dataset name>",
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
                "type": "SapTableSource",
                "partitionOption": "PartitionOnInt",
                "partitionSettings": {
                     "partitionColumnName": "<partition column name>",
                     "partitionUpperBound": "2000",
                     "partitionLowerBound": "1",
                     "maxPartitionsNumber": 500
                 }
            },
            "sink": {
                "type": "<sink type>"
            },
            "parallelCopies": 4
        }
    }
]
```

## <a name="data-type-mappings-for-an-sap-table"></a>SAP 테이블에 대한 데이터 형식 매핑

SAP 테이블에서 데이터를 복사 하는 경우 SAP 테이블 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로 다음 매핑이 사용 됩니다. 복사 활동에서 원본 스키마와 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP ABAP 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| `C` (문자열) | `String` |
| `I` (정수) | `Int32` |
| `F` (부동 소수점) | `Double` |
| `D` (Date) | `String` |
| `T` (시간) | `String` |
| `P` (BCD 압축, 통화, 10 진수, 수량) | `Decimal` |
| `N` (숫자) | `String` |
| `X` (이진 및 원시) | `String` |

## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.


## <a name="next-steps"></a>다음 단계

Azure Data Factory의 복사 작업에서 원본 및 싱크로 지원 되는 데이터 저장소 목록은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조 하세요.
