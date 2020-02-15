---
title: Azure IoT Central을 사용하여 의료 데이터 심사 대시보드 만들기 | Microsoft Docs
description: Azure IoT Central 애플리케이션 템플릿을 사용하여 의료 데이터 심사 대시보드를 빌드하는 방법을 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 99b27ec53d955079b5f73986408e698955c0969b
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77021647"
---
# <a name="tutorial-build-a-power-bi-provider-dashboard"></a>자습서: Power BI 공급자 대시보드 빌드



지속적인 환자 모니터링 솔루션을 빌드할 때 병원 진료 팀에서 환자 데이터를 시각화할 수 있는 대시보드를 만들 수도 있습니다. 이 자습서에서는 IoT Central 지속적인 환자 모니터링 애플리케이션 템플릿에서 Power BI 실시간 스트리밍 대시보드를 만드는 방법을 알아봅니다.

>[!div class="mx-imgBorder"]
>![대시보드 GIF](media/dashboard-gif-3.gif)

기본 아키텍처는 다음 구조를 따릅니다.

>[!div class="mx-imgBorder"] 
>![공급자 심사 대시보드](media/dashboard-architecture.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure IoT Central에서 Azure Event Hubs로 데이터 내보내기
> * Power BI 스트리밍 데이터 세트 설정
> * 논리 앱을 Azure Event Hubs에 연결
> * 논리 앱에서 Power BI로 데이터 스트리밍
> * 환자 바이탈에 대한 실시간 대시보드 빌드

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/)합니다.

* Azure IoT Central 지속적인 환자 모니터링 애플리케이션 템플릿. 아직 템플릿이 없는 경우 [애플리케이션 템플릿 배포](overview-iot-central-healthcare.md) 단계를 따르면 됩니다.

* Azure [Event Hubs 네임스페이스 및 Event Hub](https://docs.microsoft.com/azure/event-hubs/event-hubs-create).

* Event Hub에 액세스하려는 논리 앱. Azure Event Hubs 트리거를 통해 논리 앱을 시작하려면 [빈 논리 앱](https://docs.microsoft.com/azure/logic-apps/quickstart-create-first-logic-app-workflow)이 필요합니다.

* Power BI 서비스 계정. 아직 계정이 없는 경우 [Power BI 서비스 평가판 계정을 만들 수 있습니다](https://app.powerbi.com/). 이전에 Power BI를 사용한 적이 없는 경우 [Power BI 시작](https://docs.microsoft.com/power-bi/service-get-started)을 수행하면 도움이 될 수 있습니다.

## <a name="set-up-a-continuous-data-export-to-azure-event-hubs"></a>Azure Event Hubs로 지속적인 데이터 내보내기 설정
먼저 Azure IoT Central 앱 템플릿에서 구독의 Azure Event Hub로 지속적인 데이터 내보내기를 설정해야 합니다. 이렇게 하려면 [Event Hubs로 내보내기](https://docs.microsoft.com/azure/iot-central/core/howto-export-data)에 대한 이 Azure IoT Central 자습서의 단계를 따르면 됩니다. 이 자습서의 목적에 맞게 원격 분석이 가능하도록 내보내기만 하면 됩니다.

## <a name="create-a-power-bi-streaming-dataset"></a>Power BI 스트리밍 데이터 세트 만들기

1. Power BI 계정에 로그인합니다.

2. 선호하는 작업 영역에서 도구 모음의 오른쪽 위 모서리에 있는 **+ 만들기** 단추를 선택하여 새 스트리밍 데이터 세트를 만듭니다. 대시보드에 포함하려는 환자마다 별도의 데이터 세트를 만들어야 합니다.

    >[!div class="mx-imgBorder"] 
    >![스트리밍 데이터 세트 만들기](media/create-streaming-dataset.png)

3. 데이터 세트 원본에 대한 **API**를 선택합니다.

4. 데이터 세트의 **이름**(예: 환자 이름)을 입력한 다음, 스트림의 값을 입력합니다. 지속적인 환자 모니터링 애플리케이션 템플릿의 시뮬레이션된 디바이스에서 들어오는 값에 따라 아래 예제를 참조할 수 있습니다. 이 예제에는 다음과 같은 두 명의 환자가 있습니다.

    * Teddy Silvers는 스마트 무릎 보호대의 데이터를 갖고 있습니다.
    * Yesenia Sanford는 스마트 바이탈 패치의 데이터를 갖고 있습니다.

    >[!div class="mx-imgBorder"] 
    >![데이터 세트 값 입력](media/enter-dataset-values.png)

Power BI의 스트리밍 데이터 세트에 대한 자세한 내용은 [Power BI의 실시간 스트리밍](https://docs.microsoft.com/power-bi/service-real-time-streaming) 문서를 참조하세요.

## <a name="connect-your-logic-app-to-azure-event-hubs"></a>논리 앱을 Azure Event Hubs에 연결
논리 앱을 Azure Event Hubs에 연결하려면 [Azure Event Hubs 및 Azure Logic Apps를 사용하여 이벤트 전송](https://docs.microsoft.com/azure/connectors/connectors-create-api-azure-event-hubs#add-event-hubs-action) 문서에 설명된 지침을 따르면 됩니다. 다음은 몇 가지 제안하는 매개 변수입니다.

|매개 변수|값|
|---|---|
|내용 유형|application/json|
|간격|3|
|빈도|초|

이 단계를 마치면 논리 앱 디자이너가 다음과 같이 보일 것입니다.

>[!div class="mx-imgBorder"] 
>![Logic Apps가 Event Hubs에 연결](media/eh-logic-app.png)

## <a name="stream-data-to-power-bi-from-your-logic-app"></a>논리 앱에서 Power BI로 데이터 스트리밍
다음 단계는 Event Hub에서 들어오는 데이터를 구문 분석하여 이전에 만든 Power BI 데이터 세트로 스트리밍하는 것입니다.

1. 이 작업을 수행하려면 먼저 디바이스에서 Event Hub로 전송되는 JSON 페이로드를 이해해야 합니다. 그러려면 이 [샘플 스키마](https://docs.microsoft.com/azure/iot-central/core/howto-export-data#telemetry)를 확인하고 스키마에 맞게 수정하거나 [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer)를 사용하여 메시지를 검사합니다. 지속적인 환자 모니터링 애플리케이션을 사용하는 경우 메시지는 다음과 같습니다.

**스마트 바이탈 패치 원격 분석**

```json
{
  "HeartRate": 80,
  "RespiratoryRate": 12,
  "HeartRateVariability": 64,
  "BodyTemperature": 99.08839032397609,
  "BloodPressure": {
    "Systolic": 23,
    "Diastolic": 34
  },
  "Activity": "walking"
}
```

**스마트 무릎 보호대 원격 분석**

```json
{
  "Acceleration": {
    "x": 72.73510947763711,
    "y": 72.73510947763711,
    "z": 72.73510947763711
  },
  "RangeOfMotion": 123,
  "KneeBend": 3
}
```

**속성**

```json
{
  "iothub-connection-device-id": "1qsi9p8t5l2",
  "iothub-connection-auth-method": "{\"scope\":\"device\",\"type\":\"sas\",  \"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
  "iothub-connection-auth-generation-id": "637063718586331040",
  "iothub-enqueuedtime": 1571681440990,
  "iothub-message-source": "Telemetry",
  "iothub-interface-name": "Patient_health_data_3bk",
  "x-opt-sequence-number": 7,
  "x-opt-offset": "3672",
  "x-opt-enqueued-time": 1571681441317
}
```

2. JSON 페이로드를 검사했으므로, 논리 앱 디자이너로 돌아가서 **+ 새 단계**를 선택합니다. 다음 단계로 **변수 초기화**를 검색하여 추가하고 다음 매개 변수를 입력합니다.

    |매개 변수|값|
    |---|---|
    |속성|인터페이스 이름|
    |Type|String|

    **저장**을 누릅니다. 

3. **본문**이라는 다른 변수를 **문자열** 유형으로 추가합니다. 논리 앱에 다음 작업이 추가됩니다.

    >[!div class="mx-imgBorder"]
    >![변수 초기화](media/initialize-string-variables.png)
    
4. **+ 새 단계**를 선택하고 **JSON 구문 분석** 작업을 추가합니다. 이름을 **속성 구문 분석**으로 바꿉니다. 콘텐츠의 경우 Event Hub에서 오는 **속성**을 선택합니다. 맨 아래에서 **샘플 페이로드를 사용하여 스키마 생성**을 선택하고, 위의 속성 섹션에 있는 샘플 페이로드를 붙여넣습니다.

5. 다음으로, **변수 설정** 작업을 선택하고, **인터페이스 이름** 변수를 구문 분석된 JSON 속성의 **iothub-interface-name**으로 업데이트합니다.

6. 다음 작업으로 **분할** 컨트롤을 추가하고, On 매개 변수로 **인터페이스 이름** 변수를 선택합니다. 이 변수를 사용하여 데이터를 올바른 데이터 세트로 걸러낼 것입니다.

7. Azure IoT Central 애플리케이션의 **디바이스 템플릿** 보기에서 스마트 바이탈 패치 의료 데이터 및 스마트 무릎 보호대 의료 데이터의 인터페이스 이름을 찾습니다. 각 인터페이스 이름의 **스위치** 컨트롤에 대한 두 가지 서로 다른 사례를 만들고 컨트롤의 이름을 적절하게 변경합니다. **종료** 컨트롤을 사용하도록 기본 사례를 설정하고 표시할 상태를 선택할 수 있습니다.

    >[!div class="mx-imgBorder"] 
    >![분할 컨트롤](media/split-by-interface.png)

8. **스마트 바이탈 패치**의 경우 **JSON 구문 분석** 작업을 추가합니다. 콘텐츠의 경우 Event Hub에서 오는 **콘텐츠**를 선택합니다. 위의 스마트 바이탈 패치에 대한 샘플 페이로드를 복사하고 붙여넣어 스키마를 생성합니다.

9. **변수 설정** 작업을 추가하고 **본문** 변수를 7단계에서 구문 분석한 JSON의 **본문**으로 업데이트합니다.

10. 다음 작업으로 **조건**을 추가하고, 조건을 **본문**, **포함**, **심박수**로 설정합니다. 이렇게 하면 Power BI 데이터 세트를 채우기 전에 스마트 바이탈 패치에서 들어오는 올바른 데이터 세트를 얻을 수 있습니다. 7-9단계는 다음과 같습니다.

    >[!div class="mx-imgBorder"] 
    >![스마트 바이탈 조건 추가](media/smart-vitals-pbi.png)

11. 조건의 **True** 사례의 경우 **데이터 세트에 행 추가** Power BI 기능을 호출하는 작업을 추가합니다. 이렇게 하려면 Power BI에 로그인해야 합니다. **False** 사례에서는 **종료** 컨트롤을 다시 사용할 수 있습니다.

12. 적절한 **작업 영역**, **데이터 세트** 및 **테이블**를 선택합니다. Power BI에서 스트리밍 데이터 세트를 만들 때 지정한 매개 변수를 Event Hub에서 들어오는 구문 분석된 JSON 값에 매핑합니다. 채워진 작업은 다음과 같습니다.

    >[!div class="mx-imgBorder"] 
    >![Power BI에 행 추가](media/add-rows-yesenia.png)

13. **스마트 무릎 보호대** 스위치 사례의 경우 7단계와 마찬가지로 **JSON 구문 분석** 작업을 추가하여 콘텐츠를 구문 분석합니다. 그런 다음, **데이터 세트에 행을 추가**하여 Power BI에서 Teddy Silvers 데이터 세트를 업데이트합니다.

    >[!div class="mx-imgBorder"] 
    >![스마트 바이탈 조건 추가](media/knee-brace-pbi.png)

14. **저장**을 누르고 논리 앱을 실행합니다.

## <a name="build-a-real-time-dashboard-for-patient-vitals"></a>환자 바이탈에 대한 실시간 대시보드 빌드
이제 Power BI로 돌아가서 **+ 만들기**를 선택하여 새 **대시보드**을 만듭니다. 대시보드 이름을 지정하고 **만들기**를 누릅니다.

위쪽 탐색 모음에서 점 세 개를 선택한 다음, **+ 타일 추가**를 선택합니다.

>[!div class="mx-imgBorder"] 
>![대시보드에 타일 추가](media/add-tile.png)

추가할 타일 유형을 선택하고 앱을 원하는 대로 사용자 지정합니다.

## <a name="clean-up-resources"></a>리소스 정리

이 애플리케이션을 계속 사용할 계획이 없으면 다음 단계에 따라 리소스를 삭제합니다.

1. Azure Portal에서, 이전에 만든 Event Hub 및 Logic Apps 리소스를 삭제할 수 있습니다.

2. IoT Central 애플리케이션의 경우 [관리] 탭으로 이동하여 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

* [지속적인 환자 모니터링 아키텍처 지침](concept-continuous-patient-monitoring-architecture.md)을 검토합니다.
