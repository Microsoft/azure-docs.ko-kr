---
title: Gremlin API를 사용하여 Azure Cosmos DB .NET Framework, Core 애플리케이션 빌드
description: Azure Cosmos DB에 연결 및 쿼리하는 데 사용할 수 있는 .NET Framework/Core 코드 샘플을 제시합니다.
author: luisbosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 02/21/2020
ms.author: lbosq
ms.custom: devx-track-dotnet
ms.openlocfilehash: f781018fc5fc832be92de34c3ef220517ad28d75
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89020425"
---
# <a name="quickstart-build-a-net-framework-or-core-application-using-the-azure-cosmos-db-gremlin-api-account"></a>빠른 시작: Azure Cosmos DB Gremlin API 계정을 사용한 .NET Framework 또는 Core 애플리케이션 빌드

> [!div class="op_single_selector"]
> * [Gremlin 콘솔](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.JS](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 Azure Portal을 사용하여 Azure Cosmos DB [Gremlin API](graph-introduction.md) 계정, 데이터베이스 및 그래프(컨테이너)를 만드는 방법을 보여줍니다. 그런 후 오픈 소스 드라이버 [Gremlin.Net](https://tinkerpop.apache.org/docs/3.2.7/reference/#gremlin-DotNet)을 사용하여 콘솔 앱을 빌드하고 실행합니다.  

## <a name="prerequisites"></a>필수 구성 요소

Visual Studio 2019가 아직 설치되지 않은 경우 **평가판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>그래프 추가

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 Gremlin API 앱을 복제하고, 연결 문자열을 설정하고, 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다. 

1. 명령 프롬프트를 열고, git-samples라는 새 폴더를 만든 다음 명령 프롬프트를 닫습니다.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-gremlindotnet-getting-started.git
    ```

4. 그런 다음 Visual Studio를 열고 솔루션 파일을 엽니다.

5. 프로젝트에서 NuGet 패키지를 복원합니다. Gremlin.Net 드라이버와 Newtonsoft.Json 패키지가 포함되어야 합니다.


6. Nuget 패키지 관리자 또는 [nuget 명령줄 유틸리티](https://docs.microsoft.com/nuget/install-nuget-client-tools)를 사용하여 Gremlin.Net 드라이버를 수동으로 설치할 수도 있습니다. 

    ```bash
    nuget install Gremlin.Net
    ```

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [연결 문자열 업데이트](#update-your-connection-string)로 건너뛸 수 있습니다. 

다음 코드 조각은 모두 Program.cs 파일에서 가져옵니다.

* 위에서 만든 계정을 기반으로 하여 연결 매개 변수를 설정합니다. 

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="configureConnectivity":::

* 실행할 Gremlin 명령이 사전에 나열됩니다

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineQueries":::

* 위에서 제공한 매개 변수를 사용하여 새 `GremlinServer` 및 `GremlinClient` 연결 개체를 만듭니다.

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="defineClientandServerObjects":::

* `GremlinClient` 개체를 비동기 작업에 사용하여 각 Gremlin 쿼리를 실행합니다. 이전 단계에서 정의한 사전에서 Gremlin 쿼리를 읽고 실행할 수 있습니다. 나중에 결과를 가져와서 Newtonsoft.Json 패키지의 `JsonSerializer` 클래스를 사용하여 사전 형식의 값을 읽습니다.

   :::code language="csharp" source="~/azure-cosmosdb-graph-dotnet/GremlinNetSample/Program.cs" id="executeQueries":::

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다.

1. [Azure Portal](https://portal.azure.com/)에서 그래프 데이터베이스 계정으로 이동합니다. **개요** 탭에 두 개의 엔드포인트가 표시됩니다. 
 
   **.NET SDK URI** - Microsoft.Azure.Graphs 라이브러리를 사용하여 그래프 계정에 연결한 경우 이 값이 사용됩니다. 

   **Gremlin 엔드포인트** - Gremlin.Net 라이브러리를 사용하여 그래프 계정에 연결하는 경우 이 값이 사용됩니다.

    :::image type="content" source="./media/create-graph-dotnet/endpoint.png" alt-text="엔드포인트 복사":::

   이 샘플을 실행하려면 **Gremlin 엔드포인트** 값을 복사하고, 끝 부분에 있는 포트 번호를 삭제합니다. 즉, URI가 `https://<your cosmos db account name>.gremlin.cosmosdb.azure.com`이 됩니다. 엔드포인트 값이 `testgraphacct.gremlin.cosmosdb.azure.com`과 같습니다.

1. 다음으로, **키** 탭으로 이동하고, Azure Portal에서 **기본 키** 값을 복사합니다. 

1. 계정의 URI 및 기본 키를 복사한 후 애플리케이션을 실행하는 로컬 머신의 새 환경 변수에 저장합니다. 환경 변수를 설정하려면 명령 프롬프트 창을 열고 다음 명령을 실행합니다. <Your_Azure_Cosmos_account_URI> 및 <Your_Azure_Cosmos_account_PRIMARY_KEY> 값을 바꾸세요.

   ```console
   setx EndpointUrl "<your Azure Cosmos account name>.gremlin.cosmosdb.azure.com"
   setx PrimaryKey "<Your_Azure_Cosmos_account_PRIMARY_KEY>"
   ```

1. *Program.cs* 파일을 열고, "database" 및 "container" 변수를 위에서 만든 데이터베이스 및 컨테이너 이름(그래프 이름이기도 함)으로 업데이트합니다.

    `private static string database = "your-database-name";` `private static string container = "your-container-or-graph-name";`

1. Program.cs 파일을 저장합니다. 

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 

## <a name="run-the-console-app"></a>콘솔 앱 실행

Ctrl+F5를 눌러 애플리케이션을 실행합니다. 애플리케이션이 Gremlin 쿼리 명령과 콘솔의 결과를 모두 인쇄합니다.

   그래프에 추가된 꼭짓점 및 에지가 콘솔 창에 표시됩니다. 스크립트가 완료되면 ENTER 키를 눌러 콘솔 창을 닫습니다.

## <a name="browse-using-the-data-explorer"></a>데이터 탐색기를 사용하여 찾아보기

이제 Azure Portal에서 데이터 탐색기로 돌아가고 새 그래프 데이터를 찾아 쿼리합니다.

1. [데이터 탐색기]에서 새 데이터베이스가 그래프 창에 표시됩니다. 데이터베이스 및 컨테이너 노드를 확장한 다음, **그래프**를 클릭합니다.

2. **필터 적용** 단추를 클릭하고 기본 쿼리를 사용하여 그래프의 모든 꼭짓점을 봅니다. 샘플 앱에 의해 생성된 데이터는 그래프 창에 표시됩니다.

    그래프를 확대/축소하고, 그래프 표시 공간을 확장하고, 꼭짓점을 추가하고, 표시 표면에서 꼭짓점을 이동할 수 있습니다.

    :::image type="content" source="./media/create-graph-dotnet/graph-explorer.png" alt-text="Azure Portal의 데이터 탐색기에서 그래프 보기":::

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서, Azure Cosmos DB 계정을 만들고, 데이터 탐색기를 사용하여 그래프를 만들고, 앱을 실행하는 방법을 알아보았습니다. 이제 Gremlin을 사용하여 더 복잡한 쿼리를 작성하고 강력한 그래프 순회 논리를 구현할 수 있습니다. 

> [!div class="nextstepaction"]
> [Gremlin을 사용하여 쿼리](tutorial-query-graph.md)

