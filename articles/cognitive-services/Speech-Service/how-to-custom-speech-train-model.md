---
title: Custom Speech 음성 서비스를 위한 모델 학습
titleSuffix: Azure Cognitive Services
description: 음성 텍스트 모델을 학습 하면 Microsoft의 기본 모델이 나 사용자 지정 모델에 대 한 인식 정확도를 향상 시킬 수 있습니다. 사람 레이블 기록 및 관련된 텍스트를 사용하여 모델을 학습시킵니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: a2bc39a35299f56ba52a0143ce123560bd4d88fa
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137764"
---
# <a name="train-a-model-for-custom-speech"></a>사용자 지정 음성 모델 학습

음성 텍스트 모델을 학습 하면 Microsoft의 기본 모델에 대 한 인식 정확도를 향상 시킬 수 있습니다. 사람 레이블 기록 및 관련된 텍스트를 사용하여 모델을 학습시킵니다. 이전에 업로드된 오디오 데이터와 함께 이러한 데이터 집합은 단어, 구, 약어, 이름 및 제품 관련 용어를 인식하는 음성-텍스트 모델을 학습시키고 다듬는 데 사용됩니다. 제공하는 도메인 내부 데이터 집합(사용자가 말할 내용과 인식될 것으로 기대하는 관련된 데이터)이 많을수록 모델이 더 정확해져, 인식 결과가 향상됩니다. 관련 없는 데이터를 학습에 추가하게 되면 모델의 정확도를 저하하거나 해칠 수 있다는 것을 명심해야 합니다.

## <a name="use-training-to-resolve-accuracy-issues"></a>정확도 문제를 해결하기 위해 학습 사용

모델에서 인식 문제가 발생할 경우, 추가 학습에 대한 사람 레이블 기록과 관련 데이터를 사용하면 정확도를 향상시키는 데 도움이 됩니다. 이 표를 사용하여 문제를 해결하는 데 사용할 데이터 집합을 결정합니다.

| 사용 사례 | 데이터 형식 |
| -------- | --------- |
| 의료 용어 또는 IT 전문 용어와 같은 업계 별 어휘 및 문법에 대 한 인식 정확도를 향상 시킵니다. | 관련 텍스트 (문장/길이 발언) |
| 제품 이름 또는 머리글자어와 같이 비표준 발음을 포함 하는 단어 또는 용어의 윗주와 표시 된 형태를 정의 합니다. | 관련된 텍스트(발음) |
| 말하는 스타일, 강조 또는 특정 배경 소음에 대 한 인식 정확도를 향상 시킵니다. | 오디오 + 사람 레이블 기록 |

> [!IMPORTANT]
> 데이터 집합을 업로드 하지 않은 경우 [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)를 참조 하세요. 이 문서에서는 데이터를 업로드하는 것에 대한 지침 및 고품질 데이터 집합 만들기에 대한 지침을 제공합니다.

## <a name="train-and-evaluate-a-model"></a>모델 학습 및 평가

모델을 학습시키는 첫 번째 단계는 학습 데이터를 업로드하는 것입니다. [데이터 준비 및 테스트](how-to-custom-speech-test-data.md) 를 사용 하 여 사용자에 게 레이블이 지정 된 텍스트 (길이 발언 및 발음)를 준비 하는 단계별 지침을 확인 합니다. 학습 데이터를 업로드한 후, 모델을 학습시키려면 다음 지침을 따릅니다.

1. [Custom Speech 포털](https://speech.microsoft.com/customspeech)에 로그인 합니다.
2. **음성 텍스트 > Custom Speech > 학습**으로 이동 합니다.
3. **모델 학습**을 클릭 합니다.
4. 다음으로, 교육에 **이름** 및 **설명을**제공 합니다.
5. **시나리오 및 기준 모델** 드롭다운 메뉴에서 도메인에 가장 적합 한 시나리오를 선택 합니다. 선택할 시나리오를 잘 모르겠으면 **일반**을 선택 합니다. 기준 모델은 학습을 위한 시작 지점입니다. 선호하는 것이 없는 경우, 최신을 사용할 수 있습니다.
6. 학습 **데이터 선택** 페이지에서 학습에 사용할 오디오 + 사람이 레이블 지정 된 기록 데이터 집합을 하나 또는 여러 개 선택 합니다.
7. 학습이 완료되면 새로 학습된 모델에서 정확도 테스트를 수행하는 것을 선택할 수 있습니다. 이 단계는 선택 항목입니다.
8. 만들기를 선택 하 여 사용자 지정 모델을 **만듭니다** .

학습 표는 새롭게 생성된 모델에 해당하는 새 항목을 표시합니다. 또한 테이블에는 처리 중, 성공, 실패 됨 상태가 표시 됩니다.

## <a name="evaluate-the-accuracy-of-a-trained-model"></a>학습된 모델의 정확도 평가

다음 문서를 사용하여 데이터를 검사하고 모델 정확도를 평가할 수 있습니다.

- [데이터 검사](how-to-custom-speech-inspect-data.md)
- [데이터 평가](how-to-custom-speech-evaluate-data.md)

정확도를 테스트하려는 경우, 실제 모델의 성능을 파악할 수 있도록 모델에서 사용한 것과 다른 음향 데이터 집합을 선택하는 것이 중요합니다.

## <a name="next-steps"></a>다음 단계

- [모델 배포](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>추가 리소스

- [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
- [데이터 검사](how-to-custom-speech-inspect-data.md)
- [데이터 평가](how-to-custom-speech-evaluate-data.md)
- [모델 학습](how-to-custom-speech-train-model.md)
