---
title: Azure IoT Hub Device Provisioning 서비스 자동 프로비전을 사용하여 IoT Hub에 MXChip IoT DevKit을 등록하는 방법 | Microsoft Docs
description: Azure IoT Hub Device Provisioning 서비스 자동 프로비전을 사용하여 IoT Hub에 MXChip IoT DevKit을 등록하는 방법
author: liydu
ms.author: liydu
ms.date: 12/18/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: jeffya
ms.openlocfilehash: 80e4895e0b276e701a6d7f10d8fc67649db0f188
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122829"
---
# <a name="use-azure-iot-hub-device-provisioning-service-auto-provisioning-to-register-the-mxchip-iot-devkit-with-iot-hub"></a>Azure IoT Hub Device Provisioning 서비스 자동 프로비전을 사용하여 IoT Hub에 MXChip IoT DevKit 등록

이 문서에서는 Azure IoT Hub Device Provisioning 서비스 [자동 프로비전](concepts-auto-provisioning.md)을 사용하여 Azure IoT Hub에 MXChip IoT DevKit을 등록하는 방법을 설명합니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

* 디바이스에서 Device Provisioning Service의 글로벌 엔드포인트를 구성합니다.
* UDS(고유 디바이스 비밀)를 사용하여 X.509 인증서를 생성합니다.
* 개별 디바이스를 등록합니다.
* 디바이스가 등록되었는지 확인합니다.

[MXChip IoT DevKit](https://aka.ms/iot-devkit)는 풍부한 주변 장치 및 센서가 포함된 올인원 Arduino 호환 보드입니다. Visual Studio Code에서 [Azure IoT Device Workbench](https://aka.ms/iot-workbench) 또는 [Azure IoT Tools](https://aka.ms/azure-iot-tools) 확장 팩을 사용하여 개발할 수 있습니다. DevKit에는 Azure 서비스를 활용하는 IoT(사물 인터넷) 솔루션의 프로토타입을 안내하기 위해 확장 중인 [프로젝트 카탈로그](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/)가 제공됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이 자습서를 완료하려면 먼저 다음 작업을 수행합니다.

* DevKit의 Wifi를 구성 하 고 단계를 수행 하 여 개발 환경 준비 [클라우드에서 Azure IoT Hub에 IoT DevKit AZ3166 연결](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)합니다.
* [DevKit 펌웨어 업데이트](https://microsoft.github.io/azure-iot-developer-kit/docs/firmware-upgrading/) 자습서를 사용하여 최신 펌웨어(1.3.0 이상)로 업그레이드합니다.
* [Azure Portal에서 IoT Hub Device Provisioning 서비스 설정](/azure/iot-dps/quick-setup-auto-provision) 단계에 따라 IoT Hub를 만들어 Device Provisioning Service 인스턴스와 연결합니다.

## <a name="open-sample-project"></a>샘플 프로젝트 열기

1. IoT DevKit가 컴퓨터에 연결되어 있지 **않은지** 확인합니다. VS Code를 시작하고 DevKit를 컴퓨터에 연결합니다.

1. `F1` 키를 클릭하여 명령 팔레트를 열고, **Azure IoT Device Workbench: 예제 열기...** 를 입력하고 선택합니다. 보드로 **IoT DevKit**를 선택합니다.

1. IoT Workbench 예제 페이지에서 **DPS를 사용하여 디바이스 등록**을 찾고 **샘플 열기**를 클릭합니다. 기본 경로를 선택하여 샘플 코드를 다운로드합니다.
    ![샘플 열기](media/how-to-connect-mxchip-iot-devkit/open-sample.png)

## <a name="save-a-unique-device-secret-on-device-security-storage"></a>디바이스 보안 스토리지에 고유 디바이스 비밀 저장

자동 프로비전은 디바이스의 [증명 메커니즘](concepts-security.md#attestation-mechanism)에 따라 디바이스에 구성할 수 있습니다. MXChip IoT DevKit는 [신뢰할 수 있는 컴퓨팅 그룹](https://trustedcomputinggroup.org)의 [Device Identity Composition Engine](https://trustedcomputinggroup.org/wp-content/uploads/Foundational-Trust-for-IOT-and-Resource-Constrained-Devices.pdf)을 사용합니다. DevKit의 STSAFE 보안 칩([STSAFE-A100](https://microsoft.github.io/azure-iot-developer-kit/docs/understand-security-chip/))에 저장된 UDS(**고유 디바이스 비밀**)가 디바이스 고유의 [X.509 인증서](concepts-security.md#x509-certificates)를 생성하는 데 사용됩니다. 이 인증서는 나중에 Device Provisioning Service의 등록 프로세스에 사용되며 런타임에 등록하는 동안에도 사용됩니다.

다음 샘플에서처럼 일반적인 UDS는 64자 문자열입니다.

```
19e25a259d0c2be03a02d416c05c48ccd0cc7d1743458aae1cb488b074993eae
```

DevKit에 UDS를 저장하려면:

1. VS Code에서 상태 표시줄을 클릭하여 DevKit에 대한 COM 포트를 선택합니다.
  ![COM 포트 선택](media/how-to-connect-mxchip-iot-devkit/select-com.png)

1. DevKit에서 **A 단추**를 누른 상태로 **다시 설정** 단추를 눌렀다 떼고, **A 단추**를 뗍니다. DevKit가 구성 모드로 전환합니다.

1. `F1` 키를 클릭하여 명령 팔레트를 열고, **Azure IoT Device Workbench: 디바이스 설정 구성... > UDS(고유한 디바이스 문자열) 구성**을 입력하고 선택합니다.
  ![UDS 구성](media/how-to-connect-mxchip-iot-devkit/config-uds.png)

1. 생성된 UDS 문자열을 적어 둡니다. X.509 인증서를 생성하는 데 필요합니다. 그런 다음, `Enter` 키를 누릅니다.
  ![UDS 복사](media/how-to-connect-mxchip-iot-devkit/copy-uds.png)

1. UDS가 STSAFE에 성공적으로 구성되었는지 알림에서 확인합니다.
  ![UDS 성공 구성](media/how-to-connect-mxchip-iot-devkit/config-uds-success.png)

> [!NOTE]
> 또는 Putty와 같은 유틸리티를 사용하여 직렬 포트를 통해 UDS를 구성할 수 있습니다. 이렇게 하려면 [ 구성 모드 사용](https://microsoft.github.io/azure-iot-developer-kit/docs/use-configuration-mode/)을 따릅니다.

## <a name="update-the-global-device-endpoint-and-id-scope"></a>글로벌 디바이스 엔드포인트 및 ID 범위 업데이트

디바이스 코드에서 테넌트 격리를 보장하도록 [디바이스 프로비저닝 엔드포인트](/azure/iot-dps/concepts-service#device-provisioning-endpoint) 및 ID 범위를 지정해야 합니다.

1. Azure Portal에서 Device Provisioning Service에 대한 **개요** 창을 선택하고, **글로벌 디바이스 엔드포인트** 및 **ID 범위** 값을 적어둡니다.
  ![Device Provisioning Service 글로벌 엔드포인트 및 ID 범위](media/how-to-connect-mxchip-iot-devkit/dps-global-endpoint.png)

1. **DeKitDPS.ino**를 엽니다. `[Global Device Endpoint]` 및 `[ID Scope]`를 찾아 방금 적어둔 값으로 바꿉니다.
  ![디바이스 프로비저닝 서비스 엔드포인트](media/how-to-connect-mxchip-iot-devkit/endpoint.png)

1. 코드의 `registrationId` 변수를 채웁니다. 최대 128자의 영숫자, 소문자 및 하이픈 조합만 허용됩니다. 값도 적어 둡니다.
  ![등록 ID](media/how-to-connect-mxchip-iot-devkit/registration-id.png)

1. `F1` 키를 클릭한 다음, **Azure IoT Device Workbench: 디바이스 코드 업로드**를 입력하고 선택합니다. DevKit에 코드를 컴파일하고 업로드하기 시작합니다.
  ![디바이스 업로드](media/how-to-connect-mxchip-iot-devkit/device-upload.png)

## <a name="generate-x509-certificate"></a>X.509 인증서 생성

이 샘플에서 사용되는 [증명 메커니즘](/azure/iot-dps/concepts-device#attestation-mechanism)은 X.509 인증서입니다. 유틸리티를 사용하여 생성해야 합니다.

> [!NOTE]
> X.509 인증서 생성기는 Windows만 지원합니다.

1. VS Code에서 `F1` 키를 클릭하고 **새 터미널 열기**를 선택하여 터미널 창을 엽니다.

1. `tool` 폴더에서 `dps_cert_gen.exe`를 실행합니다.

1. 컴파일된 이진 파일 위치를 `..\.build\DevKitDPS`로 지정합니다. 그런 다음, 방금 적어둔 **UDS** 및 **registrationId**를 붙여넣습니다. 
  ![X.509 생성](media/how-to-connect-mxchip-iot-devkit/gen-x509.png)

1. `.pem` X.509 인증서는 동일한 폴더에 생성됩니다.
  ![X.509 파일](media/how-to-connect-mxchip-iot-devkit/pem-file.png)

## <a name="create-a-device-enrollment-entry"></a>디바이스 등록 항목 만들기

1. Azure Portal에서 디바이스 프로비전 서비스를 열고, 등록 관리 섹션으로 이동하고, **개별 등록 추가**를 클릭합니다.
  ![개별 등록 추가](media/how-to-connect-mxchip-iot-devkit/add-enrollment.png)

1. **기본 인증서 .pem 또는 .cer 파일** 옆의 파일 아이콘을 클릭하여 생성된 `.pem` 파일을 업로드합니다.
  ![.pem 업로드](media/how-to-connect-mxchip-iot-devkit/upload-pem.png)

## <a name="verify-the-devkit-is-registered-with-azure-iot-hub"></a>DevKit가 Azure IoT Hub에 등록되었는지 확인

DevKit의 **다시 설정** 단추를 누릅니다. DevKit 화면에 **DPS 연결됨!** 이 표시됩니다. 디바이스가 다시 부팅되면 다음 작업이 수행됩니다.

1. 디바이스가 Device Provisioning Service에 등록 요청을 보냅니다.
1. Device Provisioning Service에서 디바이스가 응답하는 등록 챌린지를 다시 보냅니다.
1. 등록에 성공하면 Device Provisioning Service는 IoT Hub URI, 디바이스 ID 및 암호화된 키를 디바이스로 다시 보냅니다.
1. 장치의 IoT Hub 클라이언트 애플리케이션이 사용자 허브에 연결됩니다.
1. 허브에 성공적으로 연결되면 IoT Hub의 Device Explorer에 디바이스가 표시됩니다.
  ![등록된 디바이스](./media/how-to-connect-mxchip-iot-devkit/device-registered.png)

## <a name="problems-and-feedback"></a>문제 및 피드백

문제가 발생하면 Lot DevKit [FAQ](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/)를 참조하거나 다음 지원 채널을 통해 문의해주세요.

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [스택 오버플로](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 디바이스가 Azure IoT Hub에 자동으로 등록될 수 있도록 Device Identity Composition Engine을 사용하여 디바이스를 Device Provisioning Service에 안전하게 등록하는 방법을 알아보았습니다. 

요약하면 다음 방법을 배웠습니다.

> [!div class="checklist"]
> * 디바이스에서 Device Provisioning Service의 글로벌 엔드포인트를 구성합니다.
> * 고유 디바이스 비밀을 사용하여 X.509 인증서를 생성합니다.
> * 개별 디바이스를 등록합니다.
> * 디바이스가 등록되었는지 확인합니다.

[시뮬레이트된 디바이스 만들기 및 프로비전](./quick-create-simulated-device.md) 방법을 알아봅니다.

