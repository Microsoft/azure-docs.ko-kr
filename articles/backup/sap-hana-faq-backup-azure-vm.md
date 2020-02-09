---
title: FAQ-Azure Vm에서 SAP HANA 데이터베이스 백업
description: 이 문서에서는 Azure Backup 서비스를 사용 하 여 SAP HANA 데이터베이스 백업에 대한 일반적인 질문에 대한 대답을 검색 합니다.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: e5684024668103ccbe13be4af3d7a9336651df77
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287841"
---
# <a name="frequently-asked-questions--back-up-sap-hana-databases-on-azure-vms"></a>질문과 대답-Azure Vm에서 SAP HANA 데이터베이스 백업

이 문서에서는 Azure Backup 서비스를 사용 하 여 SAP HANA 데이터베이스 백업에 대한 일반적인 질문에 답변 합니다.

## <a name="backup"></a>Backup

### <a name="how-many-full-backups-are-supported-per-day"></a>하루에 지원 되는 전체 백업은 몇 개입니까?

하루에 하나의 전체 백업만 지원 합니다. 차등 백업 및 전체 백업은 같은 날짜에 트리거될 수 없습니다.

### <a name="do-successful-backup-jobs-create-alerts"></a>성공한 백업 작업에 경고가 만들어지나요?

아니요. 성공한 백업 작업에서는 경고가 생성되지 않습니다. 경고는 실패한 백업 작업에 대해서만 전송됩니다. 포털 경고에 대한 자세한 동작은 [여기](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-built-in-monitor)에 설명 되어 있습니다. 그러나 성공한 작업에 대한 경고를 포함 하는 경우에는 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)를 사용할 수 있습니다.

### <a name="can-i-see-scheduled-backup-jobs-in-the-backup-jobs-menu"></a>백업 작업 메뉴에서 예약 된 백업 작업을 볼 수 있나요?

백업 작업 메뉴에는 임시 백업 작업만 표시 됩니다. 예약 된 작업의 경우 [Azure Monitor](https://docs.microsoft.com/azure/backup/backup-azure-monitoring-use-azuremonitor)를 사용 합니다.

### <a name="are-future-databases-automatically-added-for-backup"></a>이후 데이터베이스가 백업을 위해 자동으로 추가되나요?

아니요. 현재는 지원 되지 않습니다.

### <a name="if-i-delete-a-database-from-an-instance-what-will-happen-to-the-backups"></a>인스턴스에서 데이터베이스를 삭제 하는 경우 백업에 어떤 일이 발생 하나요?

데이터베이스를 SAP HANA 인스턴스에서 삭제 한 경우에는 데이터베이스 백업을 계속 시도 합니다. 이는 삭제 된 데이터베이스가 **백업 항목** 에서 비정상 상태로 표시 되기 시작 하 고 계속 보호 됨을 의미 합니다.
이 데이터베이스 보호를 중지 하는 올바른 방법은이 데이터베이스에서 **데이터를 삭제 하는 백업 중지** 를 수행 하는 것입니다.

### <a name="if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-the-behavior-be"></a>보호 된 데이터베이스의 이름을 변경 하는 경우 동작은 무엇 인가요?

이름이 바뀐 데이터베이스는 새 데이터베이스로 처리 됩니다. 따라서 서비스는 데이터베이스를 찾을 수 없고 백업에 실패 한 것 처럼이 상황을 처리 합니다. 이름이 바뀐 데이터베이스는 새 데이터베이스로 표시 되 고 보호를 위해 구성 되어야 합니다.

### <a name="what-are-the-prerequisites-to-back-up-sap-hana-databases-on-an-azure-vm"></a>Azure VM에서 SAP HANA 데이터베이스를 백업 하기 위한 필수 구성 요소는 무엇 인가요?

[필수 구성 요소](tutorial-backup-sap-hana-db.md#prerequisites) 및 [권한 설정](tutorial-backup-sap-hana-db.md#setting-up-permissions) 섹션을 참조 하세요.

### <a name="what-permissions-should-be-set-for-azure-to-be-able-to-back-up-sap-hana-databases"></a>SAP HANA 데이터베이스를 백업할 수 있도록 Azure에 대해 설정 해야 하는 권한은 무엇 인가요?

사전 등록 스크립트를 실행 하면 Azure에서 SAP HANA 데이터베이스를 백업 하도록 허용 하는 데 필요한 권한을 설정 합니다. 권한에 대한 자세한 내용은 [여기](tutorial-backup-sap-hana-db.md#setting-up-permissions)에서 찾을 수 있습니다.

### <a name="will-backups-work-after-migrating-sap-hana-from-10-to-20"></a>SAP HANA를 1.0에서 2.0로 마이그레이션한 후 백업이 작동 하나요?

문제 해결 가이드의 [이 섹션](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) 을 참조 하세요.

## <a name="restore"></a>복원

### <a name="why-cant-i-see-the-hana-system-i-want-my-database-to-be-restored-to"></a>내 데이터베이스를 복원 하려는 HANA 시스템이 표시 되지 않는 이유는 무엇 인가요?

대상 SAP HANA 인스턴스에 복원 하기 위한 모든 필수 구성 요소가 충족 되는지 확인 합니다. 자세한 내용은 [필수 구성 요소-AZURE VM에서 데이터베이스 SAP HANA 복원](https://docs.microsoft.com/azure/backup/sap-hana-db-restore#prerequisites)을 참조 하세요.

### <a name="why-is-the-overwrite-db-restore-failing-for-my-database"></a>데이터베이스에 대한 덮어쓰기 DB 복원이 실패 하는 이유는 무엇 인가요?

복원 하는 동안 **강제 덮어쓰기** 옵션을 선택 했는지 확인 합니다.

### <a name="why-do-i-see-the-source-and-target-systems-for-restore-are-incompatible-error"></a>"복원을 위한 원본 및 대상 시스템이 호환 되지 않습니다." 오류가 표시 되는 이유는 무엇 인가요?

현재 지원 되는 복원 유형은 SAP HANA 참고 [1642148](https://launchpad.support.sap.com/#/notes/1642148) 을 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Vm에서 실행 되는 [SAP HANA 데이터베이스를 백업](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database) 하는 방법에 대해 알아봅니다.
