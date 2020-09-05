---
title: Azure Load Balancer의 새로운 기능
description: 최신 릴리스 정보, 알려진 문제, 버그 수정, 사용되지 않는 기능 및 예정된 변경 내용과 같은 Azure Load Balancer의 새로운 기능을 알아봅니다.
services: load-balancer
author: anavinahar
ms.service: load-balancer
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: anavin
ms.openlocfilehash: ffea6cdd1c8558a07559829b025cb5338cc59ee3
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88586718"
---
# <a name="whats-new-in-azure-load-balancer"></a>Azure Load Balancer의 새로운 기능

Azure Load Balancer는 정기적으로 업데이트됩니다. 최신 공지 사항을 최신 상태로 유지합니다. 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능(해당하는 경우)

[여기](https://azure.microsoft.com/updates/?category=networking&query=load%20balancer)에서 최신 Azure Load Balancer 업데이트를 찾아 RSS 피드를 구독할 수도 있습니다.

## <a name="recent-releases"></a>최근 릴리스

| Type |속성 |Description  |추가된 날짜  |
| ------ |---------|---------|---------|
| 기능 | IP 기반 백 엔드 풀 관리 지원(미리 보기) | Azure Load Balancer는 IPv4 또는 IPv6 주소를 통해 백 엔드 풀에서 리소스 추가 및 제거를 지원합니다. 이렇게 하면 Load Balancer와 연결된 컨테이너, 가상 머신 및 가상 머신 확장 집합을 쉽게 관리할 수 있습니다. 또한 관련 리소스를 만들기 전에 IP 주소를 백 엔드 풀의 일부로 예약할 수 있습니다. [여기](backend-pool-management.md)에서 자세히 알아보세요.|2020년 7월 |
| 기능| Azure Monitor를 사용하는 Azure Load Balancer Insights | 네트워크용 Azure Monitor의 일부로 빌드된 고객은 이제 Azure Portal에서 메트릭으로 미리 구성된 표준 Load Balancers용 모든 Load Balancer 구성과 상태 대시보드에 대한 토폴로지 맵을 보유하고 있습니다. [시작 및 자세한 정보](https://azure.microsoft.com/blog/introducing-azure-load-balancer-insights-using-azure-monitor-for-networks/) | 2020년 6월 |
| 유효성 검사 | HA 포트에 대한 유효성 검사 추가 | 부동 IP가 활성화된 경우 HA 포트 규칙과 비 HA 포트 규칙만 구성할 수 있도록 유효성 검사가 추가되었습니다. 이전에는 이 구성이 진행되었지만 의도한 대로 작동하지 않았습니다. 기능이 변경되지 않았습니다. [여기](load-balancer-ha-ports-overview.md#limitations)에서 자세히 알아볼 수 있습니다.| 2020년 6월 |
| 기능| Azure Load Balancer에 대한 IPv6 지원(일반적으로 사용 가능) | Azure Load Balancers에 대한 프런트 엔드로 IPv6 주소를 사용할 수 있습니다. [여기에서 이중 스택 애플리케이션](../virtual-network/virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md)을 만드는 방법에 대해 알아봅니다. |2020년 4월|
| 기능| 유휴 시간 제한 시 TCP 다시 설정(일반적으로 사용 가능)| TCP 다시 설정을 사용하여 보다 예측 가능한 애플리케이션 동작을 만듭니다. [자세히 알아보기](load-balancer-tcp-reset.md)| 2020년 2월 |

## <a name="known-issues"></a>알려진 문제

현재 제품 그룹에서 다음과 같은 알려진 문제를 해결하기 위해 적극적으로 작업 중입니다.

|문제 |Description  |완화 방법  |
| ---------- |---------|---------|
| Log Analytics 내보내기 | Log Analytics에서 표준 Load Balancer의 메트릭과 기본 Load Balancer의 상태 프로브 상태 로그를 내보낼 수 없음  | [표준 Load Balancer의 다차원 메트릭에 Azure Monitor를 활용](load-balancer-standard-diagnostics.md)합니다. Log Analytics를 모니터링에 사용할 수는 없지만, Azure Monitor는 다양한 다차원 메트릭 세트에 대한 시각화를 제공합니다. Load Balancer의 인사이트 하위 블레이드를 통해 미리 구성된 메트릭 대시보드를 활용할 수 있습니다. 기본 Load Balancer 사용하는 경우 프로덕션 수준 메트릭 모니터링이 가능하도록 [표준으로 업그레이드](upgrade-basic-standard.md)하세요.

  

## <a name="next-steps"></a>다음 단계

Azure Load Balancer에 대한 자세한 내용은 [Azure Load Balancer란?](load-balancer-overview.md) 및 [질문과 대답](load-balancer-faqs.md)을 참조하세요.
