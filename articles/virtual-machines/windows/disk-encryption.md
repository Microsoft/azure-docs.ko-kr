---
title: Azure Managed Disks의 서버 쪽 암호화 - PowerShell
description: Azure Storage는 미사용 데이터를 암호화한 후 저장소 클러스터에 보관하여 데이터를 보호합니다. Microsoft 관리형 키를 사용하여 사용자의 관리 디스크를 암호화하거나, 고객 관리형 키를 사용하여 사용자 고유의 키로 암호화를 관리할 수 있습니다.
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6174fbeb45c23c0ff04597305c6f65aef05bd26e
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88815592"
---
# <a name="server-side-encryption-of-azure-disk-storage-for-powershell"></a>PowerShell에 대 한 Azure 디스크 저장소의 서버 쪽 암호화

SSE(서버 쪽 암호화)는 데이터를 보호하고 조직의 보안 및 규정 준수 노력에 부합하는 데 도움이 됩니다. SSE는 기본적으로 Azure 관리 디스크 (OS 및 데이터 디스크)에 저장 된 데이터를 클라우드에 유지할 때 자동으로 암호화 합니다. 

Azure Managed Disks의 데이터는 사용 가능한 가장 강력한 암호 중 하나인 256비트 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)를 사용하여 투명하게 암호화되며 FIPS 140-2 규격입니다. 암호화 모듈의 기본 Azure Managed Disks에 대한 자세한 정보는 [Cryptography API: Next Generation](/windows/desktop/seccng/cng-portal)을 참조하세요.

서버 쪽 암호화는 관리 디스크의 성능에 영향을 주지 않으며 추가 비용이 없습니다. 

> [!NOTE]
> 임시 디스크는 관리 디스크가 아니므로 호스트에서 암호화를 사용 하도록 설정 하지 않으면 SSE로 암호화 되지 않습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

플랫폼 관리형 키를 사용하여 관리 디스크를 암호화하거나 사용자 고유의 키를 사용하여 암호화를 관리할 수 있습니다. 사용자 고유의 키를 사용하여 암호화를 관리하는 경우 관리 디스크의 모든 데이터를 암호화 및 암호 해독하는 데 사용할 *고객 관리형 키*를 지정할 수 있습니다. 

다음 섹션에서는 키 관리에 대한 각 옵션을 자세히 설명합니다.

### <a name="platform-managed-keys"></a>플랫폼 관리형 키

기본적으로 관리 디스크는 플랫폼 관리형 암호화 키를 사용합니다. 기존 관리 디스크에 작성 된 모든 관리 디스크, 스냅숏, 이미지 및 데이터는 플랫폼 관리 키를 사용 하 여 미사용 상태로 자동으로 암호화 됩니다.

### <a name="customer-managed-keys"></a>고객 관리형 키

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>제한

현재, 고객 관리형 키에는 다음과 같은 제한 사항이 있습니다.

- 디스크에 대해 이 기능을 사용하는 경우에는 사용하지 않도록 설정할 수 없습니다.
    이 문제를 해결해야 하는 경우 고객 관리형 키를 사용하지 않는 완전히 다른 관리 디스크로 [모든 데이터를 복사](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)해야 합니다.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>호스트의 암호화-VM 데이터의 종단 간 암호화

종단 간 암호화는 vm이 할당 되는 Azure 서버인 VM 호스트에서 시작 됩니다. 임시 디스크의 데이터, 임시 OS 디스크 및 지속형 OS/데이터 디스크 캐시가 해당 VM 호스트에 저장 됩니다. 종단 간 암호화를 사용 하도록 설정 하면이 모든 데이터가 미사용 및 저장소 서비스로 암호화 된 흐름으로 암호화 됩니다. 종단 간 암호화는 VM의 CPU를 사용 하지 않으며 VM의 성능에 영향을 주지 않습니다. 

종단 간 암호화를 사용 하는 경우 임시 디스크와 사용 후 삭제 OS 디스크는 플랫폼 관리 키를 사용 하 여 미사용에 암호화 됩니다. OS 및 데이터 디스크 캐시는 암호화 유형에 따라 고객이 관리 하거나 플랫폼에서 관리 하는 키를 사용 하 여 미사용으로 암호화 됩니다. 예를 들어, 디스크가 고객 관리 키를 사용 하 여 암호화 된 경우 디스크의 캐시는 고객 관리 키를 사용 하 여 암호화 되 고, 디스크는 플랫폼 관리 키를 사용 하 여 암호화 되는 경우 디스크에 대 한 캐시는 플랫폼 관리 키를 사용 하 여 암호화 됩니다.

### <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>지원되는 지역

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>지원되는 VM 크기

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>휴지 상태의 이중 암호화

공격 받는 특정 암호화 알고리즘, 구현 또는 키와 관련 된 위험을 염려 하는 높은 수준의 보안 민감한 고객은 이제 플랫폼 관리 암호화 키를 사용 하 여 인프라 계층에서 다른 암호화 알고리즘/모드를 사용 하 여 추가 암호화 계층을 선택할 수 있습니다. 이 새 계층은 지속형 OS와 데이터 디스크, 스냅숏 및 이미지에 적용할 수 있으며,이는 모두 이중 암호화를 사용 하 여 미사용에서 암호화 됩니다.

### <a name="supported-regions"></a>지원되는 지역

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>서버 쪽 암호화 및 Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md)은 Windows의 [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-overview) 기능을 활용하여 게스트 VM 내에서 고객 관리 키를 사용하여 Managed Disks를 암호화합니다. 고객 관리형 키를 사용하는 서버 쪽 암호화는 스토리지 서비스의 데이터를 암호화하여 VM에 대한 모든 OS 유형 및 이미지를 사용할 수 있도록 설정하여 ADE에서 향상됩니다.

> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동하는 경우, 관리 디스크와 연결된 관리 ID는 새로운 테넌트로 전송되지 않으므로 고객 관리형 키가 더 이상 작동하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [PowerShell](disks-enable-host-based-encryption-powershell.md) 또는 [Azure Portal](../disks-enable-host-based-encryption-portal.md)에서 호스트의 암호화를 사용 하 여 종단 간 암호화를 사용 하도록 설정 합니다.
- [PowerShell](disks-enable-double-encryption-at-rest-powershell.md) 또는 [Azure Portal](../disks-enable-double-encryption-at-rest-portal.md)를 사용 하 여 관리 디스크에 대해 미사용에서 이중 암호화를 사용 하도록 설정 합니다.
- [PowerShell](disks-enable-customer-managed-keys-powershell.md) 또는 [Azure Portal](../disks-enable-customer-managed-keys-portal.md)를 사용 하 여 관리 디스크에 대해 고객이 관리 하는 키를 사용 하도록 설정 합니다.
- [고객 관리형 키를 사용하여 암호화된 디스크를 만들기 위한 Azure Resource Manager 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault란?](../../key-vault/general/overview.md)
