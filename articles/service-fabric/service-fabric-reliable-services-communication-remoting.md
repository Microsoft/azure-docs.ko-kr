---
title: Service Fabric에서 C#을 사용하여 서비스 원격 호출 | Microsoft Docs
description: Service Fabric 원격 호출을 사용하면 클라이언트와 서비스가 원격 프로시저 호출을 사용하여 C# 서비스와 통신할 수 있습니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: BharatNarasimman
ms.assetid: abfaf430-fea0-4974-afba-cfc9f9f2354b
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 09/20/2017
ms.author: vturecek
ms.openlocfilehash: f9cd6e2fee738d2d42c790b4eb7b9a876a44b01d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60772978"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Reliable Services로 C#에서 서비스 원격 호출

> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-services-communication-remoting.md)
> * [Linux에서 Java](service-fabric-reliable-services-communication-remoting-java.md)
>
>

특정한 통신 프로토콜 또는 스택에 얽매여 있지 않는 서비스(예: Web API, Windows Communication Foundation 등)의 경우, Reliable Services 프레임워크가 원격 메커니즘을 제공하여 서비스에 대한 원격 프로시저 호출을 신속하고 간편하게 설정합니다. 이 문서에서는 C#을 사용하여 서비스에 대한 원격 프로시저 호출을 설정하는 방법을 설명합니다.

## <a name="set-up-remoting-on-a-service"></a>서비스에 원격 호출 설정

다음과 같은 간단한 두 가지 단계로 서비스에 대한 원격 호출을 설정할 수 있습니다.

1. 서비스로 구현할 인터페이스를 만듭니다. 이 인터페이스는 서비스의 원격 프로시저 호출에 사용할 수 있는 메서드를 정의합니다. 메서드는 작업을 반환하는 비동기 메서드여야 합니다. 인터페이스는 서비스에 원격 호출 인터페이스가 있다는 것을 신호하기 위해 `Microsoft.ServiceFabric.Services.Remoting.IService` 를 구현해야 합니다.
2. 서비스에서 원격 수신기를 사용합니다. 원격 수신기는 원격 호출 기능을 제공하는 `ICommunicationListener` 구현입니다. `Microsoft.ServiceFabric.Services.Remoting.Runtime` 네임스페이스는 상태 비저장 및 상태 저장 서비스에 대해 확장 메서드인 `CreateServiceRemotingListener`를 포함하며 이러한 상태 비저장 및 상태 저장 서비스는 기본 원격 전송 프로토콜을 사용하여 원격 수신기를 만드는 데 사용할 수 있습니다.

>[!NOTE]
>`Remoting` 네임스페이스는 `Microsoft.ServiceFabric.Services.Remoting`이라는 별도의 NuGet 패키지로 제공됩니다.

예를 들어 다음의 상태 비저장 서비스는 단일 메서드를 노출하여 원격 프로시저 호출에 "Hello World"를 가져옵니다.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> 서비스 인터페이스의 인수 및 반환 형식은 간단한 형식, 복합 형식 또는 사용자 지정 형식이 가능하지만 .NET [DataContractSerializer](https://msdn.microsoft.com/library/ms731923.aspx)에 의한 직렬화가 가능해야 합니다.
>
>

## <a name="call-remote-service-methods"></a>원격 서비스 메서드 호출

원격 스택을 사용하여 서비스에서 메서드를 호출하는 작업은 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 클래스를 통해 서비스에 대한 로컬 프록시를 사용하여 수행됩니다. `ServiceProxy` 메서드는 서비스가 구현하는 것과 동일한 인터페이스를 사용하여 로컬 프록시를 만듭니다. 이 프록시를 통해 원격으로 인터페이스의 메서드를 호출할 수 있습니다.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

원격 호출 프레임워크는 서비스가 throw한 예외를 클라이언트에 전파합니다. 그 결과 `ServiceProxy`를 사용하는 경우, 클라이언트는 서비스가 throw한 예외 처리를 담당합니다.

## <a name="service-proxy-lifetime"></a>서비스 프록시 수명

서비스 프록시 만들기는 가벼운 작업이므로 원하는 만큼 만들 수 있습니다. 서비스 프록시 인스턴스는 필요하다면 다시 사용할 수 있습니다. 원격 프로시저 호출에서 예외가 발생하는 경우 동일한 프록시 인스턴스를 계속 다시 사용할 수 있습니다. 각 서비스 프록시는 유선으로 메시지를 보내는 데 사용되는 통신 클라이언트를 포함합니다. 원격 호출을 수행하는 동안 통신 클라이언트가 유효한지 결정하기 위한 내부 확인을 수행합니다. 이러한 검사 결과에 따라, 필요한 경우 통신 클라이언트를 다시 만듭니다. 따라서 예외가 발생하더라도 `ServiceProxy`를 다시 만들 필요가 없습니다.

### <a name="service-proxy-factory-lifetime"></a>서비스 프록시 팩터리 수명

[ServiceProxyFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory)는 다른 원격 인터페이스를 위한 프록시 인스턴스를 만드는 팩터리입니다. 프록시를 만들기 위해 API `ServiceProxyFactory.CreateServiceProxy`를 사용하는 경우 프레임워크는 싱글톤 서비스 프록시를 만듭니다.
[IServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) 속성을 재정의해야 하는 경우 수동으로 만드는 것이 유용합니다.
팩터리 생성은 비용이 많이 드는 작업입니다. 서비스 프록시 팩터리는 통신 클라이언트의 내부 캐시를 유지 관리합니다.
서비스 프록시 팩터리를 가능한 한 오랫동안 캐시하는 것이 가장 좋습니다.

## <a name="remoting-exception-handling"></a>원격 예외 처리

서비스 API에 의해 throw되는 모든 원격 예외는 AggregateException으로 클라이언트에 다시 전송됩니다. 원격 예외는 DataContract에서 직렬화할 수 있어야 합니다. 그렇지 않으면 프록시 API가 serialization 오류가 있는 [ServiceException](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.serviceexception)을 throw합니다.

서비스 프록시는 만들어진 서비스 파티션에 대한 모든 장애 조치(failover) 예외를 처리합니다. 장애 조치 예외(영구적인 예외)가 있는 경우 엔드포인트를 다시 확인하고 올바른 엔드포인트로 호출을 다시 시도합니다. 장애 조치(failover) 예외에 대한 재시도 횟수는 무한합니다.
일시적 예외가 발생할 경우 프록시는 다시 호출을 시도합니다.

기본 재시도 매개 변수는 [OperationRetrySettings](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings)에서 제공됩니다.

사용자는 OperationRetrySettings 개체를 ServiceProxyFactory 생성자에 전달하여 이러한 값을 구성할 수 있습니다.

## <a name="use-the-remoting-v2-stack"></a>Remoting V2 스택 사용

버전 2.8의 NuGet Remoting 패키지를 기준으로 Remoting V2 스택을 사용할 수 있는 옵션이 있습니다. Remoting V2 스택은 더 나은 성능을 제공합니다. 또한 사용자 지정 serialization 및 플러그형 API와 같은 기능도 제공합니다.
템플릿 코드는 계속 Remoting V1 스택을 사용합니다.
Remoting V2는 V1(이전 Remoting 스택)과 호환되지 않습니다. 서비스 가용성에 미치는 영향을 피하려면 [V1에서 V2로 업그레이드](#upgrade-from-remoting-v1-to-remoting-v2) 문서의 지침을 따르세요.

다음 접근 방식은 V2 스택을 활성화하는 데 사용할 수 있습니다.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>어셈블리 특성을 사용하여 V2 스택 사용

이러한 단계는 어셈블리 특성을 사용하여 V2 스택을 사용하도록 템플릿 코드를 변경합니다.

1. 서비스 매니페스트의 `"ServiceEndpoint"`에서 `"ServiceEndpointV2"`로 엔드포인트 리소스를 변경합니다.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners` 확장 메서드를 사용하여 원격 수신기를 만듭니다(V1 및 V2에 모두 동일).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. `FabricTransportServiceRemotingProvider` 특성으로 원격 인터페이스를 포함하는 어셈블리를 표시합니다.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

클라이언트 프로젝트에는 변경이 필요하지 않습니다.
이전에 표시된 어셈블리 특성이 사용되었는지 확인하기 위해 인터페이스 어셈블리를 사용하여 클라이언트 어셈블리를 빌드합니다.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>명시적 V2 클래스를 사용하여 V2 스택 사용

V2 스택은 어셈블리 특성을 사용하는 대신 명시적 V2 클래스를 사용하여 활성화할 수도 있습니다.

이러한 단계에서는 명시적 V2 클래스를 사용하여 V2 스택을 사용하도록 템플릿 코드를 변경합니다.

1. 서비스 매니페스트의 `"ServiceEndpoint"`에서 `"ServiceEndpointV2"`로 엔드포인트 리소스를 변경합니다.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` 네임스페이스에서 [FabricTransportServiceRemotingListener](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotingListener?view=azure-dotnet)를 사용합니다.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` 네임스페이스에서 [FabricTransportServiceRemotingClientFactory](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)를 사용하여 클라이언트를 만듭니다.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Remoting V1에서 Remoting V2로 업그레이드

V1에서 V2로 업그레이드하려면 2단계 업그레이드가 필요합니다. 이 순서대로 단계를 따르세요.

1. 이 특성을 사용하여 V1 서비스를 V2 서비스로 업그레이드합니다.
이렇게 변경하면 서비스가 V1 및 V2 수신기에서 수신하는지 확인합니다.

    a. 서비스 매니페스트에서 엔드포인트 리소스를 “ServiceEndpointV2”라는 이름으로 추가합니다.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. 다음 확장 메서드를 사용하여 원격 수신기를 만듭니다.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    다. V1 및 V2 수신기와 V2 클라이언트를 사용하려면 원격 인터페이스에 어셈블리 특성을 추가합니다.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. V2 클라이언트 특성을 사용하여 V1 클라이언트를 V2 클라이언트로 업그레이드합니다.
이 단계에서는 클라이언트가 V2 스택을 사용하는지 확인합니다.
클라이언트 프로젝트/서비스에 어떠한 변경도 수행할 필요가 없습니다. 업데이트된 인터페이스 어셈블리로 클라이언트 프로젝트를 빌드하는 것으로 충분합니다.

3. 이 단계는 선택 사항입니다. V2 수신기 특성을 사용하여 V2 서비스를 업그레이드합니다.
이 단계는 서비스가 V2 수신기에서만 수신 중인지 확인합니다.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Remoting V2(인터페이스 호환 가능) 스택 사용

 Remoting V2(인터페이스 호환 가능, V2_1이라고 함) 스택에는 V2 Remoting 스택의 모든 기능이 포함되어 있습니다. 해당 인터페이스 스택은 Remoting V1 스택과 호환되지만 이전 버전의 V2 및 V1과는 호환되지 않습니다. 서비스 가용성에 영향을 주지 않고 V1에서 V2_1로 업그레이드하려면 V1에서 V2(인터페이스 호환 가능)로 업그레이드 문서의 단계를 따르세요.


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>어셈블리 특성을 사용하여 Remoting V2(인터페이스 호환 가능) 스택 사용

V2_1 스택으로 변경하려면 다음 단계를 따르세요.

1. 서비스 매니페스트에서 Endpoint Resource를 “ServiceEndpointV2_1”이라는 이름으로 추가합니다.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. 원격 확장 메서드를 사용하여 원격 수신기를 만듭니다.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. 원격 인터페이스에 [어셈블리 특성](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet)을 추가합니다.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

클라이언트 프로젝트에는 변경이 필요하지 않습니다.
이전 어셈블리 특성이 사용 중인지 확인하기 위해 인터페이스 어셈블리를 사용하여 클라이언트 어셈블리를 빌드합니다.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>명시적 원격 클래스를 사용하여 V2(인터페이스 호환 가능) 버전에 대한 수신기/클라이언트 팩터리 만들기

다음 단계를 수행하세요.

1. 서비스 매니페스트에서 Endpoint Resource를 “ServiceEndpointV2_1”이라는 이름으로 추가합니다.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. [Remoting V2 수신기](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet)를 사용합니다. 사용된 기본 서비스 엔드포인트 리소스 이름은 “ServiceEndpointV2_1”이며 서비스 매니페스트에서 정의해야 합니다.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. V2 [클라이언트 팩터리](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)를 사용합니다.
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Remoting V1에서 Remoting V2(인터페이스 호환 가능)로 업그레이드

V1에서 V2(인터페이스 호환 가능, V2_1이라고 함)로 업그레이드하려면 2단계 업그레이드가 필요합니다. 이 순서대로 단계를 따르세요.

1. 다음 특성을 사용하여 V1 서비스를 V2_1 서비스로 업그레이드합니다.
이렇게 변경하면 서비스가 V1 및 V2_1 수신기에서 수신 중인지 확인합니다.

    a. 서비스 매니페스트에서 Endpoint Resource를 “ServiceEndpointV2_1”이라는 이름으로 추가합니다.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. 다음 확장 메서드를 사용하여 원격 수신기를 만듭니다.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    다. V1 및 V2_1 수신기와 V2_1 클라이언트를 사용하기 위해 원격 인터페이스에 어셈블리 특성을 추가합니다.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. V2_1 클라이언트 특성을 사용하여 V1 클라이언트를 V2_1 클라이언트로 업그레이드합니다.
이 단계에서는 클라이언트가 V2_1 스택을 사용하고 있는지 확인합니다.
클라이언트 프로젝트/서비스에 어떠한 변경도 수행할 필요가 없습니다. 업데이트된 인터페이스 어셈블리로 클라이언트 프로젝트를 빌드하는 것으로 충분합니다.

3. 이 단계는 선택 사항입니다. 특성에서 V1 수신기 버전을 제거한 다음, V2 서비스를 업그레이드합니다.
이 단계는 서비스가 V2 수신기에서만 수신 중인지 확인합니다.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>원격 래핑된 메시지로 사용자 지정 serialization 사용

원격 래핑된 메시지에서 모든 매개 변수를 필드로 사용하여 래핑된 단일 개체를 만듭니다.
다음 단계를 수행하세요.

1. 사용자 지정 serialization에 대한 구현을 제공하기 위해 `IServiceRemotingMessageSerializationProvider` 인터페이스를 구현합니다.
    이 코드 조각은 구현 모습을 보여줍니다.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. 원격 수신기에 대해 `JsonSerializationProvider`로 기본 serialization 공급자를 재정의합니다.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. 원격 클라이언트 팩터리에 대해 `JsonSerializationProvider`로 기본 serialization 공급자를 재정의합니다.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>다음 단계

* [Reliable Services에서 OWIN을 사용하는 Web API](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services와의 Windows Communication Foundation 통신](service-fabric-reliable-services-communication-wcf.md)
* [Reliable Services에 대한 보안 통신](service-fabric-reliable-services-secure-communication.md)
