---
title: Azure Service Fabric에서 Java를 통한 서비스 원격 통신 보호 | Microsoft Docs
description: Azure Service Fabric 클러스터에서 실행되는 Java Reliable Services에서 서비스 원격 기반 통신을 보호하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: b465ab602a14285f8cf40b24ce1dfa9c763fecb8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60773352"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Java 서비스에서 서비스 원격 통신 보호
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-services-secure-communication.md)
> * [Linux에서 Java](service-fabric-reliable-services-secure-communication-java.md)
>
>

통신의 가장 중요한 측면 중 하나는 보안입니다. Reliable Services 애플리케이션 프레임워크는 보안을 향상시키는 데 사용할 수 있는 미리 빌드된 통신 스택 및 도구 몇 가지를 제공합니다. 이 문서에서는 Java 서비스에서 서비스 원격을 사용하는 경우 보안을 향상시키는 방법에 대해 설명합니다. Java로 작성된 Reliable Services에 대한 원격 기능을 설정하는 방법에 대해 설명하는 기존 [예제](service-fabric-reliable-services-communication-remoting-java.md)를 기반으로 빌드됩니다. 

Java 서비스에서 서비스 원격 기능을 사용하는 경우 서비스를 보호하려면 다음 단계를 따르세요.

1. 서비스의 원격 프로시저 호출에 사용할 수 있는 메서드를 정의하는 인터페이스 `HelloWorldStateless`을 만듭니다. 서비스는 `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` 패키지에 선언되는 `FabricTransportServiceRemotingListener`를 사용합니다. 이것은 원격 호출 기능을 제공하는 `CommunicationListener` 구현입니다.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. 수신기 설정 및 보안 자격 증명을 추가합니다.

    서비스 통신을 보호하는 데 사용할 인증서는 클러스터의 모든 노드에 설치해야 합니다. Linux에서 실행 중인 서비스의 경우 인증서를 PEM 형식 파일로 사용할 수 있어야 합니다. 인증서와 개인 키가 포함된 `.pem` 파일 또는 인증서가 포함된 `.crt` 파일 중 하나와 개인 키가 포함된 `.key` 파일입니다. 자세히 알아보려면 [Linux 노드에서 X.509 인증서의 위치 및 형식](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes)을 참조하세요.
    
    두 가지 방법으로 수신기 설정 및 보안 자격 증명을 제공할 수 있습니다.

   1. [config 패키지](service-fabric-application-and-service-manifests.md)를 사용하여 제공:

       settings.xml 파일에서 명명된 `TransportSettings` 섹션을 추가합니다.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       이 경우 `createServiceInstanceListeners` 메서드는 다음과 같습니다.

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        settings.xml 파일에 접두사 없이 `TransportSettings` 섹션을 추가하면 `FabricTransportListenerSettings`은(는) 기본적으로 이 섹션의 모든 설정을 로드합니다.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        이 경우 `CreateServiceInstanceListeners` 메서드는 다음과 같습니다.

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` 클래스를 사용하여 서비스 프록시를 만드는 대신 원격 스택을 사용하여 보안 서비스에서 메서드를 호출하는 경우 `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`를 사용합니다.

    클라이언트 코드를 서비스의 일부로 실행하는 경우 settings.xml 파일에서 `FabricTransportSettings` 를 로드할 수 있습니다. 위에 나와 있는 서비스 코드와 비슷한 TransportSettings 섹션을 만듭니다. 클라이언트 코드를 다음과 같이 변경합니다.

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
