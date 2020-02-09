---
title: Azure Data Factory를 사용 하 여 웹 테이블에서 데이터 복사
description: 웹 테이블의 데이터를 데이터 팩터리에서 싱크로 지원하는 데이터 저장소로 복사할 수 있는 Azure Data Factory의 웹 테이블 커넥터에 대해 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 08/01/2019
ms.author: jingwang
ms.openlocfilehash: 76f0dbb48ca5e250a383e8427ce2dd0c9dd618c9
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/08/2019
ms.locfileid: "74930942"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 웹 테이블의 데이터 복사
> [!div class="op_single_selector" title1="사용 중인 Data Factory 서비스 버전을 선택합니다."]
> * [버전 1](v1/data-factory-web-table-connector.md)
> * [현재 버전](connector-web-table.md)

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 웹 테이블 데이터베이스에서 데이터를 복사하는 방법을 설명합니다. 이 문서는 복사 작업에 대한 일반적인 개요를 제공하는 [복사 작업 개요](copy-activity-overview.md) 문서를 기반으로 합니다.

이 웹 테이블 커넥터인 [REST 커넥터](connector-rest.md)와 [HTTP 커넥터](connector-http.md) 간의 차이점은 다음과 같습니다.

- **웹 테이블 커넥터**는 HTML 웹 페이지에서 테이블 콘텐츠를 추출합니다.
- **REST 커넥터**는 특히 RESTful API에서 데이터를 복사하는 것을 지원합니다.
- **HTTP 커넥터**는 일반적으로 모든 HTTP 엔드포인트에서 데이터를 검색합니다(예: 파일 다운로드). 

## <a name="supported-capabilities"></a>지원되는 기능

이 웹 테이블 커넥터는 다음과 같은 작업에 대해 지원 됩니다.

- [지원 되는 원본/싱크 매트릭스](copy-activity-overview.md) 를 사용 하 여 [복사 작업](copy-activity-overview.md)
- [조회 작업](control-flow-lookup-activity.md)

웹 테이블 데이터베이스에서 지원되는 모든 싱크 데이터 저장소로 데이터를 복사할 수 있습니다. 복사 작업의 원본/싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

특히 이 웹 테이블 커넥터는 **HTML 페이지에서 테이블 콘텐츠를 추출**하도록 지원합니다.

## <a name="prerequisites"></a>전제 조건

이 웹 테이블 커넥터를 사용하려면 자체 호스팅 Integration Runtime을 설정해야 합니다. 자세한 내용은 [자체 호스팅 Integration Runtime](create-self-hosted-integration-runtime.md)을 참조하세요.

## <a name="getting-started"></a>시작

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

다음 섹션에서는 웹 테이블 커넥터에 한정된 Data Factory 엔터티를 정의하는 데 사용되는 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성

웹 테이블 연결된 서비스에 다음 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 형식 속성은 **웹** |yes |
| URL | 웹 원본에 대한 URL입니다. |yes |
| authenticationType | 허용되는 값은 **Anonymous**입니다. |yes |
| connectVia | 데이터 저장소에 연결하는 데 사용할 [Integration Runtime](concepts-integration-runtime.md)입니다. [필수 조건](#prerequisites)에 설명된 대로 자체 호스팅 Integration Runtime이 필요합니다. |yes |

**예제:**

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성

데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트](concepts-datasets-linked-services.md) 문서를 참조하세요. 이 섹션에서는 웹 테이블 데이터 세트에서 지원하는 속성의 목록을 제공합니다.

웹 테이블에서 데이터를 복사하려면 데이터 세트의 type 속성을 **WebTable**로 설정합니다. 다음과 같은 속성이 지원됩니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| type | 데이터 세트의 type 속성을 **WebTable**로 설정해야 합니다. | yes |
| 경로 |테이블을 포함하는 리소스에 대한 상대 URL입니다. |아닙니다. 경로를 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. |
| index |리소스에 있는 테이블의 인덱스입니다. HTML 페이지에서 테이블의 인덱스를 가져오는 단계는 [HTML 페이지에서 테이블의 인덱스 가져오기](#get-index-of-a-table-in-an-html-page) 섹션을 참조하세요. |yes |

**예제:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성

작업 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인](concepts-pipelines-activities.md) 문서를 참조하세요. 이 섹션에서는 웹 테이블 원본에서 지원하는 속성의 목록을 제공합니다.

### <a name="web-table-as-source"></a>웹 테이블을 원본으로

웹 테이블에서 데이터를 복사하려면 복사 작업에서 원본 유형을 **WebSource**로 설정합니다. 추가적인 속성은 지원되지 않습니다.

**예제:**

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
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
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>HTML 페이지에서 테이블의 인덱스 가져오기

[데이터 세트 속성](#dataset-properties)에서 구성해야 하는 테이블의 인덱스를 가져오려면 다음과 같은 도구(예: Excel 2016)를 사용할 수 있습니다.

1. **Excel 2016**을 시작하고 **데이터** 탭으로 전환합니다.
2. 도구 모음에서 **새 쿼리**를 클릭하고 **기타 원본에서**를 가리킨 다음 **웹에서**를 클릭합니다.

    ![파워 쿼리 메뉴](./media/copy-data-from-web-table/PowerQuery-Menu.png)
3. **웹에서** 대화 상자에서 연결된 서비스 JSON에 사용할 **URL**(예: https://en.wikipedia.org/wiki/) )과 데이터 세트에 대해 지정할 경로(예: AFI%27s_100_Years...100_Movies)를 입력하고 **확인**을 클릭합니다.

    ![웹 대화 상자](./media/copy-data-from-web-table/FromWeb-DialogBox.png)

    이 예제에서 사용되는 URL은 https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies 입니다.
4. **웹 콘텐츠 액세스** 대화 상자가 표시된 경우 오른쪽 **URL**, **인증**을 선택하고 **연결**을 클릭합니다.

   ![웹 콘텐츠 액세스 대화 상자](./media/copy-data-from-web-table/AccessWebContentDialog.png)
5. 트리 뷰에서 **테이블** 항목을 클릭하여 테이블에서 콘텐츠를 표시한 다음 아래쪽의 **편집** 단추를 클릭합니다.  

   ![탐색기 대화 상자](./media/copy-data-from-web-table/Navigator-DialogBox.png)
6. **쿼리 편집기** 창에서 도구 모음의 **고급 편집기** 단추를 클릭합니다.

    ![고급 편집기 단추](./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png)
7. 고급 편집기 대화 상자에서 "원본" 옆에 있는 숫자가 인덱스입니다.

    ![고급 편집기 - 인덱스](./media/copy-data-from-web-table/AdvancedEditor-Index.png)

Excel 2013을 사용하는 경우 [Excel용 Microsoft 파워 쿼리](https://www.microsoft.com/download/details.aspx?id=39379) 를 사용하여 인덱스를 가져옵니다. 자세한 내용은 [웹 페이지에 연결](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) 문서를 참조하세요. [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)을 사용하는 경우에도 작업 단계는 비슷합니다.


## <a name="lookup-activity-properties"></a>조회 작업 속성

속성에 대한 자세한 내용을 보려면 [조회 작업](control-flow-lookup-activity.md)을 확인 하세요.

## <a name="next-steps"></a>다음 단계
Azure Data Factory에서 복사 작업의 원본 및 싱크로 지원되는 데이터 저장소 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조하세요.
