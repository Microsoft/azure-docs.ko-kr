---
title: Azure Logic Apps를 사용하여 IoT 원격 모니터링 및 알림 | Microsoft Docs
description: Azure Logic Apps을 사용하여 IoT Hub에서 IoT 온도를 모니터링하고 감지된 이상에 대한 전자 메일 알림을 자동으로 사서함에 보낼 수 있습니다.
author: robinsh
keywords: iot 모니터링, iot 알림, iot 온도 모니터링
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/19/2019
ms.author: robinsh
ms.openlocfilehash: 26637468f44e12f7ad66f907e0f6be3d907e578f
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126210"
---
# <a name="iot-remote-monitoring-and-notifications-with-azure-logic-apps-connecting-your-iot-hub-and-mailbox"></a>Azure Logic Apps으로 IoT Hub와 사서함을 연결하여 IoT 원격 모니터링 및 알림

![종단 간 다이어그램](media/iot-hub-monitoring-notifications-with-azure-logic-apps/iot-hub-e2e-logic-apps.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

[Azure Logic Apps](https://docs.microsoft.com/azure/logic-apps/) 도움이 될 수 있습니다 온-프레미스 간의 워크플로 오케스트레이션 하 고 클라우드 서비스를 하나 이상의 기업 및 다양 한 프로토콜입니다. 그 다음에는 조건 및 반복기와 같은 기본 제공 컨트롤을 사용 하 여 시퀀싱 할 수 있는 하나 이상의 작업 트리거를 사용 하 여 논리 앱을 시작 합니다. 이러한 유연성을이 통해 Logic Apps IoT 모니터링 시나리오에 적합 한 IoT 솔루션을 만듭니다. 예를 들어, IoT Hub 끝점에서 장치에서 원격 분석 데이터를 Azure Storage blob에서 데이터 웨어하우스 데이터 이상에 대 한 경고, 장치 오류를 보고 하는 경우 기술 지원 엔지니어가 방문을 예약 하는 보내도록 전자 메일 알림을 보내는 논리 앱 워크플로 시작할 수 있습니다. 등입니다.

## <a name="what-you-learn"></a>학습 내용

IoT Hub와 사서함을 연결하여 온도를 모니터링하고 알림을 보내는 논리 앱을 만드는 방법을 배웁니다.

응용 프로그램 속성을 설정 하는 장치에서 실행 되는 클라이언트 코드 `temperatureAlert`의 모든 원격 분석 IoT hub로 보내는 메시지. 클라이언트 코드는 온도 30c를 감지 하는 경우에이 속성을 설정 `true`이 고, 그렇지 않으면 속성을 설정 합니다. `false`합니다.

이 항목에서는 설정한 라우팅 IoT hub에는 메시지를 보내도록 `temperatureAlert = true` Service bus 끝점 설정는 Service Bus 끝점에 도착 하는 메시지에 대해 트리거 및 알림 전자 메일을 보내는 논리 앱을 등록 합니다.

## <a name="what-you-do"></a>수행할 작업

* Service Bus 네임 스페이스 만들기 및 Service Bus 큐를 추가 합니다.
* 메시지를 라우팅할 온도 경고를 Service Bus 큐를 포함 하는 IoT hub에 사용자 지정 끝점 및 라우팅 규칙을 추가 합니다.
* 만들기, 구성 및 Service Bus 큐에서 메시지를 사용 하 여 원하는 받는 사람에 게 알림 전자 메일을 보내는 논리 앱을 테스트 합니다.

## <a name="what-you-need"></a>필요한 항목

* 완료 합니다 [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 자습서 또는 장치 자습서; 중 하나 예를 들어 [node.js 사용 하 여 Raspberry Pi](iot-hub-raspberry-pi-kit-node-get-started.md)합니다. 이러한 다음 요구 사항을 다룹니다.

  * 활성 Azure 구독.
  * 구독 중인 Azure IoT Hub
  * Azure IoT hub로 원격 분석 메시지를 전송 하는 장치에서 실행 되는 클라이언트 응용 프로그램.

## <a name="create-service-bus-namespace-and-queue"></a>Service Bus 네임 스페이스 및 큐 만들기

Service Bus 네임스페이스 및 큐를 만듭니다. 이 항목의 뒷부분에 논리 앱에서 선택 됩니다 및 알림 전자 메일을 보내도록 트리거하는 Service Bus 큐에 온도 경고를 포함 하는 직접 메시지를 IoT hub에서 라우팅 규칙을 만듭니다.

### <a name="create-a-service-bus-namespace"></a>Service Bus 네임스페이스 만들기

1. 에 [Azure portal](https://portal.azure.com/)를 선택 **+ 리소스 만들기** > **Integration** > **Service Bus**합니다.

1. 에 **네임 스페이스 만들기** 창 다음 정보를 제공 합니다.

   **이름**: Service bus 네임 스페이스의 이름입니다. 네임 스페이스는 Azure에서 고유 해야 합니다.

   **가격 책정 계층**: 선택 **기본** 드롭 다운 목록에서. 이 자습서에서는 기본 계층이면 충분합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **Location**: IoT hub를 사용 하는 동일한 위치를 사용 합니다.

   ![Azure Portal에서 Service Bus 네임스페이스 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/1-create-service-bus-namespace-azure-portal.png)

1. **만들기**를 선택합니다. 배포가 다음 단계로 넘어가기 전에 완료 될 때까지 기다립니다.

### <a name="add-a-service-bus-queue-to-the-namespace"></a>네임 스페이스에 Service Bus 큐 추가

1. Service Bus 네임 스페이스를 엽니다. 가장 쉬운 방법은 Service Bus 네임 스페이스를 이동 하려면 선택 하는 것 **리소스 그룹** 리소스 창에서 리소스 그룹을 선택한 다음 리소스 목록에서 Service Bus 네임 스페이스를 선택 합니다.

1. 에 **Service Bus Namespace** 창 **+ 큐**합니다.

1. 큐에 대 한 이름을 입력 하 고 선택한 **만들기**합니다. 큐에 성공적으로 생성 된 때를 **큐 만들기** 창을 닫습니다.

   ![Azure Portal에서 Service Bus 큐 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-queue.png)

1. 다시 합니다 **Service Bus Namespace** 창 아래에 있는 **엔터티**를 선택 **큐**합니다. 목록에서 Service Bus 큐를 열고 선택한 **공유 액세스 정책** > **+ 추가**합니다.

1. 검사 정책에 대 한 이름을 입력 **관리**를 선택한 후 **만들기**합니다.

   ![Azure portal에서 service bus 큐 정책 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/2-add-service-bus-queue-azure-portal.png)

## <a name="add-a-custom-endpoint-and-routing-rule-to-your-iot-hub"></a>IoT hub에 사용자 지정 끝점 및 라우팅 규칙 추가

IoT hub에 Service Bus 큐에 대 한 사용자 지정 끝점을 추가 하 고 여기서는 픽업 됩니다 논리 앱에서 해당 끝점에 온도 경고를 포함 하는 메시지를 보내는 메시지 라우팅 규칙을 만듭니다. 라우팅 규칙을 라우팅 쿼리를 사용 하 여 `temperatureAlert = "true"`의 값을 기반으로 메시지를 전달 하는 `temperatureAlert` 장치에서 실행 중인 클라이언트 코드로 설정 하는 응용 프로그램 속성입니다. 자세한 내용은 참조 하세요 [메시지 속성을 기반으로 하는 라우팅 쿼리 메시지](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-properties)합니다.

### <a name="add-a-custom-endpoint"></a>사용자 지정 끝점을 추가 합니다.

1. IoT Hub를 엽니다. IoT hub에 연결할 수는 가장 쉬운 방법은 선택 하는 것 **리소스 그룹** 리소스 창에서 리소스 그룹을 선택한 다음 리소스 목록에서 IoT hub를 선택 합니다.

1. 아래 **Messaging**를 선택 **메시지 라우팅**합니다. 에 **메시지 라우팅** 창 합니다 **사용자 지정 끝점** 탭을 선택한 다음 **+ 추가**합니다. 드롭다운 목록에서 선택 **Service bus 큐**합니다.

   ![Azure Portal에서 IoT Hub에 엔드포인트 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-iot-hub-custom-endpoint.png)

1. 에 **service bus 끝점을 추가** 창 다음 정보를 입력 합니다.

   **끝점 이름**: 엔드포인트의 이름입니다.

   **Service Bus 네임스페이스**: 만든 네임 스페이스를 선택 합니다.

   **Service bus 큐**: 만든 큐를 선택 합니다.

   ![Azure Portal에서 IoT Hub에 엔드포인트 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/3-add-iot-hub-endpoint-azure-portal.png)

1. **만들기**를 선택합니다. 끝점을 성공적으로 만들어진 후에 다음 단계로 진행 합니다.

### <a name="add-a-routing-rule"></a>라우팅 규칙 추가

1. 다시 합니다 **메시지 라우팅** 창 합니다 **경로** 탭을 선택한 다음 **+ 추가**합니다.

1. 에 **경로 추가** 창 다음 정보를 입력 합니다.

   **이름**: 라우팅 규칙의 이름입니다.

   **엔드포인트**: 만든 끝점을 선택 합니다.

   **데이터 원본**: 선택 **장치 원격 분석 메시지**합니다.

   **라우팅 쿼리**: [https://slack.botframework.com](`temperatureAlert = "true"`) 을 입력합니다.

   ![Azure Portal에서 라우팅 규칙 추가](media/iot-hub-monitoring-notifications-with-azure-logic-apps/4-add-routing-rule-azure-portal.png)

1. **저장**을 선택합니다. 닫을 수 있습니다 합니다 **메시지 라우팅** 창입니다.

## <a name="create-and-configure-a-logic-app"></a>논리 앱 만들기 및 구성

이전 섹션에서는 메시지를 라우팅할 온도 경고를 Service Bus 큐를 포함 하 여 IoT hub를 설정 합니다. 이제 논리 앱을 Service Bus 큐를 모니터링 하 고 큐에 메시지가 추가 될 때마다 전자 메일 알림을 보내도록를 설정할 수도 있습니다.

### <a name="create-a-logic-app"></a>논리 앱 만들기

1. 선택 **리소스 만들기** > **Integration** > **논리 앱**합니다.

1. 다음 정보를 입력합니다.

   **이름**: 논리 앱의 이름입니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **Location**: IoT hub를 사용 하는 동일한 위치를 사용 합니다.

   ![Azure portal에서 논리 앱 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-a-logic-app.png)

1. **만들기**를 선택합니다.

### <a name="configure-the-logic-app-trigger"></a>논리 앱 트리거 구성

1. 논리 앱을 엽니다. 논리 앱으로 이동 하는 가장 쉬운 방법은 선택 하는 것 **리소스 그룹** 리소스 창에서 리소스 그룹을 선택한 다음 리소스 목록에서 논리 앱을 선택 합니다. 논리 앱을 선택 하면 논리 앱 디자이너가 열립니다.

1. 논리 앱 디자이너에서 아래로 스크롤하여 **템플릿을** 선택한 **빈 Logic App**합니다.

   ![Azure Portal에서 빈 논리 앱 시작](media/iot-hub-monitoring-notifications-with-azure-logic-apps/5-start-with-blank-logic-app-azure-portal.png)

1. 선택 된 **모든** 탭을 선택한 다음 **Service Bus**합니다.

   ![Service Bus를 선택하여 Azure Portal에서 논리 앱 만들기 시작](media/iot-hub-monitoring-notifications-with-azure-logic-apps/6-select-service-bus-when-creating-blank-logic-app-azure-portal.png)

1. 아래 **트리거**를 선택 **(자동 완성) 큐에 하나 이상의 메시지가 도착 하는 경우**합니다.

   ![Azure portal에서 논리 앱에 대 한 트리거를 선택 합니다.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/select-service-bus-trigger.png)

1. Service Bus 연결을 만듭니다.
   1. 연결 이름을 입력 하 고 목록에서 Service Bus 네임 스페이스를 선택 합니다. 다음 화면이 열립니다.

      ![Azure Portal에서 논리 앱에 대한 Service Bus 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/create-service-bus-connection-1.png)

   1. Service bus 정책 (RootManageSharedAccessKey)를 선택 합니다. 선택한 **만들기**합니다.

      ![Azure Portal에서 논리 앱에 대한 Service Bus 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/7-create-service-bus-connection-in-logic-app-azure-portal.png)

   1. 마지막 화면에서에 대 한 **큐 이름**, 드롭다운 목록에서 만든 큐를 선택 합니다. 입력 `175` 에 대 한 **최대 메시지 수**입니다.

      ![논리 앱에서 Service Bus 연결의 최대 메시지 수 지정](media/iot-hub-monitoring-notifications-with-azure-logic-apps/8-specify-maximum-message-count-for-service-bus-connection-logic-app-azure-portal.png)

   1. 선택 **저장할** 변경 내용을 저장 하려면 Logic Apps 디자이너의 맨 위에 있는 메뉴.

### <a name="configure-the-logic-app-action"></a>논리 앱 작업 구성

1. SMTP 서비스 연결을 만듭니다.

   1. **새 단계**를 선택합니다. **작업 선택**를 선택 합니다 **모든** 탭 합니다.

   1. 형식 `smtp` 검색 상자에서 선택 합니다 **SMTP** 검색 결과에서 서비스를 선택한 **전자 메일 보내기**합니다.

      ![Azure Portal에서 논리 앱에 SMTP 연결 만들기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/9-create-smtp-connection-logic-app-azure-portal.png)

   1. 사서함에 대 한 SMTP 정보를 입력 한 다음 선택 **만들기**합니다.

      ![Azure Portal에서 논리 앱에 SMTP 연결 정보 입력](media/iot-hub-monitoring-notifications-with-azure-logic-apps/10-enter-smtp-connection-info-logic-app-azure-portal.png)

      [Hotmail/Outlook.com](https://support.office.com/article/Add-your-Outlook-com-account-to-another-mail-app-73f3b178-0009-41ae-aab1-87b80fa94970), [Gmail](https://support.google.com/a/answer/176600?hl=en) 및 [Yahoo Mail](https://help.yahoo.com/kb/SLN4075.html)에 대한 SMTP 정보를 가져옵니다.

      > [!NOTE]
      > 연결을 설정 하는 SSL을 사용 하지 않도록 설정 해야 합니다. 이 경우 연결이 설정 된 후 SSL을 다시 사용 하도록 설정 하려는 경우이 섹션의 끝에 선택적 단계를 참조 하세요.

   1. **새 매개 변수 추가** 드롭 다운에서는 **전자 메일 보내기** 단계 선택 **에서**, **에**, **주체**하 고 **본문**합니다. 클릭 하거나 아무 곳 이나 선택 상자를 닫으려면 화면에서 누릅니다.

      ![SMTP 연결 전자 메일 필드를 선택 합니다.](media/iot-hub-monitoring-notifications-with-azure-logic-apps/smtp-connection-choose-fields.png)

   1. **보내는 사람** 및 **받는 사람**의 전자 메일 주소를 입력하고, **제목** 및 **본문**에 `High temperature detected`를 입력합니다. 경우는 **앱 및이 흐름에서 사용 되는 커넥터에서 동적 콘텐츠 추가** 대화 상자가 열리고, 선택 **숨기기** 를 닫습니다. 이 자습서에서 동적 콘텐츠를 사용 하지 마세요.

      ![SMTP 연결 전자 메일 필드 채우기](media/iot-hub-monitoring-notifications-with-azure-logic-apps/fill-in-smtp-connection-fields.png)

   1. 선택 **저장할** SMTP 연결을 저장 합니다.

1. (선택 사항) 전자 메일 공급자를 사용 하 여 연결을 다시 사용 하도록 설정 하는 SSL을 사용 하지 않도록 설정 해야 하는 경우 다음이 단계를 수행 합니다.

   1. 에 **논리 앱** 창 아래에 있는 **개발 도구**를 선택 **API 연결**합니다.

   1. API 연결 목록에서 SMTP 연결을 선택 합니다.

   1. 에 **smtp API 연결** 창 아래에 있는 **일반**를 선택 **API 연결 편집**합니다.

   1. 에 **API 연결 편집** 창 **SSL 사용?** 전자 메일 계정의 암호를 다시 입력 하 고 선택 **저장**합니다.

      ![Azure portal에서 논리 앱에 SMTP API 연결 편집](media/iot-hub-monitoring-notifications-with-azure-logic-apps/re-enable-smtp-connection-ssl.png)

논리 앱 이메일 계정에 게 알림을 보내고 Service Bus 큐에서 온도 경고를 처리할 준비가 되었습니다.

## <a name="test-the-logic-app"></a>논리 앱 테스트

1. 장치에서 클라이언트 응용 프로그램을 시작 합니다.

1. 물리적 장치를 사용 하는 경우 신중 하 게 가져올 열 센서 가까이 열 원본 온도 섭씨 30을 초과할 때까지 클라이언트 코드는 30 3. 초과 하는 원격 분석 메시지를 출력 임의로 온라인 시뮬레이터를 사용 하는 경우

1. 논리 앱에서 보낸 전자 메일 알림을 수신 해야 합니다.

   > [!NOTE]
   > 전자 메일을 보낸 사람이 본인이라는 것을 확인하기 위해 전자 메일 서비스 공급자가 발신자의 신분을 확인해야 할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

IoT Hub와 사서함을 연결하여 온도를 모니터링하고 알림을 보내는 논리 앱을 성공적으로 만들었습니다.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
