---
title: Node.js v2용 클라이언트 라이브러리를 사용하여 Azure Storage에 Blob을 만드는 방법
description: 개체(Blob) 저장소에서 저장소 계정 및 컨테이너를 만듭니다. 그런 다음, Node.js v2용 Azure Storage 클라이언트 라이브러리를 사용하여 Azure Storage에 Blob을 업로드하고, Blob을 다운로드하고, 컨테이너에 Blob을 나열합니다.
services: storage
author: tamram
ms.custom: mvc
ms.service: storage
ms.topic: conceptual
ms.date: 02/04/2019
ms.author: tamram
ms.openlocfilehash: 182315c705360d254c3bf342cd9c64ffafa0c021
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62121541"
---
# <a name="how-to-upload-download-and-list-blobs-using-the-client-library-for-nodejs-v2"></a>Node.js SDK v2용 클라이언트 라이브러리를 사용하여 Blob을 업로드, 다운로드 및 나열하는 방법

이 가이드에서는 Node.js v2용 클라이언트 라이브러리를 사용하여 Blob을 업로드 및 다운로드하고 Azure Blob 스토리지와 함께 나열하는 방법에 대해 알아봅니다.

> [!TIP]
> Node.js용 Azure Storage 클라이언트 라이브러리의 최신 버전은 v10입니다. 가능한 경우 클라이언트 라이브러리의 최신 버전을 사용 하는 것이 좋습니다. v10을 사용하여 시작하려면 [빠른 시작: JavaScript용 Azure Storage 클라이언트 라이브러리 v10을 사용하여 Blob 업로드, 다운로드, 나열 및 삭제(미리 보기)](storage-quickstart-blobs-nodejs-v10.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[Azure Portal](https://portal.azure.com/#create/Microsoft.StorageAccount-ARM)에서 Azure Storage 계정을 만듭니다. 저장소 계정을 만드는 데 도움이 필요한 경우 [저장소 계정 만들기](../common/storage-quickstart-create-account.md)를 참조하세요.

## <a name="download-the-sample-application"></a>샘플 애플리케이션 다운로드

[샘플 애플리케이션](https://github.com/Azure-Samples/storage-blobs-node-quickstart.git)은 간단한 Node.js 콘솔 애플리케이션입니다. 시작하려면 다음 명령을 사용하여 리포지토리를 컴퓨터에 복제합니다.

```bash
git clone https://github.com/Azure-Samples/storage-blobs-node-quickstart.git
```

애플리케이션을 열려면 *storage-blobs-node-quickstart* 폴더를 찾아 즐겨찾는 코드 편집 환경에서 엽니다.

[!INCLUDE [storage-copy-connection-string-portal](../../../includes/storage-copy-connection-string-portal.md)]

## <a name="configure-your-storage-connection-string"></a>저장소 연결 문자열 구성

애플리케이션을 실행하기 전에 스토리지 계정에 대한 연결 문자열을 제공해야 합니다. 샘플 리포지토리에는 *.env.example*이라는 파일이 포함되어 있습니다. *.example* 확장명을 제거하여 *.env* 파일이 되도록 이 파일의 이름을 바꿀 수 있습니다. *.env* 파일 내에서 *AZURE_STORAGE_CONNECTION_STRING* 키 뒤에 연결 문자열 값을 추가합니다.

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

이 스크립트의 출력은 다음과 유사합니다.

```bash
Containers:
 - container-one
 - container-two
Container "demo" is created
Blob "quickstart.txt" is uploaded
Local file "./readme.md" is uploaded
Blobs in "demo" container:
 - quickstart.txt
 - readme.md
Blob downloaded blob content: "hello Blob SDK"
Blob "quickstart.txt" is deleted
Container "demo" is deleted
Done
```

이 예제에서 새로운 스토리지 계정을 사용할 경우 “*Containers*”라는 레이블 아래에 나열된 컨테이너 이름이 보이지 않을 수 있습니다.

## <a name="understanding-the-code"></a>코드 이해
첫 번째 식은 환경 변수로 값을 로드하는 데 사용됩니다.

```javascript
if (process.env.NODE_ENV !== 'production') {
    require('dotenv').load();
}
```

*dotenv* 모듈은 로컬에서 디버깅을 위해 앱을 실행할 때 환경 변수를 로드합니다. 값은 *.env*라는 파일에 정의되고, 현재 실행 컨텍스트에 로드됩니다. 프로덕션 컨텍스트에서는 서버 구성이 이러한 값을 제공하므로 스크립트가 "프로덕션" 컨텍스트에서 실행되고 있지 않을 때만 이 코드가 실행됩니다.

```javascript
const path = require('path');
const storage = require('azure-storage');
```

모듈의 용도는 다음과 같습니다. 

*.env*라는 파일을 현재 실행 컨텍스트로
- *path*는 Blob Storage에 업로드할 파일의 절대 파일 경로를 결정하는 데 필요합니다.
- *azure-storage*는 Node.js용 [Azure Storage 클라이언트 라이브러리](https://docs.microsoft.com/javascript/api/azure-storage) 모듈입니다.

다음으로, **blobService** 변수는 Azure Blob Service의 새 인스턴스로 초기화됩니다.

```javascript
const blobService = storage.createBlobService();
```

다음 구현에서는 [Azure Storage API](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest)의 콜백 특성을 간소화하기 위해 각 *blobService* 함수가 *Promise*에 래핑되어 JavaScript의 *async* 함수와 *await* 연산자에 액세스할 수 있습니다. 각 함수에 대한 응답이 성공적으로 반환되면, 프라미스에서 작업과 관련된 메시지와 함께 관련 데이터를 사용하여 확인합니다.

### <a name="list-containers"></a>컨테이너 나열

*listContainers* 함수는 그룹의 컨테이너 컬렉션을 반환하는 [listContainersSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest)를 호출합니다.

```javascript
const listContainers = async () => {
    return new Promise((resolve, reject) => {
        blobService.listContainersSegmented(null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} containers`, containers: data.entries });
            }
        });
    });
};
```

그룹의 크기는 [ListContainersOptions](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.listcontaineroptions?view=azure-node-latest)를 통해 구성할 수 있습니다. *listContainersSegmented*를 호출하면 Blob 메타데이터가 [ContainerResult](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.containerresult?view=azure-node-latest) 인스턴스의 배열로 반환됩니다. 결과는 5,000개의 증분 일괄 처리(세그먼트)로 반환됩니다. 컨테이너에 5,000개보다 많은 Blob이 있는 경우 *continuationToken*에 대한 값이 결과에 포함됩니다. Blob 컨테이너에서 후속 세그먼트를 나열하려면 연속 토큰을 두 번째 인수로 *listContainersSegment*에 다시 전달할 수 있습니다.

### <a name="create-a-container"></a>컨테이너 만들기

*createContainer* 함수는 [createContainerIfNotExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest)를 호출하고 Blob에 적절한 액세스 수준을 설정합니다.

```javascript
const createContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.createContainerIfNotExists(containerName, { publicAccessLevel: 'blob' }, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' created` });
            }
        });
    });
};
```

**createContainerIfNotExists**에 대한 두 번째 매개 변수(*options*)는 [publicAccessLevel](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest)에 대한 값을 수락합니다. *publicAccessLevel*에 대한 *blob* 값은 특정 Blob 데이터가 공개적으로 노출되도록 지정합니다. 이 설정은 *컨테이너* 수준 액세스와 달리 컨테이너의 내용을 나열하는 기능을 부여합니다.

**createContainerIfNotExists**를 사용하면 컨테이너가 이미 있을 때 애플리케이션에서 오류를 반환하지 않고 *createContainer* 명령을 여러 번 실행할 수 있습니다. 프로덕션 환경에서는 애플리케이션 전체에서 동일한 컨테이너가 사용되므로 **createContainerIfNotExists**만 호출하는 경우가 많습니다. 이러한 경우 포털 또는 Azure CLI를 통해 컨테이너를 미리 만들 수 있습니다.

### <a name="upload-text"></a>텍스트 업로드

*uploadString* 함수는 [createBlockBlobFromText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest)를 호출하여 Blob 컨테이너에 임의 문자열을 쓰거나 덮어씁니다).

```javascript
const uploadString = async (containerName, blobName, text) => {
    return new Promise((resolve, reject) => {
        blobService.createBlockBlobFromText(containerName, blobName, text, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Text "${text}" is written to blob storage` });
            }
        });
    });
};
```
### <a name="upload-a-local-file"></a>로컬 파일 업로드

*uploadLocalFile* 함수는 [createBlockBlobFromLocalFile](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromlocalfile-string--string--string--errororresult-blobresult--)을 사용하여 파일을 파일 시스템에서 Blob Storage에 업로드하고 쓰거나 덮어씁니다. 

```javascript
const uploadLocalFile = async (containerName, filePath) => {
    return new Promise((resolve, reject) => {
        const fullPath = path.resolve(filePath);
        const blobName = path.basename(filePath);
        blobService.createBlockBlobFromLocalFile(containerName, blobName, fullPath, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Local file "${filePath}" is uploaded` });
            }
        });
    });
};
```
콘텐츠를 Blob에 업로드하는 데 사용할 수 있는 다른 방법은 [text](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest-string--string--string---buffer--errororresult-blobresult--) 및 [streams](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#createblockblobfromstream-string--string--stream-readable--number--errororresult-blobresult--)를 사용하는 것입니다. 파일이 Blob 스토리지에 업로드되었는지 확인하려면 [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)를 사용하여 계정의 데이터를 확인하면 됩니다.

### <a name="list-the-blobs"></a>Blob 나열

*listBlobs* 함수는 [listBlobsSegmented](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#listblobssegmented-string--continuationtoken--errororresult-listblobsresult--) 메서드를 호출하여 컨테이너의 Blob 메타데이터 목록을 반환합니다. 

```javascript
const listBlobs = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.listBlobsSegmented(containerName, null, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `${data.entries.length} blobs in '${containerName}'`, blobs: data.entries });
            }
        });
    });
};
```

*listBlobsSegmented*를 호출하면 Blob 메타데이터가 [BlobResult](https://docs.microsoft.com/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice.blobresult?view=azure-node-latest) 인스턴스의 배열로 반환됩니다. 결과는 5,000개의 증분 일괄 처리(세그먼트)로 반환됩니다. 컨테이너에 5,000개보다 많은 Blob이 있는 경우 **continuationToken**에 대한 값이 결과에 포함됩니다. Blob 컨테이너에서 후속 세그먼트를 나열하려면 연속 토큰을 두 번째 인수로 **listBlobSegmented**에 다시 전달할 수 있습니다.

### <a name="download-a-blob"></a>Blob 다운로드

*downloadBlob* 함수는 [getBlobToText](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest)를 사용하여 Blob의 내용을 지정된 절대 파일 경로로 다운로드합니다.

```javascript
const downloadBlob = async (containerName, blobName) => {
    const dowloadFilePath = path.resolve('./' + blobName.replace('.txt', '.downloaded.txt'));
    return new Promise((resolve, reject) => {
        blobService.getBlobToText(containerName, blobName, (err, data) => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Blob downloaded "${data}"`, text: data });
            }
        });
    });
};
```
여기에 표시된 구현은 원본을 변경하고, Blob의 콘텐츠를 문자열로 반환합니다. 또한 Blob를 [스트림](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest) 으로 다운로드하거나 [로컬 파일](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest)에 직접 다운로드할 수도 있습니다.

### <a name="delete-a-blob"></a>Blob 삭제

*deleteBlob* 함수는 [deleteBlobIfExists](/javascript/api/azure-storage/azurestorage.services.blob.blobservice.blobservice?view=azure-node-latest#deleteblobifexists-string--string--errororresult-boolean--) 함수를 호출합니다. 이름에서 알 수 있듯이 이 함수는 Blob이 이미 삭제된 오류를 반환하지 않습니다.

```javascript
const deleteBlob = async (containerName, blobName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteBlobIfExists(containerName, blobName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Block blob '${blobName}' deleted` });
            }
        });
    });
};
```

### <a name="delete-a-container"></a>컨테이너 삭제

Blob Service에서 *deleteContainer* 메소드를 호출하고 컨테이너 이름에 전달하면 컨테이너가 삭제됩니다.

```javascript
const deleteContainer = async (containerName) => {
    return new Promise((resolve, reject) => {
        blobService.deleteContainer(containerName, err => {
            if (err) {
                reject(err);
            } else {
                resolve({ message: `Container '${containerName}' deleted` });
            }
        });
    });
};
```

### <a name="calling-code"></a>코드 호출

JavaScript의 *async/await* 구문을 지원하기 위해 모든 호출 코드는 *execute*라는 함수에 래핑됩니다. 그런 다음 execute가 호출되고 promise로 처리됩니다.

```javascript
async function execute() {
    // commands 
}

execute().then(() => console.log("Done")).catch((e) => console.log(e));
```
다음 코드는 모두 `// commands` 주석이 있는 execute 함수 내에서 실행됩니다.

먼저 이름, 샘플 콘텐츠를 할당하고 Blob Storage에 업로드할 로컬 파일을 가리키는 관련 변수가 선언됩니다.

```javascript
const containerName = "demo";
const blobName = "quickstart.txt";
const content = "hello Node SDK";
const localFilePath = "./readme.md";
let response;
```

저장소 계정의 컨테이너를 나열하기 위해 listContainers 함수가 호출되고 반환된 컨테이너 목록이 출력 창에 기록됩니다.

```javascript
console.log("Containers:");
response = await listContainers();
response.containers.forEach((container) => console.log(` -  ${container.name}`));
```

컨테이너 목록이 표시되면 Array *findIndex* 메서드를 사용하여 만들려는 컨테이너가 이미 있는지 확인할 수 있습니다. 컨테이너가 없으면 컨테이너가 생성됩니다.

```javascript
const containerDoesNotExist = response.containers.findIndex((container) => container.name === containerName) === -1;

if (containerDoesNotExist) {
    await createContainer(containerName);
    console.log(`Container "${containerName}" is created`);
}
```
다음으로, 문자열 및 로컬 파일이 Blob Storage에 업로드됩니다.

```javascript
await uploadString(containerName, blobName, content);
console.log(`Blob "${blobName}" is uploaded`);

response = await uploadLocalFile(containerName, localFilePath);
console.log(response.message);
```
Blob을 나열하는 프로세스는 컨테이너를 나열하는 프로세스와 동일합니다. *listBlobs*를 호출하면 컨테이너의 Blob 배열이 반환되어 출력 창에 기록됩니다.

```javascript
console.log(`Blobs in "${containerName}" container:`);
response = await listBlobs(containerName);
response.blobs.forEach((blob) => console.log(` - ${blob.name}`));
```

Blob을 다운로드하기 위해 응답이 캡처되고 Blob 값에 액세스하는 데 사용됩니다. 응답에서 readableStreamBody가 문자열로 변환되어 출력 창에 기록됩니다.

```javascript
response = await downloadBlob(containerName, blobName);
console.log(`Downloaded blob content: "${response.text}"`);
```

마지막으로, Blob 및 컨테이너가 저장소 계정에서 삭제됩니다.

```javascript
await deleteBlob(containerName, blobName);
console.log(`Blob "${blobName}" is deleted`);

await deleteContainer(containerName);
console.log(`Container "${containerName}" is deleted`);
```

## <a name="clean-up-resources"></a>리소스 정리
저장소 계정에 기록된 모든 데이터는 코드 샘플 끝 부분에서 자동으로 삭제됩니다. 

## <a name="resources-for-developing-nodejs-applications-with-blobs"></a>BLOB을 사용하여 Node.js 애플리케이션을 개발하기 위한 리소스

Blob Storage를 사용하여 Node.js 응용 프로그램을 개발하기 위한 추가 리소스는 다음과 같습니다.

### <a name="binaries-and-source-code"></a>이진 파일 및 소스 코드

- GitHub에서 Azure Storage용 [Node.js 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-storage-node)를 검색 및 설치하세요.

### <a name="client-library-reference-and-samples"></a>클라이언트 라이브러리 참조 및 샘플

- Node.js 클라이언트 라이브러리에 대한 자세한 내용은 [Node.js API 참조](https://docs.microsoft.com/javascript/api/overview/azure/storage)를 참조하세요.
- Node.js 클라이언트 라이브러리를 사용하여 작성된 [Blob Storage 샘플](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=nodejs&term=blob)을 탐색하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Node.js를 사용하여 로컬 디스크와 Azure Blob Storage 간에 파일을 업로드하는 방법을 설명합니다. Blob Storage를 사용하는 방법을 자세히 알아보려면 GitHub 리포지토리로 이동하세요.

> [!div class="nextstepaction"]
> [Node.js용 Microsoft Azure Storage SDK 및 브라우저의 JavaScript](https://github.com/Azure/azure-storage-node)
