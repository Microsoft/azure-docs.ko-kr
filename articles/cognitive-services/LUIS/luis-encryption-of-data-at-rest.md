---
title: 휴지 상태의 데이터에 대 한 서비스 암호화 Language Understanding
titleSuffix: Azure Cognitive Services
description: Microsoft는 Microsoft에서 관리 하는 암호화 키를 제공 하며, CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 Cognitive Services 구독을 관리할 수도 있습니다. 이 문서에서는 Language Understanding (LUIS)에 대 한 미사용 데이터 암호화 및 CMK를 사용 하 고 관리 하는 방법을 설명 합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ce6561652801d52e5600ddc63e573070281da3f2
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078132"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>휴지 상태의 데이터에 대 한 서비스 암호화 Language Understanding

Language Understanding 서비스는 데이터가 클라우드에 유지 될 때 자동으로 암호화 합니다. Language Understanding 서비스 암호화는 데이터를 보호 하 고 조직의 보안 및 규정 준수 약정을 충족 하는 데 도움이 됩니다.

## <a name="about-cognitive-services-encryption"></a>Cognitive Services 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 규격 [256 비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용 하 여 암호화 및 암호 해독 됩니다. 암호화 및 암호 해독은 투명 합니다. 즉, 암호화 및 액세스를 관리 합니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 또는 애플리케이션을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 구독을 관리 하는 옵션도 있습니다. CMK는 액세스 제어를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있는 유연성을 제공 합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault를 사용하는 고객 관리형 키

사용자 고유의 키를 사용 하 여 구독을 관리 하는 옵션도 있습니다. BYOK (사용자 고유 키 사용) 라고도 하는 CMK (고객 관리 키)는 액세스 제어를 보다 유연 하 게 만들고, 회전 하 고, 사용 하지 않도록 설정 하 고, 취소할 수 있는 유연성을 제공 합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다.

Azure Key Vault를 사용 하 여 고객 관리 키를 저장 해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. Cognitive Services 리소스 및 키 자격 증명 모음은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테 넌 트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 무엇입니까?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)를 참조 하세요.

### <a name="customer-managed-keys-for-language-understanding"></a>Language Understanding에 대 한 고객 관리 키

고객 관리 키를 사용 하는 기능을 요청 하려면 [LUIS Service 고객이 관리 하는 키 요청 양식을](https://aka.ms/cogsvc-cmk)작성 하 고 제출 합니다. 요청 상태를 다시 들으려면 영업일 3-5 영업일이 소요 됩니다. 요청에 따라 큐에 배치 되 고 공간을 사용할 수 있게 되 면 승인 될 수 있습니다. LUIS와 함께 CMK를 사용 하도록 승인 되 면 Azure Portal에서 새 Language Understanding 리소스를 만들고 해당 가격 책정 계층으로 E0를 선택 해야 합니다. 새 SKU는 CMK를 제외 하 고 이미 사용할 수 있는 F0 SKU와 동일 하 게 작동 합니다. 사용자는 F0에서 새 E0 SKU로 업그레이드할 수 없습니다.

![LUIS subscription 이미지](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="limitations"></a>제한 사항

기존/이전에 만든 응용 프로그램에서 E0 계층을 사용할 때 몇 가지 제한 사항이 있습니다.

* E0 리소스로의 마이그레이션이 차단 됩니다. 사용자는 앱을 F0 리소스로 마이그레이션할 수만 있습니다. 기존 리소스를 F0로 마이그레이션한 후에는 E0 계층에서 새 리소스를 만들 수 있습니다. 마이그레이션에 대 한 자세한 내용은 [여기](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)를 참조 하세요.  
* E0 리소스로 응용 프로그램을 이동 하는 것은 차단 됩니다. 이러한 제한에 대 한 해결 방법은 기존 응용 프로그램을 내보내고이를 E0 리소스로 가져오는 것입니다.
* Bing 맞춤법 검사 기능은 지원 되지 않습니다.
* 응용 프로그램이 E0 인 경우 최종 사용자 트래픽 로깅을 사용할 수 없습니다.
* Azure Bot service의 Speech 준비 기능은 E0 계층의 응용 프로그램에 대해 지원 되지 않습니다. 이 기능은 CMK를 지원 하지 않는 Azure Bot Service를 통해 사용할 수 있습니다.
* 포털의 speech 준비 기능을 사용 하려면 Azure Blob Storage 필요 합니다. 자세한 내용은 [고유한 저장소 가져오기](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)를 참조 하세요.

### <a name="enable-customer-managed-keys"></a>고객 관리형 키 사용

새 Cognitive Services 리소스는 항상 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 리소스를 만들 때 고객 관리 키를 사용 하도록 설정할 수 없습니다. 고객 관리 키는 Azure Key Vault에 저장 되며, 키 자격 증명 모음은 Cognitive Services 리소스와 연결 된 관리 되는 id에 대 한 키 사용 권한을 부여 하는 액세스 정책을 사용 하 여 프로 비전 되어야 합니다. 관리 id는 CMK에 대 한 가격 책정 계층을 사용 하 여 리소스를 만든 후에만 사용할 수 있습니다.

Cognitive Services 암호화를 위해 Azure Key Vault에서 고객이 관리 하는 키를 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Portal에서 Cognitive Services 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](../Encryption/cognitive-services-encryption-keys-portal.md)

고객 관리 키를 사용 하도록 설정 하면 Azure AD의 기능인 시스템 할당 관리 id도 사용할 수 있습니다. 시스템 할당 관리 id를 사용 하도록 설정 하면이 리소스가 Azure Active Directory 등록 됩니다. 등록 한 후 관리 되는 id에는 고객이 관리 하는 키를 설정 하는 동안 선택 된 Key Vault에 대 한 액세스 권한이 부여 됩니다. [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)에 대해 자세히 알아볼 수 있습니다.

> [!IMPORTANT]
> 시스템 할당 관리 id를 사용 하지 않도록 설정 하면 키 자격 증명 모음에 대 한 액세스가 제거 되 고 고객 키로 암호화 된 데이터에 더 이상 액세스할 수 없게 됩니다. 이 데이터에 의존 하는 기능은 작동 하지 않습니다.

> [!IMPORTANT]
> 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure Portal에서 고객이 관리 하는 키를 구성 하는 경우 관리 id는 내부적으로 자동으로 할당 됩니다. 이후에 Azure AD 디렉터리 간에 구독, 리소스 그룹 또는 리소스를 이동 하는 경우 해당 리소스와 연결 된 관리 id는 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 Faq에서 **AZURE AD 디렉터리 간 구독 전송** [및 azure 리소스에 대 한 관리 id의 알려진 문제](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)를 참조 하세요.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에서 고객이 관리 하는 키 저장

고객이 관리 하는 키를 사용 하도록 설정 하려면 Azure Key Vault를 사용 하 여 키를 저장 해야 합니다. 키 자격 증명 모음에서 **일시 삭제** 및 **제거 안 함** 속성을 모두 사용 하도록 설정 해야 합니다.

2048 크기의 RSA 키만 Cognitive Services 암호화에서 지원 됩니다. 키에 대 한 자세한 내용은 **Key Vault 키** [Azure Key Vault 키, 암호 및 인증서](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)정보를 참조 하세요.

### <a name="rotate-customer-managed-keys"></a>고객 관리 키 회전

규정 준수 정책에 따라 Azure Key Vault에서 고객 관리형 키를 순환할 수 있습니다. 키를 회전 하는 경우 새 키 URI를 사용 하도록 Cognitive Services 리소스를 업데이트 해야 합니다. Azure Portal에서 새 버전의 키를 사용 하도록 리소스를 업데이트 하는 방법을 알아보려면 [Azure Portal를 사용 하 여 Cognitive Services에 대 한 고객 관리 키 구성](../Encryption/cognitive-services-encryption-keys-portal.md)의 **키 버전 업데이트** 섹션을 참조 하세요.

키를 회전 해도 리소스의 데이터를 다시 암호화 하는 것은 트리거되지 않습니다. 사용자에 게 필요한 추가 작업은 없습니다.

### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대 한 액세스 취소

고객 관리 키에 대 한 액세스를 취소 하려면 PowerShell 또는 Azure CLI를 사용 합니다. 자세한 내용은 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 또는 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조하세요. Cognitive Services에서 암호화 키에 액세스할 수 없으므로 액세스를 취소 하면 Cognitive Services 리소스의 모든 데이터에 대 한 액세스가 효과적으로 차단 됩니다.

## <a name="next-steps"></a>다음 단계

* [LUIS Service 고객이 관리 하는 키 요청 양식](https://aka.ms/cogsvc-cmk)
* [Azure Key Vault에 대 한 자세한 정보](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
