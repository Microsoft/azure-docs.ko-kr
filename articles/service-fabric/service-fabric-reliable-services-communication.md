---
title: Reliable Services 통신 개요
description: 서비스에서 수신기 열기, 엔드포인트 확인 및 서비스 간 통신을 비롯한 Reliable Services 통신 모델의 개요입니다.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 9b45ceaed9f0d3d64a0fc6890549542acc6b1c21
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018640"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Reliable Services 통신 API를 사용하는 방법
플랫폼인 Azure 서비스 패브릭은 서비스 간에 이루어지는 통신을 전혀 알 수 없습니다. UDP에서 HTTP까지 모든 프로토콜 및 스택이 허용됩니다. 서비스 개발자가 서비스가 통신하는 방법을 선택합니다. Reliable Services 애플리케이션 프레임워크는 사용자 지정 통신 구성 요소를 빌드하는 데 사용할 수 있는 API 뿐만 아니라 기본 제공 통신 스택을 제공합니다.

## <a name="set-up-service-communication"></a>서비스 통신 설정
Reliable Services API는 서비스 통신을 위해 간단한 인터페이스를 사용합니다. 서비스에 대한 엔드포인트를 열려면 다음 인터페이스를 구현하기만 하면 됩니다.

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

그런 다음 서비스 기본 클래스 메서드 재정의에서 통신 수신기를 반환하여 통신 수신기 구현을 추가합니다.

상태 비저장 서비스의 경우:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

상태 저장 서비스의 경우:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

두 경우 모두 수신기의 컬렉션을 반환합니다. 이렇게 하면 서비스가 여러 수신기를 사용하여 서로 다른 프로토콜을 잠재적으로 사용하는 다수의 엔드포인트에서 수신할 수 있게 됩니다. 예를 들어 HTTP 수신기 및 별도의 WebSocket 수신기가 있을 수 있습니다. 각 수신기는 이름을 가져오며 *이름 : 주소* 쌍의 결과 콜렉션은 클라이언트가 서비스 인스턴스 또는 파티션에 대한 수신 주소를 요청하는 경우 JSON 개체로 표현됩니다.

상태 비저장 서비스에서는 재정의가 ServiceInstanceListeners의 컬렉션을 반환합니다. `ServiceInstanceListener`에는 `ICommunicationListener(C#) / CommunicationListener(Java)`를 만들고 이름을 부여하는 함수가 포함되어 있습니다. 상태 저장 서비스에서는 재정의가 ServiceReplicaListeners의 컬렉션을 반환합니다. 이는 `ServiceReplicaListener`에 보조 복제본에서 `ICommunicationListener`를 여는 옵션이 있기 때문에 해당 상태 비저장에 상응하는 것과는 약간 다릅니다. 서비스에서 여러 통신 수신기를 사용할 수 있을 뿐만 아니라 보조 복제본에서 요청을 수락하는 수신기와 주 복제본에서만 수신하는 수신기를 지정할 수도 있습니다.

예를 들어 주 복제본에서는 RPC만 호출하는 ServiceRemotingListener와 HTTP를 통해 보조 복제본에서는 읽기 요청을 수행하는 두 번째 사용자 지정 수신기가 있을 수 있습니다.

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> 서비스에 대한 여러 수신기를 만들 때 각 수신기에 고유한 이름을 지정 **해야** 합니다.
>
>

마지막으로 엔드포인트의 섹션에 있는 [서비스 매니페스트](service-fabric-application-and-service-manifests.md) 에서 서비스에 필요한 엔드포인트를 설명합니다.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

통신 수신기는 `ServiceContext`의 `CodePackageActivationContext`에서 할당된 엔드포인트 리소스에 액세스할 수 있습니다. 그런 다음 수신기가 열릴 때 요청을 수신하기 시작할 수 있습니다.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> 엔드포인트 리소스는 전체 서비스 패키지에 공통되며 서비스 패키지가 활성화될 때 서비스 패브릭에서 할당합니다. 동일한 ServiceHost에서 호스팅되는 여러 서비스 복제본은 동일한 포트를 공유할 수 있습니다. 이는 통신 수신기가 포트 공유를 지원해야 한다는 의미입니다. 이에 대한 권장 방법은 통신 수신기가 수신 주소를 생성할 때 파티션 ID 및 복제본/인스턴스 ID를 사용하도록 하는 것입니다.
>
>

### <a name="service-address-registration"></a>서비스 주소 등록
*명명 서비스* 라는 시스템 서비스는 서비스 패브릭 클러스터에서 실행됩니다. 명명 서비스는 서비스의 각 인스턴스 또는 복제본이 수신 중인 서비스 및 해당 주소에 등록됩니다. `ICommunicationListener(C#) / CommunicationListener(Java)`의 `OpenAsync(C#) / openAsync(Java)` 메서드가 완료되면 해당 반환 값은 명명 서비스에 등록됩니다. 명명 서비스에 게시되는 반환 값은 어떤 값도 가능한 문자열입니다. 이 문자열 값은 클라이언트가 명명 서비스에서 서비스에 대한 주소를 요청할 때 표시되는 값입니다.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Service Fabric은 클라이언트 및 기타 서비스가 서비스 이름별로 이 주소를 요청할 수 있게 해주는 API를 제공합니다. 서비스 주소가 정적이 아니기 때문에 중요합니다. 서비스는 리소스 균형 조정 및 가용성을 위한 클러스터 주변으로 이동됩니다. 이는 클라이언트가 서비스의 수신 대기 주소를 확인할 수 있도록 하는 메커니즘입니다.

> [!NOTE]
> C#의 경우는 [OWIN 자체 호스팅이 포함된 Service Fabric 웹 API 서비스](./service-fabric-reliable-services-communication-aspnetcore.md)에서 통신 수신기를 작성하는 방법에 대한 전체 연습을 참조합니다. Java의 경우는 HTTP 서버 구현을 직접 작성할 수 있습니다. https://github.com/Azure-Samples/service-fabric-java-getting-started에서 EchoServer 애플리케이션 예제를 참조합니다.
>
>

## <a name="communicating-with-a-service"></a>서비스와 통신
Reliable Services API는 서비스와 통신하는 클라이언트를 작성하도록 다음 라이브러리를 제공합니다.

### <a name="service-endpoint-resolution"></a>서비스 엔드포인트 확인
서비스와 통신하는 첫 번째 단계는 통신하려는 서비스의 파티션 또는 인스턴스의 엔드포인트 주소를 확인하는 것입니다. ph x="1" /&gt; 유틸리티 클래스는 클라이언트가 런타임 시 서비스의 엔드포인트를 확인할 수 있게 도와주는 기본 항목입니다. 서비스 패브릭 용어로 서비스의 엔드포인트를 결정하는 프로세스를 *서비스 엔드포인트 확인*이라고 합니다.

클러스터 내의 서비스에 연결하려면 기본 설정을 사용하여 ServicePartitionResolver를 만들 수 있습니다. 다음은 대부분의 상황에 대한 권장 사용법입니다.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

다른 클러스터의 서비스에 연결하면 일련의 클러스터 게이트웨이 엔드포인트로 ServicePartitionResolver를 만들 수 있습니다. 게이트웨이 엔드포인트는 동일한 클러스터에 연결하기 위한 다른 엔드포인트입니다. 예를 들어:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

또는 `ServicePartitionResolver`은 `FabricClient`를 만들기 위한 함수를 지정하여 내부적으로 사용할 수 있습니다.

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient` 은 서비스 패브릭 클러스터의 다양한 관리 작업에서 클러스터와 통신하는 데 사용되는 개체입니다. 서비스 파티션 확인자가 클러스터가 상호 작용하는 방법을 더 제어하려는 경우에 유용합니다. `FabricClient`은 내부적으로 캐싱을 수행하며 만드는 데 일반적으로 비용이 많이 드므로 `FabricClient` 인스턴스를 최대한 많이 다시 사용하는 것이 중요합니다.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

그런 다음 확인 메서드는 분할된 서비스에 대한 서비스의 주소 또는 서비스 파티션을 검색하는 데 사용됩니다.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

서비스 주소는 ServicePartitionResolver를 사용하여 쉽게 확인할 수 있지만 확인된 주소를 올바르게 사용할 수 있는지 확인하려면 더 많은 작업이 필요합니다. 클라이언트는 연결 시도가 일시적 오류(예: 서비스 이동 또는 일시적으로 사용 불가함) 또는 영구 오류(예: 서비스가 삭제되었거나 요청된 리소스가 더 이상 존재하지 않음) 중 어떤 것 때문에 실패했는지를 감지하고 다시 시도할 수 있습니다. 서비스 인스턴스 또는 복제본은 여러 가지 이유로 인해 언제든지 노드에서 노드로 이동할 수 있습니다. ServicePartitionResolver를 통해 확인된 서비스 주소는 클라이언트 코드가 연결하려고 시도한 시점에 기한이 경과될 수 있습니다. 이 경우에 클라이언트가 주소를 다시 확인해야 합니다. 이전 `ResolvedServicePartition` 을 제공하면 해결 프로그램이 캐시된 주소를 간단히 검색하는 대신 다시 시도해야 함을 나타냅니다.

일반적으로 클라이언트 코드는 ServicePartitionResolver와 직접 연동할 필요가 없습니다. 이 코드가 생성되면 Reliable Services API의 통신 클라이언트 팩터리에 전달됩니다. 팩터리는 내부적으로 해결 프로그램을 사용하여 서비스와 통신하는 데 사용할 수 있는 클라이언트 개체를 생성합니다.

### <a name="communication-clients-and-factories"></a>통신 클라이언트 및 팩터리
통신 팩터리 라이브러리는 확인된 서비스 엔드포인트에 대한 연결을 다시 시도하도록 하는 일반적인 오류 처리 재시도 패턴을 구현합니다. 오류 처리기를 제공하는 동안 팩터리 라이브러리는 재시도 메커니즘을 제공합니다.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)`은 Service Fabric 서비스와 통신할 수 있는 클라이언트를 생성하는 통신 클라이언트 팩터리에서 구현되는 기본 인터페이스를 정의합니다. CommunicationClientFactory의 구현은 클라이언트가 통신하려고 하는 서비스 패브릭 서비스에서 사용하는 통신 스택에 따라 달라집니다. Reliable Services API는 `CommunicationClientFactoryBase<TCommunicationClient>`를 제공합니다. 이는 CommunicationClientFactory 인터페이스의 기본 구현을 제공하고 모든 통신 스택에 공통된 작업을 수행합니다. 이러한 작업은 ServicePartitionResolver를 사용하여 서비스 엔드포인트를 확인하는 것을 포함합니다. 클라이언트는 일반적으로 추상 CommunicationClientFactoryBase 클래스를 구현하여 통신 스택에 특정된 논리를 처리합니다.

통신 클라이언트는 주소를 수신하고 서비스에 연결하는 데 사용합니다. 클라이언트는 원하는 모든 프로토콜을 사용할 수 있습니다.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

클라이언트 팩터리는 주로 통신 클라이언트를 만드는 작업을 담당합니다. HTTP 클라이언트와 같은 영구 연결을 유지하지 않는 클라이언트의 경우 팩터리는 클라이언트를 만들고 반환해야 합니다. 또한 일부 이진 프로토콜과 같은 영구 연결을 유지하는 다른 프로토콜은 팩터리를 통해 유효성을 검사하여 연결을 다시 만들어야 하는지 여부를 결정해야 합니다.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

마지막으로 예외 처리기는 예외가 발생할 때 수행할 동작을 결정하는 작업을 담당합니다. 예외는 **다시 시도 가능** 및 **다시 시도 불가능**으로 분류됩니다.

* **다시 시도 불가능** 예외는 단순히 다시 호출자로 throw됩니다.
* **다시 시도 가능** 예외는 **일시적** 및 **영구** 예외로 더 세분화됩니다.
  * **일시적** 예외는 서비스 엔드포인트 주소를 다시 확인하지 않고 다시 시도할 수 있는 예외입니다. 일시적인 네트워크 문제 또는 서비스 엔드포인트 주소가 존재하지 않음을 나타내는 것 이외의 서비스 오류 응답을 포함합니다.
  * **영구** 예외는 다시 확인할 서비스 엔드포인트 주소를 필요로 하는 예외입니다. 이는 서비스 엔드포인트에 도달하지 못했음을 나타내는 예외를 포함하며 이는 서비스가 다른 노드로 이동되었음을 나타냅니다.

`TryHandleException` 은 주어진 예외에 대한 결정을 내립니다. 예외에 대해 어떤 결정을 내릴지 **모르는** 경우 **false**를 반환해야 합니다. 어떤 결정을 내릴지 **아는** 경우 결과를 적절하게 설정하여 **true**를 반환해야 합니다.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>모든 항목 요약
통신 프로토콜을 기반으로 구축한 `ICommunicationClient(C#) / CommunicationClient(Java)`, `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` 및 `IExceptionHandler(C#) / ExceptionHandler(Java)`으로 `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)`는 모두를 함께 래핑하고 이러한 구성 요소에 대한 오류 처리 및 서비스 파티션 주소 확인 루프를 제공합니다.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>다음 단계
* [ASP.NET Core와 Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Reliable Services 원격을 사용하여 원격 프로시저 호출](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services를 사용한 WCF 통신](service-fabric-reliable-services-communication-wcf.md)
