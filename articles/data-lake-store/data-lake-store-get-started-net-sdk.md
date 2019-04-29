---
title: '.NET SDK: 계정 관리 작업 Azure 데이터 레이크 저장소 Gen1 | Microsoft Docs'
description: Azure Data Lake Storage Gen1 .NET SDK를 사용하여 Data Lake Storage Gen1 계정에서 계정 관리 작업 수행
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8ab051d49e7ed67e642ef656dfb382ed07763ed2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877442"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>.NET SDK를 사용한 Azure Data Lake Storage Gen1의 계정 관리 작업
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

이 문서에서는 .NET SDK를 사용하여 Azure Data Lake Storage Gen1에서 계정 관리 작업을 수행하는 방법을 알아봅니다. 계정 관리 작업에는 Data Lake Storage Gen1 계정 만들기, Azure 구독에서 계정 나열, 계정 삭제 등이 포함됩니다.

.NET SDK를 사용하여 Data Lake Storage Gen1에서 데이터 관리 작업을 수행하는 방법에 대한 지침은 [.NET SDK를 사용한 Data Lake Storage Gen1의 파일 시스템 작업](data-lake-store-data-operations-net-sdk.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
* **Visual Studio 2013, 2015 또는 2017**. 아래 지침에서는 Visual Studio 2017을 사용합니다.

* **Azure 구독**. [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

## <a name="create-a-net-application"></a>.NET 애플리케이션 만들기
1. Visual Studio를 열고 콘솔 애플리케이션을 만듭니다.
2. **파일** 메뉴에서 **새로 만들기**를 클릭한 다음 **프로젝트**를 클릭합니다.
3. **새 프로젝트**에서 다음 값을 입력하거나 선택합니다.

   | 자산 | 값 |
   | --- | --- |
   | Category |Templates/Visual C#/Windows |
   | Template |콘솔 애플리케이션 |
   | 이름 |CreateADLApplication |
4. **확인**을 클릭하여 프로젝트를 만듭니다.
5. NuGet 패키지를 프로젝트에 추가합니다.

   1. 솔루션 탐색기에서 프로젝트 이름을 마우스 오른쪽 단추로 클릭한 후 **NuGet 패키지 관리**를 클릭합니다.
   2. **NuGet 패키지 관리자** 탭에서 **패키지 원본**이 **nuget.org**로 설정되어 있고 **시험판 포함** 확인란이 선택되어 있는지 확인합니다.
   3. 다음 NuGet 패키지를 검색하고 설치합니다.

      * `Microsoft.Azure.Management.DataLake.Store` - 이 자습서에서는 v2.1.3-미리 보기를 사용합니다.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` - 이 자습서는 v2.2.12를 사용합니다.

        ![NuGet 원본 추가](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "새 Azure Data Lake 계정 만들기")
   4. **NuGet 패키지 관리자**를 닫습니다.
6. **Program.cs**를 열고 기존 코드를 삭제한 후 다음 문을 포함시켜서 네임스페이스에 대한 참조를 추가합니다.

        using System;
        using System.IO;
        using System.Linq;
        using System.Text;
        using System.Threading;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.Management.DataLake.Store;
        using Microsoft.Azure.Management.DataLake.Store.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. 변수를 선언하고 자리 표시자에 대한 값을 제공합니다. 또한, 제공하는 로컬 경로와 파일 이름이 컴퓨터에 존재하는지 확인합니다.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

문서의 나머지 섹션에서는 제공되는 .NET 메서드를 사용하여 인증, 파일 업로드 등의 작업을 수행하는 방법을 볼 수 있습니다.

## <a name="authentication"></a>Authentication

* 응용 프로그램에 대한 최종 사용자 인증의 경우 [.NET SDK를 사용한 Data Lake Storage Gen1의 최종 사용자 인증](data-lake-store-end-user-authenticate-net-sdk.md)을 참조하세요.
* 응용 프로그램에 대한 서비스 간 인증의 경우 [.NET SDK를 사용한 Data Lake Storage Gen1의 서비스 간 인증](data-lake-store-service-to-service-authenticate-net-sdk.md)을 참조하세요.

## <a name="create-client-object"></a>클라이언트 개체 만들기
다음 코드 조각은 계정 만들기, 계정 삭제 등과 같은 서비스에 계정 관리 요청을 발급하는 데 사용되는 Data Lake Storage Gen1 계정 클라이언트 개체를 만듭니다.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 계정 만들기
다음 코드 조각은 Data Lake Storage Gen1 계정 클라이언트 개체를 만드는 동안 사용자가 제공한 Azure 구독에 Data Lake Storage Gen1 계정을 만듭니다.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>구독 내 모든 Data Lake Storage Gen1 계정 나열
클래스 정의에 다음 메서드를 추가합니다. 다음 코드 조각은 지정된 Azure 구독에 있는 모든 Data Lake Storage Gen1 계정을 나열합니다.

    // List all Data Lake Storage Gen1 accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-storage-gen1-account"></a>Data Lake Storage Gen1 계정 삭제
다음 코드 조각은 이전에 만든 Data Lake Storage Gen1 계정을 삭제합니다.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>참고 항목
* [.NET SDK를 사용한 Data Lake Storage Gen1의 파일 시스템 작업](data-lake-store-data-operations-net-sdk.md)
* [Data Lake Storage Gen1 .NET SDK 참조](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>다음 단계
* [Data Lake Storage Gen1의 데이터 보호](data-lake-store-secure-data.md)
