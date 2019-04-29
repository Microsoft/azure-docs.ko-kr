---
title: 애플리케이션 시나리오 및 설계 | Microsoft Docs
description: 서비스 패브릭의 클라우드 애플리케이션 범주 개요 상태 저장 및 상태 비저장 서비스를 사용하는 애플리케이션 설계에 대해 논의합니다.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: ''
ms.assetid: 3a8ca6ea-b8e9-4bc3-9e20-262437d2528e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 7/02/2017
ms.author: atsenthi
ms.openlocfilehash: c9b2f9ac131e71b7c6b37ed85568adc0c3978dc2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60621215"
---
# <a name="service-fabric-application-scenarios"></a>서비스 패브릭 애플리케이션 시나리오
Azure 서비스 패브릭은 다양한 유형의 비즈니스 애플리케이션 및 서비스를 쓰고 실행할 수 있도록 하는 안정적이고 유연한 플랫폼을 제공합니다. 이러한 애플리케이션 및 마이크로 서비스는 상태를 저장하지 않을 수도 있고 상태를 저장할 수도 있으며, 가상 머신 간에 리소스를 분산하여 효율성을 극대화합니다. Service Fabric의 고유한 아키텍처는 애플리케이션에서 거의 실시간으로 데이터 분석, 메모리 내 계산, 병렬 트랜잭션 및 이벤트 처리가 가능합니다. 리소스 요구 사항의 변화에 따라 애플리케이션을 간단하게 확장 또는 축소(실제 내부 또는 외부)할 수 있습니다.

Azure의 서비스 패브릭 플랫폼은 다음과 같은 애플리케이션 범주에 이상적입니다.

* **가용성이 높은 서비스**: Service Fabric 서비스는 여러 보조 서비스 복제본을 만들어 신속한 장애 조치(failover)를 제공합니다. 노드, 프로세스 또는 개별 서비스가 하드웨어 또는 다른 오류로 인해 중단되면 최소한의 서비스 손실로 보조 복제본 중 하나가 주 복제본으로 승격됩니다.
* **확장성 있는 서비스**: 개별 서비스를 분할하여 클러스터 전체로 상태를 확장할 수 있습니다. 또한 개별 서비스를 즉시 생성하고 제거할 수 있습니다. 서비스는 리소스 요구 사항에 따라 몇 개 노드의 적은 인스턴스에서 많은 노드의 수천 개 인스턴스로 신속하고 간단하게 규모를 확장했다가 다시 축소할 수 있습니다. 서비스 패브릭을 사용하여 서비스를 빌드하고 전체 수명 주기를 관리할 수 있습니다.
* **비정적 데이터에 대한 계산**: Service Fabric을 사용하여 데이터, 입력/출력 및 컴퓨팅 집약적인 상태 저장 애플리케이션을 빌드할 수 있습니다. 서비스 패브릭을 사용하면 애플리케이션에서 처리(계산) 및 데이터의 공동 장소를 만들 수 있습니다. 일반적으로 애플리케이션이 데이터에 액세스해야 하는 경우 외부 데이터 캐시 또는 저장소 계층과 연관된 네트워크 대기 시간이 있습니다. 대기 시간이 제거된 상태 저장 서비스 패브릭 서비스를 사용하면 읽고 쓰는 기능을 더 사용할 수 있습니다. 예를 들어 100밀리초 보다 적은 왕복 시간을 요구하는 고객에게 실시간 권장 사항을 선택하도록 하는 애플리케이션이 있다고 가정합니다. 서비스 패브릭 서비스의 대기 시간 및 성능 특징(권장 사항 선택의 계산이 데이터 및 규칙과 함께 배치됨)은 원격 저장소에서 필요한 데이터를 인출하도록 하는 표준 구현 모델에 비해 사용자에게 뛰어난 응답을 제공합니다.  
* **세션 기반 대화형 애플리케이션**: 온라인 게임 또는 인스턴트 메시징 같은 애플리케이션의 읽기 및 쓰기 대기 시간이 짧아야 하는 경우 Service Fabric이 유용합니다. Service Fabric을 사용하면 상태 비저장 앱에 필요한 것처럼 별도의 저장소 또는 캐시를 만들 필요 없이 이러한 대화형 상태 저장 애플리케이션을 빌드할 수 있습니다. (대기 시간이 증가하고 잠재적으로 일관성 문제가 시작됩니다.)
* **데이터 분석 및 워크플로**: Service Fabric은 읽기 및 쓰기 속도가 빠르기 때문에 이벤트 또는 데이터 스트림을 안정적으로 처리해야 하는 애플리케이션에 적합합니다. 또한 서비스 패브릭을 사용하면 결과가 손실 없이 다음 처리 단계에 안정적으로 전달되어야 하는 처리 파이프라인을 설명하는 애플리케이션을 사용할 수 있습니다. 데이터 일관성 및 계산 보장이 필수적인 트랜잭션 및 재무 시스템을 포함합니다.
* **데이터 수집, 처리 및 IoT**: Service Fabric은 큰 규모를 처리하고 상태 저장 서비스를 통해 낮은 대기 시간을 가지므로 디바이스에 대한 데이터 및 계산이 공동 배치된 수백만 대의 디바이스의 데이터 처리에 이상적입니다.
[BMW](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/24/service-fabric-customer-profile-bmw-technology-corporation/), [Schneider Electric](https://blogs.msdn.microsoft.com/azureservicefabric/2016/08/05/service-fabric-customer-profile-schneider-electric/) 및 [Mesh Systems](https://blogs.msdn.microsoft.com/azureservicefabric/2016/06/20/service-fabric-customer-profile-mesh-systems/)를 포함한 Service Fabric을 사용하여 IoT 시스템을 구축한 여러 고객을 살펴봤습니다.

## <a name="application-design-case-studies"></a>애플리케이션 디자인 사례 연구
Service Fabric을 사용하여 애플리케이션을 디자인하는 방법을 보여 주는 여러 사례 연구가 [Service Fabric 팀 블로그](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/) 및 [마이크로 서비스 솔루션 사이트](https://azure.microsoft.com/solutions/microservice-applications/)에 게시되어 있습니다.

## <a name="design-applications-composed-of-stateless-and-stateful-microservices"></a>상태 비저장 및 상태 저장 마이크로 서비스로 구성된 애플리케이션 설계
Azure Cloud Service 작업자 역할이 있는 애플리케이션을 구축하는 것은 상태 비저장 서비스의 예입니다. 이와는 반대로 상태 저장 마이크로 서비스는 요청 및 응답 이후에도 믿을 수 있는 상태를 유지합니다. 복제에서 지원하는 트랜잭션 보장을 제공하는 간단한 API를 통해 상태의 높은 가용성 및 일관성을 제공합니다. 서비스 패브릭의 상태 저장 서비스는 고가용성을 보편화하여, 데이터베이스 및 기타 데이터 저장소가 아닌 모든 유형의 애플리케이션에 가져옵니다. 자연적 진행 방향입니다. 애플리케이션은 이미 NoSQL 데이터베이스에 대한 고가용성을 위해 순수하게 관계형 데이터베이스를 사용하지 않습니다. 이제 애플리케이션 자체에서는 안정성, 일관성, 또는 가용성을 유지하면서 추가 성능 향상을 위해 자체 내에서 관리되는 "핫" 상태 및 데이터를 보유할 수 있습니다.

마이크로 서비스로 구성된 애플리케이션을 구축할 경우 일반적으로 상태 비저장 및 상태 저장 비즈니스 중간 계층 서비스를 호출하는 상태 비저장 웹앱(ASP.NET, Node.js 등) 조합이 있으며 모두 서비스 패브릭 명령을 사용하여 동일한 서비스 패브릭 클러스터에 배포됩니다. 이러한 서비스 각각은 규모, 안정성 및 리소스 사용과 관련하여 독립적이고 개발 및 수명 주기 관리에서 민첩성을 크게 향상시킵니다.

상태 저장 마이크로 서비스는 순수한 상태 비저장 애플리케이션의 가용성 및 대기 시간 요구 사항을 해결하기 위해 일반적으로 필요했던 추가 큐 및 캐시에 대한 필요성을 제거하기 때문에 애플리케이션 설계를 간소화합니다. 상태 저장 서비스는 기본적으로 가용성이 높고 대기 시간이 낮기 때문에 전체적으로 애플리케이션에서 관리하는 이동 부분이 적습니다. 아래 다이어그램에는 상태 비저장 애플리케이션과 상태 저장 애플리케이션 간의 설계 차이점을 보여줍니다. [Reliable Services](service-fabric-reliable-services-introduction.md) 및 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 프로그래밍 모델을 활용하여 상태 저장 서비스에서 애플리케이션 복잡성을 줄이고 높은 처리량과 낮은 대기 시간을 달성합니다.

## <a name="an-application-built-using-stateless-services"></a>상태 비저장 서비스를 사용하여 빌드된 애플리케이션
![상태 비저장 서비스를 사용하는 애플리케이션][Image1]

## <a name="an-application-built-using-stateful-services"></a>상태 저장 서비스를 사용하여 빌드된 애플리케이션
![상태 비저장 서비스를 사용하는 애플리케이션][Image2]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>다음 단계

* [고객 사례 연구](https://blogs.msdn.microsoft.com/azureservicefabric/tag/customer-profile/)에 대해 알아보세요.
* [패턴 및 시나리오](service-fabric-patterns-and-scenarios.md)에 대해 자세히 알아보세요.

* Service Fabric [Reliable Services](service-fabric-reliable-services-quick-start.md) 및 [Reliable Actors](service-fabric-reliable-actors-get-started.md) 프로그래밍 모델을 사용하여 상태 비저장 및 상태 저장 서비스 구축을 시작합니다.
* 또한 다음 항목을 참조하세요.
  * [마이크로 서비스 정보](service-fabric-overview-microservices.md)
  * [서비스 상태 정의 및 관리](service-fabric-concepts-state.md)
  * [서비스 패브릭 서비스의 가용성](service-fabric-availability-services.md)
  * [서비스 패브릭 서비스 크기 조정](service-fabric-concepts-scalability.md)
  * [서비스 패브릭 서비스 분할](service-fabric-concepts-partitioning.md)

[Image1]: media/service-fabric-application-scenarios/AppwithStatelessServices.jpg
[Image2]: media/service-fabric-application-scenarios/AppwithStatefulServices.jpg
