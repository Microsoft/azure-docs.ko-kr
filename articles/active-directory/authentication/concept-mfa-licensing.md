---
title: Azure Multi-Factor Authentication 버전 및 소비 계획
description: Azure Multi-factor Authentication 클라이언트 및 사용 가능한 다양 한 방법과 버전에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 01/24/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0d37b0cd042e6e11f8b2029b51c589c96154065
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/31/2020
ms.locfileid: "76909094"
---
# <a name="features-and-licenses-for-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication에 대 한 기능 및 라이선스

조직의 사용자 계정을 보호 하려면 multi-factor authentication을 사용 해야 합니다. 이 기능은 리소스에 대한 액세스 권한이 있는 계정에 특히 중요합니다. 기본 multi-factor authentication 기능은 Office 365 및 Azure Active Directory (Azure AD) 관리자가 추가 비용 없이 사용할 수 있습니다. 관리자의 기능을 업그레이드 하거나 multi-factor authentication을 나머지 사용자로 확장 하려는 경우 여러 가지 방법으로 Azure Multi-Factor Authentication를 구매할 수 있습니다.

> [!IMPORTANT]
> 이 문서에서는 Azure Multi-Factor Authentication의 사용을 허가 하 고 사용할 수 있는 다양 한 방법을 자세히 설명 합니다. 가격 책정 및 청구에 대 한 자세한 내용은 [Azure Multi-Factor Authentication 가격 책정 페이지](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)를 참조 하세요.

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication의 사용 가능한 버전

Azure Multi-Factor Authentication는 조직의 요구에 따라 몇 가지 방법으로 사용 하 고 사용이 허가 될 수 있습니다. 현재 azure AD, Office 365, EMS 또는 Microsoft 365 라이선스에 따라 Azure Multi-Factor Authentication를 사용 하도록 이미 자격이 있을 수 있습니다. 다음 표에서는 Azure Multi-Factor Authentication 및 각 기능에 대 한 몇 가지 기능 및 사용 사례를 가져오는 다양 한 방법을 자세히 설명 합니다.

| 사용자 인 경우 | 기능 및 사용 사례 |
| --- | --- |
| EMS 또는 Microsoft 365 E3 및 E5 | EMS E3 또는 Microsoft 365 E3 (EMS 및 Office 365 포함)에는 Azure AD Premium P1이 포함 됩니다. EMS E5 또는 Microsoft 365 E5는 Azure AD Premium P2를 포함 합니다. 다음 섹션에서 설명한 것과 동일한 조건부 액세스 기능을 사용 하 여 사용자에 게 multi-factor authentication을 제공할 수 있습니다. |
| Azure AD Premium P1 | [AZURE AD 조건부 액세스](../conditional-access/overview.md) 를 사용 하 여 비즈니스 요구 사항에 맞게 특정 시나리오 또는 이벤트 중에 사용자에 게 multi-factor authentication을 요청할 수 있습니다. |
| Azure AD Premium P2 | 가장 강력한 보안 위치 및 향상 된 사용자 환경을 제공 합니다. 사용자의 패턴에 맞게 조정 되 고 multi-factor authentication 프롬프트를 최소화 하는 Azure AD Premium P1 기능에 [위험 기반 조건부 액세스](../conditional-access/howto-conditional-access-policy-risk.md) 를 추가 합니다. |
| Office Premium, E3 또는 E5 | 모든 로그인 이벤트에 대해 Azure Multi-Factor Authentication는 모든 사용자에 대해 사용 하거나 사용 하지 않도록 설정 됩니다. 사용자의 하위 집합 또는 특정 시나리오 에서만 multi-factor authentication을 사용 하도록 설정할 수 있는 기능은 없습니다. Office 365 포털을 통해 관리할 수 있습니다. 향상 된 사용자 환경을 위해 Azure AD Premium P1 또는 P2로 업그레이드 하 고 조건부 액세스를 사용 합니다. 자세한 내용은 [multi-factor authentication을 사용 하 여 Office 365 리소스 보호](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6)를 참조 하세요. |
| Azure AD Free | 인증 요청을 수행할 때마다 [보안 기본값](../fundamentals/concept-fundamentals-security-defaults.md) 을 사용 하 여 모든 사용자에 대해 multi-factor authentication을 사용 하도록 설정할 수 있습니다. 활성화 된 사용자 또는 시나리오를 세부적으로 제어할 수는 없지만 추가 보안 단계를 제공 합니다.<br /> 보안 기본값을 사용 하 여 모든 사용자에 대해 multi-factor authentication을 사용 하도록 설정 하는 경우에도 *AZURE AD 전역 관리자* 역할이 할당 된 사용자는 multi-factor authentication을 사용 하도록 구성할 수 있습니다. 무료 계층의이 기능을 사용 하면 중요 한 관리자 계정이 multi-factor authentication에 의해 보호 됩니다. |

## <a name="feature-comparison-of-versions"></a>버전 기능 비교

다음 표에서 다양한 버전의 Azure Multi-Factor Authentication에서 사용 가능한 기능의 목록을 제공합니다. 사용자 인증을 보호 하기 위한 요구 사항을 계획 하 고 해당 요구 사항을 충족 하는 방법을 결정 합니다. 예를 들어 Azure AD Free는 Azure Multi-Factor Authentication를 제공 하는 보안 기본값을 제공 하지만 휴대폰 통화 또는 SMS가 아닌 인증 프롬프트에는 모바일 authenticator 앱만 사용할 수 있습니다. 이 접근 방식은 사용자의 개인 장치에 모바일 인증 앱이 설치 되어 있는지 확인할 수 없는 경우에 제한이 있을 수 있습니다.

| 기능 | Azure AD Free-보안 기본값 | Azure AD Free-Azure AD 전역 관리자 | Office Premium, E3 또는 E5 | Azure AD Premium P1 또는 P2 |
| --- |:---:|:---:|:---:|:---:|
| MFA를 사용하여 Azure AD 관리자 계정 보호 | ● | ● (*AZURE AD 전역 관리자* 계정에만 해당) | ● | ● |
| 두 번째 단계로 모바일 앱 | ● | ● | ● | ● |
| 두 번째 단계로 전화 통화 | | ● | ● | ● |
| 두 번째 단계로 SMS | | ● | ● | ● |
| 확인 방법에 대한 관리자 제어 | | ● | ● | ● |
| 사기 경고 | | | | ● |
| MFA 보고서 | | | | ● |
| 전화 통화에 대한 사용자 지정 인사말 | | | | ● |
| 전화 통화에 대한 사용자 지정 발신자 번호 | | | | ● |
| 신뢰할 수 있는 IP | | | | ● |
| 신뢰할 수 있는 디바이스에 대한 MFA 기억 | | ● | ● | ● |
| 온-프레미스 애플리케이션에 대한 MFA | | | | ● |

> [!IMPORTANT]
> 2019 년 3 월을 기준으로 Azure AD Free/평가판 테넌트의 azure Multi-Factor Authentication 및 Azure 셀프 서비스 암호 재설정 사용자가 전화 통화 옵션을 더 이상 사용할 수 없습니다. SMS 메시지는 이러한 변경의 영향을 받지 않습니다. 전화 통화는 Azure AD Premium P1 또는 P2 테넌트 또는 사용 또는 Office Premium, E3 또는 E5의 사용자가 계속 사용할 수 있습니다.

## <a name="purchase-and-enable-azure-multi-factor-authentication"></a>Azure Multi-Factor Authentication 구매 및 사용

Azure Multi-Factor Authentication을 사용 하려면 적격 Azure AD 계층에 등록 하거나 구매 합니다. Azure AD는 무료, Office 365 apps 버전 (Office 365 Premium E3 또는 E5 고객용), Premium P1 및 Premium P2의 네 가지 버전으로 제공 됩니다.

Free 버전은 Azure 구독에 포함됩니다. 보안 기본값을 사용 하는 방법 또는 *AZURE AD 전역 관리자* 역할로 계정을 보호 하는 방법에 대 한 자세한 내용은 [아래 섹션](#azure-ad-free-tier) 을 참조 하세요.

Azure AD Premium 버전은 Microsoft 담당자, [오픈 볼륨 라이선스 프로그램](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx)및 [클라우드 솔루션 공급자 프로그램](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409)을 통해 사용할 수 있습니다. Azure 및 Office 365 구독자는 Azure Active Directory Premium P1 및 P2를 온라인으로 구입할 수도 있습니다. 구입 하려면 [로그인](https://portal.office.com/Commerce/Catalog.aspx) 하세요.

> [!IMPORTANT]
> 2018 년 9 월 1 일에 적용 되는 새 고객에 게는 소비 기반 라이선스를 더 이상 사용할 수 없습니다. 소비 기반 모델을 사용 하는 기존 고객은 활성화 된 사용자 당 또는 인증 당 청구 요금을 계속 사용할 수 있습니다.

필요한 Azure AD 계층을 구매한 후 [azure Multi-Factor Authentication를 계획 하 고 배포](howto-mfa-getstarted.md)합니다.

### <a name="azure-ad-free-tier"></a>Azure AD Free 계층

Azure AD Free 테넌트의 모든 사용자는 보안 기본값을 사용 하 여 Azure Multi-factor authentication을 사용할 수 있습니다. 이러한 보안 기본값은 로그인 할 때마다 모든 사용자에 대해 Azure Multi-factor authentication을 사용 하도록 설정 합니다. 모바일 인증 앱은 Azure AD Free 보안 기본값을 사용 하는 경우 Azure Multi-Factor Authentication에 사용할 수 있는 유일한 방법입니다.

* [Azure AD 보안 기본값에 대 한 자세한 정보](../fundamentals/concept-fundamentals-security-defaults.md)
* [Azure AD Free의 사용자에 대 한 보안 기본값 사용](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

모든 사용자 및 로그인 이벤트에 대해 Azure Multi-Factor Authentication를 사용 하도록 설정 하지 않으려면 대신 *AZURE AD 전역 관리자* 역할로 사용자 계정만 보호 하도록 선택할 수 있습니다. 이 방법은 중요 한 관리자 계정에 대 한 추가 인증 프롬프트를 제공 합니다. 사용 하는 계정 유형에 따라 다음 방법 중 하나를 사용 하 여 Azure Multi-Factor Authentication를 사용 하도록 설정 합니다.

* Microsoft 계정을 사용 하는 경우 [multi-factor authentication에 등록](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification)합니다.
* Microsoft 계정을 사용 하지 않는 경우 [AZURE AD의 사용자 또는 그룹에 대해 multi-factor authentication을 사용 하도록 설정](howto-mfa-userstates.md)합니다.

## <a name="next-steps"></a>다음 단계

비용에 대 한 자세한 내용은 [Azure Multi-Factor Authentication 가격 책정](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)을 참조 하세요.
