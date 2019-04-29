---
title: Batch 서비스 API를 사용하여 Azure Storage에 작업 및 태스크 출력 유지 - Azure Batch | Microsoft Docs
description: Batch 서비스 API를 사용하여 Azure Storage에 Batch 작업 및 태스크 출력을 유지하는 방법을 알아봅니다.
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 03/05/2019
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: 1d2d53213af34377d23c9ea140bab15822fc1b2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60554714"
---
# <a name="persist-task-data-to-azure-storage-with-the-batch-service-api"></a>Batch 서비스 API를 사용하여 Azure Storage에 태스크 데이터 유지

[!INCLUDE [batch-task-output-include](../../includes/batch-task-output-include.md)]

Batch 서비스 API에서 가상 머신 구성으로 풀에서 실행되는 태스크 및 작업 관리자 태스크의 출력 데이터를 Azure Storage에 유지하도록 지원합니다. 태스크를 추가할 때 Azure Storage의 컨테이너를 태스크 출력의 대상으로 지정할 수 있습니다. 그런 다음 태스크가 완료되면 Batch 서비스에서 해당 컨테이너에 출력 데이터를 씁니다.

Batch 서비스 API를 사용하여 태스크 출력을 유지하는 이점은 태스크가 실행되는 애플리케이션을 수정할 필요가 없다는 것입니다. 대신 클라이언트 응용 프로그램을 약간만 수정하면 태스크를 만드는 동일한 코드 내에서 태스크 출력을 유지할 수 있습니다.

## <a name="when-do-i-use-the-batch-service-api-to-persist-task-output"></a>Batch 서비스 API를 사용하여 태스크 출력을 유지하는 경우는?

Azure Batch는 태스크 출력을 유지하는 한 가지 이상의 방법을 제공합니다. Batch 서비스 API를 사용하는 것은 다음 시나리오에 가장 적합한 편리한 방식입니다.

- 태스크가 실행되는 애플리케이션을 수정하지 않고 클라이언트 애플리케이션 내에서 태스크 출력을 유지하는 코드를 작성하려고 합니다.
- 가상 컴퓨터 구성으로 만든 풀에서 Batch 태스크 및 작업 관리자 태스크의 출력을 유지하려고 합니다.
- Azure Storage 컨테이너에 임의의 이름으로 출력을 유지하려고 합니다.
- [Batch 파일 규칙 표준](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)(영문)에 따라 명명된 Azure Storage 컨테이너에 출력을 유지하려고 합니다. 

위에서 나열한 시나리오와 다른 시나리오이면 다른 방법을 고려해야 할 수도 있습니다. 예를 들어 Batch 서비스 API는 현재 태스크가 실행되는 동안 Azure Storage에 대한 스트리밍 출력을 지원하지 않습니다. 출력을 스트리밍하려면 .NET에서 사용할 수 있는 Batch 파일 규칙 라이브러리를 사용하는 것이 좋습니다. 다른 언어의 경우 사용자 고유의 자체 솔루션을 구현해야 합니다. 태스크 출력을 유지하기 위한 다른 옵션에 대한 자세한 내용은 [Azure Storage에 작업 및 태스크 출력 유지](batch-task-output.md)를 참조하세요.

## <a name="create-a-container-in-azure-storage"></a>Azure Storage에 컨테이너 만들기

Azure Storage에 태스크 출력을 유지하려면 출력 파일의 대상으로 사용할 컨테이너를 만들어야 합니다. 태스크를 실행하기 전에, 오히려 작업을 제출하기 전에 컨테이너를 만드세요. 컨테이너를 만들려면 적절한 Azure Storage 클라이언트 라이브러리 또는 SDK를 사용합니다. Azure Storage API에 대한 자세한 내용은 [Azure Storage 설명서](https://docs.microsoft.com/azure/storage/)를 참조하세요.

예를 들어 C#으로 애플리케이션을 작성하는 경우 [.NET용 Azure Storage 클라이언트 라이브러리](https://www.nuget.org/packages/WindowsAzure.Storage/)를 사용합니다. 다음 예제에서는 컨테이너를 만드는 방법을 보여 줍니다.

```csharp
CloudBlobContainer container = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
await container.CreateIfNotExists();
```

## <a name="get-a-shared-access-signature-for-the-container"></a>컨테이너에 대한 공유 액세스 서명 가져오기

컨테이너를 만든 후에는 컨테이너에 대한 쓰기 액세스 권한이 있는 SAS(공유 액세스 서명)를 가져옵니다. SAS는 컨테이너에 대해 위임된 액세스를 제공합니다. SAS는 지정된 권한 집합 및 지정된 시간 간격으로 액세스 권한을 부여합니다. Batch 서비스에는 컨테이너에 태스크 출력을 기록하는 쓰기 권한이 있는 SAS가 필요합니다. SAS에 대한 자세한 내용은 [Azure Storage에서 \(SAS\)(공유 액세스 서명) 사용](../storage/common/storage-dotnet-shared-access-signature-part-1.md)을 참조하세요.

Azure Storage API를 사용하여 SAS를 가져오면 API에서 SAS 토큰 문자열을 반환합니다. 이 토큰 문자열에는 SAS가 유효한 권한과 간격을 포함하여 SAS의 모든 매개 변수가 포함됩니다. SAS를 사용하여 Azure Storage의 컨테이너에 액세스하려면 SAS 토큰 문자열을 리소스 URI에 추가해야 합니다. 리소스 URI는 추가된 SAS 토큰과 함께 Azure Storage에 대해 인증된 액세스를 제공합니다.

다음 예제에서는 컨테이너에 대한 쓰기 전용 SAS 토큰 문자열을 가져온 다음 컨테이너 URI에 SAS를 추가하는 방법을 보여 줍니다.

```csharp
string containerSasToken = container.GetSharedAccessSignature(new SharedAccessBlobPolicy()
{
    SharedAccessExpiryTime = DateTimeOffset.UtcNow.AddDays(1),
    Permissions = SharedAccessBlobPermissions.Write
});

string containerSasUrl = container.Uri.AbsoluteUri + containerSasToken;
```

## <a name="specify-output-files-for-task-output"></a>태스크 출력에 대한 출력 파일 지정

태스크에 대한 출력 파일을 지정하려면 [OutputFile](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile) 개체의 컬렉션을 만들고 태스크를 만들 때 [CloudTask.OutputFiles](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.cloudtask.outputfiles#Microsoft_Azure_Batch_CloudTask_OutputFiles) 속성에 이 컬렉션을 할당합니다.

다음 C# 코드 예제에서는 `output.txt`라는 파일에 임의의 숫자를 쓰는 태스크를 만듭니다. 이 예제에서는 `output.txt`라는 출력 파일을 만들어 컨테이너에 씁니다. 또한 `std*.txt` 파일 패턴(_, 예:_ , `stdout.txt` 및 `stderr.txt`)과 일치하는 모든 로그 파일에 대한 출력 파일도 만듭니다. 컨테이너 URL에는 이전에 컨테이너에 대해 만든 SAS가 필요합니다. Batch 서비스에서는 이 SAS를 사용하여 컨테이너에 대한 액세스를 인증합니다.

```csharp
new CloudTask(taskId, "cmd /v:ON /c \"echo off && set && (FOR /L %i IN (1,1,100000) DO (ECHO !RANDOM!)) > output.txt\"")
{
    OutputFiles = new List<OutputFile>
    {
        new OutputFile(
            filePattern: @"..\std*.txt",
            destination: new OutputFileDestination(
         new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId)),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
        new OutputFile(
            filePattern: @"output.txt",
            destination: 
         new OutputFileDestination(new OutputFileBlobContainerDestination(
                    containerUrl: containerSasUrl,
                    path: taskId + @"\output.txt")),
            uploadOptions: new OutputFileUploadOptions(
            uploadCondition: OutputFileUploadCondition.TaskCompletion)),
}
```

### <a name="specify-a-file-pattern-for-matching"></a>일치시킬 파일 패턴 지정

출력 파일을 지정할 때 [OutputFile.FilePattern](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfile.filepattern#Microsoft_Azure_Batch_OutputFile_FilePattern) 속성을 사용하여 일치시킬 파일 패턴을 지정할 수 있습니다. 파일 패턴은 0개 파일, 단일 파일 또는 태스크에서 만든 파일 집합과 일치할 수 있습니다.

**FilePattern** 속성은 `*`(비재귀 일치의 경우) 및 `**`(재귀 일치의 경우)와 같은 표준 파일 시스템 와일드카드를 지원합니다. 예를 들어 위의 코드 샘플에서는 `std*.txt`와 비일치하는 파일 패턴을 지정합니다.

`filePattern: @"..\std*.txt"`

단일 파일을 업로드하려면 와일드카드 없이 파일 패턴을 지정합니다. 예를 들어 위의 코드 샘플에서는 `output.txt`와 비재귀적으로 일치하는 파일 패턴을 지정합니다.

`filePattern: @"output.txt"`

### <a name="specify-an-upload-condition"></a>업로드 조건 지정

[OutputFileUploadOptions.UploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileuploadoptions.uploadcondition#Microsoft_Azure_Batch_OutputFileUploadOptions_UploadCondition) 속성은 출력 파일의 조건부 업로드를 허용합니다. 일반적인 시나리오는 태스크가 성공하면 한 파일 집합을 업로드하고, 실패하면 다른 파일 집합을 업로드하는 것입니다. 예를 들어 태스크가 실패하고 0이 아닌 종료 코드로 종료되는 경우에만 자세한 로그 파일을 업로드하는 것이 좋습니다. 마찬가지로 태스크가 실패하는 경우 파일이 없거나 불완전할 수 있으므로, 태스크가 성공한 경우에만 결과 파일을 업로드하는 것이 좋습니다.

위의 코드 샘플에서는 **UploadCondition** 속성을 **TaskCompletion**으로 설정합니다. 이 설정은 종료 코드의 값에 관계 없이 태스크가 완료된 후에 파일을 업로드하도록 지정합니다.

`uploadCondition: OutputFileUploadCondition.TaskCompletion`

다른 설정은 [OutputFileUploadCondition](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.common.outputfileuploadcondition) 열거형을 참조하세요.

### <a name="disambiguate-files-with-the-same-name"></a>이름이 같은 파일의 구분

작업의 태스크에서 이름이 같은 파일을 만들 수 있습니다. 예를 들어 `stdout.txt` 및 `stderr.txt`는 작업에서 실행되는 모든 태스크에 대해 만들어집니다. 태스크마다 자체의 컨텍스트에서 실행되므로 이러한 파일은 노드의 파일 시스템에서 충돌하지 않습니다. 그러나 여러 태스크의 파일을 공유 컨테이너에 업로드할 때는 이름이 같은 파일을 명확하게 구분해야 합니다.

[OutputFileBlobContainerDestination.Path](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination.path#Microsoft_Azure_Batch_OutputFileBlobContainerDestination_Path) 속성은 출력 파일에 대한 대상 Blob 또는 가상 디렉터리를 지정합니다. **Path** 속성을 사용하여 이름이 같은 출력 파일이 Azure Storage에서 고유한 이름으로 지정되는 방식으로 Blob 또는 가상 디렉터리의 이름을 지정할 수 있습니다. 고유한 이름을 지정하고 파일을 쉽게 식별할 수 있도록 경로에 태스크 ID를 사용하는 것이 좋습니다.

**FilePattern** 속성이 와일드카드 식으로 설정되면 해당 패턴과 일치하는 모든 파일이 **Path** 속성으로 지정된 가상 디렉터리에 업로드됩니다. 예를 들어 컨테이너가 `mycontainer`이고 태스크 ID가 `mytask`이고 파일 패턴이 `..\std*.txt`인 경우, Azure Storage의 출력 파일에 대한 절대 URI는 다음과 비슷합니다.

```
https://myaccount.blob.core.windows.net/mycontainer/mytask/stderr.txt
https://myaccount.blob.core.windows.net/mycontainer/mytask/stdout.txt
```

**FilePattern** 속성이 단일 파일 이름과 일치하도록 설정되면(와일드카드 문자가 포함되지 않음) **Path** 속성의 값은 정규화된 Blob 이름을 지정합니다. 여러 태스크에서 단일 파일과 이름이 충돌할 것으로 예상되는 경우 가상 디렉터리의 이름을 파일 이름의 일부로 포함하여 해당 파일을 구분합니다. 예를 들어 태스크 ID, 구분 기호 문자(일반적으로 슬래시) 및 파일 이름을 포함하도록 **Path** 속성을 설정합니다.

`path: taskId + @"/output.txt"`

태스크 집합의 출력 파일에 대한 절대 URI는 다음과 비슷합니다.

```
https://myaccount.blob.core.windows.net/mycontainer/task1/output.txt
https://myaccount.blob.core.windows.net/mycontainer/task2/output.txt
```

Azure Storage의 가상 디렉터리에 대한 자세한 내용은 [컨테이너의 Blob 나열](../storage/blobs/storage-quickstart-blobs-dotnet.md#list-the-blobs-in-a-container)에 있는 Blob 나열을 참조하세요.

## <a name="diagnose-file-upload-errors"></a>파일 업로드 오류 진단

Azure Storage에 대한 출력 파일 업로드가 실패하면 태스크가 **완료됨** 상태로 전환되고 [TaskExecutionInformation.FailureInformation](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.taskexecutioninformation.failureinformation#Microsoft_Azure_Batch_TaskExecutionInformation_FailureInformation) 속성이 설정됩니다. **FailureInformation** 속성을 검사하여 발생한 오류를 확인합니다. 예를 들어 컨테이너가 없는 경우 파일 업로드에서 발생하는 오류는 다음과 같습니다.

```
Category: UserError
Code: FileUploadContainerNotFound
Message: One of the specified Azure container(s) was not found while attempting to upload an output file
```

모든 파일 업로드에서 Batch는 두 개의 로그 파일을 `fileuploadout.txt` 및 `fileuploaderr.txt` 계산 노드에 씁니다. 이러한 로그 파일을 검사하여 특정 오류에 대해 자세히 확인할 수 있습니다. 예를 들어 태스크 자체가 실행되지 않아 파일 업로드를 시도할 수 없는 경우에는 이러한 로그 파일이 존재하지 않습니다.

## <a name="diagnose-file-upload-performance"></a>파일 업로드 성능 진단

`fileuploadout.txt` 파일은 업로드 진행률을 기록합니다. 이 파일을 검사하면 파일 업로드에 소요된 시간에 대해 자세히 확인할 수 있습니다. 노드 크기, 업로드 시 노드의 다른 활동, 대상 컨테이너가 Batch 풀과 동일한 지역에 있는지 여부, 저장소 계정에 동시에 업로드되는 노드 수 등 업로드 성능에 영향을 주는 많은 요인들이 있음에 유의하세요.

## <a name="use-the-batch-service-api-with-the-batch-file-conventions-standard"></a>Batch 파일 규칙 표준과 함께 Batch 서비스 API 사용

Batch 서비스 API를 사용하여 태스크 출력을 유지할 때 원하는 대로 대상 컨테이너 및 Blob의 이름을 지정할 수 있습니다. 또한 [Batch 파일 규칙 표준](https://github.com/Azure/azure-sdk-for-net/tree/psSdkJson6/src/SDKs/Batch/Support/FileConventions#conventions)(영문)에 따라 이름을 지정하도록 선택할 수도 있습니다. 파일 규칙 표준에서는 지정된 출력 파일에 대한 Azure Storage 대상 컨테이너 및 Blob의 이름을 작업 및 태스크의 이름을 기반으로 하여 결정합니다. 파일 규칙 표준을 사용하여 출력 파일 이름을 지정하면 [Azure Portal](https://portal.azure.com)에서 출력 파일을 볼 수 있습니다.

C#에서 개발하는 경우 [.NET용 Batch 파일 규칙 라이브러리](https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files)(영문)에 기본 제공된 메서드를 사용할 수 있습니다. 이 라이브러리는 적절하게 명명된 컨테이너와 Blob 경로를 만듭니다. 예를 들어 API를 호출하여 작업 이름에 기반한 올바른 컨테이너 이름을 가져올 수 있습니다.

```csharp
string containerName = job.OutputStorageContainerName();
```

[CloudJobExtensions.GetOutputStorageContainerUrl](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.conventions.files.cloudjobextensions.getoutputstoragecontainerurl) 메서드를 사용하여 컨테이너에 쓰는 데 사용되는 SAS(공유 액세스 서명) URL을 반환할 수 있습니다. 그런 다음 이 SAS를 [OutputFileBlobContainerDestination](https://docs.microsoft.com/dotnet/api/microsoft.azure.batch.outputfileblobcontainerdestination) 생성자에 전달할 수 있습니다.

C# 이외의 언어로 개발하는 경우 파일 규칙 표준을 직접 구현해야 합니다.

## <a name="code-sample"></a>코드 샘플

[PersistOutputs][github_persistoutputs] 샘플 프로젝트는 GitHub의 [Azure Batch 코드 샘플][github_samples] 중 하나입니다. 이 Visual Studio 솔루션에서는 .NET용 Batch 클라이언트 라이브러리를 사용하여 영구 저장소에 태스크 출력을 유지하는 방법을 보여 줍니다. 샘플을 실행하려면 다음 단계를 수행합니다.

1. **Visual Studio 2017**에서 프로젝트를 엽니다.
2. Microsoft.Azure.Batch.Samples.Common 프로젝트에서 Batch 및 Storage **계정 자격 증명**을 **AccountSettings.settings**에 추가합니다.
3. **빌드** 합니다(하지만 실행하지 않음). 메시지가 표시되면 모든 NuGet 패키지를 복원합니다.
4. Azure 포털을 사용하여 [PersistOutputsTask](batch-application-packages.md) 에 대한 **애플리케이션 패키지**를 업로드합니다. `PersistOutputsTask.exe` 및 종속 어셈블리를 .zip 패키지에 포함하고, 애플리케이션 ID를 "PersistOutputsTask"로, 애플리케이션 패키지 버전을 "1.0"으로 설정합니다.
5. **PersistOutputs** 프로젝트를 **시작**(실행)합니다.
6. 샘플을 실행하는 데 사용할 지속성 기술을 선택하라는 메시지가 표시될 때 Batch 서비스 API를 통해 샘플을 실행하여 태스크 출력을 유지하려면 **2**를 입력합니다.
7. 원하는 경우 샘플을 다시 실행하고 **3**을 입력하여 Batch 서비스 API를 통해 출력을 유지하고 파일 규칙 표준에 따라 대상 컨테이너와 Blob 경로의 이름도 지정합니다.

## <a name="next-steps"></a>다음 단계

- .NET용 파일 규칙 라이브러리를 사용하여 태스크 출력을 유지하는 방법에 대한 자세한 내용은 [.NET용 Batch 파일 규칙 라이브러리를 사용하여 Azure Storage에 작업 및 태스크 데이터 유지](batch-task-output-file-conventions.md)를 참조하세요.
- Azure Batch에서 출력 데이터를 유지하는 다른 방법에 대한 자세한 내용은 [Azure Storage에 작업 및 태스크 출력 유지](batch-task-output.md)를 참조하세요.

[github_persistoutputs]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/ArticleProjects/PersistOutputs
[github_samples]: https://github.com/Azure/azure-batch-samples
