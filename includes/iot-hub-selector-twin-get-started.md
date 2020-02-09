---
author: wesmc7777
ms.author: wesmc
ms.service: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.openlocfilehash: c412f7f3e20e4d04083e457bfb245b850b65e126
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050437"
---
> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-twin-getstarted.md)
> * [.NET](../articles/iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)
> * [Java](../articles/iot-hub/iot-hub-java-java-twin-getstarted.md)
> * [Python](../articles/iot-hub/iot-hub-python-twin-getstarted.md)

장치 쌍은 메타 데이터, 구성 및 조건을 포함 하 여 장치 상태 정보를 저장 하는 JSON 문서입니다. IoT Hub는 여기에 연결하는 각 디바이스에 대해 하나의 디바이스 쌍을 유지합니다.

[!INCLUDE [iot-hub-basic](iot-hub-basic-whole.md)]

디바이스 쌍의 용도:

* 솔루션 백 엔드의 디바이스 메타데이터를 저장합니다.

* 장치 앱에서 사용 하는 연결 방법과 같은 사용 가능한 기능 및 조건과 같은 현재 상태 정보를 보고 합니다.

* 장치 앱과 백 엔드 앱 간에 장기 실행 워크플로 (예: 펌웨어 및 구성 업데이트)의 상태를 동기화 합니다.

* 디바이스 메타데이터, 구성 또는 상태를 쿼리합니다.

디바이스 쌍은 디바이스 구성 및 상태를 동기화하고 쿼리하기 위해 설계되었습니다. 디바이스 쌍을 사용하는 경우에 대한 자세한 내용은 [디바이스 쌍 이해](../articles/iot-hub/iot-hub-devguide-device-twins.md)에서 찾을 수 있습니다.

장치 쌍은 IoT hub에 저장 되 고 다음 요소를 포함 합니다.

* **태그**. 솔루션 백 엔드에서 액세스할 수 있는 장치 메타 데이터입니다.

* **desired 속성**. 솔루션 백 엔드에서 수정 가능 하 고 장치 앱에서 관찰 가능한 JSON 개체입니다.

* **reported 속성**. 장치 앱에서 수정할 수 있고 솔루션 백 엔드에서 읽을 수 있는 JSON 개체입니다.

태그 및 속성은 배열을 포함할 수 없지만, 개체는 중첩될 수 있습니다.

다음 그림은 장치 쌍 조직을 보여줍니다.

![기능을 보여 주는 디바이스 쌍 이미지](./media/iot-hub-selector-twin-get-started/twin.png)

또한 솔루션 백 엔드는 위의 모든 데이터를 기반으로 하는 디바이스 쌍을 쿼리할 수 있습니다.
장치 쌍에 대한 자세한 내용은 장치 쌍 [이해](../articles/iot-hub/iot-hub-devguide-device-twins.md)를 참조 하세요. 쿼리 하는 방법에 대한 자세한 내용은 [IoT Hub 쿼리 언어](../articles/iot-hub/iot-hub-devguide-query-language.md)를 참조 하세요.


이 자습서에서는 다음을 수행하는 방법에 대해 설명합니다.

* 장치 쌍에 태그를 추가 하는 백 엔드 앱과 연결 채널을 장치 쌍에 보고 된 속성으로 보고 하는 시뮬레이션 된 장치 앱을 만듭니다.

* 이전에 만든 태그 및 속성에 필터를 사용하여 백 엔드 앱에서 디바이스를 쿼리합니다.
