---
title: '빠른 시작: 브라우저의 JavaScript v10용 Azure Blob 스토리지'
description: HTML 페이지에서 JavaScript v10 SDK를 사용하여 Blob을 업로드, 나열 및 삭제하는 방법을 알아봅니다.
services: storage
author: mhopkins-msft
ms.custom: mvc
ms.service: storage
ms.author: mhopkins
ms.date: 01/24/2020
ms.topic: quickstart
ms.subservice: blobs
ms.openlocfilehash: 257af309ebdb9080c3cd60b8b89a2c992ecf5145
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906580"
---
<!-- Customer intent: As a web application developer I want to interface with Azure Blob storage entirely on the client so that I can build a SPA application that is able to upload and delete files on blob storage. -->

# <a name="quickstart-manage-blobs-with-javascript-v10-sdk-in-browser"></a>빠른 시작: 브라우저에서 JavaScript v10 SDK를 사용하여 Blob 관리

이 빠른 시작에서는 브라우저에서 완전히 실행되는 JavaScript 코드를 사용하여 Blob을 관리하는 방법을 알아봅니다. Blob은 이미지, 문서, 스트리밍 미디어 및 보관 데이터를 포함하여 대량의 텍스트 또는 이진 데이터를 저장할 수 있는 개체입니다. 필요한 보안 조치를 사용하여 Blob Storage 계정에 대한 보호된 액세스를 보장합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Azure Storage 계정. [스토리지 계정 만들기](../common/storage-account-create.md)
- 로컬 웹 서버입니다. 이 문서에서는 [Node.js](https://nodejs.org)를 사용하여 기본 서버를 엽니다.
- [Visual Studio Code](https://code.visualstudio.com)
- 브라우저 디버깅을 위한 VS Code 코드 확장(예: [Chrome용 디버거](vscode:extension/msjsdiag.debugger-for-chrome) 또는 [Microsoft Edge용 디버거](vscode:extension/msjsdiag.debugger-for-edge)).

## <a name="setting-up-storage-account-cors-rules"></a>스토리지 계정 CORS 규칙 설정

웹 애플리케이션에서 클라이언트의 Blob 스토리지에 액세스하려면 [원본 간 리소스 공유](https://docs.microsoft.com/rest/api/storageservices/cross-origin-resource-sharing--cors--support-for-the-azure-storage-services) 또는 CORS를 사용하도록 계정을 구성해야 합니다.

Azure Portal로 돌아가서 스토리지 계정을 선택합니다. 새 CORS 규칙을 정의하려면 **설정** 섹션으로 이동하여 **CORS** 링크를 클릭합니다. 그런 다음, **추가** 단추를 클릭하여 **CORS 규칙 추가** 창을 엽니다. 이 빠른 시작의 경우 공개 CORS 규칙을 만듭니다.

![Azure Blob Storage 계정 CORS 설정](media/storage-quickstart-blobs-javascript-client-libraries-v10/azure-blob-storage-cors-settings.png)

다음 표에서는 각 CORS 설정을 설명하고 규칙을 정의하는 데 사용되는 값을 설명합니다.

|설정  |값  | Description |
|---------|---------|---------|
| 허용된 원본 | * | 허용 가능한 원본으로 설정된 도메인의 쉼표로 구분된 목록을 허용합니다. 값을 `*`로 설정하면 스토리지 계정에 대한 모든 도메인 액세스가 허용됩니다. |
| 허용되는 동사     | delete, get, head, merge, post, options, put | 스토리지 계정에 대해 실행하도록 허용된 HTTP 동사를 나열합니다. 이 빠른 시작에서는 사용 가능한 옵션을 모두 선택합니다. |
| 허용된 헤더 | * | 스토리지 계정에서 허용하는 요청 헤더(접두 헤더 포함)의 목록을 정의합니다. 값을 `*`로 설정하면 모든 헤더에 액세스할 수 있습니다. |
| 노출된 헤더 | * | 계정에서 허용되는 응답 헤더를 나열합니다. 값을 `*`로 설정하면 계정에서 모든 헤더를 보낼 수 있습니다.  |
| 최대 기간(초) | 86400 | 브라우저에서 실행 전 OPTIONS 요청을 캐시하는 최대 시간입니다. 값이 *86400*이면 하루 동안 캐시를 유지할 수 있습니다. |

> [!IMPORTANT]
> 프로덕션 환경에서 사용하는 모든 설정이 보안 액세스를 유지하기 위해 스토리지 계정에 필요한 최소한의 액세스 권한을 공개하는지 확인하세요. 여기에 설명된 CORS 설정은 관대한 보안 정책을 정의하므로 빠른 시작에 적합합니다. 하지만 이러한 설정은 실제 컨텍스트에서는 적합하지 않습니다.

그런 다음, Azure Cloud Shell을 사용하여 보안 토큰을 만듭니다.

[!INCLUDE [Open the Azure cloud shell](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-shared-access-signature"></a>공유 액세스 서명 만들기

SAS(공유 액세스 서명)는 Blob Storage에 대한 요청을 인증하기 위해 브라우저에서 실행되는 코드에 사용됩니다. SAS를 사용하면 클라이언트는 계정 액세스 키 또는 연결 문자열 없이 스토리지 리소스에 대한 액세스 권한을 부여할 수 있습니다. SAS에 대한 자세한 내용은 [SAS(공유 액세스 서명) 사용](../common/storage-sas-overview.md)을 참조하세요.

SAS는 Azure Cloud Shell, Azure Portal 또는 Azure Storage Explorer를 통해 Azure CLI를 사용하여 만들 수 있습니다. 다음 표에는 CLI를 사용하여 SAS를 생성하는 데 필요한 값을 제공하는 매개 변수가 나와 있습니다.

| 매개 변수      |Description  | 자리 표시자 |
|----------------|-------------|-------------|
| *expiry*       | YYYY-MM-DD 형식의 액세스 토큰 만료 날짜입니다. 빠른 시작에서 사용할 내일 날짜를 입력합니다. | *FUTURE_DATE* |
| *account-name* | 스토리지 계정 이름입니다. 이전 단계에서 따로 설정한 이름을 사용합니다. | *YOUR_STORAGE_ACCOUNT_NAME* |
| *account-key*  | 스토리지 계정 키입니다. 이전 단계에 따로 설정한 키를 사용합니다. | *YOUR_STORAGE_ACCOUNT_KEY* |

각 자리 표시자에 대한 실제 값이 포함된 다음 CLI 명령을 사용하여 JavaScript 코드에서 사용할 수 있는 SAS를 생성합니다.

```azurecli-interactive
az storage account generate-sas \
  --permissions racwdl \
  --resource-types sco \
  --services b \
  --expiry FUTURE_DATE \
  --account-name YOUR_STORAGE_ACCOUNT_NAME \
  --account-key YOUR_STORAGE_ACCOUNT_KEY
```

약간 복잡한 각 매개 변수 뒤에 일련의 값이 있습니다. 이 매개 변수 값은 해당 권한의 첫 문자에서 가져옵니다. 다음 표는 값이 발생한 위치를 설명합니다.

| 매개 변수        | 값   | Description  |
|------------------|---------|---------|
| *사용 권한*    | racwdl  | 이 SAS는 *읽기*, *추가*, *만들기*, *쓰기*, *삭제* 및 *나열* 기능을 허용합니다. |
| *resource-types* | sco     | SAS의 영향을 받는 리소스는 서비스  , 컨테이너  및 개체  입니다. |
| *services*       | b       | SAS의 영향을 받는 서비스는 *Blob* 서비스입니다. |

이제 SAS가 생성되었으므로 다음 단계에서 사용하기 위해 반환 값을 복사하여 어딘가에 저장합니다. Azure CLI 이외의 메서드를 사용하여 SAS를 생성한 경우 초기 `?`가 있으면 이를 제거해야 합니다. 이 문자는 이 항목의 뒷부분에서 SAS를 사용하는 URL 템플릿에 이미 제공된 URL 구분 기호입니다.

> [!IMPORTANT]
> 프로덕션 환경에서는 항상 SSL을 사용하여 SAS 토큰을 전달합니다. 또한 SAS 토큰은 서버에서 생성되어 HTML 페이지로 전송되고 Azure Blob Storage로 다시 전달됩니다. 한 가지 고려할 수 있는 방법은 serverless 함수를 사용하여 SAS 토큰을 생성하는 것입니다. Azure Portal에는 JavaScript 함수로 SAS를 생성하는 기능이 있는 함수 템플릿이 포함되어 있습니다.

## <a name="implement-the-html-page"></a>HTML 페이지 구현

이 섹션에서는 기본 웹 페이지를 만들고, 페이지를 시작하고 디버그하도록 VS Code를 구성합니다. 그러나 시작하기 전에 Node.js를 사용하여 로컬 웹 서버를 시작하고 브라우저에서 요청할 때 해당 페이지를 제공해야 합니다. 다음으로, 다양한 Blob 스토리지 API를 호출하고 그 결과를 페이지에 표시하는 JavaScript 코드를 추가합니다. 또한 이러한 호출의 결과는 [Azure Portal](https://portal.azure.com), [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer) 및 VS Code용 [Azure Storage 확장](vscode:extension/ms-azuretools.vscode-azurestorage)에서 확인할 수 있습니다.

### <a name="set-up-the-web-application"></a>웹 애플리케이션 설정

먼저, *azure-blobs-javascript*라는 새 폴더를 만들고 VS Code에서 엽니다. 그런 다음, VS Code에서 새 파일을 만들고, 다음 HTML을 추가하고, *index.html*로 *azure-blobs-javascript* 폴더에 저장합니다.

```html
<!DOCTYPE html>
<html>

<body>
    <button id="create-container-button">Create container</button>
    <button id="delete-container-button">Delete container</button>
    <button id="select-button">Select and upload files</button>
    <input type="file" id="file-input" multiple style="display: none;" />
    <button id="list-button">List files</button>
    <button id="delete-button">Delete selected files</button>
    <p><b>Status:</b></p>
    <p id="status" style="height:160px; width: 593px; overflow: scroll;" />
    <p><b>Files:</b></p>
    <select id="file-list" multiple style="height:222px; width: 593px; overflow: scroll;" />
</body>

<!-- You'll add code here later in this quickstart. -->

</html>
```

### <a name="configure-the-debugger"></a>디버거 구성

VS Code에서 디버거 확장을 설정하려면 **디버그 > 구성 추가...** 를 차례로 선택한 다음, 앞서의 필수 조건 섹션에서 설치한 확장에 따라 **Chrome** 또는 **Edge**를 선택합니다. 이 작업은 *launch.json* 파일을 만들어 편집기에서 엽니다.

다음으로, 아래와 같이 `url` 값에 `/index.html`이 포함되도록 *launch.json* 파일을 수정합니다.

```json
{
    // Use IntelliSense to learn about possible attributes.
    // Hover to view descriptions of existing attributes.
    // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
    "version": "0.2.0",
    "configurations": [
        {
            "type": "chrome",
            "request": "launch",
            "name": "Launch Chrome against localhost",
            "url": "http://localhost:8080/index.html",
            "webRoot": "${workspaceFolder}"
        }
    ]
}
```

이 구성은 시작할 브라우저와 로드할 URL을 VS Code에 알려줍니다.

### <a name="launch-the-web-server"></a>웹 서버 시작

로컬 Node.js 웹 서버를 시작하려면 **보기 > 터미널**을 차례로 선택하여 VS Code 내에서 콘솔 창을 열고, 다음 명령을 입력합니다.

```console
npx http-server
```

이 명령은 *http-server* 패키지를 설치하고 서버를 시작하여 이전 단계에서 표시된 URL을 포함한 기본 URL을 통해 현재 폴더를 사용할 수 있도록 합니다.

### <a name="start-debugging"></a>디버그 시작

VS Code 디버거가 연결된 브라우저에서 *index.html*을 시작하려면 VS Code에서 **디버그 > 디버깅 시작**을 차례로 선택하거나 F5 키를 누릅니다.

표시된 UI는 아직 아무 작업도 수행하지 않지만 다음 섹션에서 표시된 함수 각각을 구현하는 JavaScript 코드를 추가합니다. 그런 다음, 중단점을 설정하고, 코드에서 일시 중지하면 디버거와 상호 작용할 수 있습니다.

*index.html*을 변경하는 경우 브라우저에서 페이지를 다시 로드하여 변경 내용을 확인해야 합니다. VS Code에서 **디버그 > 디버깅 다시 시작**을 차례로 선택하거나 Ctrl+Shift+F5를 누를 수도 있습니다.

### <a name="add-the-blob-storage-client-library"></a>Blob 스토리지 클라이언트 라이브러리 추가

Blob 스토리지 API에 대한 호출을 사용하도록 설정하려면 먼저 [JavaScript용 Azure Storage SDK - Blob 클라이언트 라이브러리를 다운로드](https://aka.ms/downloadazurestoragejsblob)하고, zip의 내용을 추출한 다음, *azure-storage-blob.js* 파일을 *azure-blobs-javascript* 폴더에 배치합니다.

다음으로, 다음 HTML을 *index.html*의 닫는 `</body>` 태그 뒤에 붙여넣고 자리 표시자 주석을 바꿉니다.

```html
<script src="azure-storage-blob.js" charset="utf-8"></script>

<script>
// You'll add code here in the following sections.
</script>
```

이 코드는 스크립트 파일에 참조를 추가하고, 사용자 고유의 JavaScript 코드를 위한 공간을 제공합니다. 이 빠른 시작을 위해 *azure-storage-blob.js* 스크립트 파일을 사용하여 VS Code에서 열고, 내용을 읽고, 중단점을 설정할 수 있습니다. 프로덕션 환경에서는 zip 파일에도 제공되는 더 간단한 *azure-storage.blob.min.js* 파일을 사용해야 합니다.

각 Blob 스토리지 함수에 대한 자세한 내용은 [참조 설명서](https://docs.microsoft.com/javascript/api/%40azure/storage-blob/index)에서 확인할 수 있습니다. SDK의 일부 함수는 Node.js 또는 브라우저에서만 사용할 수 있습니다.

*azure-storage-blob.js*의 코드는 JavaScript 코드에서 사용하여 Blob 스토리지 API에 액세스하는 데 사용할 수 있는 `azblob`이라는 글로벌 변수를 내보냅니다.

### <a name="add-the-initial-javascript-code"></a>초기 JavaScript 코드 추가

다음으로, 다음 코드를 이전 코드 블록에 표시된 `<script>` 요소에 붙여넣고 자리 표시자 주석을 바꿉니다.

```javascript
const createContainerButton = document.getElementById("create-container-button");
const deleteContainerButton = document.getElementById("delete-container-button");
const selectButton = document.getElementById("select-button");
const fileInput = document.getElementById("file-input");
const listButton = document.getElementById("list-button");
const deleteButton = document.getElementById("delete-button");
const status = document.getElementById("status");
const fileList = document.getElementById("file-list");

const reportStatus = message => {
    status.innerHTML += `${message}<br/>`;
    status.scrollTop = status.scrollHeight;
}
```

이 코드는 다음 코드에서 사용할 각 HTML 요소에 대한 필드를 만들고, 출력을 표시하는 `reportStatus` 함수를 구현합니다.

다음 섹션에서는 이전 블록 뒤에 새 JavaScript 코드 블록을 추가합니다.

### <a name="add-your-storage-account-info"></a>스토리지 계정 정보 추가

다음으로, 스토리지 계정에 액세스하는 코드를 추가하고, 자리 표시자를 계정 이름과 이전 단계에서 생성한 SAS로 바꿉니다.

```javascript
const accountName = "<Add your storage account name>";
const sasString = "<Add the SAS you generated earlier>";
const containerName = "testcontainer";
const containerURL = new azblob.ContainerURL(
    `https://${accountName}.blob.core.windows.net/${containerName}?${sasString}`,
    azblob.StorageURL.newPipeline(new azblob.AnonymousCredential));
```

이 코드는 계정 정보와 SAS를 사용하여 스토리지 컨테이너를 만들고 조작하는 데 유용한 [ContainerURL](https://docs.microsoft.com/javascript/api/@azure/storage-blob/ContainerURL) 인스턴스를 만듭니다.

### <a name="create-and-delete-a-storage-container"></a>스토리지 컨테이너 만들기 및 삭제

다음으로, 해당 단추를 누르면 스토리지 컨테이너를 만들고 삭제하는 코드를 추가합니다.

```javascript
const createContainer = async () => {
    try {
        reportStatus(`Creating container "${containerName}"...`);
        await containerURL.create(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

const deleteContainer = async () => {
    try {
        reportStatus(`Deleting container "${containerName}"...`);
        await containerURL.delete(azblob.Aborter.none);
        reportStatus(`Done.`);
    } catch (error) {
        reportStatus(error.body.message);
    }
};

createContainerButton.addEventListener("click", createContainer);
deleteContainerButton.addEventListener("click", deleteContainer);
```

이 코드는 [Aborter](https://docs.microsoft.com/javascript/api/@azure/storage-blob/aborter) 인스턴스를 사용하지 않고 ContainerURL [create](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#create-containercreateoptions-)(만들기) 및 [delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#delete-containerdeletemethodoptions-)(삭제) 함수를 호출합니다. 이 빠른 시작을 간단히 유지하기 위해 이 코드에서 스토리지 계정이 만들어져 있고 사용하도록 설정되었다고 가정합니다. 프로덕션 코드에서 Aborter 인스턴스를 사용하여 시간 제한 기능을 추가합니다.

### <a name="list-blobs"></a>Blob 나열

다음으로, **파일 나열** 단추를 누르면 스토리지 컨테이너의 내용을 나열하는 코드를 추가합니다.

```javascript
const listFiles = async () => {
    fileList.size = 0;
    fileList.innerHTML = "";
    try {
        reportStatus("Retrieving file list...");
        let marker = undefined;
        do {
            const listBlobsResponse = await containerURL.listBlobFlatSegment(
                azblob.Aborter.none, marker);
            marker = listBlobsResponse.nextMarker;
            const items = listBlobsResponse.segment.blobItems;
            for (const blob of items) {
                fileList.size += 1;
                fileList.innerHTML += `<option>${blob.name}</option>`;
            }
        } while (marker);
        if (fileList.size > 0) {
            reportStatus("Done.");
        } else {
            reportStatus("The container does not contain any files.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

listButton.addEventListener("click", listFiles);
```

이 코드는 루프의 [ContainerURL.listBlobFlatSegment](https://docs.microsoft.com/javascript/api/@azure/storage-blob/containerclient#listblobsflat-containerlistblobsoptions-) 함수를 호출하여 모든 세그먼트가 검색되도록 합니다. 각 세그먼트에 대해 포함된 Blob 항목의 목록을 반복하여 **Files**(파일) 목록을 업데이트합니다.

### <a name="upload-blobs"></a>Blob 업로드

다음으로, **파일 선택 및 업로드** 단추를 누르면 파일을 스토리지 컨테이너에 업로드하는 코드를 추가합니다.

```javascript
const uploadFiles = async () => {
    try {
        reportStatus("Uploading files...");
        const promises = [];
        for (const file of fileInput.files) {
            const blockBlobURL = azblob.BlockBlobURL.fromContainerURL(containerURL, file.name);
            promises.push(azblob.uploadBrowserDataToBlockBlob(
                azblob.Aborter.none, file, blockBlobURL));
        }
        await Promise.all(promises);
        reportStatus("Done.");
        listFiles();
    } catch (error) {
        reportStatus(error.body.message);
    }
}

selectButton.addEventListener("click", () => fileInput.click());
fileInput.addEventListener("change", uploadFiles);
```

이 코드는 **선택 및 업로드 파일** 단추를 숨겨진 `file-input` 요소에 연결합니다. 이 방식으로 단추 `click` 이벤트에서 파일 입력 `click` 이벤트를 트리거하고 파일 선택기를 표시합니다. 파일을 선택하고 대화 상자를 닫으면 `input` 이벤트가 발생하고 `uploadFiles` 함수가 호출됩니다. 이 함수는 선택한 각 파일에 대해 브라우저 전용 [uploadBrowserDataToBlockBlob](https://docs.microsoft.com/javascript/api/@azure/storage-blob/blockblobclient#uploadbrowserdata-blob---arraybuffer---arraybufferview--blockblobparalleluploadoptions-) 함수를 호출합니다. 각 호출에서 Promise(약속)를 반환합니다. 이 약속은 한 번에 모든 파일을 기다릴 수 있도록 목록에 추가되며, 이에 따라 모든 파일을 동시에 업로드합니다.

### <a name="delete-blobs"></a>Blob 삭제

다음으로, **선택한 파일 삭제** 단추를 누르면 스토리지 컨테이너에서 파일을 삭제하는 코드를 추가합니다.

```javascript
const deleteFiles = async () => {
    try {
        if (fileList.selectedOptions.length > 0) {
            reportStatus("Deleting files...");
            for (const option of fileList.selectedOptions) {
                const blobURL = azblob.BlobURL.fromContainerURL(containerURL, option.text);
                await blobURL.delete(azblob.Aborter.none);
            }
            reportStatus("Done.");
            listFiles();
        } else {
            reportStatus("No files selected.");
        }
    } catch (error) {
        reportStatus(error.body.message);
    }
};

deleteButton.addEventListener("click", deleteFiles);
```

이 코드는 [BlobURL.delete](https://docs.microsoft.com/javascript/api/@azure/storage-blob/BlobURL#delete-aborter--iblobdeleteoptions-) 함수를 호출하여 목록에서 선택한 각 파일을 제거합니다. 그런 다음, 앞에서 표시된 `listFiles` 함수를 호출하여 **Files** 목록의 내용을 새로 고칩니다.

### <a name="run-and-test-the-web-application"></a>웹 애플리케이션 실행 및 테스트

이제 페이지를 시작하고 Blob 스토리지의 작동 방법을 자유롭게 실험할 수 있습니다. 오류가 발생하면(예: 컨테이너를 만들기 전에 파일을 나열하려는 경우) **상태** 창에 받은 오류 메시지가 표시됩니다. 또한 JavaScript 코드에서 중단점을 설정하여 스토리지 API에서 반환되는 값을 검사할 수도 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 리소스를 정리하려면 [Azure Portal](https://portal.azure.com)로 이동하여 필수 조건 섹션에서 만든 리소스 그룹을 삭제합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 브라우저 기반 JavaScript에서 Blob 스토리지에 액세스하는 간단한 웹 사이트를 만들었습니다. 웹 사이트 자체를 Blob 스토리지에서 호스팅하는 방법에 대해 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Blob Storage에서 정적 웹 사이트 호스팅](https://docs.microsoft.com/azure/storage/blobs/storage-blob-static-website-host)
