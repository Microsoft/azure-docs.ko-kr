---
title: .NET 및 Azure Cosmos DB의 API for MongoDB를 사용하여 Xamarin 앱 빌드
description: Azure Cosmos DB의 MongoDB API를 사용하여 연결하고 쿼리할 수 있는 Xamarin 코드 샘플을 제공합니다.
author: codemillmatt
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 03/16/2020
ms.author: masoucou
ms.custom: devx-track-csharp
ms.openlocfilehash: 0d5e8643abf41a488dd5f9b8cbc39c3f1bac1c0a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005040"
---
# <a name="quickstart-build-a-xamarinforms-app-with-net-sdk-and-azure-cosmos-dbs-api-for-mongodb"></a>빠른 시작: .NET SDK 및 Azure Cosmos DB의 MongoDB API를 사용하여 Xamarin.Forms 앱 빌드

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB는 전 세계에 배포된 Microsoft의 다중 모델 데이터베이스 서비스입니다. Azure Cosmos DB의 핵심인 전역 배포 및 수평적 크기 조정 기능의 이점을 활용하여 문서, 키/값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다.

이 빠른 시작에서는 Azure Portal을 사용하여 [Azure Cosmos DB의 MongoDB API로 구성된 Cosmos 계정](mongodb-introduction.md), 문서 데이터베이스 및 컬렉션을 만드는 방법을 보여 줍니다. 그런 다음, [MongoDB .NET 드라이버](https://docs.mongodb.com/ecosystem/drivers/csharp/)를 사용하여 todo 앱인 Xamarin.Forms 앱을 빌드합니다.

## <a name="prerequisites-to-run-the-sample-app"></a>샘플 앱을 실행하기 위한 필수 구성 요소

샘플을 실행하려면 [Visual Studio](https://www.visualstudio.com/downloads/) 또는 [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/) 및 유효한 Azure CosmosDB 계정이 필요합니다.

아직 Visual Studio가 없으면 설치 시 **.NET을 사용한 모바일 개발** 워크로드가 설치된 [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드합니다.

Mac에서 작업하려는 경우 [Mac용 Visual Studio](https://visualstudio.microsoft.com/vs/mac/)를 다운로드하여 설치 프로그램을 실행합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

<a id="create-account"></a>

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

이 문서에 설명된 샘플은 MongoDB.Driver 버전 2.6.1과 호환됩니다.

## <a name="clone-the-sample-app"></a>샘플 앱 복제

먼저 GitHub에서 샘플 앱을 다운로드합니다. 이 앱은 MongoDB의 문서 스토리지 모델을 사용하여 todo 앱을 구현합니다.



# <a name="windows"></a>[Windows](#tab/windows)

1. Windows에서 명령 프롬프트를 열거나 Mac에서 터미널을 열고 git-samples라는 새 폴더를 만든 다음, 창을 닫습니다.

    ```batch
    md "C:\git-samples"
    ```

    ```bash
    mkdir '$home\git-samples\
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started.git
    ```

git을 사용하지 않으려면 [프로젝트를 ZIP 파일로 다운로드](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-xamarin-getting-started/archive/master.zip)하면 됩니다.

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [연결 문자열 업데이트](#update-your-connection-string)로 건너뛸 수 있습니다.

다음 코드 조각은 모두 `MongoService` 클래스에서 가져오고 src/TaskList.Core/Services/MongoService.cs 경로에 있습니다.

* Mongo 클라이언트를 초기화합니다.
    ```cs
    MongoClientSettings settings = MongoClientSettings.FromUrl(
        new MongoUrl(APIKeys.ConnectionString)
    );

    settings.SslSettings =
        new SslSettings() { EnabledSslProtocols = SslProtocols.Tls12 };

    settings.RetryWrites = false;

    MongoClient mongoClient = new MongoClient(settings);
    ```

* 데이터베이스 및 컬렉션에 대한 참조를 검색합니다. 아직 존재하지 않는 경우 MongoDB .NET SDK는 자동으로 데이터베이스 및 컬렉션을 모두 만듭니다.
    ```cs
    string dbName = "MyTasks";
    string collectionName = "TaskList";

    var db = mongoClient.GetDatabase(dbName);

    var collectionSettings = new MongoCollectionSettings {
        ReadPreference = ReadPreference.Nearest
    };

    tasksCollection = db.GetCollection<MyTask>(collectionName, collectionSettings);
    ```
* 모든 문서를 목록으로 검색합니다.
    ```cs
    var allTasks = await TasksCollection
                    .Find(new BsonDocument())
                    .ToListAsync();
    ```

* 특정 문서를 쿼리합니다.
    ```cs
    public async Task<List<MyTask>> GetIncompleteTasksDueBefore(DateTime date)
    {
        var tasks = await TasksCollection
                        .AsQueryable()
                        .Where(t => t.Complete == false)
                        .Where(t => t.DueDate < date)
                        .ToListAsync();

        return tasks;
    }
    ```

* 작업을 만들어 컬렉션에 삽입합니다.
    ```cs
    public async Task CreateTask(MyTask task)
    {
        await TasksCollection.InsertOneAsync(task);
    }
    ```

* 컬렉션에서 작업을 업데이트합니다.
    ```cs
    public async Task UpdateTask(MyTask task)
    {
        await TasksCollection.ReplaceOneAsync(t => t.Id.Equals(task.Id), task);
    }
    ```

* 컬렉션에서 작업을 삭제합니다.
    ```cs
    public async Task DeleteTask(MyTask task)
    {
        await TasksCollection.DeleteOneAsync(t => t.Id.Equals(task.Id));
    }
    ```

<a id="update-your-connection-string"></a>

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다.

1. [Azure Portal](https://portal.azure.com/)의 Azure Cosmos DB 계정에서 왼쪽 탐색 영역의 **연결 문자열**을 클릭한 다음 **읽기-쓰기 키**를 클릭합니다. 화면의 오른쪽에서 복사 단추를 사용하여 다음 단계에서 기본 연결 문자열을 복사합니다.

2. **TaskList.Core** 프로젝트의 **Helpers** 디렉터리에서 **APIKeys.cs** 파일을 엽니다.

3. (복사 단추를 사용하여) 포털에서 **기본 연결 문자열** 값을 복사하고 **APIKeys.cs** 파일에서 **ConnectionString** 필드의 값으로 만듭니다.

4. 연결 문자열에서 `&replicaSet=globaldb`를 제거합니다. 쿼리 문자열에서 해당 값을 제거하지 않으면 런타임 오류가 발생합니다.

> [!IMPORTANT]
> 런타임 오류를 방지하려면 연결 문자열의 쿼리 문자열에서 `&replicaSet=globaldb` 키/값 쌍을 제거해야 합니다.

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다.

## <a name="run-the-app"></a>앱 실행

### <a name="visual-studio-2019"></a>Visual Studio 2019

1. Visual Studio의 **솔루션 탐색기**에서 각 프로젝트를 마우스 오른쪽 단추로 클릭한 다음, **NuGet 패키지 관리**를 클릭합니다.
2. **모든 NuGet 패키지 복원**을 클릭합니다.
3. **TaskList.Android**를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다.
4. F5 키를 눌러 애플리케이션 디버깅을 시작합니다.
5. iOS에서 실행하려는 경우 먼저 컴퓨터는 Mac에 연결됩니다(수행하는 방법에 대한 [지침](https://docs.microsoft.com/xamarin/ios/get-started/installation/windows/introduction-to-xamarin-ios-for-visual-studio)은 다음과 같음).
6. **TaskList.iOS** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 선택합니다.
7. F5 키를 클릭하여 애플리케이션 디버깅을 시작합니다.

### <a name="visual-studio-for-mac"></a>Mac용 Visual Studio

1. 플랫폼 드롭다운 목록에서 실행할 플랫폼에 따라 TaskList.iOS 또는 TaskList.Android를 선택합니다.
2. cmd+Enter 키를 눌러 애플리케이션 디버깅을 시작합니다.

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cosmos DB 계정을 만들고, MongoDB의 API를 사용하여 Xamarin.Forms 앱을 실행하는 방법을 알아보았습니다. 이제 사용자의 Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Cosmos DB의 MongoDB API로 구성된 Azure Cosmos DB로 데이터 가져오기](mongodb-migrate.md)
