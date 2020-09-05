---
title: Azure Active Directory 응용 프로그램 프록시 배포 계획
description: 조직 내 응용 프로그램 프록시 배포를 계획 하기 위한 종단 간 가이드
services: active-directory
documentationcenter: azure
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/04/2019
ms.author: kenwith
ms.openlocfilehash: 09a930778e35897671d10f14a95f3fa48ea5e9eb
ms.sourcegitcommit: 628be49d29421a638c8a479452d78ba1c9f7c8e4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88642421"
---
# <a name="plan-an-azure-ad-application-proxy-deployment"></a>Azure AD 애플리케이션 프록시 배포 계획

Azure Active Directory (Azure AD) 응용 프로그램 프록시는 온-프레미스 응용 프로그램에 대 한 안전 하 고 비용 효율적인 원격 액세스 솔루션입니다. 이 기능은 아직 최신 프로토콜을 사용할 수 없는 레거시 온-프레미스 응용 프로그램에 대 한 액세스를 관리할 수 있는 "클라우드 우선" 조직에 대 한 즉각적인 전환 경로를 제공 합니다. 추가 소개 정보 [는 응용 프로그램 프록시 정의](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)를 참조 하세요.

원격 사용자에 게 내부 리소스에 대 한 액세스를 제공 하기 위해 응용 프로그램 프록시가 권장 됩니다. 응용 프로그램 프록시는 이러한 원격 액세스 사용 사례에 대 한 VPN 또는 역방향 프록시에 대 한 필요성을 대체 합니다. 회사 네트워크에 있는 사용자에 게는 적합 하지 않습니다. 인트라넷 액세스에 응용 프로그램 프록시를 사용 하는 이러한 사용자에 게는 바람직하지 않은 성능 문제가 발생할 수 있습니다.

이 문서에는 Azure AD 응용 프로그램 프록시를 계획, 운영 및 관리 하는 데 필요한 리소스가 포함 되어 있습니다.

## <a name="plan-your-implementation"></a>구현 계획

다음 섹션에서는 효율적인 배포 환경에 맞게 설정 하는 주요 계획 요소를 광범위 하 게 보여 줍니다.

### <a name="prerequisites"></a>사전 요구 사항

구현을 시작 하기 전에 다음 필수 구성 요소를 충족 해야 합니다. 이러한 필수 구성 요소를 포함 하 여 환경을 설정 하는 방법에 대 한 자세한 내용은이 [자습서](application-proxy-add-on-premises-application.md)에서 확인할 수 있습니다.

* **커넥터**: 커넥터는 배포할 수 있는 간단한 에이전트입니다.
   * 온-프레미스의 물리적 하드웨어
   * 하이퍼바이저 솔루션 내에서 호스트 되는 VM
   * 응용 프로그램 프록시 서비스에 대 한 아웃 바운드 연결을 사용 하도록 설정 하기 위해 Azure에서 호스트 되는 VM입니다.

* 자세한 개요는 [프록시 커넥터 Azure AD 앱 이해](application-proxy-connectors.md) 를 참조 하세요.

     * 커넥터를 설치 하기 전에 커넥터 컴퓨터에서 [TLS 1.2를 사용 하도록 설정](application-proxy-add-on-premises-application.md) 해야 합니다.

     * 가능 하면 백 엔드 웹 응용 프로그램 서버와 [동일한 네트워크](application-proxy-network-topology.md) 및 세그먼트에 커넥터를 배포 합니다. 응용 프로그램 검색을 완료 한 후 커넥터를 배포 하는 것이 가장 좋습니다.
     * 고가용성 및 확장성을 제공 하기 위해 커넥터 그룹에는 두 개 이상의 커넥터가 있는 것이 좋습니다. 3 개의 커넥터가 있는 경우 언제 든 지 컴퓨터를 서비스 해야 할 수 있습니다. [커넥터 용량 표](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#capacity-planning) 를 검토 하 여 커넥터를 설치할 컴퓨터 유형을 결정 하는 데 도움을 줍니다. 시스템 크기가 클수록 버퍼가 증가 하 고 커넥터가 성능이 향상 됩니다.

* **네트워크 액세스 설정**: azure AD 응용 프로그램 프록시 커넥터는 [HTTPS (tcp 포트 443) 및 HTTP (tcp 포트 80)를 통해 azure에 연결](application-proxy-add-on-premises-application.md)됩니다.

   * 커넥터를 종료 하는 것은 지원 되지 않으며 커넥터에서 해당 Azure 앱 프록시 끝점을 사용 하 여 보안 채널을 설정 하는 것을 방지 합니다.

   * 커넥터와 Azure 간의 아웃 바운드 TLS 통신에 대 한 모든 형태의 인라인 검사를 방지 합니다. 커넥터와 백 엔드 응용 프로그램 간의 내부 검사는 가능 하지만 사용자 환경이 저하 될 수 있으므로 권장 하지 않습니다.

   * 커넥터 자체의 부하 분산도 지원 되거나 필요 하지 않습니다.

### <a name="important-considerations-before-configuring-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 구성 하기 전에 중요 한 고려 사항

Azure AD 응용 프로그램 프록시를 구성 하 고 구현 하려면 다음 핵심 요구 사항을 충족 해야 합니다.

*  **Azure 온 보 딩**: 응용 프로그램 프록시를 배포 하기 전에 사용자 id를 온-프레미스 디렉터리에서 동기화 하거나 Azure AD 테 넌 트 내에서 직접 만들어야 합니다. ID 동기화를 통해 Azure AD가 사용자에게 App Proxy 게시 애플리케이션에 대한 액세스 권한을 부여하기 전에 미리 인증하고 SSO(Single Sign-On)를 수행하는 데 필요한 사용자 식별자 정보를 가질 수 있습니다.

* **조건부 액세스 요구 사항**: 사용자에 게 영향을 주는 대기 시간을 추가 하기 때문에 인트라넷 액세스에 응용 프로그램 프록시를 사용 하지 않는 것이 좋습니다. 인터넷에서 원격 액세스에 대 한 사전 인증 및 조건부 액세스 정책과 함께 응용 프로그램 프록시를 사용 하는 것이 좋습니다.  인트라넷 사용에 대 한 조건부 액세스를 제공 하는 방법은 응용 프로그램을 현대화 하 여 AAD로 직접 인증할 수 있도록 하는 것입니다. 자세한 내용은 [AAD로 응용 프로그램 마이그레이션에 대 한 리소스](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) 를 참조 하세요.

* **서비스 제한**: 개별 테 넌 트가 과도 하 게 리소스를 사용 하지 않도록 보호 하기 위해 응용 프로그램 및 테 넌 트 당 제한 한도가 설정 됩니다. 이러한 한도를 확인 하려면 [AZURE AD 서비스 제한 및 제한 사항](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)을 참조 하세요. 이러한 제한 제한은 일반적인 사용량 볼륨 위에 있는 벤치 마크를 기반으로 하며 대다수 배포에 대 한 충분 한 버퍼를 제공 합니다.

* **공용 인증서**: 사용자 지정 도메인 이름을 사용 하는 경우 TLS/SSL 인증서를 사용 해야 합니다. 조직의 요구 사항에 따라 인증서를 가져오는 데 약간의 시간이 걸릴 수 있으므로 가능한 한 빨리 프로세스를 시작 하는 것이 좋습니다. Azure 애플리케이션 프록시는 표준, [와일드 카드](application-proxy-wildcard.md)또는 SAN 기반 인증서를 지원 합니다. 자세한 내용은 [Azure AD 응용 프로그램 프록시를 사용 하 여 사용자 지정 도메인 구성](application-proxy-configure-custom-domain.md)을 참조 하세요.

* **도메인 요구 사항**: Kcd (Kerberos 제한 위임)를 사용 하 여 게시 된 응용 프로그램에 Single sign-on을 사용 하려면 커넥터를 실행 하는 서버와 앱을 실행 하는 서버가 도메인에 가입 되어 있고 동일한 도메인 또는 트러스팅 도메인에 속해 있어야 합니다.
항목에 대 한 자세한 내용은 응용 프로그램 프록시를 사용 하는 [Single Sign-On 용 Kcd](application-proxy-configure-single-sign-on-with-kcd.md) 를 참조 하세요. 커넥터 서비스는 로컬 시스템의 컨텍스트에서 실행 되며 사용자 지정 id를 사용 하도록 구성 되지 않아야 합니다.

* **Url에 대 한 DNS 레코드**

   * 응용 프로그램 프록시에서 사용자 지정 도메인을 사용 하려면 먼저 공용 DNS에서 CNAME 레코드를 만들어야 합니다 .이를 통해 클라이언트는 미리 정의 된 사용자 지정 외부 URL을 미리 정의 된 응용 프로그램 프록시 주소로 확인할 수 있습니다. 사용자 지정 도메인을 사용 하는 응용 프로그램에 대 한 CNAME 레코드를 만들지 못하면 원격 사용자가 응용 프로그램에 연결 하지 못합니다. CNAME 레코드를 추가 하는 데 필요한 단계는 DNS 공급자 마다 다를 수 있으므로 [Azure Portal를 사용 하 여 dns 레코드 및 레코드 집합을 관리](https://docs.microsoft.com/azure/dns/dns-operations-recordsets-portal)하는 방법을 알아봅니다.

   * 마찬가지로 커넥터 호스트는 게시 중인 응용 프로그램의 내부 URL을 확인할 수 있어야 합니다.

* **관리 권한 및 역할**

   * **커넥터를 설치** 하려면 설치 되는 Windows server에 대 한 로컬 관리자 권한이 있어야 합니다. 또한 인증 하 고 Azure AD 테 넌 트에 커넥터 인스턴스를 등록 하려면 최소한의 *응용 프로그램 관리자* 역할이 필요 합니다.

   * 응용 프로그램 **게시 및 관리** 에는 *응용 프로그램 관리자* 역할이 필요 합니다. 응용 프로그램 관리자는 등록, SSO 설정, 사용자 및 그룹 할당/라이선스, 응용 프로그램 프록시 설정 및 동의를 포함 하 여 디렉터리에 있는 모든 응용 프로그램을 관리할 수 있습니다. 조건부 액세스를 관리하는 기능은 부여하지 않습니다. *클라우드 응용 프로그램 관리자* 역할에는 응용 프로그램 프록시 설정 관리를 허용 하지 않는다는 점을 제외 하 고 응용 프로그램 관리자의 모든 기능이 있습니다.

* **라이선스**: 응용 프로그램 프록시는 Azure AD Premium 구독을 통해 사용할 수 있습니다. 라이선스 옵션 및 기능의 전체 목록은 [Azure Active Directory 가격 책정 페이지](https://azure.microsoft.com/pricing/details/active-directory/) 를 참조 하세요.

### <a name="application-discovery"></a>응용 프로그램 검색

다음 정보를 수집 하 여 응용 프로그램 프록시를 통해 게시 되는 모든 범위 내 응용 프로그램의 인벤토리를 컴파일합니다.

| Information Type| 수집할 정보 |
|---|---|
| 서비스 종류| 예: SharePoint, SAP, CRM, 사용자 지정 웹 응용 프로그램, API |
| 애플리케이션 플랫폼 | 예: Windows IIS, Linux의 Apache, Tomcat, NGINX |
| 도메인 멤버 자격| 웹 서버의 FQDN (정규화 된 도메인 이름) |
| 응용 프로그램 위치 | 웹 서버 또는 팜이 인프라에 있는 위치 |
| 내부 액세스 | 내부적으로 응용 프로그램에 액세스할 때 사용 되는 정확한 URL입니다. <br> 팜이 사용 중인 부하 분산 유형은 무엇 인가요? <br> 응용 프로그램이 자체가 아닌 소스에서 콘텐츠를 그릴지 여부를 지정 합니다.<br> 응용 프로그램이 Websocket을 통해 작동 하는지 확인 합니다. |
| 외부 액세스 | 응용 프로그램이 이미 외부에 노출 된 공급 업체 솔루션입니다. <br> 외부 액세스에 사용할 URL입니다. SharePoint 인 경우 [이 지침](https://docs.microsoft.com/SharePoint/administration/configure-alternate-access-mappings)에 따라 대체 액세스 매핑을 구성 해야 합니다. 그렇지 않은 경우에는 외부 Url을 정의 해야 합니다. |
| 공용 인증서 | 사용자 지정 도메인을 사용 하는 경우 해당 주체 이름으로 인증서를 확보 합니다. 인증서가 있는 경우 일련 번호와 해당 위치에서 가져올 수 있는 위치를 확인 합니다. |
| 인증 유형| 응용 프로그램 지원에서 지원 되는 인증 유형 (예: 기본, Windows 통합 인증, 양식 기반, 헤더 기반 및 클레임)입니다. <br>응용 프로그램이 특정 도메인 계정으로 실행 되도록 구성 된 경우 서비스 계정의 FQDN (정규화 된 도메인 이름)을 확인 합니다.<br> SAML 기반 인 경우 식별자 및 회신 Url입니다. <br> 헤더 기반 인 경우 공급 업체 솔루션 이며 인증 유형을 처리 하기 위한 특정 요구 사항입니다. |
| 커넥터 그룹 이름 | 이 백 엔드 응용 프로그램에 대 한 통로 및 SSO를 제공 하도록 지정 되는 커넥터 그룹의 논리적 이름입니다. |
| 사용자/그룹 액세스 | 응용 프로그램에 대 한 외부 액세스 권한이 부여 되는 사용자 또는 사용자 그룹입니다. |
| 추가 요구 사항 | 응용 프로그램을 게시 하기 위해 고려해 야 하는 추가 원격 액세스 또는 보안 요구 사항을 확인 합니다. |

이 [응용 프로그램 인벤토리 스프레드시트](https://aka.ms/appdiscovery) 를 다운로드 하 여 앱을 인벤토리에 추가할 수 있습니다.

### <a name="define-organizational-requirements"></a>조직 요구 사항 정의

다음은 조직의 비즈니스 요구 사항에 대해 정의 해야 하는 영역입니다. 각 영역에는 요구 사항의 예가 포함 되어 있습니다.

 **Access**

* 도메인에 가입 된 원격 사용자 또는 Azure AD 조인 장치 사용자는 원활한 SSO (Single Sign-On)를 사용 하 여 게시 된 응용 프로그램에 안전 하 게 액세스할 수 있습니다.

* 승인 된 개인 장치를 사용 하는 원격 사용자는 MFA에 등록 된 경우 게시 된 응용 프로그램에 안전 하 게 액세스할 수 있으며 인증 방법으로 휴대폰에 Microsoft Authenticator 앱을 등록 했습니다.

**거버넌스**

* 관리자는 응용 프로그램 프록시를 통해 게시 된 응용 프로그램에 대 한 사용자 할당의 수명 주기를 정의 하 고 모니터링할 수 있습니다.

**보안**

* 그룹 멤버 자격을 통해 또는 개별적으로 응용 프로그램에 할당 된 사용자만 이러한 응용 프로그램에 액세스할 수 있습니다.

**성능**

* 내부 네트워크에서 응용 프로그램에 액세스 하는 것과 비교 하 여 응용 프로그램 성능 저하가 발생 하지 않습니다.

**사용자 환경**

* 사용자는 모든 장치 플랫폼에서 친숙 한 회사 Url을 사용 하 여 응용 프로그램에 액세스 하는 방법을 알고 있습니다.

**감사**
* 관리자는 사용자 액세스 작업을 감사할 수 있습니다.


### <a name="best-practices-for-a-pilot"></a>파일럿에 대 한 모범 사례

SSO (Single sign-on)를 사용 하 여 단일 응용 프로그램에 대해 원격 액세스를 완전히 수행 하는 데 필요한 시간과 노력을 결정 합니다. 초기 검색, 게시 및 일반 테스트를 고려 하는 파일럿을 실행 하 여이 작업을 수행 합니다. IWA (Windows 통합 인증)에 미리 구성 된 간단한 IIS 기반 웹 응용 프로그램을 사용 하는 경우 원격 액세스 및 SSO를 성공적으로 파일럿 하기 위해 최소한의 노력이 필요 하므로 기준선을 설정 하는 데 도움이 됩니다.

다음 디자인 요소는 프로덕션 테 넌 트에서 직접 파일럿 구현의 성공을 높입니다.

**커넥터 관리**:

* 커넥터는 응용 프로그램에 온-프레미스 통로를 제공 하는 데 중요 한 역할을 합니다. **기본** 커넥터 그룹을 사용 하는 것은 게시 된 응용 프로그램을 프로덕션 환경으로 위탁 하기 전에 테스트 하는 초기 파일럿 테스트에 적합 합니다. 테스트를 완료 한 후 프로덕션 커넥터 그룹으로 응용 프로그램을 이동할 수 있습니다.

**응용 프로그램 관리**:

* 직원은 일반적으로 외부 URL이 친숙 하 고 관련성이 높은 것을 기억할 수 있습니다. 미리 정의 된 msappproxy.net 또는 onmicrosoft.com 접미사를 사용 하 여 응용 프로그램을 게시 하지 마세요. 대신, 인트라넷과 같은 논리적 호스트 이름이 접두사로 추가 된 친숙 한 최상위 수준의 확인 된 도메인을 제공 합니다. *<customers_domain> .com*입니다.

* Azure MyApps 포털에서 시작 아이콘을 숨겨 파일럿 응용 프로그램의 아이콘 표시를 파일럿 그룹으로 제한 합니다. 프로덕션 준비가 완료 되 면 동일한 프리 프로덕션 테 넌 트에서 응용 프로그램을 대상으로 지정 된 대상 사용자로 지정 하거나 프로덕션 테 넌 트에 응용 프로그램을 게시할 수도 있습니다.

**Single sign-on 설정**: 일부 SSO 설정에는 설정 하는 데 시간이 걸릴 수 있는 특정 종속성이 있으므로 종속성이 미리 처리 되도록 하 여 변경 제어 지연을 방지할 수 있습니다. 여기에는 KCD (Kerberos 제한 위임)를 사용 하 여 SSO를 수행 하 고 다른 시간의 소비 활동을 처리 하는 도메인 가입 커넥터 호스트가 포함 됩니다. 예를 들어 헤더 기반 SSO가 필요한 경우 PING 액세스 인스턴스를 설정 합니다.

**커넥터 호스트와 대상 응용 프로그램 간 tls**: 보안이 가장 중요 하므로 커넥터 호스트와 대상 응용 프로그램 간의 tls는 항상 사용 해야 합니다. 특히 웹 응용 프로그램이 FBA (폼 기반 인증)에 대해 구성 된 경우 사용자 자격 증명은 일반 텍스트로 효과적으로 전송 됩니다.

**증분을 구현 하 고 각 단계를 테스트**합니다.
응용 프로그램을 게시 한 후 다음 지침에 따라 모든 사용자 및 비즈니스 요구 사항을 충족 하는지 확인 하는 기본적인 기능 테스트를 수행 합니다.

1. 사전 인증을 사용 하지 않는 웹 응용 프로그램에 대 한 일반 액세스를 테스트 하 고 유효성을 검사 합니다.
2. 성공 하면 사전 인증을 사용 하도록 설정 하 고 사용자 및 그룹을 할당 합니다. 액세스를 테스트 하 고 유효성을 검사 합니다.
3. 그런 다음 응용 프로그램에 대 한 SSO 메서드를 추가 하 고 다시 테스트 하 여 액세스의 유효성을 검사 합니다.
4. 필요에 따라 조건부 액세스 및 MFA 정책을 적용 합니다. 액세스를 테스트 하 고 유효성을 검사 합니다.

**문제 해결 도구**: 문제를 해결 하는 경우 항상 커넥터 호스트의 브라우저에서 게시 된 응용 프로그램에 대 한 액세스의 유효성을 검사 하 고 응용 프로그램이 예상 대로 작동 하는지 확인 합니다. 더 간단 하 게 설치 하면 근본 원인을 쉽게 확인할 수 있으므로 단일 커넥터를 사용 하 고 SSO를 사용 하지 않는 것과 같은 최소 구성으로 문제를 재현 하는 것이 좋습니다. 경우에 따라 Telerik의 Fiddler와 같은 웹 디버깅 도구는 프록시를 통해 액세스 하는 응용 프로그램에서 액세스 또는 콘텐츠 문제를 해결 하는 데 필수적인을 입증할 수 있습니다. Fiddler는 iOS, Android 등의 모바일 플랫폼 및 프록시를 통해 라우팅하도록 구성할 수 있는 거의 모든 항목에 대 한 트래픽을 추적 하 고 디버깅 하는 데 도움이 되는 프록시로 동작할 수도 있습니다. 자세한 내용은 [문제 해결 가이드](application-proxy-troubleshoot.md) 를 참조 하세요.

## <a name="implement-your-solution"></a>솔루션 구현

### <a name="deploy-application-proxy"></a>응용 프로그램 프록시 배포

응용 프로그램 프록시를 배포 하는 단계는 [원격 액세스를 위해 온-프레미스 응용 프로그램을 추가 하기 위한이 자습서](application-proxy-add-on-premises-application.md)에 설명 되어 있습니다. 설치에 실패 한 경우 포털에서  **응용 프로그램 프록시 문제 해결**  을 선택 하거나 [응용 프로그램 프록시 에이전트 커넥터를 설치할 때 발생 하는 문제에 대 한](application-proxy-connector-installation-problem.md)문제 해결 가이드를 사용 합니다.

### <a name="publish-applications-via-application-proxy"></a>응용 프로그램 프록시를 통해 응용 프로그램 게시

응용 프로그램 게시는 모든 필수 구성 요소를 충족 하 고 응용 프로그램 프록시 페이지에서 여러 커넥터를 등록 하 고 활성 상태로 표시 하는 것으로 가정 합니다.

[PowerShell](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)을 사용 하 여 응용 프로그램을 게시할 수도 있습니다.

응용 프로그램을 게시할 때 따라야 할 몇 가지 모범 사례는 다음과 같습니다.

* **커넥터 그룹 사용**: 각 응용 프로그램을 게시 하기 위해 지정 된 커넥터 그룹을 할당 합니다. 고가용성 및 확장성을 제공 하기 위해 커넥터 그룹에는 두 개 이상의 커넥터가 있는 것이 좋습니다. 3 개의 커넥터가 있는 경우 언제 든 지 컴퓨터를 서비스 해야 할 수 있습니다. 또한 커넥터 그룹을 [사용 하 여 별도의 네트워크와 위치에 응용 프로그램 게시](application-proxy-connector-groups.md) 를 참조 하 여 커넥터 그룹을 사용 하 여 네트워크 또는 위치로 커넥터를 분할 하는 방법을 확인 합니다.

* **백 엔드 응용 프로그램 제한 시간 설정**:이 설정은 응용 프로그램에서 클라이언트 트랜잭션을 처리 하는 데 75 초 이상이 필요할 수 있는 시나리오에서 유용 합니다. 예를 들어 클라이언트에서 데이터베이스에 대 한 프런트 엔드 역할을 하는 웹 응용 프로그램으로 쿼리를 보내는 경우입니다. 프런트 엔드는 백 엔드 데이터베이스 서버에이 쿼리를 보내고 응답을 받을 때까지 대기 하지만 응답을 받을 때까지 대화의 클라이언트 쪽 시간이 초과 됩니다. 시간 제한 시간을 Long으로 설정 하면 더 긴 트랜잭션이 완료 될 때까지 180 초를 제공 합니다.

* **적절 한 쿠키 유형 사용**

   * **HTTP 전용 쿠키**: 응용 프로그램 프록시에 집합 쿠키 HTTP 응답 헤더에 HTTPOnly 플래그를 포함 하 여 추가 보안을 제공 합니다. 이 설정은 XSS (교차 사이트 스크립팅)와 같은 악용을 완화 하는 데 도움이 됩니다. 세션 쿠키에 대 한 액세스가 필요한 클라이언트/사용자 에이전트에 대해서는이 설정을 아니요로 둡니다. 예를 들어 RDP/MTSC 클라이언트는 앱 프록시를 통해 게시 된 원격 데스크톱 게이트웨이에 연결 합니다.

   * **보안 쿠키**: 보안 특성을 사용 하 여 쿠키를 설정 하는 경우 요청이 TLS 보안 채널을 통해 전송 되는 경우 사용자 에이전트 (클라이언트 쪽 앱)에는 HTTP 요청에만 쿠키가 포함 됩니다. 이렇게 하면 일반 텍스트 채널을 통해 쿠키가 손상 되는 위험을 줄일 수 있으므로 사용 하도록 설정 해야 합니다.

   * **영구 쿠키**: 응용 프로그램 프록시 세션 쿠키가 만료 되거나 삭제 될 때까지 브라우저 클로저를 남은 유효 기간 사이에 유지할 수 있습니다. Office와 같은 리치 응용 프로그램이 사용자가 인증을 다시 요청 하지 않고 게시 된 웹 응용 프로그램 내의 문서에 액세스 하는 시나리오에 사용 됩니다. 그러나 영구적 쿠키가 다른 보정 컨트롤과 함께 사용 되지 않는 경우 궁극적으로는 무단 액세스의 위험에 노출 될 수 있으므로 주의 해 서 사용 해야 합니다. 이 설정은 프로세스 간에 쿠키를 공유할 수 없는 이전 응용 프로그램에만 사용 해야 합니다. 이 설정을 사용 하는 대신 프로세스 간에 쿠키 공유를 처리 하도록 응용 프로그램을 업데이트 하는 것이 좋습니다.

* **헤더의 Url 변환**: 조직의 공용 네임 스페이스와 일치 하도록 내부 dns를 구성할 수 없는 시나리오에 대해이 기능을 사용 하도록 설정 합니다. 응용 프로그램이 클라이언트 요청에서 원래 호스트 헤더를 요구 하지 않는 한이 값을 예로 설정 된 상태로 둡니다. 대신 커넥터에서 실제 트래픽을 라우팅하는 데 내부 URL에 FQDN을 사용 하 고 외부 URL에 FQDN을 사용 하 여 호스트 헤더로 사용 하는 것이 좋습니다. 대부분의 경우이 대안은 응용 프로그램이 원격으로 액세스 될 때 정상적으로 작동 하도록 허용 해야 하지만, 사용자는 URL 외부 &에 일치 하는 이점을 얻을 수 있습니다.

* **응용 프로그램 본문의 Url 변환**: 해당 앱의 링크를 클라이언트로 다시 변환 하려면 앱에 대 한 응용 프로그램 본문 링크 변환을 설정 합니다. 사용 하도록 설정 된 경우이 함수는 응용 프로그램 프록시가 HTML 및 CSS 응답에서 찾은 모든 내부 링크를 클라이언트에 반환 하는 데 가장 적합 한 작업을 제공 합니다. 콘텐츠에 하드 코드 된 절대 또는 NetBIOS 짧은 이름 링크를 포함 하는 앱 또는 다른 온-프레미스 응용 프로그램에 연결 되는 콘텐츠가 있는 앱을 게시 하는 경우에 유용 합니다.

게시 된 앱이 다른 게시 된 앱에 연결 하는 시나리오의 경우 앱 당 수준에서 사용자 환경을 제어할 수 있도록 각 응용 프로그램에 대해 링크 변환을 사용 하도록 설정 합니다.

예를 들어 응용 프로그램 프록시를 통해 응용 프로그램 프록시를 통해 게시 된 세 개의 응용 프로그램 (혜택, 비용, 여행 및 응용 프로그램 프록시를 통해 게시 되지 않은 네 번째 앱, 피드백)이 있다고 가정 합니다.

![그림 1](media/App-proxy-deployment-plan/link-translation.png)

혜택 앱에 대 한 링크 변환을 사용 하도록 설정 하면 비용 및 여행에 대 한 링크가 해당 앱에 대 한 외부 Url로 리디렉션되고 회사 네트워크 외부에서 응용 프로그램에 액세스 하는 사용자가 액세스할 수 있습니다. 이러한 두 앱에 대해 링크 변환을 사용 하도록 설정 하지 않았기 때문에 비용에서의 링크 및 혜택에 대 한 다시 이동은 작동 하지 않습니다. 외부 URL이 없기 때문에 피드백 링크가 리디렉션되지 않으므로 혜택 앱을 사용 하는 사용자는 회사 네트워크 외부에서 피드백 앱에 액세스할 수 없습니다. [링크 변환 및 기타 리디렉션 옵션](application-proxy-configure-hard-coded-link-translation.md)에 대 한 자세한 정보를 참조 하세요.

### <a name="access-your-application"></a>응용 프로그램 액세스

앱 프록시 게시 된 리소스에 대 한 액세스를 관리 하기 위한 몇 가지 옵션이 있습니다. 따라서 지정 된 시나리오 및 확장성 요구 사항에 가장 적합 한 옵션을 선택 합니다. 일반적인 방법으로는 Azure AD Connect을 통해 동기화 되는 온-프레미스 그룹 사용, 사용자 특성에 따라 Azure AD에서 동적 그룹 만들기, 리소스 소유자가 관리 하는 셀프 서비스 그룹 사용 또는 이러한 모든의 조합이 포함 됩니다. 각의 이점에 대 한 링크 된 리소스를 참조 하세요.

사용자에 게 응용 프로그램에 대 한 액세스 권한을 할당 하는 가장 직접적인 방법은 게시 된 응용 프로그램의 왼쪽 창에서 **사용자 및 그룹** 옵션으로 이동 하 여 그룹 또는 개인을 직접 할당 하는 것입니다.

![그림 24](media/App-proxy-deployment-plan/add-user.png)

사용자가 현재 멤버로 속해 있지 않은 그룹을 할당 하 고 셀프 서비스 옵션을 구성 하 여 사용자가 응용 프로그램에 대 한 셀프 서비스 액세스를 허용할 수도 있습니다.

![그림 25](media/App-proxy-deployment-plan/allow-access.png)

사용 하도록 설정 하면 사용자는 MyApps 포털에 로그인 하 여 액세스를 요청 하 고, 자동으로 승인 되 고 이미 허용 된 셀프 서비스 그룹에 추가 되거나, 지정 된 승인자의 승인이 필요할 수 있습니다.

또한 [AZURE AD B2B를 통해 응용 프로그램 프록시를 통해 게시 된 내부 응용 프로그램에 액세스 하도록 게스트 사용자를 초대할](https://docs.microsoft.com/azure/active-directory/b2b/add-users-information-worker)수 있습니다.

일반적으로 익명으로 액세스할 수 있고 인증을 요구 하지 않는 온-프레미스 응용 프로그램의 경우 응용 프로그램의 **속성**에 있는 옵션을 사용 하지 않도록 설정할 수 있습니다.

![그림 26](media/App-proxy-deployment-plan/assignment-required.png)


이 옵션을 아니요로 설정 하면 사용자가 권한 없이 Azure AD 앱 프록시를 통해 온-프레미스 응용 프로그램에 액세스할 수 있으므로 주의 해 서 사용 합니다.

응용 프로그램을 게시 한 후에는 브라우저에서 또는의 아이콘을 통해 외부 URL을 입력 하 여 액세스할 수 있어야 합니다 [https://myapps.microsoft.com](https://myapps.microsoft.com/) .

### <a name="enable-pre-authentication"></a>사전 인증 사용

외부 URL을 통해 응용 프로그램 프록시에 액세스 하는 응용 프로그램 프록시를 통해 응용 프로그램에 액세스할 수 있는지 확인 합니다.

1. **Azure Active Directory**  >  **엔터프라이즈 응용 프로그램**  >  **모든 응용 프로그램** 으로 이동 하 고 관리 하려는 앱을 선택 합니다.

2. **애플리케이션 프록시**를 선택합니다.

3. **사전 인증** 필드에서 드롭다운 목록을 사용 하 여 **Azure Active Directory**를 선택 하 고 **저장**을 선택 합니다.

사전 인증을 사용 하는 경우 Azure AD는 인증을 위해 먼저 사용자에 게 질문을 하 고 Single Sign-On 구성 된 경우 응용 프로그램에 대 한 액세스가 부여 되기 전에 백 엔드 응용 프로그램도 사용자를 확인 합니다. 이전 인증 모드를 통과에서 Azure AD로 변경 하면 HTTPS를 사용 하 여 외부 URL도 구성 되므로 HTTP에 대해 초기에 구성 된 모든 응용 프로그램은 이제 HTTPS를 사용 하 여 보안이 유지 됩니다.

### <a name="enable-single-sign-on"></a>Single Sign-On 사용

SSO는 사용자가 Azure AD에 액세스할 때 한 번만 로그인 하면 되기 때문에 최상의 사용자 환경 및 보안을 제공 합니다. 사용자가 미리 인증 되 면 사용자를 대신 하 여 온-프레미스 응용 프로그램을 인증 하는 응용 프로그램 프록시 커넥터에서 SSO를 수행 합니다. 백 엔드 응용 프로그램은 사용자 자체 였던 것 처럼 로그인을 처리 합니다.

**통과** 옵션을 선택 하면 사용자가 Azure AD에 인증 하지 않고도 게시 된 응용 프로그램에 액세스할 수 있습니다.

SSO를 수행 하는 것은 Azure AD가 리소스에 대 한 액세스를 요청 하는 사용자를 식별할 수 있는 경우에만 가능 합니다. 따라서 SSO가 작동 하려면 Azure AD를 사용 하 여 사용자를 사전 인증 하도록 구성 해야 합니다. 그렇지 않으면 SSO 옵션을 사용할 수 없습니다.

[AZURE AD에서 응용 프로그램에 대 한 Single sign-on을](what-is-single-sign-on.md) 읽어 응용 프로그램을 구성할 때 가장 적합 한 SSO 방법을 선택할 수 있습니다.

###  <a name="working-with-other-types-of-applications"></a>다른 유형의 응용 프로그램 작업

Azure AD 응용 프로그램 프록시는 [MSAL (Microsoft 인증 라이브러리)](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)을 사용 하도록 개발 된 응용 프로그램을 지원할 수도 있습니다. 사용자를 대신 하 여 사전 인증을 수행 하기 위해 클라이언트 요청의 헤더 정보에서 받은 Azure AD 발급 토큰을 사용 하 여 네이티브 클라이언트 앱을 지원 합니다.

응용 프로그램 프록시의 사용 가능한 구성에 대 한 자세한 내용은 [기본 및 모바일 클라이언트 앱](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-native-client) 및 [클레임 기반 응용 프로그램](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-claims-aware-apps) 게시를 참조 하세요.

### <a name="use-conditional-access-to-strengthen-security"></a>조건부 액세스를 사용 하 여 보안 강화

응용 프로그램 보안을 위해서는 온-프레미스 및 클라우드에서 복잡 한 위협 으로부터 보호 하 고이에 대응할 수 있는 고급 보안 기능 집합이 필요 합니다. 공격자는 취약 하거나, 기본 또는 도난 당한 사용자 자격 증명을 통해 회사 네트워크에 액세스 하는 경우가 가장 많습니다.  Microsoft id 기반 보안은 권한 있는 id와 권한 없는 id를 관리 하 고 보호 하 여 도난당 한 자격 증명의 사용을 줄입니다.

Azure AD 응용 프로그램 프록시을 지원 하기 위해 다음 기능을 사용할 수 있습니다.

* 사용자 및 위치 기반 조건부 액세스: [위치 기반 조건부 액세스 정책을](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-locations)사용 하 여 지리적 위치 또는 IP 주소에 따라 사용자 액세스를 제한 하 여 중요 한 데이터를 보호 합니다.

* 장치 기반 조건부 액세스: 등록, 승인 및 규격 장치만 [장치 기반 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)를 사용 하 여 회사 데이터에 액세스할 수 있도록 합니다.

* 응용 프로그램 기반 조건부 액세스: 사용자가 회사 네트워크에 있지 않은 경우 작업을 중지할 필요가 없습니다. [회사 클라우드 및 온-프레미스 앱에 안전 하 게 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-mam) 하 고 조건부 액세스를 통해 제어를 유지 합니다.

* 위험 기반 조건부 액세스: 온-프레미스 또는 클라우드에서 모든 앱 및 모든 사용자에 게 적용할 수 있는 [위험 기반 조건부 액세스 정책을](https://www.microsoft.com/cloud-platform/conditional-access) 사용 하 여 악의적인 해커 로부터 데이터를 보호 합니다.

* Azure AD 내 앱: 응용 프로그램 프록시 서비스를 배포 하 고 응용 프로그램을 안전 하 게 게시 하 여 사용자에 게 모든 응용 프로그램을 검색 하 고 액세스할 수 있는 간단한 허브를 제공 합니다. [내 앱](https://aka.ms/AccessPanelDPDownload)을 통해 새로운 앱 및 그룹에 대 한 액세스를 요청 하거나 이러한 리소스에 대 한 액세스를 관리 하는 기능과 같은 셀프 서비스 기능으로 생산성을 향상 하세요.

## <a name="manage-your-implementation"></a>구현 관리

### <a name="required-roles"></a>필요한 역할

Microsoft는 Azure AD에서 필요한 작업을 수행할 수 있는 최소한의 권한을 부여 하는 원칙을 포함 합니다. [사용 가능한 다양 한 Azure 역할을 검토](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) 하 고 올바른 항목을 선택 하 여 각 가상 사용자의 요구 사항을 해결 합니다. 일부 역할은 배포를 완료 한 후 임시로 적용 해야 할 수도 있습니다.

| 비즈니스 역할| 비즈니스 작업| Azure AD 역할 |
|---|---|---|
| 지원 센터 관리자 | 일반적으로 최종 사용자가 보고 한 문제를 검증 하 고 사용자 암호 변경, 새로 고침 토큰 무효화 및 서비스 상태 모니터링과 같은 제한 된 작업을 수행 하는 것으로 제한 됩니다. | 기술 지원팀 관리자 |
| Id 관리| Azure AD 로그인 보고서 및 감사 로그를 읽어 앱 프록시 관련 문제를 디버그 합니다.| 보안 판독기 |
| 애플리케이션 소유자| 엔터프라이즈 응용 프로그램, 응용 프로그램 등록 및 응용 프로그램 프록시 설정의 모든 측면을 만들고 관리 합니다.| 응용 프로그램 관리자 |
| 인프라 관리자 | 인증서 롤오버 소유자 | 응용 프로그램 관리자 |

보안 정보 또는 리소스에 대 한 액세스 권한이 있는 사용자 수를 최소화 하면 악의적인 행위자가 무단 액세스를 얻거나 권한 있는 사용자가 실수로 중요 한 리소스에 영향을 줄 수 있는 기회를 줄일 수 있습니다.

그러나 사용자는 여전히 일상적인 권한 있는 작업을 수행 해야 하므로, JIT (just-in-time) 기반 [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) 정책을 적용 하 여 azure 리소스에 대 한 권한 있는 액세스 권한을 제공 하 고 azure AD를 사용 하 여 관리 액세스 및 감사를 효과적으로 관리 하는 것이 좋습니다.

### <a name="reporting-and-monitoring"></a>보고 및 모니터링

Azure AD는 [감사 로그 및 보고서](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)를 통해 조직의 응용 프로그램 사용 현황 및 작업 상태에 대 한 추가 정보를 제공 합니다. 응용 프로그램 프록시를 사용 하면 Azure AD 포털 및 Windows 이벤트 로그에서 커넥터를 매우 쉽게 모니터링할 수 있습니다.

#### <a name="application-audit-logs"></a>애플리케이션 감사 로그

이러한 로그는 응용 프로그램 프록시 및 장치와 응용 프로그램에 액세스 하는 사용자로 구성 된 응용 프로그램에 로그인 하는 방법에 대 한 자세한 정보를 제공 합니다. [감사 로그](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 는 Azure Portal 및 내보내기에 대 한 [감사 API](https://docs.microsoft.com/graph/api/resources/directoryaudit?view=graph-rest-beta) 에 있습니다. 또한 응용 프로그램에 대 한 [사용량 및 통찰력 보고서](../reports-monitoring/concept-usage-insights-report.md?context=azure/active-directory/manage-apps/context/manage-apps-context) 도 사용할 수 있습니다.

#### <a name="application-proxy-connector-monitoring"></a>응용 프로그램 프록시 커넥터 모니터링

커넥터와 서비스는 모든 고가용성 작업을 처리합니다. Azure AD 포털의 응용 프로그램 프록시 페이지에서 커넥터의 상태를 모니터링할 수 있습니다. 커넥터 유지/보수에 대 한 자세한 내용은 [Azure AD 응용 프로그램 프록시 커넥터 이해](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#maintenance)를 참조 하세요.

![예: Azure AD 응용 프로그램 프록시 커넥터](./media/application-proxy-connectors/app-proxy-connectors.png)

#### <a name="windows-event-logs-and-performance-counters"></a>Windows 이벤트 로그 및 성능 카운터

커넥터에는 관리자와 세션 로그가 모두 있습니다. 관리 로그에는 주요 이벤트와 해당 오류가 포함됩니다. 세션 로그에는 모든 트랜잭션 및 처리 세부 정보가 포함됩니다. 로그 및 카운터는 Windows 이벤트 로그에 있습니다. 자세한 내용은 [Azure AD 응용 프로그램 프록시 커넥터 이해](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-connectors#under-the-hood)를 참조 하세요. 이 [자습서에 따라 Azure Monitor에서 이벤트 로그 데이터 원본을 구성할 수](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events)있습니다.

### <a name="troubleshooting-guide-and-steps"></a>문제 해결 가이드 및 단계

일반적인 문제 및 오류 메시지 [문제 해결](application-proxy-troubleshoot.md) 가이드를 사용 하 여 해결 하는 방법에 대해 자세히 알아보세요.

다음 문서에서는 지원 조직에 대 한 문제 해결 가이드를 만드는 데 사용할 수 있는 일반적인 시나리오를 다룹니다.

* [앱 페이지를 표시할 때 문제](application-proxy-page-appearance-broken-problem.md)
* [너무 긴 애플리케이션 로드](application-proxy-page-load-speed-problem.md)
* [애플리케이션 페이지에서 링크가 작동하지 않음](application-proxy-page-links-broken-problem.md)
* [내 앱에 대해 열 포트](application-proxy-add-on-premises-application.md)
* [내 앱에 대한 커넥터 그룹에서 작동하지 않는 커넥터](application-proxy-connectivity-no-working-connector.md)
* [관리자 포털에서 구성](application-proxy-config-how-to.md)
* [내 앱에 Single Sign-On 구성](application-proxy-config-sso-how-to.md)
* [관리 포털에서 앱을 만드는 문제](application-proxy-config-problem.md)
* [Kerberos 제한된 위임 구성](application-proxy-back-end-kerberos-constrained-delegation-how-to.md)
* [PingAccess를 사용하여 구성](application-proxy-back-end-ping-access-how-to.md)
* [회사 애플리케이션에 액세스할 수 없음 오류](application-proxy-sign-in-bad-gateway-timeout-error.md)
* [애플리케이션 프록시 에이전트 커넥터를 설치할 때 문제 발생](application-proxy-connector-installation-problem.md)
* [로그인 문제](application-sign-in-problem-on-premises-application-proxy.md)
