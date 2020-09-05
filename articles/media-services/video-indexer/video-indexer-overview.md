---
title: Azure Media Services Video Indexer란?
titleSuffix: Azure Media Services
description: 이 문서에서는 Azure Media Services Video Indexer 서비스 개요를 제공합니다.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/31/2020
ms.author: juliako
ms.openlocfilehash: b4d629351ae7b6154b332a1b1f99374de1187518
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89267873"
---
# <a name="what-is-azure-media-services-video-indexer"></a>Azure Media Services Video Indexer란?

VI(Video Indexer)는 Azure Media Services AI 솔루션이며 Azure Cognitive Services 브랜드의 일부입니다. Video Indexer는 여러 채널(음성, 발성, 시각적 개체)을 기반으로 하는 기계 학습 모델을 사용하여 깊이 있는 인사이트를 추출하는 기능을 제공합니다(데이터 분석 또는 코딩 기술이 필요 없음). 모델을 추가로 사용자 지정하고 학습할 수 있습니다. 이 서비스는 딥 서치를 가능하게 하고, 운영 비용을 절감하고, 새로운 수익 창출 기회를 가능하게 하고, 대규모 비디오 보관에 대한 새로운 사용자 환경을 만듭니다(진입 장벽이 낮음).

Video Indexer로 인사이트 추출을 시작하려면 계정을 만들고 비디오를 업로드해야 합니다. Video Indexer에 비디오를 업로드하면 Video Indexer는 다양한 AI 모델을 실행하여 시각적 개체와 오디오를 둘 다 분석합니다. Video Indexer가 비디오를 분석할 때 AI 모델이 인사이트를 추출합니다.

Video Indexer 계정을 만들고 Media Services에 연결 하는 경우 미디어 및 메타 데이터 파일은 해당 Media Services 계정과 연결 된 Azure storage 계정에 저장 됩니다. 자세한 내용은 [Azure에 연결 된 Video Indexer 계정 만들기](connect-to-azure.md)를 참조 하세요.

다음 다이어그램은 일러스트레이션이며, Video Indexer가 백 엔드에서 작동하는 방법에 관한 기술적 설명이 아닙니다.

![Azure Media Services Video Indexer 흐름 다이어그램](./media/video-indexer-overview/model-chart.png)


## <a name="compliance-privacy-and-security"></a>규정 준수, 개인 정보 보호 및 보안

중요한 알림을 사용하는 경우 Video Indexer 사용 시 적용 가능한 모든 법률을 준수해야 하며, 다른 사용자의 권리를 위반하거나 다른 사용자에게 해로운 방법으로 Video Indexer 또는 Azure 서비스를 사용해서는 안됩니다.

비디오/이미지를 Video Indexer에 업로드하기 전에, Video Indexer와 Azure에서 데이터를 사용하고, 처리하고, 저장하는 데 대한 비디오/이미지에 포함된 개인(있는 경우)의 모든 필요한 동의(법에서 요구되는 경우)를 비롯하여 비디오/이미지를 사용할 수 있는 모든 적절한 권한이 있어야 합니다. 일부 관할 지역에서는 생체 데이터와 같은 특정 범주의 데이터를 수집하고 온라인으로 처리하고 저장하는 데 대해 특별한 법적 요구 사항이 적용될 수 있습니다. Video Indexer와 Azure를 특별한 법적 요구 사항이 적용되는 데이터를 처리하고 저장하는 데 사용하기 전에, 귀하에게 적용될 수 있는 이러한 법적 요구 사항을 반드시 준수해야 합니다.

Video Indexer의 규정 준수, 개인 정보 보호 및 보안에 대해 알아보려면 Microsoft [보안 센터](https://www.microsoft.com/TrustCenter/CloudServices/Azure/default.aspx)를 참조하세요. Microsoft의 개인 정보 보호 의무, 데이터 삭제 방법을 비롯한 데이터 처리 및 보존 방식에 대한 자세한 내용은 Microsoft의 [개인정보처리방침](https://privacy.microsoft.com/PrivacyStatement), [Online Services 사용 약관](https://www.microsoft.com/licensing/product-licensing/products?rtc=1)("OST") 및 [Data Processing 추록](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67)("DPA")을 검토하세요. Video Indexer를 사용하면 OST, DPA 및 개인정보처리방침을 준수할 것에 동의하는 것입니다.

## <a name="what-can-i-do-with-video-indexer"></a>Video Indexer를 사용하여 무엇을 수행할 수 있나요?

Video Indexer의 인사이트는 다음과 같은 다양한 시나리오에 적용할 수 있습니다.

* ‘딥 서치’: 비디오에서 추출된 인사이트를 사용하여 비디오 라이브러리 전체에서 검색 환경을 개선합니다. 예를 들어 음성 단어와 얼굴을 인덱싱하면 비디오에서 사람이 특정 단어를 말했거나 두 사람이 함께 본 시점을 찾는 검색 환경을 사용할 수 있습니다. 비디오에서 이 인사이트에 기반한 검색은 뉴스 기관, 교육 기관, 방송사, 엔터테인먼트 콘텐츠 소유자, 엔터프라이즈 LOB 앱 및 일반적으로 사용자가 검색해야 하는 비디오 라이브러리가 있는 모든 산업에 적용됩니다.
* ‘콘텐츠 만들기’: Video Indexer가 콘텐츠에서 추출하는 인사이트를 기반으로 트레일러, 하이라이트 릴, 소셜 미디어 콘텐츠 또는 뉴스 클립을 만듭니다. 사람 및 레이블 모양의 키 프레임, 장면 제작자 및 타임스탬프를 통해 만들기 프로세스가 훨씬 더 원활하고 쉽게 진행되며 만들고 있는 콘텐츠에 필요한 비디오의 파트에 접근할 수 있습니다.
* ‘접근성’: 장애가 있는 사람이 콘텐츠를 사용할 수 있게 하거나 다양한 언어를 사용하여 여러 지역에 콘텐츠를 배포하려는 경우 Video Indexer에서 제공하는 기록과 번역을 여러 언어로 사용할 수 있습니다.
* ‘수익 창출’: Video Indexer는 비디오의 가치를 높이는 데 도움이 될 수 있습니다. 예를 들어 광고 수익(뉴스 미디어, 소셜 미디어 등)에 의존하는 산업은 추출된 인사이트를 광고 서버에 대한 추가 신호로 사용하여 관련 광고를 전달할 수 있습니다.
* ‘콘텐츠 조정’: 텍스트 및 시각적 콘텐츠 조정 모델을 사용하여 부적절한 콘텐츠로부터 사용자를 보호하고 게시하는 콘텐츠가 조직의 가치에 부합하는지 유효성을 검사합니다. 자동으로 특정 비디오를 차단하거나 사용자에게 콘텐츠에 관해 경고할 수 있습니다.
* *권장 사항*: 비디오 인사이트는 사용자에게 관련 비디오 시점을 강조하여 사용자 참여를 향상시키는 데 사용할 수 있습니다. 추가 메타데이터로 각 비디오에 태그를 지정하여 가장 관련성이 높은 비디오를 사용자에게 추천하고 필요 맞는 비디오 파트를 강조할 수 있습니다.

## <a name="features"></a>기능

다음 목록은 Video Indexer 비디오 및 오디오 모델을 사용하여 비디오에서 검색할 수 있는 인사이트를 보여 줍니다.

### <a name="video-insights"></a>비디오 인사이트

* **얼굴 감지**: 비디오에 표시되는 얼굴을 감지하고 그룹화합니다.
* **유명인 식별**: Video Indexer는 전 세계의 리더, 배우, 운동 선수, 연구원, 비즈니스 및 기술 리더와 같은 100만 명 이상의 유명인을 자동으로 식별합니다. 이와 같은 유명인에 관한 데이터는 다양한 웹 사이트(IMDB, Wikipedia 등)에서도 찾아볼 수 있습니다.
* **계정 기반 얼굴 인식**: Video Indexer는 특정 계정에 대한 모델을 학습합니다. 그런 다음, 학습된 모델을 기반으로 비디오에서 얼굴을 인식합니다. 자세한 내용은 [Video Indexer 웹 사이트에서 개인 모델 사용자 지정](customize-person-model-with-website.md) 및 [Video Indexer API를 사용하여 개인 모델 사용자 지정](customize-person-model-with-api.md)을 참조하세요.
* **얼굴의 썸네일 추출**(“최적 얼굴”): 각 얼굴 그룹에서 가장 잘 캡처된 얼굴을 자동으로 식별하고(품질, 크기 및 정면 위치 기준) 이미지 자산으로 추출합니다.
* **시각적 텍스트 인식**(OCR): 비디오에서 시각적으로 표시되는 텍스트를 추출합니다.
* **시각적 콘텐츠 조정**: 성인 및/또는 외설 시각적 개체를 검색합니다.
* **레이블 식별**: 표시되는 시각적 개체 및 작업을 식별합니다.
* **장면 구분**: 시각 신호를 기반으로 비디오에서 장면이 변경되는 시기를 결정합니다. 장면은 단일 이벤트를 묘사하며 의미상 관련된 일련의 연속 샷으로 구성됩니다.
* **샷 감지**: 시각 신호를 기반으로 비디오에서 샷이 변경되는 시기를 결정합니다. 샷은 동일한 영화 촬영 카메라에서 촬영된 일련의 프레임입니다. 자세한 내용은 [장면, 샷 및 키 프레임](scenes-shots-keyframes.md)을 참조하세요.
* **검정 프레임 감지**: 비디오에 표시되는 검은색 프레임을 식별합니다.
* **키 프레임 추출**: 비디오에서 안정적인 키 프레임을 검색합니다.
* **롤링 크레딧**: TV 프로그램과 영화의 끝 부분에 있는 롤링 크레딧의 시작과 끝을 식별합니다.
* **애니메이션 캐릭터 검색**(미리 보기): [Cognitive Services 사용자 지정 비전](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/)과 통합을 통해 애니메이션 콘텐츠에서 캐릭터를 검색, 그룹화 및 인식합니다. 자세한 내용은 [애니메이션 캐릭터 검색](animated-characters-recognition.md)을 참조하세요.
* **편집 샷 유형 검색**: 관련 유형(예: 와이드 샷, 중간 샷, 클로즈업, 익스트림 클로즈업, 2인 샷, 여러 사람, 실외, 실내 등)을 기준으로 샷에 태그를 지정합니다. 자세한 내용은 [편집 샷 유형 검색](scenes-shots-keyframes.md#editorial-shot-type-detection)을 참조하세요.

### <a name="audio-insights"></a>오디오 인사이트

* **오디오 전사**: 음성을 12개 언어의 텍스트로 변환하고 확장을 허용합니다. 지원되는 언어는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어(간체), 일본어, 아랍어, 러시아어, 포르투갈어, 힌디어 및 한국어입니다.
* **자동 언어 감지**: 기준 음성 언어를 자동으로 식별합니다. 지원되는 언어는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어(간체), 일본어, 러시아어 및 포르투갈어입니다. 언어를 확실하게 식별할 수 없는 경우 Video Indexer는 음성 언어를 영어로 가정합니다. 자세한 내용은 [언어 식별 모델](language-identification-model.md)을 참조하세요.
* **다국어 음성 식별 및 전사**(미리 보기): 오디오의 여러 세그먼트에서 음성 언어를 자동으로 식별합니다. 미디어 파일의 각 세그먼트를 전사되도록 전송한 다음 전사를 다시 하나의 통합된 전사로 결합합니다. 자세한 내용은 [자동으로 다국어 콘텐츠 식별 및 전사](multi-language-identification-transcription.md)를 참조하세요.
* **선택 자막**: VTT, TTML, SRT의 세 가지 형식으로 선택 자막을 만듭니다.
* **두 채널 처리**: 개별 대본을 자동 검색하고 단일 타임라인으로 병합합니다.
* **노이즈 감소**: 전화 통신 오디오 또는 노이즈가 많은 녹음을 지웁니다(Skype 필터 기준).
* **전사 사용자 지정**(CRIS): 사용자 지정 음성 텍스트 변환 모델을 학습하여 산업별 대본을 만듭니다. 자세한 내용은 [Video Indexer 웹 사이트에서 언어 모델 사용자 지정](customize-language-model-with-website.md) 및 [Video Indexer API를 사용하여 언어 모델 사용자 지정](customize-language-model-with-api.md)을 참조하세요.
* **화자 열거**: 어떤 화자가 어떤 단어를 언제 말했는지 매핑하고 파악합니다.
* **화자 통계**: 화자 음성 비율에 대한 통계를 제공합니다.
* **텍스트 콘텐츠 조정**: 오디오 전사에서 명시적 텍스트를 검색합니다.
* **오디오 효과**: 박수, 음성 및 묵음과 같은 오디오 효과를 식별합니다.
* **감정 감지**: 음성(말하는 내용) 및 음성 어조(말하는 방식)를 기반으로 하는 감정을 식별합니다. 감정은 joy(기쁨), sadness(슬픔), anger(분노) 또는 fear(두려움)일 수 있습니다.
* **번역**: 오디오 전사의 번역을 54개 언어로 만듭니다.

### <a name="audio-and-video-insights-multi-channels"></a>오디오 및 비디오 인사이트(다중 채널)

한 채널로 인덱싱하는 경우 해당 모델의 부분 결과가 제공됩니다.

* **키워드 추출**: 음성 및 시각적 텍스트에서 키워드를 추출합니다.
* **명명된 엔터티 추출**: NLP(자연어 처리)를 통해 음성 및 시각적 텍스트에서 브랜드, 위치 및 사람을 추출합니다.
* **주제 유추**: 전사에서 주요 주제를 유추합니다. IPTC 수준 2 분류가 포함됩니다.
* **아티팩트**: 각 모델에 대한 풍부한 아티팩트 세트(“다음 수준의 세부 정보”)를 추출합니다.
* **감정 분석**: 음성 및 시각적 텍스트에서 긍정적, 부정적 및 중립적 감정을 식별합니다.

## <a name="how-can-i-get-started-with-video-indexer"></a>Video Indexer를 시작하려면 어떻게 하나요?

다음 세 가지 방법으로 Video Indexer 기능에 액세스할 수 있습니다.

* Video Indexer 포털: 제품을 평가하고, 계정을 관리하고, 모델을 사용자 지정할 수 있는 사용하기 쉬운 솔루션입니다.

    포털에 관한 자세한 내용은 [Video Indexer 웹 사이트 시작](video-indexer-get-started.md)을 참조하세요.  

* API 통합: 앱 및 인프라에 솔루션을 통합할 수 있는 REST API를 통해 Video Indexer의 모든 기능을 사용할 수 있습니다.

    개발자로 시작하려면  [Video Indexer REST API 사용](video-indexer-use-apis.md)을 참조하세요.

* 포함할 있는 위젯: Video Indexer 인사이트, 플레이어 및 편집기 환경을 앱에 포함할 수 있습니다.

    자세한 내용은  [애플리케이션에 시각적 위젯 포함](video-indexer-embed-widgets.md)을 참조하세요.

웹 사이트를 사용하는 경우 인사이트는 메타데이터로 추가되며 포털에 표시됩니다. API를 사용하는 경우에는 인사이트를 JSON 파일로 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Video Indexer를 시작할 준비가 되었습니다. 자세한 내용은 다음 문서를 참조하세요.

- [Video Indexer 웹 사이트 시작](video-indexer-get-started.md)
- [Video Indexer REST API를 사용하여 콘텐츠 처리](video-indexer-use-apis.md)
- [애플리케이션에 시각적 위젯 포함](video-indexer-embed-widgets.md)
