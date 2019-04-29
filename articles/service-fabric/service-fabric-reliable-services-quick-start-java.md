---
title: Java에서 Azure Service Fabric Reliable Services 처음 만들기 | Microsoft Docs
description: 상태 비저장 및 상태 저장 서비스를 사용하여 Microsoft Azure Service Fabric 애플리케이션 만들기 소개
services: service-fabric
documentationcenter: java
author: suhuruli
manager: chackdan
editor: ''
ms.assetid: 7831886f-7ec4-4aef-95c5-b2469a5b7b5d
ms.service: service-fabric
ms.devlang: java
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/02/2017
ms.author: suhuruli
ms.openlocfilehash: 6bf8c632a7513d018745bc74aa0a1db95a39af8b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62130129"
---
# <a name="get-started-with-reliable-services"></a>Reliable Services로 시작하기
> [!div class="op_single_selector"]
> * [Windows에서 C#](service-fabric-reliable-services-quick-start.md)
> * [Linux에서 Java](service-fabric-reliable-services-quick-start-java.md)
>
>

이 문서는 Azure Service Fabric Reliable Services의 기본 개념을 설명하고 Java로 작성된 Reliable Services 애플리케이션을 생성 및 배포하는 과정을 안내합니다. 

## <a name="installation-and-setup"></a>설치 및 설정
시작하기 전에 컴퓨터에 Service Fabric 개발 환경이 설정되어 있는지 확인합니다.
설정해야 하는 경우 [Mac에서 시작](service-fabric-get-started-mac.md) 또는 [Linux에서 시작](service-fabric-get-started-linux.md)으로 이동합니다.

## <a name="basic-concepts"></a>기본 개념
Reliable Services를 시작하려면 몇 가지 기본 개념만 이해하면 됩니다.

* **서비스 유형**: 서비스 구현입니다. 이름 및 버전 번호와 함께 `StatelessService` 및 기타 코드 또는 여기에 사용된 종속성을 확장하도록 사용자가 작성한 클래스에 의해 정의됩니다.
* **명명된 서비스 인스턴스**: 서비스를 실행하려는 경우 클래스 유형의 개체 인스턴스를 만드는 것처럼 서비스 유형의 명명된 인스턴스를 만듭니다. 서비스 인스턴스는 실제로 사용자가 작성한 서비스 클래스의 개체 인스턴스화입니다.
* **서비스 호스트**: 생성한 명명된 서비스 인스턴스는 호스트 내에서 실행해야 합니다. 서비스 호스트는 서비스의 인스턴스에서 실행할 수 있는 프로세스일 뿐입니다.
* **서비스 등록**: 서비스를 등록하면 모든 요소가 통합 등록됩니다. Service Fabric에서 실행할 인스턴스를 만들 수 있도록 서비스 호스트의 Service Fabric 런타임에 서비스 유형을 등록해야 합니다.  

## <a name="create-a-stateless-service"></a>상태 비저장 서비스 만들기
Service Fabric 애플리케이션을 만드는 것으로 시작합니다. Linux용 Service Fabric SDK는 Service Fabric 애플리케이션용 스캐폴딩에 상태 비저장 서비스를 제공하기 위해 Yeoman 생성기를 포함합니다. 다음 Yeoman 명령을 실행하여 시작합니다.

```bash
$ yo azuresfjava
```

지침에 따라 **신뢰할 수 있는 상태 비저장 서비스**를 만듭니다. 이 자습서에서는 애플리케이션을 "HelloWorldApplication", 행위자를 "HelloWorld"라고 이름을 지정합니다. 결과에는 `HelloWorldApplication` 및 `HelloWorld`에 대한 디렉터리가 포함됩니다.

```bash
HelloWorldApplication/
├── build.gradle
├── HelloWorld
│   ├── build.gradle
│   └── src
│       └── statelessservice
│           ├── HelloWorldServiceHost.java
│           └── HelloWorldService.java
├── HelloWorldApplication
│   ├── ApplicationManifest.xml
│   └── HelloWorldPkg
│       ├── Code
│       │   ├── entryPoint.sh
│       │   └── _readme.txt
│       ├── Config
│       │   └── _readme.txt
│       ├── Data
│       │   └── _readme.txt
│       └── ServiceManifest.xml
├── install.sh
├── settings.gradle
└── uninstall.sh
```
### <a name="service-registration"></a>서비스 등록
서비스 유형은 Service Fabric 런타임에 등록되어야 합니다. 서비스 유형은 `ServiceManifest.xml`에서 정의되고 `StatelessService`를 구현하는 서비스 클래스입니다. 서비스 등록은 프로세스 주 진입점에서 수행됩니다. 이 예제에서는 프로세스 주 진입점은 `HelloWorldServiceHost.java`입니다.

```java
public static void main(String[] args) throws Exception {
    try {
        ServiceRuntime.registerStatelessServiceAsync("HelloWorldType", (context) -> new HelloWorldService(), Duration.ofSeconds(10));
        logger.log(Level.INFO, "Registered stateless service type HelloWorldType.");
        Thread.sleep(Long.MAX_VALUE);
    }
    catch (Exception ex) {
        logger.log(Level.SEVERE, "Exception in registration:", ex);
        throw ex;
    }
}
```

## <a name="implement-the-service"></a>서비스 구현

**HelloWorldApplication/HelloWorld/src/statelessservice/HelloWorldService.java**를 엽니다. 이 클래스는 서비스 유형을 정의하고 모든 코드를 실행할 수 있습니다. 서비스 API는 코드에 대한 두 진입점을 제공합니다.

* 장기 실행 계산 워크로드를 포함하여 모든 워크로드의 실행을 시작할 수 있는 `runAsync()`라는 개방형 진입점 메서드.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    ...
}
```

* 원하는 통신 스택을 연결할 수 있는 통신 진입점. 사용자 및 다른 서비스에서 요청을 수신하도록 시작할 수 있습니다.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {
    ...
}
```

이 자습서에서는 `runAsync()` 진입점 메서드에 집중합니다. 바로 코드를 실행하기 시작할 수 있습니다.

### <a name="runasync"></a>RunAsync
서비스의 인스턴스가 배치되어 실행할 준비가 되면 플랫폼이 이 메서드를 호출합니다. 상태 비저장 서비스의 경우 이는 서비스 인스턴스가 열릴 때를 의미합니다. 서비스 인스턴스를 종료해야 하는 경우 취소 토큰이 제공됩니다. 서비스 패브릭에서 서비스 인스턴스의 열림-닫힘 주기는 서비스의 수명 동안 전체적으로 여러 번 발생할 수 있습니다. 다음을 포함하여 여러 가지 이유로 발생할 수 있습니다.

* 시스템은 리소스 분산을 위해 서비스 인스턴스를 이동시킵니다.
* 오류는 코드에서 발생합니다.
* 애플리케이션 또는 시스템 업그레이드됩니다.
* 기본 하드웨어가 중단됩니다.

이러한 오케스트레이션은 서비스의 가용성을 높게 유지하고 제대로 균형을 유지하기 위해 Service Fabric에 의해 관리됩니다.

`runAsync()`는 동기적으로 차단하지 않아야 합니다. runAsync의 구현에서는 런타임을 지속할 수 있게 CompletableFuture를 반환해야 합니다. 워크로드에서 실행 시간이 긴 작업을 구현해야 하는 경우 이 작업은 CompletableFuture에서 수행해야 합니다.

#### <a name="cancellation"></a>취소
워크로드 취소는 제공된 취소 토큰에 의해 조정된 공동의 노력입니다. 시스템은 계속하기 전에 태스크가 종료(성공적인 완료, 취소 또는 오류에 의해)될 때까지 기다립니다. 시스템이 취소를 요청할 때 가능한 한 빨리 취소 토큰을 받아들이고 작업을 마치며 `runAsync()` 를 종료하는 것이 중요합니다. 다음 예제에서는 취소 이벤트를 처리하는 방법을 보여줍니다.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {

    // TODO: Replace the following sample code with your own logic
    // or remove this runAsync override if it's not needed in your service.

    return CompletableFuture.runAsync(() -> {
        long iterations = 0;
        while(true)
        {
        cancellationToken.throwIfCancellationRequested();
        logger.log(Level.INFO, "Working-{0}", ++iterations);

        try {
            Thread.sleep(1000);
        } catch (InterruptedException ex){}
        }
    });
}
```

이 상태 비저장 서비스 예에서는 개수가 로컬 변수에 저장됩니다. 하지만 이는 상태 비저장 서비스이므로 저장되는 값은 해당 서비스 인스턴스의 현재 주기에만 존재합니다. 서비스가 이동하거나 다시 시작되면 값이 손실됩니다.

## <a name="create-a-stateful-service"></a>상태 저장 서비스 만들기
서비스 패브릭은 상태 저장하는 서비스의 새로운 종류를 도입합니다. 상태 저장 서비스는 서비스를 사용하는 코드와 함께 위치한 서비스 자체 내에서 안정적으로 상태를 유지할 수 있습니다. 외부 저장소에 상태를 유지하지 않고도 서비스 패브릭에서 상태는 높은 가용성을 가집니다.

서비스가 이동하거나 다시 시작하는 경우에도 카운터 값을 상태 비저장에서 항상 사용 가능하고 지속되게 만들려면 상태 저장 서비스가 필요합니다.

HelloWorld 애플리케이션과 같은 디렉터리에서 `yo azuresfjava:AddService` 명령을 실행하여 새 서비스를 추가할 수 있습니다. 프레임워크에서 "신뢰할 수 있는 상태 저장 서비스"를 선택하고 서비스 이름을 "HelloWorldStateful"로 지정합니다. 

애플리케이션에 이제 상태 비저장 서비스 HelloWorld 및 상태 저장 서비스 HelloWorldStateful이라는 두 서비스가 있어야 합니다.

상태 저장 서비스에는 상태 비저장 서비스와 동일한 진입점이 있습니다. 주요 차이점은 상태를 안정적으로 저장할 수 있는 상태 제공자의 가용성입니다. Service Fabric은 신뢰할 수 있는 상태 관리자를 통해 복제된 데이터 구조를 만들 수 있는 신뢰할 수 있는 컬렉션이라는 상태 제공자 구현과 함께 제공됩니다. 상태 저장 Reliable Service는 기본적으로 이 상태 제공자를 사용합니다.

다음 RunAsync 메서드를 포함하는 **HelloWorldStateful -> src**에서 HelloWorldStateful.java를 엽니다.

```java
@Override
protected CompletableFuture<?> runAsync(CancellationToken cancellationToken) {
    Transaction tx = stateManager.createTransaction();
    return this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap").thenCompose((map) -> {
        return map.computeAsync(tx, "counter", (k, v) -> {
            if (v == null)
                return 1L;
            else
                return ++v;
            }, Duration.ofSeconds(4), cancellationToken)
                .thenCompose((r) -> tx.commitAsync())
                .whenComplete((r, e) -> {
            try {
                tx.close();
            } catch (Exception e) {
                logger.log(Level.SEVERE, e.getMessage());
            }
        });
    });
}
```

### <a name="runasync"></a>RunAsync
`RunAsync()` 은 상태 저장 및 상태 비저장 서비스에서 비슷하게 작동합니다. 그러나 상태 저장 서비스에서 플랫폼은 `RunAsync()`을 실행하기 전에 사용자 대신 추가 작업을 수행합니다. 이 작업은 신뢰할 수 있는 상태 관리자 및 신뢰할 수 있는 컬렉션을 사용할 준비가 되도록 포함할 수 있습니다.

### <a name="reliable-collections-and-the-reliable-state-manager"></a>신뢰할 수 있는 컬렉션 및 신뢰할 수 있는 상태 관리자
```java
ReliableHashMap<String,Long> map = this.stateManager.<String, Long>getOrAddReliableHashMapAsync("myHashMap")
```

[ReliableHashMap](https://docs.microsoft.com/java/api/microsoft.servicefabric.data.collections.reliablehashmap)은 서비스에서 상태를 안정적으로 저장하는데 사용할 수 있는 사전 구현입니다. Service Fabric 및 신뢰할 수 있는 해시 맵을 사용하면 외부 영구 저장소 없이도 서비스에 데이터를 직접 저장할 수 있습니다. 신뢰할 수 있는 해시 맵은 데이터를 항상 사용할 수 있게 만듭니다. 서비스 패브릭은 서비스의 여러 *복제본* 을 만들고 관리하여 이를 달성합니다. 또한 해당 복제본 및 해당 상태 전환을 관리하는 복잡성을 추상화하는 API를 제공합니다.

신뢰할 수 있는 컬렉션은 사용자 지정 형식을 포함하여 모든 Java 형식을 저장할 수 있습니다. 단, 몇 가지 주의 사항이 있습니다.

* Service Fabric은 노드의 상태를 *복제*하고, 신뢰할 수 있는 해시 맵은 데이터를 각 복제본의 로컬 디스크에 저장합니다. 즉, 신뢰할 수 있는 해시 맵에 저장된 모든 항목이 *직렬화 가능* 상태가 됩니다. 
* 신뢰할 수 있는 해시 맵의 트랜잭션을 커밋하면 고가용성을 위해 개체가 복제됩니다. 신뢰할 수 있는 해시 맵에 저장된 개체는 서비스의 로컬 메모리에 유지됩니다. 즉, 개체에 대한 로컬 참조가 있습니다.
  
   트랜잭션의 신뢰할 수 있는 컬렉션에서 업데이트 작업을 수행하지 않고 해당 개체의 로컬 인스턴스를 변환하지 않는 것이 중요합니다. 개체의 로컬 인스턴스에 대한 변경 내용은 자동으로 복제되지 않기 때문입니다. 개체를 디렉터리에 다시 삽입하거나 디렉터리의 *update* 메서드 중 하나를 사용해야 합니다.

신뢰할 수 있는 상태 관리자는 사용자에 대한 신뢰할 수 있는 해시 맵을 관리합니다. 언제든지 서비스의 어느 위치에서든 신뢰할 수 있는 컬렉션에 대한 신뢰할 수 있는 상태 관리자를 요청할 수 있습니다. 신뢰할 수 있는 상태 관리자는 참조를 다시 가져오도록 합니다. 클래스 멤버 변수 또는 속성에서 신뢰할 수 있는 컬렉션 인스턴스에 대한 참조를 저장하지 않는 것이 좋습니다. 서비스 수명 주기에서 항상 참조가 인스턴스로 설정되어 있도록 특별히 주의해야 합니다. 신뢰할 수 있는 상태 관리자는 사용자를 위해 이 작업을 처리하고 반복 방문에 최적화됩니다.


### <a name="transactional-and-asynchronous-operations"></a>트랜잭션 및 비동기 작업
```java
return map.computeAsync(tx, "counter", (k, v) -> {
    if (v == null)
        return 1L;
    else
        return ++v;
    }, Duration.ofSeconds(4), cancellationToken)
        .thenCompose((r) -> tx.commitAsync())
        .whenComplete((r, e) -> {
    try {
        tx.close();
    } catch (Exception e) {
        logger.log(Level.SEVERE, e.getMessage());
    }
});
```

신뢰할 수 있는 해시 맵의 작업은 비동기적입니다. 신뢰할 수 있는 컬렉션을 사용한 쓰기 작업에서는 I/O 작업을 수행하여 데이터를 복제하고 디스크에 보존하기 때문입니다.

신뢰할 수 있는 해시 맵 작업은 *트랜잭션*이므로 여러 신뢰할 수 있는 해시 맵 및 작업에서 상태를 일관성 있게 유지할 수 있습니다. 예를 들어 신뢰할 수 있는 사전에서 작업 항목을 가져오고, 여기서 작업을 수행하고, 다른 신뢰할 수 있는 해시 맵의 결과를 모두 단일 트랜잭션 내에 저장할 수 있습니다. 이는 원자성 작업으로 처리되며, 전체 작업이 성공하거나 롤백되도록 보장합니다. 항목을 큐에서 제거한 다음이지만 결과를 저장하기 전에 오류가 발생하면 전체 트랜잭션이 롤백되고 항목이 처리를 위해 큐에 남아 있습니다.


## <a name="build-the-application"></a>애플리케이션 빌드

Yeoman 스캐폴딩은 애플리케이션을 빌드하는 Gradle 스크립트와 애플리케이션을 배포하고 제거하는 Bash 스크립트를 포함합니다. 애플리케이션을 실행하려면 먼저 다음과 같은 Gradle을 통해 애플리케이션을 빌드합니다.

```bash
$ gradle
```

그러면 Service Fabric CLI를 사용하여 배포할 수 있는 Service Fabric 애플리케이션 패키지를 생성합니다.

## <a name="deploy-the-application"></a>애플리케이션 배포

애플리케이션이 빌드되면 로컬 클러스터에 배포할 수 있습니다.

1. 로컬 Service Fabric 클러스터에 연결합니다.

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. 템플릿에 제공된 설치 스크립트를 실행하여 클러스터의 이미지 저장소에 애플리케이션 패키지를 복사하고 애플리케이션 유형을 등록하며 애플리케이션의 인스턴스를 만듭니다.

    ```bash
    ./install.sh
    ```

빌드된 애플리케이션을 배포하는 방법은 다른 Service Fabric 애플리케이션과 같습니다. 자세한 지침은 [Service Fabric CLI에서 Service Fabric 애플리케이션 관리](service-fabric-application-lifecycle-sfctl.md)에 대한 설명서를 참조하세요.

애플리케이션 패키지 내에 생성된 매니페스트에서 이러한 명령의 매개 변수를 찾을 수 있습니다.

애플리케이션이 배포되면 브라우저를 열고 [http://localhost:19080/Explorer](http://localhost:19080/Explorer)에 있는 [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)로 이동합니다. 그런 다음 **애플리케이션** 노드를 확장하면 애플리케이션 형식에 대한 항목 및 해당 형식의 첫 번째 인스턴스에 대한 다른 항목이 만들어집니다.

> [!IMPORTANT]
> 애플리케이션을 Azure의 보안 Linux 클러스터에 배포하려면 Service Fabric 런타임으로 애플리케이션의 유효성을 검사하는 인증서를 구성해야 합니다. 이렇게 하면 Reliable Services 서비스에서 기본 Service Fabric 런타임 API와 통신할 수 있습니다. 자세히 알아보려면 [Linux 클러스터에서 실행하도록 Reliable Services 앱 구성](./service-fabric-configure-certificates-linux.md#configure-a-reliable-services-app-to-run-on-linux-clusters)을 참조하세요.  
>

## <a name="next-steps"></a>다음 단계

* [Service Fabric CLI 시작](service-fabric-cli.md)
