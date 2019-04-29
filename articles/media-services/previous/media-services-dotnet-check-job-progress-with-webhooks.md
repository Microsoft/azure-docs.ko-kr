---
title: Azure Webhooks를 사용하여 .NET으로 Media Services 작업 알림 모니터링 | Microsoft Docs
description: Azure Webhooks를 사용하여 Media Services 작업 알림을 모니터링하는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: a61fe157-81b1-45c1-89f2-224b7ef55869
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 36ef27dfb4a5d77ec2e595013a82f55cdf240c0b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465697"
---
# <a name="use-azure-webhooks-to-monitor-media-services-job-notifications-with-net"></a>Azure Webhooks를 사용하여 .NET으로 Media Services 작업 알림 모니터링 

작업을 실행할 때 작업 진행 상태를 추적하는 방법이 종종 필요합니다. Azure Webhooks 또는 [Azure Queue Storage](media-services-dotnet-check-job-progress-with-queues.md)를 사용하여 Media Services 작업 알림을 모니터링할 수 있습니다 이 문서에서는 웹후크를 사용하는 방법을 보여 줍니다.

이 문서는 방법을 안내합니다.

*  Webhook에 응답하도록 사용자 지정된 Azure Function을 정의합니다. 
    
    이 경우에 인코딩 작업이 상태를 변경하면 Webhook이 Media Services에서 트리거됩니다. 함수는 Media Services 알림의 Webhook 호출을 수신 대기하고 작업이 완료되면 출력 자산을 게시합니다. 
    
    >[!NOTE]
    >계속 진행하기 전에 [Azure Functions HTTP 및 Webhook 바인딩](../../azure-functions/functions-bindings-http-webhook.md) 작동 방법에 대해 이해해야 합니다.
    >
    
* 인코딩 태스크에 Webhook을 추가하고 Webhook URL 및 이 Webhook이 응답하는 암호 키를 지정합니다. 이 문서의 끝에서 인코딩 작업에 웹후크를 추가하는 예제를 찾을 수 있습니다.  

[여기](https://github.com/Azure-Samples/media-services-dotnet-functions-integration)에서는 이 문서에 나와 있는 항목을 포함하여 다양한 Media Services .NET Azure Functions의 정의를 찾을 수 있습니다.

## <a name="prerequisites"></a>필수 조건

자습서를 완료하는 데 필요한 조건은 다음과 같습니다.

* Azure 계정. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
* Media Services 계정. Media Services 계정을 만들려면 [Media Services 계정을 만드는 방법](media-services-portal-create-account.md)을 참조하세요.
* [Azure Functions를 사용하는 방법](../../azure-functions/functions-overview.md)을 이해합니다. 또한 [Azure Functions HTTP 및 웹후크 바인딩](../../azure-functions/functions-bindings-http-webhook.md)을 검토합니다.

## <a name="create-a-function-app"></a>함수 앱 만들기

1. [Azure Portal](https://portal.azure.com) 로 이동하여 Azure 계정으로 로그인합니다.
2. [여기](../../azure-functions/functions-create-function-app-portal.md)에 설명한 대로 함수 앱을 만듭니다.

## <a name="configure-function-app-settings"></a>함수 앱 구성 설정

Media Services 함수를 개발하는 경우 함수 전체에서 사용할 환경 변수를 쉽게 추가할 수 있습니다. 앱 설정을 구성하려면 앱 설정 구성 링크를 클릭합니다. 

[애플리케이션 설정](media-services-dotnet-how-to-use-azure-functions.md#configure-function-app-settings) 섹션은 이 문서에 정의된 웹후크에서 사용되는 매개 변수를 정의합니다. 또한 다음 매개 변수를 앱 설정에 추가합니다. 

|이름|정의|예| 
|---|---|---|
|SigningKey |서명 키입니다.| j0txf1f8msjytzvpe40nxbpxdcxtqcgxy0nt|
|WebHookEndpoint | 웹후크 엔드포인트 주소입니다. 웹후크 함수를 만든 후에 **함수 URL 가져오기** 링크에서 URL을 복사할 수 있습니다. | https:\//juliakofuncapp.azurewebsites.net/api/Notification_Webhook_Function?code=iN2phdrTnCxmvaKExFWOTulfnm4C71mMLIy8tzLr7Zvf6Z22HHIK5g==.|

## <a name="create-a-function"></a>함수 만들기

함수 앱을 배포하면 **App Services** Azure Functions에서 찾을 수 있습니다.

1. 함수 앱을 선택하고 **새 함수**를 클릭합니다.
2. **C#** 코드 및 **API & Webhooks** 시나리오를 선택합니다. 
3. **제네릭 웹후크 - C#** 을 선택합니다.
4. 웹후크에 이름을 지정하고 **만들기**를 누릅니다.

### <a name="files"></a>파일

Azure Function은 이 섹션에 설명된 코드 파일 및 기타 파일과 연결됩니다. 기본적으로 함수는 **function.json** 및 **run.csx**(C#) 파일과 연결됩니다. **project.json** 파일을 추가해야 합니다. 이 섹션의 나머지 부분에서는 이러한 파일의 정의를 보여 줍니다.

![업로드](./media/media-services-azure-functions/media-services-azure-functions003.png)

#### <a name="functionjson"></a>function.json

function.json 파일은 함수 바인딩 및 기타 구성 설정을 정의합니다. 런타임은 이 파일을 사용하여 모니터링할 이벤트와 함수 실행에서 데이터를 전달하고 반환하는 방법을 결정합니다. 

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "genericJson",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

#### <a name="projectjson"></a>project.json

project.json 파일은 종속성을 포함합니다. 

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "windowsazure.mediaservices": "4.0.0.4",
        "windowsazure.mediaservices.extensions": "4.0.0.4",
        "Microsoft.IdentityModel.Clients.ActiveDirectory": "3.13.1",
        "Microsoft.IdentityModel.Protocol.Extensions": "1.0.2.206221351"
      }
    }
   }
}
```
    
#### <a name="runcsx"></a>run.csx

이 섹션의 코드는 웹후크인 Azure Function의 구현을 보여 줍니다. 이 샘플에서 함수는 Media Services 알림의 Webhook 호출을 수신 대기하고 작업이 완료되면 출력 자산을 게시합니다.

Webhook은 알림 엔드포인트를 구성하는 경우에 전달되는 것과 일치하는 서명 키(자격 증명)이 필요합니다. 서명 키는 Azure Media Services에서 Webhook 콜백을 보호하고 보안하는 데 사용되는 64바이트 Base64 인코딩 값입니다. 

다음에 나오는 웹후크 정의 코드에서 **VerifyWebHookRequestSignature** 메서드는 알림 메시지에 대한 검증을 수행합니다. 이 유효성 검사는 메시지가 Azure Media Services에서 전송되었는지, 손상되지 않았는지를 확인하기 위해 수행됩니다. TLS(전송 계층 보안)에서 쿼리 매개 변수로 **코드** 값을 보유하는 것처럼 서명도 Azure Functions에 대해 선택적입니다. 

>[!NOTE]
>다른 AMS 정책(예: 로케이터 정책 또는 ContentKeyAuthorizationPolicy의 경우)은 1,000,000개의 정책으로 제한됩니다. 항상 같은 날짜/액세스 권한을 사용하는 경우(예: 비 업로드 정책처럼 오랫동안 배치되는 로케이터에 대한 정책) 동일한 정책 ID를 사용해야 합니다. 자세한 내용은 [이 항목](media-services-dotnet-manage-entities.md#limit-access-policies)을 참조하세요.

```csharp
///////////////////////////////////////////////////
#r "Newtonsoft.Json"

using System;
using Microsoft.WindowsAzure.MediaServices.Client;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using Newtonsoft.Json;
using Microsoft.Azure;
using System.Net;
using System.Security.Cryptography;
using Microsoft.Azure.WebJobs;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

internal const string SignatureHeaderKey = "sha256";
internal const string SignatureHeaderValueTemplate = SignatureHeaderKey + "={0}";
static string _webHookEndpoint = Environment.GetEnvironmentVariable("WebHookEndpoint");
static string _signingKey = Environment.GetEnvironmentVariable("SigningKey");

static readonly string _AADTenantDomain = Environment.GetEnvironmentVariable("AMSAADTenantDomain");
static readonly string _RESTAPIEndpoint = Environment.GetEnvironmentVariable("AMSRESTAPIEndpoint");

static readonly string _AMSClientId = Environment.GetEnvironmentVariable("AMSClientId");
static readonly string _AMSClientSecret = Environment.GetEnvironmentVariable("AMSClientSecret");

static CloudMediaContext _context = null;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
    byte[] requestBody = await taskForRequestBody;

    string jsonContent = await req.Content.ReadAsStringAsync();
    log.Info($"Request Body = {jsonContent}");

    IEnumerable<string> values = null;
    if (req.Headers.TryGetValues("ms-signature", out values))
    {
        byte[] signingKey = Convert.FromBase64String(_signingKey);
        string signatureFromHeader = values.FirstOrDefault();

        if (VerifyWebHookRequestSignature(requestBody, signatureFromHeader, signingKey))
        {
            string requestMessageContents = Encoding.UTF8.GetString(requestBody);

            NotificationMessage msg = JsonConvert.DeserializeObject<NotificationMessage>(requestMessageContents);

            if (VerifyHeaders(req, msg, log))
            { 
                string newJobStateStr = (string)msg.Properties.Where(j => j.Key == "NewState").FirstOrDefault().Value;
                if (newJobStateStr == "Finished")
                {
                    AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AADTenantDomain,
                                new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                                AzureEnvironments.AzureCloudEnvironment);

                    AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

                    if(_context!=null)   
                    {                        
                        string urlForClientStreaming = PublishAndBuildStreamingURLs(msg.Properties["JobId"]);
                        log.Info($"URL to the manifest for client streaming using HLS protocol: {urlForClientStreaming}");
                    }
                }

                return req.CreateResponse(HttpStatusCode.OK, string.Empty);
            }
            else
            {
                log.Info($"VerifyHeaders failed.");
                return req.CreateResponse(HttpStatusCode.BadRequest, "VerifyHeaders failed.");
            }
        }
        else
        {
            log.Info($"VerifyWebHookRequestSignature failed.");
            return req.CreateResponse(HttpStatusCode.BadRequest, "VerifyWebHookRequestSignature failed.");
        }
    }

    return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
}

private static string PublishAndBuildStreamingURLs(String jobID)
{
    IJob job = _context.Jobs.Where(j => j.Id == jobID).FirstOrDefault();
    IAsset asset = job.OutputMediaAssets.FirstOrDefault();

    // Create a 30-day readonly access policy. 
    // You cannot create a streaming locator using an AccessPolicy that includes write or delete permissions.
    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
    TimeSpan.FromDays(30),
    AccessPermissions.Read);

    // Create a locator to the streaming content on an origin. 
    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
    policy,
    DateTime.UtcNow.AddMinutes(-5));

    // Get a reference to the streaming manifest file from the  
    // collection of files in the asset. 
    var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
                EndsWith(".ism")).
                FirstOrDefault();

    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest" +  "(format=m3u8-aapl)";
    return urlForClientStreaming;

}

private static bool VerifyWebHookRequestSignature(byte[] data, string actualValue, byte[] verificationKey)
{
    using (var hasher = new HMACSHA256(verificationKey))
    {
        byte[] sha256 = hasher.ComputeHash(data);
        string expectedValue = string.Format(CultureInfo.InvariantCulture, SignatureHeaderValueTemplate, ToHex(sha256));

        return (0 == String.Compare(actualValue, expectedValue, System.StringComparison.Ordinal));
    }
}

private static bool VerifyHeaders(HttpRequestMessage req, NotificationMessage msg, TraceWriter log)
{
    bool headersVerified = false;

    try
    {
        IEnumerable<string> values = null;
        if (req.Headers.TryGetValues("ms-mediaservices-accountid", out values))
        {
            string accountIdHeader = values.FirstOrDefault();
            string accountIdFromMessage = msg.Properties["AccountId"];

            if (0 == string.Compare(accountIdHeader, accountIdFromMessage, StringComparison.OrdinalIgnoreCase))
            {
                headersVerified = true;
            }
            else
            {
                log.Info($"accountIdHeader={accountIdHeader} does not match accountIdFromMessage={accountIdFromMessage}");
            }
        }
        else
        {
            log.Info($"Header ms-mediaservices-accountid not found.");
        }
    }
    catch (Exception e)
    {
        log.Info($"VerifyHeaders hit exception {e}");
        headersVerified = false;
    }

    return headersVerified;
}

private static readonly char[] HexLookup = new char[] { '0', '1', '2', '3', '4', '5', '6', '7', '8', '9', 'A', 'B', 'C', 'D', 'E', 'F' };

/// <summary>
/// Converts a <see cref="T:byte[]"/> to a hex-encoded string.
/// </summary>
private static string ToHex(byte[] data)
{
    if (data == null)
    {
        return string.Empty;
    }

    char[] content = new char[data.Length * 2];
    int output = 0;
    byte d;

    for (int input = 0; input < data.Length; input++)
    {
        d = data[input];
        content[output++] = HexLookup[d / 0x10];
        content[output++] = HexLookup[d % 0x10];
    }

    return new string(content);
}

internal enum NotificationEventType
{
    None = 0,
    JobStateChange = 1,
    NotificationEndPointRegistration = 2,
    NotificationEndPointUnregistration = 3,
    TaskStateChange = 4,
    TaskProgress = 5
}

internal sealed class NotificationMessage
{
    public string MessageVersion { get; set; }
    public string ETag { get; set; }
    public NotificationEventType EventType { get; set; }
    public DateTime TimeStamp { get; set; }
    public IDictionary<string, string> Properties { get; set; }
}
```

저장하고 함수를 실행합니다.

### <a name="function-output"></a>함수 출력

웹후크가 트리거되면 위의 예제는 다음과 같이 출력되고 사용자의 값이 달라집니다.

    C# HTTP trigger function processed a request. RequestUri=https://juliako001-functions.azurewebsites.net/api/Notification_Webhook_Function?code=9376d69kygoy49oft81nel8frty5cme8hb9xsjslxjhalwhfrqd79awz8ic4ieku74dvkdfgvi
    Request Body = 
    {
      "MessageVersion": "1.1",
      "ETag": "b8977308f48858a8f224708bc963e1a09ff917ce730316b4e7ae9137f78f3b20",
      "EventType": 4,
      "TimeStamp": "2017-02-16T03:59:53.3041122Z",
      "Properties": {
        "JobId": "nb:jid:UUID:badd996c-8d7c-4ae0-9bc1-bd7f1902dbdd",
        "TaskId": "nb:tid:UUID:80e26fb9-ee04-4739-abd8-2555dc24639f",
        "NewState": "Finished",
        "OldState": "Processing",
        "AccountName": "mediapkeewmg5c3peq",
        "AccountId": "301912b0-659e-47e0-9bc4-6973f2be3424",
        "NotificationEndPointId": "nb:nepid:UUID:cb5d707b-4db8-45fe-a558-19f8d3306093"
      }
    }
    
    URL to the manifest for client streaming using HLS protocol: http://mediapkeewmg5c3peq.streaming.mediaservices.windows.net/0ac98077-2b58-4db7-a8da-789a13ac6167/BigBuckBunny.ism/manifest(format=m3u8-aapl)

## <a name="add-a-webhook-to-your-encoding-task"></a>인코딩 작업에 웹후크 추가

이 섹션에서는 태스크에 Webhook 알림을 추가하는 코드가 표시됩니다. 작업 수준 알림을 추가할 수도 있습니다. 그러면 연결된 태스크를 사용하여 작업에 더 유용합니다.  

1. Visual Studio를 사용하여 새 C# 콘솔 애플리케이션을 만듭니다. 이름, 위치 및 솔루션 이름을 입력하고 확인을 클릭합니다.
2. [NuGet](https://www.nuget.org/packages/windowsazure.mediaservices)을 사용하여 Azure Media Services를 설치합니다.
3. App.config 파일을 적절한 값으로 업데이트합니다. 
    
   * Azure Media Services 연결 정보 
   * 알림을 가져오려는 Webhook URL 
   * Webhook이 필요로 하는 키와 일치하는 서명 키 서명 키는 Azure Media Services에서 웹후크 콜백을 보호하고 보안하는 데 사용되는 64바이트 Base64 인코딩 값입니다. 

     ```xml
           <appSettings>
               <add key="AMSAADTenantDomain" value="domain" />
               <add key="AMSRESTAPIEndpoint" value="endpoint" />

               <add key="AMSClientId" value="clinet id" />
               <add key="AMSClientSecret" value="client secret" />

               <add key="WebhookURL" value="https://yourapp.azurewebsites.net/api/functionname?code=ApiKey" />
               <add key="WebhookSigningKey" value="j0txf1f8msjytzvpe40nxbpxdcxtqcgxy0nt" />
           </appSettings>
     ```

4. Program.cs 파일을 다음 코드로 업데이트합니다.

    ```csharp
            using System;
            using System.Configuration;
            using System.Linq;
            using Microsoft.WindowsAzure.MediaServices.Client;

            namespace NotificationWebHook
            {
                class Program
                {
                // Read values from the App.config file.
                private static readonly string _AMSAADTenantDomain =
                    ConfigurationManager.AppSettings["AMSAADTenantDomain"];
                private static readonly string _AMSRESTAPIEndpoint =
                    ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];

                private static readonly string _AMSClientId =
                    ConfigurationManager.AppSettings["AMSClientId"];
                private static readonly string _AMSClientSecret =
                    ConfigurationManager.AppSettings["AMSClientSecret"];

                private static readonly string _webHookEndpoint =
                    ConfigurationManager.AppSettings["WebhookURL"];
                private static readonly string _signingKey =
                    ConfigurationManager.AppSettings["WebhookSigningKey"];

                // Field for service context.
                private static CloudMediaContext _context = null;

                static void Main(string[] args)
                {
                    AzureAdTokenCredentials tokenCredentials = new AzureAdTokenCredentials(_AMSAADTenantDomain,
                        new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                        AzureEnvironments.AzureCloudEnvironment);

                    AzureAdTokenProvider tokenProvider = new AzureAdTokenProvider(tokenCredentials);

                    _context = new CloudMediaContext(new Uri(_AMSRESTAPIEndpoint), tokenProvider);

                    byte[] keyBytes = Convert.FromBase64String(_signingKey);

                    IAsset newAsset = _context.Assets.FirstOrDefault();

                    // Check for existing Notification Endpoint with the name "FunctionWebHook"

                    var existingEndpoint = _context.NotificationEndPoints.Where(e => e.Name == "FunctionWebHook").FirstOrDefault();
                    INotificationEndPoint endpoint = null;

                    if (existingEndpoint != null)
                    {
                    Console.WriteLine("webhook endpoint already exists");
                    endpoint = (INotificationEndPoint)existingEndpoint;
                    }
                    else
                    {
                    endpoint = _context.NotificationEndPoints.Create("FunctionWebHook",
                        NotificationEndPointType.WebHook, _webHookEndpoint, keyBytes);
                    Console.WriteLine("Notification Endpoint Created with Key : {0}", keyBytes.ToString());
                    }

                    // Declare a new encoding job with the Standard encoder
                    IJob job = _context.Jobs.Create("MES Job");

                    // Get a media processor reference, and pass to it the name of the 
                    // processor to use for the specific task.
                    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

                    ITask task = job.Tasks.AddNew("My encoding task",
                    processor,
                    "Adaptive Streaming",
                    TaskOptions.None);

                    // Specify the input asset to be encoded.
                    task.InputAssets.Add(newAsset);

                    // Add an output asset to contain the results of the job. 
                    // This output is specified as AssetCreationOptions.None, which 
                    // means the output asset is not encrypted. 
                    task.OutputAssets.AddNew(newAsset.Name, AssetCreationOptions.None);

                    // Add the WebHook notification to this Task and request all notification state changes.
                    // Note that you can also add a job level notification
                    // which would be more useful for a job with chained tasks.  
                    if (endpoint != null)
                    {
                    task.TaskNotificationSubscriptions.AddNew(NotificationJobState.All, endpoint, true);
                    Console.WriteLine("Created Notification Subscription for endpoint: {0}", _webHookEndpoint);
                    }
                    else
                    {
                    Console.WriteLine("No Notification Endpoint is being used");
                    }

                    job.Submit();

                    Console.WriteLine("Expect WebHook to be triggered for the Job ID: {0}", job.Id);
                    Console.WriteLine("Expect WebHook to be triggered for the Task ID: {0}", task.Id);

                    Console.WriteLine("Job Submitted");

                }
                private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
                {
                    var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
                    ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

                    if (processor == null)
                    throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

                    return processor;
                }
                }
            }
    ```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
