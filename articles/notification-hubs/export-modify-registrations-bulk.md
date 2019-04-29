---
title: Azure Notification Hubs 등록을 대량 가져오기 및 내보내기 | Microsoft Docs
description: Notification Hubs 대량 지원을 사용 하 여 많은 수의 알림 허브에 대 한 작업을 수행 하거나 모든 등록을 내보내는 방법에 알아봅니다.
services: notification-hubs
author: jwargo
manager: patniko
editor: spelluru
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 03/18/2019
ms.author: jowargo
ms.openlocfilehash: c24fcd5f007b641bb594bb07348491f70c03ea41
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623989"
---
# <a name="export-and-import-azure-notification-hubs-registrations-in-bulk"></a>내보내기 및 Azure Notification Hubs 등록을 대량 가져오기
생성 또는 많은 수의 알림 허브에서 등록을 수정할 필요는 시나리오가 있습니다. 이러한 시나리오 중 일부는 일괄 처리 계산 후 또는 기존 푸시 구현을 Notification Hubs를 사용 하 여 마이그레이션 태그 업데이트입니다.

이 문서에는 모든 등록을 대량 내보내기 또는 많은 수의 알림 허브에 대 한 작업을 수행 하는 방법을 설명 합니다.

## <a name="high-level-flow"></a>상위 수준 흐름
일괄 처리 지원은 수백만 개 등록 관련 된 장기 실행 작업을 지원 하도록 설계 되었습니다. 이 규모를 달성 하려면 일괄 처리 지원 작업 세부 정보 및 출력을 저장할 Azure Storage를 사용 합니다. 대량 업데이트 작업에 대 한 사용자가 내용이 등록 업데이트 작업의 목록은 blob 컨테이너에 파일을 만들 필요가 있습니다. 작업을 시작할 때 사용자 (blob 컨테이너)에 출력 디렉터리에 대 한 URL과 함께 입력된 blob에 대 한 URL을 제공 합니다. 작업이 시작 된 후 사용자 작업의 시작에서 제공 하는 URL 위치를 쿼리하여 상태를 확인할 수 있습니다. 특정 작업은 특정 종류의 작업에만 수행할 수 있습니다 (만들고, 업데이트 또는 삭제). 내보내기 작업은 유사 하 게 수행 됩니다.

## <a name="import"></a>가져오기

### <a name="set-up"></a>설정
이 섹션에서는 다음 엔터티를 있다고 가정 합니다.

- 프로 비전 된 알림 허브입니다.
- Azure Storage blob 컨테이너입니다.
- 에 대 한 참조를 [Azure Storage NuGet 패키지가](https://www.nuget.org/packages/windowsazure.storage/) 하 고 [Notification Hubs NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.9)합니다.

### <a name="create-input-file-and-store-it-in-a-blob"></a>입력된 파일을 만들고 blob에 저장
입력된 파일을 행당 하나씩 XML로 직렬화 된 등록의 목록을 포함 합니다. Azure SDK를 사용 하 여, 다음 코드 예제에서는 등록을 직렬화 하 고 blob 컨테이너에 업로드 하는 방법을 보여 줍니다.

```csharp
private static void SerializeToBlob(CloudBlobContainer container, RegistrationDescription[] descriptions)
{
    StringBuilder builder = new StringBuilder();
    foreach (var registrationDescription in descriptions)
    {
        builder.AppendLine(RegistrationDescription.Serialize());
    }

    var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
    using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
    {
        inputBlob.UploadFromStream(stream);
    }
}
```

> [!IMPORTANT]
> 앞의 코드는 메모리에서 등록을 직렬화 하 고 전체 스트림을 blob에 업로드 합니다. 둘 몇 메가바이트의 파일을 업로드 한 경우 이러한 단계를 수행 하는 방법에는 Azure blob 지침을 참조 하세요 예를 들어 [블록 blob](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs)합니다.

### <a name="create-url-tokens"></a>URL 토큰 만들기
입력된 파일이 업로드 되 면 입력 파일과 출력 디렉터리에 대 한 알림 허브를 제공 하는 Url을 생성 합니다. 입력 및 출력에 대 한 두 개의 다른 blob 컨테이너를 사용할 수 있습니다.

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
두 입력 및 출력 Url을 사용 하 여 이제 일괄 처리 작업을 시작할 수 있습니다.

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

이 예제에서는 입력 및 출력 Url 외에도 만듭니다는 `NotificationHubJob` 포함 된 개체는 `JobType` 다음 유형 중 하나일 수 있는 개체:

- `ImportCreateRegistrations`
- `ImportUpdateRegistrations`
- `ImportDeleteRegistrations`

호출이 완료 되기, 작업 알림 허브에서 계속 되 고 해당 상태에 대 한 호출을 사용 하 여 확인할 수 있습니다 [GetNotificationHubJobAsync](/dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.getnotificationhubjobasync?view=azure-dotnet)합니다.

작업이 완료 되 면 출력 디렉터리에서 다음 파일을 살펴보고 결과 검사할 수 있습니다.

- `/<hub>/<jobid>/Failed.txt`
- `/<hub>/<jobid>/Output.txt`

이러한 파일에 일괄 처리에서 성공한 요청과 실패 한 작업 목록을 포함합니다. 파일 형식은 `.cvs`, 각 행에 원래 입력된 파일의 줄 번호 및 작업 (일반적으로 만들어지거나 업데이트 된 등록 설명)의 출력에서.

### <a name="full-sample-code"></a>전체 샘플 코드
다음 샘플 코드는 알림 허브에 등록을 가져옵니다.

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

            //write to blob store to create an input file
            var blobClient = new CloudBlobClient(STORAGE_ENDPOINT, new Microsoft.WindowsAzure.Storage.Auth.StorageCredentials(STORAGE_ACCOUNT, STORAGE_PASSWORD));
            var container = blobClient.GetContainerReference("testjobs");
            container.CreateIfNotExists();

            SerializeToBlob(container, descriptions);

            // TODO then create Sas
            var outputContainerSasUri = GetOutputDirectoryUrl(container);
            var inputFileSasUri = GetInputFileUrl(container, INPUT_FILE_NAME);


            //Lets import this file
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
                builder.AppendLine(RegistrationDescription.Serialize());
            }

            var inputBlob = container.GetBlockBlobReference(INPUT_FILE_NAME);
            using (MemoryStream stream = new MemoryStream(Encoding.UTF8.GetBytes(builder.ToString())))
            {
                inputBlob.UploadFromStream(stream);
            }
        }

        static Uri GetOutputDirectoryUrl(CloudBlobContainer container)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.List | SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
            return new Uri(container.Uri + sasContainerToken);
        }

        static Uri GetInputFileUrl(CloudBlobContainer container, string filePath)
        {
            //Set the expiry time and permissions for the container.
            //In this case no start time is specified, so the shared access signature becomes valid immediately.
            SharedAccessBlobPolicy sasConstraints = new SharedAccessBlobPolicy
            {
                SharedAccessExpiryTime = DateTime.UtcNow.AddHours(4),
                Permissions = SharedAccessBlobPermissions.Read
            };

            //Generate the shared access signature on the container, setting the constraints directly on the signature.
            string sasToken = container.GetBlockBlobReference(filePath).GetSharedAccessSignature(sasConstraints);

            //Return the URI string for the container, including the SAS token.
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
등록 내보내기 하는 것은 다음과 같은 차이점을 사용 하 여 가져오기를 비슷합니다.

- 출력 URL만 하면 됩니다.
- ExportRegistrations 형식의 NotificationHubJob 만들어야합니다.

### <a name="sample-code-snippet"></a>샘플 코드 조각
Java에서 등록 내보내기에 대 한 샘플 코드 조각은 다음과 같습니다.

```java
// submit an export job
NotificationHubJob job = new NotificationHubJob();
job.setJobType(NotificationHubJobType.ExportRegistrations);
job.setOutputContainerUri("container uri with SAS signature");
job = hub.submitNotificationHubJob(job);

// wait until the job is done
while(true){
    Thread.sleep(1000);
    job = hub.getNotificationHubJob(job.getJobId());
    if(job.getJobStatus() == NotificationHubJobStatus.Completed)
        break;
}

```

## <a name="next-steps"></a>다음 단계
등록에 대 한 자세한 내용은 다음 문서를 참조 하세요.

- [등록 관리](notification-hubs-push-notification-registration-management.md)
- [등록에 대 한 태그](notification-hubs-tags-segment-push-message.md)
- [템플릿 등록](notification-hubs-templates-cross-platform-push-messages.md)
