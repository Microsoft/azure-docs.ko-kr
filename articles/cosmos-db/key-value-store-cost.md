---
title: 키 값 저장소로 Azure Cosmos DB에 대한 요청 단위 요금
description: 키/값 저장소로 사용될 경우 간단한 쓰기 및 읽기 작업의 Azure Cosmos DB의 요청 단위 요금에 대해 설명합니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: 3a79db11ff05bcc9d18619c7f508a9864c17c3b8
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012805"
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>키 값 저장소로 Azure Cosmos DB-비용 개요

Azure Cosmos DB는 대규모의 고가용성 애플리케이션을 쉽게 빌드하기 위한 전역으로 분산된 다중 모델 데이터베이스 서비스입니다. 기본적으로 Azure Cosmos DB는 수집 모든 데이터를 자동으로 효율적으로 인덱싱합니다. 이를 통해 데이터에 대해 빠르고 일관 된 [SQL](how-to-sql-query.md) (및 [JavaScript](stored-procedures-triggers-udfs.md)) 쿼리를 수행할 수 있습니다. 

이 문서는 키/값 저장소로 사용될 경우 간단한 쓰기 및 읽기 작업의 Azure Cosmos DB 비용에 대해 설명합니다. 쓰기 작업에는 데이터 항목의 삽입, 바꾸기, 삭제 및 upsert 포함 됩니다. Azure Cosmos DB은 모든 다중 지역 계정에 대해 99.999%의 가용성 SLA를 보장 하는 것 외에도 99 번째 백분위 수에서 읽기 및 (인덱싱된) 쓰기에 대해 10 밀리초의 대기 시간을 < 보장 합니다. 

## <a name="why-we-use-request-units-rus"></a>RU(요청 단위)를 사용하는 이유

Azure Cosmos DB 성능은 [요청 단위](request-units.md) (r u/초)로 표현 된 프로 비전 된 처리량의 양을 기반으로 합니다. 프로 비전은 두 번째 세분성으로 제공 되며 r u/초 단위로 구매 됩니다 ([시간당 청구와 혼동 하지 않음](https://azure.microsoft.com/pricing/details/cosmos-db/)). RUs는 응용 프로그램에 필요한 처리량의 프로 비전을 간소화 하는 논리적 추상화 (통화)로 간주 되어야 합니다. 사용자는 읽기 및 쓰기 처리량을 차별화 한다고 생각할 필요가 없습니다. 단일 통화 모델의 RU를 사용하면 읽기 및 쓰기 간에 프로비전된 용량을 효율적으로 공유할 수 있습니다. 이 프로 비전 된 용량 모델을 사용 하면 서비스에서 **예측 가능 하 고 일관성 있는 처리량을 보장 하며 낮은 대기 시간 및 고가용성**을 제공할 수 있습니다. 마지막으로, 및 모델을 사용 하 여 처리량을 나타냅니다. 프로 비전 된 각 항목에는 정의 된 양의 리소스 (예: 메모리, 코어/CPU 및 IOPS)가 있습니다.

전역적으로 분산 된 데이터베이스 시스템으로 Cosmos DB는 대기 시간, 처리량, 일관성 및 고가용성을 포괄 하는 포괄적인 Sla를 제공 하는 유일한 Azure 서비스입니다. 프로 비전 하는 처리량은 Cosmos 계정과 연결 된 각 지역에 적용 됩니다. 읽기의 경우 Cosmos DB는 선택 가능한 여러 개의 잘 정의된 [일관성 수준](consistency-levels.md)을 제공합니다. 

다음 표에서는 크기 1kb 및 100 Kb 데이터 항목을 기반으로 읽기 및 쓰기 작업을 수행 하는 데 필요한 RUs의 수를 보여 줍니다.

|항목 크기|1 읽기|1 쓰기|
|-------------|------|-------|
|1KB|1RU|5RU|
|100KB|10RU|50RU|

## <a name="cost-of-reads-and-writes"></a>읽기 및 쓰기의 비용

1000 r u/초를 프로 비전 하는 경우이 크기는 360만 r u/시간으로 청구 되며 해당 시간 (미국 및 유럽)에 대한 비용 $0.08이 청구 됩니다. 1kb 크기 데이터 항목의 경우 프로 비전 된 처리량을 사용 하 여 360만 읽기 또는 72만 쓰기 (360만 r u/5)를 사용할 수 있습니다. 백만 읽기 및 쓰기로 정규화 된 비용은 $0.022/백만의 읽기 ($0.08/3.6)와 $0.111/백만의 쓰기 ($0.08/0.72)입니다. 백만당 비용은 아래 표에 나와 있는 것처럼 최소화됩니다.

|항목 크기|100만 읽기 비용|100만 쓰기 비용|
|-------------|-------|--------|
|1KB|$0.022|$0.111|
|100KB|$0.222|$1.111|


대부분의 기본 Blob 또는 개체 저장소 서비스는 백만 읽기 트랜잭션당 $0.40, 백만 쓰기 트랜잭션당 $5가 부과됩니다. 최적으로 사용 하는 경우 이러한 다른 솔루션 보다 최대 98%의 비용을 Cosmos DB 수 있습니다 (1kb 거래의 경우).

## <a name="next-steps"></a>다음 단계

* 작업에 대한 처리량을 예측 하려면 사용 [계산기](https://cosmos.azure.com/capacitycalculator/) 를 사용 합니다.

