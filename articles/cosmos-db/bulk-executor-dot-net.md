---
title: 대량 가져오기 및 업데이트 작업을 위해 Azure Cosmos DB에서 대량 실행자 .NET 라이브러리 사용
description: 대량 실행자 .NET 라이브러리를 사용 하 여 Azure Cosmos DB 문서를 대량으로 가져오고 업데이트 합니다.
author: tknandu
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: how-to
ms.date: 03/23/2020
ms.author: ramkris
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: 829ddc0b63031722cdcb572a2833926a7837d05d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004122"
---
# <a name="use-the-bulk-executor-net-library-to-perform-bulk-operations-in-azure-cosmos-db"></a>대량 실행자 .NET 라이브러리를 사용 하 여 Azure Cosmos DB에서 대량 작업을 수행 합니다.

> [!NOTE]
> 이 문서에서 설명 하는이 대량 실행자 라이브러리는 .NET SDK 2.x 버전을 사용 하는 응용 프로그램에 대해 유지 관리 됩니다. 새 응용 프로그램의 경우 [.NET SDK 버전](tutorial-sql-api-dotnet-bulk-import.md) 3.x에서 직접 사용할 수 있는 **대량 지원을** 사용할 수 있으며 외부 라이브러리는 필요 하지 않습니다. 

> 현재 대량 실행자 라이브러리를 사용 하 고 최신 SDK에서 대량 지원으로 마이그레이션하려는 경우 [마이그레이션 가이드](how-to-migrate-from-bulk-executor-library.md) 의 단계를 사용 하 여 응용 프로그램을 마이그레이션합니다.

이 자습서에서는 Bulk Executor .NET 라이브러리를 사용하여 Azure Cosmos 컨테이너에 문서를 가져오고 업데이트하는 데 관한 지침을 제공합니다. 대량 실행자 라이브러리 및이 라이브러리를 사용 하 여 대규모 처리량 및 저장소를 활용 하는 방법에 대 한 자세한 내용은 [bulk executor 라이브러리 개요](bulk-executor-overview.md) 문서를 참조 하세요. 이 자습서에서는 무작위로 생성 된 문서를 Azure Cosmos 컨테이너로 대량으로 가져오는 샘플 .NET 응용 프로그램이 표시 됩니다. 가져온 후에는 특정 문서 필드에서 수행할 작업으로 패치를 지정하여 가져온 데이터를 대량으로 업데이트할 수 있는 방법을 보여 줍니다.

현재 대량 실행자 라이브러리는 Azure Cosmos DB SQL API 및 Gremlin API 계정 에서만 지원 됩니다. 이 문서에서는 SQL API 계정에서 bulk executor .NET 라이브러리를 사용 하는 방법을 설명 합니다. Gremlin API 계정으로 bulk executor .NET 라이브러리를 사용 하는 방법에 대 한 자세한 내용은 [Azure Cosmos DB GREMLIN API에서 대량 작업 수행](bulk-executor-graph-dotnet.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Visual Studio 2019이 아직 설치 되지 않은 경우 [Visual studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드 하 여 사용할 수 있습니다. Visual Studio를 설치 하는 동안 "Azure 개발"을 사용 하도록 설정 했는지 확인 합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.

* Azure 구독, 요금 및 약정 없이 [무료로 Azure Cosmos DB를 사용해 볼 수 있습니다](https://azure.microsoft.com/try/cosmosdb/). 또는 끝점과 함께 [Azure Cosmos DB 에뮬레이터](https://docs.microsoft.com/azure/cosmos-db/local-emulator) 를 사용할 수 있습니다 `https://localhost:8081` . 기본 키는 [인증 요청](local-emulator.md#authenticating-requests)에 제공됩니다.

* .NET 빠른 시작 문서의 [데이터베이스 계정 만들기](create-sql-api-dotnet.md#create-account) 섹션에 설명된 단계를 사용하여 Azure Cosmos DB SQL API 계정을 만듭니다.

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 샘플 .NET 응용 프로그램을 다운로드 하 여 코드 작업으로 전환 해 보겠습니다. 이 응용 프로그램은 Azure Cosmos 계정에 저장 된 데이터에 대 한 대량 작업을 수행 합니다. 응용 프로그램을 복제 하려면 명령 프롬프트를 열고 복사 하려는 디렉터리로 이동한 후 다음 명령을 실행 합니다.

```bash
git clone https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started.git
```

복제 된 리포지토리에는 "대량 Importsample" 및 "BulkUpdateSample" 라는 두 개의 샘플이 포함 되어 있습니다. 샘플 응용 프로그램 중 하나를 열고, Azure Cosmos DB 계정의 연결 문자열로 App.config 파일에서 연결 문자열을 업데이트 하 고, 솔루션을 빌드하고 실행할 수 있습니다.

"대량 Importsample" 응용 프로그램은 임의의 문서를 생성 하 여 Azure Cosmos 계정으로 대량으로 가져옵니다. “BulkUpdateSample” 애플리케이션은 특정 문서 필드에서 수행할 작업으로 패치를 지정하여 가져온 문서를 대량으로 업데이트합니다. 다음 섹션에서는 이러한 각 샘플 앱에서 코드를 검토하겠습니다.

## <a name="bulk-import-data-to-an-azure-cosmos-account"></a>Azure Cosmos 계정으로 데이터 대량 가져오기

1. “BulkImportSample” 폴더로 이동하고 “BulkImportSample.sln” 파일을 엽니다.  

2. Azure Cosmos DB의 연결 문자열은 다음 코드와 같이 App.config 파일에서 검색 됩니다.  

   ```csharp
   private static readonly string EndpointUrl = ConfigurationManager.AppSettings["EndPointUrl"];
   private static readonly string AuthorizationKey = ConfigurationManager.AppSettings["AuthorizationKey"];
   private static readonly string DatabaseName = ConfigurationManager.AppSettings["DatabaseName"];
   private static readonly string CollectionName = ConfigurationManager.AppSettings["CollectionName"];
   private static readonly int CollectionThroughput = int.Parse(ConfigurationManager.AppSettings["CollectionThroughput"]);
   ```

   대량 가져오기는 데이터베이스 이름, 컨테이너 이름 및 App.config 파일에 지정 된 처리량 값을 사용 하 여 새 데이터베이스 및 컨테이너를 만듭니다.

3. 다음으로 DocumentClient 개체가 직접 TCP 연결 모드를 사용하여 초기화됩니다.  

   ```csharp
   ConnectionPolicy connectionPolicy = new ConnectionPolicy
   {
      ConnectionMode = ConnectionMode.Direct,
      ConnectionProtocol = Protocol.Tcp
   };
   DocumentClient client = new DocumentClient(new Uri(endpointUrl),authorizationKey,
   connectionPolicy)
   ```

4. 대기 시간 및 제한 된 요청에 대 한 높은 다시 시도 값으로 대량 실행자 개체가 초기화 됩니다. 그런 다음, 정체 제어를 해당 수명에 대한 BulkExecutor로 전달하도록 0으로 설정됩니다.  

   ```csharp
   // Set retry options high during initialization (default values).
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 30;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 9;

   IBulkExecutor bulkExecutor = new BulkExecutor(client, dataCollection);
   await bulkExecutor.InitializeAsync();

   // Set retries to 0 to pass complete control to bulk executor.
   client.ConnectionPolicy.RetryOptions.MaxRetryWaitTimeInSeconds = 0;
   client.ConnectionPolicy.RetryOptions.MaxRetryAttemptsOnThrottledRequests = 0;
   ```

5. 애플리케이션은 BulkImportAsync API를 호출합니다. .NET 라이브러리는 직렬화 된 JSON 문서 목록을 수락 하는 대량 가져오기 API의 두 오버 로드와 deserialize 된 POCO 문서의 목록을 수락 하는 오버 로드를 제공 합니다. 이러한 오버 로드 된 메서드의 정의에 대해 자세히 알아보려면 [API 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkexecutor.bulkimportasync?view=azure-dotnet)를 참조 하세요.

   ```csharp
   BulkImportResponse bulkImportResponse = await bulkExecutor.BulkImportAsync(
     documents: documentsToImportInBatch,
     enableUpsert: true,
     disableAutomaticIdGeneration: true,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```
   **BulkImportAsync 메서드는 다음과 같은 매개 변수를 수락합니다.**
   
   |**매개 변수**  |**설명** |
   |---------|---------|
   |enableUpsert    |   문서에 대해 upsert 작업을 사용 하도록 설정 하는 플래그입니다. 지정 된 ID를 가진 문서가 이미 있으면 업데이트 됩니다. 기본적으로 false로 설정됩니다.      |
   |disableAutomaticIdGeneration    |    ID의 자동 생성을 사용하지 않도록 설정하는 플래그입니다. 기본적으로 true로 설정됩니다.     |
   |maxConcurrencyPerPartitionKeyRange    | 파티션 키 범위당 최대 동시성 수준으로, null로 설정하면 라이브러리는 기본값 20을 사용하게 됩니다. |
   |maxInMemorySortingBatchSize     |  각 단계에서 API 호출에 전달 되는 문서 열거자에서 끌어올 수 있는 최대 문서 수입니다. 대량 가져오기 전에 발생 하는 메모리 내 정렬 단계의 경우이 매개 변수를 null로 설정 하면 라이브러리에서 기본 최소값 (documents, 100만)을 사용 합니다.       |
   |cancellationToken    |    대량 가져오기 작업을 정상적으로 종료 하는 취소 토큰입니다.     |

   **대량 가져오기 응답 개체 정의** 대량 가져오기 API 호출의 결과에는 다음과 같은 특성이 포함되어 있습니다.

   |**매개 변수**  |**설명**  |
   |---------|---------|
   |NumberOfDocumentsImported(long)   |  대량 가져오기 API 호출에 제공 된 총 문서에서 성공적으로 가져온 총 문서 수입니다.       |
   |TotalRequestUnitsConsumed(double)   |   대량 가져오기 API 호출에서 사용된 총 요청 단위(RU)입니다.      |
   |TotalTimeTaken(TimeSpan)    |   대량 가져오기 API 호출에서 실행을 완료 하는 데 소요 된 총 시간입니다.      |
   |BadInputDocuments(List\<object>)   |     대량 가져오기 API 호출에 성공적으로 가져오지 못한 잘못된 형식의 문서 목록입니다. 반환 된 문서를 수정 하 고 가져오기를 다시 시도 하세요. 잘못된 형식의 문서에는 ID 값이 문자열이 아닌 문서가 포함됩니다(null 또는 다른 데이터 형식이 잘못된 것으로 간주됨).    |

## <a name="bulk-update-data-in-your-azure-cosmos-account"></a>Azure Cosmos 계정에서 데이터 대량 업데이트

BulkUpdateAsync API를 사용하여 기존 문서를 업데이트할 수 있습니다. 이 예제에서는 `Name` 필드를 새 값으로 설정 하 고 `Description` 기존 문서에서 필드를 제거 합니다. 지원 되는 업데이트 작업의 전체 집합은 [API 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)를 참조 하세요.

1. “BulkUpdateSample” 폴더로 이동하고 “BulkUpdateSample.sln” 파일을 엽니다.  

2. 해당 하는 필드 업데이트 작업과 함께 업데이트 항목을 정의 합니다. 이 예에서는를 사용 하 여 `SetUpdateOperation` 필드를 업데이트 하 `Name` 고 `UnsetUpdateOperation` `Description` 모든 문서에서 필드를 제거 합니다. 특정 값으로 문서 필드 증가와 같은 다른 작업을 수행하거나, 배열 필드에 특정 값을 푸시하거나, 배열 필드에서 특정 값을 제거할 수 있습니다. 대량 업데이트 API에서 제공하는 다른 방법을 알아보려면 [API 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.bulkupdate?view=azure-dotnet)를 참조하세요.

   ```csharp
   SetUpdateOperation<string> nameUpdate = new SetUpdateOperation<string>("Name", "UpdatedDoc");
   UnsetUpdateOperation descriptionUpdate = new UnsetUpdateOperation("description");

   List<UpdateOperation> updateOperations = new List<UpdateOperation>();
   updateOperations.Add(nameUpdate);
   updateOperations.Add(descriptionUpdate);

   List<UpdateItem> updateItems = new List<UpdateItem>();
   for (int i = 0; i < 10; i++)
   {
    updateItems.Add(new UpdateItem(i.ToString(), i.ToString(), updateOperations));
   }
   ```

3. 애플리케이션은 BulkUpdateAsync API를 호출합니다. BulkUpdateAsync 메서드의 정의에 대해 자세히 알아보려면 [API 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor.ibulkexecutor.bulkupdateasync?view=azure-dotnet)를 참조 하세요.  

   ```csharp
   BulkUpdateResponse bulkUpdateResponse = await bulkExecutor.BulkUpdateAsync(
     updateItems: updateItems,
     maxConcurrencyPerPartitionKeyRange: null,
     maxInMemorySortingBatchSize: null,
     cancellationToken: token);
   ```  
   **BulkUpdateAsync 메서드는 다음과 같은 매개 변수를 수락합니다.**

   |**매개 변수**  |**설명** |
   |---------|---------|
   |maxConcurrencyPerPartitionKeyRange    |   파티션 키 범위 당 최대 동시성 수준입니다 .이 매개 변수를 null로 설정 하면 라이브러리에서 기본값 (20)을 사용 합니다.   |
   |maxInMemorySortingBatchSize    |    각 단계에서 API 호출에 전달 된 업데이트 항목 열거자에서 가져온 최대 업데이트 항목 수입니다. 대량 업데이트를 수행 하기 전에 발생 하는 메모리 내 정렬 단계에 대해이 매개 변수를 null로 설정 하면 라이브러리에서 기본 최소값 (updateItems. count, 100만)을 사용 합니다.     |
   | cancellationToken|대량 업데이트 작업을 정상적으로 종료 하는 취소 토큰입니다. |

   **대량 업데이트 응답 개체 정의** 대량 업데이트 API 호출의 결과에는 다음과 같은 특성이 포함되어 있습니다.

   |**매개 변수**  |**설명** |
   |---------|---------|
   |NumberOfDocumentsUpdated(long)    |   대량 업데이트 API 호출에 제공 된 총 문서에서 성공적으로 업데이트 된 문서 수입니다.      |
   |TotalRequestUnitsConsumed(double)   |    대량 업데이트 API 호출에서 사용 되는 총 RUs (요청 단위)입니다.    |
   |TotalTimeTaken(TimeSpan)   | 대량 업데이트 API 호출에서 실행을 완료 하는 데 소요 된 총 시간입니다. |
    
## <a name="performance-tips"></a>성능 팁 

대량 실행자 라이브러리를 사용할 때 성능을 향상 시키려면 다음 사항을 고려 하세요.

* 최상의 성능을 위해 Azure Cosmos 계정의 쓰기 지역과 동일한 지역에 있는 Azure 가상 머신에서 응용 프로그램을 실행 합니다.  

* `BulkExecutor`특정 Azure Cosmos 컨테이너에 해당 하는 단일 가상 머신 내에서 전체 응용 프로그램에 대해 단일 개체를 인스턴스화하는 것이 좋습니다.  

* 단일 대량 작업 API 실행은 클라이언트 컴퓨터의 CPU 및 네트워크 IO의 대규모 청크를 사용 하므로 내부적으로 여러 작업을 생성 하 여 발생 합니다. 대량 작업 API 호출을 실행 하는 응용 프로그램 프로세스 내에서 여러 동시 작업을 생성 하지 않도록 합니다. 단일 가상 머신에서 실행 되는 단일 대량 작업 API 호출에서 전체 컨테이너의 처리량을 사용할 수 없는 경우 (컨테이너의 처리량이 100만 r u/초 > 경우) 대량 작업 API 호출을 동시에 실행 하는 별도의 가상 머신을 만드는 것이 좋습니다.  

* `InitializeAsync()`대상 Cosmos 컨테이너의 파티션 맵을 인출 하기 위해 대량 실행자 개체를 인스턴스화한 후 메서드가 호출 되는지 확인 합니다.  

* 애플리케이션의 App.Config에서 성능 향상을 위해 **gcServer**를 사용할 수 있도록 설정되었는지 확인합니다.
  ```xml  
  <runtime>
    <gcServer enabled="true" />
  </runtime>
  ```
* 라이브러리는 로그 파일 또는 콘솔에 수집할 수 있는 추적을 내보냅니다. 둘 다 사용 하도록 설정 하려면 응용 프로그램의 App.Config 파일에 다음 코드를 추가 합니다.

  ```xml
  <system.diagnostics>
    <trace autoflush="false" indentsize="4">
      <listeners>
        <add name="logListener" type="System.Diagnostics.TextWriterTraceListener" initializeData="application.log" />
        <add name="consoleListener" type="System.Diagnostics.ConsoleTraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
  ```

## <a name="next-steps"></a>다음 단계

* NuGet 패키지 세부 정보 및 릴리스 정보에 대 한 자세한 내용은 [bulk EXECUTOR SDK 세부 정보](sql-api-sdk-bulk-executor-dot-net.md)를 참조 하세요.
