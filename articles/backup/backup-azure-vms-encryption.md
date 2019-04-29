---
title: 백업 및 Azure Backup을 사용 하 여 암호화 된 Azure Vm 복원
description: 백업 및 Azure Backup 서비스를 사용 하 여 암호화 된 Azure Vm을 복원 하는 방법을 설명 합니다.
services: backup
author: geetha
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 4/3/2019
ms.author: geetha
ms.openlocfilehash: 893a22fb9f325625707869c8f6571d572b8f6b33
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61217042"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>백업 및 복원에 Azure VM 암호화

이 문서에서는 사용 하 여 암호화 된 디스크를 사용 하 여 백업 하 고 Windows 또는 Linux Azure virtual machines (Vm)를 복원 하는 방법을 설명 합니다 [Azure Backup](backup-overview.md) 서비스입니다.

시작 하기 전에 Azure 백업 Azure Vm과 함께 작용 하는 방법에 대 한 자세한 정보를 계속 확인 하려는 경우 이러한 리소스를 검토 합니다.

- [검토](backup-architecture.md#architecture-direct-backup-of-azure-vms) Azure VM 백업 아키텍처입니다.
- [에 대 한 자세한](backup-azure-vms-introduction.md) Azure VM 백업 및 Azure Backup 확장 합니다.

## <a name="encryption-support"></a>암호화 지원

Azure Backup에는 해당 OS/데이터 디스크가 있는 암호화 된 Azure 디스크 암호화 (ADE)를 사용 하 여 Azure Vm의 백업을 지원 합니다. ADE는 Linux Vm에 대 한 Windows Vm의 경우 dm-crypt 기능을 암호화에 대 한 BitLocker를 사용합니다. ADE는 디스크 암호화 키와 비밀을 관리 하려면 Azure Key Vault와 통합 됩니다. 키 자격 증명 모음 키 암호화 키 (Kek) Key Vault에 쓰기 전에 암호화 비밀 암호화, 보안 강화를 추가 하려면 사용할 수 있습니다.

Azure Backup를 백업 하 고 다음 표에 요약 된 것 처럼와 Azure AD 앱에 없는 ADE를 사용 하 여 Azure Vm을 복원할 수 있습니다.

**VM 디스크 유형** | **ADE (BEK/dm 암호화)** | **ADE 및 KEK**
--- | --- | ---
**관리 되지 않는** | 예 | 예
**관리 되는**  | 예 | 예

- 에 대해 자세히 알아보세요 [ADE](../security/azure-security-disk-encryption-overview.md)를 [Key Vault](../key-vault/key-vault-overview.md), 및 [Kek](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/)합니다.
- 읽기를 [FAQ](../security/azure-security-disk-encryption-faq.md) Azure VM 디스크 암호화에 대 한 합니다.



### <a name="limitations"></a>제한 사항

- 백업 하 고 동일한 구독 및 지역 내에서 암호화 된 Vm을 복원할 수 있습니다.
- Azure Backup 독립 실행형 키를 사용 하 여 암호화 된 Vm을 지원 합니다. VM 암호화를 사용 하는 인증서의 일부인 모든 키 현재 지원 되지 않습니다.
- 백업 하 고 동일한 구독 및 Recovery Services 백업 자격 증명 모음 지역 내에서 암호화 된 Vm을 복원할 수 있습니다.
- 암호화된 VM은 파일/폴더 수준에서 복구할 수 없습니다. 파일 및 폴더를 복원 하려면 전체 VM을 복구 해야 합니다.
- VM을 복원 하는 경우 사용할 수 없습니다는 [기존 VM을 대체](backup-azure-arm-restore-vms.md#restore-options) 암호화 된 Vm에 대 한 옵션입니다. 이 옵션은 암호화 되지 않은 관리 디스크에 대해만 지원 됩니다.




## <a name="before-you-start"></a>시작하기 전에

시작하기 전에 다음을 수행합니다.

1. 하나 이상의 했는지 확인 [Windows](../security/azure-security-disk-encryption-windows.md) 하거나 [Linux](../security/azure-security-disk-encryption-linux.md) ADE 사용 하 여 Vm에 사용 하도록 설정 합니다.
2. [지원 매트릭스 검토.](backup-support-matrix-iaas.md) Azure VM 백업
3. [만들](backup-azure-arm-vms-prepare.md#create-a-vault) Recovery Services 백업 자격 증명 모음을 계정이 없는 경우.
4. Vm 백업이 이미 설정 된에 대 한 암호화를 사용 하도록 설정 하면 백업을 중단 없이 계속할 수 있도록 Key Vault에 액세스할 수 있는 권한이 있는 백업 제공 해야 하는 하기만 하면 됩니다. [자세한](#provide-permissions) 이러한 사용 권한을 할당 하는 방법에 대 한 합니다.

또한 일부 상황에서 작업을 수행 해야 할 수 있는 작업의 두 가지가 있습니다.

- **VM에 VM 에이전트 설치**: Azure Backup은 컴퓨터에서 실행 중인 Azure VM 에이전트에 확장을 설치하여 Azure VM을 백업합니다. VM을 만든 경우 Azure marketplace 이미지에서 에이전트를 설치 하 고 실행. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션한 경우 해야 [에이전트를 수동으로 설치](backup-azure-arm-vms-prepare.md#install-the-vm-agent)합니다.
- **아웃 바운드 액세스를 명시적으로 허용**: 일반적으로 명시적으로 Azure Backup을 사용 하 여 통신을 위해에서 Azure VM에 대 한 아웃 바운드 네트워크 액세스를 허용 하도록 필요가 없습니다. 그러나 일부 Vm 발생할 연결 문제를 보여 주는 합니다 **ExtensionSnapshotFailedNoNetwork** 연결 하려고 하는 동안 오류가 발생 했습니다. 이 경우 수행 해야 합니다 [아웃 바운드 액세스를 명시적으로 허용](backup-azure-arm-vms-prepare.md#explicitly-allow-outbound-access), 백업 트래픽에 대해 Azure 공용 IP 주소를 사용 하 여 Azure Backup 확장 통신할 수 있도록 합니다.



## <a name="configure-a-backup-policy"></a>백업 정책 구성

1. Recovery Services 백업 자격 증명을 아직 만들지 않은 경우에 따라 [이러한 지침](backup-azure-arm-vms-prepare.md#create-a-vault)
2. 포털에서 자격 증명 모음을 열고 선택 **Backup** 에 **Getting Started** 섹션입니다.

    ![백업 블레이드 ](./media/backup-azure-vms-encryption/select-backup.png)

3. **Backup 목표** > **작업이 실행 되는 위치?** 선택 **Azure**합니다.
4. **를 백업 하 시겠습니까?** 선택 **가상 머신** > **확인**합니다.

      ![시나리오 블레이드](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. **Backup 정책** > **백업 정책 선택**, 자격 증명 모음을 사용 하 여 연결 하려는 정책을 선택 합니다. 그런 후 **OK**를 클릭합니다.
    - 백업 정책을 경우 백업이 수행 되 고 저장 되는 기간을 지정 합니다.
    - 드롭다운 메뉴 아래에 기본 정책의 상세 정보가 나열됩니다.

    ![시나리오 블레이드 열기](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. 기본 정책을 사용 하지 않으려는 경우 선택 **새로 만들기**, 및 [사용자 지정 정책을 만드는](backup-azure-arm-vms-prepare.md#create-a-custom-policy)합니다.


7. 선택 정책을 사용 하 여 백업 하려는 암호화 된 Vm을 선택 하 고 선택 **확인**합니다.

      ![암호화된 VM 선택](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Azure Key Vault에 자격 증명 모음 페이지를 사용 하는 경우 Azure Backup 키 및 Key Vault에서 비밀에 대 한 읽기 전용 액세스를 해야 한다는 메시지가 표시 됩니다.

    - 이 메시지가 나타나면 조치가 필요 하지 않습니다.

        ![액세스 확인](./media/backup-azure-vms-encryption/access-ok.png)

    - 에 설명 된 대로 권한을 설정 해야 하는 경우이 메시지가 나타나면 합니다 [아래의 절차](#provide-permissions)합니다.

        ![액세스 경고](./media/backup-azure-vms-encryption/access-warning.png)

9. 클릭 **백업 사용** 자격 증명 모음에서 백업 정책을 배포 하 여 선택한 Vm에 대 한 백업을 사용 하도록 설정 합니다.


## <a name="trigger-a-backup-job"></a>백업 작업 트리거

초기 백업은 일정에 따라 실행 됩니다 있지만 즉시 같이 바로 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **백업 항목**에서 **Azure Virtual Machine**을 클릭합니다.
3. 에 **Backup 항목** 목록에서 줄임표 (...)를 클릭 합니다.
4. **지금 백업**을 클릭합니다.
5. **지금 Backup**, 달력 컨트롤을 사용 하 여 복구 지점을 유지 되어야 하는 마지막 날을 선택 합니다. 그런 후 **OK**를 클릭합니다.
6. 포털 알림을 모니터링합니다. 자격 증명 모음 대시보드 > **백업 작업** > **진행 중**에서 작업 진행률을 모니터링할 수 있습니다. VM의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.


## <a name="provide-permissions"></a>사용 권한 제공

Azure VM에는 키 및 비밀을 함께 연결된 된 Vm 백업에 대 한 읽기 전용으로 액세스를 해야 합니다.

- Key Vault는 Azure 구독의 Azure AD 테 넌 트와 연결 됩니다. 경우는 **멤버 사용자**, Azure Backup 추가 작업 없이 Key Vault에 대 한 액세스를 획득 합니다.
- 경우는 **게스트 사용자**, key vault에 액세스 하려면 Azure Backup에 대 한 권한을 제공 해야 합니다.

사용 권한 설정:

1. Azure portal에서 선택 **모든 서비스**를 찾아 **Key vault**합니다.
2. 백업 하는 암호화 된 VM과 연결 된 key vault를 선택 합니다.
3. 선택 **액세스 정책** > **새로 추가**합니다.
4. 선택 **보안 주체 선택**를 차례로 **백업 관리**합니다.
5. 선택 **Management 서비스 백업** > **선택**합니다.

    ![백업 서비스 선택](./media/backup-azure-vms-encryption/select-backup-service.png)

6. **액세스 정책 추가** > **구성 (선택 사항) 템플릿에서**선택 **Azure Backup**합니다.
    - **키 권한** 및 **비밀 권한**에서 필요한 권한이 미리 입력됩니다.
    - 사용 하 여 VM을 암호화 **BEK만 해당**에 대 한 선택 항목을 제거 **키 권한** 만 비밀에 대 한 권한이 필요 하므로 합니다.

    ![Azure Backup 선택](./media/backup-azure-vms-encryption/select-backup-template.png)

6. **확인**을 클릭합니다. **백업 관리 서비스** 추가할 **액세스 정책**합니다.

    ![액세스 정책](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

7. 클릭 **저장할** 권한으로 Azure Backup을 제공 합니다.

## <a name="restore-an-encrypted-vm"></a>암호화된 VM 복원

암호화 된 Vm을 다음과 같이 복원:

1. [VM 디스크 복원](backup-azure-arm-restore-vms.md#restore-disks)합니다.
2. 다음 중 하나를 수행 합니다.
    - VM 설정을 사용자 지정 하 여 VM 배포를 트리거할 복원 작업 중에 생성 되는 템플릿을 사용 합니다. [자세히 알아보기](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    - Powershell을 사용 하 여 복원된 된 디스크에서 새 VM을 만듭니다. [자세히 알아보기](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="next-steps"></a>다음 단계

모든 문제를 실행 하는 경우 검토

- [일반적인 오류](backup-azure-vms-troubleshoot.md) 경우 백업 및 복원 하는 Azure Vm 암호화 합니다.
- [Azure VM 에이전트/백업 확장](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) 문제입니다.
