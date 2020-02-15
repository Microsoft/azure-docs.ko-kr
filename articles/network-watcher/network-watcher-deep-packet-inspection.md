---
title: Azure Network Watcher로 패킷 검사 | Microsoft Docs
description: 이 문서에서는 Network Watcher를 사용하여 VM에서 수집한 패킷에 심도 있는 검사를 수행하는 방법을 설명합니다.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7d32043ca73e9cf810b3eab5e65cb4b42b599d18
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152927"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Azure Network Watcher로 패킷 검사

Network Watcher의 패킷 캡처 기능을 사용하여 포털, PowerShell, CLI, SDK와 REST API를 통해 프로그래밍 방식으로 Azure VM에서 캡처 세션을 시작 및 관리할 수 있습니다. 패킷 캡처를 통해 쉽게 사용 가능한 형식으로 정보를 제공하여 패킷 수준 데이터가 필요한 시나리오를 해결할 수 있습니다. 데이터 검사에 무료로 제공되는 도구를 활용하여 VM 간에 전송되는 통신을 검토하고 네트워크 트래픽에 대한 정보를 얻을 수 있습니다. 패킷 캡처 데이터를 사용하는 예로는, 네트워크 또는 애플리케이션 문제 조사, 네트워크 악용 및 침입 시도 감지 또는 규정 준수 유지 관리가 있습니다. 이 문서에서는 널리 사용되는 오픈 소스 도구를 사용하여 Network Watcher에서 제공하는 패킷 캡처 파일을 여는 방법을 보여 줍니다. 연결 대기 시간 계산, 비정상적인 트래픽 식별 및 네트워킹 통계를 검사하는 방법을 보여 주는 예제도 제공합니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에서는 이전에 실행했던 패킷 캡처에 대해 미리 구성된 몇 가지 시나리오를 안내합니다. 이러한 시나리오는 패킷 캡처를 검토하여 액세스할 수 있는 기능을 보여 줍니다. 이 시나리오에서는 [WireShark](https://www.wireshark.org/)를 사용하여 패킷 캡처를 검사합니다.

이 시나리오에서는 가상 머신에서 패킷 캡처를 이미 실행했다고 가정합니다. 패킷 캡처를 만드는 방법에 대해 알아보려면 [포털에서 패킷 캡처 관리](network-watcher-packet-capture-manage-portal.md)(영문)를 방문하거나 [REST API로 패킷 캡처 관리](network-watcher-packet-capture-manage-rest.md)(영문)를 방문하여 REST를 사용하세요.

## <a name="scenario"></a>시나리오

이 시나리오에서는 다음을 수행합니다.

* 패킷 캡처 검토

## <a name="calculate-network-latency"></a>네트워크 대기 시간 계산

이 시나리오에서는 두 엔드포인트 사이에 발생하는 TCP(전송 제어 프로토콜) 대화의 초기 RTT(왕복 시간)를 확인하는 방법을 보여 줍니다.

TCP 연결이 설정되면 연결에 전송된 처음 3개 패킷은 일반적으로 세 방향 핸드셰이크라고 하는 패턴을 따릅니다. 이 핸드셰이크에 전송된 처음 두 패킷과 클라이언트로부터의 초기 요청 및 서버에서의 응답을 검사하여 연결이 설정되었을 때의 대기 시간을 계산할 수 있습니다. 이 대기 시간을 RTT(왕복 시간)라고 합니다. TCP 프로토콜 및 세 방향 핸드셰이크에 대한 자세한 내용은 다음 리소스를 참조하세요. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>1단계

WireShark를 시작합니다.

### <a name="step-2"></a>2단계

패킷 캡처에서 **.cap** 파일을 로드합니다. 이 파일은 구성 방식에 따라, 가상 머신에 로컬로 저장된 BLOB에서 찾을 수 있습니다.

### <a name="step-3"></a>3단계

TCP 대화에서 초기 RTT(왕복 시간)를 보려면 TCP 핸드셰이크에 관련된 처음 두 패킷만 확인합니다. 세 방향 핸드셰이크에서 처음 두 패킷([SYN], [SYN, ACK] 패킷)을 사용할 것입니다. TCP 헤더에 설정된 플래그에 따라 이름이 지정되었습니다. 핸드셰이크에서 마지막 패킷인 [ACK] 패킷은 이 시나리오에서는 사용하지 않습니다. 클라이언트에 의해 [SYN] 패킷이 전송됩니다. 수신 되 면 서버는 클라이언트에서 SYN을 수신 하는 승인으로 [ACK] 패킷을 보냅니다. 서버의 응답에는 오버헤드가 거의 필요하지 않다는 사실에 따라 [SYN, ACK] 패킷이 클라이언트에 수신된 시간에서 클라이언트가 [SYN] 패킷을 전송한 시간을 뺀 값으로 RTT를 계산합니다.

WireShark를 사용하여 이 값을 계산합니다.

TCP 세 방향 핸드셰이크에서 처음 두 패킷을 보다 쉽게 보려면 WireShark에서 제공한 필터링 기능을 활용합니다.

WireShark에서 필터를 적용하려면 캡처에서 [SYN] 패킷의 "전송 제어 프로토콜" 세그먼트를 확장하고 TCP 헤더에 설정된 플래그를 검사합니다.

모든 [SYN] 및 [SYN, ACK] 패킷에서 필터링을 확인 중이므로 플래그 아래에서 동기화 비트가 1로 설정되었는지 확인하고, 동기화 비트를 마우스 오른쪽 단추로 클릭한 다음, -> Apply as Filter(필터로 적용) -> Selected(선택됨)를 선택합니다.

![그림 7][7]

### <a name="step-4"></a>4단계

이제 [SYN] 비트가 설정된 패킷만 표시되도록 창을 필터링했으며 초기 RTT를 보려는 대화를 쉽게 선택할 수 있습니다. WireShark에서 RTT를 보는 간단한 방법은 "SEQ/ACK" 분석으로 표시된 드롭다운을 클릭하는 것입니다. 그러면 RTT가 표시됩니다. 이 경우 RTT는 0.0022114초 또는 2.211ms입니다.

![그림 8][8]

## <a name="unwanted-protocols"></a>원치 않는 프로토콜

Azure에 배포한 가상 머신 인스턴스에 많은 애플리케이션이 실행 중일 수 있습니다. 이러한 많은 애플리케이션은 명시적인 사용 권한 없이 네트워크를 통해 통신할 것입니다. 패킷 캡처를 사용하여 네트워크 통신을 저장하면 애플리케이션이 네트워크에서 어떻게 통신하는지 조사하고 문제를 찾을 수 있습니다.

이 예에서는 머신에서 실행 중인 애플리케이션에서 무단 통신을 나타낼 수 있는 원치 않는 프로토콜에 대해 이전에 실행된 패킷 캡처를 검토합니다.

### <a name="step-1"></a>1단계

이전 시나리오의 동일한 캡처를 사용하고 **통계** > **프로토콜 계층**을 클릭합니다.

![프로토콜 계층 메뉴][2]

프로토콜 계층 창이 나타납니다. 이 보기는 캡처 세션 중에 사용되었던 모든 프로토콜 목록과 해당 프로토콜을 사용하여 전송 및 수신된 패킷 수를 제공합니다. 이 보기는 가상 머신 또는 네트워크에서 원치 않는 네트워크 트래픽을 찾는 데 유용할 수 있습니다.

![프로토콜 계층 열림][3]

다음 화면 캡처에서 볼 수 있는 것처럼 피어 투 피어 파일 공유에 사용되는 BitTorrent 프로토콜을 사용하는 트래픽이 있습니다. 관리자는 이 특정 가상 머신에 BitTorrent 트래픽이 표시될 것이라고 예상하지 않습니다. 이제 이 트래픽을 인식하고 가상 머신에 설치된 피어 투 피어 소프트웨어를 제거하거나 네트워크 보안 그룹 또는 방화벽을 사용하여 트래픽을 차단할 수 있습니다. 또는 일정에 따라 패킷 캡처를 실행하도록 선택하여 가상 머신에서 정기적으로 프로토콜 사용을 검토할 수 있습니다. azure에서 네트워크 작업을 자동화하는 방법에 대한 예는 [Azure Automation으로 네트워크 리소스 모니터링](network-watcher-monitor-with-azure-automation.md)(영문)을 참조하세요.

## <a name="finding-top-destinations-and-ports"></a>상위 대상 및 포트 찾기

네트워크에서 애플리케이션 및 리소스 문제를 모니터링하거나 해결할 때는 트래픽 형식, 엔드포인트 및 통신이 이루어지는 포트를 이해하는 것이 중요합니다. 위의 패킷 캡처 파일을 활용하면 VM이 통신하는 상위 대상 및 활용하는 포트를 신속하게 파악할 수 있습니다.

### <a name="step-1"></a>1단계

이전 시나리오의 동일한 캡처를 사용하고 **통계** > **IPv4 통계** > **대상 및 포트**를 클릭합니다.

![패킷 캡처 창][4]

### <a name="step-2"></a>2단계

강조 표시된 줄의 결과를 살펴보면 포트 111에서 여러 연결이 있습니다. 가장 많은 사용된 포트는 원격 데스크톱인 3389이며 나머지는 RPC 동적 포트입니다.

이 트래픽은 아무 것도 의미하지 않을 수 있지만 많은 연결에 사용된 포트이며 관리자는 알 수 없습니다.

![그림 5][5]

### <a name="step-3"></a>3단계

이제 부적절한 포트를 확인했으며 포트에 따라 캡처를 필터링할 수 있습니다.

이 시나리오의 필터는 다음과 같습니다.

```
tcp.port == 111
```

위의 필터 텍스트를 필터 텍스트 상자에 입력하고 Enter 키를 누릅니다.

![그림 6][6]

결과에서 모든 트래픽을 동일한 서브넷의 로컬 가상 컴퓨터에서 가져오는 것을 확인할 수 있습니다. 이 트래픽이 발생하는 이유가 여전히 이해되지 않는다면 패킷을 더 검사하여 포트 111에서 이러한 호출이 이루어지는 이유를 확인할 수 있습니다. 이 정보를 통해 적절한 조치를 취할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Azure 네트워크 모니터링 개요](network-watcher-monitoring-overview.md)(영문)를 방문하여 Network Watcher의 다른 진단 기능에 대해 알아보세요.

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













