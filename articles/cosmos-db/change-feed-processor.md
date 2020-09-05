---
title: Azure Cosmos DB의 변경 피드 프로세서
description: Azure Cosmos DB 변경 피드 프로세서를 사용하여 변경 피드, 변경 피드 프로세서의 구성 요소를 읽는 방법에 대해 알아봅니다.
author: timsander1
ms.author: tisande
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/13/2020
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 3a802cc3d6178302445e0c31c52785d00207d0bd
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998546"
---
# <a name="change-feed-processor-in-azure-cosmos-db"></a>Azure Cosmos DB의 변경 피드 프로세서

변경 피드 프로세서는 [Azure Cosmos DB SDK V3](https://github.com/Azure/azure-cosmos-dotnet-v3)의 일부입니다. 이를 통해 변경 피드를 읽고 이벤트 처리를 여러 소비자에게 효과적으로 배포하는 프로세스를 간소화할 수 있습니다.

변경 피드 프로세서 라이브러리의 주요 혜택은 변경 피드의 모든 이벤트를 "최소 한 번" 제공할 수 있는 내결함성이 있는 동작입니다.

## <a name="components-of-the-change-feed-processor"></a>변경 피드 프로세서의 구성 요소

변경 피드 프로세서를 구현하는 주요 구성 요소 4개는 다음과 같습니다.

1. **모니터링된 컨테이너:** 모니터링된 컨테이너에는 변경 피드가 생성되는 데이터가 있습니다. 모니터링된 컨테이너에 대한 모든 삽입 및 업데이트는 컨테이너의 변경 피드에 반영됩니다.

1. **임대 컨테이너:** 임대 컨테이너는 상태 스토리지 역할을 하며 여러 작업자의 변경 피드 처리를 조정합니다. 임대 컨테이너는 모니터링되는 컨테이너와 동일한 계정 또는 별도의 계정에 저장할 수 있습니다.

1. **호스트:** 호스트는 변경 피드 프로세서를 사용하여 변경 내용을 수신하는 애플리케이션 인스턴스입니다. 동일한 임대 구성을 사용하는 여러 인스턴스는 병렬로 실행될 수 있지만 각 인스턴스는 다른 **인스턴스 이름**을 가져야 합니다.

1. **대리자:** 대리자는 개발자가 변경 피드 프로세서에서 읽는 각 변경 내용 일괄 처리로 수행하려는 작업을 정의하는 코드입니다. 

변경 피드 프로세서의 이러한 4개의 요소가 상호 작용하는 방법을 이해하기 위해 다음 다이어그램의 예제를 살펴보겠습니다. 모니터링된 컨테이너는 문서를 저장하고 파티션 키로 "City"를 사용합니다. 파티션 키 값이 항목을 포함하는 범위 내에 배포되는 것을 볼 수 있습니다. 두 개의 호스트 인스턴스가 있으며 변경 피드 프로세서는 각 인스턴스에 서로 다른 파티션 키 값 범위를 할당하여 컴퓨팅 배포를 최대화합니다. 각 범위를 병렬로 읽고 있으며 해당 진행률이 임대 컨테이너의 다른 범위와 별도로 유지됩니다.

:::image type="content" source="./media/change-feed-processor/changefeedprocessor.png" alt-text="변경 피드 프로세서 예제" border="false":::

## <a name="implementing-the-change-feed-processor"></a>변경 피드 프로세서 구현

항목의 지점은 `GetChangeFeedProcessorBuilder`를 호출하는 `Container` 인스턴스에서 항상 모니터링되는 컨테이너입니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=DefineProcessor)]

여기서 첫 번째 매개 변수는 이 프로세서의 목표를 설명하는 고유한 이름이고, 두 번째 이름은 변경 내용을 처리하는 대리자 구현입니다. 

대리자의 예제는 다음과 같습니다.


[!code-csharp[Main](~/samples-cosmosdb-dotnet-change-feed-processor/src/Program.cs?name=Delegate)]

마지막으로 `WithInstanceName`을 사용하여 이 프로세서 인스턴스의 이름을 정의합니다. 이는 `WithLeaseContainer`를 사용하여 임대 상태를 유지하는 컨테이너입니다.

`Build`를 호출하면 `StartAsync`를 호출하여 시작할 수 있는 프로세서 인스턴스가 제공됩니다.

## <a name="processing-life-cycle"></a>처리 수명 주기

호스트 인스턴스의 일반적인 수명 주기는 다음과 같습니다.

1. 변경 피드를 읽습니다.
1. 변경 내용이 없는 경우에는 미리 정의된 시간(작성기에서 `WithPollInterval`로 사용자 지정 가능) 동안 일시 중지하고 #1로 이동합니다.
1. 변경 내용이 있으면 **대리자**에게 보냅니다.
1. **성공적으로**  변경 내용 처리를 완료하면 최신 처리 시점으로 임대 상점을 업데이트하고 #1로 이동합니다.

## <a name="error-handling"></a>오류 처리

변경 피드 프로세서는 사용자 코드 오류에 대한 복원력이 있습니다. 즉, 대리자 구현에 처리되지 않은 예외가 있는 경우(#4단계) 특정 변경 내용의 일괄 처리를 처리하는 스레드가 중지되고 새 스레드가 생성됩니다. 새 스레드는 해당 파티션 키 값 범위에 대한 임대 저장소의 최신 시점을 확인하고, 해당 위치에서 다시 시작하여 대리자에게 동일한 변경 내용의 일괄 처리를 효율적으로 보냅니다. 이 동작은 대리자가 변경사항을 올바르게 처리할 때까지 계속되며, 대리자 코드가 예외를 throw하는 경우 해당 일괄 처리를 다시 시도하므로 변경 피드 프로세서에서 "한 번 이상"의 보장을 가지는 이유입니다.

변경 피드 프로세서가 동일한 변경 내용의 일괄 처리를 계속해서 다시 시도하는 것에 “고착”되지 않도록 하려면 예외 발생 시 배달 못 한 큐에 문서를 쓰도록 처리 함수에 논리를 추가해야 합니다. 이 디자인은 계속해서 나중에 변경 내용을 처리할 수 있는 동안 처리되지 않은 변경 내용을 추적할 수 있도록 합니다. 배달 못 한 편지 큐는 단순히 다른 Cosmos 컨테이너가 될 수 있습니다. 정확한 데이터 저장소는 중요하지 않습니다. 단지 처리되지 않은 변경 내용이 지속됩니다.

또한 [변경 피드 평가기](how-to-use-change-feed-estimator.md)를 사용하여 변경 피드를 읽을 때 변경 피드 프로세서 인스턴스의 진행률을 모니터링할 수 있습니다. 변경 피드 프로세서에서 계속해서 동일한 변경 내용의 일괄 처리를 다시 시도하는 경우를 모니터링하는 것 외에도, 변경 피드 프로세서가 CPU, 메모리, 네트워크 대역폭 등의 사용 가능한 리소스로 인해 지연되는지 여부를 파악할 수도 있습니다.

## <a name="deployment-unit"></a>배포 단위

단일 변경 피드 프로세서 배포 단위는 동일한 `processorName` 및 임대 컨테이너 구성을 사용하는 하나 이상의 인스턴스로 구성됩니다. 하나 이상의 인스턴스로 구성된 각 배포 단위 및 변경 내용에 대해 서로 다른 비즈니스 흐름이 있는 여러 배포 단위를 사용할 수 있습니다. 

예를 들어 컨테이너에 변경 내용이 있을 때마다 외부 API를 트리거하는 하나의 배포 단위가 있을 수 있습니다. 다른 배포 단위는 변경 내용이 있을 때마다 데이터를 실시간으로 이동할 수 있습니다. 모니터링되는 컨테이너에서 변경이 발생하면 모든 배포 단위에 대한 알림이 표시됩니다.

## <a name="dynamic-scaling"></a>동적 크기 조정

앞서 언급했듯이 배포 단위 내에서 하나 이상의 인스턴스를 사용할 수 있습니다. 배포 단위 내에서 컴퓨팅 배포를 활용하기 위해 유일한 주요 요구 사항은 다음과 같습니다.

1. 모든 인스턴스에 동일한 임대 컨테이너 구성이 있어야 합니다.
1. 모든 인스턴스의 `processorName`이 동일해야 합니다.
1. 각 인스턴스에는 다른 인스턴스 이름(`WithInstanceName`)이 있어야 합니다.

이러한 세 조건이 적용되는 경우 변경 피드 프로세서는 동일한 배포 알고리즘을 사용하여 모든 임대 컨테이너의 임대를 해당 배포 단위의 실행 중인 모든 인스턴스에 배포하고 컴퓨팅을 병렬화합니다. 하나의 임대는 지정된 시간에 하나의 인스턴스만 소유할 수 있으므로 최대 인스턴스 수는 임대 수와 동일합니다.

인스턴스 수는 증가 및 축소될 수 있으며, 변경 피드 프로세서는 적절히 재배포하여 부하를 동적으로 조정합니다.

또한 변경 피드 프로세서는 처리량 또는 스토리지가 늘어남에 따라 컨테이너 크기를 동적으로 조정할 수 있습니다. 컨테이너가 커지면 변경 피드 프로세서는 임대를 동적으로 늘리고 기존 인스턴스 간에 새 임대를 배포하여 이러한 시나리오를 투명하게 처리합니다.

## <a name="change-feed-and-provisioned-throughput"></a>변경 피드 및 프로비전된 처리량

Cosmos 컨테이너 내부 및 외부의 데이터 이동은 항상 RU를 사용하므로 사용된 RU 요금이 청구됩니다. 임대 컨테이너에서 사용되는 RU 요금이 청구됩니다.

## <a name="where-to-host-the-change-feed-processor"></a>변경 피드 프로세서를 호스트할 위치

변경 피드 프로세서는 장기 실행 프로세스나 작업을 지 원하는 모든 플랫폼에서 호스팅될 수 있습니다.

* 연속 실행 중인 [Azure WebJob](https://docs.microsoft.com/learn/modules/run-web-app-background-task-with-webjobs/).
* [Azure 가상 컴퓨터](https://docs.microsoft.com/azure/architecture/best-practices/background-jobs#azure-virtual-machines)의 프로세스입니다.
* [Azure Kubernetes Service](https://docs.microsoft.com/azure/architecture/best-practices/background-jobs#azure-kubernetes-service)의 백그라운드 작업입니다.
* [ASP.NET 호스 티 드 서비스](https://docs.microsoft.com/aspnet/core/fundamentals/host/hosted-services)입니다.

변경 피드 프로세서는 수명이 짧은 환경에서 실행 될 수 있지만, 임대 컨테이너는 상태를 유지 하기 때문에 이러한 환경의 시작 및 중지 주기는 환경을 시작할 때마다 프로세서를 시작 하는 오버 헤드로 인해 알림을 수신 하는 지연을 추가 합니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub에서 샘플 응용 프로그램 완료](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)
* [GitHub의 추가 사용 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [변경 피드 프로세서에 대 한 Cosmos DB 워크숍 랩](https://azurecosmosdb.github.io/labs/dotnet/labs/08-change_feed_with_azure_functions.html#consume-cosmos-db-change-feed-via-the-change-feed-processor)

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드 프로세서에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 개요](change-feed.md)
* [변경 피드 풀 모델](change-feed-pull-model.md)
* [변경 피드 프로세서 라이브러리에서 마이그레이션하는 방법](how-to-migrate-from-change-feed-library.md)
* [변경 피드 평가기 사용](how-to-use-change-feed-estimator.md)
* [변경 피드 프로세서 시작 시간](how-to-configure-change-feed-start-time.md)
