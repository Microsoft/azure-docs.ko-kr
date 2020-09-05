---
title: Azure AD에서 응급 액세스 관리자 계정 관리 - Azure AD | Microsoft Docs
description: 이 문서에서는 응급 액세스 계정을 사용하여 Azure AD(Azure Active Directory) 조직에서 실수로 계정이 잠기는 것을 방지하는 방법을 설명합니다.
services: active-directory
author: markwahl-msft
manager: daveba
ms.author: curtand
ms.date: 11/08/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: a98ff68500593c644e6f0fa5eacb338ab90f5604
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "88795382"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Azure AD에서 응급 액세스 계정 관리

관리자는 다른 사용자의 계정에 로그인하거나 이 계정을 활성화 수 없기 때문에 Azure AD(Azure Active Directory) 조직에서 실수로 계정이 잠기는 것을 방지하는 것이 중요합니다. 조직에서 둘 이상의 응급 액세스 계정을 만들어 우발적인 관리 액세스 부족에 따른 영향을 완화할 수 있습니다.

응급 액세스 계정에는 높은 권한이 부여되고 특정 개인에게 할당되지 않습니다. 응급 액세스 계정은 일반 관리 계정을 사용할 수 없는 '비상' 시나리오의 긴급한 상황으로 제한됩니다. 응급 계정 사용은 절대적으로 필요한 경우에만 사용한다는 목표를 유지하는 것이 좋습니다.

이 문서에서는 Azure AD에서 응급 액세스 계정을 관리하는 지침을 제공합니다.

## <a name="why-use-an-emergency-access-account"></a>응급 액세스 계정은 왜 사용하나요?

조직은 다음과 같은 상황에서 응급 액세스 계정을 사용해야 할 수도 있습니다.

- 사용자 계정이 페더레이션되고, 페더레이션이 현재 셀 네트워크 중단 또는 ID 공급자 중단으로 인해 사용할 수 없습니다. 예를 들어, 사용자 환경에서 ID 공급자 호스트가 중단된 경우 사용자는 Azure AD가 해당 ID 공급자로 리디렉션할 때 로그인하지 못할 수 있습니다.
- 관리자가 Azure Multi-Factor Authentication을 통해 등록되었고 관리자의 개별 디바이스를 모두 사용할 수 없거나 서비스를 사용할 수 없습니다. 사용자는 역할을 활성화하는 Multi-Factor Authentication을 완료하지 못할 수도 있습니다. 예를 들어 셀 네트워크 장애로 인해 해당 디바이스에 대해 등록된 단 두 개의 인증 메커니즘인 수신 전화에 응답 및 문자 메시지 수신이 불가능할 수 있습니다.
- 최근 글로벌 관리자 액세스 권한이 있는 사용자가 조직을 떠났습니다. Azure AD는 마지막 글로벌 관리자 계정이 삭제되지 않도록 할 수 있으나, 계정이 온-프레미스에서 삭제되거나 비활성화되는 것은 방지할 수 없습니다. 각 상황에서 조직은 계정을 복구하지 못할 수 있습니다.
- 자연 재해 비상 사태와 같이 예기치 않은 상황이 발생하여 휴대폰이나 기타 네트워크를 사용하지 못하는 경우. 

## <a name="create-emergency-access-accounts"></a>응급 액세스 계정 만들기

두 개 이상의 응급 액세스 계정을 만듭니다. 이러한 계정은 \*.onmicrosoft.com 도메인을 사용하고, 온-프레미스 환경에서 페더레이션되거나 동기화되지 않은 클라우드 전용 계정이어야 합니다.

이러한 계정을 구성할 때는 다음 요구 사항이 충족되어야 합니다.

- 응급 액세스 계정은 조직의 개별 사용자와 연결되지 않아야 합니다. 직원이 제공한 휴대폰, 개별 직원이 가지고 다니는 하드웨어 토큰 또는 기타 직원 전용 자격 증명에 계정이 연결되지 않아야 합니다. 이 예방 조치는 개별 직원이 자격 증명이 필요한 경우 접근할 수 없는 인스턴스를 포함합니다. 등록된 모든 디바이스가 Azure AD와 통신하는 여러 수단이 있는 안전하고 알려진 위치에 있는지 확인하는 것이 중요합니다.
- 응급 액세스 계정에 사용되는 인증 메커니즘은 다른 응급 액세스 계정을 비롯한 기타 관리 계정에 사용되는 메커니즘과 구분되어야 합니다.  예를 들어 온-프레미스 MFA를 통해 일반 관리자 로그인이 수행되는 경우에는 Azure MFA가 다른 메커니즘이 될 수 있습니다.  하지만 Azure MFA가 관리 계정에 대한 인증의 기본 부분인 경우에는 다른 방법(예: [사용자 지정 컨트롤](../conditional-access/controls.md)을 통해 타사 MFA 공급자로 조건부 액세스 사용)을 고려하는 것이 좋습니다.
- 디바이스나 자격 증명이 만료되지 않아야 하고 사용 부족으로 인해 자동으로 정리되는 범위에 속하지 않아야 합니다.  
- 응급 액세스 계정에 대한 글로벌 관리자 역할 할당은 영구적으로 설정해야 합니다. 

### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>전화 기반 MFA에서 하나 이상의 계정 제외

암호 손상으로 인한 공격 위험을 줄이기 위해서, Azure AD에서는 모든 개별 사용자에 대해 MFA을 요구하는 것이 좋습니다. 이 그룹에는 계정이 공격에 노출되면 상당한 영향을 미치는 관리자 및 기타 모든 사용자(예: 회계 담당자)가 포함됩니다.

하지만 응급 액세스 계정 중 하나 이상은 응급 계정이 아닌 기타 계정과 MFA 메커니즘이 달라야 합니다. 여기에는 타사 MFA 솔루션이 포함됩니다. Azure AD 및 기타 연결된 SaaS(software as a service)의 [모든 관리자에게 MFA](../authentication/howto-mfa-userstates.md)를 요구하는 조건부 액세스 정책이 있는 경우에는 이러한 요구 사항에서 응급 액세스 계정을 제외하고 대신 다른 메커니즘을 구성해야 합니다. 또한 계정에 사용자별 MFA 정책이 없도록 해야 합니다.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>조건부 액세스 정책에서 하나 이상의 계정 제외

응급 상황에서 정책으로 인해 액세스 권한이 차단되어 문제를 해결하지 못하는 경우는 없어야 합니다. 따라서 하나 이상의 응급 액세스 계정을 모든 조건부 액세스 정책에서 제외시켜야 합니다.

## <a name="federation-guidance"></a>페더레이션 지침

AD Domain Services와 ADFS 또는 유사한 ID 공급자를 사용하여 Azure AD에 페더레이션하는 조직에 대한 추가 옵션은 해당 ID 공급자가 MFA 클레임을 제공할 수 있는 응급 액세스 계정을 구성하는 것입니다.  예를 들어, 응급 액세스 계정은 스마트 카드에 저장된 인증서와 키 쌍으로 지원할 수 있습니다.  사용자가 AD에서 인증되면 ADFS는 사용자가 MFA 요구 사항을 충족했음을 알려주며 Azure AD에 클레임을 제공할 수 있습니다.  이 방법을 사용하더라도 페더레이션을 설정할 수 없는 경우에는 조직에 클라우드 기반 응급 액세스 계정이 여전히 있어야 합니다. 

## <a name="store-account-credentials-safely"></a>계정 자격 증명을 안전하게 저장

조직은 응급 액세스 계정의 로그인 정보를 안전하게 유지하고 사용이 허가된 사람에게만 알려야 합니다. 일부 고객은 스마트 카드를 사용하고 일부는 암호를 사용합니다. 한 응급 액세스 계정에 대한 암호는 일반적으로 두세 개 부분으로 나뉘는데, 각기 다른 종이에 써서 각기 다른 위치에 있는 안전한 내화 금고에 보관합니다.

암호를 사용하는 경우에는 계정에 만료되지 않는 강력한 암호가 있어야 합니다. 이상적으로, 암호는 16자 이상이어야 하고 임의로 생성되어야 합니다.

## <a name="monitor-sign-in-and-audit-logs"></a>로그인 및 감사 로그 모니터링

조직은 응급 계정에서 로그인 및 감사 로그 작업을 모니터링 하고 다른 관리자에게 알림을 트리거해야 합니다. 비상 계정에서 작업을 모니터링하는 경우, 이러한 계정이 테스트 또는 실제 응급 상황에서만 사용되는지 확인할 수 있습니다. Azure Log Analytics를 사용하여 로그인 로그를 모니터링할 수 있으며, 비상 계정이 로그인할 때마다 관리자에게 이메일 및 SMS 경고를 트리거할 수 있습니다.

### <a name="prerequisites"></a>사전 요구 사항

1. Azure Monitor에 [Azure AD 로그인 로그를 전송](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)합니다.

### <a name="obtain-object-ids-of-the-break-glass-accounts"></a>비상 계정의 개체 ID 가져오기

1. 사용자 관리자 역할에 할당된 계정으로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **사용자**를 선택합니다.
1. 비상 계정을 검색하고 사용자의 이름을 선택합니다.
1. 개체 ID 특성을 복사하여 나중에 사용할 수 있도록 저장합니다.
1. 두 번째 비상 계정에 대해 이전 단계를 반복합니다.

### <a name="create-an-alert-rule"></a>경고 규칙 만들기

1. Azure Monitor의 모니터링 참가자 역할에 할당된 계정을 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. "**모든 서비스**를 선택하고 검색에 "로그 분석"을 입력한 다음, **Log Analytics 작업 영역**을 선택합니다.
1. 작업 영역을 선택합니다.
1. 작업 영역에서 **경고** > **새 경고 규칙**을 선택합니다.
    1. **리소스**에서 구독이 경고 규칙을 연결하려는 구독에 해당되는지 확인합니다.
    1. **조건**에서 **추가**를 선택합니다.
    1. **신호 이름**에서 **사용자 지정 로그 검색**을 선택합니다.
    1. **검색 쿼리**에서 다음 쿼리를 입력하여 두 개의 비상 계정에 대한 개체 ID를 삽입합니다.
        > [!NOTE]
        > 포함하려는 각 추가 비상 계정에 대해 다른 "or UserId == "ObjectGuid""를 쿼리에 추가합니다.

        ![경고 규칙에 비상 계정의 개체 ID를 추가](./media/directory-emergency-access/query-image1.png)

    1. **경고 논리**에서 다음을 입력합니다.

        - 기준: 결과의 수
        - 연산자: 초과
        - 임계값: 0

    1. **평가 기준**에서 쿼리를 실행할 기간에 대한 **기간(분)** 과 쿼리를 실행할 빈도에 대한 **빈도(분)** 를 각각 선택합니다. 빈도는 기간보다 작거나 같아야 합니다.

        ![경고 논리](./media/directory-emergency-access/alert-image2.png)

    1. **완료** 를 선택합니다. 이제 이 경고의 월별 예상 비용을 볼 수 있습니다.
1. 경고에서 알림을 받을 사용자의 작업 그룹을 선택합니다. 작업 그룹 하나를 만들려면 [작업 그룹 만들기](#create-an-action-group)를 참조하세요.
1. 작업 그룹의 멤버로 전송 되는 이메일 알림을 사용자 지정 하려면 **작업 사용자 지정**에서 작업을 선택합니다.
1. **경고 세부 정보**에서 경고 규칙 이름을 지정하고 설명(선택 사항)을 추가합니다.
1. 이벤트의 **심각도 수준**을 설정합니다. **중요(심각도 0)** 로 설정하는 것이 좋습니다.
1. **생성 시 규칙 사용**에서는 **예**로 설정된 상태로 둡니다.
1. 잠시 동안 경고를 해제하려면 **경고 표시 안 함** 확인란을 선택하고, 경고를 다시 표시하기 전에 대기 시간을 입력한 후 **저장**을 선택 합니다.
1. **경고 규칙 만들기**를 클릭합니다.

### <a name="create-an-action-group"></a>작업 그룹 만들기

1. **작업 그룹 만들기**를 선택합니다.

    ![알림 작업에 대한 작업 그룹 만들기](./media/directory-emergency-access/action-group-image3.png)

1. 작업 그룹 이름 및 짧은 이름을 입력합니다.
1. 구독 및 리소스 그룹을 확인합니다.
1. 작업 유형에서 **이메일/SMS/푸시/음성**을 선택합니다.
1. 작업 이름(예: **전역 관리자에게 알림**)을 입력합니다.
1. **작업 유형**을 **이메일/SMS/푸시/음성**으로 선택합니다.
1. **세부 정보 편집**을 선택하여 구성하려는 알림 방법을 선택하고 필요한 연락처 정보를 입력한 후 **확인**을 선택하여 세부 정보를 저장합니다.
1. 트리거할 추가 작업을 추가합니다.
1. **확인**을 선택합니다.

## <a name="validate-accounts-regularly"></a>정기적으로 계정 유효성 검사

응급 액세스 계정을 사용하고 응급 액세스 계정의 유효성을 검사하도록 직원을 교육할 때에는 최소한 다음 단계를 일정한 간격으로 수행하세요.

- 보안 모니터링 직원이 계정 검사 작업이 진행 중임을 알고 있는지 확인합니다.
- 이 계정을 사용할 수 있는 응급 비상 프로세스가 문서화되어 있고 최신 상태인지 확인합니다.
- 응급 상황 시, 이 단계를 수행해야 하는 관리자와 보안 담당자가 해당 프로세스에 대한 교육을 받도록 합니다.
- 응급 액세스 계정의 계정 로그인 정보(특히 암호)를 업데이트한 다음, 응급 액세스 계정이 로그인할 수 있고 관리 작업을 수행할 수 있는지 확인합니다.
- 개인 사용자의 디바이스 또는 개인 정보에 Multi-Factor Authentication 또는 SSPR(셀프 서비스 암호 재설정)을 등록하지 않았는지 확인합니다. 
- 계정이 디바이스에 대한 Multi-Factor Authentication에 등록된 경우에는, 로그인 또는 역할 활성화 중에 사용할 수 있도록, 응급 상황에서 디바이스를 사용해야 하는 모든 관리자가 디바이스에 액세스할 수 있어야 합니다. 또한 일반적인 오류 모드를 공유하지 않는 둘 이상의 네트워크 경로를 통해 디바이스가 통신할 수 있는지 확인합니다. 예를 들어 디바이스는 시설의 무선 네트워크 및 휴대 전화 공급자 네트워크 모두를 통해 인터넷과 통신할 수 있습니다.

다음 단계는 정기적인 간격으로 수행하고 주요 변경 사항이 있는 경우에도 수행해야 합니다.

- 최소 90일 간격
- 업무 변동, 사직 또는 신규 채용과 같이 최근 IT 직원에 변동이 있는 경우
- 조직에서 Azure AD 구독이 변경된 경우

## <a name="next-steps"></a>다음 단계

- [Azure AD에서 하이브리드 및 클라우드 배포를 위한 권한 있는 액세스 보안](directory-admin-roles-secure.md)
- [Azure AD를 사용하여 사용자를 추가](../fundamentals/add-users-azure-active-directory.md)하고 [새 사용자를 글로벌 관리자 역할에 할당](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- 아직 가입하지 않은 경우 [Azure AD Premium에 가입](../fundamentals/active-directory-get-started-premium.md)
- [사용자에 대해 2단계 인증을 요구하는 방법](../authentication/howto-mfa-userstates.md)
- Microsoft 365을 사용 하는 경우 [Microsoft 365에서 전역 관리자에 대 한 추가 보호를 구성](/office365/enterprise/protect-your-global-administrator-accounts)합니다.
- [ 관리자에 대한 액세스 검토를 시작](../privileged-identity-management/pim-how-to-start-security-review.md)하고 [기존 글로벌 관리자를 보다 구체적인 관리자 역할로 전환](directory-assign-admin-roles.md)