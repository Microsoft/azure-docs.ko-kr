---
title: 고객 관리형 키를 사용하여 데이터 암호화 - Azure Database for PostgreSQL - Single 서버
description: 고객 관리형 키를 사용하여 Azure Database for PostgreSQL Single 서버 데이터를 암호화하면 저장 데이터 보호에 BYOK(Bring Your Own Key)를 적용할 수 있습니다. 또한 조직이 키 및 데이터 관리에서 업무 분리를 구현할 수도 있습니다.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/13/2020
ms.openlocfilehash: 66faa2b3e6d24c264e2fe26ab42eeaffd48384f6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732839"
---
# <a name="azure-database-for-postgresql-single-server-data-encryption-with-a-customer-managed-key"></a>고객 관리형 키를 사용하여 Azure Database for PostgreSQL Single 서버 데이터 암호화

Azure PostgreSQL는 기본적으로 Microsoft 관리 키를 사용 하 여 미사용 데이터를 암호화 하는 [Azure Storage 암호화](../storage/common/storage-service-encryption.md) 를 활용 합니다. Azure PostgreSQL 사용자의 경우 SQL Server와 같은 다른 데이터베이스의 TDE (투명한 데이터 암호화)와 매우 비슷합니다. 많은 조직에서 고객이 관리 하는 키를 사용 하 여 데이터에 대 한 액세스를 완벽 하 게 제어 해야 합니다. Azure Database for PostgreSQL Single 서버 데이터에 고객 관리형 키를 사용한 암호화를 적용하면 저장 데이터 보호에 BYOK(Bring Your Own Key)를 사용할 수 있습니다. 또한 조직이 키 및 데이터 관리에서 업무 분리를 구현할 수도 있습니다. 고객 관리 암호화를 사용하여 사용자가 키 수명 주기, 키 사용 권한 및 키 작업 감사를 담당합니다.

고객 관리형 키를 사용한 Azure Database for PostgreSQL Single 서버 데이터의 암호화는 서버 수준에서 설정됩니다. 특정 서버에 대해 KEK(키 암호화 키)라고 하는 고객 관리형 키를 사용하여 서비스에 사용되는 DEK(데이터 암호화 키)를 암호화합니다. KEK는 고객이 소유하고 관리하는 [Azure Key Vault](../key-vault/general/secure-your-key-vault.md) 인스턴스에 저장되는 비대칭 키입니다. KEK(키 암호화 키) 및 DEK(데이터 암호화 키)에 대한 자세한 내용은 이 문서의 뒷부분에서 설명합니다.

Key Vault는 클라우드 기반의 외부 키 관리 시스템입니다. 가용성이 우수하며 RSA 암호화 키에 대한 안전하고 확장성 있는 스토리지를 제공합니다. FIPS 140-2 수준 2 유효성 검사를 통과한 HSM(하드웨어 보안 모듈)을 선택 사항으로 지원합니다. 저장된 키에 직접 액세스할 수 없지만, 권한 있는 엔터티에 암호화 및 암호 해독 서비스를 제공합니다. Key Vault는 키를 생성하고, 가져오고, [온-프레미스 HSM 디바이스에서 전송](../key-vault/keys/hsm-protected-keys.md)되도록 할 수 있습니다.

> [!NOTE]
> Azure Database for PostgreSQL Single 서버가 "범용" 및 "메모리 최적화" 가격 책정 계층을 지원하는 모든 Azure 지역에서 이 기능을 사용할 수 있습니다. 기타 제한 사항에 대해서는 [제한](concepts-data-encryption-postgresql.md#limitations) 사항 섹션을 참조 하세요.

## <a name="benefits"></a>이점

단일 서버 Azure Database for PostgreSQL 고객 관리 키를 사용 하 여 데이터를 암호화 하면 다음과 같은 이점이 있습니다.

* 키를 제거하고 데이터베이스 액세스를 차단하는 기능을 통해 데이터 액세스를 사용자가 완벽하게 제어 
*    회사 정책에 맞게 키를 회전하는 기능을 포함하여 키 수명 주기를 완벽하게 제어
*    Azure Key Vault의 키를 중앙에서 관리 및 구성
*    보안 책임자와 DBA 및 시스템 관리자의 책임을 분리하는 기능

## <a name="terminology-and-description"></a>용어 및 설명

**DEK(데이터 암호화 키)** : 데이터 파티션 또는 블록을 암호화하는 데 사용되는 대칭 AES256 키입니다. 서로 다른 키로 각 데이터 블록을 암호화하면 암호 분석 공격이 더욱 어려워집니다. DEK에 대한 액세스는 특정 블록을 암호화하고 암호 해독하는 리소스 공급자 또는 애플리케이션 인스턴스에 필요합니다. DEK를 새 키로 바꾸면 연결된 블록의 데이터만 새 키로 다시 암호화해야 합니다.

**KEK(키 암호화 키)** : DEK를 암호화하는 데 사용되는 암호화 키입니다. Key Vault를 절대 벗어나지 않는 KEK를 사용하면 DEK 자체를 암호화하고 제어할 수 있습니다. KEK에 액세스할 수 있는 엔터티와 DEK가 필요한 엔터티가 서로 다를 수 있습니다. KEK는 DEK를 암호 해독하는 데 필요하므로 KEK는 실질적으로 KEK를 삭제함으로써 DEK를 효과적으로 삭제할 수 있는 단일 지점이 됩니다.

KEK로 암호화된 DEK는 별도로 저장됩니다. KEK에 대한 액세스 권한이 있는 엔터티만 이러한 DEK를 암호 해독할 수 있습니다. 자세한 내용은 [저장 데이터 암호화의 보안](../security/fundamentals/encryption-atrest.md)을 참조하세요.

## <a name="how-data-encryption-with-a-customer-managed-key-work"></a>고객 관리형 키를 사용하는 데이터 암호화의 작동 원리

:::image type="content" source="media/concepts-data-access-and-security-data-encryption/postgresql-data-encryption-overview.png" alt-text="Bring Your Own Key의 개요를 보여주는 다이어그램":::

PostgreSQL 서버에서 Key Vault에 저장된 고객 관리형 키를 DEK 암호화에 사용하려면 Key Vault 관리자는 서버에 대한 다음 액세스 권한을 제공해야 합니다.

* **get**: 키 자격 증명 모음에 저장된 키의 공개 파트와 속성을 검색합니다.
* **wrapKey**: DEK를 암호화할 수 있습니다. 암호화 된 DEK는 Azure Database for PostgreSQL에 저장 됩니다.
* **unwrapKey**: DEK를 암호 해독할 수 있습니다. 데이터를 암호화/암호 해독 하려면 암호 해독 된 DEK가 필요 Azure Database for PostgreSQL

또한 키 자격 증명 모음 관리자는 [Key Vault 감사 이벤트 로깅을 사용하도록 설정](../azure-monitor/insights/key-vault-insights-overview.md)하여 나중에 감사할 수도 있습니다.

키 자격 증명 모음에 저장된 고객 관리형 키를 사용하도록 구성된 서버는 암호화를 위해 DEK를 키 자격 증명 모음으로 보냅니다. Key Vault는 암호화된 DEK를 반환하고, 암호화된 DEK는 사용자 데이터베이스에 저장됩니다. 마찬가지로, 서버는 암호화된 DEK를 암호 해독해야 하는 상황이 되면 암호화된 DEK를 키 자격 증명 모음으로 보냅니다. 로깅 기능이 사용되는 경우 감사자는 Azure Monitor을 사용하여 Key Vault 감사 이벤트 로그를 검토할 수 있습니다.

## <a name="requirements-for-configuring-data-encryption-for-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL Single 서버 데이터 암호화를 구성하기 위한 요구 사항

Key Vault를 구성하기 위한 요구 사항은 다음과 같습니다.

* Key Vault와 Azure Database for PostgreSQL Single 서버가 동일한 Azure AD(Azure Active Directory) 테넌트에 속해야 합니다. 테넌트 간 Key Vault 및 서버 상호 작용은 지원되지 않습니다. 나중에 Key Vault 리소스를 이동 하려면 데이터 암호화를 다시 구성 해야 합니다.
* 키 자격 증명 모음은 ' 삭제 된 자격 증명 모음 보존 기간 (일) 동안 90 일로 설정 해야 합니다. 기존 키 자격 증명 모음이 더 낮은 숫자로 구성 된 경우 새 키 자격 증명 모음을 만든 후 수정할 수 없으므로 해당 자격 증명 모음을 만들어야 합니다.
* 실수로 인한 키(또는 Key Vault) 삭제 시 데이터가 손실되지 않도록 키 자격 증명 모음에서 일시 삭제 기능을 사용하도록 설정해야 합니다. 일시 삭제된 리소스는 사용자가 직접 복구하거나 제거하지 않는 한 보존 기간 동안 유지되며, 보존 기간은 90일입니다. 복구 및 제거 작업에는 키 자격 증명 모음 액세스 정책과 연결된 고유 권한이 있습니다. 일시 삭제 기능은 기본적으로 해제되어 있지만, PowerShell 또는 Azure CLI를 통해 사용하도록 설정할 수 있습니다(Azure Portal에서는 사용하도록 설정할 수 없음). 
* 제거 방지를 사용 하 여 삭제 된 자격 증명 모음 및 자격 증명 모음 개체에 대 한 필수 보존 기간 적용
* 고유한 관리 ID를 사용하여 get, wrapKey 및 unwrapKey 권한이 있는 키 자격 증명 모음에 대한 액세스 권한을 Azure Database for PostgreSQL Single 서버에 부여합니다. Azure Portal PostgreSQL 단일 서버에서 데이터 암호화를 사용 하도록 설정 하면 고유한 ' Service ' id가 자동으로 생성 됩니다. Azure Portal 사용 시 자세한 단계별 지침은 [Azure Portal을 사용하여 Azure Database for PostgreSQL Single 서버 데이터 암호화](howto-data-encryption-portal.md)를 참조하세요.

다음은 고객 관리형 키를 구성하기 위한 요구 사항입니다.

* DEK를 암호화하는 데 사용되는 고객 관리형 키는 비대칭 RSA 2048만 가능합니다.
* 키 활성화 날짜(설정된 경우)는 과거의 날짜와 시간이어야 합니다. 만료 날짜(설정된 경우)는 미래의 날짜와 시간이어야 합니다.
* 키가 *사용* 상태여야 합니다.
* 키 자격 증명 모음에 [기존 키를 가져오는](/rest/api/keyvault/ImportKey/ImportKey) 경우 지원 되는 파일 형식 ( `.pfx` ,,)에 제공 해야 `.byok` `.backup` 합니다.

## <a name="recommendations"></a>권장 사항

고객 관리형 키를 사용하여 데이터를 암호화할 때에는 다음과 같이 Key Vault를 구성하는 것이 좋습니다.

* 중요한 리소스를 삭제할 수 있는 사용자를 제어하고 실수로 인한 삭제나 무단 삭제를 방지할 수 있도록 Key Vault에 리소스 잠금을 설정합니다.
* 모든 암호화 키에서 감사 및 보고를 사용합니다. Key Vault는 다른 보안 정보 및 이벤트 관리 도구에 쉽게 삽입할 수 있는 로그를 제공합니다. Azure Monitor Log Analytics는 이미 통합된 서비스의 한 가지 예입니다.
* DEK 래핑 및 래핑 해제 작업에 더 빠르게 액세스할 수 있도록 Key Vault와 Azure Database for PostgreSQL Single 서버가 동일한 지역에 있어야 합니다.
* Azure KeyVault를 **프라이빗 엔드포인트 및 선택한 네트워크** 로 잠가서 *신뢰할 수 있는 Microsoft* 서비스만 허용하여 리소스를 보호합니다.

    :::image type="content" source="media/concepts-data-access-and-security-data-encryption/keyvault-trusted-service.png" alt-text="trusted-service-with-AKV":::

고객 관리형 키를 다음과 같이 구성하는 것이 좋습니다.

* 고객 관리형 키의 복사본을 안전한 장소에 보관하거나 에스크로 서비스로 에스크로합니다.

* Key Vault에서 키를 생성하면 키를 처음으로 사용하기 전에 키 백업을 만듭니다. 백업을 Key Vault로 복원하는 것만 가능합니다. 백업 명령에 대한 자세한 내용은 [Backup-AzKeyVaultKey](/powershell/module/az.keyVault/backup-azkeyVaultkey)를 참조하세요.

## <a name="inaccessible-customer-managed-key-condition"></a>고객 관리형 키에 액세스할 수 없는 조건

Key Vault에서 고객 관리형 키를 사용하여 데이터 암호화를 구성할 때, 서버가 온라인 상태를 유지하려면 이 키에 대한 지속적인 액세스가 필요합니다. 서버가 Key Vault에서 고객 관리형 키에 대한 액세스 권한을 상실하면 서버는 10분 이내에 모든 연결을 거부하기 시작합니다. 서버에서 해당 오류 메시지를 발행하고, 서버 상태를 *액세스할 수 없음* 으로 변경합니다. 서버가 이 상태에 도달할 수 있는 몇 가지 이유는 다음과 같습니다.

* 데이터 암호화를 사용하는 Azure Database for PostgreSQL Single 서버에 대한 특정 시점 복원 서버를 만들면 새로 만든 서버는 *액세스할 수 없음* 상태가 됩니다. [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) 또는 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)를 통해 서버 상태를 수정할 수 있습니다.
* 데이터 암호화를 사용하는 Azure Database for PostgreSQL Single 서버에 대한 읽기 복제본을 만들면 복제본 서버는 *액세스할 수 없음* 상태가 됩니다. [Azure Portal](howto-data-encryption-portal.md#using-data-encryption-for-restore-or-replica-servers) 또는 [CLI](howto-data-encryption-cli.md#using-data-encryption-for-restore-or-replica-servers)를 통해 서버 상태를 수정할 수 있습니다.
* KeyVault를 삭제하면 Azure Database for PostgreSQL Single 서버는 키에 액세스할 수 없게 되고 *액세스할 수 없음* 상태로 변경됩니다. 서버를 *사용 가능* 상태로 만들려면 [Key Vault](../key-vault/general/key-vault-recovery.md)를 복구하고 데이터 암호화의 유효성을 다시 검사하세요.
* KeyVault에서 키를 삭제하면 Azure Database for PostgreSQL Single 서버는 키에 액세스할 수 없게 되고 *액세스할 수 없음* 상태로 변경됩니다. 서버를 *사용 가능* 상태로 만들려면 [키](../key-vault/general/key-vault-recovery.md)를 복구하고 데이터 암호화의 유효성을 다시 검사하세요.
* Azure KeyVault에 저장된 키가 만료되면 키가 무효화되고 Azure Database for PostgreSQL Single 서버가 *액세스할 수 없음* 상태로 전환됩니다. [CLI](/cli/azure/keyvault/key#az-keyvault-key-set-attributes)를 사용하여 키 만료 날짜를 연장한 다음, 데이터 암호화의 유효성을 다시 검사하여 서버를 *사용 가능* 상태로 만드세요.

### <a name="accidental-key-access-revocation-from-key-vault"></a>Key Vault에서 실수로 인한 키 액세스 해지

Key Vault에 대한 충분한 액세스 권한을 가진 사용자가 다음과 같은 실수를 저질러서 키에 대한 서버 액세스 권한을 해제하는 사건이 발생할 수 있습니다.

* 서버에서 키 자격 증명 모음의 get, wrapKey 및 unwrapKey 권한을 해지합니다.
* 키를 삭제합니다.
* 키 자격 증명 모음을 삭제합니다.
* 키 자격 증명 모음의 방화벽 규칙을 변경합니다.

* Azure AD에서 서버의 관리 ID를 삭제합니다.

## <a name="monitor-the-customer-managed-key-in-key-vault"></a>Key Vault에서 고객 관리형 키 모니터링

데이터베이스 상태를 모니터링하고 투명한 데이터 암호화 보호기 액세스 권한 손실을 경고하도록 설정하려면 다음 Azure 기능을 구성합니다.

* [Azure Resource Health](../service-health/resource-health-overview.md): 고객 키에 대한 액세스 권한이 손실된 액세스 불가능 데이터베이스는 데이터베이스에 대한 첫 번째 연결이 거부된 후 "액세스할 수 없음"으로 표시됩니다.
* [활동 로그](../service-health/alerts-activity-log-service-notifications-portal.md): 고객 관리형 Key Vault의 고객 키에 대한 액세스가 실패하면 항목이 활동 로그에 추가됩니다. 이러한 이벤트에 대한 경고를 생성하면 최대한 신속하게 액세스를 복구할 수 있습니다.

* [작업 그룹](../azure-monitor/alerts/action-groups.md): 기본 설정에 따라 알림과 경고를 보낼 작업 그룹을 정의합니다.

## <a name="restore-and-replicate-with-a-customers-managed-key-in-key-vault"></a>Key Vault에서 고객 관리형 키를 사용하여 복원 및 복제

Key Vault에 저장된 고객 관리형 키를 사용하여 Azure Database for PostgreSQL Single 서버를 암호화한 후에는 새로 만든 서버 복사본도 암호화됩니다. 로컬/지역 복원 작업을 통해 또는 읽기 복제본을 통해 새 복사본을 만들 수 있습니다. 그러나 새 고객이 암호화에 사용하는 관리형 키를 반영하도록 복사본을 변경할 수 있습니다. 고객 관리형 키가 변경되면 서버의 이전 백업에서 최신 키를 사용하기 시작합니다.

복원 또는 복제본을 만드는 동안 고객이 관리 하는 데이터 암호화를 설정 하는 동안 문제를 방지 하려면 기본 및 복원/복제 서버에서 다음 단계를 수행 하는 것이 중요 합니다.

* 주 Azure Database for PostgreSQL 단일 서버에서 복원 또는 읽기 복제본 만들기 프로세스를 시작 합니다.
* 새로 만든 서버(복원된 서버/복제본)의 고유 ID에 아직 Key Vault에 대한 권한이 부여되지 않았으므로 액세스할 수 없음 상태로 유지합니다.
* 복원된/복제본 서버의 데이터 암호화 설정에서 고객 관리형 키의 유효성을 다시 검사합니다. 이렇게 하면 Key Vault에 저장된 키에 대한 래핑 및 래핑 해제 권한이 새로 만든 서버에 부여됩니다.

## <a name="limitations"></a>제한 사항

Azure Database for PostgreSQL의 경우 CMK (customers 관리 키)를 사용 하 여 저장소 데이터 암호화에 대 한 지원에는 몇 가지 제한 사항이 있습니다.

* 이 기능에 대 한 지원은 **범용** 및 메모리 액세스에 **최적화** 된 가격 책정 계층으로 제한 됩니다.
* 이 기능은 최대 16TB의 스토리지를 지원하는 지역 및 서버에서만 지원됩니다. 최대 16TB의 저장소를 지 원하는 Azure 지역 목록은 [여기](concepts-pricing-tiers.md#storage) 설명서의 저장소 섹션을 참조 하세요.

    > [!NOTE]
    > - 위에 나열 된 지역에서 만든 모든 새 PostgreSQL 서버를 **사용할 수** 있습니다. PITR (특정 시점 복원) 서버 또는 읽기 복제본은 이론적으로는 ' 신규 '입니다.
    > - 프로 비전 된 서버에서 최대 16TB를 지원 하는지 확인 하기 위해 포털의 가격 책정 계층 블레이드로 이동 하 여 프로 비전 된 서버에서 지 원하는 최대 저장소 크기를 확인할 수 있습니다. 슬라이더를 최대 4TB까지 이동할 수 있는 경우 서버에서 고객 관리 키를 사용 하 여 암호화를 지원 하지 않을 수 있습니다. 그러나 데이터는 서비스 관리 키를 사용 하 여 항상 암호화 됩니다. AskAzureDBforPostgreSQL@service.microsoft.com궁금한 점이 있으면에 문의 하세요.

* 암호화는 RSA 2048 암호화 키 에서만 지원 됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Portal에서 고객 관리형 키를 사용하여 Azure Database for PostgreSQL Single 서버에 대한 데이터 암호화를 설정](howto-data-encryption-portal.md)하는 방법을 알아보세요.
