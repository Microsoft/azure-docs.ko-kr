---
title: 모듈이 디바이스에서 로직을 실행하는 방법 알아보기 - Azure IoT Edge | Microsoft Docs
description: Azure IoT Edge 모듈은 IoT Edge 디바이스에서 비즈니스 논리를 실행할 수 있도록 원격으로 배포 및 관리할 수 있는 컨테이너화된 논리 단위입니다.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/21/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: devx-track-csharp
ms.openlocfilehash: 9feef9ec04bcc44bc09d55202b72ada4adc94cf5
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017059"
---
# <a name="understand-azure-iot-edge-modules"></a>Azure IoT Edge 모듈 이해

Azure IoT Edge를 사용 하 여 *모듈*형태로에 지에 비즈니스 논리를 배포 하 고 관리할 수 있습니다. Azure IoT Edge 모듈은 IoT Edge가 관리하는 계산의 최소 단위이며 Azure 서비스(예: Azure Stream Analytics) 또는 고유한 솔루션별 코드를 포함할 수 있습니다. 모듈을 개발, 배포 및 유지 관리 하는 방법을 이해 하려면 모듈의 네 가지 개념적 요소를 고려 합니다.

* **모듈 이미지**는 모듈을 정의하는 소프트웨어가 포함된 패키지입니다.
* **모듈 인스턴스**는 IoT Edge 디바이스에서 모듈 이미지를 실행하는 특정 계산 단위입니다. 모듈 인스턴스는 IoT Edge 런타임에 의해 시작됩니다.
* **모듈 ID**는 각 모듈 인스턴스에 연결된 IoT Hub에 저장된 정보(보안 자격 증명 포함)입니다.
* **모듈 쌍**은 IoT Hub에 저장된 JSON 문서로, 메타데이터, 구성 및 조건 등 모듈 인스턴스의 상태 정보를 포함합니다.

## <a name="module-images-and-instances"></a>모듈 이미지 및 인스턴스

IoT Edge 모듈 이미지에는 IoT Edge 런타임의 관리, 보안 및 통신 기능을 활용하는 애플리케이션이 포함되어 있습니다. 자신만의 모듈 이미지를 개발하거나 Azure Stream Analytics와 같이 지원되는 Azure 서비스에서 모듈 이미지를 내보낼 수 있습니다.
이미지는 클라우드에 존재하며 다른 솔루션으로 업데이트, 변경 및 배포할 수 있습니다. 예를 들어 기계 학습을 사용하여 생산 라인의 결과를 예측하는 모듈은 컴퓨터 시각을 사용하여 드론을 제어하는 모듈과는 별도의 이미지로 존재합니다.

모듈 이미지가 디바이스에 배포되고 IoT Edge 런타임에서 시작될 때마다 해당 모듈의 새 인스턴스가 만들어집니다. 전 세계의 다른 부분에 있는 두 디바이스가 동일한 모듈 이미지를 사용할 수 있습니다. 그러나 디바이스에서 모듈이 시작될 때 각 디바이스는 고유한 모듈 인스턴스를 갖게 됩니다.

![다이어그램 - 클라우드의 모듈 이미지, 디바이스의 모듈 인스턴스](./media/iot-edge-modules/image_instance.png)

구현 시 모듈 이미지는 컨테이너 이미지로 리포지토리에 존재하며 모듈 인스턴스는 디바이스의 컨테이너입니다.

<!--
As use cases for Azure IoT Edge grow, new types of module images and instances will be created. For example, resource constrained devices cannot run containers so may require module images that exist as dynamic link libraries and instances that are executables. 
-->

## <a name="module-identities"></a>모듈 ID

IoT Edge 런타임에서 새 모듈 인스턴스를 만들면 해당 모듈 id를 가져옵니다. 모듈 id는 IoT Hub에 저장 되며 해당 모듈 인스턴스에 대 한 모든 로컬 및 클라우드 통신의 주소 지정 및 보안 범위로 사용 됩니다.

모듈 인스턴스와 연관된 ID는 인스턴스가 실행 중인 디바이스의 ID와 솔루션의 해당 모듈에 제공한 이름에 따라 다릅니다. 예를 들어 Azure Stream Analytics를 사용하는 `insight` 모듈을 호출하여 `Hannover01`이라는 디바이스에 배포하면 IoT Edge 런타임에서 `/devices/Hannover01/modules/insight`이라는 해당 모듈 ID를 만듭니다.

동일한 디바이스에 하나의 모듈 이미지를 여러 번 배포해야 하는 경우 동일한 이미지를 다른 이름으로 여러 번 배포할 수 있습니다.

![다이어그램 - 모듈 ID는 디바이스 내에서나 디바이스 간에 고유합니다.](./media/iot-edge-modules/identity.png)

## <a name="module-twins"></a>모듈 쌍

각 모듈 인스턴스에는 모듈 인스턴스를 구성하는 데 사용할 수 있는 해당 모듈 쌍이 있습니다. 인스턴스와 쌍은 모듈 ID를 통해 서로 연결됩니다.

모듈 쌍은 모듈 정보와 구성 속성을 저장하는 JSON 문서입니다. 이 개념은 IoT Hub의 [디바이스 쌍](../iot-hub/iot-hub-devguide-device-twins.md) 개념과 유사합니다. 모듈 쌍의 구조는 디바이스 쌍과 같습니다. 두 유형의 쌍과 상호 작용하는 데 사용되는 API도 같습니다. 이 둘의 유일한 차이점은 클라이언트 SDK를 인스턴스화하는 데 사용되는 ID입니다.

```csharp
// Create a ModuleClient object. This ModuleClient will act on behalf of a
// module since it is created with a module’s connection string instead
// of a device connection string.
ModuleClient client = new ModuleClient.CreateFromEnvironmentAsync(settings);
await client.OpenAsync();

// Get the module twin
Twin twin = await client.GetTwinAsync();
```

## <a name="offline-capabilities"></a>오프라인 기능

Azure IoT Edge 모듈은 한 번 이상 IoT Hub와 동기화 한 후 무기한으로 작업할 수 있습니다. IoT Edge 장치는이 오프 라인 기능을 다른 IoT 장치로 확장할 수도 있습니다. 자세한 내용은 [IoT Edge 디바이스, 모듈 및 하위 디바이스용 확장 오프라인 기능 이해](offline-capabilities.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [IoT Edge 모듈 개발을 위한 요구 사항 및 도구 이해](module-development.md)
* [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)
