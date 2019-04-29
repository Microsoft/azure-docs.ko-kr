---
title: Resource Manager 아키텍처 | Microsoft Docs
description: 서비스 패브릭 클러스터 리소스 관리자의 아키텍처 개요
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 6c4421f9-834b-450c-939f-1cb4ff456b9b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bfbdb05e8d2764d2b878e22d236cae30519da176
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113974"
---
# <a name="cluster-resource-manager-architecture-overview"></a>클러스터 리소스 관리자 아키텍처 개요
Service Fabric Cluster Resource Manager는 클러스터에서 실행되는 중앙 서비스입니다. 이 서비스는 특히 리소스 소비 및 배치 규칙 측면에서 클러스터에 있는 서비스의 원하는 상태를 관리합니다. 

클러스터의 리소스를 관리하려면 Service Fabric Cluster Resource Manager에게 일부 정보가 있어야 합니다.

- 현재 존재하는 서비스
- 각 서비스의 현재(또는 기본) 리소스 소비량 
- 나머지 클러스터 용량 
- 클러스터의 노드 용량 
- 각 노드에서 사용되는 리소스의 양

주어진 서비스의 리소스 사용은 지남에 따라 변할 수 있으며, 서비스는 일반적으로 하나 이상의 리소스 유형에 대해 주의를 기울입니다. 다른 서비스에 있는 실제 물리적 리소스와 물리적 리소스를 모두 측정하고 있을 수 있습니다. 서비스는 메모리 및 디스크 소비와 같은 물리적 메트릭을 추적할 수 있습니다. 일반적으로 서비스는 "WorkQueueDepth" 또는 "TotalRequests"와 같은 논리 메트릭을 고려할 수 있습니다. 동일한 클러스터에서 논리 및 실제 메트릭을 둘 다 사용할 수 있습니다. 메트릭은 많은 서비스에서 공유될 수도 있고 특정 서비스와만 관련될 수 있습니다.

## <a name="other-considerations"></a>기타 고려 사항
클러스터의 소유자 및 연산자는 서비스 및 애플리케이션 작성자와 다른 사람일 수도 있고, 최소한 여러 가지 작업을 수행하는 동일한 사람입니다. 애플리케이션을 개발할 때는 필요한 사항을 알 것입니다. 사용될 리소스의 예상 크기와 다른 서비스가 배포되는 방식을 알 것입니다. 예를 들어 웹 계층은 인터넷에 노출되는 노드에서 실행되어야 하지만 데이터베이스 서비스는 그렇지 않아야 합니다. 또 다른 예로, 웹 서비스는 CPU 및 네트워크로 제한될 수 있지만 데이터 계층 서비스는 메모리 및 디스크 소비량에 의해 더 많은 영향을 받습니다. 그러나 프로덕션 중에 해당 서비스에 대한 라이브 사이트 인시던트를 처리거나 서비스에 대한 업그레이드를 관리하는 사용자는 다양한 작업을 수행해야 하며 다양한 도구가 필요합니다. 

클러스터 및 서비스 둘 다 동적인 경우:

- 클러스터의 노드 수는 확장되고 축소될 수 있습니다.
- 다양한 크기 및 형식의 노드는 오갈 수 있습니다.
- 서비스를 생성, 제거할 수 있고 원하는 리소스 할당 및 배치 규칙을 변경할 수 있습니다.
- 업그레이드 또는 기타 관리 작업은 인프라 수준의 애플리케이션에서 클러스터를 통해 실행될 수 있습니다.
- 언제든지 실패할 수 있습니다.

## <a name="cluster-resource-manager-components-and-data-flow"></a>클러스터 리소스 관리자 구성 요소 및 데이터 흐름
Cluster Resource Manager는 해당 서비스 내의 각 서비스 개체별로 각 서비스의 요구 사항 및 리소스 사용을 추적해야 합니다. Cluster Resource Manager에는 각 노드에서 실행되는 에이전트 및 내결함성 서비스라는 두 가지 개념적 부분이 있습니다. 각 노드 트랙의 에이전트는 서비스에서 보고서를 로드하고 집계하며 주기적으로 보고합니다. Cluster Resource Manager 서비스는 로컬 에이전트에서 모든 정보를 집계하고 현재 구성에 따라 반응합니다.

다음 다이어그램을 살펴보겠습니다.

<center>

![리소스 분산 장치 아키텍처][Image1]
</center>

런타임 중에 많은 내용이 변경될 수 있습니다. 예를 들어 일부 서비스가 사용하는 리소스의 양이 변경되고 일부 서비스가 실패하고 일부 노드가 클러스터를 연결하고 연결 해제한다고 가정하겠습니다. 노드에 대한 모든 변경 사항은 집계되어 Cluster Resource Manager 서비스(1, 2)로 정기적으로 전송되고, 여기서 다시 집계되고 분석되고 저장됩니다. 해당 서비스에서는 몇 초마다 변경 사항을 보고 필요한 작업이 있는지 확인합니다(3). 예를 들어 비어 있는 노드가 클러스터에 추가되었다는 것을 알 수 있습니다. 결과적으로 일부 서비스를 해당 노드로 이동하기로 결정합니다. Cluster Resource Manager는 특정 노드가 오버로드되거나 특정 서비스가 실패하거나 삭제되어 다른 곳에서 리소스를 확보했는지도 알 수 있습니다.

다음 다이어그램을 살펴보고 그 다음에 어떤 결과가 발생하는지 알아보겠습니다. 클러스터 Resource Manager에서 변경이 필요한지 판단한다고 가정합니다. 이는 다른 시스템 서비스(특히 장애 조치 관리자)와 함께 필요한 변경을 수행합니다. 그런 다음 필요한 명령을 적절한 노드(4)로 전송합니다. 예를 들어 Resource Manager에서 Node5가 오버로드되었음을 알아차리고 서비스 B를 Node5에서 Node4로 이동하기로 결정했다고 가정합니다. 재구성(5)이 마무리되면 클러스터는 다음과 같이 됩니다.

<center>

![리소스 분산 장치 아키텍처][Image2]
</center>

## <a name="next-steps"></a>다음 단계
- Cluster Resource Manager에는 클러스터를 설명하기 위한 많은 옵션이 있습니다. 이에 대해 자세히 알아보려면 [Service Fabric 클러스터 설명](./service-fabric-cluster-resource-manager-cluster-description.md)에 대한 문서를 확인하세요.
- Cluster Resource Manager의 기본 임무는 클러스터의 부하를 다시 분산하고 배치 규칙을 적용하는 것입니다. 이러한 동작은 구성하는 방법에 대한 자세한 내용은 [Service Fabric 클러스터 부하 분산](./service-fabric-cluster-resource-manager-balancing.md)을 참조하세요.

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
