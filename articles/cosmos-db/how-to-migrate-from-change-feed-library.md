---
title: 변경 피드 프로세서 라이브러리에서 Azure Cosmos DB .NET V3 SDK로 마이그레이션
description: 변경 피드 프로세서 라이브러리를 사용 하 여 Azure Cosmos DB SDK V3로 응용 프로그램을 마이그레이션하는 방법에 대해 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 09/17/2019
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 7a15e5135cd89d7360a1357e3518b1253e80ee65
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019524"
---
# <a name="migrate-from-the-change-feed-processor-library-to-the-azure-cosmos-db-net-v3-sdk"></a>변경 피드 프로세서 라이브러리에서 Azure Cosmos DB .NET V3 SDK로 마이그레이션

이 문서에서는 [변경 피드 프로세서 라이브러리](https://github.com/Azure/azure-documentdb-changefeedprocessor-dotnet) 를 사용 하는 기존 응용 프로그램 코드를 최신 버전의 .net sdk (.NET V3 sdk 라고도 함)의 [변경 피드](change-feed.md) 기능으로 마이그레이션하는 데 필요한 단계를 설명 합니다.

## <a name="required-code-changes"></a>필요한 코드 변경

.NET V3 SDK에는 몇 가지 주요 변경 사항이 포함 되어 있으며, 다음은 응용 프로그램을 마이그레이션하는 주요 단계입니다.

1. 인스턴스를 `DocumentCollectionInfo` 모니터링 되 `Container` 는 및 임대 컨테이너에 대 한 참조로 변환 합니다.
1. 를 사용 하 여 `WithProcessorOptions` `WithLeaseConfiguration` 시작 시간에 대해 및를 사용 하 `WithPollInterval` `WithStartTime` [for start time](how-to-configure-change-feed-start-time.md)고 `WithMaxItems` 최대 항목 수를 정의 하려면를 사용 하는 사용자 지정을 업데이트 해야 합니다.
1. 에 `processorName` 구성 된 `GetChangeFeedProcessorBuilder` 값과 일치 하도록 on을 설정 `ChangeFeedProcessorOptions.LeasePrefix` 하거나, `string.Empty` 그렇지 않으면를 사용 합니다.
1. 변경 내용은로 더 이상 전달 되지 않습니다 `IReadOnlyList<Document>` . 대신에서 `IReadOnlyCollection<T>` 정의 해야 하는 `T` 형식이 며 기본 항목 클래스가 더 이상 없습니다.
1. 변경 내용을 처리 하기 위해 더 이상 구현이 필요 하지 않으며 대신 [대리자를 정의](change-feed-processor.md#implementing-the-change-feed-processor)해야 합니다. 대리자는 정적 함수 이거나 실행 간에 상태를 유지 해야 하는 경우 고유한 클래스를 만들고 인스턴스 메서드를 대리자로 전달할 수 있습니다.

예를 들어, 변경 피드 프로세서를 빌드하는 원래 코드는 다음과 같습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorLibrary)]

마이그레이션된 코드는 다음과 같습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=ChangeFeedProcessorMigrated)]

대리자는 정적 메서드 일 수 있습니다.

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=Delegate)]

## <a name="state-and-lease-container"></a>상태 및 임대 컨테이너

변경 피드 프로세서 라이브러리와 마찬가지로 .NET V3 SDK의 변경 피드 기능은 [임대 컨테이너](change-feed-processor.md#components-of-the-change-feed-processor) 를 사용 하 여 상태를 저장 합니다. 그러나 스키마는 다릅니다.

SDK V3 변경 피드 프로세서는 마이그레이션된 응용 프로그램 코드를 처음 실행할 때 모든 이전 라이브러리 상태를 검색 하 고 새 스키마로 자동으로 마이그레이션합니다. 

이전 코드를 사용 하 여 응용 프로그램을 안전 하 게 중지 하 고, 코드를 새 버전으로 마이그레이션하고, 마이그레이션된 응용 프로그램을 시작 하 고, 응용 프로그램이 중지 되는 동안 발생 한 모든 변경 내용을 새 버전에서 선택 하 고 처리할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

* [Azure Cosmos DB SDK](sql-api-sdk-dotnet.md)
* [GitHub의 사용 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [GitHub의 추가 샘플](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>다음 단계

이제 다음 문서에서 변경 피드 프로세서에 대해 자세히 알아볼 수 있습니다.

* [변경 피드 프로세서 개요](change-feed-processor.md)
* [변경 피드 평가기 사용](how-to-use-change-feed-estimator.md)
* [변경 피드 프로세서 시작 시간](how-to-configure-change-feed-start-time.md)
