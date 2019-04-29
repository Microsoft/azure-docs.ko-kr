---
title: 시스템 상태 보고서 문제 해결 | Microsoft Docs
description: Azure Service Fabric 구성 요소에서 보낸 상태 보고서와 클러스터 또는 애플리케이션 문제 해결에 대한 사용을 설명합니다.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 52574ea7-eb37-47e0-a20a-101539177625
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: caeef04a27cec7bbeda5dd96335d9b7bd1a8eca0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716271"
---
# <a name="use-system-health-reports-to-troubleshoot"></a>시스템 상태 보고서를 사용하여 문제 해결
Azure Service Fabric 구성 요소가 클러스터 내의 모든 엔터티에 대해 즉각적으로 시스템 상태 보고서를 제공합니다. [Health 스토어](service-fabric-health-introduction.md#health-store) 는 시스템 보고서를 기반으로 엔터티를 만들고 삭제합니다. 또한 엔터티 상호 작용을 캡처하는 계층 구조에서 보고서를 구성합니다.

> [!NOTE]
> 상태 관련 개념을 이해하려면 [Service Fabric 상태 모델](service-fabric-health-introduction.md)을 자세히 읽어봅니다.
> 
> 

시스템 상태 보고서는 클러스터 및 애플리케이션에 대한 가시성을 제공하고 문제를 플래그합니다. 애플리케이션 및 서비스의 경우, 시스템 상태 보고서는 서비스 패브릭 관점에서 엔터티가 올바르게 구현되고 동작하는지 확인합니다. 보고서가 응답 하지 않는 프로세스의 감지 또는 서비스의 비즈니스 논리의 상태 모니터링을 제공 하지 않습니다. 사용자 서비스는 논리에 고유한 정보로 건강 데이터를 보강할 수 있습니다.

> [!NOTE]
> 사용자 워치독이 보낸 상태 보고서는 시스템 구성 요소가 엔터티를 만든 *후*에만 표시됩니다. 엔터티가 삭제되면 Health 스토어가 모든 관련 상태 보고서를 자동으로 삭제합니다. 엔터티의 새 인스턴스가 만들어질 때도 마찬가지입니다. 예를 들어 새 상태 저장 유지 서비스 복제본 인스턴스가 만들어지는 경우가 있습니다. 이전 인스턴스와 연결된 모든 보고서는 삭제되고 저장소에서 정리됩니다.
> 
> 

시스템 구성 요소 보고서는 "**System.**" 접두사로 시작되는 원본에 의해 식별됩니다. 잘못된 매개 변수가 있는 보고서가 거부되므로 Watchdogs는 소스에 대해 동일한 접두사를 사용할 수 없습니다.

몇 가지 시스템 보고서를 검토하여 무엇이 해당 보고서를 트리거하는지 이해하고 보고서가 나타내는 가능한 문제의 해결 방법을 살펴봅니다.

> [!NOTE]
> Service Fabric은 관심 있는 조건에 대한 보고서를 계속 추가하여 클러스터 및 애플리케이션에서 발생하는 상황에 대한 가시성을 향상시킵니다. 기존 보고서를 더 자세한 정보로 보강하여 문제를 더 빨리 해결할 수 있습니다.
> 
> 

## <a name="cluster-system-health-reports"></a>클러스터 시스템 상태 보고서
클러스터 상태 엔터티는 Health 스토어에서 자동으로 만들어집니다. 모든 것이 제대로 작동하면 시스템 보고서는 없습니다.

### <a name="neighborhood-loss"></a>환경 손실
**System.Federation** 은 환경 손실을 감지하면 오류를 보고합니다. 보고서는 개별 노드에서 나오며 노드 ID는 속성 이름에 포함됩니다. 전체 Service Fabric 링에서 하나의 환경이 손실되면 일반적으로 간격 보고서의 양측을 나타내는 두 이벤트를 예상할 수 있습니다. 더 많은 환경이 손실된 경우 더 많은 이벤트가 있습니다.

보고서는 전역 임대 시간 제한을 TTL(Time to live)로 지정합니다. 조건이 활성인 한 보고서는 TTL 기간의 절반마다 다시 보내집니다. 이벤트는 만료되면 자동으로 제거됩니다. 보고 노드가 다운되었더라도 보고서가 Health 스토어에서 제대로 정리된 것을 만료된 동작이 보장하면 제거합니다.

* **SourceId**: System.Federation
* **속성**: 시작 **환경이** 노드 정보를 포함 합니다.
* **다음 단계**: 환경이 손실 된 이유를 조사 합니다. 예를 들어 클러스터 노드 간의 통신을 확인합니다.

### <a name="rebuild"></a>다시 빌드

FM(장애 조치(Failover) 관리자) 서비스는 클러스터 노드에 대한 정보를 관리합니다. FM이 해당 데이터를 잃어 데이터 손실이 발생하면 클러스터 노드에 대한 최신 업데이트 정보를 보장할 수 없습니다. 이 경우 시스템은 다시 빌드되고, System.FM은 상태를 다시 작성하기 위해 클러스터의 모든 노드에서 데이터를 수집합니다. 경우에 따라 네트워킹 또는 노드 문제로 인해 다시 빌드가 중지되거나 중단될 수도 있습니다. FMM(장애 조치(Failover) 관리자 마스터) 서비스에서도 마찬가지입니다. FMM은 모든 FM이 클러스터에 있는 위치를 추적하는 상태 비저장 시스템 서비스입니다. FMM의 기본 노드는 항상 0에 가장 가까운 ID가 있는 노드입니다. 노드가 삭제되면 다시 빌드가 트리거됩니다.
이전 조건 중 하나가 발생하면 **System.FM** 또는 **System.FMM**에서 오류 보고서를 통해 플래그를 지정합니다. 다시 빌드는 두 단계 중 하나에서 중단될 수 있습니다.

* **브로드캐스트 대기**: FM/FMM에서 다른 노드로부터 브로드캐스트 메시지 회신을 기다립니다.

  * **다음 단계**: 노드 간 네트워크 연결 문제가 있는지 조사 합니다.
* **노드 대기**: FM/FMM는 이미 다른 노드에서 브로드캐스트 회신을 받았으며 및 특정 노드로부터의 회신을 기다리고 있습니다. 상태 보고서에서 FM/FMM이 응답을 기다리고 있는 노드를 나열합니다.
   * **다음 단계**: FM/FMM 및 나열 된 노드 간에 네트워크 연결을 조사 합니다. 다른 가능한 문제에 대해 나열된 각 노드를 조사합니다.

* **SourceID**: System.FM 또는 System.FMM
* **속성**: 다시 작성 합니다.
* **다음 단계**: 상태 보고서의 설명에 나열 된 특정 노드의 상태 뿐만 아니라 노드 간의 네트워크 연결을 조사 합니다.

## <a name="node-system-health-reports"></a>노드 시스템 상태 보고서
장애 조치(Failover) 관리자 서비스를 나타내는 System.FM은 클러스터 노드에 대한 정보를 관리하는 기관입니다. 모든 노드는 상태를 보여주는 System.FM로부터 하나의 보고서를 가져야 합니다. 노드 상태가 제거되면 노드 엔터티도 제거됩니다. 자세한 내용은 [RemoveNodeStateAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.clustermanagementclient.removenodestateasync)를 참조하세요.

### <a name="node-updown"></a>노드 위/아래
System.FM은 노드가 링에 조인하는 경우 확인으로 보고합니다.(실행 중) 노드가 링에서 분리하는 경우 오류를 보고합니다.(업그레이드이든 단순히 실패한 것이든 중단되었습니다) Health 스토어에서 구성한 상태 계층이 System.FM 노드 보고서와의 상관관계에 따라, 배포된 엔터티에 대해 작동합니다. 배포된 모든 엔터티의 가상 부모를 노드로 간주합니다. 엔터티와 연결된 인스턴스와 동일한 인스턴스와 함께 노드가 가동 중인 것으로 System.FM에 의해 보고되면 해당 노드에 배포된 엔터티가 쿼리를 통해 노출됩니다. System.FM에서 노드가 다운되었거나 다시 시작한 것을 보고하면(새로운 인스턴스) Health 스토어가 다운 노드 또는 이전 노드 인스턴스에서만 존재할 수 있는 배포된 엔터티를 자동으로 정리합니다.

* **SourceId**: System.FM
* **속성**: 상태입니다.
* **다음 단계**: 업그레이드에 대 한 노드가 다운 된 경우 해당 돌아와야를 업그레이드 한 후입니다. 이 경우에 상태를 확인으로 다시 전환해야 합니다. 노드가 다시 돌아오지 않거나 실패하면 문제는 자세한 조사가 필요합니다.

다음 예제는 노드 실행을 위해 상태가 정상인 System.FM 이벤트를 표시합니다.

```powershell
PS C:\> Get-ServiceFabricNodeHealth  _Node_0

NodeName              : _Node_0
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 8
                        SentAt                : 7/14/2017 4:54:51 PM
                        ReceivedAt            : 7/14/2017 4:55:14 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```


### <a name="certificate-expiration"></a>인증서가 만료
**System.FabricNode** 는 노드에 의해 사용되는 인증서가 만료가 가까워질 경우 경고를 보고합니다. 세 가지 인증서 노드당: **Certificate_cluster**하십시오 **Certificate_server**, 및 **Certificate_default_client**합니다. 적어도 2주 후에 만료될 때 보고서 상태는 확인입니다. 2주 이내에 만료되면 보고서 형식은 경고입니다. 이러한 이벤트의 TTL은 무한대이며 노드가 클러스터를 벗어나면 제거됩니다

* **SourceId**: System.FabricNode
* **속성**: 시작 **인증서** 인증서 유형에 대 한 자세한 정보를 포함 합니다.
* **다음 단계**: 만료가 가까운 경우 인증서를 업데이트 합니다.

### <a name="load-capacity-violation"></a>부하 용량 위반
노드 용량 위반이 발견되면 서비스 패브릭 부하 분산 장치에서 경고를 보고합니다.

* **SourceId**: System.PLB
* **속성**: 시작 **용량**합니다.
* **다음 단계**: 제공된 된 메트릭을 확인 하 고 노드에서 현재 용량을 봅니다.

### <a name="node-capacity-mismatch-for-resource-governance-metrics"></a>리소스 거버넌스 메트릭의 노드 용량 불일치
클러스터 매니페스트에 정의된 노드 용량이 리소스 관리 메트릭(메모리 및 CPU 코어 수)에 대한 실제 노드 용량보다 크면 System.Hosting에서 경고를 보고합니다. [리소스 관리](service-fabric-resource-governance.md)를 사용하는 첫 번째 서비스 패키지에서 지정된 노드에 등록하면 상태 보고서가 표시됩니다.

* **SourceId**: System.Hosting
* **속성**: **ResourceGovernance**합니다.
* **다음 단계**: 서비스 패키지 관리가 예상 대로 적용 되지 않고 때문에이 문제는 문제 수와 [리소스 거 버 넌 스](service-fabric-resource-governance.md) 제대로 작동 하지 않습니다. 클러스터 매니페스트를 이러한 메트릭에 대한 올바른 노드 용량으로 업데이트하거나, 클러스터 매니페스트를 지정하지 않고 Service Fabric에서 사용 가능한 리소스를 자동으로 검색하도록 합니다.

## <a name="application-system-health-reports"></a>애플리케이션 시스템 상태 보고서
클러스터 관리자 서비스를 나타내는 System.CM은 애플리케이션에 대한 정보를 관리하는 기관입니다.

### <a name="state"></a>시스템 상태
애플리케이션을 만들거나 업데이트할 때 System.CM은 확인을 보고합니다. 애플리케이션을 삭제할 때 스토어에서 제거할 수 있도록 Health 스토어에 이를 알려줍니다.

* **SourceId**: System.CM
* **속성**: 상태입니다.
* **다음 단계**: 응용 프로그램 생성 되거나 업데이트 된 경우 클러스터 관리자 상태 보고서를 포함 해야 하 합니다. 그렇지 않으면 쿼리를 실행하여 애플리케이션의 상태를 확인합니다. 예를 들어 **Get-ServiceFabricApplication -ApplicationName** *applicationName* PowerShell cmdlet을 사용합니다.

다음 예제는 **fabric:/WordCount** 애플리케이션에 대한 상태 이벤트를 보여 줍니다.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None -ExcludeHealthStatistics

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    : 
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 282
                                  SentAt                : 7/13/2017 5:57:05 PM
                                  ReceivedAt            : 7/14/2017 4:55:10 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 7/13/2017 5:57:05 PM, LastWarning = 1/1/0001 12:00:00 AM
```

## <a name="service-system-health-reports"></a>서비스 시스템 상태 보고서
장애 조치(failover) 관리자 서비스를 나타내는 System.FM은 서비스에 대한 정보를 관리하는 기관입니다.

### <a name="state"></a>시스템 상태
System.FM은 서비스가 만들어질 때 확인을 보고합니다. 서비스가 삭제되면 Health 스토어에서 엔터티를 삭제합니다.

* **SourceId**: System.FM
* **속성**: 상태입니다.

다음 예제는 서비스 **fabric:/WordCount/WordCountWebService**에 상태 이벤트를 보여 줍니다.

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountWebService -ExcludeHealthStatistics


ServiceName           : fabric:/WordCount/WordCountWebService
AggregatedHealthState : Ok
PartitionHealthStates : 
                        PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
                        AggregatedHealthState : Ok
                        
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 14
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="service-correlation-error"></a>서비스 상관 관계 오류 
**System.PLB**는 다른 서비스와 상관되는 서비스를 업데이트할 때 선호도 체인이 만들어지는 것을 발견하면 오류를 보고합니다. 업데이트에 성공하면 보고서가 지워집니다.

* **SourceId**: System.PLB
* **속성**: **ServiceDescription**.
* **다음 단계**: 상관 관계가 지정 된 서비스 설명을 확인 합니다.

## <a name="partition-system-health-reports"></a>파티션 시스템 상태 보고서
장애 조치(failover) 관리자 서비스를 나타내는 System.FM은 서비스 파티션에 대한 정보를 관리하는 기관입니다.

### <a name="state"></a>시스템 상태
System.FM은 파티션이 생성되고 정상적이면 확인을 보고합니다. 파티션이 삭제될 때 Health 스토어에서 엔터티를 삭제합니다.

파티션이 최소 복제본 수 이하인 경우 오류를 보고합니다. 파티션이 최소 복제본 개수 이하는 아니지만 대상 복제본 개수 이하라면 경고를 보고합니다. 파티션이 쿼럼 손실인 경우 System.FM이 오류를 보고합니다.

기타 주목할 만한 이벤트는 재구성이 예상보다 오래 걸리며 빌드가 예상보다 오래 걸리는 경우 경고를 포함합니다. 예상되는 빌드 및 재구성 시간을 서비스 시나리오에 따라 구성할 수 있습니다. 예를 들어 서비스에 Azure SQL Database와 같은 테라바이트 상태가 있는 경우 작은 양의 상태가 있는 서비스보다 빌드가 오래 걸립니다.

* **SourceId**: System.FM
* **속성**: 상태입니다.
* **다음 단계**: 상태 확인 없는 경우는 일부 복제본 되거나 되지 않은 생성, 열, 기본 또는 보조로 올바르게 승격 가능한 것입니다. 

설명에 쿼럼 손실이 표시될 경우 다운된 복제본에 대한 자세한 상태 보고서를 검토하고 복제본을 다시 시작하면 파티션을 다시 온라인으로 전환할 수 있습니다.

설명에 [재구성](service-fabric-concepts-reconfiguration.md)에서 멈춘 파티션이 표시되면 주 복제본에 대한 상태 보고서에 추가 정보가 제공됩니다.

다른 System.FM 상태 보고서의 경우 복제본 또는 다른 시스템 구성 요소에 대한 파티션이나 서비스에 대한 보고서가 있을 수 있습니다. 

다음 예제에서는 몇 가지 이러한 보고서를 설명합니다. 

다음 예제는 정상 파티션을 보여줍니다.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountWebService | Get-ServiceFabricPartitionHealth -ExcludeHealthStatistics -ReplicasFilter None

PartitionId           : 8bbcd03a-3a53-47ec-a5f1-9b77f73c53b2
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 70
                        SentAt                : 7/13/2017 5:57:05 PM
                        ReceivedAt            : 7/14/2017 4:55:10 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/13/2017 5:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM
```

다음 예제는 대상 복제본 개수 이하인 파티션의 상태를 보여줍니다. 다음 단계는 다음과 같습니다. 구성 된 방식을 보여 주는 파티션 설명을 가져옵니다 **MinReplicaSetSize** 3 개 및 **TargetReplicaSetSize** 는 7입니다. 그런 다음 클러스터에 있는 노드 수를 가져옵니다. 이 경우에는 5입니다. 따라서 이 경우 복제본의 목표 수가 사용 가능한 노드 수보다 더 높으므로 두 개의 복제본을 배치할 수 없습니다.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None -ExcludeHealthStatistics


PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.
                        
ReplicaHealthStates   : None
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 123
                        SentAt                : 7/14/2017 4:55:39 PM
                        ReceivedAt            : 7/14/2017 4:55:44 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        fabric:/WordCount/WordCountService 7 2 af2e3e44-a8f8-45ac-9f31-4093eb897600
                          N/S Ready _Node_2 131444422260002646
                          N/S Ready _Node_4 131444422293113678
                          N/S Ready _Node_3 131444422293113679
                          N/S Ready _Node_1 131444422293118720
                          N/P Ready _Node_0 131444422293118721
                          (Showing 5 out of 5 replicas. Total available replicas: 5)
                        
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:55:44 PM, LastOk = 1/1/0001 12:00:00 AM
                        
                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_af2e3e44-a8f8-45ac-9f31-4093eb897600
                        HealthState           : Warning
                        SequenceNumber        : 131445250939703027
                        SentAt                : 7/14/2017 4:58:13 PM
                        ReceivedAt            : 7/14/2017 4:58:14 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        Secondary replica could not be placed due to the following constraints and properties:  
                        TargetReplicaSetSize: 7
                        Placement Constraint: N/A
                        Parent Service: N/A
                        
                        Constraint Elimination Sequence:
                        Existing Secondary Replicas eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        Existing Primary Replica eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.
                        
                        Nodes Eliminated By Constraints:
                        
                        Existing Secondary Replicas -- Nodes with Partition's Existing Secondary Replicas/Instances:
                        --
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status: None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status: None/None
                        
                        Existing Primary Replica -- Nodes with Partition's Existing Primary Replica or Secondary Replicas:
                        --
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status: None/None
                        
                        
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 7/14/2017 4:56:14 PM, LastOk = 1/1/0001 12:00:00 AM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | select MinReplicaSetSize,TargetReplicaSetSize

MinReplicaSetSize TargetReplicaSetSize
----------------- --------------------
                2                    7                        

PS C:\> @(Get-ServiceFabricNode).Count
5
```

다음 예제는 사용자가 **RunAsync** 메서드에서 취소 토큰을 허용하지 않아 재구성에서 멈춘 파티션의 상태를 보여 줍니다. P(주)로 표시된 복제본의 상태 보고서를 조사하면 문제를 더 자세히 살펴볼 수 있습니다.

```powershell
PS C:\utilities\ServiceFabricExplorer\ClientPackage\lib> Get-ServiceFabricPartitionHealth 0e40fd81-284d-4be4-a665-13bc5a6607ec -ExcludeHealthStatistics 


PartitionId           : 0e40fd81-284d-4be4-a665-13bc5a6607ec
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                                               
HealthEvents          : 
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 7
                        SentAt                : 8/27/2017 3:43:09 AM
                        ReceivedAt            : 8/27/2017 3:43:32 AM
                        TTL                   : Infinite
                        Description           : Partition reconfiguration is taking longer than expected.
                        fabric:/app/test1 3 1 0e40fd81-284d-4be4-a665-13bc5a6607ec
                          P/S Ready Node1 131482789658160654
                          S/P Ready Node2 131482789688598467
                          S/S Ready Node3 131482789688598468
                          (Showing 3 out of 3 replicas. Total available replicas: 3)                        
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/27/2017 3:43:32 AM, LastError = 1/1/0001 12:00:00 AM
```
이 상태 보고서는 재구성이 진행 중인 파티션 복제본의 상태를 보여 줍니다. 

```
  P/S Ready Node1 131482789658160654
  S/P Ready Node2 131482789688598467
  S/S Ready Node3 131482789688598468
```

각 복제본에 대한 상태 보고서에는 다음 내용이 포함됩니다.
- 이전 구성 역할
- 현재 구성 역할
- [복제본 상태](service-fabric-concepts-replica-lifecycle.md)
- 복제본이 실행 중인 노드
- 복제본 ID

예제와 같은 경우에는 추가적인 조사가 필요합니다. 이전 예제에서는 `Primary` 및 `Secondary`(131482789658160654 및 131482789688598467)로 표시된 복제본부터 각 개별 복제본의 상태를 조사합니다.

### <a name="replica-constraint-violation"></a>복제본 제약 조건 위반
**System.PLB**는 복제본 제약 조건 위반을 감지하고 일부 파티션 복제본을 배치할 수 없는 경우 경고를 보고합니다. 보고서 상세 정보에는 복제본 배치에 방해가 되는 제약과 속성을 표시합니다.

* **SourceId**: System.PLB
* **속성**: 시작 **ReplicaConstraintViolation**합니다.

## <a name="replica-system-health-reports"></a>복제본 시스템 상태 보고서
재구성 에이전트 구성 요소를 나타내는 **System.RA**는 복제본 상태의 기관입니다.

### <a name="state"></a>시스템 상태
System.RA는 복제본이 만들어지면 정상으로 보고합니다.

* **SourceId**: System.RA
* **속성**: 상태입니다.

다음 예제는 정상 복제본을 보여줍니다.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth

PartitionId           : af2e3e44-a8f8-45ac-9f31-4093eb897600
ReplicaId             : 131444422293118721
AggregatedHealthState : Ok
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131445248920273536
                        SentAt                : 7/14/2017 4:54:52 PM
                        ReceivedAt            : 7/14/2017 4:55:13 PM
                        TTL                   : Infinite
                        Description           : Replica has been created._Node_0
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 7/14/2017 4:55:13 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="replicaopenstatus-replicaclosestatus-replicachangerolestatus"></a>ReplicaOpenStatus, ReplicaCloseStatus, ReplicaChangeRoleStatus
이 속성은 복제본을 열거나 닫고 역할 간에 전환하려고 할 때 경고 또는 실패를 나타내는 데 사용됩니다. 자세한 내용은 [복제본 수명 주기](service-fabric-concepts-replica-lifecycle.md)를 참조하세요. 실패는 이 기간에 서비스 호스트 프로세스의 API 호출이나 작동 중지로 인해 발생한 예외일 수 있습니다. C# 코드의 API 호출로 인한 실패가 발생하면 Service Fabric이 예외와 스택 추적을 상태 보고서에 추가합니다.

이런 상태 경고는 로컬에서 몇 번(정책에 따라) 작업을 다시 시도한 후 발생합니다. Service Fabric이 최대 임계값까지 작업을 재시도합니다. 최대 임계값에 도달하면 상황을 해결하기 위한 작업을 시도할 수 있습니다. 이 시도로 인해 이 노드에서의 작업에 대해 포기하기 때문에 경고가 해제될 수 있습니다. 예를 들어 복제본이 노드에서 열리지 않으면 Service Fabric이 상태 경고를 발생시킵니다. 복제본이 계속 열리지 않으면 Service Fabric이 자체 복구를 위해 작동합니다. 이 작업에는 다른 노드에서의 동일한 작업 시도가 관련될 수 있습니다. 이 시도로 인해 이 복제본에 대해 발생한 경고가 지워집니다. 

* **SourceId**: System.RA
* **속성**: **ReplicaOpenStatus**하십시오 **ReplicaCloseStatus**, 및 **ReplicaChangeRoleStatus**합니다.
* **다음 단계**: 작업에 실패 한 이유를 확인 하는 서비스 코드 또는 크래시 덤프를 조사 합니다.

다음 예제에서는 열기 메서드에서 `TargetInvocationException`을 야기하는 복제본의 상태를 표시합니다. 설명에는 실패 지점(**IStatefulServiceReplica.Open**), 예외 형식(**TargetInvocationException**) 및 스택 추적이 포함됩니다.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 337cf1df-6cab-4825-99a9-7595090c0b1b -ReplicaOrInstanceId 131483509874784794


PartitionId           : 337cf1df-6cab-4825-99a9-7595090c0b1b
ReplicaId             : 131483509874784794
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaOpenStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaOpenStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483510001453159
                        SentAt                : 8/27/2017 11:43:20 PM
                        ReceivedAt            : 8/27/2017 11:43:21 PM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during open on _Node_0 API call: IStatefulServiceReplica.Open(); Error = System.Reflection.TargetInvocationException (-2146232828)
Exception has been thrown by the target of an invocation.
   at Microsoft.ServiceFabric.Replicator.RecoveryManager.d__31.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.LoggingReplicator.d__137.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.DynamicStateManager.d__109.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.TransactionalReplicator.d__79.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Replicator.StatefulServiceReplica.d__21.MoveNext()
--- End of stack trace from previous location where exception was thrown ---
   at System.Runtime.CompilerServices.TaskAwaiter.ThrowForNonSuccess(Task task)
   at System.Runtime.CompilerServices.TaskAwaiter.HandleNonSuccessAndDebuggerNotification(Task task)
   at Microsoft.ServiceFabric.Services.Runtime.StatefulServiceReplicaAdapter.d__0.MoveNext()

    For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/27/2017 11:43:21 PM, LastOk = 1/1/0001 12:00:00 AM                        
```

다음 예제에서는 다는 동안 지속적으로 작동 중지되는 복제본을 보여 줍니다.

```powershell
C:>Get-ServiceFabricReplicaHealth -PartitionId dcafb6b7-9446-425c-8b90-b3fdf3859e64 -ReplicaOrInstanceId 131483565548493142


PartitionId           : dcafb6b7-9446-425c-8b90-b3fdf3859e64
ReplicaId             : 131483565548493142
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='ReplicaCloseStatus', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : ReplicaCloseStatus
                        HealthState           : Warning
                        SequenceNumber        : 131483565611258984
                        SentAt                : 8/28/2017 1:16:01 AM
                        ReceivedAt            : 8/28/2017 1:16:03 AM
                        TTL                   : Infinite
                        Description           : Replica had multiple failures during close on _Node_1. The application 
                        host has crashed.
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 1:16:03 AM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="reconfiguration"></a>Reconfiguration
이 속성은 [재구성](service-fabric-concepts-reconfiguration.md)을 수행하는 복제본이 재구성 중단 또는 멈춤을 감지할 경우를 나타내는 데 사용됩니다. 이 상태 보고서는 현재 역할이 주인 복제본에 관련됩니다(주 복제본에서 활성 보조 복제본으로 수준이 내려지는 복제본에서 수행될 수 있는 스왑 주 재구성의 경우 제외).

다음과 같은 이유 중 하나로 재구성이 멈출 수 있습니다.

- 로컬 복제본(재구성을 수행하는 것과 동일한 복제본)에 대한 작업이 완료되지 않습니다. 이 경우 다른 구성 요소(System.RAP 또는 System.RE)에서 이 복제본에 대한 상태 보고서를 조사하면 추가 정보가 제공될 수 있습니다.

- 원격 복제본에서 작업이 완료되지 않습니다. 작업이 보류 중인 복제본이 상태 보고서에 나열됩니다. 해당 원격 복제본에 대한 상태 보고서에서 추가 조사를 수행해야 합니다. 이 노드와 원격 노드 간에 통신 문제가 있을 수도 있습니다.

드문 경우지만 이 노드와 장애 조치(Failover) 관리자 서비스 간 통신 또는 기타 문제로 인해 재구성이 멈출 수 있습니다.

* **SourceId**: System.RA
* **속성**: 재구성 합니다.
* **다음 단계**: 상태 보고서의 설명에 따라 로컬 또는 원격 복제본을 조사 합니다.

다음 예제에서는 로컬 복제본에서 재구성이 멈춘 상태 보고서를 나타냅니다. 이 예제에서는 서비스가 취소 토큰을 허용하지 않은 것이 원인입니다.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 9a0cedee-464c-4603-abbc-1cf57c4454f3 -ReplicaOrInstanceId 131483600074836703


PartitionId           : 9a0cedee-464c-4603-abbc-1cf57c4454f3
ReplicaId             : 131483600074836703
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', 
                        ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483600309264482
                        SentAt                : 8/28/2017 2:13:50 AM
                        ReceivedAt            : 8/28/2017 2:13:57 AM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from the local replica
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 2:13:57 AM, LastOk = 1/1/0001 12:00:00 AM
```

다음 예제에서는 두 원격 복제본으로부터 응답을 대기 중인 상태로 재구성이 멈춘 상태 보고서를 나타냅니다. 이 예제에는 현재 주 복제본을 포함하여 파티션에 세 개 복제본이 있습니다. 

```Powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId  579d50c6-d670-4d25-af70-d706e4bc19a2 -ReplicaOrInstanceId 131483956274977415


PartitionId           : 579d50c6-d670-4d25-af70-d706e4bc19a2
ReplicaId             : 131483956274977415
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          : 
                        SourceId              : System.RA
                        Property              : Reconfiguration
                        HealthState           : Warning
                        SequenceNumber        : 131483960376212469
                        SentAt                : 8/28/2017 12:13:57 PM
                        ReceivedAt            : 8/28/2017 12:14:07 PM
                        TTL                   : Infinite
                        Description           : Reconfiguration is stuck. Waiting for response from 2 replicas
                        
                        Pending Replicas: 
                        P/I Down 40 131483956244554282
                        S/S Down 20 131483956274972403
                        
                        For more information see: https://aka.ms/sfhealth
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:07:37 PM, LastOk = 1/1/0001 12:00:00 AM
```

이 상태 보고서는 두 개의 복제본에서 응답을 기다리는 동안 재구성이 멈춰 있음을 보여 줍니다. 

```
    P/I Down 40 131483956244554282
    S/S Down 20 131483956274972403
```

각 복제본에 대해 다음 정보가 제공됩니다.
- 이전 구성 역할
- 현재 구성 역할
- [복제본 상태](service-fabric-concepts-replica-lifecycle.md)
- 노드 ID
- 복제본 ID

재구성 차단을 해제하려면:
- **down** 복제본을 시작해야 합니다. 
- **inbuild** 복제본이 빌드를 완료하고 준비 상태로 전환되어야 합니다.

### <a name="slow-service-api-call"></a>느린 서비스 API 호출
**System.RAP** 및 **System.Replicator**는 사용자 서비스 코드에 대한 호출이 구성된 시간보다 오래 걸리는 경우 경고를 보고합니다. 이 경고는 호출이 완료되면 지워집니다.

* **SourceId**: System.RAP 또는 System.Replicator
* **속성**: 느린 API의 이름입니다. 이 설명은 API가 보류된 시간에 대한 자세한 정보를 제공합니다.
* **다음 단계**: 호출이 예상 보다 오래 걸리는 이유를 조사 합니다.

다음 예제는 **RunAsync**에서 취소 토큰을 허용하지 않은 신뢰할 수 있는 서비스에 대한 System.RAP의 상태 이벤트를 보여 줍니다.

```powershell
PS C:\> Get-ServiceFabricReplicaHealth -PartitionId 5f6060fb-096f-45e4-8c3d-c26444d8dd10 -ReplicaOrInstanceId 131483966141404693


PartitionId           : 5f6060fb-096f-45e4-8c3d-c26444d8dd10
ReplicaId             : 131483966141404693
AggregatedHealthState : Warning
UnhealthyEvaluations  : 
                        Unhealthy event: SourceId='System.RA', Property='Reconfiguration', HealthState='Warning', ConsiderWarningAsError=false.
                        
HealthEvents          :                         
                        SourceId              : System.RAP
                        Property              : IStatefulServiceReplica.ChangeRole(S)Duration
                        HealthState           : Warning
                        SequenceNumber        : 131483966663476570
                        SentAt                : 8/28/2017 12:24:26 PM
                        ReceivedAt            : 8/28/2017 12:24:56 PM
                        TTL                   : Infinite
                        Description           : The api IStatefulServiceReplica.ChangeRole(S) on _Node_1 is stuck. Start Time (UTC): 2017-08-28 12:23:56.347.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 8/28/2017 12:24:56 PM, LastOk = 1/1/0001 12:00:00 AM
                        
```

속성과 텍스트가 API가 멈추었음을 나타냅니다. 중단된 API에 대해 수행할 다음 단계는 서로 다릅니다. *IStatefulServiceReplica* 또는 *IStatelessServiceInstance*의 API는 보통 서비스 코드의 버그입니다. 다음 섹션에서는 이런 API를 [신뢰할 수 있는 서비스 모델](service-fabric-reliable-services-lifecycle.md)로 전환하는 방법을 설명합니다.

- **IStatefulServiceReplica.Open**: 이 경고에 대 한 호출 `CreateServiceInstanceListeners`, `ICommunicationListener.OpenAsync`를 재정의 하는 경우 또는 `OnOpenAsync` 중단 됩니다.

- **IStatefulServiceReplica.Close** 하 고 **IStatefulServiceReplica.Abort**: 가장 일반적인 경우는 서비스에 전달 된 취소 토큰을 허용 하지 않는 `RunAsync`합니다. `ICommunicationListener.CloseAsync`를 나타내거나 재정의된 `OnCloseAsync`가 멈춘 경우를 나타낼 수도 있습니다.

- **IStatefulServiceReplica.ChangeRole (S)** 하 고 **istatefulservicereplica.changerole (n)**: 가장 일반적인 경우는 서비스에 전달 된 취소 토큰을 허용 하지 않는 `RunAsync`합니다.

- **IStatefulServiceReplica.ChangeRole(P)**: 가장 일반적인 경우는 서비스에서 작업을 반환 하지 않은 `RunAsync`합니다.

멈출 수 있는 다른 API 호출은 **IReplicator** 인터페이스에 있습니다. 예를 들면 다음과 같습니다.

- **IReplicator.CatchupReplicaSet**: 이 경고는 두 가지 중 하나를 나타냅니다. 복제본이 충분하지 않습니다. 이러한 경우에 해당하는지 확인하려면 파티션에 있는 복제본의 복제본 상태 또는 중단된 재구성을 위한 System.FM 상태 보고서를 살펴봅니다. 복제본이 작업을 승인하고 있지 않습니다. `Get-ServiceFabricDeployedReplicaDetail` PowerShell cmdlet은 모든 복제본의 진행 상황을 확인하는 데 사용할 수 있습니다. 문제는 `LastAppliedReplicationSequenceNumber` 값이 주 복제본의 `CommittedSequenceNumber` 값 뒤에 있는 복제본에 있습니다.

- **IReplicator.BuildReplica(\<Remote ReplicaId>)**: 이 경고는 빌드 프로세스의 문제를 나타냅니다. 자세한 내용은 [복제본 수명 주기](service-fabric-concepts-replica-lifecycle.md)를 참조하세요. 복제기 주소의 잘못된 구성이 원인일 수 있습니다. 자세한 내용은 [상태 저장 신뢰할 수 있는 서비스 구성](service-fabric-reliable-services-configuration.md) 및 [서비스 매니페스트에서 리소스 지정](service-fabric-service-manifest-resources.md)을 참조하세요. 원격 노드의 문제일 수도 있습니다.

### <a name="replicator-system-health-reports"></a>복제자 시스템 상태 보고서
**복제 큐가 가득 찼습니다.**
**System.Replicator**는 복제본 큐가 가득 차면 경고를 보고합니다. 보통은 기본 데이터베이스에서는 하나 이상의 보조 복제본이 작업을 승인하는 속도가 느리기 때문에 복제본 큐가 가득차게 됩니다. 보조 데이터베이스에서는 서비스가 작업에 적용하는 속도가 느릴 때 일반적으로 발생합니다. 큐가 더 이상 가득 차지 않으면 경고가 지워집니다.

* **SourceId**: System.Replicator
* **속성**: **PrimaryReplicationQueueStatus** 나 **SecondaryReplicationQueueStatus**복제본의 역할에 따라 합니다.
* **다음 단계**: 보고서는 주 복제본의 경우 클러스터의 노드 사이의 연결을 확인 합니다. 모든 연결이 정상이면 디스크 대기 시간이 길고 속도가 느린 보조 복제본이 하나 이상 있을 수 있으며, 이러한 복제본에 작업을 적용합니다. 보고서가 보조 복제본에 있으면 먼저 노드에서 디스크 사용량 및 성능을 확인합니다. 그런 다음, 저속 노드에서 주 복제본으로 나가는 연결을 확인합니다.

**RemoteReplicatorConnectionStatus**: 기본 복제본의 
**System.Replicator**는 보조(원격) 복제자에 대한 연결이 정상이 아닐 경우 경고를 보고합니다. 원격 복제자의 주소가 보고서 메시지에 표시되므로 잘못된 구성이 전달되었는지 또는 복제자 간에 네트워크 문제가 있는지를 더 편리하게 검색할 수 있습니다.

* **SourceId**: System.Replicator
* **속성**: **RemoteReplicatorConnectionStatus**.
* **다음 단계**: 오류 메시지를 확인 하 고 원격 복제자 주소가 올바르게 구성 되어 있는지 확인 합니다. 예를 들어 원격 복제자가 "localhost" 수신 주소를 통해 열리면 외부에서 연결할 수 없습니다. 주소가 올바르게 표시되면 주 노드와 원격 주소 간의 연결을 확인하여 잠재적인 네트워크 문제를 찾습니다.

### <a name="replication-queue-full"></a>복제 큐 전체
**System.Replicator**는 복제본 큐가 가득 차면 경고를 보고합니다. 보통은 기본 데이터베이스에서는 하나 이상의 보조 복제본이 작업을 승인하는 속도가 느리기 때문에 복제본 큐가 가득차게 됩니다. 보조 데이터베이스에서는 서비스가 작업에 적용하는 속도가 느릴 때 일반적으로 발생합니다. 큐가 더 이상 가득 차지 않으면 경고가 지워집니다.

* **SourceId**: System.Replicator
* **속성**: **PrimaryReplicationQueueStatus** 나 **SecondaryReplicationQueueStatus**복제본의 역할에 따라 합니다.

### <a name="slow-naming-operations"></a>느린 이름 지정 작업
**System.NamingService**는 이름 지정 작업이 허용 가능한 시간보다 오래 걸리는 경우 주 복제본에 해당 상태를 보고합니다. 이름 지정 작업의 예는 [CreateServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync) 또는 [DeleteServiceAsync](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.deleteserviceasync)입니다. FabricClient에서 더 많은 메서드를 찾을 수 있습니다. 예를 들어 [서비스 관리 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient) 또는 [속성 관리 메서드](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.propertymanagementclient)에 있습니다.

> [!NOTE]
> 명명 서비스는 서비스 이름을 클러스터의 한 위치로 확인합니다. 사용자는 이를 사용하여 서비스 이름 및 속성을 관리할 수 있습니다. Service Fabric 분할 지속형 서비스입니다. 파티션 중 하나는 모든 Service Fabric 이름 및 서비스에 대한 메타데이터를 포함하는 *기관 소유자*를 나타냅니다. 서비스 패브릭 이름은 *이름 소유자* 파티션이라는 다른 파티션에 매핑되므로 서비스는 확장 가능합니다. [이름 지정 서비스](service-fabric-architecture.md)에 대해 자세히 알아봅니다.
> 
> 

이름 지정 작업이 예상보다 오래 걸리는 경우 작업은 작업을 사용하는 이름 지정 서비스 파티션의 주 복제본의 경고 보고서로 플래그 지정됩니다. 작업이 성공적으로 완료되면 경고는 해제됩니다. 오류와 함께 작업이 완료되면 상태 보고서는 오류에 대한 세부 정보를 포함합니다.

* **SourceId**: System.NamingService
* **속성**: 접두사로 시작 "**Duration_**" 고 느린 작업 및 작업이 적용 되는 Service Fabric 이름을 식별 합니다. 예를 들어 이름 **fabric:/MyApp/MyService**에서 서비스 만들기가 너무 오래 걸리는 경우 속성은 **Duration_AOCreateService.fabric:/MyApp/MyService**입니다. “AO”는 이 이름 및 작업에 대한 이름 지정 파티션의 역할을 가리킵니다.
* **다음 단계**: 명명 작업이 실패 하는 이유를 확인 합니다. 각 작업에는 다른 원인이 있을 수 있습니다. 예를 들어 삭제 서비스가 멈춰 있을 수 있습니다. 서비스 코드의 사용자 버그로 인해 애플리케이션 호스트가 노드에서 계속 충돌하기 때문에 서비스가 중단되었을 수 있습니다.

다음 예제는 서비스 만들기 작업을 보여 줍니다. 작업이 구성된 기간보다 오래 걸렸습니다. “AO”는 다시 시도하고 “NO”로 작업을 보냅니다. “NO”에서 시간 제한이 있는 마지막 작업을 완료했습니다. 이 경우 동일한 복제본은 “AO” 및 “NO” 역할에 대해 주 복제본입니다.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>DeployedApplication 시스템 상태 보고서
**System.Hosting** 은 배포된 엔터티에 대한 권한입니다.

### <a name="activation"></a>활성화
System.Hosting은 애플리케이션이 노드에서 성공적으로 활성화되면 확인을 보고합니다. 그렇지 않으면 오류를 보고합니다.

* **SourceId**: System.Hosting
* **속성**: **활성화**, 롤아웃 버전 포함 합니다.
* **다음 단계**: 응용 프로그램이 정상 상태 이면 활성화에 실패 한 이유를 조사 합니다.

다음 예제는 성공적인 활성화를 보여줍니다.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ExcludeHealthStatistics

ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates : 
                                     ServiceManifestName   : WordCountServicePkg
                                     ServicePackageActivationId : 
                                     NodeName              : _Node_1
                                     AggregatedHealthState : Ok
                                     
HealthEvents                       : 
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131445249083836329
                                     SentAt                : 7/14/2017 4:55:08 PM
                                     ReceivedAt            : 7/14/2017 4:55:14 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>다운로드
System.Hosting은 애플리케이션 패키지 다운로드에 실패하면 오류를 보고합니다.

* **SourceId**: System.Hosting
* **속성**: **다운로드**, 롤아웃 버전 포함 합니다.
* **다음 단계**: 노드에 다운로드 실패 한 이유를 조사 합니다.

## <a name="deployedservicepackage-system-health-reports"></a>DeployedServicePackage 시스템 상태 보고서
**System.Hosting** 은 배포된 엔터티에 대한 권한입니다.

### <a name="service-package-activation"></a>서비스 패키지 활성화
System.Hosting은 노드에서 서비스 패키지 활성화가 성공하면 확인을 보고합니다. 그렇지 않으면 오류를 보고합니다.

* **SourceId**: System.Hosting
* **속성**: 활성화 합니다.
* **다음 단계**: 활성화에 실패 한 이유를 조사 합니다.

### <a name="code-package-activation"></a>코드 패키지 활성화
System.Hosting은 활성화가 성공한 경우 각 코드 패키지에 대해 확인을 보고합니다. 활성화에 실패하는 경우 구성된 대로 경고를 보고합니다. **CodePackage**가 활성화에 실패하거나 구성된 **CodePackageHealthErrorThreshold**보다 큰 오류와 함께 종료되면 호스팅이 오류를 보고합니다. 서비스 패키지에 여러 코드 패키지가 있다면 각 패키지에 대해 생성된 활성화 보고서가 있습니다.

* **SourceId**: System.Hosting
* **속성**: 접두사를 사용 하 여 **CodePackageActivation** 코드 패키지 및 진입점의 이름을 포함 하 고 *CodePackageActivation:CodePackageName:SetupEntryPoint / EntryPoint*합니다. 예를 들어 **CodePackageActivation:Code:SetupEntryPoint**입니다.

### <a name="service-type-registration"></a>서비스 유형 등록
서비스 유형이 제대로 등록되면 System.Hosting이 OK를 보고합니다. 등록이 제시간에 완료되지 않으면 **ServiceTypeRegistrationTimeout**을 사용하여 구성된 대로 오류를 보고합니다. 런타임은 닫혀 있는 경우 서비스 유형이 노드에서 등록 해제되고 호스팅이 경고를 보고합니다.

* **SourceId**: System.Hosting
* **속성**: 접두사를 사용 하 여 **ServiceTypeRegistration** 서비스 유형 이름을 포함 합니다. 예를 들어 **ServiceTypeRegistration:FileStoreServiceType**입니다.

다음 예제는 정상으로 배포된 서비스 패키지를 보여줍니다.

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName _Node_1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName            : fabric:/WordCount
ServiceManifestName        : WordCountServicePkg
ServicePackageActivationId : 
NodeName                   : _Node_1
AggregatedHealthState      : Ok
HealthEvents               : 
                             SourceId              : System.Hosting
                             Property              : Activation
                             HealthState           : Ok
                             SequenceNumber        : 131445249084026346
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServicePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : CodePackageActivation:Code:EntryPoint
                             HealthState           : Ok
                             SequenceNumber        : 131445249084306362
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The CodePackage was activated successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
                             
                             SourceId              : System.Hosting
                             Property              : ServiceTypeRegistration:WordCountServiceType
                             HealthState           : Ok
                             SequenceNumber        : 131445249088096842
                             SentAt                : 7/14/2017 4:55:08 PM
                             ReceivedAt            : 7/14/2017 4:55:14 PM
                             TTL                   : Infinite
                             Description           : The ServiceType was registered successfully.
                             RemoveWhenExpired     : False
                             IsExpired             : False
                             Transitions           : Error->Ok = 7/14/2017 4:55:14 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="download"></a>다운로드
서비스 패키지 다운로드에 실패하면System.Hosting이 오류를 보고합니다.

* **SourceId**: System.Hosting
* **속성**: **다운로드**, 롤아웃 버전 포함 합니다.
* **다음 단계**: 노드에 다운로드 실패 한 이유를 조사 합니다.

### <a name="upgrade-validation"></a>유효성 검사 업그레이드
System.Hosting은 업그레이드 중에 유효성 검사에 실패하거나 노드에서 업그레이드에 실패하면 오류를 보고합니다.

* **SourceId**: System.Hosting
* **속성**: 접두사를 사용 하 여 **FabricUpgradeValidation** 고 업그레이드 버전을 포함 합니다.
* **설명**: 발생 한 오류를 가리킵니다.

### <a name="undefined-node-capacity-for-resource-governance-metrics"></a>리소스 거버넌스 메트릭의 노드 용량이 정의되지 않음
클러스터 매니페스트에 노드 용량이 정의되어 있지 않고 자동 검색 구성이 해제되어 있으면 System.Hosting에서 경고를 보고합니다. [리소스 관리](service-fabric-resource-governance.md)를 사용하는 서비스 패키지가 지정된 노드에 등록할 때마다 Service Fabric에서 상태 경고가 발생합니다.

* **SourceId**: System.Hosting
* **속성**: **ResourceGovernance**합니다.
* **다음 단계**: 이 문제를 해결 하는 기본 방법은 사용 가능한 리소스의 자동 검색을 사용 하도록 클러스터 매니페스트를 변경할 방법은입니다. 또 다른 방법으로, 이러한 메트릭에 대해 올바르게 지정된 노드 용량으로 클러스터 매니페스트를 업데이트합니다.

## <a name="next-steps"></a>다음 단계
* [서비스 패브릭 상태 보고서 보기](service-fabric-view-entities-aggregated-health.md)

* [서비스 상태를 보고 및 확인하는 방법](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

* [로컬로 서비스 모니터링 및 진단](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

* [Service Fabric 애플리케이션 업그레이드](service-fabric-application-upgrade.md)

