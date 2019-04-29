---
title: Azure Cloud Services에서 성능 카운터 수집 | Microsoft Docs
description: Azure Diagnostics 및 Application Insights를 사용하여 Cloud Services에서 성능 카운터를 검색, 사용 및 만드는 방법을 알아봅니다.
services: cloud-services
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: ''
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2018
ms.author: jeconnoc
ms.openlocfilehash: 68101be211335d51eb4bf99361ea36b73fa19218
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653742"
---
# <a name="collect-performance-counters-for-your-azure-cloud-service"></a>Azure Cloud Service에 대한 성능 카운터 수집

성능 카운터는 애플리케이션과 호스트의 성능을 추적할 수 있는 방법을 제공합니다. Windows Server는 하드웨어, 애플리케이션, 운영 체제 등과 관련된 다양한 성능 카운터를 제공합니다. 성능 카운터를 수집하고 Azure에 보내면 이 정보를 분석하여 더 나은 결정을 내릴 수 있습니다. 

## <a name="discover-available-counters"></a>사용 가능한 카운터 검색

성능 카운터는 두 부분, 즉 세트 이름(범주라고도 함)과 하나 이상의 카운터로 이루어져 있습니다. PowerShell을 사용하여 사용 가능한 성능 카운터 목록을 가져올 수 있습니다.

```powershell
Get-Counter -ListSet * | Select-Object CounterSetName, Paths | Sort-Object CounterSetName

CounterSetName                                  Paths
--------------                                  -----
.NET CLR Data                                   {\.NET CLR Data(*)\SqlClient...
.NET CLR Exceptions                             {\.NET CLR Exceptions(*)\# o...
.NET CLR Interop                                {\.NET CLR Interop(*)\# of C...
.NET CLR Jit                                    {\.NET CLR Jit(*)\# of Metho...
.NET Data Provider for Oracle                   {\.NET Data Provider for Ora...
.NET Data Provider for SqlServer                {\.NET Data Provider for Sql...
.NET Memory Cache 4.0                           {\.NET Memory Cache 4.0(*)\C...
AppV Client Streamed Data Percentage            {\AppV Client Streamed Data ...
ASP.NET                                         {\ASP.NET\Application Restar...
ASP.NET Apps v4.0.30319                         {\ASP.NET Apps v4.0.30319(*)...
ASP.NET State Service                           {\ASP.NET State Service\Stat...
ASP.NET v2.0.50727                              {\ASP.NET v2.0.50727\Applica...
ASP.NET v4.0.30319                              {\ASP.NET v4.0.30319\Applica...
Authorization Manager Applications              {\Authorization Manager Appl...

#... results cut to save space ...
```

`CounterSetName` 속성은 세트(또는 범주)를 나타내며 성능 카운터가 무엇과 관련이 있는지를 보여 줍니다. `Paths` 속성은 세트에 대한 카운터 컬렉션을 나타냅니다. 카운터 세트에 대한 추가 정보를 위해 `Description` 속성을 가져올 수도 있습니다.

세트에 대한 모든 카운터를 가져오려면 `CounterSetName` 값을 사용하고 `Paths` 컬렉션을 확장합니다. 각 경로 항목은 쿼리할 수 있는 카운터입니다. 예를 들어 `Processor` 세트와 관련된 사용 가능한 카운터를 가져오려면 `Paths` 컬렉션을 확장합니다.

```powershell
Get-Counter -ListSet * | Where-Object CounterSetName -eq "Processor" | Select -ExpandProperty Paths

\Processor(*)\% Processor Time
\Processor(*)\% User Time
\Processor(*)\% Privileged Time
\Processor(*)\Interrupts/sec
\Processor(*)\% DPC Time
\Processor(*)\% Interrupt Time
\Processor(*)\DPCs Queued/sec
\Processor(*)\DPC Rate
\Processor(*)\% Idle Time
\Processor(*)\% C1 Time
\Processor(*)\% C2 Time
\Processor(*)\% C3 Time
\Processor(*)\C1 Transitions/sec
\Processor(*)\C2 Transitions/sec
\Processor(*)\C3 Transitions/sec
```

이러한 개별 카운터 경로를 클라우드 서비스가 사용하는 진단 프레임워크에 추가할 수 있습니다. 성능 카운터 경로를 구성하는 방법에 대한 자세한 내용은 [카운터 경로 지정](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))을 참조하세요.

## <a name="collect-a-performance-counter"></a>성능 카운터 수집

Azure Diagnostics 또는 Application Insights를 위해 클라우드 서비스에 성능 카운터를 추가할 수 있습니다.

### <a name="application-insights"></a>Application Insights

Cloud Services용 Azure Application Insights를 사용하면 수집할 성능 카운터를 지정할 수 있습니다. [Application Insights를 프로젝트에 추가](../azure-monitor/app/cloudservices.md#sdk)하면 **ApplicationInsights.config**라는 구성 파일이 Visual Studio 프로젝트에 추가됩니다. 이 구성 파일은 Application Insights가 수집하여 Azure에 전송하는 정보 유형을 정의합니다.

**ApplicationInsights.config** 파일을 열고 **ApplicationInsights** > **TelemetryModules** 요소를 찾습니다. 각 `<Add>` 자식 요소는 해당 구성과 함께 수집할 원격 분석 유형을 정의합니다. 성능 카운터 원격 분석 모듈 유형은 `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector`입니다. 이 요소가 이미 정의된 경우에는 다시 추가하지 마세요. 수집할 각 성능 카운터는 `<Counters>`라는 노드 아래에 정의됩니다. 다음은 드라이브 성능 카운터를 수집하는 예제입니다.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Write Bytes/sec" ReportAs="Disk write (C:)" />
        <Add PerformanceCounter="\LogicalDisk(C:)\Disk Read Bytes/sec" ReportAs="Disk read (C:)" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

각 성능 카운터는 `<Counters>` 아래에 `<Add>` 요소로 표시됩니다. `PerformanceCounter` 특성은 수집할 성능 카운터를 정의합니다. `ReportAs` 특성은 성능 카운터에 대해 Azure Portal에 표시할 제목입니다. 수집한 성능 카운터는 포털의 **사용자 지정** 범주에 배치됩니다. Azure Diagnostics와 달리 이러한 성능 카운터를 수집하여 Azure에 전송하는 간격은 설정할 수 없습니다. Application Insights를 사용하면 성능 카운터가 1분마다 수집되어 전송됩니다. 

Application Insights는 다음 성능 카운터를 자동으로 수집합니다.

* \Process(??APP_WIN32_PROC??)\% Processor Time
* \Memory\Available Bytes
* \.NET CLR Exceptions(??APP_CLR_PROC??)\# of Exceps Thrown / sec
* \Process(??APP_WIN32_PROC??)\Private Bytes
* \Process(??APP_WIN32_PROC??)\IO Data Bytes/sec
* \Processor(_Total)\% 프로세서 시간

자세한 내용은 [Application Insights의 시스템 성능 카운터](../azure-monitor/app/performance-counters.md) 및 [Azure Cloud Services용 Application Insights](../azure-monitor/app/cloudservices.md#performance-counters)를 참조하세요.

### <a name="azure-diagnostics"></a>Azure Diagnostics

> [!IMPORTANT]
> 이러한 모든 데이터가 저장소 계정에 집계되지만, 포털은 데이터를 차트로 작성하는 기본적인 방법을 제공하지 **않습니다**. Application Insights 등의 다른 진단 서비스를 애플리케이션에 통합하는 것이 좋습니다.

Cloud Services용 Azure Diagnostics 확장을 사용하면 수집할 성능 카운터를 지정할 수 있습니다. Azure Diagnostics를 설정하려면 [클라우드 서비스 모니터링 개요](cloud-services-how-to-monitor.md#setup-diagnostics-extension)를 참조하세요.

수집하려는 성능 카운터는 **diagnostics.wadcfgx** 파일에 정의됩니다. **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters**를 찾습니다. 새 **PerformanceCounterConfiguration** 요소를 자식으로 추가합니다. 이 요소에는 두 가지 특성(`counterSpecifier` 및 `sampleRate`)이 있습니다. `counterSpecifier` 특성은 수집할 시스템 성능 카운터 세트(이전 섹션에 요약되어 있음)를 정의합니다. `sampleRate` 값은 해당 값이 폴링되는 빈도를 나타냅니다. 전반적으로 모든 성능 카운터는 부모 `PerformanceCounters` 요소의 `scheduledTransferPeriod` 특성 값에 따라 Azure에 전송됩니다.

`PerformanceCounters` 스키마 요소에 대한 자세한 내용은 [Azure 진단 스키마](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)를 참조하세요.

`sampleRate` 특성으로 정의된 기간은 XML 기간 데이터 형식을 사용하여 성능 카운터가 폴링되는 빈도를 나타냅니다. 아래 예제에서는 속도가 `PT3M`(`[P]eriod[T]ime[3][M]inutes`: 3분마다)으로 설정됩니다.

`sampleRate` 및 `scheduledTransferPeriod` 정의 방법에 대한 자세한 내용은 [W3 XML 날짜 및 시간 날짜 형식](https://www.w3schools.com/XML/schema_dtypes_date.asp) 자습서의 **기간 데이터 형식** 섹션을 참조하세요.

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />

          <!-- This is a new perf counter which will track the C: disk read activity in bytes per second, every minute -->
          <PerformanceCounterConfiguration counterSpecifier="\LogicalDisk(C:)\Disk Read Bytes/sec" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="create-a-new-perf-counter"></a>새 성능 카운터 만들기

코드에서 새 성능 카운터를 만들고 사용할 수 있습니다. 새 성능 카운터를 만드는 코드는 관리자 권한으로 실행해야 합니다. 그러지 않으면 실패합니다. 클라우드 서비스 `OnStart` 시작 코드는 성능 카운터를 만들 수 있으므로 관리자 권한 컨텍스트에서 역할을 실행해야 합니다. 또는 관리자 권한으로 실행되고 성능 카운터를 만드는 시작 작업을 만들 수 있습니다. 시작 작업에 대한 자세한 내용은 [클라우드 서비스에 대한 시작 작업을 구성 및 실행하는 방법](cloud-services-startup-tasks.md)을 참조하세요.

관리자 권한으로 실행되도록 역할을 구성하려면 [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) 파일에 `<Runtime>` 요소를 추가합니다.

```xml
<ServiceDefinition name="CloudServiceLoadTesting" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Large">

    <!-- ... cut to save space ... -->

    <Runtime executionContext="elevated">
      
    </Runtime>

    <!-- ... cut to save space ... -->

  </WorkerRole>
</ServiceDefinition>
```

몇 줄의 코드로 새 성능 카운터를 만들고 등록할 수 있습니다. 범주와 카운터를 둘 다 만드는 `System.Diagnostics.PerformanceCounterCategory.Create` 메서드 오버로드를 사용합니다. 다음 코드에서는 먼저 범주가 있는지 확인하고, 없을 경우 범주와 카운터를 둘 다 만듭니다.

```csharp
using System.Diagnostics;
using Microsoft.WindowsAzure;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRoleWithSBQueue1
{
    public class WorkerRole : RoleEntryPoint
    {
        // Perf counter variable representing times service was used.
        private PerformanceCounter counterServiceUsed;

        public override bool OnStart()
        {
            // ... Other startup code here ...

            // Define the category and counter names.
            string perfCounterCatName = "MyService";
            string perfCounterName = "Times Used";

            // Create the counter if needed. Our counter category only has a single counter.
            // Both the category and counter are created in the same method call.
            if (!PerformanceCounterCategory.Exists(perfCounterCatName))
            {
                PerformanceCounterCategory.Create(perfCounterCatName, "Collects information about the cloud service.", 
                                                  PerformanceCounterCategoryType.SingleInstance, 
                                                  perfCounterName, "How many times the cloud service was used.");
            }

            // Get reference to our counter
            counterServiceUsed = new PerformanceCounter(perfCounterCatName, perfCounterName);
            counterServiceUsed.ReadOnly = false;
            
            return base.OnStart();
        }

        // ... cut class code to save space
    }
}
```

카운터를 사용하려는 경우 `Increment` 또는 `IncrementBy` 메서드를 호출합니다.

```csharp
// Increase the counter by 1
counterServiceUsed.Increment();
```

이제 애플리케이션에서 사용자 지정 카운터를 사용하므로 카운터를 추적하도록 Azure Diagnostics 또는 Application Insights를 구성해야 합니다.


### <a name="application-insights"></a>Application Insights

이전에 설명한 대로 Application Insights의 성능 카운터는 **ApplicationInsights.config** 파일에서 정의됩니다. **ApplicationInsights.config**를 열고 **ApplicationInsights** > **TelemetryModules** > **Add** > **Counters** 요소를 찾습니다. `<Add>` 자식 요소를 만들고 `PerformanceCounter` 특성을 코드에서 만든 성능 카운터의 범주 및 이름으로 설정합니다. `ReportAs` 특성을 포털에 표시하려는 이름으로 설정합니다.

```xml
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">

  <TelemetryModules>

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <!-- ... cut other perf counters to save space ... -->

        <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
        <Add PerformanceCounter="\MyService\Times Used" ReportAs="Service used counter" />
      </Counters>
    </Add>

  </TelemetryModules>

<!-- ... cut to save space ... -->
```

### <a name="azure-diagnostics"></a>Azure Diagnostics

이전에 언급한 대로 수집하려는 성능 카운터는 **diagnostics.wadcfgx** 파일에 정의됩니다. **DiagnosticsConfiguration** > **PublicConfig** > **WadCfg** > **DiagnosticMonitorConfiguration** > **PerformanceCounters**를 찾습니다. 새 **PerformanceCounterConfiguration** 요소를 자식으로 추가합니다. `counterSpecifier` 특성을 코드에서 만든 성능 카운터의 범주 및 이름으로 설정합니다. 

```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration  xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig>
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096">

        <!-- ... cut to save space ... -->

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <!-- ... cut other perf counters to save space ... -->
          
          <!-- This new perf counter matches the [category name]\[counter name] defined in your code -->
          <PerformanceCounterConfiguration counterSpecifier="\MyService\Times Used" sampleRate="PT1M" />

        </PerformanceCounters>
      </DiagnosticMonitorConfiguration>
    </WadCfg>
    
    <!-- ... cut to save space ... -->

  </PublicConfig>
</DiagnosticsConfiguration>
```

## <a name="more-information"></a>자세한 정보

- [Azure Cloud Services용 Application Insights](../azure-monitor/app/cloudservices.md#performance-counters)
- [Application Insights의 시스템 성능 카운터](../azure-monitor/app/performance-counters.md)
- [카운터 경로 지정](https://msdn.microsoft.com/library/windows/desktop/aa373193(v=vs.85))
- [Azure 진단 스키마 - 성능 카운터](../azure-monitor/platform/diagnostics-extension-schema-1dot3.md#performancecounters-element)
