---
title: '빠른 시작: Azure Blob 스토리지 라이브러리 v12 - Xamarin'
description: 이 빠른 시작에서는 Xamarin이 포함된 Azure Blob 스토리지 클라이언트 라이브러리버전 12를 사용하여 Blob(개체) 스토리지에서 컨테이너 및 Blob을 만드는 방법을 알아봅니다. 그런 다음, BLOB을 모바일 디바이스에 다운로드하는 방법과 컨테이너의 모든 BLOB을 나열하는 방법을 알아봅니다.
author: codemillmatt
ms.author: masoucou
ms.date: 05/08/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 4fa7ebc71f2bbe6abe6956ad36daaf983e011a94
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89001330"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-with-xamarin"></a>빠른 시작: Xamarin이 포함된 Azure Blob 스토리지 클라이언트 라이브러리 v12

Xamarin이 포함된 Azure Blob 스토리지 클라이언트 라이브러리 v12 시작 Azure Blob Storage는 클라우드를 위한 Microsoft의 개체 스토리지 솔루션입니다. 단계에 따라 패키지를 설치하고 기본 작업을 위한 예제 코드를 사용해 봅니다. Blob Storage는 대량의 비정형 데이터를 저장하도록 최적화되어 있습니다.

Xamarin이 포함된 Azure Blob 스토리지 클라이언트 라이브러리 v12를 사용하여 다음을 수행합니다.

* 컨테이너 만들기
* Azure Storage에 Blob 업로드
* 컨테이너의 모든 blob 나열
* 디바이스에 BLOB 다운로드
* 컨테이너 삭제

참조 링크:

* [API 참조 설명서](/dotnet/api/azure.storage.blobs)
* [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs)
* [패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs)
* [샘플](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
* Azure Storage 계정 - [스토리지 계정 만들기](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* [Mobile Development for .NET 워크로드](https://docs.microsoft.com/xamarin/get-started/installation/?pivots=windows)가 설치된 Visual Studio 또는 [Mac용 Visual Studio](https://docs.microsoft.com/visualstudio/mac/installation?view=vsmac-2019)

## <a name="setting-up"></a>설치
    
이 섹션에서는 Xamarin이 포함된 Azure Blob 스토리지 클라이언트 라이브러리 v12를 사용하기 위한 프로젝트 준비 과정을 안내합니다.
    
### <a name="create-the-project"></a>프로젝트 만들기

1. Visual Studio를 열고 빈 Forms 앱을 만듭니다.
1. 이름을 BlobQuickstartV12로 지정합니다.

### <a name="install-the-package"></a>패키지 설치

1. 솔루션 탐색기 창에서 해당 솔루션을 마우스 오른쪽 단추로 클릭하고 **솔루션에 대한 NuGet 패키지 관리**를 선택합니다.
1. **Azure.Storage.Blobs**를 검색하고 솔루션의 모든 프로젝트에 안정적인 최신 버전을 설치합니다.

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

**BlobQuickstartV12** 디렉터리에서 다음을 수행합니다.

1. 편집기에서 *MainPage.xaml* 파일을 엽니다.
1. `<ContentPage></ContentPage>` 요소 사이에 있는 모든 항목을 제거하고 아래와 같이 바꿉니다.

```xaml
<StackLayout HorizontalOptions="Center" VerticalOptions="Center">

    <Button x:Name="uploadButton" Text="Upload Blob" Clicked="Upload_Clicked"  IsEnabled="False"/>
    <Button x:Name="listButton" Text="List Blobs" Clicked="List_Clicked"  IsEnabled="False" />
    <Button x:Name="downloadButton" Text="Download Blob" Clicked="Download_Clicked"  IsEnabled="False" />
    <Button x:Name="deleteButton" Text="Delete Container" Clicked="Delete_Clicked" IsEnabled="False" />

    <Label Text="" x:Name="resultsLabel" HorizontalTextAlignment="Center" Margin="0,20,0,0" TextColor="Red" />
        
</StackLayout>
```

[!INCLUDE [storage-quickstart-credentials-xamarin-include](../../../includes/storage-quickstart-credentials-xamarin-include.md)]

## <a name="object-model"></a>개체 모델

Azure Blob Storage는 대량의 비정형 데이터를 저장하도록 최적화되어 있습니다. 비정형 데이터는 텍스트 또는 이진 데이터와 같은 특정 데이터 모델 또는 정의를 따르지 않는 데이터입니다. Blob Storage는 다음 세 가지 유형의 리소스를 제공합니다.

* 스토리지 계정
* 스토리지 계정의 컨테이너
* 컨테이너의 blob

다음 다이어그램에서는 이러한 리소스 간의 관계를 보여줍니다.

![Blob Storage 아키텍처 다이어그램](./media/storage-blobs-introduction/blob1.png)

다음 .NET 클래스를 사용하여 이러한 리소스와 상호 작용합니다.

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient): `BlobServiceClient` 클래스를 사용하여 Azure Storage 리소스 및 blob 컨테이너를 조작할 수 있습니다.
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient): `BlobContainerClient` 클래스를 사용하여 Azure Storage 컨테이너 및 해당 blob을 조작할 수 있습니다.
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient): `BlobClient` 클래스를 사용하여 Azure Storage blob을 조작할 수 있습니다.
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo): `BlobDownloadInfo` 클래스는 blob 다운로드에서 반환된 속성 및 콘텐츠를 나타냅니다.

## <a name="code-examples"></a>코드 예제

이 예제 코드 조각은 Xamarin.Forms 앱에서 .NET용 Azure Blob 스토리지 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여 줍니다.

* [클래스 수준 변수 만들기](#create-class-level-variables)
* [컨테이너 만들기](#create-a-container)
* [컨테이너에 Blob 업로드](#upload-blobs-to-a-container)
* [컨테이너의 Blob 나열](#list-the-blobs-in-a-container)
* [Blob 다운로드](#download-blobs)
* [컨테이너 삭제](#delete-a-container)

### <a name="create-class-level-variables"></a>클래스 수준 변수 만들기

아래 코드는 여러 클래스 수준 변수를 선언합니다. 이 샘플의 나머지 부분에서 Azure Blob 스토리지와 통신하는 데 필요합니다.

다음 코드는 [스토리지 연결 문자열 구성](#configure-your-storage-connection-string) 섹션에서 설정한 스토리지 계정의 연결 문자열에 더해서 추가로 필요합니다.

다음 코드를 *MainPage.xaml.cs* 파일 안에 클래스 수준 변수로 추가합니다.

```csharp
string storageConnectionString = "{set in the Configure your storage connection string section}";
string fileName = $"{Guid.NewGuid()}-temp.txt";

BlobServiceClient client;
BlobContainerClient containerClient;
BlobClient blobClient;
```

### <a name="create-a-container"></a>컨테이너 만들기

새 컨테이너의 이름을 결정합니다. 아래 코드는 컨테이너 이름이 고유해지도록 이름에 GUID 값을 추가합니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름 지정에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) 클래스의 인스턴스를 만듭니다. 그런 다음, [CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) 메서드를 호출하여 스토리지 계정에 컨테이너를 만듭니다.

*MainPage.xaml.cs* 파일에 이 코드를 추가합니다.

```csharp
protected async override void OnAppearing()
{            
    string containerName = $"quickstartblobs{Guid.NewGuid()}";
    
    client = new BlobServiceClient(storageConnectionString);
    containerClient = await client.CreateBlobContainerAsync(containerName);

    resultsLabel.Text = "Container Created\n";

    blobClient = containerClient.GetBlobClient(fileName);

    uploadButton.IsEnabled = true;
}
```

### <a name="upload-blobs-to-a-container"></a>컨테이너에 Blob 업로드

다음 코드 조각을 실행합니다.

1. `MemoryStream` 텍스트를 만듭니다.
1. [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient) 클래스의 [UploadAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.uploadblobasync?view=azure-dotnet#Azure_Storage_Blobs_BlobContainerClient_UploadBlobAsync_System_String_System_IO_Stream_System_Threading_CancellationToken_) 함수를 호출하여 파일 이름과 `MemoryStream` 텍스트 모두에 전달하여 텍스트를 Blob에 업로드합니다. 이 메서드는 Blob이 없는 경우 만들고, Blob이 있는 경우 덮어씁니다.

*MainPage.xaml.cs* 파일에 다음 코드를 추가합니다.

```csharp
async void Upload_Clicked(object sender, EventArgs e)
{                                    
    using MemoryStream memoryStream = new MemoryStream(Encoding.UTF8.GetBytes("Hello World!"));

    await containerClient.UploadBlobAsync(fileName, memoryStream);

    resultsLabel.Text += "Blob Uploaded\n";

    uploadButton.IsEnabled = false;
    listButton.IsEnabled = true;
}
```

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) 메서드를 호출하여 컨테이너의 blob을 나열합니다. 이 경우 하나의 Blob만 컨테이너에 추가되었으므로 나열된 작업은 하나의 해당 Blob만 반환합니다.

*MainPage.xaml.cs* 파일에 다음 코드를 추가합니다.

```csharp
async void List_Clicked(object sender, EventArgs e)
{            
    await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
    {
        resultsLabel.Text += blobItem.Name + "\n";                
    }

    listButton.IsEnabled = false;
    downloadButton.IsEnabled = true;
}
```

### <a name="download-blobs"></a>Blob 다운로드

[DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) 메서드를 호출하여 이전에 만든 blob을 다운로드합니다. 예제 코드는 텍스트를 표시할 수 있도록 BLOB의 `Stream` 표현을 `MemoryStream`에 먼저 복사한 다음, `StreamReader`에 복사합니다.

*MainPage.xaml.cs* 파일에 다음 코드를 추가합니다.

```csharp
async void Download_Clicked(object sender, EventArgs e)
{
    BlobDownloadInfo downloadInfo = await blobClient.DownloadAsync();

    using MemoryStream memoryStream = new MemoryStream();
    
    await downloadInfo.Content.CopyToAsync(memoryStream);
    memoryStream.Position = 0;

    using StreamReader streamReader = new StreamReader(memoryStream);

    resultsLabel.Text += "Blob Contents: \n";
    resultsLabel.Text += await streamReader.ReadToEndAsync();
    resultsLabel.Text += "\n";

    downloadButton.IsEnabled = false;
    deleteButton.IsEnabled = true;
}
```

### <a name="delete-a-container"></a>컨테이너 삭제

다음 코드는 [DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync)를 사용하여 전체 컨테이너를 삭제하여 앱이 만든 리소스를 정리합니다.

앱에서 BLOB 및 컨테이너를 삭제하기 전에 확인 메시지를 표시합니다. 리소스를 삭제하기 전에 올바르게 만들어졌는지 확인하는 것이 좋습니다.

*MainPage.xaml.cs* 파일에 다음 코드를 추가합니다.

```csharp
async void Delete_Clicked(object sender, EventArgs e)
{            
    var deleteContainer = await Application.Current.MainPage.DisplayAlert("Delete Container",
        "You are about to delete the container proceeed?", "OK", "Cancel");

    if (deleteContainer == false)
        return;

    await containerClient.DeleteAsync();

    resultsLabel.Text += "Container Deleted";

    deleteButton.IsEnabled = false;
}
```

## <a name="run-the-code"></a>코드 실행

앱이 시작되고, 가장 먼저 앱은 표시되는 컨테이너를 만듭니다. 그러면 사용자는 단추를 클릭하여 BLOB을 업로드, 나열, 다운로드하고 컨테이너를 삭제해야 합니다.

Windows에서 앱을 실행하려면 F5 키를 누릅니다. Mac에서 앱을 실행하려면 Cmd+Enter 키를 누릅니다.

앱은 작업이 끝날 때마다 화면에 기록합니다. 앱의 출력은 아래 예제와 비슷합니다.

```output
Container Created
Blob Uploaded
98d9a472-8e98-4978-ba4f-081d69d2e6f8-temp.txt
Blob Contents:
Hello World!
Container Deleted
```

정리 프로세스를 시작하기 전에, 화면의 BLOB 출력 내용이 업로드된 값과 일치하는지 확인합니다.

값을 확인한 후에는 컨테이너를 삭제하는 메시지를 확인하고 데모를 마칩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Xamarin이 포함된 Azure Blob 스토리지 클라이언트 라이브러리 v12를 사용하여 BLOB을 업로드, 다운로드 및 나열하는 방법을 알아보았습니다.

Blob 스토리지 샘플 앱을 보려면 다음을 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Blob 스토리지 SDK v12 Xamarin 샘플](https://github.com/Azure-Samples/storage-blobs-xamarin-quickstart)

* 자습서, 샘플, 빠른 시작 및 기타 설명서는 [모바일 개발자용 Azure](/azure/mobile-apps)를 방문하세요.
* Xamarin에 대한 자세한 내용은 [Xamarin 시작](/xamarin/get-started/)을 참조하세요.
