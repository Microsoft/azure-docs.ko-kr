---
title: SAP HANA 데이터베이스 백업 오류 문제 해결
description: Azure Backup를 사용 하 여 SAP HANA 데이터베이스를 백업 하는 경우 발생할 수 있는 일반적인 오류를 해결 하는 방법을 설명 합니다.
ms.topic: troubleshooting
ms.date: 11/7/2019
ms.openlocfilehash: 04f9bafba0ca490b33a0daf3c3725e57d81bcc7e
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/05/2020
ms.locfileid: "75664601"
---
# <a name="troubleshoot-backup-of-sap-hana-databases-on-azure"></a>Azure에서 SAP HANA 데이터베이스의 백업 문제 해결

이 문서에서는 Azure virtual machines의 SAP HANA 데이터베이스 백업에 대한 문제 해결 정보를 제공 합니다. 현재 지원 되는 SAP HANA 백업 시나리오에 대한 자세한 내용은 [시나리오 지원](sap-hana-backup-support-matrix.md#scenario-support)을 참조 하세요.

## <a name="prerequisites-and-permissions"></a>필수 조건 및 권한

백업을 구성 하기 전에 [필수 구성 요소](tutorial-backup-sap-hana-db.md#prerequisites) 및 [권한 설정](tutorial-backup-sap-hana-db.md#setting-up-permissions) 섹션을 참조 하세요.

## <a name="common-user-errors"></a>일반 사용자 오류

### <a name="usererrorinopeninghanaodbcconnection"></a>UserErrorInOpeningHanaOdbcConnection

| 오류 메시지      | <span style="font-weight:normal">HANA 시스템에 연결 하지 못했습니다.</span>                        |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | SAP HANA 인스턴스가 다운 되었을 수 있습니다.<br/>HANA 데이터베이스와 상호 작용 하기 위해 Azure backup에 필요한 권한이 설정 되지 않았습니다. |
| **권장 작업** | SAP HANA 데이터베이스가 작동 하는지 확인 합니다. 데이터베이스가 실행 중인 경우 필요한 모든 권한이 설정 되어 있는지 확인 합니다. 사용 권한이 없는 경우 [preregistration 스크립트](https://aka.ms/scriptforpermsonhana) 를 실행 하 여 누락 된 사용 권한을 추가 합니다. |

### <a name="usererrorhanainstancenameinvalid"></a>UserErrorHanaInstanceNameInvalid

| 오류 메시지      | <span style="font-weight:normal">지정한 SAP HANA 인스턴스가 잘못 되었거나 찾을 수 없습니다.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | 단일 Azure VM의 여러 SAP HANA 인스턴스는 백업할 수 없습니다. |
| **권장 작업** | 백업 하려는 SAP HANA 인스턴스에서 [preregistration 스크립트](https://aka.ms/scriptforpermsonhana) 를 실행 합니다. 그래도 문제가 계속 되 면 Microsoft 지원에 문의 하세요. |

### <a name="usererrorhanaunsupportedoperation"></a>UserErrorHanaUnsupportedOperation

| 오류 메시지      | <span style="font-weight:normal">지정 된 SAP HANA 작업이 지원 되지 않습니다.</span>              |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | SAP HANA 용 Azure backup은 SAP HANA native client에서 수행 되는 증분 백업 및 작업을 지원 하지 않습니다 (스튜디오/환경/DBA 환경). |
| **권장 작업** | 자세한 내용은 [여기](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)를 참조 하세요. |

### <a name="usererrorhanapodoesnotsupportbackuptype"></a>UserErrorHANAPODoesNotSupportBackupType

| 오류 메시지      | <span style="font-weight:normal">이 SAP HANA 데이터베이스는 요청 된 백업 유형을 지원 하지 않습니다.</span>  |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | Azure backup은 스냅숏을 사용한 증분 백업 및 백업을 지원 하지 않습니다. |
| **권장 작업** | 자세한 내용은 [여기](https://docs.microsoft.com/azure/backup/sap-hana-backup-support-matrix#scenario-support)를 참조 하세요. |

### <a name="usererrorhanalsnvalidationfailure"></a>UserErrorHANALSNValidationFailure

| 오류 메시지      | <span style="font-weight:normal">백업 로그 체인이 끊어졌습니다.</span>                                    |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | 로그 백업 대상이 backint에서 파일 시스템으로 업데이트 되었거나 backint 실행 파일이 변경 되었을 수 있습니다. |
| **권장 작업** | 문제를 해결 하기 위해 전체 백업 트리거                   |

### <a name="usererrorincomaptiblesrctargetsystsemsforrestore"></a>UserErrorIncomaptibleSrcTargetSystsemsForRestore

| 오류 메시지      | <span style="font-weight:normal">복원에 사용할 원본 및 대상 시스템이 호환 되지 않습니다.</span>    |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | 복원 대상 시스템이 원본과 호환 되지 않습니다. |
| **권장 작업** | 지금 지원 되는 복원 유형에 대한 자세한 내용은 SAP Note [1642148](https://launchpad.support.sap.com/#/notes/1642148) 을 참조 하세요. |

### <a name="usererrorsdctomdcupgradedetected"></a>UserErrorSDCtoMDCUpgradeDetected 됨

| 오류 메시지      | <span style="font-weight:normal">SDC에서 MDC로 업그레이드 됨</span>                                   |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | SAP HANA 인스턴스가 SDC에서 MDC로 업그레이드 되었습니다. 업데이트 후에는 백업이 실패 합니다. |
| **권장 작업** | [SAP HANA 1.0에서 2.0으로 업그레이드 섹션](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database-troubleshoot#upgrading-from-sap-hana-10-to-20) 에 나열 된 단계에 따라 문제를 해결 합니다. |

### <a name="usererrorinvalidbackintconfiguration"></a>UserErrorInvalidBackintConfiguration

| 오류 메시지      | <span style="font-weight:normal">잘못 된 backint 구성이 검색 되었습니다.</span>                       |
| ------------------ | ------------------------------------------------------------ |
| **가능한 원인**    | Azure backup에 대해 지원 매개 변수가 잘못 지정 되었습니다. |
| **권장 작업** | 다음 (backint) 매개 변수가 설정 되었는지 확인 합니다.<br/>\* [catalog_backup_using_backint: true]<br/>\* [enable_accumulated_catalog_backup: false]<br/>\* [parallel_data_backup_backint_channels: 1]<br/>\* [log_backup_timeout_s: 900)]<br/>\* [backint_response_timeout: 7200]<br/>호스트에 backint 기반 매개 변수가 있는 경우 해당 매개 변수를 제거 합니다. 매개 변수가 호스트 수준에 없지만 데이터베이스 수준에서 수동으로 수정 된 경우 앞에서 설명한 대로 적절 한 값으로 되돌립니다. 또는 Azure Portal에서 [보호 중지를 실행 하 고 백업 데이터를 보존](https://docs.microsoft.com/azure/backup/sap-hana-db-manage#stop-protection-for-an-sap-hana-database) 한 후 **백업 다시 시작**을 선택 합니다. |

## <a name="restore-checks"></a>복원 확인

### <a name="single-container-database-sdc-restore"></a>SDC (단일 컨테이너 데이터베이스) 복원

HANA 용 단일 컨테이너 데이터베이스 (SDC)를 다른 SDC 컴퓨터로 복원 하는 동안 입력을 처리 합니다. 데이터베이스 이름에는 소문자를 지정 하 고 "sdc"는 대괄호 안에 추가 해야 합니다. HANA 인스턴스는 대문자로 표시 됩니다.

SDC HANA 인스턴스 "H21"이 백업 된 것으로 가정 합니다. 백업 항목 페이지에 백업 항목 이름이 **"h21 (sdc)"** 로 표시 됩니다. 이 데이터베이스를 다른 대상 SDC로 복원 하려고 시도 하는 경우 (예 H11) 다음 입력을 제공 해야 합니다.

![복원 된 SDC 데이터베이스 이름](media/backup-azure-sap-hana-database/hana-sdc-restore.png)

다음 사항에 유의하세요.

- 기본적으로 복원 된 db 이름에는 백업 항목 이름이 채워집니다. 이 경우 h21 (sdc)입니다.
- H11로 대상을 선택 하면 복원 된 db 이름이 자동으로 변경 되지 않습니다. **H11 (sdc)로 편집 해야**합니다. SDC와 관련 하 여 복원 된 db 이름에는 소문자를 사용 하 고 ' sdc '를 포함 하는 대상 인스턴스 ID가 추가 됩니다.
- SDC에는 단일 데이터베이스만 있을 수 있으므로이 확인란을 클릭 하 여 기존 데이터베이스 데이터를 복구 지점 데이터로 재정의 하도록 허용 해야 합니다.
- Linux는 대/소문자를 구분 합니다. 따라서 대/소문자를 유지 해야 합니다.

### <a name="multiple-container-database-mdc-restore"></a>여러 컨테이너 데이터베이스 (MDC) 복원

HANA에 대한 여러 컨테이너 데이터베이스에서 표준 구성은 SYSTEMDB + 1 이상의 테넌트 DB입니다. 전체 SAP HANA 인스턴스를 복원 하는 것은 SYSTEMDB와 테넌트 DB를 모두 복원 하는 것입니다. 하나는 먼저 SYSTEMDB를 복원한 다음 테넌트 DB를 진행 합니다. 시스템 DB는 기본적으로 선택한 대상의 시스템 정보를 재정의 합니다. 또한이 복원은 대상 인스턴스의 BackInt 관련 정보를 재정의 합니다. 따라서 시스템 DB가 대상 인스턴스로 복원 된 후 등록 전 스크립트를 다시 실행 합니다. 이후 테넌트 DB 복원 작업이 성공 합니다.

## <a name="upgrading-from-sap-hana-10-to-20"></a>SAP HANA 1.0에서 2.0로 업그레이드

SAP HANA 1.0 데이터베이스를 보호 하 고 2.0로 업그레이드 하려는 경우 다음 단계를 수행 합니다.

- 이전 SDC 데이터베이스에 대한 데이터 보존을 사용 하 여 [보호를 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database) 합니다.
- 업그레이드를 수행 합니다. 완료 후 HANA는 이제 시스템 DB 및 테넌트 DB를 사용 하 여 MDC를 사용 합니다.
- (Sid 및 mdc)의 올바른 세부 정보를 사용 하 여 [등록 전 스크립트](https://aka.ms/scriptforpermsonhana) 를 다시 실행 합니다.
- Azure Portal에서 동일한 컴퓨터에 대한 확장을 다시 등록 합니다 (백업 > 보기 세부 정보-> 관련 Azure VM 선택-> 다시 등록).
- 동일한 VM에 대해 Db 다시 검색을 클릭 합니다. 이 작업은 2 단계에서 새 Db를 표시 하 고, 올바른 세부 정보 (SYSTEMDB 및 SDC가 아닌 테넌트 DB)를 표시 합니다.
- 이러한 새 데이터베이스에 대한 백업을 구성 합니다.

## <a name="upgrading-without-an-sid-change"></a>SID 변경 없이 업그레이드

다음에 설명 된 대로 SID 변경을 발생 시 키 지 않는 OS 또는 SAP HANA로의 업그레이드가 처리 될 수 있습니다.

- 데이터베이스에 대한 데이터 보관을 사용 하 여 [보호 중지](sap-hana-db-manage.md#stop-protection-for-an-sap-hana-database)
- 업그레이드를 수행 합니다.
- [사전 등록 스크립트](https://aka.ms/scriptforpermsonhana)를 다시 실행 합니다. 일반적으로 업그레이드 프로세스가 필요한 역할을 제거 하는 것을 볼 수 있습니다. 사전 등록 스크립트를 실행 하면 필요한 모든 역할을 확인 하는 데 도움이 됩니다.
- 데이터베이스에 대한 [보호 다시 시작](sap-hana-db-manage.md#resume-protection-for-an-sap-hana-database)

## <a name="next-steps"></a>다음 단계

- Azure Vm에서 SAP HANA 데이터베이스 백업에 대한 질문과 [대답](https://docs.microsoft.com/azure/backup/sap-hana-faq-backup-azure-vm) 검토
