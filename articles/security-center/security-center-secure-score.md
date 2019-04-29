---
title: Azure Security Center의 보안 점수 | Microsoft Docs
description: " Azure Security Center에서 보안 점수를 사용하여 보안 권장 사항의 우선 순위를 지정하세요. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: monhaber
ms.openlocfilehash: 22791fc43ff17d56e1f51e7f7737a10109f47c59
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906232"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Azure Security Center의 보안 점수 개선


보안 혜택을 제공하는 서비스는 매우 많으므로 워크로드를 보호하고 강화하기 위해 먼저 수행할 단계를 아는 것은 종종 어려운 일입니다. Azure 보안 점수는 사용자의 보안 권장 사항을 검토하고 순위를 지정하므로 사용자는 먼저 수행할 권장 사항을 파악할 수 있습니다. 이를 통해 가장 심각한 보안 취약점을 찾아내 조사의 순위를 지정할 수 있습니다. 보안 점수는 워크로드 보안에 대한 사용자의 대비 상태를 평가할 수 있는 도구입니다.

## <a name="secure-score-calculation"></a>보안 점수 계산

Security Center는 보안 권장 사항을 검토하고 고급 알고리즘을 적용하여 각 권장 사항의 중요도를 판별하는 보안 분석가의 작업을 모방합니다.
Azure Security Center에서는 지속적으로 활성 권장 사항을 검토하고 이를 기반으로 보안 점수를 계산하며, 권장 사항의 점수는 워크로드 보안에 가장 많은 영향을 주는 심각도 및 보안 모범 사례에서 파생됩니다.

Security Center에서는 **전체 보안 점수**도 제공합니다. 

**전체 보안 점수**는 모든 권장 사항의 누적 점수입니다. 사용자가 선택하는 내용에 따라 구독 또는 관리 그룹의 전체 보안 점수를 볼 수 있습니다. 점수는 선택되어 있는 구독과 이 구독에 대한 활성 권장 사항에 따라 달라집니다.

 
보안 점수에 가장 많이 영향을 주는 권장 사항을 확인하기 위해 Security Center 대시보드에서 가장 영향력이 있는 최상위 3개의 권장 사항을 보거나 **보안 점수 영향** 열을 사용하여 권장 사항 목록 블레이드에서 권장 사항을 정렬할 수도 있습니다.


전체 보안 점수를 보려면

1. Azure 대시보드에서 **Security Center**, **보안 점수**를 차례로 클릭합니다.
2. 화면 위쪽에 보안 점수 관련 주요 정보가 표시됩니다.
   - **전체 보안 점수**는 선택한 구독별 정책당 점수를 나타냅니다.
   - **범주별 보안 점수**에는 가장 시급하게 확인해야 하는 리소스가 표시됩니다.
   - **보안 점수별 상위 권장 사항**에서는 구현 시 보안 점수를 가장 많이 높일 수 있는 권장 사항 목록이 제공됩니다.
 
   ![보안 점수](./media/security-center-secure-score/secure-score-dashboard.png)

3. 점수 아래쪽의 표에서는 각 구독과 구독별 전체 보안 점수를 확인할 수 있습니다.

   > [!NOTE]
   > 각 구독의 보안 점수 합이 전체 보안 점수와 같지는 않습니다. 보안 점수는 권장 사항별 정상 리소스와 총 리소스의 비율을 기준으로 계산되므로 전체 구독의 보안 점수를 합한 값이 아닙니다. 
   >
4. **권장 사항 보기**를 클릭하면 해당 구독에서 보안 점수를 높이기 위해 교정할 수 있는 권장 사항을 확인할 수 있습니다.
4. 권장 사항 목록에서 각 권장 사항에는 **보안 점수 영향**을 나타내는 열이 있습니다. 이 숫자는 권장 사항을 따르는 경우 전체 보안 점수가 향상되는 정도를 나타냅니다. 예를 들어, 아래 화면에서 **컨테이너 보안 구성에서 취약성 수정**을 수행하는 경우 보안 점수가 35포인트만큼 증가합니다.

   ![보안 점수](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>개별 보안 점수

또한 개별적인 보안 점수를 확인하기 위해 개별 권장 사항 블레이드 내에서 이러한 점수를 찾을 수 있습니다.  

**권장 사항 보안 점수**는 정상 리소스와 총 리소스 간의 비율을 기반으로 하는 계산입니다. 정상 상태의 리소스 수가 총 리소스 수와 같은 경우 최대 50점의 권장 사항 보안 점수를 받습니다. 최대 점수에 근접한 보안 점수를 받으려면 권장 사항을 수행하여 비정상 리소스를 수정합니다.

**권장 사항 영향**을 통해 권장 사항 절차를 적용할 경우 보안 점수가 향상되는 정도를 알 수 있습니다. 예를 들어 보안 점수가 42이고 **권장 사항 영향**이 +3인 경우, 권장 사항에 요약된 절차를 수행하면 보안 점수는 45로 향상됩니다.

권장 사항은 수정 절차가 수행되지 않는 경우 워크로드가 노출되는 위협을 보여 줍니다.

![개별 권장 사항 보안 점수](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure Security Center의 **보안 점수**를 사용하여 보안 태세를 개선하는 방법을 설명했습니다. Security Center에 대해 자세히 알아보려면 다음 항목을 참조하세요.

* [Azure Security Center FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.


