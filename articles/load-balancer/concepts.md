---
title: Azure Load Balancer 개념
description: Azure Load Balancer 개념에 대한 개요입니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 96b91c08e0bf9a176af2ed844cd63a2cfa1e233d
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589642"
---
# <a name="azure-load-balancer-concepts"></a>Azure Load Balancer 개념

부하 분산 장치는 몇 가지 기능을 UDP 및 TCP 애플리케이션 모두에 제공합니다. 

## <a name="load-balancing-algorithm"></a>부하 분산 알고리즘
트래픽을 프런트 엔드에서 백 엔드 풀로 분산시키는 부하 분산 규칙을 만들 수 있습니다. Azure Load Balancer는 인바운드 흐름(바이트 아님)을 배포하는 데 해싱 알고리즘을 사용합니다. 부하 분산 장치는 흐름의 헤더를 백 엔드 풀 인스턴스에 다시 작성합니다. 상태 프로브가 정상 백 엔드 엔드포인트을 나타내면 서버에서 새 흐름을 받을 수 있습니다.

기본적으로 부하 분산 장치는 5 튜플 해시를 사용합니다.

해시에는 다음이 포함됩니다.

- **원본 IP 주소**
- **원본 포트**
- **대상 IP 주소**
- **대상 포트**
- **사용 가능한 서버에 흐름을 매핑하기 위한 IP 프로토콜 번호**

원본 IP 주소에 대한 선호도는 2 튜플 또는 3 튜플 해시를 사용하여 만들어집니다. 동일한 흐름의 패킷은 부하 분산된 프런트 엔드 내의 동일한 인스턴스에 도착합니다. 

클라이언트에서 동일한 원본 IP의 새 흐름을 시작하면 원본 포트가 변경됩니다. 결과적으로 5 튜플 해시로 인해 트래픽이 다른 백 엔드 엔드포인트로 이동할 수 있습니다.
자세한 내용은 [Azure Load Balancer의 배포 모드 구성](./load-balancer-distribution-mode.md)을 참조하세요.

다음 이미지는 해시 기반 배포를 표시합니다.

![해시 기반 배포](./media/load-balancer-overview/load-balancer-distribution.png)

*그림: 해시 기반 배포*

## <a name="application-independence-and-transparency"></a>애플리케이션 독립성 및 투명성

부하 분산 장치는 TCP, UDP 또는 애플리케이션 계층과 직접 상호 작용하지 않습니다. 모든 TCP 또는 UDP 애플리케이션 시나리오를 지원할 수 있습니다. 부하 분산 장치는 흐름을 닫거나 시작하거나 흐름의 페이로드와 상호 작용하지 않습니다. 부하 분산 장치는 애플리케이션 계층 게이트웨이 기능을 제공하지 않습니다. 프로토콜 핸드셰이크는 항상 클라이언트와 백 엔드 풀 인스턴스 사이에서 직접 발생합니다. 인바운드 흐름에 대한 응답은 항상 가상 머신의 응답입니다. 흐름이 가상 머신에 도착하면 원래 원본 IP 주소도 유지됩니다.

* 모든 엔드포인트는 VM을 통해 응답합니다. 예를 들어 TCP 핸드셰이크는 클라이언트와 선택한 백 엔드 VM 간에 발생합니다. 프런트 엔드에 요청에 대한 응답은 백 엔드 VM에서 생성한 응답입니다. 프런트 엔드에 대한 연결의 유효성을 성공적으로 검사하는 경우 하나 이상의 백 엔드 가상 머신에 대한 전체 연결의 유효성을 검사하게 됩니다.
* 애플리케이션 페이로드는 부하 분산 장치에 투명합니다. 모든 UDP 또는 TCP 애플리케이션을 지원할 수 있습니다.
* 부하 분산 장치는 TCP 페이로드와 상호 작용하지 않고 TLS 오프로드를 제공하므로 포괄적인 암호화 시나리오를 구축할 수 있습니다. 부하 분산 장치를 사용하면 VM 자체에서 TLS 연결을 종료하여 TLS 애플리케이션에 대한 대규모 확장이 구현됩니다. 예를 들어 TLS 세션 키 용량은 백 엔드 풀에 추가하는 VM의 수와 유형으로만 제한됩니다.

## <a name="outbound-connections"></a>아웃바운드 연결 

백 엔드 풀에서 공용 IP로의 흐름이 프런트 엔드에 매핑됩니다. Azure는 부하 분산 아웃바운드 규칙을 통해 아웃바운드 연결을 공용 프런트 엔드 IP 주소로 변환합니다. 이 구성의 장점은 다음과 같습니다. 프런트 엔드를 서비스의 다른 인스턴스에 동적으로 매핑할 수 있으므로 서비스의 업그레이드 및 재해 복구가 용이합니다. ACL(액세스 제어 목록)을 보다 쉽게 관리할 수 있습니다. 프런트 엔드 IP로 표현되는 ACL은 서비스를 확장 또는 축소하거나 다시 배포해도 변경되지 않습니다. 아웃바운드 연결을 컴퓨터보다 더 적은 수의 IP 주소로 변환하면 수신 허용 - 받는 사람 목록을 구현해야 한다느 부담을 줄일 수 있습니다. SNAT(원본 네트워크 주소 변환) 및 Azure Load Balancer에 대한 자세한 내용은 [SNAT 및 Azure Load Balancer](load-balancer-outbound-connections.md)를 참조하세요.

## <a name="availability-zones"></a>가용성 영역 

표준 부하 분산 장치는 가용성 영역을 사용할 수 있는 지역에서 추가 기능을 지원합니다. 가용성 영역 구성은 퍼블릭 및 내부 표준 Standard Load Balancer 모두에 사용할 수 있습니다. 영역 중복 프런트 엔드는 모든 영역에서 전용 인프라를 동시에 사용하여 영역 실패에 대비합니다. 또한 특정 영역에 대한 프런트 엔드도 보장할 수 있습니다. 영역 프런트 엔드는 단일 영역의 전용 인프라를 통해 제공됩니다. 영역 간 부하 분산은 백 엔드 풀에 사용할 수 있습니다. 가상 네트워크의 모든 가상 머신 리소스는 백 엔드 풀에 포함될 수 있습니다. 기본 부하 분산 장치는 영역을 지원하지 않습니다. 자세한 내용은 [가용성 영역 관련 기능에 대한 자세한 설명](load-balancer-standard-availability-zones.md) 및 [가용성 영역 개요](../availability-zones/az-overview.md)를 참조하세요.

## <a name="ha-ports"></a>HA 포트

애플리케이션 크기를 조정하고 안정성을 높이기 위해 HA 포트 부하 분산 규칙을 구성할 수 있습니다. 내부 부하 분산 장치 프런트 엔드 IP의 수명이 짧은 포트에 대한 흐름당 부하 분산은 이러한 규칙을 통해 제공됩니다. 이 기능은 개별 포트를 지정하는 것이 실용적이지 않거나 바람직하지 않은 경우에 유용합니다. HA 포트 규칙을 사용하면 활성-수동 또는 활성-활성 n+1 시나리오를 만들 수 있습니다. 이러한 시나리오는 광범위한 인바운드 포트가 필요한 네트워크 가상 어플라이언스와 모든 애플리케이션을 위한 것입니다. 상태 프로브를 사용하여 새 흐름을 받아야 하는 백 엔드를 결정할 수 있습니다.  네트워크 보안 그룹을 사용하여 포트 범위 시나리오를 에뮬레이트할 수 있습니다. 기본 부하 분산 장치는 HA 포트를 지원하지 않습니다. [HA 포트에 대한 자세한 논의](load-balancer-ha-ports-overview.md)를 검토하세요.

## <a name="multiple-frontends"></a>여러 프런트 엔드 

부하 분산 장치는 여러 프런트 엔드를 사용하는 여러 규칙을 지원합니다.  표준 Load Balancer는 이 기능을 아웃바운드 시나리오로 확장합니다. 아웃바운드 규칙은 인바운드 규칙과 반대입니다. 아웃바운드 규칙은 아웃바운드 연결에 대한 연결을 만듭니다. 표준 부하 분산 장치는 부하 분산 규칙을 통해 가상 머신 리소스와 연결된 모든 프런트 엔드를 사용합니다. 또한 부하 분산 규칙에 대한 매개 변수를 사용하면 아웃바운드 연결을 위한 부하 분산 규칙을 사용하지 않도록 설정할 수 있습니다. 이를 통해 없음을 포함하여 특정 프런트 엔드를 선택할 수 있습니다. 비교를 위해 기본 부하 분산 장치는 단일 프런트 엔드를 임의로 선택합니다. 선택한 프런트 엔드를 제어할 수 있는 기능이 없습니다.

## <a name="floating-ip"></a>부동 IP

일부 애플리케이션 시나리오는 백 엔드 풀의 단일 VM에서 여러 애플리케이션 인스턴스가 동일한 포트를 사용하는 것을 선호하거나 필요로 합니다. 포트 재사용의 일반적인 예에는 고가용성을 위한 클러스터링, 네트워크 가상 어플라이언스 및 재암호화 없이 다중 TLS 엔드포인트 노출이 포함됩니다. 여러 규칙 전반에 백 엔드 포트를 재사용하려면 규칙 정의에 부동 IP를 사용하도록 설정해야 합니다.

**부동 IP**는 DSR(Direct Server Return)로 알려진 부분에 대한 Azure의 용어입니다. DSR은 다음과 같은 두 부분으로 구성됩니다. 

- 흐름 토폴로지
- IP 주소 매핑 구성표

플랫폼 수준에서 Azure Load Balancer는 부동 IP 사용 여부에 관계 없이 DSR 흐름 토폴로지에서 항상 작동합니다. 이는 아웃바운드 부분의 흐름이 다시 원점으로 직접 흘러가도록 언제나 올바르게 다시 작성되는 것을 의미합니다.
부동 IP가 없으면 Azure는 사용 편의성을 위해 기존의 부하 분산 IP 주소 매핑 체계(VM 인스턴스의 IP)를 제공합니다. 부동 IP를 사용하도록 설정하면 Load Balancer의 프런트 엔드 IP에 매핑되는 IP 주소가 변경되어 추가적인 유연성이 제공됩니다. [여기](load-balancer-multivip-overview.md)를 참조하세요.


## <a name="limitations"></a><a name = "limitations"></a>제한 사항

- 부동 IP는 현재 Load Balancing 시나리오의 보조 IP 구성에서 지원되지 않습니다.

- 부하 분산 장치 규칙은 두 개의 가상 네트워크에 걸쳐 있을 수 없습니다.  프런트 엔드 및 해당 백 엔드 인스턴스는 동일한 가상 네트워크에 있어야 합니다.  

- 가상 네트워크 및 기타 Microsoft 플랫폼 서비스를 사용하지 않는 웹 작업자 역할은 표준 내부 부하 분산 장치 내의 인스턴스에서만 액세스할 수 있습니다. 각 서비스 자체 또는 기본 플랫폼이 예고 없이 변경될 수 있으므로 이 접근성은 사용하지 마세요. 표준 내부 부하 분산 장치를 사용할 때 아웃바운드 연결이 필요한 경우 [아웃바운드 연결](load-balancer-outbound-connections.md)을 구성해야 합니다.

- 부하 분산 장치는 특정 TCP 또는 UDP 프로토콜에 대한 부하 분산 및 포트 전달을 제공합니다. 부하 분산 규칙 및 인바운드 NAT 규칙은 TCP 및 UDP를 지원하지만 ICMP를 비롯한 다른 IP 프로토콜은 지원하지 않습니다.

- 백 엔드 VM에서 내부 Load Balancer의 프런트 엔드로의 아웃바운드 흐름이 실패합니다.

- 부하 분산 규칙에서는 IP 조각 전달이 지원되지 않습니다. UDP 및 TCP 패킷의 IP 조각화는 부하 분산 규칙에서 지원되지 않습니다. HA 포트 부하 분산 규칙을 사용하여 기존 IP 조각을 전달할 수 있습니다. 자세한 내용은 [고가용성 포트 개요](load-balancer-ha-ports-overview.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Load Balancer를 사용하여 시작하려면 [공용 표준 Load Balancer 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요. 하나의 부하 분산 장치를 만들고, 사용자 지정 IIS 확장이 설치된 VM을 만들고, VM 간에 웹앱의 부하를 분산시킵니다.
- [Azure Load Balancer 아웃바운드 연결](load-balancer-outbound-connections.md)에 대해 자세히 알아보세요.
- [Azure Load Balancer에 대해 자세히 알아보세요](load-balancer-overview.md).
- [상태 프로브](load-balancer-custom-probe-overview.md)에 대해 자세히 알아보세요.
- [표준 Load Balancer 진단](load-balancer-standard-diagnostics.md)에 대해 자세히 알아보세요.
- [네트워크 보안 그룹](../virtual-network/security-overview.md)에 대해 자세히 알아보세요.
