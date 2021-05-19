---
title: 로깅 및 진단
description: Azure Spatial Anchors에서 로깅 및 진단을 생성하고 검색하는 방법에 대한 자세한 설명입니다.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: da8ffd7ff0b8473ce558943bb420b36f26c3fc32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "95494644"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Azure Spatial Anchors의 로깅 및 진단

Azure Spatial Anchors는 앱 개발에 유용한 표준 로깅 메커니즘을 제공합니다. Spatial Anchors 진단 로깅 모드는 더 많은 디버깅 정보가 필요한 경우에 유용합니다. 진단 로깅은 환경의 이미지를 저장합니다.

## <a name="standard-logging"></a>표준 로깅
Spatial Anchors API에서 로깅 메커니즘을 구독하여 애플리케이션 개발 및 디버깅에 유용한 로그를 가져올 수 있습니다. 표준 로깅 API는 디바이스 디스크에 환경 사진을 저장하지 않습니다. SDK는 이러한 로그를 이벤트 콜백으로 제공합니다. 애플리케이션의 로깅 메커니즘에 이러한 로그를 통합할 수 있습니다.

### <a name="configuration-of-log-messages"></a>로그 메시지 구성
사용자에게는 두 가지 중요한 콜백이 있습니다. 다음 샘플에서는 샘플을 구성하는 방법을 보여 줍니다.

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

세션에서 로그 및 오류를 처리하기 위해 다음 이벤트 콜백이 제공됩니다.

- [LogLevel](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): 런타임에서 받을 이벤트의 세부 수준을 지정합니다.
- [OnLogDebug](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): 표준 디버그 로그 이벤트를 제공합니다.
- [Error](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): 런타임에서 오류로 간주하는 로그 이벤트를 제공합니다.

## <a name="diagnostics-logging"></a>진단 로깅

Spatial Anchors에는 표준 로깅 작업 모드 외에도 진단 모드가 있습니다. 진단 모드는 환경의 이미지를 캡처하고 디스크에 기록합니다. 이 모드를 사용하여 앵커를 예상대로 찾지 못하는 등의 특정 종류의 문제를 디버깅할 수 있습니다. 진단 로깅을 사용하여 특정 문제를 재현합니다. 그런 다음 진단 로깅을 사용하지 않도록 설정합니다. 앱을 정상적으로 실행하는 경우 진단을 사용하지 마세요.

Microsoft와의 상호 작용을 지원하는 동안 Microsoft 담당자가 추가 조사를 위해 진단 번들을 전송할지 여부를 문의할 수 있습니다. 이 경우 진단을 사용하도록 설정하고 진단 번들을 제출할 수 있도록 문제를 재현해야 할 수 있습니다.

Microsoft 담당자의 사전 승인 없이 진단 로그를 Microsoft에 제출하면 제출에 응답하지 않습니다.

다음 섹션에서는 진단 모드를 사용하도록 설정하는 방법과 진단 로그를 Microsoft에 제출하는 방법을 보여 줍니다.

### <a name="enable-diagnostics-logging"></a>진단 로깅 사용

진단 로깅에 대한 세션을 사용하도록 설정하면 세션의 모든 작업은 로컬 파일 시스템에 해당 진단 로깅을 저장합니다. 로깅 중에 환경의 이미지는 디스크에 저장됩니다.

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

다음 코드 조각에서는 진단 번들을 Microsoft에 제출하는 방법을 보여 줍니다. 이 번들에는 진단을 사용하도록 설정한 후 세션에서 캡처한 환경의 이미지가 포함됩니다.

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

### <a name="parts-of-a-diagnostics-bundle"></a>진단 번들 내용
진단 번들에는 다음과 같은 정보가 포함될 수 있습니다.

- **키 프레임 이미지**: 진단을 사용하도록 설정하는 동안 세션 중에 캡처된 환경의 이미지입니다.
- **로그**: 런타임에 의해 기록된 로그 이벤트입니다.
- **세션 메타데이터**: 세션을 식별하는 메타데이터입니다.