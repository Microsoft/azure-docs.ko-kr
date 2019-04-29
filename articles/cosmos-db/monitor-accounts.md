---
title: Azure Cosmos DB에서 성능 및 스토리지 메트릭 모니터링
description: '성능 메트릭(예: 요청 및 서버 오류) 및 사용 메트릭(예: 저장소 사용)에 대해 Azure Cosmos DB 계정을 모니터링하는 방법을 알아봅니다.'
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: a12e0f567747b67c67f7bc825df1079d852e2f16
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61331002"
---
# <a name="monitor-performance-and-storage-metrics-in-azure-cosmos-db"></a>Azure Cosmos DB에서 성능 및 스토리지 메트릭 모니터링

[Azure Portal](https://portal.azure.com/)에서 Azure Cosmos DB 계정을 모니터링할 수 있습니다. 각 Azure Cosmos DB 계정의 경우 처리량, 저장소, 가용성, 대기 시간 및 일관성을 모니터링하는 데 메트릭의 전체 도구 모음을 사용할 수 있습니다.

계정 페이지, 새 메트릭 페이지 또는 Azure Monitor에서 메트릭을 검토할 수 있습니다.

## <a name="view-performance-metrics-on-the-metrics-page"></a>메트릭 페이지에서 성능 메트릭 보기
1. [Azure Portal](https://portal.azure.com/)에서 **모든 서비스**를 클릭하고, **데이터베이스**로 스크롤한 다음, **Azure Cosmos DB**를 클릭하고 성능 메트릭을 보려는 Azure Cosmos DB 계정의 이름을 클릭합니다.
2. 새 페이지가 로드되면 리소스 메뉴의 **모니터링**에서 **메트릭**을 클릭합니다.
3. 메트릭 페이지가 열리면 **컬렉션** 드롭다운에서 검토할 컬렉션을 선택합니다.

   Azure Portal에 사용할 수 있는 컬렉션 메트릭의 도구 모음이 표시됩니다. 처리량, 저장소, 가용성, 대기 시간 및 일관성 메트릭은 별도 탭에 제공됨을 유의하세요. 메트릭에 제공되는 추가 세부 정보를 가져오려면 각 메트릭 창의 오른쪽 위에 있는 이중 화살표를 클릭합니다.

   ![메트릭 도구 모음을 보여 주는 모니터링 렌즈의 스크린샷](./media/monitor-accounts/metrics-suite.png)

## <a name="view-performance-metrics-by-using-azure-monitoring"></a>Azure 모니터링을 사용하여 성능 메트릭 보기
1. [Azure Portal](https://portal.azure.com/)에서 왼쪽 표시줄에 있는 **모니터**를 클릭합니다.
2. 리소스 메뉴에서 **메트릭**을 클릭합니다.
3. **모니터 - 메트릭** 창의 **리소스 그룹** 드롭다운 메뉴에서 모니터링할 Azure Cosmos DB 계정과 연결된 리소스 그룹을 선택합니다. 
4. **리소스** 드롭 다운 메뉴에서 모니터링할 데이터베이스 계정을 선택합니다.
5. **사용 가능한 메트릭** 목록에서 표시할 메트릭을 선택합니다. 다중 선택을 하려면 CTRL 단추를 사용합니다. 

## <a name="view-performance-metrics-on-the-account-page"></a>계정 페이지에서 성능 메트릭 보기
1. [Azure Portal](https://portal.azure.com/)에서 **모든 서비스**를 클릭하고, **데이터베이스**로 스크롤한 다음, **Azure Cosmos DB**를 클릭하고 성능 메트릭을 보려는 Azure Cosmos DB 계정의 이름을 클릭합니다.
2. **모니터링** 렌즈에는 기본적으로 다음 타일이 표시됩니다.
   
   * 오늘의 총 요청 수
   * 사용된 저장소.
   
   ![요청 및 저장소 사용량을 보여 주는 모니터링 렌즈의 스크린샷](./media/monitor-accounts/documentdb-total-requests-and-usage.png)
3. **요청** 타일의 오른쪽 위에 있는 이중 화살표를 클릭하면 자세한 **메트릭** 페이지가 열립니다.
4. **메트릭** 페이지에 총 요청에 대한 세부 정보가 표시됩니다. 

## <a name="set-up-alerts-in-the-portal"></a>포털에서 경고 설정
1. [Azure Portal](https://portal.azure.com/)에서 **모든 서비스** 및 **Azure Cosmos DB**를 차례로 클릭하고 성능 메트릭 경고를 설정할 Azure Cosmos DB 계정의 이름을 클릭합니다.
2. 리소스 메뉴에서 **경고 규칙**을 클릭하여 경고 규칙 페이지를 엽니다.  
   ![선택한 경고 규칙 파트의 스크린샷](./media/monitor-accounts/madocdb10.5.png)
3. **경고 규칙** 페이지에서 **경고 추가**를 클릭합니다.  
   ![경고 추가 단추가 강조 표시된 경고 규칙 페이지의 스크린샷](./media/monitor-accounts/madocdb11.png)
4. **경고 규칙 추가** 페이지에서 다음을 지정합니다.
   
   * 설정 중인 경고 규칙의 이름
   * 새 경고 규칙에 대한 설명
   * 경고 규칙에 대한 메트릭
   * 경고가 활성화되는 시기를 결정하는 조건, 임계값 및 기간. 예를 들어 서버 오류가 지난 15분 동안 5개보다 많습니다.
   * 경고가 발생할 때 서비스 관리자 및 공동 관리자에게 메일을 보낼지 여부
   * 경고 알림에 대한 추가 메일 주소  
     ![추가 경고 규칙 페이지의 스크린샷](./media/monitor-accounts/madocdb12.png)

## <a name="monitor-azure-cosmos-db-programmatically"></a>프로그래밍 방식으로 Azure Cosmos DB 모니터링
포털에서 제공되는 계정 수준 메트릭(예: 계정 저장소 사용 및 총 요청)은 SQL API를 통해 사용할 수 없습니다. 그러나 SQL API를 사용하여 컬렉션 수준에서 사용 데이터를 검색할 수 있습니다. 컬렉션 수준 데이터를 검색하려면 다음을 수행합니다.

* REST API를 사용하려면 [컬렉션에 대해 GET을 수행](https://msdn.microsoft.com/library/mt489073.aspx)합니다. 컬렉션에 대한 할당량 및 사용량 정보는 응답의 x-ms-resource-quota 및 x-ms-resource-usage 헤더에서 반환됩니다.
* .NET SDK를 사용하려면 [DocumentClient.ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) 메서드를 사용합니다. 이 메서드는 **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage** 등의 여러 사용량 속성이 포함된 [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx)를 반환합니다.

추가 메트릭에 액세스하려면 [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights)를 사용하세요. 가용 메트릭 정의는 다음을 호출하면 검색할 수 있습니다.

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

개별 메트릭을 검색하는 쿼리는 다음 형식을 사용합니다.

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z

자세한 내용은 [Azure Monitor REST API를 통해 리소스 메트릭 검색](https://blogs.msdn.microsoft.com/cloud_solution_architect/2016/02/23/retrieving-resource-metrics-via-the-azure-insights-api/)을 참조하세요. “Azure Inights”가 “Azure Monitor”로 이름이 변경되었습니다.  이 블로그 항목에서는 이전 이름을 참조합니다.

## <a name="next-steps"></a>다음 단계
Azure Cosmos DB 용량 계획에 대한 자세한 내용은 [Azure Cosmos DB Capacity Planner 계산기](https://www.documentdb.com/capacityplanner)를 참조하세요.

