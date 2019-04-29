---
title: Azure Service Fabric 앱 테스트를 위한 클러스터 노드 시작 및 중지 | Microsoft Docs
description: 클러스터 노드를 시작 및 중지하여 Service Fabric 애플리케이션을 테스트하기 위해 오류 주입을 사용하는 방법을 살펴봅니다.
services: service-fabric
documentationcenter: .net
author: LMWF
manager: rsinha
editor: ''
ms.assetid: f4e70f6f-cad9-4a3e-9655-009b4db09c6d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/12/2017
ms.author: lemai
ms.openlocfilehash: df0e53736c08fd2c26c467def7328e85f2989f26
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60718141"
---
# <a name="replacing-the-start-node-and-stop-node-apis-with-the-node-transition-api"></a>시작 노드 및 중지 노드 API를 노드 전환 API로 바꾸기

## <a name="what-do-the-stop-node-and-start-node-apis-do"></a>시작 노드 및 중지 노드 API는 어떤 작업을 수행하나요?

중지 노드 API(관리: [StopNodeAsync()][stopnode], PowerShell: [Stop-ServiceFabricNode][stopnodeps])는 Service Fabric 노드를 중지합니다.  Service Fabric 노드는 VM도 컴퓨터도 아닌 프로세스입니다. VM이나 컴퓨터는 계속 실행됩니다.  이 문서의 나머지 부분에서 "노드"는 Service Fabric 노드를 의미합니다.  노드를 중지하면 클러스터의 멤버가 아니고 서비스를 호스트할 수 없는 *중지됨* 상태가 되므로 *작동 중단* 노드가 시뮬레이트됩니다.  이 기능은 시스템에 오류를 삽입하여 애플리케이션을 테스트할 때 유용합니다.  시작 노드 API(관리: [StartNodeAsync()][startnode], PowerShell: [Start-ServiceFabricNode][startnodeps]])는 중지 노드 API를 되돌려 노드를 다시 정상 상태로 만듭니다.

## <a name="why-are-we-replacing-these"></a>이러한 API를 교체하는 이유는 무엇일까요?

앞에서 설명한 대로 *중지됨* Service Fabric 노드는 노드 중지 API를 사용할 때 의도적으로 대상으로 지정되는 노드입니다.  *작동 중단* 노드는 어떤 이유로든 작동 중단된 노드입니다(예: VM 또는 컴퓨터가 꺼져 있음).  노드 중지 API를 사용할 경우 시스템은 *중지됨* 노드와 *작동 중단* 노드 간을 구분하기 위한 정보를 제공하지 않습니다.

또한 이러한 API에서 반환하는 일부 오류는 충분한 설명을 포함하지 않습니다.  예를 들어 이미 *중지된* 노드에 대해 노드 중지 API를 호출하면 오류 *InvalidAddress*가 반환됩니다.  이러한 환경을 향상시킬 수 있습니다.

또한 노드 시작 API를 호출할 때까지 노드가 중지된 기간은 "무제한"이 됩니다.  이로 인해 문제가 발생하고 오류가 발생하기 쉬워질 수 있습니다.  예를 들어 사용자가 노드에 대해 노드 중지 API를 호출한 다음 그 사실을 잊어버리는 문제가 확인되었습니다.  따라서 나중에 노드가 *작동 중단* 또는 *중지됨* 상태인지가 명확해지지 않을 수 있습니다.


## <a name="introducing-the-node-transition-apis"></a>노드 전환 API 소개

새로운 API 집합에서 이러한 문제를 살펴보았습니다.  새로운 노드 전환 API(관리: [StartNodeTransitionAsync()][snt])는 Service Fabric 노드를 *중지됨* 상태로 전환하거나 *중지됨* 상태를 정상 작동 상태로 전환하는 데 사용할 수 있습니다.  API의 이름에 "Start"가 있다고 해서 노드 시작을 나타내는 것은 아닙니다.  시스템이 노드를 *중지됨* 또는 시작됨 상태로 전환하기 위해 실행하는 비동기 작업이 시작됨을 나타냅니다.

**사용 현황**

노드 전환 API가 호출 시 예외를 throw하지 않으면 시스템은 해당 비동기 작업을 수락하고 실행합니다.  호출이 성공했다고 해서 작업이 이미 완료된 것을 의미하지는 않습니다.  작업의 현재 상태에 대한 정보를 가져오려면 노드 전환 진행률 API(관리: [GetNodeTransitionProgressAsync()][gntp])를 이 작업에 대한 노드 전환 API를 호출할 때 사용하는 guid로 호출합니다.  노드 전환 진행률 API는 NodeTransitionProgress 개체를 반환합니다.  이 개체의 State 속성은 작업의 현재 상태를 지정합니다.  상태가 “실행 중”이면 작업이 실행되고 있는 것입니다.  상태가 완료됨이면 작업이 오류 없이 완료된 것입니다.  상태가 실패인 경우 작업을 실행하는 동안 문제가 발생한 것입니다.  Result 속성의 Exception 속성은 발생한 문제가 어떤 것인지를 나타냅니다.  State 속성에 대한 자세한 내용은 https://docs.microsoft.com/dotnet/api/system.fabric.testcommandprogressstate를 참조하고, 코드 예제는 아래의 "샘플 사용" 섹션을 참조하세요.


**중지된 노드 및 노드 다운 노드 간의 구분** 노드 전환 API를 사용하여 노드가 *중지*되는 경우 노드 쿼리의 출력(관리: [GetNodeListAsync()][nodequery], PowerShell: [Get-ServiceFabricNode][nodequeryps])은 이 노드에 true의 *IsStopped* 속성 값이 있음을 나타냅니다.  이 값은 *Down*으로 표시되는 *NodeStatus* 속성 값과 다릅니다.  *NodeStatus* 속성 값이 *Down*이지만 *IsStopped*가 false이면 노드는 노드 전환 API를 사용하여 중지되지 않은 것이며 다른 이유로 인해 *Down* 상태인 것입니다.  *IsStopped* 속성이 true이고 *NodeStatus* 속성이 *Down*이면 노드 전환 API를 사용하여 중지된 것입니다.

노드 전환 API를 사용하여 *중지된* 노드를 시작하면 클러스터의 일반 멤버로 다시 작동됩니다.  노드 쿼리 API의 출력에는 *IsStopped*가 false로, *NodeStatus*가 Down이 아닌 다른 상태(예: Up)로 표시됩니다.


**기간 제한** 노드 전환 API를 사용하여 노드를 중지할 때 필수 매개 변수 *stopNodeDurationInSeconds* 중 하나는 노드를 *중지됨* 상태로 유지할 시간(초)을 나타냅니다.  이 값은 허용되는 600~14400초 범위 내에 있어야 합니다.  이 시간이 만료되면 노드는 자동으로 작동 상태로 다시 시작됩니다.  사용 예제를 보려면 아래의 샘플 1을 참조하세요.

> [!WARNING]
> 노드 전환 API와 노드 중지 및 노드 시작 API를 혼합해서 사용하지 마세요.  노드 전환 API만 사용하는 것이 좋습니다.  노드 중지 API를 사용하여 노드가 이미 중지된 경우 노드 전환 API를 사용하기 전에 먼저 노드 시작 API를 사용하여 노드를 시작해야 합니다.

> [!WARNING]
> 동일한 노드에 대해 여러 노드 전환 API 호출을 동시에 수행할 수 없습니다.  이러한 경우 노드 전환 API는 ErrorCode 속성 값 NodeTransitionInProgress을 사용하여 FabricException을 throw합니다.  특정 노드의 노드 전환이 시작되면 해당 작업이 종료 상태(Completed, Faulted, 또는 ForceCancelled)에 도달할 때까지 기다렸다가 같은 노드에서 새 전환을 시작해야 합니다.  여러 다른 노드에 대한 병렬 노드 전환 호출이 허용됩니다.


#### <a name="sample-usage"></a>샘플 사용


**샘플 1** - 다음 샘플에서는 노드 전환 API를 사용하여 노드를 중지합니다.

```csharp
        // Helper function to get information about a node
        static Node GetNodeInfo(FabricClient fc, string node)
        {
            NodeList n = null;
            while (n == null)
            {
                n = fc.QueryManager.GetNodeListAsync(node).GetAwaiter().GetResult();
                Task.Delay(TimeSpan.FromSeconds(1)).GetAwaiter();
            };

            return n.FirstOrDefault();
        }

        static async Task WaitForStateAsync(FabricClient fc, Guid operationId, TestCommandProgressState targetState)
        {
            NodeTransitionProgress progress = null;

            do
            {
                bool exceptionObserved = false;
                try
                {
                    progress = await fc.TestManager.GetNodeTransitionProgressAsync(operationId, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                    exceptionObserved = true;
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                    exceptionObserved = true;
                }

                if (!exceptionObserved)
                {
                    Console.WriteLine("Current state of operation '{0}': {1}", operationId, progress.State);

                    if (progress.State == TestCommandProgressState.Faulted)
                    {
                        // Inspect the progress object's Result.Exception.HResult to get the error code.
                        Console.WriteLine("'{0}' failed with: {1}, HResult: {2}", operationId, progress.Result.Exception, progress.Result.Exception.HResult);

                        // ...additional logic as required
                    }

                    if (progress.State == targetState)
                    {
                        Console.WriteLine("Target state '{0}' has been reached", targetState);
                        break;
                    }
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (true);
        }

        static async Task StopNodeAsync(FabricClient fc, string nodeName, int durationInSeconds)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            // Create a Guid
            Guid guid = Guid.NewGuid();

            // Create a NodeStopDescription object, which will be used as a parameter into StartNodeTransition
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, durationInSeconds);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStopDescription from above, which will stop the target node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    // This is retryable
                }
                catch (FabricTransientException fte)
                {
                    // This is retryable
                }

                // Backoff
                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);
            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**샘플 2** - 다음 샘플에서는 *중지된* 노드를 시작합니다.  여기서는 첫 번째 샘플의 일부 도우미 메서드를 사용합니다.

```csharp
        static async Task StartNodeAsync(FabricClient fc, string nodeName)
        {
            // Uses the GetNodeListAsync() API to get information about the target node
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = n.NodeInstanceId;

            // Create a NodeStartDescription object, which will be used as a parameter into StartNodeTransition
            NodeStartDescription description = new NodeStartDescription(guid, n.NodeName, nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Completed).ConfigureAwait(false);
        }
```

**샘플 3** - 다음 샘플에서는 잘못된 사용을 보여 줍니다.  제공하는 *stopDurationInSeconds*가 허용 범위보다 크기 때문에 이 사용은 잘못된 것입니다.  StartNodeTransitionAsync()는 치명적인 오류로 인해 실패하므로 작업은 허용되지 않고 진행률 API가 호출되지 않습니다.  이 샘플에서는 첫 번째 샘플의 일부 도우미 메서드를 사용합니다.

```csharp
        static async Task StopNodeWithOutOfRangeDurationAsync(FabricClient fc, string nodeName)
        {
            Node n = GetNodeInfo(fc, nodeName);

            Guid guid = Guid.NewGuid();

            // Use an out of range value for stopDurationInSeconds to demonstrate error
            NodeStopDescription description = new NodeStopDescription(guid, n.NodeName, n.NodeInstanceId, 99999);

            try
            {
                await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
            }

            catch (FabricException e)
            {
                Console.WriteLine("Caught {0}", e);
                Console.WriteLine("ErrorCode {0}", e.ErrorCode);
                // Output:
                // Caught System.Fabric.FabricException: System.Runtime.InteropServices.COMException (-2147017629)
                // StopDurationInSeconds is out of range ---> System.Runtime.InteropServices.COMException: Exception from HRESULT: 0x80071C63
                // << Parts of exception omitted>>
                //
                // ErrorCode InvalidDuration
            }
        }
```

**샘플 4** - 다음 샘플에서는 노드 전환 API에 의해 시작된 작업이 허용되지만 나중에 실행하는 동안 오류가 발생하는 경우 노드 전환 진행률 API에서 반환될 오류 정보를 보여 줍니다.  이 경우 노드 전환 API가 존재하지 않는 노드를 시작하려고 하기 때문에 실패합니다.  이 샘플에서는 첫 번째 샘플의 일부 도우미 메서드를 사용합니다.

```csharp
        static async Task StartNodeWithNonexistentNodeAsync(FabricClient fc)
        {
            Guid guid = Guid.NewGuid();
            BigInteger nodeInstanceId = 12345;

            // Intentionally use a nonexistent node
            NodeStartDescription description = new NodeStartDescription(guid, "NonexistentNode", nodeInstanceId);

            bool wasSuccessful = false;

            do
            {
                try
                {
                    // Invoke StartNodeTransitionAsync with the NodeStartDescription from above, which will start the target stopped node.  Retry transient errors.
                    await fc.TestManager.StartNodeTransitionAsync(description, TimeSpan.FromMinutes(1), CancellationToken.None).ConfigureAwait(false);
                    wasSuccessful = true;
                }
                catch (OperationCanceledException oce)
                {
                    Console.WriteLine("Caught exception '{0}'", oce);
                }
                catch (FabricTransientException fte)
                {
                    Console.WriteLine("Caught exception '{0}'", fte);
                }

                await Task.Delay(TimeSpan.FromSeconds(5)).ConfigureAwait(false);

            }
            while (!wasSuccessful);

            // Now call StartNodeTransitionProgressAsync() until the desired state is reached.  In this case, it will end up in the Faulted state since the node does not exist.
            // When StartNodeTransitionProgressAsync()'s returned progress object has a State if Faulted, inspect the progress object's Result.Exception.HResult to get the error code.
            // In this case, it will be NodeNotFound.
            await WaitForStateAsync(fc, guid, TestCommandProgressState.Faulted).ConfigureAwait(false);
        }
```

[stopnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[stopnodeps]: https://msdn.microsoft.com/library/mt125982.aspx
[startnode]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.faultmanagementclient?redirectedfrom=MSDN
[startnodeps]: https://msdn.microsoft.com/library/mt163520.aspx
[nodequery]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.queryclient
[nodequeryps]: https://docs.microsoft.com/powershell/module/servicefabric/get-servicefabricnode
[snt]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
[gntp]: https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.testmanagementclient
