---
title: SAP HANA 데이터베이스 백업 오류 문제 해결
description: Azure Backup를 사용하여 SAP HANA 데이터베이스를 백업하는 경우 발생할 수 있는 일반적인 오류를 해결하는 방법을 설명합니다.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 4958a5e93e27c34772c7c3285470abbc31f5b089
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89004173"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure의 SAP HANA 데이터베이스 백업 문제 해결

이 문서에서는 Azure Virtual Machines의 SAP HANA 데이터베이스 백업에 대한 문제 해결 정보를 제공합니다. 현재 지원되는 SAP HANA 백업 시나리오에 대한 자세한 내용은 [시나리오 지원](sap-hana-backup-support-matrix.md#scenario-support)을 참조하세요.

## <a name="prerequisites-and-permissions"></a>필수 조건 및 권한

백업을 구성하기 전에 [필수 조건](tutorial-backup-sap-hana-db.md#prerequisites) 및 [사전 등록 스크립트의 기능](tutorial-backup-sap-hana-db.md#what-the-pre-registration-script-does) 섹션을 참조하세요.

## <a name="common-user-errors"></a>일반 사용자 오류

### <a name="usererrorhanainternalrolenotpresent"></a>UserErrorHANAInternalRoleNotPresent

| **오류 메시지**      | <span style="font-weight:normal">Azure Backup에는 백업을 수행 하는 데 필요한 역할 권한이 없습니다.</span>    |
| ---------------------- | ------------------------------------------------------------ |
| **가능한 원인**    | 역할을 덮어쓴 것일 수 있습니다.                          |
| **권장 작업** | 이 문제를 해결하려면 **DB 검색** 창에서 스크립트를 실행하거나 [여기](https://aka.ms/scriptforpermsonhana)에서 다운로드합니다. 또는 워크로드 백업 사용자(AZUREWLBACKUPHANAUSER)에 'SAP_INTERNAL_HANA_SUPPORT' 역할을 추가합니다. |

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| 오류 메시지      | <span style="font-weight:normal">HANA 시스템에 연결하지 못했습니다.</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | SAP HANA 인스턴스가 작동 중단된 것일 수 있습니다.<br/>HANA 데이터베이스와 상호 작용 하는 Azure Backup에 필요한 권한이 설정 되지 않았습니다. |
| **권장 작업** | SAP HANA 데이터베이스가 작동 중인지 확인합니다. 데이터베이스가 실행 중인 경우 필요한 모든 권한이 설정되어 있는지 확인합니다. 사용 권한이 없는 경우 [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 실행하여 누락된 사용 권한을 추가합니다. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| 오류 메시지      | <span style="font-weight:normal">지정된 SAP HANA 인스턴스가 잘못되었거나 없습니다.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | 단일 Azure VM의 여러 SAP HANA 인스턴스는 백업할 수 없습니다. |
| **권장 작업** | 백업하려는 SAP HANA 인스턴스에서 [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 실행합니다. 문제가 여전히 지속되면 Microsoft 지원 서비스에 문의하세요. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| 오류 메시지      | <span style="font-weight:normal">지정한 SAP HANA 작업이 지원되지 않습니다.</span>              |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | SAP HANA Azure Backup는 SAP HANA native client에서 수행 되는 증분 백업 및 작업을 지원 하지 않습니다 (스튜디오/환경/DBA 환경). |
| **권장 작업** | 자세한 내용은 [여기](./sap-hana-backup-support-matrix.md#scenario-support)를 참조하세요. |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| 오류 메시지      | <span style="font-weight:normal">이 SAP HANA 데이터베이스는 요청된 백업 유형을 지원하지 않습니다.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | Azure Backup는 스냅숏을 사용한 증분 백업 및 백업을 지원 하지 않습니다. |
| **권장 작업** | 자세한 내용은 [여기](./sap-hana-backup-support-matrix.md#scenario-support)를 참조하세요. |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| 오류 메시지      | <span style="font-weight:normal">Backup 로그 체인이 손상되었습니다.</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | 로그 백업 대상이 backint에서 파일 시스템으로 업데이트되었거나 backint 실행 파일이 변경되었을 수 있습니다. |
| **권장 작업** | 문제를 해결하려면 전체 백업을 트리거하세요.                   |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected

| 오류 메시지      | <span style="font-weight:normal">SDC와 MDC 간 업그레이드가 검색되었습니다.</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | SAP HANA 인스턴스가 SDC에서 MDC로 업그레이드되었습니다. 업데이트 후에 백업이 실패합니다. |
| **권장 작업** | [SDC에서 MDC로 업그레이드](#sdc-to-mdc-upgrade-with-a-change-in-sid)에 나열된 단계에 따라 문제를 해결합니다. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 오류 메시지      | <span style="font-weight:normal">잘못된 Backint 구성이 검색되었습니다.</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | 지원 매개 변수가 잘못 지정 된 Azure Backup |
| **권장 작업** | 다음(backint) 매개 변수가 설정되었는지 확인합니다.<br/>\* [catalog_backup_using_backint:true]<br/>\* [enable_accumulated_catalog_backup:false]<br/>\* [parallel_data_backup_backint_channels:1]<br/>\* [log_backup_timeout_s:900)]<br/>\* [backint_response_timeout:7200]<br/>HOST에 backint 기반 매개 변수가 있는 경우 해당 매개 변수를 제거합니다. 매개 변수가 HOST 수준에 없지만 데이터베이스 수준에서 수동으로 수정된 경우 앞에서 설명한 대로 적절한 값으로 되돌립니다. 또는 Azure Portal에서 [보호 중지 및 백업 데이터 보존](./sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)을 실행한 후 **백업 다시 시작**을 선택합니다. |

### <a name="usererrorincompatiblesrctargetsystemsforrestore"></a>UserErrorIncompatibleSrcTargetSystemsForRestore

|오류 메시지  |복원의 원본 및 대상 시스템이 호환되지 않습니다.  |
|---------|---------|
|가능한 원인   | 복원을 위해 선택한 원본 및 대상 시스템이 호환되지 않습니다.        |
|권장 작업   |   복원 시나리오가 다음과 같은 호환되지 않는 복원 목록에 있지 않은지 확인합니다. <br><br>   **사례 1:** 복원하는 동안 SYSTEMDB의 이름을 바꿀 수 없습니다.  <br><br> **사례 2:** 원본 - SDC 및 대상 - MDC: 원본 데이터베이스를 대상의 SYSTEMDB 또는 테넌트 DB로 복원할 수 없습니다. <br><br> **사례 3:** 원본 - MDC 및 대상 - SDC: 원본 데이터베이스(SYSTEMDB 또는 테넌트 DB)를 대상으로 복원할 수 없습니다. <br><br>  자세한 내용은 [SAP 지원 실행 패드](https://launchpad.support.sap.com)의 **1642148** 참고 사항을 참조하세요. |

## <a name="restore-checks"></a>복원 확인

### <a name="single-container-database-sdc-restore"></a>SDC(단일 컨테이너 데이터베이스) 복원

HANA용 SDC(단일 컨테이너 데이터베이스)를 다른 SDC 머신으로 복원하는 동안 입력에 유의합니다. 데이터베이스 이름에는 소문자를 지정하고 괄호로 묶은 "sdc"를 앞에 추가해야 합니다. HANA 인스턴스는 대문자로 표시됩니다.

SDC HANA 인스턴스 "H21"이 백업되었다고 가정합니다. 백업 항목 페이지에서 백업 항목 이름이 **"h21(sdc)"** 으로 표시됩니다. 이 데이터베이스를 다른 대상 SDC(예: H11)로 복원하려고 하는 경우 다음 입력을 제공해야 합니다.

![복원된 SDC 데이터베이스 이름](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

다음 사항에 유의하세요.

- 기본적으로 복원된 db 이름에는 백업 항목 이름이 채워집니다. 이 경우 h21(sdc)입니다.
- H11로 대상을 선택 하면 복원 된 db 이름이 자동으로 변경 되지 않습니다. **h11(sdc)로 편집해야 합니다**. SDC와 관련하여 복원된 db 이름은 괄호로 묶은 소문자 'sdc'를 앞에 붙인 대상 인스턴스 ID입니다.
- SDC에는 단일 데이터베이스만 있을 수 있으므로 이 확인란을 클릭하여 기존 데이터베이스 데이터를 복구 지점 데이터로 재정의하도록 허용해야 합니다.
- Linux는 대/소문자를 구분합니다. 따라서 대/소문자를 유지해야 합니다.

### <a name="multiple-container-database-mdc-restore"></a>MDC(여러 컨테이너 데이터베이스) 복원

HANA에 대한 여러 컨테이너 데이터베이스에서 표준 구성은 SYSTEMDB + 1개 이상의 테넌트 DB입니다. 전체 SAP HANA 인스턴스를 복원하는 것은 SYSTEMDB와 테넌트 DB를 모두 복원하는 것입니다. 먼저 SYSTEMDB를 복원한 다음, 테넌트 DB의 복원을 진행합니다. 시스템 DB는 기본적으로 선택한 대상의 시스템 정보를 재정의합니다. 또한 이 복원은 대상 인스턴스의 BackInt 관련 정보를 재정의합니다. 따라서 시스템 DB가 대상 인스턴스로 복원된 후 사전 등록 스크립트를 다시 실행합니다. 그래야만 후속 테넌트 DB 복원 작업이 성공합니다.

## <a name="back-up-a-replicated-vm"></a>복제된 VM 백업

### <a name="scenario-1"></a>시나리오 1

원본 VM은 Azure Site Recovery 또는 Azure VM 백업을 사용하여 복제되었습니다. 새 VM은 이전 VM을 시뮬레이트하도록 빌드되었습니다. 따라서 설정이 정확히 동일합니다. 원래 VM이 삭제되었으며 VM 백업 또는 Azure Site Recovery에서 복원이 수행되었기 때문입니다.

이 시나리오에는 두 가지 가능한 사례가 포함될 수 있습니다. 이러한 두 경우 모두에서 복제된 VM을 백업하는 방법을 알아봅니다.

1. 만든 새 VM의 이름은 동일하며 삭제된 VM과 동일한 리소스 그룹 및 구독에 있습니다.

    - 확장이 VM에 이미 있지만 어떤 서비스에도 표시되지 않습니다.
    - 사전 등록 스크립트 실행
    - Azure Portal에서 동일한 머신에 대한 확장을 다시 등록합니다(**백업** -> **세부 정보 보기** -> 관련 Azure VM 선택 -> 다시 등록).
    - 그러면 기존에 백업한 데이터베이스(삭제된 VM)가 성공적으로 백업되기 시작합니다.

2. 새로 만든 VM에는 다음 중 하나가 지정됩니다.

    - 삭제된 VM과 다른 이름
    - 삭제된 VM과 이름이 같지만 삭제된 VM과 비교하여 다른 리소스 그룹 또는 구독에 있습니다.

    이 경우 다음 단계를 수행합니다.

    - 확장이 VM에 이미 있지만 어떤 서비스에도 표시되지 않습니다.
    - 사전 등록 스크립트 실행
    - 새 데이터베이스를 검색하고 보호하는 경우 포털에 중복된 활성 데이터베이스가 표시되기 시작합니다. 이 문제를 방지하려면 이전 데이터베이스에 대해 [데이터 보관을 통해 보호를 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)합니다. 그런 다음, 나머지 단계를 계속 진행합니다.
    - 백업을 사용하도록 설정할 데이터베이스 검색
    - 이러한 데이터베이스에 대해 백업 사용
    - 기존에 백업한 데이터베이스(삭제된 VM)는 계속 자격 증명 모음에 저장되고 해당 백업은 정책에 따라 보존됩니다.

### <a name="scenario-2"></a>시나리오 2

원본 VM은 Azure Site Recovery 또는 Azure VM 백업을 사용하여 복제되었습니다. 새 VM은 템플릿으로 사용되는 콘텐츠에서 빌드되었습니다. 이 VM은 새 SID를 사용하는 새 VM입니다.

다음 단계에 따라 새 VM에서 백업을 사용하도록 설정하세요.

- 확장이 VM에 이미 있지만 어떤 서비스에도 표시되지 않습니다.
- 사전 등록 스크립트를 실행합니다. 새 VM의 SID를 기준으로 다음과 같은 두 가지 시나리오가 발생할 수 있습니다.
  - 원래 VM과 새 VM의 SID가 동일합니다. 사전 등록 스크립트가 성공적으로 실행됩니다.
  - 원래 VM과 새 VM의 SID가 다릅니다. 사전 등록 스크립트가 실패합니다. 이 시나리오에 대해 도움이 필요하면 지원 서비스에 문의하세요.
- 백업하려는 데이터베이스를 검색합니다.
- 이러한 데이터베이스에 대해 백업을 사용하도록 설정합니다.

## <a name="sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm"></a>동일한 VM에서 SDC 버전 업그레이드 또는 MDC 버전 업그레이드

SID 변경을 발생시키지 않는 OS 업그레이드, SDC 버전 변경 또는 MDC 버전 변경은 다음과 같이 처리할 수 있습니다.

- 새 OS 버전, SDC 또는 MDC 버전이 현재 [Azure Backup에서 지원](sap-hana-backup-support-matrix.md#scenario-support)되는지 확인합니다.
- 데이터베이스에 대해 [데이터 보관을 통해 보호 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 업그레이드 또는 업데이트 수행
- 사전 등록 스크립트를 다시 실행합니다. 일반적으로 업그레이드 프로세스는 필요한 역할을 제거합니다. 사전 등록 스크립트는 필요한 모든 역할을 확인하는 데 유용합니다.
- 데이터베이스에 대한 보호 재개

## <a name="sdc-to-mdc-upgrade-with-no-change-in-sid"></a>SID를 변경하지 않으면서 SDC에서 MDC로 업그레이드

SID 변경을 발생시키지 않는 SDC에서 MDC로의 업그레이드는 다음과 같이 처리할 수 있습니다.

- 새 MDC 버전이 현재 [Azure Backup에서 지원](sap-hana-backup-support-matrix.md#scenario-support)되는지 확인합니다.
- 이전 SDC 데이터베이스에 대해 [데이터 보관을 통해 보호 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 업그레이드를 수행합니다. 완료되면 HANA 시스템은 이제 시스템 DB 및 테넌트 DB가 있는 MDC가 됩니다.
- [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 다시 실행합니다.
- Azure Portal에서 동일한 머신에 대한 확장을 다시 등록합니다(**백업** -> **세부 정보 보기** -> 관련 Azure VM 선택 -> 다시 등록).
- 동일한 VM에 대해 **DB 다시 검색**을 클릭합니다. 이 작업을 수행하면 3단계의 새 DB가 SDC가 아닌 SYSTEMDB 및 테넌트 DB로 표시됩니다.
- 이전 SDC 데이터베이스는 계속 자격 증명 모음에 존재하며 이전에 백업된 데이터는 정책에 따라 보존됩니다.
- 이러한 데이터베이스에 대한 백업 구성

## <a name="sdc-to-mdc-upgrade-with-a-change-in-sid"></a>SID를 변경하면서 SDC에서 MDC로 업그레이드

SID를 변경하는 SDC에서 MDC로의 업그레이드는 다음과 같이 처리할 수 있습니다.

- 새 MDC 버전이 현재 [Azure Backup에서 지원](sap-hana-backup-support-matrix.md#scenario-support)되는지 확인합니다.
- 이전 SDC 데이터베이스에 대해 **데이터 보관을 통해 보호를 중지**합니다.
- 업그레이드를 수행합니다. 완료되면 HANA 시스템은 이제 시스템 DB 및 테넌트 DB가 있는 MDC가 됩니다.
- 올바른 세부 정보(새 SID 및 MDC)를 사용하여 [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 다시 실행합니다. SID의 변경으로 인해 스크립트를 성공적으로 실행하는 데 문제가 있을 수 있습니다. 문제가 발생하는 경우 Azure Backup 지원 서비스에 문의하세요.
- Azure Portal에서 동일한 머신에 대한 확장을 다시 등록합니다(**백업** -> **세부 정보 보기** -> 관련 Azure VM 선택 -> 다시 등록).
- 동일한 VM에 대해 **DB 다시 검색**을 클릭합니다. 이 작업을 수행하면 3단계의 새 DB가 SDC가 아닌 SYSTEMDB 및 테넌트 DB로 표시됩니다.
- 이전 SDC 데이터베이스는 계속 자격 증명 모음에 존재하며 이전에 백업된 데이터는 정책에 따라 보존됩니다.
- 이러한 데이터베이스에 대한 백업 구성

## <a name="re-registration-failures"></a>다시 등록 오류

다시 등록 작업을 트리거하기 전에 하나 이상의 다음 증상을 확인하세요.

- 다음 오류 코드 중 하나를 사용하여 VM에서 모든 작업(예: 백업, 복원 및 구성 백업)이 실패합니다. **WorkloadExtensionNotReachable, UserErrorWorkloadExtensionNotInstalled, WorkloadExtensionNotPresent, WorkloadExtensionDidntDequeueMsg**.
- 백업 항목에 대한 **백업 상태** 영역에 **연결할 수 없음**이 표시되면 동일한 상태를 유발하는 다른 모든 원인을 제외합니다.

  - VM에서 백업 관련 작업을 수행할 수 있는 권한이 없습니다.
  - VM이 종료되었으므로 백업을 수행할 수 없습니다.
  - 네트워크 문제

다음 이유 중 하나 이상으로 인해 이러한 현상이 발생할 수 있습니다.

- 확장이 삭제되었거나 포털에서 제거되었습니다.
- VM이 내부 디스크 복원을 통해 다시 복원되었습니다.
- 오랜 기간 동안 VM이 종료되었으므로 확장 구성이 만료되었습니다.
- VM이 삭제되고 삭제된 VM과 이름이 같고 동일한 리소스 그룹에 있는 다른 VM이 생성되었습니다.

위의 시나리오에서 VM에 대한 다시 등록 작업을 트리거하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- Azure VM의 SAP HANA 데이터베이스 백업에 대해서는 [질문과 대답](./sap-hana-faq-backup-azure-vm.md)을 검토하세요.
