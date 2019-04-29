---
title: Azure Portal에서 자동 배포 만들기 - Azure IoT Edge | Microsoft Docs
description: Azure Portal을 사용하여 IoT Edge 디바이스 그룹에 대한 자동 배포 만들기
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 02/19/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 69ba0a882c0e52e7c0d063b8f77e7a0fe22526a1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126367"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-portal"></a>Azure Portal을 사용하여 대규모 IoT Edge 모듈 배포 및 모니터링

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

Azure IoT Edge를 사용하면 분석을 에지로 이동할 수 있고 클라우드 인터페이스를 제공하므로, 각 디바이스에 물리적으로 액세스하지 않고도 IoT Edge 디바이스를 관리하고 모니터링할 수 있습니다. IoT(사물 인터넷) 솔루션이 점점 더 커지고 복잡하게 됨에 따라 디바이스를 원격으로 관리하는 기능이 점점 더 중요해지고 있습니다. Azure IoT Edge는 추가하는 디바이스의 수와 관계없이 비즈니스 목표를 지원하도록 설계되었습니다.

개별 디바이스를 관리하고 모듈을 한 번에 하나씩 배포할 수 있습니다. 그러나 대대적으로 디바이스를 변경하려는 경우 IoT Hub에서 자동 디바이스 관리의 일부인 **IoT Edge 자동 배포**를 만들 수 있습니다. 배포는 여러 모듈을 여러 디바이스에 한 번에 배포하고, 모듈의 상태를 추적하며, 필요한 경우 변경할 수 있게 하는 동적 프로세스입니다. 

## <a name="identify-devices-using-tags"></a>태그를 사용하여 디바이스 식별

배포를 만들려면 먼저 적용할 디바이스를 지정할 수 있어야 합니다. Azure IoT Edge는 디바이스 쌍의 **태그**를 사용하여 디바이스를 식별합니다. 각 디바이스는 여러 개의 태그를 갖출 수 있으며 솔루션에 적합한 방식으로 이러한 태그를 정의할 수 있습니다. 예를 들어 스마트 건물의 캠퍼스를 관리하는 경우 디바이스에 다음 태그를 추가할 수 있습니다.

```json
"tags":{
    "location":{
        "building": "20",
        "floor": "2"
    },
    "roomtype": "conference",
    "environment": "prod"
}
```

디바이스 쌍 및 태그에 대한 자세한 내용은 [IoT Hub의 디바이스 쌍 이해 및 사용](../iot-hub/iot-hub-devguide-device-twins.md)을 참조하세요.

## <a name="create-a-deployment"></a>배포 만들기

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다. 
1. **IoT Edge**를 선택합니다.
1. **IoT Edge 배포 추가**를 선택합니다.

배포를 만드는 데에는 5개 단계가 있습니다. 다음 섹션에서는 각 단계로 안내합니다. 

### <a name="step-1-name-and-label"></a>1단계: 이름 및 레이블

1. 배포에 최대 128자의 소문자로 된 고유한 이름을 지정합니다. 공백과 잘못된 문자(`& ^ [ ] { } \ | " < > /`)는 사용하지 않도록 합니다.
1. 배포 추적에 도움이 되도록 레이블을 키-값 쌍으로 추가할 수 있습니다. 예를 들어 **HostPlatform** 및 **Linux** 또는 **Version** 및 **3.0.1**입니다.
1. **다음**을 선택하여 2단계로 이동합니다. 

### <a name="step-2-add-modules-optional"></a>2단계: 모듈 추가(선택 사항)

배포에 추가할 수 있는 두 가지 유형의 모듈이 있습니다. 첫 번째는 스토리지 계정 또는 Stream Analytics와 같은 Azure 서비스를 기반으로 하는 모듈입니다. 두 번째는 사용자 고유의 코드를 사용하는 모듈입니다. 두 가지 유형의 여러 모듈을 하나의 배포에 추가할 수 있습니다. 

모듈 없이 배포를 만드는 경우 디바이스에서 모든 현재 모듈이 제거됩니다. 

>[!NOTE]
>Azure Functions는 아직 자동화된 Azure 서비스 배포를 지원하지 않습니다. 사용자 지정 모듈 배포를 사용하여 해당 서비스를 배포에 수동으로 추가합니다. 

Azure Stream Analytics에서 모듈을 추가하려면 다음 단계를 수행합니다.

1. 페이지의 **배포 모듈** 섹션에서 **추가**를 클릭합니다.
1. **Azure Stream Analytics 모듈**을 선택합니다.
1. 드롭다운 메뉴에서 **구독**을 선택합니다.
1. 드롭다운 메뉴에서 **Edge 작업**을 선택합니다.
1. **저장**을 선택하여 모듈을 배포에 추가합니다. 

사용자 지정 코드를 모듈로 추가하거나 Azure 서비스 모듈을 수동으로 추가하려면 다음 단계를 수행합니다.

1. 페이지의 **컨테이너 레지스트리 설정** 섹션에서 이 배포에 대한 모듈 이미지를 포함하는 개인 컨테이너 레지스트리에 대한 이름 및 자격 증명을 제공합니다. Edge 에이전트는 Docker 이미지에 대한 컨테이너 레지스트리 자격 증명을 찾을 수 없는 경우 오류 500을 보고합니다.
1. 페이지의 **배포 모듈** 섹션에서 **추가**를 클릭합니다.
1. **IoT Edge 모듈**을 선택합니다.
1. 모듈에 **이름**을 지정합니다.
1. **이미지 URI** 필드에 대해 모듈의 컨테이너 이미지를 입력합니다. 
1. 컨테이너에 전달되어야 하는 **컨테이너 만들기 옵션**을 지정합니다. 자세한 내용은 [docker create](https://docs.docker.com/engine/reference/commandline/create/)를 참조하세요.
1. 드롭다운 메뉴를 사용하여 **다시 시작 정책**을 선택합니다. 다음 옵션 중에서 선택합니다. 
   * **Always(항상 다시 시작)** - 모듈이 어떤 이유로든 종료되면 항상 다시 시작됩니다.
   * **Never(다시 시작 안 함)** - 모듈이 어떤 이유로든 종료되면 다시 시작되지 않습니다.
   * **On-failed(실패)** - 모듈이 충돌하면 다시 시작되지만 완전히 종료되지는 않습니다. 
   * **On-unhealthy(비정상)** - 모듈이 충돌하거나 비정상 상태를 반환하면 다시 시작됩니다. 상태 기능을 구현하는 것은 각 모듈에 달려 있습니다. 
1. 드롭다운 메뉴를 사용하여 모듈에 대한 **원하는 상태**를 선택합니다. 다음 옵션 중에서 선택합니다.
   * **실행 중** - 기본 옵션입니다. 모듈이 배포된 직후에 실행됩니다.
   * **중지됨** - 모듈이 배포된 후에 사용자 또는 다른 모듈에서 시작하도록 호출할 때까지 유휴 상태를 유지합니다.
1. 모듈 쌍에 태그 또는 기타 속성을 추가하려는 경우 **모듈 쌍의 원하는 속성 설정**을 선택합니다.
1. 이 모듈에 대한 **환경 변수**를 입력합니다. 환경 변수는 모듈에 구성 프로세스를 용이하게 하는 추가 정보를 제공합니다.
1. **저장**을 선택하여 모듈을 배포에 추가합니다. 

배포에 대해 모든 모듈을 구성했으면 **다음**을 선택하여 3단계로 이동합니다.

### <a name="step-3-specify-routes-optional"></a>3단계: 경로 지정(선택 사항)

경로는 배포 내 모듈 간에 서로 통신하는 방식을 정의합니다. 기본적으로 마법사에서는 사용자에게 **route**라는 **FROM /\* INTO $upstream**으로 정의된 경로를 제공합니다. 즉, 모듈에 의한 메시지 출력은 IoT Hub에 전송됩니다.  

[경로 선언](module-composition.md#declare-routes)의 정보를 포함한 경로를 추가하거나 업데이트한 다음, **다음**을 선택하여 검토 섹션을 진행합니다.

### <a name="step-4-specify-metrics-optional"></a>4단계: 메트릭 지정(선택 사항)

메트릭은 디바이스가 구성 콘텐츠를 적용한 결과로 다시 보고할 수 있는 다양한 상태의 요약 수를 제공합니다.

1. **메트릭 이름**에 대한 이름을 입력합니다.

1. **메트릭 조건**에 대한 쿼리를 입력합니다. 쿼리는 IoT Edge 허브 모듈 쌍의 [보고된 속성](module-edgeagent-edgehub.md#edgehub-reported-properties)을 기반으로 합니다. 메트릭은 쿼리에 의해 반환되는 행 수를 나타냅니다.

예를 들면 다음과 같습니다.

```sql
SELECT deviceId FROM devices
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="step-5-target-devices"></a>5단계: 대상 디바이스

디바이스의 tags 속성을 사용하여 이 배포를 받아야 하는 특정 디바이스를 대상으로 지정합니다. 

여러 배포에서 동일한 디바이스를 대상으로 할 수 있으므로 각 배포에 우선 순위 번호를 부여해야 합니다. 충돌하는 경우 우선 순위가 가장 높은 배포가 먼저 적용됩니다(높은 값은 높은 우선 순위를 나타냄). 두 배포의 우선 순위 번호가 동일하면 가장 최근에 만든 배포가 먼저 적용됩니다. 

1. 배포 **우선 순위**에 대해 양의 정수를 입력합니다. 둘 이상의 배포가 동일한 디바이스를 대상으로 하는 경우, Priority의 숫자 값이 가장 큰 배포가 적용됩니다.
1. **대상 조건**을 입력하여 이 배포의 대상으로 지정할 디바이스를 결정합니다. 조건은 디바이스 쌍 태그 또는 보고되는 디바이스 쌍 속성을 기반으로 하며, 표현 형식이 일치해야 합니다. 예를 들면 `tags.environment='test'` 또는 `properties.reported.devicemodel='4000x'`과 같습니다. 
1. **다음**을 선택하여 최종 단계로 이동합니다.

### <a name="step-6-review-deployment"></a>6단계: 배포 검토

배포 정보를 검토한 다음 **제출**을 선택합니다.

## <a name="deploy-modules-from-azure-marketplace"></a>Azure Marketplace에서 모듈 배포

Azure Marketplace는 [IoT Edge 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules)을 포함하여 Azure에서 실행되도록 인증되고 최적화된 광범위한 엔터프라이즈 애플리케이션 및 솔루션을 검색할 수 있는 온라인 애플리케이션 및 서비스 마켓플레이스입니다. Azure Portal의 **리소스 만들기**에서도 Azure Marketplace에 액세스할 수 있습니다.

Azure Marketplace 또는 Azure Portal에서 IoT Edge 모듈을 설치할 수 있습니다.

1. 모듈을 찾고 배포 프로세스를 시작합니다.

   * Azure Portal: 모듈을 찾고 **만들기**를 선택합니다.

   * Azure Marketplace:

     1. 모듈을 찾고 **지금 가져오기**를 선택합니다.
     1. **계속**을 선택하여 공급자의 사용 약관 및 개인정보처리방침을 확인합니다.

1. 사용자 구독 및 대상 디바이스가 연결된 IoT Hub를 선택합니다.

1. **대규모 배포**를 선택합니다.

1. 새 배포에 모듈을 추가할지, 기존 배포의 복제본에 추가할지 선택합니다. 복제하는 경우 목록에서 기존 배포를 선택합니다.

1. **만들기**를 선택하여 대규모 배포를 만드는 프로세스를 계속합니다. 모든 배포와 동일한 세부 정보를 지정할 수도 있습니다.

## <a name="monitor-a-deployment"></a>배포 모니터링

배포의 세부 정보를 확인하고 이를 실행하는 디바이스를 모니터링하려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다. 
1. **IoT Edge**를 선택합니다.
1. **IoT Edge 배포**를 선택합니다. 

   ![IoT Edge 배포 보기](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 배포 목록을 검사합니다. 각 배포에 대해 다음 세부 정보를 볼 수 있습니다.
   * **ID** - 배포의 이름
   * **대상 조건** - 대상 디바이스를 정의하는 데 사용된 태그
   * **우선 순위** - 배포에 할당된 우선 순위 번호
   * **시스템 메트릭** - **대상 지정됨**은 대상 조건과 일치하는 IoT Hub에서 디바이스 쌍의 수를 지정하고, **적용됨**은 IoT Hub의 해당 모듈 쌍에 배포 콘텐츠를 적용한 디바이스의 수를 지정합니다. 
   * **디바이스 메트릭** - IoT Edge 클라이언트 런타임에서 성공 또는 오류를 보고하는 배포에서 Edge 디바이스의 수입니다.
   * **사용자 지정 메트릭** - 배포에 대해 정의한 메트릭 데이터를 보고하는 배포의 Edge 디바이스 수입니다.
   * **만든 시간** - 배포를 만든 때의 타임스탬프입니다. 이 타임스탬프는 두 배포의 우선 순위가 동일한 경우 연결을 중단하는 데 사용됩니다. 
1. 모니터링하려는 배포를 선택합니다.  
1. 배포 세부 정보를 검사합니다. 탭을 사용하여 배포의 세부 내용을 검토할 수 있습니다.

## <a name="modify-a-deployment"></a>배포 수정

배포를 수정하면 변경 내용이 모든 대상 디바이스에 즉시 복제됩니다. 

대상 조건을 업데이트하면 다음과 같은 업데이트가 발생합니다.

* 디바이스에서 이전 대상 조건을 충족하지 않았지만 새 대상 조건은 충족하고 이 배포가 해당 디바이스에 대해 가장 높은 순위이면 이 배포가 디바이스에 적용됩니다. 
* 이 배포를 현재 실행 중인 디바이스에서 더 이상 대상 조건을 충족하지 않으면 이 배포가 제거되고 다음으로 우선 순위가 가장 높은 배포가 적용됩니다. 
* 이 배포를 현재 실행 중인 디바이스에서 더 이상 대상 조건을 충족하지 않고 다른 배포의 대상 조건도 충족하지 않으면 디바이스에서 변경되지 않습니다. 디바이스는 현재 모듈을 현재 상태로 계속 실행하지만 더 이상 이 배포의 일부로 관리되지 않습니다. 다른 배포의 대상 조건을 충족하면 이 배포를 제거하고 새 배포가 적용됩니다. 

배포를 수정하려면 다음 단계를 수행합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다. 
1. **IoT Edge**를 선택합니다.
1. **IoT Edge 배포**를 선택합니다. 

   ![IoT Edge 배포 보기](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 수정하려는 배포를 선택합니다. 
1. 다음 필드를 업데이트합니다. 
   * 대상 조건
   * 메트릭 - 정의한 메트릭을 수정 또는 삭제하거나 새 메트릭을 추가할 수 있습니다.
   * 레이블
   * 우선 순위
1. **저장**을 선택합니다.
1. [배포 모니터링](#monitor-a-deployment)의 단계에 따라 변경 내용이 배포되는지 확인합니다. 

## <a name="delete-a-deployment"></a>배포 삭제

배포를 삭제하면 모든 디바이스에서 다음으로 우선 순위가 가장 높은 배포가 적용됩니다. 디바이스에서 다른 배포의 대상 조건을 충족하지 않으면 배포를 삭제해도 모듈이 제거되지 않습니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다. 
1. **IoT Edge**를 선택합니다.
1. **IoT Edge 배포**를 선택합니다. 

   ![IoT Edge 배포 보기](./media/how-to-deploy-monitor/iot-edge-deployments.png)

1. 확인란을 사용하여 삭제하려는 배포를 선택합니다. 
1. **삭제**를 선택합니다.
1. 이 작업으로 이 배포가 삭제되고 모든 디바이스에 대한 이전 상태로 되돌릴 것임을 알리는 메시지가 표시됩니다.  즉 우선 순위가 낮은 배포가 적용됩니다.  다른 배포가 대상으로 지정되지 않으면 모듈이 제거되지 않습니다. 디바이스에서 모든 모듈을 제거하려는 경우 모듈이 없는 배포를 만들어서 동일한 디바이스에 배포합니다. **예**를 선택하여 계속합니다. 

## <a name="next-steps"></a>다음 단계

[Edge 디바이스에 모듈 배포](module-deployment-monitoring.md)에 대해 자세히 알아봅니다.
