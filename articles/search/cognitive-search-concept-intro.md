---
title: AI 보강 개념
titleSuffix: Azure Cognitive Search
description: 콘텐츠 추출, NLP (자연어 처리) 및 이미지 처리는 미리 정의 된 인식 기술과 사용자 지정 AI 알고리즘을 모두 사용 하 여 Azure Cognitive Search 인덱스에서 검색 가능한 콘텐츠를 만드는 데 사용 됩니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/18/2020
ms.openlocfilehash: 1c041d594b29c6e93b73eb1b0c623b3e566ceef5
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935503"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search의 AI 보강

AI 보강는 이미지, blob 및 기타 구조화 되지 않은 데이터 원본에서 텍스트를 추출 하는 데 사용할 수 있는 [인덱서의](search-indexer-overview.md) 확장입니다. 보강 및 추출은 [검색 인덱스](search-what-is-an-index.md) 또는 [지식 저장소](knowledge-store-concept-intro.md)중에서 인덱서 출력 개체의 콘텐츠를 더 검색 가능 하 게 만듭니다. 

추출 및 보강는 인덱서 기반 파이프라인에 연결 된 *인식 기술을* 사용 하 여 구현 됩니다. Microsoft에서 제공 하는 기본 제공 기술을 사용 하거나 사용자가 만든 [*사용자 지정 기술*](cognitive-search-create-custom-skill-example.md) 에 외부 처리를 포함할 수 있습니다. 사용자 지정 기술에 대 한 예로는 재무, 과학 게시 또는 의약품와 같은 특정 도메인을 대상으로 하는 사용자 지정 엔터티 모듈 또는 문서 분류자가 있습니다.

기본 제공 기술은 다음과 같은 범주로 분류 됩니다. 

+ **자연어 처리** 기술에는 [엔터티 인식](cognitive-search-skill-entity-recognition.md), [언어 검색](cognitive-search-skill-language-detection.md), [핵심 구 추출](cognitive-search-skill-keyphrases.md), 텍스트 조작, [감정 검색](cognitive-search-skill-sentiment.md) 및 [PII 검색](cognitive-search-skill-pii-detection.md)이 포함됩니다. 이러한 기술을 통해 구조화 되지 않은 텍스트는 인덱스에서 검색 가능 하 고 필터링 가능한 필드로 매핑됩니다.

+ **이미지 처리** 기술에는 시각 [인식 (OCR)](cognitive-search-skill-ocr.md) 및 [시각적 기능](cognitive-search-skill-image-analysis.md)(예: 얼굴 감지, 이미지 해석, 이미지 인식 (유명한 사용자 및 랜드마크) 또는 이미지 방향과 같은 특성)의 id가 포함 됩니다. 이러한 기술은 이미지 콘텐츠의 텍스트 표현을 만들어 Azure Cognitive Search의 쿼리 기능을 사용 하 여 검색할 수 있도록 합니다.

![보강 파이프라인 다이어그램](./media/cognitive-search-intro/cogsearch-architecture.png "보강 파이프라인 개요")

Azure Cognitive Search의 기본 제공 기술은 Cognitive Services API: [Computer Vision](../cognitive-services/computer-vision/index.yml) 및 [Text Analytics](../cognitive-services/text-analytics/overview.md)의 미리 학습 된 기계 학습 모델을 기반으로 합니다. 콘텐츠를 처리 하는 동안 이러한 리소스를 활용 하려는 경우 Cognitive Services 리소스를 연결할 수 있습니다.

자연어 및 이미지 처리는 데이터 수집 단계에서 적용되며, 여기서 결과는 Azure Cognitiv Search의 검색 가능한 인덱스에서 문서의 컴퍼지션 중 일부가 됩니다. 데이터는 Azure 데이터 집합으로 소싱된 다음, 필요한 [기본 제공 기술](cognitive-search-predefined-skills.md)을 사용하여 인덱싱 파이프라인을 통해 푸시됩니다.  

## <a name="when-to-use-ai-enrichment"></a>AI 보강를 사용 하는 경우

원시 콘텐츠가 구조화되지 않은 텍스트, 이미지 콘텐츠 또는 언어 감지 및 번역이 필요한 콘텐츠인 경우 기본 제공되는 인지 기술을 사용하는 것이 좋습니다. 기본 제공되는 인지 기술을 통해 AI를 적용하면 이 콘텐츠가 잠금 해제되어 검색 및 데이터 과학 앱에서의 가치 및 효용성이 늘어날 수 있습니다. 

또한 파이프라인에 통합하고 싶은 오픈 소스, 타사 또는 자사 코드가 있을 경우, 사용자 지정 기술을 추가하는 것이 좋을 수 있습니다. 다양 한 문서 유형의 두드러진 몇 가지 특성을 식별 하는 분류 모델은이 범주에 속하지만 콘텐츠에 값을 추가 하는 패키지를 사용할 수 있습니다.

### <a name="more-about-built-in-skills"></a>기본 제공 기술 추가 정보

기본 제공 기술을 사용 하 여 어셈블된 [기술](cognitive-search-defining-skillset.md) 는 다음과 같은 응용 프로그램 시나리오에 적합 합니다.

+ 전체 텍스트를 검색 가능하게 만들고 싶은 스캔한 문서(JPEG). OCR(광학 문자 인식) 기술을 연결하여 JPEG 파일의 텍스트를 식별, 추출 및 수집할 수 있습니다.

+ 이미지와 텍스트가 결합된 PDF PDF의 텍스트는 인덱싱 중에 보강 단계를 사용하지 않고 추출할 수 있지만, 이미지 및 자연어 처리를 추가하면 표준 인덱싱보다 더 나은 결과를 얻을 수 있습니다.

+ 언어 검색 그리고 가능하다면 텍스트 번역을 적용하고 싶은 다국어 콘텐츠

+ 내재된 의미가 있는 콘텐츠 또는 더 큰 문서에 숨겨진 컨텍스트를 포함하고 있는 비정형 또는 반정형 문서 

  특히 Blob은 단일 "필드"로 압축된 큰 콘텐츠 본문을 포함하는 경우가 많습니다. 이미지 및 자연어 처리 기술을 인덱서에 연결하면 원시 콘텐츠에 존재하지만, 그렇지 않은 경우 별도의 필드로 표시되는 새 정보를 만들 수 있습니다. 즉시 사용 가능한 기본 제공 인식 기술로는 핵심 문구 추출, 감정 분석 및 엔터티 인식(사람, 조직 및 위치)이 있습니다.

  또한 기본 제공 기술을 사용하여 텍스트 분할, 병합 및 셰이프 작업을 통해 콘텐츠를 재구성하는 데 사용할 수도 있습니다.

### <a name="more-about-custom-skills"></a>사용자 지정 기술 추가 정보

사용자 지정 기술은 양식 인식, 또는 [사용자 지정 기술 웹 인터페이스](cognitive-search-custom-skill-interface.md)에서 제공하고 래핑하는 모델을 사용하는 사용자 지정 엔터티 검색처럼 좀 더 복잡한 시나리오를 지원할 수 있습니다. 사용자 지정 기술의 예로는 [Form Recognizer](../cognitive-services/form-recognizer/overview.md), [Bing Entity Search API](./cognitive-search-create-custom-skill-example.md) 통합, [사용자 지정 엔터티 인식](https://github.com/Microsoft/SkillsExtractorCognitiveSearch) 등이 있습니다.

## <a name="steps-in-an-enrichment-pipeline"></a>보강 파이프라인의 단계 <a name="enrichment-steps"></a>

보강 파이프라인은 [*인덱서*](search-indexer-overview.md)를 기반으로 합니다. 인덱서는 문서 크랙에 대해 인덱스와 데이터 원본 간의 필드-필드 매핑을 기반으로 인덱스를 채웁니다. 이제 인덱서에 연결 된 기술은 사용자가 정의한 기술에 따라 문서를 가로채 고 보강 합니다. 인덱싱이 완료되면 [Azure Cognitive Search에서 지원하는 모든 쿼리 유형](search-query-overview.md)을 통한 검색 요청에서 콘텐츠에 액세스할 수 있습니다.  인덱서를 처음 접하는 경우, 이 섹션의 단계별 안내를 참조하세요.

### <a name="step-1-connection-and-document-cracking-phase"></a>1 단계: 연결 및 문서 크랙 단계

파이프라인의 시작 부분에는 구조화 되지 않은 텍스트 또는 텍스트가 아닌 콘텐츠 (예: 이미지, 스캔 한 문서 또는 JPEG 파일)가 있습니다. 데이터는 인덱서가 액세스할 수 있는 Azure 데이터 스토리지 서비스에 있어야 합니다. 인덱서는 원본 문서에서 텍스트를 추출하기 위해 원본 문서를 "크래킹"할 수 있습니다. 문서 크랙는 인덱싱 중 텍스트가 아닌 소스에서 텍스트 콘텐츠를 추출 하거나 만드는 프로세스입니다.

![문서 크래킹 단계](./media/cognitive-search-intro/document-cracking-phase-blowup.png "문서 크래킹")

 지원되는 원본에는 Azure Blob Storage, Azure Table Storage, Azure SQL Database 및 Azure Cosmos DB가 포함됩니다. 텍스트 기반 콘텐츠는 PDF, Word, PowerPoint, CSV 파일 형식에서 추출할 수 있습니다. 전체 목록은 [지원되는 형식](search-howto-indexing-azure-blob-storage.md#supported-document-formats)을 참조하세요. 인덱싱에 시간이 걸리므로 작은 대표 데이터 집합으로 시작한 다음, 솔루션의 완성도가 높아지면 증분 방식으로 빌드합니다.

### <a name="step-2-cognitive-skills-and-enrichment-phase"></a>2 단계: 인식 기술 및 보강 단계

보강는 원자성 작업을 수행 하는 *인지 기술을* 사용 하 여 수행 됩니다. 예를 들어 PDF를 깨진 후 엔터티 인식, 언어 검색 또는 키 구 추출을 적용 하 여 원본에서 기본적으로 사용할 수 없는 인덱스에 새 필드를 생성할 수 있습니다. 전체적으로 파이프라인에 사용된 기술 컬렉션을 기술 집합(*skillset*)이라고 합니다.  

![보강 단계](./media/cognitive-search-intro/enrichment-phase-blowup.png "보강 단계")

기술 집합은 [기본 제공 인지 기술](cognitive-search-predefined-skills.md) 또는 사용자가 제공하고 기술 집합에 연결하는 [사용자 지정 기술](cognitive-search-create-custom-skill-example.md)을 기반으로 합니다. 기술 집합은 매우 간단하거나 복잡할 수 있으며 처리 유형뿐만 아니라 작업 순서도 결정합니다. 기술 집합 및 인덱서의 일부로 정의된 필드 매핑은 보강 파이프라인을 완벽하게 지정합니다. 이 모든 부분을 한데 모으는 방법에 대한 자세한 정보 [기술 집합 정의](cognitive-search-defining-skillset.md)를 참조하세요.

내부적으로 파이프라인은 보강된 문서 컬렉션을 생성합니다. 보강된 문서의 어느 부분을 검색 인덱스의 인덱싱 가능 필드에 매핑할지 결정할 수 있습니다. 예를 들어 키 구 추출 및 엔터티 인식 기술을 적용 한 경우 이러한 새 필드는 보강 문서의 일부가 되며 인덱스의 필드에 매핑될 수 있습니다. 입/출력에 대한 자세한 내용은 [주석](cognitive-search-concept-annotations-syntax.md)을 참조하세요.

#### <a name="add-a-knowledgestore-element-to-save-enrichments"></a>보강 내용을 저장하는 knowledgeStore 요소 추가

[SEARCH REST api-version = 2020-06-30](/rest/api/searchservice/) 은 기술력과를 확장 하 `knowledgeStore` 고 강화 저장 방법을 설명 하는 Azure storage 연결 및 프로젝션을 제공 합니다. 인덱스에 추가 됩니다. 표준 AI 파이프라인에서 보강된 문서는 일시적이며 인덱싱 중에만 사용된 후에 삭제됩니다. 보강된 문서는 지식 저장소를 사용하여 유지됩니다. 자세한 내용은 [기술 자료 저장소](knowledge-store-concept-intro.md)를 참조 하세요.

### <a name="step-3-search-index-and-query-based-access"></a>3 단계: 인덱스 및 쿼리 기반 액세스 검색

처리가 완료되면 Azure Cognitive Search에서 전체 텍스트를 검색할 수 있는 보강된 문서로 구성된 검색 인덱스가 있습니다. [인덱스 쿼리](search-query-overview.md)는 개발자와 사용자가 파이프라인에서 생성된 보강된 콘텐츠에 액세스하는 방법입니다. 

![검색 아이콘이 있는 인덱스](./media/cognitive-search-intro/search-phase-blowup.png "검색 아이콘이 있는 인덱스")

인덱스는 Azure Cognitive Search에 대해 만들 수 있는 다른 항목과 비슷합니다. 사용자 지정 분석기를 사용하여 보완하거나, 유사 항목 검색 쿼리를 호출하거나, 필터링된 검색을 추가하거나, 점수 매기기 프로필을 실험하여 검색 결과를 변형시킬 수 있습니다.

인덱스는 채점 프로파일 및 동의어 맵과 같은 특정 인덱스에 연결된 필드, 특성 및 기타 구문을 정의하는 인덱스 스키마에서 생성됩니다. 인덱스가 정의되고 채워진 후에는 새 원본 문서나 업데이트된 원본 문서를 가져오기 위해 증분 방식으로 인덱싱할 수 있습니다. 특정 수정에는 전체 다시 빌드가 필요합니다. 스키마 디자인이 안정될 때까지 작은 데이터 집합을 사용해야 합니다. 자세한 내용은 [인덱스를 다시 빌드하는 방법](search-howto-reindex.md)을 참조하세요.

**검사 목록: 일반적인 워크플로**

1. Azure 원본 데이터에서 대표 샘플 하위 집합을 생성합니다. 인덱싱에 시간이 걸리므로 작은 대표 데이터 집합으로 시작한 다음, 솔루션의 완성도가 높아지면 증분 방식으로 빌드합니다.

1. Azure Cognitive Search에서 [데이터 원본 개체](/rest/api/searchservice/create-data-source)를 만들어 데이터 검색을 위한 연결 문자열을 제공합니다.

1. 보강 단계를 통해 [기술 집합](/rest/api/searchservice/create-skillset)을 만듭니다.

1. [인덱스 스키마](/rest/api/searchservice/create-index)를 정의합니다. 필드(*Field*) 컬렉션에 원본 데이터의 필드를 포함합니다. 보강 과정에서 만들어진 콘텐츠에 대해 생성된 값을 저장할 필드를 더 추가해야 합니다.

1. 데이터 원본, 기술 집합 및 인덱스를 참조하는 [인덱서](/rest/api/searchservice/create-indexer)를 정의합니다.

1. 인덱서 내에 *outputFieldMappings*를 추가합니다. 이 섹션에서는 기술 집합(3단계)의 출력을 인덱스 스키마(4단계)의 입력 필드에 매핑합니다.

1. Azure Cognitive Search에서 인덱서를 표현하기 위해 방금 만든 *인덱서 만들기* 요청(요청 본문에 인덱서 정의가 있는 POST 요청)을 보냅니다. 이 단계는 인덱서를 실행하여 파이프라인을 호출하는 방법에 대한 내용입니다.

1. 쿼리를 실행하여 결과를 평가하고 코드를 수정하여 기술 집합, 스키마 또는 인덱서 구성을 업데이트합니다.

1. 파이프라인을 다시 빌드하기 전에 [인덱서를 다시 설정](search-howto-reindex.md)합니다.

## <a name="next-steps"></a>다음 단계

+ [AI 보강 설명서 링크](cognitive-search-resources-documentation.md)
+ [예: AI 보강 사용자 지정 기술 만들기 (c #)](cognitive-search-create-custom-skill-example.md)
+ [빠른 시작: 포털 연습에서 AI 보강 사용해 보기](cognitive-search-quickstart-blob.md)
+ [자습서: AI 보강 Api에 대 한 자세한 정보](cognitive-search-tutorial-blob.md)
+ [지식 저장소](knowledge-store-concept-intro.md)
+ [REST에서 지식 저장소 만들기](knowledge-store-create-rest.md)
+ [문제 해결 팁](cognitive-search-concept-troubleshooting.md)