---
title: Azure Cosmos DB의 API for MongoDB 및 .NET SDK를 사용하여 웹앱 빌드
description: Azure Cosmos DB의 API for MongoDB를 사용하여 연결 및 쿼리할 수 있는 .NET 코드 샘플을 제공합니다.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 105578df0a37ba1d53df2be8ebed1d1f6d77499f
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018793"
---
# <a name="quickstart-build-a-net-web-app-using-azure-cosmos-dbs-api-for-mongodb"></a>빠른 시작: Azure Cosmos DB의 MongoDB API를 사용하여 .NET 웹앱 빌드 

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Cosmos DB의 핵심인 글로벌 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 

이 빠른 시작에서는 [Azure Cosmos DB의 API for MongoDB](mongodb-introduction.md)를 사용하여 Cosmos 계정을 만드는 방법을 보여줍니다. 그런 다음, [MongoDB .NET 드라이버](https://docs.mongodb.com/ecosystem/drivers/csharp/)를 사용하여 작업 목록 웹앱을 빌드 및 배포합니다.

## <a name="prerequisites-to-run-the-sample-app"></a>샘플 앱을 실행하기 위한 필수 구성 요소

샘플을 실행하려면 [Visual Studio](https://www.visualstudio.com/downloads/) 및 유효한 Azure Cosmos DB 계정이 필요합니다.

아직 Visual Studio가 없으면 설치 시 **ASP.NET 및 웹 개발** 워크로드가 설치되는 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 

<a id="create-account"></a>
## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

이 문서에 설명된 샘플은 MongoDB.Driver 버전 2.6.1과 호환됩니다.

## <a name="clone-the-sample-app"></a>샘플 앱 복제

먼저 GitHub에서 샘플 앱을 다운로드합니다. 

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
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started.git
    ```

git을 사용하지 않으려면 [프로젝트를 ZIP 파일로 다운로드](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-getting-started/archive/master.zip)하면 됩니다.

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [연결 문자열 업데이트](#update-your-connection-string)로 건너뛸 수 있습니다. 

다음 코드 조각은 모두 DAL 디렉터리에 있는 Dal.cs 파일에서 가져옵니다.

* 클라이언트를 초기화합니다.

    ```cs
        MongoClientSettings settings = new MongoClientSettings();
        settings.Server = new MongoServerAddress(host, 10255);
        settings.UseSsl = true;
        settings.SslSettings = new SslSettings();
        settings.SslSettings.EnabledSslProtocols = SslProtocols.Tls12;

        MongoIdentity identity = new MongoInternalIdentity(dbName, userName);
        MongoIdentityEvidence evidence = new PasswordEvidence(password);

        settings.Credential = new MongoCredential("SCRAM-SHA-1", identity, evidence);

        MongoClient client = new MongoClient(settings);
    ```

* 데이터베이스 및 컬렉션을 검색합니다.

    ```cs
    private string dbName = "Tasks";
    private string collectionName = "TasksList";

    var database = client.GetDatabase(dbName);
    var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
    ```

* 모든 문서를 검색합니다.

    ```cs
    collection.Find(new BsonDocument()).ToList();
    ```

작업을 만들어 컬렉션에 삽입합니다.

   ```csharp
    public void CreateTask(MyTask task)
    {
        var collection = GetTasksCollectionForEdit();
        try
        {
            collection.InsertOne(task);
        }
        catch (MongoCommandException ex)
        {
            string msg = ex.Message;
        }
    }
   ```
   마찬가지로, [collection.UpdateOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.updateOne/index.html) 및 [collection.DeleteOne()](https://docs.mongodb.com/stitch/mongodb/actions/collection.deleteOne/index.html) 메서드를 사용하여 문서를 업데이트하고 삭제합니다. 

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다.

1. [Azure Portal](https://portal.azure.com/)의 Cosmos 계정에서 왼쪽 탐색 영역의 **연결 문자열**을 클릭한 다음, **읽기-쓰기 키**를 클릭합니다. 다음 단계에서 화면의 오른쪽에 있는 복사 단추를 사용하여 사용자, 암호 및 호스트를 Dal.cs 파일에 복사합니다.

2. **DAL** 디렉터리에서 **Dal.cs** 파일을 엽니다. 

3. 포털에서 복사 단추를 사용하여 **사용자 이름** 값을 복사하고 **Dal.cs** 파일의 **사용자 이름** 값으로 만듭니다. 

4. 그 다음, 포털에서 사용자의 **호스트** 값을 복사하고 **Dal.cs** 파일의 **호스트** 값으로 만듭니다. 

5. 마지막으로, 포털에서 사용자의 **암호** 값을 복사하고 **Dal.cs** 파일의 **암호** 값으로 만듭니다. 

이제 Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 
    
## <a name="run-the-web-app"></a>웹앱 실행

1. Visual Studio의 **솔루션 탐색기**에서 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 클릭합니다. 

2. NuGet **찾아보기** 상자에 *MongoDB.Driver*를 입력합니다.

3. 결과에서 **MongoDB.Driver** 라이브러리를 설치합니다. 그러면 MongoDB.Driver 패키지 뿐만 아니라 모든 종속성도 설치됩니다.

4. Ctrl+F5를 눌러 애플리케이션을 실행합니다. 앱이 브라우저에 표시됩니다. 

5. 브라우저에서 **만들기**를 클릭하고 작업 목록 앱에서 몇 가지 새 작업을 만듭니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Cosmos 계정을 만들고, 컬렉션을 만들고, 콘솔 앱을 실행하는 방법을 알아보았습니다. 이제 Cosmos 데이터베이스에 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](mongodb-migrate.md)
