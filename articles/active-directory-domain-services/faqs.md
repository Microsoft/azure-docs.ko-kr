---
title: Azure AD Domain Services에 대 한 질문과 대답 Microsoft Docs
description: Azure Active Directory Domain Services에 대 한 구성, 관리 및 가용성과 관련 된 몇 가지 질문과 대답을 읽고 이해 합니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 48731820-9e8c-4ec2-95e8-83dba1e58775
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 06/05/2020
ms.author: iainfou
ms.openlocfilehash: 6a18dbf5c00c3f3aba2b2d58f060856aba9fb080
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88722900"
---
# <a name="frequently-asked-questions-faqs-about-azure-active-directory-ad-domain-services"></a>AD (Azure Active Directory) 도메인 서비스에 대 한 Faq (질문과 대답)

이 페이지는 Azure Active Directory Domain Services에 대 한 자주 묻는 질문에 답변 합니다.

## <a name="configuration"></a>구성

* [단일 Azure AD 디렉터리에 여러 관리되는 도메인을 만들 수 있나요?](#can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory)
* [클래식 가상 네트워크에서 Azure AD Domain Services를 사용 하도록 설정할 수 있나요?](#can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network)
* [Azure Resource Manager 가상 네트워크에서 Azure AD Domain Services를 사용할 수 있습니까?](#can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network)
* [기존 관리 되는 도메인을 클래식 가상 네트워크에서 리소스 관리자 가상 네트워크로 마이그레이션할 수 있나요?](#can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network)
* [Azure CSP(클라우드 솔루션 공급자) 구독에서 Azure AD Domain Services를 사용할 수 있나요?](#can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription)
* [페더레이션된 Azure AD 디렉터리에서 Azure AD Domain Services를 사용 하도록 설정할 수 있나요? 암호 해시를 Azure AD와 동기화 하지 않습니다. 이 디렉터리에 대해 Azure AD Domain Services를 사용 하도록 설정할 수 있나요?](#can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory)
* [Azure AD Domain Services를 내 구독 내의 여러 가상 네트워크에서 사용할 수 있나요?](#can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription)
* [PowerShell을 사용하여 Azure AD 도메인 서비스를 사용할 수 있나요?](#can-i-enable-azure-ad-domain-services-using-powershell)
* [Resource Manager 템플릿을 사용하여 Azure AD Domain Services를 사용할 수 있나요?](#can-i-enable-azure-ad-domain-services-using-a-resource-manager-template)
* [Azure AD 도메인 서비스 관리되는 도메인에 도메인 컨트롤러를 추가할 수 있나요?](#can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain)
* [내 디렉터리에 초대된 게스트 사용자가 Azure AD Domain Services를 사용할 수 있나요?](#can-guest-users-invited-to-my-directory-use-azure-ad-domain-services)
* [기존 Azure AD Domain Services 관리 되는 도메인을 다른 구독, 리소스 그룹, 지역 또는 가상 네트워크로 이동할 수 있나요?](#can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network)
* [고가용성 옵션을 포함 Azure AD Domain Services?](#does-azure-ad-domain-services-include-high-availability-options)

### <a name="can-i-create-multiple-managed-domains-for-a-single-azure-ad-directory"></a>단일 Azure AD 디렉터리에 여러 관리되는 도메인을 만들 수 있나요?
아니요. 단일 Azure AD 디렉터리에 Azure AD Domain Services에서 제공하는 단일 관리되는 도메인만 만들 수 있습니다.

### <a name="can-i-enable-azure-ad-domain-services-in-a-classic-virtual-network"></a>클래식 가상 네트워크에서 Azure AD Domain Services를 사용 하도록 설정할 수 있나요?
클래식 가상 네트워크는 새 배포에 대해 지원 되지 않습니다. 클래식 가상 네트워크에 배포 된 기존의 관리 되는 도메인은 2023 년 3 월 1 일에 사용 중지 될 때까지 계속 지원 됩니다. 기존 배포의 경우 [클래식 가상 네트워크 모델에서 리소스 관리자로 Azure AD Domain Services을 마이그레이션할](migrate-from-classic-vnet.md)수 있습니다.

자세한 내용은 공식 사용 중단 [알림](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)을 참조 하세요.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-resource-manager-virtual-network"></a>Azure Resource Manager 가상 네트워크에서 Azure AD Domain Services를 사용할 수 있습니까?
예. Azure Resource Manager 가상 네트워크에서 Azure AD Domain Services를 사용할 수 있습니다. 클래식 Azure 가상 네트워크는 관리 되는 도메인을 만들 때 더 이상 사용할 수 없습니다.

### <a name="can-i-migrate-my-existing-managed-domain-from-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>기존 관리 되는 도메인을 클래식 가상 네트워크에서 리소스 관리자 가상 네트워크로 마이그레이션할 수 있나요?
예. 자세한 내용은 [클래식 가상 네트워크 모델에서 리소스 관리자로 Azure AD Domain Services 마이그레이션](migrate-from-classic-vnet.md)을 참조 하세요.

### <a name="can-i-enable-azure-ad-domain-services-in-an-azure-csp-cloud-solution-provider-subscription"></a>Azure CSP(클라우드 솔루션 공급자) 구독에서 Azure AD Domain Services를 사용할 수 있나요?
예. 자세한 내용은 [AZURE CSP 구독에서 Azure AD Domain Services를 사용 하도록 설정 하는 방법](csp.md)을 참조 하세요.

### <a name="can-i-enable-azure-ad-domain-services-in-a-federated-azure-ad-directory-i-do-not-synchronize-password-hashes-to-azure-ad-can-i-enable-azure-ad-domain-services-for-this-directory"></a>페더레이션된 Azure AD 디렉터리에서 Azure AD Domain Services를 사용할 수 있습니까? 암호 해시를 Azure AD와 동기화하지 않았습니다. 이 디렉터리에 Azure AD Domain Services를 사용할 수 있습니까?
아니요. NTLM 또는 Kerberos를 통해 사용자를 인증 하려면 사용자 계정의 암호 해시에 액세스 해야 Azure AD Domain Services. 페더레이션된 디렉터리에서 암호 해시는 Azure AD 디렉터리에 저장 되지 않습니다. 따라서 Azure AD Domain Services는 이러한 Azure AD 디렉터리에서 작동 하지 않습니다.

그러나 암호 해시 동기화에 Azure AD Connect를 사용 하는 경우 암호 해시 값이 Azure AD에 저장 되므로 Azure AD Domain Services를 사용할 수 있습니다.

### <a name="can-i-make-azure-ad-domain-services-available-in-multiple-virtual-networks-within-my-subscription"></a>Azure AD Domain Services를 내 구독 내의 여러 가상 네트워크에서 사용할 수 있나요?
서비스 자체는이 시나리오를 직접 지원 하지 않습니다. 관리되는 도메인은 한 번에 하나의 가상 네트워크에서만 사용할 수 있습니다. 그러나 다른 가상 네트워크에 Azure AD Domain Services를 노출 하기 위해 여러 가상 네트워크 간의 연결을 구성할 수 있습니다. 자세한 내용은 VPN 게이트웨이 또는 [가상 네트워크 피어 링](../virtual-network/virtual-network-peering-overview.md)을 [사용 하 여 Azure에서 가상 네트워크를 연결 하는 방법](../vpn-gateway/vpn-gateway-howto-vnet-vnet-portal-classic.md) 을 참조 하세요.

### <a name="can-i-enable-azure-ad-domain-services-using-powershell"></a>PowerShell을 사용하여 Azure AD 도메인 서비스를 사용할 수 있나요?
예. 자세한 내용은 [PowerShell을 사용 하 여 Azure AD Domain Services를 사용 하도록 설정 하는 방법](powershell-create-instance.md)을 참조 하세요.

### <a name="can-i-enable-azure-ad-domain-services-using-a-resource-manager-template"></a>Resource Manager 템플릿을 사용하여 Azure AD Domain Services를 사용할 수 있나요?
예, 리소스 관리자 템플릿을 사용 하 여 Azure AD Domain Services 관리 되는 도메인을 만들 수 있습니다. 템플릿을 배포 하기 전에 Azure Portal 또는 Azure PowerShell를 사용 하 여 관리를 위한 서비스 사용자 및 Azure AD 그룹을 만들어야 합니다. 자세한 내용은 [Azure Resource Manager 템플릿을 사용 하 여 Azure AD DS 관리 되는 도메인 만들기](template-create-instance.md)를 참조 하세요. Azure Portal에서 Azure AD Domain Services 관리 되는 도메인을 만드는 경우 추가 배포와 함께 사용할 템플릿을 내보낼 수도 있습니다.

### <a name="can-i-add-domain-controllers-to-an-azure-ad-domain-services-managed-domain"></a>Azure AD 도메인 서비스 관리되는 도메인에 도메인 컨트롤러를 추가할 수 있나요?
아니요. Azure AD 도메인 서비스에서 제공하는 도메인은 관리되는 도메인입니다. 이 도메인에 대해 도메인 컨트롤러를 프로 비전, 구성 또는 관리 하지 않아도 됩니다. 이러한 관리 활동은 Microsoft에서 서비스로 제공 됩니다. 따라서 관리 되는 도메인에 대 한 추가 도메인 컨트롤러 (읽기-쓰기 또는 읽기 전용)를 추가할 수 없습니다.

### <a name="can-guest-users-invited-to-my-directory-use-azure-ad-domain-services"></a>내 디렉터리에 초대된 게스트 사용자가 Azure AD Domain Services를 사용할 수 있나요?
아니요. [Azure AD B2B](../active-directory/external-identities/what-is-b2b.md) 초대 프로세스를 사용하여 Azure AD 디렉터리에 초대된 게스트 사용자는 Azure AD Domain Services 관리되는 도메인과 동기화됩니다. 그러나 이러한 사용자에 대 한 암호는 Azure AD 디렉터리에 저장 되지 않습니다. 따라서 Azure AD Domain Services는 이러한 사용자에 대 한 NTLM 및 Kerberos 해시를 관리 되는 도메인으로 동기화 할 방법이 없습니다. 이러한 사용자는 컴퓨터에 로그인 하거나 관리 되는 도메인에 컴퓨터를 가입 시킬 수 없습니다.

### <a name="can-i-move-an-existing-azure-ad-domain-services-managed-domain-to-a-different-subscription-resource-group-region-or-virtual-network"></a>기존 Azure AD Domain Services 관리 되는 도메인을 다른 구독, 리소스 그룹, 지역 또는 가상 네트워크로 이동할 수 있나요?
아니요. Azure AD Domain Services 관리 되는 도메인을 만든 후에는 관리 되는 도메인을 다른 리소스 그룹, 가상 네트워크, 구독 등으로 이동할 수 없습니다. 관리 되는 도메인을 배포할 때 가장 적절 한 구독, 리소스 그룹, 지역 및 가상 네트워크를 선택 합니다.

### <a name="does-azure-ad-domain-services-include-high-availability-options"></a>고가용성 옵션을 포함 Azure AD Domain Services?

예. 각 Azure AD Domain Services 관리 되는 도메인에는 두 개의 도메인 컨트롤러가 포함 됩니다. 이러한 도메인 컨트롤러를 관리 하거나 연결 하지 않아도 관리 서비스의 일부입니다. 가용성 영역를 지 원하는 지역에 Azure AD Domain Services를 배포 하는 경우 도메인 컨트롤러는 영역 간에 분산 됩니다. 가용성 영역를 지원 하지 않는 지역에서 도메인 컨트롤러는 가용성 집합에 분산 됩니다. 이 배포에 대 한 구성 옵션 또는 관리 제어 권한이 없습니다. 자세한 내용은 [Azure의 가상 컴퓨터에 대 한 가용성 옵션](../virtual-machines/availability.md)을 참조 하세요.

## <a name="administration-and-operations"></a>관리 및 운영

* [원격 데스크톱을 사용하여 관리되는 도메인의 도메인 컨트롤러에 연결할 수 있습니까?](#can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop)
* [Azure AD Domain Services를 사용 하도록 설정 했습니다. 이 도메인에 컴퓨터를 도메인에 가입 시키는 데 어떤 사용자 계정을 사용 하나요?](#ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain)
* [Azure AD Domain Services에서 제공하는 관리되는 도메인에 대한 도메인 관리자 권한이 부여됩니까?](#do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services)
* [관리되는 도메인에서 LDAP 또는 다른 AD 관리 도구를 사용하여 그룹 멤버 자격을 수정할 수 있습니까?](#can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains)
* [Azure AD 디렉터리에 적용한 변경 사항이 내 관리되는 도메인에 표시되는 데 얼마나 걸리나요?](#how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain)
* [Azure AD Domain Services에서 제공하는 관리되는 도메인의 스키마를 확장할 수 있습니까?](#can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services)
* [관리되는 도메인에서 DNS 레코드를 수정하거나 추가할 수 있습니까?](#can-i-modify-or-add-dns-records-in-my-managed-domain)
* [관리되는 도메인에 대한 암호 수명 정책은 무엇인가요?](#what-is-the-password-lifetime-policy-on-a-managed-domain)
* [Azure AD Domain Services에서 AD 계정 잠금 보호를 제공하나요?](#does-azure-ad-domain-services-provide-ad-account-lockout-protection)
* [Azure AD Domain Services 내에서 분산 파일 시스템 (DFS) 및 복제를 구성할 수 있나요?](#can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services)

### <a name="can-i-connect-to-the-domain-controller-for-my-managed-domain-using-remote-desktop"></a>원격 데스크톱을 사용하여 관리되는 도메인의 도메인 컨트롤러에 연결할 수 있습니까?
아니요. 원격 데스크톱을 사용 하 여 관리 되는 도메인에 대 한 도메인 컨트롤러에 연결할 수 있는 권한이 없습니다. *AAD DC 관리자* 그룹의 멤버는 adac (Active Directory 관리 센터) 또는 ad PowerShell과 같은 ad 관리 도구를 사용 하 여 관리 되는 도메인을 관리할 수 있습니다. 이러한 도구는 관리 되는 도메인에 가입 된 Windows Server에서 *원격 서버 관리 도구* 기능을 사용 하 여 설치 됩니다. 자세한 내용은 [관리 VM을 만들어 Azure AD Domain Services 관리 되는 도메인 구성 및](tutorial-create-management-vm.md)관리를 참조 하세요.

### <a name="ive-enabled-azure-ad-domain-services-what-user-account-do-i-use-to-domain-join-machines-to-this-domain"></a>Azure AD Domain Services를 사용 하도록 설정 했습니다. 이 도메인에 도메인 가입 컴퓨터를 사용하려면 사용자 계정은 무엇입니까?
관리 되는 도메인의 일부인 모든 사용자 계정은 VM에 가입할 수 있습니다. *AAD DC Administrators* 그룹의 구성원에 게는 관리 되는 도메인에 가입 된 컴퓨터에 대 한 원격 데스크톱 액세스가 부여 됩니다.

### <a name="do-i-have-domain-administrator-privileges-for-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services에서 제공하는 관리되는 도메인에 대한 도메인 관리자 권한이 부여됩니까?
아니요. 관리 되는 도메인에 대 한 관리 권한이 부여 되지 않았습니다. 도메인 *관리자* 및 *엔터프라이즈 관리자* 권한은 도메인 내에서 사용할 수 없습니다. 온-프레미스 Active Directory 도메인 관리자 또는 엔터프라이즈 관리자 그룹의 멤버에 게는 관리 되는 도메인에 대 한 도메인/엔터프라이즈 관리자 권한도 부여 되지 않습니다.

### <a name="can-i-modify-group-memberships-using-ldap-or-other-ad-administrative-tools-on-managed-domains"></a>관리되는 도메인에서 LDAP 또는 다른 AD 관리 도구를 사용하여 그룹 멤버 자격을 수정할 수 있습니까?
Azure Active Directory에서 Azure AD Domain Services로 동기화 된 사용자 및 그룹은 원본의 원본이 Azure Active Directory 되므로 수정할 수 없습니다. 관리 되는 도메인에서 시작 되는 모든 사용자 또는 그룹을 수정할 수 있습니다.

### <a name="how-long-does-it-take-for-changes-i-make-to-my-azure-ad-directory-to-be-visible-in-my-managed-domain"></a>Azure AD 디렉터리에 적용한 변경 사항이 내 관리되는 도메인에 표시되는 데 얼마나 걸리나요?
Azure AD UI 또는 PowerShell을 사용 하 여 Azure AD 디렉터리에서 변경한 내용은 관리 되는 도메인에 자동으로 동기화 됩니다. 이 동기화 프로세스는 백그라운드에서 실행됩니다. 이 동기화가 모든 개체 변경을 완료 하는 데는 정의 된 기간이 없습니다.

### <a name="can-i-extend-the-schema-of-the-managed-domain-provided-by-azure-ad-domain-services"></a>Azure AD Domain Services에서 제공하는 관리되는 도메인의 스키마를 확장할 수 있습니까?
아니요. 스키마는 관리되는 도메인에 대해 Microsoft에서 관리합니다. Azure AD Domain Services에서 스키마 확장을 지원 하지 않습니다.

### <a name="can-i-modify-or-add-dns-records-in-my-managed-domain"></a>관리되는 도메인에서 DNS 레코드를 수정하거나 추가할 수 있습니까?
예. *AAD DC Administrators* 그룹의 구성원에 게는 관리 되는 도메인의 dns 레코드를 수정할 수 있는 *dns 관리자* 권한이 부여 됩니다. 이러한 사용자는 관리 되는 도메인에 가입 된 Windows Server를 실행 하는 컴퓨터에서 DNS 관리자 콘솔을 사용 하 여 DNS를 관리할 수 있습니다. DNS 관리자 콘솔을 사용 하려면 서버에 *원격 서버 관리 도구* 선택적 기능의 일부인 *dns 서버 도구*를 설치 합니다. 자세한 내용은 [관리 되는 Azure AD Domain Services 도메인에서 DNS 관리](manage-dns.md)를 참조 하세요.

### <a name="what-is-the-password-lifetime-policy-on-a-managed-domain"></a>관리되는 도메인에 대한 암호 수명 정책은 무엇인가요?
Azure AD Domain Services 관리되는 도메인의 기본 암호 수명은 90일입니다. 이 암호 수명은 Azure AD에 구성된 암호 수명과 동기화되지 않습니다. 따라서 사용자의 암호가 관리되는 도메인에는 만료되지만 Azure AD에서는 여전히 유효한 상황이 발생할 수 있습니다. 이러한 시나리오에서 사용자는 Azure AD에서 자신의 암호를 변경해야 하며, 새 암호는 관리되는 도메인과 동기화됩니다. 관리 되는 도메인에서 기본 암호 수명을 변경 하려면 [사용자 지정 암호 정책을 만들고 구성할](password-policy.md)수 있습니다.

또한 *DisablePasswordExpiration* 에 대 한 Azure AD 암호 정책은 관리 되는 도메인에 동기화 됩니다. Azure AD의 사용자에 *DisablePasswordExpiration* 가 적용 되 면 관리 되는 도메인의 동기화 된 사용자에 대 한 *UserAccountControl* 값이 적용 *DONT_EXPIRE_PASSWORD* .

사용자가 Azure AD에서 자신의 암호를 다시 설정 하면 *forceChangePasswordNextSignIn = True* 특성이 적용 됩니다. 관리 되는 도메인은 Azure AD에서이 특성을 동기화 합니다. 관리 되는 도메인에서 Azure AD의 동기화 된 사용자에 대해 *forceChangePasswordNextSignIn* 가 설정 된 경우 관리 되는 도메인의 *pwdlastset* 특성은 *0*으로 설정 되며,이는 현재 설정 된 암호를 무효화 합니다.

### <a name="does-azure-ad-domain-services-provide-ad-account-lockout-protection"></a>Azure AD Domain Services에서 AD 계정 잠금 보호를 제공하나요?
예. 관리되는 도메인에서 2분 안에 5차례 암호를 잘못 입력하면 사용자 계정이 30분 동안 잠깁니다. 30분 후 사용자 계정이 자동으로 잠금 해제됩니다. 관리 되는 도메인에 대 한 잘못 된 암호 시도는 Azure AD에서 사용자 계정을 잠그지 않습니다. 사용자 계정은 Azure AD Domain Services 관리되는 도메인 안에서만 잠깁니다. 자세한 내용은 [관리 되는 도메인의 암호 및 계정 잠금 정책](password-policy.md)을 참조 하세요.

### <a name="can-i-configure-distributed-file-system-and-replication-within-azure-ad-domain-services"></a>Azure AD Domain Services 내에서 분산 파일 시스템 및 복제를 구성할 수 있나요?
아니요. Azure AD Domain Services를 사용 하는 경우 분산 파일 시스템 (DFS) 및 복제를 사용할 수 없습니다.

## <a name="billing-and-availability"></a>요금 청구 및 가용성

* [Azure AD Domain Services는 유료 서비스인가요?](#is-azure-ad-domain-services-a-paid-service)
* [서비스에 대한 무료 평가판이 있습니까?](#is-there-a-free-trial-for-the-service)
* [Azure AD Domain Services 관리되는 도메인을 일시 중지할 수 있나요?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [Azure AD Domain Services를 DR 이벤트의 다른 지역으로 장애 조치(failover)할 수 있나요?](#can-i-pause-an-azure-ad-domain-services-managed-domain)
* [EMS (Enterprise Mobility Suite)의 일부로 Azure AD Domain Services를 가져올 수 있나요? Azure AD Domain Services을 사용 하는 Azure AD Premium 필요 합니까?](#can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event)
* [어떤 Azure 지역에서 서비스를 사용할 수 있습니까?](#can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services)

### <a name="is-azure-ad-domain-services-a-paid-service"></a>Azure AD Domain Services는 유료 서비스인가요?
예. 자세한 내용은 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory-ds/)를 참조하세요.

### <a name="is-there-a-free-trial-for-the-service"></a>서비스에 대한 무료 평가판이 있습니까?
Azure AD Domain Services는 Azure 무료 평가판에 포함 되어 있습니다. [Azure의 1개월 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)에 등록할 수 있습니다.

### <a name="can-i-pause-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리되는 도메인을 일시 중지할 수 있나요?
아니요. Azure AD Domain Services 관리 되는 도메인을 사용 하도록 설정 하면 관리 되는 도메인을 삭제할 때까지 선택한 가상 네트워크 내에서 서비스를 사용할 수 있습니다. 서비스를 일시 중지할 수 있는 방법은 없습니다. 관리되는 도메인을 삭제할 때까지 시간 기준으로 계속 청구됩니다.

### <a name="can-i-failover-azure-ad-domain-services-to-another-region-for-a-dr-event"></a>Azure AD Domain Services를 DR 이벤트의 다른 지역으로 장애 조치(failover)할 수 있나요?
아니요. Azure AD Domain Services은 현재 지역 중복 배포 모델을 제공 하지 않습니다. Azure 지역의 단일 가상 네트워크로 제한 됩니다. 여러 Azure 지역을 사용하려면 Azure IaaS VM에서 Active Directory 도메인 컨트롤러를 실행해야 합니다. 아키텍처 지침은 [온-프레미스 Active Directory 도메인을 Azure로 확장](/azure/architecture/reference-architectures/identity/adds-extend-domain)을 참조 하세요.

### <a name="can-i-get-azure-ad-domain-services-as-part-of-enterprise-mobility-suite-ems-do-i-need-azure-ad-premium-to-use-azure-ad-domain-services"></a>Enterprise Mobility Suite(EMS)의 일부로 Azure AD 도메인 서비스를 가져올 수 있습니까? Azure AD Domain Services를 사용하려면 Azure AD Premium이 필요합니까?
아니요. Azure AD Domain Services는 종 량 제 Azure 서비스 이며 EMS의 일부가 아닙니다. Azure AD Domain Services은 모든 버전의 Azure AD (무료 및 프리미엄)에서 사용할 수 있습니다. 사용량에 따라 시간 단위로 요금이 청구 됩니다.

### <a name="what-azure-regions-is-the-service-available-in"></a>어떤 Azure 지역에서 서비스를 사용할 수 있습니까?
Azure AD Domain Services를 사용할 수 있는 Azure 지역의 목록을 알아보려면 [지역별 Azure 서비스](https://azure.microsoft.com/regions/#services/) 페이지를 참조하세요.

## <a name="troubleshooting"></a>문제 해결

Azure AD Domain Services 구성 또는 관리에서 발생하는 일반적인 문제에 대한 솔루션은 [문제 해결 가이드](troubleshoot.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure AD Domain Services에 대 한 자세한 내용은 [Azure Active Directory Domain Services 무엇입니까?](overview.md)를 참조 하세요.

시작 하려면 [Azure Active Directory Domain Services 관리 되는 도메인 만들기 및 구성](tutorial-create-instance.md)을 참조 하세요.