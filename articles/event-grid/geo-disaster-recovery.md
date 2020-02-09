---
title: Azure Event Grid에서 지역 재해 복구 | Microsoft Docs
description: Azure Event Grid에서 지 원하는 방법 지역 재해 복구 (GeoDR) 자동으로 설명 합니다.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: spelluru
ms.openlocfilehash: 5b5c973a8daa8776efb0909092c569ea46902265
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "66307319"
---
# <a name="server-side-geo-disaster-recovery-in-azure-event-grid"></a>Azure Event Grid의 서버 쪽 지역 재해 복구
Event Grid는 자동 지역 재해 복구 (GeoDR)의 메타 데이터 뿐 아니라에 대한 새 하지만 모든 기존 도메인, 항목 및 이벤트 구독 되었습니다. 전체 Azure 지역이 중단 되 면 Event Grid는 이미 모든 쌍을 이루는 지역에 동기화 인프라 이벤트와 관련 된 메타 데이터입니다. 새 이벤트는 사용자가 개입 없이 다시 흐름을 시작 합니다. 

재해 복구는 두 개의 메트릭을 사용 하 여 측정 됩니다.

- [복구 지점 목표 (RPO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_Point_Objective): 분 또는 시간 데이터는 손실 될 수 있습니다.
- [복구 시간 목표 (RTO)](https://en.wikipedia.org/wiki/Disaster_recovery#Recovery_time_objective):이 서비스는 시간의 분 축소 합니다.

Event Grid의 자동 장애 조치에는 다양 한 Rpo 및 Rto (이벤트 구독 등) 메타 데이터에 대한 데이터 (이벤트)에 있습니다. 다음 위치에서 다른 사양에 필요한 경우를 구현할 수 있습니다 고유한 [클라이언트 쪽 항목을 사용 하 여 조치 (failover) 상태 api](custom-disaster-recovery.md)합니다.

## <a name="recovery-point-objective-rpo"></a>복구 지점 목표(RPO)
- **메타 데이터 RPO**: 0 분입니다. 리소스는 Event Grid에 만들어지면, 언제 든 지는 즉시 지역 간에 복제 됩니다. 장애 조치가 발생 하는 경우 메타 데이터가 손실 됩니다.
- **데이터 RPO**: 시스템이 정상이며 지역별 장애 조치(failover) 시 기존 트래픽을 처리하는 경우 이벤트의 RPO는 약 5분입니다.

## <a name="recovery-time-objective-rto"></a>복구 시간 목표(RTO)
- **메타 데이터 RTO**: 일반적으로 훨씬 더 신속 하 게 내에서 발생 60 분 하지만 Event Grid 토픽 및 구독에 대한 만들기/업데이트/삭제 호출을 수락 하기 시작 합니다.
- **데이터 RTO**: 메타 데이터와 같은 일반적으로 발생 훨씬 더 신속 하 게 단 60 분 내 Event Grid 예정 지역 장애 조치 후 새 트래픽을 허용 합니다.

> [!NOTE]
> GeoDR Event Grid에서 메타 데이터에 대한 비용은: 0 원입니다.


## <a name="next-steps"></a>다음 단계
고유한 클라이언트 쪽 장애 조치 논리를 구현 하려는 경우 참조 [# 빌드 Event Grid의 사용자 지정 항목에 대한 고유한 재해 복구](custom-disaster-recovery.md)
