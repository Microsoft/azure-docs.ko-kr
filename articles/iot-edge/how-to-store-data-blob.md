---
title: 디바이스에 블록 Blob 저장 - Azure IoT Edge | Microsoft Docs
description: 에지에 데이터를 저장하도록 IoT Edge 디바이스에 Azure Blob Storage 모듈을 배포합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 3a0df408e70ed61355ffba319f6261f90d8e4348
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595555"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>IoT Edge(미리 보기)에서 Azure Blob Storage를 사용하여 에지에 데이터 저장

IoT Edge의 Azure Blog Storage는 에지에 [블록 Blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) 스토리지 솔루션을 제공합니다. IoT Edge 장치의 Blob Storage 모듈은 Azure 블록 Blob 서비스처럼 작동하지만, 블록 Blob은 IoT Edge 장치에 로컬로 저장됩니다. 동일한 Azure 저장소 SDK 메서드 또는 이미 익숙한 블록 Blob API 호출을 사용하여 Blob에 액세스할 수 있습니다. 

이 모듈을 수반 **자동 계층화** 하 고 **자동 만료** 기능입니다.

> [!NOTE]
> 현재 자동 계층화 및 자동 만료 기능 에서만 사용할 Linux AMD64 및 Linux ARM32 합니다.

**자동 계층화** 는 일시적인 인터넷 연결 지원 사용 하 여 Azure에 로컬 blob storage에서 데이터를 자동으로 업로드할 수 있는 기능을 구성할 수 있습니다. 할 수 있습니다.
- / 사용 안 함 계층 기능
- NewestFirst 또는 OldestFirst와 같은 Azure에 복사 하는 데이터는 순서를 선택 합니다.
- 데이터 업로드 하려면 Azure Storage 계정을 지정 합니다.
- Azure에 업로드 하려면 컨테이너를 지정 합니다. 이 모듈을 사용 하면 소스와 대상에 해당 하는 컨테이너의 이름을 지정할 수 있습니다.
- Blob 계층화 전체 수행 (사용 하 여 `Put Blob` 작업) 수준 계층화를 차단 하 고 (사용 하 여 `Put Block` 및 `Put Block List` 작업).

이 모듈에는 블록 수준 계층화의 블록 blob 구성 되어 있을 때 사용 합니다. 몇 가지 일반적인 시나리오는 다음과 같습니다.
- 이전에 업로드 된 blob의 일부 요소를 업데이트 하는 응용 프로그램, 업데이트 된 블록만 및는 전체 blob이 아니라이 모듈을 업로드 합니다.
- 모듈은 blob을 업로드 및 인터넷 연결이 사라진, 연결 되 면 다시 다시 나머지 블록만 하지 전체 blob을 업로드 합니다.

Blob 업로드 하는 동안 문제가 발생 하면 (예: 정전) 예기치 않은 프로세스 종료를 모듈을 다시 온라인 상태가 되 면 업로드에 예정 된 모든 블록을 다시 업로드 됩니다.

**자동 만료** 기능은 구성할 수 있는이 모듈 자동으로 blob의 로컬 저장소에서 시간을 TTL (Live) 만료 되 면 삭제 됩니다. 분 단위로 측정 됩니다. 할 수 있습니다.
- / 사용 안 함 기능을 자동 만료
- TTL을 분 단위로 지정 합니다.

비디오, 이미지, 재무 데이터, 병원 데이터 또는 모든 데이터는 나중에 로컬로 저장 해야 하는 경우 로컬로 처리 하거나 클라우드로 전송할 수 있는 데이터의 같은 위치 하는 시나리오는이 모듈을 사용 하는 좋은 예입니다.

이 문서에서는 IoT Edge 디바이스에서 Blob service를 실행하는 Azure Blob Storage를 IoT Edge 컨테이너에 배포하기 위한 지침을 제공합니다. 

>[!NOTE]
>IoT Edge의 Azure Blob Storage는 현재 [공개 미리 보기](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)입니다. 

간략 한 소개 비디오를 시청 하세요.
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>필수 조건

Azure IoT Edge 장치:

* [Linux](quickstart-linux.md) 또는 [Windows 장치](quickstart.md)의 빠른 시작에 설명된 단계에 따라 개발 머신 또는 가상 머신을 Edge 장치로 사용할 수 있습니다.
* IoT Edge 모듈의 Azure Blob Storage는 다음 디바이스 구성을 지원합니다.

   | 운영 체제 | 아키텍처 |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core(10월 업데이트) | AMD64 |
   | Windows 10 IoT Enterprise(10월 업데이트) | AMD64 |
   | Windows Server 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

클라우드 리소스:

* Azure의 표준 계층 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md). 


## <a name="deploy-blob-storage-to-your-device"></a>장치에 Blob Storage 배포

IoT Edge 디바이스에 모듈을 배포하는 여러 가지 방법이 있는데, 모든 방법이 IoT Edge 모듈의 Azure Blob Storage에서 작동합니다. 가장 간단한 두 가지 방법은 Azure Portal 또는 Visual Studio Code 템플릿을 사용하는 것입니다. 

### <a name="azure-portal"></a>Azure portal

Azure Marketplace는 IoT Edge의 Azure Blob Storage를 포함하여 사용자의 IoT Edge 디바이스에 직접 배포할 수 있는 IoT Edge 모듈을 제공합니다. Azure Portal에서 모듈을 배포하기 위한 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 “IoT Edge의 Azure Blob Storage”를 검색합니다. Marketplace에서 검색 결과를 **선택**합니다.

   ![Marketplace 검색에서 모듈 만들기](./media/how-to-store-data-blob/marketplace-module.png)

2. 이 모듈을 수신할 IoT Edge 디바이스를 선택합니다. **IoT Edge 모듈의 대상 디바이스** 페이지에서 다음 정보를 입력합니다.

   1. 사용 중인 IoT Hub가 포함된 **구독**을 선택합니다.

   2. 자신의 **IoT Hub**를 선택합니다.

   3. 자신의 **IoT Edge 디바이스 이름**을 알고 있는 경우 텍스트 상자에 입력합니다. 그렇지 않은 경우 **디바이스 찾기**를 선택하여 IoT Hub에 있는 IoT Edge 디바이스 목록에서 선택합니다. 
   
   4. **만들기**를 선택합니다.

   Azure Marketplace에서 IoT Edge 모듈을 선택하고 모듈을 수신할 IoT Edge 디바이스를 선택하면 3단계 마법사로 이동하여 모듈 배포 방식을 정확하게 정의할 수 있습니다.

3. 모듈 설정 마법사의 **모듈 추가** 단계에서는 **AzureBlobStorageonIoTEdge** 모듈이 이미 **배포 모듈**에 나열되어 있습니다. 

2. 배포 모듈 목록에서 Blob Storage 모듈을 선택하여 모듈 세부 사항을 엽니다. 

   ![모듈 이름을 선택하여 모듈 세부 정보 열기](./media/how-to-store-data-blob/open-module-details.png)

3. **IoT Edge 사용자 지정 모듈** 페이지에서 다음 단계를 사용하여 IoT Edge 모듈에서 Azure Blob Storage를 업데이트합니다.

   1. 모듈 **이름**을 소문자로 변경합니다. 원하는 경우 모듈의 이름을 바꾸거나 `azureblobstorageoniotedge`를 사용할 수 있습니다. 

      >[!IMPORTANT]
      >Azure IoT Edge는 모듈을 호출할 때 대/소문자를 구분하며 Storage SDK는 기본적으로 소문자로 설정됩니다. IoT Edge 모듈에서 Azure Blob Storage에 대한 연결이 중단되지 않도록 하려면 이름을 소문자로 입력합니다. 

   2. 컨테이너에서 필요한 포트 바인딩인 기본 **컨테이너 만들기 옵션** - 하지만 스토리지 계정 정보 및 디바이스의 스토리지 디렉터리에 대한 바인드도 추가해야 합니다. 아래의 JSON으로 포털의 JSON을 덮어씁니다.
    
      ```json
      {
          "Env":[
              "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
              "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
          ],
          "HostConfig":{
              "Binds":[
                  "<storage directory bind>"
              ],
              "PortBindings":{
                  "11002/tcp":[{"HostPort":"11002"}]
              }
          }
      }
      ```   
   3. 다음 정보로 복사한 JSON을 업데이트합니다. 

      * `<your storage account name>`을 기억하기 쉬운 이름으로 바꿉니다. 계정 이름은 3~24자 사이여야 하고 소문자와 숫자만 사용해야 합니다.
      * `<your storage account key>`를 64바이트 base64 키로 바꿉니다. [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob Storage에 액세스하려면 이러한 자격 증명을 사용합니다.
      * 컨테이너 운영 체제에 따라 `<storage directory bind>`를 바꿉니다. IoT Edge 디바이스에서 Blob 모듈이 데이터를 저장할 [볼륨](https://docs.docker.com/storage/volumes/) 이름 또는 디렉터리 절대 경로를 제공합니다. 스토리지 디렉터리 바인드는 사용자가 제공한 디바이스의 위치를 모듈에서 설정된 위치로 매핑합니다. 

         * Linux 컨테이너: **\<storage path>:/blobroot**. 예: /srv/containerdata:/blobroot. 또는 my-volume:/blobroot. 
         * Windows 컨테이너: **\<storage path>:C:/BlobRoot**. 예: C:/ContainerData:C:/BlobRoot. 또는 my-volume:C:/blobroot.
   
      > [!IMPORTANT]
      > 스토리지 디렉터리 바인드 값의 두 번째 절반을 변경하지 마세요. 이는 모듈의 특정 위치를 가리킵니다. 항상 스토리지 디렉터리 바인드는 Linux 컨테이너의 경우 **:/blobroot**로 끝나고 Windows 컨테이너의 경우 **:C:/BlobRoot**로 끝납니다.

      ![업데이트 모듈 컨테이너 만들기 옵션 - 포털](./media/how-to-store-data-blob/edit-module.png)

   4. 설정할 [자동 계층화 및 자동 만료](#configure-auto-tiering-and-auto-expiration-via-azure-portal) desired 속성에 있습니다. 목록을 [자동 계층화](#auto-tiering-properties) 하 고 [자동 만료](#auto-expiration-properties) 속성 및 가능한 값입니다. 

   5. **저장**을 선택합니다. 

4. **다음**을 선택하여 마법사의 다음 단계로 진행합니다.
5. 마법사의 **경로 지정** 단계에서 **다음**을 선택합니다.
6. 마법사의 **배포 검토** 단계에서 **제출**을 선택합니다.
7. 배포를 제출한 후 IoT Hub의 **IoT Edge** 페이지로 돌아갑니다. 해당 세부 정보를 열려면 배포할 대상으로 지정한 IoT Edge 디바이스를 선택합니다. 
8. 디바이스 세부 정보에서 Blob Storage 모듈이 **배포에 지정됨** 및 **디바이스에서 보고됨** 모두로 나열되어 있는지를 확인합니다. 모듈을 디바이스에서 시작한 다음, IoT Hub에 다시 보고하려면 몇 분 정도 걸릴 수 있습니다. 업데이트된 상태를 보려면 페이지를 새로 고칩니다. 

### <a name="visual-studio-code-templates"></a>Visual Studio Code 템플릿

Azure IoT Edge는 Visual Studio Code에 에지 솔루션 개발을 도와주는 템플릿을 제공합니다. 이러한 단계를 수행하려면 개발 머신에 [Visual Studio Code](https://code.visualstudio.com/)가 설치되어 있고 [Azure IoT 도구](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)가 구성되어 있어야 합니다.

다음 단계에 따라 Blob Storage 모듈을 사용하여 새 IoT Edge 솔루션을 만들고, 배포 매니페스트를 구성합니다. 

1. **보기** > **명령 팔레트**를 선택합니다. 

2. 명령 팔레트에서 **Azure IoT Edge: 새 IoT Edge 솔루션** 명령을 입력하고 실행합니다. 명령 팔레트의 프롬프트에 따라 솔루션을 만듭니다.

   | 필드 | 값 |
   | ----- | ----- |
   | 폴더 선택 | VS Code에 대한 개발 머신에서 위치를 선택하여 솔루션 파일을 만듭니다. |
   | 솔루션 이름 제공 | 솔루션에 대한 설명이 포함된 이름을 입력하거나 기본값 **EdgeSolution**을 적용합니다. |
   | 모듈 템플릿 선택 | **기존 모듈(전체 이미지 URL 입력)** 을 선택합니다. |
   | 모듈 이름 제공 | 모듈에 대한 이름을 모두 소문자로 입력합니다(예: **azureblobstorage**).<br><br>IoT Edge 모듈에서 Azure Blob Storage에 사용할 이름은 반드시 소문자로 사용해야 합니다. IoT Edge는 모듈을 참조할 때 대/소문자를 구분하며 Storage SDK는 기본적으로 소문자로 설정됩니다. |
   | 모듈의 Docker 이미지 제공 | 이미지 URI를 **mcr.microsoft.com/azure-blob-storage:latest**로 입력합니다. |

   VS Code는 입력한 정보를 사용하여 IoT Edge 솔루션을 만든 다음, 새 창에서 로드합니다. 솔루션 템플릿은 Blob Storage 모듈 이미지를 포함하는 배포 매니페스트 템플릿을 만들지만, 모듈의 만들기 옵션을 구성해야 합니다. 

3. 새 솔루션 작업 영역에서 **deployment.template.json**을 열고 **모듈** 섹션을 찾습니다. 다음 구성을 변경합니다.

   1. 이 배포에 필요 없는 **tempSensor** 모듈을 삭제합니다. 

   2. 다음 코드를 복사하여 Blob Storage 모듈의 **createOptions** 필드에 붙여넣습니다. 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![모듈 createOptions 업데이트 - VS Code](./media/how-to-store-data-blob/create-options.png)

4. 만들기 옵션 JSON에서 컨테이너 운영 체제에 따라 `<storage directory bind>`를 업데이트합니다. IoT Edge 디바이스에서 Blob 모듈이 데이터를 저장할 [볼륨](https://docs.docker.com/storage/volumes/) 이름 또는 디렉터리 절대 경로를 제공합니다. 스토리지 디렉터리 바인드는 사용자가 제공한 디바이스의 위치를 모듈에서 설정된 위치로 매핑합니다.  

   * Linux 컨테이너: **\<storage path>:/blobroot**. 예: /srv/containerdata:/blobroot. 또는 my-volume:/blobroot.
   * Windows 컨테이너: **\<storage path>:C:/BlobRoot**. 예: C:/ContainerData:C:/BlobRoot. 또는 my-volume:C:/blobroot.
   
   > [!IMPORTANT]
   > 스토리지 디렉터리 바인드 값의 두 번째 절반을 변경하지 마세요. 이는 모듈의 특정 위치를 가리킵니다. 항상 스토리지 디렉터리 바인드는 Linux 컨테이너의 경우 **:/blobroot**로 끝나고 Windows 컨테이너의 경우 **:C:/BlobRoot**로 끝납니다.

5. 구성할 [자동 계층화 및 자동 만료](#configure-auto-tiering-and-auto-expiration-via-vscode)합니다. 목록을 [자동 계층화](#auto-tiering-properties) 하 고 [자동 만료](#auto-expiration-properties) 속성

6. **deployment.template.json** 파일을 저장합니다.

7. 솔루션 작업 영역에서 **.env** 파일을 엽니다. 

8. .env 파일이 컨테이너 레지스트리 자격 증명을 수신하도록 설정되어 있지만, 공개적으로 사용할 수 있으므로 Blob Storage 이미지에 대해서는 필요하지 않습니다. 대신, 두 개의 새로운 환경 변수를 사용하여 파일을 바꿉니다. 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. `STORAGE_ACCOUNT_NAME`의 값을 입력합니다. 계정 이름은 3~24자 사이여야 하고 소문자와 숫자만 사용해야 합니다. `STORAGE_ACCOUNT_KEY`의 64바이트 base64 키를 제공합니다. [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64) 같은 도구를 사용하여 키를 생성할 수 있습니다. 다른 모듈에서 Blob Storage에 액세스하려면 이러한 자격 증명을 사용합니다. 

   제공하는 값에는 공백 또는 따옴표를 포함하지 마십시오. 

10. **.env** 파일을 저장합니다. 

11. **deployment.template.json**을 마우스 오른쪽 단추로 클릭하고 **IoT Edge 배포 매니페스트 생성**을 선택합니다. 

12. Visual Studio Code는 deployment.template.json 및 .env에 제공된 정보를 사용하여 새로운 배포 매니페스트 파일을 만듭니다. 솔루션 작업 영역의 새 **config** 폴더에 배포 매니페스트가 만들어집니다. 이 파일이 생겼으면 [Visual Studio Code에서 Azure IoT Edge 모듈 배포](how-to-deploy-modules-vscode.md) 또는 [Azure CLI 2.0을 사용하여 Azure IoT Edge 모듈 배포](how-to-deploy-modules-cli.md)의 단계를 수행할 수 있습니다.

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>자동 계층화 자동 만료 속성 및 구성

Desired 속성을 사용 하 여 자동 계층화 및 자동 만료 속성을 설정할 수 있습니다. 배포 중에 설정 또는 다시 배포 하지 않아도 모듈 쌍을 편집 하 여 나중에 변경할 수 있습니다. 에 대 한 "모듈 쌍" 확인을 좋습니다 `reported configuration` 및 `configurationValidation` 값 올바르게 전파 되도록 합니다.

### <a name="auto-tiering-properties"></a>자동 계층화 속성 
이 설정의 이름은 `tieringSettings`

| 필드 | 가능한 값 | 설명 |
| ----- | ----- | ---- |
| tieringOn | true, false | 기본적으로 설정은 `false`설정 하려는 경우에 설정 `true`|
| backlogPolicy | NewestFirst를 OldestFirst | Azure에 복사 하는 데이터는 순서를 선택할 수 있습니다. 기본적으로 설정은 `OldestFirst`합니다. 순서는 Blob의 마지막 수정된 시간 기준 |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` 원하는 데이터를 Azure Storage 계정을 지정할 수 있도록 연결 문자열을 업로드 합니다. 지정할 `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`합니다. 전역 Azure, Azure Government 및 Microsoft Azure Stack에 대 한 다릅니다 적절 한 EndpointSuffix Azure 데이터가 업로드 수를 추가 합니다. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Azure에 업로드 하려면 컨테이너 이름을 지정할 수 있습니다. 이 모듈을 사용 하면 소스와 대상에 해당 하는 컨테이너의 이름을 지정할 수 있습니다. 대상 컨테이너 이름을 지정 하지 않으면, 컨테이너 이름으로 자동으로 할당 됩니다 것 `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`입니다. 대상 컨테이너 이름, 체크 아웃의 가능한 값 열에 대 한 템플릿 문자열을 만들 수 있습니다. <br>* %h IoT Hub 이름 (3-50 자)-> 합니다. <br>* %d IoT 장치 Id (129 자 1)-> 합니다. <br>* %m 모듈 이름 (1 ~ 64 자)-> 합니다. <br>* %c 원본 컨테이너 이름 (63 자가 하 3)-> 합니다. <br><br>컨테이너 이름의 최대 크기는 63 자 할당 하는 동안 자동으로 대상 컨테이너 이름은 컨테이너 크기에서 제거 하는 63 자를 초과 하는 경우 각 섹션 (IoTHubName, IotEdgeDeviceName, ModuleName, ContainerName) 15 자입니다. |

### <a name="auto-expiration-properties"></a>자동 만료 속성
이 설정의 이름은 `ttlSettings`

| 필드 | 가능한 값 | 설명 |
| ----- | ----- | ---- |
| ttlOn | true, false | 기본적으로 설정은 `false`설정 하려는 경우에 설정 `true`|
| timeToLiveInMinutes | `<minutes>` | TTL을 분 단위로 지정 합니다. TTL 만료 되 면 모듈을 로컬 저장소에서 자동으로 blob 삭제 됩니다. |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>Azure portal을 통해 자동 계층화 및 자동 만료 구성

자동 계층화를 사용 하도록 설정 하려면 원하는 속성을 설정 하 고 자동 만료, 이러한 값을 설정할 수 있습니다.

- **초기 배포 동안**: JSON을 복사 **집합 모듈 쌍의 desired 속성** 상자입니다. 적절 한 값을 사용 하 여 각 속성을 구성 하 고, 저장 및 배포를 사용 하 여 계속 합니다.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

  ![자동 계층화 및 자동 만료 속성 설정](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **모듈 "모듈 Identity 쌍" 기능을 통해 배포 된 후**: 이 모듈의 "모듈 Identity 쌍"로 이동, desired 속성에서 JSON을 복사 하, 적절 한 값을 사용 하 여 각 속성을 구성 하 고 저장 합니다. 추가 되거나 업데이트 될 때마다이 해야 desired 속성, "모듈 Identity 쌍" json에서 합니다 `reported configuration` 섹션에는 변경 내용을 반영 되어 및 `configurationValidation` 섹션 각 속성에 대 한 성공한 것으로 보고 합니다.

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```

![계층화 + ttl module_identity_twin](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>자동 계층화 및 VSCode 통해 자동 만료를 구성 합니다.

- **초기 배포 동안**: 추가 된이 모듈에 대 한 desired 속성을 정의 하 여 deployment.template.json에서 JSON 아래. 적절 한 값을 사용 하 여 각 속성을 구성 하 고 저장 합니다.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>, 
         "timeToLiveInMinutes": <timeToLiveInMinutes> 
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

   ```

다음은이 모듈에 대 한 desired 속성의 예: ![azureblobstorageoniotedge-VS Code에 대 한 desired 속성 설정](./media/how-to-store-data-blob/tiering_ttl.png)

- **모듈 "모듈 트윈"을 통해 배포 된 후**: [모듈 쌍 편집](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) 이 모듈의 desired 속성에서 JSON을 복사, 적절 한 값을 사용 하 여 각 속성을 구성 하 고 저장 합니다. 추가 되거나 업데이트 될 때마다이 해야 desired 속성, "모듈 쌍" json에서 합니다 `reported configuration` 섹션에는 변경 내용을 반영 되어 및 `configurationValidation` 섹션 각 속성에 대 한 성공한 것으로 보고 합니다.

   ```json 
    "ttlSettings": {
        "ttlOn": <true, false>, 
        "timeToLiveInMinutes": <timeToLiveInMinutes> 
    },
    "tieringSettings": {
        "tieringOn": <true, false>,
        "backlogPolicy": "<NewestFirst, OldestFirst>",
        "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
        "tieredContainers": {
            "<source container name1>": {
                "target": "<target container name1>"
            }
        }
    }

   ```
## <a name="logs"></a>로그

지침에 따르세요 [IoT Edge 모듈에 대 한 docker 로그를 구성 합니다.](production-checklist.md#set-up-logs-and-diagnostics)

## <a name="connect-to-your-blob-storage-module"></a>Blob Storage 모듈에 연결

모듈에 대해 구성한 계정 이름 및 계정 키를 사용하여 IoT Edge 장치의 Blob Storage에 액세스할 수 있습니다. 

만드는 저장소 요청의 Blob 엔드포인트로 IoT Edge 디바이스를 지정합니다. 구성한 IoT Edge 디바이스 정보 및 계정 이름을 사용하여 [명시적 저장소 엔드포인트에 대한 연결 문자열을 만들 수 있습니다](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint). 

1. "Azure Blob 저장소에 IoT Edge" 실행 되 고 있는 동일한 edge 장치에 배포 된 모듈에 대 한 blob 끝점은: `http://<module name>:11002/<account name>`합니다. 
2. 여기서 "Azure Blob 저장소에 IoT Edge"를 실행 한 다음은 blob 끝점 설정에 따라에 지 장치가 보다 다른에 지 장치에서 배포 된 모듈의 경우: `http://<device IP >:11002/<account name>` 또는 `http://<IoT Edge device hostname>:11002/<account name>` 또는 `http://<FQDN>:11002/<account name>`

## <a name="deploy-multiple-instances"></a>여러 인스턴스 배포

IoT Edge에서 Azure Blob Storage의 여러 인스턴스를 배포 하려는 경우 다른 저장소 경로 제공 하 고 모듈에 바인딩되는 HostPort 변경 해야 합니다. Blob Storage 모듈은 항상 컨테이너에서 11002 포트를 공개하지만, 호스트에서 모듈이 바인딩될 포트를 선언할 수도 있습니다. 

모듈 만들기 옵션을 편집하여 HostPort 값을 변경할 수 있습니다.

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

추가 Blob Storage 모듈에 연결할 때 업데이트된 호스트 포트를 가리키도록 엔드포인트를 변경합니다. 

## <a name="try-it-out"></a>체험

### <a name="azure-blob-storage-quickstart-samples"></a>Azure Blob 저장소 빠른 시작 샘플
Azure Blob Storage 설명서에는 여러 언어로 샘플 코드를 제공하는 빠른 시작이 포함되어 있습니다. 이러한 샘플을 실행하여 Blob 스토리지 모듈을 가리키도록 Blob 엔드포인트를 변경하는 방법으로 IoT Edge의 Azure Blob Storage를 테스트할 수 있습니다. 다음 단계에 따라 [blob 저장소 모듈에 연결](#connect-to-your-blob-storage-module)

다음 빠른 시작에서는 IoT Edge에서도 지원되는 언어를 사용하므로, Blob Storage 모듈과 함께 IoT Edge 모듈로 배포할 수 있습니다.

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.JS](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Azure Storage 탐색기
또한 로컬 저장소 계정에 연결 하려면 "Azure Storage 탐색기"를 시도할 수 있습니다. 협력 [Azure Storage 탐색기 버전 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0)합니다.

> [!NOTE]
> 예: 로컬 저장소 계정에 대 한 연결을 추가 또는 로컬 저장소 계정에서 컨테이너를 만드는 다음 단계를 수행 하는 동안 오류가 발생할 수 있습니다. 무시 하 고 새로 고침 하세요. 

1. Azure Storage 탐색기 다운로드 및 설치
2. 연결 문자열을 사용 하 여 Azure Storage에 연결
3. 연결 문자열을 제공 합니다. `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. 연결 하는 단계를 진행 합니다.
5. 로컬 저장소 계정 내의 컨테이너 만들기
6. 블록 blob으로 파일 업로드를 시작 합니다.
   > [!NOTE]
   > 페이지 blob으로 업로드 하려면이 확인란을 선택 취소 합니다. 이 모듈에서 페이지 blob을 지원 하지 않습니다. 하면이 프롬프트.iso,.vhd에서.vhdx 또는 큰 파일 처럼 파일을 업로드 하는 중입니다.

7. 여기서 데이터를 업로드 하는 Azure storage 계정 연결을 선택할 수 있습니다. 제공 단일 뷰를 로컬 저장소 계정 및 Azure storage 계정 모두에 대 한

## <a name="supported-storage-operations"></a>지원되는 저장소 작업

Blob 저장소 IoT Edge의 동일한 Azure Storage Sdk를 사용 하 여 모듈과의 블록 blob 끝점에 대 한 Azure Storage API 2017-04-17 버전과 일치 합니다. 이후 릴리스는 고객의 요구에 따라 다릅니다.

일부 Azure Blob Storage 작업은 IoT Edge의 Azure Blob Storage에서 지원되지 않습니다. 다음 섹션에는 지원 되거나 지원 되지 않는 작업을 나열합니다.

### <a name="account"></a>계좌

지원됨: 
* 컨테이너 나열

지원되지 않음: 
* Blob 서비스 속성을 가져오고 설정
* 실행 전 Blob 요청
* Blob 서비스 통계 가져오기
* 계정 정보 가져오기

### <a name="containers"></a>컨테이너

지원됨: 
* 컨테이너 만들기 및 삭제
* 컨테이너 속성 및 메타데이터 가져오기
* Blob 나열
* 컨테이너 ACL 가져오기 및 설정
* 컨테이너 메타데이터 설정

지원되지 않음:
* 컨테이너 임대

### <a name="blobs"></a>Blob

지원됨: 
* Blob 배치, 가져오기 및 삭제
* Blob 속성 가져오기 및 설정
* Blob 메타데이터 가져오기 및 설정

지원되지 않음: 
* Blob 임대
* Blob 스냅숏
* Blob 복사 및 중단
* Blob 삭제 취소
* Blob 계층 설정

### <a name="block-blobs"></a>블록 Blob

지원됨: 
* 블록 배치
* 블록 목록 배치 및 가져오기

지원되지 않음:
* URL에서 블록 배치

## <a name="feedback"></a>피드백:
여러분의 의견은 매우 유용 하 고 쉽게 사용할 수 있도록이 모듈 및 해당 기능 소중 합니다. 사용자 의견 및 개선 하는 방법에 대해 알려주세요.

우리에 게 연결할 수 있습니다. absiotfeedback@microsoft.com 

## <a name="next-steps"></a>다음 단계

[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md)에 대해 자세히 알아보기

