---
title: 용어 설명-IoT 플러그 앤 플레이 미리 보기 | Microsoft Docs
description: 개념-IoT 플러그 앤 플레이 미리 보기와 관련 된 일반적인 용어의 용어입니다.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: ef5ce9cc1cda7f1ff6b1985771e20cb20123e264
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77025642"
---
# <a name="glossary-of-terms-for-iot-plug-and-play-preview"></a>IoT 플러그 앤 플레이 미리 보기용 용어 설명

IoT 플러그 앤 플레이 문서에서 사용 되는 일반적인 용어의 정의.

## <a name="azure-certified-for-iot-portal"></a>IoT용 Azure Certified 포털

[IoT용 Azure Certified 포털](https://aka.ms/ACFI) 웹 사이트를 사용 하 여 다음을 수행할 수 있습니다.

- [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device)에 대 한 [인증 프로세스](#device-certification) 를 완료 합니다.
- [장치 기능 모델](#device-capability-model)을 찾습니다.
- [공용 모델 리포지토리에](#public-model-repository)장치 기능 모델을 게시 합니다.

## <a name="azure-cli"></a>Azure CLI

Azure CLI은 Azure 리소스를 관리 하기 위한 플랫폼 간 명령줄 도구입니다. Azure CLI에 대 한 Azure IoT 확장은 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device)를 조작 하 고 테스트 하기 위한 명령줄 도구입니다. 확장을 사용 하 여 다음을 수행할 수 있습니다.

- IoT 플러그 앤 플레이 장치에 연결 합니다.
- 장치에서 보내는 [원격 분석](#telemetry) 을 봅니다.
- 장치 [속성](#properties)을 사용 합니다.
- 장치 [명령을](#commands)호출 합니다.
- [모델 리포지토리](#model-repository), [인터페이스](#interface)및 [장치 기능 모델](#device-capability-model)을 관리 합니다.

## <a name="azure-iot-central"></a>Azure IoT Central

Azure IoT Central는 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device)를 쉽게 연결, 모니터링 및 관리 하는 데 사용 되는 완전히 관리 되는 software as (software as a service) 솔루션입니다. [장치 기능 모델](#device-capability-model) 을 사용 하 여 IoT Central 응용 프로그램에서 장치를 모니터링 하 고 관리할 수 있도록 자동으로 구성할 수 있습니다.

## <a name="azure-iot-certification-service"></a>Azure IoT 인증 서비스

Azure IoT 인증 서비스는 [IoT용 Azure Certified 포털](#azure-certified-for-iot-portal)을 통해 인증을 위해 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 를 제출할 때 일련의 인증 테스트를 실행 합니다. 장치를 [IoT용 Certified 디바이스 카탈로그](#certified-for-iot-device-catalog)에 추가 하려면 먼저 장치를 인증 해야 합니다.

## <a name="azure-iot-tools-extension"></a>Azure IoT 도구 확장

Azure IoT 도구는 IoT Hub 상호 작용 하 고 IoT 장치를 개발 하는 데 도움이 되는 [Visual Studio code](#visual-studio-code) 의 확장 모음입니다. IoT 플러그 앤 플레이 장치 개발의 경우 다음 작업을 수행 하는 데 도움이 됩니다.

- [장치 기능 모델](#device-capability-model) 및 [인터페이스](#interface)를 작성 합니다.
- [모델 리포지토리에](#model-repository)게시 합니다.
- 장치 응용 프로그램을 구현 하는 기본 코드를 생성 합니다.

## <a name="azure-iot-explorer-tool"></a>Azure IoT 탐색기 도구

Azure IoT 탐색기는 [iot 플러그 앤 플레이 장치](#iot-plug-and-play-device)를 조작 하 고 테스트 하는 데 사용할 수 있는 그래픽 도구입니다. 로컬 컴퓨터에 도구를 설치한 후에이 도구를 사용 하 여 다음 작업을 수행할 수 있습니다.

- [IoT hub](#azure-iot-hub)에 연결 된 장치를 확인 합니다.
- IoT 플러그 앤 플레이 장치에 연결 합니다.
- 장치에서 보내는 [원격 분석](#telemetry) 을 봅니다.
- 장치 [속성](#properties)을 사용 합니다.
- 장치 [명령을](#commands)호출 합니다.

## <a name="azure-iot-hub"></a>Azure IoT Hub

IoT Hub는 클라우드에서 호스팅되는 관리 서비스이며, IoT 애플리케이션과 이를 통해 관리하는 디바이스 간의 양방향 통신을 위한 중앙 메시지 허브 역할을 합니다. [Iot 플러그 앤 플레이 장치](#iot-plug-and-play-device) 는 iot hub에 연결할 수 있습니다. IoT 솔루션은 IoT hub를 사용 하 여 다음을 사용 하도록 설정 합니다.

- 클라우드 기반 솔루션에 원격 분석을 전송 하는 장치입니다.
- 연결 된 장치를 관리 하는 클라우드 기반 솔루션입니다.

## <a name="azure-iot-device-sdk"></a>Azure IoT 디바이스 SDK

IoT 플러그 앤 플레이 장치 클라이언트 응용 프로그램을 빌드하는 데 사용할 수 있는 여러 언어에 대 한 장치 Sdk가 있습니다. [장치 인증](#device-certification) 에 대 한 요구 사항 중 하나는 장치 클라이언트 코드에서 Azure IoT 장치 sdk 중 하나를 사용 하는 것입니다.

## <a name="certified-for-iot-device-catalog"></a>IoT용 Certified 디바이스 카탈로그

[IoT용 Certified 디바이스 카탈로그](https://catalog.azureiotsolutions.com/) [장치 인증](#device-certification) 테스트를 통과 한 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 를 나열 합니다. 카탈로그의 IoT 플러그 앤 플레이 장치에 대 한 [장치 기능 모델](#device-capability-model) 및 공용 모델 리포지토리에 게시 됩니다.

## <a name="commands"></a>명령

[인터페이스](#interface) 에 정의 된 명령은 [디지털](#digital-twin)쌍에서 실행할 수 있는 메서드를 나타냅니다. 예를 들어 장치를 다시 부팅 하는 명령이 있습니다.

## <a name="common-interface"></a>공용 인터페이스

모든 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 는 몇 가지 공통 [인터페이스](#interface)를 구현 해야 합니다. 예를 들어 장치 정보 인터페이스는 장치에 대 한 하드웨어 및 운영 체제 정보를 정의 합니다. [장치 인증](#device-certification) 을 사용 하려면 장치에서 몇 가지 공통 인터페이스를 구현 해야 합니다. 공용 모델 리포지토리에서 일반적인 인터페이스 정의를 검색할 수 있습니다.

## <a name="company-model-repository"></a>회사 모델 리포지토리

조직에서는 회사 [모델 리포지토리](#model-repository) 를 [장치 기능 모델](#device-capability-model) 및 [인터페이스](#interface)에 대 한 개인 저장소로 사용할 수 있습니다.

## <a name="connection-string"></a>연결 문자열

연결 문자열은 엔드포인트에 연결 하는 데 필요한 정보를 캡슐화 합니다. 일반적으로 연결 문자열에는 엔드포인트의 주소와 보안 정보가 포함되지만 연결 문자열의 형식은 서비스 간에 다양합니다. IoT Hub 서비스와 연관된 연결 문자열에는 다음 두 종류가 있습니다.

- 장치 연결 문자열은 iot [플러그 앤 플레이 장치가](#iot-plug-and-play-device) iot hub의 장치 지향 엔드포인트에 연결 하는 데 사용 됩니다. 장치의 클라이언트 코드는 연결 문자열을 사용 하 여 IoT hub와의 보안 연결을 설정 합니다.
- 연결 문자열 IoT Hub 백 엔드 솔루션 및 도구를 사용 하 여 IoT Hub의 서비스 지향 엔드포인트에 안전 하 게 연결할 수 있습니다. 이러한 솔루션 및 도구는 IoT hub와 연결 된 장치를 관리 합니다.
- 회사 모델 리포지토리 연결 문자열을 사용 하면 백 엔드 솔루션과 도구를 사용 하 여 [회사 모델 리포지토리에](#company-model-repository)안전 하 게 연결할 수 있습니다. 이러한 솔루션 및 도구는 리포지토리에서 [장치 기능 모델](#device-capability-model) 및 [인터페이스](#interface) 를 사용 하거나 관리 합니다.

## <a name="device-capability-model"></a>디바이스 기능 모델

장치 기능 모델은 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 를 설명 하 고 장치에서 구현 하는 [인터페이스](#interface) 집합을 정의 합니다. 장치 기능 모델은 일반적으로 물리적 장치, 제품 또는 SKU에 해당 합니다. [디지털 쌍 정의 언어](#digital-twin-definition-language) 를 사용 하 여 장치 기능 모델을 정의 합니다.

## <a name="device-certification"></a>장치 인증

장치 인증은 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 를 인증 하 여 [IoT용 Certified 디바이스 카탈로그](#certified-for-iot-device-catalog) 에 추가 하 고 해당 [장치 기능 모델과](#device-capability-model) [인터페이스](#interface) 를 [공용 모델 리포지토리에](#public-model-repository)추가할 수 있도록 하는 프로세스입니다.

## <a name="device-developer"></a>디바이스 개발자

장치 개발자는 [장치 기능 모델](#device-capability-model), [인터페이스](#interface)및 [Azure iot 장치 SDK](#azure-iot-device-sdk) 를 사용 하 여 [iot 플러그 앤 플레이 장치](#iot-plug-and-play-device)에서 실행 되는 코드를 구현 합니다.

## <a name="device-modeling"></a>장치 모델링

[장치 개발자](#device-developer) 는 디지털 쌍 [정의 언어](#digital-twin-definition-language) 를 사용 하 여 [IoT 플러그 앤 플레이 장치의](#iot-plug-and-play-device)기능을 모델링 합니다. 모델 리포지토리를 사용 하 여 모델을 공유할 수 있습니다. 장치 개발자는 모델에서 기본 장치 코드를 생성할 수 있습니다. [솔루션 개발자](#solution-developer) 는 모델에서 IoT 솔루션을 구성할 수 있습니다.

## <a name="device-provisioning-service"></a>Device Provisioning Service

[Azure IoT Central](#azure-iot-central) 는 장치 프로 비전 서비스를 사용 하 여 모든 장치 등록 및 연결을 관리 합니다. 자세한 내용은 [Azure IoT Central에서 장치 연결](../iot-central/core/concepts-get-connected.md)을 참조 하세요. 장치 프로 비전 서비스를 사용 하 여 IoT Hub 기반 IoT 솔루션에 대 한 장치 등록 및 연결을 관리할 수도 있습니다. 자세한 내용은 [Azure IoT Hub 장치 프로 비전 서비스를 사용 하 여 장치 프로 비전](../iot-dps/about-iot-dps.md)을 참조 하세요.

## <a name="device-registration"></a>디바이스 등록

[Iot 플러그 앤 플레이 장치](#iot-plug-and-play-device) 를 iot 솔루션에 연결 하려면 먼저 솔루션에 등록 해야 합니다. [Azure IoT Central](#azure-iot-central) 는 장치 [프로 비전 서비스](#device-provisioning-service) 를 사용 하 여 장치 등록을 관리 합니다. 사용자 지정 IoT 솔루션에서는 Azure Portal 또는 프로그래밍 방식으로 IoT hub에 장치를 등록할 수 있습니다.

## <a name="device-first"></a>장치-우선

[Azure IoT Central](#azure-iot-central) 는 장치 우선 등록 및 연결 시나리오를 지원 합니다. 이 시나리오에서는 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 를 미리 등록 하지 않고도 IoT Central 응용 프로그램에 연결할 수 있습니다. 장치를 응용 프로그램에 처음 연결할 때 등록이 자동으로 수행 됩니다.

## <a name="digital-twin"></a>디지털 쌍

디지털 쌍은 [IoT 플러그 앤 플레이 장치의](#iot-plug-and-play-device)모델입니다. 디지털 쌍은 [디지털 쌍 정의 언어](#digital-twin-definition-language)를 사용 하 여 모델링 됩니다. [Azure IoT 장치 sdk](#azure-iot-device-sdk) 를 사용 하 여 런타임에 디지털 쌍과 상호 작용할 수 있습니다. 예를 들어 장치에서 디지털 쌍의 속성 값을 설정할 수 있으며 SDK는 클라우드의 IoT 솔루션에이 변경 내용을 전달 합니다.

## <a name="digital-twin-change-events"></a>디지털 쌍 변경 이벤트

[Iot 플러그 앤 플레이 장치가](#iot-plug-and-play-device) [iot hub](#azure-iot-hub)에 연결 된 경우 허브는 해당 라우팅 기능을 사용 하 여 디지털 쌍 변경 내용에 대 한 알림을 보낼 수 있습니다. 예를 들어, 장치에서 [속성](#properties) 값이 변경 될 때마다 Service Bus 큐와 같은 엔드포인트으로 알림을 보낼 수 IoT Hub.

## <a name="digital-twin-definition-language"></a>디지털 쌍 정의 언어

[IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device)에 대 한 모델 및 인터페이스를 설명 하는 언어입니다. 디지털 쌍 [정의 언어](https://aka.ms/DTDL) 를 사용 하 여 [디지털 쌍의](#digital-twin) 기능을 설명 하 고 iot 플랫폼과 iot 솔루션을 사용 하도록 설정 하 여 엔터티의 의미 체계를 활용할 수 있습니다.

## <a name="digital-twin-route"></a>디지털 쌍 경로

[IoT hub](#azure-iot-hub) 에 설정 된 경로를 통해 디지털 쌍 [변경 이벤트](#digital-twin-change-events) 와 엔드포인트 (예: Service Bus 큐)을 전달 합니다.

## <a name="interface"></a>인터페이스

인터페이스는 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 또는 [디지털](#digital-twin)쌍에 의해 구현 되는 관련 기능을 설명 합니다. 여러 [장치 기능 모델](#device-capability-model)에서 인터페이스를 재사용할 수 있습니다.

## <a name="iot-hub-query-language"></a>IoT Hub 쿼리 언어

IoT Hub 쿼리 언어는 여러 용도로 사용 됩니다. 예를 들어 언어를 사용 하 여 IoT hub에 [등록 된 장치](#device-registration) 를 검색 하거나 [디지털 쌍 라우팅](#digital-twin-route) 동작을 구체화할 수 있습니다.

## <a name="iot-plug-and-play-device"></a>IoT 플러그 앤 플레이 장치

IoT 플러그 앤 플레이 장치는 일반적으로 데이터를 수집 하거나 다른 장치를 제어 하 고 [장치 기능 모델](#device-capability-model)을 구현 하는 소프트웨어 또는 펌웨어를 실행 하는 소규모의 독립 실행형 컴퓨팅 장치입니다.  예를 들어 IoT 플러그 앤 플레이 장치는 환경 모니터링 장치 이거나 스마트 농업 관개 시스템용 컨트롤러 일 수 있습니다. 클라우드 호스팅 IoT 솔루션을 명령에 작성 하 고, 제어 하 고, IoT 플러그 앤 플레이 장치에서 데이터를 받을 수 있습니다. [IoT용 Azure Certified 디바이스 카탈로그](#certified-for-iot-device-catalog) 에는 사용 가능한 IoT 플러그 앤 플레이 장치가 나열 됩니다. 카탈로그의 각 IoT 플러그 앤 플레이 장치는 유효성을 검사 하 고 [장치 기능 모델](#device-capability-model)을 포함 합니다.

## <a name="microsoft-partner-center"></a>Microsoft 파트너 센터

[Microsoft 파트너 센터](https://docs.microsoft.com/partner-center/) 는 조직이 microsoft와 종단 간 관계를 관리 하는 곳입니다. [IoT용 Azure Certified 포털](#azure-certified-for-iot-portal)에서 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 를 인증 하려면 먼저 Microsoft 파트너 센터 계정이 필요 합니다.

## <a name="model-discovery"></a>모델 검색

[Iot 플러그 앤 플레이 장치가](#iot-plug-and-play-device) iot 솔루션에 연결 되 면 솔루션은 [장치 기능 모델](#device-capability-model)을 찾아 장치의 기능을 검색할 수 있습니다. 장치는 해당 기능 모델을 솔루션에 보내거나 솔루션은 [모델 리포지토리에서](#model-repository)장치 기능 모델을 찾을 수 있습니다.

## <a name="model-repository"></a>모델 리포지토리

모델 리포지토리는 [장치 기능 모델과](#device-capability-model) [인터페이스](#interface)를 저장 합니다. 단일 [공용 모델 리포지토리가](#public-model-repository)있습니다. 조직에서는 고유한 조직 모델 리포지토리를 만들 수 있습니다.

## <a name="model-repository-rest-api"></a>모델 리포지토리 REST API

모델 리포지토리를 관리 하 고 상호 작용 하기 위한 API입니다. 예를 들어 API를 사용 하 여 [장치 기능 모델](#device-capability-model) 을 추가 하 고 기능 모델을 검색할 수 있습니다.

## <a name="properties"></a>속성

속성은 디지털 쌍의 일부 상태를 나타내는 [인터페이스](#interface) 에 정의 된 데이터 필드입니다. 속성은 읽기 전용 또는 쓰기 가능으로 선언할 수 있습니다. 일련 번호와 같은 읽기 전용 속성은 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 자체에서 실행 되는 코드에 의해 설정 됩니다.  경보 임계값과 같은 쓰기 가능한 속성은 일반적으로 클라우드 기반 IoT 솔루션에서 설정 됩니다.

## <a name="public-model-repository"></a>공용 모델 리포지토리

[인증 된 장치](#device-certification)에 대 한 [장치 기능 모델](#device-capability-model) 및 [인터페이스](#interface) 를 저장 하는 단일 공용 모델 리포지토리가 있습니다. 공용 모델 리포지토리는 또한 [공통 인터페이스](#common-interface) 정의를 저장 합니다.

## <a name="registration-id"></a>등록 ID

등록 ID는 [장치 프로 비전 서비스](#device-provisioning-service)에서 장치를 고유 하 게 식별 합니다. 이 ID는 [IoT hub](#azure-iot-hub)의 장치에 대 한 고유 식별자 인 장치 id와 동일 하지 않습니다.

## <a name="scope-id"></a>범위 ID

범위 ID 범위는 [장치 프로 비전 서비스](#device-provisioning-service) 인스턴스를 고유 하 게 식별 합니다.

## <a name="shared-access-signature"></a>공유 액세스 서명

공유 액세스 서명은 SHA-256 보안 해시 또는 URI에 따른 인증 메커니즘입니다. 공유 액세스 서명 인증에는 공유 액세스 정책 및 공유 액세스 서명 (토큰이 라고도 함)의 두 가지 구성 요소가 있습니다. [Iot 플러그 앤 플레이 장치](#iot-plug-and-play-device) 는 공유 액세스 서명을 사용 하 여 [iot hub](#azure-iot-hub)를 인증 합니다.

## <a name="solution-developer"></a>솔루션 개발자

솔루션 개발자는 솔루션 백 엔드를 만듭니다. 솔루션 개발자는 일반적으로 [IoT Hub](#azure-iot-hub) 및 [모델 리포지토리와](#model-repository)같은 Azure 리소스를 사용 하거나 [IoT Central](#azure-iot-central)와 함께 작동 합니다.

## <a name="telemetry"></a>원격 분석

[인터페이스](#interface) 에 정의 된 원격 분석 필드는 측정값을 나타냅니다. 이러한 측정은 일반적으로 [IoT 플러그 앤 플레이 장치](#iot-plug-and-play-device) 에서 데이터 스트림으로 전송 되는 센서 판독값과 같은 값입니다.

## <a name="visual-studio-code"></a>Visual Studio code

Visual Studio code는 여러 플랫폼에서 사용할 수 있는 최신 코드 편집기입니다. [Azure IoT 도구](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) 팩의 확장과 같은 확장을 사용 하면 다양 한 개발 시나리오를 지원 하도록 편집기를 사용자 지정할 수 있습니다.
