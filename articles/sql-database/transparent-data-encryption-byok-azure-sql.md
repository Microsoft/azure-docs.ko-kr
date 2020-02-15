---
title: 고객 관리 TDE (투명 한 데이터 암호화)
description: SQL Database 및 Data Warehouse에서 Azure Key Vault를 통해 BYOK(Bring Your Own Key)를 TDE(투명한 데이터 암호화)에 지원합니다. BYOK를 통한 TDE 개요, 이점, 작동 방법, 고려 사항 및 권장 사항을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/12/2020
ms.openlocfilehash: 8e91bb9223f3e6ccd4c76614d75db8591dbed045
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201522"
---
# <a name="azure-sql-transparent-data-encryption-with-customer-managed-key"></a>고객 관리 키를 사용 하는 Azure SQL 투명한 데이터 암호화

고객 관리 키를 사용 하는 [TDE (](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) Azure SQL 투명한 데이터 암호화)를 사용 하 여 미사용 데이터 보호를 위한 Bring Your Own Key (byok) 시나리오를 사용 하 고, 조직이 키와 데이터 관리에서 의무 분리를 구현할 수 있습니다. 고객이 관리 하는 투명 한 데이터 암호화를 사용 하는 고객은 키 수명 주기 관리 (키 생성, 업로드, 회전, 삭제), 키 사용 권한 및 키에 대 한 작업 감사에 대 한 모든 권한을 담당 합니다.

이 시나리오에서 TDE 보호기 라는 DEK (데이터베이스 암호화 키)를 암호화 하는 데 사용 되는 키는 클라우드 기반 외부 키 관리 시스템으로, 고객이 소유 하 고 고객 관리 [Azure Key Vault (AKV)](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)에 저장 된 고객 관리 비대칭 키입니다. Key Vault는 FIPS 140-2 수준 2 유효성 검사 된 Hsm (하드웨어 보안 모듈)에 의해 선택적으로 지원 되는 RSA 암호화 키에 대해 항상 사용 가능 하며 확장 가능한 보안 저장소입니다. 저장 된 키에 대 한 직접 액세스를 허용 하지 않지만 권한 있는 엔터티에 대 한 키를 사용 하 여 암호화/암호 해독 서비스를 제공 합니다. 키를 키 자격 증명 모음에 의해 생성 되거나, [프레미스 HSM 장치에서 키 자격 증명 모음으로 전송](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys)하거나 가져올 수 있습니다.

Azure SQL Database 및 Azure SQL Data Warehouse의 경우 TDE 보호기는 논리 서버 수준에서 설정 되며 해당 서버와 연결 된 모든 암호화 된 데이터베이스에서 상속 됩니다. Azure SQL Managed Instance의 경우 TDE 보호기는 인스턴스 수준에서 설정 되 고 해당 인스턴스의 모든 암호화 된 데이터베이스에서 상속 됩니다. *서버* 라는 용어는 다르게 지정 되지 않는 한이 문서 전체에서 SQL Database 논리 서버와 관리 되는 인스턴스를 모두 나타냅니다.

> [!IMPORTANT]
> 고객이 관리 하는 TDE를 사용 하 여 시작 하려는 서비스 관리 TDE를 사용 하는 경우 데이터를 전환 하는 동안 데이터는 암호화 된 상태로 유지 되며 가동 중지 또는 데이터베이스 파일의 다시 암호화는 발생 하지 않습니다. 서비스 관리 키에서 고객 관리 키로 전환 하는 경우에만 빠른 온라인 작업 인 DEK를 다시 암호화 해야 합니다.

## <a name="benefits-of-the-customer-managed-tde"></a>고객 관리 TDE의 이점

고객 관리 TDE는 고객에 게 다음과 같은 이점을 제공 합니다.

- TDE 보호기의 사용 및 관리에 대 한 완전 하 고 세부적인 제어

- TDE 보호기 사용의 투명성

- 조직 내의 키 및 데이터 관리에서 의무 분리를 구현 하는 기능

- Key Vault 관리자는 암호화 된 데이터베이스에 액세스할 수 없도록 키 액세스 권한을 해지할 수 있습니다.

- AKV의 키 중앙 관리

- AKV는 Microsoft가 암호화 키를 보거나 추출할 수 없도록 설계 되었으므로 최종 고객의 신뢰를 강화 합니다.

## <a name="how-customer-managed-tde-works"></a>고객 관리 TDE 작동 방법

![고객 관리 TDE의 설정 및 기능](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-roles.PNG)

DEK의 암호화를 위해 AKV에 저장 된 TDE 보호기를 사용할 수 있으려면 key vault 관리자는 고유한 AAD id를 사용 하 여 서버에 대 한 다음 액세스 권한을 부여 해야 합니다.

- **get** -Key Vault에 있는 키의 공용 파트 및 속성을 검색 합니다.

- **wrapKey** -dek를 보호 (암호화) 할 수 있음

- **unwrapKey** -dek를 보호 해제 (암호 해독) 할 수 있음

Key vault 관리자는 [키 자격 증명 모음 감사 이벤트에 대 한 로깅을 사용 하도록 설정](https://docs.microsoft.com/azure/azure-monitor/insights/azure-key-vault)하 여 나중에 감사할 수 있습니다.

서버가 AKV에서 TDE 보호기를 사용 하도록 구성 된 경우 서버는 암호화를 위해 각 TDE 지원 데이터베이스의 DEK를 key vault로 보냅니다. Key vault는 암호화 된 DEK를 반환 하며,이는 사용자 데이터베이스에 저장 됩니다.

필요한 경우 서버는 암호 해독을 위해 보호 된 DEK를 key vault로 보냅니다.

로깅이 사용 되는 경우 감사자는 Azure Monitor을 사용 하 여 key vault 감사 이벤트 로그를 검토할 수 있습니다.

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="requirements-for-configuring-customer-managed-tde"></a>고객 관리 TDE를 구성 하기 위한 요구 사항

### <a name="requirements-for-configuring-akv"></a>AKV 구성에 대 한 요구 사항

- Key vault 및 SQL Database/관리 되는 인스턴스는 동일한 Azure Active Directory 테 넌 트에 속해야 합니다. 교차 테넌트 키 자격 증명 모음 및 서버 상호 작용은 지원되지 않습니다. 나중에 리소스를 이동 하려면 AKV를 사용 하는 TDE를 다시 구성 해야 합니다. [리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)에 대해 자세히 알아보세요.

- 데이터 손실 실수로 인 한 키 (또는 키 자격 증명 모음) 삭제가 발생 하지 않도록 보호 하기 위해 키 자격 증명 모음에서 [일시 삭제](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 기능을 사용 하도록 설정 해야 합니다. 일시 삭제 된 리소스는 고객이 복구 하거나 제거 하지 않는 한 90 일간 보존 됩니다. *복구* 및 *제거* 작업에는 키 자격 증명 모음 액세스 정책과 연결된 고유 권한이 있습니다. 일시 삭제 기능은 기본적으로 해제 되어 있으며 [Powershell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell#enabling-soft-delete) 또는 [CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli#enabling-soft-delete)를 통해 사용 하도록 설정할 수 있습니다. Azure Portal를 통해 사용 하도록 설정할 수 없습니다.  

- Azure Active Directory id를 사용 하 여 키 자격 증명 모음 (get, wrapKey, unwrapKey)에 대 한 SQL Database 서버 또는 관리 되는 인스턴스 액세스를 부여 합니다. Azure Portal 사용 하는 경우 Azure AD id가 자동으로 생성 됩니다. PowerShell 또는 CLI를 사용 하는 경우 Azure AD id를 명시적으로 만들고 완료를 확인 해야 합니다. PowerShell을 사용하는 경우 자세한 단계별 지침은 [BYOK 기반 TDE 구성](transparent-data-encryption-byok-azure-sql-configure.md) 및 [Managed Instance에 대해 BYOK 기반 TDE 구성](https://aka.ms/sqlmibyoktdepowershell)을 참조하세요.

- AKV에서 방화벽을 사용 하는 경우 *신뢰할 수 있는 Microsoft 서비스에서 방화벽을 우회 하도록 허용*옵션을 사용 하도록 설정 해야 합니다.

### <a name="requirements-for-configuring-tde-protector"></a>TDE 보호기를 구성 하기 위한 요구 사항

- TDE 보호기는 비대칭, RSA 2048 또는 RSA HSM 2048 키 일 수 있습니다.

- 키 활성화 날짜 (설정 된 경우)는 과거 날짜와 시간 이어야 합니다. 만료 날짜 (설정 된 경우)는 미래의 날짜 및 시간 이어야 합니다.

- 키는 *사용* 상태 여야 합니다.

- 키 자격 증명 모음에 기존 키를 가져오는 경우 지원 되는 파일 형식 (.pfx, byok 또는. backup)에서 제공 해야 합니다.

## <a name="recommendations-when-configuring-customer-managed-tde"></a>고객 관리 TDE 구성 시 권장 사항

### <a name="recommendations-when-configuring-akv"></a>AKV 구성 시 권장 사항

- 서버가 주요 자격 증명 모음의 TDE 보호기에 액세스할 때 고가용성을 보장 하기 위해 최대 500 범용 또는 200 중요 비즈니스용 단일 구독의 key vault에 연결 합니다. 이러한 수치는 환경을 기반으로 하며 [주요 자격 증명 모음 서비스 제한](https://docs.microsoft.com/azure/key-vault/key-vault-service-limits)에 문서화 되어 있습니다. 여기서는 서버 장애 조치 (failover) 후에 문제가 발생 하지 않도록 하는 것이 좋습니다 .이는 해당 서버에 있는 데이터베이스의 경우 자격 증명 모음에 대해 많은 키 작업을 트리거합니다.

- 주요 자격 증명 모음에 리소스 잠금을 설정 하 여이 중요 한 리소스를 삭제할 수 있는 사용자를 제어 하 고 실수로 또는 무단 삭제를 방지할 수 있습니다. [리소스 잠금](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-lock-resources)에 대 한 자세한 정보.

- 모든 암호화 키에 대 한 감사 및 보고 사용: Key vault는 다른 보안 정보 및 이벤트 관리 도구에 쉽게 삽입할 수 있는 로그를 제공 합니다. Operations Management Suite [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-key-vault) 는 이미 통합 된 서비스의 한 예입니다.

- 서로 다른 지역에 있는 두 개의 키 자격 증명 모음에 각 서버를 연결 하 고 암호화 된 데이터베이스의 고가용성을 보장 하기 위해 동일한 키 자료를 보관 합니다. TDE 보호기와 동일한 지역에 있는 key vault의 키만 표시 합니다. 시스템에서 사용

### <a name="recommendations-when-configuring-tde-protector"></a>TDE 보호기를 구성할 때의 권장 사항
- TDE 보호기의 복사본을 안전한 장소에 보관 하거나 에스크로 서비스로 에스크로 하세요.

- 키가 키 자격 증명 모음에 생성 된 경우 AKV에서 키를 처음 사용 하기 전에 키 백업을 만듭니다. 백업은 Azure Key Vault 으로만 복원할 수 있습니다. [AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey) 명령에 대해 자세히 알아보세요.

- 키에 대 한 변경 내용이 있을 때마다 (예: 키 특성, 태그, Acl) 새 백업을 만듭니다.

- 키를 회전할 때 키 자격 증명 모음에서 **이전 버전의 키를 유지**합니다. 그러면 이전 데이터베이스 백업을 복원할 수 있습니다. 데이터베이스에 대 한 TDE 보호기가 변경 되 면 데이터베이스의 이전 백업은 최신 TDE 보호기를 사용 하도록 **업데이트 되지 않습니다** . 복원 시 각 백업에는 생성 시에 암호화 된 TDE 보호기가 필요 합니다. 키 회전은 [PowerShell을 사용하여 투명한 데이터 암호화 방지 프로그램 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)에 나와 있는 지침에 따라 수행할 수 있습니다.

- 서비스 관리 키로 전환한 후에도 AKV에서 이전에 사용한 키를 모두 유지 합니다. 이를 통해 AKV에 저장 된 TDE 보호기를 사용 하 여 데이터베이스 백업을 복원할 수 있습니다.  를 사용 하 Azure Key Vault 여 만든 TDE 보호기는 서비스 관리 키를 사용 하 여 남은 저장 된 모든 백업이 생성 될 때까지 유지 되어야 합니다. [AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyvault/backup-azkeyvaultkey)를 사용 하 여 이러한 키의 복구 가능한 백업 복사본을 만듭니다.

- 데이터 손실 위험 없이 보안 인시던트 중에 잠재적으로 손상 된 키를 제거 하려면 [잠재적으로 손상 된 키 제거](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)의 단계를 따르세요.

## <a name="inaccessible-tde-protector"></a>액세스할 수 없는 TDE 보호기

투명 한 데이터 암호화가 고객 관리 키를 사용 하도록 구성 된 경우 데이터베이스를 온라인 상태로 유지 하려면 TDE 보호기에 대 한 지속적인 액세스가 필요 합니다. 서버가 AKV에서 고객이 관리 하는 TDE 보호기에 대 한 액세스 권한을 상실 하는 경우 (최대 10 분) 데이터베이스에서 해당 오류 메시지와 함께 모든 연결을 거부 하 고 해당 상태를 *액세스할 수*없음으로 변경 하기 시작 합니다. 액세스할 수 없는 상태의 데이터베이스에 대해 허용 되는 유일한 작업은 삭제 하는 것입니다.

> [!NOTE]
> 일시적인 네트워킹 중단으로 인해 데이터베이스에 액세스할 수 없는 경우에는 작업이 필요 하지 않으며 데이터베이스가 자동으로 다시 온라인 상태가 됩니다.

키에 대 한 액세스가 복원 된 후 데이터베이스를 다시 온라인 상태로 전환 하려면 추가 시간과 단계가 필요 하며,이는 키에 대 한 액세스 권한 없이 경과 된 시간 및 데이터베이스의 데이터 크기에 따라 달라질 수 있습니다.

- 키 액세스를 8 시간 이내에 복원 하는 경우 데이터베이스는 다음 시간 내에 자동으로 치료 됩니다.

- 키 액세스가 8 시간 넘게 복원 된 경우 자동 치료를 수행할 수 없으며 데이터베이스를 다시 가져오면 포털에서 추가 단계가 필요 하며 데이터베이스 크기에 따라 상당한 시간이 걸릴 수 있습니다. 데이터베이스가 다시 온라인 상태가 되 면 [장애 조치 (failover) 그룹](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) 구성, 지정 시간 복원 기록 및 태그와 같은 이전에 구성 된 서버 수준 설정이 **손실**됩니다. 따라서 8 시간 이내에 기본 키 액세스 문제를 식별 하 고 해결할 수 있는 알림 시스템을 구현 하는 것이 좋습니다.

### <a name="accidental-tde-protector-access-revocation"></a>우발적 TDE 보호기 액세스 해지

키 자격 증명 모음에 대 한 충분 한 액세스 권한이 있는 사용자가 실수로 키에 대 한 서버 액세스를 사용 하지 않도록 설정할 수 있습니다.

- 서버에서 key vault의 *get*, *wrapKey*, *unwrapKey* 권한 취소

- 키 삭제

- 키 자격 증명 모음 삭제

- 주요 자격 증명 모음 방화벽 규칙 변경

- Azure Active Directory에서 서버의 관리 되는 id를 삭제 하는 중

[데이터베이스에 액세스할 수 없게 되는 일반적인 원인](https://docs.microsoft.com/sql/relational-databases/security/encryption/troubleshoot-tde?view=azuresqldb-current#common-errors-causing-databases-to-become-inaccessible)에 대해 자세히 알아보세요.

## <a name="monitoring-of-the-customer-managed-tde"></a>고객 관리 TDE 모니터링

데이터베이스 상태를 모니터링 하 고 TDE 보호기 액세스 손실에 대 한 경고를 사용 하도록 설정 하려면 다음 Azure 기능을 구성 합니다.
- [Azure Resource Health](https://docs.microsoft.com/azure/service-health/resource-health-overview). TDE 보호기에 대 한 액세스 권한이 손실 된 액세스 불가능 데이터베이스는 데이터베이스에 대 한 첫 번째 연결이 거부 된 후 "사용할 수 없음"으로 표시 됩니다.
- 고객 관리 키 자격 증명 모음의 TDE 보호기에 대 한 액세스가 실패 하는 [활동 로그](https://docs.microsoft.com/azure/service-health/alerts-activity-log-service-notifications) 는 활동 로그에 항목을 추가 합니다.  이러한 이벤트에 대 한 경고를 만들면 최대한 빨리 액세스를 복구할 수 있습니다.
- 사용자의 기본 설정에 따라 알림 및 경고를 보내도록 [작업 그룹](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups) 을 정의할 수 있습니다 (예: 이메일/SMS/푸시/음성, 논리 앱, 웹 후크, Itsm 또는 Automation Runbook).

## <a name="database-backup-and-restore-with-customer-managed-tde"></a>고객 관리 TDE를 사용 하 여 데이터베이스 백업 및 복원

Key Vault의 키를 사용 하 여 데이터베이스가 TDE로 암호화 되 면 새로 생성 된 백업도 동일한 TDE 보호기를 사용 하 여 암호화 됩니다. TDE 보호기가 변경 되 면 데이터베이스의 이전 백업은 최신 TDE 보호기를 사용 하도록 **업데이트 되지 않습니다** .

Key Vault에서 TDE 보호기로 암호화 된 백업을 복원 하려면 대상 서버에서 키 자료를 사용할 수 있는지 확인 합니다. 따라서 모든 이전 버전의 TDE 보호기를 key vault에 보관 하 여 데이터베이스 백업을 복원할 수 있도록 하는 것이 좋습니다.

> [!IMPORTANT]
> 언제 든 지 서버에 대해 둘 이상의 TDE 보호기를 설정할 수 있습니다. Azure Portal 블레이드의 "키를 기본 TDE 보호기로 설정"으로 표시 된 키입니다. 그러나 여러 추가 키를 TDE 보호기로 표시 하지 않고 서버에 연결할 수 있습니다. 이러한 키는 DEK를 보호 하는 데 사용 되지 않지만 백업 파일이 해당 지 문으로 키를 사용 하 여 암호화 된 경우 백업에서 복원 하는 동안 사용할 수 있습니다.

백업을 복원 하는 데 필요한 키를 대상 서버에서 더 이상 사용할 수 없는 경우 restore try: "대상 서버 `<Servername>`에 \<타임 스탬프 #1 > 및 \<타임 스탬프 #2 > 사이에 만들어진 모든 AKV Uri에 대 한 액세스 권한이 없습니다." 라는 오류 메시지가 반환 됩니다. 모든 AKV Uri를 복원한 후 작업을 다시 시도 하세요. "

이를 완화 하려면 대상 SQL Database 논리 서버에 대 한 [AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) cmdlet을 실행 하거나 대상 관리 인스턴스의 [AzSqlInstanceKeyVaultKey](/powershell/module/az.sql/get-azsqlinstancekeyvaultkey) 을 실행 하 여 사용 가능한 키 목록을 반환 하 고 누락 된 키를 식별 합니다. 모든 백업을 복원할 수 있도록 하려면 복원 대상 서버에 필요한 모든 키에 대 한 액세스 권한이 있는지 확인 합니다. 이러한 키는 TDE 보호기로 표시할 필요가 없습니다.

SQL Database 백업 복구에 대한 자세한 내용은 [Azure SQL 데이터베이스 복구](sql-database-recovery-using-backups.md)를 참조하세요. SQL Data Warehouse 백업 복구에 대한 자세한 내용은 [Azure SQL Data Warehouse 복구](../sql-data-warehouse/backup-and-restore.md)를 참조하세요. 관리 되는 인스턴스를 사용 하는 SQL Server의 네이티브 백업/복원에 대 한 자세한 내용은 [빠른 시작: 데이터베이스를 Managed Instance 복원](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started-restore) 을 참조 하세요.

로그 파일에 대 한 추가 고려 사항: 백업 된 로그 파일은 회전 되 고 데이터베이스에서 새 TDE 보호기를 사용 하는 경우에도 원래 TDE 보호기를 사용 하 여 암호화 된 상태로 유지 됩니다.  복원할 때 데이터베이스를 복원하려면 두 키가 모두 필요합니다.  로그 파일이 Azure Key Vault에 저장 된 TDE 보호기를 사용 하는 경우 데이터베이스에서 서비스 관리 TDE를 사용 하도록 변경 했더라도 복원 시이 키가 필요 합니다.

## <a name="high-availability-with-customer-managed-tde"></a>고객 관리 TDE를 사용 하는 고가용성

서버에 대해 구성 된 지역 중복이 없는 경우에도 동일한 키 자료를 사용 하 여 서로 다른 두 지역에 두 개의 다른 키 자격 증명 모음을 사용 하도록 서버를 구성 하는 것이 좋습니다. 서버와 동일한 지역에 있는 기본 키 자격 증명 모음을 사용 하 여 TDE 보호기를 만들고, 다른 Azure 지역의 키 자격 증명 모음에 키를 복제 하 여 서버가 기본 키 자격 증명 모음을 사용 해야 하는 두 번째 key vault에 대 한 액세스 권한을 갖도록 할 수 있습니다. 데이터베이스가 가동 및 실행 되는 동안 중단 됩니다.

AzKeyVaultKey cmdlet을 사용 하 여 기본 키 자격 증명 모음에서 암호화 된 형식으로 키를 검색 한 다음 AzKeyVaultKey cmdlet을 사용 하 고 두 번째 지역에서 키 자격 증명 모음을 지정 하 여 키를 복제 합니다. 또는 Azure Portal를 사용 하 여 키를 백업 및 복원 합니다. 다른 지역의 보조 키 자격 증명 모음에 있는 키는 TDE 보호기로 표시 되지 않아야 하며 허용 되지 않습니다.

 기본 키 자격 증명 모음에 영향을 주는 작동 중단이 발생 하는 경우에만 시스템은 보조 키 자격 증명 모음에서 지문이 같은 다른 연결 된 키로 자동으로 전환 합니다 (있는 경우). 이 스위치는 취소 된 액세스 권한으로 인해 TDE 보호기에 액세스할 수 없거나, 키 또는 키 자격 증명 모음이 삭제 되어 고객이 의도적으로 서버에서 키에 액세스를 제한 하는 것을 의미할 수 있으므로 발생 하지 않습니다.

![단일 서버 HA](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-ha.png)

## <a name="geo-dr-and-customer-managed-tde"></a>지리적 DR 및 고객 관리 TDE

[활성 지역 복제](https://docs.microsoft.com/azure/sql-database/sql-database-active-geo-replication) 및 [장애 조치 (failover) 그룹](https://docs.microsoft.com/azure/sql-database/sql-database-auto-failover-group) 시나리오 모두에서 관련 된 각 서버에는 별도의 키 자격 증명 모음이 필요 하며,이는 동일한 Azure 지역에 있는 서버와 함께 배치 되어야 합니다. 고객은 주요 자격 증명 모음에 대 한 키 자료를 일관 되 게 유지 하 여 지역에서 가동 중단으로 인해 주 복제본이 액세스할 수 없게 되 고 장애 조치 (failover)가 트리거되면 로컬 키 자격 증명 모음에서 동일한 키를 사용할 수 있습니다. . 최대 4 개의 보조 복제본을 구성할 수 있으며 체인 (보조의 보조)은 지원 되지 않습니다.

불완전 한 키 자료로 인해 지역에서 복제를 설정 하거나 수행 하는 동안 문제를 방지 하려면 고객 관리 TDE를 구성할 때 다음 규칙을 따르는 것이 중요 합니다.

- 관련 된 모든 주요 자격 증명 모음에는 동일한 속성 및 해당 서버에 대 한 동일한 액세스 권한이 있어야 합니다.

- 관련 된 모든 key vault는 동일한 키 자료를 포함 해야 합니다. 현재 TDE 보호기 뿐만 아니라 백업 파일에 사용 될 수 있는 이전의 모든 TDE 보호기에 적용 됩니다.

- 처음에는 TDE 보호기의 초기 설정 및 회전이 모두 보조 복제본에서 수행 된 다음 주 복제본에서 수행 되어야 합니다.

![장애 조치 그룹 및 Geo-DR](./media/transparent-data-encryption-byok-azure-sql/customer-managed-tde-with-bcdr.png)

장애 조치 (failover)를 테스트 하려면 [활성 지역 복제 개요](sql-database-geo-replication-overview.md)의 단계를 따르세요. 두 키 자격 증명 모음에 대 한 SQL 액세스 권한이 유지 되는지 확인 하기 위해 정기적으로 수행 해야 합니다.

## <a name="next-steps"></a>다음 단계

고객 관리 TDE를 사용 하 여 일반적인 작업에 대해 다음 PowerShell 샘플 스크립트를 확인할 수도 있습니다.

- [PowerShell을 사용 하 여 SQL Database에 대 한 투명한 데이터 암호화 보호기 회전](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- [PowerShell을 사용 하 여 SQL Database에 대 한 TDE (투명한 데이터 암호화) 보호기 제거](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql-remove-tde-protector)

- [PowerShell을 사용 하 여 사용자 고유의 키로 Managed Instance의 투명한 데이터 암호화 관리](https://docs.microsoft.com/azure/sql-database/scripts/transparent-data-encryption-byok-sql-managed-instance-powershell?toc=%2fpowershell%2fmodule%2ftoc.json)
