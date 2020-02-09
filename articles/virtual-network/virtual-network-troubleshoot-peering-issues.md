---
title: 가상 네트워크 피어링 문제 해결
description: 대부분의 가상 네트워크 피어 링 문제를 해결 하는 데 도움이 되는 단계입니다.
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-network
ms.assetid: 1a3d1e84-f793-41b4-aa04-774a7e8f7719
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: 1fddbe908ccebc1384dcccde0810366f1a6d5da7
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796247"
---
# <a name="troubleshoot-virtual-network-peering-issues"></a>가상 네트워크 피어링 문제 해결

이 문제 해결 가이드에서는 대부분의 [가상 네트워크 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 문제를 해결 하는 데 유용한 단계를 제공 합니다.

![가상 네트워크 피어 링 다이어그램](./media/virtual-network-troubleshoot-peering-issues/4489538_en_1.png)

## <a name="configure-virtual-network-peering-between-two-virtual-networks"></a>두 가상 네트워크 간에 가상 네트워크 피어 링 구성

가상 네트워크가 동일한 구독 또는 다른 구독에 있나요?

### <a name="the-virtual-networks-are-in-the-same-subscription"></a>가상 네트워크가 동일한 구독에 있습니다.

동일한 구독에 있는 가상 네트워크에 대 한 가상 네트워크 피어 링을 구성 하려면 다음 문서에서 메서드를 사용 합니다.

* 가상 네트워크가 *동일한 지역*에 있는 경우 [피어 링 만들기](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#create-a-peering)를 참조 하세요.
* 가상 네트워크가 *서로 다른 지역*에 있는 경우 [가상 네트워크 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)을 참조 하세요. 

> [!Note]
> 다음 리소스에 대 한 글로벌 가상 네트워크 피어 링을 통해 연결이 작동 하지 않습니다. 
>
> * 기본 내부 부하 분산 장치 (ILB) SKU 뒤의 Vm (가상 머신)
> * Redis cache (기본 ILB SKU 사용)
> * Application gateway (기본 ILB SKU 사용)
> * Virtual machine scale sets (기본 ILB SKU 사용)
> * Azure Service Fabric 클러스터 (기본 ILB SKU 사용)
> * SQL Server Always On (기본 ILB SKU 사용)
> * PowerApps에 대 한 Azure App Service Environment (기본 ILB SKU 사용)
> * Azure API Management (기본 ILB SKU 사용)
> * Azure Active Directory Domain Services (Azure AD DS) (기본 ILB SKU 사용)

자세한 내용은 전역 피어 링의 [요구 사항 및 제약 조건](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) 을 참조 하세요.

### <a name="the-virtual-networks-are-in-different-subscriptions-or-active-directory-tenants"></a>가상 네트워크가 서로 다른 구독 또는 Active Directory 테넌트에 있습니다.

다른 구독 또는 Active Directory 테넌트의 가상 네트워크에 대 한 가상 네트워크 피어 링을 구성 하려면 [Azure CLI에 대 한 다른 구독에서 피어 링 만들기](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)를 참조 하세요.

> [!Note]
> 네트워크 피어 링을 구성 하려면 두 구독 모두에서 **네트워크 참가자** 권한이 있어야 합니다. 자세한 내용은 [피어 링 권한](virtual-network-manage-peering.md#permissions)을 참조 하세요.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-that-uses-on-premises-resources"></a>온-프레미스 리소스를 사용 하는 허브-스포크 토폴로지를 사용 하 여 가상 네트워크 피어 링 구성

![온-프레미스 스포크를 사용 하는 가상 네트워크 피어 링 다이어그램](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1a.png)

### <a name="for-a-site-to-site-connection-or-an-expressroute-connection"></a>사이트 간 연결 또는 Express 경로 연결의 경우

[가상 네트워크 피어 링에 대 한 VPN gateway 전송 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)의 단계를 수행 합니다.

### <a name="for-point-to-site-connections"></a>지점 및 사이트 간 연결의 경우

1. [가상 네트워크 피어 링에 대 한 VPN gateway 전송 구성](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-peering-gateway-transit?toc=/azure/virtual-network/toc.json)의 단계를 수행 합니다.
2. 가상 네트워크 피어 링이 설정 되거나 변경 된 후 지점 및 사이트 간 클라이언트에서 스포크 가상 네트워크에 업데이트 된 경로를 가져오도록 지점 및 사이트 간 패키지를 다운로드 하 여 다시 설치 합니다.

## <a name="configure-virtual-network-peering-with-hub-spoke-topology-virtual-network"></a>허브-스포크 토폴로지 가상 네트워크를 사용 하 여 가상 네트워크 피어 링 구성

![가상 네트워크 스포크를 사용 하는 가상 네트워크 피어 링 다이어그램](./media/virtual-network-troubleshoot-peering-issues/4488712_en_1b.png)

### <a name="the-virtual-networks-are-in-the-same-region"></a>가상 네트워크가 동일한 지역에 있습니다.


1. 허브 가상 네트워크에서 NVA (네트워크 가상 어플라이언스)를 구성 합니다.
1. 스포크 가상 네트워크에서 다음 홉 유형인 "네트워크 가상 어플라이언스"가 적용 된 사용자 정의 경로를 사용 합니다.

자세한 내용은 [서비스](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)연결을 참조 하세요.

> [!Note]
> NVA를 설정 하는 데 도움이 필요한 경우 [nva 공급 업체에 문의 하세요](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines).

NVA 장치 설정 및 라우팅 문제를 해결 하는 데 도움이 필요한 경우 [Azure의 네트워크 가상 어플라이언스 문제](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)를 참조 하세요.

### <a name="the-virtual-networks-are-in-different-regions"></a>가상 네트워크가 서로 다른 지역에 있습니다.

이제 글로벌 가상 네트워크 피어 링을 통한 전송이 지원 됩니다. 다음 리소스에 대 한 글로벌 가상 네트워크 피어 링을 통해 연결이 작동 하지 않습니다.

* 기본 ILB SKU 뒤의 Vm
* Redis cache (기본 ILB SKU 사용)
* Application gateway (기본 ILB SKU 사용)
* 크기 집합 (기본 ILB SKU 사용)
* 클러스터 Service Fabric (기본 ILB SKU 사용)
* SQL Server Always On (기본 ILB SKU 사용)
* App Service Environment (기본 ILB SKU 사용)
* API Management (기본 ILB SKU 사용)
* Azure AD DS (기본 ILB SKU 사용)

전역 피어 링 요구 사항 및 제한은에 대 한 자세한 내용은 [가상 네트워크 피어 링](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints)을 참조 하세요.

## <a name="troubleshoot-a-connectivity-issue-between-two-peered-virtual-networks"></a>두 피어 링 가상 네트워크 간의 연결 문제 해결

필요한 [역할 및 사용 권한이](virtual-network-manage-peering.md#permissions)있는 계정으로 [Azure Portal](https://portal.azure.com/) 에 로그인 합니다. 가상 네트워크를 선택 하 고 **피어 링**을 선택한 다음 **상태** 필드를 확인 합니다. 상태는 무엇 인가요?

### <a name="the-peering-status-is-connected"></a>피어 링 상태는 "연결 됨"입니다.

이 문제를 해결 하려면 다음을 수행 합니다.

1. 네트워크 트래픽 흐름을 확인 합니다.

   원본 VM에서 대상 VM으로의 [연결 문제 해결](https://docs.microsoft.com/azure/network-watcher/network-watcher-connectivity-overview) 및 [IP 흐름 확인](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) 을 사용 하 여 트래픽 흐름에 간섭을 일으키는 nsg 또는 udr이 있는지 여부를 확인 합니다.

   방화벽 또는 NVA를 사용 하는 경우: 
   1. 이 단계가 완료 된 후 복원할 수 있도록 UDR 매개 변수를 문서화 합니다.
   2. NVA를 가리키는 원본 VM 서브넷 또는 NIC에서 UDR을 다음 홉으로 제거 합니다. 원본 VM에서 NVA를 우회 하는 대상으로 직접의 연결을 확인 합니다. 이 단계가 작동 하지 않는 경우 [Nva 문제 해결사](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)를 참조 하세요.

2. 네트워크 추적을 사용 합니다. 
   1. 대상 VM에서 네트워크 추적을 시작 합니다. Windows의 경우 **Netsh**를 사용할 수 있습니다. Linux의 경우 **TCPDump**를 사용 합니다.
   2. 원본에서 대상 IP로 **Tcpping** 또는 **psping** 를 실행 합니다.

      다음은 **Tcpping** 명령의 예입니다. `tcping64.exe -t <destination VM address> 3389`

   3. **Tcpping** 이 완료 되 면 대상에서 네트워크 추적을 중지 합니다.
   4. 패킷이 원본에서 도착 하는 경우 네트워킹 문제가 발생 하지 않습니다. VM 방화벽과 해당 포트에서 수신 대기 하는 응용 프로그램을 모두 검사 하 여 구성 문제를 찾습니다.

   > [!Note]
   > 글로벌 가상 네트워크 피어 링 (다른 지역의 가상 네트워크)을 통해 다음 리소스 유형에 연결할 수 없습니다.
   >
   > * 기본 ILB SKU 뒤의 Vm
   > * Redis cache (기본 ILB SKU 사용)
   > * Application gateway (기본 ILB SKU 사용)
   > * 크기 집합 (기본 ILB SKU 사용)
   > * 클러스터 Service Fabric (기본 ILB SKU 사용)
   > * SQL Server Always On (기본 ILB SKU 사용)
   > * App Service Environment (기본 ILB SKU 사용)
   > * API Management (기본 ILB SKU 사용)
   > * Azure AD DS (기본 ILB SKU 사용)

자세한 내용은 전역 피어 링의 [요구 사항 및 제약 조건](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) 을 참조 하세요.

### <a name="the-peering-status-is-disconnected"></a>피어 링 상태는 "연결 끊김"입니다.

이 문제를 해결 하려면 두 가상 네트워크에서 피어 링을 삭제 한 후 다시 만드십시오.

## <a name="troubleshoot-a-connectivity-issue-between-a-hub-spoke-virtual-network-and-an-on-premises-resource"></a>허브-스포크 가상 네트워크와 온-프레미스 리소스 간의 연결 문제 해결

네트워크에서 타사 NVA 또는 VPN gateway를 사용 하나요?

### <a name="my-network-uses-a-third-party-nva-or-vpn-gateway"></a>네트워크에서 타사 NVA 또는 VPN gateway를 사용 합니다.

타사 NVA 또는 VPN gateway에 영향을 주는 연결 문제를 해결 하려면 다음 문서를 참조 하세요.

* [NVA 문제 해결사](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-nva)
* [서비스 체인](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)

### <a name="my-network-does-not-use-a-third-party-nva-or-vpn-gateway"></a>네트워크에서 타사 NVA 또는 VPN gateway를 사용 하지 않습니다.

허브 가상 네트워크 및 스포크 가상 네트워크에 VPN gateway가 있나요?

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-have-a-vpn-gateway"></a>허브 가상 네트워크와 스포크 가상 네트워크에 모두 VPN 게이트웨이가 있습니다.

원격 게이트웨이 사용은 지원 되지 않습니다.

스포크 가상 네트워크에 이미 VPN gateway가 있는 경우 스포크 가상 네트워크에서 **원격 게이트웨이 사용** 옵션이 지원 되지 않습니다. 이는 가상 네트워크 피어 링 제한 때문입니다.

#### <a name="both-the-hub-virtual-network-and-the-spoke-virtual-network-do-not-have-a-vpn-gateway"></a>허브 가상 네트워크와 스포크 가상 네트워크에 모두 VPN 게이트웨이가 없습니다.

사이트 간 또는 Azure Express 경로 연결의 경우 온-프레미스에서 원격 가상 네트워크에 연결 문제의 다음과 같은 주요 원인을 확인 합니다.

* 게이트웨이가 있는 가상 네트워크에서 **전달 된 트래픽 허용** 확인란이 선택 되어 있는지 확인 합니다.
* 게이트웨이가 없는 가상 네트워크에서 **원격 게이트웨이 사용** 확인란이 선택 되어 있는지 확인 합니다.
* 네트워크 관리자에 게 온-프레미스 장치를 확인 하 여 모든 사용자에 게 원격 가상 네트워크 주소 공간이 추가 되었는지 확인 합니다.

지점 및 사이트 간 연결의 경우:

* 게이트웨이가 있는 가상 네트워크에서 **전달 된 트래픽 허용** 확인란이 선택 되어 있는지 확인 합니다.
* 게이트웨이가 없는 가상 네트워크에서 **원격 게이트웨이 사용** 확인란이 선택 되어 있는지 확인 합니다.
* 지점 및 사이트 간 클라이언트 패키지를 다운로드 하 여 다시 설치 합니다. 새로 피어 링 가상 네트워크 경로는 지점 및 사이트 간 클라이언트에 경로를 자동으로 추가 하지 않습니다.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-the-same-region"></a>동일한 지역의 스포크 가상 네트워크 간 허브-스포크 네트워크 연결 문제 해결

허브 네트워크는 NVA를 포함 해야 합니다. NVA가 다음 홉으로 설정 된 스포크에서 UDRs를 구성 하 고 허브 가상 네트워크에서 **전달 된 트래픽 허용** 을 사용 하도록 설정 합니다.

자세한 내용은 [서비스](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#service-chaining)연결을 참조 하 고, 원하는 [nva 공급 업체](https://support.microsoft.com/help/2984655/support-for-azure-market-place-for-virtual-machines) 와 이러한 요구 사항을 논의 합니다.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-spoke-virtual-networks-in-different-regions"></a>다른 지역의 스포크 가상 네트워크 간 허브-스포크 네트워크 연결 문제 해결

이제 글로벌 가상 네트워크 피어 링을 통한 전송이 지원 됩니다. 다음 리소스에 대 한 글로벌 가상 네트워크 피어 링을 통해 연결이 작동 하지 않습니다.

* 기본 ILB SKU 뒤의 Vm
* Redis cache (기본 ILB SKU 사용)
* Application gateway (기본 ILB SKU 사용)
* 크기 집합 (기본 ILB SKU 사용)
* 클러스터 Service Fabric (기본 ILB SKU 사용)
* SQL Server Always On (기본 ILB SKU 사용)
* App Service Environment (기본 ILB SKU 사용)
* API Management (기본 ILB SKU 사용)
* Azure AD DS (기본 ILB SKU 사용)

자세한 내용은 전역 피어 링 및 [다른 VPN 토폴로지의](https://blogs.msdn.microsoft.com/igorpag/2016/02/11/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-v2/) [요구 사항 및 제약 조건](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#requirements-and-constraints) 을 참조 하세요.

## <a name="troubleshoot-a-hub-spoke-network-connectivity-issue-between-a-web-app-and-the-spoke-virtual-network"></a>웹 앱과 스포크 가상 네트워크 간의 허브-스포크 네트워크 연결 문제 해결

이 문제를 해결 하려면 다음을 수행 합니다.

1. Azure 포털에 로그인합니다. 
1. 웹 앱에서 **네트워킹**을 선택한 다음 **VNet 통합**을 선택 합니다.
1. 원격 가상 네트워크를 볼 수 있는지 여부를 확인 합니다. 수동으로 원격 가상 네트워크 주소 공간 (**동기화 네트워크** 및 **추가 경로**)을 입력 합니다.

자세한 내용은 다음 문서를 참조하세요.

* [Azure 가상 네트워크와 앱 통합](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
* [지점 및 사이트 간 VPN 라우팅 정보](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing)

## <a name="troubleshoot-a-virtual-network-peering-configuration-error-message"></a>가상 네트워크 피어 링 구성 오류 메시지 문제 해결 

### <a name="current-tenant-tenant-id-isnt-authorized-to-access-linked-subscription"></a>현재 테넌트 `<TENANT ID>` 연결 된 구독에 액세스할 수 있는 권한이 없습니다.

이 문제를 해결 하려면 [피어 링-Azure CLI 만들기](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions#cli)를 참조 하세요.

### <a name="not-connected"></a>연결되지 않음

이 문제를 해결 하려면 두 가상 네트워크에서 피어 링을 삭제 한 다음 다시 만듭니다.

### <a name="failed-to-peer-a-databricks-virtual-network"></a>Databricks 가상 네트워크를 피어 링 하지 못했습니다.

이 문제를 해결 하려면 **Azure Databricks**에서 가상 네트워크 피어 링을 구성 하 고 **리소스 ID**를 사용 하 여 대상 가상 네트워크를 지정 합니다. 자세한 내용은 [Databricks virtual network를 원격 가상 네트워크에 피어](https://docs.azuredatabricks.net/administration-guide/cloud-configurations/azure/vnet-peering.html#id2)링을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [Azure VM 간의 연결 문제 해결](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)
