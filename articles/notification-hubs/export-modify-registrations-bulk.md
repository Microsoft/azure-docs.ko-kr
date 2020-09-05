---
title: 대량으로 Azure Notification Hubs 등록 내보내기 및 가져오기 | Microsoft Docs
description: Notification Hubs 대량 지원을 사용 하 여 알림 허브에서 많은 작업을 수행 하거나 모든 등록을 내보내는 방법을 알아봅니다.
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 08/04/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 03/18/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: c0771864229c8a3918da076de48fb6e033d2cf5a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018181"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>대량으로 Azure Notification Hubs 등록 내보내기 및 가져오기

알림 허브에서 많은 수의 등록을 만들거나 수정해야 하는 시나리오가 있습니다. 이러한 시나리오 중 일부는 일괄 처리 계산 후의 태그 업데이트 이거나 기존 푸시 구현을 Azure Notification Hubs를 사용 하도록 마이그레이션하는 것입니다.

이 문서에서는 알림 허브에서 많은 수의 작업을 수행 하거나 모든 등록을 대량으로 내보내는 방법을 설명 합니다.

## <a name="high-level-flow"></a>상위 수준 흐름

일괄 처리 지원은 수백만 개의 등록을 포함하여 장기간 실행되는 작업을 지원하도록 설계되었습니다. 이러한 규모를 달성할 수 있도록 batch 지원에서는 Azure Storage를 사용 하 여 작업 세부 정보 및 출력을 저장 합니다. 대량 업데이트 작업의 경우 사용자가 blob 컨테이너에서 파일을 만들어야 합니다. 이 파일의 내용은 등록 업데이트 작업 목록입니다. 작업을 시작할 때 사용자는 입력 blob의 URL과 출력 디렉터리(및 blob 컨테이너)의 URL을 함께 제공합니다. 작업이 시작 된 후 사용자는 작업을 시작할 때 제공 된 URL 위치를 쿼리하여 상태를 확인할 수 있습니다. 특정 작업은 특정 종류의 작업 (만들기, 업데이트 또는 삭제)만 수행할 수 있습니다. 내보내기 작업은 유사하게 수행됩니다.

## <a name="import"></a>가져오기

### <a name="set-up"></a>설정

이 섹션에서는 다음과 같은 엔터티가 있다고 가정 합니다.

- 프로비전된 알림 허브
- Azure 저장소 blob 컨테이너
- [Azure Storage nuget 패키지](https://www.nuget.org/packages/windowsazure.storage/) 및 [Notification Hubs nuget 패키지](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)에 대 한 참조입니다.

### <a name="create-input-file-and-store-it-in-a-blob"></a>입력 파일을 만들어 blob에 저장

입력 파일에는 XML로 직렬화된 등록 목록이 행당 하나씩 포함됩니다. 다음 코드 예제에서는 Azure SDK를 사용 하 여 등록을 직렬화 하 고 blob 컨테이너에 업로드 하는 방법을 보여 줍니다.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(registrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> 위의 코드는 메모리에서 등록을 직렬화한 다음 전체 스트림을 blob에 업로드합니다. 몇 메가바이트 이상의 파일을 업로드 한 경우 이러한 단계를 수행 하는 방법에 대 한 Azure blob 지침을 참조 하세요. 예: [블록 blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="create-url-tokens"></a>URL 토큰 만들기

입력 파일을 업로드 한 후에는 입력 파일과 출력 디렉터리 모두에 대해 알림 허브에 제공할 Url을 생성 합니다. 입력 및 출력에 대해 두 개의 다른 blob 컨테이너를 사용할 수 있습니다.

```csharp
static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
    };

    string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + sasContainerToken);
}

static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
{
    SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
    {
        SharedAccessExpiryTime = DateTime.UtcNow.AddDays(1),
        Permissions = SharedAccessBlobPermissions.Read
    };
    string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);
    return new Uri(container.Uri + "/" + filePath + sasToken);
}
```

### <a name="submit-the-job"></a>작업 제출

이제 두 개의 입력 및 출력 Url을 사용 하 여 batch 작업을 시작할 수 있습니다.

```csharp
NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
var createTask = client.SubmitNotificationHubJobAsync(
new NotificationHubJob {
        JobType = NotificationHubJobType.ImportCreateRegistrations,
        OutputContainerUri = outputContainerSasUri,
        ImportFileUri = inputFileSasUri
    }
);
createTask.Wait();

var job = createTask.Result;
long i = 10;
while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
{
    var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
    getJobTask.Wait();
    job = getJobTask.Result;
    Thread.Sleep(1000);
    i--;
}
```

이 예제에서는 입력 및 출력 Url 외에 `NotificationHubJob` 다음 형식 중 하나일 수 있는 개체를 포함 하는 개체를 만듭니다 `JobType` .

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

호출이 완료 되 면 알림 허브에서 작업을 계속 하 고 [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet)에 대 한 호출을 사용 하 여 해당 상태를 확인할 수 있습니다.

작업이 완료되면 출력 디렉터리에서 다음 파일을 확인하여 결과를 조사할 수 있습니다.

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

이러한 파일에는 일괄 처리에서 성공한 작업과 실패한 작업 목록이 포함됩니다. 파일 형식은 이며 `.cvs` 각 행에는 원래 입력 파일의 줄 번호와 작업의 출력 (일반적으로 만들어지거나 업데이트 된 등록 설명)이 있습니다.

### <a name="full-sample-code"></a>전체 샘플 코드

다음 샘플 코드는 등록을 알림 허브로 가져옵니다.

```csharp
using Microsoft.Azure.NotificationHubs;
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Blob;
using System;
using System.Collections.Generic;
using System.Globalization;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Xml;

namespace ConsoleApplication1
{
    class Program
    {
        private static string CONNECTION_STRING = "---";
        private static string HUB_NAME = "---";
        private static string INPUT_FILE_NAME = "CreateFile.txt";
        private static string STORAGE_ACCOUNT = "---";
        private static string STORAGE_PASSWORD = "---";
        private static StorageUri STORAGE_ENDPOINT = new StorageUri(new Uri("---"));

        static void Main(string[] args)
        {
            var descriptions = new[]
            {
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMkUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMjUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMhUxREQFBlVTTkMwMQ"),
                new MpnsRegistrationDescription(@"http://dm2.notify.live.net/throttledthirdparty/01.00/12G9Ed13dLb5RbCii5fWzpFpAgAAAAADAQAAAAQUZm52OkJCMjg1QTg1QkZDMdUxREQFBlVTTkMwMQ"),
            };

            // Write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            // Import this file
            NotificationHubClient client = NotificationHubClient.CreateClientFromConnectionString(CONNECTION_STRING, HUB_NAME);
            var createTask = client.SubmitNotificationHubJobAsync(
                new NotificationHubJob {
                    JobType = NotificationHubJobType.ImportCreateRegistrations,
                    OutputContainerUri = outputContainerSasUri,
                    ImportFileUri = inputFileSasUri
                }
            );
            createTask.Wait();

            var job = createTask.Result;
            long i = 10;
            while (i > 0 && job.Status != NotificationHubJobStatus.Completed)
            {
                var getJobTask = client.GetNotificationHubJobAsync(job.JobId);
                getJobTask.Wait();
                job = getJobTask.Result;
                Thread.Sleep(1000);
                i--;
            }
        }

        private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
        {
            StringBuilder builder = new StringBuilder();
            foreach (var registrationDescription in descriptions)
            {
                builder.AppendLine(registrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            // Set the expiry time and permissions for the container.
            // In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            // Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            // Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + "/" + filePath + sasToken);
        }

        static string GetJobPath(string namespaceName, string notificationHubPath, string jobId)
        {
            return string.Format(CultureInfo.InvariantCulture, @"{0}//{1}/{2}/", namespaceName, notificationHubPath, jobId);
        }
    }
}
```

## <a name="export"></a>내보내기

등록 내보내기는 가져오기와 유사하지만 다음과 같은 차이점이 있습니다.

- 출력 URL만 필요합니다.
- ExportRegistrations 형식의 NotificationHubJob을 만듭니다.

### <a name="sample-code-snippet"></a>샘플 코드 조각

다음은 Java에서 등록을 내보내는 샘플 코드 조각입니다.

```java
// Submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// Wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>다음 단계

등록에 대해 자세히 알아보려면 다음 문서를 참조 하세요.

- [등록 관리](notification-hubs-push-notification-registration-management.md)
- [등록에 대한 태그](notification-hubs-tags-segment-push-message.md)
- [템플릿 등록](notification-hubs-templates-cross-platform-push-messages.md)
