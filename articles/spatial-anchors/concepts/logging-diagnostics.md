---
title: 로깅 및 진단
description: Azure 공간 앵커에서 로깅 및 진단을 생성 하 고 검색 하는 방법에 대 한 자세한 설명입니다.
author: ramonarguelles
manager: vriveras
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: da941aa8b616fffeb2e283480c2a1e2f9cf7a093
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89003119"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Azure 공간 앵커의 로깅 및 진단

Azure 공간 앵커는 앱 개발에 유용한 표준 로깅 메커니즘을 제공 합니다. 공간 고정 진단 로깅 모드는 디버깅에 추가 정보가 필요한 경우에 유용 합니다. 진단 로깅은 환경의 이미지를 저장 합니다.

## <a name="standard-logging"></a>표준 로깅
공간 앵커 API에서 로깅 메커니즘을 구독 하 여 응용 프로그램 개발 및 디버깅에 대 한 유용한 로그를 가져올 수 있습니다. 표준 로깅 Api는 장치 디스크에 환경 사진을 저장 하지 않습니다. SDK는 이러한 로그를 이벤트 콜백으로 제공 합니다. 응용 프로그램의 로깅 메커니즘에 이러한 로그를 통합할 수 있습니다.

### <a name="configuration-of-log-messages"></a>로그 메시지 구성
사용자에 게는 두 가지 중요 한 콜백이 있습니다. 다음 샘플에서는 세션을 구성 하는 방법을 보여 줍니다.

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>이벤트 및 속성

이러한 이벤트 콜백은 세션에서 로그 및 오류를 처리 하는 데 제공 됩니다.

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): 런타임에서 받을 이벤트의 세부 수준을 지정 합니다.
- [Onlogdebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): 표준 디버그 로그 이벤트를 제공 합니다.
- [오류](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): 런타임에서 오류로 간주 하는 로그 이벤트를 제공 합니다.

## <a name="diagnostics-logging"></a>진단 로깅

표준 로깅 작업 모드 외에도 공간 앵커에는 진단 모드가 있습니다. 진단 모드는 환경의 이미지를 캡처하고 디스크에 기록 합니다. 이 모드를 사용 하 여 예측을 예측 하지 못한 것과 같은 특정 종류의 문제를 디버깅할 수 있습니다. 진단 로깅을 사용 하 여 특정 문제를 재현 합니다. 그런 다음 사용 하지 않도록 설정 합니다. 앱을 정상적으로 실행 하는 경우 진단을 사용 하도록 설정 하지 마세요.

Microsoft와의 상호 작용을 지원 하는 동안 Microsoft 담당자는 추가 조사를 위해 진단 번들을 전송할지 여부를 요청할 수 있습니다. 이 경우 진단 기능을 사용 하도록 설정 하 고 문제를 재현할 수 있으므로 진단 번들을 제출할 수 있습니다.

Microsoft 담당자의 사전 승인 없이 진단 로그를 Microsoft에 제출 하면 제출에 응답 하지 않습니다.

다음 섹션에서는 진단 모드를 사용 하도록 설정 하는 방법과 진단 로그를 Microsoft에 제출 하는 방법을 보여 줍니다.

### <a name="enable-diagnostics-logging"></a>진단 로깅 사용

진단 로깅에 대 한 세션을 사용 하도록 설정 하면 세션의 모든 작업에 해당 하는 진단 로깅이 로컬 파일 시스템에 있습니다. 로깅 중에 환경의 이미지는 디스크에 저장 됩니다.

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>진단 번들 제출

다음 코드 조각에서는 진단 번들을 Microsoft에 제출 하는 방법을 보여 줍니다. 이 번들에는 진단을 사용 하도록 설정한 후 세션에서 캡처한 환경의 이미지가 포함 됩니다.

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>진단 번들의 일부
진단 번들에는 다음과 같은 정보가 포함 될 수 있습니다.

- **키 프레임 이미지**: 진단을 사용 하도록 설정 하는 동안 세션 중에 캡처된 환경의 이미지입니다.
- **Logs**: 런타임에 의해 기록 된 로그 이벤트입니다.
- **세션 메타 데이터**: 세션을 식별 하는 메타 데이터입니다.
