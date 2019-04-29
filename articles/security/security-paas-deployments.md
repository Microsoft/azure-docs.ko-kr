---
title: PaaS 배포 보안 | Microsoft Docs
description: " PaaS와 다른 클라우드 서비스 모델의 보안 이점을 이해하고 Azure PaaS 배포를 보호하기 위한 권장 사례에 대해 알아봅니다. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: techlake
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2019
ms.author: terrylan
ms.openlocfilehash: e833317fa16576fa0006a774226d12974fd93ed8
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62107449"
---
# <a name="securing-paas-deployments"></a>PaaS 배포 보안

이 문서에서 제공하는 유용한 정보는 다음과 같습니다.

- 클라우드에서의 애플리케이션 호스팅에 대한 보안 이점 이해
- 다른 클라우드 서비스 모델과 비교하여 서비스로서의 플랫폼 (PaaS)에 대한 보안 이점 평가
- 네트워크 중심에서 ID 중심 경계 보안 방식으로 보안 목표 변경
- 일반적인 PaaS 보안 모범 사례 권장 구현

## <a name="cloud-security-advantages"></a>클라우드 보안 이점
클라우드에 해당하는 보안 이점이 있습니다. 온-프레미스 환경에서 조직은 충족되지 않은 책임과 제한된 리소스를 보안에 투자할 가능성이 높으며, 이로 인해 공격자가 모든 계층의 취약점을 악용할 수 있는 환경이 만들어집니다.

![클라우드 시대의 보안 이점][1]

조직에서는 공급자의 클라우드 기반 보안 기능 및 클라우드 인텔리전스를 사용하여 위협 요소 탐지 및 응답 시간을 향상시킬 수 있습니다.  조직은 클라우드 공급자에 책임을 전가함으로써 보안 적용 범위를 더 넓힐 수 있으며, 보안 리소스와 예산을 다른 비즈니스 우선 순위에 다시 할당할 수 있습니다.

## <a name="division-of-responsibility"></a>책임 분담
사용자와 Microsoft 간의 책임 분담을 이해해야 합니다. 온-프레미스에서는 전체 스택을 가지고 있지만 클라우드로 이동할 때 일부 책임이 Microsoft로 이전됩니다. 다음 책임 매트릭스에서는 사용자와 Microsoft가 책임을 지는 SaaS, PaaS 및 IaaS 배포의 스택 영역을 보여 줍니다.

![책임 영역][2]

모든 클라우드 배포 유형에 대해 사용자는 고유의 데이터와 ID를 소유합니다. 사용자는 자신이 제어하는 데이터 및 ID 보안, 온-프레미스 리소스 및 클라우드 구성 요소(서비스 유형에 따라 다름)의 보안을 보호하는 데 책임이 있습니다.

배포 유형에 관계 없이 사용자가 항상 책임을 지는 영역은 다음과 같습니다.

- Data
- 엔드포인트
- 계좌
- 액세스 관리

## <a name="security-advantages-of-a-paas-cloud-service-model"></a>PaaS 클라우드 서비스 모델의 보안 이점
동일한 책임 매트릭스를 사용하여 Azure PaaS 배포와 온-프레미스 배포 간의 보안 이점을 비교해 보겠습니다.

![PaaS의 보안 이점][3]

Microsoft는 물리적 인프라인 스택 맨 아래부터 시작하여 일반적인 위험과 책임을 덜어줍니다. Microsoft 클라우드는 Microsoft에서 지속적으로 모니터링하므로 공격하기가 어렵습니다. 따라서 공격자가 Microsoft 클라우드를 목표로 삼는 것은 의미가 없습니다. 공격자가 많은 돈과 자원을 갖고 있지 않으면 다른 대상으로 이동할 가능성이 있습니다.  

스택 중간에 수행되는 PaaS 배포와 온-프레미스 배포 간에는 차이가 없습니다. 유사한 위험이 애플리케이션 계층과 계정/액세스 관리 계층에도 있습니다. 이 문서의 다음 단계 섹션에서는 이러한 위험을 제거하거나 최소화하는 모범 사례로 안내합니다.

스택 맨 위에는 데이터 관리와 권한 관리가 있으며 키 관리를 통해 완화될 수 있는 위험이 하나 있습니다. (키 관리는 모범 사례에서 설명합니다.) 키 관리는 추가적인 책임이지만 더 이상 관리할 필요가 없는 PaaS 배포 영역이 있으므로 리소스를 키 관리로 이동할 수 있습니다.

또한 Azure 플랫폼은 다양한 네트워크 기반 기술을 사용하여 강력한 DDoS 보호 기능을 제공합니다. 하지만 모든 유형의 네트워크 기반 DDoS 보호 방법에는 링크별 및 데이터 센터별로 제한이 있습니다. 대규모 DDoS 공격의 영향을 피하기 위해 Azure의 핵심 클라우드 기능을 활용하여 DDoS 공격으로부터 자동으로 신속하게 확장할 수 있습니다. 권장 사례 항목에서 이 작업을 수행하는 방법에 대해 자세히 설명하겠습니다.

## <a name="modernizing-the-defenders-mindset"></a>방어자의 사고 방식 현대화
PaaS를 배포하면 전반적인 보안 접근 방식이 바뀌게 됩니다. 스스로 모든 것을 제어해야 하는 것에서 Microsoft와 책임을 분담하는 것으로 태도를 바꾸어야 합니다.

PaaS 배포와 기존 온-프레미스 배포 간의 또 다른 중요한 차이점은 기본 보안 경계를 정의하는 새로운 개념에 있습니다. 전통적으로 기본 온-프레미스 보안 경계는 네트워크였으며 대부분의 온-프레미스 보안 설계에서는 네트워크를 기본 보안 중심축으로 사용하고 있습니다. 하지만 PaaS 배포의 경우 ID가 기본 보안 경계라고 생각하면 더 효과적으로 수행할 수 있습니다.

## <a name="adopt-a-policy-of-identity-as-the-primary-security-perimeter"></a>기본 보안 경계로서의 ID 정책 채택
클라우드 컴퓨팅의 필수적인 다섯 가지 특성 중 하나는 광범위한 네트워크 액세스이며, 이는 네트워크 중심적 사고의 관련성을 떨어뜨립니다. 클라우드 컴퓨팅의 목표는 사용자가 위치에 관계 없이 리소스에 액세스할 수 있도록 하는 것입니다. 대부분의 사용자에 대한 위치는 인터넷 어딘가에 있을 것입니다.

다음 그림에서는 보안 경계가 네트워크 경계에서 ID 경계까지 전개되는 방식을 보여 줍니다. 보안 수준이 네트워크 보호에 대해서는 낮아지고 데이터를 보호하고 앱과 사용자의 보안을 관리하는 방법에 대해서는 높아집니다. 주요 차이점은 보안을 회사의 중요한 요소에 더 가깝게 두려는 것입니다.

![새로운 보안 경계로서의 ID][4]

초기에는 Azure PaaS 서비스(예: 웹 역할 및 Azure SQL)에서 기존의 네트워크 경계 방어를 거의 또는 전혀 제공하지 않았습니다. 요소의 목적은 인터넷(웹 역할)에 노출하는 것이고 인증은 새로운 경계(예: BLOB 또는 Azure SQL)를 제공한다는 것이 이해되었습니다.

최신 보안 사례에서는 공격자가 네트워크 경계를 침범했다고 가정합니다. 따라서 최신 방어 사례가 ID로 전환했습니다. 조직은 강력한 인증 및 권한 부여(모범 사례)로 ID 기반 보안 경계를 설정해야 합니다.

네트워크 경계의 원리와 패턴은 수십 년 동안 사용할 수 있었습니다. 반면 업계에서는 ID를 기본 보안 경계로 사용한 경험이 비교적 적습니다. 그렇게 말하면서도 현장에서 증명되고 거의 모든 PaaS 서비스에 적용할 수 있는 몇 가지 일반적인 권장 사항을 제공하기에 충분한 경험을 축적했습니다.

ID 경계 관리를 위한 모범 사례는 다음과 같습니다.

**모범 사례**: 키와 자격 증명을 안전하게 보관하여 PaaS 배포를 보호합니다.   
**세부 정보**: 키와 자격 증명을 잃어 버리는 것은 일반적인 문제입니다. 키 및 비밀을 Hsm (하드웨어 보안 모듈)에 저장할 수 있습니다는 중앙 집중식된 솔루션을 사용할 수 있습니다. [Azure Key Vault](../key-vault/key-vault-whatis.md) 인증 키, 저장소 계정 키, 데이터 암호화 키, pfx 파일 및 Hsm으로 보호 되는 키를 사용 하 여 암호를 암호화 하 여 키와 비밀을 보호 합니다.

**모범 사례**: 소스 코드 또는 GitHub에 자격 증명 및 기타 비밀을 포함하지 않습니다.   
**세부 정보**: 키 및 자격 증명을 분실하는 것보다 더 위험한 상황은 권한이 없는 사람이 해당 정보에 액세스하는 것뿐입니다. 공격자는 봇 기술을 이용하여 GitHub와 같은 코드 리포지토리에 저장된 키와 비밀을 찾을 수 있습니다. 따라서 공용 코드 리포지토리에 키와 비밀 정보를 보관해서는 안 됩니다.

**모범 사례**: VM을 직접 원격 관리하는 데 사용할 수 있는 관리 인터페이스를 사용하여 하이브리드 PaaS 및 IaaS 서비스의 VM 관리 인터페이스를 보호합니다.   
**세부 정보**: [SSH](https://en.wikipedia.org/wiki/Secure_Shell), [RDP](https://support.microsoft.com/kb/186607), [PowerShell 원격](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.core/enable-psremoting) 등의 원격 관리 프로토콜을 사용할 수 있습니다. 일반적으로는 인터넷에서 VM에 대해 직접 원격 액세스를 사용하지 않는 것이 좋습니다.

가능한 경우에는 Azure Virtual Network에서 가상 사설망을 사용하는 등의 다른 방법을 사용해야 합니다. 다른 방법을 사용할 수 없는 경우 복잡한 암호 구문을 사용하고 2단계 인증(예: [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md))을 사용해야 합니다.

**모범 사례**: 강력한 인증 및 권한 부여 플랫폼을 사용합니다.   
**세부 정보**: 사용자 지정 사용자 저장소 대신 Azure AD의 페더레이션 ID를 사용합니다. 페더레이션 ID를 사용하면 플랫폼 기반 접근 방식을 활용하고 권한 있는 ID 관리를 파트너에게 위임합니다. 페더레이션 ID 접근 방식은 직원 퇴사 시에 여러 ID와 권한 부여 시스템을 통해 퇴사 정보를 반영해야 하는 경우에 특히 중요합니다.

사용자 지정 코드 대신 플랫폼에서 제공하는 인증 및 권한 부여 메커니즘을 사용합니다. 이는 사용자 지정 인증 코드를 개발하면 오류가 발생할 수 있기 때문입니다. 대부분의 개발자는 보안 전문가가 아니며 인증 및 권한 부여의 미묘한 측면과 최신 개발 정보를 알지 못할 것입니다. Microsoft에서 제공하는 코드와 같은 상용 코드의 경우 광범위하게 보안을 검토하는 경우가 많습니다.

따라서 2단계 인증을 사용해야 합니다. 2단계 인증은 인증의 사용자 이름과 암호 유형에 내재된 보안 약점을 방지할 수 있기 때문에 인증 및 권한 부여의 현재 표준입니다. Azure 관리(포털/원격 PowerShell) 인터페이스와 고객 대상 서비스에 대한 액세스는 [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)을 사용하도록 설계되고 구성되어야 합니다.

OAuth2 및 Kerberos와 같은 표준 인증 프로토콜을 사용합니다. 이러한 프로토콜은 광범위하게 검토되었으며 인증 및 권한 부여를 위한 플랫폼 라이브러리의 일부로 구현될 수 있습니다.

## <a name="use-threat-modeling-during-application-design"></a>애플리케이션 디자인 과정에서 위협 모델링 사용
Microsoft [Security Development Lifecycle](https://www.microsoft.com/en-us/sdl)에서는 디자인 단계에서 팀이 위협 모델링 프로세스를 진행해야 하도록 지정하고 있습니다. Microsoft는 이 프로세스를 원활하게 진행할 수 있도록 [SDL Threat Modeling Tool](../security/azure-security-threat-modeling-tool.md)을 제작했습니다. 애플리케이션 디자인을 모델링하고 모든 보안 경계에 걸쳐 [STRIDE](https://docs.google.com/viewer?a=v&pid=sites&srcid=ZGVmYXVsdGRvbWFpbnxzZWN1cmVwcm9ncmFtbWluZ3xneDo0MTY1MmM0ZDI0ZjQ4ZDMy) 위협을 열거하면 디자인 오류를 초기에 파악할 수 있습니다.

아래 표에는 STRIDE 위협 및 Azure 기능을 사용하는 몇 가지 완화 방식의 예가 나와 있습니다. 이러한 완화 방식을 모든 상황에서 사용할 수 있는 것은 아닙니다.

| 위협 | 보안 속성 | Azure 플랫폼 완화 가능성 |
| --- | --- | --- |
| 스푸핑 | Authentication | HTTPS 연결을 사용해야 하도록 지정합니다. |
| 변조 | 무결성 | SSL 인증서 유효성을 검사합니다. |
| 거부 | 거부 없음 | Azure [모니터링 및 진단](https://docs.microsoft.com/azure/architecture/best-practices/monitoring)을 사용하도록 설정합니다. |
| 정보 공개 | 기밀성 | [서비스 인증서](https://docs.microsoft.com/rest/api/appservice/certificates)를 사용하여 미사용 상태의 중요한 데이터를 암호화합니다. |
| 서비스 거부 | 가용성 | 성능 메트릭에서 서비스 거부 상황 가능성을 모니터링합니다. 연결 필터를 구현합니다. |
| 권한 상승 | 권한 부여 | [Privileged Identity Management](../active-directory/privileged-identity-management/subscription-requirements.md)를 사용합니다. |

## <a name="develop-on-azure-app-service"></a>Azure App Service에서 개발 진행
[Azure App Service](../app-service/overview.md)는 플랫폼이나 디바이스를 위한 웹 및 모바일 앱을 만들고 어디서나 클라우드 또는 온-프레미스에 있는 데이터에 연결할 수 있는 PaaS 서비스 제품군입니다. 앱 서비스는 이전에 개별적으로 Azure 웹 사이트 및 Azure Mobile Services로 전달한 웹 및 모바일 기능을 포함합니다. 또한 비즈니스 프로세스를 자동화하고 클라우드 API를 호스팅하는 새 기능을 포함합니다. App Service는 단일 통합 서비스로서 웹, 모바일 및 통합 시나리오에 풍부한 기능 집합을 제공합니다.

App Service 사용 시의 모범 사례는 다음과 같습니다.

**모범 사례**: [Azure Active Directory를 통해 인증](../app-service/overview-authentication-authorization.md)합니다.   
**세부 정보**: App Service는 ID 공급자를 위한 OAuth 2.0 서비스를 제공합니다. OAuth 2.0은 클라이언트 개발자의 단순성에 기반하여 웹 응용 프로그램, 데스크톱 응용 프로그램 및 휴대폰에 대한 특정 권한 부여 흐름을 제공합니다. Azure AD는 OAuth 2.0을 사용하여 모바일 및 웹 애플리케이션에 대한 액세스 권한을 부여합니다.

**모범 사례**: 알아야 할 필요성과 최소 권한 보안 원칙에 따라 액세스를 제한합니다.   
**세부 정보**: 데이터 액세스를 위한 보안 정책을 시행하려는 조직에서는 액세스를 반드시 제한해야 합니다. RBAC를 사용하여 특정 범위에서 사용자, 그룹 및 애플리케이션에 권한을 할당할 수 있습니다. 사용자에게 애플리케이션 액세스 권한을 부여하는 방법에 대해 자세히 알아보려면 [액세스 관리 시작](../role-based-access-control/overview.md)을 참조하세요.

**모범 사례**: 키를 보호합니다.   
**세부 정보**: Azure Key Vault를 사용하면 클라우드 애플리케이션과 서비스에 사용되는 암호화 키 및 비밀을 보호할 수 있습니다. Key Vault를 사용하면 HSM(하드웨어 보안 모듈)을 통해 보호되는 키를 통해 키와 비밀(예: 인증 키, 저장소 계정 키, 데이터 암호화 키, .PFX 파일 및 암호)를 암호화할 수 있습니다. 추가된 보증을 위해, HSM에서 키를 생성하거나 가져올 수 있습니다. 자세한 내용은 [Azure Key Vault](../key-vault/key-vault-whatis.md)를 참조하세요. 또한 Key Vault를 사용하여 자동 갱신으로 TLS 인증서를 관리할 수도 있습니다.

**모범 사례**: 들어오는 원본 IP 주소를 제한합니다.   
**세부 정보**: [App Service Environment](../app-service/environment/intro.md)에는 네트워크 보안 그룹을 통해 들어오는 원본 IP 주소를 제한하는 데 도움이 되는 가상 네트워크 통합 기능이 있습니다. 가상 네트워크에서는 액세스를 제어할 수 있는 인터넷이 아닌 라우팅 가능 네트워크에 Azure 리소스를 배치할 수 있습니다. 자세히 알아보려면 [Azure Virtual Network에 앱 통합](../app-service/web-sites-integrate-with-vnet.md)을 참조하세요.

**모범 사례**: App Service 환경의 보안 상태를 모니터링합니다.   
**세부 정보**: Azure Security Center를 사용하여 App Service 환경을 모니터링합니다. Security Center는 잠재적 보안 취약성이 확인되면 필요한 컨트롤을 구성하는 과정을 안내하는 [권장 사항](../security-center/security-center-virtual-machine-recommendations.md)을 만듭니다.

> [!NOTE]
> Monitoring App Service는 미리 보기로 제공되며 Security Center의 [표준 계층](../security-center/security-center-pricing.md)에서만 사용할 수 있습니다.
>
>

## <a name="install-a-web-application-firewall"></a>웹 애플리케이션 방화벽 설치
웹 애플리케이션의 널리 알려진 취약점을 악용하는 악의적인 공격이 점점 많아지고 있습니다. 이러한 공격으로는 SQL 삽입 공격, 사이트 간 스크립팅 공격 등이 있습니다. 응용 프로그램 코드로 이러한 공격을 방어하기란 매우 어려울 수 있으며 응용 프로그램 토폴로지의 다양한 계층에서 엄격한 유지 관리, 패치 적용 및 모니터링이 필요할 수 있습니다. 중앙 집중식 웹 응용 프로그램 방화벽을 통해 보안 관리가 훨씬 간단해지고 응용 프로그램 관리자에게 위협 또는 침입으로부터 효과적인 보호를 제공합니다. 또한 WAF 솔루션은 각각의 웹 응용 프로그램을 보호하는 대신 중앙의 위치에서 알려진 취약점에 패치를 적용하여 보다 신속하게 보안 위협에 대응할 수 있습니다. 기존 Application Gateway는 웹 애플리케이션 방화벽을 사용한 Application Gateway로 쉽게 변환될 수 있습니다.

[WAF(웹 애플리케이션 방화벽)](../application-gateway/waf-overview.md)는 일반적인 악용 및 취약점으로부터 웹 애플리케이션에 대해 중앙 집중화된 보호를 제공하는 Application Gateway의 기능입니다. WAF는 [OWASP(Open Web Application Security Project) 핵심 규칙 집합](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 또는 2.2.9의 규칙에 기반합니다.

## <a name="monitor-the-performance-of-your-applications"></a>애플리케이션 성능 모니터링
모니터링은 애플리케이션의 성능, 상태 및 가용성을 확인하기 위해 데이터를 수집 및 분석하는 작업입니다. 효과적인 모니터링 전략은 애플리케이션 구성 요소의 세부 작업을 이해하는 데 도움이 됩니다. 또한 중요한 문제의 알림이 제공되므로 문제가 커지기 전에 해결하여 작동 시간을 늘릴 수 있습니다. 그리고 보안과 관련이 있을 수 있는 변칙적인 상황도 검색할 수 있습니다.

[Azure Application Insights](https://azure.microsoft.com/documentation/services/application-insights)를 사용하여 클라우드 또는 온-프레미스에서 호스트되는 애플리케이션의 가용성, 성능 및 사용량을 모니터링합니다. Application Insights를 사용하면 사용자가 보고할 때까지 기다리지 않고 애플리케이션의 오류를 빠르게 식별하고 진단할 수 있습니다. 수집한 정보를 사용하여, 애플리케이션의 유지 관리 및 개선에 대해 현명한 결정을 내릴 수 있습니다.

Application Insight에는 수집하는 데이터와 상호 작용할 수 있는 광범위한 도구가 있습니다. Application Insights는 공용 저장소에 데이터를 저장합니다. 경고, 대시보드 및 Kusto 쿼리 언어를 사용 하 여 심층 분석과 같은 공유 기능을 활용을 걸릴 수 있습니다.



## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure PaaS 배포의 보안 이점과 클라우드 애플리케이션의 보안 모범 사례에 대해 중점적으로 설명했습니다. 다음 단계에서는 특정 Azure 서비스를 사용하여 PaaS 웹 및 모바일 솔루션을 보호하는 권장 사례에 대해 알아보겠습니다. 먼저 Azure App Service, Azure SQL Database, Azure SQL Data Warehouse 및 Azure Storage를 살펴보겠습니다. 다른 Azure 서비스에 권장되는 사례에 대한 문서를 사용할 수 있으며, 해당 링크는 다음 목록에서 제공합니다.

- [Azure App Service](security-paas-applications-using-app-services.md)
- [Azure SQL Database 및 Azure SQL Data Warehouse](security-paas-applications-using-sql.md)
- [Azure Storage](security-paas-applications-using-storage.md)
- Azure Cache for Redis
- Azure Service Bus
- 웹 애플리케이션 방화벽

[Azure 보안 모범 사례 및 패턴](security-best-practices-and-patterns.md)에서 Azure를 사용하여 클라우드 솔루션을 디자인하고, 배포하고, 관리할 때 사용할 수 있는 더 많은 보안 모범 사례를 참조하세요.

Azure 보안 및 관련 Microsoft 서비스에 대한 보다 일반적인 정보를 제공하는 다음 리소스도 확인할 수 있습니다.
* [Azure 보안 팀 블로그](https://blogs.msdn.microsoft.com/azuresecurity/) – Azure Security 관련 최신 정보를 확인할 수 있습니다.
* [Microsoft 보안 응답 센터](https://technet.microsoft.com/library/dn440717.aspx) - Azure와 관련된 문제를 비롯한 Microsoft 보안 취약점을 보고하거나 secure@microsoft.com으로 이메일을 보낼 수 있습니다.

<!--Image references-->
[1]: ./media/security-paas-deployments/advantages-of-cloud.png
[2]: ./media/security-paas-deployments/responsibility-zones.png
[3]: ./media/security-paas-deployments/advantages-of-paas.png
[4]: ./media/security-paas-deployments/identity-perimeter.png
