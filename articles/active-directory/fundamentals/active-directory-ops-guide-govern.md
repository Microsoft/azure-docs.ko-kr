---
title: Azure Active Directory 거 버 넌 스 작업 참조 가이드
description: 이 작업 참조 가이드에서는 거 버 넌 스 관리를 보호 하기 위해 수행 해야 하는 검사 및 작업을 설명 합니다.
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: 4826bcdc85e0c6189c51aa262014fe154bb479b1
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74535458"
---
# <a name="azure-active-directory-governance-operations-reference-guide"></a>Azure Active Directory 거 버 넌 스 작업 참조 가이드

[AZURE AD 작업 참조 가이드](active-directory-ops-guide-intro.md) 의이 섹션에서는 환경에 대 한 권한 없는 권한 및 권한 있는 id, 감사 및 제어 변경 사항을 평가 하 고 증명 하기 위해 수행 해야 하는 검사 및 작업에 대해 설명 합니다.

> [!NOTE]
> 이러한 권장 사항은 게시 날짜를 따라 최신 이지만 시간이 지남에 따라 변경 될 수 있습니다. 조직에서는 Microsoft 제품 및 서비스가 시간이 지남에 따라 발전 함에 따라 해당 관리 사례를 지속적으로 평가 해야 합니다.

## <a name="key-operational-processes"></a>주요 운영 프로세스

### <a name="assign-owners-to-key-tasks"></a>키 작업에 소유자 할당

Azure Active Directory를 관리 하려면 롤아웃 프로젝트에 포함 되지 않을 수 있는 주요 운영 작업 및 프로세스를 지속적으로 실행 해야 합니다. 환경 최적화를 위해 이러한 작업을 설정 하는 것도 중요 합니다. 핵심 작업과 권장 소유자는 다음과 같습니다.

| 작업 | 소유자 |
| :- | :- |
| SIEM 시스템에서 Azure AD 감사 로그 보관 | InfoSec 운영 팀 |
| 비준수로 관리 되는 응용 프로그램 검색 | IAM 운영 팀 |
| 응용 프로그램에 대 한 액세스를 정기적으로 검토 | InfoSec 아키텍처 팀 |
| 정기적으로 외부 id에 대 한 액세스 검토 | InfoSec 아키텍처 팀 |
| 권한 있는 역할이 있는 사용자를 정기적으로 검토 | InfoSec 아키텍처 팀 |
| 보안 게이트를 정의 하 여 권한 있는 역할 활성화 | InfoSec 아키텍처 팀 |
| 정기적으로 동의 부여 검토 | InfoSec 아키텍처 팀 |
| 조직의 직원을 기반으로 응용 프로그램 및 리소스에 대 한 카탈로그 및 액세스 패키지 디자인 | 앱 소유자 |
| 사용자를 할당 하 여 패키지에 액세스할 수 있도록 보안 정책 정의 | InfoSec 팀 + 앱 소유자 |
| 정책에 승인 워크플로가 포함 되어 있는 경우 워크플로 승인 정기적으로 검토 | 앱 소유자 |
| 액세스 검토를 사용 하 여 조건부 액세스 정책과 같은 보안 정책의 예외를 검토 합니다. | InfoSec 운영 팀 |

목록을 검토할 때 소유자가 없는 작업의 소유자를 할당 하거나 위의 권장 사항에 맞지 않는 소유자가 있는 작업에 대 한 소유권을 조정 해야 할 수 있습니다.

#### <a name="owner-recommended-reading"></a>소유자 권장 읽기

- [Azure Active Directory에서 관리자 역할 할당](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)
- [Azure에서 거버넌스](https://docs.microsoft.com/azure/security/governance-in-azure)

### <a name="configuration-changes-testing"></a>구성 변경 테스트

사용자의 대상 하위 집합을 롤백하여 병렬 테스트 테넌트에 변경 내용을 배포 하는 것과 같은 간단한 기술에서 테스트 하는 경우 특별히 고려해 야 할 사항이 있습니다. 테스트 전략을 구현 하지 않은 경우 다음 표의 지침에 따라 테스트 방법을 정의 해야 합니다.

| 시나리오| 권장 사항 |
|-|-|
|인증 유형을 페더레이션된에서 PHS/PTA로 변경 하거나 그 반대로 변경| [준비 된 롤아웃](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-staged-rollout) 을 사용 하 여 인증 유형 변경의 영향을 테스트 합니다.|
|새 조건부 액세스 (CA) 정책 또는 Id 보호 정책 롤아웃|새 CA 정책을 만들고 테스트 사용자에 게 할당 합니다.|
|응용 프로그램의 테스트 환경 온 보 딩|응용 프로그램을 프로덕션 환경에 추가 하 고 MyApps 패널에서 숨긴 다음 QA (품질 보증) 단계에서 테스트 사용자에 게 할당 합니다.|
|동기화 규칙 변경|현재 프로덕션에 있는 동일한 구성 (스테이징 모드 라고도 함)을 사용 하 여 테스트 Azure AD Connect의 변경을 수행 하 고 CSExport 결과를 분석 합니다. 만족 스 러 우면 준비가 되 면 프로덕션으로 바꿉니다.|
|브랜딩 변경|별도의 테스트 테넌트에서 테스트 합니다.|
|새 기능 롤아웃|기능이 대상 사용자 집합에 대해 롤아웃을 지 원하는 경우 파일럿 사용자를 식별 하 고 빌드합니다. 예를 들어 셀프 서비스 암호 재설정 및 multi-factor authentication은 특정 사용자 또는 그룹을 대상으로 할 수 있습니다.|
|온-프레미스 IdP (Id 공급자) (예: Active Directory)에서 Azure AD로 응용 프로그램을 시작 합니다.|응용 프로그램에서 Salesforce와 같이 여러 IdP 구성을 지 원하는 경우에는 변경 기간 (응용 프로그램에 HRD 페이지가 도입 되는 경우) 중에 Azure AD와 테스트를 모두 구성 합니다. 응용 프로그램에서 여러 IdPs를 지원 하지 않는 경우 변경 제어 창 및 프로그램 가동 중지 시간 중에 테스트를 예약 합니다.|
|동적 그룹 규칙 업데이트|새 규칙을 사용 하 여 병렬 동적 그룹을 만듭니다. 계산 된 결과와 비교 합니다. 예를 들어 동일한 조건으로 PowerShell을 실행 합니다.<br>테스트를 통과 하는 경우 이전 그룹이 사용 된 위치 (가능한 경우)를 바꿉니다.|
|제품 라이선스 마이그레이션|[Azure Active Directory에서 라이선스 그룹의 단일 사용자에 대 한 라이선스를 변경](https://docs.microsoft.com/azure/active-directory/users-groups-roles/licensing-groups-change-licenses)하려면을 참조 하세요.|
|권한 부여, 발급, MFA 등의 AD FS 규칙 변경|그룹 클레임을 사용 하 여 사용자의 하위 집합을 대상으로 합니다.|
|AD FS 인증 환경 또는 이와 유사한 팜 전체 변경 내용 변경|동일한 호스트 이름을 사용 하 여 병렬 팜을 만들고, 구성 변경 내용을 구현 하 고, 호스트 파일, NLB 라우팅 규칙 또는 유사한 라우팅을 사용 하 여 클라이언트에서 테스트 합니다.<br>대상 플랫폼에서 호스트 파일 (예: 모바일 장치)을 지원 하지 않는 경우 변경 내용을 제어 합니다.|

## <a name="access-reviews"></a>액세스 검토

### <a name="access-reviews-to-applications"></a>응용 프로그램에 대 한 액세스 검토

시간이 지남에 따라 사용자는 리소스에 대 한 액세스를 여러 팀과 위치에서 이동할 때 누적 할 수 있습니다. 리소스 소유자는 응용 프로그램에 대 한 액세스를 정기적으로 검토 하 고 사용자의 수명 주기 동안 더 이상 필요 하지 않은 권한을 제거 하는 것이 중요 합니다. Azure AD [액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) 를 사용 하면 조직에서 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다. 리소스 소유자는 사용자의 액세스를 정기적으로 검토 하 여 적절 한 사용자만 계속 액세스할 수 있도록 해야 합니다. 이상적으로이 작업에는 Azure AD 액세스 검토를 사용 하는 것이 좋습니다.

![액세스 검토 시작 페이지](./media/active-directory-ops-guide/active-directory-ops-img15.png)

> [!NOTE]
> 액세스 검토와 상호 작용 하는 각 사용자에 게는 유료 Azure AD Premium P2 라이선스가 있어야 합니다.

### <a name="access-reviews-to-external-identities"></a>외부 id에 대 한 액세스 검토

필요한 시간 동안 필요한 리소스에만 제한 되는 외부 id에 대 한 액세스를 유지 하는 것이 중요 합니다. Azure AD [액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)를 사용 하 여 모든 외부 id 및 응용 프로그램 액세스에 대 한 일반적인 자동화 된 액세스 검토 프로세스를 설정 합니다. 프로세스가 이미 온-프레미스에 있는 경우 Azure AD 액세스 검토를 사용 하는 것이 좋습니다. 응용 프로그램이 사용 중지 되거나 더 이상 사용 되지 않으면 응용 프로그램에 액세스할 수 있는 모든 외부 id를 제거 합니다.

> [!NOTE]
> 액세스 검토와 상호 작용 하는 각 사용자에 게는 유료 Azure AD Premium P2 라이선스가 있어야 합니다.

## <a name="privileged-account-management"></a>권한 있는 계정 관리

### <a name="privileged-account-usage"></a>권한 있는 계정 사용

해커가 중요 한 데이터 및 시스템에 빠르게 액세스할 수 있도록 관리자 계정 및 권한 있는 액세스의 다른 요소를 대상으로 하는 경우가 많습니다. 권한 있는 역할을 가진 사용자는 시간이 지남에 따라 누적 되기 때문에 관리자 액세스를 정기적으로 검토 및 관리 하 고 Azure AD 및 Azure 리소스에 대 한 just-in-time 권한 액세스를 제공 하는 것이 중요 합니다.

권한 있는 계정을 관리 하기 위해 조직에 프로세스가 없거나, 현재 관리자가 일반 사용자 계정을 사용 하 여 서비스 및 리소스를 관리 하는 관리자가 있는 경우 정기적으로 매일 별도의 계정을 사용 하 여 시작 해야 합니다. 내용 기타는 특권 수준의 액세스를 위해 MFA를 사용 하 여 구성 됩니다. 그러나 조직에 Azure AD Premium P2 구독이 있는 경우 PIM ( [Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure#license-requirements) )을 즉시 배포 해야 합니다. 동일한 토큰에서 해당 하는 경우 권한 있는 계정도 검토 하 고 [권한이 적은 역할을 할당](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) 해야 합니다.

구현 해야 하는 권한 있는 계정 관리의 또 다른 측면은 [PIM을 통해](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-how-to-perform-security-review)수동 또는 자동화 된 계정에 대 한 [액세스 검토](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview) 를 정의 하는 것입니다.

#### <a name="privileged-account-management-recommended-reading"></a>권한 있는 계정 관리 권장 읽기

- [Azure AD Privileged Identity Management의 역할](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-roles)

### <a name="emergency-access-accounts"></a>응급 액세스 계정

조직은 다음과 같은 인증 중단 등의 경우 Azure AD를 관리 하기 위해 준비할 [응급 계정을](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-emergency-access) 만들어야 합니다.

- 인증 인프라의 작동 중단 구성 요소 (AD FS, 온-프레미스 AD, MFA 서비스)
- 관리 직원 회전율

관리자 권한으로 기존 개인 사용자의 계정을 로그인 하거나 활성화할 수 없어 테넌트에서 실수로 잠기는 것을 방지 하기 위해 두 개 이상의 응급 계정을 만들어 [Microsoft의 모범 사례](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure) 및 충돌 방지 [절차](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-admin-roles-secure#break-glass-what-to-do-in-an-emergency)에 맞게 구현 및 맞춰야 합니다.

### <a name="privileged-access-to-azure-ea-portal"></a>Azure EA 포털에 대 한 권한 있는 액세스

Azure [EA (azure 기업계약) 포털](https://azure.microsoft.com/blog/create-enterprise-subscription-experience-in-azure-portal-public-preview/) 을 사용 하면 엔터프라이즈 내에서 강력한 역할인 마스터 기업계약에 대해 azure 구독을 만들 수 있습니다. Azure AD가 준비 되기 전에이 포털의 생성을 부트스트랩 하는 것이 일반적입니다. 따라서 Azure AD id를 사용 하 여이를 잠그고, 포털에서 개인 계정을 제거 하 고, 적절 한 위임이 적절 한지 확인 하 고, 잠금 위험을 완화 해야 합니다. .

명확 하 게 하기 위해 EA 포털 권한 부여 수준이 현재 "혼합 모드"로 설정 되어 있는 경우 EA 포털의 모든 권한 있는 액세스에서 모든 [Microsoft 계정을](https://support.skype.com/en/faq/FA12059/what-is-a-microsoft-account) 제거 하 고 Azure AD 계정만 사용 하도록 ea 포털을 구성 해야 합니다. EA 포털 위임 된 역할이 구성 되지 않은 경우 부서와 계정에 대 한 위임 된 역할도 찾아서 구현 해야 합니다.

#### <a name="privileged-access-recommended-reading"></a>권한 있는 액세스 권장 읽기

- [Azure Active Directory의 관리자 역할 사용 권한](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

## <a name="entitlement-management"></a>권한 관리

[EM (권한 관리](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview) )을 통해 앱 소유자는 리소스를 번들로 묶어 조직의 특정 사용자 (내부 및 외부 모두)에 할당할 수 있습니다. EM을 사용 하면 관리 정책을 통해 액세스 권한을 부여 하 고, 액세스 기간을 설정 하 고, 승인 워크플로를 허용 하도록 셀프 서비스 등록 및 비즈니스 소유자에 게 위임할 수 있습니다. 

> [!NOTE]
> Azure AD 자격 관리에 Azure AD Premium P2 라이선스가 필요 합니다.

## <a name="summary"></a>요약

보안 Id 거 버 넌 스에는 8 가지 측면이 있습니다. 이 목록은 권한 없는 id 및 권한 있는 id에 부여 된 액세스를 평가 하 고 증명 하기 위해 수행 해야 하는 작업을 식별 하는 데 도움이 됩니다.

- 키 작업에 소유자를 할당 합니다.
- 테스트 전략을 구현 합니다.
- Azure AD 액세스 검토를 사용 하 여 그룹 멤버 자격, 엔터프라이즈 응용 프로그램에 대 한 액세스 및 역할 할당을 효율적으로 관리할 수 있습니다.
- 모든 유형의 외부 id 및 응용 프로그램 액세스에 대해 자동화 된 일반 액세스 검토 프로세스를 설정 합니다.
- 액세스 검토 프로세스를 설정 하 여 정기적으로 관리자 액세스를 검토 및 관리 하 고 Azure AD 및 Azure 리소스에 대 한 just-in-time 권한 액세스를 제공 합니다.
- 예기치 않은 작동 중단을 위해 Azure AD를 관리 하기 위해 준비할 응급 계정을 프로 비전 합니다.
- Azure EA 포털에 대 한 액세스를 잠급니다.
- 권한 관리를 구현 하 여 리소스 컬렉션에 대 한 관리 되는 액세스를 제공 합니다.

## <a name="next-steps"></a>다음 단계

[AZURE AD 작업 검사 및 작업](active-directory-ops-guide-ops.md)을 시작 합니다.
