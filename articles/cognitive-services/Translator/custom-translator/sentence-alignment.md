---
title: 문장 페어링 및 정렬 - Custom Translator
titleSuffix: Azure Cognitive Services
description: 훈련이 실행되는 동안, 병렬 문서에 포함된 문장들이 페어링 또는 정렬됩니다. Custom Translator는 하나의 문장과 이 문장의 번역을 읽어서 한 번에 한 문장씩 번역을 학습합니다. 그런 다음 두 문장의 단어와 구문을 서로 정렬합니다.
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 04/19/2021
ms.author: lajanuar
ms.topic: conceptual
ms.openlocfilehash: d775496ad9490cfac81eecc9c08ef7beddea73dc
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111968125"
---
# <a name="sentence-pairing-and-alignment-in-parallel-documents"></a>병렬 문서의 문장 페어링 및 정렬

문서를 업로드한 후에는 병렬 문서에 있는 문장이 페어링되거나 정렬됩니다. Custom Translator는 페어링한 문장의 개수를 각 데이터 세트에서 정렬된 문장으로 보고합니다.

## <a name="pairing-and-alignment-process"></a>페어링 및 정렬 프로세스

Custom Translator는 한 번에 한 문장씩 문장의 번역을 학습합니다. 원본 텍스트에서 문장을 읽은 다음 대상 텍스트에서 이 문장의 번역을 읽습니다. 그런 다음 두 문장의 단어와 구문을 서로 정렬합니다. 이 프로세스를 통해 하나의 문장에 포함된 단어와 구문이 이 문장의 번역에 포함된 동등한 단어와 구문으로 매핑됩니다. 정렬 과정에서는 시스템이 서로가 서로의 번역인 문장을 훈련하도록 확인하는 작업이 이루어집니다.

## <a name="pre-aligned-documents"></a>미리 정렬된 문서

이미 병렬 문서를 가지고 있다면 미리 정렬된 텍스트 파일을 입력하여 문장 정렬 과정을 무시할 수 있습니다. 두 문서에서 모든 문장을 텍스트 파일로 추출하고 한 줄에 한 문장씩 구성한 다음 `.align` 확장자로 업로드하면 됩니다. Custom Translator는 `.align` 확장자가 입력되면 문장 정렬을 건너뜁니다.

최상의 결과를 얻으려면 파일에서 한 줄에 한 문장이 들어가도록 하세요. 문장 안에 줄 바꿈 문자가 있으면 정렬이 제대로 이루어지지 않으니 주의하세요.

## <a name="suggested-minimum-number-of-sentences"></a>권장 최소 문장 수

아래 표에는 각 문서 유형에서 학습이 성공하는 데 필요한 최소 문장의 수가 나와 있습니다.이러한 제한 사항은 병렬 문장에 번역 모델을 성공적으로 학습시킬 수 있는 충분한 고유 어휘가 포함되도록 하는 안전망입니다. 일반 지침은 인간 번역 품질의 도메인 내 병렬 문장이 많을수록 생성되는 모델의 품질이 높아진다는 것입니다.

| 문서 형식   | 권장 최소 문장 수 | 최대 문장 수 |
|------------|--------------------------------------------|--------------------------------|
| 학습   | 10000                                     | 상한 없음                 |
| 튜닝     | 500                                      | 2,500       |
| 테스트    | 500                                      | 2,500  |
| Dictionary | 0                                          | 250,000                 |

> [!NOTE]
>
> - 학습을 위한 최소 문장 수 10,000개가 충족되지 않으면 교육이 시작되지 않으며 실패합니다.
> - 튜닝 및 테스트는 선택 사항입니다. 이를 제공하지 않으면 시스템은 유효성 검사 및 테스트에 사용할 적절한 비율을 학습에서 제거합니다.
> - 사전 데이터만 사용하여 모델을 학습할 수 있습니다. [사전이란?](./what-is-dictionary.md)을 참조하세요.
> -  사전에 250,000개를 초과한 문장이 포함된 경우 문서 번역기를 선택하는 것이 더 좋습니다. [문서 Translator](../document-translation/overview.md)를 참조하세요.
> - 무료(F0) 구독 학습의 최대 제한은 2,000,000자입니다. 

## <a name="next-steps"></a>다음 단계

- Custom Translator에서 [사전](what-is-dictionary.md)을 사용하는 방법을 알아봅니다.