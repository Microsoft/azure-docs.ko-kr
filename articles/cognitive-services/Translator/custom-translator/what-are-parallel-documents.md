---
title: 병렬 문서란? - Custom Translator
titleSuffix: Azure Cognitive Services
description: 병렬 문서는 서로가 서로의 번역인 문서 쌍입니다. 이 쌍에 포함된 한 문서에는 원본 언어의 문장이 포함되어 있고, 다른 문서에는 이 문장을 대상 언어로 번역한 문장이 포함되어 있습니다.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: ce9f7e922b9a168205ea1d193830dd654691998a
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510813"
---
# <a name="what-are-parallel-documents"></a>병렬 문서란?

병렬 문서는 서로가 서로의 번역인 문서 쌍입니다. 이 쌍에 포함된 한 문서에는 원본 언어의 문장이 포함되어 있고, 다른 문서에는 이 문장을 대상 언어로 번역한 문장이 포함되어 있습니다.
어떤 언어가 "소스"로 표시되고 어떤 언어가 "대상"으로 표시되는지는 중요하지 않습니다. 병렬 문서는 어느 방향으로든 번역 시스템을 학습시키는 데 사용할 수 있습니다.

## <a name="requirements"></a>요구 사항

시스템을 학습 하려면 최소 1만의 고유한 맞춤 병렬 문장이 필요 합니다. 이러한 제한 사항은 병렬 문장에 번역 모델을 성공적으로 학습할 수 있는 충분 한 고유 어휘가 포함 되어 있는지 확인 하는 보안 네트워크입니다. 변환 시스템의 품질을 향상 시키기 위해 지속적으로 더 많은 병렬 콘텐츠와 다시 학습를 추가 하는 것이 가장 좋습니다. [문장 맞춤](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment)을 참조 하십시오.

Microsoft에서는 Custom Translator에 업로드된 문서가 타사의 저작권 또는 지적 재산권을 위반하지 말 것을 요구합니다. 자세한 내용은 [사용 약관](https://azure.microsoft.com/support/legal/cognitive-services-terms/)을 참조하세요.
포털을 사용하여 문서를 업로드하면 문서 자체의 지적 재산 소유권은 변경되지 않습니다.

## <a name="use-of-parallel-documents"></a>병렬 문서 사용

병렬 문서는 다음과 같은 시스템에서 사용됩니다.

1.  단어, 구 및 문장이 일반적으로 두 언어 사이에 매핑되는 방법을 알아봅니다.

2.  주변 구에 따라 적절한 컨텍스트를 처리하는 방법을 알아봅니다. 단어는 항상 다른 언어의 정확히 동일한 단어로 번역되지 않을 수 있습니다.

모범 사례로, 소스 언어 및 대상 언어 버전의 문서 간에 1:1 문장 대응 관계가 있어야 합니다.

프로젝트가 특정 도메인(범주)에 속하는 경우 문서는 해당 범주 내에서 용어가 일치해야 합니다. 결과 번역 시스템의 품질은 문장의 품질 및 문서 집합의 문장 수에 따라 달라집니다. 범주에 특정한 단어에 대한 다양한 용도의 더 많은 예제를 포함하면 할수록, 시스템은 번역하는 동안 더 나은 작업을 수행할 수 있습니다.

업로드된 문서는 각 작업 영역에 대해 프라이빗이며, 원하는 만큼 많은 프로젝트나 학습에 사용할 수 있습니다. 문서에서 추출된 문장은 일반 유니코드 텍스트 파일로 리포지토리에 별도 저장되고 삭제하는 데 사용할 수 있습니다. Custom Translator를 문서 리포지토리로 사용하지 않습니다. 그렇지 않으면 업로드한 문서를 업로드한 형식으로 다운로드할 수 없습니다.



## <a name="next-steps"></a>다음 단계

- Custom Translator에서 [사전](what-is-dictionary.md)을 사용하는 방법을 알아봅니다.
