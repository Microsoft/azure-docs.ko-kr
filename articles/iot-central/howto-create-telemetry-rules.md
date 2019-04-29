---
title: Azure IoT Central 애플리케이션에서 원격 분석 규칙 만들기 및 관리 | Microsoft Docs
description: Azure IoT Central 원격 분석 규칙을 사용하여 디바이스를 거의 실시간으로 모니터링하고, 규칙이 트리거되면 이메일 보내기 등의 작업을 자동으로 호출합니다.
author: ankitgupta
ms.author: ankitgup
ms.date: 02/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 0fb7cdaa24d139549545c93c920d60936d3c9fc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60886122"
---
# <a name="create-a-telemetry-rule-and-set-up-notifications-in-your-azure-iot-central-application"></a>Azure IoT Central 애플리케이션에서 원격 분석 규칙을 만들고 알림 설정

*이 문서는 운영자, 빌더 및 관리자에게 적용됩니다.*

Azure IoT Central을 사용하여 원격으로 연결된 디바이스를 모니터링할 수 있습니다. Azure IoT Central 규칙을 사용하여 디바이스를 거의 실시간으로 모니터링하고, 이메일 보내기 또는 Microsoft Flow 트리거 등의 작업을 자동으로 호출합니다. 클릭 몇 번으로 디바이스 데이터를 모니터링하는 조건을 정의하고 해당 작업을 구성할 수 있습니다. 이 문서에서는 디바이스에서 보낸 원격 분석을 모니터링하는 규칙을 만드는 방법을 설명합니다.

디바이스는 원격 분석 측정을 사용하여 디바이스에서 숫자 데이터를 보낼 수 있습니다. 선택한 디바이스가 지정된 임계값을 초과하면 원격 분석 규칙이 트리거됩니다.

## <a name="create-a-telemetry-rule"></a>원격 분석 규칙 만들기

원격 분석 규칙을 만들려면 디바이스 템플릿에 하나 이상의 원격 분석 측정이 정의되어 있어야 합니다. 이 예제에서는 온도 및 습도 원격 분석 데이터를 전송하는 냉장 자동 판매기를 사용합니다. 이 규칙은 디바이스에서 보고한 온도를 모니터링하다가 온도가 80도를 초과하면 이메일을 보냅니다.

1. 사용 하는 **장치 템플릿** 페이지에 대 한 규칙을 추가할 장치 템플릿으로 이동 합니다.

1. 아직 규칙을 하나도 만들지 않은 경우 다음 화면이 표시됩니다.

    ![규칙 없음](media/howto-create-telemetry-rules/Rules_Landing_Page.png)

1. 에 **규칙** 탭을 선택 **+ 새 규칙** 만들어야 하는 규칙의 유형을 보려면.

1. 선택 **원격 분석** 장치 원격 분석을 모니터링 하는 규칙을 만듭니다.

    ![규칙 유형](media/howto-create-telemetry-rules/Rule_Types.png)

1. 이 디바이스 템플릿에서 규칙을 식별하는 데 도움이 되는 이름을 입력합니다.

1. 이 템플릿에서 만든 모든 디바이스에 즉시 규칙을 사용하려면 **이 템플릿의 모든 디바이스에 규칙 사용**으로 전환합니다.

   ![규칙 세부 정보](media/howto-create-telemetry-rules/Rule_Detail.png)

    규칙은 디바이스 템플릿 아래에 있는 모든 디바이스에 자동으로 적용됩니다.

### <a name="configure-the-rule-conditions"></a>규칙 조건 구성

조건은 규칙이 모니터링하는 기준을 정의합니다.

1. 선택 **+** 옆에 **조건** 새 조건을 추가 합니다.

1. **단위** 드롭다운에서 모니터링하려는 원격 분석을 선택합니다.

1. 그런 다음, **집계**, **연산자**를 선택하고 **임계값**을 입력합니다.
   - 집계는 선택 사항입니다. 집계를 사용하지 않으면 조건을 충족하는 각 원격 분석 데이터 요소에 대해 규칙이 트리거됩니다. 예를 들어 규칙은 거의 즉시 트리거 온도가 80 개를 초과 하는 경우 다음 규칙이 구성 된 경우 경우 장치 보고 온도 > 80입니다.
   - Average, Min, Max, Count와 같은 집계 함수를 선택할 경우, 사용자는 조건을 평가할 **집계 기간**을 지정해야 합니다. 예를 들어 기간을 "5분"으로 설정할 경우, 평균 온도가 5분 이상 80도를 초과할 경우 규칙이 80도를 초과하는 평균 온도를 찾습니다. 규칙 평가 주기는 **집계 기간**과 동일합니다. 즉, 이 예에서 규칙은 5분에 한 번 평가됩니다.

     ![조건](media/howto-create-telemetry-rules/Aggregate_Condition_Filled_Out.png)

     >[!NOTE]
     >**조건** 아래에 둘 이상의 원격 분석 측정값을 추가할 수 있습니다. 조건을 여러 개 지정하는 경우 모든 조건을 충족해야 규칙이 트리거됩니다. 각 조건은 'AND' 절을 사용하여 암시적으로 조인됩니다. 집계를 사용할 경우 모든 측정값이 집계되어야 합니다.

### <a name="configure-actions"></a>작업 구성

이 섹션에서는 규칙이 실행될 때 수행할 작업을 설정하는 방법을 보여줍니다. 규칙에 지정된 모든 조건이 true로 평가될 경우 작업이 호출됩니다.

1. **작업** 옆에 있는 **+** 기호를 선택합니다. 사용 가능한 작업 목록을 표시됩니다.  

    ![작업 추가](media/howto-create-telemetry-rules/Add_Action.png)

1. **이메일** 작업을 선택하고, **받는 사람** 필드에 유효한 이메일 주소를 입력하고, 규칙이 트리거될 때 이메일 본문에 표시할 메모를 입력합니다.

    > [!NOTE]
    > 애플리케이션에 추가되어 한 번 이상 로그인한 사용자에게만 이메일이 발송됩니다. Azure IoT Central에서 [사용자 관리](howto-administer.md)에 대해 자세히 알아보세요.

   ![작업 구성](media/howto-create-telemetry-rules/Configure_Action.png)

1. 규칙을 저장하려면 **저장**을 선택합니다. 몇 분 이내에 규칙이 적용되어 애플리케이션으로 전송되는 원격 분석 데이터의 모니터링이 시작됩니다. 규칙에 지정된 조건이 충족하는 경우 규칙이 구성된 이메일 작업을 트리거합니다.

Microsoft Flow 및 webhook와 같은 다른 작업을 규칙에 추가할 수 있습니다. 규칙당 최대 5개의 작업을 추가할 수 있습니다.

- [Microsoft Flow 작업](howto-add-microsoft-flow.md): 규칙이 트리거되면 Microsoft Flow에서 워크플로를 시작합니다. 
- [웹후크 작업](howto-create-webhooks.md): 규칙이 트리거되면 다른 서비스에 알립니다.

## <a name="parameterize-the-rule"></a>규칙 매개 변수화

규칙은 **디바이스 속성**에서 특정 값을 매개 변수로 얻을 수 있습니다. 매개 변수를 사용하면 디바이스에 따라 원격 분석 임계값이 달라지는 시나리오에서 유용합니다. 규칙을 만들 때 80도 같은 절대값을 입력하지 말고, **이상적인 최대 임계값**처럼 임계값을 지정하는 디바이스 속성을 선택합니다. 규칙이 실행되면 규칙은 디바이스 원격 분석 데이터를 디바이스 속성에 설정된 값과 매칭합니다.

매개 변수는 디바이스 템플릿당 관리할 규칙 수를 줄이는 효과적인 방법입니다.

**디바이스 속성**을 매개 변수로 사용하여 작업을 구성할 수도 있습니다. 이메일 주소가 속성으로 저장되는 경우 **받는 사람** 주소를 정의할 때 사용할 수 있습니다.

## <a name="delete-a-rule"></a>규칙 삭제

규칙이 더 이상 필요 없으면 해당 규칙을 열고 **삭제**를 선택하여 삭제할 수 있습니다. 규칙을 삭제하면 디바이스 템플릿 및 모든 관련 디바이스에서 해당 규칙이 제거됩니다.

## <a name="enable-or-disable-a-rule-for-a-device-template"></a>디바이스 템플릿에 규칙을 사용하도록 또는 사용하지 않도록 설정

해당 디바이스로 이동하여 사용하도록 또는 사용하지 않도록 설정할 규칙을 선택합니다. 규칙에서 **이 템플릿의 모든 디바이스에 규칙 사용** 단추를 누르면 디바이스 템플릿과 연결된 모든 디바이스에 규칙을 사용하도록 또는 사용하지 않도록 설정됩니다.

## <a name="enable-or-disable-a-rule-for-a-device"></a>디바이스에 규칙을 사용하도록 또는 사용하지 않도록 설정

해당 디바이스로 이동하여 사용하도록 또는 사용하지 않도록 설정할 규칙을 선택합니다. **이 디바이스에 규칙 사용** 단추를 눌러 해당 디바이스에 규칙을 사용하도록 또는 사용하지 않도록 설정합니다.

## <a name="next-steps"></a>다음 단계

Azure IoT Central 애플리케이션에서 규칙을 만드는 방법을 알아보았으니, 다음과 같은 후속 단계를 진행하시기 바랍니다.

- [규칙에 Microsoft Flow 작업 추가](howto-add-microsoft-flow.md)
- [규칙에 Webhook 작업 추가](howto-create-webhooks.md)
- [하나 이상의 규칙에서를 실행 하려면 여러 작업을 그룹화](howto-use-action-groups.md)
- [디바이스를 관리하는 방법](howto-manage-devices.md)
