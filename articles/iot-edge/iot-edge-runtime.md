---
title: 런타임에서 디바이스를 관리하는 방법 알아보기 - Azure IoT Edge | Microsoft Docs
description: IoT Edge 런타임이 장치에서 모듈, 보안, 통신 및 보고를 관리 하는 방법을 알아봅니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/01/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: amqp, mqtt, devx-track-csharp
ms.openlocfilehash: 25493312854bbd495dce01f8f107b3e3320cb92c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016957"
---
# <a name="understand-the-azure-iot-edge-runtime-and-its-architecture"></a>Azure IoT Edge 런타임 및 해당 아키텍처 이해

IoT Edge 런타임은 디바이스를 IoT Edge 디바이스로 바꾸는 프로그램 컬렉션입니다. IoT Edge 런타임 구성 요소를 통해 IoT Edge 장치가 Edge에서 실행 되는 코드를 수신 하 고 결과를 전달할 수 있습니다.

IoT Edge 런타임은 IoT Edge 장치에서 다음 기능을 담당 합니다.

* 디바이스에 워크로드를 설치하고 업데이트합니다.
* 디바이스에서 Azure IoT Edge 보안 표준을 유지합니다.
* [IoT Edge 모듈이](iot-edge-modules.md) 항상 실행 되 고 있는지 확인 합니다.
* 원격 모니터링을 위해 모듈 상태를 클라우드에 보고합니다.
* 다운스트림 장치와 IoT Edge 장치 간의 통신을 관리 합니다.
* IoT Edge 장치의 모듈 간 통신을 관리 합니다.
* IoT Edge 장치와 클라우드 간의 통신을 관리 합니다.

![런타임은 인사이트 및 모듈 상태를 IoT Hub에 전달합니다.](./media/iot-edge-runtime/Pipeline.png)

IoT Edge 런타임의 책임은 모듈 관리와 통신이라는 두 가지 범주로 나뉩니다. 이러한 두 역할은 IoT Edge 런타임의 일부인 두 구성 요소에 의해 수행 됩니다.*IoT Edge 허브* 는 통신을 담당 하며, *IoT Edge 에이전트* 는 모듈을 배포 하 고 모니터링 합니다.

IoT Edge 허브와 IoT Edge 에이전트는 모두 IoT Edge 디바이스에서 실행되는 다른 모듈과 마찬가지로 모듈입니다. 때로는 *런타임 모듈이*라고도 합니다.

## <a name="iot-edge-hub"></a>IoT Edge 허브

IoT Edge 허브는 Azure IoT Edge 런타임을 구성하는 두 가지 모듈 중 하나입니다. IoT Hub와 동일한 프로토콜 엔드포인트를 노출하여 IoT Hub에 대한 로컬 프록시 역할을 합니다. 이 일관성은 클라이언트(디바이스 또는 모듈)에서 IoT Hub와 마찬가지로 IoT Edge 런타임에 연결할 수 있음을 의미합니다.

>[!NOTE]
> IoT Edge 허브는 MQTT 또는 AMQP를 사용 하 여 연결 하는 클라이언트를 지원 합니다. HTTP를 사용하는 클라이언트는 지원하지 않습니다.

IoT Edge 허브는 로컬에서 실행 되는 IoT Hub의 전체 버전이 아닙니다. IoT Edge 허브는 일부 작업을 IoT Hub에 자동으로 위임 합니다. 예를 들어 디바이스에서 처음으로 연결하려고 할 때 IoT Edge 허브는 IoT Hub에 인증 요청을 전달합니다. 첫 번째 연결이 설정되면 IoT Edge 허브에서 보안 정보를 로컬로 캐시합니다. 클라우드를 다시 인증 하지 않고도 해당 장치에서 나중에 연결할 수 있습니다.

IoT Edge 솔루션에서 사용 하는 대역폭을 줄이기 위해 IoT Edge 허브는 클라우드에 대 한 실제 연결 수를 최적화 합니다. IoT Edge 허브는 모듈 또는 다운스트림 장치에서 논리적 연결을 사용 하 여 클라우드로의 단일 실제 연결에 대 한 연결을 결합 합니다. 이 프로세스의 세부 정보는 솔루션의 나머지 부분에 투명합니다. 클라이언트는 모두 동일한 연결을 통해 전송되는 경우에도 클라우드에 대한 자체 연결이 있다고 생각하면 됩니다.

![IoT Edge 허브는 물리적 디바이스와 IoT Hub 간의 게이트웨이입니다.](./media/iot-edge-runtime/Gateway.png)

IoT Edge 허브는 IoT Hub에 연결되어 있는지 여부를 결정할 수 있습니다. 연결이 끊어진 경우 IoT Edge 허브는 메시지 또는 쌍 업데이트를 로컬로 저장합니다. 연결이 다시 설정되면 모든 데이터가 동기화됩니다. 이 임시 캐시에 사용 되는 위치는 IoT Edge 허브 모듈 쌍의 속성에 의해 결정 됩니다. 캐시의 크기는 제한되지 않으며, 디바이스에 스토리지 용량이 있는 동안에는 증가합니다.자세한 내용은 [오프 라인 기능](offline-capabilities.md)을 참조 하세요.

### <a name="module-communication"></a>모듈 통신

IoT Edge 허브는 모듈 간 통신을 용이하게 합니다. IoT Edge 허브를 메시지 broker로 사용하면 모듈이 서로 독립적으로 유지됩니다. 모듈은 메시지를 수락하는 입력과 메시지를 쓰는 출력을 지정하기만 하면 됩니다. 솔루션 개발자는 이러한 입력 및 출력을 함께 연결 하 여 모듈에서 해당 솔루션과 관련 된 순서로 데이터를 처리할 수 있습니다.

![IoT Edge 허브는 모듈 간 통신을 용이하게 합니다](./media/iot-edge-runtime/module-endpoints.png)

IoT Edge 허브에 데이터를 보내려면 모듈에서 SendEventAsync 메서드를 호출합니다. 첫 번째 인수는 메시지를 보낼 출력을 지정합니다. 다음 의사 코드는 **output1**에 메시지를 보냅니다.

   ```csharp
   ModuleClient client = await ModuleClient.CreateFromEnvironmentAsync(transportSettings);
   await client.OpenAsync();
   await client.SendEventAsync("output1", message);
   ```

메시지를 수신하려면 특정 입력에 들어오는 메시지를 처리하는 콜백을 등록합니다. 다음 의사 코드는 **input1**에서 받은 모든 메시지를 처리 하는 데 사용 되는 messageprocessor 함수를 등록 합니다.

   ```csharp
   await client.SetInputMessageHandlerAsync("input1", messageProcessor, userContext);
   ```

ModuleClient 클래스 및 해당 통신 방법에 대 한 자세한 내용은 [c #](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet), [c](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h), [Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python), [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)또는 [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)의 기본 SDK 언어에 대 한 API 참조를 참조 하세요.

솔루션 개발자는 IoT Edge 허브에서 모듈 간에 메시지를 전달하는 방식을 결정하는 규칙을 지정합니다. 라우팅 규칙은 클라우드에서 정의 되며 모듈 쌍의 IoT Edge 허브로 푸시됩니다. IoT Hub 경로에 대한 동일한 구문이 Azure IoT Edge의 모듈 간 경로를 정의하는 데 사용됩니다. 자세한 내용은 [IoT Edge에서 모듈 배포 및 경로를 설정하는 방법 알아보기](module-composition.md)을 참조하세요.

![모듈 간 경로는 IoT Edge 허브를 통과합니다.](./media/iot-edge-runtime/module-endpoints-with-routes.png)

## <a name="iot-edge-agent"></a>IoT Edge 에이전트

IoT Edge 에이전트는 Azure IoT Edge 런타임을 구성하는 다른 모듈입니다. 모듈을 인스턴스화하고, 모듈을 계속 실행하며, 모듈의 상태를 IoT Hub에 다시 보고합니다. 이 구성 데이터는 IoT Edge 에이전트 모듈 쌍의 속성으로 작성 됩니다.

[IoT Edge 보안 디먼](iot-edge-security-manager.md)은 디바이스 시작 시 IoT Edge 에이전트를 시작합니다. 에이전트는 IoT Hub에서 모듈 쌍을 검색하고 배포 매니페스트를 검사합니다. 배포 매니페스트는 시작되어야 하는 모듈을 선언하는 JSON 파일입니다.

배포 매니페스트의 각 항목에는 모듈에 대 한 특정 정보가 포함 되어 있으며 IoT Edge 에이전트가 모듈의 수명 주기를 제어 하는 데 사용 됩니다. 더 유용한 속성 중 일부는 다음과 같습니다.

* **settings.image** – IoT Edge 에이전트에서 모듈을 시작하는 데 사용하는 컨테이너 이미지입니다. 이미지가 암호로 보호되는 경우 IoT Edge 에이전트는 컨테이너 레지스트리에 대한 자격 증명으로 구성되어야 합니다. 컨테이너 레지스트리에 대한 자격 증명은 배포 매니페스트를 사용하여 또는 IoT Edge 프로그램 폴더에서 `config.yaml` 파일을 업데이트하여 IoT Edge 디바이스 자체에서 원격으로 구성될 수 있습니다.
* **설정 createOptions** – 모듈의 컨테이너를 시작할 때 Moby 컨테이너 데몬에 직접 전달 되는 문자열입니다. 이 속성에 옵션을 추가 하면 포트 전달 또는 모듈의 컨테이너에 볼륨 탑재와 같은 고급 구성을 사용할 수 있습니다.  
* **status** – IoT Edge 에이전트에서 모듈을 배치하는 상태입니다. 일반적으로이 값은 대부분의 사용자가 장치에서 모든 모듈을 즉시 시작 하도록 하는 대부분의 IoT Edge 사용자가 *실행 중* 으로 설정 됩니다. 그러나 중지할 모듈의 초기 상태를 지정하고, 나중에 IoT Edge 에이전트에 모듈 시작을 알릴 때까지 기다릴 수 있습니다.IoT Edge 에이전트는 각 모듈의 상태를 reported 속성의 클라우드로 다시 보고합니다. desired 속성과 reported 속성의 차이는 표시기 또는 오작동 디바이스입니다. 지원되는 상태는 다음과 같습니다.

  * 다운로드
  * 실행 중
  * Unhealthy
  * 실패
  * 중지됨

* **restartPolicy** – IoT Edge 에이전트에서 모듈을 다시 시작하는 방법입니다. 가능한 값은 다음과 같습니다.
  
  * `never` – IoT Edge 에이전트가 모듈을 다시 시작 하지 않습니다.
  * `on-failure` -모듈이 충돌 하면 IoT Edge 에이전트가 다시 시작 합니다. 모듈이 완전히 종료 되 면 IoT Edge 에이전트가 다시 시작 되지 않습니다.
  * `on-unhealthy` -모듈이 충돌 하거나 비정상으로 간주 되는 경우 IoT Edge 에이전트가 다시 시작 합니다.
  * `always` -모듈의 작동이 중단 되는 경우는 비정상으로 간주 되거나 어떤 식으로든 종료 되 면 IoT Edge 에이전트가 다시 시작 합니다.

* **Imagepullpolicy** -IoT Edge 에이전트가 모듈의 최신 이미지를 자동으로 가져오려고 하는지 여부를 지정 합니다. 값을 지정 하지 않는 경우 기본값은 *onCreate*입니다. 가능한 값은 다음과 같습니다.

  * `on-create` -모듈을 시작 하거나 새 배포 매니페스트에 따라 모듈을 업데이트할 때 IoT Edge 에이전트가 컨테이너 레지스트리에서 모듈 이미지를 가져오려고 시도 합니다.
  * `never` -IoT Edge 에이전트는 컨테이너 레지스트리에서 모듈 이미지를 가져오려고 시도 하지 않습니다. 이 구성을 사용 하 여 장치에 모듈 이미지를 가져오고 이미지 업데이트를 관리 하는 일을 담당 합니다.

IoT Edge 에이전트는 IoT Hub에 런타임 응답을 보냅니다. 가능한 응답 목록은 다음과 같습니다.
  
* 200 - 확인
* 400 - 배포 구성이 잘못되었거나 유효하지 않습니다.
* 417-장치에 배포 구성 집합이 없습니다.
* 412 - 배포 구성의 스키마 버전이 잘못되었습니다.
* 406 - IoT Edge 디바이스가 오프라인 상태이거나 상태 보고서를 전송하지 않습니다.
* 500 - IoT Edge 런타임에서 오류가 발생했습니다.

자세한 내용은 [IoT Edge에서 모듈 배포 및 경로를 설정하는 방법 알아보기](module-composition.md)을 참조하세요.

### <a name="security"></a>보안

IoT Edge 에이전트는 IoT Edge 디바이스의 보안에서 중요한 역할을 합니다. 예를 들어 시작 하기 전에 모듈의 이미지를 확인 하는 등의 작업을 수행 합니다.

Azure IoT Edge 보안 프레임 워크에 대 한 자세한 내용은 [IoT Edge security manager](iot-edge-security-manager.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

[Azure IoT Edge 모듈 이해](iot-edge-modules.md)
