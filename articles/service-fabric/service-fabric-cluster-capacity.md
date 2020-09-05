---
title: 서비스 패브릭 클러스터 용량 계획 고려 사항
description: 노드 유형, 내구성, 안정성 및 Service Fabric 클러스터를 계획할 때 고려해 야 할 기타 사항입니다.
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: pepogors
ms.custom: sfrev
ms.openlocfilehash: 28a01bbc54f752ffc1f25b57dcf2eca566aa635a
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88718104"
---
# <a name="service-fabric-cluster-capacity-planning-considerations"></a>서비스 패브릭 클러스터 용량 계획 고려 사항

클러스터 용량 계획은 모든 Service Fabric 프로덕션 환경에 중요 합니다. 주요 고려 사항은 다음과 같습니다.

* **클러스터 *노드 형식의* 초기 수 및 속성**

* Azure 인프라 내에서 VM 권한을 Service Fabric 결정 하는 **각 노드 유형의 *내구성* 수준**

* Service Fabric 시스템 서비스의 안정성 및 전체 클러스터 기능을 결정 하는 **클러스터의 *안정성* 수준**

이 문서에서는 이러한 각 영역에 대 한 중요 한 결정 사항을 안내 합니다.

## <a name="initial-number-and-properties-of-cluster-node-types"></a>클러스터 노드 형식의 초기 수 및 속성

*노드 유형은* 클러스터의 노드 (가상 머신) 집합에 대 한 크기, 수 및 속성을 정의 합니다. Service Fabric 클러스터에 정의된 모든 노드 형식은 [가상 머신 확장 집합](../virtual-machine-scale-sets/overview.md)에 매핑됩니다.

각 노드 형식은 고유한 확장 집합 이므로 독립적으로 확장 또는 축소 하 고, 서로 다른 포트 집합을 열고, 다른 용량 메트릭을 가질 수 있습니다. 노드 형식과 virtual machine scale sets 간의 관계에 대 한 자세한 내용은 [Service Fabric 클러스터 노드 형식](service-fabric-cluster-nodetypes.md)을 참조 하세요.

각 클러스터에는 Service Fabric 플랫폼 기능을 제공 하는 중요 한 시스템 서비스를 실행 하는 하나의 **주 노드 유형이**필요 합니다. 주 노드 유형을 사용 하 여 응용 프로그램을 실행할 수도 있지만 시스템 서비스를 실행 하는 용도로만 사용 하는 것이 좋습니다.

**주 노드가 아닌 노드 유형을** 사용 하 여 응용 프로그램 역할 (예: *프런트 엔드* 및 *백 엔드* 서비스)을 정의 하 고 클러스터 내에서 서비스를 물리적으로 격리할 수 있습니다. Service Fabric 클러스터에는 주 노드 유형이 0 개 이상 있을 수 있습니다.

주 노드 유형은 `isPrimary` Azure Resource Manager 배포 템플릿의 노드 유형 정의에서 특성을 사용 하 여 구성 됩니다. 노드 형식 속성의 전체 목록은 [NodeTypeDescription 개체](/azure/templates/microsoft.servicefabric/clusters#nodetypedescription-object) 를 참조 하세요. 예를 들어 사용 하는 경우 [Service Fabric 클러스터 샘플](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/) 에서 파일 *의AzureDeploy.js* 을 열고 개체에 대 한 *페이지 검색을 찾습니다* `nodetTypes` .

### <a name="node-type-planning-considerations"></a>노드 유형 계획 고려 사항

초기 노드 형식의 수는 클러스터의 목적과 응용 프로그램 및 서비스에서 실행 되는 서비스에 따라 달라 집니다. 다음과 같은 질문을 고려해보세요.

* ***애플리케이션이 여러 서비스를 보유하고 해당 서비스 중 일부를 공용 또는 인터넷에 연결해야 하나요?***

    일반적인 응용 프로그램에는 클라이언트에서 입력을 수신 하는 프런트 엔드 게이트웨이 서비스 및 프런트 엔드 서비스와 통신 하는 하나 이상의 백 엔드 서비스, 프런트 엔드 서비스와 백 엔드 서비스 간에 별도의 네트워킹이 포함 되어 있습니다. 일반적으로 이러한 경우에는 세 가지 노드 형식, 즉 하나의 주 노드 유형 및 두 개의 주 노드가 아닌 노드 유형 (프런트 엔드 및 백 엔드 서비스에 각각 하나씩)이 필요 합니다.

* ***응용 프로그램을 구성 하는 서비스에 더 많은 RAM 또는 더 높은 CPU 주기와 같은 다른 인프라 요구 사항이 있나요?***

    프런트 엔드 서비스는 인터넷에 열려 있는 포트가 있는 더 작은 Vm (D2와 같은 VM 크기)에서 실행할 수 있습니다.  계산 집약적인 백 엔드 서비스는 인터넷에 연결 되지 않는 더 큰 Vm (D4, D6, D15 같은 VM 크기)에서 실행 해야 할 수 있습니다. 이러한 서비스에 대해 서로 다른 노드 유형을 정의 하면 기본 Service Fabric Vm을 보다 효율적이 고 안전 하 게 사용할 수 있으며 이러한 Vm을 독립적으로 확장할 수 있습니다. 필요한 리소스의 양을 예측 하는 방법에 대 한 자세한 내용은 [Service Fabric 응용 프로그램에 대 한 용량 계획](service-fabric-capacity-planning.md) 을 참조 하세요.

* ***응용 프로그램 서비스 중에서 100 노드 이상으로 규모를 확장 해야 하나요?***

    단일 노드 형식은 Service Fabric 응용 프로그램에 대 한 가상 머신 확장 집합 당 100 노드 이상으로 안정적으로 확장할 수 없습니다. 100 개 이상의 노드를 실행 하려면 추가 가상 머신 확장 집합 (따라서 추가 노드 유형)이 필요 합니다.

* ***클러스터가 가용성 영역에 걸쳐 있는지 여부***

    Service Fabric는 특정 영역에 고정 된 노드 형식을 배포 하 여 [가용성 영역](../availability-zones/az-overview.md) 에 걸쳐 있는 클러스터를 지원 하 여 응용 프로그램의 고가용성을 보장 합니다. 가용성 영역 추가 노드 유형 계획 및 최소 요구 사항을 충족 해야 합니다. 자세한 내용은 [가용성 영역 전체에 걸쳐 있는 Service Fabric 클러스터의 주 노드 형식에 대 한 권장 토폴로지](service-fabric-cross-availability-zones.md#recommended-topology-for-primary-node-type-of-azure-service-fabric-clusters-spanning-across-availability-zones)를 참조 하십시오. 

클러스터를 처음 만들 때 노드 형식의 수와 속성을 결정 하는 경우 항상 클러스터가 배포 되 면 항상 주 노드가 아닌 노드 유형을 추가, 수정 또는 제거할 수 있습니다. [주 노드 유형은](service-fabric-scale-up-primary-node-type.md) 실행 중인 클러스터 에서도 수정할 수 있습니다. 그러나 이러한 작업을 수행 하려면 프로덕션 환경에서 상당한 계획 및 주의가 필요 합니다.

노드 형식 속성에 대 한 추가 고려 사항은 노드 형식의 Vm이 Azure 인프라 내에서 갖는 권한을 결정 하는 내구성 수준입니다. 클러스터에 대해 선택 하는 Vm의 크기와 개별 노드 형식에 할당 하는 인스턴스 수를 사용 하 여 다음에 설명 된 대로 각 노드 형식에 대 한 적절 한 내구성 계층을 결정 하는 데 도움을 줍니다.

## <a name="durability-characteristics-of-the-cluster"></a>클러스터의 내구성 특성

*내구성 수준은* 기본 Azure 인프라와 Service Fabric vm의 권한을 지정 합니다. 이 권한을 통해 Service Fabric는 Service Fabric 시스템 서비스 및 상태 저장 서비스에 대 한 쿼럼 요구 사항에 영향을 주는 VM 수준 인프라 요청 (예: 다시 부팅, 이미지로 다시 설치 또는 마이그레이션)을 일시 중지할 수 있습니다.

> [!IMPORTANT]
> 내구성 수준은 노드 유형별으로 설정 됩니다. 지정 되지 않은 경우에는 *청동* 계층이 사용 되지만 자동 OS 업그레이드는 제공 되지 않습니다. *실버* 또는 *골드* 내구성은 프로덕션 워크 로드에 권장 됩니다.

아래 표에 Service Fabric 내구성 계층, 요구 사항 및 affordances 나열 되어 있습니다.

| 내구성 계층  | 필요한 최소 VM 수 | 지원 되는 VM 크기                                                                  | 가상 머신 확장 집합의 업데이트                               | Azure에서 시작되는 업데이트 및 유지 관리                                                              | 
| ---------------- |  ----------------------------  | ---------------------------------------------------------------------------------- | ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| 금             | 5                              | 단일 고객 전용 전체 노드 크기 (예: L32s, GS5, G5, DS15_v2, D15_v2) | Service Fabric 클러스터에서 승인될 때까지 지연될 수 있음 | 이전 오류에서 복제본이 복구 될 때까지 추가 시간을 허용 하도록 업그레이드 도메인당 2 시간 동안 일시 중지 될 수 있습니다. |
| 은           | 5                              | 최소 50 GB 이상의 로컬 SSD를 포함 하는 단일 코어 이상의 Vm                      | Service Fabric 클러스터에서 승인될 때까지 지연될 수 있음 | 오랜 기간 동안 지연될 수 없음                                                    |
| 동          | 1                              | 50 GB 이상의 로컬 SSD를 포함 하는 Vm                                              | Service Fabric 클러스터에 의해 지연되지 않음           | 오랜 기간 동안 지연될 수 없음                                                    |

> [!WARNING]
> 청동 내구성이 있으면 자동 OS 이미지 업그레이드를 사용할 수 없습니다. [패치 오케스트레이션 응용 프로그램](service-fabric-patch-orchestration-application.md) (비 Azure 호스트 클러스터에만 해당)은 실버 이상의 내구성 수준에 *권장 되지* 않지만 Service Fabric 업그레이드 도메인에 대해 Windows 업데이트를 자동화 하는 유일한 옵션입니다.

> [!IMPORTANT]
> 내구성 수준에 관계 없이 가상 머신 확장 집합에 대 한 [할당](/rest/api/compute/virtualmachinescalesets/deallocate) 취소 작업을 실행 하면 클러스터가 삭제 됩니다.

### <a name="bronze"></a>동

Bronze 내구성으로 실행되는 노드 형식은 권한 없음이 됩니다. 즉, 상태 저장 워크 로드에 영향을 주는 인프라 작업이 중지 되거나 지연 되지 않습니다. 상태 비저장 워크 로드만 실행 하는 노드 형식에 대해 브론즈 내구성을 사용 합니다. 프로덕션 워크로드의 경우 Silver 이상을 실행하는 것이 좋습니다.

### <a name="silver-and-gold"></a>실버 및 골드

자주 규모를 확장 해야 하는 상태 저장 서비스를 호스트 하는 모든 노드 형식에 대해 실버 또는 골드 내구성을 사용 하 고, 프로세스 간소화를 위해 배포 작업을 지연 하 고 용량을 줄일 수 있습니다. 규모 확장 시나리오는 선택한 내구성 계층에 영향을 주지 않아야 합니다.

#### <a name="advantages"></a>장점

* 확장 작업에 필요한 단계 수를 줄입니다 (노드 비활성화 및 Remove-servicefabricnodestate는 자동으로 호출 됨).
* 내부 VM 크기 변경 작업 및 Azure 인프라 작업으로 인 한 데이터 손실 위험을 줄입니다.

#### <a name="disadvantages"></a>단점

* 가상 머신 확장 집합 및 기타 관련 Azure 리소스에 대 한 배포는 시간이 초과 되거나, 지연 되거나, 클러스터 또는 인프라 수준에서 발생 하는 문제에 의해 완전히 차단 될 수 있습니다.
* Azure 인프라 작업 중에 자동으로 수행되는 노드 비활성화로 인해 [복제본 수명 주기 이벤트](service-fabric-reliable-services-lifecycle.md)(예: 기본 스왑) 수가 증가합니다.
* Azure 플랫폼 소프트웨어 업데이트 또는 하드웨어 유지 관리 작업이 발생하는 기간 동안 노드를 서비스 불가능 상태로 유지합니다. 이러한 작업 중에는 노드 상태가 비활성화 중/사용 안 함으로 표시될 수 있습니다. 이로 인해 클러스터 용량이 일시적으로 감소하지만 클러스터 또는 애플리케이션의 가용성에는 영향을 주지 않아야 합니다.

#### <a name="best-practices-for-silver-and-gold-durability-node-types"></a>실버 및 골드 내구성 노드 유형에 대 한 모범 사례

실버 또는 골드 내구성으로 노드 유형 관리에 대 한 다음 권장 사항을 따르세요.

* 클러스터와 애플리케이션을 항상 정상 상태로 유지하고, 애플리케이션이 모든 [서비스 복제본 수명 주기 이벤트](service-fabric-reliable-services-lifecycle.md)(예: 빌드의 복제본에 문제가 있을 경우)에 제때에 응답하는지 확인하세요.
* VM 크기를 변경 하는 더 안전한 방법 (확장/축소)을 채택 합니다. 가상 머신 확장 집합의 VM 크기를 변경 하려면 신중한 계획과 주의가 필요 합니다. 자세한 내용은 [Service Fabric 노드 형식](service-fabric-scale-up-primary-node-type.md) 강화를 참조 하세요.
* Gold 또는 Silver 내구성 수준이 활성화된 가상 머신 확장 집합의 노드 수는 최소 5개로 유지합니다. 이 임계값 이하로 크기를 조정 하는 경우 클러스터는 오류 상태를 입력 하 고 제거 된 노드에 대해 상태 ()를 수동으로 정리 해야 `Remove-ServiceFabricNodeState` 합니다.
* 내구성 수준이 Silver 또는 Gold인 각 가상 머신 확장 집합은 Service Fabric 클러스터에서 고유한 노드 형식에 매핑되어야 합니다. 여러 가상 머신 확장 집합을 단일 노드 형식에 매핑하면 Service Fabric 클러스터와 Azure 인프라 간의 조정이 제대로 작동하지 않습니다.
* 임의의 VM 인스턴스를 삭제 하지 마세요. 항상 기능에서 가상 머신 확장 집합 소수 자릿수를 사용 하십시오. 임의의 VM 인스턴스를 삭제 하면 [업그레이드 도메인](service-fabric-cluster-resource-manager-cluster-description.md#upgrade-domains) 및 [장애 도메인](service-fabric-cluster-resource-manager-cluster-description.md#fault-domains)에 분산 된 VM 인스턴스에서 불균형 잔액을 만들 수 있습니다. 이러한 불균형은 서비스 인스턴스/서비스 복제본 간에 적절 하 게 부하를 분산 하는 시스템 기능에 부정적인 영향을 미칠 수 있습니다.
* 자동 크기 조정을 사용 하는 경우 크기 조정 (VM 인스턴스 제거) 작업은 한 번에 하나의 노드에서만 수행 됩니다. 한 번에 여러 인스턴스의 규모를 축소하는 방식은 안전하지 않습니다.
* 주 노드 유형에 서 Vm을 삭제 하거나 할당을 취소 하는 경우에는 안정성 계층에 필요한 것 보다 낮은 할당 된 Vm 수를 줄이지 마세요. 이러한 작업은 내구성 수준이 Silver 또는 Gold인 확장 집합에서 무기한 차단됩니다.

### <a name="changing-durability-levels"></a>내구성 수준 변경

특정 제약 조건 내에서 노드 형식 내구성 수준은 다음과 같이 조정할 수 있습니다.

* 실버 또는 골드의 내구성 수준을 포함 하는 노드 유형은 청동로 다운 그레이드할 수 없습니다.
* Bronze에서 Silver 또는 Gold로 업그레이드하는 데 몇 시간이 걸릴 수 있습니다.
* 내구성 수준을 변경 하는 경우 가상 머신 확장 집합 리소스의 Service Fabric 확장 구성과 Service Fabric 클러스터 리소스의 노드 유형 정의 모두에서 업데이트 해야 합니다. 이러한 값이 일치해야 합니다.

용량 계획을 클러스터의 안정성 수준으로 결정 하는 또 다른 고려 사항은 다음 섹션에 설명 된 대로 시스템 서비스 및 전체 클러스터의 안정성을 결정 합니다.

## <a name="reliability-characteristics-of-the-cluster"></a>클러스터의 안정성 특성

클러스터 *안정성 수준은* 클러스터의 주 노드 유형에 서 실행 되는 시스템 서비스 복제본 수를 결정 합니다. 복제본이 많을 수록 시스템 서비스 (결과적으로 전체 클러스터)가 더 안정적입니다.

> [!IMPORTANT]
> 안정성 수준은 클러스터 수준에서 설정 되며 주 노드 형식의 최소 노드 수를 결정 합니다. 프로덕션 워크 로드에는 은색 (5 개 이상의 노드) 이상의 안정성 수준이 필요 합니다.  

안정성 계층은 다음 값을 사용할 수 있습니다.

* **플래티넘** -9의 대상 복제본 세트 수를 사용 하 여 실행 되는 시스템 서비스
* **골드** -7 개의 대상 복제본 세트 수를 사용 하 여 시스템 서비스 실행
* **실버** -대상 복제본 세트 수를 5로 설정 하 고 시스템 서비스 실행
* **브론즈** -세 개의 대상 복제본 세트 수를 사용 하 여 시스템 서비스 실행

안정성 계층 선택과 관련한 권장 사항은 다음과 같습니다. 또한 시드 노드 수는 안정성 계층의 최소 개수로 설정됩니다.


| **노드 수** | **안정성 계층** |
| --- | --- |
| 1 | *매개 변수를 지정 하지 마세요. `reliabilityLevel` 시스템에서 매개 변수를 계산 합니다.* |
| 3 | 동 |
| 5 또는 6| 은 |
| 7 또는 8 | 금 |
| 9 이상 | 플래티넘 |

클러스터의 크기를 늘리거나 줄일 때 (모든 노드 유형에 서 VM 인스턴스의 합계) 클러스터의 안정성을 한 계층에서 다른 계층으로 업데이트 하는 것이 좋습니다. 이렇게 하면 시스템 서비스 복제본 세트 수를 변경하는 데 필요한 클러스터 업그레이드를 트리거합니다. 노드를 추가하는 것처럼 클러스터를 다르게 변경하기 전에 진행 중인 업그레이드가 완료될 때까지 기다립니다.  Service Fabric Explorer에서 또는 [Get-ServiceFabricClusterUpgrade](/powershell/module/servicefabric/get-servicefabricclusterupgrade?view=azureservicefabricps)를 실행하여 업그레이드 진행률을 모니터링할 수 있습니다.

### <a name="capacity-planning-for-reliability"></a>안정성을 위한 용량 계획

클러스터의 용량 요구는 특정 워크 로드 및 안정성 요구 사항에 따라 결정 됩니다. 이 섹션에서는 용량 계획을 시작 하는 데 도움이 되는 일반적인 지침을 제공 합니다.

#### <a name="virtual-machine-sizing"></a>가상 머신 크기 조정

**프로덕션 워크 로드의 경우 권장 되는 VM 크기 (SKU)는 최소 50 GB의 로컬 SSD, 2 개 코어 및 4 GiB의 메모리를 포함 하는 [표준 D2_V2](../virtual-machines/dv2-dsv2-series.md) (또는 동등한)입니다.** 최소 50 GB의 로컬 SSD를 권장 하지만 일부 작업 (예: Windows 컨테이너를 실행 하는 작업)에는 더 큰 디스크가 필요 합니다. 프로덕션 워크 로드에 대해 다른 [VM 크기](../virtual-machines/sizes-general.md) 를 선택할 때는 다음 제약 조건을 염두에 두어야 합니다.

- 표준 A0와 같은 부분 코어 VM 크기는 지원 되지 않습니다.
- *A-시리즈* VM 크기는 성능상의 이유로 지원 되지 않습니다.
- 우선 순위가 낮은 VM은 지원되지 않습니다.

#### <a name="primary-node-type"></a>주 노드 유형

Azure의 **프로덕션 워크 로드** 에는 최소 5 개의 주 노드 (VM 인스턴스)와 실버의 안정성 계층이 필요 합니다. 클러스터 주 노드 유형을 시스템 서비스에 전용으로 사용 하 고 배치 제약 조건을 사용 하 여 보조 노드 유형에 응용 프로그램을 배포 하는 것이 좋습니다.

Azure의 **테스트 작업** 은 최소 하나 또는 세 개의 주 노드를 실행할 수 있습니다. 하나의 노드 클러스터를 구성 하려면 `reliabilityLevel` 리소스 관리자 템플릿에서 설정이 완전히 생략 되었는지를 반드시 지정 해야 합니다 .에 대 한 빈 문자열 값을 지정 하면 `reliabilityLevel` 충분 하지 않습니다. Azure Portal를 사용 하 여 하나의 노드 클러스터를 설정 하는 경우이 구성은 자동으로 수행 됩니다.

> [!WARNING]
> 단일 노드 클러스터는 안정성이 없는 특별 한 구성으로 실행 되며 scale out은 지원 되지 않습니다.

#### <a name="non-primary-node-types"></a>주 노드가 아닌 노드 형식

주 노드가 아닌 노드 형식에 대 한 최소 노드 수는 노드 형식의 특정 [내구성 수준](#durability-characteristics-of-the-cluster) 에 따라 달라 집니다. 노드 형식에 대해 실행 하려는 응용 프로그램 또는 서비스의 복제본 수를 기반으로 하 고, 워크 로드가 상태 저장 인지 또는 상태 비저장 인지에 따라 노드 수를 계획 해야 합니다. 클러스터를 배포한 후 언제 든 지 노드 형식의 Vm 수를 늘리거나 줄일 수 있습니다.

##### <a name="stateful-workloads"></a>상태 저장 작업

Service Fabric 신뢰할 수 있는 [컬렉션 또는 신뢰할](service-fabric-choose-framework.md)수 있는 행위자를 사용 하는 상태 저장 프로덕션 작업의 경우 최소 및 대상 복제본 수를 5로 권장 합니다. 이를 통해 안정 된 상태에서 각 장애 도메인 및 업그레이드 도메인의 복제본 (복제본 세트에서)으로 끝납니다. 일반적으로 시스템 서비스에 대해 설정한 안정성 수준을 상태 저장 서비스에 사용 하는 복제본 수에 대 한 가이드로 사용 합니다.

##### <a name="stateless-workloads"></a>상태 비저장 워크 로드

상태 비저장 프로덕션 워크 로드의 경우 쿼럼을 유지 관리 하는 데 최소 지원 되는 최소 주 노드 유형 크기는 3 이지만 노드 유형 크기는 5로 권장 됩니다.

## <a name="next-steps"></a>다음 단계

클러스터를 구성 하기 전에 클러스터 `Not Allowed` [업그레이드 정책](service-fabric-cluster-fabric-settings.md) 을 검토 하 여 변경할 수 없는 시스템 구성 설정으로 인해 나중에 클러스터를 다시 만드는 것을 완화 합니다.

클러스터 계획에 대 한 자세한 내용은 다음을 참조 하세요.

* [컴퓨팅 계획 및 크기 조정](service-fabric-best-practices-capacity-scaling.md)
* [Service Fabric 애플리케이션의 용량 계획](service-fabric-capacity-planning.md)
* [재해 복구 계획](service-fabric-disaster-recovery.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
