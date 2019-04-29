---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: 459c99d1b45af9c98cc1a6d0d7dd7a9a04c824ec
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516974"
---
Azure 부하 분산 장치는 계층 4(TCP, UDP) 부하 분산 장치입니다. 부하 분산 장치는 부하 분산 장치 집합에 있는 클라우드 서비스 또는 가상 머신의 정상 서비스 인스턴스 간에 들어오는 트래픽을 배포하여 고가용성을 제공합니다. Azure Load Balancer는 여러 포트, 여러 IP 주소 또는 둘 다에서 이러한 서비스를 제공할 수도 있습니다.

다음에 대해 부하 분산 장치를 구성할 수 있습니다.

* 들어오는 인터넷 트래픽을 가상 머신(VM)에 부하 분산합니다. 이 시나리오에서 부하 분산 장치를 [인터넷 연결 부하 분산 장치](../articles/load-balancer/load-balancer-internet-overview.md)라고 합니다.
* 가상 네트워크(VNet)의 VM 간, 클라우드 서비스의 VM 간 또는 크로스-프레미스 가상 네트워크의 온-프레미스 컴퓨터와 VM 간에 트래픽을 부하 분산합니다. 이 시나리오에서 부하 분산 장치를 [내부 부하 분산 장치(ILB)](../articles/load-balancer/load-balancer-internal-overview.md)라고 합니다.
* 외부 트래픽을 특정 VM 인스턴스로 전달합니다.