---
title: 변경 피드 프로세서 시작 시간을 구성하는 방법 - Azure Cosmos DB
description: 특정 날짜와 시간부터 읽기를 시작하도록 변경 피드 프로세서를 구성하는 방법을 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/13/2019
ms.author: maquaran
ms.custom: devx-track-csharp
ms.openlocfilehash: dcd92421a7ef4e2314d27a7724be01336c26c9eb
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018198"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>변경 피드 프로세서 시작 시간을 구성하는 방법

이 문서는 특정 날짜와 시간부터 읽기를 시작하도록 [변경 피드 프로세서](./change-feed-processor.md)를 구성하는 방법을 설명합니다.

## <a name="default-behavior"></a>기본 동작

변경 피드 프로세서가 처음으로 시작되면 임대 컨테이너를 초기화하고 [처리 수명 주기](./change-feed-processor.md#processing-life-cycle)을 시작합니다. 변경 피드 프로세서가 처음 초기화되기 전에 컨테이너에서 발생한 변경 내용은 감지되지 않습니다.

## <a name="reading-from-a-previous-date-and-time"></a>이전 날짜와 시간에서 읽기

`DateTime` 인스턴스를 `WithStartTime` 빌더 확장에 전달하여 **특정 날짜와 시간**에 시작되는 변경 사항을 읽도록 변경 피드 프로세서를 초기화할 수 있습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

특정 날짜 및 시간에 변경 피드 프로세서가 초기화되고 이후에 발생한 변경 내용을 읽기 시작합니다.

## <a name="reading-from-the-beginning"></a>시작부터 읽기

데이터 마이그레이션이나 컨테이너의 전체 기록 분석과 같은 다른 시나리오에서는 **컨테이너 수명이 시작**될 때부터 변경 피드를 읽어야 합니다. 이렇게 하려면 빌더 확장에 `WithStartTime`을 사용하면 되지만 `DateTime.MinValue.ToUniversalTime()`을 전달하면 다음과 같이 최소 `DateTime` 값의 UTC 표현이 생성됩니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

변경 피드 프로세서가 초기화되고 컨테이너 수명이 시작되는 시점부터 변경 내용을 읽기 시작합니다.

> [!NOTE]
> 이러한 사용자 지정 옵션은 변경 피드 프로세서의 시작 시점을 설정하는 데만 작동합니다. 임대 컨테이너가 처음으로 초기화되고 나면 변경해도 아무런 영향이 없습니다.

> [!NOTE]
> 특정 시점을 지정하면 컨테이너에 현재 존재하는 항목에 대한 변경 내용만 읽습니다. 항목이 삭제되면 변경 피드의 해당 기록도 제거됩니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub의 사용 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub의 추가 샘플](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드 프로세서에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 프로세서 개요](change-feed-processor.md)
* [변경 피드 평가기 사용](how-to-use-change-feed-estimator.md)
