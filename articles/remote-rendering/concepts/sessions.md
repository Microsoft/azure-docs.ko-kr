---
title: Remote Rendering 세션
description: Remote Rendering 세션에 대해 설명합니다.
author: jakrams
ms.author: jakras
ms.date: 02/21/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 74fae6a8aa0c59043db0ab816e09b16affb63580
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021836"
---
# <a name="remote-rendering-sessions"></a>Remote Rendering 세션

Azure Remote Rendering(ARR)에서는 *세션*이 핵심 개념입니다. 이 문서에서는 세션이 정확히 무엇인지 설명합니다.

## <a name="overview"></a>개요

Azure Remote Rendering은 복잡한 렌더링 작업을 클라우드로 오프로딩하는 방식으로 작동합니다. 대부분의 클라우드 서버에 GPU가 없기 때문에 아무 서버나 이러한 렌더링 작업을 수행할 수 없습니다. 더 일반적인 웹 트래픽과 달리, 관련 데이터 양과 대화식 프레임 속도로 결과 생성에 대한 까다로운 요구 사항으로 인해 어느 서버가 어느 사용자 요청을 처리하는지에 대한 책임을 다른 머신으로 즉시 넘길 수 없습니다.

즉, Azure Remote Rendering을 사용하는 경우 렌더링 요청을 처리하기 위해 필요한 하드웨어 기능이 있는 클라우드 서버를 독점 예약해야 합니다. *세션*은 이 서버와의 상호 작용과 관련된 모든 것을 가리킵니다. 세션은 사용할 머신을 예약(*임대*)하는 초기 요청으로 시작하고, 모델을 로드하고 조작하는 모든 명령을 계속 진행하고, 클라우드 서버에서 임대를 해제하는 것으로 끝납니다.

## <a name="managing-sessions"></a>세션 관리

세션을 관리하 고 상호 작용하는 방법에는 여러 가지가 있습니다. 세션을 만들고, 업데이트하고, 종료하는 언어 독립 방법은 [세션 관리 REST API](../how-tos/session-rest-api.md)를 사용하는 것입니다. C# 및 C++에서 이러한 작업은 클래스 `AzureFrontend` 및 `AzureSession`을 통해 노출됩니다. Unity 애플리케이션의 경우 `ARRServiceUnity` 구성 요소에서 제공하는 추가 유틸리티 기능이 있습니다.

활성 세션에 *연결*되면 [모델 로드](models.md) 및 장면과 상호 작용 등의 작업이 `AzureSession` 클래스를 통해 노출됩니다.

### <a name="managing-multiple-sessions-simultaneously"></a>동시에 여러 세션 관리

한 디바이스에서 여러 세션에 완전히 *연결*하는 것은 불가능합니다. 그러나 단일 애플리케이션에서 원하는 만큼 세션을 만들고, 관찰하고, 종료할 수 있습니다. 앱이 세션에 연결되지 않는 한 HoloLens 2와 같은 디바이스에서 실행할 필요가 없습니다. 이러한 구현의 사용 사례로 중앙 메커니즘을 통해 세션을 제어하려는 경우가 있습니다. 예를 들어 여러 태블릿과 HoloLens가 로그인할 수 있는 웹 앱을 빌드할 수 있습니다. 그러면 앱은 표시할 CAD 모델과 같은 옵션을 태블릿에 표시할 수 있습니다. 사용자가 선택하면 이 정보는 공유 환경을 만들기 위해 모든 HoloLens에 전달됩니다.

## <a name="session-phases"></a>세션 단계

모든 세션은 여러 단계를 거칩니다.

### <a name="session-startup"></a>세션 시작

ARR에 [새 세션을 만들도록](../how-tos/session-rest-api.md#create-a-session) 요청하면 먼저 세션 [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)가 반환됩니다. 이 UUID를 사용하여 세션에 대한 정보를 쿼리할 수 있습니다. UUID와 세션에 대한 일부 기본 정보는 30일 동안 유지되므로 세션이 중지된 후에도 해당 정보를 쿼리할 수 있습니다. 이때 **세션 상태**는 **시작 중**으로 보고됩니다.

다음으로, Azure Remote Rendering이 세션을 호스트할 수 있는 서버를 찾으려고 합니다. 이 검색에는 2개의 매개 변수가 사용됩니다. 먼저 [지역](../reference/regions.md)의 서버만 예약됩니다. 지역 간의 네트워크 대기 시간이 너무 길어서 적절한 환경을 보장할 수 없기 때문입니다. 두 번째 요소는 사용자가 지정한 원하는 *크기*입니다. 각 지역에는 [*표준*](../reference/vm-sizes.md) 또는 [*프리미엄*](../reference/vm-sizes.md) 크기 요청을 수행할 수 있는 제한 된 수의 서버가 있습니다. 따라서 요청된 크기의 서버가 지역에서 모두 현재 사용 중인 경우 세션을 만들지 못합니다. 실패 이유를 [쿼리](../how-tos/session-rest-api.md#get-sessions-properties)할 수 있습니다.

> [!IMPORTANT]
> *표준* 서버 크기를 요청 하는 경우 요청에 따라 요청이 실패 하 고, *프리미엄* 서버를 요청 하는 것도 실패 합니다. 따라서 사용자가 선택할 수 있는 경우 *프리미엄* 서버 크기로 다시 대체 해 볼 수 있습니다.

서비스에서 적합한 서버를 찾으면 적절한 VM(가상 머신)을 복사하여 Azure Remote Rendering 호스트로 전환해야 합니다. 이 프로세스는 몇 분 정도 걸립니다. 이후에 VM이 부팅되고 **세션 상태**가 **준비**로 전환됩니다.

이때 서버는 입력을 단독으로 대기합니다. 또한 이 시점부터 서비스 요금이 청구됩니다.

### <a name="connecting-to-a-session"></a>세션에 연결

세션이 *준비*되면 해당 세션에 *연결*할 수 있습니다. 연결되어 있는 동안 디바이스는 모델을 로드하고 수정하는 명령을 보낼 수 있습니다. 모든 ARR 호스트는 한 번에 하나의 클라이언트 디바이스만 서비스하므로 클라이언트가 세션에 연결할 때 렌더링된 콘텐츠를 독점적으로 제어할 수 있습니다. 즉, 렌더링 성능은 사용자가 제어할 수 없는 이유로 변경되지 않습니다.

> [!IMPORTANT]
> 한 클라이언트만 세션에 *연결*할 수 있지만 현재 상태와 같은 세션에 대한 기본 정보는 연결하지 않고 쿼리할 수 있습니다.

디바이스가 세션에 연결되어 있는 동안 다른 디바이스의 연결 시도는 실패합니다. 그러나 연결된 디바이스가 자발적으로 또는 어떤 종류의 오류로 인해 연결을 끊은 후에는 세션에서 다른 연결 요청을 수락합니다. 이전 상태(로드된 모델 등)는 다음 연결 디바이스가 깨끗 한 슬레이트를 갖도록 모두 버려집니다. 따라서 여러 디바이스에서 세션을 여러 번 재사용할 수 있으며 대부분의 경우 최종 사용자에게서 세션 시작 오버헤드를 숨길 수 있습니다.

> [!IMPORTANT]
> 원격 서버는 클라이언트 쪽 데이터의 상태를 변경하지 않습니다. 클라이언트 애플리케이션이 변환 업데이트와 로드 요청 등의 모든 데이터 변형을 수행해야 합니다. 모든 작업은 클라이언트 상태를 즉시 업데이트합니다.

### <a name="session-end"></a>세션 종료

새 세션을 요청할 때 대개 1 ~ 8 시간 범위의 *최대 임대 시간*을 지정합니다. 이 기간 동안 호스트가 사용자의 입력을 수락합니다.

대개 다음과 같은 두 가지 이유로 세션이 종료합니다. 사용자가 세션을 중지하도록 수동으로 요청하거나 최대 임대 시간이 만료됩니다. 두 경우 모두 호스트에 대한 활성 연결이 즉시 닫히고 해당 서버에서 서비스가 종료됩니다. 그런 다음, 서버를 Azure 풀로 반환하고 다른 용도로 요청할 수 있습니다. 세션 중지는 취소할 수 없습니다. 중지된 세션에서 **세션 상태**를 쿼리하면 수동으로 종료되었는지 아니면 최대 임대 시간에 도달했는지에 따라 **중지됨** 또는 **만료됨**이 반환됩니다.

일부 오류로 인해 세션이 중지될 수도 있습니다.

모든 경우에 세션이 중지되면 더 이상 요금이 청구되지 않습니다.

> [!WARNING]
> 세션 연결 여부와 기간은 청구에 영향을 주지 않습니다. 서비스에 대 한 비용을 지불 하는 것은 *세션 기간*에 따라 달라 집니다. 즉, 서버가 사용자를 위해 단독으로 예약 된 시간과 요청 된 하드웨어 기능 ( [할당 된 크기](../reference/vm-sizes.md))이 사용 됩니다. 세션을 시작하는 경우 5분 동안 연결한 후 임대가 만료될 때까지 계속 실행되도록 세션을 중지하지 않으면 전체 세션 임대 시간에 대한 요금이 청구됩니다. 반대로 *최대 임대 시간*는 대부분 보안 네트워크입니다. 임대 시간이 8시간인 세션을 요청하는지 여부는 중요하지 않습니다. 나중에 세션을 수동으로 중지하는 경우 5분 동안만 사용합니다.

#### <a name="extend-a-sessions-lease-time"></a>세션의 임대 시간 연장

시간이 더 필요한 경우 활성 세션의 [임대 시간을 연장](../how-tos/session-rest-api.md#modify-and-query-session-properties)할 수 있습니다.

## <a name="example-code"></a>예제 코드

아래 코드는 세션을 시작하고, *준비* 상태가 되기를 기다리고, 연결한 다음, 연결을 끊고, 다시 종료하는 간단한 구현을 보여줍니다.

```cs
RemoteRenderingInitialization init = new RemoteRenderingInitialization();
// fill out RemoteRenderingInitialization parameters...

RemoteManagerStatic.StartupRemoteRendering(init);

AzureFrontendAccountInfo accountInfo = new AzureFrontendAccountInfo();
// fill out accountInfo details...

AzureFrontend frontend = new AzureFrontend(accountInfo);

RenderingSessionCreationParams sessionCreationParams = new RenderingSessionCreationParams();
// fill out sessionCreationParams...

AzureSession session = await frontend.CreateNewRenderingSessionAsync(sessionCreationParams).AsTask();

RenderingSessionProperties sessionProperties;
while (true)
{
    sessionProperties = await session.GetPropertiesAsync().AsTask();
    if (sessionProperties.Status != RenderingSessionStatus.Starting &&
        sessionProperties.Status != RenderingSessionStatus.Unknown)
    {
        break;
    }
    // REST calls must not be issued too frequently, otherwise the server returns failure code 429 ("too many requests"). So we insert the recommended delay of 10s
    await Task.Delay(TimeSpan.FromSeconds(10));
}

if (sessionProperties.Status != RenderingSessionStatus.Ready)
{
    // Do some error handling and either terminate or retry.
}

// Connect to server
Result connectResult = await session.ConnectToRuntime(new ConnectToRuntimeParams()).AsTask();

// Connected!

while(...)
{
    // per frame update

    session.Actions.Update();
}

// Disconnect
session.DisconnectFromRuntime();

// stop the session
await session.StopAsync().AsTask();

// shut down the remote rendering SDK
RemoteManagerStatic.ShutdownRemoteRendering();
```

여러 `AzureFrontend` 및 `AzureSession` 인스턴스를 코드에서 유지 관리, 조작 및 쿼리할 수 있습니다. 그러나 한 번에 하나의 디바이스만 `AzureSession`에 연결할 수 있습니다.

가상 머신의 수명은 `AzureFrontend` 인스턴스나 `AzureSession` 인스턴스에 연결되지 않습니다. 세션을 중지하려면 `AzureSession.StopAsync`를 호출해야 합니다.

`AzureSession.SessionUUID()`를 통해 지속적 세션 ID를 쿼리하고 로컬에 캐시할 수 있습니다. 이 ID로 애플리케이션은 해당 세션에 바인딩할 `AzureFrontend.OpenSession`을 호출할 수 있습니다.

`AzureSession.IsConnected`가 true이면 `AzureSession.Actions`는 [모델을 로드](models.md)하고, [엔터티](entities.md)를 조작하고, 렌더링된 장면에 대한 [정보를 쿼리](../overview/features/spatial-queries.md)하는 기능이 포함된 `RemoteManager`의 인스턴스를 반환합니다.

## <a name="next-steps"></a>다음 단계

* [엔터티](entities.md)
* [모델](models.md)
