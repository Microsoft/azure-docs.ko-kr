---
title: iOS에서 개체(Blob) 저장소를 사용하는 방법 - Azure | Microsoft Docs
description: Azure Blob Storage(개체 스토리지)를 사용하여 클라우드에 구조화되지 않은 데이터를 저장합니다.
services: storage
author: michaelhauss
ms.service: storage
ms.devlang: objective-c
ms.topic: article
ms.date: 11/20/2018
ms.author: michaelhauss
ms.subservice: blobs
ms.openlocfilehash: 1ab799ef7eb9d7c591e76ab9180d4e3f4ba6ba59
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122746"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Azure 파일 공유를 만들고 헤드 노드에 탑재하는 세부 단계는 Windows에서 Azure File Storage 시작을 참조하세요.

이 문서에서는 Microsoft Azure Blob Storage를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Objective-C로 작성되었으며 [Azure Storage Client Library for iOS](https://github.com/Azure/azure-storage-ios)를 사용합니다. 여기서 다루는 시나리오에는 Blob 업로드, 나열, 다운로드 및 삭제가 포함됩니다. Blob에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오. 또한 [샘플 앱](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample)을 다운로드하여 iOS 애플리케이션에서 Azure Storage의 사용을 신속하게 볼 수 있습니다.

Blob Storage에 대한 자세한 내용은 [Azure Blob Storage 소개](storage-blobs-introduction.md)를 참조하세요.

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Azure Storage iOS 라이브러리를 애플리케이션으로 가져오기
[Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient)를 사용하거나 **프레임워크** 파일을 가져와 Azure Storage iOS 라이브러리를 애플리케이션으로 가져올 수 있습니다. CocoaPod는 라이브러리를 손쉽게 통합하는 반면, 프레임워크 파일에서 가져오기는 기존 프로젝트에 대해 덜 침입적이기 때문에 권장되는 방식입니다.

이 라이브러리를 사용하려면 다음이 필요합니다.
- iOS 8+
- Xcode 7+

## <a name="cocoapod"></a>CocoaPod
1. 컴퓨터에 [CocoaPod](https://guides.cocoapods.org/using/getting-started.html#toc_3)를 아직 설치하지 않은 경우 터미널 창을 열고 다음 명령을 실행하여 컴퓨터에 이 프로그램을 설치합니다.
    
    ```shell   
    sudo gem install cocoapods
    ```

2. 그런 다음 프로젝트 디렉터리(.xcodeproj 파일이 포함된 디렉터리)에서 _Podfile_(파일 확장명 없음)이라는 새 파일을 만듭니다. _Podfile_에 다음을 추가한 후 저장합니다.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. 터미널 창에서 프로젝트 디렉터리로 이동하고 다음 명령을 실행합니다.

    ```shell    
    pod install
    ```

4. Xcode에서 .xcodeproj가 열려 있으면 닫습니다. 프로젝트 디렉터리에서 확장명이 .xcworkspace인 새로 만든 프로젝트 파일을 엽니다. 지금부터 이 파일로 작업하게 됩니다.

## <a name="framework"></a>프레임워크
라이브러리를 사용하는 다른 방법은 프레임워크를 수동으로 빌드하는 것입니다.

1. 먼저, [azure-storage-ios repo](https://github.com/azure/azure-storage-ios)를 다운로드하거나 복제합니다.
2. *azure-storage-ios* -> *Lib* -> *Azure Storage 클라이언트 라이브러리*로 이동하고 X 코드에서 `AZSClient.xcodeproj`를 엽니다.
3. Xcode의 왼쪽 위에서 "Azure Storage 클라이언트 라이브러리"의 활성 구성표를 "프레임워크"로 변경합니다.
4. 프로젝트를 빌드합니다(⌘+B). 그러면 바탕 화면에 `AZSClient.framework` 파일이 만들어집니다.

그런 다음, 다음을 수행하여 프레임워크 파일을 애플리케이션으로 가져올 수 있습니다.

1. 새 프로젝트를 만들거나 Xcode에서 기존 프로젝트를 엽니다.
2. `AZSClient.framework`를 Xcode 프로젝트 탐색기에 끌어다 놓습니다.
3. *필요한 경우 항목 복사*를 선택하고 *마침*을 클릭합니다.
4. 왼쪽의 탐색에서 프로젝트를 클릭하고 프로젝트 편집기의 위에서 *일반* 탭을 클릭합니다.
5. *연결된 프레임워크 및 라이브러리* 섹션 아래에서 추가 단추 (+)를 클릭합니다.
6. 이미 제공된 라이브러리 목록에서 `libxml2.2.tbd`를 검색하고 프로젝트에 추가합니다.

## <a name="import-the-library"></a>라이브러리 가져오기 
```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Swift를 사용하는 경우에 브리징 헤더를 만들고 <AZSClient/AZSClient.h>를 가져와야 합니다.

1. 헤더 파일 `Bridging-Header.h`를 만들고 위의 import 문을 추가합니다.
2. *빌드 설정* 탭으로 이동하고 *Objective-C 브리징 헤더*를 검색합니다.
3. *Objective-C 브리징 헤더*의 필드를 두 번 클릭하고 다음 헤더 파일에 경로를 추가합니다.`ProjectName/Bridging-Header.h`
4. 프로젝트(⌘+B)를 빌드하여 Xcode로 브리징 헤더를 받았음을 확인합니다.
5. Swift 파일에서 직접 라이브러리를 사용하여 시작하면 import 문이 필요 없습니다.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>비동기 작업
> [!NOTE]
> 서비스에 대한 요청을 수행하는 모든 메서드는 비동기 작업입니다. 코드 샘플에서 이러한 메서드에는 완료 처리기가 있음을 확인할 수 있습니다. 완료 처리기 내에 있는 코드는 요청이 완료된 **후** 실행됩니다. 완료 처리기 이후 코드는 요청이 이루어지는 **동안** 실행됩니다.
> 
> 

## <a name="create-a-container"></a>컨테이너 만들기
Azure Storage의 모든 Blob는 컨테이너에 있어야 합니다. 다음 예제에서는 *newcontainer*라는 컨테이너를 Storage 계정에 만드는 방법을 보여 줍니다(아직 없는 경우). 컨테이너에 대한 이름을 선택할 때 위에서 언급한 명명 규칙을 따릅니다.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

[Microsoft Azure Storage Explorer](https://storageexplorer.com) 를 확인하고 해당 *newcontainer* 가 스토리지 계정에 대한 컨테이너 목록에 있는지 확인하여 이 작업을 확인할 수 있습니다.

## <a name="set-container-permissions"></a>컨테이너 사용 권한 설정
기본적으로 컨테이너의 사용 권한은 **개인** 액세스용으로 구성됩니다. 그러나 컨테이너는 컨테이너 액세스에 대한 몇 가지 다른 옵션을 제공합니다.

* **개인**: 계정 소유자만 컨테이너 및 Blob 데이터를 읽을 수 있습니다.
* **Blob**: 이 컨테이너 내의 Blob 데이터는 익명 요청을 통해 읽을 수 있으나 컨테이너 데이터는 읽을 수 없습니다. 클라이언트는 익명 요청을 통해 컨테이너 내의 Blob을 열거할 수 없습니다.
* **컨테이너**: 익명 요청을 통해 컨테이너와 Blob 데이터를 읽을 수 있습니다. 클라이언트는 익명 요청을 통해 컨테이너 내에서 Blob을 열거할 수 있지만 저장소 계정 내에서 컨테이너를 열거할 수는 없습니다.

다음 예제에서는 인터넷에서 모든 사용자의 공용, 읽기 전용 액세스를 허용할 **컨테이너** 액세스 권한으로 컨테이너를 만드는 방법을 보여 줍니다.

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>컨테이너에 Blob 업로드
Blob 서비스 개념 섹션에서 설명한 것처럼 Blob Storage는 블록 Blob, 추가 Blob, 페이지 Blob의 세 가지 Blob 유형을 제공합니다. Azure Storage iOS 라이브러리는 세 가지 형식의 Blob을 모두 지원합니다. 대부분의 경우에는 블록 Blob을 사용하는 것이 좋습니다.

다음 예제에서는 NSString에서 블록 Blob를 업로드하는 방법을 보여줍니다. 같은 이름의 Blob가 이 컨테이너에 이미 있는 경우 이 Blob의 내용을 덮어씁니다.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

[Microsoft Azure Storage Explorer](https://storageexplorer.com)를 확인하고 컨테이너 *containerpublic*이 Blob *sampleblob*을 포함하는지 확인하여 이 작업을 확인할 수 있습니다. 이 샘플에서는 공용 컨테이너를 사용했으므로 Blob URI로 이동하여 이 애플리케이션 작업을 확인할 수도 있습니다.

    https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob

NSString에서 블록 Blob을 업로드하는 것 외에도 이와 유사한 메서드가 NSData, NSInputStream 또는 로컬 파일에 존재합니다.

## <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열
다음 예제에서는 컨테이너의 모든 Blob를 나열하는 방법을 보여줍니다. 이 작업을 수행할 때는 다음 매개 변수를 염두에 두어야 합니다.     

* **continuationToken** - 연속 토큰은 목록 작업을 시작할 위치를 나타냅니다. 토큰이 제공되지 않는 경우 처음부터 Blob를 나열합니다. 0에서 최대 설정까지 개수에 관계 없이 Blob를 나열할 수 있습니다. 이 메서드가 0개의 결과를 반환하더라도 `results.continuationToken` 이 nil이 아니면 서비스에 나열되지 않은 더 많은 Blob이 있을 수 있습니다.
* **prefix** - Blob 목록에 사용할 접두사를 지정할 수 있습니다. 이 접두사로 시작하는 Blob만 나열됩니다.
* **useFlatBlobListing** - [컨테이너 및 Blob 이름 명명 및 참조](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) 섹션에서 설명한 것처럼 Blob service가 플랫 저장소 스키마인 경우에도 경로 정보로 Blob 이름을 지정하여 가상 계층 구조를 만들 수 있습니다. 그러나 현재는 플랫이 아닌 목록은 지원되지 않습니다. 이 기능은 곧 제공됩니다. 현재 이 값은 **YES**여야 합니다.
* **blobListingDetails** - Blob을 나열할 때 포함할 항목을 지정할 수 있습니다.
  * _AZSBlobListingDetailsNone_: 커밋된 Blob만 나열하고 Blob 메타데이터는 반환하지 않습니다.
  * _AZSBlobListingDetailsSnapshots_: 커밋된 Blob 및 Blob 스냅숏을 나열합니다.
  * _AZSBlobListingDetailsMetadata_: 목록에 반환된 각 Blob에 대한 Blob 메타데이터를 검색합니다.
  * _AZSBlobListingDetailsUncommittedBlobs_: 커밋 및 커밋되지 않은 Blob을 나열합니다.
  * _AZSBlobListingDetailsCopy_: 목록에 복사 속성을 포함합니다.
  * _AZSBlobListingDetailsAll_: 사용 가능한 모든 커밋된 Blob, 커밋되지 않은 Blob 및 스냅숏을 나열하고 모든 메타데이터와 해당 Blob에 대한 복사 상태를 반환합니다.
* **maxResults** - 이 작업에 대해 반환할 결과의 최대 수입니다. 제한을 설정하지 않으려면 -1을 사용합니다.
* **completionHandler** - 나열 작업의 결과와 함께 실행할 코드 블록입니다.

이 예제에서는 연속 토큰이 반환될 때마다 나열 Blob 메서드를 재귀적으로 호출하는 데 도우미 메서드가 사용됩니다.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Blob 다운로드
다음 예제에서는 NSString 개체로 Blob를 다운로드하는 방법을 보여줍니다.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Blob 삭제
다음 예제에서는 Blob를 삭제하는 방법을 보여줍니다.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Blob 컨테이너 삭제
다음 예제에서는 컨테이너를 삭제하는 방법을 보여줍니다.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>다음 단계
지금까지 iOS에서 Blob Storage를 사용하는 방법을 살펴보았으므로 다음 링크를 따라 이동하여 iOS 라이브러리 및 Storage 서비스에 대한 자세한 내용을 확인하세요.

* [iOS용 Azure Storage 클라이언트 라이브러리](https://github.com/azure/azure-storage-ios)
* [Azure Storage iOS 참조 설명서](https://azure.github.io/azure-storage-ios/)
* [Azure Storage 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx)
* [Azure Storage 팀 블로그](https://blogs.msdn.com/b/windowsazurestorage)

이 라이브러리에 대한 문의 사항이 있는 경우 [MSDN Azure 포럼](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=windowsazuredata) 또는 [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files)에 자유롭게 게시해 주세요.
Azure Storage에 대한 기능 제안 사항이 있는 경우 [Azure Storage 피드백](https://feedback.azure.com/forums/217298-storage/)에 게시해 주세요.

