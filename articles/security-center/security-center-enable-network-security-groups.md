---
title: Azure Security Center의 네트워크 보안 그룹 활성화 | Microsoft Docs
description: 이 문서에서는 Azure Security Center 권장 사항 **네트워크 보안 그룹 활성화**를 구현하는 방법을 보여 줍니다.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f53ed853-ffaf-4530-a019-1906ba6f341b
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2018
ms.author: rkarlin
ms.openlocfilehash: 14b7cc8f8162574380ca21ac515af8b7d3d5ded9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60911448"
---
# <a name="enable-network-security-groups-in-azure-security-center"></a>Azure Security Center의 네트워크 보안 그룹 활성화
Azure Security Center는 아직 활성화되지 않은 경우 NSG(네트워크 보안 그룹)를 활성화하는 것을 권장합니다. NSG는 ACL(액세스 제어 목록)의 Virtual Network에 VM 인스턴스에 대한 허용 또는 거부 네트워크 트래픽 규칙의 목록을 포함합니다. Nsg는 서브넷 또는 서브넷 내의 개별 VM 인스턴스 중 하나와 연결될 수 있습니다. NSG를 서브넷과 연결한 경우 ACL 규칙은 해당 서브넷에 있는 모든 VM 인스턴스에 적용됩니다. 또한 개별 VM에 대한 트래픽은 해당 VM에 직접 NSG를 연결하여 추가로 제한할 수 있습니다. 자세한 내용은 [NSG(네트워크 보안 그룹)란?](../virtual-network/security-overview.md)

NSG를 활성화하지 않은 경우 Security Center는 두 가지 권장 사항으로 서브넷에서 네트워크 보안 그룹 활성화 및 가상 머신에서 네트워크 보안 그룹 활성화를 제시합니다. NSG를 적용할 수준, 서브넷 또는 VM을 선택합니다.

> [!NOTE]
> 이 문서에서는 배포 예제를 사용하여 서비스를 소개합니다.  단계별 가이드는 아닙니다.
>
>

## <a name="implement-the-recommendation"></a>권장 사항 구현
1. **권장 사항** 블레이드의 서브넷 또는 가상 머신에서 **네트워크 보안 그룹 활성화**를 선택합니다.
   ![네트워크 보안 그룹 활성화][1]
2. 이렇게 하면 선택한 권장 사항에 따라 서브넷 또는 가상 머신에 대한 **누락된 네트워크 보안 그룹 구성** 블레이드가 열립니다. NSG를 구성할 서브넷 또는 가상 컴퓨터를 선택합니다.

   ![서브넷에 대한 NSG 구성][2]

   ![VM에 대한 NSG 구성][3]
3. **네트워크 보안 그룹 선택** 블레이드에서 기존 NSG를 선택하거나 **새로 만들기**를 선택하여 NSG를 만듭니다.

   ![네트워크 보안 그룹 선택][4]

NSG를 만드는 경우 [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md)의 단계에 따라 NSG를 만들고 보안 규칙을 설정합니다.

## <a name="see-also"></a>참고 항목
이 문서에서는 보안 센터 권장 사항 서브넷 또는 가상 머신에 대한 "네트워크 보안 그룹 활성화"를 구현하는 방법을 보여 주었습니다. NSG 활성화에 대한 자세한 내용은 다음을 참조하세요.

* [NSG(네트워크 보안 그룹)란?](../virtual-network/security-overview.md)
* [네트워크 보안 그룹 관리](../virtual-network/manage-network-security-group.md)

보안 센터에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Security Center에서 보안 정책 설정](tutorial-security-policy.md) -- Azure 구독 및 리소스 그룹에 대해 보안 정책을 구성하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 권장 사항 관리](security-center-recommendations.md) -- 권장 사항이 Azure 리소스 보호에 어떤 도움이 되는지를 알아봅니다.
* [Azure Security Center에서 보안 상태 모니터링](security-center-monitoring.md) –- Azure 리소스의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center에서 보안 경고 관리 및 대응](security-center-managing-and-responding-alerts.md) - 보안 경고를 관리하고 대응하는 방법을 알아봅니다.
* [Azure Security Center를 사용하여 파트너 솔루션 모니터링](security-center-partner-solutions.md) -- 파트너 솔루션의 상태를 모니터링하는 방법을 알아봅니다.
* [Azure Security Center FAQ](security-center-faq.md) - 서비스 사용에 관한 질문과 대답을 찾습니다.
* [Azure 보안 블로그](https://blogs.msdn.com/b/azuresecurity/) -- 최신 Azure 보안 뉴스 및 정보를 가져옵니다.

<!--Image references-->
[1]: ./media/security-center-enable-nsg/enable-nsg.png
[2]:./media/security-center-enable-nsg/configure-nsg-for-subnet.png
[3]: ./media/security-center-enable-nsg/configure-nsg-for-vm.png
[4]: ./media/security-center-enable-nsg/choose-nsg.png
