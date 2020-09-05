---
title: Azure Load Balancer SKU
description: Azure Load Balancer SKU의 개요
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/01/2020
ms.author: allensu
ms.openlocfilehash: 3509b99d1b222fc4f0bfa48effe42496c209d9ae
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654483"
---
# <a name="azure-load-balancer-skus"></a>Azure Load Balancer SKU

Azure Load Balancer에는 두 가지 SKU가 있습니다.

## <a name="sku-comparison"></a><a name="skus"></a> SKU 비교

부하 분산 장치는 표준 및 기본 SKU를 모두 지원합니다. 이러한 SKU는 시나리오 규모, 기능 및 가격 책정에서 차이가 있습니다. 기본 부하 분산 장치로 가능한 시나리오를 표준 부하 분산 장치로 만들 수 있습니다.

차이점을 비교하고 파악하려면 아래 표를 참조하요. 자세한 내용은 [Azure 표준 Load Balancer 개요](load-balancer-standard-overview.md)를 참조하세요.

>[!NOTE]
> Microsoft는 표준 부하 분산 장치를 권장합니다.
독립 실행형 VM, 가용성 집합 및 가상 머신 확장 집합은 하나의 SKU에만 연결될 수 있습니다. 공용 IP 주소와 함께 사용할 때는 부하 분산 장치 및 공용 IP 주소 SKU가 일치해야 합니다. 부하 분산 장치 및 공용 IP SKU는 변경할 수 없습니다.

| | 표준 Load Balancer | 기본 Load Balancer |
| --- | --- | --- |
| **[백 엔드 풀 크기](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)** | 최대 1000개 인스턴스를 지원합니다. | 최대 300개 인스턴스를 지원합니다. |
| **백 엔드 풀 엔드포인트** | 단일 가상 네트워크에 있는 가상 머신 또는 가상 머신 확장 집합 | 단일 가용성 집합 또는 가상 머신 확장 집합의 가상 머신. |
| **[상태 프로브](./load-balancer-custom-probe-overview.md#types)** | TCP, HTTP, HTTPS | TCP, HTTP |
| **[상태 프로브 다운 동작](./load-balancer-custom-probe-overview.md#probedown)** | TCP 연결은 인스턴스 프로브 __및__ 모든 프로브가 다운되어 있을 때 그대로 유지됩니다. | TCP 연결은 인스턴스 프로브가 다운되어 있을 때 그대로 유지됩니다. 모든 프로브가 중지되면 모든 TCP 연결은 종료됩니다. |
| **가용성 영역** | 인바운드 및 아웃바운드 트래픽에 대한 영역 중복 및 영역 프런트 엔드가 있습니다. | 사용할 수 없음 |
| **진단** | [Azure Monitor 다차원 메트릭](./load-balancer-standard-diagnostics.md) | [Azure Monitor 로그](./load-balancer-monitor-log.md) |
| **HA 포트** | [내부 Load Balancer에 사용 가능](./load-balancer-ha-ports-overview.md) | 사용할 수 없음 |
| **기본적으로 보안 적용** | 네트워크 보안 그룹에서 허용하지 않는 한 인바운드 흐름으로 종결됩니다. 가상 네트워크에서 내부 부하 분산 장치에 대한 내부 트래픽은 허용됩니다. | 기본적으로 엽니다. 네트워크 보안 그룹 옵션입니다. |
| **아웃바운드 규칙** | [선언적 아웃바운드 NAT 구성](./load-balancer-outbound-rules-overview.md) | 사용할 수 없음 |
| **유휴 상태의 TCP 재설정** | [모든 규칙에서 사용 가능](./load-balancer-tcp-reset.md) | 사용할 수 없음 |
| **[여러 프론트 엔드](./load-balancer-multivip-overview.md)** | 인바운드 및 [아웃바운드](./load-balancer-outbound-connections.md) | 인바운드 전용 |
| **관리 작업** | 대부분 작업을 30초 이내에 수행 | 일반적으로 60-90+초 |
| **SLA** | [99.99%](https://azure.microsoft.com/support/legal/sla/load-balancer/v1_0/) | 사용할 수 없음 | 

자세한 내용은 [Load Balancer 제한](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer)을 참조하세요. 표준 Load Balancer 세부 내용은 [개요](load-balancer-standard-overview.md), [가격](https://aka.ms/lbpricing) 및 [SLA](https://aka.ms/lbsla)를 참조하세요.

## <a name="limitations"></a>제한 사항

- SKU는 변경할 수 없습니다. 기존 리소스의 SKU는 변경할 수 없습니다.
- 독립 실행형 가상 머신 리소스, 가용성 집합 리소스 또는 가상 머신 확장 집합 리소스는 하나의 SKU만 참조할 수 있습니다.
- 표준 Load Balancer 및 표준 공용 IP 리소스에는 [구독 이동 작업](../azure-resource-manager/management/move-resource-group-and-subscription.md)이 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- Load Balancer를 사용하여 시작하려면 [공용 표준 Load Balancer 만들기](quickstart-load-balancer-standard-public-portal.md)를 참조하세요.
- [표준 Load Balancer 및 가용성 영역](load-balancer-standard-availability-zones.md) 사용에 대해 자세히 알아보세요.
- [상태 프로브](load-balancer-custom-probe-overview.md)에 대해 자세히 알아보세요.
- [아웃바운드 연결에 Load Balancer](load-balancer-outbound-connections.md) 사용에 대해 자세히 알아보세요.
- [HA 포트 부하 분산 규칙을 사용하는 표준 Load Balancer](load-balancer-ha-ports-overview.md)에 대해 자세히 알아보세요.
- [네트워크 보안 그룹](../virtual-network/security-overview.md)에 대해 자세히 알아보세요.
