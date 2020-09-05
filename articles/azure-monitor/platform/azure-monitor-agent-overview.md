---
title: Azure Monitor 에이전트 개요
description: 가상 컴퓨터의 게스트 운영 체제에서 모니터링 데이터를 수집 하는 Azure Monitor 에이전트 (AMA)의 개요입니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/10/2020
ms.openlocfilehash: ff70beef89f6db240db244de1e11e54193858be0
ms.sourcegitcommit: e0785ea4f2926f944ff4d65a96cee05b6dcdb792
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88705778"
---
# <a name="azure-monitor-agent-overview-preview"></a>Azure Monitor 에이전트 개요 (미리 보기)
Azure Monitor 에이전트 (AMA)는 가상 컴퓨터의 게스트 운영 체제에서 모니터링 데이터를 수집 하 여 Azure Monitor에 전달 합니다. 이 문서에서는 Azure Monitor 에이전트를 설치 하는 방법 및 데이터 수집을 구성 하는 방법을 비롯 하 여 에이전트에 대 한 개요를 제공 합니다.

## <a name="relationship-to-other-agents"></a>다른 에이전트와의 관계
Azure Monitor 에이전트는 Azure Monitor에서 현재 사용 하 고 있는 다음 에이전트를 대체 하 여 가상 머신에서 게스트 데이터를 수집 합니다.

- [Log Analytics 에이전트](log-analytics-agent.md) -Log Analytics 작업 영역으로 데이터를 보내고 VM용 Azure Monitor 및 모니터링 솔루션을 지원 합니다.
- [진단 확장](diagnostics-extension-overview.md) -Azure Monitor 메트릭 (Windows에만 해당), Azure Event Hubs 및 Azure Storage에 데이터를 보냅니다.
- [Telegraf 에이전트](collect-custom-metrics-linux-telegraf.md) -Azure Monitor 메트릭에 데이터를 보냅니다 (Linux에만 해당).

이 기능을 단일 에이전트로 통합 하는 것 외에도 Azure Monitor 에이전트는 기존 에이전트에 대해 다음과 같은 이점을 제공 합니다.

- 모니터링의 범위입니다. 서로 다른 Vm 집합의 다양 한 데이터 집합에 대 한 컬렉션을 중앙에서 구성 합니다.  
- Linux 멀티 호 밍: Linux Vm에서 여러 작업 영역으로 데이터를 보냅니다.
- Windows 이벤트 필터링: XPATH 쿼리를 사용 하 여 수집 되는 Windows 이벤트를 필터링 합니다.
- 향상 된 확장 관리: Azure Monitor 에이전트는 현재 Log Analytics 에이전트의 관리 팩 및 Linux 플러그 인 보다 투명 하 고 제어 되는 확장성을 처리 하는 새로운 방법을 사용 합니다.

### <a name="changes-in-data-collection"></a>데이터 컬렉션의 변경 내용
기존 에이전트에 대 한 데이터 수집을 정의 하는 방법은 개별적으로 서로 다르며 Azure Monitor 에이전트를 사용 하 여 해결할 수 있는 과제가 있습니다.

- Log Analytics 에이전트는 Log Analytics 작업 영역에서 구성을 가져옵니다. 이는 중앙 집중식으로 구성 하기 쉽지만, 다른 가상 컴퓨터에 대 한 독립적인 정의를 정의 하기는 어렵습니다. Log Analytics 작업 영역에만 데이터를 보낼 수 있습니다.

- 진단 확장에는 각 가상 컴퓨터에 대 한 구성이 있습니다. 이는 서로 다른 가상 컴퓨터에 대 한 독립적인 정의를 정의 하기 쉽지만 중앙에서 관리 하기 어렵습니다. Azure Monitor 메트릭, Azure Event Hubs 또는 Azure Storage에만 데이터를 보낼 수 있습니다. Linux 에이전트의 경우 Azure Monitor 메트릭에 데이터를 전송 하려면 오픈 소스 Telegraf 에이전트가 필요 합니다.

Azure Monitor 에이전트는 [DCR (데이터 수집 규칙)](data-collection-rule-overview.md) 를 사용 하 여 각 에이전트에서 수집할 데이터를 구성 합니다. 데이터 수집 규칙을 사용 하면 컴퓨터의 하위 집합에 대해 고유 하 고 범위가 지정 된 구성을 사용 하면서도 컬렉션 설정의 관리 효율성을 높일 수 있습니다. 작업 영역과 독립적 이며, 한 번 정의 하 여 컴퓨터 및 환경에서 다시 사용할 수 있도록 하는 가상 컴퓨터와 독립적입니다. [Azure Monitor 에이전트에 대 한 데이터 수집 구성 (미리 보기)](data-collection-rule-azure-monitor-agent.md)을 참조 하세요.



## <a name="current-limitations"></a>현재 제한 사항
Azure Monitor 에이전트의 공개 미리 보기 중에는 다음과 같은 제한 사항이 적용 됩니다.

- Azure Monitor 에이전트는 VM용 Azure Monitor 및 Azure Security Center와 같은 솔루션 및 통찰력을 지원 하지 않습니다. 현재 지원 되는 유일한 시나리오는 구성 하는 데이터 수집 규칙을 사용 하 여 데이터를 수집 하는 것입니다. 
- 데이터 수집 규칙은 대상으로 사용 되는 Log Analytics 작업 영역과 동일한 지역에 만들어야 합니다.
- 현재 Azure virtual machines만 지원 됩니다. 온-프레미스 가상 머신, 가상 머신 확장 집합, 서버에 대 한 Arc, Azure Kubernetes Service 및 기타 계산 리소스 유형은 현재 지원 되지 않습니다.
- 가상 컴퓨터에는 다음 HTTPS 끝점에 대 한 액세스 권한이 있어야 합니다.
  - *.ods.opinsights.azure.com
  - *. ingest.monitor.azure.com
  - *. control.monitor.azure.com


## <a name="coexistence-with-other-agents"></a>다른 에이전트와의 공존
Azure Monitor 에이전트는 기존 에이전트와 함께 사용할 수 있으므로 평가 또는 마이그레이션 중에 기존 기능을 계속 사용할 수 있습니다. 이는 기존 솔루션 지원에서 공개 미리 보기의 제한 사항 때문에 특히 중요 합니다. 중복 데이터를 수집 하는 경우 쿼리 결과를 왜곡 하 고 데이터 수집 및 보존에 대 한 추가 요금이 발생할 수 있으므로 주의 해야 합니다.

예를 들어 VM용 Azure Monitor Log Analytics 에이전트를 사용 하 여 성능 데이터를 Log Analytics 작업 영역으로 보냅니다. 에이전트에서 Windows 이벤트 및 Syslog 이벤트를 수집 하도록 작업 영역을 구성 했을 수도 있습니다. Azure Monitor 에이전트를 설치 하 고 동일한 이벤트 및 성능 데이터에 대 한 데이터 수집 규칙을 만드는 경우 중복 된 데이터가 생성 됩니다.


## <a name="costs"></a>비용
Azure Monitor 에이전트에 대 한 비용은 없지만 데이터 수집에 대 한 요금이 발생할 수 있습니다. Log Analytics 데이터 수집 및 보존 및 고객 메트릭에 대 한 자세한 내용은 [Azure Monitor 가격 책정](https://azure.microsoft.com/pricing/details/monitor/) 을 참조 하세요.

## <a name="data-sources-and-destinations"></a>데이터 원본 및 대상
다음 표에서는 데이터 수집 규칙을 사용 하 여 현재 Azure Monitor 에이전트로 수집할 수 있는 데이터 형식과 해당 데이터를 보낼 수 있는 위치를 나열 합니다. Azure Monitor 에이전트를 사용 하 여 다른 종류의 데이터를 수집 하는 정보, 솔루션 및 기타 솔루션 목록은 [Azure Monitor에서 모니터링 되는 내용](../monitor-reference.md) 을 참조 하세요.


Azure Monitor 에이전트는 Azure Monitor 메트릭 또는 Azure Monitor 로그를 지 원하는 Log Analytics 작업 영역에 데이터를 보냅니다.

| 데이터 원본 | Destinations | Description |
|:---|:---|:---|
| 성능        | Azure Monitor 메트릭<br>Log Analytics 작업 영역 | 운영 체제 및 워크로드의 여러 측면에서 성능을 측정하는 숫자 값입니다. |
| Windows 이벤트 로그 | Log Analytics 작업 영역 | Windows 이벤트 로깅 시스템으로 전송되는 정보입니다. |
| syslog             | Log Analytics 작업 영역 | Linux 이벤트 로깅 시스템으로 전송되는 정보입니다. |


## <a name="supported-operating-systems"></a>지원되는 운영 체제
현재 Azure Monitor 에이전트에서 지원 되는 운영 체제는 다음과 같습니다.

### <a name="windows"></a>Windows 
  - Windows Server 2019
  - Windows Server 2016
  - Windows Server 2012
  - Windows Server 2012 R2

### <a name="linux"></a>Linux
  - CentOS 6<sup>1</sup>, 7
  - Debian 9, 10
  - Oracle Linux 6<sup>1</sup>, 7
  - RHEL 6<sup>1</sup>, 7, 8
  - SLES 11, 12, 15
  - Ubuntu 14.04 LTS, 16.04 LTS, 18.04 LTS

> [!IMPORTANT]
> <sup>1</sup> 이러한 배포에서 Syslog 데이터를 보내려면 rsyslog를 제거 하 고 syslog를 설치 해야 합니다.


## <a name="security"></a>보안
Azure Monitor 에이전트에는 키가 필요 하지 않지만 대신 [시스템 할당 관리 id](../../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#system-assigned-managed-identity)가 필요 합니다. 에이전트를 배포 하기 전에 각 가상 머신에서 시스템 할당 관리 id를 사용 하도록 설정 해야 합니다.


## <a name="install-the-azure-monitor-agent"></a>Azure Monitor 에이전트 설치
Azure Monitor 에이전트는 다음 표의 세부 정보를 사용 하 여 [AZURE VM 확장](../../virtual-machines/extensions/overview.md) 으로 구현 됩니다. 

| 속성 | Windows | Linux |
|:---|:---|:---|
| 게시자 | Microsoft. Azure 모니터  | Microsoft. Azure 모니터 |
| 유형      | AzureMonitorWindowsAgent | AzureMonitorLinuxAgent  |
| TypeHandlerVersion  | 1.0 | 1.5 |

PowerShell 또는 CLI를 사용 하 여 다음을 포함 하 여 가상 머신 에이전트를 설치 하는 방법 중 하나를 사용 하 여 Azure Monitor 에이전트를 설치 합니다. 또는 [Azure Monitor 에이전트에 대 한 데이터 수집 구성 (미리 보기)](data-collection-rule-azure-monitor-agent.md#create-using-the-azure-portal)에 설명 된 절차에 따라 포털을 사용 하 여 Azure 구독의 가상 컴퓨터에서 에이전트를 설치 하 고 데이터 수집을 구성할 수 있습니다.

### <a name="windows"></a>Windows

# <a name="cli"></a>[CLI](#tab/CLI1)

```azurecli
az vm extension set --name AzureMonitorWindowsAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell1)

```powershell
Set-AzVMExtension -Name AMAWindows -ExtensionType AzureMonitorWindowsAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---


### <a name="linux"></a>Linux

# <a name="cli"></a>[CLI](#tab/CLI2)

```azurecli
az vm extension set --name AzureMonitorLinuxAgent --publisher Microsoft.Azure.Monitor --ids {resource ID of the VM}

```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell2)

```powershell
Set-AzVMExtension -Name AMALinux -ExtensionType AzureMonitorLinuxAgent -Publisher Microsoft.Azure.Monitor -ResourceGroupName {Resource Group Name} -VMName {VM name} -Location eastus
```
---

## <a name="next-steps"></a>다음 단계

- 에이전트에서 데이터를 수집 하 고 Azure Monitor로 보내기 위한 [데이터 수집 규칙을 만듭니다](data-collection-rule-azure-monitor-agent.md) .
