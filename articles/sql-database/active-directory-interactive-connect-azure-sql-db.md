---
title: ActiveDirectoryInteractive SQL에 연결
description: SqlAuthenticationMethod.ActiveDirectoryInteractive 모드를 사용하여 Azure SQL Database에 연결하는 C# 코드와 설명입니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: active directory
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: MirekS
ms.reviewer: GeneMi, vanto
ms.date: 10/11/2019
ms.openlocfilehash: 5e7d58e5e0fc79e407e77ae9d73314a1d5d22666
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692296"
---
# <a name="connect-to-azure-sql-database-with-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication를 사용 하 여 Azure SQL Database에 연결

이 문서에서는 Azure SQL Database C# 에 연결 하는 프로그램을 제공 합니다. 프로그램은 [Azure Multi-Factor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)를 지 원하는 대화형 모드 인증을 사용 합니다.

SQL tools에 대 한 Multi-Factor Authentication 지원에 대 한 자세한 내용은 [SQL Server Data Tools의 Azure Active Directory 지원 (SSDT) (영문)](https://docs.microsoft.com/sql/ssdt/azure-active-directory)을 참조 하십시오.

## <a name="multi-factor-authentication-for-azure-sql-database"></a>Azure SQL Database에 대 한 Multi-Factor Authentication

.NET Framework 버전 4.7.2부터 열거 [`SqlAuthenticationMethod`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient.sqlauthenticationmethod) 에 새 값 `ActiveDirectoryInteractive`있습니다. 클라이언트 C# 프로그램에서 열거형 값은 Multi-Factor Authentication을 지 원하는 azure AD (Azure Active Directory) 대화형 모드를 사용 하 여 azure SQL database에 연결 하도록 시스템에 지시 합니다. 그러면 프로그램을 실행하는 사용자에게 다음 대화 상자가 표시됩니다.

* Azure AD 사용자 이름을 표시하고 사용자의 암호를 요청하는 대화 상자.

   사용자의 도메인이 Azure AD와 페더레이션된 경우 암호가 필요 하지 않기 때문에이 대화 상자는 표시 되지 않습니다.

   Azure AD 정책이 사용자에 게 Multi-Factor Authentication 하는 경우 다음 두 대화 상자가 표시 됩니다.

* 사용자가 Multi-Factor Authentication를 처음으로 이동할 때 텍스트 메시지를 보낼 휴대폰 번호를 요청 하는 대화 상자가 시스템에 표시 됩니다. 각 메시지는 사용자가 그 다음 대화 상자에서 입력해야 하는 *확인 코드*를 제공합니다.

* 시스템이 휴대폰으로 보낸 Multi-Factor Authentication 확인 코드를 요청 하는 대화 상자입니다.

Multi-Factor Authentication를 요구 하도록 Azure AD를 구성 하는 방법에 대 한 자세한 내용은 [클라우드에서 azure Multi-Factor Authentication 시작](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-cloud)하기를 참조 하세요.

이러한 대화 상자의 스크린샷 [SQL Server Management Studio 및 AZURE AD에 대 한 다단계 인증 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조 하세요.

> [!TIP]
> [.NET Api 브라우저 도구 페이지](https://docs.microsoft.com/dotnet/api/)를 사용 하 여 .NET Framework api를 검색할 수 있습니다.
>
> [선택적? term =&lt;검색 값&gt; 매개 변수](https://docs.microsoft.com/dotnet/api/?term=SqlAuthenticationMethod)를 사용 하 여 직접 검색할 수도 있습니다.

## <a name="configure-your-c-application-in-the-azure-portal"></a>Azure Portal에서 C# 애플리케이션 구성

시작하기 전에 [Azure SQL Database 서버를 만들었고](sql-database-get-started-portal.md) 사용할 수 있어야 합니다.

### <a name="register-your-app-and-set-permissions"></a>앱 등록 및 사용 권한 설정

Azure AD 인증을 사용하려면 C# 프로그램이 Azure AD 애플리케이션으로 등록해야 합니다. 앱을 등록하려면 Azure AD 관리자이거나 사용자가 할당한 Azure AD *애플리케이션 개발자* 역할이 있어야 합니다. 역할 할당에 대 한 자세한 내용은 [Azure Active Directory를 사용 하 여 사용자에 게 관리자 및 비관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-users-assign-role-azure-portal)을 참조 하세요.

앱 등록을 완료 하면 **응용 프로그램 ID**가 생성 되 고 표시 됩니다. 연결하려면 프로그램이 이 ID를 포함해야 합니다.

애플리케이션에 등록하고 필요한 권한을 설정하려면

1. Azure Portal에서 **Azure Active Directory** > 를 선택 하 **앱 등록** **새 등록**을 > 합니다.

    ![앱 등록](media/active-directory-interactive-connect-azure-sql-db/image1.png)

    앱 등록을 만든 후에는 **응용 프로그램 ID** 값이 생성 되 고 표시 됩니다.

    ![표시된 앱 ID](media/active-directory-interactive-connect-azure-sql-db/image2.png)

2. **API 권한** > **사용 권한 추가를**선택 합니다.

    ![등록된 앱의 권한 설정](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-c32.png)

3. **내 조직에서 사용** > 검색 >에 **Azure SQL Database** 입력 하 고 **Azure SQL Database**를 선택 합니다.

    ![Azure SQL Database에 대한 API 액세스 추가](media/active-directory-interactive-connect-azure-sql-db/sshot-registered-app-settings-required-permissions-add-api-access-Azure-sql-db-d11.png)

4. **위임 된 권한** > **User_impersonation** > **권한 추가**를 선택 합니다.

    ![Azure SQL Database에 대한 API에 권한 위임](media/active-directory-interactive-connect-azure-sql-db/sshot-add-api-access-azure-sql-db-delegated-permissions-checkbox-e14.png)

### <a name="set-an-azure-ad-admin-for-your-sql-database-server"></a>SQL Database 서버에 대해 Azure AD 관리자 설정

C# 프로그램을 실행 하려면 azure SQL server 관리자가 SQL Database 서버에 대 한 azure AD 관리자를 할당 해야 합니다. 

**SQL Server** 페이지에서 **Active Directory 관리자** > **설정 관리자**를 선택 합니다.

Azure SQL Database에 대 한 Azure AD 관리자 및 사용자에 대 한 자세한 내용은 [SQL Database를 사용 하 여 Azure Active Directory 인증 구성 및 관리](sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)의 스크린샷을 참조 하세요.

### <a name="add-a-non-admin-user-to-a-specific-database-optional"></a>특정 데이터베이스에 관리자가 아닌 사용자 추가(선택 사항)

SQL Database 서버의 Azure AD 관리자는 C# 예제 프로그램을 실행할 수 있습니다. Azure AD 사용자는 데이터베이스에 있는 경우 해당 프로그램을 실행할 수 있습니다. Azure AD SQL 관리자 또는 데이터베이스에 이미 있으며 데이터베이스에 대해 `ALTER ANY USER` 권한이 있는 Azure AD 사용자는 사용자를 추가할 수 있습니다.

SQL [`Create User`](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql) 명령을 사용하여 데이터베이스에 사용자를 추가할 수 있습니다. 예는 `CREATE USER [<username>] FROM EXTERNAL PROVIDER`입니다.

자세한 내용은 [SQL Database, Managed Instance 및 SQL Data Warehouse에서 인증을 위해 Azure Active Directory 인증 사용](sql-database-aad-authentication.md)을 참조하세요.

## <a name="new-authentication-enum-value"></a>새 인증 열거형 값

C# 예제는 [`System.Data.SqlClient`](https://docs.microsoft.com/dotnet/api/system.data.sqlclient) 네임스페이스에 의존합니다. Multi-Factor Authentication에 대 한 특별 한 관심은 다음 값을 가진 열거형 `SqlAuthenticationMethod`입니다.

- `SqlAuthenticationMethod.ActiveDirectoryInteractive`

   Azure AD 사용자 이름에이 값을 사용 하 여 Multi-Factor Authentication을 구현 합니다. 이 문서에서는 이 값을 집중적으로 다룹니다. 사용자 암호에 대 한 대화 상자를 표시 한 다음이 사용자에 게 Multi-Factor Authentication가 설정 된 경우 Multi-Factor Authentication 유효성 검사에 대 한 대화 상자를 표시 하 여 대화형 환경을 생성 합니다. 이 값은 .NET framework 버전 4.7.2부터 사용할 수 있습니다.

- `SqlAuthenticationMethod.ActiveDirectoryIntegrated`

  *페더레이션된* 계정에 이 값을 사용합니다. 페더레이션된 계정에서 사용자 이름은 Windows 도메인에 알려져 있습니다. 이 인증 방법은 Multi-Factor Authentication을 지원 하지 않습니다.

- `SqlAuthenticationMethod.ActiveDirectoryPassword`

  Azure AD 사용자 이름 및 암호가 필요한 인증에 이 값을 사용합니다. Azure SQL Database가 인증을 수행합니다. 이 메서드는 Multi-Factor Authentication을 지원 하지 않습니다.

## <a name="set-c-parameter-values-from-the-azure-portal"></a>Azure Portal에서 C# 매개 변수 값 설정

C# 프로그램이 성공적으로 실행되려면 정적 필드에 적절한 값을 할당해야 합니다. 다음은 예제 값을 포함하는 필드입니다. 또한 필요한 값을 얻을 수 있는 Azure Portal 위치도 표시 됩니다.

| 정적 필드 이름 | 예제 값 | Azure Portal 내 위치 |
| :---------------- | :------------ | :-------------------- |
| Az_SQLDB_svrName | "my-sqldb-svr.database.windows.net" | **SQL 서버** > **이름으로 필터링** |
| AzureAD_UserID | "auser\@abc.onmicrosoft.com" | **Azure Active Directory** > **사용자** > **새 게스트 사용자** |
| Initial_DatabaseName | "myDatabase" | **SQL 서버** > **SQL 데이터베이스** |
| ClientApplicationID | "a94f9c62-97fe-4d19-b06d-111111111111" | **Azure Active Directory** > **앱 등록** > **이름별로 검색** > **애플리케이션 ID** |
| RedirectUri | new Uri("https://mywebserver.com/") | **Azure Active Directory** **앱 등록** > **이름으로 검색** >  >  *[사용자 앱 등록]* ** > ** **redirecturis** > <br /><br />이 문서에서 유효한 값은 여기에서 사용 되지 않으므로 RedirectUri에 대해 적절 합니다. |
| &nbsp; | &nbsp; | &nbsp; |

## <a name="verify-with-sql-server-management-studio"></a>SQL Server Management Studio 확인

C# 프로그램을 실행 하기 전에 SSMS (SQL Server Management Studio)에서 설치 및 구성이 올바른지 확인 하는 것이 좋습니다. 그런 후에 C# 프로그램 오류 원인을 소스 코드로 좁혀갈 수 있습니다.

### <a name="verify-sql-database-firewall-ip-addresses"></a>SQL Database 방화벽 IP 주소 확인

C# 프로그램을 실행하려는 같은 컴퓨터, 같은 건물에서 SSMS를 실행합니다. 이 테스트의 경우 **인증** 모드는 정상입니다. 데이터베이스 서버 방화벽에서 IP 주소를 허용하지 않는 경우 [Azure SQL Database 서버 수준 및 데이터베이스 수준 방화벽 규칙](sql-database-firewall-configure.md)을 참조하여 도움을 얻을 수 있습니다.

### <a name="verify-azure-active-directory-multi-factor-authentication"></a>Azure Active Directory Multi-Factor Authentication 확인

이번에는 **인증**을 **Active Directory - MFA 지원을 통한 유니버설 인증**으로 설정하고 SSMS를 다시 실행합니다. 이 옵션에는 SSMS 버전 17.5 이상이 필요합니다.

자세한 내용은 [SSMS 및 AZURE AD에 대 한 Multi-Factor Authentication 구성](sql-database-ssms-mfa-authentication-configure.md)을 참조 하세요.

> [!NOTE]
> 데이터베이스의 게스트 사용자 인 경우 데이터베이스에 대 한 Azure AD 도메인 이름을 제공 해야 합니다. **옵션** > **AD 도메인 이름 또는 테넌트 ID**를 선택 합니다. Azure Portal에서 도메인 이름을 찾으려면 **Azure Active Directory** > **사용자 지정 도메인 이름**을 선택합니다. C# 예제 프로그램에서는 도메인 이름을 지정하지 않아도 됩니다.

## <a name="c-code-example"></a>C# 코드 예제

예제 C# 프로그램은 [*Microsoft.IdentityModel.Clients.ActiveDirectory*](https://docs.microsoft.com/dotnet/api/microsoft.identitymodel.clients.activedirectory) DLL 어셈블리를 사용합니다.

Visual Studio에서 이 패키지를 설치하려면 **프로젝트** > **NuGet 패키지 관리**를 선택합니다. **Microsoft.IdentityModel.Clients.ActiveDirectory**를 검색하고 설치합니다.

소스 코드의 C# 예제입니다.

```csharp

using System;

// Reference to Azure AD authentication assembly
using Microsoft.IdentityModel.Clients.ActiveDirectory;

using DA = System.Data;
using SC = System.Data.SqlClient;
using AD = Microsoft.IdentityModel.Clients.ActiveDirectory;
using TX = System.Text;
using TT = System.Threading.Tasks;

namespace ADInteractive5
{
    class Program
    {
        // ASSIGN YOUR VALUES TO THESE STATIC FIELDS !!
        static public string Az_SQLDB_svrName = "<Your SQL DB server>";
        static public string AzureAD_UserID = "<Your User ID>";
        static public string Initial_DatabaseName = "<Your Database>";
        // Some scenarios do not need values for the following two fields:
        static public readonly string ClientApplicationID = "<Your App ID>";
        static public readonly Uri RedirectUri = new Uri("<Your URI>");

        public static void Main(string[] args)
        {
            var provider = new ActiveDirectoryAuthProvider();

            SC.SqlAuthenticationProvider.SetProvider(
                SC.SqlAuthenticationMethod.ActiveDirectoryInteractive,
                //SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated,  // Alternatives.
                //SC.SqlAuthenticationMethod.ActiveDirectoryPassword,
                provider);

            Program.Connection();
        }

        public static void Connection()
        {
            SC.SqlConnectionStringBuilder builder = new SC.SqlConnectionStringBuilder();

            // Program._  static values that you set earlier.
            builder["Data Source"] = Program.Az_SQLDB_svrName;
            builder.UserID = Program.AzureAD_UserID;
            builder["Initial Catalog"] = Program.Initial_DatabaseName;

            // This "Password" is not used with .ActiveDirectoryInteractive.
            //builder["Password"] = "<YOUR PASSWORD HERE>";

            builder["Connect Timeout"] = 15;
            builder["TrustServerCertificate"] = true;
            builder.Pooling = false;

            // Assigned enum value must match the enum given to .SetProvider().
            builder.Authentication = SC.SqlAuthenticationMethod.ActiveDirectoryInteractive;
            SC.SqlConnection sqlConnection = new SC.SqlConnection(builder.ConnectionString);

            SC.SqlCommand cmd = new SC.SqlCommand(
                "SELECT '******** MY QUERY RAN SUCCESSFULLY!! ********';",
                sqlConnection);

            try
            {
                sqlConnection.Open();
                if (sqlConnection.State == DA.ConnectionState.Open)
                {
                    var rdr = cmd.ExecuteReader();
                    var msg = new TX.StringBuilder();
                    while (rdr.Read())
                    {
                        msg.AppendLine(rdr.GetString(0));
                    }
                    Console.WriteLine(msg.ToString());
                    Console.WriteLine(":Success");
                }
                else
                {
                    Console.WriteLine(":Failed");
                }
                sqlConnection.Close();
            }
            catch (Exception ex)
            {
                Console.ForegroundColor = ConsoleColor.Red;
                Console.WriteLine("Connection failed with the following exception...");
                Console.WriteLine(ex.ToString());
                Console.ResetColor();
            }
        }
    } // EOClass Program.

    /// <summary>
    /// SqlAuthenticationProvider - Is a public class that defines 3 different Azure AD
    /// authentication methods.  The methods are supported in the new .NET 4.7.2.
    ///  . 
    /// 1. Interactive,  2. Integrated,  3. Password
    ///  . 
    /// All 3 authentication methods are based on the Azure
    /// Active Directory Authentication Library (ADAL) managed library.
    /// </summary>
    public class ActiveDirectoryAuthProvider : SC.SqlAuthenticationProvider
    {
        // Program._ more static values that you set!
        private readonly string _clientId = Program.ClientApplicationID;
        private readonly Uri _redirectUri = Program.RedirectUri;

        public override async TT.Task<SC.SqlAuthenticationToken>
            AcquireTokenAsync(SC.SqlAuthenticationParameters parameters)
        {
            AD.AuthenticationContext authContext =
                new AD.AuthenticationContext(parameters.Authority);
            authContext.CorrelationId = parameters.ConnectionId;
            AD.AuthenticationResult result;

            switch (parameters.AuthenticationMethod)
            {
                case SC.SqlAuthenticationMethod.ActiveDirectoryInteractive:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,  // "https://database.windows.net/"
                        _clientId,
                        _redirectUri,
                        new AD.PlatformParameters(AD.PromptBehavior.Auto),
                        new AD.UserIdentifier(
                            parameters.UserId,
                            AD.UserIdentifierType.RequiredDisplayableId));
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_1 == '.ActiveDirectoryIntegrated'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserCredential());
                    break;

                case SC.SqlAuthenticationMethod.ActiveDirectoryPassword:
                    Console.WriteLine("In method 'AcquireTokenAsync', case_2 == '.ActiveDirectoryPassword'.");

                    result = await authContext.AcquireTokenAsync(
                        parameters.Resource,
                        _clientId,
                        new AD.UserPasswordCredential(
                            parameters.UserId,
                            parameters.Password));
                    break;

                default: throw new InvalidOperationException();
            }
            return new SC.SqlAuthenticationToken(result.AccessToken, result.ExpiresOn);
        }

        public override bool IsSupported(SC.SqlAuthenticationMethod authenticationMethod)
        {
            return authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryIntegrated
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryInteractive
                || authenticationMethod == SC.SqlAuthenticationMethod.ActiveDirectoryPassword;
        }
    } // EOClass ActiveDirectoryAuthProvider.
} // EONamespace.  End of entire program source code.

```

&nbsp;

이는 C# 테스트 출력의 예입니다.

```
[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>> ADInteractive5.exe
In method 'AcquireTokenAsync', case_0 == '.ActiveDirectoryInteractive'.
******** MY QUERY RAN SUCCESSFULLY!! ********

:Success

[C:\Test\VSProj\ADInteractive5\ADInteractive5\bin\Debug\]
>>
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Azure SQL Database, Azure Resource Manager PowerShell 모듈은 계속 지원하지만 모든 향후 개발은 Az.Sql 모듈에 대해 진행됩니다. 이러한 cmdlet에 대 한 자세한 내용은 [AzureRM](https://docs.microsoft.com/powershell/module/AzureRM.Sql/)를 참조 하세요. Az 모듈과 AzureRm 모듈에서 명령의 인수는 실질적으로 동일합니다.

- [AzSqlServerActiveDirectoryAdministrator](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserveractivedirectoryadministrator)
