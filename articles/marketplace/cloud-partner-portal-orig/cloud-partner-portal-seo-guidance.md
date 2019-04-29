---
title: Azure Marketplace SEO 지침 | Microsoft Docs
description: SEO(검색 엔진 최적화)를 극대화하기 위한 지침을 제공합니다.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: pbutlerm
ms.openlocfilehash: da7b59400baa4c964dff71fa1f842fede9d68df1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776797"
---
# <a name="azure-marketplace-seo-guidance"></a>Azure Marketplace SEO 지침

이 문서에서 검색 기능을 통해 제품의 검색 기능을 최대화 하는 방법에 설명 합니다 [Azure Marketplace](https://azuremarketplace.microsoft.com) 하 고 [AppSource](https://appsource.microsoft.com)합니다. 


## <a name="general-explanation-of-algorithm"></a>알고리즘에 대한 일반적인 설명

Microsoft 마켓플레이스 사이트의 검색 기능을 강화 하는 것에 대 한 Azure Search를 활용 합니다. 이 알고리즘은 용어 [TF-IDF](https://en.wikipedia.org/wiki/Tf–idf)(용어 빈도/역 문서 빈도)를 기준으로 합니다. [표준](https://lucene.apache.org/core/) Lucene 분석기가 사용됩니다.

일반적으로 모든 텍스트 필드, 범주 및 산업이 관련성의 가중치에 포함됩니다. 다른 앱과 달리 사용자 앱에서만 자주 사용되는 특수한 용어는 검색을 수행할 때 훨씬 더 높은 일치 점수를 생성합니다. 따라서 “Azure search”는 훨씬 더 전문적이지만 “VM”과 같은 용어를 포함하면 별로 이점이 없습니다.
다음은 고려해야 할 가장 관련성이 높은 필드입니다.

 
|  필드                   | 중요도 | 지침                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| 제품 이름               |  높음      | 검색 쿼리와 정확히 일치하거나 완전 일치에 가까우면 높은 순위를 얻습니다.                       |
| 게시자 이름           |  높음      | 검색 쿼리와 정확히 일치하거나 완전 일치에 가까우면 높은 순위를 얻습니다.                       |
| 간단한 설명        |  중간    | 앱 및 게시자의 이름을 지정하면 거의 높은 순위에 오를 확률이 높습니다. 관련성이 가장 높지는 않을 수 있습니다. 이 경우에는 간단한 설명이 중요합니다. 간결하고 핵심적인 텍스트를 사용합니다. 최상의 결과를 얻기 위해 키워드 및 예상 검색어를 포함해야 합니다.  예를 들어 "This is the best Retail POS built fully on top of Dynamics 365"가 "Retail POS (point of sale) for Dynamics 365"보다 덜 효과적입니다.  | 
| 자세한 설명         |  낮음       | 설명은 좀 더 심층적으로 이동하는 방법을 제공합니다. 가장 효과적인 설명은 적절한 길이를 가지며 키워드를 포함합니다.  키워드를 사용한 간단 명료한 설명이 길고 장황한 텍스트보다 도움이 됩니다. "IoT"와 같은 핵심 용어를 설명에 포함해야 합니다.  |
| 제품 범주       | 중간     |  제품 범주는 게시자 선택 사항과 Microsoft의 조합으로 결정됩니다. 사용자가 올바른 범주에서 앱을 쉽게 찾을 수 있도록 이러한 범주를 적절히 선택합니다. |
|  |  |  |


## <a name="other-tips"></a>기타 팁

-   검색을 하게 되면 과도한 사용자 활동이 수반됩니다. 앱 이름/게시자와 일치하는지 우선적으로 확인합니다. 검색 용어가 게시자/앱 이름과 정확히 일치하지 않으면 짧은 설명이 핵심 필드가 됩니다.
-   다운로드용 문서는 검색 가중치에 포함되지 않습니다.
-   앱의 실제 획득 및 사용도 검색 순위에 영향을 미칩니다. 예를 들어, 두 개의 동급 앱 중에서 하나의 앱이 훨씬 더 많은 사용자로부터 높은 순위로 지정될 수 있습니다.
