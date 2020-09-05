---
title: Azure Service Fabric에서 행위자 열거
description: 예제를 사용 하 여 Azure Service Fabric 응용 프로그램에서 Reliable Actors 및 해당 메타 데이터 열거에 대해 알아봅니다.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: dd3a61db32fb8e442beb42bd45c88da8559a29dd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016651"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Service Fabric Reliable Actors 열거
Reliable Actors 서비스를 사용하면 클라이언트가 서비스가 호스트하는 행위자에 대한 메타데이터를 열거할 수 있습니다. 행위자 서비스는 분할된 상태 저장 서비스이므로 열거는 파티션에 따라 수행됩니다. 각 파티션에는 많은 행위자가 포함될 수 있으므로 이 열거는 일련의 페이징된 결과로 반환됩니다. 페이지는 모든 페이지를 읽을 때까지 반복됩니다. 다음 예제에서는 행위자 서비스의 한 파티션에 있는 모든 활성 행위자의 목록을 만드는 방법을 보여줍니다.

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>다음 단계
* [행위자 상태 관리](service-fabric-reliable-actors-state-management.md)
* [행위자 수명 주기 및 가비지 수집](service-fabric-reliable-actors-lifecycle.md)
* [행위자 API 참조 설명서](/previous-versions/azure/dn971626(v=azure.100))
* [.NET 샘플 코드](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java 샘플 코드](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
