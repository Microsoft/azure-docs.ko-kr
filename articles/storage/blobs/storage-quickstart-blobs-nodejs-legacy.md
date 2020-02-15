---
title: '빠른 시작: JavaScript용 Azure Blob 스토리지 클라이언트 라이브러리 v10'
description: JavaScript용 Azure Storage 클라이언트 라이브러리 v10을 통해 Node.js에서 blob 및 컨테이너 만들기, 업로드 및 삭제
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 6a530c96c99a9f22f1943a5b874656bcabd9594b
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906479"
---
# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-nodejs"></a>빠른 시작: Node.js에서 JavaScript v10 SDK를 사용하여 Blob 관리

이 빠른 시작에서는 Node.js를 사용하여 Blob을 관리하는 방법을 알아봅니다. Blob은 이미지, 문서, 스트리밍 미디어 및 보관 데이터를 포함하여 대량의 텍스트 또는 이진 데이터를 저장할 수 있는 개체입니다. Blob을 업로드, 다운로드, 나열 및 삭제하고 컨테이너를 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage 계정. [스토리지 계정 만들기](../common/storage-account-create.md)
- [Node.js](https://nodejs.org/en/download/).

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

이 빠른 시작의 [샘플 애플리케이션](https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git)은 간단한 Node.js 콘솔 애플리케이션입니다. 시작하려면 다음 명령을 사용하여 리포지토리를 컴퓨터에 복제합니다.

```bash
git clone https://github.com/Azure-Samples/azure-storage-js-v10-quickstart.git
```

다음으로, 애플리케이션에 대한 폴더를 변경합니다.

```bash
cd azure-storage-js-v10-quickstart
```

이제 즐겨찾는 코드 편집 환경에서 폴더를 엽니다.

## <a name="configure-your-storage-credentials"></a>스토리지 자격 증명 구성

애플리케이션을 실행하기 전에 스토리지 계정에 대한 보안 자격 증명을 제공해야 합니다. 샘플 리포지토리에는 *.env.example*이라는 파일이 포함되어 있습니다. *.example* 확장명을 제거하여 *.env*라는 파일이 되도록 이 파일의 이름을 바꿉니다. *.env* 파일에서 *AZURE_STORAGE_ACCOUNT_NAME* 및 *AZURE_STORAGE_ACCOUNT_ACCESS_KEY* 키 뒤에 사용자 계정 이름과 액세스 키 값을 추가합니다.

## <a name="install-required-packages"></a>필요한 패키지를 설치합니다.

애플리케이션 디렉터리에서 *npm install*을 실행하여 애플리케이션에 필요한 패키지를 설치합니다.

```bash
npm install
```

## <a name="run-the-sample"></a>샘플 실행

이제 종속성이 설치되었으므로 다음 명령을 발행하여 샘플을 실행할 수 있습니다.

```bash
npm start
```

앱의 출력은 다음 예제와 유사하게 표시됩니다.

```bash
Container "demo" is created
Containers:
 - container-one
 - container-two
 - demo
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme-stream.md
 - readme.md
Blob downloaded blob content: "hello!"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

이 빠른 시작에 새 스토리지 계정을 사용하는 경우 "*Containers:* " 레이블 아래에 나열된 *demo* 컨테이너만 표시될 수 있습니다.

## <a name="understanding-the-code"></a>코드 이해

Azure Blob Storage 네임스페이스에서 다양한 클래스 및 함수를 가져오면 샘플이 시작됩니다. 가져온 각 항목은 각 샘플에서 사용되는 상황에 맞게 설명되어 있습니다.

```javascript
const {
    Aborter,
    BlobURL,
    BlockBlobURL,
    ContainerURL,
    ServiceURL,
    SharedKeyCredential,
    StorageURL,
    uploadStreamToBlockBlob
} = require('@azure/storage-blob');
```

적절한 컨텍스트를 기반으로 하는 환경 변수에서 자격 증명을 읽습니다.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').config();
}
```

*dotenv* 모듈은 로컬에서 디버깅을 위해 앱을 실행할 때 환경 변수를 로드합니다. 값은 *.env*라는 파일에 정의되고, 현재 실행 컨텍스트에 로드됩니다. 프로덕션에서는 서버 구성이 이러한 값을 제공하므로 스크립트가 “프로덕션” 환경에서 실행되고 있지 *않을* 때만 이 코드가 실행됩니다.

파일 시스템과의 인터페이스에 도움이 되도록 모듈의 다음 블록을 가져옵니다.

```javascript
const fs = require('fs');
const path = require('path');
```

이러한 모듈의 목적은 다음과 같습니다. 

- *fs*는 파일 시스템과 작동하는 데 사용되는 네이티브 Node.js 모듈입니다.

- *path*는 Blob Storage에 파일을 업로드할 때 사용되는 절대 파일 경로를 결정하는 데 필요합니다.

다음으로, 환경 변수 값은 상수에서 따로 읽고 설정됩니다.

```javascript
const STORAGE_ACCOUNT_NAME = process.env.AZURE_STORAGE_ACCOUNT_NAME;
const ACCOUNT_ACCESS_KEY = process.env.AZURE_STORAGE_ACCOUNT_ACCESS_KEY;
```
다음 상수 집합은 업로드 작업 중 파일 크기 계산의 의도를 표시하는 데 유용합니다.

```javascript
const ONE_MEGABYTE = 1024 * 1024;
const FOUR_MEGABYTES = 4 * ONE_MEGABYTE;
```

API에 의해 수행된 요청은 지정된 간격 이후의 시간 초과로 설정할 수 있습니다. [Aborter](/javascript/api/%40azure/storage-blob/aborter?view=azure-node-legacy) 클래스는 요청이 시간 초과되는 방식을 관리하는 역할을 담당하며, 이 샘플에서 사용되는 시간 제한을 정의하는 데 다음 상수가 사용됩니다.

```javascript
const ONE_MINUTE = 60 * 1000;
```

### <a name="calling-code"></a>코드 호출

JavaScript의 *async/await* 구문을 지원하기 위해 모든 호출 코드는 *execute*라는 함수에 래핑됩니다. 그런 다음, *execute*가 호출되고 promise로 처리됩니다.

```javascript
async function execute() {
    // commands... 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```

다음 코드는 모두 `// commands...` 주석이 있는 execute 함수 내에서 실행됩니다.

먼저 이름, 샘플 콘텐츠를 할당하고 Blob Storage에 업로드할 로컬 파일을 가리키는 관련 변수가 선언됩니다.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello!";
const localFilePath = "./readme.md";
```

REST API에 요청을 보내는 방식을 관리하는 파이프라인을 만드는 데 계정 자격 증명이 사용됩니다. 파이프라인은 스레드로부터 안전하고 다시 시도 정책, 로깅, HTTP 응답 deserialization 규칙 등에 대한 논리를 지정합니다.

```javascript
const credentials = new SharedKeyCredential(STORAGE_ACCOUNT_NAME, ACCOUNT_ACCESS_KEY);
const pipeline = StorageURL.newPipeline(credentials);
const serviceURL = new ServiceURL(`https://${STORAGE_ACCOUNT_NAME}.blob.core.windows.net`, pipeline);
```

다음 클래스가 이 코드 블록에 사용됩니다.

- [SharedKeyCredential](/javascript/api/%40azure/storage-blob/sharedkeycredential?view=azure-node-legacy) 클래스는 요청 파이프라인에 제공하기 위해 스토리지 계정 자격 증명을 래핑하는 일을 담당합니다.

- [StorageURL](/javascript/api/%40azure/storage-blob/storageurl?view=azure-node-legacy) 클래스는 새 파이프라인을 만듭니다.

- [ServiceURL](/javascript/api/%40azure/storage-blob/serviceurl?view=azure-node-legacy)은 REST API에 사용되는 URL을 모델링합니다. 이 클래스의 인스턴스를 사용하면 컨테이너를 나열하는 등의 작업을 수행하고, 컨텍스트 정보를 제공하여 컨테이너 URL을 생성할 수 있습니다.

스토리지 계정에서 컨테이너 및 blob을 관리하는 데 *ServiceURL*의 인스턴스가 [ContainerURL](/javascript/api/%40azure/storage-blob/containerurl?view=azure-node-legacy) 및 [BlockBlobURL](/javascript/api/%40azure/storage-blob/blockbloburl?view=azure-node-legacy) 인스턴스와 함께 사용됩니다.

```javascript
const containerURL = ContainerURL.fromServiceURL(serviceURL, containerName);
const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, blobName);
```

*containerURL* 및 *blockBlobURL* 변수는 스토리지 계정에서 작동하도록 샘플 전체에서 다시 사용됩니다. 

이 시점에서 컨테이너는 스토리지 계정에 존재하지 않습니다. *ContainerURL*의 인스턴스는 취할 수 있는 URL을 나타냅니다. 이 인스턴스를 사용하면 컨테이너를 만들고 삭제할 수 있습니다. 이 컨테이너의 위치는 다음과 같은 위치와 동일합니다.

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo
```

*blockBlobURL*은 blob 콘텐츠를 업로드, 다운로드 및 삭제할 수 있도록 하는 개별 blob을 관리하는 데 사용됩니다. 여기에 표시되는 URL은 다음 위치와 비슷합니다.

```bash
https://<ACCOUNT_NAME>.blob.core.windows.net/demo/quickstart.txt
```

컨테이너와 마찬가지로 블록 blob은 아직 존재하지 않습니다. *blockBlobURL* 변수는 나중에 콘텐츠를 업로드하여 blob을 만드는 데 사용됩니다.

### <a name="using-the-aborter-class"></a>Aborter 클래스 사용

API에 의해 수행된 요청은 지정된 간격 이후의 시간 초과로 설정할 수 있습니다. *Aborter* 클래스는 요청이 시간 초과되는 방식을 관리하는 일을 담당합니다. 다음 코드는 요청 집합을 30분 동안 실행할 컨텍스트를 만듭니다.

```javascript
const aborter = Aborter.timeout(30 * ONE_MINUTE);
```

Aborters는 사용자에게 다음을 허용하여 요청에 대한 제어를 제공합니다.

- 요청의 일괄 처리를 위한 시간 지정
- 개별 요청이 일괄 처리에서 실행되어야 하는 기간 지정
- 요청을 취소할 수 있도록 허용
- 요청이 모두 시간 초과되지 않도록 *Aborter.none* 정적 멤버 사용

### <a name="create-a-container"></a>컨테이너 만들기

컨테이너를 만들려면 *ContainerURL*의 *create* 메서드를 사용합니다.

```javascript
await containerURL.create(aborter);
console.log(`Container: "${containerName}" is created`);
```

*ContainerURL.fromServiceURL(serviceURL, containerName)* 을 호출할 때 컨테이너의 이름이 정의된 것과 마찬가지로, *create* 메서드를 호출하면 컨테이너가 생성됩니다.

### <a name="show-container-names"></a>컨테이너 이름 표시

계정은 많은 컨테이너를 저장할 수 있습니다. 다음 코드는 많은 컨테이너를 순환할 수 있도록 분할된 방식으로 컨테이너를 나열하는 방법을 설명합니다. *showContainerNames* 함수가 *ServiceURL* 및 *Aborter*의 인스턴스에 전달됩니다.

```javascript
console.log("Containers:");
await showContainerNames(serviceURL, aborter);
```

*showContainerNames* 함수는 *listContainersSegment* 메서드를 사용하여 스토리지 계정에서 컨테이너 이름의 일괄 처리를 요청합니다.

```javascript
async function showContainerNames(aborter, serviceURL) {
    let marker = undefined;

    do {
        const listContainersResponse = await serviceURL.listContainersSegment(aborter, marker);
        marker = listContainersResponse.nextMarker;
        for(let container of listContainersResponse.containerItems) {
            console.log(` - ${ container.name }`);
        }
    } while (marker);
}
```

응답이 반환되면 *containerItems*가 반복되어 이름을 콘솔에 기록합니다. 

### <a name="upload-text"></a>텍스트 업로드

텍스트를 blob에 업로드하려면 *upload* 메서드를 사용합니다.

```javascript
await blockBlobURL.upload(aborter, content, content.length);
console.log(`Blob "${blobName}" is uploaded`);
```

다음에 나온 텍스트 및 해당 길이가 메서드로 전달됩니다.

### <a name="upload-a-local-file"></a>로컬 파일 업로드

로컬 파일을 컨테이너에 업로드하려면 컨테이너 URL 및 파일 경로가 필요합니다.

```javascript
await uploadLocalFile(aborter, containerURL, localFilePath);
console.log(`Local file "${localFilePath}" is uploaded`);
```

*uploadLocalFile* 함수는 *uploadFileToBlockBlob* 함수를 호출합니다. 이는 파일 경로 및 블록 blob의 대상 인스턴스를 인수로 사용합니다.

```javascript
async function uploadLocalFile(aborter, containerURL, filePath) {

    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath);
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    return await uploadFileToBlockBlob(aborter, filePath, blockBlobURL);
}
```

### <a name="upload-a-stream"></a>스트림 업로드

스트림 업로드도 지원됩니다. 이 샘플에서는 업로드 메서드에 전달할 스트림으로 로컬 파일을 엽니다.

```javascript
await uploadStream(containerURL, localFilePath, aborter);
console.log(`Local file "${localFilePath}" is uploaded as a stream`);
```

*uploadStream* 함수는 *uploadStreamToBlockBlob*을 호출하여 스토리지 컨테이너에 스트림을 업로드합니다.

```javascript
async function uploadStream(aborter, containerURL, filePath) {
    filePath = path.resolve(filePath);

    const fileName = path.basename(filePath).replace('.md', '-stream.md');
    const blockBlobURL = BlockBlobURL.fromContainerURL(containerURL, fileName);

    const stream = fs.createReadStream(filePath, {
      highWaterMark: FOUR_MEGABYTES,
    });

    const uploadOptions = {
        bufferSize: FOUR_MEGABYTES,
        maxBuffers: 5,
    };

    return await uploadStreamToBlockBlob(
                    aborter, 
                    stream, 
                    blockBlobURL, 
                    uploadOptions.bufferSize, 
                    uploadOptions.maxBuffers);
}
```

업로드하는 동안 *uploadStreamToBlockBlob*은 다시 시도해야 하는 경우 스트림에서 데이터를 캐시하도록 버퍼를 할당합니다. *maxBuffers* 값은 각 버퍼가 별도 업로드 요청을 만들 때 사용되는 최대 버퍼 수를 지정합니다. 이상적으로 버퍼 수가 높으면 속도도 빠르지만, 메모리 사용 비용도 높아집니다. 업로드 속도는 버퍼 수가 클라이언트 대신 네트워크 또는 디스크로의 병목 전환에 충분할 정도로 높을 때 안정화됩니다.

### <a name="show-blob-names"></a>blob 이름 표시

계정이 여러 컨테이너를 포함할 수 있는 것처럼 각 컨테이너는 잠재적으로 방대한 양의 blob을 포함할 수 있습니다. 컨테이너의 각 blob은 *ContainerURL* 클래스의 인스턴스를 통해 액세스할 수 있습니다.

```javascript
console.log(`Blobs in "${containerName}" container:`);
await showBlobNames(aborter, containerURL);
```

*showBlobNames* 함수는 *listBlobFlatSegment*를 호출하여 컨테이너에서 blob의 일괄 처리를 요청합니다.

```javascript
async function showBlobNames(aborter, containerURL) {
    let marker = undefined;

    do {
        const listBlobsResponse = await containerURL.listBlobFlatSegment(Aborter.none, marker);
        marker = listBlobsResponse.nextMarker;
        for (const blob of listBlobsResponse.segment.blobItems) {
            console.log(` - ${ blob.name }`);
        }
    } while (marker);
}
```

### <a name="download-a-blob"></a>Blob 다운로드

Blob을 만들고 나면, *download* 메서드를 사용하여 콘텐츠를 다운로드할 수 있습니다.

```javascript
const downloadResponse = await blockBlobURL.download(aborter, 0);
const downloadedContent = await streamToString(downloadResponse.readableStreamBody);
console.log(`Downloaded blob content: "${downloadedContent}"`);
```

응답이 스트림으로 반환됩니다. 이 예제에서 스트림은 다음 *streamToString* 도우미 함수를 사용하여 문자열로 변환됩니다.

```javascript
// A helper method used to read a Node.js readable stream into a string
async function streamToString(readableStream) {
    return new Promise((resolve, reject) => {
      const chunks = [];
      readableStream.on("data", data => {
        chunks.push(data.toString());
      });
      readableStream.on("end", () => {
        resolve(chunks.join(""));
      });
      readableStream.on("error", reject);
    });
}
```

### <a name="delete-a-blob"></a>Blob 삭제

*BlockBlobURL* 인스턴스의 *delete* 메서드는 컨테이너에서 blob을 삭제합니다.

```javascript
await blockBlobURL.delete(aborter)
console.log(`Block blob "${blobName}" is deleted`);
```

### <a name="delete-a-container"></a>컨테이너 삭제

*ContainerURL* 인스턴스의 *delete* 메서드는 스토리지 계정에서 컨테이너를 삭제합니다.

```javascript
await containerURL.delete(aborter);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>리소스 정리

스토리지 계정에 기록된 모든 데이터는 코드 샘플 끝 부분에서 자동으로 삭제됩니다. 

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Node.js를 사용하여 Azure Blob Storage에서 blob 및 컨테이너를 관리하는 방법을 설명합니다. 이 SDK 사용에 관해 자세히 알아보려면 GitHub 리포지토리를 참조하세요.

> [!div class="nextstepaction"]
> [JavaScript 리포지토리용 Azure Storage v10 SDK](https://github.com/Azure/azure-storage-js)
> [Azure Storage JavaScript API 참조](/javascript/api/overview/azure/storage?view=azure-node-legacy)
