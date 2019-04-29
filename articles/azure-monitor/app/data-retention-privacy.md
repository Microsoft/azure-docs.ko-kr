---
title: Azure Application Insights 데이터 보존 및 저장소 | Microsoft Docs
description: 보존 및 개인 정보 취급 방침
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: a6268811-c8df-42b5-8b1b-1d5a7e94cbca
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: mbullwin
ms.openlocfilehash: 0f8f1c5585eb13506baea1e5ddbe611cc931758e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60899251"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Application Insights 데이터 수집, 보존 및 저장소

앱에 [Azure Application Insights][start] SDK를 설치하는 경우 앱에 대한 원격 분석을 클라우드로 보냅니다. 담당하는 개발자는 전송되는 데이터가 정확한 내용, 데이터에 발생한 내용, 데이터를 제어할 수 있는 방법을 알고자 합니다. 특히 중요한 데이터를 보낼 수 있는지, 저장되었는지 및 얼마나 안전한지를 파악합니다. 

우선 짧은 응답은 다음과 같습니다.

* "초기"를 실행하는 표준 원격 분석 모듈은 서비스에 중요한 데이터를 전송하지 않을 가능성이 있습니다. 원격 분석은 로드, 성능 및 사용 현황 메트릭, 예외 보고서 및 기타 진단 데이터와 관련되어 있습니다. 진단 보고서에 표시되는 기본 사용자 데이터는 URL입니다. 하지만 앱은 어떤 경우에도 URL에 일반 텍스트로 중요한 데이터를 배치하지 않아야 합니다.
* 추가 사용자 지정 원격 분석을 보내는 코드를 작성하여 사용 현황의 진단 및 모니터링을 도울 수 있습니다. (이 확장성은 Application Insights의 매우 유용한 기능입니다.) 실수로 개인 및 기타 중요한 데이터가 포함되도록 이 코드를 작성할 수 있습니다. 애플리케이션이 이러한 데이터를 사용하여 작동하는 경우 작성하는 모든 코드에 철저한 검토 프로세스를 적용해야 합니다.
* 앱을 개발하고 테스트하는 동안 SDK에서 보낼 항목을 검사하기 쉽습니다. IDE 및 브라우저의 디버깅 출력 창에 데이터가 나타납니다. 
* 데이터는 미국 또는 유럽의 [Microsoft Azure](https://azure.com) 서버에 저장됩니다. 그러나 앱은 어디서나 실행할 수 있습니다. Azure는 [강력한 보안 프로세스를 가지고 광범위한 규정 준수 표준을 충족](https://azure.microsoft.com/support/trust-center/)합니다. 사용자와 지정된 팀만 데이터에 액세스할 수 있습니다. Microsoft 직원은 지식으로 제한된 특정 상황에서만 해당 데이터에 제한된 액세스 권한을 갖을 수 있습니다. 전송 중 및 미사용 시 암호화 됩니다.

이 문서의 나머지 부분에서는 이러한 대답에 대해 더 자세하게 설명합니다. 자체 포함되도록 설계되어 소속 팀에 속하지 않은 동료에게 표시할 수 있습니다.

## <a name="what-is-application-insights"></a>Application Insights란?
[Azure Application Insights][start]는 라이브 애플리케이션의 성능 및 가용성을 향상시키는 Microsoft에서 제공하는 서비스입니다. 테스트 중인 경우 및 게시하거나 배포한 후에 실행 중인 모든 시간 동안 애플리케이션을 모니터링합니다. 예를 들어 Application Insights는 많은 사용자를 가져오는 시간, 앱이 얼마나 반응하는지, 종속된 외부 서비스에서 얼마나 잘 제공되는지를 보여주는 차트 및 테이블을 만듭니다. 충돌, 오류 또는 성능 문제가 있는 경우 세부 정보에서 원격 분석 데이터를 통해 검색하여 원인을 진단할 수 있습니다. 그리고 앱의 성능과 가용성에 변경 사항이 있는 경우 서비스는 사용자에게 전자 메일을 보냅니다.

이 기능을 가져오기 위해 애플리케이션에서 해당 코드의 일부가 되는 Application Insights SDK를 설치합니다. 앱이 실행 중일 때 SDK는 작업을 모니터링하고 Application Insights 서비스에 원격 분석을 보냅니다. [Microsoft Azure](https://azure.com)에서 호스팅하는 클라우드 서비스입니다. (하지만 Application Insights는 Azure에서 호스팅되는 서비스가 아닌 모든 애플리케이션에 대해 작동합니다.)

Application Insights 서비스는 원격 분석 데이터를 저장하고 분석합니다. 저장된 원격 분석을 통해 분석 또는 검색을 보려면 Azure 계정에 로그인하고 애플리케이션에 Application Insights 리소스를 엽니다. 또한 팀의 다른 구성원 또는 지정된 Azure 구독자와 데이터에 대한 액세스를 공유할 수 있습니다.

예를 들어 데이터베이스 또는 외부 도구에 Application Insights 서비스에서 내보낸 데이터가 있을 수 있습니다. 서비스에서 가져와야 하는 특수 키를 사용하여 각 도구를 제공합니다. 필요한 경우 키를 해지할 수 있습니다. 

Application Insights SDK는 사용자 고유의 Java EE 또는 ASP.NET 서버나 Azure에 호스트된 웹 서비스, 웹 클라이언트(즉, 웹 페이지에서 실행되는 코드), 데스크톱 앱 및 서비스, Windows Phone, iOS, Android 등의 디바이스 앱과 같은 다양한 애플리케이션 유형에 사용할 수 있습니다. 모두 동일한 서비스에 원격 분석을 보냅니다.

## <a name="what-data-does-it-collect"></a>어떤 데이터를 수집하나요?
### <a name="how-is-the-data-is-collected"></a>데이터는 어떻게 수집되나요?
세 가지 데이터 원본이 있습니다.

* [개발 시](../../azure-monitor/app/asp-net.md) 또는 [런타임 시](../../azure-monitor/app/monitor-performance-live-website-now.md) 앱과 통합하는 SDK가 있습니다. 다른 애플리케이션 형식에 대한 여러 SDK가 있습니다. 또한 페이지와 함께 최종 사용자의 브라우저에 로드하는 [웹 페이지에 대한 SDK](../../azure-monitor/app/javascript.md)가 있습니다.
  
  * 각 SDK에는 다양한 [모듈](../../azure-monitor/app/configuration-with-applicationinsights-config.md)이 있으며 이는 서로 다른 기술을 사용하여 다른 형식의 원격 분석 데이터를 수집합니다.
  * 개발 시 SDK를 설치하면 표준 모듈 외에도 API를 사용하여 사용자 고유의 원격 분석을 보낼 수 있습니다. 이 사용자 지정 원격 분석은 전송하려는 데이터를 포함할 수 있습니다.
* 또한 일부 웹 서버에는 앱과 함께 실행하고 CPU, 메모리 및 네트워크 선점에 대한 원격 분석을 보내는 에이전트가 있습니다. 예를 들어 Azure VM, Docker 호스트 및 [Java EE 서버](../../azure-monitor/app/java-agent.md)에는 이러한 에이전트가 있을 수 있습니다.
* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md) 는 정기적으로 웹앱에 요청을 전송하는 Microsoft에서 실행되는 프로세스입니다. 결과는 Application Insights 서비스에 전송됩니다.

### <a name="what-kinds-of-data-are-collected"></a>어떤 종류의 데이터를 수집하나요?
주요 범주는 다음과 같습니다.

* [웹 서버 원격 분석](../../azure-monitor/app/asp-net.md) - HTTP가 요청합니다.  URI, 요청, 응답 코드, 클라이언트 IP 주소를 처리하는 데 걸린 시간입니다. 세션 ID.
* [웹 페이지](../../azure-monitor/app/javascript.md) -페이지, 사용자 및 세션 수입니다. 페이지 로드 시간. 예외. Ajax 호출.
* 성능 카운터 - 메모리, CPU, IO, 네트워크 선점입니다.
* 클라이언트 및 서버 컨텍스트 - OS, 로캘, 디바이스 형식, 브라우저, 화면 해상도입니다.
* [예외](../../azure-monitor/app/asp-net-exceptions.md) 및 작동 중단 - **스택 덤프**, 작성 ID, CPU 형식입니다. 
* [종속성](../../azure-monitor/app/asp-net-dependencies.md) - REST, SQL, AJAX와 같은 외부 서비스를 호출합니다. URI 또는 연결 문자열, 시간, 성공, 명령입니다.
* [가용성 테스트](../../azure-monitor/app/monitor-web-app-availability.md) - 테스트, 단계, 응답의 기간입니다.
* [추적 로그](../../azure-monitor/app/asp-net-trace-logs.md) 및 [사용자 지정 원격 분석](../../azure-monitor/app/api-custom-events-metrics.md) - **로그 또는 원격 분석에 코딩한 것**입니다.

[자세한 내용](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>수집되는 항목을 어떻게 확인할 수 있나요?
Visual Studio를 사용하여 앱을 개발하는 경우 디버그(F5) 모드에서 앱을 실행합니다. 원격 분석은 출력 창에 나타납니다. 여기에서 복사하고 쉬운 검사를 JSON으로 형식으로 지정할 수 있습니다. 

![](./media/data-retention-privacy/06-vs.png)

진단 창에 보다 읽기 쉬운 보기도 있습니다.

웹 페이지의 경우 브라우저의 디버깅 창을 엽니다.

![F12 키를 누르고 네트워크 탭을 엽니다.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>전송하기 전에 코드를 작성하여 원격 분석을 필터링할 수 있습니까?
[원격 분석 프로세서 플러그 인](../../azure-monitor/app/api-filtering-sampling.md)을 작성하여 수행할 수 있습니다.

## <a name="how-long-is-the-data-kept"></a>데이터가 얼마 동안 보존되나요?
원시 데이터 요소(즉, Analytics에서 쿼리하고 Search에서 검사할 수 있는 항목)는 최대 90일 동안 보관됩니다. 이보다 더 오래 데이터를 보존해야 하는 경우 [연속 내보내기](../../azure-monitor/app/export-telemetry.md) 를 사용하여 해당 데이터를 저장소 계정에 복사할 수 있습니다.

집계 데이터(즉, 메트릭 탐색기에 표시되는 개수, 평균 및 기타 통계 데이터)는 90일 동안 1분 단위로 보존됩니다.

[디버그 스냅숏](../../azure-monitor/app/snapshot-debugger.md)은 7일 동안 저장됩니다. 이 보존 정책은 응용 프로그램 단위로 설정됩니다. 이 값을 늘려야 하는 경우 Azure Portal에서 지원 사례를 열어 증가를 요청할 수 있습니다.

## <a name="who-can-access-the-data"></a>데이터에 액세스할 수 있는 사용자는 누구인가요?
데이터는 사용자 및 조직 계정이 있는 경우 팀 멤버에게 표시됩니다. 

사용자와 팀 멤버는 데이터를 내보낼 수 있으며, 다른 위치로 복사하고 다른 사람에게 전달할 수 있습니다.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Microsoft는 내 앱이 Application Insights로 보내는 정보로 무엇을 하나요?
Microsoft는 서비스를 제공하기 위한 목적으로만 데이터를 사용합니다.

## <a name="where-is-the-data-held"></a>데이터가 저장되는 위치는 어디인가요?
* 미국, 유럽 또는 동남 아시아에 있습니다. 새 Application Insights 리소스를 만들 때 위치를 선택할 수 있습니다. 

#### <a name="does-that-mean-my-app-has-to-be-hosted-in-the-usa-europe-or-southeast-asia"></a>내 앱을 미국, 유럽 또는 동남 아시아에서 호스트해야 한다는 뜻인가요?
* 아니요. 애플리케이션은 자체 온-프레미스 호스트 또는 클라우드의 어디에서나 실행할 수 있습니다.

## <a name="how-secure-is-my-data"></a>내 데이터는 어느 정도 안전한가요?
Application Insights는 Azure 서비스입니다. 보안 정책은 [Azure 보안, 개인 정보 보호 및 규정 준수 백서](https://go.microsoft.com/fwlink/?linkid=392408)에 설명되어 있습니다.

데이터는 Microsoft Azure 서버에 저장됩니다. Azure Portal 계정의 경우 [Azure 보안, 개인 정보 및 규정 준수 문서](https://go.microsoft.com/fwlink/?linkid=392408)에 계정 제한 사항이 설명되어 있습니다.

Microsoft 직원의 사용자 데이터에 대한 액세스는 제한되어 있습니다. Microsoft에서는 사용자가 허락한 경우에만, 그리고 Application Insights 사용을 지원하는 데 필요한 경우에 사용자 데이터에 액세스합니다. 

모든 고객의 애플리케이션에 대한 집계 데이터(예: 데이터 속도 및 평균 추적 크기)는 Application Insights를 개선하는 데 사용됩니다.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>다른 사람의 원격 분석이 내 Application Insights 데이터에 방해가 될 수 있나요?
웹 페이지의 코드에서 찾을 수 있는 계측 키를 사용하여 계정에 추가 원격 분석을 보낼 수 있습니다. 추가 데이터가 충분하면 메트릭이 앱의 성능 및 사용을 올바르게 나타냅니다.

다른 프로젝트와 코드를 공유하는 경우 계측 키를 제거해야 합니다.

## <a name="is-the-data-encrypted"></a>데이터는 암호화되나요?
모든 데이터 미사용 암호화 되 고 있으므로 간에 이동 데이터 센터.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>내 애플리케이션에서 Application Insights 서버로 전송 중에 데이터가 암호화되나요?
예. 웹 서버, 디바이스 및 HTTPS 웹 페이지를 포함하여 거의 모든 SDK에서 https를 사용하여 포털로 데이터를 보냅니다. 유일한 예외는 일반 HTTP 웹 페이지에서 전송된 데이터입니다.

## <a name="does-the-sdk-create-temporary-local-storage"></a>SDK에서 임시 로컬 저장소를 작성하나요?

예, 엔드포인트에 도달할 수 없는 경우 특정 원격 분석 채널은 로컬에 데이터를 지속합니다. 영향을 받는 프레임워크 및 원격 분석 채널을 확인하려면 아래를 검토하세요.

로컬 저장소를 이용하는 원격 분석 채널은 사용자 애플리케이션을 실행하는 특정 계정으로 제한된 TEMP 또는 APPDATA 디렉터리에 임시 파일을 작성합니다. 이는 엔드포인트를 일시적으로 사용할 수 없거나 조정 제한에 도달했을 때 발생할 수 있습니다. 이 문제가 해결되면 원격 분석 채널이 모든 새 지속 데이터 및 지속된 데이터 전송을 재개합니다.

이 지속 된 데이터를 로컬로 암호화 되지 않습니다. 중요 한 경우 데이터를 검토 하 고 개인 데이터의 컬렉션을 제한 합니다. (자세한 정보는 [개인 데이터를 내보내고 삭제하는 방법](https://docs.microsoft.com/azure/application-insights/app-insights-customer-data#how-to-export-and-delete-private-data) 참조)

고객이 특정 보안 요구 사항으로 이 디렉터리를 구성해야 하는 경우 프레임워크별로 구성할 수 있습니다. 애플리케이션을 실행하는 프로세스에 이 디렉터리에 대한 쓰기 액세스 권한이 있는지 확인하세요. 그러나 의도하지 않은 사용자가 원격 분석을 읽을 수 없도록 보호되었는지도 확인하세요.

### <a name="java"></a>자바

`C:\Users\username\AppData\Local\Temp`는 데이터를 지속하는 데 사용됩니다. 이 위치는 구성 디렉터리에서 구성할 수 없으며 이 폴더에 대한 액세스 권한은 필수 자격 증명이 있는 특정 사용자로 제한됩니다. (여기서 [구현](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72) 참조)

###  <a name="net"></a>.Net

기본적으로 `ServerTelemetryChannel`은 현재 사용자의 로컬 앱 데이터 폴더 `%localAppData%\Microsoft\ApplicationInsights` 또는 임시 폴더`%TMP%`를 사용합니다. (여기서 [구현](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) 참조)


구성 파일:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

코드:

- 구성 파일에서 ServerTelemetryChannel 제거
- 이 코드 조각을 구성에 추가:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

기본적으로 `ServerTelemetryChannel`은 현재 사용자의 로컬 앱 데이터 폴더 `%localAppData%\Microsoft\ApplicationInsights` 또는 임시 폴더`%TMP%`를 사용합니다. (여기서 [구현](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) 참조) Linux 환경에서는 저장소 폴더를 지정하지 않으면 로컬 저장소가 비활성화됩니다.

다음 코드 조각은  `Startup.cs`  클래스의 `ConfigureServices()`  메서드에서 `ServerTelemetryChannel.StorageFolder`를 설정하는 방법을 보여줍니다.

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(자세한 정보는 [AspNetCore 사용자 지정 구성](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration) 참조 )

### <a name="nodejs"></a>Node.js

기본적으로 `%TEMP%/appInsights-node{INSTRUMENTATION KEY}`이(가) 데이터를 지속하는 데 사용됩니다. 이 폴더에 대한 액세스 권한은 현재 사용자 및 관리자로 제한됩니다. (여기서 [구현](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) 참조)

[Sender.ts](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384)에 있는 정적 변수 `Sender.TEMPDIR_PREFIX`의 런타임 값을 변경하여 폴더 접두사 `appInsights-node`을(를) 재정의할 수 있습니다.



## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>TLS 1.2를 사용하여 데이터를 Application Insights에 보내려면 어떻게 할까요?

Application Insights 엔드포인트에 전송 중인 데이터를 보호하려면 고객이 적어도 TLS(전송 계층 보안) 1.2 이상을 사용하도록 해당 애플리케이션을 구성하는 것이 좋습니다. 이전 버전의 TLS/SSL(Secure Sockets Layer)이 취약한 것으로 나타났습니다. 따라서 현재 이전 버전과 호환성을 허용하기 위해 작동하지만 **사용하지 않는 것이 좋으며** 업계는 이러한 이전 프로토콜에 대한 지원을 중단하도록 빠르게 변화하고 있습니다. 

[PCI 보안 표준 위원회](https://www.pcisecuritystandards.org/)는 이전 버전의 TLS/SSL를 사용하지 않고 추가 보안 프로토콜을 업그레이드하는 [최종 기한인 2018년 6월 30일](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf)을 설정했습니다. Azure가 레거시 지원을 삭제하면 애플리케이션/클라이언트가 TLS 1.2를 통해 통신할 수 없는 경우 Application Insights에 데이터를 보낼 수 없게 됩니다. 애플리케이션의 TLS 지원을 테스트하고 유효성을 검사하는 애플리케이션에서 사용하는 방법은 언어/프레임워크뿐만 아니라 운영 체제/플랫폼에 따라 달라집니다.

TLS 1.3 등을 사용할 수 있게 되면 더 안전한 최신 프로토콜을 자동으로 검색하고 활용할 수 있도록 플랫폼 수준 보안 기능을 중단할 수 있으므로 반드시 필요하지 않다면 애플리케이션이 TLS 1.2만을 사용하도록 명시적으로 설정하지 않는 것이 좋습니다. 특정 TLS/SSL 버전의 하드 코딩을 확인하려면 애플리케이션 코드에 대해 철저한 감사를 수행하는 것이 좋습니다.

### <a name="platformlanguage-specific-guidance"></a>플랫폼/언어 특정 지침

|플랫폼/언어 | 지원 | 추가 정보 |
| --- | --- | --- |
| Azure App Services  | 지원됨, 구성이 필요할 수 있습니다. | 지원은 2018년 4월에 발표되었습니다. [구성 세부 정보](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)에 대한 공지를 참고하세요.  |
| Azure Function App | 지원됨, 구성이 필요할 수 있습니다. | 지원은 2018년 4월에 발표되었습니다. [구성 세부 정보](https://blogs.msdn.microsoft.com/appserviceteam/2018/04/17/app-service-and-functions-hosted-apps-can-now-update-tls-versions/)에 대한 공지를 참고하세요. |
|.NET | 지원됨, 구성이 버전에 따라 다릅니다. | .NET 4.7 이전 버전에 대한 자세한 구성 정보는 [이러한 지침](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)을 참조하세요.  |
|상태 모니터 | 지원됨, 구성이 필요합니다. | 상태 모니터는 [OS 구성](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) + [.NET 구성](https://docs.microsoft.com/dotnet/framework/network-programming/tls#support-for-tls-12)을 사용하여 TLS 1.2를 지원합니다.
|Node.js |  지원됨, v10.5.0에서 구성이 필요할 수 있습니다. | 애플리케이션 특정 구성에 대해 [공식 Node.js TLS/SSL 설명서](https://nodejs.org/api/tls.html)를 사용합니다. |
|자바 | 지원됨, TLS 1.2에 대한 JDK 지원이 [JDK 6 업데이트 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) 및 [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html)에서 추가되었습니다. | JDK 8은 [기본적으로 TLS 1.2](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default)를 사용합니다.  |
|Linux | Linux 배포판은 TLS 1.2 지원에 대해 [OpenSSL](https://www.openssl.org)을 사용하는 경향이 있습니다.  | [OpenSSL Changelog](https://www.openssl.org/news/changelog.html)를 확인하여 OpenSSL 버전이 지원되는지 확인합니다.|
| Windows 8.0 - 10 | 지원됨, 기본적으로 활성화됩니다. | [기본 설정](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)을 여전히 사용하는지 확인하려면  |
| Windows Server 2012 - 2016 | 지원됨, 기본적으로 활성화됩니다. | [기본 설정](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings)을 여전히 사용하는지 확인하려면 |
| Windows 7 SP1 및 Windows Server 2008 R2 SP1 | 지원됨, 하지만 기본적으로 활성화되지 않습니다. | 활성화하는 방법에 대한 자세한 내용은 [TLS(전송 계층 보안) 레지스트리 설정](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings) 페이지를 참조하세요.  |
| Windows Server 2008 SP2 | TLS 1.2에 대한 지원에는 업데이트가 필요합니다. | Windows Server 2008 SP2에서 [TLS 1.2에 대한 지원을 추가하는 업데이트](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s)를 참조하세요. |
|Windows Vista | 지원되지 않습니다. | N/A

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Linux 배포에서 실행 중인 OpenSSL 버전을 확인합니다.

설치한 OpenSSL 버전을 확인하려면 터미널을 열고 다음을 실행합니다.

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Linux에서 테스트 TLS 1.2 트랜잭션을 실행합니다.

Linux 시스템이 TLS 1.2를 통해 통신할 수 있는지 확인하기 위해 기본 예비 테스트를 실행하려면: 터미널을 열고 다음을 실행합니다.

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Application Insights에 저장된 개인 데이터

[Application Insights 개인 데이터 아티클](../../azure-monitor/platform/personal-data-mgmt.md)에서는 이 문제를 자세히 설명합니다.

#### <a name="can-my-users-turn-off-application-insights"></a>내 사용자가 Application Insights를 끌 수 있나요?
직접 끌 수는 없습니다. 사용자가 Application Insights를 끄기 위해 작동할 수 있는 스위치는 제공되지 않습니다.

그러나 애플리케이션에서 이러한 기능을 구현할 수 있습니다. 모든 SDK에는 원격 분석 수집을 끄는 API 설정이 포함되어 있습니다. 

## <a name="data-sent-by-application-insights"></a>Application Insights에서 보내는 데이터
SDK는 플랫폼마다 다르며, 설치할 수 있는 여러 구성 요소가 있습니다. [Application Insights - 개요][start]를 참조하세요. 각 구성 요소마다 다른 데이터를 보냅니다.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>다양한 시나리오에서 전송되는 데이터 클래스

| 사용자 작업 | 수집되는 데이터 클래스(다음 표 참조) |
| --- | --- |
| [.NET 웹 프로젝트에 Application Insights SDK 추가][greenbrown] |ServerContext<br/>유추<br/>성능 카운터<br/>요청<br/>**예외**<br/>세션<br/>users |
| [IIS에서 상태 모니터 설치][redfield] |종속성<br/>ServerContext<br/>유추<br/>성능 카운터 |
| [Java 웹앱에 Application Insights SDK 추가][java] |ServerContext<br/>유추<br/>요청<br/>세션<br/>users |
| [웹 페이지에 JavaScript SDK 추가][client] |ClientContext  <br/>유추<br/>Page<br/>ClientPerf<br/>Ajax |
| [기본 속성 정의][apiproperties] |**속성**  |
| [호출 TrackMetric][api] |숫자 값<br/>**속성** |
| [호출 추적*][api] |이벤트 이름<br/>**속성** |
| [호출 TrackException][api] |**예외**<br/>스택 덤프<br/>**속성** |
| SDK는 데이터를 수집할 수 없습니다. 예를 들면 다음과 같습니다. <br/> - 성능 카운터에 액세스할 수 없음<br/> - 원격 분석 이니셜라이저 예외 |SDK 진단 |

[다른 플랫폼에 대한 SDK][platforms]의 경우 해당 문서를 참조하세요.

#### <a name="the-classes-of-collected-data"></a>수집되는 데이터 클래스

| 수집되는 데이터 클래스 | 포함(전체 목록 아님) |
| --- | --- |
| **속성** |**임의 데이터 - 코드에 의해 결정됨** |
| DeviceContext |ID, IP, 로캘, 디바이스 모델, 네트워크, 네트워크 종류, OEM 이름, 화면 해상도, 역할 인스턴스, 역할 이름, 디바이스 유형 |
| ClientContext  |OS, 로캘, 언어, 네트워크, 창 해상도 |
| 세션 |세션 ID |
| ServerContext |컴퓨터 이름, 로캘, OS, 디바이스, 사용자 세션, 사용자 컨텍스트, 작업 |
| 유추 |IP 주소, 타임스탬프, OS, 브라우저에서 지리적 위치 유추 |
| 메트릭 |메트릭 이름 및 값 |
| 이벤트 |이벤트 이름 및 값 |
| PageViews |URL 및 페이지 이름이나 화면 이름 |
| 클라이언트 성능 |URL/페이지 이름, 브라우저 로드 시간 |
| Ajax |웹 페이지에서 서버로 HTTP 호출 |
| 요청 |URL, 기간, 응답 코드 |
| 종속성 |유형(SQL, HTTP,...), 연결 문자열 또는 URI, 동기/비동기, 기간, 성공, SQL 문(상태 모니터 사용) |
| **예외** |유형, **메시지**, 호출 스택, 원본 파일 및 줄 번호, 스레드 ID |
| 충돌 |프로세스 ID, 부모 프로세스 ID, 충돌 스레드 ID; 애플리케이션 패치, ID, 빌드; 예외 유형, 주소, 이유; 난독 처리된 기호 및 레지스터, 이진 시작 및 끝 주소, 이진 이름 및 경로, CPU 종류 |
| 추적 |**메시지** 및 심각도 수준 |
| 성능 카운터 |프로세서 시간, 사용 가능한 메모리, 요청 속도, 예외 속도, 프로세스 전용 바이트, IO 속도, 요청 기간, 요청 큐 길이 |
| 가용성 |웹 테스트 응답 코드, 각 테스트 단계, 테스트 이름, 타임 스탬프, 성공, 응답 시간, 테스트 위치의 기간 |
| SDK 진단 |추적 메시지 또는 예외 |

[ApplicationInsights.config를 편집하여 일부 데이터를 해제][config]할 수 있습니다.

> [!NOTE]
> 클라이언트 IP는 지리적 위치를 유추하는 데 사용되지만 기본적으로 IP 데이터는 더 이상 저장되지 않으며 모든 0은 연결된 필드에 기록됩니다. 개인 데이터 처리에 대한 자세한 내용은 이 [문서](../../azure-monitor/platform/personal-data-mgmt.md#application-data)를 권장합니다. IP 주소를 저장해야 하는 경우 [원격 분석 이니셜라이저](./../../azure-monitor/app/api-filtering-sampling.md#add-properties-itelemetryinitializer)를 사용하여 수행할 수 있습니다.

## <a name="credits"></a>크레딧
이 제품은 [https://www.maxmind.com](https://www.maxmind.com)에 있는 MaxMind에서 작성된 GeoLite2 데이터를 포함합니다.



<!--Link references-->

[api]: ../../azure-monitor/app/api-custom-events-metrics.md
[apiproperties]: ../../azure-monitor/app/api-custom-events-metrics.md#properties
[client]: ../../azure-monitor/app/javascript.md
[config]: ../../azure-monitor/app/configuration-with-applicationinsights-config.md
[greenbrown]: ../../azure-monitor/app/asp-net.md
[java]: ../../azure-monitor/app/java-get-started.md
[platforms]: ../../azure-monitor/app/platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ../../azure-monitor/app/monitor-performance-live-website-now.md
[start]: ../../azure-monitor/app/app-insights-overview.md

