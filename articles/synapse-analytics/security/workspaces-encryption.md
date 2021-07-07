---
title: Azure Synapse Analytics 암호화
description: Azure Synapse Analytics의 암호화를 설명 하는 문서
author: nanditavalsan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 11/19/2020
ms.author: nanditav
ms.reviewer: jrasnick
ms.openlocfilehash: d4bc59a9bd5299698bff9949aaaa881fbdf385ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100526275"
---
# <a name="encryption-for-azure-synapse-analytics-workspaces"></a>Azure Synapse Analytics 작업 영역에 대한 암호화

이 문서는 다음을 설명합니다.
* Synapse Analytics 작업 영역에서 미사용 데이터의 암호화.
* 고객 관리 키를 사용 하 여 암호화를 사용 하도록 설정 하는 Synapse 작업 영역 구성
* 작업 영역에서 데이터를 암호화 하는 데 사용 되는 키 관리

## <a name="encryption-of-data-at-rest"></a>저장소 데이터 암호화

전체 암호화 솔루션은 데이터를 암호화 되지 않은 형식으로 유지 하지 않도록 합니다. 휴지 상태의 데이터에 대 한 이중 암호화는 단일 계층의 손상 으로부터 보호 하기 위해 두 개의 개별 암호화 계층으로 위협을 완화 합니다. Azure Synapse Analytics는 고객 관리 키를 사용 하 여 작업 영역에서 데이터에 대 한 두 번째 암호화 계층을 제공 합니다. 이 키는 키 관리 및 회전의 소유권을 가져올 수 있는 [Azure Key Vault](../../key-vault/general/overview.md)에 보호 됩니다.

Azure 서비스에 대 한 첫 번째 암호화 계층은 플랫폼 관리 키를 사용 하 여 사용 하도록 설정 됩니다. 기본적으로 Azure Storage 계정의 Azure 디스크 및 데이터는 미사용 시 자동으로 암호화 됩니다. [Azure 암호화 개요](../../security/fundamentals/encryption-overview.md)의 Microsoft Azure에서 암호화를 사용 하는 방법에 대해 자세히 알아보세요.

## <a name="azure-synapse-encryption"></a>Azure Synapse 암호화

이 섹션에서는 Synapse 작업 영역에서 고객 관리 키 암호화를 사용 하도록 설정 하 고 적용 하는 방법을 더 잘 이해 하는 데 도움이 됩니다. 이 암호화는 기존 키 또는 Azure Key Vault에서 생성 된 새 키를 사용 합니다. 단일 키를 사용 하 여 작업 영역에서 모든 데이터를 암호화 합니다. Synapse 작업 영역은 2048 및 3072 바이트 크기의 키를 사용 하는 RSA 키를 지원 합니다.

> [!NOTE]
> Synapse 작업 영역은 암호화에 ECC (Elliptic-Curve 암호화) 키를 사용 하는 것을 지원 하지 않습니다.

다음 Synapse 구성 요소의 데이터는 작업 영역 수준에서 구성 된 고객 관리 키를 사용 하 여 암호화 됩니다.
* SQL 풀
 * 전용 SQL 풀
 * 서버를 사용 하지 않는 SQL 풀
* Apache Spark 풀
* 통합 런타임, 파이프라인, 데이터 집합을 Azure Data Factory 합니다.

## <a name="workspace-encryption-configuration"></a>작업 영역 암호화 구성

작업 영역을 만들 때 고객 관리 키를 사용 하 여 이중 암호화를 사용 하도록 작업 영역을 구성할 수 있습니다. 새 작업 영역을 만들 때 "보안" 탭에서 "고객이 관리 하는 키를 사용 하 여 이중 암호화 사용" 옵션을 선택 합니다. 키 식별자 URI를 입력 하거나 작업 영역과 **동일한 지역** 에 있는 키 자격 증명 모음 목록에서 선택할 수 있습니다. Key Vault 자체는 **제거 보호를 사용 하도록 설정** 해야 합니다.

> [!IMPORTANT]
> 작업 영역을 만든 후에는 이중 암호화에 대 한 구성 설정을 변경할 수 없습니다.

:::image type="content" source="./media/workspaces-encryption/workspaces-encryption.png" alt-text="이 다이어그램에서는 고객이 관리 하는 키를 사용 하 여 이중 암호화를 위해 작업 영역을 사용 하도록 설정 하기 위해 선택 해야 하는 옵션을 보여 줍니다.":::

### <a name="key-access-and-workspace-activation"></a>키 액세스 및 작업 영역 활성화

고객 관리 키를 사용 하는 Azure Synapse encryption 모델은 필요에 따라 암호화 하 고 암호 해독 하는 Azure Key Vault의 키에 액세스 하는 작업 영역을 포함 합니다. 액세스 정책 또는 [AZURE KEY VAULT RBAC 액세스](../../key-vault/general/rbac-guide.md)를 통해 작업 영역에서 키에 액세스할 수 있습니다. Azure Key Vault 액세스 정책을 통해 권한을 부여 하는 경우 정책 생성 중에 ["응용 프로그램 전용"](../../key-vault/general/secure-your-key-vault.md#key-vault-authentication-options) 옵션을 선택 합니다 (작업 영역의 관리 되는 id를 선택 하 고 권한 있는 응용 프로그램으로 추가 하지 않음).

 작업 영역을 활성화 하려면 먼저 키 자격 증명 모음에 필요한 사용 권한을 작업 영역 관리 id에 부여 해야 합니다. 작업 영역 활성화에 대 한 이러한 단계적 접근 방식은 작업 영역의 데이터가 고객 관리 키로 암호화 되도록 합니다. 전용 SQL 풀에 대해 암호화를 사용 하거나 사용 하지 않도록 설정할 수 있습니다. 각 풀은 기본적으로 암호화를 사용 하도록 설정 되어 있지 않습니다.

#### <a name="permissions"></a>권한

미사용 데이터를 암호화 하거나 암호를 해독 하려면 작업 영역 관리 id에 다음 사용 권한이 있어야 합니다.
* WrapKey (새 키를 만들 때 Key Vault에 키를 삽입 하는 데 사용할 수 있습니다.)
* UnwrapKey (암호 해독을 위한 키를 가져오기 위해).
* Get (키의 공개 부분을 읽으려면)

#### <a name="workspace-activation"></a>작업 영역 활성화

작업 영역 (이중 암호화를 사용 하는 경우)을 만든 후에는 활성화가 성공할 때까지 "보류 중" 상태로 유지 됩니다. 모든 기능을 완전히 사용 하려면 작업 영역을 활성화 해야 합니다. 예를 들어, 활성화가 성공 하면 새 전용 SQL 풀만 만들 수 있습니다. 작업 영역에 키 자격 증명 모음에 대 한 관리 id 액세스 권한을 부여 하 고 작업 영역 Azure Portal 배너에서 활성화 링크를 클릭 합니다. 활성화가 성공적으로 완료 되 면 작업 영역을 사용 하 여 고객 관리 키로 보호 되는 모든 데이터를 보호할 수 있습니다. 앞에서 설명한 것 처럼 인증을 성공적으로 수행 하려면 키 자격 증명 모음에 제거 보호를 설정 해야 합니다.

:::image type="content" source="./media/workspaces-encryption/workspace-activation.png" alt-text="이 다이어그램에서는 작업 영역에 대 한 활성화 링크를 사용 하 여 배너를 표시 합니다.":::


### <a name="manage-the-workspace-customer-managed-key"></a>고객 관리 키 작업 영역 관리 

Azure Portal의 **암호화** 페이지에서 데이터를 암호화 하는 데 사용 되는 고객 관리 키를 변경할 수 있습니다. 여기서는 키 식별자를 사용 하 여 새 키를 선택 하거나, 작업 영역과 동일한 지역에서 액세스 권한이 있는 Key vault에서 선택할 수 있습니다. 이전에 사용한 것과 다른 키 자격 증명 모음에서 키를 선택 하는 경우 새 키 자격 증명 모음에 작업 영역 관리 id "Get", "Wrap" 및 "래핑 해제" 권한을 부여 합니다. 작업 영역에서 새 키 자격 증명 모음에 대 한 액세스의 유효성을 검사 하 고 작업 영역의 모든 데이터가 새 키로 다시 암호화 됩니다.

:::image type="content" source="./media/workspaces-encryption/workspace-encryption-management.png" alt-text="이 다이어그램에서는 Azure Portal의 작업 영역 암호화 섹션을 보여 줍니다.":::

>[!IMPORTANT]
>작업 영역의 암호화 키를 변경 하는 경우 키를 새 키로 바꿀 때까지 키를 유지 합니다. 이는 새 키로 다시 암호화 되기 전에 이전 키를 사용 하 여 데이터의 암호를 해독할 수 있도록 하기 위한 것입니다.

키의 자동, 주기적 회전 또는 키에 대 한 작업에 대 한 Azure Key vault 정책을 통해 새 키 버전이 생성 될 수 있습니다. 작업 영역의 모든 데이터를 최신 버전의 활성 키로 다시 암호화 하도록 선택할 수 있습니다. 다시 암호화 하려면 Azure Portal의 키를 임시 키로 변경한 다음 암호화에 사용할 키로 다시 전환 합니다. 예를 들어 active key Key1의 최신 버전을 사용 하 여 데이터 암호화를 업데이트 하려면 고객 관리 키 작업 영역을 임시 키, Key2로 변경 합니다. Key2를 사용 하 여 암호화를 마칠 때까지 기다립니다. 그런 다음, 고객 관리 키 작업 영역을 다시 Key1로 전환 합니다. 작업 영역의 데이터는 최신 버전의 Key1로 다시 암호화 됩니다.

> [!NOTE]
> Azure Synapse Analytics는 새 키 버전이 만들어질 때 자동으로 데이터를 다시 암호화 하는 기능을 추가 하는 작업을 수행 합니다. 이 기능을 사용할 수 있을 때까지 작업 영역에서 일관성을 유지 하기 위해 위에서 설명한 프로세스를 사용 하 여 데이터의 다시 암호화를 적용 합니다.

#### <a name="sql-transparent-data-encryption-with-service-managed-keys"></a>서비스 관리 키를 사용 하는 SQL 투명한 데이터 암호화

TDE (SQL 투명한 데이터 암호화)는 이중 암호화에 대해 사용 하도록 설정 *되지 않은* 작업 영역의 전용 sql 풀에 사용할 수 있습니다. 이러한 유형의 작업 영역에서 서비스 관리 키를 사용 하 여 전용 SQL 풀의 데이터에 이중 암호화를 제공 합니다. 서비스 관리 키를 사용 하는 TDE는 개별 전용 SQL 풀에 대해 사용 하거나 사용 하지 않도록 설정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[기본 제공 Azure 정책을 사용 하 여 Synapse 작업 영역에 대 한 암호화 보호 구현](../policy-reference.md)

