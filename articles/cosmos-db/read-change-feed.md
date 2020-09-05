---
title: Cosmos DB 변경 피드 읽기
description: 이 문서에서는 Azure Cosmos DB에서 변경 피드를 읽고 액세스할 수 있는 다양한 옵션에 대해 설명합니다.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2020
ms.reviewer: sngun
ms.openlocfilehash: 23f99dc5c648948ce07f1b40106667d24906328a
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236797"
---
# <a name="reading-azure-cosmos-db-change-feed"></a>Cosmos DB 변경 피드 읽기

푸시 모델 또는 풀 모델 중 하나를 사용하여 Azure Cosmos DB 변경 피드로 작업할 수 있습니다. 밀어넣기 모델을 사용 하면 서버 (변경 피드)가이 작업을 처리 하는 비즈니스 논리가 있는 클라이언트에 작업을 푸시합니다. 그러나 작업을 확인 하 고 마지막으로 처리 된 작업의 상태를 저장 하는 것은 서버에서 처리 됩니다.

끌어오기 모델을 사용 하는 경우 클라이언트는 서버에서 작업을 가져와야 합니다. 이 경우 클라이언트는 작업 처리를 위한 비즈니스 논리 뿐만 아니라 마지막으로 처리 된 작업의 상태를 저장 하 고, 여러 클라이언트에서 동시에 작업을 처리 하는 부하 분산을 처리 하 고, 오류를 처리 합니다.

Azure Cosmos DB 변경 피드를 읽을 때 일반적으로 다음 사항에 대해 걱정할 필요가 없으므로 푸시 모델을 사용하는 것이 좋습니다.

- 이후 변경 내용에 대한 변경 피드를 폴링.
- 마지막으로 처리된 변경 내용에 대한 상태를 저장. 변경 피드에서 읽을 때 이는 [임대 컨테이너](change-feed-processor.md#components-of-the-change-feed-processor)에 자동으로 저장됩니다.
- 여러 클라이언트에서 변경 내용을 소비하는 부하를 분산. 예를 들어 한 클라이언트가 변경 내용의 처리를 유지할 수 없고 다른 클라이언트는 사용 가능한 용량이 있는 경우입니다.
- [오류 처리](change-feed-processor.md#error-handling). 예를 들어 코드에서 처리되지 않은 예외 또는 일시적인 네트워크 문제로 인해 올바르게 처리되지 않은 실패한 변경 내용을 자동으로 다시 시도합니다.

Azure Cosmos DB 변경 피드를 사용하는 대부분의 시나리오에서는 푸시 모델 옵션 중 하나를 사용합니다. 그러나 풀 모델의 추가적인 낮은 수준 제어를 원하는 몇 가지 시나리오가 있습니다. 이러한 개체는 다음과 같습니다.

- 특정 파티션 키에서 변경 내용 읽기
- 클라이언트에서 변경 내용을 받아 처리하는 속도 제어
- 변경 피드에서 기존 데이터를 한 번 읽음(예: 데이터 마이그레이션 수행)

## <a name="reading-change-feed-with-a-push-model"></a>푸시 모델을 사용하여 변경 피드 읽기

푸시 모델을 사용하는 것은 변경 피드를 읽는 가장 쉬운 방법입니다. 푸시 모델로 변경 피드를 읽을 수 있는 방법에는 [Azure Functions Cosmos DB 트리거](change-feed-functions.md) 및 [변경 피드 프로세서 라이브러리](change-feed-processor.md)의 두 가지 방법이 있습니다. Azure Functions는 백그라운드에서 변경 피드 프로세서를 사용하므로 이 두 가지는 모두 변경 피드를 읽는 매우 비슷한 방법입니다. 변경 피드를 완전히 다른 방식으로 읽는 것이 아니라, Azure Functions를 변경 피드 프로세서의 호스팅 플랫폼으로 생각하세요.

### <a name="azure-functions"></a>Azure 기능

Azure Functions는 단순히 변경 피드를 사용하여 시작하는 경우 가장 간단한 옵션입니다. 이러한 단순함 덕분에 대부분의 변경 피드 사용 사례에 권장되는 옵션이기도 합니다. Azure Cosmos DB에 대한 Azure Functions 트리거를 생성할 때 연결할 컨테이너를 선택하면 컨테이너에 변경 내용이 있을 때마다 Azure 함수가 트리거됩니다. Azure Functions는 백그라운드에서 변경 피드 프로세서를 사용하므로, 컨테이너의 [파티션](partition-data.md)에서 변경 내용 처리를 자동으로 병렬화합니다.

Azure Functions를 사용하여 개발하는 것은 손쉬운 환경으로, 변경 피드 프로세서를 직접 배포하는 것보다 더 빠를 수 있습니다. 트리거는 Azure Functions 포털을 사용하거나, SDK를 사용하여 프로그래밍 방식으로 만들 수 있습니다. Visual Studio 및 VS Code는 Azure Functions를 작성하는 지원을 제공하며 플랫폼 간 개발을 위해 Azure Functions CLI를 사용할 수도 있습니다. 데스크톱에서 코드를 작성하고 디버그한 다음, 클릭 한 번으로 함수를 배포할 수 있습니다. 자세한 내용은 [Azure Functions를 사용한 서버리스 데이터베이스 컴퓨팅](serverless-computing-database.md) 및 [Azure Functions와 함께 변경 피드 사용](change-feed-functions.md) 문서를 참조하세요.

### <a name="change-feed-processor-library"></a>변경 피드 프로세서 라이브러리

변경 피드 프로세서를 통해 변경 피드를 더 잘 제어할 수 있으며, 복잡성은 낮아집니다. 변경 피드 프로세서 라이브러리는 관찰자 패턴을 따릅니다. 여기서 처리 함수는 라이브러리를 통해 호출됩니다. 변경 피드 프로세서 라이브러리는 변경 내용을 자동으로 확인하고, 변경 내용이 있으면 클라이언트에 “푸시”합니다. 높은 처리량 변경 피드가 있는 경우 변경 피드를 읽을 여러 클라이언트를 인스턴스화할 수 있습니다. 변경 피드 프로세서 라이브러리는 자동으로 여러 클라이언트 간에 부하를 분배합니다. 임대 상태를 유지하기 위해 여러 클라이언트 또는 논리에서 부하 분산 논리를 구현할 필요가 없습니다.

변경 피드 프로세서 라이브러리는 모든 변경 내용에 대한 “최소 한 번” 배달 기능을 보장합니다. 즉, 변경 피드 프로세서 라이브러리를 사용하는 경우 변경 피드의 모든 항목에 대해 처리 기능이 성공적으로 호출됩니다. 처리 함수의 비즈니스 논리에 처리되지 않은 예외가 있는 경우 실패한 변경 내용은 성공적으로 처리될 때까지 다시 시도됩니다. 변경 피드 프로세서가 동일한 변경 내용을 계속해서 다시 시도하는 것에 “고착”되지 않도록 하려면 예외 발생 시 배달 못 한 큐에 문서를 쓰는 논리를 처리 함수에 추가합니다. [오류 처리](change-feed-processor.md#error-handling)에 대해 자세히 알아봅니다.

Azure Functions에서 오류 처리에 대한 권장 사항은 동일합니다. 예외 발생 시 배달 못 한 큐에 문서를 쓰는 논리를 대리자 코드에 추가해야 합니다. 그러나 Azure 함수에 처리되지 않은 예외가 있는 경우 예외를 생성한 변경 내용은 자동으로 다시 시도되지 않습니다. 비즈니스 논리에 처리되지 않은 예외가 있는 경우 Azure 함수는 다음 변경 내용 처리로 이동합니다. Azure 함수는 같은 실패 변경 내용을 다시 시도하지 않습니다.

Azure Functions와 마찬가지로 변경 피드 프로세서 라이브러리를 사용하여 개발하는 것이 쉽습니다. 그러나 사용자는 변경 피드 프로세서에 대해 하나 이상의 호스트를 배포해야 할 책임이 있습니다. 호스트는 변경 피드 프로세서를 사용하여 변경 내용을 수신하는 애플리케이션 인스턴스입니다. Azure Functions에는 자동 크기 조정을 위한 기능이 있는 반면, 호스트 크기를 조정하는 책임은 사용자에게 있습니다. 자세한 내용을 알아보려면 [변경 피드 프로세서 사용](change-feed-processor.md#dynamic-scaling)을 참조하세요. 변경 피드 프로세서 라이브러리는 [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)의 일부입니다.

## <a name="reading-change-feed-with-a-pull-model"></a>풀 모델을 사용하여 변경 피드 읽기

[변경 피드 풀 모델](change-feed-pull-model.md)을 사용하면 변경 피드를 원하는 속도로 사용할 수 있습니다. 변경 내용은 클라이언트에 의해 요청되어야 하며 변경 내용에 대한 자동 폴링은 없습니다. 마지막으로 처리된 변경 내용에 영구적으로 “책갈피”를 설정하려면(푸시 모델의 임대 컨테이너와 유사) [연속 토큰을 저장](change-feed-pull-model.md#saving-continuation-tokens)해야 합니다.

변경 피드 풀 모델을 사용하여 변경 피드를 더 낮은 수준으로 제어할 수 있습니다. 풀 모델을 사용하여 변경 피드를 읽을 때 다음 세 가지 옵션을 사용할 수 있습니다.

- 전체 컨테이너에 대한 변경 내용 읽기
- 특정 [FeedRange](change-feed-pull-model.md#using-feedrange-for-parallelization)에 대한 변경 내용 읽기
- 특정 파티션 키 값에 대한 변경 내용 읽기

변경 피드 프로세서를 사용할 때와 마찬가지로 여러 클라이언트에서 변경 내용의 처리를 병렬화할 수 있습니다. 그러나 풀 모델은 클라이언트에서 부하 분산을 자동으로 처리하지 않습니다. 풀 모델을 사용하여 변경 피드의 처리를 병렬화할 때 먼저 FeedRange의 목록을 얻습니다. FeedRange는 파티션 키 값의 범위를 나타냅니다. FeedRange를 가져오고 컴퓨터에 배포하는 오케스트레이터 프로세스가 필요합니다. 그런 다음, 이러한 FeedRange를 사용하여 여러 컴퓨터에서 변경 피드를 병렬로 읽을 수 있습니다.

풀 모델을 사용하는 “최소 한 번” 배달 보장은 제공되지 않습니다. 풀 모델은 오류를 처리하는 방법을 결정하는 낮은 수준의 제어를 제공합니다.

> [!NOTE]
> 변경 피드 끌어오기 모델은 현재 [Azure Cosmos DB .NET SDK에서만 미리 보기](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.9.0-preview)로 제공됩니다. 다른 SDK 버전에서는 아직 미리 보기를 사용할 수 없습니다.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Cassandra 및 MongoDB에 대한 API의 변경 피드

변경 피드 기능은 MongoDB API의 변경 스트림 및 Cassandra API의 조건자가 있는 쿼리로 표시됩니다. MongoDB API의 구현 세부 정보에 대한 자세한 내용은 [Azure Cosmos DB API for MongoDB에서 스트림 변경](mongodb-change-streams.md)을 참조하세요.

기본 Apache Cassandra는 CDC 로그의 구성 가능한 디스크 크기에 도달하면 보관을 위해 특정 테이블에 플래그를 지정하고 해당 테이블에 대한 쓰기를 거부하는 메커니즘인 CDC(변경 데이터 캡처)를 제공합니다. Cassandra에 대한 Azure Cosmos DB API의 변경 피드 기능은 CQL을 통해 조건자를 사용하여 변경 내용을 쿼리하는 기능을 향상시킵니다. 구현 세부 정보에 대한 자세한 내용은 [Cassandra용 Azure Cosmos DB API에서 스트림 변경](cassandra-change-feed.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [Azure Functions로 변경 피드 사용](change-feed-functions.md)
* [변경 피드 프로세서 라이브러리 사용](change-feed-processor.md)
