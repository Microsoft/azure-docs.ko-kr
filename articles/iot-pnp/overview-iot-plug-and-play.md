---
title: IoT 플러그 앤 플레이 미리 보기 소개 | Microsoft Docs
description: IoT 플러그 앤 플레이 미리 보기에 대해 알아봅니다. IoT 플러그 앤 플레이는 스마트 IoT 디바이스가 기능을 선언할 수 있는 개방형 모델링 언어를 기반으로 합니다. IoT 디바이스는 클라우드 솔루션에 연결할 때 디바이스 모델이라는 이러한 선언을 제시합니다. 그러면 클라우드 솔루션이 코드를 작성하지 않고도 디바이스를 자동으로 이해하고 디바이스와 상호 작용을 시작할 수 있습니다.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.custom: references_regions
ms.openlocfilehash: 32a873af3d287c3bd1e83de6db8e17ebc1d2958b
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855621"
---
# <a name="what-is-iot-plug-and-play-preview"></a>IoT 플러그 앤 플레이 미리 보기란?

IoT 플러그 앤 플레이 미리 보기를 사용하면 솔루션 빌더가 수동으로 구성하지 않고 스마트 디바이스를 솔루션과 통합할 수 있습니다. IoT 플러그 앤 플레이의 핵심은 디바이스가 IoT 플러그 앤 플레이 가능 애플리케이션에 디바이스의 기능을 알리는 데 사용하는 디바이스 모델입니다. 이 모델은 다음을 정의하는 요소 세트로 구성됩니다.

- 속성 - 디바이스 또는 다른 엔터티의 읽기 전용 또는 쓰기 가능 상태를 나타냅니다. 예를 들어 디바이스 일련 번호는 읽기 전용 속성일 수 있으며, 자동 온도 조절기의 목표 온도는 쓰기 가능 속성일 수 있습니다.
- 원격 분석 - 데이터가 규칙적인 센서 판독 스트림, 가끔 발생하는 오류 또는 정보 메시지인지 여부에 관계 없이 디바이스가 내보내는 데이터입니다.
- 디바이스에서 수행할 수 있는 함수 또는 작업을 설명하는 _명령_. 예를 들어 명령으로 게이트웨이를 다시 부팅하거나 원격 카메라를 사용하여 사진을 찍을 수 있습니다.

인터페이스에서 이러한 요소를 그룹화하여 모델 전체에서 재사용하면 협업을 더 쉽게 수행하고 개발 속도를 높일 수 있습니다.

IoT 플러그 앤 플레이가 [Azure Digital Twins](../digital-twins/about-digital-twins.md)와 작동하도록 하려면 [DTDL(Digital Twins 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)을 사용하여 모델 및 인터페이스를 정의합니다. IoT 플러그 앤 플레이 및 DTDL은 커뮤니티에 공개되며, Microsoft는 고객, 파트너 및 업계 기업과의 협업을 환영합니다. 두 가지 모두 JSON-LD 및 RDF와 같은 개방형 W3C 표준에 기반하기 때문에 서비스와 도구를 더 쉽게 채택할 수 있습니다.

IoT 플러그 앤 플레이 및 DTDL 사용에 대한 추가 비용은 없습니다. [Azure IoT Hub](../iot-hub/about-iot-hub.md) 및 기타 Azure 서비스에 대한 표준 요금은 동일하게 유지됩니다.

이 문서에서는 다음 내용을 간략하게 설명합니다.

- IoT 플러그 앤 플레이를 사용하는 프로젝트와 연결된 일반적인 역할
- 애플리케이션에서 IoT 플러그 앤 플레이 디바이스를 사용하는 방법
- IoT 플러그 앤 플레이를 지원하는 IoT 디바이스 애플리케이션을 개발하는 방법

## <a name="user-roles"></a>사용자 역할

IoT 플러그 앤 플레이는 다음과 같은 두 가지 유형의 개발자에게 유용합니다.

- 솔루션 빌더는 Azure IoT Hub 및 기타 Azure 리소스를 사용하여 IoT 솔루션을 개발하고 통합할 IoT 디바이스를 식별하는 일을 담당합니다.
- 디바이스 빌더는 솔루션에 연결된 디바이스에서 실행되는 코드를 만듭니다.

## <a name="use-iot-plug-and-play-devices"></a>IoT 플러그 앤 플레이 디바이스 사용

솔루션 빌더는 IoT 플러그 앤 플레이 디바이스를 사용하는 클라우드 호스팅 IoT 솔루션을 개발할 수 있습니다. [IoT Hub](../iot-hub/about-iot-hub.md) - IoT 애플리케이션과 디바이스 간의 안전한 양방향 통신을 위한 메시지 허브 역할을 하는 관리형 클라우드 서비스입니다.

IoT 플러그 앤 플레이 디바이스를 IoT 허브에 연결하면 [Azure IoT 탐색기](./howto-use-iot-explorer.md) 도구를 사용하여 모델을 구성하는 인터페이스에 정의된 원격 분석, 속성 및 명령을 볼 수 있습니다.

## <a name="develop-an-iot-device-application"></a>IoT 디바이스 애플리케이션 개발

디바이스 빌더는 IoT 플러그 앤 플레이를 지원하는 IoT 하드웨어 제품을 개발할 수 있습니다. 이 프로세스에는 세 가지 주요 단계가 포함됩니다.

1. 디바이스 모델을 정의합니다. [DTDL](https://github.com/Azure/opendigitaltwins-dtdl)을 사용하여 디바이스의 기능을 정의하는 JSON 파일 세트를 제작합니다. 이 모델은 물리적 제품과 같은 전체 엔터티를 설명하고, 해당 엔터티에 의해 구현된 인터페이스 세트를 정의합니다. 인터페이스는 디바이스에서 지원되는 원격 분석, 속성 및 명령을 고유하게 식별하는 공유 계약입니다. 인터페이스는 여러 모델에서 재사용할 수 있습니다.

1. 원격 분석, 속성 및 명령이 IoT 플러그 앤 플레이 규칙을 따르는 방식으로 디바이스 소프트웨어 또는 펌웨어를 제작합니다.

1. 디바이스는 MQTT 연결의 일부로 모델 ID를 알려줍니다. Azure IoT SDK에는 연결 시 모델 ID를 제공하는 새로운 구문이 포함되어 있습니다.

> [!Important]
> IoT 플러그 앤 플레이 디바이스는 WebSocket을 통해 MQTT 또는 MQTT를 사용해야 합니다. AMQP 또는 HTTP와 같은 다른 프로토콜은 IoT 플러그 앤 플레이 디바이스를 구현하는 데 유효하지 않습니다.

## <a name="device-certification"></a>디바이스 인증

[IoT 플러그 앤 플레이 디바이스 인증 프로그램](howto-certify-device.md)은 디바이스가 IoT 플러그 앤 플레이 인증 요구 사항을 충족하는지 확인합니다. 인증된 디바이스를 공용 [Azure IoT 디바이스 카탈로그 인증](https://aka.ms/devicecatalog)에 추가할 수 있습니다.

## <a name="regional-availability"></a>국가별 가용성

IoT 플러그 앤 플레이 미리 보기 새로 고침은 미국 중부, 북유럽 및 일본 동부에서 생성된 IoT 허브에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

IoT 플러그 앤 플레이의 개요를 살펴보았으므로, 그 다음 단계로 다음 빠른 시작 중 하나를 도전해보세요.

- [디바이스를 IoT Hub에 연결(C)](./quickstart-connect-device-c.md)
- [솔루션에서 디바이스와 상호 작용(Node.js)](./quickstart-service-node.md)

