---
title: Azure Data Factory를 사용하여 Open Hub를 통해 SAP Business Warehouse에서 데이터 복사 | Microsoft Docs
description: Azure Data Factory 파이프라인의 복사 작업을 사용하여 Open Hub를 통해 SAP BW(Business Warehouse)에서 지원되는 싱크 데이터 저장소로 데이터를 복사하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: WenJason
manager: digimobile
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
origin.date: 03/08/2019
ms.date: 04/22/2019
ms.author: v-jay
ms.openlocfilehash: c64842dc89c9519c738701558f510940f4cc148d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60848897"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Azure Data Factory를 사용하여 Open Hub를 통해 SAP Business Warehouse에서 데이터 복사

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 SAP BW(Business Warehouse)에서 Open Hub를 통해 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

## <a name="supported-capabilities"></a>지원되는 기능

Open Hub를 통해 SAP Business Warehouse에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

이 SAP Business Warehouse 커넥터는 구체적으로 다음 작업을 지원합니다.

- SAP Business Warehouse **7.01 또는 (스택의 상위에 최신 SAP 지원 패키지 2015 년 이후에 출시) 버전**합니다.
- Open Hub 대상 로컬 테이블(DSO, InfoCube, MultiProvider, DataSource 아래에 있을 수 있음)을 통해 데이터 복사
- 기본 인증을 사용하여 데이터를 복사합니다.
- 애플리케이션 서버에 연결

## <a name="sap-bw-open-hub-integration"></a>SAP BW Open Hub 통합 

[SAP BW Open Hub Service](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service)를 사용하면 SAP BW에서 데이터를 효율적으로 추출할 수 있습니다. 다음 다이어그램은 고객 SAP 시스템의 일반적인 흐름 중 하나를 보여줍니다. 여기서 데이터는 SAP ECC -> PSA -> DSO -> Cube로 이동됩니다.

SAP BW OHD(Open Hub 대상)에 따라 SAP 데이터가 릴레이되는 대상이 정의됩니다. 예를 들어 DSO, InfoCube, DataSource 등 오픈 허브 데이터 원본으로 하 여 SAP 데이터 전송 프로세스 (DTP)를 지원 되는 모든 개체를 사용할 수 있습니다. 릴레이되는 데이터가 저장되는 Open Hub 대상 유형은 데이터베이스 테이블(로컬 또는 원격) 및 플랫 파일일 수 있습니다. 이 SAP BW Open Hub 커넥터를 사용하면 BW에서 OHD 로컬 테이블의 데이터를 복사할 수 있습니다. 다른 형식을 사용하는 경우에는 다른 커넥터를 통해 데이터베이스 또는 파일 시스템에 직접 연결할 수 있습니다.

![SAP BW Open Hub](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>델타 추출 흐름

ADF SAP BW Open Hub 커넥터는 두 가지 선택적 속성을 제공 합니다. `excludeLastRequest` 고 `baseRequestId` 오픈 허브에서 델타 로드를 처리 하려면 사용할 수 있는 합니다. 

- **excludeLastRequestId**: 마지막 요청의 레코드를 제외할지 여부입니다. 기본값은 true입니다. 
- **baseRequestId**: 델타 로드의 요청 ID입니다. 설정 되 면이 속성의 값 보다 큰 요청 Id 사용 하 여 데이터에만 검색 됩니다. 

전반적으로 SAP InfoProviders에서 추출 Azure 데이터 팩터리 (ADF)를 2 단계로 이루어집니다. 

1. **SAP BW 데이터 전송 프로세스 (DTP)** 이 단계는 SAP BW InfoProvider에서 SAP BW Open Hub 테이블에 데이터를 복사 

1. **ADF 데이터 복사본** 이 단계에서는 오픈 허브 테이블 ADF 커넥터에서 읽기 

![델타 추출 흐름](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

첫 번째 단계에서는 DTP 실행 됩니다. 각 실행을 새 SAP 요청 ID를 만듭니다. 요청 ID 오픈 허브 테이블에 저장 됩니다 및 그런 다음는 데 ADF 커넥터에서 델타를 식별 합니다. 두 단계를 비동기적으로 실행: DTP를 SAP에 의해 트리거되는 및 ADF 데이터 복사를 ADF를 통해 트리거됩니다. 

기본적으로 ADF는 읽지 않습니다 최신 델타 오픈 허브 테이블에서 ("마지막 요청 제외" 옵션은 true). 이로써 귀하를 ADF에서 데이터 (마지막 델타가 없음) 오픈 허브 테이블의 데이터를 사용 하 여 최신 100% 아닙니다. 반환 시이 절차를 통해 행이 없는 비동기 추출 때문일 잃기 합니다. ADF는 DTP는 여전히 동일한 테이블에 작성 하는 동안 오픈 허브 테이블을 읽는 경우에 작동. 

일반적으로 마지막 실행 ADF에서 준비 데이터 저장소 (예: 위의 다이어그램에서 Azure Blob)에서 최대 복사 요청 ID를 저장 합니다. 따라서 동일한 요청에서 읽을 수 없습니다를 두 번째로 ADF 후속 실행에서 합니다. 한편 오픈 허브 테이블에서 데이터 자동으로 삭제 되지 않습니다.

적절 한 델타에 대 한 처리 되지 동일한 오픈 허브 테이블 다른 Dtp Id 요청에 있습니다. 따라서 만들어서는 안 둘 이상의 DTP 각 오픈 허브 대상을 (OHD)에 대 한 합니다. 동일한 InfoProvider에서 전체 및 델타 추출, 필요한 경우 동일한 InfoProvider에 대 한 두 OHDs를 만들어야 합니다. 

## <a name="prerequisites"></a>필수 조건

이 SAP Business Warehouse 커넥터를 사용하려면 다음 작업을 수행해야 합니다.

- 버전 3.13 이상의 자체 호스팅 통합 런타임을 설정합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.

- SAP 웹 사이트에서 **64비트 [SAP .NET Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)** 을 다운로드하여 자체 호스팅 IR 컴퓨터에 설치합니다. 설치 시 선택적 설정 단계 창에서 다음 이미지에 나와 있는 것처럼 **GAC에 어셈블리 설치** 옵션을 선택하세요. 

    ![SAP .NET Connector 설치](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Data Factory BW 커넥터에서 사용 중인 SAP 사용자에게는 다음 권한이 있어야 합니다. 

    - RFC 및 SAP BW에 대해 부여된 권한 
    - 권한 부여 개체 "S_SDSAUTH"의 "실행" 작업에 대한 권한

- "기술 키" 옵션을 선택하여 SAP Open Hub 대상 유형을 **데이터베이스 테이블**로 만듭니다.  테이블에서 데이터 삭제는 선택을 취소한 상태로 유지하는 것이 좋습니다(반드시 선택 취소해야 하는 것은 아님). DTP(직접 실행 또는 기존 프로세스 체인에 통합)를 활용해 큐브 등의 선택한 원본 개체에서 Open Hub 대상 테이블로 데이터를 전송합니다.

## <a name="getting-started"></a>시작

> [!TIP]
>
> SAP BW 오픈 허브 커넥터를 사용 하 여 연습을 참조 하세요 [Azure Data Factory를 사용 하 여 SAP Business Warehouse (BW)에서 데이터를 로드](load-sap-bw-data.md)합니다.

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 SAP Business Warehouse Open Hub 커넥터와 관련된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

SAP Business Warehouse Open Hub 연결된 서비스에 지원되는 속성은 다음과 같습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | type 속성을 다음으로 설정해야 합니다. **SapOpenHub** | 예 |
| 서버 | SAP BW 인스턴스가 상주하는 서버의 이름. | 예 |
| systemNumber | SAP BW 시스템의 시스템 번호.<br/>허용되는 값: 문자열로 표현되는 두 자리 10진수 | 예 |
| clientId | SAP W 시스템에 있는 클라이언트의 클라이언트 ID.<br/>허용되는 값: 문자열로 표현되는 세 자리 10진수 | 예 |
| language | SAP 시스템에서 사용하는 언어입니다. | No(기본값: **EN**)|
| userName | SAP 서버에 대한 액세스 권한이 있는 사용자의 이름입니다. | 예. |
| 암호 | 사용자에 대한 암호입니다. 이 필드를 SecureString으로 표시하여 Data Factory에 안전하게 저장하거나 [Azure Key Vault에 저장되는 비밀을 참조](store-credentials-in-key-vault.md)합니다. | 예 |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |예. |

**예제:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
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

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 Salesforce 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

SAP BW Open Hub 간에 데이터를 복사하려면 데이터 세트의 형식 속성을 **SapOpenHubTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 | 이 속성은 **SapOpenHubTable**로 설정해야 합니다.  | 예 |
| openHubDestinationName | 복사할 데이터가 있는 Open Hub 대상의 이름입니다. | 예 |
| excludeLastRequest | 마지막 요청의 레코드를 제외할지 여부입니다. | No(기본값: **true**) |
| baseRequestId | 델타 로드의 요청 ID입니다. 설정하는 경우 requestId가 이 속성의 값**보다 큰** 데이터만 검색됩니다.  | 아니요 |

>[!TIP]
>항상 테이블의 기존 데이터를 모두 로드하고 덮어쓰거나, 테스트를 위해 DTP를 한 번만 실행하는 등 Open Hub 테이블에 단일 요청 ID를 통해 생성된 데이터만 포함되는 경우에는 "excludeLastRequest" 옵션 선택을 취소하여 데이터를 외부로 복사해야 합니다.

**예제:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 SAP BW Open Hub 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="sap-bw-open-hub-as-source"></a>SAP BW Open Hub(원본)

SAP BW Open Hub에서 데이터를 복사하려면 복사 작업의 원본 형식을 **SapOpenHubSource**로 설정합니다. 복사 작업 **source** 섹션에는 추가 형식별 속성이 필요하지 않습니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>SAP BW Open Hub에 대한 데이터 형식 매핑

SAP BW Open Hub에서 데이터를 복사할 때는 SAP BW 데이터 형식에서 Azure Data Factory 중간 데이터 형식으로의 다음과 같은 매핑이 사용됩니다. 복사 작업에서 원본 스키마 및 데이터 형식을 싱크에 매핑하는 방법에 대한 자세한 내용은 [스키마 및 데이터 형식 매핑](copy-activity-schema-and-type-mapping.md)을 참조하세요.

| SAP ABAP 형식 | Data Factory 중간 데이터 형식 |
|:--- |:--- |
| C(문자열) | 문자열 |
| I(정수) | Int32 |
| F(부동) | Double |
| D(날짜) | 문자열 |
| T(시간) | 문자열 |
| P(BCD 압축, 통화, 10진수, 수량) | 10진수 |
| N(숫자) | 문자열 |
| X(이진 및 원시) | 문자열 |

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
