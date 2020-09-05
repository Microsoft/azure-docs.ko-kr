---
title: Application Insights를 사용하여 라이브 Azure Cloud Services 프로파일링 | Microsoft Docs
description: Azure Cloud Services에 대해 Application Insights Profiler를 사용하도록 설정합니다.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 69a9ba316a1fda14a2e85d4d981321d4b2f9f289
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930318"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Application Insights로 라이브 Azure Cloud Services 프로파일링

다음과 같은 서비스에 Application Insights Profiler를 배포할 수도 있습니다.
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric 애플리케이션](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Application Insights Profiler는 Azure Diagnostics 확장과 함께 설치됩니다. Profiler를 설치하고 Application Insights 리소스로 프로필을 전송하도록 Azure Diagnostics를 구성하기만 하면 됩니다.

## <a name="enable-profiler-for-azure-cloud-services"></a>Azure Cloud Services에 대해 Profiler 사용
1. [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상 버전을 사용 하 고 있는지 확인 합니다. OS 제품군 4를 사용 하는 경우 [시작 작업](../../cloud-services/cloud-services-dotnet-install-dotnet.md)을 사용 하 여 .NET Framework 4.6.1 이상 버전을 설치 해야 합니다. OS 제품군 5에는 기본적으로 .NET Framework 호환 되는 버전이 포함 되어 있습니다. 

1. [Azure Cloud Services에 Application Insights SDK](./cloudservices.md?toc=%2fazure%2fazure-monitor%2ftoc.json)를 추가합니다.

    **Cloud Services에 대 한 WAD에서 제공 되는 프로파일러의 버그가 수정 되었습니다.** Cloud Services에 대 한 최신 버전의 WAD (1.12.2.0)는 최신 버전의 App Insights SDK와 함께 작동 합니다. 클라우드 서비스 호스트는 WAD를 자동으로 업그레이드 하지만 즉시 실행 되지 않습니다. 강제로 업그레이드 하려면 서비스를 다시 배포 하거나 노드를 다시 부팅 하면 됩니다.

1. Application Insights를 사용하여 요청을 추적합니다.

    * ASP.NET 웹 역할의 경우 Application Insights에서 요청을 자동으로 추적할 수 있습니다.

    * 작업자 역할의 경우 [요청을 추적 하는 코드를 추가](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)합니다.

1. Azure 진단 확장을 구성 하 여 프로파일러를 사용 하도록 설정 합니다.

    a. 애플리케이션 역할에 대한 [Azure Diagnostics](../platform/diagnostics-extension-overview.md) *diagnostics.wadcfgx* 파일을 다음과 같이 찾습니다.  

      ![진단 구성 파일의 위치](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      파일을 찾을 수 없는 경우 [Azure Cloud Services 및 Virtual Machines에 대한 진단 설정](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines?view=vs-2019)을 참조하세요.

    b. 다음 `SinksConfig` 섹션을 `WadCfg`의 자식 요소로 추가합니다.  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    > [!NOTE]
    > *diagnostics.wadcfgx* 파일에 ApplicationInsights 형식의 다른 싱크도 포함된 경우 다음 세 가지 계측 키가 모두 일치해야 합니다.  
    > * 애플리케이션에 사용되는 키입니다. 
    > * ApplicationInsights 싱크에 사용되는 키 
    > * ApplicationInsightsProfiler 싱크에 사용되는 키 
    >
    > `ApplicationInsights`Serviceconfiguration.cscfg에서 싱크에서 사용 하는 실제 계측 키 값을 찾을 수 있습니다. * \* .cscfg* 파일. 
    > Visual Studio 15.5 Azure SDK 릴리스 후에는 애플리케이션과 ApplicationInsightsProfiler 싱크에 사용되는 계측 키만 서로 일치하면 됩니다.

1. 새 진단 구성을 사용하여 서비스를 배포하면 서비스에서 실행되도록 Application Insights Profiler가 구성됩니다.
 
## <a name="next-steps"></a>다음 단계

* 애플리케이션에 대한 트래픽을 생성합니다(예: [가용성 테스트](monitor-web-app-availability.md) 시작). 그런 다음, 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
* Azure Portal의 [Profiler 추적](profiler-overview.md?toc=/azure/azure-monitor/toc.json) 을 참조 하세요.
* Profiler 문제를 해결하려면 [Profiler 문제 해결](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.

