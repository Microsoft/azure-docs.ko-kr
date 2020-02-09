---
title: Azure VMware 솔루션 (AVS)-Azure AD를 AVS 사설 클라우드의 id 원본으로 사용
description: Azure에서 AVS에 액세스 하는 사용자를 인증 하기 위해 Azure AD를 AVS 사설 클라우드의 id 공급자로 추가 하는 방법에 대해 설명 합니다.
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: a453d40f976d11a41e1ba536d2f7baab15900b13
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77015926"
---
# <a name="use-azure-ad-as-an-identity-provider-for-vcenter-on-avs-private-cloud"></a>Azure AD를 AVS의 id 공급자로 사용 하 여 AVS 사설 클라우드에서 vCenter

VMware 관리자가 vCenter에 액세스할 수 있도록 Azure AD (Azure Active Directory)를 사용 하 여 인증 하도록 AVS 사설 클라우드 vCenter를 설정할 수 있습니다. Single Sign-On id 원본이 설정 된 후 **cloudowner** 사용자는 id 원본에서 vCenter에 사용자를 추가할 수 있습니다.  

다음과 같은 방법으로 Active Directory 도메인 및 도메인 컨트롤러를 설정할 수 있습니다.

* 온-프레미스에서 실행 되는 도메인 컨트롤러 및 도메인 컨트롤러 Active Directory
* Azure 구독에서 가상 머신으로 Azure에서 실행 되는 도메인 및 도메인 컨트롤러 Active Directory
* 새 Active Directory 도메인 및 AVS 사설 클라우드에서 실행 되는 도메인 컨트롤러
* Azure Active Directory 서비스

이 가이드에서는 id 원본으로 Azure AD를 설정 하는 데 필요한 작업에 대해 설명 합니다. 온-프레미스 Active Directory 또는 Azure에서 실행 되는 Active Directory를 사용 하는 방법에 대 한 자세한 내용은 id 원본 설정에 대 한 자세한 지침은 [Active Directory 사용 하도록 vCenter id 원본 설정](set-vcenter-identity.md) 을 참조 하세요.

## <a name="about-azure-ad"></a>Azure AD 정보

Azure AD는 Microsoft 다중 테넌트, 클라우드 기반 디렉터리 및 id 관리 서비스입니다. Azure AD는 사용자가 Azure에서 다양 한 서비스를 인증 하 고 액세스 하는 데 사용할 수 있는 확장 가능 하 고 일관 되며 안정적인 인증 메커니즘을 제공 합니다. 또한 모든 타사 서비스에 대 한 보안 LDAP 서비스를 제공 하 여 Azure AD를 인증/id 원본으로 사용 합니다. Azure AD는 핵심 디렉터리 서비스, 고급 id 관리 및 응용 프로그램 액세스 관리를 결합 하 여, AVS 사설 클라우드를 관리 하는 사용자를 위해 AVS 사설 클라우드에 액세스를 제공 하는 데 사용할 수 있습니다.

VCenter를 사용 하 여 Azure AD를 id 원본으로 사용 하려면 Azure ad 및 Azure AD domain services를 설정 해야 합니다. 다음 지침을 따릅니다.

1. [Azure AD 및 Azure AD domain services를 설정 하는 방법](#set-up-azure-ad-and-azure-ad-domain-services)
2. [AVS 사설 클라우드 vCenter에서 id 원본을 설정 하는 방법](#set-up-an-identity-source-on-your-avs-private-cloud-vcenter)

## <a name="set-up-azure-ad-and-azure-ad-domain-services"></a>Azure AD 및 Azure AD domain services 설정

시작 하기 전에 전역 관리자 권한으로 Azure 구독에 액세스 해야 합니다. 다음 단계는 일반 지침을 제공 합니다. 자세한 내용은 Azure 설명서에 포함 되어 있습니다.

### <a name="azure-ad"></a>Azure AD

> [!NOTE]
> Azure AD가 이미 있는 경우이 섹션을 건너뛸 수 있습니다.

1. [AZURE ad 설명서](../active-directory/fundamentals/get-started-azure-ad.md)에 설명 된 대로 구독에서 azure ad를 설정 합니다.
2. [Azure Active Directory Premium 등록](../active-directory/fundamentals/active-directory-get-started-premium.md)에 설명 된 대로 구독에서 Azure Active Directory Premium를 사용 하도록 설정 합니다.
3. [Azure Active Directory에](../active-directory/fundamentals/add-custom-domain.md)사용자 지정 도메인 이름 추가에 설명 된 대로 사용자 지정 도메인 이름을 설정 하 고 사용자 지정 도메인 이름을 확인 합니다.
    1. Azure에서 제공 하는 정보를 사용 하 여 도메인 등록 기관에서 DNS 레코드를 설정 합니다.
    2. 사용자 지정 도메인 이름을 주 도메인으로 설정 합니다.

필요에 따라 다른 Azure AD 기능을 구성할 수 있습니다. Azure AD에서 vCenter 인증을 사용 하도록 설정 하는 데 필요 하지 않습니다.

### <a name="azure-ad-domain-services"></a>Azure AD 도메인 서비스

> [!NOTE]
> 이는 vCenter의 id 원본으로 Azure AD를 사용 하도록 설정 하는 중요 한 단계입니다. 문제를 방지 하려면 모든 단계가 제대로 수행 되었는지 확인 합니다.

1. [Azure Portal를 사용 하 여 Azure Active Directory 도메인 서비스](../active-directory-domain-services/active-directory-ds-getting-started.md)사용에 설명 된 대로 Azure AD domain services를 사용 하도록 설정 합니다.
2. [Azure Portal를 사용 하 여 Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-network.md)사용에 설명 된 대로 Azure AD domain services에서 사용할 네트워크를 설정 합니다.
3. [Azure Portal를 사용 하 여 Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-admingroup.md)사용에 설명 된 대로 Azure AD Domain Services 관리를 위한 관리자 그룹을 구성 합니다.
4. [Azure Active Directory Domain Services 사용](../active-directory-domain-services/active-directory-ds-getting-started-dns.md)에 설명 된 대로 Azure AD Domain Services에 대 한 DNS 설정을 업데이트 합니다.  인터넷을 통해 AD에 연결 하려는 경우 Azure AD 도메인 서비스의 공용 IP 주소에 대 한 DNS 레코드를 도메인 이름으로 설정 합니다.
5. 사용자에 대 한 암호 해시 동기화를 사용 하도록 설정 합니다. 이 단계에서는 NTLM (NT LAN Manager) 및 Kerberos 인증에 필요한 암호 해시의 동기화를 Azure AD Domain Services 합니다. 암호 해시 동기화를 설정하면 사용자는 회사 자격 증명을 사용하여 관리되는 도메인에 로그인할 수 있습니다. [Azure Active Directory Domain Services에 암호 해시 동기화 사용을](../active-directory-domain-services/active-directory-ds-getting-started-password-sync.md)참조 하세요.
    1. 클라우드 전용 사용자가 있는 경우 암호 해시가 NTLM 또는 Kerberos에서 요구 하는 형식으로 저장 되도록 하려면 <a href="http://myapps.microsoft.com/" target="_blank">AZURE AD 액세스 패널</a> 을 사용 하 여 암호를 변경 해야 합니다. [클라우드 전용 사용자 계정에 대 한 관리 되는 도메인에 대 한 암호 해시 동기화 사용](../active-directory-domain-services/tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)의 지침을 따릅니다. Azure Portal 또는 Azure AD PowerShell cmdlet을 사용 하 여 Azure AD 디렉터리에서 만들어진 개별 사용자 및 새 사용자에 대해이 단계를 수행 해야 합니다. Azure AD 도메인 서비스에 액세스 해야 하는 사용자는 <a href="http://myapps.microsoft.com/" target="_blank">AZURE ad 액세스 패널</a> 을 사용 하 고 자신의 프로필에 액세스 하 여 암호를 변경 해야 합니다.

        > [!NOTE]
        > 조직에 클라우드 전용 사용자 계정이 있는 경우 Azure Active Directory Domain Services를 사용 해야 하는 모든 사용자는 암호를 변경 해야 합니다. 클라우드 전용 사용자 계정은 Azure Portal 또는 Azure AD PowerShell cmdlet을 사용하여 Azure AD 디렉터리에 만든 계정입니다. 이러한 사용자 계정은 온-프레미스 디렉터리에서 동기화되지 않습니다.

    2. 온-프레미스 Active directory에서 암호를 동기화 하는 경우 [Active Directory 설명서](../active-directory-domain-services/active-directory-ds-getting-started-password-sync-synced-tenant.md)의 단계를 따르세요.

6.  [Azure AD Domain Services 관리 되는 도메인에 대해 보안 ldap (LDAPS) 구성](../active-directory-domain-services/tutorial-configure-ldaps.md)에 설명 된 대로 Azure Active Directory Domain Services에서 보안 ldap를 구성 합니다.
    1. Azure 토픽 [보안 ldap에 대 한 인증서 가져오기](../active-directory-domain-services/tutorial-configure-ldaps.md#create-a-certificate-for-secure-ldap)에 설명 된 대로 보안 ldap에서 사용할 인증서를 업로드 합니다. AVS는 인증 기관에서 발급 한 서명 된 인증서를 사용 하 여 vCenter에서 인증서를 신뢰할 수 있는지 확인 하는 것이 좋습니다.
    2. 에 설명 된 대로 보안 LDAP 사용 [Azure AD Domain Services 관리 되는 도메인에 대해 보안 ldap (LDAPS)를 사용 하도록 설정](../active-directory-domain-services/tutorial-configure-ldaps.md)합니다.
    3. Id 원본을 구성 하는 동안 vCenter에서 사용할 .cer 형식으로 인증서의 공개 부분 (개인 키 제외)을 저장 합니다.
    4. Azure AD 도메인 서비스에 대 한 인터넷 액세스가 필요한 경우 ' 인터넷을 통해 LDAP에 보안 액세스 허용 ' 옵션을 사용 하도록 설정 합니다.
    5. TCP 포트 636에 대 한 Azure AD 도메인 서비스 NSG에 대 한 인바운드 보안 규칙을 추가 합니다.

## <a name="set-up-an-identity-source-on-your-avs-private-cloud-vcenter"></a>AVS 사설 클라우드 vCenter에서 id 원본 설정

1. AVS 사설 클라우드 vCenter에 대 한 [권한을 에스컬레이션](escalate-private-cloud-privileges.md) 합니다.
2. Id 원본을 설정 하는 데 필요한 구성 매개 변수를 수집 합니다.

    | **옵션** | **설명** |
    |------------|-----------------|
    | **이름** | Id 원본의 이름입니다. |
    | **사용자의 기본 DN** | 사용자의 기본 고유 이름입니다.  Azure AD의 경우 `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` 예제: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`을 사용 합니다.|
    | **도메인 이름** | 도메인의 FQDN (예: example.com)입니다. 이 텍스트 상자에 IP 주소를 제공 하지 마십시오. |
    | **도메인 별칭** | *(선택 사항)* 도메인 NetBIOS 이름입니다. SSPI 인증을 사용 하는 경우 Active Directory 도메인의 NetBIOS 이름을 id 원본의 별칭으로 추가 합니다. |
    | **그룹의 기본 DN** | 그룹에 대 한 기본 고유 이름입니다. Azure AD의 경우 다음을 사용 합니다. `OU=AADDC Users,DC=<domain>,DC=<domain suffix>` 예: `OU=AADDC Users,DC=cloudsimplecustomer,DC=com`|
    | **주 서버 URL** | 도메인에 대 한 주 도메인 컨트롤러 LDAP 서버입니다.<br><br> `ldaps://hostname:port`형식을 사용 합니다. 포트는 일반적으로 LDAPS 연결의 636입니다. <br><br>주 또는 보조 LDAP URL에서 `ldaps://` 를 사용 하는 경우 Active Directory 서버의 LDAPS 엔드포인트에 대 한 신뢰를 설정 하는 인증서가 필요 합니다. |
    | **보조 서버 URL** | 장애 조치 (failover)에 사용 되는 보조 도메인 컨트롤러 LDAP 서버의 주소입니다. |
    | **인증서 선택** | Active Directory LDAP 서버 또는 OpenLDAP 서버 id 원본에서 LDAPS를 사용 하려는 경우 URL 텍스트 상자에 `ldaps://` 를 입력 하면 **인증서 선택** 단추가 나타납니다. 보조 URL은 필요 하지 않습니다. |
    | **사용자 이름** | 도메인에서 사용자 및 그룹의 기본 DN에 대 한 읽기 전용 액세스 권한이 있는 사용자의 ID입니다. |
    | **암호** | 사용자 이름으로 지정 된 사용자의 암호입니다. |

3. 권한이 에스컬레이션 된 후에는 AVS 사설 클라우드 vCenter에 로그인 합니다.
4. 이전 단계의 값을 사용 하 여 [vCenter에서 id 원본 추가](set-vcenter-identity.md#add-an-identity-source-on-vcenter) 의 지침에 따라 Azure Active Directory를 id 원본으로 설정 합니다.
5. VMware 토픽 [Vcenter Single Sign-on 그룹에 멤버 추가](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html)에 설명 된 대로 Azure AD의 사용자/그룹을 vcenter 그룹에 추가 합니다.

> [!CAUTION]
> 새 사용자는 *클라우드-소유자-그룹*, *클라우드-글로벌-* 관리-그룹, 클라우드- *네트워크* -관리자- *그룹, 클라우드*-글로벌- *v m*i-관리 그룹에만 추가 해야 합니다.  *Administrators* 그룹에 추가 된 사용자는 자동으로 제거 됩니다.  서비스 계정만 *Administrators* 그룹에 추가 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [사설 클라우드 권한 모델에 대해 알아보기](learn-private-cloud-permissions.md)
