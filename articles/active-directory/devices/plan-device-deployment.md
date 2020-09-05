---
title: Azure Active Directory 장치 배포 계획
description: 조직의 요구를 충족 하는 Azure AD 장치 통합 전략을 선택 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3f17f6fd881243d91428c6025fd63469a38afa0f
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268811"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>Azure Active Directory 장치 배포 계획

이 문서를 통해 장치를 Azure AD와 통합 하는 방법을 평가 하 고, 구현 계획을 선택 하 고, 지원 되는 장치 관리 도구에 대 한 주요 링크를 제공할 수 있습니다.

사용자가 로그인 하는 장치의 가로입니다. 조직은 데스크톱, 노트북, 휴대폰, 태블릿 및 기타 장치를 제공할 수 있습니다. 사용자는 고유한 장치 배열을 가져오고 다양 한 위치에서 정보에 액세스할 수 있습니다. 이 환경에서 관리자 권한으로 작업은 모든 장치에서 조직 리소스를 안전 하 게 유지 하는 것입니다.

Azure AD (Azure Active Directory)를 사용 하면 조직에서 장치 id 관리로 이러한 목표를 충족할 수 있습니다. 이제 Azure AD에서 장치를 가져오고 [Azure Portal](https://portal.azure.com/)의 중앙 위치에서 장치를 제어할 수 있습니다. 이를 통해 통합 된 환경을 제공 하 고 보안을 강화 하 고 새 장치를 구성 하는 데 필요한 시간을 줄일 수 있습니다.

여러 가지 방법으로 장치를 Azure AD에 통합할 수 있습니다.

* Azure AD에 [장치를 등록할](concept-azure-ad-register.md) 수 있습니다.

* [장치](concept-azure-ad-join.md) 를 Azure AD에 가입 (클라우드 전용) 또는

* 온-프레미스 Active Directory 및 Azure AD의 장치 간에 [하이브리드 AZURE AD 조인을 만듭니다](concept-azure-ad-join-hybrid.md) . 

## <a name="learn"></a>배우기

시작 하기 전에 [장치 id 관리 개요](overview.md)에 대해 잘 알고 있는지 확인 합니다.

### <a name="benefits"></a>혜택

장치에 Azure AD id를 제공 하는 주요 이점은 다음과 같습니다.

* 생산성 향상 – Azure AD를 사용 하면 사용자가 온-프레미스 및 클라우드 리소스에 대 한 [원활한 로그온 (SSO)](./azuread-join-sso.md) 을 수행할 수 있으므로 어디에서 든 생산성을 높일 수 있습니다.

* 보안 강화 – Azure AD 장치를 사용 하 여 장치 또는 사용자의 id에 따라 리소스에 대 한 [조건부 액세스 (CA) 정책을](../conditional-access/require-managed-devices.md) 적용할 수 있습니다. CA 정책은 [Azure AD ID 보호](../identity-protection/overview-identity-protection.md)를 사용 하 여 추가 보호를 제공할 수 있습니다. Azure AD에 장치를 조인 하는 것은 [암호 없는 인증](../authentication/concept-authentication-passwordless.md) 전략으로 보안을 강화 하기 위한 필수 구성 요소입니다.

* 사용자 환경 개선 – Azure AD의 장치 id를 사용 하면 개인 및 회사 장치 모두에서 조직의 클라우드 기반 리소스에 쉽게 액세스할 수 있도록 사용자에 게 제공할 수 있습니다. 관리자는 모든 Windows 장치에서 통합 된 환경에 대 한 [Enterprise State Roaming](enterprise-state-roaming-overview.md) 를 사용 하도록 설정할 수 있습니다.

* 배포 및 관리 간소화 – 장치 id 관리를 사용 하면 [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot), [대량 프로 비전](/mem/intune/enrollment/windows-bulk-enroll)및 [셀프 서비스: OOBE (첫 실행 경험)](../user-help/user-help-join-device-on-network.md)를 통해 Azure AD로 장치를 가져오는 프로세스가 간소화 됩니다. [Microsoft Intune](/mem/intune/fundamentals/what-is-intune)와 같은 MDM (모바일 장치 관리) 도구 및 [Azure Portal](https://portal.azure.com/)에서 해당 id를 사용 하 여 이러한 장치를 관리할 수 있습니다.

### <a name="training-resources"></a>학습 리소스

비디오:  [장치 컨트롤을 사용한 조건부 액세스](https://youtu.be/NcONUf-jeS4)

Faq: [AZURE AD 장치 관리 faq](faq.md)  및 [설정 및 데이터 로밍 faq](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>배포 프로젝트 계획

사용자 환경에서 이 배포에 대한 전략을 결정하는 동안 조직의 요구 사항을 고려합니다.

### <a name="engage-the-right-stakeholders"></a>올바른 관련자 참여

기술 프로젝트가 실패하는 이유는 일반적으로 영향, 결과 및 책임에 대한 기대 수준이 일치하지 않기 때문입니다. 이러한 문제를 방지 하려면 [적절 한 관련자](https://aka.ms/deploymentplans) 를 사용 하 고 프로젝트의 관련자 역할을 잘 이해 하 고 있어야 합니다. 

이 계획의 경우 목록에 다음 관련자를 추가 합니다.

| 역할| 설명 |
| - | - |
| 장치 관리자| 요금제가 조직의 장치 요구 사항을 충족 하는지 확인할 수 있는 장치 팀의 담당자입니다. |
| 네트워크 관리자| 네트워크 요구 사항을 충족 하는지 확인할 수 있는 네트워크 팀의 담당자입니다. |
| 장치 관리 팀| 장치 인벤토리를 관리 하는 팀입니다. |
| OS 특정 관리자 팀| 특정 OS 버전을 지원 하 고 관리 하는 팀입니다. 예를 들어 Mac 또는 iOS 중심의 팀이 있을 수 있습니다. |

### <a name="plan-communications"></a>통신 계획

통신은 새 서비스의 성공에 대단히 중요합니다. 사용자의 경험이 어떻게 변화하고 언제 변할 것인지, 문제가 발생할 경우 지원을 받는 방법에 대해 사용자와 사전에 소통합니다.

### <a name="plan-a-pilot"></a>파일럿 계획

통합 방법의 초기 구성은 테스트 환경에 있거나 작은 테스트 장치 그룹을 사용 하는 것이 좋습니다. [파일럿에 대한 모범 사례](../fundamentals/active-directory-deployment-plans.md)를 참조하세요.

하이브리드 Azure AD 조인 배포는 간단 하며, 최종 사용자 조치가 필요 없는 관리자 작업의 100%입니다. 전체 조직에서 한 번에 사용 하도록 설정 하기 전에 [하이브리드 AZURE AD 조인에 대 한 제어 된 유효성 검사](hybrid-azuread-join-control.md) 를 수행할 수 있습니다.

## <a name="choose-your-integration-methods"></a>통합 방법 선택

조직에서는 단일 Azure AD 테 넌 트에서 여러 장치 통합 방법을 사용할 수 있습니다. 목표는 Azure AD에서 장치를 안전 하 게 관리 하는 데 적합 한 방법을 선택 하는 것입니다. 소유권, 장치 유형, 기본 대상 그룹 및 조직의 인프라를 포함 하 여이 결정을 내리는 많은 매개 변수가 있습니다.

다음 정보는 사용할 통합 방법을 결정 하는 데 도움이 될 수 있습니다.

### <a name="decision-tree-for-devices-integration"></a>장치 통합에 대 한 의사 결정 트리

이 트리를 사용 하 여 조직에서 소유 하는 장치에 대 한 옵션을 결정할 수 있습니다. 

> [!NOTE]
> 개인 또는 BYOD (사용자 소유 장치) 시나리오는이 다이어그램에 표시 되지 않습니다. Azure AD 등록이 항상 발생 합니다.

 ![의사 결정 트리](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>비교 행렬

iOS 및 Android 장치는 Azure AD만 등록할 수 있습니다. 다음 표에서는 Windows 클라이언트 장치에 대 한 높은 수준의 고려 사항을 보여 줍니다. 개요로 사용 하 고 다양 한 통합 방법을 자세히 살펴보세요.

| 고려 사항 | Azure AD 등록| Azure AD 조인| 하이브리드 Azure AD 조인 |
| - | - | - | - |
| **클라이언트 운영 체제**| | |  |
| Windows 10 디바이스| ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png) |
| Windows 하위 수준 장치 (Windows 8.1 또는 Windows 7)| | | ![선택](./media/plan-device-deployment/check.png) |
|**로그인 옵션**| | |  |
| 최종 사용자 로컬 자격 증명| ![선택](./media/plan-device-deployment/check.png)| |  |
| 암호| ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png) |
| 디바이스 PIN| ![선택](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![선택](./media/plan-device-deployment/check.png)| |  |
| 비즈니스용 Windows Hello| | ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png) |
| FIDO 2.0 보안 키| | ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator 앱 (passwordless)| ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png) |
|**주요 기능**| | |  |
| 클라우드 리소스에 대 한 SSO| ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png) |
| 온-프레미스 리소스에 대한 SSO| | ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png) |
| 조건부 액세스 <br> (장치를 규격으로 표시 해야 함) <br> (MDM에서 관리 해야 함)| ![선택](./media/plan-device-deployment/check.png) | ![선택](./media/plan-device-deployment/check.png)|![선택](./media/plan-device-deployment/check.png) |
조건부 액세스 <br>(하이브리드 Azure AD 조인 장치 필요)| | | ![선택](./media/plan-device-deployment/check.png)
| Windows 로그인 화면에서 셀프 서비스 암호 재설정| | ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png) |
| Windows hello PIN 다시 설정| | ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png) |
| 장치 간 엔터프라이즈 상태 로밍| | ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD 등록 

등록 된 장치는 종종 [Microsoft Intune](/mem/intune/enrollment/device-enrollment)로 관리 됩니다. 장치는 운영 체제에 따라 다양 한 방법으로 Intune에 등록 됩니다. 

Azure AD 등록 장치는 클라우드 리소스에 대 한 SSO에 대 한 사용자 고유의 장치 (BYOD) 및 회사 소유 장치를 제공 하도록 지원 합니다. 리소스에 대 한 액세스는 장치 및 사용자에 게 적용 되는 Azure AD [CA 정책을](../conditional-access/require-managed-devices.md) 기반으로 합니다.

### <a name="registering-devices"></a>디바이스 등록

등록 된 장치는 종종 [Microsoft Intune](/mem/intune/enrollment/device-enrollment)로 관리 됩니다. 장치는 운영 체제에 따라 다양 한 방법으로 Intune에 등록 됩니다. 

BYOD 및 회사 소유의 모바일 장치는 회사 포털 앱을 설치 하는 사용자가 등록 합니다.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

조직에 가장 적합 한 옵션을 장치에 등록 하는 경우 다음 리소스를 참조 하세요.

* [AZURE AD 등록 장치](concept-azure-ad-register.md)에 대 한 개요입니다.

* 이 최종 사용자 설명서는 [조직의 네트워크에 개인 장치를 등록](../user-help/user-help-register-device-on-network.md)하는 방법에 대해 설명 합니다.

## <a name="azure-ad-join"></a>Azure AD 조인

Azure AD 조인을 사용 하면 Windows를 사용 하 여 클라우드 우선 모델로 전환할 수 있습니다. 장치 관리를 현대화 하 고 장치 관련 IT 비용을 줄이는 계획 이라면 매우 편리 합니다. Azure AD 조인은 Windows 10 장치 에서만 작동 합니다. 새 장치에 대 한 첫 번째 선택 사항으로 간주 합니다.

그러나 Azure AD 조인 장치는 조직의 네트워크에 있는 경우 온 [-프레미스 리소스에 SSO를 사용할 수 있으며](azuread-join-sso.md) , 파일, 인쇄 및 기타 응용 프로그램과 같은 온-프레미스 서버에 인증할 수 있습니다.

조직에 가장 적합 한 옵션을 선택 하는 경우 다음 리소스를 참조 하세요.

* [AZURE AD 가입 장치](concept-azure-ad-join.md)에 대 한 개요입니다.

* [AZURE AD 조인 구현 계획](azureadjoin-plan.md)을 숙지 합니다.

### <a name="provisioning-azure-ad-join-to-your-devices"></a>장치에 Azure AD 조인 프로 비전

Azure AD 조인을 프로 비전 하려면 다음과 같은 방법을 사용할 수 있습니다.

* 셀프 서비스: [Windows 10 첫 실행 환경](azuread-joined-devices-frx.md)

Windows 10 Professional 또는 Windows 10 Enterprise가 디바이스에 설치된 경우 환경은 기본적으로 회사 소유의 디바이스에 대한 설정 프로세스로 지정됩니다.

* [Windows OOBE (Box Out Experience) 또는 Windows 설정](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [대량 등록](/mem/intune/enrollment/windows-bulk-enroll)

[이러한 접근 방식을](azureadjoin-plan.md)신중 하 게 비교한 후 배포 절차를 선택 합니다.

Azure AD 조인이 장치에 가장 적합 한 솔루션 이며 해당 장치가 이미 다른 상태에 있을 수 있습니다. 업그레이드 고려 사항은 다음과 같습니다.

| 현재 장치 상태| 원하는 장치 상태| 방법 |
| - | - | - |
| 온-프레미스 도메인에 가입 됨| Azure AD 조인| Azure AD에 가입 하기 전에 온-프레미스 도메인에서 장치 가입 해제 |
| 하이브리드 Azure AD 조인| Azure AD 조인| Azure AD에 가입 하기 전에 온-프레미스 도메인 및 Azure AD에서 장치 가입 해제 |
| Azure AD 등록| Azure AD 조인| Azure AD에 가입 하기 전에 장치 등록 취소 |


## <a name="hybrid-azure-ad-join"></a>하이브리드 Azure AD 조인

온-프레미스 Active Directory 환경이 있고 Active Directory 도메인에 가입 된 컴퓨터를 Azure AD에 가입 하려는 경우 하이브리드 Azure AD 조인을 사용 하 여이를 수행할 수 있습니다. Windows 현재 및 Windows 하위 수준 장치를 비롯 한 [광범위 한 windows 장치](hybrid-azuread-join-plan.md)를 지원 합니다.

대부분의 조직에는 도메인에 가입 된 장치가 이미 있고 SCCM (그룹 정책 또는 System Center Configuration Manager)을 통해 관리 됩니다. 이 경우 기존 투자를 활용 하면서 이점을 얻기 위해 하이브리드 Azure AD 조인을 구성 하는 것이 좋습니다.

하이브리드 Azure AD 조인이 조직에 가장 적합 한 옵션 인 경우 다음 리소스를 참조 하세요.

* [하이브리드 AZURE AD 가입 장치](concept-azure-ad-join-hybrid.md)에 대 한 개요입니다.

* [하이브리드 AZURE AD 조인 구현](hybrid-azuread-join-plan.md) 계획을 숙지 합니다.

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>장치에 하이브리드 Azure AD 조인 프로 비전

[Id 인프라를 검토](hybrid-azuread-join-plan.md)합니다. Azure AD Connect는 다음에 대 한 하이브리드 Azure AD 조인을 구성 하는 마법사를 제공 합니다.

* [페더레이션된 도메인](hybrid-azuread-join-federated-domains.md)

* [관리되는 도메인](hybrid-azuread-join-managed-domains.md)

필요한 Azure AD Connect 버전을 설치할 수 없는 경우 [하이브리드 AZURE AD 조인을 수동으로 구성 하는 방법](hybrid-azuread-join-manual.md)을 참조 하세요. 

> [!NOTE] 
> 온-프레미스 도메인에 가입 된 Windows 10 장치는 기본적으로 하이브리드 Azure AD에 조인 되기 위해 Azure AD에 자동 가입을 시도 합니다. 이는 올바른 환경을 설정 하는 경우에만 성공 합니다 repair 둘. 

하이브리드 Azure AD 조인이 장치에 가장 적합 한 솔루션 이며 해당 장치가 이미 다른 상태에 있을 수 있음을 확인할 수 있습니다. 업그레이드 고려 사항은 다음과 같습니다.

| 현재 장치 상태| 원하는 장치 상태| 방법 |
| - | - | - |
| 온-프레미스 도메인 가입| 하이브리드 Azure AD 조인| Azure AD connect 또는 AD FS를 사용 하 여 Azure에 가입 |
| 온-프레미스 작업 그룹 조인 또는 신규| 하이브리드 Azure AD 조인| [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)에서 지원 됩니다. 그렇지 않으면 하이브리드 Azure AD 조인 전에 장치가 온-프레미스 도메인에 가입 되어 있어야 합니다. |
| Azure AD 가입| 하이브리드 Azure AD 조인| 온-프레미스 작업 그룹 또는 새 상태에 배치 하는 Azure AD의 가입을 취소 합니다. |
| Azure AD 리디렉션합니다| 하이브리드 Azure AD 조인| Windows 버전에 따라 다릅니다. [이러한 고려 사항을 참조](hybrid-azuread-join-plan.md)하세요. |

## <a name="manage-your-devices"></a>디바이스 관리

장치를 Azure AD에 등록 하거나 가입한 후에는 [Azure Portal](https://portal.azure.com/) 를 중앙 위치로 사용 하 여 장치 id를 관리 합니다. Azure Active Directory 장치 페이지에서 다음을 수행할 수 있습니다.

* [디바이스 설정 구성](device-management-azure-portal.md#configure-device-settings)
* Windows 장치를 관리 하려면 로컬 관리자 여야 합니다. [AZURE ad는 AZURE ad 조인 장치에 대해이 멤버 자격을 업데이트](assign-local-admin.md)하 고, 장치 관리자 역할을 사용 하 여 연결 된 모든 장치에 관리자로 자동으로 추가 합니다.

[오래 된 장치를 관리](manage-stale-devices.md)하 여 환경을 정리 하 고 현재 장치를 관리 하는 리소스에 집중 하도록 합니다.

* [장치 관련 감사 로그 검토](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>지원 되는 장치 관리 도구

관리자는 추가 장치 관리 도구를 사용 하 여 등록 된 장치 및 조인 된 장치를 안전 하 게 제어할 수 있습니다. 이러한 도구는 스토리지 암호화, 암호 복잡성, 소프트웨어 설치 및 소프트웨어 업데이트를 요구하는 것처럼 조직에 필요한 구성을 적용할 수 있는 수단을 제공합니다. 

통합 장치에 대해 지원 되는 플랫폼 및 지원 되지 않는 플랫폼 검토:

| 디바이스 관리 도구| Azure AD 등록| Azure AD 조인| 하이브리드 Azure AD 조인|
| - | - | - | - |
| [MDM (모바일 장치 관리) ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>예: Microsoft Intune| ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png)|  |
| [Microsoft Intune 및 Microsoft 끝점을 사용 하 여 공동 관리 Configuration Manager](/mem/configmgr/comanage/overview) <br>(Windows 10 이상)| | ![선택](./media/plan-device-deployment/check.png)| ![선택](./media/plan-device-deployment/check.png)|  |
| [그룹 정책](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Windows에만 해당)| | | ![선택](./media/plan-device-deployment/check.png)|  |



 등록 된 iOS 또는 Android 장치에 대 한 장치 관리를 사용 하거나 사용 하지 않고 [MAM (모바일 응용 프로그램 관리)을 Microsoft Intune](/mem/intune/apps/app-management) 하는 것이 좋습니다.

 또한 관리자는 조직에서 Windows 운영 체제를 호스팅하는 [VDI (가상 데스크톱 인프라) 플랫폼을 배포](howto-device-identity-virtual-desktop-infrastructure.md) 하 여 관리를 간소화 하 고 리소스의 통합 및 중앙 집중화를 통해 비용을 절감할 수 있습니다. 

### <a name="troubleshoot-device-identities"></a>디바이스 ID 문제 해결

* [Dsregcmd.exe 명령을 사용 하 여 장치 문제 해결](troubleshoot-device-dsregcmd.md)

* [Azure Active Directory에서 엔터프라이즈 상태 로밍 설정 문제 해결](enterprise-state-roaming-troubleshooting.md)

도메인 가입 Windows 디바이스에 대한 하이브리드 Azure AD 조인을 완료할 때 문제가 발생하면 다음을 참조하세요.

* [Windows 최신 디바이스의 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-current.md)

* [Windows 하위 수준 장치에 대 한 하이브리드 Azure AD 조인 문제 해결](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>다음 단계

* [Azure AD 조인 구현 계획](azureadjoin-plan.md)
* [하이브리드 Azure AD 조인 구현 계획](hybrid-azuread-join-plan.md)
* [디바이스 ID 관리](device-management-azure-portal.md)