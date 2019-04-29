---
title: 클러스터 리소스 관리자 클러스터 설명 | Microsoft Docs
description: 클러스터 리소스 관리자에 대한 장애 도메인, 업그레이드 도메인, 노드 속성 및 노드 용량을 지정하여 Service Fabric 클러스터를 설명합니다.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 55f8ab37-9399-4c9a-9e6c-d2d859de6766
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: ff291bda87ca4b2b4055e36989b035cf410b3b0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60744311"
---
# <a name="describing-a-service-fabric-cluster"></a>서비스 패브릭 클러스터 설명
서비스 패브릭 클러스터 리소스 관리자는 클러스터를 설명하는 몇 가지 메커니즘을 제공합니다. 런타임 중에 클러스터 Resource Manager는 이 정보를 사용하여 클러스터에서 실행되는 서비스의 높은 가용성을 보장합니다. 이러한 주요 규칙을 적용하는 동시에 클러스터의 리소스 사용도 최적화되도록 시도합니다.

## <a name="key-concepts"></a>주요 개념
클러스터 Resource Manager는 클러스터를 설명하는 다음과 같은 몇 가지 기능을 지원합니다.

* 장애 도메인
* 업그레이드 도메인
* 노드 속성
* 노드 용량

## <a name="fault-domains"></a>장애 도메인
장애 도메인은 통합된 오류는 영역입니다. 단일 컴퓨터는 장애 도메인입니다(전원 공급 장치 오류, 드라이브 오류, 잘못된 NIC 펌웨어 등 다양한 이유로 자체 실패할 수 있기 때문). 동일한 이더넷 스위치에 연결된 컴퓨터는 하나의 전원을 공유하는 컴퓨터 또는 단일 위치에 있는 컴퓨터와 같이 동일한 장애 도메인에 있습니다. 하드웨어 오류가 중복되는 것이 당연하므로 장애 도메인은 기본적으로 계층 구조를 가지며 Service Fabric에서 URI로 표시됩니다.

Service Fabric이 이러한 정보를 사용하여 서비스를 안전하게 배치하기 때문에 장애 도메인을 올바르게 설정하는 것이 중요합니다. Service Fabric은 일부 구성 요소의 오류로 인해 발생하는 장애 도메인의 손실 때문에 서비스가 중단될 수 있는 서비스를 배치하지 않으려고 합니다. Service Fabric은 Azure 환경에서 사용자 대신 클러스터의 노드를 올바르게 구성하기 위해 작업 환경에 제공되는 장애 도메인 정보를 사용합니다. 독립 실행형 Service Fabric의 경우 클러스터를 설정할 때 장애 도메인이 정의됩니다. 

> [!WARNING]
> Service Fabric에 제공되는 장애 도메인 정보는 정확해야 합니다. 예를 들어 Service Fabric 클러스터의 노드가 5개의 물리적 호스트에서 실행되는 10개의 가상 머신에서 실행된다고 가정해 보겠습니다. 이 경우 10개의 가상 머신이 있지만 별도의 5개(최상위 수준) 장애 도메인만 있습니다. 동일한 물리적 호스트를 공유하면 물리적 호스트에 장애가 발생하는 경우 VM에서 조정된 오류가 발생하므로 동일한 루트 장애 도메인을 공유하게 됩니다.  
>
> Service Fabric에서 노드의 장애 도메인이 변경되지 않을 것으로 예상합니다. [HA-VM](https://technet.microsoft.com/library/cc967323.aspx)과 같이 VM의 고가용성을 보장하는 다른 메커니즘은 한 호스트에서 다른 호스트로 VM의 투명한 마이그레이션을 사용하는 경우 Service Fabric과 충돌할 수 있습니다. 이러한 메커니즘은 VM 내부의 실행 중인 코드를 다시 구성하거나 알리지 않습니다. 따라서 Service Fabric 클러스터를 실행하기 위한 환경으로 **지원되지 않습니다**. Service Fabric은 사용되는 유일한 고가용성 기술이어야 합니다. 라이브 VM 마이그레이션, SAN 또는 기타와 같은 메커니즘은 필요하지 않습니다. 이러한 메커니즘은 Service Fabric과 함께 사용되는 경우 복잡성을 추가하고, 중앙 집중화된 오류 원인을 추가하며, Service Fabric과 충돌하는 안정성 및 가용성 전략을 사용하기 때문에 애플리케이션 가용성 및 안정성을 _떨어뜨립니다_. 
>
>

아래 그래픽에서는 장애 도메인에 적용되고 발생한 모든 다른 장애 도메인을 나열하는 모든 엔터티에 색을 지정했습니다. 이 예제에는 데이터센터("DC"), 랙("R") 및 블레이드("B")가 있습니다. 각 블레이드에 하나 이상의 가상 컴퓨터가 있는 경우 장애 도메인 계층 구조에 또 다른 계층이 있다고 볼 수 있습니다.

<center>

![장애 도메인을 통해 구성 된 노드][Image1]
</center>

런타임에 Service Fabric 클러스터 리소스 관리자는 클러스터의 장애 도메인을 고려하여 레이아웃을 계획합니다. 지정된 서비스에 대한 상태 저장 복제본 또는 상태 비저장 인스턴스가 별도의 장애 도메인에 있도록 분산됩니다. 장애 도메인을 통해 서비스를 분산하면 장애 도메인이 모든 계층 수준에서 실패할 때 서비스의 가용성이 손상되지 않습니다.

Service Fabric의 Cluster Resource Manager는 장애 도메인 계층 구조에 있는 계층 수를 고려하지 않습니다. 그러나 계층 구조의 일부가 손실되어도 실행되는 서비스에 영향을 주지 않도록 보장하려고 합니다. 

장애 도메인 계층 구조의 각 수준에서 노드 수가 동일한 것이 가장 좋습니다. 장애 도메인의 "트리"가 클러스터에서 분산되지 않은 경우 클러스터 리소스 관리자에서 최상의 서비스 재할당을 계산하기가 더 어려워집니다. 분산되지 않은 오류 도메인 레이아웃은 일부 도메인의 손실이 다른 도메인보다 서비스 가용성에 더 많은 영향을 미친다는 것을 의미합니다. 결과적으로 Cluster Resource Manager는 두 가지 목표 사이에서 고민합니다. 즉 서비스를 배치하여 "사용량이 많은" 도메인의 머신을 사용하려고 하며, 도메인의 손실로 인해 문제가 발생하지 않도록 다른 도메인에 서비스를 배치하려고 합니다. 

분산되지 않은 도메인은 어떤 모양일까요? 아래 다이어그램에서는 두 가지 클러스터 레이아웃을 보여 줍니다. 첫 번째 예의 경우 노드는 장애 도메인 간에 균등하게 분산됩니다. 두 번째 예의 경우 하나의 장애 도메인에는 다른 장애 도메인보다 더 많은 노드가 있습니다. 

<center>

![두 개의 다른 클러스터 레이아웃][Image2]
</center>

Azure에서는 장애 도메인에 노드가 포함된 선택 항목이 사용자를 위해 관리됩니다. 그러나 프로비전하는 노드의 수에 따라 여전히 장애 도메인에 다른 항목보다 더 많은 노드가 배치될 수 있습니다. 예를 들어 클러스터에 5개의 장애 도메인이 있지만 지정된 NodeType에 대해 7개의 노드를 프로비전한다고 가정합니다. 이 경우 처음 두 장애 도메인에 더 많은 노드가 배치됩니다. 몇 가지 인스턴스를 사용하여 더 많은 NodeTypes만 계속 배포하는 경우 문제가 악화됩니다. 이러한 이유로 각 노드 형식의 노드 수는 장애 도메인 수의 배수가 되는 것이 좋습니다.

## <a name="upgrade-domains"></a>업그레이드 도메인
업그레이드 도메인은 Service Fabric 클러스터 리소스 관리자에서 클러스터의 레이아웃을 이해하는 데 도움이 되는 또 다른 기능입니다. 업그레이드 도메인은 동시에 업그레이드된 노드 집합을 정의합니다. 업그레이드 도메인은 클러스터 리소스 관리자에서 업그레이드와 같은 관리 작업을 이해하고 조정하는 데 도움이 됩니다.

업그레이드 도메인은 장애 도메인과 많이 닮았지만 몇 가지 주요 차이점이 있습니다. 먼저 장애 도메인은 조정된 하드웨어 오류 영역으로 정의되지만, 업그레이드 도메인은 정책으로 정의됩니다. 환경에 의해 결정되지 않고 사용자가 원하는 수를 결정할 수 있습니다. 노드를 갖추는 만큼 많은 업그레이드 도메인을 갖출 수 있습니다. 장애 도메인과 업그레이드 도메인의 또 다른 차이점은 업그레이드 도메인이 계층적 구조가 아니라는 것입니다. 대신, 간단한 태그와 비슷합니다. 

다음 다이어그램에서는 3개의 업그레이드 도메인이 세 개의 장애 도메인에 스트라이프되어 있음을 보여 줍니다. 또한 상태 저장 서비스의 세 개의 서로 다른 복제본을 배치하는 한 가지 방법을 보여줍니다. 여기서 각각 다른 장애 도메인 및 업그레이드 도메인에 배치됩니다. 이 배치를 사용하면 서비스를 업그레이드하는 중에 장애 도메인이 손실되어도 코드와 데이터의 복사본이 하나씩 남아 있을 수 있습니다.  

<center>

![장애 도메인 및 업그레이드 도메인으로 배치][Image3]
</center>

많은 수의 업그레이드 도메인이 있는 경우 장단점이 있습니다. 업그레이드 도메인이 많으면 업그레이드의 각 단계가 더 세분화되어 더 적은 수의 노드 또는 서비스에 영향을 줍니다. 따라서 한 번에 이동해야 하는 서비스가 줄어들어 시스템 변동이 줄어듭니다. 그러면 서비스가 업그레이드 중에 발생된 모든 문제로 인해 영향을 받는 서비스가 줄기 때문에 안정성을 향상시키는 경향이 있습니다. 또한 업그레이드 도메인이 많아질수록 업그레이드의 영향을 처리하기 위해 다른 노드에서 사용할 수 있는 버퍼는 더 적게 필요합니다. 예를 들어 5개의 업그레이드 도메인이 있으면 각각의 노드는 트래픽의 약 20%를 처리합니다. 업그레이드를 위해 업그레이드 도메인을 중단해야 하는 경우 일반적으로 해당 로드를 다른 곳으로 이동해야 합니다. 남은 업그레이드 도메인이 4개이므로 각각에는 총 트래픽의 약 5%를 차지할 수 있는 공간이 있어야 합니다. 업그레이드 도메인이 많아질수록 클러스터의 노드에 필요한 버퍼는 더 적어집니다. 예를 들어 10개의 업그레이드 도메인을 대신 갖추는 경우를 고려합니다. 이 경우 각 UD에서는 총 트래픽의 약10%만 처리합니다. 클러스터를 통해 업그레이드가 진행될 때 각 도메인에는 총 트래픽의 약 1.1% 공간만 있으면 됩니다. 일반적으로 업그레이드 도메인을 더 많이 사용하면 예약되는 용량이 더 적어야 하므로 더 높은 사용률로 노드를 실행할 수 있습니다. 장애 도메인에서도 마찬가지입니다.  

많은 업그레이드 도메인을 가진 경우의 단점은 업그레이드 시간이 오래 걸린다는 점입니다. Service Fabric에서는 업그레이드 도메인이 완료된 후 잠시 기다렸다가 다음 업그레이드를 시작하기 전에 검사를 수행합니다. 이러한 지연은 업그레이드가 진행되기 전에 업그레이드로 인한 문제를 감지할 수 있게 합니다. 이러한 단점은 한 번에 너무 많은 서비스에 영향을 주는 잘못된 변경 사항을 방지하기 때문에 받아들일 만합니다.

업그레이드 도메인이 너무 적어도 많은 부작용이 있습니다. 개별 업그레이드 도메인이 각각 중단되고 업그레이드되는 동안 전체 용량 중 상당한 부분을 사용할 수 없습니다. 예를 들어, 세 개의 업그레이드 도메인만 있는 경우 전체 서비스 또는 클러스터 용량은 약 1/3로 저하됩니다. 워크로드를 처리하는 클러스터의 나머지 부분에 용량이 충분해야 하기 때문에 한 번에 서비스의 상당 부분이 중단되는 것은 바람직하지 않습니다. 해당 버퍼를 유지 관리하면 정상 작업 중에 이러한 노드가 다른 경우에 로드하는 것보다 더 적게 로드됩니다. 이로 인해 서비스 실행 비용이 증가합니다.

환경에서 장애 도메인 또는 업그레이드 도메인의 총 수에 대한 제한이 없고 중첩 방법에 대한 제약도 없습니다. 즉 일반적인 몇 가지 패턴이 있습니다.

- 1:1 매핑된 장애 도메인 및 업그레이드 도메인
- 노드(실제 또는 가상 OS 인스턴스) 당 하나의 업그레이드 도메인
- 장애 도메인 및 업그레이드 도메인이 대각선으로 실행되는 컴퓨터를 사용하여 행렬을 형성하는 "스트라이프" 또는 "매트릭스" 모델

<center>

![장애 도메인 및 업그레이드 도메인 레이아웃][Image4]
</center>

어떤 레이아웃을 선택하느냐에 대한 정답은 없으며, 각각 몇 가지 장단점이 있습니다. 예를 들어 1FD:1UD 모델의 설치는 간단합니다. 노드당 하나의 업그레이드 도메인 모델은 사람들이 익숙한 것과 같습니다. 업그레이드하는 동안 각 노드는 독립적으로 업데이트됩니다. 이는 이전에 수동으로 작은 컴퓨터 집합을 업그레이드한 방법과 비슷합니다. 

가장 일반적인 모델은 FD/UD 행렬이며, 여기서 FD와 UD는 테이블을 형성하고 노드는 대각선을 따라 배치됩니다. 이는 Azure의 Service Fabric 클러스터에서 기본적으로 사용되는 모델입니다. 노드가 많은 클러스터의 경우 위의 조밀한 행렬 패턴처럼 보입니다.

## <a name="fault-and-upgrade-domain-constraints-and-resulting-behavior"></a>장애 도메인 및 업그레이드 도메인 제약 조건 및 결과 동작
### <a name="default-approach"></a>*기본 접근 방식*
기본적으로 Cluster Resource Manager는 장애 도메인 및 업그레이드 도메인에서 서비스 간의 균형을 유지합니다. 이것이 [제약 조건](service-fabric-cluster-resource-manager-management-integration.md)으로 모델링됩니다. 장애 및 업그레이드 도메인 제약 조건은 다음과 같습니다. "지정된 서비스 파티션에서 계층 구조 수준이 동일한 두 도메인 간의 서비스 개체(상태 비저장 서비스 인스턴스 또는 상태 저장 서비스 복제본) 수는 1개 이상 차이가 나지 않아야 합니다." 이 제약 조건이 "최대 차이" 보증을 제공한다고 가정해 봅니다. 장애 도메인 및 업그레이드 도메인 제약 조건은 위에서 설명한 규칙을 위반하는 특정 이동 또는 정렬 작업을 방지합니다. 

한 가지 예를 살펴보겠습니다. 6개의 노드를 포함하며 5개의 장애 도메인과 5개의 업그레이드 도메인으로 구성된 클러스터가 있다고 가정해 보겠습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | | |N3 | | |
| **UD3** | | | |N4 | |
| **UD4** | | | | |N5 |

*구성 1*

이제 TargetReplicaSetSize(또는 상태 비저장 서비스를 위한 InstanceCount)가 5인 서비스를 만든다고 가정해 보겠습니다. 복제본은 N1-N5에 생성됩니다. 실제로 많은 서비스를 만들더라도 N6은 절대 사용되지 않습니다. 그렇지만 그 이유는 무엇일까요? N6을 선택하는 경우 현재 레이아웃 및 발생하는 상황 간의 차이를 살펴보겠습니다.

다음은 장애 도메인 및 업그레이드 도메인에 따른 레이아웃 및 총 복제본 수입니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** | |R2 | | | |1 |
| **UD2** | | |R3 | | |1 |
| **UD3** | | | |R4 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*레이아웃 1*


이 레이아웃은 장애 도메인 및 업그레이드 도메인당 노드의 측면에서 균형을 이룹니다. 장애 도메인 및 업그레이드 도메인에 따른 복제본의 수 측면에서도 균형을 이룹니다. 각 도메인에는 동일한 수의 노드 및 동일한 수의 복제본이 있습니다.

이제 N2 대신 N6를 사용하는 경우 발생하는 결과를 살펴보겠습니다. 복제본은 어떻게 배포되나요?

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R5 | | | | |1 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |2 |0 |1 |1 |1 |- |

*레이아웃 2*


이 레이아웃은 장애 도메인 제약 조건에 대한 “최대 차이” 보증 정의를 위반합니다. FD0에는 두 개의 복제본이 있지만 FD1에는 하나도 없기 때문에 FD0과 FD1 사이에 총 2의 차이가 있으며, 이는 최대 차이인 1보다 큽니다. 제약 조건 위반이므로 Cluster Resource Manager는 이 정렬을 허용하지 않습니다. 마찬가지로 N2 및 N6(N1 및 N2 대신)을 선택한 경우 다음에서 얻을 수 있습니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** | | | | | |0 |
| **UD1** |R5 |R1 | | | |2 |
| **UD2** | | |R2 | | |1 |
| **UD3** | | | |R3 | |1 |
| **UD4** | | | | |R4 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*레이아웃 3*


이 레이아웃은 장애 도메인 측면에서 분산됩니다. 그러나 UD0에는 복제본이 하나도 없고 UD1에는 두 개가 있으므로 업그레이드 도메인 제약 조건을 위반합니다. 따라서 이 레이아웃도 잘못되었으며 클러스터 리소스 관리자에서 선택하지 않습니다.

이 상태 저장 복제본 또는 상태 비저장 인스턴스 배포 방식은 가능한 최고의 내결함성을 제공합니다. 한 도메인이 중지되는 경우 복제본/인스턴스 손실이 최소화됩니다. 

반면 이 방법은 지나치게 엄격하여 클러스터가 모든 리소스를 활용하지 못할 수 있습니다. 특정 클러스터 구성에서는 특정 노드를 사용할 수 없습니다. 이로 인해 Service Fabric이 서비스를 배치하지 않아 경고 메시지가 발생할 수 있습니다. 이전 예제에서는 클러스터 노드 중 일부를 사용할 수 없습니다(제공된 예제에서는 N6). 해당 클러스터(N7 – N10)에 노드를 추가하더라도 장애 도메인 및 업그레이드 도메인 제약 조건 때문에 복제본/인스턴스가 N1 – N5에만 배치됩니다. 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | |N10 |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | |N9 |N5 |

*구성 2*


### <a name="alternative-approach"></a>*대안*

Cluster Resource Manager는 최소 수준의 안전을 보장하면서도 배치를 허용하는 또 다른 버전의 장애 도메인 및 업그레이드 도메인 제약 조건을 지원합니다. 대체 장애 및 업그레이드 도메인 제약 조건은 다음과 같이 설명할 수 있습니다. "지정된 서비스 파티션에서, 여러 도메인에 복제본을 배포할 때 파티션에서 쿼럼 손실 문제가 발생하지 않아야 합니다." 이 제약 조건이 "쿼럼 안전" 보증을 제공한다고 가정해 봅니다. 

> [!NOTE]
>상태 저장 서비스의 경우 대부분의 파티션 복제본이 동시에 중지되는 상황에서 *쿼럼 손실*을 정의합니다. 예를 들어 TargetReplicaSetSize가 5이면 모든 3개 복제본 집합은 쿼럼을 나타냅니다. 마찬가지로, TargetReplicaSetSize가 6이면 복제본 4개가 쿼럼에 필요합니다. 두 경우 모두 파티션이 계속해서 정상적으로 작동하기를 원한다면 동시에 중지 가능한 복제본 수는 2개 이하입니다. 상태 비저장 서비스는 대부분의 인스턴스가 동시에 중지되더라도 계속해서 정상적으로 작동하므로 *쿼럼 손실*이 없습니다. 그러므로 텍스트의 나머지 부분에서는 상태 저장 서비스를 집중적으로 살펴보겠습니다.
>

이전 예제로 돌아갑시다. 제약 조건의 "쿼럼 안전" 버전에서는 지정된 제약 조건 세 가지가 모두 유효합니다. 두 번째 레이아웃의 FD0 또는 세 번째 레이아웃의 UD1에서 오류가 발생하더라도 파티션에 여전히 쿼럼이 있기 때문입니다(복제본의 대부분은 계속 실행됨). 이 버전의 제약 조건에서는 N6를 거의 항상 활용할 수 있습니다.

"쿼럼 안전" 접근 방식은 거의 모든 클러스터 토폴로지에서 유효한 복제본 배포를 쉽게 찾을 수 있으므로 "최대 차이" 접근 방식보다 유연합니다. 그러나 이 접근 방식은 일부 오류가 다른 오류보다 심각하기 때문에 최고의 내결함성을 보장할 수 없습니다. 최악의 경우 하나의 도메인과 하나의 추가 복제본에서 오류가 발생할 때 복제본의 대부분이 손실될 수 있습니다. 예를 들어 복제본 또는 인스턴스 5개가 있는 쿼럼이 손실되려면 오류 3건이 필요했지만, 이제는 2건만으로도 대부분이 손실될 수 있습니다. 

### <a name="adaptive-approach"></a>*적응형 접근 방식*
두 접근 방식 모두 장점과 단점이 있기 때문에 두 가지 전략을 결합한 적응형 접근 방식을 도입했습니다.

> [!NOTE]
> Service Fabric 버전 6.2부터는 이것이 기본 동작입니다. 
> 
> 적응형 접근 방식은 기본적으로 "최대 차이" 논리를 사용하다가 필요할 때만 "쿼럼 안전" 논리로 전환합니다. Cluster Resource Manager는 클러스터 및 서비스가 구성된 방식을 확인하여 어떤 전략이 필요한지 자동으로 계산합니다. 지정된 서비스의 경우: *TargetReplicaSetSize를 장애 도메인 수 및 업그레이드 도메인 수로 균등하게 나눌 수 있고, **그리고** 노드 수가 (장애 도메인 수) * (업그레이드 도메인 수)보다 작거나 같으면 Cluster Resource Manager는 해당 서비스에 "쿼럼 기반" 논리를 사용해야 합니다.* 쿼럼 손실이 상태 비저장 서비스와는 관련이 없지만, Cluster Resource Manager는 상태 비저장 서비스와 상태 저장 서비스 모두에 이 접근 방식을 사용한다는 것을 기억해야 합니다.

이전 예제로 돌아가서 클러스터에 노드 8개가 있다고 가정해 봅시다(클러스터는 여전히 5개의 장애 도메인 및 업그레이드 도메인으로 구성되며 해당 클러스터에 호스트되는 서비스의 TargetReplicaSetSize는 5를 유지). 

|  | FD0 | FD1 | FD2 | FD3 | FD4 |
| --- |:---:|:---:|:---:|:---:|:---:|
| **UD0** |N1 | | | | |
| **UD1** |N6 |N2 | | | |
| **UD2** | |N7 |N3 | | |
| **UD3** | | |N8 |N4 | |
| **UD4** | | | | |N5 |

*구성 3*

필요한 모든 조건을 충족하므로 Cluster Resource Manager는 서비스 배포에 "쿼럼 기반" 논리를 사용합니다. 따라서 N6 – N8을 사용할 수 있습니다. 이 경우 가능한 서비스 배포 중 하나는 다음과 비슷합니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |R1 | | | | |1 |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | | | |0 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |2 |1 |1 |0 |1 |- |

*레이아웃 4*

예를 들어 서비스의 TargetReplicaSetSize가 4로 축소되면 Cluster Resource Manager가 해당 변경 내용을 인지하고 다시 "최대 차이" 논리를 사용합니다. TargetReplicaSetSize를 더 이상 FD 및 UD 수로 나눌 수 없기 때문입니다. 결과적으로, 장애 도메인 및 업그레이드 도메인 논리의 “최대 차이” 버전을 위반하지 않도록 N1-N5 노드에 있는 나머지 복제본 4개를 배포하기 위해 특정 복제본 이동이 발생합니다. 

네 번째 레이아웃과 TargetReplicaSetSize 5를 다시 살펴봅시다. 클러스터에서 N1이 제거되면 업그레이드 도메인 수는 4가 됩니다. 마찬가지로 Cluster Resource Manager는 다시 “최대 차이” 논리를 사용합니다. 더 이상 서비스의 TargetReplicaSetSize를 UD 수로 균등하게 나눌 수 없기 때문입니다. 결과적으로 복제본 R1은 다시 빌드되면 장애 도메인 및 업그레이드 도메인 제약 조건을 위반하지 않기 위해 N4에 도착해야 합니다.

|  | FD0 | FD1 | FD2 | FD3 | FD4 | UDTotal |
| --- |:---:|:---:|:---:|:---:|:---:|:---:|
| **UD0** |N/A |해당 사항 없음 |해당 사항 없음 |해당 사항 없음 |해당 사항 없음 |N/A |
| **UD1** |R2 | | | | |1 |
| **UD2** | |R3 |R4 | | |2 |
| **UD3** | | | |R1 | |1 |
| **UD4** | | | | |R5 |1 |
| **FDTotal** |1 |1 |1 |1 |1 |- |

*레이아웃 5*

## <a name="configuring-fault-and-upgrade-domains"></a>장애 도메인 및 업그레이드 도메인 구성
장애 도메인 및 업그레이드 도메인 정의는 Azure 호스티드 Service Fabric 배포에서 자동으로 수행됩니다. Service Fabric은 Azure의 환경 정보를 선택하고 사용합니다.

사용자 고유의 클러스터를 만들거나 개발 중인 특정 토폴로지를 실행하려면 장애 도메인 및 업그레이드 도메인 정보를 직접 제공할 수 있습니다. 이 예제에서는 각각 세 개의 랙을 가진 세 가지 "데이터 센터"에 걸쳐 있는 9개의 노드 로컬 개발 클러스터를 정의합니다. 이 클러스터에는 세 개의 해당 데이터 센터에 스트라이프된 3개의 업그레이드 도메인도 있습니다. 구성 예제는 다음과 같습니다. 

ClusterManifest.xml

```xml
  <Infrastructure>
    <!-- IsScaleMin indicates that this cluster runs on one-box /one single server -->
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

독립 실행형 배포의 경우 ClusterConfig.json을 통해

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
> Azure 리소스 관리자를 통해 클러스터를 정의할 때 Azure에서 장애 도메인과 업그레이드 도메인이 할당됩니다. 따라서 Azure 리소스 관리자 템플릿의 노드 형식 및 Virtual Machine Scale Sets의 정의에는 장애 도메인 또는 업그레이드 도메인 정보가 포함되지 않습니다.
>

## <a name="node-properties-and-placement-constraints"></a>노드 속성 및 배치 제약 조건
때로는(실제로 대부분의 경우) 특정 워크로드가 클러스터의 특정 노드 형식에서만 실행되도록 하려고 할 수 있습니다. 예를 들어, 일부 워크로드는GPU 또는 SSD가 필요할 수 있습니다. 특정 워크로드에 대한 하드웨어를 대상으로 하는 예제는 거의 모든 n계층 아키텍처입니다. 특정 머신은 프런트 엔드 또는 애플리케이션에서 제공하는 API 쪽의 역할을 하며 인터넷에 노출됩니다. 종종 서로 다른 하드웨어 리소스를 사용하는 다양한 머신에서 계산 또는 스토리지 계층의 작업을 처리합니다. 이러한 컴퓨터는 대개 클라이언트 또는 인터넷에 직접 노출되지 _않습니다_. Service Fabric은 특정 하드웨어 구성에서 특정 워크로드를 실행해야 하는 경우가 있다고 예상합니다. 예를 들면 다음과 같습니다.

* 기존 n 계층 애플리케이션은 서비스 패브릭 환경으로 "리프트 및 이동"되었습니다.
* 성능, 규모 또는 보안상의 이유 등으로 특정 하드웨어에서 워크로드를 실행하려고 합니다.
* 워크로드는 정책 또는 리소스 소비를 이유로 다른 워크로드로부터 격리되어야 합니다.

이러한 유형의 구성을 지원하기 위해 Service Fabric에는 노드에 적용될 수 있는 첫 번째 클래스 개념이 있습니다. 이러한 태그를 **노드 속성**이라고 합니다. **배치 제약 조건**은 하나 이상의 노드 속성에 대해 선택하는 개별 서비스에 연결되는 문입니다. 배치 제약 조건은 서비스를 실행해야 하는 위치를 정의합니다. 제약 조건 집합은 확장 가능하며 모든 키/값 쌍을 사용할 수 있습니다. 

<center>

![클러스터 레이아웃 다양 한 워크 로드][Image5]
</center>

### <a name="built-in-node-properties"></a>기본 제공 노드 속성
Service Fabric은 사용자가 정의할 필요 없이 자동으로 사용할 수 있는 몇 가지 기본 노드 속성을 정의합니다. 각 노드에 정의되는 기본 속성은 **NodeType** 및 **NodeName**입니다. 예를 들어 배치 제약 조건을 `"(NodeType == NodeType03)"`으로 작성할 수 있습니다. 일반적으로 NodeType이 가장 일반적으로 사용되는 속성임을 알게 됩니다. 이는 컴퓨터 종류와 1:1로 대응하므로 유용합니다. 각 머신의 종류는 기존의 n 계층 애플리케이션에서 워크로드 유형에 해당합니다.

<center>

![배치 제약 조건 및 노드 속성][Image6]
</center>

## <a name="placement-constraint-and-node-property-syntax"></a>배치 제약 조건 및 노드 속성 구문 
노드 속성에서 지정된 값은 문자열, 부울 또는 기호가 있는 long이 될 수 있습니다. 서비스가 클러스터에서 실행할 수 있는 위치를 제한하기 때문에 서비스의 문은 배치 *제약 조건*이라고 합니다. 이 제약 조건은 클러스터의 다른 노드 속성에 작동하는 부울 문일 수 있습니다. 이러한 부울 문의 유효한 선택기는 다음과 같습니다.

1) 특정 문을 만들기 위한 조건부 검사

| 문 | 구문 |
| --- |:---:|
| "같음" | "==" |
| "다음과 같지 않음" | "!=" |
| "다음보다 큼" | ">" |
| "다음보다 크거나 같음" | ">=" |
| "다음보다 작음" | "<" |
| "다음보다 작거나 같음" | "<=" |

2) 그룹화 및 논리 작업에 대한 부울 문

| 문 | 구문 |
| --- |:---:|
| "및" | "&&" |
| "또는" | "&#124;&#124;" |
| "아님" | "!" |
| "단일 문인 그룹" | "()" |

다음은 기본 제약 조건문의 몇 가지 예입니다.

  * `"Value >= 5"`
  * `"NodeColor != green"`
  * `"((OneProperty < 100) || ((AnotherProperty == false) && (OneProperty >= 100)))"`

전체 배치 제약 조건 문이 "True"로 평가되는 노드에만 서비스가 배치될 수 있습니다. 정의된 속성이 없는 노드는 해당 속성을 포함하는 배치 제약 조건과 일치하지 않습니다.

다음과 같은 노드 속성이 제시된 노드 형식에 대해 정의되었다고 가정해 보겠습니다.

ClusterManifest.xml

```xml
    <NodeType Name="NodeType01">
      <PlacementProperties>
        <Property Name="HasSSD" Value="true"/>
        <Property Name="NodeColor" Value="green"/>
        <Property Name="SomeProperty" Value="5"/>
      </PlacementProperties>
    </NodeType>
```

독립 실행형 배포의 경우 ClusterConfig.json, Azure 호스티드 클러스터의 경우 Template.json을 통해 수행됩니다. 

> [!NOTE]
> Azure 리소스 관리자 템플릿에서 노드 형식은 대개 매개 변수화됩니다. "NodeType01" 대신 "[parameters('vmNodeType1Name')]"와 비슷합니다.
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

다음과 같이 서비스에 대한 서비스 배치 *제약 조건*을 만들 수 있습니다.

C#

```csharp
FabricClient fabricClient = new FabricClient();
StatefulServiceDescription serviceDescription = new StatefulServiceDescription();
serviceDescription.PlacementConstraints = "(HasSSD == true && SomeProperty >= 4)";
// add other required servicedescription fields
//...
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceType -Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementConstraint "HasSSD == true && SomeProperty >= 4"
```

NodeType01의 모든 노드가 유효하면 "(NodeType == NodeType01)" 제약 조건을 사용하여 해당 노드 형식을 선택할 수도 있습니다.

서비스 배치 제약 조건에 대한 멋진 기능 중 하나는 런타임 도중 동적으로 업데이트할 수 있다는 것입니다. 따라서 필요한 경우 클러스터에서 서비스를 이동할 수 있으며, 요구 사항 등을 추가 및 제거할 수 있습니다. Service Fabric은 이러한 유형의 변경이 수행된 경우에도 서비스를 유지하고 사용할 수 있도록 보장합니다.

C#:

```csharp
StatefulServiceUpdateDescription updateDescription = new StatefulServiceUpdateDescription();
updateDescription.PlacementConstraints = "NodeType == NodeType01";
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateful -ServiceName $serviceName -PlacementConstraints "NodeType == NodeType01"
```

배치 제약 조건은 명명된 다른 모든 서비스 인스턴스에 대해 지정됩니다. 업데이트는 이전에 지정된 항목(덮어쓰기)에서 발생합니다.

클러스터 정의는 노드의 속성을 정의합니다. 노드 속성을 변경하려면 클러스터 구성을 업그레이드해야 합니다. 노드 속성을 업그레이드하려면 영향을 받는 각 노드를 다시 시작하여 새 속성을 보고해야 합니다. 이러한 롤링 업그레이드는 Service Fabric에서 관리됩니다.

## <a name="describing-and-managing-cluster-resources"></a>클러스터 리소스 설명 및 관리
모든 조정자의 가장 중요한 작업 중 하나는 클러스터에서 리소스 소비를 관리할 수 있도록 돕는 일입니다. 클러스터 리소스 관리는 여러 가지 다른 작업을 의미할 수 있습니다. 첫째, 컴퓨터에 과부하가 걸리지 않도록 해야 합니다. 즉 컴퓨터에서 처리할 수 있는 것보다 더 많은 서비스를 실행하지 않도록 하는 것입니다. 둘째, 서비스를 효율적으로 실행하는 데 중요한 분산 및 최적화가 있습니다. 비용 효율적이거나 성능에 민감한 서비스 제품은 일부 노드가 핫 노드인 반면 다른 노드는 콜드 노드이도록 허용하지 않습니다. 핫 노드는 리소스 경합 및 성능 저하를 일으키고, 콜드 노드는 리소스 낭비와 증가한 비용 증가를 나타냅니다. 

Service Fabric은 리소스를 `Metrics`으로 나타냅니다. 메트릭은 서비스 패브릭에 대해 설명하려는 논리적 또는 물리적 리소스입니다. 메트릭의 예로는 "WorkQueueDepth" 또는 "MemoryInMb" 등이 있습니다. Service Fabric에서 노드에 대해 관리할 수 있는 물리적 리소스에 대한 자세한 내용은 [리소스 관리](service-fabric-resource-governance.md)를 참조하세요. 사용자 지정 메트릭 및 해당 용도를 구성하는 방법에 대한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 참조하세요.

메트릭은 배치 제약 조건 및 노드 속성이 서로 다릅니다. 노드 속성은 노드 자체의 정적 설명자입니다. 메트릭은 노드에 있고 노드에서 실행될 때 서비스에서 사용하는 리소스를 설명합니다. 노드 속성은 "HasSSD"일 수 있고 true 또는 false로 설정될 수 있습니다. 해당 SSD에서 사용할 수 있는 공간의 양과 서비스에서 사용되는 양은 "DriveSpaceInMb"와 같은 메트릭일 수 있습니다. 

배치 제약 조건 및 노드 속성의 경우와 같이 Service Fabric Cluster Resource Manager가 메트릭 이름의 의미를 이해하지 못한다는 점에 유의합니다. 메트릭 이름은 단순한 문자열입니다. 만든 메트릭 이름이 모호할 경우 단위를 그 일부로 선언하는 것이 좋습니다.

## <a name="capacity"></a>용량
모든 리소스 *분산*을 해제한 경우에도 Service Fabric의 클러스터 리소스 관리자는 여전히 노드가 해당 용량을 초과하지 않도록 합니다. 클러스터가 가득 차지 않았거나 워크로드가 어떤 노드보다 크지 않으면 용량 초과를 관리할 수 있습니다. 용량은 Cluster Resource Manager가 노드에 있는 리소스의 양을 이해하기 위해 사용하는 또 다른 *제약 조건*입니다. 전체 클러스터에 대해 남은 용량을 추적합니다. 서비스 수준에서 용량과 소비량은 메트릭을 기준으로 표현됩니다. 예를 들어 메트릭이 "ClientConnections"이고 지정된 노드에 대한 "ClientConnections" 용량이 32768일 수 있습니다. 다른 노드에는 다른 제한이 있을 수 있습니다. 해당 노드에서 실행 중인 어떤 서비스에서 현재 "ClientConnections" 메트릭 중 32256을 사용하고 있다고 말할 수 있습니다.

런타임 중에 클러스터 리소스 관리자는 클러스터 및 노드에서 남아 있는 용량을 추적합니다. 용량을 추적하기 위해 클러스터 리소스 관리자는 서비스에서 실행되는 노드의 용량에서 각 서비스의 사용량을 뺍니다. 이 정보를 통해 Service Fabric Cluster Resource Manager는 노드가 용량을 초과하지 않도록 복제본을 배치하거나 이동시킬 위치를 결정할 수 있습니다.

<center>

![클러스터 노드 및 용량][Image7]
</center>

C#:

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

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton –Metric @("ClientConnections,High,1024,0)
```

클러스터 매니페스트에 정의된 용량을 확인할 수 있습니다.

ClusterManifest.xml

```xml
    <NodeType Name="NodeType03">
      <Capacities>
        <Capacity Name="ClientConnections" Value="65536"/>
      </Capacities>
    </NodeType>
```

독립 실행형 배포의 경우 ClusterConfig.json, Azure 호스티드 클러스터의 경우 Template.json을 통해 수행됩니다. 

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

일반적으로 서비스의 로드는 동적으로 변경됩니다. 복제본의 "ClientConnections"로드가 1024에서 2048로 변경되었지만, 실행 중인 노드에는 해당 메트릭에 대해 512 용량만 남아 있습니다. 이제 해당 노드에 있는 충분한 공간이 없기 때문에 해당 복제본 또는 인스턴스의 배치가 유효하지 않습니다. 클러스터 리소스 관리자가 시작되고 노드를 용량 이하로 다시 가져와야 합니다. 하나 이상의 복제본 또는 인스턴스를 해당 노드에서 다른 노드로 이동하여 용량을 초과한 노드의 로드를 줄입니다. 복제본을 이동하는 경우 Cluster Resource Manager에서는 이러한 움직임의 비용을 최소화하려고 합니다. 이동 비용은 [이 문서](service-fabric-cluster-resource-manager-movement-cost.md)에서 설명하고, 클러스터 리소스 관리자의 재분산 전략 및 규칙에 대한 자세한 내용은 [여기](service-fabric-cluster-resource-manager-metrics.md)에서 설명하고 있습니다.

## <a name="cluster-capacity"></a>클러스터 용량
그렇다면 Service Fabric 클러스터 리소스 관리자에서 전체 클러스터가 가득 차지 않도록 유지하는 방법은 어떨까요? 물론 동적 로드로 수행할 수 있는 작업은 많지 않습니다. 서비스에서는 Cluster Resource Manager에서 수행한 작업과 독립적으로 해당 부하가 급증할 수 있습니다. 즉, 오늘은 여유가 많은 클러스터가 내일은 수요가 많아져 전력이 부족해질 수 있습니다. 즉 문제를 방지하기 위해 변환된 몇 가지 컨트롤이 있습니다. 가장 먼저 할 수 있는 작업은 클러스터가 꽉 차도록 만드는 워크로드가 생성되지 않도록 하는 것입니다.

상태 비저장 서비스를 만들고 여기에는 이 서비스와 연결되는 일부 로드가 있습니다. 서비스가 "DiskSpaceInMb" 메트릭을 고려하지 않는다고 가정해 봅니다. 서비스의 모든 인스턴스에 대해 5단위의 "DiskSpaceInMb"를 사용하게 된다고 가정하겠습니다. 3개의 서비스 인스턴스를 만들려고 합니다. 잘하셨습니다. 즉, 이러한 서비스 인스턴스를 만들기 위해서는 클러스터에서 15단위의 "DiskSpaceInMb"가 있어야 합니다. 클러스터 리소스 관리자에서 각 메트릭의 용량 및 사용량을 지속적으로 계산하여 클러스터에 남아 있는 용량을 결정할 수 있습니다. 공간이 충분하지 않으면 클러스터 리소스 관리자에서 서비스 만들기 호출을 거부합니다.

요구 사항은 사용할 수 있는 15단위이기 때문에 여러 가지 방법으로 이 공간을 할당할 수 있습니다. 예를 들어, 다른 15개의 노드에서 남은 1단위의 용량이나 다른 5개의 노드에서 남은 3단위의 용량일 수 있습니다. 클러스터 리소스 관리자에서 작업을 다시 정렬하여 3개 노드에서 5단위를 사용할 수 있으면 서비스를 배치합니다. 클러스터가 거의 가득차거나 어떤 이유로 기존 서비스를 통합할 수 없는 경우가 아니면 일반적으로 클러스터를 다시 정렬할 수 있습니다.

## <a name="buffered-capacity"></a>버퍼링된 용량
버퍼링된 용량은 클러스터 리소스 관리자의 또 다른 기능입니다. 전체 노드 용량의 일부를 예약할 수 있습니다. 이 용량 버퍼는 업그레이드 및 노드가 실패하는 동안에만 서비스를 배치하는 데 사용됩니다. 버퍼링된 용량은 모든 노드에 대해 메트릭별로 전역으로 지정됩니다. 예약된 용량에 대해 선택하는 값은 클러스터에 있는 장애 도메인 및 업그레이드 도메인 수의 함수입니다. 장애 도메인과 업그레이드 도메인이 증가하면 버퍼링된 용량에 대해 낮은 값을 선택할 수 있습니다. 더 많은 도메인이 있는 경우 업그레이드 및 오류 중 사용할 수 없는 클러스터의 수가 감소할 수 있습니다. 버퍼링된 용량 지정은 메트릭에 노드 용량을 지정한 경우에만 의미가 있습니다.

버퍼링된 용량을 지정하는 방법의 예는 다음과 같습니다.

ClusterManifest.xml

```xml
        <Section Name="NodeBufferPercentage">
            <Parameter Name="SomeMetric" Value="0.15" />
            <Parameter Name="SomeOtherMetric" Value="0.20" />
        </Section>
```

독립 실행형 배포의 경우 ClusterConfig.json 또는 Azure 호스티드 클러스터의 경우 Template.json를 통해 수행됩니다.

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

클러스터가 메트릭에 대해 버퍼링된 용량을 소모한 경우 새 서비스를 만드는 데 실패합니다. 버퍼를 유지하기 위해 새 서비스를 만들지 않도록 방지하면 업그레이드 및 실패로 인해 노드의 용량이 초과되지 않습니다. 버퍼링된 용량은 선택적이지만 메트릭에 대한 용량을 정의하는 모든 클러스터에 권장됩니다.

클러스터 리소스 관리자는 이 로드 정보를 공개합니다. 각 메트릭에 대해 다음 정보가 포함됩니다. 
  - 버퍼링된 용량 설정
  - 총 용량
  - 현재 사용량
  - 각 메트릭이 분산된 것으로 간주되는지 여부
  - 표준 편차에 대한 통계
  - 최대 및 최소 로드가 있는 노드  
  
해당 출력의 예제는 다음과 같습니다.

```posh
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
* 클러스터 Resource Manager 내의 아키텍처 및 정보 흐름에 대 한 내용은 체크 아웃 [이 문서](service-fabric-cluster-resource-manager-architecture.md)
* 조각 모음 메트릭 정의는 노드의 부하를 분배하는 대신 통합하는 한 가지 방법입니다. 조각 모음을 구성하는 방법에 대해 알아보려면 [이 문서](service-fabric-cluster-resource-manager-defragmentation-metrics.md)
* 처음부터 시작 및 [서비스 패브릭 클러스터 Resource Manager 소개](service-fabric-cluster-resource-manager-introduction.md)
* 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-domains.png
[Image2]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-uneven-fault-domain-layout.png
[Image3]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domains-with-placement.png
[Image4]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-fault-and-upgrade-domain-layout-strategies.png
[Image5]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-layout-different-workloads.png
[Image6]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-placement-constraints-node-properties.png
[Image7]:./media/service-fabric-cluster-resource-manager-cluster-description/cluster-nodes-and-capacity.png
