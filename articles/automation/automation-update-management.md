---
title: Azure의 업데이트 관리 솔루션
description: 이 문서는 Azure 업데이트 관리 솔루션을 사용하여 Windows 및 Linux 컴퓨터에 대한 업데이트를 관리하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: update-management
author: georgewallace
ms.author: gwallace
ms.date: 04/22/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: f49b8ef3717675ae6d93d07218a00f2c22890de0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61306534"
---
# <a name="update-management-solution-in-azure"></a>Azure의 업데이트 관리 솔루션

Azure, 온-프레미스 환경 또는 다른 클라우드 공급자에서 Windows 및 Linux 컴퓨터에 대 한 운영 체제 업데이트를 관리 하려면 Azure Automation에서 업데이트 관리 솔루션을 사용할 수 있습니다. 모든 에이전트 컴퓨터에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버에 대한 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다.

Azure Automation 계정에서 직접 가상 머신에 업데이트 관리를 사용하도록 설정할 수 있습니다. Automation 계정에서 가상 머신에 업데이트 관리를 사용하는 방법을 알아보려면 [여러 가상 머신에 대한 업데이트 관리](manage-update-multi.md)를 참조하세요. Azure Portal의 가상 머신 페이지에서 가상 머신에 대한 업데이트 관리를 사용할 수도 있습니다. 이 시나리오는 [Linux](../virtual-machines/linux/tutorial-monitoring.md#enable-update-management) 및 [Windows](../virtual-machines/windows/tutorial-monitoring.md#enable-update-management) 가상 머신에서 지원됩니다.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="solution-overview"></a>솔루션 개요

업데이트 관리를 통해 관리되는 컴퓨터는 다음 구성을 사용하여 평가 및 업데이트 배포를 수행합니다.

* Windows 또는 Linux용 MMA(Microsoft Monitoring Agent)
* Linux용 PowerShell DSC(필요한 상태 구성)
* Automation Hybrid Runbook Worker
* Windows 컴퓨터용 Microsoft Update 또는 WSUS(Windows Server Update Services)

다음 다이어그램에서는 솔루션이 작업 영역에서 보안 업데이트를 평가하고, 연결된 모든 Windows Server 및 Linux 컴퓨터에 적용하는 방식과 관련된 동작 및 데이터 흐름을 개념적으로 보여 줍니다.

![업데이트 관리 프로세스 흐름](./media/automation-update-management/update-mgmt-updateworkflow.png)

업데이트 관리는 동일한 테넌트의 여러 구독에 있는 머신을 기본적으로 등록하는 데 사용할 수 있습니다.

CVE가 릴리스되면 평가할 Linux 머신에 대한 패치가 나타날 때까지 2~3시간이 걸립니다.  Windows 머신의 경우 릴리스된 후 평가할 패치가 나타날 때까지 12-15시간이 걸립니다.

컴퓨터에 업데이트 준수 검사가 완료 되 면 에이전트를 대량으로 Azure Monitor 로그의 정보를 전달 합니다. Windows 컴퓨터는 기본적으로 12시간마다 준수 검사가 실행됩니다.

검사 일정 외에도, MMA가 다시 시작되면 업데이트 설치 전과 업데이트 설치 후 15분 이내에 업데이트 준수 검사가 시작됩니다.

Linux 컴퓨터에서 준수 검사는 기본적으로 3시간마다 수행됩니다. MMA 에이전트가 다시 시작되면 15분 이내에 준수 검사가 시작됩니다.

솔루션은 동기화하도록 구성된 소스를 기반으로 컴퓨터가 얼마나 최신 상태인지를 보고합니다. Windows 컴퓨터가 WSUS에 보고하도록 구성된 경우 WSUS가 Microsoft Update와 마지막으로 동기화된 시기에 따라 그 결과는 Microsoft Update가 표시하는 것과 다를 수 있습니다. 이 동작은 공용 리포지토리 대신 로컬 리포지토리에 보고하도록 구성된 Linux 컴퓨터에서도 동일합니다.

> [!NOTE]
> 서비스에 제대로 보고하려면 업데이트 관리를 수행할 때 특정 URL 및 포트가 사용되도록 설정되어야 합니다. 이러한 요구 사항에 대한 자세한 내용은 [Hybrid Worker에 대한 네트워크 계획](automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

예약 배포를 만들어서 업데이트가 필요한 컴퓨터에 소프트웨어 업데이트를 배포하고 설치할 수 있습니다. *선택 사항*으로 분류된 업데이트는 Windows 컴퓨터의 배포 범위에 포함되지 않습니다. 배포 범위에는 필수 업데이트만 포함됩니다.

예약 배포는 컴퓨터를 명시적으로 지정하거나 특정 컴퓨터 집합의 로그 검색을 기반으로 하는 [컴퓨터 그룹](../azure-monitor/platform/computer-groups.md)을 선택하여 해당 업데이트를 받는 대상 컴퓨터를 정의합니다. 또한 업데이트를 설치할 수 있는 기간을 승인 및 설정하는 일정을 지정합니다. 이 기간에는 유지 관리 기간을 이라고 합니다. 유지 관리 기간이 10 분 동안 다시 부팅 해야 하 고 적절 한 다시 부팅 옵션을 선택한 경우 다시 부팅 예약 되어 있습니다. 예상 보다 오래 걸리는 패치 및 유지 관리 기간에 10 분 이내에 다시 부팅이 발생 하지 않습니다.

Azure Automation의 runbook에서 업데이트가 설치됩니다. 이러한 Runbook은 볼 수 없으며 구성이 필요하지 않습니다. 업데이트 배포가 생성되면 업데이트 배포는 포함된 컴퓨터에 대해 지정된 시간에 마스터 업데이트 Runbook을 시작하는 일정을 만듭니다. 이 마스터 Runbook은 각 에이전트에서 하위 Runbook을 시작하여 필수 업데이트를 설치합니다.

업데이트 배포에 지정된 날짜 및 시간에 대상 컴퓨터는 배포를 병렬로 실행합니다. 설치 전에, 업데이트가 여전히 필요한지 확인하기 위한 검사가 수행됩니다. WSUS 클라이언트 컴퓨터의 경우 업데이트가 WSUS에서 승인되지 않으면 업데이트 배포가 실패합니다.

둘 이상의 Log Analytics 작업 영역 (멀티 호 밍)에서 업데이트 관리를 위해 등록 하는 컴퓨터에 있는 지원 되지 않습니다.

## <a name="clients"></a>클라이언트

### <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에서는 지원되는 운영 체제의 목록을 보여 줍니다.

|운영 체제  |메모  |
|---------|---------|
|Windows Server 2008, Windows Server 2008 R2 RTM    | 업데이트 평가만 지원합니다.         |
|Windows Server 2008 R2 SP1 이상(Windows Server 2012 및 2016 포함)    |.NET Framework 4.5.1 이상이 필요합니다. ([.NET Framework 다운로드](/dotnet/framework/install/guide-for-developers))<br/> Windows PowerShell 4.0 이상이 필요합니다. ([WMF 4.0 다운로드](https://www.microsoft.com/download/details.aspx?id=40855))<br/> Windows PowerShell 5.1은 안정성 개선을 위해 필요합니다.  ([WMF 5.1 다운로드](https://www.microsoft.com/download/details.aspx?id=54616))        |
|CentOS 6(x86/x64) 및 7(x64)      | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다. 분류 기반 패치에는 CentOS에 기본 제공되지 않은 보안 데이터를 반환하기 위해 'yum'이 필요합니다. CentOS에서 패치 분류 기반에 대 한 자세한 내용은 참조 하세요. [Linux 분류 업데이트](#linux-2)          |
|Red Hat Enterprise 6(x86/x64) 및 7(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)     | Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.        |
|Ubuntu 14.04 LTS, 16.04 LTS 및 18.04(x86/x64)      |Linux 에이전트에는 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다.         |

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

다음 표에는 지원되지 않는 운영 체제가 나열되어 있습니다.

|운영 체제  |메모  |
|---------|---------|
|Windows 클라이언트     | 클라이언트 운영 체제(예: Windows 7 및 Windows 10)는 지원되지 않습니다.        |
|Windows Server 2016 Nano Server     | 지원되지 않습니다.       |

### <a name="client-requirements"></a>클라이언트 요구 사항

#### <a name="windows"></a>Windows

Windows 에이전트는 WSUS 서버와 통신하도록 구성되거나 Microsoft 업데이트에 대한 액세스 권한을 가지고 있어야 합니다. System Center Configuration Manager에서 업데이트 관리를 사용할 수 있습니다. 통합 시나리오에 대한 자세한 내용은 [업데이트 관리와 System Center Configuration Manager 통합](oms-solution-updatemgmt-sccmintegration.md#configuration)을 참조하세요. [Windows 에이전트](../azure-monitor/platform/agent-windows.md)가 필요합니다. 이 에이전트는 Azure Virtual Machine을 등록하는 경우 자동으로 설치됩니다.

#### <a name="linux"></a>Linux

Linux의 경우 컴퓨터에 업데이트 리포지토리에 대한 액세스 권한이 있어야 합니다. 업데이트 리포지토리는 사설 또는 공용일 수 있습니다. 업데이트 관리와 상호 작용하는 데 TLS 1.1 또는 TLS 1.2가 필요합니다. 이 솔루션은 여러 Azure Log Analytics 작업 영역에 보고하도록 구성된 Linux용 Log Analytics 에이전트를 지원하지 않습니다.

Linux 용 Log Analytics 에이전트를 설치 하 고 최신 버전을 다운로드 하는 방법에 대 한 정보를 참조 하세요 [Linux 용 Log Analytics 에이전트](https://github.com/microsoft/oms-agent-for-linux)합니다. Windows Log Analytics 에이전트를 설치 하는 방법에 대 한 정보를 참조 하세요 [Microsoft 모니터링 에이전트에 대 한 Windows](../log-analytics/log-analytics-windows-agent.md)합니다.

## <a name="permissions"></a>권한

업데이트 배포를 만들고 관리하려면 특정 권한이 필요합니다. 이러한 권한에 대한 내용은 [역할 기반 액세스 - 업데이트 관리](automation-role-based-access-control.md#update-management)를 참조하세요.

## <a name="solution-components"></a>솔루션 구성 요소

솔루션은 다음 리소스로 이루어져 있습니다. 리소스는 Automation 계정에 추가됩니다. 이러한 리소스는 직접 연결된 에이전트이거나 Operations Manager에 연결된 관리 그룹에 속합니다.

### <a name="hybrid-worker-groups"></a>Hybrid Worker 그룹

이 솔루션을 사용하도록 설정하면 이 솔루션에 포함된 Runbook을 지원하기 위해 Log Analytics 작업 영역에 직접 연결된 모든 Windows 컴퓨터가 자동으로 Hybrid Runbook Worker로 구성됩니다.

솔루션에서 관리되는 각 Windows 컴퓨터의 경우 Automation 계정에 대한 **시스템 하이브리드 작업자 그룹**으로 **Hybrid Worker 그룹** 창에 나열됩니다. 솔루션은 명명 규칙 *Hostname FQDN_GUID*를 사용합니다. 계정에서 Runbook을 사용하여 이러한 그룹을 대상으로 지정할 수 없습니다. 이렇게 하려고 하면 실패합니다. 이러한 그룹은 관리 솔루션을 지원하는 용도로만 사용할 수 있습니다.

솔루션과 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 경우, Automation Runbook을 지원하기 위해 Automation 계정의 Hybrid Runbook Worker 그룹에 Windows 컴퓨터를 추가할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

### <a name="management-packs"></a>관리 팩

System Center Operations Manager 관리 그룹이 Log Analytics 작업 영역에 연결되면 다음 관리 팩이 Operations Manager에 설치됩니다. 이 솔루션을 추가한 후 직접 연결된 Windows 컴퓨터에 이러한 관리 팩도 함께 설치됩니다. 이러한 관리 팩을 구성하거나 관리할 필요가 없습니다.

* Microsoft System Center Advisor 업데이트 평가 인텔리전스 팩(Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration(Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 업데이트 배포 MP

> [!NOTE]
> 재정의에 경우 Operations Manager 1807 관리 그룹 작업 영역에 연결할 관리 그룹 수준에서 구성 하는 에이전트를 사용 하 여 표시 되도록 어셈블리를 가져오려고 현재 문제를 해결 **IsAutoRegistrationEnabled** 를 **True** 에 **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** 규칙입니다.

솔루션 관리 팩은 업데이트 하는 방법에 대 한 자세한 내용은 참조 하십시오 [Azure Monitor에 Operations Manager 연결 로그](../azure-monitor/platform/om-agents.md)합니다.

> [!NOTE]
> Operations Manger 에이전트가 있는 시스템을 업데이트 관리로 완전히 관리할 수 있으려면 에이전트를 Microsoft Monitoring Agent로 업데이트해야 합니다. 에이전트를 업데이트하는 방법을 알아보려면 [Operations Manager 에이전트를 업그레이드하는 방법](https://docs.microsoft.com/system-center/scom/deploy-upgrade-agents)을 참조하세요. Operations Manager를 사용 하 여 환경에 대 한 필요는 14 이상 System Center Operations Manager 2012 R2 UR 실행 합니다.

## <a name="onboard"></a>업데이트 관리 사용

시스템 패치를 시작하려면 업데이트 관리 솔루션을 사용하도록 설정해야 합니다. 다양한 방법으로 업데이트 관리에 컴퓨터를 등록할 수 있습니다. 아래에는 권장/지원되는 솔루션 등록 방법이 나와 있습니다.

* [가상 머신에서 등록](automation-onboard-solutions-from-vm.md)
* [여러 컴퓨터에서 찾아보기](automation-onboard-solutions-from-browse.md)
* [Automation 계정에서 등록](automation-onboard-solutions-from-automation-account.md)
* [Azure Automation Runbook 사용](automation-onboard-solutions.md)
  
### <a name="confirm-that-non-azure-machines-are-onboarded"></a>비 Azure 컴퓨터가 등록되어 있는지 확인

몇 분 후 컴퓨터 직접 연결 된 Azure Monitor 로그를 사용 하 여 통신 하는지 확인 하려면 수 하나를 실행 하면 다음 로그 검색 합니다.

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

Windows 컴퓨터에서 Azure Monitor 로그를 사용 하 여 에이전트가 연결을 확인 하려면 다음 정보를 검토할 수 있습니다.

1. 제어판에서 **Microsoft Monitoring Agent**를 엽니다. **Azure Log Analytics** 탭에서 에이전트가 다음 메시지를 표시합니다. **Microsoft Monitoring Agent가 Microsoft Log Analytics 서비스에 성공적으로 연결되었습니다.**
2. Windows 이벤트 로그를 엽니다. **애플리케이션 및 서비스 로그\Operations Manager**로 이동한 후, 원본 **서비스 커넥터**에서 이벤트 ID 3000 및 이벤트 ID 5002를 검색합니다. 이러한 이벤트는 컴퓨터가 Log Analytics 작업 영역에 등록되었으며 구성을 수신하고 있음을 나타냅니다.

에이전트가 통신할 수 없는 경우 Azure Monitor 로그 및 에이전트는 방화벽 또는 프록시 서버를 통해 인터넷과 통신, 방화벽을 확인 하도록 구성 된 또는 프록시 서버가 올바르게 구성 되어 있습니다. 방화벽 또는 프록시 서버가 올바르게 구성되어 있는지 확인하는 방법을 알아보려면 [Windows 에이전트에 대한 네트워크 구성](../azure-monitor/platform/agent-windows.md) 또는 [Linux 에이전트에 대한 네트워크 구성](../log-analytics/log-analytics-agent-linux.md)을 참조하세요.

> [!NOTE]
> Linux 시스템에서 프록시 또는 Log Analytics 게이트웨이와 통신하도록 구성되고 이 솔루션을 등록하는 경우, 다음 명령을 수행하여 omiuser 그룹 읽기 권한을 파일에 부여하도록 *proxy.conf* 권한을 업데이트하세요.
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

평가가 수행된 후 새로 추가된 Linux 에이전트의 상태가 **업데이트됨**으로 표시됩니다. 이 프로세스는 최대 6시간까지 걸릴 수 있습니다.

Operations Manager 관리 그룹을 통신 하 고 있는 Azure Monitor 로그를 확인 하려면 참조 [Azure Monitor 로그를 사용 하 여 유효성을 검사 하는 Operations Manager 통합](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)합니다.

## <a name="data-collection"></a>데이터 수집

### <a name="supported-agents"></a>지원되는 에이전트

다음 표는 이 솔루션이 지원하는 연결된 원본을 설명합니다.

| 연결된 원본 | 지원됨 | 설명 |
| --- | --- | --- |
| Windows 에이전트 |예 |솔루션은 Windows 에이전트에서 시스템 업데이트에 대한 정보를 수집한 후 필수 업데이트를 설치하기 시작합니다. |
| Linux 에이전트 |예 |이 솔루션은 Linux 에이전트에서 시스템 업데이트에 대한 정보를 수집하고 지원되는 배포판에서 필수 업데이트 설치를 시작합니다. |
| Operations Manager 관리 그룹 |예 |솔루션은 연결된 관리 그룹의 에이전트에서 시스템 업데이트에 대한 정보를 수집합니다.<br/>Operations Manager 에이전트에서 Azure Monitor 로그로 직접 연결은 필요 하지 않습니다. 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다. |

### <a name="collection-frequency"></a>수집 빈도

관리되는 Windows 컴퓨터 각각에서 하루에 두 번 검사가 수행됩니다. 15분마다 Windows API가 호출되어 마지막 업데이트 시간을 쿼리하고 상태가 변경되었는지 확인합니다. 상태가 변경되었으면 준수 검사가 시작됩니다.

관리되는 Linux 컴퓨터 각각에서 3시간마다 검사가 수행됩니다.

관리되는 컴퓨터의 업데이트 데이터를 대시보드에 표시하는 데 30분에서 6시간이 걸릴 수 있습니다.

업데이트 관리를 사용 하는 컴퓨터에 대 한 Azure Monitor 로그 데이터 사용량 평균은 매월 약 25MB입니다. 이 값은 근사값이며, 사용자 환경에 따라 변경될 수 있습니다. 사용자 환경을 모니터링하여 정확한 사용량을 확인하는 것이 좋습니다.

## <a name="viewing-update-assessments"></a>업데이트 평가 보기

Automation 계정에서 **업데이트 관리**를 선택하여 컴퓨터 상태를 확인합니다.

이 보기에서는 컴퓨터, 누락 업데이트, 업데이트 배포 및 예약된 업데이트 배포에 대한 정보를 제공합니다. **준수 열**에서 컴퓨터를 마지막으로 평가한 시간을 확인할 수 있습니다. **에이전트 업데이트 준비** 열에서 에이전트 업데이트의 상태를 확인할 수 있습니다. 문제가 있는 경우 링크를 선택하여 문제를 해결하기 위해 수행할 단계를 알려주는 문제 해결 설명서로 이동합니다.

컴퓨터, 업데이트 또는 배포에 대한 정보를 반환하는 로그 검색을 실행하려면 목록에서 항목을 선택합니다. 이렇게 하면 선택한 항목에 대한 쿼리가 있는 **로그 검색** 창이 열립니다.

![업데이트 관리 기본 보기](media/automation-update-management/update-management-view.png)

## <a name="install-updates"></a>업데이트 설치

작업 영역의 모든 Linux 및 Windows 컴퓨터에 대한 업데이트 평가가 완료된 후에는 *업데이트 배포*를 만들어서 필수 업데이트를 설치할 수 있습니다. 업데이트 배포를 만들려면 Automation 계정에 대 한 쓰기 액세스 및 배포의 대상으로 하는 모든 Azure Vm에 대 한 쓰기 액세스 해야 합니다. 업데이트 배포는 예약된 일정에 따라 하나 이상의 컴퓨터에 필요한 업데이트를 설치합니다. 배포 날짜 및 시간과 배포 범위에 포함할 컴퓨터 또는 컴퓨터 그룹을 지정합니다. 컴퓨터 그룹에 대 한 자세한 내용은 참조 하세요 [Azure Monitor 로그의 컴퓨터 그룹](../azure-monitor/platform/computer-groups.md)합니다.

업데이트 배포에 컴퓨터 그룹이 포함되면 일정을 만들 때 그룹 멤버 자격이 한 번만 평가됩니다. 이후의 그룹 변경 내용은 반영되지 않습니다. 이 사용 하이 여 해결 하기 위해 [동적 그룹](#using-dynamic-groups), 이러한 그룹은 배포 시 계산은 하며 Azure Vm 또는 비 Azure Vm에 대 한 저장된 된 검색에 대 한 쿼리에 의해 정의 됩니다.

> [!NOTE]
> Azure Marketplace에서 배포된 Windows 가상 머신은 기본적으로 Windows 업데이트 서비스에서 자동으로 업데이트를 받도록 설정됩니다. 이 동작은 이 솔루션을 추가하거나 작업 영역에 Windows 가상 머신을 추가해도 달라지지 않습니다. 이 솔루션을 사용하여 업데이트를 적극적으로 관리하지 않는 경우 기본 동작(업데이트 자동 적용)이 적용됩니다.

Ubuntu에서 유지 관리 기간 외에 업데이트가 적용되지 않도록 하려면 자동 업데이트를 사용하지 않도록 Unattended-Upgrade 패키지를 다시 구성합니다. 패키지 구성 방법에 대한 자세한 내용은 [Ubuntu Server 가이드의 자동 업데이트 항목](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)을 참조하세요.

Azure Marketplace에서 사용할 수 있는 RHEL(주문형 Red Hat Enterprise Linux) 이미지에서 만든 가상 머신은 Azure에 배포된 [RHUI(Red Hat Update Infrastructure)](../virtual-machines/virtual-machines-linux-update-infrastructure-redhat.md)에 액세스하도록 등록됩니다. 다른 모든 Linux 배포판은 지원되는 방법에 따라 배포판 온라인 파일 리포지토리에서 업데이트되어야 합니다.

새 업데이트 배포를 만들려면 **업데이트 배포 예약**을 선택합니다. 합니다 **새 업데이트 배포** 페이지가 열립니다. 다음 표에 설명된 속성의 값을 입력하고 **만들기**를 클릭합니다.

| 자산 | 설명 |
| --- | --- |
| Name |업데이트 배포를 식별하는 고유 이름입니다. |
|운영 체제| Linux 또는or Windows|
| 업데이트 그룹 |Azure 컴퓨터에 대 한 배포에 포함 하려면 Azure Vm의 동적 그룹을 만들려는 구독, 리소스 그룹, 위치 및 태그의 조합을 기준으로 쿼리를 정의 합니다. </br></br>비 Azure 컴퓨터에 대 한 기존 저장 된 배포에 포함 하는 비 Azure 컴퓨터의 그룹을 선택 하려면 검색을 선택 합니다. </br></br>자세한 내용은 [동적 그룹](automation-update-management.md#using-dynamic-groups)을 참조하세요.|
| 업데이트할 컴퓨터 |저장된 검색, 가져온 그룹을 선택하거나 드롭다운에서 머신을 선택하고 개별 머신을 선택합니다. **머신**을 선택한 경우 머신의 준비는 **업데이트 에이전트 준비** 열에 표시됩니다.</br> Azure Monitor 로그에서 컴퓨터 그룹을 만드는 다른 방법에 대해 알아보려면 [Azure Monitor 로그의 컴퓨터 그룹](../azure-monitor/platform/computer-groups.md)을 참조하세요. |
|업데이트 분류|필요한 모든 업데이트 분류를 선택합니다.|
|업데이트 포함/제외|**포함/제외** 페이지를 엽니다. 포함 또는 제외할 업데이트는 별도의 탭에 있습니다. 포함이 처리되는 방식에 대한 자세한 내용은 [포함 동작](automation-update-management.md#inclusion-behavior)을 참조하세요. |
|일정 설정|시작 시간을 선택하고 되풀이에 대해 [한 번] 또는 [정기]를 선택합니다.|
| 사전 스크립트 + 사후 스크립트|배포 전후에 실행할 스크립트를 선택합니다.|
| 유지 관리 기간 |업데이트에 대해 설정되는 시간(분)입니다. 값은 30분 이상 6시간 이하여야 합니다. |
| 다시 부팅 제어| 다시 부팅을 처리하는 방법을 결정합니다. 사용 가능한 옵션은 다음과 같습니다.</br>필요한 경우 다시 부팅(기본값)</br>항상 다시 부팅</br>다시 부팅 안 함</br>다시 부팅만 - 업데이트 설치 안 함|

업데이트 배포를 프로그래밍 방식으로 만들 수도 있습니다. REST API를 사용하여 업데이트 배포를 만드는 방법은 [소프트웨어 업데이트 구성 - 만들기](/rest/api/automation/softwareupdateconfigurations/create)를 참조하세요. 주간 업데이트 배포를 만드는 데 사용할 수 있는 샘플 Runbook도 있습니다. 이 Runbook에 대한 자세한 내용은 [리소스 그룹에 있는 하나 이상의 VM에 대한 주간 업데이트 배포 만들기](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)를 참조하세요.

### <a name="multi-tenant"></a> 테넌트 간 업데이트 배포

패치해야 하는 업데이트 관리에 대해 보고하는 또 다른 Azure 테넌트에 머신이 있는 경우 예약하려면 다음 해결 방법을 사용해야 합니다. `-ForUpdate` 스위치와 함께 [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet을 사용하여 일정을 만들고 [New-AzureRmAutomationSoftwareUpdateConfiguration](/powershell/module/azurerm.automation/new-azurermautomationsoftwareupdateconfiguration
) cmdlet을 사용한 후 다른 테넌트의 머신을 `-NonAzureComputer` 매개 변수에 전달할 수 있습니다. 다음 예제는 이 작업을 수행하는 방법의 예를 보여 줍니다.

```azurepowershell-interactive
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$sched = New-AzureRmAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdate

New-AzureRmAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName <automationAccountName> -Schedule $sched -Windows -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="view-missing-updates"></a>누락 업데이트 보기

**누락 업데이트**를 선택하여 컴퓨터에서 누락된 업데이트 목록을 표시합니다. 각 업데이트가 나열되며 선택 가능합니다. 업데이트가 필요한 컴퓨터 수, 운영 체제 및 세부 정보에 대한 링크와 관련된 정보가 표시됩니다. **로그 검색** 창에 업데이트에 대한 자세한 정보가 표시됩니다.

## <a name="view-update-deployments"></a>업데이트 배포 보기

**업데이트 배포** 탭을 선택하여 기존 업데이트 배포의 목록을 볼 수 있습니다. 테이블에서 업데이트 배포 중 하나를 선택하면 해당 업데이트 배포에 대한 **업데이트 배포 실행** 페이지가 열립니다.

![업데이트 배포 결과의 개요](./media/automation-update-management/update-deployment-run.png)

REST API에서 업데이트 배포를 보려면 [소프트웨어 업데이트 구성 실행](/rest/api/automation/softwareupdateconfigurationruns)을 참조하세요.

## <a name="update-classifications"></a>업데이트 분류

다음 표에는 각 분류에 대한 정의와 함께 업데이트 관리의 업데이트 분류가 나열됩니다.

### <a name="windows"></a>Windows

|분류  |설명  |
|---------|---------|
|중요 업데이트     | 보안 관련 중요 버그를 해결하는 특정 문제에 대한 업데이트입니다.        |
|보안 업데이트     | 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.        |
|업데이트 롤업     | 간편한 배포를 위해 함께 패키지된 핫픽스의 누적 집합입니다.        |
|기능 팩     | 제품 릴리스와 따로 배포되는 새로운 제품 기능입니다.        |
|서비스 팩     | 애플리케이션에 적용되는 핫픽스의 누적 집합입니다.        |
|정의 업데이트     | 바이러스 또는 기타 정의 파일에 대한 업데이트입니다.        |
|도구     | 하나 이상의 작업을 완료하는 데 도움이 되는 유틸리티 또는 기능입니다.        |
|업데이트     | 현재 설치되어 있는 애플리케이션 또는 파일에 대한 업데이트입니다.        |

### <a name="linux"></a>Linux

|분류  |설명  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 본질적으로 중요하지 않거나 보안 업데이트가 아닌 기타 모든 업데이트입니다.        |

Linux의 경우 클라우드의 풍부한 데이터로 인해 평가 데이터가 표시되면서, 업데이트 관리가 클라우드에서 중요 및 보안 업데이트 간을 구분할 수 있습니다. 패치의 경우, 업데이트 관리는 컴퓨터에서 사용할 수 있는 분류 데이터에 의존합니다. 다른 배포판과 달리, CentOS에서는 기본적으로 이 정보를 사용할 수 없습니다. CentOS 컴퓨터가 다음 명령에 대해 보안 데이터를 반환하는 방식으로 구성된 경우 업데이트 관리는 분류에 따라 패치를 수행할 수 있습니다.

```bash
sudo yum -q --security check-update
```

현재, CentOS에서 네이티브 분류 데이터 가용성을 지원하는 메서드 지원 메서드가 없습니다. 따라서 이러한 기능을 직접 사용하도록 설정했을 수 있는 고객에게만 최선의 지원이 제공됩니다.

## <a name="firstparty-predownload"></a>고급 설정

업데이트 관리는 Windows 업데이트를 활용하여 Windows 업데이트를 다운로드 및 설치합니다. 따라서 Windows 업데이트에서 사용되는 많은 설정을 적용하고 있습니다. 설정을 사용하여 Windows 이외 업데이트를 사용하도록 설정하는 경우 업데이트 관리는 해당 업데이트도 관리합니다. 업데이트 배포를 수행하기 전에 업데이트 다운로드를 사용하도록 설정하면 업데이트 배포가 더 빠르게 진행되고 유지 관리 기간을 초과하지 않을 수 있습니다.

### <a name="pre-download-updates"></a>업데이트 사전 다운로드

그룹 정책에서 업데이트를 자동으로 다운로드하도록 구성하려면 [자동 업데이트 설정 구성](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates#BKMK_comp5)을 **3**으로 설정할 수 있습니다. 이 경우 필요한 업데이트가 백그라운드에서 다운로드되지만 설치되지는 않습니다. 이를 통해 업데이트 관리 일정을 제어할 수 있지만, 업데이트 관리 유지 관리 기간 이후에도 업데이트를 다운로드할 수 있게 됩니다. 이를 통해 업데이트 관리에서 **유지 관리 기간을 초과함** 오류가 발생하지 않을 수 있습니다.

PowerShell을 사용하여 설정할 수도 있습니다. 이를 위해 업데이트를 자동으로 다운로드하려는 시스템에서 다음 PowerShell을 실행합니다.

```powershell
$WUSettings = (New-Object -com "Microsoft.Update.AutoUpdate").Settings
$WUSettings.NotificationLevel = 3
$WUSettings.Save()
```

### <a name="disable-automatic-installation"></a>자동 설치를 사용 하지 않도록 설정

Azure Vm에는 기본적으로 사용 하도록 설정 하는 업데이트의 자동 설치를 있습니다. 이 인해 업데이트가 업데이트 관리를 통해 설치를 예약 하기 전에 설치 될 수 있습니다. 설정 하 여이 동작을 비활성화할 수는 `NoAutoUpdate` 레지스트리 키를 `1`입니다. 다음 PowerShell 코드 조각은이 작업을 수행 하는 한 가지 방법은 보여 줍니다.

```powershell
$AutoUpdatePath = "HKLM:SOFTWARE\Policies\Microsoft\Windows\WindowsUpdate\AU"
Set-ItemProperty -Path $AutoUpdatePath -Name NoAutoUpdate -Value 1
```

### <a name="enable-updates-for-other-microsoft-products"></a>다른 Microsoft 제품에 대한 업데이트 사용

기본적으로 Windows 업데이트는 Windows용 업데이트만 제공합니다. 사용 하도록 설정 하면 **Windows를 업데이트 해도 다른 Microsoft 제품 업데이트 제공 me**, 보안 패치를 포함 하 여 SQL Server 또는 다른 첫 번째 타사 소프트웨어에 대 한 다른 제품에 대 한 업데이트로 제공 됩니다. 이 옵션은 그룹 정책을 통해 구성할 수 없습니다. 다른 타사 패치를 사용하도록 설정하려는 시스템에서 다음 PowerShell을 실행하면 업데이트 관리에 이 설정이 적용됩니다.

```powershell
$ServiceManager = (New-Object -com "Microsoft.Update.ServiceManager")
$ServiceManager.Services
$ServiceID = "7971f918-a847-4430-9279-4a52d1efe18d"
$ServiceManager.AddService2($ServiceId,7,"")
```

## <a name="third-party"></a> Windows의 타사 패치

업데이트 관리는 지원 되는 Windows 시스템을 패치 하는 로컬로 구성 된 업데이트 리포지토리에 의존 합니다. 이 WSUS 또는 Windows 업데이트입니다. [System Center Updates Publisher](/sccm/sum/tools/updates-publisher
)(Updates Publisher)와 같은 도구를 사용하면 WSUS에 사용자 지정 업데이트를 게시할 수 있습니다. 이 시나리오에는 타사 소프트웨어를 사용 하 여 해당 업데이트 리포지토리로 System Center Configuration Manager를 사용 하는 패치 컴퓨터로 업데이트 관리할 수 있습니다. 업데이트 게시자 구성 방법을 알아보려면 [업데이트 게시자 설치](/sccm/sum/tools/install-updates-publisher)를 참조하세요.

## <a name="ports"></a>네트워크 계획

다음 주소는 업데이트 관리를 위해 특별히 필요합니다. 이러한 주소에 대한 통신은 443 포트를 통해 발생합니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|\*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|
|* .azure-automation.net|*.azure-automation.us|

Hybrid Runbook Worker에 필요한 포트에 대한 자세한 내용은 [Hybrid Worker 역할 포트](automation-hybrid-runbook-worker.md#hybrid-worker-role)를 참조하세요.

예외를 정의할 때 나열된 주소를 사용하는 것이 좋습니다. IP 주소의 경우 [Microsoft Azure 데이터 센터 IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)를 다운로드할 수 있습니다. 이 파일은 매주 업데이트되고 현재 배포된 범위와 향후 예정된 IP 범위 변경 내용을 반영합니다.

## <a name="search-logs"></a>로그 검색

Azure Portal에서 제공하는 세부 정보 외에도 로그에 대한 검색을 수행할 수 있습니다. 솔루션 페이지에 **Log Analytics**를 선택합니다. **로그 검색** 창이 열립니다.

또한 쿼리를 사용자 지정하거나 다른 클라이언트에서 쿼리를 사용하는 방법을 알아보려면 다음을 참조하세요.  [Log Analytics 검색 API 설명서](
https://dev.loganalytics.io/).

### <a name="sample-queries"></a>샘플 쿼리

다음 섹션에서는 이 솔루션에서 수집되는 업데이트 레코드에 대한 샘플 로그 쿼리를 제공합니다.

#### <a name="single-azure-vm-assessment-queries-windows"></a>단일 Azure VM 평가 쿼리(Windows)

VMUUID 값을 쿼리 중인 가상 머신의 VM GUID로 바꿉니다. Azure Monitor 로그에서 다음 쿼리를 실행 하 여 사용 해야 하는 VMUUID를 찾을 수 있습니다. `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>누락 업데이트 요약

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="missing-updates-list"></a>누락 업데이트 목록

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

#### <a name="single-azure-vm-assessment-queries-linux"></a>단일 Azure VM 평가 쿼리(Linux)

일부 Linux 배포판이를 [엔디언](https://en.wikipedia.org/wiki/Endianness) Azure Resource Manager 및 Azure Monitor 로그에 저장 된 것에서 제공 되는 VMUUID 값과 일치 하지 않습니다. 다음 쿼리는 엔디언의 일치 여부를 확인합니다. 결과를 적절히 반환하려면 VMUUID 값을 GUID의 big-endian 및 little-endian 형식으로 바꿉니다. Azure Monitor 로그에서 다음 쿼리를 실행 하 여 사용 해야 하는 VMUUID를 찾을 수 있습니다. `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

##### <a name="missing-updates-summary"></a>누락 업데이트 요약

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

##### <a name="missing-updates-list"></a>누락 업데이트 목록

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

#### <a name="multi-vm-assessment-queries"></a>다중 VM 평가 쿼리

##### <a name="computers-summary"></a>컴퓨터 요약

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

##### <a name="missing-updates-summary"></a>누락 업데이트 요약

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security"
```

##### <a name="computers-list"></a>컴퓨터 목록

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

##### <a name="missing-updates-list"></a>누락 업데이트 목록

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="using-dynamic-groups"></a>동적 그룹을 사용 하 여

업데이트 관리 업데이트 배포에 대 한 Azure 또는 비 Azure Vm의 동적 그룹을 대상으로 하는 기능을 제공 합니다. 이러한 그룹은 컴퓨터를 추가 하려면 배포를 편집 해야 하므로 배포 시에 평가 됩니다.

> [!NOTE]
> 업데이트 배포를 만들 때 적절 한 권한이 있어야 합니다. 자세한 내용은 참조 하세요 [업데이트 설치](#install-updates)합니다.

### <a name="azure-machines"></a>Azure 컴퓨터

이러한 그룹은 쿼리를 통해 정의되고, 업데이트 배포가 시작되면 해당 그룹의 멤버가 평가됩니다. 동적 그룹 클래식 Vm을 사용 하 여 작동 하지 않습니다. 쿼리를 정의할 때 다음 항목을 함께 사용하여 동적 그룹을 채울 수 있습니다.

* 구독
* 리소스 그룹
* 위치
* 태그들

![그룹 선택](./media/automation-update-management/select-groups.png)

동적 그룹의 결과를 미리 보려면 **미리 보기** 단추를 클릭합니다. 이 미리 보기는 당시의 그룹 멤버를 보여주며, 이 예제에서는 **Role** is equal to **BackendServer** 태그가 지정된 머신을 검색합니다. 더 많은 머신에 이 태그를 추가하면 해당 그룹에 대한 향후 배포 시에 추가됩니다.

![미리 보기 그룹](./media/automation-update-management/preview-groups.png)

### <a name="non-azure-machines"></a>비 Azure 컴퓨터

비 azure 컴퓨터에 저장 된 검색 라고도 컴퓨터 그룹의 동적 그룹을 만드는 데 사용 됩니다. 저장된 된 검색을 만드는 방법에 알아보려면 참조 [컴퓨터 그룹을 만드는](../azure-monitor/platform/computer-groups.md#creating-a-computer-group)합니다. 그룹에 생성 되 면 저장 된 검색의 목록에서 선택할 수 있습니다. 클릭 **미리 보기** 당시에 저장된 된 검색의 컴퓨터를 미리 보려면.

![그룹 선택](./media/automation-update-management/select-groups-2.png)

## <a name="integrate-with-system-center-configuration-manager"></a>System Center Configuration Manager와 상호 작용

PC, 서버 및 모바일 디바이스를 관리하기 위해 System Center Configuration Manager에 투자하는 고객들은 소프트웨어 업데이트 관리에 도움을 얻기 위해 Configuration Manager의 강점과 완성도에 의존합니다. Configuration Manager는 해당 SUM(소프트웨어 업데이트 관리) 주기에 속합니다.

System Center Configuration Manager와 관리 솔루션을 통합하는 방법을 알아보려면 [업데이트 관리와 System Center Configuration Manager 통합](oms-solution-updatemgmt-sccmintegration.md)을 참조하세요.

## <a name="inclusion-behavior"></a>포함 동작

업데이트 포함을 사용하면 적용할 특정 업데이트를 지정할 수 있습니다. 포함된 패치 또는 패키지가 설치됩니다. 패치 또는 패키지가 포함되고 분류가 선택되면 포함된 항목 및 분류를 충족하는 항목이 모두 설치됩니다.

제외는 포함을 재정의한다는 사실을 기억해야 합니다. 예를 들어 `*`의 제외 규칙을 정의하는 경우 모든 패치 또는 패키지가 제외되므로 설치되지 않습니다. 제외된 패치가 여전히 머신에서 누락된 것으로 표시됩니다. Linux 머신의 경우 패키지가 포함되었지만 종속 패키지가 제외되면 패키지가 설치되지 않습니다.

## <a name="patch-linux-machines"></a>Linux 컴퓨터 패치

다음 섹션에서는 Linux 패치의 잠재적 문제에 대해 설명합니다.

### <a name="unexpected-os-level-upgrades"></a>예기치 않은 운영 체제 수준 업그레이드

Red Hat Enterprise Linux와 같은 일부 Linux 변형에서는 OS 수준 업그레이드가 패키지를 통해 발생할 수 있습니다. 이로 인해 OS 버전 번호가 변경되는 업데이트 관리가 실행될 수 있습니다. 업데이트 관리는 관리자가 Linux 컴퓨터에서 로컬로 사용하는 것과 동일한 방법으로 패키지를 업데이트하므로 이 동작은 의도적 인 것입니다.

업데이트 관리 실행을 통해 OS 버전을 업데이트하지 않으려면 **제외** 기능을 사용합니다.

Red Hat Enterprise Linux에서 제외할 패키지 이름은 redhat-release-server.x86_64입니다.

![제외할 Linux용 패키지](./media/automation-update-management/linuxpatches.png)

### <a name="critical--security-patches-arent-applied"></a>중요/보안 패치가 적용되지 않음

Linux 컴퓨터에 업데이트를 배포할 때 업데이트 분류를 선택할 수 있습니다. 그러면 지정된 조건에 맞는 머신에 적용되는 업데이트가 필터링됩니다. 이 필터는 업데이트가 배포될 때 컴퓨터에 로컬로 적용됩니다.

업데이트 관리 업데이트 보강 클라우드에서으로 수행 하므로 몇 가지 업데이트 플래그를 지정할 수 업데이트 관리에서 보안의 영향을 주는 것으로 로컬 컴퓨터에는 해당 정보가 없는 경우에 합니다. 따라서 Linux 시스템에 중요 업데이트를 적용하면 해당 컴퓨터에 보안에 영향을 주는 것으로 표시되지 않은 일부 업데이트가 있을 수 있으며, 이 경우 업데이트가 적용되지 않습니다.

그러나 업데이트 관리는 관련 업데이트에 대한 추가 정보가 있으므로 해당 컴퓨터를 호환되지 않는 것으로 보고할 수 있습니다.

업데이트 분류에 따라 업데이트를 배포하는 것은 CentOS에서 기본적으로 지원되지 않습니다. CentOS에 대한 업데이트를 제대로 배포하려면 업데이트를 적용할 수 있도록 모든 분류를 선택합니다. SUSE의 경우 분류로 ‘기타 업데이트’*만* 선택하면 zypper(패키지 관리자)와 관련된 보안 업데이트나 해당 종속성이 먼저 필요한 경우에도 일부 보안 업데이트가 설치될 수 있습니다. 이 동작은 zypper의 제한 사항입니다. 경우에 따라 업데이트 배포를 다시 실행해야 할 수도 있습니다. 확인하려면 업데이트 로그를 확인합니다.

## <a name="remove-a-vm-for-update-management"></a>업데이트 관리용 VM 제거

업데이트 관리에서 VM을 제거하려면:

* Log Analytics 작업 영역에서, 범위 구성 `MicrosoftDefaultScopeConfig-Updates`에 대한 저장된 검색에서 VM을 제거합니다. 저장된 검색은 작업 영역의 **일반**에서 찾을 수 있습니다.
* [Microsoft Monitoring Agent](../azure-monitor/learn/quick-collect-windows-computer.md#clean-up-resources) 또는 [Linux용 Log Analytics 에이전트](../azure-monitor/learn/quick-collect-linux-computer.md#clean-up-resources)를 제거합니다.

## <a name="next-steps"></a>다음 단계

Windows 가상 머신에 대한 업데이트를 관리하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Windows VM에 대한 업데이트 및 패치 관리](automation-tutorial-update-management.md)

* 로그 검색을 사용 하 여 [Azure Monitor 로그](../log-analytics/log-analytics-log-searches.md) 자세한 업데이트 데이터를 볼 수 있습니다.
* 업데이트 배포 상태에 대한 [경고 만들기](automation-tutorial-update-management.md#configure-alerts).

* REST API를 통해 업데이트 관리와 상호 작용하는 방법은 [소프트웨어 업데이트 구성](/rest/api/automation/softwareupdateconfigurations)을 참조하세요.
* 업데이트 관리 문제를 해결하는 방법을 알아보려면 [업데이트 관리 문제 해결](troubleshoot/update-management.md)을 참조하세요.
