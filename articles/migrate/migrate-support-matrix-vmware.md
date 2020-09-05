---
title: Azure Migrate의 VMware 평가 지원
description: Azure Migrate:Server Assessment 도구를 사용하여 VM을 평가하기 위한 VMware에 대한 지원을 알아봅니다.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 4f724297f216267dadda31be4bd548eb241b9845
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89266972"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 평가에 대한 지원 매트릭스 

이 문서에서는 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용 하 여 Azure로 마이그레이션하기 위한 VMware vm을 검색 하 고 평가 하는 경우 필수 구성 요소 및 지원 요구 사항을 요약 합니다. VMware Vm을 평가 하려면 Azure Migrate 프로젝트를 만들고 프로젝트에 서버 평가 도구를 추가 합니다. 도구가 추가된 후 Azure Migrate 어플라이언스를 배포합니다. 이 어플라이언스는 지속적으로 온-프레미스 머신을 검색하고 머신 메타데이터 및 성능 데이터를 Azure에 보냅니다. 검색이 완료된 후에는 검색된 머신을 그룹으로 수집하고 그룹에 대한 평가를 실행합니다. 

VMware VM을 Azure로 마이그레이션하려면 [마이그레이션 지원 매트릭스](migrate-support-matrix-vmware-migration.md)를 검토하세요.



## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다.<br/><br/> 단일 [프로젝트](migrate-support-matrix.md#azure-migrate-projects)에서 최대 35,000개의 VMware VM을 검색하고 평가할 수 있습니다. 프로젝트에는 물리적 서버와 Hyper-V VM이 각각에 대한 평가 제한까지 포함될 수도 있습니다.
**검색** | Azure Migrate 어플라이언스는 vCenter Server에서 VMware VM을 10,000개까지 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35,000개의 머신을 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35,000개의 VM을 평가할 수 있습니다.

평가에 대해 [자세히 알아보세요](concepts-assessment-calculation.md).


## <a name="vmware-requirements"></a>VMware 요구 사항

**VMware** | **세부 정보**
--- | ---
**vCenter Server** | 검색 하 고 평가 하려는 컴퓨터는 vCenter Server 버전 5.5, 6.0, 6.5 또는 6.7에서 관리 해야 합니다.
**권한** | 서버 평가에는 검색 및 평가를 위한 vCenter Server 읽기 전용 계정이 필요 합니다.<br/><br/> 응용 프로그램 검색 또는 종속성 시각화를 수행 하려면 계정에 **Virtual Machines**  >  **게스트 작업**에 대 한 사용 권한이 있어야 합니다.

## <a name="vm-requirements"></a>VM 요구 사항
**VMware** | **세부 정보**
--- | ---
**VMware VM** | 모든 운영 체제의 마이그레이션을 평가할 수 있습니다. 
**스토리지** | SCSI, IDE 및 SATA 기반 컨트롤러에 연결 된 디스크가 지원 됩니다.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md)를 사용합니다. vCenter Server로 가져온 OVA 템플릿을 사용하거나 [PowerShell 스크립트](deploy-appliance-script.md)를 사용하여 어플라이언스를 VMWare VM으로 배포할 수 있습니다.

- VMware에 대한 [어플라이언스 요구 사항](migrate-appliance.md#appliance---vmware)에 대해 알아봅니다.
- Azure Government에서는 [스크립트](deploy-appliance-script-government.md)를 사용하여 어플라이언스를 배포해야 합니다.
- 어플라이언스에서 [공용](migrate-appliance.md#public-cloud-urls) 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스 해야 하는 url을 검토 합니다.


## <a name="port-access-requirements"></a>포트 액세스 요구 사항

**디바이스** | **연결**
--- | ---
**어플라이언스** | 어플라이언스에 대한 원격 데스크톱 연결을 허용하기 위한 TCP 포트 3389에서 인바운드 연결<br/><br/> URL ```https://<appliance-ip-or-name>:44368```을 사용하여 어플라이언스 관리 앱에 원격으로 액세스하기 위한 포트 44368에서 인바운드 연결 <br/><br/>Azure Migrate에 검색 및 성능 메타데이터를 보내기 위한 포트 443(HTTPS)의 아웃바운드 연결
**vCenter Server** | 어플라이언스에서 평가를 위한 구성 및 성능 메타데이터를 수집할 수 있도록 TCP 포트 443에서 인바운드 연결 <br/><br/> 어플라이언스는 기본적으로 포트 443의 vCenter에 연결됩니다. vCenter Server가 다른 포트에서 수신 대기하는 경우 검색을 설정할 때 포트를 수정할 수 있습니다.
**ESXi 호스트** | [앱 검색](how-to-discover-applications.md)또는 [에이전트 없는 종속성 분석](concepts-dependency-visualization.md#agentless-analysis)을 수행 하려는 경우 어플라이언스는 TCP 포트 443의 ESXi 호스트에 연결 하 여 응용 프로그램을 검색 하 고 vm에서 에이전트 없는 종속성 시각화를 실행 합니다.

## <a name="application-discovery-requirements"></a>응용 프로그램 검색 요구 사항

서버 평가는 컴퓨터를 검색 하는 것 외에도 컴퓨터에서 실행 되는 앱, 역할 및 기능을 검색할 수 있습니다. 앱 인벤토리를 검색하면 온-프레미스 워크로드에 맞게 조정된 마이그레이션 경로를 파악하고 계획할 수 있습니다. 

**지원** | **세부 정보**
--- | ---
**지원되는 머신** | 앱 검색은 현재 VMware VM에만 지원됩니다.
**검색** | 앱 검색은 에이전트 없이 수행됩니다. 머신 게스트 자격 증명을 사용하고 WMI 및 SSH 호출을 사용하여 머신에 원격으로 액세스합니다.
**VM 지원** | 앱 검색은 모든 Windows 및 Linux 버전을 실행 하는 Vm에 대해 지원 됩니다.
**vCenter** | 평가에 사용 되는 vCenter Server 읽기 전용 계정 **Virtual Machines**  >  입니다. 응용 프로그램 검색을 위해 VM과 상호 작용 하기 위해 Virtual Machines**게스트 작업**에 대해 사용 권한이 있어야 합니다.
**VM 액세스** | 앱 검색에는 응용 프로그램 검색을 위한 VM의 로컬 사용자 계정이 필요 합니다.<br/><br/> 현재 Azure Migrate는 모든 Windows 서버에 대해 하나의 자격 증명을 사용 하 고 모든 Linux 서버에 대해 하나의 자격 증명을 사용 하도록 지원 합니다.<br/><br/> Windows VM에 대한 게스트 사용자 계정과 모든 Linux VM에 대한 일반/정상 사용자 계정(sudo가 아닌 액세스)을 만듭니다.
**VMware 도구** | 검색하려는 VM에 VMware 도구를 설치하고 실행해야 합니다. <br/><br/> VMware 도구 버전은 10.2.0 이상이어야 합니다.
**PowerShell** | VM에는 PowerShell 버전 2.0 이상이 설치되어 있어야 합니다.
**포트 액세스** | 검색하려는 VM을 실행하는 ESXi 호스트에서 Azure Migrate 어플라이언스는 TCP 포트 443에 연결할 수 있어야 합니다.
**제한** | 앱 검색의 경우 각 Azure Migrate 어플라이언스에서 최대 10,000개의 VM을 검색할 수 있습니다.


## <a name="dependency-analysis-requirements-agentless"></a>종속성 분석 요구 사항 (에이전트 없는)

[종속성 분석](concepts-dependency-visualization.md)을 사용하면 평가하고 Azure로 마이그레이션하려는 온-프레미스 머신 간의 종속성을 파악할 수 있습니다. 이 표에는 에이전트 없는 종속성 분석을 설정하기 위한 요구 사항이 요약되어 있습니다.

**요구 사항** | **세부 정보**
--- | --- 
**배포 전** | Azure Migrate 프로젝트가 준비되어 있고 서버 평가 도구가 프로젝트에 추가되어 있어야 합니다.<br/><br/>  온-프레미스 VMware 컴퓨터를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다.<br/><br/> 처음으로 프로젝트를 만드는 [방법을 알아봅니다](create-manage-projects.md).<br/> 기존 프로젝트에 평가 도구를 추가하는 [방법을 알아봅니다](how-to-assess.md).<br/> VMware VM의 평가를 위해 Azure Migrate 어플라이언스를 설정하는 [방법을 알아봅니다](how-to-set-up-appliance-vmware.md).
**지원되는 머신** | 현재 VMware VM에만 지원됩니다.
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2(64비트)
**vCenter Server 자격 증명** | 종속성 시각화에는 읽기 전용 액세스 권한이 있는 vCenter Server 계정과 가상 머신 > 게스트 작업에 대해 사용하도록 설정된 권한이 필요합니다.
**Windows VM 권한** |  종속성 분석의 경우 Azure Migrate 어플라이언스에는 도메인 관리자 계정 또는 로컬 관리자 계정이 있어야 Windows VM에 액세스할 수 있습니다.
**Linux VM** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7
**Linux 계정** | 종속성 분석의 경우 Linux 머신에서 Azure Migrate 어플라이언스는 루트 권한이 있는 사용자 계정이 필요합니다.<br/><br/> 또는 사용자 계정에 /bin/netstat 및 /bin/ls 파일에 대한 CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE 권한이 필요합니다.
**필요한 에이전트** | 분석하려는 머신에 에이전트가 필요하지 않습니다.
**VMware 도구** | 분석하려는 각 VM에 VMware 도구(10.2 이상)를 설치하고 실행해야 합니다.

**PowerShell** | Windows Vm에는 PowerShell 버전 2.0 이상이 설치 되어 있어야 합니다.
**포트 액세스** | 분석 하려는 Vm을 실행 하는 ESXi 호스트에서 Azure Migrate 어플라이언스는 TCP 포트 443에 연결할 수 있어야 합니다.


## <a name="dependency-analysis-requirements-agent-based"></a>종속성 분석 요구 사항 (에이전트 기반)

[종속성 분석](concepts-dependency-visualization.md)을 사용하면 평가하고 Azure로 마이그레이션하려는 온-프레미스 머신 간의 종속성을 파악할 수 있습니다. 이 표에는 에이전트 기반 종속성 분석을 설정하기 위한 요구 사항이 요약되어 있습니다. 

**요구 사항** | **세부 정보** 
--- | --- 
**배포 전** | Migrate 프로젝트가 준비되어 있고 Azure Migrate: Server Assessment 도구가 프로젝트에 추가되어 있어야 합니다.<br/><br/>  온-프레미스 머신을 검색하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포합니다.<br/><br/> 처음으로 프로젝트를 만드는 [방법을 알아봅니다](create-manage-projects.md).<br/> 기존 프로젝트에 평가 도구를 추가하는 [방법을 알아봅니다](how-to-assess.md).<br/> [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) 또는 물리적 서버의 평가를 위해 Azure Migrate 어플라이언스를 설정하는 방법을 알아봅니다.
**지원되는 머신** | 모든 컴퓨터에 대해 지원 됩니다.
**Azure Government** | Azure Government에서는 종속성 시각화를 사용할 수 없습니다.
**Log Analytics** | Azure Migrate는 종속성 시각화에 대한 [Azure Monitor 로그](../azure-monitor/log-query/log-query-overview.md)의 [서비스 맵](../azure-monitor/insights/service-map.md) 솔루션을 사용합니다.<br/><br/> 신규 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결합니다. Azure Migrate 프로젝트에 대한 작업 영역은 추가된 후 수정할 수 없습니다. <br/><br/> 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> 작업 영역은 미국 동부, 동남 아시아 또는 서유럽 지역에 있어야 합니다. 다른 지역의 작업 영역은 프로젝트에 연결할 수 없습니다.<br/><br/> 작업 영역은 [서비스 맵 지원되는](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.<br/><br/> Log Analytics에서 Azure Migrate와 연결된 작업 영역에는 마이그레이션 프로젝트 키와 프로젝트 이름이 태그로 지정됩니다.
**필요한 에이전트** | 분석하려는 각 머신에서 다음 에이전트를 설치합니다.<br/><br/> [MMA(Microsoft Monitoring Agent) 업그레이드](../azure-monitor/platform/agent-windows.md)<br/> [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> 온-프레미스 머신이 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.<br/><br/> [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)를 설치하는 방법에 대한 자세한 내용을 알아보세요.
**Log Analytics 작업 영역** | 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 현재 미국 동부, 동남 아시아 및 서유럽 지역에 있는 작업 영역을 지원합니다.<br/><br/>  작업 영역은 [서비스 맵 지원되는](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.<br/><br/> Azure Migrate 프로젝트에 대한 작업 영역은 추가된 후 수정할 수 없습니다.
**비용** | Azure Migrate 프로젝트와 Log Analytics 작업 영역을 연결한 날로부터 180일 동안은 서비스 맵 솔루션에서 요금이 부과되지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결된 Log Analytics 작업 영역 내에서 서비스 맵 이외의 다른 솔루션을 사용하면 Log Analytics에 대한 [표준 요금](https://azure.microsoft.com/pricing/details/log-analytics/)이 발생합니다.<br/><br/> Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트 삭제 후에는 서비스 맵 사용이 무료가 아니며 Log Analytics 작업 영역의 유료 계층에 따라 노드마다 요금이 부과됩니다.<br/><br/>Azure Migrate GA(일반 공급 - 2018년 2월 28일) 전에 만든 프로젝트가 있는 경우 추가 서비스 맵 요금이 발생했을 수 있습니다. 180일 후에만 지불되도록 하려면 GA 이전의 기존 작업 영역은 여전히 비용이 부과될 수 있으므로 새 프로젝트를 만드는 것이 좋습니다.
**관리** | 작업 영역에 에이전트를 등록할 때 Azure Migrate 프로젝트에서 제공하는 ID 및 키를 사용합니다.<br/><br/> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br/><br/> 연결된 Azure Migrate 프로젝트를 삭제하면 작업 영역이 자동으로 삭제되지 않습니다. [수동으로 삭제합니다](../azure-monitor/platform/manage-access.md).<br/><br/> Azure Migrate 프로젝트를 삭제하지 않는 한 Azure Migrate가 만든 작업 영역을 삭제하면 안 됩니다. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.
**인터넷 연결** | 머신이 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 설치해야 합니다.
**Azure Government** | 에이전트 기반 종속성 분석은 지원되지 않습니다.


## <a name="next-steps"></a>다음 단계

- 평가를 만드는 모범 사례를 [검토합니다](best-practices-assessment.md).
- [VMware VM 평가 준비](tutorial-prepare-vmware.md)
