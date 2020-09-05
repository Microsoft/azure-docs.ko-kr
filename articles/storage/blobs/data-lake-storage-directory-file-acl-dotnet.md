---
title: Acl & 파일에 대 한 .NET SDK Azure Data Lake Storage Gen2
description: Azure Storage 클라이언트 라이브러리를 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정의 디렉터리 및 파일 및 디렉터리 ACL (액세스 제어 목록)을 관리 합니다.
author: normesta
ms.service: storage
ms.date: 08/26/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 6533809be614210a88bfa605e24209d094bb679d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89019303"
---
# <a name="use-net-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>.NET을 사용 하 여 Azure Data Lake Storage Gen2에서 디렉터리, 파일 및 Acl 관리

이 문서에서는 .NET을 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리 하는 방법을 보여 줍니다. 

[패키지 (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  [API 참조](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)  |  [Gen1 To Gen2 mapping](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>필수 구성 요소

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](data-lake-storage-quickstart-create-account.md)을 수행합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

시작 하려면 [DataLake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) NuGet 패키지를 설치 합니다.

NuGet 패키지를 설치 하는 방법에 대 한 자세한 내용은 [Nuget 패키지 관리자를 사용 하 여 Visual Studio에서 패키지 설치 및 관리](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio)를 참조 하세요.

그런 다음 이러한 using 문을 코드 파일의 맨 위에 추가 합니다.

```csharp
using Azure.Storage.Files.DataLake;
using Azure.Storage.Files.DataLake.Models;
using Azure.Storage;
using System.IO;
using Azure;
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) 인스턴스를 만듭니다.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient,
    string accountName, string accountKey)
{
    StorageSharedKeyCredential sharedKeyCredential =
        new StorageSharedKeyCredential(accountName, accountKey);

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient
        (new Uri(dfsUri), sharedKeyCredential);
}
```

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD)를 사용 하 여 연결

[.Net 용 azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient?) 인스턴스를 만듭니다.  이러한 값을 얻으려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 획득](../common/storage-auth-aad-app.md)을 참조 하세요.

```cs
public void GetDataLakeServiceClient(ref DataLakeServiceClient dataLakeServiceClient, 
    String accountName, String clientID, string clientSecret, string tenantID)
{

    TokenCredential credential = new ClientSecretCredential(
        tenantID, clientID, clientSecret, new TokenCredentialOptions());

    string dfsUri = "https://" + accountName + ".dfs.core.windows.net";

    dataLakeServiceClient = new DataLakeServiceClient(new Uri(dfsUri), credential);
}

```

> [!NOTE]
> 더 많은 예제는 [.net 용 Azure id 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) 설명서를 참조 하세요.

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 파일에 대 한 파일 시스템 역할을 합니다. [DataLakeServiceClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakeserviceclient.createfilesystemasync) 메서드를 호출 하 여 만들 수 있습니다.

이 예제에서는 라는 컨테이너를 만듭니다 `my-file-system` . 

```cs
public async Task<DataLakeFileSystemClient> CreateFileSystem
    (DataLakeServiceClient serviceClient)
{
        return await serviceClient.CreateFileSystemAsync("my-file-system");
}
```

## <a name="create-a-directory"></a>디렉터리 만들기

[DataLakeFileSystemClient. CreateDirectoryAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.createdirectoryasync) 메서드를 호출 하 여 디렉터리 참조를 만듭니다.

이 예제에서는 라는 디렉터리를 `my-directory` 컨테이너에 추가한 다음 라는 하위 디렉터리를 추가 `my-subdirectory` 합니다. 

```cs
public async Task<DataLakeDirectoryClient> CreateDirectory
    (DataLakeServiceClient serviceClient, string fileSystemName)
{
    DataLakeFileSystemClient fileSystemClient =
        serviceClient.GetFileSystemClient(fileSystemName);

    DataLakeDirectoryClient directoryClient =
        await fileSystemClient.CreateDirectoryAsync("my-directory");

    return await directoryClient.CreateSubDirectoryAsync("my-subdirectory");
}
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

[DataLakeDirectoryClient RenameAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.renameasync) 메서드를 호출 하 여 디렉터리 이름을 바꾸거나 이동 합니다. 원하는 디렉터리의 경로를 매개 변수로 전달 합니다. 

이 예에서는 하위 디렉터리의 이름을로 바꿉니다 `my-subdirectory-renamed` .

```cs
public async Task<DataLakeDirectoryClient> 
    RenameDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory");

    return await directoryClient.RenameAsync("my-directory/my-subdirectory-renamed");
}
```

이 예에서는 라는 디렉터리를 `my-subdirectory-renamed` 라는 디렉터리의 하위 디렉터리로 이동 `my-directory-2` 합니다. 

```cs
public async Task<DataLakeDirectoryClient> MoveDirectory
    (DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
            fileSystemClient.GetDirectoryClient("my-directory/my-subdirectory-renamed");

    return await directoryClient.RenameAsync("my-directory-2/my-subdirectory-renamed");                
}
```

## <a name="delete-a-directory"></a>디렉터리 삭제

[DataLakeDirectoryClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.delete) 메서드를 호출 하 여 디렉터리를 삭제 합니다.

다음 예제에서는 `my-directory`라는 디렉터리를 삭제합니다.  

```cs
public void DeleteDirectory(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    directoryClient.Delete();
}
```

## <a name="manage-a-directory-acl"></a>디렉터리 ACL 관리

[DataLakeDirectoryClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) 메서드를 호출 하 고 [DataLakeDirectoryClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) 를 호출 하 여 acl을 설정 하는 방법으로 디렉터리의 acl (액세스 제어 목록)을 가져옵니다.

> [!NOTE]
> 응용 프로그램에서 Azure Active Directory (Azure AD)를 사용 하 여 액세스 권한을 부여 하는 경우 응용 프로그램에서 액세스 권한을 부여 하는 데 사용 하는 보안 주체가 [저장소 Blob 데이터 소유자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 권한이 적용되는 방식과 권한 변경의 영향에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조하세요. 

이 예제에서는 이름이 인 디렉터리의 ACL을 가져오고 설정 합니다 `my-directory` . 문자열은 `user::rwx,group::r-x,other::rw-` 소유 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 및 쓰기 권한을 부여 합니다.

```cs
public async Task ManageDirectoryACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    PathAccessControl directoryAccessControl =
        await directoryClient.GetAccessControlAsync();

    foreach (var item in directoryAccessControl.AccessControlList)
    {
        Console.WriteLine(item.ToString());
    }


    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    directoryClient.SetAccessControlList(accessControlList);

}

```

컨테이너의 루트 디렉터리에 대 한 ACL을 가져오고 설정할 수도 있습니다. 루트 디렉터리를 가져오려면 `""` [DataLakeFileSystemClient client](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) 메서드에 빈 문자열 ()을 전달 합니다.

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

먼저 [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) 클래스의 인스턴스를 만들어 대상 디렉터리에 파일 참조를 만듭니다. [DataLakeFileClient async](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) 메서드를 호출 하 여 파일을 업로드 합니다. [DataLakeFileClient FlushAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.flushasync) 메서드를 호출 하 여 업로드를 완료 해야 합니다.

이 예제에서는 라는 디렉터리에 텍스트 파일을 업로드 `my-directory` 합니다.    

```cs
public async Task UploadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = await directoryClient.CreateFileAsync("uploaded-file.txt");

    FileStream fileStream = 
        File.OpenRead("C:\\file-to-upload.txt");

    long fileSize = fileStream.Length;

    await fileClient.AppendAsync(fileStream, offset: 0);

    await fileClient.FlushAsync(position: fileSize);

}
```

> [!TIP]
> 파일 크기가 큰 경우 코드는 [DataLakeFileClient async](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync)를 여러 번 호출 해야 합니다. 대신 [DataLakeFileClient UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) 메서드를 사용 하는 것이 좋습니다. 이렇게 하면 단일 호출에서 전체 파일을 업로드할 수 있습니다. 
>
> 예제는 다음 섹션을 참조하세요.

## <a name="upload-a-large-file-to-a-directory"></a>디렉터리에 용량이 많은 파일 업로드

[DataLakeFileClient async](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.appendasync) 메서드를 여러 번 호출 하지 않고도 [DataLakeFileClient UploadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.uploadasync?view=azure-dotnet#Azure_Storage_Files_DataLake_DataLakeFileClient_UploadAsync_System_IO_Stream_) 메서드를 사용 하 여 많은 파일을 업로드할 수 있습니다.

```cs
public async Task UploadFileBulk(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = directoryClient.GetFileClient("uploaded-file.txt");

    FileStream fileStream =
        File.OpenRead("C:\\file-to-upload.txt");

    await fileClient.UploadAsync(fileStream);

}

```

## <a name="manage-a-file-acl"></a>파일 ACL 관리

[DataLakeFileClient. GetAccessControlAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) 메서드를 호출 하 고 [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) 를 호출 하 여 acl을 설정 하는 방법으로 파일의 acl (액세스 제어 목록)을 가져옵니다.

> [!NOTE]
> 응용 프로그램에서 Azure Active Directory (Azure AD)를 사용 하 여 액세스 권한을 부여 하는 경우 응용 프로그램에서 액세스 권한을 부여 하는 데 사용 하는 보안 주체가 [저장소 Blob 데이터 소유자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 권한이 적용되는 방식과 권한 변경의 영향에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조하세요. 

이 예제에서는 라는 파일의 ACL을 가져오고 설정 합니다 `my-file.txt` . 문자열은 `user::rwx,group::r-x,other::rw-` 소유 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 및 쓰기 권한을 부여 합니다.

```cs
public async Task ManageFileACLs(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("hello.txt");

    PathAccessControl FileAccessControl =
        await fileClient.GetAccessControlAsync();

    foreach (var item in FileAccessControl.AccessControlList)
    {
        Console.WriteLine(item.ToString());
    }

    IList<PathAccessControlItem> accessControlList
        = PathAccessControlExtensions.ParseAccessControlList
        ("user::rwx,group::r-x,other::rw-");

    fileClient.SetAccessControlList(accessControlList);
}
```

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드 

먼저 다운로드 하려는 파일을 나타내는 [DataLakeFileClient](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient) 인스턴스를 만듭니다. [DataLakeFileClient ReadAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefileclient.readasync) 메서드를 사용 하 고 반환 값을 구문 분석 하 여 [스트림](https://docs.microsoft.com/dotnet/api/system.io.stream) 개체를 가져옵니다. 모든 .NET 파일 처리 API를 사용 하 여 스트림에서 바이트를 파일에 저장 합니다. 

이 예에서는 [BinaryReader](https://docs.microsoft.com/dotnet/api/system.io.binaryreader) 및 [FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) 을 사용 하 여 바이트를 파일에 저장 합니다. 

```cs
public async Task DownloadFile(DataLakeFileSystemClient fileSystemClient)
{
    DataLakeDirectoryClient directoryClient =
        fileSystemClient.GetDirectoryClient("my-directory");

    DataLakeFileClient fileClient = 
        directoryClient.GetFileClient("my-image.png");

    Response<FileDownloadInfo> downloadResponse = await fileClient.ReadAsync();

    BinaryReader reader = new BinaryReader(downloadResponse.Value.Content);

    FileStream fileStream = 
        File.OpenWrite("C:\\my-image-downloaded.png");

    int bufferSize = 4096;

    byte[] buffer = new byte[bufferSize];

    int count;

    while ((count = reader.Read(buffer, 0, buffer.Length)) != 0)
    {
        fileStream.Write(buffer, 0, count);
    }

    await fileStream.FlushAsync();

    fileStream.Close();
}
```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

[FileSystemClient GetPathsAsync](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getpathsasync) 메서드를 호출 하 고 결과를 열거 하 여 디렉터리 내용을 나열 합니다.

이 예에서는 라는 디렉터리에 있는 각 파일의 이름을 인쇄 `my-directory` 합니다.

```cs
public async Task ListFilesInDirectory(DataLakeFileSystemClient fileSystemClient)
{
    IAsyncEnumerator<PathItem> enumerator = 
        fileSystemClient.GetPathsAsync("my-directory").GetAsyncEnumerator();

    await enumerator.MoveNextAsync();

    PathItem item = enumerator.Current;

    while (item != null)
    {
        Console.WriteLine(item.Name);

        if (!await enumerator.MoveNextAsync())
        {
            break;
        }
                
        item = enumerator.Current;
    }

}
```

## <a name="set-an-acl-recursively-preview"></a>재귀적으로 ACL 설정 (미리 보기)

각 자식 항목에 대해 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에서 Acl을 재귀적으로 추가, 업데이트 및 제거할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen2에 대해 재귀적으로 acl (액세스 제어 목록) 설정](recursive-access-control-lists.md)을 참조 하세요.

## <a name="see-also"></a>참고 항목

* [API 참조 설명서](https://docs.microsoft.com/dotnet/api/azure.storage.files.datalake)
* [패키지(NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
* [샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
* [Gen1과 Gen2 매핑](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-net/issues)

