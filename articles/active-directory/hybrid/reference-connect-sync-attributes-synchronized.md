---
title: Azure AD Connect에서 동기화된 특성 | Microsoft Docs
description: Azure Active Directory에 동기화된 특성을 나열합니다.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: c2bb36e0-5205-454c-b9b6-f4990bcedf51
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 01/24/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb7010ec16592fea2f530329916e00056ca03a70
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096129"
---
# <a name="azure-ad-connect-sync-attributes-synchronized-to-azure-active-directory"></a>Azure AD Connect 동기화: Azure Active Directory에 동기화된 특성
이 항목에서는 Azure AD Connect 동기화에 의해 동기화되는 특성을 보여 줍니다.  
특성은 관련된 Azure AD 앱으로 그룹화되었습니다.

## <a name="attributes-to-synchronize"></a>동기화할 특성
일반적으로 *동기화할 최소 특성 목록*에 대해 궁금해 합니다. 기본 및 권장되는 접근 방법은 클라우드에서 전체 GAL(전체 주소 목록)을 생성하고 Office 365 워크로드의 모든 기능을 사용할 수 있도록 기본 특성을 유지하는 것입니다. 일부 경우에 이 예제(  
![잘못된 특성](./media/reference-connect-sync-attributes-synchronized/badextensionattribute.png)

이 경우 이 항목에서 특성의 목록으로 시작하고 중요한 데이터 또는 PII 데이터를 포함하고 동기화할 수 없는 이러한 특성을 식별합니다. 그런 다음 [Azure AD 앱 및 특성 필터링](how-to-connect-install-custom.md#azure-ad-app-and-attribute-filtering)을 사용하여 설치 중에 이러한 특성을 선택 취소합니다.

> [!WARNING]
> 특성을 선택 취소하는 경우 주의해야 하고 동기화가 불가능한 특성만 선택 취소해야 합니다. 다른 특성을 선택 취소하면 기능에 부정적인 영향을 미칠 수도 있습니다.
>
>

## <a name="office-365-proplus"></a>Office 365 ProPlus
| 특성 이름 | 사용자 | 주석 |
| --- |:---:| --- |
| accountEnabled |X |활성화된 계정을 정의합니다. |
| cn |X | |
| displayName |X | |
| objectSID |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| pwdLastSet |X |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
|samAccountName|X| |
| sourceAnchor |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| usageLocation |X |기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |

## <a name="exchange-online"></a>Exchange Online
| 특성 이름 | 사용자 | 연락처 | 그룹 | 주석 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| 도우미 |X |X | | |
| altRecipient |X | | |Azure AD Connect 빌드 1.1.552.0 이상이 필요합니다. |
| authOrig |X |X |X | |
| C |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| homePhone |X |X | | |
| info |X |X |X |이 특성은 현재 그룹에 사용되지 않습니다. |
| Initials |X |X | | |
| l |X |X | | |
| legacyExchangeDN |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| maDS-HABSeniorityIndex |X |X |X | |
| msDS-PhoneticDisplayName |X |X |X | |
| msExchArchiveGUID |X | | | |
| msExchArchiveName |X | | | |
| msExchAssistantName |X |X | | |
| msExchAuditAdmin |X | | | |
| msExchAuditDelegate |X | | | |
| msExchAuditDelegateAdmin |X | | | |
| msExchAuditOwner |X | | | |
| msExchBlockedSendersHash |X |X | | |
| msExchBypassAudit |X | | | |
| msExchBypassModerationLink | | |X |Azure AD Connect 버전 1.1.524.0에서 사용 가능 |
| msExchCoManagedByLink | | |X | |
| msExchDelegateListLink |X | | | |
| msExchELCExpirySuspensionEnd |X | | | |
| msExchELCExpirySuspensionStart |X | | | |
| msExchELCMailboxFlags |X | | | |
| msExchEnableModeration |X | |X | |
| msExchExtensionCustomAttribute1 |X |X |X |이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute2 |X |X |X |이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute3 |X |X |X |이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute4 |X |X |X |이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchExtensionCustomAttribute5 |X |X |X |이 특성은 현재 Exchange Online에 사용되지 않습니다. |
| msExchHideFromAddressLists |X |X |X | |
| msExchImmutableID |X | | | |
| msExchLitigationHoldDate |X |X |X | |
| msExchLitigationHoldOwner |X |X |X | |
| msExchMailboxAuditEnable |X | | | |
| msExchMailboxAuditLogAgeLimit |X | | | |
| msExchMailboxGuid |X | | | |
| msExchModeratedByLink |X |X |X | |
| msExchModerationFlags |X |X |X | |
| msExchRecipientDisplayType |X |X |X | |
| msExchRecipientTypeDetails |X |X |X | |
| msExchRemoteRecipientType |X | | | |
| msExchRequireAuthToSendTo |X |X |X | |
| msExchResourceCapacity |X | | | |
| msExchResourceDisplay |X | | | |
| msExchResourceMetaData |X | | | |
| msExchResourceSearchProperties |X | | | |
| msExchRetentionComment |X |X |X | |
| msExchRetentionURL |X |X |X | |
| msExchSafeRecipientsHash |X |X | | |
| msExchSafeSendersHash |X |X | | |
| msExchSenderHintTranslations |X |X |X | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| msExchUserHoldPolicies |X | | | |
| msOrg IsOrganizational | | |X | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| proxyAddresses |X |X |X | |
| publicDelegates |X |X |X | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 동기화 및 페더레이션 모두 사용됩니다. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |groupType에서 파생됩니다 |
| sn |X |X | | |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| title |X |X | | |
| unauthOrig |X |X |X | |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다. |
| userCertificate |X |X | | |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |
| userSMIMECertificates |X |X | | |
| wWWHomePage |X |X | | |

## <a name="sharepoint-online"></a>SharePoint Online
| 특성 이름 | 사용자 | 연락처 | 그룹 | 주석 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| authOrig |X |X |X | |
| C |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| dLMemRejectPerms |X |X |X | |
| dLMemSubmitPerms |X |X |X | |
| extensionAttribute1 |X |X |X | |
| extensionAttribute10 |X |X |X | |
| extensionAttribute11 |X |X |X | |
| extensionAttribute12 |X |X |X | |
| extensionAttribute13 |X |X |X | |
| extensionAttribute14 |X |X |X | |
| extensionAttribute15 |X |X |X | |
| extensionAttribute2 |X |X |X | |
| extensionAttribute3 |X |X |X | |
| extensionAttribute4 |X |X |X | |
| extensionAttribute5 |X |X |X | |
| extensionAttribute6 |X |X |X | |
| extensionAttribute7 |X |X |X | |
| extensionAttribute8 |X |X |X | |
| extensionAttribute9 |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| hideDLMembership | | |X | |
| homePhone |X |X | | |
| info |X |X |X | |
| Initials |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| middleName |X |X | | |
| mobile |X |X | | |
| msExchTeamMailboxExpiration |X | | | |
| msExchTeamMailboxOwners |X | | | |
| msExchTeamMailboxSharePointLinkedBy |X | | | |
| msExchTeamMailboxSharePointUrl |X | | | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| oOFReplyToOriginator | | |X | |
| otherFacsimileTelephone |X |X | | |
| otherHomePhone |X |X | | |
| otherIpPhone |X |X | | |
| otherMobile |X |X | | |
| otherPager |X |X | | |
| otherTelephone |X |X | | |
| pager |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| postOfficeBox |X |X | |이 특성은 현재 SharePoint Online에 사용되지 않습니다. |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
| reportToOriginator | | |X | |
| reportToOwner | | |X | |
| securityEnabled | | |X |groupType에서 파생됩니다 |
| sn |X |X | | |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| st |X |X | | |
| streetAddress |X |X | | |
| targetAddress |X |X | | |
| telephoneAssistant |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| title |X |X | | |
| unauthOrig |X |X |X | |
| URL |X |X | | |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |
| wWWHomePage |X |X | | |

## <a name="lync-online-subsequently-known-as-skype-for-business"></a>Lync Online(이후에 비즈니스용 Skype라고 함)
| 특성 이름 | 사용자 | 연락처 | 그룹 | 주석 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| C |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| department |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X |X | |
| givenName |X |X | | |
| homePhone |X |X | | |
| ipPhone |X |X | | |
| l |X |X | | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| msExchHideFromAddressLists |X |X |X | |
| msRTCSIP-ApplicationOptions |X | | | |
| msRTCSIP-DeploymentLocator |X |X | | |
| msRTCSIP-Line |X |X | | |
| msRTCSIP-OptionFlags |X |X | | |
| msRTCSIP-OwnerUrn |X | | | |
| msRTCSIP-PrimaryUserAddress |X |X | | |
| msRTCSIP-UserEnabled |X |X | | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| otherTelephone |X |X | | |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
| securityEnabled | | |X |groupType에서 파생됩니다 |
| sn |X |X | | |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| thumbnailphoto |X |X | | |
| title |X |X | | |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |
| wWWHomePage |X |X | | |

## <a name="azure-rms"></a>Azure RMS
| 특성 이름 | 사용자 | 연락처 | 그룹 | 주석 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| cn |X | |X |일반 이름 또는 별칭입니다. 대부분 [mail] 값의 접두사입니다. |
| displayName |X |X |X |이름을 나타내는 문자열로 보통 친숙한 이름(성명)으로 표시됩니다. |
| mail |X |X |X |메일 주소 전체입니다. |
| member | | |X | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| proxyAddresses |X |X |X |기계적 속성입니다. Azure AD에서 사용됩니다. 사용자에 대한 모든 보조 메일 주소를 포함합니다. |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. |
| securityEnabled | | |X |groupType에서 파생됩니다. |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |이 UPN은 사용자용 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |

## <a name="intune"></a>Intune
| 특성 이름 | 사용자 | 연락처 | 그룹 | 주석 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| C |X |X | | |
| cn |X | |X | |
| description |X |X |X | |
| displayName |X |X |X | |
| mail |X |X |X | |
| mailNickname |X |X |X | |
| member | | |X | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
| securityEnabled | | |X |groupType에서 파생됩니다 |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |

## <a name="dynamics-crm"></a>Dynamics CRM
| 특성 이름 | 사용자 | 연락처 | 그룹 | 주석 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| C |X |X | | |
| cn |X | |X | |
| co |X |X | | |
| company |X |X | | |
| countryCode |X |X | | |
| description |X |X |X | |
| displayName |X |X |X | |
| facsimiletelephonenumber |X |X | | |
| givenName |X |X | | |
| l |X |X | | |
| managedBy | | |X | |
| manager |X |X | | |
| member | | |X | |
| mobile |X |X | | |
| objectSID |X | |X |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| physicalDeliveryOfficeName |X |X | | |
| postalCode |X |X | | |
| preferredLanguage |X | | | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
| securityEnabled | | |X |groupType에서 파생됩니다 |
| sn |X |X | | |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| st |X |X | | |
| streetAddress |X |X | | |
| telephoneNumber |X |X | | |
| title |X |X | | |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |

## <a name="3rd-party-applications"></a>타사 애플리케이션
이 그룹은 일반 워크로드 또는 애플리케이션에 필요한 최소한의 특성으로 사용되는 특성 모음입니다. 다른 섹션에 나열되지 않은 워크로드 또는 타사 앱에 사용할 수 있습니다. 다음에 명시적으로 사용됩니다.

* Yammer(User만 사용됨)
* [SharePoint와 같은 리소스에서 제공하는 하이브리드 B2B 조직 간 공동 작업 시나리오](https://go.microsoft.com/fwlink/?LinkId=747036)

이 그룹은 Azure AD Directory가 Office 365, Dynamics, 또는 Intune을 지원하지 않을 때 사용되는 특성의 집합입니다. 코어 특성의 작은 집합이 있습니다.

| 특성 이름 | 사용자 | 연락처 | 그룹 | 주석 |
| --- |:---:|:---:|:---:| --- |
| accountEnabled |X | | |활성화된 계정을 정의합니다. |
| cn |X | |X | |
| displayName |X |X |X | |
| givenName |X |X | | |
| mail |X | |X | |
| managedBy | | |X | |
| mailNickname |X |X |X | |
| member | | |X | |
| objectSID |X | | |기계적 속성입니다. Azure AD와 AD 사이의 동기화를 유지하기 위해 사용되는 AD 사용자 식별자입니다. |
| proxyAddresses |X |X |X | |
| pwdLastSet |X | | |기계적 속성입니다. 이미 발급된 토큰을 무효화하는 경우를 파악하는 데 사용합니다. 암호 해시 동기화, 통과 인증 및 페더레이션에서 사용됩니다. |
| sn |X |X | | |
| sourceAnchor |X |X |X |기계적 속성입니다. ADDS와 Azure AD 간의 관계를 유지하기 위한 변경할 수 없는 식별자입니다. |
| usageLocation |X | | |기계적 속성입니다. 사용자의 국가입니다. 라이선스 할당에 사용됩니다. |
| userPrincipalName |X | | |UPN은 사용자의 로그인 ID입니다. 대부분 [mail] 값과 같습니다. |

## <a name="windows-10"></a>윈도우 10
Windows 10 도메인에 가입된 컴퓨터(장치)는 일부 특성을 Azure AD에 동기화합니다. 이 시나리오에 대한 자세한 내용은 [Windows 10 환경용 Azure AD에 도메인 조인된 디바이스 연결](../active-directory-azureadjoin-devices-group-policy.md)을 참조하세요. 이 특성은 항상 동기화되며 Windows 10은 선택 취소할 수 있는 앱으로 표시되지 않습니다. Windows 10 도메인에 가입된 컴퓨터는 특성 userCertificate를 채우는 것으로 식별됩니다.

| 특성 이름 | 디바이스 | 주석 |
| --- |:---:| --- |
| accountEnabled |X | |
| deviceTrustType |X |도메인에 가입된 컴퓨터에 대해 하드 코딩된 값입니다. |
| displayName |X | |
| ms-DS-CreatorSID |X |registeredOwnerReference라고도 합니다. |
| objectGUID |X |deviceID라고도 합니다. |
| objectSID |X |onPremisesSecurityIdentifier라고도 합니다. |
| operatingSystem |X |deviceOSType이라고도 합니다. |
| operatingSystemVersion |X |deviceOSVersion이라고도 합니다. |
| userCertificate |X | |

이 특성은 선택한 다른 앱 외에 **사용자** 에 대해서도 있습니다.  

| 특성 이름 | 사용자 | 주석 |
| --- |:---:| --- |
| domainFQDN |X |dnsDomainName이라고도 합니다. 예를 들어 contoso.com입니다. |
| domainNetBios |X |netBiosName이라고도 합니다. 예를 들어 CONTOSO입니다. |
| msDS-KeyCredentialLink |X |사용자가 비즈니스용 Windows Hello에 등록됩니다. | 

## <a name="exchange-hybrid-writeback"></a>Exchange 하이브리드 쓰기 저장
이 특성들은 **Exchange 하이브리드**를 사용하도록 설정할 때 Azure AD에서 온-프레미스 Active Directory로 다시 기록됩니다. Exchange 버전에 따라 더 적은 특성을 동기화 할 수 있습니다.

| 특성 이름(Connect UI) |특성 이름(온-프레미스 AD) | 사용자 | 연락처 | 그룹 | 주석 |
| --- |:---:|:---:|:---:| --- |---|
| msDS-ExternalDirectoryObjectID| ms-DS-External-Directory-Object-Id |X | | |Azure AD의 cloudAnchor에서 파생됩니다. 이 특성은 Exchange 2016 및 Windows Server 2016 AD의 새로운 기능입니다. |
| msExchArchiveStatus| ms-Exch-ArchiveStatus |X | | |온라인 보관: 고객이 메일을 보관할 수 있도록 설정합니다. |
| msExchBlockedSendersHash| ms-Exch-BlockedSendersHash |X | | |필터링: 온-프레미스 필터링 및 클라이어트에서 안전하게 차단된 보낸 사람의 온라인 데이터를 다시 씁니다. |
| msExchSafeRecipientsHash| ms-Exch-SafeRecipientsHash  |X | | |필터링: 온-프레미스 필터링 및 클라이어트에서 안전하게 차단된 보낸 사람의 온라인 데이터를 다시 씁니다. |
| msExchSafeSendersHash| ms-Exch-SafeSendersHash  |X | | |필터링: 온-프레미스 필터링 및 클라이어트에서 안전하게 차단된 보낸 사람의 온라인 데이터를 다시 씁니다. |
| msExchUCVoiceMailSettings| ms-Exch-UCVoiceMailSettings |X | | |UM(통합 메시징) - 온라인 음성 메일 사용: Microsoft Lync Server 통합에서 사용자의 온라인 서비스에 음성 메일이 있음을 Lync Server 온-프레미스에 알리는 데 사용합니다. |
| msExchUserHoldPolicies| ms-Exc-hUserHoldPolicies |X | | |소송 보존: 클라우드 서비스에서 소송 보존 상태에 있는 사용자를 확인하도록 설정합니다. |
| proxyAddresses| proxyAddresses |X |X |X |Exchange Online의 x500 주소만 삽입됩니다. |
| publicDelegates| ms-Exch-Public-Delegates  |X | | |Exchange Online 사서함에 온-프레미스 Exchange 사서함이 있는 사용자에게 SendOnBehalfTo 권한을 부여할 수 있습니다. Azure AD Connect 빌드 1.1.552.0 이상이 필요합니다. |

## <a name="exchange-mail-public-folder"></a>Exchange 메일 공용 폴더
이러한 특성은 **Exchange 메일 공용 폴더**를 사용하도록 설정할 때 온-프레미스 Active Directory에서 Azure AD에서 동기화됩니다.

| 특성 이름 | PublicFolder | 주석 |
| --- | :---:| --- |
| displayName | X |  |
| mail | X |  |
| msExchRecipientTypeDetails | X |  |
| objectGUID | X |  |
| proxyAddresses | X |  |
| targetAddress | X |  |

## <a name="device-writeback"></a>디바이스 쓰기 저장
Active Directory에 디바이스 개체를 만듭니다. 이러한 개체는 Azure AD에 조인된 디바이스이거나 도메인에 조인된 Windows 10 컴퓨터일 수 있습니다.

| 특성 이름 | 디바이스 | 주석 |
| --- |:---:| --- |
| altSecurityIdentities |X | |
| displayName |X | |
| dn |X | |
| msDS-CloudAnchor |X | |
| msDS-DeviceID |X | |
| msDS-DeviceObjectVersion |X | |
| msDS-DeviceOSType |X | |
| msDS-DeviceOSVersion |X | |
| msDS-DevicePhysicalIDs |X | |
| msDS-KeyCredentialLink |X |Windows Server 2016 AD 스키마에서만 |
| msDS-IsCompliant |X | |
| msDS-IsEnabled |X | |
| msDS-IsManaged |X | |
| msDS-RegisteredOwner |X | |

## <a name="notes"></a>메모
* 대체 ID를 사용하면, 온-프레미스 특성 userPrincipalName은 Azure AD 특성 onPremisesUserPrinciparlName과 동기화됩니다. 대체 ID 특성(예: 메일)은 Azure AD 특성 userPrincipalName과 동기화됩니다.
* 위의 목록에서 개체 형식 **User**는 개체 형식 **iNetOrgPerson**에도 적용됩니다.

## <a name="next-steps"></a>다음 단계
[Azure AD Connect 동기화](how-to-connect-sync-whatis.md) 구성에 대해 자세히 알아봅니다.

[Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)에 대해 자세히 알아봅니다.
