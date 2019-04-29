---
title: 원격 모니터링 솔루션의 시뮬레이션된 디바이스 동작 - Azure | Microsoft Docs
description: 이 문서에서는 JavaScript를 사용하여 원격 모니터링 솔루션의 시뮬레이션된 디바이스 동작을 정의하는 방법을 설명합니다.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 01/29/2018
ms.topic: conceptual
ms.openlocfilehash: c2151a4b1eb2a853ed343f6720b4f53af5e5e449
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61446347"
---
# <a name="implement-the-device-model-behavior"></a>디바이스 모델 동작 구현

[디바이스 모델 스키마 이해](iot-accelerators-remote-monitoring-device-schema.md) 문서에서는 시뮬레이션된 디바이스 모델을 정의하는 스키마에 대해 설명했습니다. 해당 문서에서는 시뮬레이션된 디바이스의 동작을 구현하는 다음 두 가지 유형의 JavaScript 파일을 참조했습니다.

- 디바이스의 내부 상태를 업데이트하기 위해 고정 간격으로 실행되는 **상태** JavaScript 파일
- 솔루션이 디바이스에서 메서드를 호출할 때 실행되는 **메서드** JavaScript 파일

> [!NOTE]
> 디바이스 모델 동작은 디바이스 시뮬레이션 서비스에 호스트된 시뮬레이션된 디바이스에만 해당합니다. 실제 디바이스를 만들려는 경우 [원격 모니터링 솔루션 가속기에 디바이스 연결](iot-accelerators-connecting-devices.md)을 참조하세요.

이 문서에서는 다음 방법을 설명합니다.

>[!div class="checklist"]
> * 시뮬레이션된 디바이스의 상태 제어
> * 시뮬레이션된 디바이스가 원격 모니터링 솔루션의 메서드 호출에 응답하는 방법 정의
> * 스크립트 디버그

[!INCLUDE [iot-accelerators-device-behavior](../../includes/iot-accelerators-device-behavior.md)]

## <a name="next-steps"></a>다음 단계

이 문서에서는 고유한 사용자 지정 시뮬레이션된 디바이스 모델의 동작을 정의하는 방법을 설명했습니다. 이 문서에서는 다음을 수행하는 방법을 보여 주었습니다.

<!-- Repeat task list from intro -->
>[!div class="checklist"]
> * 시뮬레이션된 디바이스의 상태 제어
> * 시뮬레이션된 디바이스가 원격 모니터링 솔루션의 메서드 호출에 응답하는 방법 정의
> * 스크립트 디버그

이제 시뮬레이션된 디바이스의 동작을 지정하는 방법을 배웠으므로 다음 단계에서는 [시뮬레이션된 디바이스를 만드는](iot-accelerators-remote-monitoring-create-simulated-device.md) 방법을 알아보는 것이 좋습니다.

원격 모니터링 솔루션에 대한 자세한 개발자 정보는 다음을 참조하세요.

* [개발자 참조 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide)
* [개발자 문제 해결 가이드](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Troubleshooting-Guide)

<!-- Next tutorials in the sequence -->
