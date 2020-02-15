---
title: '빠른 시작: Azure Blob 스토리지 라이브러리 v12 - Python'
description: 이 빠른 시작에서는 Python용 Azure Blob 스토리지 클라이언트 라이브러리버전 12를 사용하여 Blob(개체) 스토리지에서 컨테이너 및 Blob을 만드는 방법을 알아봅니다. 그런 다음, Blob을 로컬 컴퓨터로 다운로드하는 방법과 컨테이너의 모든 Blob을 나열하는 방법을 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 03f298b49e6a1eba84e8adf5ca6039df0bfe1abd
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906421"
---
# <a name="quickstart-manage-blobs-with-python-v12-sdk"></a>빠른 시작: Python v12 SDK로 Blob 관리

이 빠른 시작에서는 Python을 사용하여 Blob을 관리하는 방법을 알아봅니다. Blob은 이미지, 문서, 스트리밍 미디어 및 보관 데이터를 포함하여 대량의 텍스트 또는 이진 데이터를 저장할 수 있는 개체입니다. Blob을 업로드, 다운로드 및 나열하고 컨테이너를 만들고 삭제합니다.

[API 참조 설명서](/python/api/azure-storage-blob) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob) | [패키지(Python 패키지 인덱스)](https://pypi.org/project/azure-storage-blob/) | [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage 계정. [스토리지 계정 만들기](../common/storage-account-create.md)
- [Python](https://www.python.org/downloads/) 2.7, 3.5 이상.

> [!NOTE]
> 이전 SDK 버전을 시작하려면 [빠른 시작: Python v2.1 SDK로 Blob 관리](storage-quickstart-blobs-python-legacy.md)를 참조하세요.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>설치

이 섹션에서는 Python용 Azure Blob 스토리지 클라이언트 라이브러리 v12를 사용하는 프로젝트를 준비합니다.

### <a name="create-the-project"></a>프로젝트 만들기

*blob-quickstart-v12*라는 Python 애플리케이션을 만듭니다.

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 프로젝트에 대한 새 디렉터리를 만듭니다.

    ```console
    mkdir blob-quickstart-v12
    ```

1. 새로 만든 *blob-quickstart-v12* 디렉터리로 전환합니다.

    ```console
    cd blob-quickstart-v12
    ```

1. *blob-quickstart-v12* 디렉터리에서 *data*라는 다른 디렉터리를 만듭니다. 여기에서 Blob 데이터 파일이 생성되고 저장됩니다.

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>패키지 설치

애플리케이션 디렉터리에서 `pip install` 명령을 사용하여 Python용 Azure Blob 스토리지 클라이언트 라이브러리를 설치합니다.

```console
pip install azure-storage-blob
```

이 명령은 Python용 Azure Blob 스토리지 클라이언트 라이브러리 패키지와 이 패키지에 사용되는 모든 라이브러리를 설치합니다. 이 경우에는 Python용 Azure 핵심 라이브러리만 설치합니다.

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 코드 편집기에서 새 텍스트 파일 열기
1. `import` 문 추가
1. 기본적인 예외 처리를 포함하여 프로그램의 구조 만들기

    코드는 다음과 같습니다.

    ```python
    import os, uuid
    from azure.storage.blob import BlobServiceClient, BlobClient, ContainerClient

    try:
        print("Azure Blob storage v12 - Python quickstart sample")
        # Quick start code goes here
    except Exception as ex:
        print('Exception:')
        print(ex)
    ```

1. 새 파일을 *blob-quickstart-v12* 디렉터리에 *blob-quickstart-v12.py*로 저장합니다.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>개체 모델

Azure Blob Storage는 대량의 비정형 데이터를 저장하도록 최적화되어 있습니다. 비정형 데이터는 텍스트 또는 이진 데이터와 같은 특정 데이터 모델 또는 정의를 따르지 않는 데이터입니다. Blob Storage는 다음 세 가지 유형의 리소스를 제공합니다.

* 스토리지 계정
* 스토리지 계정의 컨테이너
* 컨테이너의 blob

다음 다이어그램에서는 이러한 리소스 간의 관계를 보여줍니다.

![Blob Storage 아키텍처 다이어그램](./media/storage-blob-introduction/blob1.png)

다음 Python 클래스를 사용하여 이러한 리소스와 상호 작용합니다.

* [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient): `BlobServiceClient` 클래스를 사용하여 Azure Storage 리소스 및 blob 컨테이너를 조작할 수 있습니다.
* [ContainerClient](/python/api/azure-storage-blob/azure.storage.blob.containerclient): `ContainerClient` 클래스를 사용하여 Azure Storage 컨테이너 및 해당 blob을 조작할 수 있습니다.
* [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient): `BlobClient` 클래스를 사용하여 Azure Storage blob을 조작할 수 있습니다.

## <a name="code-examples"></a>코드 예제

이 예제 코드 조각은 Python용 Azure Blob 스토리지 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여 줍니다.

* [연결 문자열 가져오기](#get-the-connection-string)
* [컨테이너 만들기](#create-a-container)
* [컨테이너에 Blob 업로드](#upload-blobs-to-a-container)
* [컨테이너의 Blob 나열](#list-the-blobs-in-a-container)
* [Blob 다운로드](#download-blobs)
* [컨테이너 삭제](#delete-a-container)

### <a name="get-the-connection-string"></a>연결 문자열 가져오기

아래 코드에서는 [스토리지 연결 문자열 구성](#configure-your-storage-connection-string) 섹션에서 만든 환경 변수에서 스토리지 계정에 대한 연결 문자열을 검색합니다.

다음 코드를 `try` 블록 내에 추가합니다.

```python
# Retrieve the connection string for use with the application. The storage
# connection string is stored in an environment variable on the machine
# running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable is
# created after the application is launched in a console or with Visual Studio,
# the shell or application needs to be closed and reloaded to take the
# environment variable into account.
connect_str = os.getenv('AZURE_STORAGE_CONNECTION_STRING')
```

### <a name="create-a-container"></a>컨테이너 만들기

새 컨테이너의 이름을 결정합니다. 아래 코드는 컨테이너 이름이 고유해지도록 이름에 UUID 값을 추가합니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름 지정에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

[from_connection_string](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#from-connection-string-conn-str--credential-none----kwargs-) 메서드를 호출하여 [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient) 클래스의 인스턴스를 만듭니다. 그런 다음, [create_container](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient#create-container-name--metadata-none--public-access-none----kwargs-)를 호출하여 스토리지 계정에 컨테이너를 실제로 만듭니다.

이 코드를 `try` 블록의 끝에 추가합니다.

```python
# Create the BlobServiceClient object which will be used to create a container client
blob_service_client = BlobServiceClient.from_connection_string(connect_str)

# Create a unique name for the container
container_name = "quickstart" + str(uuid.uuid4())

# Create the container
container_client = blob_service_client.create_container(container_name)
```

### <a name="upload-blobs-to-a-container"></a>컨테이너에 Blob 업로드

다음 코드 조각을 실행합니다.

1. 로컬 디렉터리에 텍스트 파일을 만듭니다.
1. [컨테이너 만들기](#create-a-container) 섹션에서 [BlobServiceClient](/python/api/azure-storage-blob/azure.storage.blob.blobserviceclient)에 대해 [get_blob_client](/python/api/azure-storage-blob/azure.storage.blob.containerclient#get-blob-client-blob--snapshot-none-) 메서드를 호출하여 [BlobClient](/python/api/azure-storage-blob/azure.storage.blob.blobclient) 개체에 대한 참조를 가져옵니다.
1. [upload_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#upload-blob-data--blob-type--blobtype-blockblob---blockblob----length-none--metadata-none----kwargs-) 메서드를 호출하여 로컬 텍스트 파일을 Blob에 업로드합니다.

이 코드를 `try` 블록의 끝에 추가합니다.

```python
# Create a file in local Documents directory to upload and download
local_path = "./data"
local_file_name = "quickstart" + str(uuid.uuid4()) + ".txt"
upload_file_path = os.path.join(local_path, local_file_name)

# Write text to the file
file = open(upload_file_path, 'w')
file.write("Hello, World!")
file.close()

# Create a blob client using the local file name as the name for the blob
blob_client = blob_service_client.get_blob_client(container=container_name, blob=local_file_name)

print("\nUploading to Azure Storage as blob:\n\t" + local_file_name)

# Upload the created file
with open(upload_file_path, "rb") as data:
    blob_client.upload_blob(data)
```

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[list_blobs](/python/api/azure-storage-blob/azure.storage.blob.containerclient#list-blobs-name-starts-with-none--include-none----kwargs-) 메서드를 호출하여 컨테이너의 blob을 나열합니다. 이 경우 하나의 Blob만 컨테이너에 추가되었으므로 나열된 작업은 하나의 해당 Blob만 반환합니다.

이 코드를 `try` 블록의 끝에 추가합니다.

```python
print("\nListing blobs...")

# List the blobs in the container
blob_list = container_client.list_blobs()
for blob in blob_list:
    print("\t" + blob.name)
```

### <a name="download-blobs"></a>Blob 다운로드

[download_blob](/python/api/azure-storage-blob/azure.storage.blob.blobclient#download-blob-offset-none--length-none----kwargs-) 메서드를 호출하여 이전에 만든 blob을 다운로드합니다. 예제 코드는 로컬 파일 시스템에서 두 파일을 볼 수 있도록 파일 이름에 "DOWNLOAD" 접미사를 추가합니다.

이 코드를 `try` 블록의 끝에 추가합니다.

```python
# Download the blob to a local file
# Add 'DOWNLOAD' before the .txt extension so you can see both files in Documents
download_file_path = os.path.join(local_path, str.replace(local_file_name ,'.txt', 'DOWNLOAD.txt'))
print("\nDownloading blob to \n\t" + download_file_path)

with open(download_file_path, "wb") as download_file:
    download_file.write(blob_client.download_blob().readall())
```

### <a name="delete-a-container"></a>컨테이너 삭제

다음 코드는 [delete_container](/python/api/azure-storage-blob/azure.storage.blob.containerclient#delete-container---kwargs-) 메서드로 전체 컨테이너를 제거하여 앱이 만든 리소스를 정리합니다. 원하는 경우 로컬 파일을 삭제할 수도 있습니다.

앱은 blob, 컨테이너 및 로컬 파일을 삭제하기 전에 `input()`을 호출하여 사용자 입력을 일시 중지합니다. 리소스를 삭제하기 전에 올바르게 만들어졌는지 확인하는 것이 좋습니다.

이 코드를 `try` 블록의 끝에 추가합니다.

```python
# Clean up
print("\nPress the Enter key to begin clean up")
input()

print("Deleting blob container...")
container_client.delete_container()

print("Deleting the local source and downloaded files...")
os.remove(upload_file_path)
os.remove(download_file_path)

print("Done")
```

## <a name="run-the-code"></a>코드 실행

이 앱은 로컬 폴더에 테스트 파일을 만들고 Blob 스토리지에 업로드합니다. 그런 다음, 컨테이너에 Blob을 나열하고 이전 파일과 새 파일을 비교할 수 있도록 새 이름으로 해당 파일을 다운로드합니다.

*blob-quickstart-v12.py* 파일이 포함된 디렉터리로 이동한 후 다음 `python` 명령을 실행하여 앱을 실행합니다.

```console
python blob-quickstart-v12.py
```

앱의 출력은 다음 예제 출력과 유사합니다.

```output
Azure Blob storage v12 - Python quickstart sample

Uploading to Azure Storage as blob:
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Listing blobs...
        quickstartcf275796-2188-4057-b6fb-038352e35038.txt

Downloading blob to
        ./data/quickstartcf275796-2188-4057-b6fb-038352e35038DOWNLOAD.txt

Press the Enter key to begin clean up

Deleting blob container...
Deleting the local source and downloaded files...
Done
```

정리 프로세스를 시작하기 전에 *Documents* 폴더에서 두 파일을 확인합니다. 이 파일을 열어 동일한지 확인할 수 있습니다.

파일을 확인한 후에 **Enter** 키를 눌러 테스트 파일을 삭제하고 데모를 완료합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Python을 사용하여 Blob을 업로드, 다운로드 및 나열하는 방법을 배웠습니다.

Blob 스토리지 샘플 앱을 보려면 다음을 계속 진행합니다.

> [!div class="nextstepaction"]
> [Azure Blob 스토리지 SDK v12 Python 샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-blob/samples)

* 자세한 내용은 [Python용 Azure SDK](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-blob/README.md)를 참조하세요.
* 자습서, 샘플, 빠른 시작 및 기타 설명서는 [Python 개발자용 Azure](/azure/python/)를 참조하세요.
