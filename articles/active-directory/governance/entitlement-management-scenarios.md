---
title: 자격 관리의 일반적인 시나리오-Azure AD
description: Azure Active Directory 자격 관리의 일반적인 시나리오에 대해 수행 해야 하는 개략적인 단계를 알아봅니다.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f40ed05391c36f820940d5351373ecd8f9492b17
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/11/2020
ms.locfileid: "77120147"
---
# <a name="common-scenarios-in-azure-ad-entitlement-management"></a>Azure AD 자격 관리의 일반적인 시나리오

여러 가지 방법으로 조직에 대 한 자격 관리를 구성할 수 있습니다. 그러나 처음 시작 하는 경우 관리자, 카탈로그 소유자, 액세스 패키지 관리자, 승인자 및 요청자에 대 한 일반적인 시나리오를 이해 하는 것이 좋습니다.

## <a name="delegate"></a>대리자

### <a name="administrator-delegate-management-of-resources"></a>관리자: 리소스 관리 위임

1. [비디오 보기: IT 부서에서 부서 관리자로 위임](https://www.microsoft.com/videoplayer/embed/RE3Lq00)
1. [사용자를 카탈로그 작성자 역할에 위임](entitlement-management-delegate-catalog.md)

### <a name="catalog-creator-delegate-management-of-resources"></a>카탈로그 작성자: 리소스 관리 위임

- [새 카탈로그 만들기](entitlement-management-catalog-create.md#create-a-catalog)

### <a name="catalog-owner-delegate-management-of-resources"></a>카탈로그 소유자: 리소스 관리를 위임 합니다.

1. [카탈로그에 공동 소유자 추가](entitlement-management-catalog-create.md#add-additional-catalog-owners)
1. [카탈로그에 리소스 추가](entitlement-management-catalog-create.md#add-resources-to-a-catalog)

### <a name="catalog-owner-delegate-management-of-access-packages"></a>카탈로그 소유자: 액세스 패키지의 관리 위임

1. [비디오 보기: 카탈로그 소유자의 위임에서 패키지 관리자에 액세스](https://www.microsoft.com/videoplayer/embed/RE3Lq08)
1. [사용자를 위임 하 여 패키지 관리자 역할에 액세스](entitlement-management-delegate-managers.md)

## <a name="govern-access-for-users-in-your-organization"></a>조직의 사용자에 대 한 액세스 제어

### <a name="access-package-manager-allow-employees-in-your-organization-to-request-access-to-resources"></a>액세스 패키지 관리자: 조직의 직원이 리소스에 대 한 액세스를 요청할 수 있도록 허용

1. [새 액세스 패키지 만들기](entitlement-management-access-package-create.md#start-new-access-package)
1. [패키지에 액세스할 그룹, 팀, 응용 프로그램 또는 SharePoint 사이트를 추가 합니다.](entitlement-management-access-package-create.md#resource-roles)
1. [디렉터리의 사용자가 액세스를 요청할 수 있도록 요청 정책을 추가 합니다.](entitlement-management-access-package-create.md#for-users-in-your-directory)
1. [만료 설정 지정](entitlement-management-access-package-create.md#lifecycle)

### <a name="requestor-request-access-to-resources"></a>요청자: 리소스에 대 한 액세스 요청

1. [내 액세스 포털에 로그인 합니다.](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 액세스 패키지 찾기
1. [액세스 요청](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>승인자: 리소스에 대 한 요청을 승인 합니다.

1. [내 액세스 포털에서 요청 열기](entitlement-management-request-approve.md#open-request)
1. [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-you-already-have-access-to"></a>요청자: 이미 액세스 권한이 있는 리소스를 봅니다.

1. [내 액세스 포털에 로그인 합니다.](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 활성 액세스 패키지 보기

## <a name="govern-access-for-users-outside-your-organization"></a>조직 외부 사용자에 대 한 액세스 제어

### <a name="administrator-collaborate-with-an-external-partner-organization"></a>관리자: 외부 파트너 조직과 공동 작업

1. [외부 사용자에 대 한 액세스 작동 방법 읽기](entitlement-management-external-users.md#how-access-works-for-external-users)
1. [외부 사용자에 대 한 설정 검토](entitlement-management-external-users.md#settings-for-external-users)
1. [외부 조직에 연결 추가](entitlement-management-organization.md)

### <a name="access-package-manager-collaborate-with-an-external-partner-organization"></a>액세스 패키지 관리자: 외부 파트너 조직과 공동 작업

1. [새 액세스 패키지 만들기](entitlement-management-access-package-create.md#start-new-access-package)
1. [패키지에 액세스할 그룹, 팀, 응용 프로그램 또는 SharePoint 사이트를 추가 합니다.](entitlement-management-access-package-resources.md#add-resource-roles)
1. [디렉터리에 없는 사용자가 액세스를 요청할 수 있도록 요청 정책을 추가 합니다.](entitlement-management-access-package-request-policy.md#for-users-not-in-your-directory)
1. [만료 설정 지정](entitlement-management-access-package-create.md#lifecycle)
1. [링크를 복사 하 여 액세스 패키지 요청](entitlement-management-access-package-settings.md)
1. 외부 파트너 연락 파트너에 게 링크를 보내 사용자와 공유

### <a name="requestor-request-access-to-resources-as-an-external-user"></a>요청자: 외부 사용자로 리소스에 대 한 액세스 요청

1. 연락처에서 받은 액세스 패키지 링크 찾기
1. [내 액세스 포털에 로그인 합니다.](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. [액세스 요청](entitlement-management-request-access.md#request-an-access-package)

### <a name="approver-approve-requests-to-resources"></a>승인자: 리소스에 대 한 요청을 승인 합니다.

1. [내 액세스 포털에서 요청 열기](entitlement-management-request-approve.md#open-request)
1. [액세스 요청 승인 또는 거부](entitlement-management-request-approve.md#approve-or-deny-request)

### <a name="requestor-view-the-resources-your-already-have-access-to"></a>요청자: 이미 액세스 권한이 있는 리소스 보기

1. [내 액세스 포털에 로그인 합니다.](entitlement-management-request-access.md#sign-in-to-the-my-access-portal)
1. 활성 액세스 패키지 보기

## <a name="day-to-day-management"></a>일상적인 관리

### <a name="access-package-manager-update-the-resources-for-a-project"></a>액세스 패키지 관리자: 프로젝트에 대 한 리소스 업데이트

1. [비디오 보기: 일상적인 관리: 변경 된 항목](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 액세스 패키지 열기
1. [그룹, 팀, 응용 프로그램 또는 SharePoint 사이트 추가 또는 제거](entitlement-management-access-package-resources.md#add-resource-roles)

### <a name="access-package-manager-update-the-duration-for-a-project"></a>액세스 패키지 관리자: 프로젝트의 기간을 업데이트 합니다.

1. [비디오 보기: 일상적인 관리: 변경 된 항목](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. 액세스 패키지 열기
1. [수명 주기 설정 열기](entitlement-management-access-package-lifecycle-policy.md#open-lifecycle-settings)
1. [만료 설정 업데이트](entitlement-management-access-package-lifecycle-policy.md#lifecycle)

### <a name="access-package-manager-update-how-access-is-approved-for-a-project"></a>액세스 패키지 관리자: 프로젝트에 대 한 액세스가 승인 되는 방법 업데이트

1. [비디오 보기: 일상적인 관리: 변경 된 항목](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [요청 및 승인 설정의 기존 정책 열기](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [승인 설정 업데이트](entitlement-management-access-package-request-policy.md#approval)

### <a name="access-package-manager-update-the-people-for-a-project"></a>액세스 패키지 관리자: 프로젝트에 대 한 사용자를 업데이트 합니다.

1. [비디오 보기: 일상적인 관리: 변경 된 항목](https://www.microsoft.com/videoplayer/embed/RE3LD4Z)
1. [더 이상 액세스할 필요가 없는 사용자 제거](entitlement-management-access-package-assignments.md)
1. [요청 및 승인 설정의 기존 정책 열기](entitlement-management-access-package-request-policy.md#open-an-existing-policy-of-request-and-approval-settings)
1. [액세스 권한이 필요한 사용자 추가](entitlement-management-access-package-request-policy.md#for-users-in-your-directory)

### <a name="access-package-manager-directly-assign-specific-users-to-an-access-package"></a>액세스 패키지 관리자: 액세스 패키지에 특정 사용자를 직접 할당 합니다.

1. [사용자에 게 다른 수명 주기 설정이 필요한 경우 액세스 패키지에 새 정책을 추가 합니다.](entitlement-management-access-package-request-policy.md#add-a-new-policy-of-request-and-approval-settings)
1. [액세스 패키지에 특정 사용자를 직접 할당](entitlement-management-access-package-assignments.md#directly-assign-a-user)

## <a name="assignments-and-reports"></a>할당 및 보고서

### <a name="administrator-view-who-has-assignments-to-an-access-package"></a>관리자: 액세스 패키지에 할당 된 사용자 보기

1. 액세스 패키지 열기
1. [할당 보기](entitlement-management-access-package-assignments.md#view-who-has-an-assignment)

### <a name="administrator-view-resources-assigned-to-users"></a>관리자: 사용자에 게 할당 된 리소스 보기

1. [사용자에 대 한 액세스 패키지 보기](entitlement-management-reports.md#view-access-packages-for-a-user)
1. [사용자에 대 한 리소스 할당 보기](entitlement-management-reports.md#view-resource-assignments-for-a-user)

## <a name="programmatic-administration"></a>프로그래밍 방식 관리

Microsoft Graph를 사용 하 여 액세스 패키지, 카탈로그, 정책, 요청 및 할당을 관리할 수도 있습니다.  위임 된 `EntitlementManagement.ReadWrite.All` 권한이 있는 응용 프로그램을 사용 하 여 적절 한 역할의 사용자는 [자격 관리 API](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)를 호출할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [위임 및 역할](entitlement-management-delegate.md)
- [요청 프로세스 및 전자 메일 알림](entitlement-management-process.md)
