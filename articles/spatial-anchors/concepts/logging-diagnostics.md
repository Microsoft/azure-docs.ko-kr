---
title: 로깅 및 진단 Azure 공간 앵커에 | Microsoft Docs
description: 생성 하 고 로깅 및 진단 Azure 공간 앵커에 검색 하는 방법의 자세한 설명은 합니다.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: ramonarguelles
ms.date: 02/22/2019
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 134023c0884ce3a402b99806f1bf19dcb59ecc32
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564870"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>로깅 및 진단 Azure 공간 앵커에

Azure 공간 앵커 앱 개발에 유용한 표준 로깅 메커니즘을 제공 합니다. 공간 앵커 진단 로깅 모드 디버깅에 대 한 자세한 정보가 필요한 경우에 유용 합니다. 진단 로깅 환경의 이미지를 저장합니다.

## <a name="standard-logging"></a>표준 로깅
공간 앵커 api에서 응용 프로그램 개발 및 디버깅에 대 한 유용한 로그를 가져오는 로깅 메커니즘을 구독할 수 있습니다. 표준 로깅 Api는 환경에 대 한 사진을 장치 디스크에 저장 하지 않습니다. SDK은 이벤트 콜백으로 이러한 로그를 제공합니다. 가 이러한 로그는 응용 프로그램의 로깅 메커니즘에 통합할 수 있습니다.

### <a name="configuration-of-log-messages"></a>로그 메시지의 구성
사용자에 대 한 관심 두 개의 콜백이 있습니다. 다음 샘플에는 세션을 구성 하는 방법을 보여 줍니다.

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

이러한 이벤트 콜백은 로그 및 세션에서 오류를 처리 하는 데 제공 됩니다.

- [LogLevel](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel): 런타임에서 받으려는 이벤트에 대 한 세부 정보 수준을 지정 합니다.
- [OnLogDebug](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug): 표준 디버그 로그 이벤트를 제공합니다.
- [오류](https://docs.microsoft.com/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error): 런타임 오류로 간주 하는 이벤트 로그를 제공 합니다.

## <a name="diagnostics-logging"></a>진단 로깅

작업 로깅에 대 한 표준 모드 외에도 공간 앵커 진단 모드가 있습니다. 진단 모드 환경의 이미지를 캡처하고 디스크에 기록 합니다. 특정 종류의 예측 가능한 방식으로 앵커를 찾기 오류는 같은 문제를 디버깅 하려면이 모드를 사용할 수 있습니다. 진단 특정 문제를 재현에 로깅을 사용 하도록 설정 합니다. 그런 다음 비활성화 합니다. 일반적으로 앱을 실행 하는 경우 진단을 사용 하지 마십시오.

Microsoft 지원 상호 작용 하는 동안 추가로 조사 하기 위해 진단 번들을 제출 하려는 경우 Microsoft 담당자를 요청할 수 있습니다. 이 경우 진단을 사용 하도록 설정 하 고 진단 번들을 전송할 수 있도록 문제를 재현을 결정할 수 있습니다. 

제출 하는 경우 진단 로그를 Microsoft로 이전 승인 없이 Microsoft 담당자에 게, 제출 답변 되지 않은 이동 합니다.

다음 섹션에서는 진단 모드를 사용 하는 방법 및 진단 로그를 Microsoft에 제출 하는 방법을 보여 줍니다.

### <a name="enable-diagnostics-logging"></a>진단 로깅 사용

세션에서 모든 작업 진단 로깅에 대 한 세션을 설정한 경우 로컬 파일 시스템에 로그인 하는 해당 진단 합니다. 로깅, 하는 동안 환경 이미지가 디스크에 저장 됩니다.

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

다음 코드 조각에는 Microsoft에 진단 번들을 제출 하는 방법을 보여 줍니다. 이 번들에는 환경 진단을 사용 하도록 설정한 후 세션에서 캡처된 이미지가 포함 됩니다. 

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
진단 번들에는 다음 정보를 포함할 수 있습니다.

- **키 프레임 이미지**: 진단을 사용 하도록 설정 된 동안 세션에서 캡처한 환경의 이미지입니다.
- **로그**: 런타임에 의해 기록 된 이벤트를 기록 합니다.
- **세션 메타 데이터**: 세션을 식별 하는 메타 데이터입니다.
