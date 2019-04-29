---
title: 'Azure Notification Hubs: FAQ(질문과 대답) | Microsoft Docs'
description: Notification Hubs에서 솔루션을 디자인/구현하는 방법과 관련한 FAQ
services: notification-hubs
documentationcenter: mobile
author: jwargo
manager: patniko
editor: spelluru
keywords: 푸시 알림, 푸시 알림, iOS 푸시 알림, Android 푸시 알림, iOS 푸시, Android 푸시
ms.assetid: 7b385713-ef3b-4f01-8b1f-ffe3690bbd40
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 03/11/2019
ms.author: jowargo
ms.openlocfilehash: 642e586371a0ce6abe362f8b10b54bfd8baff678
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60872040"
---
# <a name="push-notifications-with-azure-notification-hubs-frequently-asked-questions"></a>Azure Notification Hubs를 사용하는 푸시 알림: 질문과 대답

## <a name="general"></a>일반

### <a name="what-is-the-resource-structure-of-notification-hubs"></a>Notification Hubs의 리소스 구조는 무엇입니까?

Azure Notification Hubs에는 허브 및 네임스페이스라는 두 개의 리소스 수준이 있습니다. 허브는 한 앱의 교차 플랫폼 푸시 정보를 저장할 수 있는 단일 푸시 리소스입니다. 네임스페이스는 한 지역의 허브 컬렉션입니다.

권장되는 매핑은 하나의 네임스페이스를 하나의 앱과 일치시키는 것입니다. 네임스페이스 내에 프로덕션 앱과 작동하는 프로덕션 허브, 테스트 앱과 작동하는 테스트 허브 등이 있을 수 있습니다.

### <a name="what-is-the-price-model-for-notification-hubs"></a>Notification Hubs의 가격 책정 모델은 무엇인가요?

최신 가격 책정 정보는 [Notification Hubs 가격 책정] 페이지에서 찾을 수 있습니다. Notification Hubs는 네임스페이스 수준에서 대금이 청구됩니다. (네임스페이스에 대한 정의는 “Notification Hubs의 리소스 구조는 무엇입니까?” 참조) Notification Hubs는 세 가지 계층을 제공합니다.

* **체험**: 이 계층은 푸시 기능을 탐색하기 적절한 시작점입니다. 프로덕션 앱에는 권장되지 않습니다. 네임스페이스마다 매달 500개의 디바이스와 백만 개의 푸시를 Service Level Agreement(서비스 수준 약정) 보장 없이 받게 됩니다.
* **기본**: 이 계층(또는 표준 계층)은 더 작은 프로덕션 앱에 권장됩니다. 네임스페이스마다 매달 200,000개의 디바이스와 천만 개의 푸시를 기본으로 받게 됩니다.
* **표준**: 이 계층은 중간 및 대형 프로덕션 앱에 권장됩니다. 네임스페이스마다 매달 천만 개의 디바이스와 천만 개의 푸시를 기본으로 받게 됩니다. 풍부한 원격 분석 데이터(제공된 푸시 상태에 대한 추가 데이터)를 포함합니다.

표준 계층 기능:

* **다양한 원격 분석**: 메시지 원격 분석 단위로 Notification Hubs를 사용하여 푸시 요청을 추적하고 디버깅을 위해 플랫폼 알림 시스템 피드백을 사용할 수 있습니다.
* **다중 테넌시**: 네임스페이스 수준에서 플랫폼 알림 시스템 자격 증명 작업을 수행할 수 있습니다. 이 옵션을 사용하면 동일한 네임스페이스 내에서 허브로 테넌트를 쉽게 분할할 수 있습니다.
* **예약된 푸시**: 알림이 언제든지 전송되도록 예약할 수 있습니다.
* **대량 작업**: [등록 내보내기/가져오기] 문서에 설명된 대로 등록 내보내기/가져오기 기능을 사용합니다.

### <a name="what-is-the-notification-hubs-sla"></a>Notification Hubs SLA란?

기본 및 표준 Notification Hubs 계층의 경우에 올바르게 구성된 애플리케이션은 99.9% 이상의 성공률로 푸시 알림을 보내거나 등록 관리 작업을 수행할 수 있습니다. SLA에 대해 자세히 알아보려면 [Notification Hubs SLA](https://azure.microsoft.com/support/legal/sla/notification-hubs/) 페이지로 이동하세요.

> [!NOTE]
> 푸시 알림은 타사 플랫폼 알림 시스템(예: Apple APNS 및 Google FCM)에 의존하므로 이러한 메시지 배달에 대한 SLA 보장이 없습니다. Notification Hubs에서 플랫폼 알림 시스템(SLA 보장)에 대한 전송을 일괄 처리하면 푸시를 제공하는 것(SLA 보장 안 함)은 플랫폼 알림 시스템의 책임입니다.

### <a name="how-do-i-upgrade-or-downgrade-my-hub-or-namespace-to-a-different-tier"></a>내 허브 또는 네임스페이스를 다른 계층으로 업그레이드하거나 다운그레이드하려면 어떻게 해야 합니까?

**[Azure Portal]** > **Notification Hubs 네임스페이스** 또는 **Notification Hubs**로 이동합니다. 업데이트할 리소스를 선택하고 **가격 책정 계층**으로 이동합니다. 다음 요구 사항에 유의하세요.

* 업데이트된 가격 책정 계층은 작업 중인 네임스페이스 내 *모든* 허브에 적용됩니다.
* 디바이스 수가 다운그레이드하려는 계층의 한도를 초과하는 경우에는 디바이스를 삭제해야 다운그레이드할 수 있습니다.

## <a name="design-and-development"></a>디자인 및 개발

### <a name="which-server-side-platforms-do-you-support"></a>어떤 서버 쪽 플랫폼을 지원하나요?

.NET, Java, Node.js, PHP 및 Python용 서버 SDK가 제공됩니다. Notification Hubs API는 REST 인터페이스를 기반으로 하므로 다양한 플랫폼을 사용하거나 추가 종속성을 원하지 않는 경우 REST API로 직접 작업할 수 있습니다. 자세한 내용을 보려면 [Notification Hubs REST API] 페이지로 이동하세요.

### <a name="which-client-platforms-do-you-support"></a>어떤 클라이언트 플랫폼이 지원되나요?

푸시 알림은 [iOS](notification-hubs-ios-apple-push-notification-apns-get-started.md), [Android](notification-hubs-android-push-notification-google-fcm-get-started.md), [Windows 유니버설](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md), [Windows Phone](notification-hubs-windows-mobile-push-notifications-mpns.md), [Kindle](notification-hubs-kindle-amazon-adm-push-notification.md), [Android China(Baidu 경유)](notification-hubs-baidu-china-android-notifications-get-started.md), Xamarin([iOS](xamarin-notification-hubs-ios-push-notification-apns-get-started.md) 및 Android), [Chrome 앱](notification-hubs-chrome-push-notifications-get-started.md) 및 [Safari](https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSafari)에서 지원됩니다. 자세한 내용을 보려면 [Notification Hubs 시작 자습서] 페이지로 이동하세요.

### <a name="do-you-support-text-message-email-or-web-notifications"></a>문자 메시지, 전자 메일, 웹 알림이 지원되나요?

Notification Hubs는 기본적으로 모바일 앱에 알림을 보내도록 디자인되었습니다. 전자 메일 또는 문자 메시지 기능은 제공하지 않습니다. 그러나 이러한 기능을 제공하는 타사 플랫폼을 Notification Hubs와 통합하여 [Mobile Apps]를 사용해 네이티브 푸시 알림을 보낼 수는 있습니다.

또한 Notification Hubs는 브라우저 내 푸시 알림 배달 서비스를 기본적으로 제공하지 않습니다. 고객은 지원되는 서버 쪽 플랫폼의 맨 위에서 SignalR을 사용하여 이를 구현할 수 있습니다. Chrome 샌드박스에서 브라우저 앱에 알림을 보내려면 [Chrome 앱 자습서]를 참조하세요.

### <a name="how-are-mobile-apps-and-azure-notification-hubs-related-and-when-do-i-use-them"></a>Mobile Apps와 Azure Notification Hubs는 어떻게 관련성이 있으며 이 두 항목은 각각 어떤 경우에 사용해야 하나요?

기존 모바일 앱 백 엔드가 있으며 푸시 알림을 보내는 기능만 추가하려는 경우 Azure Notification Hubs를 사용할 수 있습니다. 모바일 앱 백 엔드를 처음부터 설정하려면 Azure App Service의 Mobile Apps를 사용하는 것이 좋습니다. 모바일 앱은 모바일 앱 백 엔드에서 푸시 알림을 쉽게 보낼 수 있도록 알림 허브를 자동으로 프로비전합니다. Mobile Apps의 가격 책정에는 알림 허브의 기본 요금이 포함됩니다. 포함된 푸시를 초과하는 경우에만 요금을 지불하면 됩니다. 비용에 대한 자세한 정보는 [App Service 가격 책정] 페이지로 이동하세요.

### <a name="how-many-devices-can-i-support-if-i-send-push-notifications-via-notification-hubs"></a>Notification Hubs를 통해 푸시 알림을 보낼 경우 얼마나 많은 디바이스를 지원할 수 있나요?

지원되는 디바이스 수에 대한 내용은 [Notification Hubs 가격 책정] 페이지를 참조하세요.

천만 개 이상의 등록된 디바이스에 대한 지원이 필요한 경우 직접 [문의](https://azure.microsoft.com/overview/contact-us/)하여 솔루션을 확장하도록 합니다.

### <a name="how-many-push-notifications-can-i-send-out"></a>보낼 수 있는 푸시 알림 수는 몇 개인가요?

선택한 계층에 따라 Azure Notification Hubs는 시스템에서 전송되는 알림 수에 따라 알림 허브가 자동으로 확장합니다.

> [!NOTE]
> 전체 사용 비용은 제공되는 푸시 알림 수에 따라 증가될 수 있습니다. [Notification Hubs 가격 책정] 페이지에 설명된 계층 한도를 알고 있는지 확인합니다.

고객들은 Notification Hubs를 사용하여 매일 수백만 건의 푸시 알림을 보내고 있습니다. Azure Notification Hubs를 사용하는 한 푸시 알림 도달률의 규모를 조정하기 위해 특별한 작업을 수행할 필요가 없습니다.

### <a name="how-long-does-it-take-for-sent-push-notifications-to-reach-my-device"></a>푸시 알림이 디바이스에 도달하는 데 시간이 얼마나 걸리나요?

수신 부하가 비교적 일정하며 기본적으로 수신량이 급증하지 않는 일반적인 사용 시나리오에서 Azure Notification Hubs는 분당 *1백만 건 이상의 푸시 알림 전송*을 처리할 수 있습니다. 이 속도는 태그 수, 들어오는 보내기의 특성 및 기타 외부 요인에 따라 달라질 수 있습니다.

예상된 배달 시간 동안 서비스는 플랫폼당 대상 수를 계산한 다음 등록된 태그 또는 태그 식을 기준으로 푸시 알림 서비스(PNS)에 메시지를 라우팅할 수 있습니다. 이것은 디바이스에 알림을 보내기 위해 PNS가 담당합니다.

PNS는 알림 배달에 대한 SLA를 보장하지 않습니다. 그러나 대부분의 푸시 알림은 Notification Hubs로 전송된 시점에서 몇 분 이내에(일반적으로 10분 이내) 대상 디바이스로 배달됩니다. 몇 가지 알림에 더 많은 시간이 소요될 수 있습니다.

> [!NOTE]
> Azure Notification Hubs에서는 30분 이내에 PNS로 배달되지 않는 푸시 알림은 삭제하는 내부 정책을 사용합니다. 이러한 지연은 여러 가지 원인으로 인해 나타날 수 있지만, 그 중 가장 일반적인 이유는 PNS에서 애플리케이션을 제한하는 경우입니다.

### <a name="is-there-any-latency-guarantee"></a>대기 시간 보장이 있나요?

푸시 알림은 외부 플랫폼별 PNS를 통해 배달되는 특성이 있으므로 대기 시간이 보장되지 않습니다. 일반적으로 대부분의 푸시 알림은 보통 몇 분 이내에 배달됩니다.

### <a name="what-do-i-need-to-consider-when-designing-a-solution-with-namespaces-and-notification-hubs"></a>네임스페이스와 알림 허브를 사용하는 솔루션을 디자인할 때 고려해야 하는 사항은 무엇인가요?

#### <a name="mobile-appenvironment"></a>모바일 앱/환경

* 환경당 모바일 앱마다 알림 허브를 하나씩 사용합니다.
* 다중 테넌트 시나리오에서는 각 테넌트에 별도의 허브가 있어야 합니다.
* 프로덕션 및 테스트 환경에 대해 동일한 알림 허브를 공유하지 마세요. 이 경우 알림을 보낼 때 문제를 일으킬 수 있습니다. (Apple에서는 각기 별도의 자격 증명을 사용하는 샌드박스 및 프로덕션 푸시 엔드포인트를 제공합니다.)
* 기본적으로 Azure Portal 또는 Visual Studio의 Azure 통합 구성 요소를 통해 등록된 디바이스에 테스트 알림을 보낼 수 있습니다. 임계값은 등록 풀에서 임의로 선택된 10개의 디바이스에 설정됩니다.

> [!NOTE]
> 원래 Apple 샌드박스 인증서를 사용하여 허브를 구성했다가 Apple 프로덕션 인증서를 사용하도록 허브를 다시 구성하면 원래 디바이스 토큰이 무효화됩니다. 잘못된 토큰으로 푸시가 실패합니다. 프로덕션 환경과 테스트 환경을 분리하고 각기 환경에 서로 다른 허브를 사용합니다.

#### <a name="pns-credentials"></a>PNS 자격 증명

Apple, Google 등의 플랫폼 개발자 포털에 모바일 앱을 등록하면 앱 식별자 및 보안 토큰이 전송됩니다. 디바이스에 푸시 알림을 보낼 수 있도록 앱 백 엔드에서 플랫폼의 PNS에 이 토큰을 제공합니다. 보안 토큰은 인증서(예: Apple iOS 또는 Windows Phone) 또는 보안 키(예: Google Android 또는 Windows) 형식일 수 있습니다. 이들은 알림 허브에서 구성해야 합니다. 일반적으로 구성은 알림 허브 수준에서 수행되지만 다중 테넌트 시나리오에서는 네임스페이스 수준에서 수행할 수도 있습니다.

#### <a name="namespaces"></a>네임스페이스

네임스페이스를 배포 그룹화에 사용할 수 있습니다. 또한 다중 테넌트 시나리오에서 같은 앱의 모든 테넌트에 대해 모든 알림 허브를 표시하는 데 사용할 수도 있습니다.

#### <a name="geo-distribution"></a>지역 배포

지역 배포가 푸시 알림 시나리오에서 반드시 중요한 것은 아닙니다. 디바이스에 푸시 알림을 전송하는 다양한 PNS(예: APNS 또는 FCM)가 고르게 배포되지 않습니다.

애플리케이션이 전역적으로 사용되는 경우에는 전 세계 여러 Azure 지역에서 Notification Hubs 서비스를 사용하여 다른 네임스페이스에 허브를 만들 수 있습니다.

> [!NOTE]
> 관리 비용(특히, 등록에)이 늘어날 수 있으므로 이러한 배열은 권장하지 않습니다. 명시적인 수요가 있는 경우에만 수행해야 합니다.

### <a name="should-i-do-registrations-from-the-app-backend-or-directly-through-client-devices"></a>등록은 앱 백 엔드에서 하나요? 아니면 클라이언트 디바이스를 통해 직접 하나요?

등록을 만들기 전에 클라이언트를 인증해야 하는 경우 앱 백 엔드에서 등록하는 것이 유용합니다. 또한 앱 논리 기반 앱 백 엔드에서 생성 또는 수정해야 하는 태그가 있는 경우에도 유용합니다. 자세한 내용을 보려면 [백 엔드 등록 지침] 및 [백 엔드 등록 지침 2] 페이지로 이동하세요.

### <a name="what-is-the-push-notification-delivery-security-model"></a>푸시 알림 배달 보안 모델이란?

Azure Notification Hubs에서는 [공유 액세스 서명](../storage/common/storage-dotnet-shared-access-signature-part-1.md) 기반 보안 모델을 사용합니다. 루트 네임스페이스 수준 또는 상세한 알림 허브 수준에서 공유 액세스 서명 토큰을 사용할 수 있습니다. 메시지 보내기 권한, 알림 수신 대기 권한 등의 여러 권한 부여 규칙에 따라 공유 액세스 서명 토큰을 설정할 수 있습니다. 자세한 내용은 [Notification Hubs 보안 모델] 문서를 참조하세요.

### <a name="how-should-i-handle-sensitive-payload-in-push-notifications"></a>푸시 알림의 중요한 페이로드를 어떻게 처리해야 하나요?

모든 알림은 플랫폼의 PNS를 통해 대상 디바이스로 배달됩니다. 알림을 Azure Notification Hubs로 보내면 처리된 후 각 PNS로 전달됩니다.

보낸 사람으로부터 Azure Notification Hubs를 거쳐 PNS로 가는 모든 연결에서는 HTTPS를 사용합니다.

> [!NOTE]
> Azure Notification Hubs는 어떤 방식으로도 메시지 페이로드를 기록하지 않습니다.

중요한 페이로드를 전송하려면 보안 푸시 패턴을 사용하는 것이 좋습니다. 보낸 사람은 메시지 식별자가 포함된 ping 알림을 중요한 페이로드 없이 디바이스에 배달합니다. 디바이스의 앱이 페이로드를 수신하면 앱에서 보안 API를 직접 호출하여 메시지 세부 정보를 가져옵니다. 이 패턴을 구현하는 방법에 대한 가이드를 보려면 [Notification Hubs 보안 푸시 자습서] 페이지로 이동하세요.

## <a name="operations"></a>작업

### <a name="what-support-is-provided-for-disaster-recovery"></a>재해 복구에는 어떤 지원이 제공되나요?

끝에서 Notification Hubs 이름, 연결 문자열 및 기타 중요한 정보에 대한 메타데이터 재해 복구를 제공합니다. 재해 복구 시나리오가 트리거되면 등록 데이터는 손실될 Notification Hubs 인프라의 *유일한 세그먼트* 입니다. 솔루션을 구현하여 이 데이터를 새 허브 사후 복구에 다시 채워야 합니다.

1. 다른 데이터 센터에서 보조 알림 허브를 만듭니다. 관리 기능에 영향을 줄 수 있는 재해 복구 이벤트로부터 사용자를 보호하기 위해 처음부터 알림 허브를 만드는 것이 좋습니다. 재해 복구 이벤트 시 만들 수도 있습니다.

2. 주 알림 허브의 등록 정보로 보조 알림 허브를 채웁니다. 두 허브의 등록을 모두 유지 관리하면서 등록이 수신되면 두 허브를 동기화 상태로 유지하는 방식은 사용하지 않는 것이 좋습니다. PNS 쪽에서 만료되는 등록의 기본적인 성향 때문에 이러한 방식은 원활히 작동하지 않습니다. 만료되거나 잘못된 등록에 대한 PNS 피드백이 수신되면 Notification Hubs에서는 해당 등록을 정리합니다.  

앱 백 엔드에 대한 두 가지 추천 사항이 있습니다.

* 끝부분에서 주어진 등록 세트를 유지 관리하는 앱 백 엔드를 사용합니다. 그런 다음 보조 알림 허브로 대량 삽입을 수행할 수 있습니다.
* 주 알림 허브에서 일반 등록 덤프를 백업으로 가져오는 앱 백 엔드를 사용합니다. 그런 다음 보조 알림 허브로 대량 삽입을 수행할 수 있습니다.

> [!NOTE]
> 표준 계층에서 제공되는 등록 내보내기/가져오기는 [등록 내보내기/가져오기] 문서에서 설명합니다.

백 엔드가 없고 앱에서 대상 디바이스를 시작하면 보조 알림 허브에서 새 등록을 수행합니다. 결국 보조 알림 허브는 등록된 모든 활성 디바이스를 포함합니다.

앱이 열리지 않은 디바이스가 알림을 수신하지 않을 때 시간 간격이 있게 됩니다.

### <a name="is-there-audit-log-capability"></a>감사 로그 기능이 있나요?

예. 모든 Notification Hubs 관리 작업은 [Azure Portal]에 공개되는 Azure 활동 로그를 업데이트합니다. Azure 활동 로그는 구독의 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 활동 로그를 사용하면 구독의 리소스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에서 누가, 무엇을, 언제 수행했는지 확인할 수 있습니다. 작업과 기타 관련 속성의 상태도 이해할 수 있습니다. 그러나 활동 로그에는 읽기(GET) 작업이 포함되지 않습니다.

## <a name="monitoring-and-troubleshooting"></a>모니터링 및 문제 해결

### <a name="what-troubleshooting-capabilities-are-available"></a>어떤 문제 해결 기능이 제공되나요?

Azure Notification Hubs에서는 문제 해결을 위한 여러 기능을 제공하며, 특히 알림 삭제와 관련한 가장 일반적인 시나리오에서 사용 가능한 기능을 제공합니다. 자세한 내용은 [Notification Hubs 문제 해결] 백서를 참조하세요.

### <a name="what-telemetry-features-are-available"></a>어떤 원격 분석 기능이 제공되나요?

Azure Notification Hubs에서는 [Azure Portal]의 원격 분석 데이터를 확인할 수 있습니다. 사용 가능한 메트릭에 대한 자세한 내용은 [Notification Hubs 메트릭] 페이지에서 제공됩니다.

또한 프로그래밍 방식으로 메트릭에 액세스할 수 있습니다. 자세한 내용은 다음 문서를 참조하세요.

- [.NET을 사용 하 여 Azure Monitor 메트릭 검색](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/)합니다. 이 샘플에서는 사용자 이름 및 암호를 사용 합니다. 인증서를 사용 하려면에 표시 된 대로 인증서를 제공할 FromServicePrincipal 메서드를 오버 로드 [이 예제에서는](https://github.com/Azure/azure-libraries-for-net/blob/master/src/ResourceManagement/ResourceManager/Authentication/AzureCredentialsFactory.cs)합니다. 
- [리소스에 대 한 메트릭 및 활동 로그 가져오기](https://azure.microsoft.com/resources/samples/monitor-dotnet-query-metrics-activitylogs/)
- [Azure Monitoring REST API 연습](../azure-monitor/platform/rest-api-walkthrough.md)


> [!NOTE]
> 성공적인 알림은 푸시 알림이 외부 PNS(예: Apple용 APNS, Google용 FCM)에 전송되었음을 의미합니다. 이것은 대상 디바이스에 알림을 배달하기 위해 PNS가 담당합니다. 일반적으로 PNS는 제 3자에게 배달 메트릭을 노출하지 않습니다.  

[Azure Portal]: https://portal.azure.com
[Notification Hubs 가격 책정]: https://azure.microsoft.com/pricing/details/notification-hubs/
[Notification Hubs SLA]: https://azure.microsoft.com/support/legal/sla/
[Notification Hubs REST API]: https://msdn.microsoft.com/library/azure/dn530746.aspx
[Notification Hubs 시작 자습서]: https://azure.microsoft.com/documentation/articles/notification-hubs-ios-get-started/
[Chrome 앱 자습서]: https://azure.microsoft.com/documentation/articles/notification-hubs-chrome-get-started/
[Mobile Services Pricing]: https://azure.microsoft.com/pricing/details/mobile-services/
[백 엔드 등록 지침]: https://msdn.microsoft.com/library/azure/dn743807.aspx
[백 엔드 등록 지침 2]: https://msdn.microsoft.com/library/azure/dn530747.aspx
[Notification Hubs 보안 모델]: https://msdn.microsoft.com/library/azure/dn495373.aspx
[Notification Hubs 보안 푸시 자습서]: https://azure.microsoft.com/documentation/articles/notification-hubs-aspnet-backend-ios-secure-push/
[Notification Hubs 문제 해결]: https://azure.microsoft.com/documentation/articles/notification-hubs-diagnosing/
[Notification Hubs 메트릭]: ../azure-monitor/platform/metrics-supported.md#microsoftnotificationhubsnamespacesnotificationhubs
[등록 내보내기/가져오기]: https://docs.microsoft.com/en-us/azure/notification-hubs/export-modify-registrations-bulk
[Azure Portal]: https://portal.azure.com
[complete samples]: https://github.com/Azure/azure-notificationhubs-samples
[Mobile Apps]: https://azure.microsoft.com/services/app-service/mobile/
[App Service 가격 책정]: https://azure.microsoft.com/pricing/details/app-service/
