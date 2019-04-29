---
title: Azure 모니터링 에이전트 개요 | Microsoft Docs
description: 이 문서에서는 Azure 또는 하이브리드 환경에서 호스팅되는 가상 머신의 모니터링을 지원하는 사용 가능한 Azure 에이전트에 대한 자세한 개요를 제공합니다.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/14/2018
ms.author: magoedte
ms.openlocfilehash: 89e73a4578134493a25ec3cbd2385433a2b36156
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60775883"
---
# <a name="overview-of-the-azure-monitoring-agents"></a>Azure 모니터링 에이전트 개요 
Microsoft Azure는 Azure, 데이터 센터 또는 기타 클라우드 공급자에 호스팅되는 Microsoft Windows 및 Linux를 실행하는 가상 머신에서 다양한 형식의 데이터를 수집하는 여러 방법을 제공합니다. VM을 모니터링하는 데 사용할 수 있는 세 가지 에이전트 유형은 다음과 같습니다.

* Azure Diagnostics 확장
* Linux 및 Windows용 Log Analytics 에이전트
* 종속성 에이전트

이 문서에서는 IT 서비스 관리 또는 일반 모니터링 요구 사항을 지원할 기능을 결정하기 위해 이러한 서비스와 해당 기능 간의 차이점을 설명합니다.  

## <a name="azure-diagnostic-extension"></a>Azure 진단 확장
2010년에 일반적으로 지원된 이후로 Azure Cloud Services에 대해 제공되는 [Azure Diagnostics 확장](../../azure-monitor/platform/diagnostics-extension-overview.md)(일반적으로 WAD(Windows Azure Diagnostic) 또는 LAD(Linux Azure Diagnostic) 확장이라고 함)은 VM과 같은 Azure 컴퓨팅 리소스에서 진단 데이터의 간단한 컬렉션을 제공하고 Azure 스토리지에 저장하는 에이전트입니다. 스토리지에 저장되면 [Visual Studio의 서버 탐색기](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage) 및 [Azure Storage 탐색기](../../vs-azure-tools-storage-manage-with-storage-explorer.md)와 같이 지원되는 여러 도구 중 하나를 사용하여 볼 수 있습니다.

다음을 수집하도록 선택할 수 있습니다.

* 운영 체제 성능 카운터 및 이벤트 로그의 미리 정의된 세트 또는 수집할 항목을 지정할 수 있습니다. 
* 모든 요청 및/또는 IIS 웹 서버에 대해 실패한 요청
* 출력 로그를 추적하는 .NET 앱
* Windows(ETW) 이벤트에 대해 추적하는 이벤트 
* syslog에서 로그 이벤트 수집  
* 크래시 덤프 

Azure Diagnostics 에이전트는 다음 작업을 수행할 때 사용해야 합니다.

* 로그 및 메트릭을 Azure Storage에 보관
* 모니터링 데이터를 타사 도구와 통합합니다. 이러한 도구는 스토리지 계정 쿼리, [Event Hubs](../../event-hubs/event-hubs-about.md)로 전달 또는 [Azure Monitoring REST API](../../azure-monitor/platform/rest-api-walkthrough.md)를 사용하여 쿼리하는 등 다양한 방법을 사용합니다.
* Azure Monitor에 데이터를 업로드하여 Azure Portal에서 메트릭 차트를 만들거나 거의 실시간 [메트릭 경고](../../azure-monitor/platform/alerts-metric-overview.md)를 만듭니다. 
* 게스트 OS 메트릭을 기반으로 가상 머신 확장 집합 및 Classic Cloud Services를 자동 크기 조정합니다.
* [부팅 진단](../../virtual-machines/troubleshooting/boot-diagnostics.md)을 사용하여 VM 부팅 문제를 조사합니다.
* 애플리케이션을 수행하는 방법을 이해하고 [Application Insights](../../azure-monitor/overview.md)를 통해 애플리케이션에 영향을 주는 문제를 사전에 식별합니다.
* Azure Monitor 메트릭 가져오기 및 클래식 Vm의 경우 Cloud Services에서 수집 된 데이터 로그를 구성 하 고 Service Fabric 노드는 Azure storage 계정에 저장 합니다.

## <a name="log-analytics-agent"></a>Log Analytics 에이전트
고급 모니터링 메트릭 및 로그의 하위 집합을 수집 이상 수행 해야 합니다 (Microsoft 모니터링 에이전트 (MMA)으로) 하는 Windows 및 Linux 용 Log Analytics 에이전트를이 필요 합니다. Log Analytics 에이전트는 온-프레미스 실제 및 가상 머신, System Center Operations Manager에서 모니터링하는 컴퓨터 및 다른 클라우드에서 호스팅되는 VM의 포괄적인 관리를 위해 개발되었습니다. Windows 및 Linux 에이전트 모니터링 솔루션 기반 데이터 뿐만 아니라 구성 하는 사용자 지정 데이터 원본을 수집 하려면 Azure Monitor에서 Log Analytics 작업 영역에 연결 합니다.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

Log Analytics 에이전트는 다음 작업을 수행할 때 사용해야 합니다.

* Azure, 기타 클라우드 공급자 및 온-프레미스 리소스 내 다양한 원본에서 데이터를 수집합니다. 
* [VM용 Azure Monitor](../insights/vminsights-overview.md), [컨테이너용 Azure Monitor](../insights/container-insights-overview.md) 등과 같은 Azure Monitor 모니터링 솔루션 중 하나를 사용합니다.  
* [Azure Security Center](../../security-center/security-center-intro.md), [Azure Automation](../../automation/automation-intro.md) 등과 같은 Azure 관리 서비스 중 하나를 사용합니다.

이전에는 여러 Azure 서비스가 *Operations Management Suite*로 번들되었으며, 그 결과 Log Analytics는 Azure Security Center 및 Azure Automation을 비롯한 여러 서비스에서 공유됩니다.  여기에는 제공되는 모든 기능 집합이 포함되어 있어, Azure VM의 수명 주기 전반에 걸쳐 포괄적인 관리 기능을 제공합니다.  몇 가지 예는 다음과 같습니다.

* 운영 체제 업데이트의 [Azure Automation 업데이트 관리](../../automation/automation-update-management.md).
* 일관된 구성 상태를 유지 관리하는 [Azure Automation 필요한 상태 구성](../../automation/automation-dsc-overview.md).
* [Azure Automation 변경 내용 추적 및 인벤토리](../../automation/automation-change-tracking.md)를 사용하여 구성 변경 내용을 추적합니다.
* [Application Insights](https://docs.microsoft.com/azure/application-insights/) 및 [Azure Security Center](https://docs.microsoft.com/azure/security-center/)와 같은 Azure 서비스로서 기본적으로 Log Analytics에 직접 데이터를 저장합니다.  

## <a name="dependency-agent"></a>종속성 에이전트
종속성 에이전트는 원래 Microsoft에서 개발 되지 않은 서비스 맵 솔루션의 일부로 개발 되었습니다. [서비스 맵](../insights/service-map.md) 하 고 [Vm에 대 한 Azure Monitor](../insights/vminsights-overview.md) 종속성 에이전트가 필요 Windows 및 Linux virtual machines와 통합 됩니다 가상에서 실행 중인 프로세스에 대 한 검색 된 데이터를 수집 하도록 Log Analytics 에이전트 컴퓨터 및 외부 프로세스 종속성입니다. Log Analytics 작업 영역에서이 데이터를 저장 하 고 검색 된 상호 연결 된 구성 요소를 시각화 합니다.

VM을 모니터링하기 위해 이러한 에이전트의 몇 가지 조합이 필요할 수 있습니다. 에이전트를 Azure 확장으로 나란히 설치할 수 있지만 Linux에서는 Log Analytics 에이전트를 먼저 설치*해야*합니다. 그렇지 않으면 설치가 실패합니다. 

## <a name="next-steps"></a>다음 단계

- Azure, 데이터 센터 또는 기타 클라우드 환경에서 호스팅되는 컴퓨터에 에이전트를 배포하기 위한 요구 사항 및 지원되는 방법을 검토하려면 [Log Analytics 에이전트 개요](../../azure-monitor/platform/log-analytics-agent.md)를 참조하세요.

