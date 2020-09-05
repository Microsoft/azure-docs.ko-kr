---
title: 새로운 기능은 무엇입니까? IoT 플러그 앤 플레이 미리 보기 새로 고침 | Microsoft Docs
description: IoT 플러그 앤 플레이 미리 보기 새로 고침 릴리스의 새 기능에 대해 알아봅니다.
author: rido-min
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: eliotgra
ms.openlocfilehash: c415ffdaa2eb3ad6a76cd48c3a895b6618dd3986
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208054"
---
# <a name="iot-plug-and-play-preview-refresh"></a>IoT 플러그 앤 플레이 미리 보기 새로 고침

이 문서에서는 2020년 7월 IoT 플러그 앤 플레이 미리 보기 새로 고침 릴리스에 포함된 SDK, 라이브러리, 도구 및 서비스의 주요 변경 내용에 대해 설명합니다. 이전 IoT 플러그 앤 플레이 미리 보기 릴리스는 2019년 8월에 출시되었습니다.

## <a name="digital-twins-definition-language-dtdl"></a>DTDL(디지털 쌍 정의 언어)

이 릴리스에서는 [DTDL v2](https://github.com/Azure/opendigitaltwins-dtdl) 지원이 추가되고 [DTDL v1](https://github.com/Azure/opendigitaltwins-dtdl/tree/master/DTDL/v1-preview)이 사용 중단됩니다.

다음 목록은 DTDL v1과 DTDL v2의 주요 차이점을 보여줍니다. DTDL v2:

- 모델 ID에 `urn` 대신 `dtmi` 접두사가 사용됩니다. DTDL v1에서 사용하던 `urn` 접두사가 붙은 디지털 쌍 ID가 DTMI(디지털 쌍 모델 식별자)로 대체됩니다. 이제 버전 앞에 `;`이 붙습니다. 예를 들어 이전에는 `urn:CompanyName:Model:1`을 사용했지만, 지금은 `dtmi:CompanyName:Model;1`을 사용합니다.
- `@context`를 `http://azureiot.com/v1/contexts/IoTModel.json` 대신 `dtmi:dtdl:context;2`로 설정합니다.
- **CapabilityModel** 형식 대신 **Interface** 형식을 사용하여 디바이스를 모델링합니다.
- **구성 요소**가 **인터페이스** 인스턴스를 대체합니다. **인터페이스** 콘텐츠의 일부로 **관계** 및 **구성 요소**를 정의할 수 있습니다.
- **인터페이스**는 다른 **인터페이스에서** 상속할 수 있습니다.
- _확장 가능한 의미 형식_을 사용하여 DTDL을 확장할 수 있습니다. 이 확장 가능한 형식 시스템은 DTDL v1의 온도 및 습도와 같은 하드 코딩된 의미 체계 형식보다 훨씬 유연합니다.
- **displayUnit** 속성이 제거되었습니다.
- 이름에 선행 또는 후행 밑줄을 사용할 수 없습니다. 이름의 선행 밑줄은 시스템에 사용하도록 예약되어 있습니다.

DTDL v1으로 작업하려면 이전 버전의 SDK 및 Azure IoT Explorer 0.10.x를 사용해야 합니다. DTDL v2로 작업하려면 최신 버전의 SDK 및 Azure IoT Explorer 0.11.x가 필요합니다.

## <a name="no-component-and-multiple-component-implementations"></a>구성 요소 및 다중 구성 요소 구현 없음

몇 가지 원격 분석, 명령 또는 속성을 사용하는 간단한 디바이스는 구성 요소를 사용하지 않고 단일 인터페이스로 설명할 수 있습니다. 기존 디바이스 구현을 변경하지 않고 **모델 ID**을 발표하여 기존 디바이스를 IoT 플러그 앤 플레이로 만들 수 있습니다.

복잡한 디바이스는 여러 인터페이스에서 원격 분석, 명령 및 속성을 그룹화하여 복잡성을 관리하고 모든 디바이스에서 재사용할 수 있습니다. 이러한 디바이스는 [IoT 플러그 앤 플레이 미리 보기 메시지 규칙](concepts-convention.md)에 정의된 간단한 규칙 세트를 따르도록 업데이트해야 합니다.

## <a name="registration-and-discovery"></a>등록 및 검색

이 릴리스에서 디바이스는 연결할 때마다 IoT Hub를 통해 **모델 ID**를 알립니다. IoT Hub는 **모델 ID**를 캐시하여 백 엔드 솔루션이 디바이스 쌍 `modelId` 속성을 사용하여 **모델 ID**를 검색할 수 있도록 합니다. 디지털 쌍의 `$metadata.$model`에서 **모델 ID**를 검색할 수도 있습니다.

## <a name="microsoft-defined-interfaces"></a>Microsoft에서 정의한 인터페이스

Microsoft에서 정의한 다음 인터페이스는 더 이상 사용되지 않으며 새 모델 리포지토리에 게시되지 않습니다.

- **urn:azureiot:ModelDiscovery:DigitalTwin:1**
- **urn:azureiot:ModelDiscovery:ModelInformation:1**

URL [https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview](https://repo.azureiotrepository.com/Models/dtmi:azure:DeviceManagement:DeviceInformation;1?api-version=2020-05-01-preview)에서 사용할 수 있는 `dtmi:azure:DeviceManagement:DeviceInformation;1` 인터페이스는 새 모델 리포지토리에 게시됩니다.

## <a name="digitaltwinchangeevents"></a>DigitalTwinChangeEvents

**DigitalTwinChangeEvents** [이벤트 원본](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events)의 이벤트 구조가 **JSON 패치** 형식을 사용하도록 변경되었습니다. 이 변경은 이전 버전과 호환되지 않는 호환성이 손상되는 변경입니다.

## <a name="message-routing"></a>메시지 라우팅

원격 분석 메시지가 [SystemProperties](../iot-hub/iot-hub-devguide-messages-construct.md) 컬렉션에서 다음과 같이 변경됩니다.

이제 디바이스에서 등록한 **모델 ID**인 **dt-dataschema** 속성을 포함합니다.

**dt-subject** 속성은 원격 분석 메시지를 전송하는 구성 요소를 나타냅니다.

**iothub-interface-name** 속성은 더 이상 사용되지 않습니다.

## <a name="device-and-service-sdks"></a>디바이스 및 서비스 SDK

이전 미리 보기 버전의 SDK와 호환되지 않습니다. 최신 미리 보기 버전의 SDK로 전환하는 경우 코드를 변경해야 합니다.

규칙 기반 접근 방식을 사용하는 경우 별도의 디바이스 클라이언트 SDK가 필요 없습니다. 이 미리 보기 릴리스에서는 기존 **DigitalTwinClient** 라이브러리가 모든 언어에서 더 이상 사용되지 않습니다. 그 대신, **모델 ID**를 알리는 옵션을 포함하도록 IoT Hub 디바이스 클라이언트 SDK가 업데이트되었습니다.

구성 요소를 사용하지 않는 디바이스의 경우 최소한의 코드 변경만(**모델 ID** 발표만) 필요합니다. 여러 구성 요소를 사용하는 복잡한 디바이스의 경우 [규칙](concepts-convention.md)을 구현하는 데 다시 사용할 수 있는 기능이 필요할 수 있습니다. 디바이스 샘플에는 디바이스 구현에 다시 사용할 수 있는 함수 세트가 포함되어 있습니다.

### <a name="service-sdks"></a>서비스 SDK

서비스 SDK는 [Node.js](https://github.com/Azure/azure-iot-sdk-node/blob/digitaltwins-preview/digitaltwins/service/readme.md) 및 [Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/azure-iot-hub/README.md)에서 사용할 수 있습니다.

## <a name="vs-code-extension"></a>VS Code 확장

[Azure IoT Device Workbench](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-iot-workbench) 확장은 DTDL v1에 대한 작성 지원, 이전 버전 모델 리포지토리와의 통합 및 코드 생성 기능을 제공합니다.

VS Code에서 DTDL v2 작성 지원이 필요한 경우 VS Code에 새 [DTDL 확장](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl)을 설치합니다. 이 확장은 모델 리포지토리와의 통합 또는 코드 생성 기능을 제공하지 않습니다. 이제 [웹 UI](https://aka.ms/iotmodelrepo) 또는 [CLI](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot/pnp?view=azure-cli-latest)를 사용하여 리포지토리의 모델을 관리할 수 있습니다.

## <a name="digital-twin-service-side-rest-apis"></a>디지털 쌍 서비스 측 REST API

[디지털 쌍 서비스 쪽 REST API](https://docs.microsoft.com/rest/api/iothub/service/digitaltwin) 및 페이로드가 변경되었습니다. 지원되는 API는 다음과 같습니다.

- 디지털 쌍을 검색합니다.
- 명령을 호출합니다.
- **JSON 패치** 페이로드를 사용하여 디지털 쌍을 업데이트합니다.

기존 REST API는 이 릴리스에서 계속 지원됩니다.

## <a name="model-repository"></a>모델 리포지토리

이제 게시된 퍼블릭 모델과 RBAC로 보호된 프라이빗 회사 모델을 모두 포함하는 단일 모델 리포지토리가 있습니다. 모든 모델에는 고유 식별자가 있으며 만든 후에는 변경할 수 없습니다.

이전 릴리스의 기존 회사 모델 리포지토리는 이 릴리스에서 지원되지 않습니다. [IoT용 Azure Certified](https://preview.catalog.azureiotsolutions.com/products) 웹 사이트를 계속 사용하여 이전 DTDL v1 모델을 관리할 수 있습니다. 그러나 더 이상 이 웹 사이트를 사용하여 디바이스를 등록, 테스트 및 인증할 수 없습니다.

## <a name="azure-iot-central"></a>Azure IoT Central

현재 Azure IoT Central은 IoT 플러그 앤 플레이 미리 보기 새로 고침 릴리스를 지원하도록 업데이트 중입니다.
