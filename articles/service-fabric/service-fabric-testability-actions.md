---
title: Azure 마이크로 서비스에서 시뮬레이션 실패 | Microsoft 문서
description: 이 문서에서는 Microsoft Azure 서비스 패브릭에서 발견되는 테스트 용이성 작업에 대해 다룹니다.
services: service-fabric
documentationcenter: .net
author: motanv
manager: chackdan
editor: heeldin
ms.assetid: ed53ca5c-4d5e-4b48-93c9-e386f32d8b7a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/07/2017
ms.author: motanv
ms.openlocfilehash: 37a794387f3a2f02124805705d380ad9f1fc1270
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544796"
---
# <a name="testability-actions"></a>테스트 용이성 작업
불안정한 인프라를 시뮬레이트할 수 있도록 Azure 서비스 패브릭에서는 개발자에게 다양한 실제 오류 및 상태 전환을 시뮬레이트할 수 있는 방법을 제공합니다. 이러한 작업을 테스트 용이성 작업이라고 합니다. 이러한 작업은 특정 오류 주입, 상태 전환 또는 유효성 검사를 발생시키는 저수준 API입니다. 이러한 작업을 결합하여 서비스에 대한 포괄적인 테스트 시나리오를 작성할 수 있습니다.

서비스 패브릭은 이러한 작업으로 구성된 몇 가지 일반 테스트 시나리오를 제공합니다. 이러한 기본 제공 시나리오는 일반 상태 전환 및 장애 사례를 테스트하기 위해 신중하게 선택된 시나리오이므로 적극적으로 활용하는 것이 좋습니다. 그러나 기본 제공 시나리오에 포함되지 않거나 사용자 지정 애플리케이션에 맞게 조정된 범위를 추가하려는 경우 작업을 사용하여 사용자 지정 테스트 시나리오를 만들 수 있습니다.

C#으로 구현한 작업은 System.Fabric.dll 어셈블리에 있습니다. 시스템 패브릭 PowerShell 모듈은 Microsoft.ServiceFabric.Powershell.dll 어셈블리에 있습니다. 런타임 설치의 일부로 간편한 사용을 위해 ServiceFabric PowerShell 모듈이 설치됩니다.

## <a name="graceful-vs-ungraceful-fault-actions"></a>정상적인 오류 작업과 비정상적인 오류 작업 비교
테스트 용이성 작업은 두 주요 버킷으로 분류됩니다.

* 비정상적 오류: 이러한 오류는 머신 재시작, 프로세스 충돌 같은 실패를 시뮬레이션합니다. 이러한 실패의 경우 프로세스의 실행 컨텍스트가 갑자기 중지합니다. 즉, 애플리케이션을 다시 시작하기 전에 상태 정리를 실행할 수 없습니다.
* 정상적인 오류: 이러한 오류는 부하 분산에 의해 트리거되는 복제본 이동 또는 삭제 등의 정상적인 작업을 시뮬레이션합니다. 이러한 경우에는 서비스에서 종료에 대한 알림을 가져와서 상태 정리 후 종료할 수 있습니다.

다양한 정상 및 비정상 오류를 유도하면서 서비스 및 비즈니스 작업을 실행하면 보다 완벽하게 품질을 확인할 수 있습니다. 비정상적 오류의 경우 워크플로 도중에 서비스 프로세스가 갑자기 종료되는 시나리오를 실행합니다. 이 시나리오는 Service Fabric에서 서비스 복제본을 복원할 때 복구 경로를 테스트합니다. 데이터 일관성을 테스트하고 실패 후 서비스 상태가 올바르게 유지되는지 테스트하는 데 도움이 됩니다. 다른 실패 집합(정상적인 실패)은 서비스 패브릭에 의해 이동되는 복제본에 대해 서비스가 올바르게 반응하는지를 테스트 합니다. 이 테스트는 RunAsync 메서드의 취소 처리를 테스트합니다. 서비스에서 설정되는 취소 토큰을 확인하고 상태를 올바르게 저장한 후 RunAsync 메서드를 종료해야 합니다.

## <a name="testability-actions-list"></a>테스트 용이성 작업 목록
| 액션(Action) | 설명 | 관리 API | PowerShell cmdlet | 정상/비정상 오류 |
| --- | --- | --- | --- | --- |
| CleanTestState |테스트 드라이버가 비정상적으로 종료될 경우 클러스터에서 모든 테스트 상태를 제거합니다. |CleanTestStateAsync |Remove-ServiceFabricTestState |해당 없음 |
| InvokeDataLoss |서비스 파티션으로 데이터 손실을 유도합니다. |InvokeDataLossAsync |Invoke-ServiceFabricPartitionDataLoss |정상 |
| InvokeQuorumLoss |지정된 상태 저장 서비스 파티션을 쿼럼 손실에 배치합니다. |InvokeQuorumLossAsync |Invoke-ServiceFabricQuorumLoss |정상 |
| MovePrimary |상태 저장 서비스의 지정된 주 복제본을 지정된 클러스터 노드로 이동합니다. |MovePrimaryAsync |Move-ServiceFabricPrimaryReplica |정상 |
| MoveSecondary |상태 저장 서비스의 현재 보조 복제본을 다른 클러스터 노드로 이동합니다. |MoveSecondaryAsync |Move-ServiceFabricSecondaryReplica |정상 |
| RemoveReplica |클러스터에서 복제본을 제거하여 복제 오류를 시뮬레이션합니다. 그러면 복제본이 닫히고 역할 '없음'으로 전환되어 클러스터에서 해당 복제본의 모든 상태가 제거됩니다. |RemoveReplicaAsync |Remove-ServiceFabricReplica |정상 |
| RestartDeployedCodePackage |클러스터의 노드에 배포된 코드 패키지를 다시 시작하여 코드 패키지 프로세스 오류를 시뮬레이션합니다. 그러면 코드 패키지 프로세스가 취소되고 해당 프로세스에서 호스팅되는 모든 사용자 서비스 복제본이 다시 시작됩니다. |RestartDeployedCodePackageAsync |Restart-ServiceFabricDeployedCodePackage |비정상 |
| RestartNode |노드를 다시 시작하여 서비스 패브릭 클러스터 노드 오류를 시뮬레이션합니다. |RestartNodeAsync |Restart-ServiceFabricNode |비정상 |
| RestartPartition |파티션의 일부 또는 모든 복제본을 다시 시작하여 데이터 센터 블랙아웃 또는 클러스터 블랙아웃 시나리오를 시뮬레이션합니다. |RestartPartitionAsync |Restart-ServiceFabricPartition |정상 |
| RestartReplica |클러스터에 보관된 복제본을 다시 시작하고, 복제본을 닫은 후 다시 열어서 복제본 오류를 시뮬레이션합니다. |RestartReplicaAsync |Restart-ServiceFabricReplica |정상 |
| StartNode |클러스터에서 이미 중지된 노드를 시작합니다. |StartNodeAsync |Start-ServiceFabricNode |해당 없음 |
| StopNode |클러스터의 노드를 중지하여 노드 오류를 시뮬레이션합니다. StartNode가 호출될 때까지 노드가 계속 중지됩니다. |StopNodeAsync |Stop-ServiceFabricNode |비정상 |
| ValidateApplication |일반적으로 시스템에 일부 오류를 유도한 후 애플리케이션 내의 모든 서비스 패브릭 서비스의 가용성 및 상태를 검사합니다. |ValidateApplicationAsync |Test-ServiceFabricApplication |해당 없음 |
| ValidateService |일반적으로 시스템에 일부 오류를 유도한 후 서비스 패브릭 서비스의 가용성 및 상태를 검사합니다. |ValidateServiceAsync |Test-ServiceFabricService |해당 없음 |

## <a name="running-a-testability-action-using-powershell"></a>PowerShell을 사용하여 테스트 용이성 작업 실행
이 자습서에서는 PowerShell을 사용하여 테스트 용이성 작업을 실행하는 방법을 보여 줍니다. 로컬(one-box) 클러스터 또는 Azure 클러스터에 대해 테스트 용이성 작업을 실행하는 방법을 배울 것입니다. Microsoft.Fabric.Powershell.dll(서비스 패브릭PowerShell 모듈)은 Microsoft 서비스 패브릭 MSI를 설치할 때 자동으로 설치됩니다. PowerShell 프롬프트를 열면 이 모듈이 자동으로 로드됩니다.

자습서 세그먼트:

* [one-box 클러스터에 대해 작업 실행](#run-an-action-against-a-one-box-cluster)
* [Azure 클러스터에 대해 작업 실행](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>one-box 클러스터에 대해 작업 실행
로컬 클러스터에 대해 테스트 용이성 작업을 실행하려면 먼저 클러스터에 연결하고 관리자 모드에서 PowerShell 프롬프트를 엽니다. **Restart-ServiceFabricNode** 작업을 살펴보겠습니다.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

"Node1"이라는 노드에서 **Restart-ServiceFabricNode** 작업이 실행되고 있습니다. 완료 모드는 다시 시작 노드 작업이 실제로 성공했는지 여부를 확인하지 말라고 지정합니다. 완료 모드를 "Verify"로 지정하면 다시 시작 작업이 실제로 성공했는지 여부를 확인합니다. 노드 이름을 사용하여 직접 지정하는 대신 다음과 같이 파티션 키와 복제본의 종류를 통해 지정할 수 있습니다.

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Restart-ServiceFabricNode**를 사용하여 클러스터의 Service Fabric 노드를 다시 시작해야 합니다. 이 방법을 사용하면 Fabric.exe 프로세스가 중지됩니다. 이 프로세스는 해당 노드에 호스팅되는 모든 시스템 서비스 및 사용자 서비스 복제본을 다시 시작합니다. 이 API를 사용하여 서비스를 테스트하면 장애 조치(failover) 복구 경로를 따라 버그를 발견할 수 있습니다. 이는 클러스터의 노드 오류를 시뮬레이션하는 데 도움이 됩니다.

다음 스크린샷은 동작 중인 **Restart-ServiceFabricNode** 테스트 용이성 명령을 보여 줍니다.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

첫 번째 **Get-ServiceFabricNode**(Service Fabric PowerShell 모듈의 cmdlet)의 출력은 로컬 클러스터에 다섯 개의 노드가 있음을 보여줍니다 (Node.1~Node.5). Node.4 노드에서 테스트 용이성 작업(cmdlet) **Restart-ServiceFabricNode** 를 실행하면 노드의 가동 시간이 다시 설정된 것을 알 수 있습니다.

### <a name="run-an-action-against-an-azure-cluster"></a>Azure 클러스터에 대해 작업 실행
PowerShell을 사용하여 Azure 클러스터에 대해 테스트 용이성 작업을 실행하는 방법은 로컬 클러스터에 대해 작업을 실행하는 방법과 비슷합니다. 작업을 실행하려면 로컬 클러스터 대신 Azure 클러스터에 먼저 연결해야 한다는 점만 다릅니다.

## <a name="running-a-testability-action-using-c35"></a>C&#35;을 사용하여 테스트 용이성 작업 실행
C#을 사용하여 테스트 용이성 작업을 실행하려면 먼저 FabricClient를 사용하여 클러스터에 연결해야 합니다. 그런 다음 작업 실행에 필요한 매개 변수를 가져옵니다. 다른 매개 변수를 사용하여 같은 작업을 실행할 수 있습니다.
RestartServiceFabricNode 작업을 살펴보면 노드 정보(노드 이름 및 노드 인스턴스 ID)를 사용하여 노드를 실행할 수 있습니다.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

매개 변수 설명:

* **CompleteMode** 는 모드에서 다시 시작 작업이 실제로 성공했는지 여부를 확인하지 말라고 지정합니다. 완료 모드를 "Verify"로 지정하면 다시 시작 작업이 실제로 성공했는지 여부를 확인합니다.  
* **OperationTimeout** 은 TimeoutException 예외가 throw되기 전에 작업이 완료되어야 하는 시간을 설정합니다.
* **CancellationToken** 은 보류 중인 호출을 취소할 수 있습니다.

노드 이름을 사용하여 직접 지정하는 대신 파티션 키와 복제본의 종류를 통해 지정할 수 있습니다.

자세한 내용은 PartitionSelector 및 ReplicaSelector를 참조하세요.

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector 및 ReplicaSelector
### <a name="partitionselector"></a>PartitionSelector
PartitionSelector는 테스트 용이성에 노출되는 도우미이며 테스트 용이성 작업을 수행할 특정 파티션을 선택하는 데 사용됩니다. 파티션 ID가 이미 알려져 있는 경우 특정 파티션을 선택하는 데 사용할 수 있습니다. 또는 파티션 키를 제공하면 작업에서 파티션 ID를 내부적으로 해결합니다. 또한 임의의 파티션을 선택하는 옵션도 있습니다.

이 도우미를 사용하려면 PartitionSelector 개체를 만들고 Select* 메서드 중 하나를 사용하여 파티션을 선택합니다. 그런 다음 PartitionSelector 개체를 필요로 하는 API에 전달합니다. 옵션을 선택하지 않으면 기본적으로 임의의 파티션이 사용됩니다.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector는 테스트 용이성에 노출되는 도우미이며 테스트 용이성 작업을 수행할 특정 복제본을 선택하는 데 사용됩니다. 복제본 ID가 이미 알려져 있는 경우 특정 복제본을 선택하는 데 사용할 수 있습니다. 또한 주 복제본 또는 임의의 보조 복제본을 선택하는 옵션도 있습니다. ReplicaSelector는 PartitionSelector에서 파생되므로 테스트 용이성 작업을 수행할 복제본과 파티션을 모두 선택해야 합니다.

이 도우미를 사용하려면 ReplicaSelector 개체를 만들고 복제본 및 파티션을 선택하는 방법을 설정합니다. 그런 다음 개체를 필요로 하는 API에 전달할 수 있습니다. 옵션을 선택하지 않으면 기본적으로 임의의 복제본 및 파티션이 사용됩니다.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>다음 단계
* [테스트 용이성 시나리오](service-fabric-testability-scenarios.md)
* 서비스를 테스트하는 방법
  * [서비스 작업 중 오류 시뮬레이션](service-fabric-testability-workload-tests.md)
  * [서비스 대 서비스 통신 오류](service-fabric-testability-scenarios-service-communication.md)

