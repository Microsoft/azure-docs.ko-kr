---
title: '자습서: 관리 ID를 사용하여 데이터 액세스'
description: 관리 ID를 사용하여 데이터베이스 연결을 보다 안전하게 만드는 방법과 이를 다른 Azure 서비스에 적용하는 방법에 대해 알아봅니다.
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/27/2020
ms.custom: devx-track-csharp, mvc, cli-validate
ms.openlocfilehash: 882188c638c77fa0055f1c2d2e664d97a4fe9359
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962267"
---
# <a name="tutorial-secure-azure-sql-database-connection-from-app-service-using-a-managed-identity"></a>자습서: 관리 ID를 사용하여 App Service에서 Azure SQL Database 연결 보호

[App Service](overview.md)는 Azure에서 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 또한 [Azure SQL Database](/azure/sql-database/) 및 기타 Azure 서비스에 대한 액세스를 보호하기 위한 턴키 솔루션인 [관리 ID](overview-managed-identity.md)를 앱에 제공합니다. App Service의 관리 ID는 연결 문자열의 자격 증명과 같은 비밀을 앱에서 제거하여 앱의 보안을 보다 강화합니다. 이 자습서에서는 다음 자습서 중 하나에서 빌드한 샘플 웹앱에 관리 ID를 추가합니다. 

- [자습서: SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md)
- [자습서: Azure App Service에서 ASP.NET Core 및 SQL Database 앱 빌드](tutorial-dotnetcore-sqldb-app.md)

완료되면 샘플 앱은 사용자 이름과 암호 없이도 안전하게 SQL Database에 연결됩니다.

> [!NOTE]
> 이 자습서에서 다루는 단계는 다음 버전을 지원합니다.
> 
> - .NET Framework 4.7.2 이상
> - .NET Core 2.2 이상
>

다음을 알아봅니다.

> [!div class="checklist"]
> * 관리 ID 사용
> * 관리 ID에 SQL Database 액세스 권한 부여
> * SQL Database에서 Azure AD 인증을 사용하도록 Entity Framework 구성
> * Visual Studio에서 Azure AD 인증을 사용하여 SQL Database에 연결

> [!NOTE]
>Azure AD 인증은 온-프레미스 Active Directory(AD DS)의 [통합 Windows 인증](/previous-versions/windows/it-pro/windows-server-2003/cc758557(v=ws.10))과 _다릅니다_. AD DS 및 Azure AD는 완전히 다른 인증 프로토콜을 사용합니다. 자세한 내용은 [Azure AD Domain Services 설명서](../active-directory-domain-services/index.yml)를 참조하세요.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 문서는 [자습서: SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md) 또는 [자습서: Azure App Service에서 ASP.NET Core 및 SQL Database 앱 빌드](tutorial-dotnetcore-sqldb-app.md)에서 중단한 곳에서 이어집니다. 아직 수행하지 않은 경우 두 자습서 중 하나를 먼저 수행합니다. 또는 SQL Database를 사용하여 해당 .NET 앱에 맞게 단계를 조정할 수 있습니다.

SQL Database를 백 엔드로 사용하여 앱을 디버깅하려면 컴퓨터에서 클라이언트 연결을 허용했는지 확인합니다. 그렇지 않은 경우 [Azure Portal를 사용하여 서버 수준 IP 방화벽 규칙 관리](../azure-sql/database/firewall-configure.md#use-the-azure-portal-to-manage-server-level-ip-firewall-rules)의 단계에 따라 클라이언트 IP를 추가합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="grant-database-access-to-azure-ad-user"></a>Azure AD 사용자에 데이터베이스 액세스 권한 부여

먼저 Azure AD 사용자를 서버의 Active Directory 관리자로 할당하여 SQL Database에 대한 Azure AD 인증을 사용하도록 설정합니다. 이 사용자는 Azure 구독에 가입하는 데 사용한 Microsoft 계정과 다릅니다. 자신이 만들거나, Azure AD로 가져오거나, 동기화하거나, 초대한 사용자여야 합니다. 허용된 Azure AD 사용자에 대한 자세한 내용은 [Azure AD 기능 및 SQL Database의 제한 사항](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations)을 참조하세요.

Azure AD 테넌트에 아직 사용자가 없는 경우 [Azure Active Directory를 사용하여 사용자 추가 또는 삭제](../active-directory/fundamentals/add-users-azure-active-directory.md)의 단계에 따라 하나를 만듭니다.

[`az ad user list`](/cli/azure/ad/user?view=azure-cli-latest#az-ad-user-list)를 사용하여 Azure AD 사용자의 개체 ID를 찾고 *\<user-principal-name>* 를 바꿉니다. 결과는 변수에 저장됩니다.

```azurecli-interactive
azureaduser=$(az ad user list --filter "userPrincipalName eq '<user-principal-name>'" --query [].objectId --output tsv)
```
> [!TIP]
> Azure AD의 모든 사용자 계정 이름 목록을 보려면 `az ad user list --query [].userPrincipalName`을 실행합니다.
>

Cloud Shell에서 [`az sql server ad-admin create`](/cli/azure/sql/server/ad-admin?view=azure-cli-latest#az-sql-server-ad-admin-create) 명령을 사용하여 이 Azure AD 사용자를 Active Directory 관리자로 추가합니다. 다음 명령에서 *\<server-name>* 을 서버 이름(`.database.windows.net` 접미사 없이)으로 바꿉니다.

```azurecli-interactive
az sql server ad-admin create --resource-group myResourceGroup --server-name <server-name> --display-name ADMIN --object-id $azureaduser
```

Active Directory 관리자를 추가하는 방법에 대한 자세한 내용은 [서버의 Azure Active Directory 관리자 프로비전](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance)을 참조하세요.

## <a name="set-up-visual-studio"></a>Visual Studio 설정

### <a name="windows-client"></a>Windows 클라이언트
Windows용 Visual Studio는 Azure AD 인증과 통합됩니다. Visual Studio에서 개발 및 디버깅을 사용하도록 설정하려면 Visual Studio의 메뉴에서 **파일** > **계정 설정**을 선택하여 Azure AD 사용자를 추가하고 **계정 추가**를 클릭합니다.

Azure 서비스 인증의 Azure AD 사용자를 설정하려면 메뉴에서 **도구** > **옵션**을 선택한 후 **Azure 서비스 인증** > **계정 선택**을 선택합니다. 추가한 Azure AD 사용자를 선택하고 **확인**을 클릭합니다.

이제 백 엔드 SQL Database에서 Azure AD 인증을 사용하여 앱을 개발하고 디버그할 준비가 되었습니다.

### <a name="macos-client"></a>macOS 클라이언트

Mac용 Visual Studio는 Azure AD 인증과 통합되지 않습니다. 그러나 나중에 사용할 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 라이브러리는 Azure CLI의 토큰을 사용할 수 있습니다. Visual Studio에서 개발 및 디버깅을 사용하려면 먼저 로컬 머신에 [Azure CLI를 설치](/cli/azure/install-azure-cli)해야 합니다.

로컬 머신에 Azure CLI를 설치했으면 다음 명령을 사용하여 Azure AD 사용자로 Azure CLI에 로그인합니다.

```bash
az login --allow-no-subscriptions
```
이제 백 엔드 SQL Database에서 Azure AD 인증을 사용하여 앱을 개발하고 디버그할 준비가 되었습니다.

## <a name="modify-your-project"></a>프로젝트 수정

프로젝트에 대해 수행하는 단계는 ASP.NET 프로젝트인지 아니면 ASP.NET Core 프로젝트인지에 따라 달라집니다.

- [ASP.NET 수정](#modify-aspnet)
- [ASP.NET Core 수정](#modify-aspnet-core)

### <a name="modify-aspnet"></a>ASP.NET 수정

Visual Studio에서 패키지 관리자 콘솔을 열고, [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) NuGet 패키지를 추가합니다.

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

*Web.config*에서 파일 위쪽부터 다음과 같은 변경 작업을 수행합니다.

- `<configSections>`에서 다음과 같은 섹션 선언을 추가합니다.

    ```xml
    <section name="SqlAuthenticationProviders" type="System.Data.SqlClient.SqlAuthenticationProviderConfigurationSection, System.Data, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" />
    ```

- 닫는 `</configSections>` 태그 아래에서 `<SqlAuthenticationProviders>`에 다음 XML 코드를 추가합니다.

    ```xml
    <SqlAuthenticationProviders>
      <providers>
        <add name="Active Directory Interactive" type="Microsoft.Azure.Services.AppAuthentication.SqlAppAuthenticationProvider, Microsoft.Azure.Services.AppAuthentication" />
      </providers>
    </SqlAuthenticationProviders>
    ```    

- `MyDbConnection`이라는 연결 문자열을 찾고 `connectionString` 값을 `"server=tcp:<server-name>.database.windows.net;database=<db-name>;UID=AnyString;Authentication=Active Directory Interactive"`로 바꿉니다. _\<server-name>_ 및 _\<db-name>_ 을 서버 이름 및 데이터베이스 이름으로 바꿉니다.

> [!NOTE]
> 방금 등록한 SqlAuthenticationProvider는 이전에 설치한 AppAuthentication 라이브러리를 기반으로 합니다. 기본적으로 시스템 할당 ID를 사용합니다. 사용자 할당 ID를 활용하려면 추가 구성을 제공해야 합니다. AppAuthentication 라이브러리는 [연결 문자열 지원](../key-vault/general/service-to-service-authentication.md#connection-string-support)을 참조하세요.

SQL Database에 연결하는 데 필요한 모든 항목입니다. Visual studio에서 디버깅하는 경우 코드는 [Visual Studio 설정](#set-up-visual-studio)에서 구성한 Azure AD 사용자를 사용합니다. App Service 앱의 관리 ID에서 연결할 수 있도록 SQL Database를 나중에 설정합니다.

`Ctrl+F5`를 입력하여 앱을 다시 실행합니다. 이제 브라우저에서 동일한 CRUD 앱이 Azure AD 인증을 사용하여 Azure SQL Database에 직접 연결합니다. 이 설정을 사용하면 Visual Studio에서 데이터베이스 마이그레이션을 실행할 수 있습니다.

### <a name="modify-aspnet-core"></a>ASP.NET Core 수정

Visual Studio에서 패키지 관리자 콘솔을 열고, [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) NuGet 패키지를 추가합니다.

```powershell
Install-Package Microsoft.Azure.Services.AppAuthentication -Version 1.4.0
```

[ASP.NET Core 및 SQL Database 자습서](tutorial-dotnetcore-sqldb-app.md)에서는 로컬 개발 환경에서 Sqlite 데이터베이스 파일을 사용하고 Azure 프로덕션 환경에서 App Service의 연결 문자열을 사용하기 때문에 `MyDbConnection` 연결 문자열은 전혀 사용되지 않습니다. Active Directory 인증을 사용하여 두 환경에서 동일한 연결 문자열을 사용하려고 합니다. *appsettings.json*에서 연결 문자열 `MyDbConnection`의 값을 다음으로 바꿉니다.

```json
"Server=tcp:<server-name>.database.windows.net,1433;Database=<database-name>;"
```

다음으로, SQL Database에 대한 액세스 토큰을 사용하여 Entity Framework 데이터베이스 컨텍스트를 제공합니다. *Data\MyDatabaseContext.cs*에서 다음 코드를 빈 `MyDatabaseContext (DbContextOptions<MyDatabaseContext> options)` 생성자의 중괄호 내에 추가합니다.

```csharp
var conn = (Microsoft.Data.SqlClient.SqlConnection)Database.GetDbConnection();
conn.AccessToken = (new Microsoft.Azure.Services.AppAuthentication.AzureServiceTokenProvider()).GetAccessTokenAsync("https://database.windows.net/").Result;
```

> [!NOTE]
> 이 데모 코드는 명확성과 단순성을 위해 동기식입니다.

SQL Database에 연결하는 데 필요한 모든 항목입니다. Visual studio에서 디버깅하는 경우 코드는 [Visual Studio 설정](#set-up-visual-studio)에서 구성한 Azure AD 사용자를 사용합니다. App Service 앱의 관리 ID에서 연결할 수 있도록 SQL Database를 나중에 설정합니다. `AzureServiceTokenProvider` 클래스는 메모리에서 토큰을 캐시하여 만료 직전에 Azure AD에서 검색합니다. 토큰을 새로 고치는 데 사용자 지정 코드는 필요하지 않습니다.

> [!TIP]
> 구성한 Azure AD 사용자가 여러 테넌트에 액세스할 수 있는 경우 원하는 테넌트 ID로 `GetAccessTokenAsync("https://database.windows.net/", tenantid)`를 호출하여 적절한 액세스 토큰을 검색합니다.

`Ctrl+F5`를 입력하여 앱을 다시 실행합니다. 이제 브라우저에서 동일한 CRUD 앱이 Azure AD 인증을 사용하여 Azure SQL Database에 직접 연결합니다. 이 설정을 사용하면 Visual Studio에서 데이터베이스 마이그레이션을 실행할 수 있습니다.

## <a name="use-managed-identity-connectivity"></a>관리 ID 연결 사용

다음으로, 시스템에서 할당한 관리 ID를 사용하여 SQL Database에 연결하도록 App Service 앱을 구성합니다.

> [!NOTE]
> 이 섹션의 지침은 시스템 할당 ID에 대한 것이지만 사용자 할당 ID를 쉽게 사용할 수 있습니다. 이 작업을 수행하려면 원하는 사용자 할당 ID를 할당하려면 `az webapp identity assign command`를 변경해야 합니다. 그런 다음, SQL 사용자를 만들 때 사이트 이름이 아닌 사용자 할당 ID 리소스의 이름을 사용해야 합니다.

### <a name="enable-managed-identity-on-app"></a>앱에서 관리 ID 사용

Azure 앱의 관리 ID를 사용하려면 Cloud Shell에서 [az webapp identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) 명령을 사용합니다. 다음 명령에서 *\<app-name>* 을 바꿉니다.

```azurecli-interactive
az webapp identity assign --resource-group myResourceGroup --name <app-name>
```

다음은 출력의 예입니다.

<pre>
{
  "additionalProperties": {},
  "principalId": "21dfa71c-9e6f-4d17-9e90-1d28801c9735",
  "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
  "type": "SystemAssigned"
}
</pre>

### <a name="grant-permissions-to-managed-identity"></a>관리 ID에 사용 권한 부여

> [!NOTE]
> 원하는 경우 [Azure AD 그룹](../active-directory/fundamentals/active-directory-manage-groups.md)에 ID를 추가한 다음, ID 대신 Azure AD 그룹에 SQL Database 액세스 권한을 부여할 수 있습니다. 예를 들어 다음 명령은 이전 단계의 관리 ID를 _myAzureSQLDBAccessGroup_이라는 새 그룹에 추가합니다.
> 
> ```azurecli-interactive
> groupid=$(az ad group create --display-name myAzureSQLDBAccessGroup --mail-nickname myAzureSQLDBAccessGroup --query objectId --output tsv)
> msiobjectid=$(az webapp identity show --resource-group myResourceGroup --name <app-name> --query principalId --output tsv)
> az ad group member add --group $groupid --member-id $msiobjectid
> az ad group member list -g $groupid
> ```
>

Cloud Shell에서 SQLCMD 명령을 사용하여 SQL Database에 로그인합니다. _\<server-name>_ 를 서버 이름으로, _\<db-name>_ 을 앱에서 사용하는 데이터베이스 이름으로, _\<aad-user-name>_ 및 _\<aad-password>_ 를 Azure AD 사용자의 자격 증명으로 바꿉니다.

```azurecli-interactive
sqlcmd -S <server-name>.database.windows.net -d <db-name> -U <aad-user-name> -P "<aad-password>" -G -l 30
```

원하는 데이터베이스에 대한 SQL 프롬프트에서 다음 명령을 실행하여 앱에 필요한 권한을 부여합니다. 예를 들면 다음과 같습니다. 

```sql
CREATE USER [<identity-name>] FROM EXTERNAL PROVIDER;
ALTER ROLE db_datareader ADD MEMBER [<identity-name>];
ALTER ROLE db_datawriter ADD MEMBER [<identity-name>];
ALTER ROLE db_ddladmin ADD MEMBER [<identity-name>];
GO
```

*\<identity-name>* 은 Azure AD의 관리 ID 이름입니다. ID가 시스템 할당된 경우 이름은 App Service 앱의 이름과 항상 동일합니다. Azure AD 그룹에 대한 사용 권한을 부여하려면 대신 그룹의 표시 이름을 사용합니다(예: *myAzureSQLDBAccessGroup*).

`EXIT`를 입력하여 Cloud Shell 프롬프트로 돌아갑니다.

> [!NOTE]
> 또한 관리 ID의 백 엔드 서비스는 만료된 경우에만 대상 리소스에 대한 토큰을 업데이트하는 [토큰 캐시를 유지 관리](overview-managed-identity.md#obtain-tokens-for-azure-resources)합니다. SQL Database 사용 권한을 구성하는 실수를 하고 앱을 사용하여 토큰을 가져오려고 시도한 *후*에 사용 권한을 수정하려고 하는 경우 캐시된 토큰이 만료될 때까지 실제로 업데이트된 권한으로 새 토큰을 얻을 수 없습니다.

### <a name="modify-connection-string"></a>연결 문자열 수정

*Web.config* 또는 *appsettings.json*에서 변경한 내용은 관리 ID와 함께 작동하므로 Visual Studio가 처음으로 앱을 배포할 때 만든 기존 연결 문자열을 App Service에서 제거하기만 하면 됩니다. 다음 명령을 사용하되 *\<app-name>* 을 앱의 이름으로 바꾸어 사용합니다.

```azurecli-interactive
az webapp config connection-string delete --resource-group myResourceGroup --name <app-name> --setting-names MyDbConnection
```

## <a name="publish-your-changes"></a>변경 내용 게시

이제 Azure에 변경 내용을 게시하면 됩니다.

**[자습서: SQL Database를 사용하여 Azure에서 ASP.NET 앱 빌드](app-service-web-tutorial-dotnet-sqldatabase.md)** 를 진행 중이었다면 Visual Studio에서 변경 내용을 게시합니다. **솔루션 탐색기**에서 **DotNetAppSqlDb** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

![솔루션 탐색기에서 게시](./media/app-service-web-tutorial-dotnet-sqldatabase/solution-explorer-publish.png)

게시 페이지에서 **게시**를 클릭합니다. 

**[자습서: Azure App Service에서 ASP.NET Core 및 SQL Database 앱 빌드](tutorial-dotnetcore-sqldb-app.md)** 를 진행 중이었다면 다음 명령으로 Git을 사용하여 변경 내용을 게시합니다.

```bash
git commit -am "configure managed identity"
git push azure master
```

새 웹 페이지에 할 일 목록이 표시되면 앱이 관리 ID를 사용하여 데이터베이스에 연결합니다.

![Code First 마이그레이션 후 Azure 앱](./media/app-service-web-tutorial-dotnet-sqldatabase/this-one-is-done.png)

이제 이전처럼 할 일 목록을 편집할 수 있어야 합니다.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 관리 ID 사용
> * 관리 ID에 SQL Database 액세스 권한 부여
> * SQL Database에서 Azure AD 인증을 사용하도록 Entity Framework 구성
> * Visual Studio에서 Azure AD 인증을 사용하여 SQL Database에 연결

다음 자습서로 이동하여 사용자 지정 DNS 이름을 웹앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)