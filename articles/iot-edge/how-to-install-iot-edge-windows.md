---
title: Windows에 Azure IoT Edge 설치 | Microsoft Docs
description: Windows 10, Windows Server 및 Windows IoT Core에 대한 Azure IoT Edge 설치 지침
author: kgremban
manager: philmea
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: kgremban
ms.custom: seodec18
ms.openlocfilehash: 95e984f6f08af01a2ffd7b9b4e0ec598d73f4d05
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595116"
---
# <a name="install-the-azure-iot-edge-runtime-on-windows"></a>Windows에 Azure IoT Edge 런타임 설치

Azure IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꿔줍니다. 런타임은 Raspberry Pi처럼 작은 디바이스 또는 산업용 서버처럼 큰 디바이스에 배포할 수 있습니다. IoT Edge 런타임을 사용하여 디바이스를 구성하면 클라우드에서 디바이스에 비즈니스 논리를 배포할 수 있습니다. 

IoT Edge 런타임에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

이 문서에는 Windows x64(AMD/Intel) 시스템에 Azure IoT Edge 런타임을 설치하는 단계가 나와 있습니다. Windows 지원은 현재 미리 보기로 제공되고 있습니다.

> [!NOTE]
> Windows 운영 체제를 눌러도 절전 모드 및 IoT Edge 모듈 (Windows Nano Server 컨테이너 프로세스 격리)을 실행 하는 경우 전원 상태를 최대 절전 모드로 전환할 수 없습니다. 이 문제는 배터리 장치에 영향을 줍니다.
>
> 대 안으로 명령을 사용 하 여 `Stop-Service iotedge` 이러한 전원 상태를 사용 하기 전에 실행 중인 모든 IoT Edge 모듈을 중지 합니다. 

<!--
> [!NOTE]
> Using Linux containers on Windows systems is not a recommended or supported production configuration for Azure IoT Edge. However, it can be used for development and testing purposes.
-->

Linux를 사용 하 여 Windows 시스템에서 컨테이너 아닌 경우 Azure IoT Edge에 대 한 권장 되거나 지원 되는 프로덕션 구성을 그러나 개발 및 테스트 용도로만 사용할 수 있습니다. 

## <a name="prerequisites"></a>필수 조건

이 섹션을 사용하여 Windows 디바이스에서 IoT Edge를 지원할 수 있는지 여부를 검토하고 설치 전에 컨테이너 엔진에 대해 준비합니다. 

### <a name="supported-windows-versions"></a>지원되는 Windows 버전

Azure IoT Edge는 Windows 컨테이너 또는 Linux 컨테이너를 실행 중인지에 따라 다른 버전의 Windows를 지원합니다. 

Windows 컨테이너가 포함된 Azure IoT Edge의 최신 버전은 다음 버전의 Windows에서 실행할 수 있습니다.
* 2018년 10월 업데이트(빌드 17763)가 적용된 Windows 10 또는 IoT Core
* Windows Server 2019(빌드 17763)

Linux 컨테이너가 포함된 Azure IoT Edge의 최신 버전은 다음 버전의 Windows에서 실행할 수 있습니다. 
* Windows 10 1주년 업데이트(빌드 14393) 이상
* Windows Server 2016 이상

현재 지원되는 운영 체제에 대한 자세한 내용은 [Azure IoT Edge 지원](support.md#operating-systems)을 참조하세요. 

최신 버전의 IoT Edge에 포함된 기능에 대한 자세한 내용은 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조하세요.

### <a name="prepare-for-a-container-engine"></a>컨테이너 엔진 준비 

Azure IoT Edge는 [OCI 호환](https://www.opencontainers.org/) 컨테이너 엔진을 사용합니다. 프로덕션 시나리오에서는 설치 스크립트에 포함된 Moby 엔진을 사용하여 Windows 디바이스에서 Windows 컨테이너를 실행합니다. 개발 및 테스트의 경우 Windows 디바이스에서 Linux 컨테이너를 실행할 수 있지만 IoT Edge를 설치하기 전에 컨테이너 엔진을 설치하고 구성해야 합니다. 두 시나리오 모두, 디바이스를 준비하기 위한 필수 조건은 다음 섹션을 참조하세요. 

가상 컴퓨터에서 IoT Edge를 설치 하려는 경우 중첩 된 가상화를 사용 하도록 설정 하 고 최소 2GB 메모리를 할당 합니다. 중첩 된 가상화를 사용 하는 방법에 사용 하 여 하이퍼바이저에 따라 달라 집니다. Hyper-v에 대 한 2 세대 가상 컴퓨터는 중첩 된 가상화 기본적으로 사용 하도록 설정 합니다. VMWare 가상 컴퓨터에 기능을 사용 하도록 설정/해제가 됩니다. 

#### <a name="moby-engine-for-windows-containers"></a>Windows 컨테이너용 Moby 엔진

프로덕션 시나리오에서 IoT Edge를 실행하는 Windows 디바이스의 경우 Moby가 공식적으로 지원되는 유일한 컨테이너 엔진입니다. 설치 스크립트는 IoT Edge를 설치하기 전에 자동으로 디바이스에 Moby 엔진을 설치합니다. 컨테이너 기능을 켜서 디바이스를 준비합니다. 

1. 시작 표시줄에서 **Windows 기능 켜기/끄기**를 검색하고 제어판 프로그램을 엽니다.
2. **컨테이너**를 찾아 선택합니다.
3. **확인**을 선택합니다. 

#### <a name="docker-for-linux-containers"></a>Linux 컨테이너용 Docker

Windows를 사용하여 Linux 디바이스용 컨테이너를 개발 및 테스트하는 경우 [Windows용 Docker](https://www.docker.com/docker-windows)를 컨테이너 엔진으로 사용할 수 있습니다. [Linux 컨테이너를 사용](https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers)하도록 Docker를 구성할 수 있습니다. IoT Edge를 설치하기 전에 Docker를 설치하고 구성해야 합니다. 프로덕션의 Windows 디바이스에서는 Linux 컨테이너가 지원되지 않습니다. 

IoT Edge 디바이스가 Windows 컴퓨터인 경우 Hyper-V에 대한 [시스템 요구 사항](https://docs.microsoft.com/virtualization/hyper-v-on-windows/reference/hyper-v-requirements)을 충족하는지 확인합니다.

## <a name="install-iot-edge-on-a-new-device"></a>새 디바이스에 IoT Edge 설치

>[!NOTE]
>Azure IoT Edge 소프트웨어 패키지에는 패키지(LICENSE 디렉터리)에 있는 사용 조건이 적용됩니다. 패키지를 사용하기 전에 사용 조건을 읽어보시기 바랍니다. 패키지를 설치 및 사용하면 이러한 사용 조건에 동의하게 됩니다. 사용 조건에 동의하지 않는 경우, 패키지를 사용하지 마세요.

PowerShell 스크립트가 Azure IoT Edge 보안 디먼을 다운로드하여 설치합니다. 그런 다음, 보안 디먼이 다른 모듈의 원격 배포를 가능하게 하는 두 가지 런타임 모듈 중 첫 번째인 IoT Edge 에이전트를 시작합니다. 

디바이스에 IoT Edge 런타임을 처음 설치하는 경우 IoT 허브의 ID를 사용하여 디바이스를 프로비전해야 합니다. IoT Hub에서 제공하는 디바이스 연결 문자열을 사용하여 단일 IoT Edge 디바이스를 수동으로 프로비전할 수 있습니다. 또는 Device Provisioning Service를 사용하여 자동으로 디바이스를 프로비전할 수 있습니다. 이 기능은 많은 디바이스를 설정해야 하는 경우에 유용합니다. 프로비전 선택 사항에 따라 적절한 설치 스크립트를 선택합니다. 

다음 섹션에서는 새 디바이스의 IoT Edge 설치 스크립트에 대한 일반적인 사용 사례 및 매개 변수를 설명합니다. 

### <a name="option-1-install-and-manually-provision"></a>옵션 1: 설치 및 수동으로 프로비전

이 첫 번째 옵션에서는 디바이스를 프로비전하기 위해 IoT Hub에서 생성된 디바이스 연결 문자열을 제공합니다. 

[새 Azure IoT Edge 디바이스 등록](how-to-register-device-portal.md)의 단계를 따라 디바이스를 등록하고 디바이스 연결 문자열을 검색합니다. 

다음 예제는 Windows 컨테이너를 사용한 수동 설치를 보여 줍니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -ContainerOs Windows -DeviceConnectionString '<connection-string>'
```

디바이스를 수동으로 설치하고 프로비전하는 경우 추가 매개 변수를 사용하여 다음과 같이 설치를 수정할 수 있습니다.
* 트래픽이 프록시 서버를 통과하도록 설정
* 설치 관리자에서 오프라인 디렉터리를 확인하도록 설정
* 특정 에이전트 컨테이너 이미지를 선언하고, 개인 레지스트리에 있는 경우 자격 증명 제공
* Moby CLI 설치 건너뛰기

이러한 설치 옵션에 대한 자세한 내용을 보려면 이 문서를 계속 읽거나 [모든 설치 매개 변수](#all-installation-parameters)에 대한 자세한 정보로 건너뛰세요.

### <a name="option-2-install-and-automatically-provision"></a>옵션 2: 설치 및 자동으로 프로비전

이 두 번째 옵션에서는 IoT Hub Device Provisioning Service를 사용하여 디바이스를 프로비전합니다. Device Provisioning Service 인스턴스의 **범위 ID** 및 디바이스의 **등록 ID**를 제공합니다.

[Windows에서 시뮬레이션된 TPM Edge 디바이스 만들기 및 프로비전](how-to-auto-provision-simulated-device-windows.md)의 단계를 따라 Device Provisioning Service를 설정하고 해당 **범위 ID**를 검색하고, TPM 디바이스를 시뮬레이션하고 해당 **등록 ID**를 검색한 다음, 개별 등록을 만듭니다. 디바이스가 IoT Hub에 등록되면 설치를 계속합니다.  

   >[!TIP]
   >설치 및 테스트를 수행하는 동안 TPM 시뮬레이터를 실행하는 창을 열린 상태로 유지합니다. 

다음 예제는 Windows 컨테이너를 사용한 자동 설치를 보여 줍니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Dps -ContainerOs Windows -ScopeId <DPS scope ID> -RegistrationId <device registration ID>
```

디바이스를 수동으로 설치하고 프로비전하는 경우 추가 매개 변수를 사용하여 다음과 같이 설치를 수정할 수 있습니다.
* 트래픽이 프록시 서버를 통과하도록 설정
* 설치 관리자에서 오프라인 디렉터리를 확인하도록 설정
* 특정 에이전트 컨테이너 이미지를 선언하고, 개인 레지스트리에 있는 경우 자격 증명 제공
* Moby CLI 설치 건너뛰기

이러한 설치 옵션에 대한 자세한 내용을 보려면 이 문서를 계속 읽거나 [모든 설치 매개 변수](#all-installation-parameters)에 대한 자세한 정보로 건너뛰세요.

## <a name="update-an-existing-installation"></a>기존 설치 업데이트

이전에 디바이스에 IoT Edge 런타임을 설치하고 IoT Hub의 ID로 프로비전한 경우 간소화된 설치 스크립트를 사용할 수 있습니다. `-ExistingConfig` 플래그는 IoT Edge 구성 파일이 디바이스에 이미 있음을 선언합니다. 구성 파일에는 디바이스 ID, 인증서 및 네트워크 설정에 대한 정보가 들어 있습니다. 이 설치 옵션은 디바이스가 원래 수동 또는 자동으로 프로비전되었는지에 관계없이 사용할 수 있습니다. 

자세한 내용은 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.

다음 예제는 기존 구성 파일을 가리키고 Windows 컨테이너를 사용하는 설치를 보여 줍니다. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOs Windows
```

기존 구성 파일에서 IoT Edge를 설치하는 경우 추가 매개 변수를 사용하여 다음과 같이 설치를 수정할 수 있습니다.
* 트래픽이 프록시 서버를 통과하도록 설정
* 설치 관리자에서 오프라인 디렉터리를 확인하도록 설정 
* Moby CLI 설치 건너뛰기 

스크립트 매개 변수를 사용하여 IoT Edge 에이전트 컨테이너 이미지를 선언할 수 없습니다. 해당 정보는 이전 설치의 구성 파일에서 이미 설정되었습니다. 에이전트 컨테이너 이미지를 수정하려는 경우 config.yaml 파일을 사용합니다. 

이러한 설치 옵션에 대한 자세한 내용을 보려면 이 문서를 계속 읽거나 [모든 설치 매개 변수](#all-installation-parameters)에 대한 자세한 정보로 건너뛰세요.

## <a name="offline-installation"></a>오프라인 설치

설치 중에 다음 4개의 파일이 다운로드됩니다. 
* IoT Edge 보안 디먼(iotedgd) zip 
* Moby 엔진 zip
* Moby CLI zip
* Visual C++ 재배포 가능 패키지(VC 런타임) msi

이 파일 중 하나 또는 전체를 미리 디바이스로 다운로드한 다음, 설치 스크립트에서 파일이 포함된 디렉터리를 확인하도록 설정할 수 있습니다. 설치 관리자는 먼저 디렉터리를 확인한 다음, 디렉터리에 없는 구성 요소만 다운로드합니다. 파일 4개를 오프라인에서 모두 사용할 수 있는 경우 인터넷 연결 없이 설치할 수 있습니다. 이 기능을 사용하여 하나 이상 구성 요소의 온라인 버전을 재정의할 수도 있습니다.  

이전 버전 및 최신 설치 파일은 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조하세요.

오프라인 구성 요소를 사용하여 설치하려면 `-OfflineInstallationPath` 매개 변수를 사용하고 파일 디렉터리의 절대 경로를 제공합니다. 예를 들면 다음과 같습니다.

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -Manual -DeviceConnectionString '<connection-string>' -OfflineInstallationPath C:\Downloads\iotedgeoffline
```

## <a name="all-installation-parameters"></a>모든 설치 매개 변수

이전 섹션에서는 매개 변수를 사용하여 설치 스크립트를 수정하는 방법의 예제와 함께 일반적인 설치 시나리오를 소개했습니다. 이 섹션에서는 IoT Edge를 설치하는 데 사용할 수 있는 유효한 매개 변수의 참조 테이블을 제공합니다. 자세한 내용을 보려면 PowerShell 창에서 `get-help Install-SecurityDaemon -full`을 실행합니다. 

기존 구성으로 IoT Edge를 설치하려면 설치 명령에서 다음과 같은 일반 매개 변수를 사용할 수 있습니다. 

| 매개 변수 | 허용되는 값 | 설명 |
| --------- | --------------- | -------- |
| **수동** | 없음 | **매개 변수를 전환**합니다. 모든 설치는 manual, DPS 또는 existingconfig로 선언되어야 합니다.<br><br>디바이스 연결 문자열을 제공하여 디바이스를 수동으로 프로비전할 것을 선언합니다. |
| **Dps** | 없음 | **매개 변수를 전환**합니다. 모든 설치는 manual, DPS 또는 existingconfig로 선언되어야 합니다.<br><br>DPS(Device Provisioning Service) 범위 ID 및 디바이스의 등록 ID를 제공하여 DPS를 통해 프로비전할 것을 선언합니다.  |
| **ExistingConfig** | 없음 | **매개 변수를 전환**합니다. 모든 설치는 manual, DPS 또는 existingconfig로 선언되어야 합니다.<br><br>config.yaml 파일이 해당 프로비저닝 정보와 함께 디바이스에 이미 있음을 선언합니다. |
| **DeviceConnectionString** | IoT Hub에 등록된 IoT Edge 디바이스의 연결 문자열을 작은따옴표 안에 표시합니다. | 수동 설치에 **필요**합니다. 스크립트 매개 변수에 연결 문자열을 제공하지 않을 경우 설치 중에 확인 메시지가 표시됩니다. |
| **ScopeId** | IoT Hub와 연결된 Device Provisioning Service 인스턴스의 범위 ID입니다. | DPS 설치에 **필요**합니다. 스크립트 매개 변수에 범위 ID를 제공하지 않을 경우 설치 중에 확인 메시지가 표시됩니다. |
| **RegistrationId** | 디바이스에서 생성된 등록 ID입니다. | DPS 설치에 **필요**합니다. 스크립트 매개 변수에 등록 ID를 제공하지 않을 경우 설치 중에 확인 메시지가 표시됩니다. |
| **ContainerOs** | **Windows** 또는 **Linux** | 컨테이너 OS를 지정하지 않을 경우 Linux가 기본값입니다. Windows 컨테이너의 경우 컨테이너 엔진이 설치에 포함됩니다. Linux 컨테이너의 경우 설치를 시작하기 전에 컨테이너 엔진을 설치해야 합니다. Windows에서 Linux 컨테이너를 실행하는 것은 유용한 개발 시나리오이지만 프로덕션에서는 지원되지 않습니다. |
| **프록시** | 프록시 URL | 디바이스가 프록시 서버를 통해 인터넷에 연결해야 하는 경우 이 매개 변수를 포함합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **InvokeWebRequestParameters** | 매개 변수 및 값의 해시 테이블입니다. | 설치 중에 여러 개의 웹 요청이 생성됩니다. 이 필드를 사용하여 해당 웹 요청에 대한 매개 변수를 설정합니다. 이 매개 변수는 프록시 서버에 대한 자격 증명을 구성하는 데 유용합니다. 자세한 내용은 [프록시 서버를 통해 통신하도록 IoT Edge 디바이스 구성](how-to-configure-proxy-support.md)을 참조하세요. |
| **OfflineInstallationPath** | 디렉터리 경로 | 이 매개 변수가 포함된 경우 설치 관리자는 디렉터리에서 설치에 필요한 iotedged zip, Moby 엔진 zip, Moby CLI zip 및 VC 런타임 MSI 파일을 확인합니다. 파일 4개가 이 디렉터리에 모두 있는 경우 오프라인 상태에서 IoT Edge를 설치할 수 있습니다. 이 매개 변수를 사용하여 특정 구성 요소의 온라인 버전을 재정의할 수도 있습니다. |
| **AgentImage** | IoT Edge 에이전트 이미지 URI | 기본적으로 새 IoT Edge 설치는 IoT Edge 에이전트 이미지에 대한 최신 롤링 태그를 사용합니다. 이 매개 변수를 사용하여 이미지 버전에 대한 특정 태그를 설정하거나 사용자 고유의 에이전트 이미지를 제공합니다. 자세한 내용은 [IoT Edge 태그 이해](how-to-update-iot-edge.md#understand-iot-edge-tags)를 참조하세요. |
| **사용자 이름** | 컨테이너 레지스트리 사용자 이름입니다. | -AgentImage 매개 변수를 개인 레지스트리의 컨테이너로 설정한 경우에만 이 매개 변수를 사용합니다. 레지스트리에 대한 액세스 권한이 있는 사용자 이름을 제공합니다. |
| **암호** | 보안 암호 문자열입니다. | -AgentImage 매개 변수를 개인 레지스트리의 컨테이너로 설정한 경우에만 이 매개 변수를 사용합니다. 레지스트리에 액세스하기 위한 암호를 제공합니다. | 
| **SkipMobyCli** | 없음 | -ContainerOS가 Windows로 설정된 경우에만 적용할 수 있습니다. $MobyInstallDirectory에 Moby CLI(docker.exe)를 설치하지 마세요. |

## <a name="verify-successful-installation"></a>성공적인 설치 확인

다음과 같은 방법으로 IoT Edge 서비스의 상태를 확인할 수 있습니다. 

```powershell
Get-Service iotedge
```

다음을 사용하여 최근 5분간의 서비스 로그를 검사합니다.

```powershell

# Displays logs from last 5 min, newest at the bottom.

Get-WinEvent -ea SilentlyContinue `
  -FilterHashtable @{ProviderName= "iotedged";
    LogName = "application"; StartTime = [datetime]::Now.AddMinutes(-5)} |
  select TimeCreated, Message |
  sort-object @{Expression="TimeCreated";Descending=$false} |
  format-table -autosize -wrap
```

다음을 사용하여 실행 중인 모듈을 나열합니다.

```powershell
iotedge list
```

새로 설치를 실행 하는 것을 표시만 모듈 뒤 **edge 에이전트**합니다. 한 후 [IoT Edge 모듈 배포](how-to-deploy-modules-portal.md), 다른 사용자가 표시 됩니다. 

## <a name="manage-module-containers"></a>모듈 컨테이너 관리

IoT Edge 서비스는 장치에서 실행 하는 컨테이너 엔진에 필요 합니다. 장치에 모듈을 배포할 때 IoT Edge 런타임에서 컨테이너 엔진을 사용 하 여 클라우드에서 레지스트리에서 컨테이너 이미지를 끌어옵니다. IoT Edge 서비스 모듈을 사용 하 여 상호 작용 하 고 로그를 검색할 수 있습니다 하지만 컨테이너 엔진을 사용 하 여 컨테이너 자체와 상호 작용 하도록 하려는 경우가 있습니다. 

모듈 개념에 대 한 자세한 내용은 참조 하세요. [이해 Azure IoT Edge 모듈](iot-edge-modules.md)합니다. 

Windows IoT Edge 장치에서 Windows 컨테이너를 실행 하는 경우 IoT Edge 설치 모 비 컨테이너 엔진을 포함 합니다. Windows 개발 컴퓨터에 Linux 컨테이너를 개발 하는 경우 Docker 데스크톱 사용할 수 있습니다. 모 비 Docker와 같은 표준을 기반 엔진과 동일한 Docker 데스크톱 컴퓨터에서 병렬로 실행 하도록 설계 되었습니다. 이런 이유로 모 비 엔진에 의해 관리 되는 대상 컨테이너 하려는 경우 특히 Docker 대신 해당 엔진을 대상으로 합니다. 

예를 들어, 모든 Docker 이미지를 나열 하려면 다음 명령을 사용 합니다.

```powershell
docker images
```

모든 모 비 이미지를 나열 하려면 모 비 엔진에 대 한 포인터를 사용 하 여 동일한 명령을 수정 합니다. 

```powershell
docker -H npipe:////./pipe/iotedge_moby_engine images
```

URI 엔진 설치 스크립트의 출력에 나열 된 또는 config.yaml 파일에 대 한 컨테이너 런타임 설정 섹션에서 찾을 수 있습니다. 

![config.yaml moby_runtime uri](./media/how-to-install-iot-edge-windows/moby-runtime-uri.png)

컨테이너 및 장치에서 실행 되는 이미지와 상호 작용 하 여 명령에 대 한 자세한 내용은 참조 [Docker 명령줄 인터페이스](https://docs.docker.com/engine/reference/commandline/docker/)합니다.

## <a name="uninstall-iot-edge"></a>IoT Edge 제거

Windows 디바이스에서 IoT Edge 설치를 제거하려는 경우 관리 PowerShell 창에서 다음 명령을 사용합니다. 이 명령은 기존 구성 및 Moby 엔진 데이터와 함께 IoT Edge 런타임을 제거합니다. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon -DeleteConfig -DeleteMobyDataRoot
```

디바이스에 IoT Edge를 다시 설치하려는 경우 나중에 기존 구성 정보로 보안 디먼을 다시 설치할 수 있도록 `-DeleteConfig` 및 `-DeleteMobyDataRoot` 매개 변수를 그대로 둡니다. 

## <a name="next-steps"></a>다음 단계

런타임을 설치하여 IoT Edge 디바이스를 프로비전했으므로 [IoT Edge 모듈을 배포](how-to-deploy-modules-portal.md)할 수 있습니다.

IoT Edge를 제대로 설치하는 데 문제가 있는 경우 [문제 해결](troubleshoot.md) 페이지를 확인합니다.

기존 설치를 최신 버전의 IoT Edge로 업데이트하려면 [IoT Edge 보안 디먼 및 런타임 업데이트](how-to-update-iot-edge.md)를 참조하세요.
