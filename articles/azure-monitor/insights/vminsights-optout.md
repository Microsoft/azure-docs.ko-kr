---
title: VM용 Azure Monitor(미리 보기)를 사용하여 모니터링을 사용하지 않도록 설정하는 방법 | Microsoft Docs
description: 이 문서에서는 VM용 Azure Monitor를 사용하여 가상 머신의 모니터링을 중단하는 방법을 설명합니다.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: c6a8322998b391b9021292a3a7213ded56b67feb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61386447"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>VM용 Azure Monitor(미리 보기)를 사용하여 가상 머신의 모니터링을 사용하지 않도록 설정하는 방법

가상 머신의 모니터링을 사용하도록 설정한 후 VM용 Azure Monitor를 사용하여 해당 가상 머신을 더 이상 모니터링하지 않기로 한 경우 모니터링을 사용하지 않도록 설정할 수 있습니다. 이 문서에서는 단일 또는 여러 VM에 대해 이 작업을 수행하는 방법을 보여줍니다.  

현재 VM용 Azure Monitor는 VM의 모니터링을 선택적으로 비활성화하는 방법을 지원하지 않습니다. 이 솔루션 및 기타 솔루션을 지원할 뿐만 아니라 기타 모니터링 데이터를 수집하도록 Log Analytics 작업 영역을 구성하는 경우 계속 진행하기 전에 아래에 설명된 영향 및 메서드를 이해하는 것이 중요합니다.

VM용 Azure Monitor는 해당 환경을 제공하기 위해 다음 구성 요소를 사용합니다.

* VM 및 기타 원본에서 수집한 모니터링 데이터를 저장하는 Log Analytics 작업 영역.
* 작업 영역에 연결하는 모든 VM의 모니터링 구성을 업데이트하는 작업 영역에서 구성된 성능 카운터의 컬렉션.
* 작업 영역에서 구성된 두 개의 모니터링 솔루션 - 작업 영역에 연결된 모든 VM의 모니터링 구성을 업데이트하는 **InfrastructureInsights** 및 **ServiceMap**.
* 데이터를 수집해 작업 영역에 전송하는 두 Azure 가상 머신 확장 **MicrosoftMonitoringAgent** 및 **DependencyAgent**.

VM용 Azure Monitor(미리 보기)를 사용하여 가상 머신의 모니터링을 비활성화하도록 준비하는 경우 다음을 고려합니다.

* 단일 VM으로 평가하고 미리 선택된 기본 Log Analytics 작업 영역을 허용한 경우 VM에서 종속성 에이전트를 제거하고 이 작업 영역에서 Log Analytics 에이전트의 연결을 해제하여 모니터링을 비활성화할 수 있습니다. 이 접근 방식은 다른 용도로 VM을 사용하고 나중에 다른 작업 영역에 VM을 다시 연결하려는 경우에 적합합니다.
* 기타 원본에서 데이터 컬렉션 및 기타 모니터링 솔루션을 지원하기 위해 Log Analytics 작업 영역을 사용하고 있는 경우 작업 영역을 중단하거나 작업 영역에 영향을 주지 않고 작업 영역에서 VM용 Azure Monitor 솔루션 구성을 제거할 수 있습니다.  

>[!NOTE]
> 작업 영역에서 솔루션 구성 요소를 제거한 후에는 Azure VM의 성능 상태, 특히 포털에서 보기로 이동할 경우 성능 및 맵 데이터를 계속 확인할 수 있습니다. 결국 데이터는 잠시 후 성능 및 맵 보기에서 표시되지 않지만 상태 보기는 VM에 대한 성능 상태를 계속 표시합니다. **지금 시도** 옵션은 향후 모니터링을 다시 활성화할 수 있도록 선택한 Azure VM에서 사용할 수 있습니다.  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>VM용 Azure Monitor의 완전한 제거

다음 단계에서는 Log Analytics 작업 영역이 계속 필요한 경우 VM용 Azure Monitor를 완전히 제거하는 방법을 설명합니다. 작업 영역에서 **InfrastructureInsights** 및 **ServiceMap** 솔루션을 제거하도록 하겠습니다.  

>[!NOTE]
>VM용 Azure Monitor를 사용하도록 설정하기 전에 서비스 맵 모니터링 솔루션을 여전히 사용하는 경우 아래 6단계에 설명된 대로 해당 솔루션을 제거하지 마세요.  
>

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. Azure Portal에서 **모든 서비스**를 클릭합니다. 리소스 목록에서 Log Analytics를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Log Analytics**를 선택합니다.
3. Log Analytics 작업 영역 목록에서 VM용 Azure Monitor를 온보딩할 때 선택한 작업 영역을 선택합니다.
4. 왼쪽 창에서 **솔루션**을 선택합니다.  
5. 솔루션 목록에서 **InfrastructureInsights(작업 영역 이름)** 를 선택한 다음, 솔루션에 대한 **개요** 페이지에서 **삭제**를 클릭합니다.  확인하라는 메시지가 나타나면 **예**를 클릭합니다.  
6. 솔루션 목록에서 **ServiceMap(작업 영역 이름)** 을 선택한 다음, 솔루션에 대한 **개요** 페이지에서 **삭제**를 클릭합니다.  확인하라는 메시지가 나타나면 **예**를 클릭합니다.  

VM용 Azure Monitor를 온보딩하기 전에 작업 영역에서 Windows 또는 Linux 기반 VM에 [사용하도록 설정된 성능 카운터를 수집](vminsights-onboard.md?toc=/azure/azure-monitor/toc.json#performance-counters-enabled)하지 않는 경우 [여기](../../azure-monitor/platform/data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json#configuring-performance-counters)에 설명된 단계를 따라 Linux 및 Windows에 대한 해당 규칙을 사용하지 않도록 설정해야 합니다.

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>Azure VM에 대한 모니터링을 사용하지 않도록 설정하고 작업 영역 보존  

다음 단계에서는 VM용 Azure Monitor를 평가하도록 설정된 가상 머신에 대한 모니터링을 사용하지 않도록 설정하는 방법을 설명합니다. 그러나 Log Analytics 작업 영역은 다른 원본에서 모니터링을 여전히 지원해야 합니다. Azure VM인 경우 해당 VM에서 직접 Windows/linux용 종속성 에이전트 VM 확장 및 Log Analytics 에이전트 VM 확장을 제거해야 합니다. 

>[!NOTE]
>프로세스를 오케스트레이션하거나 구성을 관리하거나 업데이트를 관리하기 위해 가상 머신을 Azure Automation에서 관리하거나, 보안 관리 및 위협 감지를 위해 Azure Security Center에서 관리하는 경우 Log Analytics 에이전트를 제거하지 말아야 합니다. 그렇지 않은 경우 해당 서비스 및 솔루션이 사전에 VM을 관리하는 것을 방지합니다. 

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다. 
2. Azure Portal에서 **Virtual Machines**를 선택합니다. 
3. 목록에서 VM을 선택합니다. 
4. 왼쪽 창에서 **확장**을 선택하고 **확장** 페이지에서 **DependencyAgent**를 선택합니다.
5. 확장 속성 페이지에서 **제거**를 클릭합니다.
6. **확장** 페이지에서 **MicrosoftMonitoringAgent**를 선택하고 확장 속성 페이지에서 **제거**를 클릭합니다.  
