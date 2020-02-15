---
title: Azure Data Lake Storage Gen2에서 Acl & 파일에 대해 JavaScript 사용 (미리 보기)
description: JavaScript 용 Azure Storage Data Lake 클라이언트 라이브러리를 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정의 디렉터리 및 파일 및 디렉터리 ACL (액세스 제어 목록)을 관리 합니다.
author: normesta
ms.service: storage
ms.date: 12/18/2019
ms.author: normesta
ms.topic: conceptual
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: 8fd63adc76422b7fd9978e626208aa90593f8604
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154869"
---
# <a name="use-javascript-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>JavaScript를 사용 하 여 Azure Data Lake Storage Gen2에서 디렉터리, 파일 및 Acl 관리 (미리 보기)

이 문서에서는 JavaScript를 사용 하 여 HNS (계층적 네임 스페이스)를 사용 하도록 설정 된 저장소 계정에서 디렉터리, 파일 및 사용 권한을 만들고 관리 하는 방법을 보여 줍니다. 

> [!IMPORTANT]
> 이 문서에서 설명 하는 JavaScript 라이브러리는 현재 공개 미리 보기로 제공 됩니다.

[패키지 (노드 패키지 관리자)](https://www.npmjs.com/package/@azure/storage-file-datalake) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples) | [피드백 제공](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>필수 조건

> [!div class="checklist"]
> * Azure 구독 [Azure 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.
> * 계층적 네임 스페이스 (HNS)를 사용 하도록 설정 된 저장소 계정입니다. [다음](data-lake-storage-quickstart-create-account.md) 지침에 따라 새로 만듭니다.
> * Node.js 응용 프로그램에서이 패키지를 사용 하는 경우 node.js 8.0.0 이상이 필요 합니다.

## <a name="set-up-your-project"></a>프로젝트 설정

터미널 창을 열고 다음 명령을 입력 하 여 JavaScript 용 Data Lake 클라이언트 라이브러리를 설치 합니다.

```javascript
npm install @azure/storage-file-datalake
```

이 문을 코드 파일의 맨 위에 배치 하 여 `storage-file-datalake` 패키지를 가져옵니다. 

```javascript
const AzureStorageDataLake = require("@azure/storage-file-datalake");
```

## <a name="connect-to-the-account"></a>계정에 연결 

이 문서의 코드 조각을 사용 하려면 저장소 계정을 나타내는 **DataLakeServiceClient** 인스턴스를 만들어야 합니다. 하나를 가져오는 가장 쉬운 방법은 계정 키를 사용 하는 것입니다. 

이 예에서는 계정 키를 사용 하 여 **DataLakeServiceClient** 의 인스턴스를 만듭니다.

```javascript

function GetDataLakeServiceClient(accountName, accountKey) {

  const sharedKeyCredential = 
     new StorageSharedKeyCredential(accountName, accountKey);
  
  const datalakeServiceClient = new DataLakeServiceClient(
      `https://${accountName}.dfs.core.windows.net`, sharedKeyCredential);

  return datalakeServiceClient;             
}      

```
> [!NOTE]
> 이 권한 부여 방법은 node.js 응용 프로그램에 대해서만 작동 합니다. 브라우저에서 코드를 실행 하려는 경우 AD (Azure Active Directory)를 사용 하 여 권한을 부여할 수 있습니다. 이 작업을 수행 하는 방법에 대 한 지침은 JavaScript 추가 정보 파일 [에 대 한 Azure Storage 파일 Data Lake 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/storage-file-datalake) 를 참조 하세요. 

## <a name="create-a-file-system"></a>파일 시스템 만들기

파일 시스템은 파일의 컨테이너 역할을 합니다. **FileSystemClient** 인스턴스를 가져온 다음 **FileSystemClient** 메서드를 호출 하 여 만들 수 있습니다.

이 예에서는 `my-file-system`이라는 파일 시스템을 만듭니다. 

```javascript
async function CreateFileSystem(datalakeServiceClient) {

  const fileSystemName = "my-file-system";
  
  const fileSystemClient = datalakeServiceClient.getFileSystemClient(fileSystemName);

  const createResponse = await fileSystemClient.create();
        
}
```

## <a name="create-a-directory"></a>디렉터리 만들기

**Directoryclient** 인스턴스를 가져온 다음 **directoryclient. create** 메서드를 호출 하 여 디렉터리 참조를 만듭니다.

이 예제에서는 `my-directory` 라는 디렉터리를 파일 시스템에 추가 합니다. 

```javascript
async function CreateDirectory(fileSystemClient) {
   
  const directoryClient = fileSystemClient.getDirectoryClient("my-directory");
  
  await directoryClient.create();

}
```

## <a name="rename-or-move-a-directory"></a>디렉터리 이름 바꾸기 또는 이동

**Directoryclient. rename** 메서드를 호출 하 여 디렉터리 이름을 바꾸거나 이동 합니다. 원하는 디렉터리의 경로를 매개 변수로 전달 합니다. 

이 예에서는 `my-directory-renamed`이름으로 하위 디렉터리의 이름을 바꿉니다.

```javascript
async function RenameDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.move("my-directory-renamed");

}
```

이 예제에서는 이름이 `my-directory-renamed` 인 디렉터리를 `my-directory-2`디렉터리의 하위 디렉터리로 이동 합니다. 

```javascript
async function MoveDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory-renamed"); 
  await directoryClient.move("my-directory-2/my-directory-renamed");      

}
```

## <a name="delete-a-directory"></a>디렉터리 삭제

**Directoryclient. delete** 메서드를 호출 하 여 디렉터리를 삭제 합니다.

이 예에서는 `my-directory`라는 디렉터리를 삭제 합니다.   

```javascript
async function DeleteDirectory(fileSystemClient) {

  const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
  await directoryClient.delete();

}
```

## <a name="manage-a-directory-acl"></a>디렉터리 ACL 관리

이 예제에서는 `my-directory`된 디렉터리의 ACL을 가져온 다음 설정 합니다. 이 예제에서는 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 권한을 부여 합니다.

> [!NOTE]
> 응용 프로그램에서 Azure Active Directory (Azure AD)를 사용 하 여 액세스 권한을 부여 하는 경우 응용 프로그램에서 액세스 권한을 부여 하는 데 사용 하는 보안 주체가 [저장소 Blob 데이터 소유자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 사용 권한을 적용 하는 방법 및 변경의 영향에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조 하세요.

```javascript
async function ManageDirectoryACLs(fileSystemClient) {

    const directoryClient = fileSystemClient.getDirectoryClient("my-directory"); 
    const permissions = await directoryClient.getAccessControl();

    console.log(permissions.acl);

    const acl = [
    {
      accessControlType: "user",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: true
      }
    },
    {
      accessControlType: "group",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: false,
        execute: true
      }
    },
    {
      accessControlType: "other",
      entityId: "",
      defaultScope: false,
      permissions: {
        read: true,
        write: true,
        execute: false
      }

    }

  ];

  await directoryClient.setAccessControl(acl);
}
```

## <a name="upload-a-file-to-a-directory"></a>디렉터리에 파일 업로드

먼저 파일을 읽습니다. 이 예제에서는 node.js `fs` 모듈을 사용 합니다. 그런 다음 **FileClient** 인스턴스를 만든 다음 **FileClient** 메서드를 호출 하 여 대상 디렉터리에 파일 참조를 만듭니다. **FileClient** 메서드를 호출 하 여 파일을 업로드 합니다. **FileClient** 메서드를 호출 하 여 업로드를 완료 해야 합니다.

이 예제에서는 `my-directory`라는 디렉터리에 텍스트 파일을 업로드 합니다.

```javascript
async function UploadFile(fileSystemClient) {

  const fs = require('fs') 

  var content = "";
  
  fs.readFile('mytestfile.txt', (err, data) => { 
      if (err) throw err; 

      content = data.toString();

  }) 
  
  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");
  await fileClient.create();
  await fileClient.append(content, 0, content.length);
  await fileClient.flush(content.length);

}
```

## <a name="manage-a-file-acl"></a>파일 ACL 관리

이 예제에서는 `upload-file.txt`이라는 파일의 ACL을 가져온 다음 설정 합니다. 이 예제에서는 소유 하는 사용자에 게 읽기, 쓰기 및 실행 권한을 부여 하 고 소유 그룹에 읽기 및 실행 권한만 제공 하 고 다른 모든 읽기 권한을 부여 합니다.

> [!NOTE]
> 응용 프로그램에서 Azure Active Directory (Azure AD)를 사용 하 여 액세스 권한을 부여 하는 경우 응용 프로그램에서 액세스 권한을 부여 하는 데 사용 하는 보안 주체가 [저장소 Blob 데이터 소유자 역할](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner)에 할당 되었는지 확인 합니다. ACL 사용 권한을 적용 하는 방법 및 변경의 영향에 대 한 자세한 내용은 [Azure Data Lake Storage Gen2의 액세스 제어](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)를 참조 하세요.

```javascript
async function ManageFileACLs(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt"); 
  const permissions = await fileClient.getAccessControl();

  console.log(permissions.acl);

  const acl = [
  {
    accessControlType: "user",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: true
    }
  },
  {
    accessControlType: "group",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: false,
      execute: true
    }
  },
  {
    accessControlType: "other",
    entityId: "",
    defaultScope: false,
    permissions: {
      read: true,
      write: true,
      execute: false
    }

  }

];

await fileClient.setAccessControl(acl);        
}
```

## <a name="download-from-a-directory"></a>디렉터리에서 다운로드

먼저 다운로드 하려는 파일을 나타내는 **FileSystemClient** 인스턴스를 만듭니다. **FileSystemClient** 메서드를 사용 하 여 파일을 읽습니다. 그런 다음 파일을 작성 합니다. 이 예제에서는 node.js `fs` 모듈을 사용 하 여 해당 작업을 수행 합니다. 

> [!NOTE]
> 이 파일 다운로드 방법은 node.js 응용 프로그램에 대해서만 작동 합니다. 브라우저에서 코드를 실행 하려면 브라우저에서이 작업을 수행 하는 방법에 대 한 예제는 JavaScript 추가 정보 파일 [Azure Storage 파일 Data Lake 클라이언트 라이브러리](https://www.npmjs.com/package/@azure/storage-file-datalake) 를 참조 하세요. 

```javascript
async function DownloadFile(fileSystemClient) {

  const fileClient = fileSystemClient.getFileClient("my-directory/uploaded-file.txt");

  const downloadResponse = await fileClient.read();

  const downloaded = await streamToString(downloadResponse.readableStreamBody);
 
  async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", (data) => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
  }   
  
  const fs = require('fs');

  fs.writeFile('mytestfiledownloaded.txt', downloaded, (err) => {
    if (err) throw err;
  });
}

```

## <a name="list-directory-contents"></a>디렉터리 콘텐츠 나열

이 예에서는 `my-directory`라는 디렉터리에 있는 각 디렉터리와 파일의 이름을 출력 합니다.

```javascript
async function ListFilesInDirectory(fileSystemClient) {
  
  let i = 1;

  let iter = await fileSystemClient.listPaths({path: "my-directory", recursive: true});

  for await (const path of iter) {
    
    console.log(`Path ${i++}: ${path.name}, is directory: ${path.isDirectory}`);
  }

}
```

## <a name="see-also"></a>참고 항목

* [패키지 (노드 패키지 관리자)](https://www.npmjs.com/package/@azure/storage-file-datalake)
* [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-file-datalake/samples)
* [사용자 의견 제공](https://github.com/Azure/azure-sdk-for-java/issues)