---
title: 모듈의 Blob Storage를 디바이스에 배포 - Azure IoT Edge
description: 에지에 데이터를 저장하도록 IoT Edge 디바이스에 Azure Blob Storage 모듈을 배포합니다.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: f766a77ee55351e498a379146826ba1d5507bc93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "103201178"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>IoT Edge 모듈의 Azure Blob Storage를 디바이스에 배포

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge 디바이스에 모듈을 배포하는 여러 가지 방법이 있는데, 모든 방법이 IoT Edge 모듈의 Azure Blob Storage에서 작동합니다. 가장 간단한 두 가지 방법은 Azure Portal 또는 Visual Studio Code 템플릿을 사용하는 것입니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)
- IoT Edge 디바이스.

  IoT Edge 디바이스를 설정하지 않은 경우 Azure 가상 머신에서 만들 수 있습니다. 빠른 시작 문서 중 하나에 있는 단계에 따라 [가상 Linux 디바이스를 만들거나](quickstart-linux.md) [가상 Windows 디바이스를 만듭니다](quickstart.md).

- Visual Studio Code에서 배포하는 경우 [Visual Studio Code](https://code.visualstudio.com/) 및 [Azure IoT 도구](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

## <a name="deploy-from-the-azure-portal"></a>Azure Portal에서 배포

Azure Portal에서는 배포 매니페스트를 만들고 IoT Edge 디바이스에 배포를 푸시하는 방법을 안내합니다.

### <a name="select-your-device"></a>디바이스 선택

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 메뉴에서 **IoT Edge** 를 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. **모듈 설정** 을 선택합니다.

### <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. Azure Portal에는 배포 매니페스트를 만들 수 있도록 안내하는 마법사를 제공합니다. 마법사는 **모듈**, **경로** 및 **검토 + 만들기** 탭으로 구성된 세 단계를 제공합니다.

#### <a name="add-modules"></a>모듈 추가

1. 페이지의 **IoT Edge 모듈** 섹션에서 **추가** 드롭다운을 클릭하고 **IoT Edge 모듈** 을 선택하면 **IoT Edge 모듈 추가** 페이지가 표시됩니다.

2. **모듈 설정** 탭에서 모듈의 이름을 입력한 다음 컨테이너 이미지 URI를 지정합니다.

   예:
  
   - **IoT Edge 모듈 이름**: `azureblobstorageoniotedge`
   - **이미지 URI**: `mcr.microsoft.com/azure-blob-storage:latest`

   ![스크린샷은 IoT Edge 모듈 추가 페이지의 모듈 설정 탭을 보여 줍니다.](./media/how-to-deploy-blob/addmodule-tab1.png)

   이 절차에 설명된 대로 **모듈 설정**, **컨테이너 만들기 옵션** 및 **모듈 쌍 설정** 탭에서 값을 지정할 때까지 **추가** 를 선택하지 마세요.

   > [!IMPORTANT]
   > Azure IoT Edge는 모듈을 호출할 때 대/소문자를 구분하며, Storage SDK도 기본적으로 소문자로 설정됩니다. [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace)의 모듈 이름은 **AzureBlobStorageonIoTEdge** 이지만, 이름을 소문자로 변경하면 Azure Blob Storage on IoT Edge에 대한 연결이 중단되지 않도록 하는 데 도움이 됩니다.

3. **컨테이너 만들기 옵션** 탭을 엽니다.

   ![스크린샷은 IoT Edge 모듈 추가 페이지의 컨테이너 만들기 옵션 탭을 보여 줍니다.](./media/how-to-deploy-blob/addmodule-tab3.png)

   디바이스에 스토리지 계정 정보와 스토리지 탑재를 제공하려면 다음 JSON을 복사하여 입력란에 붙여넣습니다.
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

4. 다음 정보를 사용하여 **컨테이너 만들기 옵션** 에 복사한 JSON을 업데이트합니다.

   - `<your storage account name>`을 기억하기 쉬운 이름으로 바꿉니다. 계정 이름은 3~24자 사이여야 하고 소문자와 숫자만 사용해야 합니다. 공백 없음

   - `<your storage account key>`를 64바이트 base64 키로 바꿉니다. [GeneratePlus](https://generate.plus/en/base64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob Storage에 액세스하려면 이러한 자격 증명을 사용합니다.

   - 컨테이너 운영 체제에 따라 `<storage mount>`를 대체합니다. Blob 모듈이 데이터를 저장하는 IoT Edge 디바이스의 기존 디렉터리에 대한 절대 경로 또는 [볼륨](https://docs.docker.com/storage/volumes/) 이름을 제공합니다. 스토리지 탑재는 사용자가 제공한 디바이스의 위치를 모듈에서 설정된 위치로 매핑합니다.

     - Linux 컨테이너의 경우 형식은 **\<your storage path or volume>:/blobroot** 입니다. 예를 들면 다음과 같습니다.
         - [볼륨 탑재](https://docs.docker.com/storage/volumes/)사용: `my-volume:/blobroot`
         - [바인드 탑재](https://docs.docker.com/storage/bind-mounts/) 사용: `/srv/containerdata:/blobroot`. [컨테이너 사용자에게 디렉터리 액세스 권한을 부여](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)하는 단계를 수행해야 합니다.
     - Windows 컨테이너의 경우 형식은 **\<your storage path or volume>:C:/BlobRoot** 입니다. 예를 들면 다음과 같습니다.
         - [볼륨 탑재](https://docs.docker.com/storage/volumes/) 사용: `my-volume:C:/BlobRoot`.
         - [바인드 탑재](https://docs.docker.com/storage/bind-mounts/) 사용: `C:/ContainerData:C:/BlobRoot`.
         - 로컬 드라이브를 사용하지 않고 SMB 네트워크 위치를 매핑할 수도 있습니다. 자세한 내용은 [로컬 스토리지로 SMB 공유 사용](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)을 참조하세요.

     > [!IMPORTANT]
     > IoT Edge 모듈의 Blob Storage에서 특정 위치를 가리키는 스토리지 탑재 값의 후반부를 변경하지 마세요. 항상 스토리지 탑재는 Linux 컨테이너의 경우 **:/blobroot** 로 끝나고 Windows 컨테이너의 경우 **:C:/BlobRoot** 로 끝납니다.

5. **모듈 쌍 설정** 탭에서 다음 JSON을 복사하여 입력란에 붙여넣습니다.

   ![스크린샷은 IoT Edge 모듈 추가 페이지의 모듈 쌍 설정 탭을 보여 줍니다.](./media/how-to-deploy-blob/addmodule-tab4.png)

   자리 표시자에 나와 있는 대로 적절한 값을 사용하여 각 속성을 구성합니다. IoT Edge 시뮬레이터를 사용하는 경우 [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) 및 [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)에 설명된 대로 해당 속성에 대한 관련 환경 변수로 값을 설정합니다.

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
     },
     "deviceToCloudUploadProperties": {
       "uploadOn": <true, false>,
       "uploadOrder": "<NewestFirst, OldestFirst>",
       "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
       "storageContainersForUpload": {
         "<source container name1>": {
           "target": "<target container name1>"
         }
       },
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   모듈이 배포된 후 deviceToCloudUploadProperties 및 deviceAutoDeleteProperties를 구성하는 방법에 대한 자세한 내용은 [모듈 쌍 편집](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)을 참조하세요. desired 속성에 대한 자세한 내용은 [desired 속성 정의 또는 업데이트](module-composition.md#define-or-update-desired-properties)를 참조하세요.

6. **추가** 를 선택합니다.

7. **다음: 경로** 를 선택하여 경로 섹션으로 진행합니다.

#### <a name="specify-routes"></a>경로 지정

기본 경로를 유지하고 **다음: 검토 + 만들기** 를 선택하여 검토 섹션으로 진행합니다.

#### <a name="review-deployment"></a>배포 검토

검토 섹션에서는 이전 두 개의 섹션에서 선택한 항목에 따라 생성된 JSON 배포 매니페스트를 보여줍니다. 추가하지 않고 선언된 **$edgeAgent** 및 **$edgeHub** 라는 두 개의 모듈도 있습니다. 이 두 개의 모듈은 [IoT Edge 런타임](iot-edge-runtime.md)을 구성하며 모든 배포에서 필수 기본값입니다.

배포 정보를 검토한 다음 **제출** 을 선택합니다.

### <a name="verify-your-deployment"></a>배포 확인

배포를 만든 후 IoT Hub의 **IoT Edge** 페이지로 돌아갑니다.

1. 해당 세부 정보를 열려면 배포할 대상으로 지정한 IoT Edge 디바이스를 선택합니다.
1. 디바이스 세부 정보에서 Blob Storage 모듈이 **배포에 지정됨** 및 **디바이스에서 보고됨** 모두로 나열되어 있는지를 확인합니다.

모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다.

## <a name="deploy-from-visual-studio-code"></a>Visual Studio Code에서 배포

Azure IoT Edge는 Visual Studio Code에 에지 솔루션 개발을 도와주는 템플릿을 제공합니다. 다음 단계에 따라 Blob Storage 모듈을 사용하여 새 IoT Edge 솔루션을 만들고, 배포 매니페스트를 구성합니다.

1. **뷰** > **명령 팔레트** 를 선택합니다.

1. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다.

   ![New IoT Edge Solution 실행](./media/how-to-develop-csharp-module/new-solution.png)

   명령 팔레트의 프롬프트에 따라 솔루션을 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | Visual Studio Code에 대한 개발 머신에서 위치를 선택하여 솔루션 파일을 만듭니다. |
   | 솔루션 이름 제공 | 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값 **EdgeSolution** 을 적용합니다. |
   | 모듈 템플릿 선택 | **기존 모듈(전체 이미지 URL 입력)** 을 선택합니다. |
   | 모듈 이름 제공 | 모듈에 대한 이름을 모두 소문자로 입력합니다(예: **azureblobstorageoniotedge**).<br/><br/>IoT Edge 모듈에서 Azure Blob Storage에 사용할 이름은 반드시 소문자로 사용해야 합니다. IoT Edge는 모듈을 참조할 때 대/소문자를 구분하며 Storage SDK는 기본적으로 소문자로 설정됩니다. |
   | 모듈의 Docker 이미지 제공 | 이미지 URI를 **mcr.microsoft.com/azure-blob-storage:latest** 로 입력합니다. |

   Visual Studio Code는 입력한 정보를 사용하여 IoT Edge 솔루션을 만든 다음, 새 창에서 로드합니다. 솔루션 템플릿은 Blob Storage 모듈 이미지를 포함하는 배포 매니페스트 템플릿을 만들지만, 모듈의 만들기 옵션을 구성해야 합니다.

1. 새 솔루션 작업 영역에서 *deployment.template.json* 을 열고 **모듈** 섹션을 찾습니다. 다음 구성을 변경합니다.

   1. 이 배포에는 필요하지 않으므로 **SimulatedTemperatureSensor** 모듈을 삭제합니다.

   1. 다음 코드를 복사하여 `createOptions` 필드에 붙여넣습니다.

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![모듈 createOptions 업데이트 - Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. `<your storage account name>`을 기억하기 쉬운 이름으로 바꿉니다. 계정 이름은 3~24자 사이여야 하고 소문자와 숫자만 사용해야 합니다. 공백 없음

1. `<your storage account key>`를 64바이트 base64 키로 바꿉니다. [GeneratePlus](https://generate.plus/en/base64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob Storage에 액세스하려면 이러한 자격 증명을 사용합니다.

1. 컨테이너 운영 체제에 따라 `<storage mount>`를 대체합니다. IoT Edge 디바이스에서 Blob 모듈이 데이터를 저장할 [볼륨](https://docs.docker.com/storage/volumes/) 이름 또는 디렉터리 절대 경로를 제공합니다. 스토리지 탑재는 사용자가 제공한 디바이스의 위치를 모듈에서 설정된 위치로 매핑합니다.  

     - Linux 컨테이너의 경우 형식은 **\<your storage path or volume>:/blobroot** 입니다. 예를 들면 다음과 같습니다.
         - [볼륨 탑재](https://docs.docker.com/storage/volumes/)사용: `my-volume:/blobroot`
         - [바인드 탑재](https://docs.docker.com/storage/bind-mounts/) 사용: `/srv/containerdata:/blobroot`. [컨테이너 사용자에게 디렉터리 액세스 권한을 부여](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)하는 단계를 수행해야 합니다.
     - Windows 컨테이너의 경우 형식은 **\<your storage path or volume>:C:/BlobRoot** 입니다. 예를 들면 다음과 같습니다.
         - [볼륨 탑재](https://docs.docker.com/storage/volumes/) 사용: `my-volume:C:/BlobRoot`.
         - [바인드 탑재](https://docs.docker.com/storage/bind-mounts/) 사용: `C:/ContainerData:C:/BlobRoot`.
         - 로컬 드라이브를 사용하지 않고 SMB 네트워크 위치를 매핑할 수도 있습니다. 자세한 내용은 [로컬 스토리지로 SMB 공유 사용](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)을 참조하세요.

     > [!IMPORTANT]
     > IoT Edge 모듈의 Blob Storage에서 특정 위치를 가리키는 스토리지 탑재 값의 후반부를 변경하지 마세요. 항상 스토리지 탑재는 Linux 컨테이너의 경우 **:/blobroot** 로 끝나고 Windows 컨테이너의 경우 **:C:/BlobRoot** 로 끝납니다.

1. *deployment.template.json* 파일에 다음 JSON을 추가하여 모듈의 [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) 및 [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)를 구성합니다. 적절한 값을 사용하여 각 속성을 구성하고 파일을 저장합니다. IoT Edge 시뮬레이터를 사용하는 경우 [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) 및 [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) 설명 섹션에 나와 있는 해당 속성에 대한 관련 환경 변수로 값을 설정합니다.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![azureblobstorageoniotedge의 desired 속성 설정 - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   모듈이 배포된 후 deviceToCloudUploadProperties 및 deviceAutoDeleteProperties를 구성하는 방법에 대한 자세한 내용은 [모듈 쌍 편집](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)을 참조하세요. 컨테이너 생성 옵션, 다시 시작 정책 및 원하는 상태에 대한 자세한 내용은 [EdgeAgent desired 속성](module-edgeagent-edgehub.md#edgeagent-desired-properties)을 참조하세요.

1. *deployment.template.json* 파일을 저장합니다.

1. **deployment.template.json** 을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 배포 매니페스트 생성** 을 선택합니다.

1. Visual Studio Code는 *deployment.template.json* 에 제공된 정보를 사용하여 새로운 배포 매니페스트 파일을 만듭니다. 솔루션 작업 영역의 새 **config** 폴더에 배포 매니페스트가 만들어집니다. 이 파일이 생겼으면 [Visual Studio Code에서 Azure IoT Edge 모듈 배포](how-to-deploy-modules-vscode.md) 또는 [Azure CLI 2.0을 사용하여 Azure IoT Edge 모듈 배포](how-to-deploy-modules-cli.md)의 단계를 수행할 수 있습니다.

## <a name="deploy-multiple-module-instances"></a>여러 모듈 인스턴스 배포

IoT Edge 모듈에 Azure Blob Storage의 여러 인스턴스를 배포하려는 경우 다른 스토리지 경로를 제공하고 모듈이 바인딩되는 `HostPort` 값을 변경해야 합니다. Blob Storage 모듈은 항상 컨테이너에서 11002 포트를 공개하지만, 호스트에서 모듈이 바인딩될 포트를 선언할 수도 있습니다.

**컨테이너 만들기 옵션**(Azure Portal) 또는 **createOptions** 필드(Visual Studio Code의 *deployment.template.json* 파일)을 편집하여 `HostPort` 값을 변경합니다.

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

추가 Blob Storage 모듈에 연결할 때 업데이트된 호스트 포트를 가리키도록 엔드포인트를 변경합니다.

## <a name="configure-proxy-support"></a>프록시 지원 구성

조직에서 프록시 서버를 사용하는 경우 edgeAgent 및 edgeHub 런타임 모듈에 프록시 지원을 구성해야 합니다. 이 프로세스에는 다음 두 작업이 포함됩니다.

- 디바이스에서 런타임 디먼 및 IoT Edge 에이전트를 구성합니다.
- 배포 매니페스트 JSON 파일의 모듈에 대한 HTTPS_PROXY 환경 변수를 설정합니다.

이 프로세스는 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)에 나와 있습니다.

또한 Blob Storage 모듈에는 매니페스트 배포 파일의 HTTPS_PROXY 설정도 필요합니다. 배포 매니페스트 파일을 직접 편집하거나 Azure Portal을 사용할 수 있습니다.

1. Azure Portal에서 IoT Hub로 이동하고 왼쪽 창 메뉴에서 **IoT Edge** 를 선택합니다.

1. 구성할 모듈이 있는 디바이스를 선택합니다.

1. **모듈 설정** 을 선택합니다.

1. 페이지의 **IoT Edge 모듈** 섹션에서 Blob Storage 모듈을 선택합니다.

1. **IoT Edge 모듈 업데이트** 페이지에서 **환경 변수** 탭을 선택합니다.

1. **이름** 에 `HTTPS_PROXY`를, **값** 에 프록시 URL을 추가합니다.

      ![스크린샷은 지정된 값을 입력할 수 있는 IoT Edge 모듈 업데이트 창을 보여 줍니다.](./media/how-to-deploy-blob/https-proxy-config.png)

1. **업데이트** 를 클릭한 다음 **검토 + 만들기** 를 클릭합니다.

1. 프록시가 배포 매니페스트의 모듈에 추가되면 **만들기** 를 선택합니다.

1. 디바이스 세부 정보 페이지에서 모듈을 선택하고 **IoT Edge 모듈 세부 정보** 페이지 하단의 **환경 변수** 탭을 선택하여 설정을 확인합니다.

      ![스크린샷은 환경 변수 탭을 보여 줍니다.](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>다음 단계

[IoT Edge 기반 Azure Blob Storage](how-to-store-data-blob.md)에 대해 자세히 알아보세요.

배포 매니페스트의 작동 방식 및 생성 방법에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.
