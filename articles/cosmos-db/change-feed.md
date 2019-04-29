---
title: Azure Cosmos DB에서 변경 피드 지원 사용
description: Azure Cosmos DB의 변경 피드 지원을 사용하여 문서에서 변경 내용을 추적하고 트리거와 마찬가지로 이벤트 기반 처리를 수행하고 캐시 및 분석 시스템을 최신 상태로 유지합니다.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 85a1dad9feb15550cf27cf032802af5055fdf155
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60898397"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Azure Cosmos DB의 변경 피드 - 개요

Azure Cosmos DB의 변경 피드 지원은 모든 변경 사항에 대해 Azure Cosmos DB 컨테이너를 수신하여 작동합니다. 그런 다음 변경된 문서가 수정된 순서로 정렬된 목록이 출력됩니다. 변경 사항은 지속적이며, 비동기적 및 증분적으로 처리할 수 있고 출력을 하나 이상의 소비자 사이에 분산하여 병렬 처리가 가능합니다. 

Azure Cosmos DB는 IoT, 게임, 소매 및 운영 로깅 애플리케이션에 적합합니다. 이러한 애플리케이션에서 일반적인 설계 패턴은 데이터 변경 사항을 사용하여 추가 작업을 트리거하는 것입니다. 추가 작업의 예제는 다음을 포함합니다.

* 항목이 삽입 또는 업데이트되는 경우 알림 또는 API에 대한 호출 트리거
* IoT에 대한 실시간 스트림 처리 또는 운영 데이터에 대한 실시간 분석 처리
* 캐시 또는 검색 엔진 또는 데이터 웨어하우스와 동기화하거나 데이터를 콜드 스토리지로 아카이브하여 추가 데이터 이동

Azure Cosmos DB의 변경 피드를 사용하면 다음 그림과 같이 이러한 패턴 각각에 대해 효율적이고 확장 가능한 솔루션을 구축할 수 있습니다.

![Azure Cosmos DB 변경 피드를 사용하여 실시간 분석 및 이벤트 기반 컴퓨팅 시나리오 작동](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>지원되는 API 및 클라이언트 SDK

이 기능은 현재 다음 Azure Cosmos DB API 및 클라이언트 SDK에서 지원됩니다.

| **클라이언트 드라이버** | **Azure CLI** | **SQL API** | **Cassandra API** | **Azure Cosmos DB의 MongoDB API** | **Gremlin API**|**Table API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | 해당 없음 | 예 | 아니오 | 아니요 | 사용자 계정 컨트롤 | 아닙니다. |
|자바|해당 없음|예|아니오|아니요|사용자 계정 컨트롤|아닙니다.|
|Python|해당 없음|예|아니오|아니요|사용자 계정 컨트롤|아닙니다.|
|Node/JS|해당 없음|예|아니오|아니요|사용자 계정 컨트롤|아닙니다.|

## <a name="change-feed-and-different-operations"></a>변경 피드 및 다양한 작업

현재 변경 피드에서 모든 작업이 표시됩니다. 업데이트만하고 삽입하지 않는 것과 같은 특정 작업에 대해 변경 피드를 제어할 수 있는 기능은 아직 사용할 수 없습니다. 업데이트에 대한 항목에 "소프트 표식"을 추가하고 변경 피드에서 항목을 처리할 때 이를 기준으로 필터링할 수 있습니다. 현재 변경 피드는 삭제를 기록하지 않습니다. 이전 예제와 마찬가지로 삭제되는 항목에 소프트 표식을 추가할 수 있습니다. 예를 들어 자동으로 삭제될 수 있도록 "deleted"라는 항목에 특성을 추가하고 "true"로 설정하고 항목에 TTL을 설정할 수 있습니다. 기록 항목에 대한 변경 피드를 읽을 수 있습니다(예: 5년 전에 추가된 항목). 항목이 삭제되지 않은 경우 컨테이너의 원본인 한, 변경 피드를 읽을 수 있습니다.

### <a name="sort-order-of-items-in-change-feed"></a>변경 피드에서 항목의 정렬 순서

변경 피드 항목은 수정 시간 순서대로 도착합니다. 이 정렬 순서는 논리 파티션 키 단위로 보장됩니다.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>다중 지역 Azure Cosmos 계정의 변경 피드

다중 지역 Azure Cosmos 계정에서 쓰기 지역이 장애 조치(failover)되는 경우 변경 피드는 수동 장애 조치(failover) 작업에서 작동하고 계속 인접합니다.

### <a name="change-feed-and-time-to-live-ttl"></a>변경 피드 및 TTL(Time to Live)

항목에서 TTL(Time to Live) 속성이 -1로 설정된 경우 변경 피드는 영원히 유지됩니다. 데이터를 삭제하지 않는 이상, 데이터는 변경 피드에 계속 남아 있습니다.  

### <a name="change-feed-and-etag-lsn-or-ts"></a>변경 피드 및 _etag, _lsn 또는 _ts

_etag 형식은 내부적이며 언제든지 변경될 수 있으므로 의존하면 안 됩니다. _ts는 수정 또는 생성 타임스탬프입니다. _ts를 시간순 비교에 사용할 수 있습니다. _lsn은 변경 피드만;에 대 한 추가 되는 일괄 처리 ID 트랜잭션 ID를 나타내므로 많은 항목에는 동일한 _lsn이 있을 수 있습니다. FeedResponse의 ETag는 항목에 표시된 _etag와 다릅니다. _etag는 내부 식별자이며 항목의 버전에 대해 알려주는 동시성 컨트롤에 사용되는 반면 ETag는 피드 시퀀싱에 사용됩니다.

## <a name="change-feed-use-cases-and-scenarios"></a>변경 피드 사용 사례 및 시나리오

변경 피드를 통해 대량의 쓰기가 있는 큰 데이터 세트를 효율적으로 처리할 수 있습니다. 변경 피드는 또한 변경된 내용을 식별하기 위해 전체 데이터 세트를 쿼리하는 대안을 제공합니다.

### <a name="use-cases"></a>사용 사례

예를 들어 변경 피드를 사용하여 다음 작업을 효율적으로 수행할 수 있습니다.

* Azure Cosmos DB에 저장된 데이터를 사용하여 캐시, 검색 인덱스 또는 데이터 웨어하우스를 업데이트합니다.

* 애플리케이션 수준 데이터 계층 및 보관을 구현합니다. 예를 들어 Azure Cosmos DB에 "핫 데이터"를 저장하고 "콜드 데이터"를 다른 스토리지 시스템(예: [Azure Blob Storage](../storage/common/storage-introduction.md))으로 내보냅니다.

* 다른 논리 파티션 키를 사용하여 다른 Azure Cosmos 계정 또는 다른 Azure Cosmos 컨테이너에 대한 중단 시간이 없는 마이그레이션을 수행합니다.

* Azure Cosmos DB를 사용하여 [람다 아키텍처](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/)를 구현합니다. 여기서 Azure Cosmos DB는 실시간, 일괄 처리 및 쿼리 서비스 계층을 지원하므로 낮은 TCO로 람다 아키텍처를 사용할 수 있습니다.

* 디바이스, 센서, 인프라 및 애플리케이션에서 이벤트 데이터를 수신하고 저장하며, [Spark](../hdinsight/spark/apache-spark-overview.md)를 사용하여 이러한 이벤트를 실시간으로 처리합니다.  다름 이미지에서는 변경 피드를 통해 Azure Cosmos DB를 사용하여 람다 아키텍처를 구현하는 방법을 보여줍니다.

![수집 및 쿼리를 위한 Azure Cosmos DB 기반 lambda 파이프라인](./media/change-feed/lambda.png)

### <a name="scenarios"></a>시나리오

다음은 변경 피드를 사용하여 쉽게 구현할 수 있는 일부 시나리오입니다.

* [서버를 사용하지 않는](https://azure.microsoft.com/solutions/serverless/) 웹 또는 모바일 앱 내에서는 고객의 프로필, 기본 설정 또는 해당 위치에 대한 모든 변경 내용과 같은 이벤트를 추적하고 특정 작업(예: [Azure Functions](change-feed-functions.md)를 사용하여 해당 디바이스에 푸시 알림 보내기)을 트리거할 수 있습니다.

* 예를 들어, Azure Cosmos DB를 사용하여 게임을 빌드하는 경우 변경 피드를 사용하여 완료된 게임의 점수에 따라 실시간 순위표를 구현할 수 있습니다.


## <a name="working-with-change-feed"></a>변경 피드를 사용하여 작업

다음 옵션을 통해 변경 피드를 사용하여 작업할 수 있습니다.

* [Azure Functions와 함께 변경 피드 사용](change-feed-functions.md)
* [변경 피드 프로세서 라이브러리와 함께 변경 피드 사용](change-feed-processor.md) 

변경 피드는 컨테이너 내에서 각 논리 파티션 키에 대해 사용할 수 있으며, 아래 이미지와 같이 한 명 이상의 소비자에게 배포하여 병렬 처리할 수 있습니다.

![Azure Cosmos DB 변경 피드의 분산 처리](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>변경 피드의 기능

* 변경 피드는 모든 Azure Cosmos 계정에 기본적으로 사용됩니다.

* Azure Cosmos 데이터베이스와 연결된 모든 지역에서 [프로비전된 처리량](request-units.md)을 사용하여 Azure Cosmos DB의 다른 작업과 마찬가지로 변경 피드에서 읽을 수 있습니다.

* 변경 피드는 컨테이너 내의 항목에 수행된 삽입 및 업데이트 작업을 포함합니다. 삭제 대신 항목(예: 문서) 내에서 "soft-delete" 플래그를 설정하여 삭제를 캡처할 수 있습니다. 또는 [TTL 기능](time-to-live.md)을 사용하여 항목에 대한 제한된 만료 기간을 설정할 수 있습니다. 예를 들어 24시간 및 삭제를 캡처하는 해당 속성의 값을 사용합니다. 이 솔루션을 사용하여 TTL 만료 기간보다 짧은 시간 간격 내에 변경 내용을 처리해야 합니다. 

* 각 항목에 대한 변경 내용이 정확히 한 번 변경 피드에 표시되고, 클라이언트는 검사점 논리를 관리해야 합니다. 검사점 관리의 복잡성을 방지하려는 경우 변경 피드 프로세서 라이브러리는 자동 검사점 및 “최소 한 번” 의미 체계를 제공합니다. [변경 피드 프로세서 라이브러리와 함께 변경 피드 사용](change-feed-processor.md)을 참조하세요.

* 지정된 항목에 대한 가장 최근의 변경 내용만이 변경 로그에 포함됩니다. 중간 변경 내용을 사용할 수 없습니다.

* 변경 피드는 각 논리 파티션 키 값 내에서 수정된 순서로 정렬됩니다. 파티션 키 값에 보장된 순서가 없습니다.

* 특정 시점에서 변경 내용을 동기화할 수 있습니다. 즉, 변경 내용을 사용할 수 있는 고정 데이터 보존 기간이 없습니다.

* Azure Cosmos 컨테이너의 모든 논리 파티션 키에 대해 변경 내용을 동시에 사용할 수 있습니다. 이 기능을 사용하면 대규모 컨테이너의 변경 내용을 여러 소비자에 의해 병렬로 처리할 수 있습니다.

* 응용 프로그램에서 동일한 컨테이너에 여러 변경 피드를 동시에 요청할 수 있습니다. 초기 시작점을 제공하기 위해 ChangeFeedOptions.StartTime을 사용할 수 있습니다. 예를 들어 지정된 클록 시간에 해당하는 연속 토큰을 찾을 수 있습니다. ContinuationToken을 지정할 경우 이것이 StartTime 및 StartFromBeginning 값보다 우선합니다. ChangeFeedOptions.StartTime의 정확도는 5초입니다. 

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드에 대해 자세히 알아볼 수 있습니다.

* [변경 피드를 읽는 옵션](read-change-feed.md)
* [Azure Functions와 함께 변경 피드 사용](change-feed-functions.md)
* [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)
