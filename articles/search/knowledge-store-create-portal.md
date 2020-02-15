---
title: Azure Portal에서 지식 저장소(미리 보기) 만들기
titleSuffix: Azure Cognitive Search
description: 데이터 가져오기 마법사를 사용하여 보강 콘텐츠를 유지하는 데 사용되는 지식 저장소를 만듭니다. 분석을 목적으로 다른 앱에서 지식 저장소에 연결하거나, 보강 콘텐츠를 다운스트림 프로세스에 보냅니다. 이 기능은 현재 공개 미리 보기로 제공됩니다.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 01/29/2020
ms.openlocfilehash: b75b760704511627c74301ae3fff82c24a262e17
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76904894"
---
# <a name="quickstart-create-an-azure-cognitive-search-knowledge-store-in-the-azure-portal"></a>빠른 시작: Azure Portal에서 Azure Cognitive Search 지식 저장소 만들기

> [!IMPORTANT] 
> 지식 저장소는 현재 공개 미리 보기로 제공됩니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 

지식 저장소는 후속 분석 또는 다운스트림 처리를 위해 인지 기술 파이프라인의 출력을 유지하는 Azure Cognitive Search의 기능입니다. 

파이프라인은 비정형 텍스트 및 이미지를 원시 콘텐츠로 수락하고, Cognitive Services를 통해 AI를 적용하고(예: OCR, 이미지 분석 및 자연어 처리), 정보를 추출하고, 새 구조 및 정보를 출력합니다. 파이프라인에서 만드는 실제 아티팩트 중 하나는 도구를 통해 액세스하여 콘텐츠를 분석하고 살펴볼 수 있는 [지식 저장소](knowledge-store-concept-intro.md)입니다.

이 빠른 시작에서는 Azure 클라우드에서 서비스와 데이터를 결합하여 지식 저장소를 만듭니다. 모든 것이 준비되면 포털에서 **데이터 가져오기** 마법사를 실행하여 모두 가져옵니다. 최종 결과는 원본 텍스트 콘텐츠 + AI에서 생성한 콘텐츠이며, 포털에서 볼 수 있습니다([스토리지 탐색기](knowledge-store-view-storage-explorer.md)).

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-services-and-load-data"></a>서비스 만들기 및 데이터 로드

이 빠른 시작에서는 Azure Cognitive Search, Azure Blob 스토리지 및 [Azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/)를 AI에 사용합니다. 

워크로드가 너무 작으므로 Cognitive Services는 Azure Cognitive Search에서 호출될 때 매일 최대 20개의 트랜잭션을 무료로 처리하기 위해 백그라운드에 탭으로 처리됩니다. 제공하는 샘플 데이터를 사용하는 경우 Cognitive Services 리소스 만들기 또는 연결을 건너뛸 수 있습니다.

1. [HotelReviews_Free.csv를 다운로드합니다](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). 이 데이터는 CSV 파일로 저장된 호텔 리뷰 데이터이며(Kaggle.com에서 가져온 데이터) 단일 호텔에 대한 19개 고객 피드백을 포함하고 있습니다. 

1. [Azure 스토리지 계정을 만들거나](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal) 현재 구독에서 [기존 계정을 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 가져올 원시 콘텐츠와 최종 결과인 지식 저장소 둘 모두에 Azure 스토리지를 사용할 것입니다.

   **StorageV2(범용 V2)** 계정 유형을 선택합니다.

1. Blob 서비스 페이지를 열고 *hotel-reviews*라는 컨테이너를 만듭니다.

1. **업로드**를 클릭합니다.

    ![데이터 업로드](media/knowledge-store-create-portal/upload-command-bar.png "호텔 리뷰 업로드")

1. 첫 번째 단계에서 다운로드한 **HotelReviews-Free.csv** 파일을 선택합니다.

    ![Azure Blob 컨테이너 만들기](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Azure Blob 컨테이너 만들기")

1. 이 리소스가 거의 완료되었지만, 이 페이지를 나가기 전에 왼쪽 탐색 창의 링크를 사용하여 **액세스 키** 페이지를 엽니다. Blob 스토리지에서 데이터를 검색할 연결 문자열을 가져옵니다. 연결 문자열은 `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net` 예제와 비슷한 현식입니다.

1. 여전히 포털에서 Azure Cognitive Search로 전환합니다. [새 서비스를 만들](search-create-service-portal.md)거나 [기존 서비스를 찾습니다](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). 이 빠른 시작에서는 체험 서비스를 사용할 수 있습니다.

이제 데이터 가져오기 마법사로 이동할 준비가 되었습니다.

## <a name="run-the-import-data-wizard"></a>데이터 가져오기 마법사 실행

검색 서비스 [개요] 페이지의 명령 모음에서 **데이터 가져오기**를 클릭하여 다음 네 단계에 따라 지식 저장소를 만듭니다.

  ![데이터 가져오기 명령](media/cognitive-search-quickstart-blob/import-data-cmd2.png)

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

1. **데이터에 연결**에서 **Azure Blob 스토리지**를 선택하고 만든 계정 및 컨테이너를 선택합니다. 
1. **이름**에 대해 `hotel-reviews-ds`를 입력합니다.
1. **구문 분석 모드**에 대해 **분리된 텍스트**를 선택한 다음, **첫 줄에 헤더 포함** 확인란을 선택합니다. **구분 기호 문자**가 쉼표(,)인지 확인합니다.
1. **연결 문자열**에서 Azure Storage의 **액세스 키** 페이지에서 복사한 연결 문자열을 붙여 넣습니다.
1. **컨테이너**에서 데이터를 보관하는 Blob 컨테이너의 이름을 입력합니다.

    이 페이지는 다음 스크린샷과 비슷합니다.

    ![데이터 원본 개체 만들기](media/knowledge-store-create-portal/hotel-reviews-ds.png "데이터 원본 개체 만들기")

1. 다음 페이지를 계속합니다.

### <a name="step-2-add-cognitive-skills"></a>2단계: 인식 기술 추가

이 마법사 단계에서는 인지 기술 보강을 사용하여 기술 세트를 만듭니다. 원본 데이터는 여러 언어로 된 고객 리뷰로 구성됩니다. 이 데이터 세트와 관련된 기술에는 핵심 구 추출, 감정 감지 및 텍스트 번역이 포함됩니다. 이후 단계에서 이러한 보강은 지식 저장소에 Azure 테이블로 "프로젝션"됩니다.

1. **Cognitive Services 연결** 섹션을 펼칩니다. **무료(제한적 보강)** 가 기본적으로 선택되어 있습니다. HotelReviews-Free.csv의 레코드 수가 19개이고 이 무료 리소스에서 하루 최대 20개의 트랜잭션을 허용하므로 이 리소스를 사용할 수 있습니다.
1. **보강 추가**를 확장합니다.
1. **기술 세트 이름**에 대해 `hotel-reviews-ss`를 입력합니다.
1. **원본 데이터 필드**에 대해 **reviews_text**를 선택합니다.
1. **보강 세분성 수준**에 대해 **페이지(5,000자 청크)** 를 선택합니다.
1. 다음 인지 기술을 선택합니다.
    + **핵심 구 추출**
    + **텍스트 번역**
    + **감정 감지**

      ![기술 세트 만들기](media/knowledge-store-create-portal/hotel-reviews-ss.png "기술 집합 만들기")

1. **지식 저장소에 보강 저장**을 펼칩니다.
1. 다음 **Azure 테이블 프로젝션**을 선택합니다.
    + **문서**
    + **페이지**
    + **핵심 구**
1. 이전 단계에서 저장한 **스토리지 계정 연결 문자열**을 입력합니다.

    ![지식 저장소 구성](media/knowledge-store-create-portal/hotel-reviews-ks.png "지식 저장소 구성")

1. 필요에 따라 Power BI 템플릿을 다운로드합니다. 마법사에서 템플릿에 액세스하는 경우 사용자의 데이터 셰이프를 반영하도록 로컬 .pbit 파일이 조정됩니다.

1. 다음 페이지를 계속합니다.

### <a name="step-3-configure-the-index"></a>3단계: 인덱스 구성

이 마법사 단계에서는 선택적 전체 텍스트 검색 쿼리에 대한 인덱스를 구성합니다. 마법사는 데이터 원본을 샘플링하여 필드와 데이터 형식을 유추합니다. 원하는 동작의 특성만 선택하면 됩니다. 예를 들어 **조회 가능** 특성을 사용하면 검색 서비스에서 필드 값을 반환할 수 있지만, **검색 가능**을 사용하면 필드에서 전체 텍스트를 검색할 수 있습니다.

1. **인덱스 이름**에 대해 `hotel-reviews-idx`를 입력합니다.
1. 특성의 경우 기본 선택 항목을 적용합니다. 파이프라인에서 만드는 새 필드에 대해 **조회 가능** 및 **검색 가능**입니다.

    인덱스는 다음 이미지와 비슷합니다. 긴 목록이므로 일부 필드가 이미지에 표시되지 않습니다.

    ![인덱스 구성](media/knowledge-store-create-portal/hotel-reviews-idx.png "인덱스 구성")

1. 다음 페이지를 계속합니다.

### <a name="step-4-configure-the-indexer"></a>4단계: 인덱서 구성

이 마법사 단계에서는 이전 마법사 단계에서 정의한 데이터 원본, 기술 세트 및 인덱스를 함께 가져오는 인덱서를 구성합니다.

1. **이름**에 `hotel-reviews-idxr`를 입력합니다.
1. **일정**에 대해 **한 번**(기본값)을 유지합니다.
1. **제출**을 클릭하여 인덱서를 실행합니다. 데이터 추출, 인덱싱, 인지 기술 적용은 모두 이 단계에서 수행됩니다.

## <a name="monitor-status"></a>상태 모니터링

인지 기술 인덱싱은 일반적인 텍스트 기반 인덱싱보다 완료하는 데 더 오래 걸립니다. 마법사는 진행률을 추적할 수 있도록 개요 페이지에서 인덱서 목록을 열어야 합니다. 자체 탐색을 위해 개요 페이지로 이동하고 **인덱서**를 클릭합니다.

Azure Portal에서 클릭 가능한 **Azure Cognitive Search 알림** 상태 링크에 대한 알림 활동 로그를 모니터링할 수도 있습니다. 실행을 완료하는 데 몇 분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 인지 서비스를 사용하여 데이터를 보강하고 결과를 지식 저장소에 프로젝션했으므로, Storage Explorer 또는 Power BI를 사용하여 보강된 데이터 세트를 검색할 수 있습니다.

Storage Explorer에서 콘텐츠를 볼 수도 있고, Power BI를 사용하여 시각화를 통해 인사이트를 얻을 수도 있습니다.

> [!div class="nextstepaction"]
> [Storage Explorer에서 보기](knowledge-store-view-storage-explorer.md)
> [Power BI와 연결](knowledge-store-connect-power-bi.md)

> [!Tip]
> 이 연습을 반복하거나 다른 AI 보강 연습을 수행하려면 *hotel-reviews-idxr* 인덱서를 삭제합니다. 인덱서를 삭제하면 Cognitive Services 처리를 위해 무료 일별 트랜잭션 카운터가 0으로 다시 설정됩니다.
