---
title: 디바이스에서 IoT Edge 버전 업데이트 - Azure IoT Edge | Microsoft Docs
description: 최신 버전의 보안 디먼 및 IoT Edge 런타임을 실행하도록 IoT Edge 디바이스를 업데이트하는 방법
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: a3dd7f78362b5f5c99dc4a74fe0a32c4d26be5b7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125936"
---
# <a name="update-the-iot-edge-security-daemon-and-runtime"></a>IoT Edge 보안 디먼 및 런타임 업데이트

IoT Edge 서비스 릴리스는 새 버전으로 최신 기능 및 향상 된 보안 기능에 대 한 IoT Edge 장치를 업데이트 하는 것이 좋습니다. 이 문서에서는 새 버전을 사용할 수 있을 때 IoT Edge 디바이스를 업데이트하는 방법에 대한 정보를 제공합니다. 

새 버전으로 전환하려면 IoT Edge 디바이스의 두 가지 구성 요소를 업데이트해야 합니다. 첫 번째는 디바이스에서 실행되고 디바이스가 시작될 때 런타임 모듈을 시작하는 보안 디먼입니다. 현재, 보안 디먼은 디바이스 자체에서만 업데이트할 수 있습니다. 두 번째 구성 요소는 런타임으로, IoT Edge 허브 및 IoT Edge 에이전트 모듈로 구성됩니다. 배포 구성 방법에 따라 디바이스에서 또는 원격으로 런타임을 업데이트할 수 있습니다. 

최신 버전의 Azure IoT Edge를 찾으려면 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 참조하세요.

>[!IMPORTANT]
>Windows 디바이스에서 Azure IoT Edge를 실행할 때 다음 중 하나가 디바이스에 적용되는 경우에는 버전 1.0.5로 업데이트하지 마세요. 
>* 디바이스를 Windows 빌드 17763으로 업그레이드하지 않았습니다. IoT Edge 버전 1.0.5는 17763보다 오래된 Windows 빌드를 지원하지 않습니다.
>* Windows 디바이스에서 Java 또는 Node.js 모듈을 실행합니다. Windows 디바이스를 최신 빌드로 업데이트한 경우에도 버전 1.0.5를 건너뜁니다. 
>
>IoT Edge 버전 1.0.5에 대한 자세한 내용은 [1.0.5 릴리스 정보](https://github.com/Azure/azure-iotedge/releases/tag/1.0.5)를 참조하세요. 개발 도구를 최신 버전으로 업데이트 하지 못하도록 하는 방법에 대 한 자세한 내용은 참조 하세요. [IoT 개발자 블로그](https://devblogs.microsoft.com/iotdev/)합니다.


## <a name="update-the-security-daemon"></a>보안 디먼 업데이트

IoT Edge 보안 디먼은 IoT Edge 디바이스에서 패키지 관리자를 사용하여 업데이트해야 하는 네이티브 구성 요소입니다. 

`iotedge version` 명령을 사용하여 디바이스에서 실행 중인 보안 디먼의 버전을 확인합니다. 

### <a name="linux-devices"></a>Linux 디바이스

Linux 디바이스에서는 apt-get 또는 해당 패키지 관리자를 사용하여 보안 디먼을 업데이트합니다. 

```bash
apt-get update
apt-get install libiothsm iotedge
```

### <a name="windows-devices"></a>Windows 디바이스

Windows 디바이스에서는 PowerShell 스크립트를 사용하여 보안 디먼을 제거했다가 다시 설치합니다. 설치 스크립트는 최신 버전의 보안 디먼을 자동으로 끌어옵니다. 

관리자 PowerShell 세션에서 보안 디먼을 제거합니다. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Uninstall-SecurityDaemon
```

실행 된 `Uninstall-SecurityDaemon` 명령 매개 변수 없이 두 개의 런타임 컨테이너 이미지와 함께 장치에서 보안 디먼 제거 합니다. config.yaml 파일뿐 아니라 Moby 컨테이너 엔진의 데이터도 디바이스에서 유지됩니다. 연결 문자열 또는 다시 설치 과정에서 장치에 대 한 Device Provisioning Service 정보를 제공 하지 않아도 구성 정보 즉 유지 합니다. 

IoT Edge 디바이스가 Windows 컨테이너를 사용하는지 또는 Linux 컨테이너를 사용하는지에 따라 보안 디먼을 다시 설치합니다. 구 바꿉니다 **\<Windows 또는 Linux\>** 적절 한 컨테이너 운영 체제입니다. **-ExistingConfig** 플래그를 사용하여 디바이스의 기존 config.yaml 파일을 가리킵니다. 

```powershell
. {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; `
Install-SecurityDaemon -ExistingConfig -ContainerOS <Windows or Linux>
```

특정 버전의 보안 디먼을 설치하려면 [IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)에서 적절한 iotedged-windows.zip 파일을 다운로드합니다. 그런 다음, `-OfflineInstallationPath` 매개 변수를 사용하여 파일 위치를 가리킵니다. 자세한 내용은 [오프라인 설치](how-to-install-iot-edge-windows.md#offline-installation)를 참조하세요.

## <a name="update-the-runtime-containers"></a>런타임 컨테이너 업데이트

IoT Edge 에이전트 및 IoT Edge 허브 컨테이너를 업데이트하는 방법은 배포에서 롤링 태그(예: 1.0)를 사용할지 또는 특정 태그(예: 1.0.2)를 사용할지에 따라 다릅니다. 

`iotedge logs edgeAgent` 또는 `iotedge logs edgeHub` 명령을 사용하여 현재 사용 중인 디바이스에 있는 IoT Edge 에이전트 및 IoT Edge 허브 모듈 버전을 확인합니다. 

  ![로그의 컨테이너 버전 찾기](./media/how-to-update-iot-edge/container-version.png)

### <a name="understand-iot-edge-tags"></a>IoT Edge 태그 이해

IoT Edge 에이전트 및 IoT Edge 허브 이미지에는 연결된 IoT Edge 버전으로 태그가 지정됩니다. 런타임 이미지에 태그를 사용하는 방법에는 다음 두 가지가 있습니다. 

* **롤링 태그** - 버전 번호의 처음 두 개 값만 사용하여 해당 숫자와 일치하는 최신 이미지를 가져옵니다. 예를 들어, 최신 1.0.x 버전을 가리키는 새 릴리스가 있을 때마다 1.0이 업데이트됩니다. IoT Edge 디바이스의 컨테이너 런타임이 이미지를 다시 끌어오면 런타임 모듈은 최신 버전으로 업데이트됩니다. 이 접근 방법은 개발 목적으로 제안됩니다. Azure Portal에서 배포할 때는 기본적으로 롤링 태그가 사용됩니다. 
* **특정 태그** - 버전 번호의 세 값을 모두 사용하여 이미지 버전을 명시적으로 설정합니다. 예를 들어, 1.0.2는 초기 릴리스 이후에 변경되지 않습니다. 업데이트할 준비가 되면 배포 매니페스트에서 새 버전 번호를 선언할 수 있습니다. 이 접근 방법은 프로덕션 목적으로 제안됩니다.

### <a name="update-a-rolling-tag-image"></a>롤링 태그 이미지 업데이트

배포에 롤링 태그를 사용하는 경우(예: mcr.microsoft.com/azureiotedge-hub:  **1.0**) 디바이스의 컨테이너 런타임이 강제로 최신 버전의 이미지를 가져오도록 해야 합니다. 

IoT Edge 디바이스에서 이미지의 로컬 버전을 삭제합니다. Windows 머신에서 보안 디먼을 제거하면 런타임 이미지도 제거되므로 이 단계를 다시 수행할 필요가 없습니다. 

```bash
docker rmi mcr.microsoft.com/azureiotedge-hub:1.0
docker rmi mcr.microsoft.com/azureiotedge-agent:1.0
```

이미지를 제거하기 위해 강제 `-f` 플래그를 사용해야 할 수 있습니다. 

IoT Edge 서비스는 최신 버전의 런타임 이미지를 끌어오고 해당 이미지를 디바이스에서 자동으로 다시 시작합니다. 

### <a name="update-a-specific-tag-image"></a>특정 태그 이미지 업데이트

배포에서 특정 태그를 사용하는 경우(예: mcr.microsoft.com/azureiotedge-hub:**1.0.2**) 배포 매니페스트에서 태그를 업데이트하고 변경 내용을 디바이스에 적용하기만 하면 됩니다. 

Azure Portal에서 런타임 배포 이미지는 **고급 에지 런타임 설정 구성** 섹션에서 선언됩니다. 

![고급 edge 런타임 설정 구성](./media/how-to-update-iot-edge/configure-runtime.png)

JSON 배포 매니페스트에서 **systemModules** 섹션의 모듈 이미지를 업데이트합니다. 

```json
"systemModules": {
  "edgeAgent": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-agent:1.0.2",
      "createOptions": ""
    }
  },
  "edgeHub": {
    "type": "docker",
    "status": "running",
    "restartPolicy": "always",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0.2",
      "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}], \"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
    }
  }
},
```

## <a name="next-steps"></a>다음 단계

최신 [Azure IoT Edge 릴리스](https://github.com/Azure/azure-iotedge/releases)를 확인합니다.

[IoT(사물 인터넷) 블로그](https://azure.microsoft.com/blog/topics/internet-of-things/)에서 최신 업데이트 및 알림을 받아 보세요. 