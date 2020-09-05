---
title: '빠른 시작: 첫 번째 .NET Core 쿼리'
description: 이 빠른 시작에서는 .NET Core용 Resource Graph NuGet 패키지를 사용하도록 설정하고 첫 번째 쿼리를 실행하는 단계를 수행합니다.
ms.date: 06/29/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 0470f100b6323f760bc48af70e8a6c11b94dfb3c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89005873"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>빠른 시작: .NET Core를 사용하여 첫 번째 Resource Graph 쿼리 실행

Azure Resource Graph를 사용하는 첫 번째 단계는 .NET Core에 필요한 패키지가 설치되었는지 확인하는 것입니다. 이 빠른 시작에서는 설치된 .NET Core에 패키지를 추가하는 과정을 안내합니다.

이 과정이 끝나면 설치된 .NET Core에 패키지가 추가되고 첫 번째 Resource Graph 쿼리를 실행할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.
- _clientId_ 및 _clientSecret_을 포함하는 Azure 서비스 주체. Resource Graph에 사용할 서비스 주체가 없거나 새로 만들려면 [.NET 인증을 위한 Azure 관리 라이브러리](/dotnet/azure/sdk/authentication#mgmt-auth)를 참조하세요.
  .NET Core 패키지를 설치하는 단계는 건너뜁니다. 다음 단계에서 수행할 것입니다.

## <a name="create-the-resource-graph-project"></a>Resource Graph 프로젝트 만들기

.NET Core를 사용하여 Azure Resource Graph를 쿼리하려면 새 콘솔 애플리케이션을 만들고 필요한 패키지를 설치합니다.

1. 최신 .NET Core가 설치되었는지 확인합니다(**3.1.5** 이상). 아직 설치되지 않았으면 [dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core)에서 다운로드합니다.

1. 다음과 같이 "argQuery"라는 새 .NET Core 콘솔 애플리케이션을 초기화합니다.

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. 디렉터리를 새 프로젝트 폴더로 변경하고 Azure Resource Graph에 필요한 패키지를 설치합니다.

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. 기본 `program.cs`를 다음 코드로 바꾸고 업데이트된 파일을 저장합니다.

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. `argQuery` 콘솔 애플리케이션을 빌드하고 게시합니다.

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>첫 번째 Resource Graph 실행

.NET Core 콘솔 애플리케이션을 빌드하고 게시한 후에는 간단한 Resource Graph 쿼리를 시도해 봅니다. 쿼리는 각 리소스의 **이름** 및 **리소스 형식**과 함께 처음 5개 Azure 리소스를 반환합니다.

`argQuery`를 호출할 때마다 다음 변수를 각자 해당하는 값으로 바꿔야 합니다.

- `{tenantId}` - 테넌트 ID로 대체
- `{clientId}` - 서비스 주체의 클라이언트 ID로 대체
- `{clientSecret}` - 서비스 주체의 클라이언트 암호로 대체
- `{subscriptionId}` - 사용자의 구독 ID로 대체

1. 디렉터리를 이전에 `dotnet publish` 명령을 사용하여 정의한 `{run-folder}`로 변경합니다.

1. 다음과 같이 컴파일된 .NET Core 애플리케이션을 사용하여 첫 번째 Azure Resource Graph 쿼리를 실행합니다.

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > 이 쿼리 예제에서는 `order by`와 같은 정렬 한정자를 제공하지 않으므로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다.

1. 마지막 매개 변수를 `argQuery.exe`로 변경하고, **Name** 속성을 기준으로 `order by`하도록 쿼리를 변경합니다.

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > 첫 번째 쿼리와 마찬가지로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다. 쿼리 명령의 순서는 중요합니다. 이 예제에서 `order by`는 `limit` 뒤에 옵니다. 이 명령 순서는 먼저 쿼리 결과를 제한한 다음, 정렬합니다.

1. 마지막 매개 변수를 `argQuery.exe`로 변경하고, 먼저 **Name** 속성을 기준으로 `order by`한 후 상위 5개 결과로 `limit`하도록 쿼리를 변경합니다.

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

최종 쿼리가 여러 번 실행될 때 환경이 전혀 변경되지 않는다고 가정하면 반환되는 결과는 **Name** 속성을 기준으로 일관되고 정렬되지만 여전히 상위 5개 결과로 제한됩니다.

## <a name="clean-up-resources"></a>리소스 정리

.NET Core 콘솔 애플리케이션 및 설치된 패키지를 제거하려면 `argQuery` 프로젝트 폴더를 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 필요한 Resource Graph 패키지가 설치된 .NET Core 콘솔 애플리케이션을 만들고 첫 번째 쿼리를 실행했습니다. 리소스 그래프 언어에 대한 자세한 내용을 보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](./concepts/query-language.md)