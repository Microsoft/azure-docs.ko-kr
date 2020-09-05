---
title: Azure에서 Service Fabric 클러스터 모니터링
description: 이 자습서에서는 Service Fabric 이벤트를 보고, EventStore API를 쿼리하고, 성능 카운터를 모니터링하고, 상태 보고서를 확인하여 클러스터를 모니터링하는 방법을 알아봅니다.
author: srrengar
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: srrengar
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: 17ec3c6d3b9b19b7bc6edcb82cef438e27189c33
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020119"
---
# <a name="tutorial-monitor-a-service-fabric-cluster-in-azure"></a>자습서: Azure에서 Service Fabric 클러스터 모니터링

모니터링 및 진단은 모든 클라우드 환경에서 워크로드를 개발, 테스트 및 배포하는 데 중요합니다. 이 자습서는 시리즈의 2부로서 이벤트, 성능 카운터 및 상태 보고서를 사용하여 Service Fabric 클러스터를 모니터링하고 진단하는 방법을 보여 줍니다.   자세한 내용은 [클러스터 모니터링](service-fabric-diagnostics-overview.md#platform-cluster-monitoring) 및 [인프라 모니터링](service-fabric-diagnostics-overview.md#infrastructure-performance-monitoring)에 대한 개요를 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Service Fabric 이벤트 보기
> * EventStore API를 통한 클러스터 이벤트 쿼리
> * 인프라 모니터링/성능 카운터 수집
> * 클러스터 상태 보고서 보기

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 템플릿을 사용하여 Azure에서 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md) 만들기
> * 클러스터 모니터링
> * [클러스터 규모 확장 또는 규모 감축](service-fabric-tutorial-scale-cluster.md)
> * [클러스터의 런타임 업그레이드](service-fabric-tutorial-upgrade-cluster.md)
> * [클러스터 삭제](service-fabric-tutorial-delete-cluster.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 시작하기 전에:

* Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
* [Azure PowerShell](/powershell/azure/install-az-ps) 또는 [Azure CLI](/cli/azure/install-azure-cli)를 설치합니다.
* 보안 [Windows 클러스터](service-fabric-tutorial-create-vnet-and-windows-cluster.md)를 만듭니다. 
* 클러스터에 대해 [진단 수집](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configurediagnostics_anchor)을 설정합니다.
* 클러스터에서 [EventStore 서비스](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureeventstore_anchor)를 사용하도록 설정합니다.
* 클러스터에 대한 [Azure Monitor 로그 및 Log Analytics 에이전트](service-fabric-tutorial-create-vnet-and-windows-cluster.md#configureloganalytics_anchor)를 구성합니다.

## <a name="view-service-fabric-events-using-azure-monitor-logs"></a>Azure Monitor 로그를 사용하여 Service Fabric 이벤트 보기

Azure Monitor 로그는 클라우드에서 호스팅되는 애플리케이션 및 서비스에서 원격 분석 데이터를 수집 및 분석하고, 가용성과 성능을 최대화하는 데 도움이 되는 분석 도구를 제공합니다. Azure Monitor 로그에서 쿼리를 실행하여 인사이트를 얻고 클러스터에서 발생하는 문제를 해결할 수 있습니다.

Service Fabric 분석 솔루션에 액세스하려면 [Azure Portal](https://portal.azure.com)로 이동하여 Service Fabric 분석 솔루션을 만든 리소스 그룹을 선택합니다.

**ServiceFabric(mysfomsworkspace)** 리소스를 선택합니다.

**개요**에서 Service Fabric에 대한 타일을 포함하여 사용하도록 설정된 각 솔루션에 대해 그래프 형태의 타일이 표시됩니다. **Service Fabric** 그래프를 클릭하여 Service Fabric 분석 솔루션으로 이동합니다.

![Service Fabric 솔루션](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-summary.png)

다음 이미지에서는 Service Fabric 분석 솔루션의 홈페이지를 보여 줍니다. 이 홈페이지는 클러스터에서 수행되는 작업에 대한 스냅샷 보기를 제공합니다.

![Service Fabric 솔루션](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-solution.png)

 클러스터를 만들 때 진단을 활성화하면 다음에 대한 이벤트를 볼 수 있습니다. 

* [Service Fabric 클러스터 이벤트](service-fabric-diagnostics-event-generation-operational.md)
* [Reliable Actors 프로그래밍 모델 이벤트](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services 프로그래밍 모델 이벤트](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>즉시 사용이 가능한 Service Fabric 이벤트 외에도, [진단 확장 프로그램의 구성을 업데이트](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)하여 더 자세한 시스템 이벤트를 수집할 수 있습니다.

### <a name="view-service-fabric-events-including-actions-on-nodes"></a>노드에 대한 작업을 포함한 Service Fabric 이벤트 보기

Service Fabric 분석 페이지에서 **클러스터 이벤트**에 대한 그래프를 클릭합니다.  수집된 모든 시스템 이벤트에 대한 로그가 표시됩니다. 참고로, 이러한 로그는 Azure Storage 계정의 **WADServiceFabricSystemEventsTable**에서 제공되며, 마찬가지로 다음에 표시되는 Reliable Services 및 Reliable Actors 이벤트는 해당 테이블에서 제공됩니다.
    
![쿼리 조작 채널](media/service-fabric-tutorial-monitor-cluster/oms-service-fabric-events.png)

쿼리는 Kusto 쿼리 언어를 사용하여 원하는 항목을 구체화하도록 수정할 수 있습니다. 예를 들어 클러스터의 노드에서 수행된 모든 작업을 찾으려면 다음 쿼리를 사용하면 됩니다. 아래에 사용된 이벤트 ID는 [운영 채널 이벤트 참조](service-fabric-diagnostics-event-generation-operational.md)에서 찾을 수 있습니다.

```kusto
ServiceFabricOperationalEvent
| where EventId < 25627 and EventId > 25619 
```

Kusto 쿼리 언어는 강력합니다. 여기에는 유용한 몇 가지 다른 쿼리가 있습니다.

쿼리를 ServiceFabricEvent 별칭이 있는 함수로 저장하여 *ServiceFabricEvent* 조회 테이블을 사용자 정의 함수로 만듭니다.

```kusto
let ServiceFabricEvent = datatable(EventId: int, EventName: string)
[
    ...
    18603, 'NodeUpOperational',
    18604, 'NodeDownOperational',
    ...
];
ServiceFabricEvent
```

지난 한 시간 동안 기록된 작업 이벤트를 반환합니다.
```kusto
ServiceFabricOperationalEvent
| where TimeGenerated > ago(1h)
| join kind=leftouter ServiceFabricEvent on EventId
| project EventId, EventName, TaskName, Computer, ApplicationName, EventMessage, TimeGenerated
| sort by TimeGenerated
```

EventId == 18604 및 EventName == 'NodeDownOperational'인 작업 이벤트를 반환합니다.
```kusto
ServiceFabricOperationalEvent
| where EventId == 18604
| project EventId, EventName = 'NodeDownOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
```

EventId == 18604 및 EventName == 'NodeUpOperational'인 작업 이벤트를 반환합니다.
```kusto
ServiceFabricOperationalEvent
| where EventId == 18603
| project EventId, EventName = 'NodeUpOperational', TaskName, Computer, EventMessage, TimeGenerated
| sort by TimeGenerated 
``` 

HealthState == 3 (Error)인 상태 보고서를 반환하고, EventMessage 필드에서 추가 속성을 추출합니다.

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| extend HealthStateId = extract(@"HealthState=(\S+) ", 1, EventMessage, typeof(int))
| where TaskName == 'HM' and HealthStateId == 3
| extend SourceId = extract(@"SourceId=(\S+) ", 1, EventMessage, typeof(string)),
         Property = extract(@"Property=(\S+) ", 1, EventMessage, typeof(string)),
         HealthState = case(HealthStateId == 0, 'Invalid', HealthStateId == 1, 'Ok', HealthStateId == 2, 'Warning', HealthStateId == 3, 'Error', 'Unknown'),
         TTL = extract(@"TTL=(\S+) ", 1, EventMessage, typeof(string)),
         SequenceNumber = extract(@"SequenceNumber=(\S+) ", 1, EventMessage, typeof(string)),
         Description = extract(@"Description='([\S\s, ^']+)' ", 1, EventMessage, typeof(string)),
         RemoveWhenExpired = extract(@"RemoveWhenExpired=(\S+) ", 1, EventMessage, typeof(bool)),
         SourceUTCTimestamp = extract(@"SourceUTCTimestamp=(\S+)", 1, EventMessage, typeof(datetime)),
         ApplicationName = extract(@"ApplicationName=(\S+) ", 1, EventMessage, typeof(string)),
         ServiceManifest = extract(@"ServiceManifest=(\S+) ", 1, EventMessage, typeof(string)),
         InstanceId = extract(@"InstanceId=(\S+) ", 1, EventMessage, typeof(string)),
         ServicePackageActivationId = extract(@"ServicePackageActivationId=(\S+) ", 1, EventMessage, typeof(string)),
         NodeName = extract(@"NodeName=(\S+) ", 1, EventMessage, typeof(string)),
         Partition = extract(@"Partition=(\S+) ", 1, EventMessage, typeof(string)),
         StatelessInstance = extract(@"StatelessInstance=(\S+) ", 1, EventMessage, typeof(string)),
         StatefulReplica = extract(@"StatefulReplica=(\S+) ", 1, EventMessage, typeof(string))
```

EventId !=17523인 이벤트의 시간 차트를 반환합니다.

```kusto
ServiceFabricOperationalEvent
| join kind=leftouter ServiceFabricEvent on EventId
| where EventId != 17523
| summarize Count = count() by Timestamp = bin(TimeGenerated, 1h), strcat(tostring(EventId), " - ", case(EventName != "", EventName, "Unknown"))
| render timechart 
```

특정 서비스 및 노드와 함께 집계된 Service Fabric 작업 이벤트를 가져옵니다.

```kusto
ServiceFabricOperationalEvent
| where ApplicationName  != "" and ServiceName != ""
| summarize AggregatedValue = count() by ApplicationName, ServiceName, Computer 
```

교차 리소스 쿼리를 사용하여 EventId/EventName별 Service Fabric 이벤트 수를 렌더링합니다.

```kusto
app('PlunkoServiceFabricCluster').traces
| where customDimensions.ProviderName == 'Microsoft-ServiceFabric'
| extend EventId = toint(customDimensions.EventId), TaskName = tostring(customDimensions.TaskName)
| where EventId != 17523
| join kind=leftouter ServiceFabricEvent on EventId
| extend EventName = case(EventName != '', EventName, 'Undocumented')
| summarize ["Event Count"]= count() by bin(timestamp, 30m), EventName = strcat(tostring(EventId), " - ", EventName)
| render timechart
```

### <a name="view-service-fabric-application-events"></a>Service Fabric 애플리케이션 이벤트 보기

클러스터에 배포된 Reliable Services 및 Reliable Actors 애플리케이션에 대한 이벤트를 볼 수 있습니다.  Service Fabric 분석 페이지에서 **애플리케이션 이벤트**에 대한 그래프를 클릭합니다.

다음 쿼리를 실행하여 Reliable Services 애플리케이션의 이벤트를 봅니다.
```kusto
ServiceFabricReliableServiceEvent
| sort by TimeGenerated desc
```

일반적으로 배포 및 업그레이드에서 발생하는 서비스 `runasync`가 시작되고 완료되는 시점에 대한 다양한 이벤트를 볼 수 있습니다.

![Service Fabric 솔루션 Reliable Services](media/service-fabric-tutorial-monitor-cluster/oms-reliable-services-events-selection.png)

또한 ServiceName == "fabric:/Watchdog/WatchdogService"를 사용하여 Reliable Service에 대한 이벤트를 찾을 수 있습니다.

```kusto
ServiceFabricReliableServiceEvent
| where ServiceName == "fabric:/Watchdog/WatchdogService"
| project TimeGenerated, EventMessage
| order by TimeGenerated desc  
```
 
Reliable Actor 이벤트도 비슷한 방식으로 볼 수 있습니다.

```kusto
ServiceFabricReliableActorEvent
| sort by TimeGenerated desc
```
Reliable Actors에 대한 자세한 이벤트를 구성하려면 클러스터 템플릿의 진단 확장에 대한 구성에서 `scheduledTransferKeywordFilter`를 변경할 수 있습니다. 이러한 값에 대한 세부 정보는 [Reliable Actors 이벤트 참조](service-fabric-reliable-actors-diagnostics.md#keywords)를 참조하세요.

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

## <a name="view-performance-counters-with-azure-monitor-logs"></a>Azure Monitor 로그를 사용하여 성능 카운터 보기
성능 카운터를 보려면 [Azure Portal](https://portal.azure.com) 및 Service Fabric 분석 솔루션을 만든 리소스 그룹으로 이동합니다. 

**ServiceFabric(mysfomsworkspace)** 리소스, **Log Analytics 작업 영역**, **고급 설정**을 차례로 선택합니다.

**데이터**, **Windows 성능 카운터**를 차례로 클릭합니다. 사용하도록 선택할 수 있는 기본 카운터의 목록이 있고 컬렉션 간격도 설정할 수 있습니다. 수집할 [성능 카운터를 더 추가](service-fabric-diagnostics-event-generation-perf.md)할 수도 있습니다. 올바른 형식은 이 [문서에](/windows/desktop/PerfCtrs/specifying-a-counter-path) 설명되어 있습니다. **저장**을 클릭한 다음, **확인**을 클릭합니다.

[고급 설정] 블레이드를 닫고, **일반** 제목 아래에서 **작업 영역 요약**을 선택합니다. 사용하도록 설정된 각 솔루션에는 Service Fabric에 대한 타일을 포함하여 그래픽 타일이 있습니다. **Service Fabric** 그래프를 클릭하여 Service Fabric 분석 솔루션으로 이동합니다.

작업 채널 및 Reliable Services 이벤트에 대한 그래픽 타일이 있습니다. 선택한 카운터에 들어오는 데이터 흐름의 그래픽 표현이 **노드 메트릭** 아래에 표시됩니다. 

**컨테이너 메트릭** 그래프를 선택하여 추가 세부 정보를 확인합니다. 성능 카운터 데이터를 클러스터 이벤트와 유사하게 쿼리하고 Kusto 쿼리 언어를 사용하여 노드, 성능 카운터 이름 및 값을 필터링할 수도 있습니다.

## <a name="query-the-eventstore-service"></a>EventStore 서비스 쿼리
[EventStore 서비스](service-fabric-diagnostics-eventstore.md)는 특정 시점에서 클러스터 또는 워크로드의 상태를 파악할 수 있는 방법을 제공합니다. EventStore는 클러스터에서 이벤트를 유지 관리하는 상태 저장 Service Fabric 서비스입니다. 이벤트는 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md), REST 및 API를 통해 공개됩니다. EventStore는 클러스터를 직접 쿼리하여 클러스터의 엔터티에 대한 진단 데이터를 가져옵니다. EventStore에서 사용할 수 있는 전체 이벤트 목록을 보려면 [Service Fabric 이벤트](service-fabric-diagnostics-event-generation-operational.md)를 참조하세요.

EventStore API는 [Service Fabric 클라이언트 라이브러리](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)를 사용하여 프로그래밍 방식으로 쿼리할 수 있습니다.

GetClusterEventListAsync 함수를 통해 2018-04-03T18:00:00Z 및 2018-04-04T18:00:00Z 사이의 모든 클러스터 이벤트에 대한 요청 예제는 다음과 같습니다.

```csharp
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl, settings);

var clstrEvents = sfhttpClient.EventsStore.GetClusterEventListAsync(
    "2018-04-03T18:00:00Z",
    "2018-04-04T18:00:00Z")
    .GetAwaiter()
    .GetResult()
    .ToList();
```

2018년 9월에 클러스터 상태 및 모든 노드 이벤트에 대해 쿼리하고 출력하는 또 다른 예제는 다음과 같습니다.

```csharp
const int timeoutSecs = 60;
var clusterUrl = new Uri(@"http://localhost:19080"); // This example is for a Local cluster
var sfhttpClient = ServiceFabricClientFactory.Create(clusterUrl);

var clusterHealth = sfhttpClient.Cluster.GetClusterHealthAsync().GetAwaiter().GetResult();
Console.WriteLine("Cluster Health: {0}", clusterHealth.AggregatedHealthState.Value.ToString());


Console.WriteLine("Querying for node events...");
var nodesEvents = sfhttpClient.EventsStore.GetNodesEventListAsync(
    "2018-09-01T00:00:00Z",
    "2018-09-30T23:59:59Z",
    timeoutSecs,
    "NodeDown,NodeUp")
    .GetAwaiter()
    .GetResult()
    .ToList();
Console.WriteLine("Result Count: {0}", nodesEvents.Count());

foreach (var nodeEvent in nodesEvents)
{
    Console.Write("Node event happened at {0}, Node name: {1} ", nodeEvent.TimeStamp, nodeEvent.NodeName);
    if (nodeEvent is NodeDownEvent)
    {
        var nodeDownEvent = nodeEvent as NodeDownEvent;
        Console.WriteLine("(Node is down, and it was last up at {0})", nodeDownEvent.LastNodeUpAt);
    }
    else if (nodeEvent is NodeUpEvent)
    {
        var nodeUpEvent = nodeEvent as NodeUpEvent;
        Console.WriteLine("(Node is up, and it was last down at {0})", nodeUpEvent.LastNodeDownAt);
    }
}
```


## <a name="monitor-cluster-health"></a>클러스터 상태 모니터링
Service Fabric은 시스템 구성 요소와 Watchdog에서 모니터링하는 로컬 조건을 보고할 수 있는 상태 엔터티가 있는 [상태 모델](service-fabric-health-introduction.md)을 사용합니다. [상태 저장소](service-fabric-health-introduction.md#health-store) 는 모든 상태 데이터를 집계하여 엔터티가 정상인지 여부를 판단합니다.

자동으로 클러스터는 시스템 구성 요소에 의해 전송되는 상태 보고서로 채워집니다. 추가 정보는 [시스템 상태 보고서를 사용하여 문제 해결](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)을 참조하세요.

서비스 패브릭은 각각의 지원되는 [엔터티 유형](service-fabric-health-introduction.md#health-entities-and-hierarchy)에 대해 상태 쿼리를 노출합니다. 이러한 항목은 [FabricClient.HealthManager](/dotnet/api/system.fabric.fabricclient.healthmanager?view=azure-dotnet), PowerShell cmdlet 및 REST의 메서드를 사용하여 API를 통해 액세스할 수 있습니다. 이러한 쿼리는 집계된 성능 상태, 엔터티 상태 이벤트, 자식 상태(해당되는 경우), 엔터티가 정상이 아닐 때 비정상적 평가 및 자식 상태 통계(해당되는 경우) 등을 포함한 엔터티에 대한 완전한 상태 정보를 반환합니다.

### <a name="get-cluster-health"></a>클러스터 상태 가져오기
[Get-ServiceFabricClusterHealth cmdlet](/powershell/module/servicefabric/get-servicefabricclusterhealth)은 클러스터 엔터티의 상태를 반환하고, 애플리케이션과 노드(클러스터의 자식)의 상태를 포함합니다.  먼저 [Connect-ServiceFabricCluster cmdlet](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)을 사용하여 클러스터에 연결합니다.

클러스터 상태는 설명한 대로 구성된 11개 노드, 시스템 애플리케이션 및 fabric:/Voting입니다.

다음 예제에서는 기본 상태 정책을 사용하여 클러스터 상태를 가져옵니다. 11개 노드가 정상이지만, fabric:/Voting 애플리케이션이 Error이므로 클러스터 집계 상태가 Error입니다. 비정상 평가가 집계된 상태를 트리거한 조건을 어떻게 자세히 제공하는지 확인합니다.

```powershell
Get-ServiceFabricClusterHealth

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            33% (1/3) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                          
NodeHealthStates        : 
                          NodeName              : _nt2vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_4
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_3
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_2
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_1
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt1vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt3vm_0
                          AggregatedHealthState : Ok
                          
                          NodeName              : _nt2vm_4
                          AggregatedHealthState : Ok
                          
ApplicationHealthStates : 
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok
                          
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
HealthStatistics        : 
                          Node                  : 11 Ok, 0 Warning, 0 Error
                          Replica               : 4 Ok, 0 Warning, 0 Error
                          Partition             : 2 Ok, 0 Warning, 0 Error
                          Service               : 2 Ok, 0 Warning, 0 Error
                          DeployedServicePackage : 3 Ok, 1 Warning, 1 Error
                          DeployedApplication   : 1 Ok, 1 Warning, 1 Error
                          Application           : 0 Ok, 0 Warning, 1 Error
```

다음 예제에서는 사용자 지정 애플리케이션 정책을 사용하여 클러스터 상태를 가져옵니다. 오류 또는 경고 애플리케이션 및 노드만 가져오도록 결과를 필터링합니다. 이 예제에서는 노드가 모두 정상이므로 반환되지 않습니다. fabric:/Voting 애플리케이션만 애플리케이션 필터를 따릅니다. 사용자 지정 정책에서 경고를 fabric:/Voting 애플리케이션에 대한 오류로 간주하도록 지정하므로 애플리케이션이 오류 상태로 평가되며 클러스터도 마찬가지입니다.

```powershell
$appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/Voting"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error" -ExcludeHealthStatistics

AggregatedHealthState   : Error
UnhealthyEvaluations    : 
                          100% (1/1) applications are unhealthy. The evaluation tolerates 0% unhealthy applications.
                          
                          Application 'fabric:/Voting' is in Error.
                          
                            100% (5/5) deployed applications are unhealthy. The evaluation tolerates 0% unhealthy deployed applications.
                          
                            Deployed application on node '_nt2vm_3' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '8723eb73-9b83-406b-9de3-172142ba15f3' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376195593305'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_2' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2466f2f9-d5fd-410c-a6a4-5b1e00630cca' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376486201388'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_4' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '5faa5201-eede-400a-865f-07f7f886aa32' is in Error.
                          
                                    'System.Hosting' reported Warning for property 'CodePackageActivation:Code:SetupEntryPoint:131959376207396204'. The evaluation treats 
                          Warning as Error.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt2vm_0' is in Error.
                          
                                100% (1/1) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '204f1783-f774-4f3a-b371-d9983afaf059' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959375885791093'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1
                          
                            Deployed application on node '_nt3vm_0' is in Error.
                          
                                50% (1/2) deployed service packages are unhealthy.
                          
                                Service package for manifest 'VotingWebPkg' and service package activation ID '2533ae95-2d2a-4f8b-beef-41e13e4c0081' is in Error.
                          
                                    'System.Hosting' reported Error for property 'CodePackageActivation:Code:SetupEntryPoint:131959376108346272'.
                                    There was an error during CodePackage activation.The service host terminated with exit code:1                         
                          
NodeHealthStates        : None
ApplicationHealthStates : 
                          ApplicationName       : fabric:/Voting
                          AggregatedHealthState : Error
                          
HealthEvents            : None
```

### <a name="get-node-health"></a>노드 상태 가져오기
[Get-ServiceFabricNodeHealth cmdlet](/powershell/module/servicefabric/get-servicefabricnodehealth)은 노드 엔터티의 상태를 반환하고 노드에서 보고된 상태 이벤트를 포함합니다. 먼저 [Connect-ServiceFabricCluster cmdlet](/powershell/module/servicefabric/connect-servicefabriccluster?view=azureservicefabricps)을 사용하여 클러스터에 연결합니다. 다음 예제에서는 기본 상태 정책을 사용하여 특정 노드의 상태를 가져옵니다.

```powershell
Get-ServiceFabricNodeHealth _nt1vm_3
```

다음 예제에서는 클러스터의 모든 노드에 대한 상태를 가져옵니다.
```powershell
Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize
```

### <a name="get-system-service-health"></a>시스템 서비스 상태 가져오기 

시스템 서비스에 대한 집계 상태를 가져옵니다.

```powershell
Get-ServiceFabricService -ApplicationName fabric:/System | Get-ServiceFabricServiceHealth | select ServiceName, AggregatedHealthState | ft -AutoSize
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * Service Fabric 이벤트 보기
> * EventStore API를 통한 클러스터 이벤트 쿼리
> * 인프라 모니터링/성능 카운터 수집
> * 클러스터 상태 보고서 보기

다음으로, 클러스터 크기를 조정하는 방법을 알아보려면 다음 자습서로 진행하세요.
> [!div class="nextstepaction"]
> [클러스터의 크기 조정](service-fabric-tutorial-scale-cluster.md)

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
[template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/blob/master/7-VM-Windows-3-NodeTypes-Secure-NSG/AzureDeploy.json
