---
title: Azure 애플리케이션 Insights의 원격 분석 채널 | Microsoft Docs
description: .NET 및 .NET Core 용 Azure 애플리케이션 Insights Sdk에서 원격 분석 채널을 사용자 지정 하는 방법입니다.
ms.topic: conceptual
ms.date: 05/14/2019
ms.custom: devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: 41d2feefc5af1e795520d9b3d90809e625502fa6
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918403"
---
# <a name="telemetry-channels-in-application-insights"></a>Application Insights의 원격 분석 채널

원격 분석 채널은 [Azure 애플리케이션 Insights sdk](./app-insights-overview.md)의 필수적인 부분입니다. Application Insights 서비스에 대 한 원격 분석의 버퍼링 및 전송을 관리 합니다. Sdk의 .NET 및 .NET Core 버전에는 두 가지 기본 제공 원격 분석 채널 (및)이 있습니다. `InMemoryChannel` `ServerTelemetryChannel` 이 문서에서는 채널 동작을 사용자 지정 하는 방법을 비롯 하 여 각 채널에 대해 자세히 설명 합니다.

## <a name="what-are-telemetry-channels"></a>원격 분석 채널 이란?

원격 분석 채널은 원격 분석 항목을 버퍼링 하 고 Application Insights 서비스로 전송 하 여 쿼리 및 분석을 위해 저장 합니다. 원격 분석 채널은 인터페이스를 구현 하는 클래스입니다 [`Microsoft.ApplicationInsights.ITelemetryChannel`](/dotnet/api/microsoft.applicationinsights.channel.itelemetrychannel?view=azure-dotnet) .

원격 분석 `Send(ITelemetry item)` 채널의 메서드는 모든 원격 분석 이니셜라이저 및 원격 분석 프로세서가 호출 된 후에 호출 됩니다. 따라서 원격 분석 프로세서에 의해 삭제 된 항목은 채널에 도달 하지 않습니다. `Send()` 는 일반적으로 백 엔드에 항목을 즉시 보내지 않습니다. 일반적으로 메모리에 버퍼를 버퍼링 하 고 효율적인 전송을 위해 일괄 처리로 보냅니다.

또한 [라이브 메트릭 스트림](live-stream.md) 에는 원격 분석의 라이브 스트리밍을 지 원하는 사용자 지정 채널이 있습니다. 이 채널은 일반 원격 분석 채널과 독립적 이며이 문서는 적용 되지 않습니다.

## <a name="built-in-telemetry-channels"></a>기본 제공 원격 분석 채널

Application Insights .NET 및 .NET Core Sdk는 두 개의 기본 제공 채널과 함께 제공 됩니다.

* `InMemoryChannel`: 항목이 전송 될 때까지 메모리에 항목을 버퍼링 하는 간단한 채널입니다. 항목은 메모리에 버퍼링 되 고 30 초 마다 또는 500 항목이 버퍼링 될 때마다 플러시됩니다. 이 채널은 실패 후 원격 분석을 다시 시도 하지 않으므로 최소한의 안정성 보장을 제공 합니다. 또한이 채널은 디스크에 항목을 유지 하지 않으므로, 보내지 않은 모든 항목은 응용 프로그램이 종료 될 때 영구적으로 손실 됩니다 (정상 또는 그렇지 않음). 이 채널은 `Flush()` 메모리 내 원격 분석 항목을 동기적으로 강제 플러시하는 데 사용할 수 있는 메서드를 구현 합니다. 이 채널은 동기 플러시가 이상적인 단기 실행 응용 프로그램에 적합 합니다.

    이 채널은 대규모 Microsoft ApplicationInsights NuGet 패키지의 일부 이며, 다른 사용자가 구성 되지 않은 경우 SDK에서 사용 하는 기본 채널입니다.

* `ServerTelemetryChannel`: 재시도 정책 및 로컬 디스크에 데이터를 저장 하는 기능이 있는 고급 채널입니다. 일시적인 오류가 발생 하는 경우이 채널은 원격 분석 전송을 다시 시도 합니다. 또한이 채널은 로컬 디스크 저장소를 사용 하 여 네트워크 중단 또는 높은 원격 분석 볼륨에서 디스크에 항목을 유지 합니다. 이러한 재시도 메커니즘과 로컬 디스크 저장소로 인해이 채널은 더 안정적으로 간주 되므로 모든 프로덕션 시나리오에 권장 됩니다. 이 채널은 공식 설명서에 따라 구성 된 [ASP.NET](./asp-net.md) 및 [ASP.NET Core](./asp-net-core.md) 응용 프로그램에 대 한 기본값입니다. 이 채널은 장기 실행 프로세스를 포함 하는 서버 시나리오에 최적화 되어 있습니다. [`Flush()`](#which-channel-should-i-use)이 채널에서 구현 하는 메서드는 동기화 되지 않습니다.

    이 채널은 TelemetryChannel NuGet 패키지로 제공 되며, AspNetCore NuGet 패키지를 사용 하는 경우 자동으로 획득 됩니다 (예를 들어).

## <a name="configure-a-telemetry-channel"></a>원격 분석 채널 구성

원격 분석 채널을 활성 원격 분석 구성으로 설정 하 여 구성 합니다. ASP.NET 응용 프로그램의 경우 구성에는 원격 분석 채널 인스턴스를로 설정 하거나를 수정 하는 작업이 포함 됩니다 `TelemetryConfiguration.Active` `ApplicationInsights.config` . ASP.NET Core 응용 프로그램의 경우 구성에는 종속성 주입 컨테이너에 채널을 추가 하는 작업이 포함 됩니다.

다음 섹션에서는 `StorageFolder` 다양 한 응용 프로그램 유형에 서 채널에 대 한 설정을 구성 하는 예를 보여 줍니다. `StorageFolder` 구성 가능한 설정 중 하나일 뿐입니다. 구성 설정의 전체 목록은이 문서의 뒷부분에 있는 [설정 섹션](#configurable-settings-in-channels) 을 참조 하세요.

### <a name="configuration-by-using-applicationinsightsconfig-for-aspnet-applications"></a>ASP.NET 응용 프로그램에 대해 ApplicationInsights.config를 사용 하 여 구성

[ApplicationInsights.config](configuration-with-applicationinsights-config.md) 에서 다음 섹션은를 `ServerTelemetryChannel` `StorageFolder` 사용자 지정 위치로 설정 하 여 구성 된 채널을 보여 줍니다.

```xml
    <TelemetrySinks>
        <Add Name="default">
            <TelemetryProcessors>
                <!-- Telemetry processors omitted for brevity  -->
            </TelemetryProcessors>
            <TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel, Microsoft.AI.ServerTelemetryChannel">
                <StorageFolder>d:\temp\applicationinsights</StorageFolder>
            </TelemetryChannel>
        </Add>
    </TelemetrySinks>
```

### <a name="configuration-in-code-for-aspnet-applications"></a>ASP.NET 응용 프로그램에 대 한 코드의 구성

다음 코드는가 `StorageFolder` 사용자 지정 위치로 설정 된 ' ServerTelemetryChannel ' 인스턴스를 설정 합니다. 일반적으로 Global.aspx.cs의 메서드에서 응용 프로그램의 시작 부분에이 코드를 추가 `Application_Start()` 합니다.

```csharp
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
protected void Application_Start()
{
    var serverTelemetryChannel = new ServerTelemetryChannel();
    serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
    serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
}
```

### <a name="configuration-in-code-for-aspnet-core-applications"></a>ASP.NET Core 응용 프로그램에 대 한 코드의 구성

클래스의 메서드를 다음과 같이 수정 `ConfigureServices` 합니다 `Startup.cs` .

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;

public void ConfigureServices(IServiceCollection services)
{
    // This sets up ServerTelemetryChannel with StorageFolder set to a custom location.
    services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel() {StorageFolder = @"d:\temp\applicationinsights" });

    services.AddApplicationInsightsTelemetry();
}

```

> [!IMPORTANT]
> ASP.NET Core 응용 프로그램에는를 사용 하 여 채널을 구성 `TelemetryConfiguration.Active` 하지 않는 것이 좋습니다.

### <a name="configuration-in-code-for-netnet-core-console-applications"></a>.NET/.NET Core 콘솔 응용 프로그램에 대 한 코드의 구성

콘솔 앱의 경우 코드는 .NET 및 .NET Core 모두에 대해 동일 합니다.

```csharp
var serverTelemetryChannel = new ServerTelemetryChannel();
serverTelemetryChannel.StorageFolder = @"d:\temp\applicationinsights";
serverTelemetryChannel.Initialize(TelemetryConfiguration.Active);
TelemetryConfiguration.Active.TelemetryChannel = serverTelemetryChannel;
```

## <a name="operational-details-of-servertelemetrychannel"></a>ServerTelemetryChannel의 작업 세부 정보

`ServerTelemetryChannel` 는 도착 항목을 메모리 내 버퍼에 저장 합니다. 항목은 `Transmission` 30 초 마다, 또는 500 항목이 버퍼링 될 때 인스턴스로 직렬화, 압축 및 저장 됩니다. 단일 `Transmission` 인스턴스는 최대 500 개 항목을 포함 하며 단일 HTTPS 호출을 통해 Application Insights 서비스에 전송 되는 원격 분석의 일괄 처리를 나타냅니다.

기본적으로 최대 10 개의 인스턴스를 `Transmission` 병렬로 전송할 수 있습니다. 원격 분석이 더 빠른 속도로 도착 하거나 네트워크 또는 Application Insights 백 엔드가 느려지는 경우 `Transmission` 인스턴스는 메모리에 저장 됩니다. 이 메모리 내 버퍼의 기본 용량은 `Transmission` 5mb입니다. 메모리 내 용량이 초과 되 면 `Transmission` 인스턴스는 50 MB까지 로컬 디스크에 저장 됩니다. `Transmission` 인스턴스는 네트워크 문제가 있는 경우에도 로컬 디스크에 저장 됩니다. 로컬 디스크에 저장 되는 항목만 응용 프로그램 작동 중단으로 유지 됩니다. 응용 프로그램이 다시 시작 될 때마다 전송 됩니다.

## <a name="configurable-settings-in-channels"></a>채널의 구성 가능한 설정

각 채널의 구성 가능한 설정에 대 한 전체 목록은 다음을 참조 하세요.

* [InMemoryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Channel/InMemoryChannel.cs)

* [ServerTelemetryChannel](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/ServerTelemetryChannel/ServerTelemetryChannel.cs)

에 가장 일반적으로 사용 되는 설정은 `ServerTelemetryChannel` 다음과 같습니다.

1. `MaxTransmissionBufferCapacity`: 메모리의 버퍼 전송을 위해 채널에서 사용 하는 최대 메모리 양 (바이트)입니다. 이 용량에 도달 하면 새 항목이 로컬 디스크에 직접 저장 됩니다. 기본값은 5MB입니다. 높은 값을 설정 하면 디스크 사용이 감소 하지만 응용 프로그램이 충돌 하는 경우 메모리의 항목이 손실 됩니다.

1. `MaxTransmissionSenderCapacity`: 동시에 `Transmission` Application Insights 하기 위해 전송 되는 최대 인스턴스 수입니다. 기본값은 10입니다. 이 설정은 대규모 원격 분석을 생성할 때 권장 되는 더 높은 수로 구성할 수 있습니다. 높은 볼륨은 일반적으로 부하 테스트 중 또는 샘플링을 해제할 때 발생 합니다.

1. `StorageFolder`: 필요에 따라 디스크에 항목을 저장 하기 위해 채널에서 사용 하는 폴더입니다. 다른 경로를 명시적으로 지정 하지 않으면 Windows 에서% LOCALAPPDATA% 또는% TEMP%가 사용 됩니다. Windows 이외의 환경에서는 유효한 위치를 지정 해야 합니다. 그렇지 않으면 원격 분석이 로컬 디스크에 저장 되지 않습니다.

## <a name="which-channel-should-i-use"></a>어떤 채널을 사용 해야 하나요?

`ServerTelemetryChannel` 는 장기 실행 응용 프로그램을 포함 하는 대부분의 프로덕션 시나리오에 권장 됩니다. `Flush()`에서 구현 하는 메서드는 `ServerTelemetryChannel` 동기적이 지 않으며 메모리 나 디스크에서 보류 중인 모든 항목을 전송 하는 것도 보장 하지 않습니다. 응용 프로그램이 종료 되려고 하는 시나리오에서이 채널을 사용 하는 경우를 호출한 후 약간의 지연을 도입 하는 것이 좋습니다 `Flush()` . 필요할 수 있는 정확한 지연 시간은 예측할 수 없습니다. 이는 메모리에 있는 항목 또는 인스턴스의 수 `Transmission` , 디스크의 수, 백 엔드에서 전송 되는 항목 수, 채널이 지 수 백오프 시나리오의 중간에 있는지 여부 등의 요소에 따라 달라 집니다.

동기 플러시를 수행 해야 하는 경우를 사용 하는 것이 좋습니다 `InMemoryChannel` .

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="does-the-application-insights-channel-guarantee-telemetry-delivery-if-not-what-are-the-scenarios-in-which-telemetry-can-be-lost"></a>Application Insights 채널은 원격 분석 배달을 보장 하나요? 그렇지 않은 경우 원격 분석이 손실 될 수 있는 시나리오는 무엇입니까?

짧은 대답은 기본 제공 채널이 백 엔드에 대 한 원격 분석 배달의 트랜잭션 형식 보증을 제공 하지 않는다는 것입니다. `ServerTelemetryChannel` 는 신뢰할 수 있는 배달용과 비교 하 여 더 고급 `InMemoryChannel` 이지만 원격 분석을 전송 하는 데에는 최상의 시도만 수행 합니다. 다음과 같은 일반적인 시나리오를 비롯 한 몇 가지 상황에서는 원격 분석이 여전히 손실 될 수 있습니다.

1. 응용 프로그램의 작동이 중단 되 면 메모리의 항목이 손실 됩니다.

1. 네트워크 문제를 연장 하는 동안 원격 분석을 잃게 됩니다. 원격 분석은 네트워크 중단 중에 또는 Application Insights 백 엔드에서 문제가 발생 하는 경우 로컬 디스크에 저장 됩니다. 그러나 48 시간 보다 오래 된 항목은 삭제 됩니다.

1. Windows에서 원격 분석을 저장 하는 데 사용할 수 있는 기본 디스크 위치 는% LOCALAPPDATA% 또는% TEMP%입니다. 이러한 위치는 일반적으로 컴퓨터에 로컬로 있습니다. 응용 프로그램이 물리적 위치에서 다른 위치로 마이그레이션되면 원래 위치에 저장 된 원격 분석은 모두 손실 됩니다.

1. Windows의 Web Apps 기본 디스크 저장소 위치는 D:\local\LocalAppData.. 이 위치는 지속 되지 않습니다. 앱을 다시 시작 하 고, 스케일 아웃 하 고, 기타 작업을 수행 하 여이에 저장 된 원격 분석의 손실을 초래 합니다. 기본값을 재정의 하 고 D:\home입니다 .와 같은 지속형 위치에 저장소를 지정할 수 있습니다. 그러나 이러한 지속형 위치는 원격 저장소에서 제공 되므로 속도가 느려질 수 있습니다.

### <a name="does-servertelemetrychannel-work-on-systems-other-than-windows"></a>ServerTelemetryChannel는 Windows 이외의 시스템에서 작동 하나요?

패키지와 네임 스페이스의 이름에 "WindowsServer"가 포함 되어 있지만이 채널은 Windows 이외의 시스템에서 지원 됩니다. 단, 다음은 예외입니다. Windows 이외의 시스템에서 채널은 기본적으로 로컬 저장소 폴더를 만들지 않습니다. 로컬 저장소 폴더를 만들고 채널을 사용 하도록 구성 해야 합니다. 로컬 저장소가 구성 된 후에는 채널이 모든 시스템에서 동일한 방식으로 작동 합니다.

### <a name="does-the-sdk-create-temporary-local-storage-is-the-data-encrypted-at-storage"></a>SDK에서 임시 로컬 스토리지를 작성하나요? 데이터가 저장소에서 암호화 되나요?

SDK는 네트워크 문제나 조정 중에 로컬 저장소에 원격 분석 항목을 저장 합니다. 이 데이터는 로컬로 암호화 되지 않습니다.

Windows 시스템의 경우 SDK는 자동 으로% TEMP% 또는% LOCALAPPDATA% 디렉터리에 임시 로컬 폴더를 만들고 관리자 및 현재 사용자만 액세스할 수 있도록 제한 합니다.

Windows 이외의 시스템의 경우에는 SDK에서 로컬 저장소를 자동으로 만들지 않으므로 기본적으로 로컬에 저장 되는 데이터가 없습니다. 저장소 디렉터리를 직접 만들고 채널을 사용 하도록 구성할 수 있습니다. 이 경우 디렉터리에 보안이 설정 되었는지 확인 해야 합니다.
자세한 내용은 [데이터 보호 및 개인 정보](data-retention-privacy.md#does-the-sdk-create-temporary-local-storage)를 참조 하세요.

## <a name="open-source-sdk"></a>오픈 소스 SDK
Application Insights에 대 한 모든 SDK와 마찬가지로 채널은 오픈 소스입니다. [공식 GitHub](https://github.com/Microsoft/ApplicationInsights-dotnet)리포지토리에서 코드를 읽고 참여 하거나 문제를 보고 합니다.

## <a name="next-steps"></a>다음 단계

* [샘플링](./sampling.md)
* [SDK 문제 해결](./asp-net-troubleshoot-no-data.md)

