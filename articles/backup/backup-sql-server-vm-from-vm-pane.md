---
title: VM 창에서 SQL Server VM 백업
description: 이 문서에서는 VM 창에서 Azure virtual machines의 SQL Server 데이터베이스를 백업 하는 방법에 대해 알아봅니다.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 4f4ea202ee96e93a621c8dd0025c9ebc8b8d445d
ms.sourcegitcommit: c6b9a46404120ae44c9f3468df14403bcd6686c1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88891660"
---
# <a name="back-up-a-sql-server-from-the-vm-pane"></a>VM 창에서 SQL Server 백업

이 문서에서는 [Azure Backup](backup-overview.md) 서비스를 사용 하 여 Azure vm에서 실행 중인 SQL Server를 백업 하는 방법을 설명 합니다. 다음 두 가지 방법을 사용 하 여 SQL Server Vm을 백업할 수 있습니다.

- 단일 SQL Server Azure VM:이 문서의 지침은 VM 보기에서 직접 SQL Server VM를 백업 하는 방법을 설명 합니다.
- 여러 SQL Server Azure Vm: Recovery Services 자격 증명 모음을 설정 하 고 여러 Vm에 대 한 백업을 구성할 수 있습니다. 해당 시나리오에 대 한 [이 문서의](backup-sql-server-database-azure-vms.md) 지침을 따르세요.

## <a name="before-you-start"></a>시작하기 전에

1. [지원 매트릭스](sql-support-matrix.md)를 사용 하 여 환경을 확인 합니다.
2. SQL Server VM에 대 한 Azure Backup [개요](backup-azure-sql-database.md) 를 확인 하세요.
3. VM에 [네트워크 연결](backup-sql-server-database-azure-vms.md#establish-network-connectivity)이 있는지 확인합니다.

## <a name="configure-backup-on-the-sql-server"></a>SQL Server에서 백업 구성

VM의 **백업** 창에서 SQL Server VM에 대 한 백업을 사용 하도록 설정할 수 있습니다. 이 메서드는 다음 두 가지 작업을 수행 합니다.

- SQL VM을 Azure Backup 서비스에 등록 하 여 액세스 권한을 제공 합니다.
- VM 내에서 실행 중인 모든 SQL Server 인스턴스를 자동으로 보호 합니다. 이는 백업 정책이 모든 기존 데이터베이스 및 나중에 이러한 인스턴스에 추가 될 데이터베이스에 적용 됨을 의미 합니다.

1. 페이지 맨 위에 있는 배너를 선택 하 SQL Server 백업 보기를 엽니다.

    ![SQL Server 백업 보기](./media/backup-sql-server-vm-from-vm-pane/sql-server-backup-view.png)

    >[!NOTE]
    >배너가 표시 되지 않나요? 배너는 Azure Marketplace 이미지를 사용 하 여 만든 SQL Server Vm에 대해서만 표시 됩니다. Azure VM Backup을 사용 하 여 보호 되는 Vm에 대해서도 추가로 표시 됩니다. 다른 이미지의 경우 [여기](backup-sql-server-database-azure-vms.md)에 설명 된 대로 백업을 구성할 수 있습니다.

2. Recovery Services 자격 증명 모음 이름을 입력 합니다. 자격 증명 모음은 모든 백업을 저장 하 고 관리 하는 논리적 엔터티입니다. 새 자격 증명 모음을 만드는 경우:

    - 보호 하 고 있는 SQL Server VM와 동일한 구독 및 지역에 생성 됩니다.
    - 모든 백업에 대 한 GRS (지역 중복 저장소) 설정을 사용 하 여 생성 됩니다. 중복성 유형을 변경 하려는 경우 VM을 보호 하기 전에 수행 해야 합니다. 자세한 내용은 [이 문서](backup-create-rs-vault.md#set-storage-redundancy)를 참조하세요.

3. **백업 정책을**선택 합니다. 기본 정책 또는 자격 증명 모음에서 만든 다른 기존 정책에서 선택할 수 있습니다. 새 정책을 만들려면 [이 문서](backup-sql-server-database-azure-vms.md#create-a-backup-policy) 를 참조 하 여 단계별 가이드를 참조 하세요.

    ![백업 정책 선택](./media/backup-sql-server-vm-from-vm-pane/backup-policy.png)

4. **백업 사용**을 선택합니다. 작업을 완료 하는 데 몇 분 정도 걸릴 수 있습니다.

    ![백업 사용 선택](./media/backup-sql-server-vm-from-vm-pane/enable-backup.png)

5. 작업이 완료 되 면 배너에 **자격 증명 모음 이름이** 표시 됩니다.

    ![자격 증명 모음 이름이 배너에 표시 됩니다.](./media/backup-sql-server-vm-from-vm-pane/vault-name.png)

6. 배너를 선택 하 여 자격 증명 모음 보기로 이동 합니다. 여기에서 등록 된 모든 Vm과 해당 보호 상태를 볼 수 있습니다.

    ![등록 된 Vm이 있는 자격 증명 모음 보기](./media/backup-sql-server-vm-from-vm-pane/vault-view.png)

7. 비 marketplace 이미지의 경우 등록이 성공할 수 있지만 Azure Backup 확장에 SQL Server에 대 한 사용 권한이 부여 될 때까지 **백업 구성이** 트리거되지 않을 수 있습니다. 이러한 경우 **백업 준비** 열은 **준비 되지 않습니다**. Marketplace가 아닌 이미지에 대 한 [적절 한 사용 권한을 수동으로 할당](backup-azure-sql-database.md#set-vm-permissions) 해야 하므로 백업이 트리거될 수 있습니다.

    ![백업 준비가 완료 되지 않았습니다.](./media/backup-sql-server-vm-from-vm-pane/backup-readiness-not-ready.png)

8. 백업 된 SQL Server VM에서 수행 해야 하는 추가 작업 또는 모니터링을 위해 해당 Recovery Services 자격 증명 모음으로 이동 합니다. **백업 항목** 으로 이동 하 여이 자격 증명 모음에서 백업 된 모든 데이터베이스를 확인 하 고 요청 시 백업 및 복원과 같은 작업을 트리거합니다. 마찬가지로, **백업 작업** 으로 이동 하 여 보호, 백업, 복원 등의 작업에 해당 하는 작업을 [모니터링할](manage-monitor-sql-database-backup.md) 수 있습니다.

    ![백업 항목의 백업 데이터베이스를 참조 하세요.](./media/backup-sql-server-vm-from-vm-pane/backup-items.png)

>[!NOTE]
>백업은 보호 된 VM에 나중에 추가 될 수 있는 새 SQL Server 인스턴스에 자동으로 구성 되지 않습니다. 새로 추가 된 인스턴스에 백업을 구성 하려면 VM이 등록 된 자격 증명 모음으로 이동 하 여 [여기](backup-sql-server-database-azure-vms.md)에 나열 된 단계를 수행 해야 합니다.

## <a name="next-steps"></a>다음 단계

방법 배우기:

- [백업된 SQL Server 데이터베이스 복원](restore-sql-database-azure-vm.md)
- [백업된 SQL Server 데이터베이스 관리](manage-monitor-sql-database-backup.md)
