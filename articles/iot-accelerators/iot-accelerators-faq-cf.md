---
title: 연결된 팩터리 솔루션 FAQ - Azure | Microsoft Docs
description: 연결된 팩터리 솔루션 가속기에 대한 질문과 대답
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: ed429d923cad2c715621990c146d4cf3a23e7bca
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61447933"
---
# <a name="frequently-asked-questions-for-connected-factory-solution-accelerator"></a>연결된 팩터리 솔루션 가속기에 대한 질문과 대답

IoT 솔루션 가속기에 대한 일반 [FAQ](iot-accelerators-faq.md)도 참조하세요.

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerator"></a>어디에서 솔루션 가속기에 대한 소스 코드를 찾을 수 있나요?

소스 코드는 다음 GitHub 리포지토리에 저장됩니다.

* [연결된 팩터리 솔루션 가속기](https://github.com/Azure/azure-iot-connected-factory)

### <a name="what-is-opc-ua"></a>OPC UA란?

2008년에 발표된 OPC UA(통합 아키텍처)는 플랫폼 독립적이며 서비스 지향 상호 운용성 표준입니다. OPC UA는 산업 PC, PLC 및 센서와 같은 다양한 산업 시스템 및 디바이스에서 사용됩니다. OPC UA는 OPC 클래식 사양의 기능을 보안이 기본적으로 제공되는 확장 가능한 단일 프레임워크에 통합합니다. 이 표준은 OPC Foundation에서 운영합니다. [OPC Foundation](https://opcfoundation.org/)은 440명 이상의 구성원이 있는 비영리 조직입니다. 조직의 목표는 OPC 사양을 사용하여 다음을 통해 다중 공급업체, 다중 플랫폼, 안전하고 신뢰할 수 있는 상호 운용성을 지원하는 것입니다.

* 인프라
* 사양
* 기술
* 프로세스

### <a name="why-did-microsoft-choose-opc-ua-for-the-connected-factory-solution-accelerator"></a>Microsoft가 연결된 팩터리 솔루션 가속기로 OPC UA를 선택한 이유

Microsoft는 플랫폼 독립적이고 업계에서 통용되며 입증된 오픈 비독점 표준인 OPC UA를 선택했습니다. 광범위한 제조 프로세스 및 장비 간의 상호 운용성을 보장하는 Industrie 4.0(RAMI4.0) 참조 아키텍처 솔루션을 위해 이 제품이 필요합니다. Microsoft는 Industrie 4.0 솔루션을 빌드하려는 고객의 요청을 확인했습니다. OPC UA에 대한 지원은 고객의 목표 달성을 가로막는 장애물을 해소하는 데 도움을 주고 즉각적인 비즈니스 가치를 제공합니다.

### <a name="how-do-i-add-a-public-ip-address-to-the-simulation-vm"></a>공용 IP 주소를 시뮬레이션 VM에 어떻게 추가하나요?

IP 주소를 추가하는 두 가지 옵션이 있습니다.

* [리포지토리](https://github.com/Azure/azure-iot-connected-factory)에서 PowerShell 스크립트 `Simulation/Factory/Add-SimulationPublicIp.ps1`을 사용합니다. 배포 이름을 매개 변수로 전달합니다. 로컬 배포의 경우 `<your username>ConnFactoryLocal`을 사용합니다. 스크립트는 VM의 IP 주소를 출력합니다.

* Azure Portal에서 배포의 리소스 그룹을 찾습니다. 로컬 배포의 경우를 제외하고 리소스 그룹에는 솔루션 또는 배포 이름으로 지정한 이름이 있습니다. 빌드 스크립트를 사용하는 로컬 배포의 경우 리소스 그룹의 이름은 `<your username>ConnFactoryLocal`입니다. 이제 리소스 그룹에 새 **공용 IP 주소** 리소스를 추가합니다.

> [!NOTE]
> 두 경우에서 [Ubuntu 웹 사이트](https://wiki.ubuntu.com/Security/Upgrades)의 지침을 따라 최신 패치를 설치했는지 확인합니다. 공용 IP 주소를 통해 VM에 액세스할 수 있는 한 설치를 최신 상태로 유지합니다.

### <a name="how-do-i-remove-the-public-ip-address-to-the-simulation-vm"></a>시뮬레이션 VM에 대한 공용 IP 주소를 어떻게 제거하나요?

IP 주소를 제거하는 두 가지 옵션이 있습니다.

* [리포지토리](https://github.com/Azure/azure-iot-connected-factory)의 PowerShell 스크립트 Simulation/Factory/Remove-SimulationPublicIp.ps1을 사용합니다. 배포 이름을 매개 변수로 전달합니다. 로컬 배포의 경우 `<your username>ConnFactoryLocal`을 사용합니다. 스크립트는 VM의 IP 주소를 출력합니다.

* Azure Portal에서 배포의 리소스 그룹을 찾습니다. 로컬 배포의 경우를 제외하고 리소스 그룹에는 솔루션 또는 배포 이름으로 지정한 이름이 있습니다. 빌드 스크립트를 사용하는 로컬 배포의 경우 리소스 그룹의 이름은 `<your username>ConnFactoryLocal`입니다. 이제 리소스 그룹에서 **공용 IP 주소** 리소스를 제거합니다.

### <a name="how-do-i-sign-in-to-the-simulation-vm"></a>시뮬레이션 VM에 어떻게 로그인하나요?

[리포지토리](https://github.com/Azure/azure-iot-connected-factory)에서 PowerShell 스크립트 `build.ps1`을 사용하여 솔루션을 배포한 경우에만 시뮬레이션 VM에 대한 로그인이 지원됩니다.

www.azureiotsolutions.com에서 솔루션을 배포한 경우 VM에 로그인할 수 없습니다. 암호는 임의로 생성되고 다시 설정할 수 없으므로 로그인할 수 없습니다.

1. VM에 공용 IP 주소를 추가합니다. [공용 IP 주소를 시뮬레이션 VM에 어떻게 추가하나요?](#how-do-i-remove-the-public-ip-address-to-the-simulation-vm)를 참조하세요.
1. VM의 IP 주소를 사용하여 VM에 대한 SSH 세션을 만듭니다.
1. 사용할 사용자 이름은 `docker`입니다.
1. 사용할 암호는 배포하는 데 사용한 버전에 따라 다릅니다.
    * 2017년 6월 1일 이전에 build.ps1 스크립트를 사용하여 배포된 솔루션의 경우 암호는 `Passw0rd`입니다.
    * 2017년 6월 1일 이후에 build.ps1 스크립트를 사용하여 배포된 솔루션의 경우 `<name of your deployment>.config.user` 파일에서 암호를 찾을 수 있습니다. 암호는 **VmAdminPassword** 설정에 저장됩니다. `build.ps1` 스크립트 매개 변수 `-VmAdminPassword`를 사용하여 암호를 지정하지 않은 한 암호는 배포 시 임의로 생성됩니다.

### <a name="how-do-i-stop-and-start-all-docker-processes-in-the-simulation-vm"></a>시뮬레이션 VM에서 모든 docker 프로세스를 어떻게 중지하고 시작하나요?

1. 시뮬레이션 VM에 로그인합니다. [시뮬레이션 VM에 어떻게 로그인하나요?](#how-do-i-sign-in-to-the-simulation-vm)를 참조하세요.
1. 활성화되어 있는 컨테이너를 확인하려면 `docker ps`를 실행합니다.
1. 모든 시뮬레이션 컨테이너를 중지하려면 `./stopsimulation`을 실행합니다.
1. 모든 시뮬레이션 컨테이너를 시작하려면:
    * **IOTHUB_CONNECTIONSTRING** 이름으로 셸 변수를 내보냅니다. `<name of your deployment>.config.user` 파일에서 **IotHubOwnerConnectionString** 설정의 값을 사용합니다. 예를 들면 다음과 같습니다.

        ```sh
        export IOTHUB_CONNECTIONSTRING="HostName={yourdeployment}.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey={your key}"
        ```

    * `./startsimulation`을 실행합니다.

### <a name="how-do-i-update-the-simulation-in-the-vm"></a>VM에서 시뮬레이션을 어떻게 업데이트하나요?

시뮬레이션에 변경 사항을 만든 경우 `updatedimulation` 명령을 사용하여 [리포지토리](https://github.com/Azure/azure-iot-connected-factory)에서 PowerShell 스크립트 `build.ps1`을 사용할 수 있습니다. 이 스크립트는 모든 시뮬레이션 구성 요소를 빌드하고, VM에서 시뮬레이션을 중지하고, 모든 시뮬레이션 구성 요소를 업로드, 설치 및 시작합니다.

### <a name="how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution"></a>내 솔루션에서 사용하는 IoT 허브의 연결 문자열을 어떻게 찾나요?

[리포지토리](https://github.com/Azure/azure-iot-connected-factory)에서 `build.ps1` 스크립트를 사용하여 솔루션을 배포한 경우 연결 문자열은 `<name of your deployment>.config.user` 파일에서 **IotHubOwnerConnectionString**의 값입니다.

Azure Portal을 사용하여 연결 문자열을 찾을 수도 있습니다. 배포의 리소스 그룹의 IoT Hub 리소스에서 연결 문자열 설정을 찾습니다.

### <a name="which-iot-hub-devices-does-the-connected-factory-simulation-use"></a>연결된 팩터리 시뮬레이션에서 어떤 IoT Hub 장치를 사용하나요?

시뮬레이션은 다음 디바이스를 자체 등록합니다.

* proxy.beijing.corp.contoso
* proxy.capetown.corp.contoso
* proxy.mumbai.corp.contoso
* proxy.munich0.corp.contoso
* proxy.rio.corp.contoso
* proxy.seattle.corp.contoso
* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

[DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) 또는 [Azure CLI용 IoT 확장](https://github.com/Azure/azure-iot-cli-extension) 도구를 사용하여 솔루션이 사용 중인 IoT Hub에 등록된 디바이스를 확인할 수 있습니다. 디바이스 탐색기를 사용하려면 배포에서 IoT Hub에 대한 연결 문자열이 필요합니다. Azure CLI용 IoT 확장을 사용하려면 IoT Hub 이름이 필요합니다.

### <a name="how-can-i-get-log-data-from-the-simulation-components"></a>시뮬레이션 구성 요소에서 로그 데이터는 어떻게 얻을 수 있나요?

시뮬레이션에서 모든 구성 요소는 로그 파일에 정보를 기록합니다. 이러한 파일은 `home/docker/Logs` 폴더의 VM에서 찾을 수 있습니다. 로그를 검색하기 위해 [리포지토리](https://github.com/Azure/azure-iot-connected-factory)에서 PowerShell 스크립트 `Simulation/Factory/Get-SimulationLogs.ps1`을 사용할 수 있습니다.

이 스크립트는 VM에 로그인해야 합니다. 로그인에 대한 자격 증명을 제공해야 합니다. 자격 증명을 찾으려면 [시뮬레이션 VM에 어떻게 로그인하나요?](#how-do-i-sign-in-to-the-simulation-vm)를 참조하세요.

공용 IP 주소가 아직 없거나 제거하는 경우 스크립트는 VM에 대한 공용 IP 주소를 추가/제거합니다. 스크립트는 보관 파일에 모든 로그 파일을 저장하고 개발 워크스테이션으로 보관 파일을 다운로드합니다.

또는 SSH를 통해 VM에 로그인하고 런타임 시 로그 파일을 검사합니다.

### <a name="how-can-i-check-if-the-simulation-is-sending-data-to-the-cloud"></a>시뮬레이션이 데이터를 클라우드로 보내는지 어떻게 확인할 수 있나요?

[DeviceExplorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer) 또는 [Azure IoT CLI Extension monitor-events](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/hub?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-hub-monitor-events) 명령을 사용하여 특정 디바이스에서 IoT Hub로 전송된 데이터를 검사할 수 있습니다. 이러한 도구를 사용하려면 배포에서 IoT 허브에 대한 연결 문자열을 알아야 합니다. [내 솔루션에서 사용하는 IoT 허브의 연결 문자열을 어떻게 찾나요?](#how-do-i-find-out-the-connection-string-of-the-iot-hub-used-by-my-solution)를 참조하세요.

게시자 디바이스 중 하나를 통해 보낸 데이터를 검사합니다.

* publisher.beijing.corp.contoso
* publisher.capetown.corp.contoso
* publisher.mumbai.corp.contoso
* publisher.munich0.corp.contoso
* publisher.rio.corp.contoso
* publisher.seattle.corp.contoso

IoT Hub로 전송된 데이터를 볼 수 없는 경우 시뮬레이션에 문제가 있습니다. 첫 번째 분석 단계로 시뮬레이션 구성 요소의 로그 파일을 분석해야 합니다. [시뮬레이션 구성 요소에서 로그 데이터는 어떻게 얻을 수 있나요?](#how-can-i-get-log-data-from-the-simulation-components)를 참조하세요. 다음으로 시뮬레이션 중지 및 시작을 시도하고, 전송된 데이터가 여전히 없는 경우 시뮬레이션을 완전히 업데이트합니다. [VM에서 시뮬레이션을 어떻게 업데이트하나요?](#how-do-i-update-the-simulation-in-the-vm)를 참조하세요.

### <a name="how-do-i-enable-an-interactive-map-in-my-connected-factory-solution"></a>연결된 팩터리 솔루션에서 대화형 맵을 사용하려면 어떻게 할까요?

연결된 팩터리 솔루션에서 대화형 맵을 사용하려면 Azure Maps 계정이 있어야 합니다.

[www.azureiotsolutions.com](https://www.azureiotsolutions.com)에서 배포하는 경우, 배포 프로세스는 Azure Maps 계정을 솔루션 가속기 서비스를 포함하는 리소스 그룹에 추가합니다.

연결된 팩터리 GitHub 리포지토리의 `build.ps1` 스크립트를 사용하여 배포하는 경우 빌드 창의 변수 `$env:MapApiQueryKey`를[ Azure Maps 계정의 키](../azure-maps/how-to-manage-account-keys.md)로 설정합니다. 그러면 대화형 지도가 자동으로 사용되도록 설정됩니다.

또한 배포 후 솔루션 가속기에 Azure Maps 계정 키를 추가할 수도 있습니다. Azure Portal로 이동한 후 연결된 팩터리 배포의 App Service 리소스에 액세스합니다. **응용 프로그램 설정** 섹션을 찾을 수 있는 **응용 프로그램 설정**으로 이동합니다. **MapApiQueryKey**를 [Azure Maps 계정의 키](../azure-maps/how-to-manage-account-keys.md)로 설정합니다. 설정을 저장한 다음 **개요**로 이동하고 App Service를 다시 시작합니다.

### <a name="how-do-i-create-an-azure-maps-account"></a>Azure Maps 계정 만들기

[Azure Maps 계정과 키를 관리하는 방법](../azure-maps/how-to-manage-account-keys.md)을 참조하세요.

### <a name="how-to-obtain-your-azure-maps-account-key"></a>Azure Maps 계정 키를 가져오는 방법

[Azure Maps 계정과 키를 관리하는 방법](../azure-maps/how-to-manage-account-keys.md)을 참조하세요.

### <a name="how-do-enable-the-interactive-map-while-debugging-locally"></a>로컬로 디버그하는 동안 대화형 맵을 사용하려면 어떻게 하나요?

로컬로 디버그하는 동안 대화형 맵을 사용하려면 배포 루트의 `local.user.config` 및 `<yourdeploymentname>.user.config` 파일에서 `MapApiQueryKey` 설정 값을 이전에 복사한 **QueryKey** 값으로 설정합니다.

### <a name="how-do-i-use-a-different-image-at-the-home-page-of-my-dashboard"></a>내 대시보드의 홈페이지에서 다른 이미지를 사용하려면 어떻게 할까요?

대시보드의 홈페이지에서 표시되는 고정 이미지를 변경하려면 `WebApp\Content\img\world.jpg` 이미지를 바꿉니다. 그러면 WebApp을 다시 빌드하고 다시 배포합니다.

### <a name="how-do-i-use-non-opc-ua-devices-with-connected-factory"></a>연결된 팩터리에서 비 OPC UA 장치를 사용하려면 어떻게 할까요?

비 OPC UA 디바이스에서 연결된 팩터리에 원격 분석 데이터를 보내려면:

1. `ContosoTopologyDescription.json` 파일에서 [연결된 팩터리 토폴로지의 새 스테이션을 구성](iot-accelerators-connected-factory-configure.md)합니다.

1. 연결된 팩터리 호환 가능 JSON 형식으로 원격 분석 데이터를 수집합니다.

    ```json
    [
      {
        "ApplicationUri": "<the_value_of_OpcUri_of_your_station",
        "DisplayName": "<name_of_the_datapoint>",
        "NodeId": "value_of_NodeId_of_your_datapoint_in_the_station",
        "Value": {
          "Value": <datapoint_value>,
          "SourceTimestamp": "<timestamp>"
        }
      }
    ]
    ```

1. `<timestamp>`의 형식은 `2017-12-08T19:24:51.886753Z`입니다.

1. 연결된 팩터리 App Service를 다시 시작합니다.

### <a name="next-steps"></a>다음 단계

IoT 솔루션 가속기의 몇 가지 다른 기능을 탐색할 수도 있습니다.

* [예측 유지 관리 솔루션 가속기 개요](iot-accelerators-predictive-overview.md)
* [연결된 팩터리 솔루션 가속기 배포](quickstart-connected-factory-deploy.md)
* [처음부터 IoT 보안을 고려](/azure/iot-fundamentals/iot-security-ground-up)
