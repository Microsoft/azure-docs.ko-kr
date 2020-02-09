---
title: Azure Cosmos DB Java 비동기 SDK 진단 및 문제 해결
description: 클라이언트 쪽 로깅 기타 타사 도구 등의 기능을 사용하여 Azure Cosmos DB 문제를 파악, 진단 및 해결합니다.
author: moderakh
ms.service: cosmos-db
ms.date: 04/30/2019
ms.author: moderakh
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 572139743c66546622450cef8f8a0fa264d24779
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65519974"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Azure Cosmos DB SQL API 계정에서 Java 비동기 SDK를 사용하는 경우 발생하는 문제 해결
이 문서에서는 Azure Cosmos DB SQL API 계정으로 [Java 비동기 SDK](sql-api-sdk-async-java.md)를 사용할 때 일반적인 문제, 해결, 진단 단계 및 도구를 설명합니다.
Java 비동기 SDK는 Azure Cosmos DB SQL API에 액세스하기 위한 클라이언트 쪽 논리적 표현을 제공합니다. 이 문서에서는 문제가 발생하는 경우 사용자에게 도움이 되는 도구 및 방법을 설명합니다.

이 목록을 사용하여 시작합니다.

* 이 문서의 [일반적인 문제 및 해결 방법] 섹션을 살펴봅니다.
* [GitHub에서 오픈 소스](https://github.com/Azure/azure-cosmosdb-java)로 사용할 수 있는 SDK를 살펴봅니다. 여기에는 능동적으로 모니터링되는 [문제 섹션](https://github.com/Azure/azure-cosmosdb-java/issues)이 있습니다. 해결 방법이 있는 유사한 문제가 이미 제출되었는지 확인합니다.
* [성능 팁](performance-tips-async-java.md)을 검토하고 제안된 사례를 따릅니다.
* 솔루션을 찾지 못한 경우 이 문서의 나머지 부분을 읽어봅니다. 그런 후, [GitHub 문제](https://github.com/Azure/azure-cosmosdb-java/issues)를 제출합니다.

## <a name="common-issues-workarounds"></a>일반적인 문제 및 해결 방법

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>네트워크 문제, Netty 읽기 시간 제한 오류, 낮은 처리량, 높은 대기 시간

#### <a name="general-suggestions"></a>일반 제안
* 앱이 Azure Cosmos DB 계정과 동일한 지역에서 실행되고 있는지 확인합니다. 
* 앱이 실행되는 호스트에서 CPU 사용량을 확인합니다. CPU 사용량이 90% 이상인 경우 더 높은 구성을 사용해서 호스트에서 앱을 실행합니다. 또는 더 많은 컴퓨터에서 부하를 분산할 수 있습니다.

#### <a name="connection-throttling"></a>연결 제한
연결 제한은 [호스트 컴퓨터의 연결 제한] 또는 [Azure SNAT(PAT) 포트 고갈] 중 하나로 인해 발생할 수 있습니다.

##### <a name="connection-limit-on-host"></a>호스트 컴퓨터의 연결 제한
일부 Linux 시스템(예: 'Red Hat')에는 열려 있는 파일의 총 수에 상한이 있습니다. Linux의 소켓은 파일로 구현되므로 이 숫자는 총 연결 수도 제한합니다.
다음 명령을 실행합니다.

```bash
ulimit -a
```
"nofile"로 식별되는 허용되는 열린 파일의 최대 수는 연결 풀 크기의 두 배 이상이어야 합니다. 자세한 내용은 [성능 팁](performance-tips-async-java.md)을 참조하세요.

##### <a name="snat"></a>Azure SNAT(PAT) 포트 고갈

공용 IP 주소 없이 앱이 Azure Virtual Machines에 배포되는 경우 기본적으로 [Azure SNAT 포트](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)는 VM 외부의 모든 엔드포인트에 대한 연결을 설정하는 데 사용됩니다. VM에서 Azure Cosmos DB 엔드포인트로 허용되는 연결 수는 [Azure SNAT 구성](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports)으로 제한됩니다.

 Azure SNAT 포트는 VM에 개인 IP 주소 및 VM에서 공용 IP 주소에 연결하려고 하는 프로세스가 있는 경우에만 사용됩니다. Azure SNAT 제한을 피하는 두 가지 해결 방법이 있습니다.

* Azure Virtual Machines 가상 네트워크의 서브넷에 Azure Cosmos DB 서비스 엔드포인트를 추가합니다. 자세한 내용은 [Azure Virtual Network 서비스 엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)를 참조하세요. 

    서비스 엔드포인트를 사용하도록 설정한 경우 요청이 더 이상 공용 IP에서 Azure Cosmos DB로 전송되지 않습니다. 대신 가상 네트워크 및 서브넷 ID가 전송됩니다. 공용 IP만 허용되는 경우 이 변경 내용으로 인해 방화벽이 삭제될 수 있습니다. 방화벽을 사용하는 경우 서비스 엔드포인트를 사용하도록 설정하면 [Virtual Network ACL](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl)을 사용하여 방화벽에 서브넷을 추가합니다.
* Azure VM에 공용 IP를 할당합니다.

##### <a name="cant-connect"></a>이 서비스에 도달할 수 없습니다.-방화벽
``ConnectTimeoutException`` SDK 서비스를 연결할 수 없습니다 나타냅니다.
직접 모드를 사용 하는 경우 오류를 다음과 유사한 나타날 수 있습니다.
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

앱에서 사용 하는 컴퓨터에서 실행 되는 방화벽이 있는 경우 10,000 20,000 대 직접 모드에서 사용 되는 포트 범위를 엽니다.
또한 따릅니다 합니다 [호스트 컴퓨터에서 연결 제한을](#connection-limit-on-host)합니다.

#### <a name="http-proxy"></a>HTTP 프록시

HTTP 프록시를 사용하는 경우 SDK `ConnectionPolicy`에서 구성된 연결 수를 지원할 수 있는지 확인합니다.
그렇지 않으면 연결 문제가 발생할 수 있습니다.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>잘못된 코딩 패턴: Netty IO 스레드 차단

SDK는 [Netty](https://netty.io/) IO 라이브러리를 사용하여 Azure Cosmos DB와 통신합니다. SDK는 비동기 API를 포함하며 Netty의 비차단 IO API를 사용합니다. SDK의 IO 작업은 IO Netty 스레드에서 수행됩니다. IO Netty 스레드의 수는 앱 머신의 CPU 코어 수와 동일하게 구성됩니다. 

Netty IO 스레드는 비차단 Netty IO 작업에만 사용해야 합니다. SDK는 앱의 코드에 대한 Netty IO 스레드 중 하나에서 API 호출 결과를 반환합니다. 앱이 Netty 스레드에서 결과를 수신한 후 오래 지속되는 작업을 수행하는 경우 SDK는 해당 내부 IO 작업을 수행하기에 충분한 IO 스레드가 없을 수도 있습니다. 이러한 앱 코딩으로 인해 낮은 처리량, 높은 대기 시간 및 `io.netty.handler.timeout.ReadTimeoutException` 실패가 발생할 수 있습니다. 해결 방법은 작업에 시간이 걸리는 것을 알고 있는 경우 스레드를 전환하는 것입니다.

예를 들어, 다음 코드 조각을 살펴보세요. Netty 스레드에서 수 밀리초 이상 소요되는 오래 지속되는 작업을 수행할 수 있습니다. 이 경우 결과적으로 IO 작업을 처리하기 위한 Netty IO 스레드가 없는 상태가 될 수 있습니다. 따라서 ReadTimeoutException 오류가 발생합니다.
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   해결 방법은 시간이 걸리는 작업을 수행하는 스레드를 변경하는 것입니다. 앱에 대한 스케줄러의 싱글톤 인스턴스를 정의합니다.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   계산이 많은 작업 또는 차단 IO 작업과 같이 시간이 걸리는 작업을 수행해야 할 수 있습니다. 이 경우 `.observeOn(customScheduler)` API를 사용하여 `customScheduler`가 제공하는 작업자로 스레드를 전환합니다.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
`observeOn(customScheduler)`을 사용하여 Netty IO 스레드를 해제하고 사용자 지정 스케줄러에서 제공한 사용자 고유의 사용자 지정 스레드로 전환합니다. 이와 같이 수정하면 문제가 해결됩니다. 이제 더 이상 `io.netty.handler.timeout.ReadTimeoutException` 오류가 발생하지 않습니다.

### <a name="connection-pool-exhausted-issue"></a>연결 풀 고갈 문제

`PoolExhaustedException`은 클라이언트 쪽 오류입니다. 이 오류는 앱 작업이 SDK 연결 풀에서 제공할 수 있는 것보다 크다는 것을 나타냅니다. 연결 풀 크기를 늘리거나 여러 앱에서 부하를 분산하세요.

### <a name="request-rate-too-large"></a>너무 많은 요청 빈도
이 오류는 서버 쪽 오류입니다. 프로비전된 처리량을 다 사용했음을 나타냅니다. 나중에 다시 시도하세요. 이 오류가 자주 발생하는 경우 컬렉션 처리량을 늘리는 것이 좋습니다.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Azure Cosmos DB 에뮬레이터 연결 오류

Azure Cosmos DB 에뮬레이터 HTTPS 인증서는 자체 서명입니다. SDK를 에뮬레이터와 함께 사용하려면 에뮬레이터 인증서를 Java TrustStore로 가져와야 합니다. 자세한 내용은 [Azure Cosmos DB 에뮬레이터 인증서 내보내기](local-emulator-export-ssl-certificates.md)를 참조하세요.

### <a name="dependency-conflict-issues"></a>종속성 충돌 문제

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

위의 예외 RxJava lib (예: 1.2.2)의 이전 버전에 종속성이 있는 것을 제안 합니다. SDK RxJava 1.3.8 RxJava의 이전 버전에서 사용할 수 없는 Api에 의존 합니다. 

RxJava 1.2.2에는 이러한 issuses 다른 종속성을 식별 하는 것에 대한 해결 방법은 RxJava-1.2.2 종속 전이 제외 하 고 CosmosDB SDK 허용 최신 버전을 표시 합니다.

RxJava 1.2.2 옆에 있는 프로젝트 pom.xml 파일에 다음 명령을 실행 하는 라이브러리는 식별:
```bash
mvn dependency:tree
```
자세한 내용은 참조는 [maven 종속성 트리 가이드](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html)합니다.

1\.2.2 RxJava 식별 한 후은 pom 파일에 제외 RxJava 전이적 종속성 lib는 전이적 종속성은 다른 프로젝트의 종속성에 대한 종속성을 수정할 수 있습니다.

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

자세한 내용은 참조는 [전이적 종속성 가이드 제외](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)합니다.


## <a name="enable-client-sice-logging"></a>클라이언트 SDK 로깅 사용

Java 비동기 SDK는 log4j 및 logback과 같은 인기 있는 로깅 프레임워크로 로깅을 지원하는 로깅 외관으로 SLF4j를 사용합니다.

예를 들어 로깅 프레임워크로 log4j를 사용하려는 경우 다음 라이브러리를 Java 클래스 경로에 추가합니다.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

log4j 구성도 추가합니다.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

자세한 내용은 [sfl4j 로깅 설명서](https://www.slf4j.org/manual.html)를 참조하세요.

## <a name="netstats"></a>OS 네트워크 통계
netstat 명령을 실행하여 `ESTABLISHED` 및 `CLOSE_WAIT`와 같은 상태에 있는 연결 수를 확인합니다.

Linux에서 다음 명령을 실행할 수 있습니다.
```bash
netstat -nap
```
Azure Cosmos DB 엔드포인트에 대한 연결로만 결과를 필터링합니다.

`ESTABLISHED` 상태에서 Azure Cosmos DB 엔드포인트에 대한 연결 수는 구성된 연결 풀 크기보다 크지 않아야 합니다.

Azure Cosmos DB 엔드포인트에 대한 많은 연결이 `CLOSE_WAIT` 상태일 수 있습니다. 1,000개가 넘을 수 있습니다. 이렇게 숫자가 높으면 연결이 설정되었다가 빠르게 삭제되는 것을 나타냅니다. 이 경우 잠재적으로 문제가 발생할 수 있습니다. 자세한 내용은 [일반적인 문제 및 해결 방법] 섹션을 참조하세요.

 <!--Anchors-->
[일반적인 문제 및 해결 방법]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[호스트 컴퓨터의 연결 제한]: #connection-limit-on-host
[Azure SNAT(PAT) 포트 고갈]: #snat


