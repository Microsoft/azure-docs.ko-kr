---
title: Azure SignalR Service 질문과 대답
description: 문제 해결 및 일반적인 사용 시나리오에 대해 Azure SignalR Service에서 자주 묻는 질문과 대답을 빠르게 액세스할 수 있습니다.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 9aa510a055cb76b30508cb98a25cd9c919eb117d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928312"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service가 프로덕션에 사용할 준비가 되었나요?

예.
일반 공급에 대한 공지는 [Azure SignalR Service가 이제 일반 공급됩니다](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/)를 참조하세요. 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction)이 완전히 지원됩니다.

ASP.NET SignalR에 대한 지원은 아직 *공개 미리 보기* 상태입니다. 여기에 [코드 예제](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)가 있습니다.

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>"서버를 사용할 수 없음" 오류 메시지가 표시되면서 클라이언트 연결이 닫힙니다. 무엇을 의미하나요?

이 오류는 클라이언트가 SignalR Service에 메시지를 보내는 경우에만 발생합니다.

다른 애플리케이션 서버가 없고 SignalR Service REST API만 사용하는 경우, 이 동작은 **의도적인** 동작입니다.
서버리스 아키텍처에서 클라이언트 연결은 수신 대기(**LISTEN**) 모드이며 SignalR Service에 어떠한 메시지도 보내지 않습니다.
자세한 내용은 [REST API](./signalr-quickstart-rest-api.md)를 참조하세요.

애플리케이션 서버가 있는 경우 이 오류 메시지가 표시되면 SignalR Service 인스턴스에 연결되어 있는 애플리케이션 서버가 없다는 의미입니다.

가능한 원인:
- SignalR Service에 연결되어 있는 애플리케이션 서버가 없습니다. 애플리케이션 서버 로그에서 가능한 연결 오류를 확인하세요. 둘 이상의 애플리케이션 서버가 있는 고가용성 설정에서는 이러한 경우가 드뭅니다.
- SignalR Service 인스턴스와 연결 문제가 있습니다. 이 문제는 일시적이며 자동으로 복구됩니다.
문제가 한 시간 넘게 지속되면 [GitHub에서 문제를 제기](https://github.com/Azure/azure-signalr/issues/new)하거나 [Azure에서 지원 요청을 생성](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)하십시오.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>애플리케이션 서버가 여러 개 있으면 클라이언트 메시지가 모든 서버에 전송되나요? 아니면 그 중 하나에만 전송되나요?

이것은 클라이언트와 애플리케이션 서버 간의 일대일 매핑입니다. 한 클라이언트에서 보낸 메시지는 항상 동일한 애플리케이션 서버로 전송됩니다.

클라이언트와 애플리케이션 서버 간의 매핑은 클라이언트나 애플리케이션 서버 연결이 끊어질 때까지 유지됩니다.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>애플리케이션 서버 중 하나가 다운되는 경우, 어떤 서버인지 어떻게 알아내고 알림을 받을 수 있나요?

SignalR Service는 애플리케이션 서버의 하트비트를 모니터링합니다.
지정된 기간 동안 하트비트를 수신하지 못하면 해당 애플리케이션 서버는 오프라인으로 간주됩니다. 이 애플리케이션 서버에 매핑된 모든 클라이언트 연결이 끊어집니다.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>ASP.NET Core SignalR SDK에서 Azure SignalR Service SDK로 전환하는 경우 사용자 지정 `IUserIdProvider`에서 예외가 발생하는 이유가 무엇인가요?

`IUserIdProvider`가 호출되는 경우 ASP.NET Core SignalR SDK와 Azure SignalR Service SDK의 매개 변수 `HubConnectionContext context`가 다릅니다.

ASP.NET Core SignalR에서 `HubConnectionContext context`는 모든 속성에 대해 유효한 값이 있는 실제 클라이언트 연결의 컨텍스트입니다.

Azure SignalR Service SDK에서 `HubConnectionContext context`는 논리적 클라이언트 연결의 컨텍스트입니다. 실제 클라이언트 연결은 SignalR Service 인스턴스에 연결되기 때문에 제한된 수의 속성만 제공됩니다.

지금은 `HubConnectionContext.GetHttpContext()`와 `HubConnectionContext.User`만 액세스에 사용할 수 있습니다.
소스 코드는 [여기](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)에서 확인할 수 있습니다.

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>SignalR Service에서 사용할 수 있는 전송을 ASP.NET Core SignalR로 서버 쪽에서 구성하도록 구성할 수 있나요? 예를 들어 WebSocket 전송을 사용하지 않도록 설정할 수 있나요?

아니요.

Azure SignalR Service는 기본적으로 ASP.NET Core SignalR에서 지원하는 세 가지 전송을 모두 제공합니다. 이 사항은 구성할 수 없습니다. SignalR Service는 연결 및 모든 클라이언트에 대한 전송을 처리합니다.

클라이언트 쪽 전송은 [여기](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2)의 설명대로 구성하면 됩니다.

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Azure Portal에 표시된 메시지 수 또는 연결 수와 같은 메트릭의 의미는 무엇인가요? 어떤 종류의 집계 유형을 선택해야 하나요?

[여기](signalr-concept-messages-and-connections.md)에서 이러한 메트릭을 계산하는 방법에 대한 세부 정보를 찾을 수 있습니다.

Azure SignalR Service 리소스의 개요 블레이드에서 적절한 집계 유형을 이미 선택했습니다. 메트릭 블레이드로 이동하는 경우 [여기](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr)에서 집계 유형을 참조로 사용할 수 있습니다.

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>서비스 모드 `Default`/`Serverless`/`Classic`의 의미는 무엇인가요? 어떻게 선택할 수 있나요?

모드:
* `Default` 모드에는 허브 서버가 *필요합니다*. 이 모드에서 Azure SignalR은 클라이언트 트래픽을 연결된 허브 서버 연결로 라우팅합니다. Azure SignalR은 연결된 허브 서버를 확인합니다. 연결된 허브 서버가 없는 경우 Azure SignalR은 들어오는 클라이언트 연결을 거부합니다. 이 모드에서 **Management API**를 사용하여 Azure SignalR을 통해 연결된 클라이언트를 직접 관리할 수도 있습니다.
* `Serverless` 모드는 서버 연결을 허용하지 *않습니다*. 즉, 모든 서버 연결을 거부합니다. 모든 클라이언트는 서버리스 모드여야 합니다. 클라이언트는 Azure SignalR에 연결되며, 사용자는 일반적으로 **Azure 함수**와 같은 서버리스 기술을 사용하여 허브 논리를 처리합니다. Azure SignalR의 서버리스 모드를 사용하는 [간단한 예제](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu)를 참조하세요.
* `Classic` 모드는 혼합 상태입니다. 허브에서 서버 연결을 사용하는 경우 새 클라이언트는 허브 서버로 라우팅됩니다. 그렇지 않으면 클라이언트가 서버리스 모드로 전환됩니다.

  이로 인해 일부 문제가 발생할 수 있습니다. 예를 들어 모든 서버 연결은 잠시 동안 손실되며, 일부 클라이언트는 허브 서버에 대한 경로가 아닌 서버리스 모드로 전환됩니다.

선택:
1. 허브 서버가 없는 경우 `Serverless`를 선택합니다.
1. 모든 허브에는 허브 서버가 있으며 `Default`를 선택합니다.
1. 일부 허브에는 허브 서버가 있지만, 다른 허브에는 없습니다. `Classic`을 선택하지만 이로 인해 몇 가지 문제가 발생할 수 있습니다. 더 좋은 방법은 두 개의 인스턴스를 만드는 것입니다. 하나는 `Serverless`이며, 다른 하나는 `Default`입니다.

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Azure SignalR for ASP.NET SignalR을 사용하는 경우 기능 차이점이 있나요?
Azure SignalR을 사용하는 경우 ASP.NET SignalR의 일부 API 및 기능이 더 이상 지원되지 않습니다.
- Azure SignalR을 사용하는 경우 클라이언트와 허브 간에 임의 상태를 전달하는 기능(종종 `HubState`라고 함)은 지원되지 않음
- Azure SignalR을 사용하는 경우 `PersistentConnection` 클래스는 아직 지원되지 않음
- Azure SignalR을 사용하는 경우 **Forever Frame 전송**은 지원되지 않음
- 클라이언트가 오프라인인 경우 Azure SignalR이 클라이언트로 전송된 메시지를 더 이상 재생하지 않음
- Azure SignalR을 사용하는 경우 한 클라이언트 연결에 대한 트래픽은 항상 연결 기간 동안 하나의 앱 서버 인스턴스로 라우팅됨(**고정**)

ASP.NET SignalR에 대한 지원은 호환성에 중점을 두므로 ASP.NET Core SignalR의 모든 새로운 기능이 지원되지 않습니다. 예를 들어 **MessagePack**, **Streaming** 등은 ASP.NET Core SignalR 애플리케이션에만 사용할 수 있습니다.

SignalR Service는 다른 서비스 모드로 구성할 수 있습니다. `Classic`/`Default`/`Serverles` 이 ASP.NET 지원에서는 `Serverless` 모드가 지원되지 않습니다. 데이터 평면 REST API도 지원되지 않습니다.

## <a name="where-do-my-data-reside"></a>데이터가 어디에 상주하나요?

Azure SignalR Service가 데이터 프로세서 서비스로 작동 중입니다. 고객 콘텐츠를 저장하지 않으며 데이터 상주 디자인에 의해 영향을 받습니다. 진단에 대해 Azure Storage와 같은 다른 Azure 서비스와 Azure SignalR Service를 함께 사용하는 경우 Azure 지역에서 데이터 상주를 유지하는 방법에 대한 지침은 [여기](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)를 확인하세요.
