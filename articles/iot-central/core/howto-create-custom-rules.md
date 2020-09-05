---
title: 사용자 지정 규칙 및 알림을 사용 하 여 Azure IoT Central 확장 | Microsoft Docs
description: 솔루션 개발자는 장치에서 원격 분석 전송을 중지할 때 전자 메일 알림을 보내도록 IoT Central 응용 프로그램을 구성 합니다. 이 솔루션은 Azure Stream Analytics, Azure Functions 및 SendGrid를 사용 합니다.
author: dominicbetts
ms.author: dobett
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc, devx-track-csharp
manager: philmea
ms.openlocfilehash: 572b5328a433839dafbfe23eb7207dfaeb9ea309
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89017858"
---
# <a name="extend-azure-iot-central-with-custom-rules-using-stream-analytics-azure-functions-and-sendgrid"></a>Stream Analytics, Azure Functions 및 SendGrid를 사용하여 사용자 지정 규칙으로 Azure IoT Central 확장



이 방법 가이드에서는 솔루션 개발자 인 사용자 지정 규칙 및 알림을 사용 하 여 IoT Central 응용 프로그램을 확장 하는 방법을 보여 줍니다. 이 예에서는 장치가 원격 분석 전송을 중지할 때 운영자에 게 알림을 보내는 방법을 보여 줍니다. 솔루션은 [Azure Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/) 쿼리를 사용 하 여 장치에서 원격 분석 보내기가 중지 된 시기를 검색 합니다. Stream Analytics 작업은 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/) 를 사용 하 여 [SendGrid](https://sendgrid.com/docs/for-developers/partners/microsoft-azure/)를 사용 하 여 알림 전자 메일을 보냅니다.

이 방법 가이드에서는 기본 제공 규칙 및 작업을 사용 하 여 이미 수행할 수 있는 작업 이상의 IoT Central 확장 하는 방법을 보여 줍니다.

이 방법 가이드에서는 다음 방법에 대해 알아봅니다.

* *연속 데이터 내보내기를*사용 하 여 IoT Central 응용 프로그램에서 원격 분석을 스트리밍합니다.
* 장치에서 데이터 전송을 중지 한 경우를 검색 하는 Stream Analytics 쿼리를 만듭니다.
* Azure Functions 및 SendGrid 서비스를 사용 하 여 전자 메일 알림을 보냅니다.

## <a name="prerequisites"></a>필수 구성 요소

이 가이드의 수행 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="iot-central-application"></a>IoT Central 애플리케이션

다음 설정을 사용 하 여 [Azure IoT Central 응용 프로그램](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 응용 프로그램을 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 요금제 | 표준 |
| 애플리케이션 템플릿 | 저장소 내 분석-조건 모니터링 |
| 애플리케이션 이름 | 기본값을 그대로 적용 하거나 고유한 이름을 선택 합니다. |
| URL | 기본값을 그대로 적용 하거나 고유한 URL 접두사를 선택 합니다. |
| 디렉터리 | Azure Active Directory 테 넌 트 |
| Azure 구독 | Azure 구독 |
| 지역 | 가장 가까운 지역 |

이 문서의 예제 및 스크린샷은 **미국** 지역을 사용 합니다. 가까운 위치를 선택 하 고 동일한 지역에 모든 리소스를 만들어 두어야 합니다.

이 응용 프로그램 템플릿에는 원격 분석을 전송 하는 두 개의 시뮬레이션 된 자동 온도 조절기 장치가 포함 되어 있습니다.

### <a name="resource-group"></a>Resource group

Azure Portal를 사용 하 여 만든 다른 리소스를 포함 하는 **DetectStoppedDevices** 라는 [리소스 그룹을 만듭니다](https://portal.azure.com/#create/Microsoft.ResourceGroup) . IoT Central 응용 프로그램과 동일한 위치에 Azure 리소스를 만듭니다.

### <a name="event-hubs-namespace"></a>Event Hubs 네임스페이스

Azure Portal를 사용 하 여 다음 설정으로 [Event Hubs 네임 스페이스를 만듭니다](https://portal.azure.com/#create/Microsoft.EventHub) .

| 설정 | 값 |
| ------- | ----- |
| Name    | 네임 스페이스 이름 선택 |
| 가격 책정 계층 | 기본 |
| Subscription | 사용자의 구독 |
| Resource group | DetectStoppedDevices |
| 위치 | 미국 동부 |
| 처리량 단위 | 1 |

### <a name="stream-analytics-job"></a>Stream Analytics 작업

Azure Portal를 사용 하 여 다음 설정으로 [Stream Analytics 작업을 만듭니다](https://portal.azure.com/#create/Microsoft.StreamAnalyticsJob)  .

| 설정 | 값 |
| ------- | ----- |
| Name    | 작업 이름 선택 |
| Subscription | 사용자의 구독 |
| Resource group | DetectStoppedDevices |
| 위치 | 미국 동부 |
| 호스팅 환경 | 클라우드 |
| 스트리밍 단위 | 3 |

### <a name="function-app"></a>함수 앱

다음 설정을 사용 하 여 [함수 앱을 만들려면 Azure Portal를](https://portal.azure.com/#create/Microsoft.FunctionApp) 사용 합니다.

| 설정 | 값 |
| ------- | ----- |
| 앱 이름    | 함수 앱 이름 선택 |
| Subscription | 사용자의 구독 |
| Resource group | DetectStoppedDevices |
| OS | Windows |
| 호스팅 계획 | 소비 계획 |
| 위치 | 미국 동부 |
| 런타임 스택 | .NET |
| 스토리지 | Create new |

### <a name="sendgrid-account"></a>SendGrid 계정

Azure Portal를 사용 하 여 다음 설정으로 [SendGrid 계정을 만듭니다](https://portal.azure.com/#create/Sendgrid.sendgrid) .

| 설정 | 값 |
| ------- | ----- |
| Name    | SendGrid 계정 이름 선택 |
| 암호 | 암호 만들기 |
| Subscription | 사용자의 구독 |
| Resource group | DetectStoppedDevices |
| 가격 책정 계층 | F1 무료 |
| 연락처 정보 | 필요한 정보를 입력 합니다. |

필요한 모든 리소스를 만든 경우 **DetectStoppedDevices** 리소스 그룹은 다음 스크린샷 처럼 보입니다.

![중지 된 장치 검색 리소스 그룹](media/howto-create-custom-rules/resource-group.png)

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

이벤트 허브로 원격 분석을 지속적으로 내보내도록 IoT Central 응용 프로그램을 구성할 수 있습니다. 이 섹션에서는 IoT Central 응용 프로그램에서 원격 분석을 수신 하는 이벤트 허브를 만듭니다. 이벤트 허브는 처리를 위해 원격 분석을 Stream Analytics 작업에 전달 합니다.

1. Azure Portal에서 Event Hubs 네임 스페이스로 이동 하 고 **+ 이벤트 허브**를 선택 합니다.
1. 이벤트 허브의 이름을 **centralexport**으로 선택 하 고 **만들기**를 선택 합니다.

Event Hubs 네임 스페이스는 다음 스크린샷 처럼 보입니다.

![Event Hubs 네임스페이스](media/howto-create-custom-rules/event-hubs-namespace.png)

## <a name="get-sendgrid-api-key"></a>SendGrid API 키 가져오기

함수 앱에는 전자 메일 메시지를 보내기 위한 SendGrid API 키가 필요 합니다. SendGrid API 키를 만들려면 다음을 수행 합니다.

1. Azure Portal에서 SendGrid 계정으로 이동 합니다. 그런 다음 **관리** 를 선택 하 여 SendGrid 계정에 액세스 합니다.
1. SendGrid 계정에서 **설정**, **API 키**를 차례로 선택 합니다. **API 키 만들기**를 선택 합니다.

    ![SendGrid API 키 만들기](media/howto-create-custom-rules/sendgrid-api-keys.png)

1. **API 키 만들기** 페이지에서 **모든** 권한이 있는 **AzureFunctionAccess** 라는 키를 만듭니다.
1. API 키를 기록해 둡니다. 함수 앱을 구성할 때 필요 합니다.

## <a name="define-the-function"></a>함수 정의

이 솔루션은 Stream Analytics 작업에서 중지 된 장치를 검색할 때 Azure Functions 앱을 사용 하 여 전자 메일 알림을 보냅니다. 함수 앱을 만들려면 다음을 수행 합니다.

1. Azure Portal에서 **DetectStoppedDevices** 리소스 그룹의 **App Service** 인스턴스로 이동 합니다.
1. **+** 새 함수를 만들려면 선택 합니다.
1. **개발 환경 선택** 페이지에서 **포털 내** 를 선택한 다음 **계속**을 선택 합니다.
1. **함수 만들기** 페이지에서 **Webhook + API** 를 선택한 다음 **만들기**를 선택 합니다.

포털에서 **HttpTrigger1**라는 기본 함수를 만듭니다.

![기본 HTTP 트리거 함수](media/howto-create-custom-rules/default-function.png)

### <a name="configure-function-bindings"></a>함수 바인딩 구성

SendGrid를 사용 하 여 전자 메일을 보내려면 다음과 같이 함수에 대 한 바인딩을 구성 해야 합니다.

1. **통합**을 선택 하 고, 출력 **HTTP ($return)** 를 선택한 다음, **삭제**를 선택 합니다.
1. **+ 새 출력**을 선택 하 고 **SendGrid**를 선택한 다음 **선택**을 선택 합니다. **설치** 를 선택 하 여 SendGrid 확장을 설치 합니다.
1. 설치가 완료 되 면 **함수 반환 값 사용**을 선택 합니다. 유효한 받는 사람 **주소** 를 추가 하 여 전자 메일 알림을 받습니다.  전자 메일 보낸 사람으로 사용할 유효한 **보낸 사람 주소** 를 추가 합니다.
1. **SENDGRID API 키 앱 설정**옆에서 **새로 만들기** 를 선택 합니다. **Sendgridapikey** 를 키로 입력 하 고 이전에 값으로 적어둔 SendGrid API 키를 입력 합니다. 그런 다음 **만들기**를 선택합니다.
1. **저장** 을 선택 하 여 함수에 대 한 SendGrid 바인딩을 저장 합니다.

통합 설정은 다음 스크린샷 처럼 보입니다.

![함수 앱 통합](media/howto-create-custom-rules/function-integrate.png)

### <a name="add-the-function-code"></a>함수 코드 추가

함수를 구현 하려면 c # 코드를 추가 하 여 들어오는 HTTP 요청을 구문 분석 하 고 다음과 같이 전자 메일을 보냅니다.

1. 함수 앱에서 **HttpTrigger1** 함수를 선택 하 고 c # 코드를 다음 코드로 바꿉니다.

    ```csharp
    #r "Newtonsoft.Json"
    #r "..\bin\SendGrid.dll"

    using System;
    using SendGrid.Helpers.Mail;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Extensions.Primitives;
    using Newtonsoft.Json;

    public static SendGridMessage Run(HttpRequest req, ILogger log)
    {
        string requestBody = new StreamReader(req.Body).ReadToEnd();
        log.LogInformation(requestBody);
        var notifications = JsonConvert.DeserializeObject<IList<Notification>>(requestBody);

        SendGridMessage message = new SendGridMessage();
        message.Subject = "Contoso device notification";

        var content = "The following device(s) have stopped sending telemetry:<br/><br/><table><tr><th>Device ID</th><th>Time</th></tr>";
        foreach(var notification in notifications) {
            log.LogInformation($"No message received - Device: {notification.deviceid}, Time: {notification.time}");
            content += $"<tr><td>{notification.deviceid}</td><td>{notification.time}</td></tr>";
        }
        content += "</table>";
        message.AddContent("text/html", content);

        return message;
    }

    public class Notification
    {
        public string deviceid { get; set; }
        public string time { get; set; }
    }
    ```

    새 코드를 저장할 때까지 오류 메시지가 표시 될 수 있습니다.

1. **저장** 을 선택 하 여 함수를 저장 합니다.

### <a name="test-the-function-works"></a>함수 작동 테스트

포털에서 함수를 테스트 하려면 먼저 코드 편집기의 맨 아래에 있는 **로그** 를 선택 합니다. 그런 다음 코드 편집기의 오른쪽에 있는 **테스트** 를 선택 합니다. **요청 본문**으로 다음 JSON을 사용 합니다.

```json
[{"deviceid":"test-device-1","time":"2019-05-02T14:23:39.527Z"},{"deviceid":"test-device-2","time":"2019-05-02T14:23:50.717Z"},{"deviceid":"test-device-3","time":"2019-05-02T14:24:28.919Z"}]
```

함수 로그 메시지는 **로그** 패널에 표시 됩니다.

![함수 로그 출력](media/howto-create-custom-rules/function-app-logs.png)

몇 분 후 받는 **사람 전자 메일 주소는 다음** 내용이 포함 된 전자 메일을 받습니다.

```txt
The following device(s) have stopped sending telemetry:

Device ID    Time
test-device-1    2019-05-02T14:23:39.527Z
test-device-2    2019-05-02T14:23:50.717Z
test-device-3    2019-05-02T14:24:28.919Z
```

## <a name="add-stream-analytics-query"></a>Stream Analytics 쿼리 추가

이 솔루션은 Stream Analytics 쿼리를 사용 하 여 장치에서 120 초 넘게 원격 분석 전송을 중지할 때를 검색 합니다. 쿼리에서는 이벤트 허브의 원격 분석을 입력으로 사용 합니다. 작업은 함수 앱에 쿼리 결과를 보냅니다. 이 섹션에서는 Stream Analytics 작업을 구성 합니다.

1. Azure Portal에서 Stream Analytics 작업으로 이동 하 여 **작업 토폴로지** 에서 **입력**을 선택 하 고 **+ 스트림 입력 추가**를 선택한 다음 **이벤트 허브**를 선택 합니다.
1. 이전에 만든 이벤트 허브를 사용 하 여 입력을 구성 하려면 다음 표의 정보를 사용 하 고 **저장**을 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 입력 별칭 | centraltelemetry |
    | Subscription | 사용자의 구독 |
    | 이벤트 허브 네임스페이스 | 이벤트 허브 네임 스페이스 |
    | 이벤트 허브 이름 | 기존- **centralexport** 사용 |

1. **작업 토폴로지**에서 **출력**을 선택 하 고 **+ 추가**를 선택한 다음, **Azure 함수**를 선택 합니다.
1. 다음 표의 정보를 사용 하 여 출력을 구성한 후 **저장**을 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 출력 별칭 | emailnotification |
    | Subscription | 사용자의 구독 |
    | 함수 앱 | 함수 앱 |
    | 함수  | HttpTrigger1 |

1. **작업 토폴로지**에서 **쿼리** 를 선택 하 고 기존 쿼리를 다음 SQL로 바꿉니다.

    ```sql
    with
    LeftSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid1, 
        EventEnqueuedUtcTime AS time1
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    ),
    RightSide as
    (
        SELECT
        -- Get the device ID from the message metadata and create a column
        GetMetadataPropertyValue([centraltelemetry], '[EventHub].[IoTConnectionDeviceId]') as deviceid2, 
        EventEnqueuedUtcTime AS time2
        FROM
        -- Use the event enqueued time for time-based operations
        [centraltelemetry] TIMESTAMP BY EventEnqueuedUtcTime
    )

    SELECT
        LeftSide.deviceid1 as deviceid,
        LeftSide.time1 as time
    INTO
        [emailnotification]
    FROM
        LeftSide
        LEFT OUTER JOIN
        RightSide 
        ON
        LeftSide.deviceid1=RightSide.deviceid2 AND DATEDIFF(second,LeftSide,RightSide) BETWEEN 1 AND 120
        where
        -- Find records where a device didn't send a message 120 seconds
        RightSide.deviceid2 is NULL
    ```

1. **저장**을 선택합니다.
1. Stream Analytics 작업을 시작 하려면 **개요**, **시작**, **지금**을 차례로 선택한 다음 **시작**을 선택 합니다.

    ![Stream Analytics](media/howto-create-custom-rules/stream-analytics.png)

## <a name="configure-export-in-iot-central"></a>IoT Central에서 내보내기 구성

[Azure IoT Central application manager](https://aka.ms/iotcentral) 웹 사이트에서 Contoso 템플릿에서 만든 IoT Central 응용 프로그램으로 이동 합니다. 이 섹션에서는 시뮬레이션 된 장치에서 이벤트 허브로 원격 분석을 스트리밍하기 응용 프로그램을 구성 합니다. 내보내기를 구성 하려면:

1. **데이터 내보내기** 페이지로 이동 하 고, **+ 새로 만들기**를 선택 하 고, **Azure Event Hubs**를 선택 합니다.
1. 내보내기를 구성 하려면 다음 설정을 사용 하 고 **저장**을 선택 합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 표시 이름 | Event Hubs로 내보내기 |
    | 사용 | 켜기 |
    | Event Hubs 네임스페이스 | Event Hubs 네임 스페이스 이름 |
    | 이벤트 허브 | centralexport |
    | 측정 | 켜기 |
    | 디바이스 | 끄기 |
    | 디바이스 템플릿 | 끄기 |

![연속 데이터 내보내기 구성](media/howto-create-custom-rules/cde-configuration.png)

계속 하기 전에 내보내기 상태가 **실행 중** 이 될 때까지 기다립니다.

## <a name="test"></a>테스트

솔루션을 테스트 하려면 IoT Central에서 시뮬레이션 된 중지 된 장치로 연속 데이터 내보내기를 사용 하지 않도록 설정할 수 있습니다.

1. IoT Central 응용 프로그램에서 **데이터 내보내기** 페이지로 이동 하 고 **내보내기를 선택 하 여 Event Hubs** 내보내기 구성으로 이동 합니다.
1. **사용** 을 **Off** 로 설정 하 고 **저장**을 선택 합니다.
1. 두 분 이상에서 받는 **사람 전자 메일 주소는 다음** 예제 콘텐츠와 같은 하나 이상의 메일을 받습니다.

    ```txt
    The following device(s) have stopped sending telemetry:

    Device ID         Time
    Thermostat-Zone1  2019-11-01T12:45:14.686Z
    ```

## <a name="tidy-up"></a>정리

이 방법 및 불필요 한 비용을 방지 하기 위해 Azure Portal에서 **DetectStoppedDevices** 리소스 그룹을 삭제 합니다.

응용 프로그램 내의 **관리** 페이지에서 IoT Central 응용 프로그램을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 다음 작업 방법을 배웁니다.

* *연속 데이터 내보내기를*사용 하 여 IoT Central 응용 프로그램에서 원격 분석을 스트리밍합니다.
* 장치에서 데이터 전송을 중지 한 경우를 검색 하는 Stream Analytics 쿼리를 만듭니다.
* Azure Functions 및 SendGrid 서비스를 사용 하 여 전자 메일 알림을 보냅니다.

이제 사용자 지정 규칙 및 알림을 만드는 방법을 배웠으므로 제안 된 다음 단계는 [사용자 지정 분석을 사용 하 여 Azure IoT Central를 확장](howto-create-custom-analytics.md)하는 방법을 설명 하는 것입니다.
