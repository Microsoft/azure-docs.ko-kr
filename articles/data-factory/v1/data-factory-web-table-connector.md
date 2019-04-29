---
title: Azure Data Factory를 사용하여 웹 테이블에서 데이터 이동 | Microsoft Docs
description: Azure Data Factory를 사용하여 웹 페이지의 테이블로 데이터를 이동하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: f54a26a4-baa4-4255-9791-5a8f935898e2
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 81b7bf7c230c66087bf286ebd9369d992e93be90
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61250579"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Azure Data Factory를 사용하여 웹 테이블 원본에서 데이터 이동
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [버전 1](data-factory-web-table-connector.md)
> * [버전 2(현재 버전)](../connector-web-table.md)

> [!NOTE]
> 이 문서의 내용은 Data Factory 버전 1에 적용됩니다. 현재 버전의 Data Factory 서비스를 사용 중인 경우, [V2의 웹 테이블 커넥터](../connector-web-table.md)를 참조하세요.

이 문서에서는 Azure Data Factory의 복사 작업을 사용하여 웹 페이지의 테이블에서 지원되는 싱크 데이터 저장소로 데이터를 이동하는 방법에 대해 간략하게 설명합니다. 이 문서는 복사 작업 및 지원되는 데이터 저장소를 원본/싱크로 사용한 데이터 이동의 일반적인 개요를 보여주는 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서를 작성합니다.

현재 Data Factory는 웹 테이블에서 다른 데이터 저장소로 데이터 이동이 아닌 다른 데이터 저장소에서 웹 테이블 대상으로 데이터 이동만을 지원합니다.

> [!IMPORTANT]
> 이 웹 커넥터는 현재 HTML 페이지에서 테이블 콘텐츠를 추출하도록 지원합니다. HTTP/s 엔드포인트에서 데이터를 검색하려면 [HTTP 커넥터](data-factory-http-connector.md)를 대신 사용합니다.

## <a name="prerequisites"></a>필수 조건

이 웹 테이블 커넥터를 사용하려면 자체 호스트 통합 런타임(즉, 데이터 관리 게이트웨이)을 설정하고 싱크 연결된 서비스에서 `gatewayName` 속성을 구성해야 합니다. 예를 들어, 웹 테이블에서 Azure Blob 스토리지로 복사하려면 다음과 같이 Azure Storage 연결된 서비스를 구성합니다.

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>시작
여러 도구/API를 사용하여 온-프레미스 Cassandra 데이터 저장소의 데이터를 이동하는 복사 작업으로 파이프라인을 만들 수 있습니다. 

- 파이프라인을 만드는 가장 쉬운 방법은 **복사 마법사**를 사용하는 것입니다. 단계별 지침은 [자습서: 복사 마법사를 사용하여 파이프라인 만들기](data-factory-copy-data-wizard-tutorial.md)를 참조하세요. 데이터 복사 마법사를 사용하여 파이프라인을 만드는 방법에 대한 빠른 연습을 볼 수 있습니다. 
- 또한 다음 도구를 사용하여 파이프라인을 만들 수 있습니다. **Azure Portal**, **Visual Studio**, **Azure PowerShell**, **Azure Resource Manager 템플릿**, **.NET API** 및 **REST API** 복사 작업을 사용하여 파이프라인을 만드는 단계별 지침은 [복사 작업 자습서](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요. 

도구를 사용하든 API를 사용하든, 다음 단계에 따라 원본 데이터 저장소에서 싱크 데이터 저장소로 데이터를 이동하는 파이프라인을 만들면 됩니다.

1. 입력 및 출력 데이터 저장소를 데이터 팩터리에 연결하는 **연결된 서비스**를 만듭니다.
2. 복사 작업의 입력 및 출력 데이터를 나타내는 **데이터 세트**를 만듭니다. 
3. 입력으로 데이터 세트를, 출력으로 데이터 세트를 사용하는 복사 작업을 통해 **파이프라인**을 만듭니다. 

마법사를 사용하는 경우 이러한 Data Factory 엔터티(연결된 서비스, 데이터 세트 및 파이프라인)에 대한 JSON 정의가 자동으로 생성됩니다. 도구/API(.NET API 제외)를 사용하는 경우 JSON 형식을 사용하여 이러한 Data Factory 엔터티를 정의합니다.  웹 테이블의 데이터를 복사하는 데 사용되는 Data Factory 엔터티의 JSON 정의에 대한 샘플은 이 문서의 [JSON의 예: 웹 테이블에서 Azure Blob으로 데이터 복사](#json-example-copy-data-from-web-table-to-azure-blob) 섹션을 참조하세요. 

다음 섹션에서는 웹 테이블에 한정된 데이터 팩터리 엔터티를 정의하는 데 사용되는 JSON 속성에 대해 자세히 설명합니다.

## <a name="linked-service-properties"></a>연결된 서비스 속성
다음 테이블은 웹 연결된 서비스에 특정된 JSON 요소에 대한 설명을 제공합니다.

| 자산 | 설명 | 필수 |
| --- | --- | --- |
| 형식 |type 속성을 다음으로 설정해야 합니다. **웹** |예 |
| Url |웹 원본에 대한 URL입니다. |예 |
| authenticationType |익명 |예 |

### <a name="using-anonymous-authentication"></a>익명 인증 사용

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>데이터 세트 속성
데이터 세트 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [데이터 세트 만들기](data-factory-create-datasets.md) 문서를 참조하세요. 구조, 가용성 및 JSON 데이터 세트의 정책과 같은 섹션이 모든 데이터 세트 형식에 대해 유사합니다(Azure SQL, Azure blob, Azure 테이블 등).

**typeProperties** 섹션은 데이터 세트의 각 형식에 따라 다르며 데이터 저장소에 있는 데이터의 위치에 대한 정보를 제공합니다. **WebTable** 형식의 데이터 세트에 대한 typeProperties 섹션에는 다음 속성이 있습니다.

| 자산 | 설명 | 필수 |
|:--- |:--- |:--- |
| 형식 |데이터 세트의 형식입니다. **데이터 집합** |예 |
| 경로 |테이블을 포함하는 리소스에 대한 상대 URL입니다. |아니요. 경로를 지정하지 않으면 연결된 서비스 정의에 지정된 URL만 사용됩니다. |
| index |리소스에 있는 테이블의 인덱스입니다. HTML 페이지에서 테이블의 인덱스를 가져오는 단계는 [HTML 페이지에서 테이블의 인덱스 가져오기](#get-index-of-a-table-in-an-html-page) 섹션을 참조하세요. |예 |

**예제:**

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>복사 작업 속성
활동 정의에 사용할 수 있는 섹션 및 속성의 전체 목록은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 이름, 설명, 입력/출력 테이블, 정책 등의 속성은 모든 형식의 활동에 사용할 수 있습니다.

반면 활동의 typeProperties 섹션에서 사용할 수 있는 속성은 각 활동 유형에 따라 달라집니다. 복사 활동의 경우 이러한 속성은 소스 및 싱크의 형식에 따라 달라집니다.

현재 복사 작업의 원본이 **WebSource**형식인 경우 추가 속성이 지원되지 않습니다.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>JSON 예제: 웹 테이블의 데이터를 Azure Blob으로 복사
다음 샘플은 다음과 같은 내용을 보여 줍니다.

1. [웹](#linked-service-properties) 형식의 연결된 서비스입니다.
2. [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties) 형식의 연결된 서비스
3. [WebTable](#dataset-properties) 형식의 입력 [데이터 세트](data-factory-create-datasets.md)입니다.
4. [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties) 형식의 출력 [데이터 세트](data-factory-create-datasets.md)
5. [WebSource](#copy-activity-properties) 및 [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)를 사용하는 복사 작업의 [파이프라인](data-factory-create-pipelines.md)입니다.

샘플은 1시간마다 웹 테이블의 데이터를 Azure Blob으로 복사합니다. 이 샘플에 사용된 JSON 속성은 샘플 다음에 나오는 섹션에서 설명합니다.

다음 샘플에서는 웹 테이블의 데이터를 Azure Blob으로 복사하는 방법을 보여 줍니다. 그러나 Azure Data Factory의 복사 작업을 사용하여 [데이터 이동 활동](data-factory-data-movement-activities.md) 문서에 설명한 싱크로 직접 데이터를 복사할 수 있습니다.

**웹 연결된 서비스** 이 예제에서는 익명 인증으로 웹 연결된 서비스를 사용합니다. 사용할 수 있는 다른 유형의 인증은 [웹 연결된 서비스](#linked-service-properties) 섹션을 참조하세요.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Azure Storage 연결된 서비스**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**WebTable 입력 데이터 세트****external**을 **true**로 설정하면 데이터 세트가 Data Factory의 외부에 있으며 Data Factory의 활동에 의해 생성되지 않는다는 사실이 Data Factory 서비스에 전달됩니다.

> [!NOTE]
> HTML 페이지에서 테이블의 인덱스를 가져오는 단계는 [HTML 페이지에서 테이블의 인덱스 가져오기](#get-index-of-a-table-in-an-html-page) 섹션을 참조하세요.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Azure Blob 출력 데이터 세트**

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
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**복사 작업을 포함하는 파이프라인**

파이프라인은 입력 및 출력 데이터 세트를 사용하도록 구성된 복사 작업을 포함하고 매시간 실행하도록 예약됩니다. 파이프라인 JSON 정의에서 **source** 형식은 **WebSource**로 설정되고 **sink** 형식은 **BlobSink**로 설정됩니다.

WebSource에서 지원되는 속성 목록은 WebSource 형식 속성을 참조하세요.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
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

## <a name="get-index-of-a-table-in-an-html-page"></a>HTML 페이지에서 테이블의 인덱스 가져오기
1. **Excel 2016**을 시작하고 **데이터** 탭으로 전환합니다.  
2. 도구 모음에서 **새 쿼리**를 클릭하고 **기타 원본에서**를 가리킨 다음 **웹에서**를 클릭합니다.

    ![파워 쿼리 메뉴](./media/data-factory-web-table-connector/PowerQuery-Menu.png)
3. **웹에서** 대화 상자에서 연결된 서비스 JSON에 사용할 **URL**(예: https://en.wikipedia.org/wiki/))과 데이터 세트에 대해 지정할 경로(예: AFI%27s_100_Years...100_Movies)를 입력하고 **확인**을 클릭합니다.

    ![웹 대화 상자](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

    이 예제에서 사용되는 URL은 https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies입니다.
4. **웹 콘텐츠 액세스** 대화 상자가 표시된 경우 오른쪽 **URL**, **인증**을 선택하고 **연결**을 클릭합니다.

   ![웹 콘텐츠 액세스 대화 상자](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5. 트리 뷰에서 **테이블** 항목을 클릭하여 테이블에서 콘텐츠를 표시한 다음 아래쪽의 **편집** 단추를 클릭합니다.  

   ![탐색기 대화 상자](./media/data-factory-web-table-connector/Navigator-DialogBox.png)
6. **쿼리 편집기** 창에서 도구 모음의 **고급 편집기** 단추를 클릭합니다.

    ![고급 편집기 단추](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)
7. 고급 편집기 대화 상자에서 "원본" 옆에 있는 숫자가 인덱스입니다.

    ![고급 편집기 - 인덱스](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)

Excel 2013을 사용하는 경우 [Excel용 Microsoft 파워 쿼리](https://www.microsoft.com/download/details.aspx?id=39379) 를 사용하여 인덱스를 가져옵니다. 자세한 내용은 [웹 페이지에 연결](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) 문서를 참조하세요. [Microsoft Power BI for Desktop](https://powerbi.microsoft.com/desktop/)을 사용하는 경우에도 작업 단계는 비슷합니다.

> [!NOTE]
> 원본 데이터 세트의 열을 싱크 데이터 세트의 열로 매핑하려면 [Azure Data Factory의 데이터 세트 열 매핑](data-factory-map-columns.md)을 참조하세요.

## <a name="performance-and-tuning"></a>성능 및 튜닝
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소 및 최적화하는 다양한 방법에 대해 알아보려면 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md)를 참조하세요.
