---
title: Azure Vm에서 SAP HANA 데이터베이스 복원
description: 이 문서에서는 Azure Virtual Machines에서 실행 되는 SAP HANA 데이터베이스를 복원 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 999edba61177758ad9039e81e789efcef99ca1de
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74287919"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Azure Vm에서 SAP HANA 데이터베이스 복원

이 문서에서는 Azure Backup 서비스가 Azure Backup Recovery Services 자격 증명 모음에 백업 된 Azure VM (가상 컴퓨터)에서 실행 되는 SAP HANA 데이터베이스를 복원 하는 방법을 설명 합니다. 복원을 사용 하 여 개발/테스트 시나리오에 대한 데이터의 복사본을 만들거나 이전 상태로 돌아갈 수 있습니다.

SAP HANA 데이터베이스를 백업 하는 방법에 대한 자세한 내용은 [Azure vm에서 SAP HANA 데이터베이스 백업](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)을 참조 하세요.

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>지정 시간 또는 복구 지점으로 복원

다음과 같이 Azure Vm에서 실행 되는 SAP HANA 데이터베이스를 복원할 수 Azure Backup.

* 로그 백업을 사용 하 여 특정 날짜 또는 시간 (초)으로 복원 합니다. Azure Backup은 선택 된 시간에 따라 복원 하는 데 필요한 적절 한 전체, 차등 백업 및 로그 백업 체인을 자동으로 결정 합니다.

* 특정 복구 지점으로 복원 하려면 특정 전체 또는 차등 백업으로 복원 합니다.

## <a name="prerequisites"></a>선행 조건

데이터베이스를 복원 하기 전에 다음 사항에 유의 하십시오.

* 동일한 지역에 있는 SAP HANA 인스턴스로만 데이터베이스를 복원할 수 있습니다.

* 대상 인스턴스를 원본과 동일한 자격 증명 모음에 등록 해야 합니다.

* Azure Backup는 동일한 VM에서 두 개의 다른 SAP HANA 인스턴스를 식별할 수 없습니다. 따라서 동일한 VM의 한 인스턴스에서 다른 인스턴스로 데이터를 복원 하는 것은 불가능 합니다.

* 대상 SAP HANA 인스턴스가 복원 준비가 되었는지 확인 하려면 해당 **백업 준비** 상태를 확인 합니다.

  * 대상 SAP HANA 인스턴스가 등록 된 자격 증명 모음 열기

  * 자격 증명 모음 대시보드의 **시작**에서 **백업** 을 선택 합니다.

![자격 증명 모음 대시보드에 백업](media/sap-hana-db-restore/getting-started-backup.png)

* **백업**에서 **백업할 항목** 아래에 있는 **Azure VM에서 SAP HANA** 선택 합니다.

![Azure VM에서 SAP HANA 선택](media/sap-hana-db-restore/sap-hana-backup.png)

* **Vm에서 Db 검색** 아래에서 **세부 정보 보기** 를 클릭 합니다.

![세부 정보 보기](media/sap-hana-db-restore/view-details.png)

* 대상 VM의 **백업 준비 상태** 검토

![보호되는 서버](media/sap-hana-db-restore/protected-servers.png)

* SAP HANA에서 지 원하는 복원 유형에 대한 자세한 내용은 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) 을 참조 하세요.

## <a name="restore-a-database"></a>데이터베이스 복원

* 복원할 SAP HANA 데이터베이스가 등록 된 자격 증명 모음을 엽니다.

* 자격 증명 모음 대시보드의 **보호 된 항목**에서 **백업 항목** 을 선택 합니다.

![Backup 항목](media/sap-hana-db-restore/backup-items.png)

* **백업 항목**의 **백업 관리 유형** 에서 **Azure VM의 SAP HANA** 를 선택 합니다.

![백업 관리 유형](media/sap-hana-db-restore/backup-management-type.png)

* 복원할 데이터베이스를 선택 하십시오.

 ![복원할 데이터베이스](media/sap-hana-db-restore/database-to-restore.png)

* 데이터베이스 메뉴를 검토합니다. 다음을 포함 하 여 데이터베이스 백업에 대한 정보를 제공 합니다.

  * 가장 오래 되 고 최신 복원 지점이

  * 데이터베이스의 최근 24 시간 및 72 시간에 대한 로그 백업 상태

![데이터베이스 메뉴](media/sap-hana-db-restore/database-menu.png)

* **데이터베이스 복원** 선택

* **복원 구성**에서 데이터를 복원할 위치 (또는 방법)를 지정 합니다.

  * **대체 위치**: 데이터베이스를 대체 위치로 복원 하 고 원래 원본 데이터베이스를 유지 합니다.

  * **데이터베이스 덮어쓰기**: 원래 원본과 동일한 SAP HANA 인스턴스로 데이터를 복원 합니다. 이 옵션은 원본 데이터베이스를 덮어씁니다.

![복원 구성](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>대체 위치로 복원

* **복원 구성** 메뉴의 **복원 위치**에서 **대체 위치**를 선택 합니다.

![대체 위치로 복원](media/sap-hana-db-restore/restore-alternate-location.png)

* 데이터베이스를 복원 하려는 SAP HANA 호스트 이름 및 인스턴스 이름을 선택 합니다.
* **백업 준비 상태** 를 확인 하 여 대상 SAP HANA 인스턴스가 복원 준비가 되었는지 확인 합니다. 자세한 내용은 [필수 구성 요소 섹션](#prerequisites) 을 참조 하세요.
* **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.

> [!NOTE]
> SDC (Single Database 컨테이너) 복원은 다음 [검사](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)를 수행 해야 합니다.

* 해당 하는 경우 **선택한 HANA 인스턴스에 동일한 이름의 DB가 이미 있는 경우 덮어쓰기**를 선택 합니다.
* **확인**을 선택합니다.

![복원 구성-최종 화면](media/sap-hana-db-restore/restore-configuration-last.png)

* **복원 지점 선택**에서 **로그 (지정 시간)** 를 선택 하 여 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 또는 **전체 & 차등** 을 선택 하 여 [특정 복구 지점으로 복원](#restore-to-a-specific-recovery-point)합니다.

### <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

* **복원 구성** 메뉴에서 **복원할 위치**아래에 있는 **데이터베이스 덮어쓰기** > **확인**을 선택 합니다.

![DB 덮어쓰기](media/sap-hana-db-restore/overwrite-db.png)

* **복원 지점 선택**에서 **로그 (지정 시간)** 를 선택 하 여 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 또는 **전체 & 차등** 을 선택 하 여 [특정 복구 지점으로 복원](#restore-to-a-specific-recovery-point)합니다.

### <a name="restore-to-a-specific-point-in-time"></a>특정 시점으로 복원

복원 유형으로 **로그(시점)** 을 선택한 경우 다음을 수행합니다.

* 로그 그래프에서 복구 지점을 선택 하 고 **확인** 을 선택 하 여 복원 지점을 선택 합니다.

![복원 지점](media/sap-hana-db-restore/restore-point.png)

* **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.

![복원 선택](media/sap-hana-db-restore/restore-restore.png)

* **알림** 영역에서 복원 진행률을 추적 하거나 데이터베이스 메뉴에서 **작업 복원** 을 선택 하 여 해당 진행률을 추적 합니다.

![복원이 트리거 되었습니다.](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>특정 복구 지점으로 복원

복원 유형으로 **전체 및 차등**을 선택한 경우 다음을 수행합니다.

* 목록에서 복구 지점을 선택 하 고 **확인** 을 선택 하 여 복원 지점을 선택 합니다.

![특정 복구 지점 복원](media/sap-hana-db-restore/specific-recovery-point.png)

* **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.

![복원 작업 시작](media/sap-hana-db-restore/restore-specific.png)

* **알림** 영역에서 복원 진행률을 추적 하거나 데이터베이스 메뉴에서 **작업 복원** 을 선택 하 여 해당 진행률을 추적 합니다.

![복원 진행률](media/sap-hana-db-restore/restore-progress.png)

> [!NOTE]
> 여러 데이터베이스 컨테이너 (MDC)에서 시스템 DB가 대상 인스턴스로 복원 된 후 복원 되는 경우 등록 전 스크립트를 다시 실행 해야 합니다. 이후 테넌트 DB 복원 작업이 성공 합니다. 자세한 내용은 [문제 해결 – MDC 복원](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* Azure Backup를 사용 하 여 백업 되는 SAP HANA 데이터베이스를 관리 하 [는 방법을 알아봅니다](sap-hana-db-manage.md) .
