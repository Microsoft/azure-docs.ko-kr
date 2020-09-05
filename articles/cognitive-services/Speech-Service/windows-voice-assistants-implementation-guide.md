---
title: Windows의 음성 도우미-위의 잠금 구현 지침
titleSuffix: Azure Cognitive Services
description: 음성 에이전트 응용 프로그램에 대 한 음성 활성화 및 잠금 기능을 구현 하기 위한 지침입니다.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.custom: devx-track-csharp
ms.openlocfilehash: a529875536c2feafe05695e5d20daed0873a95e6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88934449"
---
# <a name="implementing-voice-assistants-on-windows"></a>Windows에서 음성 도우미 구현

이 가이드에서는 Windows에서 음성 도우미를 만들기 위한 중요 한 구현 세부 정보를 안내 합니다.

## <a name="implementing-voice-activation"></a>음성 활성화 구현

[환경을 설정](how-to-windows-voice-assistants-get-started.md) 하 고 [음성 정품 인증의 작동 방식을](windows-voice-assistants-overview.md#how-does-voice-activation-work)파악 한 후에는 고유한 음성 도우미 응용 프로그램에 대 한 음성 활성화 구현을 시작할 수 있습니다.

### <a name="registration"></a>등록

#### <a name="ensure-that-the-microphone-is-available-and-accessible-then-monitor-its-state"></a>마이크가 사용 가능 하 고 액세스 가능한 지 확인 한 다음 해당 상태를 모니터링 합니다.

MVA는 마이크가 있어야 하 고 음성 정품 인증을 검색할 수 있는 액세스 가능 해야 합니다. [Appcapability](https://docs.microsoft.com/uwp/api/windows.security.authorization.appcapabilityaccess.appcapability?view=winrt-18362), [Devicewatcher](https://docs.microsoft.com/uwp/api/windows.devices.enumeration.devicewatcher?view=winrt-18362)및 [MediaCapture](https://docs.microsoft.com/uwp/api/windows.media.capture.mediacapture?view=winrt-18362) 클래스를 사용 하 여 마이크 개인 정보 액세스, 장치 상태 및 장치 상태 (예: 볼륨 및 음소거)를 각각 확인 합니다.

### <a name="register-the-application-with-the-background-service"></a>백그라운드 서비스에 응용 프로그램 등록

MVA가 백그라운드에서 응용 프로그램을 시작 하도록 하려면 백그라운드 서비스에 응용 프로그램을 등록 해야 합니다. [여기](https://docs.microsoft.com/windows/uwp/launch-resume/register-a-background-task)에서 백그라운드 서비스 등록에 대 한 전체 가이드를 참조 하세요.

### <a name="unlock-the-limited-access-feature"></a>제한 된 액세스 기능 잠금 해제

Microsoft에서 제공 하는 제한 된 액세스 기능 키를 사용 하 여 음성 도우미 기능의 잠금을 해제 합니다. Windows SDK에서 [LimitedAccessFeature](https://docs.microsoft.com/uwp/api/windows.applicationmodel.limitedaccessfeatures?view=winrt-18362) 클래스를 사용 하 여이 작업을 수행 합니다.

### <a name="register-the-keyword-for-the-application"></a>응용 프로그램에 대 한 키워드 등록

응용 프로그램은 자체, 해당 키워드 모델 및 Windows에 해당 언어를 등록 해야 합니다.

먼저 키워드 감지기를 검색 합니다. 이 샘플 코드에서는 첫 번째 탐지기를 검색 하지만, 특정 탐지기를 선택 하 여 선택할 수 있습니다 `configurableDetectors` .

```csharp
private static async Task<ActivationSignalDetector> GetFirstEligibleDetectorAsync()
{
    var detectorManager = ConversationalAgentDetectorManager.Default;
    var allDetectors = await detectorManager.GetAllActivationSignalDetectorsAsync();
    var configurableDetectors = allDetectors.Where(candidate => candidate.CanCreateConfigurations
        && candidate.Kind == ActivationSignalDetectorKind.AudioPattern
        && (candidate.SupportedModelDataTypes.Contains("MICROSOFT_KWSGRAPH_V1")));

    if (configurableDetectors.Count() != 1)
    {
        throw new NotSupportedException($"System expects one eligible configurable keyword spotter; actual is {configurableDetectors.Count()}.");
    }

    var detector = configurableDetectors.First();

    return detector;
}
```

ActivationSignalDetector 개체를 검색 한 후에는 `ActivationSignalDetector.CreateConfigurationAsync` 신호 id, 모델 id 및 표시 이름을 사용 하 여 해당 메서드를 호출 하 여 키워드를 등록 하 고 응용 프로그램의를 검색 `ActivationSignalDetectionConfiguration` 합니다. 신호 및 모델 Id는 개발자가 결정 한 guid 여야 하며 동일한 키워드에 대해 일관 된 상태를 유지 해야 합니다.

### <a name="verify-that-the-voice-activation-setting-is-enabled"></a>음성 활성화 설정이 사용 되는지 확인 합니다.

음성 정품 인증을 사용 하려면 사용자는 자신의 시스템에 대해 음성 정품 인증을 사용 하도록 설정 하 고 해당 응용 프로그램에 대해 음성 활성화를 사용 해야 합니다. Windows 설정의 "음성 활성화 개인 정보 설정"에서 설정을 찾을 수 있습니다. 응용 프로그램에서 음성 활성화 설정의 상태를 확인 하려면의 인스턴스를 사용 하 여 `ActivationSignalDetectionConfiguration` 키워드를 등록 합니다. 의 [AvailabilityInfo](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/blob/master/clients/csharp-uwp/UWPVoiceAssistantSample/UIAudioStatus.cs#L128) 필드에는 `ActivationSignalDetectionConfiguration` 음성 활성화 설정의 상태를 설명 하는 열거형 값이 포함 되어 있습니다.

### <a name="retrieve-a-conversationalagentsession-to-register-the-app-with-the-mva-system"></a>ConversationalAgentSession를 검색 하 여 MVA 시스템에 앱을 등록 합니다.

는 `ConversationalAgentSession` 앱이 앱 상태 (유휴 상태, 검색, 수신 대기, 작업 중, 말하기) 및 수신 이벤트 (예: 화면 잠금 등의 시스템 상태 변경)를 사용 하 여 Windows를 업데이트할 수 있도록 하는 Windows SDK의 클래스입니다. 또한 AgentSession 인스턴스를 검색 하면 응용 프로그램을 음성으로 활성화할 수 있는 Windows에 등록 하는 역할도 수행 합니다. 에 대 한 참조를 유지 관리 하는 것이 가장 좋습니다 `ConversationalAgentSession` . 세션을 검색 하려면 API를 사용 `ConversationalAgentSession.GetCurrentSessionAsync` 합니다.

### <a name="listen-to-the-two-activation-signals-the-onbackgroundactivated-and-onsignaldetected"></a>OnBackgroundActivated 및 OnSignalDetected의 두 가지 활성화 신호를 수신 대기 합니다.

Windows는 두 가지 방법 중 하나로 키워드를 검색할 때 앱에 신호를 알립니다. 앱이 활성화 되지 않은 경우 (즉, 삭제 되지 않은 인스턴스에 대 한 참조가 없는 경우 `ConversationalAgentSession` ) 앱이 시작 되 고 응용 프로그램의 App.xaml.cs 파일에서 OnBackgroundActivated 메서드를 호출 합니다. 이벤트 인수의 `BackgroundActivatedEventArgs.TaskInstance.Task.Name` 필드가 "AgentBackgroundTrigger" 문자열과 일치 하는 경우 음성 활성화에 의해 응용 프로그램 시작이 트리거됩니다. 응용 프로그램은이 메서드를 재정의 하 고 ConversationalAgentSession의 인스턴스를 검색 하 여 현재 활성화 된 창에 신호를 보내야 합니다. 응용 프로그램이 활성화 되 면 Windows는 이벤트를 사용 하 여 음성 정품 인증 발생을 알립니다 `ConversationalAgentSession.OnSignalDetected` . 를 검색 하는 즉시이 이벤트에 이벤트 처리기를 추가 `ConversationalAgentSession` 합니다.

## <a name="keyword-verification"></a>키워드 확인

음성 에이전트 응용 프로그램이 음성으로 활성화 되 면 다음 단계는 키워드 검색이 유효한 지 확인 하는 것입니다. Windows에서는 키워드 확인을 위한 솔루션을 제공 하지 않지만, 음성 도우미가 가설 정품 인증에서 오디오에 액세스 하 고 자체 확인을 완료할 수 있도록 합니다.

### <a name="retrieve-activation-audio"></a>정품 인증 오디오 검색

[오디오 그래프](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph) 를 만들어의에 전달 합니다 `CreateAudioDeviceInputNodeAsync` `ConversationalAgentSession` . 그러면 *키워드가 검색 되기 약 3 초 전에*오디오를 사용 하 여 그래프의 오디오 버퍼가 로드 됩니다. 이 추가 선행 오디오는 광범위 한 키워드 길이와 스피커 속도를 수용 하기 위해 포함 되었습니다. 그런 다음 오디오 그래프에서 [QuantumStarted](https://docs.microsoft.com/uwp/api/windows.media.audio.audiograph.quantumstarted?view=winrt-18362) 이벤트를 처리 하 여 오디오 데이터를 검색 합니다.

```csharp
var inputNode = await agentSession.CreateAudioDeviceInputNodeAsync(audioGraph);
var outputNode = inputGraph.CreateFrameOutputNode();
inputNode.AddOutgoingConnection(outputNode);
audioGraph.QuantumStarted += OnQuantumStarted;
```

참고: 오디오 버퍼에 포함 된 선행 오디오로 인해 키워드 확인이 실패할 수 있습니다. 이 문제를 해결 하려면 키워드 확인을 위해 오디오를 보내기 전에 오디오 버퍼의 시작 부분을 자릅니다. 이 초기 트리밍을 각 보조자에 맞게 조정 해야 합니다.

### <a name="launch-in-the-foreground"></a>포그라운드에서 시작

키워드 확인이 성공 하면 응용 프로그램은 UI를 표시 하기 위해 포그라운드로 이동 해야 합니다. API를 호출 `ConversationalAgentSession.RequestForegroundActivationAsync` 하 여 응용 프로그램을 포그라운드로 이동 합니다.

### <a name="transition-from-compact-view-to-full-view"></a>Compact 보기에서 전체 보기로 전환

응용 프로그램을 먼저 음성으로 정품 인증 하는 경우에는 압축 된 보기에서 시작 됩니다. Windows의 음성 도우미에 대 한 다양 한 보기 및 전환에 대 한 지침은 [음성 활성화 미리 보기에 대 한 디자인 지침](windows-voice-assistants-best-practices.md#design-guidance-for-voice-activation-preview) 을 참조 하세요.

Compact 보기에서 전체 앱 보기로 전환 하려면 ApplicationView API를 사용 합니다 `TryEnterViewModeAsync` .

```csharp
var appView = ApplicationView.GetForCurrentView();
await appView.TryEnterViewModeAsync(ApplicationViewMode.Default);
```

## <a name="implementing-above-lock-activation"></a>위의 잠금 활성화 구현

다음 단계에서는 예제 코드에 대 한 참조 및 응용 프로그램 수명 주기 관리에 대 한 지침을 포함 하 여 Windows에서 음성 길잡이가 위의 잠금을 실행 하도록 설정 하는 요구 사항을 다룹니다.

위의 잠금 환경을 디자인 하는 방법에 대 한 지침은 [모범 사례 가이드](windows-voice-assistants-best-practices.md)를 참조 하세요.

앱이 잠금 위에 표시 되 면 "키오스크 모드"로 간주 됩니다. 키오스크 모드를 사용 하는 앱을 구현 하는 방법에 대 한 자세한 내용은 [키오스크 모드 설명서](https://docs.microsoft.com/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access)를 참조 하세요.

### <a name="transitioning-above-lock"></a>위의 잠금 전환

위의 활성화 잠금은 잠금의 활성화와 비슷합니다. 응용 프로그램의 활성 인스턴스가 없으면 백그라운드에서 새 인스턴스가 시작 되 고 `OnBackgroundActivated` App.xaml.cs가 호출 됩니다. 응용 프로그램의 인스턴스가 있는 경우 해당 인스턴스는 이벤트를 통해 알림을 받게 됩니다 `ConversationalAgentSession.SignalDetected` .

응용 프로그램에서 위의 잠금을 아직 표시 하지 않는 경우를 호출 해야 `ConversationalAgentSession.RequestForegroundActivationAsync` 합니다. 이렇게 하면 `OnLaunched` App.xaml.cs에서 메서드가 트리거되고이는 잠금 위에 표시 되는 뷰로 이동 해야 합니다.

### <a name="detecting-lock-screen-transitions"></a>잠금 화면 전환 감지

Windows SDK의 ConversationalAgent 라이브러리는 잠금 화면 상태와 잠금 화면 상태에 대 한 변경 내용을 쉽게 액세스할 수 있도록 하는 API를 제공 합니다. 현재 잠금 화면 상태를 검색 하려면 필드를 확인 `ConversationalAgentSession.IsUserAuthenticated` 합니다. 잠금 상태의 변경 내용을 검색 하려면 개체의 이벤트에 이벤트 처리기를 추가 `ConversationalAgentSession` `SystemStateChanged` 합니다. 화면이 잠금 해제에서 잠김 또는 그 반대로 변경 될 때마다 발생 합니다. 이벤트 인수의 값이 이면 `ConversationalAgentSystemStateChangeType.UserAuthentication` 잠금 화면 상태가 변경 된 것입니다.

```csharp
conversationalAgentSession.SystemStateChanged += (s, e) =>
{
    if (e.SystemStateChangeType == ConversationalAgentSystemStateChangeType.UserAuthentication)
    {
        // Handle lock state change
    }
};
```

### <a name="detecting-above-lock-activation-user-preference"></a>위의 잠금 활성화 사용자 기본 설정 검색

음성 정품 인증 개인 정보 설정 페이지의 응용 프로그램 항목에는 위의 잠금 기능에 대 한 토글 설정이 있습니다. 앱이 위의 잠금을 시작할 수 있도록 하려면 사용자가이 설정을 켜야 합니다. 위의 잠금 권한 상태는 ActivationSignalDetectionConfiguration 개체에도 저장 됩니다. AvailabilityInfo 권한 상태는 사용자가 위의 잠금 권한을 지정 했는지 여부를 반영 합니다. 이 설정을 사용 하지 않도록 설정한 경우 음성 응용 프로그램은 응용 프로그램에 대해 위의 잠금 활성화를 사용 하도록 설정 하는 방법에 대 한 지침이 포함 된 "voiceactivation" 링크의 적절 한 설정 페이지로 이동 하도록 사용자에 게 요청할 수 있습니다.

## <a name="closing-the-application"></a>응용 프로그램 닫기

잠금 보다 높거나 낮은 상태에서 프로그래밍 방식으로 응용 프로그램을 닫으려면 API를 사용 `WindowService.CloseWindow()` 합니다. 이렇게 하면 OnSuspend를 비롯 한 모든 UWP 수명 주기 메서드가 트리거되고 응용 프로그램에서 인스턴스를 삭제 하는 것을 허용 `ConversationalAgentSession` 합니다.

> [!NOTE]
> 응용 프로그램은 [아래 잠금 인스턴스](https://docs.microsoft.com/windows-hardware/drivers/partnerapps/create-a-kiosk-app-for-assigned-access#add-a-way-out-of-assigned-access-)를 닫지 않고 닫을 수 있습니다. 이 경우 위의 잠금 보기를 "정리" 하 여 화면이 잠금 해제 된 후에는 위의 잠금 보기를 조작 하려고 하는 이벤트 처리기 나 태스크가 없도록 해야 합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [예제 및 코드 연습을 보려면 UWP Voice Assistant 샘플 앱을 방문 하세요.](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample)
