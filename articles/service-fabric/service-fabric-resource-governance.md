---
title: 컨테이너 및 서비스에 대한 Azure Service Fabric 리소스 거버넌스 | Microsoft Docs
description: Azure Service Fabric을 사용하면 컨테이너 내부 또는 외부에서 실행 중인 서비스에 대해 리소스 제한을 지정할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 8/9/2017
ms.author: aljo, subramar
ms.openlocfilehash: e011554e61411fddca034f024c30c2270593e07b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772538"
---
# <a name="resource-governance"></a>리소스 관리

동일한 노드 또는 클러스터에서 여러 서비스를 실행하는 경우 한 서비스가 프로세스의 다른 서비스보다 많은 리소스를 사용할 수 있습니다. 이 문제를 방해가 되는 이웃 문제라고 합니다. Azure Service Fabric을 사용하면 개발자가 서비스당 예약 및 제한을 지정하여 리소스를 보장하고 리소스 사용량을 제한할 수 있습니다.

> 이 문서를 진행하기 전에 [Service Fabric 애플리케이션 모델](service-fabric-application-model.md) 및 [Service Fabric 호스팅 모델](service-fabric-hosting-model.md)에 대해 익숙해져야 합니다.
>

## <a name="resource-governance-metrics"></a>리소스 관리 메트릭

리소스 관리는 Service Fabric에서 [서비스 패키지](service-fabric-application-model.md)에 따라 지원됩니다. 서비스 패키지에 할당된 리소스를 코드 패키지 간에 다시 나눌 수 있습니다. 지정된 리소스 제한은 리소스 예약을 의미하기도 합니다. Service Fabric은 두 개의 기본 제공 [메트릭](service-fabric-cluster-resource-manager-metrics.md)을 사용하여 서비스 패키지당 CPU 및 메모리 지정을 지원합니다.

* *CPU*(메트릭 이름 `servicefabric:/_CpuCores`): 호스트 머신에서 사용할 수 있는 논리 코어입니다. 모든 노드에서 모든 코어에 동일하게 가중치를 적용합니다.

* *메모리*(메트릭 이름 `servicefabric:/_MemoryInMB`): 메모리는 메가바이트 단위로 표현되며, 머신에서 사용할 수 있는 실제 메모리에 매핑됩니다.

이러한 두 메트릭을 위해 [클러스터 리소스 관리자](service-fabric-cluster-resource-manager-cluster-description.md)는 총 클러스터 용량, 클러스터의 각 노드 부하, 클러스터에 남은 리소스를 추적합니다. 이 두 메트릭은 모든 다른 사용자 또는 사용자 지정 메트릭에 동일합니다. 기존의 모든 기능을 함께 사용할 수 있습니다.

* 두 메트릭에 따라 클러스터의 [균형을 조정](service-fabric-cluster-resource-manager-balancing.md)할 수 있습니다(기본 동작).
* 두 메트릭에 따라 클러스터를 [조각 모음](service-fabric-cluster-resource-manager-defragmentation-metrics.md)할 수 있습니다.
* [클러스터를 설명](service-fabric-cluster-resource-manager-cluster-description.md)할 때 두 메트릭에 대해 버퍼링된 용량을 설정할 수 있습니다.

두 메트릭에 대한 [동적 부하 보고](service-fabric-cluster-resource-manager-metrics.md)는 지원되지 않으며 해당 메트릭에 대한 부하는 생성 시 정의됩니다.

## <a name="resource-governance-mechanism"></a>리소스 거버넌스 메커니즘

Service Fabric 런타임은 현재 리소스 예약을 제공하지 않습니다. 프로세스 또는 컨테이너를 열 때 런타임은 생성 시점에 정의된 부하로 리소스 한도를 설정합니다. 또한 런타임은 사용 가능한 리소스를 초과하는 새 서비스 패키지의 열기를 거부합니다. 이 프로세스의 작동 방식을 더 잘 이해하기 위해 CPU 코어가 2개인 노드를 예로 들어 보겠습니다(메모리 거버넌스 메커니즘 해당).

1. 먼저 컨테이너가 노드에 배치되고 CPU 코어 1개를 요청합니다. 런타임이 컨테이너를 열고 CPU 제한을 1개 코어로 설정합니다. 이 컨테이너는 한 번에 둘 이상의 코어를 사용할 수 없습니다.

2. 그런 다음 서비스의 복제본이 노드에 배치되고 해당 서비스 패키지가 1개 CPU 코어 제한을 지정합니다. 런타임이 코드 패키지를 열고 CPU 제한을 1개 코어로 설정합니다.

이 시점에서 제한의 합은 노드의 용량과 같습니다. 프로세스 및 컨테이너는 각각 1개 코어로 실행되며 서로 간섭하지 않습니다. Service Fabric은 CPU 제한을 지정할 때 컨테이너나 복제본을 더 이상 배치하지 않습니다.

그러나 다른 프로세스가 CPU를 경합하는 두 가지 상황이 있습니다. 이 상황에서는 이 예제의 프로세스와 컨테이너에 방해가 되는 이웃 문제가 발생할 수 있습니다.

* *관리형 및 비관리형 서비스와 컨테이너 혼합*: 사용자가 리소스 거버넌스를 지정하지 않고 서비스를 만들면 런타임이 해당 서비스는 리소스를 소비하지 않는다고 간주하며 이 예제의 노드에 배치할 수 있습니다. 이 경우 이 새 프로세스가 이미 노드에서 실행 중인 서비스를 희생하여 일부 CPU를 효과적으로 소비하게 됩니다. 이 문제에 대한 두 가지 해결 방법이 있습니다. 관리 및 비관리 서비스를 같은 클러스터에서 혼합하지 않거나, [배치 제약 조건](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)을 사용하여 두 가지 종류의 서비스가 동일한 노드 집합에서 종료되지 않게 하는 것입니다.

* *Service Fabric 외부에 있는 다른 프로세스가 노드에서 시작(예: 일부 OS 서비스)*: 이 경우, Service Fabric 외부의 프로세스도 기존 서비스와 CPU를 경합합니다. 이 문제의 해결 방법은 다음 섹션에서처럼 OS 오버헤드에 부합하는 노드 용량을 올바르게 설정하는 것입니다.

## <a name="cluster-setup-for-enabling-resource-governance"></a>리소스 거버넌스를 사용하기 위한 클러스터 설정

노드가 시작되고 클러스터에 연결되면 Service Fabric은 사용 가능한 메모리 용량과 코어 수를 확인하고 해당 두 리소스에 대한 노드 용량을 설정합니다.

운영 체제와, 노드에서 실행될 수 있는 다른 프로세스를 위해 일부 버퍼 공간을 남겨두기 위해 Service Fabric은 노드에서 사용 가능한 리소스의 80%만 사용합니다. 이 백분율은 구성할 수 있고 클러스터 매니페스트에서 변경할 수 있습니다.

Service Fabric이 사용 가능한 CPU의 50%와 사용 가능한 메모리의 70%를 사용하도록 지시하는 방법의 예제는 다음과 같습니다.

```xml
<Section Name="PlacementAndLoadBalancing">
    <!-- 0.0 means 0%, and 1.0 means 100%-->
    <Parameter Name="CpuPercentageNodeCapacity" Value="0.5" />
    <Parameter Name="MemoryPercentageNodeCapacity" Value="0.7" />
</Section>
```

노드 용량의 완전 수동 설정이 필요한 경우 클러스터의 노드를 설명하는 데 기본 메커니즘을 사용할 수도 있습니다. 다음은 2GB의 메모리와 4개 코어로 노드를 설정하는 방법의 예제입니다.

```xml
    <NodeType Name="MyNodeType">
      <Capacities>
        <Capacity Name="servicefabric:/_CpuCores" Value="4"/>
        <Capacity Name="servicefabric:/_MemoryInMB" Value="2048"/>
      </Capacities>
    </NodeType>
```

사용 가능한 리소스의 자동 확인을 사용하고 노드 용량이 클러스터 매니페스트에서 수동으로 정의된 경우 Service Fabric은 노드가 사용자가 정의한 용량을 지원하기에 충분한 리소스를 갖는지 확인합니다.

* 매니페스트에서 정의된 노드 용량이 노드에서 사용 가능한 리소스 이하인 경우 Service Fabric은 매니페스트에서 지정된 용량을 사용합니다.

* 매니페스트에서 정의된 노드 용량이 사용 가능한 리소스보다 크면 Service Fabric은 사용 가능한 리소스를 노드 용량으로 사용합니다.

필요하지 않은 경우 사용 가능한 리소스의 자동 검색을 해제할 수 있습니다. 이 기능을 해제하려면 다음 설정을 변경합니다.

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="AutoDetectAvailableResources" Value="false" />
</Section>
```

최적 성능을 얻으려면 클러스터 매니페스트에서 다음 설정도 켜야 합니다.

```xml
<Section Name="PlacementAndLoadBalancing">
    <Parameter Name="PreventTransientOvercommit" Value="true" />
    <Parameter Name="AllowConstraintCheckFixesDuringApplicationUpgrade" Value="true" />
</Section>
```

## <a name="specify-resource-governance"></a>리소스 거버넌스 지정

리소스 관리 제한은 다음 예제와 같이 애플리케이션 매니페스트(ServiceManifestImport 섹션)에서 지정됩니다.

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <!--
  ServicePackageA has the number of CPU cores defined, but doesn't have the MemoryInMB defined.
  In this case, Service Fabric sums the limits on code packages and uses the sum as 
  the overall ServicePackage limit.
  -->
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="1"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="512" MemoryInMB="1000" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="256" MemoryInMB="1000" />
    </Policies>
  </ServiceManifestImport>
```

이 예제에서 **ServicePackageA**라는 서비스 패키지는 자신이 배치된 노드의 코어 하나를 취합니다. 이 서비스 패키지에는 코드 패키지 2개(**CodeA1** 및 **CodeA2**)가 포함되어 있으며, 둘 다 `CpuShares` 매개 변수를 지정합니다. 비율이 CpuShares 512:256이면 코어가 코드 패키지 2개에 나뉩니다.

따라서 이 예제에서 CodeA1은 코어의 2/3를 받고, CodeA2는 코어의 1/3을 받습니다(동일한 비율의 소프트 보장 예약). 코드 패키지에 대한 CpuShares를 지정하지 않은 경우 Service Fabric은 코어를 균일하게 나눕니다.

메모리 제한은 절대값이므로 두 코드 패키지가 모두 1024MB 메모리로 제한됩니다(동일한 값의 소프트 보장 예약). 코드 패키지(컨테이너 또는 프로세스)는 이 제한보다 많은 메모리를 할당할 수 없으며, 할당하려고 하면 메모리 부족 예외가 발생합니다. 리소스 제한 적용이 작동하려면 서비스 패키지 내의 모든 코드 패키지에 메모리 제한이 지정되어 있어야 합니다.

### <a name="using-application-parameters"></a>애플리케이션 매개 변수 사용

리소스 관리를 지정하는 경우 [애플리케이션 매개 변수](service-fabric-manage-multiple-environment-app-configuration.md)를 사용하여 여러 앱 구성을 관리할 수 있습니다. 다음 예제에서는 애플리케이션 매개 변수의 사용법을 보여 줍니다.

```xml
<?xml version='1.0' encoding='UTF-8'?>
<ApplicationManifest ApplicationTypeName='TestAppTC1' ApplicationTypeVersion='vTC1' xsi:schemaLocation='http://schemas.microsoft.com/2011/01/fabric ServiceFabricServiceModel.xsd' xmlns='http://schemas.microsoft.com/2011/01/fabric' xmlns:xsi='https://www.w3.org/2001/XMLSchema-instance'>

  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="4" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="2048" />
    <Parameter Name="MemoryB" DefaultValue="2048" />
  </Parameters>

  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName='ServicePackageA' ServiceManifestVersion='v1'/>
    <Policies>
      <ServicePackageResourceGovernancePolicy CpuCores="[CpuCores]"/>
      <ResourceGovernancePolicy CodePackageRef="CodeA1" CpuShares="[CpuSharesA]" MemoryInMB="[MemoryA]" />
      <ResourceGovernancePolicy CodePackageRef="CodeA2" CpuShares="[CpuSharesB]" MemoryInMB="[MemoryB]" />
    </Policies>
  </ServiceManifestImport>
```

이 예제에서는 기본 매개 변수 값이 프로덕션 환경에 대해 설정되며, 각 서비스 패키지에 4개 코어와 2GB의 메모리가 할당됩니다. 애플리케이션 매개 변수 파일을 사용해 기본값을 변경할 수 있습니다. 이 예제에서는 애플리케이션을 로컬로 테스트하는 데 하나의 매개 변수 파일을 사용할 수 있습니다. 이 경우 프로덕션 환경보다 리소스가 덜 사용됩니다.

```xml
<!-- ApplicationParameters\Local.xml -->

<Application Name="fabric:/TestApplication1" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Parameters>
    <Parameter Name="CpuCores" DefaultValue="2" />
    <Parameter Name="CpuSharesA" DefaultValue="512" />
    <Parameter Name="CpuSharesB" DefaultValue="512" />
    <Parameter Name="MemoryA" DefaultValue="1024" />
    <Parameter Name="MemoryB" DefaultValue="1024" />
  </Parameters>
</Application>
```

> [!IMPORTANT]
> 애플리케이션 매개 변수를 사용해서 리소스 관리를 지정하는 방식은 Service Fabric 버전 6.1부터 사용할 수 있습니다.<br>
>
> 리소스 관리를 지정하는 데 애플리케이션 매개 변수를 사용할 경우 Service Fabric을 버전 6.1 이전 버전으로 다운그레이드할 수 없습니다.

## <a name="other-resources-for-containers"></a>컨테이너에 대한 기타 리소스

CPU 및 메모리 외에도 컨테이너의 다른 리소스 한도를 지정할 수 있습니다. 이러한 한도는 코드 패키지 수준에서 지정되며 컨테이너를 시작하면 적용됩니다. CPU 및 메모리와는 달리 클러스터 리소스 관리자는 이러한 리소스를 인식하지 않으며 그에 대한 용량 확인이나 부하 분산을 수행하지 않습니다.

* *MemorySwapInMB*: 컨테이너가 사용할 수 있는 스왑 메모리 크기입니다.
* *MemoryReservationInMB*: 노드에서 메모리 경합이 확인된 경우에만 시행되는 메모리 거버넌스의 소프트 제한입니다.
* *CpuPercent*: 컨테이너가 사용할 수 있는 CPU의 백분율입니다. 서비스 패키지에 대해 CPU 한도를 지정한 경우 이 매개 변수는 실질적으로 무시됩니다.
* *MaximumIOps*: 컨테이너가 사용할 수 있는 최대 IOPS입니다(읽기 및 쓰기).
* *MaximumIOBytesps*: 컨테이너가 사용할 수 있는 최대 IO(초당 바이트)입니다(읽기 및 쓰기).
* *BlockIOWeight*: 타 컨테이너에 상대적인 블록 IO 가중치입니다.

이러한 리소스를 CPU 및 메모리와 결합할 수 있습니다. 컨테이너에 대한 추가 리소스를 지정하는 방법의 예는 다음과 같습니다.

```xml
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="FrontendServicePackage" ServiceManifestVersion="1.0"/>
        <Policies>
            <ResourceGovernancePolicy CodePackageRef="FrontendService.Code" CpuPercent="5"
            MemorySwapInMB="4084" MemoryReservationInMB="1024" MaximumIOPS="20" />
        </Policies>
    </ServiceManifestImport>
```

## <a name="next-steps"></a>다음 단계

* 클러스터 리소스 관리자에 대한 자세한 내용은 [Service Fabric 클러스터 리소스 관리자 소개](service-fabric-cluster-resource-manager-introduction.md)를 확인하세요.
* 애플리케이션 모델, 서비스 패키지, 코드 패키지 및 복제본 매핑 방식에 대한 자세한 내용은 [Service Fabric의 애플리케이션 모델링](service-fabric-application-model.md)을 참조하세요.
