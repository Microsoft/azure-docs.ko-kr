---
title: Azure Service Fabric에 행위자 기반 서비스 만들기 | Microsoft Docs
description: Service Fabric Reliable Actors를 사용하여 C#에 행위자 기반 서비스를 처음 생성, 디버그 및 배포하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: d4aebe72-1551-4062-b1eb-54d83297f139
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/16/2018
ms.author: vturecek
ms.openlocfilehash: b6ca4810d86bb3c8413f0a740ac4483a848b8e10
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60726392"
---
# <a name="getting-started-with-reliable-actors"></a>Reliable Actors 시작
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-actors-get-started.md)
> * [Linux에서 Java](service-fabric-reliable-actors-get-started-java.md)

이 문서에서는 Visual Studio에서 간단한 Reliable Actor 애플리케이션을 만들고 디버그하는 과정을 안내합니다. Reliable Actors에 대한 자세한 내용은 [Service Fabric Reliable Actors 소개](service-fabric-reliable-actors-introduction.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

시작하기 전에 컴퓨터에 Visual Studio를 비롯한 Service Fabric 개발 환경이 설정되어 있는지 확인합니다. 자세한 내용은 [개발 환경 설정 방법](service-fabric-get-started.md)을 참조하세요.

## <a name="create-a-new-project-in-visual-studio"></a>Visual Studio에서 새 프로젝트 만들기

관리자 권한으로 Visual Studio 2015 이상을 시작하고 새로운 **Service Fabric 애플리케이션** 프로젝트를 만듭니다.

![Visual Studio용 서비스 패브릭 도구 - 새 프로젝트][1]

다음 대화 상자에서 선택 **행위자 서비스** 아래에서 **.NET Core 2.0** 서비스에 대 한 이름을 입력 합니다.

![서비스 패브릭 프로젝트 템플릿][5]

만든 프로젝트는 다음과 같은 구조를 표시합니다.

![서비스 패브릭 프로젝트 구조][2]

## <a name="examine-the-solution"></a>솔루션 검사

솔루션에는 3개의 프로젝트가 포함되어 있습니다.

* **애플리케이션 프로젝트(MyApplication)**. 이 프로젝트는 배포를 위해 모든 서비스를 함께 패키지합니다. 애플리케이션을 관리하기 위한 *ApplicationManifest.xml* 및 PowerShell 스크립트가 포함되어 있습니다.

* **인터페이스 프로젝트(HelloWorld.Interfaces)**. 이 프로젝트는 행위자에 대한 인터페이스 정의를 포함합니다. 행위자 인터페이스는 임의 이름의 모든 프로젝트에 정의할 수 있습니다.  인터페이스는 행위자 구현 및 행위자를 호출하는 클라이언트에 의해 공유되는 행위자 계약을 정의합니다.  클라이언트 프로젝트가 의존하므로, 일반적으로 행위자 구현과는 별도의 어셈블리에 정의하는 것이 적절합니다.

* **행위자 서비스 프로젝트(HelloWorld)**. 이 프로젝트는 행위자를 호스트할 Service Fabric 서비스를 정의합니다. 행위자의 *HelloWorld.cs* 구현이 포함되어 있습니다. 행위자 구현은 기본 형식(`Actor`)에서 파생되는 클래스로서, *MyActor.Interfaces* 프로젝트에 정의된 인터페이스를 구현합니다. 또한 행위자 클래스는 `ActorService` 인스턴스 및 `ActorId`를 허용하고 이를 기본 `Actor` 클래스에 전달하는 생성자를 구현해야 합니다.
    
    이 프로젝트에는 `ActorRuntime.RegisterActorAsync<T>()`를 사용하여 행위자 클래스를 Service Fabric 런타임에 등록하는 *Program.cs*도 포함되어 있습니다. `HelloWorld` 클래스는 이미 등록되어 있습니다. 프로젝트에 추가된 추가 행위자 구현을 `Main()` 메서드에도 등록해야 합니다.

## <a name="customize-the-helloworld-actor"></a>HelloWorld 행위자 사용자 지정

이 프로젝트 템플릿은 `IHelloWorld` 인터페이스의 일부 메서드를 정의한 후 `HelloWorld` 행위자 구현에 구현합니다.  행위자 서비스가 간단한 "Hello World" 문자열을 반환하도록 해당 메서드를 대체합니다.

*HelloWorld.Interfaces* 프로젝트의 *IHelloWorld.cs* 파일에서 인터페이스 정의를 다음과 같이 바꿉니다.

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

**HelloWorld** 프로젝트의 **HelloWorld.cs**에서 전체 클래스 정의를 다음과 같이 바꿉니다.

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

**Ctrl+Shift+B**를 눌러 프로젝트를 빌드하고 모든 내용이 컴파일되는지 확인합니다.

## <a name="add-a-client"></a>클라이언트 추가

행위자 서비스를 호출하는 간단한 콘솔 애플리케이션을 만듭니다.

1. 솔루션 탐색기에서 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가** > **새 프로젝트...** 를 클릭합니다.

2. **.NET Core** 프로젝트 형식에서 **콘솔 응용 프로그램(.NET Core)** 을 선택합니다.  프로젝트 이름을 *ActorClient*로 지정합니다.
    
    ![새 프로젝트 추가 대화 상자][6]    
    
    > [!NOTE]
    > 콘솔 애플리케이션은 일반적으로 Service Fabric에서 클라이언트로 사용하는 앱 유형이 아니지만 로컬 Service Fabric 클러스터를 사용하여 디버그 및 테스트하기 위한 편리한 예제를 만들어줍니다.

3. 콘솔 애플리케이션은 인터페이스 프로젝트와의 호환성과 기타 종속성을 유지하기 위해 64비트 애플리케이션이어야 합니다.  솔루션 탐색기에서 **ActorClient** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **속성**을 클릭합니다.  **빌드** 탭에서 **플랫폼 대상**을 **x64**로 설정합니다.
    
    ![빌드 속성][8]

4. 클라이언트 프로젝트에는 Reliable Actors NuGet 패키지가 필요합니다.  **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 클릭합니다.  패키지 관리자 콘솔에서 다음 명령을 입력합니다.
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    NuGet 패키지 및 모든 해당 종속성이 ActorClient 프로젝트에 설치됩니다.

5. 클라이언트 프로젝트에는 인터페이스 프로젝트에 대한 참조도 필요합니다.  ActorClient 프로젝트에서 마우스 오른쪽 단추로 **종속성**을 클릭한 다음 **참조 추가...** 를 클릭합니다.  **프로젝트 > 솔루션**(아직 선택하지 않은 경우)을 선택하고 **HelloWorld.Interfaces** 옆의 확인란을 선택합니다.  **확인**을 클릭합니다.
    
    ![참조 추가 대화 상자][7]

6. ActorClient 프로젝트에서 *Program.cs*의 전체 내용을 다음 코드로 바꿉니다.
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>실행 및 디버그

**F5** 키를 눌러 Service Fabric 개발 클러스터에서 로컬로 애플리케이션을 빌드, 배포 및 실행합니다.  배포 프로세스가 진행되는 동안 **출력** 창에서 진행률을 볼 수 있습니다.

![서비스 패브릭 디버깅 출력 창][3]

출력에 *The application is ready*가 포함되면 ActorClient 애플리케이션을 사용하여 서비스를 테스트할 수 있습니다.  솔루션 탐색기에서 마우스 오른쪽 단추로 **ActorClient** 프로젝트를 클릭하고 **디버그** > **새 인스턴스 시작**을 클릭합니다.  명령줄 애플리케이션에는 행위자 서비스의 출력이 표시됩니다.

![애플리케이션 출력][9]

> [!TIP]
> 서비스 패브릭 행위자 런타임에서는 일부 [행위자 메서드와 관련된 이벤트 및 성능 카운터](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters)를 내보냅니다. 진단 및 성능 모니터링에 유용합니다.

## <a name="next-steps"></a>다음 단계
[Reliable Actors가 Service Fabric 플랫폼을 사용하는 방법](service-fabric-reliable-actors-platform.md)에 대해 자세히 알아봅니다.


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png