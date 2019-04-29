---
title: Microsoft Azure Cloud Services FAQ에 대한 연결 및 네트워킹 문제 | Microsoft Docs
description: 이 문서는 Microsoft Azure Cloud Services의 연결 및 네트워킹에 대한 질문과 대답을 나열합니다.
services: cloud-services
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2018
ms.author: genli
ms.openlocfilehash: 2a46879a6882e6d45e4a7ccce59e4a02feea9005
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61432962"
---
# <a name="connectivity-and-networking-issues-for-azure-cloud-services-frequently-asked-questions-faqs"></a>연결 및 Azure Cloud Services에 대 한 네트워킹 문제: FAQ(질문과 대답)

이 문서는 [Azure Cloud Services](https://azure.microsoft.com/services/cloud-services)의 연결 및 네트워킹에 대한 질문과 대답을 포함합니다. 크기 정보는 [Cloud Services VM 크기 페이지](cloud-services-sizes-specs.md)를 참조하세요.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>IP는 여러 VIP 클라우드 서비스에서 예약할 수 없습니다.
우선, IP를 예약하려는 가상 머신 인스턴스가 켜져 있는지 확인합니다. 다음으로 스테이징 및 프로덕션 배포 모두에 대해 예약된 IP를 사용하는지 확인합니다. *않습니다* .

## <a name="how-do-i-use-remote-desktop-when-i-have-an-nsg"></a>NSG가 있을 때 원격 데스크톱을 어떻게 사용하나요?
포트 **3389** 및 **20000**에서 트래픽을 허용하는 규칙을 NSG에 추가합니다. 원격 데스크톱은 포트 **3389**를 사용합니다. 클라우드 서비스 인스턴스에서 부하가 분산되므로 연결할 인스턴스를 직접 제어할 수 없습니다. *RemoteForwarder* 및 *RemoteAccess* 에이전트가 RDP(원격 데스크톱 프로토콜) 트래픽을 관리하고 클라이언트에서 RDP 쿠키를 전송하고 연결할 개별 인스턴스를 지정할 수 있도록 합니다. *RemoteForwarder* 및 *RemoteAccess* 에이전트를 사용하려면 포트 **20000**이 열려 있어야 합니다. 이 포트는 NSG가 있으면 차단될 수 있습니다.

## <a name="can-i-ping-a-cloud-service"></a>클라우드 서비스를 ping할 수 있나요?

아니요. 일반적인 "ping"/ICMP 프로토콜을 사용하면 안됩니다. Azure Load Balancer를 통해 ICMP 프로토콜을 사용할 수 없습니다.

연결을 테스트하려면 포트 ping을 수행하는 것이 좋습니다. Ping.exe가 ICMP를 사용하는 반면, 사용자는 PSPing, Nmap, 텔넷과 같은 다른 도구를 사용하여 특정 TCP 포트에 대한 연결을 테스트할 수 있습니다.

자세한 내용은 [ICMP 대신 포트 ping을 사용하여 Azure VM 연결 테스트](https://blogs.msdn.microsoft.com/mast/2014/06/22/use-port-pings-instead-of-icmp-to-test-azure-vm-connectivity/)를 참조하세요.

## <a name="how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service"></a>알 수 없는 IP 주소로부터 클라우드 서비스에 대한 악의적 공격을 의미하는 수천 번의 적중을 수신하지 않도록 방지하려면 어떻게 할까요?
Azure는 DDoS(distributed denial-of-service) 공격에 대해 플랫폼 서비스를 보호하기 위해 다중 계층 네트워크 보안을 구현합니다. Azure DDoS 방어 시스템은 Azure의 연속 모니터링 프로세스의 일부이며 침투 테스트를 통해 지속적으로 개선됩니다. 이 DDoS 방어 시스템은 외부에서의 공격뿐만 아니라 다른 Azure 테넌트에서의 공격에 대응하도록 설계되었습니다. 자세한 내용은 [Azure 네트워크 보안](https://download.microsoft.com/download/C/A/3/CA3FC5C0-ECE0-4F87-BF4B-D74064A00846/AzureNetworkSecurity_v3_Feb2015.pdf)을 참조하세요.

또한 일부 특정 IP 주소를 선택적으로 차단하는 시작 작업을 만들 수 있습니다. 자세한 내용은 [특정 IP 주소 차단](cloud-services-startup-tasks-common.md#block-a-specific-ip-address)을 참조하세요.

## <a name="when-i-try-to-rdp-to-my-cloud-service-instance-i-get-the-message-the-user-account-has-expired"></a>클라우드 서비스 인스턴스에 RDP하려는 경우 “사용자 계정이 만료되었습니다”라는 메시지가 표시됩니다.
RDP 설정에 구성된 만료 날짜를 바이패스하면 “이 사용자 계정이 만료되었습니다”라는 오류 메시지가 표시될 수 있습니다. 다음과 같은 단계를 수행하여 포털에서 만료 날짜를 변경할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하여, 클라우드 서비스로 이동하고 **원격 데스크톱** 탭을 선택합니다.

2. **프로덕션** 또는 **준비 프로세스** 배포 슬롯을 선택합니다.

3. **만료** 날짜를 변경하고 구성을 저장합니다.

이제 컴퓨터에 RDP할 수 있습니다.

## <a name="why-is-azure-load-balancer-not-balancing-traffic-equally"></a>Azure Load Balancer가 트래픽을 동일하게 분산하지 않는 이유는 무엇인가요?
내부 부하 분산 장치 작동 방법에 대한 정보는 [Azure Load Balancer 새 배포 모드](https://azure.microsoft.com/blog/azure-load-balancer-new-distribution-mode/)를 참조하세요.

사용되는 배포 알고리즘은 트래픽을 사용 가능한 서버에 매핑하는 5 튜플(소스 IP, 소스 포트, 대상 IP, 대상 포트 및 프로토콜 종류) 해시입니다. 전송 세션 내에서만 연결이 유지됩니다. 동일한 TCP 또는 UDP 세션의 패킷은 부하 분산 엔드포인트 뒤의 동일한 DIP(데이터 센터 IP) 인스턴스로 전송됩니다. 클라이언트가 연결을 닫았다가 다시 열거나 동일한 원본 IP에서 새 세션을 시작하는 경우 원본 포트가 변경되어 트래픽이 다른 DIP 엔드포인트로 이동합니다.

## <a name="how-can-i-redirect-incoming-traffic-to-the-default-url-of-my-cloud-service-to-a-custom-url"></a>내 클라우드 서비스의 기본 URL에 들어오는 트래픽을 사용자 지정 URL로 리디렉션하려면 어떻게 해야 하나요?

IIS의 URL 다시 쓰기 모듈을 사용하여 클라우드 서비스의 기본 URL(예를 들어 \*.cloudapp.net)에 들어오는 트래픽을 일부 사용자 지정 이름/URL로 리디렉션할 수 있습니다. URL 재작성 모듈은 기본적으로 웹 역할에서 사용 하 고 해당 규칙은 응용 프로그램의 web.config에 구성 되므로 항상 재부팅/이미지로 다시 설치에 관계 없이 VM에서 사용할 수 있습니다. 자세한 내용은 다음을 참조 하세요.

- [URL 다시 쓰기 모듈에 대한 다시 쓰기 규칙 만들기](https://docs.microsoft.com/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)
- [기본 링크 제거](https://stackoverflow.com/questions/32286487/azure-website-how-to-remove-default-link?answertab=votes#tab-top)

## <a name="how-can-i-blockdisable-incoming-traffic-to-the-default-url-of-my-cloud-service"></a>내 클라우드 서비스의 기본 URL에 들어오는 트래픽을 차단하거나 사용하지 않도록 설정하려면 어떻게 해야 하나요?

기본 URL/클라우드 서비스의 이름에 들어오는 트래픽을 방지할 수 있습니다(예를 들어 \*.cloudapp.net). 다음에 표시된 대로 클라우드 서비스 정의(*.csdef) 파일의 사이트 바인딩 구성에서 사용자 지정 DNS 이름(예: www.MyCloudService.com)으로 호스트 헤더를 설정합니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceDefinition name="AzureCloudServicesDemo" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
    <WebRole name="MyWebRole" vmsize="Small">
        <Sites>
            <Site name="Web">
            <Bindings>
                <Binding name="Endpoint1" endpointName="Endpoint1" hostHeader="www.MyCloudService.com" />
            </Bindings>
            </Site>
        </Sites>
        <Endpoints>
            <InputEndpoint name="Endpoint1" protocol="http" port="80" />
        </Endpoints>
        <ConfigurationSettings>
            <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
        </ConfigurationSettings>
    </WebRole>
</ServiceDefinition>
```

이 호스트 헤더 바인딩은 csdef 파일을 통해서 적용되기 때문에 서비스는 사용자 지정 이름인 “www.MyCloudService.com”을 통해서만 액세스할 수 있습니다. “*.cloudapp.net” 도메인에 들어오는 모든 요청은 항상 실패합니다. 서비스에서 사용자 지정 SLB 프로브 또는 내부 부하 분산 장치를 사용하는 경우 서비스의 기본 URL/이름을 차단하는 것은 프로브 동작에 방해가 될 수 있습니다.

## <a name="how-can-i-make-sure-the-public-facing-ip-address-of-a-cloud-service-never-changes"></a>클라우드 서비스의 공용 IP 주소가 절대 바뀌지 않았는지를 어떻게 확인할 수 있습니까?

클라우드 서비스의 공용 IP 주소(VIP로 알려짐)가 절대 바뀌지 않아 몇 가지 특정 클라이언트에 의해 관례적으로 허용 목록에 추가되도록 하려면 이에 연결된 예약 IP가 있어야 합니다. 또는 배포를 삭제하는 경우 Azure에서 제공되는 가상 IP가 사용자의 구독에서 할당이 취소됩니다. VIP 교환 작업에 성공하려면 프로덕션 및 스테이징 슬롯 모두에 대해 예약된 개별 IP가 필요합니다. 없는 경우 교환 작업이 실패합니다. IP 주소를 예약하고 클라우드 서비스에 연결하려면 다음 문서를 참조하세요.

- [기존 클라우드 서비스의 IP 주소 예약](../virtual-network/virtual-networks-reserved-public-ip.md#reserve-the-ip-address-of-an-existing-cloud-service)
- [서비스 구성 파일을 사용하여 클라우드 서비스에 예약된 IP 연결](../virtual-network/virtual-networks-reserved-public-ip.md#associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file)

역할 인스턴스가 둘 이상 있는 경우 클라우드 서비스와 RIP를 연결 가동 중지 시간이 발생 하지 않아야 합니다. 또는 Azure 데이터 센터 IP 범위 허용 목록에 추가할을 수 있습니다. 모든 Azure IP 범위를 찾을 수 있습니다 합니다 [Microsoft 다운로드 센터](https://www.microsoft.com/en-us/download/details.aspx?id=41653)합니다.

이 파일에는 Azure 데이터 센터에서 사용되는 IP 주소 범위(계산, SQL 및 스토리지 범위 포함)가 포함되어 있습니다. 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 반영하는 업데이트 파일이 매주 게시됩니다. 파일에 제시된 새 범위는 데이터 센터에서 적어도 한 주 동안 사용되지 않습니다. Azure에서 실행되는 서비스를 정확하게 식별할 수 있도록 매주 새로운 .xml 파일을 다운로드하여 사이트에서 필요한 변경 작업을 수행하세요. Azure ExpressRoute 사용자는 매월 첫 번째 주에 Azure 공간의 BGP 광고를 업데이트하는 데 이 파일이 사용되고 있음을 알 수 있습니다.

## <a name="how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services"></a>클라우드 서비스와 Azure Resource Manager 가상 네트워크를 사용하려면 어떻게 해야 하나요?

클라우드 서비스는 Azure Resource Manager 가상 네트워크에 배치할 수 없습니다. Resource Manager 가상 네트워크 및 클래식 배포 가상 네트워크는 피어링을 통해 연결할 수 있습니다. 자세한 내용은 [가상 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 참조하세요.


## <a name="how-can-i-get-the-list-of-public-ips-used-by-my-cloud-services"></a>Cloud Services에서 사용되는 공용 IP 목록은 어떻게 확인할 수 있나요?

다음 PS 스크립트를 사용하면 구독에서 Cloud Services에 사용되는 공용 IP 목록을 확인할 수 있습니다.

```powershell
$services = Get-AzureService  | Group-Object -Property ServiceName

foreach ($service in $services)
{
    "Cloud Service '$($service.Name)'"

    $deployment = Get-AzureDeployment -ServiceName $service.Name
    "VIP - " +  $deployment.VirtualIPs[0].Address
    "================================="
}
```
