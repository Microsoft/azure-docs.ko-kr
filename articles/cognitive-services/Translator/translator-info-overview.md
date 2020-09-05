---
title: Translator란? - Translator
titlesuffix: Azure Cognitive Services
description: Translator를 애플리케이션, 웹 사이트, 도구 및 기타 솔루션에 통합하여 다국어 사용자 환경을 제공합니다.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: overview
ms.date: 05/26/2020
ms.author: swmachan
ms.custom: seodec18
ms.openlocfilehash: 5da9e5b38b88a5a148bd3d85664daa523153086b
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589454"
---
# <a name="what-is-the-translator"></a>Translator란?

Translator는 애플리케이션, 웹 사이트, 도구 및 솔루션에 쉽게 통합할 수 있습니다. [70개국 초과 언어](languages.md)로 제공되는 다국어 사용자 환경을 추가할 수 있게 해주며, 텍스트 간 언어 번역에 사용되는 운영 체제가 설치된 모든 하드웨어 플랫폼에서 사용할 수 있습니다.

Translator는 클라우드의 AI 알고리즘 및 기계 학습의 [Azure Cognitive Services](https://docs.microsoft.com/azure/?pivot=products&panel=ai) 컬렉션에 포함되며, 사용자 개발 프로젝트에서 쉽게 사용할 수 있습니다.

## <a name="about-microsoft-translator"></a>Microsoft Translator 정보

Translator는 클라우드 기반 기계 번역 서비스입니다. 핵심 서비스는 다양한 Microsoft 제품 및 서비스를 제공하는 Translator이며, 전 세계 수천 개 기업에서 전 세계 잠재 고객에게 콘텐츠가 도달할 수 있도록 애플리케이션 및 워크플로에 사용하고 있습니다.

Translator에서 제공하는 음성 번역도 [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/)를 통해 사용할 수 있습니다. 이는 Translator Speech API 및 Custom Speech Service의 기능을 완전히 사용자 지정 가능한 통합 서비스에 결합합니다. Speech Service가 Translator Speech API를 대체함에 따라, Translator Speech API는 2019년 10월 15일부로 사용이 중단됩니다.

## <a name="language-support"></a>언어 지원

Microsoft Translator는 번역, 음차, 언어 감지 및 사전에 다국어 지원을 제공합니다. [언어 지원](language-support.md)에서 전체 목록을 참조하거나 [REST API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-languages)를 사용하여 목록에 프로그래매틱 방식으로 액세스하세요.  

## <a name="microsoft-translator-neural-machine-translation"></a>Microsoft Translator 신경망 기계 번역

NMT(신경망 기계 번역)는 고품질 AI 기반 기계 번역에 대한 새로운 표준으로, 2010년 중반에 안정적인 품질 수준에 도달한 레거시 SMT(통계 기계 번역) 기술을 대체합니다.

NMT는 원시 번역 품질 채점 관점에서 SMT보다 더 나은 번역을 제공할 뿐만 아니라 더 유창하고 인간적으로 들릴 수도 있습니다. 이렇게 유연한 번역이 제공될 수 있는 핵심 이유는 NMT가 단어를 번역하는 데 문장의 전체 컨텍스트를 사용한다는 것입니다. SMT는 각 단어 앞뒤의 몇 개 단어로 된 컨텍스트만 사용합니다.

NMT 모델은 API의 핵심이며, 최종 사용자에게는 보이지 않습니다. 눈에 띄는 유일한 차이점은 특히 중국어, 일본어 및 아랍어와 같은 언어에 대한 번역 품질이 향상되었다는 것입니다.

[NMT 작동 방법](https://www.microsoft.com/en-us/translator/mt.aspx#nnt)에 대해 자세히 알아보기

## <a name="language-customization"></a>언어 사용자 지정

핵심 Microsoft Translator 서비스의 확장인 Custom Translator를 Translator와 함께 사용하여 인공 신경망 번역 시스템을 사용자 지정하고 특정 용어 및 스타일에 맞게 번역을 향상시킬 수 있습니다.

Custom Translator를 사용하면 고유한 비즈니스 및 업계에서 사용되는 용어를 처리하는 번역 시스템을 구축할 수 있습니다. 사용자 지정 번역 시스템은 범주 매개 변수를 사용하여 일반 Translator를 통해 여러 유형의 디바이스에서 기존 애플리케이션, 워크플로 및 웹 사이트에 쉽게 통합됩니다.

[언어 사용자 지정](customization.md)에 대한 자세한 정보

## <a name="next-steps"></a>다음 단계

- 액세스 키를 [등록](translator-text-how-to-signup.md)합니다.
- [API 참조](https://docs.microsoft.com/azure/cognitive-services/Translator/reference/v3-0-reference)는 API에 대한 기술 설명서를 제공합니다.
- [가격 정보](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
