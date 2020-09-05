---
title: 클러스터 리소스 관리자를 사용 하 여 클러스터 설명
description: 클러스터 리소스 관리자에 대 한 장애 도메인, 업그레이드 도메인, 노드 속성, 노드 용량을 지정 하 여 Service Fabric 클러스터를 설명 합니다.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.custom: devx-track-csharp
ms.openlocfilehash: 5ec5db2b2fefeba3bffb7e30a77850b30dccf95e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005635"
---
# <a name="describe-a-service-fabric-cluster-by-using-cluster-resource-manager"></a>클러스터를 사용 하 여 Service Fabric 클러스터 설명 리소스 관리자
Azure Service Fabric의 클러스터 리소스 관리자 기능은 클러스터를 설명 하는 몇 가지 메커니즘을 제공 합니다.

* 장애 도메인
* 업그레이드 도메인
* 노드 속성
* 노드 용량

런타임 중에 클러스터 리소스 관리자는이 정보를 사용 하 여 클러스터에서 실행 되는 서비스의 고가용성을 보장 합니다. 이러한 중요 한 규칙을 적용 하는 동안 클러스터 내에서 리소스 사용량을 최적화 하려고 합니다.

## <a name="fault-domains"></a>장애 도메인
장애 도메인은 통합된 오류는 영역입니다. 단일 컴퓨터는 장애 도메인입니다. 전원 공급 장치 오류, 드라이브 오류, 잘못 된 NIC 펌웨어 등 다양 한 이유로 자체에서 실패할 수 있습니다. 

동일한 이더넷 스위치에 연결 된 컴퓨터는 동일한 장애 도메인에 있습니다. 즉, 단일 전원 또는 단일 위치를 공유 하는 컴퓨터입니다. 

하드웨어 오류가 중복 되기 때문에 장애 도메인은 기본적으로 계층 구조입니다. Service Fabric에서 Uri로 표시 됩니다.

Service Fabric는이 정보를 사용 하 여 서비스를 안전 하 게 저장할 수 있으므로 장애 도메인을 올바르게 설정 하는 것이 중요 합니다. 장애 도메인의 손실로 인해 (일부 구성 요소의 오류로 인해 발생) 서비스를 중지 하는 것은 서비스를 중지 하는 것을 원하지 Service Fabric. 

Azure 환경에서 Service Fabric는 환경에서 제공 하는 장애 도메인 정보를 사용 하 여 사용자 대신 클러스터의 노드를 올바르게 구성 합니다. Service Fabric 독립 실행형 인스턴스의 경우 클러스터를 설정할 때 장애 도메인이 정의 됩니다. 

> [!WARNING]
> Service Fabric에 제공 되는 장애 도메인 정보는 정확 해야 합니다. 예를 들어 Service Fabric 클러스터의 노드가 5 개의 실제 호스트에서 실행 되는 10 개의 가상 머신 내에서 실행 되 고 있다고 가정해 보겠습니다. 이 경우 10개의 가상 머신이 있지만 별도의 5개(최상위 수준) 장애 도메인만 있습니다. 실제 호스트에 장애가 발생 하는 경우 Vm에서 조정 된 오류가 발생 하므로 동일한 실제 호스트를 공유 하면 Vm은 동일한 루트 장애 도메인을 공유 합니다.  
>
> Service Fabric 노드의 장애 도메인이 변경 되지 않을 것으로 예상 합니다. [HA vm](/previous-versions/system-center/virtual-machine-manager-2008-r2/cc967323(v=technet.10))과 같이 vm의 고가용성을 보장 하는 다른 메커니즘은 Service Fabric와 충돌을 일으킬 수 있습니다. 이러한 메커니즘은 한 호스트에서 다른 호스트로 Vm의 투명 한 마이그레이션을 사용 합니다. VM 내에서 실행 중인 코드를 다시 구성 하거나 알리지 않습니다. 따라서 Service Fabric 클러스터를 실행 하는 환경으로 *지원 되지 않습니다* . 
>
> Service Fabric은 사용되는 유일한 고가용성 기술이어야 합니다. Live VM 마이그레이션과 San과 같은 메커니즘은 필요 하지 않습니다. 이러한 메커니즘을 Service Fabric와 함께 사용 하면 응용 프로그램의 가용성과 안정성이 _줄어듭니다_ . 그 이유는 추가 복잡성을 소개 하 고, 실패의 중앙 소스를 추가 하 고, Service Fabric의 기능과 충돌 하는 안정성 및 가용성 전략을 사용 하기 때문입니다. 
>
>

다음 그림에서는 장애 도메인에 영향을 주는 모든 엔터티를 색으로 표시 하 고 그 결과에 해당 하는 여러 장애 도메인을 나열 합니다. 이 예제에는 데이터센터("DC"), 랙("R") 및 블레이드("B")가 있습니다. 각 블레이드에 둘 이상의 가상 컴퓨터가 있는 경우 장애 도메인 계층 구조에 또 다른 계층이 있을 수 있습니다.

<center>

![장애 도메인을 통해 구성 된 노드][Image1]
</center>

런타임 중에 클러스터 리소스 관리자 Service Fabric 클러스터의 장애 도메인과 계획 레이아웃을 고려 합니다. 서비스에 대 한 상태 저장 복제본 또는 상태 비저장 인스턴스는 별도의 장애 도메인에 있도록 분산 됩니다. 장애 도메인에 걸쳐 서비스를 배포 하면 장애 도메인에 있는 모든 계층 수준에서 오류가 발생할 때 서비스의 가용성이 손상 되지 않습니다.

클러스터 리소스 관리자는 장애 도메인 계층 구조에 있는 계층 수를 고려 하지 않습니다. 계층 구조에서 실행 중인 서비스에 영향을 주지 않도록 하는 것이 좋습니다. 

장애 도메인 계층 구조에서 각 깊이 수준에 동일한 수의 노드가 있는 경우에 가장 적합 합니다. 장애 도메인의 "트리"가 클러스터에서 불균형 하는 경우 클러스터 리소스 관리자에서 가장 적합 한 서비스 할당을 파악 하기 어렵습니다. 불균형 장애 도메인 레이아웃은 일부 도메인의 손실이 다른 도메인 보다 서비스 가용성에 영향을 준다는 것을 의미 합니다. 결과적으로 클러스터 리소스 관리자는 두 가지 목표 사이에서 조각날 수 있습니다. 

* 이러한 "많은" 도메인에 서비스를 배치 하 여 해당 컴퓨터를 사용 하려고 합니다. 
* 도메인 손실로 인해 문제가 발생 하지 않도록 다른 도메인에 서비스를 추가 하려고 합니다. 

분산되지 않은 도메인은 어떤 모양일까요? 다음 다이어그램에서는 두 개의 서로 다른 클러스터 레이아웃을 보여 줍니다. 첫 번째 예에서는 노드가 장애 도메인 전체에 고르게 분산 됩니다. 두 번째 예제에서 한 장애 도메인은 다른 장애 도메인 보다 많은 노드를 포함 합니다. 

<center>

![두 개의 다른 클러스터 레이아웃][Image2]
</center>

Azure에서 노드를 포함 하는 장애 도메인을 선택 하는 것은 사용자를 위해 관리 됩니다. 그러나 프로 비전 하는 노드의 수에 따라 다른 노드에 더 많은 노드가 있는 장애 도메인으로 끝날 수 있습니다. 

예를 들어 클러스터에 5 개의 장애 도메인이 있지만 노드 유형 (**NodeType**)에 대해 7 개의 노드를 프로 비전 한다고 가정 합니다. 이 경우 처음 두 장애 도메인은 더 많은 노드로 종료 됩니다. 몇 개의 인스턴스만 사용 하 여 더 많은 **NodeType** 인스턴스를 계속 배포 하는 경우 문제가 더 악화 됩니다. 이러한 이유로 각 노드 형식의 노드 수는 장애 도메인 수의 배수를 나타내는 것이 좋습니다.

## <a name="upgrade-domains"></a>업그레이드 도메인
업그레이드 도메인은 클러스터 리소스 관리자 Service Fabric 클러스터의 레이아웃을 이해 하는 데 도움이 되는 또 다른 기능입니다. 업그레이드 도메인은 동시에 업그레이드 되는 노드 집합을 정의 합니다. 업그레이드 도메인은 클러스터 리소스 관리자 업그레이드와 같은 관리 작업을 이해 하 고 오케스트레이션 하는 데 도움이 됩니다.

업그레이드 도메인은 장애 도메인과 매우 유사 하지만 몇 가지 중요 한 차이점이 있습니다. 첫째, 조정 된 하드웨어 오류의 영역에서 장애 도메인을 정의 합니다. 반면 업그레이드 도메인은 정책에 의해 정의 됩니다. 환경에서 숫자를 지시 하는 대신 원하는 수를 결정 합니다. 노드 수 만큼의 업그레이드 도메인을 사용할 수 있습니다. 장애 도메인과 업그레이드 도메인의 또 다른 차이점은 업그레이드 도메인은 계층 구조가 아닙니다. 대신 간단한 태그와 유사 합니다. 

다음 다이어그램에서는 세 개의 장애 도메인에 걸쳐 스트라이프 된 세 개의 업그레이드 도메인을 보여 줍니다. 또한 서로 다른 장애 도메인 및 업그레이드 도메인에 있는 상태 저장 서비스의 세 가지 복제본에 대 한 가능한 배치를 보여 줍니다. 이러한 배치를 통해 서비스를 업그레이드 하는 동안 장애 도메인의 손실을 허용 하 고 코드 및 데이터의 복사본을 계속 사용할 수 있습니다.  

<center>

![장애 도메인 및 업그레이드 도메인으로 배치][Image3]
</center>

업그레이드 도메인의 수가 많으면 장단점이 있습니다. 업그레이드 도메인은 업그레이드의 각 단계가 보다 세분화 되어 더 적은 수의 노드 또는 서비스에 영향을 준다는 것을 의미 합니다. 한 번에 이동 해야 하는 서비스의 수를 줄이면 시스템에 변동 수준이 줄어듭니다. 이는 업그레이드 중에 발생 하는 문제의 영향을 받지 않기 때문에 안정성을 향상 시키는 경향이 있습니다. 업그레이드 도메인은 업그레이드의 영향을 처리 하기 위해 다른 노드에서 사용 가능한 버퍼 수가 더 적으면 안 됩니다. 

예를 들어 5 개의 업그레이드 도메인이 있는 경우 각각의 노드는 약 20%의 트래픽을 처리 합니다. 업그레이드를 위해 업그레이드 도메인을 중단 해야 하는 경우 일반적으로 해당 로드를 어딘가에 두어야 합니다. 업그레이드 도메인이 4 개 있기 때문에 각에는 총 트래픽의 약 25%에 대 한 공간이 있어야 합니다. 업그레이드 도메인이 많을 수록 클러스터의 노드에 더 작은 버퍼가 필요 합니다.

대신 10 개의 업그레이드 도메인이 있는 경우를 고려 합니다. 이 경우 각 업그레이드 도메인은 총 트래픽의 약 10%만 처리 합니다. 클러스터를 통해 업그레이드를 수행 하는 경우 각 도메인에는 총 트래픽의 약 11%에 대 한 공간이 있어야 합니다. 일반적으로 더 많은 업그레이드 도메인을 사용 하면 예약 된 용량이 줄어들기 때문에 더 높은 사용률에서 노드를 실행할 수 있습니다. 장애 도메인의 경우에도 마찬가지입니다.  

업그레이드 도메인을 많이 사용 하는 경우에는 업그레이드가 더 오래 걸릴 수 있다는 단점이 있습니다. Service Fabric 업그레이드 도메인이 완료 된 후 잠시 기다렸다가 다음 업데이트를 시작 하기 전에 검사를 수행 합니다. 이러한 지연은 업그레이드가 진행되기 전에 업그레이드로 인한 문제를 감지할 수 있게 합니다. 이러한 단점은 한 번에 너무 많은 서비스에 영향을 주는 잘못된 변경 사항을 방지하기 때문에 받아들일 만합니다.

업그레이드 도메인 수가 너무 적으면 부정적인 부작용이 많이 발생 합니다. 각 업그레이드 도메인의 작동이 중단 되 고 업그레이드 되는 동안 전체 용량의 상당 부분을 사용할 수 없습니다. 예를 들어 세 개의 업그레이드 도메인만 있는 경우 전체 서비스 또는 클러스터 용량의 1/3이 한 번에 수행 되는 것이 좋습니다. 워크 로드를 처리 하기 위해 클러스터의 나머지 부분에 충분 한 용량이 필요 하므로 서비스를 한 번에 한 번에 작동 하지 않는 것이 좋습니다. 이 버퍼를 유지 관리 하면 정상 작업 중에 해당 노드가 다른 경우 보다 로드 되지 않습니다. 이로 인해 서비스 실행 비용이 증가합니다.

환경에서 장애 또는 업그레이드 도메인의 총 수는 실제 제한이나 중첩 방법에 대한 제약이 없습니다. 하지만 다음과 같은 일반적인 패턴이 있습니다.

- 장애 도메인 및 업그레이드 도메인 1:1 매핑
- 노드당 하나의 업그레이드 도메인 (실제 또는 가상 OS 인스턴스)
- 장애 도메인 및 업그레이드 도메인이 일반적으로 대각선을 실행 하는 컴퓨터를 사용 하 여 행렬을 형성 하는 "스트라이프" 또는 "matrix" 모델

<center>

![장애 도메인 및 업그레이드 도메인의 레이아웃][Image4]
</center>

선택할 레이아웃에 대 한 최상의 대답은 없습니다. 각각에 장점 및 단점이 있습니다. 예를 들어 1FD:1UD 모델의 설치는 간단합니다. 노드 모델 당 하나의 업그레이드 도메인 모델은 사용자가 사용 하는 것과 매우 유사 합니다. 업그레이드 하는 동안 각 노드는 독립적으로 업데이트 됩니다. 이는 이전에 수동으로 작은 컴퓨터 집합을 업그레이드한 방법과 비슷합니다.

가장 일반적인 모델은 FD/UD 행렬로, 장애 도메인 및 업그레이드 도메인은 테이블을 형성 하 고 노드는 대각선을 따라 배치 됩니다. 이는 Azure의 Service Fabric 클러스터에서 기본적으로 사용되는 모델입니다. 노드가 많은 클러스터의 경우 모든 항목은 조밀한 행렬 패턴 처럼 보입니다.

> [!NOTE]
> Azure에서 호스트 되는 Service Fabric 클러스터는 기본 전략 변경을 지원 하지 않습니다. 독립 실행형 클러스터만 해당 사용자 지정을 제공 합니다.
>

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>장애 및 업그레이드 도메인 제약 조건 및 결과 동작
### <a name="default-approach"></a>기본 접근 방식
기본적으로 클러스터 리소스 관리자는 장애 도메인 및 업그레이드 도메인 간에 서비스 균형이 유지 됩니다. 이것이 [제약 조건](service-fabric-cluster-resource-manager-management-integration.md)으로 모델링됩니다. 장애 도메인 및 업그레이드 도메인에 대 한 제약 조건 상태: "지정 된 서비스 파티션의 경우 동일한 계층 구조에 있는 두 도메인 간의 서비스 개체 (상태 비저장 서비스 인스턴스 또는 상태 저장 서비스 복제본) 수에서 1 보다 큰 차이가 없어야 합니다."

이 제약 조건이 "최대 차이" 보증을 제공 한다고 가정해 보겠습니다. 장애 도메인 및 업그레이드 도메인에 대 한 제약 조건은 규칙을 위반 하는 특정 이동 또는 정렬을 방지 합니다.

예를 들어 5 개의 장애 도메인과 5 개의 업그레이드 도메인으로 구성 된 6 개의 노드가 있는 클러스터가 있다고 가정해 보겠습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

이제 **TargetReplicaSetSize** (또는 상태 비저장 서비스의 경우 **InstanceCount**) 값이 5 인 서비스를 만든다고 가정해 보겠습니다. 복제본은 N1-N5에 생성됩니다. 실제로 많은 서비스를 만들더라도 N6은 절대 사용되지 않습니다. 그렇지만 그 이유는 무엇일까요? N6을 선택하는 경우 현재 레이아웃 및 발생하는 상황 간의 차이를 살펴보겠습니다.

여기에서 제공 하는 레이아웃 및 장애 및 업그레이드 도메인 당 총 복제본 수는 다음과 같습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

이 레이아웃은 장애 도메인 및 업그레이드 도메인 별로 노드 측면에서 균형을 이룹니다. 또한 장애 도메인 및 업그레이드 도메인당 복제본 수를 기준으로 균형을 유지 합니다. 각 도메인에는 동일한 수의 노드 및 동일한 수의 복제본이 있습니다.

이제 N2 대신 N6를 사용하는 경우 발생하는 결과를 살펴보겠습니다. 복제본은 어떻게 배포되나요?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

이 레이아웃은 장애 도메인 제약 조건에 대 한 "최대 차이" 보증의 정의를 위반 합니다. F D 0에는 두 개의 복제본이 있고, f에는 0이 있습니다. F D 0와 f의 차이는 1의 최대값 보다 큰 총 2입니다. 제약 조건을 위반 하기 때문에 클러스터 리소스 관리자는이 정렬을 허용 하지 않습니다. 마찬가지로, N2 및 N6 (N1 및 N2 대신)를 선택한 경우 다음을 얻게 됩니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

이 레이아웃은 장애 도메인 측면에서 균형을 이룹니다. 그러나 UD0에는 복제본이 없고 UD1에는 2 개가 있으므로 upgrade 도메인 제약 조건을 위반 하 게 됩니다. 이 레이아웃도 유효 하지 않으며 클러스터 리소스 관리자에서 선택 하지 않습니다.

이 상태 저장 복제본 또는 상태 비저장 인스턴스 배포 방식은 가능한 최고의 내결함성을 제공합니다. 한 도메인의 작동이 중단 되 면 최소 복제본/인스턴스 수가 손실 됩니다. 

반면 이 방법은 지나치게 엄격하여 클러스터가 모든 리소스를 활용하지 못할 수 있습니다. 특정 클러스터 구성에서는 특정 노드를 사용할 수 없습니다. 이로 인해 Service Fabric 서비스를 사용할 수 없으므로 경고 메시지가 생성 될 수 있습니다. 이전 예제에서는 일부 클러스터 노드 (이 예제에서는 N6)를 사용할 수 없습니다. 클러스터에 노드를 추가한 경우에도 (N7-N10) 장애 도메인 및 업그레이드 도메인에 대 한 제약 조건 때문에 복제본/인스턴스는 N1 – N5에만 배치 됩니다. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |



### <a name="alternative-approach"></a>대안

클러스터 리소스 관리자는 장애 도메인 및 업그레이드 도메인에 대 한 다른 버전의 제약 조건을 지원 합니다. 최소 수준의 안전을 보장 하면서 배치를 허용 합니다. 대체 제약 조건은 다음과 같이 지정할 수 있습니다. "지정 된 서비스 파티션에 대해 도메인 간 복제본 배포는 파티션이 쿼럼 손실을 방지 해야 합니다." 이 제약 조건이 "쿼럼 안전" 보증을 제공 한다고 가정해 보겠습니다. 

> [!NOTE]
> 상태 저장 서비스의 경우 대부분의 파티션 복제본이 동시에 중지되는 상황에서 *쿼럼 손실*을 정의합니다. 예를 들어, **TargetReplicaSetSize** 이 5 인 경우 세 개의 복제본 집합은 쿼럼을 나타냅니다. 마찬가지로, **TargetReplicaSetSize** 가 6 인 경우 쿼럼에 4 개의 복제본이 필요 합니다. 두 경우 모두 파티션이 계속 정상적으로 작동 하는 경우 두 개 이상의 복제본을 동시에 종료할 수 없습니다. 
>
> 상태 비저장 서비스의 경우 *쿼럼 손실과*같은 것은 아닙니다. 상태 비저장 서비스는 대부분의 인스턴스가 동시에 다운 되는 경우에도 계속 정상적으로 작동 합니다. 이 문서의 나머지 부분에서는 상태 저장 서비스에 대해 집중적으로 설명 합니다.
>

이전 예제로 돌아갑시다. 제약 조건의 "쿼럼 안전" 버전을 사용 하면 세 가지 레이아웃을 모두 사용할 수 있습니다. 두 번째 레이아웃에서 F D 0이 실패 했거나 세 번째 레이아웃에서 UD1이 실패 한 경우에도 파티션은 쿼럼을 유지 합니다. 대부분의 복제본은 계속 작동 합니다. 이 버전의 제약 조건을 사용 하는 경우 N6는 거의 항상 활용 될 수 있습니다.

"쿼럼 안전" 접근 방식은 "최대 차이" 접근 방법 보다 더 많은 유연성을 제공 합니다. 그 이유는 거의 모든 클러스터 토폴로지에서 유효한 복제본 배포를 찾기가 더 쉽기 때문입니다. 그러나 이 접근 방식은 일부 오류가 다른 오류보다 심각하기 때문에 최고의 내결함성을 보장할 수 없습니다. 

최악의 경우에는 한 도메인 및 추가 복제본 하나에서 오류가 발생 하 여 대부분의 복제본이 손실 될 수 있습니다. 예를 들어 5 개의 복제본 또는 인스턴스와 쿼럼을 손실 하기 위해 세 가지 오류가 발생 하는 대신, 이제는 두 번의 오류만 있으면 과반수를 손실할 수 있습니다. 

### <a name="adaptive-approach"></a>적응형 접근 방식
두 방법 모두 장단점이 있으므로 이러한 두 가지 전략을 결합 하는 적응 접근 방식이 도입 되었습니다.

> [!NOTE]
> 이는 Service Fabric 버전 6.2부터 시작 하는 기본 동작입니다. 
> 
> 적응형 접근 방식은 기본적으로 "최대 차이" 논리를 사용하다가 필요할 때만 "쿼럼 안전" 논리로 전환합니다. 클러스터 리소스 관리자는 클러스터와 서비스를 구성 하는 방법을 확인 하 여 필요한 전략을 자동으로 파악 합니다.
> 
> 클러스터 리소스 관리자는 서비스에 대 한 "쿼럼 기반" 논리를 사용 해야 합니다. 두 조건 모두 true입니다.
>
> * 서비스에 대 한 **TargetReplicaSetSize** 는 장애 도메인 수와 업그레이드 도메인의 수로 균등 하 게 나눌 수 있습니다.
> * 노드 수는 장애 도메인 수와 업그레이드 도메인 수를 곱한 값 보다 작거나 같습니다.
>
> 쿼럼 손실이 상태 비저장 서비스와 관련이 없더라도 클러스터 리소스 관리자는 상태 비저장 및 상태 저장 서비스 모두에 대해이 방법을 사용 합니다.

이전 예제로 돌아가서 이제 클러스터에 8 개의 노드가 있다고 가정해 보겠습니다. 클러스터는 여전히 5 개의 장애 도메인과 5 개의 업그레이드 도메인으로 구성 되 고 해당 클러스터에서 호스트 되는 서비스의 **TargetReplicaSetSize** 값은 5로 유지 됩니다. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

필요한 모든 조건이 충족 되기 때문에 클러스터 리소스 관리자는 서비스 배포에 "쿼럼 기반" 논리를 사용 합니다. 이렇게 하면 N6-N8를 사용할 수 있습니다. 이 경우 가능한 서비스 배포 중 하나는 다음과 같습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

서비스의 **TargetReplicaSetSize** 값을 4로 줄이면 (예:) 클러스터 리소스 관리자 변경 내용이 표시 됩니다. **TargetReplicaSetSize** 은 장애 도메인 및 업그레이드 도메인의 수에 따라 dividable 되지 않으므로 "최대 차이" 논리를 사용 하 여 다시 시작 합니다. 따라서 특정 복제본 이동이 발생 하 여 N5 노드에 남아 있는 4 개의 복제본을 배포 합니다. 이렇게 하면 장애 도메인 및 업그레이드 도메인 논리의 "최대 차이" 버전이 위반 되지 않습니다. 

이전 레이아웃에서 **TargetReplicaSetSize** 값이 5이 고 N1가 클러스터에서 제거 된 경우 업그레이드 도메인 수는 4이 됩니다. 업그레이드 도메인 수가 서비스의 **TargetReplicaSetSize** 값을 더 이상 분할 하지 않으므로 클러스터 리소스 관리자 "최대 차이" 논리를 사용 하 여 시작 합니다. 결과적으로, 복제본 R1을 다시 빌드하면 장애 도메인과 업그레이드 도메인에 대 한 제약 조건을 위반 하지 않도록 N4를 설정 해야 합니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |N/A |N/A |N/A |N/A |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

## <a name="configuring-fault-and-upgrade-domains"></a>장애 및 업그레이드 도메인 구성
Azure에서 호스트 되는 Service Fabric 배포에서 장애 도메인과 업그레이드 도메인은 자동으로 정의 됩니다. Service Fabric은 Azure의 환경 정보를 선택하고 사용합니다.

사용자 고유의 클러스터를 만들거나 개발 중인 특정 토폴로지를 실행 하려는 경우 장애 도메인 및 업그레이드 도메인 정보를 직접 제공할 수 있습니다. 이 예제에서는 세 개의 데이터 센터에 걸쳐 있는 9 개 노드 로컬 개발 클러스터를 정의 합니다 (각각 3 개의 랙이 포함 됨). 이 클러스터에는 세 개의 데이터 센터에 걸쳐 스트라이프 된 세 개의 업그레이드 도메인도 있습니다. ClusterManifest.xml의 구성 예는 다음과 같습니다.

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one box/one single server -->
    <WindowsServer IsScaleMin="true">
      <NodeList>
        <Node NodeName="Node01" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType01" FaultDomain="fd:/DC01/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node02" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType02" FaultDomain="fd:/DC01/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node03" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType03" FaultDomain="fd:/DC01/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node04" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType04" FaultDomain="fd:/DC02/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node05" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType05" FaultDomain="fd:/DC02/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node06" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType06" FaultDomain="fd:/DC02/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
        <Node NodeName="Node07" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType07" FaultDomain="fd:/DC03/Rack01" UpgradeDomain="UpgradeDomain1" IsSeedNode="true" />
        <Node NodeName="Node08" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType08" FaultDomain="fd:/DC03/Rack02" UpgradeDomain="UpgradeDomain2" IsSeedNode="true" />
        <Node NodeName="Node09" IPAddressOrFQDN="localhost" NodeTypeRef="NodeType09" FaultDomain="fd:/DC03/Rack03" UpgradeDomain="UpgradeDomain3" IsSeedNode="true" />
      </NodeList>
    </WindowsServer>
  </Infrastructure>
```

이 예에서는 독립 실행형 배포에 대 한 ClusterConfig.js를 사용 합니다.

```json
"nodes": [
  {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm3",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm4",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm5",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm6",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc2/r0",
    "upgradeDomain": "UD3"
  },
  {
    "nodeName": "vm7",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD1"
  },
  {
    "nodeName": "vm8",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD2"
  },
  {
    "nodeName": "vm9",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc3/r0",
    "upgradeDomain": "UD3"
  }
],
```

> [!NOTE]
> Azure Resource Manager를 통해 클러스터를 정의 하는 경우 Azure는 장애 도메인 및 업그레이드 도메인을 할당 합니다. 따라서 Azure Resource Manager 템플릿에 있는 노드 형식 및 가상 머신 확장 집합의 정의에 장애 도메인 또는 업그레이드 도메인에 대 한 정보가 포함 되지 않습니다.
>

## <a name="node-properties-and-placement-constraints"></a>노드 속성 및 배치 제약 조건
경우에 따라 특정 워크 로드가 클러스터의 특정 노드 형식 에서만 실행 되도록 하는 것이 좋습니다. 예를 들어 일부 작업에는 Gpu 또는 Ssd가 필요할 수 있으며, 일부 작업에는 그렇지 않을 수 있습니다. 

특정 워크 로드에 대 한 하드웨어를 대상으로 하는 좋은 예는 거의 모든 n 계층 아키텍처입니다. 특정 컴퓨터는 응용 프로그램의 프런트 엔드 또는 API 서비스 측 역할을 하며 클라이언트나 인터넷에 노출 됩니다. 종종 서로 다른 하드웨어 리소스를 사용하는 다양한 머신에서 컴퓨팅 또는 스토리지 계층의 작업을 처리합니다. 이러한 컴퓨터는 대개 클라이언트 또는 인터넷에 직접 노출되지 _않습니다_. 

경우에 따라 특정 워크 로드를 특정 하드웨어 구성에서 실행 해야 하는 경우도 있습니다. Service Fabric 예를 들어:

* 기존 n 계층 응용 프로그램이 Service Fabric 환경으로 "리프트 및 이동" 되었습니다.
* 성능, 규모 또는 보안 격리를 위해 특정 하드웨어에서 작업을 실행 해야 합니다.
* 작업은 정책 또는 리소스 사용 상의 이유로 다른 워크 로드 로부터 격리 되어야 합니다.

이러한 종류의 구성을 지원 하기 위해 Service Fabric는 노드에 적용할 수 있는 태그를 포함 합니다. 이러한 태그를 *노드 속성*이라고 합니다. *배치 제약 조건은* 하나 이상의 노드 속성에 대해 선택한 개별 서비스에 연결 된 문입니다. 배치 제약 조건은 서비스를 실행해야 하는 위치를 정의합니다. 제약 조건 집합은 확장 가능 합니다. 모든 키/값 쌍이 작동할 수 있습니다. 

<center>

![클러스터 레이아웃에 대 한 다양 한 워크 로드][Image5]
</center>

### <a name="built-in-node-properties"></a>기본 제공 노드 속성
Service Fabric는 자동으로 사용할 수 있는 몇 가지 기본 노드 속성을 정의 하므로 정의 하지 않아도 됩니다. 각 노드에서 정의 된 기본 속성은 **NodeType** 및 **NodeName**입니다. 

예를 들어 배치 제약 조건을로 작성할 수 있습니다 `"(NodeType == NodeType03)"` . **NodeType** 은 일반적으로 사용 되는 속성입니다. 이는 컴퓨터의 유형과 1:1에 해당 하므로 유용 합니다. 각 머신의 종류는 기존의 n 계층 애플리케이션에서 워크로드 유형에 해당합니다.

<center>

![배치 제약 조건 및 노드 속성][Image6]
</center>

## <a name="placement-constraints-and-node-property-syntax"></a>배치 제약 조건 및 노드 속성 구문 
Node 속성에 지정 된 값은 문자열, 부울 또는 부호 있는 long 일 수 있습니다. 서비스의 문은 클러스터에서 서비스를 실행할 수 있는 위치를 제한 하기 때문에 배치 *제약 조건* 이라고 합니다. 제약 조건은 클러스터의 노드 속성에 대해 작동 하는 모든 부울 문이 될 수 있습니다. 이러한 부울 문의 유효한 선택기는 다음과 같습니다.

* 특정 문을 만들기 위한 조건부 검사:

  | 인수를 제거합니다. | 구문 |
  | --- |:---:|
  | "같음" | "==" |
  | "다음과 같지 않음" | "!=" |
  | "다음보다 큼" | ">" |
  | "다음보다 크거나 같음" | ">=" |
  | "다음보다 작음" | "<" |
  | "다음보다 작거나 같음" | "<=" |

* 그룹화 및 논리 연산에 대 한 부울 문:

  | 인수를 제거합니다. | 구문 |
  | --- |:---:|
  | "및" | "&&" |
  | "또는" | "&#124;&#124;" |
  | "아님" | "!" |
  | "단일 문인 그룹" | "()" |

다음은 기본 제약 조건 문의 몇 가지 예입니다.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

전체 배치 제약 조건 문이 "True"로 평가되는 노드에만 서비스가 배치될 수 있습니다. 정의 된 속성이 없는 노드는 속성을 포함 하는 배치 제약 조건과 일치 하지 않습니다.

ClusterManifest.xml에서 노드 형식에 대해 다음과 같은 노드 속성이 정의 되어 있다고 가정해 보겠습니다.

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

다음 예에서는 독립 실행형 배포에 대 한 ClusterConfig.json 또는 Azure에서 호스트 되는 클러스터에 대 한 Template.js를 통해 정의 된 노드 속성을 보여 줍니다. 

> [!NOTE]
> Azure Resource Manager 템플릿에서 노드 형식은 일반적으로 매개 변수화 됩니다. NodeType01가 아닌 것 같습니다 `"[parameters('vmNodeType1Name')]"` .
>

```json
"nodeTypes": [
    {
        "name": "NodeType01",
        "placementProperties": {
            "HasSSD": "true",
            "NodeColor": "green",
            "SomeProperty": "5"
        },
    }
],
```

다음과 같이 서비스에 대 한 서비스 배치 *제약 조건을* 만들 수 있습니다.

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// Add other required ServiceDescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

NodeType01의 모든 노드가 유효한 경우 제약 조건을 사용 하 여 해당 노드 형식을 선택할 수도 있습니다 `"(NodeType == NodeType01)"` .

서비스의 배치 제약 조건은 런타임 중에 동적으로 업데이트할 수 있습니다. 필요한 경우 클러스터에서 서비스를 이동 하 고, 요구 사항을 추가 및 제거 하는 등의 방법을 사용할 수 있습니다. Service Fabric은 이러한 유형의 변경이 수행 되는 경우에도 서비스를 유지 하 고 사용할 수 있게 합니다.

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

```PowerShell
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

배치 제약 조건은 명명 된 모든 서비스 인스턴스에 대해 지정 됩니다. 업데이트는 이전에 지정된 항목(덮어쓰기)에서 발생합니다.

클러스터 정의는 노드의 속성을 정의합니다. 노드의 속성을 변경 하려면 클러스터 구성으로 업그레이드 해야 합니다. 노드 속성을 업그레이드하려면 영향을 받는 각 노드를 다시 시작하여 새 속성을 보고해야 합니다. Service Fabric는 이러한 롤링 업그레이드를 관리 합니다.

## <a name="describing-and-managing-cluster-resources"></a>클러스터 리소스 설명 및 관리
모든 조정자의 가장 중요한 작업 중 하나는 클러스터에서 리소스 소비를 관리할 수 있도록 돕는 일입니다. 클러스터 리소스 관리는 여러 가지 다른 작업을 의미할 수 있습니다. 

첫째, 컴퓨터에 과부하가 걸리지 않도록 해야 합니다. 즉 컴퓨터에서 처리할 수 있는 것보다 더 많은 서비스를 실행하지 않도록 하는 것입니다. 

둘째, 서비스를 효율적으로 실행 하는 데 중요 한 분산 및 최적화가 있습니다. 비용 효율적 이거나 성능에 민감한 서비스 제공으로 일부 노드가 핫이 될 수 있지만 다른 노드가 콜드 인 경우 핫 노드는 리소스 경합 및 성능 저하를 초래 합니다. 콜드 노드는 낭비 된 리소스와 비용 증가를 나타냅니다. 

Service Fabric는 리소스를 *메트릭으로*나타냅니다. 메트릭은 서비스 패브릭에 대해 설명하려는 논리적 또는 물리적 리소스입니다. 메트릭에 대 한 예로는 "워크 Queuedepth" 또는 "MemoryInMb"가 있습니다. Service Fabric 노드에서 제어할 수 있는 물리적 리소스에 대 한 자세한 내용은 [리소스 관리](service-fabric-resource-governance.md)를 참조 하세요. 클러스터 리소스 관리자에서 사용 하는 기본 메트릭과 사용자 지정 메트릭을 구성 하는 방법에 대 한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 참조 하세요.

메트릭은 배치 제약 조건 및 노드 속성과 다릅니다. 노드 속성은 노드 자체의 정적 설명자입니다. 메트릭은 노드에서 실행 될 때 노드에 포함 되 고 서비스가 사용 하는 리소스를 설명 합니다. 노드 속성은 **Hasssd** 일 수 있으며 true 또는 false로 설정할 수 있습니다. 해당 SSD에서 사용 가능한 공간의 양과 서비스에서 사용 되는 공간은 "DriveSpaceInMb"와 같은 메트릭입니다. 

배치 제약 조건 및 노드 속성의 경우와 마찬가지로 Service Fabric 클러스터 리소스 관리자는 메트릭의 이름이 의미 하는 것을 인식 하지 못합니다. 메트릭 이름은 단순한 문자열입니다. 모호한 것일 수 있는 경우 생성 하는 메트릭 이름의 일부로 단위를 선언 하는 것이 좋습니다.

## <a name="capacity"></a>용량
모든 리소스 *분산*을 해제 한 경우 클러스터 리소스 관리자 Service Fabric 해당 용량을 초과 하지 않도록 합니다. 클러스터가 가득 차지 않았거나 워크로드가 어떤 노드보다 크지 않으면 용량 초과를 관리할 수 있습니다. 용량은 리소스 관리자 클러스터가 노드에 있는 리소스의 양을 이해 하는 데 사용 하는 또 다른 *제약 조건* 입니다. 전체 클러스터에 대해 남은 용량을 추적합니다. 

서비스 수준에서 용량과 소비량은 메트릭을 기준으로 표현됩니다. 예를 들어 메트릭은 "ClientConnections"이 고 노드는 32768의 "ClientConnections"에 대해 용량이 있을 수 있습니다. 다른 노드에는 다른 제한이 있을 수 있습니다. 해당 노드에서 실행 되는 서비스는 현재 "ClientConnections" 메트릭의 32256를 사용 하 고 있을 수 있습니다.

런타임 중에 클러스터 리소스 관리자 클러스터와 노드의 남은 용량을 추적 합니다. 용량을 추적 하기 위해 클러스터 리소스 관리자 서비스를 실행 하는 노드의 용량에서 각 서비스의 사용량을 뺍니다. 이 정보를 사용 하 여 클러스터 리소스 관리자는 노드가 용량을 초과 하지 않도록 복제본을 가져오거나 이동할 위치를 파악할 수 있습니다.

<center>

![클러스터 노드 및 용량][Image7]
</center>

```csharp
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
ServiceLoadMetricDescription metric = new ServiceLoadMetricDescription();
metric.Name = "ClientConnections";
metric.PrimaryDefaultLoad = 1024;
metric.SecondaryDefaultLoad = 0;
metric.Weight = ServiceLoadMetricWeight.High;
serviceDescription.Metrics.Add(metric);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

```PowerShell
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

클러스터 매니페스트에 정의 된 용량을 확인할 수 있습니다. ClusterManifest.xml에 대 한 예제는 다음과 같습니다.

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

다음은 독립 실행형 배포에 대 한 ClusterConfig.js를 통해 정의 된 용량과 Azure에서 호스트 되는 클러스터에 대 한 Template.js에 대 한 예입니다. 

```json
"nodeTypes": [
    {
        "name": "NodeType03",
        "capacities": {
            "ClientConnections": "65536",
        }
    }
],
```

서비스의 로드는 동적으로 변경 되는 경우가 많습니다. 복제본의 "ClientConnections" 부하가 1024에서 2048로 변경 되었다고 가정 합니다. 그런 다음 해당 메트릭에 대해 실행 중인 노드에는 512만 남은 용량이 있습니다. 이제 해당 노드에 충분 한 공간이 없기 때문에 복제본 또는 인스턴스의 배치가 잘못 되었습니다. 클러스터 리소스 관리자는 노드를 다시 용량 아래로 가져와야 합니다. 하나 이상의 복제본 또는 인스턴스를 해당 노드에서 다른 노드로 이동 하 여 용량을 초과 하는 노드의 부하를 줄입니다. 

클러스터 리소스 관리자 복제본 이동 비용을 최소화 하려고 시도 합니다. [이동 비용](service-fabric-cluster-resource-manager-movement-cost.md) 및 [균형 재조정 전략 및 규칙](service-fabric-cluster-resource-manager-metrics.md)에 대해 자세히 알아볼 수 있습니다.

## <a name="cluster-capacity"></a>클러스터 용량
Service Fabric 클러스터는 전체 클러스터가 너무 꽉 차지 리소스 관리자 어떻게 유지 되나요? 동적 로드를 사용 하면 많은 작업을 수행할 수 없습니다. 서비스는 클러스터 리소스 관리자에서 수행 하는 작업과 별개로 부하 급증을 수행할 수 있습니다. 결과적으로 미래의 스파이크가 있는 경우 현재 충분 한 여유 공간이 있는 클러스터가 알 될 수 있습니다. 

클러스터의 컨트롤 리소스 관리자 문제를 방지 하는 데 도움이 됩니다. 가장 먼저 수행할 수 있는 작업은 클러스터가 꽉 찰 수 있는 새 작업을 만드는 것을 방지 하는 것입니다.

상태 비저장 서비스를 만들고 일부 부하가 연결 되어 있다고 가정해 보겠습니다. 서비스는 "DiskSpaceInMb" 메트릭에 대해 관심을 갖습니다. 서비스는 서비스의 모든 인스턴스에 대해 5 개의 "DiskSpaceInMb" 단위를 사용 합니다. 3개의 서비스 인스턴스를 만들려고 합니다. 즉, 이러한 서비스 인스턴스를 만들기 위해 클러스터에 15 단위 "DiskSpaceInMb"가 있어야 합니다.

클러스터 리소스 관리자는 클러스터의 남은 용량을 확인할 수 있도록 각 메트릭의 용량과 소비를 지속적으로 계산 합니다. 공간이 부족 한 경우 클러스터 리소스 관리자 서비스를 만들기 위한 호출을 거부 합니다.

요구 사항은 15 단위를 사용할 수 있기 때문에 다양 한 방법으로이 공간을 할당할 수 있습니다. 예를 들어 15 개의 다른 노드에는 남은 용량의 용량이 하나 또는 5 개의 서로 다른 노드에 3 개의 남은 용량 단위가 있을 수 있습니다. 클러스터 리소스 관리자에서 작업을 다시 정렬할 수 있는 경우 3 개의 노드에서 사용할 수 있는 5 개의 단위가 있습니다. 클러스터가 거의 가득차거나 어떤 이유로 기존 서비스를 통합할 수 없는 경우가 아니면 일반적으로 클러스터를 다시 정렬할 수 있습니다.

## <a name="buffered-capacity"></a>버퍼링 된 용량
버퍼링 된 용량은 클러스터 리소스 관리자의 또 다른 기능입니다. 전체 노드 용량의 일부를 예약할 수 있습니다. 이 용량 버퍼는 업그레이드 및 노드 오류 중에 서비스를 저장 하는 데만 사용 됩니다. 

버퍼링 된 용량은 모든 노드에 대해 메트릭에 따라 전역적으로 지정 됩니다. 예약 된 용량에 대해 선택 하는 값은 클러스터에 있는 장애 도메인 및 업그레이드 도메인의 수에 대 한 함수입니다. 장애 도메인 및 업그레이드 도메인은 버퍼링 된 용량에 대해 더 적은 수를 선택할 수 있다는 의미입니다. 더 많은 도메인이 있는 경우 업그레이드 및 오류 중 사용할 수 없는 클러스터의 수가 감소할 수 있습니다. 메트릭에 대해 노드 용량을 지정한 경우에만 버퍼링 된 용량을 지정 하는 것이 좋습니다.

ClusterManifest.xml에서 버퍼링 된 용량을 지정 하는 방법의 예는 다음과 같습니다.

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

다음은 독립 실행형 배포에 대 한 ClusterConfig.json을 통해 버퍼링 된 용량을 지정 하는 방법 또는 Azure에서 호스트 되는 클러스터의 Template.js에 대 한 예입니다.

```json
"fabricSettings": [
  {
    "name": "NodeBufferPercentage",
    "parameters": [
      {
          "name": "SomeMetric",
          "value": "0.15"
      },
      {
          "name": "SomeOtherMetric",
          "value": "0.20"
      }
    ]
  }
]
```

클러스터가 메트릭에 대해 버퍼링된 용량을 소모한 경우 새 서비스를 만드는 데 실패합니다. 버퍼를 유지하기 위해 새 서비스를 만들지 않도록 방지하면 업그레이드 및 실패로 인해 노드의 용량이 초과되지 않습니다. 버퍼링 된 용량은 선택 사항 이지만 메트릭에 대 한 용량을 정의 하는 모든 클러스터에서 권장 됩니다.

클러스터 리소스 관리자이 로드 정보를 노출 합니다. 각 메트릭에 대해 다음 정보가 포함됩니다. 
- 버퍼링 된 용량 설정입니다.
- 총 용량입니다.
- 현재 사용량입니다.
- 각 메트릭이 분산 된 것으로 간주 되는지 여부를 나타냅니다.
- 표준 편차에 대 한 통계입니다.
- 가장 및 최소 로드를 포함 하는 노드  
  
다음 코드에서는 해당 출력의 예를 보여 줍니다.

```PowerShell
PS C:\Users\user> Get-ServiceFabricClusterLoadInformation
LastBalancingStartTimeUtc : 9/1/2016 12:54:59 AM
LastBalancingEndTimeUtc   : 9/1/2016 12:54:59 AM
LoadMetricInformation     :
                            LoadMetricName        : Metric1
                            IsBalancedBefore      : False
                            IsBalancedAfter       : False
                            DeviationBefore       : 0.192450089729875
                            DeviationAfter        : 0.192450089729875
                            BalancingThreshold    : 1
                            Action                : NoActionNeeded
                            ActivityThreshold     : 0
                            ClusterCapacity       : 189
                            ClusterLoad           : 45
                            ClusterRemainingCapacity : 144
                            NodeBufferPercentage  : 10
                            ClusterBufferedCapacity : 170
                            ClusterRemainingBufferedCapacity : 125
                            ClusterCapacityViolation : False
                            MinNodeLoadValue      : 0
                            MinNodeLoadNodeId     : 3ea71e8e01f4b0999b121abcbf27d74d
                            MaxNodeLoadValue      : 15
                            MaxNodeLoadNodeId     : 2cc648b6770be1bc9824fa995d5b68b1
```

## <a name="next-steps"></a>다음 단계
* 클러스터 리소스 관리자 내의 아키텍처 및 정보 흐름에 대 한 자세한 내용은 [클러스터 리소스 관리자 아키텍처 개요](service-fabric-cluster-resource-manager-architecture.md)를 참조 하세요.
* 조각 모음 메트릭을 정의 하는 것은 노드를 분산 하는 대신 노드에 로드를 통합 하는 한 가지 방법입니다. 조각 모음을 구성 하는 방법에 대 한 자세한 내용은 [메트릭 조각 모음 및 Service Fabric 로드](service-fabric-cluster-resource-manager-defragmentation-metrics.md)를 참조 하세요.
* 처음부터 시작 하 여 [Service Fabric 클러스터 리소스 관리자에 대 한 소개를 가져옵니다](service-fabric-cluster-resource-manager-introduction.md).
* 클러스터 리소스 관리자에서 클러스터를 관리 하 고 부하를 분산 하는 방법을 알아보려면 [Service Fabric 클러스터 분산](service-fabric-cluster-resource-manager-balancing.md)을 참조 하세요.

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
