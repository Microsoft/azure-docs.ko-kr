---
title: Azure의 보안 기술 기능-Microsoft Azure
description: 클라우드에서 데이터, 리소스 및 응용 프로그램을 보호 하는 데 도움이 되는 Azure의 보안 서비스를 소개 합니다.
services: security
author: TerryLanfear
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.date: 02/04/2021
ms.author: terrylan
ms.openlocfilehash: 54be25317717bcf98b9e296d5c9ed59643cd334e
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044756"
---
# <a name="azure-security-technical-capabilities"></a>Azure 보안 기술 기능
이 문서에서는 클라우드에서 데이터, 리소스 및 응용 프로그램을 보호 하 고 비즈니스의 보안 요구를 충족 하는 데 도움이 되는 Azure의 보안 서비스에 대해 소개 합니다.

## <a name="azure-platform"></a>Azure 플랫폼

[Microsoft Azure](https://azure.microsoft.com/overview/what-is-azure/)는 데이터 서비스 및 고급 분석, 개발자 도구 및 서비스가 통합되고 Microsoft의 퍼블릭 클라우드 데이터 센터 내에 호스트된 인프라 및 애플리케이션 서비스로 구성된 클라우드 플랫폼입니다. 고객은 기본 계산, 네트워킹, 저장소, 모바일 및 웹 앱 서비스, 사물 인터넷 같은 전체 클라우드 시나리오에 이르기까지 다양 한 용량 및 시나리오에 Azure를 사용 하 고, 오픈 소스 기술과 함께 사용 하 고, 하이브리드 클라우드로 배포 하거나, 고객의 데이터 센터 내에서 호스트 될 수 있습니다. Azure는 기업이 비용을 절감하고, 더 빠르게 혁신을 이루고, 사전 대응적으로 시스템을 관리하는 데 도움이 되는 기본 구성 요소로서 클라우드 기술을 제공합니다. IT 자산을 만들거나 클라우드 공급자로 마이그레이션하는 경우 조직에서 제공하는 서비스와 제어를 통해 애플리케이션과 데이터를 보호할 수 있는 해당 조직의 능력에 의존하여 클라우드 기반 자산의 보안을 관리합니다.

Microsoft Azure는 팀이 Microsoft 플랫폼 및 타사 플랫폼의 데이터, 오픈 프레임워크 및 도구를 통해 통찰력을 얻도록 하는 통합 데이터 서비스 및 분석 기능을 사용하여 다양한 클라우드 기술 집합 및 다양한 복잡성의 프로젝트 내에서 작업할 수 있도록 하는 안전하고 일관된 애플리케이션 플랫폼 및 Infrastructure-as-a-Service를 제공하는 유일한 클라우드 컴퓨팅 공급자로, 온-프레미스와 클라우드를 통합하거나 온-프레미스 데이터 센터 내에서 Azure Cloud Services를 배포할 수 있습니다. Microsoft 신뢰 클라우드의 일부로, 고객은 Azure에서 업계 최고의 보안, 안정성, 규정 준수, 개인 정보 보호와 클라우드에서 조직을 지원하기 위한 방대한 사용자, 파트너 및 프로세스 네트워크를 획득할 수 있습니다.

Microsoft Azure를 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- 클라우드를 통해 혁신을 가속화합니다.

- 통찰력으로 비즈니스 의사 결정 및 앱을 강화합니다.

- 어디서든 자유롭게 빌드하고 배포합니다.

- 비즈니스를 보호합니다.

## <a name="security-technical-capabilities-to-fulfill-your-responsibility"></a>책임을 충족 하는 보안 기술 기능

Microsoft Azure는 보안, 개인 정보 보호 및 규정 준수 요구 사항을 충족 하는 데 도움이 되는 서비스를 제공 합니다. 다음 그림은 업계 표준을 기반으로 하는 보안 및 규정 준수 응용 프로그램 인프라를 구축 하는 데 사용할 수 있는 다양 한 Azure 서비스를 설명 합니다.

![사용 가능한 보안 기술 기능 - 전반적 이해](./media/technical-capabilities/azure-security-technical-capabilities-fig1.png)

## <a name="manage-and-control-identity-and-user-access"></a>Id 및 사용자 액세스 관리 및 제어

Azure에서는 사용자 ID 및 자격 증명을 관리하고 액세스를 제어할 수 있도록 하여 회사 및 개인 정보를 보호하는 데 도움을 줍니다.

### <a name="azure-active-directory"></a>Azure Active Directory

Microsoft id 및 액세스 관리 솔루션을 사용 하면 회사 데이터 센터와 클라우드 간에 응용 프로그램 및 리소스에 대 한 액세스를 보호 하 여 multi-factor authentication 및 조건부 액세스 정책과 같은 추가 수준의 유효성 검사를 수행할 수 있습니다. 고급 보안 보고, 감사 및 경고를 통해 의심스러운 작업을 모니터링하여 잠재적인 보안 문제를 완화시킵니다. [Azure Active Directory Premium](../../active-directory/fundamentals/active-directory-whatis.md) 은 수천 개의 클라우드 앱에 Single Sign-On를 제공 하 고 온-프레미스에서 실행 하는 웹 앱에 대 한 액세스를 제공 합니다.

Azure Active Directory (Azure AD)의 보안 이점은 다음과 같은 기능을 포함합니다.

- 하이브리드 엔터프라이즈에서 사용자, 그룹 및 디바이스의 동기화가 유지되도록 각 사용자에 대해 단일 ID를 만들고 관리합니다.

- 수천 개의 미리 통합된 SaaS 앱을 포함한 애플리케이션에 대한 Single Sign-On 액세스 제공

- 온-프레미스 및 클라우드 애플리케이션 모두에 대해 규칙 기반 Multi-Factor Authentication을 적용하여 애플리케이션 액세스 보안을 사용하도록 설정

- Azure AD 애플리케이션 프록시를 통해 온-프레미스 웹 애플리케이션에 대한 보안 원격 액세스 프로비전

[Azure Active Directory 포털](https://aad.portal.azure.com/) 은 Azure Portal의 일부로 사용할 수 있습니다. 이 대시보드에서는 조직의 상태 개요를 파악 하 고 디렉터리, 사용자 또는 응용 프로그램 액세스를 쉽게 관리할 수 있습니다.

![Azure Active Directory](./media/technical-capabilities/azure-security-technical-capabilities-fig2.png)

다음은 핵심적인 Azure id 관리 기능입니다.

- Single Sign-On

- Multi-Factor Authentication

- 보안 모니터링, 경고 및 기계 학습 기반 보고서

- 소비자 ID 및 액세스 관리

- 디바이스 등록

- Privileged Identity Management

- ID 보호

#### <a name="single-sign-on"></a>Single Sign-On

[Sso (single sign-on)](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) 는 단일 사용자 계정을 사용 하 여 한 번만 로그인 함으로써 비즈니스를 수행 하는 데 필요한 모든 응용 프로그램 및 리소스에 액세스할 수 있음을 의미 합니다. 로그인하면 다시 인증(예: 암호 입력)을 수행하지 않아도 필요한 모든 애플리케이션에 액세스할 수 있습니다.

많은 조직에서는 최종 사용자 생산성을 위해 Microsoft 365, Box 및 Salesforce와 같은 SaaS (software as a service) 응용 프로그램에 의존 합니다. 지금까지 IT 담당자는 각 SaaS 애플리케이션에서 사용자 계정을 개별적으로 만들고 업데이트해야 하며, 사용자는 각 SaaS 애플리케이션에 대한 암호를 기억해야 했습니다.

[AZURE AD는 온-프레미스 Active Directory을 클라우드로 확장](../../active-directory/manage-apps/what-is-single-sign-on.md)하 여 사용자가 자신의 기본 조직 계정을 사용 하 여 도메인에 가입 된 장치 및 회사 리소스 뿐만 아니라 작업에 필요한 모든 웹 및 SaaS 응용 프로그램에 로그인 할 수 있도록 합니다.

사용자는 여러 사용자 이름과 암호 집합을 관리할 필요가 없을 뿐만 아니라, 조직 그룹 및 직원으로서의 상태에 따라 해당 애플리케이션 액세스를 자동으로 프로비전하거나 프로비전을 해제할 수 있습니다. Azure AD는 SaaS 응용 프로그램에서 사용자의 액세스를 중앙에서 관리할 수 있는 [보안 및 액세스 관리 제어를 도입](../../active-directory/manage-apps/view-applications-portal.md) 합니다.

#### <a name="multi-factor-authentication"></a>Multi-Factor Authentication

[MFA (AZURE AD Multi-Factor Authentication)](../../active-directory/authentication/concept-mfa-howitworks.md) 는 둘 이상의 인증 방법을 사용 해야 하 고 사용자 로그인 및 트랜잭션에 중요 한 두 번째 보안 계층을 추가 하는 인증 방법입니다. MFA는 간단한 로그인 프로세스에 대 한 사용자의 요구를 충족 하면서 데이터와 응용 프로그램에 대 한 액세스를 보호 하는 [데 도움이 됩니다](../../active-directory/authentication/concept-mfa-howitworks.md) . 전화 통화, 문자 메시지 또는 모바일 앱 알림 또는 확인 코드 및 타사 OAuth 토큰과 같은 다양한 확인 옵션을 통해 강력한 인증을 전달합니다.

#### <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>보안 모니터링, 경고 및 기계 학습 기반 보고서

일치하지 않는 액세스 패턴을 식별하는 보안 모니터링 및 경고 및 기계 학습 기반 보고서로 비즈니스를 보호할 수 있습니다. Azure Active Directory의 액세스 및 사용 보고서를 사용하여 조직 디렉터리의 무결성 및 보안을 볼 수 있습니다 이 정보를 사용하면 디렉터리 관리자는 가능한 보안 위험이 발생할 수 있는 위치를 보다 잘 결정하여 이러한 위험을 적절하게 완화할 수 있습니다.

Azure Portal 또는 [Azure Active Directory 포털](https://aad.portal.azure.com/)에서 [보고서](../../active-directory/reports-monitoring/overview-reports.md)는 다음과 같은 방식으로 분류되어 있습니다.

- 비정상 보고서 – 비정상으로 확인된 로그인 이벤트가 포함됩니다. 이러한 활동을 인식하고 이벤트가 의심스러운지 확인할 수 있게 해 줍니다.

- 통합 응용 프로그램 보고서 – 클라우드 응용 프로그램이 조직에서 사용 되는 방식에 대 한 통찰력을 제공 합니다. Azure Active Directory는 수천 개의 클라우드 애플리케이션과 통합을 제공합니다.

- 오류 보고서 – 외부 애플리케이션에 계정을 프로비전할 때 발생할 수 있는 오류를 나타냅니다.

- 사용자별 보고서 – 특정 사용자에 대 한 장치 및 로그인 활동 데이터를 표시 합니다.

- 활동 로그 - 최근 24시간, 최근 7일 또는 최근 30일 이내에 감사된 모든 이벤트의 레코드와 그룹 활동 변경 사항, 암호 재설정 및 등록 활동이 포함됩니다.

#### <a name="consumer-identity-and-access-management"></a>소비자 ID 및 액세스 관리

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)는 몇 억 개의 ID로 확장하는 소비자 지향 애플리케이션에 항상 사용 가능한 전역적인 ID 관리 서비스입니다. 이 서비스는 모바일 및 웹 플랫폼에 통합될 수 있습니다. 고객은 사용자 지정 가능한 환경을 통해 기존 소셜 계정을 사용하거나 새 자격 증명을 만들어 모든 애플리케이션에 로그온할 수 있습니다.

이전에는 응용 프로그램에 [소비자를 등록 하 고 로그인](../../active-directory-b2c/overview.md) 하려는 응용 프로그램 개발자가 자신의 코드를 작성 했습니다. 또한 온-프레미스 데이터베이스나 시스템을 사용하여 사용자 이름과 암호를 저장했습니다. Azure Active Directory B2C는 조직에게 안전한 표준 기반 플랫폼 및 확장할 수 있는 정책의 다양한 집합으로 소비자 ID 관리를 애플리케이션에 통합하는 더 나은 방법을 제공합니다.

Azure Active Directory B2C를 사용하면 소비자는 기존 소셜 계정(Facebook, Google, Amazon, LinkedIn)을 사용하거나 새 자격 증명(전자 메일 주소 및 암호 또는 사용자 이름 및 암호)을 만들어서 애플리케이션을 등록할 수 있습니다.

#### <a name="device-registration"></a>디바이스 등록

[AZURE AD device registration](../../active-directory/devices/overview.md) 은 장치 기반 [조건부 액세스](../../active-directory/devices/overview.md) 시나리오의 기초입니다. 디바이스가 등록되면 Azure AD 디바이스 등록은 사용자가 로그인할 때 디바이스를 인증하는 데 사용되는 ID와 함께 디바이스를 제공합니다. 그런 다음 인증 된 장치 및 장치 특성을 사용 하 여 클라우드 및 온-프레미스에서 호스트 되는 응용 프로그램에 대 한 조건부 액세스 정책을 적용할 수 있습니다.

Intune과 같은 [MDM (모바일 장치 관리)](https://www.microsoft.com/itshowcase/Article/Content/588/Mobile-device-management-at-Microsoft) 솔루션과 함께 사용 하는 경우 Azure Active Directory의 장치 특성이 장치에 대 한 추가 정보로 업데이트 됩니다. 이렇게 하면 보안 및 규정 준수에 대 한 표준을 충족 하기 위해 장치에서 액세스를 적용 하는 조건부 액세스 규칙을 만들 수 있습니다.

#### <a name="privileged-identity-management"></a>Privileged Identity Management

[Ad (Azure Active Directory) Privileged Identity Management](../../active-directory/privileged-identity-management/pim-configure.md) 를 사용 하면 권한 있는 id를 관리, 제어 및 모니터링 하 고 Azure AD의 리소스 뿐만 아니라 Microsoft 365 또는 Microsoft Intune 같은 기타 Microsoft 온라인 서비스에 액세스할 수 있습니다.

사용자가 Azure 또는 Microsoft 365 리소스 또는 기타 SaaS 앱에서 권한 있는 작업을 수행 해야 하는 경우가 있습니다. 이는 보통 조직이 사용자에게 Azure AD에서 영구 권한 있는 액세스를 제공해야 함을 의미합니다. 조직은 사용자가 관리자 권한으로 수행하는 작업을 충분히 모니터링할 수 없으므로 클라우드에 호스트된 리소스의 보안 위험이 증가합니다. 또한 권한 있는 액세스가 있는 사용자 계정이 손상되면 이로 인해 전반적인 클라우드 보안에 영향을 줄 수 있습니다. Azure AD 권한 있는 ID 관리는 이 위험을 해결하는 데 도움이 됩니다.

Azure AD Privileged Identity Management를 통해 다음을 할 수 있습니다.

- Azure AD 관리자인 사용자를 확인할 수 있습니다.

- Microsoft 365 및 Intune과 같은 Microsoft Online Services에 대 한 주문형 "just-in-time" 관리 액세스를 사용 하도록 설정

- 관리자 액세스 기록 및 관리자 할당 변경에 대한 보고서 가져오기

- 권한 있는 역할의 액세스에 대한 알림을 받을 수 있습니다.

#### <a name="identity-protection"></a>ID 보호

[Azure AD ID 보호](../../active-directory/identity-protection/overview-identity-protection.md) 은 조직의 id에 영향을 주는 위험 검색 및 잠재적 취약성에 대 한 통합 보기를 제공 하는 보안 서비스입니다. Id 보호는 기존 Azure Active Directory의 변칙 감지 기능 (Azure AD의 비정상적인 작업 보고서를 통해 사용 가능)을 사용 하 고 실시간으로 변칙을 검색할 수 있는 새로운 위험 검색 유형을 도입 합니다.

## <a name="secure-resource-access"></a>보안 리소스 액세스

Azure의 액세스 제어는 결제 관점에서 시작합니다. [Azure 계정 센터](https://account.windowsazure.com/subscriptions)를 방문하여 액세스하는 Azure 계정의 소유자는 계정 관리자(AA)입니다. 구독은 결제를 위한 컨테이너이지만 보안 경계로서의 역할도 합니다. 각 구독에는 Azure Portal을 사용하여 해당 구독에서 Azure 리소스를 추가, 제거 및 수정할 수 있는 SA(서비스 관리자)가 있습니다. 새 구독의 기본 SA는 AA이지만 AA가 Azure 계정 센터에서 SA를 변경할 수 있습니다.

![Azure의 보안 리소스 액세스](./media/technical-capabilities/azure-security-technical-capabilities-fig3.png)

또한 구독은 디렉터리와 연결되어 있습니다. 디렉터리는 사용자 집합을 정의합니다. 사용자 집합은 디렉터리를 만든 회사 또는 학교 사용자이거나 외부 사용자(즉, Microsoft 계정)일 수 있습니다. 구독은 서비스 관리자(SA) 또는 CA(공동 관리자)로 할당된 해당 디렉터리 사용자의 하위 집합에서 액세스할 수 있습니다. 유일한 예외는 레거시의 이유로 Microsoft 계정(이전의 Windows Live ID)이 디렉터리에 없는 SA 또는 CA로 할당될 수 있다는 점입니다.

보안 지향적 회사는 직원에게 정확히 필요한 권한을 제공하는 데 중점을 두어야 합니다. 권한이 너무 많으면 공격자에게 계정이 노출될 수 있고, 권한이 너무 적으면 직원이 업무를 효율적으로 수행할 수 없습니다. Azure [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/overview.md) 는 azure에 대 한 세분화 된 액세스 관리를 제공 하 여이 문제를 해결 하는 데 도움이 됩니다.

![보안 리소스 액세스](./media/technical-capabilities/azure-security-technical-capabilities-fig4.png)

Azure RBAC를 사용하면 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다. Azure 구독 또는 리소스에서 모든 사람에게 무제한 권한을 제공하는 대신 특정 작업만 허용할 수 있습니다. 예를 들어 Azure RBAC를 사용 하 여 한 직원이 구독의 가상 컴퓨터를 관리 하 고 다른 직원이 동일한 구독 내에서 SQL 데이터베이스를 관리할 수 있게 합니다.

![Azure RBAC를 사용 하 여 보안 리소스 액세스](./media/technical-capabilities/azure-security-technical-capabilities-fig5.png)

## <a name="data-security-and-encryption"></a>데이터 보안 및 암호화

클라우드의 데이터를 보호하기 위한 핵심 중 하나는 데이터에서 발생 가능한 상태 그리고 해당 상태에 어떤 컨트롤을 제공할 것인지 설명하는 것입니다. Azure 데이터 보안 및 암호화 모범 사례의 경우 다음과 같은 데이터 상태와 관련된 권장 사항이 제공됩니다.

- 스토리지: 모든 정보 스토리지 개체, 컨테이너 및 물리적 미디어(자기 또는 광 디스크)에 정적으로 존재하는 유형이 여기에 포함됩니다.
- 전송 중: 데이터를 구성 요소, 위치 또는 프로그램 간에 전송 하는 경우 (예: Express 경로와 같은 하이브리드 연결을 포함 하 여 온-프레미스에서 클라우드로 또는 그 반대의 경우) 또는 입/출력 프로세스 중에는 이동 중 이라고 생각 합니다.

### <a name="encryption-at-rest"></a>미사용 암호화

휴지 상태의 암호화에 대해서는 [Azure 저장소 데이터 암호화](encryption-atrest.md)에 자세히 설명 되어 있습니다.

### <a name="encryption-in-transit"></a>전송 중인 암호화

전송 중인 데이터 보호는 데이터 보호 전략에서 절대 빠질 수 없는 핵심입니다. 데이터는 여러 위치 사이를 이동하므로 항상 SSL/TLS 프로토콜을 사용하여 여러 위치 간에 데이터를 교환하는 것이 일반적인 권장 사항입니다. VPN(가상 사설망)을 사용하여 온-프레미스와 클라우드 인프라 간에 전체 통신 채널을 격리하려는 경우가 있습니다.

온-프레미스 인프라와 Azure 사이를 이동하는 데이터의 경우 HTTPS 또는 VPN처럼 적절한 안전 장치를 고려해야 합니다.

온-프레미스에 있는 여러 워크스테이션에서 Azure로의 액세스를 보호해야 하는 조직의 경우 [Azure 사이트 간 VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md)을 사용합니다.

온-프레미스에 있는 한 워크스테이션에서 Azure로의 액세스를 보호해야 하는 조직의 경우 [지점 및 사이트 간 VPN](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md)을 사용합니다.

대용량 데이터 집합은 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 같은 전용 고속 WAN 링크를 통해 이동할 수 있습니다. ExpressRoute를 사용하기로 선택하는 경우 [SSL/TLS](https://web.archive.org/web/20150221085231/http://support.microsoft.com:80/kb/257591) 또는 기타 프로토콜을 사용하여 애플리케이션 수준에서 데이터를 암호화하면 보안 수준을 더욱 높일 수 있습니다.

Azure Portal를 통해 Azure Storage와 상호 작용 하는 경우 모든 트랜잭션이 HTTPS를 통해 발생 합니다. 또한 HTTPS를 통해 [스토리지 REST API](/rest/api/storageservices/)를 사용하여 [Azure Storage](https://azure.microsoft.com/services/storage/) 및 [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)와 상호 작용할 수 있습니다.

전송 중인 데이터를 보호 하지 못하는 조직은 메시지 가로채기 ( [man-in-the-middle) 공격](/previous-versions/office/skype-server-2010/gg195821(v=ocs.14)), [도청](/previous-versions/office/skype-server-2010/gg195641(v=ocs.14))및 세션 하이재킹에 더 취약 합니다. 이러한 공격은 기밀 데이터에 액세스하기 위한 첫 번째 단계일 수 있습니다.

Azure VPN 옵션에 대한 자세한 내용은 [VPN Gateway 계획 및 설계](../../vpn-gateway/vpn-gateway-about-vpngateways.md) 문서를 참조하세요.

### <a name="enforce-file-level-data-encryption"></a>파일 수준 데이터 암호화 적용

[Azure RMS](/azure/information-protection/what-is-azure-rms)는 암호화, ID 및 권한 부여 정책을 사용하여 파일 및 전자 메일을 보호합니다. Azure RMS는 조직 내와 조직 외부 모두에서 보호 하 여 휴대폰, 태블릿, Pc 등의 여러 장치에서 작동 합니다. Azure RMS는 데이터가 조직의 경계를 벗어나는 경우에도 데이터를 유지하는 보호 수준을 추가하기 때문에 이 기능이 가능한 것입니다.

Azure RMS를 사용하여 파일을 보호하면 [FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf)를 완벽하게 지원하는 업계 표준 암호화를 사용하는 것입니다. 데이터 보호를 위해 Azure RMS를 사용 하는 경우 클라우드 저장소 서비스와 같이 IT의 제어를 받지 않는 저장소로 복사 하는 경우에도 보호 기능이 파일에 유지 된다는 보장이 있습니다. 전자 메일을 통해 공유되는 파일도 마찬가지입니다. 파일이 전자 메일 메시지의 첨부 파일로 보호되며 보호되는 첨부 파일을 여는 방법에 대한 지침이 함께 제공됩니다.
Azure RMS 도입 계획을 세울 때 다음 사항을 권장합니다.

- [RMS 공유 앱](/azure/information-protection/rms-client/sharing-app-windows) 설치. 이 앱은 사용자가 간편하게 파일을 직접 보호할 수 있도록 Office 추가 기능을 설치하여 Office 애플리케이션과 통합됩니다.

- Azure RMS를 지원하도록 애플리케이션 및 서비스 구성

- 비즈니스 요구 사항을 반영하는 [사용자 지정 템플릿](/azure/information-protection/configure-policy-templates) 만들기. 예: 모든 극비 관련 전자 메일에 적용해야 하는 극비 데이터 템플릿.

[데이터 분류](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) 및 파일 보호에 약한 조직은 데이터 유출에 좀 더 취약할 수 있습니다. 적절한 파일 보호 수단이 없는 조직은 비즈니스 통찰력을 얻고, 데이터 남용을 모니터링하고, 악의적인 파일 액세스를 방지할 수 없습니다.

> [!Note]
> Azure RMS에 대한 자세한 내용은 [Azure Rights Management 시작](/azure/information-protection/requirements) 문서를 참조하세요.

## <a name="secure-your-application"></a>애플리케이션 보안
Azure가 애플리케이션이 실행되는 인프라 및 플랫폼의 보안을 담당하는 반면 애플리케이션 자체를 보호하는 것은 사용자 책임입니다. 즉, 애플리케이션 코드 및 콘텐츠를 안전한 방식으로 개발, 배포 및 관리해야 합니다. 이렇게 하지 않으면 애플리케이션 코드 또는 콘텐츠는 위협에 취약할 수 있습니다.

### <a name="web-application-firewall"></a>웹 애플리케이션 방화벽
[WAF (웹 응용 프로그램 방화벽)](../../web-application-firewall/ag/ag-overview.md) 는 일반적인 악용 및 취약점 으로부터 웹 응용 프로그램에 대 한 중앙 집중식 보호를 제공 하는 [Application Gateway](../../application-gateway/overview.md) 기능입니다.

웹 애플리케이션 방화벽은 [OWASP 핵심 규칙 집합](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 또는 2.2.9의 규칙에 기반합니다. 웹 애플리케이션의 널리 알려진 취약점을 악용하는 악의적인 공격이 점점 많아지고 있습니다. 이러한 공격으로는 SQL 삽입 공격, 사이트 간 스크립팅 공격 등이 있습니다. 애플리케이션 코드로 이러한 공격을 방어하기란 매우 어려울 수 있으며 애플리케이션 토폴로지의 여러 계층에서 엄격한 유지 관리, 패치 적용 및 모니터링이 필요할 수 있습니다. 중앙 집중식 웹 애플리케이션 방화벽을 통해 보안 관리가 훨씬 간단해지고 애플리케이션 관리자에게 위협 또는 침입으로부터 효과적인 보호를 제공합니다. 또한 WAF 솔루션은 각각의 웹 애플리케이션을 보호하는 대신 중앙의 위치에서 알려진 취약점에 패치를 적용하여 보다 신속하게 보안 위협에 대응할 수 있습니다. 기존 Application Gateway는 웹 애플리케이션 방화벽을 사용한 Application Gateway로 쉽게 변환될 수 있습니다.

웹 애플리케이션 방화벽이 보호하는 일반적인 웹 취약점에는 다음이 포함됩니다.

- SQL 삽입 공격 보호

- 교차 사이트 스크립팅 공격 보호

- 명령 삽입, HTTP 요청 밀반입, HTTP 응답 분할, 원격 파일 포함 공격 등의 일반 웹 공격 보호

- HTTP 프로토콜 위반 보호

- 누락된 호스트 사용자-에이전트 및 수락 헤더 같은 HTTP 프로토콜 이상 보호

- 보트, 크롤러 및 스캐너 방지

- 일반적인 애플리케이션 구성 오류(즉 Apache, IIS 등) 검색

> [!Note]
> 더 자세한 규칙 목록 및 해당 보호는 다음 [핵심 규칙 집합](../../web-application-firewall/ag/ag-overview.md)을 참조 하세요.

또한 Azure에서는 앱에 대한 안전한 인바운드 및 아웃바운드 트래픽에 도움이 되는 사용하기 쉬운 몇 가지 기능도 제공합니다. 그뿐 아니라 Azure에서는 웹 애플리케이션에서 취약점을 검색하는 기능을 외부적으로 제공함으로써 애플리케이션 코드의 보안에 도움을 줍니다.

- [앱에 대한 Azure Active Directory 인증 설정](https://azure.microsoft.com/blog/azure-websites-authentication-authorization/)

- [전송 계층 보안(TLS/SSL) - HTTPS를 사용하여 앱에 대한 트래픽 보호](../../app-service/configure-ssl-bindings.md)

  - [HTTPS 연결을 통해 들어오는 모든 트래픽 강제 지정](http://microsoftazurewebsitescheatsheet.info/)

  - [엄격한 전송 보안(HSTS)을 사용하도록 설정](http://microsoftazurewebsitescheatsheet.info/#enable-http-strict-transport-security-hsts)

- [클라이언트의 IP 주소로 앱에 대한 액세스 제한](http://microsoftazurewebsitescheatsheet.info/#filtering-traffic-by-ip)

- [클라이언트의 동작 - 요청 빈도 및 동시성으로 앱에 대한 액세스 제한](http://microsoftazurewebsitescheatsheet.info/#dynamic-ip-restrictions)

- [Tinfoil Security 검사를 사용하여 취약성에 대한 웹앱 코드 검사](https://azure.microsoft.com/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/)

- [웹앱에 연결하는 클라이언트 인증서를 요구하도록 TLS 상호 인증 구성](../../app-service/app-service-web-configure-tls-mutual-auth.md)

- [외부 리소스에 안전하게 연결하도록 앱의 사용에 대한 클라이언트 인증서 구성](https://azure.microsoft.com/blog/using-certificates-in-azure-websites-applications/)

- [도구의 앱 지문을 방지하도록 표준 서버 헤더 제거](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

- [지점 및 사이트 간 VPN을 사용하여 프라이빗 네트워크의 리소스로 안전하게 앱 연결](../../app-service/web-sites-integrate-with-vnet.md)

- [하이브리드 연결을 사용하여 프라이빗 네트워크의 리소스로 안전하게 앱 연결](../../app-service/app-service-hybrid-connections.md)

Azure App Service는 Azure Cloud Services 및 Virtual Machines에서 사용하는 것과 동일한 맬웨어 방지 솔루션을 사용합니다. 이에 대 한 자세한 내용은 [맬웨어 방지 설명서](antimalware.md)를 참조 하세요.

## <a name="secure-your-network"></a>네트워크 보안
Microsoft Azure에는 사용자의 애플리케이션과 서비스 연결 요구 사항을 지원하기 위한 강력한 네트워킹 인프라가 포함되어 있습니다. 네트워크 연결은 Azure에 위치한 리소스 간, 온-프레미스와 Azure 호스팅 리소스 간, 그리고 인터넷과 Azure 간에 가능합니다.

[Azure 네트워크 인프라](/previous-versions/azure/virtual-machines/windows/infrastructure-example) 를 사용 하면 [vnet (가상 네트워크)](../../virtual-network/virtual-networks-overview.md)를 사용 하 여 azure 리소스를 서로 안전 하 게 연결할 수 있습니다. VNet은 클라우드에 있는 사용자의 네트워크를 나타내며, 구독 전용 Azure 클라우드 네트워크를 논리적으로 격리한 것이 VNet입니다. 또한 온-프레미스 네트워크에 VNet을 연결할 수 있습니다.

![네트워크 보안(보호)](./media/technical-capabilities/azure-security-technical-capabilities-fig6.png)

기본 네트워크 수준 액세스 제어 (IP 주소 및 TCP 또는 UDP 프로토콜 기반)가 필요한 경우 [네트워크 보안 그룹](../../virtual-network/virtual-network-vnet-plan-design-arm.md)을 사용할 수 있습니다. NSG(네트워크 보안 그룹)은 기본적인 상태 저장 패킷 필터링 방화벽이며, [5개 튜플](https://www.techopedia.com/definition/28190/5-tuple)에 기반하여 액세스를 제어할 수 있게 합니다.

Azure 네트워킹은 Azure Virtual Network의 네트워크 트래픽에 대한 라우팅 동작을 사용자 지정할 수 있는 기능을 지원하여 Azure에서 [사용자 정의 경로](../../virtual-network/virtual-networks-udr-overview.md) 를 구성 하 여이 작업을 수행할 수 있습니다.

[강제 터널링](https://www.petri.com/azure-forced-tunneling) 은 서비스에서 인터넷의 장치에 대 한 연결을 시작할 수 없도록 하는 데 사용할 수 있는 메커니즘입니다.

Azure는 온-프레미스 네트워크에 대한 전용 WAN 링크 연결과 [ExpressRoute](../../expressroute/expressroute-introduction.md)를 통한 Azure Virtual Network를 지원합니다. Azure와 사이트 간의 링크는 공용 인터넷을 사용하지 않는 전용 연결을 사용합니다. Azure 응용 프로그램이 여러 데이터 센터에서 실행 되는 경우 [azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) 를 사용 하 여 사용자의 요청을 응용 프로그램의 여러 인스턴스에서 지능적으로 라우팅할 수 있습니다. 또한 인터넷에서 액세스할 수 있는 경우 Azure에서 실행되지 않는 서비스로 트래픽을 라우팅할 수 있습니다.

Azure는 azure [개인 링크](../../private-link/private-link-overview.md)를 사용 하 여 azure Virtual Network에서 PaaS 리소스 (예: Azure Storage 및 SQL Database)에 대 한 개인 및 안전한 연결도 지원 합니다. PaaS 리소스는 가상 네트워크의 [개인 끝점](../../private-link/private-endpoint-overview.md) 에 매핑됩니다. 가상 네트워크와 PaaS 리소스의 개인 끝점 간 링크는 Microsoft 백본 네트워크를 사용 하며 공용 인터넷을 통해 이동 하지 않습니다. 서비스를 공용 인터넷에 더 이상 노출할 필요가 없습니다. 또한 Azure 개인 링크를 사용 하 여 가상 네트워크에서 Azure에서 호스트 되는 고객 소유 및 파트너 서비스에 액세스할 수 있습니다.  또한 Azure 개인 링크를 사용 하면 가상 네트워크에서 [개인 링크 서비스](../../private-link/private-link-service-overview.md) 를 만들어 가상 네트워크에서 개인적으로 고객에 게 제공할 수 있습니다. Azure Private Link를 사용한 설치 및 소비는 Azure PaaS, 고객 소유 및 공유 파트너 서비스에서 일관적입니다.

## <a name="virtual-machine-security"></a>가상 머신 보안

[Azure Virtual Machines](../../virtual-machines/index.yml)를 사용하면 다양한 컴퓨팅 솔루션을 민첩하게 배포할 수 있습니다. Microsoft Windows, Linux, Microsoft SQL Server, Oracle, IBM, SAP 및 Azure BizTalk Services 지원을 통해 거의 모든 운영 체제에 모든 작업과 언어를 배포할 수 있습니다.

Azure를 통해 가상 머신을 악성 파일, 애드웨어 및 기타 위협으로부터 보호할 수 있도록 Microsoft, Symantec, Trend Micro 및 Kaspersky 등의 보안 공급업체의 [맬웨어 방지 소프트웨어](antimalware.md)를 사용할 수 있습니다.

Azure Cloud Services 및 Virtual Machines를 위한 Microsoft 맬웨어 방지 프로그램은 바이러스, 스파이웨어 및 기타 악성 소프트웨어를 식별 및 제거하는 데 도움이 되는 실시간 보호 기능입니다. Microsoft 맬웨어 방지 프로그램은 알려진 악성 또는 원치 않는 소프트웨어가 Azure 시스템에서 스스로의 설치나 실행을 시도할 때 구성 가능한 경고를 제공합니다.

[Azure Backup](../../backup/backup-overview.md) 은 자본 투자 없이 최소의 운영 비용으로 응용 프로그램 데이터를 보호 하는 확장 가능한 솔루션입니다. 애플리케이션 오류로 인해 데이터가 손상되고 사용자 오류로 인해 애플리케이션에 버그가 발생할 수 있습니다. Azure Backup은 Windows 및 Linux를 실행하는 가상 머신의 보호에 도움이 됩니다.

[Azure Site Recovery](../../site-recovery/site-recovery-overview.md)를 사용하면 기본 위치가 중단되는 경우 보조 위치에서 사용할 수 있도록 워크로드 및 앱의 복제, 장애 조치 및 복구를 오케스트레이션할 수 있습니다.

## <a name="ensure-compliance-cloud-services-due-diligence-checklist"></a>준수 확인: 클라우드 서비스 기한 성실 검사 목록

Microsoft는 조직이 클라우드로의 전환을 고려할 때 실사 작업을 연습해보는 데 도움을 주기 위해 [Cloud Services 실사 검사 목록](https://aka.ms/cloudchecklist.download)을 개발했습니다. 이 검사 목록은 개인 기업 및 공공 부문 조직(모든 수준 및 비영리 조직 정부 기관 포함)을 비롯한 모든 규모 및 종류의 조직이 자체 성과, 서비스, 데이터 관리 및 거버넌스 목표와 요구 사항을 식별하기 위한 체계를 제공합니다. 이를 통해 다양한 클라우드 서비스 공급자의 제품을 비교할 수 있으며 궁극적으로 클라우드 서비스 계약의 토대를 형성할 수 있게 됩니다.

이 검사 목록은 클라우드 서비스 계약인 ISO/IEC 19086에 대한 새로운 국제 표준에 맞는 프레임워크를 제공합니다. 이러한 표준은 조직에 대해 통일된 항목을 고려할 수 있도록 하여 클라우드 채택에 대해 의사 결정을 내리고 클라우드 서비스 제품을 비교할 수 있는 공통된 토대를 구축하도록 도와줍니다.

이 검사 목록은 철저한 검증을 통해 클라우드로 전환하도록 하며 클라우드 서비스 공급자를 선택하기 위한 체계적인 지침과 일관되고 반복 가능한 접근 방식을 제공합니다.

클라우드 채택은 더 이상 단순히 기술 의사 결정이 아닙니다. 검사 목록 요구 사항은 조직의 모든 측면을 아우르므로 CIO 및 CISO를 비롯한 모든 핵심 의사 결정자들과 합법적인 위험 관리, 조달 및 규정 준수 전문가들이 관여하게 됩니다. 이를 통해 의사 결정 프로세스 및 기본적인 결정의 효율성이 높아질 수 밖에 없으며 채택 과정에서 예기치 않은 장애 요인이 발생할 가능성이 줄어듭니다.

또한 이러한 검사 목록은 다음과 같은 효과를 가져옵니다.

- 클라우드 채택 프로세스의 초기에 의사 결정자들이 논의해야 할 핵심적인 주제가 노출됩니다.

- 는 규정 및 조직의 개인 정보, 개인 정보 및 데이터 보안에 대 한 철저 한 비즈니스 토론을 지원 합니다.

- 조직에서 클라우드 프로젝트에 영향을 줄 수 있는 잠재적인 문제를 식별하는 데 도움을 줍니다.

- 각 공급자에게 동일한 조건, 정의, 메트릭 및 전달 방식으로 일관된 질문을 제공함으로써 다양한 클라우드 서비스 공급자의 제품을 보다 간편하게 비교할 수 있습니다.

## <a name="azure-infrastructure-and-application-security-validation"></a>Azure 인프라 및 응용 프로그램 보안 유효성 검사

[Azure 운영 보안](operational-security.md) 은 사용자가 Microsoft Azure에서 데이터, 응용 프로그램 및 기타 자산을 보호 하는 데 사용할 수 있는 서비스, 제어 및 기능을 나타냅니다.

![보안 유효성 검사(검색)](./media/technical-capabilities/azure-security-technical-capabilities-fig7.png)

Azure 운영 보안은 Microsoft SDL(Security Development Lifecycle), Microsoft 보안 대응 센터 프로그램 및 사이버 보안 위협 환경에 대 한 심층 인식을 포함 하 여 Microsoft에서 제공 하는 다양 한 기능을 통해 얻은 지식을 통합 한 프레임 워크를 기반으로 합니다.

### <a name="microsoft-azure-monitor"></a>Microsoft Azure 모니터

하이브리드 클라우드를 위한 IT 관리 솔루션 [Azure Monitor](../../azure-monitor/index.yml) 입니다. 단독으로 사용 하거나 기존 System Center 배포를 확장 하기 위해 Azure Monitor 로그는 인프라의 클라우드 기반 관리에 대 한 최대 유연성과 제어를 제공 합니다.

![Azure Monitor](./media/technical-capabilities/azure-security-technical-capabilities-fig8.png)

Azure Monitor를 사용 하면 경쟁 솔루션 보다 저렴 한 비용으로 온-프레미스, Azure, AWS, Windows Server, Linux, VMware 및 OpenStack을 비롯 한 모든 클라우드의 모든 인스턴스를 관리할 수 있습니다. 클라우드 중심 세계를 위해 구축 된 Azure Monitor 새로운 비즈니스 과제를 충족 하 고 새로운 작업, 응용 프로그램 및 클라우드 환경을 수용 하는 가장 빠르고 비용 효율적인 방법으로 엔터프라이즈를 관리 하는 새로운 접근 방식을 제공 합니다.

### <a name="azure-monitor-logs"></a>Azure Monitor 로그

[Azure Monitor 로그](https://azure.microsoft.com/documentation/services/log-analytics) 는 관리 되는 리소스의 데이터를 중앙 리포지토리로 수집 하 여 모니터링 서비스를 제공 합니다. 이 데이터에는 이벤트, 성능 데이터 또는 API를 통해 제공되는 사용자 지정 데이터가 포함될 수 있습니다. 수집된 데이터는 경고, 분석 및 내보내기에 사용할 수 있습니다.

![Azure Monitor 로그](./media/technical-capabilities/azure-security-technical-capabilities-fig9.png)

이 방법을 사용하면 다양한 원본의 데이터를 통합할 수 있으므로 Azure 서비스의 데이터를 기존 온-프레미스 환경과 결합할 수 있습니다. 또한 모든 종류의 데이터에 모든 작업을 제공할 수 있도록 데이터 컬렉션을 해당 데이터에서 수행된 작업과 명확하게 구분합니다.

### <a name="azure-security-center"></a>Azure Security Center

[Azure Security Center](../../security-center/security-center-introduction.md) 는 Azure 리소스의 보안에 대 한 향상 된 가시성과 제어를 통해 위협을 예방, 감지 및 대응 하는 데 도움이 됩니다. 이는 Azure 구독에 대해 통합된 보안 모니터링 및 정책 관리를 제공하고 다른 방법으로 발견되지 않을 수 있는 위협을 감지하는 데 도움이 되며 보안 솔루션의 광범위한 환경에서 작동합니다.

보안 센터는 Azure 리소스의 보안 상태를 분석하여 잠재적인 보안 취약성을 식별합니다. 권장 사항 목록은 필요한 컨트롤 구성 과정을 안내합니다.

다음은 이러한 서비스의 예입니다.

- 맬웨어 방지 프로그램을 프로비전하여 악성 소프트웨어 식별 및 제거 지원

- 네트워크 보안 그룹 및 VM에 대한 트래픽 제어 규칙 구성

- 웹 애플리케이션 방화벽을 프로비전하여 웹 애플리케이션의 대상이 되는 공격에 대한 방어 지원

- 누락된 시스템 업데이트 배포

- 권장 기준과 일치하지 않는 OS 구성 해결

보안 센터는 Azure 리소스, 네트워크 및 맬웨어 방지 프로그램과 방화벽 같은 파트너 솔루션에서 자동으로 로그 데이터를 수집, 분석 및 통합합니다. 위협이 감지되었을 때 보안 경고가 생성됩니다. 감지되는 사항의 예:

- 알려진 악성 IP 주소와 통신하는 손상된 VM

- Windows 오류 보고를 사용하여 감지된 고급 맬웨어 프로그램

- VM에 대한 무작위 공격

- 통합 맬웨어 방지 프로그램 및 방화벽에서의 보안 경고

### <a name="azure-monitor"></a>Azure Monitor

[Azure Monitor](../../azure-monitor/overview.md) 는 특정 리소스 형식에 대 한 정보에 대 한 포인터를 제공 합니다. Azure 인프라(활동 로그)와 개별 Azure 리소스(진단 로그)의 데이터에 대한 시각화, 쿼리, 라우팅, 경고, 자동 크기 조정 및 자동화를 제공합니다.

클라우드 애플리케이션은 이동하는 부분이 많아 복잡합니다. 모니터링은 애플리케이션을 유지하고 정상 상태에서 실행할 수 있는 데이터를 제공합니다. 또한 잠재적 문제를 방지하거나 지난 문제를 해결할 수 있습니다.

![모니터링 데이터를 사용 하 여 응용 프로그램에 대 한 심층 통찰력을 얻을 수 있음을 보여 주는 다이어그램입니다.](./media/technical-capabilities/azure-security-technical-capabilities-fig10.png)
또한 애플리케이션에 대해 깊이 이해하는 데 모니터링 데이터를 사용할 수 있습니다. 이러한 정보를 통해 애플리케이션 성능이나 유지 관리를 개선하거나 그렇지 않으면 수동 개입이 필요한 작업을 자동화하는 데 도움이 될 수 있습니다.

네트워크 보안 감사는 네트워크 취약성을 검색하고 IT 보안 및 규제 관리 모델을 준수하는 것이 중요합니다. 보안 그룹 보기를 사용하면 구성된 네트워크 보안 그룹과 해당 보안 규칙 및 효과적인 보안 규칙을 검색할 수 있습니다. 적용된 규칙 목록을 사용하면 열려 있는 포트를 확인하고 네트워크 취약성을 평가할 수 있습니다.

### <a name="network-watcher"></a>Network Watcher

[Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md)는 Azure 내에서, Azure로, Azure로부터 네트워크 수준에서 상태를 모니터링하고 진단할 수 있게 하는 지역 서비스입니다. Network Watcher에서 제공하는 네트워크 진단 및 시각화 도구를 사용하면 Azure에서 네트워크를 파악하고, 진단하고, 정보를 얻을 수 있습니다. 이 서비스에는 패킷 캡처, 다음 홉, IP 흐름 확인, 보안 그룹 보기, NSG 흐름 로그가 포함됩니다. 시나리오 수준 모니터링에서는 개별 네트워크 리소스 모니터링과 달리 네트워크 리소스의 종단 간 보기를 제공합니다.

### <a name="storage-analytics"></a>스토리지 분석

[스토리지 분석](/rest/api/storageservices/fileservices/storage-analytics) 는 저장소 서비스에 대 한 요청에 대 한 집계 된 트랜잭션 통계 및 용량 데이터를 포함 하는 메트릭을 저장할 수 있습니다. 트랜잭션은 API 작업 수준과 스토리지 서비스 수준에서 모두 보고되며 용량은 스토리지 서비스 수준에서 보고됩니다. 메트릭 데이터를 사용하면 스토리지 서비스 사용량을 분석하고 스토리지 서비스에 대한 요청의 문제를 진단하며 서비스를 사용하는 애플리케이션의 성능을 개선할 수 있습니다.

### <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) 는 여러 플랫폼의 웹 개발자를 위한 확장 가능한 APM (응용 프로그램 성능 관리) 서비스입니다. 이를 사용하여 라이브 웹 애플리케이션을 모니터링합니다. 성능 이상을 자동으로 감지합니다. 사용자가 문제를 진단하고 앱을 사용하여 수행할 작업을 이해할 수 있도록 돕는 강력한 분석 도구를 포함합니다. 성능 및 가용성을 지속적으로 향상시킬 수 있도록 설계되었습니다. .NET, Node.js 및 Java EE를 포함하여 온-프레미스 또는 클라우드에서 호스팅되는 다양한 플랫폼의 애플리케이션에서 작동합니다. DevOps 프로세스와 통합 되며 다양 한 개발 도구에 대 한 연결 지점이 있습니다.

다음 사항을 모니터링합니다.

- **요청 속도, 응답 시간 및 실패율** - 하루 중 어느 시간에 어떤 페이지를 가장 많이 방문하는지, 사용자가 어디에 있는지 확인합니다. 어떤 페이지가 가장 성능이 우수한지 확인합니다. 요청이 더 있는데 응답 시간과 실패율이 높아지면 아마도 리소스 문제가 있는 것입니다.

- **종속성 비율, 응답 시간 및 실패율** - 외부 서비스 때문에 속도가 느려지는지 확인합니다.

- **예외** - 집계된 통계를 분석하거나 특정 인스턴스를 선택하여 스택 추적 및 관련 요청을 자세히 분석합니다. 서버 및 브라우저 예외가 전부 보고됩니다.

- **페이지 보기 및 로드 성능** - 사용자의 브라우저에서 보고합니다.

- **웹 페이지의 AJAX 호출** -속도, 응답 시간 및 실패율.

- **사용자 및 세션 수입니다.**

- Windows 또는 Linux 서버 컴퓨터의 **성능 카운터** - CPU, 메모리, 네트워크 사용량 등.

- Docker 또는 Azure의 **호스트 진단**.

- 앱의 **진단 추적 로그** - 추적 이벤트를 요청과 상호 연결하는 데 사용됩니다.

- 클라이언트 또는 서버 코드에서 직접 작성 하는 **사용자 지정 이벤트 및 메트릭** : 판매 된 항목, 판매 된 항목 등의 비즈니스 이벤트를 추적 하는 데 사용할 수 있습니다.

애플리케이션에 대한 인프라는 일반적으로 가상 컴퓨터, 스토리지 계정 및 가상 네트워크 또는 웹앱, 데이터베이스, 데이터베이스 서버 및 타사 서비스 등의 많은 구성 요소를 만듭니다. 이러한 구성 요소를 별도 엔터티로 표시하지 않으면, 대신 관련된 단일 엔터티의 상호 종속적으로 부분으로 표시됩니다. 그룹으로 배포, 관리 및 모니터링하려고 합니다. [Azure Resource Manager](../../azure-resource-manager/management/overview.md) 를 사용 하면 솔루션에서 리소스를 그룹으로 사용할 수 있습니다.

조정된 단일 작업에서 솔루션에 대한 모든 리소스를 배포, 업데이트 또는 삭제할 수 있습니다. 배포용 템플릿을 사용하고 이 템플릿을 테스트, 스테이징 및 프로덕션과 같은 여러 환경에서 사용할 수 있습니다. 리소스 관리자는 보안, 감사 및 태그 기능을 제공하여 배포 후에 리소스를 관리할 수 있습니다.

**리소스 관리자를 사용할 경우의 이점**

리소스 관리자는 다음과 같은 여러 이점이 있습니다.

- 이 리소스를 개별적으로 처리하는 것이 아니라 솔루션에 대한 모든 리소스를 그룹으로 배포, 관리 및 모니터링할 수 있습니다.

- 개발 수명 주기 내내 솔루션을 반복적으로 배포하며 안심하고 일관된 상태로 리소스를 배포할 수 있습니다.

- 스크립트가 아닌 선언적 템플릿을 통해 인프라를 관리할 수 있습니다.

- 올바른 순서로 배포되므로 리소스 간의 종속성을 정의할 수 있습니다.

- Azure 역할 기반 access control (Azure RBAC)은 기본적으로 관리 플랫폼에 통합 되기 때문에 리소스 그룹의 모든 서비스에 액세스 제어를 적용할 수 있습니다.

- 리소스에 태그를 적용하여 구독에서 모든 리소스를 논리적으로 구성할 수 있습니다.

- 같은 태그를 공유하는 리소스 그룹에 대한 비용을 확인하여 조직의 청구를 명확히 할 수 있습니다.

> [!Note]
> 리소스 관리자는 솔루션을 배포 및 관리하는 새로운 방식을 제공합니다. 이전 배포 모델을 사용 하 고 변경 내용을 알아보려면 [리소스 관리자 배포 및 클래식 배포 이해](../../azure-resource-manager/management/deployment-models.md)를 참조 하세요.

## <a name="next-step"></a>다음 단계

[Azure 보안 벤치 마크](../benchmarks/introduction.md) 프로그램에는 azure에서 사용 하는 서비스를 보호 하는 데 사용할 수 있는 보안 권장 사항 컬렉션이 포함 되어 있습니다.