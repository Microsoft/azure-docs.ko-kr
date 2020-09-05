---
title: 질문과 대답-인증서 가져오기 Azure Key Vault
description: Azure Key Vault 인증서 가져오기에 대 한 자주 묻는 질문에 대 한 대답을 확인 하세요.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: b7a2c78238de58ee8851462aa7193121b35f72a9
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588824"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Azure Key Vault 인증서 가져오기 FAQ

이 문서에서는 Azure Key Vault 인증서 가져오기에 대 한 자주 묻는 질문에 답변 합니다.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Azure Key Vault에서 인증서를 가져오려면 어떻게 해야 하나요?

인증서 가져오기 작업의 경우 두 가지 인증서 파일 형식 (PEM 및 PFX)을 허용 Azure Key Vault 합니다. 공용 부분만 포함 된 PEM 파일이 있지만 Key Vault에서는 개인 키가 있는 PEM 또는 PFX 파일만을 사용 하 고 허용 합니다. 자세한 내용은 [Key Vault에 인증서 가져오기](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)를 참조 하세요.

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Key Vault으로 암호로 보호 된 인증서를 가져온 후 다운로드 한 후에는 연결 된 암호가 표시 되지 않는 이유는 무엇 인가요?
    
Key Vault에서 인증서를 가져오고 보호 한 후에는 연결 된 암호가 저장 되지 않습니다. 가져오기 작업 중에는 암호를 한 번만 입력 하면 됩니다. 이것은 의도적인 것 이지만 항상 비밀으로 인증서를 가져온 다음 [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx)를 통해 암호를 추가 하 여 BASE64에서 PFX로 변환할 수 있습니다.

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>"잘못 된 매개 변수" 오류를 해결 하려면 어떻게 해야 하나요? Key Vault로 가져오기에 대해 지원 되는 인증서 형식은 무엇입니까?

인증서를 가져올 때 키가 파일에 포함 되어 있는지 확인 해야 합니다. 개인 키를 다른 형식으로 별도로 저장 한 경우에는 인증서와 키를 결합 해야 합니다. 일부 인증 기관 (Ca)은 다른 형식의 인증서를 제공 합니다. 따라서 인증서를 가져오기 전에 해당 인증서가 PEM 또는 PFX 파일 형식으로 되어 있고 키가 Rivest – Rivest-shamir-adleman – Rivest-shamir-adleman (RSA) 또는 ECC (타원) 암호화를 사용 하는지 확인 해야 합니다. 

자세한 내용은 [인증서 요구 사항](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) 및 [인증서 키 요구 사항](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection)을 참조 하세요.

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>ARM 템플릿을 사용 하 여 인증서를 가져올 수 있나요?

아니요, Azure Resource Manager (ARM) 템플릿을 사용 하 여 인증서 작업을 수행할 수 없습니다. 권장 되는 해결 방법은 Azure API, Azure CLI 또는 PowerShell에서 인증서 가져오기 방법을 사용 하는 것입니다. 기존 인증서가 있는 경우 암호로 가져올 수 있습니다.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Azure Portal를 통해 인증서를 가져올 때 "문제가 발생 했습니다." 오류가 발생 합니다. 자세히 조사 하려면 어떻게 해야 하나요?
    
보다 설명적인 오류를 보려면 [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) 또는 [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0)을 사용 하 여 인증서 파일을 가져옵니다.

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>"오류 유형: 액세스 거부 또는 사용자가 인증서를 가져올 수 없습니다."를 어떻게 해결할 수 있나요?
    
가져오기 작업을 수행 하려면 액세스 정책에서 인증서를 가져올 수 있는 권한을 사용자에 게 부여 해야 합니다. 이렇게 하려면 주요 자격 증명 모음으로 이동 하 여 **액세스**정책  >  **추가 액세스 정책**을 선택 합니다.  >  **인증서 권한**  >  **보안 주체**를 선택 하 고 사용자를 검색 한 다음 사용자의 전자 메일 주소를 추가 합니다. 

인증서 관련 액세스 정책에 대 한 자세한 내용은 [Azure Key Vault 인증서 정보](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)를 참조 하세요.


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>"오류 유형: 인증서를 만들 때 충돌"을 해결 하려면 어떻게 해야 하나요?
    
각 인증서 이름은 고유 해야 합니다. 다른 이름과 같은 이름을 가진 인증서는 일시 삭제 된 상태에 있을 수 있습니다. 또한 Azure Key Vault [인증서의 조합](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) 에 따라, 인증서에 지정 하려는 것과 같은 이름의 키 자격 증명 모음에 다른 키 또는 암호가 있으면 인증서 만들기가 실패 하 고 해당 키 또는 비밀을 제거 하거나 인증서에 다른 이름을 사용 해야 합니다. 

자세한 내용은 [삭제 된 인증서 가져오기 작업](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)을 참조 하세요.

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>"오류 유형: 문자 길이가 너무 깁니다."가 표시 되는 이유는 무엇 인가요?
이 오류는 다음 두 가지 이유로 인해 발생할 수 있습니다.    
* 인증서 주체 이름은 200 자로 제한 됩니다.
* 인증서 암호는 200 자로 제한 됩니다.

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Azure Key Vault 만료 된 인증서를 가져올 수 있나요?
    
아니요, 만료 된 PFX 인증서를 Key Vault으로 가져올 수 없습니다.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>인증서를 적절 한 형식으로 변환 하려면 어떻게 해야 하나요?

인증서를 필요한 형식으로 제공 하도록 CA에 요청할 수 있습니다. 또한 인증서를 적절 한 형식으로 변환 하는 데 도움이 되는 타사 도구도 있습니다.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>파트너가 아닌 Ca에서 인증서를 가져올 수 있나요?
예, 모든 CA에서 인증서를 가져올 수 있지만 키 자격 증명 모음에서 인증서를 자동으로 갱신할 수 없습니다. 인증서 만료에 대 한 알림을 받을 미리 알림을 설정할 수 있습니다.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>파트너 CA에서 인증서를 가져오는 경우 자동 갱신 기능이 계속 작동 하나요?
예. 인증서를 업로드 한 후에는 인증서의 발급 정책에서 autorotation를 지정 해야 합니다. 설정은 다음 주기 또는 인증서 버전이 릴리스될 때까지 적용 됩니다.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Key Vault으로 가져온 App Service 인증서가 표시 되지 않는 이유는 무엇 인가요? 
인증서를 성공적으로 가져온 경우 **암호** 창으로 이동 하 여 확인할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- [인증서 Azure Key Vault](/azure/key-vault/certificates/about-certificates)
