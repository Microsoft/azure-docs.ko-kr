---
title: 자습서`:` 관리 ID를 사용하여 Azure SQL Database에 액세스 - Windows - Azure AD
description: Windows VM 시스템 할당 관리 ID를 사용하여 Azure SQL Database에 액세스하는 프로세스를 단계별로 안내하는 자습서입니다.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d576fb4f5dea10a2adf0d7488aa422e1397fd6d1
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255752"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-sql"></a>자습서: Windows VM 시스템 할당 관리 ID를 사용하여 Azure SQL에 액세스

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

이 자습서에서는 Windows VM(가상 머신)에 대한 시스템 할당 ID를 사용하여 Azure SQL Database에 액세스하는 방법을 보여 줍니다. Azure에서 자동으로 관리되는 관리 서비스 ID를 사용하면 Azure AD 인증을 지원하는 서비스에 인증할 수 있으므로 코드에 자격 증명을 삽입할 필요가 없습니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
>
> * VM에 Azure SQL Database에 대한 액세스 권한 부여
> * Azure AD 인증 사용
> * VM의 시스템 할당 ID를 나타내는 데이터베이스에 포함된 사용자를 만듭니다.
> * VM ID를 사용하여 액세스 토큰을 가져온 다음, Azure SQL Database를 쿼리하는 데 사용

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="enable"></a>사용

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]

## <a name="grant-access"></a>액세스 권한 부여

Azure SQL Databaser의 데이터베이스에 대한 액세스 권한을 VM에 부여하려면 기존 [논리 SQL 서버](../../azure-sql/database/logical-servers.md)를 사용하거나 서버를 새로 만들면 됩니다. Azure Portal을 사용하여 새 서버 및 데이터베이스를 만들려면 다음 [Azure SQL 빠른 시작](../../azure-sql/database/single-database-create-quickstart.md)을 따릅니다. [Azure SQL 설명서](/azure/sql-database/)에서 Azure CLI 및 Azure PowerShell을 사용하는 빠른 시작도 있습니다.

VM에 데이터베이스에 대한 액세스 권한을 부여하는 과정은 다음과 같은 2단계로 진행됩니다.

1. 서버에 대한 Azure AD 인증을 사용하도록 설정합니다.
2. VM의 시스템 할당 ID를 나타내는 데이터베이스에 **포함된 사용자**를 만듭니다.

### <a name="enable-azure-ad-authentication"></a>Azure AD 인증 사용

**Azure[ AD 인증 구성하기](../../azure-sql/database/authentication-aad-configure.md):**

1. Azure Portal의 왼쪽 탐색 영역에서 **SQL Server**를 선택합니다.
2. Azure AD 인증에 사용할 SQL Server를 클릭합니다.
3. 블레이드의 **설정** 섹션에서 **Active Directory 관리자**를 클릭합니다.
4. 명령 모음에서 **Set admin**을 클릭합니다.
5. 서버 관리자로 만들 Azure AD 사용자 계정을 선택하고 **선택**을 클릭합니다.
6. 명령 모음에서 **저장**을 클릭합니다.

### <a name="create-contained-user"></a>포함된 사용자 만들기

이 섹션에서는 VM의 시스템 할당 ID를 나타내는 데이터베이스에 포함된 사용자를 만드는 방법을 보여줍니다. 이 단계에서는 [Microsoft SSMS(SQL Server Management Studio)](/sql/ssms/download-sql-server-management-studio-ssms)가 필요합니다. 시작하기 전에 다음 문서를 검토하여 Azure AD 통합의 배경 정보를 숙지하면 도움이 될 수 있습니다.

- [SQL Database 및 Azure Synapse Analytics를 통한 유니버설 인증(MFA에 대한 SSMS 지원)](../../azure-sql/database/authentication-mfa-ssms-overview.md)
- [SQL Database 또는 Azure Synapse Analytics를 사용하여 Azure Active Directory 인증 구성 및 관리](../../azure-sql/database/authentication-aad-configure.md)

SQL DB에는 고유한 AAD 표시 이름이 필요합니다. 이를 통해 사용자, 그룹 및 서비스 주체(애플리케이션)와 같은 AAD 계정 및 관리 ID에 사용하도록 설정된 VM 이름은 해당 표시 이름과 관련하여 AAD에서 고유하게 정의되어야 합니다. SQL DB는 이러한 사용자의 T-SQL을 만드는 중에 AAD 표시 이름을 확인하고, 고유하지 않은 경우 지정된 계정에 고유한 AAD 표시 이름을 제공하도록 요청하는 데 실패합니다.

**포함된 사용자를 만들려면 다음을 수행합니다.**

1. SQL Server Management Studio를 시작합니다.
2. **서버에 연결** 대화 상자의 **서버 이름** 필드에 서버 이름을 입력합니다.
3. **인증** 필드에서 **Active Directory - MFA 지원을 통한 유니버설 인증**을 선택합니다.
4. **사용자 이름** 필드에 서버 관리자로 설정한 Azure AD 계정의 이름(예: helen@woodgroveonline.com)을 입력합니다.
5. **옵션**을 클릭합니다.
6. **연결할 데이터베이스** 필드에 구성하려면 비시스템 데이터베이스의 이름을 입력합니다.
7. **연결**을 클릭합니다. 로그인 프로세스를 완료합니다.
8. **개체 탐색기**에서 **데이터베이스** 폴더를 확장합니다.
9. 사용자 데이터베이스를 마우스 오른쪽 단추로 클릭하고 **새 쿼리**를 클릭합니다.
10. 쿼리 창에서 다음 줄을 입력하고 도구 모음에서 **실행**을 클릭합니다.

    > [!NOTE]
    > 다음 명령의 `VMName`은 필수 구성 요소 섹션에서 시스템 할당 ID를 사용하도록 설정한 VM의 이름입니다.

    ```sql
    CREATE USER [VMName] FROM EXTERNAL PROVIDER
    ```

    이 명령은 성공적으로 완료되고 VM의 시스템 할당 ID에 대해 포함된 사용자가 만들어집니다.
11. 쿼리 창을 지우고 다음 줄을 입력한 후 도구 모음에서 **실행**을 클릭합니다.

    > [!NOTE]
    > 다음 명령의 `VMName`은 필수 구성 요소 섹션에서 시스템 할당 ID를 사용하도록 설정한 VM의 이름입니다.

    ```sql
    ALTER ROLE db_datareader ADD MEMBER [VMName]
    ```

    명령이 성공적으로 완료되고 포함된 사용자에게 전체 데이터베이스를 읽을 수 있는 기능이 부여됩니다.

이제 VM에서 실행되는 코드는 해당 시스템 할당 관리 ID를 사용하여 토큰을 가져오고 해당 토큰을 사용하여 서버 인증을 받을 수 있습니다.

## <a name="access-data"></a>데이터 액세스

이 섹션에서는 VM의 시스템 할당 관리 ID를 사용하여 액세스 토큰을 가져오고 이를 사용하여 Azure SQL을 호출하는 방법을 보여줍니다. Azure SQL은 기본적으로 Azure AD 인증을 지원하므로 Azure 리소스에 대한 관리 ID를 사용하여 획득한 액세스 토큰을 직접 수락할 수 있습니다. SQL에 대한 연결을 만들기 위해 **액세스 토큰** 방법을 사용합니다. 이 방법은 Azure SQL을 Azure AD와 통합하는 작업의 일부로, 연결 문자열에서 자격 증명을 제공하는 것과는 다릅니다.

다음은 액세스 토큰을 사용하여 SQL에 대한 연결을 여는 .NET 코드 예제입니다. VM의 시스템 할당 관리 ID의 엔드포인트에 액세스하기 위해서는 코드가 VM에서 실행되어야 합니다. 액세스 토큰 방법을 사용하려면 **.NET Framework 4.6** 이상 또는 **.NET Core 2.2** 이상이 필요합니다. 그에 따라 AZURE-SQL-SERVERNAME 및 DATABASE 값을 바꿉니다. Azure SQL에 대한 리소스 ID는 `https://database.windows.net/`입니다.

```csharp
using System.Net;
using System.IO;
using System.Data.SqlClient;
using System.Web.Script.Serialization;

//
// Get an access token for SQL.
//
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://database.windows.net/");
request.Headers["Metadata"] = "true";
request.Method = "GET";
string accessToken = null;

try
{
    // Call managed identities for Azure resources endpoint.
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader and extract access token.
    StreamReader streamResponse = new StreamReader(response.GetResponseStream());
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

//
// Open a connection to the server using the access token.
//
if (accessToken != null) {
    string connectionString = "Data Source=<AZURE-SQL-SERVERNAME>; Initial Catalog=<DATABASE>;";
    SqlConnection conn = new SqlConnection(connectionString);
    conn.AccessToken = accessToken;
    conn.Open();
}
```

또는 VM에서 앱을 작성 및 배포하지 않고도 PowerShell을 사용하여 전체적인 설정을 빠르게 테스트할 수도 있습니다.

1. Portal에서 **Virtual Machines** -> Windows Virtual Machines로 이동한 다음 **개요**에서 **연결**을 클릭합니다.
2. Windows VM을 만들 때 추가한 **사용자 이름**과 **암호**를 입력합니다.
3. 이제 가상 머신에 대한 **원격 데스크톱 연결**을 만들었으므로 원격 세션에서 **PowerShell**을 엽니다.
4. PowerShell의 `Invoke-WebRequest`를 사용하여 로컬 관리 ID의 엔드포인트에 대한 요청을 수행해 Azure SQL용 액세스 토큰을 가져옵니다.

    ```powershell
        $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatabase.windows.net%2F' -Method GET -Headers @{Metadata="true"}
    ```

    JSON 개체에서 PowerShell 개체로 응답을 변환합니다.

    ```powershell
    $content = $response.Content | ConvertFrom-Json
    ```

    응답에서 액세스 토큰을 추출합니다.

    ```powershell
    $AccessToken = $content.access_token
    ```

5. 서버에 대한 연결을 엽니다. AZURE-SQL-SERVERNAME 및 DATABASE 값을 바꿉니다.

    ```powershell
    $SqlConnection = New-Object System.Data.SqlClient.SqlConnection
    $SqlConnection.ConnectionString = "Data Source = <AZURE-SQL-SERVERNAME>; Initial Catalog = <DATABASE>"
    $SqlConnection.AccessToken = $AccessToken
    $SqlConnection.Open()
    ```

    다음으로 쿼리를 만든 후 서버로 보냅니다. TABLE 값을 바꿉니다.

    ```powershell
    $SqlCmd = New-Object System.Data.SqlClient.SqlCommand
    $SqlCmd.CommandText = "SELECT * from <TABLE>;"
    $SqlCmd.Connection = $SqlConnection
    $SqlAdapter = New-Object System.Data.SqlClient.SqlDataAdapter
    $SqlAdapter.SelectCommand = $SqlCmd
    $DataSet = New-Object System.Data.DataSet
    $SqlAdapter.Fill($DataSet)
    ```

`$DataSet.Tables[0]` 값을 확인하여 쿼리의 결과를 검토합니다.

## <a name="disable"></a>사용 안 함

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]

## <a name="next-steps"></a>다음 단계

이 자습서에서는 시스템 할당 관리 ID를 사용하여 Azure SQL Database에 액세스하는 방법을 알아보았습니다. Azure SQL Database에 대한 자세한 정보는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Azure SQL Database](../../azure-sql/database/sql-database-paas-overview.md)