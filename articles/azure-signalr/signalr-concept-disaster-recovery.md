---
title: Azure SignalR Service의 복원력 및 재해 복구
description: 여러 SignalR Service 인스턴스를 설정하여 복원력 및 재해 복구를 달성하기 위한 방법에 대한 개요
author: chenkennt
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: kenchen
ms.openlocfilehash: eb70e65db4a086afc60e91cadf55a8844b102591
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402135"
---
# <a name="resiliency-and-disaster-recovery"></a>복원력 및 재해 복구

복원력 및 재해 복구는 온라인 시스템에 공통적으로 필요합니다. Azure SignalR Service는 이미 99.9% 가용성을 보장하지만 여전히 지역 서비스에 불과합니다.
서비스 인스턴스 한 지역에서 항상 실행 되 고 및 없습니다 장애 조치 다른 지역에는 전체 지역 가동 중단 될 경우.

Microsoft의 서비스 SDK는 대신 여러 SignalR Service 인스턴스를 지원하고 그 중 일부를 사용할 수 없는 경우 다른 인스턴스로 자동으로 전환하는 기능을 제공합니다.
이 기능을 사용하면 재해가 발생할 때 복구할 수 있지만 올바른 시스템 토폴로지를 직접 설정해야 합니다. 이 문서에서는 그 방법에 대해 알아봅니다.

## <a name="high-available-architecture-for-signalr-service"></a>SignalR Service에 대한 고가용성 아키텍처

SignalR Service에 대한 지역 간 복원력을 위해서는 다른 지역에서 여러 서비스 인스턴스를 설정해야 합니다. 그러면 하나의 지역이 다운되는 경우 다른 지역을 백업으로 사용할 수 있습니다.
여러 서비스 인스턴스를 앱 서버에 연결할 때 기본 및 보조와 같은 두 가지 역할이 있습니다.
기본은 온라인 트래픽을 담당하는 인스턴스이며, 보조는 완벽하게 작동하지만 기본에 대한 백업 인스턴스입니다.
SDK 구현에서 협상은 기본 엔드포인트만 반환하므로, 일반적인 경우 클라이언트는 기본 엔드포인트에만 연결됩니다.
하지만 기본 인스턴스가 다운되는 경우 협상은 보조 엔드포인트를 반환하므로 클라이언트는 연결을 계속 유지할 수 있습니다.
기본 인스턴스 및 응용 프로그램 서버는 일반 서버 연결을 통해 연결 되어 있지만 특수 한 유형의 약한 연결 이라는 연결을 통해 연결 된 보조 인스턴스 및 응용 프로그램 서버.
약한 연결의 주요 차이점은 해당 하지 그대로 사용 하는 클라이언트 연결 라우팅 보조 인스턴스는 다른 지역에 위치 하기 때문입니다. 다른 지역에 클라이언트를 라우팅 (대기 시간이 증가)는 최적의 선택은 아닙니다.

여러 앱 서버에 연결하는 경우 하나의 서비스 인스턴스가 여러 다른 역할을 가질 수 있습니다.
지역 간 시나리오에 대한 한 가지 일반적인 설정은 SignalR Service 인스턴스와 앱 서버의 쌍을 두 개(또는 이상) 가지는 것입니다.
앱 서버와 SignalR Service의 각 쌍은 동일한 지역에 위치하며 SignalR Service는 앱 서버와 기본 역할로 연결됩니다.
앱 서버와 SignalR Service의 각 쌍은 서로 연결되지만, SignalR은 다른 지역의 서버에 연결할 때 보조가 됩니다.

이 토폴로지를 통해 모든 앱 서버와 SignalR Service 인스턴스가 서로 연결되므로 한 서버의 메시지를 모든 클라이언트에 계속 배달할 수 있습니다.
하지만 클라이언트가 연결되어 있으면 항상 최적의 네트워크 대기 시간을 달성하기 위해 동일한 지역의 앱 서버로 라우팅됩니다.

다음은 이러한 토폴로지를 보여주는 다이어그램입니다.

![토폴로지](media/signalr-concept-disaster-recovery/topology.png)

## <a name="configure-app-servers-with-multiple-signalr-service-instances"></a>여러 SignalR Service 인스턴스를 사용한 앱 서버 구성

각 지역에서 SignalR Service와 앱 서버를 만든 후에는 모든 SignalR Service 인스턴스에 연결되도록 앱 서버를 구성할 수 있습니다.

두 가지 방법이 있습니다.

### <a name="through-config"></a>구성을 통해

구성 항목을 통해 환경 변수/앱 settings/web.cofig 통해 SignalR service 연결 문자열을 설정 하는 방법을 알려진 해야 `Azure:SignalR:ConnectionString`합니다.
여러 엔드포인트가 있는 경우 여러 구성 항목에서 각각 다음 형식으로 설정할 수 있습니다.

```
Azure:SignalR:Connection:<name>:<role>
```

여기서 `<name>`은 엔드포인트의 이름이며 `<role>`은 해당 역할(기본 또는 보조)입니다.
이름은 선택 사항이지만 여러 엔드포인트 간에 라우팅 동작을 추가로 사용자 지정하려는 경우 유용합니다.

### <a name="through-code"></a>코드를 통해

연결 문자열을 다른 곳에 저장하려는 경우 `AddAzureSignalR()`(ASP.NET Core에서) 또는 `MapAzureSignalR()`(ASP.NET에서)을 호출할 때 코드에서 이를 읽어 매개 변수로 사용할 수 있습니다.

다음은 샘플 코드입니다.

ASP.NET Core:

```cs
services.AddSignalR()
        .AddAzureSignalR(options => options.Endpoints = new ServiceEndpoint[]
        {
            new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
            new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
        });
```

ASP.NET:

```cs
app.MapAzureSignalR(GetType().FullName, hub,  options => options.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint("<connection_string1>", EndpointType.Primary, "region1"),
        new ServiceEndpoint("<connection_string2>", EndpointType.Secondary, "region2"),
    };
```

## <a name="failover-sequence-and-best-practice"></a>장애 조치(Failover) 시퀀스와 모범 사례

이제 올바른 시스템 토폴로지 설정이 완료되었습니다. 하나의 SignalR Service 인스턴스가 다운될 때마다 온라인 트래픽은 다른 인스턴스로 라우팅됩니다.
기본 인스턴스가 다운되는 경우 다음과 같은 상황이 발생합니다(그리고 얼마 후 복구됨).

1. 기본 서비스 인스턴스가 다운되면 이 인스턴스의 모든 서버 연결이 삭제됩니다.
2. 이 인스턴스에 연결된 모든 서버는 이를 오프라인으로 표시하고 협상은 이 엔드포인트 반환을 중지하며 보조 엔드포인트를 반환하기 시작합니다.
3. 또한 이 인스턴스의 모든 클라이언트 연결이 닫히고, 클라이언트가 다시 연결됩니다. 이제 앱 서버가 보조 엔드포인트를 반환하므로 클라이언트는 보조 인스턴스에 연결됩니다.
4. 이제 보조 인스턴스가 모든 온라인 트래픽을 사용하게 됩니다. 보조가 모든 앱 서버에 연결되므로 서버에서 클라이언트로 가는 모든 메시지는 계속 배달될 수 있습니다. 하지만 클라이언트에서 서버로 가는 메시지는 동일한 지역에 있는 앱 서버로만 라우팅됩니다.
5. 기본 인스턴스가 복구되어 다시 온라인 상태가 되면 앱 서버에서 이에 대한 연결을 다시 설정하고 온라인으로 표시합니다. 새로운 클라이언트가 기본에 다시 연결되므로 이제 협상은 기본 엔드포인트를 다시 반환합니다. 하지만 기존 클라이언트는 삭제되지 않으며 자체 연결을 차단할 때까지 보조로 계속 라우팅됩니다.

아래 다이어그램은 SignalR Service에서 장애 조치가 수행되는 방법을 보여줍니다.

그림 1. 장애 조치(failover) 전![장애 조치(failover) 전](media/signalr-concept-disaster-recovery/before-failover.png)

그림 2. 장애 조치(failover) 후![장애 조치(failover) 후](media/signalr-concept-disaster-recovery/after-failover.png)

그림 3. 기본 복구 후 잠시 동안 ![기본 복구 후 잠시 동안](media/signalr-concept-disaster-recovery/after-recover.png)

정상 사례에서 볼 수 있듯이 기본 앱 서버 및 SignalR Service만 온라인 트래픽을 가집니다(파란색).
장애 조치(failover) 후 보조 앱 서버 및 SignalR Service도 활성화됩니다.
기본 SignalR Service가 다시 온라인 상태가 된 후 새 클라이언트가 기본 SignalR 에 연결됩니다. 하지만 기존 클라이언트는 두 인스턴스 모두에 트래픽이 있으므로 계속 보조에 연결됩니다.
모든 기존 클라이언트 연결이 끊긴 후, 시스템은 정상으로 돌아갑니다(그림 1).

지역 간 고가용성 아키텍처를 구현하기 위한 두 가지 주요 패턴이 있습니다.

1. 첫 번째는 모든 온라인 트래픽을 사용하는 앱 서버와 SignalR Service의 쌍을 갖고, 백업으로 다른 쌍을 확보하는 것입니다(활성/수동이라고 함, 그림 1에서 설명됨). 
2. 다른 하나는 앱 서버와 SignalR Service 인스턴스의 쌍이 두 개(또는 이상)인 것입니다. 각각 온라인 트래픽의 일부를 사용하며 다른 쌍에 대한 백업 역할을 합니다(활성/활성이라고 함, 그림 3과 유사).

SignalR Service는 두 패턴을 모두 지원하며 주요 차이점은 앱 서버를 구현하는 방법입니다.
앱 서버가 활성/수동인 경우 SignalR Service는 활성/수동이 될 수 있습니다(기본 앱 서버는 해당 기본 SignalR Service 인스턴스만 반환하므로).
앱 서버가 활성/활성인 경우 SignalR Service는 활성/활성이 될 수 있습니다(모든 앱 서버는 자체 기본 SignalR 인스턴스를 반환하므로 모두 트래픽을 받을 수 있음).

사용 하도록 선택 하면 어떤 패턴에 관계 없이 각 SignalR 서비스 인스턴스를 주 응용 프로그램 서버에 연결 해야 확인 합니다.

또한 SignalR 연결의 특성상(긴 연결임) 재해 및 장애 조치(failover)가 발생하는 경우 클라이언트에서 연결이 끊깁니다.
최종 고객에게 명료하도록 그러한 경우를 클라이언트 쪽에서 처리해야 합니다. 예를 들어 연결을 닫은 후 다시 연결을 수행합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 SignalR Service에 대한 복원력을 달성하기 위해 애플리케이션을 구성하는 방법을 알아보았습니다. SignalR Service의 서버/클라이언트 연결 및 연결 라우팅에 대해 자세히 알아보려면 [이 문서](signalr-concept-internals.md)에서 SignalR Service 내부 기능에 대해 읽어보세요.

많은 수의 연결을 처리 하려면 여러 인스턴스를 함께 사용 하는 예: 분할 시나리오를 크기 조정에 대 한 읽을 [여러 인스턴스를 확장 하는 방법을](signalr-howto-scale-multi-instances.md)?