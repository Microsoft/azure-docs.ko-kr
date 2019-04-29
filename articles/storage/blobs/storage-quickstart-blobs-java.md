---
title: Java v7용 클라이언트 라이브러리를 사용하여 Azure Storage에 Blob을 만드는 방법 | Microsoft Docs
description: 개체(Blob) 저장소에서 저장소 계정 및 컨테이너를 만듭니다. 그런 다음, Java v7용 Azure Storage 클라이언트 라이브러리를 사용하여 Azure Storage에 Blob을 업로드하고, Blob을 다운로드하고, 컨테이너의 Blob을 나열합니다.
services: storage
author: roygara
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: rogarana
ms.openlocfilehash: c9aaa22a656d1164ad4e9f9c1ed7539a93dd0457
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128922"
---
# <a name="how-to-upload-download-and-list-blobs-using-the-client-library-for-java-v7"></a>Java v7용 클라이언트 라이브러리를 사용하여 Blob을 업로드, 다운로드 및 나열하는 방법

이 방법 가이드에서는 Java v7용 클라이언트 라이브러리를 사용하여 Azure Blob 스토리지의 컨테이너에 블록 Blob을 업로드, 다운로드 및 나열하는 방법을 알아봅니다.

> [!TIP]
> Java용 Azure Storage 클라이언트 라이브러리의 최신 버전은 v10입니다. 되도록이면 클라이언트 라이브러리의 최신 버전을 사용 하는 것이 좋습니다. v10을 사용하여 시작하려면 [빠른 시작: Java Storage SDK V10을 사용하여 Blob 업로드, 다운로드 및 나열](storage-quickstart-blobs-java-v10.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

또한 [Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM)에서 Azure 스토리지 계정을 만듭니다. 저장소 계정을 만드는 데 도움이 필요한 경우 [저장소 계정 만들기](../common/storage-quickstart-create-account.md)를 참조하세요.

다음 필수 조건을 갖추고 있는지 확인합니다.

* Maven 통합이 있는 IDE 설치

* 또는 명령줄에서 작동할 수 있게 Maven 설치 및 구성

이 가이드에서는 "Java 개발자를 위한 Eclipse IDE" 구성으로 [Eclipse](https://www.eclipse.org/downloads/)를 사용합니다.

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

[애플리케이션 예제](https://github.com/Azure-Samples/storage-blobs-java-quickstart)는 기본 콘솔 애플리케이션입니다.  

[git](https://git-scm.com/)을 사용하여 개발 환경에 애플리케이션 복사본을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-java-quickstart.git
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. 프로젝트를 열려면 Eclipse를 시작하고 시작 화면을 닫습니다. **File**을 선택한 다음, **Open Projects from File System**을 선택합니다. **Detect and configure project natures**를 선택합니다. **Directory**를 선택한 다음, 복제한 리포지토리를 저장한 위치로 이동합니다. 복제한 리포지토리 내에서 **blobAzureApp** 폴더를 선택합니다. **blobAzureApp** 프로젝트가 Eclipse 프로젝트로 표시되는지 확인한 다음, **Finish**를 선택합니다.

프로젝트 가져오기가 완료되면 **AzureApp.java**(**src/main/java**의 **blobQuickstart.blobAzureApp**에 있음)를 열고 `storageConnectionString` 문자열 내부의 `accountname`및 `accountkey`를 바꿉니다. 그런 다음, 애플리케이션을 실행합니다. 이러한 작업을 완료하는 것에 대한 구체적인 지침은 다음 섹션에 설명되어 있습니다.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]    

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성
    
애플리케이션에서 스토리지 계정에 대한 연결 문자열을 제공해야 합니다. **AzureApp.Java** 파일을 엽니다. `storageConnectionString` 변수를 찾고 이전 섹션에서 복사한 연결 문자열 값을 붙여 넣습니다. `storageConnectionString` 변수는 다음 코드 예제와 유사해야 합니다.

```java
public static final String storageConnectionString =
"DefaultEndpointsProtocol=https;" +
"AccountName=<account-name>;" +
"AccountKey=<account-key>";
```

## <a name="run-the-sample"></a>샘플 실행

이 응용 프로그램 예제는 기본 디렉터리(Windows 사용자의 경우 *C:\Users\<user&gt;\AppData\Local\Temp*)에 테스트 파일을 만들고, Blob Storage에 업로드하고, 컨테이너의 Blob을 나열한 다음, 해당 파일을 새 이름으로 다운로드하여 이전 파일과 새 파일을 비교할 수 있도록 합니다. 

명령줄에서 Maven을 사용하여 샘플을 실행합니다. 셸을 열고 복제한 디렉터리 내부의 **blobAzureApp**으로 이동합니다. 그런 후 `mvn compile exec:java`를 입력합니다. 

다음 예제는 Windows에서 애플리케이션을 실행하는 경우의 출력을 보여줍니다.

```
Azure Blob storage quick start sample
Creating container: quickstartcontainer
Creating a sample file at: C:\Users\<user>\AppData\Local\Temp\sampleFile514658495642546986.txt
Uploading the sample file 
URI of blob is: https://myexamplesacct.blob.core.windows.net/quickstartcontainer/sampleFile514658495642546986.txt
The program has completed successfully.
Press the 'Enter' key while in the console to delete the sample files, example container, and exit the application.

Deleting the container
Deleting the source, and downloaded files
```

계속하기 전에 샘플 파일에 대한 기본 디렉터리(Windows 사용자의 경우 *C:\Users\<user>\AppData\Local\Temp*)를 확인합니다. 콘솔 창에서 blob에 대한 URL을 복사하고 브라우저에 붙여 넣어 Blob Storage의 파일 콘텐츠를 봅니다. 디렉터리의 샘플 파일을 Blob Storage에 저장된 콘텐츠와 비교하는 경우 동일하게 표시됩니다. 

  >[!NOTE]
  >[Azure Storage 탐색기](https://storageexplorer.com/?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage 탐색기는 스토리지 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다.

파일을 확인한 후에 **Enter** 키를 눌러 데모를 완료하고 테스트 파일을 삭제합니다. 이 샘플의 용도 파악했으므로 **AzureApp.java** 파일을 열고 코드를 확인합니다. 

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로, 작동 방식을 이해하도록 샘플 코드를 따라 진행합니다.

### <a name="get-references-to-the-storage-objects"></a>저장소 개체에 대한 참조 가져오기

가장 먼저 할 일은 Blob Storage의 액세스 및 관리에 사용되는 개체에 대한 참조를 만드는 것입니다. 이러한 개체는 서로를 기준으로 작성됩니다. 즉, 각 개체가 목록의 다음 개체에 사용됩니다.

* 스토리지 계정을 가리키는 [CloudStorageAccount](/java/api/com.microsoft.azure.management.storage.storageaccount) 개체의 인스턴스를 만듭니다.

    **CloudStorageAccount** 개체는 스토리지 계정을 나타내며 이를 통해 스토리지 계정 속성을 프로그래밍 방식으로 설정하고 액세스할 수 있습니다. **CloudStorageAccount** 개체를 사용하여 Blob 서비스에 액세스하는 데 필요한 **CloudBlobClient** 인스턴스를 만들 수 있습니다.

* 저장소 계정의 [Blob service](/java/api/com.microsoft.azure.storage.blob._cloud_blob_client)를 가리키는 **CloudBlobClient** 개체의 인스턴스를 만듭니다.

    **CloudBlobClient**는 Blob 서비스에 대한 액세스 지점을 제공하여 이를 통해 Blob Storage 속성을 프로그래밍 방식으로 설정하고 액세스할 수 있습니다. **CloudBlobClient** 개체를 사용하여 컨테이너를 만드는 데 필요한 **CloudBlobContainer** 인스턴스를 만들 수 있습니다.

* 액세스하는 컨테이너를 나타내는 [CloudBlobContainer](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container) 개체의 인스턴스를 만듭니다. 컨테이너를 사용하여 컴퓨터에서 폴더를 사용하여 파일을 구성하는 것과 같이 Blob을 구성합니다.    

    **CloudBlobContainer**가 있으면 관심 있는 특정 Blob을 가리키는 [CloudBlockBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob) 개체의 인스턴스를 만들고, 업로드, 다운로드, 복사 또는 기타 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

### <a name="create-a-container"></a>컨테이너 만들기

이 섹션에서는 개체의 인스턴스를 만들고, 새 컨테이너를 만든 다음, 컨테이너에 대해 사용 권한을 설정하여 Blob을 공용 Blob으로 유지하고 URL을 통해서만 액세스할 수 있게 합니다. 컨테이너를 **quickstartcontainer**로 지칭합니다. 

이 예제에서는 샘플이 실행될 때마다 새 컨테이너를 만들려고 하기 때문에 [CreateIfNotExists](/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.createifnotexists)를 사용합니다. 애플리케이션 전체에서 동일한 컨테이너를 사용하는 프로덕션 환경에서는 **CreateIfNotExists**를 한 번만 호출하는 것이 더 좋은 방법입니다. 또는 코드에서 만들 필요가 없도록 컨테이너를 미리 만들 수도 있습니다.

```java
// Parse the connection string and create a blob client to interact with Blob storage
storageAccount = CloudStorageAccount.parse(storageConnectionString);
blobClient = storageAccount.createCloudBlobClient();
container = blobClient.getContainerReference("quickstartcontainer");

// Create the container if it does not exist with public access.
System.out.println("Creating container: " + container.getName());
container.createIfNotExists(BlobContainerPublicAccessType.CONTAINER, new BlobRequestOptions(), new OperationContext());
```

### <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

블록 Blob에 파일을 업로드하려면 대상 컨테이너에 Blob에 대한 참조를 가져옵니다. Blob 참조가 있으면 [CloudBlockBlob.Upload](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload)를 사용하여 데이터를 업로드할 수 있습니다. 이 작업은 Blob이 없는 경우 만들고, Blob이 이미 있는 경우 덮어씁니다.

샘플 코드는 업로드 및 다운로드에 사용할 로컬 파일을 만들고 해당 파일이 **blob**에 **source** 및 blob의 이름으로 업로드될 수 있게 저장합니다. 다음 예제에서는 **quickstartcontainer**라는 컨테이너에 이 파일을 업로드합니다.

```java
//Creating a sample file
sourceFile = File.createTempFile("sampleFile", ".txt");
System.out.println("Creating a sample file at: " + sourceFile.toString());
Writer output = new BufferedWriter(new FileWriter(sourceFile));
output.write("Hello Azure!");
output.close();

//Getting a blob reference
CloudBlockBlob blob = container.getBlockBlobReference(sourceFile.getName());

//Creating blob and uploading file to it
System.out.println("Uploading the sample file ");
blob.uploadFromFile(sourceFile.getAbsolutePath());
```

[upload](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.upload), [uploadBlock](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadblock), [uploadFullBlob](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadfullblob), [uploadStandardBlobTier](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadstandardblobtier) 및 [uploadText](/java/api/com.microsoft.azure.storage.blob._cloud_block_blob.uploadtext)를 포함하여 Blob Storage에서 사용할 수 있는 몇 가지 `upload` 메서드가 있습니다. 예를 들어 문자열이 있는 경우 `Upload` 메서드 대신 `UploadText` 메서드를 사용할 수 있습니다. 

블록 Blob은 모든 유형의 텍스트 또는 이진 파일이 될 수 있습니다. 페이지 Blob은 IaaS VM을 백업하는 VHD 파일에 주로 사용됩니다. 파일에 쓴 다음, 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 추가 Blob을 사용합니다. Blob Storage에 저장된 대부분의 개체는 블록 Blob입니다.

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[CloudBlobContainer.ListBlobs](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.listblobs)를 사용하여 컨테이너의 파일 목록을 가져올 수 있습니다. 다음 코드는 blob 목록을 검색하고, 이 과정을 반복하여 발견된 Blob의 URI를 표시합니다. 명령 창에서 URI를 복사한 후 브라우저에 붙여 넣어 파일을 봅니다.

```java
//Listing contents of container
for (ListBlobItem blobItem : container.listBlobs()) {
    System.out.println("URI of blob is: " + blobItem.getUri());
}
```

### <a name="download-blobs"></a>Blob 다운로드

[CloudBlob.DownloadToFile](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob.downloadtofile)를 사용하여 blob을 로컬 디스크에 다운로드합니다.

다음 코드는 이전 섹션에서 업로드된 blob을 다운로드하고, blob 이름에 "_DOWNLOADED" 접미사를 추가하여 로컬 디스크에서 두 파일을 모두 볼 수 있도록 합니다. 

```java
// Download blob. In most cases, you would have to retrieve the reference
// to cloudBlockBlob here. However, we created that reference earlier, and 
// haven't changed the blob we're interested in, so we can reuse it. 
// Here we are creating a new file to download to. Alternatively you can also pass in the path as a string into downloadToFile method: blob.downloadToFile("/path/to/new/file").
downloadedFile = new File(sourceFile.getParentFile(), "downloadedFile.txt");
blob.downloadToFile(downloadedFile.getAbsolutePath());
```

### <a name="clean-up-resources"></a>리소스 정리

업로드한 Blob이 더 이상 필요하지 않은 경우 [CloudBlobContainer.DeleteIfExists](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.blob._cloud_blob_container.deleteifexists)를 사용하여 전체 컨테이너를 삭제할 수 있습니다. 이 메서드는 컨테이너의 파일도 삭제합니다.

```java
try {
if(container != null)
    container.deleteIfExists();
} catch (StorageException ex) {
System.out.println(String.format("Service error. Http code: %d and error code: %s", ex.getHttpStatusCode(), ex.getErrorCode()));
}

System.out.println("Deleting the source, and downloaded files");

if(downloadedFile != null)
downloadedFile.deleteOnExit();
        
if(sourceFile != null)
sourceFile.deleteOnExit();
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 Java를 사용하여 로컬 디스크와 Azure Blob 스토리지 간에 파일을 전송하는 방법을 알아보았습니다. Java를 사용하는 방법에 대해 자세히 알려면 GitHub 소스 코드 리포지토리를 계속합니다.

> [!div class="nextstepaction"]
> [Java용 Microsoft Azure Storage SDK v10](https://github.com/azure/azure-storage-java) 
> [Java API 참조](https://docs.microsoft.com/java/azure/)
> [Java용 코드 샘플](../common/storage-samples-java.md)
