---
title: Azure Automation에서 여러 VM에 대한 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션을 등록하는 방법
description: Azure Automation에 포함된 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션을 사용하여 Azure Virtual Machine을 등록하는 방법을 알아봅니다.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/11/2019
ms.topic: article
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: df59342bebae3ac0f6e80e5b58f429fedf3c3336
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60739044"
---
# <a name="enable-update-management-change-tracking-and-inventory-solutions-on-multiple-vms"></a>여러 VM에서 업데이트 관리, 변경 내용 추적 및 인벤토리 솔루션 사용

Azure Automation은 운영 체제 보안 업데이트를 관리하고, 변경 내용을 추적하며, 컴퓨터에 설치된 항목을 재고 자산으로 처리(인벤토리)하기 위한 솔루션을 제공합니다. 컴퓨터를 등록하는 여러 가지 방법이 있습니다. [가상 머신](automation-onboard-solutions-from-vm.md)에서 솔루션을 등록할 수도 있고, 가상 머신을 검색할 때 [Automation 계정](automation-onboard-solutions-from-automation-account.md)에서 등록할 수도 있고, [Runbook](automation-onboard-solutions.md)에서 등록할 수도 있습니다. 이 문서에서는 Azure에서 가상 머신을 검색할 때 솔루션을 등록하는 방법을 다룹니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

https://portal.azure.com에서 Azure에 로그인

## <a name="enable-solutions"></a>솔루션 사용

Azure Portal에서 **가상 머신**으로 이동합니다.

확인란을 사용하여 변경 내용 추적 및 인벤토리 또는 업데이트 관리를 등록할 가상 머신을 선택합니다. 한 번에 최대 3개의 리소스 그룹을 등록할 수 있습니다. Azure Vm의 Automation 계정 위치에 관계 없이 모든 지역에 있을 수 있습니다.

![VM 목록](media/automation-onboard-solutions-from-browse/vmlist.png)
> [!TIP]
> 필터 컨트롤을 사용하여 가상 머신 목록을 수정한 후 맨 위에 있는 확인란을 클릭하여 목록의 가상 머신을 모두 선택합니다.

명령 모음에서 **서비스**를 클릭하고 **변경 내용 추적**, **인벤토리** 또는 **업데이트 관리**를 선택합니다.

> [!NOTE]
> **변경 내용 추적** 및 **인벤토리**는 동일한 솔루션을 사용하며, 하나가 활성화되면 다른 솔루션도 활성화됩니다.

다음 이미지는 업데이트 관리에 대한 것입니다. 변경 내용 추적 및 인벤토리의 레이아웃과 동작도 동일합니다.

가상 머신 목록은 동일한 구독 및 위치에 있는 가상 머신만 표시하도록 필터링됩니다. 가상 머신이 3개 초과 리소스 그룹에 있는 경우 처음 3개 리소스 그룹이 선택됩니다.

### <a name="resource-group-limit"></a> 온보딩 제한 사항

온보딩에 사용할 수 있는 리소스 그룹 수는 [Resource Manager 배포 제한](../azure-resource-manager/resource-manager-cross-resource-group-deployment.md)으로 제한됩니다. Resource Manager 배포(업데이트 배포와 혼동하지 말 것)는 배포당 5개의 리소스 그룹으로 제한됩니다. 온보딩의 무결성을 보장하기 위해 이러한 리소스 그룹 중 2개는 Log Analytics 작업 영역, Automation 계정 및 관련 리소스를 구성하기 위해 예약되어 있습니다. 그러면 배포를 위해 선택할 리소스 그룹이 3개 남습니다.

필터 컨트롤을 사용하여 다른 구독, 위치 및 리소스 그룹의 가상 머신을 선택할 수 있습니다.

![업데이트 관리 솔루션 등록](media/automation-onboard-solutions-from-browse/onboardsolutions.png)

Log Analytics 작업 영역 및 Automation 계정에 대 한 선택 항목을 검토 합니다. 기존 작업 영역 및 Automation 계정은 기본적으로 선택됩니다. 서로 다른 Log Analytics 작업 영역 및 Automation 계정을 사용하려면 **사용자 지정 구성** 페이지에서 **사용자 지정**을 클릭하여 선택하면 됩니다. Log Analytics 작업 영역을 선택하는 경우 Automation 계정에 연결되는지 확인이 이루어집니다. 연결된 Automation 계정에 있으면 다음과 같은 화면이 표시됩니다. 완료하면 **확인**을 클릭합니다.

![작업 영역 및 계정 선택](media/automation-onboard-solutions-from-browse/selectworkspaceandaccount.png)

선택한 작업 영역이 Automation 계정에 연결되지 않은 경우 다음과 같은 화면이 표시됩니다. Automation 계정을 선택하고 완료되면 **확인**을 클릭합니다.

![작업 영역 없음](media/automation-onboard-solutions-from-browse/no-workspace.png)

솔루션을 사용하도록 설정할 때 특정 Azure 지역에서만 Log Analytics 작업 영역 및 Automation 계정을 연결할 수 있습니다.

다음 표에 지원되는 매핑이 나와 있습니다.

|**Log Analytics 작업 영역 지역**|**Azure Automation 지역**|
|---|---|
|AustraliaSoutheast|AustraliaSoutheast|
|CanadaCentral|CanadaCentral|
|CentralIndia|CentralIndia|
|EastUS<sup>1</sup>|EastUS2|
|JapanEast|JapanEast|
|SoutheastAsia|SoutheastAsia|
|WestCentralUS<sup>2</sup>|WestCentralUS<sup>2</sup>|
|WestEurope|WestEurope|
|UKSouth|UKSouth|
|USGovVirginia|USGovVirginia|
|EastUS2EUAP<sup>1</sup>|CentralUSEUAP|

<sup>1</sup> EastUS2EUAP 및 EastUS 매핑을 Automation 계정과 Log Analytics 작업 영역에 대 한 정확한 지역 매핑이 되지 않지만 올바른 매핑이 있습니다.

<sup>2</sup> 용량 제한으로 인해 지역을 사용할 수 없는 새 리소스를 만들 때. Automation 계정 및 Log Analytics 작업 영역을 포함 합니다. 그러나 지역에서 기존 연결 된 리소스는 계속 작동 합니다.

사용하지 않을 가상 머신 옆에 있는 확인란을 선택 취소합니다. 사용할 수 없는 가상 머신은 이미 선택이 취소되어 있습니다.

**사용**을 클릭하여 솔루션을 사용하도록 설정합니다. 솔루션을 사용하도록 설정하는 데 최대 15분이 걸립니다.

## <a name="unlink-workspace"></a>작업 영역 연결 해제

다음 솔루션은 Log Analytics 작업 영역에 따라 다릅니다.

* [업데이트 관리](automation-update-management.md)
* [변경 내용 추적](automation-change-tracking.md)
* [작업이 없는 동안 VM 시작/중지](automation-solution-vm-management.md)

더 이상 Log Analytics 작업 영역과 Automation 계정을 통합 하려는 경우 Azure portal에서 직접 계정 연결을 해제할 수 없습니다. 계속하기 전에 앞에서 언급한 솔루션을 제거해야 합니다. 그러지 않으면 이 프로세스가 계속 진행되지 않습니다. 가져온 특정 솔루션에 대한 문서를 검토하여 제거에 필요한 단계를 이해하세요.

이러한 솔루션을 제거한 후에 다음 단계에 따라 Automation 계정 연결을 해제할 수 있습니다.

> [!NOTE]
> 이전 버전의 Azure SQL 모니터링 솔루션을 포함한 일부 솔루션에서 자동화 자산을 만들었을 수 있으며, 작업 영역을 연결 해제하기 전에 제거해야 할 수도 있습니다.

1. Azure Portal에서 Automation 계정을 열고 Automation 계정 페이지에서 왼쪽의 **관련된 리소스** 섹션 아래의 **연결된 작업 영역**을 선택합니다.

2. 작업 영역 연결 해제 페이지에서 **작업 영역 연결 해제**를 클릭합니다.

   ![작업 영역 연결 해제 페이지](media/automation-onboard-solutions-from-browse/automation-unlink-workspace-blade.png).

   계속할지 묻는 메시지가 나타납니다.

3. Azure Automation이 Log Analytics 작업 영역의 연결을 끊으려고 하는 동안 메뉴의 **알림**에서 진행 상황을 추적할 수 있습니다.

업데이트 관리 솔루션을 사용한 경우 솔루션을 제거한 후 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

* 업데이트 일정 - 각 일정에는 사용자가 만든 업데이트 배포와 일치하는 이름이 지정됩니다.

* 솔루션용으로 만든 Hybrid Worker 그룹 - 각 그룹에는 machine1.contoso.com_9ceb8108-26c9-4051-b6b3-227600d715c8과 비슷하게 이름이 지정됩니다.

작업이 없는 동안 VM 시작/중지를 사용한 경우 솔루션을 제거한 후 더 이상 필요하지 않은 다음 항목을 제거할 수도 있습니다.

* VM runbook 시작 및 중지 일정
* VM runbook 시작 및 중지
* 변수

## <a name="troubleshooting"></a>문제 해결

여러 컴퓨터를 등록할 때 일부 컴퓨터가 **사용할 수 없음**으로 표시될 수 있습니다. 일부 컴퓨터를 사용할 수 없는 여러 가지 이유가 있습니다. 다음 섹션에서는 컴퓨터를 등록하려고 시도하면 VM 상태가 **사용할 수 없음**으로 표시되는 이유를 설명합니다.

### <a name="vm-reports-to-a-different-workspace-workspacename--change-configuration-to-use-it-for-enabling"></a>VM이 다른 작업 영역('\<workspaceName\>')에 보고합니다.  활성화에 사용하도록 구성 변경

**원인**: 이 오류는 등록하려는 VM이 다른 작업 영역에 보고함을 나타냅니다.

**솔루션**: **구성으로 사용**을 클릭하여 대상 Automation 계정 및 Log Analytics 작업 영역을 변경합니다.

### <a name="vm-reports-to-a-workspace-that-is-not-available-in-this-subscription"></a>VM이 이 구독에서 사용할 수 있는 작업 영역에 보고

**원인**: 가상 머신이 보고하는 작업 영역의 상태가 다음과 같습니다.

* 다른 구독에 있습니다.
* 더 이상 존재하지 않습니다.
* 액세스 권한이 없는 리소스 그룹에 있습니다.

**솔루션**: VM이 보고하는 작업 영역과 연결된 Automation 계정을 찾아서 범위 구성을 변경하여 가상 머신을 등록합니다.

### <a name="vm-operating-system-version-or-distribution-is-not-supported"></a>VM 운영 체제 버전 또는 배포가 지원되지 않음

**원인:** 솔루션이 일부 Linux 배포판 또는 일부 Windows에 지원되지 않습니다.

**해결 방법:** 해결 방법은 [지원되는 클라이언트 목록](automation-update-management.md#clients)을 참조하세요.

### <a name="classic-vms-cannot-be-enabled"></a>클래식 VM을 사용할 수 없음

**원인**: 클래식 배포 모델을 사용하는 가상 머신이 지원되지 않습니다.

**솔루션**: 가상 머신을 리소스 관리자 배포 모델로 마이그레이션합니다. 작업 방법은 [클래식 배포 모델 리소스 마이그레이션](../virtual-machines/windows/migration-classic-resource-manager-overview.md)을 참조하세요.

### <a name="vm-is-stopped-deallocated"></a>VM이 중지되었습니다. (할당 취소됨)

**원인**: 가상 머신의 상태가 **실행 중**이 아닙니다.

**솔루션**: VM을 솔루션에 등록하려면 VM이 실행 중이어야 합니다. 페이지에서 나가지 말고 **VM 시작** 인라인 링크를 클릭하여 VM을 시작합니다.

## <a name="next-steps"></a>다음 단계

가상 머신에 솔루션을 사용하도록 설정했으니, 업데이트 관리 개요 문서를 방문하여 컴퓨터의 업데이트 평가를 살펴보는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [업데이트 관리 - 업데이트 평가 보기](./automation-update-management.md#viewing-update-assessments)

솔루션 및 솔루션 사용 방법에 대한 추가 자습서:

* [자습서 - VM 업데이트 관리](automation-tutorial-update-management.md)

* [자습서 - VM에 설치된 소프트웨어 식별](automation-tutorial-installed-software.md)

* [자습서 - VM에 대한 변경 내용 문제 해결](automation-tutorial-troubleshoot-changes.md)
