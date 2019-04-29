---
title: Azure Portal에서 모듈 배포 - Azure IoT Edge | Microsoft Docs
description: Azure Portal을 사용하여 IoT Edge 디바이스에 모듈 배포
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.reviewer: menchi
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 9d7729dce5419c5813de3c4dfce55c40098f5988
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60595235"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Azure Portal에서 Azure IoT Edge 모듈 배포

비즈니스 논리를 사용하여 IoT Edge 모듈을 만들면 디바이스에 배포하여 에지에서 작동시킵니다. 데이터를 수집하고 처리하기 위해 다중 모듈을 사용한 경우 한 번에 모두 배포하고 여기에 연결된 회람 규칙을 선언할 수 있습니다.

이 아티클에서는 배포 매니페스트를 만들고 IoT Edge 디바이스에 배포를 푸시하는 방법을 Azure Portal에서 어떻게 설명하는지를 보여줍니다. 해당 공유 태그에 따라 다중 디바이스를 대상으로 지정하는 배포를 만드는 방법에 대한 정보는 [대규모 IoT Edge 모듈 배포 및 모니터링](how-to-deploy-monitor.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

* Azure 구독의 [IoT Hub](../iot-hub/iot-hub-create-through-portal.md)
* IoT Edge 런타임이 설치된 [IoT Edge 디바이스](how-to-register-device-portal.md)

## <a name="select-your-device"></a>디바이스 선택

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 메뉴에서 **IoT Edge**를 선택합니다.
1. 디바이스 목록에서 대상 디바이스의 ID를 클릭합니다.
1. **모듈 설정**을 선택합니다.

## <a name="configure-a-deployment-manifest"></a>배포 매니페스트 구성

배포 매니페스트는 배포할 모듈, 모듈 간의 데이터 흐름 및 모듈 쌍의 desired 속성을 설명하는 JSON 문서입니다. 배포 매니페스트의 작동 방식 및 생성 방법에 대한 자세한 내용은 [IoT Edge 모듈을 사용, 구성 및 다시 사용하는 방법에 대한 이해](module-composition.md)를 참조하세요.

Azure Portal에는 JSON 문서를 수동으로 빌드하지 않고 배포 매니페스트를 만드는 방법을 설명하는 마법사가 포함됩니다. 여기에는 **모듈 추가**, **경로 지정** 및 **배포 검토**의 세 가지 단계가 있습니다.

### <a name="add-modules"></a>모듈 추가

1. 페이지의 **레지스트리 설정** 섹션에서는 모듈 이미지를 포함하는 개인 컨테이너 레지스트리에 액세스할 수 있는 자격 증명을 제공합니다.

1. 페이지의 **배포 모듈** 섹션에서는 **추가**를 선택합니다.

1. 드롭다운 목록에서 모듈 형식을 살펴봅니다.

   * **IoT Edge 모듈** - 기본 옵션입니다.
   * **Azure Stream Analytics 모듈** - Azure Stream Analytics 워크로드에서 생성된 모듈만 해당합니다.
   * **Azure Machine Learning 모듈** - Azure Machine Learning 작업 영역에서 생성된 모델 이미지만 해당합니다.

1. **IoT Edge 모듈**을 선택합니다.

1. 모듈에 이름을 입력한 다음, 컨테이너 이미지를 지정합니다. 예를 들면 다음과 같습니다.

   * **이름** - tempSensor
   * **이미지 URI** - mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0

1. 필요한 경우 선택적 필드를 작성합니다. 컨테이너 생성 옵션, 다시 시작 정책 및 원하는 상태에 대한 자세한 내용은 [EdgeAgent desired 속성](module-edgeagent-edgehub.md#edgeagent-desired-properties)을 참조하세요. 모듈 쌍에 대한 자세한 내용은 [desired 속성 정의 또는 업데이트](module-composition.md#define-or-update-desired-properties)을 참조하세요.

1. **저장**을 선택합니다.

1. 2-6단계를 반복하여 배포에 모듈을 추가합니다.

1. **다음**을 선택하여 경로 섹션을 계속합니다.

### <a name="specify-routes"></a>경로 지정

기본적으로 마법사에서는 사용자에게 **route**라는 **FROM /\* INTO $upstream**으로 정의된 경로를 제공합니다. 즉, 모듈에 의한 메시지 출력은 IoT Hub에 전송됩니다.  

[경로 선언](module-composition.md#declare-routes)의 정보를 포함한 경로를 추가하거나 업데이트한 다음, **다음**을 선택하여 검토 섹션을 진행합니다.

### <a name="review-deployment"></a>배포 검토

검토 섹션에서는 이전 두 개의 섹션에서 선택한 항목에 따라 생성된 JSON 배포 매니페스트를 보여줍니다. 추가하지 않고 선언된 **$edgeAgent** 및 **$edgeHub**라는 두 개의 모듈이 있습니다. 이 두 개의 모듈은 [IoT Edge 런타임](iot-edge-runtime.md)을 구성하며 모든 배포에서 필수 기본값입니다.

배포 정보를 검토한 다음 **제출**을 선택합니다.

## <a name="view-modules-on-your-device"></a>디바이스에서 모듈 보기

디바이스에 모듈을 배포하면 포털의 **디바이스 세부 정보** 페이지에서 모두 볼 수 있습니다. 이 페이지에서는 배포 상태 및 종료 코드와 같은 유용한 정보뿐만 아니라 배포된 각 모듈의 이름을 표시합니다.

## <a name="deploy-modules-from-azure-marketplace"></a>Azure Marketplace에서 모듈 배포

Azure Marketplace는 [IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)을 포함하여 Azure에서 실행되도록 인증되고 최적화된 광범위한 엔터프라이즈 애플리케이션 및 솔루션을 검색할 수 있는 온라인 애플리케이션 및 서비스 마켓플레이스입니다. Azure Portal의 **리소스 만들기**에서도 Azure Marketplace에 액세스할 수 있습니다.

Azure Marketplace 또는 Azure Portal에서 IoT Edge 모듈을 설치할 수 있습니다.

1. 모듈을 찾고 배포 프로세스를 시작합니다.

   * Azure Portal: 모듈을 찾고 **만들기**를 선택합니다.

   * Azure Marketplace:

     1. 모듈을 찾고 **지금 가져오기**를 선택합니다.
     1. **계속**을 선택하여 공급자의 사용 약관 및 개인정보처리방침을 확인합니다.

1. 대상 디바이스가 연결된 구독 및 IoT Hub를 선택합니다.

1. **디바이스에 배포**를 선택합니다.

1. 디바이스 이름을 입력하거나 **디바이스 찾기**를 선택하여 허브에 등록된 디바이스 중에서 찾습니다.

1. 원하는 경우 다른 모듈 추가를 포함하여 배포 매니페스트를 구성하는 표준 프로세스를 계속하려면 **만들기**를 선택합니다. 이미지 URI, 만들기 옵션 및 desired 속성과 같은 새 모듈에 대한 세부 정보가 미리 정의되어 있지만 변경할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[대규모 IoT Edge 모듈을 배포 및 모니터링](how-to-deploy-monitor.md)하는 방법 알아보기
