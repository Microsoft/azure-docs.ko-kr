---
title: Azure Portal를 사용 하 여 Vm 복원
description: Azure Portal을 사용하여 복구 지점에서 Azure Virtual Machine 복원
ms.reviewer: geg
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: d70db384101d838b2b65707090577156daa59b9a
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77138051"
---
# <a name="how-to-restore-azure-vm-data-in-azure-portal"></a>Azure Portal에서 Azure VM 데이터를 복원 하는 방법

이 문서에서는 [Azure Backup](backup-overview.md) Recovery Services 자격 증명 모음에 저장된 복구 지점에서 Azure VM 데이터를 복원하는 방법에 대해 설명합니다.

## <a name="restore-options"></a>복원 옵션

Azure Backup은 VM을 복원하는 다양한 방법을 제공합니다.

**복원 옵션** | **세부 정보**
--- | ---
**새 VM 만들기** | 복원 지점에서 기본 VM을 빠르게 만들고, 준비하고, 실행할 수 있습니다.<br/><br/> VM의 이름을 지정 하 고, 리소스 그룹 및 가상 네트워크 (VNet)를 선택 하 고, 복원 된 VM에 대 한 저장소 계정을 지정할 수 있습니다. 새 VM은 원본 VM과 동일한 지역에 만들어야 합니다.
**디스크 복원** | 새 VM을 만드는 데 사용할 수 있는 VM 디스크를 복원 합니다.<br/><br/> Azure Backup은 VM을 사용자 지정하고 만드는 데 도움이 되는 템플릿을 제공합니다. <br/><br> 복원 작업은 다운로드하여 사용자 지정 VM 설정을 지정하고 VM을 만드는 데 사용할 수 있는 템플릿을 생성합니다.<br/><br/> 지정한 리소스 그룹에 디스크가 복사 됩니다.<br/><br/> 또는 디스크를 기존 VM에 연결하거나 PowerShell을 사용하여 새 VM을 만들 수 있습니다.<br/><br/> 이 옵션은 VM을 사용자 지정하거나, 백업 시 존재하지 않았던 구성 설정을 추가하거나, 템플릿 또는 PowerShell을 사용하여 구성해야 하는 설정을 추가하려는 경우에 유용합니다.
**기존 항목 바꾸기** | 디스크를 복원하고 이 디스크를 사용하여 기존 VM의 디스크를 바꿀 수 있습니다.<br/><br/> 현재 VM이 있어야 합니다. 삭제 된 경우에는이 옵션을 사용할 수 없습니다.<br/><br/> Azure Backup는 디스크를 교체 하기 전에 기존 VM의 스냅숏을 만들어 지정한 스테이징 위치에 저장 합니다. VM에 연결 된 기존 디스크는 선택한 복원 지점으로 대체 됩니다.<br/><br/> 스냅숏은 자격 증명 모음에 복사 되 고 보존 정책에 따라 보존 됩니다. <br/><br/> 디스크 바꾸기 작업 후에는 원래 디스크가 리소스 그룹에 유지 됩니다. 필요 하지 않은 경우 원본 디스크를 수동으로 삭제 하도록 선택할 수 있습니다. <br/><br/>기존 항목 바꾸기는 암호화되지 않은 관리 VM에 대해 지원됩니다. 관리되지 않는 디스크, [일반화된 VM](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) 또는 [사용자 지정 이미지를 사용하여 만든](https://azure.microsoft.com/resources/videos/create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell/) VM에 대해서는 지원되지 않습니다.<br/><br/> 현재 VM보다 많거나 적은 디스크가 복원 지점에 있는 경우 복원 지점의 디스크 수에는 VM 구성만 반영됩니다.
**지역 간 (보조 지역)** | 지역 간 복원은 [azure 쌍을 이루는 지역](https://docs.microsoft.com/azure/best-practices-availability-paired-regions#what-are-paired-regions)인 보조 지역에서 azure vm을 복원 하는 데 사용할 수 있습니다.<br><br> 백업이 보조 지역에서 수행 되는 경우 선택한 복구 지점에 대 한 모든 Azure Vm을 복원할 수 있습니다.<br><br> 이 기능은 아래 옵션에 사용할 수 있습니다.<br> [VM을 만드는](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm) *  <br> * [복원 디스크](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks) <br><br> 현재 [기존 디스크 바꾸기](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#replace-existing-disks) 옵션을 지원 하지 않습니다.<br><br> 권한<br> 보조 지역에 대 한 복원 작업은 Backup Admins 및 App Admins를 통해 수행할 수 있습니다.

> [!NOTE]
> Azure VM에서 특정 파일과 폴더를 복구할 수도 있습니다. [자세히 알아봅니다](backup-azure-restore-files-from-vm.md).
>
> Azure VM에 대해 [최신 버전](backup-instant-restore-capability.md)의 Azure Backup을 실행하는 경우(즉시 복원이라고 함), 스냅샷은 최대 7일 동안 유지되며 백업 데이터를 자격 증명 모음으로 보내기 전에 스냅샷에서 VM을 복원할 수 있습니다. 지난 7일간의 백업에서 VM을 복원하려면 자격 증명 모음이 아니라 스냅샷에서 복원하는 것이 더 빠릅니다.

## <a name="storage-accounts"></a>Storage 계정

저장소 계정에 대 한 몇 가지 세부 정보:

- **Vm 만들기**: 새 vm을 만들 때 지정 하는 저장소 계정에 vm이 배치 됩니다.
- 디스크 **복원**: 디스크를 복원 하면 지정한 저장소 계정에 디스크가 복사 됩니다. 복원 작업은 다운로드 하 여 사용자 지정 VM 설정을 지정 하는 데 사용할 수 있는 템플릿을 생성 합니다. 이 템플릿은 지정 된 저장소 계정에 배치 됩니다.
- **디스크 바꾸기**: 기존 vm에서 디스크를 교체 하는 경우 Azure Backup는 디스크를 교체 하기 전에 기존 vm의 스냅숏을 만듭니다. 스냅숏은 지정한 스테이징 위치 (저장소 계정)에 저장 됩니다. 이 저장소 계정은 복원 프로세스 중에 스냅숏을 일시적으로 저장 하는 데 사용 되며, 나중에 쉽게 제거할 수 있는 새 계정을 만드는 것이 좋습니다.
- **저장소 계정 위치**: 저장소 계정은 자격 증명 모음과 동일한 지역에 있어야 합니다. 이러한 계정만 표시 됩니다. 위치에 저장소 계정이 없는 경우 새로 만들어야 합니다.
- **저장소 유형**: Blob 저장소는 지원 되지 않습니다.
- **저장소 중복성**: ZRS (영역 중복 저장소)는 지원 되지 않습니다. 계정에 대 한 복제 및 중복성 정보는 계정 이름 뒤에 괄호 안에 표시 됩니다.
- **Premium storage**:
  - Premium이 아닌 Vm을 복원 하는 경우 premium storage 계정은 지원 되지 않습니다.
  - 관리 Vm을 복원 하는 경우 네트워크 규칙으로 구성 된 premium storage 계정은 지원 되지 않습니다.

## <a name="before-you-start"></a>시작하기 전 확인 사항

Vm을 복원 하려면 (새 VM 만들기) VM 복원 작업에 대 한 올바른 RBAC (역할 기반 액세스 제어) [권한이](backup-rbac-rs-vault.md#mapping-backup-built-in-roles-to-backup-management-actions) 있는지 확인 합니다.

사용 권한이 없는 경우 [디스크를 복원한](#restore-disks)다음 디스크가 복원 된 후 복원 작업의 일부로 생성 된 [템플릿을 사용](#use-templates-to-customize-a-restored-vm) 하 여 새 VM을 만들 수 있습니다.

## <a name="select-a-restore-point"></a>복원 지점 선택

1. 복원하려는 VM과 연결된 자격 증명 모음에서 **백업 항목**  > **Azure Virtual Machine**을 차례로 클릭합니다.
2. VM을 클릭합니다. 기본적으로 VM 대시보드에는 지난 30일간의 복구 지점이 표시됩니다. 30일 이전의 복구 지점을 표시하거나, 날짜, 시간 범위 및 다양한 유형의 스냅샷 일관성을 기준으로 복구 지점을 찾도록 필터링할 수 있습니다.
3. VM을 복원하려면 **VM 복원**을 클릭합니다.

    ![복원 지점](./media/backup-azure-arm-restore-vms/restore-point.png)

4. 복구에 사용할 복원 지점을 선택합니다.

## <a name="choose-a-vm-restore-configuration"></a>VM 복원 구성 선택

1. **복원 구성**에서 복원 옵션을 선택합니다.
    - **새로 만들기**: 새 VM을 만들려면이 옵션을 사용 합니다. 간단한 설정으로 VM을 만들거나, 디스크를 복원하고 사용자 지정된 VM을 만들 수 있습니다.
    - **기존**가상 컴퓨터 바꾸기: 기존 VM에서 디스크를 교체 하려면이 옵션을 사용 합니다.

        ![복원 구성 마법사](./media/backup-azure-arm-restore-vms/restore-configuration.png)

2. 선택한 복원 옵션에 대한 설정을 지정합니다.

## <a name="create-a-vm"></a>VM 만들기

[복원 옵션](#restore-options) 중 하나로, 복원 지점의 기본 설정을 사용하여 VM을 빠르게 만들 수 있습니다.

1. **복원 구성** > **새로 만들기** > **복원 유형**에서 **가상 머신 만들기**를 선택합니다.
2. **가상 컴퓨터 이름**에 구독에 존재 하지 않는 VM을 지정 합니다.
3. **리소스 그룹**에서 새 VM에 대해 기존 리소스 그룹을 선택하거나, 새 리소스 그룹을 전역 고유 이름으로 만듭니다. 이미 있는 이름을 할당하면 Azure에서 VM과 동일한 이름을 그룹에 할당합니다.
4. **가상 네트워크**에서 VM을 배치할 VNet를 선택합니다. 구독과 연관된 모든 VNet가 표시됩니다. 서브넷을 선택합니다. 기본적으로 첫 번째 서브넷이 선택됩니다.
5. **저장소 위치**에서 VM에 대 한 저장소 계정을 지정 합니다. [자세히 알아봅니다](#storage-accounts).

    ![복원 구성 마법사](./media/backup-azure-arm-restore-vms/recovery-configuration-wizard1.png)

6. **복원 구성**에서 **확인**을 선택합니다. **복원**에서 **복원**을 클릭하여 복원 작업을 트리거합니다.

## <a name="restore-disks"></a>디스크 복원

[복원 옵션](#restore-options) 중 하나로, 복원 지점에서 디스크를 만들 수 있습니다. 그런 다음, 이 디스크를 사용하여 다음 중 하나를 수행할 수 있습니다.

- 복원 작업 중에 생성된 템플릿을 사용하여 설정을 사용자 지정하고 VM 배포를 트리거합니다. 기본 템플릿 설정을 편집하고 VM 배포용 템플릿을 제출합니다.
- [복원된 디스크](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal)를 기존 VM에 연결합니다.
- PowerShell을 사용 하 여 복원 된 디스크에서 [새 VM을 만듭니다](https://docs.microsoft.com/azure/backup/backup-azure-vms-automation#create-a-vm-from-restored-disks) .

1. **복원 구성** > **새로 만들기** > **복원 유형**에서 **복원 디스크**를 선택합니다.
2. **리소스 그룹**에서 복원된 디스크에 대해 기존 리소스 그룹을 선택하거나, 새 리소스 그룹을 전역 고유 이름으로 만듭니다.
3. **스토리지 계정**에서 VHD를 복사할 계정을 지정합니다. [자세히 알아봅니다](#storage-accounts).

    ![복구 구성 완료](./media/backup-azure-arm-restore-vms/trigger-restore-operation1.png)

4. **복원 구성**에서 **확인**을 선택합니다. **복원**에서 **복원**을 클릭하여 복원 작업을 트리거합니다.

가상 머신에서 관리 디스크를 사용 하는 경우 **가상 머신 만들기** 옵션을 선택 하면 Azure Backup 지정 된 저장소 계정을 사용 하지 않습니다. **복원 디스크** 및 **인스턴트 복원**의 경우 저장소 계정은 템플릿을 저장 하는 데만 사용 됩니다. 지정 된 리소스 그룹에서 관리 디스크를 만듭니다.
가상 컴퓨터가 관리 되지 않는 디스크를 사용 하는 경우 저장소 계정에 blob으로 복원 됩니다.

### <a name="use-templates-to-customize-a-restored-vm"></a>템플릿을 사용하여 복원된 VM 사용자 지정

디스크가 복원되면 복원 작업의 일부로 생성된 템플릿을 사용하여 새 VM을 사용자 지정하고 만듭니다.

1. 관련 작업에 대한 **복원 작업 세부 정보**를 엽니다.

2. **복원 작업 세부 정보**에서 **템플릿 배포** 단추를 클릭하여 템플릿 배포를 시작합니다.

    ![복원 작업 드릴 다운](./media/backup-azure-arm-restore-vms/restore-job-drill-down1.png)

3. 템플릿에 제공된 VM 설정을 사용자 지정하려면 **템플릿 편집**을 클릭합니다. 사용자 지정을 추가하려면 **매개 변수 편집**을 클릭합니다.
    - 사용자 지정 템플릿에서 리소스를 배포하는 방법에 대해 [자세히 알아보세요](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).
    - 템플릿 작성에 대해 [자세히 알아보세요](../azure-resource-manager/templates/template-syntax.md).

   ![템플릿 배포 로드](./media/backup-azure-arm-restore-vms/edit-template1.png)

4. VM에 대한 사용자 지정 값을 입력하고, **사용 약관**에 동의하고, **구매**를 클릭합니다.

   ![템플릿 배포 제출](./media/backup-azure-arm-restore-vms/submitting-template1.png)

## <a name="replace-existing-disks"></a>기존 디스크 바꾸기

[복원 옵션](#restore-options) 중 하나로, 기존 VM 디스크를 선택한 복원 지점으로 바꿀 수 있습니다. 모든 복원 옵션을 [검토](#restore-options)합니다.

1. **복원 구성**에서 **기존 항목 바꾸기**를 클릭합니다.
2. **복원 유형**에서 **디스크 바꾸기**를 선택합니다. 이는 기존 VM 디스크를 바꾸는 데 사용할 복원 지점입니다.
3. **준비 위치**에서 복원 프로세스 중에 현재 관리 디스크의 스냅숏을 저장 해야 하는 위치를 지정 합니다. [자세히 알아봅니다](#storage-accounts).

   ![기존 항목을 바꾸는 구성 복원 마법사](./media/backup-azure-arm-restore-vms/restore-configuration-replace-existing.png)

## <a name="cross-region-restore"></a>지역 간 복원

[복원 옵션](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-options)중 하나로, crr (교차 지역 복원)을 사용 하면 azure 쌍을 이루는 지역에 있는 보조 지역에서 azure vm을 복원할 수 있습니다.

미리 보기 중에 기능에 등록 하려면 [시작 하기 전에 섹션](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-cross-region-restore)을 참조 하세요.

CRR을 사용 하도록 설정 되어 있는지 확인 하려면 [지역 간 복원 구성](backup-create-rs-vault.md#configure-cross-region-restore) 의 지침을 따르세요.

### <a name="view-backup-items-in-secondary-region"></a>보조 지역에서 백업 항목 보기

CRR을 사용 하는 경우 보조 지역에서 백업 항목을 볼 수 있습니다.

1. 포털에서 **Recovery Services 자격 증명 모음** > **백업 항목** 으로 이동 합니다.
2. 보조 **지역을 클릭 하** 여 보조 지역의 항목을 봅니다.

![보조 지역의 가상 컴퓨터](./media/backup-azure-arm-restore-vms/secbackedupitem.png)

![보조 지역 선택](./media/backup-azure-arm-restore-vms/backupitems-sec.png)

### <a name="restore-in-secondary-region"></a>보조 지역에서 복원

보조 지역 복원 사용자 환경은 주 지역 복원 사용자 환경과 유사 합니다. 복원 구성 블레이드에서 세부 정보를 구성 하 여 복원을 구성 하는 경우 보조 지역 매개 변수만 제공 하 라는 메시지가 표시 됩니다.

![복원할 VM 선택](./media/backup-azure-arm-restore-vms/sec-restore.png)

![복원 지점 선택](./media/backup-azure-arm-restore-vms/sec-rp.png)

![복원 구성](./media/backup-azure-arm-restore-vms/rest-config.png)

![진행 중인 복원 알림 트리거](./media/backup-azure-arm-restore-vms/restorenotifications.png)

- VM을 복원 하 고 만들려면 [Vm 만들기](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#create-a-vm)를 참조 하세요.
- 디스크로 복원 하려면 [디스크 복원](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#restore-disks)을 참조 하세요.

>[!NOTE]
>복원이 트리거되고 데이터 전송 단계에서는 복원 작업을 취소할 수 없습니다.

### <a name="monitoring-secondary-region-restore-jobs"></a>보조 지역 복원 작업 모니터링

1. 포털에서 **Recovery Services 자격 증명 모음** > **백업 작업** 으로 이동 합니다.
2. 보조 **지역을 클릭 하** 여 보조 지역의 항목을 봅니다.

![필터링 된 백업 작업](./media/backup-azure-arm-restore-vms/secbackupjobs.png)

## <a name="restore-vms-with-special-configurations"></a>특수 구성을 사용 하 여 Vm 복원

VM을 복원해야 하는 일반적인 시나리오는 여러 가지가 있습니다.

**시나리오** | **지침**
--- | ---
**하이브리드 사용 혜택을 사용하여 VM 복원** | Windows VM에서 [HUB(하이브리드 사용 혜택) 라이선스](../virtual-machines/windows/hybrid-use-benefit-licensing.md)를 사용하는 경우, 디스크를 복원하고 제공된 템플릿(**라이선스 유형** 이 **Windows_Server**로 설정됨) 또는 PowerShell을 사용하여 새 VM을 만듭니다.  이 설정은 VM을 만든 후에 적용할 수도 있습니다.
**Azure 데이터 센터 재해 중 VM 복원** | 자격 증명 모음에서 GRS를 사용하고 VM에 대한 기본 데이터 센터의 작동이 중단되면, Azure Backup에서 쌍을 이루는 데이터 센터로 백업된 VM을 복원할 수 있습니다. 쌍을 이루는 데이터 센터에서 스토리지 계정을 선택하고 정상적으로 복원합니다. Azure Backup는 쌍을 이루는 지역에서 계산 서비스를 사용 하 여 복원 된 VM을 만듭니다. 데이터 센터 복원력에 대해 [자세히 알아보세요](../resiliency/resiliency-technical-guidance-recovery-loss-azure-region.md).
**단일 도메인에서 단일 도메인 컨트롤러 VM 복원** | 다른 VM과 마찬가지로 VM을 복원합니다. 다음 사항에 유의합니다.<br/><br/> Active Directory 관점에서 Azure VM은 다른 VM과 비슷합니다.<br/><br/> Active Directory 복원 모드(DSRM)도 사용할 수 있으므로 모든 Directory Services 복원 모드를 실행할 수 있습니다. 가상화된 도메인 컨트롤러에 대한 백업 및 복원 고려 사항에 대해 [자세히 알아보세요](https://docs.microsoft.com/azure/backup/backup-azure-arm-restore-vms#post-restore-steps).
**단일 도메인에서 여러 도메인 컨트롤러 VM 복원** | 네트워크를 통해 동일한 도메인의 다른 도메인 컨트롤러에 연결할 수 있는 경우에는 모든 VM 처럼 도메인 컨트롤러를 복원할 수 있습니다. 도메인에서 마지막으로 남아 있는 도메인 컨트롤러이거나 격리된 네트워크에서 복구가 수행되면 [포리스트 복구](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)를 사용합니다.
**단일 포리스트에 여러 도메인 복원** | [포리스트 복구](https://docs.microsoft.com/windows-server/identity/ad-ds/manage/ad-forest-recovery-single-domain-in-multidomain-recovery)를 사용하는 것이 좋습니다.
**완전 복원** | Azure VM과 온-프레미스 하이퍼바이저의 주요 차이점은 Azure에서 사용할 수 있는 VM 콘솔이 없다는 것입니다. BMR(완전 복구) 유형 백업을 사용한 복구와 같은 특정 시나리오에서는 콘솔이 필요합니다. 하지만 자격 증명 모음의 VM 복원이 BMR로 완전히 대체됩니다.
**특수 네트워크 구성으로 VM 복원** | 특수 네트워크 구성에는 내부 또는 외부 부하 분산을 사용하거나, 여러 NICS를 사용하거나, 예약된 여러 IP 주소를 사용하는 VM이 포함되어 있습니다. [디스크 복원 옵션](#restore-disks)을 사용하여 이러한 VM을 복원합니다. 이 옵션은 지정 된 저장소 계정에 Vhd의 복사본을 만들고, 구성에 따라 [내부](https://azure.microsoft.com/documentation/articles/load-balancer-internal-getstarted/) 또는 [외부](/azure/load-balancer/quickstart-create-standard-load-balancer-powershell) 부하 분산 장치, [다중 NIC](../virtual-machines/windows/multiple-nics.md)또는 [여러 예약 된 IP 주소](../virtual-network/virtual-network-multiple-ip-addresses-powershell.md)를 사용 하 여 VM을 만들 수 있습니다.
**NIC/서브넷의 NSG (네트워크 보안 그룹)** | Azure VM 백업은 vnet, 서브넷 및 NIC 수준에서 NSG 정보를 백업 하 고 복원 하는 것을 지원 합니다.
**영역 고정 Vm** | Azure Backup은 고정 된 배열로 영역 설정 Vm의 백업 및 복원을 지원 합니다. [자세히 알아보기](https://azure.microsoft.com/global-infrastructure/availability-zones/)

## <a name="track-the-restore-operation"></a>복원 작업 추적

복원 작업이 트리거되면 백업 서비스에서 추적 작업을 만듭니다. Azure Backup에서 작업에 대한 알림을 포털에 표시합니다. 표시 되지 않는 경우 **알림** 기호를 선택한 다음 **모든 작업 보기** 를 선택 하 여 복원 프로세스 상태를 확인 합니다.

![트리거된 복원](./media/backup-azure-arm-restore-vms/restore-notification1.png)

 다음과 같이 복원을 추적합니다.

1. 작업에 대한 작업을 보려면 알림 하이퍼링크를 클릭합니다. 또는 자격 증명 모음에서 **백업 작업**을 클릭한 다음, 관련 VM을 클릭합니다.

    ![자격 증명 모음의 VM 목록](./media/backup-azure-arm-restore-vms/restore-job-in-progress1.png)

2. 복원 진행 상황을 모니터링하려면 상태가 **진행 중**인 복원 작업을 클릭합니다. 그러면 복원 진행률에 대 한 정보가 표시 되는 진행률 표시줄이 표시 됩니다.

    - **예상 복원 시간**: 초기에 복원 작업을 완료 하는 데 걸린 시간을 제공 합니다. 작업이 진행됨에 따라 소요 시간이 감소하고, 복원 작업이 완료되면 0에 도달합니다.
    - **복원 비율**: 수행되는 복원 작업에 대한 백분율을 표시합니다.
    - **전송 된 바이트 수**: 새 VM을 만들어 복원 하는 경우 전송 되는 총 바이트 수에 대해 전송 된 바이트 수를 표시 합니다.

## <a name="post-restore-steps"></a>복원 후 단계

VM이 복원되면 주의해야 할 몇 가지 사항이 있습니다.

- 백업 구성 중에 제공되는 확장이 설치되지만 사용하도록 설정되어 있지 않습니다. 문제가 있으면 확장을 다시 설치합니다.
- 백업된 VM에 고정 IP 주소가 있으면 복원된 VM에서 동적 IP 주소를 사용하도록 하여 충돌을 방지합니다. [고정 IP 주소는 복원된 VM에 추가](/previous-versions/azure/virtual-network/virtual-networks-reserved-private-ip#how-to-add-a-static-internal-ip-to-an-existing-vm)할 수 있습니다.
- 복원된 VM에는 가용성 집합이 없습니다. 복원 디스크 옵션을 사용 하는 경우 제공 된 템플릿 또는 PowerShell을 사용 하 여 디스크에서 VM을 만들 때 [가용성 집합을 지정할](../virtual-machines/windows/tutorial-availability-sets.md) 수 있습니다.
- Ubuntu와 같은 cloud-init 기반 Linux 배포를 사용하면 보안상의 이유로 복원 후에 암호가 차단됩니다. 복원된 VM에서 VMAccess 확장을 사용하여 [암호를 재설정](../virtual-machines/linux/reset-password.md)하세요. 복원 후에 암호를 다시 설정할 필요가 없도록 이러한 배포에서 SSH 키를 사용하는 것이 좋습니다.
- VM이 도메인 컨트롤러와의 관계로 인해 복원 된 후 VM에 액세스할 수 없는 경우 다음 단계에 따라 VM을 가져옵니다.
  - 복구 된 VM에 OS 디스크를 데이터 디스크로 연결 합니다.
  - 이 [링크](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/install-vm-agent-offline)를 따라 Azure 에이전트가 응답 하지 않는 경우 수동으로 VM 에이전트를 설치 합니다.
  - Vm에 대 한 직렬 콘솔 액세스를 사용 하도록 설정 하 여 VM에 대 한 명령줄 액세스 허용

  ```cmd
    bcdedit /store <drive letter>:\boot\bcd /enum
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<<BOOT LOADER IDENTIFIER>>} ON
    bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
    ```

  - VM을 다시 작성 하는 경우 Azure Portal 사용 하 여 로컬 관리자 계정 및 암호를 다시 설정 합니다.
  - 직렬 콘솔 access 및 CMD를 사용 하 여 도메인에서 VM 가입

    ```cmd
    cmd /c "netdom remove <<MachineName>> /domain:<<DomainName>> /userD:<<DomainAdminhere>> /passwordD:<<PasswordHere>> /reboot:10 /Force"
    ```

- VM이 가입 해제 되 고 다시 시작 되 면 로컬 관리자 자격 증명을 사용 하 여 VM에 성공적으로 RDP 하 고 VM을 다시 도메인에 다시 조인할 수 있습니다.

## <a name="backing-up-restored-vms"></a>복원된 VM 백업

- VM을 원래 백업한 VM과 동일한 이름의 리소스 그룹에 복원한 경우 복원 후에도 VM에서 계속 백업됩니다.
- VM을 다른 리소스 그룹에 복원했거나 복원된 VM에 다른 이름을 지정한 경우 복원된 VM에 대한 백업을 설정해야 합니다.

## <a name="next-steps"></a>다음 단계

- 복원 프로세스 중에 문제가 발생하는 경우 일반적인 문제 및 오류를 [검토](backup-azure-vms-troubleshoot.md#restore)합니다.
- VM이 복원되면 [가상 머신 관리](backup-azure-manage-vms.md)에 대해 알아봅니다.
