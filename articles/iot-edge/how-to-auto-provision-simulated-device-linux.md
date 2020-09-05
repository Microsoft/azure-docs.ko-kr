---
title: Linux VM에서 가상 TPM을 사용 하 여 장치 프로 비전-Azure IoT Edge
description: Linux VM에서 시뮬레이션된 TPM을 사용하여 Azure IoT Edge에 대한 Azure Device Provisioning Service 테스트
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 6/30/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 3bc9344459802f4bb4268093d905a051525d78dc
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684459"
---
# <a name="create-and-provision-an-iot-edge-device-with-a-tpm-on-linux"></a>Linux에서 TPM을 사용 하 여 IoT Edge 장치 만들기 및 프로 비전

이 문서에서는 TPM (신뢰할 수 있는 플랫폼 모듈)을 사용 하 여 Linux IoT Edge 장치에서 자동 프로 비전을 테스트 하는 방법을 보여 줍니다. [장치 프로 비전 서비스](../iot-dps/index.yml)를 사용 하 여 Azure IoT Edge 장치를 자동으로 프로 비전 할 수 있습니다. 자동 프로비전 프로세스에 익숙하지 않은 경우 계속하기 전에 [자동 프로비전 개념](../iot-dps/concepts-auto-provisioning.md)을 검토하세요.

작업은 다음과 같습니다.

1. 하드웨어 보안을 위해 시뮬레이션된 TPM(신뢰할 수 있는 플랫폼 모듈)을 사용하여 Hyper-v에서 Linux 가상 머신(VM)을 만듭니다.
1. IoT Hub DPS(Device Provisioning Service)의 인스턴스를 만듭니다.
1. 디바이스에 대한 개별 등록을 만듭니다.
1. IoT Edge 런타임을 설치하고 IoT Hub에 디바이스를 연결합니다.

> [!TIP]
> 이 문서에서는 TPM 시뮬레이터를 사용 하 여 DPS 프로 비전을 테스트 하는 방법을 설명 하지만 대부분의 [BROADCOMM OPTIGA &trade; TPM](https://catalog.azureiotsolutions.com/details?title=OPTIGA-TPM-SLB-9670-Iridium-Board), IoT용 Azure Certified 장치와 같은 실제 TPM 하드웨어에 적용 됩니다.
>
> 물리적 장치를 사용 하는 경우이 문서의 [물리적 장치에서 프로 비전 정보 검색](#retrieve-provisioning-information-from-a-physical-device) 섹션으로 건너뛸 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Hyper-V를 사용하도록 설정된](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/enable-hyper-v) Windows 개발 컴퓨터. 이 문서에서는 Ubuntu Server VM을 실행하는 Windows 10을 사용합니다.
* 활성 IoT Hub

> [!NOTE]
> Tpm 2.0는 DPS에서 TPM 증명을 사용 하는 경우 필요 하며, 개별 그룹, 등록 그룹을 만드는 데만 사용할 수 있습니다.

## <a name="create-a-linux-virtual-machine-with-a-virtual-tpm"></a>가상 TPM을 사용하여 Linux 가상 머신 만들기

이 섹션에서는 Hyper-v에서 새 Linux 가상 머신을 만듭니다. 자동 프로비저닝이 IoT Edge와 작동 하는 방식을 테스트 하기 위해 시뮬레이트된 TPM으로이 가상 머신을 구성 합니다.

### <a name="create-a-virtual-switch"></a>가상 스위치 만들기

가상 스위치를 사용하면 가상 머신을 실제 네트워크에 연결할 수 있습니다.

1. Windows 컴퓨터에서 Hyper-v 관리자를 엽니다.

2. **작업** 메뉴에서 **가상 스위치 관리자**를 선택합니다.

3. **외부** 가상 스위치를 선택한 다음, **가상 스위치 만들기**를 선택합니다.

4. 새 가상 스위치에 이름을 지정합니다(예를 들어 **EdgeSwitch**). 연결 형식이 **외부 네트워크**에 설정되어 있는지 확인한 다음, **확인**을 선택합니다.

5. 팝업은 네트워크 연결이 중단될 수 있다는 경고 메시지를 표시합니다. 계속하려면 **예**를 선택합니다.

새 가상 스위치를 만드는 동안 오류가 표시되는 경우 다른 스위치가 이더넷 어댑터를 사용하고 있지 않은지 그리고 동일한 이름을 사용하지 않는지 확인합니다.

### <a name="create-virtual-machine"></a>가상 머신 만들기

1. 가상 머신에 사용하기 위해 디스크 이미지 파일을 다운로드하고 로컬로 저장합니다. 예를 들면 [Ubuntu server 18.04](http://releases.ubuntu.com/18.04/)입니다. IoT Edge 장치에 대해 지원 되는 운영 체제에 대 한 자세한 내용은 [지원 되는 시스템 Azure IoT Edge](support.md)을 참조 하세요.

2. Hyper-v 관리자의 **Action**  >  **New**  >  **작업** 메뉴에서 작업 새**가상 컴퓨터** 를 다시 선택 합니다.

3. 다음 특정 구성으로 **새 Virtual Machine 마법사**를 완료합니다.

   1. **세대 지정**: **2세대**를 선택합니다. 2 세대 가상 컴퓨터에는 가상 컴퓨터에서 IoT Edge를 실행 하는 데 필요한 중첩 된 가상화가 사용 하도록 설정 되어 있습니다.
   2. **네트워킹 구성**: 이전 섹션에서 만든 가상 스위치에 **연결** 값을 설정합니다.
   3. **설치 옵션**: **부팅 가능한 이미지 파일에서 운영 체제 설치**를 선택하고, 로컬에 저장한 디스크 이미지 파일을 찾습니다.

4. 마법사에서 **마침** 을 선택 하 여 가상 컴퓨터를 만듭니다.

새 VM을 만드는 데 몇 분이 걸릴 수 있습니다.

### <a name="enable-virtual-tpm"></a>가상 TPM 사용

VM을 만든 후에는 해당 설정을 열어 장치를 자동으로 프로 비전 할 수 있는 TPM (가상 신뢰할 수 있는 플랫폼 모듈)을 사용 하도록 설정 합니다.

1. Hyper-v 관리자에서 VM을 마우스 오른쪽 단추로 클릭 하 고 **설정**을 선택 합니다.

2. **보안**으로 이동합니다.

3. **보안 부팅 사용**을 선택 취소합니다.

4. **신뢰할 수 있는 플랫폼 모듈 사용**을 확인합니다.

5. **확인**을 클릭합니다.  

### <a name="start-the-virtual-machine-and-collect-tpm-data"></a>가상 머신 시작 및 TPM 데이터 수집

가상 머신에서 장치의 **등록 ID** 및 **인증 키**를 검색 하는 데 사용할 수 있는 도구를 빌드합니다.

1. Hyper-v 관리자에서 VM을 시작 하 고 연결 합니다.

1. 가상 컴퓨터 내의 메시지에 따라 설치 프로세스를 완료 하 고 컴퓨터를 다시 부팅 합니다.

1. VM에 로그인 한 다음 [Linux 개발 환경 설정](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) 의 단계에 따라 C 용 Azure IOT 장치 SDK를 설치 하 고 빌드합니다.

   >[!TIP]
   >이 문서에서는 Hyper-v 관리자 연결 응용 프로그램을 통해 쉽게 수행할 수 없는 가상 머신에서 복사 하 여 붙여 넣습니다. Hyper-v 관리자를 통해 가상 컴퓨터에 연결 하 여 해당 IP 주소를 검색 하는 것이 좋습니다. 먼저 `sudo apt install net-tools` 를 실행 한 다음를 실행 `hostname -I` 합니다. 그런 다음 IP 주소를 사용 하 여 SSH를 통해 연결할 수 있습니다 `ssh <username>@<ipaddress>` .

1. 다음 명령을 실행 하 여 TPM에서 장치 프로 비전 정보를 검색 하는 SDK 도구를 빌드합니다.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. 출력 창에 장치의 **등록 ID** 및 **인증 키**가 표시 됩니다. 나중에 장치에 대 한 개별 등록을 만들 때 사용 하기 위해 이러한 값을 복사 합니다.

등록 ID 및 인증 키가 있으면 [IoT Hub Device Provisioning Service 설정](#set-up-the-iot-hub-device-provisioning-service) 섹션으로 이동 합니다.

## <a name="retrieve-provisioning-information-from-a-physical-device"></a>물리적 장치에서 프로 비전 정보 검색

VM 대신 물리적 IoT Edge 장치를 사용 하는 경우 장치의 프로 비전 정보를 검색 하는 데 사용할 수 있는 도구를 빌드합니다.

1. [Linux 개발 환경 설정](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md#linux) 의 단계에 따라 C 용 Azure IOT 장치 SDK를 설치 하 고 빌드합니다.

1. 다음 명령을 실행 하 여 TPM 장치에서 장치 프로 비전 정보를 검색 하는 SDK 도구를 빌드합니다.

   ```bash
   cd azure-iot-sdk-c/cmake
   cmake -Duse_prov_client:BOOL=ON ..
   cd provisioning_client/tools/tpm_device_provision
   make
   sudo ./tpm_device_provision
   ```

1. **등록 ID** 및 **인증 키**에 대 한 값을 복사 합니다. 이러한 값을 사용하여 DPS에서 디바이스에 대한 개별 등록을 만듭니다.

## <a name="set-up-the-iot-hub-device-provisioning-service"></a>IoT Hub Device Provisioning Service 설정

Azure에서 IoT Hub Device Provisioning Service의 새 인스턴스를 만들어 IoT hub에 연결합니다. [IoT Hub DPS 설정](../iot-dps/quick-setup-auto-provision.md) 지침을 따르면 됩니다.

Device Provisioning Service를 실행한 후 개요 페이지에서 **ID 범위** 값을 복사합니다. IoT Edge 런타임을 구성하는 경우 이 값을 사용합니다.

## <a name="create-a-dps-enrollment"></a>DPS 등록 만들기

가상 머신에서 프로비전 정보를 검색하고 이를 사용하여 Device Provisioning Service에서 개별 등록을 만듭니다.

DPS에서 등록을 만들 때 **초기 디바이스 쌍 상태**를 선언할 기회가 있습니다. 디바이스 쌍에서 지역, 환경, 위치 또는 디바이스 유형 같은 솔루션에 필요한 모든 메트릭으로 디바이스 그룹에 태그를 설정할 수 있습니다. 이러한 태그는 [자동 배포](how-to-deploy-at-scale.md)를 만드는 데 사용됩니다.

> [!TIP]
> Azure CLI에서 [등록](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/dps/enrollment) 을 만들고 **가장자리 사용** 플래그를 사용 하 여 장치가 IoT Edge 장치 임을 지정할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub Device Provisioning Service 인스턴스로 이동 합니다.

2. **설정**에서 **등록 관리**를 선택합니다.

3. 등록을 구성하려면 **개별 등록 추가**를 선택한 다음, 다음 단계를 완료합니다.  

   1. **메커니즘**의 경우 **TPM**을 선택합니다.

   2. 가상 머신에서 복사한 **인증 키** 및 **등록 ID** 를 제공 합니다.

      > [!TIP]
      > 물리적 TPM 장치를 사용 하는 경우 각 TPM 칩에 고유한 **인증 키**를 확인 해야 하며,이는 연결 된 tpm 칩 제조업체에서 가져옵니다. 예를 들어 인증 키의 SHA-256 해시를 만들어 TPM 장치에 대 한 고유한 **등록 ID** 를 파생할 수 있습니다.

   3. 원하는 경우 디바이스에 대한 ID를 제공합니다. 장치 ID를 제공 하지 않으면 등록 ID가 사용 됩니다.

   4. 이 가상 머신이 IoT Edge 장치 임을 선언 하려면 **True** 를 선택 합니다.

   5. 장치를 연결할 연결 된 IoT Hub를 선택 하거나 **새 IoT Hub에 연결**을 선택 합니다. 여러 허브를 선택할 수 있으며, 선택한 할당 정책에 따라 장치 중 하나에 장치가 할당 됩니다.

   6. 원하는 경우 **초기 디바이스 쌍 상태**에 태그 값을 추가합니다. 태그를 사용하여 모듈 배포에 대한 디바이스 그룹을 대상으로 할 수 있습니다. 자세한 내용은 [대규모로 IoT Edge 모듈 배포](how-to-deploy-at-scale.md)를 참조 하세요.

   7. **저장**을 선택합니다.

이제이 장치에 대 한 등록이 있으므로 IoT Edge 런타임은 설치 중에 장치를 자동으로 프로 비전 할 수 있습니다.

## <a name="install-the-iot-edge-runtime"></a>IoT Edge 런타임 설치

IoT Edge 런타임은 모든 IoT Edge 디바이스에 배포되며, 해당 구성 요소는 컨테이너에서 실행되며, Edge에서 코드를 실행할 수 있도록 디바이스에 추가 컨테이너의 배포를 허용합니다. IoT Edge 런타임을 가상 머신에 설치합니다.

디바이스 유형과 일치하는 문서를 시작하기 전에 DPS **ID 범위** 및 디바이스 **등록 ID**를 알아야 합니다. 예제 Ubuntu 서버를 설치했으면 **x64** 지침을 사용합니다. 수동이 아닌 자동 프로비전에 대한 IoT Edge 런타임을 구성해야 합니다.

보안 데몬을 구성 하는 단계에 대 한 자세한 내용은 다음을 확인 하 고 [옵션 2 자동 프로 비전](how-to-install-iot-edge-linux.md#option-2-automatic-provisioning) 및 TPM 증명 구성을 선택 합니다.

[Linux에 Azure IoT Edge 런타임 설치](how-to-install-iot-edge-linux.md)

## <a name="give-iot-edge-access-to-the-tpm"></a>TPM에 대한 IoT Edge 액세스 권한 부여

IoT Edge 런타임은 장치를 자동으로 프로 비전 하도록 TPM에 액세스 해야 합니다.

서비스에 루트 권한이 있도록 systemd 설정을 재정의 하 여 IoT Edge 런타임에 대 한 TPM 액세스를 제공할 수 있습니다 `iotedge` . 서비스 권한을 승격시키지 않으려는 경우 다음 단계를 사용하여 수동으로 TPM 액세스 권한을 제공할 수도 있습니다.

1. 디바이스에서 TPM 하드웨어 모듈에 대한 파일 경로를 찾아 로컬 변수로 저장합니다.

   ```bash
   tpm=$(sudo find /sys -name dev -print | fgrep tpm | sed 's/.\{4\}$//')
   ```

2. tpm0에 IoT Edge 런타임 액세스 권한을 부여할 새 규칙을 만듭니다.

   ```bash
   sudo touch /etc/udev/rules.d/tpmaccess.rules
   ```

3. 규칙 파일을 엽니다.

   ```bash
   sudo nano /etc/udev/rules.d/tpmaccess.rules
   ```

4. 규칙 파일에 다음과 같은 액세스 정보를 복사합니다.

   ```input
   # allow iotedge access to tpm0
   KERNEL=="tpm0", SUBSYSTEM=="tpm", OWNER="iotedge", MODE="0600"
   ```

5. 파일을 저장하고 종료합니다.

6. 새 규칙을 평가하려면 udev 시스템을 트리거합니다.

   ```bash
   /bin/udevadm trigger $tpm
   ```

7. 규칙이 성공적으로 적용됐는지 확인합니다.

   ```bash
   ls -l /dev/tpm0
   ```

   성공적인 출력은 다음과 같이 표시 됩니다.

   ```output
   crw-rw---- 1 root iotedge 10, 224 Jul 20 16:27 /dev/tpm0
   ```

   올바른 사용 권한이 적용됐는지 확인할 수 없는 경우 머신을 재부팅하여 udev를 새로 고칩니다.

## <a name="restart-the-iot-edge-runtime"></a>IoT Edge 런타임 다시 시작

디바이스에서 한 모든 구성 변경을 선택하도록 IoT Edge 런타임을 다시 시작합니다.

   ```bash
   sudo systemctl restart iotedge
   ```

IoT Edge 런타임이 실행되고 있는지 확인합니다.

   ```bash
   sudo systemctl status iotedge
   ```

프로비전 오류가 표시되면 구성 변경 내용이 아직 적용되지 않았을 수 있습니다. IoT Edge 디먼을 다시 시작합니다.

   ```bash
   sudo systemctl daemon-reload
   ```

또는 가상 머신을 다시 시작하여 변경 내용이 새로 시작 시 적용되는지 확인합니다.

## <a name="verify-successful-installation"></a>성공적인 설치 확인

런타임이 성공적으로 시작한 경우 IoT Hub로 이동하여 새 디바이스를 자동으로 프로비전했는지 확인할 수 있습니다. 이제 디바이스가 IoT Edge 모듈을 실행할 준비가 되었습니다.

IoT Edge 디먼의 상태를 확인합니다.

```cmd/sh
systemctl status iotedge
```

디먼 로그를 검사합니다.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

실행 중인 모듈을 나열합니다.

```cmd/sh
iotedge list
```

장치 프로 비전 서비스에서 만든 개별 등록이 사용 되었는지 확인할 수 있습니다. Azure Portal에서 장치 프로 비전 서비스 인스턴스로 이동 합니다. 만든 개별 등록에 대 한 등록 정보를 엽니다. 등록 상태가 **할당** 되 고 장치 ID가 나열 됩니다.

## <a name="next-steps"></a>다음 단계

DPS 등록 프로세스를 통해 새 장치를 프로 비전 할 때 장치 ID 및 장치 쌍 태그를 동시에 설정할 수 있습니다. 자동 디바이스 관리를 사용하여 개별 디바이스 또는 디바이스 그룹을 대상으로 하려면 이러한 값을 사용할 수 있습니다. Azure Portal 또는 [Azure CLI](how-to-deploy-cli-at-scale.md)를 사용 [하 여 대규모로 IoT Edge 모듈을 배포 하 고 모니터링](how-to-deploy-at-scale.md) 하는 방법을 알아봅니다.
