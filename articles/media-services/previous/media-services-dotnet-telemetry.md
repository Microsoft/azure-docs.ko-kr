---
title: .NET을 사용하여 Azure Media Services 원격 분석 구성 | Microsoft Docs
description: 이 문서는 .NET SDK를 사용하여 Azure Media Services 원격 분석을 사용하는 방법을 보여 줍니다.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: f8f55e37-0714-49ea-bf4a-e6c1319bec44
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 1ffaefc51121aeb7421d6e49a3c0e58c76d4391e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61464949"
---
# <a name="configuring-azure-media-services-telemetry-with-net"></a>.NET을 사용하여 Azure Media Services 원격 분석 구성 

이 문서는 .NET SDK를 사용하여 AMS(Azure Media Services) 원격 분석을 구성할 때 수행할 수 있는 일반적인 단계를 설명합니다. 

>[!NOTE]
>AMS 원격 분석이 무엇인지, 어떻게 사용하는지에 대한 자세한 설명을 보려면 [개요](media-services-telemetry-overview.md) 문서를 참조하세요.

다음 방법 중 하나를 사용하여 원격 분석 데이터를 사용할 수 있습니다.

- Azure Table Storage에서 직접 데이터를 읽습니다(예: Storage SDK 사용). 원격 분석 저장소 테이블에 대한 설명을 보려면 [이](https://msdn.microsoft.com/library/mt742089.aspx) 항목의 **원격 분석 정보 사용**을 참조하세요.

또는

- 저장소 데이터를 읽기 위한 Media Services .NET SDK의 지원을 사용합니다. 이 문서에서는 지정된 AMS 계정에 대해 원격 분석을 사용하도록 설정하고 Azure Media Services .NET SDK를 사용하여 메트릭을 쿼리하는 방법을 보여 줍니다.  

## <a name="configuring-telemetry-for-a-media-services-account"></a>Media Services 계정에 대해 원격 분석 구성

원격 분석을 사용하도록 설정하려면 다음 단계가 필요합니다.

- Media Services 계정에 연결된 저장소 계정의 자격 증명을 가져옵니다. 
- **EndPointType**이 **AzureTable**로 설정되고 endPointAddress가 저장소 테이블을 가리키는 알림 엔드포인트를 만듭니다.

```csharp
        INotificationEndPoint notificationEndPoint = 
                      _context.NotificationEndPoints.Create("monitoring", 
                      NotificationEndPointType.AzureTable,
                      "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");
```

- 모니터링할 서비스에 대한 모니터링 구성 설정을 만듭니다. 한 개 이하의 모니터링 구성 설정이 허용됩니다. 

```csharp
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });
```

## <a name="consuming-telemetry-information"></a>이

원격 분석 정보 사용에 대한 자세한 내용은 [이](media-services-telemetry-overview.md) 문서를 참조하세요.

## <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성

1. 개발 환경을 설정하고 [.NET을 사용한 Media Services 환경](media-services-dotnet-how-to-use.md)에 설명된 대로 연결 정보를 사용하여 app.config 파일을 채웁니다. 

2. 다음 요소를 app.config 파일에 정의된 **appSettings**에 추가합니다.

    ```xml
        <add key="StorageAccountName" value="storage_name" />
    ```
 
## <a name="example"></a>예  
    
다음 예제에서는 지정된 AMS 계정에 대해 원격 분석을 사용하도록 설정하고 Azure Media Services .NET SDK를 사용하여 메트릭을 쿼리하는 방법을 보여 줍니다.  

```csharp
using System;
using System.Collections.Generic;
using System.Configuration;
using System.Linq;
using Microsoft.WindowsAzure.MediaServices.Client;

namespace AMSMetrics
{
    class Program
    {
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly string _mediaServicesStorageAccountName =
            ConfigurationManager.AppSettings["StorageAccountName"];

        // Field for service context.
        private static CloudMediaContext _context = null;

        private static IStreamingEndpoint _streamingEndpoint = null;
        private static IChannel _channel = null;

        static void Main(string[] args)
        {
            AzureAdTokenCredentials tokenCredentials =
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);

            _streamingEndpoint = _context.StreamingEndpoints.FirstOrDefault();
            _channel = _context.Channels.FirstOrDefault();

            var monitoringConfigurations = _context.MonitoringConfigurations;
            IMonitoringConfiguration monitoringConfiguration = null;

            // No more than one monitoring configuration settings is allowed.
            if (monitoringConfigurations.ToArray().Length != 0)
            {
                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
            }
            else
            {
                INotificationEndPoint notificationEndPoint =
                          _context.NotificationEndPoints.Create("monitoring",
                          NotificationEndPointType.AzureTable, GetTableEndPoint());

                monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
                    new List<ComponentMonitoringSetting>()
                    {
                    new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                    new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)

                    });
            }

            //Print metrics for a Streaming Endpoint.
            PrintStreamingEndpointMetrics();

            Console.ReadLine();
        }

        private static string GetTableEndPoint()
        {
            return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
        }

        private static void PrintStreamingEndpointMetrics()
        {
            Console.WriteLine(string.Format("Telemetry for streaming endpoint '{0}'", _streamingEndpoint.Name));

            DateTime timerangeEnd = DateTime.UtcNow;
            DateTime timerangeStart = DateTime.UtcNow.AddHours(-5);

            // Get some streaming endpoint metrics.
            var telemetry = _streamingEndpoint.GetTelemetry();

            var res = telemetry.GetStreamingEndpointRequestLogs(timerangeStart, timerangeEnd);

            Console.Title = "Streaming endpoint metrics:";

            foreach (var log in res)
            {
                Console.WriteLine("AccountId: {0}", log.AccountId);
                Console.WriteLine("BytesSent: {0}", log.BytesSent);
                Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
                Console.WriteLine("HostName: {0}", log.HostName);
                Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
                Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
                Console.WriteLine("RequestCount: {0}", log.RequestCount);
                Console.WriteLine("ResultCode: {0}", log.ResultCode);
                Console.WriteLine("RowKey: {0}", log.RowKey);
                Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
                Console.WriteLine("StatusCode: {0}", log.StatusCode);
                Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
                Console.WriteLine();
            }

            Console.WriteLine();
        }

        private static void PrintChannelMetrics()
        {
            if (_channel == null)
            {
                Console.WriteLine("There are no channels in this AMS account");
                return;
            }

            Console.WriteLine(string.Format("Telemetry for channel '{0}'", _channel.Name));

            DateTime timerangeEnd = DateTime.UtcNow;
            DateTime timerangeStart = DateTime.UtcNow.AddHours(-5);

            // Get some channel metrics.
            var telemetry = _channel.GetTelemetry();

            var channelMetrics = telemetry.GetChannelHeartbeats(timerangeStart, timerangeEnd);

            // Print the channel metrics.
            Console.WriteLine("Channel metrics:");

            foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
            {
                Console.WriteLine(
                    "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
                    channelHeartbeat.ObservedTime,
                    channelHeartbeat.LastTimestamp,
                    channelHeartbeat.IncomingBitrate);
            }

            Console.WriteLine();
        }
    }
}
```

## <a name="next-steps"></a>다음 단계

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
