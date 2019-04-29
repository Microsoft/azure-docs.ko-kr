---
title: Azure Queue Storage를 사용하여 .NET으로 Media Services 작업 알림 모니터링 | Microsoft 문서
description: Azure Queue Storage를 사용하여 Media Services 작업 알림을 모니터링하는 방법에 대해 알아봅니다. 코드 샘플은 C#으로 작성되었으며 Media Services SDK for .NET을 사용합니다.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.assetid: f535d0b5-f86c-465f-81c6-177f4f490987
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 811ed6dde4ed98222bdd2589d8d6fdd8e0f64ce8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61465629"
---
# <a name="use-azure-queue-storage-to-monitor-media-services-job-notifications-with-net"></a>Azure Queue Storage를 사용하여 .NET으로 Media Services 작업 알림 모니터링 

인코딩 작업을 실행할 때 작업 진행 상태를 추적하는 방법이 종종 필요합니다. [Azure Queue Storage](../../storage/storage-dotnet-how-to-use-queues.md)에 알림을 배달하도록 Media Services를 구성할 수 있습니다. Queue Storage에서 알림을 가져와 작업 진행 상태를 모니터링할 수 있습니다. 

세계 어디에서나 Queue Storage에 배달된 메시지에 액세스할 수 있습니다. Queue Storage 메시징 아키텍처는 안정적이고 확장성이 뛰어납니다. 다른 방법을 사용하는 것보다 메시지에 대한 Queue Storage를 폴링하는 것이 좋습니다.

Media Services 알림 수신에 대한 일반적인 시나리오는 인코딩 작업 후 일부 추가 작업을 수행해야 하는 콘텐츠 관리 시스템을 개발하는 경우(예를 들어, 다음에는 워크플로에서 다음 단계를 트리거하거나 콘텐츠를 게시)입니다.

이 문서에서는 Queue Storage에서 알림 메시지를 가져오는 방법을 보여 줍니다.  

## <a name="considerations"></a>고려 사항
Queue Storage를 사용하는 Media Services 애플리케이션을 개발할 때 다음 사항을 고려합니다.

* Queue Storage는 선입 선출(FIFO) 순차적 전달을 보장하지 않습니다. 자세한 내용은 [Azure 큐 및 Azure Service Bus 큐 비교 및 대조](https://msdn.microsoft.com/library/azure/hh767287.aspx)를 참조하세요.
* Queue Storage는 푸시 서비스가 아닙니다. 큐를 폴링해야 합니다.
* 개수에 관계 없이 큐를 사용할 수 있습니다. 자세한 내용은 [큐 서비스 REST API](https://docs.microsoft.com/rest/api/storageservices/Queue-Service-REST-API)를 참조하세요.
* Queue Storage에는 알아야 할 몇 가지 제한 사항 및 주의 사항이 있습니다. 이러한 내용은 [Azure 큐 및 Azure Service Bus 큐 - 비교 및 대조](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)에 설명되어 있습니다.

## <a name="net-code-example"></a>.NET 코드 예제

이 섹션의 코드는 다음 작업을 수행합니다.

1. 알림 메시지 형식에 매핑되는 **EncodingJobMessage** 클래스를 정의합니다. 코드는 큐에서 수신한 메시지를 **EncodingJobMessage** 유형의 개체로 deserialize합니다.
2. app.config 파일에서 Media Services 및 Storage 계정 정보를 로드합니다. 코드 예제에서는 이 정보를 사용하여 **CloudMediaContext** 및 **CloudQueue** 개체를 만듭니다.
3. 인코딩 작업에 대한 알림 메시지를 받는 큐를 만듭니다.
4. 큐에 매핑되는 알림 끝점을 만듭니다.
5. 알림 끝점 작업에 연결하고 인코딩 작업을 제출합니다. 작업에 연결하는 여러 알림 끝점이 있을 수 있습니다.
6. **NotificationJobState.FinalStatesOnly**를 **AddNew** 메서드에 전달합니다. 이 예제에서는 작업 처리의 최종 상태만 확인합니다.

        job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
7. **NotificationJobState.All**을 전달하는 경우 모든 상태 변경 알림(큐에 대기, 예약됨, 처리 중, 완료됨)을 가져와야 합니다. 그러나 앞에서 설명한 대로 Queue Storage가 순차적 전달을 보장하지 않습니다. 메시지의 순서를 지정하려면 **Timestamp** 속성(아래 예제에서는 **EncodingJobMessage** 형식에서 정의됨)을 사용합니다. 중복된 메시지도 허용됩니다. 중복을 확인하려면 **ETag 속성**(**EncodingJobMessage** 형식에서 정의됨)을 사용합니다. 또한 일부 상태 변경 알림을 건너뛸 수 있습니다.
8. 10초마다 큐를 검사하여 작업이 완성된 상태가 될 때를 기다립니다. 처리된 후 메시지를 삭제합니다.
9. 큐와 알림 끝점을 삭제합니다.

> [!NOTE]
> 작업 상태 모니터링 방법으로 다음 예제와 같이 알림 메시지 수신을 권장합니다.
>
> 또는 **IJob.State** 속성을 사용하여 작업 상태를 확인할 수 있습니다.  **IJob**의 상태가 **완료됨**으로 설정되기 전에 작업 완료에 대한 알림 메시지를 수신할 수 있습니다. **IJob.State** 속성은 약간의 지연 시간을 포함하여 정확한 상태를 반영합니다.
>
>

### <a name="create-and-configure-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기 및 구성

1. 개발 환경을 설정하고 [.NET을 사용한 Media Services 환경](media-services-dotnet-how-to-use.md)에 설명된 대로 연결 정보를 사용하여 app.config 파일을 채웁니다. 
2. 로컬 드라이브 내 임의의 위치에 새 폴더를 만들고, 인코딩하여 스트리밍하거나 점진적으로 다운로드하려는 .mp4 파일을 복사합니다. 이 예제에서는 "C:\Media" 경로가 사용됩니다.
3. **System.Runtime.Serialization** 라이브러리에 참조를 추가합니다.

### <a name="code"></a>코드

```csharp
using System;
using System.Linq;
using System.Configuration;
using System.IO;
using System.Threading;
using System.Collections.Generic;
using Microsoft.WindowsAzure.MediaServices.Client;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Queue;
using System.Runtime.Serialization.Json;

namespace JobNotification
{
    public class EncodingJobMessage
    {
        // MessageVersion is used for version control.
        public String MessageVersion { get; set; }

        // Type of the event. Valid values are
        // JobStateChange and NotificationEndpointRegistration.
        public String EventType { get; set; }

        // ETag is used to help the customer detect if
        // the message is a duplicate of another message previously sent.
        public String ETag { get; set; }

        // Time of occurrence of the event.
        public String TimeStamp { get; set; }

        // Collection of values specific to the event.

        // For the JobStateChange event the values are:
        //     JobId - Id of the Job that triggered the notification.
        //     NewState- The new state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
        //     OldState- The old state of the Job. Valid values are:
        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished

        // For the NotificationEndpointRegistration event the values are:
        //     NotificationEndpointId- Id of the NotificationEndpoint
        //          that triggered the notification.
        //     State- The state of the Endpoint.
        //          Valid values are: Registered and Unregistered.

        public IDictionary<string, object> Properties { get; set; }
    }

    class Program
    {

        // Read values from the App.config file.
        private static readonly string _AADTenantDomain =
            ConfigurationManager.AppSettings["AMSAADTenantDomain"];
        private static readonly string _RESTAPIEndpoint =
            ConfigurationManager.AppSettings["AMSRESTAPIEndpoint"];
        private static readonly string _AMSClientId =
            ConfigurationManager.AppSettings["AMSClientId"];
        private static readonly string _AMSClientSecret =
            ConfigurationManager.AppSettings["AMSClientSecret"];

        private static readonly string _StorageConnectionString = 
            ConfigurationManager.AppSettings["StorageConnectionString"];

        private static CloudMediaContext _context = null;
        private static CloudQueue _queue = null;
        private static INotificationEndPoint _notificationEndPoint = null;

        private static readonly string _singleInputMp4Path =
            Path.GetFullPath(@"C:\Media\BigBuckBunny.mp4");

        static void Main(string[] args)
        {
            string endPointAddress = Guid.NewGuid().ToString();

            // Create the context.
            AzureAdTokenCredentials tokenCredentials = 
                new AzureAdTokenCredentials(_AADTenantDomain,
                    new AzureAdClientSymmetricKey(_AMSClientId, _AMSClientSecret),
                    AzureEnvironments.AzureCloudEnvironment);

            var tokenProvider = new AzureAdTokenProvider(tokenCredentials);

            _context = new CloudMediaContext(new Uri(_RESTAPIEndpoint), tokenProvider);
            
            // Create the queue that will be receiving the notification messages.
            _queue = CreateQueue(_StorageConnectionString, endPointAddress);

            // Create the notification point that is mapped to the queue.
            _notificationEndPoint =
                    _context.NotificationEndPoints.Create(
                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);


            if (_notificationEndPoint != null)
            {
                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
                WaitForJobToReachedFinishedState(job.Id);
            }

            // Clean up.
            _queue.Delete();
            _notificationEndPoint.Delete();
        }


        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
        {
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);

            // Create the queue client
            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

            // Retrieve a reference to a queue
            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);

            // Create the queue if it doesn't already exist
            queue.CreateIfNotExists();

            return queue;
        }


        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
        {
            // Declare a new job.
            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");

            //Create an encrypted asset and upload the mp4.
            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted,
                inputMediaFilePath);

            // Get a media processor reference, and pass to it the name of the
            // processor to use for the specific task.
            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");

            // Create a task with the conversion details, using a configuration file.
            ITask task = job.Tasks.AddNew("My encoding Task",
                processor,
                "Adaptive Streaming",
                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);

            // Specify the input asset to be encoded.
            task.InputAssets.Add(asset);

            // Add an output asset to contain the results of the job.
            task.OutputAssets.AddNew("Output asset",
                AssetCreationOptions.None);

            // Add a notification point to the job. You can add multiple notification points.  
            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly,
                _notificationEndPoint);

            job.Submit();

            return job;
        }

        public static void WaitForJobToReachedFinishedState(string jobId)
        {
            int expectedState = (int)JobState.Finished;
            int timeOutInSeconds = 600;

            bool jobReachedExpectedState = false;
            DateTime startTime = DateTime.Now;
            int jobState = -1;

            while (!jobReachedExpectedState)
            {
                // Specify how often you want to get messages from the queue.
                Thread.Sleep(TimeSpan.FromSeconds(10));

                foreach (var message in _queue.GetMessages(10))
                {
                    using (Stream stream = new MemoryStream(message.AsBytes))
                    {
                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
                        settings.UseSimpleDictionaryFormat = true;
                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);

                        Console.WriteLine();

                        // Display the message information.
                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
                        foreach (var property in encodingJobMsg.Properties)
                        {
                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
                        }

                        // We are only interested in messages
                        // where EventType is "JobStateChange".
                        if (encodingJobMsg.EventType == "JobStateChange")
                        {
                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
                            if (JobId == jobId)
                            {
                                string oldJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
                                string newJobStateStr = (String)encodingJobMsg.Properties.
                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;

                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);

                                if (newJobState == (JobState)expectedState)
                                {
                                    Console.WriteLine("job with Id: {0} reached expected state: {1}",
                                        jobId, newJobState);
                                    jobReachedExpectedState = true;
                                    break;
                                }
                            }
                        }
                    }
                    // Delete the message after we've read it.
                    _queue.DeleteMessage(message);
                }

                // Wait until timeout
                TimeSpan timeDiff = DateTime.Now - startTime;
                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
                if (timedOut)
                {
                    Console.WriteLine(@"Timeout for checking job notification messages,
                                        latest found state ='{0}', wait time = {1} secs",
                        jobState,
                        timeDiff.TotalSeconds);

                    throw new TimeoutException();
                }
            }
        }

        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
        {
            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(),
                assetCreationOptions);

            var fileName = Path.GetFileName(singleFilePath);

            var assetFile = asset.AssetFiles.Create(fileName);

            Console.WriteLine("Created assetFile {0}", assetFile.Name);
            Console.WriteLine("Upload {0}", assetFile.Name);

            assetFile.Upload(singleFilePath);
            Console.WriteLine("Done uploading of {0}", assetFile.Name);

            return asset;
        }

        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
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

위 예제는 다음과 같이 출력됩니다. 값은 달라질 수 있습니다.

    Created assetFile BigBuckBunny.mp4
    Upload BigBuckBunny.mp4
    Done uploading of BigBuckBunny.mp4

    EventType: NotificationEndPointRegistration
    MessageVersion: 1.0
    ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
    TimeStamp: 2013-05-14T20:22:37
        NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
        State: Registered
        Name: dde957b2-006e-41f2-9869-a978870ac620
        Created: 2013-05-14T20:22:35

    EventType: JobStateChange
    MessageVersion: 1.0
    ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
    TimeStamp: 2013-05-14T20:24:40
        JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
        JobName: My MP4 to Smooth Streaming encoding job
        NewState: Finished
        OldState: Processing
        AccountName: westeuropewamsaccount
    job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected
    State: Finished


## <a name="next-step"></a>다음 단계
Media Services 학습 경로를 검토합니다.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>피드백 제공
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
