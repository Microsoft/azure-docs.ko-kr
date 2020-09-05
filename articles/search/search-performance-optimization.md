---
title: 성능 확장
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 성능을 조정 하 고 최적 규모를 구성 하는 방법 및 모범 사례를 알아봅니다.
manager: nitinme
author: LiamCavanagh
ms.author: liamca
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/14/2020
ms.openlocfilehash: 5fd949466978714fe1dc0c4ccc67a3cb8f993314
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934959"
---
# <a name="scale-for-performance-on-azure-cognitive-search"></a>Azure Cognitive Search의 성능에 대 한 크기 조정

이 문서에서는 확장성 및 가용성을 위한 정교한 요구 사항이 있는 고급 시나리오에 대 한 모범 사례를 설명 합니다.

## <a name="start-with-baseline-numbers"></a>기준 번호로 시작

더 큰 배포 작업을 수행 하기 전에 일반적인 쿼리 로드 모양에 대해 알고 있어야 합니다. 다음 지침은 기준 쿼리 번호에 도달 하는 데 도움이 될 수 있습니다.

1. 일반적인 검색 요청을 완료하는 데 걸리는 목표 대기 시간(또는 최대 시간)을 선택합니다.

1. 실제 데이터 집합을 사용 하 여 검색 서비스에 대 한 실제 워크 로드를 만들고 테스트 하 여 이러한 대기 시간 비율을 측정 합니다.

1. QPS (초당 쿼리 수)로 시작 하 고 쿼리 대기 시간이 미리 정의 된 대상 아래로 떨어질 때까지 테스트에서 실행 된 수를 점차적으로 늘립니다. 이러한 방식은 애플리케이션 사용량이 증가함에 따라 확장을 계획하는 데 도움이 되는 중요한 벤치마크입니다.

1. 가능하면 HTTP 연결을 다시 사용합니다. Azure Cognitive Search .NET SDK를 사용 하는 경우 인스턴스 또는 [Searchindexclient](/dotnet/api/microsoft.azure.search.searchindexclient) 인스턴스를 다시 사용 해야 하며, REST API를 사용 하는 경우 단일 httpclient를 다시 사용 해야 합니다.

1. 인덱스의 여러 부분에 대해 검색을 수행 하도록 쿼리 요청을 다양 하 게 변경 합니다. 동일한 검색 요청을 계속 해 서 실행 하는 경우 데이터의 캐싱이 보다 개별 쿼리 집합을 사용 하는 것 보다 성능이 향상 되기 시작 하기 때문에 변형이 중요 합니다.

1. 쿼리 요청의 구조를 변경 하 여 다양 한 유형의 쿼리를 가져옵니다. 모든 검색 쿼리가 같은 수준에서 수행 되는 것은 아닙니다. 예를 들어 문서 조회 또는 검색 제안은 일반적으로 많은 수의 패싯 및 필터를 포함 하는 쿼리 보다 빠릅니다. 테스트 컴포지션에는 프로덕션에서 짐작할 수 있는 것과 거의 동일한 비율의 다양 한 쿼리가 포함 되어야 합니다.  

이러한 테스트 작업을 만드는 동안 Azure Cognitive Search의 몇 가지 특성을 염두에 두어야 합니다.

+ 한 번에 너무 많은 검색 쿼리를 푸시하여 서비스를 오버 로드할 수 있습니다. 이 경우 HTTP 503 응답 코드가 표시됩니다. 테스트 하는 동안 503을 방지 하려면 다양 한 검색 요청을 시작 하 여 더 많은 검색 요청을 추가할 때 대기 시간 비율의 차이를 확인 합니다.

+ Azure Cognitive Search은 백그라운드에서 인덱싱 작업을 실행 하지 않습니다. 서비스에서 쿼리 및 인덱싱 워크 로드를 동시에 처리 하는 경우 쿼리 테스트에 인덱싱 작업을 도입 하거나 사용량이 적은 시간에 인덱싱 작업을 실행 하기 위한 옵션을 탐색 하 여이를 고려 합니다.

> [!Tip]
> 부하 테스트 도구를 사용 하 여 현실적인 쿼리 부하를 시뮬레이션할 수 있습니다. [Azure DevOps를 사용 하 여 부하 테스트](/azure/devops/test/load-test/get-started-simple-cloud-load-test?view=azure-devops) 를 시도 하거나 다음 [대안](/azure/devops/test/load-test/overview?view=azure-devops#alternatives)중 하나를 사용 합니다.

## <a name="scale-for-high-query-volume"></a>높은 쿼리 볼륨에 대 한 크기 조정

쿼리가 너무 오래 걸리고 서비스에서 요청을 삭제 하기 시작 하는 경우 서비스는 overburdened 됩니다. 이 경우 다음 두 가지 방법 중 하나로 문제를 해결할 수 있습니다.

+ **복제본 추가**  

  각 복제본은 데이터의 복사본으로, 서비스에서 여러 복사본에 대 한 요청의 부하를 분산할 수 있도록 합니다.  모든 부하 분산 및 데이터 복제는 Azure Cognitive Search에 의해 관리 되며 언제 든 지 서비스에 할당 된 복제본 수를 변경할 수 있습니다. 표준 검색 서비스에서는 최대 12개, 기본 검색 서비스에서는 최대 3개의 복제본을 할당할 수 있습니다. 복제본은 [Azure Portal](search-create-service-portal.md) 또는 [PowerShell](search-manage-powershell.md)을 통해 조정할 수 있습니다.

+ **더 높은 계층에서 새 서비스 만들기**  

  Azure Cognitive Search는 다양 한 [계층](https://azure.microsoft.com/pricing/details/search/) 으로 제공 되며 각 계층은 다양 한 수준의 성능을 제공 합니다. 일부 경우에는 복제본이 최대값 경우에도 너무 많은 쿼리가 충분 한 시간을 제공 하지 못할 수 있습니다. 이 경우 많은 수의 문서와 매우 높은 쿼리 워크 로드를 포함 하는 시나리오를 위해 설계 된 표준 S3 계층과 같은 더 높은 성능의 계층으로 이동 하는 것이 좋습니다.

## <a name="scale-for-slow-individual-queries"></a>저속 개별 쿼리를 위한 크기 조정

대기 시간이 긴 또 다른 이유는 단일 쿼리를 완료 하는 데 너무 오래 걸립니다. 이 경우 복제본을 추가 하는 것은 도움이 되지 않습니다. 다음을 포함 하는 두 가지 옵션을 사용할 수 있습니다.

+ **파티션 늘리기**

  파티션은 추가 컴퓨팅 리소스 간에 데이터를 분할 합니다. 두 파티션은 데이터를 절반으로 분할 하 고, 세 번째 파티션은 세 번째 파티션으로 분할 합니다. 한 가지 긍정 부작용은 병렬 컴퓨팅으로 인해 느린 쿼리가 때때로 더 빠르게 수행 되는 것입니다. 많은 문서와 일치 하는 쿼리 또는 많은 문서에 대 한 개수를 제공 하는 패싯에 대해 낮은 선택이 가능한 쿼리를 병렬화 했습니다. 문서 관련성을 점수를 매기는 데 상당한 계산이 필요 하기 때문에 문서 수를 계산 하기 위해 여분의 파티션을 추가 하면 쿼리가 더 빨리 완료 됩니다.  
   
  표준 검색 서비스에는 최대 12 개의 파티션이 있을 수 있으며 기본 검색 서비스에는 1 개의 파티션이 있을 수 있습니다. 파티션은 [Azure Portal](search-create-service-portal.md) 또는 [PowerShell](search-manage-powershell.md)을 통해 조정할 수 있습니다.

+ **높은 카디널리티 필드 제한**

  높은 카디널리티 필드는 많은 수의 고유 값을 포함 하는 패싯 가능 또는 필터링 가능한 필드로 구성 되며 결과를 계산할 때 상당한 리소스를 소비 합니다. 예를 들어 패싯 가능/필터링 가능으로 제품 ID 또는 설명 필드를 설정 하는 것은 문서와 문서에 있는 대부분의 값이 고유 하기 때문에 높은 카디널리티로 계산 됩니다. 가능한 경우 높은 카디널리티 필드의 수를 제한하도록 합니다.

+ **검색 계층 증가**  

  더 높은 Azure Cognitive Search 계층으로 이동 하는 것은 속도가 느려지는 쿼리 성능을 향상 시키는 또 다른 방법일 수 있습니다. 각 상위 계층은 더 빠른 Cpu와 더 많은 메모리를 제공 하며, 둘 다 쿼리 성능에 긍정적인 영향을 줍니다.

## <a name="scale-for-availability"></a>가용성을 위한 크기 조정

복제본은 쿼리 대기 시간을 줄이는 데 도움이 될 뿐만 아니라 고가용성을 허용할 수도 있습니다. 단일 복제본을 사용하는 경우 소프트웨어 업데이트 이후의 서버 다시 부팅 또는 발생하게 되는 기타 유지 관리 이벤트로 인한 주기적인 작동 중지가 예상될 수 있습니다. 결과적으로 애플리케이션이 높은 검색(쿼리) 및 쓰기(인덱싱 이벤트) 가용성을 요구하는지를 고려하는 것이 중요합니다. Azure Cognitive Search는 다음 특성을 사용 하 여 모든 유료 검색 제품에 대 한 SLA 옵션을 제공 합니다.

+ 읽기 전용 작업(쿼리)의 고가용성을 위한 복제본 두 개

+ 읽기/쓰기 작업 (쿼리 및 인덱싱)의 고가용성을 위한 복제본 3 개 이상

이에 대 한 자세한 내용은 [Azure Cognitive Search Service Level Agreement(서비스 수준 약정)](https://azure.microsoft.com/support/legal/sla/search/v1_0/)를 참조 하세요.

복제본이 데이터의 복사본이 기 때문에 여러 복제본이 있으면 Azure Cognitive Search에서 한 복제본에 대해 컴퓨터 재부팅 및 유지 관리를 수행할 수 있습니다. 반면 쿼리 실행은 다른 복제본에서 계속 됩니다. 반대로 복제본을 사용 하지 않는 경우 해당 복제본이 미달 사용 리소스인 것으로 가정 하 여 쿼리 성능이 저하 될 수 있습니다.

## <a name="scale-for-geo-distributed-workloads-and-geo-redundancy"></a>지리적으로 분산 되는 워크 로드 및 지리적 중복성을 위한 크기 조정

지리적으로 분산 된 작업의 경우 호스트 데이터 센터에서 멀리 떨어져 있는 사용자는 더 높은 대기 시간 속도를 갖게 됩니다. 한 가지 완화 방법은 이러한 사용자에 게 더 가까운 지역에서 여러 검색 서비스를 프로 비전 하는 것입니다.

Azure Cognitive Search는 현재 지역 간에 Azure Cognitive Search 인덱스를 지리적으로 복제 하는 자동화 된 방법을 제공 하지 않지만,이 프로세스를 간단 하 게 구현 하 고 관리 하는 데 사용할 수 있는 몇 가지 기술이 있습니다. 이러한 기술은 다음 일부 섹션에 간단히 설명되어 있습니다.

검색 서비스의 지리적으로 분산 된 집합은 두 개 이상의 지역에서 두 개 이상의 인덱스를 사용할 수 있도록 하는 것입니다 .이 예제에서 볼 수 있는 것 처럼 가장 낮은 대기 시간을 제공 하는 Azure Cognitive Search 서비스로 사용자가 라우팅됩니다.

   ![지역별 서비스 크로스탭][1]

### <a name="keep-data-synchronized-across-multiple-services"></a>여러 서비스 간에 데이터를 동기화 된 상태로 유지

분산 검색 서비스를 동기화 상태로 유지 하는 두 가지 옵션이 있습니다 .이 옵션은 [azure Cognitive Search 인덱서](search-indexer-overview.md) 또는 밀어넣기 API ( [azure Cognitive Search REST API](/rest/api/searchservice/)라고도 함)를 사용 하 여 구성 됩니다.  

### <a name="use-indexers-for-updating-content-on-multiple-services"></a>여러 서비스에서 콘텐츠를 업데이트 하는 데 인덱서 사용

한 서비스에서 이미 인덱서를 사용 하 고 있는 경우 같은 데이터 원본 개체를 사용 하 여 동일한 위치에서 데이터를 끌어오는 두 번째 서비스에서 두 번째 인덱서를 구성할 수 있습니다. 각 지역의 각 서비스에는 고유한 인덱서 및 대상 인덱스가 있습니다. 검색 인덱스는 공유 되지 않으므로 (데이터가 중복 됨) 각 인덱서는 동일한 데이터 원본을 참조 합니다.

아키텍처의 모양에 대 한 개략적인 시각적 개체는 다음과 같습니다.

   ![분산 인덱서 및 서비스 조합을 포함하는 단일 데이터 원본][2]

### <a name="use-rest-apis-for-pushing-content-updates-on-multiple-services"></a>REST Api를 사용 하 여 여러 서비스에 콘텐츠 업데이트 푸시

Azure Cognitive Search REST API를 사용 하 여 [azure Cognitive Search 인덱스에 콘텐츠를 푸시하](/rest/api/searchservice/update-index)는 경우 업데이트가 필요할 때마다 모든 search 서비스에 변경 내용을 푸시하여 다양 한 검색 서비스를 동기화 된 상태로 유지할 수 있습니다. 코드에서 한 검색 서비스에 대 한 업데이트가 실패 하지만 다른 검색 서비스에 대해 성공 하는 경우를 처리 해야 합니다.

## <a name="leverage-azure-traffic-manager"></a>Azure Traffic Manager 활용

[Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) 를 사용 하면 여러 개의 검색 서비스에서 지원 되는 여러 지리적 위치 웹 사이트로 요청을 라우팅할 수 있습니다. Traffic Manager의 이점 중 하나는 Azure Cognitive Search를 검색 하 여 사용 가능한 지 확인 하 고 가동 중지 시간 발생 시 사용자를 대체 검색 서비스로 라우팅합니다. 또한 azure 웹 사이트를 통해 검색 요청을 라우팅하는 경우 Azure Traffic Manager를 사용 하 여 웹 사이트가 있지만 Azure Cognitive Search 아닌 경우의 부하를 분산할 수 있습니다. Traffic Manager를 활용하는 아키텍처 예는 다음과 같습니다.

   ![지역별 서비스 크로스탭(중앙 Traffic Manager 포함)][3]

## <a name="next-steps"></a>다음 단계

각 항목에 대 한 가격 책정 계층 및 서비스 제한에 대 한 자세한 내용은 [서비스 제한](search-limits-quotas-capacity.md)을 참조 하세요. 파티션 및 복제본 조합에 대해 자세히 알아보려면 [용량 계획](search-capacity-planning.md) 을 참조 하세요.

이 문서에서 설명 하는 기술의 성능 및 데모에 대 한 자세한 내용은 다음 동영상을 시청 하세요.

> [!VIDEO https://channel9.msdn.com/Events/Microsoft-Azure/AzureCon-2015/ACON319/player]
> 

<!--Image references-->
[1]: ./media/search-performance-optimization/geo-redundancy.png
[2]: ./media/search-performance-optimization/scale-indexers.png
[3]: ./media/search-performance-optimization/geo-search-traffic-mgr.png