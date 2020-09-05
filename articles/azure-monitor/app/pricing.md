---
title: Azure Application Insights의 사용량 및 비용 관리 | Microsoft Docs
description: Application Insights에서 원격 분석을 관리하고 비용을 모니터링합니다.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 5/7/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 5dd1fd1209be29774d19a155b6e585fa6ebcc036
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930488"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Application Insights의 사용량 및 비용 관리

> [!NOTE]
> 이 문서에서는 Application Insights의 비용을 이해하고 관리하는 방법을 설명합니다.  관련 문서인 [사용량 및 예상 비용 모니터링](../platform/usage-estimated-costs.md)에서는 다른 가격 책정 모델에 대해 여러 Azure 모니터링 기능에서 사용량 및 예상 비용을 보는 방법을 설명합니다.

Application Insights는 Azure와 온-프레미스에 호스트된 웹 애플리케이션의 가용성, 성능 및 사용량을 모니터링하는 데 필요한 모든 것을 제공하도록 설계되어 있습니다. Application Insights는 .NET, Java, Node.js와 같이 널리 사용되는 언어 및 프레임워크를 지원하며, Azure DevOps, Jira, PagerDuty와 같은 DevOps 프로세스 및 도구와 통합됩니다. 애플리케이션 모니터링 비용을 결정하는 요소가 무엇인지 이해하는 것이 중요합니다. 이 문서에서는 애플리케이션 모니터링 비용을 결정하는 요소가 무엇인지 알아보고 비용을 선제적으로 모니터링하고 제어하는 방법을 살펴봅니다.

Application Insights의 가격 책정 방식에 대해 궁금한 사항이 있으면 [Microsoft Q&A 질문 페이지](/answers/topics/azure-monitor.html)에 질문을 게시하세요.

## <a name="pricing-model"></a>가격 책정 모델

[Azure Application Insights][start]의 기본 가격은 수집된 데이터 볼륨을 기반으로 하며 필요에 따라 데이터를 더 오래 보존하기 위한 **종량제** 모델에 속합니다. 각 Application Insights 리소스는 별도의 서비스로 요금이 부과되고 Azure 구독에 대한 청구서에 추가됩니다. 데이터 볼륨은 애플리케이션의 Application Insights에서 받은 압축되지 않은 JSON 데이터 패키지의 크기로 측정됩니다. [라이브 메트릭 스트림](./live-stream.md) 사용에 대한 데이터 볼륨 요금은 없습니다.

[다중 단계 웹 테스트](./availability-multistep.md)는 추가 요금이 발생합니다. 다중 단계 웹 테스트는 일련의 작업을 수행하는 웹 테스트입니다. 단일 페이지의 *ping 테스트*에 대해 별도의 요금이 부과되지 않습니다. ping 테스트와 다중 단계 테스트의 원격 분석은 앱의 다른 원격 분석과 동일하게 청구됩니다.

[사용자 지정 메트릭 차원에 대한 경고를 사용하도록 설정](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)하는 Application Insights 옵션을 사용할 경우 추가로 사전 집계 메트릭이 생성될 수 있으므로 추가 비용이 발생할 수 있습니다. Application Insights의 로그 기반 및 사전 집계 메트릭과 Azure Monitor 사용자 지정 메트릭 [가격](https://azure.microsoft.com/pricing/details/monitor/)에 대해 [자세히 알아보세요](./pre-aggregated-metrics-log-metrics.md).

### <a name="workspace-based-application-insights"></a>작업 영역 기반 Application Insights

데이터를 Log Analytics 작업 영역으로 보내는 Application Insights 리소스([작업 영역 기반 Application Insights 리소스](create-workspace-resource.md))의 경우, 데이터 수집 및 보존에 대한 요금 청구는 해당 Application Insights 데이터가 위치한 작업 영역에 의해 이루어집니다. 이로 인해 고객은 종량제에 더해 용량 예약까지 포함하는 Log Analytics [가격 책정 모델](../platform/manage-cost-storage.md#pricing-model)의 모든 옵션을 활용할 수 있습니다. Log Analytics에는 [데이터 형식에 따른 보존](../platform/manage-cost-storage.md#retention-by-data-type)을 포함하여 더 많은 데이터 보존 옵션이 있습니다. 작업 영역에 있는 Application Insights 데이터 형식은 추가 요금 없이 90일 동안 보존됩니다. 웹 테스트를 사용하는 경우와 사용자 지정 메트릭 차원에 대한 경고를 사용하도록 설정하는 경우는 Application Insights를 통해 보고됩니다. [사용량 및 예상 비용](../platform/manage-cost-storage.md#understand-your-usage-and-estimate-costs), [Azure Cost Management + 청구](../platform/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill) 및 [Log Analytics 쿼리](#data-volume-for-workspace-based-application-insights-resources)를 사용하여 Log Analytics의 데이터 수집 및 보존 비용을 추적하는 방법을 알아보세요. 

## <a name="estimating-the-costs-to-manage-your-application"></a>애플리케이션 관리 비용 추정

Application Insights를 아직 사용하고 있지 않다면 [Azure Monitor 가격 계산기](https://azure.microsoft.com/pricing/calculator/?service=monitor)를 사용하여 Application Insights를 사용하는 데 드는 비용을 추정할 수 있습니다. 먼저 검색 상자에 “Azure Monitor”를 입력한 후 화면에 표시되는 Azure Monitor 타일을 클릭합니다. Azure Monitor가 표시될 때까지 페이지를 아래로 스크롤하여 유형 드롭다운에서 Application Insights를 선택합니다.  여기에서 매월 수집할 것으로 예상되는 데이터의 GB 수를 입력할 수 있습니다. 따라서 문제는 Application Insights가 애플리케이션을 모니터링하는 과정에서 얼마만큼의 데이터를 수집할 것인가가 됩니다.

이 문제에는 두 가지 방법으로 접근할 수 있습니다. 하나는 ASP.NET SDK의 기본 모니터링 및 적응 샘플링을 사용하는 것이고, 다른 하나는 본인과 비슷한 다른 고객들의 경우를 기준으로 데이터 수집이 어느 정도가 될 것인지 추정하는 것입니다.

### <a name="data-collection-when-using-sampling"></a>샘플링을 사용하는 경우의 데이터 수집

ASP.NET SDK의 [적응 샘플링](sampling.md#adaptive-sampling)을 사용하면 데이터 볼륨이 기본 Application Insights 모니터링의 지정된 최대 트래픽 속도 아래로 유지되도록 자동으로 조정됩니다. 디버깅할 때나 사용량이 낮은 경우와 같이 애플리케이션이 낮은 양의 원격 분석을 생성하는 경우, 볼륨이 두 번째 수준에 대해 구성된 이벤트보다 낮은 수준으로 유지되는 한 항목이 샘플링 프로세서에 의해 삭제되지 않습니다. 기본 임계값이 초당 이벤트 5개인 높은 볼륨의 애플리케이션의 경우, 적응 샘플링은 일일 이벤트의 개수를 432,000개로 제한합니다. 일반적인 평균 이벤트 크기인 1KB로 계산했을 때 이는 애플리케이션을 호스트하는 노드당(샘플링은 각 노드에서 로컬로 이루어지므로) 월(31일) 13.4GB의 원격 분석에 해당합니다. 

적응 샘플링을 지원하지 않는 SDK의 경우, 보존할 데이터의 백분율을 기준으로 Application Insights가 데이터를 수신하는 시점을 샘플링하는 [수집 샘플링](./sampling.md#ingestion-sampling)을 사용하거나 [ASP.NET, ASP.NET Core 및 Java 웹 사이트를 위한 고정 속도 샘플링](sampling.md#fixed-rate-sampling)을 사용하여 웹 서버와 웹 브라우저에서 전송하는 트래픽을 줄일 수 있습니다.

### <a name="learn-from-what-similar-customers-collect"></a>비슷한 고객이 수집하는 데이터의 볼륨으로부터 추정

Application Insights용 Azure Monitoring 가격 계산기에서 “애플리케이션 작업을 기반으로 데이터 볼륨 추정” 기능을 사용하도록 설정한 경우 애플리케이션에 대한 입력(클라이언트 쪽 원격 분석을 수집할 경우 월 요청 건수와 월 페이지 보기 수)을 제공하면 계산기에서 비슷한 애플리케이션에 의해 수집된 데이터 양의 중앙값과 90번째 백분위 수를 알려 줍니다. 이러한 애플리케이션에는 여러 Application Insights 구성이 적용되어 있으므로(예: 기본 [샘플링](./sampling.md)을 사용하는 경우와 샘플링을 사용하지 않는 경우 등) 샘플링을 사용하여 수집하는 데이터의 볼륨을 중앙값보다 훨씬 밑으로 제어할 수 있습니다. 그러나 이것은 다른 비슷한 고객의 경우를 이해하기 위한 출발점에 지나지 않습니다.

## <a name="understand-your-usage-and-estimate-costs"></a>사용량 및 예상 비용의 이해

Application Insights를 사용하면 최근 사용 패턴에 따른 예상 비용을 쉽게 파악할 수 있습니다. 시작하려면 Application Insights 리소스의 경우 Azure Portal에서 **사용량 및 예상 비용** 페이지로 이동합니다.

![가격 책정 선택](./media/pricing/pricing-001.png)

A. 해당 월의 데이터 볼륨을 검토합니다. 여기에는 서버 및 클라이언트 앱과 가용성 테스트에서 수신되고 보유되는([샘플링](./sampling.md) 이후) 모든 데이터가 포함됩니다.  
B. [다단계 웹 테스트](./availability-multistep.md)에 대해 별도의 요금이 부과 됩니다. (여기에는 간단한 가용성 테스트는 포함되지 않습니다. 이 테스트의 경우 데이터 볼륨 요금에 포함됩니다.)  
C. 지난 달의 데이터 볼륨 추세를 봅니다.  
D. 데이터 수집 [샘플링](./sampling.md)을 사용합니다.
E. 일일 데이터 볼륨 한도를 설정합니다.  

(이 문서의 스크린샷에 표시된 모든 가격은 예시 목적으로만 제시된 것입니다. 사용자의 통화 및 지역에 따른 현재 가격은 [Application Insights 가격 책정][pricing]을 참조하세요.)

Application Insights 사용량을 보다 자세히 조사하려면 **메트릭** 페이지를 열고, "데이터 요소 볼륨"이라는 메트릭을 추가한 다음, *분할 적용* 옵션을 선택하여 데이터를 "원격 분석 항목 유형"에 따라 분할합니다.

Application Insights 요금은 Azure 청구서에 추가됩니다. Azure 청구서의 자세한 내용은 Azure Portal의 **Cost Management + 청구** 섹션 또는 [Azure 청구 포털](https://account.windowsazure.com/Subscriptions)에서 참고할 수 있습니다.  Application Insights에서 이 기능을 사용하는 방법은 [아래를 참조](#viewing-application-insights-usage-on-your-azure-bill)하세요. 

![왼쪽 메뉴에서 청구를 선택합니다.](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>데이터 볼륨 메트릭 사용
<a id="understanding-ingested-data-volume"></a>

데이터 볼륨에 대해 자세히 알아보려면 Application Insights 리소스의 **메트릭**을 선택하고 새 차트를 추가합니다. 차트 메트릭의 경우 **로그 기반 메트릭** 아래에서 **데이터 요소 볼륨**을 선택합니다. **분할 적용**을 클릭하고 **`Telemetryitem` 형식** 기준 그룹화를 선택합니다.

![메트릭을 사용하여 데이터 볼륨 검토](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>데이터 볼륨 세부 정보를 파악하기 위한 쿼리

Application Insights의 데이터 볼륨을 조사하는 방법에는 두 가지가 있습니다. 첫 번째 방법에서는 `systemEvents` 테이블의 집계 정보를 사용하고, 두 번째 방법에서는 수집된 각 이벤트의 `_BilledSize` 속성을 사용합니다. `systemEvents`에는 [작업 영역 기반 Application Insights](#data-volume-for-workspace-based-application-insights-resources)의 데이터 크기 정보가 없습니다.

#### <a name="using-aggregated-data-volume-information"></a>집계된 데이터 볼륨 정보 사용

예를 들어, 다음 쿼리를 사용하여 `systemEvents` 테이블에서 지난 24시간 동안 수집된 데이터 볼륨을 확인할 수 있습니다.

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

또는 다음과 같은 쿼리를 사용하여 데이터 형식을 기준으로 지난 30일 동안의 데이터 볼륨 차트를 볼 수 있습니다(단위: 바이트).

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

이 쿼리는 [Azure 로그 경고](../platform/alerts-unified-log.md)에서 데이터 볼륨에 대한 경고를 설정하는 데 사용할 수 있습니다.  

원격 분석 데이터 변경 사항에 대해 자세히 알아보기 위해 다음 쿼리를 사용하여 형식을 기준으로 이벤트의 개수를 확인할 수 있습니다.

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>이벤트당 데이터 크기 정보 사용

데이터 볼륨의 원본에 대한 세부 정보를 알아보려면 수집된 각 이벤트의 `_BilledSize` 속성을 사용할 수 있습니다.

예를 들어, 지난 30일 동안 어느 작업이 가장 많은 데이터 볼륨을 생성했는지 살펴보려면 모든 종속성 이벤트에 대해 `_BilledSize`의 합을 구할 수 있습니다.

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>작업 영역 기반 Application Insights 리소스의 데이터 볼륨

작업 영역에 있는 모든 [작업 영역 기반 Application Insights 리소스](create-workspace-resource.md)의 지난 1주간의 데이터 볼륨 추세를 살펴보려면 Log Analytics 작업 영역으로 이동하여 다음 쿼리를 실행합니다.

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

특정 작업 영역 기반 Application Insights 리소스에 대해 형식을 기준으로 데이터 볼륨 추세를 쿼리하려면 Log Analytics 작업 영역에서 다음 쿼리를 사용합니다.

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Azure 청구서에서 Application Insights 사용량 보기

Azure는 [Azure Cost Management + 청구](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) 허브에서 많은 유용한 기능을 제공합니다. 예를 들어, “비용 분석” 기능을 사용하면 Azure 리소스에 대한 지출 내역을 확인할 수 있습니다. 리소스 종류를 기준으로 필터를 추가하면(Application Insights의 경우 microsoft.insights/components에 추가) 지출을 추적할 수 있습니다. “그룹화 방법”으로는 “미터 범주” 또는 “미터”를 선택합니다.  현재 가격 플랜이 적용되는 Application Insights 리소스의 경우, 모든 Azure Monitor 구성 요소에 대한 로그 백엔드는 하나이므로 대부분의 사용량이 Log Analytics의 미터 범주로 표시됩니다. 

사용량에 대한 자세한 내용은 [Azure Portal에서 사용량을 다운로드](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal)하여 확인할 수 있습니다.
다운로드한 스프레드시트에서 Azure 리소스별 일일 사용량을 볼 수 있습니다. 이 Excel 스프레드시트에서는 먼저 “미터 범주” 열에서 필터링을 진행하여 “Application Insights” 및 “Log Analytics”를 표시한 다음, “microsoft.insights/components 포함”에 해당되는 “인스턴스 ID” 열에서 하나의 필터를 추가하면 Application Insights 리소스의 사용량을 확인할 수 있습니다.  모든 Azure Monitor 구성 요소에 대한 로그 백엔드는 하나이므로 대부분의 Application Insights 사용량이 Log Analytics의 미터 범주를 사용하여 미터로 보고됩니다.  레거시 가격 책정 계층이 적용되는 Application Insights 리소스와 다중 단계 웹 테스트만 Application Insights의 미터 범주를 사용하여 보고됩니다.  사용량은 “사용한 수량” 열에 표시되며 각 항목에 대한 단위는 “측정 단위” 열에 표시됩니다.  [Microsoft Azure 청구 방식을 이해](../../cost-management-billing/understand/review-individual-bill.md)하는 데 도움이 되는 자세한 정보도 준비되어 있습니다.

## <a name="managing-your-data-volume"></a>데이터 볼륨 관리

전송하는 데이터 볼륨은 다음 기법을 사용하여 관리할 수 있습니다.

* **샘플링**: 샘플링을 사용하여 메트릭 왜곡을 최소화하면서 서버 및 클라이언트 앱에서 전송되는 원격 분석의 양을 줄일 수 있습니다. 샘플링은 보내는 데이터의 양을 조정하는 데 사용할 수 있는 기본 도구입니다. [샘플링 기능](./sampling.md)에 대해 자세히 알아보세요.

* **Ajax 호출 제한**: 모든 페이지 보기에서 [보고될 수 있는 Ajax 호출 수를 제한](./javascript.md#configuration)하거나 Ajax 보고를 해제할 수 있습니다.

* **불필요한 모듈 사용 안 함**: [ApplicationInsights.config를 편집](./configuration-with-applicationinsights-config.md)하여 필요하지 않은 컬렉션 모듈을 끕니다. 예를 들어 성능 카운터 또는 종속성 데이터가 필요하지 않다고 결정할 수 있습니다.

* **메트릭 사전 집계**: 앱에 TrackMetric에 대한 호출을 추가한 경우 측정 일괄 처리의 평균 및 표준 편차 계산을 허용하는 오버로드를 사용하여 트래픽을 줄일 수 있습니다. 또는 [사전 집계 패키지](https://www.myget.org/gallery/applicationinsights-sdk-labs)를 사용할 수 있습니다.
 
* **일일 한도**: Azure Portal에서 Application Insights 리소스를 만들 때 일일 한도는 100GB/일로 설정됩니다. Visual Studio에서 Application Insights 리소스를 만들 때 기본값은 적습니다(불과 32.3MB/일). 일일 한도 기본값은 테스트가 용이하도록 설정됩니다. 앱을 프로덕션에 배포하기 전에 사용자가 일일 한도를 높여야 합니다. 

    트래픽이 많은 애플리케이션에 대해 더 높은 최대값을 요구하지 않으면 최대 한도는 하루 1,000GB입니다.
    
    일일 상한에 대한 경고 메일은 Application Insights 리소스의 “ServiceAdmin”, “AccountAdmin”, “CoAdmin”, “Owner” 역할의 구성원인 계정으로 전송됩니다.

    일일 한도를 설정할 때 주의해야 합니다. *일일 한도에 도달하지 않도록* 해야 합니다. 일일 한도에 도달하는 경우 해당 날짜의 남은 기간 동안 데이터가 손실되고 애플리케이션을 모니터링할 수 없습니다. 일일 한도를 변경하려면 **일일 볼륨 한도** 옵션을 사용합니다. **사용량 및 예상 비용** 창에서 이 옵션에 액세스할 수 있습니다(문서의 뒷부분에 자세히 설명되어 있음).
    
    Application Insights에 대해 사용될 수 없는 크레딧이 있는 일부 구독 유형에 대한 제한을 제거했습니다. 이전에 구독에 지출 한도가 있는 경우 일일 한도 대화 상자에 지출 한도를 제거하고 일일 한도가 하루 32.3MB가 넘도록 설정하는 지침이 표시됩니다.
    
* **제한**: 제한은 데이터 속도를 계측 키당 평균 1분 이상 초당 32,000개 이벤트로 제한합니다. 앱에 보내는 데이터의 양은 1분마다 평가됩니다. 해당 분에 대한 평균 초당 속도를 초과하면 서버는 일부 요청을 거부합니다. SDK는 데이터를 버퍼링한 다음, 다시 전송하도록 시도합니다. 몇 분 동안 급증을 분산시킵니다. 앱이 제한율 이상으로 일관되게 데이터를 보내는 경우 일부 데이터가 삭제됩니다. (ASP.NET, Java 및 JavaScript SDK는 이러한 방식으로 데이터 재전송을 시도합니다. SDK는 제한된 데이터를 제거하기만 합니다.) 제한이 발생하는 경우 이를 경고하는 알림이 표시됩니다.

## <a name="manage-your-maximum-daily-data-volume"></a>일일 최대 데이터 볼륨 관리

일일 볼륨 한도를 사용하여 수집된 데이터를 제한할 수 있습니다. 그러나 한도가 충족되는 경우 해당 날짜의 나머지 기간 동안 애플리케이션에서 보낸 모든 원격 분석의 손실이 발생합니다. 애플리케이션이 일일 한도에 도달하는 것은 *권장되지 않습니다*. 일일 한도에 도달한 후 애플리케이션의 상태 및 성능을 추적할 수 없습니다.

일별 볼륨 한도를 사용하는 대신 [샘플링](./sampling.md)을 사용하여 원하는 수준으로 데이터 볼륨을 조정합니다. 그런 다음, 애플리케이션이 예기치 않게 높은 볼륨의 원격 분석을 보내기 시작하는 경우 "최후의 수단"으로만 일일 한도를 사용합니다.

### <a name="identify-what-daily-data-limit-to-define"></a>정의할 일일 데이터 한도 식별

데이터 수집 추세 및 정의할 일일 볼륨 한도를 이해하려면 Application Insights 사용량 및 예상 비용을 검토합니다. 한도에 도달한 후에는 리소스를 모니터링할 수 없으므로 신중하게 고려해야 합니다.

### <a name="set-the-daily-cap"></a>일일 상한 설정

일일 한도를 변경하려면 Application Insights 리소스의 **구성** 섹션에 있는 **사용량 및 예상 비용** 페이지에서 **일일 한도**를 선택합니다.

![일별 원격 분석 볼륨 한도 조정](./media/pricing/pricing-003.png)

[Azure Resource Manager를 통해 일일 한도를 변경](./powershell.md)하려면 `dailyQuota` 속성을 변경해야 합니다.  Azure Resource Manager를 통해 `dailyQuotaResetTime`과 일일 한도의 `warningThreshold`도 설정할 수 있습니다.

### <a name="create-alerts-for-the-daily-cap"></a>일일 한도 경고 만들기

Application Insights 일일 한도는 수집된 데이터 볼륨이 경고 수준이나 일일 한도 수준에 도달하면 Azure 활동 로그에 이벤트를 만듭니다.  [이러한 활동 로그 이벤트를 기반으로 경고를 만들 수 있습니다](../platform/alerts-activity-log.md#create-with-the-azure-portal). 이러한 이벤트의 신호는 다음과 같습니다.

* Application Insights 구성 요소 일일 상한 경고 임계값에 도달함

* Application Insights 구성 요소 일일 상한에 도달함

## <a name="sampling"></a>샘플링
[샘플링](./sampling.md) 은 진단 검색 중에 관련 이벤트를 찾는 기능을 유지 하면서 앱에 원격 분석이 전송 되는 속도를 줄이는 방법입니다. 또한 올바른 이벤트 개수를 유지합니다.

샘플링은 요금을 줄이고 월간 할당량 내로 유지하는 효과적인 방법입니다. 샘플링 알고리즘은 예를 들어 검색을 사용하면 특정 예외와 관련된 요청을 찾을 수 있도록 원격 분석의 관련된 항목을 유지합니다. 알고리즘은 또한 요청 속도, 예외 속도 및 기타 카운트에 대해 메트릭 탐색기에 올바른 값을 확인할 수 있도록 올바른 카운트를 유지합니다.

샘플링에는 여러 가지 유형이 있습니다.

* [적응 샘플링](./sampling.md)은 ASP.NET SDK에 대한 기본값입니다. 적응 샘플링은 앱이 보내는 원격 분석의 양을 자동으로 조정합니다. 웹앱의 SDK에서 자동으로 작동하여 네트워크에서 원격 분석 트래픽이 감소됩니다. 
* *수집 샘플링*은 한 가지 대안으로서 앱의 원격 분석이 Application Insights 서비스에 들어가는 지점에서 작동합니다. 수집 샘플링은 앱에서 보낸 원격 분석의 양에 영향을 주지 않지만 서비스에서 보존하는 양을 줄여 줍니다. 수집 샘플링을 사용하여 브라우저 및 다른 SDK의 원격 분석에서 사용한 할당량을 줄일 수 있습니다.

수집 샘플링을 설정하려면 **가격 책정** 창으로 이동합니다.

![할당량 및 가격 책정 창에서 샘플 타일을 선택한 다음, 샘플링 비율을 선택합니다.](./media/pricing/pricing-004.png)

> [!WARNING]
> **데이터 샘플링** 창은 수집 샘플링의 값만을 제어합니다. 앱에 있는 Application Insights SDK에서 적용하는 샘플링 속도를 반영하지 않습니다. 들어오는 원격 분석이 SDK에서 이미 샘플링된 경우 수집 샘플링은 적용되지 않습니다.
>

적용된 위치에 관계 없이 실제 샘플링 주기를 검색하려면 [Analytics 쿼리](../log-query/log-query-overview.md)를 사용합니다. 쿼리는 다음과 같습니다.

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

보존된 각 레코드에서 `itemCount`은 나타내는 원래 레코드 수를 나타냅니다. 1 + 이전에 삭제된 레코드의 수와 같습니다.

## <a name="change-the-data-retention-period"></a>데이터 보존 기간 변경

Application Insights 리소스의 기본 보존 기간은 90일입니다. 각 Application Insights 리소스에 대해 서로 다른 보존 기간을 선택할 수 있습니다. 사용 가능한 보존 기간은 30, 60, 90, 120, 180, 270, 365, 550, 730일입니다. 보다 긴 데이터 보존을 사용할 경우의 가격을 [자세히 알아보세요](https://azure.microsoft.com/pricing/details/monitor/). 

보존 기간을 변경하려면 Application Insights 리소스에서 **사용량 및 예상 비용** 페이지로 이동하고 다음과 같은 **데이터 보존** 옵션을 선택합니다.

![일별 원격 분석 볼륨 한도 조정](./media/pricing/pricing-005.png)

데이터 보존 기간을 줄이면 가장 오래된 데이터를 제거하기 전에 며칠의 유예 기간이 적용됩니다.

`retentionInDays` 매개 변수를 사용하여 [PowerShell을 통해 프로그래밍 방식으로](powershell.md#set-the-data-retention) 보존 기간을 설정할 수도 있습니다. 데이터 보존 기간을 30일로 설정하면 `immediatePurgeDataOn30Days` 매개 변수를 사용하여 이전 데이터를 즉시 제거할 수 있는데, 이는 규정 준수 관련 시나리오에 도움이 될 수 있습니다. 이 제거 기능은 Azure Resource Manager를 통해서만 노출되며, 극도로 주의하여 사용해야 합니다. 데이터 볼륨 한도의 일일 재설정 시간은 Azure Resource Manager에서 `dailyQuotaResetTime` 매개 변수를 설정하여 구성할 수 있습니다.

## <a name="data-transfer-charges-using-application-insights"></a>Application Insights를 사용하는 경우의 데이터 전송 요금

Application Insights로 데이터를 전송하면 데이터 대역폭 요금이 발생할 수 있습니다. [Azure Bandwidth 가격 책정 페이지](https://azure.microsoft.com/pricing/details/bandwidth/)에 설명된 바와 같이, 두 지역에 위치한 Azure 서비스 간의 데이터 전송 시 요금은 일반 요율을 적용한 아웃바운드 데이터 전송 요금으로 청구되었습니다. 인바운드 데이터 전송은 무료입니다. 다만 이 요금은 Application Insights 로그 데이터 수집에 드는 비용에 비해 매우 적습니다(수% 정도에 불과). 따라서 Log Analytics에 대한 비용은 수집된 데이터 볼륨에 중점을 두고 관리해야 하며, 이러한 비용 관리를 이해하는 데 도움이 될만한 지침은 [여기](#managing-your-data-volume)를 참조하시기 바랍니다.

## <a name="limits-summary"></a>제한 요약

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>일일 상한도 이메일을 사용하지 않도록 설정

일일 볼륨 상한 이메일을 사용하지 않도록 설정하려면 Application Insights 리소스의 **구성** 섹션 아래에 있는 **사용량 및 예상 비용** 창에서 **일일 상한**을 선택합니다. 상한에 도달할 때, 그리고 조정 가능한 경고 수준에 도달할 때 이메일을 보내는 설정이 있습니다. 일일 상한 볼륨과 관련된 모든 메일을 사용하지 않도록 설정하려면 확인란을 선택 취소합니다.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>레거시 엔터프라이즈(노드당) 가격 책정 계층

초기에 Azure Application Insights를 도입한 경우 Basic 및 Enterprise의 두 가지 가격 책정 요금제가 있습니다. Basic 가격 책정 계층은 위에 설명한 것과 동일한 기본 계층입니다. 여기에는 추가 비용 없이 모든 Enterprise 계층 기능이 포함됩니다. Basic 계층에서는 기본적으로 수집된 데이터의 볼륨에 따라 비용이 청구됩니다.

> [!NOTE]
> 이 레거시 가격 책정 계층의 이름이 변경되었습니다. Enterprise 가격 책정 계층은 **노드당** 계층으로 변경되었고, Basic 가격 책정 계층은 **GB당** 계층으로 변경되었습니다. 아래에서, 그리고 Azure Portal에서 이 새로운 이름이 사용됩니다.  

노드당(이전 명칭: Enterprise) 계층에는 노드별 청구가 사용되며 각 노드는 일일 데이터 허용량을 수신합니다. 노드당 가격 책정 계층에서는 포함된 허용량 이상으로 수집된 데이터에 대해 요금이 부과됩니다. Operations Management Suite를 사용하는 경우 노드당 계층을 선택해야 합니다.

사용자의 통화 및 지역에 따른 현재 가격은 [Application Insights 가격 책정](https://azure.microsoft.com/pricing/details/application-insights/)을 참조하세요.

> [!NOTE]
> 2018년 4월에는 Azure 모니터링을 위한 새로운 가격 책정 모델이 [도입](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)되었습니다. 이 모델은 모니터링 서비스의 전체 포트폴리오에서 간단한 "종량제" 모델을 채택합니다. [새 가격 책정 모델](../platform/usage-estimated-costs.md), 사용 패턴에 따라 [이 모델로 전환하는 영향을 평가](../platform/usage-estimated-costs.md#understanding-your-azure-monitor-costs)하는 방법 및 [새 모델을 옵트인하는 방법](../platform/usage-estimated-costs.md#azure-monitor-pricing-model)에 대해 자세히 알아봅니다.

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>노드당 계층 및 Operations Management Suite 구독 자격

[전에 발표](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription)된 대로 Operations Management Suite E1 및 E2를 구매하는 고객은 추가 비용 없이 노드당 Application Insights를 추가 구성 요소로 얻을 수 있습니다. 특히, Operations Management Suite E1 및 E2의 각 구매 단위에는 Application Insights 노드당 계층의 노드 1개에 대한 자격이 포함됩니다. 각 Application Insights 노드에는 매일 수집되는 데이터가 200MB까지 포함되고(Log Analytics 데이터 수집과 별도) 데이터는 추가 비용 없이 90일 간 보존됩니다. 계층은 문서 뒷부분에 자세히 설명되어 있습니다.

이 계층은 Operations Management Suite 구독이 있는 고객에게만 적용 가능하므로 Operations Management Suite 구독이 없는 고객에게는 이 계층을 선택하는 옵션이 표시되지 않습니다.

> [!NOTE]
> 이 자격을 얻으려면 노드당 가격 책정 계층에 Application Insights 리소스가 있어야 합니다. 이 자격은 노드로만 적용됩니다. GB당 계층의 Application Insights 리소스에는 이점이 없습니다. 이 자격은 **사용량 및 예상 비용** 창에 표시된 예상 비용에서 볼 수 없습니다. 또한 구독을 2018년 4월의 새 Azure 모니터링 가격 책정 모델로 이동하는 경우 GB당 계층은 사용할 수 있는 유일한 계층입니다. Operations Management Suite 구독이 있는 경우 새 Azure 모니터링 가격 책정 모델로 구독을 이동하는 것을 권장하지 않습니다.

### <a name="how-the-per-node-tier-works"></a>노드당 계층이 작동하는 방식

* 노드당 계층의 모든 앱에 대한 원격 분석을 보내는 노드당 비용을 지불해야 합니다.
  * *노드*는 앱을 호스트하는 실제/가상 서버 컴퓨터 또는 PaaS(Platform-as-a-Service) 역할 인스턴스입니다.
  * 개발 컴퓨터, 클라이언트 브라우저 및 모바일 디바이스는 노드로 계산되지 않습니다.
  * 원격 분석을 보내는 여러 구성 요소(예: 웹 서비스 및 백 엔드 작업자)가 앱에 있는 경우 해당 구성 요소는 개별적으로 집계됩니다.
  * [라이브 메트릭 스트림](./live-stream.md) 데이터는 가격 책정에 계산 되지 않습니다. 구독에서 요금은 앱 기준이 아니라 노드 기준으로 부과됩니다. 12개 앱에 대해 원격 분석을 보내는 노드가 5개 있는 경우 5개 노드에 대한 요금이 부과됩니다.
* 요금이 매월 견적되지만 노드에서 앱의 원격 분석을 보내는 모든 시간에 대해서만 부과됩니다. 시간당 요금은 견적된 월별 요금을 744(한 달이 31일일 때 시간 수)로 나눈 것입니다.
* 시간별로 감지되는 각 노드에 대해 1일 200MB의 데이터 볼륨 할당이 제공됩니다. 사용되지 않은 데이터 할당은 다음 날로 이월되지 않습니다.
  * 노드당 가격 책정 계층을 선택하면 각 구독은 해당 구독의 Application Insights 리소스에 원격 분석을 보내는 노드 수에 따라 일일 데이터 허용량을 받습니다. 따라서 하루 종일 데이터를 전송하는 5개의 노드가 있는 경우 해당 구독의 모든 Application Insights 리소스에 풀링된 1GB 허용량이 적용됩니다. 모든 노드에서 포함된 데이터를 공유하기 때문에 특정 노드에서 다른 노드보다 더 많은 데이터를 보내는 것은 중요하지 않습니다. 지정된 날에 Application Insights 리소스에서 해당 구독의 일일 데이터 할당에 포함된 것보다 많은 데이터를 받으면 GB당 초과 데이터 요금이 적용됩니다. 
  * 일일 데이터 허용량은 각 노드에서 원격 분석을 보내는 날의 시간 수(UTC 사용)를 24로 나눈 값에 200MB를 곱해 계산합니다. 따라서 하루 24시간 중 15시간 동안 원격 분석을 보내는 4개의 노드가 있는 경우 해당일의 포함된 데이터는 ((4 x 15) / 24) x 200MB = 500MB가 됩니다. 데이터 초과분의 가격 조건이 GB당 2.30 달러일 때 노드에서 해당일 1GB의 데이터를 보내는 경우 요금은 1.15달러입니다.
  * 노드당 계층 일일 허용량은 GB당 계층을 선택한 애플리케이션과는 공유되지 않습니다. 사용하지 않는 허용량은 다음 날로 넘어가지 않습니다.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>고유 노드 수를 결정하는 방법 예제

| 시나리오                               | 일일 총 노드 수 |
|:---------------------------------------|:----------------:|
| 1개 애플리케이션에서 3개 Azure App Service 인스턴스 및 1개 가상 서버를 사용합니다. | 4 |
| 2개 VM에서 실행되는 3개 애플리케이션 및 이러한 애플리케이션의 Application Insights 리소스는 동일한 구독 및 노드당 계층에 있습니다. | 2 | 
| 4개의 애플리케이션은 해당 Application Insights 리소스가 같은 구독에 있고 각 애플리케이션은 사용률이 낮은 16시간 동안 2개의 인스턴스를 실행하고, 사용률이 최대로 높은 8시간 동안 4개의 인스턴스를 실행합니다. | 13.33 |
| 1개 작업자 역할 및 1개 웹 역할이 부여된 클라우드 서비스에서 각 역할마다 2개 인스턴스를 실행합니다. | 4 | 
| 5개 노드 Azure Service Fabric 클러스터에서 50개의 마이크로 서비스를 실행하며 각 마이크로 서비스에서 3개의 인스턴스를 실행합니다. | 5|

* 정확한 노드 계산은 애플리케이션에서 사용하는 Application Insights SDK에 따라 다릅니다. 
  * SDK 버전 2.2 이상에서 Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) 및 [웹 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) 둘 다 각 애플리케이션 호스트를 노드로 보고합니다. 물리적 서버 및 VM 호스트에 대한 컴퓨터 이름이나 클라우드 서비스에 대한 인스턴스 이름을 예로 들 수 있습니다.  유일한 예외는 [.NET Core](https://dotnet.github.io/) 및 Application Insights Core SDK만 사용하는 애플리케이션입니다. 이 경우 호스트 이름을 사용할 수 없으므로 모든 호스트에 대해 하나의 노드만 보고됩니다.
  * 이전 버전의 SDK에서는 [웹 SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)가 최신 SDK 버전과 마찬가지로 작동하지만, [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)는 애플리케이션 호스트 수에 관계 없이 하나의 노드만 보고합니다.
  * 애플리케이션에서 SDK를 사용하여 **roleInstance**를 사용자 지정 값으로 설정하는 경우 기본적으로 이 동일한 값이 노드 수를 결정하는 데 사용됩니다.
  * 클라이언트 컴퓨터 또는 모바일 디바이스에서 실행되는 앱과 함께 새 SDK 버전을 사용하는 경우, 노드 계산 시 큰 숫자가 반환될 수 있습니다(클라이언트 컴퓨터 또는 모바일 디바이스 수가 많으므로).

## <a name="automation"></a>Automation

Azure Resource Management를 사용하여 가격 책정 계층을 설정하는 스크립트를 작성할 수 있습니다. [방법을 알아보세요](powershell.md#price).

## <a name="next-steps"></a>다음 단계

* [견본](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/

