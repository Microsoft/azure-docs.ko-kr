---
title: 최종 사용자 인증-Data Lake Storage Gen1를 사용 하는 .NET-Azure
description: Azure Active Directory와 .NET SDK를 사용하여 Azure Data Lake Storage Gen1로 최종 사용자 인증을 수행하는 방법을 알아봅니다.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.custom: devx-track-csharp
ms.openlocfilehash: 67ba4f12aec9e987d79109b7197d03301bf40650
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004785"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>.NET SDK를 사용하여 Azure Data Lake Storage Gen1로 최종 사용자 인증
> [!div class="op_single_selector"]
> * [Java 사용](data-lake-store-end-user-authenticate-java-sdk.md)
> * [.NET SDK 사용](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Python 사용](data-lake-store-end-user-authenticate-python.md)
> * [REST API 사용](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

이 문서에서는 .NET SDK를 사용하여 Azure Data Lake Storage Gen1로 최종 사용자 인증을 수행하는 방법을 배웁니다. .NET SDK를 사용하여 Data Lake Storage Gen1에서 서비스 간 인증을 수행하려면 [.NET SDK를 사용한 Data Lake Storage Gen1의 서비스 간 인증](data-lake-store-service-to-service-authenticate-net-sdk.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소
* **Visual Studio 2013 이상** 아래 지침에서는 Visual Studio 2019을 사용 합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* **Azure Active Directory "네이티브" 애플리케이션을 만듭니다**. [Azure Active Directory를 사용하여 Data Lake Storage Gen1로 최종 사용자 인증](data-lake-store-end-user-authenticate-using-active-directory.md)의 단계를 완료해야 합니다.

## <a name="create-a-net-application"></a>.NET 애플리케이션 만들기
1. Visual Studio에서 **파일** 메뉴, **새로 만들기**, **프로젝트**를 차례로 선택 합니다.
2. **콘솔 앱 (.NET Framework)** 을 선택 하 고 **다음**을 선택 합니다.
3. **프로젝트 이름**에 `CreateADLApplication`를 입력하고 **만들기**를 선택합니다.

4. NuGet 패키지를 프로젝트에 추가합니다.

   1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
   2. **NuGet 패키지 관리자** 탭에서 **패키지 원본** 이 **nuget.org** 로 설정 되어 있고 **시험판 포함** 확인란이 선택 되어 있는지 확인 합니다.
   3. 다음 NuGet 패키지를 검색하고 설치합니다.

      * `Microsoft.Azure.Management.DataLake.Store` - 이 자습서에서는 v2.1.3-미리 보기를 사용합니다.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 이 자습서는 v2.2.12를 사용합니다.

        ![NuGet 소스 추가](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "새 Azure Data Lake 계정 만들기")
   4. **NuGet 패키지 관리자**를 닫습니다.

5. **Program.cs** 열기
6. using 문을 다음 줄로 바꿉니다.

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>최종 사용자 인증
.NET 클라이언트 애플리케이션에서 이 코드 조각을 추가합니다. 자리 표시자 값을 Microsoft Azure Active Directory 네이티브 애플리케이션에서 검색된 값(필수 구성 요소로 나열된)으로 바꿉니다. 이 코드 조각을 사용하면 Data Lake Storage Gen1로 애플리케이션을 **대화형으로** 인증할 수 있습니다. 다시 말해서, Azure 자격 증명을 입력하라는 메시지가 표시됩니다.

사용 편의를 위해, 다음 코드 조각은 모든 Azure 구독에 유효한 클라이언트 ID 및 리디렉션 URI에 기본값을 사용합니다. 다음 코드 조각에서 테넌트 ID 값만 입력하면 됩니다. [테넌트 ID 가져오기](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)에 제공된 지침에 따라 테넌트 ID를 검색할 수 있습니다.
    
- Main() 함수를 다음 코드로 바꿉니다.

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

위의 코드 조각에 대해 몇 가지 알아야 할 사항이 있습니다.

* 선행 코드 조각은 도우미 함수 `GetTokenCache` 및 `GetCreds_User_Popup`을 사용합니다. 이 도우미 함수에 대한 코드는 [여기 GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache)에서 사용할 수 있습니다.
* 이 코드 조각은 자습서를 신속하게 완료할 수 있도록 기본적으로 모든 Azure 구독에서 사용할 수 있는 원시 애플리케이션 클라이언트 ID를 사용합니다. 따라서 **이 코드 조각을 애플리케이션에서 있는 그대로 사용**할 수 있습니다.
* 하지만, 자체적인 Azure AD 도메인과 애플리케이션 클라이언트 ID를 사용하려면 Azure AD 네이티브 애플리케이션을 만든 다음 그 애플리케이션에 대한 Azure AD 테넌트 ID, 클라이언트 ID 및 리디렉션 URI를 사용해야 합니다. 지침은 [Data Lake Storage Gen1을 사용하여 최종 사용자 인증을 위한 Active Directory 애플리케이션 만들기](data-lake-store-end-user-authenticate-using-active-directory.md)를 참조하세요.

  
## <a name="next-steps"></a>다음 단계
이 문서에서는 최종 사용자 인증을 사용하여 .NET SDK로 Azure Data Lake Storage Gen1을 인증하는 방법을 배웠습니다. 이제 다음 문서를 통해 .NET SDK를 Azure Data Lake Storage Gen1과 함께 사용하는 방법을 살펴볼 수 있습니다.

* [.NET SDK를 사용한 Data Lake Storage Gen1의 계정 관리 작업](data-lake-store-get-started-net-sdk.md)
* [.NET SDK를 사용한 Data Lake Storage Gen1의 데이터 작업](data-lake-store-data-operations-net-sdk.md)

