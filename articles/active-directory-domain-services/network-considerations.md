---
title: Azure AD Domain Services에 대 한 네트워크 계획 및 연결 | Microsoft Docs
description: Azure Active Directory Domain Services을 실행할 때 연결에 사용 되는 가상 네트워크 디자인 고려 사항 및 리소스에 대해 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2020
ms.author: iainfou
ms.openlocfilehash: ec38f16c5a658848eab505794ed1a2d072f22aea
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "88749615"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services에 대 한 가상 네트워크 디자인 고려 사항 및 구성 옵션

Azure Active Directory Domain Services (Azure AD DS)는 다른 응용 프로그램 및 작업에 대 한 인증 및 관리 서비스를 제공 합니다. 네트워크 연결은 주요 구성 요소입니다. 가상 네트워크 리소스를 올바르게 구성 하지 않으면 응용 프로그램 및 워크 로드에서 Azure AD DS 제공 하는 기능을 사용 하 여 통신할 수 없습니다. 가상 네트워크 요구 사항을 계획 하 여 Azure AD DS 필요에 따라 응용 프로그램 및 워크 로드를 제공할 수 있는지 확인 합니다.

이 문서에서는 azure AD DS을 지원 하기 위해 Azure virtual network에 대 한 디자인 고려 사항 및 요구 사항을 간략하게 설명 합니다.

## <a name="azure-virtual-network-design"></a>Azure 가상 네트워크 디자인

네트워크 연결을 제공 하 고 응용 프로그램 및 서비스가 Azure AD DS 관리 되는 도메인에 대해 인증할 수 있도록 허용 하려면 Azure virtual network 및 서브넷을 사용 합니다. 관리 되는 도메인을 자체 가상 네트워크에 배포 하는 것이 가장 좋습니다.

관리 VM 또는 경량 응용 프로그램 워크 로드를 호스트 하기 위해 동일한 가상 네트워크에 별도의 응용 프로그램 서브넷을 포함할 수 있습니다. 더 크거나 복잡 한 응용 프로그램 워크 로드에 대 한 별도의 가상 네트워크는 일반적으로 가장 적합 한 AD DS 피어 링입니다.

가상 네트워크 및 서브넷에 대 한 다음 섹션에서 설명 하는 요구 사항을 충족 하는 경우 다른 디자인 선택 항목이 유효 합니다.

Azure AD DS에 대 한 가상 네트워크를 설계할 때 다음과 같은 고려 사항이 적용 됩니다.

* Azure AD DS는 가상 네트워크와 동일한 Azure 지역에 배포 되어야 합니다.
    * 이번에는 Azure AD 테 넌 트 당 하나의 관리 되는 도메인만 배포할 수 있습니다. 관리 되는 도메인은 단일 지역에 배포 됩니다. [Azure AD DS을 지 원하는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)에서 가상 네트워크를 만들거나 선택 해야 합니다.
* 응용 프로그램 워크 로드를 호스트 하는 다른 Azure 지역 및 가상 네트워크의 근접성을 고려 합니다.
    * 대기 시간을 최소화 하려면 관리 되는 도메인에 대 한 가상 네트워크 서브넷과 동일한 지역에 가까운 코어 응용 프로그램을 유지 합니다. Azure 가상 네트워크 간의 가상 네트워크 피어 링 또는 VPN (가상 사설망) 연결을 사용할 수 있습니다. 이러한 연결 옵션에 대해서는 다음 섹션에서 설명 합니다.
* 가상 네트워크는 관리 되는 도메인에서 제공 하는 서비스 이외의 DNS 서비스를 사용할 수 없습니다.
    * Azure AD DS는 자체 DNS 서비스를 제공 합니다. 이러한 DNS 서비스 주소를 사용 하도록 가상 네트워크를 구성 해야 합니다. 조건부 전달자를 사용 하 여 추가 네임 스페이스에 대 한 이름 확인을 수행할 수 있습니다.
    * 사용자 지정 DNS 서버 설정을 사용 하 여 Vm을 비롯 한 다른 DNS 서버의 쿼리를 전달할 수 없습니다. 가상 네트워크의 리소스는 관리 되는 도메인에서 제공 하는 DNS 서비스를 사용 해야 합니다.

> [!IMPORTANT]
> 서비스를 사용 하도록 설정한 후에는 Azure AD DS을 다른 가상 네트워크로 이동할 수 없습니다.

관리 되는 도메인은 Azure virtual network의 서브넷에 연결 됩니다. 다음 사항을 고려 하 여 Azure AD DS에 대 한이 서브넷을 설계 합니다.

* 관리 되는 도메인은 자체 서브넷에 배포 되어야 합니다. 기존 서브넷 또는 게이트웨이 서브넷을 사용 하지 마세요.
* 네트워크 보안 그룹은 관리 되는 도메인을 배포 하는 동안 만들어집니다. 이 네트워크 보안 그룹에는 올바른 서비스 통신에 필요한 규칙이 포함 되어 있습니다.
    * 사용자 지정 규칙을 사용 하 여 기존 네트워크 보안 그룹을 만들거나 사용 하지 마세요.
* 관리 되는 도메인에는 3-5 IP 주소가 필요 합니다. 서브넷 IP 주소 범위에서이 수의 주소를 제공할 수 있는지 확인 합니다.
    * 사용 가능한 IP 주소를 제한 하면 관리 되는 도메인에서 두 개의 도메인 컨트롤러를 유지 하지 못할 수 있습니다.

다음 예제 다이어그램에서는 관리 되는 도메인에 자체 서브넷이 있고, 외부 연결용 게이트웨이 서브넷이 있고, 응용 프로그램 작업이 가상 네트워크 내의 연결 된 서브넷에 있는 유효한 디자인을 간략하게 설명 합니다.

![권장되는 서브넷 디자인](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Azure AD DS 가상 네트워크에 연결

이전 섹션에서 설명한 것 처럼 Azure의 단일 가상 네트워크에만 관리 되는 도메인을 만들 수 있으며, Azure AD 테 넌 트 당 하나의 관리 되는 도메인만 만들 수 있습니다. 이 아키텍처에 따라 응용 프로그램 워크 로드를 호스트 하는 하나 이상의 가상 네트워크를 관리 되는 도메인의 가상 네트워크에 연결 해야 할 수 있습니다.

다음 방법 중 하나를 사용 하 여 다른 Azure 가상 네트워크에서 호스트 되는 응용 프로그램 작업을 연결할 수 있습니다.

* 가상 네트워크 피어링
* VPN (가상 사설망)

### <a name="virtual-network-peering"></a>가상 네트워크 피어링

가상 네트워크 피어링은 Azure 백본 네트워크를 통해 동일한 지역에 있는 두 개의 가상 네트워크를 연결하는 메커니즘입니다. 글로벌 가상 네트워크 피어 링은 Azure 지역에서 가상 네트워크를 연결할 수 있습니다. 피어 링 두 개의 가상 네트워크를 사용 하면 Vm과 같은 리소스가 개인 IP 주소를 사용 하 여 서로 직접 통신할 수 있습니다. 가상 네트워크 피어 링을 사용 하면 다른 가상 네트워크에 배포 된 응용 프로그램 워크 로드를 사용 하 여 관리 되는 도메인을 배포할 수 있습니다.

![피어링을 사용하여 가상 네트워크 연결](./media/active-directory-domain-services-design-guide/vnet-peering.png)

자세한 내용은 [Azure 가상 네트워크 피어 링 개요](../virtual-network/virtual-network-peering-overview.md)를 참조 하세요.

### <a name="virtual-private-networking-vpn"></a>VPN(가상 사설망)

가상 네트워크를 온-프레미스 사이트 위치에 구성할 수 있는 것과 동일한 방식으로 가상 네트워크를 다른 가상 네트워크 (VNet 간)에 연결할 수 있습니다. 두 연결 모두 VPN gateway를 사용 하 여 IPsec/IKE를 통한 보안 터널을 만듭니다. 이 연결 모델을 사용 하면 관리 되는 도메인을 Azure 가상 네트워크에 배포한 후 온-프레미스 위치 또는 다른 클라우드를 연결할 수 있습니다.

![VPN Gateway를 사용 하 여 가상 네트워크 연결](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

가상 사설망을 사용 하는 방법에 대 한 자세한 내용은 [Azure Portal를 사용 하 여 vnet 간 VPN 게이트웨이 연결 구성](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)을 참조 하세요.

## <a name="name-resolution-when-connecting-virtual-networks"></a>가상 네트워크를 연결할 때 이름 확인

관리 되는 도메인의 가상 네트워크에 연결 된 가상 네트워크에는 일반적으로 자체 DNS 설정이 있습니다. 가상 네트워크를 연결 하는 경우 관리 되는 도메인에서 제공 하는 서비스를 확인 하기 위해 연결 하는 가상 네트워크에 대 한 이름 확인을 자동으로 구성 하지 않습니다. 응용 프로그램 워크 로드에서 관리 되는 도메인을 찾을 수 있도록 하려면 연결 하는 가상 네트워크에 대 한 이름 확인을 구성 해야 합니다.

연결 하는 가상 네트워크를 지 원하는 DNS 서버에서 또는 관리 되는 도메인의 가상 네트워크에서 동일한 DNS IP 주소를 사용 하 여 이름 확인을 사용 하도록 설정할 수 있습니다.

## <a name="network-resources-used-by-azure-ad-ds"></a>Azure AD DS에서 사용 하는 네트워크 리소스

관리 되는 도메인은 배포 중에 일부 네트워킹 리소스를 만듭니다. 이러한 리소스는 관리 되는 도메인의 성공적인 작업 및 관리에 필요 하며 수동으로 구성할 수 없습니다.

| Azure 리소스                          | Description |
|:----------------------------------------|:---|
| 네트워크 인터페이스 카드                  | Azure AD DS는 Windows Server에서 실행 되는 두 개의 Dc (도메인 컨트롤러)에서 관리 되는 도메인을 Azure Vm으로 호스팅합니다. 각 VM에는 가상 네트워크 서브넷에 연결 하는 가상 네트워크 인터페이스가 있습니다. |
| 동적 표준 공용 IP 주소      | Azure AD DS는 표준 SKU 공용 IP 주소를 사용 하 여 동기화 및 관리 서비스와 통신 합니다. 공용 IP 주소에 대 한 자세한 내용은 [Azure의 ip 주소 유형 및 할당 방법](../virtual-network/public-ip-addresses.md)을 참조 하세요. |
| Azure 표준 부하 분산 장치            | Azure AD DS는 NAT (네트워크 주소 변환) 및 부하 분산을 위해 표준 SKU 부하 분산 장치를 사용 합니다 (보안 LDAP와 함께 사용 하는 경우). Azure 부하 분산 장치에 대 한 자세한 내용은 [Azure Load Balancer?](../load-balancer/load-balancer-overview.md) 을 참조 하세요. |
| NAT (Network address translation) 규칙 | Azure AD DS는 부하 분산 장치에 대 한 세 가지 NAT 규칙을 만들고 사용 합니다 .이 규칙은 보안 HTTP 트래픽에 대 한 규칙 하나, 보안 PowerShell 원격을 위한 두 가지 규칙입니다. |
| 부하 분산 장치 규칙                     | 관리 되는 도메인이 TCP 포트 636에서 보안 LDAP를 사용 하도록 구성 된 경우 부하 분산 장치에서 트래픽을 분산 하는 세 가지 규칙이 만들어지고 사용 됩니다. |

> [!WARNING]
> 부하 분산 장치 또는 규칙을 수동으로 구성 하는 것과 같이 Azure AD DS에서 만든 네트워크 리소스를 삭제 하거나 수정 하지 마십시오. 네트워크 리소스를 삭제 하거나 수정 하는 경우 Azure AD DS 서비스 중단이 발생할 수 있습니다.

## <a name="network-security-groups-and-required-ports"></a>네트워크 보안 그룹 및 필수 포트

[NSG (네트워크 보안 그룹)](../virtual-network/security-overview.md) 에는 Azure virtual network의 트래픽에 대 한 네트워크 트래픽을 허용 하거나 거부 하는 규칙의 목록이 포함 되어 있습니다. 네트워크 보안 그룹은 서비스에서 인증 및 관리 기능을 제공할 수 있도록 하는 일련의 규칙을 포함 하는 관리 되는 도메인을 배포할 때 만들어집니다. 이 기본 네트워크 보안 그룹은 관리 되는 도메인이 배포 된 가상 네트워크 서브넷과 연결 됩니다.

다음 네트워크 보안 그룹 규칙은 관리 되는 도메인에서 인증 및 관리 서비스를 제공 하는 데 필요 합니다. 관리 되는 도메인이 배포 된 가상 네트워크 서브넷에 대해 이러한 네트워크 보안 그룹 규칙을 편집 하거나 삭제 하지 마세요.

| 포트 번호 | 프로토콜 | 원본                             | 대상 | 작업 | 필수 | 용도 |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | 모두         | Allow  | 예      | Azure AD 테 넌 트와 동기화. |
| 3389        | TCP      | CorpNetSaw                         | 모두         | Allow  | 예      | 도메인 관리. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | 모두         | Allow  | 예      | 도메인 관리. |

이러한 규칙을 적용하는 Azure 표준 부하 분산 장치가 생성됩니다. 이 네트워크 보안 그룹은 Azure AD DS를 보호하며, 관리되는 도메인이 제대로 작동하는 데 꼭 필요합니다. 이 네트워크 보안 그룹을 삭제 하지 마세요. 부하 분산 장치가 없으면 제대로 작동 하지 않습니다.

> [!WARNING]
> 이러한 네트워크 리소스 및 구성은 수동으로 편집 하지 마세요. 잘못 구성 된 네트워크 보안 그룹 또는 사용자 정의 경로 테이블을 관리 되는 도메인이 배포 된 서브넷과 연결 하면 Microsoft에서 도메인을 서비스 하 고 관리 하는 기능을 방해할 수 있습니다. Azure AD 테 넌 트와 관리 되는 도메인 간의 동기화도 중단 됩니다.
>
> 보안 LDAP를 사용 하는 경우 필요한 경우 외부 트래픽을 허용 하는 데 필요한 TCP 포트 636 규칙을 추가할 수 있습니다. 이 규칙을 추가 해도 네트워크 보안 그룹 규칙은 지원 되지 않는 상태가 되지 않습니다. 자세한 내용은 [인터넷을 통한 보안 LDAP 액세스 잠금](tutorial-configure-ldaps.md#lock-down-secure-ldap-access-over-the-internet) 을 참조 하세요.
>
> *Allowvnetinbound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *Allowvnetinbound*, *allowinternetoutbound*및 *DenyAllOutBound* 에 대 한 기본 규칙이 네트워크 보안 그룹에도 존재 합니다. 이러한 기본 규칙을 편집 하거나 삭제 하지 마십시오.
>
> Azure AD DS에서 도메인을 업데이트 하 고 관리 하는 것을 차단 하는 부적절 하 게 구성 된 네트워크 보안 그룹 및/또는 사용자 정의 경로 테이블이 적용 된 배포에는 Azure SLA가 적용 되지 않습니다.

### <a name="port-443---synchronization-with-azure-ad"></a>포트 443-Azure AD와의 동기화

* Azure AD 테 넌 트를 관리 되는 도메인과 동기화 하는 데 사용 됩니다.
* 이 포트에 대 한 액세스 권한이 없으면 관리 되는 도메인을 Azure AD 테 넌 트와 동기화 할 수 없습니다. 사용자는 자신의 암호에 대 한 변경 내용이 관리 되는 도메인에 동기화 되지 않기 때문에 로그인 하지 못할 수 있습니다.
* IP 주소에 대 한이 포트에 대 한 인바운드 액세스는 기본적으로 **AzureActiveDirectoryDomainServices** service 태그를 사용 하 여 제한 됩니다.
* 이 포트에서 아웃 바운드 액세스를 제한 하지 않습니다.

### <a name="port-3389---management-using-remote-desktop"></a>포트 3389-원격 데스크톱을 사용 하 여 관리

* 관리 되는 도메인의 도메인 컨트롤러에 대 한 원격 데스크톱 연결에 사용 됩니다.
* 기본 네트워크 보안 그룹 규칙은 *CorpNetSaw* 서비스 태그를 사용 하 여 트래픽을 추가로 제한 합니다.
    * 이 서비스 태그는 원격 데스크톱을 관리 되는 도메인에 사용할 수 있도록 Microsoft 회사 네트워크의 보안 액세스 워크스테이션만 허용 합니다.
    * 관리 또는 문제 해결 시나리오와 같은 비즈니스 근거에만 액세스를 사용할 수 있습니다.
* 이 규칙은 *Deny*로 설정할 수 있으며, 필요한 경우에만 *허용* 으로 설정 합니다. 대부분의 관리 및 모니터링 작업은 PowerShell 원격을 사용 하 여 수행 됩니다. RDP는 고급 문제 해결을 위해 Microsoft에서 관리 되는 도메인에 원격으로 연결 해야 하는 드문 경우에만 사용 됩니다.

> [!NOTE]
> 이 네트워크 보안 그룹 규칙을 편집 하려고 하면 포털에서 *CorpNetSaw* service 태그를 수동으로 선택할 수 없습니다. *CorpNetSaw* service 태그를 사용 하는 규칙을 수동으로 구성 하려면 Azure PowerShell 또는 Azure CLI를 사용 해야 합니다.
>
> 예를 들어 다음 스크립트를 사용 하 여 RDP를 허용 하는 규칙을 만들 수 있습니다. 
>
> `Get-AzureRmNetworkSecurityGroup -Name "nsg-name" -ResourceGroupName "resource-group-name" | Add-AzureRmNetworkSecurityRuleConfig -Name "new-rule-name" -Access "Allow" -Protocol "TCP" -Direction "Inbound" -Priority "priority-number" -SourceAddressPrefix "CorpNetSaw" -SourcePortRange "" -DestinationPortRange "3389" -DestinationAddressPrefix "" | Set-AzureRmNetworkSecurityGroup`

### <a name="port-5986---management-using-powershell-remoting"></a>포트 5986-PowerShell 원격을 사용 하 여 관리

* 관리 되는 도메인에서 PowerShell 원격을 사용 하 여 관리 작업을 수행 하는 데 사용 됩니다.
* 이 포트에 대 한 액세스 권한이 없으면 관리 되는 도메인을 업데이트, 구성, 백업 또는 모니터링할 수 없습니다.
* 리소스 관리자 기반 가상 네트워크를 사용 하는 관리 되는 도메인의 경우이 포트에 대 한 인바운드 액세스를 *AzureActiveDirectoryDomainServices* service 태그로 제한할 수 있습니다.
    * 클래식 기반 가상 네트워크를 사용 하는 레거시 관리 도메인의 경우이 포트에 대 한 인바운드 액세스를 다음 원본 IP 주소 ( *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*및 *104.40.87.209*)로 제한할 수 있습니다.

    > [!NOTE]
    > 2017에서는 Azure Resource Manager 네트워크의 호스트에서 Azure AD Domain Services를 사용할 수 있었습니다. 그 이후에는 Azure Resource Manager의 최신 기능을 사용 하 여 보다 안전한 서비스를 구축할 수 있었습니다. Azure Resource Manager 배포가 클래식 배포를 완전히 대체 하기 때문에 Azure AD DS 클래식 가상 네트워크 배포는 2023 년 3 월 1 일에 사용 중지 됩니다.
    >
    > 자세한 내용은 공식 사용 중단 [알림](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/) 을 참조 하세요.

## <a name="user-defined-routes"></a>사용자 정의 경로

사용자 정의 경로는 기본적으로 생성 되지 않으며 Azure AD DS 올바르게 작동 하지 않습니다. 경로 테이블을 사용 해야 하는 경우 *0.0.0.0* 경로를 변경 하지 마십시오. 이 경로를 변경 하면 Azure AD DS 중단 되 고 관리 되는 도메인을 지원 되지 않는 상태로 전환 합니다.

또한 해당 Azure 서비스 태그에 포함 된 IP 주소에서 관리 되는 도메인의 서브넷으로 인바운드 트래픽을 라우팅합니다. 서비스 태그 및 관련 IP 주소에 대 한 자세한 내용은 [AZURE Ip 범위 및 서비스 태그-공용 클라우드](https://www.microsoft.com/en-us/download/details.aspx?id=56519)를 참조 하세요.

> [!CAUTION]
> 이러한 Azure 데이터 센터 IP 범위는 예 고 없이 변경 될 수 있습니다. 최신 IP 주소가 있는지 확인 하는 프로세스가 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS에서 사용 하는 몇 가지 네트워크 리소스 및 연결 옵션에 대 한 자세한 내용은 다음 문서를 참조 하세요.

* [Azure 가상 네트워크 피어 링](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN 게이트웨이](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure 네트워크 보안 그룹](../virtual-network/security-overview.md)