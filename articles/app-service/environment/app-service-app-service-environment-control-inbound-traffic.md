---
title: 인바운드 트래픽 v1 제어
description: App Service Environment에 대 한 인바운드 트래픽을 제어 하는 방법을 알아봅니다. 이 문서는 레거시 v1 ASE를 사용하는 고객에게만 제공됩니다.
author: ccompy
ms.assetid: 4cc82439-8791-48a4-9485-de6d8e1d1a08
ms.topic: article
ms.date: 01/11/2017
ms.author: stefsch
ms.custom: seodec18
ms.openlocfilehash: fe9326ea9ebd5afe981b7ba6c34b1a5d51e084b0
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962063"
---
# <a name="how-to-control-inbound-traffic-to-an-app-service-environment"></a>App Service Environment로의 인바운드 트래픽을 제어하는 방법
## <a name="overview"></a>개요
App Service Environment를 Azure Resource Manager 가상 네트워크에서 **만들 수도 있고** 클래식 배포 모델 [가상 네트워크][virtualnetwork]에서 **만들 수도 있습니다**.  App Service Environment를 만들 때 새 가상 네트워크 및 새 서브넷을 정의할 수 있습니다. 대신 기존 가상 네트워크 및 기존 서브넷에서 App Service Environment를 만들 수 있습니다.  6 월 2016을 기준으로 공용 주소 범위 또는 RFC1918 주소 공간 (개인 주소) 중 하나를 사용 하는 가상 네트워크에 Ase를 배포할 수도 있습니다.  자세한 내용은 [How To Create a App Service Environment][HowToCreateAnAppServiceEnvironment]를 참조 하세요.

항상 서브넷 내에 App Service Environment를 만듭니다. 서브넷은 업스트림 장치 및 서비스 뒤에서 인바운드 트래픽을 잠그는 데 사용할 수 있는 네트워크 경계를 제공 합니다. 이 설치를 통해 특정 업스트림 IP 주소만 HTTP 및 HTTPS 트래픽을 허용할 수 있습니다.

서브넷의 인바운드 및 아웃바운드 네트워크 트래픽은 [네트워크 보안 그룹][NetworkSecurityGroups]을 사용하여 제어합니다. 인바운드 트래픽을 제어 하려면 네트워크 보안 그룹에 네트워크 보안 규칙을 만듭니다. 그런 다음 App Service Environment 포함 된 서브넷에 네트워크 보안 그룹을 할당 합니다.

네트워크 보안 그룹을 서브넷에 할당 하면 네트워크 보안 그룹에 정의 된 허용 및 거부 규칙에 따라 App Service Environment의 앱에 대 한 인바운드 트래픽이 허용 되거나 차단 됩니다.

[!INCLUDE [app-service-web-to-api-and-mobile](../../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="inbound-network-ports-used-in-an-app-service-environment"></a>App Service 환경에서 사용하는 인바운드 네트워크 포트
네트워크 보안 그룹을 사용 하 여 인바운드 네트워크 트래픽을 잠그기 전에 App Service Environment에서 사용 하는 필수 및 선택적 네트워크 포트 집합을 알고 있어야 합니다.  일부 포트에 대한 트래픽을 실수로 닫으면 App Service Environment의 기능이 손실될 수 있습니다.

다음 목록에는 App Service Environment에서 사용 하는 포트가 나와 있습니다. 명확하게 언급이 없는 한 모든 포트는 **TCP**입니다.

* 454: TLS를 통해 App Service 환경을 관리 하 고 유지 관리 하기 위해 Azure 인프라에서 사용 되는  **필수 포트** 입니다.  이 포트에 대 한 트래픽을 차단 하지 않습니다.  이 포트는 항상 ASE의 공용 VIP에 바인딩됩니다.
* 455: TLS를 통해 App Service 환경을 관리 하 고 유지 관리 하기 위해 Azure 인프라에서 사용 되는  **필수 포트** 입니다.  이 포트에 대 한 트래픽을 차단 하지 않습니다.  이 포트는 항상 ASE의 공용 VIP에 바인딩됩니다.
* 80: App Service 환경의 App Service 계획에서 실행되는 앱에 대한 인바운드 HTTP 트래픽의 기본 포트입니다.  ILB 지원 ASE에서 이 포트는 ASE의 ILB 주소에 바인딩됩니다.
* 443: App Service Environment App Service 계획에서 실행 되는 앱에 대 한 인바운드 TLS 트래픽의 기본 포트입니다.  ILB 지원 ASE에서 이 포트는 ASE의 ILB 주소에 바인딩됩니다.
* 21: FTP에 대한 컨트롤 채널입니다.  FTP를 사용 하지 않는 경우이 포트를 안전 하 게 차단할 수 있습니다.  ILB 지원 ASE에서 이 포트는 ASE의 ILB 주소에 바인딩될 수 있습니다.
* 990: FTPS에 대한 컨트롤 채널입니다.  FTPS를 사용 하지 않는 경우이 포트를 안전 하 게 차단할 수 있습니다.  ILB 지원 ASE에서 이 포트는 ASE의 ILB 주소에 바인딩될 수 있습니다.
* 10001~10020: FTP에 대한 데이터 채널입니다.  컨트롤 채널과 마찬가지로 FTP를 사용 하지 않는 경우 이러한 포트를 안전 하 게 차단할 수 있습니다.  ILB 지원 ASE에서 이 포트는 ASE의 ILB 주소에 바인딩될 수 있습니다.
* 4016: Visual Studio 2012를 통한 원격 디버깅에 사용됩니다.  기능을 사용 하지 않는 경우이 포트를 안전 하 게 차단할 수 있습니다.  ILB 지원 ASE에서 이 포트는 ASE의 ILB 주소에 바인딩됩니다.
* 4018: Visual Studio 2013을 통한 원격 디버깅에 사용됩니다.  기능을 사용 하지 않는 경우이 포트를 안전 하 게 차단할 수 있습니다.  ILB 지원 ASE에서 이 포트는 ASE의 ILB 주소에 바인딩됩니다.
* 4020: Visual Studio 2015를 통한 원격 디버깅에 사용됩니다.  기능을 사용 하지 않는 경우이 포트를 안전 하 게 차단할 수 있습니다.  ILB 지원 ASE에서 이 포트는 ASE의 ILB 주소에 바인딩됩니다.

## <a name="outbound-connectivity-and-dns-requirements"></a>아웃 바운드 연결 및 DNS 요구 사항
App Service 환경이 제대로 작동하려면 다양한 엔드포인트에 대한 아웃바운드 액세스가 필요합니다. ASE에서 사용하는 외부 엔드포인트의 전체 목록은 [ExpressRoute에 대한 네트워크 구성](app-service-app-service-environment-network-configuration-expressroute.md#required-network-connectivity) 문서의 "필수 네트워크 연결" 섹션에 있습니다.

App Service Environment에는 가상 네트워크에 대해 구성된 유효한 DNS 인프라가 필요합니다.  App Service Environment를 만든 후에 DNS 구성이 변경 되는 경우 개발자는 강제로 App Service Environment 새 DNS 구성을 선택할 수 있습니다.  **다시 시작** 아이콘을 사용 하 여 롤링 환경 다시 부팅을 트리거하는 경우 환경에서 새 DNS 구성을 선택 합니다. **다시 시작** 아이콘은 App Service Environment 관리 블레이드의 맨 위에 있는 [Azure Portal][NewPortal]에 있습니다.

또한 App Service Environment을 만들기 전에 vnet의 사용자 지정 DNS 서버를 미리 설정 하는 것이 좋습니다.  App Service Environment를 만드는 동안 가상 네트워크의 DNS 구성이 변경 되 면 App Service Environment 만들기 프로세스가 실패 합니다.  마찬가지로, VPN 게이트웨이의 다른 쪽 끝에 연결할 수 없거나 사용할 수 없는 사용자 지정 DNS 서버가 있는 경우에도 App Service Environment 생성 프로세스가 실패 합니다.

## <a name="creating-a-network-security-group"></a>네트워크 보안 그룹 만들기
네트워크 보안 그룹의 작동 방식에 대한 자세한 내용은 다음 [정보][NetworkSecurityGroups]를 참조하세요.  아래 Azure 서비스 관리 예제는 네트워크 보안 그룹의 주요 사항에 대해 설명 합니다. 이 예제에서는 App Service Environment를 포함 하는 서브넷에 네트워크 보안 그룹을 구성 하 고 적용 합니다.

**참고:** [Azure Portal](https://portal.azure.com) 또는 Azure PowerShell를 사용 하 여 네트워크 보안 그룹을 그래픽으로 구성할 수 있습니다.

네트워크 보안 그룹은 먼저 구독과 연결된 독립 실행형 엔터티로 만들어집니다. 네트워크 보안 그룹은 Azure 지역에 생성 되므로 App Service Environment와 동일한 지역에 네트워크 보안 그룹을 만듭니다.

다음 명령은 네트워크 보안 그룹을 만드는 방법을 보여 줍니다.

```azurepowershell-interactive
New-AzureNetworkSecurityGroup -Name "testNSGexample" -Location "South Central US" -Label "Example network security group for an app service environment"
```

네트워크 보안 그룹을 만들면 하나 이상의 네트워크 보안 규칙이 해당 그룹에 추가됩니다.  규칙 집합은 시간이 지남에 따라 변경 될 수 있으므로 규칙 우선 순위에 사용 되는 번호 매기기 체계를 사용 해야 합니다. 이 방법을 사용 하면 시간에 따라 추가 규칙을 쉽게 삽입할 수 있습니다.

아래 예제에서 규칙은 Azure 인프라에서 App Service Environment를 관리 하 고 유지 관리 하는 데 필요한 관리 포트에 대 한 액세스 권한을 명시적으로 부여 합니다.  모든 관리 트래픽은 TLS를 통해 전달 되며 클라이언트 인증서로 보호 됩니다. 포트가 열린 경우에도 Azure 관리 인프라 이외의 엔터티에서 액세스할 수 없습니다.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "ALLOW AzureMngmt" -Type Inbound -Priority 100 -Action Allow -SourceAddressPrefix 'INTERNET'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '454-455' -Protocol TCP
```

포트 80 및 443에 대 한 액세스를 잠그고 업스트림 장치 또는 서비스 뒤 App Service Environment를 "숨기기" 할 때 업스트림 IP 주소를 기억할 수 있습니다.  예를 들어 WAF (웹 응용 프로그램 방화벽)를 사용 하는 경우 WAF에 고유한 IP 주소 또는 주소가 있습니다. WAF는 트래픽을 다운스트림 App Service Environment에 프록시 하는 경우이를 사용 합니다.  네트워크 보안 규칙의 *SourceAddressPrefix* 매개 변수에서이 IP 주소를 사용 해야 합니다.

아래 예제에서는 특정 업스트림 IP 주소의 인바운드 트래픽이 명시적으로 허용됩니다.  주소 *1.2.3.4* 는 업스트림 WAF의 IP 주소에 대한 자리 표시자로 사용됩니다.  업스트림 디바이스 또는 서비스에서 사용하는 주소와 일치하도록 값을 변경합니다.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTP" -Type Inbound -Priority 200 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '80' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT HTTPS" -Type Inbound -Priority 300 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '443' -Protocol TCP
```

FTP 지원이 필요한 경우 다음 규칙을 템플릿으로 사용 하 여 FTP 제어 포트 및 데이터 채널 포트에 대 한 액세스 권한을 부여 합니다.  FTP는 상태 저장 프로토콜 이므로 기존 HTTP/HTTPS 방화벽이 나 프록시 장치를 통해 FTP 트래픽을 라우팅할 수 없습니다.  이 경우 *SourceAddressPrefix* 를 FTP 클라이언트가 실행 되는 개발자 또는 배포 컴퓨터의 IP 주소 범위와 같은 다른 값으로 설정 해야 합니다. 

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPCtrl" -Type Inbound -Priority 400 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '21' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT FTPDataRange" -Type Inbound -Priority 500 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '10001-10020' -Protocol TCP
```

(**참고:**  데이터 채널 포트 범위는 미리 보기 기간 동안 변경 될 수 있습니다.)

Visual Studio를 통한 원격 디버깅을 사용하는 경우 다음과 같은 규칙에 따라 액세스 권한을 부여할 수 있습니다.  각 버전에는 원격 디버깅을 위해 다른 포트를 사용 하기 때문에 지원 되는 각 버전의 Visual Studio에 대 한 별도의 규칙이 있습니다.  FTP 액세스와 마찬가지로 원격 디버깅 트래픽은 기존 WAF 또는 프록시 디바이스를 통해 제대로 흐르지 않을 수 있습니다.  대신 *SourceAddressPrefix* 를 Visual Studio를 실행하는 개발자 컴퓨터의 IP 주소 범위로 설정할 수 있습니다.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2012" -Type Inbound -Priority 600 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4016' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2013" -Type Inbound -Priority 700 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4018' -Protocol TCP
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityRule -Name "RESTRICT RemoteDebuggingVS2015" -Type Inbound -Priority 800 -Action Allow -SourceAddressPrefix '1.2.3.4/32'  -SourcePortRange '*' -DestinationAddressPrefix '*' -DestinationPortRange '4020' -Protocol TCP
```

## <a name="assigning-a-network-security-group-to-a-subnet"></a>서브넷에 네트워크 보안 그룹 할당
네트워크 보안 그룹에는 모든 외부 트래픽에 대 한 액세스를 거부 하는 기본 보안 규칙이 있습니다. 위의 네트워크 보안 규칙과이 규칙을 결합 하는 경우 *허용* 작업과 연결 된 원본 주소 범위의 트래픽만 App Service Environment에서 실행 되는 앱에 트래픽을 보낼 수 있습니다.

네트워크 보안 그룹을 보안 규칙으로 채운 후 App Service Environment 포함 된 서브넷에 할당 합니다.  할당 명령은 App Service Environment이 있는 가상 네트워크의 이름 및 App Service Environment 만들어진 서브넷의 이름을 참조 합니다.  

아래 예제에서는 서브넷 및 가상 네트워크에 할당되는 네트워크 보안 그룹을 보여 줍니다.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Set-AzureNetworkSecurityGroupToSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

할당은 장기 실행 작업 이므로 완료 하는 데 몇 분 정도 걸릴 수 있습니다. 네트워크 보안 그룹 할당이 성공 하면 *허용* 규칙과 일치 하는 인바운드 트래픽만 App Service Environment의 앱에 성공적으로 도달 하 게 됩니다.

완전성을 위해 다음 예제에서는 서브넷에서 네트워크 보안 그룹을 제거 하 고 분리 하는 방법을 보여 줍니다.

```azurepowershell-interactive
Get-AzureNetworkSecurityGroup -Name "testNSGexample" | Remove-AzureNetworkSecurityGroupFromSubnet -VirtualNetworkName 'testVNet' -SubnetName 'Subnet-test'
```

## <a name="special-considerations-for-explicit-ip-ssl"></a>명시적 IP-SSL에 대한 특별 고려 사항
앱이 명시적 Ase 주소 (공용 VIP가 있는 경우에 *만* 적용 됨)로 구성 된 경우 App Service Environment의 기본 ip 주소를 사용 하는 대신 HTTP 및 HTTPS 트래픽은 포트 80 및 443 이외의 포트를 통해 서브넷으로 흐릅니다.

각 IP SSL 주소에서 사용 하는 개별 포트 쌍을 찾으려면 포털로 이동 하 여 App Service Environment의 세부 정보 UX 블레이드를 확인 합니다.  **모든 설정**  >  **IP 주소**를 선택 합니다.  **Ip 주소** 블레이드는 App Service Environment에 대해 명시적으로 구성 된 모든 IP SSL 주소 표를 표시 합니다. 블레이드에는 각 IP SSL 주소와 연결 된 HTTP 및 HTTPS 트래픽을 라우팅하는 데 사용 되는 특수 포트 쌍도 표시 됩니다.  네트워크 보안 그룹에서 규칙을 구성할 때 DestinationPortRange 매개 변수에이 포트 쌍을 사용 합니다.

ASE의 앱이 IP-HTTPS를 사용 하도록 구성 된 경우 외부 고객은 특별 한 포트 쌍 매핑에 대해 걱정할 필요가 없습니다.  앱에 대한 트래픽은 구성된 IP-SSL 주소를 정상적으로 이동됩니다.  특수 포트 쌍으로의 변환은 자동으로 발생 하며,이는 ASE를 포함 하는 서브넷에 라우팅 트래픽의 최종 레그를 수행 하는 동안 자동으로 발생 합니다. 

## <a name="getting-started"></a>시작
App Service 환경을 시작 하려면 [App Service Environment 소개][IntroToAppServiceEnvironment]를 참조 하세요.

자세한 내용은 [App Service Environment에서 백 엔드 리소스에 안전 하 게 연결][SecurelyConnecttoBackend]을 참조 하세요.

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[virtualnetwork]: ../../virtual-network/virtual-networks-faq.md
[HowToCreateAnAppServiceEnvironment]: app-service-web-how-to-create-an-app-service-environment.md
[NetworkSecurityGroups]: ../../virtual-network/virtual-network-vnet-plan-design-arm.md
[IntroToAppServiceEnvironment]:  app-service-app-service-environment-intro.md
[SecurelyConnecttoBackend]:  app-service-app-service-environment-securely-connecting-to-backend-resources.md
[NewPortal]:  https://portal.azure.com  

<!-- IMAGES -->