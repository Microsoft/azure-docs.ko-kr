---
title: Azure Cosmos DB C# Notebooks(미리 보기)의 기본 제공 Notebook 명령 및 기능 사용
description: 기본 제공 명령 및 기능을 사용하여 Azure Cosmos DB의 기본 제공 C# Notebooks를 사용하는 일반적인 작업을 수행하는 방법을 알아봅니다.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.custom: devx-track-python, devx-track-csharp
ms.openlocfilehash: 47ac6b4caf97b264df58732f8d031fc63091171e
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019269"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-c-notebooks-preview"></a>Azure Cosmos DB C# Notebooks(미리 보기)의 기본 제공 Notebook 명령 및 기능 사용

Azure Cosmos DB의 기본 제공 Jupyter Notebook을 사용하여 Azure Portal에서 데이터를 분석하고 시각화할 수 있습니다. 이 문서에서는 기본 제공 Notebook 명령 및 기능을 사용하여 C# Notebooks에서 일반적인 작업을 수행하는 방법을 설명합니다.

## <a name="install-a-new-nuget-package"></a>새 NuGet 패키지 설치
Azure Cosmos 계정에 대해 Notebook 지원을 사용하도록 설정한 후 새 Notebook을 열고 패키지를 설치할 수 있습니다.

새 코드 셀에서 다음 코드를 삽입하고 실행하여 ``PackageToBeInstalled``를 원하는 NuGet 패키지로 바꾸고, 원하는 경우 ``optionalVersion``을 패키지의 특정 버전으로 바꿉니다. 

```csharp
#r "nuget: PackageToBeInstalled, optionalVersion"
```

동일한 셀에 여러 NuGet 패키지를 설치할 수 있습니다. Azure Cosmos 계정 작업 영역의 모든 Notebook에서 패키지를 사용할 수 있습니다. 

현재, C# Notebooks 작업 영역에서는 NuGet 패키지의 재귀적 확인을 지원하지 않습니다. NuGet 패키지에 현재 설치되어 있지 않은 다른 NuGet 패키지에 대한 종속성이 있는 경우 해당 패키지를 부모 패키지와 함께 명시적으로 참조해야 합니다.

> [!TIP]
> Notebook에서 사용자 지정 패키지를 필요로 하는 경우에는 Notebook에 셀을 추가하여 패키지를 설치하고 첫 번째 셀로 만드는 것이 좋습니다. 이렇게 하면 기본적으로 Azure Cosmos DB에서 로드하는 다른 패키지와의 충돌 가능성이 줄어듭니다. 모든 패키지를 제거하는 [작업 영역을 다시 설정](#reset-notebooks-workspace)하는 경우에도 패키지를 다시 설치하는 것이 쉽습니다. 

## <a name="use-the-built-in-azure-cosmos-db-net-sdk"></a>기본 제공 Azure Cosmos DB .NET SDK 사용
[Azure Cosmos DB .NET SDK for SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3) 버전 3은 Azure Cosmos 계정에 대한 Notebook 환경에 설치되어 포함됩니다.

``CosmosClient``의 인스턴스를 만들어 SDK 작업을 실행합니다. 

다음은 그 예입니다.

```csharp
// Include usings
using System;
using Microsoft.Azure.Cosmos; //namespace for Azure Cosmos DB .NET V3 SDK
using System.Collections;

// Initialize a new instance of CosmosClient using the built-in account endpoint and key parameters
CosmosClient cosmosClient = new CosmosClient(Cosmos.Endpoint, Cosmos.Key);

// Create a new database and container with 400 RU/s
Database database = await cosmosClient.CreateDatabaseIfNotExistsAsync("DatabaseName");
Container container = await database.CreateContainerIfNotExistsAsync("ContainerName", "/partitionKey", 400);
```
자세히 알아보려면 [.NET V3 SDK 샘플](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage)을 참조하세요. 

> [!IMPORTANT]
> 기본 제공 Azure Cosmos DB .NET SDK는 SQL(Core) API 계정에 대해서만 지원됩니다. 다른 API의 경우 API에 해당하는 [관련 .NET 드라이버를 설치](#install-a-new-nuget-package)해야 합니다. 

## <a name="set-custom-options-using-cosmosclientoptions"></a>``CosmosClientOptions``를 사용하여 사용자 지정 옵션 설정
더 많은 유연성을 위해 사용자 지정 ``CosmosClientOptions`` 속성을 설정하고 ``CosmosClient`` 인스턴스에 전달할 수 있습니다. 이 속성을 사용하여 다음을 수행할 수 있습니다.

- 사용자 에이전트 접미사에 애플리케이션 이름을 설정하여 모든 요청에 포함시킵니다.
- 서비스에 대해 작업을 실행할 때 사용할 기본 지역을 설정합니다.
- 속도 제한된 요청을 처리하도록 사용자 지정 재시도 정책을 설정합니다.

지원되는 모든 설정은 [CosmosClientOptions API 참조](/dotnet/api/microsoft.azure.cosmos.cosmosclientoptions) 문서를 참조하세요. 다음은 `cosmosClientOptions` 속성을 설정하는 방법을 보여 주는 예제입니다.

```csharp
using Microsoft.Azure.Cosmos;

// Configure CosmosClientOptions
var cosmosClientOptions = new CosmosClientOptions
{
    ApplicationName = "ContosoNotebookAppName",
    ApplicationRegion = Regions.RegionName, // By default, this is the region you first created your account in
    MaxRetryAttemptsOnRateLimitedRequests = 9, // By default, this value is 9
};

var client = new CosmosClient(Cosmos.Endpoint, Cosmos.Key, cosmosClientOptions);
```

## <a name="access-the-account-endpoint-and-primary-key-variables"></a>계정 엔드포인트 및 기본 키 변수에 액세스
Notebook이 있는 Azure Cosmos 계정의 기본 제공 엔드포인트 및 키에 액세스할 수 있습니다.

```csharp
var key = Cosmos.Key;
var endpoint = Cosmos.Endpoint;
```

> [!IMPORTANT]
> ``Cosmos.Key`` 및 ``Cosmos.Endpoint`` 변수는 SQL API에만 적용됩니다. 다른 API의 경우 Azure Cosmos 계정의 **연결 문자열** 또는 **키** 블레이드에서 엔드포인트 및 키를 찾습니다.  

## <a name="print-console-output-in-c-code"></a>C# 코드에서 콘솔 출력 인쇄
C# 코드에서 [문자열 보간](/dotnet/csharp/language-reference/tokens/interpolated)과 함께 Display.AsMarkdown() 구문을 사용하여 셀을 실행할 때 표시되는 콘솔 출력을 인쇄할 수 있습니다. 

다음은 그 예입니다. 

```csharp
// Print text in the output
Display.AsMarkdown($"Hello world!");

// Print a variable in the output
for (int i = 0; i < 5; i++) {
    Display.AsMarkdown($"Printing out variable: {i}");
}
```
> [!IMPORTANT]
> Console.WriteLine() 구문은 현재 C# Notebooks에서 지원되지 않습니다. Display.AsMarkdown을 사용하여 프로그램에서 콘솔 출력을 인쇄합니다. 

## <a name="use-built-in-nteract-data-explorer"></a>기본 제공 nteract 데이터 탐색기 사용
기본 제공 [nteract 데이터 탐색기](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897)를 사용하여 항목 컬렉션을 필터링하고 시각화할 수 있습니다. 셀에서 시각화하려는 변수를 마지막 줄에 배치합니다. 이 변수는 셀을 실행할 때 nteract에 자동으로 표시됩니다.

예를 들어 *GetingStarted_Csharp.ipynb* 예제에서는 결과 ``telemetryEvents``를 사용하여 변수를 출력할 수 있습니다. 전체 샘플은 [GettingStarted_Csharp.ipynb notebook](https://github.com/Azure-Samples/cosmos-notebooks/blob/master/CSharp_quickstarts/GettingStarted_CSharp.ipynb)을 참조하세요. 

:::image type="content" source="media/use-notebook-features-and-commands/csharp-query-cell.png" alt-text="Csharp 쿼리 셀":::

:::image type="content" source="media/use-notebook-features-and-commands/csharp-nteract-built-in-chart.png" alt-text="nteract 데이터 탐색기":::

## <a name="use-built-in-dictionary-viewer"></a>기본 제공 사전 뷰어 사용
기본 제공 사전 뷰어를 사용하여 변수를 볼 수 있습니다. 셀에서 시각화하려는 변수를 마지막 줄에 배치합니다. 이 변수는 셀이 실행될 때 자동으로 표시됩니다.

:::image type="content" source="media/use-notebook-features-and-commands/csharp-built-in-dictionary-viewer.png" alt-text="기본 제공 사전 뷰어":::

## <a name="upload-json-items-to-a-container"></a>컨테이너에 JSON 항목 업로드
``%%upload`` 매직 명령을 사용하여 JSON 파일에서 지정된 Azure Cosmos 컨테이너로 데이터를 업로드할 수 있습니다. 다음 명령을 실행하여 다음 항목을 업로드합니다.

```csharp
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- ``{database_id}`` 및 ``{container_id}``을(를) Azure Cosmos 계정에 있는 데이터베이스 및 컨테이너의 이름으로 바꿉니다. 
- ``{url_location_of_file}``을 JSON 파일의 위치로 바꿉니다. 파일은 유효한 JSON 개체의 배열이어야 하며 공용 인터넷을 통해 액세스할 수 있어야 합니다.

다음은 그 예입니다.

```csharp
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```

출력 통계를 사용하여 항목을 업로드하는 데 사용되는 유효 RU/s를 계산할 수 있습니다. 예를 들어 38초를 초과하여 25,000 RUs를 사용하는 경우 유효 RU/s는 25,000RUs/38초 = 658RU/s입니다.

## <a name="run-another-notebook-in-current-notebook"></a>현재 Notebook에서 다른 Notebook 실행 
``%%run`` 매직 명령을 사용하여 현재 Notebook의 작업 영역에서 다른 Notebook을 실행할 수 있습니다. 다음 구문을 사용합니다.

```csharp
%%run ./path/to/{notebookName}.ipynb
```
``{notebookName}``을 실행하려는 Notebook의 이름으로 바꿉니다. Notebook은 현재 'My Notebooks' 작업 영역에 있어야 합니다. 

## <a name="reset-notebooks-workspace"></a>Notebook 작업 영역 다시 설정
Notebook 작업 영역을 기본 설정으로 다시 설정하려면 명령 모음에서 **작업 영역 다시 설정**을 선택합니다. 그러면 설치된 모든 사용자 지정 패키지가 제거되고 Jupyter 서버가 다시 시작됩니다. Notebook, 파일 및 Azure Cosmos 리소스는 영향을 받지 않습니다.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Notebook 작업 영역 다시 설정":::

## <a name="next-steps"></a>다음 단계

- [Azure Cosmos DB Jupyter notebooks](cosmosdb-jupyter-notebooks.md)의 이점에 대해 알아보기
- [Azure Cosmos DB .NET SDK for SQL API](https://github.com/Azure/azure-cosmos-dotnet-v3)에 대해 알아보기
