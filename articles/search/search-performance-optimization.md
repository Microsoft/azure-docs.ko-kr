---
title: 배포 전략 및 Azure Search-성능을 최적화 하기 위한 모범 사례
description: Azure Search 성능을 조정하고 최적의 크기를 구성하기 위한 기술 및 모범 사례를 알아봅니다.
author: LiamCavanagh
manager: jlembicz
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: liamca
ms.custom: seodec2018
ms.openlocfilehash: 8a07657d04cee57cb69c9f5f7862fed3e7965716
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61283577"
---
# <a name="deployment-strategies-and-best-practices-for-optimizing-performance-on-azure-search"></a>배포 전략 및 Azure Search에서 성능 최적화에 대 한 모범 사례

이 문서는 확장성 및 가용성 요구를 사용 하 여 고급 시나리오에 대 한 모범 사례를 설명합니다. 

## <a name="develop-baseline-numbers"></a>기준 수치를 개발 합니다.
검색 성능을 최적화 하는 경우에 쿼리 실행 시간을 줄이면 집중 해야 합니다. 이렇게 하려면 일반 쿼리 부하를 살펴 보았으면 해야 합니다. 다음 지침은 기준 쿼리 수에 도달할 수 있습니다.

1. 일반적인 검색 요청을 완료하는 데 걸리는 목표 대기 시간(또는 최대 시간)을 선택합니다.
2. 실제 데이터 세트를 사용하여 검색 서비스에 대한 실제 작업을 만들고 테스트하여 이러한 대기 시간 속도를 측정합니다.
3. 낮은 QPS (초당)는 쿼리 수를 사용 하 여 시작 및 쿼리 대기 시간이 정의 된 대상 대기 시간 아래로 떨어질 때까지 테스트 실행 수를 점차적으로 늘립니다. 이러한 방식은 애플리케이션 사용량이 증가함에 따라 확장을 계획하는 데 도움이 되는 중요한 벤치마크입니다.
4. 가능하면 HTTP 연결을 다시 사용합니다. Azure Search .NET SDK를 사용하는 경우 인스턴스 또는 [SearchIndexClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchindexclient) 인스턴스를 다시 사용해야 하고 REST API를 사용하는 경우에는 단일 HttpClient를 다시 사용해야 합니다.
5. 검색 하는 인덱스의 다른 부분을 통해 발생 하므로 쿼리 요청의 물질 달라 집니다. 동일한 검색 요청을 지속적으로 실행 하는 경우 데이터의 캐싱을 시작 확인이 좀 더 이질적인 쿼리를 사용 하 여 설정할 수 보다 더 나은 성능을 변형은 중요 합니다.
6. 다양 한 유형의 쿼리를 받을 수 있도록 쿼리 요청의 구조 달라 집니다. 일부 검색 쿼리는 같은 수준에서 수행합니다. 예를 들어 문서 조회 또는 검색 제안은 패싯과 필터의 상당수를 사용 하 여 쿼리를 보다 일반적으로 빠릅니다. 프로덕션 환경에서 예상한 대로 테스트 컴퍼지션 거의 동일한 비율로 다양 한 쿼리를 포함 해야 합니다.  

이러한 테스트 워크로드를 만드는 동안 다음과 같은 Azure Search의 몇 가지 특성에 유의해야 합니다.

+ 수 많은 검색 쿼리를 한 번에 푸시하여 서비스를 오버 로드 합니다. 이 경우 HTTP 503 응답 코드가 표시됩니다. 테스트 중에 503를 방지 하려면 다양 한 범위의 검색 요청을 추가 하면 대기 시간 속도의 차이점을 볼 수 있는 검색 요청을 시작 합니다.

+ Azure Search는 인덱싱 작업을 백그라운드에서 실행 되지 않습니다. 서비스에는 쿼리 및 인덱싱 작업을 동시에 처리를 하는 경우이를 고려 하거나 소개 쿼리 테스트에 인덱싱 작업 하거나 사용량이 적은 시간 동안 인덱싱 작업을 실행 하기 위한 옵션을 탐색 하 여 합니다.

> [!NOTE]
> [Visual Studio Load Testing](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)은 Azure Search에 대해 쿼리를 실행하는 데 필요한 HTTP 요청을 실행할 수 있도록 하며 요청의 병렬 처리를 사용할 수 있게 하므로 벤치마크 테스트를 수행하는 데 있어서 아주 유용한 방법입니다.
> 
> 

## <a name="scaling-for-high-query-volume-and-throttled-requests"></a>높은 쿼리 볼륨에 대 한 크기 조정 및 스로틀 된 요청
너무 많은 제한 된 요청을 받는지 하거나 쿼리 부하가 높아져서에서 사용자의 대상 대기 시간 비율을 초과할 때 두 가지 방법 중 하나로 대기 시간 속도 저하를 찾을 수 있습니다.

1. **복제본 증가:**  복제본은 데이터 복사본과 같은 것으로, Azure Search 기능은 이러한 복제본을 통해 여러 복사본에 대한 요청의 부하를 분산시킬 수 있습니다.  복제본에 대한 모든 부하 분산 및 데이터 복제 작업은 Azure Search를 통해 관리되며 언제든지 서비스에 할당되는 복제본 수를 변경할 수 있습니다.  표준 검색 서비스에서는 최대 12개, 기본 검색 서비스에서는 최대 3개의 복제본을 할당할 수 있습니다. 복제본은 [Azure Portal](search-create-service-portal.md) 또는 [PowerShell](search-manage-powershell.md)을 통해 조정할 수 있습니다.
2. **검색 계층 증가:**  Azure Search에는 [여러 계층](https://azure.microsoft.com/pricing/details/search/)이 포함되어 있으며 이러한 각 계층은 다른 수준의 성능을 제공합니다.  경우에 따라 쿼리 수가 너무 많아서 복제본이 최대 제한을 초과한 경우에도 현재 사용자가 있는 계층에서 대기 시간 속도가 충분히 낮지 않을 수 있습니다. 이 경우 많은 수의 문서 및 매우 높은 쿼리 워크 로드를 사용 하 여 시나리오에 적합 하는 Azure Search S3 계층 등의 상위 검색 계층 중 하나를 활용 하 여 고려해 야 할 수 있습니다.

## <a name="scaling-for-slow-individual-queries"></a>느린 개별 쿼리에 대 한 크기 조정
높은 대기 시간 속도 대 한 또 다른 이유는 완료 하는 데 시간이 너무 오래 단일 쿼리입니다. 이 경우 추가 복제본도 도움이 되지 않습니다. 다음을 포함 하는 데 도움이 되는 가능한 옵션 옵션 2:

1. **파티션 증가** 파티션은 추가 리소스로 데이터를 분할하는 메커니즘입니다. 데이터를 두 개로 분할 두 번째 파티션을 추가, 세 번째 파티션을 3 등으로 분할 합니다. 한 가지 양의 부작용은 느린 쿼리 경우도 수행할 것 더 빠르게 병렬 컴퓨팅으로 인해입니다. 많은 문서 또는 문서 수가 많은 대해 개수 기능을 제공 하는 패싯을 일치 하는 쿼리와 같은 선택도 낮은 쿼리 병렬화를 기록 했을 것입니다. 이후 많은 계산 문서의 관련성 점수를 매기 필요 또는 파티션을 더 추가 하는 문서 수를 계산 하려면 쿼리 더 빠르게 완료 합니다.  
   
   표준 검색 서비스의 파티션 제한은 12개이고, 기본 검색 서비스의 파티션 제한은 1개입니다.  파티션은 [Azure Portal](search-create-service-portal.md) 또는 [PowerShell](search-manage-powershell.md)을 통해 조정할 수 있습니다.

2. **높은 카디널리티 필드 제한:** 높은 카디널리티 필드는 패싯 가능 또는 많은 고유 값의 결과 계산할 때 결과적으로, 많은 리소스를 사용 하는 필터링 가능한 필드 구성 됩니다. 예를 들어, 문서 간에서 값 대부분이 고유 하므로 제품 ID 또는 설명 필드를 패싯 가능/필터링 가능으로 설정 높은 카디널리티로 계산 됩니다. 가능한 경우 높은 카디널리티 필드의 수를 제한하도록 합니다.

3. **검색 계층 증가:**  상위 Azure Search 계층으로 이동하는 것도 느린 쿼리의 성능을 향상시키는 또 다른 방법일 수 있습니다. 각 상위 계층 Cpu 및 쿼리 성능에 긍정적인 영향을 미칠 둘 다 더 많은 메모리를 더 빠르게 제공 합니다.

## <a name="scaling-for-availability"></a>가용성에 따른 크기 조정
복제본은 쿼리 대기 시간을 줄이는 데 도움이 될 뿐만 아니라 고가용성도 가능하게 합니다. 단일 복제본을 사용하는 경우 소프트웨어 업데이트 이후의 서버 다시 부팅 또는 발생하게 되는 기타 유지 관리 이벤트로 인한 주기적인 작동 중지가 예상될 수 있습니다.  결과적으로 애플리케이션이 높은 검색(쿼리) 및 쓰기(인덱싱 이벤트) 가용성을 요구하는지를 고려하는 것이 중요합니다. Azure Search는 다음 특성을 갖는 모든 유료 Search 서비스에 대해 SLA 옵션을 제공합니다.

* 읽기 전용 작업(쿼리)의 고가용성을 위한 복제본 2개
* 읽기/쓰기 작업(쿼리 및 인덱싱)의 고가용성을 위한 복제본 3개 이상

이에 대한 자세한 내용은 [Azure Search 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/search/v1_0/)을 참조하세요.

복제본 데이터의 복사본 되므로 여러 복제본 수행 컴퓨터 다시 부팅 및 유지 관리 하나의 복제본에 대해 다른 복제본에서 계속 쿼리 실행을 허용 하는 동안 Azure Search 수 있습니다. 반대로, 복제본 제거를 수행할 때 청구가 쿼리 성능 저하를 해당 복제본 된 활용도 낮은 리소스를 가정 합니다.

## <a name="scaling-for-geo-distributed-workloads-and-geo-redundancy"></a>지리적으로 분산 된 워크 로드 및 지리적 복제에 대 한 크기 조정
지리적으로 분산 된 워크 로드의 경우 Azure Search를 호스팅하는 데이터 센터 멀리 있는 사용자는 더 높은 대기 시간 속도 갖게 됩니다. 완화 이러한 사용자에 게 가까운 근접성을 사용 하 여 지역에서 여러 검색 서비스를 프로 비전 하는 것입니다. Azure Search 기능은 현재 여러 지역에 걸쳐 Azure Search 인덱스를 자동으로 지리적으로 복제하는 방법을 제공하지 않지만, 이러한 프로세스를 간단히 구현하고 관리하는 데 사용할 수 있는 몇 가지 기술이 있습니다. 이러한 기술은 다음 일부 섹션에 간단히 설명되어 있습니다.

Search 서비스의 지리적으로 분산 된 집합의 목적은 사용자가이 예제와 같이 가장 낮은 대기 시간을 제공 하는 Azure Search 서비스에 라우팅되는 위치는 두 개 이상의 지역에서 사용할 수 있는 두 개 이상의 인덱스를 할 것입니다.

   ![지역별 서비스 크로스탭][1]

### <a name="keeping-data-in-sync-across-multiple-azure-search-services"></a>여러 Azure Search 서비스에서 데이터를 동기화 상태로 유지
분산 검색 서비스를 동기화 상태로 유지하기 위한 두 가지 방법이 있습니다. 이러한 방법은 [Azure Search 인덱서](search-indexer-overview.md) 또는 푸시 API([Azure Search REST API](https://docs.microsoft.com/rest/api/searchservice/)라고도 함)를 사용합니다.  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>여러 서비스에서 콘텐츠를 업데이트 하는 것에 대 한 인덱서를 사용 합니다.

한 서비스에 인덱서를 이미 사용 하는, 하는 경우 동일한 위치에서 데이터를 끌어오는 두 번째 인덱서를 동일한 데이터 원본 개체를 사용 하기 위해 두 번째 서비스에 구성할 수 있습니다. 각 지역의 각 서비스에 자체 인덱서 및 대상 인덱스 (검색 코 퍼스를 공유 하지 않으면 즉, 데이터가 중복), 하지만 각 인덱서는 동일한 데이터 원본을 참조 합니다.

아키텍처에는 모양을 높은 수준의 시각적 개체는 다음과 같습니다.

   ![분산 인덱서 및 서비스 조합을 포함하는 단일 데이터 원본][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>여러 서비스에서 콘텐츠 업데이트를 푸시하기 위한 REST Api를 사용 하 여
Azure Search REST API를 사용 하는 경우 [Azure Search 인덱스의 콘텐츠를 푸시](https://docs.microsoft.com/rest/api/searchservice/update-index)를 유지할 수 있습니다 다양 한 검색 서비스에서 동기화 업데이트 필요할 때마다 모든 search 서비스에 변경 내용을 푸시하여 합니다. 코드에서 하나의 검색 서비스에 대 한 업데이트 실패 하지만 다른 search 서비스에 대 한 실패 한 경우를 처리 해야 합니다.

## <a name="leverage-azure-traffic-manager"></a>Azure Traffic Manager 활용
[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md)를 사용하면 여러 지역에 위치한 웹 사이트로 요청을 라우팅할 수 있습니다. 그러면 이러한 요청은 여러 Azure Search 서비스를 통해 처리될 수 있습니다. 이 Traffic Manager의 장점 중 하나는 Azure Search가 사용 가능한지 조사하고 가동 중지 시간이 발생할 경우 사용자를 대체 Search 서비스로 라우팅할 수 있다는 것입니다. 또한 Azure 웹 사이트를 통해 검색 요청을 라우팅하면, 웹 사이트는 작동되지만 Azure Search는 작동되지 않는 경우의 부하를 Azure Traffic Manager를 사용하면 분산할 수 있습니다. Traffic Manager를 활용하는 아키텍처 예는 다음과 같습니다.

   ![지역별 서비스 크로스탭(중앙 Traffic Manager 포함)][3]

## <a name="monitor-performance"></a>성능 모니터링
분석을 통해 서비스의 성능을 모니터링 하는 기능을 제공 하는 azure Search [검색 트래픽 분석](search-traffic-analytics.md)합니다. 이 기능을 사용 하 고 클라이언트 앱에 계측을 추가 하는 경우 분석을 위해 처리 하거나 Power BI에서 시각화할 수 있는 Azure Storage 계정에 집계 된 메트릭 뿐 아니라 개별 검색 작업을 기록할 필요에 따라 있습니다. 이러한 방식으로 메트릭을 캡처 평균 쿼리 또는 쿼리 응답 시간과 같은 성능 통계를 제공 합니다. 또한 작업 로깅을 사용하여 특정 검색 작업의 세부 정보를 찾아볼 수 있습니다.

트래픽 분석은 Azure Search 관점에서 대기 시간 속도 이해 하는 데 유용 합니다. 로깅된 쿼리 성능 메트릭은 Azure Search에서 쿼리가 완전히 처리되는 데 소요되는 시간(요청된 시간부터 전송된 시간까지)을 기준으로 하므로 이 값을 사용하여 대기 시간 문제가 Azure Search 서비스 쪽에서 발생한 것인지 또는 서비스 외부(예: 네트워크 대기 시간)에서 발생한 것인지 확인할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
각각의 가격 책정 계층 및 서비스 제한에 대해 자세히 알아보려면 [Azure Search의 서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

파티션 및 복제본 조합에 대해 자세히 알아보려면 [용량 계획](search-capacity-planning.md) 을 참조하세요.

성능에 대해 좀 더 드릴다운하고 이 문서에 설명된 최적화 구현 방법의 추가적인 예시를 보려면 다음 동영상을 시청하세요.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png
