---
title: '가상 네트워크 성능 문제 해결: Azure | Microsoft Docs'
description: 이 페이지는 Azure 네트워크 링크 성능을 테스트하는 표준화된 방법을 제공합니다.
services: expressroute
author: tracsman
ms.service: expressroute
ms.topic: article
ms.date: 12/20/2017
ms.author: jonor
ms.custom: seodec18
ms.openlocfilehash: 9ec310ffaa9d2bb297abde9341bf7b6c2dc763b4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60883319"
---
# <a name="troubleshooting-network-performance"></a>네트워크 성능 문제 해결
## <a name="overview"></a>개요
Azure는 온-프레미스 네트워크에서 Azure에 빠르고 안정적으로 연결하는 방법을 제공합니다. 크고 작은 고객이 Azure에서 비즈니스를 실행하기 위해 사이트 간 VPN이나 ExpressRoute 같은 방법을 사용하고 있습니다. 그런데 성능이 기대에 못 미치거나 이전 경험보다 떨어지면 어떻게 될까요? 이 문서에서는 특정 환경을 테스트하고 기준을 설정하는 방법을 표준화합니다.

이 문서에서는 두 호스트 간의 네트워크 대기 시간 및 대역폭을 쉽고 일관적으로 테스트하는 방법을 보여 줍니다. 또한 Azure 네트워크를 살펴보고 문제점을 격리하는 방법에 대한 몇 가지 조언을 제공합니다. 설명해 드린 PowerShell 스크립트 및 도구를 사용하려면 네트워크에 두 개 호스트가 필요합니다(테스트할 링크의 양쪽 끝에 하나씩). 한 호스트는 Windows Server 또는 Desktop이어야 하고, 다른 호스트는 Windows 또는 Linux여야 합니다. 

>[!NOTE]
>문제 해결 방법, 도구 및 사용되는 방법은 개인의 기호에 따라 선택하면 됩니다. 이 문서에서는 제가 자주 사용하는 접근 방식과 도구를 설명하겠습니다. 사람마다 다른 방법을 사용할 수 있으며, 다른 방법으로 문제를 해결한다고 해서 잘못될 것은 하나도 없습니다. 하지만 아직 자신만의 확실한 방법이 없는 분들은 이 문서를 통해 네트워크 문제를 해결하는 자신만의 방법, 도구 및 기본 설정을 개발할 수 있을 것입니다.
>
>

## <a name="network-components"></a>네트워크 구성 요소
문제 해결 방법을 자세히 살펴보기 전에 일반적인 용어 및 구성 요소를 살펴보겠습니다. 이 토론을 통해 Azure에서 연결이 가능하게 해주는 종단 간 체인의 각 구성 요소에 대해 알아볼 것입니다.
[![1]][1]

크게 요약하자면, 다음과 같은 세 가지 주요 네트워크 라우팅 도메인이 있습니다.

- Azure 네트워크(오른쪽의 파란색 클라우드)
- 인터넷 또는 WAN(가운데의 녹색 클라우드)
- 회사 네트워크(왼쪽의 복숭아색 클라우드)

오른쪽에서 왼쪽으로 다이어그램을 보면서 각 구성 요소를 간략하게 살펴보겠습니다.
 - **가상 머신** - 서버에 여러 NIC가 있을 수 있으며 고정 경로, 기본 경로 및 운영 체제 설정이 개발자의 생각대로 트래픽을 주고 받도록 만들어야 합니다. 또한 각 VM SKU에는 대역폭 제한이 있습니다. 작은 VM SKU를 사용하는 경우 NIC에 제공되는 대역폭에 따라 트래픽이 제한됩니다. 저는 VM의 대역폭을 충분한 수준으로 유지하기 위해 테스트용으로 DS5v2를 주로 사용합니다(비용을 줄이기 위해 테스트가 끝나면 삭제).
 - **NIC** - 의심스러운 NIC에 할당된 개인 IP를 알아야 합니다.
 - **NIC NSG** - NIC 수준에서 적용되는 특정 NSG가 있을 수 있으며, NSG 규칙 집합이 전달하려는 트래픽에 적합해야 합니다. 예를 들어 테스트 트래픽을 전달할 수 있도록 iPerf에 대한 5201, RDP에 대한 3389 또는 SSH에 대한 22를 열어야 합니다.
 - **VNet 서브넷** - NIC는 특정 서브넷에 할당되며, 해당 서브넷과 연결된 NIC 및 규칙을 알고 있어야 합니다.
 - **서브넷 NSG** - NIC와 마찬가지로, 서브넷에도 NSG를 적용할 수 있습니다. NSG 규칙 집합이 전달하려는 트래픽에 적합해야 합니다. (NIC에 대한 인바운드 트래픽의 경우 서브넷 NSG가 먼저 적용되고, 이와 반대로 VM의 아웃바운드 트래픽의 경우 NIC NSG가 먼저 적용된 후 서브넷 NSG가 작동합니다).
 - **서브넷 UDR** - 사용자 정의 경로가 트래픽을 중간 홉(예: 방화벽 또는 부하 분산 장치)으로 보낼 수 있습니다. 트래픽에 대한 UDR이 있는지 확인하고, 만약 있다면 어디로 가는지, 그 다음 홉이 트래픽에 대해 무엇을 하는지 알아야 합니다. (예를 들어 방화벽이 동일한 두 호스트 사이에서 일부 트래픽을 전달하고 다른 트래픽을 거부할 수 있습니다).
 - **게이트웨이 서브넷/NSG/UDR** - VM 서브넷과 마찬가지로, 게이트웨이 서브넷에 NSG와 UDR이 있을 수 있습니다. NSG와 UDR이 있는지 여부와 트래픽에 미치는 영향을 알아야 합니다.
 - **VNet 게이트웨이(ExpressRoute)** - 피어링(ExpressRoute) 또는 VPN을 사용하도록 설정되면 트래픽이 라우팅되는 방식 또는 그 여부에 영향을 줄 수 있는 설정은 그리 많지 않습니다. 여러 ExpressRoute 회로 또는 VPN 터널이 동일한 VNet 게이트웨이에 연결되어 있는 경우 이 설정이 연결 기본 설정 및 트래픽에서 사용하는 경로에 영향을 주기 때문에 연결 가중치 설정을 알고 있어야 합니다.
 - **경로 필터**(표시되지 않음) - 경로 필터는 ExpressRoute에서 Microsoft Peering에만 적용되지만, Microsoft Peering에 예상하는 경로가 보이지 않는지 확인하는 것이 중요합니다. 

현재 위치는 링크의 WAN 부분입니다. 이 라우팅 도메인은 서비스 공급자, 회사 WAN 또는 인터넷일 수 있습니다. 이러한 링크와 관련된 여러 홉, 기술 및 회사에서 문제를 해결하기가 다소 어려울 수 있습니다. 여러분이 Azure와 회사 네트워크를 모두 제거한 후 이 회사 및 홉 컬렉션을 시작하는 경우가 자주 있습니다.

이전 다이어그램에서 맨 왼쪽에 있는 것이 여러분의 회사 네트워크입니다. 회사 크기에 따라 이 라우팅 도메인은 여러분과 WAN 사이에 있는 몇 대의 네트워크 디바이스이거나 캠퍼스/엔터프라이즈 네트워크의 디바이스 레이어일 수 있습니다.

이러한 세 가지 상위 수준 네트워크 환경의 복잡성을 고려할 때 에지에서 시작한 후 성능이 양호한 곳과 성능이 저하되는 위치를 표시하는 것이 가장 적합한 경우가 자주 있습니다. 이 방법은 세 도메인을 라우팅하는 문제를 식별한 후 해당 특정 환경의 문제 해결에 집중하는 데 도움이 될 수 있습니다.

## <a name="tools"></a>도구
대부분의 네트워크 문제는 ping 및 traceroute 같은 기본 도구를 사용하여 분석하고 격리할 수 있습니다. Wireshark 같은 패킷 분석만큼 깊이 들어가야 하는 경우는 별로 없습니다. 문제 해결에 도움을 주기 위해 이러한 도구 중 일부를 간편한 패키지에 집어넣는 AzureCT(Azure 연결 도구 키트)가 개발되었습니다. 저는 성능 테스트에 iPerf와 PSPing을 사용합니다. iPerf는 자주 사용되는 도구로 대부분의 운영 체제에서 실행됩니다. iPerf는 기본 성능 테스트에 적합하며 사용 방법이 매우 간단합니다. PSPing은 SysInternals에서 개발한 ping 도구입니다. PSPing은 간편한 명령 하나로 TCP 및 ICMP ping을 수행할 수 있는 간단한 방법입니다. 두 도구는 호스트의 디렉터리로 파일을 복사하여 "설치"되는 경량 도구입니다.

이러한 도구와 메서드를 개발자가 설치하여 사용할 수 있는 PowerShell 모듈(AzureCT) 하나에 통합했습니다.

### <a name="azurect---the-azure-connectivity-toolkit"></a>AzureCT - Azure 연결 도구 키트
AzureCT PowerShell 모듈은 [가용성 테스트][Availability Doc] 및 [성능 테스트][Performance Doc]라는 두 가지 구성 요소를 포함하고 있습니다. 이 문서에서는 성능 테스트에 대한 내용만 다루기 때문에 이 PowerShell 모듈의 두 가지 링크 성능 명령을 집중적으로 살펴볼 것입니다.

성능 테스트에 이 도구 키트를 사용하기 위한 세 가지 기본 단계가 있습니다. 1) PowerShell 모듈을 설치하고, 2) 지원 애플리케이션 iPerf 및 PSPing을 설치하고, 3) 성능 테스트를 실행합니다.

1. PowerShell 모듈 설치

    ```powershell
    (new-object Net.WebClient).DownloadString("https://aka.ms/AzureCT") | Invoke-Expression
    
    ```

    이 명령은 PowerShell 모듈을 다운로드하고 로컬로 설치합니다.

2. 지원 애플리케이션 설치
    ```powershell
    Install-LinkPerformance
    ```
    이 AzureCT 명령은 "C:\ACTTools"라는 새 디렉터리에 iPerf 및 PSPing을 설치하고, 또한 ICMP 및 5201(iPerf) 트래픽 포트를 허용하기 위해 Windows 방화벽 포트를 엽니다.

3. 성능 테스트 실행

    첫째, 원격 호스트에서 iPerf를 서버 모드로 실행하고 설치해야 합니다. 또한 원격 호스트가 3389(Windows용 RDP) 또는 22(linux용 SSH)에서 수신 대기하도록 하고 5201 포트에서 iPerf에 대한 트래픽을 허용해야 합니다. 원격 호스트가 Windows인 경우 AzureCT를 설치하고 Install-LinkPerformance 명령을 실행하여 iPerf 및 iPerf를 서버 모드에서 시작하는 데 필요한 방화벽 규칙을 설정합니다. 
    
    원격 컴퓨터가 준비되면 로컬 컴퓨터에서 PowerShell을 열고 테스트를 시작합니다.
    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 10
    ```

    이 명령은 네트워크 링크의 대역폭 용량과 대기 시간을 예측할 수 있도록 일련의 동시 부하 및 대기 시간 테스트를 실행합니다.

4. 테스트 출력 검토

    PowerShell 출력 형식은 다음과 비슷합니다.

    [![4]][4]

    모든 iPerf 및 PSPing 테스트의 구체적인 결과는 "C:\ACTTools"의 AzureCT 도구 디렉터리에 있는 개별 텍스트 파일에 들어 있습니다.

## <a name="troubleshooting"></a>문제 해결
성능 테스트에서 예상된 결과를 얻지 못한 경우 점진적 단계별 프로세스여야 하는 이유를 파악해야 합니다. 경로의 구성 요소 수를 감안할 때. 일반적으로 이리저리 이동하면서 꼭 필요하지 않은 동일한 테스트를 여러 번 수행하는 것보다는 체계적 접근 방법이 좀 더 빠른 해결 경로를 제공합니다.

>[!NOTE]
>여기에 제시된 시나리오는 연결 문제가 아닌 성능 문제입니다. 트래픽이 전혀 전달되지 않는 경우 단계가 달라질 것입니다.
>
>

첫째, 가정이 올바른지 점검합니다. 예상이 합리적인가요? 예를 들어 ExpressRoute 회로가 1Gbps이고 대기 시간이 100ms인 경우 대기 시간이 긴 링크에서 TCP가 갖는 성능 특징을 고려할 때 1Gbps를 예상하는 것은 합리적이지 않습니다. 성능 가정에 대한 자세한 내용은 [참조 섹션](#references)을 참조하세요.

다음으로, 라우팅 도메인 사이의 가장자리에서 시작하여 문제를 단일 주요 라우팅 도메인(회사 네트워크, WAN 또는 Azure 네트워크)으로 격리하는 것이 좋습니다. 사람들이 경로의 "블랙 박스"를 탓하는 모습을 자주 볼 수 있습니다. 블랙 박스를 탓하기는 쉽지만 개발자가 변경할 수 있는 영역에 문제가 있는 경우에는 해결 시간이 크게 지연될 수 있습니다. 서비스 공급자 또는 ISP에 문제를 전달하기 전에 자신의의 무를 성실하게 수행해야 합니다.

문제가 있는 것으로 보이는 주요 라우팅 도메인을 식별한 후에는 의심 영역 다이어그램을 만들어야 합니다. 화이트 보드, 메모장 또는 Visio에 그린 다이어그램은 문제를 체계적 접근 방식을 통해 문제를 보다 구체적으로 격리할 수 있는 "전투 맵"을 제공합니다. 테스트 지점을 계획하고, 영역을 정리할 때 맵을 업데이트하거나 테스트가 진행될 때 보다 심층적으로 분석할 수 있습니다.

이제 다이어그램이 생겼으니, 네트워크를 여러 세그먼트로 나누고 문제 범위를 좁힙니다. 정상적으로 작동하는 곳과 그렇지 않은 곳을 확인합니다. 테스트 지점을 계속 이동하면서 문제가 있는 구성 요소까지 격리합니다.

잊지 말고 OSI 모델의 다른 레이어도 확인해야 합니다. 네트워크 및 레이어 1-3(물리적, 데이터 및 네트워크 레이어)에 초점을 맞추기 쉽지만 애플리케이션 계층의 레이어 7에 문제가 있을 가능성도 있습니다. 열린 마음으로 가정을 확인합니다.

## <a name="advanced-expressroute-troubleshooting"></a>고급 ExpressRoute 문제 해결
클라우드 에지가 실제로 어디인지 확실하지 않은 경우 Azure 구성 요소를 격리하는 것이 어려울 수 있습니다. ExpressRoute를 사용하는 경우 에지는 MSEE(Microsoft Enterprise Edge)라고 하는 네트워크 구성 요소입니다. **ExpressRoute를 사용하는 경우** MSEE는 Microsoft의 네트워크와 처음으로 접촉하는 접촉 지점이자 Microsoft 네트워크를 떠나는 마지막 홉입니다. VNet 게이트웨이와 ExpressRoute 회로 사이에 연결 개체를 만드는 것은 실제로는 MSEE에 대한 연결을 만드는 것입니다. Azure 네트워크 문제를 격리하여 문제가 Azure에 있다는 것을 증명하거나 WAN 또는 회사 네트워크로 추가 다운스트림하려면 MSEE를 첫 번째 또는 마지막 홉으로 인식(이동하는 방향에 따라)하는 것이 중요합니다. 

[![2]][2]

>[!NOTE]
> MSEE는 Azure 클라우드에 있지 않습니다. ExpressRoute는 Azure가 아닌 Microsoft 네트워크의 에지에 있습니다. ExpressRoute로 MSEE와 연결되면 Microsoft의 네트워크에 연결된 것이며, 여기서 Office 365(Microsoft 피어링을 통해) 또는 Azure(개인 및/또는 Microsoft 피어링을 통해) 같은 클라우드 서비스로 이동할 수 있습니다.
>
>

두 VNet(다이어그램의 VNet A와 B)이 **동일한** ExpressRoute 회로에 연결된 경우 일련의 테스트를 수행하여 Azure에서 문제를 격리(또는 문제가 Azure에 있지 않음을 증명)할 수 있습니다.
 
### <a name="test-plan"></a>테스트 계획
1. VM1 및 VM2 간의 Get-LinkPerformance 테스트를 실행합니다. 이 테스트는 문제가 로컬에 있는지 여부에 대한 실마리를 제공합니다. 이 테스트의 결과로 생성되는 대기 시간 및 대역폭이 용인 가능한 수준인 경우 로컬 VNet 로컬 네트워크가 양호하다고 표시할 수 있습니다.
2. 로컬 VNet 트래픽이 양호하다고 가정하고 VM1 및 VM3 간의 Get LinkPerformance 테스트를 실행합니다. 이 테스트는 Microsoft 네트워크를 거쳐 MSEE까지 갔다가 다시 Azure로 돌아오는 연결을 실행합니다. 이 테스트의 결과로 생성되는 대기 시간 및 대역폭이 용인 가능한 수준인 경우 Azure 네트워크가 양호하다고 표시할 수 있습니다.
3. Azure를 제외하면 회사 네트워크에서 비슷한 테스트 순서를 수행할 수 있습니다. 이 테스트도 잘 진행되면 서비스 공급자 또는 ISP를 작업하여 WAN 연결을 진단할 시간입니다. 예제: 두 지점 사이에서 또는 데스크와 데이터 센터 서버 사이에서 이 테스트를 실행합니다. 테스트 대상에 따라 해당 경로를 실행할 수 있는 엔드포인트(서버, PC 등)를 찾아봅니다.

>[!IMPORTANT]
> 공통 위치(개인적으로는 OneNote 또는 Excel을 선호합니다)에 각 테스트의 실행 시간을 표시하고 결과를 기록하는 것이 중요합니다. 여러 테스트 실행의 결과 데이터를 비교할 수 있고 데이터에 "허점"이 없도록 각 테스트 실행의 결과가 동일해야 합니다. 제가 문제 해결에 AzureCT를 사용하는 주된 이유는 테스트 간의 일관성입니다. *마법*은 제가 실행하는 부하 시나리오의 정확성에 있는 것이 아니라 모든 테스트에서 *일관적인 테스트 및 데이터 출력*을 얻는다는 사실에 있습니다. 특히 문제가 산발적으로 발생하는 경우 매번 시간을 기록하고 일관적인 데이터를 얻는 것이 매우 중요합니다. 성실하게 데이터를 수집하면 동일한 시나리오를 다시 테스트하는 시간을 줄일 수 있습니다(저는 이 사실을 몇 년 전에 어렵게 배웠습니다).
>
>

## <a name="the-problem-is-isolated-now-what"></a>문제를 격리한 후에는 어떻게 해야 할까요?
문제를 보다 철저하게 격리할수록 보다 쉽게 문제를 해결할 수 있지만, 문제를 좀 더 심층적으로 살펴볼 수 없는 경우가 종종 있습니다. 예: 서비스 공급자의 링크가 유럽을 통해 홉을 사용하고 있는데, 여러분이 예상하는 경로는 모두 아시아에 있습니다. 이 경우 도움을 요청해야 합니다. 누구에게 도움을 요청해야 하는지는 문제를 격리한 라우팅 도메인에 따라 다르며, 문제의 범위를 특정 구성 요소로 좁힐 수 있다면 더욱 좋습니다.

회사 네트워크 문제인 경우 내부 IT 부서 또는 네트워크를 지원하는 서비스 공급자(하드웨어 제조업체일 수도 있음)가 디바이스 구성 또는 하드웨어 복구를 도와줄 수 있습니다.

WAN 문제인 경우 서비스 공급자 또는 ISP와 테스트 결과를 공유하면 그들이 바로 문제 해결을 시작하고 여러분이 이미 테스트한 내용을 다시 살펴보는 번거로움을 피할 수 있습니다. 하지만 여러분이 테스트한 결과를 그들이 다시 확인하려 하더라도 불쾌하게 생각하지는 마세요. 타인이 보고한 결과를 토대로 문제를 해결할 때에는 "신뢰하되 검증하라"는 모토를 잊어서는 안 됩니다.

Azure에서 문제를 최대한 구체적으로 격리한 후에는 [Azure 네트워크 설명서][Network Docs]를 검토하고 여전히 [지원 티켓을 열어야][Ticket Link] 하는지 결정해야 합니다.

## <a name="references"></a>참조
### <a name="latencybandwidth-expectations"></a>대기 시간/대역폭 예상치
>[!TIP]
> 개발자가 테스트하는 엔드포인트 간 지리적 대기 시간(마일 또는 킬로미터)은 대기 시간을 구성하는 요소 중 단연코 가장 큰 구성 요소입니다. 장비 대기 시간(실제 및 가상 구성 요소, 홉 수 등)도 있지만 WAN 연결에서 전체 대기 시간의 구성 요소 중 가장 큰 비율을 차지하는 것은 지리적 대기 시간이라는 것이 증명되었습니다. 또한 거리는 직선 또는 로드맵 거리가 아닌 파이버 실행의 거리라는 것을 이해하는 것이 중요합니다. 이 거리의 정확한 값을 구하기는 매우 어렵습니다. 그래서 저는 보통 인터넷의 도시 거리 계산기를 사용하는데, 이 방법이 매우 부정확하다는 것을 알지만 일반적인 예상치를 설정하기에는 충분합니다.
>
>

저는 미국 워싱턴주 시애틀에 ExpressRoute를 설정했습니다. 다음 표는 다양한 Azure 위치를 테스트한 대기 시간 및 대역폭을 보여 줍니다. 저는 각 테스트가 종료되는 사이의 지리적 거리를 예상했습니다.

테스트 설정:
 - Windows Server 2016을 실행하고 10Gbps NIC를 사용하고 ExpressRoute 회로에 연결된 물리적 서버.
 - 개인 피어링을 사용하도록 설정된 식별된 위치의 10Gbps Premium ExpressRoute 회로.
 - 지정된 지역의 UltraPerformance 게이트웨이를 사용하는 Azure VNet.
 - VNet에서 Windows Server 2016을 실행하는 DS5v2 VM. VM은 도메인에 가입되지 않았으며, AzureCT가 설치된 기본 Azure 이미지(최적화 또는 사용자 지정 없는)를 사용하여 빌드되었습니다.
 - 6회의 테스트가 실행되었으며, 테스트마다 AzureCT Get-LinkPerformance 명령을 사용하여 5분 부하 테스트를 수행했습니다. 예를 들면 다음과 같습니다.

    ```powershell
    Get-LinkPerformance -RemoteHost 10.0.0.1 -TestSeconds 300
    ```
 - 각 테스트의 데이터 흐름에는 온-프레미스 실제 서버(시애틀의 iPerf 클라이언트)에서 Azure VM(나열된 Azure 지역의 iPerf 서버)까지 흐르는 부하가 포함되어 있습니다.
 - "대기 시간" 열 데이터는 무부하 테스트(iPerf를 실행하지 않고 TCP 대기 시간 테스트)에서 얻습니다.
 - "최대 대역폭" 열 데이터는 1Mb 창 크기의 16 TCP 흐름 부하 테스트에서 얻습니다.

[![3]][3]

### <a name="latencybandwidth-results"></a>대기 시간/대역폭 결과
>[!IMPORTANT]
> 이러한 숫자는 일반 참조용일 뿐입니다. 다양한 요인이 대기 시간에 영향을 미치며, 이러한 값은 시간이 흘러도 대체적으로 일관적이지만 Azure 또는 서비스 공급자 네트워크 내의 조건에서 언제든지 다른 경로를 통해 트래픽을 보낼 수 있으므로 대기 시간과 대역폭이 영향을 받을 수 있습니다. 일반적으로 이러한 변경의 영향이 큰 차이로 이어지지는 않습니다.
>
>

| | | | | | |
|-|-|-|-|-|-|
|ExpressRoute<br/>위치|Azure<br/>지역|예상<br/>거리(km)|대기 시간|1 세션<br/>대역폭|최대<br/>대역폭|
| 시애틀 | 미국 서부 2        |    191km |   5ms | 262.0Mbits/sec |  3.74Gbits/sec |
| 시애틀 | 미국 서부          |  1,094km |  18ms |  82.3Mbits/sec |  3.70Gbits/sec |
| 시애틀 | 미국 중부       |  2,357km |  40ms |  38.8Mbits/sec |  2.55Gbits/sec |
| 시애틀 | 미국 중남부 |  2,877km |  51ms |  30.6Mbits/sec |  2.49Gbits/sec |
| 시애틀 | 미국 중북부 |  2,792km |  55ms |  27.7Mbits/sec |  2.19Gbits/sec |
| 시애틀 | 미국 동부 2        |  3,769km |  73ms |  21.3Mbits/sec |  1.79Gbits/sec |
| 시애틀 | 미국 동부          |  3,699km |  74ms |  21.1Mbits/sec |  1.78Gbits/sec |
| 시애틀 | 일본 동부       |  7,705km | 106ms |  14.6Mbits/sec |  1.22Gbits/sec |
| 시애틀 | 영국 남부         |  7,708km | 146ms |  10.6Mbits/sec |   896Mbits/sec |
| 시애틀 | 서유럽      |  7,834km | 153ms |  10.2Mbits/sec |   761Mbits/sec |
| 시애틀 | 오스트레일리아 동부   | 12,484km | 165ms |   9.4Mbits/sec |   794Mbits/sec |
| 시애틀 | 동남아시아   | 12,989km | 170ms |   9.2Mbits/sec |   756Mbits/sec |
| 시애틀 | 브라질 남부 *   | 10,930km | 189ms |   8.2Mbits/sec |   699Mbits/sec |
| 시애틀 | 인도 남부      | 12,918km | 202ms |   7.7Mbits/sec |   634Mbits/sec |

\* 브라질까지의 대기 시간은 직선 거리가 파이버 실행 거리와 크게 다르다는 것을 보여 주는 좋은 예입니다. 저는 대기 시간이 160ms 근처일 것으로 예상했지만 실제로는 189ms입니다. 어딘가에 네트워크 문제가 있어서 제 예상과 다른 결과가 나올 수도 있지만, 대부분은 파이버 실행이 브라질까지 직선으로 가는 것이 아니라 시애틀에서 브라질까지 이동할 때 약 1,000km를 더 이동하기 때문입니다.

## <a name="next-steps"></a>다음 단계
1. [https://aka.ms/AzCT][ACT]의 GitHub에서 Azure 연결 도구 키트 다운로드
2. [링크 성능 테스트][Performance Doc]에 대한 지침 수행

<!--Image References-->
[1]: ./media/expressroute-troubleshooting-network-performance/network-components.png "Azure 네트워크 구성 요소"
[2]: ./media/expressroute-troubleshooting-network-performance/expressroute-troubleshooting.png "ExpressRoute 문제 해결"
[3]: ./media/expressroute-troubleshooting-network-performance/test-diagram.png "성능 테스트 환경"
[4]: ./media/expressroute-troubleshooting-network-performance/powershell-output.png "PowerShell 출력"

<!--Link References-->
[Performance Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/PerformanceTesting.md
[Availability Doc]: https://github.com/Azure/NetworkMonitoring/blob/master/AzureCT/AvailabilityTesting.md
[Network Docs]: https://docs.microsoft.com/azure/index
[Ticket Link]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview
[ACT]: https://aka.ms/AzCT
