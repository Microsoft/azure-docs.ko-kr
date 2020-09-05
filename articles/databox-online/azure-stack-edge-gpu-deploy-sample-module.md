---
title: Azure Stack Edge GPU 장치에 GPU 모듈을 배포 합니다. Microsoft Docs
description: 계산을 사용 하도록 설정 하 고 로컬 UI를 통해 Azure Stack Edge 장치를 계산 하도록 설정 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/31/2020
ms.author: alkohli
ms.openlocfilehash: 5af86001d46bf194c9b61f325052a4cde0d86d5e
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254562"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-gpu-device"></a>Azure Stack Edge GPU 장치에서 GPU 사용 IoT 모듈 배포

이 문서에서는 Azure Stack Edge GPU 장치에서 GPU 사용 IoT Edge 모듈을 배포 하는 방법을 설명 합니다. 

이 문서에서는 다음 방법을 설명합니다.
  - GPU 모듈을 실행할 Azure Stack Edge를 준비 합니다.
  - Git 리포지토리에서 샘플 코드를 다운로드 하 고 설치 합니다.
  - 솔루션을 빌드하고 배포 매니페스트를 생성 합니다.
  - 솔루션을 Azure Stack Edge 장치에 배포 합니다.
  - 모듈 출력을 모니터링 합니다.


## <a name="about-sample-module"></a>샘플 모듈 정보

이 문서의 GPU 샘플 모듈에는 GPU에 대 한 CPU에 대 한 PyTorch 및 TensorFlow 벤치마킹 샘플 코드가 포함 되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음을 확인합니다.

- GPU 사용 1 노드 Azure Stack Edge 장치에 액세스할 수 있습니다. 이 장치는 Azure의 리소스를 사용 하 여 활성화 됩니다. [장치 활성화를](azure-stack-edge-gpu-deploy-activate.md)참조 하세요.
- 이 장치에서 계산을 구성 했습니다. [자습서: Azure Stack Edge 장치에서 계산 구성](azure-stack-edge-gpu-deploy-configure-compute.md)의 단계를 따릅니다.
- ACR (Azure Container Registry)입니다. **액세스 키** 블레이드로 이동 하 여 ACR 로그인 서버, 사용자 이름 및 암호를 기록해 둡니다. 자세한 내용은 [빠른 시작: Azure Portal을 사용 하 여 개인 컨테이너 레지스트리 만들기](../container-registry/container-registry-get-started-portal.md#create-a-container-registry)를 참조 하세요.
- Windows 클라이언트에서 다음과 같은 개발 리소스가 있습니다.
    - [Azure CLI 2.0 이상](https://aka.ms/installazurecliwindows)
    - [DOCKER CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). 소프트웨어를 다운로드 및 설치할 계정을 만들어야 할 수 있습니다.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code에 대 한 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)입니다.    
    - [Visual Studio Code용 Python 확장](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - Python 패키지 설치를 위한 Pip (일반적으로 Python 설치에 포함 됨)

## <a name="get-the-sample-code"></a>샘플 코드 가져오기

1. [Azure 샘플에서 Azure Intelligent Edge 패턴](https://github.com/azure-samples/azure-intelligent-edge-patterns)으로 이동 합니다. 코드에 대 한 zip 파일을 복제 하거나 다운로드 합니다. 

    ![Zip 파일 다운로드](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    Zip에서 파일의 압축을 풉니다. 예제를 복제할 수도 있습니다.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>빌드 및 배포 모듈

1. Visual Studio Code에서 **GpuReferenceModules** 폴더를 엽니다.

    ![VS Code에서 GPUReferenceModules 열기](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. *에서deployment.template.js* 을 열고 컨테이너 레지스트리에 대해 참조 하는 매개 변수를 확인 합니다. 다음 파일에서 CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD 및 CONTAINER_REGISTRY_NAME 사용 됩니다.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. 새 파일을 만듭니다. 컨테이너 레지스트리 매개 변수에 대 한 값을 다음과 같이 입력 합니다 (이전 단계에서 식별 된 매개 변수 사용). 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    .Sample 파일은 다음과 *같습니다.*
    
    ![Env 파일 만들기 및 저장](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. **SampleSolution** 폴더에 파일을 *env* 로 저장 합니다.

5. Docker에 로그인 하려면 Visual Studio Code 통합 터미널에서 다음 명령을 입력 합니다. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Azure Portal 컨테이너 레지스트리의 **액세스 키** 섹션으로 이동 합니다. 레지스트리 이름, 암호 및 로그인 서버를 복사 하 여 사용 합니다.

    ![컨테이너 레지스트리의 액세스 키](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    자격 증명이 제공 되 면 로그인이 성공 합니다.

    ![성공한 로그인](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. 이미지를 Azure container registry로 푸시합니다. VS Code 탐색기에서 파일 ** 의deployment.template.js** 를 선택 하 고 마우스 오른쪽 단추로 클릭 한 다음 **빌드 및 푸시 IoT Edge 솔루션**을 선택 합니다. 

    ![IoT Edge 솔루션 빌드 및 푸시](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Python 및 Python 확장이 설치 되지 않은 경우 솔루션을 빌드하고 푸시할 때 설치 됩니다. 그러나이로 인해 빌드 시간이 길어질 수 있습니다. 

    이 단계가 완료 되 면 컨테이너 레지스트리에 모듈이 표시 됩니다.

    ![컨테이너 레지스트리의 모듈](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. 배포 매니페스트를 만들려면 **deployment.template.js** 를 마우스 오른쪽 단추로 클릭 한 다음 **IoT Edge 배포 매니페스트 생성**을 선택 합니다. 

    ![IoT Edge 배포 매니페스트 생성](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    알림은 배포 매니페스트가 생성 된 경로를 알려 줍니다. 매니페스트는 `deployment.amd64.json` **config** 폴더에 생성 되는 파일입니다. 

8. **구성** 폴더의 파일 **에서deployment.amd64.js** 를 선택한 다음 **단일 장치에 대 한 배포 만들기**를 선택 합니다. 파일 ** 에deployment.template.js** 을 사용 하지 마십시오. 

    ![단일 디바이스용 배포 만들기](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    **출력** 창에 배포에 성공 했다는 메시지가 표시 됩니다.

    ![출력에서 배포 성공](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>모듈 모니터링  

1. VS Code 명령 팔레트에서 **Azure IoT Hub: IoT Hub 선택**을 실행합니다.

2. 구성하려는 IoT Edge 디바이스가 포함된 구독 및 IoT Hub를 선택합니다. 이 경우 Azure Stack Edge 장치를 배포 하는 데 사용 되는 구독을 선택 하 고 Azure Stack Edge 장치에 대해 생성 된 IoT Edge 장치를 선택 합니다. 이는 이전 단계의 Azure Portal를 통해 compute를 구성 하는 경우에 발생 합니다.

3. VS Code 탐색기에서 Azure IoT Hub 섹션을 확장 합니다. **장치**에 Azure Stack에 지 장치에 해당 하는 IoT Edge 장치가 표시 됩니다. 

    1. 해당 장치를 선택 하 고 마우스 오른쪽 단추를 클릭 한 다음 **기본 제공 모니터링 시작 이벤트 끝점**을 선택 합니다.
  
        ![모니터링 시작](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. **장치 > 모듈로** 이동 하면 **GPU 모듈이** 실행 되 고 있는 것을 볼 수 있습니다.

        ![IoT Hub 모듈](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. 또한 VS Code 터미널은 IoT Hub 이벤트를 Azure Stack Edge 장치에 대 한 모니터링 출력으로 표시 해야 합니다.

        ![모니터링 출력](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        GPU를 사용 하 여 동일한 작업 집합 (도형 변환의 5000 반복)을 실행 하는 데 소요 된 시간이 CPU 보다 훨씬 작습니다.

## <a name="next-steps"></a>다음 단계

- [모듈을 사용 하도록 GPU를 구성](azure-stack-edge-j-series-configure-gpu-modules.md)하는 방법에 대해 자세히 알아보세요.
