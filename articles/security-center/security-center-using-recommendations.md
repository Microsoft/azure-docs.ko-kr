---
title: Azure Security Center 권장 사항을 사용하여 보안 강화 | Microsoft Docs
description: " Azure Security Center에서 보안 정책 및 권장 사항을 사용하여 보안 공격을 완화할 수 있는 방법을 알아봅니다. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/26/2019
ms.author: monhaber
ms.openlocfilehash: 82a46ae9523c4c2778f86c445e35d0bec961307f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60703463"
---
# <a name="use-azure-security-center-recommendations-to-enhance-security"></a>Azure Security Center 권장 사항을 사용하여 보안 강화
보안 정책을 구성한 다음 Azure Security Center에서 제공한 권장 사항을 구현하여 중요한 보안 이벤트의 가능성을 줄일 수 있습니다. 이 문서에서는 Security Center에서 보안 정책 및 권장 사항을 사용하여 보안 공격을 완화할 수 있는 방법을 보여 줍니다. 

Security Center는 자동으로 Azure 리소스의 보안 상태를 분석 하는 연속 검색을 실행 합니다. Security Center는 잠재적 보안 취약성이 확인되면 필요한 보안 컨트롤을 구성하는 과정을 안내하는 권장 사항을 만듭니다. Security Center는 다음과 같은 예외를 사용 하 여 24 시간 이내 해당 권장 구성을 업데이트합니다.

- 운영 체제 보안 구성 권장 사항은 48 시간 이내 업데이트 됩니다.
- Endpoint Protection 문제 권장 사항은 8 시간 이내 업데이트 됩니다.

## <a name="scenario"></a>시나리오
이 시나리오에서는 Security Center 권장 사항을 모니터링하고 조치를 취하여 보안 인시던트가 발생할 가능성을 줄일 수 있도록 Security Center를 사용하는 방법을 보여 줍니다. 시나리오는 Security Center [계획 및 운영 가이드](security-center-planning-and-operations-guide.md#security-roles-and-access-controls)에서 제공된 가상의 회사, Contoso 및 역할을 사용합니다. 이 시나리오에서는 다음 가상 사용자의 역할을 중점적으로 살펴봅니다.

![시나리오 역할](./media/security-center-using-recommendations/scenario-roles.png)

Contoso는 최근 해당 온-프레미스 리소스의 일부를 Azure로 마이그레이션했습니다. Contoso는 클라우드에서 리소스를 보호하고 해당 리소스의 취약성을 줄여줍니다.

## <a name="use-azure-security-center"></a>Azure Security Center 사용
Contoso의 IT 보안 부서에서 일하는 David은 보안 취약성을 감지하고 방지하기 위해 이미 Azure Security Center의 Contoso의 구독에서 Security Center를 온보딩하도록 선택했습니다. 

Security Center는 Contoso Azure 리소스의 보안 상태를 자동으로 분석하고 기본 보안 정책을 적용합니다. Security Center가 잠재적인 보안 취약점을 식별하는 경우 보안 정책에서 설정된 컨트롤에 따라 **권장 사항**을 만듭니다. 

David은 전체 권장 사항 및 사용할 수 있는 보안 기능을 가져오기 위해 모든 구독에서 Azure 보안 표준 계층을 실행합니다. Jeff 또한 [Windows](quick-onboard-windows-computer.md) 및 [Linux](quick-onboard-linux-computer.md) 서버에서 Security Center의 하이브리드 지원을 활용할 수 있도록 클라우드로 아직 마이그레이션하지 않은 기존의 모든 온-프레미스 서버를 온보딩합니다.

Jeff는 클라우드 워크로드 소유자입니다. Jeff는 Contoso의 보안 정책에 따라 보안 제어를 적용하는 일을 담당합니다. 

Jeff는 다음 작업을 수행합니다.

- Security Center에서 제공한 보안 권장 사항 모니터링
- 보안 권장 사항을 평가하고 적용하거나 해제해야 할지를 결정합니다.
- 보안 권장 사항 적용

### <a name="remediate-threats-using-recommendations"></a>권장 사항을 사용하여 위협 해결
Jeff는 일상적인 모니터링 작업의 일부로, Azure에 로그인하고 Security Center를 엽니다. 

1. Jeff는 워크로드의 구독을 선택합니다.

2. Jeff는 **보안 점수**를 확인하여 구독의 안전 상태를 전반적으로 파악하고 점수가 548이라는 것을 알았습니다.

3. Jeff는 먼저 처리할 권장 사항을 결정해야 합니다. 따라서 Jeff는 보안 점수를 클릭하여 [보안 점수 영향](security-center-secure-score.md)의 개선 정도에 따라 권장 사항을 처리하기 시작합니다.

4. Jeff에 많은 VM 및 서버에 연결되어 있으므로 **Compute 및 앱**에 집중하기로 결정했습니다.

5. Jeff는 **Cmpute 및 앱**을 클릭하여 표시되는 권장 사항 목록을 보면서 보안 점수 영향에 따라 권장 사항을 처리합니다.

6. Jeff는 다양한 인터넷 기반 VM을 보유하고 있으며, 해당 포트가 노출되어 있으므로 공격자가 서버를 제어하게 될지도 몰라 걱정이 됩니다. 따라서 Jeff는 (**Just In Time VM 액세스**)[security-center-just-in-time.md]를 사용하기로 선택했습니다.

Jeff는 계속해서 높은 우선 순위 및 중간 우선 순위 권장 사항을 살펴보면서 구현할지 결정했습니다. 각 권장 사항에 대해 Jeff는 Security Center에서 제공한 세부 정보를 확인하여 영향을 받은 리소스, 보안 점수 영향, 각 권장 사항의 의미와 각 문제를 마이그레이션하는 방법에 대한 수정 단계를 이해합니다.

## <a name="conclusion"></a>결론
Security Center에서 권장 사항을 모니터링하면 공격이 발생하기 전에 보안 취약점을 제거할 수 있습니다. 권장 사항을 수정하는 경우 보안 점수 및 워크로드의 보안 태세가 개선됩니다. Security Center는 사용자가 배포한 새 리소스를 자동으로 검색하고, 보안 정책에 따라 평가하고, 보안을 유지하기 위한 새 권장 사항을 제공합니다.


## <a name="next-steps"></a>다음 단계
모니터링 프로세스가 제대로 설정되었는지 확인하고, 계속해서 리소스를 안전하게 유지할 수 있도록 Security Center에서 권장 사항을 정기적으로 확인해야 합니다.

이 시나리오에서는 Security Center에서 보안 정책 및 권장 사항을 사용하여 보안 공격을 완화할 수 있는 방법을 보여 줍니다. [사고 대응 시나리오](security-center-incident-response.md)에서 공격이 발생하기 전에 사고 대응 계획을 적절히 수립하는 방법을 참조하세요.

[인시던트 응답](security-center-incident-response.md)을 사용하여 위협에 대응하는 방법을 알아봅니다.
