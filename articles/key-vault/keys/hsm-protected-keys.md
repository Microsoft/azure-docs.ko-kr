---
title: HSM 보호 키를 생성하고 전송하는 방법 – Azure Key Vault
description: 이 문서를 통해 Azure Key Vault에서 사용할 고유의 HSM 보호 키를 생성하고 전송하는 데 필요한 계획을 세울 수 있습니다. BYOK, 즉 Bring Your Own Key라고도 합니다.
services: key-vault
author: amitbapat
manager: devtiw
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: tutorial
ms.date: 05/29/2020
ms.author: ambapat
ms.openlocfilehash: 76b10dbd9b6d801d93cd5d9704531eb1a6de36a5
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88585495"
---
# <a name="import-hsm-protected-keys-to-key-vault"></a>Key Vault로 HSM 보호 키 가져오기

보안을 강화하기 위해 Azure Key Vault 사용 시 HSM 경계를 절대로 벗어나지 않고 HSM(하드웨어 보안 모듈)에서 키를 가져오거나 생성할 수 있습니다. 이 시나리오를 흔히 BYOK( *Bring Your Own Key*)라고 합니다. Azure Key Vault는 HSM(FIPS 140-2 Level 2 유효성 검사 통과)의 nCipher nShield 제품군을 사용하여 키를 보호합니다.

이 기능은 Azure 중국 21Vianet에 사용할 수 없습니다.

> [!NOTE]
> Azure Key Vault에 대한 자세한 내용은 [Azure Key Vault란?](../general/overview.md)  
> HSM 보호 키에 대한 자격 증명 모음을 만드는 내용이 포함된 자습서를 시작하려면 [Azure Key Vault란?](../general/overview.md)을 참조하세요.

## <a name="supported-hsms"></a>지원되는 HSM

사용하는 HSM에 따라 두 가지 방법을 통해 HSM 보호 키를 Key Vault에 전송할 수 있습니다. 아래 표를 참조하여 HSM 생성에 사용할 방법을 결정한 다음, Azure Key Vault에 사용할 고유의 HSM 보호 키를 전송하세요. 

|공급업체 이름|공급업체 유형|지원되는 HSM 모델|지원되는 HSM 키 전송 방법|
|---|---|---|---|
|[nCipher](https://www.ncipher.com/products/key-management/cloud-microsoft-azure)|제조업체,<br/>서비스로 제공되는 HSM|<ul><li>HSM의 nShield 제품군</li><li>서비스로 제공되는 nShield</ul>|**방법 1:** [nCipher BYOK](hsm-protected-keys-ncipher.md)(키 가져오기 및 HSM 유효성 검사에 대한 강력한 보증 포함)<br/>**방법 2:** [새로운 BYOK 방법 사용](hsm-protected-keys-byok.md) |
|Thales|제조업체|<ul><li>펌웨어 버전 7.3 이상을 사용하는 Luna HSM 7 제품군</li></ul>| [새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|Fortanix|제조업체,<br/>서비스로 제공되는 HSM|<ul><li>SDKMS(자체 방어 키 관리 서비스)</li><li>Equinix SmartKey</li></ul>|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|Marvell|제조업체|다음을 사용하는 모든 LiquidSecurity HSM<ul><li>펌웨어 버전 2.0.4 이상</li><li>펌웨어 버전 3.2 이상</li></ul>|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|
|Cryptomathic|ISV(엔터프라이즈 키 관리 시스템)|다음을 포함한 여러 HSM 브랜드 및 모델<ul><li>nCipher</li><li>Thales</li><li>Utimaco</li></ul>자세한 내용은 [Cryptomathic 사이트](https://www.cryptomathic.com/azurebyok) 참조|[새로운 BYOK 방법 사용](hsm-protected-keys-byok.md)|


## <a name="next-steps"></a>다음 단계

* [Key Vault 모범 사례](../general/best-practices.md)에 따라 키의 보안, 내구성 및 모니터링을 확보합니다.
* 새 BYOK 방법에 대한 자세한 내용은 [BYOK 사양](https://docs.microsoft.com/azure/key-vault/keys/byok-specification)을 참조하세요.
