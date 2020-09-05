---
title: ASP.NET Core와 서비스 통신
description: 상태 비저장 및 상태 저장 Azure Service Fabric Reliable Services 응용 프로그램에서 ASP.NET Core를 사용 하는 방법을 알아봅니다.
author: vturecek
ms.topic: conceptual
ms.date: 10/12/2018
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 69423e7545178fd74ad44f5cab7b37b6f24b3577
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022193"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>Azure Service Fabric에서 ASP.NET Core Reliable Services

ASP.NET Core는 오픈 소스 및 플랫폼 간 프레임 워크입니다. 이 프레임 워크는 웹 앱, IoT 앱 및 모바일 백 엔드와 같은 클라우드 기반의 인터넷 연결 응용 프로그램을 빌드하기 위해 설계 되었습니다.

이 문서는 **AspNetCore** 를 사용 하 여 Service Fabric Reliable Services에서 ASP.NET Core 서비스를 호스트 하는 자세한 가이드입니다. NuGet 패키지 집합입니다.

개발 환경을 설정 하는 방법에 대 한 소개 Service Fabric ASP.NET Core 자습서는 [자습서: ASP.NET Core 웹 API 프런트 엔드 서비스 및 상태 저장 백 엔드 서비스를 사용 하 여 응용 프로그램 만들기 및 배포](service-fabric-tutorial-create-dotnet-app.md)를 참조 하세요.

이 문서의 나머지 부분에서는 이미 ASP.NET Core 잘 알고 있다고 가정 합니다. 그렇지 않으면 [ASP.NET Core 기본 사항](/aspnet/core/fundamentals/index)을 참조 하세요.

## <a name="aspnet-core-in-the-service-fabric-environment"></a>Service Fabric 환경에서 ASP.NET Core

ASP.NET Core 및 Service Fabric 앱은 모두 .NET Core 또는 전체 .NET Framework에서 실행할 수 있습니다. Service Fabric에서 ASP.NET Core를 두 가지 방법으로 사용할 수 있습니다.
 - **게스트 실행 파일로 호스팅됨** - 이러한 방식은 코드를 변경 하지 않고 Service Fabric에서 기존 ASP.NET Core 응용 프로그램을 실행 하는 데 주로 사용 됩니다.
 - **신뢰할 수 있는 서비스 내에서를 실행**합니다. 이렇게 하면 Service Fabric 런타임과 보다 효율적으로 통합할 수 있으며 상태 저장 ASP.NET Core 서비스를 사용할 수 있습니다.

이 문서의 나머지 부분에서는 Service Fabric SDK와 함께 제공 되는 ASP.NET Core 통합 구성 요소를 통해 신뢰할 수 있는 서비스 내에서 ASP.NET Core를 사용 하는 방법을 설명 합니다.

## <a name="service-fabric-service-hosting"></a>Service Fabric 서비스 호스팅

Service Fabric에서 서비스의 하나 이상의 인스턴스 및/또는 복제본이 서비스 *호스트 프로세스*에서 실행 됩니다. 서비스 코드를 실행 하는 실행 파일입니다. 서비스 작성자는 서비스 호스트 프로세스를 소유 하 고 Service Fabric을 활성화 하 고 모니터링 합니다.

기존 ASP.NET(MVC 5 이하)은 System.Web.dll을 통해 IIS에 긴밀하게 결합됩니다. ASP.NET Core는 웹 서버와 웹 애플리케이션 간의 분리를 제공합니다. 이러한 분리를 통해 웹 응용 프로그램을 서로 다른 웹 서버 간에 이식할 수 있습니다. 또한 웹 서버를 *자체 호스트할*수 있습니다. 즉, IIS와 같은 전용 웹 서버 소프트웨어에서 소유한 프로세스와는 달리 웹 서버를 자체 프로세스에서 시작할 수 있습니다.

Service Fabric 서비스와 ASP.NET를 게스트 실행 파일 또는 신뢰할 수 있는 서비스로 결합 하려면 서비스 호스트 프로세스 내에서 ASP.NET를 시작할 수 있어야 합니다. ASP.NET Core 자체 호스팅을 사용하면 이 작업을 수행할 수 있습니다.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>안정적인 서비스에서 ASP.NET Core 호스팅
일반적으로 자체 호스팅되는 ASP.NET Core 애플리케이션은 `Program.cs`의 `static void Main()` 메서드와 같이 애플리케이션의 진입점에 WebHost를 만듭니다. 이 경우 WebHost의 수명 주기는 프로세스의 수명 주기에 바인딩됩니다.

![프로세스에서 ASP.NET Core 호스팅][0]

그러나 응용 프로그램 진입점은 신뢰할 수 있는 서비스에서 WebHost를 만들 수 있는 올바른 위치가 아닙니다. 응용 프로그램 진입점은 서비스 유형을 Service Fabric 런타임으로 등록 하는 데만 사용 되므로 해당 서비스 유형의 인스턴스를 만들 수 있기 때문입니다. WebHost는 신뢰할 수 있는 서비스 자체에서 만들어야 합니다. 서비스 호스트 프로세스 내에서 서비스 인스턴스 및/또는 복제본은 여러 수명 주기를 진행할 수 있습니다. 

Reliable Service 인스턴스는 `StatelessService` 또는 `StatefulService`에서 파생되는 서비스 클래스로 표현됩니다. 서비스를 위한 통신 스택은 서비스 클래스의 `ICommunicationListener` 구현에 포함되어 있습니다. `Microsoft.ServiceFabric.AspNetCore.*`NuGet 패키지에는 `ICommunicationListener` Kestrel 또는 신뢰할 수 있는 서비스의 HTTP.sys에 대 한 ASP.NET Core WebHost를 시작 하 고 관리 하는 구현이 포함 되어 있습니다.

![신뢰할 수 있는 서비스에서 ASP.NET Core 호스팅을 위한 다이어그램][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`ICommunicationListener`NuGet 패키지의 Kestrel 및 HTTP.sys에 대 한 구현은 `Microsoft.ServiceFabric.AspNetCore.*` 비슷한 사용 패턴을 사용 합니다. 하지만 각 웹 서버에 따라 약간 다른 작업을 수행 합니다. 

두 통신 수신기는 다음 인수를 사용하는 생성자를 제공합니다.
 - **`ServiceContext serviceContext`**: `ServiceContext` 실행 중인 서비스에 대 한 정보를 포함 하는 개체입니다.
 - **`string endpointName`**: `Endpoint` ServiceManifest.xml의 구성 이름입니다. 주로 두 통신 수신기가 서로 다릅니다. HTTP.sys에는 구성이 *필요* 하지만 `Endpoint` kestrel은 그렇지 않습니다.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**:를 만들고 반환 하는 사용자가 구현 하는 람다입니다 `IWebHost` . 이를 통해 `IWebHost` ASP.NET Core 응용 프로그램에서 정상적으로 작동 하는 방식을 구성할 수 있습니다. 람다는 사용 하는 Service Fabric 통합 옵션과 사용자가 제공 하는 구성에 따라 생성 되는 URL을 제공 `Endpoint` 합니다. 그런 다음 해당 URL을 수정 하거나 사용 하 여 웹 서버를 시작할 수 있습니다.

## <a name="service-fabric-integration-middleware"></a>Service Fabric 통합 미들웨어
`Microsoft.ServiceFabric.AspNetCore`NuGet 패키지에는 `UseServiceFabricIntegration` `IWebHostBuilder` Service Fabric – 인식 미들웨어를 추가 하는에 대 한 확장 메서드가 포함 되어 있습니다. 이 미들웨어는 Kestrel 또는 HTTP.sys를 구성 `ICommunicationListener` 하 여 Service Fabric Naming Service에 고유한 서비스 URL을 등록 합니다. 그런 다음 클라이언트 요청의 유효성을 검사 하 여 클라이언트가 올바른 서비스에 연결 하는지 확인 합니다. 

이 단계는 클라이언트가 잘못 된 서비스에 연결 되지 않도록 방지 하는 데 필요 합니다. 이는 Service Fabric 같은 공유 호스트 환경에서 여러 웹 응용 프로그램을 동일한 물리적 컴퓨터 또는 가상 컴퓨터에서 실행할 수 있지만 고유한 호스트 이름은 사용 하지 않기 때문입니다. 이 시나리오에 대해서는 다음 섹션에서 자세히 설명합니다.

### <a name="a-case-of-mistaken-identity"></a>잘못된 ID의 경우
프로토콜에 관계없이 서비스 복제본은 고유한 IP:포트 조합에서 수신 대기합니다. 서비스 복제본이 IP: 포트 끝점에서 수신 대기를 시작 하면 해당 끝점 주소를 Service Fabric Naming Service에 보고 합니다. 클라이언트 또는 다른 서비스에서 검색할 수 있습니다. 서비스에서 동적으로 할당 된 응용 프로그램 포트를 사용 하는 경우, 서비스 복제본은 동일한 실제 또는 가상 컴퓨터에서 이전에 다른 서비스의 동일한 IP: 포트 끝점을 우연히 사용할 수 있습니다. 이로 인해 클라이언트에서 실수로 잘못된 서비스에 연결할 수 있습니다. 이 시나리오는 다음과 같은 이벤트 시퀀스가 발생 하는 경우에 발생할 수 있습니다.

 1. 서비스 A가 HTTP를 통해 10.0.0.1:30000에서 수신 대기합니다. 
 2. 클라이언트에서 서비스 A를 확인 하 고 주소 10.0.0.1:30000을 가져옵니다.
 3. 서비스 A가 다른 노드로 이동합니다.
 4. 서비스 B가 10.0.0.1에 위치하고 우연히 동일한 30000 포트를 사용합니다.
 5. 클라이언트가 캐시된 10.0.0.1:30000 주소로 서비스 A에 연결하려고 시도합니다.
 6. 클라이언트가 잘못 된 서비스에 연결 되어 있다는 것을 인식 하지 못하는 상태에서 이제 service B에 성공적으로 연결 되었습니다.

이로 인해 진단할 수 없는 버그가 임의의 시간에 발생할 수 있습니다.

### <a name="using-unique-service-urls"></a>고유한 서비스 URL 사용
이러한 버그를 방지 하기 위해 서비스는 고유 식별자를 사용 하 여 Naming Service에 끝점을 게시 한 다음 클라이언트 요청 중에 고유 식별자의 유효성을 검사할 수 있습니다. 이 작업은 적대적이지 않은 테넌트가 신뢰할 수 있는 환경에서 서비스 간의 공동 작업이며, 악의적인 테 넌 트 환경에서 보안 서비스 인증을 제공 하지 않습니다.

신뢰할 수 있는 환경에서 메서드에 의해 추가 된 미들웨어는 `UseServiceFabricIntegration` Naming Service에 게시 된 주소에 고유 식별자를 자동으로 추가 합니다. 각 요청에서 해당 식별자의 유효성을 검사 합니다. 식별자가 일치 하지 않으면 미들웨어는 즉시 HTTP 410이 아닌 응답을 반환 합니다.

동적으로 할당된 포트를 사용하는 서비스는 이 미들웨어를 사용해야 합니다.

고정 된 고유 포트를 사용 하는 서비스는 협조적 환경에서이 문제가 발생 하지 않습니다. 고유한 고정 포트는 일반적으로 클라이언트 애플리케이션이 연결될 잘 알려진 포트가 필요한 외부 연결 서비스에 사용됩니다. 예를 들어 대부분의 인터넷 연결 웹 응용 프로그램은 웹 브라우저 연결에 포트 80 또는 443을 사용 합니다. 이 경우 고유 식별자를 사용 하도록 설정 하면 안 됩니다.

다음 다이어그램에서는 미들웨어를 사용하도록 설정된 요청 흐름을 보여 줍니다.

![Service Fabric ASP.NET Core 통합][2]

Kestrel과 HTTP.sys 구현은 모두 똑같은 `ICommunicationListener` 방식으로이 메커니즘을 사용 합니다. HTTP.sys 기본 **HTTP.sys** 포트 공유 기능을 사용 하 여 고유한 URL 경로에 따라 요청을 내부적으로 구분할 수 있지만 해당 기능은 HTTP.sys 구현에서 사용 *되지* 않습니다 `ICommunicationListener` . 앞에서 설명한 시나리오에서 HTTP 503 및 HTTP 404 오류 상태 코드가 발생 하기 때문입니다. 이렇게 하면 HTTP 503 및 HTTP 404이 일반적으로 다른 오류를 나타내는 데 사용 되므로 클라이언트에서 오류의 의도를 확인 하기가 어려워집니다. 

따라서 Kestrel과 HTTP.sys 구현은 모두 `ICommunicationListener` 확장 메서드에서 제공 하는 미들웨어를 표준화 `UseServiceFabricIntegration` 합니다. 따라서 클라이언트는 HTTP 410 응답에서 서비스 끝점 다시 확인 작업을 수행 하기만 하면 됩니다.

## <a name="httpsys-in-reliable-services"></a>Reliable Services HTTP.sys
**ServiceFabric AspNetCore** NuGet 패키지를 가져와 Reliable Services에서 HTTP.sys를 사용할 수 있습니다. 이 패키지에는 `HttpSysCommunicationListener` 의 구현인가 포함 되어 있습니다 `ICommunicationListener` . `HttpSysCommunicationListener` 를 사용 하면 HTTP.sys를 웹 서버로 사용 하 여 신뢰할 수 있는 서비스 내에 ASP.NET Core WebHost를 만들 수 있습니다.

HTTP.sys는 [WINDOWS HTTP 서버 API](/windows/win32/http/http-api-start-page)를 기반으로 합니다. 이 API는 **HTTP.sys** 커널 드라이버를 사용 하 여 HTTP 요청을 처리 하 고 웹 응용 프로그램을 실행 하는 프로세스로 라우팅합니다. 이를 통해 동일한 물리적 컴퓨터 또는 가상 컴퓨터의 여러 프로세스가 동일한 포트에서 웹 응용 프로그램을 호스팅할 수 있으며,이는 고유한 URL 경로 또는 호스트 이름으로 명확 하 게 구분 됩니다. 이러한 기능은 동일한 클러스터에서 여러 웹 사이트를 호스팅하는 Service Fabric에서 유용합니다.

>[!NOTE]
>HTTP.sys 구현은 Windows 플랫폼 에서만 작동 합니다.

다음 다이어그램은 HTTP.sys Windows에서 포트 공유에 **HTTP.sys** 커널 드라이버를 사용 하는 방법을 보여 줍니다.

![HTTP.sys 다이어그램][3]

### <a name="httpsys-in-a-stateless-service"></a>상태 비저장 서비스의 HTTP.sys
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

### <a name="httpsys-in-a-stateful-service"></a>상태 저장 서비스의 HTTP.sys

`HttpSysCommunicationListener` 는 기본 **HTTP.sys** 포트 공유 기능과 관련 된 복잡 한 상태 저장 서비스에서 사용 하도록 설계 되지 않았습니다. 자세한 내용은 HTTP.sys를 사용 하는 동적 포트 할당에 대 한 다음 섹션을 참조 하세요. 상태 저장 서비스의 경우 Kestrel이 제안 된 웹 서버입니다.

### <a name="endpoint-configuration"></a>엔드포인트 구성

`Endpoint`HTTP.sys를 포함 하 여 WINDOWS HTTP 서버 API를 사용 하는 웹 서버에 대 한 구성이 필요 합니다. Windows HTTP 서버 API를 사용 하는 웹 서버는 먼저 HTTP.sys를 사용 하 여 URL을 예약 해야 합니다 .이는 일반적으로 [netsh](/windows/win32/http/netsh-commands-for-http) 도구를 사용 하 여 수행 됩니다. 

이 작업을 수행 하려면 서비스에 기본적으로 포함 되지 않는 높은 권한이 필요 합니다. ServiceManifest.xml 구성의 속성에 대 한 "http" 또는 "https" 옵션은 `Protocol` `Endpoint` Service Fabric 런타임이 사용자를 대신해 HTTP.sys URL을 등록 하도록 지시 하는 데 특히 사용 됩니다. [*강력한 와일드 카드*](/windows/win32/http/urlprefix-strings) URL 접두사를 사용 하 여이를 수행 합니다.

예를 들어 서비스를 예약 하려면 `http://+:80` ServiceManifest.xml에서 다음 구성을 사용 합니다.

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

#### <a name="use-httpsys-with-a-static-port"></a>정적 포트를 사용 하 여 HTTP.sys 사용
HTTP.sys에서 정적 포트를 사용 하려면 구성에 포트 번호를 제공 합니다 `Endpoint` .

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>동적 포트를 사용 하 여 HTTP.sys 사용
HTTP.sys에서 동적으로 할당 된 포트를 사용 하려면 `Port` 구성에서 속성을 생략 합니다 `Endpoint` .

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

구성에 의해 할당 된 동적 포트는 `Endpoint` *호스트 프로세스 당*하나의 포트만 제공 합니다. 현재 Service Fabric 호스팅 모델을 사용 하면 여러 서비스 인스턴스 및/또는 복제본을 동일한 프로세스에서 호스팅할 수 있습니다. 즉, 구성을 통해 할당 될 때 각 항목은 동일한 포트를 공유 `Endpoint` 합니다. 여러 **HTTP.sys** 인스턴스는 기본 **HTTP.sys** 포트 공유 기능을 사용 하 여 포트를 공유할 수 있습니다. 그러나 `HttpSysCommunicationListener` 클라이언트 요청에 대 한 복잡성으로 인해에서 지원 되지 않습니다. 동적 포트 사용의 경우 Kestrel이 제안 된 웹 서버입니다.

## <a name="kestrel-in-reliable-services"></a>Reliable Services의 Kestrel
**ServiceFabric. AspNetCore** NuGet 패키지를 가져와 Reliable Services에서 kestrel을 사용할 수 있습니다. 이 패키지에는 `KestrelCommunicationListener` 의 구현인가 포함 되어 있습니다 `ICommunicationListener` . `KestrelCommunicationListener` 웹 서버로 Kestrel을 사용 하 여 신뢰할 수 있는 서비스 내에 ASP.NET Core WebHost를 만들 수 있습니다.

Kestrel은 ASP.NET Core의 플랫폼 간 웹 서버입니다. HTTP.sys와 달리 Kestrel은 중앙 집중식 끝점 관리자를 사용 하지 않습니다. 또한 HTTP.sys와 달리 Kestrel은 여러 프로세스 간의 포트 공유를 지원 하지 않습니다. Kestrel의 각 인스턴스는 고유한 포트를 사용해야 합니다. Kestrel에 대 한 자세한 내용은 [구현 세부 정보](/aspnet/core/fundamentals/servers/kestrel?view=aspnetcore-2.2)를 참조 하세요.

![Kestrel 다이어그램][4]

### <a name="kestrel-in-a-stateless-service"></a>상태 비저장 서비스의 Kestrel
상태 비저장 서비스에서 `Kestrel`를 사용하려면 `CreateServiceInstanceListeners` 메서드를 재정의하고 `KestrelCommunicationListener` 인스턴스를 반환합니다.

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

이 예제에서는 WebHost 종속성 삽입 컨테이너에 `IReliableStateManager`의 singleton 인스턴스를 제공합니다. 이는 반드시 필요한 것은 아니지만 `IReliableStateManager` MVC 컨트롤러 작업 메서드에서 및 신뢰할 수 있는 컬렉션을 사용할 수 있습니다.

상태 저장 서비스에서는 `Endpoint` 구성 이름이 `KestrelCommunicationListener`에 제공되지 *않습니다*. 자세한 내용은 다음 섹션에서 설명합니다.

### <a name="configure-kestrel-to-use-https"></a>HTTPS를 사용하도록 Kestrel 구성
서비스에서 Kestrel을 사용 하 여 HTTPS를 사용 하도록 설정 하는 경우 몇 가지 수신 대기 옵션을 설정 해야 합니다. `ServiceInstanceListener` *EndpointHttps* 끝점을 사용 하 고 특정 포트 (예: 포트 443)에서 수신 하도록를 업데이트 합니다. Kestrel 웹 서버를 사용 하도록 웹 호스트를 구성 하는 경우 모든 네트워크 인터페이스에서 IPv6 주소를 수신 하도록 Kestrel을 구성 해야 합니다. 

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

자습서의 전체 예제는 [HTTPS를 사용 하도록 Kestrel 구성](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https)을 참조 하세요.


### <a name="endpoint-configuration"></a>엔드포인트 구성
`Endpoint`Kestrel을 사용 하는 경우 구성이 필요 하지 않습니다. 

Kestrel은 간단한 독립 실행형 웹 서버입니다. HTTP.sys (또는 HttpListener)와는 달리 `Endpoint` 시작 하기 전에 URL을 등록할 필요가 없기 때문에 ServiceManifest.xml의 구성이 필요 하지 않습니다. 

#### <a name="use-kestrel-with-a-static-port"></a>정적 포트로 Kestrel 사용
Kestrel에서 사용할 ServiceManifest.xml 구성에서 정적 포트를 구성할 수 있습니다 `Endpoint` . 반드시 필요한 것은 아니지만, 다음과 같은 두 가지 잠재적인 이점을 제공 합니다.
 - 포트가 응용 프로그램 포트 범위에 속하지 않는 경우 Service Fabric 하 여 OS 방화벽을 통해 열립니다.
 - `KestrelCommunicationListener`를 통해 제공된 URL은 이 포트를 사용합니다.

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

ServiceManifest.xml 구성을 사용 하지 않는 경우 `Endpoint` 생성자에서 이름을 생략 합니다 `KestrelCommunicationListener` . 이 경우 동적 포트를 사용 합니다. 이에 대 한 자세한 내용은 다음 섹션을 참조 하세요.

#### <a name="use-kestrel-with-a-dynamic-port"></a>동적 포트로 Kestrel 사용
Kestrel은 ServiceManifest.xml의 구성에서 자동 포트 할당을 사용할 수 없습니다 `Endpoint` . 그 이유는 구성의 자동 포트 할당은 `Endpoint` *호스트 프로세스*당 고유한 포트를 할당 하 고 단일 호스트 프로세스에는 여러 kestrel 인스턴스가 포함 될 수 있기 때문입니다. 이는 포트 공유를 지원 하지 않기 때문에 Kestrel에서 작동 하지 않습니다. 따라서 각 Kestrel 인스턴스는 고유 포트에서 열어야 합니다.

Kestrel에서 동적 포트 할당을 사용 하려면 `Endpoint` ServiceManifest.xml에서 구성을 완전히 생략 하 고 다음과 같이 끝점 이름을 생성자에 전달 하지 마세요 `KestrelCommunicationListener` .

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

이 구성에서 `KestrelCommunicationListener`는 애플리케이션 포트 범위에서 사용되지 않는 포트를 자동으로 선택합니다.

HTTPS의 경우 ServiceManifest.xml에 지정 된 포트 없이 HTTPS 프로토콜을 사용 하 여 끝점을 구성 하 고 끝점 이름을 KestrelCommunicationListener 생성자에 전달 해야 합니다.


## <a name="service-fabric-configuration-provider"></a>Service Fabric 구성 공급자
ASP.NET Core의 앱 구성은 구성 공급자가 설정한 키-값 쌍을 기반으로 합니다. 일반적인 ASP.NET Core 구성 지원에 대 한 자세한 내용은 [ASP.NET Core 구성](/aspnet/core/fundamentals/configuration/) 을 참조 하세요.

이 섹션에서는 NuGet 패키지를 가져와 Service Fabric 구성 공급자를 ASP.NET Core 구성과 통합 하는 방법에 대해 설명 합니다 `Microsoft.ServiceFabric.AspNetCore.Configuration` .

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration 시작 확장
NuGet 패키지를 가져온 후에는 `Microsoft.ServiceFabric.AspNetCore.Configuration` ASP.NET Core 구성 API를 사용 하 여 Service Fabric 구성 소스를 등록 해야 합니다. 이렇게 하려면 네임 스페이스의 **AddServiceFabricConfiguration** extensions를 확인 `Microsoft.ServiceFabric.AspNetCore.Configuration` `IConfigurationBuilder` 합니다.

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

이제 ASP.NET Core 서비스에서 다른 응용 프로그램 설정과 마찬가지로 Service Fabric 구성 설정에 액세스할 수 있습니다. 예를 들어 옵션 패턴을 사용 하 여 강력한 형식의 개체로 설정을 로드할 수 있습니다.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>기본 키 매핑
기본적으로 Service Fabric 구성 공급자에는 패키지 이름, 섹션 이름 및 속성 이름이 포함 됩니다. 이와 함께 다음과 같이 ASP.NET Core 구성 키를 형성 합니다.
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

예를 들어 다음 콘텐츠를 사용 하 여 라는 구성 패키지가 있는 경우 `MyConfigPackage` `IConfiguration` *myconfigpackage: Myconfigpackage: MyParameter*을 통해 ASP.NET Core에서 구성 값을 사용할 수 있습니다.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Service Fabric 구성 옵션
또한 Service Fabric 구성 공급자는 `ServiceFabricConfigurationOptions` 키 매핑의 기본 동작을 변경 하는 것을 지원 합니다.

#### <a name="encrypted-settings"></a>암호화 된 설정
Service Fabric는 Service Fabric 구성 공급자와 마찬가지로 암호화 된 설정을 지원 합니다. 암호화 된 설정은 기본적으로 ASP.NET Core로 해독 되지 않습니다 `IConfiguration` . 대신 암호화 된 값이 저장 됩니다. 그러나 ASP.NET Core IConfiguration에 저장할 값을 해독 하려면 다음과 같이 확장에서 *DecryptValue* 플래그를 false로 설정 합니다 `AddServiceFabricConfiguration` .

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>여러 구성 패키지
Service Fabric는 여러 구성 패키지를 지원 합니다. 기본적으로 패키지 이름은 구성 키에 포함 되어 있습니다. 그러나 다음과 `IncludePackageName` 같이 플래그를 false로 설정할 수 있습니다.
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
또한 Service Fabric 구성 공급자는를 사용 하 여 키 매핑을 사용자 지정 하 고를 사용 하 여 `ExtractKeyFunc` 값을 사용자 지정 추출할 수 있는 고급 시나리오를 지원 합니다 `ExtractValueFunc` . 을 사용 하 여 Service Fabric 구성에서 ASP.NET Core 구성으로 데이터를 채우는 전체 프로세스를 변경할 수도 있습니다 `ConfigAction` .

다음 예에서는를 사용 하 여 `ConfigAction` 데이터 채우기를 사용자 지정 하는 방법을 보여 줍니다.
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

### <a name="configuration-updates"></a>구성 업데이트
Service Fabric 구성 공급자는 구성 업데이트도 지원 합니다. ASP.NET Core를 사용 하 여 `IOptionsMonitor` 변경 알림을 받은 다음를 사용 하 여 `IOptionsSnapshot` 구성 데이터를 다시 로드할 수 있습니다. 자세한 내용은 [ASP.NET Core 옵션](/aspnet/core/fundamentals/configuration/options)을 참조 하세요.

이러한 옵션은 기본적으로 지원 됩니다. 구성 업데이트를 사용 하도록 설정 하려면 추가 코딩이 필요 하지 않습니다.

## <a name="scenarios-and-configurations"></a>시나리오 및 구성
이 섹션에서는 다음과 같은 시나리오를 해결 하는 데 권장 되는 웹 서버, 포트 구성, Service Fabric 통합 옵션 및 기타 설정의 조합을 제공 합니다.
 - 외부에 노출된 ASP.NET Core 상태 비저장 서비스
 - 내부 전용 ASP.NET Core 상태 비저장 서비스
 - 내부 전용 ASP.NET Core 상태 저장 서비스

**외부에 노출** 되는 서비스는 일반적으로 부하 분산 장치를 통해 클러스터 외부에서 호출 되는 끝점을 노출 하는 서비스입니다.

**내부 전용** 서비스는 해당 끝점이 클러스터 내 에서만 호출 되는 서비스입니다.

> [!NOTE]
> 상태 저장 서비스 끝점은 일반적으로 인터넷에 노출 되지 않아야 합니다. Azure Load Balancer와 같이 Service Fabric 서비스 확인을 인식 하지 않는 부하 분산 장치 뒤의 클러스터는 상태 저장 서비스를 노출할 수 없습니다. 이는 부하 분산 장치에서 트래픽을 찾아 적절 한 상태 저장 서비스 복제본으로 라우팅할 수 없기 때문입니다. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>외부에 노출된 ASP.NET Core 상태 비저장 서비스
Kestrel은 외부 인터넷 연결 HTTP 끝점을 노출 하는 프런트 엔드 서비스에 권장 되는 웹 서버입니다. Windows HTTP.sys에서는 포트 공유 기능을 제공할 수 있습니다 .이 기능을 사용 하면 동일한 포트를 사용 하 여 동일한 노드 집합에서 여러 웹 서비스를 호스트할 수 있습니다. 이 시나리오에서 웹 서비스는 HTTP 라우팅을 제공 하기 위해 프런트 엔드 프록시 또는 게이트웨이를 사용 하지 않고 호스트 이름 또는 경로로 구분 됩니다.
 
상태 비저장 서비스는 인터넷에 노출 되는 경우 부하 분산 장치를 통해 연결할 수 있는 잘 알려진 안정적인 끝점을 사용 해야 합니다. 응용 프로그램의 사용자에 게이 URL을 제공 합니다. 다음 구성이 권장됩니다.

| Type | 권장 | 메모 |
| ---- | -------------- | ----- |
| 웹 서버 | Kestrel | Windows 및 Linux에서 지원 되므로 kestrel은 기본 웹 서버입니다. |
| 포트 구성 | static | 잘 알려진 정적 포트는 ServiceManifest.xml의 `Endpoints` 구성에 구성해야 합니다(예: HTTP의 경우 80, HTTPS의 경우 443). |
| ServiceFabricIntegrationOptions | 없음 | `ServiceFabricIntegrationOptions.None`서비스에서 고유 식별자에 대 한 들어오는 요청의 유효성을 검사 하지 않도록 Service Fabric 통합 미들웨어를 구성할 때 옵션을 사용 합니다. 응용 프로그램의 외부 사용자는 미들웨어에서 사용 하는 고유한 식별 정보를 알지 못합니다. |
| 인스턴스 수 | -1 | 일반적인 사용 사례에서 인스턴스 수 설정은 *-1*로 설정 해야 합니다. 이 작업을 수행 하면 부하 분산 장치에서 트래픽을 수신 하는 모든 노드에서 인스턴스를 사용할 수 있습니다. |

외부에 노출 된 여러 서비스가 동일한 노드 집합을 공유 하는 경우 고유 하지만 안정적인 URL 경로를 사용 하 여 HTTP.sys를 사용할 수 있습니다. IWebHost를 구성할 때 제공 된 URL을 수정 하 여이를 수행할 수 있습니다. 이는 HTTP.sys에만 적용 됩니다.

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
클러스터 내에서만 호출되는 상태 비저장 서비스는 고유한 URL과 동적으로 할당된 포트를 사용하여 여러 서비스 간의 공동 작업을 확인해야 합니다. 다음 구성이 권장됩니다.

| Type | 권장 | 메모 |
| ---- | -------------- | ----- |
| 웹 서버 | Kestrel | 내부 상태 비저장 서비스에 HTTP.sys를 사용할 수 있지만, Kestrel은 여러 서비스 인스턴스에서 호스트를 공유할 수 있도록 하는 최상의 서버입니다.  |
| 포트 구성 | 동적으로 할당 | 상태 저장 서비스의 여러 복제본이 호스트 프로세스 또는 호스트 운영 체제를 공유할 수 있으므로 고유한 포트가 필요 합니다. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 동적 포트 할당으로 인해 이 설정은 앞에서 설명한 잘못된 ID 문제를 방지합니다. |
| InstanceCount | any | 인스턴스 수 설정은 서비스를 작동하는 데 필요한 모든 값으로 설정할 수 있습니다. |

### <a name="internal-only-stateful-aspnet-core-service"></a>내부 전용 ASP.NET Core 상태 저장 서비스
클러스터 내에서만 호출되는 상태 저장 서비스는 동적으로 할당된 포트를 사용하여 여러 서비스 간의 공동 작업을 확인해야 합니다. 다음 구성이 권장됩니다.

| Type | 권장 | 메모 |
| ---- | -------------- | ----- |
| 웹 서버 | Kestrel | 는 `HttpSysCommunicationListener` 복제본이 호스트 프로세스를 공유 하는 상태 저장 서비스에서 사용 하도록 설계 되지 않았습니다. |
| 포트 구성 | 동적으로 할당 | 상태 저장 서비스의 여러 복제본이 호스트 프로세스 또는 호스트 운영 체제를 공유할 수 있으므로 고유한 포트가 필요 합니다. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | 동적 포트 할당으로 인해 이 설정은 앞에서 설명한 잘못된 ID 문제를 방지합니다. |

## <a name="next-steps"></a>다음 단계
[Visual Studio를 사용하여 Service Fabric 애플리케이션 디버그](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
