---
author: robinsh
manager: philmea
ms.author: robinsh
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 57a5de4c590ff98429aa1d4a0b96cebed6084a0f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60780117"
---
## <a name="add-a-consumer-group-to-your-iot-hub"></a>IoT Hub에 소비자 그룹 추가

소비자 그룹은 애플리케이션에서 Azure IoT Hub의 데이터를 끌어오는 데 사용됩니다. 이 자습서에서는 예정된 Azure 서비스에서 IoT Hub의 데이터를 읽는 데 사용할 소비자 그룹을 만듭니다.

IoT Hub에 소비자 그룹을 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에서 IoT Hub를 엽니다.

2. 왼쪽 창에서 **기본 제공 엔드포인트**를 클릭하고, 위쪽 창에서 **이벤트**를 선택하고, 오른쪽 창의 **소비자 그룹** 아래에서 이름을 입력합니다. **기본 TTL** 값을 변경한 후에 **저장**을 클릭하고, 다시 원래 값으로 되돌립니다.

   ![IoT Hub에서 소비자 그룹 만들기](./media/iot-hub-get-started-create-consumer-group/iot-hub-create-consumer-group-azure.png)
