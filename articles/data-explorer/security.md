---
title: Azure에서 Azure 데이터 탐색기 클러스터 보호
description: Azure 데이터 탐색기에서 클러스터를 보호 하는 방법에 대해 알아봅니다.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 5f3bceb8398f9837f6f8eaa390def41456daf08d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/19/2020
ms.locfileid: "76271602"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Azure에서 Azure 데이터 탐색기 클러스터 보호

이 문서에서는 클라우드에서 데이터 및 리소스를 보호 하 고 비즈니스의 보안 요구를 충족 하는 데 도움이 되는 Azure 데이터 탐색기의 보안에 대해 소개 합니다. 클러스터를 안전 하 게 유지 하는 것이 중요 합니다. 클러스터 보안에는 보안 액세스 및 저장소를 포함 하는 Azure 기능이 하나 이상 포함 되어 있습니다. 이 문서에서는 클러스터를 안전 하 게 유지 하는 데 도움이 되는 정보를 제공 합니다.

## <a name="managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID

클라우드 응용 프로그램을 빌드할 때 일반적인 문제는 클라우드 서비스를 인증 하기 위한 자격 증명 관리입니다. 자격 증명을 안전하게 보호하는 것이 중요합니다. 자격 증명을 개발자 워크스테이션에 저장 하거나 소스 제어에 체크 인하지 않아야 합니다. Azure Key Vault를 사용하면 자격 증명, 비밀 및 기타 키를 안전하게 저장할 수 있지만, 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다.

Azure 리소스의 Azure Active Directory (Azure AD) 관리 되는 id 기능은이 문제를 해결 합니다. 이 기능은 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명이 필요 없습니다. 이 서비스에 대한 자세한 내용은 [Azure 리소스에 대한 관리 되는 id](/azure/active-directory/managed-identities-azure-resources/overview) 개요 페이지를 참조 하세요.

## <a name="data-encryption"></a>데이터 암호화

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) 는 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호 하 고 보호 합니다. 클러스터의 가상 컴퓨터에 대한 OS 및 데이터 디스크에 대한 볼륨 암호화를 제공 합니다. 또한 Azure Disk Encryption은 디스크 암호화 키와 암호를 제어 하 고 관리할 수 있도록 하 고 VM 디스크의 모든 데이터가 암호화 되도록 하는 [Azure Key Vault](/azure/key-vault/)와 통합 됩니다. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault를 사용 하는 고객 관리 키

기본적으로 데이터는 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 암호화 키에 대한 추가 제어를 위해 고객 관리 키를 제공 하 여 데이터 암호화에 사용할 수 있습니다. 사용자 고유의 키를 사용 하 여 저장소 수준에서 데이터의 암호화를 관리할 수 있습니다. 고객 관리 키는 모든 데이터를 암호화 하 고 해독 하는 데 사용 되는 루트 암호화 키에 대한 액세스를 보호 하 고 제어 하는 데 사용 됩니다. 고객 관리 키를 통해 액세스 제어를 보다 유연 하 게 만들고, 회전 하 고, 사용 하지 않도록 설정 하 고, 취소할 수 있습니다. 데이터를 보호 하는 데 사용 되는 암호화 키를 감사할 수도 있습니다.

Azure Key Vault를 사용 하 여 고객 관리 키를 저장 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장 하거나 Azure Key Vault API를 사용 하 여 키를 생성할 수 있습니다. Azure 데이터 탐색기 클러스터와 Azure Key Vault는 동일한 지역에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault 무엇입니까?](/azure/key-vault/key-vault-overview)를 참조 하세요. 고객 관리 키에 대한 자세한 내용은 [Azure Key Vault를 사용 하 여 고객 관리 키](/azure/storage/common/storage-service-encryption) 를 참조 하세요.

> [!Note]
> 고객 관리 키는 azure AD (Azure Active Directory 기능) 인 Azure 리소스에 대한 관리 되는 id를 사용 합니다. Azure Portal에서 고객이 관리 하는 키를 구성 하려면 클러스터에 대한 **Systemassigned** 관리 id를 구성 해야 합니다.

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에서 고객이 관리 하는 키 저장

클러스터에서 고객이 관리 하는 키를 사용 하도록 설정 하려면 Azure Key Vault을 사용 하 여 키를 저장 합니다. 키 자격 증명 모음에서 **일시 삭제** 및 **제거 안 함** 속성을 모두 사용 하도록 설정 해야 합니다. 키 자격 증명 모음은 클러스터와 동일한 구독에 있어야 합니다. Azure 데이터 탐색기는 Azure 리소스에 대해 관리 되는 id를 사용 하 여 암호화 및 암호 해독 작업을 위해 키 자격 증명 모음에 인증 합니다. 관리 id는 디렉터리 간 시나리오를 지원 하지 않습니다.

#### <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객이 관리 하는 키를 회전할 수 있습니다. 키를 회전 하는 경우 새 키 URI를 사용 하도록 클러스터를 업데이트 해야 합니다. 키를 회전 해도 클러스터에 있는 데이터의 다시 암호화는 트리거되지 않습니다. 

#### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대한 액세스 취소

고객 관리 키에 대한 액세스를 취소 하려면 PowerShell 또는 Azure CLI를 사용 합니다. 자세한 내용은 [Azure Key Vault PowerShell](/powershell/module/az.keyvault/) 또는 [Azure Key Vault CLI](/cli/azure/keyvault)를 참조 하세요. 액세스를 취소 하면 Azure 데이터 탐색기에서 암호화 키에 액세스할 수 없으므로 클러스터의 저장소 수준에 있는 모든 데이터에 대한 액세스가 차단 됩니다.

> [!Note]
> Azure 데이터 탐색기는 고객이 관리 하는 키에 대한 액세스가 해지 되었음을 식별 하 고 캐시 된 데이터를 삭제 하도록 클러스터를 자동으로 일시 중단 합니다. 키에 대한 액세스가 반환 되 면 클러스터를 수동으로 다시 시작 해야 합니다.

## <a name="role-based-access-control"></a>역할 기반 액세스 제어

[RBAC (역할 기반 액세스 제어)](/azure/role-based-access-control/overview)를 사용 하 여 팀 내에서 업무를 분리 하 고 클러스터 사용자에 게 필요한 액세스만 부여할 수 있습니다. 모든 사람에 게 클러스터에 대한 무제한 권한을 부여 하는 대신 특정 작업만 허용할 수 있습니다. [Azure CLI](/azure/role-based-access-control/role-assignments-cli)또는 [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell)를 사용 하 여 [Azure Portal](/azure/role-based-access-control/role-assignments-portal)에서 [데이터베이스에 대한 액세스 제어](/azure/data-explorer/manage-database-permissions) 를 구성할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure 데이터 탐색기 클러스터에 대한 관리 id 구성](managed-identities.md)
* 미사용 암호화를 사용 하도록 설정 하 여 [Azure 데이터 탐색기-포털에서 클러스터를 보호](manage-cluster-security.md) 합니다.
* [Azure Resource Manager 템플릿을 사용 하 여 고객 관리 키 구성](customer-managed-keys-resource-manager.md)
* [을 사용 하 여 고객이 관리 하는 키 구성C#](customer-managed-keys-csharp.md)

