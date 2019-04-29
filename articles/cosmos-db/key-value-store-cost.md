---
title: 키 값 저장소로 Azure Cosmos DB에 대한 요청 단위 요금
description: 키/값 저장소로 사용될 경우 간단한 쓰기 및 읽기 작업의 Azure Cosmos DB의 요청 단위 요금에 대해 설명합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: e81dec7d7556819e60a65a44106426c226c6223d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61043289"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>키 값 저장소로서의 Azure Cosmos DB – 비용 개요

Azure Cosmos DB는 대규모의 고가용성 애플리케이션을 쉽게 빌드하기 위한 전역으로 분산된 다중 모델 데이터베이스 서비스입니다. 기본적으로 Azure Cosmos DB는 수집하는 모든 데이터를 자동으로 효율적으로 인덱싱합니다. 이를 통해 모든 종류의 데이터에 대해 빠르고 일관된 [SQL](how-to-sql-query.md)(및 [JavaScript](stored-procedures-triggers-udfs.md)) 쿼리가 가능해집니다. 

이 문서는 키/값 저장소로 사용될 경우 간단한 쓰기 및 읽기 작업의 Azure Cosmos DB 비용에 대해 설명합니다. 쓰기 작업에는 문서의 삽입, 바꾸기, 삭제 및 upsert가 포함됩니다. Azure Cosmos DB는 모든 단일 지역 계정과, 여유 일관성으로 구성된 모든 다중 지역 계정에 대해 99.99% 가용성 SLA를 보장하고 모든 다중 지역 데이터베이스 계정에 대해 99.999% 읽기 가용성을 보장하는 것 외에도, &lt;10-ms 읽기 대기 시간과 &lt;15-ms 쓰기(인덱싱된) 대기 시간을 보장합니다(백분위수 99). 

## <a name="why-we-use-request-units-rus"></a>RU(요청 단위)를 사용하는 이유

Azure Cosmos DB 성능은 파티션에 대해 프로비전된 RU([요청 단위](request-units.md)) 크기를 기준으로 합니다. 프로비저닝은 초 단위이며 초당 RU 단위로 구입합니다([시간별 청구와 혼동하지 말 것](https://azure.microsoft.com/pricing/details/cosmos-db/)). RU는 애플리케이션의 필수 처리량 프로비전을 간소화하는 통화로 간주되어야 합니다. 고객은 읽기 및 쓰기 용량 단위 간을 구분해서 생각할 필요가 없습니다. 단일 통화 모델의 RU를 사용하면 읽기 및 쓰기 간에 프로비전된 용량을 효율적으로 공유할 수 있습니다. 이러한 프로비전된 용량 모델을 사용하면 서비스는 짧은 대기 시간 및 높은 가용성이 보장되는 예측 가능하고 일관된 처리량을 제공할 수 있습니다. 마지막으로 RU를 사용하여 처리량을 모델링하지만 프로비전된 각 RU에는 정의된 양의 리소스(메모리, 코어)가 있습니다. 초당 RU는 IOPS만이 아닙니다.

전역적으로 분산된 데이터베이스 시스템인 Cosmos DB는 고가용성 외에 대기 시간, 처리량 및 일관성에 대한 SLA를 제공하는 유일한 Azure 서비스입니다. 프로비전하는 처리량은 Cosmos DB 데이터베이스 계정에 연결된 각 하위 지역에 적용됩니다. 읽기의 경우 Cosmos DB는 선택 가능한 여러 개의 잘 정의된 [일관성 수준](consistency-levels.md)을 제공합니다. 

다음 표에는 1KB 및 100KB 문서 크기에 따라 읽기 및 쓰기 트랜잭션을 수행하는 데 필요한 RU 수가 나와 있습니다.

|항목 크기|1 읽기|1 쓰기|
|-------------|------|-------|
|1KB|1RU|5RU|
|100KB|10RU|50RU|

## <a name="cost-of-reads-and-writes"></a>읽기 및 쓰기의 비용

초당 1,000RU를 프로비전하는 시간당 3.6m RU에 해당하며 시간당 $0.08가 됩니다(미국 및 유럽). 1KB 크기 문서에서는 프로비전된 처리량을 사용해서 3.6m 읽기 또는 0.72m 쓰기(초당 3.6m RU/5)를 사용할 수 있음을 의미합니다. 1백만 읽기 및 쓰기로 정규화할 경우 비용은 m 읽기당 $0.022($0.08/3.6) 및 m 쓰기당 $0.111($0.08/0.72)에 해당합니다. 백만당 비용은 아래 표에 나와 있는 것처럼 최소화됩니다.

|항목 크기|1-m 읽기|1m 쓰기|
|-------------|-------|--------|
|1KB|$0.022|$0.111|
|100KB|$0.222|$1.111|


대부분의 기본 Blob 또는 개체 저장소 서비스는 백만 읽기 트랜잭션당 $0.40, 백만 쓰기 트랜잭션당 $5가 부과됩니다. 최적으로 사용하는 경우 Cosmos DB는 이러한 다른 솔루션보다 최대 98% 더 저렴할 수 있습니다(1KB 트랜잭션에 대해).

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB 리소스 프로비저닝 최적화에 대한 새 문서를 계속 확인하세요. 당분간은 [RU 계산기](https://www.documentdb.com/capacityplanner)를 무료로 사용하세요.

