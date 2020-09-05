---
title: VM용 Azure Monitor 사용 개요
description: VM용 Azure Monitor를 배포 하 고 구성 하는 방법을 알아봅니다. 시스템 요구 사항을 확인 합니다.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/27/2020
ms.openlocfilehash: 449979443577d22f8cc2ec35ec770dd1e107bb76
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88998410"
---
# <a name="enable-azure-monitor-for-vms-overview"></a>VM용 Azure Monitor 사용 개요

이 문서에서는 VM용 Azure Monitor에서 다음의 상태와 성능을 모니터링할 수 있도록 하는 데 사용할 수 있는 옵션의 개요를 제공 합니다.

- Azure 가상 머신 
- Azure 가상 머신 확장 집합
- Azure Arc와 연결 된 하이브리드 가상 머신
- 온-프레미스 가상 머신
- 다른 클라우드 환경에서 호스트 되는 가상 컴퓨터.  

VM용 Azure Monitor을 설정 하려면:

* Azure Portal의 메뉴에서 직접 **정보** 를 선택 하 여 단일 azure VM, AZURE vmss 또는 azure Arc 컴퓨터를 사용 하도록 설정 합니다.
* Azure Policy를 사용 하 여 여러 Azure Vm, Azure VMSS 또는 Azure Arc 컴퓨터를 사용 하도록 설정 합니다. 이 방법을 사용 하면 기존 및 새 Vm 및 확장 집합에서 필요한 종속성이 설치 되 고 올바르게 구성 됩니다. 비규격 Vm 및 크기 집합을 보고 하므로이를 사용 하도록 설정할지 여부를 결정할 수 있습니다.
* PowerShell을 사용 하 여 지정 된 구독 또는 리소스 그룹에서 여러 Azure Vm, Azure Arc Vm, Azure VMSS 또는 Azure Arc 컴퓨터를 사용 하도록 설정 합니다.
* VM용 Azure Monitor 사용 하 여 회사 네트워크 또는 다른 클라우드 환경에서 호스트 되는 Vm 또는 물리적 컴퓨터를 모니터링할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기 전에 다음 섹션의 정보를 이해해야 합니다. 

>[!NOTE]
>이 섹션에서 설명 하는 다음 정보는 [서비스 맵 솔루션](service-map.md)에도 적용 됩니다.  

### <a name="log-analytics"></a>Log Analytics

VM용 Azure Monitor는 다음 지역에서 Log Analytics 작업 영역을 지원 합니다.

- 미국 중서부
- 미국 서부
- 미국 서부 2
- 미국 중남부
- 미국 동부
- 미국 동부2
- 미국 중부
- 미국 중북부
- US Gov Az
- US Gov Va
- 캐나다 중부
- 영국 남부
- 북유럽
- 서유럽
- 동아시아
- 동남아시아
- 인도 중부
- 일본 동부
- 오스트레일리아 동부
- 오스트레일리아 남동부

>[!NOTE]
>모든 지역에서 Azure Vm을 모니터링할 수 있습니다. Vm 자체는 Log Analytics 작업 영역에서 지원 되는 지역으로 제한 되지 않습니다.
>

Log Analytics 작업 영역이 없는 경우 리소스 중 하나를 사용 하 여 작업 영역을 만들 수 있습니다.
* [Azure CLI](../learn/quick-create-workspace-cli.md)
* [PowerShell](../platform/powershell-workspace-configuration.md)
* [Azure Portal](../learn/quick-create-workspace.md)
* [Azure Resource Manager](../platform/template-workspace-configuration.md)

- Azure 가상 머신
- Azure 가상 머신 확장 집합
- Azure Arc와 연결 된 하이브리드 가상 컴퓨터

## <a name="supported-operating-systems"></a>지원되는 운영 체제

VM용 Azure Monitor는 Log Analytics 에이전트 및 종속성 에이전트를 지 원하는 모든 운영 체제를 지원 합니다. 전체 목록은 [Azure Monitor 에이전트 개요 ](../platform/agents-overview.md#supported-operating-systems) 를 참조 하세요.

VM용 Azure Monitor를 지 원하는 종속성 에이전트의 Linux 지원에 대 한 다음 고려 사항 목록을 참조 하세요.

- 기본 및 SMP Linux 커널 릴리스만 지원됩니다.
- PAE(Physical Address Extension) 및 Xen과 같은 비표준 커널 릴리스는 Linux 배포판에 대해 지원되지 않습니다. 예를 들어 *2.6.16.21-0.8-xen*의 릴리스 문자열이 있는 시스템은 지원되지 않습니다.
- 표준 커널 다시 컴파일이 포함 된 사용자 지정 커널은 지원 되지 않습니다.
- 9.4 버전 이외의 Debian 배포판에서는 맵 기능이 지원 되지 않으며 성능 기능은 Azure Monitor 메뉴 에서만 사용할 수 있습니다. Azure VM의 왼쪽 창에서 직접 사용할 수 없습니다.
- CentOSPlus 커널이 지원 됩니다.
- 스펙터 취약성에 대해 Linux 커널의 패치를 적용 해야 합니다. 자세한 내용은 Linux 배포 공급 업체에 문의 하세요.



## <a name="supported-azure-arc-machines"></a>지원 되는 Azure Arc 컴퓨터
VM용 Azure Monitor는 Arc 확장 서비스를 사용할 수 있는 지역에서 Azure Arc 사용 서버에 사용할 수 있습니다. Arc 에이전트 버전 0.9 이상을 실행 해야 합니다.

| 연결된 원본 | 지원됨 | Description |
|:--|:--|:--|
| Windows 에이전트 | 예 | [Windows에 대 한 Log Analytics 에이전트](../platform/log-analytics-agent.md)와 함께 windows 에이전트에는 종속성 에이전트가 필요 합니다. 자세한 내용은 [지원 되는 운영 체제](../platform/agents-overview.md#supported-operating-systems)를 참조 하세요. |
| Linux 에이전트 | 예 | Linux [에 대 한 Log Analytics 에이전트](../platform/log-analytics-agent.md)와 함께 linux 에이전트에는 종속성 에이전트가 필요 합니다. 자세한 내용은 [지원 되는 운영 체제](#supported-operating-systems)를 참조 하세요. |
| System Center Operations Manager 관리 그룹 | 아니요 | |

## <a name="agents"></a>에이전트
VM용 Azure Monitor 하려면 모니터링할 가상 컴퓨터 또는 가상 컴퓨터 확장 집합에 다음 두 에이전트를 설치 해야 합니다. 이러한 에이전트를 설치 하 고 작업 영역에 연결 하는 것은 리소스를 등록 하기 위한 유일한 요구 사항입니다.

- [에이전트를 Log Analytics](../platform/log-analytics-agent.md)합니다. 가상 머신 또는 가상 머신 확장 집합에서 이벤트 및 성능 데이터를 수집 하 여 Log Analytics 작업 영역으로 전달 합니다. Azure 리소스의 Log Analytics 에이전트에 대 한 배포 방법에서는 [Windows](../../virtual-machines/extensions/oms-windows.md) 및 [Linux](../../virtual-machines/extensions/oms-linux.md)용 VM 확장을 사용 합니다.
- 종속성 에이전트. [VM용 Azure Monitor의 맵 기능](vminsights-maps.md)에서 사용 되는 가상 컴퓨터 및 외부 프로세스 종속성에서 실행 중인 프로세스에 대 한 검색 된 데이터를 수집 합니다. 종속성 에이전트는 Log Analytics 에이전트를 사용 하 여 데이터를 Azure Monitor으로 배달 합니다. Azure 리소스에 대 한 종속성 에이전트의 배포 방법에서는 [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 및 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md)용 VM 확장을 사용 합니다.

> [!NOTE]
> Log Analytics 에이전트는 System Center Operations Manager에서 사용 하는 것과 동일한 에이전트입니다. Operations Manager 직접 연결 된 경우에도 모니터링 하는 에이전트를 모니터링 하 고 해당 에이전트에 종속성 에이전트를 설치할 수 VM용 Azure Monitor. [관리 그룹 연결](../tform/../platform/om-agents.md) 을 통해 Azure Monitor에 연결 된 에이전트는 VM용 Azure Monitor에서 모니터링할 수 없습니다.

다음은 이러한 에이전트를 배포 하는 여러 가지 방법입니다. 

| 방법 | Description |
|:---|:---|
| [Azure Portal](./vminsights-enable-portal.md) | 단일 가상 머신, 가상 머신 확장 집합 또는 Azure Arc와 연결 된 하이브리드 가상 머신에 두 에이전트를 모두 설치 합니다. |
| [리소스 관리자 템플릿](vminsights-enable-powershell.md) | 지원 되는 방법 중 하나를 사용 하 여 CLI 및 PowerShell을 비롯 한 리소스 관리자 템플릿을 배포 하는 두 에이전트를 모두 설치 합니다. |
| [Azure Policy](./vminsights-enable-policy.md) | 가상 머신 또는 가상 머신 확장 집합을 만들 때 에이전트를 자동으로 설치 하도록 Azure Policy 이니셔티브를 할당 합니다. |
| [수동 설치](./vminsights-enable-hybrid.md) | 데이터 센터 또는 다른 클라우드 환경을 포함 하 여 Azure 외부에 호스트 된 컴퓨터에 게스트 운영 체제에 에이전트를 설치 합니다. |




## <a name="management-packs"></a>관리 팩
VM용 Azure Monitor에 대해 Log Analytics 작업 영역을 구성한 경우 해당 작업 영역에 연결 된 모든 Windows 컴퓨터에 두 개의 관리 팩이 전달 됩니다. 관리 팩의 이름은 *microsoft.intelligencepacks.updateassessment. ApplicationDependencyMonitor* 및 *microsoft.intelligencepacks.updateassessment입니다* .이 관리 팩은 *%Programfiles%\Microsoft 모니터링 Agent\Agent\Health Service State\agst\stst\stst\styyyyyyyy\ \* 

*ApplicationDependencyMonitor* 관리 팩에서 사용 하는 데이터 원본은 **% Program files%\Microsoft Monitoring \Agent\health Service state\resources \<AutoGeneratedID>\Microsoft.EnterpriseManagement.Advisor.ApplicationDependencyMonitorDataSource.dll*입니다. *VMInsights* 관리 팩에서 사용 하는 데이터 원본은 *% Program files%\Microsoft Monitoring \Agent\health Service state\sources\ \<AutoGeneratedID> Microsoft.VirtualMachineMonitoringModule.dll*입니다.

## <a name="diagnostic-and-usage-data"></a>진단 및 사용량 현황 데이터

Microsoft는 Azure Monitor 서비스를 사용하여 사용 현황 및 성능 데이터를 자동으로 수집합니다. Microsoft는이 데이터를 사용 하 여 서비스의 품질, 보안 및 무결성을 향상 시킵니다. 

정확 하 고 효율적인 문제 해결 기능을 제공 하기 위해 지도 기능에는 소프트웨어의 구성에 대 한 데이터가 포함 됩니다. 데이터는 운영 체제, 버전, IP 주소, DNS 이름 및 워크스테이션 이름과 같은 정보를 제공 합니다. 이름, 주소 또는 기타 연락처 정보는 수집하지 않습니다.

데이터 수집 및 사용에 대한 자세한 내용은 [Microsoft Online Services 개인정보처리방침](https://go.microsoft.com/fwlink/?LinkId=512132)을 참조하세요.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>다음 단계

성능 모니터링 기능을 사용 하는 방법에 대 한 자세한 내용은 [VM용 Azure Monitor 성능 보기](vminsights-performance.md)를 참조 하세요. 검색된 애플리케이션 종속성을 보려면 [VM용 Azure Monitor 맵 보기](vminsights-maps.md)를 참조하세요.