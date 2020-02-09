---
title: Recovery Services 자격 증명 모음에 Azure VM 백업
description: Azure Backup를 사용 하 여 Recovery Services 자격 증명 모음에서 Azure Vm을 백업 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: aeadd7bc798f690c67eef38c6dc645204ff39115
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2020
ms.locfileid: "76705550"
---
# <a name="back-up-azure-vms-in-a-recovery-services-vault"></a>Recovery Services 자격 증명 모음에 Azure VM 백업

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용 하 여 Recovery Services 자격 증명 모음에서 Azure vm을 백업 하는 방법을 설명 합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
>
> * Azure VM을 준비합니다.
> * 자격 증명 모음을 만듭니다.
> * Vm을 검색 하 고 백업 정책을 구성 합니다.
> * Azure Vm에 대한 백업을 사용 하도록 설정 합니다.
> * 초기 백업을 실행합니다.

> [!NOTE]
> 이 문서에서는 자격 증명 모음을 설정 하 고 백업할 Vm을 선택 하는 방법을 설명 합니다. 여러 VM을 백업하는 경우에 유용합니다. 또는 VM 설정에서 직접 [단일 AZURE vm을 백업할](backup-azure-vms-first-look-arm.md) 수 있습니다.

## <a name="before-you-start"></a>시작하기 전에

* Azure VM 백업 아키텍처를 [검토](backup-architecture.md#architecture-built-in-azure-vm-backup) 합니다.
* Azure VM 백업 및 백업 확장에 대해 [자세히 알아봅니다](backup-azure-vms-introduction.md).
* 백업을 구성 하기 전에 [지원 매트릭스를 검토](backup-support-matrix-iaas.md) 합니다.

또한 경우에 따라 몇 가지 작업을 수행 해야 할 수도 있습니다.

* Vm **에 vm 에이전트 설치**: Azure Backup는 컴퓨터에서 실행 되는 azure vm 에이전트에 확장을 설치 하 여 azure vm을 백업 합니다. Azure marketplace 이미지에서 VM을 만든 경우 에이전트가 설치 되 고 실행 됩니다. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션하는 경우 [에이전트를 수동으로 설치](#install-the-vm-agent)해야 할 수 있습니다.

## <a name="create-a-vault"></a>자격 증명 모음 만들기

 자격 증명 모음은 백업과 시간 경과에 따라 생성된 복구 지점을 저장하고 백업된 머신과 연결된 백업 정책을 저장합니다. 다음과 같이 자격 증명 모음을 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 검색에 **Recovery Services**을 입력 합니다. **서비스**에서 **Recovery Services 자격 증명 모음**을 클릭 합니다.

     ![Recovery Services 자격 증명 모음 검색](./media/backup-azure-arm-vms-prepare/browse-to-rs-vaults-updated.png)

3. **Recovery Services 자격 증명 모음** 메뉴에서 **+ 추가**를 클릭 합니다.

     ![Recovery Services 자격 증명 모음 만들기 2단계](./media/backup-azure-arm-vms-prepare/rs-vault-menu.png)

4. **Recovery Services 자격 증명 모음**에서 자격 증명 모음을 식별 하기 위한 이름을 입력 합니다.
    * 이름은 Azure 구독에 대해 고유해야 합니다.
    * 2~50자를 포함할 수 있습니다.
    * 문자로 시작해야 하며, 문자, 숫자, 하이픈만 사용할 수 있습니다.
5. 자격 증명 모음을 만들어야 하는 Azure 구독, 리소스 그룹 및 지역을 선택 합니다. 그런 다음, **만들기**를 클릭합니다.
    * 자격 증명 모음을 만드는 데 시간이 걸릴 수 있습니다.
    * 포털의 오른쪽 위 영역에 있는 상태 알림을 모니터링합니다.

자격 증명 모음이 생성 되 면 Recovery Services 자격 증명 모음 목록에 표시 됩니다. 자격 증명 모음이 표시되지 않으면 **새로 고침**을 선택합니다.

![백업 자격 증명 모음 목록](./media/backup-azure-arm-vms-prepare/rs-list-of-vaults.png)

>[!NOTE]
> 이제 Azure Backup Azure Backup 서비스에서 만든 리소스 그룹 이름을 사용자 지정할 수 있습니다. 자세한 내용은 [Azure Backup 리소스 그룹 Virtual Machines](backup-during-vm-creation.md#azure-backup-resource-group-for-virtual-machines)을 참조 하세요.

### <a name="modify-storage-replication"></a>저장소 복제 수정

기본적으로 자격 증명 모음은 [GRS (지역 중복 저장소)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs)를 사용 합니다.

* 자격 증명 모음이 기본 백업 메커니즘인 경우 GRS를 사용 하는 것이 좋습니다.
* 저렴 한 옵션으로 [LRS (로컬 중복 저장소)](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 를 사용할 수 있습니다.

저장소 복제 유형을 다음과 같이 수정 합니다.

1. 새 자격 증명 모음에서 **설정** 섹션의 **속성** 을 클릭 합니다.
2. **속성**의 **백업 구성**에서 **업데이트**를 클릭 합니다.
3. 저장소 복제 유형을 선택 하 고 **저장**을 클릭 합니다.

      ![새 자격 증명 모음의 스토리지 구성 설정](./media/backup-try-azure-backup-in-10-mins/full-blade.png)

> [!NOTE]
   > 자격 증명 모음을 설정 하 고 백업 항목을 포함 한 후에는 저장소 복제 유형을 수정할 수 없습니다. 이 작업을 수행 하려는 경우 자격 증명 모음을 다시 만들어야 합니다.

## <a name="apply-a-backup-policy"></a>백업 정책 적용

자격 증명 모음에 대한 백업 정책을 구성 합니다.

1. 자격 증명 모음에서 **개요** 섹션의 **+ Backup** 을 클릭 합니다.

   ![백업 단추](./media/backup-azure-arm-vms-prepare/backup-button.png)

2. **워크 로드가 실행 되는 위치** > **백업 목표** 에서 **Azure**를 선택 합니다. **백업할 항목** 에서 **가상 컴퓨터** >  **확인**을 선택 합니다. 이렇게 하면 자격 증명 모음에 VM 확장을 등록합니다.

   ![백업 및 백업 목표 창](./media/backup-azure-arm-vms-prepare/select-backup-goal-1.png)

3. **백업 정책**에서 자격 증명 모음과 연결하려는 정책을 선택합니다.
    * 기본 정책은 하루에 한 번 VM을 백업 합니다. 매일 백업은 30 일 동안 보존 됩니다. 즉시 복구 스냅숏은 이틀 동안 보존 됩니다.
    * 기본 정책을 사용 하지 않으려면 **새로 만들기**를 선택 하 고 다음 절차에 설명 된 대로 사용자 지정 정책을 만듭니다.

      ![기본 백업 정책](./media/backup-azure-arm-vms-prepare/default-policy.png)

4. **가상 컴퓨터 선택**에서 정책을 사용 하 여 백업할 vm을 선택 합니다. 그런 후 **OK**를 클릭합니다.

   * 선택한 Vm의 유효성이 검사 됩니다.
   * 자격 증명 모음과 동일한 지역에만 VM을 선택할 수 있습니다.
   * VM은 단일 자격 증명 모음에만 백업할 수 있습니다.

     !["가상 머신 선택" 창](./media/backup-azure-arm-vms-prepare/select-vms-to-backup.png)

5. **백업**에서 **백업 사용**을 클릭 합니다. 이렇게 하면 자격 증명 모음과 VM에 정책을 배포하고 Azure VM에서 실행되는 VM 에이전트에 백업 확장을 설치합니다.

     !["백업 사용" 단추](./media/backup-azure-arm-vms-prepare/vm-validated-click-enable.png)

백업 사용 단추를 클릭한 후 다음을 수행합니다.

* 백업 서비스는 VM의 실행 여부와 상관 없이 백업 확장을 설치합니다.
* 초기 백업은 백업 일정에 따라 실행 됩니다.
* 백업이 실행 되는 경우 다음을 참고 하십시오.
  * 실행 중인 VM은 응용 프로그램 일치 복구 지점을 캡처할 가능성이 가장 큽니다.
  * 그러나 VM이 해제 된 경우에도 백업 됩니다. 이러한 VM을 오프 라인 VM 이라고 합니다. 이 경우 복구 지점은 크래시 일관성을 유지 합니다.
* Azure Vm의 백업을 허용 하기 위해 명시적인 아웃 바운드 연결이 필요 하지 않습니다.

### <a name="create-a-custom-policy"></a>사용자 지정 정책 만들기

새 백업 정책을 만들도록 선택한 경우 정책 설정을 입력 합니다.

1. **정책 이름**에서 의미 있는 이름을 지정 합니다.
2. **백업 일정**에서 백업을 수행 해야 하는 시기를 지정 합니다. Azure Vm에 대해 매일 또는 매주 백업을 수행할 수 있습니다.
3. **즉시**복원에서 즉시 복원에 대한 스냅숏을 로컬로 보존할 기간을 지정 합니다.
    * 복원 하면 백업 된 VM 디스크가 저장소에서 네트워크를 통해 복구 저장소 위치로 복사 됩니다. 즉시 복원을 사용 하면 백업 데이터가 자격 증명 모음에 전송 될 때까지 기다리지 않고 백업 작업 중에 수행 되는 로컬로 저장 된 스냅숏을 활용할 수 있습니다.
    * 하루에서 5 일 사이에 즉시 복원에 대한 스냅숏을 유지할 수 있습니다. 기본 설정은 이틀입니다.
4. **보존 범위**에서 매일 또는 매주 백업 시점을 유지 하려는 기간을 지정 합니다.
5. **월간 백업 지점 보존**에서 매일 또는 매주 백업의 월간 백업을 유지할지 여부를 지정 합니다.
6. **확인** 을 클릭하여 정책을 저장합니다.

    ![새 백업 정책](./media/backup-azure-arm-vms-prepare/new-policy.png)

> [!NOTE]
   > Azure Backup은 Azure VM 백업을 위한 일광 절약 시간 변경에 대한 자동 시계 조정을 지원하지 않습니다. 시간이 변경 되 면 필요에 따라 백업 정책을 수동으로 수정 합니다.

## <a name="trigger-the-initial-backup"></a>초기 백업 트리거

초기 백업은 일정에 따라 실행 되지만 다음과 같이 즉시 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **Backup 항목**에서 **Azure Virtual Machine**을 클릭 합니다.
3. **백업 항목** 목록에서 줄임표 (...)를 클릭 합니다.
4. **지금 백업**을 클릭합니다.
5. **지금 백업**에서 달력 컨트롤을 사용 하 여 복구 지점을 유지할 마지막 날을 선택 합니다. 그런 후 **OK**를 클릭합니다.
6. 포털 알림을 모니터링합니다. 자격 증명 모음 대시보드 > **백업 작업** > **진행 중**에서 작업 진행률을 모니터링할 수 있습니다. VM의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="verify-backup-job-status"></a>백업 작업 상태 확인

각 VM 백업에 대한 백업 작업 세부 정보는 다음 두 단계로 구성 됩니다. **스냅숏** 단계 다음에는 **자격 증명 모음으로 데이터 전송** 단계가 나옵니다.<br/>
스냅숏 단계를 통해 **즉시 복원** 하기 위해 디스크와 함께 저장 되는 복구 지점의 가용성을 보장 하 고 사용자가 구성한 스냅숏 보존 기간에 따라 최대 5 일 동안 사용할 수 있습니다. 자격 증명 모음으로 데이터 전송은 장기 보존을 위해 자격 증명 모음에 복구 지점을 만듭니다. 데이터를 자격 증명 모음으로 전송은 스냅숏 단계가 완료 된 후에만 시작 됩니다.

  ![백업 작업 상태](./media/backup-azure-arm-vms-prepare/backup-job-status.png)

백 엔드에서 실행 되는 두 개의 **하위 작업** 은 아래와 같이 **백업 작업** 세부 정보 블레이드에서 확인할 수 있는 프런트 엔드 백업 작업용으로 하나입니다.

  ![백업 작업 상태](./media/backup-azure-arm-vms-prepare/backup-job-phase.png)

**데이터를 자격 증명 모음에 전송** 단계는 디스크 크기, 디스크당 변동 및 기타 여러 가지 요소에 따라 완료 하는 데 며칠이 걸릴 수 있습니다.

작업 상태는 다음 시나리오에 따라 달라질 수 있습니다.

**스냅샷** | **자격 증명 모음으로 데이터 전송** | **작업 상태**
--- | --- | ---
Completed | 진행 중 | 진행 중
Completed | 건너뜀 | Completed
Completed | Completed | Completed
Completed | 실패 | 완료 되었지만 경고 발생
실패 | 실패 | 실패

이제이 기능을 사용 하는 경우 동일한 VM에 대해 두 개의 백업이 병렬로 실행 될 수 있지만 두 단계 (스냅숏, 자격 증명 모음에 데이터 전송)는 하나의 하위 작업만 실행할 수 있습니다. 따라서 진행 중인 백업 작업이 발생 하 여 다음 날의 백업이 실패 하는 경우이 분리 기능이 사용 되지 않습니다. 이후 날의 백업은 이전 날짜의 백업 작업이 진행 중일 경우 **자격 증명 모음으로 데이터를 전송** 하는 동안 스냅숏이 완료 될 수 있습니다.
자격 증명 모음에 생성 된 증분 복구 지점은 자격 증명 모음에서 만든 마지막 복구 지점부터 모든 변동 (code)을 캡처합니다. 사용자에 게는 비용에 영향을 주지 않습니다.

## <a name="optional-steps"></a>선택적 단계

### <a name="install-the-vm-agent"></a>VM 에이전트 설치

Azure Backup은 컴퓨터에서 실행 중인 Azure VM 에이전트에 확장을 설치하여 Azure VM을 백업합니다. Azure Marketplace 이미지에서 VM을 만든 경우 에이전트가 설치 되어 실행 되 고 있는 것입니다. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션하는 경우 표에 요약 된 대로 에이전트를 수동으로 설치 해야 할 수 있습니다.

**VM** | **세부 정보**
--- | ---
**Windows** | 1. 에이전트 MSI 파일을 [다운로드 하 여 설치](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409) 합니다.<br/><br/> 2. 컴퓨터에 대한 관리자 권한으로를 설치 합니다.<br/><br/> 3. 설치를 확인 합니다. VM의 >  *c:\windowsazure\\pupv\\pupv\\pupv\ppv\pv* **세부 정보** 탭에서 **제품 버전** 을 2.6.1198.718 이상 이상으로 설정 해야 합니다.<br/><br/> 에이전트를 업데이트 하는 경우 백업 작업이 실행 되 고 있지 않은지 확인 한 후 [에이전트를 다시 설치](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)하십시오.
**Linux** | 배포의 패키지 리포지토리에서 RPM 또는 DEB 패키지를 사용 하 여를 설치 합니다. 이는 Azure Linux 에이전트를 설치 하 고 업그레이드 하는 데 선호 되는 방법입니다. 모든 [인증 배포 공급자](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)는 이미지 및 리포지토리에 Azure Linux 에이전트 패키지를 통합합니다. 에이전트는 [GitHub](https://github.com/Azure/WALinuxAgent)에서 사용할 수 있지만 설치하지 않는 것이 좋습니다.<br/><br/> 에이전트를 업데이트 하는 경우 백업 작업이 실행 되 고 있지 않은지 확인 하 고 이진 파일을 업데이트 합니다.

>[!NOTE]
> **이제 Azure Backup는 Azure 가상 컴퓨터 백업 솔루션을 사용 하 여 선택적 디스크 백업 및 복원을 지원 합니다.**
>
>현재 Azure Backup에서는 가상 컴퓨터 백업 솔루션을 사용 하 여 VM의 모든 디스크 (운영 체제 및 데이터)를 백업 하는 작업을 지원 합니다. 디스크 제외 기능을 사용 하면 VM의 여러 데이터 디스크에서 하나 또는 몇 개를 백업 하는 옵션을 사용할 수 있습니다. 이는 백업 및 복원 요구에 효율적이 고 비용 효율적인 솔루션을 제공 합니다. 각 복구 지점에는 백업 작업에 포함 된 디스크의 데이터가 포함 되어 있으므로 복원 작업을 수행 하는 동안 지정 된 복구 지점에서 복원 된 디스크의 하위 집합을 사용할 수 있습니다. 이는 스냅숏과 자격 증명 모음 둘 다의 복원에 적용 됩니다.
>
>**미리 보기에 등록 하려면 AskAzureBackupTeam@microsoft.com에 씁니다.**

## <a name="next-steps"></a>다음 단계

* [AZURE vm 에이전트](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) 또는 [azure vm 백업](backup-azure-vms-troubleshoot.md)관련 문제를 해결 합니다.
* [복원](backup-azure-arm-restore-vms.md) Azure Vm.
