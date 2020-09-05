---
title: Azure Service Fabric에 대 한 비정상 및 장애 조치 (failover) 테스트 만들기
description: 서비스 패브릭 Chaos 테스트 및 장애 조치 테스트 시나리오를 통해 결함을 유도하고 서비스의 신뢰성을 확인합니다.
author: motanv
ms.topic: conceptual
ms.date: 10/1/2019
ms.author: motanv
ms.custom: devx-track-csharp
ms.openlocfilehash: 17f0af8c041042ddceccc1f8701e44ab8522840f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022125"
---
# <a name="testability-scenarios"></a>테스트 용이성 시나리오
클라우드 인프라 같은 대규모 분산 시스템은 본질적으로 불안정합니다. Azure 서비스 패브릭은 개발자에게 불안정한 인프라를 기반으로 실행되는 서비스를 작성할 수 있는 기능을 제공합니다. 고품질 서비스를 작성하려면 개발자는 이처럼 불안정한 인프라에서 서비스의 안정성을 테스트하도록 유도할 수 있어야 합니다.

오류 분석 서비스는 개발자에게 오류가 있는 상황에서 서비스를 테스트할 수 있도록 오류 작업을 유도하는 기능을 제공합니다. 그러나 특정 대상을 통한 오류 시뮬레이션으로는 한계가 있습니다. 테스트를 보다 길게 수행하려면 서비스 패브릭의 테스트 시나리오인 비정상 상황 테스트 및 장애 조치(failover) 테스트를 사용하세요. 이러한 시나리오는 장기간에 걸쳐 클러스터 전체에서 정상적 및 비정상적 인터리브 오류를 지속적으로 시뮬레이션합니다. 오류의 비율 및 종류로 테스트를 구성하면 C# API 또는 PowerShell을 통해 시작되어 클러스터 및 서비스에서 오류를 생성할 수 있습니다.

> [!WARNING]
> ChaosTestScenario는 회복력이 뛰어난 서비스 기반 비정상 상황으로 바뀝니다. 자세한 내용은 새 문서인 [제어되는 비정상 상황](service-fabric-controlled-chaos.md) 을 참조하세요.
> 
> 

## <a name="chaos-test"></a>비정상 상황 테스트
비정상 상황 시나리오는 전체 서비스 패브릭 클러스터에서 오류를 생성합니다. 이 시나리오에서는 일반적으로 수개월 또는 수년 후에 발견되는 오류를 수시간으로 압축합니다. 인터리브 오류를 높은 오류 비율과 결합하면 놓치기 쉬운 특이한 사례를 발견할 수 있습니다. 따라서 서비스 코드 품질이 대폭 개선됩니다.

### <a name="faults-simulated-in-the-chaos-test"></a>비정상 상황 테스트에서 시뮬레이션하는 오류
* 노드 다시 시작
* 배포된 코드 패키지 다시 시작
* 복제본 제거
* 복제본 다시 시작
* 주 복제본 이동(선택 사항)
* 보조 복제본 이동(선택 사항)

비정상 상황 테스트에서는 지정된 기간 동안 오류 및 클러스터 유효성 검사를 여러 차례 반복해서 실행합니다. 클러스터가 안정화되고 유효성 검사가 성공하는 데 걸리는 시간도 구성할 수 있습니다. 클러스터 유효성 검사에서 단일 오류가 발생하면 시나리오가 실패합니다.

예를 들어 1시간 동안 실행되고 최대 세 가지 오류가 동시에 발생하도록 설정된 테스트를 생각해 보세요. 이 테스트에서는 세 가지 오류를 유도한 후 클러스터 상태를 확인합니다. 클러스터가 비정상 상태가 되거나 1시간이 지날 때까지 이전 단계가 반복됩니다. 구성된 시간 내에 클러스터가 안정화되지 못한 경우처럼 반복 과정 중에 클러스터가 비정상 상태가 되면 예외와 함께 테스트가 실패합니다. 이 예외는 뭔가가 잘못되었으며 자세한 조사가 필요함을 나타냅니다.

현재 상태에서는 비정상 상황 테스트의 오류 생성 엔진이 안전 오류만 유도합니다. 다시 말해서 외부 오류가 없으면 쿼럼 또는 데이터 손실이 절대 발생하지 않습니다.

### <a name="important-configuration-options"></a>중요 구성 옵션
* **TimeToRun**: 테스트가 성공적으로 완료될 때까지 실행되는 총 시간입니다. 유효성 검사를 실패하기 전에 테스트가 일찍 완료될 수 있습니다.
* **MaxClusterStabilizationTimeout**: 클러스터가 정상 상태가 될 때까지 기다리는 최대 시간입니다. 이 시간이 지나면 테스트가 실패합니다. 클러스터 상태가 정상인지, 서비스 상태가 정상인지, 서비스 파티션에 대해 대상 복제본 세트 크기가 달성되었는지, InBuild 복제본이 없는지에 대한 검사가 수행됩니다.
* **MaxConcurrentFaults**: 각 반복에서 유도되는 동시 오류의 최대 수입니다. 숫자가 클수록 테스트가 공격적이므로 더 복잡한 장애 조치(failover)와 전환 조합으로 이어집니다. 외부 오류가 없으면 이 숫자를 얼마나 높게 구성하든 쿼럼 또는 데이터 손실이 없습니다.
* **EnableMoveReplicaFaults**: 주 복제본 또는 보조 복제본을 이동하게 만드는 오류를 설정하거나 해제합니다. 이러한 오류는 기본적으로 해제됩니다.
* **WaitTimeBetweenIterations**: 반복 사이의 대기 시간입니다(예: 오류 및 그에 해당하는 유효성 검사 후).

### <a name="how-to-run-the-chaos-test"></a>비정상 상황 테스트를 실행하는 방법
C# 샘플

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunChaosTestScenarioAsync(clusterConnection).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunChaosTestScenarioAsync(string clusterConnection)
    {
        TimeSpan maxClusterStabilizationTimeout = TimeSpan.FromSeconds(180);
        uint maxConcurrentFaults = 3;
        bool enableMoveReplicaFaults = true;

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        ChaosTestScenarioParameters scenarioParameters = new ChaosTestScenarioParameters(
          maxClusterStabilizationTimeout,
          maxConcurrentFaults,
          enableMoveReplicaFaults,
          timeToRun);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        ChaosTestScenario chaosScenario = new ChaosTestScenario(fabricClient, scenarioParameters);

        try
        {
            await chaosScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```

PowerShell

Service Fabric Powershell 모듈에는 비정상 상황 시나리오를 시작 하는 두 가지 방법이 포함 되어 있습니다. `Invoke-ServiceFabricChaosTestScenario` 는 클라이언트 기반 이며, 클라이언트 컴퓨터가 테스트를 통해 중간에 종료 되는 경우 더 이상 오류가 발생 하지 않습니다. 또는 컴퓨터 종료 시 테스트 실행을 유지 하는 일련의 명령이 있습니다. `Start-ServiceFabricChaos` Faultanalysisservice의입니다 라는 상태 저장 및 안정적인 시스템 서비스를 사용 하 여 TimeToRun가 가동 될 때까지 오류가 계속 해 서 발생 하는지 확인 합니다. `Stop-ServiceFabricChaos` 를 사용 하 여 시나리오를 수동으로 중지 하 고 `Get-ServiceFabricChaosReport` 보고서를 가져올 수 있습니다. 자세한 내용은 [Azure Service Fabric Powershell 참조](/powershell/module/servicefabric/?view=azureservicefabricps) 및 [Service Fabric 클러스터에서 비정상 상황 제어 유도](service-fabric-controlled-chaos.md)를 참조 하세요.

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$concurrentFaults = 3
$waitTimeBetweenIterationsSec = 60

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricChaosTestScenario -TimeToRunMinute $timeToRun -MaxClusterStabilizationTimeoutSec $maxStabilizationTimeSecs -MaxConcurrentFaults $concurrentFaults -EnableMoveReplicaFaults -WaitTimeBetweenIterationsSec $waitTimeBetweenIterationsSec
```


## <a name="failover-test"></a>장애 조치(failover) 테스트
장애 조치(failover) 테스트 시나리오는 비정상 상황 테스트의 한 버전으로써 특정 서비스 파티션을 대상으로 합니다. 다른 서비스에는 영향을 주지 않고 장애 조치(failover)가 특정 서비스 파티션에 미치는 영향을 테스트합니다. 대상 파티션 정보 및 기타 매개 변수를 사용하여 테스트를 구성할 경우 C# API 또는 PowerShell을 사용하는 클라이언트 쪽 도구로 실행되어 서비스 파티션에 대한 오류를 생성합니다. 한 쪽에서 비즈니스 논리가 실행되어 작업을 제공하는 동안 일련의 시뮬레이션된 오류 및 서비스 유효성 검사를 통해 시나리오가 반복됩니다. 서비스 유효성 검사에서 오류가 발생하면 추가 조사가 필요한 문제가 있다는 의미입니다.

### <a name="faults-simulated-in-the-failover-test"></a>장애 조치(failover) 테스트에서 시뮬레이션하는 오류
* 파티션이 호스팅되는 배포된 코드 패키지 다시 시작
* 주/보조 복제본 또는 상태 비저장 인스턴스 제거
* 주/보조 복제본 다시 시작(서비스가 지속되는 경우)
* 주 복제본 이동
* 보조 복제본 이동
* 파티션 다시 시작

장애 조치(failover) 테스트는 선택된 오류를 유도한 후 서비스에 대해 유효성 검사를 실행하여 안정화합니다. 비정상 상황 테스트에서 다중 오류가 가능한 것과는 달리 장애 조치(failover) 테스트는 오류를 한 번에 하나씩만 유도할 수 있습니다. 각 오류 후 서비스 파티션이 구성된 제한 시간 내에 안정화되지 않으면 테스트가 실패합니다. 이 테스트는 안전 오류만 유도합니다. 다시 말해서 외부 오류가 없으면 쿼럼 또는 데이터 손실이 발생하지 않습니다.

### <a name="important-configuration-options"></a>중요 구성 옵션
* **PartitionSelector**: 대상으로 삼을 파티션을 지정하는 선택기 개체입니다.
* **TimeToRun**: 테스트가 완료될 때까지 실행되는 총 시간입니다.
* **MaxServiceStabilizationTimeout**: 클러스터가 정상 상태가 될 때까지 기다리는 최대 시간입니다. 이 시간이 지나면 테스트가 실패합니다. 서비스 상태가 정상인지, 모든 파티션에 대해 대상 복제본 세트 크기가 달성되었는지, InBuild 복제본이 없는지에 대한 검사가 수행됩니다.
* **WaitTimeBetweenFaults**: 모든 오류 및 유효성 검사 주기 사이에 대기하는 시간입니다.

### <a name="how-to-run-the-failover-test"></a>장애 조치(failover) 테스트를 실행하는 방법
**C#**

```csharp
using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Chaos Test Scenario...");
        try
        {
            RunFailoverTestScenarioAsync(clusterConnection, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Chaos Test Scenario did not complete: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Chaos Test Scenario completed.");
        return 0;
    }

    static async Task RunFailoverTestScenarioAsync(string clusterConnection, Uri serviceName)
    {
        TimeSpan maxServiceStabilizationTimeout = TimeSpan.FromSeconds(180);
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);

        // The chaos test scenario should run at least 60 minutes or until it fails.
        TimeSpan timeToRun = TimeSpan.FromMinutes(60);
        FailoverTestScenarioParameters scenarioParameters = new FailoverTestScenarioParameters(
          randomPartitionSelector,
          timeToRun,
          maxServiceStabilizationTimeout);

        // Other related parameters:
        // Pause between two iterations for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenIterations = TimeSpan.FromSeconds(30);
        // Pause between concurrent actions for a random duration bound by this value.
        // scenarioParameters.WaitTimeBetweenFaults = TimeSpan.FromSeconds(10);

        // Create the scenario class and execute it asynchronously.
        FailoverTestScenario failoverScenario = new FailoverTestScenario(fabricClient, scenarioParameters);

        try
        {
            await failoverScenario.ExecuteAsync(CancellationToken.None);
        }
        catch (AggregateException ae)
        {
            throw ae.InnerException;
        }
    }
}
```


**PowerShell**

```powershell
$connection = "localhost:19000"
$timeToRun = 60
$maxStabilizationTimeSecs = 180
$waitTimeBetweenFaultsSec = 10
$serviceName = "fabric:/SampleApp/SampleService"

Connect-ServiceFabricCluster $connection

Invoke-ServiceFabricFailoverTestScenario -TimeToRunMinute $timeToRun -MaxServiceStabilizationTimeoutSec $maxStabilizationTimeSecs -WaitTimeBetweenFaultsSec $waitTimeBetweenFaultsSec -ServiceName $serviceName -PartitionKindSingleton
```
