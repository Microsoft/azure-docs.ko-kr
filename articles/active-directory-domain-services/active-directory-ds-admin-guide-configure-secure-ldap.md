---
title: Azure AD Domain Services에서 보안 LDAP(LDAPS) 구성 | Microsoft Docs
description: Azure AD 도메인 서비스 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: c6da94b6-4328-4230-801a-4b646055d4d7
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: ergreenl
ms.openlocfilehash: 66c50d7ec9948b1f2447aadcef4a4a8f321204ef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62104102"
---
# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인에 대해 보안 LDAP(LDAPS) 구성
이 문서에서는 Azure AD 도메인 서비스 관리되는 도메인에 대해 LDAPS(Secure Lightweight Directory Access Protocol)를 사용하도록 설정하는 방법을 보여 줍니다. 보안 LDAP는 'SSL(Secure Sockets Layer)/TLS(Transport Layer Security)를 통한 LDAP(Lightweight Directory Access Protocol)'라고도 합니다.

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>시작하기 전에
이 문서에 나열된 작업을 수행하려면 다음이 필요합니다.

1. 유효한 **Azure 구독**.
2. **Azure AD 디렉터리** - 온-프레미스 디렉터리 또는 클라우드 전용 디렉터리와 동기화됩니다.
3. **Azure AD 도메인 서비스** 를 사용하도록 설정해야 합니다. 그렇지 않은 경우 [시작 가이드](active-directory-ds-getting-started.md)에 간략히 설명된 모든 작업을 따릅니다.
4. **보안 LDAP를 사용하도록 설정하는 데 사용할 인증서**

   * **권장** - 신뢰할 수 있는 공용 인증 기관에서 인증서를 가져옵니다. 이 구성 옵션은 더 안전합니다.
   * 또는 이 문서의 뒷부분에 나와 있는 것처럼 [자체 서명된 인증서를 만들도록](#task-1---obtain-a-certificate-for-secure-ldap) 선택할 수도 있습니다.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>보안 LDAP 인증서에 대한 요구 사항
보안 LDAP를 사용하도록 설정하기 전에 다음 지침에 따라 유효한 인증서를 획득합니다. 유효하지 않거나/잘못된 인증서로 관리되는 도메인에 대해 보안 LDAP를 사용하도록 설정하려는 경우 오류가 발생합니다.

1. **신뢰할 수 있는 발급자** - 인증서는 보안 LDAP를 사용하여 관리되는 도메인에 연결하는 컴퓨터에서 신뢰하는 기관에서 발급된 것이어야 합니다. 이 기관은 이러한 컴퓨터에서 신뢰할 수 있는 CA(공용 인증 기관)이거나 엔터프라이즈 CA일 수 있습니다.
2. **수명** - 인증서는 다음 3-6개월 이상 동안 유효해야 합니다. 인증서가 만료될 때 관리되는 도메인에 대한 보안 LDAP 액세스가 중단됩니다.
3. **주체 이름** - 인증서의 주체 이름은 관리되는 도메인이어야 합니다. 예를 들어 도메인 이름이 'contoso100.com'인 경우 인증서의 주체 이름은 'contoso100.com'이어야 합니다. DNS 이름(주체 대체 이름)을 관리되는 도메인에 대한 와일드 카드 이름으로 설정합니다.
4. **키 사용** - 디지털 서명 및 키 암호화와 같은 사례에 인증서를 구성해야 합니다.
5. **인증서 용도** - 인증서는 SSL 서버 인증에 대해 유효해야 합니다.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>작업 1 - 보안 LDAP를 위한 인증서 가져오기
첫 번째 태스크는 관리되는 도메인에 대한 보안 LDAP 액세스에 사용할 인증서를 얻는 것입니다. 다음 두 가지 옵션을 사용할 수 있습니다.

* 공용 CA 또는 엔터프라이즈 CA에서 인증서를 가져옵니다.
* 자체 서명된 인증서를 만듭니다.

> [!NOTE]
> 보안 LDAP를 사용하여 관리되는 도메인에 연결해야 하는 클라이언트 컴퓨터는 보안 LDAP 인증서의 발급자를 신뢰해야 합니다.
>

### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>옵션 A(권장) - 인증 기관에서 보안 LDAP 인증서를 가져옵니다.
조직이 공용 CA에서 해당 인증서를 가져오는 경우 해당 공용 CA에서 보안 LDAP 인증서를 가져옵니다. 엔터프라이즈 CA를 배포하는 경우 엔터프라이즈 CA에서 보안 LDAP 인증서를 가져옵니다.

> [!TIP]
> **'.onmicrosoft.com' 도메인 접미사가 있는 관리되는 도메인에 자체 서명된 인증서를 사용합니다.**
> 관리되는 도메인의 DNS 도메인 이름이 '.onmicrosoft.com'으로 끝나면 공용 인증 기관에서 보안 LDAP 인증서를 얻을 수 없습니다. Microsoft가 'onmicrosoft.com' 도메인을 소유하고 있으므로 공용 인증 기관에서 이 접미사가 있는 도메인에 대해 보안 LDAP 인증서 발급을 거부합니다. 이 시나리오에서는 자체 서명된 인증서를 만들고 이를 사용하여 보안 LDAP를 구성합니다.
>

공용 인증 기관에서 얻은 인증서가 [보안 LDAP 인증서에 대한 요구 사항](#requirements-for-the-secure-ldap-certificate)에 설명된 모든 요구 사항을 충족하는지 확인합니다.


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>옵션 B - 보안 LDAP에 대한 자체 서명된 인증서 만들기
공용 인증 기관의 인증서를 사용하지 않으려는 경우 보안 LDAP에 대한 자체 서명된 인증서를 만들도록 선택할 수 있습니다. 관리되는 도메인의 DNS 도메인 이름이 '.onmicrosoft.com'으로 끝나면 이 옵션을 선택합니다.

**PowerShell을 사용하여 자체 서명된 인증서 만들기**

Windows 컴퓨터에서 새로 자체 서명된 인증서를 만들려면 **관리자** 로 새 PowerShell 창을 열고 다음 명령을 입력합니다.

```powershell
$lifetime=Get-Date
New-SelfSignedCertificate -Subject contoso100.com `
  -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment `
  -Type SSLServerAuthentication -DnsName *.contoso100.com, contoso100.com
```

앞의 샘플에서 ‘.contoso100.com’을 관리되는 도메인의 DNS 도메인 이름으로 대체합니다. 예를 들어 ‘contoso100.onmicrosoft.com’이라는 관리되는 도메인을 만든 경우 Subject 특성의 ‘contoso100.com’을 ‘contoso100.onmicrosoft.com’으로 바꾸고, DnsName 특성의 ‘*.contoso100.com’을 ‘*.contoso100.onmicrosoft.com’으로 바꿉니다.

![Azure AD 디렉터리 선택](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

새로 만든 자체 서명된 인증서는 로컬 컴퓨터의 인증서 저장소에 배치됩니다.


## <a name="next-step"></a>다음 단계
[작업 2 - 보안 LDAP 인증서를 .PFX 파일로 내보내기](active-directory-ds-admin-guide-configure-secure-ldap-export-pfx.md)
