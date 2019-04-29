---
title: Java를 사용하여 Azure Files 개발 | Microsoft Docs
description: Azure Files를 사용하여 파일 데이터를 저장하는 Java 애플리케이션 및 서비스를 개발하는 방법을 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.devlang: Java
ms.topic: article
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 238e5971e79b192e0ef422dcd452859ff7566580
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763499"
---
# <a name="develop-for-azure-files-with-java"></a>Java를 사용하여 Azure Files 개발
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="about-this-tutorial"></a>이 자습서 정보
이 자습서에서는 Java를 사용하여 Azure Files를 사용하여 파일 데이터를 저장하는 애플리케이션이나 서비스를 개발하는 데 필요한 기본 사항을 보여줍니다. 즉 콘솔 애플리케이션을 만들고, Java 및 Azure Files를 통해 기본 동작을 수행하는 방법을 보여줍니다.

* Azure 파일 공유 만들기 및 삭제
* 디렉터리 만들기 및 삭제
* Azure 파일 공유의 파일 및 디렉터리 열거
* 파일 업로드, 다운로드 및 삭제

> [!Note]  
> Azure Files는 SMB를 통해 액세스할 수 있기 때문에 표준 Java I/O 클래스를 사용하여 Azure 파일 공유에 액세스하는 애플리케이션을 작성할 수 있습니다. 이 문서에서는 [Azure Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api)를 사용하여 Azure Files와 통신하는 Azure Storage Java SDK를 사용하는 애플리케이션을 작성하는 방법에 대해 설명합니다.

## <a name="create-a-java-application"></a>Java 애플리케이션 만들기
샘플을 빌드하려면 JDK(Java 개발 키트)와 [Java용 Azure Storage SDK](https://github.com/Azure/azure-storage-java)가 필요합니다. Azure 저장소 계정도 만들었어야 합니다.

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files를 사용하도록 애플리케이션 설정
Azure 저장소 API를 사용하려면 저장소 서비스를 액세스하고자 하는 위치에서 Java 파일의 맨 위에 다음 명령문을 추가하십시오.

```java
// Include the following imports to use blob APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.file.*;
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure 저장소 연결 문자열 설정
Azure Files를 사용하려면 Azure Storage 계정에 연결해야 합니다. 첫 번째 단계는 저장소 계정에 연결하는 데 사용할 연결 문자열을 구성하는 것입니다. 이를 위해 정적 변수를 정의해 보겠습니다.

```java
// Configure the connection-string with your values
public static final String storageConnectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account_name;" +
    "AccountKey=your_storage_account_key";
```

> [!NOTE]
> your_storage_account_name과 your_storage_account_key를 자신의 스토리지 계정에 대한 실제 값으로 바꿉니다.
> 
> 

## <a name="connecting-to-an-azure-storage-account"></a>Azure 저장소 계정에 연결
스토리지 계정에 연결하려면, **CloudStorageAccount** 개체를 사용하여 **구문 분석** 메서드로 연결 문자열을 전달합니다.

```java
// Use the CloudStorageAccount object to connect to your storage account
try {
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);
} catch (InvalidKeyException invalidKey) {
    // Handle the exception
}
```

**CloudStorageAccount.parse** 는 InvalidKeyException을 발생시키므로 try/catch 블록 안에 넣어야 합니다.

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
Azure Files의 모든 파일 및 디렉터리는 **공유**라는 이름의 컨테이너 안에 있습니다. 저장소 계정은 계정 용량이 허용하는 만큼의 공유를 가질 수 있습니다. 공유 및 해당 콘텐츠에 액세스하려면 Azure Files 클라이언트를 사용해야 합니다.

```java
// Create the Azure Files client.
CloudFileClient fileClient = storageAccount.createCloudFileClient();
```

Azure Files 클라이언트를 사용하면 공유에 대한 참조를 가져올 수 있습니다.

```java
// Get a reference to the file share
CloudFileShare share = fileClient.getShareReference("sampleshare");
```

공유를 실제로 만들고자 한다면 CloudFileShare 개체의 **createIfNotExists** 메서드를 사용합니다.

```java
if (share.createIfNotExists()) {
    System.out.println("New share created");
}
```

이 시점에서, **공유**는 **sampleshare**라는 이름의 공유에 대한 참조를 보유합니다.

## <a name="delete-an-azure-file-share"></a>Azure 파일 공유 삭제
공유 삭제는 CloudFileShare 개체에서 **deleteIfExists** 메서드를 호출하여 수행할 수 있습니다. 다음이 샘플 코드입니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount = CloudStorageAccount.parse(storageConnectionString);

    // Create the file client.
   CloudFileClient fileClient = storageAccount.createCloudFileClient();

   // Get a reference to the file share
   CloudFileShare share = fileClient.getShareReference("sampleshare");

   if (share.deleteIfExists()) {
       System.out.println("sampleshare deleted");
   }
} catch (Exception e) {
    e.printStackTrace();
}
```

## <a name="create-a-directory"></a>디렉터리 만들기
또한 루트 디렉터리에 이들 모두를 포함하는 대신 하위 디렉터리 내에서 파일을 배치하여 저장소를 구성할 수 있습니다. Azure Files를 사용하면 계정이 허용하는 만큼 많은 디렉터리를 만들 수 있습니다. 아래 코드는 루트 디렉터리 아래에 **sampledir**이라는 이름의 하위 디렉터리를 만듭니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the sampledir directory
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

if (sampleDir.createIfNotExists()) {
    System.out.println("sampledir created");
} else {
    System.out.println("sampledir already exists");
}
```

## <a name="delete-a-directory"></a>디렉터리 삭제
디렉터리의 삭제는 간단한 작업입니다. 단, 여전히 파일 또는 기타 디렉터리가 포함된 디렉터리는 삭제할 수 없습니다.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory you want to delete
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

// Delete the directory
if ( containerDir.deleteIfExists() ) {
    System.out.println("Directory deleted");
}
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure 파일 공유의 파일 및 디렉터리 열거
공유 내에서 파일 및 디렉터리 목록을 가져오는 것은 CloudFileDirectory 참조에서 **listFilesAndDirectories** 를 호출하여 쉽게 수행할 수 있습니다. 메서드는 반복할 수 있는 ListFileItem 개체 목록을 반환합니다. 한 예로, 다음 코드는 파일 및 디렉터리를 루트 디렉터리 안에 나열합니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

for ( ListFileItem fileItem : rootDir.listFilesAndDirectories() ) {
    System.out.println(fileItem.getUri());
}
```

## <a name="upload-a-file"></a>파일 업로드
이 섹션에서는 로컬 저장소에서 공유의 루트 디렉터리로 파일을 업로드하는 방법을 배웁니다.

파일을 업로드하는 첫 번째 단계는 상주해야 하는 디렉터리에 대한 참조를 가져오는 것입니다. 공유 개체의 **getRootDirectoryReference** 메서드를 호출하여 가져올 수 있습니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();
```

이제 공유의 루트 디렉터리에 대한 참조를 가졌으므로 다음 코드를 사용하여 파일을 업로드할 수 있습니다.

```java
        // Define the path to a local file.
        final String filePath = "C:\\temp\\Readme.txt";
    
        CloudFile cloudFile = rootDir.getFileReference("Readme.txt");
        cloudFile.uploadFromFile(filePath);
```

## <a name="download-a-file"></a>파일 다운로드
Azure Files에 대해 가장 자주 수행하게 될 작업 중 하나는 파일의 다운로드입니다. 다음 예제에서 코드가 SampleFile.txt를 다운로드하고 그 내용을 표시합니다.

```java
//Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

//Get a reference to the directory that contains the file
CloudFileDirectory sampleDir = rootDir.getDirectoryReference("sampledir");

//Get a reference to the file you want to download
CloudFile file = sampleDir.getFileReference("SampleFile.txt");

//Write the contents of the file to the console.
System.out.println(file.downloadText());
```

## <a name="delete-a-file"></a>파일 삭제
다른 일반적인 Azure Files 작업은 파일 삭제입니다. 다음 코드는 **sampledir**라는 이름의 디렉터리 안에 저장된 SampleFile.txt라는 이름의 파일을 삭제합니다.

```java
// Get a reference to the root directory for the share.
CloudFileDirectory rootDir = share.getRootDirectoryReference();

// Get a reference to the directory where the file to be deleted is in
CloudFileDirectory containerDir = rootDir.getDirectoryReference("sampledir");

String filename = "SampleFile.txt"
CloudFile file;

file = containerDir.getFileReference(filename)
if ( file.deleteIfExists() ) {
    System.out.println(filename + " was deleted");
}
```

## <a name="next-steps"></a>다음 단계
다른 Azure 저장소 API에 대해 더 알아보려면 다음 링크를 따르십시오.

* [Java 개발자용 Azure](/java/azure)/)
* [Java용 Azure Storage SDK](https://github.com/azure/azure-storage-java)
* [Android용 Azure Storage SDK](https://github.com/azure/azure-storage-android)
* [Azure Storage 클라이언트 SDK 참조](http://dl.windowsazure.com/storage/javadoc/)
* [Azure Storage 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage 팀 블로그](https://blogs.msdn.com/b/windowsazurestorage/)
* [AzCopy 명령줄 유틸리티로 데이터 전송](../common/storage-use-azcopy.md)
* [Azure Files 문제 해결 - Windows](storage-troubleshoot-windows-file-connection-problems.md)
