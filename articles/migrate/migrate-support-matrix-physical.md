---
title: Azure Migrate의 물리적 서버 평가 지원
description: Azure Migrate Server 평가를 사용 하 여 물리적 서버 평가 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 2b96bff7468f0705f2b80f60dcd5248960495f16
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88640126"
---
# <a name="support-matrix-for-physical-server-assessment"></a>물리적 서버 평가를 위한 지원 매트릭스 

이 문서에서는 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-assessment-tool) 도구를 사용 하 여 Azure로의 마이그레이션에 대 한 물리적 서버를 평가 하는 경우 필수 구성 요소 및 지원 요구 사항을 요약 합니다. 물리적 서버를 Azure로 마이그레이션하려면 [마이그레이션 지원 매트릭스](migrate-support-matrix-physical-migration.md)를 검토 하세요.


물리적 서버를 평가 하려면 Azure Migrate 프로젝트를 만들고 프로젝트에 서버 평가 도구를 추가 합니다. 도구가 추가된 후 [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포합니다. 이 어플라이언스는 지속적으로 온-프레미스 머신을 검색하고 머신 메타데이터 및 성능 데이터를 Azure에 보냅니다. 검색이 완료된 후에는 검색된 머신을 그룹으로 수집하고 그룹에 대한 평가를 실행합니다.


## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**평가 제한** | 단일 [Azure Migrate 프로젝트](migrate-support-matrix.md#azure-migrate-projects)에서 최대 35000 대의 물리적 서버를 검색 하 고 평가할 수 있습니다.
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다. 물리적 서버 외에도 프로젝트에는 각각에 대 한 평가 제한까지 VMware Vm 및 Hyper-v Vm이 포함 될 수 있습니다.
**검색** | Azure Migrate 어플라이언스는 최대 1000 대의 물리적 서버를 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35,000개의 머신을 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35000 대의 컴퓨터를 평가할 수 있습니다.

평가에 대해 [자세히 알아보세요](concepts-assessment-calculation.md).

## <a name="physical-server-requirements"></a>물리적 서버 요구 사항

| **지원**                | **세부 정보**               
| :-------------------       | :------------------- |
| **실제 서버 배포**       | 물리적 서버는 독립 실행형 이거나 클러스터에 배포할 수 있습니다. |
| **권한**           | **Windows:** 도메인에 가입 된 컴퓨터에 도메인 계정을 사용 하 고 도메인에 가입 되지 않은 컴퓨터에 대 한 로컬 계정을 사용 합니다. 사용자 계정은 다음 그룹에 추가되어야 합니다. 원격 관리 사용자, 성능 모니터 사용자 및 성능 로그 사용자. <br/><br/> **Linux:** 검색하려는 Linux 서버의 루트 계정이 필요합니다. <br/> 또는 다음 명령을 사용 하 여 필요한 기능이 설정 되었는지 확인 합니다. <br/> setcap CAP_DAC_READ_SEARCH + eip/usr/sbin/fdisk <br/> setcap CAP_DAC_READ_SEARCH + eip/sbin/fdisk (/usr/sbin/fdisk이 없는 경우) <br/> setcap "cap_dac_override, cap_dac_read_search, cap_fowner, cap_fsetid, cap_setuid, cap_setpcap, cap_net_bind_service, cap_net_admin, cap_sys_chroot, cap_sys_admin, cap_sys_resource, cap_audit_control, cap_setfcap = + eip"/sbin/lvm <br/> setcap CAP_DAC_READ_SEARCH + eip/usr/sbin/dmidecode <br/> chmod a + r/sys/class/dmi/id/product_uuid
| **운영 체제** | Windows Server 2003 및 SUSE Linux를 제외한 모든 운영 체제는 마이그레이션을 평가할 수 있습니다. |


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md)를 사용합니다. 물리적 서버에 대 한 어플라이언스는 VM 또는 물리적 컴퓨터에서 실행할 수 있습니다. 

- 물리적 서버의 [어플라이언스 요구 사항](migrate-appliance.md#appliance---physical) 에 대해 알아봅니다.
- 어플라이언스가 [퍼블릭](migrate-appliance.md#public-cloud-urls) 클라우드 및 [정부](migrate-appliance.md#government-cloud-urls) 클라우드에서 액세스해야 하는 URL에 대해 알아봅니다.
- Azure Portal에서 다운로드 하는 [PowerShell 스크립트](how-to-set-up-appliance-physical.md) 를 사용 하 여 어플라이언스를 설정 합니다.
Azure Government에서 [이 스크립트를 사용 하 여](deploy-appliance-script-government.md)어플라이언스를 배포 합니다.

## <a name="port-access"></a>포트 액세스

다음 표에는 평가를 위한 포트 요구 사항이 요약 되어 있습니다.

**디바이스** | **연결**
--- | ---
**어플라이언스** | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하는 TCP 포트 3389에 대 한 인바운드 연결입니다.<br/><br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위해 포트 44368에서 인바운드 연결: ``` https://<appliance-ip-or-name>:44368 ```<br/><br/> Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443 (HTTPS)의 아웃 바운드 연결
**물리적 서버** | **Windows:** Windows 서버에서 구성 및 성능 메타 데이터를 가져오기 위한 WinRM 포트 5985 (HTTP)에 대 한 인바운드 연결입니다. <br/><br/> **Linux:**  Linux 서버에서 구성 및 성능 메타 데이터를 가져오기 위해 포트 22 (TCP)에서 인바운드 연결 |

## <a name="agent-based-dependency-analysis-requirements"></a>에이전트 기반 종속성 분석 요구 사항

[종속성 분석](concepts-dependency-visualization.md)을 사용하면 평가하고 Azure로 마이그레이션하려는 온-프레미스 머신 간의 종속성을 파악할 수 있습니다. 이 표에는 에이전트 기반 종속성 분석을 설정하기 위한 요구 사항이 요약되어 있습니다. 현재 실제 서버에 대해서는 에이전트 기반 종속성 분석만 지원 됩니다.

**요구 사항** | **세부 정보** 
--- | --- 
**배포 전** | Azure Migrate 프로젝트가 준비되어 있고 서버 평가 도구가 프로젝트에 추가되어 있어야 합니다.<br/><br/>  온-프레미스 머신을 검색하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포합니다.<br/><br/> 처음으로 프로젝트를 만드는 [방법을 알아봅니다](create-manage-projects.md).<br/> 기존 프로젝트에 평가 도구를 추가하는 [방법을 알아봅니다](how-to-assess.md).<br/> [Hyper-V](how-to-set-up-appliance-hyper-v.md), [VMware](how-to-set-up-appliance-vmware.md) 또는 물리적 서버의 평가를 위해 Azure Migrate 어플라이언스를 설정하는 방법을 알아봅니다.
**Azure Government** | Azure Government에서는 종속성 시각화를 사용할 수 없습니다.
**Log Analytics** | Azure Migrate는 종속성 시각화에 대한 [Azure Monitor 로그](../azure-monitor/log-query/log-query-overview.md)의 [서비스 맵](../azure-monitor/insights/service-map.md) 솔루션을 사용합니다.<br/><br/> 신규 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결합니다. Azure Migrate 프로젝트에 대한 작업 영역은 추가된 후 수정할 수 없습니다. <br/><br/> 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> 작업 영역은 미국 동부, 동남 아시아 또는 서유럽 지역에 있어야 합니다. 다른 지역의 작업 영역은 프로젝트에 연결할 수 없습니다.<br/><br/> 작업 영역은 [서비스 맵 지원되는](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.<br/><br/> Log Analytics에서 Azure Migrate와 연결된 작업 영역에는 마이그레이션 프로젝트 키와 프로젝트 이름이 태그로 지정됩니다.
**필요한 에이전트** | 분석하려는 각 머신에서 다음 에이전트를 설치합니다.<br/><br/> [MMA(Microsoft Monitoring Agent) 업그레이드](../azure-monitor/platform/agent-windows.md)<br/> [종속성 에이전트](../azure-monitor/platform/agents-overview.md#dependency-agent)<br/><br/> 온-프레미스 머신이 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 다운로드하여 설치해야 합니다.<br/><br/> [종속성 에이전트](how-to-create-group-machine-dependencies.md#install-the-dependency-agent) 및 [MMA](how-to-create-group-machine-dependencies.md#install-the-mma)를 설치하는 방법에 대한 자세한 내용을 알아보세요.
**Log Analytics 작업 영역** | 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 현재 미국 동부, 동남 아시아 및 서유럽 지역에 있는 작업 영역을 지원합니다.<br/><br/>  작업 영역은 [서비스 맵 지원되는](../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) 지역에 있어야 합니다.<br/><br/> Azure Migrate 프로젝트에 대한 작업 영역은 추가된 후 수정할 수 없습니다.
**비용** | Azure Migrate 프로젝트와 Log Analytics 작업 영역을 연결한 날로부터 180일 동안은 서비스 맵 솔루션에서 요금이 부과되지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결된 Log Analytics 작업 영역 내에서 서비스 맵 이외의 다른 솔루션을 사용하면 Log Analytics에 대한 [표준 요금](https://azure.microsoft.com/pricing/details/log-analytics/)이 발생합니다.<br/><br/> Azure Migrate 프로젝트가 삭제될 때 작업 영역은 함께 삭제되지 않습니다. 프로젝트 삭제 후에는 서비스 맵 사용이 무료가 아니며 Log Analytics 작업 영역의 유료 계층에 따라 노드마다 요금이 부과됩니다.<br/><br/>Azure Migrate GA(일반 공급 - 2018년 2월 28일) 전에 만든 프로젝트가 있는 경우 추가 서비스 맵 요금이 발생했을 수 있습니다. 180일 후에만 지불되도록 하려면 GA 이전의 기존 작업 영역은 여전히 비용이 부과될 수 있으므로 새 프로젝트를 만드는 것이 좋습니다.
**관리** | 작업 영역에 에이전트를 등록할 때 Azure Migrate 프로젝트에서 제공하는 ID 및 키를 사용합니다.<br/><br/> Azure Migrate 외부에서 Log Analytics 작업 영역을 사용할 수 있습니다.<br/><br/> 연결된 Azure Migrate 프로젝트를 삭제하면 작업 영역이 자동으로 삭제되지 않습니다. [수동으로 삭제합니다](../azure-monitor/platform/manage-access.md).<br/><br/> Azure Migrate 프로젝트를 삭제하지 않는 한 Azure Migrate가 만든 작업 영역을 삭제하면 안 됩니다. 작업 영역을 삭제하면 종속성 시각화 기능이 정상적으로 작동하지 않습니다.
**인터넷 연결** | 머신이 인터넷에 연결되어 있지 않은 경우 Log Analytics 게이트웨이를 설치해야 합니다.
**Azure Government** | 에이전트 기반 종속성 분석은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

[물리적 서버 평가를 준비](tutorial-prepare-physical.md)합니다.
