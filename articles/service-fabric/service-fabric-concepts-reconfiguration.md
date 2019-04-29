---
title: Azure Service Fabric의 재구성 | Microsoft Docs
description: Service Fabric의 파티션 재구성 이해
services: service-fabric
documentationcenter: .net
author: appi101
manager: anuragg
editor: ''
ms.assetid: d5ab75ff-98b9-4573-a2e5-7f5ab288157a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/10/2018
ms.author: aprameyr
ms.openlocfilehash: a24aa6aa1695a3d1166816b7960bdd7b551e1a37
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882200"
---
# <a name="reconfiguration-in-azure-service-fabric"></a>Azure Service Fabric의 재구성
*구성*은 상태 저장 서비스의 파티션에 대한 복제본과 그 역할로 정의됩니다.

*재구성*은 한 구성을 다른 구성으로 이동하는 프로세스입니다. 상태 저장 서비스의 파티션에 대해 복제본 세트에 변경을 수행합니다. 기존 구성을 *Previous Configuration (PC), 이전 구성*이라고 하고 새 구성을 *Current Configuration(CC), 현재 구성*이라 합니다. Azure Service Fabric의 재구성 프로토콜은 복제본 세트에 대한 모든 변경에서 일관성을 유지하고 가용성을 유지 관리합니다.

장애 조치(Failover) 관리자는 시스템의 여러 이벤트에 대한 대처로 재구성을 시작합니다. 예를 들어, 주 복제본이 실패하면 활성 보조를 주 복제본으로 승격시키기 위해 재구성이 시작됩니다. 또 다른 예로 노드 업그레이드를 위해 주 복제본을 다른 노드로 이동해야 하는 애플리케이션 업그레이드를 들 수 있습니다.

## <a name="reconfiguration-types"></a>재구성 유형
재구성은 두 가지 유형으로 분류할 수 있습니다.

- 주 복제본이 변경 중인 재구성
    - **장애 조치(Failover)**: 장애 조치는 실행 중인 주의 오류에 대 한 응답으로 재구성 합니다.
    - **SwapPrimary**: 교환에는 서비스 패브릭 부하 분산에 대 한 일반적인 응답을 실행 중인 주 노드에서 간에 이동 해야 하는 재구성 또는 업그레이드입니다.

- 주 복제본이 변경되지 않는 재구성

## <a name="reconfiguration-phases"></a>재구성 단계
재구성은 몇 가지 단계로 진행됩니다.

- **Phase0**: 이 단계는 현재 주 복제본이 상태를 새 주 서버와 활성 보조로 전환 하는 스왑 주 재구성에서 발생 합니다.

- **Phase1**: 이 단계는 기본 변경 되는 재구성 하는 동안 발생 합니다. 이 단계 중에는 Service Fabric이 현재 복제본 중 어떤 것이 정확한 주 복제본인가를 식별합니다. 주 복제본 교체 재구성 중에는 새 주 복제본이 이미 선택되었으므로 이 단계가 필요하지 않습니다. 

- **Phase2**: 이 단계에서는 Service Fabric 모든 데이터를 현재 구성의 복제본 대부분에서 사용할 수 있는지 확인 합니다.

내부 전용인 몇 가지 다른 단계가 있습니다.

## <a name="stuck-reconfigurations"></a>멈춘 재구성
재구성은 다양한 이유로 *멈출 수* 있습니다. 몇 가지 일반적인 이유는 다음과 같습니다.

- **복제본 다운**: 일부 재구성 단계에서는 구성에서 복제본 대부분이 가동 상태 여야 합니다.
- **네트워크 또는 통신 문제**: 재구성에서는 서로 다른 노드 간의 네트워크 연결이 필요 합니다.
- **API 오류**: 재구성 프로토콜은 서비스 구현이 특정 Api를 완료 해야 합니다. 예를 들어, 신뢰할 수 있는 서비스에서 토큰 취소를 적용하지 않으면 SwapPrimary 재구성이 멈춥니다.

System.FM, System.RA, System.RAP 등과 같은 시스템 구성 요소의 상태 보고서를 사용하여 재구성이 멈춘 지점을 진단합니다. [시스템 상태 보고서 페이지](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)에서 이러한 상태 보고서를 설명합니다.

## <a name="next-steps"></a>다음 단계
Service Fabric 개념에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Reliable Services 수명 주기 - C#](service-fabric-reliable-services-lifecycle.md)
- [시스템 상태 보고서](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)
- [복제본 및 인스턴스](service-fabric-concepts-replica-lifecycle.md)
