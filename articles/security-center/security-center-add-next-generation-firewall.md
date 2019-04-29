---
title: Azure Security Center에서 차세대 방화벽 추가 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 권장 사항 **차세대 방화벽 추가** 및 **NGFW를 통해서만 트래픽 라우팅**을 구현하는 방법을 보여줍니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 48b99015-4db8-4ce8-85e4-b544c0fa203e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 731102037b596091b80fbdfa02a8ff3c111b556e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707077"
---
# <a name="add-a-next-generation-firewall-in-azure-security-center"></a>Azure Security Center에서 차세대 방화벽 추가
Azure Security Center에서는 보안 보호를 증가시키기 위해 Microsoft 파트너의 차세대 방화벽(NGFW)을 추가하도록 권장할 수 있습니다. 이 문서에서는 이를 수행하는 방법의 예를 보여 줍니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  단계별 가이드는 아닙니다.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현
1. **권장 사항** 블레이드에서 **차세대 방화벽 추가**를 선택합니다.
   ![차세대 방화벽 추가][1]
2. **차세대 방화벽 추가** 블레이드에서 엔드포인트를 선택합니다.
   ![엔드포인트 선택][2]
3. 두 번째 **차세대 방화벽 추가** 블레이드가 열립니다. 새 솔루션을 사용할 수 있거나 만들 수 있는 경우 기존 솔루션을 사용하도록 선택할 수 있습니다. 이 예제에서는 사용할 수 있는 기존 솔루션이 없으므로 NGFW를 만들어 보겠습니다.
   ![차세대 방화벽 만들기][3]
4. NGFW를 만들려면 통합 파트너 목록에서 솔루션을 선택합니다. 이 예제에서는 **검사점**을 선택합니다.
   ![차세대 방화벽 솔루션 선택][4]
5. 파트너 솔루션에 대한 정보를 제공하는 **검사점** 블레이드가 열립니다. 정보 블레이드에서 **만들기** 를 선택합니다.
   ![방화벽 정보 블레이드][5]
6. **가상 머신 만들기** 블레이드가 열립니다. 여기서 NGFW를 실행하는 VM(가상 머신)을 스핀업하는 데 필요한 정보를 입력할 수 있습니다. 단계를 수행하고 필요한 NGFW 정보를 제공합니다. 적용하려면 확인을 선택합니다.
   ![NGFW를 실행하기 위해 가상 머신 만들기][6]

## <a name="route-traffic-through-ngfw-only"></a>NGFW를 통해서만 트래픽 라우팅
**권장 사항** 블레이드로 돌아갑니다. Security Center를 통해 NGFW를 추가한 후 **NGFW를 통해서만 트래픽 라우팅**이라는 새 항목이 생성되었습니다. 이 권장 사항은 보안 센터를 통해 NGFW를 설치한 경우에만 생성됩니다. 인터넷 엔드포인트가 있는 경우 Security Center는 NGFW를 통해 인바운드 트래픽을 VM으로 강제하는 네트워크 보안 그룹 규칙을 구성할 것을 권장합니다.

1. **권장 사항 블레이드**에서 **NGFW를 통해서만 트래픽 라우팅**을 선택합니다.
   ![NGFW를 통해서만 트래픽 라우팅][7]
2. 트래픽을 라우팅할 수 있는 VM을 나열하는 **NGFW를 통해서만 트래픽 라우팅** 블레이드가 열립니다. 목록에서 VM을 선택합니다.
   ![VM 선택][8]
3. 선택된 VM에 대한 블레이드가 열리고 관련 인바운드 규칙을 표시합니다. 설명은 가능한 다음 단계에 대한 자세한 내용을 제공합니다. **인바운드 규칙 편집** 을 선택하여 인바운드 규칙 편집을 계속 진행합니다. NGFW로 연결된 인터넷 연결 엔드포인트의 경우 **원본**이 **모두**로 설정되지 않아야 합니다. 인바운드 규칙의 속성에 대한 자세한 내용은 [보안 규칙](../virtual-network/security-overview.md#security-rules)을 참조하세요.
   ![액세스를 제한하는 규칙 구성][9]
   ![인바운드 규칙 편집][10]

## <a name="see-also"></a>참고 항목
이 문서에서는 보안 센터 권장 사항 "차세대 방화벽 추가"를 구현하는 방법을 보여 주었습니다. NGFW 및 검사점 파트너 솔루션에 대해 자세히 알아보려면 다음을 참조하세요.

* [차세대 방화벽](https://en.wikipedia.org/wiki/Next-Generation_Firewall)
* [검사점 vSEC](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/checkpoint.vsec)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -- 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) –- Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) -- Azure 보안 및 규정 준수에 관한 블로그 게시물을 찾습니다.

<!--Image references-->
[1]: ./media/security-center-add-next-gen-firewall/add-next-gen-firewall.png
[2]: ./media/security-center-add-next-gen-firewall/select-an-endpoint.png
[3]: ./media/security-center-add-next-gen-firewall/create-new-next-gen-firewall.png
[4]: ./media/security-center-add-next-gen-firewall/select-next-gen-firewall.png
[5]: ./media/security-center-add-next-gen-firewall/firewall-solution-info-blade.png
[6]: ./media/security-center-add-next-gen-firewall/create-virtual-machine.png
[7]: ./media/security-center-add-next-gen-firewall/route-traffic-through-ngfw.png
[8]: ./media/security-center-add-next-gen-firewall/select-vm.png
[9]: ./media/security-center-add-next-gen-firewall/configure-rules-to-limit-access.png
[10]: ./media/security-center-add-next-gen-firewall/edit-inbound-rule.png
