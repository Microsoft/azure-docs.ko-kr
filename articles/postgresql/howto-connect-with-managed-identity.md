---
title: 관리 ID를 사용하여 연결 - Azure Database for PostgreSQL - 단일 서버
description: Azure Database for PostgreSQL 인증을 위해 관리 ID를 사용하여 연결하고 인증하는 방법을 알아봅니다.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 05/19/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 1b9603e43541ec1a364e4653caeeafc751f7e4f0
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89012095"
---
# <a name="connect-with-managed-identity-to-azure-database-for-postgresql"></a>관리 ID를 사용하여 Azure Database for PostgreSQL에 연결

이 문서에서는 Azure VM(가상 머신)에 대한 사용자가 할당한 ID를 사용하여 Azure Database for PostgreSQL 서버에 액세스하는 방법을 보여 줍니다. Azure에서 자동으로 관리되는 관리 서비스 ID를 사용하면 Azure AD 인증을 지원하는 서비스에 인증할 수 있으므로 코드에 자격 증명을 삽입할 필요가 없습니다. 

다음 방법을 알아봅니다.
- Azure Database for PostgreSQL 서버에 대한 액세스 권한을 VM에 부여
- VM의 사용자가 할당한 ID를 나타내는 사용자를 데이터베이스에 만들기
- VM ID를 사용하여 액세스 토큰을 가져와서 Azure Database for PostgreSQL 서버를 쿼리하는 데 사용
- C# 예제 애플리케이션에서 토큰 검색 구현

## <a name="prerequisites"></a>사전 요구 사항

- Azure 리소스에 대한 관리 ID 기능이 익숙하지 않은 경우 [개요](../../articles/active-directory/managed-identities-azure-resources/overview.md)를 참조하세요. Azure 계정이 없으면 계속하기 전에 [체험 계정에 등록](https://azure.microsoft.com/free/)합니다.
- 필요한 리소스를 만들고 역할을 관리하려면 적절한 범위(사용자 구독 또는 리소스 그룹)의 "소유자" 권한이 계정에 필요합니다. 역할 할당에 관한 도움이 필요한 경우 [역할 기반 액세스 제어를 사용하여 Azure 구독 리소스에 대한 액세스 관리](../../articles/role-based-access-control/role-assignments-portal.md)를 참조하세요.
- 관리 ID를 사용하여 데이터베이스에 액세스하는 데 사용할 Azure VM(예: Ubuntu Linux 실행)이 필요합니다.
- [Azure AD 인증](howto-configure-sign-in-aad-authentication.md)이 구성된 Azure Database for PostgreSQL 데이터베이스 서버가 필요합니다.
- C# 예제를 따르려면 먼저 [C#을 사용하여 연결](connect-csharp.md)하는 방법 가이드를 완료합니다.

## <a name="creating-a-user-assigned-managed-identity-for-your-vm"></a>VM에 대한 사용자가 할당한 관리 ID 만들기

[az identity create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) 명령을 사용하여 구독에서 ID를 만듭니다. 가상 머신이 실행되는 것과 동일한 리소스 그룹 또는 다른 리소스 그룹을 사용할 수 있습니다.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myManagedIdentity
```

다음 단계에서 ID를 구성하려면 [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) 명령을 사용하여 ID의 리소스 ID 및 클라이언트 ID를 변수에 저장합니다.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query id --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show --resource-group myResourceGroup --name myManagedIdentity --query clientId --output tsv)
```

이제 [az vm identity assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) 명령을 사용하여 사용자가 할당한 ID를 VM에 할당할 수 있습니다.

```azurecli
az vm identity assign --resource-group myResourceGroup --name myVM --identities $resourceID
```

설정을 완료하려면 다음 몇 단계에서 필요한 클라이언트 ID 값을 표시합니다.

```bash
echo $clientID
```

## <a name="creating-a-postgresql-user-for-your-managed-identity"></a>관리 ID에 대한 PostgreSQL 사용자 만들기

이제 Azure AD 관리자 권한으로 PostgreSQL 데이터베이스에 연결하고 다음 SQL 문을 실행합니다.

```sql
SET aad_validate_oids_in_tenant = off;
CREATE ROLE myuser WITH LOGIN PASSWORD 'CLIENT_ID' IN ROLE azure_ad_user;
```

이제 `myuser`(선택한 이름으로 바꿈)라는 사용자 이름을 사용하여 인증하면 관리 ID에 액세스할 수 있습니다.

## <a name="retrieving-the-access-token-from-azure-instance-metadata-service"></a>Azure Instance Metadata Service에서 액세스 토큰 검색

이제 애플리케이션에서 Azure Instance Metadata Service를 통해 액세스 토큰을 검색하여 데이터베이스를 인증하는 데 사용할 수 있습니다.

이 토큰 검색은 `http://169.254.169.254/metadata/identity/oauth2/token`에 대한 HTTP 요청을 수행하고 다음 매개 변수를 전달하여 수행됩니다.

* `api-version` = `2018-02-01`
* `resource` = `https://ossrdbms-aad.database.windows.net`
* `client_id` = `CLIENT_ID`(이전에 검색한 항목)

`access_token` 필드가 포함된 JSON 결과가 반환됩니다. 이 긴 텍스트 값은 데이터베이스에 연결할 때 암호로 사용해야 하는 관리 ID 액세스 토큰입니다.

테스트를 위해 셸에서 다음 명령을 실행할 수 있습니다. `curl`, `jq` 및 `psql` 클라이언트를 설치해야 합니다.

```bash
# Retrieve the access token
export PGPASSWORD=`curl -s 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=CLIENT_ID' -H Metadata:true | jq -r .access_token`

# Connect to the database
psql -h SERVER --user USER@SERVER DBNAME
```

이제 이전에 구성한 데이터베이스에 연결되었습니다.

## <a name="connecting-using-managed-identity-in-c"></a>C#에서 관리 ID를 사용하여 연결

이 섹션에서는 VM의 사용자가 할당한 관리 ID를 사용하여 액세스 토큰을 가져오고, 이를 사용하여 Azure Database for PostgreSQL을 호출하는 방법을 보여 줍니다. Azure Database for PostgreSQL은 기본적으로 Azure AD 인증을 지원하므로 Azure 리소스에 대한 관리 ID를 사용하여 획득한 액세스 토큰을 직접 수락할 수 있습니다. PostgreSQL에 대한 연결을 만들 때 액세스 토큰을 암호 필드에 전달합니다.

다음은 액세스 토큰을 사용하여 PostgreSQL에 대한 연결을 여는 .NET 코드 예제입니다. 이 코드는 VM의 사용자가 할당한 관리 ID의 엔드포인트에 액세스하기 위해 VM에서 실행해야 합니다. 액세스 토큰 방법을 사용하려면 .NET Framework 4.6 이상 또는 .NET Core 2.2 이상이 필요합니다. HOST, USER, DATABASE 및 CLIENT_ID의 값을 바꿉니다.

```csharp
using System;
using System.Net;
using System.IO;
using System.Collections;
using System.Collections.Generic;
using System.Text.Json;
using System.Text.Json.Serialization;
using Npgsql;

namespace Driver
{
    class Script
    {
        // Obtain connection string information from the portal
        //
        private static string Host = "HOST";
        private static string User = "USER";
        private static string Database = "DATABASE";
        private static string ClientId = "CLIENT_ID";

        static void Main(string[] args)
        {
            //
            // Get an access token for PostgreSQL.
            //
            Console.Out.WriteLine("Getting access token from Azure Instance Metadata service...");

            // Azure AD resource ID for Azure Database for PostgreSQL is https://ossrdbms-aad.database.windows.net/
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fossrdbms-aad.database.windows.net&client_id=" + ClientId);
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
                var list = JsonSerializer.Deserialize<Dictionary<string, string>>(stringResponse);
                accessToken = list["access_token"];
            }
            catch (Exception e)
            {
                Console.Out.WriteLine("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                System.Environment.Exit(1);
            }

            //
            // Open a connection to the PostgreSQL server using the access token.
            //
            string connString =
                String.Format(
                    "Server={0}; User Id={1}; Database={2}; Port={3}; Password={4};SSLMode=Prefer",
                    Host,
                    User,
                    Database,
                    5432,
                    accessToken);

            using (var conn = new NpgsqlConnection(connString))
            {
                Console.Out.WriteLine("Opening connection using access token...");
                conn.Open();

                using (var command = new NpgsqlCommand("SELECT version()", conn))
                {

                    var reader = command.ExecuteReader();
                    while (reader.Read())
                    {
                        Console.WriteLine("\nConnected!\n\nPostgres version: {0}", reader.GetString(0));
                    }
                }
            }
        }
    }
}
```

이 명령을 실행하면 다음과 같은 출력이 제공됩니다.

```
Getting access token from Azure Instance Metadata service...
Opening connection using access token...

Connected!

Postgres version: PostgreSQL 11.6, compiled by Visual C++ build 1800, 64-bit
```

## <a name="next-steps"></a>다음 단계

* [Azure Database for PostgreSQL로 Azure Active Directory 인증](concepts-aad-authentication.md)에 대한 전반적인 개념 검토
