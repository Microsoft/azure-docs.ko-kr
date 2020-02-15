---
title: 고객 관리 키를 사용 하 여 단일 서버 데이터 암호화 Azure Database for PostgreSQL
description: 고객 관리 키를 사용 하 여 단일 서버 데이터 암호화를 Azure Database for PostgreSQL 하 여 미사용 데이터 보호에 대 한 Bring Your Own Key (BYOK)를 수행할 수 있습니다. 또한 조직이 키 및 데이터 관리에서 의무 분리를 구현할 수 있습니다.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 5516bfcb3ed32ba6635943298db2a7773db0a622
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77198703"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>고객 관리 키를 사용 하 여 단일 서버 데이터 암호화 Azure Database for PostgreSQL

> [!NOTE]
> 지금은이 기능을 사용 하기 위해 액세스를 요청 해야 합니다. 이렇게 하려면 AskAzureDBforPostgreSQL@service.microsoft.com에 문의 하세요.

Azure Database for PostgreSQL 단일 서버에 대 한 고객 관리 키를 사용 하 여 데이터 암호화를 통해 미사용 데이터 보호에 대 한 사용자 고유의 키 (BYOK)를 가져올 수 있습니다. 또한 조직이 키 및 데이터 관리에서 의무 분리를 구현할 수 있습니다. 고객 관리 암호화를 사용 하 여 키의 수명 주기, 키 사용 권한 및 키에 대 한 작업 감사를 모든 사용자에 게 책임 집니다.

Azure Database for PostgreSQL 단일 서버에 대 한 고객 관리 키를 사용 하는 데이터 암호화는 서버 수준에서 설정 됩니다. 지정 된 서버에 대해 KEK (키 암호화 키) 라고 하는 고객 관리 키를 사용 하 여 서비스에서 사용 하는 DEK (데이터 암호화 키)를 암호화 합니다. KEK는 고객이 소유 하 고 고객이 관리 하는 [Azure Key Vault](../key-vault/key-Vault-secure-your-key-Vault.md) 인스턴스에 저장 되는 비대칭 키입니다. KEK (키 암호화 키) 및 DEK (데이터 암호화 키)는이 문서의 뒷부분에서 자세히 설명 합니다.

Key Vault은 클라우드 기반의 외부 키 관리 시스템입니다. 가용성이 높고 RSA 암호화 키에 대 한 확장 가능 하 고 안전한 저장소를 제공 하며, 선택적으로 FIPS 140-2 수준 2의 유효성을 검사 한 하드웨어 보안 모듈 (Hsm)을 지원 합니다. 저장 된 키에 대 한 직접 액세스를 허용 하지 않지만 권한 있는 엔터티에 대 한 암호화 및 암호 해독 서비스를 제공 합니다. Key Vault는 키를 생성 하거나 가져올 수 있거나 [온-프레미스 HSM 장치에서 전송](../key-vault/key-Vault-hsm-protected-keys.md)될 수 있습니다.

> [!NOTE]
> 이 기능은 Azure Database for PostgreSQL 단일 서버가 "범용" 및 "메모리 액세스에 최적화 된" 가격 책정 계층을 지 원하는 모든 Azure 지역에서 사용할 수 있습니다.

## <a name="benefits"></a>이점

Azure Database for PostgreSQL 단일 서버에 대 한 데이터 암호화는 다음과 같은 이점을 제공 합니다.

* 데이터 액세스는 키를 제거 하 고 데이터베이스에 액세스할 수 없도록 하는 기능을 통해 사용자가 완전히 제어 합니다. 
*   키 수명 주기에 대 한 모든 권한 (회사 정책에 맞게 키 회전 포함)
*   Azure Key Vault의 중앙 관리 및 키 구성
*   보안 책임자와 DBA 및 시스템 관리자 간에 업무 분리를 구현 하는 기능

## <a name="terminology-and-description"></a>용어 및 설명

**DEK (데이터 암호화 키**): 파티션 또는 데이터 블록을 암호화 하는 데 사용 되는 대칭 AES256 키입니다. 서로 다른 키로 각 데이터 블록을 암호화하면 암호 분석 공격이 더욱 어려워집니다. DEK에 대한 액세스는 특정 블록을 암호화하고 암호 해독하는 리소스 공급자 또는 애플리케이션 인스턴스에 필요합니다. DEK를 새 키로 바꾸면 연결 된 블록의 데이터만 새 키로 다시 암호화 되어야 합니다.

**키 암호화 키 (KEK)** : deks를 암호화 하는 데 사용 되는 암호화 키입니다. Key Vault 하지 않는 KEK는 DEKs 자체를 암호화 하 고 제어할 수 있습니다. KEK에 대 한 액세스 권한이 있는 엔터티는 DEK가 필요한 엔터티와 다를 수 있습니다. KEK는 DEK를 암호 해독하는 데 필요하므로 KEK는 실질적으로 KEK를 삭제함으로써 DEK를 효과적으로 삭제할 수 있는 단일 지점이 됩니다.

KEKs로 암호화 된 DEKs는 별도로 저장 됩니다. KEK에 대 한 액세스 권한이 있는 엔터티만 이러한 DEKs의 암호를 해독할 수 있습니다. 자세한 내용은 [미사용 암호화의 보안](../security/fundamentals/encryption-atrest.md)을 참조 하세요.

## <a name="how-data-encryption-with-a-customer-managed-key-works"></a>고객이 관리 하는 키로 데이터 암호화가 작동 하는 방식

![개요를 보여 주는 다이어그램 Bring Your Own Key](media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png)

PostgreSQL 서버에서 DEK의 암호화를 위해 Key Vault에 저장 된 고객 관리 키를 사용 하도록 Key Vault 관리자는 서버에 대 한 다음 액세스 권한을 제공 합니다.

* **get**: 키 자격 증명 모음에서 키의 공용 파트 및 속성을 검색 합니다.
* **wrapKey**: dek를 암호화할 수 있습니다.
* **unwrapKey**: dek의 암호를 해독할 수 있습니다.

또한 키 자격 증명 모음 관리자는 [Key Vault 감사 이벤트 로깅을 사용 하도록 설정](../azure-monitor/insights/azure-key-vault.md)하 여 나중에 감사할 수 있습니다.

서버가 키 자격 증명 모음에 저장 된 고객 관리 키를 사용 하도록 구성 된 경우 서버는 암호화를 위해 키 자격 증명 모음에 DEK를 보냅니다. Key Vault은 사용자 데이터베이스에 저장 된 암호화 된 DEK를 반환 합니다. 마찬가지로, 필요한 경우 서버는 암호 해독을 위해 보호 된 DEK를 key vault로 보냅니다. 로깅 기능을 사용 하는 경우 감사자는 Azure Monitor을 사용 하 여 Key Vault 감사 이벤트 로그를 검토할 수 있습니다.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 단일 서버에 대 한 데이터 암호화를 구성 하기 위한 요구 사항

Key Vault를 구성 하기 위한 요구 사항은 다음과 같습니다.

* Key Vault 및 Azure Database for PostgreSQL 단일 서버는 동일한 Azure Active Directory (Azure AD) 테 넌 트에 속해야 합니다. 교차 테 넌 트 Key Vault 및 서버 상호 작용은 지원 되지 않습니다. 나중에 리소스를 이동 하려면 데이터 암호화를 다시 구성 해야 합니다.
* 실수로 키 (또는 Key Vault) 삭제가 발생 하는 경우 데이터 손실을 방지 하려면 키 자격 증명 모음에서 일시 삭제 기능을 사용 하도록 설정 해야 합니다. 일시 삭제 된 리소스는 사용자가이를 복구 하거나 제거 하는 경우를 제외 하 고 90 일 동안 보존 됩니다. 복구 및 제거 작업에는 Key Vault 액세스 정책에 연결 된 고유한 사용 권한이 있습니다. 일시 삭제 기능은 기본적으로 해제 되어 있지만 PowerShell 또는 Azure CLI를 통해 사용 하도록 설정할 수 있습니다 (Azure Portal를 통해 사용 하도록 설정할 수 없음).
* 고유한 관리 되는 id를 사용 하 여 get, wrapKey 및 unwrapKey 권한으로 key vault에 대 한 단일 서버 액세스를 Azure Database for PostgreSQL 부여 합니다. PostgreSQL 단일 서버에서 데이터 암호화를 사용 하도록 설정 하면 Azure Portal 고유 id가 자동으로 생성 됩니다. Azure Portal를 사용 하는 경우 자세한 단계별 지침은 [Azure Portal를 사용 하 여 Azure Database for PostgreSQL 단일 서버에 대 한 데이터 암호화](howto-data-encryption-portal.md) 를 참조 하세요.

* Key Vault에서 방화벽을 사용 하는 경우 **신뢰할 수 있는 Microsoft 서비스에서 방화벽을 우회 하도록 허용**옵션을 사용 하도록 설정 해야 합니다.

다음은 고객이 관리 하는 키를 구성 하기 위한 요구 사항입니다.

* DEK를 암호화 하는 데 사용 되는 고객 관리 키는 비대칭, RSA 2028만 가능 합니다.
* 키 활성화 날짜 (설정 된 경우)는 과거 날짜와 시간 이어야 합니다. 만료 날짜 (설정 된 경우)는 미래의 날짜 및 시간 이어야 합니다.
* 키는 *사용* 상태 여야 합니다.
* 키 자격 증명 모음에 기존 키를 가져오는 경우 지원 되는 파일 형식 (`.pfx`, `.byok`, `.backup`)에 제공 해야 합니다.

## <a name="recommendations"></a>권장 사항

고객 관리 키를 사용 하 여 데이터 암호화를 사용 하는 경우 Key Vault를 구성 하기 위한 권장 사항은 다음과 같습니다.

* Key Vault에 대 한 리소스 잠금을 설정 하 여이 중요 한 리소스를 삭제 하 고 실수로 또는 무단 삭제를 방지할 수 있는 사용자를 제어 합니다.
* 모든 암호화 키에 대 한 감사 및 보고를 사용 하도록 설정 합니다. Key Vault는 다른 보안 정보 및 이벤트 관리 도구에 쉽게 삽입할 수 있는 로그를 제공 합니다. Azure Monitor Log Analytics는 이미 통합 된 서비스의 한 예입니다.

* Key Vault 및 Azure Database for PostgreSQL 단일 서버가 동일한 지역에 있는지 확인 하 여 DEK 래핑 및 래핑 해제 작업에 더 빠르게 액세스할 수 있도록 합니다.

다음은 고객이 관리 하는 키를 구성 하기 위한 권장 사항입니다.

* 고객 관리 키의 복사본을 안전한 장소에 보관 하거나 에스크로 서비스로 에스크로 하세요.

* 키를 생성 하 Key Vault 경우 키를 처음 사용 하기 전에 키 백업을 만듭니다. 백업만 Key Vault로 복원할 수 있습니다. 백업 명령에 대 한 자세한 내용은 [AzKeyVaultKey](https://docs.microsoft.com/powershell/module/az.keyVault/backup-azkeyVaultkey)를 참조 하세요.

## <a name="inaccessible-customer-managed-key-condition"></a>고객 관리 키 조건에 액세스할 수 없음

Key Vault에서 고객 관리 키를 사용 하 여 데이터 암호화를 구성 하는 경우 서버를 온라인 상태로 유지 하려면이 키에 대 한 지속적인 액세스가 필요 합니다. 서버가 Key Vault에서 고객이 관리 하는 키에 대 한 액세스 권한을 상실 하면 서버는 10 분 이내에 모든 연결을 거부 하기 시작 합니다. 서버에서 해당 오류 메시지를 발행 하 고 서버 상태를 *액세스할 수*없음으로 변경 합니다. 이 상태에서 데이터베이스에 대해 허용 되는 유일한 작업은 삭제입니다.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Key Vault에서 실수로 인 한 키 액세스 해지

Key Vault에 대 한 충분 한 액세스 권한이 있는 사용자가 실수로 키에 대 한 서버 액세스를 사용 하지 않도록 설정할 수 있습니다.

* 서버에서 key vault의 get, wrapKey 및 unwrapKey 권한을 취소 합니다.
* 키를 삭제 합니다.
* 키 자격 증명 모음을 삭제 합니다.
* 주요 자격 증명 모음 방화벽 규칙 변경

* Azure AD에서 서버의 관리 되는 id를 삭제 하는 중입니다.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Key Vault에서 고객이 관리 하는 키 모니터링

데이터베이스 상태를 모니터링 하 고 투명 한 데이터 암호화 보호기 액세스 손실에 대 한 경고를 사용 하도록 설정 하려면 다음 Azure 기능을 구성 합니다.

* [Azure Resource Health](../service-health/resource-health-overview.md): 데이터베이스에 대 한 첫 번째 연결이 거부 된 후 고객 키에 대 한 액세스 권한이 손실 된 액세스 불가능 데이터베이스는 "액세스할 수 없음"으로 표시 됩니다.
* [활동 로그](../service-health/alerts-activity-log-service-notifications.md): 고객이 관리 하는 Key Vault의 고객 키에 대 한 액세스가 실패 하면 활동 로그에 항목이 추가 됩니다. 이러한 이벤트에 대 한 경고를 생성 하는 경우 가능한 한 빨리 액세스를 복구할 수 있습니다.

* [작업 그룹](../azure-monitor/platform/action-groups.md): 기본 설정에 따라 알림 및 경고를 보내도록 정의 합니다.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Key Vault에서 고객의 관리 키를 사용 하 여 복원 및 복제

Key Vault에 저장 된 고객의 관리 키를 사용 하 여 단일 서버를 암호화 Azure Database for PostgreSQL 한 후에는 새로 만든 서버 복사본도 암호화 됩니다. 로컬 또는 지역 복원 작업이 나 읽기 복제본을 통해이 새 복사본을 만들 수 있습니다. 그러나 새 고객이 관리 하는 암호화 키를 반영 하도록 복사본을 변경할 수 있습니다. 고객 관리 키가 변경 되 면 서버의 이전 백업에서 최신 키를 사용 하기 시작 합니다.

복원 또는 복제본을 만드는 동안 고객이 관리 하는 데이터 암호화를 설정 하는 동안 문제를 방지 하려면 master 및 복원/복제 서버에서 다음 단계를 수행 하는 것이 중요 합니다.

* Master Azure Database for PostgreSQL 단일 서버에서 복원 또는 읽기 복제본 만들기 프로세스를 시작 합니다.
* 고유 id에 아직 Key Vault 권한이 부여 되지 않았으므로 새로 만든 서버 (복원 된/복제본)를 액세스할 수 없는 상태로 유지 합니다.
* 복원/복제 서버의 데이터 암호화 설정에서 고객 관리 키의 유효성을 다시 검사 합니다. 이렇게 하면 새로 만든 서버에 Key Vault에 저장 된 키에 대 한 래핑 및 래핑 해제 권한이 제공 됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal를 사용 하 여 Azure database For PostgreSQL 단일 서버에 대해 고객이 관리](howto-data-encryption-portal.md)하는 키를 사용 하 여 데이터 암호화를 설정 하는 방법에 대해 알아봅니다.

