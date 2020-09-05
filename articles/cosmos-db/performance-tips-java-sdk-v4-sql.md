---
title: Azure Cosmos DB Java SDK v4에 대한 성능 팁
description: Java SDK v4에 대한 Azure Cosmos 데이터베이스 성능을 향상시키기 위한 클라이언트 구성 옵션에 대해 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.devlang: java
ms.topic: how-to
ms.date: 07/08/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: a014038996ae2846d059551b565feedd8de560a0
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258317"
---
# <a name="performance-tips-for-azure-cosmos-db-java-sdk-v4"></a>Azure Cosmos DB Java SDK v4에 대한 성능 팁

> [!div class="op_single_selector"]
> * [Java SDK v4](performance-tips-java-sdk-v4-sql.md)
> * [Async Java SDK v2](performance-tips-async-java.md)
> * [Sync Java SDK v2](performance-tips-java.md)
> * [.NET SDK v3](performance-tips-dotnet-sdk-v3-sql.md)
> * [.NET SDK v2](performance-tips.md)
> 

> [!IMPORTANT]  
> 이 문서의 성능 팁은 Azure Cosmos DB Java SDK v4 전용입니다. 자세한 내용은 Azure Cosmos DB Java SDK v4 [릴리스 정보](sql-api-sdk-java-v4.md), [Maven 리포지토리](https://mvnrepository.com/artifact/com.azure/azure-cosmos) 및 Azure Cosmos DB Java SDK v4 [문제 해결 가이드](troubleshoot-java-sdk-v4-sql.md)를 참조하세요. 현재 v4 이전 버전을 사용하는 경우 v4로 업그레이드하는 데 도움이 필요하면 [Azure Cosmos DB Java SDK v4로 마이그레이션](migrate-java-v4-sdk.md)을 참조하세요.
>

Azure Cosmos DB는 보장된 대기 시간 및 처리량으로 매끄럽게 크기가 조정되는 빠르고 유연한 분산 데이터베이스입니다. Azure Cosmos DB를 사용하여 데이터베이스의 크기를 조정하기 위해 주요 아키텍처를 변경하거나 복잡한 코드를 작성할 필요는 없습니다. 규모를 확장 및 축소하는 것은 단일 API 호출 또는 SDK 메서드 호출을 수행하는 것만큼 쉽습니다. 그러나 네트워크 호출을 통해 Azure Cosmos DB에 액세스하므로 Azure Cosmos DB Java SDK v4를 사용할 때 최대 성능을 얻기 위해 클라이언트 쪽에서 최적화를 수행할 수 있습니다.

"내 데이터베이스 성능을 향상시키는 방법"을 물으면 다음 옵션을 고려합니다.

## <a name="networking"></a>네트워킹

* **연결 모드: 직접 모드 사용**
<a id="direct-connection"></a>
    
    클라이언트에서 Azure Cosmos DB에 연결하는 방법은 특히 클라이언트 쪽 대기 시간 측면에서 성능에 중요한 영향을 미칩니다. 연결 모드는 클라이언트를 구성 하는 데 사용할 수 있는 키 구성 설정입니다. Java SDK v4 Azure Cosmos DB 사용 가능한 두 가지 연결 모드는 다음과 같습니다.  

    * 직접 모드 (기본값)      
    * 게이트웨이 모드

    이러한 연결 모드는 기본적으로 데이터 평면 요청에서 문서 읽기 및 쓰기를 수행 하는 경로를 클라이언트 컴퓨터에서 Azure Cosmos DB 백 엔드의 파티션으로 가져오는 조건입니다. 일반적으로 직접 모드는 최상의 성능을 위해 기본 설정 된 옵션입니다. 클라이언트는 백 엔드를 사용 하지 않고 직접 Azure Cosmos DB 백 엔드에 있는 파티션에 대 한 TCP 연결을 직접 열고 요청을 *직접*보낼 수 있습니다. 반대로 게이트웨이 모드에서는 클라이언트의 요청이 Azure Cosmos DB 프런트 엔드의 소위 "게이트웨이" 서버로 라우팅된 다음, Azure Cosmos DB 백 엔드에서 적절한 파티션으로 전달됩니다. 애플리케이션이 엄격한 방화벽으로 제한된 회사 네트워크 내에서 실행되는 경우 표준 HTTPS 포트 및 단일 엔드포인트를 사용하므로 게이트웨이 모드를 선택하는 것이 가장 좋습니다. 그러나 성능상의 균형을 유지하기 위해 게이트웨이 모드에는 Azure Cosmos DB에서 데이터를 읽거나 쓸 때마다 추가 네트워크 홉(클라이언트에서 게이트웨이로 및 게이트웨이에서 파티션으로)이 포함됩니다. 이로 인해 네트워크 홉이 적은 직접 모드에서는 더 나은 성능을 제공합니다.

    데이터 평면 요청에 대 한 연결 모드는 아래와 같이 *Directmode ()* 또는 *gmode ()* 메서드를 사용 하 여 Azure Cosmos DB 클라이언트 빌더에서 구성 됩니다. 기본 설정을 사용 하 여 두 모드를 구성 하려면 인수 없이 메서드를 호출 합니다. 그렇지 않으면 구성 설정 클래스 인스턴스를 인수로 전달 합니다 ( *Directmode ()* 의 경우*directconnectionconfig* , gmode의 경우 *directconnectionconfig* *()*).
    
    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4(Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientConnectionModeAsync)]

    # <a name="sync"></a>[동기화](#tab/api-sync)

    Java SDK V4(Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientConnectionModeSync)]

    --- 

    *Directmode ()* 메서드에는 다음과 같은 이유로 추가 재정의가 있습니다. 데이터베이스 및 컨테이너 CRUD와 같은 제어 평면 작업은 *항상* 게이트웨이 모드를 활용 합니다. 사용자가 데이터 평면 작업에 대해 직접 모드를 구성 하면 제어 평면 작업에서 기본 게이트웨이 모드 설정을 사용 합니다. 이는 대부분의 사용자에 게 적합 합니다. 그러나 데이터 평면 작업에 대 한 직접 모드를 사용 하 고 제어 평면 게이트웨이 모드 매개 변수의 tunability을 사용 하려는 사용자는 다음과 같은 *directmode ()* 재정의를 사용할 수 있습니다.

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4(Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientDirectOverrideAsync)]

    # <a name="sync"></a>[동기화](#tab/api-sync)

    Java SDK V4(Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientDirectOverrideSync)]

    --- 

<a name="collocate-clients"></a>
* **성능을 위해 동일한 Azure 지역에 클라이언트 배치** <a id="same-region"></a>

    가능한 경우 Azure Cosmos DB를 호출하는 애플리케이션을 Azure Cosmos 데이터베이스와 동일한 지역에 배치합니다. 대략적으로 비교한다면, 동일한 지역 내의 Azure Cosmos DB 호출은 1-2밀리초 내에 완료되지만 미국 서부와 동부 해안 간의 대기 시간은 50밀리초보다 큽니다. 클라이언트에서 Azure 데이터 센터 경계로 요청이 전달되는 경로에 따라 이러한 요청 간 대기 시간은 달라질 수 있습니다. 호출하는 애플리케이션이 프로비전된 Azure Cosmos DB 엔드포인트와 동일한 Azure 지역 내에 있도록 하면 가능한 최저 대기 시간을 얻을 수 있습니다. 사용 가능한 영역 목록은 [Azure 지역](https://azure.microsoft.com/regions/#services)을 참조하세요.

    :::image type="content" source="./media/performance-tips/same-region.png" alt-text="Azure Cosmos DB 연결 정책 그림" border="false":::

    다중 지역 Azure Cosmos DB 계정과 상호 작용하는 앱은 요청이 배치된 지역으로 전달되도록 [기본 설정 위치](tutorial-global-distribution-sql-api.md#preferred-locations)를 구성해야 합니다.

* **짧은 대기 시간을 위해 Azure VM에서 가속화된 네트워킹 사용**

성능을 최대화하려면 지침에 따라 [Windows(지침을 보려면 클릭)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) 또는 [Linux(지침을 보려면 클릭)](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) Azure VM에서 가속화된 네트워킹을 사용하도록 설정하는 것이 좋습니다.

가속화된 네트워킹을 사용하지 않으면 Azure VM과 다른 Azure 리소스 간에 전송되는 IO가 VM과 해당 네트워크 카드 사이에 위치한 호스트 및 가상 스위치를 통해 불필요하게 라우팅될 수 있습니다. 데이터 경로에 호스트 및 가상 스위치를 인라인으로 두면 통신 채널의 대기 시간과 jitter가 증가할 뿐만 아니라 VM의 CPU 사이클도 도용합니다. 가속화된 네트워킹을 사용하면 VM에서 중간자 없이 NIC와 직접 상호 작용합니다. 호스트와 가상 스위치에서 처리되었던 네트워크 정책 세부 정보는 이제 NIC의 하드웨어에서 처리되며, 호스트와 가상 스위치가 무시됩니다. 일반적으로 가속화된 네트워킹을 사용하도록 설정하는 경우 대기 시간이 줄어들고 처리량이 향상될 뿐만 아니라 더 *일관된* 대기 시간이 유지되며 CPU 사용률이 줄어들 수도 있습니다.

제한 사항: 가속화된 네트워킹은 VM OS에서 지원되어야 하며, VM이 중지 및 할당 취소된 경우에만 사용하도록 설정할 수 있습니다. VM은 Azure Resource Manager를 사용하여 배포할 수 없습니다.

자세한 내용은 [Windows](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) 및 [Linux](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) 지침을 참조하세요.

## <a name="sdk-usage"></a>SDK 사용
* **최신 SDK 설치**

    Azure Cosmos DB SDK는 최상의 성능을 제공하기 위해 지속적으로 향상됩니다. [Azure Cosmos DB SDK](sql-api-sdk-async-java.md) 페이지를 참조하여 최신 SDK를 확인하고 향상된 기능을 검토하세요.

* **애플리케이션 수명 동안 singleton Azure Cosmos DB 클라이언트 사용**

    각 Azure Cosmos DB 클라이언트 인스턴스는 스레드로부터 안전하며 연결 관리와 주소 캐싱을 효율적으로 수행합니다. Azure Cosmos DB 클라이언트에서 연결을 효율적으로 관리하고 성능을 향상시키려면 애플리케이션 수명 동안 AppDomain당 단일 Azure Cosmos DB 클라이언트 인스턴스를 사용하는 것이 좋습니다.

   <a id="max-connection"></a>

* **애플리케이션에 필요한 가장 낮은 일관성 수준 사용**

    *CosmosClient*를 만들 때 명시적으로 설정되지 않은 경우 사용되는 기본 일관성은 *세션*입니다. 애플리케이션 논리에 *세션* 일관성이 필요하지 않으면 *일관성*을 *최종*으로 설정합니다. 참고: Azure Cosmos DB 변경 피드 프로세서를 사용하는 애플리케이션에서는 *세션* 이상의 일관성을 사용하는 것이 좋습니다.

* **Async API를 사용하여 프로비저닝된 처리량 최대화**

    Azure Cosmos DB Java SDK v4는 Sync 및 Async라는 두 개의 API를 번들로 묶습니다. 대략적으로 말하면 Async API는 SDK 기능을 구현하지만, Sync API는 Async API에 대한 호출을 차단하는 씬 래퍼입니다. 이는 비동기 전용인 이전의 Azure Cosmos DB Async Java SDK v2 및 동기 전용이고 완전히 분리된 이전의 Azure Cosmos DB Sync Java SDK v2와는 대조적입니다. 
    
    API 선택은 클라이언트 초기화 중에 결정됩니다. *CosmosAsyncClient*는 Async API를 지원하지만, *CosmosClient*는 Sync API를 지원합니다. 
    
    비차단 IO를 구현하며, Azure Cosmos DB에 대한 요청을 실행할 때 처리량을 최대화하는 것이 목표인 경우 Async API를 선택하는 것이 가장 좋습니다. 
    
    각 요청에 대한 응답을 차단하는 API가 필요하거나 동기 작업이 애플리케이션의 주요 패러다임인 경우 Sync API를 사용하는 것이 올바른 선택일 수 있습니다. 예를 들어 처리량이 중요하지 않으면 마이크로서비스 애플리케이션에서 Azure Cosmos DB에 대한 데이터를 유지할 때 Sync API를 사용할 수 있습니다.  
    
    요청 응답 시간이 증가함에 따라 Sync API 처리량이 저하되지만, Async API는 하드웨어의 전체 대역폭 기능을 포화 상태로 만들 수 있습니다. 
    
    지리적 배치를 사용하면 Sync API를 사용할 때 더 높고 일관된 처리량을 제공할 수 있습니다([성능을 위해 동일한 Azure 지역에 클라이언트 배치](#collocate-clients) 참조). 그러나 여전히 달성 가능한 Async API 처리량을 초과하지는 않습니다.

    또한 일부 사용자는 Azure Cosmos DB Java SDK v4 Async API를 구현하는 데 사용되는 Reactive Streams 프레임워크인 [Project Reactor](https://projectreactor.io/)에 익숙하지 않을 수 있습니다. 문제가 되는 경우 [Reactor 패턴 가이드](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-pattern-guide.md) 소개를 읽은 다음, 이 [반응성 프로그래밍 소개](https://tech.io/playgrounds/929/reactive-programming-with-reactor-3/Intro)를 살펴보고 숙지합니다. 이미 비동기 인터페이스를 통해 Azure Cosmos DB를 사용했고 사용한 SDK가 Azure Cosmos DB Async Java SDK v2인 경우 [ReactiveX](http://reactivex.io/)/[RxJava](https://github.com/ReactiveX/RxJava)에는 익숙하지만 Project Reactor에서 변경된 내용을 확실히 알지 못할 수 있습니다. 이 경우 [Reactor 및 RxJava 가이드](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md)를 살펴보고 숙지합니다.

    다음 코드 조각에서는 각각 Async API 또는 Sync API 작업을 위해 Azure Cosmos DB 클라이언트를 초기화하는 방법을 보여 줍니다.

    ### <a name="java-v4-sdk"></a><a id="override-default-consistency-javav4"></a> Java V4 SDK

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4(Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceClientAsync)]

    # <a name="sync"></a>[동기화](#tab/api-sync)

    Java SDK V4(Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceClientSync)]

    --- 

* **ConnectionPolicy 튜닝**

    Azure Cosmos DB Java SDK v4를 사용하는 경우 직접 모드 Cosmos DB 요청은 기본적으로 TCP를 통해 수행됩니다. 내부적으로 직접 모드는 특수 아키텍처를 사용 하 여 네트워크 리소스를 동적으로 관리 하 고 최적의 성능을 얻을 수 있습니다.

    Azure Cosmos DB Java SDK v4에서 직접 모드는 대부분의 워크로드에서 데이터베이스 성능을 향상시키기 위한 가장 좋은 방법입니다. 

    * ***직접 모드 개요***

        :::image type="content" source="./media/performance-tips-async-java/rntbdtransportclient.png" alt-text="직접 모드 아키텍처의 그림" border="false":::

        직접 모드에서 사용되는 클라이언트 쪽 아키텍처를 사용하면 네트워크 사용률을 예측할 수 있고 Azure Cosmos DB 복제본에 멀티플렉싱 방식으로 액세스할 수 있습니다. 위의 다이어그램에서는 직접 모드에서 Cosmos DB 백 엔드를 통해 클라이언트 요청을 복제본으로 라우팅하는 방법을 보여 줍니다. 직접 모드 아키텍처는 DB 복제본당 최대 10개의 **채널**을 클라이언트 쪽에 할당합니다. 채널은 요청 깊이가 30개인 요청 버퍼 뒤에 오는 TCP 연결입니다. 복제본에 속하는 채널은 복제본의 **서비스 엔드포인트**에서 필요에 따라 동적으로 할당됩니다. 사용자가 직접 모드에서 요청을 실행하면 **TransportClient**에서 파티션 키에 따라 요청을 적절한 서비스 엔드포인트로 라우팅합니다. **요청 큐**는 서비스 엔드포인트 앞에 요청을 버퍼링합니다.

    * ***직접 모드의 구성 옵션***

        기본이 아닌 직접 모드 동작을 원하는 경우 *Directconnectionconfig* 인스턴스를 만들고 해당 속성을 사용자 지정한 다음 Azure Cosmos DB client Builder의 *directmode ()* 메서드에 사용자 지정 된 속성 인스턴스를 전달 합니다.

        이러한 구성 설정은 위에서 설명한 기본 직접 모드 아키텍처의 동작을 제어 합니다.

        첫 번째 단계로 아래 추천 구성 설정을 사용합니다. 이러한 *Directconnectionconfig* 옵션은 예기치 않은 방식으로 SDK 성능에 영향을 줄 수 있는 고급 구성 설정입니다. 장단점을 이해 하는 데 매우 편안 하 게 생각 하 고 반드시 필요한 경우가 아니면 수정 하지 않는 것이 좋습니다. 이 특정 항목에서 문제가 발생하는 경우 [Azure Cosmos DB 팀](mailto:CosmosDBPerformanceSupport@service.microsoft.com)에 문의하세요.

        | 구성 옵션       | 기본값    |
        | :------------------:       | :-----:    |
        | idleConnectionTimeout      | "PT1M"     |
        | maxConnectionsPerEndpoint  | "PT0S"     |
        | connectTimeout             | "PT1M10S"  |
        | idleEndpointTimeout        | 8388608    |
        | maxRequestsPerConnection   | 10         |

* **분할된 컬렉션에 대한 병렬 쿼리 튜닝**

    Azure Cosmos DB Java SDK v4는 병렬 쿼리를 지원하므로 분할된 컬렉션을 병렬로 쿼리할 수 있습니다. 자세한 내용은 Azure Cosmos DB Java SDK v4 사용과 관련된 [코드 샘플](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)을 참조하세요. 병렬 쿼리는 해당 직렬 대응을 통해 쿼리 대기 시간 및 처리량을 개선하기 위해 설계되었습니다.

    * ***setMaxDegreeOfParallelism 튜닝\:***
    
        병렬 쿼리는 여러 파티션을 병렬로 쿼리하여 작동합니다. 그러나 개별 분할된 컬렉션의 데이터는 쿼리와 관련하여 순차적으로 가져오기 됩니다. 따라서 setMaxDegreeOfParallelism을 사용하여 파티션 수를 설정하면 다른 모든 시스템 조건을 동일하게 유지하는 동시에 가장 성능이 뛰어난 쿼리를 달성할 수 있는 가능성을 극대화합니다. 파티션 수를 모르는 경우 setMaxDegreeOfParallelism을 사용하여 더 높은 값을 설정할 수 있습니다. 그러면 시스템에서 최소값(사용자가 제공한 입력인 파티션 수)을 최대 병렬 처리 수준으로 선택합니다.

        데이터가 쿼리와 관련하여 모든 파티션에 균등하게 분산되어 있는 경우 병렬 쿼리가 최고의 성능을 발휘한다는 것이 중요합니다. 쿼리에서 반환된 전체 또는 대부분의 데이터가 몇 개의 파티션(최악의 경우 하나의 파티션)에 집중되는 것처럼 분할된 컬렉션이 분할되는 경우 해당 파티션으로 인해 쿼리의 성능에는 장애가 발생합니다.

    * ***setMaxBufferedItemCount 튜닝\:***
    
        병렬 쿼리는 클라이언트에서 현재 결과 일괄 처리를 처리하는 동안 결과를 프리페치하도록 설계되었습니다. 프리페치는 쿼리의 전체 대기 시간 개선 사항에 도움이 됩니다. setMaxBufferedItemCount는 프리페치된 결과의 수를 제한합니다. setMaxBufferedItemCount를 설정하면 반환된 결과의 예상 수(또는 더 높은 수)로 설정하면 쿼리가 프리페치의 최대 이점을 얻을 수 있습니다.

        프리페치는 MaxDegreeOfParallelism에 관계없이 동일한 방식으로 작동하고 여기에는 모든 파티션의 데이터에 대한 단일 버퍼가 있습니다.

* **클라이언트 워크로드 규모 확장**

    높은 처리량 수준에서 테스트하는 경우 컴퓨터에서 CPU 또는 네트워크 사용률 제한을 초과하여 클라이언트 애플리케이션에서 병목 상태가 발생할 수 있습니다. 이 시점에 도달하면 여러 서버에 걸쳐 클라이언트 애플리케이션을 확장하여 Azure Cosmos DB 계정을 계속 추가할 수 있습니다.

    대기 시간을 짧게 유지하려면 지정된 서버에서 CPU 사용률이 50%를 초과하지 않는 것이 좋습니다.

   <a id="tune-page-size"></a>

* **성능 향상을 위해 쿼리/읽기 피드에 맞게 페이지 크기 조정**

    읽기 피드 기능(예: *readItems*)을 사용하여 문서를 대량으로 읽거나 SQL 쿼리(*queryItems*)를 실행하는 경우 결과 세트가 너무 크면 결과가 분할되어 반환됩니다. 기본적으로, 100개의 항목 또는 1MB 단위(둘 중 먼저 도달하는 단위)로 결과가 반환됩니다.

    애플리케이션에서 Azure Cosmos DB에 대한 쿼리를 실행하고 작업을 완료하기 위해 전체 쿼리 결과 세트를 요구한다고 가정합니다. 적용 가능한 모든 결과를 검색하는 데 필요한 네트워크 왕복 횟수를 줄이려면 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 요청 헤더 필드를 조정하여 페이지 크기를 늘릴 수 있습니다. 

    * 단일 파티션 쿼리의 경우 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 필드 값을 -1(페이지 크기 제한 없음)로 조정하면 쿼리 응답 페이지 수를 최소화하여 대기 시간을 최대화합니다. 전체 결과 세트가 단일 페이지로 반환되거나, 쿼리가 시간 제한 간격보다 오래 걸리는 경우 전체 결과 세트가 가능한 최소 페이지 수로 반환됩니다. 이렇게 하면 여러 배수의 요청 왕복 시간이 절약됩니다.
    
    * 교차 파티션 쿼리의 경우 [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 필드를 -1로 설정하고 페이지 크기 제한을 제거하면 관리할 수 없는 페이지 크기로 인해 SDK에 과도한 부하가 발생할 위험이 있습니다. 교차 파티션 사례에서는 대기 시간을 줄이기 위해 페이지 크기 제한을 어느 정도 큰 값이지만 유한 값(약 1,000)까지 높이는 것이 좋습니다. 
    
    일부 애플리케이션에서는 전체 쿼리 결과 세트가 필요하지 않을 수 있습니다. 예를 들어 사용자 인터페이스 또는 애플리케이션 API에서 한 번에 10개의 결과만 반환하는 경우와 같이 몇 가지 결과만 표시해야 하는 경우 페이지 크기를 10으로 줄여 읽기 및 쿼리에 사용되는 처리량을 줄일 수도 있습니다.

    또한 REST 헤더 필드를 직접 수정하는 대신, *byPage* 메서드의 기본 설정 페이지 크기 인수를 설정할 수 있습니다. [x-ms-max-item-count](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 또는 *byPage*의 기본 설정 페이지 크기 인수는 절대적인 요구 사항이 아니라 페이지 크기에 대한 상한만 설정하는 것입니다. 따라서 다양한 이유로 기본 페이지 크기보다 작은 Azure Cosmos DB 반환 페이지가 표시될 수 있습니다. 

* **적절한 스케줄러 사용(이벤트 루프 IO Netty 스레드 도용 방지)**

    Azure Cosmos DB Java SDK의 비동기 기능은 [netty](https://netty.io/) 비차단 IO를 기반으로 합니다. SDK는 IO 작업을 실행하기 위해 고정된 수의 IO netty 이벤트 루프 스레드(시스템에 있는 CPU 코어 수만큼)를 사용합니다. API에서 반환되는 Flux는 공유 IO 이벤트 반복 netty 스레드 중 하나에 대한 결과를 내보냅니다. 따라서 이 공유 IO 이벤트 루프 netty 스레드를 차단하지 않는 것이 중요합니다. IO 이벤트 루프 netty 스레드에서 CPU 집약적인 작업 또는 차단 작업을 수행하면 교착 상태가 발생하거나 SDK 처리량이 크게 저하될 수 있습니다.

    예를 들어 다음 코드는 이벤트 루프 IO netty 스레드에서 CPU 집약적인 작업을 실행합니다.
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-noscheduler"></a>Java SDK V4(Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNeedsSchedulerAsync)]

    결과에 대해 CPU 집약적인 작업을 수행하려는 경우 결과를 수신한 후에는 이벤트 루프 IO netty 스레드에서 이를 수행하지 않아야 합니다. 대신 사용자 고유의 스케줄러를 제공 하 여 아래와 같이 작업을 실행 하기 위한 고유한 스레드를 제공할 수 있습니다 (필요 `import reactor.core.scheduler.Schedulers` ).

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4(Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddSchedulerAsync)]

    작업 유형에 따라 적절한 Reactor Scheduler를 작업에 사용해야 합니다. 여기 [``Schedulers``](https://projectreactor.io/docs/core/release/api/reactor/core/scheduler/Schedulers.html)를 읽으세요.

    Azure Cosmos DB Java SDK v4에 대한 자세한 내용은 [GitHub에 있는 Java용 Azure SDK에 대한 Cosmos DB 디렉터리 단일 리포지토리](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)를 참조하세요.

* **애플리케이션의 로깅 설정 최적화**

    다양한 이유로 높은 요청 처리량을 생성하는 스레드에서 로깅을 추가하려고 하거나 추가해야 할 수도 있습니다. 이 스레드에서 생성된 요청을 사용하여 프로비저닝된 컨테이너의 처리량을 완전한 포화 상태로 만드는 것이 목표인 경우 로깅 최적화는 성능을 크게 향상시킬 수 있습니다.

    * ***비동기 로거 구성***

        동기 로거의 대기 시간은 반드시 요청 생성 스레드의 전체 대기 시간 계산에 영향을 미칩니다. 고성능 애플리케이션 스레드에서 로깅 오버헤드를 분리하려면 [log4j2](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Flogging.apache.org%2Flog4j%2Flog4j-2.3%2Fmanual%2Fasync.html&data=02%7C01%7CCosmosDBPerformanceInternal%40service.microsoft.com%7C36fd15dea8384bfe9b6b08d7c0cf2113%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637189868158267433&sdata=%2B9xfJ%2BWE%2F0CyKRPu9AmXkUrT3d3uNA9GdmwvalV3EOg%3D&reserved=0)와 같은 비동기 로거를 사용하는 것이 좋습니다.

    * ***netty 로깅 사용 안 함***

        Netty 라이브러리 로깅은 대화량이 많으므로 추가 CPU 비용을 피하려면 꺼두어야 합니다(구성에서 로그를 억제하는 것으로 충분하지 않음). 디버깅 모드가 아니라면 netty의 로깅을 모두 해제합니다. 따라서 log4j를 사용하여 netty에서 ``org.apache.log4j.Category.callAppenders()``에 의한 추가 CPU 비용이 발생하지 않도록 하려면 코드베이스에 다음 행을 추가합니다.

        ```java
        org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
        ```

 * **OS 파일 열기 리소스 제한**
 
    일부 Linux 시스템(Red Hat 등)에는 열린 파일 수와 총 연결 수에 대한 상한이 있습니다. 현재 한도를 보려면 다음을 실행합니다.

    ```bash
    ulimit -a
    ```

    열린 파일(nofile)의 수는 OS에 의해 구성된 연결 풀 크기와 다른 열린 파일을 위한 공간이 충분할 만큼 커야 합니다. 연결 풀 크기를 더 크게 수정할 수 있습니다.

    limits.conf 파일을 엽니다.

    ```bash
    vim /etc/security/limits.conf
    ```
    
    다음 줄을 추가/수정합니다.

    ```
    * - nofile 100000
    ```

* **지점 쓰기에서 파티션 키 지정**

    지점 쓰기의 성능을 향상시키려면 아래와 같이 지점 쓰기 API 호출에서 항목 파티션 키를 지정합니다.

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4(Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceNoPKAsync)]

    # <a name="sync"></a>[동기화](#tab/api-sync)

    Java SDK V4(Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceNoPKSync)]

    --- 

    대신 아래와 같이 항목 인스턴스만 제공합니다.

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4(Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceAddPKAsync)]

    # <a name="sync"></a>[동기화](#tab/api-sync)

    Java SDK V4(Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceAddPKSync)]

    --- 

    후자는 지원되지만 대기 시간을 애플리케이션에 추가합니다. SDK는 항목을 구문 분석하여 파티션 키를 추출해야 합니다.

## <a name="indexing-policy"></a>인덱싱 정책
 
* **더 빠른 쓰기에 대한 인덱싱에서 사용하지 않는 경로 제외**

    Azure Cosmos DB의 인덱싱 정책을 통해 인덱싱 경로(setIncludedPaths 및 setExcludedPaths)를 활용하여 인덱싱에 포함하거나 제외할 문서 경로를 지정할 수 있습니다. 인덱싱 비용이 인덱싱된 고유 경로 수와 직접 관련이 있기 때문에, 인덱싱 경로를 사용하면 사전에 알려진 쿼리 패턴의 시나리오에 대해 쓰기 성능을 향상시키고 인덱스 스토리지를 낮출 수 있습니다. 예를 들어 다음 코드는 "*" 와일드 카드를 사용 하 여 인덱싱에서 문서의 전체 섹션 (하위 트리 라고도 함)을 포함 하 고 제외 하는 방법을 보여 줍니다.

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos"></a><a id="java4-indexing"></a>Java SDK V4(Maven com.azure::azure-cosmos)

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=MigrateIndexingAsync)]

    자세한 내용은 [Azure Cosmos DB 인덱싱 정책](indexing-policies.md)을 참조하세요.

## <a name="throughput"></a>처리량
<a id="measure-rus"></a>

* **낮은 요청 단위/초 사용량 측정 및 튜닝**

    Azure Cosmos DB는 관계형 쿼리와 계층형 쿼리 등 다양한 데이터베이스 작업에 데이터베이스 컬렉션 내부의 문서에서 적용되는 UDF, 저장 프로시저 및 트리거를 제공합니다. 이러한 작업 각각과 관련된 비용은 작업을 완료하는 데 필요한 CPU, IO 및 메모리에 따라 달라집니다. 하드웨어 리소스를 고려하고 관리하는 대신 다양한 데이터베이스 작업을 수행하고 애플리케이션 요청을 처리하는 데 필요한 리소스의 단일 측정값으로 RU(요청 단위)를 고려할 수 있습니다.

    처리량은 각 컨테이너에 설정된 [요청 단위](request-units.md) 수에 따라 프로비전됩니다. 요청 단위 소비는 초당 비율로 평가됩니다. 해당 컨테이너에 프로비전된 요청 단위 비율을 초과하는 애플리케이션은 비율이 컨테이너에 프로비전된 수준 아래로 떨어질 때까지 제한됩니다. 애플리케이션에 더 높은 수준의 처리량이 필요한 경우 추가 요청 단위를 프로비전하여 처리량을 늘릴 수 있습니다.

    쿼리의 복잡성은 작업에 사용되는 요청 단위의 양에 영향을 줍니다. 조건자의 수, 조건자의 특성, UDF 수 및 원본 데이터 집합의 크기는 모두 쿼리 작업의 비용에 영향을 줍니다.

    모든 작업(만들기, 업데이트 또는 삭제)에 대한 오버헤드를 측정하려면 [x-ms-request-charge](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 헤더를 검사하여 이 작업에 사용된 요청 단위 수를 측정합니다. ResourceResponse\<T> 또는 FeedResponse\<T>에서 동일한 RequestCharge 속성을 볼 수도 있습니다.

    # <a name="async"></a>[Async](#tab/api-async)

    Java SDK V4(Maven com.azure::azure-cosmos) Async API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=PerformanceRequestChargeAsync)]

    # <a name="sync"></a>[동기화](#tab/api-sync)

    Java SDK V4(Maven com.azure::azure-cosmos) Sync API

    [!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/sync/SampleDocumentationSnippets.java?name=PerformanceRequestChargeSync)]

    --- 

    이 헤더에서 반환된 요청 비용은 프로비전된 처리량의 일부입니다. 예를 들어 RU 2000개를 프로비전했고 앞의 쿼리에서 1000개의 1KB 문서를 반환하는 경우 작업 비용은 1000입니다. 따라서 1초 이내에 서버는 후속 요청의 속도를 제한하기 전에 이러한 두 가지 요청만 인식합니다. 자세한 내용은 [요청 단위](request-units.md)와 [요청 단위 계산기](https://www.documentdb.com/capacityplanner)를 참조하세요.

<a id="429"></a>
* **너무 큰 속도 제한/요청 속도 처리**

    클라이언트가 계정에 대해 예약된 처리량을 초과하려 할 때도 서버의 성능이 저하되거나 예약된 수준 이상의 처리량이 사용되지 않습니다. 서버에서 RequestRateTooLarge(HTTP 상태 코드 429)를 사용하여 선제적으로 요청을 종료하고, 사용자가 요청을 다시 시도할 수 있을 때까지 기다려야 하는 시간을 밀리초 단위로 표시하는 [x-ms-retry-after-ms](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) 헤더를 반환합니다.

    ```xml
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100
    ```

    SDK는 이 응답을 암시적으로 모두 catch하고, server-specified retry-after 헤더를 준수하고 요청을 다시 시도합니다. 동시에 여러 클라이언트가 계정에 액세스하지만 않으면 다음 재시도가 성공할 것입니다.

    하나 이상의 클라이언트에서 요청 속도가 점증적으로 일관되게 초과하여 작동하는 경우 클라이언트에서 현재 내부적으로 9로 설정한 기본 재시도 횟수가 충분하지 않을 수 있습니다. 이 경우 클라이언트에서 상태 코드가 429인 *CosmosClientException*을 애플리케이션에 throw합니다. 기본 재시도 횟수는 ConnectionPolicy 인스턴스에서 setRetryOptions를 설정하여 변경할 수 있습니다. 기본적으로 요청이 요청 속도 이상으로 계속 작동하는 경우 상태 코드가 429인 *CosmosClientException*은 30초의 누적 대기 시간 후에 반환됩니다. 현재 재시도 횟수가 최대 재시도 횟수보다 작은 경우에도 이러한 현상이 발생하기 때문에 기본값인 9 또는 사용자 정의 값으로 두세요.

    자동화된 재시도 동작은 대부분의 애플리케이션에 대한 복원력 및 유용성을 개선하는 데 도움이 되는 반면, 성능 벤치마크 수행 시 특히 대기 시간을 측정할 때 방해가 될 수 있습니다. 실험이 서버 제한에 도달하고 클라이언트 SDK를 자동으로 재시도하는 경우 클라이언트 관찰 대기 시간이 급증합니다. 성능 실험 중 대기 시간 급증을 방지하려면, 각 작업에 의해 반환된 비용을 측정하고 요청이 예약된 요청 속도 이하로 작동하고 있는지 확인합니다. 자세한 내용은 [요청 단위](request-units.md)를 참조하세요.

* **처리량을 높이기 위해 문서 크기를 줄이도록 설계**

    주어진 작업의 요청 비용(요청 처리 비용)은 문서 크기와 직접 관련이 있습니다. 큰 문서에서 작업하는 경우 작은 문서 작업에 비해 비용이 많이 듭니다. 항목 크기가 최대 1KB이거나 비슷한 순서 또는 크기가 되도록 애플리케이션과 워크플로를 설계하는 것이 가장 좋습니다. 대기 시간이 중요한 애플리케이션의 경우 많은 항목을 사용하지 않도록 방지합니다. 다중 MB 크기의 문서는 애플리케이션 속도를 낮춥니다.

## <a name="next-steps"></a>다음 단계

확장성 및 고성능을 위한 애플리케이션 설계 방법에 대한 자세한 내용은 [Azure Cosmos DB의 분할 및 크기 조정](partition-data.md)을 참조하세요.
