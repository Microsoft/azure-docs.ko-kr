---
title: Azure 애플리케이션 Insights 에이전트-시작 하기 | Microsoft Docs
description: Application Insights 에이전트에 대 한 빠른 시작 가이드입니다. 웹 사이트를 다시 배포 하지 않고 웹 사이트 성능을 모니터링 합니다. 온-프레미스, Vm 또는 Azure에서 호스트 되는 ASP.NET 웹 앱에서 작동 합니다.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 690304ecef80d988f9a554cd10ce4689f5c72133
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070142"
---
# <a name="get-started-with-azure-monitor-application-insights-agent-for-on-premises-servers"></a>온-프레미스 서버에 대 한 Azure Monitor Application Insights 에이전트 시작

이 문서에는 대부분의 환경에서 작동 하는 데 필요한 빠른 시작 명령이 포함 되어 있습니다.
지침은 업데이트를 배포 하는 PowerShell 갤러리에 따라 달라 집니다.
이러한 명령은 PowerShell `-Proxy` 매개 변수를 지원 합니다.

이러한 명령에 대 한 설명, 사용자 지정 지침 및 문제 해결에 대 한 정보는 [자세한 지침](status-monitor-v2-detailed-instructions.md)을 참조 하세요.

Azure 구독이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="download-and-install-via-powershell-gallery"></a>PowerShell 갤러리를 통해 다운로드 및 설치

### <a name="install-prerequisites"></a>필수 구성 요소 설치
관리자 권한으로 PowerShell을 실행 합니다.
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
PowerShell을 닫습니다.

### <a name="install-application-insights-agent"></a>Application Insights 에이전트 설치
관리자 권한으로 PowerShell을 실행 합니다.
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>모니터링 사용
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download-and-install-manually-offline-option"></a>수동으로 다운로드 및 설치 (오프 라인 옵션)
### <a name="download-the-module"></a>모듈 다운로드
[PowerShell 갤러리](https://www.powershellgallery.com/packages/Az.ApplicationMonitor)에서 모듈의 최신 버전을 수동으로 다운로드 합니다.

### <a name="unzip-and-install-application-insights-agent"></a>Application Insights 에이전트 압축 풀기 및 설치
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.3.0-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>모니터링 사용
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```



## <a name="next-steps"></a>다음 단계

 원격 분석 보기:

- [메트릭을 탐색](../platform/metrics-charts.md) 하 여 성능 및 사용량을 모니터링 합니다.
- [이벤트와 로그를 검색](./diagnostic-search.md) 하 여 문제를 진단 합니다.
- 고급 쿼리를 위해 [분석을 사용](../log-query/log-query-overview.md) 합니다.
- [대시보드를 만듭니다](./overview-dashboard.md).

 원격 분석 더 추가:

- [웹 테스트를 만들어](monitor-web-app-availability.md) 사이트가 라이브 상태로 유지되고 있는지 확인합니다.
- 웹 [클라이언트 원격 분석을 추가](./javascript.md) 하 여 웹 페이지 코드에서 예외를 확인 하 고 추적 호출을 사용 하도록 설정 합니다.
- 추적 및 로그 호출을 삽입할 수 있도록 [APPLICATION INSIGHTS SDK를 코드에 추가](./asp-net.md) 합니다.

Application Insights 에이전트를 사용 하 여 더 많은 작업 수행:

- [자세한 지침은](status-monitor-v2-detailed-instructions.md) 여기에서 찾을 수 있는 명령에 대 한 설명을 참조 하세요.
- 가이드를 사용 하 여 Application Insights 에이전트 [문제를 해결할](status-monitor-v2-troubleshoot.md) 수 있습니다.

