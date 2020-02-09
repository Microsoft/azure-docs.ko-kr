---
title: 셀프 서비스 암호 재설정 배포-Azure Active Directory
description: Azure AD 셀프 서비스 암호 재설정의 성공적인 구현 전략
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
ms.openlocfilehash: 785a8a031a10232a37b235711ba919fdc1df35d3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77061426"
---
# <a name="plan-an-azure-active-directory-self-service-password-reset"></a>Azure Active Directory 셀프 서비스 암호 재설정 계획

> [!NOTE]
> 이 배포 계획에서는 Azure AD 셀프 서비스 암호 재설정 (SSPR)을 배포 하는 방법에 대 한 계획 지침 및 모범 사례를 제공 합니다. <br>**SSPR 도구를 찾고 계정으로 돌아가려면 [https://aka.ms/sspr](https://aka.ms/sspr)으로 이동** 합니다.

[SSPR (셀프 서비스 암호 재설정)](https://www.youtube.com/watch?v=tnb2Qf4hTP8) 는 사용자가 도움을 위해 IT 직원에 게 연락 하지 않고도 자신의 암호를 재설정할 수 있도록 하는 AD (Azure Active Directory) 기능입니다. 사용자는 신속 하 게 차단을 해제 하 고 하루 또는 시간에 관계 없이 작업을 계속할 수 있습니다. 직원 들이 자신을 차단 해제할 수 있도록 하 여 대부분의 일반적인 암호 관련 문제에 대 한 비 생산성 시간 및 지원 비용을 줄일 수 있습니다. 

SSPR는 다음과 같은 주요 기능을 제공 합니다.

* 셀프 서비스를 사용 하면 최종 사용자가 관리자나 기술 지원팀에 문의 하지 않고도 만료 되었거나 만료 되지 않은 암호를 다시 설정할 수 있습니다.

* [비밀 번호 쓰기 저장](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) 을 사용 하면 클라우드를 통해 온-프레미스 암호와 계정 잠금 확인을 관리할 수 있습니다.

* 암호 관리 작업 보고서는 조직에서 발생 하는 암호 재설정 및 등록 작업을 관리자에 게 제공 합니다.

## <a name="learn-about-sspr"></a>SSPR에 대 한 자세한 정보

SSPR에 대해 자세히 알아보세요. [작동 방법: AZURE AD 셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)을 참조 하세요.

### <a name="key-benefits"></a>주요 이점

SSPR를 사용 하는 경우의 주요 이점은 다음과 같습니다.

* **비용 관리**. SSPR 사용자가 자신의 암호를 다시 설정할 수 있도록 하 여 IT 지원 비용을 절감 합니다. 또한 암호 및 잠금 손실로 인 한 손실 된 시간을 줄일 수 있습니다. 

* **직관적인 사용자 환경**. 사용자가 장치 또는 위치에서 암호를 재설정 하 고 요청 시 계정을 차단 해제할 수 있도록 하는 직관적인 일회성 사용자 등록 프로세스를 제공 합니다. SSPR를 사용 하면 사용자가 더 빠르게 작업 하 고 생산성을 높일 수 있습니다.

* **유연성 및 보안**. SSPR를 통해 기업은 클라우드 플랫폼에서 제공 하는 보안과 유연성에 액세스할 수 있습니다. 관리자는 새 보안 요구 사항에 맞게 설정을 변경 하 고 이러한 변경 내용을 로그인을 방해 하지 않고 사용자에 게 롤아웃할 수 있습니다.

* **강력한 감사 및 사용 현황 추적**. 조직에서는 사용자가 자신의 암호를 다시 설정 하는 동안 비즈니스 시스템이 안전 하 게 유지 되도록 할 수 있습니다. 강력한 감사 로그에는 암호 다시 설정 프로세스의 각 단계에 대 한 정보가 포함 됩니다. 이러한 로그는 API에서 사용할 수 있으며 사용자가 선택한 SIEM (보안 인시던트 및 이벤트 모니터링) 시스템으로 데이터를 가져올 수 있도록 합니다.

### <a name="licensing"></a>라이선스

Azure Active Directory는 사용자별로 사용이 허가 됩니다. 각 사용자는 사용 하는 기능에 대 한 적절 한 라이선스가 필요 합니다. SSPR에 대 한 그룹 기반 라이선스를 사용 하는 것이 좋습니다. 

버전 및 기능을 비교 하 고 그룹 또는 사용자 기반 라이선스를 사용 하도록 설정 하려면 [AZURE AD 셀프 서비스 암호 재설정에 대 한 라이선스 요구 사항](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-licensing)을 참조 하세요.

가격 책정에 대 한 자세한 내용은 [Azure Active Directory 가격 책정](https://azure.microsoft.com/pricing/details/active-directory/)을 참조 하세요.

### <a name="prerequisites"></a>필수 조건

* 적어도 평가판 라이선스가 설정된 작동 중인 Azure AD 테넌트 필요한 경우, [체험 계정을 만드세요](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* 전역 관리자 권한이 있는 계정


### <a name="training-resources"></a>학습 리소스

| 리소스| 링크 및 설명 |
| - | - |
| 비디오| [더 나은 IT 확장성으로 사용자의 역량 강화](https://youtu.be/g9RpRnylxS8) 
| |[셀프 서비스 암호 재설정 이란?](https://youtu.be/hc97Yx5PJiM)|
| |[셀프 서비스 암호 재설정 배포](https://www.youtube.com/watch?v=Pa0eyqjEjvQ&index=18&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0)|
| |[Azure AD에서 사용자에 대해 셀프 서비스 암호 재설정을 구성 하는 방법](https://azure.microsoft.com/resources/videos/self-service-password-reset-azure-ad/) |
| |[방법 [사용자 준비] Azure Active Directory에 대 한 보안 정보 등록](https://youtu.be/gXuh0XS18wA) |
| 온라인 과정|[Microsoft Azure Active Directory에서 Id 관리](https://www.pluralsight.com/courses/microsoft-azure-active-directory-managing-identities) SSPR를 사용 하 여 사용자에 게 현대적인 보호 된 환경을 제공 합니다. 특히 "[Azure Active Directory 사용자 및 그룹 관리](https://app.pluralsight.com/library/courses/microsoft-azure-active-directory-managing-identities/table-of-contents)" 모듈을 참조 하세요. |
|Pluralsight 유료 과정 |[Id 및 액세스 관리 문제](https://www.pluralsight.com/courses/identity-access-management-issues) 조직에서 알아야 할 IAM 및 보안 문제에 대해 알아봅니다. 특히 "기타 인증 방법" 모듈을 참조 하세요.|
| |[Microsoft Enterprise Mobility Suite 시작](https://www.pluralsight.com/courses/microsoft-enterprise-mobility-suite-getting-started) 인증, 권한 부여, 암호화 및 보안 모바일 환경을 허용 하는 방식으로 온-프레미스 자산을 클라우드로 확장 하기 위한 모범 사례를 알아봅니다. 특히 "Microsoft Azure Active Directory Premium의 고급 기능 구성" 모듈을 참조 하세요.
|자습서 |[Azure AD 셀프 서비스 암호 재설정 파일럿 롤아웃 완료](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) |
| |[비밀번호 쓰기 저장을 사용하도록 설정](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback) |
| |[Windows 10의 로그인 화면에서 Azure AD 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows) |
| FAQ|[암호 관리에 대 한 질문과 대답](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) |


### <a name="solution-architecture"></a>솔루션 아키텍처

다음 예제에서는 일반적인 하이브리드 환경에 대 한 암호 재설정 솔루션 아키텍처에 대해 설명 합니다.

![솔루션 아키텍처 다이어그램](./media/howto-sspr-deployment//solutions-architecture.png)

워크플로에 대 한 설명

암호를 재설정 하기 위해 사용자는 [암호 재설정 포털로](https://aka.ms/sspr)이동 합니다. Id를 증명 하려면 이전에 등록 된 인증 방법이 나 메서드를 확인 해야 합니다. 암호를 성공적으로 다시 설정 하면 다시 설정 프로세스가 시작 됩니다.

* 클라우드 전용 사용자의 경우 SSPR는 Azure AD에 새 암호를 저장 합니다. 

* 하이브리드 사용자의 경우 SSPR는 Azure AD Connect 서비스를 통해 암호를 온-프레미스 Active Directory에 다시 씁니다. 

참고: [암호 해시 동기화 (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) 를 사용 하지 않도록 설정 된 사용자의 경우 SSPR는 프레미스 Active Directory에만 암호를 저장 합니다.

### <a name="best-practices"></a>모범 사례

조직에서 널리 사용 되는 다른 응용 프로그램 또는 서비스와 함께 SSPR를 배포 하 여 사용자가 신속 하 게 등록 하도록 도울 수 있습니다. 이 작업을 수행 하면 많은 양의 로그인이 생성 되 고 등록이 구동 됩니다.

SSPR를 배포 하기 전에 각 암호 재설정 호출의 수와 평균 비용을 결정 하도록 선택할 수 있습니다. 이 데이터 후 배포를 사용 하 여 SSPR가 조직에 가져오는 값을 표시할 수 있습니다.

#### <a name="enable-combined-registration-for-sspr-and-mfa"></a>SSPR 및 MFA에 대해 결합 된 등록 사용

조직에서 SSPR 및 multi-factor authentication에 대해 결합 된 등록 환경을 사용 하도록 설정 하는 것이 좋습니다. 이러한 결합 된 등록 환경을 사용 하도록 설정 하는 경우 사용자는 두 기능을 모두 사용 하도록 등록 정보를 한 번만 선택 해야 합니다.

결합 된 등록 환경에서는 SSPR 및 Azure Multi-Factor Authentication를 모두 사용 하도록 설정할 필요가 없습니다. 결합 된 등록을 통해 조직에서 더 나은 사용자 환경을 제공 합니다. 자세한 내용은 [결합 된 보안 정보 등록 (미리 보기)](concept-registration-mfa-sspr-combined.md) 을 참조 하세요.

## <a name="plan-the-deployment-project"></a>배포 프로젝트 계획

사용자 환경에서이 배포에 대 한 전략을 결정 하는 동안 조직의 요구 사항을 고려 하세요.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트에 오류가 발생 하는 경우 일반적으로 영향, 결과 및 책임에 대 한 예상치가 일치 하지 않기 때문입니다. 이러한 문제를 방지 하려면 이해 관계자와 프로젝트의 프로젝트 입력 및 accountabilities를 문서화 하 여 프로젝트의 관련자 역할을 잘 이해 하 고 [있어야](https://aka.ms/deploymentplans) 합니다.

#### <a name="required-administrator-roles"></a>필수 관리자 역할


| 비즈니스 역할/가상 사용자| Azure AD 역할 (필요한 경우) |
| - | - |
| 수준 1 기술 지원팀| 암호 관리자 |
| 수준 2 기술 지원팀| 사용자 관리자 |
| SSPR 관리자| 전역 관리자 |


### <a name="plan-communications"></a>통신 계획

통신은 모든 새 서비스의 성공에 중요 합니다. 사용자가 경험을 변경 하는 방법, 변경 된 시간 및 문제가 발생 한 경우 지원을 얻는 방법에 대 한 사용자와 사전에 연락 해야 합니다. [Microsoft 다운로드 센터에서 셀프 서비스 암호 재설정 롤아웃 자료](https://www.microsoft.com/download/details.aspx?id=56768) 를 검토 하 여 최종 사용자 통신 전략을 계획 하는 방법에 대 한 아이디어를 확인 합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

SSPR의 초기 구성은 테스트 환경에 제공 하는 것이 좋습니다. 조직에서 사용자의 하위 집합에 대해 SSPR를 사용 하도록 설정 하 여 파일럿 그룹으로 시작 합니다. [파일럿에 대 한 모범 사례](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans)를 참조 하세요.

그룹을 만들려면 [그룹을 만들고 Azure Active Directory에서 구성원을 추가](https://docs.microsoft.com/azure/active-directory/active-directory-groups-create-azure-portal)하는 방법을 참조 하세요. 

## <a name="plan-configuration"></a>계획 구성

권장 값과 함께 SSPR를 사용 하도록 설정 하려면 다음 설정이 필요 합니다.

| 영역 | 설정 | 값 |
| --- | --- | --- |
| **SSPR 속성** | 셀프 서비스 암호 재설정 사용 | 파일럿 **/프로덕션용** 으로 **선택한** 그룹 |
| **인증 방법** | 등록 하는 데 필요한 인증 방법 | 다시 설정 하는 데 필요한 것 보다 항상 1 개 이상 |
|   | 다시 설정 하는 데 필요한 인증 방법 | 1개 또는 2개 |
| **등록** | 로그인 시 사용자가 등록하도록 요구 | 예 |
|   | 사용자가 인증 정보를 다시 확인해야 하기 전의 일 수 | 90 – 180 일 |
| **알림** | 사용자에게 암호 재설정에 대해 알림 | 예 |
|   | 다른 관리자가 암호를 재설정하면 모든 관리자에게 알림 | 예 |
| **사용자 지정** | 기술 지원팀 링크 사용자 지정 | 예 |
|   | 사용자 지정 기술 지원팀 전자 메일 또는 URL | 지원 사이트 또는 메일 주소 |
| **온-프레미스 통합** | 온-프레미스 AD에 암호 다시 쓰기 | 예 |
|   | 사용자가 암호를 재설정 하지 않고 계정의 잠금을 해제할 수 있도록 허용 | 예 |

### <a name="sspr-properties"></a>SSPR 속성

SSPR를 사용 하도록 설정 하는 경우 파일럿 환경에서 적절 한 보안 그룹을 선택 합니다.

* 모든 사용자에 대해 SSPR 등록을 적용 하려면 **All** 옵션을 사용 하는 것이 좋습니다.
* 그렇지 않으면 적절 한 Azure AD 또는 AD 보안 그룹을 선택 합니다.

### <a name="authentication-methods"></a>인증 방법

SSPR를 사용 하도록 설정 하면 사용자는 관리자가 사용 하도록 설정한 인증 방법에 데이터가 있는 경우에만 암호를 다시 설정할 수 있습니다. 메서드에는 전화, 인증자 앱 알림, 보안 질문 등이 포함 됩니다. 자세한 내용은 [인증 방법 이란?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)을 참조 하세요.

다음 인증 방법 설정을 권장 합니다.

* 다시 설정 하는 데 필요한 수 이상으로 **등록 하는 데 필요한 인증 방법을** 설정 합니다. 여러 인증을 허용 하면 사용자가 다시 설정 해야 할 경우 유연성이 제공 됩니다.

* 조직에 적합 한 수준으로 **다시 설정 하는 데 필요한 방법의 수** 를 설정 합니다. 하나는 최소한의 마찰을 필요로 하 고 두 개는 보안 상태를 높일 수 있습니다. 

참고: 사용자는 [Azure Active Directory의 암호 정책 및 제한 사항](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy)에서 구성 된 인증 메서드를 사용 해야 합니다.

### <a name="registration-settings"></a>등록 설정

**사용자가 로그인 할 때 등록 해야** 함을 **예**로 설정 합니다. 이 설정을 사용 하려면 사용자가 로그인 할 때 등록 하 여 모든 사용자가 보호 되도록 해야 합니다.

조직에서 더 짧은 시간 프레임에 대 한 비즈니스 요구 사항이 없는 경우 사용자가 인증 정보를 **90** ~ **180** 일 사이에 **다시 확인 묻는 메시지를 표시 하기 전까지 남은 일 수** 를 설정 합니다.

### <a name="notifications-settings"></a>알림 설정

**사용자에 게 암호 재설정에 대 한 알림** 및 **다른 관리자가 자신의 암호를 예로 재설정 하면 모든 관리자** 에 게 알림을 구성 합니다. 둘 다에 대해 **예** 를 선택 하면 사용자가 암호를 재설정할 때 사용자가 인식 하 게 되므로 보안이 강화 됩니다. 또한 관리자가 암호를 변경 하는 경우 모든 관리자가 인식 하는지 확인 합니다. 사용자 또는 관리자가 알림을 받고 변경을 시작 하지 않은 경우 잠재적인 보안 문제를 즉시 보고할 수 있습니다.

### <a name="customization-settings"></a>사용자 지정 설정

문제를 경험 하는 사용자가 즉시 도움을 받을 수 있도록 기술 지원팀 전자 메일 또는 URL을 사용자 지정 하는 것이 중요 합니다. 이 옵션을 일반 기술 지원팀 전자 메일 주소나 사용자에 게 친숙 한 웹 페이지로 설정 합니다. 

자세한 내용은 [셀프 서비스 암호 재설정을 위한 AZURE AD 기능 사용자 지정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)을 참조 하세요.

### <a name="password-writeback"></a>암호 쓰기 저장

**비밀 번호 쓰기 저장** 은 [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-hybrid-identity) 에서 사용 하도록 설정 되며 클라우드의 암호 재설정을 실시간으로 기존 온-프레미스 디렉터리에 다시 기록 합니다. 자세한 내용은 [암호 쓰기 저장 (Writeback](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) ) 이란?을 참조 하세요.

다음 설정을 권장 합니다.

* **온-프레미스 AD에 암호 쓰기** 를 **예**로 설정 해야 합니다. 
* **사용자가 암호를 다시 설정 하지 않고 계정을 잠금 해제할 수 있도록 허용** 을 **예**로 설정 합니다.

기본적으로 Azure AD는 암호 재설정을 수행할 때 계정을 잠금 해제합니다.

### <a name="administrator-password-setting"></a>관리자 암호 설정

관리자 계정에는 상승 된 권한이 있습니다. 온-프레미스 엔터프라이즈 또는 도메인 관리자는 SSPR를 통해 암호를 재설정할 수 없습니다. 온-프레미스 관리자 계정에는 다음과 같은 제한 사항이 있습니다.

* 는 자신의 온-프레미스 환경 에서만 암호를 변경할 수 있습니다.
* 암호를 재설정 하는 방법으로 본인 확인 질문 및 답변을 사용할 수 없습니다.

온-프레미스 Active Directory admin 계정을 Azure AD와 동기화 하지 않는 것이 좋습니다.

### <a name="environments-with-multiple-identity-management-systems"></a>여러 id 관리 시스템을 사용 하는 환경

일부 환경에는 여러 id 관리 시스템이 있습니다. Oracle AM 및 SiteMinder와 같은 온-내부 id 관리자는 암호에 대해 AD와 동기화 해야 합니다. Microsoft Identity Manager (MIM)와 함께 PCNS (암호 변경 알림 서비스)와 같은 도구를 사용 하 여이 작업을 수행할 수 있습니다. 더 복잡 한이 시나리오에 대 한 정보를 찾으려면 [도메인 컨트롤러에 MIM 암호 변경 알림 서비스 배포](https://docs.microsoft.com/microsoft-identity-manager/deploying-mim-password-change-notification-service-on-domain-controller)문서를 참조 하세요.

## <a name="plan-testing-and-support"></a>테스트 및 지원 계획

조직 전체를 통해 초기 파일럿 그룹에서 배포의 각 단계에서 결과가 예상 대로 작동 하는지 확인 합니다.

### <a name="plan-testing"></a>테스트 계획

배포가 예상 대로 작동 하도록 하려면 테스트 사례 집합을 계획 하 여 구현 유효성을 검사 해야 합니다. 테스트 사례를 평가 하려면 암호를 사용 하는 비관리자 테스트 사용자가 필요 합니다. 사용자를 만들어야 하는 경우 [Azure Active Directory에 새 사용자 추가](https://docs.microsoft.com/azure/active-directory/add-users-azure-active-directory)를 참조 하세요.

다음 표에서는 정책에 따라 조직의 예상 결과를 문서화 하는 데 사용할 수 있는 유용한 테스트 시나리오를 제공 합니다.
<br>


| 비즈니스 사례| 예상 결과 |
| - | - |
| SSPR 포털은 회사 네트워크 내에서 액세스할 수 있습니다.| 조직에서 결정 |
| SSPR 포털은 회사 네트워크 외부에서 액세스할 수 있습니다.| 조직에서 결정 |
| 사용자가 암호 재설정을 사용 하도록 설정 되지 않은 경우 브라우저에서 사용자 암호 다시 설정| 사용자가 암호 재설정 흐름에 액세스할 수 없습니다. |
| 사용자가 암호 재설정에 등록 하지 않은 경우 브라우저에서 사용자 암호 다시 설정| 사용자가 암호 재설정 흐름에 액세스할 수 없습니다. |
| 사용자가 암호 재설정 등록을 수행 하도록 강제 적용 하는 경우 로그인| 사용자에 게 보안 정보를 등록 하 라는 메시지를 표시 합니다. |
| 암호 재설정 등록이 완료 되 면 사용자 로그인| 사용자에 게 보안 정보를 등록 하 라는 메시지를 표시 합니다. |
| 사용자에 게 라이선스가 없으면 SSPR 포털에 액세스할 수 있습니다.| 액세스 가능 |
| Windows 10 Azure AD 조인 또는 하이브리드 Azure AD 조인 장치 잠금 화면에서 사용자 암호 다시 설정| 사용자가 암호를 재설정할 수 있음 |
| SSPR 등록 및 사용 현황 데이터는 거의 실시간으로 관리자에 게 제공 됩니다.| 감사 로그를 통해 사용할 수 있습니다. |

[AZURE AD 셀프 서비스 암호 재설정 파일럿 롤업을](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot)참조 하 여 수행할 수도 있습니다. 이 자습서에서는 조직에서 SSPR의 파일럿 롤아웃을 사용 하도록 설정 하 고 관리자가 아닌 계정을 사용 하 여 테스트 합니다.

### <a name="plan-support"></a>지원 계획

SSPR는 일반적으로 사용자 문제를 만들지 않지만, 발생할 수 있는 문제를 해결 하기 위해 지원 직원을 준비 하는 것이 중요 합니다. 관리자는 Azure AD 포털을 통해 최종 사용자에 대 한 암호를 다시 설정할 수 있지만 셀프 서비스 지원 프로세스를 통해 문제를 해결 하는 것이 좋습니다.

지원 팀의 성공을 사용 하도록 설정 하려면 사용자가 받은 질문에 따라 FAQ를 만들 수 있습니다. 다음은 몇 가지 예입니다.

| 시나리오| 설명 |
| - | - |
| 사용자에 게 사용 가능한 등록 된 인증 방법이 없습니다.| 사용자가 자신의 암호를 재설정 하려고 하지만 등록 한 인증 방법이 없습니다. (예: 집에서 휴대폰을 사용 하 고 전자 메일에 액세스할 수 없음) |
| 사용자가 office 또는 휴대폰에서 텍스트를 받지 않거나 전화를 받지 않습니다.| 사용자가 텍스트 또는 호출을 통해 id를 확인 하려고 하지만 텍스트/호출을 받지 않습니다. |
| 사용자가 암호 재설정 포털에 액세스할 수 없음| 사용자가 암호를 재설정 하려고 하지만 암호 재설정에 사용할 수 없으며,이 페이지에 액세스 하 여 암호를 업데이트할 수 없습니다. |
| 사용자가 새 암호를 설정할 수 없음| 사용자가 암호 다시 설정 흐름 중에 확인을 완료 하지만 새 암호를 설정할 수는 없습니다. |
| 사용자가 Windows 10 장치에서 암호 재설정 링크를 표시 하지 않음| 사용자가 Windows 10 잠금 화면에서 암호를 재설정 하려고 하지만 장치가 Azure AD에 가입 되어 있지 않거나 Intune 장치 정책이 사용 하도록 설정 되어 있지 않습니다. |

### <a name="plan-roll-back"></a>요금제 롤백

배포를 롤백하려면:

* 단일 사용자의 경우 보안 그룹에서 사용자를 제거 합니다. 

* 그룹의 경우 SSPR 구성에서 그룹을 제거 합니다.

* Everyone의 경우 Azure AD 테넌트에 대해 SSPR를 사용 하지 않도록 설정 합니다.

## <a name="deploy-sspr"></a>SSPR 배포

를 배포 하기 전에 다음을 수행 했는지 확인 합니다.

1. [통신 계획](#plan-communications)실행을 만들고 시작 했습니다.

1. 적절 한 [구성 설정이](#plan-configuration)결정 되었습니다.

1. [파일럿](#plan-a-pilot) 및 프로덕션 환경에 대 한 사용자 및 그룹을 식별 합니다.

1. 등록 및 셀프 서비스에 대 한 [구성 설정을 확인 했습니다](#plan-configuration) .

1. 하이브리드 환경이 있는 경우 [비밀 번호 쓰기 저장을 구성](#password-writeback) 했습니다.


**이제 SSPR을 배포할 준비가 되었습니다.**

다음 영역을 구성 하는 방법에 대 한 전체 단계별 지침은 [셀프 서비스 암호 재설정 사용](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot#enable-self-service-password-reset) 을 참조 하세요.

1. [인증 방법](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

1. [등록 설정](https://docs.microsoft.com/azure/active-directory/authentication/concept-registration-mfa-sspr-combined)

1. [알림 설정](#notifications-settings)

1. [사용자 지정 설정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

1. [온-프레미스 통합](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-writeback)

### <a name="enable-sspr-in-windows"></a>Windows에서 SSPR 사용
Windows 7, 8, 8.1 및 10을 실행 하는 컴퓨터의 경우 [사용자가 windows 로그인 화면에서 자신의 암호를 재설정할 수 있도록 설정할](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-windows) 수 있습니다.

## <a name="manage-sspr"></a>SSPR 관리

Azure AD는 감사 및 보고서를 통해 SSPR 성능에 대 한 추가 정보를 제공할 수 있습니다.

### <a name="password-management-activity-reports"></a>암호 관리 활동 보고서 

Azure Portal에서 미리 작성 된 보고서를 사용 하 여 SSPR 성능을 측정할 수 있습니다. 적절히 라이선스를 받은 경우 사용자 지정 쿼리를 만들 수도 있습니다. 자세한 내용은 [AZURE AD 암호 관리에 대 한 보고 옵션](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-reporting) 을 참조 하세요.

> [!NOTE]
>  사용자 [는 전역 관리자](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)여야 하며 조직에 대해이 데이터를 수집 하기 위해 옵트인 해야 합니다. 옵트인 (opt in) 하려면 Azure Portal에서 한 번 이상 보고 탭 또는 감사 로그를 방문 해야 합니다. 그때까지 데이터는 조직에 대해 수집 되지 않습니다.

등록 및 암호 재설정에 대 한 감사 로그는 30 일 동안 사용할 수 있습니다. 회사 내의 보안 감사에 더 긴 보존이 필요한 경우 로그를 내보내고 [Azure 센티널](https://docs.microsoft.com/azure/sentinel/connect-azure-active-directory), Splunk 또는 arcsight와 같은 siem 도구로 사용 해야 합니다.

![SSPR 보고 스크린샷](./media/howto-sspr-deployment/sspr-reporting.png)

### <a name="authentication-methods--usage-and-insights"></a>인증 방법-사용 및 정보

[Usage 및 insights](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights) 를 사용 하면 Azure MFA 및 SSPR와 같은 기능의 인증 방법이 조직에서 작동 하는 방식을 이해할 수 있습니다. 이 보고 기능은 사용자가 등록 하는 방법 및 사용 방법에 대 한 이해를 조직에 제공 합니다.

### <a name="troubleshoot"></a>문제 해결

* [셀프 서비스 암호 재설정 문제 해결](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-troubleshoot) 을 참조 하세요. 

* [암호 관리 질문과 대답](https://docs.microsoft.com/azure/active-directory/authentication/active-directory-passwords-faq) 을 따릅니다. 

### <a name="helpful-documentation"></a>유용한 설명서

* [인증 방법이란?](https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods)

* [작동 방법: Azure AD 셀프 서비스 암호 재설정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks)

* [셀프 서비스 암호 재설정을 위한 Azure AD 기능 사용자 지정](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-customization)

* [Azure Active Directory에서 암호 정책 및 제한](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy) 문서를 검토하세요.

* [비밀번호 쓰기 저장이란?](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback)

## <a name="next-steps"></a>다음 단계

* SSPR 배포를 시작 하려면 [AZURE AD 셀프 서비스 암호 재설정 파일럿 롤아웃 완료](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-pilot) 를 참조 하세요.

* [Azure AD 암호 보호 구현 고려](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad)

* [Azure AD 스마트 잠금 구현 고려](https://docs.microsoft.com/azure/active-directory/authentication/howto-password-smart-lockout)