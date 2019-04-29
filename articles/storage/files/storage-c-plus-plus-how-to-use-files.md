---
title: C++를 사용하여 Azure Files 개발 | Microsoft Docs
description: Azure Files를 사용하여 파일 데이터를 저장하는 C++ 애플리케이션 및 서비스를 개발하는 방법을 알아봅니다.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 09/19/2017
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 424db4435c569475f9463894b5364fc22190689a
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2019
ms.locfileid: "63766401"
---
# <a name="develop-for-azure-files-with-c"></a>C++를 사용하여 Azure Files 개발

[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>이 자습서 정보

이 자습서는 Azure Files에서 기본 작업을 수행하는 방법을 알려줍니다. C++로 작성된 샘플을 통해 공유 및 디렉터리 만들기, 업로드, 목록 및 파일을 삭제하는 방법을 배웁니다. Azure Files를 처음 접하는 경우 다음 섹션에 있는 개념들을 살펴보면 샘플에 대한 이해를 높이는 데 유용할 것입니다.

* Azure 파일 공유 만들기 및 삭제
* 디렉터리 만들기 및 삭제
* Azure 파일 공유의 파일 및 디렉터리 열거
* 파일 업로드, 다운로드 및 삭제
* Azure 파일 공유에 대한 할당량(최대 크기) 설정
* 공유에 정의된 공유 액세스 정책을 사용하는 파일에 대해 공유 액세스 서명(SAS 키) 만들기

> [!Note]  
> Azure Files는 SMB를 통해 액세스할 수 있기 때문에 표준 C++ I/O 클래스 및 함수를 사용하여 Azure 파일 공유에 액세스하는 간단한 애플리케이션을 작성할 수 있습니다. 이 문서에서는 [File REST API](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api)를 사용하여 Azure Files와 통신하는 Azure Storage C++ SDK를 사용하는 애플리케이션을 작성하는 방법에 대해 설명합니다.

## <a name="create-a-c-application"></a>C++ 애플리케이션 만들기

샘플을 빌드하려면 Azure Storage Client Library 2.4.0 for C++를 설치해야 합니다. Azure 저장소 계정도 만들었어야 합니다.

Azure Storage Client 2.4.0 for C++를 설치하려면 다음 방법 중 하나를 사용할 수 있습니다.

* **Linux:** [Azure Storage Client Library for C++ README](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) 페이지의 지침을 따릅니다.
* **Windows:** Visual Studio에서 **도구&gt; NuGet 패키지 관리자 &gt; 패키지 관리자 콘솔**을 클릭합니다. [NuGet 패키지 관리자 콘솔](https://docs.nuget.org/docs/start-here/using-the-package-manager-console) 에 다음 명령을 입력하고 **ENTER**를 누릅니다.
  

```powershell
Install-Package wastorage
```

## <a name="set-up-your-application-to-use-azure-files"></a>Azure Files를 사용하도록 애플리케이션 설정

Azure Files를 조작하려는 C++ 소스 파일의 맨 위에 다음 include 문을 추가합니다.

```cpp
#include <was/storage_account.h>
#include <was/file.h>
```

## <a name="set-up-an-azure-storage-connection-string"></a>Azure 저장소 연결 문자열 설정

File Storage를 사용하려면 Azure 스토리지 계정에 연결해야 합니다. 첫 번째 단계는 저장소 계정에 연결하는 데 사용할 연결 문자열을 구성하는 것입니다. 이를 위해 정적 변수를 정의해 보겠습니다.

```cpp
// Define the connection-string with your values.
const utility::string_t
storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

## <a name="connecting-to-an-azure-storage-account"></a>Azure 저장소 계정에 연결

**cloud_storage_account** 클래스를 사용하여 Storage 계정 정보를 나타낼 수 있습니다. 저장소 연결 문자열에서 저장소 계정 정보를 검색하려면 **구문 분석** 메서드를 사용할 수 있습니다.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);
```

## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기

Azure Files의 모든 파일 및 디렉터리는 **공유**라는 이름의 컨테이너 안에 있습니다. 저장소 계정은 계정 용량이 허용하는 만큼의 공유를 가질 수 있습니다. 공유 및 해당 콘텐츠에 액세스하려면 Azure Files 클라이언트를 사용해야 합니다.

```cpp
// Create the Azure Files client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();
```

Azure Files 클라이언트를 사용하면 공유에 대한 참조를 가져올 수 있습니다.

```cpp
// Get a reference to the file share
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
```

공유를 만들려면 **cloud_file_share** 개체의 **create_if_not_exists** 메서드를 사용합니다.

```cpp
if (share.create_if_not_exists()) {
    std::wcout << U("New share created") << std::endl;
}
```

이 시점에서 **공유**는 **my-sample-share**라는 이름의 공유에 대해 참조를 포함합니다.

## <a name="delete-an-azure-file-share"></a>Azure 파일 공유 삭제

공유 삭제는 cloud_file_share 개체에서 **delete_if_exists** 메서드를 호출하여 수행할 수 있습니다. 다음이 샘플 코드입니다.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// delete the share if exists
share.delete_share_if_exists();
```

## <a name="create-a-directory"></a>디렉터리 만들기

루트 디렉터리에 이들 모두를 포함하는 대신 하위 디렉터리 내에서 파일을 배치하여 저장소를 구성할 수 있습니다. Azure Files를 사용하면 계정이 허용하는 만큼 많은 디렉터리를 만들 수 있습니다. 아래 코드에서는 루트 디렉터리 아래 **my-sample-directory**를 만들고 **my-sample-subdirectory**라는 하위 디렉터리를 만듭니다.

```cpp
// Retrieve a reference to a directory
azure::storage::cloud_file_directory directory = share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Return value is true if the share did not exist and was successfully created.
directory.create_if_not_exists();

// Create a subdirectory.
azure::storage::cloud_file_directory subdirectory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));
subdirectory.create_if_not_exists();
```

## <a name="delete-a-directory"></a>디렉터리 삭제

디렉터리의 삭제는 간단한 작업입니다. 단, 여전히 파일 또는 기타 디렉터리가 포함된 디렉터리는 삭제할 수 없습니다.

```cpp
// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

// Get a reference to the directory.
azure::storage::cloud_file_directory directory =
  share.get_directory_reference(_XPLATSTR("my-sample-directory"));

// Get a reference to the subdirectory you want to delete.
azure::storage::cloud_file_directory sub_directory =
  directory.get_subdirectory_reference(_XPLATSTR("my-sample-subdirectory"));

// Delete the subdirectory and the sample directory.
sub_directory.delete_directory_if_exists();

directory.delete_directory_if_exists();
```

## <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Azure 파일 공유의 파일 및 디렉터리 열거

공유 내에서 파일 및 디렉터리 목록을 가져오는 것은 **cloud_file_directory** 참조에서 **list_files_and_directories**를 호출하여 쉽게 수행할 수 있습니다. 반환된 **list_file_and_directory_item**의 메서드 및 다양한 속성에 액세스하려면 **cloud_file** 개체를 가져오는 **list_file_and_directory_item.as_file** 메서드를 호출하거나, **cloud_file_directory** 개체를 가져오는 **list_file_and_directory_item.as_directory** 메서드를 호출해야 합니다.

다음 코드는 공유의 루트 디렉터리에 있는 각 항목의 URI를 검색하고 출력하는 방법을 보여 줍니다.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

// Output URI of each item.
azure::storage::list_file_and_directory_result_iterator end_of_results;

for (auto it = directory.list_files_and_directories(); it != end_of_results; ++it)
{
    if(it->is_directory())
    {
        ucout << "Directory: " << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
    else if (it->is_file())
    {
        ucout << "File: " << it->as_file().uri().primary_uri().to_string() << std::endl;
    }
}
```

## <a name="upload-a-file"></a>파일 업로드

Azure 파일 공유에는 파일이 상주할 수 있는 최소한의 루트 디렉터리가 포함되어 있습니다. 이 섹션에서는 로컬 저장소에서 공유의 루트 디렉터리로 파일을 업로드하는 방법을 배웁니다.

파일을 업로드하는 첫 번째 단계는 상주해야 하는 디렉터리에 대한 참조를 가져오는 것입니다. 공유 개체의 **get_root_directory_reference** 메서드를 호출하여 가져올 수 있습니다.

```cpp
//Get a reference to the root directory for the share.
azure::storage::cloud_file_directory root_dir = share.get_root_directory_reference();
```

이제 공유의 루트 디렉터리에 대한 참조를 가졌으므로 파일을 업로드할 수 있습니다. 이 예제에서는 파일, 텍스트 및 스트림에서 업로드합니다.

```cpp
// Upload a file from a stream.
concurrency::streams::istream input_stream =
  concurrency::streams::file_stream<uint8_t>::open_istream(_XPLATSTR("DataFile.txt")).get();

azure::storage::cloud_file file1 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));
file1.upload_from_stream(input_stream);

// Upload some files from text.
azure::storage::cloud_file file2 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
file2.upload_text(_XPLATSTR("more text"));

// Upload a file from a file.
azure::storage::cloud_file file4 =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));
file4.upload_from_file(_XPLATSTR("DataFile.txt"));
```

## <a name="download-a-file"></a>파일 다운로드

파일을 다운로드하려면 먼저 파일 참조를 검색한 다음 **download_to_stream** 메서드를 호출하여 파일 콘텐츠를 스트림 개체로 전송하며 이 개체를 로컬 파일에 저장할 수 있습니다. 또는 **download_to_file** 메서드를 사용하여 로컬 파일에 파일 콘텐츠를 다운로드할 수 있습니다. **download_text** 메서드를 사용하여 파일 콘텐츠를 텍스트 문자열로 다운로드할 수 있습니다.

다음 예제에서는 **download_to_stream** 및 **download_text** 메서드를 사용하여 이전 섹션에서 만든 파일을 다운로드하는 방법을 보여 줍니다.

```cpp
// Download as text
azure::storage::cloud_file text_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-2"));
utility::string_t text = text_file.download_text();
ucout << "File Text: " << text << std::endl;

// Download as a stream.
azure::storage::cloud_file stream_file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
stream_file.download_to_stream(output_stream);
std::ofstream outfile("DownloadFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();
outfile.write((char *)&data[0], buffer.size());
outfile.close();
```

## <a name="delete-a-file"></a>파일 삭제

다른 일반적인 Azure Files 작업은 파일 삭제입니다. 다음 코드는 루트 디렉터리 아래 저장된 my-sample-file-3이라는 파일을 삭제합니다.

```cpp
// Get a reference to the root directory for the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

azure::storage::cloud_file_directory root_dir =
  share.get_root_directory_reference();

azure::storage::cloud_file file =
  root_dir.get_file_reference(_XPLATSTR("my-sample-file-3"));

file.delete_file_if_exists();
```

## <a name="set-the-quota-maximum-size-for-an-azure-file-share"></a>Azure 파일 공유에 대한 할당량(최대 크기) 설정

파일 공유를 위한 할당량(또는 최대 크기)을 기가바이트 단위로 설정할 수 있습니다. 또한 공유에 현재 저장된 데이터의 양도 확인할 수 있습니다.

공유에 대한 할당량을 설정하여 공유에 저장되는 파일의 전체 크기를 제한할 수 있습니다. 공유에 있는 파일의 총 크기가 공유에 대해 설정된 할당량을 초과하면 클라이언트는 해당 파일이 비어 있지 않는 한, 기존 파일의 크기를 늘리거나 새 파일을 만들 수 없습니다.

아래 예제에서는 공유에 대한 현재 사용량을 확인하고 공유에 대해 할당량을 설정하는 방법을 보여 줍니다.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client.
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

// Get a reference to the share.
azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));
if (share.exists())
{
    std::cout << "Current share usage: " << share.download_share_usage() << "/" << share.properties().quota();

    //This line sets the quota to 2560GB
    share.resize(2560);

    std::cout << "Quota increased: " << share.download_share_usage() << "/" << share.properties().quota();

}
```

## <a name="generate-a-shared-access-signature-for-a-file-or-file-share"></a>파일 또는 파일 공유에 대한 공유 액세스 서명 생성

파일 공유 또는 개별 파일에 대한 SAS(공유 액세스 서명)를 생성할 수 있습니다. 또한 파일 공유에 대해 공유 액세스 정책을 만들어 공유 액세스 서명을 관리할 수도 있습니다. 공유 액세스 정책을 만들면 노출된 SAS를 해지할 수 있으므로 권장됩니다.

다음 예제에서는 공유에 대해 공유 액세스 정책을 만들고 해당 정책을 사용하여 공유의 파일에 대해 SAS에 대한 제약 조건을 제공합니다.

```cpp
// Parse the connection string for the storage account.
azure::storage::cloud_storage_account storage_account =
  azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the file client and get a reference to the share
azure::storage::cloud_file_client file_client =
  storage_account.create_cloud_file_client();

azure::storage::cloud_file_share share =
  file_client.get_share_reference(_XPLATSTR("my-sample-share"));

if (share.exists())
{
    // Create and assign a policy
    utility::string_t policy_name = _XPLATSTR("sampleSharePolicy");

    azure::storage::file_shared_access_policy sharedPolicy =
      azure::storage::file_shared_access_policy();

    //set permissions to expire in 90 minutes
    sharedPolicy.set_expiry(utility::datetime::utc_now() +
       utility::datetime::from_minutes(90));

    //give read and write permissions
    sharedPolicy.set_permissions(azure::storage::file_shared_access_policy::permissions::write | azure::storage::file_shared_access_policy::permissions::read);

    //set permissions for the share
    azure::storage::file_share_permissions permissions;

    //retrieve the current list of shared access policies
    azure::storage::shared_access_policies<azure::storage::file_shared_access_policy> policies;

    //add the new shared policy
    policies.insert(std::make_pair(policy_name, sharedPolicy));

    //save the updated policy list
    permissions.set_policies(policies);
    share.upload_permissions(permissions);

    //Retrieve the root directory and file references
    azure::storage::cloud_file_directory root_dir =
        share.get_root_directory_reference();
    azure::storage::cloud_file file =
      root_dir.get_file_reference(_XPLATSTR("my-sample-file-1"));

    // Generate a SAS for a file in the share
    //  and associate this access policy with it.
    utility::string_t sas_token = file.get_shared_access_signature(sharedPolicy);

    // Create a new CloudFile object from the SAS, and write some text to the file.
    azure::storage::cloud_file file_with_sas(azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()));
    utility::string_t text = _XPLATSTR("My sample content");
    file_with_sas.upload_text(text);

    //Download and print URL with SAS.
    utility::string_t downloaded_text = file_with_sas.download_text();
    ucout << downloaded_text << std::endl;
    ucout << azure::storage::storage_credentials(sas_token).transform_uri(file.uri().primary_uri()).to_string() << std::endl;

}
```

## <a name="next-steps"></a>다음 단계

Azure Storage에 대한 자세한 내용은 다음 리소스를 살펴보세요.

* [Storage Client Library for C++](https://github.com/Azure/azure-storage-cpp)
* [C++ 형식의 Azure Storage 파일 서비스 샘플](https://github.com/Azure-Samples/storage-file-cpp-getting-started)
* [Azure Storage 탐색기](https://go.microsoft.com/fwlink/?LinkID=822673&clcid=0x409)
* [Azure Storage 설명서](https://azure.microsoft.com/documentation/services/storage/)