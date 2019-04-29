---
title: Azure Service Fabric에서 C#을 통한 서비스 원격 통신 보호 | Microsoft Docs
description: Azure Service Fabric 클러스터에서 실행되는 C# Reliable Services에서 서비스 원격 기반 통신을 보호하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: suchiagicha
manager: chackdan
editor: vturecek
ms.assetid: fc129c1a-fbe4-4339-83ae-0e69a41654e0
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 04/20/2017
ms.author: suchiagicha
ms.openlocfilehash: f247142f26490e1899256917b64fbec7308fb281
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107673"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>C# 서비스에서 서비스 원격 통신 보호
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-services-secure-communication.md)
> * [Linux에서 Java](service-fabric-reliable-services-secure-communication-java.md)
>
>

통신의 가장 중요한 측면 중 하나는 보안입니다. Reliable Services 애플리케이션 프레임워크는 보안을 향상시키는 데 사용할 수 있는 미리 빌드된 통신 스택 및 도구 몇 가지를 제공합니다. 이 문서에서는 C# 서비스에서 서비스 원격을 사용하는 경우 보안을 향상시키는 방법에 대해 설명합니다. C#으로 작성된 Reliable Services에 대한 원격 기능을 설정하는 방법에 대해 설명하는 기존 [예제](service-fabric-reliable-services-communication-remoting.md)를 기반으로 빌드됩니다. 

C# 서비스에서 서비스 원격 기능을 사용하는 경우 서비스를 보호하려면 다음 단계를 따르세요.

1. 서비스의 원격 프로시저 호출에 사용할 수 있는 메서드를 정의하는 인터페이스 `IHelloWorldStateful`을 만듭니다. 서비스는 `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` 네임스페이스에 선언되는 `FabricTransportServiceRemotingListener`를 사용합니다. 이것은 원격 호출 기능을 제공하는 `ICommunicationListener` 구현입니다.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. 수신기 설정 및 보안 자격 증명을 추가합니다.

    서비스 통신을 보호하는 데 사용할 인증서는 클러스터의 모든 노드에 설치해야 합니다. 
    
    > [!NOTE]
    > Linux 노드에서 인증서는 PEM 형식 파일로 */var/lib/sfcerts* 디렉터리에 있어야 합니다. 자세히 알아보려면 [Linux 노드에서 X.509 인증서의 위치 및 형식](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes)을 참조하세요. 

    두 가지 방법으로 수신기 설정 및 보안 자격 증명을 제공할 수 있습니다.

   1. 서비스 코드에서 직접 제공:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. [config 패키지](service-fabric-application-and-service-manifests.md)를 사용하여 제공:

       settings.xml 파일에서 명명된 `TransportSettings` 섹션을 추가합니다.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       이 경우 `CreateServiceReplicaListeners` 메서드는 다음과 같습니다.

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        settings.xml 파일에 `TransportSettings` 섹션을 추가하면 `FabricTransportRemotingListenerSettings`는 기본적으로 이 섹션의 모든 설정을 로드합니다.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        이 경우 `CreateServiceReplicaListeners` 메서드는 다음과 같습니다.

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` 클래스를 사용하여 서비스 프록시를 만드는 대신 원격 스택을 사용하여 보안 서비스에서 메서드를 호출하는 경우 `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`를 사용합니다. `SecurityCredentials`를 포함하는 `FabricTransportRemotingSettings`를 전달합니다.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    클라이언트 코드를 서비스의 일부로 실행하는 경우 settings.xml 파일에서 `FabricTransportRemotingSettings` 를 로드할 수 있습니다. 위에 나와 있는 서비스 코드와 비슷한 HelloWorldClientTransportSettings 섹션을 만듭니다. 클라이언트 코드를 다음과 같이 변경합니다.

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    클라이언트를 서비스의 일부로 실행하지 않는 경우에는 client_name.exe와 같은 위치에 client_name.settings.xml 파일을 만들 수 있습니다. 그런 다음 해당 파일에서 TransportSettings 섹션을 만듭니다.

    서비스와 마찬가지로 클라이언트 settings.xml/client_name.settings.xml에 `TransportSettings` 섹션을 추가하면 `FabricTransportRemotingSettings`는 기본적으로 이 섹션의 모든 설정을 로드합니다.

    그러면 앞의 코드를 더 간략하게 작성할 수 있습니다.  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


다음 단계는 [Reliable Services에서 OWIN을 사용하는 Web API](service-fabric-reliable-services-communication-webapi.md)를 참조하세요.
