---
title: Azure IoT Hub 디바이스 쌍 이해 | Microsoft Docs
description: 개발자 가이드 - 디바이스 쌍을 사용하여 IoT Hub와 디바이스 간의 상태 및 구성 데이터 동기화
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 883e81572218e39d84ad8793423b02468d49d00a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61322554"
---
# <a name="understand-and-use-device-twins-in-iot-hub"></a>IoT Hub의 디바이스 쌍 이해 및 사용

*디바이스 쌍*은 메타데이터, 상태 및 조건을 포함하는 디바이스의 상태 정보를 저장하는 JSON 문서입니다. Azure IoT Hub는 IoT Hub에 연결하는 각 디바이스에 대해 하나의 디바이스 쌍을 유지합니다. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

이 문서에서는 다음을 설명합니다.

* 디바이스 쌍의 구조: *태그*, *desired* 및 *reported 속성*
* 디바이스 앱과 백 엔드가 디바이스 쌍에 수행할 수 있는 작업

디바이스 쌍의 용도:

* 클라우드에서 디바이스 전용 메타데이터를 저장합니다. 예를 들어 자동 판매기의 배포 위치입니다.

* 디바이스 앱의 사용 가능한 기능 및 상태와 같은 현재 상태 정보를 보고합니다. 예를 들어 디바이스는 셀룰러 또는 WiFi를 통해 IoT Hub에 연결됩니다.

* 디바이스 앱과 백 엔드 앱 간에 장기 실행 워크플로의 상태를 동기화합니다. 예를 들어 솔루션 백 엔드가 설치할 새 펌웨어 버전을 지정하고 디바이스 앱이 다양한 업데이트 프로세스 단계를 보고할 경우입니다.

* 디바이스 메타데이터, 구성 또는 상태를 쿼리합니다.

reported 속성, 디바이스-클라우드 메시지 또는 파일 업로드를 사용하는 방법에 대한 지침은 [디바이스-클라우드 통신 지침](iot-hub-devguide-d2c-guidance.md)을 참조하세요.

desired 속성, 직접 메서드 또는 클라우드-장치 메시지를 사용하는 방법에 대한 지침은 [클라우드-장치 통신 지침](iot-hub-devguide-c2d-guidance.md)을 참조하세요.

## <a name="device-twins"></a>디바이스 쌍

디바이스 쌍이 저장하는 디바이스 관련 정보는:

* 디바이스 및 백 엔드가 디바이스 상태 및 구성 동기화에 사용할 수 있습니다.

* 솔루션 백 엔드는 오래 실행되는 작업을 쿼리하고 대상으로 하는 데 사용할 수 있습니다.

디바이스 쌍의 수명 주기는 해당하는 [디바이스 ID](iot-hub-devguide-identity-registry.md)에 연결됩니다. IoT Hub에서 디바이스 ID를 만들거나 삭제하면 디바이스 쌍이 암시적으로 만들어지거나 삭제됩니다.

디바이스 쌍은 JSON 문서이며 다음을 포함합니다.

* **태그**. 솔루션 백 엔드에서 읽고 쓸 수 있는 JSON 문서의 섹션입니다. 태그는 디바이스 앱에 표시되지 않습니다.

* **desired 속성**. reported 속성과 함께 디바이스 구성 또는 상황을 동기화하는 데 사용됩니다. 솔루션 백 엔드는 원하는 속성을 설정할 수 있으며 디바이스 앱에서 이를 읽을 수 있습니다. 디바이스 앱에서도 desired 속성의 변경 내용 알림을 수신할 수 있습니다.

* **reported 속성**. desired 속성과 함께 디바이스 구성 또는 상황을 동기화하는 데 사용됩니다. 디바이스 앱은 reported 속성을 설정할 수 있으며 솔루션 백 엔드는 이를 읽고 쿼리할 수 있습니다.

* **디바이스 ID 속성**. 디바이스 쌍 JSON 문서의 루트에는 [ID 레지스트리](iot-hub-devguide-identity-registry.md)에 포함된 해당 디바이스 ID의 읽기 전용 속성이 포함됩니다.

![디바이스 쌍 속성의 스크린샷](./media/iot-hub-devguide-device-twins/twin.png)

다음 예제에서는 디바이스 쌍 JSON 문서를 보여 줍니다.

```json
{
    "deviceId": "devA",
    "etag": "AAAAAAAAAAc=", 
    "status": "enabled",
    "statusReason": "provisioned",
    "statusUpdateTime": "0001-01-01T00:00:00",
    "connectionState": "connected",
    "lastActivityTime": "2015-02-30T16:24:48.789Z",
    "cloudToDeviceMessageCount": 0, 
    "authenticationType": "sas",
    "x509Thumbprint": {     
        "primaryThumbprint": null, 
        "secondaryThumbprint": null 
    }, 
    "version": 2, 
    "tags": {
        "$etag": "123",
        "deploymentLocation": {
            "building": "43",
            "floor": "1"
        }
    },
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata" : {...},
            "$version": 1
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": 55,
            "$metadata" : {...},
            "$version": 4
        }
    }
}
```

루트 개체에는 디바이스 ID 속성과 `tags`, `reported` 및 `desired` 속성의 컨테이너 개체가 있습니다. `properties` 컨테이너에는 [디바이스 쌍 메타데이터](iot-hub-devguide-device-twins.md#device-twin-metadata) 및 [낙관적 동시성](iot-hub-devguide-device-twins.md#optimistic-concurrency) 섹션에서 설명한 몇 가지 읽기 전용 요소(`$metadata`, `$etag` 및 `$version`)가 포함됩니다.

### <a name="reported-property-example"></a>reported 속성 예

이전 예제에서 디바이스 쌍에 `batteryLevel` 속성이 포함되어 있으며 이것은 디바이스 앱에 의해 보고됩니다. 이 속성은 마지막으로 보고된 배터리 수준에 기반하여 디바이스에서 쿼리 및 작업을 가능하게 합니다. 다른 예제에는 디바이스 앱 보고 디바이스 기능 또는 연결 옵션이 포함됩니다.

> [!NOTE]
> reported 속성은 솔루션 백 엔드가 마지막으로 알려진 속성 값에 관여하는 시나리오를 간소화합니다. 솔루션 백 엔드에서 타임스탬프가 적용된 이벤트 시퀀스(예: 시계열)의 형태로 디바이스 원격 분석을 처리해야 하는 경우 [디바이스-클라우드 메시지](iot-hub-devguide-messages-d2c.md)를 사용합니다.

### <a name="desired-property-example"></a>desired 속성 예제

이전 예제에서 `telemetryConfig` 디바이스 쌍 desired 및 reported 속성은 솔루션 백 엔드 및 디바이스 앱에서 이 디바이스의 원격 분석 구성을 동기화하는 데 사용됩니다. 예를 들면 다음과 같습니다.

1. 솔루션 백 엔드는 desired 구성 값으로 desired 속성을 설정합니다. 다음은 desired 속성 집합이 포함된 문서의 일부분입니다.

   ```json
   "desired": {
       "telemetryConfig": {
           "sendFrequency": "5m"
       },
       ...
   },
   ```

2. 디바이스 앱이 연결되거나 처음으로 다시 연결되면 변경 내용이 즉시 통보됩니다. 그런 다음 디바이스 앱은 업데이트된 구성(또는 `status` 속성을 사용한 오류 상태)을 보고합니다. 다음은 reported 속성의 일부분입니다.

   ```json
   "reported": {
       "telemetryConfig": {
           "sendFrequency": "5m",
           "status": "success"
       }
       ...
   }
   ```

3. 솔루션 백 엔드는 디바이스 쌍을 [쿼리](iot-hub-devguide-query-language.md)하여 많은 디바이스의 구성 작업 결과를 추적할 수 있습니다.

> [!NOTE]
> 이전 코드 조각은 디바이스 구성 및 상태를 인코딩하는 한 가지 방식을 가독성을 위해 최적화해 놓은 예입니다. IoT Hub는 디바이스 쌍의 desired 및 reported 속성에 대해 특정 스키마를 적용하지 않습니다.
> 

쌍을 사용하여 펌웨어 업데이트와 같이 오래 실행되는 작업을 동기화할 수 있습니다. 속성을 사용하여 디바이스에서 장기 실행 작업을 동기화하고 추적하는 방법에 대한 자세한 내용은 [desired 속성을 사용하여 디바이스 구성](tutorial-device-twins.md)을 참조하세요.

## <a name="back-end-operations"></a>백 엔드 작업

솔루션 백 엔드는 HTTPS를 통해 공개되는 다음과 같은 원자성 작업을 사용하여 디바이스 쌍에서 작동합니다.

* **ID로 디바이스 쌍 검색** 이 작업은 태그, desired 및 reported 시스템 속성을 포함하여 디바이스 쌍 문서를 반환합니다.

* **디바이스 쌍 부분 업데이트**. 이 작업은 솔루션 백 엔드에서 디바이스 쌍의 태그 또는 desired 속성을 부분적으로 업데이트할 수 있도록 합니다. 부분 업데이트는 속성을 추가하거나 업데이트하는 JSON 문서로 표현됩니다. `null`로 설정된 속성은 제거됩니다. 다음 예제에서는 값이 `{"newProperty": "newValue"}`인 desired 속성을 새로 생성하고, `existingProperty`의 기존 값을 `"otherNewValue"`로 덮어쓰고, `otherOldProperty`를 제거합니다. 기존 desired 속성이나 태그에는 변화가 발생하지 않습니다.

   ```json
   {
       "properties": {
           "desired": {
               "newProperty": {
                   "nestedProperty": "newValue"
               },
               "existingProperty": "otherNewValue",
               "otherOldProperty": null
           }
       }
   }
   ```

* **desired 속성 바꾸기**. 솔루션 백 엔드에서 기존의 모든 desired 속성을 완전히 덮어쓰고 `properties/desired`에 대해 새 JSON 문서를 대체할 수 있는 작업입니다.

* **태그 바꾸기**. 이 작업을 사용하여 솔루션 백 엔드에서 기존의 모든 태그를 완전히 덮어쓰고 `tags`에 대해 새 JSON 문서를 대체할 수 있습니다.

* **쌍 알림을 받습니다**. 이 작업을 통해 쌍이 수정될 때 솔루션 백 엔드는 알림을 받습니다. 이를 수행하려면 IoT 솔루션은 경로를 만들고 데이터 원본을 *twinChangeEvents*와 동일하게 설정해야 합니다. 기본적으로 이러한 경로가 미리 존재하지 않습니다. 따라서 쌍 알림이 전송되지 않습니다. 변경 속도가 너무 높은 경우 또는 내부 오류와 같은 다른 이유로 IoT Hub는 모든 변경 내용을 포함하는 하나의 알림만을 보낼 수 있습니다. 따라서 애플리케이션에 신뢰할 수 있는 감사 및 모든 중간 상태의 로깅이 필요한 경우 장치-클라우드 메시지를 사용하는 것이 좋습니다. 쌍 알림 메시지는 속성 및 본문을 포함합니다.

  - properties

    | 이름 | 값 |
    | --- | --- |
    $content-type | application/json |
    $iothub-enqueuedtime |  알림이 전송된 시간 |
    $iothub-message-source | twinChangeEvents |
    $content-encoding | utf-8 |
    deviceId | 디바이스의 ID |
    hubName | IoT Hub의 이름 |
    operationTimestamp | [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) 작업의 타임스탬프 |
    iothub-message-schema | deviceLifecycleNotification |
    opType | "replaceTwin" 또는 "updateTwin" |

    메시지 시스템 속성 앞에 `$` 기호를 붙입니다.

  - 본문
        
    이 섹션은 JSON 형식으로 모든 쌍 변경 내용을 포함합니다. 모든 쌍 섹션: 태그, properties.reported, properties.desired를 포함할 수 있으며 "$metadata" 요소를 포함한다는 차이점으로 패치와 동일한 형식을 사용합니다. 예를 들면 다음과 같습니다.

    ```json
    {
      "properties": {
          "desired": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          },
          "reported": {
              "$metadata": {
                  "$lastUpdated": "2016-02-30T16:24:48.789Z"
              },
              "$version": 1
          }
      }
    }
    ```

이전의 모든 작업은 [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)에서 정의된 대로 [낙관적 동시성](iot-hub-devguide-device-twins.md#optimistic-concurrency)을 지원하며 **ServiceConnect** 사용 권한이 필요합니다.

이러한 작업 외에도 솔루션 백 엔드는 다음을 수행할 수 있습니다.

* SQL 스타일 [IoT Hub 쿼리 언어](iot-hub-devguide-query-language.md)를 사용하여 디바이스 쌍을 쿼리합니다.

* [작업](iot-hub-devguide-jobs.md)을 사용하여 디바이스 쌍의 큰 집합에서 작업을 수행합니다.

## <a name="device-operations"></a>디바이스 작업

디바이스 앱은 다음과 같은 원자성 작업을 사용하여 디바이스 쌍에서 작동합니다.

* **디바이스 쌍 검색** 이 작업은 현재 연결된 디바이스의 태그, desired 및 reported 시스템 속성을 포함하는 디바이스 쌍 문서를 반환합니다.

* **reported 속성 부분 업데이트**. 현재 연결된 디바이스의 reported 속성을 부분적으로 업데이트할 수 있는 작업입니다. 이 작업은 desired 속성의 부분 업데이트를 사용하는 솔루션 백 엔드와 동일한 JSON 업데이트 형식을 사용합니다.

* **desired 속성 관찰**. 현재 연결된 디바이스는 desired 속성에 대한 업데이트가 발생하면 알림을 받도록 선택할 수 있습니다. 디바이스는 솔루션 백 엔드에 의해 실행된 것과 같은 형태의 업데이트(부분 또는 전체 바꾸기)를 수신합니다.

이전의 모든 작업에는 [IoT Hub에 대한 액세스 제어](iot-hub-devguide-security.md)에서 정의한 대로 **DeviceConnect** 사용 권한이 필요합니다.

[Azure IoT 디바이스 SDK](iot-hub-devguide-sdks.md)를 사용하면 이전 작업을 다양한 언어와 플랫폼에서 손쉽게 사용할 수 있습니다. desired 속성 동기화를 위한 IoT Hub 기본 형식에 대한 자세한 내용은 [디바이스 다시 연결 흐름](iot-hub-devguide-device-twins.md#device-reconnection-flow)을 참조하세요.

## <a name="tags-and-properties-format"></a>태그 및 속성 형식

태그, desired 속성 및 reported 속성은 JSON 개체이며 다음과 같은 제한 사항이 있습니다.

* JSON 개체의 모든 키는 대/소문자를 구분하는 64바이트 UTF-8 유니코드 문자열입니다. 허용되는 문자에서 유니코드 제어 문자(세그먼트 C0 및 C1) 및 `.`, `$` 및 SP는 제외됩니다.

* JSON 개체의 모든 값은 부울, 숫자, 문자열, 개체와 같은 JSON 형식이 될 수 있습니다. 배열은 허용되지 않습니다. 정수에 대한 최대값은 4503599627370495이며 정수에 대한 최소값은 4503599627370496입니다.

* tags, desired 속성 및reported 속성의 모든 JSON 개체는 최대 5의 깊이를 가질 수 있습니다. 예를 들어 다음 개체는 유효합니다.

   ```json
   {
       ...
       "tags": {
           "one": {
               "two": {
                   "three": {
                       "four": {
                           "five": {
                               "property": "value"
                           }
                       }
                   }
               }
           }
       },
       ...
   }
   ```

* 모든 문자열 값의 길이는 최대 512바이트입니다.

## <a name="device-twin-size"></a>디바이스 쌍 크기

IoT Hub는 `tags`, `properties/desired` 및 `properties/reported`의 총 값 각각에 8KB의 크기 제한을 적용합니다. 읽기 전용 요소는 제외됩니다.

크기는 문자열 상수 외부의 유니코드 제어 문자(세그먼트 C0 및 C1) 및 공백을 제외한 모든 문자의 개수를 세어서 계산됩니다.

IoT Hub는 한도 이상으로 해당 문서의 크기를 증가시키는 모든 작업을 오류와 함께 거부합니다.

## <a name="device-twin-metadata"></a>디바이스 쌍 메타데이터

IoT Hub는 디바이스 쌍 desired 또는 reported 속성의 각 JSON 개체에 대한 마지막 업데이트의 타임스탬프를 유지합니다. 타임스탬프는 UTC 형식이며 [ISO8601](https://en.wikipedia.org/wiki/ISO_8601) 형식 `YYYY-MM-DDTHH:MM:SS.mmmZ`로 인코딩됩니다.

예를 들면 다음과 같습니다.

```json
{
    ...
    "properties": {
        "desired": {
            "telemetryConfig": {
                "sendFrequency": "5m"
            },
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": {
                        "$lastUpdated": "2016-03-30T16:24:48.789Z"
                    },
                    "$lastUpdated": "2016-03-30T16:24:48.789Z"
                },
                "$lastUpdated": "2016-03-30T16:24:48.789Z"
            },
            "$version": 23
        },
        "reported": {
            "telemetryConfig": {
                "sendFrequency": "5m",
                "status": "success"
            },
            "batteryLevel": "55%",
            "$metadata": {
                "telemetryConfig": {
                    "sendFrequency": "5m",
                    "status": {
                        "$lastUpdated": "2016-03-31T16:35:48.789Z"
                    },
                    "$lastUpdated": "2016-03-31T16:35:48.789Z"
                },
                "batteryLevel": {
                    "$lastUpdated": "2016-04-01T16:35:48.789Z"
                },
                "$lastUpdated": "2016-04-01T16:24:48.789Z"
            },
            "$version": 123
        }
    }
    ...
}
```

이 정보는 개체 키를 제거하는 업데이트를 유지하기 위해서 모든 수준(JSON 구조의 수준뿐만 아니라)에서 유지됩니다.

## <a name="optimistic-concurrency"></a>낙관적 동시성

태그, desired 및 reported 속성은 모두 낙관적 동시성을 지원합니다.
[RFC7232](https://tools.ietf.org/html/rfc7232)에 따르면 태그에는 ETag가 있고 이것은 태그의 JSON 표현을 나타냅니다. 일관성을 보장하기 위해 솔루션 백 엔드에서 조건부 업데이트 작업에 ETag를 사용할 수 있습니다.

디바이스 쌍 desired 및 reported 속성에는 ETag가 없지만 증분될 수 있는 `$version` 값이 있습니다. ETag와 마찬가지로 버전은 업데이트의 일관성을 유지하는 파티를 업데이트하여 사용할 수 있습니다. 예를 들어 reported 속성에 대한 디바이스 앱이나 desired 속성에 대한 솔루션 백 엔드입니다.

버전은 관찰 에이전트(예: desired 속성을 관찰하는 디바이스 앱)가 업데이트 알림과 검색 작업의 결과 간에 속도를 중재해야 하는 경우에도 유용합니다. [디바이스 다시 연결 흐름 섹션](iot-hub-devguide-device-twins.md#device-reconnection-flow)에는 자세한 정보가 있습니다.

## <a name="device-reconnection-flow"></a>디바이스 다시 연결 흐름

IoT Hub는 연결되지 않은 디바이스에 대한 desired 속성 업데이트 알림을 유지하지 않습니다. 연결 중인 디바이스는 완전한 desired 속성 문서를 가져와야 하고 또한 업데이트 알림을 구독해야 합니다. 업데이트 알림과 전체 검색이 서로 경쟁할 가능성이 있으므로 다음과 같은 흐름을 따라야 합니다.

1. 디바이스 앱을 IoT hub에 연결합니다.
2. 디바이스 앱이 desired 속성 업데이트 알림을 구독합니다.
3. 디바이스 앱이 desired 속성에 대한 전체 문서를 검색합니다.

디바이스 앱은 검색이 완료된 문서의 버전보다 작거나 같은 `$version`이 포함된 모든 알림을 무시할 수 있습니다. 이 방법은 IoT Hub가 버전이 항상 증가한다는 것을 보장하기 때문에 가능합니다.

> [!NOTE]
> 이 논리는 이미 [Azure IoT 디바이스 SDK](iot-hub-devguide-sdks.md)에 구현되어 있습니다. 이 설명은 디바이스 앱이 Azure IoT 디바이스 SDK를 아무것도 사용할 수 없고 MQTT 인터페이스를 직접 프로그래밍해야 하는 경우에만 유용합니다.
> 

## <a name="additional-reference-material"></a>추가 참조 자료

이 IoT Hub 개발자 가이드의 다른 참조 자료:

* [IoT Hub 엔드포인트](iot-hub-devguide-endpoints.md) 문서에서는 각 IoT Hub가 런타임 및 관리 작업에 노출하는 다양한 엔드포인트를 설명합니다.

* [제한 및 할당량](iot-hub-devguide-quotas-throttling.md) 문서에서는 IoT Hub 서비스에 적용되는 할당량 및 서비스를 사용할 때 예상되는 제한 동작에 대해 설명합니다.

* [Azure IoT 디바이스 및 서비스 SDK](iot-hub-devguide-sdks.md) 문서에서는 IoT Hub와 상호 작용하는 디바이스 및 서비스 앱 모두를 개발할 때 사용할 수 있는 다양한 언어 SDK를 나열합니다.

* [디바이스 쌍, 작업 및 메시지 라우팅을 위한 IoT Hub 쿼리 언어](iot-hub-devguide-query-language.md) 문서에서는 IoT Hub에서 디바이스 쌍 및 작업에 대한 정보를 검색하는 데 사용할 수 있는 IoT Hub 쿼리 언어에 대해 설명합니다.

* [IoT Hub MQTT 지원](iot-hub-mqtt-support.md) 문서에서는 MQTT 프로토콜에 대한 IoT Hub 지원에 대해 자세히 설명합니다.

## <a name="next-steps"></a>다음 단계

디바이스 쌍에 대해 알아봤으니 다음 IoT Hub 개발자 가이드 항목에 대해서 살펴보세요.

* [IoT Hub에서 모듈 쌍 이해 및 사용](iot-hub-devguide-module-twins.md)
* [디바이스에서 직접 메서드 호출](iot-hub-devguide-direct-methods.md)
* [여러 디바이스에서 작업 예약](iot-hub-devguide-jobs.md)

이 문서에서 설명한 일부 개념을 시도해 보려면 다음과 같은 IoT Hub 자습서를 참조하세요.

* [디바이스 쌍을 사용하는 방법](iot-hub-node-node-twin-getstarted.md)
* [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
* [VS Code용 Azure IoT Tools를 사용한 디바이스 관리](iot-hub-device-management-iot-toolkit.md)
