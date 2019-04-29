---
title: Azure Service Fabric 역방향 프록시 | Microsoft Docs
description: Service Fabric의 역방향 프록시를 사용하여 클러스터 내부 및 외부에서 마이크로 서비스와 통신
services: service-fabric
documentationcenter: .net
author: BharatNarasimman
manager: chackdan
editor: vturecek
ms.assetid: 47f5c1c1-8fc8-4b80-a081-bc308f3655d3
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/03/2017
ms.author: bharatn
ms.openlocfilehash: 6ce6f1f6559b43a64fb7edd0773a20f8ee0cf8a3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60837966"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Azure Service Fabric의 역방향 프록시
Azure Service Fabric에 기본 제공되는 역방향 프록시는 Service Fabric 클러스터 탐색에서 마이크로 서비스의 실행을 지원하고 http 엔드포인트가 있는 타 서비스와 통신합니다.

## <a name="microservices-communication-model"></a>마이크로 서비스 통신 모델
서비스 패브릭의 마이크로 서비스는 클러스터의 노드 하위 집합에서 실행되며 여러 가지 이유로 노드 간에 마이그레이션할 수 있습니다. 따라서 마이크로 서비스에 대한 엔드포인트는 동적으로 변할 수 있습니다. 클러스터에서 다른 서비스를 찾아 통신하기 위해 마이크로 서비스는 다음 단계를 거쳐야 합니다.

1. 이름 지정 서비스를 통해 서비스 위치를 확인합니다.
2. 서비스에 연결합니다.
3. 연결 실패 시 적용하는 서비스 확인 및 재시도 정책을 구현하는 루프에서 앞의 단계 래핑

자세한 내용은 [서비스와 연결 및 통신](service-fabric-connect-and-communicate-with-services.md)을 참조하세요.

### <a name="communicating-by-using-the-reverse-proxy"></a>역방향 프록시를 사용하여 통신
역방향 프록시는 모든 노드에서 실행되고 클라이언트 서비스를 대신하여 엔드포인트 확인, 자동 재시도 및 기타 연결 실패를 처리하는 서비스입니다. 역방향 프록시는 클라이언트 서비스로부터의 요청을 처리하므로 다양한 정책을 적용하도록 구성될 수 있습니다. 역방향 프록시를 통해 클라이언트 서비스가 클라이언트 쪽 HTTP 통신 라이브러리를 사용할 수 있으며, 서비스에서 특수 확인 및 재시도 논리가 필요하지 않습니다. 

역방향 프록시는 클라이언트 서비스가 다른 서비스에 요청을 보내는 데 사용할 수 있게 로컬 노드에서 하나 이상의 엔드포인트를 공개합니다.

![내부 통신][1]

> [!NOTE]
> **지원되는 플랫폼**
>
> Service Fabric의 역방향 프록시는 현재 다음 플랫폼을 지원합니다.
> * *Windows 클러스터*: Windows 8 이상 또는 Windows Server 2012 이상
> * *Linux 클러스터*: Linux 클러스터에는 현재 역방향 프록시를 사용할 수 없습니다.
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>클러스터 외부에서 마이크로 서비스에 연결
마이크로 서비스에 대한 기본 외부 통신 모델은 옵트인 모델이며, 여기서 각 서비스는 외부 클라이언트에서 직접 액세스될 수 없습니다. 마이크로 서비스와 외부 클라이언트 간의 네트워크 경계인 [Azure Load Balancer](../load-balancer/load-balancer-overview.md)는 네트워크 주소 변환을 수행하고 외부 요청을 내부 IP:port 엔드포인트에 전달합니다. 마이크로 서비스의 엔드포인트에서 외부 클라이언트에 직접 액세스할 수 있도록 하려면 먼저 클러스터의 서비스가 사용하는 각 포트에 트래픽을 전달하도록 부하 분산 장치를 구성해야 합니다. 그뿐 아니라 대부분 마이크로 서비스( 특히 상태 저장 마이크로 서비스)가 클러스터의 모든 노드에 있는 것은 아닙니다. 마이크로 서비스는 장애 조치(Failover) 시 노드 간을 이동할 수 있습니다. 이러한 경우 부하 분산 장치를 트래픽을 전달할 복제본의 대상 노드 위치를 효과적으로 확인할 수 없습니다.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>클러스터 외부에서 역방향 프록시를 통해 마이크로 서비스에 연결
부하 분산 장치에서 개별 서비스의 포트를 구성하는 대신, 부하 분산 장치에서 역방향 프록시 포트만 구성할 수 있습니다. 이 구성을 사용하면 클러스터 외부의 클라이언트가 추가 구성 없이 역방향 프록시를 사용하여 클러스터 내부의 서비스에 연결할 수 있습니다.

![외부 통신][0]

> [!WARNING]
> 부하 분산 장치에서 역방향 프록시의 포트를 구성하면 HTTP 엔드포인트를 표시하는 클러스터의 모든 마이크로 서비스의 주소를 클러스터 외부에서 지정할 수 있습니다. 즉, 내부용으로 의도된 마이크로 서비스를 악의적인 사용자가 검색할 수 있습니다. 이는 잠재적으로 악용될 수 있는 심각한 취약성을 나타냅니다. 예를 들어 다음과 같습니다.
>
> * 악의적인 사용자는 충분히 강화된 공격 노출 영역이 없는 내부 서비스를 반복적으로 호출하여 서비스 거부 공격을 시작할 수 있습니다.
> * 악의적인 사용자가 잘못된 패킷을 내부 서비스로 전송하여 의도하지 않은 동작이 발생할 수 있습니다.
> * 내부용으로 의도된 서비스는 클러스터 외부의 서비스에 노출되지 않아야 하는 개인 정보나 중요한 정보를 반환할 수 있으므로 이 민감한 정보가 악의적인 사용자에게 노출될 수 있습니다. 
>
> 역방향 프록시 포트를 공개하기 전에 클러스터 및 클러스터에서 실행 중인 앱의 잠재적인 보안 결과를 완전히 파악하고 완화해야 합니다. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>역방향 프록시를 사용하여 서비스 주소를 지정하기 위한 URI 형식
역방향 프록시는 특정 URI(Uniform Resource Identifier) 형식을 사용하여 수신 요청을 전달해야 하는 서비스 파티션을 식별합니다.

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http(s):** HTTP 또는 HTTPS 트래픽을 허용하도록 역방향 프록시를 구성할 수 있습니다. HTTPS 전달의 경우 HTTPS에서 수신 대기하도록 역방향 프록시가 설정되면 [Connect to a secure service with the reverse proxy](service-fabric-reverseproxy-configure-secure-communication.md)(역방향 프록시를 사용하여 보안 서비스에 연결)를 참조하세요.
* **Cluster FQDN(정규화된 도메인 이름) | internal IP:** 외부 클라이언트의 경우 클러스터 도메인(예: mycluster.eastus.cloudapp.azure.com)을 통해 연결할 수 있도록 역방향 프록시를 구성할 수 있습니다. 기본적으로 역방향 프록시는 모든 노드에서 실행됩니다. 내부 트래픽의 경우 localhost 또는 모든 내부 노드 IP(예: 10.0.0.1)에서 역방향 프록시에 연결할 수 있습니다.
* **Port:** 역방향 프록시에 대해 지정된 포트(예: 19081)입니다.
* **ServiceInstanceName:** "fabric:/" 구성표 없이 연결하려고 하는 배포된 서비스 인스턴스의 정규화된 이름입니다. 예를 들어 *fabric:/myapp/myservice/* 서비스에 연결하려면 *myapp/myservice*를 사용합니다.

    서비스 인스턴스 이름은 대/소문자를 구분합니다. URL에서 서비스 인스턴스 이름의 대/소문자 표기가 달라지면 요청이 실패하고 404(찾을 수 없음)가 표시됩니다.
* **Suffix path(접미사 경로):** 연결하려는 서비스에 대한 실제 URL 경로(예: *myapi/values/add/3*)입니다.
* **PartitionKey:** 분할 서비스의 경우 연결하려는 파티션에 대해 계산된 파티션 키입니다. 참고로 이는 파티션 ID GUID가 *아닙니다* . 이 매개 변수는 단일 파티션 체계를 사용하는 서비스에는 필요하지 않습니다.
* **PartitionKind:** 서비스 파티션 구성표입니다. 이는 'Int64Range' 또는 'Named'일 수 있습니다. 이 매개 변수는 단일 파티션 체계를 사용하는 서비스에는 필요하지 않습니다.
* **ListenerName**: 서비스의 엔드포인트 형식은 {"Endpoints":{"Listener1":"Endpoint1","Listener2":"Endpoint2" ...}}입니다. 서비스에서 여러 엔드포인트를 노출하는 경우 이 매개 변수는 클라이언트 요청을 전달해야 하는 엔드포인트를 식별합니다. 서비스에 수신기 하나만 있으면 생략할 수 있습니다.
* **TargetReplicaSelector**: 대상 복제본 또는 인스턴스를 선택하는 방법을 지정합니다.
  * 대상 서비스가 상태 저장인 경우 TargetReplicaSelector는  'PrimaryReplica', 'RandomSecondaryReplica' 또는 'RandomReplica' 중 하나일 수 있습니다. 이 매개 변수를 지정하지 않으면 기본값은 'PrimaryReplica'입니다.
  * 대상 서비스가 상태 비저장인 경우 역방향 프록시는 서비스 파티션의 임의 인스턴스를 선택하여 요청을 전달합니다.
* **Timeout:**  클라이언트 요청을 대신하여 서비스에 대한 역방향 프록시에서 만든 HTTP 요청에 대한 시간 제한을 지정합니다. 기본값은 60초입니다. 선택적 매개 변수입니다.

### <a name="example-usage"></a>사용 예
예를 들어 다음 URL에서 HTTP 수신기를 여는 *fabric:/MyApp/MyService* 서비스를 살펴보겠습니다.

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

다음은 서비스에 대한 리소스입니다.

* `/index.html`
* `/api/users/<userId>`

서비스가 단일 분할 체계를 사용하는 경우 *PartitionKey* 및 *PartitionKind* 쿼리 문자열 매개 변수는 필요하지 않으며 다음과 같이 게이트웨이를 사용하여 서비스에 연결할 수 있습니다.

* 외부에서: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* 내부에서: `http://localhost:19081/MyApp/MyService`

서비스가 Uniform Int64 분할 체계를 사용하는 경우 *PartitionKey* 및 *PartitionKind* 쿼리 문자열 매개 변수를 사용하여 서비스의 파티션에 연결해야 합니다.

* 외부에서: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* 내부에서: `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

서비스가 노출하는 리소스에 연결하려면 URL의 서비스 이름 뒤에 리소스 경로를 추가합니다.

* 외부에서: `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* 내부에서: `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

그러면 게이트웨이가 이 요청을 서비스의 URL에 전달합니다.

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>포트 공유 서비스에 대한 특수 처리
Service Fabric 역방향 프록시는 서비스 주소의 다시 확인을 시도하고 서비스에 연결할 수 없는 경우 요청을 재시도합니다. 일반적으로 서비스에 연결할 수 없는 경우 서비스 인스턴스 또는 복제가 일반적인 수명 주기의 일부로 다른 노드로 이동된 것입니다. 이 경우 역방향 프록시는 엔드포인트가 더 이상 원래 확인된 주소에 열려 있지 않음을 나타내는 네트워크 연결 오류를 수신할 수 있습니다.

그러나 복제 또는 서비스 인스턴스는 호스트 프로세스를 공유할 수 있으며 다음을 비롯하여 http.sys 기반 웹 서버에 의해 호스팅될 경우 포트를 공유할 수도 있습니다.

* [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener%28v=vs.110%29.aspx)
* [ASP.NET 코어 WebListener](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [카타나](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

이 상황에서 웹 서버는 호스트 프로세스에서 사용할 수 있고 요청에 응답할 가능성이 있지만 확인된 서비스 인스턴스 또는 복제는 더 이상 호스트에서 사용할 수 없습니다. 이 경우 게이트웨이는 웹 서버에서 HTTP 404 응답을 수신합니다. 따라서 HTTP 404 응답은 다음과 같은 두 가지 의미를 가질 수 있습니다.

- 사례 #1: 서비스 주소가 올바르지만 사용자가 요청한 리소스가 없습니다.
- 사례 #2: 서비스 주소가 올바르지 않고 사용자가 요청한 리소스가 다른 노드에 있을 수 있습니다.

첫 번째 경우는 사용자 오류로 간주되는 일반적인 HTTP 404입니다. 그러나 두 번째 경우에 사용자는 존재하는 리소스를 요청했습니다. 서비스 자체가 이동되어 역방향 프록시가 해당 서비스를 찾을 수 없습니다. 역방향 프록시가 주소를 다시 확인하고 요청을 다시 시도해야 합니다.

그러므로 역방향 프록시가 두 경우를 구별할 수 있는 방법이 필요합니다. 이러한 구별을 하려면 서버에서 제공하는 힌트가 필요합니다.

* 기본적으로 역방향 프록시는 사례 # 2를 가정하여 요청을 다시 확인하고 다시 실행하려고 시도합니다.
* 역방향 프록시에 사례 #1이라는 것을 알리려면 서비스가 다음과 같은 HTTP 응답 헤더를 반환해야 합니다.

  `X-ServiceFabric : ResourceNotFound`

이 HTTP 응답 헤더는 요청한 리소스가 존재하지 않는 일반적인 HTTP 404 상황을 나타내며, 역방향 프록시는 서비스 주소를 다시 확인하려고 시도하지 않습니다.

## <a name="special-handling-for-services-running-in-containers"></a>컨테이너에서 실행되는 서비스에 대한 특수 처리

컨테이너 내에서 실행되는 서비스의 경우 다음 코드에서처럼 [역방향 프록시 URL](#uri-format-for-addressing-services-by-using-the-reverse-proxy)을 생성하는 환경 변수 `Fabric_NodeIPOrFQDN`을 사용할 수 있습니다.

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
로컬 클러스터의 경우 `Fabric_NodeIPOrFQDN`은 기본적으로 "localhost"로 설정됩니다. 매개 변수 `-UseMachineName`을 사용하여 로컬 클러스터를 시작하여 컨테이너가 노드에서 실행되는 역방향 프록시에 연결될 수 있는지 확인합니다. 자세한 내용은 [컨테이너를 디버그하도록 개발자 환경 구성](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers)을 참조하세요.

Docker Compose 컨테이너 내에서 실행되는 Service Fabric 서비스에는 특별한 docker-compose.yml *포트 섹션* http: 또는 https: 구성이 필요합니다. 자세한 내용은 [Azure Service Fabric의 Docker Compose 배포 지원](service-fabric-docker-compose.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계
* [클러스터에서 역방향 프록시를 설정 및 구성합니다](service-fabric-reverseproxy-setup.md).
* [역방향 프록시를 사용하여 보안 HTTP 서비스에 전달 설정](service-fabric-reverseproxy-configure-secure-communication.md)
* [역방향 프록시 이벤트 진단](service-fabric-reverse-proxy-diagnostics.md)
* [GitHub의 샘플 프로젝트](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)에서 서비스 간 HTTP 통신의 예제를 참조하세요.
* [Reliable Services 원격을 사용하여 원격 프로시저 호출](service-fabric-reliable-services-communication-remoting.md)
* [Reliable Services에서 OWIN을 사용하는 Web API](service-fabric-reliable-services-communication-webapi.md)
* [Reliable Services를 사용한 WCF 통신](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
