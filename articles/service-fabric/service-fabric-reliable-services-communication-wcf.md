---
title: Reliable Services WCF 통신 스택 | Microsoft Docs
description: 서비스 패브릭의 기본 제공 WCF 통신 스택은 Reliable Services를 위한 클라이언트-서비스 WCF 통신을 제공합니다.
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 75516e1e-ee57-4bc7-95fe-71ec42d452b2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/07/2017
ms.author: bharatn
ms.openlocfilehash: ae8a0ab0382083ebfca0834d2238403668efa71d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60725604"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Reliable Services에 대한 WCF 기반 통신 스택
Reliable Services 프레임워크를 사용하면 서비스 작성자가 서비스에 사용하려는 통신 스택을 결정할 수 있습니다. 선택한 통신 스택을 **CreateServiceReplicaListeners 또는 CreateServiceInstanceListeners** 메서드에서 반환되는 [ICommunicationListener](service-fabric-reliable-services-communication.md) 를 통해 플러그인할 수 있습니다. 이 프레임워크는 WCF 기반 통신을 사용하려는 서비스 작성자를 위한 WCF(Windows Communication Foundation) 기반 통신 스택 구현을 제공합니다.

## <a name="wcf-communication-listener"></a>WCF 통신 수신기
**ICommunicationListener**의 WCF 관련 구현은 **Microsoft.ServiceFabric.Services.Communication.Wcf.Runtime.WcfCommunicationListener** 클래스에서 제공됩니다.

서비스 계약의 형식이 있다고 가정합니다 `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

다음과 같은 방식으로 서비스에서 WCF 통신 수신기를 만들 수 있습니다.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>WCF 통신 스택에 대한 클라이언트 작성
WCF를 사용하여 서비스와 통신하는 클라이언트를 작성하는 경우 이 프레임워크에서는 **ClientCommunicationFactoryBase**의 WCF 특정 구현인 [WcfClientCommunicationFactory](service-fabric-reliable-services-communication.md)를 제공합니다.

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

WCF 통신 채널은 **WcfCommunicationClientFactory**에서 만든 **WcfCommunicationClient**에서 액세스될 수 있습니다.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

클라이언트 코드는 **ServicePartitionClient**를 구현하는 **WcfCommunicationClient**와 함께 **WcfCommunicationClientFactory**를 사용하여 서비스 엔드포인트를 결정하고 서비스와 통신할 수 있습니다.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> 기본 ServicePartitionResolver는 클라이언트가 서비스와 동일한 클러스터에서 실행되고 있다고 가정합니다. 그렇지 않은 경우 ServicePartitionResolver 개체를 만들고 클러스터 연결 엔드포인트에 전달합니다.
> 
> 

## <a name="next-steps"></a>다음 단계
* [Reliable Services 원격을 사용하여 원격 프로시저 호출](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services에서 OWIN을 사용하는 Web API](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services에 대한 통신 보안 유지](service-fabric-reliable-services-secure-communication-wcf.md)

