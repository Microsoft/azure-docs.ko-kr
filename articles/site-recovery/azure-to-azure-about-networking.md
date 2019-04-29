---
title: Azure Site Recovery를 사용하는 Azure 간 재해 복구 정보 | Microsoft Docs
description: Azure Site Recovery를 사용하는 Azure VM의 복제에 대한 네트워킹을 간략히 설명합니다.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 3/29/2019
ms.author: sujayt
ms.openlocfilehash: a6c9c690efe8b75cd1a939de1c68cf4e5bd40d70
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789801"
---
# <a name="about-networking-in-azure-to-azure-replication"></a>Azure 간 복제의 네트워킹 정보



이 아티클에서는 [Azure Site Recovery](site-recovery-overview.md)를 사용하여 지역 간에 Azure VM을 복제 및 복구할 때의 네트워킹 지침을 제공합니다.

## <a name="before-you-start"></a>시작하기 전에

Site Recovery가 [이 시나리오](azure-to-azure-architecture.md)에 재해 복구를 지원하는 방법을 알아봅니다.

## <a name="typical-network-infrastructure"></a>일반적인 네트워크 인프라

다음 다이어그램에서는 Azure VM에서 실행되는 애플리케이션에 대한 일반적인 Azure 환경을 보여 줍니다.

![고객 환경](./media/site-recovery-azure-to-azure-architecture/source-environment.png)

온-프레미스 네트워크와 Azure 간의 연결에 Azure ExpressRoute 또는 VPN 연결을 사용하는 경우 환경은 다음과 같습니다.

![고객 환경](./media/site-recovery-azure-to-azure-architecture/source-environment-expressroute.png)

일반적으로 네트워크는 방화벽 및 NSG(네트워크 보안 그룹)를 사용하여 보호됩니다. 방화벽은 URL 또는 IP 기반 허용 목록을 사용하여 네트워크 연결을 제어합니다. NSG는 IP 주소 범위를 사용하여 네트워크 연결을 제어하는 규칙을 제공합니다.

>[!IMPORTANT]
> 인증된 프록시를 사용한 네트워크 연결 제어는 Site Recovery에서 지원되지 않으며 복제를 사용할 수 없습니다.


## <a name="outbound-connectivity-for-urls"></a>URL에 대한 아웃바운드 연결

URL 기반 방화벽 프록시를 사용하여 아웃바운드 연결을 제어하는 경우 이러한 Site Recovery URL을 허용하세요.


**URL** | **세부 정보**  
--- | ---
\*.blob.core.windows.net | VM에서 원본 지역의 캐시 저장소 계정에 데이터를 쓸 수 있도록 하는 데 필요합니다. 를 알고 있는 경우 모든 캐시 저장소 계정은 Vm에 대 한 하면 허용 목록에 추가할 특정 저장소 계정 Url (예: cache1.blob.core.windows.net 및 cache2.blob.core.windows.net) 대신 *. blob.core.windows.net
login.microsoftonline.com | Site Recovery 서비스 URL에 대한 권한 부여 및 인증에 필요합니다.
*.hypervrecoverymanager.windowsazure.com | VM에서 Site Recovery 서비스 통신이 발생할 수 있도록 하는 데 필요합니다. 방화벽 프록시 Ip를 지 원하는 경우에 해당 ' Site Recovery IP'를 사용할 수 있습니다.
\*.servicebus.windows.net | VM에서 Site Recovery 모니터링 및 진단 데이터를 쓸 수 있도록 하는 데 필요합니다. 방화벽 프록시 Ip를 지 원하는 경우에 해당 ' Site Recovery 모니터링 IP'를 사용할 수 있습니다.

## <a name="outbound-connectivity-for-ip-address-ranges"></a>IP 주소 범위에 대한 아웃바운드 연결

IP 기반 방화벽 프록시 또는 NSG 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 이러한 IP 범위를 허용해야 합니다.

- 원본 지역의 저장소 계정 해당하는 모든 IP 주소 범위
    - 원본 지역에 대한 NSG 규칙을 기반으로 [저장소 서비스 태그](../virtual-network/security-overview.md#service-tags)를 만듭니다.
    - VM에서 캐시 저장소 계정에 데이터를 쓸 수 있도록 이러한 주소를 허용합니다.
- AAD에 해당하는 모든 IP 주소에 대한 액세스를 허용하는 [AAD(Azure Active Directory) 서비스 태그](../virtual-network/security-overview.md#service-tags) 기반 NSG 규칙을 만드세요.
    - 나중에 AAD(Azure Active Directory)에 새 주소가 추가될 때 새 NSG 규칙을 만들어야 합니다.
- Site Recovery 서비스 엔드포인트 IP 주소는 [XML 파일로 제공](https://aka.ms/site-recovery-public-ips)되며 대상 위치에 따라 달라집니다.
- 프로덕션 NSG에서 규칙을 만들기 전에 테스트 NSG에서 필요한 NSG 규칙을 만들고 문제가 없는지 확인하는 것이 좋습니다.


Site Recovery IP 주소 범위는 다음과 같습니다.

   **대상** | **Site Recovery IP** |  **Site Recovery 모니터링 IP**
   --- | --- | ---
   동아시아 | 52.175.17.132 | 13.94.47.61
   동남아시아 | 52.187.58.193 | 13.76.179.223
   중앙 인도 | 52.172.187.37 | 104.211.98.185
   인도 남부 | 52.172.46.220 | 104.211.224.190
   미국 중북부 | 23.96.195.247 | 168.62.249.226
   유럽 북부 | 40.69.212.238 | 52.169.18.8
   서유럽 | 52.166.13.64 | 40.68.93.145
   미국 동부 | 13.82.88.226 | 104.45.147.24
   미국 서부 | 40.83.179.48 | 104.40.26.199
   미국 중남부 | 13.84.148.14 | 104.210.146.250
   미국 중부 | 40.69.144.231 | 52.165.34.144
   미국 동부 2 | 52.184.158.163 | 40.79.44.59
   일본 동부 | 52.185.150.140 | 138.91.1.105
   일본 서부 | 52.175.146.69 | 138.91.17.38
   브라질 남부 | 191.234.185.172 | 23.97.97.36
   오스트레일리아 동부 | 104.210.113.114 | 191.239.64.144
   오스트레일리아 남동부 | 13.70.159.158 | 191.239.160.45
   캐나다 중부 | 52.228.36.192 | 40.85.226.62
   캐나다 동부 | 52.229.125.98 | 40.86.225.142
   미국 중서부 | 52.161.20.168 | 13.78.149.209
   미국 서부 2 | 52.183.45.166 | 13.66.228.204
   영국 서부 | 51.141.3.203 | 51.141.14.113
   영국 남부 | 51.140.43.158 | 51.140.189.52
   영국 남부 2 | 13.87.37.4| 13.87.34.139
   영국 북부 | 51.142.209.167 | 13.87.102.68
   한국 중부 | 52.231.28.253 | 52.231.32.85
   한국 남부 | 52.231.198.185 | 52.231.200.144
   프랑스 중부 | 52.143.138.106 | 52.143.136.55
   프랑스 남부 | 52.136.139.227 |52.136.136.62
   오스트레일리아 중부| 20.36.34.70 | 20.36.46.142
   오스트레일리아 중부 2| 20.36.69.62 | 20.36.74.130
   남아프리카 서부 | 102.133.72.51 | 102.133.26.128
   남아프리카 북부 | 102.133.160.44 | 102.133.154.128
   US Gov 버지니아 | 52.227.178.114 | 23.97.0.197
   미국 아이오와 주 정부 | 13.72.184.23 | 23.97.16.186
   미국 정부 애리조나 | 52.244.205.45 | 52.244.48.85
   미국 정부 텍사스 | 52.238.119.218 | 52.238.116.60
   미국 국방부 동부 | 52.181.164.103 | 52.181.162.129
   미국 국방부 중부 | 52.182.95.237 | 52.182.90.133
## <a name="example-nsg-configuration"></a>NSG 구성 예제

이 예제에서는 복제할 VM에 대한 NSG 규칙을 구성하는 방법을 보여 줍니다.

- NSG 규칙을 사용하여 아웃바운드 연결을 제어하는 경우 필요한 모든 IP 주소 범위에 대한 포트:443에 "HTTPS 아웃바운드 허용" 규칙을 사용합니다.
- 이 예제에서는 VM 원본 위치가 "미국 동부"이고 대상 위치가 "미국 중부"라고 가정합니다.

### <a name="nsg-rules---east-us"></a>NSG 규칙 - 미국 동부

1. 아래 스크린샷에 표시된 것처럼 NSG에서 "Storage.EastUS"에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

      ![storage-tag](./media/azure-to-azure-about-networking/storage-tag.png)

2. 아래 스크린샷에 표시된 것처럼 NSG에서 “AzureActiveDirectory”에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

      ![aad-tag](./media/azure-to-azure-about-networking/aad-tag.png)

3. 대상 위치에 해당하는 Site Recovery IP에 대한 아웃바운드 HTTPS(443) 규칙을 만듭니다.

   **위치**: | **Site Recovery IP 주소** |  **Site Recovery 모니터링 IP 주소**
    --- | --- | ---
   미국 중부 | 40.69.144.231 | 52.165.34.144

### <a name="nsg-rules---central-us"></a>NSG 규칙 - 미국 중부

이러한 규칙은 장애 조치 후 대상 지역에서 원본 지역으로의 복제를 활성화하는 데 필요합니다.

1. NSG에서 "Storage.CentralUS"에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

2. NSG에서 “AzureActiveDirectory”에 대한 아웃바운드 HTTPS(443) 보안 규칙을 만듭니다.

3. 원본 위치에 해당하는 Site Recovery IP에 대한 아웃바운드 HTTPS(443) 규칙을 만듭니다.

   **위치**: | **Site Recovery IP 주소** |  **Site Recovery 모니터링 IP 주소**
    --- | --- | ---
   미국 중부 | 13.82.88.226 | 104.45.147.24

## <a name="network-virtual-appliance-configuration"></a>네트워크 가상 어플라이언스 구성

NVA(네트워크 가상 어플라이언스)를 사용하여 VM에서 아웃바운드 네트워크 트래픽을 제어하는 경우 모든 복제 트래픽이 NVA를 통해 전달되면 어플라이언스는 제한을 받을 수 있습니다. 복제 트래픽이 NVA로 이동하지 않도록 "Storage"에 대한 가상 네트워크에서 네트워크 서비스 엔드포인트를 만드는 것이 좋습니다.

### <a name="create-network-service-endpoint-for-storage"></a>Storage에 대한 네트워크 서비스 엔드포인트 만들기
복제 트래픽이 Azure 경계를 나가지 않도록 "Storage"에 대한 가상 네트워크에서 네트워크 서비스 엔드포인트를 만들 수 있습니다.

- Azure 가상 네트워크를 선택하고 '서비스 엔드포인트'를 클릭합니다.

    ![storage-endpoint](./media/azure-to-azure-about-networking/storage-service-endpoint.png)

- '추가'를 클릭하면 '서비스 엔드포인트 추가' 탭이 열립니다.
- '서비스' 아래의 'Microsoft.Storage' 및 '서브넷' 필드 아래의 필요한 서브넷을 선택하고 '추가'를 클릭합니다.

>[!NOTE]
>ASR에 사용되는 저장소 계정에 대한 가상 네트워크 액세스를 제한하지 마십시오. '모든 네트워크'에서 액세스를 허용해야 합니다.

### <a name="forced-tunneling"></a>강제 터널링

[사용자 지정 경로](../virtual-network/virtual-networks-udr-overview.md#custom-routes)를 사용하여 Azure 기본 시스템 경로의 0.0.0.0/0 주소 접두사를 재정의하고 VM 트래픽을 온-프레미스 NVA(네트워크 가상 어플라이언스)로 우회시킬 수 있지만 Site Recovery 복제에는 이 구성이 권장되지 않습니다. 사용자 지정 경로를 사용하는 경우 복제 트래픽이 Azure 경계를 나가지 않도록 "Storage"에 대한 가상 네트워크에서 [가상 네트워크 서비스 엔드포인트를 생성](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage)해야 합니다.

## <a name="next-steps"></a>다음 단계
- [Azure 가상 머신을 복제](site-recovery-azure-to-azure.md)하여 워크로드 보호를 시작합니다.
- Azure 가상 머신 장애 조치(failover)를 위한 [IP 주소 보존](site-recovery-retain-ip-azure-vm-failover.md)에 대해 자세히 알아보세요.
- 재해 복구에 자세히 알아보려면 [ExpressRoute 사용 하 여 Azure virtual machines](azure-vm-disaster-recovery-with-expressroute.md)합니다.
