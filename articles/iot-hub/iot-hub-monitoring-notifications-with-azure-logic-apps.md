---
title: Azure Logic Apps를 사용하여 IoT 원격 모니터링 및 알림 | Microsoft Docs
description: Azure Logic Apps을 사용하여 IoT Hub에서 IoT 온도를 모니터링하고 감지된 이상에 대한 전자 메일 알림을 자동으로 사서함에 보낼 수 있습니다.
author: robinsh
keywords: iot 모니터링, iot 알림, iot 온도 모니터링
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 07/18/2019
ms.author: robinsh
ms.openlocfilehash: ad1fcb67704e79f5aef62a59604e47f477804405
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2019
ms.locfileid: "68385723"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Azure Logic Apps으로 IoT Hub와 사서함을 연결하여 IoT 원격 모니터링 및 알림

![엔드투엔드 다이어그램](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) 를 통해 온-프레미스 및 클라우드 서비스, 하나 이상의 엔터프라이즈, 다양 한 프로토콜에서 워크플로를 오케스트레이션 할 수 있습니다. 논리 앱은 트리거로 시작 하 고 그 다음에는 조건 및 반복기와 같은 기본 제공 컨트롤을 사용 하 여 시퀀싱 할 수 있는 하나 이상의 작업이 있습니다. 이러한 유연성을 통해 IoT 모니터링 시나리오를 위한 이상적인 IoT 솔루션을 Logic Apps 수 있습니다. 예를 들어 IoT Hub 엔드포인트의 장치에서 원격 분석 데이터가 도착 하면 Azure Storage blob에 데이터를 웨어하우스 하 고, 데이터 이상에 대 한 경고를 표시 하는 전자 메일 경고를 보내고, 장치에서 오류를 보고 하는 경우 기술 지원 담당자에 게 경고를 보낼 수 있습니다. .

## <a name="what-you-learn"></a>학습 내용

IoT Hub와 사서함을 연결하여 온도를 모니터링하고 알림을 보내는 논리 앱을 만드는 방법을 배웁니다.

장치에서 실행 되는 클라이언트 코드는 IoT hub에 보내는 `temperatureAlert`모든 원격 분석 메시지에서 응용 프로그램 속성을 설정 합니다. 클라이언트 코드가 30 C 이상의 온도를 검색 하면이 속성을로 `true`설정 하 고, 그렇지 않은 경우 속성을로 `false`설정 합니다.

IoT hub에 도착 하는 메시지는 다음과 유사 합니다. 본문 `temperatureAlert` 에 포함 된 원격 분석 데이터와 응용 프로그램 속성에 포함 된 속성 (시스템 속성은 표시 되지 않음)이 있습니다.

```json
{
  "body": {
    "messageId": 18,
    "deviceId": "Raspberry Pi Web Client",
    "temperature": 27.796111770668457,
    "humidity": 66.77637926438427
  },
  "applicationProperties": {
    "temperatureAlert": "false"
  }
}
```

IoT Hub 메시지 형식에 대 한 자세한 내용은 [IoT Hub 메시지 만들기 및 읽기](iot-hub-devguide-messages-construct.md)를 참조 하세요.

이 항목에서는 Service Bus 엔드포인트 `temperatureAlert` `true` 에 속성이 있는 메시지를 보내도록 IoT hub에 라우팅을 설정 합니다. 그런 다음 Service Bus 엔드포인트에 도착 하는 메시지를 트리거하고 사용자에 게 전자 메일 알림을 보내는 논리 앱을 설정 합니다.

## <a name="what-you-do"></a>수행할 작업

* Service Bus 네임 스페이스를 만들고 여기에 Service Bus 큐를 추가 합니다.
* 온도 경고가 포함 된 메시지를 Service Bus 큐로 라우팅하도록 IoT hub에 사용자 지정 엔드포인트 및 라우팅 규칙을 추가 합니다.
* Service Bus 큐에서 메시지를 사용 하 고 원하는 받는 사람에 게 알림 전자 메일을 보내는 논리 앱을 만들고, 구성 하 고, 테스트 합니다.

## <a name="what-you-need"></a>필요한 항목

* [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 장치 자습서 중 하나를 완료 합니다. 예를 들어 [node.js를 사용 하 여 Pi를 Raspberry](iot-hub-raspberry-pi-kit-node-get-started.md)합니다. 다음 요구 사항을 다룹니다.

  * 활성화된 Azure 구독.
  * 구독 중인 Azure IoT Hub
  * Azure IoT hub에 원격 분석 메시지를 전송 하는 장치에서 실행 되는 클라이언트 응용 프로그램입니다.

## <a name="create-service-bus-namespace-and-queue"></a>Service Bus 네임 스페이스 및 큐 만들기

Service Bus 네임스페이스 및 큐를 만듭니다. 이 항목의 뒷부분에서는 온도 경고가 포함 된 메시지를 Service Bus 큐에 보내도록 IoT hub에 라우팅 규칙을 만듭니다 .이 규칙은 논리 앱에서 선택 하 고 알림 전자 메일을 보내도록 트리거합니다.

### <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

1. [Azure Portal](https://portal.azure.com/)에서 **+ 리소스** > **통합** > 만들기**Service Bus**를 선택 합니다.

1. **네임 스페이스 만들기** 창에서 다음 정보를 제공 합니다.

   **이름**: Service bus 네임 스페이스의 이름입니다. 네임 스페이스는 Azure에서 고유 해야 합니다.

   **가격 책정 계층**: 드롭다운 목록에서 **기본** 을 선택 합니다. 이 자습서에서는 기본 계층이면 충분합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **위치**: IoT hub에서 사용 하는 것과 동일한 위치를 사용 합니다.

   ![Azure Portal에서 Service Bus 네임스페이스 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. **만들기**를 선택합니다. 다음 단계로 이동 하기 전에 배포가 완료 될 때까지 기다립니다.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>네임 스페이스에 Service Bus 큐 추가

1. Service Bus 네임 스페이스를 엽니다. Service Bus 네임 스페이스를 가져오는 가장 쉬운 방법은 리소스 창에서 **리소스 그룹** 을 선택 하 고 리소스 그룹을 선택한 다음 리소스 목록에서 Service Bus 네임 스페이스를 선택 하는 것입니다.

1. **Service Bus 네임 스페이스** 창에서 **+ 큐**를 선택 합니다.

1. 큐의 이름을 입력 한 다음 **만들기**를 선택 합니다. 큐가 성공적으로 만들어지면 **큐 만들기** 창이 닫힙니다.

   ![Azure Portal에서 Service Bus 큐 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. **Service Bus 네임 스페이스** 창으로 돌아가서 **엔터티**아래에서 **큐**를 선택 합니다. 목록에서 Service Bus 큐를 열고 **공유 액세스 정책** >  **+ 추가**를 선택 합니다.

1. 정책의 이름을 입력 하 고 **관리**를 선택한 다음 **만들기**를 선택 합니다.

   ![Azure Portal에서 service bus 큐 정책 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>IoT hub에 사용자 지정 엔드포인트 및 라우팅 규칙 추가

Service Bus 큐에 대 한 사용자 지정 엔드포인트을 IoT hub에 추가 하 고, 온도 경고가 포함 된 메시지를 해당 엔드포인트에 전달 하는 메시지 라우팅 규칙을 만들어 논리 앱에서 선택 합니다. 라우팅 규칙은 라우팅 쿼리 `temperatureAlert = "true"`를 사용 하 여 장치에서 실행 되는 클라이언트 코드에 의해 설정 된 `temperatureAlert` 응용 프로그램 속성의 값을 기반으로 메시지를 전달 합니다. 자세히 알아보려면 [메시지 속성을 기반으로 하는 메시지 라우팅 쿼리](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties)를 참조 하세요.

### <a name="add-a-custom-endpoint"></a>사용자 지정 엔드포인트 추가

1. IoT Hub를 엽니다. IoT hub를 가져오는 가장 쉬운 방법은 리소스 창에서 **리소스 그룹** 을 선택 하 고 리소스 그룹을 선택한 다음 리소스 목록에서 IoT hub를 선택 하는 것입니다.

1. **메시징**에서 **메시지 라우팅**을 선택 합니다. **메시지 라우팅** 창에서 **사용자 지정 엔드포인트** 탭을 선택 하 고 **+ 추가**를 선택 합니다. 드롭다운 목록에서 **Service bus 큐**를 선택 합니다.

   ![Azure Portal에서 IoT Hub에 엔드포인트 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. **Service bus 엔드포인트 추가** 창에서 다음 정보를 입력 합니다.

   **엔드포인트 이름**: 엔드포인트의 이름입니다.

   **Service Bus 네임스페이스**: 만든 네임 스페이스를 선택 합니다.

   **Service bus 큐**: 만든 큐를 선택 합니다.

   ![Azure Portal에서 IoT Hub에 엔드포인트 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. **만들기**를 선택합니다. 엔드포인트이 성공적으로 생성 되 면 다음 단계를 진행 합니다.

### <a name="add-a-routing-rule"></a>회람 규칙 추가

1. **메시지 라우팅** 창으로 돌아가서 **경로** 탭을 선택한 다음 **+ 추가**를 선택 합니다.

1. **경로 추가** 창에서 다음 정보를 입력 합니다.

   **이름**: 라우팅 규칙의 이름입니다.

   **엔드포인트**: 만든 엔드포인트을 선택 합니다.

   **데이터 원본**: **장치 원격 분석 메시지**를 선택 합니다.

   **라우팅 쿼리**:  `temperatureAlert = "true"` 을 입력합니다.

   ![Azure Portal에서 라우팅 규칙 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1.           **저장**을 선택합니다. **메시지 라우팅** 창을 닫을 수 있습니다.

## <a name="create-and-configure-a-logic-app"></a>논리 앱 만들기 및 구성

이전 섹션에서는 온도 경고가 포함 된 메시지를 Service Bus 큐로 라우팅하도록 IoT hub를 설정 합니다. 이제 Service Bus 큐를 모니터링 하 고 메시지가 큐에 추가 될 때마다 전자 메일 알림을 보내는 논리 앱을 설정 합니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기

1. **리소스** > 만들기 통합논리 > **앱**을 선택 합니다.

1. 다음 정보를 입력합니다.

   **이름**: 논리 앱의 이름입니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **위치**: IoT hub에서 사용 하는 것과 동일한 위치를 사용 합니다.

   ![Azure Portal에서 논리 앱 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. **만들기**를 선택합니다.

### <a name="configure-the-logic-app-trigger"></a>논리 앱 트리거 구성

1. 논리 앱을 엽니다. 논리 앱을 가져오는 가장 쉬운 방법은 리소스 창에서 **리소스 그룹** 을 선택 하 고 리소스 그룹을 선택한 다음 리소스 목록에서 논리 앱을 선택 하는 것입니다. 논리 앱을 선택 하면 Logic Apps 디자이너가 열립니다.

1. Logic Apps 디자이너에서 **템플릿으로** 스크롤하고 **빈 논리 앱**을 선택 합니다.

   ![Azure Portal에서 빈 논리 앱 시작](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. **모두** 탭을 선택 하 고 **Service Bus**를 선택 합니다.

   ![Service Bus를 선택하여 Azure Portal에서 논리 앱 만들기 시작](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. **트리거**아래에서 **큐에 하나 이상의 메시지가 도착 하는 경우 (자동 완성)** 를 선택 합니다.

   ![Azure Portal에서 논리 앱에 대 한 트리거를 선택 합니다.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Service Bus 연결을 만듭니다.
   1. 연결 이름을 입력 하 고 목록에서 Service Bus 네임 스페이스를 선택 합니다. 다음 화면이 열립니다.

      ![Azure Portal에서 논리 앱에 대한 Service Bus 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Service bus 정책 (RootManageSharedAccessKey)을 선택 합니다. 그런 다음 **만들기**를 선택 합니다.

      ![Azure Portal에서 논리 앱에 대한 Service Bus 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. 마지막 화면에서 **큐 이름**에 대해 드롭다운에서 만든 큐를 선택 합니다. `175` **최대 메시지 수**에 대해를 입력 합니다.

      ![논리 앱에서 Service Bus 연결의 최대 메시지 수 지정](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. Logic Apps 디자이너의 맨 위에 있는 메뉴에서 **저장** 을 선택 하 여 변경 내용을 저장 합니다.

### <a name="configure-the-logic-app-action"></a>논리 앱 작업 구성

1. SMTP 서비스 연결을 만듭니다.

   1. **새 단계**를 선택합니다. **작업 선택**에서 **모두** 탭을 선택 합니다.

   1. 검색 `smtp` 상자에을 입력 하 고 검색 결과에서 **SMTP** 서비스를 선택한 다음 **전자 메일 보내기**를 선택 합니다.

      ![Azure Portal에서 논리 앱에 SMTP 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. 사서함에 대 한 SMTP 정보를 입력 한 다음 **만들기**를 선택 합니다.

      ![Azure Portal에서 논리 앱에 SMTP 연결 정보 입력](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) 및 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html)에 대한 SMTP 정보를 가져옵니다.

      > [!NOTE]
      > 연결을 설정 하려면 SSL을 사용 하지 않도록 설정 해야 할 수 있습니다. 이 경우 연결이 설정 된 후 SSL을 다시 사용 하도록 설정 하려는 경우이 섹션의 끝에 있는 선택적 단계를 참조 하세요.

   1. **전자 메일 보내기** 단계의 **새 매개 변수 추가** 드롭다운에서 **시작**, **받는**사람, **제목** 및 **본문**을 선택 합니다. 화면에서 아무 곳 이나 클릭 하거나 탭 하 여 선택 상자를 닫습니다.

      ![SMTP 연결 전자 메일 필드 선택](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. **보내는 사람** 및 **받는 사람**의 전자 메일 주소를 입력하고, **제목** 및 **본문**에 `High temperature detected`를 입력합니다. **이 흐름에서 사용 하는 앱 및 커넥터에서 동적 콘텐츠 추가** 대화 상자가 열리면 **숨기기** 를 선택 하 여 닫습니다. 이 자습서에서는 동적 콘텐츠를 사용 하지 않습니다.

      ![SMTP 연결 전자 메일 필드 입력](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. **저장** 을 선택 하 여 SMTP 연결을 저장 합니다.

1. 필드 전자 메일 공급자와의 연결을 설정 하 고 다시 사용 하도록 설정 하려면 SSL을 사용 하지 않도록 설정 해야 하는 경우 다음 단계를 수행 합니다.

   1. **논리 앱** 창의 **개발 도구**아래에서 **API 연결**을 선택 합니다.

   1. API 연결 목록에서 SMTP 연결을 선택 합니다.

   1. **SMTP Api 연결** 창의 **일반**에서 **API 연결 편집**을 선택 합니다.

   1. **API 연결 편집** 창에서 **SSL 사용**을 선택 하 고 전자 메일 계정에 대 한 암호를 다시 입력 하 고 **저장**을 선택 합니다.

      ![Azure Portal에서 논리 앱의 SMTP API 연결 편집](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

이제 논리 앱이 Service Bus 큐에서 온도 경고를 처리 하 고 전자 메일 계정으로 알림을 보낼 준비가 되었습니다.

## <a name="test-the-logic-app"></a>논리 앱 테스트

1. 장치에서 클라이언트 응용 프로그램을 시작 합니다.

1. 물리적 장치를 사용 하는 경우 온도가 30도를 초과 하는 경우에만 열 센서 근처의 열 원본을 신중 하 게 가져옵니다. 온라인 시뮬레이터를 사용 하는 경우 클라이언트 코드는 30 C를 초과 하는 원격 분석 메시지를 임의로 출력 합니다.

1. 논리 앱에서 보낸 전자 메일 알림을 받기 시작 해야 합니다.

   > [!NOTE]
   > 전자 메일을 보낸 사람이 본인이라는 것을 확인하기 위해 전자 메일 서비스 공급자가 발신자의 신분을 확인해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub와 사서함을 연결하여 온도를 모니터링하고 알림을 보내는 논리 앱을 성공적으로 만들었습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
