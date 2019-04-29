---
title: 배포 매니페스트로 모듈 및 경로 선언 - Azure IoT Edge | Microsoft Docs
description: 배포 매니페스트에서 배포할 모듈을 선언하는 방법, 배포하는 방법 및 이들 간에 메시지 경로를 만드는 방법을 알아봅니다.
author: kgremban
manager: philmea
ms.author: v-yiso
origin.date: 03/28/2019
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: f4a562cab445398986c1b8f379f6cb90ca843342
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363207"
---
# <a name="learn-how-to-deploy-modules-and-establish-routes-in-iot-edge"></a>IoT Edge에서 모듈을 배포하고 경로를 설정하는 방법 알아보기

각 IoT Edge 디바이스는 적어도 $edgeAgent 및 $edgeHub라는 두 개의 모듈을 실행합니다. 두 모듈은 IoT Edge 런타임의 일부입니다. 또한 IoT Edge 디바이스는 여러 모듈을 실행하여 개수에 관계 없이 프로세스를 수행할 수 있습니다. 이 모듈을 모두 한 번에 디바이스에 배포하므로, IoT Edge는 설치할 모듈 및 함께 작동하도록 구성하는 방법을 선언하는 옵션을 제공합니다. 

*배포 매니페스트*는 다음 항목을 설명하는 JSON 문서입니다.

* 각 모듈에 대한 컨테이너 이미지, 개인 컨테이너 레지스트리에 액세스하기 위한 자격 증명 및 각 모듈을 만들고 관리하는 방법에 대한 지침을 포함하는 **IoT Edge 에이전트** 모듈 쌍입니다.
* 모듈 간 및 궁극적으로 IoT Hub에 대한 메시지 흐름 방법을 포함하는 **IoT Edge 허브** 모듈 쌍입니다.
* 경우에 따라 추가 모듈 쌍의 원하는 속성입니다.

모든 IoT Edge 디바이스는 배포 매니페스트로 구성해야 합니다. 새로 설치된 IoT Edge 런타임은 유효한 매니페스트로 구성될 때까지 오류 코드를 보고합니다. 

Azure IoT Edge 자습서에서는 Azure IoT Edge 포털의 마법사를 통해 배포 매니페스트를 빌드합니다. REST 또는 IoT Hub 서비스 SDK를 사용하여 프로그래밍 방식으로 배포 매니페스트를 적용할 수도 있습니다. 자세한 내용은 [IoT Edge 배포 이해](module-deployment-monitoring.md)를 참조하세요.

## <a name="create-a-deployment-manifest"></a>배포 매니페스트 만들기

상위 수준에서 배포 매니페스트는 원하는 속성으로 구성된 모듈 쌍의 목록입니다. 배포 매니페스트는 설치할 모듈과 구성 방법을 IoT Edge 디바이스(또는 디바이스 그룹)에 알려 줍니다. 배포 매니페스트에는 각 모듈 쌍의 ‘원하는 속성’이 포함되어 있습니다. IoT Edge 디바이스는 각 모듈에 대해 ‘보고된 속성’을 다시 보고합니다. 

모든 배포 매니페스트에는 `$edgeAgent` 및 `$edgeHub`라는 두 개의 모듈이 필요합니다. 두 모듈은 IoT Edge 디바이스와 이 디바이스에서 실행되는 모듈을 관리하는 IoT Edge 런타임의 일부입니다. 이러한 모듈에 대한 자세한 내용은 [IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

두 개의 런타임 모듈 외에도 IoT Edge 디바이스에서 실행할 고유한 모듈을 최대 20개까지 추가할 수 있습니다. 

IoT Edge 런타임(edgeAgent 및 edgeHub)만 포함하는 배포 매니페스트가 유효합니다.

배포 매니페스트는 다음 구조를 따릅니다.

```json
{
    "modulesContent": {
        "$edgeAgent": { // required
            "properties.desired": {
                // desired properties of the Edge agent
                // includes the image URIs of all modules
                // includes container registry credentials
            }
        },
        "$edgeHub": { //required
            "properties.desired": {
                // desired properties of the Edge hub
                // includes the routing information between modules, and to IoT Hub
            }
        },
        "module1": {  // optional
            "properties.desired": {
                // desired properties of module1
            }
        },
        "module2": {  // optional
            "properties.desired": {
                // desired properties of module2
            }
        },
        ...
    }
}
```

## <a name="configure-modules"></a>모듈 구성

IoT Edge 런타임에서 사용자 배포에 모듈을 설치하는 방법을 정의합니다. IoT Edge 에이전트는 IoT Edge 디바이스에 대한 설치, 업데이트 및 상태 보고를 관리하는 런타임 구성 요소입니다. 따라서 $edgeAgent 모듈 쌍에는 모든 모듈에 대한 구성 및 관리 정보가 필요합니다. 이 정보는 IoT Edge 에이전트 자체에 대 한 구성 매개 변수를 포함합니다. 

수 있거나 포함 되어야 하는 속성의 전체 목록은 참조 하세요 [IoT Edge 에이전트 및 IoT Edge 허브의 속성](module-edgeagent-edgehub.md)합니다.

$edgeAgent 속성은 다음과 같은 구조를 따릅니다.

```json
"$edgeAgent": {
    "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
            "settings":{
                "registryCredentials":{ // give the edge agent access to container images that aren't public
                    }
                }
            }
        },
        "systemModules": {
            "edgeAgent": {
                // configuration and management details
            },
            "edgeHub": {
                // configuration and management details
            }
        },
        "modules": {
            "module1": { // optional
                // configuration and management details
            },
            "module2": { // optional
                // configuration and management details
            }
        }
    }
},
```

## <a name="declare-routes"></a>경로 선언

IoT Edge 허브는 모듈, IoT Hub 및 리프 디바이스 간의 통신을 관리합니다. 따라서 $edgeHub 모듈 쌍에는 메시지가 배포 내에서 전달되는 방식을 선언하는 ‘경로’라는 원하는 속성이 포함되어 있습니다. 동일한 배포 내에 여러 경로가 있을 수 있습니다.

경로는 다음과 같은 구문을 사용하여 **$edgeHub** 원하는 속성에 선언됩니다.

```json
"$edgeHub": {
    "properties.desired": {
        "routes": {
            "route1": "FROM <source> WHERE <condition> INTO <sink>",
            "route2": "FROM <source> WHERE <condition> INTO <sink>"
        },
    }
}
```

모든 경로에는 원본 및 싱크가 필요하지만 조건은 메시지를 필터링하는 데 사용할 수 있는 선택적 요소입니다. 


### <a name="source"></a>원본

원본은 메시지가 발생한 위치를 지정합니다. IoT Edge는 리프 디바이스 또는 모듈의 메시지를 라우팅할 수 있습니다.

원본 속성은 다음 값 중 하나일 수 있습니다.

| 원본 | 설명 |
| ------ | ----------- |
| `/*` | 모든 모듈 또는 리프 디바이스의 모든 디바이스-클라우드 메시지 또는 쌍 변경 알림 |
| `/twinChangeNotifications` | 모든 모듈 또는 리프 디바이스에서 발생하는 모든 쌍 변경(보고된 속성) |
| `/messages/*` | 일부 출력을 통하거나 어떠한 출력도 없이 모듈 또는 리프 디바이스에서 보낸 모든 디바이스-클라우드 메시지 |
| `/messages/modules/*` | 일부 출력을 통하거나 어떠한 출력도 없이 모듈에서 보낸 모든 디바이스-클라우드 메시지 |
| `/messages/modules/<moduleId>/*` | 일부 출력을 통하거나 어떠한 출력도 없이 특정 모듈에서 보낸 모든 디바이스-클라우드 메시지 |
| `/messages/modules/<moduleId>/outputs/*` | 일부 출력을 통해 특정 모듈에서 보낸 모든 디바이스-클라우드 메시지 |
| `/messages/modules/<moduleId>/outputs/<output>` | 특정 출력을 통해 특정 모듈에서 보낸 모든 디바이스-클라우드 메시지 |

### <a name="condition"></a>조건
조건은 경로 선언의 선택 사항입니다. 싱크에서 원본으로 모든 메시지를 전달하려는 경우 전체 **WHERE** 절을 그대로 둡니다. 또는 [IoT Hub 쿼리 언어](../iot-hub/iot-hub-devguide-routing-query-syntax.md)를 사용하여 조건을 만족하는 특정 메시지 또는 메시지 유형에 대해 필터링할 수 있습니다. IoT Edge 경로는 쌍 태그 또는 속성을 기반으로 하는 메시지 필터링을 지원하지 않습니다. 

IoT Edge의 모듈 간에 전달되는 메시지는 디바이스와 Azure IoT Hub 간에 전달되는 메시지와 동일한 서식이 지정됩니다. 모든 메시지는 JSON으로 서식이 지정되고 **systemProperties**, **appProperties** 및 **body** 매개 변수를 포함합니다. 

다음 구문을 사용하여 3개 매개 변수 중 하나를 중심으로 쿼리를 작성할 수 있습니다. 

* 시스템 속성: `$<propertyName>` 또는 `{$<propertyName>}`
* 애플리케이션 속성: `<propertyName>`
* 본문 속성: `$body.<propertyName>` 

메시지 속성에 대한 쿼리를 만드는 방법에 대한 예제는 [디바이스-클라우드 메시지 경로에 대한 쿼리 식](../iot-hub/iot-hub-devguide-routing-query-syntax.md)을 참조하세요.

IoT Edge에 특정되는 예제는 리프 디바이스에서 게이트웨이 디바이스에 도착하는 메시지를 필터링하려는 경우입니다. 모듈에서 발생한 메시지에는 **connectionModuleId**라는 시스템 속성이 포함되어 있습니다. 따라서 리프 디바이스에서 IoT Hub로 직접 메시지를 라우팅하려는 경우 다음 경로를 사용하여 모듈 메시지를 제외합니다.

```query
FROM /messages/* WHERE NOT IS_DEFINED($connectionModuleId) INTO $upstream
```

### <a name="sink"></a>싱크
싱크는 메시지를 보낼 위치를 정의합니다. 모듈 및 IoT Hub로만 메시지를 보낼 수 있으며, 다른 디바이스로는 라우팅할 수 없습니다. 싱크 속성에는 와일드카드 옵션이 없습니다. 

싱크 속성은 다음과 같습니다.

| 싱크 | 설명 |
| ---- | ----------- |
| `$upstream` | IoT Hub로 메시지 전송 |
| `BrokeredEndpoint("/modules/<moduleId>/inputs/<input>")` | 특정 모듈의 특정 입력으로 메시지 전송 |

IoT Edge는 최소 한 번의 보장을 제공합니다. IoT Edge 허브는 라우트가 메시지를 싱크로 전달할 수 없는 경우 메시지를 로컬에 저장합니다. 예를 들어, IoT Edge 허브가 IoT 허브에 연결할 수 없거나 대상 모듈이 연결되어 있지 않은 경우입니다.

IoT Edge 허브는 [IoT Edge 허브 선호 속성](module-edgeagent-edgehub.md) 중 `storeAndForwardConfiguration.timeToLiveSecs` 속성에 지정된 기간 동안 메시지를 저장합니다.

## <a name="define-or-update-desired-properties"></a>원하는 속성 정의 또는 업데이트 

배포 매니페스트는 IoT Edge 디바이스에 배포된 각 모듈의 원하는 속성을 지정합니다. 배포 매니페스트의 원하는 속성은 현재 모듈 쌍에 있는 원하는 속성을 모두 덮어씁니다.

배포 매니페스트에서 모듈 쌍의 원하는 속성을 지정하지 않으면, IoT Hub는 어떤 방식으로든 모듈 쌍을 수정하지 않습니다. 대신, 원하는 속성을 프로그래밍 방식으로 설정할 수 있습니다.

디바이스 쌍을 수정할 수 있게 해주는 동일한 메커니즘이 모듈 쌍을 수정하는 데 사용됩니다. 자세한 내용은 [모듈 쌍 개발자 가이드](../iot-hub/iot-hub-devguide-module-twins.md)를 참조하세요.   

## <a name="deployment-manifest-example"></a>배포 매니페스트 예제

다음 예제에서는 유효한 배포 매니페스트 문서의 모양을 보여 줍니다.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "ContosoRegistry": {
                "username": "myacr",
                "password": "<password>",
                "address": "myacr.azurecr.io"
              }
            }
          }
        },
        "systemModules": {
          "edgeAgent": {
            "type": "docker",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
              "createOptions": ""
            }
          },
          "edgeHub": {
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
              "createOptions": ""
            }
          }
        },
        "modules": {
          "tempSensor": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": "{}"
            }
          },
          "filtermodule": {
            "version": "1.0",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "myacr.azurecr.io/filtermodule:latest",
              "createOptions": "{}"
            }
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "sensorToFilter": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/filtermodule/inputs/input1\")",
          "filterToIoTHub": "FROM /messages/modules/filtermodule/outputs/output1 INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 10
        }
      }
    }
  }
}
```

## <a name="next-steps"></a>다음 단계

* 수 있거나 $edgeAgent $edgeHub에 포함 되어야 하는 속성의 전체 목록은 참조 하세요 [IoT Edge 에이전트 및 IoT Edge 허브의 속성](module-edgeagent-edgehub.md)합니다.

* 이제 IoT Edge 모듈을 사용하는 방법을 알았으므로 [IoT Edge 모듈 개발을 위한 요구 사항 및 도구에 대해 알아봅니다](module-development.md).

[lnk-deploy]: module-deployment-monitoring.md
[lnk-iothub-query]: ../iot-hub/iot-hub-devguide-routing-query-syntax.md
[lnk-docker-create-options]: https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate
[lnk-docker-logging-options]: https://docs.docker.com/engine/admin/logging/overview/
[lnk-module-dev]: module-development.md
