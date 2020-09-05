---
title: '자습서: 웹앱 만들기(캐시 배제) - Azure Cache for Redis'
description: 캐시 배제 패턴을 사용하는 Azure Cache for Redis로 웹앱을 만드는 방법에 대해 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: tutorial
ms.custom: devx-track-csharp, mvc
ms.date: 03/30/2018
ms.openlocfilehash: 71f1e2b50daf333e19bc11bce119f37cec28d146
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88209193"
---
# <a name="tutorial-create-a-cache-aside-leaderboard-on-aspnet"></a>자습서: ASP.NET에서 캐시 배제 순위표 만들기

이 자습서에서는 Azure Cache for Redis로 [캐시 배제 패턴](https://docs.microsoft.com/azure/architecture/patterns/cache-aside)을 사용하는 순위표를 포함시키기 위해 [Azure Cache for Redis를 위한 ASP.NET 빠른 시작](cache-web-app-howto.md)에서 만든 *ContosoTeamStats* ASP.NET 웹앱을 업데이트합니다. 샘플 애플리케이션은 성능 개선을 위해 데이터베이스에서 팀 통계 목록을 표시하고 Azure Cache for Redis를 사용하여 캐시에서 데이터를 저장하고 검색하는 여러 가지 방법을 보여줍니다. 이 자습서를 완료하면 데이터베이스에 읽고 쓰는 웹앱을 실행하고, Azure Cache for Redis에 최적화되고, Azure에서 호스팅하게 됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure Cache for Redis를 사용하는 데이터를 저장하고 검색하여 데이터 처리량을 개선하고 데이터베이스 부하를 줄입니다.
> * Redis 정렬된 집합을 사용하여 상위 5개 팀을 검색합니다.
> * Resource Manager 템플릿을 사용하여 애플리케이션에 대한 Azure 리소스를 프로비전합니다.
> * Visual Studio를 사용하여 애플리케이션을 Azure에 게시합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 구성 요소가 있어야 합니다.

* 이 자습서는 [Azure Cache for Redis를 위한 ASP.NET 빠른 시작](cache-web-app-howto.md)에서 중단된 부분부터 계속됩니다. 아직 수행하지 않은 경우 먼저 빠른 시작을 수행합니다.
* 다음 워크로드로 [Visual Studio 2019](https://www.visualstudio.com/downloads/)를 설치합니다.
    * ASP.NET 및 웹 개발
    * Azure 개발
    * SQL Server Express LocalDB 또는 [SQL Server 2017 Express Edition](https://www.microsoft.com/sql-server/sql-server-editions-express)을 통한 .NET 데스크톱 개발.

## <a name="add-a-leaderboard-to-the-project"></a>프로젝트에 순위표 추가

자습서의 이 섹션에서는 가상 팀 목록에 대한 승리, 패배 및 동률 통계를 보고하는 순위표로 *ContosoTeamStats* 프로젝트를 구성합니다.

### <a name="add-the-entity-framework-to-the-project"></a>프로젝트에 Entity Framework 추가

1. Visual Studio에서, [Azure Cache for Redis를 위한 ASP.NET 빠른 시작](cache-web-app-howto.md)에서 만든 *ContosoTeamStats* 솔루션을 엽니다.
2. **도구 > NuGet 패키지 관리자 > 패키지 관리자 콘솔**을 클릭합니다.
3. **패키지 관리자 콘솔** 창에서 다음 명령을 실행하여 EntityFramework를 설치합니다.

    ```powershell
    Install-Package EntityFramework
    ```

이 패키지에 대한 자세한 내용은 [EntityFramework](https://www.nuget.org/packages/EntityFramework/) NuGet 페이지를 참조하세요.

### <a name="add-the-team-model"></a>팀 모델 추가

1. **솔루션 탐색기**에서 **모델**을 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 선택합니다.

1. 클래스 이름으로 `Team` 을 입력하고 **추가**를 클릭합니다.

    ![모델 클래스 추가](./media/cache-web-app-cache-aside-leaderboard/cache-model-add-class-dialog.png)

1. *Team.cs* 파일의 위쪽에 있는 `using` 문을 다음 `using` 문으로 바꿉니다.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Data.Entity;
    using System.Data.Entity.SqlServer;
    ```

1. `Team` 클래스의 정의를 업데이트된 `Team` 클래스 정의뿐만 아니라 몇몇 다른 Entity Framework 도우미 클래스도 포함하고 있는 다음 코드 조각으로 바꿉니다. 이 자습서는 Entity Framework를 통해 코드의 첫 번째 방법을 사용하고 있습니다. Entity Framework는 이 방법으로 사용자 코드에서 데이터베이스를 만들 수 있습니다. 이 자습서에 사용되는 Entity Framework에 대한 Code First 접근방식에 대한 자세한 내용은 [새 데이터베이스에 대한 Code First](/ef/ef6/modeling/code-first/workflows/new-database)를 참조하세요.

    ```csharp
    public class Team
    {
        public int ID { get; set; }
        public string Name { get; set; }
        public int Wins { get; set; }
        public int Losses { get; set; }
        public int Ties { get; set; }

        static public void PlayGames(IEnumerable<Team> teams)
        {
            // Simple random generation of statistics.
            Random r = new Random();

            foreach (var t in teams)
            {
                t.Wins = r.Next(33);
                t.Losses = r.Next(33);
                t.Ties = r.Next(0, 5);
            }
        }
    }

    public class TeamContext : DbContext
    {
        public TeamContext()
            : base("TeamContext")
        {
        }

        public DbSet<Team> Teams { get; set; }
    }

    public class TeamInitializer : CreateDatabaseIfNotExists<TeamContext>
    {
        protected override void Seed(TeamContext context)
        {
            var teams = new List<Team>
            {
                new Team{Name="Adventure Works Cycles"},
                new Team{Name="Alpine Ski House"},
                new Team{Name="Blue Yonder Airlines"},
                new Team{Name="Coho Vineyard"},
                new Team{Name="Contoso, Ltd."},
                new Team{Name="Fabrikam, Inc."},
                new Team{Name="Lucerne Publishing"},
                new Team{Name="Northwind Traders"},
                new Team{Name="Consolidated Messenger"},
                new Team{Name="Fourth Coffee"},
                new Team{Name="Graphic Design Institute"},
                new Team{Name="Nod Publishers"}
            };

            Team.PlayGames(teams);

            teams.ForEach(t => context.Teams.Add(t));
            context.SaveChanges();
        }
    }

    public class TeamConfiguration : DbConfiguration
    {
        public TeamConfiguration()
        {
            SetExecutionStrategy("System.Data.SqlClient", () => new SqlAzureExecutionStrategy());
        }
    }
    ```

1. **솔루션 탐색기**에서 **Web.config**를 두 번 클릭하여 엽니다.

    ![Web.config](./media/cache-web-app-cache-aside-leaderboard/cache-web-config.png)

1. `configuration` 섹션 내에 다음 `connectionStrings` 섹션을 추가합니다. 연결 문자열의 이름은 `TeamContext`인 Entity Framework 데이터베이스 컨텍스트 클래스의 이름과 일치해야 합니다.

    이 연결 문자열은 사용자가 [필수 구성 요소](#prerequisites)를 충족하며 Visual Studio 2019와 함께 설치된 *.NET 데스크톱 개발* 워크로드의 일부인 SQL Server Express LocalDB를 설치했다고 가정합니다.

    ```xml
    <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    다음 예제에서는 `configuration` 섹션 내에서 `configSections` 다음의 새로운 `connectionStrings` 섹션을 보여줍니다.

    ```xml
    <configuration>
        <configSections>
        <section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=6.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
        </configSections>
        <connectionStrings>
        <add name="TeamContext" connectionString="Data Source=(LocalDB)\MSSQLLocalDB;AttachDbFilename=|DataDirectory|\Teams.mdf;Integrated Security=True"     providerName="System.Data.SqlClient" />
        </connectionStrings>
        ...
    ```

### <a name="add-the-teamscontroller-and-views"></a>TeamsController 및 뷰 추가

1. Visual Studio에서 프로젝트를 빌드합니다. 

1. **솔루션 탐색기**에서 **컨트롤러** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가**, **컨트롤러**를 클릭합니다.

1. **Entity Framework를 사용하여 보기가 포함된 MVC 5 컨트롤러**를 선택하고 **추가**를 클릭합니다. **추가**를 클릭한 후 오류가 발생하면 먼저 프로젝트를 빌드했는지 확인합니다.

    ![컨트롤러 클래스 추가](./media/cache-web-app-cache-aside-leaderboard/cache-add-controller-class.png)

1. **모델 클래스** 드롭다운 목록에서 **팀(ContosoTeamStats.Models)** 을 선택합니다. **데이터 컨텍스트 클래스** 드롭다운 목록에서 **TeamContext(ContosoTeamStats.Models)** 를 선택합니다. **컨트롤러** 이름 텍스트 상자에 `TeamsController`를 입력합니다(자동으로 채워지지 않은 경우). **추가** 를 클릭하여 컨트롤러 클래스를 만들고 기본 보기를 추가합니다.

    ![컨트롤러 구성](./media/cache-web-app-cache-aside-leaderboard/cache-configure-controller.png)

1. **솔루션 탐색기**에서 **Global.asax**를 확장하고 **Global.asax.cs**를 두 번 클릭하여 엽니다.

    ![Global.asax.cs](./media/cache-web-app-cache-aside-leaderboard/cache-global-asax.png)

1. 파일의 위쪽에 있는 다음 두 `using` 문을 다른 `using` 문 아래에 추가합니다.

    ```csharp
    using System.Data.Entity;
    using ContosoTeamStats.Models;
    ```

1. 다음 코드 줄을 `Application_Start` 메서드의 끝에 추가합니다.

    ```csharp
    Database.SetInitializer<TeamContext>(new TeamInitializer());
    ```

1. **솔루션 탐색기**에서 `App_Start`를 확장하고 `RouteConfig.cs`를 두 번 클릭합니다.

    ![RouteConfig.cs](./media/cache-web-app-cache-aside-leaderboard/cache-RouteConfig-cs.png)

1. `RegisterRoutes` 메서드에서 다음 예제와 같이 `Default`경로에 있는 `controller = "Home"`을 `controller = "Teams"`로 바꿉니다.

    ```csharp
    routes.MapRoute(
        name: "Default",
        url: "{controller}/{action}/{id}",
        defaults: new { controller = "Teams", action = "Index", id = UrlParameter.Optional }
    );
    ```

### <a name="configure-the-layout-view"></a>레이아웃 뷰 구성

1. **솔루션 탐색기**에서 **보기** 폴더, **공유** 폴더를 차례로 확장하고 **_Layout.cshtml**를 두 번 클릭합니다. 

    ![_Layout.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml.png)

1. 다음 예제와 같이 `title` 요소의 내용을 변경하고 `My ASP.NET Application`을 `Contoso Team Stats`로 바꿉니다.

    ```html
    <title>@ViewBag.Title - Contoso Team Stats</title>
    ```

1. `body` 섹션에서 *Contoso Team Stats*에 대한 다음과 같은 새로운 `Html.ActionLink` 문을 *Azure Cache for Redis 테스트*에 대한 링크 바로 아래에 추가합니다.

    ```csharp
    @Html.ActionLink("Contoso Team Stats", "Index", "Teams", new { area = "" }, new { @class = "navbar-brand" })`
    ```

    ![코드 변경 내용](./media/cache-web-app-cache-aside-leaderboard/cache-layout-cshtml-code.png)

1. **Ctrl+F5** 키를 눌러 애플리케이션을 빌드 및 실행합니다. 이 버전의 애플리케이션이 데이터베이스에서 직접 결과를 읽습니다. 참고로 **새로 만들기**, **편집**, **세부 정보** 및 **삭제** 작업은 애플리케이션에 **Entity Framework를 사용하는 보기 포함 MVC 5 컨트롤러** 스캐폴드에 의해 자동으로 추가되었습니다. 자습서의 다음 섹션에서는 데이터 액세스를 최적화하고 애플리케이션에 추가 기능을 제공하기 위해 Azure Cache for Redis를 추가합니다.

    ![시작 애플리케이션](./media/cache-web-app-cache-aside-leaderboard/cache-starter-application.png)

## <a name="configure-the-app-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 앱 구성

자습서의 이 섹션에서는 [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) 캐시 클라이언트를 사용하여 Azure Cache for Redis 인스턴스에서 Contoso 팀 통계를 저장하고 검색하도록 샘플 애플리케이션을 구성합니다.

### <a name="add-a-cache-connection-to-the-teams-controller"></a>Teams Controller에 캐시 연결 추가

빠른 시작에서 이미 *StackExchange.Redis* 클라이언트 라이브러리 패키지를 설치했습니다. 또한 이미 *CacheConnection* 앱 설정을 게시된 App Service를 통해 로컬로 사용하도록 구성했습니다. *TeamsController*에서 이 동일한 클라이언트 라이브러리 및 *CacheConnection* 정보를 사용합니다.

1. **솔루션 탐색기**에서 **컨트롤러** 폴더를 확장하고 **TeamsController.cs**를 두 번 클릭하여 엽니다.

    ![팀 컨트롤러](./media/cache-web-app-cache-aside-leaderboard/cache-teamscontroller.png)

1. **TeamsController.cs**에 다음 두 `using` 문을 추가합니다.

    ```csharp
    using System.Configuration;
    using StackExchange.Redis;
    ```

1. `TeamsController` 클래스에 다음 두 속성을 추가합니다.

    ```csharp
    // Redis Connection string info
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        string cacheConnection = ConfigurationManager.AppSettings["CacheConnection"].ToString();
        return ConnectionMultiplexer.Connect(cacheConnection);
    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

### <a name="update-the-teamscontroller-to-read-from-the-cache-or-the-database"></a>캐시 또는 데이터베이스에서 읽도록 TeamsController 업데이트

이 샘플에서는 데이터베이스 또는 캐시에서 팀 통계를 검색할 수 있습니다. 팀 통계는 직렬화된 `List<Team>`으로 캐시에 저장되며 Redis 데이터 유형을 사용하여 정렬된 집합으로도 저장됩니다. 정렬된 집합에서 항목을 검색하는 경우 일부 또는 모두 검색하거나 특정 항목을 쿼리할 수 있습니다. 이 샘플에서는 정렬된 집합에서 승리 횟수 순으로 상위 5개 팀을 쿼리합니다.

Azure Cache for Redis를 사용하려는 경우 팀 통계를 여러 형식으로 캐시에 저장할 필요가 없습니다. 이 자습서에서는 여러 형식을 사용하여 데이터를 캐시하는 데 사용할 수 있는 몇 가지 방법 및 여러 가지 데이터 형식을 보여 줍니다.

1. 다음 `using` 문을 다른 `using` 문과 함께 위쪽의 `TeamsController.cs` 파일에 추가합니다.

    ```csharp
    using System.Diagnostics;
    using Newtonsoft.Json;
    ```

1. 현재 `public ActionResult Index()` 메서드 구현을 다음 구현으로 바꿉니다.

    ```csharp
    // GET: Teams
    public ActionResult Index(string actionType, string resultType)
    {
        List<Team> teams = null;

        switch(actionType)
        {
            case "playGames": // Play a new season of games.
                PlayGames();
                break;

            case "clearCache": // Clear the results from the cache.
                ClearCachedTeams();
                break;

            case "rebuildDB": // Rebuild the database with sample data.
                RebuildDB();
                break;
        }

        // Measure the time it takes to retrieve the results.
        Stopwatch sw = Stopwatch.StartNew();

        switch(resultType)
        {
            case "teamsSortedSet": // Retrieve teams from sorted set.
                teams = GetFromSortedSet();
                break;

            case "teamsSortedSetTop5": // Retrieve the top 5 teams from the sorted set.
                teams = GetFromSortedSetTop5();
                break;

            case "teamsList": // Retrieve teams from the cached List<Team>.
                teams = GetFromList();
                break;

            case "fromDB": // Retrieve results from the database.
            default:
                teams = GetFromDB();
                break;
        }

        sw.Stop();
        double ms = sw.ElapsedTicks / (Stopwatch.Frequency / (1000.0));

        // Add the elapsed time of the operation to the ViewBag.msg.
        ViewBag.msg += " MS: " + ms.ToString();

        return View(teams);
    }
    ```

1. 이전 코드 조각에 추가된 switch 문에서 `playGames`, `clearCache` 및 `rebuildDB` 작업을 구현하기 위해 다음 세 가지 메서드를 `TeamsController` 클래스에 추가합니다.

    `PlayGames` 메서드는 게임 시즌을 시뮬레이션하여 팀 통계를 업데이트하고 결과를 데이터베이스에 저장하고 이제 만료된 데이터를 캐시에서 지웁니다.

    ```csharp
    void PlayGames()
    {
        ViewBag.msg += "Updating team statistics. ";
        // Play a "season" of games.
        var teams = from t in db.Teams
                    select t;

        Team.PlayGames(teams);

        db.SaveChanges();

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `RebuildDB` 메서드는 데이터베이스를 기본 팀 집합으로 다시 초기화하고 그에 대한 통계를 생성하고 이제 만료된 데이터를 캐시에서 지웁니다.

    ```csharp
    void RebuildDB()
    {
        ViewBag.msg += "Rebuilding DB. ";
        // Delete and re-initialize the database with sample data.
        db.Database.Delete();
        db.Database.Initialize(true);

        // Clear any cached results
        ClearCachedTeams();
    }
    ```

    `ClearCachedTeams` 메서드는 캐시된 모든 팀 통계를 캐시에서 제거합니다.

    ```csharp
    void ClearCachedTeams()
    {
        IDatabase cache = Connection.GetDatabase();
        cache.KeyDelete("teamsList");
        cache.KeyDelete("teamsSortedSet");
        ViewBag.msg += "Team data removed from cache. ";
    }
    ```

1. 캐시 및 데이터베이스에서 팀 통계를 검색하는 여러 가지 방법을 구현하기 위해 다음 네 가지 메서드를 `TeamsController` 클래스에 추가합니다. 이러한 각 메서드는 `List<Team>`을 반환하며 이는 뷰를 기준으로 표시됩니다.

    `GetFromDB` 메서드는 팀 통계를 데이터베이스에서 읽습니다.
    ```csharp
    List<Team> GetFromDB()
    {
        ViewBag.msg += "Results read from DB. ";
        var results = from t in db.Teams
            orderby t.Wins descending
            select t;

        return results.ToList<Team>();
    }
    ```

    `GetFromList` 메서드는 팀 통계를 직렬화된 `List<Team>`으로 캐시에서 읽습니다. 통계가 캐시에 없는 경우 캐시 누락이 발생합니다. 캐시 누락의 경우 팀 통계를 데이터베이스에서 읽은 다음, 다음 요청에 대한 캐시에 저장합니다. 이 샘플에서는 .NET 개체를 캐시에 대해 직렬화하는 데 JSON.NET 직렬화를 사용합니다. 자세한 내용은 [Azure Cache for Redis에서 .NET 개체로 작업하는 방법](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache)을 참조하세요.

    ```csharp
    List<Team> GetFromList()
    {
        List<Team> teams = null;

        IDatabase cache = Connection.GetDatabase();
        string serializedTeams = cache.StringGet("teamsList");
        if (!String.IsNullOrEmpty(serializedTeams))
        {
            teams = JsonConvert.DeserializeObject<List<Team>>(serializedTeams);

            ViewBag.msg += "List read from cache. ";
        }
        else
        {
            ViewBag.msg += "Teams list cache miss. ";
            // Get from database and store in cache
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            cache.StringSet("teamsList", JsonConvert.SerializeObject(teams));
        }
        return teams;
    }
    ```

    `GetFromSortedSet` 메서드는 캐시된 정렬된 집합에서 팀 통계를 읽습니다. 캐시 누락이 있는 경우 팀 통계를 데이터베이스에서 읽은 후 정렬된 집합으로 캐시에 저장합니다.

    ```csharp
    List<Team> GetFromSortedSet()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();
        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", order: Order.Descending);
        if (teamsSortedSet.Count() > 0)
        {
            ViewBag.msg += "Reading sorted set from cache. ";
            teams = new List<Team>();
            foreach (var t in teamsSortedSet)
            {
                Team tt = JsonConvert.DeserializeObject<Team>(t.Element);
                teams.Add(tt);
            }
        }
        else
        {
            ViewBag.msg += "Teams sorted set cache miss. ";

            // Read from DB
            teams = GetFromDB();

            ViewBag.msg += "Storing results to cache. ";
            foreach (var t in teams)
            {
                Console.WriteLine("Adding to sorted set: {0} - {1}", t.Name, t.Wins);
                cache.SortedSetAdd("teamsSortedSet", JsonConvert.SerializeObject(t), t.Wins);
            }
        }
        return teams;
    }
    ```

    `GetFromSortedSetTop5` 메서드는 캐시된 정렬된 집합에서 상위 5개 팀을 읽습니다. 먼저 캐시에서 `teamsSortedSet` 키의 유무를 확인합니다. 이 키가 없는 경우 `GetFromSortedSet` 메서드를 호출하여 팀 통계를 읽고 캐시에 저장합니다. 그런 다음, 캐시된 정렬된 집합에 대해 반환된 상위 5개 팀을 쿼리합니다.

    ```csharp
    List<Team> GetFromSortedSetTop5()
    {
        List<Team> teams = null;
        IDatabase cache = Connection.GetDatabase();

        // If the key teamsSortedSet is not present, this method returns a 0 length collection.
        var teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        if(teamsSortedSet.Count() == 0)
        {
            // Load the entire sorted set into the cache.
            GetFromSortedSet();

            // Retrieve the top 5 teams.
            teamsSortedSet = cache.SortedSetRangeByRankWithScores("teamsSortedSet", stop: 4, order: Order.Descending);
        }

        ViewBag.msg += "Retrieving top 5 teams from cache. ";
        // Get the top 5 teams from the sorted set
        teams = new List<Team>();
        foreach (var team in teamsSortedSet)
        {
            teams.Add(JsonConvert.DeserializeObject<Team>(team.Element));
        }
        return teams;
    }
    ```

### <a name="update-the-create-edit-and-delete-methods-to-work-with-the-cache"></a>캐시로 작업하도록 Create, Edit 및 Delete 메서드 업데이트

이 샘플의 일부로 생성된 스캐폴딩 코드는 팀을 추가, 편집 및 삭제하는 메서드를 포함하고 있습니다. 팀이 추가, 편집 또는 제거될 때마다 캐시의 데이터가 만료됩니다. 이 섹션에서는 캐시된 팀이 새로 고침되도록 이러한 세 가지 메서드를 수정하여 캐시된 팀을 지웁니다.

1. `TeamsController` 클래스의 `Create(Team team)` 메서드를 찾아갑니다. 다음 예제와 같이 `ClearCachedTeams` 메서드 호출을 추가합니다.

    ```csharp
    // POST: Teams/Create
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Create([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Teams.Add(team);
            db.SaveChanges();
            // When a team is added, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }

        return View(team);
    }
    ```

2. `TeamsController` 클래스의 `Edit(Team team)` 메서드를 찾아갑니다. 다음 예제와 같이 `ClearCachedTeams` 메서드 호출을 추가합니다.

    ```csharp
    // POST: Teams/Edit/5
    // To protect from overposting attacks, please enable the specific properties you want to bind to, for 
    // more details see https://go.microsoft.com/fwlink/?LinkId=317598.
    [HttpPost]
    [ValidateAntiForgeryToken]
    public ActionResult Edit([Bind(Include = "ID,Name,Wins,Losses,Ties")] Team team)
    {
        if (ModelState.IsValid)
        {
            db.Entry(team).State = EntityState.Modified;
            db.SaveChanges();
            // When a team is edited, the cache is out of date.
            // Clear the cached teams.
            ClearCachedTeams();
            return RedirectToAction("Index");
        }
        return View(team);
    }
    ```

3. `TeamsController` 클래스의 `DeleteConfirmed(int id)` 메서드를 찾아갑니다. 다음 예제와 같이 `ClearCachedTeams` 메서드 호출을 추가합니다.

    ```csharp
    // POST: Teams/Delete/5
    [HttpPost, ActionName("Delete")]
    [ValidateAntiForgeryToken]
    public ActionResult DeleteConfirmed(int id)
    {
        Team team = db.Teams.Find(id);
        db.Teams.Remove(team);
        db.SaveChanges();
        // When a team is deleted, the cache is out of date.
        // Clear the cached teams.
        ClearCachedTeams();
        return RedirectToAction("Index");
    }
    ```

### <a name="add-caching-methods-to-the-teams-index-view"></a>팀 인덱스 뷰에 캐싱 메서드 추가

1. **솔루션 탐색기**에서 **보기** 폴더, **팀** 폴더를 차례로 확장한 다음 **Index.cshtml**을 두 번 클릭합니다.

    ![Index.cshtml](./media/cache-web-app-cache-aside-leaderboard/cache-views-teams-index-cshtml.png)

1. 파일의 위쪽 부근에서 다음 단락 요소를 찾습니다.

    ![작업 테이블](./media/cache-web-app-cache-aside-leaderboard/cache-teams-index-table.png)

    이 링크는 새 팀을 만듭니다. 단락 요소를 다음 테이블로 바꿉니다. 이 테이블에는 새 게임 시즌 실행, 캐시 지우기, 여러 형식의 캐시에서 팀 검색, 데이터베이스에서 팀 검색 및 새 샘플 데이터를 사용하여 데이터베이스 다시 빌드를 수행할 수 있는 작업 링크가 있습니다.

    ```html
    <table class="table">
        <tr>
            <td>
                @Html.ActionLink("Create New", "Create")
            </td>
            <td>
                @Html.ActionLink("Play Season", "Index", new { actionType = "playGames" })
            </td>
            <td>
                @Html.ActionLink("Clear Cache", "Index", new { actionType = "clearCache" })
            </td>
            <td>
                @Html.ActionLink("List from Cache", "Index", new { resultType = "teamsList" })
            </td>
            <td>
                @Html.ActionLink("Sorted Set from Cache", "Index", new { resultType = "teamsSortedSet" })
            </td>
            <td>
                @Html.ActionLink("Top 5 Teams from Cache", "Index", new { resultType = "teamsSortedSetTop5" })
            </td>
            <td>
                @Html.ActionLink("Load from DB", "Index", new { resultType = "fromDB" })
            </td>
            <td>
                @Html.ActionLink("Rebuild DB", "Index", new { actionType = "rebuildDB" })
            </td>
        </tr>
    </table>
    ```

1. **Index.cshtml** 파일의 아래쪽으로 스크롤하고 다음 `tr` 요소를 파일의 마지막 테이블에서 마지막 행이 되도록 추가합니다.

    ```html
    <tr><td colspan="5">@ViewBag.Msg</td></tr>
    ```
    이 행은 현재 작업에 대한 상태 보고서를 포함하는 `ViewBag.Msg` 값을 표시합니다. `ViewBag.Msg`은 이전 단계에서 작업 링크를 클릭할 때 설정됩니다.

    ![상태 메시지](./media/cache-web-app-cache-aside-leaderboard/cache-status-message.png)

1. **F6** 을 눌러 프로젝트를 빌드합니다.

## <a name="run-the-app-locally"></a>로컬에서 앱 실행하기

팀을 지원하기 위해 추가된 기능을 확인하려면 사용자 컴퓨터에 애플리케이션을 로컬로 실행합니다.

이 테스트에서 애플리케이션 및 데이터베이스는 모두 로컬로 실행됩니다. 단, Azure Cache for Redis는 Azure에서 원격으로 호스팅됩니다. 따라서 캐시는 데이터베이스의 성능을 약간 저하시킬 가능성이 있습니다. 최상의 성능을 위해 클라이언트 애플리케이션 및 Azure Cache for Redis 인스턴스가 동일한 위치에 있어야 합니다. 다음 섹션에서는 모든 리소스를 Azure에 배포하여 캐시를 사용한 성능 향상을 확인합니다.

로컬로 앱을 실행하려면 다음을 수행합니다.

1. **Ctrl+F5**를 눌러 애플리케이션을 실행합니다.

    ![로컬로 실행되는 앱](./media/cache-web-app-cache-aside-leaderboard/cache-local-application.png)

1. 뷰에 추가된 새 메서드를 각각 테스트합니다. 캐시는 이러한 테스트에서 원격이므로 데이터베이스는 캐시보다 더 나은 성능을 제공해야 합니다.

## <a name="publish-and-run-in-azure"></a>Azure에서 게시 및 실행

### <a name="provision-a-database-for-the-app"></a>앱용 데이터베이스 프로비전

이 섹션에서는 Azure에서 호스팅되는 동안 사용할 앱을 위해 SQL Database의 새 데이터베이스를 프로비저닝합니다.

1. [Azure Portal](https://portal.azure.com/)의 왼쪽 위 모서리에서 **리소스 만들기**를 클릭합니다.

1. **새로 만들기** 페이지에서 **데이터베이스** > **SQL Database**를 클릭합니다.

1. 새 SQL Database에 대한 다음 설정을 사용합니다.

   | 설정       | 제안 값 | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **데이터베이스 이름** | *ContosoTeamsDatabase* | 유효한 데이터베이스 이름은 [데이터베이스 식별자](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
   | **구독** | *구독*  | 캐시를 만들고 App Service를 호스팅하는 데 사용한 것과 동일한 구독을 선택합니다. |
   | **리소스 그룹**  | *TestResourceGroup* | **기존 항목 사용**을 클릭하고 캐시와 App Service를 배치한 위치와 동일한 리소스 그룹을 사용합니다. |
   | **원본 선택** | **빈 데이터베이스** | 빈 데이터베이스에서 시작합니다. |

1. **서버**에서 **필요한 설정 구성** > **새 서버 만들기**를 클릭하고 다음 정보를 입력한 다음, **선택** 단추를 클릭합니다.

   | 설정       | 제안 값 | Description |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **서버 이름** | 전역적으로 고유한 이름 | 유효한 서버 이름은 [명명 규칙 및 제한 사항](/azure/architecture/best-practices/resource-naming)을 참조하세요. |
   | **서버 관리자 로그인** | 유효한 이름 | 유효한 로그인 이름은 [데이터베이스 식별자](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers)를 참조하세요. |
   | **암호** | 유효한 암호 | 암호는 8자 이상이어야 하며 대문자, 소문자, 숫자 및 영숫자가 아닌 문자 범주 중 세 가지 범주의 문자를 포함해야 합니다. |
   | **위치** | *미국 동부* | 캐시와 App Service를 만든 것과 동일한 지역을 선택합니다. |

1. **대시보드에 고정**을 클릭한 다음, **만들기**를 클릭하여 새 데이터베이스와 서버를 만듭니다.

1. 새 데이터베이스가 생성되면 **데이터베이스 연결 문자열 표시**를 클릭하고 **ADO.NET** 연결 문자열을 복사합니다.

    ![연결 문자열 표시](./media/cache-web-app-cache-aside-leaderboard/cache-show-connection-strings.png)

1. Azure Portal에서 App Service로 이동하고 **애플리케이션 설정**을 클릭한 다음, 연결 문자열 섹션에서 **새 연결 문자열 추가**를 클릭합니다.

1. Entity Framework 데이터베이스 컨텍스트 클래스와 일치하도록 *TeamContext*라는 새 연결 문자열을 추가합니다. 새 데이터베이스에 대한 연결 문자열을 값으로 붙여넣습니다. 연결 문자열에서 다음과 같은 자리 표시자를 바꾸었는지 확인하고 **저장**을 클릭합니다.

    | 자리 표시자 | 제안 값 |
    | --- | --- |
    | *{your_username}* | 방금 만든 서버에 대한 **서버 관리자 로그인**을 사용합니다. |
    | *{your_password}* | 방금 만든 서버에 대한 암호를 사용합니다. |

    애플리케이션 설정으로 사용자 이름 및 암호를 추가하면 사용자 이름 및 암호가 코드에 포함되지 않습니다. 이 방법은 해당 자격 증명을 보호하는 데 유용합니다.

### <a name="publish-the-application-updates-to-azure"></a>애플리케이션 업데이트를 Azure에 게시

자습서의 이 단계에서는 Azure에 애플리케이션 업데이트를 게시하고 클라우드에서 실행합니다.

1. Visual Studio에서 **ContosoTeamStats** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

    ![게시](./media/cache-web-app-cache-aside-leaderboard/cache-publish-app.png)

2. 빠른 시작에서 만든 것과 동일한 게시 프로필을 사용하려면 **게시**를 클릭합니다.

3. 게시가 완료되면 Visual Studio는 사용자의 기본 웹 브라우저에서 앱을 시작합니다.

    ![캐시 추가됨](./media/cache-web-app-cache-aside-leaderboard/cache-added-to-application.png)

    다음 테이블은 샘플 애플리케이션에서 각 작업 링크를 설명합니다.

    | 작업 | Description |
    | --- | --- |
    | 새로 만들기 |새 팀을 만듭니다. |
    | 시즌 재생 |게임 시즌을 재생하고 팀 통계를 업데이트하고 만료된 팀 데이터를 캐시에서 지웁니다. |
    | 캐시 지우기 |캐시에서 팀 통계를 지웁니다. |
    | 캐시에서 목록 |캐시에서 팀 통계를 검색합니다. 캐시 누락이 있는 경우 데이터베이스에서 통계를 로드하고 다음에 캐시에 저장합니다. |
    | 캐시에서 정렬된 집합 |정렬된 집합을 사용하여 캐시에서 팀 통계를 검색합니다. 캐시 누락이 있는 경우 데이터베이스에서 통계를 로드하고 정렬된 집합을 사용하여 캐시에 저장합니다. |
    | 캐시에서 상위 5개 팀 |정렬된 집합을 사용하여 캐시에서 상위 5개 팀을 검색합니다. 캐시 누락이 있는 경우 데이터베이스에서 통계를 로드하고 정렬된 집합을 사용하여 캐시에 저장합니다. |
    | DB에서 로드 |데이터베이스에서 팀 통계를 검색합니다. |
    | DB 다시 빌드 |데이터베이스를 다시 빌드하고 샘플 팀 데이터와 함께 다시 로드합니다. |
    | 편집 / 세부 정보 / 삭제 |팀을 편집하고 팀에 대한 세부 정보를 보고 팀을 삭제합니다. |

일부 작업을 클릭하고 다른 출처의 데이터 검색을 실험합니다. 데이터베이스 및 캐시에서 데이터를 검색하는 여러 방법을 완료하는 데 걸리는 시간 차이는 없습니다.

## <a name="clean-up-resources"></a>리소스 정리

샘플 자습서 애플리케이션을 마쳤을 때 비용 및 리소스를 절감하기 위해 사용한 Azure 리소스를 삭제할 수 있습니다. 모든 리소스는 동일한 리소스 그룹에 포함되어야 합니다. 리소스 그룹을 삭제하면 한 번에 모두 삭제할 수 있습니다. 이 항목의 지침에서는 *TestResources*라는 리소스 그룹을 사용했습니다.

> [!IMPORTANT]
> 리소스 그룹 삭제는 취소할 수 없으며 해당 리소스 그룹 및 해당 그룹 안에 있는 모든 리소스는 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 유지하려는 리소스가 포함된 기존 리소스 그룹 내에 이 샘플을 호스팅하기 위한 리소스를 만든 경우 해당 블레이드에서 각 리소스를 개별적으로 삭제할 수 있습니다.
>

1. [Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.
2. **항목 필터링...** 텍스트 상자에 리소스 그룹의 이름을 입력합니다.
3. 리소스 그룹의 오른쪽에 있는 **...** 을 클릭하여 **리소스 그룹 삭제**를 클릭합니다.

    ![DELETE](./media/cache-web-app-cache-aside-leaderboard/cache-delete-resource-group.png)

4. 리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 입력하여 확인한 후 **삭제**를 클릭합니다.

    잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Cache for Redis의 크기를 조정하는 방법](./cache-how-to-scale.md)
