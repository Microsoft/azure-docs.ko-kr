---
title: Azure 빠른 시작 - PHP를 사용하여 개체 스토리지에 Blob 만들기 | Microsoft Docs
description: PHP를 사용하여 Azure Blob 스토리지로/에서 개체를 전송하는 방법을 신속하게 알아봅니다. Azure Blob 스토리지의 컨테이너에 블록 Blob을 업로드, 다운로드 및 나열합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: a19ff153ea0625a14401770de690dcfddeb74d88
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87835751"
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>PHP를 사용하여 Azure Blob Storage에서 개체 전송
이 빠른 시작에서 PHP를 사용하여 Azure Blob Storage의 컨테이너에 블록 Blob을 업로드, 다운로드 및 나열하는 방법에 대해 알아봅니다. 

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

다음 추가 필수 구성 요소를 설치했는지 확인합니다.

* [PHP](https://php.net/downloads.php)
* [PHP용 Azure Storage SDK](https://github.com/Azure/azure-storage-php)

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드
이 빠른 시작에서 사용되는 [샘플 애플리케이션](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git)은 기본 PHP 애플리케이션입니다.  

[git](https://git-scm.com/)을 사용하여 개발 환경에 애플리케이션 복사본을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

이 명령은 로컬 git 폴더에 해당 리포지토리를 복제합니다. PHP 샘플 애플리케이션을 열려면 storage-blobs-php-quickstart 폴더를 찾아 phpqs.php 파일을 엽니다.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>스토리지 연결 문자열 구성
애플리케이션에서 애플리케이션에 대한 **BlobRestProxy** 인스턴스를 만들려면 스토리지 계정 이름과 계정 키를 제공해야 합니다. 이러한 식별자는 애플리케이션을 실행하는 로컬 컴퓨터의 환경 변수 내에 저장하는 것이 좋습니다. 운영 체제에 따라 다음 예제 중 하나를 사용하여 환경 변수를 만듭니다. **youraccountname** 및 **youraccountkey** 값을 계정 이름과 키로 바꿉니다.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windows"></a>[Windows](#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>환경 구성
로컬 git 폴더에서 폴더를 가져와서 PHP 서버에서 제공하는 디렉터리에 배치합니다. 그런 다음, 동일한 디렉터리의 범위가 지정된 명령 프롬프트를 열고 `php composer.phar install`을 입력합니다.

## <a name="run-the-sample"></a>샘플 실행
이 샘플에서는 '.' 폴더에 테스트 파일을 만듭니다. 샘플 프로그램은 Blob Storage에 테스트 파일을 업로드하고, 컨테이너에 Blob를 나열하며, 새 이름으로 파일을 다운로드합니다. 

예제를 실행합니다. 다음 출력은 애플리케이션 실행 시 반환되는 출력의 예제입니다.
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
표시된 단추를 누르면 샘플 프로그램에서 스토리지 컨테이너와 파일을 삭제합니다. 계속하기 전에 서버의 폴더에서 두 파일을 확인합니다. 이 파일을 열어 동일한지 확인할 수 있습니다.

[Azure Storage Explorer](https://storageexplorer.com)와 같은 도구를 사용하여 Blob Storage의 파일을 볼 수도 있습니다. Azure Storage Explorer는 스토리지 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다. 

파일을 확인한 후에 아무 키나 눌러 데모를 완료하고 테스트 파일을 삭제합니다. 이 샘플의 용도 파악했으므로 example.rb 파일을 열고 코드를 확인합니다. 

## <a name="understand-the-sample-code"></a>샘플 코드 이해

다음으로, 작동 방식을 이해하도록 샘플 코드를 따라 진행합니다.

### <a name="get-references-to-the-storage-objects"></a>스토리지 개체에 대한 참조 가져오기
가장 먼저 할 일은 Blob Storage의 액세스 및 관리에 사용되는 개체에 대한 참조를 만드는 것입니다. 이러한 개체는 서로를 기준으로 작성됩니다. 즉, 각 개체가 목록의 다음 개체에 사용됩니다.

* Azure Storage의 **BlobRestProxy** 개체에 대한 인스턴스를 만들어 연결 자격 증명을 설정합니다. 
* 스토리지 계정의 Blob 서비스를 가리키는 **BlobService** 개체를 만듭니다. 
* 액세스하는 컨테이너를 나타내는 **Container** 개체를 만듭니다. 컨테이너는 컴퓨터에서 폴더를 사용하여 파일을 구성하는 것과 같이 blob을 구성하는 데 사용됩니다.

**blobClient** 컨테이너 개체가 있으면 관심 있는 특정 Blob을 가리키는 **Block** Blob 개체를 만들 수 있습니다. 그런 다음, 업로드, 다운로드 및 복사와 같은 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

이 섹션에서는 Azure Storage 클라이언트의 인스턴스를 설정하고, Blob 서비스 개체를 인스턴스화하고, 새 컨테이너를 만들고, Blob이 공개되도록 컨테이너에 대한 권한을 설정합니다. 컨테이너를 **quickstartblobs**로 지칭합니다. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 Blob는 가장 일반적으로 사용되므로 이 빠른 시작 가이드에서도 사용합니다.  

Blob에 파일을 업로드하려면 로컬 드라이브에서 디렉터리 이름과 파일 이름을 조인하여 파일의 전체 경로를 가져옵니다. 그런 다음, **createBlockBlob()** 메서드를 사용하여 지정된 경로에 파일을 업로드할 수 있습니다. 

샘플 코드에서는 로컬 파일을 가져와서 Azure에 업로드합니다. 코드에서 파일은 **myfile**로, Blob의 이름은 **fileToUpload**로 저장됩니다. 다음 예제에서는 **quickstartblobs**라는 저장소에 이 파일을 업로드합니다.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

블록 Blob의 콘텐츠를 부분적으로 업데이트하려면 **createblocklist()** 메서드를 사용합니다. 블록 blob 크기는 4.7TB까지 가능하며, Excel 스프레드시트에서 큰 비디오 파일까지 다양할 수 있습니다. 페이지 blob은 IaaS VM을 백업하는 데 사용되는 VHD 파일에 주로 사용됩니다. 추가 Blob은 파일에 쓰고 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 사용됩니다. 추가 Blob은 단일 작성자 모델에서 사용해야 합니다. Blob Storage에 저장된 대부분의 개체는 블록 Blob입니다.

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

**listBlobs()** 메서드를 사용하여 컨테이너의 파일 목록을 가져올 수 있습니다. 다음 코드는 Blob 목록을 검색하고, 이 과정을 반복하여 컨테이너에서 찾은 Blob의 이름을 표시합니다.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>Blob의 콘텐츠 가져오기

**getBlob()** 메서드를 사용하여 Blob의 콘텐츠를 가져옵니다. 다음 코드에서는 이전 섹션에서 업로드된 Blob의 콘텐츠를 표시합니다.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작에서 업로드된 Blob이 더 이상 필요하지 않으면 **deleteContainer()** 메서드를 사용하여 전체 컨테이너를 삭제할 수 있습니다. 만든 파일이 더 이상 필요하지 않으면 **deleteBlob()** 메서드를 사용하여 파일을 삭제합니다.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>BLOB을 사용하여 PHP 애플리케이션을 개발하기 위한 리소스

Blob Storage를 사용하여 PHP 응용 프로그램을 개발하기 위한 추가 리소스는 다음과 같습니다.

- GitHub에서 Azure Storage용 [PHP 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-php)를 검색, 다운로드 및 설치하세요.
- PHP 클라이언트 라이브러리를 사용하여 작성된 [Blob Storage 샘플](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob)을 탐색하세요.

## <a name="next-steps"></a>다음 단계
 
이 빠른 시작에서는 PHP를 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. PHP 사용에 대해 자세히 알아보려면 PHP 개발자 센터를 계속 진행하세요.

> [!div class="nextstepaction"]
> [PHP 개발자 센터](https://azure.microsoft.com/develop/php/)


Storage Explorer 및 Blob에 대한 자세한 내용은 [Storage Explorer를 사용하여 Azure Blob Storage 리소스 관리](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.
