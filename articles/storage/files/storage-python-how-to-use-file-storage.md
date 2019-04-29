---
title: Python을 사용하여 Azure Files용으로 개발 | Microsoft Docs
description: Azure Files를 사용하여 파일 데이터를 저장하는 Python 애플리케이션 및 서비스를 개발하는 방법에 대해 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.devlang: python
ms.topic: article
ms.date: 12/14/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: ecb3ef82196c3b6febd44850b47f467ba37facc2
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63763535"
---
# <a name="develop-for-azure-files-with-python"></a>Python을 사용하여 Azure Files 개발
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

이 자습서에서는 Azure Files를 사용하여 파일 데이터를 저장하는 애플리케이션이나 서비스를 Python을 사용하여 개발하는 데 필요한 기본 사항을 보여줍니다. 즉 간단한 콘솔 애플리케이션을 만들고, Python 및 Azure Files를 통해 기본 작업을 수행하는 방법을 보여줍니다.

* Azure 파일 공유 만들기
* 디렉터리 만들기
* Azure 파일 공유의 파일 및 디렉터리 열거
* 파일 업로드, 다운로드 및 삭제

> [!Note]  
> Azure Files는 SMB를 통해 액세스할 수 있기 때문에 표준 Python I/O 클래스 및 함수를 사용하여 Azure File 공유에 액세스하는 간단한 애플리케이션을 작성할 수 있습니다. 이 문서에서는 [Azure Files REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api)를 사용하여 Azure Files와 통신하는 Azure Storage Python SDK를 사용하는 애플리케이션을 작성하는 방법에 대해 설명합니다.

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Azure Storage SDK for Python 다운로드 및 설치

[Azure Storage SDK for Python](https://github.com/azure/azure-storage-python)에는 Python 2.7, 3.3, 3.4, 3.5 또는 3.6이 필요합니다.
 
## <a name="install-via-pypi"></a>PyPi를 통해 설치

PyPi(Python Package Index)를 통해 설치하려면 다음을 입력합니다.

```bash
pip install azure-storage-file
```

> [!NOTE]
> Azure Storage SDK for Python 버전 0.36 또는 이전 버전에서 업그레이드하는 경우 최신 패키지를 설치하기 전에 `pip uninstall azure-storage`를 사용하여 이전 SDK를 제거합니다.

대체 설치 방법을 확인하려면 [GitHub의 Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python/)을 방문하세요.

## <a name="view-the-sample-application"></a>샘플 애플리케이션 보기
Azure Files에서 Python을 사용하는 방법을 보여 주는 샘플 애플리케이션을 보고 실행하려면 [Azure Storage: Python에서 Azure Files 시작](https://github.com/Azure-Samples/storage-file-python-getting-started)을 참조하세요. 

샘플 애플리케이션을 실행하려면 `azure-storage-file` 및 `azure-storage-common` 패키지를 둘 다 설치해야 합니다.

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files를 사용하도록 애플리케이션 설정
프로그래밍 방식으로 Azure Storage에 액세스하려는 Python 원본 파일의 맨 위쪽에 다음을 추가합니다.

```python
from azure.storage.file import FileService
```

## <a name="set-up-a-connection-to-azure-files"></a>Azure Files에 대한 연결 설정 
`FileService` 개체를 사용하면 공유, 디렉터리 및 파일 작업을 수행할 수 있습니다. 다음 코드는 저장소 계정 이름 및 계정 키를 사용하는 `FileService` 개체를 만듭니다. `<myaccount>` 및 `<mykey>`를 사용자의 계정 이름 및 키로 바꾸세요.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
다음 코드 예제에서는 `FileService` 개체를 사용하여 공유가 없는 경우 새로 만들 수 있습니다.

```python
file_service.create_share('myshare')
```

## <a name="create-a-directory"></a>디렉터리 만들기
또한 루트 디렉터리에 이들 모두를 포함하는 대신 하위 디렉터리 내에서 파일을 배치하여 저장소를 구성할 수 있습니다. Azure Files를 사용하면 계정이 허용하는 만큼 많은 디렉터리를 만들 수 있습니다. 아래 코드는 루트 디렉터리 아래에 **sampledir** 라는 이름의 하위 디렉터리를 만듭니다.

```python
file_service.create_directory('myshare', 'sampledir')
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure 파일 공유의 파일 및 디렉터리 열거
공유의 파일 및 디렉터리를 나열하려면 **list\_directories\_and\_files** 메서드를 사용합니다. 이 메서드는 생성기를 반환합니다. 다음 코드는 공유에 있는 각 파일 및 디렉터리의 **이름** 을 콘솔에 출력합니다.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

## <a name="upload-a-file"></a>파일 업로드 
Azure 파일 공유에는 파일이 상주할 수 있는 최소한의 루트 디렉터리가 포함되어 있습니다. 이 섹션에서는 로컬 저장소에서 공유의 루트 디렉터리로 파일을 업로드하는 방법을 배웁니다.

파일을 만들고 데이터를 업로드하려면 `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` 또는 `create_file_from_text` 메서드를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

`create_file_from_path`는 지정된 경로에서 파일의 콘텐츠를 업로드하고, `create_file_from_stream`은 이미 열려 있는 파일/스트림에서 콘텐츠를 업로드합니다. `create_file_from_bytes`는 바이트 배열을 업로드하고, `create_file_from_text`는 지정된 인코딩(기본값: UTF-8)을 사용하여 지정된 텍스트 값을 업로드합니다.

다음 예제에서는 **sunset.png** 파일의 내용을 **myfile** 파일에 업로드합니다.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

## <a name="download-a-file"></a>파일 다운로드
파일에서 데이터를 다운로드하려면 `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes` 또는 `get_file_to_text`를 사용합니다. 이러한 메서드는 데이터의 크기가 64MB를 초과할 경우 필요한 청크를 수행하는 고급 메서드입니다.

다음 예제에서는 `get_file_to_path`를 사용하여 **myfile** 파일의 콘텐츠를 다운로드한 다음 **out-sunset.png** 파일에 저장하는 방법을 보여 줍니다.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

## <a name="delete-a-file"></a>파일 삭제
마지막으로 파일을 삭제하려면 `delete_file`을 호출합니다.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="create-share-snapshot"></a>공유 스냅숏 만들기
전체 파일 공유의 특정 시점 복사본을 만들 수 있습니다.

```python
snapshot = file_service.snapshot_share(share_name)
snapshot_id = snapshot.snapshot
```

**메타데이터를 사용하여 공유 스냅숏 만들기**

```python
metadata = {"foo": "bar"}
snapshot = file_service.snapshot_share(share_name, metadata=metadata)
```

## <a name="list-shares-and-snapshots"></a>공유 및 스냅숏 나열 
특정 공유에 대한 모든 스냅숏을 나열할 수 있습니다.

```python
shares = list(file_service.list_shares(include_snapshots=True))
```

## <a name="browse-share-snapshot"></a>공유 스냅숏 찾아보기
각 공유 스냅숏의 내용을 찾아보고 특정 시점에서 파일 및 디렉터리를 검색할 수 있습니다.

```python
directories_and_files = list(file_service.list_directories_and_files(share_name, snapshot=snapshot_id))
```

## <a name="get-file-from-share-snapshot"></a>공유 스냅숏에서 파일 가져오기
복원 시나리오에 대한 공유 스냅숏에서 파일을 다운로드할 수 있습니다.

```python
with open(FILE_PATH, 'wb') as stream:
    file = file_service.get_file_to_stream(share_name, directory_name, file_name, stream, snapshot=snapshot_id)
```

## <a name="delete-a-single-share-snapshot"></a>단일 공유 스냅숏 삭제  
단일 공유 스냅숏을 삭제할 수 있습니다.

```python
file_service.delete_share(share_name, snapshot=snapshot_id)
```

## <a name="delete-share-when-share-snapshots-exist"></a>공유 스냅숏이 존재하는 경우 공유 삭제
모든 스냅숏을 먼저 삭제해야만 스냅숏을 포함한 공유를 삭제할 수 있습니다.

```python
file_service.delete_share(share_name, delete_snapshots=DeleteSnapshot.Include)
```

## <a name="next-steps"></a>다음 단계
이제 Python으로 Azure Files를 조작하는 방법을 배웠으므로 다음 링크를 통해 자세한 내용을 알아보세요.

* [Python 개발자 센터](https://azure.microsoft.com/develop/python/)
* [Azure Storage 서비스 REST API](https://msdn.microsoft.com/library/azure/dd179355)
* [Microsoft Azure Storage SDK for Python](https://github.com/Azure/azure-storage-python)
