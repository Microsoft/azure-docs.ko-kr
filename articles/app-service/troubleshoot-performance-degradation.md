---
title: 성능 저하 문제 해결 - Azure App Service | Microsoft Docs
description: 이 문서에서는 Azure App Service의 느린 앱 성능 문제를 해결하는 데 도움을 줍니다.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: top-support-issue
keywords: 웹앱 성능, 느린 앱, 앱 느림
ms.assetid: b8783c10-3a4a-4dd6-af8c-856baafbdde5
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2d17991854f13f889c4e8c3a8c6f18e933655546
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128452"
---
# <a name="troubleshoot-slow-app-performance-issues-in-azure-app-service"></a>Azure App Service에서 느린 앱 성능 문제 해결
이 문서에서는 [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714)의 느린 앱 성능 문제를 해결하는 데 도움을 줍니다.

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 스택 오버플로 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수도 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 이동한 다음 **지원 받기**를 클릭합니다.

## <a name="symptom"></a>증상
앱을 탐색할 때, 페이지 로딩이 느려지거나 멈춤

## <a name="cause"></a>원인
이 문제는 애플리케이션 수준 문제가 원인이 되는 경우가 많습니다. 예를 들어:

* 긴 네트워크 요청 시간
* 비효율적인 애플리케이션 코드 또는 데이터베이스 쿼리
* 높은 메모리/CPU를 사용하는 애플리케이션
* 예외로 인해 충돌하는 애플리케이션

## <a name="troubleshooting-steps"></a>문제 해결 단계
문제 해결은 해결하는 순서대로 세 가지로 구분할 수 있습니다.

1. [애플리케이션 작동을 관찰 및 감시](#observe)
2. [데이터 수집](#collect)
3. [문제 완화](#mitigate)

[App Service](overview.md)는 각 단계별로 다양한 옵션을 제공합니다.

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1. 애플리케이션 작동을 관찰 및 감시 
#### <a name="track-service-health"></a>서비스 상태를 추적합니다.
Microsoft Azure는 서비스가 중단되거나 성능이 저하될 때마다 경고를 표시합니다. [Azure Portal](https://portal.azure.com/)에서 서비스의 상태를 추적할 수 있습니다. 자세한 내용은 [서비스 상태 추적](../monitoring-and-diagnostics/insights-service-health.md)을 참조하세요.

#### <a name="monitor-your-app"></a>앱 모니터링
이 옵션은 애플리케이션의 문제를 해결할 수 있게 도와줍니다. 앱의 블레이드에서 **요청 및 오류** 타일을 클릭합니다. **메트릭** 블레이드는 추가 가능한 모든 메트릭을 보여 줍니다.

모니터링할 수 있는 앱의 일부 메트릭은 다음과 같습니다.

* 평균 메모리 작업 집합
* 평균 응답 시간
* CPU 시간
* 메모리 작업 집합
* 요청

![앱 성능 모니터링](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

자세한 내용은 다음을 참조하세요.

* [Azure App Service에서 앱 모니터링](web-sites-monitor.md)
* [경고 알림 받기](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>웹 엔드포인트 상태 모니터링
**표준** 가격 책정 계층에서 앱을 실행할 경우, App Service를 통해 3곳의 지리적 위치에서 엔드포인트 2개를 모니터링할 수 있습니다.

엔드포인트 모니터링은 지리적으로 분산된 위치에서 웹 URL의 응답 시간 및 가동 시간을 테스트하는 웹 테스트를 구성합니다. 테스트는 웹 URL의 HTTP 가져오기 작업을 수행하여 각 위치의 응답 시간 및 가동 시간을 확인합니다. 구성된 각 위치에서는 5분마다 테스트를 실행합니다.

가동 시간은 HTTP 응답 코드를 사용하여 모니터링되며 응답 시간은 밀리초로 측정됩니다. HTTP 응답 코드가 400 이상이거나 응답에 30초 넘게 걸리는 경우 모니터링 테스트가 실패합니다. 지정된 모든 위치에서 모니터링 테스트가 성공하는 경우 엔드포인트는 사용 가능한 것으로 간주됩니다.

설정하려면 [Azure App Service에서 앱 모니터링](web-sites-monitor.md)을 참조하세요.

또한, 엔드포인트 모니터링의 비디오에 대해서는 [Azure Websites 가동 및 끝 점 모니터링 - 스테판 스차코우(Stefan Schackow)](https://channel9.msdn.com/Shows/Azure-Friday/Keeping-Azure-Web-Sites-up-plus-Endpoint-Monitoring-with-Stefan-Schackow) 를 참조하세요.

#### <a name="application-performance-monitoring-using-extensions"></a>확장을 사용하여 애플리케이션 성능 모니터링
*사이트 확장*을 사용하여 애플리케이션 성능을 모니터링할 수도 있습니다.

각 App Service 앱은 사이트 확장처럼 사용할 수 있는 배포된 강력한 도구 세트인 확장 가능한 관리 엔드포인트를 제공합니다. 확장은 다음을 포함합니다. 

- [Azure DevOps](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx)와 같은 소스 코드 편집기. 
- 앱에 연결된 MySQL 데이터베이스와 같은 연결된 리소스에 대한 관리 도구

[Azure Application Insights](https://azure.microsoft.com/services/application-insights/)도 사용할 수 있는 성능 모니터링 사이트 확장입니다. Application Insights를 사용하려면 SDK를 통해 코드를 다시 빌드합니다. 추가 데이터에 대한 액세스를 제공하는 확장을 설치할 수도 있습니다. SDK를 통해 앱의 사용과 성능을 보다 자세하게 모니터링하기 위한 코드를 작성할 수 있습니다. 자세한 내용은 [웹 애플리케이션의 성능 모니터링](../azure-monitor/app/web-monitor-performance.md)을 참조하세요.

<a name="collect" />

### <a name="2-collect-data"></a>2. 데이터 수집
App Service는 웹 서버와 웹 애플리케이션 모두의 정보에 로깅할 수 있도록 진단 기능을 제공합니다. 이 정보는 웹 서버 진단 및 애플리케이션 진단으로 구분됩니다.

#### <a name="enable-web-server-diagnostics"></a>웹 서버 진단 사용
다음과 같은 종류의 로그를 사용하거나 사용하지 않도록 설정할 수 있습니다.

* **자세한 오류 로깅** - 오류를 나타내는 HTTP 상태 코드(상태 코드 400 이상)의 자세한 오류 정보입니다. 여기에는 서버에서 오류 코드를 반환한 이유를 확인하는 데 도움이 되는 정보가 포함될 수 있습니다.
* **실패한 요청 추적** - 요청을 처리하는 데 사용된 IIS 구성 요소 추적 및 각 구성 요소에 소요된 시간을 포함하여 실패한 요청에 대해 자세한 정보입니다. 이 기능은 앱 성능을 개선하거나 특정 HTTP 오류 원인을 격리하려는 경우에 유용할 수 있습니다.
* **웹 서버 로깅** - W3C 확장 로그 파일 형식을 사용하는 HTTP 트랜잭션에 대한 정보입니다. 이 기능은 처리된 요청의 수 또는 특정 IP 주소에서 넘어온 많은 요청처럼 전반적인 앱 메트릭을 결정할 때 유용합니다.

#### <a name="enable-application-diagnostics"></a>애플리케이션 진단 사용
App Service에서 애플리케이션 성능 데이터를 수집하고, Visual Studio에서 애플리케이션 라이브를 프로파일링하거나 자세한 내용 및 추적을 기록하도록 애플리케이션 코드를 수정하는 다양한 옵션이 있습니다. 애플리케이션에 대해 보유한 액세스 양 및 모니터링 도구에서 관찰한 내용에 따라 옵션을 선택할 수 있습니다.

##### <a name="use-application-insights-profiler"></a>Application Insights Profiler 사용
Application Insights Profiler를 활성화하여 자세한 성능 추적 캡처를 시작할 수 있습니다. 과거에 발생한 문제를 조사해야 하는 경우 최대 5일 이전에 캡처된 추적에 액세스할 수 있습니다. Azure Portal에서 앱의 Application Insights 리소스에 액세스할 수 있는 한 이 옵션을 선택할 수 있습니다.

Application Insights Profiler는 각 웹 호출에 대한 응답 시간 및 느린 응답을 일으키는 코드 줄을 나타내는 추적에 대한 통계를 제공합니다. 때로는 특정 코드가 성능 기준에 맞게 작성되지 않아 App Service 앱이 느리게 작동합니다. 예를 들어 병렬로 실행될 수 있는 순차 코드와 원하지 않는 데이터베이스 잠금 경합이 있습니다. 코드에서 이러한 병목 현상을 제거하면 앱 성능이 향상되지만, 정교한 추적과 로그를 설정하지 않으면 감지하기가 어렵습니다. Application Insights Profiler에 의해 수집된 추적은 애플리케이션의 속도를 낮추는 코드 줄을 식별하는 데 도움이 되고 App Service 앱에 대한 이 과제를 해결합니다.

 자세한 내용은 [Application Insights를 사용하여 Azure App Service에서 라이브 앱 프로파일링](../azure-monitor/app/profiler.md)을 참조하세요.

##### <a name="use-remote-profiling"></a>원격 프로파일링 사용하기
Azure App Service에서 웹앱, API 앱, 모바일 백 엔드 및 WebJob을 원격으로 프로파일할 수 있습니다. 앱 리소스에 대한 액세스가 있고 문제를 재현하는 방법을 알거나 성능 문제가 발생하는 정확한 시간 간격을 알고 있는 경우 이 옵션을 선택합니다.

원격 프로파일링은 프로세스의 CPU 사용량이 높고 프로세스가 예상보다 느리게 실행되거나, HTTP 요청 대기시간이 평상시보다 높은 경우 유용하며, 원격으로 프로세스를 프로파일할 수 있고 프로세스 활동과 코드 hot path를 분석하기 위해 CPU 샘플링 호출 스택을 가져올 수 있습니다.

자세한 내용은 [Azure App Service에서 원격 프로파일링 지원](https://azure.microsoft.com/blog/remote-profiling-support-in-azure-app-service)을 참조하세요.

##### <a name="set-up-diagnostic-traces-manually"></a>진단 추적을 수동으로 설정
웹 애플리케이션 소스 코드에 대한 액세스가 있는 경우 애플리케이션 진단은 웹 애플리케이션에서 생성되는 정보를 캡처할 수 있게 해줍니다. ASP.NET 애플리케이션은 정보를 애플리케이션 진단 로그에 기록하기 위해 `System.Diagnostics.Trace` 클래스를 사용할 수 있습니다. 그러나 코드를 변경하고 애플리케이션을 다시 배포해야 합니다. 테스트 환경에서 앱을 실행 중인 경우 이 메서드를 권장합니다.

로깅에 대한 애플리케이션을 구성하는 방법에 대한 자세한 설명은 [Azure App Service에서 앱에 대한 진단 로깅 설정](troubleshoot-diagnostic-logs.md)을 참조하세요.

#### <a name="use-the-diagnostics-tool"></a>진단 도구 사용
App Service는 앱 문제를 해결하는 데 도움이 되는 지능적인 대화형 환경으로, 구성이 필요하지 않습니다. 앱에서 문제가 발생하면 진단 도구는 무엇이 문제인지를 표시하여 문제를 더 쉽고 빠르게 확인하고 해결하기 위한 올바른 정보로 안내합니다.

App Service 진단에 액세스하려면 [Azure Portal](https://portal.azure.com)의 App Service 앱 또는 App Service 환경으로 이동합니다. 왼쪽 탐색 메뉴에서 **문제 진단 및 해결**을 클릭합니다.

#### <a name="use-the-kudu-debug-console"></a>Kudu 디버그 콘솔 사용
App Service는 사용자 환경에 대한 정보를 얻을 수 있는 JSON 엔드포인트 및 파일 디버그, 탐색, 업로드에 사용할 수 있는 디버그 콘솔과 함께 제공됩니다. 이 콘솔은 *Kudu 콘솔* 또는 사용자 앱에서는 *SCM 대시보드*라고 합니다.

**https://&lt;Your app name>.scm.azurewebsites.net/** 링크로 이동해서 대시보드에 액세스할 수 있습니다.

Kudu가 제공하는 것은 다음과 같습니다.

* 애플리케이션에 대한 환경 설정
* 로그 스트림
* 진단 덤프
* Powershell cmdlet 및 기본 DOS 명령을 실행할 수 있는 콘솔을 디버깅합니다.

Kudu의 또 다른 유용한 기능은 애플리케이션에 첫 번째 예외가 발생할 경우, 메모리 덤프를 만들기 위해 Kudu와 SysInternal 도구 Procdump를 사용할 수 있습니다. 메모리 덤프는 프로세스의 스냅숏이며 앱의 더욱 복잡한 문제를 해결하는 데 많은 도움을 줍니다.

Kudu에서 사용 가능한 기능에 대한 자세한 내용은 [사용자가 꼭 알아야 할 Azure DevOps 온라인 도구](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)를 참조하세요.

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3. 문제 완화
#### <a name="scale-the-app"></a>앱 크기 조정
Azure App Service에서 성능과 처리량의 증가를 위해 사용자가 실행하는 애플리케이션의 크기를 조정할 수 있습니다. 앱을 확장하려면 두 가지 작업이 필요합니다. App Service 계획을 상위 가격 책정 계층으로 변경해야 하고, 상위 가격 책정 계층으로 전환한 후에 특정 설정을 구성해야 합니다.

크기 조정에 대한 자세한 내용은 [Azure App Service에서 앱 크기 조정](web-sites-scale.md)을 참조하세요.

또한 둘 이상의 인스턴스에서 애플리케이션을 실행할 수 있습니다. 크기 확장은 더 많은 처리 기능을 제공할 뿐만 아니라, 어느 정도의 내결함성을 제공합니다. 하나의 인스턴스에서 프로세스가 다운되면, 또 다른 인스턴스에서 계속 요청을 처리합니다.

수동 또는 자동으로 크기를 조정할 수 있습니다.

#### <a name="use-autoheal"></a>AutoHeal를 사용
AutoHeal은 사용자가 선택한 설정(예: 구성 변경, 요청, 메모리 기반 제한 또는 요청을 실행하는데 필요한 시간)에 따라 앱에 대한 작업자 프로세스를 재활용 합니다. 대부분의 경우에는 프로세스를 재활용하는 방법이 문제를 해결하는 가장 빠른 방법입니다. Azure Portal 내에서 직접 앱을 재시작할 수도 있지만, AutoHeal은 자동으로 재시작을 수행하게 해줍니다. 앱의 루트 web.config에 일부 트리거를 추가하기만 하면 됩니다. 이러한 설정은 없으면 응용 프로그램이.NET 앱을 동일한 방식으로 작동 됩니다.

자세한 내용은 [Auto-Healing Azure Websites](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)를 참조하세요.

#### <a name="restart-the-app"></a>앱 다시 시작
재시작은 일회성 문제를 해결하는 가장 간단한 방법입니다. [Azure Portal](https://portal.azure.com/)또는 앱의 블레이드에서 앱을 중지하거나 다시 시작하는 옵션을 사용할 수 있습니다.

 ![앱을 다시 시작하여 성능 문제 해결](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

또한 Azure Powershell을 사용하여 앱을 관리할 수 있습니다. 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](../powershell-azure-resource-manager.md)을 참조하세요.
