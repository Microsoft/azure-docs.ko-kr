---
title: Acl & 파일에 대 한 Python SDK Azure Data Lake Storage Gen2
description: Python을 사용 하 여, HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 디렉터리 및 파일 및 디렉터리 ACL (액세스 제어 목록)을 관리 합니다.
author: normesta
ms.service: storage
ms.date: 08/26/2020
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: 3056e167a7482cb2967a68bbf1cd40e4bad49093
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923843"
---
# <a name="use-python-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Python을 사용 하 여 Azure Data Lake Storage Gen2에서 디렉터리, 파일 및 Acl 관리

이 문서에서는 Python을 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리 하는 방법을 보여 줍니다. 

[패키지 (Python 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/)  |  [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [API 참조](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0/azure.storage.filedatalake.html)  |  [Gen1 To Gen2 mapping](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>필수 구성 요소

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * HNS(계층 구조 네임스페이스)를 사용하도록 설정된 스토리지 계정입니다. 만들려면 [다음 지침](data-lake-storage-quickstart-create-account.md)을 수행합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

[Pip](https://pypi.org/project/pip/)를 사용 하 여 Python 용 Azure Data Lake Storage 클라이언트 라이브러리를 설치 합니다.

```
pip install azure-storage-file-datalake
```

이러한 import 문을 코드 파일의 맨 위에 추가 합니다.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>계정에 연결

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 

### <a name="connect-by-using-an-account-key"></a>계정 키를 사용 하 여 연결

계정에 연결 하는 가장 쉬운 방법입니다. 

이 예에서는 계정 키를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.

```python
try:  
    global service_client
        
    service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
        "https", storage_account_name), credential=storage_account_key)
    
except Exception as e:
    print(e)
```
 
- `storage_account_name` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

- `storage_account_key`자리 표시자 값을 저장소 계정 액세스 키로 바꿉니다.

### <a name="connect-by-using-azure-active-directory-ad"></a>Azure Active Directory (AD)를 사용 하 여 연결

[Python 용 azure identity client 라이브러리](https://pypi.org/project/azure-identity/) 를 사용 하 여 azure AD로 응용 프로그램을 인증할 수 있습니다.

이 예에서는 클라이언트 ID, 클라이언트 암호 및 테 넌 트 ID를 사용 하 여 **DataLakeServiceClient** 인스턴스를 만듭니다.  이러한 값을 얻으려면 [클라이언트 응용 프로그램의 요청에 대 한 권한 부여를 위해 AZURE AD에서 토큰 획득](../common/storage-auth-aad-app.md)을 참조 하세요.

```python
def initialize_storage_account_ad(storage_account_name, client_id, client_secret, tenant_id):
    
    try:  
        global service_client

        credential = ClientSecretCredential(tenant_id, client_id, client_secret)

        service_client = DataLakeServiceClient(account_url="{}://{}.dfs.core.windows.net".format(
            "https", storage_account_name), credential=credential)
    
    except Exception as e:
        print(e)
```

> [!NOTE]
> 더 많은 예제는 [Python 용 Azure id 클라이언트 라이브러리](https://pypi.org/project/azure-identity/) 설명서를 참조 하세요.

## <a name="create-a-container"></a>컨테이너 만들기

컨테이너는 파일에 대 한 파일 시스템 역할을 합니다. **Create_file_system FileSystemDataLakeServiceClient** 메서드를 호출 하 여 만들 수 있습니다.

이 예제에서는 라는 컨테이너를 만듭니다 `my-file-system` .

```python
def create_file_system():
    try:
        global file_system_client

        file_system_client = service_client.create_file_system(file_system="my-file-system")
    
    except Exception as e:
        print(e) 
```


## <a name="create-a-directory"></a>디렉터리 만들기

**Create_directory FileSystemClient** 메서드를 호출 하 여 디렉터리 참조를 만듭니다.

이 예제에서는 라는 디렉터리를 `my-directory` 컨테이너에 추가 합니다. 

```python
def create_directory():
    try:
        file_system_client.create_directory("my-directory")
    
    except Exception as e:
     print(e) 
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

**Rename_directory DataLakeDirectoryClient** 메서드를 호출 하 여 디렉터리 이름을 바꾸거나 이동 합니다. 원하는 디렉터리의 경로를 매개 변수로 전달 합니다. 

이 예에서는 하위 디렉터리의 이름을로 바꿉니다 `my-subdirectory-renamed` .

```python
def rename_directory():
    try:
       
       file_system_client = service_client.get_file_system_client(file_system="my-file-system")
       directory_client = file_system_client.get_directory_client("my-directory")
       
       new_dir_name = "my-directory-renamed"
       directory_client.rename_directory(rename_destination=directory_client.file_system_name + '/' + new_dir_name)

    except Exception as e:
     print(e) 
```

## <a name="delete-a-directory"></a>디렉터리 삭제

**Delete_directory DataLakeDirectoryClient** 메서드를 호출 하 여 디렉터리를 삭제 합니다.

다음 예제에서는 `my-directory`라는 디렉터리를 삭제합니다.  

```python
def delete_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")
        directory_client = file_system_client.get_directory_client("my-directory")

        directory_client.delete_directory()
    except Exception as e:
     print(e) 
```

## <a name="manage-directory-permissions"></a>디렉터리 권한 관리

**Get_access_control DataLakeDirectoryClient** 메서드를 호출 하 여 디렉터리의 acl (액세스 제어 목록)을 가져오고 **set_access_control DataLakeDirectoryClient** 메서드를 호출 하 여 acl을 설정 합니다.

> [!NOTE]
> 응용 프로그램에서 Azure Active Directory (Azure AD)를 사용 하 여 액세스 권한을 부여 하는 경우 응용 프로그램에서 액세스 권한을 부여 하는 데 사용 하는 보안 주체가 [저장소 Blob 데이터 소유자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 권한이 적용되는 방식과 권한 변경의 영향에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조하세요.

이 예제에서는 이름이 인 디렉터리의 ACL을 가져오고 설정 합니다 `my-directory` . 문자열은 `rwxr-xrw-` 소유 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 및 쓰기 권한을 부여 합니다.

```python
def manage_directory_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_dir_permissions = "rwxr-xrw-"
        
        directory_client.set_access_control(permissions=new_dir_permissions)
        
        acl_props = directory_client.get_access_control()
        
        print(acl_props['permissions'])
    
    except Exception as e:
     print(e) 
```

컨테이너의 루트 디렉터리에 대 한 ACL을 가져오고 설정할 수도 있습니다. 루트 디렉터리를 가져오려면 **_Get_root_directory_client FileSystemClient** 메서드를 호출 합니다.

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드 

먼저 **DataLakeFileClient** 클래스의 인스턴스를 만들어 대상 디렉터리에 파일 참조를 만듭니다. **Append_data DataLakeFileClient** 메서드를 호출 하 여 파일을 업로드 합니다. **Flush_data DataLakeFileClient** 메서드를 호출 하 여 업로드를 완료 해야 합니다.

이 예제에서는 라는 디렉터리에 텍스트 파일을 업로드 `my-directory` 합니다.   

```python
def upload_file_to_directory():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.create_file("uploaded-file.txt")
        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.append_data(data=file_contents, offset=0, length=len(file_contents))

        file_client.flush_data(len(file_contents))

    except Exception as e:
      print(e) 
```

> [!TIP]
> 파일 크기가 큰 경우 코드는 **Append_data DataLakeFileClient** 메서드를 여러 번 호출 해야 합니다. 대신 **Upload_data DataLakeFileClient** 메서드를 사용 하는 것이 좋습니다. 이렇게 하면 단일 호출에서 전체 파일을 업로드할 수 있습니다. 

## <a name="upload-a-large-file-to-a-directory"></a>디렉터리에 용량이 많은 파일 업로드

DataLakeFileClient 메서드를 사용 하 여 **Append_data DataLakeFileClient** 메서드를 여러 번 호출 하지 않고도 **upload_data** 메서드를 사용 하 여 많은 파일을 업로드할 수 있습니다.

```python
def upload_file_to_directory_bulk():
    try:

        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        local_file = open("C:\\file-to-upload.txt",'r')

        file_contents = local_file.read()

        file_client.upload_data(file_contents, overwrite=True)

    except Exception as e:
      print(e) 
```

## <a name="manage-file-permissions"></a>파일 사용 권한 관리

**Get_access_control DataLakeFileClient** 메서드를 호출 하 여 파일의 acl (액세스 제어 목록)을 가져오고 **set_access_control DataLakeFileClient** 메서드를 호출 하 여 acl을 설정 합니다.

> [!NOTE]
> 응용 프로그램에서 Azure Active Directory (Azure AD)를 사용 하 여 액세스 권한을 부여 하는 경우 응용 프로그램에서 액세스 권한을 부여 하는 데 사용 하는 보안 주체가 [저장소 Blob 데이터 소유자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 권한이 적용되는 방식과 권한 변경의 영향에 대한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조하세요.

이 예제에서는 라는 파일의 ACL을 가져오고 설정 합니다 `my-file.txt` . 문자열은 `rwxr-xrw-` 소유 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 및 쓰기 권한을 부여 합니다.

```python
def manage_file_permissions():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        file_client = directory_client.get_file_client("uploaded-file.txt")

        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])
        
        new_file_permissions = "rwxr-xrw-"
        
        file_client.set_access_control(permissions=new_file_permissions)
        
        acl_props = file_client.get_access_control()
        
        print(acl_props['permissions'])

    except Exception as e:
     print(e) 
```

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드 

쓰기용 로컬 파일을 엽니다. 그런 다음 다운로드 하려는 파일을 나타내는 **DataLakeFileClient** 인스턴스를 만듭니다. **Read_file DataLakeFileClient** 를 호출 하 여 파일에서 바이트를 읽은 다음 해당 바이트를 로컬 파일에 기록 합니다. 

```python
def download_file_from_directory():
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        directory_client = file_system_client.get_directory_client("my-directory")
        
        local_file = open("C:\\file-to-download.txt",'wb')

        file_client = directory_client.get_file_client("uploaded-file.txt")

        download = file_client.download_file()

        downloaded_bytes = download.readall()

        local_file.write(downloaded_bytes)

        local_file.close()

    except Exception as e:
     print(e)
```
## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

**Get_paths FileSystemClient** 메서드를 호출 하 고 결과를 열거 하 여 디렉터리 내용을 나열 합니다.

이 예에서는 라는 디렉터리에 있는 각 하위 디렉터리 및 파일의 경로를 출력 합니다 `my-directory` .

```python
def list_directory_contents():
    try:
        
        file_system_client = service_client.get_file_system_client(file_system="my-file-system")

        paths = file_system_client.get_paths(path="my-directory")

        for path in paths:
            print(path.name + '\n')

    except Exception as e:
     print(e) 
```

## <a name="set-an-acl-recursively-preview"></a>재귀적으로 ACL 설정 (미리 보기)

각 자식 항목에 대해 개별적으로 변경할 필요 없이 부모 디렉터리의 기존 자식 항목에서 Acl을 재귀적으로 추가, 업데이트 및 제거할 수 있습니다. 자세한 내용은 [Azure Data Lake Storage Gen2에 대해 재귀적으로 acl (액세스 제어 목록) 설정](recursive-access-control-lists.md)을 참조 하세요.

## <a name="see-also"></a>참조

* [API 참조 설명서](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-storage-file-datalake/12.0.0b5/index.html)
* [패키지(Python 패키지 인덱스)](https://pypi.org/project/azure-storage-file-datalake/)
* [샘플](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
* [Gen1과 Gen2 매핑](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
* [알려진 문제](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-python/issues)
