---
title: ASP.NET Core와 서비스 통신 | Microsoft Docs
description: 상태 비저장 및 상태 저장 Reliable Services에서 ASP.NET Core를 사용하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: ''
ms.assetid: 8aa4668d-cbb6-4225-bd2d-ab5925a868f2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/12/2018
ms.author: vturecek
ms.openlocfilehash: 5a4b7514005da9e9a998dba014fa0ea6c014397a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774635"
---
# <a name="aspnet-core-in-service-fabric-reliable-services"></a>Service Fabric Reliable Services의 ASP.NET Core

ASP.NET Core는 웹앱, IoT 앱 및 모바일 백 엔드와 같은 최신 클라우드 기반의 인터넷 연결 애플리케이션을 빌드하기 위한 새로운 오픈 소스 겸 플랫폼 간 프레임워크입니다. 

이 문서는 NuGet 패키지의 **Microsoft.ServiceFabric.AspNetCore.*** 집합을 사용하여 Service Fabric Reliable Services에서 ASP.NET Core 서비스 호스팅에 대한 자세한 가이드입니다.

Service Fabric에서 ASP.NET Core의 소개 자습서 및 개발 환경 설정을 가져오는 방법에 대한 지침은 [.NET 애플리케이션 만들기](service-fabric-tutorial-create-dotnet-app.md)를 참조하세요.

이 문서의 나머지 부분에서는 ASP.NET Core를 잘 알고 있다고 가정합니다. 그렇지 않은 경우 [ASP.NET Core 기본 사항](https://docs.microsoft.com/aspnet/core/fundamentals/index)을 통해 읽어 보는 것이 좋습니다.

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 환경에서 ASP.NET Core

ASP.NET Core 및 Service Fabric 앱은 전체.NET Framework 뿐만 아니라 .NET Core에서도 실행할 수 있습니다. ASP.NET Core는 Service Fabric에서 다음 두 가지 방식으로 사용할 수 있습니다.
 - **게스트 실행 파일로 호스팅됨** - 주로 코드 변경 없이 Service Fabric에서 기존 ASP.NET Core 애플리케이션을 실행하는 데 사용됩니다.
 - **Reliable Service에서 실행** - 향상된 Service Fabric 런타임 통합과 상태 저장 ASP.NET Core 서비스를 허용합니다.

이 문서의 나머지 부분에서는 Service Fabric SDK와 함께 제공되는 ASP.NET Core 통합 구성 요소를 사용하여 Reliable Service 내에서 ASP.NET Core를 사용하는 방법에 대해 설명합니다. 

## <a name="service-fabric-service-hosting"></a>Service Fabric 서비스 호스팅

Service Fabric에서 서비스의 인스턴스 및/또는 복제본이 하나 이상 *서비스 호스트 프로세스*(서비스 코드를 실행하는 실행 파일)에서 실행됩니다. 서비스 작성자는 서비스 호스트 프로세스를 소유하고, Service Fabric은 이 사용자를 위해 해당 프로세스를 활성화하고 모니터링합니다.

기존 ASP.NET(MVC 5 이하)은 System.Web.dll을 통해 IIS에 긴밀하게 결합됩니다. ASP.NET Core는 웹 서버와 웹 애플리케이션 간의 분리를 제공합니다. 이렇게 하면 웹 애플리케이션을 여러 웹 서버 간에 이동할 수 있으며 웹 서버를 *자체 호스팅*할 수도 있습니다. 즉, IIS와 같은 전용 웹 서버 소프트웨어에서 소유한 프로세스가 아니라 자신의 프로세스에서 웹 서버를 시작할 수 있습니다. 

Service Fabric 서비스와 ASP.NET을 게스트 실행 파일로 또는 Reliable Service에서 결합하려면 서비스 호스트 프로세스 내에서 ASP.NET을 시작할 수 있어야 합니다. ASP.NET Core 자체 호스팅을 사용하면 이 작업을 수행할 수 있습니다.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Reliable Service에서 ASP.NET Core 호스팅
일반적으로 자체 호스팅되는 ASP.NET Core 애플리케이션은 `Program.cs`의 `static void Main()` 메서드와 같이 애플리케이션의 진입점에 WebHost를 만듭니다. 이 경우 WebHost의 수명 주기는 프로세스의 수명 주기와 바인딩됩니다.

![프로세스에서 ASP.NET Core 호스팅][0]

그러나 애플리케이션 진입점은 Reliable Service에서 WebHost를 만들 수 있는 적절한 위치가 아닙니다. 이는 애플리케이션 진입점이 서비스 유형을 Service Fabric 런타임에 등록하는 데에만 사용되어 해당 서비스 유형의 인스턴스를 만들 수 있기 때문입니다. WebHost는 Reliable Service 자체에서 만들어야 합니다. 서비스 호스트 프로세스 내에서 서비스 인스턴스 및/또는 복제본은 여러 수명 주기를 거칠 수 있습니다. 

Reliable Service 인스턴스는 `StatelessService` 또는 `StatefulService`에서 파생되는 서비스 클래스로 표현됩니다. 서비스를 위한 통신 스택은 서비스 클래스의 `ICommunicationListener` 구현에 포함되어 있습니다. `Microsoft.ServiceFabric.AspNetCore.*` NuGet 패키지에는 Reliable Service의 Kestrel 또는 HttpSys에 대한 ASP.NET Core WebHost를 시작하고 관리하는 `ICommunicationListener` 구현이 포함되어 있습니다.

![Reliable Service에서 ASP.NET Core 호스팅][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`Microsoft.ServiceFabric.AspNetCore.*` NuGet 패키지의 Kestrel 및 HttpSys에 대한 `ICommunicationListener` 구현은 비슷한 사용 패턴을 갖지만 각 웹 서버에 따라 약간 다른 작업을 수행합니다. 

두 통신 수신기는 다음 인수를 사용하는 생성자를 제공합니다.
 - **`ServiceContext serviceContext`**: 실행 중인 서비스에 대한 정보가 포함된 `ServiceContext` 개체입니다.
 - **`string endpointName`**: ServiceManifest.xml의 `Endpoint` 구성 이름입니다. 이는 주로 두 통신 수신기가 서로 다른 곳입니다. HttpSys는 `Endpoint` 구성이 **필요**한 반면 Kestrel은 필요하지 않습니다.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: `IWebHost`를 만들고 반환하는 사용자 구현 람다입니다. 이렇게 하면 일반적으로 ASP.NET Core 애플리케이션에서 구성하는 방식으로 `IWebHost`를 구성할 수 있습니다. 람다는 사용하는 Service Fabric 통합 옵션과 제공하는 `Endpoint` 구성에 따라 생성된 URL을 제공합니다. 그런 다음 해당 URL을 수정하거나 그대로 사용하여 웹 서버를 시작할 수 있습니다.

## <a name="service-fabric-integration-middleware"></a>Service Fabric 통합 미들웨어
`Microsoft.ServiceFabric.AspNetCore` NuGet 패키지에는 Service Fabric 인식 미들웨어를 추가하는 `IWebHostBuilder`의 `UseServiceFabricIntegration` 확장 메서드가 포함되어 있습니다. 이 미들웨어는 Kestrel 또는 HttpSys `ICommunicationListener`를 구성하여 Service Fabric 명명 서비스에 고유한 서비스 URL을 등록한 다음, 클라이언트 요청의 유효성을 검사하여 클라이언트가 적절한 서비스에 연결하고 있는지 확인합니다. 이는 Service Fabric과 같은 공유 호스트 환경에서 필요합니다. 여기서는 여러 웹 애플리케이션이 동일한 물리적 머신 또는 가상 머신에서 실행될 수 있지만, 클라이언트에서 실수로 잘못된 서비스에 연결하지 못하도록 고유한 호스트 이름을 사용하지 않습니다. 이 시나리오에 대해서는 다음 섹션에서 자세히 설명합니다.

### <a name="a-case-of-mistaken-identity"></a>잘못된 ID의 경우
프로토콜에 관계없이 서비스 복제본은 고유한 IP:포트 조합에서 수신 대기합니다. 서비스 복제본이 IP:포트 엔드포인트에서 수신 대기를 시작하면 클라이언트 또는 기타 서비스에서 검색할 수 있는 Service Fabric 명명 서비스에 해당 엔드포인트 주소를 보고합니다. 서비스에서 동적으로 할당된 애플리케이션 포트를 사용하는 경우 서비스 복제본은 이전에 동일한 물리적 머신 또는 가상 머신에 있었던 다른 서비스의 동일한 IP:포트 엔드포인트를 우연히 사용할 수 있습니다. 이로 인해 클라이언트에서 실수로 잘못된 서비스에 연결할 수 있습니다. 다음과 같은 이벤트 시퀀스가 발생하면 이러한 경우가 나타날 수 있습니다.

 1. 서비스 A가 HTTP를 통해 10.0.0.1:30000에서 수신 대기합니다. 
 2. 클라이언트가 서비스 A를 확인하고 10.0.0.1:30000 주소를 가져옵니다.
 3. 서비스 A가 다른 노드로 이동합니다.
 4. 서비스 B가 10.0.0.1에 위치하고 우연히 동일한 30000 포트를 사용합니다.
 5. 클라이언트가 캐시된 10.0.0.1:30000 주소로 서비스 A에 연결하려고 시도합니다.
 6. 이제 클라이언트가 서비스 B에 성공적으로 연결되어 잘못된 서비스에 연결되었다는 것을 인식하지 못합니다.

이로 인해 진단할 수 없는 버그가 임의의 시간에 발생할 수 있습니다. 

### <a name="using-unique-service-urls"></a>고유한 서비스 URL 사용
이를 방지하기 위해 서비스에서 고유 식별자로 엔드포인트를 명명 서비스에 게시한 다음, 클라이언트 요청 중에 해당 고유 식별자의 유효성을 검사할 수 있습니다. 이 작업은 적대적이지 않은 테넌트가 신뢰할 수 있는 환경에서 서비스 간의 공동 작업이며, 적대적인 테넌트 환경에서는 보안 서비스 인증을 제공하지 않습니다.

신뢰할 수 있는 환경에서 `UseServiceFabricIntegration` 메서드로 추가된 미들웨어는 명명 서비스에 게시된 주소에 고유 식별자를 자동으로 추가하고 각 요청에서 해당 식별자의 유효성을 검사합니다. 식별자가 일치하지 않으면 미들웨어에서 즉시 HTTP 410 없음 응답을 반환합니다.

동적으로 할당된 포트를 사용하는 서비스는 이 미들웨어를 사용해야 합니다.

고유한 고정 포트를 사용하는 서비스는 공동 작업 환경에서 이 문제를 가지고 있지 않습니다. 고유한 고정 포트는 일반적으로 클라이언트 애플리케이션이 연결될 잘 알려진 포트가 필요한 외부 연결 서비스에 사용됩니다. 예를 들어 대부분의 인터넷 연결 웹 애플리케이션은 웹 브라우저 연결에 80 또는 443 포트를 사용합니다. 이 경우에는 고유 식별자를 사용할 수 없습니다.

다음 다이어그램에서는 미들웨어를 사용하도록 설정된 요청 흐름을 보여 줍니다.

![Service Fabric ASP.NET Core 통합][2]

Kestrel과 HttpSys `ICommunicationListener` 구현은 모두 똑같은 방식으로 이 메커니즘을 사용합니다. HttpSys는 기본 *http.sys* 포트 공유 기능을 사용하여 고유한 URL 경로에 따라 요청을 내부적으로 구분할 수 있지만, 이 기능은 앞에서 설명한 시나리오에서 HTTP 503 및 HTTP 404 오류 상태 코드가 발생하기 때문에 HttpSys `ICommunicationListener` 구현에서 *사용되지 않습니다*. HTTP 503 및 HTTP 404는 일반적으로 이미 다른 오류를 나타내는 데 사용되므로 클라이언트에서 오류의 의미를 확인하는 것이 어렵습니다. 따라서 Kestrel과 HttpSys `ICommunicationListener` 구현은 모두 `UseServiceFabricIntegration` 확장 메서드로 제공되는 미들웨어에서 표준화되므로 클라이언트는 HTTP 410 응답에서 서비스 엔드포인트 재확인 작업만 수행하면 됩니다.

## <a name="httpsys-in-reliable-services"></a>Reliable Services의 HttpSys
HttpSys는 **Microsoft.ServiceFabric.AspNetCore.HttpSys** NuGet 패키지를 가져와서 신뢰할 수 있는 서비스에서 사용할 수 있습니다. 이 패키지에는 `ICommunicationListener` 구현인 `HttpSysCommunicationListener`가 포함되어 있으므로 웹 서버로 HttpSys를 사용하여 신뢰할 수 있는 서비스 내에 ASP.NET Core WebHost를 만들 수 있습니다.

HttpSys는 [Windows HTTP 서버 API](https://msdn.microsoft.com/library/windows/desktop/aa364510(v=vs.85).aspx)(영문)를 기반으로 합니다. IIS에서 사용하는 *http.sys* 커널 드라이버를 사용하여 HTTP 요청을 처리하고 이를 웹 애플리케이션을 실행하는 프로세스로 라우팅합니다. 이렇게 하면 동일한 물리적 머신 또는 가상 머신의 여러 프로세스가 동일한 포트에서 고유한 URL 경로 또는 호스트 이름으로 구분되는 웹 애플리케이션을 호스팅할 수 있습니다. 이러한 기능은 동일한 클러스터에서 여러 웹 사이트를 호스팅하는 Service Fabric에서 유용합니다.

>[!NOTE]
>HttpSys 구현은 Windows 플랫폼에서만 작동합니다.

다음 다이어그램에서는 HttpSys가 포트 공유를 위해 Windows에서 *http.sys* 커널 드라이버를 사용하는 방식을 보여 줍니다.

![http.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>상태 비저장 서비스의 HttpSys
상태 비저장 서비스에서 `HttpSys`를 사용하려면 `CreateServiceInstanceListeners` 메서드를 재정의하고 `HttpSysCommunicationListener` 인스턴스를 반환합니다.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>상태 저장 서비스의 HttpSys

현재 `HttpSysCommunicationListener`는 기본 *http.sys* 포트 공유 기능에 따른 복잡성 때문에 상태 저장 서비스에서 사용하도록 설계되지 않았습니다. 자세한 내용은 HttpSys를 통한 동적 포트 할당에 대한 다음 섹션을 참조하세요. 상태 저장 서비스의 경우 Kestrel이 권장되는 웹 서버입니다.

### <a name="endpoint-configuration"></a>엔드포인트 구성

HttpSys를 포함하여 Windows HTTP 서버 API를 사용하는 웹 서버에는 `Endpoint` 구성이 필요합니다. Windows HTTP 서버 API를 사용하는 웹 서버는 먼저 *http.sys*로 URL을 예약해야 합니다(일반적으로 [netsh](https://msdn.microsoft.com/library/windows/desktop/cc307236(v=vs.85).aspx) 도구로 수행). 이 작업을 수행하려면 기본적으로 서비스에 없는 상승된 권한이 필요합니다. *ServiceManifest.xml*에 있는 `Endpoint` 구성의 `Protocol` 속성에 대한 "http" 또는 "https" 옵션은 특히 Service Fabric 런타임에서 [*강력한 와일드카드*](https://msdn.microsoft.com/library/windows/desktop/aa364698(v=vs.85).aspx) URL 접두사를 사용하는 대신 URL을 *http.sys*에 등록하도록 지시하는 데 사용됩니다.

예를 들어 서비스에 대해 `http://+:80`을 예약하려면 ServiceManifest.xml에서 다음 구성을 사용해야 합니다.

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

그리고 엔드포인트 이름은 `HttpSysCommunicationListener` 생성자에 전달해야 합니다.

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>정적 포트에 HttpSys 사용
HttpSys에 정적 포트를 사용하려면 `Endpoint` 구성에 포트 번호를 제공합니다.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>동적 포트에 HttpSys 사용
HttpSys에 동적으로 할당된 포트를 사용하려면 `Endpoint` 구성에서 `Port` 속성을 생략합니다.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

`Endpoint` 구성으로 할당된 동적 포트는 *호스트 프로세스당* 하나의 포트만 제공합니다. 현재 Service Fabric 호스팅 모델을 사용하면 동일한 프로세스에서 여러 서비스 인스턴스 및/또는 복제본을 호스팅할 수 있습니다. 즉, 각각 `Endpoint` 구성을 통해 할당될 때 동일한 포트를 공유합니다. 여러 HttpSys 인스턴스는 기본 *http.sys* 포트 공유 기능을 사용하여 포트를 공유할 수 있지만, 클라이언트 요청에 대해 발생하는 복잡성으로 인해 `HttpSysCommunicationListener`에서 지원되지 않습니다. 동적 포트를 사용하는 경우 Kestrel이 권장되는 웹 서버입니다.

## <a name="kestrel-in-reliable-services"></a>Reliable Services의 Kestrel
Kestrel은 **Microsoft.ServiceFabric.AspNetCore.Kestrel** NuGet 패키지를 가져와서 신뢰할 수 있는 서비스에서 사용할 수 있습니다. 이 패키지에는 `ICommunicationListener` 구현인 `KestrelCommunicationListener`가 포함되어 있으므로 웹 서버로 Kestrel을 사용하여 신뢰할 수 있는 서비스 내에 ASP.NET Core WebHost를 만들 수 있습니다.

Kestrel은 libuv(플랫폼 간 비동기 I/O 라이브러리)를 기반으로 하는 ASP.NET Core용 플랫폼 간 웹 서버입니다. HttpSys와 달리 Kestrel은 *http.sys*와 같은 중앙 집중식 엔드포인트 관리자를 사용하지 않으며, HttpSys와는 달리, Kestrel은 여러 프로세스 간의 포트 공유도 지원하지 않습니다. Kestrel의 각 인스턴스는 고유한 포트를 사용해야 합니다.

![Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>상태 비저장 서비스의 Kestrel
상태 비저장 서비스에서 `Kestrel`을 사용하려면 `CreateServiceInstanceListeners` 메서드를 재정의하고 `KestrelCommunicationListener` 인스턴스를 반환합니다.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>상태 저장 서비스의 Kestrel
상태 저장 서비스에서 `Kestrel`을 사용하려면 `CreateServiceReplicaListeners` 메서드를 재정의하고 `KestrelCommunicationListener` 인스턴스를 반환합니다.

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

이 예제에서는 WebHost 종속성 삽입 컨테이너에 `IReliableStateManager`의 singleton 인스턴스를 제공합니다. 이는 반드시 필요한 것이 아니지만 MVC 컨트롤러 작업 메서드에서 `IReliableStateManager` 및 신뢰할 수 있는 컬렉션을 사용할 수 있습니다.

상태 저장 서비스에서는 `Endpoint` 구성 이름이 `KestrelCommunicationListener`에 제공되지 **않습니다**. 자세한 내용은 다음 섹션에서 설명합니다.

### <a name="configure-kestrel-to-use-https"></a>HTTPS를 사용하도록 Kestrel 구성
서비스에서 Kestrel로 HTTPS를 사용하도록 설정할 때는 여러 개의 수신 대기 옵션을 설정해야 합니다.  EndpointHttps 엔드포인트를 사용하고 특정 포트(예: 포트 443)에서 수신 대기하도록 `ServiceInstanceListener`을 업데이트합니다. Kestrel 서버를 사용하도록 웹 호스트를 구성할 때는 Kestrel이 모든 네트워크 인터페이스에 대해 IPv6 주소를 수신 대기하도록 구성해야 합니다. 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

자습서에서 사용되는 전체 예를 보려면 [HTTPS를 사용하도록 Kestrel 구성](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)을 참조하세요.


### <a name="endpoint-configuration"></a>엔드포인트 구성
`Endpoint` 구성은 Kestrel을 사용하는 데 필요하지 않습니다. 

Kestrel은 간단한 독립 실행형 웹 서버입니다. HttpSys(또는 HttpListener)와는 달리 시작하기 전에 URL을 등록할 필요가 없기 때문에 *ServiceManifest.xml*에서 `Endpoint` 구성이 필요하지 않습니다. 

#### <a name="use-kestrel-with-a-static-port"></a>정적 포트로 Kestrel 사용
Kestrel에서 정적 포트를 사용하려면 ServiceManifest.xml의 `Endpoint` 구성에 해당 포트를 구성하면 됩니다. 이는 반드시 필요한 것이 아니지만 잠재적인 두 가지 이점을 제공합니다.
 1. 포트가 애플리케이션 포트 범위에 속하지 않으면 Service Fabric에서 OS 방화벽을 통해 해당 포트가 열립니다.
 2. `KestrelCommunicationListener`를 통해 제공된 URL은 이 포트를 사용합니다.

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

`Endpoint`가 구성되면 해당 이름을 `KestrelCommunicationListener` 생성자로 전달해야 합니다. 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

`Endpoint` 구성을 사용하지 않으면 `KestrelCommunicationListener` 생성자에서 이름을 생략합니다. 이 경우 동적 포트가 사용됩니다. 자세한 내용은 다음 섹션을 참조하세요.

#### <a name="use-kestrel-with-a-dynamic-port"></a>동적 포트로 Kestrel 사용
`Endpoint` 구성의 자동 포트 할당에서 *호스트 프로세스*당 고유한 포트를 하나씩 할당하고 단일 호스트 프로세스에 여러 Kestrel 인스턴스가 포함될 수 있기 때문에, Kestrel은 ServiceManifest.xml의 `Endpoint` 구성에서 자동 포트 할당을 사용할 수 없습니다. 각 Kestrel 인스턴스를 고유한 포트에서 열어야 하지만 Kestrel에서 포트 공유를 지원하지 않기 때문에 이 기능은 작동하지 않습니다.

Kestrel에서 동적 포트 할당을 사용하려면 ServiceManifest.xml에서 `Endpoint` 구성을 완전히 생략하고 `KestrelCommunicationListener` 생성자에 엔드포인트 이름을 전달하지 않습니다.

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

이 구성에서 `KestrelCommunicationListener`는 애플리케이션 포트 범위에서 사용되지 않는 포트를 자동으로 선택합니다.

## <a name="service-fabric-configuration-provider"></a>서비스 패브릭 구성 공급자
읽을 구성 공급자에서 설정 하는 키-값 쌍을 기준으로 하는 ASP.NET Core에서 앱 구성 [ASP.NET Core의 구성](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/) 자세한 온 일반적인 ASP.NET Core 구성 지원 알아야 합니다.

이 섹션에서는 ASP.NET Core 구성 가져와서 통합할 서비스 패브릭 구성 공급자를 설명 합니다 `Microsoft.ServiceFabric.AspNetCore.Configuration` NuGet 패키지.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration 시작 확장
가져오기 후 `Microsoft.ServiceFabric.AspNetCore.Configuration` 하 여 ASP.NET Core 구성 API 사용 하 여 Service Fabric 구성 소스를 등록 해야 하는 NuGet 패키지 **AddServiceFabricConfiguration** 확장 `Microsoft.ServiceFabric.AspNetCore.Configuration` 네임 스페이스 에 대해 `IConfigurationBuilder`

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

이제 ASP.NET Core 서비스 다른 응용 프로그램 설정과 마찬가지로 Service Fabric 구성 설정에 액세스할 수 있습니다. 예를 들어, 강력한 형식의 개체로 설정 로드 옵션 패턴을 사용할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>기본 키 매핑
기본적으로 Service Fabric 구성 공급자는 패키지 이름, 섹션 이름 및 asp.net core 구성을 형성 하는 속성 이름을 다음 함수를 사용 하 여 키:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

예를 들어, 명명 된 구성 패키지를 있다면 `MyConfigPackage` 사용 하 여 아래 콘텐츠를 다음 구성 값에서 사용할 수 ASP.NET Core `IConfiguration` 키를 통해 *MyConfigPackage:MyConfigSection:MyParameter*
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>서비스 패브릭 구성 옵션
서비스 패브릭 구성 공급자도 지원 합니다. `ServiceFabricConfigurationOptions` 키 매핑의 기본 동작을 변경 합니다.

#### <a name="encrypted-settings"></a>암호화 설정
이 Service Fabric 구성 공급자도 지원, Service Fabric 설정 암호화를 지원 합니다. 기본 원칙을 ASP.NET core는 기본적으로 암호화 된 설정이 are't descrypted 보안 뒤에 `IConfiguration`, 암호화 된 값 대신 저장 됩니다. 그러나 ASP.NET Core IConfiguration에 저장할 값의 암호를 해독 하려는 경우을 설정할 수 있습니다 DecryptValue 플래그가 false `AddServiceFabricConfiguration` 다음과 같이 확장 합니다.

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = true); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>여러 구성 패키지
Service Fabric은 여러 구성 패키지를 지원 합니다. 기본적으로 패키지 이름은 키 구성에 포함 됩니다. 설정할 수 있습니다는 `IncludePackageName` 기본 동작을 변경 하는 플래그입니다.
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>사용자 지정 키 매핑, 값 추출 및 데이터 채우기
기본 동작을 변경 하려면 2 플래그 위에 Service Fabric 구성 공급자도 지원 외에 더 많은 고급 시나리오 사용자 지정을 통해 키 매핑 `ExtractKeyFunc` 사용자 지정을 통해 값을 추출 하 고 `ExtractValueFunc`입니다. 전체 프로세스를 통해 ASP.NET Core 구성에 Service Fabric 구성에서 데이터를 채우는 데에 변경할 수 있습니다 `ConfigAction`합니다.

사용 하려면 다음 예제는 설명 `ConfigAction` 데이터 채우기를 사용자 지정할 수 있습니다.
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```
### <a name="configuration-update"></a>구성 업데이트
서비스 패브릭 구성 공급자도 구성 업데이트를 지원 하 고 ASP.NET Core를 사용할 수 있습니다 `IOptionsMonitor` 변경 알림을 수신 하도록 아니라와 `IOptionsSnapshot` 구성 데이터를 다시 로드 합니다. 자세한 내용은 [ASP.NET Core 옵션](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/configuration/options)합니다.

이 기본적으로 지원 하 고 더 이상 코딩 하는 데 필요한 구성 업데이트를 사용 하도록 설정 합니다.

## <a name="scenarios-and-configurations"></a>시나리오 및 구성
이 섹션에서는 다음 시나리오에 대해 설명하고, 웹 서버, 포트 구성, Service Fabric 통합 옵션 및 기타 설정에 권장되는 조합을 제공하여 적절하게 작동하는 서비스를 달성합니다.
 - 외부에 노출된 ASP.NET Core 상태 비저장 서비스
 - 내부 전용 ASP.NET Core 상태 비저장 서비스
 - 내부 전용 ASP.NET Core 상태 저장 서비스

**외부에 노출된** 서비스는 일반적으로 부하 분산 장치를 통해 클러스터 외부에서 연결할 수 있는 엔드포인트를 제공하는 서비스입니다.

**내부 전용** 서비스는 엔드포인트가 클러스터 내에서만 연결할 수 있는 서비스입니다.

> [!NOTE]
> 상태 저장 서비스 엔드포인트는 일반적으로 인터넷에 노출되어서는 안됩니다. 부하 분산 장치에서 트래픽을 찾아 적절한 상태 저장 서비스 복제본으로 라우팅할 수 없기 때문에 Azure Load Balancer와 같이 Service Fabric 서비스 확인을 인식하지 못하는 부하 분산 장치 뒤에 있는 클러스터는 상태 저장 서비스를 노출할 수 없습니다. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>외부에 노출된 ASP.NET Core 상태 비저장 서비스
Kestrel은 외부 인터넷 연결 HTTP 엔드포인트를 노출하는 프런트 엔드 서비스에 권장되는 웹 서버입니다. Windows에서 HttpSys를 사용하면 HTTP 라우팅을 제공하기 위해 프런트 엔드 프록시 또는 게이트웨이를 사용할 필요 없이, 동일한 포트를 사용하고 호스트 이름 또는 경로를 통해 구분되는 동일한 노드 집합에 여러 웹 서비스를 호스트할 수 있는 포트 공유 기능을 제공할 수 있습니다.
 
인터넷에 노출되면 상태 비저장 서비스에서 부하 분산 장치를 통해 연결할 수 있는 잘 알려진 안정적인 엔드포인트를 사용해야 합니다. 이 끝점은 애플리케이션 사용자에게 제공할 URL입니다. 권장되는 구성은 다음과 같습니다.

|  |  | **참고 사항** |
| --- | --- | --- |
| 웹 서버 | Kestrel | Kestrel은 Windows와 Linux에서 모두 지원되는 기본 웹 서버입니다. |
| 포트 구성 | 정적 | 잘 알려진 정적 포트는 ServiceManifest.xml의 `Endpoints` 구성에 구성해야 합니다(예: HTTP의 경우 80, HTTPS의 경우 443). |
| ServiceFabricIntegrationOptions | 없음 | 서비스에서 들어오는 고유 식별자 요청의 유효성을 검사하지 않도록 Service Fabric 통합 미들웨어를 구성할 때 `ServiceFabricIntegrationOptions.None` 옵션을 사용해야 합니다. 애플리케이션의 외부 사용자는 미들웨어에서 사용되는 고유한 식별 정보를 알 수 없습니다. |
| 인스턴스 수 | -1 | 일반적인 사용 사례에서는 인스턴스 수 설정을 "-1"로 설정하여 부하 분산 장치에서 트래픽을 수신하는 모든 노드에서 인스턴스를 사용할 수 있도록 합니다. |

외부에 노출되는 여러 서비스가 동일한 노드 집합을 공유하는 경우 HttpSys를 고유하지만 안정된 URL 경로에 사용할 수 있습니다. 이는 IWebHost를 구성할 때 제공된 URL을 수정하여 수행할 수 있으며, 이것은 HttpSys에만 적용됩니다.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>내부 전용 ASP.NET Core 상태 비저장 서비스
클러스터 내에서만 호출되는 상태 비저장 서비스는 고유한 URL과 동적으로 할당된 포트를 사용하여 여러 서비스 간의 공동 작업을 확인해야 합니다. 권장되는 구성은 다음과 같습니다.

|  |  | **참고 사항** |
| --- | --- | --- |
| 웹 서버 | Kestrel | HttpSys는 내부 상태 비저장 서비스에 사용될 수 있지만, Kestrel은 여러 서비스 인스턴스에서 호스트를 공유할 수 있도록 권장되는 서버입니다.  |
| 포트 구성 | 동적으로 할당 | 상태 저장 서비스의 여러 복제본에서 호스트 프로세스 또는 호스트 운영 체제를 공유할 수 있으므로 고유한 포트가 필요합니다. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 동적 포트 할당으로 인해 이 설정은 앞에서 설명한 잘못된 ID 문제를 방지합니다. |
| InstanceCount | 모든 | 인스턴스 수 설정은 서비스를 작동하는 데 필요한 모든 값으로 설정할 수 있습니다. |

### <a name="internal-only-stateful-aspnet-core-service"></a>내부 전용 ASP.NET Core 상태 저장 서비스
클러스터 내에서만 호출되는 상태 저장 서비스는 동적으로 할당된 포트를 사용하여 여러 서비스 간의 공동 작업을 확인해야 합니다. 권장되는 구성은 다음과 같습니다.

|  |  | **참고 사항** |
| --- | --- | --- |
| 웹 서버 | Kestrel | `HttpSysCommunicationListener`는 복제본에서 호스트 프로세스를 공유하는 상태 저장 서비스에서 사용하도록 설계되지 않았습니다. |
| 포트 구성 | 동적으로 할당 | 상태 저장 서비스의 여러 복제본에서 호스트 프로세스 또는 호스트 운영 체제를 공유할 수 있으므로 고유한 포트가 필요합니다. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 동적 포트 할당으로 인해 이 설정은 앞에서 설명한 잘못된 ID 문제를 방지합니다. |

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 Service Fabric 애플리케이션 디버그](service-fabric-debugging-your-application.md)

<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
