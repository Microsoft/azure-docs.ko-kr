---
title: .NET용 파일 규칙 라이브러리를 사용하여 Azure Storage에 작업 및 태스크 출력 유지 - Azure Batch | Microsoft Docs
description: .NET용 Azure Batch 파일 규칙 라이브러리를 사용하여 Batch 태스크 및 작업 출력을 Azure Storage에 유지하는 방법을 알아보고 Azure Portal에서 유지된 출력을 확인합니다.
services: batch
documentationcenter: .net
author: laurenhughes
manager: jeconnoc
editor: ''
ms.assetid: 16e12d0e-958c-46c2-a6b8-7843835d830e
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 11/14/2018
ms.author: lahugh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d30a5ca0910c5ceebb38dec7b4cdbffd9b3cf27e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60550332"
---
# <a name="persist-job-and-task-data-to-azure-storage-with-the-batch-file-conventions-library-for-net"></a>.NET용 Batch 파일 규칙 라이브러리를 사용하여 Azure Storage에 작업 및 태스크 데이터 유지

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

태스크 데이터를 유지하는 한 가지 방법은 [.NET용 Azure Batch 파일 규칙 라이브러리][nuget_package](영문)를 사용하는 것입니다. 파일 규칙 라이브러리는 태스크 출력 데이터를 Azure Storage에 저장하고 검색하는 프로세스를 간소화합니다. 태스크 코드(예: 파일 유지용)와 클라이언트 코드(예 파일 나열 및 검색용) 모두에서 파일 규칙 라이브러리를 사용할 수 있습니다. 또한 태스크 코드에서 라이브러리를 사용하여 [태스크 종속성](batch-task-dependencies.md) 시나리오에서와 같이 업스트림 태스크의 출력을 검색할 수도 있습니다.

파일 규칙 라이브러리를 사용하여 출력 파일을 검색하려면 ID별 및 용도별로 나열하여 지정된 작업 또는 태스크에 대한 파일을 찾을 수 있습니다. 파일의 이름이나 위치를 알 필요가 없습니다. 예를 들어 파일 규칙 라이브러리를 사용하여 지정된 태스크에 대한 모든 중간 파일을 나열하거나 지정된 작업에 대한 미리 보기 파일을 가져올 수 있습니다.

> [!TIP]
> 2017-05-01 버전 이상에서는 Batch 서비스 API에서 가상 컴퓨터 구성으로 만든 풀에서 실행되는 태스크 및 작업 관리자 태스크의 출력 데이터를 Azure Storage에 유지하도록 지원합니다. Batch 서비스 API는 태스크를 만들고 파일 규칙 라이브러리 대신 사용할 수 있는 코드 내에서 출력을 유지하는 간단한 방법을 제공합니다. 태스크가 실행 중인 애플리케이션을 업데이트하지 않고도 출력을 유지하도록 Batch 클라이언트 애플리케이션을 수정할 수 있습니다. 자세한 내용은 [Batch 서비스 API를 사용하여 Azure Storage에 태스크 데이터 유지](batch-task-output-files.md)를 참조하세요.

## <a name="when-do-i-use-the-file-conventions-library-to-persist-task-output"></a>파일 규칙 라이브러리를 사용하여 태스크 출력을 유지하는 경우는?

Azure Batch는 태스크 출력을 유지하는 한 가지 이상의 방법을 제공합니다. 파일 규칙이 가장 적합한 시나리오는 다음과 같습니다.

- 파일 규칙 라이브러리를 사용하여 파일을 유지하도록 태스크가 실행되는 애플리케이션의 코드를 쉽게 수정할 수 있습니다.
- 태스크가 계속 실행되는 동안 Azure Storage로 데이터를 스트리밍하려고 합니다.
- 클라우드 서비스 구성 또는 가상 머신 구성으로 만든 풀에서 데이터를 유지하려고 합니다.
- 작업의 클라이언트 애플리케이션 또는 다른 태스크에서 ID별 또는 용도별로 태스크 출력 파일을 찾고 다운로드해야 합니다.
- Azure Portal에서 태스크 출력을 확인하려고 합니다.

위에서 나열한 시나리오와 다른 시나리오이면 다른 방법을 고려해야 할 수도 있습니다. 태스크 출력을 유지하기 위한 다른 옵션에 대한 자세한 내용은 [Azure Storage에 작업 및 태스크 출력 유지](batch-task-output.md)를 참조하세요.

## <a name="what-is-the-batch-file-conventions-standard"></a>Batch 파일 규칙 표준이란?

[Batch 파일 규칙 표준](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)(영문)은 출력 파일이 작성되는 대상 컨테이너 및 Blob 경로에 대한 이름 지정 체계를 제공합니다. 파일 규칙 표준을 준수하는 Azure Storage에 유지된 파일은 Azure Portal에서 자동으로 볼 수 있습니다. 포털에서는 명명 규칙을 인식하고 있으므로 이 표준을 준수하는 파일을 표시할 수 있습니다.

.NET용 파일 규칙 라이브러리는 파일 규칙 표준에 따라 저장소 컨테이너와 태스크 출력 파일의 이름을 자동으로 지정합니다. 또한 파일 규칙 라이브러리는 작업 ID, 태스크 ID 또는 용도에 따라 Azure Storage의 출력 파일을 쿼리하는 메서드를 제공합니다.

.NET 이외의 언어로 개발하는 경우 애플리케이션에서 파일 규칙 표준을 직접 구현할 수 있습니다. 자세한 내용은 [Batch 파일 규칙 표준 구현](batch-task-output.md#implement-the-batch-file-conventions-standard)을 참조하세요.

## <a name="link-an-azure-storage-account-to-your-batch-account"></a>Azure Storage 계정을 Batch 계정에 연결

파일 규칙 라이브러리를 사용하여 Azure Storage에 출력 데이터를 유지하려면 먼저 Azure Storage 계정을 Batch 계정에 연결해야 합니다. 아직 연결하지 않은 경우 [Azure Portal](https://portal.azure.com)을 사용하여 Storage 계정을 Batch 계정에 연결합니다.

1. Azure Portal에서 Batch 계정으로 이동합니다.
1. **설정**에서 **Storage 계정**을 선택합니다.
1. Batch 계정과 연결된 Storage 계정이 아직 없으면 **Storage 계정(없음)** 을 클릭합니다.
1. 구독 목록에서 Storage 계정을 선택합니다. 최상의 성능을 위해 태스크가 실행되는 Batch 계정과 동일한 지역에 있는 Azure Storage 계정을 사용합니다.

## <a name="persist-output-data"></a>출력 데이터 유지

파일 규칙 라이브러리를 사용하여 작업 및 태스크 출력 데이터를 유지하려면 Azure Storage에 컨테이너를 만든 다음 출력을 컨테이너에 저장합니다. 태스크 코드에서 [.NET용 Azure Storage 클라이언트 라이브러리](https://www.nuget.org/packages/WindowsAzure.Storage)를 사용하여 태스크 출력을 컨테이너에 업로드합니다. 

Azure Storage의 컨테이너와 Blob 사용에 대한 자세한 내용은 [.NET을 사용하여 Azure Blob 스토리지 시작](../storage/blobs/storage-dotnet-how-to-use-blobs.md)을 참조하세요.

> [!WARNING]
> 파일 규칙 라이브러리를 사용하여 유지되는 모든 작업 및 태스크 출력은 동일한 컨테이너에 저장됩니다. 많은 수의 태스크에서 동시에 파일을 유지하려고 시도하면 [저장소 제한](../storage/common/storage-performance-checklist.md#blobs)이 적용될 수 있습니다.

### <a name="create-storage-container"></a>저장소 컨테이너 만들기

Azure Storage에 태스크 출력을 유지하려면 먼저 [CloudJob][net_cloudjob].[PrepareOutputStorageAsync][net_prepareoutputasync]를 호출하여 컨테이너를 만듭니다. 이 확장 메서드는 [CloudStorageAccount][net_cloudstorageaccount] 개체를 매개 변수로 사용합니다. Azure Portal과 이 문서의 뒷부분에서 설명하는 검색 메서드에서 해당 콘텐츠를 검색할 수 있도록 파일 규칙 표준에 따라 명명된 컨테이너를 만듭니다.

일반적으로 클라이언트 애플리케이션,&mdash; 즉 풀, 작업 및 태스크를 만드는 애플리케이션에 컨테이너를 만드는 코드를 배치합니다.

```csharp
CloudJob job = batchClient.JobOperations.CreateJob(
    "myJob",
    new PoolInformation { PoolId = "myPool" });

// Create reference to the linked Azure Storage account
CloudStorageAccount linkedStorageAccount =
    new CloudStorageAccount(myCredentials, true);

// Create the blob storage container for the outputs
await job.PrepareOutputStorageAsync(linkedStorageAccount);
```

### <a name="store-task-outputs"></a>태스크 출력 저장

이제 Azure Storage에 컨테이너를 준비했으므로 태스크에서 파일 규칙 라이브러리에 있는 [TaskOutputStorage][net_taskoutputstorage] 클래스를 사용하여 해당 컨테이너에 출력을 저장할 수 있습니다.

태스크 코드에서 먼저 [TaskOutputStorage][net_taskoutputstorage] 개체를 만든 다음 태스크가 해당 작업을 완료하면 [TaskOutputStorage][net_taskoutputstorage].[SaveAsync][net_saveasync] 메서드를 호출하여 Azure Storage에 출력을 저장합니다.

```csharp
CloudStorageAccount linkedStorageAccount = new CloudStorageAccount(myCredentials);
string jobId = Environment.GetEnvironmentVariable("AZ_BATCH_JOB_ID");
string taskId = Environment.GetEnvironmentVariable("AZ_BATCH_TASK_ID");

TaskOutputStorage taskOutputStorage = new TaskOutputStorage(
    linkedStorageAccount, jobId, taskId);

/* Code to process data and produce output file(s) */

await taskOutputStorage.SaveAsync(TaskOutputKind.TaskOutput, "frame_full_res.jpg");
await taskOutputStorage.SaveAsync(TaskOutputKind.TaskPreview, "frame_low_res.jpg");
```

[TaskOutputStorage](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage).[SaveAsync](/dotnet/api/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync#overloads) 메서드의 `kind` 매개 변수는 유지된 파일을 분류합니다. 미리 정의된 4개의 [TaskOutputKind][net_taskoutputkind] 형식, 즉 `TaskOutput`, `TaskPreview`, `TaskLog` 및 `TaskIntermediate.`가 있습니다. 사용자 정의 범주의 출력도 정의할 수 있습니다.

이러한 출력 형식을 사용하면 나중에 지정된 태스크의 보관된 출력에 대해 Batch를 쿼리할 때 나열한 출력 형식을 지정할 수 있습니다. 즉, 태스크에 대한 출력을 나열할 때 출력 형식 중 하나로 목록을 필터링할 수 있습니다. 예를 들어 "Give me the *preview* output for task *109*"와 같이 지정할 수 있습니다. 출력 나열 및 검색에 대한 보다 자세한 내용은 이 문서의 뒷부분에 있는 출력 검색 에서 확인할 수 있습니다.

> [!TIP]
> 출력 종류는 Azure Portal에서 특정 파일이 표시되는 위치도 결정합니다. 즉, *TaskOutput* 범주의 파일은 **작업 출력 파일** 아래에 표시되고, *TaskLog* 파일은 **작업 로그** 아래에 표시됩니다.

### <a name="store-job-outputs"></a>작업 출력 저장

태스크 출력 외에 전체 작업과 연관된 출력도 저장할 수 있습니다. 예를 들어 영화 렌더링 작업의 병합 태스크에서 완전히 렌더링된 영화를 작업 출력으로 보관할 수 있습니다. 작업이 완료되면 클라이언트 애플리케이션에서 작업의 출력을 나열 및 검색할 수 있으며, 개별 태스크를 쿼리할 필요가 없습니다.

[JobOutputStorage][net_joboutputstorage].[SaveAsync][net_joboutputstorage_saveasync] 메서드를 호출하여 작업 출력을 저장하고 [JobOutputKind][net_joboutputkind] 및 파일 이름을 지정합니다.

```csharp
CloudJob job = new JobOutputStorage(acct, jobId);
JobOutputStorage jobOutputStorage = job.OutputStorage(linkedStorageAccount);

await jobOutputStorage.SaveAsync(JobOutputKind.JobOutput, "mymovie.mp4");
await jobOutputStorage.SaveAsync(JobOutputKind.JobPreview, "mymovie_preview.mp4");
```

태스크 출력에 대한 **TaskOutputKind** 형식과 마찬가지로 [JobOutputKind][net_joboutputkind] 형식을 사용하여 작업의 유지된 파일을 분류합니다. 이 매개 변수를 통해 나중에 특정 출력 형식(목록)을 쿼리할 수 있습니다. **JobOutputKind** 형식은 출력 및 미리 보기 형식을 모두 포함하며, 사용자 지정 범주를 만들도록 지원합니다.

### <a name="store-task-logs"></a>태스크 로그 저장

태스크 또는 작업이 완료될 때 영구 저장소에 파일을 유지하는 것 외에도 태스크를 실행하는 동안 업데이트된 파일(로그 파일 또는 `stdout.txt` 및 `stderr.txt`)을 유지해야 할 수도 있습니다. 이를 위해 Azure Batch 파일 규칙 라이브러리에서는 [TaskOutputStorage][net_taskoutputstorage].[SaveTrackedAsync][net_savetrackedasync] 메서드를 제공합니다. [SaveTrackedAsync][net_savetrackedasync]를 사용하여 노드에서 파일 업데이트를 추적(지정한 간격으로)하고 이러한 업데이트를 Azure Storage에 보관할 수 있습니다.

다음 코드 조각에서는 [SaveTrackedAsync][net_savetrackedasync]를 사용하여 태스크가 실행되는 동안 15초마다 Azure Storage에서 `stdout.txt`를 업데이트합니다.

```csharp
TimeSpan stdoutFlushDelay = TimeSpan.FromSeconds(3);
string logFilePath = Path.Combine(
    Environment.GetEnvironmentVariable("AZ_BATCH_TASK_DIR"), "stdout.txt");

// The primary task logic is wrapped in a using statement that sends updates to
// the stdout.txt blob in Storage every 15 seconds while the task code runs.
using (ITrackedSaveOperation stdout =
        await taskStorage.SaveTrackedAsync(
        TaskOutputKind.TaskLog,
        logFilePath,
        "stdout.txt",
        TimeSpan.FromSeconds(15)))
{
    /* Code to process data and produce output file(s) */

    // We are tracking the disk file to save our standard output, but the
    // node agent may take up to 3 seconds to flush the stdout stream to
    // disk. So give the file a moment to catch up.
     await Task.Delay(stdoutFlushDelay);
}
```

주석 처리된 `Code to process data and produce output file(s)` 섹션은 태스크에서 일반적으로 수행하는 코드에 대한 자리 표시자입니다. 예를 들어 Azure Storage에서 데이터를 다운로드하고 변환 또는 계산을 수행하는 코드가 있을 수 있습니다. 이 조각의 중요한 부분은 [SaveTrackedAsync][net_savetrackedasync]를 통해 `using` 블록의 이러한 코드를 래핑하여 파일을 주기적으로 업데이트할 수 있는 방법을 보여 주고 있습니다.

노드 에이전트는 풀의 각 노드에서 실행되고 노드와 Batch 서비스 간에 명령 및 컨트롤 인터페이스를 제공하는 프로그램입니다. 노드 에이전트에서 표준 출력의 내용을 노드의 stdout.txt 파일로 플러시할 시간을 갖도록 하려면 이 `using` 블록의 끝에 `Task.Delay` 호출이 필요합니다. 이 지연 시간이 없다면 마지막 몇 초의 출력을 놓칠 수 있습니다. 이 지연 시간은 일부 파일에만 필요할 수 있습니다.

> [!NOTE]
> **SaveTrackedAsync**를 통해 파일 추적을 사용하도록 설정하면 추적된 파일의 *추가 항목*만 Azure Storage에 유지됩니다. 이 메서드는 회전하지 않는 로그 파일 또는 파일 끝에 추가 작업으로 기록된 다른 파일을 추적하는 데에만 사용합니다.

## <a name="retrieve-output-data"></a>출력 데이터 검색

Azure Batch 파일 규칙 라이브러리를 사용하여 보관된 출력을 검색할 때는 태스크 및 작업 중심 방식으로 검색합니다. Azure Storage 또는 해당 파일 이름에서 경로를 알지 못하더라도 지정된 태스크 또는 작업에 대한 출력을 요청할 수 있습니다. 대신 태스크별 또는 작업 ID별로 출력 파일을 요청할 수 있습니다.

다음 코드 조각에서는 작업의 태스크를 반복하고, 태스크의 출력 파일에 대한 정보 일부를 출력한 다음, 저장소에서 해당 파일을 다운로드합니다.

```csharp
foreach (CloudTask task in myJob.ListTasks())
{
    foreach (OutputFileReference output in
        task.OutputStorage(storageAccount).ListOutputs(
            TaskOutputKind.TaskOutput))
    {
        Console.WriteLine($"output file: {output.FilePath}");

        output.DownloadToFileAsync(
            $"{jobId}-{output.FilePath}",
            System.IO.FileMode.Create).Wait();
    }
}
```

## <a name="view-output-files-in-the-azure-portal"></a>Azure Portal에서 출력 파일 보기

Azure Portal에서는 [Batch 파일 규칙 표준](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)(영문)을 사용하여 연결된 Azure Storage 계정에 유지되는 태스크 출력 파일과 로그를 표시합니다. 이러한 규칙을 선택한 언어로 직접 구현하거나, .NET 애플리케이션의 파일 규칙 라이브러리를 사용할 수 있습니다.

포털에서 출력 파일을 표시하려면 다음 요구 사항을 충족해야 합니다.

1. Batch 계정에 Azure Storage 계정을 연결합니다.
1. 출력을 보관할 때 저장소 컨테이너 및 파일에 대한 미리 정의된 명명 규칙을 따릅니다. [추가 정보][github_file_conventions_readme](영문) 파일 규칙 라이브러리에서 이러한 규칙에 대한 정의를 찾을 수 있습니다. [Azure Batch 파일 규칙][nuget_package](영문) 라이브러리를 사용하여 출력을 유지하면 파일이 파일 규칙 표준에 따라 유지됩니다.

Azure Portal에서 태스크 출력 파일과 로그를 보려면 관심 있는 출력의 태스크로 이동한 다음 **저장된 출력 파일** 또는 **저장된 로그**를 클릭합니다. 이 이미지는 ID가 "007"인 태스크에 대한 **저장된 출력 파일** 을 보여 줍니다.

![Azure Portal의 태스크 출력 블레이드][2]

## <a name="code-sample"></a>코드 샘플

[PersistOutputs][github_persistoutputs] 샘플 프로젝트는 GitHub의 [Azure Batch 코드 샘플][github_samples] 중 하나입니다. 이 Visual Studio 솔루션은 Azure Batch 파일 규칙 라이브러리를 사용하여 영구 저장소에 태스크 출력을 보관하는 방법을 보여 줍니다. 샘플을 실행하려면 다음 단계를 수행합니다.

1. **Visual Studio 2017**에서 프로젝트를 엽니다.
2. Microsoft.Azure.Batch.Samples.Common 프로젝트에서 Batch 및 Storage **계정 자격 증명**을 **AccountSettings.settings**에 추가합니다.
3. **빌드** 합니다(하지만 실행하지 않음). 메시지가 표시되면 모든 NuGet 패키지를 복원합니다.
4. Azure 포털을 사용하여 [PersistOutputsTask](batch-application-packages.md) 에 대한 **애플리케이션 패키지**를 업로드합니다. `PersistOutputsTask.exe` 및 종속 어셈블리를 .zip 패키지에 포함하고, 애플리케이션 ID를 "PersistOutputsTask"로, 애플리케이션 패키지 버전을 "1.0"으로 설정합니다.
5. **PersistOutputs** 프로젝트를 **시작**(실행)합니다.
6. 샘플을 실행하는 데 사용할 지속성 기술을 선택하라는 메시지가 표시될 때 파일 규칙 라이브러리를 통해 샘플을 실행하여 태스크 출력을 유지하려면 **1**을 입력합니다. 

## <a name="next-steps"></a>다음 단계

### <a name="get-the-batch-file-conventions-library-for-net"></a>.NET용 Batch 파일 규칙 라이브러리 가져오기

.NET용 Batch 파일 규칙 라이브러리는 [NuGet][nuget_package]에서 사용할 수 있습니다. 확장 라이브러리는 새 메서드가 포함된 [CloudJob][net_cloudjob] 및 [CloudTask][net_cloudtask] 클래스입니다. 또한 파일 규칙 라이브러리에 대한 [참조 설명서](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files)를 참조하세요.

파일 규칙 라이브러리의 [소스 코드][github_file_conventions]는 GitHub의 .NET용 Microsoft Azure SDK 리포지토리에서 사용할 수 있습니다. 

### <a name="explore-other-approaches-for-persisting-output-data"></a>출력 데이터 유지를 위한 다른 방법 탐색

- 작업 및 태스크 데이터 유지에 대한 개요는 [Azure Storage에 작업 및 태스크 출력 유지](batch-task-output.md)를 참조하세요.
- Batch 서비스 API를 사용하여 출력 데이터를 유지하는 방법에 대한 내용은 [Batch 서비스 API를 사용하여 Azure Storage에 태스크 데이터 유지](batch-task-output-files.md)를 참조하세요.

[forum_post]: https://social.msdn.microsoft.com/Forums/en-US/87b19671-1bdf-427a-972c-2af7e5ba82d9/installing-applications-and-staging-data-on-batch-compute-nodes?forum=azurebatch
[github_file_conventions]: https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Batch/FileConventions
[github_file_conventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
[net_batchclient]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.batchclient.aspx
[net_cloudjob]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudjob.aspx
[net_cloudstorageaccount]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.cloudstorageaccount.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudtask.aspx
[net_fileconventions_readme]: https://github.com/Azure/azure-sdk-for-net/blob/AutoRest/src/Batch/FileConventions/README.md
[net_joboutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputkind.aspx
[net_joboutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.aspx
[net_joboutputstorage_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.joboutputstorage.saveasync.aspx
[net_msdn]: https://msdn.microsoft.com/library/azure/mt348682.aspx
[net_prepareoutputasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.cloudjobextensions.prepareoutputstorageasync.aspx
[net_saveasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.saveasync.aspx
[net_savetrackedasync]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.savetrackedasync.aspx
[net_taskoutputkind]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputkind.aspx
[net_taskoutputstorage]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.conventions.files.taskoutputstorage.aspx
[nuget_manager]: https://docs.nuget.org/consume/installing-nuget
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[portal]: https://portal.azure.com
[storage_explorer]: https://storageexplorer.com/

[1]: ./media/batch-task-output/task-output-01.png "포털의 저장된 출력 파일 및 저장된 로그 선택기"
[2]: ./media/batch-task-output/task-output-02.png "Azure Portal의 태스크 출력 블레이드"
