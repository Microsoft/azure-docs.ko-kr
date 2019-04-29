---
title: Linux에 Azure IoT Edge 설치 | Microsoft Docs
description: Ubuntu를 실행하는 Linux AMD64 디바이스에서 Azure IoT Edge 설치 지침
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 5829606f3ad226507c49f18dcc8ac4831d573b17
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126316"
---
# <a name="install-the-azure-iot-edge-runtime-on-linux-x64"></a>Linux(x64)에서 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

자세한 내용은 참조 하세요 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)합니다.

이 문서에서는 Ubuntu Linux x64 (Intel/AMD)에 Azure IoT Edge 런타임을 설치 하는 단계를 나열 합니다. IoT Edge 장치입니다. 가리킵니다 [Azure IoT Edge 지원](support.md#operating-systems) AMD64 운영 체제의 목록은 합니다.

> [!NOTE]
> Linux 소프트웨어 저장소의 패키지는 각 패키지에 있는 사용 조건에 따릅니다(/usr/share/doc/*package-name*). 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft 키 및 소프트웨어 리포지토리 피드 등록

IoT Edge 런타임 설치 장치를 준비 합니다.


저장소 구성을 설치 합니다. 중 하나를 선택 합니다 **16.04** 하거나 **18.04** Ubuntu의 릴리스에 대 한 적절 하 게 코드 조각:

> [!NOTE]
> Ubuntu의 버전에 대 한 올바른 코드 상자에서 코드 조각을 선택 해야 합니다.

* 에 대 한 **Ubuntu 16.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > ./microsoft-prod.list
   ```

* 에 대 한 **Ubuntu 18.04**:
   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > ./microsoft-prod.list
   ```
   
생성 된 목록에 복사 합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Microsoft GPG 공개 키를 설치 합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-the-container-runtime"></a>컨테이너 런타임 설치

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 런타임을 사용합니다. 사용 하는 좋습니다 프로덕션 시나리오에는 [모 비 기반](https://mobyproject.org/) 아래에 제공 된 엔진입니다. Azure IoT Edge에서 공식적으로 지원되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

Apt 업데이트를 수행 합니다.

   ```bash
   sudo apt-get update
   ```

Moby 엔진을 설치합니다.

   ```bash
   sudo apt-get install moby-engine
   ```

Moby CLI(명령줄 인터페이스)를 설치합니다. CLI는 개발에 유용하지만 프로덕션 배포에서는 선택 사항입니다.

   ```bash
   sudo apt-get install moby-cli
   ```

## <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 설치

합니다 **IoT Edge 보안 디먼** 제공 하 고 IoT Edge 장치의 보안 표준을 유지 관리 합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

또한 이 설치 명령은 iothsmlib가 표시되지 않으면 표준 버전의 **iothsmlib**를 설치합니다.

Apt 업데이트를 수행 합니다.

   ```bash
   sudo apt-get update
   ```

보안 디먼을 설치합니다. 패키지가 `/etc/iotedge/`에 설치됩니다.

   ```bash
   sudo apt-get install iotedge
   ```

## <a name="configure-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 구성

물리적 디바이스를 Azure IoT Hub에 있는 디바이스 ID와 연결하도록 IoT Edge 런타임을 구성합니다.

디먼은 `/etc/iotedge/config.yaml`에 있는 구성 파일을 사용하여 구성할 수 있습니다. 이 파일은 기본적으로 쓰기 금지되어 있습니다 편집하려면 관리자 권한이 필요합니다.

IoT Hub에서 제공하는 디바이스 연결 문자열을 사용하여 단일 IoT Edge 디바이스를 수동으로 프로비전할 수 있습니다. 또는 Device Provisioning Service를 사용하여 자동으로 디바이스를 프로비전할 수 있습니다. 이는 많은 디바이스를 프로비전할 때 유용합니다. 프로비전 선택 사항에 따라 적절한 설치 스크립트를 선택합니다.

### <a name="option-1-manual-provisioning"></a>옵션 1: 수동 프로비전

디바이스를 수동으로 프로비전하려면 IoT Hub에 새 디바이스를 등록하여 만들 수 있는 [디바이스 연결 문자열](how-to-register-device-portal.md)을 디바이스에 프로비전해야 합니다.

구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로 비전 섹션을 찾습니다. 주석 처리를 제거 합니다 **수동** 모드 프로 비전 및 프로 비전 모드 dps 주석 처리 되어 있는지 확인 합니다. **device_connection_string**의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다.

   ```yaml
   provisioning:
     source: "manual"
     device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   # provisioning:
   #   source: "dps"
   #   global_endpoint: "https://global.azure-devices-provisioning.net"
   #   scope_id: "{scope_id}"
   #   registration_id: "{registration_id}"
   ```

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>옵션 2: 자동 프로비전

디바이스를 자동으로 프로비전하려면 [Device Provisioning Service를 설정하고 디바이스 등록 ID를 검색](how-to-auto-provision-simulated-device-linux.md)합니다. 자동 프로비전은 TPM(신뢰할 수 있는 플랫폼 모듈) 칩이 있는 디바이스에서만 작동합니다. 예를 들어 Raspberry Pi 디바이스는 기본적으로 TPM을 제공하지 않습니다.

구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로 비전 섹션을 찾습니다. 주석 처리를 제거 합니다 **dps** 모드 및 수동 섹션을 주석 프로 비전 합니다. **scope_id** 및 **registration_id** 값을 IoT Hub Device Provisioning Service 및 TPM이 있는 IoT Edge 디바이스의 값으로 업데이트합니다.

   ```yaml
   # provisioning:
   #   source: "manual"
   #   device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
  
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "{scope_id}"
     registration_id: "{registration_id}"
   ```

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

이전 섹션의 **수동 구성** 단계를 사용한 경우 IoT Edge 런타임을 디바이스에 성공적으로 프로비전하고 실행해야 합니다. **자동 구성** 단계를 사용한 경우 사용자를 대신하여 런타임에서 디바이스를 IoT 허브에 등록할 수 있도록 몇 가지 추가 단계를 수행해야 합니다. 다음 단계를 참조 하세요. [만들기 및 Linux 가상 컴퓨터에서 시뮬레이션된 된 TPM IoT Edge 장치를 프로 비전](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)합니다.

다음을 사용하여 IoT Edge 디먼의 상태를 확인할 수 있습니다.

```bash
systemctl status iotedge
```

다음을 사용하여 디먼 로그를 검사합니다.

```bash
journalctl -u iotedge --no-pager --no-full
```

다음을 사용하여 실행 중인 모듈을 나열합니다.

```bash
sudo iotedge list
```

## <a name="tips-and-suggestions"></a>팁 및 제안 사항

`iotedge` 명령을 실행하려면 상승된 권한이 필요합니다. 런타임을 설치한 후 머신에서 로그아웃했다가 다시 로그인하면 권한이 자동으로 업데이트됩니다. 그 전까지는 `iotedge` 명령 앞에 **sudo**를 사용합니다.

리소스가 제한된 디바이스에서는 [문제 해결 가이드](troubleshoot.md)의 지침에 따라 *OptimizeForPerformance* 환경 변수를 *false*로 설정하는 것이 좋습니다.

프록시 서버가 있는 네트워크의 경우 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)의 단계를 수행합니다.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

Linux 디바이스에서 IoT Edge 설치를 제거하려면 명령줄에서 다음 명령을 사용합니다.

IoT Edge 런타임을 제거합니다.

```bash
sudo apt-get remove --purge iotedge
```

IoT Edge 런타임을 제거하면 만든 컨테이너는 중지되지만 디바이스에는 계속 남아 있습니다. 모든 컨테이너를 보고 남아 있는 항목을 확인합니다.

```bash
sudo docker ps -a
```

두 개의 런타임 컨테이너를 포함하여 디바이스에서 컨테이너를 삭제합니다.

```bash
sudo docker rm -f <container name>
```

마지막으로 디바이스에서 컨테이너 런타임을 제거합니다.

```bash
sudo apt-get remove --purge moby-cli
sudo apt-get remove --purge moby-engine
```

## <a name="next-steps"></a>다음 단계

런타임을 설치하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge 런타임이 제대로 설치를 사용 하 여 문제가 경우 체크 아웃 합니다 [문제 해결](troubleshoot.md) 페이지입니다.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
