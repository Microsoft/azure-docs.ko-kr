---
title: 자습서 - Azure IoT Central에서 비디오 분석 IoT Edge 인스턴스 만들기(Linux VM)
description: 이 자습서에서는 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿에 사용할 비디오 분석 IoT Edge 인스턴스를 만드는 방법을 보여줍니다.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.author: nandab
author: KishorIoT
ms.date: 07/31/2020
ms.openlocfilehash: 69e5b757036a2d68fa779e3fc232cc42a034e33c
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038018"
---
# <a name="tutorial-create-an-iot-edge-instance-for-video-analytics-linux-vm"></a>자습서: 비디오 분석용 IoT Edge 인스턴스 만들기(Linux VM)

Azure IoT Edge는 아래 항목을 배포하고 실행하여 클라우드 인텔리전스를 로컬로 제공하는 완전 관리형 서비스입니다.

* 사용자 지정 논리
* Azure 서비스
* 인공 지능

IoT Edge에서 이러한 서비스는 플랫폼 간 IoT 디바이스에서 직접 실행되므로, IoT 솔루션을 클라우드 또는 오프라인에서 안전하게 대규모로 실행할 수 있습니다.

이 자습서에서는 Azure VM에서 IoT Edge 디바이스를 준비하는 방법을 보여줍니다. IoT Edge 인스턴스는 Azure IoT Central 비디오 분석 - 개체 및 동작 감지 애플리케이션 템플릿에서 사용하는 라이브 비디오 분석 모듈을 실행합니다.

이 자습서에서는 다음을 수행하는 방법을 알아봅니다.
> [!div class="checklist"]
> * Azure IoT Edge 런타임이 설치된 Azure VM 만들기
> * 라이브 비디오 분석 모듈을 호스트하고 IoT Central에 연결할 IoT Edge 설치를 준비합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에, 이전 [Azure IoT Central에서 비디오 분석 애플리케이션 만들기](./tutorial-video-analytics-create-app.md) 자습서를 먼저 완료해야 합니다.

또한 Azure 구독이 필요합니다. Azure 구독이 아직 없는 경우 [Azure 가입 페이지](https://aka.ms/createazuresubscription)에서 무료로 만들 수 있습니다.

## <a name="deploy-azure-iot-edge"></a>Azure IoT Edge 배포

최신 IoT Edge 런타임 및 라이브 비디오 분석 모듈이 설치된 Azure VM을 만들려면 다음 단추를 선택합니다.

[![iotedge-vm-deploy를 위한 Azure에 배포하기 단추](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Flive-video-analytics%2Fmaster%2Fref-apps%2Flva-edge-iot-central-gateway%2Fvm_deploy%2FedgeModuleVMDeploy.json)

다음 표의 정보를 사용하여 **사용자 지정 배포** 양식을 작성합니다.

| 필드 | 값 |
| ----- | ----- |
| Subscription | Azure 구독을 선택합니다. |
| Resource group | *lva-rg* - 이전 자습서에서 만든 리소스 그룹입니다. |
| Azure 지역       | *미국 동부* |
| DNS 레이블 접두사 | VM의 고유한 DNS 접두사를 선택합니다. |
| 관리자 사용자 이름 | *AzureUser* |
| 관리자 암호 | 암호를 입력합니다. *scratchpad.txt* 파일에 암호를 기록해 둡니다. 나중에 필요합니다. |
| 범위 ID | 이전 자습서에서 게이트웨이 디바이스를 추가할 때 *scratchpad.txt* 파일에 기록해 둔 **범위 ID**입니다. |
| 디바이스 ID | *lva-gateway-001* - 이전 자습서에서 만든 게이트웨이 디바이스입니다. |
| 디바이스 키 | 이전 자습서에서 게이트웨이 디바이스를 추가할 때 *scratchpad.txt* 파일에 기록해 둔 디바이스 기본 키입니다. |
| Iot Central 앱 호스트 | 이전 자습서에서 *scratchpad.txt* 파일에 기록해 둔 **애플리케이션 URL**입니다. 예를 들어 *traders.azureiotcentral.com*입니다. |
| Iot Central 앱 Api 토큰 | 이전 자습서에서 기록해 둔 연산자 API 토큰입니다. |
| Iot Central 디바이스 프로비저닝 키 | 이전 자습서에서 *scratchpad.txt* 파일에 적어둔 기본 그룹 공유 액세스 서명 토큰입니다. |
| VM 크기 | *Standard_DS1_v2* |
| Ubuntu OS 버전 | *18.04-LTS* |
| 위치 | *[resourceGroup().location]* |

**검토 + 만들기**를 선택합니다. 유효성 검사가 완료되면 **만들기**를 선택합니다. 배포가 완료되는 데 보통 3분 정도 걸립니다. 배포가 완료되면 Azure Portal에서**lva-rg** 리소스 그룹으로 이동합니다.

## <a name="ensure-the-iot-edge-runtime-loads-the-modules"></a>IoT Edge 런타임이 모듈을 로드하는지 확인

Azure Portal에서 **lva-rg**로 이동하여 가상 머신을 선택합니다. 그런 다음, **지원 + 문제 해결** 섹션에서 **직렬 콘솔**을 선택합니다.

**Enter** 키를 눌러 `login:` 프롬프트를 불러옵니다. 사용자 이름은 *AzureUser*, 암호는 VM을 만들 때 선택한 암호를 사용합니다.

다음 명령을 실행하여 IoT Edge 런타임 버전을 확인합니다. 이 문서를 작성할 당시의 버전은 1.0.9입니다.

```bash
sudo iotedge --version
```

다음 명령을 사용하여 IoT Edge 모듈을 나열합니다.

```bash
sudo iotedge list
```

다음 5개 IoT Edge 모듈을 실행하도록 배포가 구성되었습니다.

* LvaEdgeGatewayModule
* edgeAgent
* edgeHub
* lvaEdge
* lvaYolov3

이 배포에서는 라이브 비디오 분석에 필요한 모듈을 사용하여 맞춤형 IoT Edge 환경을 만들었습니다. 이 배포에서는 IoT Edge 런타임이 IoT 디바이스 프로비저닝 서비스를 사용하여 IoT Central에 연결하도록 기본 **config.yaml**을 업데이트했습니다. 또한 이 배포에서는 모듈에 추가 구성 데이터를 제공하기 위해 **data/storage** 폴더에 **state.json**이라는 파일을 만들었습니다. 자세한 내용은 [비디오 분석용 IoT Edge 인스턴스 만들기(Intel NUC)](./tutorial-video-analytics-iot-edge-nuc.md) 자습서를 참조하세요.

IoT Edge 디바이스 문제를 해결하려면 [IoT Edge 디바이스 문제 해결](https://docs.microsoft.com/azure/iot-edge/troubleshoot)을 참조하세요.

## <a name="use-the-rtsp-simulator"></a>RTSP 시뮬레이터 사용

IoT Edge 디바이스에 연결할 실제 카메라 디바이스가 없는 경우 비디오 분석 애플리케이션 템플릿에서 제공하는 두 가지 시뮬레이션된 카메라 디바이스를 사용할 수 있습니다. 이 섹션에서는 IoT Edge 디바이스에서 시뮬레이션된 비디오 스트림을 사용하는 방법을 보여줍니다.

이 지침에서는 docker 컨테이너의 RTSP 시뮬레이터로 [Live555 Media Server](http://www.live555.com/mediaServer/)를 사용합니다.

> [!NOTE]
> 이 리포지토리의 타사 소프트웨어에 대한 참조는 정보 및 편의 제공 목적으로만 사용됩니다. Microsoft는 타사 소프트웨어에 대한 권한을 보증하거나 제공하지 않습니다. 자세한 내용은 [Live555 Media Server](http://www.live555.com/mediaServer/)를 참조하세요.

다음 명령을 사용하여 IoT Edge VM의 docker 컨테이너에서 **rtspvideo** 유틸리티를 실행합니다. docker 컨테이너는 다음과 같이 백그라운드 RTSP 스트림을 만듭니다.

```bash
sudo docker run -d --name live555 --rm -p 554:554 mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2
```

다음 명령을 사용하여 docker 컨테이너를 나열합니다.

```bash
sudo docker ps
```

이 목록에는 **live555**라는 컨테이너가 포함됩니다.

## <a name="next-steps"></a>다음 단계

Azure에서 실행되는 Linux VM에 IoT Edge 런타임, LVA 모듈 및 Live555 시뮬레이션 스트림을 배포했습니다.

카메라를 관리하려면 다음 자습서를 진행하세요.

> [!div class="nextstepaction"]
> [비디오 분석 - 개체 및 동작 감지 애플리케이션 모니터링 및 관리](./tutorial-video-analytics-manage.md)
