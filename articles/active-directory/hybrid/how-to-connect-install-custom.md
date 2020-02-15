---
title: 'Azure AD Connect: 사용자 지정 설치 | Microsoft Docs'
description: 이 문서는 Azure AD Connect에 대한 사용자 지정 설치 옵션에 대해 자세히 설명합니다. Azure AD Connect를 통해 Active Directory를 설치하려면 다음 지침을 사용합니다.
services: active-directory
keywords: Azure AD Connect의 정의, Active Directory 설치, Azure AD에 대한 필수 구성 요소
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 6d42fb79-d9cf-48da-8445-f482c4c536af
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/14/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c99a6e018edd0806daac7cd429135e522f217f23
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159831"
---
# <a name="custom-installation-of-azure-ad-connect"></a>Azure AD Connect의 사용자 지정 설치
설치에 더 많은 옵션이 필요한 경우 Azure AD Connect **사용자 지정 설정**을 사용합니다. 여러 포리스트가 있는 경우 또한 빠른 설치에서 다루지 않는 선택적 기능을 구성하려는 경우에 사용합니다. [**빠른 설치**](how-to-connect-install-express.md) 옵션이 배포 또는 토폴로지 옵션을 충족하지 않는 경우에 사용합니다.

Azure AD Connect 설치를 시작하기 전에 [Azure AD Connect를 다운로드](https://go.microsoft.com/fwlink/?LinkId=615771)하고 [Azure AD Connect: 하드웨어 및 필수 구성 요소](how-to-connect-install-prerequisites.md)의 필수 구성 요소 단계를 완료하도록 합니다. 또한 [Azure AD Connect 계정 및 사용 권한](reference-connect-accounts-permissions.md)에 설명된 대로 사용할 수 있는 계정이 있어야 합니다.

사용자 지정 설정이 토폴로지와 일치하지 않는 경우(예: DirSync 업그레이드) 다른 시나리오의 관련 설명서를 참조하세요.

## <a name="custom-settings-installation-of-azure-ad-connect"></a>Azure AD Connect의 사용자 지정 설정 설치
### <a name="express-settings"></a>Express 설정
이 페이지에서 **사용자 지정**을 클릭하여 사용자 지정된 설정을 설치하기 시작합니다.

### <a name="install-required-components"></a>필요한 구성 요소 설치
동기화 서비스를 설치한 경우 선택적 구성 섹션을 선택하지 않은 채로 두고 Azure AD Connect가 모든 내용을 자동으로 설정하도록 할 수 있습니다. 즉, SQL Server 2012 Express LocalDB 인스턴스를 설정하고 적절한 그룹을 만들고 사용 권한을 할당합니다. 기본값을 변경하려면 다음 테이블을 사용하여 사용 가능한 선택적 구성 옵션을 숙지할 수 있습니다.

![필수 구성 요소](./media/how-to-connect-install-custom/requiredcomponents2.png)

| 선택적 구성 | Description |
| --- | --- |
| 기존 SQL Server 사용 |SQL Server 이름 및 인스턴스 이름을 지정할 수 있습니다. 사용하려는 데이터베이스 서버가 이미 있는 경우 이 옵션을 선택합니다. SQL Server에서 찾아보기를 사용하도록 설정하지 않은 경우 **인스턴스 이름**에 인스턴스 이름, 쉼표 및 포트 번호를 차례로 입력합니다.  그런 다음 Azure AD Connect 데이터베이스의 이름을 지정 합니다.  SQL 권한에 따라 새 데이터베이스를 만들지, 아니면 SQL 관리자가 데이터베이스를 미리 만들어야 하는지 여부가 결정 됩니다.  SQL SA 권한이 있는 경우 [기존 데이터베이스를 사용 하 여를 설치 하는 방법을](how-to-connect-install-existing-database.md)참조 하세요.  사용 권한이 위임 된 경우 (DBO) [SQL 위임 된 관리자 권한을 사용 하 여 Azure AD Connect 설치](how-to-connect-install-sql-delegation.md)를 참조 하세요. |
| 기존 서비스 계정 사용 |기본적으로 Azure AD Connect에서는 사용할 동기화 서비스에 대한 가상 서비스 계정을 사용합니다. 원격 SQL Server를 사용하거나 인증이 필요한 프록시를 사용하는 경우 **관리 서비스 계정** 또는 도메인의 서비스 계정을 사용하고 암호를 알고 있어야 합니다. 이러한 경우에 사용할 계정을 입력합니다. 설치를 실행하는 사용자가 SQL에서 SA이므로 서비스 계정에 대한 로그인을 만들 수 있도록 합니다.  [Azure AD Connect 계정 및 권한](reference-connect-accounts-permissions.md#adsync-service-account)을 참조하세요. </br></br>최신 빌드를 사용하면 이제는 SQL 관리자가 대역 외에서 데이터베이스를 프로비전한 후 데이터베이스 소유권이 있는 Azure AD Connect 관리자가 설치할 수 있습니다.  자세한 내용은 [SQL 위임된 관리자 권한을 사용하여 Azure AD Connect 설치](how-to-connect-install-sql-delegation.md)를 참조하세요.|
| 사용자 지정 동기화 그룹 지정 |기본적으로 Azure AD Connect에서는 동기화 서비스를 설치할 때 서버에 로컬 그룹 4개를 만듭니다. 이 그룹은 Administrators 그룹, Operators 그룹, Browse 그룹, Password Reset 그룹입니다. 여기서 사용자의 고유한 그룹을 지정할 수 있습니다. 그룹은 서버에서 로컬이어야 하며 도메인에서 찾을 수 없습니다. |

### <a name="user-sign-in"></a>사용자 로그인
필수 구성 요소를 설치한 후 사용자가 Single Sign-On 방법을 선택하라는 메시지가 표시됩니다. 다음 테이블에서 사용 가능한 옵션에 대한 간략한 설명을 제공합니다. 로그인 메서드에 대한 전체 설명은 [사용자 로그인](plan-connect-user-signin.md)을 참조하세요.

![사용자 로그인](./media/how-to-connect-install-custom/usersignin4.png)

| SSO(Single Sign-On) 옵션 | Description |
| --- | --- |
| 암호 해시 동기화 |사용자는 자신의 온-프레미스 네트워크에서 사용하는 것과 동일한 암호를 사용하여 Office 365와 같은 Microsoft 클라우드 서비스에 로그인할 수 있습니다. 사용자 암호는 암호 해시로 Azure AD에 동기화되며 클라우드에서 인증이 이루어집니다. 자세한 내용은 [암호 해시 동기화](how-to-connect-password-hash-synchronization.md)를 참조하세요. |
|통과 인증|사용자는 자신의 온-프레미스 네트워크에서 사용하는 것과 동일한 암호를 사용하여 Office 365와 같은 Microsoft 클라우드 서비스에 로그인할 수 있습니다.  사용자 암호는 유효성을 검사하기 위해 온-프레미스 Active Directory 도메인 컨트롤러로 전달됩니다.
| AD FS로 페더레이션 |사용자는 자신의 온-프레미스 네트워크에서 사용하는 것과 동일한 암호를 사용하여 Office 365와 같은 Microsoft 클라우드 서비스에 로그인할 수 있습니다.  사용자는 로그인하기 위해 자신의 온-프레미스 AD FS 인스턴스로 리디렉션되며 온-프레미스로 인증이 이루어집니다. |
| PingFederate을 사용한 페더레이션|사용자는 자신의 온-프레미스 네트워크에서 사용하는 것과 동일한 암호를 사용하여 Office 365와 같은 Microsoft 클라우드 서비스에 로그인할 수 있습니다.  사용자는 로그인하기 위해 자신의 온-프레미스 PingFederate 인스턴스로 리디렉션되며 온-프레미스로 인증이 이루어집니다. |
| 구성하지 않음 |사용자 로그인 기능이 설치 및 구성되지 않습니다. 이미 타사 페더레이션 서버 또는 다른 기존 솔루션이 있는 경우 이 옵션을 선택합니다. |
|Single Sign-On을 사용하도록 설정|이 옵션은 암호 해시 동기화 및 통과 인증 모두에서 사용할 수 있으며, 회사 네트워크에서 데스크톱 사용자를 위한 Single Sign-On 환경을 제공합니다. 자세한 내용은 [Single sign-on](how-to-connect-sso.md)을 참조하세요. </br>AD FS 고객의 경우 AD FS에서 동일한 수준의 single sign on을 이미 제공하므로 이 옵션을 사용할 수 없습니다.</br>

### <a name="connect-to-azure-ad"></a>Azure에 연결
Azure AD에 연결 화면에서, 전역 관리자 계정 및 암호를 입력합니다. 이전 페이지에서 **AD FS로 페더레이션**을 선택한 경우 페더레이션을 사용하도록 설정하려는 도메인의 계정으로 로그인하지 마십시오. Azure AD 테넌트와 함께 제공되는 기본 **onmicrosoft.com** 도메인의 계정을 사용하는 것이 좋습니다.

이 계정은 Azure AD에서 서비스 계정을 만드는 데에만 사용되며 마법사를 완료한 후에는 사용되지 않습니다.  
![사용자 로그인](./media/how-to-connect-install-custom/connectaad.png)

전역 관리자 계정이 MFA를 사용하도록 설정된 경우 로그인 팝업에서 암호를 다시 제공하고 MFA 인증을 완료해야 합니다. 과제에서는 확인 코드 또는 전화 통화를 제공할 수 있습니다.  
![사용자 로그인 MFA](./media/how-to-connect-install-custom/connectaadmfa.png)

또한 전역 관리자 계정은 [Privileged Identity Management](../privileged-identity-management/pim-getting-started.md)를 사용하도록 설정할 수 있습니다.

오류가 발생하고 연결에 문제가 있는 경우 [연결 문제 해결](tshoot-connect-connectivity.md)을 참조하세요.

## <a name="pages-under-the-sync-section"></a>동기화 섹션의 페이지

### <a name="connect-your-directories"></a>디렉터리에 연결
Active Directory 도메인 서비스에 연결하려면 Azure AD Connect에 충분한 권한이 있는 계정의 포리스트 이름과 자격 증명이 필요합니다.

![연결 디렉터리](./media/how-to-connect-install-custom/connectdir01.png)

포리스트 이름을 입력하고 **디렉터리 추가**를 클릭하면 팝업 대화 상자가 나타나고 다음 옵션을 묻는 메시지가 표시됩니다.

| 옵션 | Description |
| --- | --- |
| 새 계정 만들기 | Azure AD Connect 마법사에서 디렉터리 동기화 중에 AD 포리스트에 연결하기 위해 Azure AD Connect에 필요한 AD DS 계정을 만들게 하려면 이 옵션을 선택합니다. 이 옵션을 선택하면 엔터프라이즈 관리자 계정의 사용자 이름과 암호를 입력합니다. Azure AD Connect 마법사에서 제공된 엔터프라이즈 관리자 계정을 사용하여 필요한 AD DS 계정을 만듭니다. NetBios 또는 FQDN 형식으로 도메인 부분을 입력할 수 있습니다(예: FABRIKAM\administrator 또는 fabrikam.com\administrator). |
| 기존 계정 사용 | 디렉터리 동기화 중에 AD 포리스트에 연결하기 위해 Azure AD Connect를 사용할 기존 AD DS 계정을 제공하려면 이 옵션을 선택합니다. NetBios 또는 FQDN 형식으로 도메인 부분을 입력할 수 있습니다(예: FABRIKAM\syncuser 또는 fabrikam.com\syncuser). 기본 읽기 권한만 필요하기 때문에 이 계정은 일반 사용자 계정일 수 있습니다. 그러나 시나리오에 따라 더 많은 사용 권한이 할 수 있습니다. 자세한 내용은 [Azure AD Connect 계정 및 권한](reference-connect-accounts-permissions.md#create-the-ad-ds-connector-account)을 참조하세요. |

![연결 디렉터리](./media/how-to-connect-install-custom/connectdir02.png)

#### <a name="enterprise-admin-and-domain-admin-accounts-not-supported"></a>엔터프라이즈 관리자 및 도메인 관리자 계정이 지원 되지 않음
빌드 1.4.18.0에서 엔터프라이즈 관리자 또는 도메인 관리자 계정을 AD DS 커넥터 계정으로 사용 하는 것은 더 이상 지원 되지 않습니다.  **기존 계정 사용**을 지정 하는 경우 엔터프라이즈 관리자 또는 도메인 관리자 계정을 입력 하려고 하면 다음과 같은 오류가 표시 됩니다.

  **"AD 포리스트 계정에 엔터프라이즈 또는 도메인 관리자 계정을 사용 하는 것은 허용 되지 않습니다.  계정을 만들거나 올바른 권한이 있는 동기화 계정을 지정 Azure AD Connect 합니다.  자세한 내용은 &lt;&gt;**

### <a name="azure-ad-sign-in-configuration"></a>Azure AD 로그인 구성
이 페이지를 사용하면 온-프레미스 AD DS에 있는 UPN 도메인을 검토하고 이는 Azure AD에서 확인됩니다. 또한 이 페이지를 사용하면 userPrincipalName에 사용할 특성을 구성할 수 있습니다.

![확인되지 않은 도메인](./media/how-to-connect-install-custom/aadsigninconfig2.png)  
**추가되지 않음** 및 **확인되지 않음**으로 표시된 모든 도메인을 검토합니다. 사용한 해당 도메인을 Azure AD에서 확인하도록 합니다. 도메인을 확인한 경우 새로 고침 기호를 클릭합니다. 자세한 내용은 [도메인 추가 및 확인](../active-directory-domains-add-azure-portal.md)을 참조하세요.

**UserPrincipalName** - 특성 userPrincipalName은 사용자가 Azure AD 및 Office 365에 로그인할 때 사용하는 특성입니다. UPN-접미사로 알려진 사용된 도메인은 사용자가 동기화되기 전에 Azure AD에서 확인해야 합니다. Microsoft에서는 기본 특성 userPrincipalName을 유지하는 것을 권장합니다. 이 특성이 라우팅할 수 없고 확인할 수 없는 경우 다른 특성을 선택할 수 있습니다. 예를 들어 로그인 ID를 보관하는 특성으로 전자 메일을 선택할 수 있습니다. userPrincipalName 이외의 다른 특성을 사용하는 것을 **대체 ID**라고 합니다. 대체 ID 특성 값은 RFC822 표준을 따라야 합니다. 대체 ID는 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용할 수 있습니다. 특성의 값이 하나뿐이어도 Active Directory에 다중 값으로 정의하면 안 됩니다. 대체 ID에 대 한 자세한 내용은 질문과 [대답](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta-faq#does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname) 항목을 참조 하세요.

>[!NOTE]
> 통과 인증을 사용하도록 설정한 경우 마법사를 진행하려면 하나 이상의 검증된 도메인이 있어야 합니다.

> [!WARNING]
> 대체 ID를 사용하면 모든 Office 365 워크로드 부하와 호환되지 않습니다. 자세한 내용은 [대체 로그인 ID 구성](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)을 참조하세요.
>
>

### <a name="domain-and-ou-filtering"></a>도메인 및 OU 필터링
기본적으로 모든 도메인 및 OU가 동기화됩니다. Azure AD로 동기화하지 않으려는 일부 도메인 또는 OU가 있는 경우 이러한 도메인 및 OU의 선택을 취소할 수 있습니다.  
![DomainOU 필터링](./media/how-to-connect-install-custom/domainoufiltering.png)  
마법사의 이 페이지에서는 도메인 기반 및 OU 기반 필터링을 구성합니다. 변경을 원하는 경우 변경하기 전에 [도메인 기반 필터링](how-to-connect-sync-configure-filtering.md#domain-based-filtering) 및 [ou 기반 필터링](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)을 참조하세요. 일부 OU는 기능에 필수적이므로 반드시 선택해야 합니다.

Azure AD Connect 버전 1.1.524.0 미만에서 OU 기반 필터링을 사용하면 기본적으로 나중에 추가되는 새 OU가 동기화됩니다. 새 OU가 동기화되지 않아야 하는 동작을 원하는 경우 마법사가 [OU 기반 필터링](how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering)으로 완료된 후에 구성할 수 있습니다. Azure AD Connect 버전 1.1.524.0 이상에서는 새 OU를 동기화할지 여부를 지정할 수 있습니다.

[그룹 기반 필터링](#sync-filtering-based-on-groups)을 사용하려는 경우 그룹이 있는 OU가 포함되고 OU 필터링으로 필터링되지 않는지 확인합니다. OU 필터링은 그룹 기반 필터링 전에 평가됩니다.

일부 도메인은 방화벽 제한으로 인해 연결할 수 없을 수도 있습니다. 이러한 도메인은 기본적으로 선택 취소되며 경고가 표시됩니다.  
![연결할 수 없는 도메인](./media/how-to-connect-install-custom/unreachable.png)  
이 경고가 표시된 경우 이러한 도메인에 실제로 연결할 수 없는지 그리고 경고가 예상되는지 확인합니다.

### <a name="uniquely-identifying-your-users"></a>사용자를 고유하게 식별

#### <a name="select-how-users-should-be-identified-in-your-on-premises-directories"></a>온-프레미스 디렉터리에서 사용자를 식별하는 방법 선택
포리스트 기능 간에 일치를 사용하여 AD DS 포리스트의 사용자가 Azure AD에서 표현되는 방법을 정의할 수 있습니다. 사용자는 포리스트 전반에 걸쳐 한번만 표시할 수 있거나 활성화된 계정과 비활성화된 계정의 조합으로 이루어집니다. 사용자가 일부 포리스트 내에서 연락처로 표시될 수 있습니다.

![고유한](./media/how-to-connect-install-custom/unique2.png)

| 설정 | Description |
| --- | --- |
| [사용자는 모든 포리스트에 걸쳐 한번만 표시됩니다](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |모든 사용자가 Azure AD에 개별 개체로 만들어집니다. 개체는 메타 버스에 연결되지 않습니다. |
| [Mail 특성](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |메일 특성에 다른 포리스트의 동일한 값이 있는 경우 이 옵션은 사용자 및 연락처를 연결합니다. 연락처가 GALSync를 사용하여 생성 된 경우 이 옵션을 사용합니다. 이 옵션을 선택하면 Mail 특성이 채워지지 않은 사용자 개체는 Azure AD와 동기화되지 않습니다. |
| [ObjectSID 및 msExchangeMasterAccountSID/ msRTCSIP-OriginatorSid](plan-connect-topologies.md#multiple-forests-single-azure-ad-tenant) |이 옵션은 계정 포리스트에서 활성화된 사용자를 리소스 포리스트에서 비활성화된 사용자와 조인합니다. Exchange의 경우 이 구성을 연결된 된 사서함이라고 합니다. 이 옵션은 Lync만 사용하며 Exchange는 리소스 포리스트에 없는 경우에도 사용할 수 있습니다. |
| sAMAccountName 및 MailNickName |이 옵션은 사용자에 대한 로그인 ID를 찾을 수 있을 것으로 예상되는 특성에 조인합니다. |
| 특정 특성 |이 옵션을 사용하면 고유한 특성을 선택할 수 있습니다. 이 옵션을 선택하면 (선택한) 특성이 채워지지 않은 User 개체가 Azure AD와 동기화되지 않습니다. **제한:** 메타버스에서 이미 찾을 수 있는 특성을 선택해야 합니다. 사용자 지정 특성(메타버스에 없는)을 선택하면 마법사를 완료할 수 없습니다. |

#### <a name="select-how-users-should-be-identified-with-azure-ad---source-anchor"></a>Azure AD로 사용자를 식별하는 방법 선택 - 원본 앵커
sourceAnchor 특성은 사용자 개체의 수명 동안 변경할 수 없는 특성입니다. Azure AD에서 사용자와 온-프레미스 사용자를 연결하는 기본 키입니다.

| 설정 | Description |
| --- | --- |
| Let Azure manage the source anchor for me(Azure에서 원본 앵커를 대신 관리) | Azure AD에서 이 특성을 선택하게 하려면 이 옵션을 선택합니다. 이 옵션을 선택하면 Azure AD Connect 마법사에서 [Azure AD Connect: 설계 개념 - ms-DS-ConsistencyGuid를 sourceAnchor로 사용](plan-connect-design-concepts.md#using-ms-ds-consistencyguid-as-sourceanchor) 문서 섹션에서 설명하는 sourceAnchor 특성 선택 논리를 적용합니다. 마법사에서 사용자 지정 설치가 완료된 후 원본 앵커 특성으로 선택된 속성을 알려줍니다. |
| 특정 특성 | 기존 AD 특성을 sourceAnchor 특성으로 지정하려면 이 옵션을 선택합니다. |

특성을 변경할 수 없으므로, 좋은 특성을 사용해야 합니다. 좋은 후보는 objectGUID입니다. 사용자 계정이 포리스트/도메인 간에 이동하지 않은 한 이 특성이 변경되지 않습니다. 결혼을 하거나 할당이 변경될 때 바뀔 수 있는 특성을 피하십시오. @-sign와 함께 특성을 사용할 수 없으므로 email 및 userPrincipalName을 사용할 수 없습니다. 또한 이 특성은 대소문자를 구분하므로 포리스트 간에 개체를 이동하는 경우 대/소문자를 유지해야 합니다. 이진 특성은 Base64로 인코딩되지만 다른 특성 유형은 인코딩되지 않은 상태로 남아 있습니다. 페더레이션 시나리오 및 일부 Azure AD 인터페이스에서는 이 특성을 immutableID라고도 합니다. 원본 앵커에 대한 자세한 정보는 [디자인 개념](plan-connect-design-concepts.md#sourceanchor)에서 찾을 수 있습니다.

### <a name="sync-filtering-based-on-groups"></a>그룹에 따라 동기화 필터링
그룹 기능에 대해 필터링하면 파일럿을 위해 개체의 작은 하위 집합만 동기화할 수 있습니다. 이 기능을 사용하려면 온-프레미스 Active Directory에서 이 목적을 위한 그룹을 만듭니다. 그런 다음 Azure AD에 직접 구성원으로 동기화해야 하는 사용자와 그룹을 추가합니다. 나중에 사용자를 이 그룹에 추가하고 제거하여 Azure AD에 있어야 하는 개체의 목록을 유지할 수 있습니다. 동기화하려는 모든 개체는 그룹의 직접 구성원이어야 합니다. 사용자, 그룹, 연락처 및 컴퓨터/디바이스는 모두 직접 구성원이어야 합니다. 중첩된 그룹 구성원은 확인되지 않습니다. 그룹을 구성원으로 추가하는 경우 해당 그룹 자체만 추가되며 그룹의 구성원은 추가되지 않습니다.

![동기화 필터링](./media/how-to-connect-install-custom/filter2.png)

> [!WARNING]
> 이 기능은 파일럿 배포만 지원하기 위한 것입니다. 본격적인 프로덕션 배포에 사용하지 마십시오.
>
>

본격적인 프로덕션 배포에서는 동기화할 모든 개체를 가진 단일 그룹을 유지하기 어렵습니다. 대신에 [구성 필터링](how-to-connect-sync-configure-filtering.md)의 방법 중 하나를 사용해야 합니다.

### <a name="optional-features"></a>선택적 기능
이 화면을 사용하면 특정 시나리오에 대 한 선택적 기능을 선택할 수 있습니다.

>[!WARNING]
>Azure AD Connect 버전 **1.0.8641.0** 이상에서는 비밀번호 쓰기 저장을 위해 Azure Access Control 서비스를 사용합니다.  이 서비스는 **2018년 11월 7일**에 사용이 중지됩니다.  이러한 버전의 Azure AD Connect 중 하나를 사용하고 있고 비밀번호 쓰기 저장을 사용하도록 설정한 경우 서비스의 사용이 중지되면 사용자가 암호를 변경하거나 다시 설정할 수 없게 됩니다. 이러한 버전의 Azure AD Connect를 통한 비밀번호 쓰기 저장이 지원되지 않습니다.
>
>Azure Access Control 서비스에 대한 자세한 내용은 [방법: Azure Access Control 서비스에서 마이그레이션](../azuread-dev/active-directory-acs-migration.md)을 참조하세요.
>
>최신 버전의 Azure AD Connect를 다운로드하려면 [여기](https://www.microsoft.com/download/details.aspx?id=47594)를 클릭하세요.

![선택적 기능](./media/how-to-connect-install-custom/optional2.png)

> [!WARNING]
> 현재 디렉터리 동기화 또는 Azure AD Sync가 활성화되어 있는 경우 Azure AD Connect에서 쓰기 저장 기능을 활성화하지 마세요.



| 선택적 기능 | Description |
| --- | --- |
| Exchange 하이브리드 배포 |Exchange 하이브리드 배포 기능을 통해 온-프레미스 및 Office 365에서 모두 Exchange 사서함을 동시에 존재하게 할 수 있습니다. Azure AD Connect에서는 [특성](reference-connect-sync-attributes-synchronized.md#exchange-hybrid-writeback) 의 특정 집합을 Azure AD에서 온-프레미스 디렉터리로 다시 동기화합니다. |
| Exchange 메일 공용 폴더 | Exchange 메일 공용 폴더 기능을 사용하면 온-프레미스 Active Directory의 메일 사용이 가능한 공용 폴더 개체를 Azure AD로 동기화할 수 있습니다. |
| Azure AD 앱 및 특성 필터링 |Azure AD 앱 및 특성 필터링을 사용하여 동기화된 특성 집합을 사용자 지정할 수 있습니다. 이 옵션은 마법사에 구성 페이지를 두 개 더 추가합니다. 자세한 내용은 [Azure AD 앱 및 특성 필터링](#azure-ad-app-and-attribute-filtering)을 참조하세요. |
| 암호 해시 동기화 |페더레이션을 로그인 솔루션으로 선택한 경우 이 옵션을 사용하도록 설정할 수 있습니다. 그럼 다음, 암호 해시 동기화는 백업 옵션으로 사용할 수 있습니다. 자세한 내용은 [암호 해시 동기화](how-to-connect-password-hash-synchronization.md)를 참조하세요. </br></br>통과 인증을 선택한 경우 이 옵션을 또한 백업 옵션으로 레거시 클라이언트 지원을 보장하도록 사용할 수 있습니다. 자세한 내용은 [암호 해시 동기화](how-to-connect-password-hash-synchronization.md)를 참조하세요.|
| 비밀번호 쓰기 저장 |비밀번호 쓰기 저장을 사용하도록 설정하면 Azure AD에서 이루어지는 암호 변경 사항이 온-프레미스 디렉터리에 다시 기록됩니다. 자세한 내용은 [암호 관리 시작](../authentication/quickstart-sspr.md)을 참조하세요. |
| 그룹 쓰기 저장 |**Office 365 그룹** 기능을 사용하는 경우 이 그룹을 온-프레미스 Active Directory에 표시할 수 있습니다. 이 옵션은 Exchange가 온-프레미스 Active Directory에 있는 경우 사용할 수 있습니다. 자세한 내용은 [그룹 쓰기 저장](how-to-connect-preview.md#group-writeback)을 참조하세요. |
| 디바이스 쓰기 저장 |조건부 액세스 시나리오의 경우 Azure AD의 장치 개체를 온-프레미스 Active Directory에 쓰기 저장 (writeback) 할 수 있습니다. 자세한 내용은 [Azure AD Connect에서 디바이스 쓰기 저장 사용](how-to-connect-device-writeback.md)을 참조하세요. |
| 디렉터리 확장 특성 동기화 |디렉터리 확장 특성 동기화를 사용하도록 설정하면 지정된 특성이 Azure AD에 동기화됩니다. 자세한 내용은 [디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md)을 참조하세요. |

### <a name="azure-ad-app-and-attribute-filtering"></a>Azure AD 앱 및 특성 필터링
Azure AD에 동기화되는 특성을 제한 하려면 먼저 사용 중인 서비스를 선택합니다. 이 페이지에서 구성을 변경하는 경우 설치 마법사를 다시 실행하여 새 서비스를 명시적으로 선택해야 합니다.

![앱의 선택적 기능](./media/how-to-connect-install-custom/azureadapps2.png)

이전 단계에서 선택한 서비스에 따라 이 페이지는 동기화되는 모든 특성을 표시합니다. 이 목록은 동기화된 모든 개체 형식의 조합입니다. 동기화하지 않아야 하는 몇 가지 특정 특성이 있는 경우 해당 특성을 선택 취소할 수 있습니다.

![특성의 선택적 기능](./media/how-to-connect-install-custom/azureadattributes2.png)

> [!WARNING]
> 특성을 제거하면 기능에 영향을 줄 수 있습니다. 모범 사례 및 권장 사항은 [동기화된 특성](reference-connect-sync-attributes-synchronized.md#attributes-to-synchronize)을 참조하세요.
>
>

### <a name="directory-extension-attribute-sync"></a>디렉터리 확장 특성 동기화
Azure AD에서 조직이 추가한 사용자 지정 특성 또는 Active Directory의 다른 특성으로 스키마를 확장할 수 있습니다. 이 기능을 사용하려면, **선택적 기능** 페이지에서 **디렉터리 확장 특성 동기화**를 선택합니다. 이 페이지에서 동기화할 더 많은 특성을 선택할 수 있습니다.

>[!NOTE]
>[사용 가능한 특성] 상자는 대/소문자를 구분합니다.

![디렉터리 확장](./media/how-to-connect-install-custom/extension2.png)

자세한 내용은 [디렉터리 확장](how-to-connect-sync-feature-directory-extensions.md)을 참조하세요.

### <a name="enabling-single-sign-on-sso"></a>SSO(Single Sign-On) 사용하도록 설정
암호 동기화 또는 통과 인증에 사용하기 위해 single sign-on을 구성하면 Azure AD에 동기화되는 각 포리스트에 대해 한 번만 완료하면 되므로 과정이 간단합니다. 구성은 다음과 같이 두 단계로 이루어집니다.

1.  온-프레미스 Active Directory에 필요한 컴퓨터 계정을 만듭니다.
2.  클라이언트 컴퓨터의 인트라넷 영역을 구성하여 single sign on을 지원합니다.

#### <a name="create-the-computer-account-in-active-directory"></a>Active Directory에서 컴퓨터 계정 만들기
Azure AD Connect 도구에서 추가한 각 포리스트에 대해 도메인 관리자 자격 증명을 제공하여 각 포리스트에 컴퓨터 계정을 만들 수 있도록 해야 합니다. 자격 증명은 계정을 만드는 데만 사용되며 다른 작업에 저장되거나 사용되지 않습니다. 아래와 같이 Azure AD Connect 마법사의 **Single Sign-On을 사용하도록 설정** 페이지에서 자격 증명을 추가합니다.

![Single Sign-On을 사용하도록 설정](./media/how-to-connect-install-custom/enablesso.png)

>[!NOTE]
>포리스트에서 Single sign on을 사용하지 않으려는 경우 특정 포리스트를 건너뛸 수 있습니다.

#### <a name="configure-the-intranet-zone-for-client-machines"></a>클라이언트 컴퓨터에 대한 인트라넷 영역 구성
클라이언트에서 인트라넷 영역에 자동으로 로그인 하도록 하려면 URL이 인트라넷 영역에 포함 되어 있는지 확인 해야 합니다. 이렇게 하면 도메인에 가입된 컴퓨터가 기업 네트워크에 연결될 때 Kerberos 티켓을 Azure AD로 자동으로 보냅니다.
그룹 정책 관리 도구가 있는 컴퓨터에서 다음을 수행합니다.

1.  그룹 정책 관리 도구를 엽니다.
2.  모든 사용자에게 표시될 그룹 정책을 편집합니다. 예를 들어 기본 도메인 정책은 다음과 같습니다.
3.  **User Configuration\Administrative Templates\Windows Components\Internet Explorer\Internet Control Panel\Security Page**로 이동하고 아래 이미지에 따라 **영역에 대한 사이트 할당 목록**을 선택합니다.
4.  정책을 사용하도록 설정하고 대화 상자에서 다음과 같은 항목을 입력합니다.

        Value: `https://autologon.microsoftazuread-sso.com`  
        Data: 1  


5.  결과는 다음과 비슷합니다.  
![인트라넷 영역](./media/how-to-connect-install-custom/sitezone.png)

6.  **확인**을 두 번 클릭합니다.

## <a name="configuring-federation-with-ad-fs"></a>AD FS로 페더레이션 구성
Azure AD Connect에서 AD FS를 구성하는 것은 간단히 몇 번의 클릭으로 가능합니다. 구성하기 전에 다음 사항이 필요합니다.

* 원격 관리가 사용 가능한 페더레이션 서버용 Windows Server 2012 R2 이상 서버
* 원격 관리가 사용 가능한 웹 애플리케이션 프록시 서버용 Windows Server 2012 R2 이상 서버
* 사용할 페더레이션 서비스 이름에 대한 SSL 인증서(예: sts.contoso.com)

>[!NOTE]
>페더레이션 트러스트를 관리하는 데 사용하지 않는 경우에도 Azure AD Connect를 사용하여 AD FS 팜의 SSL 인증서를 업데이트할 수 있습니다.

### <a name="ad-fs-configuration-pre-requisites"></a>AD FS 구성 필수 조건
Azure AD Connect를 사용하여 AD FS 팜을 구성하려면, 원격 서버에서 WinRM이 활성화되도록 합니다. [페더레이션 필수 구성 요소](how-to-connect-install-prerequisites.md#prerequisites-for-federation-installation-and-configuration)에서 다른 작업이 완료되었는지 확인합니다. [테이블 3 - Azure AD Connect 및 페더레이션 서버/WAP](reference-connect-ports.md#table-3---azure-ad-connect-and-ad-fs-federation-serverswap)에 나열된 포트 요구 사항을 살펴봅니다.

### <a name="create-a-new-ad-fs-farm-or-use-an-existing-ad-fs-farm"></a>새 AD FS 팜을 만들거나 기존 AD FS 팜 사용
기존 AD FS 팜을 사용하거나 새 AD FS 팜을 만들도록 선택할 수 있습니다. 새로 만들기를 선택하는 경우 SSL 인증서를 제공해야 합니다. SSL 인증서가 암호로 보호된 경우 암호를 묻는 메시지가 나타납니다.

![AD FS 팜](./media/how-to-connect-install-custom/adfs1.png)

기존 AD FS 팜을 사용하도록 선택한 경우 AD FS와 Azure AD 간의 트러스트 관계를 구성하는 화면으로 이동합니다.

>[!NOTE]
>Azure AD Connect는 AD FS 팜을 하나만 관리하는 데 사용할 수 있습니다. 선택한 AD FS 팜에 구성된 Azure AD와 기존 페더레이션 트러스트가 있는 경우 트러스트는 Azure AD Connect에서 다시 처음부터 만들어집니다.

### <a name="specify-the-ad-fs-servers"></a>AD FS 서버 지정
AD FS를 설치하려는 서버를 입력합니다. 용량 계획 요구 사항에 따라 하나 이상의 서버를 추가할 수 있습니다. 이 구성을 수행하기 전에 모든 AD FS 서버(WAP 서버에는 필요하지 않음)를 Active Directory에 조인합니다. Microsoft에서는 테스트 및 파일럿 배포를 위해 단일 AD FS 서버를 설치하는 것이 좋습니다. 그런 다음 초기 구성 후 Azure AD Connect를 다시 실행하여 규모 필요에 맞게 더 많은 서버를 추가 및 배포합니다.

> [!NOTE]
> 이 구성을 수행하기 전에 모든 서버가 AD 도메인에 조인되었는지 확인 하십시오.
>
>

![AD FS 서버](./media/how-to-connect-install-custom/adfs2.png)

### <a name="specify-the-web-application-proxy-servers"></a>웹 애플리케이션 프록시 서버 지정
웹 애플리케이션 프록시 서버로 사용할 서버를 입력합니다. 웹 애플리케이션 프록시 서버는 DMZ(엑스트라넷 연결)에 배포되며 엑스트라넷에서 인증 요청을 지원합니다. 용량 계획 요구 사항에 따라 하나 이상의 서버를 추가할 수 있습니다. Microsoft에서는 테스트 및 파일럿 배포를 위해 단일 웹 애플리케이션 프록시 서버를 설치하는 것이 좋습니다. 그런 다음 초기 구성 후 Azure AD Connect를 다시 실행하여 규모 필요에 맞게 더 많은 서버를 추가 및 배포합니다. 인트라넷에서 인증을 충족하기 위해 동일한 수의 프록시 서버를 두는 것이 좋습니다.

> [!NOTE]
> <li> 사용할 계정이 WAP 서버의 로컬 관리자가 아닌 경우 관리자 자격 증명을 요구하는 메시지가 표시됩니다.</li>
> <li> 이 단계를 실행하기 전에 Azure AD Connect 서버와 웹 애플리케이션 프록시 서버 간에 HTTP/HTTPS 연결이 되어 있는지 확인합니다.</li>
> <li> 인증 요청이 진행될 수 있도록 웹 애플리케이션 서버와 AD FS 서버 간에 HTTP/HTTPS 연결이 되어 있는지 확인합니다.</li>
>

![웹앱](./media/how-to-connect-install-custom/adfs3.png)

웹 애플리케이션 서버가 AD FS 서버에 보안 연결을 설정할 수 있도록 자격 증명을 입력하라는 메시지가 표시됩니다. 이러한 자격 증명에서 로컬 관리자는 AD FS 서버에 있어야 합니다.

![Proxy](./media/how-to-connect-install-custom/adfs4.png)

### <a name="specify-the-service-account-for-the-ad-fs-service"></a>AD FS 서비스에 대한 서비스 계정 지정
AD FS 서비스가 Active Directory에서 사용자를 인증하고 사용자 정보를 검색하는데 도메인 서비스 계정이 필요합니다. 두 종류의 서비스 계정을 지원할 수 있습니다.

* **그룹 관리 서비스 계정** -Windows Server 2012에서 Active Directory Domain Services에 도입되었습니다. 이 유형의 계정은 계정 암호를 정기적으로 업데이트할 필요 없이 AD FS와 같은 단일 계정에 서비스를 제공합니다. AD FS 서버가 속해 있는 도메인에 Windows Server 2012 도메인 컨트롤러가 이미 있는 경우 이 옵션을 사용합니다.
* **도메인 사용자 계정** - 이 유형의 계정을 사용하려면 암호를 입력하고 암호가 변경되거나 만료될 때 암호를 정기적으로 업데이트해야 합니다. AD FS 서버가 속해 있는 도메인에 Windows Server 2012 도메인 컨트롤러가 없는 경우에만 이 옵션을 사용합니다.

그룹 관리 서비스 계정 선택했는데 Active Directory에서 이 기능을 사용한 적이 없는 경우 엔터프라이즈 관리자 자격 증명에 대한 메시지가 표시됩니다. 이러한 자격 증명은 키 저장소를 시작하고 Active Directory에서 기능을 사용하도록 설정하는 데 사용됩니다.

> [!NOTE]
> Azure AD Connect는 AD FS 서비스가 이미 도메인에서 SPN으로 등록되어 있는지 검색하기 위한 검사를 수행합니다.  AD DS에서는 중복된 SPN의 동시 등록을 허용하지 않습니다.  중복된 SPN이 없으면 SPN을 제거할 때까지 계속 진행할 수 없습니다.

![AD FS 서비스 계정](./media/how-to-connect-install-custom/adfs5.png)

### <a name="select-the-azure-ad-domain-that-you-wish-to-federate"></a>페더레이션하려는 Azure AD 도메인을 선택합니다.
이 구성은 AD FS와 Azure AD 간의 페더레이션 관계를 설정하는데 사용됩니다. Azure AD에 보안 토큰을 발급하도록 AD FS를 구성하고 이 특정 AD FS 인스턴스에서 토큰을 신뢰하도록 Azure AD를 구성합니다. 이 페이지에서는 초기 설치에서 단일 도메인만 구성할 수만 있습니다. 나중에 Azure AD Connect를 다시 실행하여 더 많은 도메인을 구성할 수 있습니다.

![Azure AD 도메인](./media/how-to-connect-install-custom/adfs6.png)

### <a name="verify-the-azure-ad-domain-selected-for-federation"></a>페더레이션에 선택한 Azure AD 도메인 확인
페더레이션할 도메인을 선택하면 Azure AD Connect가 확인되지 않은 도메인을 확인하는 데 필요한 정보를 제공합니다. 이 정보를 사용하는 방법은 [도메인 추가 및 확인](../active-directory-domains-add-azure-portal.md)을 참조하세요.

![Azure AD 도메인](./media/how-to-connect-install-custom/verifyfeddomain.png)

> [!NOTE]
> AD Connect는 구성 단계에서 도메인을 확인하려고 합니다. 필요한 DNS 기록을 추가하지 않고 계속 구성하는 경우 마법사가 구성을 완료할 수 없습니다.
>
>

## <a name="configuring-federation-with-pingfederate"></a>PingFederate로 페더레이션 구성
Azure AD Connect에서 PingFederate를 구성하는 것은 간단히 몇 번의 클릭으로 가능합니다. 하지만 다음과 같은 필수 구성 요소가 필요합니다.
- PingFederate 8.4 이상.  자세한 내용은 [Azure Active Directory 및 Office 365와 PingFederate 통합](https://docs.pingidentity.com/bundle/O365IG20_sm_integrationGuide/page/O365IG_c_integrationGuide.html)을 참조하세요.
- 사용할 페더레이션 서비스 이름에 대한 SSL 인증서(예: sts.contoso.com)

### <a name="verify-the-domain"></a>도메인 확인
PingFederate을 사용한 페더레이션을 선택한 후에는 페더레이션할 도메인을 확인하라는 메시지가 표시됩니다.  드롭다운 상자에서 도메인을 선택합니다.

![도메인 확인](./media/how-to-connect-install-custom/ping1.png)

### <a name="export-the-pingfederate-settings"></a>PingFederate 설정 내보내기


PingFederate는 각 페더레이션된 Azure 도메인의 페더레이션 서버로 구성해야 합니다.  [설정 내보내기] 단추를 클릭하고 이 정보를 PingFederate 관리자와 공유합니다.  페더레이션 서버 관리자가 구성을 업데이트한 다음, PingFederate 서버 URL 및 포트 번호를 제공할 것입니다. 그러면 Azure AD Connect가 메타데이터 설정을 확인할 수 있습니다.  

![도메인 확인](./media/how-to-connect-install-custom/ping2.png)

유효성 검사 문제를 해결하려면 PingFederate 관리자에게 문의하세요.  다음은 Azure와 유효한 트러스트 관계가 없는 PingFederate 서버의 예입니다.

![신뢰](./media/how-to-connect-install-custom/ping5.png)




### <a name="verify-federation-connectivity"></a>페더레이션 연결 확인
Azure AD Connect는 이전 단계에서 PingFederate 메타데이터에서 검색된 인증 엔드포인트의 유효성을 검사하려고 시도합니다.  Azure AD Connect는 먼저 로컬 DNS 서버를 사용하여 엔드포인트를 확인하려고 시도합니다.  그 다음에는 외부 DNS 공급자를 사용하여 엔드포인트를 확인하려고 시도합니다.  유효성 검사 문제를 해결하려면 PingFederate 관리자에게 문의하세요.  

![연결 확인](./media/how-to-connect-install-custom/ping3.png)

### <a name="verify-federation-login"></a>페더레이션 로그인 확인
마지막으로, 페더레이션된 도메인에 로그인하여 새로 구성된 페더레이션된 로그인 흐름을 확인할 수 있습니다. 이 작업에 성공하면 PingFederate을 사용한 페더레이션이 성공적으로 구성됩니다.
![로그인 확인](./media/how-to-connect-install-custom/ping4.png)

## <a name="configure-and-verify-pages"></a>페이지 구성 및 확인
이 페이지에서 구성이 이루어집니다.

> [!NOTE]
> 설치를 계속하기 전에 페더레이션을 구성한 경우 [페더레이션 서버에 대 한 이름 확인](how-to-connect-install-prerequisites.md#name-resolution-for-federation-servers)을 구성했는지 확인합니다.
>
>


![구성 준비](./media/how-to-connect-install-custom/readytoconfigure2.png)

### <a name="staging-mode"></a>스테이징 모드
준비 모드와 병렬로 새 동기화 서버를 설정할 수 있습니다. 클라우드에서 하나의 디렉터리에 내보낸 하나의 동기화 서버에만 지원됩니다. 하지만 DirSync를 실행하는 서버와 같은 다른 서버에서 이동하려는 경우 스테이징 모드에서 Azure AD Connect를 사용할 수 있습니다. 사용하도록 설정한 경우 동기화 엔진이 평시와 같이 데이터를 가져오고 동기화하지만 아무 것도 Azure AD 또는 AD로 내보내지 않습니다. 준비 모드에서는 암호 동기화 및 비밀번호 쓰기 저장 기능을 사용할 수 없습니다.

![스테이징 모드](./media/how-to-connect-install-custom/stagingmode.png)

스테이징 모드에 있는 동안, 동기화 엔진에 필요한 변경 내용을 작성하고 내보낼 내용을 검토합니다. 구성마음에 드는 경우, 설치 마법사를 다시 실행하고 스테이징 모드를 사용하지 않도록 설정합니다. 이제 데이터를 이 서버에서 Azure AD로 내보냅니다. 한 서버만이 내보낼 수 있으므로 동시에 다른 서버를 사용하지 않도록 설정했는지 확인합니다.

자세한 내용은 [준비 모드](how-to-connect-sync-staging-server.md)를 참조하세요.

### <a name="verify-your-federation-configuration"></a>페더레이션 구성 확인
확인 단추를 클릭하면 Azure AD Connect가 DNS 설정을 확인합니다.

**인트라넷 연결 확인**

* 페더레이션 FQDN 확인: Azure AD Connect는 DNS에서 페더레이션 FQDN을 확인할 수 있는지 검사하여 연결을 보장합니다. Azure AD Connect가 FQDN을 확인할 수 없는 경우 확인이 실패합니다. 확인을 완료할 수 있도록 페더레이션 서비스 FQDN에 DNS 레코드가 제공되는지 확인합니다.
* DNS A 레코드: Azure AD Connect는 페더레이션 서비스에 대한 A 레코드가 있는지 확인합니다. A 레코드가 없으면 확인이 실패합니다. 확인을 완료할 수 있도록 페더레이션 FQDN에 대한 CNAME 레코드가 아닌 A 레코드를 만듭니다.

**엑스트라넷 연결 확인**

* 페더레이션 FQDN 확인: Azure AD Connect는 DNS에서 페더레이션 FQDN을 확인할 수 있는지 검사하여 연결을 보장합니다.

![완료](./media/how-to-connect-install-custom/completed.png)

![확인](./media/how-to-connect-install-custom/adfs7.png)

엔드투엔드 인증의 유효성 검사가 성공하려면 다음 테스트 중 하나 이상을 수동으로 수행해야 합니다.

* 동기화가 완료되면 Azure AD Connect에서 페더레이션 로그인 확인 추가 작업을 사용하여 원하는 온-프레미스 사용자 계정에 대한 인증을 확인합니다.
* 인트라넷의 도메인 가입된 컴퓨터에서, 브라우저에서 로그인할 수 있는지 유효성 검사: https://myapps.microsoft.com에 연결하고 로그인된 계정으로 로그인을 확인합니다. 기본 제공 AD DS 관리자 계정은 동기화되지 않으며 확인을 위해 사용할 수 없습니다.
* 엑스트라넷에서, 디바이스에서 로그인 할 수 있는지 유효성을 검사합니다. 홈 컴퓨터 또는 모바일 디바이스에서 https://myapps.microsoft.com에 연결하고 자격 증명을 제공합니다.
* 리치 클라이언트 로그인 유효성을 검사합니다. https://testconnectivity.microsoft.com에 연결하고 **Office 365** 탭 및 **Office 365 Single Sign-on 테스트**를 선택합니다.

## <a name="troubleshooting"></a>문제 해결
다음 섹션에는 Azure AD Connect 설치 문제가 발생할 때 사용할 수 있는 문제 해결 정보가 포함되어 있습니다.

### <a name="the-adsync-database-already-contains-data-and-cannot-be-overwritten"></a>"ADSync 데이터베이스에 이미 데이터가 포함되어 있어 덮어쓸 수 없습니다."
Azure AD Connect 설치 하 고 **필수 구성 요소 설치** 페이지에서 **기존 SQL server 사용** 옵션을 선택 하는 경우 **adsync 데이터베이스에 이미 데이터가 포함 되어 있으며 덮어쓸 수 없다는 오류가 발생할 수 있습니다. 기존 데이터베이스를 제거한 후 다시 시도 하십시오.**

![Error](./media/how-to-connect-install-custom/error1.png)

SQL 서버의 SQL 인스턴스에 **ADSync**라고 하는 기존 데이터베이스가 이미 있기 때문이며, 이 데이터베이스는 여러분이 위의 텍스트 상자에서 지정한 것입니다.

이 문제는 일반적으로 Azure AD Connect를 제거한 후에 발생합니다.  제거할 때 데이터베이스는 SQL Server에서 삭제되지 않습니다.

이 문제를 해결하려면 제거하기 전까지 Azure AD Connect에서 사용하던 **ADSync** 데이터베이스가 더 이상 사용되지 않는지 확인합니다.

다음으로, 데이터베이스를 백업한 후 삭제하는 것이 좋습니다.

마지막으로, 데이터베이스를 삭제해야 합니다.  **Microsoft SQL Server Management Studio**를 사용하여 처리할 수 있으며, 그 후 SQL 인스턴스에 연결합니다. **ADSync** 데이터베이스를 찾아서 마우스 오른쪽 단추로 클릭하고, 상황에 맞는 메뉴에서 **삭제**를 선택합니다.  그런 다음, **확인** 단추를 클릭하여 삭제합니다.

![Error](./media/how-to-connect-install-custom/error2.png)

**ADSync** 데이터베이스를 삭제한 후에는 **설치** 단추를 클릭하여 다시 설치할 수 있습니다.

## <a name="next-steps"></a>다음 단계
설치가 완료된 후 로그아웃하고 동기화 서비스 관리자 또는 동기화 규칙 편집기를 사용하기 전에 Windows에 다시 로그인합니다.

Azure AD Connect를 설치했으므로 [설치를 확인하고 라이선스를 할당](how-to-connect-post-installation.md)할 수 있습니다.

[실수로 인한 삭제 방지](how-to-connect-sync-feature-prevent-accidental-deletes.md) 및 [Azure AD Connect Health](how-to-connect-health-sync.md)를 설치하여 사용할 수 있는 이러한 기능에 대해 자세히 알아봅니다.

공통 항목인 [스케줄러 및 동기화를 트리거하는 방법](how-to-connect-sync-feature-scheduler.md)에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
