---
title: Application Insights 및 Log Analytics에서 사용되는 IP 주소 | Microsoft Docs
description: Application Insights에 필요한 서버 방화벽 예외
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 06/18/2020
ms.openlocfilehash: 79b9442cb31ab326d3b556da9b2e3ef066f9bd41
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88949867"
---
# <a name="ip-addresses-used-by-application-insights-and-log-analytics"></a>Application Insights 및 Log Analytics에서 사용되는 IP 주소
[Azure Application Insights](./app-insights-overview.md) 서비스는 많은 IP 주소를 사용합니다. 모니터링하는 앱이 방화벽 뒤에서 호스팅되는 경우 이러한 주소를 알아야 할 수도 있습니다.

> [!NOTE]
> 이러한 주소는 고정이지만 경우에 따라 변경해야 할 수 있습니다. 모든 Application Insights 트래픽은 인바운드 방화벽 규칙을 필요로 하는 가용성 모니터링 및 웹후크를 제외하고 아웃바운드 트래픽을 나타냅니다.

> [!TIP]
> Azure 네트워크 보안 그룹을 사용하는 경우 Azure [네트워크 서비스 태그](../../virtual-network/service-tags-overview.md)를 사용하여 액세스를 관리할 수 있습니다. 하이브리드/온-프레미스 리소스에 대한 액세스를 관리하는 경우 매주 업데이트되는 [JSON 파일](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)과 동일한 IP 주소 목록을 다운로드할 수 있습니다. 이 문서의 모든 예외를 처리하려면 'ActionGroup', 'ApplicationInsightsAvailability', 'AzureMonitor' 서비스 태그를 사용해야 합니다.

또는 즐겨 찾는 RSS/ATOM 판독기에 https://github.com/MicrosoftDocs/azure-docs/commits/master/articles/azure-monitor/app/ip-addresses.md.atom 을 추가하여 최신 변경의 알림을 받을 수 있게 이 페이지를 RSS 피드로 구독할 수 있습니다.


## <a name="outgoing-ports"></a>발신 포트
Application Insights SDK 및/또는 상태 모니터가 데이터를 포털에 보낼 수 있도록 서버 방화벽에서 일부 나가는 포트를 열어야 합니다.

| 목적 | URL | IP | 포트 |
| --- | --- | --- | --- |
| 원격 분석 |dc.applicationinsights.azure.com<br/>dc.applicationinsights.microsoft.com<br/>dc.services.visualstudio.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221<br/>52.167.221.184<br/>52.169.64.244<br/>40.85.218.175<br/>104.211.92.54<br/>52.175.198.74<br/>51.140.6.23<br/>40.71.12.231<br/>13.69.65.22<br/>13.78.108.165<br/>13.70.72.233<br/>20.44.8.7<br/>13.86.218.248<br/>40.79.138.41<br/>52.231.18.241<br/>13.75.38.7<br/>102.133.155.50<br/>52.162.110.67<br/>191.233.204.248<br/>13.69.66.140<br/>13.77.52.29<br/>51.107.59.180<br/>40.71.12.235<br/>20.44.8.10<br/>40.71.13.169<br/>13.66.141.156<br/>40.71.13.170<br/>13.69.65.23<br/>20.44.17.0<br/>20.36.114.207 <br/>51.116.155.246 <br/>51.107.155.178 <br/>51.140.212.64 <br/>13.86.218.255  | 443 |
| 라이브 메트릭 스트림 | live.applicationinsights.azure.com<br/>rt.applicationinsights.microsoft.com<br/>rt.services.visualstudio.com|23.96.28.38<br/>13.92.40.198<br/>40.112.49.101<br/>40.117.80.207<br/>157.55.177.6<br/>104.44.140.84<br/>104.215.81.124<br/>23.100.122.113| 443 |

## <a name="status-monitor"></a>상태 모니터
상태 모니터 구성 - 변경하는 경우에만 필요합니다.

| 목적 | URL | IP | 포트 |
| --- | --- | --- | --- |
| 구성 |`management.core.windows.net` | |`443` |
| 구성 |`management.azure.com` | |`443` |
| 구성 |`login.windows.net` | |`443` |
| 구성 |`login.microsoftonline.com` | |`443` |
| 구성 |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| 구성 |`auth.gfx.ms` | |`443` |
| 구성 |`login.live.com` | |`443` |
| 설치 | `globalcdn.nuget.org`, `packages.nuget.org` ,`api.nuget.org/v3/index.json` `nuget.org`, `api.nuget.org`, `dc.services.vsallin.net` | |`443` |

## <a name="availability-tests"></a>가용성 테스트
[가용성 웹 테스트](./monitor-web-app-availability.md) 가 실행되는 주소 목록입니다. 앱에서 웹 테스트를 실행하려고 하지만 웹 서버가 특정 클라이언트 서비스를 제공하도록 제한된 경우 가용성 테스트 서버에서 들어오는 트래픽을 허용해야 합니다.

### <a name="service-tag"></a>서비스 태그

Azure 네트워크 보안 그룹을 사용하는 경우 **서비스 태그**를 **원본**으로, **ApplicationInsightsAvailability**를 **원본 서비스 태그**로 선택하여 **인바운드 포트 규칙**을 추가하는 간단한 방식으로 Application Insights 가용성 테스트의 트래픽을 허용하세요.

>[!div class="mx-imgBorder"]
>![설정 아래에서 인바운드 보안 규칙을 선택한 다음, 탭 상단에서 추가 선택](./media/ip-addresses/add-inbound-security-rule.png)

>[!div class="mx-imgBorder"]
>![인바운드 보안 규칙 추가 탭](./media/ip-addresses/add-inbound-security-rule2.png)

이 주소에서 들어오는 트래픽에 대한 80(http) 및 443(https) 포트를 엽니다(IP 주소가 위치별로 그룹화됨).

### <a name="addresses-grouped-by-location"></a>위치별로 그룹화된 주소

> [!NOTE]
> 이러한 주소는 CIDR(Classless Inter-Domain Routing) 표기법을 사용하여 나열됩니다. 즉, `51.144.56.112/28` 같은 항목은 `51.144.56.112`에서 시작하여 `51.144.56.127`에서 끝나는 16개의 IP와 동일합니다.

```
Australia East
20.40.124.176/28
20.40.124.240/28
20.40.125.80/28

Brazil South
191.233.26.176/28
191.233.26.128/28
191.233.26.64/28

France Central (Formerly France South)
20.40.129.96/28
20.40.129.112/28
20.40.129.128/28
20.40.129.144/28

France Central
20.40.129.32/28
20.40.129.48/28
20.40.129.64/28
20.40.129.80/28

East Asia
52.229.216.48/28
52.229.216.64/28
52.229.216.80/28

North Europe
52.158.28.64/28
52.158.28.80/28
52.158.28.96/28
52.158.28.112/28

Japan East
52.140.232.160/28
52.140.232.176/28
52.140.232.192/28

West Europe
51.144.56.96/28
51.144.56.112/28
51.144.56.128/28
51.144.56.144/28
51.144.56.160/28
51.144.56.176/28

UK South
51.105.9.128/28
51.105.9.144/28
51.105.9.160/28

UK West
20.40.104.96/28
20.40.104.112/28
20.40.104.128/28
20.40.104.144/28

Southeast Asia
52.139.250.96/28
52.139.250.112/28
52.139.250.128/28
52.139.250.144/28

West US
40.91.82.48/28
40.91.82.64/28
40.91.82.80/28
40.91.82.96/28
40.91.82.112/28
40.91.82.128/28

Central US
13.86.97.224/28
13.86.97.240/28
13.86.98.48/28
13.86.98.0/28
13.86.98.16/28
13.86.98.64/28

North Central US
23.100.224.16/28
23.100.224.32/28
23.100.224.48/28
23.100.224.64/28
23.100.224.80/28
23.100.224.96/28
23.100.224.112/28
23.100.225.0/28

South Central US
20.45.5.160/28
20.45.5.176/28
20.45.5.192/28
20.45.5.208/28
20.45.5.224/28
20.45.5.240/28

East US
20.42.35.32/28
20.42.35.64/28
20.42.35.80/28
20.42.35.96/28
20.42.35.112/28
20.42.35.128/28

Azure US Government (Not needed if you are an Azure Public cloud customer)

20.140.48.160/27
20.140.56.160/27
20.140.64.160/27
20.140.72.160/27
52.127.49.96/27
```  

## <a name="application-insights--log-analytics-apis"></a>Application Insights 및 Log Analytics API

| 목적 | URI |  IP | 포트 |
| --- | --- | --- | --- |
| API |`api.applicationinsights.io`<br/>`api1.applicationinsights.io`<br/>`api2.applicationinsights.io`<br/>`api3.applicationinsights.io`<br/>`api4.applicationinsights.io`<br/>`api5.applicationinsights.io`<br/>`dev.applicationinsights.io`<br/>`dev.applicationinsights.microsoft.com`<br/>`dev.aisvc.visualstudio.com`<br/>`www.applicationinsights.io`<br/>`www.applicationinsights.microsoft.com`<br/>`www.aisvc.visualstudio.com`<br/>`api.loganalytics.io`<br/>`*.api.loganalytics.io`<br/>`dev.loganalytics.io`<br>`docs.loganalytics.io`<br/>`www.loganalytics.io` |20.37.52.188 <br/> 20.37.53.231 <br/> 20.36.47.130 <br/> 20.40.124.0 <br/> 20.43.99.158 <br/> 20.43.98.234 <br/> 13.70.127.61 <br/> 40.81.58.225 <br/> 20.40.160.120 <br/> 23.101.225.155 <br/> 52.139.8.32 <br/> 13.88.230.43 <br/> 52.230.224.237 <br/> 52.242.230.209 <br/> 52.173.249.138 <br/> 52.229.218.221 <br/> 52.229.225.6 <br/> 23.100.94.221 <br/> 52.188.179.229 <br/> 52.226.151.250 <br/> 52.150.36.187 <br/> 40.121.135.131 <br/> 20.44.73.196 <br/> 20.41.49.208 <br/> 40.70.23.205 <br/> 20.40.137.91 <br/> 20.40.140.212 <br/> 40.89.189.61 <br/> 52.155.118.97 <br/> 52.156.40.142 <br/> 23.102.66.132 <br/> 52.231.111.52 <br/> 52.231.108.46 <br/> 52.231.64.72 <br/> 52.162.87.50 <br/> 23.100.228.32 <br/> 40.127.144.141 <br/> 52.155.162.238 <br/> 137.116.226.81 <br/> 52.185.215.171 <br/> 40.119.4.128 <br/> 52.171.56.178 <br/> 20.43.152.45 <br/> 20.44.192.217 <br/> 13.67.77.233 <br/> 51.104.255.249 <br/> 51.104.252.13 <br/> 51.143.165.22 <br/> 13.78.151.158 <br/> 51.105.248.23 <br/> 40.74.36.208 <br/> 40.74.59.40 <br/> 13.93.233.49 <br/> 52.247.202.90 |80,443 |
| Azure 파이프라인 주석 확장 |aigs1.aisvc.visualstudio.com |동적|443 | 

## <a name="application-insights-analytics"></a>Application Insights 분석

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| 분석 포털 | analytics.applicationinsights.io | 동적 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 동적 | 80,443 |
| 미디어 CDN | applicationanalyticsmedia.azureedge.net | 동적 | 80,443 |

참고: *.applicationinsights.io 도메인은 Application Insights 팀에서 소유하고 있습니다.

## <a name="log-analytics-portal"></a>Log Analytics 포털

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| 포털 | portal.loganalytics.io | 동적 | 80,443 |
| CDN | applicationanalytics.azureedge.net | 동적 | 80,443 |

참고: *.loganalytics.io 도메인은 Log Analytics 팀에서 소유하고 있습니다.

## <a name="application-insights-azure-portal-extension"></a>Application Insights Azure Portal 확장

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| Application Insights 확장 | stamp2.app.insightsportal.visualstudio.com | 동적 | 80,443 |
| Application Insights 확장 CDN | insightsportal-prod2-cdn.aisvc.visualstudio.com<br/>insightsportal-prod2-asiae-cdn.aisvc.visualstudio.com<br/>insightsportal-cdn-aimon.applicationinsights.io | 동적 | 80,443 |

## <a name="application-insights-sdks"></a>Application Insights SDK

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| Application Insights JS SDK CDN | az416426.vo.msecnd.net | 동적 | 80,443 |

## <a name="action-group-webhooks"></a>작업 그룹 웹후크

| 목적 | IP | 포트
| --- | --- | --- |
| 경고 | 13.72.19.232 <br/>13.106.57.181<br/>13.106.54.3<br/>13.106.54.19<br/>13.106.38.142<br/>13.106.38.148<br/>13.106.57.196<br/>13.106.57.197<br/>52.244.68.117<br/>52.244.65.137<br/>52.183.31.0<br/>52.184.145.166<br/>51.4.138.199<br/>51.5.148.86<br/>51.5.149.19 | 443 |

## <a name="profiler"></a>프로파일러

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| 에이전트 | agent.azureserviceprofiler.net<br/>*.agent.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| 포털 | gateway.azureserviceprofiler.net | 동적 | 443
| 스토리지 | *.core.windows.net | 동적 | 443

## <a name="snapshot-debugger"></a>스냅샷 디버거

> [!NOTE]
> 프로파일러 및 스냅샷 디버거는 동일한 IP 주소 집합을 공유합니다.

| 목적 | URI | IP | 포트 |
| --- | --- | --- | --- |
| 에이전트 | ppe.azureserviceprofiler.net<br/>*.ppe.azureserviceprofiler.net | 20.190.60.38<br/>20.190.60.32<br/>52.173.196.230<br/>52.173.196.209<br/>23.102.44.211<br/>23.102.45.216<br/>13.69.51.218<br/>13.69.51.175<br/>138.91.32.98<br/>138.91.37.93<br/>40.121.61.208<br/>40.121.57.2<br/>51.140.60.235<br/>51.140.180.52<br/>52.138.31.112<br/>52.138.31.127<br/>104.211.90.234<br/>104.211.91.254<br/>13.70.124.27<br/>13.75.195.15<br/>52.185.132.101<br/>52.185.132.170<br/>20.188.36.28<br/>40.89.153.171<br/>52.141.22.239<br/>52.141.22.149<br/>102.133.162.233<br/>102.133.161.73<br/>191.232.214.6<br/>191.232.213.239 | 443
| 포털 | ppe.gateway.azureserviceprofiler.net | 동적 | 443
| 스토리지 | *.core.windows.net | 동적 | 443

