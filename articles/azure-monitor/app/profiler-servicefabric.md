---
title: Application Insights를 사용하여 라이브 Azure Service Fabric 앱 프로파일링
description: Service Fabric 애플리케이션에 대해 Profiler 사용
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 67e7765a1f46c2be5790c11687e06ea624702b9b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "100589573"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Application Insights를 사용하여 라이브 Azure Service Fabric 애플리케이션 프로파일링

다음과 같은 서비스에 Application Insights Profiler를 배포할 수도 있습니다.
* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="set-up-the-environment-deployment-definition"></a>환경 배포 정의 설정

Application Insights Profiler는 Azure Diagnostics에 포함되어 있습니다. Service Fabric 클러스터용 Azure Resource Manager 템플릿을 사용하여 Azure Diagnostics 확장을 설치할 수 있습니다. [Service Fabric 클러스터에 Azure Diagnostics를 설치하는 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)을 가져옵니다.

환경을 설정하려면 다음 작업을 수행합니다.

1. Profiler는 .NET Framework 및 .NET Core를 지원합니다. .NET Framework를 사용하는 경우 [.NET Framework 4.6.1](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) 이상을 사용해야 합니다. 배포된 OS가 `Windows Server 2012 R2` 이상인지 확인하면 충분합니다. Profiler는 .NET Core 2.1 이상의 애플리케이션을 지원합니다.

1. 배포 템플릿 파일에서 [Azure Diagnostics](../agents/diagnostics-extension-overview.md) 확장을 검색합니다.

1. 다음 `SinksConfig` 섹션을 `WadCfg`의 자식 요소로 추가합니다. `ApplicationInsightsProfiler` 속성 값을 고유한 Application Insights 계측 키로 바꿉니다.  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      배포 템플릿에 진단 확장을 추가하는 방법에 대한 정보는 [Windows VM 및 Azure Resource Manager 템플릿을 사용하여 모니터링 및 진단 사용](../../virtual-machines/extensions/diagnostics-template.md?toc=/azure/virtual-machines/windows/toc.json)을 참조하세요.

1. Azure Resource Manager 템플릿을 사용하여 Service Fabric 클러스터를 배포합니다.  
  설정이 올바른 경우 Azure Diagnostics 확장이 설치될 때 Application Insights Profiler가 설치되고 사용하도록 설정됩니다. 

1. Service Fabric 애플리케이션에 Application Insights를 추가합니다.  
  Profiler가 요청에 대한 프로필을 수집하려면 애플리케이션이 Application Insights를 사용하여 작업을 추적해야 합니다. 상태 비저장 API의 경우 [프로파일링을 위한 요청 추적](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)에 대한 지침을 참조할 수 있습니다. 다른 종류의 앱에서 사용자 지정 작업을 추적하는 방법에 대한 자세한 내용은 [Application Insights .NET SDK를 사용한 사용자 지정 작업 추적](custom-operations-tracking.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.

1. 애플리케이션을 다시 배포합니다.


## <a name="next-steps"></a>다음 단계

* 애플리케이션에 대한 트래픽을 생성합니다(예: [가용성 테스트](monitor-web-app-availability.md) 시작). 그런 다음, 추적을 10~15분 동안 기다려서 Application Insights 인스턴스로 보내기 시작합니다.
* Azure Portal에서 [Profiler 추적](profiler-overview.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.
* Profiler 문제 해결 지원을 받으려면 [Profiler 문제 해결](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)을 참조하세요.
