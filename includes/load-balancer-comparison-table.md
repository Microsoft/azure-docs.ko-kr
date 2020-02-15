---
title: 포함 파일
description: 포함 파일
services: load balancer
author: KumudD
ms.service: load-balancer
ms.topic: include
ms.date: 02/08/2018
ms.author: kumud
ms.custom: include file
ms.openlocfilehash: 4219df03f74f737c5f2435f9bc0842189dc1fd49
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909202"
---
| | 표준 SKU | 기본 SKU |
| --- | --- | --- |
| 백 엔드 풀 크기 | 최대 1000개 인스턴스를 지원합니다. | 최대 300개 인스턴스를 지원합니다. |
| 백 엔드 풀 엔드포인트 | 가상 머신, 가용성 집합 및 가상 머신 확장 집합의 혼합을 포함한 단일 가상 네트워크의 모든 가상 머신입니다. | 단일 가용성 집합 또는 가상 머신 확장 집합의 가상 머신. |
| [상태 프로브](../articles/load-balancer/load-balancer-custom-probe-overview.md#types) | TCP, HTTP, HTTPS | TCP, HTTP |
| [상태 프로브 다운 동작](../articles/load-balancer/load-balancer-custom-probe-overview.md#probedown) | TCP 연결은 인스턴스 프로브 __및__ 모든 프로브가 다운되어 있을 때 그대로 유지됩니다. | TCP 연결은 인스턴스 프로브가 다운되어 있을 때 그대로 유지됩니다. 모든 프로브가 다운되면 모든 TCP 연결이 종료됩니다. |
| 가용성 영역 | 인바운드 및 아웃바운드 트래픽에 대한 영역 중복 및 영역 프런트 엔드가 있습니다. 아웃바운드 흐름 매핑이 영역 오류에 남아 있습니다. 영역 간 부하 분산. | 사용할 수 없음 |
| 진단 | Azure Monitor. 바이트 및 패킷 카운터를 포함한 다차원 메트릭. 상태 프로브 상태. 연결 시도(TCP SYN). 아웃바운드 연결 상태(SNAT 성공 및 실패 흐름). 활성 데이터 평면 측정. | 퍼블릭 Load Balancer 전용 Azure Log Analytics. SNAT 소모 경고입니다. 백 엔드 풀 상태 수입니다. |
| HA 포트 | 내부 부하 분산 장치 | 사용할 수 없음 |
| 기본적으로 보안 적용 | 공용 IP, 공용 Load Balancer 엔드포인트 및 내부 Load Balancer 엔드포인트는 네트워크 보안 그룹에서 허용하지 않는 한 인바운드 흐름에 대해 닫힙니다. VNET에서 내부 부하 분산 장치에 대한 내부 트래픽은 여전히 허용됩니다. | 기본적으로 엽니다. 네트워크 보안 그룹 옵션입니다. |
| [아웃바운드 연결](../articles/load-balancer/load-balancer-outbound-connections.md) | [아웃바운드 규칙](../articles/load-balancer/load-balancer-outbound-rules-overview.md)을 사용하여 풀 기반 아웃바운드 NAT를 명시적으로 정의할 수 있습니다. 부하 분산 규칙별 옵트아웃이 적용되는 여러 프런트 엔드를 사용할 수 있습니다. 아웃바운드 연결을 사용하려면 가상 머신, 가용성 집합, 가상 머신 확장 집합에 대한 아웃바운드 시나리오를 명시적으로 만들어야 _합니다_. 가상 네트워크 서비스 엔드포인트는 아웃바운드 연결을 정의하지 않고 연결할 수 있으며, 처리된 데이터 수는 고려되지 않습니다. 가상 네트워크 서비스 엔드포인트로 사용할 수 없는 Azure PaaS 서비스를 포함하는 모든 공용 IP 주소는 아웃바운드 연결을 사용하여 연결해야 하며, 처리된 데이터 수도 고려되어야 합니다. 내부 Load Balancer만 가상 머신, 가용성 집합 또는 가상 머신 확장 집합을 제공하는 경우 기본 SNAT를 통한 아웃바운드 연결은 사용할 수 없습니다. 대신 [아웃바운드 규칙](../articles/load-balancer/load-balancer-outbound-rules-overview.md)을 사용합니다. 아웃바운드 SNAT 프로그래밍은 인바운드 부하 분산 규칙의 전송 프로토콜에 따라 달라집니다. | 여러 프런트 엔드가 있을 때 임의로 선택되는 단일 프런트 엔드입니다. 내부 Load Balancer만 가상 머신, 가용성 집합 또는 가상 머신 확장 집합을 제공하는 경우 기본 SNAT가 사용됩니다. |
| [아웃바운드 규칙](../articles/load-balancer/load-balancer-outbound-rules-overview.md) | 공용 IP 주소 또는 공용 IP 접두사 또는 둘 다를 사용하는 선언적 아웃바운드 NAT 구성. 구성 가능한 아웃바운드 유휴 시간 제한(4-120분). 사용자 지정 SNAT 포트 할당 | 사용할 수 없음 |
| [유휴 상태의 TCP 재설정](../articles/load-balancer/load-balancer-tcp-reset.md) | 규칙에서 유휴 시간 제한에 도달하면 TCP 재설정(TCP RST) 사용 | 사용할 수 없음 |
| [여러 프론트 엔드](../articles/load-balancer/load-balancer-multivip-overview.md) | 인바운드 및 [아웃바운드](../articles/load-balancer/load-balancer-outbound-connections.md) | 인바운드 전용 |
| 관리 작업 | 대부분 작업을 30초 이내에 수행 | 일반적으로 60-90+초 |
| SLA | 2개의 정상 가상 머신이 있는 데이터 경로에 대해 99.99%. | 해당 없음 | 
| 가격 책정 | 규칙 수, 리소스와 관련해서 인바운드 및 아웃바운드로 처리된 데이터에 따라 요금이 부과됩니다. | 무료 |
|  |  |  |
