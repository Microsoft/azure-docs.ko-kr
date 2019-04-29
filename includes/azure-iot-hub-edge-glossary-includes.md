---
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 11/09/2018
ms.author: dobett
ms.openlocfilehash: c95bca125ea70cf32acad0d5ea67c3ad195ed704
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60445074"
---
## <a name="automatic-device-management"></a>자동 디바이스 관리
Azure IoT Hub에서 자동 디바이스 관리는 전체 수명 주기를 통해 대규모 디바이스를 관리하는 반복적이고 복잡한 작업을 자동화합니다. 자동 디바이스 관리를 사용하여 해당 속성을 기반으로 디바이스 집합을 대상으로 지정하고, 원하는 구성을 정의하고, 범위에 나올 때마다 IoT Hub에서 디바이스를 업데이트하도록 할 수 있습니다.  [자동 디바이스 구성](../articles/iot-hub/iot-hub-auto-device-config.md) 및 [IoT Edge 자동 배포](../articles/iot-edge/how-to-deploy-monitor.md)로 구성됩니다.

## <a name="iot-edge"></a>IoT Edge
Azure IoT Edge에서는 클라우드 방식을 통해 Azure 서비스 및 솔루션별 코드를 온-프레미스 디바이스에 배포할 수 있습니다. IoT Edge 디바이스는 다른 디바이스의 데이터를 집계한 후 데이터를 클라우드로 전송하기 전에 계산 및 분석을 수행할 수 있습니다. 자세한 내용은 [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/)를 참조하세요.

## <a name="iot-edge-agent"></a>IoT Edge 에이전트
모듈 배포 및 모니터링을 담당하는 IoT Edge 런타임의 부분입니다.

## <a name="iot-edge-device"></a>IoT Edge 디바이스
IoT Edge 디바이스에는 IoT Edge 런타임이 설치되어 있고 디바이스 세부 정보에 **IoT Edge 디바이스**로 플래그가 지정되어 있습니다. [Linux에서 시뮬레이션트된 디바이스에 Azure IoT Edge 배포 - 미리 보기](https://docs.microsoft.com/azure/iot-edge/tutorial-simulate-device-linux) 방법을 알아봅니다.

## <a name="iot-edge-automatic-deployment"></a>IoT Edge 자동 배포
IoT Edge 자동 배포는 IoT Edge 디바이스의 대상 집합이 IoT Edge 모듈 집합을 실행하도록 구성합니다. 각 배포는 새 디바이스가 만들어지거나 대상 조건과 일치하도록 수정되더라도 대상 조건과 일치하는 모든 디바이스가 지정된 모듈 집합을 실행 중인지 계속 확인합니다. 각 IoT Edge 디바이스는 해당 대상 조건을 충족하는 가장 높은 우선 순위 배포만 수신합니다. [IoT Edge 자동 배포](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring)에 대해 자세히 알아보세요.

## <a name="iot-edge-deployment-manifest"></a>IoT Edge 배포 매니페스트
모듈, 경로 및 관련된 모듈의 원하는 속성 집합을 배포할 하나 이상의 IoT Edge 디바이스 쌍에 복사될 정보를 포함하는 Json 문서입니다.

## <a name="iot-edge-gateway-device"></a>IoT Edge 게이트웨이 디바이스
다운스트림 디바이스가 있는 IoT Edge 디바이스입니다. 다운스트림 디바이스는 IoT Edge 디바이스일 수도 있고 그렇지 않을 수도 있습니다.

## <a name="iot-edge-hub"></a>IoT Edge 허브
모듈 간 통신, 업스트림(IoT Hub 쪽) 및 다운스트림(IoT Hub 반대쪽) 통신을 담당하는 IoT Edge 런타임의 일부입니다. 

## <a name="iot-edge-leaf-device"></a>IoT Edge 리프 디바이스
다운스트림 디바이스가 없는 IoT Edge 디바이스입니다. 

## <a name="iot-edge-module"></a>IoT Edge 모듈
IoT Edge 모듈은 IoT Edge 디바이스에 배포할 수 있는 Docker 컨테이너입니다. 디바이스에서 메시지 수집, 메시지 변환 또는 IoT Hub로 메시지 보내기와 같은 특정 작업을 수행합니다. 다른 모듈와 통신하고 IoT Edge 런타임에 데이터를 보냅니다. [IoT Edge 모듈을 개발하기 위한 요구 사항 및 도구 이해](https://docs.microsoft.com/azure/iot-edge/module-development)

## <a name="iot-edge-module-identity"></a>IoT Edge 모듈 ID
모듈에서 Edge Hub 또는 IoT Hub로부터 인증을 받는 데 사용되는 보안 자격 증명 및 존재 여부를 자세히 설명하는 IoT Hub 모듈 ID 레지스트리의 레코드입니다.

## <a name="iot-edge-module-image"></a>IoT Edge 모듈 이미지
IoT Edge 런타임에서 모듈 인스턴스를 인스턴스화하는 데 사용하는 docker 이미지입니다.

## <a name="iot-edge-module-twin"></a>IoT Edge 모듈 쌍
IoT Hub에 유지되며 Json 문서 모듈 인스턴스에 대한 상태 정보를 저장하는 Json 문서입니다.

## <a name="iot-edge-priority"></a>IoT Edge 우선 순위
두 개의 IoT Edge 배포가 같은 디바이스를 대상으로 하는 경우 우선 순위가 더 높은 배포가 적용됩니다. 두 배포의 우선 순위가 동일한 경우 만든 날짜가 나중인 배포가 적용됩니다. [우선 순위](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#priority)에 대해 자세히 알아보세요.

## <a name="iot-edge-runtime"></a>IoT Edge 런타임
IoT Edge 런타임은 Microsoft가 IoT Edge 디바이스에 설치되도록 배포하는 모든 항목입니다. 여기에는 Edge 에이전트, Edge 허브 및 IoT Edge 보안 디먼이 포함됩니다.

## <a name="iot-edge-set-modules-to-a-single-device"></a>단일 디바이스에 대한 IoT Edge 집합 모듈
IoT Edge 매니페스트의 내용을 한 디바이스의 모듈 쌍으로 복사하는 작업입니다. 기본 API는 제네릭 '구성 적용'으로, 단순히 IoT Edge 매니페스트를 입력으로 사용합니다.

## <a name="iot-edge-target-condition"></a>IoT Edge 대상 조건
IoT Edge 배포에서 대상 조건은 배포의 대상 디바이스를 선택하기 위한 디바이스 쌍 태그의 부울 조건입니다(예: **tag.environment = prod**). 대상 조건은 요구 사항을 충족하는 새 디바이스를 포함하거나 더 이상 요구 사항을 충족하지 않는 디바이스를 제거하기 위해 지속적으로 평가됩니다. [대상 조건](https://docs.microsoft.com/azure/iot-edge/module-deployment-monitoring#target-condition)에 대해 자세히 알아보세요.