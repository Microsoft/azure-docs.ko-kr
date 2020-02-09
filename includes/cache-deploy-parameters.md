---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 04/02/2019
ms.author: wesmc
ms.openlocfilehash: 498a7ee28b9404d0733e4615f4df635a8c904b51
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/18/2019
ms.locfileid: "67182012"
---
### <a name="cacheskuname"></a>cacheSKUName

새 Azure Cache for Redis의 가격 책정 계층입니다.

```json
    "cacheSKUName": {
      "type": "string",
      "allowedValues": [
        "Basic",
        "Standard",
        "Premium"
      ],
      "defaultValue": "Basic",
      "metadata": {
        "description": "The pricing tier of the new Azure Cache for Redis."
      }
    },
```

이 매개 변수 (Basic, Standard 또는 Premium)에 대한 허용 되는 값을 정의 하 고 값을 지정 하는 경우 기본 값 (Basic)을 할당 하는 템플릿입니다. Basic은 최대 53GB를 사용할 수 있는 다양한 크기의 단일 노드를 제공합니다. Standard는 최대 53GB를 사용할 수 있으며 99.9%의 SLA를 제공하는 다양한 크기의 2-노드 주/복제본을 제공합니다.

### <a name="cacheskufamily"></a>cacheSKUFamily

SKU 제품군입니다.

```json
    "cacheSKUFamily": {
      "type": "string",
      "allowedValue/s": [
        "C",
        "P"
      ],
      "defaultValue": "C",
      "metadata": {
        "description": "The family for the sku."
      }
    },
```

### <a name="cacheskucapacity"></a>cacheSKUCapacity

새 Azure Cache for Redis 인스턴스의 크기입니다.

기본 및 표준 제품군:

```json
    "cacheSKUCapacity": {
      "type": "int",
      "allowedValues": [
        0,
        1,
        2,
        3,
        4,
        5,
        6
      ],
      "defaultValue": 0,
      "metadata": {
        "description": "The size of the new Azure Cache for Redis instance. "
      }
    }
```

프리미엄 값 캐시 용량 정의 1에서 6으로 0에서 5 대신 실행 허용 되는 값을 제외 하 고 동일 합니다.

템플릿 (0 ~ 6 기본 및 표준 제품군에 대한; 프리미엄 제품군에 대해 1-5)이 매개이 변수에 허용 되는 정수 값을 정의 합니다. 없는 값을 지정 하는 경우 템플릿을 Basic 및 Standard, Premium 1에 대한 기본값은 0 할당 합니다.

값은 다음 캐시 크기에 해당 합니다.

| 값 | 기본 및 표준<br>캐시 크기 | Premium<br>캐시 크기 |
| :---: | :------------------------------: | :-------------------: |
| 0     | 250MB (기본값)                 | 해당 없음                   |
| 1     | 1 GB                             | 6GB (기본값)        |
| 2     | 2.5GB                           | 13GB                 |
| 3     | 6GB                             | 26GB                 |
| 4     | 13GB                            | 53GB                 |
| 5     | 26GB                            | 120GB                |
| 6     | 53GB                            | 해당 없음                   |
