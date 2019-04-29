---
title: Azure Cosmos DB에서 읽기 및 쓰기 비용 최적화
description: 이 문서에서는 데이터에 대해 읽기 및 쓰기 작업을 수행할 때 Azure Cosmos DB 비용을 절감하는 방법을 설명합니다.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: rimman
ms.openlocfilehash: b6c5722d5e096300f76f60dfaf8bab1e07d0c61c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61330206"
---
# <a name="optimize-reads-and-writes-cost-in-azure-cosmos-db"></a>Azure Cosmos DB에서 읽기 및 쓰기 비용 최적화

이 문서에서는 Azure Cosmos DB에서 데이터를 읽고 쓰는 데 필요한 비용이 계산되는 방식을 설명합니다. 읽기 작업에는 항목에 대한 가져오기 작업이 포함되고, 쓰기 작업에는 항목의 삽입, 바꾸기, 삭제 및 업데이트/삽입이 포함됩니다.  

## <a name="cost-of-reads-and-writes"></a>읽기 및 쓰기의 비용

Azure Cosmos DB는 프로비전된 처리량 모델을 사용하여 처리량 및 대기 시간 측면에서 예측 가능한 성능을 보장합니다. 프로비전된 처리량은 초당 [요청 단위](request-units.md) 또는 RU/초로 표시됩니다. RU(요청 단위)는 CPU, 메모리, IO 등과 같이 요청을 수행하는 데 필요한 컴퓨팅 리소스에 대한 논리적 추상화입니다. 프로비전된 처리량(RU)은 따로 구분되며, 예측 가능한 처리량 및 대기 시간을 제공하기 위해 컨테이너 또는 데이터베이스에 전용으로 사용됩니다. 프로비전된 처리량을 사용하면 Azure Cosmos DB는 규모에 관계없이, 예측 가능하고 일관된 성능, 보장된 낮은 대기 시간 및 고가용성을 제공할 수 있습니다. 요청 단위는 애플리케이션에 필요한 리소스의 수를 보다 간편하게 파악할 수 있도록 하는 정규화된 통화를 나타냅니다. 

읽기와 쓰기 간에 요청 단위를 구분할 필요는 없습니다. 요청 단위에 대해 통합된 통화 모델을 사용하면 일기 및 쓰기 둘 다에 대해 동일한 처리량 용량을 서로 교환해서 사용할 수 있으므로 효율적입니다. 다음 표에서는 크기가 1KB 및 100KB인 항목에 대한 읽기 및 쓰기 비용을 보여줍니다.

|**항목 크기**  |**단일 읽기 비용** |**단일 쓰기 비용**|
|---------|---------|---------|
|1KB |1RU |5RU |
|100KB |10RU |50RU |

크기가 1KB인 단일 항목을 읽는 비용은 1RU입니다. 크기가 1KB인 단일 항목을 쓰는 비용은 5RU입니다. 기본 세션 [일관성 수준](consistency-levels.md)을 사용하는 경우 읽기 및 쓰기 비용이 적용됩니다.  RU에 대해 항목 크기, 속성 수, 데이터 일관성, 인덱싱된 속성, 인덱싱 및 쿼리 패턴이 고려됩니다.

## <a name="normalized-cost-for-1-million-reads-and-writes"></a>1백만 개의 읽기 및 쓰기에 대한 정규화된 비용

초당 1,000RU를 프로비전하는 것은 시간당 360만RU에 해당하며 시간당 $0.08가 됩니다(미국 및 유럽). 1KB 항목의 경우 이러한 프로비전된 처리량으로 시간당 360만 개 읽기 또는 72만 개 쓰기를 수행할 수 있습니다(이 값은 `3.6 million RU / 5`로 계산됨). 1백만 읽기 및 쓰기로 정규화된 요금은 1백만 읽기에 대해 $0.022(이 값은 $0.08/360만으로 계산됨) 및 1백만 쓰기에 대해 $0.111(이 값은 $0.08/72만으로 계산됨)이 됩니다.

## <a name="number-of-regions-and-the-request-units-cost"></a>지역 수 및 요청 단위 비용

쓰기 비용은 Azure Cosmos 계정과 연결된 지역 수에 관계없이 일정합니다. 즉, 1KB 쓰기는 계정과 연결된 지역 수에 관계없이 5RU 요금이 청구됩니다. 모든 지역에서 복제, 수락 및 복제 트래픽 처리를 위해 많은 양의 리소스가 사용됩니다. 다중 지역의 비용 최적화에 대한 자세한 내용은 [다중 지역 Cosmos 계정의 비용 최적화](optimize-cost-regions.md) 문서를 참조하세요.

## <a name="optimize-the-cost-of-writes-and-reads"></a>읽기 및 쓰기 비용 최적화

쓰기 작업을 수행하면 필요한 초당 쓰기 수를 지원하기 위한 충분한 용량을 프로비전해야 합니다. 쓰기를 수행하기 전에 SDK, 포털, CLI를 사용하여 프로비전된 처리량을 늘린 다음, 쓰기가 완료된 후에 처리량을 줄일 수 있습니다. 쓰기 기간의 처리량은 지정된 데이터에 필요한 최소 처리량에 삽입 워크로드에 필요한 처리량을 더한 것입니다. 이때 실행되는 다른 워크로드는 없다고 가정합니다. 

다른 워크로드(예: 쿼리/읽기/업데이트/삭제)를 동시에 실행 중인 경우 이러한 작업에 필요한 요청 단위도 추가해야 합니다. 쓰기 작업의 속도가 제한된 경우 Azure Cosmos DB SDK를 사용하여 다시 시도/백오프 정책을 사용자 지정할 수 있습니다. 예를 들어 작은 비율의 요청에 대해 속도가 제한될 때까지 부하를 늘릴 수 있습니다. 속도가 제한될 경우, 클라이언트 애플리케이션은 지정된 다시 시도 간격 동안 속도 제한 요청에 대해 백오프를 수행해야 합니다. 쓰기를 다시 시도하기 전에 최소한의 간격을 두고 다시 시도해야 합니다. 다시 시도 정책 지원은 SQL.NET, Java, Node.js 및 Python SDK와 지원되는 모든 버전의 .NET Core SDK에 포함됩니다. 

[Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)를 사용하여 Azure Cosmos DB에 데이터를 대량으로 삽입하거나 지원되는 원본 데이터 저장소의 데이터를 Azure Cosmos DB로 복사할 수도 있습니다. Azure Data Factory는 데이터를 쓸 때 최상의 성능을 제공하기 위해 Azure Cosmos DB 일괄 처리 API와 기본적으로 통합됩니다.

## <a name="next-steps"></a>다음 단계

이제 다음 문서를 사용하여 Azure Cosmos DB의 비용 최적화에 대해 좀 더 자세히 알아볼 수 있습니다.

* [개발 및 테스트용으로 최적화](optimize-dev-test.md)에 대한 자세한 정보
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
* [처리량 비용 최적화](optimize-cost-throughput.md)에 대한 자세한 정보
* [스토리지 비용 최적화](optimize-cost-storage.md)에 대한 자세한 정보
* [쿼리 비용 최적화](optimize-cost-queries.md)에 대한 자세한 정보
* [다중 지역 Azure Cosmos 계정 비용 최적화](optimize-cost-regions.md)에 대한 자세한 정보
