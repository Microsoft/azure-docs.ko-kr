---
title: Azure Automation 업데이트 관리에 대 한 업데이트 배포를 만드는 방법
description: 이 문서에서는 업데이트 배포를 예약 하 고 해당 상태를 검토 하는 방법을 설명 합니다.
services: automation
ms.subservice: update-management
ms.date: 08/20/2020
ms.topic: conceptual
ms.openlocfilehash: 4336ba272dd83ad2a35060c1c7524a564b928484
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717696"
---
# <a name="how-to-deploy-updates-and-review-results"></a>업데이트를 배포 하 고 결과를 검토 하는 방법

이 문서에서는 업데이트 배포를 예약 하 고 배포가 완료 된 후 프로세스를 검토 하는 방법을 설명 합니다.

## <a name="sign-in-to-the-azure-portal"></a>Azure Portal에 로그인

[Azure 포털](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>업데이트 배포 예약

업데이트 배포를 예약하면 대상 머신에서 업데이트 배포를 처리하는 **MicrosoftOMSComputers** Runbook에 연결된 [일정](../shared-resources/schedules.md) 리소스가 만들어집니다. 릴리스 일정 및 서비스 기간 이후로 배포를 예약하여 업데이트를 설치해야 합니다. 배포에 포함할 업데이트 형식을 선택할 수 있습니다. 예를 들어 중요 업데이트나 보안 업데이트를 포함하고 업데이트 롤업은 제외할 수 있습니다.

>[!NOTE]
>배포를 만든 후 Azure Portal에서 또는 PowerShell을 사용하여 일정 리소스를 삭제하면 예약된 업데이트 배포가 중단되고 포털에서 예약 리소스를 다시 구성하려고 할 때 오류가 표시됩니다. 해당 배포 일정을 삭제해야만 일정 리소스를 삭제할 수 있습니다.  

새로운 업데이트 배포를 예약하려면 다음을 수행합니다.

1. Automation 계정에서 **업데이트 관리**로 이동한 다음, **업데이트 관리**에서 **업데이트 배포 예약**을 선택합니다.

2. **새 업데이트 배포**아래의 **이름** 필드에 배포에 대 한 고유한 이름을 입력 합니다.

3. 업데이트 배포의 대상 운영 체제를 선택합니다.

4. **업데이트할 그룹(미리 보기)** 영역에서 구독, 리소스 그룹, 위치 및 태그가 결합된 쿼리를 정의하여 배포에 포함할 Azure VM의 동적 그룹을 빌드합니다. 자세히 알아보려면 [업데이트 관리에서 동적 그룹 사용](update-mgmt-groups.md)을 참조하세요.

5. **업데이트할 머신** 영역에서 저장된 검색, 가져온 그룹을 선택하거나 드롭다운 메뉴에서 **머신**을 선택하고 개별 머신을 선택합니다. 이 옵션을 사용하여 각 머신의 Log Analytics 에이전트 준비 상태를 확인할 수 있습니다. Azure Monitor 로그에서 컴퓨터 그룹을 만드는 다른 방법에 대해 알아보려면 [Azure Monitor 로그의 컴퓨터 그룹](../../azure-monitor/platform/computer-groups.md)을 참조하세요.

6. **업데이트 분류** 영역을 사용하여 제품의 [업데이트 분류](update-mgmt-view-update-assessments.md#work-with-update-classifications)를 지정합니다. 업데이트 배포에 포함할 업데이트를 제외하고 각 제품에 대해 지원되는 모든 업데이트 분류를 선택 취소합니다.

    배포에서 선택 된 업데이트 집합만 적용 하려면 다음 단계에 설명 된 대로 **업데이트 포함/제외** 옵션을 구성할 때 미리 선택 된 모든 업데이트 분류를 선택 취소 해야 합니다. 이렇게 하면이 배포에 *포함* 하도록 지정한 업데이트만 대상 컴퓨터에 설치 됩니다.

7. **업데이트 포함/제외** 영역을 사용 하 여 배포에서 선택한 업데이트를 추가 하거나 제외 합니다. **포함/제외** 페이지에서 포함 하거나 제외할 KB 문서 ID 번호를 입력 합니다.

   > [!IMPORTANT]
   > 제외 항목은 포함 항목을 재정의한다는 사실을 기억하세요. 예를 들어 `*`의 제외 규칙을 정의하면 업데이트 관리가 모든 패치 또는 패키지를 설치 대상에서 제외합니다. 제외된 패치는 여전히 머신에서 누락된 것으로 표시됩니다. Linux 머신의 경우 제외된 종속 패키지가 들어 있는 패키지를 포함할 경우 업데이트 관리에서 주 패키지를 설치하지 않습니다.

   > [!NOTE]
   > 업데이트 배포에 포함하기 위해 대체된 업데이트를 지정할 수 없습니다.

8. **일정 설정**을 선택합니다. 기본 시작 시간은 현재 시간으로부터 30분 후입니다. 앞으로 10분 이후부터 언제든지 시작 시간을 설정할 수 있습니다.

9. **되풀이** 필드를 사용 하 여 배포가 한 번 발생 하는지 또는 되풀이 일정을 사용 하는지 지정 하 고 **확인**을 선택 합니다.

10. **사전 스크립트 + 사후 스크립트(미리 보기)** 영역에서 배포 전후에 실행할 스크립트를 선택합니다. 자세히 알아보려면 [사전 스크립트 및 사후 스크립트 관리](update-mgmt-pre-post-scripts.md)를 참조하세요.
    
11. **유지 관리 기간(분)** 필드를 사용하여 업데이트 설치에 허용되는 시간을 지정합니다. 유지 관리 기간을 지정할 때는 다음 사항을 고려하세요.

    * 유지 관리 기간은 설치하려는 업데이트의 수를 제어합니다.
    * 유지 관리 기간 종료일이 임박해도 업데이트 관리는 새 업데이트 설치를 중지하지 않습니다.
    * 유지 관리 기간이 초과되어도 업데이트 관리는 진행 중인 업데이트를 종료하지 않습니다.
    * 서비스 팩 업데이트를 설치하는 데 오랜 시간이 소요되어 Windows에서 유지 관리 기간이 초과되는 경우가 많습니다.

    > [!NOTE]
    > Ubuntu에서 유지 관리 기간 외에 업데이트가 적용되지 않도록 하려면 자동 업데이트를 사용하지 않도록 `Unattended-Upgrade` 패키지를 다시 구성합니다. 패키지 구성 방법에 대한 자세한 내용은 [Ubuntu Server 가이드의 자동 업데이트 항목](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)을 참조하세요.

12. **다시 부팅 옵션** 필드를 사용하여 배포 중에 다시 부팅을 처리하는 방법을 지정할 수 있습니다. 다음 옵션을 사용할 수 있습니다. 
    * 필요한 경우 다시 부팅(기본값)
    * 항상 다시 부팅
    * 다시 부팅 안 함
    * 다시 부팅만 - 업데이트 설치 안 함

    > [!NOTE]
    > [다시 시작을 관리하는 데 사용되는 레지스트리 키](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)에 나열된 레지스트리 키는 **재부팅 옵션**이 **재부팅하지 않음**으로 설정되면 재부팅 이벤트가 발생할 수 있습니다.

13. 배포 일정을 구성 했으면 **만들기**를 선택 합니다.

    ![업데이트 일정 설정 창](./media/update-mgmt-deploy-updates/manageupdates-schedule-win.png)

14. 상태 대시보드로 돌아갑니다. **예약된 업데이트 배포**를 선택하여 만든 배포 일정을 표시합니다.

## <a name="schedule-an-update-deployment-programmatically"></a>프로그래밍 방식으로 업데이트 배포 예약

REST API를 사용하여 업데이트 배포를 만드는 방법은 [소프트웨어 업데이트 구성 - 만들기](/rest/api/automation/softwareupdateconfigurations/create)를 참조하세요.

샘플 Runbook을 사용하여 주간 업데이트 배포를 만들 수 있습니다. 이 Runbook에 대한 자세한 내용은 [리소스 그룹에 있는 하나 이상의 VM에 대한 주간 업데이트 배포 만들기](https://gallery.technet.microsoft.com/scriptcenter/Create-a-weekly-update-2ad359a1)를 참조하세요.

## <a name="check-deployment-status"></a>배포 상태 확인

예약된 배포가 시작되면 **업데이트 관리**의 **업데이트 배포** 탭에서 상태를 볼 수 있습니다. 배포가 현재 실행 중이면 상태가 **진행 중**입니다. 배포가 성공적으로 완료되면 상태가 **성공**으로 변경됩니다. 배포에서 하나 이상의 업데이트에 오류가 발생하는 경우 상태는 **부분적으로 실패**입니다.

## <a name="view-results-of-a-completed-update-deployment"></a>완료된 업데이트 배포의 결과 보기

배포가 완료 되 면 해당 배포를 선택 하 여 결과를 볼 수 있습니다.

[![특정 배포에 대 한 배포 상태 대시보드 업데이트](./media/update-mgmt-deploy-updates/manageupdates-view-results.png)](./media/update-mgmt-deploy-updates/manageupdates-view-results-expanded.png#lightbox)

**업데이트 결과**에서 요약은 대상 VM의 총 업데이트 수 및 배포 결과를 제공합니다. 오른쪽 표에는 각 업데이트에 대한 자세한 분석 및 설치 결과가 표시됩니다.

사용 가능한 값은

* **시도 안 함**: 정의된 유지 관리 기간에 따라 사용할 수 있는 시간이 충분하지 않기 때문에 업데이트가 설치되지 않았습니다.
* **선택 안 함** - 배포할 업데이트를 선택하지 않았습니다.
* **성공** - 업데이트했습니다.
* **실패** - 업데이트하지 못했습니다.

배포를 통해 생성된 모든 로그 항목을 보려면 **모든 로그**를 선택합니다.

**출력** 타일을 선택하여 대상 VM의 업데이트 배포를 관리하는 Runbook의 작업 스트림을 확인합니다.

배포의 모든 오류에 대한 자세한 정보를 보려면 **오류**를 선택합니다.

## <a name="next-steps"></a>다음 단계

업데이트 배포 결과에 대해 알리도록 경고를 만드는 방법을 알아보려면 [업데이트 관리에 대 한 경고 만들기](update-mgmt-configure-alerts.md)를 참조 하세요.