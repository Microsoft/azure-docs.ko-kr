---
title: Azure Recovery Services 자격 증명 모음 및 서버 관리
description: 이 문서에서는 Recovery Services 자격 증명 모음 개요 대시보드를 사용 하 여 Recovery Services 자격 증명 모음을 모니터링 하 고 관리 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 74351d781287d863db8be0fc7d20517e0479106c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89002133"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Recovery Services 자격 증명 모음 모니터링 및 관리

이 문서에서는 Recovery Services 자격 증명 모음 **개요** 대시보드를 사용하여 Recovery Services 자격 증명 모음을 모니터링하고 관리하는 방법에 대해 설명합니다. 목록에서 Recovery Services 자격 증명 모음을 열면 선택한 자격 증명 모음에 대한 **개요** 대시보드가 열립니다. 대시보드는 자격 증명 모음에 대한 다양한 세부 정보를 제공합니다. 위험 및 경고 알림 상태, 진행 중인 백업 및 실패 한 백업 작업, 사용 된 LRS (로컬 중복 저장소) 및 GRS (지역 중복 저장소)의 양에 표시 되는 *타일이* 있습니다. Azure VM을 자격 증명 모음에 백업하면 [**백업 사전 검사 상태** 타일에 위험 또는 경고 항목](#backup-pre-check-status)이 표시됩니다. 다음 이미지는 **Contoso-vault**에 대한 **개요** 대시보드입니다. **백업 항목** 타일에는 자격 증명 모음에 등록된 9개의 항목이 표시되어 있습니다.

![Recovery Services 자격 증명 모음 대시보드](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

이 문서의 필수 구성 요소는 Azure 구독, Recovery Services 자격 증명 모음 및 자격 증명 모음에 대해 구성 된 백업 항목이 하나 이상 있다는 것입니다.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Recovery Services Vault 열기

알림을 모니터링하거나 Recovery Services 자격 증명 모음에 대한 관리 데이터를 보려면 해당 자격 증명 모음을 엽니다.

1. Azure 구독을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. 포털에서 **모든 서비스**를 선택합니다.

   ![Recovery Services Vault 목록 열기 1단계](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. **모든 서비스** 대화 상자에서 **Recovery Services**를 입력합니다. 입력을 시작하면 입력한 내용을 바탕으로 목록이 필터링됩니다. **Recovery Services 자격 증명 모음** 옵션이 표시 되 면 선택 하 여 구독의 Recovery Services 자격 증명 모음 목록을 엽니다.

    ![Recovery Services 자격 증명 모음 만들기 1 단계](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. 자격 증명 모음 목록에서 자격 증명 모음을 선택 하 여 해당 **개요** 대시보드를 엽니다.

    ![Recovery Services 자격 증명 모음 대시보드](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    [개요] 대시보드는 타일을 사용하여 알림 및 백업 작업 데이터를 제공합니다.

## <a name="monitor-backup-jobs-and-alerts"></a>백업 작업 및 경고 모니터링

Recovery Services 자격 증명 모음 **개요** 대시보드는 모니터링 및 사용량 정보에 대한 타일을 제공합니다. [모니터링] 섹션의 타일에는 [중요/경고] 알림, [진행 중/실패] 작업이 표시됩니다. 특정 경고나 작업을 선택 하 여 해당 작업 또는 경고에 대해 필터링 된 백업 경고 또는 백업 작업 메뉴를 엽니다.

![Backup 대시보드 작업](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

[모니터링] 섹션에는 미리 정의된 **백업 경고** 및 **백업 작업** 쿼리의 결과가 표시됩니다. [모니터링] 타일에서 제공하는 최신 정보는 다음과 같습니다.

* 백업 작업에 대한 중요/경고 알림(지난 24시간 동안)
* Azure Vm에 대 한 사전 검사 상태. 사전 검사 상태에 대 한 자세한 내용은 [백업 사전 검사 상태](#backup-pre-check-status)를 참조 하세요.
* 진행 중 백업 작업 및 실패한 작업(지난 24시간 동안).

[사용량] 타일에서 제공하는 정보는 다음과 같습니다.

* 자격 증명 모음에 구성된 백업 항목 수
* 자격 증명 모음에서 사용하는 Azure Storage(LRS 및 GRS로 구분됨).

연결 된 메뉴를 열려면 타일 (백업 저장소 제외)을 선택 합니다. 위의 이미지에서 [백업 경고] 타일에는 세 개의 중요한 알림이 표시되어 있습니다. Backup 경고 타일에서 위험 경고 행을 선택 하면 중요 한 경고에 대해 필터링 된 백업 경고가 열립니다.

![중요한 알림에 대해 필터링된 백업 경고 메뉴](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

위의 이미지에서 [백업 경고] 메뉴의 경우 [상태]는 [활성], [심각도]는 [위험], 시간은 이전 24시간을 기준으로 필터링되었습니다.

### <a name="backup-pre-check-status"></a>백업 사전 검사 상태

백업 사전 검사는 Vm의 구성을 확인 하 여 백업에 부정적인 영향을 미칠 수 있는 문제를 확인 합니다. Recovery Services 자격 증명 모음 대시보드에서 직접 볼 수 있도록이 정보를 집계 하 고 성공적인 파일 일치 또는 응용 프로그램 일치 백업에 대 한 권장 조치를 제공 합니다. 인프라를 요구 하지 않으며 추가 비용이 없습니다.  

백업 사전 검사는 Azure Vm에 대 한 예약 된 백업 작업의 일부로 실행 됩니다. 다음 상태 중 하나로 결론을 합니다.

* **통과**:이 상태는 VM의 구성이 성공적인 백업을 유발 하 고 정정 작업을 수행 하지 않아야 함을 나타냅니다.
* **경고**:이 상태는 VM의 구성에서 백업 오류가 발생할 *수* 있는 하나 이상의 문제를 나타냅니다. 성공적인 백업을 보장 하기 위해 *권장 되는* 단계를 제공 합니다. 예를 들어 최신 VM 에이전트를 설치 하지 않으면 백업이 간헐적으로 실패할 수 있습니다. 이 경우 경고 상태가 제공 됩니다.
* **위험**:이 상태는 백업 실패 *를 유발 하는 VM* 의 구성에서 하나 이상의 중요 한 문제를 나타내며 성공적인 백업을 위해 *필요한* 단계를 제공 합니다. 예를 들어 VM의 NSG 규칙에 대 한 업데이트로 인해 발생 하는 네트워크 문제로 인해 VM이 Azure Backup 서비스와 통신 하지 못하도록 하는 작업이 실패 합니다. 이 경우 위험 상태가 제공 됩니다.

아래 단계에 따라 Recovery Services 자격 증명 모음에서 VM 백업에 대 한 백업 사전 검사에 의해 보고 된 모든 문제를 해결 합니다.

* Recovery Services 자격 증명 모음 대시보드에서 **백업 사전 검사 상태 (Azure vm)** 타일을 선택 합니다.
* 백업 사전 검사 상태가 **위험** 또는 **경고**인 VM을 선택 합니다. 이 작업을 수행 하면 **VM 세부 정보** 창이 열립니다.
* 창 맨 위에 있는 창 알림을 선택 하 여 구성 문제 설명 및 수정 단계를 표시 합니다.

## <a name="manage-backup-alerts"></a>Backup 경고 관리

백업 경고 메뉴에 액세스 하려면 Recovery Services 자격 증명 모음 메뉴에서 **백업 경고**를 선택 합니다.

![Backup 경고](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

[백업 경고] 보고서에는 자격 증명 모음에 대한 알림이 나열됩니다.

![Backup 경고 보고서](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>경고

[백업 경고] 목록에는 필터링된 알림에 대해 선택된 정보가 표시됩니다. [백업 경고] 메뉴에서 중요한 알림 또는 경고 알림을 필터링할 수 있습니다.

| 경고 수준 | 알림을 생성하는 이벤트 |
| ----------- | ----------- |
| 중요 | 다음과 같은 경우에 중요한 알림을 받습니다. 백업 작업이 실패하고 복구 작업이 실패하고 서버의 보호를 중지했지만 데이터를 보존할 때.|
| 경고 | 백업 작업이 완료 되었지만 경고가 발생 하면 경고 알림이 표시 됩니다. 예를 들어 손상 문제로 인해 파일이 100 개 미만으로 백업 되지 않거나 100만 파일 보다 큰 경우에는 백업 되지 않습니다. |
| 정보 제공 | 현재는 사용되는 정보 알림이 없습니다. |

### <a name="viewing-alert-details"></a>알림 세부 정보 보기

[백업 경고] 보고서는 각 알림에 대한 8가지 세부 정보를 추적합니다. 보고서의 세부 정보를 편집하려면 **열 선택** 단추를 사용합니다.

![백업 경고 열 선택 단추](./media/backup-azure-manage-windows-server/backup-alerts.png)

기본적으로 **최근 발생 시간**을 제외한 모든 세부 정보가 보고서에 표시됩니다.

* 경고
* 백업 항목
* 보호된 서버
* 심각도
* Duration
* 만든 시간
* 상태
* 최근 발생 시간

### <a name="change-the-details-in-alerts-report"></a>경고 보고서의 세부 정보 변경

1. 보고서 정보를 변경 하려면 **백업 경고** 메뉴에서 **열 선택**을 선택 합니다.

   ![열 선택 선택](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   **열 선택** 메뉴가 열립니다.

2. **열 선택** 메뉴에서 보고서에 표시하려는 세부 정보를 선택합니다.

    ![열 선택 메뉴](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. **완료** 를 선택 하 여 변경 내용을 저장 하 고 열 선택 메뉴를 닫습니다.

   변경을 수행 하지만 변경 내용을 유지 하지 않으려는 경우 **다시 설정** 을 선택 하 여 마지막으로 저장 된 구성으로 선택한를 반환 합니다.

### <a name="change-the-filter-in-alerts-report"></a>경고 보고서의 필터 변경

**필터** 메뉴를 사용하여 알림에 대한 심각도, 상태, 시작 시간 및 종료 시간을 변경합니다.

> [!NOTE]
> [백업 경고] 필터를 편집해도 자격 증명 모음 [개요] 대시보드의 중요한 알림 또는 경고 알림은 변경되지 않습니다.
>  

1. 백업 경고 필터를 변경 하려면 백업 경고 메뉴에서 **필터**를 선택 합니다.

   ![필터 메뉴 선택](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   [필터] 메뉴가 표시됩니다.

   ![필터 경고 메뉴](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. 심각도, 상태, 시작 시간 또는 종료 시간을 편집 하 고 **완료** 를 선택 하 여 변경 내용을 저장 합니다.

## <a name="configuring-notifications-for-alerts"></a>경고 알림 구성

경고 알림 또는 중요한 알림이 발생하면 이메일을 생성하도록 알림을 구성합니다. 매시간 또는 특정 알림이 발생할 때 이메일 알림을 보낼 수 있습니다.

   ![경고 필터링](./media/backup-azure-manage-windows-server/configure-notification.png)

기본적으로 이메일 알림은 **끄기**입니다. 전자 메일 알림을 중지 하려면 **끄기** 를 선택 합니다.

그룹화하지 않거나 알림을 생성할 수 있는 항목이 많지 않으면 **알림** 컨트롤에서 **경고별**을 선택합니다. 모든 경고는 하나의 알림(기본 설정)으로 처리되며 해결 전자 메일이 즉시 전송됩니다.

**시간별 요약**을 선택하면 받는 사람에게 마지막 한 시간 동안 생성되었지만 해결되지 않은 경고를 설명하는 이메일을 보냅니다. 한 시간이 끝날 때 확인 메일이 전송됩니다.

이메일을 생성하는 데 사용되는 경고 심각도([위험] 또는 [경고])를 선택합니다. 현재 정보 알림이 없습니다.

## <a name="manage-backup-items"></a>Backup 항목 관리

Recovery Services 자격 증명 모음은 다양한 유형의 백업 데이터를 보유합니다. 백업할 수 있는 항목에 [대해 자세히 알아보세요](backup-overview.md#what-can-i-back-up) . 다양 한 서버, 컴퓨터, 데이터베이스 및 작업을 관리 하려면 **백업 항목** 타일을 선택 하 여 자격 증명 모음의 내용을 확인 합니다.

![Backup 항목 타일](./media/backup-azure-manage-windows-server/backup-items.png)

[백업 관리 유형]별로 구성된 [백업 항목] 목록이 열립니다.

![백업 항목 목록](./media/backup-azure-manage-windows-server/list-backup-items.png)

특정 유형의 보호 된 인스턴스를 탐색 하려면 백업 관리 유형 열에서 항목을 선택 합니다. 예를 들어 위의 이미지에는 이 자격 증명 모음에서 보호된 두 개의 Azure 가상 머신이 있습니다. **Azure 가상 컴퓨터**를 선택 하면이 자격 증명 모음에서 보호 된 가상 컴퓨터 목록이 열립니다.

![보호 된 가상 컴퓨터 목록](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

가상 머신 목록에는 연결된 리소스 그룹, 이전 [백업 사전 검사](#backup-pre-check-status), 마지막 백업 상태 및 최근 복원 지점 날짜 등 유용한 데이터가 있습니다. 마지막 열의 줄임표를 사용하면 일반적인 작업을 트리거하는 메뉴를 엽니다. 열에 제공된 유용한 데이터는 각 백업 유형마다 다릅니다.

![일반 작업에 대 한 줄임표 메뉴 열기](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>Backup 작업 관리

자격 증명 모음 대시보드의 **백업 작업** 타일에는 지난 24시간 동안 진행 중인 작업 또는 실패한 작업의 수가 표시됩니다. 타일에서 잠시 동안 [백업 작업] 메뉴를 제공합니다.

![뒤로 작업 타일](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

작업에 대 한 추가 정보를 보려면 **진행 중** 또는 **실패** 를 선택 하 여 해당 상태에 대해 필터링 된 백업 작업 메뉴를 엽니다.

### <a name="backup-jobs-menu"></a>백업 작업 메뉴

**백업 작업** 메뉴에는 [항목 종류], [작업, 상태], [시작 시간] 및 [기간]에 대한 정보가 표시됩니다.  

백업 작업 메뉴를 열려면 자격 증명 모음의 주 메뉴에서 **백업 작업**을 선택 합니다.

![백업 작업 선택](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

[백업 작업] 목록이 열립니다.

![백업 작업 목록](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

[백업 작업] 메뉴에는 지난 24시간 동안 모든 백업 유형의 모든 작업에 대한 상태가 표시됩니다. **필터**를 사용하여 필터를 변경합니다. 필터에 대해서는 다음 섹션에서 설명합니다.

필터를 변경하려면 다음을 수행합니다.

1. 자격 증명 모음 백업 작업 메뉴에서 **필터**를 선택 합니다.

   ![백업 작업에 대 한 필터 선택](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    [필터] 메뉴가 열립니다.

   ![백업 작업에 대 한 필터 메뉴가 열립니다.](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. 필터 설정을 선택 하 고 **완료**를 선택 합니다. 필터링된 목록이 새 설정에 따라 새로 고쳐집니다.

#### <a name="item-type"></a>항목 유형

항목 종류는 보호된 인스턴스에 대한 백업 관리 유형입니다. 여기에는 네 가지 종류가 있으며, 다음 목록을 참조하세요. 하나 또는 모든 항목 종류를 볼 수 있습니다. 둘 또는 세 개의 항목 종류는 선택할 수 없습니다. 사용 가능한 항목 종류는 다음과 같습니다.

* 모든 항목 종류
* Azure 가상 머신
* 파일 및 폴더
* Azure Storage
* Azure 워크로드

#### <a name="operation"></a>작업(Operation)

하나 또는 모든 작업을 볼 수 있습니다. 둘 또는 세 개의 작업은 선택할 수 없습니다. 사용 가능한 작업은 다음과 같습니다.

* 모든 작업
* 등록
* 백업 구성
* Backup
* 복원
* 백업 사용 안 함
* 백업 데이터 삭제

#### <a name="status"></a>상태

하나 또는 모든 상태를 볼 수 있습니다. 둘 또는 세 개의 상태는 선택할 수 없습니다. 사용 가능한 상태는 다음과 같습니다.

* 모든 상태
* Completed
* 진행 중
* 실패
* 취소됨
* 경고와 함께 완료됨

#### <a name="start-time"></a>시작 시간

쿼리가 시작되는 날짜와 시간입니다. 기본값은 24시간입니다.

#### <a name="end-time"></a>종료 시간

쿼리가 종료되는 날짜와 시간입니다.

### <a name="export-jobs"></a>작업 내보내기

**작업 내보내기**를 사용하여 모든 작업 메뉴 정보가 포함된 스프레드시트를 만듭니다. 스프레드시트에는 모든 작업에 대한 요약과 각 작업에 대한 개별 시트가 포함된 하나의 시트가 있습니다.

작업 정보를 스프레드시트로 내보내려면 **작업 내보내기**를 선택 합니다. 서비스에서 날짜 및 자격 증명 모음 이름을 사용하여 스프레드시트를 만들지만 이름은 변경할 수 있습니다.

## <a name="monitor-backup-usage"></a>Backup 사용 모니터링

대시보드의 [백업 스토리지] 타일은 Azure에서 사용된 스토리지를 보여 줍니다. 다음에 대한 스토리지 사용량이 제공됩니다.

* 자격 증명 모음과 연결된 클라우드 LRS 스토리지 사용량
* 자격 증명 모음과 연결된 클라우드 GRS 스토리지 사용량

## <a name="troubleshooting-monitoring-issues"></a>모니터링 문제 해결

**문제:** Azure Backup 에이전트의 작업 및/또는 경고가 포털에 표시 되지 않습니다.

**문제 해결 단계:**```OBRecoveryServicesManagementAgent``` 프로세스에서 Azure Backup 서비스 작업 및 경고 데이터를 보냅니다. 때때로 이 프로세스가 멈추거나 종료될 수 있습니다.

1. 프로세스가 실행되고 있지 않은지 확인하려면 **작업 관리자**를 열고 ```OBRecoveryServicesManagementAgent```가 실행되고 있는지 확인합니다.

2. 프로세스가 실행되고 있지 않으면 **제어판**을 열고 서비스 목록을 검색합니다. **Microsoft Azure Recovery Services 관리 에이전트**를 시작하거나 다시 시작합니다.

    자세한 내용은 다음 위치에 있는 로그를 검색합니다.<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*` 예:<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>다음 단계

* [Azure에서 Windows Server 또는 Windows 클라이언트 복원](backup-azure-restore-windows-server.md)
* Azure Backup에 대한 자세한 내용은 [Azure Backup 개요](./backup-overview.md)
