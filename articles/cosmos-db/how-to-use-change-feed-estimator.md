---
title: 변경 피드 추정기 사용 - Azure Cosmos DB
description: 변경 피드 추정기를 사용하여 변경 피드 프로세서의 진행률을 분석하는 방법을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/15/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: 442e1c4facce076ce9eeacee772df9381fee6f71
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88997220"
---
# <a name="use-the-change-feed-estimator"></a>변경 피드 추정기 사용

이 문서에서는 [변경 피드 프로세서](./change-feed-processor.md) 인스턴스가 변경 피드를 읽을 때 진행률을 모니터링하는 방법을 설명합니다.

## <a name="why-is-monitoring-progress-important"></a>진행률 모니터링이 중요한 이유는 무엇인가요?

변경 피드 프로세서는 [변경 피드](./change-feed.md)를 통해 앞으로 이동하여 변경 내용을 대리자 구현에 전달하는 포인터 역할을 합니다. 

변경 피드 프로세서 배포는 CPU, 메모리, 네트워크 등의 사용 가능한 리소스를 기반으로 특정 속도로 변경 내용을 처리할 수 있습니다.

이 속도가 Azure Cosmos 컨테이너에서 변경이 발생하는 속도보다 느리면 프로세서가 지연되기 시작합니다.

이 시나리오를 파악하면 변경 피드 프로세서 배포를 확장해야 하는지 여부를 파악할 수 있습니다.

## <a name="implement-the-change-feed-estimator"></a>변경 피드 추정기 구현

[변경 피드 프로세서](./change-feed-processor.md)와 마찬가지로, 변경 피드 추정기는 푸시 모델로 작동합니다. 추정기는 마지막으로 처리된 항목(임대 컨테이너의 상태를 기준으로 정의)과 컨테이너의 마지막 변경 내용의 차이를 측정하고, 이 값을 대리자에게 푸시합니다. 측정 간격을 사용자 지정할 수 있으며, 기본값은 5초입니다.

예를 들어 변경 피드 프로세서가 다음과 같이 정의된 경우:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartProcessorEstimator)]

추정기를 초기화하여 해당 프로세서를 측정하는 올바른 방법은 다음과 같이 `GetChangeFeedEstimatorBuilder`를 사용하는 것입니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartEstimator)]

프로세서와 추정기는 동일한 `leaseContainer` 및 동일한 이름을 공유합니다.

다른 두 매개 변수는 대리자이며, 프로세서에서 읽을 **보류 중인 변경 수** 및 원하는 측정 간격을 나타내는 숫자를 수신합니다.

예측을 수신하는 대리자의 예는 다음과 같습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=EstimationDelegate)]

이 예측을 모니터링 솔루션으로 전송하여 시간이 지남에 따라 진행률이 어떻게 되는지 이해할 수 있습니다.

> [!NOTE]
> 변경 피드 추정기를 변경 피드 프로세서의 일부로 배포하거나 동일한 프로젝트에 포함할 필요가 없습니다. 변경 피드 추정기는 독립적이며 완전히 다른 인스턴스에서 실행할 수 있습니다. 동일한 이름과 임대 구성을 사용하기만 하면 됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub의 사용 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub의 추가 샘플](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드 프로세서에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 프로세서 개요](change-feed-processor.md)
* [변경 피드 프로세서 시작 시간](how-to-configure-change-feed-start-time.md)
