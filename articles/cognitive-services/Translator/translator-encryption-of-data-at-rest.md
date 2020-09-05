---
title: 미사용 데이터의 변환기 암호화
titleSuffix: Azure Cognitive Services
description: Microsoft에서는 CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 Cognitive Services 구독을 관리할 수 있습니다. 이 문서에서는 Translator에 대 한 미사용 데이터 암호화와 CMK를 사용 하 고 관리 하는 방법을 설명 합니다.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ce7ff6ae134835de23a0d2670e8b4f44783654f8
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89079203"
---
# <a name="translator-encryption-of-data-at-rest"></a>미사용 데이터의 변환기 암호화

Translator는 데이터를 자동으로 암호화 하 여 클라우드로 지속 될 때 사용자 지정 변환 모델을 빌드하여 조직의 보안 및 규정 준수 목표를 충족 하는 데 도움을 줍니다.

## <a name="about-cognitive-services-encryption"></a>Cognitive Services 암호화 정보

데이터는 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 규격 [256 비트 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 암호화를 사용 하 여 암호화 및 암호 해독 됩니다. 암호화 및 암호 해독은 투명 합니다. 즉, 암호화 및 액세스를 관리 합니다. 데이터는 기본적으로 안전하며 암호화를 활용하기 위해 코드 또는 애플리케이션을 수정할 필요가 없습니다.

## <a name="about-encryption-key-management"></a>암호화 키 관리 정보

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. 고객 관리 키를 지 원하는 가격 책정 계층을 사용 하는 경우 다음 그림에 표시 된 것 처럼 [Azure Portal](https://portal.azure.com)의 **암호화** 섹션에서 리소스에 대 한 암호화 설정을 확인할 수 있습니다.

![암호화 설정 보기](../media/cognitive-services-encryption/encryptionblade.png)

Microsoft에서 관리 하는 암호화 키만 지 원하는 구독의 경우에는 **암호화** 섹션이 표시 되지 않습니다.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Azure Key Vault를 사용하는 고객 관리형 키

기본적으로 구독은 Microsoft에서 관리하는 암호화 키를 사용합니다. CMK (고객이 관리 하는 키) 라고 하는 고유한 키를 사용 하 여 구독을 관리 하는 옵션도 있습니다. CMK는 액세스 제어를 만들고, 회전 하 고, 비활성화 하 고, 취소할 수 있는 유연성을 제공 합니다. 데이터를 보호하는 데 사용되는 암호화 키를 감사할 수도 있습니다. CMK가 구독에 대해 구성 된 경우 두 번째 보안 계층을 제공 하는 동시에 두 번째 암호화가 제공 되며,이를 통해 Azure Key Vault를 통해 암호화 키를 제어할 수 있습니다.

> [!IMPORTANT]
> 고객 관리 키는 Translator 서비스의 모든 가격 책정 계층에서 사용할 수 있습니다. 고객 관리 키를 사용 하 고 [고객 관리 키 요청 양식을](https://aka.ms/cogsvc-cmk) 작성 하 고 제출 하는 기능을 요청 하려면 요청 상태를 확인 하는 데 약 3-5 영업일이 소요 됩니다. 요청에 따라 큐에 배치 되 고 공간을 사용할 수 있게 되 면 승인 될 수 있습니다. Translator 서비스를 사용 하 여 CMK를 승인한 후에는 새 변환기 리소스를 만들어야 합니다. 변환기 리소스를 만든 후 Azure Key Vault를 사용 하 여 관리 id를 설정할 수 있습니다.

번역기에 대 한 고객이 관리 하는 키를 사용 하도록 설정 하려면 다음 단계를 따르세요.

1. 새 지역 번역기 또는 지역 Cognitive Services 리소스를 만듭니다. 전역 리소스에서는 작동 하지 않습니다.
2. Azure Portal에서 관리 Id를 사용 하도록 설정 하 고 고객 관리 키 정보를 추가 합니다.
3. 사용자 지정 번역기에 새 작업 영역을 만들고이 구독 정보를 연결 합니다.

### <a name="enable-customer-managed-keys"></a>고객 관리형 키 사용

Azure Key Vault를 사용 하 여 고객 관리 키를 저장 해야 합니다. 사용자 고유의 키를 만들어 키 자격 증명 모음에 저장할 수도 있고, Azure Key Vault API를 사용하여 키를 생성할 수도 있습니다. Cognitive Services 리소스 및 키 자격 증명 모음은 동일한 지역 및 동일한 Azure Active Directory (Azure AD) 테 넌 트에 있어야 하지만 다른 구독에 있을 수 있습니다. Azure Key Vault에 대 한 자세한 내용은 [Azure Key Vault 무엇입니까?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)를 참조 하세요.

새 Cognitive Services 리소스는 항상 Microsoft 관리 키를 사용 하 여 암호화 됩니다. 리소스를 만들 때 고객 관리 키를 사용 하도록 설정할 수 없습니다. 고객 관리 키는 Azure Key Vault에 저장 되며, 키 자격 증명 모음은 Cognitive Services 리소스와 연결 된 관리 되는 id에 대 한 키 사용 권한을 부여 하는 액세스 정책을 사용 하 여 프로 비전 되어야 합니다. 관리 id는 리소스가 생성 되는 즉시 사용할 수 있습니다.

Cognitive Services 암호화를 위해 Azure Key Vault에서 고객이 관리 하는 키를 사용 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [Azure Portal에서 Cognitive Services 암호화를 위해 Key Vault를 사용 하 여 고객 관리 키 구성](../Encryption/cognitive-services-encryption-keys-portal.md)

고객 관리 키를 사용 하도록 설정 하면 Azure AD의 기능인 시스템 할당 관리 id도 사용할 수 있습니다. 시스템 할당 관리 id를 사용 하도록 설정 하면이 리소스가 Azure Active Directory 등록 됩니다. 등록 한 후 관리 되는 id에는 고객이 관리 하는 키를 설정 하는 동안 선택 된 Key Vault에 대 한 액세스 권한이 부여 됩니다. [관리 id](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)에 대해 자세히 알아볼 수 있습니다.

> [!IMPORTANT]
> 시스템 할당 관리 id를 사용 하지 않도록 설정 하면 키 자격 증명 모음에 대 한 액세스가 제거 되 고 고객 키로 암호화 된 데이터에 더 이상 액세스할 수 없게 됩니다. 이 데이터에 의존 하는 기능은 작동 하지 않습니다. 배포한 모든 모델의 배포도 취소 됩니다. 사용자 지정 변환기에서 업로드 된 모든 데이터가 삭제 됩니다. 관리 되는 id를 다시 사용 하도록 설정 하면 자동으로 모델을 다시 배포 하지 않습니다.

> [!IMPORTANT]
> 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. Azure Portal에서 고객이 관리 하는 키를 구성 하는 경우 관리 id는 내부적으로 자동으로 할당 됩니다. 이후에 Azure AD 디렉터리 간에 구독, 리소스 그룹 또는 리소스를 이동 하는 경우 해당 리소스와 연결 된 관리 id는 새 테 넌 트로 전송 되지 않으므로 고객 관리 키가 더 이상 작동 하지 않을 수 있습니다. 자세한 내용은 Faq에서 **AZURE AD 디렉터리 간 구독 전송** [및 azure 리소스에 대 한 관리 id의 알려진 문제](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories)를 참조 하세요.  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Azure Key Vault에서 고객이 관리 하는 키 저장

고객이 관리 하는 키를 사용 하도록 설정 하려면 Azure Key Vault를 사용 하 여 키를 저장 해야 합니다. 키 자격 증명 모음에서 **일시 삭제** 및 **제거 안 함** 속성을 모두 사용 하도록 설정 해야 합니다.

2048 크기의 RSA 키만 Cognitive Services 암호화에서 지원 됩니다. 키에 대 한 자세한 내용은 **Key Vault 키** [Azure Key Vault 키, 암호 및 인증서](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)정보를 참조 하세요.

> [!NOTE]
> 전체 키 자격 증명 모음이 삭제 되 면 데이터가 더 이상 표시 되지 않으며 모든 모델의 배포를 취소할 수 있습니다. 사용자 지정 변환기에서 업로드 된 모든 데이터가 삭제 됩니다. 

### <a name="revoke-access-to-customer-managed-keys"></a>고객 관리 키에 대 한 액세스 취소

고객 관리 키에 대 한 액세스를 취소 하려면 PowerShell 또는 Azure CLI를 사용 합니다. 자세한 내용은 [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) 또는 [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault)를 참조하세요. 액세스를 취소 하면 Cognitive Services 리소스의 모든 데이터에 대 한 액세스가 차단 되 고 Cognitive Services에서 암호화 키에 액세스할 수 없으므로 모델이 배포 취소 됩니다. 업로드 된 모든 데이터도 사용자 지정 변환기에서 삭제 됩니다.


## <a name="next-steps"></a>다음 단계

* [Azure Key Vault에 대 한 자세한 정보](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
