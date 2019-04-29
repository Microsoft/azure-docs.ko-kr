---
title: Azure Load Balancer의 다중 프런트 엔드
titlesuffix: Azure Load Balancer
description: Azure Load Balancer의 다중 프런트 엔드에 대한 개요
services: load-balancer
documentationcenter: na
author: chkuhtz
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2018
ms.author: chkuhtz
ms.openlocfilehash: b9a140314b8eba6386c37bdbcf2bb3de58589335
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60594167"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Azure Load Balancer의 다중 프런트 엔드

Azure Load Balancer를 사용해 여러 포트, 여러 IP 주소 또는 둘 다에서 부하 분산 서비스를 할 수 있습니다. 공용 및 내부 부하 분산 장치 정의를 VM 집합 전반에 대한 부하 분산 흐름에 사용할 수 있습니다.

이 문서에서는 이 기능, 중요한 개념 및 제약 조건에 대한 기본 사항을 설명합니다. 서비스를 하나의 IP 주소에 노출만 하려는 경우 [공용](load-balancer-get-started-internet-portal.md) 또는 [내부](load-balancer-get-started-ilb-arm-portal.md) 부하 분산 장치 구성에 대한 단순화된 지침을 찾을 수 있습니다. 여러 프런트 엔드를 추가하면 단일 프런트 엔드 구성이 증가합니다. 이 문서의 개념을 사용하여 단순화된 구성을 언제라도 확장할 수 있습니다.

Azure Load Balancer를 정의할 때 프런트 엔드 및 백 엔드 풀 구성이 규칙과 연결됩니다. 규칙에서 참조하는 상태 프로브는 새로운 흐름을 백 엔드 풀의 노드로 전송하는 방법을 결정하는 데 사용됩니다. 프런트 엔드(일명 VIP)는 IP 주소(공용 또는 내부), 전송 프로토콜(UDP 또는 TCP) 및 부하 분산 규칙의 포트 번호로 구성된 3 튜플로 정의됩니다. 백 엔드 풀은 부하 분산 장치 백 엔드 풀을 참조하는 가상 머신 IP 구성(NIC 리소스의 일부)의 컬렉션입니다.

다음 표에는 프런트 엔드 구성의 몇 가지 예가 들어 있습니다.

| 프런트 엔드 | IP 주소 | protocol | 포트 |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

표에 다른 네 개의 프런트엔드가 나옵니다. 프런트 엔드 #1, #2 및 #3은 여러 규칙이 있는 단일 프런트 엔드입니다. 같은 IP 주소가 사용되지만 포트 또는 프로토콜은 각 프런트 엔드마다 다릅니다. 프런트 엔드 #1과 #4번은 다중 프런트 엔드의 한 예로 동일한 프런트 엔드 프로토콜 및 포트가 여러 프런트 엔드에서 다시 사용됩니다.

Azure Load Balancer는 부하 분산 규칙을 정의하는 데 유연성을 제공합니다. 규칙은 프런트 엔드의 주소 및 포트가 백 엔드의 대상 주소 및 포트에 매핑되는 방식을 선언합니다. 백 엔드 포트를 규칙 전반에 재사용할지 여부는 규칙 유형에 따라 달라집니다. 각 규칙 유형에는 호스트 구성 및 프로브 디자인에 영향을 줄 수 있는 특정 요구 사항이 있습니다. 다음과 같은 두 가지 규칙 유형이 있습니다.

1. 백 엔드 포트 재사용이 없는 기본 규칙
2. 백 엔드 포트가 재사용되는 부동 IP 규칙

Azure Load Balancer를 사용하면 동일한 부하 분산 장치 구성에서 두 규칙 유형을 혼합할 수 있습니다. 부하 분산 장치는 규칙의 제약 조건을 준수하는 한 주어진 VM이나 어떠한 조합에 대해서도 그것들을 동시에 사용할 수 있습니다. 어떤 규칙 유형을 선택하는지는 애플리케이션의 요구 사항 및 해당 구성을 지원하는 복잡성에 따라 달라집니다. 어떤 규칙 유형이 시나리오에 가장 적합한지 평가해야 합니다.

기본 동작을 시작으로 이러한 시나리오를 더 자세히 알아봅니다.

## <a name="rule-type-1-no-backend-port-reuse"></a>규칙 유형 #1: 백 엔드 포트 재사용하지 않음

![녹색과 자주색 프런트 엔드가 포함된 다중 프런트 엔드 설명](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

이 시나리오에서 프런트 엔드는 다음과 같이 구성됩니다.

| 프런트 엔드 | IP 주소 | protocol | 포트 |
| --- | --- | --- | --- |
| ![녹색 프런트 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![자주색 프런트 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

DIP는 인바운드 흐름의 대상입니다. 백 엔드 풀에서 각 VM은 DIP의 고유한 포트에 원하는 서비스를 노출합니다. 이 서비스는 규칙 정의 통해 프런트 엔드와 연결됩니다.

두 가지 규칙을 정의합니다.

| 규칙 | 맵 프론트 엔드 | 백 엔드 풀에 |
| --- | --- | --- |
| 1 |![녹색 프런트 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 프런트 엔드1:80 |![백 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![백 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-green.png)  DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 프런트 엔드2:80 |![백 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![백 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png)  DIP2:81 |

Azure Load Balancer에서 전체 매핑은 이제 다음과 같습니다.

| 규칙 | 프런트 엔드 IP 주소 | protocol | 포트 | 대상 | 포트 |
| --- | --- | --- | --- | --- | --- |
| ![녹색 규칙](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP IP 주소 |80 |
| ![자주색 규칙](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP IP 주소 |81 |

각 규칙은 대상 IP 주소 및 대상 포트의 고유한 조합으로 흐름을 생성해야 합니다. 흐름의 대상 포트를 변경함으로써 여러 규칙이 다른 포트의 동일한 DIP로 흐름을 전달할 수 있습니다.

상태 프로브는 언제나 VM의 DIP에 전달됩니다. 프로브가 VM의 상태를 반영하는지 확인해야 합니다.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>규칙 유형 #2: 부동 IP를 사용하여 백엔드 포트 재사용

Azure Load Balancer는 사용된 규칙 유형에 관계없이 여러 프런트 엔드 전반에 걸쳐 프런트 엔드 포트를 재사용할 수 있는 유연성을 제공합니다. 또한 일부 애플리케이션 시나리오는 백 엔드 풀의 단일 VM에서 여러 애플리케이션 인스턴스가 동일한 포트를 사용하는 것을 선호하거나 필요로 합니다. 포트 재사용의 일반적인 예에는 고가용성을 위한 클러스터링, 네트워크 가상 어플라이언스 및 재암호화 없이 다중 TLS 엔드포인트 노출이 포함됩니다.

여러 규칙 전반에 백 엔드 포트를 재사용하려면 규칙 정의에 부동 IP를 사용하도록 설정해야 합니다.

"부동 IP"는 DSR(Direct Server Return)로 알려진 부분에 대한 Azure의 용어입니다. DSR는 흐름 토폴로지와 IP 주소 매핑 구성표라는 두 부분으로 구성됩니다. 플랫폼 수준에서 Azure Load Balancer는 부동 IP 사용 여부에 관계 없이 DSR 흐름 토폴로지에서 항상 작동합니다. 이는 아웃바운드 부분의 흐름이 다시 원점으로 직접 흘러가도록 언제나 올바르게 다시 작성되는 것을 의미합니다.

기본 규칙 유형을 사용하여 Azure는 사용 편의성을 위해 기존의 부하 분산 IP 주소 매핑 체계를 노출 합니다. 부동 IP를 사용하도록 설정하면 아래에 설명한대로 IP 주소 매핑 구성표가 추가적인 유연성을 허용하도록 변경됩니다.

아래 다이어그램은 이 구성을 보여 줍니다.

![녹색 및 자주색 프런트 엔드와 DSR이 포함된 다중 프런트 엔드 설명](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

이 시나리오의 경우 백 엔드 풀의 모든 VM에 네트워크 인터페이스가 3개 있습니다.

* DIP: VM과 연결된 Virtual NIC(Azure NIC 리소스의 IP 구성)
* 프런트 엔드 1: 프런트 엔드 1의 IP 주소로 구성된 게스트 OS 내 루프백 인터페이스
* 프런트 엔드 2: 프런트 엔드 2의 IP 주소로 구성된 게스트 OS 내 루프백 인터페이스

> [!IMPORTANT]
> 루프백 인터페이스에 대한 구성은 게스트 OS 내에서 수행됩니다. 이 구성은 Azure에서 수행하거나 관리하지 않습니다. 이 구성 없이는 규칙이 작동하지 않습니다. 상태 프로브 정의에는 DSR 프런트 엔드를 나타내는 루프백 인터페이스가 아닌 VM의 DIP가 사용됩니다. 따라서 서비스는 DSR 프런트 엔드를 나타내는 루프백 인터페이스에서 제공되는 서비스의 상태를 반영하는 DIP 포트에서 프로브 응답을 제공해야 합니다.

이전 시나리오에서와 동일한 프런트 엔드 구성을 가정해 보겠습니다.

| 프런트 엔드 | IP 주소 | protocol | 포트 |
| --- | --- | --- | --- |
| ![녹색 프런트 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![자주색 프런트 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

두 가지 규칙을 정의합니다.

| 규칙 | 프런트 엔드 | 백 엔드 풀에 매핑 |
| --- | --- | --- |
| 1 |![규칙](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 프런트 엔드1:80 |![백 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 프런트 엔드1:80 (VM1 및 VM2) |
| 2 |![규칙](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 프런트 엔드2:80 |![백 엔드](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 프런트 엔드2:80 (VM1 및 VM2) |

다음 표는 부하 분산 장치에서의 전체 매핑을 보여 줍니다.

| 규칙 | 프런트 엔드 IP 주소 | protocol | 포트 | 대상 | 포트 |
| --- | --- | --- | --- | --- | --- |
| ![녹색 규칙](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |프런트 엔드와 동일(65.52.0.1) |프런트 엔드와 동일(80) |
| ![자주색 규칙](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |프런트 엔드와 동일(65.52.0.2) |프런트 엔드와 동일(80) |

인바운드 흐름의 대상은 VM에서 루프백 인터페이스의 프런트 엔드 IP 주소입니다. 각 규칙은 대상 IP 주소 및 대상 포트의 고유한 조합으로 흐름을 생성해야 합니다. 흐름의 대상 IP 주소를 변경하면 동일한 VM에서 포트 재사용이 가능합니다. 프런트 엔드의 IP 주소 및 해당 루프백 인터페이스의 포트에 바인딩하면 서비스가 부하 분산 장치에 노출됩니다.

이 예는 대상 포트를 변경하지 않는지 확인합니다. 비록 부동 IP 시나리오이지만 Azure Load Balancer는 또한 백 엔드 대상 포트를 다시 작성하여 프런트 엔드 대상 포트와 다르도록 규칙을 정의하는 것을 지원합니다.

부동 IP 규칙 유형은 여러 부하 분산 장치 구성 패턴의 기초입니다. 현재 사용할 수 있는 한 가지 예로 [Multiple Listeners를 사용한 SQL AlwaysOn](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md) 구성을 들 수 있습니다. 앞으로 이러한 시나리오 더욱 자세히 설명할 것입니다.

## <a name="limitations"></a>제한 사항

* 다중 프런트 엔드 구성은 IaaS VM에서만 지원됩니다.
* 부동 IP 규칙을 사용하면 애플리케이션은 아웃바운드 흐름에 대해 기본 IP 구성을 사용해야 합니다. 애플리케이션이 게스트 OS에서 루프백 인터페이스에 구성된 프런트 엔드 IP 주소에 바인딩하는 경우 아웃바운드 흐름을 다시 작성하는 데 Azure의 SNAT를 사용할 수 없으며 흐름이 실패합니다.
* 공용 IP 주소는 대금 청구에 영향을 미칩니다. 자세한 내용은 [IP 주소 가격 책정](https://azure.microsoft.com/pricing/details/ip-addresses/)
* 구독 제한이 적용됩니다. 자세한 내용은 [서비스 제한](../azure-subscription-service-limits.md#networking-limits) 을 참조하세요.

## <a name="next-steps"></a>다음 단계

- 아웃바운드 연결 동작에서 여러 프런트 엔드의 영향을 파악하려면 [아웃바운드 연결](load-balancer-outbound-connections.md)을 검토합니다.
