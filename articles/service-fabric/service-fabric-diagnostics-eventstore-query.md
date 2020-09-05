---
title: EventStore Api를 사용 하 여 클러스터 이벤트 쿼리
description: Azure Service Fabric EventStore API를 사용하여 플랫폼 이벤트를 쿼리하는 방법을 알아봅니다.
author: srrengar
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: srrengar
ms.custom: devx-track-csharp
ms.openlocfilehash: 614c8e5f76475c7e7af35db37334eb645ad553a3
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018606"
---
# <a name="query-eventstore-apis-for-cluster-events"></a>EventStore API를 통한 클러스터 이벤트 쿼리

이 문서에서는 Service Fabric 버전 6.2 이상에서 사용할 수 있는 EventStore API를 사용하여 쿼리하는 방법에 대해 설명합니다. EventStore 서비스에 대해 자세히 알아보려면 [EventStore 서비스 개요](service-fabric-diagnostics-eventstore.md)를 참조하세요. 현재 EventStore 서비스는 지난 7일 동안의 데이터에만 액세스할 수 있습니다(클러스터의 진단 데이터 보존 정책 기준).

>[!NOTE]
>EventStore API는 Azure에서 실행되는 Windows 클러스터에 대해서만 Service Fabric 버전 6.4로 일반 공급됩니다.

EventStore API는 REST 엔드포인트를 통해 직접 액세스하거나 프로그래밍 방식으로 액세스할 수 있습니다. 쿼리에 따라 올바른 데이터를 수집하는 데 필요한 몇 가지 매개 변수가 있습니다. 이러한 매개 변수에는 일반적으로 다음이 포함됩니다.
* `api-version`: 사용 중인 EventStore API 버전
* `StartTimeUtc`: 관심 있는 기간의 시작을 정의합니다.
* `EndTimeUtc`: 기간의 끝

이러한 매개 변수 외에도 다음과 같은 선택적 매개 변수가 있습니다.
* `timeout`: 요청 작업을 수행하기 위한 60초(기본값) 시간 제한을 재정의합니다.
* `eventstypesfilter`: 특정 이벤트 유형을 필터링하는 옵션이 제공됩니다.
* `ExcludeAnalysisEvents`: '분석' 이벤트를 반환하지 않습니다. 기본적으로 EventStore 쿼리는 가능한 경우 “분석” 이벤트와 함께 반환됩니다. 분석 이벤트는 일반적인 Service Fabric 이벤트 이외의 추가 컨텍스트 또는 정보를 포함하고 더 심층적으로 분석하는 다양한 운영 채널 이벤트입니다.
* `SkipCorrelationLookup`: 클러스터에서 상호 연결된 잠재적 이벤트를 찾지 않습니다. 기본적으로 EventStore는 클러스터 간의 이벤트를 상호 연결하려고 시도하고, 가능한 경우 이벤트를 함께 연결합니다. 

클러스터의 각 엔터티는 이벤트에 대한 쿼리가 될 수 있습니다. 또한 해당 유형의 모든 엔터티에 대한 이벤트를 쿼리할 수도 있습니다. 예를 들어 클러스터의 특정 노드 또는 모든 노드에 대한 이벤트를 쿼리할 수 있습니다. 이벤트를 쿼리할 수 있는 현재의 엔터티 집합은 쿼리를 구성하는 방법에 따라 다음과 같습니다.
* 클러스터: `/EventsStore/Cluster/Events`
* 노드(전체): `/EventsStore/Nodes/Events`
* 노드(특정): `/EventsStore/Nodes/<NodeName>/$/Events`
* 애플리케이션(전체): `/EventsStore/Applications/Events`
* 애플리케이션(특정): `/EventsStore/Applications/<AppName>/$/Events`
* 서비스(전체): `/EventsStore/Services/Events`
* 서비스(특정): `/EventsStore/Services/<ServiceName>/$/Events`
* 파티션(전체): `/EventsStore/Partitions/Events`
* 파티션(특정): `/EventsStore/Partitions/<PartitionID>/$/Events`
* 복제본(전체): `/EventsStore/Partitions/<PartitionID>/$/Replicas/Events`
* 복제본(특정): `/EventsStore/Partitions/<PartitionID>/$/Replicas/<ReplicaID>/$/Events`

>[!NOTE]
>애플리케이션 또는 서비스 이름을 참조할 때 쿼리에 "fabric:/" 접두사가 포함될 필요가 없습니다. 또한 애플리케이션 또는 서비스 이름에 "/"가 있는 경우 쿼리를 계속 작동하려면 "~"로 전환합니다. 예를 들어 애플리케이션이 "fabric:/App1/FrontendApp"으로 표시되면 애플리케이션 특정 쿼리가 `/EventsStore/Applications/App1~FrontendApp/$/Events`로 구성됩니다.
>또한 현재 서비스에 대한 상태 보고서가 해당 애플리케이션 아래에 표시되므로 적절한 애플리케이션 엔터티에 대한 `DeployedServiceHealthReportCreated` 이벤트도 쿼리할 수 있습니다. 

## <a name="query-the-eventstore-via-rest-api-endpoints"></a>REST API 엔드포인트를 통해 EventStore 쿼리

`<your cluster address>/EventsStore/<entity>/Events/`에 대한 `GET` 요청을 만들어 REST 엔드포인트를 통해 EventStore를 직접 쿼리할 수 있습니다.

예를 들어 `2018-04-03T18:00:00Z`와 `2018-04-04T18:00:00Z` 간의 모든 클러스터 이벤트를 쿼리하려면 요청이 다음과 같습니다.

```
Method: GET 
URL: http://mycluster:19080/EventsStore/Cluster/Events?api-version=6.4&StartTimeUtc=2018-04-03T18:00:00Z&EndTimeUtc=2018-04-04T18:00:00Z
```

이벤트를 반환하지 않거나 json으로 반환된 이벤트 목록을 반환합니다.

```json
Response: 200
Body:
[
  {
    "Kind": "ClusterUpgradeStart",
    "CurrentClusterVersion": "0.0.0.0:",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeType": "Rolling",
    "RollingUpgradeMode": "UnmonitoredAuto",
    "FailureAction": "Manual",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:18:59.4313064Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(0 1 2)",
    "UpgradeDomainElapsedTimeInMs": "78.5288",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:19:59.5729953Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeDomainComplete",
    "TargetClusterVersion": "6.2:1.0",
    "UpgradeState": "RollingForward",
    "UpgradeDomains": "(3 4)",
    "UpgradeDomainElapsedTimeInMs": "0",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.6271949Z",
    "HasCorrelatedEvents": false
  },
  {
    "Kind": "ClusterUpgradeComplete",
    "TargetClusterVersion": "6.2:1.0",
    "OverallUpgradeElapsedTimeInMs": "120196.5212",
    "EventInstanceId": "090add3c-8f56-4d35-8d57-a855745b6064",
    "TimeStamp": "2018-04-03T20:20:59.8134457Z",
    "HasCorrelatedEvents": false
  }
]
```

여기서는 `2018-04-03T18:00:00Z`와 `2018-04-04T18:00:00Z` 사이에서 이 클러스터가 `"OverallUpgradeElapsedTimeInMs": "120196.5212"` 동안에 `"CurrentClusterVersion": "0.0.0.0:"`에서 `"TargetClusterVersion": "6.2:1.0"`으로 처음 업그레이드되었을 때 첫 번째 업그레이드를 성공적으로 완료했음을 알 수 있습니다.

## <a name="query-the-eventstore-programmatically"></a>프로그래밍 방식으로 EventStore 쿼리

[Service Fabric 클라이언트 라이브러리](/dotnet/api/overview/azure/service-fabric?view=azure-dotnet#client-library)를 통해 EventStore를 프로그래밍 방식으로 쿼리할 수도 있습니다.

Service Fabric 클라이언트가 설정되면 `sfhttpClient.EventStore.<request>`와 같이 EventStore에 액세스하여 이벤트를 쿼리할 수 있습니다.

`GetClusterEventListAsync` 함수를 통해 `2018-04-03T18:00:00Z`과 `2018-04-04T18:00:00Z` 사이의 모든 클러스터 이벤트를 요청하는 예제는 다음과 같습니다.

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

## <a name="sample-scenarios-and-queries"></a>샘플 시나리오 및 쿼리

이벤트 저장소 REST API를 호출하여 클러스터의 상태를 파악하는 방법에 대한 몇 가지 예제는 다음과 같습니다.

*클러스터 업그레이드:*

지난 주에 클러스터가 성공적으로 업그레이드되었거나 업그레이드를 시도한 가장 최근 시간을 확인하려면 EventStore에서 “ClusterUpgradeComplete” 이벤트를 쿼리하여 API를 통해 최근에 완료된 업그레이드를 클러스터에 쿼리할 수 있습니다. `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ClusterUpgradeCompleted`

*클러스터 업그레이드 문제:*

마찬가지로 최근의 클러스터 업그레이드에 문제가 있는 경우 클러스터 엔터티에 대한 모든 이벤트를 쿼리할 수 있습니다. 업그레이드 시작 및 업그레이드가 성공적으로 수행된 각 UD를 포함한 다양한 이벤트가 표시됩니다. 또한 롤백이 시작된 지점에 대한 이벤트 및 해당 상태 이벤트도 표시됩니다. 여기에 사용할 쿼리는 다음과 같습니다. `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*노드 상태 변경 내용:*

지난 며칠 동안 변경된 노드 상태, 즉 플랫폼, 카오스 서비스 또는 사용자 입력에 따라 노드가 위/아래로 이동하거나 활성화되거나 비활성화된 경우를 확인하려면 다음 쿼리를 사용합니다. `https://mycluster.cloudapp.azure.com:19080/EventsStore/Nodes/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*응용 프로그램 이벤트:*

최근의 애플리케이션 배포 및 업그레이드를 추적할 수도 있습니다. 클러스터의 애플리케이션 이벤트를 모두 표시하려면 다음 쿼리를 사용합니다. `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z`

*애플리케이션에 대한 과거 상태:*

애플리케이션 수명 주기 이벤트만 보는 것 외에도, 특정 애플리케이션의 상태에 대한 기록 데이터를 볼 수도 있습니다. 이 작업은 데이터를 수집하려는 애플리케이션 이름을 지정하여 수행할 수 있습니다. 애플리케이션 상태 이벤트를 모두 가져오려면 다음 쿼리를 사용합니다. `https://mycluster.cloudapp.azure.com:19080/EventsStore/Applications/myApp/$/Events?api-version=6.4&starttimeutc=2018-03-24T17:01:51Z&endtimeutc=2018-03-29T17:02:51Z&EventsTypesFilter=ApplicationNewHealthReport` 만료되었을(TTL(Time to Live)이 경과된 경우) 수 있는 상태 이벤트를 포함하려면 쿼리의 끝에 `,ApplicationHealthReportExpired`를 추가하여 두 가지 유형의 이벤트를 필터링합니다.

*"myApp"의 모든 서비스에 대한 과거 상태:*

서비스에 대한 상태 보고서 이벤트는 현재 해당 애플리케이션 엔터티 아래에서 `DeployedServicePackageNewHealthReport` 이벤트로 표시됩니다. "App1"에 대한 서비스의 수행 방식을 확인하려면 다음 쿼리를 사용합니다. `https://winlrc-staging-10.southcentralus.cloudapp.azure.com:19080/EventsStore/Applications/myapp/$/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=DeployedServicePackageNewHealthReport`

*파티션 재구성:*

클러스터에서 발생한 모든 파티션 이동을 확인하려면 `PartitionReconfigured` 이벤트를 쿼리합니다. 이렇게 하면 클러스터의 문제를 진단할 때 특정 시간에 특정 노드에서 실행된 작업을 파악할 수 있습니다. 이를 수행하는 쿼리 샘플은 다음과 같습니다. `https://mycluster.cloudapp.azure.com:19080/EventsStore/Partitions/Events?api-version=6.4&starttimeutc=2018-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=PartitionReconfigured`

*카오스 서비스:*

클러스터 수준에서 노출되는 카오스 서비스가 시작되거나 중지된 경우에 대한 이벤트가 있습니다. 최근의 카오스 서비스 사용을 확인하려면 다음 쿼리를 사용합니다. `https://mycluster.cloudapp.azure.com:19080/EventsStore/Cluster/Events?api-version=6.4&starttimeutc=2017-04-22T17:01:51Z&endtimeutc=2018-04-29T17:02:51Z&EventsTypesFilter=ChaosStarted,ChaosStopped`
