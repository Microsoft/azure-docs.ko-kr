---
title: Azure Marketplace에서 Microsoft Azure Stack Edge 장치에 GPU 모듈을 배포 합니다. Microsoft Docs
description: 계산을 사용 하도록 설정 하 고 로컬 UI를 통해 Azure Stack Edge 장치를 계산 하도록 설정 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/25/2020
ms.author: alkohli
ms.openlocfilehash: ac9ff084476f37348b72bdb42b9b74660132b762
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254637"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-gpu-device"></a>Azure Stack에 지 GPU 장치의 Azure Marketplace에서 GPU 사용 IoT 모듈 배포

이 문서에서는 Azure Stack Edge 장치의 Azure Marketplace에서 GPU (그래픽 처리 장치) 사용 IoT Edge 모듈을 배포 하는 방법을 설명 합니다. 

이 문서에서는 다음 방법을 설명합니다.
  - GPU 모듈을 실행할 Azure Stack Edge를 준비 합니다.
  - Azure Marketplace에서 GPU 사용 IoT 모듈을 다운로드 하 고 배포 합니다.
  - 모듈 출력을 모니터링 합니다.

## <a name="about-sample-module"></a>샘플 모듈 정보

이 문서의 GPU 샘플 모듈에는 GPU에 대 한 CPU에 대 한 PyTorch 및 TensorFlow 벤치마킹 샘플 코드가 포함 되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음을 확인합니다.

- GPU 사용 1 노드 Azure Stack Edge 장치에 액세스할 수 있습니다. 이 장치는 Azure의 리소스를 사용 하 여 활성화 됩니다. 
- 이 장치에서 계산을 구성 했습니다. 
    - [자습서: Azure Stack Edge 장치에서 계산 구성](azure-stack-edge-gpu-deploy-configure-compute.md)의 단계를 따릅니다.
    - 다음을 확인 하세요. 
- Windows 클라이언트에서 다음과 같은 개발 리소스가 있습니다.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Visual Studio Code에 대 한 Azure IoT Edge 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)입니다.   


## <a name="get-module-from-azure-marketplace"></a>Azure Marketplace에서 모듈 가져오기

1. [Azure Marketplace의 모든 앱을](https://azuremarketplace.microsoft.com/marketplace/apps)검색 합니다.

    ![Azure Marketplace에서 앱 찾아보기](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. **Gpu 시작**을 검색 합니다.

    ![GPU 샘플 모듈 검색](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. **지금 가져오기**를 선택합니다.

    ![샘플 모듈 가져오기](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4.  **계속** 을 선택 하 여 공급자의 사용 약관 및 개인 정보 취급 방침을 승인 합니다. 

    ![샘플 모듈 가져오기](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Azure Stack Edge 장치를 배포 하는 데 사용 하는 구독을 선택 합니다.

    ![구독 선택](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Azure Stack Edge 장치를 구성할 때 만든 IoT Hub 서비스의 이름을 입력 합니다. 이 IoT Hub 서비스 이름을 찾으려면 Azure Portal에서 장치와 연결 된 Azure Stack Edge 리소스로 이동 합니다. 

    1. 왼쪽 창 메뉴 옵션에서 **가장자리 계산 > 시작**으로 이동 합니다. 

    1. **Edge 계산 구성** 타일에서 **구성 보기**를 선택 합니다. 

        ![계산 구성 보기](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. **Edge 계산 구성** 블레이드에서 다음을 수행 합니다.

        1. Azure Stack에 지 장치에서 계산을 구성할 때 생성 된 IoT Hub 서비스를 적어 둡니다.
        2. 계산을 구성할 때 생성 된 IoT Edge 장치의 이름을 적어둡니다. 이후 단계에서이 이름을 사용 합니다.

        ![Edge 계산 구성](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. **디바이스에 배포**를 선택합니다.

11. IoT Edge 장치의 이름을 입력 하거나 **장치 찾기**   를 선택 하 여 허브에 등록 된 장치 중 하나를 찾습니다.

    ![장치 찾기](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12.  **만들기**   를 선택 하 여 필요한 경우 다른 모듈 추가를 포함 하 여 배포 매니페스트를 구성 하는 표준 프로세스를 계속 합니다. 이미지 URI, 만들기 옵션 및 desired 속성과 같은 새 모듈에 대한 세부 정보가 미리 정의되어 있지만 변경할 수 있습니다.

    ![만들기 선택](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. 모듈이 Azure Portal의 IoT Hub에 배포 되었는지 확인 합니다. 장치를 선택 하 고 모듈 **설정**을 선택 하면   모듈은 **IoT Edge 모듈**섹션에 나열 됩니다   .

    ![만들기 선택](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>모듈 모니터링  

1. VS Code 명령 팔레트에서 **Azure IoT Hub: IoT Hub 선택**을 실행합니다.

2. 구성하려는 IoT Edge 디바이스가 포함된 구독 및 IoT Hub를 선택합니다. 이 경우 Azure Stack Edge 장치를 배포 하는 데 사용 되는 구독을 선택 하 고 Azure Stack Edge 장치에 대해 생성 된 IoT Edge 장치를 선택 합니다. 이는 이전 단계의 Azure Portal를 통해 compute를 구성 하는 경우에 발생 합니다.

3. VS Code 탐색기에서 Azure IoT Hub 섹션을 확장 합니다. **장치**에 Azure Stack에 지 장치에 해당 하는 IoT Edge 장치가 표시 됩니다. 

    1. 해당 장치를 선택 하 고 마우스 오른쪽 단추를 클릭 한 다음 **기본 제공 모니터링 시작 이벤트 끝점**을 선택 합니다.
  
        ![모니터링 시작](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. **장치 > 모듈로** 이동 하면 **GPU 모듈이** 실행 되 고 있는 것을 볼 수 있습니다.

    3. 또한 VS Code 터미널은 IoT Hub 이벤트를 Azure Stack Edge 장치에 대 한 모니터링 출력으로 표시 해야 합니다.

        ![모니터링 출력](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        GPU를 사용 하 여 동일한 작업 집합 (도형 변환의 5000 반복)을 실행 하는 데 소요 된 시간이 CPU 보다 훨씬 작습니다.

## <a name="next-steps"></a>다음 단계

- [모듈을 사용 하도록 GPU를 구성](azure-stack-edge-j-series-configure-gpu-modules.md)하는 방법에 대해 자세히 알아보세요.
