---
title: IoT 플러그 앤 플레이 규칙 | Microsoft Docs
description: IoT 플러그 앤 플레이에서 원격 분석 및 속성을 보낼 때 사용할 장치를 필요로 하는 규칙에 대 한 설명 및 명령과 속성 업데이트를 처리 합니다.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: ef29be53e776c4c185ac8430b3340c53ca85d855
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88856049"
---
# <a name="iot-plug-and-play-conventions"></a>IoT 플러그 앤 플레이 규칙

IoT 플러그 앤 플레이 미리 보기 장치는 IoT hub와 메시지를 교환할 때 규칙 집합을 따라야 합니다. IoT 플러그 앤 플레이 미리 보기 장치는 MQTT 프로토콜을 사용 하 여 IoT Hub와 통신 합니다.

IoT 플러그 앤 플레이 장치에서 [DTDL (디지털 Twins Language v2)](https://github.com/Azure/opendigitaltwins-dtdl) _모델_을 사용 하 여 구현 하는 원격 분석, 속성 및 명령을 설명 합니다. 이 문서에서 참조 하는 모델에는 두 가지 유형이 있습니다.

- **구성 요소가** 없습니다. 구성 요소가 없는 모델입니다. 모델은 main 인터페이스의 내용 섹션에서 원격 분석, 속성 및 명령을 최상위 속성으로 선언 합니다.
- **여러 구성 요소** -두 개 이상의 인터페이스로 구성 된 모델입니다. 원격 분석, 속성 및 명령을 사용 하는 기본 인터페이스입니다. 추가 원격 분석, 속성 및 명령을 사용 하 여 구성 요소로 선언 된 하나 이상의 인터페이스입니다.

자세한 내용은 [모델의 IoT 플러그 앤 플레이 구성 요소](concepts-components.md)를 참조 하세요.

## <a name="identify-the-model"></a>모델 식별

구현 하는 모델을 알리기 위해 IoT 플러그 앤 플레이 장치에는 필드에를 추가 하 여 MQTT 연결 패킷에 모델 ID가 포함 됩니다 `model-id` `USERNAME` .

장치가 구현 하는 모델을 식별 하기 위해 서비스는 다음에서 모델 ID를 가져올 수 있습니다.

- 장치 쌍 `modelId` 필드입니다.
- 디지털 쌍 `$metadata.$model` 필드입니다.
- 디지털 쌍 변경 알림입니다.

## <a name="telemetry"></a>원격 분석

구성 요소 장치에서 전송 된 원격 분석에는 추가 메타 데이터가 필요 하지 않습니다. 시스템이 속성을 추가 합니다 `dt-dataschema` .

여러 구성 요소 장치에서 보낸 원격 분석은 `$.sub` 메시지 속성으로 추가 해야 합니다. 시스템에서 및 속성을 추가 합니다 `dt-subject` `dt-dataschema` .

## <a name="read-only-properties"></a>읽기 전용 속성

### <a name="sample-no-component-read-only-property"></a>샘플 구성 요소 읽기 전용 속성 없음

장치는 DTDL v2 규칙을 따르는 유효한 JSON을 보낼 수 있습니다.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **샘플 페이로드**

      ```json
      "reported" :
      {
        "temperature" : 21.3
      }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-read-only-property"></a>샘플 여러 구성 요소 읽기 전용 속성

`{"__t": "c"}`요소가 구성 요소를 참조 함을 나타내려면 장치에서 표식을 추가 해야 합니다.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "temperature",
          "schema": "double"
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **보고 된 속성**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "temperature": 21.3
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="writable-properties"></a>쓰기 가능 속성

장치는 보고 된 속성을 보내 속성을 받았는지 확인 해야 합니다. 보고 된 속성에는 다음이 포함 되어야 합니다.

- `value` -속성의 실제 값 (일반적으로 받은 값 이지만 장치는 다른 값을 보고 하도록 결정할 수 있음).
- `ac` -HTTP 상태 코드를 사용 하는 승인 코드입니다.
- `av` -desired 속성의를 참조 하는 승인 버전입니다 `$version` .
- `ad` -선택적 승인 설명입니다.

### <a name="sample-no-component-writable-property"></a>샘플 구성 요소 쓰기 가능 속성 없음

장치는 DTDL v2 규칙을 따르는 유효한 JSON을 보낼 수 있습니다.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:example: Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Desired 속성**

      ```json
      "desired" :
      {
        "targetTemperature" : 21.3
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **보고 된 속성**

      ```json
      "reported": {
        "targetTemperature": {
          "value": 21.3,
          "ac": 200,
          "av": 3,
          "ad": "complete"
       }
     }
      ```
   :::column-end:::
:::row-end:::

### <a name="sample-multiple-components-writable-property"></a>예제 여러 구성 요소 쓰기 가능 속성

`{"__t": "c"}`요소가 구성 요소를 참조 함을 나타내려면 장치에서 표식을 추가 해야 합니다.

마커는 구성 요소 수준 업데이트에 대해서만 전송 되므로 장치 되어서는 안됩니다이 플래그를 확인 합니다.

장치는 보고 된 속성을 보내 속성을 받았는지 확인 해야 합니다.

:::row:::
   :::column span="":::
      **DTDL**

      ```json
      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:TemperatureController;1",
      "@type": "Interface",
      "displayName": "Temperature Controller",
      "contents": [
        {
          "@type" : "Component",
          "schema": "dtmi:com:example:Thermostat;1",
          "name": "thermostat1"
        }
      ]

      "@context": "dtmi:dtdl:context;2",
      "@id": "dtmi:com:example:Thermostat;1",
      "@type": "Interface",
      "contents": [
        {
          "@type": "Property",
          "name": "targetTemperature",
          "schema": "double",
          "writable": true
        }
      ]
      ```
   :::column-end:::
   :::column span="":::
      **Desired 속성**

      ```json
      "desired": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": 21.3
        }
      },
      "$version" : 3
      ```
   :::column-end:::
   :::column span="":::
      **보고 된 속성**

      ```json
      "reported": {
        "thermostat1": {
          "__t": "c",
          "targetTemperature": {
            "value": 23,
            "ac": 200,
            "av": 3,
            "ad": "complete"
          }
        }
      }
      ```
   :::column-end:::
:::row-end:::

## <a name="commands"></a>명령

접두사 없이 명령 이름을 사용 하는 구성 요소 인터페이스가 없습니다.

장치에서 여러 구성 요소 인터페이스는 다음과 같은 형식으로 명령 이름을 사용 `componentName*commandName` 합니다.

## <a name="next-steps"></a>다음 단계

이제 IoT 플러그 앤 플레이 규칙에 대해 알아보았습니다. 몇 가지 추가 리소스는 다음과 같습니다.

- [DTDL(디지털 쌍 정의 언어)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C 디바이스 SDK](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/)
- [IoT REST API](https://docs.microsoft.com/rest/api/iothub/device)
- [모델 구성 요소](./concepts-components.md)
