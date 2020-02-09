---
title: 암호화 된 Azure Vm 백업 및 복원
description: Azure Backup 서비스를 사용 하 여 암호화 된 Azure Vm을 백업 하 고 복원 하는 방법을 설명 합니다.
ms.topic: conceptual
ms.date: 04/03/2019
ms.openlocfilehash: 754a0899d25f2672c6a66eeafe5013f98b7a8f4d
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/22/2020
ms.locfileid: "76513782"
---
# <a name="back-up-and-restore-encrypted-azure-vm"></a>암호화 된 Azure VM 백업 및 복원

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용 하 여 암호화 된 디스크로 Windows 또는 Linux Azure vm (가상 머신)을 백업 하 고 복원 하는 방법을 설명 합니다.

시작 하기 전에 Azure Backup Azure Vm과 상호 작용 하는 방법에 대해 자세히 알아보려면 다음 리소스를 검토 합니다.

- Azure VM 백업 아키텍처를 [검토](backup-architecture.md#architecture-built-in-azure-vm-backup) 합니다.
- [자세한 정보](backup-azure-vms-introduction.md) Azure VM 백업 및 Azure Backup 확장.

## <a name="encryption-support"></a>암호화 지원

Azure Backup은 Azure Disk Encryption (ADE)로 암호화 된 OS/데이터 디스크가 있는 Azure Vm의 백업을 지원 합니다. ADE는 Windows Vm 암호화를 위해 BitLocker를 사용 하 고 Linux Vm의 경우 dm 기능을 사용 합니다. ADE는 Azure Key Vault와 통합 되어 디스크 암호화 키 및 비밀을 관리 합니다. KEKs (Key Vault 키 암호화 키)를 사용 하 여 추가 보안 계층을 추가 하 고 암호화 암호를 Key Vault에 쓸 수 있습니다.

다음 표에 요약 된 것 처럼 Azure AD 앱을 사용 하거나 사용 하지 않고 ADE를 사용 하 여 Azure Vm을 백업 하 고 복원할 수 Azure Backup.

**VM 디스크 유형** | **ADE (BEK/dm-자리)** | **ADE 및 KEK**
--- | --- | ---
**관리 되지 않는** | 예 | 예
**관리**  | 예 | 예

- [ADE](../security/azure-security-disk-encryption-overview.md), [Key Vault](../key-vault/key-vault-overview.md)및 [keks](https://blogs.msdn.microsoft.com/cclayton/2017/01/03/creating-a-key-encrypting-key-kek/)에 대해 자세히 알아보세요.
- Azure VM 디스크 암호화에 대한 [FAQ](../security/azure-security-disk-encryption-faq.md) 를 참조 하세요.

### <a name="limitations"></a>제한 사항

- 동일한 구독 및 지역 내에서 암호화 된 Vm을 백업 하 고 복원할 수 있습니다.
- Azure Backup는 독립 실행형 키를 사용 하 여 암호화 된 Vm을 지원 합니다. VM을 암호화 하는 데 사용 되는 인증서의 일부인 키는 현재 지원 되지 않습니다.
- Recovery Services 백업 자격 증명 모음과 동일한 구독 및 지역 내에서 암호화 된 Vm을 백업 하 고 복원할 수 있습니다.
- 암호화된 VM은 파일/폴더 수준에서 복구할 수 없습니다. 파일 및 폴더를 복원 하려면 전체 VM을 복구 해야 합니다.
- VM을 복원 하는 경우 암호화 된 Vm에 대해 [기존 vm 바꾸기](backup-azure-arm-restore-vms.md#restore-options) 옵션을 사용할 수 없습니다. 이 옵션은 암호화 되지 않은 managed disks에 대해서만 지원 됩니다.

## <a name="before-you-start"></a>시작하기 전에

시작하기 전에 다음을 수행합니다.

1. ADE를 사용 하는 [Windows](../security/azure-security-disk-encryption-windows.md) 또는 [Linux](../virtual-machines/linux/disk-encryption-overview.md) vm이 하나 이상 있는지 확인 합니다.
2. Azure VM 백업에 대한 [지원 매트릭스를 검토 합니다](backup-support-matrix-iaas.md) .
3. 백업 자격 증명 모음이 없는 경우 Recovery Services 백업 자격 증명 모음을 [만듭니다](backup-azure-arm-vms-prepare.md#create-a-vault) .
4. 이미 백업을 사용 하도록 설정 된 Vm에 대해 암호화를 사용 하도록 설정 하는 경우 백업을 중단 없이 계속 사용할 수 있도록 Key Vault에 액세스할 수 있는 권한이 있는 백업만 제공 하면 됩니다. 이러한 사용 권한을 할당 하는 방법에 [대해 자세히 알아보세요](#provide-permissions) .

또한 경우에 따라 몇 가지 작업을 수행 해야 할 수도 있습니다.

- Vm **에 vm 에이전트 설치**: Azure Backup는 컴퓨터에서 실행 되는 azure vm 에이전트에 확장을 설치 하 여 azure vm을 백업 합니다. Azure marketplace 이미지에서 VM을 만든 경우 에이전트가 설치 되 고 실행 됩니다. 사용자 지정 VM을 만들거나 온-프레미스 컴퓨터를 마이그레이션하는 경우 [에이전트를 수동으로 설치](backup-azure-arm-vms-prepare.md#install-the-vm-agent)해야 할 수 있습니다.

## <a name="configure-a-backup-policy"></a>백업 정책 구성

1. Recovery Services 백업 자격 증명 모음을 아직 만들지 않은 경우 [다음 지침](backup-azure-arm-vms-prepare.md#create-a-vault) 을 따르세요.
2. 포털에서 자격 증명 모음을 열고 **시작** 섹션에서 **백업** 을 선택 합니다.

    ![백업 블레이드](./media/backup-azure-vms-encryption/select-backup.png)

3. **워크 로드가 실행 되는 위치** > **백업 목표** 에서 **Azure**를 선택 합니다.
4. **백업할 항목** 에서 **가상 컴퓨터** > **확인**을 선택 합니다.

      ![시나리오 블레이드](./media/backup-azure-vms-encryption/select-backup-goal-one.png)

5. 백업 **정책** > **백업 정책을 선택**하 고 자격 증명 모음과 연결할 정책을 선택 합니다. 그런 후 **OK**를 클릭합니다.
    - 백업 정책은 백업이 수행 되는 시기와 저장 기간을 지정 합니다.
    - 드롭다운 메뉴 아래에 기본 정책의 상세 정보가 나열됩니다.

    ![시나리오 블레이드 열기](./media/backup-azure-vms-encryption/select-backup-goal-two.png)

6. 기본 정책을 사용 하지 않으려면 **새로 만들기**를 선택 하 고 [사용자 지정 정책을 만듭니다](backup-azure-arm-vms-prepare.md#create-a-custom-policy).

7. 선택 정책을 사용 하 여 백업 하려는 암호화 된 Vm을 선택 하 고 **확인**을 선택 합니다.

      ![암호화된 VM 선택](./media/backup-azure-vms-encryption/selected-encrypted-vms.png)

8. Azure Key Vault를 사용 하는 경우 자격 증명 모음 페이지에 Key Vault의 키와 암호에 대한 읽기 전용 액세스 권한이 필요 Azure Backup 메시지가 표시 됩니다.

    - 이 메시지가 표시 되 면 아무 조치도 필요 하지 않습니다.

        ![액세스 확인](./media/backup-azure-vms-encryption/access-ok.png)

    - 이 메시지가 표시 되 면 [아래 절차](#provide-permissions)에 설명 된 대로 사용 권한을 설정 해야 합니다.

        ![액세스 경고](./media/backup-azure-vms-encryption/access-warning.png)

9. 백업 **사용** 을 클릭 하 여 자격 증명 모음에 백업 정책을 배포 하 고 선택한 vm에 대한 백업을 사용 하도록 설정 합니다.

## <a name="trigger-a-backup-job"></a>백업 작업 트리거

초기 백업은 일정에 따라 실행 되지만 다음과 같이 즉시 실행할 수 있습니다.

1. 자격 증명 모음 메뉴에서 **백업 항목**을 클릭합니다.
2. **Backup 항목**에서 **Azure Virtual Machine**을 클릭 합니다.
3. **백업 항목** 목록에서 줄임표 (...)를 클릭 합니다.
4. **지금 백업**을 클릭합니다.
5. **지금 백업**에서 달력 컨트롤을 사용 하 여 복구 지점을 유지할 마지막 날을 선택 합니다. 그런 후 **OK**를 클릭합니다.
6. 포털 알림을 모니터링합니다. 자격 증명 모음 대시보드 > **백업 작업** > **진행 중**에서 작업 진행률을 모니터링할 수 있습니다. VM의 크기에 따라 초기 백업을 만드는 데 시간이 걸릴 수 있습니다.

## <a name="provide-permissions"></a>권한 제공

Azure VM에는 연결 된 Vm과 함께 키와 암호를 백업 하기 위한 읽기 전용 액세스 권한이 필요 합니다.

- Key Vault는 Azure 구독의 Azure AD 테넌트와 연결 되어 있습니다. **사용자가 멤버 사용자**인 경우 추가 작업 없이 Key Vault에 대한 액세스 권한을 획득 Azure Backup.
- **게스트 사용자**인 경우 키 자격 증명 모음에 액세스 하는 Azure Backup에 대한 권한을 제공 해야 합니다.

사용 권한을 설정 하려면:

1. Azure Portal에서 **모든 서비스**를 선택 하 고 **키 자격 증명 모음**을 검색 합니다.
2. 백업 중인 암호화 된 VM과 연결 된 key vault를 선택 합니다.
3. **액세스 정책** 을 선택 하 > **새 추가**를 선택 합니다.
4. **보안 주체 선택**을 선택 하 고 **백업 관리**를 입력 합니다.
5. **백업 관리 서비스** 를 선택 하 > **선택**합니다.

    ![백업 서비스 선택](./media/backup-azure-vms-encryption/select-backup-service.png)

6. **액세스 정책 추가** > **템플릿에서 구성 (선택 사항)** 에서 **Azure Backup**를 선택 합니다.
    - **키 권한** 및 **비밀 권한**에서 필요한 권한이 미리 입력됩니다.
    - VM이 **Bek만**사용 하 여 암호화 된 경우에는 비밀에 대한 권한만 필요 하므로 **키 사용 권한에** 대한 선택 항목을 제거 합니다.

    ![Azure Backup 선택](./media/backup-azure-vms-encryption/select-backup-template.png)

7. **확인**을 클릭합니다. **액세스 정책**에 **백업 관리 서비스가** 추가 됩니다.

    ![액세스 정책](./media/backup-azure-vms-encryption/backup-service-access-policy.png)

8. **저장** 을 클릭 하 여 사용 권한을 Azure Backup 제공 합니다.

## <a name="restore-an-encrypted-vm"></a>암호화된 VM 복원

다음과 같이 암호화 된 Vm을 복원 합니다.

1. [VM 디스크를 복원](backup-azure-arm-restore-vms.md#restore-disks)합니다.
2. 다음 중 하나를 수행 하 여 가상 머신 인스턴스를 다시 만듭니다.
    1. 복원 작업 중에 생성 된 템플릿을 사용 하 여 VM 설정을 사용자 지정 하 고 VM 배포를 트리거합니다. [자세히 알아보기](backup-azure-arm-restore-vms.md#use-templates-to-customize-a-restored-vm).
    2. PowerShell을 사용 하 여 복원 된 디스크에서 새 VM을 만듭니다. [자세히 알아보기](backup-azure-vms-automation.md#create-a-vm-from-restored-disks).
3. Linux Vm의 경우 데이터 디스크가 열리고 탑재 되도록 ADE 확장을 다시 설치 합니다.

## <a name="next-steps"></a>다음 단계

문제가 발생 하는 경우 다음 문서를 검토 하세요.

- 암호화 된 Azure Vm을 백업 하 고 복원할 때 발생 하는 [일반적인 오류](backup-azure-vms-troubleshoot.md) 입니다.
- [AZURE VM 에이전트/백업 확장](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md) 문제
