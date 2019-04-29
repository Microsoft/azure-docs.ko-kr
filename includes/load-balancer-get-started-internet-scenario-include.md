---
author: WenJason
ms.service: load-balancer
ms.topic: include
origin.date: 11/09/2018
ms.date: 12/31/2018
ms.author: v-jay
ms.openlocfilehash: bf3aee30460e052db23cf9090f13e2af3b22628b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60516975"
---
이 시나리오에서는 다음 작업이 수행 됩니다.

* 포트 80의 네트워크 트래픽을 수신하는 부하 분산 장치를 만들고 부하가 분산된 트래픽을 가상 머신 "web1" 및 "web2"에 전송합니다.
* 부하 분산 장치 뒤에 있는 가상 머신의 원격 데스크톱 액세스/SSH에 대한 NAT 규칙 만들기
* 상태 프로브 만들기

![부하 분산 장치 시나리오](./media/load-balancer-get-started-internet-scenario-include/scenario-classic.png)