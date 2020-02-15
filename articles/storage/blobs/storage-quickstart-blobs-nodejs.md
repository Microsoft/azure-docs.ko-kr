---
title: '빠른 시작: Azure Blob 스토리지 라이브러리 v12 - JavaScript'
description: 이 빠른 시작에서는 JavaScript용 Azure Blob 스토리지 클라이언트 라이브러리버전 12를 사용하여 Blob(개체) 스토리지에서 컨테이너 및 Blob을 만드는 방법을 알아봅니다. 그런 다음, Blob을 로컬 컴퓨터로 다운로드하는 방법과 컨테이너의 모든 Blob을 나열하는 방법을 알아봅니다.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: eabfefbf28b54e4a0a025698f8da48518e7df9bf
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906453"
---
# <a name="quickstart-manage-blobs-with-javascript-v12-sdk-in-nodejs"></a>빠른 시작: Node.js에서 JavaScript v12 SDK를 사용하여 Blob 관리

이 빠른 시작에서는 Node.js를 사용하여 Blob을 관리하는 방법을 알아봅니다. Blob은 이미지, 문서, 스트리밍 미디어 및 보관 데이터를 포함하여 대량의 텍스트 또는 이진 데이터를 저장할 수 있는 개체입니다. Blob을 업로드, 다운로드 및 나열하고 컨테이너를 만들고 삭제합니다.

[API 참조 설명서](/javascript/api/@azure/storage-blob) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob) | [패키지(Node Package Manager)](https://www.npmjs.com/package/@azure/storage-blob) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage 계정. [스토리지 계정 만들기](../common/storage-account-create.md)
- [Node.js](https://nodejs.org/en/download/).

> [!NOTE]
> 이전 SDK 버전을 시작하려면 [빠른 시작: Node.js에서 JavaScript v10 SDK를 사용하여 Blob 관리](storage-quickstart-blobs-nodejs-legacy.md)를 참조하세요.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="setting-up"></a>설치

이 섹션에서는 JavaScript용 Azure Blob 스토리지 클라이언트 라이브러리 v12를 사용하는 프로젝트를 준비합니다.

### <a name="create-the-project"></a>프로젝트 만들기

*blob-quickstart-v12*라는 JavaScript 애플리케이션을 만듭니다.

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 프로젝트에 대한 새 디렉터리를 만듭니다.

    ```console
    mkdir blob-quickstart-v12
    ```

1. 새로 만든 *blob-quickstart-v12* 디렉터리로 전환합니다.

    ```console
    cd blob-quickstart-v12
    ```

1. *package.json*라는 새 텍스트 파일을 만듭니다. 이 파일은 Node.js 프로젝트를 정의합니다. *blob-quickstart-v12* 디렉터리에 이 파일을 저장합니다. 파일 콘텐츠는 다음과 같습니다.

    ```json
    {
        "name": "blob-quickstart-v12",
        "version": "1.0.0",
        "description": "Use the @azure/storage-blob SDK version 12 to interact with Azure Blob storage",
        "main": "blob-quickstart-v12.js",
        "scripts": {
            "start": "node blob-quickstart-v12.js"
        },
        "author": "Your Name",
        "license": "MIT",
        "dependencies": {
            "@azure/storage-blob": "^12.0.0",
            "@types/dotenv": "^4.0.3",
            "dotenv": "^6.0.0"
        }
    }
    ```
    
    원할 경우 `author` 필드에 사용자 고유의 이름을 입력할 수 있습니다.
   
### <a name="install-the-package"></a>패키지 설치

*blob-quickstart-v12* 디렉터리에 있는 상태에서 `npm install` 명령을 사용하여 JavaScript용 Azure Blob 스토리지 클라이언트 라이브러리 패키지를 설치합니다. 이 명령은 *package.json* 파일을 읽고 JavaScript용 Azure Blob 스토리지 클라이언트 라이브러리 v12 패키지와 이 패키지가 종속되는 모든 라이브러리를 설치합니다.

```console
npm install
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. 코드 편집기에서 다른 새 텍스트 파일 열기
1. Azure 및 Node.js 모듈을 로드하는 `require` 호출 추가
1. 기본적인 예외 처리를 포함하여 프로그램의 구조 만들기

    코드는 다음과 같습니다.

    ```javascript
    const { BlobServiceClient } = require('@azure/storage-blob');
    const uuidv1 = require('uuid/v1');
    
    async function main() {
        console.log('Azure Blob storage v12 - JavaScript quickstart sample');
        // Quick start code goes here
    }
    
    main().then(() => console.log('Done')).catch((ex) => console.log(ex.message));
    ```

1. 새 파일을 *blob-quickstart-v12* 디렉터리에 *blob-quickstart-v12.js*로 저장합니다.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>개체 모델

Azure Blob Storage는 대량의 비정형 데이터를 저장하도록 최적화되어 있습니다. 비정형 데이터는 텍스트 또는 이진 데이터와 같은 특정 데이터 모델 또는 정의를 따르지 않는 데이터입니다. Blob Storage는 다음 세 가지 유형의 리소스를 제공합니다.

* 스토리지 계정
* 스토리지 계정의 컨테이너
* 컨테이너의 blob

다음 다이어그램에서는 이러한 리소스 간의 관계를 보여줍니다.

![Blob Storage 아키텍처 다이어그램](./media/storage-blob-introduction/blob1.png)

다음 JavaScript 클래스를 사용하여 이러한 리소스와 상호 작용합니다.

* [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient): `BlobServiceClient` 클래스를 사용하여 Azure Storage 리소스 및 blob 컨테이너를 조작할 수 있습니다.
* [ContainerClient](/javascript/api/@azure/storage-blob/containerclient): `ContainerClient` 클래스를 사용하여 Azure Storage 컨테이너 및 해당 blob을 조작할 수 있습니다.
* [BlobClient](/javascript/api/@azure/storage-blob/blobclient): `BlobClient` 클래스를 사용하여 Azure Storage blob을 조작할 수 있습니다.

## <a name="code-examples"></a>코드 예제

이 예제 코드 조각은 JavaScript용 Azure Blob 스토리지 클라이언트 라이브러리를 사용하여 다음을 수행하는 방법을 보여 줍니다.

* [연결 문자열 가져오기](#get-the-connection-string)
* [컨테이너 만들기](#create-a-container)
* [컨테이너에 Blob 업로드](#upload-blobs-to-a-container)
* [컨테이너의 Blob 나열](#list-the-blobs-in-a-container)
* [Blob 다운로드](#download-blobs)
* [컨테이너 삭제](#delete-a-container)

### <a name="get-the-connection-string"></a>연결 문자열 가져오기

아래 코드에서는 [스토리지 연결 문자열 구성](#configure-your-storage-connection-string) 섹션에서 만든 환경 변수에서 스토리지 계정에 대한 연결 문자열을 검색합니다.

다음 코드를 `main` 함수 내에 추가합니다.

```javascript
// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
const AZURE_STORAGE_CONNECTION_STRING = process.env.AZURE_STORAGE_CONNECTION_STRING;
```

### <a name="create-a-container"></a>컨테이너 만들기

새 컨테이너의 이름을 결정합니다. 아래 코드는 컨테이너 이름이 고유해지도록 이름에 UUID 값을 추가합니다.

> [!IMPORTANT]
> 컨테이너 이름은 소문자여야 합니다. 컨테이너 및 Blob 이름 지정에 대한 자세한 내용은 [컨테이너, Blob, 메타데이터 이름 지정 및 참조](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)를 참조하세요.

[fromConnectionString](/javascript/api/@azure/storage-blob/blobserviceclient#fromconnectionstring-string--storagepipelineoptions-) 메서드를 호출하여 [BlobServiceClient](/javascript/api/@azure/storage-blob/blobserviceclient) 클래스의 인스턴스를 만듭니다. 그런 다음, [getContainerClient](/javascript/api/@azure/storage-blob/blobserviceclient#getcontainerclient-string-) 메서드를 호출하여 컨테이너에 대한 참조를 가져옵니다. 마지막으로 [create](/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-)를 호출하여 스토리지 계정에 컨테이너를 실제로 만듭니다.

이 코드를 `main` 함수의 끝에 추가합니다.

```javascript
// Create the BlobServiceClient object which will be used to create a container client
const blobServiceClient = await BlobServiceClient.fromConnectionString(AZURE_STORAGE_CONNECTION_STRING);

// Create a unique name for the container
const containerName = 'quickstart' + uuidv1();

console.log('\nCreating container...');
console.log('\t', containerName);

// Get a reference to a container
const containerClient = await blobServiceClient.getContainerClient(containerName);

// Create the container
const createContainerResponse = await containerClient.create();
console.log("Container was created successfully. requestId: ", createContainerResponse.requestId);
```

### <a name="upload-blobs-to-a-container"></a>컨테이너에 Blob 업로드

다음 코드 조각을 실행합니다.

1. Blob에 업로드할 텍스트 문자열을 만듭니다.
1. [컨테이너 만들기](#create-a-container) 섹션에서 [ContainerClient](/javascript/api/@azure/storage-blob/containerclient)에 대해 [getBlockBlobClient](/javascript/api/@azure/storage-blob/containerclient#getblockblobclient-string-) 메서드를 호출하여 [BlockBlobClient](/javascript/api/@azure/storage-blob/blockblobclient) 개체에 대한 참조를 가져옵니다.
1. [upload](/javascript/api/@azure/storage-blob/blockblobclient#upload-httprequestbody--number--blockblobuploadoptions-) 메서드를 호출하여 텍스트 문자열 데이터를 blob에 업로드합니다.

이 코드를 `main` 함수의 끝에 추가합니다.

```javascript
// Create a unique name for the blob
const blobName = 'quickstart' + uuidv1() + '.txt';

// Get a block blob client
const blockBlobClient = containerClient.getBlockBlobClient(blobName);

console.log('\nUploading to Azure storage as blob:\n\t', blobName);

// Upload data to the blob
const data = 'Hello, World!';
const uploadBlobResponse = await blockBlobClient.upload(data, data.length);
console.log("Blob was uploaded successfully. requestId: ", uploadBlobResponse.requestId);
```

### <a name="list-the-blobs-in-a-container"></a>컨테이너의 Blob 나열

[listBlobsFlat](/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) 메서드를 호출하여 컨테이너의 blob을 나열합니다. 이 경우 하나의 Blob만 컨테이너에 추가되었으므로 나열된 작업은 하나의 해당 Blob만 반환합니다.

이 코드를 `main` 함수의 끝에 추가합니다.

```javascript
console.log('\nListing blobs...');

// List the blob(s) in the container.
for await (const blob of containerClient.listBlobsFlat()) {
    console.log('\t', blob.name);
}
```

### <a name="download-blobs"></a>Blob 다운로드

[download](/javascript/api/@azure/storage-blob/blockblobclient#download-undefined---number--undefined---number--blobdownloadoptions-) 메서드를 호출하여 이전에 만든 blob을 다운로드합니다. 예제 코드에는 Node.js 판독 가능 스트림을 문자열로 읽는 데 사용되는 `streamToString`라는 도우미 함수가 포함되어 있습니다.

이 코드를 `main` 함수의 끝에 추가합니다.

```javascript
// Get blob content from position 0 to the end
// In Node.js, get downloaded data by accessing downloadBlockBlobResponse.readableStreamBody
// In browsers, get downloaded data by accessing downloadBlockBlobResponse.blobBody
const downloadBlockBlobResponse = await blockBlobClient.download(0);
console.log('\nDownloaded blob content...');
console.log('\t', await streamToString(downloadBlockBlobResponse.readableStreamBody));
```

`main` 함수 *뒤에* 이 도우미 함수를 추가합니다.

```javascript
// A helper function used to read a Node.js readable stream into a string
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
```

### <a name="delete-a-container"></a>컨테이너 삭제

다음 코드는 [delete](/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-) 메서드로 전체 컨테이너를 제거하여 앱이 만든 리소스를 정리합니다. 원하는 경우 로컬 파일을 삭제할 수도 있습니다.

이 코드를 `main` 함수의 끝에 추가합니다.

```javascript
console.log('\nDeleting container...');

// Delete container
const deleteContainerResponse = await containerClient.delete();
console.log("Container was deleted successfully. requestId: ", deleteContainerResponse.requestId);
```

## <a name="run-the-code"></a>코드 실행

이 앱은 텍스트 문자열을 만들고 Blob 스토리지에 업로드합니다. 그런 다음, 컨테이너의 blob을 나열하고, blob을 다운로드하고, 다운로드한 데이터를 표시합니다.

콘솔 프롬프트에서 *blob-quickstart-v12.py* 파일이 포함된 디렉터리로 이동한 후 다음 `node` 명령을 실행하여 앱을 실행합니다.

```console
node blob-quickstart-v12.js
```

앱의 출력은 다음 예제 출력과 유사합니다.

```output
Azure Blob storage v12 - JavaScript quickstart sample

Creating container...
         quickstart4a0780c0-fb72-11e9-b7b9-b387d3c488da

Uploading to Azure Storage as blob:
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Listing blobs...
         quickstart4a3128d0-fb72-11e9-b7b9-b387d3c488da.txt

Downloaded blob content...
         Hello, World!

Deleting container...
Done
```

디버거에서 코드를 단계별로 실행하고 프로세스 전체에서 [Azure Portal](https://portal.azure.com)을 확인합니다. 컨테이너가 생성되고 있는지 확인합니다. 컨테이너 내에서 blob을 열고 콘텐츠를 볼 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 JavaScript를 사용하여 Blob을 업로드, 다운로드 및 나열하는 방법을 배웠습니다.

자습서, 샘플, 빠른 시작 및 기타 설명서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [JavaScript용 Azure 설명서](/azure/javascript/)

* 자세한 내용은 [JavaScript용 Azure Blob 스토리지 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/storage/storage-blob)를 참조하세요.
* Blob 스토리지 샘플 앱을 보려면 [Azure Blob 스토리지 클라이언트 라이브러리 v12 JavaScript 샘플](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/storage/storage-blob/samples)을 계속 진행하세요.
