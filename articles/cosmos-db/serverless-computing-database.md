---
title: 서버리스 데이터베이스 컴퓨팅 - Azure Functions 및 Azure Cosmos DB
description: Azure Cosmos DB 및 Azure Functions를 함께 사용하여 서버를 사용하지 않는 이벤트 구동 컴퓨팅 앱을 만들 수 있는 방법을 알아봅니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: sngun
ms.openlocfilehash: 4d259523d3f7fe7165d0ef4c8a5aac12bd7cd823
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330814"
---
# <a name="serverless-database-computing-using-azure-cosmos-db-and-azure-functions"></a>Azure Cosmos DB 및 Azure Functions를 사용하는 서버리스 데이터베이스 컴퓨팅

서버를 사용하지 않는 컴퓨팅을 통해 반복 가능한 상태 비저장 논리의 개별 항목에 집중할 수 있습니다. 이러한 항목에는 인프라 관리가 필요 없으며, 실행 기간(초 또는 밀리초) 동안만 리소스를 사용합니다. 서버를 사용하지 않는 컴퓨팅 이동의 핵심에는 [Azure Functions](https://azure.microsoft.com/services/functions)가 Azure 에코시스템에서 사용할 수 있는 함수가 있습니다. Azure의 다른 서버리스 실행 환경에 대해 자세히 알아보려면 [Azure의 서버리스](https://azure.microsoft.com/solutions/serverless/) 페이지를 참조하세요. 

[Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db)와 Azure Functions 간의 네이티브 통합을 사용하여 Azure Cosmos DB 계정에서 직접 데이터베이스 트리거, 입력 바인딩 및 출력 바인딩을 만들 수 있습니다. Azure Functions 및 Azure Cosmos DB를 통해 전역 사용자 기반의 풍부한 데이터에 짧은 대기 시간으로 액세스할 수 있는, 서버를 사용하지 않는 이벤트 구동 앱을 만들고 배포할 수 있습니다.

## <a name="overview"></a>개요

Azure Cosmos DB 및 Azure Functions를 사용하면 다음과 같은 방법으로 데이터베이스와 서버를 사용하지 않는 앱을 통합할 수 있습니다.

* Azure Functions에서 이벤트 구동 **Azure Cosmos DB 트리거**를 만듭니다. 이 트리거는 [변경 피드](change-feed.md) 스트림을 사용하여 Azure Cosmos DB 컨테이너에서 변경 내용을 모니터링합니다. 컨테이너를 변경하면 변경 피드 스트림이 트리거로 전송되고, 트리거가 Azure Functions를 호출합니다.
* 또는 **입력 바인딩**을 사용하여 Azure Functions를 Azure Cosmos DB 컨테이너에 바인딩합니다. 입력 바인딩은 함수가 실행될 때 컨테이너에서 데이터를 읽습니다.
* **출력 바인딩**을 사용하여 함수를 Azure Cosmos DB 컨테이너에 바인딩합니다. 출력 바인딩은 함수가 완료될 때 컨테이너에 데이터를 씁니다.

> [!NOTE]
> 현재, Azure Cosmos DB 트리거, 입력 바인딩 및 출력 바인딩은 SQL API에서만 사용할 수 있습니다. 다른 모든 Azure Cosmos DB API의 경우 API에 정적 클라이언트를 사용하여 함수에서 데이터베이스에 액세스해야 합니다.


다음 다이어그램은 다음 세 가지 통합을 각각 보여 줍니다. 

![Azure Cosmos DB 및 Azure Functions의 통합 방법](./media/serverless-computing-database/cosmos-db-azure-functions-integration.png)

Azure Cosmos DB 트리거, 입력 바인딩 및 출력 바인딩을 다음과 같은 조합으로 사용할 수 있습니다.
* Azure Cosmos DB 트리거를 다른 Azure Cosmos DB 컨테이너에 대한 출력 바인딩과 함께 사용할 수 있습니다. 함수가 변경 피드의 항목에 대해 작업을 수행하는 경우 다른 컨테이너에 쓸 수 있습니다(원본 컨테이너와 동일한 컨테이너에 쓰면 재귀 루프가 생성됨). 또는 Azure Cosmos DB 트리거를 사용하여 출력 바인딩을 통해 변경된 모든 항목을 컨테이너 간에 마이그레이션할 수 있습니다.
* Azure Cosmos DB에 대한 입력 바인딩 및 출력 바인딩을 동일한 Azure Functions에 사용할 수 있습니다. 이 기능은 입력 바인딩을 사용하여 특정 데이터를 찾고 Azure Functions에서 수정한 다음 수정 후 동일한 컨테이너 또는 다른 컨테이너에 저장하려는 경우에 효과적입니다.
* Azure Cosmos DB 컨테이너에 대한 입력 바인딩을 Azure Cosmos DB 트리거와 동일한 함수에 사용할 수 있으며, 출력 바인딩과 함께 또는 출력 바인딩 없이 사용할 수 있습니다. 이 조합을 사용하여 입력 바인딩과 함께 환율 컨테이너로 가져온 최신 통화 환율 정보를 쇼핑 카트 서비스의 새 주문 변경 피드에 적용할 수 있습니다. 출력 바인딩을 사용하여 현재 통화 변환이 적용된 업데이트된 쇼핑 카트 합계를 세 번째 컨테이너에 쓸 수 있습니다.

## <a name="use-cases"></a>사용 사례

다음 사용 사례는 데이터를 이벤트 구동 Azure Functions에 연결하여 Azure Cosmos DB 데이터를 최대한 활용할 수 있는 몇 가지 방법을 보여 줍니다.

### <a name="iot-use-case---azure-cosmos-db-trigger-and-output-binding"></a>IoT 사용 사례 - Azure Cosmos DB 트리거 및 출력 바인딩

IoT 구현에서는 검사 엔진 라이트가 커넥티드 자동차에 표시될 때 함수를 호출할 수 있습니다.

**구현:** Azure Cosmos DB 트리거 및 출력 바인딩 사용

1. **Azure Cosmos DB 트리거**는 커넥티드 자동차에서 켜지는 검사 엔진 라이트와 같이 자동차 경고와 관련된 이벤트를 트리거하는 데 사용됩니다.
2. 검사 엔진 라이트가 켜지면 센서 데이터가 Azure Cosmos DB로 전송됩니다.
3. Azure Cosmos DB가 새로운 센서 데이터 문서를 만들거나 업데이트한 다음 해당 변경 내용이 Azure Cosmos DB 트리거로 스트리밍됩니다.
4. 변경 피드를 통해 모든 변경 내용이 스트리밍되기 때문에 센서 데이터 컬렉션에 대한 데이터 변경 시마다 트리거가 호출됩니다.
5. 임계값 조건은 센서 데이터를 보증 부서에 보내기 위해 함수에 사용됩니다.
6. 또한 온도가 특정 값을 초과하는 경우 소유자에게도 경고가 전송됩니다.
7. 함수의 **출력 바인딩**은 다른 Azure Cosmos DB 컨테이너의 자동차 레코드를 업데이트하여 검사 엔진 이벤트에 대한 정보를 저장합니다.

다음 그림은 Azure Portal에서 이 트리거에 대해 기록되는 코드를 보여 줍니다.

![Azure Portal에서 Azure Cosmos DB 트리거 만들기](./media/serverless-computing-database/cosmos-db-trigger-portal.png)

### <a name="financial-use-case---timer-trigger-and-input-binding"></a>재무 사용 사례 - 타이머 트리거 및 입력 바인딩

재무 구현에서는 은행 계좌 잔액이 특정 금액 아래일 때 함수를 호출할 수 있습니다.

**구현:** Azure Cosmos DB 입력 바인딩을 사용하는 타이머 트리거

1. [타이머 트리거](../azure-functions/functions-bindings-timer.md)를 사용하여 **입력 바인딩**을 통해 일정 시간 간격마다 Azure Cosmos DB 컨테이너에 저장된 은행 계좌 잔액 정보를 검색할 수 있습니다.
2. 잔액이 사용자가 설정한 잔액 임계값 하한 아래에 있을 경우 Azure Functions에서 후속 작업을 수행합니다.
3. 출력 바인딩은 서비스 계정의 메일을 낮은 잔액 계정 각각에 대해 식별된 메일 주소로 보내는 [SendGrid 통합](../azure-functions/functions-bindings-sendgrid.md)일 수 있습니다.

다음 그림은 이 시나리오에 대한 Azure Portal의 코드를 보여 줍니다.

![재무 시나리오의 타이머 트리거에 대한 Index.js 파일](./media/serverless-computing-database/cosmos-db-functions-financial-trigger.png)

![재무 시나리오의 타이머 트리거에 대한 Run.csx 파일](./media/serverless-computing-database/azure-function-cosmos-db-trigger-run.png)

### <a name="gaming-use-case---azure-cosmos-db-trigger-and-output-binding"></a>게임 사용 사례 - Azure Cosmos DB 트리거 및 출력 바인딩

게임에서 새 사용자가 생성되면 [Azure Cosmos DB Gremlin API](graph-introduction.md)를 사용하여 알 수 있는 다른 사용자를 검색할 수 있습니다. 그런 다음 검색하기 쉽도록 [Azure Cosmos DB SQL 데이터베이스]에 결과를 쓸 수 있습니다.

**구현:** Azure Cosmos DB 트리거 및 출력 바인딩 사용

1. Azure Cosmos DB [그래프 데이터베이스](graph-introduction.md)를 사용하여 모든 사용자를 저장하면 Azure Cosmos DB 트리거로 새 함수를 만들 수 있습니다. 
2. 새 사용자를 삽입할 때마다 함수가 호출된 다음 **출력 바인딩**을 사용하여 결과가 저장됩니다.
3. 함수는 그래프 데이터베이스를 쿼리하여 새 사용자와 직접 관련된 모든 사용자를 검색하고 해당 데이터 세트를 함수에 반환합니다.
4. 이 데이터는 Azure Cosmos DB에 저장되며, 새 사용자에게 연결된 친구를 보여 주는 프런트 엔드 애플리케이션에서 쉽게 검색할 수 있습니다.

### <a name="retail-use-case---multiple-functions"></a>소매 사용 사례 - 여러 함수

소매 구현에서 사용자가 바구니에 항목을 추가할 때 이제 선택적 비즈니스 파이프라인 구성 요소에 대한 함수를 유연하게 만들고 호출할 수 있습니다.

**구현:** 하나의 컨테이너를 수신 대기하는 여러 Azure Cosmos DB 트리거

1. 각각에 Azure Cosmos DB 트리거를 추가하여 여러 Azure Functions를 만들 수 있으며, 모두 쇼핑 카트 데이터의 동일한 변경 피드를 수신 대기합니다. 여러 함수가 동일한 변경 피드를 수신 대기하면 각 함수에 새 임대 컬렉션이 필요합니다. 임대 컬렉션에 대한 자세한 내용은 [변경 피드 프로세서 라이브러리 이해](change-feed-processor.md)를 참조하세요.
2. 사용자 쇼핑 카트에 새 항목을 추가할 때마다 쇼핑 카트 컨테이너의 변경 피드를 통해 각 함수가 독립적으로 호출됩니다.
   * 한 함수는 현재 바구니의 콘텐츠를 사용하여 사용자가 관심을 가질 수 있는 다른 항목의 표시를 변경할 수 있습니다.
   * 다른 함수는 총 재고량을 업데이트할 수 있습니다.
   * 또 다른 함수는 특정 제품에 대한 고객 정보를 마케팅 부서에 보낼 수 있고, 마케팅 부서는 해당 정보를 프로모션 메일러에게 보냅니다. 

     모든 부서에서 변경 피드를 수신 대기하여 Azure Cosmos DB 트리거를 만들고, 이 트리거로 인해 프로세스의 중요한 주문 처리 이벤트가 지연되지 않을 것을 확신할 수 있습니다.

이러한 모든 사용 사례에서는 함수가 앱 자체를 분리했기 때문에 항상 새로운 앱 인스턴스를 스핀업할 필요가 없습니다. 대신, Azure Functions는 개별 함수를 스핀업하여 필요에 따라 개별 프로세스를 완료합니다.

## <a name="tooling"></a>도구

Azure Cosmos DB와 Azure Functions 간의 네이티브 통합은 Azure Portal 및 Visual Studio 2017에서 지원됩니다.

* Azure Functions Portal에서 Azure Cosmos DB 트리거를 만들 수 있습니다. 빠른 시작 지침은 [Azure Portal에서 Azure Cosmos DB 트리거 만들기](https://aka.ms/cosmosdbtriggerportalfunc)를 참조하세요.
* Azure Cosmos DB Portal에서 동일한 리소스 그룹의 기존 Azure Functions 앱에 Azure Cosmos DB 트리거를 추가할 수 있습니다.
* Visual Studio 2017에서 [Azure Functions Tools for Visual Studio 2017](../azure-functions/functions-develop-vs.md)을 사용하여 Azure Cosmos DB 트리거를 만들 수 있습니다.

    >[!VIDEO https://www.youtube.com/embed/iprndNsUeeg]

## <a name="why-choose-azure-functions-integration-for-serverless-computing"></a>서버를 사용하지 않는 컴퓨팅에 대해 Azure Functions 통합을 선택하는 이유

Azure Functions는 인프라를 프로비전 또는 관리하지 않고 요청 시 실행할 수 있는 확장 가능한 작업 단위나 간결한 논리 항목을 만들 수 있는 기능을 제공합니다. Azure Functions를 사용하면 Azure Cosmos DB 데이터베이스의 변경 내용에 응답할 완전한 앱을 만들 필요가 없으며 특정 작업에 대한 다시 사용 가능한 작은 함수를 만들 수 있습니다. 또한 HTTP 요청, 시간이 지정된 트리거 등의 이벤트에 대한 응답으로 Azure Cosmos DB 데이터를 Azure Functions에 대한 입력 또는 출력으로 사용할 수도 있습니다.

Azure Cosmos DB는 다음과 같은 이유로 서버를 사용하지 않는 컴퓨팅 아키텍처에 권장되는 데이터베이스입니다.

* **모든 데이터 즉시 액세스**: Azure Cosmos DB는 기본적으로 모든 데이터를 [자동으로 인덱싱](index-policy.md)하고 해당 인덱스를 즉시 사용할 수 있게 하므로 저장된 모든 값에 대한 세분화된 액세스 권한을 갖습니다. 따라서 데이터베이스를 지속적으로 쿼리, 업데이트 및 새 항목을 추가하고 Azure Functions를 통해 즉시 액세스할 수 있습니다.

* **스키마 사용 안 함**. Azure Cosmos DB는 스키마를 사용하지 않으므로 Azure Functions의 모든 데이터 출력을 처리할 수 있습니다. 이 “모두 처리” 방법을 사용하면 모두 Azure Cosmos DB에 출력되는 다양한 함수를 간단히 만들 수 있습니다.

* **확장 가능한 처리량**. Azure Cosmos DB에서 즉시 처리량을 확장 및 축소할 수 있습니다. 동일한 컨테이너를 쿼리하고 쓰는 수백 또는 수천 개의 함수가 있는 경우, [RU/s](request-units.md)를 확장하여 부하를 처리할 수 있습니다. 모든 함수는 할당된 RU/s를 사용하여 병렬로 작동할 수 있으며 데이터 [일관성](consistency-levels.md)이 보장됩니다.

* **글로벌 복제** Azure Cosmos DB 데이터를 [전 세계](distribute-data-globally.md)에 복제하여 대기 시간을 줄이고 사용자에게 가장 가까운 지역에 데이터를 배치할 수 있습니다. 모든 Azure Cosmos DB 쿼리와 마찬가지로, 이벤트 구동 트리거의 데이터는 사용자에게 가장 가까운 Azure Cosmos DB에서 읽은 데이터입니다.

데이터를 저장하기 위해 Azure Functions와 통합하려고 하며 심층 인덱싱이 필요 없거나 첨부 파일 및 미디어 파일을 저장해야 하는 경우 [Azure Blob Storage 트리거](../azure-functions/functions-bindings-storage-blob.md)가 더 나은 옵션일 수 있습니다.

Azure Functions의 이점: 

* **이벤트 구동**. Azure Functions는 이벤트 구동이며 Azure Cosmos DB의 변경 피드를 수신 대기할 수 있습니다. 따라서 수신 논리를 만들 필요가 없으며, 단지 수신 대기 중인 변경 내용을 확인합니다. 

* **제한 없음**. 함수는 병렬로 실행되며, 서비스가 필요한 횟수만큼 실행됩니다. 매개 변수를 설정합니다.

* **빠른 작업에 좋음**. 이벤트가 발생할 때마다 서비스가 함수의 새 인스턴스를 실행하고 함수가 완료되는 즉시 닫습니다. 함수가 실행되는 시간만큼만 요금을 지불하면 됩니다.

Flow, Logic Apps, Azure Functions 또는 WebJobs가 구현에 가장 적합한지 확실하지 않은 경우 [Flow, Logic Apps, Azure Functions 및 WebJobs 중에서 선택](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 Azure Cosmos DB와 Azure Functions를 실제로 연결하겠습니다. 

* [Azure Portal에서 Azure Cosmos DB 트리거 만들기](https://aka.ms/cosmosdbtriggerportalfunc)
* [Azure Cosmos DB 입력 바인딩을 사용하여 Azure Functions HTTP 트리거 만들기](https://aka.ms/cosmosdbinputbind)
* [Azure Cosmos DB 바인딩 및 트리거](../azure-functions/functions-bindings-cosmosdb.md)


 



