---
title: Azure Application Insights 스냅숏 디버거를 사용 하 여 문제 해결 | Microsoft Docs
description: 이 문서에서는 문제 해결 단계 및 개발자가 사용 하도록 설정 하거나 Application Insights 스냅숏 디버거를 사용 하 여 문제가 발생 하는 데 유용한 정보를 제공 합니다.
services: application-insights
documentationcenter: ''
author: brahmnes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 03/07/2019
ms.author: mbullwin
ms.openlocfilehash: bf19d4f5ce60411413c21fce12f9fe9d2f391bf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60783979"
---
# <a id="troubleshooting"></a> Application Insights 스냅숏 디버거를 사용 하도록 설정 하거나 스냅숏 보기 문제 해결
응용 프로그램에 대 한 Application Insights 스냅숏 디버거를 사용 하도록 설정 해도 예외에 대 한 스냅숏이 표시 되지 않는 경우에 문제를 해결 하려면 다음이 지침을 사용할 수 있습니다. 스냅숏이 생성 되지 않습니다 하는 여러 가지 이유가 있을 수 있습니다. 몇 가지 일반적인 원인은 스냅숏 상태 검사를 실행할 수 있습니다.

## <a name="use-the-snapshot-health-check"></a>스냅숏 상태 확인 사용
몇 가지 일반적인 문제로 인해 [디버그 스냅숏 열기]가 표시되지 않습니다. 오래된 스냅숏 수집기를 사용했거나(예: 일일 업로드 제한에 도달), 스냅숏을 업로드하는 데 시간이 오래 걸렸을 수도 있습니다. [스냅숏 상태 확인]을 사용하여 일반적인 문제를 해결합니다.

종단 간 추적 보기의 예외 창에는 [스냅숏 상태 확인]으로 이동하는 링크가 있습니다.

![스냅숏 상태 확인 진입](./media/snapshot-debugger/enter-snapshot-health-check.png)

채팅 모양의 대화형 인터페이스는 일반적인 문제를 찾아 수정하도록 안내합니다.

![상태 확인](./media/snapshot-debugger/healthcheck.png)

아직도 문제가 해결되지 않으면 다음 수동 문제 해결 단계를 참조하세요.

## <a name="verify-the-instrumentation-key"></a>계측 키 확인

게시된 애플리케이션에서 올바른 계측 키를 사용하는 있는지 확인합니다. 일반적으로 계측 키는 ApplicationInsights.config 파일에서 읽습니다. 포털에 표시된 Application Insights 리소스에 대한 계측 키와 동일한 값인지 확인합니다.

## <a name="upgrade-to-the-latest-version-of-the-nuget-package"></a>최신 버전의 NuGet 패키지로 업그레이드

스냅숏 디버거를 통해 활성화 된 경우는 [포털의 Application Insights 창](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json), 응용 프로그램은 이미 실행 되 고 최신 NuGet 패키지를 합니다. 스냅숏 디버거를 포함 하 여 활성화 된 경우는 [Microsoft.ApplicationInsights.SnapshotCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) NuGet 패키지를 최신 버전을 사용 하는 되도록 사용 하 여 Visual Studio의 NuGet 패키지 관리자 Microsoft.ApplicationInsights.SnapshotCollector 합니다. 릴리스 정보는 https://github.com/Microsoft/ApplicationInsights-Home/issues/167에 있습니다.

## <a name="check-the-uploader-logs"></a>업로더 로그 확인

스냅숏을 만들면 디스크에 미니덤프 파일(.dmp)이 생성됩니다. 별도의 업로더 프로세스에서 해당 미니덤프 파일을 만들어 관련 PDB와 함께 Application Insights 스냅숏 디버거 저장소에 업로드합니다. 미니덤프가 성공적으로 업로드되면 디스크에서 삭제됩니다. 업로더 프로세스에 대한 로그 파일은 디스크에 유지됩니다. App Service 환경에서는 `D:\Home\LogFiles`에서 이러한 로그를 찾을 수 있습니다. App Service에 대한 Kudu 관리 사이트를 사용하여 이러한 로그 파일을 찾을 수 있습니다.

1. Azure Portal에서 App Service 애플리케이션을 엽니다.
2. **고급 도구**를 클릭하거나 **Kudu**를 검색합니다.
3. **이동**을 클릭합니다.
4. **디버그 콘솔** 드롭다운 목록 상자에서 **CMD**를 선택합니다.
5. **LogFiles**를 클릭합니다.

이름이 `Uploader_` 또는 `SnapshotUploader_`로 시작하고 확장명이 `.log`인 파일이 하나 이상 있어야 합니다. 해당 아이콘을 클릭하여 모든 로그 파일을 다운로드하거나 브라우저에서 엽니다.
파일 이름에는 App Service 인스턴스를 식별하는 고유한 접미사가 포함됩니다. App Service 인스턴스가 둘 이상의 컴퓨터에서 호스팅되는 경우 각 컴퓨터에 대한 별도의 로그 파일이 있습니다. 업로더에서 새 미니덤프 파일을 검색하면 로그 파일에 기록됩니다. 성공적인 스냅숏 및 업로드의 예는 다음과 같습니다.

```
SnapshotUploader.exe Information: 0 : Received Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Creating minidump from Fork request ID 139e411a23934dc0b9ea08a626db16c5 from process 6368 (Low pri)
    DateTime=2018-03-09T01:42:41.8571711Z
SnapshotUploader.exe Information: 0 : Dump placeholder file created: 139e411a23934dc0b9ea08a626db16c5.dm_
    DateTime=2018-03-09T01:42:41.8728496Z
SnapshotUploader.exe Information: 0 : Dump available 139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7525022Z
SnapshotUploader.exe Information: 0 : Successfully wrote minidump to D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Uploading D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp, 214.42 MB (uncompressed)
    DateTime=2018-03-09T01:42:45.7681360Z
SnapshotUploader.exe Information: 0 : Upload successful. Compressed size 86.56 MB
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Extracting PDB info from D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp.
    DateTime=2018-03-09T01:42:59.6184651Z
SnapshotUploader.exe Information: 0 : Matched 2 PDB(s) with local files.
    DateTime=2018-03-09T01:42:59.6809606Z
SnapshotUploader.exe Information: 0 : Stamp does not want any of our matched PDBs.
    DateTime=2018-03-09T01:42:59.8059929Z
SnapshotUploader.exe Information: 0 : Deleted D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\139e411a23934dc0b9ea08a626db16c5.dmp
    DateTime=2018-03-09T01:42:59.8530649Z
```

> [!NOTE]
> 위의 예제는 Microsoft.ApplicationInsights.SnapshotCollector NuGet 패키지 버전 1.2.0에 있습니다. 이전 버전에서 업로더 프로세스는 `MinidumpUploader.exe`라고 하고 로그는 덜 자세하게 설명됩니다.

위 예에서 계측 키는 `c12a605e73c44346a984e00000000000`입니다. 이 값은 애플리케이션의 계측 키와 일치해야 합니다.
미니덤프는 ID가 `139e411a23934dc0b9ea08a626db16c5`인 스냅숏에 연결됩니다. 나중에 이 ID를 사용하여 Application Insights Analytics에서 연결된 예외 원격 분석을 찾을 수 있습니다.

업로더는 약 15분에 한 번씩 새 PDB를 검색합니다. 예를 들면 다음과 같습니다.

```
SnapshotUploader.exe Information: 0 : PDB rescan requested.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Scanning D:\home\site\wwwroot for local PDBs.
    DateTime=2018-03-09T01:47:19.4457768Z
SnapshotUploader.exe Information: 0 : Local PDB scan complete. Found 2 PDB(s).
    DateTime=2018-03-09T01:47:19.4614027Z
SnapshotUploader.exe Information: 0 : Deleted PDB scan marker : D:\local\Temp\Dumps\c12a605e73c44346a984e00000000000\6368.pdbscan
    DateTime=2018-03-09T01:47:19.4614027Z
```

App Service에서 호스팅되지 _않는_ 애플리케이션의 경우 업로더 로그는 미니덤프와 동일한 폴더 `%TEMP%\Dumps\<ikey>`(여기서 `<ikey>`는 계측 키)에 저장됩니다.

## <a name="troubleshooting-cloud-services"></a>Cloud Services 문제 해결
Cloud Services의 역할의 경우, 기본 임시 폴더가 너무 작아서 미니 덤프 파일을 저장할 수 없게 되어 스냅숏이 손실될 수 있습니다.
필요한 공간은 애플리케이션의 전체 작업 집합과 동시 스냅숏 수에 따라 다릅니다.
32비트 ASP.NET 웹 역할의 작업 집합은 일반적으로 200MB ~ 500MB 사이입니다.
둘 이상의 동시 스냅숏을 허용합니다.
예를 들어, 애플리케이션이 1GB의 전체 작업 집합을 사용하는 경우, 스냅숏 저장을 위해 2GB 이상의 디스크 공간이 있는지 확인해야 합니다.
스냅숏용 전용 로컬 리소스가 있는 클라우드 서비스 역할을 구성하려면 다음 단계를 수행합니다.

1. 클라우드 서비스 정의(.csdef) 파일을 편집하여 클라우드 서비스에 새 로컬 리소스를 추가합니다. 다음 예제에서는 크기가 5GB인 `SnapshotStore`라는 리소스를 정의합니다.
   ```xml
   <LocalResources>
     <LocalStorage name="SnapshotStore" cleanOnRoleRecycle="false" sizeInMB="5120" />
   </LocalResources>
   ```

2. 역할의 시작 코드를 수정하여 `SnapshotStore` 로컬 리소스를 가리키는 환경 변수를 추가합니다. 작업자 역할의 경우 역할의 `OnStart` 메서드에 코드를 추가해야 합니다.
   ```csharp
   public override bool OnStart()
   {
       Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
       return base.OnStart();
   }
   ```
   웹 역할(ASP.NET)의 경우 웹 애플리케이션의 `Application_Start` 메서드에 코드를 추가해야 합니다.
   ```csharp
   using Microsoft.WindowsAzure.ServiceRuntime;
   using System;

   namespace MyWebRoleApp
   {
       public class MyMvcApplication : System.Web.HttpApplication
       {
          protected void Application_Start()
          {
             Environment.SetEnvironmentVariable("SNAPSHOTSTORE", RoleEnvironment.GetLocalResource("SnapshotStore").RootPath);
             // TODO: The rest of your application startup code
          }
       }
   }
   ```

3. 사용자 역할의 ApplicationInsights.config 파일을 업데이트하여 `SnapshotCollector`에서 사용되는 임시 폴더 위치를 재정의합니다.
   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Use the SnapshotStore local resource for snapshots -->
      <TempFolder>%SNAPSHOTSTORE%</TempFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

## <a name="overriding-the-shadow-copy-folder"></a>섀도 복사본 폴더 재정의

Snapshot Collector가 시작되면 Snapshot Uploader 프로세스를 실행하기에 적합한 디스크에서 폴더를 찾으려고 시도합니다. 선택된 폴더는 섀도 복사본 폴더라고 합니다.

Snapshot Collector는 잘 알려진 위치 몇 곳에서 Snapshot Uploader 바이너리를 복사할 권한이 있는지 확인합니다. 다음 환경 변수가 사용됩니다.
- Fabric_Folder_App_Temp
- LOCALAPPDATA
- APPDATA
- TEMP

적당한 폴더를 찾을 수 없으면 Snapshot Collector는 _"Could not find a suitable shadow copy folder."_(적합한 섀도 복사본 폴더를 찾을 수 없습니다.)라는 오류를 보고합니다.

복사에 실패하면 Snapshot Collector는 `ShadowCopyFailed` 오류를 보고합니다.

업로더를 시작할 수 없으면 Snapshot Collector는 `UploaderCannotStartFromShadowCopy` 오류를 보고합니다. 메시지 본문에 `System.UnauthorizedAccessException`이 포함되는 경우가 많습니다. 일반적으로 이 오류는 권한이 축소된 계정으로 애플리케이션이 실행되기 때문에 발생합니다. 계정에 섀도 복사본 폴더에 쓸 수 있는 권한은 있지만 코드를 실행할 수 있는 권한이 없습니다.

이러한 오류는 일반적으로 시작하는 동안 발생하기 때문에 _"Uploader failed to start."_(업로드를 시작하지 못했습니다.)라는 `ExceptionDuringConnect` 오류 다음에 발생합니다.

이러한 오류를 해결하려면 `ShadowCopyFolder` 구성을 통해 섀도 복사본 폴더를 수동으로 지정하면 됩니다. ApplicationInsights.config를 사용하는 예:

   ```xml
   <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- Override the default shadow copy folder. -->
      <ShadowCopyFolder>D:\SnapshotUploader</ShadowCopyFolder>
      <!-- Other SnapshotCollector configuration options -->
    </Add>
   </TelemetryProcessors>
   ```

또는 .NET Core 애플리케이션과 appsettings.json을 사용하는 경우:

   ```json
   {
     "ApplicationInsights": {
       "InstrumentationKey": "<your instrumentation key>"
     },
     "SnapshotCollectorConfiguration": {
       "ShadowCopyFolder": "D:\\SnapshotUploader"
     }
   }
   ```

## <a name="use-application-insights-search-to-find-exceptions-with-snapshots"></a>Application Insights 검색을 사용하여 스냅숏 예외 찾기

스냅숏이 생성될 때 throw되는 예외에는 스냅숏 ID로 태그가 지정됩니다. 예외 원격 분석이 Application Insights에 보고될 때 이 스냅숏 ID가 사용자 지정 속성으로 포함됩니다. Application Insights에서 **검색**을 사용하여 `ai.snapshot.id` 사용자 지정 속성으로 모든 원격 분석을 찾을 수 있습니다.

1. Azure Portal에서 Application Insights 리소스로 이동합니다.
2. **검색**을 클릭합니다.
3. 검색 텍스트 상자에 `ai.snapshot.id`를 입력하고 Enter 키를 누릅니다.

![포털에서 스냅숏 ID로 원격 분석 검색](./media/snapshot-debugger/search-snapshot-portal.png)

이 검색에서 반환되는 결과가 없으면 선택한 시간 범위에서 애플리케이션에 대해 Application Insights에 보고된 스냅숏이 없는 것입니다.

업로더 로그에서 특정 스냅숏 ID를 검색하려면 검색 상자에 해당 ID를 입력합니다. 업로드된 것을 알고 있는 스냅숏에 대한 원격 분석을 찾을 수 없는 경우 다음 단계를 따릅니다.

1. 계측 키를 확인하여 올바른 Application Insights 리소스가 표시되어 있는지 다시 확인합니다.

2. 업로더 로그에서 타임스탬프를 사용하여 해당 시간 범위를 포함하도록 검색의 시간 범위 필터를 조정합니다.

해당 스냅숏 ID가 포함된 예외가 여전히 보이지 않는 경우 Application Insights에 예외 원격 분석이 보고되지 않은 것입니다. 스냅숏을 만든 후 예외 원격 분석을 보고하기 전에 애플리케이션의 작동이 중단된 경우에 이 상황이 발생할 수 있습니다. 이 경우 `Diagnose and solve problems`에서 App Service 로그를 검사하여 예기치 않은 다시 시작 또는 처리되지 않은 예외가 있는지 확인합니다.

## <a name="edit-network-proxy-or-firewall-rules"></a>네트워크 프록시 또는 방화벽 규칙 편집

애플리케이션에서 프록시 또는 방화벽을 통해 인터넷에 연결하는 경우 애플리케이션이 스냅숏 디버거 서비스와 통신할 수 있도록 규칙을 편집해야 할 수 있습니다. [스냅숏 디버거에서 사용하는 IP 주소 및 포트 목록](../../azure-monitor/app/ip-addresses.md#snapshot-debugger)이 있습니다.
