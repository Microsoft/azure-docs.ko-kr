---
title: Linux에 Azure IoT Edge 설치 | Microsoft Docs
description: Ubuntu 또는 Raspbian를 실행 하는 Linux 장치에 대 한 설치 지침 Azure IoT Edge
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 06/22/2020
ms.author: kgremban
ms.openlocfilehash: 4078d7e6c20571db2387cfd138ecb325fc3469e7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89022091"
---
# <a name="install-the-azure-iot-edge-runtime-on-debian-based-linux-systems"></a>Debian 기반 Linux 시스템에서 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. 디바이스가 IoT Edge 런타임을 사용하여 구성되면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. 자세히 알아보려면 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조 하세요.

이 문서에는 X64, ARM32 또는 ARM64 Linux 장치에 Azure IoT Edge 런타임을 설치 하는 단계가 나와 있습니다. Ubuntu Server 16.04, Ubuntu Server 18.04 및 Raspbian Stretch의 설치 패키지를 제공 합니다. 지원 되는 Linux 운영 체제 및 아키텍처 목록은 [지원 되는 Azure IoT Edge 시스템](support.md#operating-systems) 을 참조 하세요.

> [!NOTE]
> Linux 소프트웨어 저장소의 패키지는 각 패키지에 있는 사용 조건에 따릅니다(/usr/share/doc/*package-name*). 패키지를 사용하기 전에 사용 조건을 읽어보세요. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

## <a name="install-iot-edge-and-container-runtimes"></a>IoT Edge 및 컨테이너 런타임 설치

다음 섹션을 사용 하 여 최신 버전의 Azure IoT Edge runtime을 장치에 설치 합니다.

>[!NOTE]
>ARM64 장치에 대 한 지원은 [공개 미리 보기로](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)제공 됩니다.

### <a name="register-microsoft-key-and-software-repository-feed"></a>Microsoft 키 및 소프트웨어 리포지토리 피드 등록

IoT Edge 런타임 설치를 위해 장치를 준비 합니다.

리포지토리 구성을 설치 합니다. 장치 운영 체제와 일치 하는 **16.04** 또는 **18.04** 명령을 선택 합니다.

* **Ubuntu Server 16.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/16.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Ubuntu Server 18.04**:

   ```bash
   curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

* **Raspbian Stretch**:

   ```bash
   curl https://packages.microsoft.com/config/debian/stretch/multiarch/prod.list > ./microsoft-prod.list
   ```

생성 된 목록을 복사 합니다.

   ```bash
   sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```

Microsoft GPG 공개 키를 설치 합니다.

   ```bash
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

### <a name="install-a-container-runtime"></a>컨테이너 런타임 설치

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 런타임을 사용합니다. 프로덕션 시나리오의 경우 아래에 제공 된 [Moby 기반](https://mobyproject.org/) 엔진을 사용 하는 것이 좋습니다. Moby 엔진은 Azure IoT Edge에서 공식적으로 지원 되는 유일한 컨테이너 엔진입니다. Docker CE/EE 컨테이너 이미지는 Moby 런타임과 호환 가능합니다.

장치에서 패키지 목록을 업데이트 합니다.

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

Moby 컨테이너 런타임을 설치할 때 오류가 발생 하는 경우이 문서의 뒷부분에서 설명 하는 단계에 따라 [moby 호환성에 대해 Linux 커널을 확인](#verify-your-linux-kernel-for-moby-compatibility)합니다.

### <a name="install-the-azure-iot-edge-security-daemon"></a>Azure IoT Edge 보안 디먼 설치

**IoT Edge 보안 디먼** 은 IoT Edge 장치에서 보안 표준을 제공 하 고 유지 관리 합니다. 디먼은 부팅할 때마다 시작되며, 나머지 IoT Edge 런타임을 시작하여 디바이스를 부트스트랩합니다.

장치에서 패키지 목록을 업데이트 합니다.

   ```bash
   sudo apt-get update
   ```

사용할 수 있는 IoT Edge 버전을 확인 합니다.

   ```bash
   apt list -a iotedge
   ```

최신 버전의 보안 디먼을 설치 하려는 경우 다음 명령을 사용 하 여 최신 버전의 **libiothsm** 패키지를 설치 합니다.

   ```bash
   sudo apt-get install iotedge
   ```

특정 버전의 보안 디먼을 설치 하려는 경우 apt 목록 출력의 버전을 지정 합니다. 또한 **libiothsm** 패키지에 대해 동일한 버전을 지정 합니다. 그렇지 않으면 최신 버전이 설치 됩니다. 예를 들어 다음 명령은 1.0.8 릴리스의 최신 버전을 설치 합니다.

   ```bash
   sudo apt-get install iotedge=1.0.8* libiothsm-std=1.0.8*
   ```

설치 하려는 버전이 나열 되지 않은 경우 [릴리스 자산을 사용 하 여 런타임 설치](#install-runtime-using-release-assets)의 단계를 따릅니다. 이 섹션에서는 이전 버전의 IoT Edge 보안 디먼 또는 릴리스 후보 버전을 대상으로 하는 방법을 보여 줍니다.

에 IoT Edge 성공적으로 설치 되 면 `/etc/iotedge/` 출력은 구성 파일을 업데이트 하 라는 메시지를 표시 합니다. 다음 섹션으로 계속 진행 하 여 장치 프로 비전을 완료 합니다.

## <a name="configure-the-security-daemon"></a>보안 디먼 구성

물리적 디바이스를 Azure IoT Hub에 있는 디바이스 ID와 연결하도록 IoT Edge 런타임을 구성합니다.

디먼은 `/etc/iotedge/config.yaml`에 있는 구성 파일을 사용하여 구성할 수 있습니다. 이 파일은 기본적으로 쓰기 금지되어 있습니다 편집하려면 관리자 권한이 필요합니다.

IoT Hub에서 제공하는 디바이스 연결 문자열을 사용하여 단일 IoT Edge 디바이스를 수동으로 프로비전할 수 있습니다. 또는 Device Provisioning Service를 사용하여 자동으로 디바이스를 프로비전할 수 있습니다. 이는 많은 디바이스를 프로비전할 때 유용합니다. 프로비전 선택 사항에 따라 적절한 설치 스크립트를 선택합니다.

### <a name="option-1-manual-provisioning"></a>옵션 1: 수동 프로비전

수동으로 디바이스를 프로비전하려면 IoT Hub에 새 디바이스를 등록하여 만들 수 있는 [디바이스 연결 문자열](how-to-register-device.md#register-in-the-azure-portal)을 디바이스에 프로비전해야 합니다.

구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로 비전 구성을 찾고 **수동 프로 비전 구성** 섹션의 주석 처리를 제거 합니다. **device_connection_string**의 값을 IoT Edge 디바이스의 연결 문자열로 업데이트합니다. 다른 프로 비전 섹션이 주석 처리 되었는지 확인 합니다. **프로 비전:** 줄에 앞에 공백이 없고 중첩 된 항목이 두 개의 공백으로 들여쓰기 되는지 확인 합니다.

```yml
# Manual provisioning configuration
provisioning:
  source: "manual"
  device_connection_string: "<ADD DEVICE CONNECTION STRING HERE>"
```

클립보드 내용을 Nano에 붙여넣거나 `Shift+Right Click` 키를 누릅니다 `Shift+Insert` .

파일을 저장하고 닫습니다.

   `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

### <a name="option-2-automatic-provisioning"></a>옵션 2: 자동 프로비전

IoT Edge 장치는 [DPS (Azure IoT Hub 장치 프로 비전 서비스)](../iot-dps/index.yml)를 사용 하 여 자동으로 프로 비전 할 수 있습니다. 현재 IoT Edge는 자동 프로 비전을 사용할 때 세 가지 증명 메커니즘을 지원 하지만, 사용자의 하드웨어 요구 사항에 영향을 줄 수 있습니다. 예를 들어 Raspberry Pi 장치는 기본적으로 TPM (신뢰할 수 있는 플랫폼 모듈) 칩과 함께 제공 되지 않습니다. 자세한 내용은 다음 아티클을 참조하세요.

* [Linux VM에서 가상 TPM을 사용 하 여 IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-simulated-device-linux.md)
* [X.509 인증서를 사용 하 여 IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-x509-certs.md)
* [대칭 키 증명을 사용 하 여 IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-symmetric-keys.md)

이러한 문서는 DPS에서 등록를 설정 하 고 증명에 대 한 적절 한 인증서 또는 키를 생성 하는 과정을 안내 합니다. 선택 하는 증명 메커니즘에 관계 없이 프로 비전 정보는 IoT Edge 장치의 IoT Edge 구성 파일에 추가 됩니다.

구성 파일을 엽니다.

```bash
sudo nano /etc/iotedge/config.yaml
```

파일의 프로 비전 구성을 찾고 증명 메커니즘에 적합 한 섹션의 주석 처리를 제거 합니다. 다른 프로 비전 섹션이 주석 처리 되었는지 확인 합니다. **프로 비전:** 줄에는 선행 공백이 없어야 하며 중첩 항목은 두 개의 공백으로 들여쓰기 되어야 합니다. **Scope_id** 의 값을 IoT Hub Device Provisioning Service 인스턴스의 값으로 업데이트 하 고 증명 필드에 적절 한 값을 제공 합니다.

TPM 증명:

```yml
# DPS TPM provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "tpm"
    registration_id: "<REGISTRATION_ID>"
```

X.509 증명:

```yml
# DPS X.509 provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "x509"
#   registration_id: "<OPTIONAL REGISTRATION ID. LEAVE COMMENTED OUT TO REGISTER WITH CN OF identity_cert>"
    identity_cert: "<REQUIRED URI TO DEVICE IDENTITY CERTIFICATE>"
    identity_pk: "<REQUIRED URI TO DEVICE IDENTITY PRIVATE KEY>"
```

대칭 키 증명:

```yml
# DPS symmetric key provisioning configuration
provisioning:
  source: "dps"
  global_endpoint: "https://global.azure-devices-provisioning.net"
  scope_id: "<SCOPE_ID>"
  attestation:
    method: "symmetric_key"
    registration_id: "<REGISTRATION_ID>"
    symmetric_key: "<SYMMETRIC_KEY>"
```

클립보드 내용을 Nano에 붙여넣거나 `Shift+Right Click` 키를 누릅니다 `Shift+Insert` .

파일을 저장하고 닫습니다. `CTRL + X`, `Y`, `Enter`

구성 파일에서 프로비전 정보를 입력한 후 디먼을 다시 시작합니다.

```bash
sudo systemctl restart iotedge
```

## <a name="verify-successful-installation"></a>성공적인 설치 확인

이전 섹션의 **수동 구성** 단계를 사용한 경우 IoT Edge 런타임을 디바이스에 성공적으로 프로비전하고 실행해야 합니다. **자동 구성** 단계를 사용한 경우 사용자를 대신하여 런타임에서 디바이스를 IoT 허브에 등록할 수 있도록 몇 가지 추가 단계를 수행해야 합니다. 다음 단계는 [Linux 가상 머신에서 시뮬레이션 된 TPM IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-simulated-device-linux.md#give-iot-edge-access-to-the-tpm)을 참조 하세요.

IoT Edge 디먼의 상태를 확인할 수 있습니다.

```bash
systemctl status iotedge
```

디먼 로그 검사:

```bash
journalctl -u iotedge --no-pager --no-full
```

[문제 해결 도구](troubleshoot.md#run-the-check-command) 를 실행 하 여 가장 일반적인 구성 및 네트워킹 오류를 확인 합니다.

```bash
sudo iotedge check
```

장치에서 IoT Edge 하기 위해 첫 번째 모듈을 배포할 때 까지는 **$edgeHub** 시스템 모듈이 장치에 배포 되지 않습니다. 따라서 자동 검사는 연결 확인에 대 한 오류를 반환 합니다 `Edge Hub can bind to ports on host` . 장치에 모듈을 배포한 후에도이 오류가 발생 하지 않으면이 오류를 무시할 수 있습니다.

마지막으로 실행 중인 모듈을 나열 합니다.

```bash
sudo iotedge list
```

장치에 IoT Edge를 설치한 후 실행 되는 것으로 표시 되는 유일한 모듈은 **edgeAgent**입니다. 첫 번째 배포를 만든 후에는 다른 시스템 모듈 **$edgeHub** 장치 에서도 시작 됩니다. 자세한 내용은 [배포 IoT Edge 모듈](how-to-deploy-modules-portal.md)을 참조 하세요.

## <a name="tips-and-troubleshooting"></a>팁 및 문제 해결

`iotedge` 명령을 실행하려면 상승된 권한이 필요합니다. 런타임을 설치한 후 머신에서 로그아웃했다가 다시 로그인하면 권한이 자동으로 업데이트됩니다. 그 전까지는 `iotedge` 명령 앞에 **sudo**를 사용합니다.

리소스 제한이 있는 디바이스에서는 [문제 해결 가이드](troubleshoot.md)의 지침에 따라 *OptimizeForPerformance* 환경 변수를 *false*로 설정하는 것이 좋습니다.

프록시 서버가 있는 네트워크의 경우 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)의 단계를 수행합니다.

### <a name="verify-your-linux-kernel-for-moby-compatibility"></a>Moby 호환성을 위한 Linux 커널 확인

많은 임베디드 장치 제조업체는 컨테이너 런타임 호환성에 필요한 기능이 없는 사용자 지정 Linux 커널을 포함 하는 장치 이미지를 제공 합니다. 권장 되는 Moby 컨테이너 런타임을 설치 하는 동안 문제가 발생 하는 경우 공식 [Moby GitHub 리포지토리에서](https://github.com/moby/moby) [확인-구성](https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh) 스크립트를 사용 하 여 Linux 커널 구성 문제를 해결할 수 있습니다. 장치에서 다음 명령을 실행 하 여 커널 구성을 확인 합니다.

   ```bash
   curl -sSL https://raw.githubusercontent.com/moby/moby/master/contrib/check-config.sh -o check-config.sh
   chmod +x check-config.sh
   ./check-config.sh
   ```

이 명령은 Moby 런타임에 사용 되는 커널 기능의 상태를 포함 하는 자세한 출력을 제공 합니다. 및 아래의 모든 항목을 사용 하도록 설정 하 `Generally Necessary` 여  `Network Drivers` 커널이 Moby 런타임과 완전히 호환 되는지 확인 해야 합니다.  누락 된 기능을 확인 한 경우 원본에서 커널을 다시 작성 하 고 적절 한 커널 .config에 포함 하기 위해 관련 모듈을 선택 하 여 해당 기능을 사용 하도록 설정 합니다.  마찬가지로 또는와 같은 커널 구성 생성기를 사용 하는 `defconfig` 경우 `menuconfig` 해당 기능을 찾아서 사용 하도록 설정 하 고 그에 따라 커널을 다시 빌드합니다.  새로 수정 된 커널을 배포한 후에는 확인-구성 스크립트를 다시 실행 하 여 필요한 모든 기능이 성공적으로 설정 되었는지 확인 합니다.

## <a name="install-runtime-using-release-assets"></a>릴리스 자산을 사용 하 여 런타임 설치

에서 제공 하지 않는 특정 버전의 Azure IoT Edge 런타임을 설치 하려는 경우이 섹션의 단계를 사용 `apt-get install` 합니다. Microsoft 패키지 목록에는 제한 된 최신 버전과 해당 하위 버전 집합만 포함 되어 있으므로 이러한 단계는 이전 버전 또는 릴리스 후보 버전을 설치 하려는 모든 사용자를 위한 것입니다.

말아 넘기기 명령을 사용 하면 IoT Edge GitHub 리포지토리에서 직접 구성 요소 파일을 대상으로 지정할 수 있습니다. 다음 단계를 사용 하 여 libiothsm 및 IoT Edge 보안 디먼을 설치 합니다. [컨테이너 런타임 설치](#install-a-container-runtime) 섹션의 단계를 사용 하 여 MOBY 엔진과 CLI를 설치 합니다.

1. [Azure IoT Edge 릴리스로](https://github.com/Azure/azure-iotedge/releases)이동 하 고 대상으로 지정할 릴리스 버전을 찾습니다.

2. 해당 버전에 대 한 **자산** 섹션을 확장 합니다.

3. 모든 릴리스에는 IoT Edge 보안 디먼 및 hsmlib에 대 한 새 파일이 있어야 합니다. 다음 명령을 사용 하 여 해당 구성 요소를 업데이트 합니다.

   1. IoT Edge 장치의 아키텍처와 일치 하는 **libiothsm** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭 하 고 링크 주소를 복사 합니다.

   2. 다음 명령의 복사 된 링크를 사용 하 여 해당 버전의 hsmlib를 설치 합니다.

      ```bash
      curl -L <libiothsm-std link> -o libiothsm-std.deb && sudo dpkg -i ./libiothsm-std.deb
      ```

   3. IoT Edge 장치의 아키텍처와 일치 하는 **iotedge** 파일을 찾습니다. 파일 링크를 마우스 오른쪽 단추로 클릭 하 고 링크 주소를 복사 합니다.

   4. 다음 명령의 복사 된 링크를 사용 하 여 해당 버전의 IoT Edge 보안 디먼을 설치 합니다.

      ```bash
      curl -L <iotedge link> -o iotedge.deb && sudo dpkg -i ./iotedge.deb
      ```

에 IoT Edge 성공적으로 설치 되 면 `/etc/iotedge` 출력은 구성 파일을 업데이트 하 라는 메시지를 표시 합니다. [보안 디먼 구성](#configure-the-security-daemon) 섹션의 단계에 따라 장치 프로 비전을 완료 합니다.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

Linux 디바이스에서 IoT Edge 설치를 제거하려면 명령줄에서 다음 명령을 사용합니다.

IoT Edge 런타임을 제거합니다.

```bash
sudo apt-get remove --purge iotedge
```

IoT Edge 런타임이 제거 되 면 만든 컨테이너가 중지 되지만 여전히 장치에 존재 합니다. 모든 컨테이너를 보고 남아 있는 항목을 확인합니다.

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

설치된 런타임을 사용하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge 런타임에 제대로 설치 하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 확인 하세요.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
