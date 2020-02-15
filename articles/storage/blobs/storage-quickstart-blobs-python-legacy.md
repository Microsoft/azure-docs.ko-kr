---
title: '빠른 시작: Python용 Azure Blob 스토리지 클라이언트 라이브러리 v2.1'
description: 이 빠른 시작에서는 개체(Blob) 스토리지에서 스토리지 계정 및 컨테이너를 만듭니다. 그런 다음, Python용 스토리지 클라이언트 라이브러리 v2.1을 사용하여 Azure Storage에 BLOB을 업로드하고, BLOB을 다운로드하고, 컨테이너의 BLOB을 나열합니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.custom: seo-python-october2019
ms.openlocfilehash: 4b0248604b6e9189d5275177a4960e4c352e8215
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906444"
---
# <a name="quickstart-manage-blobs-with-python-v21-sdk"></a>빠른 시작: Python v2.1 SDK로 Blob 관리

이 빠른 시작에서는 Python을 사용하여 Blob을 관리하는 방법을 알아봅니다. Blob은 이미지, 문서, 스트리밍 미디어 및 보관 데이터를 포함하여 대량의 텍스트 또는 이진 데이터를 저장할 수 있는 개체입니다. Blob을 업로드, 다운로드 및 나열하고 컨테이너를 만들고 삭제합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage 계정. [스토리지 계정 만들기](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/).
- [Python용 Azure Storage SDK](https://github.com/Azure/azure-sdk-for-python).

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

이 빠른 시작 가이드에서 [샘플 애플리케이션](https://github.com/Azure-Samples/storage-blobs-python-quickstart.git)은 기본 Python 애플리케이션입니다.  

다음 [git](https://git-scm.com/) 명령을 사용하여 개발 환경에 애플리케이션을 다운로드합니다. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-python-quickstart.git 
```

Python 프로그램을 검토하려면 리포지토리의 루트에 있는 *example.py* 파일을 엽니다.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>스토리지 연결 문자열 구성

애플리케이션에서 스토리지 계정 이름과 계정 키를 입력하여 `BlockBlobService` 개체를 만듭니다.

1. IDE의 솔루션 탐색기에서 *example.py* 파일을 엽니다.

1. `accountname` 및 `accountkey` 값을 사용자의 스토리지 계정 이름 및 키로 바꿉니다.

    ```python
    block_blob_service = BlockBlobService(
        account_name='accountname', account_key='accountkey')
    ```

1. 파일을 저장하고 닫습니다.

## <a name="run-the-sample"></a>샘플 실행

샘플 프로그램은 *Documents* 폴더에 테스트 파일을 만들고, Blob Storage에 파일을 업로드하고, 파일에 Blob을 나열하며, 새 이름으로 파일을 다운로드합니다.

1. 종속성을 설치합니다.

    ```console
    pip install azure-storage-blob==2.1.0
    ```

1. 샘플 애플리케이션으로 이동합니다.

    ```console
    cd storage-blobs-python-quickstart
    ```

1. 샘플을 실행합니다.

    ```console
    python example.py
    ```

    다음 출력과 비슷한 메시지가 표시됩니다.
  
    ```output
    Temp file = C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Uploading to Blob storage as blobQuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    List blobs in the container
             Blob name: QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078.txt

    Downloading blob to     C:\Users\azureuser\Documents\QuickStart_9f4ed0f9-22d3-43e1-98d0-8b2c05c01078_DOWNLOADED.txt
    ```

1. 계속하기 전에 *Documents* 폴더로 이동하여 두 파일을 확인합니다.

    * *QuickStart_\<universally-unique-identifier\>*
    * *QuickStart_\<universally-unique-identifier\>_DOWNLOADED*

1. 이 파일을 열어 동일한지 확인할 수 있습니다.

    [Azure Storage Explorer](https://storageexplorer.com)와 같은 도구를 사용할 수도 있습니다. Blob 스토리지에서 파일을 보는 데 유용합니다. Azure Storage Explorer는 스토리지 계정 정보에 액세스할 수 있는 무료 플랫폼 간 도구입니다. 

1. 파일을 확인한 후에 아무 키나 눌러 샘플을 완료하고 테스트 파일을 삭제합니다.

## <a name="learn-about-the-sample-code"></a>샘플 코드에 대한 자세한 정보

이 샘플의 용도를 파악했으므로 *example.py* 파일을 열고 코드를 확인합니다.

### <a name="get-references-to-the-storage-objects"></a>스토리지 개체에 대한 참조 가져오기

이 섹션에서는 개체를 인스턴스화하고, 새 컨테이너를 만든 다음, 컨테이너에 대해 사용 권한을 설정하여 Blob를 공용 Blob로 유지합니다. `quickstartblobs` 컨테이너를 호출합니다. 

```python
# Create the BlockBlockService that the system uses to call the Blob service for the storage account.
block_blob_service = BlockBlobService(
    account_name='accountname', account_key='accountkey')

# Create a container called 'quickstartblobs'.
container_name = 'quickstartblobs'
block_blob_service.create_container(container_name)

# Set the permission so the blobs are public.
block_blob_service.set_container_acl(
    container_name, public_access=PublicAccess.Container)
```

먼저 Blob Storage에 액세스하고 관리하는 데 사용되는 개체의 참조를 만듭니다. 이러한 개체는 서로를 기준으로 작성됩니다. 즉, 각 개체가 목록의 다음 개체에 사용됩니다.

* 스토리지 계정의 Blob 서비스를 가리키는 **CloudBlobClient** 개체를 인스턴스화합니다. 

* 액세스하는 컨테이너를 나타내는 **CloudBlobContainer** 개체를 인스턴스화합니다. 시스템은 컨테이너를 사용하여 컴퓨터에서 폴더를 사용하여 파일을 구성하는 것과 같이 Blob을 구성합니다.

클라우드 Blob 컨테이너가 있으면 관심 있는 특정 Blob을 가리키는 **CloudBlockBlob** 개체를 인스턴스화합니다. 그런 다음, 필요에 따라 Blob을 업로드하고, 다운로드하고, 복사합니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 BLOB 이름에 대한 자세한 내용은 [컨테이너, BLOB, 메타데이터 이름 지정 및 참조](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

### <a name="upload-blobs-to-the-container"></a>컨테이너에 Blob 업로드

Blob Storage는 블록 Blob, 추가 Blob 및 페이지 Blob을 지원합니다. 블록 blob 크기는 4.7TB까지 가능하며, Excel 스프레드시트에서 큰 비디오 파일까지 다양할 수 있습니다. 파일에 쓴 다음, 더 많은 정보를 계속해서 추가하려는 경우처럼 로깅에 추가 Blob을 사용할 수 있습니다. 페이지 Blob은 주로 IaaS VM(서비스 제공 인프라 가상 머신)을 백업하는 VHD(가상 하드 디스크) 파일에 사용됩니다. 블록 Blob은 가장 일반적으로 사용됩니다. 이 빠른 시작에서는 블록 Blob을 사용합니다.

Blob에 파일을 업로드하려면 디렉터리 이름을 로컬 드라이브의 파일 이름과 조인하여 전체 파일 경로를 가져옵니다. 그런 다음, `create_blob_from_path` 메서드를 사용하여 지정된 경로에 파일을 업로드할 수 있습니다. 

샘플 코드는 시스템에서 업로드 및 다운로드에 사용하는 로컬 파일을 만들고 시스템에서 *full_path_to_file* 및 Blob 이름 *local_file_name*으로 업로드하는 파일을 저장합니다. 이 예제에서는 `quickstartblobs`라는 컨테이너에 파일을 업로드합니다.

```python
# Create a file in Documents to test the upload and download.
local_path = os.path.expanduser("~\Documents")
local_file_name = "QuickStart_" + str(uuid.uuid4()) + ".txt"
full_path_to_file = os.path.join(local_path, local_file_name)

# Write text to the file.
file = open(full_path_to_file, 'w')
file.write("Hello, World!")
file.close()

print("Temp file = " + full_path_to_file)
print("\nUploading to Blob storage as blob" + local_file_name)

# Upload the created file, use local_file_name for the blob name.
block_blob_service.create_blob_from_path(
    container_name, local_file_name, full_path_to_file)
```

Blob Storage에서 사용할 수 있는 몇 가지 업로드 메서드가 있습니다. 예를 들어 메모리 스트림이 있는 경우 `create_blob_from_path` 대신 `create_blob_from_stream` 메서드를 사용할 수 있습니다. 

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

다음 코드는 `list_blobs` 메서드에 대해 `generator`를 만듭니다. 코드는 컨테이너의 Blob 목록을 반복하고 콘솔에 이름을 출력합니다.

```python
# List the blobs in the container.
print("\nList blobs in the container")
generator = block_blob_service.list_blobs(container_name)
for blob in generator:
    print("\t Blob name: " + blob.name)
```

### <a name="download-the-blobs"></a>Blob 다운로드


`get_blob_to_path` 메서드를 사용하여 Blob를 로컬 디스크에 다운로드합니다.
다음 코드는 이전에 업로드한 Blob을 다운로드합니다. 시스템은 로컬 디스크에서 두 파일을 확인할 수 있도록 *_DOWNLOADED*를 Blob 이름에 추가합니다.

```python
# Download the blob(s).
# Add '_DOWNLOADED' as prefix to '.txt' so you can see both files in Documents.
full_path_to_file2 = os.path.join(local_path, local_file_name.replace(
   '.txt', '_DOWNLOADED.txt'))
print("\nDownloading blob to " + full_path_to_file2)
block_blob_service.get_blob_to_path(
    container_name, local_file_name, full_path_to_file2)
```

### <a name="clean-up-resources"></a>리소스 정리
이 빠른 시작 가이드에서는 업로드된 Blob이 더 이상 필요하지 않으면 `delete_container` 메서드를 사용하여 전체 컨테이너를 삭제해도 됩니다. 대신 개별 파일을 삭제하려면 `delete_blob` 메서드를 사용합니다.

```python
# Clean up resources. This includes the container and the temp files.
block_blob_service.delete_container(container_name)
os.remove(full_path_to_file)
os.remove(full_path_to_file2)
```

## <a name="resources-for-developing-python-applications-with-blobs"></a>BLOB을 사용하여 Python 애플리케이션을 개발하기 위한 리소스

Blob 스토리지를 사용하는 Python 개발에 대한 자세한 내용은 다음과 같은 추가 리소스를 참조하세요.

### <a name="binaries-and-source-code"></a>이진 파일 및 소스 코드

- GitHub에서 Azure Storage용 [Python 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-python)를 검색, 다운로드 및 설치하세요.

### <a name="client-library-reference-and-samples"></a>클라이언트 라이브러리 참조 및 샘플

- Python 클라이언트 라이브러리에 대한 자세한 내용은 [Python용 Azure Storage 라이브러리](https://docs.microsoft.com/python/api/overview/azure/storage)를 참조하세요.
- Python 클라이언트 라이브러리를 사용하여 작성된 [Blob Storage 샘플](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=python&term=blob)을 탐색하세요.

## <a name="next-steps"></a>다음 단계
 
이 빠른 시작 가이드에서는 Python을 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 전송하는 방법을 알아보았습니다. 

Storage Explorer 및 Blob에 대한 자세한 내용은 [Storage Explorer를 사용하여 Azure Blob 스토리지 리소스 관리](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)를 참조하세요.
