---
title: Azure SignalR Service에 대 한 여러 인스턴스를 사용 하 여 확장 하는 방법
description: 대부분의 크기 조정 시나리오에서 고객 종종 해야 여러 인스턴스를 프로 비전 하 고 대규모 배포를 만드는 데 함께 사용 하도록 구성 합니다. 예를 들어 분할 여러 인스턴스를 지원 해야 합니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: zhshang
ms.openlocfilehash: e284a0492774e02cab79db6d9006c1718a7fcfc9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60809164"
---
# <a name="how-to-scale-signalr-service-with-multiple-instances"></a>여러 인스턴스를 사용 하 여 SignalR Service를 확장 하는 방법
최신 SignalR Service SDK는 SignalR Service 인스턴스에 대 한 여러 끝점을 지원합니다. 동시 연결을 확장 하려면이 기능을 사용 하거나 지역 간 메시징에 사용할 수 있습니다.

## <a name="for-aspnet-core"></a>ASP.NET Core에 대 한

### <a name="how-to-add-multiple-endpoints-from-config"></a>구성에서 여러 끝점을 추가 하려면 어떻게 하나요?

키를 사용 하 여 Config `Azure:SignalR:ConnectionString` 또는 `Azure:SignalR:ConnectionString:` SignalR Service 연결 문자열에 대 한 합니다.

키를 사용 하 여 시작 하는 경우 `Azure:SignalR:ConnectionString:`, 형식 이어야 합니다 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`여기서 `Name` 및 `EndpointType` 의 속성을 `ServiceEndpoint` 개체 및 코드에서 액세스할 수 합니다.

다음을 사용 하 여 여러 인스턴스의 연결 문자열을 추가할 수 있습니다 `dotnet` 명령:

```batch
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-a <ConnectionString1>
dotnet user-secrets set Azure:SignalR:ConnectionString:east-region-b:primary <ConnectionString2>
dotnet user-secrets set Azure:SignalR:ConnectionString:backup:secondary <ConnectionString3>
```

### <a name="how-to-add-multiple-endpoints-from-code"></a>코드에서 여러 끝점을 추가 하려면 어떻게 하나요?

`ServicEndpoint` 클래스 Azure SignalR Service 끝점의 속성을 설명 하기 위해 도입 되었습니다.
통해 Azure SignalR Service SDK를 사용 하는 경우 여러 인스턴스 끝점을 구성할 수 있습니다.
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

### <a name="how-to-customize-endpoint-router"></a>라우터 끝점 사용자 지정 하는 방법

SDK는 기본적으로 다음을 사용 합니다.는 [DefaultEndpointRouter](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/EndpointRouters/DefaultEndpointRouter.cs) 끝점 선택 하도록 합니다.

#### <a name="default-behavior"></a>기본 동작 
1. 클라이언트 요청 라우팅

    때 클라이언트 `/negotiate` 앱 서버를 사용 하 여 합니다. 기본적으로 SDK **임의로 선택** 사용 가능한 서비스 끝점의 집합에서 하나의 끝점입니다.

2. 서버 메시지 라우팅

    경우 * 특정 메시지를 보내기 * * 연결 * * *, 및 대상 연결을 현재 서버로 라우팅됩니다, 메시지는 연결 된 끝점으로 직접 이동 합니다. 그렇지 않은 경우 모든 Azure SignalR 끝점에 메시지 브로드캐스트 됩니다.

#### <a name="customize-routing-algorithm"></a>라우팅 알고리즘 사용자 지정
메시지를 이동 해야 하는 끝점을 식별 하려면 특별 한 지식이 있는 경우 사용자 고유의 라우터를 만들 수 있습니다.

예를 들어 사용자 지정 라우터 아래 정의 되어 때로 시작 하는 그룹 `east-` 명명 된 끝점으로 이동 하는 항상 `east`:

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

동작을 협상 하는 기본값을 재정의 하는 아래 또 다른 예로, 앱 서버 있는 위치에 따라 달라 집니다 끝점을 선택 합니다.

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

라우터를 사용 하 여 DI 컨테이너를 등록 하려면 두는 것을 잊지 마세요.

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

## <a name="for-aspnet"></a>ASP.NET에 대 한

### <a name="how-to-add-multiple-endpoints-from-config"></a>구성에서 여러 끝점을 추가 하려면 어떻게 하나요?

키를 사용 하 여 Config `Azure:SignalR:ConnectionString` 또는 `Azure:SignalR:ConnectionString:` SignalR Service 연결 문자열에 대 한 합니다.

키를 사용 하 여 시작 하는 경우 `Azure:SignalR:ConnectionString:`, 형식 이어야 합니다 `Azure:SignalR:ConnectionString:{Name}:{EndpointType}`여기서 `Name` 및 `EndpointType` 의 속성을 `ServiceEndpoint` 개체 및 코드에서 액세스할 수 합니다.

여러 개의 인스턴스 연결 문자열을 추가할 수 있습니다 `web.config`:

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

### <a name="how-to-add-multiple-endpoints-from-code"></a>코드에서 여러 끝점을 추가 하려면 어떻게 하나요?

`ServicEndpoint` 클래스 Azure SignalR Service 끝점의 속성을 설명 하기 위해 도입 되었습니다.
통해 Azure SignalR Service SDK를 사용 하는 경우 여러 인스턴스 끝점을 구성할 수 있습니다.

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

ASP.NET SignalR 및 ASP.NET Core SignalR 간의 유일한 차이점은 http 컨텍스트 형식에 대 한 `GetNegotiateEndpoint`합니다. ASP.NET SignalR에 대 한 것입니다 [IOwinContext](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR.AspNet/EndpointRouters/DefaultEndpointRouter.cs#L19) 형식입니다.

사용자 지정 ASP.NET SignalR에 대 한 예제를 협상 다음과 같습니다.

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

라우터를 사용 하 여 DI 컨테이너를 등록 하려면 두는 것을 잊지 마세요.

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

## <a name="configuration-in-cross-region-scenarios"></a>지역 간 시나리오에서 구성

합니다 `ServiceEndpoint` 개체에는 `EndpointType` 속성 값을 사용 하 여 `primary` 또는 `secondary`합니다.

`primary` 끝점은 클라이언트 트래픽을 수신할 기본 끝점 및 더 안정적인 네트워크 연결; 할 것으로 간주 됩니다. `secondary` 끝점 덜 안정적인 네트워크 연결을 할 것으로 간주 됩니다 및 서버 트래픽 고려 클라이언트가 아닌에 메시지 브로드캐스트 클라이언트 트래픽을, 예를 들어, 관련 서버에만 사용 됩니다.

지역 간의 경우에서 네트워크 안정적인 될 수 있습니다. 하나의 앱에 있는 서버에 대 한 *미국 동부*, SignalR 서비스 끝점이 있는 동일한 *미국 동부* 영역으로 구성할 수 있습니다 `primary` 다른 지역의 끝점으로 표시 및 `secondary`합니다. 이 구성에서는 다른 지역에 서비스 끝점 수 **받을** 에서이 메시지 *미국 동부* 응용 프로그램 서버, 있지만 없습니다 **지역 간** 클라이언트에 게 회람 이 응용 프로그램 서버. 아키텍처는 아래 다이어그램에 표시 됩니다.

![교차 지역 인프라](./media/signalr-howto-scale-multi-instances/cross_geo_infra.png)

에서는 클라이언트가 `/negotiate` 기본 라우터는 SDK 사용 하 여 앱 서버와 **임의로 선택** 의 사용 가능한 집합에서 하나의 끝점 `primary` 끝점입니다. 끝점을 사용할 수 있으면 다음 SDK **임의로 선택** 에서 사용 가능한 모든 `secondary` 끝점입니다. 끝점으로 표시 됩니다 **사용 가능한** 경우 서버 및 서비스 끝점 간의 연결이 활성 상태입니다.

지역 간 시나리오에서 클라이언트를 열려고 하면 `/negotiate` 에 호스트 된 응용 프로그램 서버와 *미국 동부*, 기본이 항상 반환을 `primary` 동일한 지역에 있는 끝점입니다. 경우 모든 *미국 동부* 끝점을 사용할 수 있는 클라이언트가 다른 지역의 끝점으로 리디렉션됩니다. 장애 조치 섹션 아래에 시나리오를를 자세히 설명합니다.

![보통 협상](./media/signalr-howto-scale-multi-instances/normal_negotiate.png)

## <a name="fail-over"></a>장애 조치

경우 모든 `primary` 끝점을 사용할 수 없는 클라이언트의 `/negotiate` 에서 사용 가능한 선택 `secondary` 끝점입니다. 이 장애 조치 메커니즘에서는 각 끝점 역할을 해야 `primary` 하나 이상의 앱 서버에는 끝점입니다.

![장애 조치](./media/signalr-howto-scale-multi-instances/failover_negotiate.png)

## <a name="next-steps"></a>다음 단계

이 가이드에서는 크기 조정, 분할 및 지역 간 시나리오에 대 한 동일한 응용 프로그램의 여러 인스턴스를 구성 하는 방법에 대해 알아보았습니다.

여러 끝점에서 지 원하는 고가용성 및 재해 복구 시나리오에도 사용할 수 있습니다.

> [!div class="nextstepaction"]
> [재해 복구 및 고가용성 용 SignalR Service 설정](./signalr-concept-disaster-recovery.md)
