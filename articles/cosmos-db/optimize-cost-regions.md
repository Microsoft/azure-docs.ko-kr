---
title: Azure Cosmos DB에서 다중 지역 배포에 대해 비용 최적화
description: 이 문서에서는 Azure Cosmos DB에서 다중 지역 배포 비용을 관리하는 방법을 설명합니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: 012eacb172acfdeb0b82343c484c664a3f75310e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60929307"
---
# <a name="optimize-multi-region-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 여러 지역 비용 최적화

언제든지 Azure Cosmos 계정에서 지역을 추가 및 제거할 수 있습니다. 다양한 Azure Cosmos 데이터베이스 및 컨테이너에 대해 구성하는 처리량은 계정과 연결된 각 지역에 예약됩니다. Azure Cosmos 계정의 모든 데이터베이스 및 컨테이너에서 구성된 RU/초의 합계인 시간당 프로비전된 처리량이 `T`이고, 데이터베이스 계정과 연결된 Azure 지역의 수가 `N`이면 지정된 시간에 대해 Cosmos 계정의 총 프로비전된 처리량은 다음과 같습니다.

1. Azure Cosmos 계정의 단일 쓰기 지역으로 구성된 경우 `T x N RU/s` 

1. Azure Cosmos 계정이 쓰기를 처리할 수 있는 모든 지역으로 구성된 경우 `T x (N+1) RU/s` 

단일 쓰기 지역으로 프로비전된 처리량 비용은 100RU당 $0.008/시간이며, 여러 쓰기 가능 지역으로 프로비전된 처리량 비용은 100RU당 $0.016/시간입니다. 자세한 내용은 Azure Cosmos DB [가격 책정 페이지](https://azure.microsoft.com/pricing/details/cosmos-db/)를 참조하세요.

## <a name="costs-for-multiple-write-regions"></a>여러 쓰기 지역에 대한 비용

다중 마스터 시스템에서 쓰기 작업에 대해 사용 가능한 순 RU는 `N`배만큼 증가합니다. 여기서 `N`은 쓰기 지역 수입니다. 단일 지역 쓰기와 달리, 모든 지역은 이제 쓰기 가능하며 충돌 해결을 지원해야 합니다. 작성자의 워크로드 양도 증가했습니다. 관점을 수행 하려면 계획 비용 으로부터 `M` M 프로 비전 해야 RU/s 동안의 쓰기 전 세계를 `RUs` 컨테이너 또는 데이터베이스 수준. 원하는 수의 지역을 추가하고 쓰기에 사용하여 전 세계적으로 `M` RU에 상당하는 쓰기를 수행할 수 있습니다. 

### <a name="example"></a>예

처리량 10KRU/s로 프로비전된 컨테이너가 미국 서부에 있고 이번 달에 1TB 데이터를 저장한다고 가정합니다. 각각 스토리지와 처리량이 동일한 3개 지역(미국 동부, 북유럽 및 동아시아)을 추가하고, 전역 분산형 앱에서 모든 지역의 컨테이너에 쓰려고 한다고 가정합니다. 총 월간 청구 금액은 다음과 같습니다(1개월은 31일로 가정).

|**항목**|**사용량(월간)**|**비용**|**월간 비용**|
|----|----|----|----|
|미국 서부의 컨테이너에 대한 처리량 청구(다중 쓰기 지역) |10K RU/s * 24 * 31 |100RU당 $0.016/시간 |$1,190.40 |
|3개 추가 지역인 미국 동부, 북유럽 및 동아시아에 대한 처리량 청구(다중 쓰기 지역) |(3 + 1) * 10K RU/s * 24 * 31 |100RU당 $0.016/시간 |$4,761.60 |
|미국 서부의 컨테이너에 대한 스토리지 청구 |100GB |$0.25/GB |$25 |
|3개 추가 지역인 미국 동부, 북유럽 및 동아시아에 대한 스토리지 청구 |3 * 1TB |$0.25/GB |$75 |
|**합계**|||**$6,052** |

## <a name="improve-throughput-utilization-on-a-per-region-basis"></a>지역별 처리량 사용률 개선

사용률이 비효율적인 경우(예: 하나 이상의 지역의 처리량 저하 또는 과다) 다음 단계에 따라 처리량 사용률을 개선할 수 있습니다.  

1. 먼저 쓰기 지역에서 프로비전된 처리량(RU)을 최적화한 후, 쓰기 지역의 변경 피드를 사용하여 쓰기 지역의 RU를 최대한 활용합니다. 

2. Azure Cosmos 계정과 연결된 모든 지역에 걸쳐 다중 쓰기 지역 읽기 및 쓰기를 확장할 수 있습니다. 

3. 지역의 작업을 모니터링하고, 읽기 및 쓰기 처리량 확장 요구가 있을 때 지역을 추가 및 제거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 사용하여 Azure Cosmos DB의 비용 최적화에 대해 좀 더 자세히 알아볼 수 있습니다.

* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [읽기 및 쓰기 비용 최적화](optimize-cost-reads-writes.md)에 대한 자세한 정보
* [쿼리 비용 최적화](optimize-cost-queries.md)에 대한 자세한 정보

