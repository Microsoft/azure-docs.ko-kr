---
title: Azure Cosmos DB .NET 변경 피드 프로세서 API, SDK 릴리스 정보
description: 릴리스 날짜, 사용 중지 날짜 및 .NET 변경 피드 프로세서 SDK의 각 버전 간 변경 내용을 포함하여 변경 피드 프로세서 API 및 SDK에 대한 모든 것을 알아봅니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/12/2020
ms.author: anfeldma
ms.openlocfilehash: 6d25eb2965e31211c0d30ec8d5e3e376176147c0
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590050"
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET 변경 피드 프로세서 SDK: 다운로드 및 릴리스 정보

> [!div class="op_single_selector"]
>
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET 변경 피드 SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.JS](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java SDK v2](sql-api-sdk-java.md)
> * [스프링 데이터 v2](sql-api-sdk-java-spring-v2.md)
> * [스프링 데이터 v3](sql-api-sdk-java-spring-v3.md)
> * [Spark 커넥터](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * 단계 (/rest/api
> * [REST 리소스 공급자] (/rest/api
> * [SQL](sql-api-query-reference.md)
> * [대량 실행자-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [대량 실행기 - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK 다운로드**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API 설명서**|[피드 프로세서 라이브러리 API 참조 문서 변경](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**시작**|[변경 피드 프로세서 .NET SDK 시작](change-feed.md)|
|**현재 지원되는 프레임워크**| [Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

> [!NOTE]
> 변경 피드 프로세서를 사용하는 경우 SDK에 변경 피드가 기본으로 제공되는 [.NET SDK](change-feed-processor.md)의 최신 3.x 버전을 참조하세요. 

## <a name="release-notes"></a>릴리스 정보

### <a name="v2-builds"></a>v2 빌드

### <a name="232"></a><a name="2.3.2"/>2.3.2
* 핫 마이그레이션 경로를 사용 하도록 설정 하는 [V3 SDK](sql-api-sdk-dotnet-standard.md) 와의 임대 저장소 호환성이 추가 되었습니다. 응용 프로그램은 V3 SDK로 마이그레이션하고 상태를 잃지 않고 변경 피드 프로세서 라이브러리로 다시 마이그레이션할 수 있습니다.

### <a name="231"></a><a name="2.3.1"/>2.3.1
* `FeedProcessing.ChangeFeedObserverCloseReason.Unknown` `FeedProcessing.IChangeFeedObserver.CloseAsync` 파티션을 찾을 수 없거나 대상 복제본이 읽기 세션에서 최신 상태가 아닌 경우 close 이유가로 전송 되는 경우를 수정 했습니다. 이러한 경우에 `FeedProcessing.ChangeFeedObserverCloseReason.ResourceGone` `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable` 는 이제 닫는 이유가 사용 됩니다.
* `FeedProcessing.ChangeFeedObserverCloseReason.ReadSessionNotAvailable`대상 복제본이 읽기 세션에서 최신 상태가 아닐 때 변경 피드 관찰자를 닫기 위해 전송 된 새로운 닫기 이유를 추가 했습니다.

### <a name="230"></a><a name="2.3.0"/>2.3.0
* 새 메서드 `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` 및 해당 공용 인터페이스 `ICheckpointPartitionProcessorFactory`가 추가되었습니다. 이를 통해 `IPartitionProcessor` 인터페이스를 구현하여 기본 제공 검사점 메커니즘을 사용할 수 있습니다. 새 팩터리는 기존 `IPartitionProcessorFactory`와 유사합니다. 단, 해당 `Create` 메서드가 `ILeaseCheckpointer` 매개 변수도 사용한다는 점이 다릅니다.
* 두 메서드 `ChangeFeedProcessorBuilder.WithPartitionProcessorFactory` 또는 `ChangeFeedProcessorBuilder.WithCheckpointPartitionProcessorFactory` 중 하나만 동일한 `ChangeFeedProcessorBuilder` 인스턴스에 사용할 수 있습니다.

### <a name="228"></a><a name="2.2.8"></a>2.2.8
* 안정성 및 진단 기능 향상:
  * 시간이 오래 걸리는 읽기 변경 피드 검색에 대한 지원이 추가되었습니다. `ChangeFeedProcessorOptions.ChangeFeedTimeout` 속성에 지정된 값보다 오래 걸리면 다음 단계가 수행됩니다.
    * 문제가 있는 파티션에서 변경 피드를 읽는 작업이 중단됩니다.
    * 변경 피드 프로세서 인스턴스는 문제가 있는 임대의 소유권을 삭제합니다. 삭제된 임대는 같거나 다른 변경 피드 프로세서 인스턴스에 의해 수행되는 다음 임대 획득 단계 중에 선택됩니다. 이러한 방식으로 변경 피드 읽기가 다시 시작됩니다.
    * 상태 모니터에 문제가 보고됩니다. 기본 상태 모니터는 보고된 모든 문제를 추적 로그에 보냅니다.
  * 새 공용 속성 `ChangeFeedProcessorOptions.ChangeFeedTimeout`이 추가되었습니다. 이 속성의 기본값은 10분입니다.
  * 새 공용 열거형 값 `Monitoring.MonitoredOperation.ReadChangeFeed`가 추가되었습니다. `HealthMonitoringRecord.Operation` 값이 `Monitoring.MonitoredOperation.ReadChangeFeed`로 설정된 경우 상태 문제가 변경 피드 읽기와 관련되어 있음을 나타냅니다.

### <a name="227"></a><a name="2.2.7"></a>2.2.7
* 모든 임대를 가져오는 시나리오에 대해 향상된 부하 분산 전략은 네트워크 문제 등으로 인해 임대 만료 간격보다 오래 걸립니다.
  * 이 시나리오에서는 임대가 만료된 것으로 간주하여 활성 소유자의 임대를 도용하는 부하 분산 알고리즘이 사용됩니다. 이로 인해 많은 임대를 불필요하게 다시 분산시킬 수 있습니다.
  * 이 릴리스에서는 소유자가 변경하지 않은 만료된 임대를 획득하는 동안 충돌 시 재시도를 방지하고 만료된 임대를 다음 부하 분산 반복으로 가져오는 것을 연기하여 이 문제를 해결했습니다.

### <a name="226"></a><a name="2.2.6"></a>2.2.6
* 관찰자 예외 처리가 향상되었습니다.
* 관찰자 오류에 대해 더욱 자세한 정보가 제공됩니다.
  * 이제는 관찰자 ProcessChangesAsync에서 throw된 예외로 인해 관찰자가 닫힐 때 ChangeFeedObserverCloseReason.ObserverError에 대한 이유 매개 변수 세트가 수신됩니다.
  * 관찰자의 사용자 코드 내에서 오류를 확인할 수 있도록 추적 기능이 추가되었습니다.

### <a name="225"></a><a name="2.2.5"></a>2.2.5
* 공유 데이터베이스 처리량을 사용하는 컬렉션으로 분할 처리에 대한 지원이 추가되었습니다.
  * 이 릴리스는 두 개가 아닌 하나의 하위 파티션 키 범위만 만들어진 파티션 부하 다시 분산으로 결과를 분할할 때 공유 데이터베이스 처리량을 사용하는 컬렉션으로 분할 중에 발생할 수 있는 문제를 해결합니다. 이 경우 변경 피드 프로세서에서 이전 파티션 키 범위의 임대 삭제가 중단되고 새 임대 만들기는 중단되지 않을 수 있습니다. 이 문제는 이 릴리스에서 해결되었습니다.

### <a name="224"></a><a name="2.2.4"></a>2.2.4
* 요청 연속 토큰에서 변경 피드 시작을 지원하기 위해 새 속성 ChangeFeedProcessorOptions.StartContinuation을 추가했습니다. 이 속성은 임대 컬렉션이 비어 있거나 임대에 ContinuationToken이 설정되어 있지 않은 경우에만 사용됩니다. ContinuationToken이 설정된 임대 컬렉션의 임대의 경우 ContinuationToken이 사용되며 ChangeFeedProcessorOptions.StartContinuation은 무시됩니다.

### <a name="223"></a><a name="2.2.3"></a>2.2.3
* 파티션당 연속 토큰을 지속하기 위해 사용자 지정 스토리지 사용을 위한 지원이 추가되었습니다.
  * 예를 들어 사용자 지정 임대 스토리지는 어떠한 사용자 지정 방식으로든 파티션된 Azure Cosmos DB 임대 컬렉션일 수 있습니다.
  * 사용자 지정 임대 스토리지는 새 확장성 지점 ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager) 및 ILeaseStoreManager 공용 인터페이스를 사용할 수 있습니다.
  * ILeaseManager 인터페이스가 여러 역할 인터페이스로 리팩터링되었습니다.
* 호환성이 손상되는 사소한 변경: 확장성 포인트 ChangeFeedProcessorBuilder.WithLeaseManager(ILeaseManager)가 제거되었습니다. 대신 ChangeFeedProcessorBuilder.WithLeaseStoreManager(ILeaseStoreManager)를 사용합니다.

### <a name="222"></a><a name="2.2.2"></a>2.2.2
* 이 릴리스는 모니터링된 컬렉션에서 분할을 처리하고 파티션된 임대 컬렉션을 사용하는 중 발생하는 문제를 해결합니다. 분할 파티션에 대한 임대 처리 시 해당 파티션에 해당하는 임대는 삭제할 수 없습니다. 이 문제는 이 릴리스에서 해결되었습니다.

### <a name="221"></a><a name="2.2.1"></a>2.2.1
* 다중 마스터 계정 및 새 세션 토큰 형식에 대한 고정 추정기 계산입니다.

### <a name="220"></a><a name="2.2.0"></a>2.2.0
* 분할된 임대 컬렉션에 대한 지원이 추가되었습니다. 파티션 키는 /id로 정의되어야 합니다.
* 주요 변경 내용: IChangeFeedDocumentClient 인터페이스 및 ChangeFeedDocumentClient 클래스의 메서드는 RequestOptions 및 CancellationToken 매개 변수를 포함하도록 변경되었습니다. IChangeFeedDocumentClient는 변경 피드 프로세서와 함께 사용하여 문서 클라이언트의 사용자 지정 구현을 제공할 수 있는 고급 확장성 지점입니다(예: DocumentClient를 데코레이트하고 그에 대한 모든 호출을 가로채서 추가 추적, 오류 처리 등을 수행). 이 업데이트를 사용하면 새 매개 변수를 구현에 포함하도록 IChangeFeedDocumentClient를 구현하는 코드를 변경해야 합니다.
* 사소한 진단 개선 사항입니다.

### <a name="210"></a><a name="2.1.0"></a>2.1.0
* 새 API인 Task&lt;IReadOnlyList&lt;RemainingPartitionWork&gt;&gt; IRemainingWorkEstimator.GetEstimatedRemainingWorkPerPartitionAsync()를 추가했습니다. 각 파티션에 대해 예상되는 작업을 구하는 데 사용할 수 있습니다.
* Microsoft.Azure.DocumentDB SDK 2.0을 지원합니다. Microsoft.Azure.DocumentDB 2.0 이상이 필요합니다.

### <a name="206"></a><a name="2.0.6"></a>2.0.6
* v1과의 호환성에 대한 ChangeFeedEventHost.HostName 공용 속성이 추가되었습니다.

### <a name="205"></a><a name="2.0.5"></a>2.0.5
* 파티션 분할 동안 발생하는 경쟁 조건을 수정했습니다. 파티션을 분할하는 동안 경쟁 조건이 임대 획득으로 이어지고 즉시 손실되어 경합을 초래할 수 있습니다. 경쟁 조건 문제는 이 릴리스에서 해결되었습니다.

### <a name="204"></a><a name="2.0.4"></a>2.0.4
* GA SDK

### <a name="203-prerelease"></a><a name="2.0.3-prerelease"></a>2.0.3-prerelease
* 다음 문제가 수정됨:
  * 파티션 분할의 경우 분할 전에 수정된 문서의 중복 처리가 있을 수 있습니다.
  * 임대 컬렉션에 임대가 없는 경우 GetEstimatedRemainingWork API가 0를 반환했습니다.

* 다음과 같은 예외를 공개합니다. IPartitionProcessor 구현이 이러한 예외를 throw할 수 있는 확장입니다.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.LeaseLostException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionException. 
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionNotFoundException.
  * Microsoft.Azure.Documents.ChangeFeedProcessor.Exceptions.PartitionSplitException. 

### <a name="202-prerelease"></a><a name="2.0.2-prerelease"></a>2.0.2 시험판
* 부 버전 API 변경 내용:
  * 사용되지 않는 것으로 표시된 ChangeFeedProcessorOptions.IsAutoCheckpointEnabled를 제거했습니다.

### <a name="201-prerelease"></a><a name="2.0.1-prerelease"></a>2.0.1-시험판
* 안정성 향상:
  * 임대 저장소 초기화를 더 효율적으로 처리합니다. 임대 저장소가 비어 있는 경우 프로세서의 한 인스턴스만 초기화할 수 있으며 다른 인스턴스는 대기합니다.
  * 더 안정적인/효율적인 임대 갱신/릴리스를 제공합니다. 하나의 파티션 임대의 갱신 및 해제는 다른 파티션의 갱신과 독립적입니다. v1에서는 모든 파티션에 대해 순차적으로 수행됩니다.
* 새 v2 API:
  * 프로세서의 유연한 구축을 위한 작성기 패턴: ChangeFeedProcessorBuilder 클래스입니다.
    * 모든 조합의 매개 변수를 사용할 수 있습니다.
    * 모니터링 및/또는 임대 컬렉션에 DocumentClient 인스턴스를 사용할 수 있습니다(v1에서 사용할 수 없음).
  * 이제 IChangeFeedObserver.ProcessChangesAsync는 CancellationToken을 사용합니다.
  * IRemainingWorkEstimator - 남은 작업 추정을 프로세서에서 별도로 사용할 수 있습니다.
  * 새 확장성 지점:
    * IPartitionLoadBalancingStrategy - 프로세서의 인스턴스 간 파티션의 사용자 지정 부하 분산용.
    * ILease, ILeaseManager - 사용자 지정 임대 관리용
    * IPartitionProcessor - 파티션에 대한 사용자 지정 처리 변경용
* 로깅 - [LibLog](https://github.com/damianh/LibLog) 라이브러리를 사용합니다.
* v1 API를 사용하여 100% 이전 버전과 호환 가능합니다.
* 새 코드 기준입니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.21.1 이상과 호환 가능합니다.

### <a name="v1-builds"></a>v1 빌드

### <a name="133"></a><a name="1.3.3"></a>1.3.3
* 자세한 로깅을 추가했습니다.
* 보류 중인 작업 예측을 여러 번 호출할 때 DocumentClient 누수를 수정했습니다.

### <a name="132"></a><a name="1.3.2"></a>1.3.2
* 보류 중인 작업 예측에서 해결합니다.

### <a name="131"></a><a name="1.3.1"></a>1.3.1
* 안정성 향상
  * 일부 파티션에서 관찰자를 중지할 수 있는 취소된 작업 문제의 처리를 수정합니다.
* 수동 검사점 설정 지원
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.21 이상과 호환

### <a name="120"></a><a name="1.2.0"></a>1.2.0
* .NET Standard 2.0에 대한 지원을 추가합니다. 이제 패키지는 `netstandard2.0` 및 `net451` 프레임워크 모니커를 지원합니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.17.0 이상과 호환 가능합니다.
* [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 버전 1.5.1 이상과 호환 가능합니다.

### <a name="111"></a><a name="1.1.1"></a>1.1.1
* 변경 피드가 비어 있거나 보류 중인 작업이 없을 때 예상되는 남은 작업을 계산할 때 발생하는 문제를 해결했습니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.13.2 이상과 호환 가능합니다.

### <a name="110"></a><a name="1.1.0"></a>1.1.0
* 변경 피드에서 처리될 나머지 작업의 추정치를 얻을 수 있는 메서드가 추가되었습니다.
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.13.2 이상과 호환 가능합니다.

### <a name="100"></a><a name="1.0.0"></a>1.0.0
* GA SDK
* [SQL .NET SDK](sql-api-sdk-dotnet.md) 버전 1.14.1 이하와 호환 가능합니다.

## <a name="release--retirement-dates"></a>릴리스 및 사용 중지 날짜

Microsoft는 매끄럽게 최신/지원 버전으로 전환할 수 있도록 적어도 SDK 사용 중지 **12개월** 전에 알림을 제공합니다. 새로운 기능 및 최적화는 현재 SDK에만 추가되어 있으며, 따라서 항상 최신 SDK 버전으로 가능한 한 빨리 업그레이드할 것을 권장합니다.

> [!WARNING]
> 2022 년 8 월 31 일 이후에는 Azure Cosmos DB에서 더 이상 버그를 수정 하지 않으며, 새 기능을 추가 하 고, Azure Cosmos DB .NET 또는 .NET Core SDK 버전의 SQL API에 대 한 지원을 제공 합니다. 업그레이드 하지 않으려는 경우 SDK의 버전 1.x에서 전송 된 요청은 Azure Cosmos DB 서비스에서 계속 제공 됩니다.

<br/>

| 버전 | 출시 날짜 | 사용 중지 날짜 |
| --- | --- | --- |
| [2.3.2](#2.3.2) |2020년 8월 11일 |--- |
| [2.3.1](#2.3.1) |2020 년 7 월 30 일 |--- |
| [2.3.0](#2.3.0) |2020년 4월 2일 |--- |
| [2.2.8](#2.2.8) |2019년 10월 28일 |--- |
| [2.2.7](#2.2.7) |2019년 5월 14일 |--- |
| [2.2.6](#2.2.6) |2019년 1월 29일 |--- |
| [2.2.5](#2.2.5) |2018년 12월 13일 |--- |
| [2.2.4](#2.2.4) |2018년 11월 29일 |--- |
| [2.2.3](#2.2.3) |2018년 11월 19일 |--- |
| [2.2.2](#2.2.2) |2018년 10월 31일 |--- |
| [2.2.1](#2.2.1) |2018년 10월 24일 |--- |
| [1.3.3](#1.3.3) |2018년 5월 8일 |--- |
| [1.3.2](#1.3.2) |2018년 4월 18일 |--- |
| [1.3.1](#1.3.1) |2018년 3월 13일 |--- |
| [1.2.0](#1.2.0) |2017년 10월 31일 |--- |
| [1.1.1](#1.1.1) |2017년 8월 29일 |--- |
| [1.1.0](#1.1.0) |2017년 8월 13일 |--- |
| [1.0.0](#1.0.0) |2017년 7월 7일 |--- |

## <a name="faq"></a>FAQ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>참고 항목

Cosmos DB에 대한 자세한 내용은 [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) 서비스 페이지를 참조하세요.
