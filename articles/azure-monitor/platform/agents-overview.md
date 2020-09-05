---
title: Azure 모니터링 에이전트 개요 | Microsoft Docs
description: 이 문서에서는 Azure 또는 하이브리드 환경에서 호스팅되는 가상 머신의 모니터링을 지원하는 사용 가능한 Azure 에이전트에 대한 자세한 개요를 제공합니다.
services: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 6eb4aee1cfe62b09210f62d016028485594a9474
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89000790"
---
# <a name="overview-of-azure-monitor-agents"></a>Azure Monitor 에이전트 개요

가상 컴퓨터 및 기타 계산 리소스에는 게스트 운영 체제 및 워크 로드의 성능 및 가용성을 측정 하는 데 필요한 모니터링 데이터를 수집 하는 에이전트가 필요 합니다. 이 문서에서는 Azure Monitor에서 사용 하는 에이전트를 설명 하 고 특정 환경에 대 한 요구 사항을 충족 하는 데 필요한 사항을 결정 하는 데 도움을 줍니다.

> [!NOTE]
> 현재 Azure Monitor 및 Log Analytics의 최근 통합으로 인해 Azure Monitor에는 여러 개의 에이전트가 있습니다. 기능이 겹칠 수 있지만 각각에는 고유한 기능이 있습니다. 요구 사항에 따라 가상 컴퓨터에서 하나 이상의 에이전트가 필요할 수 있습니다. 

특정 가상 컴퓨터에 대해 단일 에이전트로 완전히 충족 될 수 없는 특정 요구 사항 집합이 있을 수 있습니다. 예를 들어 Azure 진단 확장이 필요 하지만 Log Analytics 에이전트 및 종속성 에이전트가 필요한 VM용 Azure Monitor 기능을 활용 하려는 메트릭 경고를 사용할 수 있습니다. 이와 같은 경우 여러 에이전트를 사용할 수 있으며,이는 각각의 기능을 필요로 하는 고객을 위한 일반적인 시나리오입니다.

## <a name="summary-of-agents"></a>에이전트 요약

다음 표에서는 Windows 및 Linux에 대 한 Azure Monitor 에이전트의 빠른 비교를 제공 합니다. 각에 대 한 자세한 내용은 아래 섹션에 나와 있습니다.

> [!NOTE]
> Azure Monitor 에이전트는 현재 제한 된 기능의 미리 보기로 제공 됩니다. 이 테이블을 업데이트 합니다. 

### <a name="windows-agents"></a>Windows 에이전트

| | Azure Monitor 에이전트 (미리 보기) | 진단<br>확장 (WAD) | Log Analytics<br>에이전트 | 종속성<br>에이전트 |
|:---|:---|:---|:---|:---|
| **지원 되는 환경** | Azure | Azure | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 | 
| **에이전트 요구 사항**  | 없음 | 없음 | 없음 | Log Analytics 에이전트가 필요 합니다. |
| **수집되는 데이터** | 이벤트 로그<br>성능 | 이벤트 로그<br>ETW 이벤트<br>성능<br>파일 기반 로그<br>IIS 로그<br>.NET 앱 로그<br>크래시 덤프<br>에이전트 진단 로그 | 이벤트 로그<br>성능<br>파일 기반 로그<br>IIS 로그<br>인사이트 및 솔루션<br>기타 서비스 | 프로세스 종속성<br>네트워크 연결 메트릭 |
| **데이터 전송 대상** | Azure Monitor 로그<br>Azure Monitor 메트릭 | Azure Storage<br>Azure Monitor 메트릭<br>이벤트 허브 | Azure Monitor 로그 | Azure Monitor 로그<br>(Log Analytics 에이전트를 통해) |
| **서비스 및**<br>**요소**<br>**되지** | Log Analytics<br>메트릭 탐색기 | 메트릭 탐색기 | VM용 Azure Monitor<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM용 Azure Monitor<br>서비스 맵 |

### <a name="linux-agents"></a>Linux 에이전트

| | Azure Monitor 에이전트 (미리 보기) | 진단<br>확장 (꼬마) | Telegraf<br>에이전트 | Log Analytics<br>에이전트 | 종속성<br>에이전트 |
|:---|:---|:---|:---|:---|:---|
| **지원 되는 환경** | Azure | Azure | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 | Azure<br>기타 클라우드<br>온-프레미스 |
| **에이전트 요구 사항**  | 없음 | 없음 | 없음 | 없음 | Log Analytics 에이전트가 필요 합니다. |
| **수집되는 데이터** | syslog<br>성능 | syslog<br>성능 | 성능 | syslog<br>성능| 프로세스 종속성<br>네트워크 연결 메트릭 |
| **데이터 전송 대상** | Azure Monitor 로그<br>Azure Monitor 메트릭 | Azure Storage<br>이벤트 허브 | Azure Monitor 메트릭 | Azure Monitor 로그 | Azure Monitor 로그<br>(Log Analytics 에이전트를 통해) |
| **서비스 및**<br>**요소**<br>**되지** | Log Analytics<br>메트릭 탐색기 | | 메트릭 탐색기 | VM용 Azure Monitor<br>Log Analytics<br>Azure Automation<br>Azure Security Center<br>Azure Sentinel | VM용 Azure Monitor<br>서비스 맵 |


## <a name="azure-monitor-agent-preview"></a>Azure Monitor 에이전트 (미리 보기)
[Azure Monitor 에이전트](azure-monitor-agent-overview.md) 는 현재 미리 보기로 제공 되며 Windows 및 Linux 가상 컴퓨터에 대 한 Log Analytics 에이전트 및 Telegraf 에이전트를 대체 합니다. Azure Monitor 로그와 Azure Monitor 메트릭 모두에 데이터를 보낼 수 있으며, 각 에이전트에 대 한 데이터 수집 및 대상을 구성 하는 보다 확장성 있는 방법을 제공 하는 [DCR (데이터 수집 규칙)](data-collection-rule-overview.md) 를 사용 합니다.

다음 작업을 수행 해야 하는 경우 Azure Monitor 에이전트를 사용 합니다.

- Azure의 모든 가상 컴퓨터, 다른 클라우드 또는 온-프레미스에서 게스트 로그 및 메트릭을 수집 합니다. (Azure는 미리 보기로만 제공 됩니다.)
- Azure Monitor를 사용 하 여 분석을 위해 Azure Monitor 로그 및 Azure Monitor 메트릭에 데이터를 보냅니다. 
- 데이터를 보관을 위해 Azure Storage 보냅니다.
- [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md)를 사용 하 여 타사 도구로 데이터를 보냅니다.
- [Azure Security Center](../../security-center/security-center-intro.md) 또는 [Azure 센티널](../../sentinel/overview.md)을 사용 하 여 가상 컴퓨터의 보안을 관리 합니다. (미리 보기에서는 사용할 수 없습니다.)

Azure Monitor 에이전트의 제한 사항은 다음과 같습니다.

- 현재 공개 미리 보기로 제공 됩니다. 공개 미리 보기 중 제한 사항 목록은 [현재 제한 사항](azure-monitor-agent-overview.md#current-limitations) 을 참조 하세요.

## <a name="log-analytics-agent"></a>Log Analytics 에이전트

[Log Analytics 에이전트](log-analytics-agent.md) 는 게스트 운영 체제의 모니터링 데이터와 Azure, 다른 클라우드 공급자 및 온-프레미스의 가상 컴퓨터 작업을 수집 합니다. Log Analytics 작업 영역에 데이터를 보냅니다. Log Analytics 에이전트는 System Center Operations Manager에서 사용 하는 것과 동일한 에이전트 이며, 에이전트 컴퓨터를 관리 그룹과 통신 하 고 동시에 Azure Monitor 멀티홈 수 있습니다. 이 에이전트는 Azure의 Azure Monitor 및 기타 서비스에 대 한 특정 정보에도 필요 합니다.


> [!NOTE]
> Windows 용 Log Analytics 에이전트를 종종 Microsoft Monitoring Agent (MMA) 라고 합니다. Linux 용 Log Analytics 에이전트를 OMS 에이전트 라고 합니다.



다음 작업을 수행 해야 하는 경우 Log Analytics 에이전트를 사용 합니다.

* Azure 내부 또는 외부의 가상 또는 물리적 컴퓨터에서 로그 및 성능 데이터를 수집 합니다. 
* Log Analytics 작업 영역으로 데이터를 전송 하 여 [로그 쿼리와](../log-query/log-query-overview.md)같은 [Azure Monitor 로그](data-platform-logs.md#what-can-you-do-with-azure-monitor-logs) 에서 지원 되는 기능을 활용 합니다.
* 규모에 따라 가상 컴퓨터를 모니터링 하 고 다른 리소스 및 외부 프로세스에 대 한 프로세스 및 종속성을 모니터링 하는 데 사용할 수 있는 [VM용 Azure Monitor](../insights/vminsights-overview.md) 을 사용 합니다.  
* [Azure Security Center](../../security-center/security-center-intro.md) 또는 [Azure 센티널](../../sentinel/overview.md)을 사용 하 여 가상 컴퓨터의 보안을 관리 합니다.
* [Azure Automation 업데이트 관리](../../automation/update-management/update-mgmt-overview.md), [Azure Automation 상태 구성](../../automation/automation-dsc-overview.md)또는 [Azure Automation 변경 내용 추적 및 인벤토리](../../automation/change-tracking.md) 를 사용 하 여 Azure vm의 포괄적인 관리 제공
* 다른 [솔루션](../monitor-reference.md#insights-and-core-solutions) 을 사용 하 여 특정 서비스 또는 응용 프로그램을 모니터링할 수 있습니다.

Log Analytics 에이전트의 제한 사항은 다음과 같습니다.

- Azure Monitor 메트릭, Azure Storage 또는 Azure Event Hubs에 데이터를 보낼 수 없습니다.
- 개별 에이전트에 대해 고유한 모니터링 정의를 구성 하기 어렵습니다.
- 각 가상 컴퓨터에는 고유한 구성이 있으므로 대규모로 관리 하기가 어렵습니다.

## <a name="azure-diagnostics-extension"></a>Azure 진단 확장

[Azure 진단 확장](diagnostics-extension-overview.md) 은 게스트 운영 체제에서 모니터링 데이터를 수집 하 고 Azure virtual machines 및 기타 계산 리소스의 작업을 수집 합니다. 주로 데이터를 Azure Storage 수집 하지만 데이터 싱크를 정의 하 여 Azure Monitor 메트릭과 Azure Event Hubs 같은 다른 대상으로 데이터를 보낼 수도 있습니다.

다음 작업을 수행 해야 하는 경우 Azure 진단 확장을 사용 합니다.

- 보관을 위해 Azure Storage 데이터를 보내거나 [Azure Storage 탐색기](../../vs-azure-tools-storage-manage-with-storage-explorer.md)와 같은 도구를 사용 하 여 분석 합니다.
- [메트릭 탐색기](metrics-getting-started.md) 를 사용 하 여 분석 하 고, 거의 실시간 [메트릭 경고](./alerts-metric-overview.md) 및 [자동 크기 조정](autoscale-overview.md) (Windows에만 해당)과 같은 기능을 활용 하기 위해 데이터를 [Azure Monitor 메트릭에](data-platform-metrics.md) 보냅니다.
- [Azure Event Hubs](diagnostics-extension-stream-event-hubs.md)를 사용 하 여 타사 도구로 데이터를 보냅니다.
- [부팅 진단을](../../virtual-machines/troubleshooting/boot-diagnostics.md) 수집 하 여 VM 부팅 문제를 조사 합니다.

Azure 진단 확장의 제한 사항은 다음과 같습니다.

- Azure 리소스에만 사용할 수 있습니다.
- Azure Monitor 로그에 데이터를 전송 하는 기능이 제한 됩니다.

## <a name="telegraf-agent"></a>Telegraf 에이전트

[InfluxData Telegraf 에이전트](collect-custom-metrics-linux-telegraf.md) 는 Linux 컴퓨터에서 Azure Monitor 메트릭에 대 한 성능 데이터를 수집 하는 데 사용 됩니다.

다음 작업을 수행 해야 하는 경우 Telegraf 에이전트를 사용 합니다.

* [메트릭 탐색기](metrics-getting-started.md) 를 사용 하 여 분석 하 고, 거의 실시간 [메트릭 경고](./alerts-metric-overview.md) 및 [자동 크기 조정](autoscale-overview.md) (Linux만 해당)과 같은 기능을 활용 하기 위해 데이터를 [Azure Monitor 메트릭에](data-platform-metrics.md) 보냅니다. 



## <a name="dependency-agent"></a>종속성 에이전트

종속성 에이전트는 가상 컴퓨터 및 외부 프로세스 종속성에서 실행 중인 프로세스에 대 한 검색 된 데이터를 수집 합니다. 

다음 작업을 수행 해야 하는 경우 종속성 에이전트를 사용 합니다.

* 지도 기능 [VM용 Azure Monitor](../insights/vminsights-overview.md) 또는 [서비스 맵](../insights/service-map.md) 솔루션을 사용 합니다.

종속성 에이전트를 사용 하는 경우 다음 사항을 고려 하세요.

- 종속성 에이전트를 사용 하려면 Log Analytics 에이전트가 동일한 가상 컴퓨터에 설치 되어 있어야 합니다.
- Linux Vm에서는 Azure 진단 확장 전에 Log Analytics 에이전트를 설치 해야 합니다.

## <a name="virtual-machine-extensions"></a>가상 머신 확장

[Windows](../../virtual-machines/extensions/oms-windows.md) 및 [Linux](../../virtual-machines/extensions/oms-linux.md) 용 Log Analytics 확장은 Azure 가상 컴퓨터에 Log Analytics 에이전트를 설치 합니다. [Windows](../../virtual-machines/extensions/agent-dependency-windows.md) 및 [Linux](../../virtual-machines/extensions/agent-dependency-linux.md) 용 Azure Monitor 종속성 확장은 Azure virtual machines에 종속성 에이전트를 설치 합니다. 이러한 에이전트는 위에서 설명한 것과 동일한 에이전트 이지만 [가상 머신 확장](../../virtual-machines/extensions/overview.md)을 통해 관리할 수 있습니다. 가능 하면 언제 든 지 확장을 사용 하 여 에이전트를 설치 하 고 관리 해야 합니다.


## <a name="supported-operating-systems"></a>지원되는 운영 체제
다음 표에서는 Azure Monitor 에이전트에서 지원 되는 운영 체제를 나열 합니다. 각 에이전트에 대 한 설명서에서 고유한 고려 사항과 설치 프로세스를 참조 하십시오. 지원 되는 운영 체제에 대 한 Telegraf 설명서를 참조 하세요. 모든 운영 체제는 x 64로 간주 됩니다. x86은 모든 운영 체제에서 지원 되지 않습니다.

### <a name="windows"></a>Windows

| 운영 체제 | Azure Monitor 에이전트 | Log Analytics 에이전트 | 종속성 에이전트 | 진단 확장 | 
|:---|:---:|:---:|:---:|:---:|
| Windows Server 2019                                      | X | X | X | X |
| Windows Server 2016                                      | X | X | X | X |
| Windows Server 2016 Core                                 |   |   |   | X |
| Windows Server 2012 R2                                   | X | X | X | X |
| Windows Server 2012                                      | X | X | X | X |
| Windows Server 2008 R2                                   |   | X | X | X |
| Windows 10 Enterprise<br>(다중 세션 포함) 및 Pro  |   | X | X | X |
| Windows 8 Enterprise 및 Pro                             |   | X | X |   |
| Windows 7 SP1                                            |   | X | X |   |


### <a name="linux"></a>Linux

| 운영 체제 | Azure Monitor 에이전트 | Log Analytics 에이전트 | 종속성 에이전트 | 진단 확장 | 
|:---|:---:|:---:|:---:|:---:
| Amazon Linux 2017.09                                     |   | X |   |   |
| CentOS Linux 7                                           | X | X |   | X |
| CentOS Linux 7.6                                         | X | X | X | X |
| CentOS Linux 6                                           | X | X |   |   |
| CentOS Linux 6.5 이상                                        | X | X |   | X |
| Debian 10                                                | X |   |   |   |
| Debian 9                                                 | X | X | x | X |
| Debian 8                                                 |   | X | X | X |
| Debian 7                                                 |   |   |   | X |
| OpenSUSE 13.1 이상                                           |   |   |   | X |
| Oracle Linux 7                                           | X | X |   | X |
| Oracle Linux 6                                           | X | X |   |   |
| Oracle Linux 6.4 이상                                        | X | X |   | X |
| Red Hat Enterprise Linux Server 8                        | X | X |   |   |
| Red Hat Enterprise Linux Server 7                        | X | X | X | X |
| Red Hat Enterprise Linux Server 6                        | X | X | X |   |
| Red Hat Enterprise Linux Server 6.7 이상                     | X | X | X | X |
| SUSE Linux Enterprise Server 15                          | X | X | X |   |
| SUSE Linux Enterprise Server 12                          | X | X | X | X |
| Ubuntu 18.04 LTS                                         | X | X | X | X |
| Ubuntu 16.04 LTS                                         | X | X | X | X |
| Ubuntu 14.04 LTS                                         | X | X |   | X |


#### <a name="dependency-agent-linux-kernel-support"></a>종속성 에이전트 Linux 커널 지원
종속성 에이전트는 커널 수준에서 작동 하므로 커널 버전에도 종속 됩니다. 다음 표에는 종속성 에이전트에 대 한 주 및 부 Linux OS 릴리스와 지원 되는 커널 버전이 나와 있습니다.

| 분포 | OS 버전 | 커널 버전 |
|:---|:---|:---|
|  Red Hat Linux 7   | 7.6     | 3.10.0-957  |
|                    | 7.5     | 3.10.0-862  |
|                    | 7.4     | 3.10.0-693  |
| Red Hat Linux 6    | 6.10    | 2.6.32-754 |
|                    | 6.9     | 2.6.32-696  |
| CentOSPlus         | 6.10    | 2.6.32 커널을-754.3.5<br>2.6.32 커널을-696.30.1 |
|                    | 6.9     | 2.6.32 커널을-696.30.1<br>2.6.32 커널을-696.18.7 |
| Ubuntu Server      | 18.04   | 5.3.0-1020<br>5.0 (Azure 조정 커널 포함)<br>4.18* <br> 4.15* |
|                    | 16.04.3 | 4.15. * |
|                    | 16.04   | 4.13.\*<br>4.11.\*<br>4.10.\*<br>4.8.\*<br>4.4.\* |
| SUSE Linux 12 Enterprise Server | 12 SP4 | 4.12. * (Azure 조정 커널 포함) |
|                                 | 12 SP3 | 4.4.* |
|                                 | 12 SP2 | 4.4.* |
| Debian                          | 9      | 4.9  | 


## <a name="next-steps"></a>다음 단계

각 에이전트에 대 한 자세한 내용은 다음을 참조 하세요.

- [Log Analytics 에이전트 개요](log-analytics-agent.md)
- [Azure Diagnostics 확장 개요](diagnostics-extension-overview.md)
- [InfluxData Telegraf 에이전트를 사용하여 Linux VM에 대한 사용자 지정 메트릭 수집](collect-custom-metrics-linux-telegraf.md)

