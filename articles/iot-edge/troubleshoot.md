---
title: 문제 해결 - Azure IoT Edge | Microsoft Docs
description: 이 문서에서는 구성 요소 상태 및 로그 검색과 같은 Azure IoT Edge의 표준 진단 기술을 알아보고 일반적인 문제를 해결하는 방법을 확인합니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 83595bf045de412954c176028babc4f94fcb21e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60612247"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Azure IoT Edge에 대한 일반적인 문제 및 해결 방법

작업 환경에서 Azure IoT Edge를 실행하는 동안 문제가 발생할 경우 이 문서에서 문제 해결 방법을 참조하세요. 

## <a name="standard-diagnostic-steps"></a>표준 진단 단계 

문제가 발생하는 경우 디바이스 간에 전달되는 메시지 및 컨테이너 로그를 검토하여 IoT Edge 디바이스의 상태를 자세히 알아봅니다. 이 섹션의 명령 및 도구를 사용하여 정보를 수집합니다. 

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>IoT Edge 보안 관리자의 상태 및 해당 로그를 확인합니다.

Linux에서:
- IoT Edge 보안 관리자의 상태를 보려면:

   ```bash
   sudo systemctl status iotedge
   ```

- IoT Edge 보안 관리자의 로그를 보려면:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- IoT Edge 보안 관리자의 더욱 자세한 로그를 보려면:

   - iotedge 디먼 설정을 편집합니다.

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - 다음 줄을 업데이트합니다.
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - IoT Edge 보안 디먼을 다시 시작합니다.
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Windows에서:
- IoT Edge 보안 관리자의 상태를 보려면:

   ```powershell
   Get-Service iotedge
   ```

- IoT Edge 보안 관리자의 로그를 보려면:

   ```powershell
   # Displays logs from today, newest at the bottom.
 
   Get-WinEvent -ea SilentlyContinue `
   -FilterHashtable @{ProviderName= "iotedged";
     LogName = "application"; StartTime = [datetime]::Today} |
   select TimeCreated, Message |
   sort-object @{Expression="TimeCreated";Descending=$false} |
   format-table -autosize -wrap
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>IoT Edge 보안 관리자가 실행되지 않는 경우 yaml 구성 파일을 확인합니다.

> [!WARNING]
> YAML 파일은 들여쓰기로 탭을 포함할 수 없습니다. 2 공백을 대신 사용합니다.

Linux에서:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows에서:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>문제에 대한 컨테이너 로그 확인

IoT Edge 보안 디먼이 실행되면 컨테이너의 로그를 확인하여 문제를 검색합니다. 배포 된 컨테이너를 시작한 다음 IoT Edge 런타임을 구성 하는 컨테이너를 살펴봅니다: edge 에이전트 및 edge 허브입니다. IoT Edge agent 로그는 일반적으로 각 컨테이너의 수명 주기 정보를 제공합니다. IoT Edge 허브 로그에서 메시징 및 라우팅 정보를 제공 합니다. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>IoT Edge hub를 통해 이동 하는 메시지를 보려면

IoT Edge hub를 통해 이동 하는 메시지를 볼 수 있으며 런타임 컨테이너에서 자세한 정보 표시 로그에서 정보를 수집할 수 있습니다. 이러한 컨테이너에서 자세한 로그를 켜려면 yaml 구성 파일에서 `RuntimeLogLevel`을 설정합니다. 파일을 열려면:

Linux에서:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows에서:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

기본적으로 `agent` 요소는 다음 예제와 같습니다.

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

`env: {}`을 다음으로 바꿉니다.

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML 파일은 들여쓰기로 탭을 포함할 수 없습니다. 2 공백을 대신 사용합니다.

파일을 저장하고 IoT Edge 보안 관리자를 다시 시작합니다.

IoT Hub 및 IoT Edge 디바이스 간에 전송되는 메시지를 확인할 수도 있습니다. Visual Studio Code용 [Azure IoT Hub 도구 키트](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) 확장(이전 이름 Azure IoT 도구 키트 확장)을 사용하여 이러한 메시지를 확인합니다. 자세한 내용은 [Azure IoT로 개발할 때 사용할 수 있는 편리한 도구](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/)를 참조하세요.

### <a name="restart-containers"></a>컨테이너 다시 시작
로그 및 메시지에서 정보를 검토한 후에는 컨테이너를 다시 시작할 수 있습니다.

```
iotedge restart <container name>
```

IoT Edge 런타임 컨테이너를 다시 시작합니다.

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>IoT Edge 보안 관리자를 다시 시작합니다.

문제가 여전히 지속되는 경우 IoT Edge 보안 관리자를 다시 시작할 수 있습니다.

Linux에서:

   ```cmd
   sudo systemctl restart iotedge
   ```

Windows에서:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>약 1 분 후에 IoT Edge 에이전트 중지

Edge 에이전트 모듈 시작 되어 약 1 분에 성공적으로 실행 하 고 중지 합니다. 로그는 IoT Edge 에이전트는 AMQP를 통해 IoT Hub에 연결 하려고 하 고 WebSocket을 통한 AMQP를 사용 하 여 연결 하려고 시도 나타냅니다. 실패 하는 IoT Edge 에이전트를 종료 합니다. 

예제 edge 에이전트 로그:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>근본 원인
호스트 네트워크의 네트워킹 구성 때문에 IoT Edge 에이전트 네트워크에 연결 합니다. 에이전트는 AMQP(포트 5671)를 통해 먼저 연결을 시도합니다. 이 연결이 실패하면 WebSockets(포트 443)을 시도합니다.

IoT Edge 런타임은 각 모듈이 통신할 네트워크를 설정합니다. Linux에서 이 네트워크는 브리지 네트워크입니다. Windows에서는 NAT를 사용합니다. 이 문제는 NAT 네트워크를 사용하는 Windows 컨테이너를 사용하는 Windows 디바이스에서 좀 더 자주 발생합니다. 

### <a name="resolution"></a>해결 방법
이 브리지/NAT 네트워크에 할당된 IP 주소가 인터넷에 연결되는 경로가 있는지 확인합니다. 경우에 따라 호스트의 VPN 구성은 IoT Edge 네트워크를 재정의합니다. 

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub가 시작

Edge 허브 모듈 시작과 로그에 다음 메시지가 출력에 실패 합니다. 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>근본 원인
호스트 컴퓨터의 일부 다른 프로세스가 포트 443에 바인딩되어 있습니다. IoT Edge 허브 포트 5671을 매핑하고 443에 대 한 게이트웨이 시나리오에서 사용 합니다. 다른 프로세스가 이미 포트에 바인딩되어 있으면 이 포트 매핑은 실패합니다. 

### <a name="resolution"></a>해결 방법
포트 443을 사용하는 프로세스를 찾아 중지합니다. 이 프로세스는 일반적으로 웹 서버입니다.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge 에이전트 모듈의 이미지 (403)에 액세스할 수 없습니다.
컨테이너를 실행 하려면 되지 못하고 edge 에이전트 로그에 403 오류가 표시 됩니다. 

### <a name="root-cause"></a>근본 원인
Iot Edge 에이전트 모듈의 이미지에 액세스할 수 있는 권한이 없습니다. 

### <a name="resolution"></a>해결 방법
레지스트리 자격 증명이 배포 매니페스트에서 제대로 지정되었는지 확인합니다.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge 보안 디먼이 유효하지 않은 호스트 이름으로 실패합니다.

`sudo journalctl -u iotedge` 명령이 실패하고 다음 메시지를 출력합니다. 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>근본 원인
IoT Edge 런타임은 64자 미만인 호스트 이름만을 지원할 수 있습니다. 물리적 머신은 일반적으로 긴 호스트 이름을 사용하지 않지만 문제는 가상 머신에서 자주 발생합니다. 특히 Azure에서 호스팅되는 Windows 가상 머신에 자동으로 생성된 호스트 이름이 너무 긴 경향이 있습니다. 

### <a name="resolution"></a>해결 방법
이 오류를 표시하는 경우 가상 머신의 DNS 이름을 구성한 다음, 설정 명령에서 DNS 이름을 호스트 이름으로 설정하여 해결할 수 있습니다.

1. Azure Portal에서 가상 머신의 개요 페이지로 이동합니다. 
2. DNS 이름에서 **구성**을 선택합니다. 가상 머신에 DNS 이름이 이미 구성되어 있으면 새 이름을 구성할 필요가 없습니다. 

   ![가상 머신의 DNS 이름 구성](./media/troubleshoot/configure-dns.png)

3. **DNS 이름 레이블**에 대한 값을 입력하고 **저장**을 선택합니다.
4. 새 DNS 이름을 복사합니다.이 형식은 **\<DNSnamelabel\>.\<vmlocation\>.cloudapp.azure.com**이어야 합니다.
5. 가상 머신 내에서 다음 명령을 사용하여 DNS 이름으로 IoT Edge 런타임을 설정합니다.

   - Linux에서:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - Windows에서:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>리소스가 제한된 디바이스의 안정성 문제 
Raspberry Pi와 같이 제한된 디바이스를 사용하는 경우, 특히 이 디바이스를 게이트웨이로 사용하는 경우에는 안정성 문제가 발생할 수 있습니다. 증상에는 에지 허브 모듈의 메모리 부족 예외가 있으며, 다운스트림 디바이스를 연결할 수 없거나 몇 시간 후 디바이스가 원격 분석 메시지를 보내지 않습니다.

### <a name="root-cause"></a>근본 원인
IoT Edge 런타임의 일부는 IoT Edge 허브는 기본적으로 성능에 최적화 된 하 고 많은 양의 메모리를 할당 하려고 합니다. 이 최적화는 제한된 에지 디바이스에 대해 이상적이지 않으며 안정성 문제를 유발할 수 있습니다.

### <a name="resolution"></a>해결 방법
IoT Edge 허브에 대 한 환경 변수를 설정 **OptimizeForPerformance** 하 **false**합니다. 이 작업을 수행하는 방법에는 다음 두 가지가 있습니다.

UI의 경우: 

포털에서로 이동 **장치 세부 정보** > **설정 모듈** > **고급 Edge 런타임 설정 구성**합니다. 호출 하는 Edge 허브 모듈에 대 한 환경 변수를 만듭니다 *OptimizeForPerformance* 로 설정 된 *false*합니다.

![OptimizeForPerformance를 false로 설정](./media/troubleshoot/optimizeforperformance-false.png)

**또는**

배포 매니페스트의 경우:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Windows에서 IoT Edge 디먼 로그를 가져올 수 없습니다.
Windows에서 `Get-WinEvent`를 사용할 때 EventLogException을 가져오면 레지스트리 항목을 확인합니다.

### <a name="root-cause"></a>근본 원인
`Get-WinEvent` PowerShell 명령은 특정 `ProviderName`별로 로그를 찾기 위해 표시된 레지스트리 항목을 사용합니다.

### <a name="resolution"></a>해결 방법
IoT Edge 디먼에 대한 레지스트리 항목을 설정합니다. 다음 콘텐츠를 사용하여 **iotedge.reg** 파일을 만들고, 파일을 두 번 클릭하거나 `reg import iotedge.reg` 명령을 사용하여 Windows 레지스트리로 가져옵니다.

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>404 오류를 나타내며 IoT Edge 모듈이 edgeHub로 메시지를 보내지 못함

404 `Module not found` 오류를 나타내며 사용자 지정 IoT Edge 모듈이 edgeHub로 메시지를 보내지 못합니다. IoT Edge 디먼은 로그에 다음 메시지를 출력합니다. 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

### <a name="root-cause"></a>근본 원인
IoT Edge 디먼은 보안상의 이유로 edgeHub에 연결하는 모든 모듈에 대해 프로세스 확인을 적용합니다. 이 디먼은 모듈이 전송하는 모든 메시지가 해당 모듈의 기본 프로세스 ID에서 온 것인지 확인합니다. 메시지가 처음에 설정한 것과는 다른 프로세스 ID의 모듈에서 전송될 경우 404 오류 메시지가 표시되며 거부됩니다.

### <a name="resolution"></a>해결 방법
사용자 지정 IoT Edge 모듈이 edgeHub에 메시지를 보내는 데 항상 동일한 프로세스 ID를 사용하는지 확인합니다. 예를 들어, 해야 `ENTRYPOINT` of `CMD` 이후 Docker 파일에서 명령을 `CMD` 모듈에 대 한 ID 및 반면 기본 프로그램을 실행 하는 bash 명령에 대 한 다른 프로세스 ID를 하나의 프로세스를 일으킵니다 `ENTRYPOINT` 이어질 수를 단일 프로세스 id입니다.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>IoT Edge 배포에 대한 방화벽 및 포트 구성 규칙
지원 되는 IoT Hub 프로토콜을 사용 하 여 Azure 클라우드로 온-프레미스 서버에서 통신을 허용 하는 azure IoT Edge를 참조 하십시오 [통신 프로토콜 선택](../iot-hub/iot-hub-devguide-protocols.md)합니다. 보안 향상된을 위해 Azure IoT Edge 및 Azure IoT Hub 간의 통신 채널은 항상 아웃바운드되도록 구성됩니다. 이 구성은 [서비스 보조 통신 패턴](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/)을 기반으로 합니다. 그러면 탐색할 악의적인 엔터티에 대한 공격 노출 영역을 최소화합니다. 인바운드 통신은 Azure IoT Hub에서 메시지를 Azure IoT Edge 디바이스에 푸시해야 하는 특정 시나리오에만 필요합니다. 클라우드-디바이스 메시지는 보안 TLS 채널을 사용하여 보호되고 X.509 인증서 및 TPM 디바이스 모듈을 사용하여 추가로 보호될 수 있습니다. Azure IoT Edge 보안 관리자는 이 통신을 설정하는 방법을 관리합니다. [IoT Edge 보안 관리자](../iot-edge/iot-edge-security-manager.md)를 참조하세요.

IoT Edge는 Azure IoT Edge 런타임 및 배포된 모듈을 보호하기 위해 향상된 구성을 제공하지만, 기본 컴퓨터 및 네트워크 구성에 여전히 종속됩니다. 따라서 반드시 적절 한 네트워크를 확인 하 고 통신을 클라우드로 보안 가장자리에 대 한 방화벽 규칙이 설정 합니다. 다음 표에서 때 사용할 수 지침으로 구성 방화벽 규칙 기본 서버에 대 한 Azure IoT Edge 런타임이 호스팅되는:

|프로토콜|포트|수신|발신|지침|
|--|--|--|--|--|
|MQTT|8883|BLOCKED(기본값)|BLOCKED(기본값)|<ul> <li>통신 프로토콜로 MQTT를 사용하는 경우 발신(아웃바운드)이 Open이 되도록 구성합니다.<li>MQTT에 대한 1883은 IoT Edge에서 지원되지 않습니다. <li>수신(인바운드) 연결을 차단해야 합니다.</ul>|
|AMQP|5671|BLOCKED(기본값)|OPEN(기본값)|<ul> <li>IoT Edge의 기본 통신 프로토콜입니다. <li> Azure IoT Edge는 지원되는 다른 프로토콜에 대해 구성되지 않았거나 AMQP가 원하는 통신 프로토콜인 경우 Open으로 구성해야 합니다.<li>AMQP에 대한 5672는 IoT Edge에서 지원되지 않습니다.<li>Azure IoT Edge가 다른 IoT Hub 지원 프로토콜을 사용하는 경우 이 포트를 차단합니다.<li>수신(인바운드) 연결을 차단해야 합니다.</ul></ul>|
|HTTPS|443|BLOCKED(기본값)|OPEN(기본값)|<ul> <li>IoT Edge 프로비전을 위해 443에서 발신(아웃바운드)을 보내는 열기로 구성합니다. 수동 스크립트 또는 Azure IoT DPS(디바이스 프로비저닝 서비스)를 사용하는 경우 이 구성이 필요합니다. <li>다음과 같은 특정 시나리오의 경우에만 수신(인바운드) 연결이 Open 상태여야 합니다. <ul> <li>  메서드 요청을 보낼 수 있는 리프 디바이스에 대한 투명 게이트웨이가 있는 경우. 이 경우 IoTHub에 연결하거나 Azure IoT Edge를 통해 IoTHub 서비스를 제공하기 위해 포트 443을 외부 네트워크로 열어둘 필요가 없습니다. 따라서 수신 규칙은 내부 네트워크에서의 수신(인바운드)만 열도록 제한될 수 있습니다. <li> 클라이언트-디바이스(C2D) 시나리오의 경우</ul><li>HTTP에 대한 80은 IoT Edge에서 지원되지 않습니다.<li>비 HTTP 프로토콜(예: AMQP 또는 MQTT)을 엔터프라이즈에서 구성할 수 없는 경우 WebSockets을 통해 메시지를 보낼 수 있습니다. 이 경우 WebSocket 통신에 포트 443이 사용됩니다.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge 에이전트 모듈 지속적으로 '빈 구성 파일' 보고서 및 모듈 없이 장치에서 시작

장치에 배포에 정의 된 모듈을 시작 하는 데 문제가 있습니다. Edge 에이전트 에서만 실행 중이지만 지속적으로 '빈 구성 파일...'를 보고 합니다.

### <a name="potential-root-cause"></a>잠재적인 근본 원인
기본적으로 IoT Edge 모듈 자체 격리 된 컨테이너 네트워크에서 시작 됩니다. 장치는이 개인 네트워크 내 DNS 이름 확인에 문제가 있을 수 있습니다.

### <a name="resolution"></a>해결 방법

**옵션 1: 엔진 설정을 컨테이너에서 DNS 서버 설정**

엔진에서 시작 하는 모든 컨테이너 모듈에 적용할 컨테이너 엔진 설정에서 사용자 환경에 대 한 DNS 서버를 지정 합니다. 라는 파일을 만들고 `daemon.json` 사용 하려면 DNS 서버를 지정 합니다. 예를 들면 다음과 같습니다.

```
{
    "dns": ["1.1.1.1"]
}
```

위의 예제는 공개적으로 액세스할 수 있는 DNS 서비스에 DNS 서버를 설정합니다. Edge 장치에 액세스할 수 없으면이 IP 해당 환경에서 액세스할 수 있는 DNS 서버 주소를 사용 하 여 대체 합니다.

위치 `daemon.json` 플랫폼에 적합 한 위치에서: 

| 플랫폼 | 위치 |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows 컨테이너를 사용 하 여 Windows 호스트 | `C:\ProgramData\iotedge-moby-data\config` |

위치에 이미 있으면 `daemon.json` 파일을 추가 합니다 **dns** 에 키 파일을 저장 합니다.

*업데이트를 적용 하려면에 대 한 컨테이너 엔진을 다시 시작*

| 플랫폼 | 명령 |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (관리자 Powershell) | `Restart-Service iotedge-moby -Force` |

**옵션 2: 모듈에 대해 IoT Edge 배포에 DNS 서버 설정**

각 모듈에 대 한 DNS 서버를 설정할 수 있습니다 *createOptions* IoT Edge 배포에서 합니다. 예를 들면 다음과 같습니다.

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

에 대해이 설정 해야 합니다 *edge 에이전트* 하 고 *edge 허브* 모듈입니다. 

## <a name="next-steps"></a>다음 단계
IoT Edge 플랫폼에서 버그를 찾았나요? 지속적인 제품 개선을 위해 [문제를 제출](https://github.com/Azure/iotedge/issues)하세요. 

추가 질문이 있으면 [지원 요청](https://portal.azure.com/#create/Microsoft.Support)을 만들어 도움을 받으세요. 

