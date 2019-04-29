---
title: Azure Security Center의 신뢰도 점수 | Microsoft Docs
description: " Azure Security Center 신뢰도 점수를 사용하는 방법을 알아봅니다. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: 64bab5c1b99720eecb189834d7a11802cf919ca3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60916595"
---
# <a name="alert-confidence-score"></a>경고 신뢰도 점수 

Azure Security Center는 Azure에서 실행하는 리소스 전반에 대한 가시성을 제공하고 잠재적인 문제를 발견하면 알려줍니다. 보안 운영 팀이 개별적으로 해결하기에 경고의 양이 너무 많을 수 있으며 조사할 경고의 우선 순위를 정하는 작업이 필요할 수 있습니다. 경고를 조사하는 작업은 복잡하고 시간이 오래 걸릴 수 있기 때문에 일부 경고는 무시됩니다.

Security Center의 신뢰도 점수를 사용하면 팀에서 경고를 심사하여 우선 순위를 정할 수 있습니다. Security Center는 분석가가 사용하는 업계 모범 사례, 지능형 알고리즘 및 프로세스를 적용하여 위협이 합법적인지 여부를 판단하고 신뢰도 점수의 형태로 유용한 인사이트를 제공합니다.

## <a name="how-the-confidence-score-is-triggered"></a>신뢰도 점수가 트리거되는 방법

경고는 가상 머신에서 의심스러운 프로세스가 검색되면 생성됩니다. Security Center는 Azure에서 실행되는 Windows 가상 머신에서 이러한 경고를 검토하고 분석합니다. 그리고 조직 전반의 여러 엔터티와 데이터 원본 및 모든 Azure 리소스에 대한 고급 알고리즘을 사용하여 자동화된 검사 및 상관 관계 분석을 수행하여 신뢰도 점수를 제공합니다. 신뢰도 점수는 경고가 실제 상황이므로 조사해야 함을 Security Center가 신뢰하는 정도를 측정한 값입니다.

## <a name="understanding-the-confidence-score"></a>신뢰도 점수 이해

신뢰도 점수의 범위는 1에서 100 사이이며 Security Center가 해당 경고를 조사해야 한다고 확신하는 정도를 나타냅니다. 점수가 높을수록 경고가 실제 악성 활동을 나타낸다는 Security Center의 확신이 높다는 것을 나타냅니다. 신뢰도 점수에는 해당 경고가 신뢰도 점수를 받은 주요 이유 목록이 포함됩니다. 신뢰도 점수가 있으면 보안 분석가가 경고에 대한 대응의 우선 순위를 정하여 가장 긴급한 공격을 먼저 처리할 수 있기 때문에 궁극적으로 공격 및 위반에 대응하는 데 걸리는 시간이 줄어듭니다.

신뢰도 점수를 보려면:
- Security Center 포털에서 보안 경고 블레이드를 엽니다.
-  경고와 인시던트는 가장 높은 것부터 가장 낮은 것으로 정리됩니다. 즉, 경고가 위협이 된다는 Security Center의 확신이 높을수록 페이지 위 쪽에 가깝게 표시됩니다. 


 ![신뢰도 점수][1]

경고에 대한 보안 센터의 확신에 영향을 준 데이터를 보려면:
- 보안 경고 블레이드의 **신뢰**에서 신뢰로 점수에 영향을 준 관찰 내용을 확인하고 경보와 관련된 인사이트를 얻으십시오. 이렇게 하면 경고를 유발한 활동의 본질에 대해 더 많은 인사이트를 얻을 수 있습니다.

  ![의심스러운 신뢰도 점수][2]

Security Center의 신뢰도 점수를 사용하여 환경에서 경고 심사의 우선 순위를 정합니다. 신뢰도 점수가 있으면 자동으로 경고를 조사하고, 업계 모범 사례와 지능형 알고리즘을 적용하고, 가상의 분석가 역할을 수행하여 어떤 위협이 실제인지, 어디에 관심을 집중해야 하는지 판단해 주기 때문에 시간과 노력이 절약됩니다.


## <a name="next-steps"></a>다음 단계
이 문서에서는 신뢰도 점수를 사용하여 경고 조사의 우선 순위를 정하는 방법을 설명했습니다. 보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
