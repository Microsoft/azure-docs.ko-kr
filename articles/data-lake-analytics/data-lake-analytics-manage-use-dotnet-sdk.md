---
title: Azure .NET SDK를 사용하여 Azure Data Lake Analytics 관리
description: 이 문서에서는 Azure.NET SDK를 사용 하 여 Data Lake Analytics 작업, 데이터 원본 및 사용자를 관리 하는 앱을 작성 하는 방법을 설명 합니다.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 811d172d-9873-4ce9-a6d5-c1a26b374c79
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/18/2017
ms.openlocfilehash: 0a10af73d754596e9b5bb34b2974d7f1647d06f8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60617710"
---
# <a name="manage-azure-data-lake-analytics-a-net-app"></a>Azure Data Lake Analytics .NET 앱 관리

[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

이 문서에서는 Azure .NET SDK를 사용하여 작성한 앱을 사용하여 Azure Data Lake Analytics 계정, 데이터 원본, 사용자 및 작업을 관리하는 방법을 설명합니다. 

## <a name="prerequisites"></a>필수 조건

* **Visual Studio 2015, Visual Studio 2013 업데이트 4 또는 Visual Studio 2012와 Visual C++ 설치**.
* **.NET 버전 2.5 이상용 Microsoft Azure SDK**.  [웹 플랫폼 설치 관리자](https://www.microsoft.com/web/downloads/platform.aspx)를 사용하여 설치합니다.
* **필요한 NuGet 패키지**

### <a name="install-nuget-packages"></a>NuGet 패키지 설치

|패키지|Version|
|-------|-------|
|[Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication)| 2.3.1|
|[Microsoft.Azure.Management.DataLake.Analytics](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Analytics)|3.0.0|
|[Microsoft.Azure.Management.DataLake.Store](https://www.nuget.org/packages/Microsoft.Azure.Management.DataLake.Store)|2.2.0|
|[Microsoft.Azure.Management.ResourceManager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|1.6.0-preview|
|[Microsoft.Azure.Graph.RBAC](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager)|3.4.0-preview|

NuGet 명령줄을 통해 다음 명령을 사용하여 이러한 패키지를 설치할 수 있습니다.

```powershell
Install-Package -Id Microsoft.Rest.ClientRuntime.Azure.Authentication  -Version 2.3.1
Install-Package -Id Microsoft.Azure.Management.DataLake.Analytics  -Version 3.0.0
Install-Package -Id Microsoft.Azure.Management.DataLake.Store  -Version 2.2.0
Install-Package -Id Microsoft.Azure.Management.ResourceManager  -Version 1.6.0-preview
Install-Package -Id Microsoft.Azure.Graph.RBAC -Version 3.4.0-preview
```

## <a name="common-variables"></a>일반적인 변수

```csharp
string subid = "<Subscription ID>"; // Subscription ID (a GUID)
string tenantid = "<Tenant ID>"; // AAD tenant ID or domain. For example, "contoso.onmicrosoft.com"
string rg == "<value>"; // Resource  group name
string clientid = "1950a258-227b-4e31-a9cf-717495945fc2"; // Sample client ID (this will work, but you should pick your own)
```

## <a name="authentication"></a>Authentication

Azure Data Lake Analytics에 로그온하는 옵션은 여러 가지가 있습니다. 다음 코드 조각은 팝업을 사용한 대화형 사용자 인증의 인증 예를 보여 줍니다.

``` csharp
using System;
using System.IO;
using System.Threading;
using System.Security.Cryptography.X509Certificates;

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Analytics;
using Microsoft.Azure.Management.DataLake.Analytics.Models;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Graph.RBAC;

public static Program
{
   public static string TENANT = "microsoft.onmicrosoft.com";
   public static string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
   public static System.Uri ARM_TOKEN_AUDIENCE = new System.Uri( @"https://management.core.windows.net/");
   public static System.Uri ADL_TOKEN_AUDIENCE = new System.Uri( @"https://datalake.azure.net/" );
   public static System.Uri GRAPH_TOKEN_AUDIENCE = new System.Uri( @"https://graph.windows.net/" );

   static void Main(string[] args)
   {
      string MY_DOCUMENTS= System.Environment.GetFolderPath( System.Environment.SpecialFolder.MyDocuments);
      string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");

      var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
      var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
      var graphCreds = GetCreds_User_Popup(TENANT, GRAPH_TOKEN_AUDIENCE, CLIENTID, tokenCache);
   }
}
```

**GetCreds_User_Popup**의 소스 코드와 인증에 대한 다른 옵션 코드는 [Data Lake Analytics .NET authentication options](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options)(Data Lake Analytics .NET 인증 옵션)에 설명되어 있습니다.


## <a name="create-the-client-management-objects"></a>클라이언트 관리 개체 만들기

``` csharp
var resourceManagementClient = new ResourceManagementClient(armCreds) { SubscriptionId = subid };

var adlaAccountClient = new DataLakeAnalyticsAccountManagementClient(armCreds);
adlaAccountClient.SubscriptionId = subid;

var adlsAccountClient = new DataLakeStoreAccountManagementClient(armCreds);
adlsAccountClient.SubscriptionId = subid;

var adlaCatalogClient = new DataLakeAnalyticsCatalogManagementClient(adlCreds);
var adlaJobClient = new DataLakeAnalyticsJobManagementClient(adlCreds);

var adlsFileSystemClient = new DataLakeStoreFileSystemManagementClient(adlCreds);

var  graphClient = new GraphRbacManagementClient(graphCreds);
graphClient.TenantID = domain;

```

## <a name="manage-accounts"></a>계정 관리

### <a name="create-an-azure-resource-group"></a>Azure 리소스 그룹 만들기

아직 만들지 않은 경우에는 반드시 Azure 리소스 그룹을 만들어 Data Lake Analytics 구성 요소를 만들어야 합니다. 인증 자격 증명, 구독 ID 및 위치가 필요합니다. 다음 코드는 리소스 그룹을 만드는 방법을 보여 줍니다.

``` csharp
var resourceGroup = new ResourceGroup { Location = location };
resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, rg);
```

자세한 내용은 Azure 리소스 그룹 및 Data Lake Analytics를 참조하세요.

### <a name="create-a-data-lake-store-account"></a>Data Lake 저장소 계정 만들기

항상 ADLA 계정에는 ADLS 계정이 필요합니다. 아직 사용할 ADLS 계정이 없는 경우 다음 코드를 사용하여 만들 수 있습니다.

``` csharp
var new_adls_params = new DataLakeStoreAccount(location: _location);
adlsAccountClient.Account.Create(rg, adls, new_adls_params);
```

### <a name="create-a-data-lake-analytics-account"></a>Data Lake 분석 계정 만들기

다음 코드는 ADLS 계정을 만듭니다.

``` csharp
var new_adla_params = new DataLakeAnalyticsAccount()
{
   DefaultDataLakeStoreAccount = adls,
   Location = location
};

adlaClient.Account.Create(rg, adla, new_adla_params);
```

### <a name="list-data-lake-store-accounts"></a>Data Lake Store 계정 나열

``` csharp
var adlsAccounts = adlsAccountClient.Account.List().ToList();
foreach (var adls in adlsAccounts)
{
   Console.WriteLine($"ADLS: {0}", adls.Name);
}
```

### <a name="list-data-lake-analytics-accounts"></a>데이터 레이크 분석 계정을 나열합니다.

``` csharp
var adlaAccounts = adlaClient.Account.List().ToList();

for (var adla in AdlaAccounts)
{
   Console.WriteLine($"ADLA: {0}, adla.Name");
}
```

### <a name="checking-if-an-account-exists"></a>계정이 있는지 확인

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
```

### <a name="get-information-about-an-account"></a>계정에 대한 정보 가져오기

``` csharp
bool exists = adlaClient.Account.Exists(rg, adla));
if (exists)
{
   var adla_accnt = adlaClient.Account.Get(rg, adla);
}
```

### <a name="delete-an-account"></a>계정 삭제

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
   adlaClient.Account.Delete(rg, adla);
}
```

### <a name="get-the-default-data-lake-store-account"></a>기본 Data Lake Store 계정 가져오기

모든 Data Lake Analytics 계정에는 기본 Data Lake Store 계정이 필요합니다. 다음 코드를 사용하여 Analytics 계정의 기본 Store 계정을 결정합니다.

``` csharp
if (adlaClient.Account.Exists(rg, adla))
{
  var adla_accnt = adlaClient.Account.Get(rg, adla);
  string def_adls_account = adla_accnt.DefaultDataLakeStoreAccount;
}
```

## <a name="manage-data-sources"></a>데이터 원본 관리

데이터 레이크 분석은 현재 다음 데이터 원본을 지원합니다.

* [Azure Data Lake Storage](../data-lake-store/data-lake-store-overview.md)
* [Azure Storage 계정](../storage/common/storage-introduction.md)

### <a name="link-to-an-azure-storage-account"></a>Azure Storage 계정에 대한 링크

Azure Storage 계정에 대한 링크를 만들 수 있습니다.

``` csharp
string storage_key = "xxxxxxxxxxxxxxxxxxxx";
string storage_account = "mystorageaccount";
var addParams = new AddStorageAccountParameters(storage_key);            
adlaClient.StorageAccounts.Add(rg, adla, storage_account, addParams);
```

### <a name="list-azure-storage-data-sources"></a>Azure Storage 데이터 원본 나열

``` csharp
var stg_accounts = adlaAccountClient.StorageAccounts.ListByAccount(rg, adla);

if (stg_accounts != null)
{
  foreach (var stg_account in stg_accounts)
  {
      Console.WriteLine($"Storage account: {0}", stg_account.Name);
  }
}
```

### <a name="list-data-lake-store-data-sources"></a>Data Lake Store 데이터 원본 나열

``` csharp
var adls_accounts = adlsClient.Account.List();

if (adls_accounts != null)
{
  foreach (var adls_accnt in adls_accounts)
  {
      Console.WriteLine($"ADLS account: {0}", adls_accnt.Name);
  }
}
```

### <a name="upload-and-download-folders-and-files"></a>폴더 및 파일 업로드 및 다운로드

Data Lake Store 파일 시스템 클라이언트 관리 개체를 사용하여 다음 메서드를 통해 Azure에서 로컬 컴퓨터로 개별 파일 또는 폴더를 업로드하고 다운로드할 수 있습니다.

- UploadFolder
- UploadFile
- DownloadFolder
- DownloadFile

이러한 메서드의 첫 번째 매개 변수는 원본 경로 및 대상 경로 매개 변수가 뒤에 나오는 Data Lake Store 계정 이름입니다.

다음 예제는 Data Lake Store에서 폴더를 다운로드하는 방법을 보여 줍니다.

``` csharp
adlsFileSystemClient.FileSystem.DownloadFolder(adls, sourcePath, destinationPath);
```

### <a name="create-a-file-in-a-data-lake-store-account"></a>Data Lake Store 계정에 파일 만들기

``` csharp
using (var memstream = new MemoryStream())
{
   using (var sw = new StreamWriter(memstream, UTF8Encoding.UTF8))
   {
      sw.WriteLine("Hello World");
      sw.Flush();
      
      memstream.Position = 0;

      adlsFileSystemClient.FileSystem.Create(adls, "/Samples/Output/randombytes.csv", memstream);
   }
}
```

### <a name="verify-azure-storage-account-paths"></a>Azure Storage 계정 경로 확인

다음 코드에서는 Azure Storage 계정(storageAccntName)이 Data Lake Analytics 계정(analyticsAccountName)에 있는지 및 컨테이너(containerName)가 Azure Storage 계정에 있는지를 확인합니다.

``` csharp
string storage_account = "mystorageaccount";
string storage_container = "mycontainer";
bool accountExists = adlaClient.Account.StorageAccountExists(rg, adla, storage_account));
bool containerExists = adlaClient.Account.StorageContainerExists(rg, adla, storage_account, storage_container));
```

## <a name="manage-catalog-and-jobs"></a>카탈로그 및 작업 관리

DataLakeAnalyticsCatalogManagementClient 개체는 각 Azure Data Lake Analytics 계정에 제공된 SQL 데이터베이스를 관리하는 메서드를 제공합니다. DataLakeAnalyticsJobManagementClient는 U-SQL 스크립트를 사용하여 데이터베이스에서 실행되는 작업을 제출하고 관리하는 메서드를 제공합니다.

### <a name="list-databases-and-schemas"></a>데이터베이스 및 스키마 나열

나열할 수 있는 항목들 중에서 가장 일반적인 항목이 데이터베이스와 해당 스키마입니다. 다음 코드에서는 데이터베이스 컬렉션을 가져온 다음 각 데이터베이스에 대한 스키마를 열거합니다.

``` csharp
var databases = adlaCatalogClient.Catalog.ListDatabases(adla);
foreach (var db in databases)
{
  Console.WriteLine($"Database: {db.Name}");
  Console.WriteLine(" - Schemas:");
  var schemas = adlaCatalogClient.Catalog.ListSchemas(adla, db.Name);
  foreach (var schm in schemas)
  {
      Console.WriteLine($"\t{schm.Name}");
  }
}
```

### <a name="list-table-columns"></a>테이블 열 나열

다음 코드에서는 Data Lake Analytics 카탈로그 관리 클라이언트를 통해 데이터베이스에 액세스하여 지정한 테이블의 열을 나열하는 방법을 보여 줍니다.

```csharp
var tbl = adlaCatalogClient.Catalog.GetTable(adla, "master", "dbo", "MyTableName");
IEnumerable<USqlTableColumn> columns = tbl.ColumnList;

foreach (USqlTableColumn utc in columns)
{
  Console.WriteLine($"\t{utc.Name}");
}
```

### <a name="submit-a-u-sql-job"></a>U-SQL 작업 제출

다음 코드에서는 Data Lake Analytics 작업 관리 클라이언트를 사용하여 작업을 제출하는 방법을 보여줍니다.

``` csharp
string scriptPath = "/Samples/Scripts/SearchResults_Wikipedia_Script.txt";
Stream scriptStrm = adlsFileSystemClient.FileSystem.Open(_adlsAccountName, scriptPath);
string scriptTxt = string.Empty;
using (StreamReader sr = new StreamReader(scriptStrm))
{
    scriptTxt = sr.ReadToEnd();
}

var jobName = "SR_Wikipedia";
var jobId = Guid.NewGuid();
var properties = new USqlJobProperties(scriptTxt);
var parameters = new JobInformation(jobName, JobType.USql, properties, priority: 1, degreeOfParallelism: 1, jobId: jobId);
var jobInfo = adlaJobClient.Job.Create(adla, jobId, parameters);
Console.WriteLine($"Job {jobName} submitted.");
```

### <a name="list-failed-jobs"></a>실패한 작업 나열

다음 코드에서는 실패한 작업에 대한 정보를 나열합니다.

```csharp
var odq = new ODataQuery<JobInformation> { Filter = "result eq 'Failed'" };
var jobs = adlaJobClient.Job.List(adla, odq);
foreach (var j in jobs)
{
   Console.WriteLine($"{j.Name}\t{j.JobId}\t{j.Type}\t{j.StartTime}\t{j.EndTime}");
}
```

### <a name="list-pipelines"></a>파이프라인 나열

다음 코드는 계정에 제출된 작업의 각 파이프라인에 대한 정보를 나열합니다.

``` csharp
var pipelines = adlaJobClient.Pipeline.List(adla);
foreach (var p in pipelines)
{
   Console.WriteLine($"Pipeline: {p.Name}\t{p.PipelineId}\t{p.LastSubmitTime}");
}
```

### <a name="list-recurrences"></a>되풀이 나열

다음 코드는 계정에 제출된 작업의 각 되풀이에 대한 정보를 나열합니다.

``` csharp
var recurrences = adlaJobClient.Recurrence.List(adla);
foreach (var r in recurrences)
{
   Console.WriteLine($"Recurrence: {r.Name}\t{r.RecurrenceId}\t{r.LastSubmitTime}");
}
```

## <a name="common-graph-scenarios"></a>일반적인 그래프 시나리오

### <a name="look-up-user-in-the-aad-directory"></a>AAD 디렉터리에서 사용자 조회

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
```

### <a name="get-the-objectid-of-a-user-in-the-aad-directory"></a>AAD 디렉터리에서 사용자의 ObjectId 가져오기

``` csharp
var userinfo = graphClient.Users.Get( "bill@contoso.com" );
Console.WriteLine( userinfo.ObjectId )
```

## <a name="manage-compute-policies"></a>계산 정책 관리

DataLakeAnalyticsAccountManagementClient 개체는 Data Lake Analytics 계정에 대한 계산 정책을 관리하는 메서드를 제공합니다.

### <a name="list-compute-policies"></a>계산 정책 나열

다음 코드는 Data Lake Analytics 계정에 대한 계산 정책 목록을 검색합니다.

``` csharp
var policies = adlaAccountClient.ComputePolicies.ListByAccount(rg, adla);
foreach (var p in policies)
{
   Console.WriteLine($"Name: {p.Name}\tType: {p.ObjectType}\tMax AUs / job: {p.MaxDegreeOfParallelismPerJob}\tMin priority / job: {p.MinPriorityPerJob}");
}
```

### <a name="create-a-new-compute-policy"></a>새 계산 정책 만들기

다음 코드는 지정된 사용자가 사용할 수 있는 최대 AU를 50으로, 최소 작업 우선 순위를 250으로 설정하는, Data Lake Analytics 계정에 대한 새 계산 정책을 만듭니다.

``` csharp
var userAadObjectId = "3b097601-4912-4d41-b9d2-78672fc2acde";
var newPolicyParams = new ComputePolicyCreateOrUpdateParameters(userAadObjectId, "User", 50, 250);
adlaAccountClient.ComputePolicies.CreateOrUpdate(rg, adla, "GaryMcDaniel", newPolicyParams);
```

## <a name="next-steps"></a>다음 단계

* [Microsoft Azure 데이터 레이크 분석 개요](data-lake-analytics-overview.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 관리](data-lake-analytics-manage-use-portal.md)
* [Azure 포털을 사용하여 Azure Data Lake Analytics 작업 모니터링 및 문제 해결](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)