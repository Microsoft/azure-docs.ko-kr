---
title: IoT Hub C SDK를 사용하여 제한된 디바이스에 대한 Azure IoT Hub 개발 | Microsoft Docs
description: 개발자 가이드 - 제한된 디바이스에 Azure IoT SDK를 사용하여 개발하는 방법에 대한 지침입니다.
services: iot-hub
documentationcenter: c
author: yzhong94
manager: timlt
editor: ''
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
origin.date: 05/24/2018
ms.date: 04/29/2019
ms.author: v-yiso
ms.openlocfilehash: 7788bca621a59ec8cdfe36edf73a99efca8c460c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61320942"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Azure IoT C SDK를 사용하여 제한된 디바이스 개발

Azure IoT Hub C SDK는 ANSI C(C99)로 작성되었으며, 작은 디스크 및 메모리 공간으로 다양한 플랫폼을 작동하는 데 적합합니다. 권장되는 RAM은 64KB 이상이지만, 정확한 메모리 공간은 사용되는 프로토콜, 열린 연결 수 및 대상 플랫폼에 따라 다릅니다.
> [!NOTE]
> * Azure IoT C SDK 개발에 도움이 되도록 리소스 사용량 정보를 정기적으로 게시 합니다.  방문 하십시오 우리의 [GitHub 리포지토리](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md) 최신 벤치 마크를 검토 합니다.
>

C SDK는 apt-get, NuGet 및 MBED의 패키지 형태로 사용할 수 있습니다. 제한된 디바이스를 대상으로 지정하려면 대상 플랫폼에 맞게 SDK를 로컬로 빌드하는 것이 좋습니다. 이 설명서에서는 C SDK의 공간을 줄이기 위해 [cmake](https://cmake.org/)를 사용하여 특정 기능을 제거하는 방법을 보여줍니다. 또한 제한된 디바이스를 사용하기 위한 최적의 프로그래밍 모델에 대해 설명합니다.

## <a name="building-the-c-sdk-for-constrained-devices"></a>제한된 디바이스에 대한 C SDK 빌드

제한된 디바이스에 대한 C SDK를 빌드합니다.

### <a name="prerequisites"></a>필수 조건

이 [C SDK 설정 가이드](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)에 따라 C SDK를 빌드하기 위한 개발 환경을 준비합니다. cmake를 사용하여 빌드하는 단계를 시작하기 전에 cmake 플래그를 호출하여 사용하지 않는 기능을 제거할 수 있습니다.

### <a name="remove-additional-protocol-libraries"></a>추가 프로토콜 라이브러리 제거

C SDK는 현재 5 프로토콜을 지원합니다. MQTT, WebSocket AMQPs, HTTPS 및 WebSocket을 통한 AMQP을 통한 MQTT 대부분의 시나리오에서는 1-2개의 프로토콜이 클라이언트에서 실행되어야 하므로 SDK에서 사용하지 않는 프로토콜 라이브러리를 제거할 수 있습니다. 시나리오에 적절한 통신 프로토콜을 선택하는 방법에 대한 자세한 내용은 [IoT Hub 통신 프로토콜 선택](iot-hub-devguide-protocols.md)에서 찾을 수 있습니다. 예를 들어 MQTT는 제한된 디바이스에 더 적합한 경량 프로토콜입니다.

다음 cmake 명령을 사용하여 AMQP 및 HTTP 라이브러리를 제거할 수 있습니다.

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>SDK 로깅 기능 제거

C SDK는 디버깅에 유용한 광범위한 로깅 기능을 제공합니다. 다음 cmake 명령을 사용하여 프로덕션 디바이스에 대한 로깅 기능을 제거할 수 있습니다.
```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>Blob에 업로드 기능 제거

SDK의 기본 제공 기능을 사용하여 대용량 파일을 Azure Storage에 업로드할 수 있습니다. Azure IoT Hub는 Azure Storage 계정과 연결된 디스패처 역할을 합니다. 이 기능을 사용하여 미디어 파일, 대용량 원격 분석 일괄 처리 및 로그를 보낼 수 있습니다. [IoT Hub를 사용하여 파일 업로드](iot-hub-devguide-file-upload.md)에서 자세한 정보를 얻을 수 있습니다. 이 기능이 애플리케이션에 필요하지 않은 경우 다음 cmake 명령을 사용하여 해당 기능을 제거할 수 있습니다.

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Linux 환경에서 strip 실행

이진 파일이 Linux 시스템에서 실행되는 경우 컴파일 후에 [strip 명령](https://en.wikipedia.org/wiki/Strip_(Unix))을 활용하여 최종 응용 프로그램의 크기를 줄일 수 있습니다.

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>제한된 디바이스에 대한 프로그래밍 모델

다음으로, 제한된 디바이스에 대한 프로그래밍 모델을 살펴봅니다.

### <a name="avoid-using-the-serializer"></a>직렬 변환기 사용 방지

C SDK에는 선언적 매핑 테이블을 사용하여 메서드 및 디바이스 쌍 속성을 정의할 수 있는 선택적 [C SDK 직렬 변환기](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)가 있습니다. 직렬 변환기는 개발을 간소화하기 위해 설계되었지만 오버헤드가 추가되므로 제한된 디바이스에 적합하지 않습니다. 이 경우에 원시 클라이언트 API를 사용하고 [parson](https://github.com/kgabis/parson)과 같은 경량 파서를 사용하여 JSON을 구문 분석하는 것이 좋습니다.

### <a name="use-the-lower-layer-ll"></a>_LL_(하위 계층) 사용

C SDK는 두 가지 프로그래밍 모델을 지원합니다. 한 집합에는 하위 계층을 나타내는 _LL_ 중위가 있는 API가 있습니다. 이 API 집합은 더 간단하며 작업자 스레드를 회전하지 않으므로 사용자가 일정 예약을 수동으로 제어해야 합니다. 예를 들어 디바이스 클라이언트의 경우 _LL_ API는 이 [헤더 파일](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h)에 있습니다. 

_LL_ 인덱스가 없는 다른 API 집합을 편의 계층이라고 하며 작업자 스레드가 자동으로 회전합니다. 예를 들어 디바이스 클라이언트용 편의 계층 API는 이 [IoT 디바이스 클라이언트 헤더 파일](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h)에서 찾을 수 있습니다. 각각의 추가 스레드에서 상당한 양의 시스템 리소스를 사용할 수 있는 제한된 디바이스의 경우 _LL_ API를 사용하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계
Azure IoT C SDK 아키텍처에 대한 자세한 내용은 다음을 참조하세요.
- [Azure IoT C SDK 소스 코드](https://github.com/Azure/azure-iot-sdk-c/)
- [C용 Azure IoT 디바이스 SDK 소개](iot-hub-device-sdk-c-intro.md)

------
[lnk-cmake]: https://cmake.org/
[lnk-devbox-setup]:  https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md
[lnk-choosing-protocol]: iot-hub-devguide-protocols.md
[lnk-hub-file-upload]: iot-hub-devguide-file-upload.md
[lnk-strip]: https://en.wikipedia.org/wiki/Strip_(Unix)
[lnk-serializer]: https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer
[lnk-parson]: https://github.com/kgabis/parson