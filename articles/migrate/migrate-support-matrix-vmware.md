---
title: Azure Migrate의 VMware 평가 지원
description: Azure Migrate의 VMware 평가 지원에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 01/08/2020
ms.openlocfilehash: 19ed506228bac425ad05edee1586740e6c33f69e
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121331"
---
# <a name="support-matrix-for-vmware-assessment"></a>VMware 평가를 위한 지원 매트릭스 

이 문서에서는 [Azure Migrate: 서버 평가](migrate-services-overview.md#azure-migrate-server-migration-tool)를 사용 하 여 VMware vm을 평가 하기 위한 지원 설정 및 제한 사항을 요약 합니다. VMware Vm을 Azure로 마이그레이션하는 방법에 대 한 정보를 찾고 있는 경우 [마이그레이션 지원 매트릭스](migrate-support-matrix-vmware-migration.md)를 검토 하세요.

## <a name="overview"></a>개요

이 문서를 사용 하 여 Azure로의 마이그레이션에 대 한 온-프레미스 컴퓨터를 평가 하려면 Azure Migrate: 서버 평가 도구를 Azure Migrate 프로젝트에 추가 합니다. [Azure Migrate 어플라이언스](migrate-appliance.md)를 배포 합니다. 어플라이언스는 온-프레미스 컴퓨터를 지속적으로 검색 하 고 구성 및 성능 데이터를 Azure로 전송 합니다. 컴퓨터 검색 후에는 검색 된 컴퓨터를 그룹으로 수집 하 고 그룹에 대 한 평가를 실행 합니다.


## <a name="limitations"></a>제한 사항

**지원** | **세부 정보**
--- | ---
**평가 제한**| 단일 [프로젝트](migrate-support-matrix.md#azure-migrate-projects)에서 최대 35000 VMware vm을 검색 하 고 평가 합니다.
**프로젝트 제한** | Azure 구독에서 여러 프로젝트를 만들 수 있습니다. 프로젝트는 VMware Vm, Hyper-v Vm 및 물리적 서버를 평가 제한까지 포함할 수 있습니다.
**조사** | Azure Migrate 어플라이언스는 vCenter Server에서 VMware Vm을 1만 개까지 검색할 수 있습니다.
**평가** | 단일 그룹에 최대 35000 대의 컴퓨터를 추가할 수 있습니다.<br/><br/> 단일 평가에서 최대 35000 Vm을 평가할 수 있습니다.

평가에 대해 자세히 [알아보세요](concepts-assessment-calculation.md) .


## <a name="application-discovery"></a>애플리케이션 검색

컴퓨터를 검색 하는 것 외에도 Azure Migrate: 서버 평가는 컴퓨터에서 실행 되는 앱, 역할 및 기능을 검색할 수 있습니다. 앱 인벤토리를 검색 하면 온-프레미스 워크 로드에 맞게 조정 된 마이그레이션 경로를 식별 하 고 계획할 수 있습니다. 

**지원** | **세부 정보**
--- | ---
**조사** | 검색은 에이전트 없이 컴퓨터 게스트 자격 증명을 사용 하 고 WMI 및 SSH 호출을 사용 하 여 컴퓨터에 원격으로 액세스 합니다.
**지원 되는 컴퓨터** | 온-프레미스 VMware Vm.
**컴퓨터 운영 체제** | 모든 Windows 및 Linux 버전
**vCenter 자격 증명** | 읽기 전용 액세스 권한이 있는 vCenter Server 계정과 게스트 작업 > Virtual Machines에 대 한 권한이 설정 되어 있습니다.
**VM 자격 증명** | 현재에서는 모든 Windows server에 대해 하나의 자격 증명을 사용할 수 있으며 모든 Linux 서버에 대해 하나의 자격 증명을 사용할 수 있습니다.<br/><br/> Windows Vm에 대 한 게스트 사용자 계정과 모든 Linux Vm에 대 한 일반/일반 사용자 계정 (비 sudo 액세스)을 만듭니다.
**VMware 도구** | 검색 하려는 Vm에 VMware 도구를 설치 하 고 실행 해야 합니다. <br/> VMware tools 버전이 9.10-10.2.0 사이인 경우 10.2.0 이상으로 업그레이드 하세요.
**포트 액세스** | 검색 하려는 Vm을 실행 하는 ESXi 호스트에서 Azure Migrate 어플라이언스는 TCP 포트 443에 연결할 수 있어야 합니다.
**제한** | 앱 검색의 경우 어플라이언스 당 최대 1만 개를 검색할 수 있습니다. 

## <a name="vmware-requirements"></a>VMware 요구 사항

**VMware** | **세부 정보**
--- | ---
**vCenter Server** | 검색 하 고 평가 하려는 컴퓨터는 vCenter Server 버전 5.5, 6.0, 6.5 또는 6.7에서 관리 해야 합니다.
**사용 권한 (평가)** | 읽기 전용 계정을 vCenter Server 합니다.
**권한 (앱 검색)** | 읽기 전용 액세스 권한이 있는 vCenter Server 계정 및 가상 컴퓨터에 대 한 권한이 설정 된 **게스트 작업 >** 합니다.
**권한 (종속성 시각화)** | 읽기 전용 액세스 권한이 있는 센터 서버 계정 및 **게스트 작업** > **가상 컴퓨터** 에 대 한 권한이 설정 되어 있습니다.


## <a name="azure-migrate-appliance-requirements"></a>Azure Migrate 어플라이언스 요구 사항

Azure Migrate는 검색 및 평가를 위해 [Azure Migrate 어플라이언스](migrate-appliance.md) 를 사용 합니다. VMware에 대 한 어플라이언스는 OVA 템플릿을 사용 하 여 배포 되며 vCenter Server로 가져왔습니다. 

- VMware에 대 한 [어플라이언스 요구 사항](migrate-appliance.md#appliance---vmware) 에 대해 알아봅니다.
- 어플라이언스에서 액세스 해야 하는 [url](migrate-appliance.md#url-access) 에 대해 알아봅니다.

## <a name="port-access"></a>포트 액세스

**디바이스** | **연결**
--- | ---
기기가 | 어플라이언스에 대 한 원격 데스크톱 연결을 허용 하기 위해 TCP 포트 3389에서 인바운드 연결<br/><br/> URL을 사용 하 여 어플라이언스 관리 앱에 원격으로 액세스 하기 위한 포트 44368의 인바운드 연결: ```https://<appliance-ip-or-name>:44368``` <br/><br/>Azure Migrate에 검색 및 성능 메타 데이터를 보내기 위한 포트 443 (HTTPS), 5671 및 5672 (AMQP)의 아웃 바운드 연결
vCenter Server | 어플라이언스에서 평가를 위한 구성 및 성능 메타 데이터를 수집할 수 있도록 TCP 포트 443에서 인바운드 연결 <br/><br/> 어플라이언스는 기본적으로 포트 443의 vCenter에 연결 됩니다. VCenter 서버가 다른 포트에서 수신 대기 하는 경우 검색을 설정할 때 포트를 수정할 수 있습니다.
ESXi 호스트 | **[응용 프로그램 검색](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#application-discovery) 및 에이전트 없는 [종속성 시각화](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#agentless-dependency-visualization) 에만 필요 합니다.** <br/><br/> 어플라이언스는 TCP 포트 443의 ESXi 호스트에 연결 하 여 응용 프로그램을 검색 하 고 호스트에서 실행 되는 Vm에서 에이전트 없는 종속성 시각화를 실행 합니다.

## <a name="agent-based-dependency-visualization"></a>에이전트 기반 종속성 시각화

[종속성 시각화](concepts-dependency-visualization.md) 를 사용 하면 평가 하 고 마이그레이션하려는 컴퓨터 간에 종속성을 시각화할 수 있습니다. 에이전트 기반 시각화의 경우 요구 사항 및 제한 사항이 다음 표에 요약 되어 있습니다.


**요구 사항** | **세부 정보**
--- | ---
**배포** | 종속성 시각화를 배포 하기 전에 Azure Migrate: 서버 평가 도구를 프로젝트에 추가 하 여 Azure Migrate 프로젝트가 준비 되어 있어야 합니다. 온-프레미스 컴퓨터를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다.<br/><br/> Azure Government에서 종속성 시각화를 사용할 수 없습니다.
**서비스 맵** | 에이전트 기반 종속성 시각화는 [Azure Monitor 로그](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)에 [서비스 맵](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map) 솔루션을 사용 합니다.<br/><br/> 를 배포 하려면 새 또는 기존 Log Analytics 작업 영역을 Azure Migrate 프로젝트와 연결 합니다.
**Log Analytics 작업 영역** | 작업 영역은 Azure Migrate 프로젝트와 동일한 구독에 있어야 합니다.<br/><br/> Azure Migrate는 미국 동부, 동남 아시아 및 유럽 서부 지역에 거주 하는 작업 영역을 지원 합니다.<br/><br/>  작업 영역은 [서비스 맵 지원](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-enable-overview#prerequisites)되는 지역에 있어야 합니다.<br/><br/> Azure Migrate 프로젝트에 대 한 작업 영역은 추가 된 후 수정할 수 없습니다.
**요금** | 서비스 맵 솔루션은 Log Analytics 작업 영역을 연결한 날부터 Azure Migrate 프로젝트와 관련 하 여 처음 180 일에 대 한 요금을 부과 하지 않습니다.<br/><br/> 180일 후에는 표준 Log Analytics 요금이 적용됩니다.<br/><br/> 연결 된 Log Analytics 작업 영역에서 서비스 맵 이외의 다른 솔루션을 사용 하면 표준 Log Analytics 요금이 발생 합니다.<br/><br/> Azure Migrate 프로젝트를 삭제 하면 작업 영역이 삭제 되지 않습니다. 프로젝트를 삭제 한 후에는 서비스 맵 무료로 제공 되며 각 노드에는 Log Analytics 작업 영역의 유료 계층에 따라 요금이 부과 됩니다.
**에이전트** | 에이전트 기반 종속성 시각화를 사용 하려면 분석 하려는 각 컴퓨터에 두 개의 에이전트를 설치 해야 합니다.<br/><br/> - [MMA (Microsoft Monitoring agent)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)<br/><br/> [종속성 에이전트](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)를 - 합니다. 
**인터넷 연결** | 컴퓨터가 인터넷에 연결 되어 있지 않으면 Log Analytics 게이트웨이를 설치 해야 합니다.


## <a name="agentless-dependency-visualization"></a>에이전트 없는 종속성 시각화

이 옵션은 현재 미리 보기로 제공되고 있습니다. [자세히 알아봅니다](how-to-create-group-machine-dependencies-agentless.md). 요구 사항은 다음 표에 요약 되어 있습니다.

**요구 사항** | **세부 정보**
--- | ---
**배포** | 종속성 시각화를 배포 하기 전에 Azure Migrate: 서버 평가 도구를 프로젝트에 추가 하 여 Azure Migrate 프로젝트가 준비 되어 있어야 합니다. 온-프레미스 컴퓨터를 검색 하도록 Azure Migrate 어플라이언스를 설정한 후 종속성 시각화를 배포 합니다.
**VM 지원** | 현재 VMware Vm에 대해서만 지원 됩니다.
**Windows VM** | Windows Server 2016<br/> Windows Server 2012 R2<br/> Windows Server 2012<br/> Windows Server 2008 R2(64비트)
**Linux VM** | Red Hat Enterprise Linux 7, 6, 5<br/> Ubuntu Linux 14.04, 16.04<br/> Debian 7, 8<br/> Oracle Linux 6, 7<br/> CentOS 5, 6, 7.
**Windows 계정** |  시각화에는 로컬 또는 도메인 관리자 계정이 필요 합니다.
**Linux 계정** | 시각화에는 루트 권한이 있는 사용자 계정이 필요 합니다.<br/><br/> 또는 사용자 계정에/bin/netstat 및/bin/ls 파일에 대 한 다음 권한이 필요 합니다. CAP_DAC_READ_SEARCH 및 CAP_SYS_PTRACE.
**VM 에이전트** | Vm에 에이전트가 필요 하지 않습니다.
**VMware 도구** | 분석 하려는 Vm에 VMware 도구를 설치 하 고 실행 해야 합니다. <br/> VMware tools 버전이 9.10-10.2.0 사이인 경우 10.2.0 이상으로 업그레이드 하세요.
**vCenter 자격 증명** | 읽기 전용 액세스 권한이 있는 vCenter Server 계정과 게스트 작업 > Virtual Machines에 대 한 권한이 설정 되어 있습니다.
**포트 액세스** | 분석 하려는 Vm을 실행 하는 ESXi 호스트에서 Azure Migrate 어플라이언스는 TCP 포트 443에 연결할 수 있어야 합니다.



## <a name="next-steps"></a>다음 단계

- 평가 만들기에 대 한 모범 사례를 [검토](best-practices-assessment.md) 합니다.
- [VMware 평가를 준비](tutorial-prepare-vmware.md) 합니다.
