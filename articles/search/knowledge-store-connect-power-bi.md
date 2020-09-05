---
title: Power BI를 사용하여 지식 저장소에 연결
titleSuffix: Azure Cognitive Search
description: 분석 및 검색을 위해 Power BI를 사용하여 Azure Cognitive Search 지식 저장소에 연결합니다.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 5be97414a2e4270870d976a9922415b227c66e27
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924353"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Power BI를 사용하여 지식 저장소 연결

이 문서에서는 Power BI Desktop 앱에서 파워 쿼리를 사용하여 지식 저장소에 연결하고 검색하는 방법을 알아봅니다. 템플릿을 사용해서 빠르게 시작하거나 사용자 지정 대시보드를 처음부터 만들 수 있습니다. 아래 간략 한 비디오에서는 Power BI와 함께 Azure Cognitive Search를 사용 하 여 데이터에 대 한 환경을 보강 하는 방법을 보여 줍니다.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=593&end=663]



+ [Azure Portal에서 지식 저장소 만들기](knowledge-store-create-portal.md) 또는 [REST를 사용하여 Azure Cognitive Search 지식 저장소 만들기](knowledge-store-create-rest.md)의 단계에 따라 이 연습에서 사용되는 지식 저장소 샘플을 만듭니다. 또한 지식 저장소를 만들 때 사용한 Azure Storage 계정의 이름과 Azure Portal의 액세스 키가 필요합니다.

+ [Power BI Desktop 설치](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Power BI 템플릿 샘플 - Azure Portal만

[Azure Portal을 사용하여 지식 저장소](knowledge-store-create-portal.md)를 만들 때 **데이터 가져오기** 마법사의 두 번째 페이지에서 [Power BI 템플릿](https://github.com/Azure-Samples/cognitive-search-templates)을 다운로드하는 옵션이 있습니다. 이 템플릿은 텍스트 기반 콘텐츠에 대한 WordCloud 및 Network Navigator와 같은 몇 가지 시각화를 제공합니다. 

**인지 기술 추가** 페이지에서 **Power BI 템플릿 가져오기**를 클릭하여 해당하는 공용 GitHub 위치에서 템플릿을 검색하고 다운로드합니다. 마법사는 마법사에 지정된 지식 저장소 프로젝션에 캡처된 것처럼 데이터의 모양을 수용하도록 템플릿을 수정합니다. 이러한 이유로, 다운로드하는 템플릿은 다른 데이터 입력과 기술 선택을 가정하여 마법사를 실행할 때마다 달라집니다.

![Azure Cognitive Search Power BI 템플릿 샘플](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Power BI 템플릿 샘플")

> [!NOTE]
> 마법사가 진행 중일 때 템플릿이 다운로드되지만, 실제로 Azure Table Storage에 지식 저장소가 생성될 때까지 기다려야 사용할 수 있습니다.

## <a name="connect-with-power-bi"></a>Power BI로 연결

1. Power BI Desktop을 시작하고 **데이터 가져오기**를 클릭합니다.

1. **데이터 가져오기**에서 **Azure**, **Azure Table Storage**를 차례로 선택합니다.

1. **연결**을 클릭합니다.

1. **계정 이름 또는 URL**에서 Azure Storage 계정 이름을 입력합니다(전체 URL이 자동으로 생성됨).

1. 스토리지 계정 키를 입력하라는 메시지가 표시되면 입력합니다.

1. 이전 연습에서 생성된 호텔 리뷰 데이터가 들어 있는 표를 선택합니다. 

   + 포털 연습의 경우 테이블 이름은 *hotelReviewsSsDocument*, *hotelReviewsSsEntities*, *hotelReviewsSsKeyPhrases* 및 *hotelReviewsSsPages*입니다. 
   
   + REST 연습의 경우 테이블 이름은 *hotelReviewsDocument*, *hotelReviewsPages*, *hotelReviewsKeyPhrases* 및 *hotelReviewsSentiment*입니다.

1. **로드**를 클릭합니다.

1. 위쪽 리본에서 **쿼리** 편집을 클릭하여 **파워 쿼리 편집기**를 엽니다.

   ![파워 쿼리 열기](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "파워 쿼리 열기")

1. *hotelReviewsSsDocument*를 선택한 다음, *PartitionKey*, *RowKey* 및 *Timestamp* 열을 제거합니다. 
   ![테이블 편집](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "테이블 편집")

1. 테이블의 오른쪽 위에서 반대 방향 화살표가 있는 아이콘을 클릭하여 *콘텐츠*를 확장합니다. 열 목록이 표시되면 모든 열을 선택한 다음, 'metadata'로 시작하는 열을 선택 취소합니다. **확인**을 클릭하여 선택한 열을 표시합니다.

   ![테이블 편집](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "콘텐츠 확장")

1. 열의 왼쪽 위에 있는 ABC-123 아이콘을 클릭하여 다음 열의 데이터 형식을 변경합니다.

   + *content.latitude* 및 *Content.longitude*의 경우 **10진수**를 선택합니다.
   + *Content.reviews_date* 및 *Content.reviews_dateAdded*의 경우 **날짜/시간**을 선택합니다.

   ![데이터 형식 변경](media/knowledge-store-connect-power-bi/powerbi-change-type.png "데이터 형식 변경")

1. *hotelReviewsSsPages*를 선택한 다음, 9단계와 10단계를 반복하여 열을 삭제하고 *콘텐츠*를 확장합니다.
1. *Content.SentimentScore*의 데이터 형식을 **10진수**로 변경합니다.
1. *hotelReviewsSsKeyPhrases*를 선택한 다음, 9단계와 10단계를 반복하여 열을 삭제하고 *콘텐츠*를 확장합니다. 이 테이블의 데이터 형식은 수정할 필요가 없습니다.

1. 명령 모음에서 **닫기 및 적용**을 클릭합니다.

1. 왼쪽 탐색 창에서 모델 타일을 클릭하고 Power BI가 세 테이블 간의 관계를 표시하는지 확인합니다.

   ![관계 유효성 검사](media/knowledge-store-connect-power-bi/powerbi-relationships.png "관계 유효성 검사")

1. 각 관계를 두 번 클릭하고 **교차 필터 방향**이 **둘 다**로 설정되었는지 확인합니다.  이렇게 하면 필터가 적용될 때 시각적 개체를 새로 고칠 수 있습니다.

1. 시각화를 통해 데이터를 검색하려면 왼쪽 탐색 창의 보고서 타일을 클릭합니다. 텍스트 필드의 경우 테이블과 카드는 유용한 시각화입니다. 세 개의 테이블에서 각각 필드를 선택하여 테이블이나 카드를 채울 수 있습니다. 

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](../cognitive-services/cognitive-services-apis-create-account.md) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>정리

본인 소유의 구독으로 이 모듈을 진행하고 있는 경우에는 프로젝트가 끝날 때 여기에서 만든 리소스가 계속 필요한지 확인하는 것이 좋습니다. 계속 실행되는 리소스에는 요금이 부과될 수 있습니다. 리소스를 개별적으로 삭제하거나 리소스 그룹을 삭제하여 전체 리소스 세트를 삭제할 수 있습니다.

왼쪽 탐색 창의 **모든 리소스** 또는 **리소스 그룹** 링크를 사용하여 포털에서 리소스를 찾고 관리할 수 있습니다.

무료 서비스를 사용하는 경우 인덱스, 인덱서, 데이터 원본 세 개로 제한됩니다. 포털에서 개별 항목을 삭제하여 제한 이하로 유지할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Storage Explorer를 사용하여 이 지식 저장소를 검색하는 방법은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [Storage Explorer를 사용하여 보기](knowledge-store-view-storage-explorer.md)