---
title: .NET 용 데이터 이동 라이브러리를 사용 하 여 데이터 전송
titleSuffix: Azure Storage
description: 데이터 이동 라이브러리를 사용 하 여 blob 및 파일 콘텐츠에서 데이터를 이동 하거나 복사 합니다. 로컬 파일에서 Azure Storage로 데이터를 복사하거나, Storage 계정 내에서 데이터를 복사하거나, Storage 계정 간에 데이터를 복사합니다. 데이터를 Azure Storage로 손쉽게 마이그레이션할 수 있습니다.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: how-to
ms.date: 06/16/2020
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 8ec35a651d4d3ef9e0877463329a654bc7491f4c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001879"
---
# <a name="transfer-data-with-the-data-movement-library"></a>데이터 이동 라이브러리를 사용하여 데이터 전송

Azure Storage 데이터 이동 라이브러리는 blob 및 파일의 고성능 업로드, 다운로드 및 복사를 위해 설계 된 플랫폼 간 오픈 소스 라이브러리입니다. 데이터 이동 라이브러리는 .NET 용 Azure Storage 클라이언트 라이브러리에서 사용할 수 없는 편리한 메서드를 제공 합니다. 이러한 메서드는 병렬 작업의 수를 설정 하 고, 전송 진행률을 추적 하 고, 취소 된 전송을 쉽게 다시 시작할 수 있는 기능을 제공 합니다.

이 라이브러리는 .NET Core를 사용하기 때문에 Windows, Linux 및 macOS용 .NET 앱을 빌드할 때 사용할 수 있습니다. .NET Core에 대한 자세한 내용은 [.NET Core 설명서](https://dotnet.github.io/)를 참조하세요. 또한 이 라이브러리는 전통적인 Windows용 .NET Framework 앱에서도 작동합니다.

이 문서에서는 Windows, Linux 및 macOS에서 실행되는 .NET Core 콘솔 애플리케이션을 만들고 다음 시나리오를 수행하는 방법을 보여 줍니다.

- Blob Storage에 파일과 디렉터리를 업로드합니다.
- 데이터를 전송할 때 수행할 병렬 작업 수를 정의합니다.
- 데이터 전송 진행률을 추적합니다.
- 취소된 데이터 전송을 다시 시작합니다.
- URL에서 Blob Storage로 파일을 복사합니다.
- Blob Storage 간에 복사합니다.

## <a name="prerequisites"></a>사전 요구 사항

- [Visual Studio Code](https://code.visualstudio.com/)
- [Azure storage 계정](storage-account-create.md)

## <a name="setup"></a>설치

1. [.NET Core 설치 가이드](https://www.microsoft.com/net/core)를 방문하여 .NET Core를 설치합니다. 사용자 환경을 선택할 때 명령줄 옵션을 선택합니다.
2. 명령줄에서 프로젝트에 대한 디렉터리를 만듭니다. 이 디렉터리로 이동한 다음 `dotnet new console -o <sample-project-name>`를 입력하여 C# 콘솔 프로젝트를 만듭니다.
3. Visual Studio Code에서 이 디렉터리를 엽니다. 이 단계는 Windows에서 `code .`를 입력하여 명령줄을 통해 빠르게 수행할 수 있습니다.
4. Visual Studio Code Marketplace에서 [c # 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) 을 설치 합니다. Visual Studio Code를 다시 시작합니다.
5. 이 시점에서 두 가지 프롬프트가 표시됩니다. 하나는 "빌드 및 디버그에 필요한 자산"을 추가하는 것입니다. "예"를 클릭합니다. 또 다른 프롬프트는 해결되지 않은 종속성을 복원하는 것입니다. "복원"을 클릭합니다.
6. `.vscode` 아래에서 `launch.json`을 수정하여 외부 터미널을 콘솔로 사용합니다. 이 설정은 `"console": "externalTerminal"`로 읽어야 합니다.
7. Visual Studio Code를 사용하면 .NET Core 애플리케이션을 디버그할 수 있습니다. `F5` 키를 눌러 애플리케이션을 실행하고 설정이 작동하는지 확인합니다. 콘솔에 "Hello World!" 가 표시됩니다.

## <a name="add-the-data-movement-library-to-your-project"></a>프로젝트에 데이터 이동 라이브러리 추가

1. 파일의 섹션에 최신 버전의 데이터 이동 라이브러리를 추가 합니다 `dependencies` `<project-name>.csproj` . 이 문서를 작성한 시점에서 해당 버전은 `"Microsoft.Azure.Storage.DataMovement": "0.6.2"`입니다.
2. 프로젝트를 복원하기 위해 프롬프트가 표시됩니다. "복원" 단추를 클릭합니다. 또한 프로젝트 디렉터리의 루트에 `dotnet restore` 명령을 입력하여 명령줄에서 프로젝트를 복원할 수도 있습니다.

다음과 같이 `<project-name>.csproj`를 수정합니다.

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>netcoreapp2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.Azure.Storage.DataMovement" Version="0.6.2" />
        </ItemGroup>
    </Project>
```

## <a name="set-up-the-skeleton-of-your-application"></a>애플리케이션의 기본 구조 설정

가장 먼저 수행할 작업은 애플리케이션의 "기본 구조" 코드를 설정하는 것입니다. 이 코드는 Storage 계정 이름과 계정 키를 묻는 메시지를 표시하고 해당 자격 증명을 사용하여 `CloudStorageAccount` 개체를 만듭니다. 이 개체는 모든 전송 시나리오에서 Storage 계정과 상호 작용하는 데 사용됩니다. 또한 이 코드는 실행하고자 하는 전송 작업의 유형도 선택하도록 요청합니다.

다음과 같이 `Program.cs`를 수정합니다.

```csharp
using System;
using System.Threading;
using System.Threading.Tasks;
using System.Diagnostics;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.Storage.DataMovement;

namespace DMLibSample
{
    public class Program
    {
        public static void Main()
        {
            Console.WriteLine("Enter Storage account name:");
            string accountName = Console.ReadLine();

            Console.WriteLine("\nEnter Storage account key:");
            string accountKey = Console.ReadLine();

            string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + accountName + ";AccountKey=" + accountKey;
            CloudStorageAccount account = CloudStorageAccount.Parse(storageConnectionString);

            ExecuteChoice(account);
        }

        public static void ExecuteChoice(CloudStorageAccount account)
        {
            Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
            int choice = int.Parse(Console.ReadLine());

            if(choice == 1)
            {
                TransferLocalFileToAzureBlob(account).Wait();
            }
            else if(choice == 2)
            {
                TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
            }
            else if(choice == 3)
            {
                TransferUrlToAzureBlob(account).Wait();
            }
            else if(choice == 4)
            {
                TransferAzureBlobToAzureBlob(account).Wait();
            }
        }

        public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
        {

        }

        public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
        {

        }

        public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
        {

        }
    }
}
```

## <a name="upload-a-local-file-to-a-blob"></a>Blob에 로컬 파일 업로드

다음과 같이 `GetSourcePath`와 `GetBlob` 메서드를 `Program.cs`에 추가합니다.

```csharp
public static string GetSourcePath()
{
    Console.WriteLine("\nProvide path for source:");
    string sourcePath = Console.ReadLine();

    return sourcePath;
}

public static CloudBlockBlob GetBlob(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    Console.WriteLine("\nProvide name of new Blob:");
    string blobName = Console.ReadLine();
    CloudBlockBlob blob = container.GetBlockBlobReference(blobName);

    return blob;
}
```

다음과 같이 `TransferLocalFileToAzureBlob` 메서드를 수정합니다.

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    await TransferManager.UploadAsync(localFilePath, blob);
    Console.WriteLine("\nTransfer operation complete.");
    ExecuteChoice(account);
}
```

이 코드는 로컬 파일의 경로, 새 컨테이너 또는 기존 컨테이너의 이름 및 새 Blob의 이름을 입력하라는 메시지를 표시합니다. `TransferManager.UploadAsync` 메서드에서 이 정보를 사용하여 업로드를 수행합니다.

`F5` 키를 눌러 애플리케이션을 실행합니다. [Microsoft Azure Storage Explorer](https://storageexplorer.com/)로 스토리지 계정을 확인하여 업로드를 수행했는지 확인할 수 있습니다.

## <a name="set-the-number-of-parallel-operations"></a>병렬 작업 수 설정

데이터 이동 라이브러리에서 제공 하는 기능 중 하나는 데이터 전송 처리량을 늘리기 위해 병렬 작업 수를 설정 하는 기능입니다. 기본적으로 데이터 이동 라이브러리는 병렬 작업 수를 8 * 컴퓨터의 코어 수로 설정 합니다.

저대역폭 환경에서는 많은 병렬 작업으로 인해 네트워크 연결에 과부하가 걸릴 수 있으며 실제로 작업이 완전히 완료되지 못할 수 있습니다. 사용 가능한 네트워크 대역폭에 따라 가장 적합한 설정을 결정하려면 해당 설정을 테스트해야 합니다.

병렬 작업 수를 설정할 수 있는 몇 가지 코드를 추가해 보겠습니다. 전송이 완료되는 데 걸리는 시간을 제어하는 코드도 추가해 보겠습니다.

다음과 같이 `SetNumberOfParallelOperations` 메서드를 `Program.cs`에 추가합니다.

```csharp
public static void SetNumberOfParallelOperations()
{
    Console.WriteLine("\nHow many parallel operations would you like to use?");
    string parallelOperations = Console.ReadLine();
    TransferManager.Configurations.ParallelOperations = int.Parse(parallelOperations);
}
```

다음과 같이 `SetNumberOfParallelOperations`를 사용하도록 `ExecuteChoice` 메서드를 수정합니다.

```csharp
public static void ExecuteChoice(CloudStorageAccount account)
{
    Console.WriteLine("\nWhat type of transfer would you like to execute?\n1. Local file --> Azure Blob\n2. Local directory --> Azure Blob directory\n3. URL (e.g. Amazon S3 file) --> Azure Blob\n4. Azure Blob --> Azure Blob");
    int choice = int.Parse(Console.ReadLine());

    SetNumberOfParallelOperations();

    if(choice == 1)
    {
        TransferLocalFileToAzureBlob(account).Wait();
    }
    else if(choice == 2)
    {
        TransferLocalDirectoryToAzureBlobDirectory(account).Wait();
    }
    else if(choice == 3)
    {
        TransferUrlToAzureBlob(account).Wait();
    }
    else if(choice == 4)
    {
        TransferAzureBlobToAzureBlob(account).Wait();
    }
}
```

다음과 같이 타이머를 사용하도록 `TransferLocalFileToAzureBlob` 메서드를 수정합니다.

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    Console.WriteLine("\nTransfer started...");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="track-transfer-progress"></a>전송 진행률 추적

데이터를 전송 하는 데 걸린 시간을 알고 있으면 도움이 됩니다. 그러나 전송 작업 *중* 에 전송 진행 상황을 확인할 수 있으면 훨씬 더 낫습니다. 이 시나리오를 달성하려면 `TransferContext` 개체를 만들어야 합니다. `TransferContext` 개체는 `SingleTransferContext`와 `DirectoryTransferContext`의 두 가지 형식으로 제공됩니다. 전자는 단일 파일을 전송 하기 위한 것이 고, 후자는 파일의 디렉터리를 전송 하기 위한 것입니다.

다음과 같이 `GetSingleTransferContext`와 `GetDirectoryTransferContext` 메서드를 `Program.cs`에 추가합니다.

```csharp
public static SingleTransferContext GetSingleTransferContext(TransferCheckpoint checkpoint)
{
    SingleTransferContext context = new SingleTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}

public static DirectoryTransferContext GetDirectoryTransferContext(TransferCheckpoint checkpoint)
{
    DirectoryTransferContext context = new DirectoryTransferContext(checkpoint);

    context.ProgressHandler = new Progress<TransferStatus>((progress) =>
    {
        Console.Write("\rBytes transferred: {0}", progress.BytesTransferred );
    });

    return context;
}
```

다음과 같이 `GetSingleTransferContext`를 사용하도록 `TransferLocalFileToAzureBlob` 메서드를 수정합니다.

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    Console.WriteLine("\nTransfer started...\n");
    Stopwatch stopWatch = Stopwatch.StartNew();
    await TransferManager.UploadAsync(localFilePath, blob, null, context);
    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

## <a name="resume-a-canceled-transfer"></a>취소된 전송 다시 시작

데이터 이동 라이브러리에서 제공 하는 또 다른 편리한 기능은 취소 된 전송을 다시 시작할 수 있는 기능입니다. `c`를 입력하여 전송을 일시적으로 취소할 수 있는 코드를 추가한 다음 3초 후에 전송을 다시 시작해 보겠습니다.

다음과 같이 `TransferLocalFileToAzureBlob`를 수정합니다.

```csharp
public static async Task TransferLocalFileToAzureBlob(CloudStorageAccount account)
{
    string localFilePath = GetSourcePath();
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.UploadAsync(localFilePath, blob, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadAsync(localFilePath, blob, null, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

지금까지 `checkpoint` 값은 항상 `null`로 설정되어 있었습니다. 이제 전송을 취소하면 전송의 마지막 검사점을 검색한 다음 전송 컨텍스트에서 새로운 이 검사점을 사용합니다.

## <a name="transfer-a-local-directory-to-blob-storage"></a>Blob 저장소에 로컬 디렉터리 전송

데이터 이동 라이브러리에서 한 번에 하나의 파일만 전송할 수 있는 경우에는 disappointing 됩니다. 다행히도 그렇지는 않습니다. 데이터 이동 라이브러리는 파일의 디렉터리와 모든 하위 디렉터리를 전송 하는 기능을 제공 합니다. 이렇게 수행할 수 있는 몇 가지 코드를 추가해 보겠습니다.

먼저 다음과 같이 `GetBlobDirectory` 메서드를 `Program.cs`에 추가합니다.

```csharp
public static CloudBlobDirectory GetBlobDirectory(CloudStorageAccount account)
{
    CloudBlobClient blobClient = account.CreateCloudBlobClient();

    Console.WriteLine("\nProvide name of Blob container. This can be a new or existing Blob container:");
    string containerName = Console.ReadLine();
    CloudBlobContainer container = blobClient.GetContainerReference(containerName);
    container.CreateIfNotExistsAsync().Wait();

    CloudBlobDirectory blobDirectory = container.GetDirectoryReference("");

    return blobDirectory;
}
```

그런 후 다음과 같이 `TransferLocalDirectoryToAzureBlobDirectory`을 수정합니다.

```csharp
public static async Task TransferLocalDirectoryToAzureBlobDirectory(CloudStorageAccount account)
{
    string localDirectoryPath = GetSourcePath();
    CloudBlobDirectory blobDirectory = GetBlobDirectory(account);
    TransferCheckpoint checkpoint = null;
    DirectoryTransferContext context = GetDirectoryTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    UploadDirectoryOptions options = new UploadDirectoryOptions()
    {
        Recursive = true
    };

    try
    {
        task = TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetDirectoryTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.UploadDirectoryAsync(localDirectoryPath, blobDirectory, options, context);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

이 메서드와 단일 파일 업로드 메서드 간에는 몇 가지 차이점이 있습니다. 현재 `TransferManager.UploadDirectoryAsync`와 이전에 만든 `getDirectoryTransferContext` 메서드를 사용하고 있습니다. 또한 업로드 작업에 `options` 값도 제공하므로 업로드 시 하위 디렉터리를 포함하도록 나타낼 수 있습니다.

## <a name="copy-a-file-from-url-to-a-blob"></a>URL에서 blob으로 파일 복사

이제 URL에서 Azure Blob으로 파일을 복사할 수 있는 코드를 추가해 보겠습니다.

다음과 같이 `TransferUrlToAzureBlob`를 수정합니다.

```csharp
public static async Task TransferUrlToAzureBlob(CloudStorageAccount account)
{
    Uri uri = new Uri(GetSourcePath());
    CloudBlockBlob blob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(uri, blob, true, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

이 기능의 중요한 사용 사례 중 하나로 다른 클라우드 서비스(예: AWS)에서 Azure로 데이터를 이동해야 하는 경우가 있습니다. 이때 리소스에 액세스할 수 있는 URL이 있으면 `TransferManager.CopyAsync` 메서드를 사용하여 해당 리소스를 Azure Blob으로 쉽게 이동할 수 있습니다. 또한 이 메서드는 새로운 부울 매개 변수를 사용합니다. 이 매개 변수를 `true`로 설정하면 비동기 서버 쪽 복사를 수행하려고 한다는 것을 나타냅니다. 이 매개 변수를 `false`로 설정하면 리소스를 로컬 시스템에 다운로드한 다음 Azure Blob에 업로드하는 동기 복사를 나타냅니다. 그러나 동기 복사는 현재 한 Azure Storage 리소스에서 다른 Azure Storage 리소스로 복사할 때만 사용할 수 있습니다.

## <a name="copy-a-blob"></a>Blob 복사

데이터 이동 라이브러리에서 고유 하 게 제공 하는 또 다른 기능은 한 Azure Storage 리소스에서 다른 리소스로 복사 하는 기능입니다.

다음과 같이 `TransferAzureBlobToAzureBlob`를 수정합니다.

```csharp
public static async Task TransferAzureBlobToAzureBlob(CloudStorageAccount account)
{
    CloudBlockBlob sourceBlob = GetBlob(account);
    CloudBlockBlob destinationBlob = GetBlob(account);
    TransferCheckpoint checkpoint = null;
    SingleTransferContext context = GetSingleTransferContext(checkpoint);
    CancellationTokenSource cancellationSource = new CancellationTokenSource();
    Console.WriteLine("\nTransfer started...\nPress 'c' to temporarily cancel your transfer...\n");

    Stopwatch stopWatch = Stopwatch.StartNew();
    Task task;
    ConsoleKeyInfo keyinfo;
    try
    {
        task = TransferManager.CopyAsync(sourceBlob, destinationBlob, CopyMethod.ServiceSideAsyncCopy, null, context, cancellationSource.Token);
        while(!task.IsCompleted)
        {
            if(Console.KeyAvailable)
            {
                keyinfo = Console.ReadKey(true);
                if(keyinfo.Key == ConsoleKey.C)
                {
                    cancellationSource.Cancel();
                }
            }
        }
        await task;
    }
    catch(Exception e)
    {
        Console.WriteLine("\nThe transfer is canceled: {0}", e.Message);
    }

    if(cancellationSource.IsCancellationRequested)
    {
        Console.WriteLine("\nTransfer will resume in 3 seconds...");
        Thread.Sleep(3000);
        checkpoint = context.LastCheckpoint;
        context = GetSingleTransferContext(checkpoint);
        Console.WriteLine("\nResuming transfer...\n");
        await TransferManager.CopyAsync(sourceBlob, destinationBlob, false, null, context, cancellationSource.Token);
    }

    stopWatch.Stop();
    Console.WriteLine("\nTransfer operation completed in " + stopWatch.Elapsed.TotalSeconds + " seconds.");
    ExecuteChoice(account);
}
```

이 예제에서는 `TransferManager.CopyAsync`의 부울 매개 변수를 `false`로 설정하여 동기 복사를 수행하려고 한다는 것을 나타냅니다. 즉 리소스를 로컬 시스템에 먼저 다운로드한 다음 Azure Blob으로 업로드합니다. 동기 복사 옵션은 복사 작업을 일관된 속도로 수행하도록 보장하는 좋은 방법입니다. 반면에 비동기 서버 쪽 복사 속도는 서버에서 사용할 수 있는 네트워크 대역폭에 따라 다르며 변동될 수 있습니다. 그러나 동기 복사는 비동기 복사에 비해 추가적인 송신 비용이 발생할 수 있습니다. 원본 스토리지 계정과 동일한 지역에 있는 Azure VM에서 동기 복사를 사용하여 송신 비용이 발생하지 않도록 하는 것이 좋습니다.

이제 데이터 이동 응용 프로그램이 완료 되었습니다. [전체 코드 샘플은 GitHub에서 사용할 수 있습니다](https://github.com/azure-samples/storage-dotnet-data-movement-library-app).

## <a name="next-steps"></a>다음 단계

[데이터 이동 라이브러리 참조 설명서를 Azure Storage](https://azure.github.io/azure-storage-net-data-movement)합니다.

[!INCLUDE [storage-try-azure-tools-blobs](../../../includes/storage-try-azure-tools-blobs.md)]
