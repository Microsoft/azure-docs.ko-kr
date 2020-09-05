---
title: 자습서 - IoT Edge 라이브 비디오 분석 모듈 수정
description: 이 자습서에서는 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿에서 사용하는 라이브 비디오 분석 게이트웨이 모듈을 수정하고 빌드하는 방법을 보여 줍니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: d21eb8d8d79ec04f0f7e766b4eeb370811553e64
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88037995"
---
# <a name="tutorial-modify-and-build-the-live-video-analytics-gateway-modules"></a>자습서: 라이브 비디오 분석 게이트웨이 모듈 수정 및 빌드

이 자습서에서는 LVA(라이브 비디오 분석) 모듈에 대한 IoT Edge 모듈 코드를 수정하는 방법을 보여 줍니다.

이전 자습서에서는 모듈의 미리 빌드된 이미지를 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

* [Node.js](https://nodejs.org/en/download/) v10 이상
* [TSLint](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-typescript-tslint-plugin) 확장이 설치된 [Visual Studio Code](https://code.visualstudio.com/Download)
* [Docker](https://www.docker.com/products/docker-desktop) 엔진
* 모듈 버전을 호스트하는 [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/)
* [Azure Media Services](https://docs.microsoft.com/azure/media-services/) 계정 이전 자습서를 완료한 경우 이전에 만든 자습서를 다시 사용할 수 있습니다.

## <a name="clone-the-repository"></a>리포지토리 복제

리포지토리를 아직 복제하지 않은 경우 다음 명령을 사용하여 로컬 머신의 적절한 위치에 복제합니다.

```cmd
git clone https://github.com/Azure/live-video-analytics
```

VS Code를 사용하여 로컬 *live-video-analytics* 리포지토리 폴더를 엽니다.

## <a name="edit-the-deploymentamd64json-file"></a>deployment.amd64.json 파일 편집

1. 아직 수행하지 않은 경우 **lva-gateway** 리포지토리의 로컬 복사본에 *ref-apps/lva-edge-iot-central-gateway/storage*라는 폴더를 만듭니다. 실수로 기밀 정보를 체크 인하는 것을 방지하기 위해 이 폴더는 Git에서 무시됩니다.
1. *setup* 폴더에서 *storage* 폴더로 *deployment.amd64.json* 파일을 복사합니다.
1. VS Code에서 *storage/deployment.amd64.json* 파일을 엽니다.
1. `registryCredentials` 섹션을 편집하여 Azure Container Registry 자격 증명을 추가합니다.
1. `LvaEdgeGatewayModule` 모듈 섹션을 편집하여 `env:amsAccountName:value`에 이미지 이름과 AMS 계정 이름을 추가합니다.
1. `lvaYolov3` 모듈 섹션을 편집하고 이미지의 이름을 추가합니다.
1. `lvaEdge` 모듈 섹션을 편집하고 이미지의 이름을 추가합니다.
1. 구성을 완료하는 방법에 대한 자세한 내용은 [Azure IoT Central에서 비디오 분석 애플리케이션 만들기](tutorial-video-analytics-create-app.md)를 참조하세요.

## <a name="build-the-code"></a>코드 빌드

1. 코드를 처음으로 빌드하기 전에 VS Code 터미널을 사용하여 `npm install` 명령을 실행합니다. 이 명령은 필요한 패키지를 설치하고 설치 스크립트를 실행합니다.

    > [!TIP]
    > 최신 버전의 리포지토리 GitHub 리포지토리를 끌어올 경우 *node_modules* 폴더를 삭제하고 `npm install`을 다시 실행합니다.

1. *./setup/imageConfig.json* 파일을 편집하여 컨테이너 레지스트리 이름에 따라 이름이 지정된 이미지를 업데이트합니다.

    ```json
    {
        "arch": "amd64",
        "imageName": "[Server].azurecr.io/lva-edge-gateway"
    }
    ```

1. VS Code 터미널을 사용하여 `docker login [your server].azurecr.io` 명령을 실행합니다. 모듈에 대한 배포 매니페스트에 제공한 것과 동일한 자격 증명을 사용합니다.

1. VS Code 터미널을 사용하여 **npm version patch** 명령을 실행합니다. 이 빌드 스크립트는 컨테이너 레지스트리에 이미지를 배포합니다. VS Code 터미널 창의 출력은 빌드에 성공했는지 여부를 표시합니다.

1. **LvaEdgeGatewayModule** 이미지의 버전은 빌드가 완료될 때마다 증가합니다. 이 버전은 배포 매니페스트 파일에서 사용해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿과 LVA IoT Edge 모듈에 대해 알아보았습니다. 다음에 대한 자세한 내용은 다음 단계를 수행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [Azure IoT Central을 사용하여 소매업 솔루션 빌드](overview-iot-central-retail.md)
