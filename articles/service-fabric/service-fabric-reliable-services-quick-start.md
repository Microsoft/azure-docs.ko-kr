---
title: C#에서 첫 번째 Service Fabric 애플리케이션 만들기 | Microsoft Docs
description: 상태 비저장 및 상태 저장 서비스를 사용하여 Microsoft Azure Service Fabric 애플리케이션 만들기 소개
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: d9b44d75-e905-468e-b867-2190ce97379a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: d27702983a4378becdbc67f3f156c92be3dc3af6
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130095"
---
# <a name="get-started-with-reliable-services"></a>Reliable Services로 시작하기
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-services-quick-start.md)
> * [Linux에서 Java](service-fabric-reliable-services-quick-start-java.md)
> 
> 

Azure Service Fabric 애플리케이션에는 코드를 실행하는 하나 이상의 서비스가 포함되어 있습니다. 이 가이드에서는 [Reliable Services](service-fabric-reliable-services-introduction.md)를 사용하여 상태 비저장 및 상태 저장 Service Fabric 애플리케이션을 만드는 방법을 보여 줍니다.  

## <a name="basic-concepts"></a>기본 개념
Reliable Services를 시작하려면 몇 가지 기본 개념만 이해하면 됩니다.

* **서비스 유형**: 서비스 구현입니다. 이름 및 버전 번호와 함께 `StatelessService` 및 기타 코드 또는 여기에 사용된 종속성을 확장하도록 사용자가 작성한 클래스에 의해 정의됩니다.
* **명명된 서비스 인스턴스**: 서비스를 실행하려는 경우 클래스 유형의 개체 인스턴스를 만드는 것처럼 서비스 유형의 명명된 인스턴스를 만듭니다. 서비스 인스턴스는 "fabric:/MyApp/MyService"와 같은 "fabric:/" 체계를 사용하는 URI 형식의 이름을 갖습니다.
* **서비스 호스트**: 생성한 명명된 서비스 인스턴스는 호스트 프로세스 내에서 실행해야 합니다. 서비스 호스트는 서비스의 인스턴스에서 실행할 수 있는 프로세스일 뿐입니다.
* **서비스 등록**: 서비스를 등록하면 모든 요소가 통합 등록됩니다. Service Fabric에서 실행할 인스턴스를 만들 수 있도록 서비스 호스트의 Service Fabric 런타임에 서비스 유형을 등록해야 합니다.  

## <a name="create-a-stateless-service"></a>상태 비저장 서비스 만들기
상태 비저장 서비스는 현재 클라우드 애플리케이션에서 정상인 서비스 유형입니다. 서비스 자체가 안정적으로 저장되거나 항상 사용 가능해야 하는 데이터를 포함하기 때문에 상태 비저장으로 간주됩니다. 상태 비저장 서비스의 인스턴스가 종료되면 모든 내부 상태가 손실됩니다. 이러한 서비스 유형에서는 Azure 테이블 또는 SQL 데이터베이스와 같은 외부 저장소에 상태를 항상 유지하고 이를 위해 높은 가용성과 안정성을 유지해야 합니다.

관리자 권한으로 Visual Studio 2015 또는 Visual Studio 2017을 시작하고 *HelloWorld*라는 새로운 Service Fabric 애플리케이션 프로젝트를 만듭니다.

![새 프로젝트 대화 상자를 사용하여 새 Service Fabric 애플리케이션 만들기](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject.png)

다음을 사용 하 여 상태 비저장 서비스 프로젝트를 만듭니다 **.NET Core 2.0** 라는 *HelloWorldStateless*:

![두 번째 대화 상자에서 상태 비저장 서비스 프로젝트 만들기](media/service-fabric-reliable-services-quick-start/hello-stateless-NewProject2.png)

이제 솔루션에는 2개의 프로젝트가 있습니다.

* *HelloWorld*. *서비스*가 포함된 *애플리케이션* 프로젝트입니다. 또한 애플리케이션을 배포하는 데 도움이 되는 다양한 PowerShell 스크립트 뿐만 아니라 애플리케이션을 설명하는 애플리케이션 매니페스트가 포함되어 있습니다.
* *HelloWorldStateless*. 서비스 프로젝트입니다. 상태 비저장 서비스 구현을 포함합니다.

## <a name="implement-the-service"></a>서비스 구현
서비스 프로젝트에서 **HelloWorldStateless.cs** 파일을 엽니다. 서비스 패브릭에서 서비스는 모든 비즈니스 논리를 실행할 수 있습니다. 서비스 API는 코드에 대한 두 진입점을 제공합니다.

* 장기 실행 계산 워크로드 등 모든 워크로드의 실행을 시작할 수 있는 *RunAsync*라는 개방형 진입점 메서드.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    ...
}
```

* ASP.NET Core와 같이 원하는 통신 스택을 연결할 수 있는 통신 진입점. 사용자 및 다른 서비스에서 요청을 수신하도록 시작할 수 있습니다.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}
```

이 자습서에서는 `RunAsync()` 진입점 메서드에 집중합니다. 바로 코드를 실행하기 시작할 수 있습니다.
프로젝트 템플릿에는 롤링 횟수를 증분하는 `RunAsync()` 의 샘플 구현이 포함됩니다.

> [!NOTE]
> 통신 스택을 사용하는 방법에 대한 자세한 내용은 [OWIN 자체 호스팅으로 서비스 패브릭 Web API 서비스](service-fabric-reliable-services-communication-webapi.md)
> 
> 

### <a name="runasync"></a>RunAsync
```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    long iterations = 0;

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", ++iterations);

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
}
```

서비스의 인스턴스가 배치되어 실행할 준비가 되면 플랫폼이 이 메서드를 호출합니다. 상태 비저장 서비스의 경우 이는 간단히 말해 서비스 인스턴스가 열릴 때를 의미합니다. 서비스 인스턴스를 종료해야 하는 경우 취소 토큰이 제공됩니다. 서비스 패브릭에서 서비스 인스턴스의 열림-닫힘 주기는 서비스의 수명 동안 전체적으로 여러 번 발생할 수 있습니다. 다음을 포함하여 여러 가지 이유로 발생할 수 있습니다.

* 시스템은 리소스 분산을 위해 서비스 인스턴스를 이동시킵니다.
* 오류는 코드에서 발생합니다.
* 애플리케이션 또는 시스템 업그레이드됩니다.
* 기본 하드웨어가 중단됩니다.

이러한 오케스트레이션은 서비스의 가용성을 높게 유지하고 제대로 균형을 유지하기 위해 시스템에 의해 관리됩니다.

`RunAsync()`는 동기적으로 차단하지 않아야 합니다. RunAsync 구현은 런타임이 지속되도록 작업을 반환하거나 장기 실행 또는 차단 작업을 대기해야 합니다. 이전 예제의 `while(true)` 루프에는 작업 반환 `await Task.Delay()`가 사용됩니다. 워크로드가 동기적으로 차단해야 하는 경우 `RunAsync` 구현에서 `Task.Run()`을 사용하여 새 작업을 예약해야 합니다.

워크로드 취소는 제공된 취소 토큰에 의해 조정된 공동의 노력입니다. 시스템은 계속하기 전에 태스크가 종료(성공적인 완료, 취소 또는 오류에 의해)될 때까지 기다립니다. 시스템이 취소를 요청할 때 가능한 한 빨리 취소 토큰을 받아들이고 작업을 마치며 `RunAsync()` 를 종료하는 것이 중요합니다.

이 상태 비저장 서비스 예에서는 개수가 로컬 변수에 저장됩니다. 하지만 이는 상태 비저장 서비스이므로 저장되는 값은 해당 서비스 인스턴스의 현재 주기에만 존재합니다. 서비스가 이동하거나 다시 시작되면 값이 손실됩니다.

## <a name="create-a-stateful-service"></a>상태 저장 서비스 만들기
서비스 패브릭은 상태 저장하는 서비스의 새로운 종류를 도입합니다. 상태 저장 서비스는 서비스를 사용하는 코드와 함께 위치한 서비스 자체 내에서 안정적으로 상태를 유지할 수 있습니다. 외부 저장소에 상태를 유지하지 않고도 서비스 패브릭에서 상태는 높은 가용성을 가집니다.

서비스가 이동하거나 다시 시작하는 경우에도 카운터 값을 상태 비저장에서 항상 사용 가능하고 지속되게 만들려면 상태 저장 서비스가 필요합니다.

동일한 *HelloWorld* 애플리케이션에서 애플리케이션 프로젝트의 서비스 참조를 마우스 오른쪽 단추로 클릭하고 **추가 -&gt; 새 Service Fabric 서비스**를 선택하여 새 서비스를 추가할 수 있습니다.

![Service Fabric 애플리케이션에 서비스 추가](media/service-fabric-reliable-services-quick-start/hello-stateful-NewService.png)

선택 **-> 상태 저장 서비스를.NET Core 2.0** 하 고 이름을 *HelloWorldStateful*합니다. **확인**을 클릭합니다.

![새 프로젝트 대화 상자를 사용하여 새 서비스 패브릭 상태 저장 서비스 만들기](media/service-fabric-reliable-services-quick-start/hello-stateful-NewProject.png)

애플리케이션에 이제 상태 비저장 서비스 *HelloWorldStateless* 및 상태 저장 서비스 *HelloWorldStateful*의 두 서비스가 있어야 합니다.

상태 저장 서비스에는 상태 비저장 서비스와 동일한 진입점이 있습니다. 주요 차이점은 상태를 안정적으로 저장할 수 있는 *상태 제공자* 의 가용성입니다. 서비스 패브릭은 신뢰할 수 있는 상태 관리자를 통해 복제된 데이터 구조를 만들 수 있는 [신뢰할 수 있는 컬렉션](service-fabric-reliable-services-reliable-collections.md)이라는 상태 제공자 구현과 함께 제공됩니다. 상태 저장 Reliable Service는 기본적으로 이 상태 제공자를 사용합니다.

다음 RunAsync 메서드를 포함하는 **HelloWorldStateful** 에서 *HelloWorldStateful.cs*를 엽니다.

```csharp
protected override async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following sample code with your own logic
    //       or remove this RunAsync override if it's not needed in your service.

    var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");

    while (true)
    {
        cancellationToken.ThrowIfCancellationRequested();

        using (var tx = this.StateManager.CreateTransaction())
        {
            var result = await myDictionary.TryGetValueAsync(tx, "Counter");

            ServiceEventSource.Current.ServiceMessage(this.Context, "Current Counter Value: {0}",
                result.HasValue ? result.Value.ToString() : "Value does not exist.");

            await myDictionary.AddOrUpdateAsync(tx, "Counter", 0, (key, value) => ++value);

            // If an exception is thrown before calling CommitAsync, the transaction aborts, all changes are
            // discarded, and nothing is saved to the secondary replicas.
            await tx.CommitAsync();
        }

        await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
    }
```

### <a name="runasync"></a>RunAsync
`RunAsync()` 은 상태 저장 및 상태 비저장 서비스에서 비슷하게 작동합니다. 그러나 상태 저장 서비스에서 플랫폼은 `RunAsync()`을 실행하기 전에 사용자 대신 추가 작업을 수행합니다. 이 작업은 신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션을 사용할 준비가 되도록 포함할 수 있습니다.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>신뢰할 수 있는 컬렉션 및 신뢰할 수 있는 상태 관리자
```csharp
var myDictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<string, long>>("myDictionary");
```

[IReliableDictionary](https://msdn.microsoft.com/library/dn971511.aspx) 는 서비스에서 상태를 안정적으로 저장하는데 사용할 수 있는 사전 구현입니다. 서비스 패브릭 및 신뢰할 수 있는 컬렉션을 사용하면 외부 영구 저장소 없이도 서비스에 데이터를 직접 저장할 수 있습니다. 신뢰할 수 있는 컬렉션은 데이터를 항상 사용할 수 있게 만듭니다. 서비스 패브릭은 서비스의 여러 *복제본* 을 만들고 관리하여 이를 달성합니다. 또한 해당 복제본 및 해당 상태 전환을 관리하는 복잡성을 추상화하는 API를 제공합니다.

신뢰할 수 있는 컬렉션은 사용자 지정 형식을 포함하여 모든 .NET 유형을 저장할 수 있습니다. 단, 몇 가지 주의 사항이 있습니다.

* 서비스 패브릭은 노드의 상태를 *복제* 하고 신뢰할 수 있는 컬렉션은 데이터를 각 복제본의 로컬 디스크에 저장합니다. 즉, 신뢰할 수 있는 컬렉션에 저장된 모든 항목이 *직렬화 가능*상태가 됩니다. 신뢰할 수 있는 컬렉션은 기본적으로 [DataContract](https://msdn.microsoft.com/library/system.runtime.serialization.datacontractattribute%28v=vs.110%29.aspx)를 사용하여 직렬화를 수행하므로 기본 직렬 변환기를 사용할 때 해당 유형이 [데이터 계약 직렬 변환기에서 지원되는지](https://msdn.microsoft.com/library/ms731923%28v=vs.110%29.aspx) 확인하는 것이 중요합니다.
* 신뢰할 수 있는 컬렉션의 트랜잭션을 커밋하면 고가용성을 위해 개체가 복제됩니다. 신뢰할 수 있는 컬렉션에 저장된 개체는 서비스의 로컬 메모리에 유지됩니다. 즉, 개체에 대한 로컬 참조가 있습니다.
  
   트랜잭션의 신뢰할 수 있는 컬렉션에서 업데이트 작업을 수행하지 않고 해당 개체의 로컬 인스턴스를 변환하지 않는 것이 중요합니다. 개체의 로컬 인스턴스에 대한 변경 내용은 자동으로 복제되지 않기 때문입니다. 개체를 디렉터리에 다시 삽입하거나 디렉터리의 *update* 메서드 중 하나를 사용해야 합니다.

신뢰할 수 있는 상태 관리자는 사용자에 대한 신뢰할 수 있는 컬렉션을 관리합니다. 언제든지 서비스의 어느 위치에서든 신뢰할 수 있는 컬렉션에 대한 신뢰할 수 있는 상태 관리자를 단순히 요청할 수 있습니다. 신뢰할 수 있는 상태 관리자는 참조를 다시 가져오도록 합니다. 클래스 멤버 변수 또는 속성에서 신뢰할 수 있는 컬렉션 인스턴스에 대한 참조를 저장하는 것이 좋습니다. 서비스 수명 주기에서 항상 참조가 인스턴스로 설정되어 있도록 특별히 주의해야 합니다. 신뢰할 수 있는 상태 관리자는 사용자를 위해 이 작업을 처리하고 반복 방문에 최적화됩니다.

### <a name="transactional-and-asynchronous-operations"></a>트랜잭션 및 비동기 작업
```csharp
using (ITransaction tx = this.StateManager.CreateTransaction())
{
    var result = await myDictionary.TryGetValueAsync(tx, "Counter-1");

    await myDictionary.AddOrUpdateAsync(tx, "Counter-1", 0, (k, v) => ++v);

    await tx.CommitAsync();
}
```

신뢰할 수 있는 컬렉션에는 LINQ를 제외하고 해당 `System.Collections.Generic` 및 `System.Collections.Concurrent` 항목이 수행하는 동일한 작업이 많이 있습니다. 신뢰할 수 있는 컬렉션의 작업은 비동기적입니다. 신뢰할 수 있는 컬렉션을 사용한 쓰기 작업에서는 I/O 작업을 수행하여 데이터를 복제하고 디스크에 보존하기 때문입니다.

신뢰할 수 있는 컬렉션 작업은 *트랜잭션*이므로 여러 신뢰할 수 있는 컬렉션 및 작업에서 상태를 일관성 있게 유지할 수 있습니다. 예를 들어 신뢰할 수 있는 큐에서 작업 항목을 제거하고, 작업을 수행하고, 신뢰할 수 있는 사전의 결과를 모두 단일 트랜잭션 내에 저장할 수 있습니다. 이는 원자성 작업으로 처리되며, 전체 작업이 성공하거나 롤백되도록 보장합니다. 항목을 큐에서 제거한 다음이지만 결과를 저장하기 전에 오류가 발생하면 전체 트랜잭션이 롤백되고 항목이 처리를 위해 큐에 남아 있습니다.

## <a name="run-the-application"></a>애플리케이션 실행
*HelloWorld* 애플리케이션으로 돌아갑니다. 이제 서비스를 빌드하고 배포할 수 있습니다. **F5**키를 누르면 애플리케이션이 빌드되고 로컬 클러스터에 배포됩니다.

서비스가 실행되기 시작한 후에 **진단 이벤트** 창에서 생성된 ETW(Windows용 이벤트 추적) 이벤트를 볼 수 있습니다. 애플리케이션의 상태 비저장 서비스 및 상태 저장 서비스 모두에서 이벤트가 표시됩니다. **일시 중지** 단추를 클릭하여 스트림을 일시 중지할 수 있습니다. 그런 다음 해당 메시지를 확장하여 메시지의 세부 정보를 검사할 수 있습니다.

> [!NOTE]
> 애플리케이션을 실행하기 전에 로컬 개발 클러스터가 실행 중인지 확인합니다. 로컬 환경 설정의 자세한 내용은 [시작 가이드](service-fabric-get-started.md) 를 확인합니다.
> 
> 

![Visual Studio에서 진단 이벤트 보기](media/service-fabric-reliable-services-quick-start/hello-stateful-Output.png)

## <a name="next-steps"></a>다음 단계
[Visual Studio에서 서비스 패브릭 애플리케이션 디버깅](service-fabric-debugging-your-application.md)

[시작: OWIN 자체 호스팅을 사용하는 Service Fabric Web API 서비스](service-fabric-reliable-services-communication-webapi.md)

[신뢰할 수 있는 컬렉션에 대한 자세한 정보](service-fabric-reliable-services-reliable-collections.md)

[애플리케이션 배포](service-fabric-deploy-remove-applications.md)

[애플리케이션 업그레이드](service-fabric-application-upgrade.md)

[신뢰할 수 있는 서비스에 대한 개발자 참조](https://msdn.microsoft.com/library/azure/dn706529.aspx)

