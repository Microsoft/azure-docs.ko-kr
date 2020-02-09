---
title: Azure Cosmos DB의 변경 피드 프로세서 라이브러리
description: Azure Cosmos DB 변경 피드 프로세서 라이브러리를 사용 하 여 변경 피드, 변경 피드 프로세서의 구성 요소를 읽는 방법에 대해 알아봅니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 3e7f5e46068844da538864fdfaa03ca7023e4372
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75445573"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB의 변경 피드 프로세서 

변경 피드 프로세서는 [AZURE COSMOS DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)의 일부입니다. 이를 통해 변경 피드를 읽고 이벤트 처리를 여러 소비자에 게 효과적으로 배포 하는 프로세스를 간소화할 수 있습니다.

변경 피드 프로세서 라이브러리의 주요 혜택은 변경 피드의 모든 이벤트를 "최소 한 번" 제공할 수 있는 내결함성이 있는 동작입니다.

## <a name="components-of-the-change-feed-processor"></a>변경 피드 프로세서의 구성 요소

변경 피드 프로세서를 구현 하는 네 가지 주요 구성 요소는 다음과 같습니다. 

1. **모니터링된 컨테이너:** 모니터링된 컨테이너에는 변경 피드가 생성되는 데이터가 있습니다. 모니터링 되는 컨테이너에 대한 모든 삽입 및 업데이트는 컨테이너의 변경 피드에 반영 됩니다.

1. **임대 컨테이너:** 임대 컨테이너는 상태 저장소 역할을 하며 여러 작업자의 변경 피드 처리를 조정 합니다. 임대 컨테이너는 모니터링 되는 컨테이너와 동일한 계정 또는 별도의 계정에 저장할 수 있습니다. 

1. **호스트:** 호스트는 변경 피드 프로세서를 사용 하 여 변경 내용을 수신 하는 응용 프로그램 인스턴스입니다. 동일한 임대 구성을 사용 하는 여러 인스턴스는 병렬로 실행 될 수 있지만 각 인스턴스의 **인스턴스 이름은**서로 달라 야 합니다. 

1. **대리자:** 대리자는 변경 피드 프로세서에서 읽는 각 변경 내용 일괄 처리로 수행할 작업을 정의 하는 코드입니다. 

변경 피드 프로세서의 이러한 4개의 요소가 상호 작용하는 방법을 이해하기 위해 다음 다이어그램의 예제를 살펴보겠습니다. 모니터링 되는 컨테이너는 문서를 저장 하 고 ' City '를 파티션 키로 사용 합니다. 파티션 키 값이 항목을 포함 하는 범위 내에 배포 되는 것을 볼 수 있습니다. 두 개의 호스트 인스턴스가 있으며, 변경 피드 프로세서는 각 인스턴스에 서로 다른 파티션 키 값 범위를 할당 하 여 계산 배포를 최대화 합니다. 각 범위를 병렬로 읽고 있으며 해당 진행률이 임대 컨테이너의 다른 범위와 별도로 유지 됩니다.

![변경 피드 프로세서 예제](./media/change-feed-processor/changefeedprocessor.png)

## <a name="implementing-the-change-feed-processor"></a>변경 피드 프로세서 구현

항목의 지점은 `GetChangeFeedProcessorBuilder`호출 하는 `Container` 인스턴스에서 항상 모니터링 되는 컨테이너입니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

여기서 첫 번째 매개 변수는이 프로세서의 목표를 설명 하는 고유한 이름이 고, 두 번째 이름은 변경 내용을 처리 하는 대리자 구현입니다. 

대리자의 예는 다음과 같습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

마지막으로 `WithInstanceName` 및 `WithLeaseContainer`를 사용 하 여 임대 상태를 유지 하는 컨테이너를 사용 하 여이 프로세서 인스턴스의 이름을 정의 합니다.

`Build`를 호출 하면 `StartAsync`를 호출 하 여 시작할 수 있는 프로세서 인스턴스가 제공 됩니다.

## <a name="processing-life-cycle"></a>처리 수명 주기

호스트 인스턴스의 일반적인 수명 주기는 다음과 같습니다.

1. 변경 피드를 읽습니다.
1. 변경 내용이 없는 경우에는 미리 정의 된 시간 (작성기에서 `WithPollInterval`로 사용자 지정 가능)에 대해 절전 모드로 전환 하 고 #1로 이동 합니다.
1. 변경 내용이 있으면 **대리자로**보냅니다.
1. 대리자가 변경 내용을 **성공적으로**처리 하는 작업이 완료 되 면 처리 된 최신 시점으로 임대 저장소를 업데이트 하 고 #1로 이동 합니다.

## <a name="error-handling"></a>오류 처리

변경 피드 프로세서는 사용자 코드 오류에 대한 복원 력이 있습니다. 즉, 대리자 구현에 처리 되지 않은 예외가 있는 경우 (단계 #4) 특정 변경 내용의 일괄 처리가 중지 되 고 새 스레드가 생성 됩니다. 새 스레드는 해당 파티션 키 값 범위에 대한 최신 지정 시간을 확인 하 고, 해당 위치에서 다시 시작 하 여 대리자에 동일한 변경 내용을 효율적으로 보냅니다. 이 동작은 대리자가 변경 내용을 올바르게 처리할 때까지 계속 되며, 변경 피드 프로세서에서 "한 번 이상" 보장 하는 이유가 있습니다. 대리자 코드가 throw 되는 경우 해당 일괄 처리를 다시 시도 합니다.

## <a name="dynamic-scaling"></a>동적 크기 조정

소개에 설명 된 대로 변경 피드 프로세서는 여러 인스턴스에 계산을 자동으로 배포할 수 있습니다. 변경 피드 프로세서를 사용 하 여 응용 프로그램의 여러 인스턴스를 배포 하 고이를 활용할 수 있으며, 유일한 주요 요구 사항은 다음과 같습니다.

1. 모든 인스턴스에 동일한 임대 컨테이너 구성이 있어야 합니다.
1. 모든 인스턴스는 동일한 워크플로 이름을 가져야 합니다.
1. 각 인스턴스에는 다른 인스턴스 이름 (`WithInstanceName`)이 있어야 합니다.

이러한 세 조건이 적용 되는 경우 변경 피드 프로세서는 동일한 배포 알고리즘을 사용 하 여 실행 중인 모든 인스턴스에 임대 컨테이너의 모든 임대를 배포 하 고 compute를 병렬화 합니다. 하나의 임대는 지정 된 시간에 하나의 인스턴스만 소유할 수 있으므로 최대 인스턴스 수는 임대 수와 동일 합니다.

인스턴스 수는 증가 및 축소 될 수 있으며, 변경 피드 프로세서는 적절히 재배포 하 여 부하를 동적으로 조정 합니다.

또한 변경 피드 프로세서는 처리량 또는 저장소가 늘어남에 따라 컨테이너 크기를 동적으로 조정할 수 있습니다. 컨테이너가 커지면 변경 피드 프로세서는 임대를 동적으로 늘리고 기존 인스턴스 간에 새 임대를 배포 하 여 이러한 시나리오를 투명 하 게 처리 합니다.

## <a name="change-feed-and-provisioned-throughput"></a>변경 피드 및 프로비전된 처리량

Cosmos 컨테이너 내부 및 외부의 데이터 이동은 항상 RU를 사용하므로 사용된 RU 요금이 청구됩니다. 임대 컨테이너에서 사용되는 RU 요금이 청구됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub의 사용 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub의 추가 샘플](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드 프로세서에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [변경 피드 프로세서 라이브러리에서 마이그레이션하는 방법](how-to-migrate-from-change-feed-library.md)
* [변경 피드 평가기 사용](how-to-use-change-feed-estimator.md)
* [변경 피드 프로세서 시작 시간](how-to-configure-change-feed-start-time.md)
