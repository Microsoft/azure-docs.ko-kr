---
title: Azure Stream Analytics 작업을 프로그래밍 방식으로 모니터링 및 관리
description: 이 문서에서는 REST API, Azure SDK 또는 PowerShell을 통해 생성된 Stream Analytics 작업을 프로그래밍 방식으로 모니터링하는 방법에 대해 설명합니다.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 04/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 196d722e57abe743fc51692dde231e8f93682c46
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002269"
---
# <a name="programmatically-create-a-stream-analytics-job-monitor"></a>프로그래밍 방식으로 Stream Analytics 작업 모니터 만들기

이 문서에서는 Stream Analytics 작업에 대한 모니터링을 사용하는 방법을 보여줍니다. REST API, Azure SDK 또는 PowerShell을 통해 생성된 Stream Analytics 작업은 기본적으로 모니터링이 설정되어 있지 않습니다. 작업의 모니터 페이지로 이동하고 사용 버튼을 클릭하여 Azure Portal에서 수동으로 설정하거나 이 문서의 단계를 수행하여 이 프로세스를 자동화할 수 있습니다. 모니터링 데이터는 Stream Analytics 작업에 대해 Azure Portal의 “모니터” 탭에서 볼 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 프로세스를 시작하기 전에 다음 필수 조건이 있어야 합니다.

* Visual Studio 2019 또는 2015
* [Azure.NET SDK](https://azure.microsoft.com/downloads/) 다운로드 및 설치
* 모니터링 설정이 필요한 기존 Stream Analytics 작업

## <a name="create-a-project"></a>프로젝트 만들기

1. Visual Studio C# .NET 콘솔 애플리케이션을 만듭니다.
2. 패키지 관리자 콘솔에서 NuGet 패키지를 설치하려면 다음 명령을 실행합니다. 첫 번째는 Azure Stream Analytics 관리.NET SDK입니다. 두 번째는 모니터링을 사용하도록 설정하는 데 사용되는 Azure Monitor SDK입니다. 마지막은 인증에 사용되는 Azure Active Directory 클라이언트입니다.
   
   ```powershell
   Install-Package Microsoft.Azure.Management.StreamAnalytics
   Install-Package Microsoft.Azure.Insights -Pre
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   ```
3. 다음 appSettings 섹션을 App.config 파일에 추가합니다.
   
   ```csharp
   <appSettings>
     <!--CSM Prod related values-->
     <add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
     <add key="JobName" value="YOUR JOB NAME" />
     <add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
     <add key="ActiveDirectoryEndpoint" value="https://login.microsoftonline.com/" />
     <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
     <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
     <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
     <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
     <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
     <add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
   </appSettings>
   ```
   *SubscriptionId*와 *ActiveDirectoryTenantId*의 값을 Azure 구독 및 테넌트 ID로 바꿉니다. 다음 PowerShell cmdlet을 실행하여 이러한 값을 얻을 수 있습니다.
   
   ```powershell
   Get-AzureAccount
   ```
4. 프로젝트의 원본 파일(Program.cs)에 다음 using 문을 추가합니다.
   
   ```csharp
     using System;
     using System.Configuration;
     using System.Threading;
     using Microsoft.Azure;
     using Microsoft.Azure.Management.Insights;
     using Microsoft.Azure.Management.Insights.Models;
     using Microsoft.Azure.Management.StreamAnalytics;
     using Microsoft.Azure.Management.StreamAnalytics.Models;
     using Microsoft.IdentityModel.Clients.ActiveDirectory;
   ```
5. 인증 도우미 메서드를 추가합니다.

   ```csharp   
   public static string GetAuthorizationHeader()
   {
      AuthenticationResult result = null;
      var thread = new Thread(() =>
      {
         try
         {
             var context = new AuthenticationContext(
                ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
             result = context.AcquireToken(
                 resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                 clientId: ConfigurationManager.AppSettings["AsaClientId"],
                 redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                 promptBehavior: PromptBehavior.Always);
         }
         catch (Exception threadEx)
         {
             Console.WriteLine(threadEx.Message);
         }
     });

     thread.SetApartmentState(ApartmentState.STA);
     thread.Name = "AcquireTokenThread";
     thread.Start();
     thread.Join();
   
     if (result != null)
     {
         return result.AccessToken;
     }
         throw new InvalidOperationException("Failed to acquire token");
   }
   ```

## <a name="create-management-clients"></a>관리 클라이언트 만들기

다음 코드는 필수 변수 및 관리 클라이언트를 설정합니다.

   ```csharp
    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);
   ```

## <a name="enable-monitoring-for-an-existing-stream-analytics-job"></a>기존 Stream Analytics 작업에 모니터링 사용

다음 코드는 **기존** Stream Analytics 작업에 모니터링을 사용합니다. 코드의 첫 번째 부분은 Stream Analytics 서비스에 대해 GET 요청을 수행하여 특정 Stream Analytics 작업에 대한 정보를 검색합니다. Stream Analytics 작업에 모니터링을 사용하기 위해 Insights 서비스에 PUT 요청을 보내는 코드의 나머지 부분에서 PUT 메서드에 대한 매개 변수로 *ID* 속성(GET 요청에서 검색됨)을 사용합니다.

> [!WARNING]
> Azure Portal을 통해 또는 아래 코드를 통해 프로그래밍 방식으로 서로 다른 Stream Analytics 작업에 대한 모니터링을 이전에 설정한 경우, **이전에 모니터링을 활성화했을 때 사용했던 동일한 스토리지 계정 이름을 제공하는 것이 좋습니다.**
> 
> 스토리지 계정은 작업 자체에 특정되지 않고 Stream Analytics 작업에서 만든 지역에 연결됩니다.
> 
> 동일 지역의 모든 Stream Analytics 작업(및 다른 모든 Azure 리소스)은 이 스토리지 계정을 공유하여 모니터링 데이터를 저장합니다. 사용자가 다른 스토리지 계정을 제공하면, 다른 Stream Analytics 작업 또는 다른 Azure 리소스에 대한 모니터링에 의도하지 않은 부작용이 발생할 수 있습니다.
> 
> 다음 코드의 `<YOUR STORAGE ACCOUNT NAME>`을 바꾸는 데 사용한 스토리지 계정 이름은 모니터링을 사용할 Stream Analytics 작업과 동일한 구독에 있는 스토리지 계정이어야 합니다.
> 
> 
>    ```csharp
>    // Get an existing Stream Analytics job
>     JobGetParameters jobGetParameters = new JobGetParameters()
>     {
>         PropertiesToExpand = "inputs,transformation,outputs"
>     };
>     JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);
>
>    // Enable monitoring
>    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
>    {
>            Properties = new ServiceDiagnosticSettings()
>           {
>               StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
>           }
>    };
>   insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);
>   ```


## <a name="get-support"></a>지원 받기

추가 지원이 필요한 경우 [Azure Stream Analytics용 Microsoft Q&A 질문 페이지](https://docs.microsoft.com/answers/topics/azure-stream-analytics.html)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics 소개](stream-analytics-introduction.md)
* [Azure Stream Analytics 사용 시작](stream-analytics-real-time-fraud-detection.md)
* [Azure  Stream Analytics 작업 규모 지정](stream-analytics-scale-jobs.md)
* [Azure  Stream Analytics 쿼리 언어 참조](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Azure Stream Analytics 관리 REST API 참조](https://msdn.microsoft.com/library/azure/dn835031.aspx)
