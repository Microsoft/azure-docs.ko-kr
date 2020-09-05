---
title: 호스트 Azure Portal 관리 디스크에서 암호화를 사용 하 여 종단 간 암호화 사용
description: 호스트의 암호화를 사용 하 여 Azure managed disks-Azure Portal에서 종단 간 암호화를 사용 하도록 설정 합니다.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 4498e78b408f64ab5bc00b9f8730559b90c95d57
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817658"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Azure Portal를 사용 하 여 호스트에서 암호화를 사용 하 여 종단 간 암호화를 사용 하도록 설정 합니다.

호스트에서 암호화를 사용 하도록 설정 하면 VM 호스트에 저장 된 데이터는 미사용 및 저장소 서비스로 암호화 된 흐름으로 암호화 됩니다. 호스트의 암호화 및 기타 관리 되는 디스크 암호화 유형에 대 한 개념 정보는 다음을 참조 하세요.

* Linux: [호스트에서 암호화-VM 데이터를 위한 종단 간 암호화](./linux/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

* Windows: [호스트에서 암호화-VM 데이터를 위한 종단 간 암호화](./windows/disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>지원되는 지역

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>지원되는 VM 크기

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>사전 요구 사항

호스트에서 Vm 또는 가상 머신 확장 집합에 대 한 암호화를 사용할 수 있으려면 구독에서 기능을 사용 하도록 설정 해야 합니다. encryptionAtHost@microsoft에 전자 메일을 보냅니다. 구독에 사용할 수 있는 기능을 제공 하는 구독 Id를 사용 하는 com.

[제공 된 링크](https://aka.ms/diskencryptionupdates)를 사용 하 여 Azure Portal에 로그인 합니다.

> [!IMPORTANT]
> [제공 된 링크](https://aka.ms/diskencryptionupdates) 를 사용 하 여 Azure Portal에 액세스 해야 합니다. 호스트의 암호화는 현재 링크를 사용 하지 않고 공용 Azure Portal에 표시 되지 않습니다.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Azure Key Vault 및 디스크 암호화 집합 만들기

기능이 사용 하도록 설정 되 면 Azure Key Vault 및 디스크 암호화 집합이 아직 설정 되지 않은 경우 설정 해야 합니다.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>VM 배포

새 VM을 배포 하 여 호스트에서 암호화를 사용 하도록 설정 해야 하며, 기존 Vm에서는 사용할 수 없습니다.

1. **Virtual Machines** 를 검색 하 고 **+ 추가** 를 선택 하 여 VM을 만듭니다.
1. 새 가상 컴퓨터를 만들고 적절 한 지역 및 지원 되는 VM 크기를 선택 합니다.
1. **기본** 블레이드에서 원하는 다른 값을 입력 한 다음 **디스크** 블레이드로 이동 합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="가상 머신 만들기 기본 사항 블레이드, 지역 및 V M 크기의 스크린샷 강조 표시 됩니다.":::

1. **디스크** 블레이드에서 **호스트의 암호화**에 대해 **예** 를 선택 합니다.
1. 원하는 대로 나머지 항목을 선택 합니다.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-disk-blade.png" alt-text="가상 컴퓨터 만들기 디스크 블레이드의 스크린샷, 호스트의 암호화가 강조 표시 됩니다.":::

1. VM 배포 프로세스를 완료 하 고 사용자 환경에 적합 한 선택 항목을 만듭니다.

이제 암호화를 사용 하 여 VM을 배포 했으며, 호스트에서 암호화를 사용 하 여 연결 된 모든 디스크가 암호화 됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Resource Manager 템플릿 샘플](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)