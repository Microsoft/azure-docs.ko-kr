---
title: 재해 복구 및 지역 배포 Azure Durable Functions
description: 지속형 함수의 재해 복구 및 지역 복제에 대해 알아봅니다.
author: MS-Santi
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: azfuncdf
ms.openlocfilehash: 01c400f51cce85ef39e9d39bcad1221253c6942d
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89071213"
---
# <a name="disaster-recovery-and-geo-distribution-in-azure-durable-functions"></a>Azure Durable Functions의 재해 복구 및 지역 배포

Microsoft는 Azure 서비스를 항상 사용할 수 있도록 하기 위해 노력합니다. 그러나 계획되지 않은 서비스 중단이 발생할 수 있습니다. 응용 프로그램에 복원 력이 필요한 경우 지역 중복성을 위해 앱을 구성 하는 것이 좋습니다. 또한 고객은 지역 서비스 중단을 처리하기 위해 재해 복구 계획을 설정하는 것이 좋습니다. 재해 복구 계획의 중요 한 부분은 주 복제본을 사용할 수 없게 되는 경우 앱 및 저장소의 보조 복제본으로 장애 조치 (failover)를 준비 하는 것입니다.

Durable Functions 모든 상태는 기본적으로 Azure Storage에서 유지 됩니다. [작업 허브](durable-functions-task-hubs.md) 는 [오케스트레이션](durable-functions-types-features-overview.md#orchestrator-functions) 및 [엔터티에](durable-functions-types-features-overview.md#entity-functions)사용 되는 Azure Storage 리소스에 대 한 논리적 컨테이너입니다. Orchestrator, activity 및 entity 함수는 동일한 작업 허브에 속하는 경우에만 상호 작용할 수 있습니다. 이 문서에서는 이러한 Azure Storage 리소스를 항상 사용 가능 하 게 유지 하기 위한 시나리오를 설명 하는 경우 작업 허브를 참조 합니다.

오케스트레이션과 엔터티는 HTTP를 통해 자체적으로 트리거된 [클라이언트 함수](durable-functions-types-features-overview.md#client-functions) 를 사용 하 여 트리거하거나 다른 지원 되는 Azure Functions 트리거 형식 중 하나를 사용 하 여 트리거될 수 있습니다. [기본 제공 HTTP api](durable-functions-http-features.md#built-in-http-apis)를 사용 하 여 트리거될 수도 있습니다. 간단히 하기 위해이 문서에서는 Azure Storage 및 HTTP 기반 기능 트리거와 관련 된 시나리오와 가용성을 높이고 재해 복구 작업을 수행 하는 동안 가동 중지 시간을 최소화 하는 옵션에 대해 집중적으로 설명 합니다. Service Bus 또는 Cosmos DB 트리거와 같은 다른 트리거 형식은 명시적으로 검사 되지 않습니다.

다음 시나리오는 Azure Storage의 사용을 안내 하므로 활성-수동 구성을 기반으로 합니다. 이 패턴은 다른 지역으로의 백업(수동) 함수 앱 배포로 구성됩니다. Traffic Manager는 기본 (활성) 함수 앱에서 HTTP 가용성을 모니터링 합니다. 기본 함수 앱이 실패하면 백업 함수 앱으로 장애 조치(Failover)됩니다. 자세한 내용은 [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/)의 [우선 순위 트래픽 라우팅 방법](../../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method) 을 참조 하세요.

> [!NOTE]
> - 제안된 활성-수동 구성은 클라이언트가 항상 HTTP를 통해 새 오케스트레이션을 트리거할 수 있도록 합니다. 그러나 두 개의 함수 앱이 저장소에서 동일한 작업 허브를 공유 하는 것으로 인해 일부 백그라운드 저장소 트랜잭션이 둘 간에 배포 됩니다. 따라서이 구성은 보조 함수 앱에 대 한 몇 가지 추가 송신 비용이 발생 합니다.
> - 기본 스토리지 계정 및 작업 허브가 기본 지역에 생성되고 두 함수 앱에서 공유됩니다.
> - 중복 배포 된 모든 함수 앱은 HTTP를 통해 활성화 되는 경우 동일한 함수 액세스 키를 공유 해야 합니다. 함수 런타임은 소비자가 프로그래밍 방식으로 함수 키를 추가, 삭제 및 업데이트할 수 있도록 하는 [관리 API](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)를 노출합니다. 키 관리는 [Azure Resource Manager api](https://www.markheath.net/post/managing-azure-functions-keys-2)를 사용 하는 경우에도 가능 합니다.

## <a name="scenario-1---load-balanced-compute-with-shared-storage"></a>시나리오 1 - 공유 스토리지에서 컴퓨팅 부하 분산

Azure의 컴퓨팅 인프라가 실패하면 함수 앱을 사용하지 못할 수 있습니다. 이러한 작동 중단 가능성을 최소화하기 위해 이 시나리오에서는 다른 지역에 배포된 두 개의 함수 앱을 사용합니다.
Traffic Manager는 기본 함수 앱의 문제를 감지하고, 보조 지역의 함수 앱으로 트래픽을 자동으로 리디렉션하도록 구성됩니다. 이 함수 앱은 동일한 Azure Storage 계정 및 작업 허브를 공유합니다. 따라서 함수 앱의 상태는 손실되지 않으며 정상적으로 다시 시작될 수 있습니다. 기본 지역의 상태가 복원되면 Azure Traffic Manager는 해당 함수 앱으로의 요청 라우팅을 자동으로 시작합니다.

![시나리오 1을 보여주는 다이어그램.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario01.png)

이 배포 시나리오를 사용할 경우 다음과 같은 몇 가지 이점을 얻을 수 있습니다.

- 계산 인프라가 실패 하면 데이터 손실 없이 장애 조치 (failover) 지역에서 작업을 다시 시작할 수 있습니다.
- Traffic Manager 정상 함수 앱에 자동 장애 조치 (failover)를 자동으로 수행 합니다.
- Traffic Manager는 작동 중단이 해결된 후에 기본 함수 앱으로의 트래픽을 자동으로 다시 설정합니다.

그러나 이 시나리오를 사용할 때는 다음을 고려하세요.

- 함수 앱이 전용 App Service 계획을 사용 하 여 배포 된 경우 장애 조치 (failover) 데이터 센터에서 계산 인프라를 복제 하면 비용이 증가 합니다.
- 이 시나리오에서는 컴퓨팅 인프라의 작동 중단을 다루지만, 스토리지 계정이 계속해서 함수 앱에 대한 단일 실패 지점이 될 수 있습니다. 저장소 중단이 발생 하면 응용 프로그램의 가동 중지 시간이 발생 합니다.
- 함수 앱이 장애 조치(Failover)되면 여러 지역에 걸쳐 해당 스토리지 계정에 액세스하게 되므로 대기 시간이 증가합니다.
- 다른 지역에 있는 스토리지 서비스에 액세스하게 되면 네트워크 송신 트래픽으로 인해 더 높은 비용이 발생합니다.
- 이 시나리오는 Traffic Manager에 따라 달라집니다. [Traffic Manager 작동 방식](../../traffic-manager/traffic-manager-how-it-works.md)을 고려한다면 약간의 시간이 경과된 후에, 지속형 함수를 사용하는 클라이언트 애플리케이션이 Traffic Manager에서 함수 앱 주소를 다시 쿼리해야 할 필요가 발생할 수 있습니다.

> [!NOTE]
> Durable Functions 확장의 **v 2.3.0** 부터 동일한 저장소 계정 및 작업 허브 구성을 사용 하 여 두 함수 앱을 동시에 안전 하 게 실행할 수 있습니다. 시작할 첫 번째 앱은 다른 앱이 작업 허브 큐에서 메시지를 도용 하지 못하도록 하는 응용 프로그램 수준의 blob 임대를 획득 합니다. 이 첫 번째 응용 프로그램의 실행이 중지 되 면 해당 임대가 만료 되 고 두 번째 앱에서 가져올 수 있습니다. 그러면 작업 허브 메시지 처리를 계속 진행 합니다.
> 
> V 2.3.0 이전에는 동일한 저장소 계정을 사용 하도록 구성 된 함수 앱에서 메시지를 처리 하 고 저장소 아티팩트를 동시에 업데이트 하므로 전체 대기 시간 및 송신 비용이 훨씬 더 높습니다. 주 앱과 복제 앱에 다른 코드가 배포 되어 있는 경우에도, 두 앱에서 오 케 스트레이 터 함수 불일치로 인해 오케스트레이션이 올바르게 실행 되지 않을 수도 있습니다. 따라서 재해 복구를 위해 지역 배포를 필요로 하는 모든 앱이 지 속성 확장의 v 2.3.0 이상을 사용 하는 것이 좋습니다.

## <a name="scenario-2---load-balanced-compute-with-regional-storage"></a>시나리오 2 - 지역 스토리지에서 컴퓨팅 부하 분산

이전 시나리오에서는 컴퓨팅 인프라의 실패 경우만 다루었습니다. 스토리지 서비스가 실패하면 함수 앱이 작동 중단됩니다.
지속형 함수가 계속해서 작동되게 하기 위해 이 시나리오에서는 함수 앱이 배포되는 각 지역에서 로컬 스토리지 계정을 사용합니다.

![시나리오 2를 보여주는 다이어그램.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario02.png)

이 방법은 이전 시나리오를 약간 개선합니다.

- 함수 앱이 실패하면 Traffic Manager는 보조 지역으로 장애 조치(Failover)되도록 합니다. 그러나 함수 앱은 자체 스토리지 계정을 사용하므로 지속형 함수는 계속 작동합니다.
- 장애 조치 (failover) 중에는 함수 앱과 저장소 계정이 공동 배치 이므로 장애 조치 (failover) 지역에 추가 대기 시간이 발생 하지 않습니다.
- 저장소 계층에 오류가 발생 하면 지 속성 함수에서 오류가 발생 하며,이로 인해 장애 조치 (failover) 지역으로의 리디렉션이 트리거됩니다. 다시 말해서, 함수 앱 및 스토리지 계정이 지역별로 구분되어 있으므로 지속형 함수는 계속 작동합니다.

이 시나리오에 대한 중요한 고려 사항:

- 함수 앱이 전용 App Service 계획을 사용 하 여 배포 된 경우 장애 조치 (failover) 데이터 센터에서 계산 인프라를 복제 하면 비용이 증가 합니다.
- 현재 상태는 장애 조치 (failover) 되지 않습니다. 즉, 주 지역이 복구 될 때까지 기존 오케스트레이션과 엔터티가 효과적으로 일시 중지 되 고 사용할 수 없게 됩니다.

요약 하자면, 첫 번째와 두 번째 시나리오의 단점은 대기 시간이 유지 되 고 송신 비용이 최소화 되지만 기존 오케스트레이션과 엔터티는 가동 중지 시간 동안 사용할 수 없게 된다는 것입니다. 이러한 장단점이 허용 되는지 여부는 응용 프로그램의 요구 사항에 따라 달라 집니다.

## <a name="scenario-3---load-balanced-compute-with-grs-shared-storage"></a>시나리오 3 - GRS 공유 스토리지에서 컴퓨팅 부하 분산

이 시나리오는 첫 번째 시나리오를 수정하고 공유 스토리지 계정을 구현합니다. 주요 차이점은 지역에서 복제를 사용 하도록 설정 하 여 저장소 계정을 만드는 것입니다.
기능적으로 볼 때, 이 시나리오는 시나리오 1과 동일한 이점을 제공하지만 데이터 복구 측면에서 추가적인 이점을 제공합니다.

- GRS(지역 중복 스토리지) 및 RA-GRS(읽기 액세스 GRS)는 스토리지 계정의 가용성을 최대화합니다.
- 저장소 서비스의 지역 가동 중단이 발생 하는 경우 [보조 복제본에 대 한 장애 조치 (failover)를 수동으로 시작할](../../storage/common/storage-initiate-account-failover.md)수 있습니다. 중대한 재해로 인해 지역이 손실되는 극단적인 경우 Microsoft는 지역 장애 조치(failover)를 시작할 수 있습니다. 이 경우에 사용자의 조치가 필요하지 않습니다.
- 장애 조치 (failover)가 발생 하면 지 속성 함수의 상태가 저장소 계정의 마지막 복제까지 유지 됩니다 .이는 일반적으로 몇 분 마다 발생 합니다.

다른 시나리오와 마찬가지로 다음과 같은 중요한 고려 사항이 있습니다.

- 복제본에 대 한 장애 조치 (failover)에는 다소 시간이 걸릴 수 있습니다. 장애 조치 (failover)가 완료 되 Azure Storage 고 DNS 레코드가 업데이트 될 때까지 함수 앱이 중단 될 수 있습니다.
- 지역 복제 스토리지 계정을 사용할 경우 추가 비용이 발생합니다.
- GRS 복제는 데이터를 비동기식으로 복사 합니다. 일부 최신 트랜잭션은 복제 프로세스의 대기 시간으로 인해 손실될 수 있습니다.

![시나리오 3을 보여주는 다이어그램.](./media/durable-functions-disaster-recovery-geo-distribution/durable-functions-geo-scenario03.png)

> [!NOTE]
> 시나리오 1에 설명 된 대로이 전략과 함께 배포 된 함수 앱은 Durable Functions 확장의 **v 2.3.0** 이상을 사용 하는 것이 좋습니다.

자세한 내용은 [Azure Storage 재해 복구 및 저장소 계정 장애 조치 (failover)](../../storage/common/storage-disaster-recovery-guidance.md) 설명서를 참조 하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [에서 항상 사용 가능한 응용 프로그램을 디자인 하는 방법에 대해 자세히 알아보세요 Azure Storage](../../storage/common/geo-redundant-design.md)
