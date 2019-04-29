---
title: 지속형 함수의 재해 복구 및 지역 복제 - Azure
description: 지속형 함수의 재해 복구 및 지역 복제에 대해 알아봅니다.
services: functions
author: MS-Santi
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 1363dd3c620789b9f3c8ce1dbe0892ee61d66051
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60741383"
---
# <a name="disaster-recovery-and-geo-distribution"></a>재해 복구 및 지역 배포

## <a name="overview"></a>개요

Durable Functions에서 모든 상태는 Azure Storage에 유지됩니다. [작업 허브](durable-functions-task-hubs.md)는 오케스트레이션에 사용되는 Azure Storage 리소스에 대한 논리적 컨테이너입니다. 오케스트레이터 및 작업 함수는 동일한 작업 허브에 속할 때만 상호 작용할 수 있습니다.
설명된 시나리오는 가용성을 높여 주는 배포 옵션을 제안하고, 재해 복구 작업 동안 작동 중단을 최소화합니다.

이러한 시나리오는 Azure Storage를 사용하면서 진행되므로 활성-수동 구성을 기준으로 한다는 점을 알아야 합니다. 이 패턴은 다른 지역으로의 백업(수동) 함수 앱 배포로 구성됩니다. Traffic Manager는 기본(활성) 함수 앱의 가용성을 모니터링합니다. 기본 함수 앱이 실패하면 백업 함수 앱으로 장애 조치(Failover)됩니다. 자세한 내용은 [Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)의 [우선 순위 트래픽 라우팅 방법](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method)을 참조하세요.

>[!NOTE]
>
> - 제안된 활성-수동 구성은 클라이언트가 항상 HTTP를 통해 새 오케스트레이션을 트리거할 수 있도록 합니다. 그러나 동일한 저장소를 공유하는 두 개의 함수 앱이 구현되므로 둘 간에 백그라운드 처리가 분산되고 같은 큐의 메시지에 대해 경합합니다. 이 구성에서는 보조 함수 앱에 대한 추가 송신 비용이 발생합니다.
> - 기본 저장소 계정 및 작업 허브가 기본 지역에 생성되고 두 함수 앱에서 공유됩니다.
> - 중복 배포된 모든 함수 앱은 HTTP를 통해 활성화될 경우 동일한 함수 액세스 키를 공유해야 합니다. 함수 런타임은 소비자가 프로그래밍 방식으로 함수 키를 추가, 삭제 및 업데이트할 수 있도록 하는 [관리 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)를 노출합니다.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>시나리오 1 - 공유 스토리지에서 계산 부하 분산

Azure의 계산 인프라가 실패하면 함수 앱을 사용하지 못할 수 있습니다. 이러한 작동 중단 가능성을 최소화하기 위해 이 시나리오에서는 다른 지역에 배포된 두 개의 함수 앱을 사용합니다.
Traffic Manager는 기본 함수 앱의 문제를 감지하고, 보조 지역의 함수 앱으로 트래픽을 자동으로 리디렉션하도록 구성됩니다. 이 함수 앱은 동일한 Azure Storage 계정 및 작업 허브를 공유합니다. 따라서 함수 앱의 상태는 손실되지 않으며 정상적으로 다시 시작될 수 있습니다. 기본 지역의 상태가 복원되면 Azure Traffic Manager는 해당 함수 앱으로의 요청 라우팅을 자동으로 시작합니다.

![시나리오 1을 보여주는 다이어그램.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

이 배포 시나리오를 사용할 경우 다음과 같은 몇 가지 이점을 얻을 수 있습니다.

- 계산 인프라가 실패하면 장애 조치(failover) 지역에서 상태 손실 없이 작업이 재개될 수 있습니다.
- Traffic Manager는 정상 상태의 함수 앱으로 자동 장애 조치(Failover)가 수행되도록 합니다.
- Traffic Manager는 작동 중단이 해결된 후에 기본 함수 앱으로의 트래픽을 자동으로 다시 설정합니다.

그러나 이 시나리오를 사용할 때는 다음을 고려하세요.

- 함수 앱이 전용 App Service 계획을 사용하여 배포될 경우 장애 조치(failover) 데이터 센터에서 계산 인프라를 복제하면 비용이 늘어납니다.
- 이 시나리오에서는 계산 인프라의 작동 중단을 다루지만, 스토리지 계정이 계속해서 함수 앱에 대한 단일 실패 지점이 될 수 있습니다. 저장소가 작동 중단되면 애플리케이션 가동이 중지됩니다.
- 함수 앱이 장애 조치(Failover)되면 여러 지역에 걸쳐 해당 저장소 계정에 액세스하게 되므로 대기 시간이 증가합니다.
- 다른 지역에 있는 저장소 서비스에 액세스하게 되면 네트워크 송신 트래픽으로 인해 더 높은 비용이 발생합니다.
- 이 시나리오는 Traffic Manager에 따라 달라집니다. [Traffic Manager 작동 방식](../../traffic-manager/traffic-manager-how-it-works.md)을 고려한다면 약간의 시간이 경과된 후에, 지속형 함수를 사용하는 클라이언트 애플리케이션이 Traffic Manager에서 함수 앱 주소를 다시 쿼리해야 할 필요가 발생할 수 있습니다.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>시나리오 2 - 지역 스토리지에서 계산 부하 분산

이전 시나리오에서는 계산 인프라의 실패 경우만 다루었습니다. 저장소 서비스가 실패하면 함수 앱이 작동 중단됩니다.
지속형 함수가 계속해서 작동되게 하기 위해 이 시나리오에서는 함수 앱이 배포되는 각 지역에서 로컬 저장소 계정을 사용합니다.

![시나리오 2를 보여주는 다이어그램.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

이 방법은 이전 시나리오를 약간 개선합니다.

- 함수 앱이 실패하면 Traffic Manager는 보조 지역으로 장애 조치(Failover)되도록 합니다. 그러나 함수 앱은 자체 저장소 계정을 사용하므로 지속형 함수는 계속 작동합니다.
- 장애 조치(Failover) 동안에는 함수 앱과 저장소 계정이 같은 위치에 있으므로 장애 조치(Failover) 지역에서 추가 대기 시간이 발생하지 않습니다.
- 저장소 계층의 오류는 지속형 함수 실패로 이어지며, 장애 조치(Failover) 지역으로의 리디렉션이 트리거됩니다. 다시 말해서, 함수 앱 및 저장소 계정이 지역별로 구분되어 있으므로 지속형 함수는 계속 작동합니다.

이 시나리오에 대한 중요한 고려 사항:

- 함수 앱이 전용 App Service 계획을 사용하여 배포될 경우 장애 조치(failover) 데이터 센터에서 계산 인프라를 복제하면 비용이 늘어납니다.
- 현재 상태는 장애 조치(Failover)되지 않습니다. 즉, 실행 중인 검사점 함수는 실패합니다. 작업을 다시 시도/다시 시작하는 것은 클라이언트 애플리케이션의 책임입니다.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>시나리오 3 - GRS 공유 스토리지에서 계산 부하 분산

이 시나리오는 첫 번째 시나리오를 수정하고 공유 저장소 계정을 구현합니다. 주요 차이점은 지역 복제가 사용되도록 설정된 저장소 계정을 만든다는 것입니다.
기능적으로 볼 때, 이 시나리오는 시나리오 1과 동일한 이점을 제공하지만 데이터 복구 측면에서 추가적인 이점을 제공합니다.

- GRS(지역 중복 저장소) 및 RA-GRS(읽기 액세스 GRS)는 저장소 계정의 가용성을 최대화합니다.
- 지역에서 저장소 서비스가 작동 중단될 경우, 데이터 센터 운영 담당자가 저장소를 보조 지역으로 장애 조치(Failover)하도록 결정할 수 있습니다. 이 경우 저장소 계정 액세스는 사용자 개입 없이, 저장소 계정의 지역 복제 사본으로 투명하게 리디렉션됩니다.
- 이 경우 지속형 함수의 상태까지 몇 분 간격으로 발생하는 저장소 계정의 마지막 복제까지 유지됩니다.

다른 시나리오와 마찬가지로 다음과 같은 중요한 고려 사항이 있습니다.

- 복제 데이터베이스로의 장애 조치(Failover)는 데이터 센터 운영자가 수행하며 다소 시간이 걸릴 수 있습니다. 그때까지 함수 앱은 작동 중단됩니다.
- 지역 복제 저장소 계정을 사용할 경우 추가 비용이 발생합니다.
- GRS는 비동기적으로 발생합니다. 일부 최신 트랜잭션은 복제 프로세스의 대기 시간으로 인해 손실될 수 있습니다.

![시나리오 3을 보여주는 다이어그램.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

## <a name="next-steps"></a>다음 단계

[RA-GRS를 사용하여 항상 사용 가능한 애플리케이션 설계](../../storage/common/storage-designing-ha-apps-with-ragrs.md)에 대해 자세히 읽어볼 수 있습니다.
