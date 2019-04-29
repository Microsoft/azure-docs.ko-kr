---
title: Azure Security Center에서 인터넷 엔드포인트를 통한 액세스 제한 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 권장 사항 **인터넷 엔드포인트를 통한 액세스 제한**을 구현하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 727d88c9-163b-4ea0-a4ce-3be43686599f
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: b736bb5549b7d236e746ba7b161cde79209e927b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60906582"
---
# <a name="restrict-access-through-internet-facing-endpoints-in-azure-security-center"></a>Azure Security Center에서 인터넷 엔드포인트를 통한 액세스 제한
Azure Security Center는 네트워크 보안 그룹(NSG)에 "임의" 원본 IP 주소에서 액세스할 수 있도록 하는 인바운드 규칙이 하나 이상 있는 경우 인터넷 엔드포인트를 통한 액세스를 제한할 것을 권장 합니다. 공격자가 "임의"에 대한 열기 액세스를 사용하여 리소스에 액세스할 수 있습니다. 보안 센터는 실제로 액세스가 필요한 원본 IP 주소에 대한 액세스를 제한하도록 이러한 인바운드 규칙을 편집할 것을 권장합니다.

이 권장 사항은 "어떤 항목도" 원본으로 사용할 수 있는 웹이 아닌 모든 포트에 대해 생성됩니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다. 단계별 가이드는 아닙니다.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현
1. **권장 사항 블레이드**에서 **인터넷 엔드포인트를 통한 액세스 제한**을 선택합니다.

   ![인터넷 엔드포인트를 통한 액세스 제한][1]
2. **인터넷 엔드포인트를 통한 액세스 제한**블레이드가 열립니다. 이 블레이드에 잠재적인 보안 문제를 발생시키는 인바운드 규칙이 있는 가상 머신(VM)를 나열합니다. VM을 선택합니다.

   ![VM 선택][2]
3. **NSG** 블레이드는 네트워크 보안 그룹 정보, 관련 인바운드 규칙 및 연결된 VM을 표시합니다. **인바운드 규칙 편집** 을 선택하여 인바운드 규칙 편집을 계속 진행합니다.

   ![네트워크 보안 그룹 블레이드][3]
4. **인바운드 보안 규칙** 블레이드에서 편집할 인바운드 규칙을 선택합니다. 이 예제에서는 **AllowWeb**을 선택합니다.

   ![인바운드 보안 규칙][4]

   참고로 **기본 규칙** 을 선택하여 모든 NSG에 의해 포함된 기본 규칙 집합을 확인할 수도 있습니다. 기본 규칙은 삭제할 수 없지만, 가장 낮은 우선순위가 할당되기 때문에 직접 만든 규칙으로 재정의할 수 있습니다. [규칙 기본](../virtual-network/security-overview.md#default-security-rules)에 대해 자세히 알아보세요.

   ![기본 규칙][5]
5. **AllowWeb** 블레이드에서 인바운드 규칙의 속성을 **원본** IP 주소가 IP 주소 또는 IP 주소 블록이 되도록 편집합니다. 인바운드 규칙의 속성에 대해 자세히 알아보려면 [NSG 규칙](../virtual-network/security-overview.md#security-rules)을 확인합니다.

   ![인바운드 규칙 편집][6]

## <a name="see-also"></a>참고 항목
이 문서에서는 보안 센터 권장 사항 "인터넷 엔드포인트를 통한 액세스 제한"을 구현하는 방법을 보여 주었습니다. NSG 및 규칙 활성화에 대한 자세한 내용은 다음을 참조하세요.

* [NSG(네트워크 보안 그룹)란?](../virtual-network/security-overview.md)
* [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md)-- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md)-- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md)--Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md)--보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md)--서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/)-- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/restrict-access-thru-internet-facing-endpoint.png
[2]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/select-a-vm.png
[3]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/network-security-group-blade.png
[4]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/inbound-security-rules.png
[5]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/default-rules.png
[6]: ./media/security-center-restrict-access-thru-internet-facing-endpoint/edit-inbound-rule.png
