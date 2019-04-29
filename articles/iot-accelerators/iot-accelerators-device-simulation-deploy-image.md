---
title: 사용자 지정 디바이스 시뮬레이션 이미지 배포 - Azure| Microsoft Docs
description: 이 사용 방법 가이드에서는 디바이스 시뮬레이션 솔루션의 사용자 지정 Docker 이미지를 Azure에 배포하는 방법을 알아봅니다.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: c1f321f452b65016c11cb66d08ebab108509cc62
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61448413"
---
# <a name="deploy-a-custom-device-simulation-docker-image"></a>사용자 지정 디바이스 시뮬레이션 Docker 이미지 배포

디바이스 시뮬레이션 솔루션을 수정하여 사용자 지정 기능을 추가할 수 있습니다. 예를 들어 [프로토콜 버퍼를 사용하여 원격 분석 직렬화](iot-accelerators-device-simulation-protobuf.md) 문서에서는 프로토콜 버퍼(Protobuf)를 사용하여 원격 분석을 보내는 솔루션에 사용자 지정 디바이스를 추가하는 방법을 보여 줍니다. 변경 내용을 로컬로 테스트한 후 다음 단계에서는 Azure에서 변경 내용을 디바이스 시뮬레이션 인스턴스에 배포합니다. 이 작업을 완료하려면 수정된 서비스가 포함된 Docker 이미지를 만들어 배포해야 합니다.

이 방법 가이드의 단계에서는 다음을 수행하는 방법을 보여 줍니다.

1. 개발 환경 준비
1. 새 Docker 이미지 생성
1. 새 Docker 이미지를 사용하도록 디바이스 시뮬레이션 구성
1. 새 이미지를 사용하여 시뮬레이션 실행

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 완료하려면 다음이 필요합니다.

* 배포된 [디바이스 시뮬레이션](quickstart-device-simulation-deploy.md) 인스턴스
* Docker. 플랫폼에 맞는 [Docker Community Edition](https://www.docker.com/products/docker-engine#/download)을 다운로드합니다.
* Docker 이미지를 업로드할 수 있는 [Docker 허브 계정](https://hub.docker.com/). Docker 허브 계정에서 **device-simulation**이라는 공용 리포지토리를 만듭니다.
* 로컬 머신에서 수정되고 테스트된 [디바이스 시뮬레이션 솔루션](https://github.com/Azure/device-simulation-dotnet/archive/master.zip). 예를 들어 [프로토콜 버퍼를 사용하여 원격 분석을 직렬화](iot-accelerators-device-simulation-protobuf.md)하도록 솔루션을 수정할 수 있습니다.
* SSH를 실행할 수 있는 셸. Git For Windows를 설치하는 경우 설치에 포함된 **bash** 셸을 사용할 수 있습니다. [Azure Cloud Shell](https://shell.azure.com/)을 사용할 수도 있습니다.

이 문서의 지침에서는 Windows를 사용한다고 가정합니다. 다른 운영 체제를 사용하는 경우 일부 파일 경로 및 명령을 사용자 환경에 맞게 조정해야 합니다.

## <a name="create-a-new-docker-image"></a>새 Docker 이미지 만들기

디바이스 시뮬레이션 서비스에 고유한 변경 내용을 배포하려면 **scripts\docker** 폴더에 있는 빌드 및 배포 스크립트를 편집하여 컨테이너를 docker-hub 계정에 업로드해야 합니다.

### <a name="modify-the-docker-scripts"></a>Docker 스크립트 수정

Docker 허브 리포지토리 정보를 사용하여 **scripts\docker** 폴더에 있는 **build.cmd**, **publish.cmd** 및 **run.cmd** Docker 스크립트를 수정합니다. 다음 단계에서는 **device-simulation**이라는 공용 리포지토리를 만들었다고 가정합니다.

`DOCKER_IMAGE={your-docker-hub-username}/device-simulation`

다음과 같이 **docker-compose.yml** 파일을 업데이트합니다.

`image: {your-docker-hub-username}/device-simulation`

### <a name="configure-the-solution-to-include-any-new-files"></a>새 파일을 포함하도록 솔루션 구성

새 디바이스 모델 파일을 추가한 경우 해당 파일을 솔루션에 명시적으로 포함해야 합니다. 포함할 각 추가 파일에 대한 항목을 **services/services.csproj**에 추가합니다. 예를 들어 [프로토콜 버퍼를 사용하여 원격 분석 직렬화](iot-accelerators-device-simulation-protobuf.md) 방법을 완료한 경우 다음 항목을 추가합니다.

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

### <a name="generate-new-docker-images-and-push-to-docker-hub"></a>새 Docker 이미지를 생성하고 Docker 허브로 푸시

docker-hub 계정을 사용하여 새 Docker 이미지를 Docker 허브에 게시합니다.

1. 명령 프롬프트를 열고 디바이스 시뮬레이션 리포지토리의 로컬 복사본으로 이동합니다.

1. **docker** 폴더로 이동합니다.

    ```cmd
    cd scripts\docker
    ```

1. 다음 명령을 실행하여 Docker 이미지를 빌드합니다.

    ```cmd
    build.cmd
    ```

1. 다음 명령을 실행하여 Docker 이미지를 Docker 허브 리포지토리에 게시합니다. Docker 허브 자격 증명을 사용하여 Docker에 로그인합니다.

    ```cmd
    docker login
    publish.cmd
    ```

<!-- TODO fix heading levels working include -->

[!INCLUDE [iot-solution-accelerators-access-vm](../../includes/iot-solution-accelerators-access-vm.md)]

## <a name="update-the-service"></a>서비스 업데이트

사용자 지정 이미지를 사용하도록 디바이스 시뮬레이션 컨테이너를 업데이트하려면 다음 단계를 수행합니다.

* SSH를 사용하여 디바이스 시뮬레이션 인스턴스를 호스팅하는 가상 머신에 연결합니다. 이전 섹션에서 적어 둔 IP 주소와 암호를 사용합니다.

    ```sh
    ssh azureuser@{your vm ip address}
    ```

* **/app** 디렉터리로 이동합니다.

    ```sh
    cd /app
    ```

* **docker-compose.yml** 파일을 편집합니다.

    ```sh
    sudo nano docker-compose.yml
    ```

    **image**에서 Docker 허브 리포지토리에 업로드한 사용자 지정 **device-simulation** 이미지를 가리키도록 수정합니다.

    ```yml
    image: {your-docker-hub-username}/device-simulation
    ```

    변경 내용을 저장합니다.

* 다음 명령을 실행하여 마이크로 서비스를 다시 시작합니다.

    ```sh
    sudo start.sh
    ```

## <a name="run-your-simulation"></a>시뮬레이션 실행

이제 사용자 지정된 디바이스 시뮬레이션 솔루션을 사용하여 시뮬레이션을 실행할 수 있습니다.

1. [Microsoft Azure IoT 솔루션 가속기](https://www.azureiotsolutions.com/Accelerators#dashboard)에서 디바이스 시뮬레이션 웹 UI를 시작합니다.

1. 웹 UI를 사용하여 시뮬레이션을 구성하고 실행합니다. 이전에 [프로토콜 버퍼를 사용하여 원격 분석 직렬화](iot-accelerators-device-simulation-protobuf.md)를 완료한 경우 사용자 지정 디바이스 모델을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이제 사용자 지정 디바이스 시뮬레이션 이미지를 배포하는 방법을 알아보았으므로 [디바이스 시뮬레이션 솔루션 가속기에서 기존 IoT Hub를 사용하는 방법](iot-accelerators-device-simulation-choose-hub.md)을 알아보는 것이 좋습니다.