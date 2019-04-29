---
title: 미사용 데이터에 대 한 azure Storage 암호화 | Microsoft Docs
description: Azure Storage는 자동으로 암호화 하 여 데이터를 보호 하기 전에 클라우드로 유지 합니다. Azure Storage에 모든 데이터 blob, 디스크, 파일, 큐 또는 테이블의 암호화는 256 비트 AES 암호화를 사용 하 여 투명 하 게 해독 및 FIPS 140-2를 준수 하는 합니다.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/16/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 211cfeb3aba29245e154f4a7db86fb4a3659c36f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60730834"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>미사용 데이터에 대 한 azure Storage 암호화

Azure Storage는 클라우드에 유지 하는 경우 자동으로 데이터를 암호화 합니다. 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정에 맞게 수 있도록 합니다. Azure Storage의 데이터는 암호화 되며 투명 하 게 256 비트를 사용 하 여 해독 [AES 암호화](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)중 가장 강력한 블록 암호화를 사용할 수 고 FIPS 140-2를 준수 하는 합니다. Azure Storage 암호화는 Windows에서 BitLocker 암호화와 비슷합니다.

Azure Storage 암호화는 모든 신규 및 기존 저장소 계정에 대해 활성화 되 고 비활성화할 수 없습니다. 데이터를 기본적으로 보호 하기 때문에 코드 또는 Azure Storage 암호화를 활용 하는 응용 프로그램을 수정할 필요가 없습니다. 해당 성능 계층 (standard 또는 premium) 또는 (Azure Resource Manager 또는 클래식) 배포 모델에 관계 없이 저장소 계정 암호화 됩니다. 모든 Azure Storage 중복 옵션에는 암호화를 지원 하며 저장소 계정의 모든 복사본이 암호화 됩니다. 모든 Azure Storage 리소스 blob, 디스크, 파일, 큐 및 테이블을 포함 하 여 암호화 됩니다.

암호화에는 Azure Storage 성능 영향을 주지 않습니다. Azure Storage 암호화에 대 한 추가 비용은 없습니다.

Azure Storage 암호화 기본 암호화 모듈에 대 한 자세한 내용은 참조 하세요. [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)을 참조하세요.

## <a name="key-management"></a>키 관리

Microsoft 관리 키 저장소 계정의 암호화에 사용 하거나 Azure Key Vault와 함께 사용자 고유의 키를 사용 하 여 암호화를 관리할 수 있습니다.

### <a name="microsoft-managed-keys"></a>Microsoft 관리 키

기본적으로 저장소 계정의 Microsoft 관리 암호화 키를 사용 합니다. 저장소 계정에 대 한 암호화 설정을 볼 수 있습니다는 **암호화** 섹션을 [Azure portal](https://portal.azure.com)다음 그림에 나와 있는 것 처럼 합니다.

![Microsoft 관리 키로 암호화 된 계정 보기](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>고객 관리 키

고객 관리 키를 사용 하 여 Azure Storage 암호화를 관리할 수 있습니다. 고객 관리 키 만들기, 회전, 해제 및 액세스 제어를 취소 하는 데 더 많은 유연성을 제공 합니다. 또한 데이터를 보호 하기 위해 사용 되는 암호화 키를 감사할 수 있습니다. 

Azure Key Vault를 사용 하 여 키 관리를 키 사용을 감사 합니다. 사용자 고유의 키를 생성 하 고 key vault에 저장 하거나 Azure Key Vault Api를 사용 하 여 키를 생성 하 합니다. 저장소 계정 및 키 자격 증명 모음은 동일한 지역에 있어야 하지만 서로 다른 구독에 있을 수도 있습니다. Azure Key Vault에 대 한 자세한 내용은 참조 하세요. [Azure Key Vault 란?](../../key-vault/key-vault-overview.md)합니다.

고객 관리 키에 대 한 액세스를 해지 하려면 참조 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) 하 고 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)합니다. 효율적으로 액세스 권한을 취소할 해제 되는 암호화 키를 Azure Storage에서 액세스할 수 있는 저장소 계정에서 모든 데이터에 대 한 액세스를 차단 합니다.

Azure Storage를 사용 하 여 고객 관리 키를 사용 하는 방법에 알아보려면 다음이 문서 중 하나를 참조 하세요.

- [Azure portal에서 Azure Storage 암호화에 대 한 고객 관리 키를 구성 합니다.](storage-encryption-keys-portal.md)
- [PowerShell에서 Azure Storage 암호화에 대 한 고객 관리 키를 구성 합니다.](storage-encryption-keys-powershell.md)
- [Azure CLI에서 Azure Storage 암호화를 사용 하 여 고객 관리 키를 사용 합니다.](storage-encryption-keys-cli.md)

> [!NOTE]  
> 고객 관리 키에 대 한 지원 되지 않습니다 [Azure 관리 디스크](../../virtual-machines/windows/managed-disks-overview.md)합니다.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>디스크 암호화 및 azure Storage 암호화

Azure Storage 암호화를 사용 하 여 모든 Azure Storage 계정 및 포함 된 리소스는 암호화, Azure 가상 머신 디스크를 백업 하는 페이지 blob을 포함 하 여. 또한 사용 하 여 Azure 가상 머신 디스크를 암호화할 수 있습니다 [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md)합니다. Azure Disk Encryption는 업계 표준을 [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) Windows에서 및 [Dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) linux Azure Key Vault와 통합 되는 운영 체제 기반 암호화 솔루션을 제공 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Key Vault란?](../../key-vault/key-vault-overview.md)
