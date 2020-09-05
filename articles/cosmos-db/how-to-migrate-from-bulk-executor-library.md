---
title: 대량 실행자 라이브러리에서 Azure Cosmos DB .NET V3 SDK의 대량 지원으로 마이그레이션
description: 대량 실행자 라이브러리를 사용 하 여 Azure Cosmos DB SDK V3에서 대량 지원으로 응용 프로그램을 마이그레이션하는 방법에 대해 알아봅니다.
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/24/2020
ms.author: maquaran
ms.custom: devx-track-dotnet
ms.openlocfilehash: 8f573a3e851fe428c66066e36a913d6580cabd51
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022482"
---
# <a name="migrate-from-the-bulk-executor-library-to-the-bulk-support-in-azure-cosmos-db-net-v3-sdk"></a>대량 실행자 라이브러리에서 Azure Cosmos DB .NET V3 SDK의 대량 지원으로 마이그레이션

이 문서에서는 [.net bulk executor 라이브러리](bulk-executor-dot-net.md) 를 사용 하는 기존 응용 프로그램 코드를 최신 버전의 .net SDK에서 [대량 지원](tutorial-sql-api-dotnet-bulk-import.md) 기능으로 마이그레이션하는 데 필요한 단계에 대해 설명 합니다.

## <a name="enable-bulk-support"></a>대량 지원 사용

`CosmosClient` [Allow대량 실행](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.allowbulkexecution) 구성을 통해 인스턴스에서 대량 지원을 사용 하도록 설정 합니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Initialization":::

## <a name="create-tasks-for-each-operation"></a>각 작업에 대 한 작업 만들기

.NET SDK에서의 대량 지원은 동시에 발생 하는 [작업 병렬 라이브러리](https://docs.microsoft.com/dotnet/standard/parallel-programming/task-parallel-library-tpl) 및 그룹화 작업을 활용 하 여 작동 합니다. 

SDK에는 입력 매개 변수로 문서 또는 작업 목록을 사용 하는 단일 메서드가 없지만, 대량으로 실행 하려는 각 작업에 대해 작업을 만든 다음 완료 될 때까지 기다려야 합니다.

예를 들어 초기 입력이 각 항목의 스키마가 있는 항목의 목록입니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="Model":::

대량 가져오기를 수행 하려면 (BulkImportAsync를 사용 하는 것과 유사)를 동시에 호출 해야 `CreateItemAsync` 합니다. 예를 들어:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkImport":::

대량 *업데이트* 를 수행 하려는 경우 ( [BulkUpdateAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkupdateasync)사용과 유사) `ReplaceItemAsync` 항목 값을 업데이트 한 후 메서드를 동시에 호출 해야 합니다. 예를 들어:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkUpdate":::

대량 *삭제* ( [BulkDeleteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkdeleteasync)사용과 유사)를 수행 하려는 경우에는 `DeleteItemAsync` `id` 각 항목의 및 파티션 키를 사용 하 여 동시에 호출 해야 합니다. 예를 들어:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkDelete":::

## <a name="capture-task-result-state"></a>작업 결과 상태 캡처

위의 코드 예제에서는 작업의 동시 목록을 만들고 `CaptureOperationResponse` 각 작업에 대해 메서드를 호출 했습니다. 이 메서드는 오류를 캡처하고 [요청 단위 사용](request-units.md)을 추적 하 여 *유사한 응답 스키마* 를 대량 실행자로 유지할 수 있도록 하는 확장입니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="CaptureOperationResult":::

여기서는 `OperationResponse` 다음과 같이 선언 됩니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="OperationResult":::

## <a name="execute-operations-concurrently"></a>동시에 작업 실행

전체 작업 목록에 대 한 범위를 추적 하기 위해이 도우미 클래스를 사용 합니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="BulkOperationsHelper":::

`ExecuteAsync`메서드는 모든 작업이 완료 될 때까지 기다렸다가 다음과 같이 사용할 수 있습니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="WhenAll":::

## <a name="capture-statistics"></a>통계 캡처

이전 코드는 모든 작업이 완료 될 때까지 대기 하 고 필요한 통계를 계산 합니다. 이러한 통계는 대량 실행자 라이브러리의 대량 [Importresponse](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkimport.bulkimportresponse)와 비슷합니다.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration/Program.cs" ID="ResponseType":::

는 `BulkOperationResponse` 다음을 포함 합니다.

1. 대량 지원을 통해 작업 목록을 처리 하는 데 걸린 총 시간입니다.
1. 성공한 작업의 수입니다.
1. 사용 된 총 요청 단위입니다.
1. 오류가 발생 하는 경우 예외를 포함 하는 튜플 목록과 로깅 및 식별 목적으로 관련 된 항목이 표시 됩니다.

## <a name="retry-configuration"></a>구성 다시 시도

대량 실행자 라이브러리에 [guidance](bulk-executor-dot-net.md#bulk-import-data-to-an-azure-cosmos-account) 는 `MaxRetryWaitTimeInSeconds` RetryOptions의 및를로 설정 하 여 컨트롤을 `MaxRetryAttemptsOnThrottledRequests` 라이브러리에 위임 [RetryOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.retryoptions) 하는 방법에 대해 언급 한 지침이 `0` 있습니다.

.NET SDK를 대량으로 지원 하기 위해 숨겨진 동작은 없습니다. [CosmosClientOptions MaxRetryAttemptsOnRateLimitedRequests](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretryattemptsonratelimitedrequests) 및 [CosmosClientOptions](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions.maxretrywaittimeonratelimitedrequests)를 통해 재시도 옵션을 직접 구성할 수 있습니다.

> [!NOTE]
> 프로 비전 된 요청 단위가 데이터 양에 따라 예상 보다 훨씬 적은 경우에는 값을 높은 값으로 설정 하는 것을 고려할 수 있습니다. 대량 작업을 수행 하는 데 더 많은 시간이 걸리지만 더 높은 재시도로 인해 완전히 성공할 가능성이 높습니다.

## <a name="performance-improvements"></a>성능 향상

.NET SDK를 사용 하는 다른 작업과 마찬가지로 stream Api를 사용 하면 성능이 향상 되 고 불필요 한 serialization이 방지 됩니다. 

Stream Api를 사용 하는 것은 사용 하는 데이터의 특성이 바이트 스트림 (예: 파일 스트림)의 특성과 일치 하는 경우에만 가능 합니다. 이러한 경우 `CreateItemStreamAsync` , 또는 메서드를 사용 하 `ReplaceItemStreamAsync` `DeleteItemStreamAsync` 고 대신 (대신)로 작업 하는 경우 `ResponseMessage` 처리량이 `ItemResponse` 증가할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* .NET SDK 릴리스에 대해 자세히 알아보려면 [AZURE COSMOS DB SDK](sql-api-sdk-dotnet.md) 문서를 참조 하세요.
* GitHub에서 전체 [마이그레이션 소스 코드](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkExecutorMigration) 를 가져옵니다.
* [GitHub의 추가 대량 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/BulkSupport)
