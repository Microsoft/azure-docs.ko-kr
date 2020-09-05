---
title: 용어 설명 - IoT 플러그 앤 플레이 미리 보기 | Microsoft Docs
description: 개념 - IoT 플러그 앤 플레이 미리 보기와 관련된 일반적인 용어 설명입니다.
author: dominicbetts
ms.author: dobett
ms.date: 07/22/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 2320bed07f574c096be1883a9d82da7311e92fa7
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854207"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT 플러그 앤 플레이 미리 보기용 용어 설명

IoT 플러그 앤 플레이 문서에서 사용되는 일반적인 용어의 정의입니다.

## <a name="azure-iot-explorer-tool"></a>Azure IoT Explorer 도구

Azure IoT Explorer는 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)와 상호 작용하고 이러한 디바이스를 테스트하는 데 사용할 수 있는 그래픽 도구입니다. 로컬 컴퓨터에 도구를 설치한 후에 이 도구를 사용하여 다음 작업을 수행할 수 있습니다.

- [IoT Hub](#azure-iot-hub)에 연결된 디바이스를 확인합니다.
- IoT 플러그 앤 플레이 디바이스에 연결합니다.
- 장치 [구성 요소](#component)를 확인 합니다.
- 디바이스가 전송하는 [원격 분석](#telemetry)을 봅니다.
- 디바이스 [속성](#properties)을 사용합니다.
- 디바이스 [명령](#commands)을 호출합니다.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub는 클라우드에서 호스팅되는 관리 서비스이며, IoT 애플리케이션과 이를 통해 관리하는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다. [Iot 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 IoT Hub에 연결할 수 있습니다. IoT 솔루션은 IoT Hub를 사용하여 다음을 지원합니다.

- 디바이스에서 클라우드 기반 솔루션에 원격 분석을 전송할 수 있도록 합니다.
- 클라우드 기반 솔루션에서 연결된 디바이스를 관리할 수 있도록 합니다.

## <a name="azure-iot-device-sdk"></a>Azure IoT 디바이스 SDK

IoT 플러그 앤 플레이 디바이스 클라이언트 애플리케이션을 빌드하는 데 사용할 수 있는 여러 언어에 대한 디바이스 SDK가 있습니다.

## <a name="commands"></a>명령

[인터페이스](#interface)에 정의된 명령은 [디지털 쌍](#digital-twin)에서 실행할 수 있는 메서드를 나타냅니다. 예를 들어, 디바이스를 재부팅하는 명령이 여기에 해당합니다.

## <a name="component"></a>구성 요소

구성 요소를 사용 하 여 다른 인터페이스의 어셈블리로 모델 [인터페이스](#interface) 를 빌드할 수 있습니다. [장치 모델](#device-model) 은 여러 인터페이스를 구성 요소로 결합할 수 있습니다. 예를 들어 모델에는 스위치 구성 요소 및 자동 온도 조절기 구성 요소가 포함 될 수 있습니다. 모델의 여러 구성 요소는 동일한 인터페이스 유형을 사용할 수도 있습니다. 예를 들어 모델에는 두 개의 자동 온도 조절기 구성 요소가 포함 될 수 있습니다.

## <a name="connection-string"></a>연결 문자열

연결 문자열은 엔드포인트에 연결하는 데 필요한 정보를 캡슐화합니다. 일반적으로 연결 문자열에는 엔드포인트의 주소와 보안 정보가 포함되지만 연결 문자열의 형식은 서비스 간에 다양합니다. IoT Hub 서비스와 연관된 연결 문자열에는 다음 두 종류가 있습니다.

- 디바이스 연결 문자열은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 IoT hub의 디바이스 연결 엔드포인트에 연결할 수 있도록 합니다. 디바이스의 클라이언트 코드는 연결 문자열을 사용하여 IoT Hub와의 보안 연결을 설정합니다.
- IoT Hub 연결 문자열은 백 엔드 솔루션 및 도구에서 IoT Hub의 서비스 연결 엔드포인트에 안전하게 연결할 수 있도록 합니다. 이러한 솔루션 및 도구는 IoT Hub 및 연결된 디바이스를 관리합니다.

## <a name="device-certification"></a>디바이스 인증

IoT 플러그 앤 플레이 장치 인증 프로그램은 장치가 IoT 플러그 앤 플레이 인증 요구 사항을 충족 하는지 확인 합니다. 인증 된 장치를 [Azure IoT 장치 카탈로그에 대해 인증](https://aka.ms/devicecatalog)된 공용에 추가할 수 있습니다.

## <a name="device-model"></a>디바이스 모델

장치 모델은 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 를 설명 하 고 장치를 구성 하는 [구성 요소](#component) 를 정의 합니다. 단순 장치 모델에는 별도의 구성 요소가 없고 단일 루트 수준 인터페이스에 대 한 정의가 포함 되어 있습니다. 더 복잡 한 장치 모델에는 여러 구성 요소가 포함 됩니다. 장치 모델은 일반적으로 물리적 장치, 제품 또는 SKU에 해당 합니다. [디지털 Twins 정의 언어 버전 2](#digital-twins-definition-language) 를 사용 하 여 장치 모델을 정의 합니다.

## <a name="device-builder"></a>장치 빌더

장치 빌더는 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device)에서 실행 되도록 코드를 구현할 때 [장치 모델](#device-model) 및 [인터페이스](#interface) 를 사용 합니다. 일반적으로 장치 빌더는 [Azure IoT 장치 sdk](#azure-iot-device-sdk) 중 하나를 사용 하 여 장치 클라이언트를 구현 하지만 반드시 필요한 것은 아닙니다.

## <a name="device-modeling"></a>디바이스 모델링

[장치 빌더](#device-builder) 는 [디지털 Twins 정의 언어](#digital-twins-definition-language) 를 사용 하 여 [IoT 플러그 앤 플레이 장치의](#iot-plug-and-play-device)기능을 모델링 합니다. [솔루션 빌더](#solution-builder) 는 모델에서 IoT 솔루션을 구성할 수 있습니다.

## <a name="digital-twin"></a>디지털 쌍

디지털 쌍은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)의 모델입니다. 디지털 쌍은 [디지털 쌍 정의 언어](#digital-twins-definition-language)를 사용 하 여 모델링 됩니다. [Azure IoT 디바이스 SDK](#azure-iot-device-sdk)를 사용하여 런타임에 디지털 쌍과 상호 작용할 수 있습니다. 예를 들어, 디바이스의 디지털 쌍에서 속성 값을 설정할 수 있으며 SDK는 클라우드의 IoT 솔루션에 이 변경 내용을 전달합니다.

## <a name="digital-twin-change-events"></a>디지털 쌍 변경 이벤트

[IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)가 [IoT Hub](#azure-iot-hub)에 연결된 경우 허브는 라우팅 기능을 사용하여 디지털 쌍 변경 내용에 대한 알림을 보낼 수 있습니다. 예를 들어, 장치에서 [속성](#properties) 값이 변경 될 때마다 이벤트 허브와 같은 끝점에 알림을 보낼 수 IoT Hub.

## <a name="digital-twins-definition-language"></a>디지털 Twins 정의 언어

[IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에 대한 모델 및 인터페이스를 설명하는 언어입니다. 디지털 쌍 [의](#digital-twin) 기능을 설명 하 고 IoT 플랫폼과 iot 솔루션을 사용 하 여 엔터티의 의미 체계를 활용 하려면 [디지털 쌍 정의 언어 버전 2](https://github.com/Azure/opendigitaltwins-dtdl) 를 사용 합니다.

## <a name="digital-twin-route"></a>디지털 쌍 경로

[IoT hub](#azure-iot-hub) 에서 설정 되어 [디지털 쌍 변경 이벤트](#digital-twin-change-events) 와 끝점 (예: 이벤트 허브)을 전달 합니다.

## <a name="interface"></a>인터페이스

인터페이스는 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 또는 [디지털 쌍](#digital-twin)에서 구현하는 관련 기능을 설명합니다. 여러 [장치 모델](#device-model)에서 인터페이스를 재사용할 수 있습니다. 인터페이스는 장치 모델에서 사용 되는 경우 장치의 [구성 요소](#component) 를 정의 합니다.

## <a name="iot-hub-query-language"></a>IoT Hub 쿼리 언어

IoT Hub 쿼리 언어는 여러 용도로 사용됩니다. 예를 들어, 언어를 사용하여 IoT Hub에 등록된 디바이스를 검색하거나 [디지털 쌍 라우팅](#digital-twin-route) 동작을 구체화할 수 있습니다.

## <a name="iot-plug-and-play-device"></a>IoT 플러그 앤 플레이 디바이스

IoT 플러그 앤 플레이 장치는 일반적으로 데이터를 수집 하거나 다른 장치를 제어 하 고 [장치 모델](#device-model)을 구현 하는 소프트웨어 또는 펌웨어를 실행 하는 소규모의 독립 실행형 컴퓨팅 장치입니다.  예를 들어, IoT 플러그 앤 플레이 디바이스는 환경 모니터링 디바이스이거나 스마트 농업 관개 시스템용 컨트롤러일 수 있습니다. IoT 플러그 앤 플레이 디바이스에서 명령을 하고 제어하고 데이터를 수신하기 위한 클라우드 호스티트 IoT 솔루션을 작성할 수 있습니다.

## <a name="iot-plug-and-play-conventions"></a>IoT 플러그 앤 플레이 규칙

IoT 플러그 앤 플레이 [장치](#iot-plug-and-play-device) 는 솔루션을 사용 하 여 데이터를 교환할 때 일련의 [규칙](concepts-convention.md) 을 따라야 합니다.

## <a name="model-id"></a>모델 ID

IoT 플러그 앤 플레이 장치가 IoT Hub에 연결 하는 경우 구현 하는 [Dtdl](#digital-twins-definition-language) 모델의 **모델 ID** 를 보냅니다. 이렇게 하면 솔루션에서 장치 모델을 찾을 수 있습니다.

## <a name="model-repository"></a>모델 리포지토리

[모델 리포지토리](concepts-model-repository.md) 는 [장치 모델](#device-model) 및 [인터페이스](#interface)를 저장 합니다.

## <a name="model-repository-rest-api"></a>Model repository REST API

모델 리포지토리를 관리 하 고 상호 작용 하기 위한 API입니다. 예를 들어 API를 사용 하 여 [장치 모델](#device-model)을 추가 하 고 검색할 수 있습니다.

## <a name="properties"></a>속성

속성은 디지털 쌍의 일부 상태를 나타내는 [인터페이스](#interface)에 정의된 데이터 필드입니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다. 일련 번호와 같은 읽기 전용 속성은 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device) 자체에서 실행되는 코드에 의해 설정됩니다.  경보 임계값과 같은 쓰기 가능한 속성은 일반적으로 클라우드 기반 IoT 솔루션에서 설정됩니다.

## <a name="shared-access-signature"></a>공유 액세스 서명

공유 액세스 서명은 SHA-256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. 공유 액세스 서명 인증에는 공유 액세스 정책과 공유 액세스 서명(토큰이라고 부름)의 두 구성 요소가 있습니다. [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)는 공유 액세스 서명을 사용하여 [IoT Hub](#azure-iot-hub)에서 인증을 받습니다.

## <a name="solution-builder"></a>솔루션 빌더

솔루션 빌더는 솔루션 백 엔드를 만듭니다. 솔루션 빌더는 일반적으로 [IoT Hub](#azure-iot-hub) 및 [모델 리포지토리와](#model-repository)같은 Azure 리소스와 함께 작동 합니다.

## <a name="telemetry"></a>원격 분석

[인터페이스](#interface)에 정의된 원격 분석 필드는 측정값을 나타냅니다. 이러한 측정값은 일반적으로 [IoT 플러그 앤 플레이 디바이스](#iot-plug-and-play-device)에서 데이터 스트림으로 보내는 센서 판독값과 같은 값입니다.
