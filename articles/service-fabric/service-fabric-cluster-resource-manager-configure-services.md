---
title: Azure Service Fabric에서 메트릭 및 배치 설정 지정 | Microsoft Docs
description: 메트릭, 배치 제약 조건 및 기타 배치 정책을 지정하여 Service Fabric 서비스를 설명하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 21fcac62c9335652d0c682a6ac889be82e649464
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60844145"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Service Fabric 서비스에 대한 클러스터 리소스 관리자 설정 구성
Service Fabric 클러스터 리소스 관리자를 사용하면 개별적으로 명명된 모든 서비스를 관리하는 규칙을 세밀하게 제어할 수 있습니다. 명명된 각 서비스 인스턴스는 클러스터에서 할당되는 방식에 대한 규칙을 지정할 수 있습니다. 명명된 각 서비스는 해당 서비스에 대해 갖는 중요도를 포함하여 보고하려는 메트릭 집합도 정의할 수 있습니다. 서비스 구성은 세 가지 작업으로 구분됩니다.

1. 배치 제약 조건 구성
2. 메트릭 구성
3. 고급 배치 정책 및 기타 규칙 구성(덜 일반적임)

## <a name="placement-constraints"></a>배치 제약 조건
배치 제약 조건은 클러스터에서 서비스가 실제로 실행될 수 있는 노드를 제어하는 데 사용됩니다. 일반적으로 명명된 특정 서비스 인스턴스 또는 특정 노드 형식에서 실행되도록 제약된 지정된 형식의 모든 서비스를 볼 수 있습니다. 배치 제약 조건은 확장할 수 있습니다. 노드 형식별로 모든 속성 집합을 정의한 다음 서비스를 만들 때 제약 조건이 포함된 속성을 선택할 수 있습니다. 또한 서비스가 실행되는 동안 서비스의 배치 제약 조건을 변경할 수도 있습니다. 이렇게 하면 클러스터의 변경 내용이나 서비스의 요구 사항에 대응할 수 있습니다. 지정된 노드의 속성을 클러스터에서 동적으로 업데이트할 수도 있습니다. 배치 제약 조건 및 구성 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>메트릭
메트릭은 명명된 특정 서비스에 필요한 리소스의 집합입니다. 서비스의 메트릭 구성에는 기본적으로 해당 서비스의 상태 저장 복제본 또는 상태 비저장 인스턴스 각각이 서비하는 리소스의 크기가 포함됩니다. 메트릭에는 조정이 필요한 경우 서비스에 대한 해당 메트릭의 부하 분산 중요도를 나타내는 가중치도 포함됩니다.

## <a name="advanced-placement-rules"></a>고급 배치 규칙
덜 일반적인 시나리오에서 유용한 다른 유형의 배치 규칙이 있습니다. 일부 사례:
- 지리적으로 분산된 클러스터를 지원하는 제약 조건
- 특정 애플리케이션 아키텍처

다른 배치 규칙은 상관 관계 또는 정책을 통해 구성됩니다.

## <a name="next-steps"></a>다음 단계
- 메트릭은 서비스 패브릭 클러스터 리소스 관리자가 클러스터의 소비와 용량을 관리하는 방법입니다. 메트릭 및 구성 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-metrics.md)를 확인하세요.
- 선호도는 서비스에 대해 구성할 수 있는 하나의 모드입니다. 일반적이지 않지만 필요한 경우 [여기](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- 추가 시나리오를 처리하기 위해 서비스에 구성할 수 있는 다양한 배치 규칙이 있습니다. 이러한 기타 배치 정책은 [여기](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- 처음부터 시작 및 [서비스 패브릭 클러스터 Resource Manager 소개](service-fabric-cluster-resource-manager-introduction.md)
- 클러스터 Resource Manager가 클러스터의 부하를 관리하고 분산하는 방법을 알아보려면 [부하 분산](service-fabric-cluster-resource-manager-balancing.md)
- 클러스터 Resource Manager에는 클러스터를 설명하기 위한 많은 옵션이 있습니다. 이에 대해 자세히 알아보려면 [Service Fabric 클러스터 설명](service-fabric-cluster-resource-manager-cluster-description.md)에 대한 문서를 확인하세요.
