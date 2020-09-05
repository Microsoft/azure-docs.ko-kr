---
title: Azure Notification Hubs에서 삭제 된 알림 진단
description: Azure Notification Hubs의 삭제된 알림과 관련된 일반적인 문제를 진단하는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: Mobile
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: b5c89a2a-63b8-46d2-bbed-924f5a4cce61
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: NA
ms.devlang: multiple
ms.topic: article
ms.date: 02/25/2020
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 04/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: b5139f75084eb0646db2fc8b05b04aaf3ddb2a12
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89010786"
---
# <a name="diagnose-dropped-notifications-in-azure-notification-hubs"></a>Azure Notification Hubs에서 삭제 된 알림 진단

Azure Notification Hubs에 대 한 일반적인 질문은 응용 프로그램의 알림이 클라이언트 장치에 표시 되지 않는 경우 문제를 해결 하는 방법입니다. 고객은 알림이 삭제 된 위치 및 이유를 파악 하 고 문제를 해결 하는 방법을 원합니다. 이 문서에서는 알림이 삭제되거나 디바이스에서 알림을 받지 못하는 이유를 알아봅니다. 또한 근본 원인을 확인 하는 방법에 대해서도 설명 합니다.

먼저 Notification Hubs가 디바이스에 알림을 푸시하는 방법을 이해해야 합니다.

![Notification Hubs 아키텍처][0]

일반적인 알림 보내기 흐름에서는 메시지가 *애플리케이션 백 엔드*에서 Notification Hubs로 전송됩니다. Notification Hubs 모든 등록을 처리 합니다. 구성 된 태그 및 태그 식을 고려 하 여 대상을 확인 합니다. 대상은 푸시 알림을 수신 해야 하는 등록입니다. 이러한 등록은 지원 되는 플랫폼 (Android, Baidu (중국의 Android 장치), 화재 OS (Amazon) iOS, Windows 및 Windows Phone 중 하나로 확장 될 수 있습니다.

대상이 설정되면 Notification Hubs는 디바이스 플랫폼에 대한 *푸시 알림 서비스*에 알림을 푸시합니다. IOS 및 macOS 용 APNs (Apple Push Notification service)와 Android 장치용 FCM (Firebase Cloud Messaging)를 예로 들 것입니다. Notification Hubs는 여러 등록 일괄 처리에 걸쳐 분할된 알림을 푸시합니다. Azure Portal에서 설정한 자격 증명을 기반으로 하는 각 푸시 알림 서비스를 사용 하 여 **알림 허브 구성**에서 인증 합니다. 그러면 푸시 알림 서비스가 각 *클라이언트 디바이스*에 알림을 전달합니다.

알림 배달의 최종 레그는 플랫폼의 푸시 알림 서비스와 장치 사이에 있습니다. 알림 배달은 푸시 알림 프로세스 (클라이언트, 응용 프로그램 백 엔드, Notification Hubs 및 플랫폼의 푸시 알림 서비스)의 네 단계 중 하나에서 실패할 수 있습니다. Notification Hubs 아키텍처에 대한 자세한 내용은 [Notification Hubs 개요]를 참조하세요.

초기 테스트/준비 단계에서 알림을 배달 하지 못할 수 있습니다. 이 단계에서 알림이 삭제될 경우 구성이 잘못된 것일 수 있습니다. 프로덕션 환경에서 알림을 배달 하지 못하는 경우 알림 일부 또는 전체를 삭제할 수 있습니다. 이 경우 더 깊은 응용 프로그램 또는 메시징 패턴 문제가 표시 됩니다.

다음 섹션에서는 일반적인 것부터 드물게 알림이 삭제 될 수 있는 시나리오를 살펴봅니다.

## <a name="notification-hubs-misconfiguration"></a>Notification Hubs 구성 오류

각 푸시 알림 서비스에 알림을 보내려면 Notification Hubs는 응용 프로그램의 컨텍스트에서 자신을 인증 해야 합니다. 대상 플랫폼의 알림 서비스 (Microsoft, Apple, Google 등)를 사용 하 여 개발자 계정을 만들어야 합니다. 그런 다음 대상 PNS 작업 하는 데 사용 하는 토큰 또는 키를 가져오는 OS에 응용 프로그램을 등록 해야 합니다.

Azure Portal에 플랫폼 자격 증명을 추가해야 합니다. 장치에 도달 하는 알림이 없으면 첫 번째 단계는 Notification Hubs에서 올바른 자격 증명을 구성 했는지 확인 하는 것입니다. 자격 증명은 플랫폼별 개발자 계정으로 만든 응용 프로그램과 일치 해야 합니다.

이 프로세스를 완료하는 단계별 지침은 [Azure Notification Hubs 시작]을 참조하세요.

다음과 같은 구성 오류를 확인해야 합니다.

### <a name="notification-hub-name-location"></a>알림 허브 이름 위치

다음 위치에서 알림 허브 이름(오타 없이)이 동일한지 확인합니다.

* 클라이언트에서 등록 하는 위치
* 백 엔드에서 알림을 보내는 위치
* 푸시 알림 서비스 자격 증명을 구성한 위치

클라이언트 및 응용 프로그램 백 엔드에서 올바른 공유 액세스 서명 구성 문자열을 사용 하는지 확인 합니다. 일반적으로 클라이언트에서 **DefaultListenSharedAccessSignature** 를 사용 하 고 응용 프로그램 백 엔드에서 **Defaultfullsharedaccesssignature** 를 사용 해야 합니다. 그러면 Notification Hubs에 알림을 보낼 수 있는 권한이 부여 됩니다.

### <a name="apn-configuration"></a>APN 구성

두 개의 다른 허브를 유지 해야 합니다. 하나는 프로덕션 용 이며 다른 하나는 테스트용입니다. 샌드박스 환경에서 사용 하는 인증서를 프로덕션 환경에서 사용 하는 인증서/허브와는 다른 허브에 업로드 해야 합니다. 같은 허브에 다른 종류의 인증서를 업로드하지 마세요. 그러면 알림이 실패 합니다.

실수로 다른 유형의 인증서를 동일한 허브에 업로드 하는 경우 허브를 삭제 하 고 새 허브를 사용 하 여 새로 시작 해야 합니다. 어떤 이유로 든 허브를 삭제할 수 없는 경우 허브에서 기존 등록을 모두 삭제 해야 합니다.

### <a name="fcm-configuration"></a>FCM 구성

1. Firebase에서 가져온 *서버 키* 가 Azure Portal에 등록 한 서버 키와 일치 하는지 확인 합니다.

   ![Firebase 서버 키][3]

2. 클라이언트에서 **프로젝트 ID**를 구성했는지 확인합니다. **프로젝트 ID** 값은 Firebase 대시보드에서 얻을 수 있습니다.

   ![Firebase 프로젝트 ID][1]

## <a name="application-issues"></a>애플리케이션 문제

### <a name="tags-and-tag-expressions"></a>태그 및 태그 식

태그 또는 태그 식을 사용 하 여 대상 그룹을 분할 하는 경우 알림을 보낼 때 대상을 찾을 수 없습니다. 이 오류는 송신 호출에서 지정 된 태그 또는 태그 식을 기반으로 합니다.

등록을 검토 하 여 알림을 보낼 때 태그가 일치 하는지 확인 합니다. 그런 다음 해당 등록이 있는 클라이언트 에서만 알림 수신을 확인 합니다.

예를 들어 Notification Hubs를 사용 하는 모든 등록이 "정치" 태그를 사용 한다고 가정 합니다. 그런 다음 "스포츠" 태그가 포함 된 알림을 보내면 모든 장치로 알림이 전송 되지 않습니다. 복합 사례에는 "태그 A" *또는* "태그 b"를 사용 하 여 등록 한 태그 식이 포함 될 수 있지만, "tag a && tag b"를 대상으로 지정 했습니다. 이 문서의 뒷부분에 있는 자체 진단 팁 섹션에서는 등록과 해당 태그를 검토 하는 방법을 보여 줍니다.

### <a name="template-issues"></a>템플릿 문제

템플릿을 사용하는 경우 [템플릿]에 설명된 지침을 따릅니다.

### <a name="invalid-registrations"></a>잘못된 등록

알림 허브가 올바르게 구성 되었고 태그 또는 태그 식이 올바르게 사용 된 경우 유효한 대상이 발견 됩니다. 이러한 대상에 알림을 보내야 합니다. 그런 다음 Notification Hubs가 여러 일괄 처리 프로세스를 병렬로 시작합니다. 각 일괄 처리는 등록 집합에 메시지를 보냅니다.

> [!NOTE]
> Notification Hubs 일괄 처리를 병렬로 처리 하므로 알림이 배달 되는 순서는 보장 되지 않습니다.

Notification Hubs은 "최대 한 번" 메시지 배달 모델에 대해 최적화 됩니다. 우리는 디바이스에 알림이 두 번 이상 전달되지 않도록 중복 제거를 시도합니다. 등록은 푸시 알림 서비스로 전송 되기 전에 장치 식별자 당 하나의 메시지만 전송 되도록 확인 됩니다.

각 일괄 처리는 푸시 알림 서비스로 전송 되며, 그 후에는 등록을 허용 하 고 유효성을 검사 합니다. 이 과정에서 푸시 알림 서비스는 하나 이상의 등록에 대 한 오류를 일괄 처리로 검색할 수 있습니다. 그러면 푸시 알림 서비스에서 Notification Hubs 하는 오류를 반환 하 고 프로세스가 중지 됩니다. 푸시 알림 서비스는 해당 일괄 처리를 완전히 삭제합니다. 이는 특히 TCP 스트림 프로토콜을 사용 하는 APNs에 적용 됩니다.

이 경우 오류가 발생 한 등록이 데이터베이스에서 제거 됩니다. 그런 다음 해당 일괄 처리의 나머지 디바이스에 알림을 다시 배달합니다.

등록에 대 한 실패 한 배달 시도에 대 한 자세한 오류 정보를 얻으려면 메시지 원격 분석 당 Notification Hubs REST Api를 사용 하면 됩니다 [. 알림 메시지 원격 분석](/rest/api/notificationhubs/get-notification-message-telemetry) 및 [PNS 피드백](/previous-versions/azure/reference/mt705560(v=azure.100))을 받습니다. 샘플 코드는 [REST 보내기 예제](https://github.com/Azure/azure-notificationhubs-dotnet/tree/master/Samples/SendRestExample/)를 참조하세요.

## <a name="push-notification-service-issues"></a>푸시 알림 서비스 문제

푸시 알림 서비스는 알림을 받은 후 해당 알림을 장치에 전달 합니다. 이 시점에서 Notification Hubs은 장치에 대 한 알림 배달을 제어 하지 않습니다.

플랫폼 알림 서비스는 강력 하기 때문에 알림은 몇 초 이내에 장치에 도달 하는 경향이 있습니다. 푸시 알림 서비스를 제한 하는 경우 Notification Hubs 지 수 백오프 전략을 적용 합니다. 푸시 알림 서비스가 30 분 동안 도달할 수 없는 상태로 유지 되는 경우 메시지를 영구적으로 만료 하 고 삭제 하는 정책이 있습니다.

푸시 알림 서비스에서 알림을 전달 하려고 하지만 장치가 오프 라인 상태인 경우 푸시 알림 서비스에서 알림을 저장 합니다. 제한 된 시간 동안만 저장 됩니다. 디바이스가 다시 온라인이 되면 디바이스에 알림이 배달됩니다.

각 앱은 최근 알림 하나를 저장 합니다. 장치가 오프 라인 상태인 동안 여러 알림이 전송 되는 경우 새 알림이 발생 하면 마지막 알림이 삭제 됩니다. 최신 알림만 유지 하는 것을 APNs의 *결합* 및 FCM *축소* 라고 합니다. (FCM는 축소 키를 사용 합니다.) 장치가 오랫동안 오프 라인 상태로 유지 되 면 장치에 대해 저장 된 알림이 삭제 됩니다. 자세한 내용은 [APNs 개요] 및 [FCM 메시지 정보]를 참조 하세요.

Notification Hubs를 사용 하면 제네릭 SendNotification API를 사용 하 여 HTTP 헤더를 통해 결합 키를 전달할 수 있습니다. 예를 들어 .NET SDK의 경우 `SendNotificationAsync`를 사용합니다. 또한 SendNotification API는 각 푸시 알림 서비스에 있는 그대로 전달 되는 HTTP 헤더를 사용 합니다.

## <a name="self-diagnosis-tips"></a>자체 진단 팁

Notification Hubs에서 삭제 된 알림의 근본 원인을 진단 하는 경로는 다음과 같습니다.

### <a name="verify-credentials"></a>자격 증명 확인

#### <a name="push-notification-service-developer-portal"></a>푸시 알림 서비스 개발자 포털

각 푸시 알림 서비스 개발자 포털(APNs, FCM, Windows Notification Service 등)에서 자격 증명을 확인합니다. 자세한 내용은 [자습서: Azure Notification Hubs를 사용 하 여 유니버설 Windows 플랫폼 앱에 알림 보내기](./notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)를 참조 하세요.

#### <a name="azure-portal"></a>Azure portal

푸시 알림 서비스 개발자 포털에서 얻은 자격 증명과 자격 증명을 검토 하 고 일치 시키려면 Azure Portal의 **액세스 정책** 탭으로 이동 합니다.

![Azure Portal 액세스 정책][4]

### <a name="verify-registrations"></a>등록 확인

#### <a name="visual-studio"></a>Visual Studio

Visual Studio에서 서버 탐색기 통해 Azure에 연결 하 여 Notification Hubs를 비롯 한 여러 Azure 서비스를 보고 관리할 수 있습니다. 이 바로 가기는 개발/테스트 환경에 주로 유용 합니다.

![Visual Studio 서버 Explorer][9]

![서버 탐색기](media/notification-hubs-push-notification-fixer/vsserverexplorer2.png)

허브의 모든 등록을 보고 관리할 수 있습니다. 등록은 플랫폼, 네이티브 또는 템플릿 등록, 태그, 푸시 알림 서비스 식별자, 등록 ID 및 만료 날짜를 기준으로 분류 될 수 있습니다. 또한 이 페이지에서 등록을 편집할 수 있습니다. 태그를 편집 하는 데 특히 유용 합니다.

**서버 탐색기**에서 알림 허브를 마우스 오른쪽 단추로 클릭 하 고 **진단**을 선택 합니다. 

![Visual Studio 서버 탐색기: 진단 메뉴](./media/notification-hubs-push-notification-fixer/diagnose-menu.png)

다음 페이지가 표시 됩니다.

![Visual Studio: 진단 페이지](./media/notification-hubs-push-notification-fixer/diagnose-page.png)

**장치 등록** 페이지로 전환 합니다.

![Visual Studio: 장치 등록](./media/notification-hubs-push-notification-fixer/VSRegistrations.png)

**테스트 보내기** 페이지를 사용 하 여 테스트 알림 메시지를 보낼 수 있습니다.

![Visual Studio: 테스트 보내기](./media/notification-hubs-push-notification-fixer/test-send-vs.png)

> [!NOTE]
> Visual Studio를 사용 하 여 개발/테스트 중에만 등록 수를 제한 하 여 등록을 편집 합니다. 등록을 대량으로 편집 해야 하 [는 경우 방법: 대량 등록 내보내기 및 수정](/previous-versions/azure/azure-services/dn790624(v=azure.100))에 설명 된 등록 내보내기 및 가져오기 기능을 사용 하는 것이 좋습니다.

#### <a name="service-bus-explorer"></a>Service Bus 탐색기

많은 고객이 [Service Bus 탐색기](https://github.com/paolosalvatori/ServiceBusExplorer) 를 사용 하 여 notification hubs를 보고 관리할 수 있습니다. Service Bus 탐색기는 오픈 소스 프로젝트입니다. 

### <a name="verify-message-notifications"></a>알림 메시지 확인

#### <a name="azure-portal"></a>Azure portal

서비스 백 엔드를 실행하지 않고 클라이언트에 테스트 알림을 보내려면 **지원 + 문제 해결**에서 **테스트 보내기**를 선택합니다.

![Azure의 테스트 보내기 기능][7]

#### <a name="visual-studio"></a>Visual Studio

Visual Studio에서 테스트 알림을 보낼 수도 있습니다.

![Visual Studio의 테스트 보내기 기능][10]

Visual Studio 서버 탐색기에서 Notification Hubs를 사용하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Notification hubs에 대 한 장치 등록을 보는 방법](/previous-versions/windows/apps/dn792122(v=win.10))
* [심층 분석: Visual Studio 2013 업데이트 2 RC 및 Azure SDK 2.3]
* [Visual Studio 2013 업데이트 3 및 Azure SDK 2.4 릴리스 발표]

### <a name="debug-failed-notifications-and-review-notification-outcome"></a>실패한 알림 디버그 및 알림 결과 검토

#### <a name="enabletestsend-property"></a>EnableTestSend 속성

Notification Hubs를 통해 알림을 보낼 때 알림은 처음에 큐에 대기 됩니다. Notification Hubs는 올바른 대상을 결정한 후 푸시 알림 서비스에 알림을 보냅니다. REST API 또는 클라이언트 Sdk 중 하나를 사용 하는 경우 보내기 호출을 반환 하는 것은 메시지가 Notification Hubs를 사용 하 여 큐에 대기 한다는 것만을 의미 합니다. 궁극적으로는 Notification Hubs 푸시 알림 서비스에 알림을 보낼 때 발생 하는 상황에 대 한 통찰력을 제공 하지 않습니다.

알림이 클라이언트 장치에 도착 하지 않으면 Notification Hubs 푸시 알림 서비스에 배달 하려고 할 때 오류가 발생 했을 수 있습니다. 예를 들어 페이로드 크기가 푸시 알림 서비스에서 허용되는 최대값을 초과하거나 Notification Hubs에서 구성된 자격 증명이 잘못된 것일 수 있습니다.

푸시 알림 서비스 오류에 대한 정보를 얻으려면 [EnableTestSend] 속성을 사용합니다. 이 속성은 포털 또는 Visual Studio 클라이언트에서 테스트 메시지를 보낼 때 자동으로 활성화됩니다. 이 속성을 사용 하 여 자세한 디버깅 정보 및 Api를 통해 볼 수도 있습니다. 현재 이 속성은 .NET SDK에서 사용할 수 있습니다. 결국 모든 클라이언트 Sdk에 추가 됩니다.

REST 호출에 `EnableTestSend` 속성을 사용하려면 송신 호출 끝에 *test*라는 쿼리 문자열 매개 변수를 추가합니다. 예를 들어:

```text
https://mynamespace.servicebus.windows.net/mynotificationhub/messages?api-version=2013-10&test
```

#### <a name="net-sdk-example"></a>.NET SDK 예제

다음은 .NET SDK를 사용하여 네이티브 팝업(토스트 알림) 알림을 보내는 예제입니다.

```csharp
NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName);
var result = await hub.SendWindowsNativeNotificationAsync(toast);
Console.WriteLine(result.State);
```

실행 마지막에 `result.State`가 간단히 `Enqueued`를 명시합니다. 결과는 푸시 알림에 발생 한 상황에 대 한 통찰력을 제공 하지 않습니다.

다음으로 `EnableTestSend` 부울 속성을 사용할 수 있습니다. `NotificationHubClient`를 초기화할 때 `EnableTestSend` 속성을 사용하여 알림을 보낼 때 발생하는 푸시 알림 서비스 오류에 대한 자세한 상태를 가져올 수 있습니다. 푸시 알림 서비스에 알림을 배달 하기 위해 먼저 Notification Hubs 필요 하기 때문에 send 호출은 반환 시간이 더 오래 걸립니다.

```csharp
    bool enableTestSend = true;
    NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString(connString, hubName, enableTestSend);

    var outcome = await hub.SendWindowsNativeNotificationAsync(toast);
    Console.WriteLine(outcome.State);

    foreach (RegistrationResult result in outcome.Results)
    {
        Console.WriteLine(result.ApplicationPlatform + "\n" + result.RegistrationId + "\n" + result.Outcome);
    }
```

#### <a name="sample-output"></a>샘플 출력

```text
DetailedStateAvailable
windows
7619785862101227384-7840974832647865618-3
The Token obtained from the Token Provider is wrong
```

이 메시지는 Notification Hubs에 구성 된 자격 증명이 잘못 되었거나 허브의 등록에 문제가 있음을 나타냅니다. 이 등록을 삭제 하 고 클라이언트에서 메시지를 보내기 전에 등록을 다시 만들도록 합니다.

> [!NOTE]
> `EnableTestSend` 속성 사용은 철저하게 제한됩니다. 이 옵션은 개발/테스트 환경 및 제한 된 등록 집합 에서만 사용 합니다. 디버그 알림은 10 개의 장치로만 전송 됩니다. 또한 분당 10 분 마다 디버그 전송 처리에 제한이 있습니다.

### <a name="review-telemetry"></a>원격 분석 검토

#### <a name="azure-portal"></a>Azure portal

포털에서 알림 허브의 모든 활동에 대한 간략한 개요를 확인할 수 있습니다.

1. **개요** 탭에서 등록, 알림 및 오류 집계를 플랫폼별로 볼 수 있습니다.

   ![Notification Hubs 개요 대시보드][5]

2. **모니터** 탭에서 세부 분석을 위한 다른 여러 플랫폼별 메트릭을 추가할 수 있습니다. Notification Hubs 푸시 알림 서비스에 알림을 보내려고 시도할 때 반환 되는 오류를 구체적으로 확인할 수 있습니다.

   ![Azure Portal 활동 로그][6]

3. 먼저 **들어오는 메시지**, **등록 작업** 및 **성공한 알림**을 검토합니다. 그런 다음 플랫폼별 탭으로 이동하여 푸시 알림 서비스와 관련된 오류를 검토합니다.

4. 알림 허브에 대한 인증 설정이 올바르지 않으면 **PNS 인증 오류**가 나타납니다. 푸시 알림 서비스 자격 증명을 확인 하는 것이 좋습니다.

#### <a name="programmatic-access"></a>프로그래밍 방식 액세스

프로그래밍 방식 액세스에 대 한 자세한 내용은 [프로그래밍 방식 액세스](/previous-versions/azure/azure-services/dn458823(v=azure.100))를 참조 하세요.

> [!NOTE]
> API를 통해 등록 및 원격 분석 액세스 내보내기 및 가져오기 같은 여러 원격 분석 관련 기능은 표준 서비스 계층에서만 사용할 수 있습니다. 무료 또는 기본 서비스 계층에서 이러한 기능을 사용 하려고 하면 SDK를 사용 하는 경우 예외 메시지가 표시 됩니다. REST Api에서 직접 기능을 사용 하는 경우 HTTP 403 (사용할 수 없음) 오류가 발생 합니다.
>
> 원격 분석 관련 기능을 사용 하려면 먼저 Azure Portal에서 표준 서비스 계층을 사용 하 고 있는지 확인 합니다.  

<!-- IMAGES -->
[0]: ./media/notification-hubs-push-notification-fixer/Architecture.png
[1]: ./media/notification-hubs-push-notification-fixer/FCMConfigure.png
[3]: ./media/notification-hubs-push-notification-fixer/FCMServerKey.png
[4]: ../../includes/media/notification-hubs-portal-create-new-hub/notification-hubs-connection-strings-portal.png
[5]: ./media/notification-hubs-push-notification-fixer/PortalDashboard.png
[6]: ./media/notification-hubs-push-notification-fixer/PortalAnalytics.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[8]: ./media/notification-hubs-push-notification-fixer/VSRegistrations.png
[9]: ./media/notification-hubs-push-notification-fixer/vsserverexplorer.png
[10]: ./media/notification-hubs-push-notification-fixer/VSTestNotification.png

<!-- LINKS -->
[Notification Hubs 개요]: notification-hubs-push-notification-overview.md
[Azure Notification Hubs 시작]: notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[템플릿]: /previous-versions/azure/azure-services/dn530748(v=azure.100)
[APNs 개요]: https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html
[FCM 메시지 정보]: https://firebase.google.com/docs/cloud-messaging/concept-options
[Export and modify registrations in bulk]: /previous-versions/azure/azure-services/dn790624(v=azure.100)
[Service Bus Explorer code]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Explorer-f2abca5a
[View device registrations for notification hubs]: /previous-versions/windows/apps/dn792122(v=win.10)
[심층 분석: Visual Studio 2013 업데이트 2 RC 및 Azure SDK 2.3]: https://azure.microsoft.com/blog/2014/04/09/deep-dive-visual-studio-2013-update-2-rc-and-azure-sdk-2-3/#NotificationHubs
[Visual Studio 2013 업데이트 3 및 Azure SDK 2.4 릴리스 발표]: https://azure.microsoft.com/blog/2014/08/04/announcing-release-of-visual-studio-2013-update-3-and-azure-sdk-2-4/
[EnableTestSend]: /dotnet/api/microsoft.azure.notificationhubs.notificationhubclient.enabletestsend?view=azure-dotnet
[Programmatic telemetry access]: /previous-versions/azure/azure-services/dn458823(v=azure.100)
