---
title: 게시자 확인 개요 - Microsoft ID 플랫폼 | Azure
description: Microsoft ID 플랫폼의 게시자 확인 프로그램(미리 보기)에 대한 개요를 제공합니다. 이점, 프로그램 요구 사항 및 질문과 대답을 나열합니다. 애플리케이션이 게시자 확인으로 표시되어 있으면 게시자가 Microsoft 파트너 네트워크 계정을 사용하여 확인 프로세스를 완료했으며 애플리케이션 등록과 연결된 MPN 계정과 연결된 ID를 확인하고 있는 것입니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/19/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: 286f813c825bcc05ce8e9fa43df5dc0299625277
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89068476"
---
# <a name="publisher-verification-preview"></a>게시자 확인(미리 보기)

Microsoft ID 플랫폼과 통합하는 애플리케이션 개발자의 신뢰성을 관리자와 최종 사용자가 알 수 있도록 도와주는 것이 게시자 확인(미리 보기)입니다. 애플리케이션이 게시자 확인으로 표시되어 있으면 게시자가 [Microsoft 파트너 네트워크](https://partner.microsoft.com/membership) 계정을 사용하여 [확인](/partner-center/verification-responses) 프로세스를 완료했으며 애플리케이션 등록과 연결된 MPN 계정과 연결된 ID를 확인하고 있는 것입니다. 

다음과 같이 파란색 "확인" 배지가 Azure AD 동의 확인 프롬프트 및 다른 화면에 표시됩니다. ![동의 확인 프롬프트](./media/publisher-verification-overview/consent-prompt.png)

이 기능의 주요 대상은 [Microsoft ID 플랫폼](v2-overview.md)에서 [OAuth 2.0 및 OpenID Connect](active-directory-v2-protocols.md)를 활용하는 다중 테넌트 앱을 빌드하는 개발자입니다. 이러한 앱은 OpenID Connect를 사용하여 사용자를 로그인할 수도 있고, OAuth 2.0을 사용하여 [Microsoft Graph](https://developer.microsoft.com/graph/) 같은 API를 통해 데이터 액세스를 요청할 수도 있습니다.

## <a name="benefits"></a>이점
게시자 확인은 다음과 같은 이점을 제공합니다.
- **고객을 위한 투명성 향상 및 위험 감소** - 이 기능을 통해 고객은 조직에서 사용하는 앱을 게시한 사람이 믿을 수 있는 개발자라는 것을 알 수 있습니다. 

- **브랜드 이미지 향상** - Azure AD [동의 확인 프롬프트](application-consent-experience.md), 엔터프라이즈 앱 페이지, 최종 사용자 및 관리자가 사용하는 추가 UX 화면에 "확인" 배지가 표시됩니다. 

- **보다 원활한 엔터프라이즈 채택** - 관리자는 새로운 사용자 동의 정책을 구성할 수 있으며, 게시자 확인 상태는 기본 정책 조건 중 하나입니다. 

- **위험 평가 향상** - Microsoft의 "위험한" 동의 요청 감지에 게시자 확인이 신호로 포함됩니다. 

## <a name="requirements"></a>요구 사항
게시자 확인을 사용하려면 몇 가지 필수 구성 요소가 필요하며, 그 중 일부는 이미 여러 Microsoft 파트너가 완료했습니다. 다음은 필수 구성 요소입니다. 

-  [확인](/partner-center/verification-responses) 프로세스를 완료한 유효한 [Microsoft 파트너 네트워크 계정](https://partner.microsoft.com/membership)의 MPN ID. MPN 계정은 조직의 [PGA(파트너 글로벌 계정)](/partner-center/account-structure#the-top-level-is-the-partner-global-account-pga)여야 합니다. 

-  [게시자 도메인](howto-configure-publisher-domain.md) 을 구성 하 여 Azure AD 테 넌 트에 등록 된 앱입니다.

-  MPN 계정 확인 중에 사용 되는 전자 메일 주소의 도메인은 앱 또는 Azure AD 테 넌 트에 추가 된 DNS 확인 [사용자 지정 도메인](../fundamentals/add-custom-domain.md) 에 구성 된 게시자 도메인과 일치 해야 합니다. 

-  확인을 수행하는 사용자는 Azure AD의 앱 등록과 파트너 센터의 MPN 계정을 변경할 수 있는 권한이 있어야 합니다. 

    -  Azure AD에서이 사용자는 응용 프로그램 관리, 클라우드 응용 프로그램 관리자 또는 전역 관리자 [역할](../users-groups-roles/directory-assign-admin-roles.md)중 하나의 구성원 이어야 합니다. 

    -  파트너 센터에서 이 사용자는 [역할](/partner-center/permissions-overview)로 MPN 관리자, 계정 관리자 또는 글로벌 관리자(Azure AD의 공유 역할) 중 하나가 할당되어야 합니다.
    
-  게시자가 [개발자용 Microsoft ID 플랫폼 사용 약관](/legal/microsoft-identity-platform/terms-of-use)에 동의해야 합니다.

이러한 필수 구성 요소를 이미 충족하는 개발자는 몇 분 안에 확인을 받을 수 있습니다. 요구 사항이 충족되지 않을 경우 무료로 설정을 가져올 수 있습니다. 

## <a name="frequently-asked-questions"></a>질문과 대답 
아래는 게시자 확인 프로그램과 관련된 질문과 대답입니다. 요구 사항 및 프로세스와 관련된 FAQ는 [앱을 게시자 확인으로 표시](mark-app-as-publisher-verified.md)를 참조하세요.

- **게시자 확인에서 제공하지 __않는__ 정보는 무엇인가요?**  애플리케이션이 게시자 확인으로 표시되어도 애플리케이션 또는 애플리케이션의 게시자가 특정 인증을 획득했는지, 산업 표준을 준수하는지, 모범 사례를 따르는지 여부를 나타내지는 않습니다. [Microsoft 365 App Certification](/microsoft-365-app-certification/overview)을 비롯한 기타 Microsoft 프로그램은 이 정보를 제공합니다.

- **비용은 얼마나 드나요? 라이선스가 필요한가요?** Microsoft는 개발자에게 게시자 확인 요금을 부과하지 않으며 라이선스도 필요 없습니다. 

- **Microsoft 365 게시자 증명과 어떤 관계가 있나요? Microsoft 365 앱 인증은 어떤가요?** 두 프로그램은 고객이 안심하고 채택할 수 있는 믿을 만한 앱을 만드는 데 사용할 수 있는 보완 프로그램입니다. 게시자 확인은 이 프로세스의 첫 번째 단계이며, 위의 조건을 충족하는 앱을 만드는 모든 개발자가 게시자 확인을 완료해야 합니다. 

  또한 Microsoft 365와 통합하는 개발자는 이러한 프로그램의 추가 혜택을 얻을 수 있습니다. 자세한 내용은 [Microsoft 365 게시자 증명](/microsoft-365-app-certification/docs/attestation) 및 [Microsoft 365 앱 인증](/microsoft-365-app-certification/docs/certification)을 참조하세요. 

- **Azure AD 애플리케이션 갤러리와 똑같은 것인가요?** 아니요, 게시자 확인은 [Azure Active Directory 애플리케이션 갤러리](../azuread-dev/howto-app-gallery-listing.md)를 보완하기는 하지만 별개의 프로그램입니다. 위의 조건을 충족하는 개발자는 해당 프로그램 참여 여부에 관계없이 게시자 확인 프로세스를 완료해야 합니다. 

## <a name="next-steps"></a>다음 단계
* [앱을 게시자 확인으로 표시](mark-app-as-publisher-verified.md)하는 방법을 알아봅니다.
* 게시자 확인 [문제를 해결](troubleshoot-publisher-verification.md)합니다.
