---
title: Windows에 Azure IoT Edge 설치 | Microsoft Docs
description: Windows 10, Windows Server 및 Windows IoT Core에 대한 Azure IoT Edge 설치 지침
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: kgremban
ms.openlocfilehash: e3f55f9be28a8b53f012e111e43ba1f495b1d585
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77186480"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Windows에 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다.

IoT Edge 런타임에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에는 windows 컨테이너를 사용 하 여 Windows x64 (AMD/Intel) 시스템에 Azure IoT Edge 런타임을 설치 하는 단계가 나와 있습니다.

> [!NOTE]
> 알려진 Windows 운영 체제 문제는 IoT Edge 모듈 (프로세스 격리 Windows Nano Server 컨테이너)이 실행 중일 때 절전 모드 및 최대 절전 모드로 전환 하는 것을 방지 합니다. 이 문제는 장치의 배터리 수명에 영향을 줍니다.
>
> 이 문제를 해결 하려면 명령 `Stop-Service iotedge`를 사용 하 여 이러한 전원 상태를 사용 하기 전에 실행 중인 IoT Edge 모듈을 모두 중지 합니다.

Windows 시스템에서 Linux 컨테이너를 사용하는 것은 Azure IoT Edge에 추천되거나 지원되는 프로덕션 구성이 아닙니다. 그러나 개발 및 테스트 용도로만 사용할 수 있습니다. 자세히 알아보려면 [Windows에서 IoT Edge를 사용 하 여 Linux 컨테이너 실행](how-to-install-iot-edge-windows-with-linux.md)을 참조 하세요.

최신 버전의 IoT Edge에 포함 된 내용에 대 한 자세한 내용은 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조 하세요.

## <a name="prerequisites"></a>사전 요구 사항

이 섹션을 사용하여 Windows 디바이스에서 IoT Edge를 지원할 수 있는지 여부를 검토하고 설치 전에 컨테이너 엔진에 대해 준비합니다.

### <a name="supported-windows-versions"></a>지원되는 Windows 버전

개발 및 테스트 Azure IoT Edge 시나리오의 경우 컨테이너 기능을 지 원하는 모든 버전의 Windows 10 또는 Windows Server 2019 (빌드 17763)에 Windows 컨테이너를 설치할 수 있습니다. 현재 프로덕션 시나리오에 대해 지원 되는 운영 체제에 대 한 자세한 내용은 [지원 되는 Azure IoT Edge 시스템](support.md#operating-systems)을 참조 하세요.

IoT Core 장치는 IoT Edge 런타임을 지원 하기 위한 IoT Core-Windows 컨테이너 선택적 기능을 포함 해야 합니다. [원격 PowerShell 세션](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell) 에서 다음 명령을 사용 하 여 장치에서 Windows 컨테이너가 지원 되는지 확인 합니다.

```powershell
Get-Service vmcompute
```

서비스가 있으면 **실행 중**으로 표시 된 서비스 상태와 함께 성공적인 응답을 얻게 됩니다. Vmcompute 서비스를 찾을 수 없는 경우 장치는 IoT Edge에 대 한 요구 사항을 충족 하지 않습니다. 하드웨어 공급자에 게 문의 하 여이 기능에 대 한 지원을 요청 하십시오.

### <a name="prepare-for-a-container-engine"></a>컨테이너 엔진 준비

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 엔진을 사용합니다. 프로덕션 시나리오에서는 설치 스크립트에 포함된 Moby 엔진을 사용하여 Windows 디바이스에서 Windows 컨테이너를 실행합니다.

## <a name="install-iot-edge-on-a-new-device"></a>새 디바이스에 IoT Edge 설치

>[!NOTE]
>Azure IoT Edge 소프트웨어 패키지에는 패키지(LICENSE 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보시기 바랍니다. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

PowerShell 스크립트가 Azure IoT Edge 보안 디먼을 다운로드하여 설치합니다. 그런 다음, 보안 디먼이 다른 모듈의 원격 배포를 가능하게 하는 두 가지 런타임 모듈 중 첫 번째인 IoT Edge 에이전트를 시작합니다.

>[!TIP]
>IoT Core 장치의 경우 RemotePowerShell 세션을 사용 하 여 설치 명령을 실행 하는 것이 좋습니다. 자세한 내용은 [Windows IoT 용 PowerShell 사용](https://docs.microsoft.com/windows/iot-core/connect-your-device/powershell)을 참조 하세요.

디바이스에 IoT Edge 런타임을 처음 설치하는 경우 IoT 허브의 ID를 사용하여 디바이스를 프로비전해야 합니다. IoT Hub에서 제공 하는 장치 연결 문자열을 사용 하 여 단일 IoT Edge 장치를 수동으로 프로 비전 할 수 있습니다. 또는 장치 프로 비전 서비스 (DPS)를 사용 하 여 장치를 자동으로 프로 비전 할 수 있습니다 .이는 설정할 장치가 많은 경우에 유용 합니다. 프로비전 선택 사항에 따라 적절한 설치 스크립트를 선택합니다.

다음 섹션에서는 새 디바이스의 IoT Edge 설치 스크립트에 대한 일반적인 사용 사례 및 매개 변수를 설명합니다.

### <a name="option-1-install-and-manually-provision"></a>옵션 1: 설치 및 수동으로 프로비전

이 첫 번째 옵션에서는 장치를 프로 비전 하기 위해 IoT Hub에서 생성 한 **장치 연결 문자열** 을 제공 합니다.

이 예제에서는 Windows 컨테이너를 사용 하 여 수동으로 설치 하는 방법을 보여 줍니다.

1. 아직 만들지 않은 경우 새 IoT Edge 장치를 등록 하 고 **장치 연결 문자열**을 검색 합니다. 이 섹션의 뒷부분에서 사용할 연결 문자열을 복사 합니다. 다음 도구를 사용 하 여이 단계를 완료할 수 있습니다.

   * [Azure Portal](how-to-register-device.md#register-in-the-azure-portal)
   * [Azure CLI](how-to-register-device.md#register-with-the-azure-cli)
   * [Visual Studio Code](how-to-register-device.md#register-with-visual-studio-code)

2. PowerShell을 관리자 권한으로 실행합니다.

   >[!NOTE]
   >PowerShell(x86)이 아닌 IoT Edge를 설치하려면 PowerShell의 AMD64 세션을 사용합니다. 사용 중인 세션 형식을 잘 모르는 경우 다음 명령을 실행합니다.
   >
   >```powershell
   >(Get-Process -Id $PID).StartInfo.EnvironmentVariables["PROCESSOR_ARCHITECTURE"]
   >```

3. **배포-IoTEdge** 명령은 Windows 컴퓨터가 지원 되는 버전에 있는지 확인 하 고 컨테이너 기능을 설정한 다음 moby 런타임 및 IoT Edge 런타임을 다운로드 합니다. 이 명령은 기본적으로 Windows 컨테이너를 사용 합니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Deploy-IoTEdge
   ```

4. 이 시점에서 IoT Core 장치가 자동으로 다시 시작 될 수 있습니다. 다른 Windows 10 또는 Windows Server 장치를 다시 시작 하 라는 메시지가 표시 될 수 있습니다. 그렇다면 장치를 지금 다시 시작 하세요. 장치가 준비 되 면 관리자 권한으로 PowerShell을 다시 실행 합니다.

5. **Initialize IoTEdge** 명령은 사용자의 머신에서 IoT Edge 런타임을 구성합니다. 이 명령은 Windows 컨테이너를 통한 수동 프로비저닝으로 기본 설정됩니다.

   ```powershell
   . {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
   Initialize-IoTEdge
   ```

6. 메시지가 표시되면 1단계에서 검색된 장치 연결 문자열을 제공합니다. 장치 연결 문자열은 물리적 장치를 IoT Hub의 장치 ID와 연결합니다.

   장치 연결 문자열은 다음 형식을 사용 하며 따옴표를 포함 하지 않아야 합니다. `HostName={IoT hub name}.azure-devices.net;DeviceId={device name};SharedAccessKey={key}`

7. [설치 성공 확인](#verify-successful-installation) 의 단계를 사용 하 여 장치에서 IoT Edge 상태를 확인 합니다.

디바이스를 수동으로 설치하고 프로비전하는 경우 추가 매개 변수를 사용하여 다음과 같이 설치를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 설치 관리자에서 오프라인 디렉터리를 확인하도록 설정
* 특정 에이전트 컨테이너 이미지를 선언하고, 프라이빗 레지스트리에 있는 경우 자격 증명 제공

이러한 설치 옵션에 대 한 자세한 내용은 [모든 설치 매개 변수에](#all-installation-parameters)대 한 자세한 정보로 건너뜁니다.

### <a name="option-2-install-and-automatically-provision"></a>옵션 2: 설치 및 자동으로 프로비전

이 두 번째 옵션에서는 IoT Hub Device Provisioning Service를 사용하여 디바이스를 프로비전합니다. 기본 [증명 메커니즘과](../iot-dps/concepts-security.md#attestation-mechanism)관련 된 다른 정보와 함께 장치 프로 비전 서비스 인스턴스의 **범위 ID** 를 제공 합니다.

* [Windows에서 가상 TPM을 사용 하 여 시뮬레이트된 IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-simulated-device-windows.md)
* [대칭 키 증명을 사용 하 여 IoT Edge 장치 만들기 및 프로 비전](how-to-auto-provision-symmetric-keys.md)

장치를 자동으로 설치 하 고 프로 비전 할 때 다음을 비롯 한 추가 매개 변수를 사용 하 여 설치를 수정할 수 있습니다.

* 트래픽이 프록시 서버를 통과하도록 설정
* 설치 관리자에서 오프라인 디렉터리를 확인하도록 설정
* 특정 에이전트 컨테이너 이미지를 선언하고, 프라이빗 레지스트리에 있는 경우 자격 증명 제공

이러한 설치 옵션에 대한 자세한 내용을 보려면 이 문서를 계속 읽거나 [모든 설치 매개 변수](#all-installation-parameters)에 대한 자세한 정보로 건너뛰세요.

## <a name="offline-or-specific-version-installation"></a>오프 라인 또는 특정 버전 설치

설치 하는 동안 다음 두 파일이 다운로드 됩니다.

* Microsoft Azure IoT Edge cab는 IoT Edge 보안 디먼 (iotedged), Moby 컨테이너 엔진 및 Moby CLI를 포함 합니다.
* Visual C++ 재배포 가능 패키지 (VC 런타임) MSI

설치 하는 동안 장치가 오프 라인 상태가 되거나 특정 버전의 IoT Edge를 설치 하려는 경우 장치에 이러한 파일 중 하나 또는 둘 다를 미리 다운로드할 수 있습니다. 를 설치할 때 다운로드 한 파일이 포함 된 디렉터리의 설치 스크립트를 가리킵니다. 설치 관리자는 해당 디렉터리를 먼저 확인 한 다음 찾을 수 없는 구성 요소만 다운로드 합니다. 모든 파일을 오프 라인으로 사용할 수 있는 경우 인터넷에 연결 되지 않은를 설치할 수 있습니다.

이전 버전과 함께 최신 IoT Edge 설치 파일은 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조 하세요.

오프 라인 구성 요소를 사용 하 여 설치 하려면 `-OfflineInstallationPath` 매개 변수를 배포-IoTEdge 명령의 일부로 사용 하 고 파일 디렉터리에 대 한 절대 경로를 제공 합니다. 예를 들면 다음과 같습니다.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

>[!NOTE]
>`-OfflineInstallationPath` 매개 변수는 제공 된 디렉터리에서 **Microsoft-Azure-IoTEdge** 이라는 파일을 찾습니다. IoT Edge 버전 1.0.9부터 사용 가능한 두 개의 .cab 파일 (AMD64 장치와 ARM32 용)이 있습니다. 장치에 맞는 파일을 다운로드 한 다음 파일 이름을 변경 하 여 아키텍처 접미사를 제거 합니다.

`Deploy-IoTEdge` 명령은 IoT Edge 구성 요소를 설치 하 고 IoT Hub 장치 ID 및 연결을 사용 하 여 장치를 프로 비전 하려면 `Initialize-IoTEdge` 명령을 계속 진행 해야 합니다. 명령을 직접 실행 하 고 IoT Hub에서 연결 문자열을 제공 하거나, 이전 섹션의 링크 중 하나를 사용 하 여 장치 프로 비전 서비스를 통해 장치를 자동으로 프로 비전 하는 방법을 알아보세요.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; `
Initialize-IoTEdge
```

업데이트-IoTEdge 명령과 함께 오프 라인 설치 경로 매개 변수를 사용할 수도 있습니다.

## <a name="verify-successful-installation"></a>성공적인 설치 확인

IoT Edge 서비스의 상태를 확인합니다. 실행 중으로 표시되어야 합니다.  

```powershell
Get-Service iotedge
```

최근 5분 간의 서비스 로그를 검사합니다. IoT Edge 런타임 설치를 완료 하면 **배포-IoTEdge** 를 실행 하는 시간과 **iotedge**를 실행 하는 동안 발생 한 오류 목록이 표시 될 수 있습니다. 서비스가 구성 되기 전에 시작 되려고 하므로 이러한 오류가 발생 합니다.

```powershell
. {Invoke-WebRequest -useb https://aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
```

가장 일반적인 구성 및 네트워킹 오류에 대 한 자동화 된 검사를 실행 합니다.

```powershell
iotedge check
```

실행 중인 모듈을 나열합니다. 새로 설치한 후에는 실행 중인 것으로 표시 되는 유일한 모듈은 **edgeAgent**입니다. [IoT Edge 모듈](how-to-deploy-modules-portal.md) 을 처음으로 배포한 후에는 다른 시스템 모듈인 **edgeHub**가 장치에서 시작 됩니다.

```powershell
iotedge list
```

## <a name="manage-module-containers"></a>모듈 컨테이너 관리

IoT Edge 서비스에는 장치에서 실행 되는 컨테이너 엔진이 필요 합니다. 장치에 모듈을 배포 하는 경우 IoT Edge 런타임은 컨테이너 엔진을 사용 하 여 클라우드의 레지스트리에서 컨테이너 이미지를 끌어옵니다. IoT Edge 서비스를 사용 하면 모듈과 상호 작용 하 고 로그를 검색할 수 있지만 컨테이너 엔진을 사용 하 여 컨테이너 자체와 상호 작용 하려는 경우가 있습니다.

모듈 개념에 대 한 자세한 내용은 [Azure IoT Edge 모듈 이해](iot-edge-modules.md)를 참조 하세요.

Windows IoT Edge 장치에서 Windows 컨테이너를 실행 하는 경우 IoT Edge 설치에는 Moby 컨테이너 엔진이 포함 됩니다. Moby 엔진은 Docker와 동일한 표준을 기반으로 하며 Docker 데스크톱과 동일한 컴퓨터에서 병렬로 실행 되도록 설계 되었습니다. 따라서 Moby 엔진에서 관리 하는 컨테이너를 대상으로 하려는 경우 Docker 대신 해당 엔진을 대상으로 해야 합니다.

예를 들어 모든 Docker 이미지를 나열 하려면 다음 명령을 사용 합니다.

```powershell
docker images
```

모든 Moby 이미지를 나열 하려면 Moby 엔진에 대 한 포인터를 사용 하 여 동일한 명령을 수정 합니다.

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

엔진 URI는 설치 스크립트의 출력에 나열 되거나, config.xml 파일의 컨테이너 런타임 설정 섹션에서 찾을 수 있습니다.

![구성에서 uri를 moby_runtime 합니다. yaml](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

장치에서 실행 되는 컨테이너 및 이미지와 상호 작용 하는 데 사용할 수 있는 명령에 대 한 자세한 내용은 [Docker 명령줄 인터페이스](https://docs.docker.com/engine/reference/commandline/docker/)를 참조 하세요.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

Windows 디바이스에서 IoT Edge 설치를 제거하려는 경우 관리 PowerShell 창에서 다음 명령을 사용합니다. 이 명령은 기존 구성 및 Moby 엔진 데이터와 함께 IoT Edge 런타임을 제거합니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-IoTEdge
```

제거-IoTEdge 명령은 Windows IoT Core에서 작동 하지 않습니다. Windows IoT Core 장치에서 IoT Edge를 제거 하려면 Windows IoT 핵심 이미지를 다시 배포 해야 합니다.

제거 옵션에 대 한 자세한 내용을 보려면 명령 `Get-Help Uninstall-IoTEdge -full`를 사용 합니다.

## <a name="all-installation-parameters"></a>모든 설치 매개 변수

이전 섹션에서는 매개 변수를 사용하여 설치 스크립트를 수정하는 방법의 예제와 함께 일반적인 설치 시나리오를 소개했습니다. 이 섹션에서는 IoT Edge를 설치, 업데이트 또는 제거 하는 데 사용 되는 일반 매개 변수의 참조 테이블을 제공 합니다.

### <a name="deploy-iotedge"></a>Deploy-IoTEdge

배포-IoTEdge 명령은 IoT Edge 보안 디먼 및 해당 종속성을 다운로드 하 고 배포 합니다. 배포 명령은 이러한 공통 매개 변수를 허용 합니다. 전체 목록은 명령 `Get-Help Deploy-IoTEdge -full`를 사용 합니다.  

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 또는 **Linux** | 컨테이너 운영 체제가 지정 되지 않은 경우 Windows가 기본값입니다.<br><br>Windows 컨테이너의 경우 IoT Edge는 설치에 포함 된 moby 컨테이너 엔진을 사용 합니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **프록시** | 프록시 URL | 디바이스가 프록시 서버를 통해 인터넷에 연결해야 하는 경우 이 매개 변수를 포함합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **OfflineInstallationPath** | 디렉터리 경로 | 이 매개 변수를 포함 하는 경우 설치 관리자는 설치에 필요한 IoT Edge cab 및 VC Runtime MSI 파일에 대해 나열 된 디렉터리를 확인 합니다. 디렉터리에서 찾을 수 없는 파일이 다운로드 됩니다. 두 파일이 모두 디렉터리에 있으면 인터넷에 연결 하지 않고 IoT Edge를 설치할 수 있습니다. 이 매개 변수를 사용 하 여 특정 버전을 사용할 수도 있습니다. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **RestartIfNeeded** | none | 이 플래그를 사용 하면 필요에 따라 메시지를 표시 하지 않고 배포 스크립트에서 컴퓨터를 다시 시작할 수 있습니다. |

### <a name="initialize-iotedge"></a>Initialize-IoTEdge

Initialize IoTEdge 명령은 장치 연결 문자열 및 작업 세부 정보를 사용 하 여 IoT Edge를 구성 합니다. 이 명령에 의해 생성 된 대부분의 정보는 iotedge\config.yaml 파일에 저장 됩니다. 초기화 명령은 이러한 공통 매개 변수를 허용 합니다. 전체 목록은 명령 `Get-Help Initialize-IoTEdge -full`를 사용 합니다.

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **수동** | None | **매개 변수를 전환**합니다. 프로 비전 유형을 지정 하지 않으면 manual이 기본값입니다.<br><br>디바이스 연결 문자열을 제공하여 디바이스를 수동으로 프로비전할 것을 선언합니다. |
| **Dps** | None | **매개 변수를 전환**합니다. 프로 비전 유형을 지정 하지 않으면 manual이 기본값입니다.<br><br>DPS(Device Provisioning Service) 범위 ID 및 디바이스의 등록 ID를 제공하여 DPS를 통해 프로비전할 것을 선언합니다.  |
| **DeviceConnectionString** | IoT Hub에 등록된 IoT Edge 디바이스의 연결 문자열을 작은따옴표 안에 표시합니다. | 수동 설치에 **필요**합니다. 스크립트 매개 변수에 연결 문자열을 제공하지 않을 경우 설치 중에 확인 메시지가 표시됩니다. |
| **ScopeId** | IoT Hub와 연결된 Device Provisioning Service 인스턴스의 범위 ID입니다. | DPS 설치에 **필요**합니다. 스크립트 매개 변수에 범위 ID를 제공하지 않을 경우 설치 중에 확인 메시지가 표시됩니다. |
| **RegistrationId** | 디바이스에서 생성된 등록 ID입니다. | TPM 또는 대칭 키 증명을 사용 하는 경우 DPS 설치에 **필요** 합니다. |
| **SymmetricKey** | DPS를 사용할 때 IoT Edge 장치 id를 프로 비전 하는 데 사용 되는 대칭 키입니다. | 대칭 키 증명을 사용 하는 경우 DPS 설치에 **필요** 합니다. |
| **ContainerOs** | **Windows** 또는 **Linux** | 컨테이너 운영 체제가 지정 되지 않은 경우 Windows가 기본값입니다.<br><br>Windows 컨테이너의 경우 IoT Edge는 설치에 포함 된 moby 컨테이너 엔진을 사용 합니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **AgentImage** | IoT Edge 에이전트 이미지 URI | 기본적으로 새 IoT Edge 설치는 IoT Edge 에이전트 이미지에 대한 최신 롤링 태그를 사용합니다. 이 매개 변수를 사용하여 이미지 버전에 대한 특정 태그를 설정하거나 사용자 고유의 에이전트 이미지를 제공합니다. 자세한 내용은 [IoT Edge 태그 이해](how-to-update-iot-edge.md#understand-iot-edge-tags)를 참조하세요. |
| **사용자 이름** | 컨테이너 레지스트리 사용자 이름입니다. | -AgentImage 매개 변수를 프라이빗 레지스트리의 컨테이너로 설정한 경우에만 이 매개 변수를 사용합니다. 레지스트리에 대한 액세스 권한이 있는 사용자 이름을 제공합니다. |
| **암호** | 보안 암호 문자열입니다. | -AgentImage 매개 변수를 프라이빗 레지스트리의 컨테이너로 설정한 경우에만 이 매개 변수를 사용합니다. 레지스트리에 액세스하기 위한 암호를 제공합니다. |

### <a name="update-iotedge"></a>Update-IoTEdge

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **ContainerOs** | **Windows** 또는 **Linux** | 컨테이너 OS가 지정 되지 않은 경우 기본값은 Windows입니다. Windows 컨테이너의 경우 컨테이너 엔진이 설치에 포함됩니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. |
| **프록시** | 프록시 URL | 디바이스가 프록시 서버를 통해 인터넷에 연결해야 하는 경우 이 매개 변수를 포함합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **OfflineInstallationPath** | 디렉터리 경로 | 이 매개 변수를 포함 하는 경우 설치 관리자는 설치에 필요한 IoT Edge cab 및 VC Runtime MSI 파일에 대해 나열 된 디렉터리를 확인 합니다. 디렉터리에서 찾을 수 없는 파일이 다운로드 됩니다. 두 파일이 모두 디렉터리에 있으면 인터넷에 연결 하지 않고 IoT Edge를 설치할 수 있습니다. 이 매개 변수를 사용 하 여 특정 버전을 사용할 수도 있습니다. |
| **RestartIfNeeded** | none | 이 플래그를 사용 하면 필요에 따라 메시지를 표시 하지 않고 배포 스크립트에서 컴퓨터를 다시 시작할 수 있습니다. |

### <a name="uninstall-iotedge"></a>Uninstall-IoTEdge

| 매개 변수 | 허용되는 값 | 주석 |
| --------- | --------------- | -------- |
| **설정** | none | 이전 제거 시도가 실패 한 경우이 플래그는 제거를 강제로 수행 합니다.
| **RestartIfNeeded** | none | 필요한 경우이 플래그를 사용 하 여 컴퓨터를 다시 시작 하 라는 메시지를 표시 하지 않고 제거할 수 있습니다. |

## <a name="next-steps"></a>다음 단계

런타임을 설치하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge를 제대로 설치하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 확인합니다.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
