---
title: AI 보강에 대한 설명서 링크
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 AI 보강 작업과 관련된 문서, 자습서, 샘플 및 블로그 게시물에 대해 주석이 달린 목록입니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/12/2020
ms.openlocfilehash: eaafb9536a8667417c0abf4ab570609f2db290bc
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935350"
---
# <a name="documentation-resources-for-ai-enrichment-in-azure-cognitive-search"></a>Azure Cognitive Search의 AI 보강에 대한 설명서 리소스

AI 보강는 텍스트 원본이 아닌 소스에서 숨어 있는 정보를 찾아 Azure Cognitive Search의 전체 텍스트 검색 가능 콘텐츠로 변환 하는 인덱서 기반 인덱싱의 추가 기능입니다. 

기본 제공 처리의 경우 Cognitive Services의 미리 학습 된 AI 모델을 내부적으로 호출 하 여 분석을 수행 합니다. Azure Machine Learning, Azure Functions 또는 기타 방법을 사용 하 여 사용자 지정 모델을 통합할 수도 있습니다.

다음은 AI 보강 설명서의 통합 목록입니다.

## <a name="concepts"></a>개념

+ [AI 강화](cognitive-search-concept-intro.md)
+ [기술 세트](cognitive-search-working-with-skillsets.md)
+ [디버그 세션](cognitive-search-debug-session.md)
+ [지식 저장소](knowledge-store-concept-intro.md)
+ [프로젝션](knowledge-store-projection-overview.md)
+ [증분 보강 (캐시 된 보강 문서 재사용)](cognitive-search-incremental-indexing-conceptual.md)

## <a name="hands-on-walkthroughs"></a>실습 연습

+ [빠른 시작: Azure Portal에서 인지 기술 만들기](cognitive-search-quickstart-blob.md)
+ [자습서: AI를 사용 하 여 보강 인덱싱](cognitive-search-tutorial-blob.md)
+ [자습서: 디버그 세션을 사용 하 여 기술에 대 한 변경 진단, 복구 및 커밋](cognitive-search-tutorial-debug-sessions.md)

## <a name="knowledge-stores"></a>지식 저장소

+ [빠른 시작: Azure Portal에서 기술 자료 저장소 만들기](knowledge-store-create-portal.md)
+ [REST 및 Postman을 사용하여 지식 저장소 만들기](knowledge-store-create-rest.md)
+ [Storage Explorer를 사용하여 지식 저장소 보기](knowledge-store-view-storage-explorer.md)
+ [Power BI를 사용하여 지식 저장소 연결](knowledge-store-connect-power-bi.md)
+ [프로젝션 예제 (강화를 모양 및 내보내는 방법)](knowledge-store-projections-examples.md)

## <a name="custom-skills-advanced"></a>사용자 지정 기술 (고급)

+ [사용자 정의 기술 인터페이스를 정의하는 방법](cognitive-search-custom-skill-interface.md)
+ [예: Azure Functions 및 Bing Entity Search Api를 사용 하 여 사용자 지정 기술 만들기](cognitive-search-create-custom-skill-example.md)
+ [예: Python을 사용 하 여 사용자 지정 기술 만들기](cognitive-search-custom-skill-python.md)
+ [예: 양식 인식기를 사용 하 여 사용자 지정 기술 만들기](cognitive-search-custom-skill-form.md) 
+ [예: Azure Machine Learning을 사용 하 여 사용자 지정 기술 만들기](cognitive-search-tutorial-aml-custom-skill.md) 

## <a name="how-to-guidance"></a>방법 가이드

+ [Cognitive Services 리소스 연결](cognitive-search-attach-cognitive-services.md)
+ [기술 집합 정의](cognitive-search-defining-skillset.md)
+ [기술의 참조 주석](cognitive-search-concept-annotations-syntax.md)
+ [인덱스에 필드 매핑](cognitive-search-output-field-mapping.md)
+ [이미지에서 정보 처리 및 추출](cognitive-search-concept-image-scenarios.md)
+ [증분 보강에 대 한 캐싱 구성](search-howto-incremental-index.md)
+ [Azure Cognitive Search 인덱스를 다시 빌드하는 방법](search-howto-reindex.md)
+ [디자인 팁](cognitive-search-concept-troubleshooting.md)
+ [일반적인 오류 및 경고](cognitive-search-common-errors-warnings.md)

## <a name="skills-reference"></a>기술 참조

+ [기본 제공 기술](cognitive-search-predefined-skills.md)
  + [Microsoft.Skills.Text.KeyPhraseExtractionSkill](cognitive-search-skill-keyphrases.md)
  + [Microsoft.Skills.Text.LanguageDetectionSkill](cognitive-search-skill-language-detection.md)
  + [Microsoft.Skills.Text.EntityRecognitionSkill](cognitive-search-skill-entity-recognition.md)
  + [Microsoft.Skills.Text.MergeSkill](cognitive-search-skill-textmerger.md)
  + [Microsoft.Skills.Text.PIIDetectionSkill](cognitive-search-skill-pii-detection.md)
  + [Microsoft.Skills.Text.SplitSkill](cognitive-search-skill-textsplit.md)
  + [Microsoft.Skills.Text.SentimentSkill](cognitive-search-skill-sentiment.md)
  + [Microsoft.Skills.Text.TranslationSkill](cognitive-search-skill-text-translation.md)
  + [Microsoft.Skills.Vision.ImageAnalysisSkill](cognitive-search-skill-image-analysis.md)
  + [Microsoft.Skills.Vision.OcrSkill](cognitive-search-skill-ocr.md)
  + [Microsoft.Skills.Util.ConditionalSkill](cognitive-search-skill-conditional.md)
  + [Microsoft.Skills.Util.DocumentExtractionSkill](cognitive-search-skill-document-extraction.md)
  + [Microsoft.Skills.Util.ShaperSkill](cognitive-search-skill-shaper.md)

+ 사용자 지정 기술
  + [Microsoft. 사용자 지정. AmlSkill](cognitive-search-aml-skill.md)
  + [Microsoft.Skills.Custom.WebApiSkill](cognitive-search-custom-skill-web-api.md)

+ [사용되지 않는 기술](cognitive-search-skill-deprecated.md)
  + [Microsoft.Skills.Text.NamedEntityRecognitionSkill](cognitive-search-skill-named-entity-recognition.md)

## <a name="apis"></a>API

+ [REST API](/rest/api/searchservice/)
  + [Create 기술 (api-version = 2020-06-30)](/rest/api/searchservice/create-skillset)
  + [Create 인덱서 (api-version = 2020-06-30)](/rest/api/searchservice/create-indexer)

## <a name="see-also"></a>참조

+ [Azure Cognitive Search REST API](/rest/api/searchservice/)
+ [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
+ [Azure Cognitive Search란?](search-what-is-azure-search.md)