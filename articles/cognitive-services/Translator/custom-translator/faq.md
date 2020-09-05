---
title: Faq (질문과 대답)-사용자 지정 변환기
titleSuffix: Azure Cognitive Services
description: 이 문서에는 Azure Cognitive Services 사용자 지정 변환기에 대 한 질문과 대답이 포함 되어 있습니다.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: reference
ms.openlocfilehash: 06caafe75682a2375a8023787f9905ca1e8117c9
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88507232"
---
# <a name="custom-translator-frequently-asked-questions"></a>사용자 지정 변환기 faq (질문과 대답)

이 문서에는 [Custom Translator](https://portal.customtranslator.azure.ai)에 대해 자주 묻는 질문과 대답이 포함되어 있습니다.

## <a name="what-are-the-current-restrictions-in-custom-translator"></a>Custom Translator의 현재 제한은 무엇인가요?

파일 크기, 모델 학습 및 모델 배포에 대해 제한 사항 및 제한이 있습니다. Custom Translator에서 모델을 빌드하도록 학습을 설정할 때 이러한 제한 사항을 염두에 두십시오.

- 제출된 파일 크기는 100MB 미만이어야 합니다.
- 단일어 데이터는 지원되지 않습니다.

## <a name="when-should-i-request-deployment-for-a-translation-system-that-has-been-trained"></a>학습을 수행한 번역 시스템에 대한 배포는 언제 요청해야 하나요?

프로젝트에 대한 최적의 번역 시스템을 만들기 위해서는 몇 가지 학습이 필요할 수 있습니다. BLEU 점수 및/또는 테스트 결과가 만족스럽지 않은 경우 더 많은 학습 데이터 또는 더 신중하게 필터링된 데이터를 사용하고자 할 수 있습니다. 번역하려는 자료의 용어 및 스타일을 완벽하게 표현하려면 튜닝 세트 및 테스트 설정을 엄격하고 신중하게 디자인해야 합니다. 학습 데이터를 보다 더 자유롭게 구성하고 다양한 옵션을 사용하여 실험할 수 있습니다. 시스템 테스트 결과의 번역에 만족하고, 학습된 시스템을 개선하기 위해 학습에 추가할 데이터가 더 이상 없으며, 학습된 모델에 API를 통해 액세스하려는 경우 시스템 배포를 요청합니다.

## <a name="how-many-trained-systems-can-be-deployed-in-a-project"></a>프로젝트에 학습된 시스템은 몇 개 배포할 수 있나요?

프로젝트당 하나의 학습된 시스템만 배포할 수 있습니다. 프로젝트에 적합한 번역 시스템을 만들려면 몇 가지 학습이 필요할 수 있으며 최상의 결과를 제공하는 학습의 배포를 요청하는 것이 좋습니다. BLEU 점수별로(높을수록 좋음) 학습의 품질을 결정하고, 번역의 품질이 배포에 적합한지 결정하기 전에 검토자와 상의할 수 있습니다.

## <a name="when-can-i-expect-my-trainings-to-be-deployed"></a>내 학습은 언제 배포되나요?

배포에는 일반적으로 1시간 미만이 소요됩니다.

## <a name="how-do-you-access-a-deployed-system"></a>배포된 시스템에는 어떻게 액세스하나요?

배포된 시스템은 CategoryID를 지정하여 Microsoft Translator Text API V3을 통해 액세스할 수 있습니다. Translator Text API에 대한 자세한 정보는 [API 참조](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) 웹 페이지에서 확인할 수 있습니다.

## <a name="how-do-i-skip-alignment-and-sentence-breaking-if-my-data-is-already-sentence-aligned"></a>내 데이터가 이미 정렬된 문장인 경우 정렬 및 문장 분리를 건너뛰려면 어떻게 해야 하나요?

Custom Translator는 `.align` 확장을 사용한 TMX 파일 및 텍스트 파일에서 문장 정렬 및 문장 분리를 건너뜁니다. `.align` 파일은 사용자에게 완벽하게 정렬되어 있고 더 이상 처리할 필요가 없는 파일에 대해 Custom Translator의 문장 분리 및 정렬 프로세스를 건너뛰는 옵션을 제공합니다. 완벽하게 정렬된 파일에 대해서만 `.align` 확장을 사용하는 것이 좋습니다.

추출된 문장의 수가 동일한 기본 이름을 가진 두 파일과 일치하지 않는 경우 Custom Translator는 `.align` 파일에서 문장 정렬기를 계속 실행합니다.

## <a name="i-tried-uploading-my-tmx-but-it-says-document-processing-failed"></a>TMX를 업로드 하려고 했지만 "문서 처리에 실패 했습니다." 라는 메시지가 표시 됨


TMX가 <https://www.gala-global.org/tmx-14b>에 있는 TMX 1.4b 사양을 준수하는지 확인하십시오.
