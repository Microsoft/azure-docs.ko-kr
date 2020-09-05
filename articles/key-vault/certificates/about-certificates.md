---
title: Azure Key Vault 인증서 정보 - Azure Key Vault
description: Azure Key Vault REST 인터페이스 및 인증서 개요입니다.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e7bae2ad19aaf4f1c93d8d2bdefa7fa9f0414860
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88923690"
---
# <a name="about-azure-key-vault-certificates"></a>Azure Key Vault 인증서 정보

Key Vault 인증서 지원은 x509 인증서 및 다음 동작의 관리를 위해 제공됩니다.  

-   인증서 소유자는 Key Vault 만들기 프로세스 또는 기존 인증서의 가져오기를 통해 인증서를 만들 수 있습니다. 자체 서명된 인증서 및 인증 기관 생성 인증서가 모두 포함됩니다.
-   Key Vault 인증서 소유자는 프라이빗 키 자료와 상호 작용하지 않고 X509 인증서의 안전한 스토리지 및 관리를 구현할 수 있습니다.  
-   인증서 소유자는 인증서의 수명 주기를 관리하도록 Key Vault를 지시하는 정책을 만들 수 있습니다.  
-   인증서 소유자는 인증서의 만료 및 갱신이라는 수명 주기 이벤트에 대한 알림을 위해 연락처 정보를 제공할 수 있습니다.  
-   선택한 발급자 - Key Vault 파트너 X509 인증서 공급자/인증서 기관을 통한 자동 갱신을 지원합니다.

>[!Note]
>파트너가 아닌 공급자/기관도 허용되지만 자동 갱신 기능은 지원하지 않습니다.

## <a name="composition-of-a-certificate"></a>인증서 작성

Key Vault 인증서가 만들어지면 주소 지정 가능한 키와 암호도 동일한 이름으로 만들어집니다. Key Vault 키는 키 작업을 허용하고, Key Vault 비밀은 인증서 값을 비밀로 검색할 수 있게 합니다. Key Vault 인증서에는 공용 x509 인증서 메타데이터도 포함됩니다.  

인증서의 식별자 및 버전은 키 및 암호의 식별자 및 버전과 비슷합니다. Key Vault 인증서 버전으로 만든 특정 버전의 주소 지정 가능한 키와 암호는 Key Vault 인증서 응답에서 사용할 수 있습니다.
 
![인증서는 복잡한 개체입니다.](../media/azure-key-vault.png)

## <a name="exportable-or-non-exportable-key"></a>내보내기 가능/불가능 키

Key Vault 인증서가 만들어지면 PFX 또는 PEM 형식의 프라이빗 키를 사용하여 주소 지정 가능한 비밀에서 해당 인증서를 검색할 수 있습니다. 인증서를 만드는 데 사용된 정책은 키를 내보낼 수 있다고 표시해야 합니다. 정책에서 내보낼 수 없다고 표시하는 경우 프라이빗 키는 비밀로 검색될 때 값에 포함되지 않습니다.  

주소 지정이 가능한 키는 내보낼 수 없는 KV 인증서와 더 관련이 있습니다. 주소 지정 가능한 KV 키의 작업은 KV 인증서를 만드는 데 사용되는 KV 인증서 정책의 *keyusage* 필드에서 매핑됩니다.  

 - 지원되는 키 유형: RSA, RSA-HSM, EC, EC-HSM, oct([여기](https://docs.microsoft.com/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)에 나열됨) Exportable은 RSA, EC에서만 사용할 수 있습니다. HSM 키는 내보낼 수 없습니다.


## <a name="certificate-attributes-and-tags"></a>인증서 특성 및 태그

Key Vault 인증서에는 인증서 메타데이터, 주소 지정 가능한 키 및 주소 지정 가능한 비밀 외에도 특성 및 태그가 포함됩니다.  

### <a name="attributes"></a>특성

인증서 특성은 KV 인증서를 만들 때 만들어지는 주소 지정 가능한 키와 암호의 특성으로 미러됩니다.  

Key Vault 인증서에 포함되는 특성은 다음과 같습니다.  

-   *enabled*: 부울, 선택 사항, 기본값은 **true**입니다. 인증서 데이터를 비밀로 검색할 수 있는지 또는 키로 작동할 수 있는지 여부를 나타내기 위해 지정할 수 있습니다. 또한 *nbf*과 *exp* 사이에 작업이 발생할 때 *nbf* 및 *exp*와 함께 사용되며, enabled가 true로 설정된 경우에만 허용됩니다. *nbf* 및 *exp* 시간 범위에 속하지 않은 작업은 자동으로 허용되지 않습니다.  

응답에 포함되는 추가 읽기 전용 특성은 다음과 같습니다.

-   *created*: IntDate: 이 버전의 인증서를 만든 시점을 나타냅니다.  
-   *updated*: IntDate: 이 버전의 인증서를 업데이트한 시점을 나타냅니다.  
-   *exp*: IntDate: x509 인증서의 만료 날짜 값을 포함합니다.  
-   *nbf*: IntDate: x509 인증서의 날짜 값을 포함합니다.  

> [!Note] 
> Key Vault 인증서가 만료되면 주소 지정 가능한 해당 키와 비밀이 작동하지 않게 됩니다.  

### <a name="tags"></a>태그들

 클라이언트에서 지정하는 키 값 쌍의 사전이며, 키와 비밀의 태그와 비슷합니다.  

 > [!Note]
> 해당 개체 형식(키, 비밀 또는 인증서)에 대한 *list* 또는 *get* 권한이 태그에 있는 경우 호출자에서 해당 태그를 읽을 수 있습니다.

## <a name="certificate-policy"></a>인증서 정책

인증서 정책에는 Key Vault 인증서의 수명 주기를 만들고 관리하는 방법에 대한 정보가 포함됩니다. 프라이빗 키가 있는 인증서를 키 자격 증명 모음에 가져오면 x509 인증서를 읽어 기본 정책을 만듭니다.  

Key Vault 인증서를 처음부터 새로 만드는 경우 정책을 제공해야 합니다. 정책은 이 Key Vault 인증서 버전 또는 그 다음 Key Vault 인증서 버전을 만드는 방법을 지정합니다. 일단 정책이 설정되면 이후 버전에 대한 연속 만들기 작업에는 해당 정책이 필요하지 않습니다. Key Vault 인증서의 모든 버전에 대한 정책 인스턴스는 하나만 있습니다.  

크게 보자면, 인증서 정책에는 다음 정보가 포함됩니다(해당 정의는 [여기](https://docs.microsoft.com/powershell/module/az.keyvault/set-azkeyvaultcertificatepolicy?view=azps-4.4.0)에서 찾을 수 있음).  

-   X509 인증서 속성: 주체 이름, 주체 대체 이름, x509 인증서 요청을 만드는 데 사용되는 기타 속성을 포함합니다.  
-   키 속성: 키 유형, 키 길이, 내보낼 수 있는 ReuseKeyOnRenewal 필드를 포함합니다. 이러한 필드는 키를 생성하는 방법을 키 자격 증명 모음에 지시합니다. 
     - 지원되는 키 유형: RSA, RSA-HSM, EC, EC-HSM, oct([여기](https://docs.microsoft.com/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype)에 나열됨) 
-   비밀 속성: 인증서를 비밀 번호로 검색하기 위해 비밀 값을 생성하기 위한 주소 지정 가능한 비밀의 콘텐츠 형식과 같은 비밀 속성을 포함합니다.  
-   수명 작업: KV 인증서에 대한 수명 작업을 포함합니다. 각 수명 작업에 포함되는 항목은 다음과 같습니다.  

     - 트리거: 만료 이전 잔존 일 수 또는 수명 범위 백분율을 통해 지정됩니다.  

     - 작업: *emailContacts* 또는 *autoRenew* 작업 유형이 지정됩니다.  

-   발급자: x509 인증서를 발급하는 데 사용할 인증서 발급자에 대한 매개 변수입니다.  
-   정책 특성: 정책과 관련된 특성을 포함합니다.  

### <a name="x509-to-key-vault-usage-mapping"></a>X509 및 Key Vault 사용 매핑

다음 표에서는 x509 키 사용 정책과 Key Vault 인증서 만들기의 일부로 만들어진 키의 효과적인 키 작업을 매핑하고 있습니다.

|**X509 키 사용 플래그**|**Key Vault 키 작업**|**기본 동작**|
|----------|--------|--------|
|DataEncipherment|encrypt, decrypt| 해당 없음 |
|DecipherOnly|decrypt| 해당 없음  |
|DigitalSignature|sign, verify| 인증서를 만들 때 사용하도록 지정하지 않은 Key Vault 기본값 | 
|EncipherOnly|encrypt| 해당 없음 |
|KeyCertSign|sign, verify|해당 없음|
|KeyEncipherment|wrapKey, unwrapKey| 인증서를 만들 때 사용하도록 지정하지 않은 Key Vault 기본값 | 
|NonRepudiation|sign, verify| 해당 없음 |
|crlsign|sign, verify| 해당 없음 |

## <a name="certificate-issuer"></a>인증서 발급자

Key Vault 인증서 개체에는 선택한 인증서 발급자 공급자와 통신하여 x509 인증서를 요청하는 데 사용되는 구성이 있습니다.  

-   Key Vault와 제휴 관계에 있는 TLS/SSL 인증서에 대한 인증서 발급자 공급자는 다음과 같습니다.

|**공급자 이름**|**위치**|
|----------|--------|
|DigiCert|퍼블릭 클라우드 및 Azure Government의 모든 키 자격 증명 모음 서비스 위치에서 지원됩니다.|
|GlobalSign|퍼블릭 클라우드 및 Azure Government의 모든 키 자격 증명 모음 서비스 위치에서 지원됩니다.|

Key Vault에 인증서 발급자를 만들려면 먼저 다음 필수 조건 1 및 2의 단계를 수행해야 합니다.  

1. CA(인증 기관) 공급자에 등록  

    -   조직 관리자는 하나 이상의 CA 공급자와 함께 자신의 회사(예: Contoso)를 등록해야 합니다.  

2. 관리자는 Key Vault에 대한 요청자 자격 증명을 만들어 TLS/SSL 인증서를 등록(및 갱신)합니다.  

    -   공급자의 발급자 개체를 만드는 데 사용할 구성을 키 자격 증명 모음에 제공합니다.  

인증서 포털에서 발급자 개체를 만드는 방법에 대한 자세한 내용은 [Key Vault 인증서 블로그](https://aka.ms/kvcertsblog)를 참조하세요.  

Key Vault를 사용하면 서로 다른 발급자 구성으로 여러 발급자 개체를 만들 수 있습니다. 발급자 개체가 만들어지면 하나 또는 여러 개의 증명서 정책에서 해당 이름을 참조할 수 있습니다. 발행자 개체를 참조하면 인증서를 만들고 갱신하는 동안 CA 공급자로부터 x509 인증서를 요청할 때 발행자 개체에서 지정한 대로 구성을 사용하도록 Key Vault에 지시합니다.  

발급자 개체는 자격 증명 모음에 만들어지며, 동일한 자격 증명 모음의 KV 인증서에만 사용할 수 있습니다.  

## <a name="certificate-contacts"></a>인증서 연락처

인증서 연락처에는 인증서 수명 이벤트에서 트리거된 알림을 보내도록 연락처 정보가 포함됩니다. 연락처 정보는 키 자격 증명 모음의 모든 인증서에서 공유합니다. 알림은 키 자격 증명 모음의 모든 인증서에 대한 이벤트에 대해 지정한 모든 연락처로 보내집니다.  

인증서를 자동으로 갱신하도록 정책이 설정되는 경우 알림을 보내는 이벤트는 다음과 같습니다.  

- 인증서 갱신 전
- 인증서 갱신 후 - 인증서가 성공적으로 갱신된 경우에 시작되거나, 오류가 있는 경우 인증서를 수동으로 갱신해야 합니다.  

  인증서를 수동으로(이메일을 통해서만) 갱신하는 정책을 설정하는 경우 인증서를 갱신할 시간이 되면 알림이 전송됩니다.  

## <a name="certificate-access-control"></a>인증서 액세스 제어

 인증서에 대한 액세스 제어는 Key Vault를 통해 관리되고, 해당 인증서를 포함하는 Key Vault를 통해 제공됩니다. 인증서에 대한 액세스 제어 정책은 동일한 Key Vault의 키 및 비밀에 대한 액세스 제어 정책과 다릅니다. 사용자는 인증서를 보관할 하나 이상의 자격 증명 모음을 만들어서 시나리오를 적절하게 세분화하고 인증서를 관리할 수 있습니다.  

 키 자격 증명 모음의 비밀 액세스 제어 항목에서 보안 주체별로 사용할 수 있고 비밀 개체에 허용되는 작업과 매우 비슷한 권한은 다음과 같습니다.  

- 인증서 관리 작업에 필요한 권한
  - *get*: 현재 인증서 버전 또는 모든 인증서 버전 가져오기 
  - *list*: 현재 인증서 또는 인증서 버전 나열  
  - *update*: 인증서 업데이트
  - *create*: Key Vault 인증서 만들기
  - *import*: 인증서 자료를 Key Vault 인증서로 가져오기
  - *delete*: 인증서, 해당 정책 및 모든 해당 버전 삭제  
  - *recover*: 삭제된 인증서 복구
  - *backup*: 인증서를 키 자격 증명 모음에 백업
  - *restore*: 키 자격 증명 모음에 백업된 인증서 복원
  - *managecontacts*: Key Vault 인증서 연락처 관리  
  - *manageissuers*: Key Vault 인증서 기관/발급자 관리
  - *getissuers*: 인증서 기관/발급자 가져오기
  - *listissuers*: 인증서 기관/발급자 나열  
  - *setissuers*: Key Vault 인증서의 기관/발급자 만들기 또는 업데이트  
  - *deleteissuers*: Key Vault 인증서의 기관/발급자 삭제  
 
- 권한 있는 작업에 필요한 권한
  - *purge*: 삭제된 인증서 제거(영구적으로 삭제)

자세한 내용은 [Key Vault REST API 참조에서 인증서 작업](/rest/api/keyvault)을 참조하세요. 권한 설정에 대한 내용은 [자격 증명 모음 - 만들기 또는 업데이트](/rest/api/keyvault/vaults/createorupdate) 및 [자격 증명 모음 - 액세스 정책 업데이트](/rest/api/keyvault/vaults/updateaccesspolicy)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Key Vault 정보](../general/overview.md)
- [키, 비밀 및 인증서에 대한 정보](../general/about-keys-secrets-certificates.md)
- [키 정보](../keys/about-keys.md)
- [비밀 정보](../secrets/about-secrets.md)
- [인증, 요청 및 응답](../general/authentication-requests-and-responses.md)
- [Key Vault 개발자 가이드](../general/developers-guide.md)
