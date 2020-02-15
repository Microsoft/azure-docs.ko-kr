---
title: Azure VM에 대한 업데이트 및 패치 관리
description: 이 문서에서는 Azure Automation 업데이트 관리를 사용하여 Azure 및 비 Azure VM에 대한 업데이트 및 패치를 관리하는 방법에 대한 개요를 제공합니다.
services: automation
ms.subservice: update-management
ms.topic: tutorial
ms.date: 01/21/2020
ms.custom: mvc
ms.openlocfilehash: 3922f8a2478f00c632b6daf294f23c7b5ad8c261
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76310138"
---
# <a name="manage-updates-and-patches-for-your-azure-vms"></a>Azure VM에 대한 업데이트 및 패치 관리

업데이트 관리 솔루션을 사용하여 가상 머신의 업데이트 및 패치를 관리할 수 있습니다. 이 자습서에서는 사용 가능한 업데이트의 상태를 빠르게 평가하고, 필수 업데이트의 설치를 예약하고, 배포 결과를 검토하고, 업데이트가 성공적으로 적용되었는지 확인하는 경고를 만드는 방법을 알아봅니다.

가격 책정 정보에 대해서는 [업데이트 관리를 위한 Automation 가격 책정](https://azure.microsoft.com/pricing/details/automation/)을 참조하세요.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 업데이트 관리를 위한 VM 온보딩
> * 업데이트 평가 보기
> * 경고 구성
> * 업데이트 배포 예약
> * 배포 결과 보기

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* Azure 구독 구독이 아직 없는 경우 [Visual Studio 구독자를 위한 월간 Azure 크레딧을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* 감시자, 작업 Runbook 및 Watcher 태스크를 보관할 [Azure Automation 계정](automation-offering-get-started.md)
* 등록할 [가상 머신](../virtual-machines/windows/quick-create-portal.md)

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="enable-update-management"></a>업데이트 관리 사용

먼저 이 자습서에 사용할 VM에서 업데이트 관리를 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **가상 머신**을 선택하거나 **홈** 페이지에서 **가상 머신**을 검색하여 선택합니다.
1. 업데이트 관리를 사용하도록 설정할 VM을 선택합니다.
1. VM 페이지의 **작업** 아래에서 **업데이트 관리**를 선택합니다. **업데이트 관리 사용** 창이 열립니다.

이 VM에 대해 업데이트 관리가 사용되도록 설정되어 있는지를 확인하기 위해 유효성 검사가 수행됩니다. 이 유효성 검사에는 Log Analytics 작업 영역 및 연결된 Automation 계정 검사와 업데이트 관리 솔루션이 작업 영역에서 활성화되어 있는지 여부가 포함됩니다.

[Log Analytics](../azure-monitor/platform/data-platform-logs.md) 작업 영역은 기능 및 서비스(예: 업데이트 관리)에서 생성된 데이터를 수집하는 데 사용됩니다. 이 작업 영역은 여러 원본의 데이터를 검토 및 분석하는 단일 위치를 제공합니다.

또한 유효성 검사 프로세스는 Log Analytics 에이전트 및 Automation 하이브리드 runbook worker로 VM이 프로비저닝되었는지 여부를 확인합니다. 이 에이전트는 Azure Automation과 통신하고 업데이트 상태에 대한 정보를 얻는 데 사용됩니다. 이 에이전트를 사용하려면 Azure Automation 서비스와 통신하고 업데이트를 다운로드하기 위한 443 포트가 필요합니다.

등록 중에 다음 필수 구성 요소 중 하나가 누락된 것으로 확인되면 자동으로 추가됩니다.

* [Log Analytics](../azure-monitor/platform/data-platform-logs.md) 작업 영역
* [Automation 계정](./automation-offering-get-started.md)
* [Hybrid Runbook Worker](./automation-hybrid-runbook-worker.md)(VM에서 사용)

**업데이트 관리** 아래에서 사용할 위치, Log Analytics 작업 영역 및 Automation 계정을 설정합니다. 그런 다음, **사용**을 선택합니다. 이러한 옵션을 사용할 수 없다면 다른 자동화 솔루션이 VM에 대해 활성화되어 있는 것입니다. 이 경우에 동일한 작업 영역 및 Automation 계정을 사용해야 합니다.

![업데이트 관리 솔루션 사용 창](./media/automation-tutorial-update-management/manageupdates-update-enable.png)

솔루션을 사용하도록 설정하는 데 몇 분이 걸릴 수 있습니다. 이 시간 동안에는 브라우저 창을 닫으면 안됩니다. 솔루션이 사용되도록 설정되면 VM의 누락된 업데이트에 대한 정보가 Azure Monitor 로그로 이동됩니다. 이 데이터를 분석에 사용할 수 있게 되는 데 30분에서 6시간까지 걸릴 수 있습니다.

## <a name="view-update-assessment"></a>업데이트 평가 보기

업데이트 관리를 사용하도록 설정하면 **업데이트 관리** 창이 열립니다. 업데이트가 누락된 것으로 식별된 경우 **누락 업데이트** 탭에 누락된 업데이트 목록이 표시됩니다.

**정보 링크** 아래에서 업데이트 링크를 선택하여 업데이트에 대한 지원 문서를 엽니다. 업데이트에 대한 중요한 정보를 알아볼 수 있습니다.

![업데이트 상태 보기](./media/automation-tutorial-update-management/manageupdates-view-status-win.png)

업데이트의 다른 위치를 클릭하면 선택한 업데이트에 대한 **로그 검색** 창이 열립니다. 로그 검색에 대한 쿼리는 해당 특정 업데이트에 대해 미리 정의되어 있습니다. 이 쿼리를 수정하거나 사용자 고유의 쿼리를 만들어 환경에서 배포되었거나 누락된 업데이트에 대한 자세한 정보를 볼 수 있습니다.

![업데이트 상태 보기](./media/automation-tutorial-update-management/logsearch.png)

## <a name="configure-alerts"></a>경고 구성

이 단계에서는 업데이트 배포 상태를 알려 주도록 경고를 설정하는 방법을 알아봅니다.

### <a name="alert-conditions"></a>경고 조건

Automation 계정의 **모니터링** 아래에서 **경고**로 이동한 후 **+ 새 경고 규칙**을 클릭합니다.

Automation 계정이 이미 리소스로 선택되었습니다. 변경하려면 **선택**을 클릭하고 **리소스 선택** 페이지의 **리소스 종류별 필터링** 드롭다운에서 **Automation 계정**을 선택합니다. Automation 계정을 선택한 다음, **완료**를 선택합니다.

**조건 추가**를 클릭하여 업데이트 배포에 적절한 신호를 선택합니다. 다음 표는 업데이트 배포에 사용 가능한 두 가지 신호의 세부 정보를 보여 줍니다.

|신호 이름|차원|Description|
|---|---|---|
|**총 업데이트 배포 실행**|- 업데이트 배포 이름</br>- 상태|이 신호는 업데이트 배포의 전체 상태를 알리는 데 사용됩니다.|
|**총 업데이트 배포 머신 실행**|- 업데이트 배포 이름</br>- 상태</br>- 대상 컴퓨터</br>- 업데이트 배포 실행 ID|이 신호는 특정 머신을 대상으로 하는 업데이트 배포의 상태를 알리는 데 사용됩니다.|

차원 값의 경우 목록에서 유효한 값을 선택합니다. 찾으려는 값이 목록에 없으면 해당 차원 옆의 **\+** 기호를 클릭하고 사용자 지정 이름을 입력합니다. 그런 다음, 찾으려는 값을 선택할 수 있습니다. 차원의 모든 값을 선택하려면 **선택\*** 단추를 클릭합니다. 차원 값을 선택하지 않으면 평가 중에 해당 차원이 무시됩니다.

![신호 논리 구성](./media/automation-tutorial-update-management/signal-logic.png)

**논리 경고** 아래에서 **임계값**으로 **1**을 입력합니다. 완료되면 **완료**를 선택합니다.

### <a name="alert-details"></a>경고 세부 정보

**2. 경고 세부 정보**를 정의하고 경고의 이름과 설명을 입력합니다. **심각도**는 성공적인 실행에 대해 **Informational(Sev 2)** 로 설정하거나, 실패한 실행에 대해 **Informational(Sev 1)** 로 설정합니다.

![신호 논리 구성](./media/automation-tutorial-update-management/define-alert-details.png)

**작업 그룹** 아래에서 **새로 만들기**를 선택합니다. 작업 그룹은 여러 경고에서 사용할 수 있는 작업의 그룹입니다. 이 작업에는 이메일 알림, Runbook, 웹후크 등이 포함되며 이에 국한되지 않습니다. 작업 그룹에 대해 자세히 알아보려면 [작업 그룹 만들기 및 관리](../azure-monitor/platform/action-groups.md)를 참조하세요.

**작업 그룹 이름** 상자에 경고의 이름 및 약식 이름을 입력합니다. 약식 이름은 이 그룹을 사용하여 알림을 보내는 경우 전체 작업 그룹 이름 대신 사용됩니다.

**작업**에서 **이메일 알림**과 같은 작업의 이름을 입력합니다. **작업 형식**에서 **이메일/SMS/푸시/음성**을 선택합니다. **세부 정보**에서 **세부 정보 편집**을 선택합니다.

**이메일/SMS/푸시/음성** 창에서 이름을 입력합니다. **이메일** 확인란을 선택한 다음, 유효한 이메일 주소를 입력합니다.

![이메일 작업 그룹 구성](./media/automation-tutorial-update-management/configure-email-action-group.png)

**이메일/SMS/푸시/음성** 창에서 **확인**을 선택합니다. **작업 그룹 추가** 창에서 **확인**을 선택합니다.

경고 이메일의 제목을 사용자 지정하려면 **규칙 만들기**의 **작업 사용자 지정** 아래에서 **메일 제목**을 선택합니다. 작업이 완료되면 **경고 규칙 만들기**를 선택합니다. 경고에서는 언제 업데이트 배포가 성공하고 해당 부분의 업데이트 배포 실행에 어떤 컴퓨터가 사용되었는지 알려줍니다.

## <a name="schedule-an-update-deployment"></a>업데이트 배포 예약

따라서 릴리스 일정 및 서비스 기간 이후로 배포를 예약하여 업데이트를 설치합니다. 배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

>[!NOTE]
>업데이트 배포를 예약하면 대상 머신에서 업데이트 배포를 처리하는 **MicrosoftOMSComputers** runbook에 연결된 [일정](shared-resources/schedules.md) 리소스가 만들어집니다. 배포를 만든 후 Azure Portal에서 또는 PowerShell을 사용하여 일정 리소스를 삭제하면 예약된 업데이트 배포가 중단되고 포털에서 다시 구성하려고 할 때 오류가 표시됩니다. 해당 배포 일정을 삭제해야만 일정 리소스를 삭제할 수 있습니다.  
>

VM에 대한 새 업데이트 배포를 예약하려면 **업데이트 관리**로 이동한 다음, **업데이트 배포 예약**을 선택합니다.

**새 업데이트 배포**에서 다음 정보를 지정합니다.

* **Name**: 업데이트 배포에 대한 고유한 이름을 입력합니다.

* **운영 체제**: 업데이트 배포의 대상 OS를 선택합니다.

* **업데이트할 그룹(미리 보기)** : 구독, 리소스 그룹, 위치 및 태그의 조합을 기반으로 쿼리를 정의하여 배포에 포함할 Azure VM의 동적 그룹을 빌드합니다. 자세한 내용은 [동적 그룹](automation-update-management-groups.md)을 참조하세요.

* **업데이트할 머신**: 저장된 검색, 가져온 그룹을 선택하거나 드롭다운에서 머신을 선택하고 개별 머신을 선택합니다. **머신**을 선택한 경우 머신의 준비는 **업데이트 에이전트 준비** 열에 표시됩니다. Azure Monitor 로그에서 컴퓨터 그룹을 만드는 다른 방법에 대해 알아보려면 [Azure Monitor 로그의 컴퓨터 그룹](../azure-monitor/platform/computer-groups.md)을 참조하세요.

* **업데이트 분류**: 업데이트 배포에 포함될 수 있는 각 제품에 대해 지원되는 업데이트 분류를 선택합니다. 이 자습서에서는 모든 종류가 선택된 상태로 둡니다.

  분류 형식은 다음과 같습니다.

   |OS  |Type  |
   |---------|---------|
   |Windows     | 중요 업데이트</br>보안 업데이트</br>업데이트 롤업</br>기능 팩</br>서비스 팩</br>정의 업데이트</br>도구</br>업데이트        |
   |Linux     | 중요 업데이트 및 보안 업데이트</br>다른 업데이트       |

   분류 형식에 대한 설명은 [업데이트 분류](automation-view-update-assessments.md#update-classifications)를 참조하세요.

* **포함/제외할 업데이트** - **포함/제외** 페이지가 열립니다. 포함 또는 제외할 업데이트는 별도의 탭에 있습니다.

> [!NOTE]
> 제외는 포함을 재정의한다는 사실을 기억해야 합니다. 예를 들어 `*`의 제외 규칙을 정의하는 경우 모든 패치 또는 패키지가 제외되므로 설치되지 않습니다. 제외된 패치가 여전히 머신에서 누락된 것으로 표시됩니다. Linux 머신의 경우 패키지가 포함되었지만 종속 패키지가 제외되면 패키지가 설치되지 않습니다.

> [!NOTE]
> 업데이트 배포에 포함하기 위해 대체된 업데이트를 지정할 수 없습니다.
>

* **일정 설정**: **일정 설정** 창이 열립니다. 기본 시작 시간은 현재 시간으로부터 30분 후입니다. 앞으로 10분 이후부터 언제든지 시작 시간을 설정할 수 있습니다.

   배포가 한 번만 수행될지 여부를 지정하거나 되풀이 일정을 설정할 수도 있습니다. **되풀이**에서 **한 번**을 선택합니다. 기본값을 1일로 그대로 두고 **확인**을 선택합니다. 이렇게 하면 되풀이 일정이 설정됩니다.

* **사전 스크립트 + 사후 스크립트**: 배포 전후에 실행할 스크립트를 선택합니다. 자세한 내용은 [사전 및 사후 스크립트 관리](pre-post-scripts.md)를 참조하세요.

* **유지 관리 기간(분)** : 기본값을 그대로 둡니다. 유지 관리 기간은 업데이트 설치에 허용되는 시간을 제어합니다. 유지 관리 기간을 지정할 때는 다음 사항을 고려하세요.

  * 유지 관리 기간은 설치하려는 업데이트의 수를 제어합니다.
  * 유지 관리 기간이 종료될 경우에도 업데이트 관리는 새 업데이트 설치를 중지하지 않습니다.
  * 유지 관리 기간을 초과하는 경우 업데이트 관리는 진행 중인 업데이트를 종료하지 않습니다.
  * Windows에서 유지 관리 기간을 초과하는 경우 서비스 팩 업데이트를 설치하는 데 오랜 시간이 소요되는 경우가 많습니다.

  > [!NOTE]
  > Ubuntu에서 유지 관리 기간 외에 업데이트가 적용되지 않도록 하려면 자동 업데이트를 사용하지 않도록 Unattended-Upgrade 패키지를 다시 구성합니다. 패키지 구성 방법에 대한 자세한 내용은 [Ubuntu Server 가이드의 자동 업데이트 항목](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)을 참조하세요.

* **다시 부팅 옵션**: 이 설정은 다시 부팅을 처리하는 방법을 결정합니다. 사용 가능한 옵션은 다음과 같습니다.
  * 필요한 경우 다시 부팅(기본값)
  * 항상 다시 부팅
  * 다시 부팅 안 함
  * 다시 부팅만 - 업데이트 설치 안 함

> [!NOTE]
> [다시 시작을 관리하는 데 사용되는 레지스트리 키](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)에 나열된 레지스트리 키는 **재부팅 제어**가 **재부팅하지 않음**으로 설정되면 재부팅 이벤트가 발생할 수 있습니다.

일정 구성을 마쳤으면 **만들기**를 선택합니다.

![업데이트 일정 설정 창](./media/automation-tutorial-update-management/manageupdates-schedule-win.png)

상태 대시보드로 돌아갑니다. **예약된 업데이트 배포**를 선택하여 만든 배포 일정을 표시합니다.

> [!NOTE]
> 업데이트 관리는 자사 업데이트 배포와 미리 다운로드 패치를 지원합니다. 이를 위해 패치 대상 시스템을 변경해야 합니다. 시스템에서 이러한 설정을 구성하는 방법은 [자사 및 미리 다운로드 지원](automation-configure-windows-update.md)을 참조하세요.

**업데이트 배포**를 프로그래밍 방식으로 만들 수도 있습니다. REST API를 사용하여 **업데이트 배포**를 만드는 방법은 [소프트웨어 업데이트 구성 - 만들기](/rest/api/automation/softwareupdateconfigurations/create)를 참조하세요. 주간 **업데이트 배포**를 만드는 데 사용할 수 있는 샘플 Runbook도 있습니다. 이 Runbook에 대한 자세한 내용은 [리소스 그룹에 있는 하나 이상의 VM에 대한 주간 업데이트 배포 만들기](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)를 참조하세요.

## <a name="view-results-of-an-update-deployment"></a>업데이트 배포의 결과 보기

예약된 배포가 시작되면 **업데이트 관리**의 **업데이트 배포** 탭에서 해당 배포에 대한 상태를 볼 수 있습니다. 배포가 현재 실행 중이면 상태가 **진행 중**입니다. 배포를 완료하면 성공한 경우 **성공**으로 상태가 변경됩니다. 배포에서 하나 이상의 업데이트에 오류가 발생하는 경우 상태는 **부분적으로 실패**입니다.

완료된 배포 업데이트를 선택하여 해당 업데이트 배포에 대한 대시보드를 확인합니다.

![특정 배포에 대한 업데이트 배포 상태 대시보드](./media/automation-tutorial-update-management/manageupdates-view-results.png)

**업데이트 결과**에서 요약은 VM에 대한 총 업데이트 수 및 배포 결과를 제공합니다. 오른쪽 테이블에는 각 업데이트에 대한 자세한 분석 및 설치 결과가 표시됩니다.

다음 목록에서는 사용 가능한 값을 보여 줍니다.

* **시도 안 함**: 정의된 유지 관리 기간에 따라 사용할 수 있는 시간이 충분하지 않기 때문에 업데이트가 설치되지 않았습니다.
* **성공**: 업데이트했습니다.
* **실패**: 업데이트하지 못했습니다.

배포를 통해 생성된 모든 로그 항목을 보려면 **모든 로그**를 선택합니다.

**출력** 타일을 선택하여 대상 VM의 업데이트 배포를 관리하는 Runbook의 작업 스트림을 확인합니다.

배포의 모든 오류에 대한 자세한 정보를 보려면 **오류**를 선택합니다.

업데이트 배포가 성공하면 배포 성공을 보여주기 위해 다음 예제와 비슷한 이메일이 전송됩니다.

![이메일 작업 그룹 구성](./media/automation-tutorial-update-management/email-notification.png)

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 업데이트 관리를 위한 VM 온보딩
> * 업데이트 평가 보기
> * 경고 구성
> * 업데이트 배포 예약
> * 배포 결과 보기

업데이트 관리 솔루션에 대한 개요로 계속 진행하세요.

> [!div class="nextstepaction"]
> [업데이트 관리 솔루션](../operations-management-suite/oms-solution-update-management.md?toc=%2fazure%2fautomation%2ftoc.json)

