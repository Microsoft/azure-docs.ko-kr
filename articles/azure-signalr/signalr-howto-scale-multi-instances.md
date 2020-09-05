---
title: 여러 인스턴스를 사용 하 여 크기 조정-Azure SignalR Service
description: 많은 크기 조정 시나리오에서 고객은 여러 인스턴스를 프로 비전 하 고이를 함께 사용 하 여 대규모 배포를 만들도록 구성 해야 하는 경우가 많습니다. 예를 들어 분할에는 여러 인스턴스가 지원 되어야 합니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: ecf4a35fc239a70e87550a97e71d7abd3d00ecfa
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88921990"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>여러 인스턴스를 사용 하 여 SignalR 서비스를 확장 하는 방법
최신 SignalR 서비스 SDK는 SignalR 서비스 인스턴스에 대 한 여러 끝점을 지원 합니다. 이 기능을 사용 하 여 동시 연결의 크기를 조정 하거나 지역 간 메시징에 사용할 수 있습니다.

## <a name="for-aspnet-core"></a>ASP.NET Core

### <a name="how-to-add-multiple-endpoints-from-config"></a>구성에서 여러 끝점을 추가 하는 방법

Key `Azure:SignalR:ConnectionString` 또는 `Azure:SignalR:ConnectionString:` SignalR 서비스 연결 문자열의 구성입니다.

키가로 시작 하는 경우 `Azure:SignalR:ConnectionString:` 형식 이어야 합니다 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` `Name` . 여기서 및 `EndpointType` 는 개체의 속성 `ServiceEndpoint` 이며 코드에서 액세스할 수 있습니다.

다음 명령을 사용 하 여 여러 인스턴스 연결 문자열을 추가할 수 있습니다 `dotnet` .

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>코드에서 여러 끝점을 추가 하는 방법

`ServicEndpoint`Azure SignalR Service 끝점의 속성을 설명 하는 클래스가 도입 되었습니다.
다음을 통해 Azure SignalR Service SDK를 사용 하는 경우 여러 인스턴스 끝점을 구성할 수 있습니다.
```cs
services.AddSignalR()
        .AddAzureSignalR(options => 
        {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString0>"),
                new ServiceEndpoint("<ConnectionString1>", type: EndpointType.Primary, name: "east-region-a"),
                new ServiceEndpoint("<ConnectionString2>", type: EndpointType.Primary, name: "east-region-b"),
                new ServiceEndpoint("<ConnectionString3>", type: EndpointType.Secondary, name: "backup"),
            };
        });
```

### <a name="how-to-customize-endpoint-router"></a>끝점 라우터를 사용자 지정 하는 방법

기본적으로 SDK는 [Defaultendpointrouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) 를 사용 하 여 끝점을 선택 합니다.

#### <a name="default-behavior"></a>기본 동작 
1. 클라이언트 요청 라우팅

    `/negotiate`응용 프로그램 서버를 사용 하는 클라이언트입니다. 기본적으로 SDK는 사용 가능한 서비스 끝점 집합에서 끝점 하나를 **임의로 선택** 합니다.

2. 서버 메시지 라우팅

    * * * 연결을 특정 * * * 연결로 보내고 대상 연결이 현재 서버로 라우트되는 경우 메시지가 해당 연결 된 끝점으로 직접 이동 합니다. 그렇지 않으면 모든 Azure SignalR 끝점에 메시지가 브로드캐스트 됩니다.

#### <a name="customize-routing-algorithm"></a>라우팅 알고리즘 사용자 지정
메시지가 이동 해야 하는 끝점을 식별 하는 데 특별 한 지식이 있는 경우 고유한 라우터를 만들 수 있습니다.

로 시작 하는 그룹이 `east-` 항상 이라는 끝점으로 이동 하는 경우 사용자 지정 라우터는 아래와 같이 정의 됩니다 `east` .

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override IEnumerable<ServiceEndpoint> GetEndpointsForGroup(string groupName, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the group broadcast behavior, if the group name starts with "east-", only send messages to endpoints inside east
        if (groupName.StartsWith("east-"))
        {
            return endpoints.Where(e => e.Name.StartsWith("east-"));
        }

        return base.GetEndpointsForGroup(groupName, endpoints);
    }
}
```

기본 negotiate 동작을 재정의 하는 아래의 또 다른 예는 응용 프로그램 서버가 있는 위치에 따라 끝점을 선택 하는 것입니다.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(HttpContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (endpointName.Count == 0)
        {
            context.Response.StatusCode = 400;
            var response = Encoding.UTF8.GetBytes("Invalid request");
            context.Response.Body.Write(response, 0, response.Length);
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

다음을 사용 하 여 DI 컨테이너에 라우터를 등록 해야 합니다.

```cs
services.AddSingleton(typeof(IEndpointRouter), typeof(CustomRouter));
services.AddSignalR()
        .AddAzureSignalR(
            options => 
            {
                options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
            });
```

## <a name="for-aspnet"></a>ASP.NET의 경우

### <a name="how-to-add-multiple-endpoints-from-config"></a>구성에서 여러 끝점을 추가 하는 방법

Key `Azure:SignalR:ConnectionString` 또는 `Azure:SignalR:ConnectionString:` SignalR 서비스 연결 문자열의 구성입니다.

키가로 시작 하는 경우 `Azure:SignalR:ConnectionString:` 형식 이어야 합니다 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` `Name` . 여기서 및 `EndpointType` 는 개체의 속성 `ServiceEndpoint` 이며 코드에서 액세스할 수 있습니다.

여러 인스턴스 연결 문자열을 다음과 같이 추가할 수 있습니다 `web.config` .

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <connectionStrings>
    <add name="Azure:SignalR:ConnectionString" connectionString="<ConnectionString1>"/>
    <add name="Azure:SignalR:ConnectionString:en-us" connectionString="<ConnectionString2>"/>
    <add name="Azure:SignalR:ConnectionString:zh-cn:secondary" connectionString="<ConnectionString3>"/>
    <add name="Azure:SignalR:ConnectionString:Backup:secondary" connectionString="<ConnectionString4>"/>
  </connectionStrings>
  ...
</configuration>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>코드에서 여러 끝점을 추가 하는 방법

`ServicEndpoint`Azure SignalR Service 끝점의 속성을 설명 하는 클래스가 도입 되었습니다.
다음을 통해 Azure SignalR Service SDK를 사용 하는 경우 여러 인스턴스 끝점을 구성할 수 있습니다.

```cs
app.MapAzureSignalR(
    this.GetType().FullName, 
    options => {
            options.Endpoints = new ServiceEndpoint[]
            {
                // Note: this is just a demonstration of how to set options.Endpoints
                // Having ConnectionStrings explicitly set inside the code is not encouraged
                // You can fetch it from a safe place such as Azure KeyVault
                new ServiceEndpoint("<ConnectionString1>"),
                new ServiceEndpoint("<ConnectionString2>"),
                new ServiceEndpoint("<ConnectionString3>"),
            }
        });
```

### <a name="how-to-customize-router"></a>라우터를 사용자 지정 하는 방법

ASP.NET SignalR와 ASP.NET Core SignalR의 유일한 차이점은에 대 한 http 컨텍스트 형식입니다 `GetNegotiateEndpoint` . ASP.NET SignalR의 경우 [Iowincontext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) 형식입니다.

ASP.NET SignalR에 대 한 사용자 지정 협상 예제는 다음과 같습니다.

```cs
private class CustomRouter : EndpointRouterDecorator
{
    public override ServiceEndpoint GetNegotiateEndpoint(IOwinContext context, IEnumerable<ServiceEndpoint> endpoints)
    {
        // Override the negotiate behavior to get the endpoint from query string
        var endpointName = context.Request.Query["endpoint"];
        if (string.IsNullOrEmpty(endpointName))
        {
            context.Response.StatusCode = 400;
            context.Response.Write("Invalid request.");
            return null;
        }

        return endpoints.FirstOrDefault(s => s.Name == endpointName && s.Online) // Get the endpoint with name matching the incoming request
               ?? base.GetNegotiateEndpoint(context, endpoints); // Or fallback to the default behavior to randomly select one from primary endpoints, or fallback to secondary when no primary ones are online
    }
}
```

다음을 사용 하 여 DI 컨테이너에 라우터를 등록 해야 합니다.

```cs
var hub = new HubConfiguration();
var router = new CustomRouter();
hub.Resolver.Register(typeof(IEndpointRouter), () => router);
app.MapAzureSignalR(GetType().FullName, hub, options => {
    options.Endpoints = new ServiceEndpoint[]
                {
                    new ServiceEndpoint(name: "east", connectionString: "<connectionString1>"),
                    new ServiceEndpoint(name: "west", connectionString: "<connectionString2>"),
                    new ServiceEndpoint("<connectionString3>")
                };
});
```

## <a name="configuration-in-cross-region-scenarios"></a>지역 간 시나리오의 구성

`ServiceEndpoint`개체에 값이 `EndpointType` 또는 인 속성이 `primary` 있습니다 `secondary` .

`primary` 끝점은 클라이언트 트래픽을 수신 하기 위한 기본 끝점으로, 보다 안정적인 네트워크 연결을 포함 하는 것으로 간주 됩니다. `secondary` 끝점은 신뢰할 수 없는 네트워크 연결을 사용 하는 것으로 간주 되며, 클라이언트에서 서버로의 트래픽을 수행 하는 것이 아니라 메시지 브로드캐스팅을 사용 하는 등의 방법으로 서버 간 트래픽을 사용 하는 데만 사용 됩니다

지역 간 경우 네트워크가 불안정 해질 수 있습니다. *미국 동부*에 있는 하나의 앱 서버에 대해 동일한 *미국 동부* 지역에 있는 SignalR 서비스 끝점은로 `primary` 표시 된 다른 지역의 끝점으로 구성할 수 있습니다 `secondary` . 이 구성에서 다른 지역의 서비스 끝점은 *미국 동부* 앱 서버 로부터 메시지를 **받을** 수 있지만이 앱 서버로 라우팅되는 **지역 간** 클라이언트는 없습니다. 아키텍처는 아래 다이어그램에 나와 있습니다.

![지역 간 인프라](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

클라이언트가 앱 서버를 사용 하 여 기본 라우터를 사용 하는 경우 `/negotiate` SDK는 사용 가능한 끝점 집합에서 끝점 하나를 **임의로 선택** `primary` 합니다. 기본 끝점을 사용할 수 없는 경우 SDK는 사용 가능한 모든 끝점에서 **임의로 선택** `secondary` 합니다. 서버와 서비스 끝점 간의 연결이 활성 상태 이면 끝점은 **사용 가능한** 것으로 표시 됩니다.

지역 간 시나리오에서 클라이언트는 `/negotiate` *미국 동부*에 호스트 된 앱 서버를 사용 하 여 시도 하는 경우 기본적으로 항상 `primary` 동일한 지역에 있는 끝점을 반환 합니다. *미국 동부* 끝점을 모두 사용할 수 없는 경우 클라이언트는 다른 지역의 끝점으로 리디렉션됩니다. 아래 장애 조치 (failover) 섹션에서는 시나리오에 대해 자세히 설명 합니다.

![일반 Negotiate](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>장애 조치 (failover)

모든 `primary` 끝점을 사용할 수 없는 경우 클라이언트는 `/negotiate` 사용 가능한 끝점에서 선택 `secondary` 합니다. 이 장애 조치 (failover) 메커니즘을 사용 하려면 각 끝점이 `primary` 하나 이상의 앱 서버에 대 한 끝점으로 사용 되어야 합니다.

![장애 조치 (failover)](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 크기 조정, 분할 및 지역 간 시나리오에 대해 동일한 응용 프로그램에서 여러 인스턴스를 구성 하는 방법에 대해 알아보았습니다.

다중 끝점은 고가용성 및 재해 복구 시나리오에도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [재해 복구 및 고가용성을 위한 SignalR 서비스 설정](./signalr-concept-disaster-recovery.md)
