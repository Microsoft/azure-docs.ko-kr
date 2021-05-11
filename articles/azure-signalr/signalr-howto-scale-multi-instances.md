---
title: 여러 인스턴스를 사용하여 스케일링 - Azure SignalR Service
description: 많은 스케일링 시나리오에서 고객은 대규모 배포를 만들기 위해 여러 인스턴스를 프로비저닝하고 함께 사용되도록 구성해야 하는 경우가 많습니다. 예를 들어 분할을 수행하려면 여러 인스턴스가 지원되어야 합니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: fd6ac8c4d4fc4c3fec4f549f8ef4f955e2b1c637
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "89439217"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>여러 인스턴스를 사용하여 SignalR Service를 스케일링하는 방법
최신 SignalR Service SDK는 SignalR Service 인스턴스에 대해 여러 엔드포인트를 지원합니다. 이 기능을 동시 연결 스케일링이나 지역 간 메시징에 사용할 수 있습니다.

## <a name="for-aspnet-core"></a>ASP.NET Core의 경우

### <a name="how-to-add-multiple-endpoints-from-config"></a>구성에서 여러 엔드포인트를 추가하는 방법

SignalR Service 연결 문자열에 `Azure:SignalR:ConnectionString` 또는 `Azure:SignalR:ConnectionString:` 키를 사용하여 구성합니다.

`Azure:SignalR:ConnectionString:`으로 시작하는 키는 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` 형식이어야 합니다. 여기서 `Name` 및 `EndpointType`은 `ServiceEndpoint` 개체의 속성이며 코드에서 액세스할 수 있습니다.

다음 `dotnet` 명령을 사용하여 여러 인스턴스 연결 문자열을 추가할 수 있습니다.

```cmd
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>코드에서 여러 엔드포인트를 추가하는 방법

`ServicEndpoint` 클래스는 Azure SignalR Service 엔드포인트의 속성을 설명하기 위해 도입되었습니다.
다음을 통해 Azure SignalR Service SDK를 사용하는 경우 여러 인스턴스 엔드포인트를 구성할 수 있습니다.
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

### <a name="how-to-customize-endpoint-router"></a>엔드포인트 라우터를 사용자 지정하는 방법

기본적으로 SDK는 [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs)를 사용하여 엔드포인트를 선택합니다.

#### <a name="default-behavior"></a>기본 동작 
1. 클라이언트 요청 라우팅

    클라이언트가 앱 서버와 `/negotiate`를 수행하는 경우입니다. 기본적으로 SDK는 사용 가능한 서비스 엔드포인트 집합에서 엔드포인트 하나를 **임의로 선택** 합니다.

2. 서버 메시지 라우팅

    ‘메시지를 특정 **연결**로 전송’하고 대상 연결이 현재 서버로 라우팅되는 경우 메시지가 연결된 엔드포인트로 직접 이동합니다. 그렇지 않으면 메시지가 모든 Azure SignalR 엔드포인트에 브로드캐스트됩니다.

#### <a name="customize-routing-algorithm"></a>라우팅 알고리즘 사용자 지정
메시지가 이동해야 하는 엔드포인트를 식별하는 특별한 지식이 있는 경우 사용자 고유의 라우터를 만들 수 있습니다.

예를 들어 `east-`로 시작하는 그룹이 항상 `east`라는 엔드포인트로 이동하는 경우 사용자 지정 라우터는 아래와 같이 정의됩니다.

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

엔드포인트를 선택하기 위해 기본 negotiate 동작을 재정의하는 아래의 또 다른 예제는 앱 서버가 있는 위치에 따라 달라집니다.

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

다음을 사용하여 DI 컨테이너에 라우터를 등록해야 합니다.

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

### <a name="how-to-add-multiple-endpoints-from-config"></a>구성에서 여러 엔드포인트를 추가하는 방법

SignalR Service 연결 문자열에 `Azure:SignalR:ConnectionString` 또는 `Azure:SignalR:ConnectionString:` 키를 사용하여 구성합니다.

`Azure:SignalR:ConnectionString:`으로 시작하는 키는 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}` 형식이어야 합니다. 여기서 `Name` 및 `EndpointType`은 `ServiceEndpoint` 개체의 속성이며 코드에서 액세스할 수 있습니다.

`web.config`에 여러 인스턴스 연결 문자열을 추가할 수 있습니다.

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>코드에서 여러 엔드포인트를 추가하는 방법

`ServicEndpoint` 클래스는 Azure SignalR Service 엔드포인트의 속성을 설명하기 위해 도입되었습니다.
다음을 통해 Azure SignalR Service SDK를 사용하는 경우 여러 인스턴스 엔드포인트를 구성할 수 있습니다.

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

### <a name="how-to-customize-router"></a>라우터를 사용자 지정하는 방법

ASP.NET SignalR과 ASP.NET Core SignalR의 유일한 차이점은 `GetNegotiateEndpoint`의 http 컨텍스트 형식입니다. ASP.NET SignalR의 경우 [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) 형식입니다.

ASP.NET SignalR의 사용자 지정 협상 예제는 다음과 같습니다.

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

다음을 사용하여 DI 컨테이너에 라우터를 등록해야 합니다.

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

`ServiceEndpoint` 개체에는 값이 `primary` 또는 `secondary`인 `EndpointType` 속성이 있습니다.

`primary` 엔드포인트는 클라이언트 트래픽을 수신하기 위한 기본 엔드포인트로, 보다 안정적인 네트워크 연결을 사용하는 것으로 간주됩니다. `secondary` 엔드포인트는 덜 안정적인 네트워크 연결을 사용하는 것으로 간주되며 클라이언트에서 서버로 이동하는 트래픽이 아니라 서버에서 클라이언트로 이동하는 트래픽 처리(예: 메시지 브로드캐스팅)에만 사용됩니다

지역 간 시나리오의 경우 네트워크가 불안정할 수 있습니다. ‘미국 동부’에 있는 앱 서버의 경우 동일한 ‘미국 동부’ 지역에 있는 SignalR Service 엔드포인트를 `primary`로 구성하고 다른 지역의 엔드포인트를 `secondary`로 표시할 수 있습니다.  이 구성에서 다른 지역의 서비스 엔드포인트는 ‘미국 동부’ 앱 서버로부터 메시지를 **수신** 할 수 있지만 해당 앱 서버로 라우팅되는 **지역 간** 클라이언트는 없습니다. 아래 다이어그램에는 아키텍처가 나와 있습니다.

![지역 간 인프라](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

클라이언트가 기본 라우터를 사용하여 앱 서버와의 `/negotiate`를 시도하는 경우 SDK는 사용 가능한 `primary` 엔드포인트 집합에서 엔드포인트 하나를 **임의로 선택** 합니다. 기본 엔드포인트를 사용할 수 없는 경우 SDK는 사용 가능한 모든 `secondary` 엔드포인트에서 **임의로 선택** 합니다. 서버와 서비스 엔드포인트 간의 연결이 활성 상태이면 엔드포인트가 **사용 가능** 으로 표시됩니다.

지역 간 시나리오에서 클라이언트가 ‘미국 동부’에서 호스트된 앱 서버와 `/negotiate`를 시도하는 경우 기본적으로 항상 동일한 지역에 있는 `primary` 엔드포인트가 반환됩니다. ‘미국 동부’ 엔드포인트를 모두 사용할 수 없는 경우 클라이언트가 다른 지역의 엔드포인트로 리디렉션됩니다. 아래 장애 조치(failover) 섹션에서 시나리오를 자세히 설명합니다.

![정상적인 협상](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>장애 조치(failover)

`primary` 엔드포인트를 모두 사용할 수 없는 경우 클라이언트의 `/negotiate`가 사용 가능한 `secondary` 엔드포인트에서 선택합니다. 이 장애 조치(failover) 메커니즘을 사용하려면 각 엔드포인트가 하나 이상의 앱 서버에 대한 `primary` 엔드포인트 역할을 해야 합니다.

![장애 조치(failover)](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 동일한 애플리케이션에서 스케일링, 분할, 지역 간 시나리오를 위한 여러 인스턴스를 구성하는 방법을 배웠습니다.

고가용성 및 재해 복구 시나리오에서도 여러 엔드포인트 지원을 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [재해 복구 및 고가용성을 위한 SignalR Service 설정](./signalr-concept-disaster-recovery.md)
