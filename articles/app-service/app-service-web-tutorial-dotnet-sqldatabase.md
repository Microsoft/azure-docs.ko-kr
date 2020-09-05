---
title: '자습서: Azure SQL Database를 사용하는 ASP.NET 앱'
description: C# ASP.NET 앱을 Azure 및 Azure SQL Database에 배포하는 방법을 알아봅니다.
ms.assetid: 03c584f1-a93c-4e3d-ac1b-c82b50c75d3e
ms.devlang: csharp
ms.topic: tutorial
ms.date: 06/25/2018
ms.custom: devx-track-csharp, mvc, devcenter, vs-azure, seodec18
ms.openlocfilehash: 90becfb79973ba45851b0e30384b0f05a7b887e3
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962250"
---
# <a name="tutorial-deploy-an-aspnet-app-to-azure-with-azure-sql-database"></a>자습서: Azure SQL Database를 사용하여 Azure에 ASP.NET 앱 배포

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 App Service에서 데이터 기반 ASP.NET 앱을 개발하고 [Azure SQL Database](../azure-sql/database/sql-database-paas-overview.md)에 연결하는 방법을 보여줍니다. 완료되면 ASP.NET 앱이 Azure에서 실행되고 SQL Database에 연결됩니다.

![Azure App Service의 게시된 ASP.NET 애플리케이션](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * Azure SQL Database에서 데이터베이스 만들기
> * SQL Database에 ASP.NET 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 터미널로 로그 스트림
> * Azure Portal에서 앱 관리

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

**ASP.NET 및 웹 개발** 워크로드가 있는 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019</a>를 설치합니다.

Visual Studio가 이미 설치된 경우 **도구** > **도구 및 기능 가져오기**를 클릭하여 Visual Studio에서 워크로드를 추가합니다.

## <a name="download-the-sample"></a>샘플 다운로드

* [샘플 프로젝트를 다운로드합니다](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/archive/master.zip).
* *dotnet-sqldb-tutorial-master.zip* 파일을 추출(압축 해제)합니다.

샘플 프로젝트에는 [Entity Framework Code First](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)를 사용하는 기본 [ASP.NET MVC](https://www.asp.net/mvc) CRUD(Create-Read-Update-Delete) 앱이 포함되어 있습니다.

### <a name="run-the-app"></a>앱 실행

Visual Studio에서 *dotnet-sqldb-tutorial-master/DotNetAppSqlDb.sln* 파일을 엽니다.

디버깅 없이 앱을 실행하려면 `Ctrl+F5`를 입력합니다. 앱이 기본 브라우저에 표시됩니다. **새로 만들기** 링크를 선택하고 두 개의 *할 일* 항목을 만듭니다.

![새 ASP.NET 프로젝트 대화 상자](media/app-service-web-tutorial-dotnet-sqldatabase/local-app-in-browser.png)

**편집**, **세부 정보** 및 **삭제** 링크를 테스트합니다.

앱은 데이터베이스 컨텍스트를 사용하여 데이터베이스에 연결합니다. 이 샘플의 데이터베이스 컨텍스트에는 `MyDbConnection`이라는 연결 문자열이 사용됩니다. 연결 문자열은 *Web.config* 파일에서 설정되고 *Models\MyDatabaseContext.cs* 파일에서 참조됩니다. 연결 문자열 이름은 이 자습서의 뒷부분에서 Azure 앱을 Azure SQL Database에 연결하는 데 사용됩니다.

## <a name="publish-aspnet-application-to-azure"></a>Azure에 ASP.NET 애플리케이션 게시

**솔루션 탐색기**에서 **DotNetAppSqlDb** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

![솔루션 탐색기에서 게시](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

**Microsoft Azure App Service**를 선택했는지 확인하고 **게시**를 클릭합니다.

![프로젝트 개요 페이지에서 게시](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-to-app-service.png)

게시하는 경우 Azure에서 ASP.NET 앱을 실행하는 데 필요한 모든 Azure 리소스를 만들 수 있는 유용한 **App Service 만들기** 대화 상자가 열립니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

**App Service 만들기** 대화 상자에서 **계정 추가**를 클릭한 다음 Azure 구독에 로그인합니다. 이미 Microsoft 계정에 로그인한 경우 계정에 Azure 구독이 있는지 확인합니다. 로그인된 Microsoft 계정에 Azure 구독이 없는 경우 클릭하여 올바른 계정을 추가합니다.

> [!NOTE]
> 이미 로그인한 경우 아직 **만들기**를 선택하지 마십시오.

![Azure에 로그인](./media/app-service-web-tutorial-dotnet-sqldatabase/sign-in-azure.png)

### <a name="configure-the-web-app-name"></a>웹앱 이름 구성

생성된 웹앱 이름을 유지하거나 다른 고유한 이름으로 변경할 수 있습니다(유효한 문자: `a-z`, `0-9` 및 `-`). 웹앱 이름은 앱의 기본 URL(`<app_name>.azurewebsites.net`, 여기서 `<app_name>`는 웹앱 이름)의 일부로 사용됩니다. 웹앱 이름은 Azure의 모든 앱에서 고유해야 합니다.

![앱 서비스 만들기 대화 상자](media/app-service-web-tutorial-dotnet-sqldatabase/wan.png)

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [resource-group](../../includes/resource-group.md)]

1. **리소스 그룹** 옆에 있는 **새로 만들기**를 클릭합니다.

   ![[리소스 그룹] 옆에 있는 [새로 만들기]를 클릭합니다.](media/app-service-web-tutorial-dotnet-sqldatabase/new_rg2.png)

2. 리소스 그룹 이름을 **myResourceGroup**으로 지정합니다.

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

[!INCLUDE [app-service-plan](../../includes/app-service-plan.md)]

1. **App Service 계획** 옆에 있는 **새로 만들기**를 클릭합니다.

2. **App Service 계획 구성** 대화 상자에서 다음 설정을 사용하여 새 App Service 계획을 구성합니다.

   ![App Service 플랜 만들기](./media/app-service-web-tutorial-dotnet-sqldatabase/configure-app-service-plan.png)

   | 설정  | 제안 값 | 참조 항목 |
   | ----------------- | ------------ | ----|
   |**App Service 계획**| myAppServicePlan | [App Service 계획](../app-service/overview-hosting-plans.md) |
   |**위치**| 서유럽 | [Azure 지역](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) |
   |**크기**| 무료 | [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)|

### <a name="create-a-server"></a>서버 만들기

먼저 [논리 SQL 서버](../azure-sql/database/logical-servers.md)가 있어야 데이터베이스를 만들 수 있습니다. 논리 SQL 서버는 그룹으로 관리되는 데이터베이스 그룹을 포함하는 논리적 구조입니다.

1. **SQL Database 만들기**를 클릭합니다.

   ![SQL Database 만들기](media/app-service-web-tutorial-dotnet-sqldatabase/web-app-name.png)

2. **SQL Database 구성** 대화 상자에서 **SQL Server** 옆에 있는 **새로 만들기**를 클릭합니다.

   고유한 서버 이름이 생성됩니다. 이 이름은 서버에 대한 기본 URL(`<server_name>.database.windows.net`)의 일부로 사용되며, Azure SQL의 모든 서버에서 고유해야 합니다. 서버 이름은 변경할 수 있지만 이 자습서에서는 생성된 값을 유지합니다.

3. 관리자 사용자 이름 및 암호를 추가합니다. 암호 복잡성 요구 사항은 [암호 정책](/sql/relational-databases/security/password-policy)을 참조하세요.

   이 사용자 이름과 암호를 기억해 두세요. 이후 서버를 관리할 때 필요합니다.

   > [!IMPORTANT]
   > 연결 문자열의 암호가 마스킹되었지만(Visual Studio 및 App Service에서도) 어딘가에서 유지 관리되기 때문에 앱의 공격 노출 영역이 늘어납니다. App Service는 [관리되는 서비스 ID](overview-managed-identity.md)를 사용하기 때문에 코드 또는 앱 구성의 비밀을 유지 관리할 필요가 전혀 없어 이러한 위험이 사라집니다. 자세한 내용은 [다음 단계](#next-steps)를 참조하세요.

   ![서버 만들기](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database-server.png)

4. **확인**을 클릭합니다. **SQL Database 구성** 대화 상자를 아직 닫지 마세요.

### <a name="create-a-database-in-azure-sql-database"></a>Azure SQL Database에서 데이터베이스 만들기

1. **SQL Database 구성** 대화 상자에서 다음을 수행합니다.

   * 생성된 기본 **데이터베이스 이름**을 유지합니다.
   * **연결 문자열 이름**에 *MyDbConnection*을 입력합니다. 이 이름은 *Models\MyDatabaseContext.cs*에서 참조되는 연결 문자열과 일치해야 합니다.
   * **확인**을 선택합니다.

    ![데이터베이스 구성](media/app-service-web-tutorial-dotnet-sqldatabase/configure-sql-database.png)

2. **App Service 만들기** 대화 상자에서 구성한 리소스를 표시합니다. **만들기**를 클릭합니다.

   ![만든 리소스](media/app-service-web-tutorial-dotnet-sqldatabase/app_svc_plan_done.png)

마법사에서 Azure 리소스 만들기를 완료하면 ASP.NET 응용 프로그램을 Azure에 게시합니다. 기본 브라우저가 배포된 앱에 대한 URL로 시작됩니다.

몇 가지 할 일 항목을 추가합니다.

![Azure 앱의 게시된 ASP.NET 애플리케이션](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-app-in-browser.png)

축하합니다! 데이터 기반 ASP.NET 애플리케이션이 Azure App Service에서 라이브로 실행되고 있습니다.

## <a name="access-the-database-locally"></a>로컬에서 데이터베이스 접속

Visual Studio를 사용하면 **SQL Server 개체 탐색기**에서 새 데이터베이스를 쉽게 탐색하고 관리할 수 있습니다.

### <a name="create-a-database-connection"></a>데이터베이스 연결 만들기

**보기** 메뉴에서 **SQL Server 개체 탐색기**를 선택합니다.

**SQL Server 개체 탐색기** 맨 위에 있는 **SQL Server 추가** 단추를 클릭합니다.

### <a name="configure-the-database-connection"></a>데이터베이스 연결 구성

**연결** 대화 상자에서 **Azure** 노드를 확장합니다. 여기서는 Azure의 모든 SQL Database 인스턴스가 나열됩니다.

이전에 만든 데이터베이스를 선택합니다. 앞에서 만든 연결이 맨 아래쪽에 자동으로 채워집니다.

이전에 사용한 데이터베이스 관리자 암호를 입력하고 **연결**을 클릭합니다.

![Visual Studio에서 데이터베이스 연결 구성](./media/app-service-web-tutorial-dotnet-sqldatabase/connect-to-sql-database.png)

### <a name="allow-client-connection-from-your-computer"></a>컴퓨터에서 클라이언트 연결 허용

**새 방화벽 규칙 만들기** 대화 상자가 열립니다. 기본적으로 서버는 Azure 앱과 같은 Azure 서비스에서 해당 데이터베이스로의 연결만 허용합니다. Azure 외부에서 데이터베이스에 연결하려면 서버 수준에서 방화벽 규칙을 만듭니다. 방화벽 규칙에는 로컬 컴퓨터의 공용 IP 주소가 허용됩니다.

대화 상자가 컴퓨터의 공용 IP 주소로 이미 채워져 있습니다.

**내 클라이언트 IP 추가**가 선택되어 있는지 확인하고 **확인**을 클릭합니다.

![방화벽 규칙 만들기](./media/app-service-web-tutorial-dotnet-sqldatabase/sql-set-firewall.png)

Visual Studio에서 SQL Database 인스턴스에 대한 방화벽 설정을 만든 후에 **SQL Server 개체 탐색기**에 연결이 표시됩니다.

여기에서 쿼리 실행, 뷰 및 저장 프로시저 만들기 등의 가장 일반적이 데이터베이스 작업을 수행할 수 있습니다.

연결 > **데이터베이스** >  **&lt;데이터베이스 >**  > **테이블**로 확장합니다. `Todoes` 테이블을 마우스 오른쪽 단추로 클릭하고 **데이터 보기**를 선택합니다.

![SQL Database 개체 탐색](./media/app-service-web-tutorial-dotnet-sqldatabase/explore-sql-database.png)

## <a name="update-app-with-code-first-migrations"></a>Code First 마이그레이션으로 앱 업데이트

Visual Studio의 친숙한 도구를 사용하여 Azure에서 데이터베이스 및 앱을 업데이트할 수 있습니다. 이 단계에서는 Entity Framework에서 Code First 마이그레이션을 사용하여 데이터베이스 스키마를 변경하고 Azure에 게시합니다.

Entity Framework Code First 마이그레이션 사용에 대한 자세한 내용은 [MVC 5를 사용하여 Entity Framework 6 Code First 시작](/aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)을 참조하세요.

### <a name="update-your-data-model"></a>데이터 모델 업데이트

코드 편집기에서 _Models\Todo.cs_를 엽니다. 다음 속성을 `ToDo` 클래스에 추가합니다.

```csharp
public bool Done { get; set; }
```

### <a name="run-code-first-migrations-locally"></a>Code First 마이그레이션을 로컬에서 실행

몇 가지 명령을 실행하여 로컬 데이터베이스를 업데이트합니다.

**도구** 메뉴에서 **NuGet 패키지 관리** > **패키지 관리자 콘솔**을 클릭합니다.

[패키지 관리자 콘솔] 창에서 Code First 마이그레이션을 사용하도록 설정합니다.

```powershell
Enable-Migrations
```

마이그레이션을 추가합니다.

```powershell
Add-Migration AddProperty
```

로컬 데이터베이스를 업데이트합니다.

```powershell
Update-Database
```

`Ctrl+F5`를 입력하여 앱을 실행합니다. 편집, 세부 정보 및 만들기 링크를 테스트합니다.

애플리케이션이 오류 없이 로드되면 Code First 마이그레이션이 성공한 것입니다. 그러나 애플리케이션 논리에서 이 새로운 속성을 아직 사용하지 않기 때문에 페이지가 여전히 동일하게 표시될 수 있습니다.

### <a name="use-the-new-property"></a>새 속성 사용

`Done` 속성을 사용하도록 코드를 약간 변경합니다. 이 자습서에서는 간단하게 `Index` 및 `Create` 보기만 변경하여 속성의 실제 작동을 확인합니다.

_Controllers\TodosController.cs_를 엽니다.

줄 52에서 `Create()` 메서드를 찾고 `Done`을 `Bind` 특성의 속성 목록에 추가합니다. 완료되면 `Create()` 메서드 시그니처가 다음 코드와 같이 표시됩니다.

```csharp
public ActionResult Create([Bind(Include = "Description,CreatedDate,Done")] Todo todo)
```

_Views\Todos\Create.cshtml_을 엽니다.

Razor 코드에서 `model.Description`을 사용하는 `<div class="form-group">` 요소가 표시된 다음 `model.CreatedDate`를 사용하는 또 다른 `<div class="form-group">` 요소가 표시되어 있습니다. 이러한 두 요소 바로 뒤에 `model.Done`을 사용하는 또 다른 `<div class="form-group">` 요소를 추가합니다.

```csharp
<div class="form-group">
    @Html.LabelFor(model => model.Done, htmlAttributes: new { @class = "control-label col-md-2" })
    <div class="col-md-10">
        <div class="checkbox">
            @Html.EditorFor(model => model.Done)
            @Html.ValidationMessageFor(model => model.Done, "", new { @class = "text-danger" })
        </div>
    </div>
</div>
```

_Views\Todos\Index.cshtml_을 엽니다.

빈 `<th></th>` 요소를 검색합니다. 이 요소 바로 위에 다음 Razor 코드를 추가합니다.

```csharp
<th>
    @Html.DisplayNameFor(model => model.Done)
</th>
```

`Html.ActionLink()` 도우미 메서드가 포함된 `<td>` 요소를 찾습니다. 이 `<td>`_위에_ 다음 Razor 코드가 포함된 다른 `<td>` 요소를 추가합니다.

```csharp
<td>
    @Html.DisplayFor(modelItem => item.Done)
</td>
```

`Index` 및 `Create` 보기에서 변경 내용을 확인하기만 하면 됩니다.

`Ctrl+F5`를 입력하여 앱을 실행합니다.

이제 할 일 항목을 추가하고 **완료**를 확인할 수 있습니다. 그러면 홈페이지에 완료된 항목으로 표시됩니다. `Edit` 보기를 변경하지 않았으므로 `Edit` 보기에서 `Done` 필드가 표시되지 않습니다.

### <a name="enable-code-first-migrations-in-azure"></a>Azure에서 Code First 마이그레이션 사용

데이터베이스 마이그레이션을 비롯하여 코드 변경이 수행되었으므로 Azure 앱에 게시하고 Code First 마이그레이션을 사용하여 SQL Database도 업데이트합니다.

이전과 마찬가지로 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

게시 설정을 열려면 **구성**을 클릭합니다.

![게시 설정 열기](./media/app-service-web-tutorial-dotnet-sqldatabase/publish-settings.png)

마법사에서 **다음**을 클릭합니다.

SQL Database에 대한 연결 문자열이 **MyDatabaseContext (MyDbConnection)** 로 채워졌는지 확인합니다. 드롭다운에서 **myToDoAppDb** 데이터베이스를 선택해야 할 수 있습니다.

**Execute Code First Migrations (runs on application start)** (Code First 마이그레이션 실행(애플리케이션 시작 시 실행))를 선택한 다음, **저장**을 클릭합니다.

![Azure 앱에서 Code First 마이그레이션 사용](./media/app-service-web-tutorial-dotnet-sqldatabase/enable-migrations.png)

### <a name="publish-your-changes"></a>변경 내용 게시

이제 Azure 앱에서 Code First 마이그레이션을 사용하도록 설정했으므로 코드 변경 내용을 게시합니다.

게시 페이지에서 **게시**를 클릭합니다.

할 일 항목 추가를 다시 시도하고 **Done**(완료)을 선택합니다. 그러면 홈페이지에 완료된 항목으로 표시되어야 합니다.

![Code First 마이그레이션 후 Azure 앱](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

기존의 모든 할 일 항목이 계속 표시됩니다. ASP.NET 애플리케이션을 다시 게시해도 SQL Database의 기존 데이터가 손실되지 않습니다. 또한 Code First 마이그레이션은 데이터 스키마만 변경하고 기존 데이터는 그대로 유지합니다.

## <a name="stream-application-logs"></a>애플리케이션 로그 스트림

Azure 앱에서 직접 Visual Studio로 추적 메시지를 스트림할 수 있습니다.

_Controllers\TodosController.cs_를 엽니다.

각 동작은 `Trace.WriteLine()` 메서드로 시작됩니다. 이 코드는 Azure 앱에 추적 메시지를 추가하는 방법을 보여주기 위해 추가되었습니다.

### <a name="open-server-explorer"></a>서버 탐색기 열기

**보기** 메뉴에서 **서버 탐색기**를 선택합니다. **서버 탐색기**에서 Azure 앱에 대한 로깅을 구성할 수 있습니다.

### <a name="enable-log-streaming"></a>로그 스트리밍 사용

**서버 탐색기**에서 **Azure** > **App Service**를 확장합니다.

Azure 앱을 처음 만들 때 만든 **myResourceGroup** 리소스 그룹을 확장합니다.

Azure 앱을 마우스 오른쪽 단추로 클릭하고 **스트리밍 로그 보기**를 선택합니다.

![로그 스트리밍 사용](./media/app-service-web-tutorial-dotnet-sqldatabase/stream-logs.png)

이제 로그가 **출력** 창으로 스트리밍됩니다.

![출력 창에서 로그 스트리밍](./media/app-service-web-tutorial-dotnet-sqldatabase/log-streaming-pane.png)

그러나 추적 메시지가 아직 표시되지 않습니다. **스트리밍 로그 보기**를 먼저 선택하면 Azure 앱에서 추적 수준을 `Error`로 설정하여 `Trace.TraceError()` 메서드로 생성된 오류 이벤트만 로깅하기 때문입니다.

### <a name="change-trace-levels"></a>추적 수준 변경

다른 추적 메시지를 출력하도록 추적 수준을 변경하려면 **서버 탐색기**로 돌아갑니다.

Azure 앱을 마우스 오른쪽 단추로 다시 클릭하고 **설정 보기**를 선택합니다.

**애플리케이션 로깅(파일 시스템)** 드롭다운에서 **자세한 정보 표시**를 선택합니다. **저장**을 클릭합니다.

![추적 수준을 자세한 정보 표시로 설정](./media/app-service-web-tutorial-dotnet-sqldatabase/trace-level-verbose.png)

> [!TIP]
> 다양한 추적 수준을 사용하여 각 수준에서 표시되는 메시지 종류를 확인할 수 있습니다. 예를 들어 **정보** 수준에는 `Trace.TraceInformation()`, `Trace.TraceWarning()` 및 `Trace.TraceError()`로 생성된 모든 로그가 포함되지만 `Trace.WriteLine()`으로 생성된 로그는 포함되지 않습니다.

브라우저에서 *http://&lt;your app name&gt;.azurewebsites.net*에 있는 앱으로 다시 이동한 다음, Azure에서 할 일 목록 애플리케이션 주위를 클릭해 보세요. 추적 메시지가 Visual Studio의 **출력** 창으로 스트리밍됩니다.

```console
Application: 2017-04-06T23:30:41  PID[8132] Verbose     GET /Todos/Index
Application: 2017-04-06T23:30:43  PID[8132] Verbose     GET /Todos/Create
Application: 2017-04-06T23:30:53  PID[8132] Verbose     POST /Todos/Create
Application: 2017-04-06T23:30:54  PID[8132] Verbose     GET /Todos/Index
```

### <a name="stop-log-streaming"></a>로그 스트리밍 중지

로그 스트리밍 서비스를 중지하려면 **출력** 창에서 **모니터링 중지** 단추를 클릭합니다.

![로그 스트리밍 중지](./media/app-service-web-tutorial-dotnet-sqldatabase/stop-streaming.png)

## <a name="manage-your-azure-app"></a>Azure 앱 관리

웹앱을 관리하려면 [Azure Portal](https://portal.azure.com)로 이동합니다. **App Services**를 검색하여 선택합니다.

![Azure App Services 검색](./media/app-service-web-tutorial-dotnet-sqldatabase/azure-portal-navigate-app-services.png)

Azure 앱의 이름을 선택합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-dotnet-sqldatabase/access-portal.png)

앱 페이지에 연결되었습니다.

기본적으로 포털에서 **개요** 페이지가 표시됩니다. 이 페이지에서는 앱이 어떻게 작동하고 있는지를 보여 줍니다. 여기에서 찾아보기, 중지, 시작, 다시 시작, 삭제와 같은 기본 관리 작업을 수행할 수 있습니다. 페이지의 왼쪽에 있는 탭에서는 열 수 있는 여러 구성 페이지를 보여 줍니다.

![Azure Portal의 App Service 페이지](./media/app-service-web-tutorial-dotnet-sqldatabase/web-app-blade.png)

[!INCLUDE [Clean up section](../../includes/clean-up-section-portal-web-app.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * Azure SQL Database에서 데이터베이스 만들기
> * SQL Database에 ASP.NET 앱 연결
> * Azure에 앱 배포
> * 데이터 모델 업데이트 및 앱 다시 배포
> * Azure에서 터미널로 로그 스트림
> * Azure Portal에서 앱 관리

연결 Azure SQL Database의 보안을 쉽게 개선하는 방법을 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [Azure 리소스에 대한 관리 ID를 사용하여 SQL Database에 안전하게 액세스](app-service-web-tutorial-connect-msi.md)

추가 리소스:

> [!div class="nextstepaction"]
> [ASP.NET 앱 구성](configure-language-dotnet-framework.md)

클라우드 비용을 최적화하여 비용을 절감하고 싶습니까?

> [!div class="nextstepaction"]
> [Cost Management를 통한 비용 분석 시작](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)