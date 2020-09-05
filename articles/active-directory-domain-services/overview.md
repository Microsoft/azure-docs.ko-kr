---
title: Azure Active Directory Domain Services 개요 | Microsoft Docs
description: 이 개요에서는 Azure Active Directory Domain Services에서 제공하는 기능과 조직에서 이를 사용하여 ID 서비스를 클라우드의 애플리케이션 및 서비스에 제공하는 방법을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: overview
ms.date: 08/14/2020
ms.author: iainfou
ms.custom: contperfq1
ms.openlocfilehash: 2255f2193378d0cc3611680c6aa5f8e0837e99aa
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88245181"
---
# <a name="what-is-azure-active-directory-domain-services"></a>Azure Active Directory Domain Services란?

Azure AD DS(Active Directory Domain Services)는 도메인 조인, 그룹 정책, LDAP(Lightweight Directory Access Protocol) 및 Kerberos/NTLM 인증과 같은 관리되는 도메인 서비스를 제공합니다. 클라우드에서 DC(도메인 컨트롤러)를 배포, 관리 및 패치하지 않고도 이러한 도메인 서비스를 사용합니다.

Azure AD DS 관리형 도메인을 사용하면 최신 인증 방법을 사용할 수 없거나 디렉터리 조회가 항상 온-프레미스 AD DS 환경으로 돌아가지 않도록 하는 레거시 애플리케이션을 클라우드에서 실행할 수 있습니다. 클라우드에서 AD DS 환경을 관리할 필요 없이 레거시 애플리케이션을 온-프레미스 환경에서 관리되는 도메인으로 리프트 앤 시프트할 수 있습니다.

Azure AD DS는 기존 Azure AD 테넌트와 통합됩니다. 이러한 통합을 통해 사용자는 기존 자격 증명을 사용하여 관리되는 도메인에 연결된 서비스 및 애플리케이션에 로그인 할 수 있습니다. 또한 기존 그룹 및 사용자 계정을 사용하여 리소스에 대한 액세스를 보호할 수도 있습니다. 이러한 기능은 Azure에 대한 온-프레미스 리소스의 원활한 리프트 앤 시프트를 제공합니다.

> [!div class="nextstepaction"]
> [시작하려면 Azure Portal을 사용하여 Azure AD DS 관리형 도메인을 만듭니다][tutorial-create].

## <a name="how-does-azure-ad-ds-work"></a>Azure AD DS의 작동 방식

Azure AD DS 관리되는 도메인을 만들 때 고유한 네임스페이스를 정의합니다. 이 네임스페이스는 *aaddscontoso.com*과 같은 도메인 이름입니다. 그런 다음, 두 개의 Windows Server DC(도메인 컨트롤러)가 선택한 Azure 지역에 배포됩니다. 이 DC 배포를 복제본 세트라고 합니다.

이러한 DC를 관리, 구성 또는 업데이트할 필요가 없습니다. Azure 플랫폼은 백업을 포함하여 관리되는 도메인의 일부로 DC를 처리합니다.

관리되는 도메인은 Azure AD에서 단방향 동기화를 수행하여 한 곳의 사용자, 그룹 및 자격 증명 세트에 대한 액세스를 제공하도록 구성되어 있습니다. 관리되는 도메인에서 리소스를 직접 만들 수 있지만, Azure AD와 다시 동기화되지는 않습니다. 그러면 관리되는 도메인에 연결되는 Azure의 애플리케이션, 서비스 및 VM에서 도메인 조인, 그룹 정책, LDAP 및 Kerberos/NTLM 인증과 같은 일반적인 AD DS 기능을 사용할 수 있습니다.

온-프레미스 AD DS 환경이 포함된 하이브리드 환경에서 [Azure AD Connect][azure-ad-connect]는 ID 정보를 Azure AD와 동기화한 다음, 관리되는 도메인에 동기화됩니다.

![Azure AD Domain Services에서 Azure AD Connect를 사용하여 Azure AD 및 온-프레미스 AD DS와 동기화](./media/active-directory-domain-services-design-guide/sync-topology.png)

Azure AD DS는 Azure AD에서 ID 정보를 복제하므로 클라우드 전용이거나 온-프레미스 AD DS 환경과 동기화된 Azure AD 테넌트에서 작동합니다. 두 환경에는 모두 동일한 Azure AD DS 기능 세트가 있습니다.

* 기존 온-프레미스 AD DS 환경이 있으면 사용자 계정 정보를 동기화하여 사용자에게 일관된 ID를 제공할 수 있습니다. 자세한 내용은 [관리되는 도메인에서 개체 및 자격 증명을 동기화하는 방법][synchronization]을 참조하세요.
* 클라우드 전용 환경의 경우 기존의 온-프레미스 AD DS 환경은 Azure AD DS의 중앙 집중식 ID 서비스를 사용하는 데 필요하지 않습니다.

관리되는 도메인을 확장하여 Azure AD 테넌트마다 복제본 세트를 2개 이상 포함할 수 있습니다. Azure AD DS을 지원하는 Azure 지역의 피어링된 가상 네트워크에 복제본 세트를 추가할 수 있습니다. 다른 Azure 지역에 있는 추가 복제본 세트는 Azure 지역이 오프라인 상태가 될 때 레거시 애플리케이션에 대한 지리적 재해 복구를 제공합니다. 복제본 세트는 현재 미리 보기로 제공됩니다. 자세한 내용은 [관리되는 도메인에 대한 복제본 세트 개념 및 기능][concepts-replica-sets]을 참조하세요.

다음 비디오에서는 Azure AD DS가 애플리케이션 및 워크로드와 통합되어 클라우드에서 ID 서비스를 제공하는 방법에 대해 간략히 설명합니다.

<br />

>[!VIDEO https://www.youtube.com/embed/T1Nd9APNceQ]

작동 중인 Azure AD DS 배포 시나리오를 보려면 다음 예제를 탐색하면 됩니다.

* [하이브리드 조직을 위한 Azure AD DS](scenarios.md#azure-ad-ds-for-hybrid-organizations)
* [클라우드 전용 조직을 위한 Azure AD DS](scenarios.md#azure-ad-ds-for-cloud-only-organizations)

## <a name="azure-ad-ds-features-and-benefits"></a>Azure AD DS 기능 및 이점

ID 서비스를 클라우드의 애플리케이션 및 VM에 제공하기 위해 Azure AD DS는 도메인 조인, LDAPS(보안 LDAP), 그룹 정책, DNS 관리, LDAP 바인딩 및 읽기 지원과 같은 작업에서 기존 AD DS 환경과 완벽하게 호환됩니다. LDAP 쓰기 지원은 관리되는 도메인에서 만든 개체에 사용할 수 있지만 Azure AD에서 동기화된 리소스에는 사용할 수 없습니다.

ID 옵션에 대해 자세히 알아보려면 [Azure AD DS를 Azure AD, Azure VM의 AD DS 및 온-프레미스 AD DS와 비교][compare]하세요.

배포 및 관리 작업을 간소화하는 Azure AD DS의 기능은 다음과 같습니다.

* **간소화된 배포 환경:** Azure AD DS는 Azure Portal에서 단일 마법사를 사용하여 Azure AD 테넌트에서 사용하도록 설정됩니다.
* **Azure AD와 통합:** 사용자 계정, 그룹 멤버 자격 및 자격 증명은 Azure AD 테넌트에서 자동으로 사용할 수 있습니다. Azure AD 테넌트 또는 온-프레미스 AD DS 환경에서 새 사용자, 그룹 또는 특성 변경 내용은 Azure AD DS에 자동으로 동기화됩니다.
    * Azure AD에 연결된 외부 디렉터리의 계정은 Azure AD DS에서 사용할 수 없습니다. 이러한 외부 디렉터리에 대해 자격 증명을 사용할 수 없으므로 관리되는 도메인으로 동기화할 수 없습니다.
* **회사 자격 증명/암호 사용:** Azure AD DS의 사용자 암호는 Azure AD 테넌트와 동일합니다. 사용자는 도메인 조인 머신에 대해 회사 자격 증명을 사용할 수 있고, 대화형으로 또는 원격 데스크톱을 통해 로그인할 수 있으며 관리되는 도메인에 대해 인증할 수 있습니다.
* **NTLM 및 Kerberos 인증:** NTLM 및 Kerberos 인증 지원을 사용하면 Windows 통합 인증을 사용하는 애플리케이션을 배포할 수 있습니다.
* **고가용성:** Azure AD DS에는 고가용성을 관리형 도메인에 제공하는 여러 도메인 컨트롤러가 포함되어 있습니다. 이 고가용성은 서비스 가동 시간과 장애 복구를 보장합니다.
    * [Azure 가용성 영역][availability-zones]을 지원하는 지역에서는 이러한 도메인 컨트롤러도 추가 복원력을 위해 영역 간에 분산됩니다.
    * [복제본 세트][concepts-replica-sets]는 Azure 지역이 오프라인 상태가 될 때 레거시 애플리케이션에 대한 지리적 재해 복구를 제공하는 데도 사용할 수 있습니다.

관리되는 도메인의 몇 가지 주요 측면은 다음을 포함합니다.

* 관리된 도메인은 독립 실행형 도메인입니다. 온-프레미스 도메인의 확장이 아닙니다.
    * 필요한 경우 Azure AD DS에서 온-프레미스 AD DS 환경으로 단방향 아웃바운드 포리스트 트러스트를 만들 수 있습니다. 자세한 내용은 [Azure AD DS에 대한 리소스 포리스트 개념 및 기능][ forest-trusts]을 참조하세요.
* IT 팀은 이 관리되는 도메인에 대한 도메인 컨트롤러를 관리, 패치 또는 모니터링할 필요가 없습니다.

AD DS를 온-프레미스에서 실행하는 하이브리드 환경의 경우 관리되는 도메인에 대한 AD 복제를 관리할 필요가 없습니다. 온-프레미스 디렉터리의 사용자 계정, 그룹 멤버 자격 및 자격 증명은 [Azure AD Connect][azure-ad-connect]를 통해 Azure AD에 동기화됩니다. 이러한 사용자 계정, 그룹 멤버 자격 및 자격 증명은 자동으로 관리된 도메인 내에서 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Azure AD DS를 다른 ID 솔루션과 비교하고 동기화가 작동하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure AD DS와 Azure AD, Azure VM의 Active Directory Domain Services 및 온-프레미스 Active Directory Domain Services의 비교][compare]
* [Azure AD Domain Services에서 Azure AD 디렉터리와 동기화하는 방법][synchronization] 알아보기
* 관리되는 도메인 관리자에 대한 자세한 내용은 [Azure AD DS에서 사용자 계정, 암호 및 관리에 대한 관리 개념][administration-concepts]을 참조하세요.

시작하려면 [Azure Portal을 사용하여 관리되는 도메인을 만듭니다][tutorial-create].

<!-- INTERNAL LINKS -->
[compare]: compare-identity-solutions.md
[synchronization]: synchronization.md
[tutorial-create]: tutorial-create-instance.md
[azure-ad-connect]: ../active-directory/hybrid/whatis-azure-ad-connect.md
[password-hash-sync]: ../active-directory/hybrid/how-to-connect-password-hash-synchronization.md
[availability-zones]: ../availability-zones/az-overview.md
[forest-trusts]: concepts-resource-forest.md
[administration-concepts]: administration-concepts.md
[synchronization]: synchronization.md
[concepts-replica-sets]: concepts-replica-sets.md
