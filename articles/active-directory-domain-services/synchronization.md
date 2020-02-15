---
title: Azure AD Domain Services에서 동기화가 작동 하는 방식 | Microsoft Docs
description: Azure AD 테 넌 트 또는 온-프레미스 Active Directory Domain Services 환경에서 관리 되는 Azure Active Directory Domain Services 도메인으로 개체 및 자격 증명에 대 한 동기화 프로세스가 작동 하는 방식을 알아봅니다.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 57cbf436-fc1d-4bab-b991-7d25b6e987ef
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: iainfou
ms.openlocfilehash: b2a1bcedcc459a21bbc8a461ba9c8d9a8d65aebe
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132199"
---
# <a name="how-objects-and-credentials-are-synchronized-in-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services 관리 되는 도메인에서 개체 및 자격 증명을 동기화 하는 방법

Azure Active Directory Domain Services (AD DS) 관리 되는 도메인의 개체 및 자격 증명은 도메인 내에서 로컬로 만들거나 Azure Active Directory (Azure AD) 테 넌 트에서 동기화 할 수 있습니다. Azure AD DS를 처음 배포 하는 경우 Azure AD에서 개체를 복제 하기 위해 자동 단방향 동기화가 구성 되 고 시작 됩니다. 이 단방향 동기화는 azure AD에서 변경 내용을 적용 하 여 Azure AD DS 관리 되는 도메인을 최신 상태로 유지 하기 위해 백그라운드에서 계속 실행 됩니다. Azure AD DS에서 Azure AD로 다시 동기화가 수행 되지 않습니다.

하이브리드 환경에서는 Azure AD Connect를 사용 하 여 온-프레미스 AD DS 도메인의 개체 및 자격 증명을 Azure AD로 동기화 할 수 있습니다. 이러한 개체가 Azure AD에 성공적으로 동기화 되 면 자동 백그라운드 동기화가 Azure AD DS 관리 되는 도메인을 사용 하 여 응용 프로그램에서 해당 개체 및 자격 증명을 사용할 수 있도록 합니다.

다음 다이어그램에서는 Azure AD DS, Azure AD 및 선택적 온-프레미스 AD DS 환경 간에 동기화가 작동 하는 방법을 보여 줍니다.

![Azure AD Domain Services 관리 되는 도메인에 대 한 동기화 개요](./media/active-directory-domain-services-design-guide/sync-topology.png)

## <a name="synchronization-from-azure-ad-to-azure-ad-ds"></a>Azure AD에서 Azure AD DS로 동기화

사용자 계정, 그룹 멤버 자격 및 자격 증명 해시는 Azure AD에서 Azure AD DS로 한 방법으로 동기화 됩니다. 이 동기화 프로세스는 자동입니다. 이 동기화 프로세스를 구성, 모니터링 또는 관리할 필요가 없습니다. 초기 동기화는 Azure AD 디렉터리의 개체 수에 따라 며칠에서 몇 시간이 걸릴 수 있습니다. 초기 동기화가 완료 된 후에는 Azure AD에서 적용 된 변경 내용 (예: 암호 또는 특성 변경)이 Azure AD DS에 자동으로 동기화 됩니다.

동기화 프로세스는 설계에 따라 단방향/단방향입니다. Azure AD DS에서 Azure AD로의 변경 내용이 역방향으로 동기화 되지 않습니다. Azure AD DS 관리 되는 도메인은 사용자가 만들 수 있는 사용자 지정 Ou를 제외 하 고는 대부분 읽기 전용입니다. Azure AD DS 관리 되는 도메인 내에서 사용자 특성, 사용자 암호 또는 그룹 멤버 자격을 변경할 수 없습니다.

## <a name="attribute-synchronization-and-mapping-to-azure-ad-ds"></a>Azure AD DS에 대 한 특성 동기화 및 매핑

다음 표에서는 몇 가지 일반적인 특성 및 Azure AD DS에 동기화 되는 방법을 보여 줍니다.

| Azure AD DS의 특성 | 원본 | 참고 |
|:--- |:--- |:--- |
| UPN | Azure AD 테 넌 트의 사용자 *UPN* 특성 | Azure AD 테 넌 트의 UPN 특성은 Azure AD DS와 동일 하 게 동기화 됩니다. Azure AD DS 관리 되는 도메인에 로그인 하는 가장 신뢰할 수 있는 방법은 UPN을 사용 하는 것입니다. |
| SAMAccountName | Azure AD 테 넌 트의 사용자 *mailNickname* 특성 또는 자동 생성 | *SAMAccountName* 특성은 Azure AD 테 넌 트의 *mailNickname* 특성을 기반으로 합니다. 여러 사용자 계정이 동일한 *mailNickname* 특성을 사용 하는 경우 *SAMAccountName* 이 자동으로 생성 됩니다. 사용자의 *mailNickname* 또는 *UPN* 접두사가 20 자 보다 길면 *samaccountname 특성의* 20 자 제한을 충족 하도록 *samaccountname* 이 자동으로 생성 됩니다. |
| 암호 | Azure AD 테 넌 트에서 사용자 암호 | NTLM 또는 Kerberos 인증에 필요한 레거시 암호 해시는 Azure AD 테 넌 트에서 동기화 됩니다. Azure AD 테 넌 트가 Azure AD Connect를 사용 하는 하이브리드 동기화로 구성 된 경우 이러한 암호 해시는 온-프레미스 AD DS 환경에서 원본으로 사용 됩니다. |
| 기본 사용자/그룹 SID | 자동 생성 | 사용자/그룹 계정의 주 SID는 Azure AD DS에서 자동으로 생성 됩니다. 이 특성은 온-프레미스 AD DS 환경에서 개체의 기본 사용자/그룹 SID와 일치 하지 않습니다. 이러한 불일치는 Azure AD DS 관리 되는 도메인에 온-프레미스 AD DS 도메인과 다른 SID 네임 스페이스가 있기 때문입니다. |
| 사용자 및 그룹에 대한 SID 기록 | 온-프레미스 기본 사용자 및 그룹 SID | Azure AD DS의 사용자 및 그룹에 대 한 *SidHistory* 특성은 온-프레미스 AD DS 환경에서 해당 하는 기본 사용자 또는 그룹 SID와 일치 하도록 설정 됩니다. 이 기능을 통해 온-프레미스 응용 프로그램의 리프트 앤 시프트는 리소스를 다시 사용 하지 않아도 되기 때문에 Azure AD DS 더 쉽게 수행할 수 있습니다. |

> [!TIP]
> **UPN 형식을 사용 하 여 관리 되는 도메인에 로그인 합니다** . `CONTOSO\driley`와 같은 *SAMAccountName* 특성은 Azure AD DS 관리 되는 도메인의 일부 사용자 계정에 대해 자동으로 생성 될 수 있습니다. 사용자의 자동 생성 된 *SAMAccountName* 은 UPN 접두사와 다를 수 있으므로 항상 신뢰할 수 있는 방식으로 로그인 할 수 없습니다.
>
> 예를 들어 여러 사용자가 동일한 *mailNickname* 특성을가지고 있거나 사용자에 게 너무 긴 UPN 접두사가 있는 경우 해당 사용자에 대 한 *SAMAccountName* 이 자동으로 생성 될 수 있습니다. `driley@contoso.com`와 같은 UPN 형식을 사용 하 여 Azure AD DS 관리 되는 도메인에 안정적으로 로그인 합니다.

### <a name="attribute-mapping-for-user-accounts"></a>사용자 계정에 대한 특성 매핑

다음 표에서는 Azure AD의 사용자 개체에 대 한 특정 특성이 Azure AD DS의 해당 특성에 동기화 되는 방법을 보여 줍니다.

| Azure AD의 사용자 특성 | Azure AD DS의 사용자 특성 |
|:--- |:--- |
| accountEnabled |userAccountControl(ACCOUNT_DISABLED 비트를 설정하거나 지움) |
| city |l |
| country |co |
| department |department |
| displayName |displayName |
| facsimileTelephoneNumber |facsimileTelephoneNumber |
| givenName |givenName |
| jobTitle |title |
| 메일 |메일 |
| mailNickname |msDS-AzureADMailNickname |
| mailNickname |SAMAccountName (경우에 따라 자동으로 생성 될 수 있음) |
| mobile |mobile |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| passwordPolicies |userAccountControl(DONT_EXPIRE_PASSWORD 비트를 설정하거나 지움) |
| physicalDeliveryOfficeName |physicalDeliveryOfficeName |
| postalCode |postalCode |
| preferredLanguage |preferredLanguage |
| state |st |
| streetAddress |streetAddress |
| surname |sn |
| telephoneNumber |telephoneNumber |
| userPrincipalName |userPrincipalName |

### <a name="attribute-mapping-for-groups"></a>그룹에 대한 특성 매핑

다음 표에서는 Azure AD의 그룹 개체에 대 한 특정 특성이 Azure AD DS의 해당 특성에 동기화 되는 방법을 보여 줍니다.

| Azure AD의 그룹 특성 | Azure AD DS의 그룹 특성 |
|:--- |:--- |
| displayName |displayName |
| displayName |SAMAccountName (경우에 따라 자동으로 생성 될 수 있음) |
| 메일 |메일 |
| mailNickname |msDS-AzureADMailNickname |
| objectid |msDS-AzureADObjectId |
| onPremiseSecurityIdentifier |sidHistory |
| securityEnabled |groupType |

## <a name="synchronization-from-on-premises-ad-ds-to-azure-ad-and-azure-ad-ds"></a>온-프레미스 AD DS에서 Azure AD 및 Azure AD DS 동기화

Azure AD Connect은 온-프레미스 AD DS 환경에서 Azure AD로 사용자 계정, 그룹 멤버 자격 및 자격 증명 해시를 동기화 하는 데 사용 됩니다. UPN 및 온-프레미스 SID (보안 식별자)와 같은 사용자 계정의 특성이 동기화 됩니다. Azure AD DS을 사용 하 여 로그인 하려면 NTLM 및 Kerberos 인증에 필요한 레거시 암호 해시가 Azure AD에도 동기화 됩니다.

> [!IMPORTANT]
> Azure AD Connect는 온-프레미스 AD DS 환경과의 동기화를 위해서만 설치되고 구성되어야 합니다. 개체를 Azure AD로 개체를 다시 동기화하기 위해 Azure AD DS 관리형 도메인에 Azure AD Connect를 설치하는 것은 지원되지 않습니다.

쓰기 복구를 구성 하는 경우 Azure AD의 변경 내용이 온-프레미스 AD DS 환경에 다시 동기화 됩니다. 예를 들어 사용자가 Azure AD 셀프 서비스 암호 관리를 사용 하 여 암호를 변경 하면 암호는 온-프레미스 AD DS 환경에서 다시 업데이트 됩니다.

> [!NOTE]
> 모든 알려진 버그에 대한 수정 프로그램을 유지할 수 있도록 항상 최신 버전의 Azure AD Connect를 사용합니다.

### <a name="synchronization-from-a-multi-forest-on-premises-environment"></a>다중 포리스트 온-프레미스 환경에서의 동기화

많은 조직에는 여러 포리스트를 포함 하는 매우 복잡 한 온-프레미스 AD DS 환경이 있습니다. Azure AD Connect는 다중 포리스트 환경에서 Azure AD로의 사용자, 그룹 및 자격 증명 해시 동기화를 지원 합니다.

Azure AD는 훨씬 간단 하 고 플랫 네임 스페이스를 포함 합니다. 사용자가 Azure AD에서 보호되는 애플리케이션에 안정적으로 액세스할 수 있도록 하려면 서로 다른 포리스트의 사용자 계정 간에 발생하는 UPN 충돌을 해결해야 합니다. Azure AD DS 관리 되는 도메인은 Azure AD와 유사한 플랫 OU 구조를 사용 합니다. 온-프레미스 계층 구조 OU 구조를 구성한 경우에도 모든 사용자 계정 및 그룹은 다른 온-프레미스 도메인 또는 포리스트에서 동기화 되더라도 *Aaddc 사용자* 컨테이너에 저장 됩니다. Azure AD DS 관리 되는 도메인은 모든 계층 구조 OU 구조를 평면화 합니다.

앞서 설명한 것 처럼 Azure AD DS에서 Azure AD로 다시 동기화 하지 않습니다. Azure AD DS에서 사용자 [지정 ou (조직 구성 단위)를 만든](create-ou.md) 다음 해당 사용자 지정 ou 내에서 사용자, 그룹 또는 서비스 계정을 만들 수 있습니다. 사용자 지정 Ou에서 만든 개체는 Azure AD에 다시 동기화 되지 않습니다. 이러한 개체는 Azure AD DS 관리 되는 도메인 내 에서만 사용할 수 있으며 azure AD PowerShell cmdlet, Microsoft Graph API 또는 Azure AD 관리 UI를 사용 하 여 볼 수 없습니다.

## <a name="what-isnt-synchronized-to-azure-ad-ds"></a>Azure AD DS에 동기화 되지 않은 항목

다음 개체 또는 특성은 온-프레미스 AD DS 환경에서 Azure AD 또는 Azure AD DS로 동기화 되지 않습니다.

* **제외 된 특성:** Azure AD Connect를 사용 하 여 온-프레미스 AD DS 환경에서 Azure AD로의 동기화에서 특정 특성을 제외 하도록 선택할 수 있습니다. 이러한 제외 된 특성은 Azure AD DS에서 사용할 수 없습니다.
* **그룹 정책:** 온-프레미스 AD DS 환경에서 구성 된 그룹 정책은 Azure AD DS와 동기화 되지 않습니다.
* **Sysvol 폴더:** 온-프레미스 AD DS 환경의 *Sysvol* 폴더 내용은 Azure AD DS와 동기화 되지 않습니다.
* **컴퓨터 개체:** 온-프레미스 AD DS 환경에 가입 된 컴퓨터의 컴퓨터 개체는 Azure AD DS와 동기화 되지 않습니다. 이러한 컴퓨터는 Azure AD DS 관리 되는 도메인과 트러스트 관계가 없으며 온-프레미스 AD DS 환경에만 속합니다. Azure AD DS에서 관리 되는 도메인에 명시적으로 도메인에 가입 된 컴퓨터에 대 한 컴퓨터 개체만 표시 됩니다.
* **사용자 및 그룹에 대 한 SidHistory 특성:** 온-프레미스 AD DS 환경의 기본 사용자 및 주 그룹 Sid는 Azure AD DS와 동기화 됩니다. 그러나 사용자 및 그룹에 대 한 기존 *SidHistory* 특성은 온-프레미스 AD DS 환경에서 Azure AD DS로 동기화 되지 않습니다.
* **OU (조직 구성 단위) 구조:** 온-프레미스 AD DS 환경에서 정의 된 조직 구성 단위는 Azure AD DS와 동기화 되지 않습니다. Azure AD DS에는 두 개의 기본 제공 Ou가 있습니다. 하나는 사용자를 위한 것이 고 다른 하나는 컴퓨터용입니다. Azure AD DS 관리 되는 도메인에는 플랫 OU 구조가 있습니다. [관리 되는 도메인에서 사용자 지정 OU를 만들도록](create-ou.md)선택할 수 있습니다.

## <a name="password-hash-synchronization-and-security-considerations"></a>암호 해시 동기화 및 보안 고려 사항

Azure AD DS을 사용 하도록 설정 하는 경우 NTLM + Kerberos 인증에 대 한 레거시 암호 해시가 필요 합니다. Azure AD는 일반 텍스트 암호를 저장 하지 않으므로 기존 사용자 계정에 대해 이러한 해시를 자동으로 생성할 수 없습니다. 생성 되 고 저장 된 NTLM 및 Kerberos 호환 암호 해시는 항상 Azure AD에서 암호화 된 방식으로 저장 됩니다.

암호화 키는 각 Azure AD 테 넌 트에 대해 고유 합니다. 이러한 해시는 Azure AD DS 암호 해독 키에 대 한 액세스 권한이 있는 경우에만 암호화 됩니다. Azure AD의 다른 서비스나 구성 요소는 암호 해독 키에 액세스할 수 없습니다.

그러면 레거시 암호 해시가 azure AD에서 Azure AD DS 관리 되는 도메인에 대 한 도메인 컨트롤러로 동기화 됩니다. Azure AD DS의 이러한 관리 되는 도메인 컨트롤러에 대 한 디스크는 미사용 시 암호화 됩니다. 이러한 암호 해시는 온-프레미스 AD DS 환경에서 암호를 저장 하 고 보호 하는 방법과 유사 하 게 이러한 도메인 컨트롤러에 저장 되 고 보안이 유지 됩니다.

클라우드 전용 Azure AD 환경의 경우 필요한 암호 해시가 생성 되어 Azure AD에 저장 되려면 [사용자가 암호를 다시 설정/변경 해야](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds) 합니다. Azure AD Domain Services를 사용하도록 설정한 후에 Azure AD에서 만든 클라우드 사용자 계정의 경우 암호 해시는 NTLM 및 Kerberos 호환 형식으로 생성되고 저장됩니다. 이러한 새 계정은 암호를 다시 설정 하거나 변경할 필요가 없습니다. 기존 암호 해시가 생성 됩니다.

Azure AD Connect를 사용 하 여 온-프레미스 AD DS 환경에서 동기화 된 하이브리드 사용자 계정의 경우 [NTLM 및 Kerberos 호환 형식에서 암호 해시를 동기화 하도록 Azure AD Connect를 구성](tutorial-configure-password-hash-sync.md)해야 합니다.

## <a name="next-steps"></a>다음 단계

암호 동기화의 세부 사항에 대 한 자세한 내용은 [암호 해시 동기화가 Azure AD Connect와 작동 하는 방식](../active-directory/hybrid/how-to-connect-password-hash-synchronization.md?context=/azure/active-directory-domain-services/context/azure-ad-ds-context)을 참조 하세요.

Azure AD DS를 시작 하려면 [관리 되는 도메인을 만듭니다](tutorial-create-instance.md).
