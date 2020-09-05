---
title: 범위 지정 필터를 사용하여 앱 프로비전 | Microsoft Docs
description: 개체가 비즈니스 요구 사항을 충족하지 못하는 경우 프로비전하는 자동화된 사용자를 지원하는 앱의 개체가 실제로 프로비전되지 않도록 하기 위한 지정 범위 필터 사용 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: kenwith
ms.openlocfilehash: 1765c6a7d19a11730567acd5e41deebc0a20cec2
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235827"
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>범위 지정 필터를 사용한 특성 기반 애플리케이션 프로비전
이 문서에서는 범위 지정 필터를 사용하여 어떤 사용자를 애플리케이션에 프로비전할지 결정하는 특성 기반 규칙을 정의하는 방법을 설명합니다.

## <a name="scoping-filter-use-cases"></a>범위 지정 필터 사용 사례

범위 지정 필터를 사용하면 Azure AD(Azure Active Directory)가 특정 값에 부합하는 특성을 갖는 사용자를 포함하거나 제외하여 서비스를 프로비전할 수 있습니다. 예를 들어 영업 팀에서 사용하는 SaaS 애플리케이션에 Azure AD 사용자를 프로비전할 때, "부서" 속성이 "영업"인 사용자만 프로비전 범위에 포함되도록 지정할 수 있습니다.

프로비전 커넥터 유형에 따라 범위 지정 필터를 다르게 사용할 수 있습니다.

* **Azure AD에서 SaaS 애플리케이션으로 아웃바운드 프로비전** Azure AD가 원본 시스템일 때 [사용자 및 그룹 할당](../manage-apps/assign-user-or-group-access-portal.md)이 프로비전 범위에 포함될 사용자를 결정하는 가장 일반적인 방법입니다. 이러한 할당은 Single Sign-On 활성화에도 사용되며 액세스 및 프로비전을 관리하는 단일 방법을 제공합니다. 범위 지정 필터는 할당과 함께 또는 할당을 대체하여 선택적으로 사용하여 특성 값에 따라 사용자를 필터링할 수 있습니다.

    >[!TIP]
    > 프로비전 설정 아래에 있는 [범위](../app-provisioning/user-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) 메뉴의 설정을 **모든 사용자 및 그룹 동기화**로 변경하여 엔터프라이즈 애플리케이션에 대한 할당을 기준으로 프로비전을 사용하지 않을 수 있습니다. 

* **HCM 애플리케이션에서 Azure AD 및 Active Directory로의 인바운드 프로비전** [Workday 같은 HCM 애플리케이션](../saas-apps/workday-tutorial.md)이 원본 시스템일 경우, 범위 지정 필터가 HCM 애플리케이션이 Active Directory 또는 Azure AD에 프로비전할 사용자를 판단하는 기본 방법이 됩니다.

기본적으로 Azure AD 프로비전 커넥터에는 특성 기반 범위 지정 필터가 구성되어 있지 않습니다. 

## <a name="scoping-filter-construction"></a>범위 지정 필터 구축

범위 지정 필터는 하나 이상의 *절*로 구성됩니다. 절은 각 사용자의 특성을 평가하여 어떤 사용자가 범위 지정 필터를 통과할 수 있는지를 파악합니다. 예를 들어, 사용자의 "상태" 특성이 뉴욕이어야 하는 절을 가졌기 때문에 "뉴욕"의 사용자만 애플리케이션에 프로비전됩니다. 

단일 절은 단일 특성 값에 대한 단일 조건을 정의합니다. 단일 범위 지정 필터에서 여러 절이 생성될 경우 "AND" 논리를 사용하여 함께 평가됩니다. 즉 사용자에게 프로비전되려면 모든 절이  "true"로 평가되어야 합니다.

마지막으로 단일 애플리케이션에 대해 여러 범위 지정 필터를 만들 수 있습니다. 범위 지정 필터가 여러 개 있으면 "OR" 논리를 사용하여 함께 평가됩니다. 즉, 구성된 범위 지정 필터의 모든 절이 "true"로 평가되면 사용자가 프로비전됩니다.

Azure AD 프로비전 서비스에서 처리한 각 사용자 또는 그룹은 항상 각 범위 지정 필터에 대해 개별적으로 평가됩니다.

예를 들어, 다음과 같은 범위 지정 필터를 고려해봅니다.

![범위 지정 필터](./media/define-conditional-rules-for-provisioning-user-accounts/scoping-filter.PNG) 

프로비전되는 범위 지정 필터에 따라 사용자는 다음 조건을 충족해야 합니다.

* 뉴욕에 있어야 합니다.
* 엔지니어링 부서에서 근무해야 합니다.
* 회사 직원 ID가 1,000,000에서 2,000,000개 사이여야 합니다.
* 직위가 null이나 공백이 아니어야 합니다.

## <a name="create-scoping-filters"></a>범위 지정 필터 만들기
범위 지정 필터는 각 Azure AD 사용자 프로비전 커넥터에 대해 특성 매핑의 일부로 구성됩니다. 다음 프로시저에서는 이미 [지원되는 애플리케이션 중 하나](../saas-apps/tutorial-list.md)에 대해 자동 프로비전을 설정했고 거기에 범위 지정 필터를 추가한다고 가정합니다.

### <a name="create-a-scoping-filter"></a>범위 지정 필터 만들기
1. [Azure Portal](https://portal.azure.com)에서 **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 섹션으로 이동합니다.

2. 자동 프로비전을 구성한 애플리케이션을 선택합니다(예: "ServiceNow").

3. **프로비전** 탭을 선택합니다.

4. **매핑** 섹션에서 범위 지정 필터를 구성하려는 매핑을 선택합니다.(예: "Azure Active Directory 사용자를 ServiceNow에 동기화합니다.")

5. **원본 개체 범위** 메뉴를 선택합니다.

6. **범위 지정 필터 추가**를 선택합니다.

7. 비교 대상이 될 **특성 이름**, **연산자**, **특성 값**을 선택하여 절을 정의합니다. 다음과 같은 연산자가 지원됩니다.

   a. **EQUALS** 평가된 특성이 입력 문자열 값과 정확히 일치하면(대소문자 구분) "true"를 반환합니다.

   b. 같지 **않음** 평가된 특성이 입력 문자열 값과 정확히 일치하면(대소문자 구분) "true"를 반환합니다.

   c. **이 TRUE 인**경우 평가된 특성이 부울 값 true를 포함하면 "true"를 반환합니다.

   d. **가 FALSE 인 경우** 평가된 특성이 부울 값 false를 포함하면 "true"를 반환합니다.

   e. **가 NULL 인 경우** 평가된 특성이 비어 있으면 "true"를 반환합니다.

   f. **가 NULL이 아닌**경우 평가된 특성이 비어 있지 않으면 "true"를 반환합니다.

   g. **REGEX MATCH** 평가된 특성이 정규식 패턴과 일치하면 "true"를 반환합니다. 예를 들어, ([1-9][0-9])는 10~99 범위의 모든 숫자와 일치합니다.

   h. **NOT REGEX MATCH** 평가된 특성이 정규식 패턴과 일치하지 않으면 "true"를 반환합니다.
   
   i. **Greater_Than.** 평가 된 특성이 값 보다 크면 절은 "true"를 반환 합니다. 범위 지정 필터에 지정 된 값은 정수 여야 하며 사용자의 특성은 [0, 1, 2,...]의 정수 여야 합니다. 
   
   j. **Greater_Than_OR_EQUALS.** 평가 된 특성이 값 보다 크거나 같으면 "true"를 반환 합니다. 범위 지정 필터에 지정 된 값은 정수 여야 하며 사용자의 특성은 [0, 1, 2,...]의 정수 여야 합니다. 
   
   k. **내용이.** 평가 된 특성이 [여기](/dotnet/api/system.string.contains?view=netframework-4.8)에 설명 된 대로 문자열 값 (대/소문자 구분)을 포함 하면 "true"를 반환 합니다. 


>[!IMPORTANT] 
> - IsMemberOf 필터는 현재 지원 되지 않습니다.
> - 같음 및 같지 않음은 다중 값 특성에 지원 되지 않습니다.

9. 필요에 따라 7~8단계를 반복하여 다른 범위 지정 절을 추가합니다.

10. **범위 지정 필터 제목**에서 범위 지정 필터의 이름을 추가합니다.

11. **확인**을 선택합니다.

12. **범위 지정 필터** 화면에서 **확인**을 다시 선택합니다. 필요에 따라 6~11단계를 반복하여 다른 범위 지정 필터를 추가합니다.

13. **특성 매핑** 화면에서 **저장**을 선택합니다. 

>[!IMPORTANT] 
> 새 범위 지정 필터를 저장하면 애플리케이션의 새로운 전체 동기화가 트리거되며 여기서 원본 시스템의 모든 사용자를 새 범위 지정 필터에 대해 다시 평가합니다. 애플리케이션의 사용자가 프로비전 범위에서 이전에 평가되었으나 범위를 벗어난 경우 해당 계정은 사용되지 않거나 애플리케이션에서 프로비전 해제됩니다. 이 기본 동작을 재정의 하려면 범위를 [벗어나는 사용자 계정에 대 한 삭제 건너뛰기](../app-provisioning/skip-out-of-scope-deletions.md)를 참조 하세요.


## <a name="common-scoping-filters"></a>공통 범위 지정 필터
| 대상 특성| 연산자 | 값 | 설명|
|----|----|----|----|
|userPrincipalName|REGEX 일치|.\*@domain.com |도메인을 가진 userPrincipal의 모든 사용자 @domain.com 는 프로 비전 범위에 포함 됩니다.|
|userPrincipalName|REGEX 일치 하지 않음|.\*@domain.com|도메인을 가진 userPrincipal의 모든 사용자 @domain.com 는 프로 비전 범위를 벗어났습니다.|
|department|EQUALS|sales|판매 부서의 모든 사용자가 프로 비전 범위에 있습니다.|
|workerID|REGEX 일치|(1[0-9][0-9][0-9][0-9][0-9][0-9])| 100만과 200만 사이에 근무 연수가 Ds가 있는 모든 직원은 프로 비전 범위에 있습니다.|

## <a name="related-articles"></a>관련 문서
* [SaaS 응용 프로그램에 대 한 사용자 프로 비전 및 프로 비전 해제 자동화](../app-provisioning/user-provisioning.md)
* [사용자 프로비전을 위한 사용자 지정 특성 매핑](../app-provisioning/customize-application-attributes.md)
* [특성 매핑 식 작성](functions-for-customizing-application-data.md)
* [계정 프로비전 알림](../app-provisioning/user-provisioning.md)
* [SCIM를 사용하여 Azure Active Directory으로부터 애플리케이션에 사용자 및 그룹의 자동 프로비전 사용](../app-provisioning/use-scim-to-provision-users-and-groups.md)
* [SaaS App을 통합하는 방법에 대한 자습서 목록](../saas-apps/tutorial-list.md)