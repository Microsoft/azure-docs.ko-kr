---
title: Service Fabric 클러스터 Resource Manager - 선호도 | Microsoft Docs
description: 서비스 패브릭 서비스에 대한 선호도 구성의 개요
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 678073e1-d08d-46c4-a811-826e70aba6c4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 29377492b90f366227ca7bedf85890b7734ea25f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62118418"
---
# <a name="configuring-and-using-service-affinity-in-service-fabric"></a>서비스 패브릭에서 서비스 선호도 구성 및 사용
선호도는 주로 더 큰 모놀리식 애플리케이션을 클라우드 및 마이크로 서비스 환경으로 쉽게 전환하도록 해주는 컨트롤입니다. 또한 서비스의 성능 향상을 위한 최적화로 사용되지만 이 경우 부작용이 있을 수 있습니다.

더 큰 앱 또는 마이크로 서비스를 고려하지 않고 설계한 앱을 Service Fabric(또는 모든 분산 환경)으로 가져오고 있다고 가정해 보겠습니다. 이 유형의 전환이 일반적입니다. 전체 앱을 환경에 변환하고 패키징한 다음 문제 없이 실행하여 시작합니다. 그런 다음 여러 개의 작은 서비스로 나눈 다음 서로 통신할 수 있도록 합니다.

결국 애플리케이션에 몇 가지 문제가 발생하고 있음을 알 수 있습니다. 이러한 문제는 대개 다음 범주 중 하나에 해당합니다.

1. 모놀리식 앱의 구성 요소 X는 구성 요소 Y에 대해 문서화되지 않은 종속성을 가지며, 해당 구성 요소들을 별도의 서비스로 전환했습니다. 현재 이러한 서비스는 클러스터의 다른 노드에서 실행되고 있으므로 끊어졌습니다.
2. 이러한 구성 요소는 로컬로 명명된 파이프 | 공유 메모리 | 디스크의 파일을 통해 통신하며, 성능상의 이유로 즉시 공유 리소스에 쓸 수 있어야 합니다. 이러한 강한 종속성은 나중에 제거될 것입니다.
3. 모든 것이 좋지만, 이들 두 구성 요소는 실제 통신이 잦고 성능에 민감한 것으로 나타났습니다. 이들을 별도의 서비스로 이동시켰을 때 애플리케이션의 전반적인 성능이 나빠지거나 대기 시간이 늘어났습니다. 결과적으로, 전체 애플리케이션은 기대를 충족하지 못하게 됩니다.

이러한 경우 리팩터링 작업을 손실하지 않으려고 하고 거대한 기념물로 남으려고 하지 않습니다. 마지막 조건은 일반 최적화로 바람직할 수도 있습니다. 그러나 자연스럽게 서비스로 작동하도록 구성 요소를 다시 디자인할 수 있거나 다른 방법으로 성능 기대치를 해결할 수 있게 될 때까지 어느 정도의 근접성이 필요합니다.

그렇다면 어떻게 해야 할까요? 이제 선호도를 켤 수 있습니다.

## <a name="how-to-configure-affinity"></a>선호도를 구성하는 방법
선호도를 설정하려면 두 개의 서로 다른 서비스 간의 선호도 관계를 정의합니다. 선호도를 다른 서비스에 있는 한 서비스를 "가리키고" "이 서비스는 해당 서비스가 실행되고 있는 장소에서만 실행될 수 있습니다."라고 생각할 수 있습니다. 경우에 따라 부모 자식 관계(부모에 있는 자식을 가리키는 경우)를 의미하기도 합니다. 선호도는 한 서비스의 복제본 또는 인스턴스가 다른 서비스의 복제본 또는 인스턴스와 동일한 노드에 배치되도록 합니다.

```csharp
ServiceCorrelationDescription affinityDescription = new ServiceCorrelationDescription();
affinityDescription.Scheme = ServiceCorrelationScheme.Affinity;
affinityDescription.ServiceName = new Uri("fabric:/otherApplication/parentService");
serviceDescription.Correlations.Add(affinityDescription);
await fabricClient.ServiceManager.CreateServiceAsync(serviceDescription);
```

> [!NOTE]
> 자식 서비스는 단일 선호도 관계에만 참여할 수 있습니다. 자식이 한 번에 두 부모 서비스와 선호도 관계를 맺도록 하려는 경우 몇 가지 옵션이 있습니다.
> - 관계를 역순으로 지정합니다(현재 자식 서비스에서 parentService1 및 parentService2를 가리키도록 함). 또는
> - 규칙에 따라 부모 중 하나를 허브로 지정하고 모든 서비스에서 해당 서비스를 가리키도록 합니다. 
>
> 결과적으로 클러스터의 배치 동작이 동일해야 합니다.
>

## <a name="different-affinity-options"></a>서로 다른 선호도 옵션
선호도는 몇 가지 상관 관계 구성표 중 하나를 통해 표시되며, 두 가지 모드가 있습니다. 선호도의 가장 일반적인 모드는 NonAlignedAffinity라고 하는 것입니다. NonAlignedAffinity에서 서로 다른 서비스의 복제본 또는 인스턴스는 같은 노드에 배치됩니다. 다른 모드는 AlignedAffinity입니다. 정렬된 선호도는 상태 저장 서비스에서만 유용합니다. 선호도가 정렬된 두 개의 상태 저장 서비스를 구성하면 이러한 서비스의 주 복제본이 서로 동일한 노드에 배치될 수 있습니다. 또한 해당 서비스에 대한 각 보조 복제본 쌍이 동일한 노드에 배치됩니다. 상태 저장 서비스를 위해 NonAlignedAffinity를 구성할 수도 있습니다(덜 일반적임). NonAlignedAffinity의 경우 두 상태 저장 서비스의 서로 다른 복제본이 동일한 노드에서 실행되지만 주 복제본은 다른 노드에 있을 수 있습니다.

<center>

![선호도 모드 및 그 영향][Image1]
</center>

### <a name="best-effort-desired-state"></a>최상의 노력이 필요한 상태
선호도 관계는 최상의 노력입니다. 동일한 실행 가능 프로세스에서 실행되는 배열 또는 안정성에 대해 동일한 보증을 제공하지 않습니다. 선호도 관계의 서비스는 실패할 수 있으며 독립적으로 이동할 수 있는 기본적으로 다른 엔터티입니다. 선호도 관계도 중단될 수 있지만 이러한 중단은 일시적입니다. 예를 들어 용량 제한은 선호도 관계에 있는 일부 서비스 개체만 지정된 노드에 맞출 수 있음을 의미할 수 있습니다. 이러한 경우 선호도 관계가 준비되었더라도 다른 제약 조건으로 인해 적용될 수 없습니다. 이렇게 할 수 있다면 나중에 위반이 자동으로 수정됩니다.

### <a name="chains-vs-stars"></a>체인 모양과 별 모양의 비교
현재 Cluster Resource Manager는 선호도 관계의 체인을 모델링할 수 없습니다. 즉, 한 선호도 관계에서 자식인 서비스는 다른 선호도 관계에서 부모가 될 수 없습니다. 이러한 형식의 관계를 모델링하려는 경우 체인이 아닌 별 모양으로 효과적으로 모델링해야 합니다. 체인을 별 모양으로 움직이려면 최하위 자식은 첫 번째 자식 부모의 부모가 될 수 있습니다. 서비스의 정렬에 따라 이 작업을 여러 번 수행해야 합니다. 기본 부모 서비스가 없는 경우 자리 표시자로 사용되는 서비스를 만들어야 합니다. 요구 사항에 따라 [애플리케이션 그룹](service-fabric-cluster-resource-manager-application-groups.md)을 살펴볼 수 있습니다.

<center>

![체인 vs입니다. 별 모양의 비교][Image2]
</center>

또 다른 사항으로 현재의 선호도 관계에는 기본적으로 방향성이 있다는 점입니다. 즉 선호도 규칙은 자식이 부모와 함께 배치되도록 합니다. 부모가 자식과 함께 있도록 보장하지는 않습니다. 따라서 선호도 위반이 있고 어떤 이유로 위반을 정정하기 위해 자식을 부모 노드로 이동할 수 없는 경우에는, 부모를 자식 노드로 이동하면 위반이 정정되더라도, 부모는 자식 노드로 이동되지 않습니다. [MoveParentToFixAffinityViolation](service-fabric-cluster-fabric-settings.md) 구성을 true로설정 하면 방향성이 제거됩니다. 또한 서로 다른 서비스에서 별도의 수명 주기를 가지고 있고 독립적으로 실패하고 이동할 수 있기 때문에 선호도 관계가 완벽하거나 즉시 적용될 수 없다는 점에 유의해야 합니다. 예를 들어 크래시로 인해 부모가 갑자기 다른 노드로 장애 조치되었다고 가정해 보겠습니다. 서비스를 계속, 일관성 있게, 사용할 수 있도록 유지하는 것이 우선 순위이므로 클러스터 리소스 관리자와 장애 조치(Failover) 관리자는 장애 조치를 먼저 처리합니다. 장애 조치가 완료되면 선호도 관계는 끊어지지만 클러스터 리소스 관리자에서는 자식이 부모와 함께 있지 않다는 알기 전까지 모든 것이 문제가 없다고 간주합니다. 이러한 종류의 검사는 정기적으로 수행됩니다. 클러스터 리소스 관리자에서 제약 조건을 평가하는 방법에 대한 자세한 내용은 [이 문서](service-fabric-cluster-resource-manager-management-integration.md#constraint-types)에서 설명하고 있으며, [이 문서](service-fabric-cluster-resource-manager-balancing.md)에서는 이러한 제약 조건을 평가하는 빈도를 구성하는 방법에 대해 자세히 설명합니다.   


### <a name="partitioning-support"></a>분할 지원
마지막으로 선호도에 대해 알아두어야 할 사항으로 선호도 관계는 부모가 분할된 곳에서는 지원되지 않습니다. 분할된 부모 서비스는 결국 지원될 수 있지만 오늘날에는 허용되지 않습니다.

## <a name="next-steps"></a>다음 단계
- 서비스 구성에 대한 자세한 내용은 [서비스 구성에 대한 자세한 정보](service-fabric-cluster-resource-manager-configure-services.md)에서 알아봅니다.
- 서비스를 작은 머신 세트으로 제한하거나 서비스 로드를 집계하려면 [애플리케이션 그룹](service-fabric-cluster-resource-manager-application-groups.md)을 사용합니다.

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resrouce-manager-affinity-modes.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-affinity/cluster-resource-manager-chains-vs-stars.png