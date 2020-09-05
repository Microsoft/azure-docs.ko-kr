---
title: Azure Vm에서 SAP HANA 데이터베이스 복원
description: 이 문서에서는 Azure Virtual Machines에서 실행 되는 SAP HANA 데이터베이스를 복원 하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: 7cb521b4034ef225d3af6d397bb6cd83b28e1b8a
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89006315"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Azure Vm에서 SAP HANA 데이터베이스 복원

이 문서에서는 Azure Backup 서비스가 Recovery Services 자격 증명 모음에 백업 된 Azure VM (가상 컴퓨터)에서 실행 되는 SAP HANA 데이터베이스를 복원 하는 방법을 설명 합니다. 복원을 사용 하 여 개발/테스트 시나리오에 대 한 데이터의 복사본을 만들거나 이전 상태로 돌아갈 수 있습니다.

SAP HANA 데이터베이스를 백업 하는 방법에 대 한 자세한 내용은 [Azure vm에서 SAP HANA 데이터베이스 백업](./backup-azure-sap-hana-database.md)을 참조 하세요.

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>지정 시간 또는 복구 지점으로 복원

Azure Backup에서는 다음과 같이 Azure VM에서 실행되는 SAP HANA 데이터베이스를 복원할 수 있습니다.

* 로그 백업을 사용하여 특정 날짜 또는 시간(초)으로 복원합니다. Azure Backup은 선택한 시간을 기준으로 복원해야 하는 적절한 전체 차등 백업 및 로그 백업 체인을 자동으로 결정합니다.

* 특정 복구 지점으로 복원하려면 특정 전체 또는 차등 백업을 복원합니다.

## <a name="prerequisites"></a>필수 구성 요소

데이터베이스를 복원 하기 전에 다음 사항에 유의 하십시오.

* 동일한 지역에 있는 SAP HANA 인스턴스로만 데이터베이스를 복원할 수 있습니다.

* 대상 인스턴스를 원본과 동일한 자격 증명 모음에 등록해야 합니다.

* Azure Backup은 동일한 VM에 있는 두 개의 서로 다른 SAP HANA 인스턴스를 구분할 수 없습니다. 따라서 동일한 VM의 한 인스턴스에서 다른 인스턴스로 데이터를 복원 하는 것은 불가능 합니다.

* 대상 SAP HANA 인스턴스가 복원 준비가 되었는지 확인 하려면 해당 **백업 준비** 상태를 확인 합니다.

  1. 대상 SAP HANA 인스턴스가 등록 된 자격 증명 모음 열기

  1. 자격 증명 모음 대시보드의 **시작**에서 **백업** 을 선택 합니다.

      ![자격 증명 모음 대시보드에 백업](media/sap-hana-db-restore/getting-started-backup.png)

  1. **백업**에서 **백업할 항목** 아래에 있는 **Azure VM에서 SAP HANA** 선택 합니다.

      ![Azure VM의 SAP HANA 선택](media/sap-hana-db-restore/sap-hana-backup.png)

  1. **Vm에서 Db 검색**아래에서 **세부 정보 보기** 를 클릭 합니다.

      ![세부 정보 보기](media/sap-hana-db-restore/view-details.png)

  1. 대상 VM의 **백업 준비 상태** 검토

      ![보호되는 서버](media/sap-hana-db-restore/protected-servers.png)

* SAP HANA에서 지 원하는 복원 유형에 대 한 자세한 내용은 SAP HANA Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) 을 참조 하세요.

## <a name="restore-a-database"></a>데이터베이스 복원

복원 하려면 다음 권한이 필요 합니다.

* 복원을 수행 하는 자격 증명 모음의 **Backup Operator** 권한입니다.
* 백업되는 원본 VM에 대한 **참가자(쓰기)** 액세스 권한
* 대상 VM에 대 한 **참가자 (쓰기**) 액세스:
  * 동일한 VM으로 복원 하는 경우이 VM은 원본 VM입니다.
  * 대체 위치로 복원 하는 경우 새 대상 VM이 됩니다.

1. 복원할 SAP HANA 데이터베이스가 등록 된 자격 증명 모음을 엽니다.

1. 자격 증명 모음 대시보드의 **보호 된 항목**에서 **백업 항목** 을 선택 합니다.

    ![Backup 항목](media/sap-hana-db-restore/backup-items.png)

1. **백업 항목**의 **백업 관리 유형** 에서 **Azure VM의 SAP HANA** 를 선택 합니다.

    ![백업 관리 유형](media/sap-hana-db-restore/backup-management-type.png)

1. 복원할 데이터베이스를 선택 하십시오.

    ![복원할 데이터베이스](media/sap-hana-db-restore/database-to-restore.png)

1. 데이터베이스 메뉴를 검토합니다. 다음을 포함 하 여 데이터베이스 백업에 대 한 정보를 제공 합니다.

    * 가장 오래 되 고 최신 복원 지점이

    * 데이터베이스의 최근 24 시간 및 72 시간에 대 한 로그 백업 상태

    ![데이터베이스 메뉴](media/sap-hana-db-restore/database-menu.png)

1. **데이터베이스 복원** 선택

1. **복원 구성**에서 데이터를 복원할 위치 (또는 방법)를 지정 합니다.

    * **대체 위치**: 데이터베이스를 대체 위치로 복원 하 고 원래 원본 데이터베이스를 유지 합니다.

    * **데이터베이스 덮어쓰기**: 원래 원본과 동일한 SAP HANA 인스턴스로 데이터를 복원 합니다. 이 옵션은 원래 데이터베이스를 덮어씁니다.

      ![복원 구성](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>대체 위치에 복원

1. **복원 구성** 메뉴의 **복원 위치**에서 **대체 위치**를 선택 합니다.

    ![대체 위치에 복원](media/sap-hana-db-restore/restore-alternate-location.png)

1. 데이터베이스를 복원 하려는 SAP HANA 호스트 이름 및 인스턴스 이름을 선택 합니다.
1. **백업 준비 상태** 를 확인 하 여 대상 SAP HANA 인스턴스가 복원 준비가 되었는지 확인 합니다. 자세한 내용은 [필수 구성 요소 섹션](#prerequisites) 을 참조 하세요.
1. **복원된 DB 이름** 상자에 대상 데이터베이스의 이름을 입력합니다.

    > [!NOTE]
    > SDC (Single Database 컨테이너) 복원은 다음 [검사](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)를 수행 해야 합니다.

1. 해당 하는 경우 **선택한 HANA 인스턴스에 동일한 이름의 DB가 이미 있는 경우 덮어쓰기**를 선택 합니다.
1. **확인**을 선택합니다.

    ![복원 구성-최종 화면](media/sap-hana-db-restore/restore-configuration-last.png)

1. **복원 지점 선택**에서 **로그 (지정 시간)** 를 선택 하 여 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 또는 **전체 & 차등** 을 선택 하 여 [특정 복구 지점으로 복원](#restore-to-a-specific-recovery-point)합니다.

### <a name="restore-and-overwrite"></a>복원 및 덮어쓰기

1. **복원 구성** 메뉴의 **복원 위치**에서 **DB로 덮어쓰기**  >  **확인**을 선택 합니다.

    ![DB 덮어쓰기](media/sap-hana-db-restore/overwrite-db.png)

1. **복원 지점 선택**에서 **로그 (지정 시간)** 를 선택 하 여 [특정 시점으로 복원](#restore-to-a-specific-point-in-time)합니다. 또는 **전체 & 차등** 을 선택 하 여 [특정 복구 지점으로 복원](#restore-to-a-specific-recovery-point)합니다.

### <a name="restore-as-files"></a>파일로 복원

데이터베이스 대신 백업 데이터를 파일로 복원 하려면 **파일로 복원**을 선택 합니다. 파일이 지정된 경로에 덤프되면 이러한 파일을 데이터베이스로 복원하려는 SAP HANA 컴퓨터로 가져올 수 있습니다. 이러한 파일을 임의의 컴퓨터로 이동할 수 있으므로 이제 구독 및 지역 간에 데이터를 복원할 수 있습니다.

1. **복원 구성** 메뉴의 **위치 및 복원 방법**에서 **파일로 복원**을 선택 합니다.
1. 백업 파일을 복원 하려는 **호스트** /HANA 서버 이름을 선택 합니다.
1. **서버의 대상 경로**에서 2 단계에서 선택한 서버에 폴더 경로를 입력 합니다. 서비스에서 필요한 모든 백업 파일을 덤프 하는 위치입니다.

    덤프 되는 파일은 다음과 같습니다.

    * 데이터베이스 백업 파일
    * 카탈로그 파일
    * JSON 메타 데이터 파일 (관련 된 각 백업 파일에 해당)

    일반적으로 네트워크 공유 경로 또는 탑재된 Azure 파일 공유 경로가 대상 경로로 지정되면 이를 통해 동일한 네트워크의 다른 컴퓨터 또는 동일한 Azure 파일 공유가 탑재된 다른 컴퓨터에서 이러한 파일에 더 쉽게 액세스할 수 있습니다.

    >[!NOTE]
    >등록된 대상 VM에 탑재된 Azure 파일 공유에서 데이터베이스 백업 파일을 복원하려면 루트 계정에 Azure 파일 공유에 대한 읽기/쓰기 권한이 있는지 확인합니다.

    ![대상 경로 선택](media/sap-hana-db-restore/restore-as-files.png)

1. 모든 백업 파일 및 폴더를 복원 하는 데 사용할 **복원 지점을** 선택 합니다.

    ![복원 지점 선택](media/sap-hana-db-restore/select-restore-point.png)

1. 선택한 복원 지점과 연결 된 모든 백업 파일은 대상 경로로 덤프 됩니다.
1. 선택한 복원 지점 유형(**특정 시점** 또는 **전체 및 차등**)에 따라 대상 경로에 만들어진 하나 이상의 폴더를 볼 수 있습니다. `Data_<date and time of restore>`라는 폴더 중 하나에는 전체 및 차등 백업이 포함되고, `Log`라는 다른 폴더에는 로그 백업이 포함됩니다.
1. 복원된 이러한 파일을 데이터베이스로 복원하려는 SAP HANA 서버로 이동합니다.
1. 그런 다음, 다음 단계를 수행합니다.
    1. 다음 명령을 사용하여 백업 파일이 저장되는 폴더/디렉터리에 대한 사용 권한을 설정합니다.

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. 다음 명령 집합을 `<SID>adm`으로 실행합니다.

        ```bash
        su - <sid>adm
        ```

    1. 복원에 사용할 카탈로그 파일을 생성합니다. 전체 백업에 대한 JSON 메타데이터 파일에서 **BackupId**를 추출합니다. 이는 나중에 복원 작업에서 사용됩니다. 전체 및 로그 백업이 서로 다른 폴더에 있는지 확인하고, 이러한 폴더에서 카탈로그 파일과 JSON 메타데이터 파일을 삭제합니다.

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        위의 명령에서 다음을 수행합니다.

        * `<DataFileDir>` - 전체 백업이 포함된 폴더
        * `<LogFilesDir>` - 로그 백업이 포함된 폴더
        * `<PathToPlaceCatalogFile>` - 생성된 카탈로그 파일을 배치해야 하는 폴더

    1. HANA Studio를 통해 새로 생성된 카탈로그 파일을 사용하여 복원하거나 새로 생성된 이 카탈로그를 사용하여 HDBSQL 복원 쿼리를 실행합니다. HDBSQL 쿼리는 아래에 나와 있습니다.

    * 특정 시점으로 복원하려면 다음을 수행합니다.

        복원된 새 데이터베이스를 만드는 경우 HDBSQL 명령을 실행하여 새 `<DatabaseName>` 데이터베이스를 만든 다음, 복원에 사용할 데이터베이스를 중지합니다. 그러나 기존 데이터베이스만 복원하는 경우 HDBSQL 명령을 실행하여 데이터베이스를 중지합니다.

        그런 다음, 다음 명령을 실행하여 데이터베이스를 복원합니다.

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` - 복원하려는 새 데이터베이스 또는 기존 데이터베이스의 이름
        * `<Timestamp>` - 특정 시점 복원에 대한 정확한 타임스탬프
        * `<DatabaseName@HostName>` - 백업이 복원에 사용되는 데이터베이스의 이름 및 이 데이터베이스가 있는 **호스트**/SAP HANA 서버 이름. `USING SOURCE <DatabaseName@HostName>` 옵션은 데이터 백업(복원에 사용됨)이 대상 SAP HANA 컴퓨터와 다른 SID 또는 이름을 사용하는 데이터베이스임을 지정합니다. 따라서 백업이 수행 되는 동일한 HANA 서버에서 수행 되는 복원에 대해 지정할 필요가 없습니다.
        * `<PathToGeneratedCatalogInStep3>`- **C 단계** 에서 생성 된 카탈로그 파일의 경로
        * `<DataFileDir>` - 전체 백업이 포함된 폴더
        * `<LogFilesDir>` - 로그 백업이 포함된 폴더
        * `<BackupIdFromJsonFile>`- **C 단계** 에서 추출 된 **BackupId**

    * 특정 전체 또는 차등 백업으로 복원하려면 다음을 수행합니다.

        복원된 새 데이터베이스를 만드는 경우 HDBSQL 명령을 실행하여 새 `<DatabaseName>` 데이터베이스를 만든 다음, 복원에 사용할 데이터베이스를 중지합니다. 그러나 기존 데이터베이스만 복원하는 경우 HDBSQL 명령을 실행하여 데이터베이스를 중지합니다.

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` - 복원하려는 새 데이터베이스 또는 기존 데이터베이스의 이름
        * `<Timestamp>` - 특정 시점 복원에 대한 정확한 타임스탬프
        * `<DatabaseName@HostName>` - 백업이 복원에 사용되는 데이터베이스의 이름 및 이 데이터베이스가 있는 **호스트**/SAP HANA 서버 이름. `USING SOURCE <DatabaseName@HostName>` 옵션은 데이터 백업(복원에 사용됨)이 대상 SAP HANA 컴퓨터와 다른 SID 또는 이름을 사용하는 데이터베이스임을 지정합니다. 따라서 백업이 수행된 동일한 HANA 서버에서 수행되는 복원에 대해서는 이를 지정할 필요가 없습니다.
        * `<PathToGeneratedCatalogInStep3>`- **C 단계** 에서 생성 된 카탈로그 파일의 경로
        * `<DataFileDir>` - 전체 백업이 포함된 폴더
        * `<LogFilesDir>` - 로그 백업이 포함된 폴더
        * `<BackupIdFromJsonFile>`- **C 단계** 에서 추출 된 **BackupId**

### <a name="restore-to-a-specific-point-in-time"></a>특정 시점으로 복원

복원 유형으로 **로그(시점)** 을 선택한 경우 다음을 수행합니다.

1. 로그 그래프에서 복구 지점을 선택 하 고 **확인** 을 선택 하 여 복원 지점을 선택 합니다.

    ![복원 지점](media/sap-hana-db-restore/restore-point.png)

1. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.

    ![복원 선택](media/sap-hana-db-restore/restore-restore.png)

1. **알림** 영역에서 복원 진행률을 추적 하거나 데이터베이스 메뉴에서 **작업 복원** 을 선택 하 여 해당 진행률을 추적 합니다.

    ![복원이 트리거 되었습니다.](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>특정 복구 지점으로 복원

복원 유형으로 **전체 및 차등**을 선택한 경우 다음을 수행합니다.

1. 목록에서 복구 지점을 선택 하 고 **확인** 을 선택 하 여 복원 지점을 선택 합니다.

    ![특정 복구 지점 복원](media/sap-hana-db-restore/specific-recovery-point.png)

1. **복원** 메뉴에서 **복원**을 선택하여 복원 작업을 시작합니다.

    ![복원 작업 시작](media/sap-hana-db-restore/restore-specific.png)

1. **알림** 영역에서 복원 진행률을 추적 하거나 데이터베이스 메뉴에서 **작업 복원** 을 선택 하 여 해당 진행률을 추적 합니다.

    ![복원 진행률](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > 여러 데이터베이스 컨테이너 (MDC)에서 시스템 DB가 대상 인스턴스로 복원 된 후 복원 되는 경우 등록 전 스크립트를 다시 실행 해야 합니다. 그래야만 후속 테넌트 DB 복원 작업이 성공합니다. 자세한 내용은 [문제 해결 – MDC 복원](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

* Azure Backup를 사용 하 여 백업 되는 SAP HANA 데이터베이스를 관리 하 [는 방법을 알아봅니다](sap-hana-db-manage.md) .
