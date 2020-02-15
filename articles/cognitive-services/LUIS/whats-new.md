---
title: 새로운 기능-Language Understanding (LUIS)
description: 이 문서는 Azure Cognitive Services Language Understanding API 대 한 뉴스로 정기적으로 업데이트 됩니다.
ms.topic: conceptual
ms.date: 02/11/2020
ms.openlocfilehash: 716860b54e7d8e75984c0365cac61d14153c09ff
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137799"
---
# <a name="whats-new-in-language-understanding"></a>Language Understanding의 새로운 기능

서비스의 새로운 기능에 대해 알아봅니다. 이러한 항목에는 릴리스 정보, 비디오, 블로그 게시물 및 다른 유형의 정보가 포함 됩니다. 서비스를 최신 상태로 유지하려면 이 페이지에 책갈피를 설정합니다.

## <a name="release-notes"></a>릴리스 정보

### <a name="november-4-2019---ignite"></a>2019 년 11 월 4 일-Ignite

* [LUIS 및 Azure Cognitive Services를 사용 하 여 비디오-NLU (고급 자연 Language Understanding) 모델 BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* 개발자 생산성 향상
    * [예측 끝점 V3](luis-migration-api-v3.md)의 일반 공급입니다.
    * . Lu ([Ludown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) 형식을 사용 하 여 앱을 가져오고 내보낼 수 있습니다. 이를 통해 효과적인 CI/CD 프로세스를 마련 수 있습니다.
* 언어 확장
    * 공개 미리 보기로 제공 되는 [아랍어 및 힌디어](luis-language-support.md) .
* 미리 작성된 모델
    * [미리 빌드된 도메인](luis-reference-prebuilt-domains.md) 은 이제 GA (일반 공급)로 제공 됩니다.
    * 일본어 [미리](luis-reference-prebuilt-entities.md#japanese-entity-support) 작성 된 엔터티-나이, 통화, 숫자 및 백분율이 V3에서 지원 되지 않습니다.
    * 이탈리아어로 미리 작성 된 [엔터티](luis-reference-prebuilt-entities.md#italian-entity-support) -나이, 통화, 차원, 숫자 및 백분율 해상도가 V2에서 변경 되었습니다.
* [Preview.luis.ai 포털](https://preview.luis.ai) 의 향상 된 사용자 환경-복잡 한 모델을 작성 하 고 디버그할 수 있도록 개선 된 레이블 지정 환경 Preview 포털 자습서를 사용해 보세요.
    * [의도만](tutorial-intents-only.md)
    * [없습니다 machine-학습 한 엔터티](tutorial-machine-learned-entity.md)
* 언어를 보다 잘 이해 하는 고급 기능을 사용 하 여 [정교한 언어 모델을 작성](luis-concept-entity-types.md) 합니다.
* 모델 수준에서 기계 학습 기능을 정의 하 고 모델을 다른 모델에 대 한 신호로 사용할 수 있도록 설정 합니다. 예를 들어 엔터티를 의도 및 기타 엔터티에 대 한 기능으로 사용할 수 있습니다.
* 새로운 확장 된 [제한](luis-boundaries.md) -구 목록 및 총 구에 대 한 최대, 새 모델, 기능 제한
* 심층 계층 구조 형식의 텍스트에서 정보를 추출 하 여 대화 응용 프로그램을 더욱 강력 하 게 만듭니다.

    ![컴퓨터에서 배운 엔터티 이미지](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>2019 년 9 월 3 일

* Azure 제작 리소스- [지금 마이그레이션](luis-migration-authoring.md).
    * Azure 리소스 당 500 앱
    * 앱 당 100 버전
* 미리 빌드된 엔터티에 대 한 터키어 지원
* DatetimeV2에 대 한 이탈리아어 지원

### <a name="july-23-2019"></a>2019 년 7 월 23 일

* [인식자 텍스트](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) 를 1.2.3로 업데이트 합니다.
    * 이탈리아어의 나이, 온도, 차원 및 통화 인식기를 사용 합니다.
    * 음성 인식의 향상으로, 추수 추수 a를 정확 하 게 계산할 수 있습니다.
    * 날짜 및 시간이 아닌 엔터티의 가양성을 줄이기 위해 프랑스어 DateTime의 향상 된 기능
    * 영어 DateRange의 calendar/school/회계 연도 및 머리글자어 지원.
    * 중국어 및 일본어로 PhoneNumber 인식 기능이 향상 되었습니다.
    * NumberRange에 대 한 지원이 향상 되었습니다.
    * 성능 향상.

### <a name="june-24-2019"></a>6 월 24 일, 2019

* Next, previous 및 last와 같은 정렬을 지원 하도록 미리 작성 된 [엔터티를 OrdinalV2](luis-reference-prebuilt-ordinal-v2.md) 합니다. 영어 문화권에만 해당 합니다.

### <a name="may-6-2019---build-conference"></a>2019 년 5 월 6 일, 회의 빌드

다음 기능은 빌드 2019 회의에서 릴리스 되었습니다.

* [V3 API 마이그레이션 가이드 미리 보기](luis-migration-api-v3.md)
* [향상 된 분석 대시보드](luis-how-to-use-dashboard.md)
* [향상 된 미리 작성 된 도메인](luis-reference-prebuilt-domains.md)
* [동적 목록 엔터티](luis-migration-api-v3.md#dynamic-lists-passed-in-at-prediction-time)
* [외부 엔터티](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>블로그

[Bot Framework](https://blog.botframework.com/)

## <a name="videos"></a>비디오

### <a name="2019-ignite-videos"></a>2019 Ignite 비디오

[LUIS 및 Azure Cognitive Services를 사용 하는 NLU (고급 자연 Language Understanding) 모델 BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 비디오 빌드

[Azure 대화형 AI를 사용 하 여 차세대 비즈니스를 위해 비즈니스를 확장 하는 방법](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>서비스 업데이트

[Cognitive Services에 대한 Azure 업데이트 공지](https://azure.microsoft.com/updates/?product=cognitive-services)
