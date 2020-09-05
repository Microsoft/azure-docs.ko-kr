---
title: AI를 사용 하 여 Blob 저장소 데이터 이해
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search의 AI 보강 파이프라인을 사용 하 여 Azure blob에 의미 체계, 자연어 처리 및 이미지 분석을 추가 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ce5eafe0b36f07d8de366b6d4adb92e894fcb67e
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88936744"
---
# <a name="use-ai-to-understand-blob-storage-data"></a>AI를 사용 하 여 Blob 저장소 데이터 이해

Azure Blob storage의 데이터는 종종 이미지, 긴 텍스트, Pdf 및 Office 문서와 같은 구조화 되지 않은 다양 한 콘텐츠입니다. Azure Cognitive Search의 AI 기능을 사용 하면 다양 한 방식으로 blob에서 중요 한 정보를 이해 하 고 추출할 수 있습니다. Blob 콘텐츠에 AI를 적용 하는 예는 다음과 같습니다.

+ OCR (광학 문자 인식)을 사용 하 여 이미지에서 텍스트 추출
+ 사진에서 장면 설명 또는 태그를 생성 합니다.
+ 언어 검색 및 텍스트를 다른 언어로 번역
+ NER (명명 된 엔터티 인식)를 사용 하 여 텍스트를 처리 하 여 사람, 날짜, 위치 또는 조직에 대 한 참조 찾기 

이러한 AI 기능 중 하나만 필요할 수도 있지만 이러한 기능 중 여러 개를 동일한 파이프라인으로 결합 하는 것이 일반적입니다. 예를 들어 스캔 한 이미지에서 텍스트를 추출한 다음 해당 위치에서 참조 되는 모든 날짜와 위치를 찾습니다. 

AI 보강는 필드로 캡처된 새 정보를 필드에 저장 합니다. 보강는 전체 텍스트 검색을 통해 검색 인덱스에서이 정보에 액세스 하거나, 보강 문서를 Azure storage로 다시 보내 검색 또는 분석 시나리오에 대 한 데이터 탐색을 포함 하는 새로운 응용 프로그램 환경을 만들 수 있습니다. 

이 문서에서는 광범위 한 렌즈를 통해 AI 보강를 확인 하 여 blob의 원시 데이터를 변환 하는 과정에서 검색 인덱스 또는 지식 저장소의 쿼리 가능한 정보로 전체 프로세스를 신속 하 게 파악할 수 있습니다.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>AI를 사용 하 여 blob 데이터를 "보강" 하는 것을 의미 합니다.

*Ai 보강* 는 사용자가 제공 하는 Microsoft 또는 사용자 지정 ai의 기본 제공 ai를 통합 하는 Azure Cognitive Search 인덱싱 아키텍처의 일부입니다. Blob을 처리 해야 하는 종단 간 시나리오를 구현 하는 데 도움이 됩니다 .이 시나리오에서는 blob (기존 항목과 새 항목 모두)을 처리 하 고, 이미지 및 텍스트를 추출 하 고, 다양 한 AI 기능을 사용 하 여 원하는 정보를 추출 하 고, 빠른 검색, 검색 및 탐색을 위해 검색 인덱스에서 인덱스를 인덱싱합니다. 

입력은 Azure Blob Storage의 단일 컨테이너에 있는 Blob입니다. Blob은 거의 모든 종류의 텍스트 또는 이미지 데이터 일 수 있습니다. 

출력은 항상 클라이언트 응용 프로그램에서 빠른 텍스트 검색, 검색 및 탐색에 사용 되는 검색 인덱스입니다. 또한 출력은 Power BI 또는 데이터 과학 워크 로드와 같은 도구에서 다운스트림 분석용으로 문서를 Azure blob 또는 Azure 테이블에 보강 하는 *정보 저장소* 일 수도 있습니다.

Between은 파이프라인 아키텍처 자체입니다. 파이프라인은 *인덱서* 기능을 기반으로 하며,이 기능을 사용 하 여 AI를 제공 하는 하나 이상의 *기술로* 구성 된 *기술*를 할당할 수 있습니다. 파이프라인의 목적은 원시 콘텐츠로 입력 하는 *보강 문서* 를 생성 하는 것이 고 파이프라인을 통해 이동 하는 동안 추가 구조, 컨텍스트 및 정보를 선택 하는 것입니다. 보강 문서는 인덱싱 중에 사용 되어 전체 텍스트 검색 또는 탐색 및 분석에 사용 되는 반전 된 인덱스 및 기타 구조를 만드는 데 사용 됩니다.

## <a name="start-with-services"></a>서비스 시작

Azure Cognitive Search 및 Azure Blob Storage가 필요합니다. Blob Storage 내에서 원본 콘텐츠를 제공하는 컨테이너가 필요합니다.

스토리지 계정 포털 페이지에서 직접 시작할 수 있습니다. 왼쪽 탐색 페이지의 **Blob service**에서 **Azure Cognitive Search 추가**를 클릭하여 새 서비스를 만들거나 기존 서비스를 선택합니다. 

Azure Cognitive Search를 저장소 계정에 추가한 후에는 표준 프로세스에 따라 Azure 데이터 원본의 데이터를 보강 할 수 있습니다. AI 보강을 쉽게 초기 소개 하기 위해 Azure Cognitive Search에서 **데이터 가져오기** 마법사를 권장 합니다. 이 빠른 시작에서는 [포털에서 AI 보강 파이프라인 만들기](cognitive-search-quickstart-blob.md)의 단계를 안내 합니다. 

다음 섹션에서는 더 많은 구성 요소 및 개념을 살펴보겠습니다.

## <a name="use-a-blob-indexer"></a>Blob 인덱서 사용

AI 보강는 인덱싱 파이프라인에 대 한 추가 기능이 며, Azure Cognitive Search에서 이러한 파이프라인은 *인덱서*위에 빌드됩니다. ‘인덱서’는 데이터를 샘플링하고, 메타데이터를 읽고, 데이터를 검색하며, 후속 가져오기를 위해 네이티브 형식의 데이터를 JSON 문서로 직렬화하기 위한 내부 논리를 포함하는 데이터 원본 인식 하위 서비스입니다. 인덱서는 AI와는 별도로 가져오기에 사용 되는 경우가 많지만 AI 보강 파이프라인을 빌드 하려는 경우에는 인덱서와 기술가 필요 합니다. 이 섹션에서는 인덱서가 강조 표시 되어 있습니다. 다음 섹션에서는 기술력과에 대해 중점적으로 설명 합니다.

Azure Storage의 Blob은 [Azure Cognitive Search Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)를 사용하여 인덱싱됩니다. **데이터 가져오기** 마법사, REST API 또는 .NET SDK를 사용하여 이 인덱서를 호출할 수 있습니다. 코드에서는 형식을 설정하고 Blob 컨테이너와 함께 Azure Storage 계정을 포함하는 연결 정보를 제공하여 이 인덱서를 사용합니다. 만든 후에 매개 변수로 전달할 수 있는 가상 디렉터리를 만들거나 파일 형식 확장에서 필터링하여 Blob의 하위 집합을 지정할 수 있습니다.

인덱서는 “문서 크래킹”을 수행하여 콘텐츠를 검사하는 Blob을 엽니다. 이 작업은 데이터 원본에 연결한 후 파이프라인에서 첫 번째 단계입니다. Blob 데이터의 경우에는 PDF, office 문서, 이미지 및 기타 콘텐츠 형식이 검색 됩니다. 텍스트 추출을 사용한 문서 크래킹은 무료로 제공됩니다. 이미지 추출을 사용한 문서 크랙 요금은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/search/)에서 확인할 수 있는 요금으로 청구 됩니다.

모든 문서가 보강에 대 한 기술을 명시적으로 제공 하는 경우에만 발생 합니다. 예를 들어 파이프라인이 이미지 분석 으로만 구성 된 경우 컨테이너의 텍스트 또는 문서는 무시 됩니다.

Blob 인덱서는 구성 매개 변수와 함께 제공되며 기본 데이터가 충분한 정보를 제공하는 경우 변경 내용 추적을 지원합니다. [Azure Cognitive Search Blob Storage 인덱서](search-howto-indexing-azure-blob-storage.md)의 핵심 기능에 대해 자세히 알아볼 수 있습니다.

## <a name="add-ai-components"></a>AI 구성 요소 추가

AI 보강는 패턴 또는 특성을 찾은 다음 그에 따라 작업을 수행 하는 모듈을 참조 합니다. 사진의 얼굴 인식, 사진 텍스트 설명, 문서의 키 구 검색, OCR (또는 이진 파일의 인쇄 또는 필기 텍스트 인식)은 예제를 보여 줍니다.

Azure Cognitive Search에서 *기술은* 독립 실행형으로 또는 다른 기술과 함께 사용할 수 있는 AI 처리의 개별 구성 요소입니다. 

+ 기본 제공 기술은 Computer Vision를 기반으로 하는 이미지 분석과 Text Analytics을 기반으로 하는 자연어 처리를 사용 하 여 Cognitive Services에 의해 지원 됩니다. 전체 목록은 [content 보강에 대 한 기본 제공 기술](cognitive-search-predefined-skills.md)을 참조 하세요.

+ 사용자 지정 기술은 파이프라인에 통합할 수 있는 [인터페이스 정의](cognitive-search-custom-skill-interface.md) 로 래핑된 사용자 지정 코드입니다. 고객 솔루션에서는 오픈 소스, 타사 또는 자사 AI 모듈을 제공 하는 사용자 지정 기술과 함께를 사용 하는 것이 일반적입니다.

*기술* 는 파이프라인에서 사용 되는 기술의 컬렉션으로, 문서 크랙 단계에서 콘텐츠를 사용할 수 있게 된 후에 호출 됩니다. 인덱서는 정확히 하나의 기술를 사용할 수 있지만, 해당 기술는 다른 시나리오에서 다시 사용할 수 있도록 인덱서와 독립적으로 존재 합니다.

사용자 지정 기술은 복잡 해질 수 있지만 구현 측면에서 간단 하 고 간단 합니다. 패턴 일치 또는 분류 모델을 제공 하는 기존 패키지가 있는 경우 blob에서 추출 하는 콘텐츠를 이러한 모델에 전달 하 여 처리할 수 있습니다. AI 보강는 Azure 기반 이므로 모델은 Azure에도 있어야 합니다. 몇 가지 일반적인 호스팅 방법론에는 [Azure Functions](cognitive-search-create-custom-skill-example.md) 또는 [컨테이너](https://github.com/Microsoft/SkillsExtractorCognitiveSearch)를 사용 하는 것이 포함 됩니다.

Cognitive Services에서 지 원하는 기본 기술에는 리소스에 대 한 액세스를 제공 하는 [연결 된 Cognitive Services](cognitive-search-attach-cognitive-services.md) 전체 구독 키가 필요 합니다. 모든 기능을 제공 하는 키는 이미지 분석, 언어 검색, 텍스트 번역 및 텍스트 분석을 제공 합니다. 기타 기본 제공 기술은 Azure Cognitive Search의 기능이 며 추가 서비스 또는 키를 요구 하지 않습니다. 텍스트 shaper, 분할자 및 합병은 파이프라인을 디자인할 때 때때로 필요한 도우미 기술의 예입니다.

사용자 지정 기술 및 기본 제공 유틸리티 기술만 사용 하는 경우 Cognitive Services 관련 된 종속성 또는 비용이 없습니다.

<!-- ## Order of operations

Now we've covered indexers, content extraction, and skills, we can take a closer look at pipeline mechanisms and order of operations.

A skillset is a composition of one or more skills. When multiple skills are involved, the skillset operates as sequential pipeline, producing dependency graphs, where output from one skill becomes input to another. 

For example, given a large blob of unstructured text, a sample order of operations for text analytics might be as follows:

1. Use Text Splitter to break the blob into smaller parts.
1. Use Language Detection to determine if content is English or another language.
1. Use Text Translator to get all text into a common language.
1. Run Entity Recognition, Key Phrase Extraction, or Sentiment Analysis on chunks of text. In this step, new fields are created and populated. Entities might be location, people, organization, dates. Key phrases are short combinations of words that appear to belong together. Sentiment score is a rating on continuum of negative (0) to positive (1) sentiment.
1. Use Text Merger to reconstitute the document from the smaller chunks. -->

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>다운스트림 솔루션에서 AI 보강 output 사용

AI 보강의 출력은 Azure Cognitive Search의 검색 인덱스 또는 Azure Storage의 [기술 자료 저장소](knowledge-store-concept-intro.md) 입니다.

Azure Cognitive Search에서 검색 인덱스는 클라이언트 앱에서 자유 텍스트 및 필터링 된 쿼리를 사용 하 여 대화형 탐색에 사용 됩니다. AI를 통해 만든 보강 문서는 JSON으로 형식이 지정 되 고, 모든 문서가 Azure Cognitive Search에서 인덱싱되는 것과 동일한 방식으로 인덱싱됩니다. 인덱서가 제공 하는 모든 혜택을 활용 합니다. 예를 들어 인덱싱 중에 blob 인덱서는 필드 매핑 또는 변경 검색 논리를 활용 하기 위해 구성 매개 변수 및 설정을 참조 합니다. 이러한 설정은 일반 인덱싱 및 AI 보강 워크 로드에 완전히 사용할 수 있습니다. 사후 인덱싱, 콘텐츠가 Azure Cognitive Search에 저장 되는 경우 다양 한 쿼리를 작성 하 고 식을 필터링 하 여 콘텐츠를 이해할 수 있습니다.

Azure Storage에서 기술 자료 저장소에는 두 가지 징후 (blob 컨테이너) 또는 테이블 저장소의 테이블이 있습니다. 

+ Blob 컨테이너는 보강 문서 전체를 캡처합니다 .이는 다른 프로세스에 피드 하려는 경우에 유용 합니다. 

+ 반면, 테이블 저장소는 보강 문서의 실제 프로젝션을 수용할 수 있습니다. 특정 파트를 포함 하거나 제외 하는 보강 문서의 조각이 나 레이어를 만들 수 있습니다. Power BI 분석을 위해 Azure Table storage의 테이블은 추가 시각화 및 탐색을 위해 데이터 원본이 됩니다.

파이프라인의 끝에 있는 보강 문서는 보강 동안 추출 되거나 생성 된 새 정보를 포함 하는 추가 필드가 있는 원래 입력 버전과 다릅니다. 따라서 사용 하는 출력 구조에 관계 없이 원래 및 만든 콘텐츠의 조합으로 작업할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다양 한 방식으로 Cognitive Services을 결합 하 고 시나리오에 대 한 기존 인지 서비스가 없는 경우에 대 한 사용자 지정 기술을 제작 하 여 Azure Storage에서 데이터를 최대한 활용 하기 위해 AI 보강로 더 많은 작업을 수행할 수 있습니다. 아래 링크를 따라 자세히 알아볼 수 있습니다.

+ [Azure Portal을 사용하여 Blob 업로드, 다운로드 및 나열(Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Blob 인덱서 설정(Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [AI 보강 개요 (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [기술 만들기 (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [주석 트리의 지도 노드 (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)