---
title: Azure Portal-SSE 관리 디스크를 사용 하 여 고객 관리 키를 사용 하도록 설정
description: Azure Portal를 통해 관리 디스크에서 고객이 관리 하는 키를 사용 하도록 설정 합니다.
author: roygara
ms.date: 08/24/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 1b40eef0a4cae8fa68631426ff72003db43d7530
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817621"
---
# <a name="use-the-azure-portal-to-enable-server-side-encryption-with-customer-managed-keys-for-managed-disks"></a>Azure Portal를 사용 하 여 관리 디스크에 대해 고객이 관리 하는 키를 사용 하 여 서버 쪽 암호화를 사용 하도록 설정 합니다.

Azure 디스크 저장소를 사용 하면 관리 디스크에 대해 SSE (서버 쪽 암호화)를 사용 하는 경우 (선택 하는 경우) 사용자 고유의 키를 관리할 수 있습니다. 고객 관리 키 및 기타 관리 되는 디스크 암호화 유형에 대 한 SSE에 대 한 개념 정보는 디스크 암호화 문서의 **고객 관리 키** 섹션을 참조 하세요.

- Linux: [고객이 관리 하는 키](./linux/disk-encryption.md#customer-managed-keys)
- Windows: [고객 관리 키](./windows/disk-encryption.md#customer-managed-keys)

## <a name="restrictions"></a>제한

현재, 고객 관리형 키에는 다음과 같은 제한 사항이 있습니다.

- 디스크에 대해 이 기능을 사용하는 경우에는 사용하지 않도록 설정할 수 없습니다.
    이 문제를 해결 해야 하는 경우 고객 관리 키를 사용 하지 않는 완전히 다른 관리 디스크에 모든 데이터를 복사 해야 합니다.

    - Linux: [관리 디스크 복사](./linux/disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk)

    - Windows: [관리 디스크 복사](./windows/disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk)

- 2048 비트, 3072 비트 및 4096 비트 크기의 [소프트웨어 및 HSM RSA 키](../key-vault/keys/about-keys.md) 만 지원 되며 다른 키 또는 크기는 지원 되지 않습니다.
    - [HSM](../key-vault/keys/hsm-protected-keys.md) 키에는 Azure 주요 자격 증명 모음의 **프리미엄** 계층이 필요 합니다.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

다음 섹션에서는 관리 디스크에 대해 고객 관리 키를 사용 하도록 설정 하 고 사용 하는 방법을 설명 합니다.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM 배포

이제 키 자격 증명 모음 및 디스크 암호화 집합을 만들고 설정 했으므로 암호화를 사용 하 여 VM을 배포할 수 있습니다.
VM 배포 프로세스는 표준 배포 프로세스와 유사 합니다. 유일한 차이점은 다른 리소스와 동일한 지역에 VM을 배포 하 고 고객 관리 키를 사용 하도록 선택 하는 것입니다.

1. **Virtual Machines** 를 검색 하 고 **+ 추가** 를 선택 하 여 VM을 만듭니다.
1. **기본** 블레이드에서 디스크 암호화 집합과 동일한 지역을 선택 하 고 Azure Key Vault 합니다.
1. **기본** 블레이드에서 원하는 다른 값을 입력 합니다.

    ![지역 값이 강조 표시 된 VM 만들기 환경의 스크린샷](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-a-vm-region.png)

1. **디스크** 블레이드에서 **고객이 관리 하는 키를 사용 하 여 미사용 암호화**를 선택 합니다.
1. **디스크 암호화 집합** 드롭다운에서 설정 된 디스크 암호화를 선택 합니다.
1. 원하는 대로 나머지 항목을 선택 합니다.

    ![VM 만들기 환경의 스크린샷, 디스크 블레이드. 디스크 암호화 설정 드롭다운이 강조 표시 됩니다.](media/virtual-machines-disk-encryption-portal/server-side-encryption-create-vm-select-customer-managed-key-disk-encryption-set.png)

## <a name="enable-on-an-existing-disk"></a>기존 디스크에서 사용

> [!CAUTION]
> VM에 연결 된 모든 디스크에 대해 디스크 암호화를 사용 하도록 설정 하려면 VM을 중지 해야 합니다.
    
1. 디스크 암호화 집합 중 하 나와 동일한 지역에 있는 VM으로 이동 합니다.
1. VM을 열고 **중지**를 선택 합니다.

    ![Stop 단추가 강조 표시 된 예제 VM에 대 한 기본 오버레이 스크린샷](media/virtual-machines-disk-encryption-portal/server-side-encryption-stop-vm-to-encrypt-disk-fix.png)

1. VM이 중지 되 면 **디스크** 를 선택 하 고 암호화 하려는 디스크를 선택 합니다.

    ![디스크 블레이드가 열려 있는 예제 VM의 스크린샷 OS 디스크는 선택할 수 있는 예제 디스크로 강조 표시 됩니다.](media/virtual-machines-disk-encryption-portal/server-side-encryption-existing-disk-select.png)

1. **암호화** 를 선택 하 고 **고객이 관리 하는 키를 사용 하 여 미사용 암호화** 를 선택한 후 드롭다운 목록에서 설정 된 디스크 암호화를 선택 합니다.
1. **저장**을 선택합니다.

    ![예제 OS 디스크의 스크린샷 암호화 블레이드가 열리고, 고객이 관리 하는 키를 사용 하 여 미사용 암호화를 선택 하 고 Azure Key Vault 예제를 선택 합니다. 이러한 항목을 선택 하면 저장 단추가 선택 됩니다.](media/virtual-machines-disk-encryption-portal/server-side-encryption-encrypt-existing-disk-customer-managed-key.png)

1. 암호화 하려는 VM에 연결 된 다른 모든 디스크에 대해이 프로세스를 반복 합니다.
1. 디스크가 고객이 관리 하는 키로 전환 되 면 암호화 하려는 다른 연결 된 디스크가 없는 경우 VM을 시작할 수 있습니다.

> [!IMPORTANT]
> 고객 관리형 키는 Azure AD(Azure Active Directory)의 기능 중 하나인 Azure 리소스에 대한 관리 ID를 사용합니다. 고객 관리형 키를 구성하는 경우 관리 ID가 내부적으로 리소스에 자동으로 할당됩니다. 이후에 구독, 리소스 그룹 또는 관리 디스크를 Azure AD 디렉터리 간에 이동 하는 경우 관리 디스크와 연결 된 관리 되는 id가 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 정보는 [Azure AD 디렉터리 간에 구독 전송](../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [고객 관리형 키를 사용하여 암호화된 디스크를 만들기 위한 Azure Resource Manager 템플릿 살펴보기](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Azure Key Vault란?](../key-vault/general/overview.md)
- [고객 관리형 키를 사용하도록 설정된 디스크를 사용하여 컴퓨터 복제](../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [PowerShell을 사용하여 Azure로 VMware VM의 재해 복구 설정](../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [PowerShell과 Azure Resource Manager를 사용하여 Hyper-V VM용 Azure에 대한 재해 복구 설정](../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
