---
title: Service Fabric 클러스터에서 비정상 상황 유도 | Microsoft Docs
description: 오류 삽입 및 클러스터 분석 서비스 API를 사용하여 클러스터의 비정상 상황을 관리합니다.
services: service-fabric
documentationcenter: .net
author: motanv
manager: anmola
editor: motanv
ms.assetid: 2bd13443-3478-4382-9a5a-1f6c6b32bfc9
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/05/2018
ms.author: motanv
ms.openlocfilehash: a1b334b34e8e234d9ce5cc5ad5cd77bf5ba7118c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60583878"
---
# <a name="induce-controlled-chaos-in-service-fabric-clusters"></a>Service Fabric 클러스터에서 제어되는 비정상 상황 유도
클라우드 인프라와 같은 대규모 분산 시스템은 기본적으로 안정적이지 않습니다. Azure Service Fabric을 사용하면 개발자들은 불안정한 인프라 위에 안정적인 분산 서비스를 작성할 수 있습니다. 불안정한 인프라 위에 강력한 분산 서비스를 작성하려는 경우 기반이 되는 불안정한 인프라가 결함으로 인해 복잡한 상태 전환을 겪을 때 개발자는 서비스의 안정성을 테스트할 수 있어야 합니다.

[오류 삽입 및 클러스터 분석 서비스](https://docs.microsoft.com/azure/service-fabric/service-fabric-testability-overview)(오류 분석 서비스라고도 함)는 개발자에게 서비스를 테스트할 수 있도록 오류를 유도하는 기능을 제공합니다. [파티션 다시 시작](https://docs.microsoft.com/powershell/module/servicefabric/start-servicefabricpartitionrestart?view=azureservicefabricps)과 같은 이러한 대상 시뮬레이트 오류는 가장 일반적인 상태 전환을 실행하는 데 도움이 됩니다. 그러나 대상 시뮬레이트 오류는 기본적으로 편향되어 있으므로 예측하기 어렵고 오래 진행되는 복잡한 상태 전환 시퀀스로만 나타나는 버그는 놓칠 수 있습니다. 편향되지 않는 테스트를 위해서는 비정상 상황을 사용할 수 있습니다.

비정상 상황은 장기간에 걸쳐 클러스터 전체에서 정상적 및 비정상적인 주기적 인터리브 오류를 지속적으로 시뮬레이트합니다. 정상적인 오류는 Service Fabric API 호출 집합으로 구성됩니다, 예를 들어 복제본 다시 시작 오류는 복제본에 대해 닫기가 수행될 후 열기가 수행되므로 정상적인 오류입니다. 복제본 제거, 주 복제본 이동 및 보조 복제본 이동은 비정상 상황에서 진행되는 다른 정상적인 오류입니다. 비정상적 오류는 프로세스 종료(예: 노드 다시 시작 및 코드 패키지 다시 시작)입니다. 

오류의 비율 및 종류로 비정상 상황을 구성하면 C#, PowerShell 또는 REST API를 통해 비정상 상황을 시작함으로써 클러스터 및 서비스에서 오류를 생성할 수 있습니다. 지정된 시간(예: 1시간) 동안 실행된 다음 자동으로 중지되도록 비정상 상황을 구성하거나, 언제든지 StopChaos API(C#, PowerShell 또는 REST)를 호출하여 중지할 수 있습니다.

> [!NOTE]
> 현재 양식에서, 비정상 상황은 안전 오류만 유도합니다. 즉, 외부 오류가 없으면 쿼럼 손실 또는 데이터 손실이 발생하지 않습니다.
>

비정상 상황이 실행되는 동안 현재 실행의 상태를 캡처하는 다양한 이벤트가 생성됩니다. 예를 들어 ExecutingFaultsEvent에는 비정상 상황이 해당 반복에서 실행하기로 결정한 모든 오류가 포함됩니다. ValidationFailedEvent에는 클러스터 유효성 검사 동안 발견된 유효성 검사 오류(상태 또는 안정성 문제)의 세부 정보가 포함됩니다. GetChaosReport API(C#, PowerShell 또는 REST)를 호출하여 비정상 상황 실행 보고서를 가져올 수 있습니다. 이러한 이벤트는 다음 두 가지 구성에 따라 잘림 정책이 적용되는 [신뢰할 수 있는 사전](https://docs.microsoft.com/azure/service-fabric/service-fabric-reliable-services-reliable-collections)에 유지됩니다. **MaxStoredChaosEventCount**(기본값 25000) 및 **StoredActionCleanupIntervalInSeconds**(기본값 3600). 가장 최근의 *MaxStoredChaosEventCount* 이벤트를 제외하고 비정상 상황이 확인하는 모든 *StoredActionCleanupIntervalInSeconds* 이벤트는 신뢰할 수 있는 사전에서 제거됩니다.

## <a name="faults-induced-in-chaos"></a>비정상 상황에서 유도되는 오류
비정상 상황에서는 전체 Service Fabric 클러스터에서 오류가 생성되며 수개월 또는 수년에 걸쳐 확인된 오류가 몇 시간으로 압축됩니다. 인터리브 오류를 높은 오류 비율과 결합하면 놓치기 쉬운 특이한 사례를 발견할 수 있습니다. 이러한 비정상 상황에 대처하는 연습을 통해 서비스 코드 품질을 대폭 개선할 수 있습니다.

비정상 상황은 다음 범주에서 오류를 유도합니다.

* 노드 다시 시작
* 배포된 코드 패키지 다시 시작
* 복제본 제거
* 복제본 다시 시작
* 주 복제본 이동(구성 가능)
* 보조 복제본 이동(구성 가능)

비정상 상황에서는 지정된 기간 동안 오류 및 클러스터 유효성 검사가 여러 차례 반복해서 실행됩니다. 클러스터가 안정화되고 유효성 검사가 성공하는 데 걸리는 시간을 구성할 수 있습니다. 클러스터 유효성 검사에 오류가 있으면 비정상 상황이 생성되며 UTC 타임스탬프 및 오류 세부 정보로 ValidationFailedEvent가 유지됩니다. 예를 들어 비정상 상황 인스턴스가 1시간 동안 실행되고 최대 세 가지 오류가 동시에 발생하도록 설정되었다고 가정하겠습니다. 비정상 상황은 세 개의 오류를 유도한 다음 클러스터 상태의 유효성을 검사합니다. 또한 StopChaosAsync API를 통해 명시적으로 서비스가 중지되거나 1시간이 경과할 때까지 이전 단계를 반복합니다. 반복 과정 중에 클러스터가 비정상 상태가 되면(전달된 MaxClusterStabilizationTimeout 내에서 안정화되지 못하거나 정상 상태가 되지 못함) 비정상 상황은 ValidationFailedEvent를 생성합니다. 이 이벤트는 무언가 잘못되었으며 자세한 조사가 필요할 수 있음을 나타냅니다.

비정상 상황이 유도한 오류를 가져오려면 GetChaosReport API(powershell, C# 또는 REST)를 사용할 수 있습니다. 이 API는 전달된 연속 토큰 또는 전달된 시간 범위를 기준으로 비정상 상황 보고서의 다음 세그먼트를 가져옵니다. 비정상 상황 보고서의 다음 세그먼트를 가져오도록 ContinuationToken을 지정하거나, StartTimeUtc 및 EndTimeUtc를 통해 시간 범위를 지정할 수 있지만 ContinuationToken과 시간 범위를 같은 호출 내에서 지정할 수는 없습니다. 100개가 넘는 비정상 상황 이벤트가 있는 경우 비정상 상황 보고서는 세그먼트에 100개 이하의 비정상 상황 이벤트가 포함되어 있는 세그먼트로 반환됩니다.

## <a name="important-configuration-options"></a>중요 구성 옵션
* **TimeToRun**: 성공적으로 완료될 때까지 비정상 상황이 실행되는 총 시간입니다. StopChaos API를 통해 TimeToRun 기간 동안 실행되기 전에 비정상 상황을 중지할 수 있습니다.

* **MaxClusterStabilizationTimeout**: 클러스터가 정상 상태가 될 때까지 기다리는 최대 시간입니다. 그 이후에는 ValidationFailedEvent가 생성됩니다. 이를 통해 복구되는 동안 클러스터의 부하를 줄일 수는 있습니다. 수행되는 검사는 다음과 같습니다.
  * 클러스터 상태가 정상인 경우
  * 서비스 상태가 정상인 경우
  * 서비스 파티션에 대해 목표 복제본 세트 크기가 달성된 경우
  * InBuild 복사본이 없음
* **MaxConcurrentFaults**: 각 반복에서 유도되는 동시 오류의 최대 수입니다. 이 수가 높을수록 비정상 상황은 좀 더 공격적으로 나타나며, 클러스터에 나타나는 장애 조치 및 상태 전환 조합도 좀 더 복잡해집니다. 

> [!NOTE]
> *MaxConcurrentFaults* 값 크기에 관계 없이, 비정상 상황은 외부 오류가 없을 때 쿼럼 또는 데이터 손실이 없도록 보장합니다.
>

* **EnableMoveReplicaFaults**: 주 복제본 또는 보조 복제본을 이동하게 만드는 오류를 설정하거나 해제합니다. 이러한 오류는 기본적으로 사용하도록 설정됩니다.
* **WaitTimeBetweenIterations**: 반복 사이의 대기 시간입니다. 즉, 일련의 오류를 실행하고 클러스터 상태에 대한 해당 유효성 검사를 완료한 후에 비정상 상황이 일시 중지되는 기간입니다. 이 값이 높을수록 평균 오류 삽입 속도는 높아집니다.
* **WaitTimeBetweenFaults**: 단일 반복에서 두 개의 연속 오류 사이에 대기하는 시간입니다. 이 값이 높을수록 오류의 동시성(또는 오류 간 중복)이 낮아집니다.
* **ClusterHealthPolicy**: 클러스터 상태 정책은 비정상 상황 반복 간에 클러스터의 상태를 확인하는 데 사용됩니다. 클러스터 상태에 오류가 있거나 오류 실행 중에 예기치 않은 예외가 발생하면 비정상 상황은 클러스터에 다시 복구할 시간을 제공하기 위해 30분 동안 대기했다가 다음 상태 검사를 수행합니다.
* **Context**: (string, string) 형식의 키-값 쌍 컬렉션입니다. 비정상 상황 실행에 대한 정보를 기록하기 위해 맵이 사용될 수 있습니다. 이러한 쌍은 100개 이하로만 존재할 수 있으며 각 문자열(키 또는 값)은 4095자 이하로만 설정할 수 있습니다. 비정상 상황 실행 시작 기능이 특정 실행에 대한 컨텍스트를 선택적으로 저장할 수 있게 이러한 맵을 설정합니다.
* **ChaosTargetFilter**: 이 필터는 비정상 상황의 대상을 특정 노드 유형 또는 특정 애플리케이션 인스턴스로만 지정하는 데 사용할 수 있습니다. ChaosTargetFilter를 사용하지 않으면 비정상 상황으로 인해 모든 클러스터 엔터티에 오류가 발생합니다. ChaosTargetFilter를 사용하면 비정상 상황으로 인해 Chaos ChaosTargetFilter 사양을 충족하는 엔터티에만 오류가 발생합니다. NodeTypeInclusionList 및 ApplicationInclusionList는 합집합 의미 체계만 허용합니다. 즉, NodeTypeInclusionList 및 ApplicationInclusionList의 교집합은 지정할 수 없습니다. 예를 들어, "해당 노드 유형에 해당할 경우에만 이 애플리케이션에 오류가 있다"고 지정할 수 없습니다. 엔터티가 NodeTypeInclusionList 또는 ApplicationInclusionList 중 하나에 포함되면 해당 엔터티는 ChaosTargetFilter를 사용하여 제외할 수 없습니다. applicationX가 ApplicationInclusionList에 표시되지 않더라도 일부 비정상 상황 반복에서 applicationX가 NodeTypeInclusionList에 포함된 nodeTypeY 노드에 있을 수 있으므로 오류가 있는 것으로 지정될 수 있습니다. NodeTypeInclusionList 및 ApplicationInclusionList 둘 다 null이거나 비어 있으면 ArgumentException이 발생합니다.
    * **NodeTypeInclusionList**: 비정상 상황 오류에 포함할 노드 유형의 목록입니다. 모든 유형의 오류(노드 다시 시작, 코드 패키지 다시 시작, 복제본 제거, 복제본 다시 시작, 주 복제본 이동 및 보조 복제본 이동)가 이러한 노드 유형의 노드에 대해 사용 가능으로 설정됩니다. nodetype(NodeTypeX)이 NodeTypeInclusionList에 표시되지 않으면 NodeTypeX의 노드에 대해 노드 수준 오류(예: NodeRestart)가 사용 가능으로 설정되지 않지만, ApplicationInclusionList의 애플리케이션이 NodeTypeX의 노드에 상주하게 될 경우 코드 패키지 및 복제본 오류는 NodeTypeX에 대해 여전히 사용될 수 있습니다. 최대 100개의 노드 유형 이름을 이 목록에 포함할 수 있으며, 이 수를 늘리려면 MaxNumberOfNodeTypesInChaosTargetFilter 구성에 대해 구성 업그레이드가 필요합니다.
    * **ApplicationInclusionList**: 비정상 상황 오류에 포함할 애플리케이션 URI의 목록입니다. 이러한 애플리케이션의 서비스에 속하는 모든 복제본은 비정상 상황에 의해 복제본 오류(복제본 다시 시작, 복제본 제거, 주 복제본 이동 및 보조 복제본 이동)로 수정될 수 있습니다. 비정상 상황은 코드 패키지가 이러한 애플리케이션의 복제본만 호스트하는 경우에만 코드 패키지를 다시 시작할 수 있습니다. 애플리케이션이 이 목록에 나타나지 않을 경우, 애플리케이션이 NodeTypeInclusionList에 포함된 노드 유형의 노드에 배치되면 일부 Chaos 반복에서 여전히 오류가 있는 것으로 지정될 수 있습니다. 그러나 applicationX가 배치 제약 조건을 통해 nodeTypeY에 연결되며 applicationX가 ApplicationInclusionList에 없고, nodeTypeY가 NodeTypeInclusionList에 없으면 applicationX는 절대 오류가 있는 것으로 지정되지 않습니다. 최대 1,000개의 애플리케이션 이름을 이 목록에 포함할 수 있으며, 이 수를 늘리려면 MaxNumberOfApplicationsInChaosTargetFilter 구성에 대해 구성 업그레이드가 필요합니다.

## <a name="how-to-run-chaos"></a>비정상 상황을 실행하는 방법

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Fabric;

using System.Diagnostics;
using System.Fabric.Chaos.DataStructures;

class Program
{
    private class ChaosEventComparer : IEqualityComparer<ChaosEvent>
    {
        public bool Equals(ChaosEvent x, ChaosEvent y)
        {
            return x.TimeStampUtc.Equals(y.TimeStampUtc);
        }
        public int GetHashCode(ChaosEvent obj)
        {
            return obj.TimeStampUtc.GetHashCode();
        }
    }

    static void Main(string[] args)
    {
        var clusterConnectionString = "localhost:19000";
        using (var client = new FabricClient(clusterConnectionString))
        {
            var startTimeUtc = DateTime.UtcNow;

            // The maximum amount of time to wait for all cluster entities to become stable and healthy. 
            // Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
            // During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
            var maxClusterStabilizationTimeout = TimeSpan.FromSeconds(30.0);

            var timeToRun = TimeSpan.FromMinutes(60.0);

            // MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
            // Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
            // The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
            // The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
            var maxConcurrentFaults = 3;

            // Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
            // the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
            // This map is set by the starter of the Chaos run to optionally store the context about the specific run.
            var startContext = new Dictionary<string, string>{{"ReasonForStart", "Testing"}};

            // Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
            var waitTimeBetweenIterations = TimeSpan.FromSeconds(10);

            // Wait time (in seconds) between consecutive faults within a single iteration. 
            // The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
            // The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
            var waitTimeBetweenFaults = TimeSpan.Zero;

            // Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
            var clusterHealthPolicy = new ClusterHealthPolicy
            {
                ConsiderWarningAsError = false,
                MaxPercentUnhealthyApplications = 100,
                MaxPercentUnhealthyNodes = 100
            };

            // All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
            // for nodes of type 'FrontEndType'
            var nodetypeInclusionList = new List<string> { "FrontEndType"};

            // In addition to the faults included by nodetypeInclusionList, 
            // restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
            // even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
            var applicationInclusionList = new List<string> { "fabric:/TestApp2" };

            // List of cluster entities to target for Chaos faults.
            var chaosTargetFilter = new ChaosTargetFilter
            {
                NodeTypeInclusionList = nodetypeInclusionList,
                ApplicationInclusionList = applicationInclusionList
            };

            var parameters = new ChaosParameters(
                maxClusterStabilizationTimeout,
                maxConcurrentFaults,
                true, /* EnableMoveReplicaFault */
                timeToRun,
                startContext,
                waitTimeBetweenIterations,
                waitTimeBetweenFaults,
                clusterHealthPolicy) {ChaosTargetFilter = chaosTargetFilter};

            try
            {
                client.TestManager.StartChaosAsync(parameters).GetAwaiter().GetResult();
            }
            catch (FabricChaosAlreadyRunningException)
            {
                Console.WriteLine("An instance of Chaos is already running in the cluster.");
            }

            var filter = new ChaosReportFilter(startTimeUtc, DateTime.MaxValue);

            var eventSet = new HashSet<ChaosEvent>(new ChaosEventComparer());

            string continuationToken = null;

            while (true)
            {
                ChaosReport report;
                try
                {
                    report = string.IsNullOrEmpty(continuationToken)
                        ? client.TestManager.GetChaosReportAsync(filter).GetAwaiter().GetResult()
                        : client.TestManager.GetChaosReportAsync(continuationToken).GetAwaiter().GetResult();
                }
                catch (Exception e)
                {
                    if (e is FabricTransientException)
                    {
                        Console.WriteLine("A transient exception happened: '{0}'", e);
                    }
                    else if(e is TimeoutException)
                    {
                        Console.WriteLine("A timeout exception happened: '{0}'", e);
                    }
                    else
                    {
                        throw;
                    }

                    Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
                    continue;
                }

                continuationToken = report.ContinuationToken;

                foreach (var chaosEvent in report.History)
                {
                    if (eventSet.Add(chaosEvent))
                    {
                        Console.WriteLine(chaosEvent);
                    }
                }

                // When Chaos stops, a StoppedEvent is created.
                // If a StoppedEvent is found, exit the loop.
                var lastEvent = report.History.LastOrDefault();

                if (lastEvent is StoppedEvent)
                {
                    break;
                }

                Task.Delay(TimeSpan.FromSeconds(1.0)).GetAwaiter().GetResult();
            }
        }
    }
}
```

```powershell
$clusterConnectionString = "localhost:19000"
$timeToRunMinute = 60

# The maximum amount of time to wait for all cluster entities to become stable and healthy. 
# Chaos executes in iterations and at the start of each iteration it validates the health of cluster entities. 
# During validation if a cluster entity is not stable and healthy within MaxClusterStabilizationTimeoutInSeconds, Chaos generates a validation failed event.
$maxClusterStabilizationTimeSecs = 30

# MaxConcurrentFaults is the maximum number of concurrent faults induced per iteration. 
# Chaos executes in iterations and two consecutive iterations are separated by a validation phase. 
# The higher the concurrency, the more aggressive the injection of faults -- inducing more complex series of states to uncover bugs. 
# The recommendation is to start with a value of 2 or 3 and to exercise caution while moving up.
$maxConcurrentFaults = 3

# Time-separation (in seconds) between two consecutive iterations of Chaos. The larger the value, the lower the fault injection rate.
$waitTimeBetweenIterationsSec = 10

# Wait time (in seconds) between consecutive faults within a single iteration. 
# The larger the value, the lower the overlapping between faults and the simpler the sequence of state transitions that the cluster goes through. 
# The recommendation is to start with a value between 1 and 5 and exercise caution while moving up.
$waitTimeBetweenFaultsSec = 0

# Passed-in cluster health policy is used to validate health of the cluster in between Chaos iterations. 
$clusterHealthPolicy = new-object -TypeName System.Fabric.Health.ClusterHealthPolicy
$clusterHealthPolicy.MaxPercentUnhealthyNodes = 100
$clusterHealthPolicy.MaxPercentUnhealthyApplications = 100
$clusterHealthPolicy.ConsiderWarningAsError = $False

# Describes a map, which is a collection of (string, string) type key-value pairs. The map can be used to record information about
# the Chaos run. There cannot be more than 100 such pairs and each string (key or value) can be at most 4095 characters long.
# This map is set by the starter of the Chaos run to optionally store the context about the specific run.
$context = @{"ReasonForStart" = "Testing"}

#List of cluster entities to target for Chaos faults.
$chaosTargetFilter = new-object -TypeName System.Fabric.Chaos.DataStructures.ChaosTargetFilter
$chaosTargetFilter.NodeTypeInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# All types of faults, restart node, restart code package, restart replica, move primary replica, and move secondary replica will happen
# for nodes of type 'FrontEndType'
$chaosTargetFilter.NodeTypeInclusionList.AddRange( [string[]]@("FrontEndType") )
$chaosTargetFilter.ApplicationInclusionList = new-object -TypeName "System.Collections.Generic.List[String]"

# In addition to the faults included by nodetypeInclusionList, 
# restart code package, restart replica, move primary replica, move secondary replica faults will happen for 'fabric:/TestApp2'
# even if a replica or code package from 'fabric:/TestApp2' is residing on a node which is not of type included in nodeypeInclusionList.
$chaosTargetFilter.ApplicationInclusionList.Add("fabric:/TestApp2")

Connect-ServiceFabricCluster $clusterConnectionString

$events = @{}
$now = [System.DateTime]::UtcNow

Start-ServiceFabricChaos -TimeToRunMinute $timeToRunMinute -MaxConcurrentFaults $maxConcurrentFaults -MaxClusterStabilizationTimeoutSec $maxClusterStabilizationTimeSecs -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ClusterHealthPolicy $clusterHealthPolicy -ChaosTargetFilter $chaosTargetFilter

while($true)
{
    $stopped = $false
    $report = Get-ServiceFabricChaosReport -StartTimeUtc $now -EndTimeUtc ([System.DateTime]::MaxValue)

    foreach ($e in $report.History) {

        if(-Not ($events.Contains($e.TimeStampUtc.Ticks)))
        {
            $events.Add($e.TimeStampUtc.Ticks, $e)
            if($e -is [System.Fabric.Chaos.DataStructures.ValidationFailedEvent])
            {
                Write-Host -BackgroundColor White -ForegroundColor Red $e
            }
            else
            {
                Write-Host $e
                # When Chaos stops, a StoppedEvent is created.
                # If a StoppedEvent is found, exit the loop.
                if($e -is [System.Fabric.Chaos.DataStructures.StoppedEvent])
                {
                    return
                }
            }
        }
    }

    Start-Sleep -Seconds 1
}
```
