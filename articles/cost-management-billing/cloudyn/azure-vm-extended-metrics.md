---
title: Azure Virtual Machines에 대한 확장 메트릭 추가
description: 이 문서를 통해 Azure VM에 대해 확장 진단 메트릭을 사용하고 구성할 수 있습니다.
author: bandersmsft
ms.reviewer: vitavor
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 89084f0631b52631708db68a11595cb24d1b9fee
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88690122"
---
# <a name="add-extended-metrics-for-azure-virtual-machines"></a>Azure Virtual Machines에 대한 확장 메트릭 추가

Cloudyn은 Azure VM의 Azure 메트릭 데이터를 사용하여 리소스 관련 상세 정보를 표시합니다. 성능 카운터라고도 하는 메트릭 데이터는 Cloudyn에서 보고서를 생성하는 데 사용됩니다. 그러나 Cloudyn은 자동으로 게스트 VM에서 모든 Azure 메트릭 데이터를 수집하는 것이 아니므로 메트릭 수집을 사용하도록 설정해야 합니다. 이 문서를 통해 Azure VM에 대해 추가 진단 메트릭을 사용하고 구성할 수 있습니다.

메트릭 수집을 사용하도록 설정한 후에는 다음이 가능합니다.

- VM이 메모리, 디스크 및 CPU 한도에 근접하는 시점을 압니다.
- 사용 추세와 비정상 상태를 검색합니다.
- 사용에 따라  크기를 조정하여 비용을 제어합니다.
- Cloudyn에서 비용 효과적인 크기 조정 최적화 권장 사항을 가져옵니다.

예를 들어 Azure VM의 CPU %와 메모리 %를 모니터링하고자 할 수 있습니다. Azure VM 메트릭은 _백분율 CPU_ 및 _\Memory\% 사용 중인 커밋된 바이트_에 해당합니다.

> [!NOTE]
> 확장 메트릭 데이터 수집은 Azure 게스트 수준 모니터링을 통해서만 지원됩니다. Cloudyn은 [Log Analytics 에이전트](../../azure-monitor/platform/agents-overview.md)와 호환되지 않습니다.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="determine-whether-extended-metrics-are-enabled"></a>확장 메트릭을 사용하도록 설정되었는지 확인

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. **가상 머신**에서 VM을 선택한 다음, **모니터링**에서 **메트릭**을 선택합니다. 사용 가능한 메트릭 목록이 표시됩니다.
3. 일부 메트릭을 선택하면 그래프가 해당 항목에 대한 데이터를 표시합니다.  
    ![예제 메트릭 - 호스트 백분율 CPU](./media/azure-vm-extended-metrics/metric01.png)

앞의 예제에서 제한된 표준 메트릭 집합을 호스트에 사용할 수 있지만 메모리 메트릭은 사용할 수 없습니다. 메모리 메트릭은 확장 메트릭의 일부입니다. 이 경우 VM에 대해 확장 메트릭이 사용하도록 설정되지 않았습니다. 확장 메트릭을 사용하도록 설정하려면 몇 가지 추가적인 단계를 수행해야 합니다. 다음 정보에서는 이를 사용하기 위한 단계를 안내합니다.

## <a name="enable-extended-metrics-in-the-azure-portal"></a>Azure Portal에서 확장 메트릭스 사용

표준 메트릭은 호스트 컴퓨터 메트릭입니다. _백분율 CPU_ 메트릭이 한 예입니다. 게스트 VM의 기본 메트릭도 있는데 역시 확장 메트릭이라고 합니다. 확장 메트릭의 예로는 _\Memory\% 사용 중인 커밋된 바이트_ 및 _\Memory\Available Bytes_가 있습니다.

확장 메트릭을 사용하도록 설정하는 방법은 간단합니다. 각 VM에 대해 게스트 수준 모니터링을 사용하도록 설정합니다. 게스트 수준 모니터링을 사용하면 Azure 진단 에이전트가 VM에 설치됩니다. 기본적으로 확장 메트릭의 기본 집합이 추가됩니다. 다음 프로세스는 클래식 및 기본 VM에서 같고, Windows 및 Linux VM에서도 동일합니다.

Azure와 Linux 게스트 수준 모니터링 모두 스토리지 계정이 필요하다는 점에 유의합니다. 게스트 수준 모니터링을 사용하면 기존 스토리지 계정을 선택하지 않은 경우 계정이 하나 만들어집니다.

### <a name="enable-guest-level-monitoring-on-existing-vms"></a>기존 VM에서 게스트 수준 모니터링 사용

1. **가상 머신**에서 VM 목록을 확인한 다음, VM을 선택합니다.
2. **모니터링** 아래에서 **진단 설정**을 선택합니다.
3. 진단 설정 페이지에서 **게스트 수준 모니터링 사용**을 클릭합니다.  
    ![개요 페이지에서 게스트 수준 모니터링 사용](./media/azure-vm-extended-metrics/enable-guest-monitoring.png)
4. 몇 분 후 VM에 Azure 진단 에이전트가 설치되어 있습니다. 기본 메트릭 집합이 추가됩니다. 페이지를 새로 고칩니다. 개요 탭에 추가 성능 카운터가 표시됩니다.
5. 모니터링에서 **메트릭**을 선택합니다.
6. **메트릭 네임스페이스**의 메트릭 차트에서 **게스트(클래식)** 을 선택합니다.
7. 메트릭 목록에서 게스트 VM에 대해 모든 사용 가능한 성능 카운터를 볼 수 있습니다.  
    ![예제 확장 메트릭 목록](./media/azure-vm-extended-metrics/extended-metrics.png)

### <a name="enable-guest-level-monitoring-on-new-vms"></a>새 VM에서 게스트 수준 모니터링 사용

새 VM을 만들 때는 관리 탭에서 **OS 게스트 진단**에 대해 **사용**을 선택합니다.

![게스트 OS 진단을 켬으로 설정](./media/azure-vm-extended-metrics/new-enable-diag.png)

Azure Virtual Machines에 대해 확장 메트릭을 활성화하는 방법은 [Azure Linux 에이전트 이해 및 사용](../../virtual-machines/extensions/agent-linux.md)과 [Azure Virtual Machine 에이전트 개요](../../virtual-machines/extensions/agent-windows.md)를 참조하세요.

## <a name="resource-manager-credentials"></a>리소스 관리자 자격 증명

확장 메트릭을 사용하도록 설정한 후에는 Cloudyn이 [리소스 관리자 자격 증명](../../cost-management/activate-subs-accounts.md)에 액세스할 수 있는지 확인합니다. Cloudyn이 VM에 대한 성능 데이터를 수집하여 표시하려면 자격 증명이 필요합니다. 비용 최적화 권장 사항을 만드는 데도 사용됩니다. Cloudyn이 다운사이징 권장 사항의 대상임을 판단하는 데는 최소 3일 분량의 인스턴스 성능 데이터가 필요합니다.

## <a name="enable-vm-metrics-with-a-script"></a>스크립트를 통해 VM 메트릭 사용

Azure PowerShell 스크립트를 통해 VM 메트릭을 사용하도록 설정할 수 있습니다. 메트릭을 사용하려는 대상 VM이 많을 경우 스크립트를 통해 프로세스를 자동화할 수 있습니다. 예제 스크립트는 GitHub의 [Azure에서 진단 사용](https://github.com/Cloudyn/azure-enable-diagnostics)에 있습니다.

## <a name="view-azure-performance-metrics"></a>Azure 성능 메트릭 보기

Cloudyn 포털에서 Azure 인스턴스의 성능 메트릭을 보려면 **자산** > **계산** > **인스턴스 탐색기**로 이동합니다. VM 인스턴스 목록에서 인스턴스를 확장한 다음, 리소스를 확장하여 세부 정보를 확인합니다.

![인스턴스 탐색기에 표시되는 예제 정보](./media/azure-vm-extended-metrics/instance-explorer.png)

## <a name="next-steps"></a>다음 단계

- 계정에 대해 Azure Resource Manager API 액세스를 아직 사용하도록 설정하지 않은 경우 [Azure 구독 및 계정 활성화](../../cost-management/activate-subs-accounts.md)를 진행합니다.
