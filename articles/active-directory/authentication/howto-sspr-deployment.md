---
title: Azure Active Directory 셀프 서비스 암호 재설정에 대한 배포 고려 사항
description: Azure AD 셀프 서비스 암호 재설정을 성공적으로 구현하기 위한 배포 고려 사항 및 전략에 대해 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: baselden
author: barbaraselden
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c9f59c7bb9b3977bdff7e3fd1ab78ed6fa2e412
ms.sourcegitcommit: 6fc156ceedd0fbbb2eec1e9f5e3c6d0915f65b8e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88717747"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset-deployment"></a>Azure Active Directory 셀프 서비스 암호 재설정 배포 계획

> [!IMPORTANT]
> 이 배포 계획은 Azure AD SSPR(셀프 서비스 암호 재설정)을 배포하기 위한 지침 및 모범 사례를 제공합니다.
>
> **최종 사용자 인 경우 계정으로 다시 전환 해야 하는 경우으로 [https://aka.ms/sspr](https://aka.ms/sspr) 이동 **합니다.

[SSPR(셀프 서비스 암호 재설정)](https://www.youtube.com/watch?v=tnb2Qf4hTP8)은 사용자가 IT 직원에게 문의하여 도움을 요청하지 않고도 자신의 암호를 재설정할 수 있는 Azure AD(Active Directory) 기능입니다. 사용자는 직접 언제 어디서든 빠르게 차단을 해제하고 작업을 계속 수행할 수 있습니다. 직원이 직접 차단 해제하도록 허용하면 조직은 가장 일반적인 암호 관련 문제에 소모되는 비생산적인 시간과 높은 지원 비용을 줄일 수 있습니다.

SSPR은 다음과 같은 주요 기능을 제공합니다.

* 셀프 서비스를 사용하면 최종 사용자가 관리자나 기술 지원팀에 지원을 요청하지 않고도 만료된 암호나 만료되지 않은 암호를 재설정할 수 있습니다.
* [암호 쓰기 저장](./concept-sspr-writeback.md)을 사용하면 클라우드를 통해 온-프레미스 암호 및 계정 잠금을 관리할 수 있습니다.
* 암호 관리 활동 보고서는 조직에서 발생하는 암호 재설정 및 등록 활동에서 관리자의 이해도를 높여줍니다.

이 배포 가이드에서는 SSPR 롤아웃을 계획하고 테스트하는 방법을 보여 줍니다.

작동 중인 SSPR을 빠르게 확인해본 후 다시 돌아와서 추가 배포 고려 사항을 알아보겠습니다.

> [!div class="nextstepaction"]
> [SSPR(셀프 서비스 암호 재설정) 사용](tutorial-enable-sspr.md)

## <a name="learn-about-sspr"></a>SSPR에 대해 알아보기

SSPR에 대해 자세히 알아봅니다. 다음 [작동 방법을 참조하세요. Azure AD 셀프 서비스 암호 재설정](./concept-sspr-howitworks.md)을 참조하세요.

### <a name="key-benefits"></a>주요 이점

SSPR을 사용하는 경우의 주요 이점은 다음과 같습니다.

* **비용 관리** SSPR은 사용자가 직접 암호를 재설정할 수 있도록 하여 IT 지원 비용을 절감합니다. 또한 암호 분실 및 잠금으로 인해 사용되는 시간 줄일 수 있습니다. 

* **직관적인 사용자 환경** 사용자가 장치 또는 위치에서 암호를 재설정하고 요청 시 계정을 차단 해제할 수 있는 직관적인 일회성 사용자 등록 프로세스를 제공합니다. SSPR을 사용하면 사용자가 더 빠르게 작업하고 생산성을 높일 수 있습니다.

* **유연성 및 보안** 기업은 SSPR을 사용하여 클라우드 플랫폼에서 제공하는 보안 및 유연성에 액세스할 수 있습니다. 관리자는 새로운 보안 요구 사항을 수용하도록 설정을 변경할 수 있으며 로그인 상태를 중단하지 않고도 사용자에게 이러한 변경 사항을 배포할 수 있습니다.

* **강력한 감사 및 사용 현황 추적** 조직은 사용자가 자신의 암호를 재설정하는 동안 비즈니스 시스템을 안전하게 유지할 수 있습니다. 강력한 감사 로그에는 암호 재설정 프로세스의 각 단계에 대한 정보가 포함되어 있습니다. 이 로그는 API에서 사용 가능하며 사용자는 선택한 SIEM(Security Incident and Event Monitoring) 시스템으로 데이터를 가져올 수 있습니다.

### <a name="licensing"></a>라이선스

Azure Active Directory는 사용자별로 사용이 허가 됩니다. 즉, 각 사용자에게는 사용하는 기능에 대한 적절한 라이선스가 필요합니다. SSPR에는 그룹 기반 라이선싱을 사용하는 것이 좋습니다. 

버전과 기능을 비교하고 그룹 또는 사용자 기반 라이선싱을 사용하려면 [Azure AD 셀프 서비스 암호 재설정에 대한 라이선싱 요구 사항](./concept-sspr-licensing.md)을 참조하세요.

가격 책정에 대한 자세한 내용은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조하세요.

### <a name="prerequisites"></a>사전 요구 사항

* 적어도 평가판 라이선스가 설정된 작동 중인 Azure AD 테넌트 필요한 경우, [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 전역 관리자 권한이 있는 계정


### <a name="training-resources"></a>교육 리소스

| 리소스| 링크 및 설명 |
| - | - |
| 동영상| [더 나은 IT 확장성으로 사용자 역량 강화](https://youtu.be/g9RpRnylxS8) 
| |[셀프 서비스 암호 재설정이란?](https://youtu.be/hc97Yx5PJiM)|
| |[셀프 서비스 암호 재설정 배포](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Azure AD에서 사용자에 대해 셀프 서비스 암호 재설정을 구성하는 방법](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[Azure Active Directory에 대한 보안 정보를 등록하는[등록하도록 사용자가 준비하는] 방법](https://youtu.be/gXuh0XS18wA) |
| 온라인 과정|[Microsoft Azure Active Directory에서 ID 관리](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) SSPR을 사용하여 사용자에게 안전한 최신 환경을 제공할 수 있습니다. 특히 "[Azure Active Directory 사용자 및 그룹 관리](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)" 모듈을 참조하세요. |
|Pluralsight 유료 과정 |[ID 및 액세스 관리 문제](https://www.pluralsight.com/courses/identity-access-management-issues) 조직에서 알아야 하는 IAM 및 보안 문제에 대해 알아봅니다. 특히 "기타 인증 방법" 모듈을 참조하세요.|
| |[Microsoft Enterprise Mobility Suite 시작](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) 인증, 권한 부여, 암호화 및 안전한 모바일 환경을 허용하는 방식으로 온-프레미스 자산을 클라우드로 확장하는 모범 사례를 알아봅니다. 특히 "Microsoft Azure Active Directory Premium의 고급 기능 구성" 모듈을 참조하세요.
|자습서 |[Azure AD 셀프 서비스 암호 재설정 파일럿 배포 완료](./tutorial-enable-sspr.md) |
| |[비밀번호 쓰기 저장을 사용하도록 설정](./tutorial-enable-sspr-writeback.md) |
| |[Windows 10의 로그인 화면에서 Azure AD 암호 재설정](./howto-sspr-windows.md) |
| FAQ|[암호 관리 질문과 대답](./active-directory-passwords-faq.md) |


### <a name="solution-architecture"></a>솔루션 아키텍처

다음 예제에서는 일반적인 하이브리드 환경에 대 한 암호 재설정 솔루션 아키텍처에 대해 설명합니다.

![솔루션 아키텍처 다이어그램](./media/howto-sspr-deployment//solutions-architecture.png)

워크플로에 대한 설명

사용자는 암호를 재설정하기 위해 [암호 재설정 포털](https://aka.ms/sspr)로 이동합니다. ID를 증명하려면 이전에 등록된 인증 방법을 확인해야 합니다. 암호를 성공적으로 재설정하면 재설정 프로세스가 시작됩니다.

* 클라우드 전용 사용자의 경우 SSPR은 Azure AD에 새 암호를 저장합니다. 

* 하이브리드 사용자의 경우 SSPR은 Azure AD Connect 서비스를 통해 암호를 온-프레미스 Active Directory에 다시 작성합니다. 

참고: [PHS(암호 해시 동기화)](../hybrid/whatis-phs.md)를 사용하지 않도록 설정한 사용자의 경우 SSPR은 온-프레미스 Active Directory에만 암호를 저장합니다.

### <a name="best-practices"></a>모범 사례

조직에서 인기 있는 다른 응용 프로그램 또는 서비스와 함께 SSPR을 배포하여 사용자가 신속하게 등록하도록 지원할 수 있습니다. 이 작업을 수행하면 많은 양의 로그인이 생성되고 등록이 작동됩니다.

SSPR을 배포하기 전에 각 암호 재설정 호출의 수와 평균 비용을 결정하도록 선택할 수 있습니다. 배포 후 이 데이터를 사용하여 SSPR이 조직에 제공하는 가치를 표시할 수 있습니다.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>SSPR 및 MFA에 대해 결합된 등록 사용

Microsoft는 조직이 SSPR 및 다단계 인증을 위한 결합된 등록 환경을 사용하는 것을 권장합니다. 이러한 결합된 등록 환경을 사용하는 경우 사용자는 두 기능을 모두 사용하려면 등록 정보를 한 번만 선택하면 합니다.

결합된 등록 환경에서는 SSPR 및 Azure Multi-Factor Authentication을 모두 사용하도록 설정할 필요가 없습니다. 결합된 등록은 조직에 더 나은 사용자 환경을 제공합니다. 자세한 내용은 [결합된 보안 정보 등록](concept-registration-mfa-sspr-combined.md)을 참조하세요.

## <a name="plan-the-deployment-project"></a>배포 프로젝트 계획

사용하는 환경에서 이 배포에 대한 전략을 결정하는 동안 조직의 요구 사항을 고려하세요.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트에 오류가 발생하는 경우는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지하려면 [올바른 관련자를 참여시키고](https://aka.ms/deploymentplans) 프로젝트의 해당 관련자 역할은 관련자와 프로젝트 입력 및 책임을 문서화하여 숙지해야 합니다.

#### <a name="required-administrator-roles"></a>필수 관리자 역할


| 비즈니스 역할/가상 사용자| Azure AD 역할(필요한 경우) |
| - | - |
| 수준 1 기술 지원팀| 암호 관리자 |
| 수준 2 기술 지원팀| 사용자 관리자 |
| SSPR 관리자| 전역 관리자 |


### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사용자와 적극적으로 소통해야 합니다. 최종 사용자 통신 전략을 계획하는 방법에 대해 알아보려면 [Microsoft 다운로드 센터의 셀프 서비스 암호 재설정 배포 자료](https://www.microsoft.com/download/details.aspx?id=56768)를 살펴보세요.

### <a name="plan-a-pilot"></a>파일럿 계획

SSPR의 초기 구성은 테스트 환경에 있는 것이 좋습니다. 조직의 사용자 하위 집합에 SSPR을 사용하도록 설정하여 파일럿 그룹을 시작합니다. [파일럿에 대한 모범 사례](../fundamentals/active-directory-deployment-plans.md)를 참조하세요.

그룹을 만들려면 [Azure Active Directory에서 그룹 만들기 및 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md) 방법을 참조하세요. 

## <a name="plan-configuration"></a>구성 계획

권장 값과 함께 SSPR을 사용하도록 설정하려면 다음 설정이 필요합니다.

| 영역 | 설정 | 값 |
| --- | --- | --- |
| **SSPR 속성** | 셀프 서비스 암호 재설정이 사용하도록 설정됨 | **파일럿의 경우**  그룹/프로덕션의 경우 **모두**가 선택됨 |
| **인증 방법** | 등록에 필요한 인증 방법 | 재설정에 필요한 것보다 항상 2개 이상 |
|   | 재설정에 필요한 인증 방법 | 1개 또는 2개 |
| **등록** | 로그인 시 사용자가 등록하도록 요구 | 예 |
|   | 사용자가 인증 정보를 다시 확인해야 하기 전의 일 수 | 90~180일 |
| **알림** | 사용자에게 암호 재설정에 대해 알림 | 예 |
|   | 다른 관리자가 암호를 재설정하면 모든 관리자에게 알림 | 예 |
| **사용자 지정** | 기술 지원팀 링크 사용자 지정 | 예 |
|   | 기술 지원팀 이메일 또는 URL 사용자 지정 | 지원 사이트 또는 이메일 주소 |
| **온-프레미스 통합** | 온-프레미스 AD에 암호 다시 쓰기 | 예 |
|   | 사용자가 암호를 재설정하지 않고 계정의 잠금을 해제하도록 허용 | 예 |

### <a name="sspr-properties"></a>SSPR 속성

SSPR을 사용하는 경우 파일럿 환경에서 적절한 보안 그룹을 선택합니다.

* 모든 사용자에 대해 SSPR 등록을 적용하려면 **모두** 옵션을 사용하는 것이 좋습니다.
* 사용하지 않는 경우에는 적절한 Azure AD 또는 AD 보안 그룹을 선택합니다.

### <a name="authentication-methods"></a>인증 방법

SSPR이 사용하도록 설정된 경우 사용자는 관리자가 사용하도록 설정한 인증 방법으로 데이터를 표시하는 경우에만 자신의 암호를 재설정할 수 있습니다. 방법에는 전화, Authenticator 앱 알림, 보안 질문 등이 포함됩니다. 자세한 내용은 [인증 방법이란?](./concept-authentication-methods.md)을 참조하세요.

다음 인증 방법 설정을 사용하는 것이 좋습니다.

* **등록에 필요한 인증 방법**을 재설정에 필요한 숫자보다 둘 이상으로 설정합니다. 여러 인증을 허용하면 사용자가 재설정해야 할 경우 유연성이 제공됩니다.

* 조직에 적합한 수준으로 **재설정에 필요한 방법 수**를 설정합니다. 하나는 최소한의 마찰이 필요하며 두 개는 보안 태세를 강화할 수 있습니다. 

참고: 사용자에게는 [Azure Active Directory의 암호 정책 및 제한 사랑](./concept-sspr-policy.md)에 구성된 인증 방법이 있어야 합니다.

### <a name="registration-settings"></a>등록 설정

**로그인 시 사용자가 등록하도록 요구**를 **예**로 설정합니다. 이 설정을 사용하려면 모든 사용자가 보호되도록 사용자가 로그인할 때 등록해야 합니다.

조직에 더 짧은 기간 동안 비즈니스 요구가 필요한 경우를 제외하고 **인증 정보를 다시 확인하도록 사용자에게 요청할 때까지의 기간**을 **90**일에서 **180**일 사이로 설정합니다.

### <a name="notifications-settings"></a>알림 설정

**암호 재설정 시 사용자에게 알림** 및 **다른 관리자가 암호를 재설정할 때 모든 관리자에게 알림**을 모두 **예**로 지정합니다. 두 항목에 모두 **예**를 선택하면 암호가 재설정될 때 사용자가 알 수 있으므로 보안이 강화됩니다. 또한 관리자가 암호를 변경할 때에도 모든 관리자가 이를 인식하도록 합니다. 사용자나 관리자가 알림을 받고 암호를 변경하지 않은 경우 잠재적인 보안 문제를 즉시 보고할 수 있습니다.

### <a name="customization-settings"></a>사용자 지정 설정

문제가 발생한 사용자가 즉시 도움을 받을 수 있도록 기술 지원팀 이메일 또는 URL을 사용자 지정해야 합니다. 이 옵션을 사용자에게 익숙한 일반적인 기술 지원팀 이메일 주소나 웹 페이지로 설정합니다. 

자세한 내용은 [셀프 서비스 암호 재설정을 위한 Azure AD 기능 사용자 지정](./howto-sspr-customization.md)을 참조하세요.

### <a name="password-writeback"></a>암호 쓰기 저장

**암호 쓰기 저장**은 [Azure AD Connect](../hybrid/whatis-hybrid-identity.md)를 통해 활성화되며 클라우드의 암호 재설정 항목을 실시간으로 기존 온-프레미스 디렉터리에 다시 쓸 수 있는 기능입니다. 자세한 내용은 [암호 쓰기 저장이란?](./concept-sspr-writeback.md)을 참조하세요.

다음 설정을 사용하는 것이 좋습니다.

* **온-프레미스 AD에 암호를 다시 쓰기**를 **예**로 설정해야 합니다. 
* **사용자가 암호를 재설정하지 않고 계정의 잠금을 해제하도록 허용**을 **예**로 설정합니다.

기본적으로 Azure AD는 암호 재설정을 수행할 때 계정을 잠금 해제합니다.

### <a name="administrator-password-setting"></a>관리자 암호 설정

관리자 계정에는 상승된 권한이 있습니다. 온-프레미스 엔터프라이즈 또는 도메인 관리자는 SSPR을 통해 암호를 재설정할 수 없습니다. 온-프레미스 관리자 계정에는 다음과 같은 제한 사항이 있습니다.

* 자신의 온-프레미스 환경에서만 암호를 변경할 수 있습니다.
* 암호를 재설정하는 방법으로 본인 확인 질문 및 답변을 사용할 수 없습니다.

온-프레미스 Active Directory 관리자 계정은 Azure AD와 동기화하지 않는 것이 좋습니다.

### <a name="environments-with-multiple-identity-management-systems"></a>여러 ID 관리 시스템을 사용하는 환경

일부 환경에는 여러 ID 관리 시스템이 있습니다. Oracle AM 및 SiteMinder와 같은 온-프레미스 ID 관리자는 암호의 AD와 동기화해야 합니다. 이 작업은 PCNS(Password Change Notification Service)과 같은 도구를 MIM(Microsoft Identity Manager)과 함께 사용하여 수행할 수 있습니다. 더 복잡한 시나리오에 대한 정보를 찾으려면 [도메인 컨트롤러에서 MIM 암호 변경 알림 서비스 배포](/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller) 문서를 참조하세요.

## <a name="plan-testing-and-support"></a>테스트 및 지원 계획

초기 파일럿 그룹에서 조직 전체에 이르는 배포의 각 단계에서 결과가 정상적으로 나타나야 합니다.

### <a name="plan-testing"></a>테스트 계획

배포가 정상적으로 작동하도록 하려면 테스트 사례 집합을 계획하여 구현의 유효성을 검사합니다. 테스트 사례를 평가하려면 암호가 있는 관리자가 아닌 테스트 사용자가 필요합니다. 사용자를 만들어야 하는 경우 [Azure Active Directory에 새 사용자 추가](../fundamentals/add-users-azure-active-directory.md)를 참조하세요.

다음 표에는 정책에 따라 조직에 예상되는 결과를 문서화하는 데 사용할 수 있는 유용한 테스트 시나리오가 포함되어 있습니다.
<br>


| 비즈니스 사례| 예상 결과 |
| - | - |
| SSPR 포털을 회사 네트워크 내에서 액세스할 수 있음| 조직이 결정함 |
| SSPR 포털을 회사 네트워크 외부에서 액세스할 수 있음| 조직이 결정함 |
| 사용자가 암호 재설정을 사용할 수 없는 경우 브라우저에서 사용자 암호를 재설정함| 사용자가 암호 재설정 흐름에 액세스할 수 없음 |
| 사용자가 암호 재설정을 등록하지 않은 경우 브라우저에서 사용자 암호를 재설정함| 사용자가 암호 재설정 흐름에 액세스할 수 없음 |
| 사용자가 암호 재설정 등록을 수행하는 경우 로그인함| 사용자에게 보안 정보를 등록하라는 메시지를 표시함 |
| 암호 재설정 등록이 완료되면 사용자 로그인| 사용자에게 보안 정보를 등록하라는 메시지를 표시함 |
| 사용자에게 라이선스가 없는 경우 SSPR 포털에 액세스할 수 있음| 액세스 가능 |
| Windows 10 Azure AD 조인 또는 하이브리드 Azure AD 조인 장치 잠금 화면에서 사용자 암호 재설정| 사용자가 암호를 재설정할 수 있음 |
| SSPR 등록 및 사용 현황 데이터가 거의 실시간으로 관리자에게 제공됨| 감사 로그를 통해 사용 가능 |

또한 [Azure AD 셀프 서비스 암호 재설정 파일럿 배포 완료](./tutorial-enable-sspr.md)를 참조할 수 있습니다. 이 자습서에서는 조직의 SSPR에서 파일럿 배포를 활성화하고 관리자가 아닌 계정을 사용하여 테스트합니다.

### <a name="plan-support"></a>지원 계획

SSPR은 일반적으로 사용자 문제를 초래하지 않지만, 발생할 수 있는 문제를 해결하기 위해 지원 직원이 대비하는 것이 중요합니다. 관리자는 Azure AD 포털을 통해 최종 사용자에 대 한 암호를 재설정할 수 있지만 셀프 서비스 지원 프로세스를 통해 문제를 해결하는 것이 좋습니다.

지원 팀의 성공을 이용하려면 사용자로부터 받은 질문에 따라 FAQ를 만들면 됩니다. 다음은 몇 가지 예입니다.

| 시나리오| Description |
| - | - |
| 사용자에게 사용 가능한 등록된 인증 방법이 없음| 사용자가 자신의 암호를 재설정하려고 하지만 사용 가능하도록 등록한 인증 방법이 없습니다(예: 집에 휴대폰을 두어 이메일에 액세스할 수 없음). |
| 사용자가 사무실 또는 휴대폰에 텍스트 메시지를 또는 전화를 받지 않음| 사용자가 텍스트 메시지 또는 전화를 통해 ID를 확인하려고 하지만 텍스트 메시지/전화가 수신되지 않습니다. |
| 사용자가 암호 재설정 포털에 액세스할 수 없음| 사용자가 암호를 재설정하려고 하지만 암호 재설정을 사용할 수 없으며, 암호를 업데이트할 페이지에 액세스할 수 없습니다. |
| 사용자가 새 암호를 설정할 수 없음| 사용자가 암호 재설정 흐름 중에 확인을 완료했지만 새 암호를 설정할 수는 없습니다. |
| 사용자의 Windows 10 장치에 암호 재설정 링크가 표시되지 않음| 사용자가 Windows 10 잠금 화면에서 암호를 재설정하려고 하지만 장치가 Azure AD에 가입 되어있지 않거나 Intune 장치 정책이 사용하도록 설정되어 있지 않습니다. |

### <a name="plan-rollback"></a>롤백 계획

배포를 롤백하려면 다음을 수행합니다.

* 단일 사용자의 경우 보안 그룹에서 사용자를 제거합니다. 

* 그룹의 경우 SSPR 구성에서 그룹을 제거합니다.

* 모든 사용자는 Azure AD 테넌트에 대해 SSPR을 사용하지 않도록 설정합니다.

## <a name="deploy-sspr"></a>SSPR 배포

배포하기 전에 다음을 수행했는지 확인합니다.

1. [통신 계획](#plan-communications)을 만들고 실행을 시작했습니다.

1. 적절한 [구성 설정](#plan-configuration)을 지정했습니다.

1. [파일럿](#plan-a-pilot) 및 프로덕션 환경에 대한 사용자 및 그룹을 식별했습니다.

1. 등록 및 셀프 서비스에 대한 [구성 설정을 지정](#plan-configuration)했습니다.

1. 하이브리드 환경인 경우 [암호 쓰기 저장을 구성](#password-writeback)했습니다.


**이제 SSPR을 배포할 준비가 되었습니다!**

다음 영역을 구성하는 전체 단계별 지침은 [셀프 서비스 암호 재설정 사용](./tutorial-enable-sspr.md#enable-self-service-password-reset)을 참조하세요.

1. [인증 방법](./concept-authentication-methods.md)

1. [등록 설정](./concept-registration-mfa-sspr-combined.md)

1. [알림 설정](#notifications-settings)

1. [사용자 지정 설정](./howto-sspr-customization.md)

1. [온-프레미스 통합](./tutorial-enable-sspr-writeback.md)

### <a name="enable-sspr-in-windows"></a>Windows에서 SSPR 사용
Windows 7, 8, 8.1 및 10을 실행하는 컴퓨터의 경우 [사용자가 Windows 로그인 화면에서 암호를 재설정하도록 설정](./howto-sspr-windows.md)할 수 있습니다.

## <a name="manage-sspr"></a>SSPR 관리

Azure AD는 감사 및 보고서를 통해 SSPR 성능에 대한 추가 정보를 제공할 수 있습니다.

### <a name="password-management-activity-reports"></a>암호 관리 활동 보고서 

Azure Portal에서 미리 빌드된 보고서를 사용하여 SSPR 성능을 측정할 수 있습니다. 적절히 라이선스를 받은 경우 사용자 지정 쿼리를 만들 수도 있습니다. 자세한 내용은 [Azure AD 암호 관리에 대한 보고 옵션](./howto-sspr-reporting.md)을 참조하세요.

> [!NOTE]
>  사용자는 [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md)여야 하며 조직의 이 데이터를 수집하기 위해 옵트인해야 합니다. 옵트인하려면 적어도 한 번 보고 탭이나 감사 로그를 방문해야 합니다. 그때까지는 조직에 데이터가 수집되지 않습니다.

등록 및 암호 재설정을 위한 감사 로그는 30일 동안 사용할 수 있습니다. 회사 내의 보안 감사에 더 긴 보존이 필요한 경우 로그를 내보내고 [Azure Sentinel](../../sentinel/connect-azure-active-directory.md), Splunk 또는 ArcSight와 같은 SIEM 도구로 사용해야 합니다.

![SSPR 보고 스크린샷](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>인증 방법 - 사용량 및 인사이트

[사용량 및 인사이트](./howto-authentication-methods-usage-insights.md)를 사용하여 Azure MFA 및 SSPR 같은 기능의 인증 방법이 조직에서 작동되는 방식을 이해할 수 있습니다. 이 보고 기능은 등록 방법 및 사용 방법을 이해할 수 있는 수단을 조직에 제공합니다.

### <a name="troubleshoot"></a>문제 해결

* [셀프 서비스 암호 재설정 문제 해결](./active-directory-passwords-troubleshoot.md)을 참조하세요. 

* [암호 관리 질문과 대답](./active-directory-passwords-faq.md)을 따르세요. 

### <a name="helpful-documentation"></a>유용한 설명서

* [인증 방법이란?](./concept-authentication-methods.md)

* [작동 방법: Azure AD 셀프 서비스 암호 재설정이란?](./concept-sspr-howitworks.md)

* [Azure AD의 셀프 서비스 암호 재설정 기능 사용자 지정](./howto-sspr-customization.md)

* [Azure Active Directory에서 암호 정책 및 제한](./concept-sspr-policy.md) 문서를 검토하세요.

* [비밀 번호 쓰기 저장 이란?](./concept-sspr-writeback.md)

## <a name="next-steps"></a>다음 단계

* SSPR 배포를 시작하려면 [Azure AD 셀프 서비스 암호 재설정을 사용](tutorial-enable-sspr.md)을 참조하세요.

* [Azure AD 암호 보호 구현 고려](./concept-password-ban-bad.md)

* [Azure AD 스마트 잠금 구현 고려](./howto-password-smart-lockout.md)