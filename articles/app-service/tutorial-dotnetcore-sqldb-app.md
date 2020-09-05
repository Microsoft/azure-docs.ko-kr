---
title: '자습서: SQL Database를 사용한 ASP.NET Core'
description: SQL Database에 연결하여 Azure App Service에서 .NET Core 앱이 작동하도록 하는 방법에 대해 알아봅니다.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 06/20/2020
ms.custom: devx-track-csharp, mvc, cli-validate, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 27b01a86d1bc44b5adb977f10339a0f2d56a64d4
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958544"
---
# <a name="tutorial-build-an-aspnet-core-and-sql-database-app-in-azure-app-service"></a>자습서: Azure App Service에서 ASP.NET Core 및 SQL Database 앱 빌드

::: zone pivot="platform-windows"  

[Azure App Service](overview.md)는 Azure에서 확장성 뛰어난 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 .NET Core 앱을 만들고 SQL Database에 연결하는 방법을 보여줍니다. 완료되면 .NET Core MVC 앱이 Windows의 App Service에서 실행됩니다.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md)는 Linux 운영 체제를 사용하여 확장성이 뛰어난 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 .NET Core 앱을 만들고 SQL Database에 연결하는 방법을 보여줍니다. 완료되면 .NET Core MVC 앱이 Linux의 App Service에서 실행됩니다.

::: zone-end

![App Service에서 실행 중인 앱](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure에서 SQL Database 만들기
> * SQL Database에 .NET Core 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 진단 로그 스트림
> * Azure Portal에서 앱 관리

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

* <a href="https://git-scm.com/" target="_blank">Git 설치</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">최신 .NET Core 3.1 SDK 설치</a>

## <a name="create-local-net-core-app"></a>로컬 .NET Core 앱 만들기

이 단계에서는 로컬 .NET Core 프로젝트를 설정합니다.

### <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

터미널 창에서 `cd`를 사용하여 작업 디렉터리로 이동합니다.

다음 명령을 실행하여 샘플 리포지토리를 복제하고 해당 루트를 변경합니다.

```bash
git clone https://github.com/azure-samples/dotnetcore-sqldb-tutorial
cd dotnetcore-sqldb-tutorial
```

샘플 프로젝트에는 [Entity Framework Core](/ef/core/)를 사용하는 기본 CRUD(Create-Read-Update-Delete) 앱이 포함되어 있습니다.

### <a name="run-the-application"></a>애플리케이션 실행

다음 명령을 실행하여 필요한 패키지를 설치하고 데이터베이스 마이그레이션을 실행하고 애플리케이션을 시작합니다.

```bash
dotnet tool install -g dotnet-ef
dotnet ef database update
dotnet run
```

브라우저에서 `http://localhost:5000` 으로 이동합니다. **새로 만들기** 링크를 선택하고 두 개의 _할 일_ 항목을 만듭니다.

![SQL Database 연결에 성공](./media/tutorial-dotnetcore-sqldb-app/local-app-in-browser.png)

언제든지 .NET Core를 중지하려면 터미널에서 `Ctrl+C`를 입력합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-production-sql-database"></a>프로덕션 SQL Database 만들기

이 단계에서는 Azure에 SQL Database를 만듭니다. Azure에 앱을 배포하면 이 클라우드 데이터베이스가 사용됩니다.

SQL Database의 경우 이 자습서에서는 [Azure SQL 데이터베이스](/azure/sql-database/)를 사용합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-a-sql-database-logical-server"></a>SQL Database 논리 서버 만들기

Cloud Shell에서 [`az sql server create`](/cli/azure/sql/server?view=azure-cli-latest#az-sql-server-create) 명령을 사용하여 SQL Database 논리 서버를 만듭니다.

*\<server-name>* 자리 표시자를 *고유한* SQL Database 이름으로 바꿉니다. 이 이름은 전역적으로 고유한 SQL Database 엔드포인트 `<server-name>.database.windows.net`의 일부로 사용됩니다. 유효한 문자는 `a`-`z`, `0`-`9`, `-`입니다. 또한 *\<db-username>* 및 *\<db-password>* 를 선택한 사용자 이름 및 암호로 바꿉니다. 


```azurecli-interactive
az sql server create --name <server-name> --resource-group myResourceGroup --location "West Europe" --admin-user <db-username> --admin-password <db-password>
```

SQL Database 논리 서버를 만들면 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

<pre>
{
  "administratorLogin": "&lt;db-username&gt;",
  "administratorLoginPassword": null,
  "fullyQualifiedDomainName": "&lt;server-name&gt;.database.windows.net",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Sql/servers/&lt;server-name&gt;",
  "identity": null,
  "kind": "v12.0",
  "location": "westeurope",
  "name": "&lt;server-name&gt;",
  "resourceGroup": "myResourceGroup",
  "state": "Ready",
  "tags": null,
  "type": "Microsoft.Sql/servers",
  "version": "12.0"
}
</pre>

### <a name="configure-a-server-firewall-rule"></a>서버 방화벽 규칙 구성

[`az sql server firewall create`](/cli/azure/sql/server/firewall-rule?view=azure-cli-latest#az-sql-server-firewall-rule-create) 명령을 사용하여 [Azure SQL Database 서버 수준 방화벽 규칙](../azure-sql/database/firewall-configure.md)을 만듭니다. 시작 IP 및 끝 IP가 0.0.0.0으로 설정되면 방화벽이 다른 Azure 리소스에 대해서만 열립니다. 

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server <server-name> --name AllowAzureIps --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0
```

> [!TIP] 
> [앱이 사용하는 아웃바운드 IP 주소만 사용](overview-inbound-outbound-ips.md#find-outbound-ips)으로 방화벽 규칙을 훨씬 더 엄격하게 제한할 수 있습니다.
>

Cloud Shell에서 *\<your-ip-address>* 를 [로컬 IPv4 IP 주소](https://www.whatsmyip.org/)로 바꾸어 로컬 컴퓨터에서 액세스할 수 있도록 명령을 다시 실행합니다.

```azurecli-interactive
az sql server firewall-rule create --name AllowLocalClient --server <server-name> --resource-group myResourceGroup --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address>
```

### <a name="create-a-database"></a>데이터베이스 만들기

[`az sql db create`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-create) 명령을 사용하여 서버에서 [S0 성능 수준](../azure-sql/database/service-tiers-dtu.md)인 데이터베이스를 만듭니다.

```azurecli-interactive
az sql db create --resource-group myResourceGroup --server <server-name> --name coreDB --service-objective S0
```

### <a name="create-connection-string"></a>연결 문자열 만들기

[`az sql db show-connection-string`](/cli/azure/sql/db?view=azure-cli-latest#az-sql-db-show-connection-string) 명령을 사용하여 연결 문자열을 가져옵니다.

```azurecli-interactive
az sql db show-connection-string --client ado.net --server <server-name> --name coreDB
```

명령 출력에서 *\<username>* 및 *\<password>* 를 이전에 사용한 데이터베이스 관리자 자격 증명으로 바꿉니다.

.NET Core 앱에 대한 연결 문자열입니다. 나중에 사용하기 위해 복사합니다.

### <a name="configure-app-to-connect-to-production-database"></a>프로덕션 데이터베이스에 연결하도록 앱 구성

로컬 리포지토리에서 Startup.cs를 열고 다음 코드를 찾습니다.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlite("Data Source=localdatabase.db"));
```

다음 코드로 바꿉니다.

```csharp
services.AddDbContext<MyDatabaseContext>(options =>
        options.UseSqlServer(Configuration.GetConnectionString("MyDbConnection")));
```

> [!IMPORTANT]
> 확장해야 하는 프로덕션 앱의 경우 [프로덕션에서 마이그레이션 적용](/aspnet/core/data/ef-rp/migrations#applying-migrations-in-production)의 모범 사례를 따르세요.
> 

### <a name="run-database-migrations-to-the-production-database"></a>프로덕션 데이터베이스로 데이터베이스 마이그레이션 실행

앱이 현재 로컬 Sqlite 데이터베이스에 연결됩니다. 이제 Azure SQL Database를 구성했으므로 초기 마이그레이션을 다시 만들어 대상으로 설정합니다. 

리포지토리 루트에서 다음 명령을 실행합니다. *\<connection-string>* 을 이전에 만든 연결 문자열로 바꿉니다.

```
# Delete old migrations
rm Migrations -r
# Recreate migrations
dotnet ef migrations add InitialCreate

# Set connection string to production database
# PowerShell
$env:ConnectionStrings:MyDbConnection="<connection-string>"
# CMD (no quotes)
set ConnectionStrings:MyDbConnection=<connection-string>
# Bash (no quotes)
export ConnectionStrings__MyDbConnection=<connection-string>

# Run migrations
dotnet ef database update
```

### <a name="run-app-with-new-configuration"></a>새 구성으로 앱 실행

이제 프로덕션 데이터베이스에서 데이터베이스 마이그레이션이 실행되었으므로 다음을 실행하여 앱을 테스트합니다.

```
dotnet run
```

브라우저에서 `http://localhost:5000` 으로 이동합니다. **새로 만들기** 링크를 선택하고 두 개의 _할 일_ 항목을 만듭니다. 이제 앱에서 데이터를 읽고 프로덕션 데이터베이스에 쓰는 중입니다.

로컬 변경 내용을 커밋한 다음, Git 리포지토리로 커밋합니다. 

```bash
git add .
git commit -m "connect to SQLDB in Azure"
```

이제 코드를 배포할 준비가 되었습니다.

## <a name="deploy-app-to-azure"></a>Azure에 앱 배포

이 단계에서는 SQL Database 연결 .NET Core 애플리케이션을 App Service에 배포합니다.

### <a name="configure-local-git-deployment"></a>로컬 Git 배포 구성

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

::: zone pivot="platform-windows"  

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

::: zone-end

### <a name="create-a-web-app"></a>웹앱 만들기

::: zone pivot="platform-windows"  

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)]

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-linux-no-h.md)]

::: zone-end

### <a name="configure-connection-string"></a>연결 문자열 구성

Azure 앱에 연결 문자열을 설정하려면 Cloud Shell에서 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) 명령을 사용합니다. 다음 명령에서 *\<app-name>* 및 *\<connection-string>* 매개 변수를 이전에 만든 연결 문자열로 바꿉니다.

```azurecli-interactive
az webapp config connection-string set --resource-group myResourceGroup --name <app-name> --settings MyDbConnection="<connection-string>" --connection-string-type SQLAzure
```

ASP.NET Core에서는 표준 패턴을 사용하여 이 명명된 연결 문자열(`MyDbConnection`)을 사용할 수 있습니다(예: *appsettings.json*에 지정된 연결 문자열). 이 경우 `MyDbConnection`은 *appsettings.json*에도 정의되어 있습니다. App Service에서 실행되는 경우 App Service에 정의된 연결 문자열이 *appsettings.json*에 정의된 연결 문자열보다 우선적으로 적용됩니다. 코드는 지역 개발 중에 *appsettings.json* 값을 사용하고, 동일한 코드는 배포될 때 App Service 값을 사용합니다.

코드에서 연결 문자열을 참조하는 방법을 보려면 [프로덕션 데이터베이스에 연결하도록 앱 구성](#configure-app-to-connect-to-production-database)을 참조하세요.

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

::: zone pivot="platform-windows"  

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 268, done.
Counting objects: 100% (268/268), done.
Compressing objects: 100% (171/171), done.
Writing objects: 100% (268/268), 1.18 MiB | 1.55 MiB/s, done.
Total 268 (delta 95), reused 251 (delta 87), pack-reused 0
remote: Resolving deltas: 100% (95/95), done.
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '64821c3558'.
remote: Generating deployment script.
remote: Project file path: .\DotNetCoreSqlDb.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: App container will begin restart within 10 seconds.
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

::: zone-end

::: zone pivot="platform-linux"

[!INCLUDE [push-to-azure-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 273, done.
Counting objects: 100% (273/273), done.
Delta compression using up to 4 threads
Compressing objects: 100% (175/175), done.
Writing objects: 100% (273/273), 1.19 MiB | 1.85 MiB/s, done.
Total 273 (delta 96), reused 259 (delta 88)
remote: Resolving deltas: 100% (96/96), done.
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'cccecf86c5'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote: .
remote: .
remote: .
remote: Done.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;app-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/cccecf86c56493ffa594e76ea1deb3abb3702d89/log'
To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
 * [new branch]      master -> master
</pre>

::: zone-end

### <a name="browse-to-the-azure-app"></a>Azure 앱 찾아보기

웹 브라우저를 사용하여 배포된 앱으로 이동합니다.

```bash
http://<app-name>.azurewebsites.net
```

몇 가지 할 일 항목을 추가합니다.

![App Service에서 실행 중인 앱](./media/tutorial-dotnetcore-sqldb-app/azure-app-in-browser.png)

**축하합니다.** App Service에서 데이터 기반 .NET Core 앱이 실행되고 있습니다.

## <a name="update-locally-and-redeploy"></a>로컬로 업데이트 및 다시 배포

이 단계에서는 데이터베이스 스키마를 변경하고 Azure에 게시합니다.

### <a name="update-your-data-model"></a>데이터 모델 업데이트

코드 편집기에서 _Models/Todo.cs_를 엽니다. 다음 속성을 `ToDo` 클래스에 추가합니다.

```csharp
public bool Done { get; set; }
```

### <a name="rerun-database-migrations"></a>데이터베이스 마이그레이션 다시 실행

몇 가지 명령을 실행하여 프로덕션 데이터베이스를 업데이트합니다.

```bash
dotnet ef migrations add AddProperty
dotnet ef database update
```

> [!NOTE]
> 새 터미널 창을 열 경우 [프로덕션 데이터베이스로 데이터베이스 마이그레이션 실행](#run-database-migrations-to-the-production-database)에서와 같이 터미널의 프로덕션 데이터베이스로 연결 문자열을 설정해야 합니다.
>

### <a name="use-the-new-property"></a>새 속성 사용

`Done` 속성을 사용하도록 코드를 약간 변경합니다. 이 자습서에서는 간단하게 `Index` 및 `Create` 보기만 변경하여 속성의 실제 작동을 확인합니다.

_Controllers/TodosController.cs_를 엽니다.

`Create([Bind("ID,Description,CreatedDate")] Todo todo)` 메서드를 찾고 `Done`을 `Bind` 특성의 속성 목록에 추가합니다. 완료되면 `Create()` 메서드 시그니처가 다음 코드와 같이 표시됩니다.

```csharp
public async Task<IActionResult> Create([Bind("ID,Description,CreatedDate,Done")] Todo todo)
```

_Views/Todos/Create.cshtml_을 엽니다.

Razor 코드에서 `Description`의 경우 `<div class="form-group">` 요소가 표시되고 `CreatedDate`의 경우 또 다른 `<div class="form-group">` 요소가 표시됩니다. 이러한 두 요소 바로 뒤에 `Done`의 경우 또 다른 `<div class="form-group">` 요소를 추가합니다.

```csharp
<div class="form-group">
    <label asp-for="Done" class="col-md-2 control-label"></label>
    <div class="col-md-10">
        <input asp-for="Done" class="form-control" />
        <span asp-validation-for="Done" class="text-danger"></span>
    </div>
</div>
```

_Views/Todos/Index.cshtml_을 엽니다.

빈 `<th></th>` 요소를 검색합니다. 이 요소 바로 위에 다음 Razor 코드를 추가합니다.

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

`asp-action` 태그 도우미가 포함된 `<td>` 요소를 찾습니다. 이 요소 바로 위에 다음 Razor 코드를 추가합니다.

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

`Index` 및 `Create` 보기에서 변경 내용을 확인하기만 하면 됩니다.

### <a name="test-your-changes-locally"></a>변경 내용을 로컬에서 테스트

로컬로 앱 실행

```bash
dotnet run
```

> [!NOTE]
> 새 터미널 창을 열 경우 [프로덕션 데이터베이스로 데이터베이스 마이그레이션 실행](#run-database-migrations-to-the-production-database)에서와 같이 터미널의 프로덕션 데이터베이스로 연결 문자열을 설정해야 합니다.
>

브라우저에서 `http://localhost:5000/`로 이동합니다. 이제 할 일 항목을 추가하고 **완료**를 확인할 수 있습니다. 그러면 홈페이지에 완료된 항목으로 표시됩니다. `Edit` 보기를 변경하지 않았으므로 `Edit` 보기에서 `Done` 필드가 표시되지 않습니다.

### <a name="publish-changes-to-azure"></a>변경 내용을 Azure에 게시

```bash
git add .
git commit -m "added done field"
git push azure master
```

`git push`가 완료되면 App Service 앱으로 이동하여 할 일 항목을 추가해보고 **완료**를 선택합니다.

![Code First 마이그레이션 후 Azure 앱](./media/tutorial-dotnetcore-sqldb-app/this-one-is-done.png)

기존의 모든 할 일 항목이 계속 표시됩니다. ASP.NET Core 앱을 다시 게시해도 SQL Database의 기존 데이터가 손실되지 않습니다. 또한 Entity Framework Core 마이그레이션은 데이터 스키마만 변경하고 기존 데이터는 그대로 유지합니다.

## <a name="stream-diagnostic-logs"></a>진단 로그 스트림

ASP.NET Core 앱이 Azure App Service에서 실행되는 동안 콘솔 로그를 Cloud Shell에 파이프할 수 있습니다. 이 방법으로 애플리케이션 오류를 디버깅하는 데 도움이 되는 진단 메시지를 동일하게 받을 수 있습니다.

샘플 프로젝트는 다음 두 가지 변경 사항과 함께 [Azure에서 ASP.NET Core 로깅](/aspnet/core/fundamentals/logging#azure-app-service-provider)의 지침을 따릅니다.

- *DotNetCoreSqlDb.csproj*에서 `Microsoft.Extensions.Logging.AzureAppServices`에 대한 참조를 포함합니다.
- *Program.cs*에서 `loggerFactory.AddAzureWebAppDiagnostics()`를 호출합니다.

App Service에서 ASP.NET Core [로그 수준](/aspnet/core/fundamentals/logging#log-level)을 기본 수준 `Error`에서 `Information`으로 설정하려면, Cloud Shell에서 [`az webapp log config`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-config) 명령을 사용합니다.

```azurecli-interactive
az webapp log config --name <app-name> --resource-group myResourceGroup --application-logging true --level information
```

> [!NOTE]
> 프로젝트의 로그 수준은 *appsettings.json*에서 `Information`으로 설정됩니다.
> 

로그 스트리밍을 시작하려면 Cloud Shell에서 [`az webapp log tail`](/cli/azure/webapp/log?view=azure-cli-latest#az-webapp-log-tail) 명령을 사용합니다.

```azurecli-interactive
az webapp log tail --name <app-name> --resource-group myResourceGroup
```

로그 스트리밍이 시작되면 브라우저에서 Azure 앱을 새로 고쳐 일부 웹 트래픽을 가져옵니다. 이제 터미널에 파이프된 콘솔 로그가 표시될 수 있습니다. 콘솔 로그가 즉시 표시되지 않으면 30초 후에 다시 확인합니다.

언제든지 로그 스트리밍을 중지하려면 `Ctrl`+`C`를 입력합니다.

ASP.NET Core 로그를 사용자 지정하는 방법은 [ASP.NET Core에서 로깅](/aspnet/core/fundamentals/logging)을 참조하세요.

## <a name="manage-your-azure-app"></a>Azure 앱 관리

만든 앱을 보려면 [Azure Portal](https://portal.azure.com)에서 **App Services**를 검색하여 선택합니다.

![Azure Portal에서 App Services 선택](./media/tutorial-dotnetcore-sqldb-app/app-services.png)

**App Service** 페이지에서 Azure 앱의 이름을 선택합니다.

![Azure 앱에 대한 포털 탐색](./media/tutorial-dotnetcore-sqldb-app/access-portal.png)

기본적으로 포털에 앱의 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/tutorial-dotnetcore-sqldb-app/web-app-blade.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Azure에서 SQL Database 만들기
> * SQL Database에 .NET Core 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 터미널로 로그 스트림
> * Azure Portal에서 앱 관리

다음 자습서로 이동하여 사용자 지정 DNS 이름을 앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 앱에 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)

또는 다른 리소스를 확인합니다.

> [!div class="nextstepaction"]
> [ASP.NET Core 앱 구성](configure-language-dotnetcore.md)