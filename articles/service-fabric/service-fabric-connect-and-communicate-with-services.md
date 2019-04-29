---
title: Azure Service Fabric에서 서비스와 통신 및 연결 | Microsoft Docs
description: 서비스 패브릭에서 서비스에 대해 확인, 연결 및 통신하는 방법에 대해 알아봅니다.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: chackdan
editor: msfussell
ms.assetid: 7d1052ec-2c9f-443d-8b99-b75c97266e6c
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 55a0a1a8097ea46c7a3407b5f42824973edcf1a2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60882328"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>서비스 패브릭에서 서비스와 연결 및 통신
서비스 패브릭에서 서비스는 일반적으로 여러 VM에 배포된 서비스 패브릭 클러스터의 임의 위치에서 실행됩니다. 서비스 소유자에 의해 한 위치에서 다른 위치로 이동하거나 서비스 패브릭에 의해 자동으로 이동할 수 있습니다. 서비스는 특정 컴퓨터 또는 주소에 정적으로 연결되지 않습니다.

Service Fabric 애플리케이션은 일반적으로 여러 가지 서비스로 구성되며, 각 서비스는 전문적인 작업을 수행합니다. 이러한 서비스는 웹 애플리케이션의 다양한 부분을 렌더링하는 것과 같이 서로 통신하여 완전한 기능을 형성할 수 있습니다. 서비스에 연결되어 통신하는 클라이언트 애플리케이션도 있습니다. 이 문서에서는 서비스 패브릭에서 서비스와 통신 및 서비스 간 통신을 설정하는 방법을 설명합니다.

## <a name="bring-your-own-protocol"></a>사용자 고유의 프로토콜 가져오기
서비스 패브릭을 통해 서비스의 수명 주기를 관리할 수 있지만 서비스가 무엇을 수행할지를 결정하지는 않습니다. 이는 통신을 포함합니다. 서비스 패브릭에서 서비스가 열리면 원하는 모든 프로토콜 또는 통신 스택을 사용하여 들어오는 요청에 대한 엔드포인트를 설정할 수 있는 서비스 기회입니다. 서비스는 URI와 같은 모든 주소 지정 스키마를 사용하는 기본 **IP:포트** 주소에 대한 수신 대기 상태입니다. 여러 서비스 인스턴스 또는 복제본은 다른 포트를 사용해야 하는 경우나 Windows에서 http.sys 커널 드라이버와 같은 포트 공유 메커니즘을 사용하는 경우에 호스트 프로세스를 공유할 수 있습니다. 두 경우 모두, 호스트 프로세스의 각 서비스 인스턴스 또는 복제본에 고유한 주소 지정이 가능해야 합니다.

![서비스 엔드포인트][1]

## <a name="service-discovery-and-resolution"></a>서비스 검색 및 확인
분산 시스템에서는 시간이 지나면 한 컴퓨터에서 다른 컴퓨터로 서비스가 이동할 수 있습니다. 이는 리소스 분산, 업그레이드, 장애 조치(failover), 확장을 포함하여 여러 가지 이유로 발생할 수 있습니다. 즉, 서비스가 노드로 이동하면서 서비스 엔드포인트 주소가 다른 IP 주소로 변경되며 서비스가 동적으로 선택된 포트를 사용하는 경우 다른 포트에서 열릴 수 있습니다.

![서비스 배포][7]

서비스 패브릭은 명명 서비스라는 검색 및 확인 서비스를 제공합니다. 명명 서비스는 명명된 서비스 인스턴스를 수신 대기하는 엔드포인트 주소에 매핑하는 테이블을 유지 관리합니다. 서비스 패브릭의 모든 명명된 서비스 인스턴스에는 URI로 나타내는 고유한 이름(예: `"fabric:/MyApplication/MyService"`)이 있습니다. 서비스 이름은 서비스 수명이 지나도 변경되지 않으며 서비스가 이동할 때 엔드포인트 주소만 변경할 수 있습니다. 이는 일정한 URL을 갖는 웹 사이트와 유사하지만 IP 주소는 변경될 수 있습니다. 또한 웹에서 웹 사이트 URL을 IP 주소로 확인하는 DNS와 유사하게, 서비스 패브릭에는 서비스 이름을 해당 엔드포인트 주소에 매핑하는 등록자가 있습니다.

![서비스 엔드포인트][2]

서비스를 확인하고 연결하려면 루프에서 다음 단계를 수행해야 합니다.

* **해결**: 서비스가 명명 서비스에서 게시한 엔드포인트를 가져옵니다.
* **연결**: 해당 엔드포인트에서 사용하는 모든 프로토콜을 사용하여 서비스에 연결합니다.
* **다시 시도**: 여러 가지 이유로(예: 서비스가 엔드포인트 주소를 확인한 마지막 시점 이후 이동한 경우) 연결 시도가 실패할 수 있습니다. 이 경우 앞의 확인 및 연결 단계를 다시 시도하고 연결에 성공할 때까지 이 주기를 반복해야 합니다.

## <a name="connecting-to-other-services"></a>다른 서비스에 연결
클러스터의 노드는 동일한 로컬 네트워크에 있으므로 클러스터 내에서 서로 연결하고 있는 서비스는 다른 서비스의 엔드포인트에 직접 액세스할 수 있습니다. 서비스 간에 연결하기 쉽게 하려면 Service Fabric에서 명명 서비스를 사용하는 추가 서비스를 제공합니다. DNS 서비스 및 역방향 프록시 서비스.


### <a name="dns-service"></a>DNS 서비스
많은 서비스, 특히 컨테이너화된 서비스는 기존 URL 이름을 사용할 수 있으므로 Naming Service 프로토콜 대신 표준 DNS 프로토콜을 사용하여 이러한 이름을 확인할 수 있으면 특히 애플리케이션 "리프트 앤 시프트" 시나리오에서 매우 편리합니다. 바로 이것이 DNS 서비스의 용도입니다. DNS 서비스를 통해 DNS 이름을 서비스 이름에 매핑할 수 있으므로 엔드포인트 IP 주소를 확인할 수 있습니다. 

다음 다이어그램과 같이 Service Fabric 클러스터에서 실행되는 DNS 서비스는 DNS 이름을 서비스 이름에 매핑하며, 명명 서비스는 이 서비스 이름을 확인하여 연결할 엔드포인트 주소를 반환합니다. 서비스의 DNS 이름은 생성 시 제공됩니다. 

![서비스 엔드포인트][9]

DNS 서비스를 사용하는 방법에 대한 자세한 내용은 [Azure Service Fabric의 DNS 서비스](service-fabric-dnsservice.md) 문서를 참조하세요.

### <a name="reverse-proxy-service"></a>역방향 프록시 서비스
HTTPS를 포함하여 HTTP 엔드포인트를 노출하는 클러스터의 역방향 프록시 주소 서비스입니다. 역방향 프록시는 특정 URI 형식을 사용하여 다른 서비스와 해당 메서드 호출을 간소화하고 명명 서비스를 사용하여 한 서비스에서 다른 서비스와 통신하는 데 필요한 확인, 연결, 재시도 단계를 처리합니다. 즉, URL 호출처럼 간단한 과정으로 설정하여 다른 서비스를 호출할 때 명명 서비스를 사용자로부터 숨깁니다.

![서비스 엔드포인트][10]

역방향 프록시 서비스를 사용하는 방법에 대한 자세한 내용은 [Azure Service Fabric의 역방향 프록시](service-fabric-reverseproxy.md) 문서를 참조하세요.

## <a name="connections-from-external-clients"></a>외부 클라이언트에서 연결
클러스터의 노드는 동일한 로컬 네트워크에 있으므로 클러스터 내에서 서로 연결하고 있는 서비스는 다른 서비스의 엔드포인트에 직접 액세스할 수 있습니다. 그러나 일부 환경에서는 클러스터가 제한된 포트 집합을 통한 외부 수신 트래픽을 경로 지정하는 부하 분산 장치 뒤에 있을 수 있습니다. 이러한 경우 서비스는 여전히 서로 통신하고 명명 서비스를 사용하여 주소를 확인할 수 있지만 외부 클라이언트가 서비스에 연결할 수 있도록 하는 추가 단계를 수행해야 합니다.

## <a name="service-fabric-in-azure"></a>Azure의 서비스 패브릭
Azure의 서비스 패브릭 클러스터는 Azure 부하 분산 장치 뒤에 배치됩니다. 클러스터로의 모든 외부 트래픽은 부하 분산 장치를 통과해야 합니다. 부하 분산 장치는 지정된 포트에서의 인바운드 트래픽을 동일한 포트가 열린 임의 *노드* 에 자동으로 전달합니다. Azure Load Balancer는 *노드*에서 열린 포트만 알고 있으며 개별 *서비스*에 의해 열린 포트는 알지 못합니다.

![Azure 부하 분산 장치 및 서비스 패브릭 토폴로지][3]

예를 들어 포트 **80**에 외부 트래픽을 허용하려면 다음을 구성해야 합니다.

1. 포트 80에서 수신 대기하는 서비스를 작성합니다. 서비스의 ServiceManifest.xml에서 포트 80을 구성하고 서비스(예: 자체 호스트된 웹 서버)에서 수신기를 엽니다.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Azure에 서비스 패브릭 클러스터를 만들고 서비스를 호스트할 노드 형식에 대한 사용자 지정 엔드포인트 포트로 포트 **80** 을 지정합니다. 둘 이상의 노드 형식이 있는 경우 열려 있는 사용자 지정 엔드포인트 포트를 가진 노드 형식에서만 실행되도록 서비스에서 *배치 제약 조건* 을 설정할 수 있습니다.

    ![노드 형식에서 포트 열기][4]
3. 클러스터가 만들어지면 트래픽을 포트 80에 전달하도록 클러스터 리소스 그룹에서 Azure 부하 분산 장치를 구성합니다. Azure 포털을 통해 클러스터를 만들 때 구성된 각 사용자 지정 엔드포인트 포트에 대해 자동으로 설정됩니다.

    ![Azure 부하 분산 장치에서 트래픽 전달][5]
4. Azure 부하 분산 장치는 프로브를 사용하여 트래픽을 특정 노드에 보낼지 여부를 결정합니다. 프로브는 정기적으로 각 노드의 엔드포인트를 확인하여 노드 응답 여부를 결정합니다. 프로브가 구성된 횟수만큼 응답을 받지 못할 경우 부하 분산 장치는 해당 노드로의 트래픽 전송을 중지합니다. Azure 포털을 통해 클러스터를 만들 때 구성된 각 사용자 지정 엔드포인트 포트에 대해 프로브가 자동으로 설정됩니다.

    ![Azure 부하 분산 장치에서 트래픽 전달][8]

Azure Load Balancer 및 프로브는 *노드*만 알고 있으며 노드에서 실행하는 *서비스*는 알지 못합니다. Azure 부하 분산 장치는 프로브에 응답하는 노드에 항상 트래픽을 보내므로 프로브에 응답할 수 있는 노드에서 서비스를 사용할 수 있도록 주의해야 합니다.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: 기본 제공 통신 API 옵션
Reliable Services 프레임워크에서는 미리 작성된 여러 통신 옵션을 제공합니다. 그 중에서 가장 적합한 옵션은 프로그래밍 모델, 통신 프레임워크 및 서비스가 작성되는 프로그래밍 언어로 무엇을 선택하는지에 따라 달라집니다.

* **특정 프로토콜이 없는 경우:**  특정 통신 프레임워크를 선택하지는 않지만 항목을 신속하게 실행하고 싶을 때 가장 적합한 옵션은 Reliable Services 및 Reliable Actors에 대한 강력한 형식의 원격 프로시저 호출을 허용하는 [서비스 원격](service-fabric-reliable-services-communication-remoting.md)입니다. 이는 서비스 통신을 시작하기에 가장 쉽고 빠른 방법입니다. 서비스 원격은 서비스 주소, 연결, 다시 시도 및 오류 처리의 확인을 처리합니다. 이 기능은 C# 및 Java 애플리케이션에 둘 다 사용할 수 있습니다.
* **HTTP**: 언어 중립적 통신의 경우, HTTP는 Service Fabric에서 전적으로 지원하는 다양한 언어로 사용할 수 있는 도구 및 HTTP 서버와 함께 업계 표준 선택을 제공합니다. 서비스는 C# 애플리케이션용 [ASP.NET Web API](service-fabric-reliable-services-communication-webapi.md)를 포함하여 사용 가능한 모든 HTTP 스택을 사용할 수 있습니다. [서비스 확인, HTTP 연결 및 다시 시도 루프](service-fabric-reliable-services-communication.md)를 위해 C#으로 작성된 클라이언트는 `ICommunicationClient` 및 `ServicePartitionClient` 클래스를 활용할 수 있지만 Java의 경우 `CommunicationClient` 및 `FabricServicePartitionClient` 클래스를 사용합니다.
* **WCF**: 통신 프레임워크로 WCF를 사용하는 기존 코드가 있는 경우, 서버 쪽에 `WcfCommunicationListener`를 사용하고 클라이언트에 `WcfCommunicationClient` 및 `ServicePartitionClient` 클래스를 사용할 수 있습니다. 그러나 이 기능은 Windows 기반 클러스터의 C# 애플리케이션에만 사용할 수 있습니다. 자세한 내용은 이 문서에서 [WCF 기반 통신 스택 구현](service-fabric-reliable-services-communication-wcf.md)에 대한 부분을 참조하세요.

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>사용자 지정 프로토콜 및 기타 통신 프레임워크 사용
서비스는 TCP 소켓을 통한 사용자 지정 이진 프로토콜이든 [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) 또는 [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/)를 통한 스트리밍 이벤트이든 통신에 대한 모든 프로토콜 또는 프레임워크를 사용할 수 있습니다. 서비스 패브릭은 모든 검색 및 연결 작업을 추상화하면서도 통신 스택을 연결할 수 있는 통신 API를 제공합니다. 자세한 내용은 이 문서에서 [Reliable Services 통신 모델](service-fabric-reliable-services-communication.md) 에 대한 부분을 참조하세요.

## <a name="next-steps"></a>다음 단계
[Reliable Services 통신 모델](service-fabric-reliable-services-communication.md)에서 개념 및 사용 가능한 API에 대해 알아본 다음 [서비스 원격](service-fabric-reliable-services-communication-remoting.md)을 사용하여 빠르게 시작하거나 [OWIN 자체 호스트를 사용하는 Web API](service-fabric-reliable-services-communication-webapi.md)를 사용하여 통신 수신기를 작성하는 방법에 대해 자세히 알아보세요.

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
