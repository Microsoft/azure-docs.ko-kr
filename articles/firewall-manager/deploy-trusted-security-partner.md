---
title: Azure 방화벽 관리자 신뢰할 수 있는 보안 파트너 배포
description: Azure Portal를 사용 하 여 Azure 방화벽 관리자의 신뢰할 수 있는 보안을 배포 하는 방법을 알아봅니다.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: bcea9a8674e4b1979698b7d28eb4192172b0dc11
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2019
ms.locfileid: "73931315"
---
# <a name="deploy-a-trusted-security-partner-preview"></a>신뢰할 수 있는 보안 파트너 배포(미리 보기)

[!INCLUDE [Preview](../../includes/firewall-manager-preview-notice.md)]

Azure 방화벽 관리자의 신뢰할 수 있는 *보안 파트너* 를 사용 하 여 친숙 한 최고의 타사 secaas security as a service) 제품을 사용 하 여 사용자에 대한 인터넷 액세스를 보호할 수 있습니다.

지원 되는 시나리오 및 모범 사례 지침에 대해 자세히 알아보려면 [신뢰할 수 있는 보안 파트너 (미리 보기) 란?](trusted-security-partners.md)을 참조 하세요.

지원 되는 보안 파트너는이 미리 보기에 대한 **ZScaler** 및 **iboss** 입니다. 지원 되는 지역은 WestCentralUS, NorthCentralUS, WestUS, WestUS2 및 EastUS입니다.

## <a name="prerequisites"></a>선행 조건

> [!IMPORTANT]
> `Register-AzProviderFeature` PowerShell 명령을 사용하여 Azure Firewall Manager Preview를 명시적으로 사용하도록 설정해야 합니다.

PowerShell 명령 프롬프트에서 다음 명령을 실행합니다.

```azure-powershell
connect-azaccount
Register-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network
```
기능 등록이 완료될 때까지 최대 30분이 걸립니다. 다음 명령을 실행하여 등록 상태를 확인합니다.

`Get-AzProviderFeature -FeatureName AllowCortexSecurity -ProviderNamespace Microsoft.Network`

## <a name="deploy-a-third-party-security-provider-in-a-new-hub"></a>새 허브에 타사 보안 공급자 배포

1. https://portal.azure.com에서 Azure Portal에 로그인합니다.
2. **검색**에서 **방화벽 관리자** 를 입력 하 고 **서비스**에서 선택 합니다.
3. **시작**으로 이동 합니다. **보안 가상 허브 만들기를**선택 합니다. 
4. 구독 및 리소스 그룹을 입력 하 고, 지원 되는 지역을 선택 하 고, 허브 및 가상 WAN 정보를 추가 합니다. 
5. **VPN Gateway 배포** 는 기본적으로 사용 하도록 설정 되어 있습니다. 허브에 신뢰할 수 있는 보안 파트너를 배포 하려면 VPN Gateway 필요 합니다. 
6. **다음 선택: Azure 방화벽**
   > [!NOTE]
   > 신뢰할 수 있는 보안 파트너는 VPN Gateway 터널을 사용 하 여 허브에 연결 합니다. VPN Gateway을 삭제 하면 신뢰할 수 있는 보안 파트너에 대한 연결이 끊어집니다.
7. Azure 방화벽을 배포 하 여 타사 서비스 공급자와 함께 개인 트래픽을 필터링 하 여 인터넷 트래픽을 필터링 하려면 Azure 방화벽에 대한 정책을 선택 합니다. [지원 되는 시나리오](trusted-security-partners.md#key-scenarios)를 참조 하십시오.
8. 허브에 타사 보안 공급자만 배포 하려는 경우 **Azure 방화벽: 사용/** 사용 안 함을 선택 하 여 **사용 안 함**으로 설정 합니다. 
9. **다음: 신뢰할 수 있는 보안 파트너**를 선택 합니다.
10. **트러스트 된 보안 파트너** 를 선택 하 여 **사용**으로 설정 합니다. 파트너를 선택 합니다. 
11. **다음**을 선택합니다. 
12. 콘텐츠를 검토 한 다음 **만들기**를 선택 합니다.

VPN gateway 배포는 30 분 이상 걸릴 수 있습니다.

허브가 만들어졌는지 확인 하려면 Azure 방화벽 관리자-> 보안 허브로 이동 합니다. 허브 > 개요 페이지를 선택 하 여 파트너 이름과 상태를 **보안 연결 보류 중**으로 표시 합니다.

허브가 만들어지고 보안 파트너가 설정 되 면 계속 해 서 보안 공급자를 허브에 연결 합니다.

## <a name="deploy-a-third-party-security-provider-in-an-existing-hub"></a>기존 허브에 타사 보안 공급자 배포

가상 WAN에서 기존 허브를 선택 하 고 *보안 가상 허브*로 변환할 수도 있습니다.

1. **시작**에서 **기존 허브 변환**을 선택 합니다.
2. 구독과 기존 허브를 선택 합니다. 새 허브에 타사 공급자를 배포 하려면 나머지 단계를 수행 합니다.

타사 공급자를 사용 하 여 기존 허브를 보안 허브로 변환 하려면 VPN gateway를 배포 해야 합니다.

## <a name="configure-third-party-security-providers-to-connect-to-a-secured-hub"></a>보안 허브에 연결 하도록 타사 보안 공급자 구성

가상 허브의 VPN Gateway에 대한 터널을 설정 하려면 타사 공급자에 게 허브에 대한 액세스 권한이 있어야 합니다. 이렇게 하려면 서비스 주체를 구독 또는 리소스 그룹에 연결 하 고 액세스 권한을 부여 합니다. 그런 다음 해당 포털을 사용 하 여 타사에 이러한 자격 증명을 제공 해야 합니다.

1. AD (Azure Active Directory) 서비스 주체 만들기: 리디렉션 URL을 건너뛸 수 있습니다. 

   [방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)
2. 서비스 사용자에 대한 액세스 권한 및 범위를 추가 합니다.
   [방법: 포털을 사용 하 여 리소스에 액세스할 수 있는 Azure AD 응용 프로그램 및 서비스 주체 만들기](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)

   > [!NOTE]
   > 더 세부적인 제어를 위해 리소스 그룹에만 액세스를 제한할 수 있습니다.
3. [ZScaler: Microsoft Azure 가상 WAN 통합 지침 구성](https://help.zscaler.com/zia/configuring-microsoft-azure-virtual-wan-integration) 을 수행 합니다.

   - 파트너 포털에 로그인 하 고 자격 증명을 추가 하 여 신뢰할 수 있는 파트너에 게 보안 허브에 대한 액세스 권한을 부여 합니다.
   - 파트너 포털에서 가상 허브를 동기화 하 고, 가상 허브에 대한 터널을 설정 합니다. Azure AD 인증 자격 증명의 유효성을 검사 한 후에이 작업을 수행할 수 있습니다.
   
4. Azure의 Azure 가상 WAN 포털에서 터널 만들기 상태를 확인할 수 있습니다. Azure와 파트너 포털 모두에 **연결 된** 터널이 표시 되 면 다음 단계를 계속 진행 하 여 파트너에 게 인터넷 트래픽을 보내야 하는 분기와 vnet를 선택 하는 경로를 설정 합니다.

## <a name="configure-route-settings"></a>경로 설정 구성

1. Azure 방화벽 관리자-> 보안 허브로 이동 합니다. 
2. 허브를 선택 합니다. 이제 허브 상태가 **보안 연결 보류**중이 아닌 **프로 비전** 됨으로 표시 됩니다.

   타사 공급자가 허브에 연결할 수 있는지 확인 합니다. VPN 게이트웨이의 터널은 **연결 된** 상태 여야 합니다. 이 상태는 이전 상태와 비교 하 여 허브와 타사 파트너 간의 연결 상태를 더 반영 합니다.
3. 허브를 선택 하 고 **경로 설정**으로 이동 합니다.

   허브에 타사 공급자를 배포 하는 경우 허브를 *보안 가상 허브*로 변환 합니다. 이렇게 하면 타사 공급자가 0.0.0.0/0 (기본) 경로를 허브로 광고 하 게 됩니다. 그러나이 기본 경로를 받아야 하는 연결을 옵트인 (opt in) 하지 않는 한, 허브에 연결 된 VNet 연결 및 사이트는이 경로를 얻지 않습니다.
4. **인터넷 트래픽**에서, 타사를 통해 전송 되는 경로를 구성 하도록 **VNet 간** 또는 **인터넷** 에서 또는 둘 다를 선택 합니다.

   허브로 라우팅해야 하는 트래픽 유형만 표시 하지만 Vnet 또는 분기의 경로에는 영향을 주지 않습니다. 이러한 경로는 기본적으로 허브에 연결 된 모든 Vnet/분기로 전파 되지 않습니다.
5. **보안 연결** 을 선택 하 고 이러한 경로를 설정 해야 하는 연결을 선택 해야 합니다. 이는 Vnet/분기가 타사 공급자에 게 인터넷 트래픽을 전송 하기 시작할 수 있음을 나타냅니다.
6. **경로 설정**에서 인터넷 트래픽 아래의 **보안 연결** 을 선택 하 고, 보안을 설정할 VNet 또는 분기 (가상 WAN의*사이트* )를 선택 합니다. **인터넷 트래픽 보안**을 선택 합니다.
   ![보안 인터넷 트래픽](media/deploy-trusted-security-partner/secure-internet-traffic.png)
7. 허브 페이지로 다시 이동 합니다. 이제 허브의 **신뢰할 수 있는 보안 파트너** 상태를 **보호**해야 합니다.

## <a name="branch-or-vnet-internet-traffic-via-third-party-service"></a>타사 서비스를 통한 분기 또는 VNet 인터넷 트래픽

그런 다음 VNet 가상 컴퓨터 또는 분기 사이트에서 인터넷에 액세스할 수 있는지 확인 하 고 트래픽이 타사 서비스로 전달 되는지 확인할 수 있습니다.

경로 설정 단계를 완료 한 후 VNet 가상 머신과 분기 사이트는 0/0을 타사 서비스 경로로 보냅니다. 이러한 가상 컴퓨터에 RDP 또는 SSH를 사용할 수 없습니다. 로그인 하려면 피어 링 VNet에 [Azure 방호](../bastion/bastion-overview.md) 서비스를 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [자습서: Azure Portal을 사용 하 여 Azure 방화벽 관리자 미리 보기로 클라우드 네트워크 보호](secure-cloud-network.md)




