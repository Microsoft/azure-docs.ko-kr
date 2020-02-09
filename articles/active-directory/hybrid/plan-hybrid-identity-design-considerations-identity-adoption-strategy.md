---
title: Azure의 하이브리드 ID 디자인 - 채택 전략 | Microsoft Docs
description: 조건부 액세스 제어를 사용 하 여 Azure Active Directory 사용자를 인증할 때 및 응용 프로그램에 대 한 액세스를 허용 하기 전에 선택한 특정 조건을 확인 합니다. 이러한 조건이 충족되면 사용자가 인증되고 애플리케이션에 대한 액세스가 허용됩니다.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: b92fa5a9-c04c-4692-b495-ff64d023792c
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: e662d2c6d7939756dee6eb25ca62fef171b7d6d0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "67109327"
---
# <a name="define-a-hybrid-identity-adoption-strategy"></a>하이브리드 ID 채택 전략 정의
이 작업에서 하이브리드 ID 솔루션에 대한 하이브리드 ID 채택 전략을 정의하여 다음에서 설명한 비즈니스 요구 사항을 충족합니다.

* [비즈니스 요구 사항 결정](plan-hybrid-identity-design-considerations-business-needs.md)
* [디렉터리 동기화 요구 사항 결정](plan-hybrid-identity-design-considerations-directory-sync-requirements.md)
* [Multi-Factor Authentication 요구 사항 결정](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>비즈니스 요구 사항 전략 정의
첫 번째 작업은 조직 비즈니스 필요를 다룹니다.  신중하지 않으면 매우 광범위한 범위 변형이 발생할 수 있습니다.  처음에 단순하게 유지하지만 나중에 변화를 수용하고 촉진하는 설계에 대한 계획을 항상 기억합니다.  간단한 설계 또는 매우 복잡한 설계에 관계 없이 Azure Active Directory는 Office 365, Microsoft 온라인 서비스 및 클라우드 인식 애플리케이션을 지원하는 Microsoft ID 플랫폼입니다.

## <a name="define-an-integration-strategy"></a>통합 전략 정의
Microsoft에는 클라우드 ID, 동기화된 ID 및 페더레이션된 ID는 3개의 주요 통합 시나리오가 있습니다.  이러한 통합 전략 중 하나를 채택하도록 계획해야 합니다.  어떤 사용자 환경을 제공할 것인지, 기존 인프라가 있는지, 가장 비용 효과적인 방법은 무엇인지에 따라 선택하는 전략이 달라질 수 있습니다.  

![통합 시나리오](./media/plan-hybrid-identity-design-considerations/integration-scenarios.png)

위의 그림에 정의된 시나리오는 다음과 같습니다.

* **클라우드 ID**: 클라우드에만 존재하는 ID입니다.  Azure AD의 경우 특히 Azure AD 디렉터리에 위치합니다.
* **동기화됨**: 온-프레미스 및 클라우드에 존재하는 ID입니다.  Azure AD Connect를 사용하여 기존 Azure AD 계정으로 이러한 사용자를 만들거나 조인합니다.  사용자의 암호 해시는 온-프레미스 환경에서 암호 해시라고 하는 클라우드로 동기화됩니다.  동기화를 사용할 때 중요한 점은 사용자가 온-프레미스 환경에서 사용 불가능해지면 해당 계정 상태를 Azure AD에 표시하는 데 최대 3시간이 걸릴 수 있다는 것입니다.  동기화 시간 간격 때문입니다.
* **페더레이션됨**: 이러한 ID는 온-프레미스 및 클라우드에 위치합니다.  Azure AD Connect를 사용하여 기존 Azure AD 계정으로 이러한 사용자를 만들거나 조인합니다.  

> [!NOTE]
> 동기화 옵션에 대한 자세한 내용은 [Azure Active Directory와 온-프레미스 ID 통합](whatis-hybrid-identity.md)을 참조하세요.
> 
> 

다음 테이블은 다음 각각의 전략이 가진 장단점을 결정하는 데 도움이 됩니다.

| 전략 | 장점 | 단점 |
| --- | --- | --- |
| **클라우드 ID** |소규모 조직을 관리하기가 쉬워집니다. <br> 온-프레미스에 아무 것도 설치하지 않습니다. 추가 하드웨어가 필요 없습니다.<br>사용자가 퇴사하는 경우 쉽게 사용 불가능해 집니다. |사용자는 클라우드의 워크로드에 액세스할 때 로그인해야 합니다 <br> 암호는 클라우드 및 온-프레미스 ID에 대해 동일할 수도 있고 않을 수도 있습니다 |
| **동기화됨** |온-프레미스 암호는 온-프레미스 및 클라우드 디렉터리를 인증합니다 <br>소규모, 중규모 또는 대규모 조직을 관리하기가 쉬워집니다. <br>사용자는 일부 리소스에 대한 SSO(Single Sign-On)를 가질 수 있습니다 <br> 동기화에 대한 Microsoft의 기본 메서드 <br> 관리가 쉬워집니다 |일부 고객은 특정 회사의 경찰 때문에 클라우드를 사용하여 해당 디렉터리를 동기화하는 것을 꺼릴 수 있습니다. |
| **페더레이션** |사용자는 Single Sign-On(SSO)을 가질 수 있습니다 <br>사용자를 종료하거나 사용자가 나가는 경우, 계정을 즉시 사용할 수 없게 하고 액세스를 해지할 수 있습니다.<br> 동기화되어 수행할 수 없는 고급 시나리오를 지원합니다 |설정 및 구성의 추가 단계 <br> 더 높은 유지 관리 <br> STS 인프라에 대한 추가 하드웨어가 필요할 수 있습니다. <br> 페더레이션 서버를 설치하는 데 추가적인 하드웨어가 필요할 수 있습니다. AD FS를 사용할 경우 추가적인 소프트웨어가 필요합니다. <br> SSO에 대한 광범위한 설정이 필요 <br> 중요 장애점, 페더레이션 서버가 다운된 경우 사용자는 인증할 수 없습니다. |

### <a name="client-experience"></a>클라이언트 환경
사용자 로그인 환경을 결정하는 데 사용하는 전략입니다.  다음 테이블에서는 어떤 사용자가 해당 로그인 환경을 예상해야 하는지에 대한 정보를 제공합니다.  페더레이션된 모든 ID 공급자가 모든 시나리오에서 SSO를 지원하는 것은 아닙니다.

**도메인 가입된 프라이빗 네트워크 애플리케이션**:

|  | 동기화된 ID | 페더레이션된 ID |
| --- | --- | --- |
| 웹 브라우저 |양식 기반 인증 |때때로 조직 ID를 제공하는 데 필요한 Single Sign On |
| Outlook |자격 증명 확인 |자격 증명 확인 |
| 비즈니스용 Skype(Lync) |자격 증명 확인 |Exchange에 대한 자격 증명을 확인하는 Lync용 Single Sign-On |
| OneDrive for Business |자격 증명 확인 |Single Sign-On |
| Office Pro Plus 구독 |자격 증명 확인 |Single Sign-On |

**외부 또는 신뢰할 수 없는 원본**:

|  | 동기화된 ID | 페더레이션된 ID |
| --- | --- | --- |
| 웹 브라우저 |양식 기반 인증 |양식 기반 인증 |
| Outlook, 비즈니스용 Skype(Lync), 비즈니스용 OneDrive, Office 구독 |자격 증명 확인 |자격 증명 확인 |
| Exchange ActiveSync |자격 증명 확인 |Exchange에 대한 자격 증명을 확인하는 Lync용 Single Sign-On |
| 모바일 앱 |자격 증명 확인 |자격 증명 확인 |

타사 IdP가 있거나 Azure AD의 페더레이션을 제공하는 IdP를 사용하도록 작업1에서 결정한 경우 지원되는 다음 기능에 주의해야 합니다.

* SP-Lite 프로필 규격인 SAML 2.0 공급자는 Azure AD 및 관련 애플리케이션에 인증을 지원할 수 있습니다.
* OWA, SPO 등에 대한 인증을 용이하게 하는 수동 인증을 지원합니다.
* Exchange Online 클라이언트는 SAML 2.0 ECP(향상된 클라이언트 프로필)를 통해 지원될 수 있습니다.

또한 어떤 기능을 사용할 수 없는지 알고 있어야 합니다.

* WS-트러스트/페더레이션이 지원되지 않으면 다른 모든 활성 클라이언트는 연결이 끊어집니다.
  * 즉, Lync 클라이언트, OneDrive 클라이언트, Office 구독, Office 2016 이전 Office 모바일은 아닙니다.
* 수동 인증에 Office를 전환하면 순수하게 SAML 2.0 IdP를 지원할 수 있지만 지원은 클라이언트 별 기준입니다.

> [!NOTE]
> 최근 업데이트된 목록은 [Azure AD 페더레이션 호환성 목록](how-to-connect-fed-compatibility.md)을 참조하세요.
> 
> 

## <a name="define-synchronization-strategy"></a>동기화 전략 정의
이 작업에서는 조직의 온-프레미스 데이터를 클라우드에 동기화하는 데 사용할 도구 및 어떤 토폴로지를 사용해야 할지 정의합니다.  대부분의 조직에서는 Active Directory를 사용하기 때문에 위의 질문을 다루기 위해 Azure AD Connect의 사용에 대한 정보는 세부 정보에서 제공됩니다.  Active Directory가 없는 환경에서 이 전략을 계획하는 데 도움이 되는 FIM 2010 R2 또는 MIM 2016의 사용에 대한 정보가 있습니다.  그러나 Azure AD Connect의 향후 릴리스는 LDAP 디렉터리를 지원하므로 타임라인에 따라 이 정보는 지원될 수 있습니다.

### <a name="synchronization-tools"></a>동기화 도구
몇년에 걸쳐 여러 동기화 도구가 존재했고 다양한 시나리오에 사용되었습니다.  현재 Azure AD Connect는 모든 시나리오에 가장 널리 쓰이는 도구입니다.  또한 AAD 동기화 및 DirSync은 여전히 있으며 사용자 환경에서 표시되기도 합니다. 

> [!NOTE]
> 각 도구의 지원되는 기능에 관한 최신 정보는 [디렉터리 통합 도구 비교](plan-hybrid-identity-design-considerations-tools-comparison.md) 문서를 읽습니다.  
> 
> 

### <a name="supported-topologies"></a>지원되는 토폴로지
동기화 전략을 정의할 때 사용되는 토폴로지를 결정해야 합니다. 2단계에서 확인된 정보에 따라 어떤 토폴로지를 사용하는 것이 적절한지를 결정할 수 있습니다. 단일 포리스트, 단일 Azure AD 토폴로지는 가장 일반적이고 단일 Active Directory 포리스트 및 Azure AD의 단일 인스턴스를 구성합니다.  이 시나리오 대부분에서 사용되며 아래 그림에 표시된 대로 Azure AD Connect Express 설치를 사용하는 경우 예상되는 토폴로지입니다.

![지원 되는 토폴로지](./media/plan-hybrid-identity-design-considerations/single-forest.png) 단일 포리스트 시나리오는 것이 일반적 다중 포리스트가 있는 크고 작은 조직에 대 한 그림 5에 표시 된 대로 합니다.

> [!NOTE]
> Azure AD connect 동기화를 사용하여 다른 온-프레미스 및 Azure AD 토폴로지에 대한 자세한 정보는 [Azure AD Connect에 대한 토폴로지](plan-connect-topologies.md)문서를 읽습니다.
> 
> 

![다중 포리스트 토폴로지](./media/plan-hybrid-identity-design-considerations/multi-forest.png) 

다중 포리스트 시나리오

이 경우에 다음 항목에 해당하면 다중 포리스트-단일 Azure AD 토폴로지를 고려해야 합니다.

* 사용자는 포리스트 전반에 1개의 ID가 있습니다 – 아래의 사용자 섹션을 고유하게 식별하는 작업을 자세히 설명합니다.
* 사용자는 해당 ID가 위치한 포리스트에 인증합니다.
* UPN 및 소스 앵커(변경할 수 없는 ID)는 이 포리스트에서 들어옵니다.
* 모든 포리스트는 Azure AD Connect에서 액세스할 수 있습니다 – 즉, 도메인에 가입할 필요가 없고 용이하게 하는 경우 DMZ에 배치할 수 있습니다.
* 사용자에게는 하나의 사서함만 있습니다.
* 사용자의 사서함을 호스트하는 포리스트는 Exchange GAL(전체 주소 목록)에 표시되는 특성에 대해 최상의 데이터 품질을 가집니다.
* 사용자에 대해 사서함이 없는 경우 아무 포리스트나 사용하여 이러한 값에 기여할 수 있습니다.
* 연결된 사서함이 있다면 로그인에 사용되는 다른 포리스트에도 다른 계정이 있습니다.

> [!NOTE]
> 온-프레미스 및 클라우드에 존재하는 개체는 고유 식별자를 통해 "연결"됩니다. 디렉터리 동기화의 컨텍스트에서 이 고유한 식별자는 SourceAnchor라고 합니다. Single Sign-On의 컨텍스트에서 ImmutableId라고 합니다. [Azure AD Connect에 대한 설계 개념](plan-connect-design-concepts.md#sourceanchor)
> 
> 

위의 조건이 충족되지 않고 둘 이상의 활성 계정 또는 둘 이상의 사서함이 있으면 Azure AD Connect는 하나를 선택하고 다른 하나는 무시합니다.  다른 계정이 없고 연결된 사서함만 있는 경우 이러한 계정은 Azure AD로 내보내지 않고 해당 사용자는 그룹의 멤버가 되지 않습니다.  DirSync를 사용과 관련하여 과거와 이러한 점이 다르며 이러한 다중 포리스트 시나리오를 더 지원하려고 합니다. 다중 포리스트 시나리오는 아래 그림에 표시됩니다.

![여러 Azure AD 테넌트](./media/plan-hybrid-identity-design-considerations/multiforest-multipleAzureAD.png) 

**다중 포리스트 여러 Azure AD 시나리오**

조직에 대한 Azure AD에서 단일 디렉터리를 보유하는 것이 좋지만 1:1 관계는 Azure AD Connect 동기화 서버와 Azure AD 디렉터리 사이에서 유지되도록 지원합니다.  Azure AD의 각 인스턴스의 경우 Azure AD Connect의 설치가 필요합니다.  또한 Azure AD는 설계로 격리되고 Azure AD의 한 인스턴스에서 사용자가 다른 인스턴스의 사용자를 확인할 수 없습니다.

아래 그림에 표시된 대로 여러 Azure AD 디렉터리에 하나의 Active Directory의 온-프레미스 인스턴스를 연결하는 것이 가능하며 지원됩니다.

![단일 포리스트 필터링](./media/plan-hybrid-identity-design-considerations/single-forest-flitering.png) 

**단일 포리스트 필터링 시나리오**

이렇게 하려면 다음 조건을 충족해야 합니다.

* Azure AD Connect Sync 서버가 필터링에 대해 구성되므로 각각 상호 배타적인 개체 집합이 있습니다.  예를 들어 특정 도메인 또는 OU에 서버의 범위를 지정하여 이를 수행합니다.
* DNS 도메인은 단일 Azure AD Directory에만 등록될 수 있으므로, 온-프레미스 AD 내의 사용자 UPN도 별도의 네임스페이스를 사용해야 합니다.
* Azure AD의 한 인스턴스에서 사용자가 해당 인스턴스에서 사용자를 확인할 수 있습니다.  다른 인스턴스에 있는 사용자를 확인할 수 없습니다.
* 하나의 Azure AD Directory만 온-프레미스 AD를 사용하여 Exchange 하이브리드를 활성화할 수 있습니다.
* 상호 배타성은 쓰기 저장에도 적용됩니다.  이렇게 하면 단일 온-프레미스 구성으로 가정되므로 일부 쓰기 저장 기능이 이 토폴로지로 지원되지 않습니다.  다음 내용이 포함됩니다.
  * 기본 구성으로 쓰기 저장 그룹화
  * 디바이스 쓰기 저장

다음은 지원되지 않으며, 구현으로 선택하지 말아야 합니다.

* 여러 Azure AD Connect Sync 서버가 상호 배타적인 개체 집합을 동기화하도록 구성되었다고 해도 동일한 Azure AD 디렉터리에 대한 연결은 지원되지 않습니다
* 동일한 사용자를 여러 Azure AD 디렉터리에 동기화하는 것은 지원되지 않습니다. 
* 또한 하나의 Azure AD에 있는 사용자가 다른 Azure AD Directory 내의 연락처로 표시되도록 구성하는 것도 지원되지 않습니다. 
* 또한 Azure AD Connect Sync를 여러 Azure AD 디렉터리에 연결되도록 수정하는 것도 지원되지 않습니다.
* Azure AD 디렉터리는 설계상 격리되어 있습니다. 디렉터리 간에 일반적이고 통합된 GAL을 구축하기 위한 시도로 다른 Azure AD Directory에서 데이터를 읽도록 Azure AD Connect Sync의 구성을 변경하는 것은 지원되지 않습니다. 또한 Azure AD Connect Sync를 사용하여 사용자를 온-프레미스 AD에 연락처로 내보내는 것도 지원되지 않습니다.

> [!NOTE]
> 조직이 네트워크의 컴퓨터를 인터넷에 연결할 수 없도록 제한하는 경우 이 문서는 클라이언트 컴퓨터의 아웃바운드 허용 목록 및 Internet Explorer 신뢰할 수 있는 사이트 영역에서 포함해야 하는 엔드포인트 (FQDN, IPv4 및 IPv6 주소 범위)을 나열하여 컴퓨터가 Office 365를 성공적으로 사용할 수 있도록 합니다. 자세한 내용은 [Office 365 URL 및 IP 주소 범위](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US)를 읽습니다.
> 
> 

## <a name="define-multi-factor-authentication-strategy"></a>Multi-Factor Authentication 전략 정의
이 작업에서 사용할 Multi-Factor Authentication 전략을 정의합니다.  두 가지 다른 버전의 Azure Multi-Factor Authentication이 있습니다.  하나는 클라우드 기반이고 다른 하나는  Azure MFA 서버를 사용하는 온-프레미스 기반입니다.  위에서 수행한 평가 결과에 따라 어떤 솔루션이 전략에 가장 적합한지 결정할 수 있습니다.  아래 테이블을 사용하여 어떤 설계 옵션이 회사의 보안 요구 사항을 가장 충족하는지 결정합니다.

Multi-Factor 설계 옵션:

| 보호할 자산 | 클라우드의 MFA | 온-프레미스에서 MFA |
| --- | --- | --- |
| Microsoft 앱 |예 |예 |
| 앱 갤러리의 SaaS 앱 |예 |예 |
| Azure AD 앱 프록시를 통해 IIS 애플리케이션 게시됨 |예 |예 |
| Azure AD 애플리케이션 프록시를 통해 IIS 애플리케이션이 게시되지 않음 |no |예 |
| VPN 및 RDG와 같은 원격 액세스 |no |예 |

전략에 대한 솔루션에 적응했더라도 여전히 사용자가 있는 위치의 위쪽에서 평가를 사용해야 합니다.  솔루션을 변경할 수 있습니다.  아래 테이블을 사용하여 다음을 결정하는 데 도움이 됩니다.

| 사용자 위치 | 선호하는 설계 옵션 |
| --- | --- |
| Azure Active Directory |클라우드에서의 Multi-Factor Authentication |
| Azure AD 및 AD FS로 페더레이션을 사용한 온-프레미스 AD |둘 다 |
| 암호 동기화 없이 Azure AD Connect를 사용하는 Azure AD 및 온-프레미스 AD |둘 다 |
| 암호 동기화와 함께 Azure AD Connect를 사용하는 Azure AD 및 온-프레미스 AD |둘 다 |
| 온-프레미스 AD |Multi-Factor Authentication 서버 |

> [!NOTE]
> 또한 선택한 Multi-Factor Authentication 설계 옵션이 설계에 필요한 기능을 지원하도록 해야 합니다.  자세한 내용은 [다단계 보안 솔루션 선택](../authentication/concept-mfa-howitworks.md)을 참조하세요.
> 

## <a name="multi-factor-auth-provider"></a>Multi-Factor Auth 공급자
다단계 인증은 기본적으로 Azure Active Directory 테넌트가 있는 전역 관리자를 위해 사용할 수 있습니다. 그러나 Multi-Factor Authentication을 모든 사용자로 확장하고 전역 관리자가 관리 포털, 사용자 지정 인사말, 보고서 등의 기능을 활용할 수 있게 하려면 Multi-Factor Authentication 공급자를 구입하고 구성해야 합니다.

> [!NOTE]
> 또한 선택한 Multi-Factor Authentication 설계 옵션이 설계에 필요한 기능을 지원하도록 해야 합니다. 
> 
> 

## <a name="next-steps"></a>다음 단계
[데이터 보호 요구 사항 결정](plan-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>참고 항목
[디자인 고려 사항 개요](plan-hybrid-identity-design-considerations-overview.md)

