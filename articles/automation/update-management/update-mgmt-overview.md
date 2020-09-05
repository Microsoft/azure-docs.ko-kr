---
title: Azure Automation - 업데이트 관리 개요
description: 이 문서에서는 Windows 및 Linux 머신의 업데이트를 구현하는 업데이트 관리 기능의 개요를 살펴봅니다.
services: automation
ms.subservice: update-management
ms.date: 07/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0fd416c844ac93ffb77eded98448b2e93e9acd30
ms.sourcegitcommit: d18a59b2efff67934650f6ad3a2e1fe9f8269f21
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88660911"
---
# <a name="update-management-overview"></a>업데이트 관리 개요

Azure Automation의 업데이트 관리를 사용하면 Azure, 온-프레미스 환경 및 다른 클라우드 환경에서 Windows 및 Linux 머신의 운영 체제 업데이트를 관리할 수 있습니다. 모든 에이전트 머신에서 사용 가능한 업데이트의 상태를 신속하게 평가하고 서버의 필수 업데이트를 설치하는 프로세스를 관리할 수 있습니다.

VM용 업데이트 관리는 다음과 같은 방법으로 사용하도록 설정할 수 있습니다.

* 하나 이상의 Azure 머신의 경우 [Azure Automation 계정](update-mgmt-enable-automation-account.md)에서.
* 비 Azure 머신의 경우 수동으로.
* 단일 Azure VM의 경우 Azure Portal의 가상 머신 페이지에서. 이 시나리오는 [Linux](../../virtual-machines/linux/tutorial-config-management.md#enable-update-management) VM과 [Windows](../../virtual-machines/windows/tutorial-config-management.md#enable-update-management) VM에서 지원됩니다.
* [여러 Azure VM](update-mgmt-enable-portal.md)의 경우 Azure Portal의 가상 머신 페이지에서 해당 VM을 선택.

> [!NOTE]
> 업데이트 관리를 사용하려면 Log Analytics 작업 영역을 Automation 계정에 연결해야 합니다. 지원되는 지역 목록은 [Azure 작업 영역 매핑](../how-to/region-mappings.md)을 참조하세요. 지역 매핑은 Automation 계정과 별도의 지역에 있는 VM을 관리하는 기능에 영향을 주지 않습니다.

신규 또는 기존 Automation 계정과 구독의 Log Analytics 작업 영역으로 업데이트 관리를 배포하는 데 도움이 되는 [Azure Resource Manager 템플릿](update-mgmt-enable-template.md)이 있습니다.

> [!NOTE]
> 업데이트 관리로 구성한 머신은 Azure Automation에서 사용자 지정 스크립트를 실행하는 데 사용할 수 없습니다. 이 머신은 Microsoft 서명 업데이트 스크립트만 실행할 수 있습니다.

## <a name="about-update-management"></a>업데이트 관리에 대해 자세히 알아보기

업데이트 관리를 통해 관리되는 머신은 다음 구성을 사용하여 평가 및 업데이트 배포를 수행합니다.

* Windows 또는 Linux용 Log Analytics 에이전트
* Linux용 PowerShell DSC(필요한 상태 구성)
* Automation Hybrid Runbook Worker
* Windows 머신용 Microsoft Update 또는 WSUS(Windows Server Update Services)

다음 다이어그램에서는 업데이트 관리가 보안 업데이트를 평가하고 작업 영역에 있는 연결된 모든 Windows Server 및 Linux 머신에 이를 적용하는 방법을 보여 줍니다.

![업데이트 관리 워크플로](./media/update-mgmt-overview/update-mgmt-updateworkflow.png)

업데이트 관리는 동일한 테넌트의 여러 구독에 있는 머신을 네이티브로 배포하는 데 사용할 수 있습니다.

패키지가 릴리스되면 평가를 위해 Linux 머신의 패치가 나타날 때까지 2~3시간이 걸립니다. Windows 머신의 경우 릴리스된 후 평가를 위해 패치가 나타날 때까지 12~15시간이 걸립니다.

머신에서 업데이트 준수 검사를 완료하면 에이전트가 Azure Monitor 로그에 정보를 대량으로 전달합니다. Windows 머신에서는 기본적으로 12시간마다 준수 검사가 실행됩니다.

검사 일정 외에도, Log Analytics 에이전트가 다시 시작된 지 15분 이내에, 업데이트 설치 전에, 그리고 업데이트 설치 후에 업데이트 준수 검사가 시작됩니다.

Linux 머신에서는 기본적으로 1시간마다 준수 검사가 이루어집니다. Log Analytics 에이전트가 다시 시작되면 15분 이내에 준수 검사가 시작됩니다.

업데이트 관리는 동기화하도록 구성된 원본을 기반으로 머신이 얼마나 최신 상태인지를 보고합니다. Windows 머신이 WSUS에 보고하도록 구성된 경우 WSUS가 Microsoft 업데이트와 마지막으로 동기화된 시기에 따라 그 결과는 Microsoft 업데이트가 표시하는 내용과 다를 수 있습니다. 이 동작은 공용 리포지토리 대신 로컬 리포지토리에 보고하도록 구성된 Linux 머신에서도 동일합니다.

> [!NOTE]
> 서비스에 제대로 보고하려면 업데이트 관리를 수행할 때 특정 URL 및 포트가 사용되도록 설정되어야 합니다. 이러한 요구 사항에 대한 자세한 내용은 [네트워크 구성](../automation-hybrid-runbook-worker.md#network-planning)을 참조하세요.

예약 배포를 만들어서 업데이트가 필요한 머신에 소프트웨어 업데이트를 배포하고 설치할 수 있습니다. 선택 사항으로 분류된 업데이트는 Windows 머신의 배포 범위에 포함되지 않습니다. 배포 범위에는 필수 업데이트만 포함됩니다.

예약 배포는 특정 컴퓨터를 명시적으로 지정 하거나 지정 된 기준에 따라 동적으로 Azure Vm을 선택 하는 특정 컴퓨터 집합 (또는 [Azure query] logs.md)의 로그 검색을 기반으로 하는 [컴퓨터 그룹](../../azure-monitor/platform/computer-groups.md) 을 선택 하 여이를 수행 합니다. 이 그룹은 업데이트 관리를 사용하도록 설정하기 위해 구성을 수신할 머신의 대상을 제어하는 데 사용되는 [범위 구성](../../azure-monitor/insights/solution-targeting.md)과는 다릅니다. 이 그룹은 업데이트 준수를 수행 및 보고하는 것을 방지하며, 승인된 필요한 업데이트를 설치합니다.

배포를 정의할 때는 승인할 일정을 지정하고 업데이트가 설치될 수 있는 기간을 설정합니다. 이 기간을 유지 관리 기간이라고 합니다. 유지 관리 기간 중 20분은 재부팅이 필요하며 사용자가 적절한 재부팅 옵션을 선택했다고 가정한 상태로 재부팅을 위해 예약됩니다. 패치에 예상보다 시간이 오래 걸리고 유지 관리 기간이 20분 미만이라면 재부팅이 이루어지지 않습니다.

Azure Automation의 runbook에서 업데이트가 설치됩니다. 이러한 Runbook은 볼 수 없고 Runbook에는 구성이 필요하지 않습니다. 업데이트 배포가 생성되면 업데이트 배포는 포함된 머신에 대해 지정된 시간에 마스터 업데이트 Runbook을 시작하는 일정을 만듭니다. 이 마스터 Runbook은 각 에이전트에서 하위 Runbook을 시작하여 필수 업데이트를 설치합니다.

업데이트 배포에 지정된 날짜 및 시간에 대상 머신은 배포를 병렬로 실행합니다. 설치 전에, 업데이트가 여전히 필요한지 확인하기 위한 검사가 수행됩니다. WSUS 클라이언트 머신의 경우 업데이트가 WSUS에서 승인되지 않았으면 업데이트 배포가 실패합니다.

머신 하나를 둘 이상의 Log Analytics 작업 영역에서 업데이트 관리에 등록(멀티 호밍)하는 것은 지원되지 않습니다.

## <a name="clients"></a>클라이언트

### <a name="supported-client-types"></a>지원되는 클라이언트 유형

다음 표에는 업데이트 평가 및 패치를 위해 지원 되는 운영 체제가 나와 있습니다. 패치를 사용하려면 Hybrid Runbook Worker가 필요합니다. Hybrid Runbook Worker 요구 사항에 대한 자세한 내용은 [Windows Hybrid Runbook Worker 배포](../automation-windows-hrw-install.md) 및 [Linux Hybrid Runbook Worker 배포](../automation-linux-hrw-install.md)를 참조하세요.

> [!NOTE]
> Linux 머신의 업데이트 평가는 Automation 계정 및 Log Analytics 작업 영역 [매핑 테이블](../how-to/region-mappings.md#supported-mappings)에 안내된 대로 특정 지역에서만 지원됩니다. 

|운영 체제  |메모  |
|---------|---------|
|Windows Server 2019(Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2016(Datacenter/Datacenter Core/Standard)<br><br>Windows Server 2012 R2(Datacenter/Standard)<br><br>Windows Server 2012 ||
|Windows Server 2008 R2(RTM 및 SP1 Standard)| 업데이트 관리은이 운영 체제에 대 한 평가 및 패치를 지원 합니다. [Hybrid Runbook Worker](../automation-windows-hrw-install.md) 는 Windows Server 2008 r 2에서 지원 됩니다. |
|CentOS 6(x86/x64) 및 7(x64)      | Linux 에이전트를 사용하려면 업데이트 리포지토리에 대한 액세스 권한이 필요합니다. 분류 기반 패치를 사용하려면 `yum`에서 CentOS가 RTM 릴리스에 없는 보안 데이터를 반환해야 합니다. CentOS의 분류 기반 패치에 대한 자세한 내용은 [Linux의 업데이트 분류](update-mgmt-view-update-assessments.md#linux)를 참조하세요.          |
|Red Hat Enterprise 6(x86/x64) 및 7(x64)     | Linux 에이전트를 사용하려면 업데이트 리포지토리에 대한 액세스 권한이 필요합니다.        |
|SUSE Linux Enterprise Server 11(x86/x64) 및 12(x64)     | Linux 에이전트를 사용하려면 업데이트 리포지토리에 대한 액세스 권한이 필요합니다.        |
|Ubuntu 14.04 LTS, 16.04 LTS 및 18.04(x86/x64)      |Linux 에이전트를 사용하려면 업데이트 리포지토리에 대한 액세스 권한이 필요합니다.         |

> [!NOTE]
> Azure Virtual Machine Scale Sets는 업데이트 관리를 통해 관리할 수 있습니다. 업데이트 관리는 기본 이미지가 아닌 인스턴스 자체에 대해 작동합니다. 모든 VM 인스턴스가 한 번에 업데이트되지 않도록 업데이트를 증분 방식으로 예약해야 합니다. [변경 내용 추적 및 인벤토리에 비 Azure 머신 추가](../automation-tutorial-installed-software.md#add-a-non-azure-machine-to-change-tracking-and-inventory)의 단계에 따라 가상 머신 확장 집합의 노드를 추가할 수 있습니다.

### <a name="unsupported-client-types"></a>지원되지 않는 클라이언트 유형

다음 표에는 지원되지 않는 운영 체제가 나와 있습니다.

|운영 체제  |메모  |
|---------|---------|
|Windows 클라이언트     | 클라이언트 운영 체제(예: Windows 7 및 Windows 10)는 지원되지 않습니다.<br> Azure WVD(Windows Virtual Desktop)의 경우<br> 업데이트를 관리 하려면 Windows 10 클라이언트 컴퓨터 패치 관리를 위한 [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) 입니다. |
|Windows Server 2016 Nano Server     | 지원되지 않습니다.       |
|Azure Kubernetes Service 노드 | 지원되지 않습니다. [AKS(Azure Kubernetes Service)에서 Linux 노드에 보안 및 커널 업데이트 적용](../../aks/node-updates-kured.md)에서 설명하는 패치 프로세스를 사용합니다.|

### <a name="client-requirements"></a>클라이언트 요구 사항

아래에서는 운영 체제별 클라이언트 요구 사항에 대해 설명합니다. 추가 지침이 필요하면 [네트워크 계획](#ports)을 참조하세요. TLS 1.2에 대 한 클라이언트 요구 사항을 이해 하려면 [Azure Automation에 대 한 tls 1.2 적용](../automation-managing-data.md#tls-12-enforcement-for-azure-automation)을 참조 하세요.

#### <a name="windows"></a>Windows

Windows 에이전트는 WSUS 서버와 통신하도록 구성되거나 Microsoft 업데이트에 대한 액세스 권한을 가지고 있어야 합니다. Windows용 Log Analytics 에이전트를 설치하는 방법은 [Azure Monitor에 Windows 컴퓨터 연결](../../azure-monitor/platform/agent-windows.md)을 참조하세요.

업데이트 관리를 Microsoft Endpoint Configuration Manager와 함께 사용할 수 있습니다. 통합 시나리오에 대한 자세한 내용은 To learn more about integration scenarios, see [Windows Endpoint Configuration Manager와 업데이트 관리 통합](update-mgmt-mecmintegration.md)을 참조하세요. Configuration Manager 환경에서 사이트에 의해 관리되는 Windows 서버에는 [Windows용 Log Analytics 에이전트](../../azure-monitor/platform/agent-windows.md)가 필요합니다. 

기본적으로 Azure Marketplace에서 배포된 VM은 Windows 업데이트 서비스에서 자동으로 업데이트를 받도록 설정됩니다. 이 동작은 작업 영역에 Windows VM을 추가해도 달라지지 않습니다. 업데이트 관리를 사용하여 업데이트를 적극적으로 관리하지 않는 경우 기본 동작(업데이트 자동 적용)이 적용됩니다.

> [!NOTE]
> 머신 재부팅이 시스템이 아니라 사용자에 의해서만 수행될 수 있도록 그룹 정책을 수정할 수 있습니다. 관리형 머신은 업데이트 관리에 사용자의 수동 개입 없이 머신을 재부팅할 권한이 없는 경우 멈출 수 있습니다. 자세한 내용은 [자동 업데이트를 위한 그룹 정책 설정 구성](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates)을 참조하세요.

#### <a name="linux"></a>Linux

Linux의 경우 머신에 업데이트 리포지토리에 대한 프라이빗 또는 퍼블릭 액세스 권한이 필요합니다. 업데이트 관리와 상호 작용하는 데 TLS 1.1 또는 TLS 1.2가 필요합니다. 업데이트 관리는 둘 이상의 Log Analytics 작업 영역에 보고하도록 구성된 Linux용 Log Analytics 에이전트를 지원하지 않습니다. 머신에 Python 2.x도 설치되어 있어야 합니다.

> [!NOTE]
> Linux 머신의 업데이트 평가는 특정 지역에서만 지원됩니다. Automation 계정 및 Log Analytics 작업 영역 [매핑 테이블](../how-to/region-mappings.md#supported-mappings)을 참조하세요.

Linux용 Log Analytics 에이전트를 설치하고 최신 버전을 다운로드하는 방법은 [Linux용 Log Analytics 에이전트](../../azure-monitor/platform/agent-linux.md)를 참조하세요.

Azure Marketplace에서 제공되는 주문형 RHEL(Red Hat Enterprise Linux) 이미지에서 만든 VM은 Azure에 배포된 [RHUI(Red Hat Update Infrastructure)](../../virtual-machines/workloads/redhat/redhat-rhui.md)에 액세스하도록 등록됩니다. 다른 모든 Linux 배포판은 해당 배포판에서 지원되는 방법을 사용하여 배포판의 온라인 파일 리포지토리에서 업데이트되어야 합니다.

## <a name="permissions"></a>사용 권한

업데이트 배포를 만들고 관리하려면 특정 권한이 필요합니다. 이러한 권한에 대한 자세한 내용은 [역할 기반 액세스 - 업데이트 관리](../automation-role-based-access-control.md#update-management-permissions)를 참조하세요.

## <a name="update-management-components"></a>업데이트 관리 구성 요소

업데이트 관리는 섹션에서 설명하는 리소스를 사용합니다. 리소스는 업데이트 관리를 사용하도록 설정하면 Automation 계정에 자동으로 추가됩니다.

### <a name="hybrid-runbook-worker-groups"></a>Hybrid Runbook Worker 그룹

업데이트 관리를 사용하도록 설정하면 Log Analytics 작업 영역에 직접 연결된 모든 Windows 머신이 자동으로 Hybrid Runbook Worker로 구성되어 업데이트 관리를 지원하는 Runbook을 지원합니다.

업데이트 관리에서 관리되는 각 Windows 머신은 경우 해당 Automation 계정의 Hybrid Worker 그룹 창에 시스템 Hybrid Worker 그룹으로 표시됩니다. 그룹은 `Hostname FQDN_GUID` 명명 규칙을 사용합니다. 계정에서 Runbook을 사용하여 이러한 그룹을 대상으로 지정할 수 없습니다. 대상으로 지정하려고 시도해도 실패하게 됩니다. 그룹은 업데이트 관리를 지원하는 용도로만 사용할 수 있습니다.

업데이트 관리와 Hybrid Runbook Worker 그룹 멤버 자격에 동일한 계정을 사용하는 경우, Automation 계정의 Hybrid Runbook Worker 그룹에 Windows 머신을 추가하여 Automation Runbook을 지원할 수 있습니다. 이 기능은 Hybrid Runbook Worker의 7.2.12024.0 버전에 추가되었습니다.

### <a name="management-packs"></a>관리 팩

Operations Manager 관리 그룹이 [Log Analytics 작업 영역에 연결되면](../../azure-monitor/platform/om-agents.md) 다음 관리 팩이 Operations Manager에 설치됩니다. 관리 팩은 직접 연결된 Windows 머신의 업데이트 관리에 대해서도 설치됩니다. 이러한 관리 팩을 구성하거나 관리할 필요가 없습니다.

* Microsoft System Center Advisor 업데이트 평가 인텔리전스 팩(Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration(Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* 업데이트 배포 MP

> [!NOTE]
> 로그 데이터를 수집하도록 관리 그룹에 에이전트가 구성된 Log Analytics 작업 영역에 연결된 Operations Manager 1807 또는 2019 관리 그룹이 있는 경우, **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** 규칙에서 `IsAutoRegistrationEnabled` 매개 변수를 재정의하여 True로 설정해야 합니다.

관리 팩의 업데이트에 대한 자세한 내용은 [Azure Monitor 로그에 Operations Manager 연결](../../azure-monitor/platform/om-agents.md)을 참조하세요.

> [!NOTE]
> 업데이트 관리가 Log Analytics 에이전트를 사용하여 머신을 완전히 관리할 수 있으려면 Windows용 Log Analytics 에이전트 또는 Linux용 Log Analytics 에이전트로 업데이트해야 합니다. 에이전트를 업데이트하는 방법을 알아보려면 [Operations Manager 에이전트를 업그레이드하는 방법](/system-center/scom/deploy-upgrade-agents)을 참조하세요. Operations Manager를 사용하는 환경에서는 System Center Operations Manager 2012 R2 UR 14 이상을 실행해야 합니다.

## <a name="data-collection"></a>데이터 수집

### <a name="supported-sources"></a>지원되는 원본

다음 표에서는 업데이트 관리에서 지원하는 연결된 원본에 대해 설명합니다.

| 연결된 원본 | 지원됨 | Description |
| --- | --- | --- |
| Windows 에이전트 |예 |업데이트 관리에서 Windows 에이전트로부터 시스템 업데이트에 대한 정보를 수집하고 필요한 업데이트를 설치하기 시작합니다. |
| Linux 에이전트 |예 |업데이트 관리에서 Linux 에이전트로부터 시스템 업데이트에 대한 정보를 수집하고 지원되는 배포판에서 필요한 업데이트를 설치하기 시작합니다. |
| Operations Manager 관리 그룹 |예 |업데이트 관리에서 연결된 관리 그룹의 에이전트로부터 시스템 업데이트에 대한 정보를 수집합니다.<br/><br/>Operations Manager 에이전트에서 Azure Monitor 로그로의 직접 연결은 필요하지 않습니다. 데이터는 관리 그룹에서 Log Analytics 작업 영역으로 전달됩니다. |

### <a name="collection-frequency"></a>수집 빈도

업데이트 관리는 다음 규칙을 사용하여 관리형 머신에서 데이터를 검사합니다. 관리형 머신의 업데이트된 데이터가 대시보드에 표시되기까지 30분에서 6시간이 걸릴 수 있습니다.

* 각 Windows 머신 - 업데이트 관리가 각 머신에 대해 하루에 두 번 검사를 수행합니다. 15분에 한 번씩 Windows API에 마지막 업데이트 시간을 쿼리하고 상태가 변경되었는지 확인합니다. 상태가 변경된 경우 업데이트 관리가 준수 검사를 시작합니다.

* 각 Linux 머신 - 업데이트 관리가 한 시간에 한 번씩 검사를 수행합니다.

업데이트 관리를 사용하는 머신에서 Azure Monitor 로그가 평균적으로 사용하는 데이터는 대략적으로 월 25MB입니다. 이 값은 근사값이며, 사용자 환경에 따라 변경될 수 있습니다. 환경을 모니터링하여 정확한 사용량을 확인하는 것이 좋습니다. Azure Monitor 로그 데이터 사용량을 분석 하는 방법에 대 한 자세한 내용은 [사용량 및 비용 관리](../../azure-monitor/platform/manage-cost-storage.md)를 참조 하세요.

## <a name="network-planning"></a><a name="ports"></a>네트워크 계획

다음 주소는 업데이트 관리를 위해 특별히 필요합니다. 이러한 주소에 대한 통신은 443 포트를 통해 발생합니다.

|Azure 공용  |Azure Government  |
|---------|---------|
|`*.ods.opinsights.azure.com`    | `*.ods.opinsights.azure.us`        |
|`*.oms.opinsights.azure.com`     | `*.oms.opinsights.azure.us`        |
|`*.blob.core.windows.net` | `*.blob.core.usgovcloudapi.net`|
|`*.azure-automation.net` | `*.azure-automation.us`|

Automation 서비스 및 Log Analytics 작업 영역에 대 한 트래픽을 허용 하도록 네트워크 그룹 보안 규칙을 만들거나 Azure 방화벽을 구성 하는 경우 [서비스 태그](../../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** 및 **azuremonitor**를 사용 합니다. 네트워크 보안 규칙의 지속적인 관리를 간소화 합니다. Azure Vm에서 자동화 서비스에 안전 하 고 개인적으로 연결 하려면 [Azure 개인 링크 사용](../how-to/private-link-security.md)을 검토 하세요. 온-프레미스 방화벽 구성의 일부로 포함할 현재 서비스 태그 및 범위 정보를 가져오려면 [다운로드 가능한 JSON 파일](../../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)을 참조 하세요.

Windows 머신의 경우 Windows 업데이트에 필요한 모든 엔드포인트로도 트래픽을 허용해야 합니다. 필요한 엔드포인트의 업데이트된 목록은 [HTTP/프록시 관련 문제](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)에서 확인할 수 있습니다. 로컬 [Windows 업데이트 서버](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)가 있는 경우 [WSUS 키](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)에 지정된 서버로도 트래픽을 허용해야 합니다.

Red Hat Linux 머신의 경우 [RHUI 콘텐츠 배달 서버의 IP](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers)에서 필요한 엔드포인트를 확인하세요. 기타 Linux 배포판의 경우 공급자의 설명서를 확인하세요.

Hybrid Runbook Worker에 필요한 포트에 대한 자세한 내용은 [Hybrid Runbook Worker의 업데이트 관리 주소](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker)를 참조하세요.

IT 보안 정책이 네트워크의 컴퓨터가 인터넷에 연결 하는 것을 허용 하지 않는 경우 [Log Analytics 게이트웨이](../../azure-monitor/platform/gateway.md) 를 설정한 다음 게이트웨이를 통해 연결 하 여 Azure Automation 및 Azure Monitor 하도록 컴퓨터를 구성할 수 있습니다.

## <a name="update-classifications"></a>업데이트 분류

다음 표에서는 업데이트 관리가 Windows 업데이트에 대해 지원하는 분류를 정의합니다. 

|분류  |Description  |
|---------|---------|
|중요 업데이트     | 보안 관련 중요 버그를 해결하는 특정 문제에 대한 업데이트입니다.        |
|보안 업데이트     | 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.        |
|업데이트 롤업     | 간편한 배포를 위해 함께 패키지된 핫픽스의 누적 집합입니다.        |
|기능 팩     | 제품 릴리스와 따로 배포되는 새로운 제품 기능입니다.        |
|서비스 팩     | 애플리케이션에 적용되는 핫픽스의 누적 집합입니다.        |
|정의 업데이트     | 바이러스 또는 기타 정의 파일에 대한 업데이트입니다.        |
|도구     | 하나 이상의 작업을 완료하는 데 도움이 되는 유틸리티 또는 기능입니다.        |
|업데이트     | 현재 설치되어 있는 애플리케이션 또는 파일에 대한 업데이트입니다.        |

다음 표에서는 Linux 업데이트에 대해 지원되는 분류를 정의합니다.

|분류  |Description  |
|---------|---------|
|중요 업데이트 및 보안 업데이트     | 특정 문제 또는 제품이 특정된 보안 관련 문제에 대한 업데이트입니다.         |
|다른 업데이트     | 본질적으로 중요하지 않거나 보안 업데이트가 아닌 그 외의 모든 업데이트입니다.        |

>[!NOTE]
>Linux 컴퓨터에 대 한 업데이트 분류는 지원 되는 Azure 공용 클라우드 지역에서 사용 되는 경우에만 사용할 수 있습니다. 다음 국가 클라우드 지역에서 업데이트 관리를 사용 하는 경우:
>* Azure 미국 정부
>* 중국의 21Vianet
>
> Linux 업데이트 분류는 없으며 **다른 업데이트** 범주에 보고 됩니다. 업데이트 관리은 지원 되는 배포판, 특히 릴리스된 [OVAL](https://oval.mitre.org/) (개방형 취약성 및 평가 언어) 파일에 의해 게시 된 데이터를 사용 합니다. 인터넷 액세스는 이러한 국가별 클라우드에서 제한 되기 때문에 이러한 파일에 액세스 하 고 사용할 수 업데이트 관리.

Linux의 경우 클라우드의 풍부한 데이터로 인해 평가 데이터가 표시되면서, 업데이트 관리가 클라우드에서 중요 업데이트와 보안 업데이트를 구분할 수 있습니다. 패치의 경우, 업데이트 관리는 컴퓨터에서 사용할 수 있는 분류 데이터에 의존합니다. 다른 배포판과 달리, CentOS에서는 RTM 버전에서 이 정보를 사용할 수 없습니다. CentOS 머신이 다음 명령에 대해 보안 데이터를 반환하도록 구성된 경우 업데이트 관리에서는 분류에 따라 패치를 수행할 수 있습니다.

```bash
sudo yum -q --security check-update
```

현재는 CentOS에서 네이티브 분류 데이터 가용성을 지원하는 메서드가 없습니다. 따라서 이러한 기능을 직접 사용하도록 설정했을 수 있는 고객에게만 최선의 지원이 제공됩니다.

Red Hat Enterprise 버전 6의 업데이트를 분류하려면 yum-보안 플러그인을 설치해야 합니다. Red Hat Enterprise Linux 7에서는 플러그인이 이미 yum 자체에 포함되어 있으므로 아무것도 설치할 필요가 없습니다. 자세한 내용은 다음 Red Hat [기술 항목](https://access.redhat.com/solutions/10021)을 참조하세요.

## <a name="integrate-update-management-with-configuration-manager"></a>Configuration Manager와 업데이트 관리 통합

PC, 서버 및 모바일 디바이스를 관리하기 위해 Microsoft Endpoint Configuration Manager에 투자한 고객은 Configuration Manager의 이점과 성숙도에 의존하여 소프트웨어 업데이트를 관리합니다. Configuration Manager에 업데이트 관리를 통합하는 방법을 알아보려면 [Windows Endpoint Configuration Manager와 업데이트 관리 통합](update-mgmt-mecmintegration.md)을 참조하세요.

## <a name="third-party-updates-on-windows"></a>Windows의 타사 업데이트

업데이트 관리는 로컬에 구성된 업데이트 리포지토리를 사용하여 지원되는 Windows 시스템(WSUS 또는 Windows 업데이트)을 업데이트합니다. [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher)와 같은 도구를 사용하면 WSUS를 통해 사용자 지정 업데이트를 가져오고 게시할 수 있습니다. 이 시나리오에서는 업데이트 관리에서 타사 소프트웨어를 통해 Configuration Manager를 업데이트 리포지토리로 사용하는 머신을 업데이트할 수 있습니다. 업데이트 게시자 구성 방법을 알아보려면 [업데이트 게시자 설치](/configmgr/sum/tools/install-updates-publisher)를 참조하세요.

## <a name="enable-update-management"></a>업데이트 관리 사용

신규 또는 기존 Automation 계정과 구독의 Azure Monitor Log Analytics 작업 영역으로 업데이트 관리를 배포하는 데 도움이 되는 Azure [Resource Manager 템플릿](update-mgmt-enable-template.md)이 있습니다. 템플릿은 관리해야 하는 머신의 범위를 구성하지는 않습니다. 이 작업은 템플릿을 사용한 후 별도의 단계로 수행됩니다.

다음은 업데이트 관리를 사용하도록 설정하고 관리할 머신을 선택하는 방법입니다.

* [가상 머신에서 등록](update-mgmt-enable-vm.md)
* [여러 컴퓨터에서 찾아보기](update-mgmt-enable-portal.md)
* [Azure Automation 계정에서](update-mgmt-enable-automation-account.md)

## <a name="next-steps"></a>다음 단계

* 업데이트 관리 작업에 대 한 자세한 내용은 [vm에 대 한 업데이트 관리](update-mgmt-manage-updates-for-vm.md)를 참조 하세요.

* [Azure Automation 자주 묻는 질문](../automation-faq.md)에서 업데이트 관리에 대한 일반적인 질문을 살펴보세요.
