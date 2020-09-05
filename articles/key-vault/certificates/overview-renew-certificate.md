---
title: Azure Key Vault 인증서 갱신 정보
description: 이 문서에서는 Azure Key Vault 인증서를 갱신하는 방법을 설명합니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 3809fa9e1ce17a5a0c3cf333ac20ef543db4b5a7
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88588806"
---
# <a name="renew-your-azure-key-vault-certificates"></a>Azure Key Vault 인증서 갱신

Azure Key Vault를 사용하여 네트워크에 대한 디지털 인증서를 쉽게 프로비저닝, 관리 및 배포하고 애플리케이션에 대한 보안 통신을 사용하도록 설정할 수 있습니다. 인증서에 대한 자세한 내용은 [Azure Key Vault 인증서 정보](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates)를 참조하세요.

단기 인증서를 사용하거나 인증서 순환 주기를 늘리면 권한 없는 사용자가 애플리케이션에 액세스하는 것을 방지할 수 있습니다.

이 문서에서는 Azure Key Vault 인증서를 갱신하는 방법을 설명합니다.

## <a name="get-notified-about-certificate-expirations"></a>인증서 만료에 대한 알림 받기
인증서가 곧 만료될 때 알림을 받으려면 다음을 수행합니다.

첫째, PowerShell cmdlet [Add-AzureKeyVaultCertificateContact](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultcertificatecontact?view=azurermps-6.13.0)를 사용하여 키 자격 증명 모음에 인증서 연락처를 추가합니다.

둘째, 인증서 만료에 대한 알림을 받을 조건을 구성합니다. 인증서의 수명 주기 특성을 구성하려면 [Key Vault에서 인증서 자동 순환 구성](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)을 참조하세요.

Key Vault에는 다음과 같은 세 가지 범주의 인증서가 있습니다.
-   DigiCert 또는 GlobalSign과 같은 통합 CA(인증 기관)를 통해 생성된 인증서
-   비-통합 CA를 통해 생성된 인증서
-   자체 서명된 인증서

## <a name="renew-an-integrated-ca-certificate"></a>통합 CA 인증서 갱신 
Azure Key Vault는 신뢰할 수 있는 Microsoft 인증 기관 DigiCert 및 GlobalSign에서 발급한 인증서의 엔드투엔드 유지 관리를 처리합니다. [신뢰할 수 있는 CA를 Key Vault와 통합하는 방법](https://docs.microsoft.com/azure/key-vault/certificates/how-to-integrate-certificate-authority)을 알아보세요.

## <a name="renew-a-nonintegrated-ca-certificate"></a>비-통합 CA 인증서 갱신 
Azure Key Vault를 사용하면 CA에서 인증서를 가져올 수 있으며, 여러 Azure 리소스를 통합하여 쉽게 배포할 수 있는 장점이 있습니다. 인증서 만료 날짜를 추적하지 못하게 되거나 인증서가 이미 만료된 후 그 사실을 알게 되는 사태를 방지할 수 있도록 키 자격 증명 모음을 통해 최신 정보를 계속 받을 수 있습니다. 비-통합 CA 인증서의 경우 키 자격 증명 모음을 통해 만료 예정 이메일 알림을 설정할 수 있습니다. 이러한 알림을 여러 사용자에 대해서도 설정할 수 있습니다.

> [!IMPORTANT]
> 인증서는 버전이 지정된 개체입니다. 현재 버전이 만료될 경우 새 버전을 만들어야 합니다. 개념적으로 각각의 새 버전은 키와 해당 키를 ID에 연결하는 BLOB으로 구성된 새 인증서입니다. 비-파트너 CA를 사용하는 경우 키 자격 증명 모음은 키/값 쌍을 생성하고 CSR(인증서 서명 요청)을 반환합니다.

비-통합 CA 인증서를 갱신하려면 다음을 수행합니다.

1. Azure Portal에 로그인한 다음, 갱신할 인증서를 엽니다.
1. 인증서 창에서 **새 버전**을 선택합니다.
1. **인증서 작업**을 선택합니다.
1. **CSR 다운로드**를 선택하여 CSR 파일을 로컬 드라이브에 다운로드합니다.
1. 선택한 CA로 CSR을 보내서 요청에 서명합니다.
1. 서명한 요청을 다시 가져온 다음, 동일한 인증서 작업 창에서 **CSR 병합**을 선택합니다.

> [!NOTE]
> 서명된 CSR을 앞에서 만든 동일한 CSR 요청과 병합해야 합니다. 그렇지 않으면 키가 일치하지 않습니다.

새 CSR을 만드는 방법에 대한 자세한 내용은 [Key Vault에서 CSR 만들기 및 병합]( https://docs.microsoft.com/azure/key-vault/certificates/create-certificate-signing-request#azure-portal)을 참조하세요.

## <a name="renew-a-self-signed-certificate"></a>자체 서명된 인증서 갱신

Azure Key Vault는 자체 서명된 인증서의 자동 갱신도 처리합니다. 발급 정책 변경 및 인증서의 수명 주기 특성 업데이트에 대한 자세한 내용은 [Key Vault에서 인증서 자동 순환 구성](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-rotate-certificates#update-lifecycle-attributes-of-a-stored-certificate)을 참조하세요.

## <a name="troubleshoot"></a>문제 해결
발급된 인증서가 Azure Portal에서 *사용 안 함* 상태이면 **인증서 작업**으로 이동하여 인증서의 오류 메시지를 확인합니다.

## <a name="frequently-asked-questions"></a>자주 묻는 질문

**인증서의 자동 순환 기능을 테스트하려면 어떻게 해야 하나요?**

유효 기간이 **1개월**인 인증서를 만든 다음, 순환의 수명 동작을 **1%** 로 설정합니다. 이렇게 설정하면 7.2시간마다 인증서가 순환됩니다.
  
**인증서 자동 갱신 후 태그가 복제되나요?**

예, 자동 갱신 후 태그가 복제됩니다.

## <a name="next-steps"></a>다음 단계
*   [DigiCert 인증 기관과 Key Vault 통합](how-to-integrate-certificate-authority.md)
*   [자습서: Key Vault에서 인증서 자동 순환 구성](tutorial-rotate-certificates.md)
