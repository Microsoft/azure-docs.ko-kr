---
title: 작업 영역 및 프로젝트란? - Custom Translator
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 사용자 지정 변환기 서비스의 프로젝트 범주와 레이블 뿐만 아니라 작업 영역과 프로젝트 간의 차이점을 설명 합니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 08/17/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 41586b2ec96d30fd583cbc9a746493ed9206548b
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88510745"
---
# <a name="what-is-a-custom-translator-workspace"></a>Custom Translator 작업 영역이란?

작업 영역이란 사용자 지정 번역 시스템을 구성하고 빌드하기 위한 작업 구축에 대 한 작업 공간입니다. 적업 영역은 여러 프로젝트, 모델 및 문서를 포함할 수 있습니다. Custom Translator에서 수행하는 모든 작업은 특정 작업 영역 내부에 있습니다.

작업 영역은 사용자 및 사용자 작업 영역에 초대한 사람에게만 프라이빗됩니다. 초대받지 않은 사람은 사용자 작업 영역의 콘텐츠에 액세스할 수 없습니다. 원하는 만큼의 사람들을 작업 영역으로 초대하고 언제든지 해당 사용자의 액세스를 수정하거나 제거할 수 있습니다. 또한 새 작업 영역을 만들 수도 있습니다. 기본적으로 작업 영역은 다른 작업 영역 내에 있는 프로젝트 또는 문서를 포함하지 않습니다.

## <a name="what-is-a-custom-translator-project"></a>Custom Translator 프로젝트란?

프로젝트란 모델, 문서 및 테스트에 대한 래퍼입니다. 각 프로젝트에는 올바른 언어 쌍이 있는 해당 작업 영역에 업로드된 모든 문서가 자동으로 포함됩니다. 예를 들어 영어-스페인어 프로젝트와 스페인어-영어 프로젝트가 모두 있다면 동일한 문서가 두 프로젝트 모두에 포함됩니다. 각 프로젝트에는 번역에 대해 [V3 API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate?tabs=curl)를 쿼리할 때 사용되는 연결된 CategoryID가 있습니다. CategoryID는 Custom Translator로 빌드된 사용자 지정 시스템에서 번역을 가져오는 데 사용되는 매개 변수입니다.

## <a name="project-categories"></a>프로젝트 범주

범주는 프로젝트에 대해 사용자가 사용하려는 용어 및 스타일의 영역인 도메인을 식별합니다. 사용자 문서와 가장 관련성이 높은 범주를 선택합니다. 경우에 따라 선택한 범주는 Custom Translator의 동작에 직접 영향을 줍니다.

두 세트의 기준 모델이 있습니다. 일반 및 기술입니다. 범주 **기술**을 선택한 경우 기술 기준 모델이 사용됩니다. 다른 범주 선택의 경우 일반 기준 모델이 사용됩니다. 기술 기준 모델은 기술 도메인에서도 잘 작동하지만 변환에 사용되는 문장이 기술 도메인 내에 속하지 않는 경우 낮은 품질을 보여줍니다. 문장이 기술 도메인 내에 엄격하게 속하는 경우 기술 범주를 선택하는 것이 좋습니다.

동일한 작업 영역에서 다양한 범주에 있는 동일한 언어 쌍에 대한 프로젝트를 만들 수도 있습니다. Custom Translator는 동일한 언어 쌍 및 범주를 사용하여 중복된 프로젝트의 생성을 방지합니다. 프로젝트에 레이블을 적용하면 이 제한 사항을 피할 수 있습니다. 프로젝트에 고유한 레이블을 추가하면 프로젝트 CategoryID에 반영되므로 여러 클라이언트에 대한 번역 시스템을 빌드하지 않는 한 레이블을 사용하지 마십시오.

## <a name="project-labels"></a>프로젝트 레이블

Custom Translator를 사용하면 프로젝트 레이블을 프로젝트에 할당할 수 있습니다. 프로젝트 레이블은 동일한 언어 쌍 및 범주를 사용하여 여러 프로젝트를 구분합니다. 가장 좋은 방법은 필요한 경우 외에는 프로젝트 레이블을 사용하지 않는 것입니다.

프로젝트 레이블은 CategoryID의 일부로 사용됩니다. 프로젝트 레이블이 설정되지 않은 상태이거나 프로젝트 간에 동일하게 설정된 경우에는 동일한 범주 및 *다른* 언어 쌍의 프로젝트는 동일한 CategoryID를 공유하게 됩니다. 이 방식의 장점은 사용자나 고객이 CategoryID가 각 프로젝트에 고유한지 걱정할 필요 없이 Text Translator API를 사용할 때 언어 간 전환이 가능하다는 점입니다.

예를 들어, 영어-프랑스어 또는 프랑스어-영어로 기술 도메인을 번역하도록 설정하려는 경우 영어 \> 프랑스어용 프로젝트와 프랑스어 -\> 영어용 프로젝트 이렇게 두 개의 프로젝트를 만듭니다. 두 프로젝트 모두에 동일한 범주(기술)를 지정하고 프로젝트 레이블은 비워 둡니다. 두 프로젝트에 대한 CategoryID는 일치하므로 내 CategoryID를 수정하지 않고도 영어와 프랑스어 번역 모두에 대해 API를 쿼리할 수 있습니다.

동일한 범주 및 언어 쌍을 유지하는 다양한 모델을 사용하여 여러 고객에게 제공하려는 언어 서비스 공급자라면 프로젝트 레이블을 사용하여 고객들을 구분하는 것이 현명한 의사 결정일 것입니다.

## <a name="next-steps"></a>다음 단계

- 효율적으로 번역 모델을 빌드하는 방법을 알아보려면 [학습 및 모델](training-and-model.md)을 읽어보세요.
