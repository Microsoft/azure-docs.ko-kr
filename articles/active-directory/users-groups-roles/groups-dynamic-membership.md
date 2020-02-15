---
title: 동적 그룹 멤버 자격 규칙-Azure AD | Microsoft Docs
description: 그룹을 자동으로 채우는 멤버 자격 규칙과 규칙 참조를 만드는 방법입니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/27/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: da983f87977de922ec547c3ade2972dfb4d69363
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77206262"
---
# <a name="dynamic-membership-rules-for-groups-in-azure-active-directory"></a>Azure Active Directory의 그룹에 대한 동적 멤버 자격 규칙

Azure AD(Azure Active Directory)에서 그룹에 대해 동적 멤버십을 사용하기 위해 복합 특성 기준 규칙을 만들 수 있습니다. 동적 그룹 멤버 자격은 사용자를 추가하고 제거하는 관리 오버헤드를 줄입니다. 이 문서에서는 사용자 또는 디바이스에 대한 동적 멤버 자격 규칙을 만드는 속성과 구문에 대해 자세히 설명합니다. 보안 그룹 또는 Office 365 그룹에서 동적 멤버 자격에 대한 규칙을 설정할 수 있습니다.

사용자 또는 디바이스의 특성이 변경될 때 변경 내용이 그룹 추가 또는 제거를 트리거할지를 확인하기 위해 시스템은 디렉터리에서 모든 동적 그룹 규칙을 평가합니다. 사용자 또는 디바이스가 그룹에 대한 규칙을 만족하면 해당 그룹의 멤버로 추가됩니다. 규칙을 더 이상 만족하지 않는 경우 제거됩니다. 동적 그룹의 멤버를 수동으로 추가하거나 제거할 수 없습니다.

- 디바이스 또는 사용자에 대한 동적 그룹은 만들 수 있지만 사용자 및 디바이스를 모두 포함하는 규칙은 만들 수 없습니다.
- 디바이스 소유자의 특성을 기반으로 하는 디바이스 그룹은 만들 수 없습니다. 디바이스 멤버 자격 규칙은 디바이스 특성만 참조할 수 있습니다.

> [!NOTE]
> 이 기능을 사용하려면 하나 이상의 동적 그룹의 멤버인 고유한 각 사용자에 대해 Azure AD Premium P1 라이선스가 필요합니다. 사용자에게 동적 그룹의 멤버가 될 수 있는 라이선스를 지정할 필요는 없지만, 이러한 사용자를 모두 포함하려면 테넌트에 최소 개수의 라이선스는 있어야 합니다. 예를 들어, 테넌트의 모든 동적 그룹에 고유한 사용자가 총 1,000명 있는 경우, 라이선스 요구 사항을 충족하려면 Azure AD Premium P1에 대해 1,000개 이상의 라이선스가 필요합니다.
> 동적 장치 그룹의 구성원 인 장치에는 라이선스가 필요 하지 않습니다.

## <a name="rule-builder-in-the-azure-portal"></a>Azure Portal의 규칙 작성기

Azure AD는 중요 한 규칙을 더 신속 하 게 만들고 업데이트 하는 규칙 작성기를 제공 합니다. 규칙 작성기는 최대 5 개 식의 생성을 지원 합니다. 규칙 작성기를 사용 하면 몇 가지 간단한 식으로 규칙을 보다 쉽게 만들 수 있지만 모든 규칙을 재현 하는 데 사용할 수는 없습니다. 규칙 작성기에서 만들려는 규칙을 지원 하지 않는 경우 텍스트 상자를 사용할 수 있습니다.

텍스트 상자를 사용 하 여 구성 하는 데 권장 되는 고급 규칙 또는 구문의 예는 다음과 같습니다.

- 5 개 이상의 식이 있는 규칙
- 부하 직원 규칙
- [연산자 우선 순위](groups-dynamic-membership.md#operator-precedence) 설정
- [복잡 한 식이 포함 된 규칙](groups-dynamic-membership.md#rules-with-complex-expressions) 예 `(user.proxyAddresses -any (_ -contains "contoso"))`

> [!NOTE]
> 규칙 작성기가 텍스트 상자에 생성 된 일부 규칙을 표시 하지 못할 수 있습니다. 규칙 작성기에서 규칙을 표시할 수 없는 경우 메시지가 표시 될 수 있습니다. 규칙 빌더는 지원 되는 구문, 유효성 검사 또는 동적 그룹 규칙의 처리를 어떤 식으로든 변경 하지 않습니다.

자세한 단계별 지침은 [동적 그룹 만들기 또는 업데이트](groups-create-rule.md)를 참조 하세요.

![동적 그룹에 대 한 멤버 자격 규칙 추가](./media/groups-dynamic-membership/update-dynamic-group-rule.png)

### <a name="rule-syntax-for-a-single-expression"></a>단일 식에 대 한 규칙 구문

단일 식은 가장 간단한 형태의 멤버 자격 규칙이며 위에서 언급한 세 부분만 있습니다. 단일 식이 있는 규칙은 `Property Operator Value`과 비슷합니다. 여기서 속성에 대한 구문은 object.property의 이름입니다.

단일 식을 사용하여 올바르게 구성된 멤버 자격 규칙의 예제는 다음과 같습니다.

```
user.department -eq "Sales"
```

단일 식에서 괄호는 선택 사항입니다. 멤버 자격 규칙 본문의 전체 길이는 2,048자를 초과할 수 없습니다.

## <a name="constructing-the-body-of-a-membership-rule"></a>멤버 자격 규칙 본문 구성

사용자 또는 디바이스를 그룹에 자동으로 채우는 멤버 자격 규칙은 참 또는 거짓 결과를 가져오는 이진 식입니다. 간단한 규칙의 세 부분은 다음과 같습니다.

- 속성
- 연산자
- 값

식 내에서 이 세 부분의 순서는 구문 오류를 방지하는 데 중요합니다.

## <a name="supported-properties"></a>지원되는 속성

멤버 자격 규칙을 구성하는 데 사용할 수 있는 세 가지 유형의 속성이 있습니다.

- 부울
- String
- 문자열 컬렉션

단일 식을 만드는 데 사용할 수 있는 사용자 속성은 다음과 같습니다.

### <a name="properties-of-type-boolean"></a>부울 형식의 속성

| 속성 | 허용되는 값 | 사용 |
| --- | --- | --- |
| accountEnabled |true false |user.accountEnabled -eq true |
| dirSyncEnabled |true false |user.dirSyncEnabled -eq true |

### <a name="properties-of-type-string"></a>문자열 형식의 속성

| 속성 | 허용되는 값 | 사용 |
| --- | --- | --- |
| city |임의의 문자열 값 또는 *null*입니다. |(user.city -eq "value") |
| country |임의의 문자열 값 또는 *null*입니다. |(user.country -eq "value") |
| companyName | 임의의 문자열 값 또는 *null*입니다. | (user.companyName -eq "value") |
| department |임의의 문자열 값 또는 *null*입니다. |(user.department -eq "value") |
| displayName |임의의 문자열 값 |(user.displayName -eq "value") |
| employeeId |임의의 문자열 값 |(user.employeeId -eq "value")<br>(user.employeeId -ne *null*) |
| facsimileTelephoneNumber |임의의 문자열 값 또는 *null*입니다. |(user.facsimileTelephoneNumber -eq "value") |
| givenName |임의의 문자열 값 또는 *null*입니다. |(user.givenName -eq "value") |
| jobTitle |임의의 문자열 값 또는 *null*입니다. |(user.jobTitle -eq "value") |
| mail |임의의 문자열 값 또는 *null*(사용자의 SMTP 주소)입니다. |(user.mail -eq "value") |
| mailNickName |임의의 문자열 값(사용자의 메일 별칭) |(user.mailNickName -eq "value") |
| mobile |임의의 문자열 값 또는 *null*입니다. |(user.mobile -eq "value") |
| objectId |사용자 개체의 GUID입니다. |(user.objectId -eq "11111111-1111-1111-1111-111111111111") |
| onPremisesSecurityIdentifier | 온-프레미스에서 클라우드로 동기화된 사용자의 온-프레미스 SID(보안 식별자)입니다. |(user.onPremisesSecurityIdentifier -eq "S-1-1-11-1111111111-1111111111-1111111111-1111111") |
| passwordPolicies |None DisableStrongPassword DisablePasswordExpiration DisablePasswordExpiration, DisableStrongPassword |(user.passwordPolicies -eq "DisableStrongPassword") |
| physicalDeliveryOfficeName |임의의 문자열 값 또는 *null*입니다. |(user.physicalDeliveryOfficeName -eq "value") |
| postalCode |임의의 문자열 값 또는 *null*입니다. |(user.postalCode -eq "value") |
| preferredLanguage |ISO 639-1 코드 |(user.preferredLanguage -eq "en-US") |
| sipProxyAddress |임의의 문자열 값 또는 *null*입니다. |(user.sipProxyAddress -eq "value") |
| state |임의의 문자열 값 또는 *null*입니다. |(user.state -eq "value") |
| streetAddress |임의의 문자열 값 또는 *null*입니다. |(user.streetAddress -eq "value") |
| surname |임의의 문자열 값 또는 *null*입니다. |(user.surname-eq "value") |
| telephoneNumber |임의의 문자열 값 또는 *null*입니다. |(user.telephoneNumber -eq "value") |
| usageLocation |두 자로 된 국가 코드 |(user.usageLocation -eq "US") |
| userPrincipalName |임의의 문자열 값 |(user.userPrincipalName -eq "alias@domain") |
| userType |member guest *null* |(user.userType -eq "Member") |

### <a name="properties-of-type-string-collection"></a>문자열 컬렉션 형식의 속성

| 속성 | 허용되는 값 | 사용 |
| --- | --- | --- |
| otherMails |임의의 문자열 값 |(user.otherMails -contains "alias@domain") |
| proxyAddresses |SMTP: alias@domain smtp: alias@domain |(user.proxyAddresses -contains "SMTP: alias@domain") |

디바이스 규칙에 사용되는 속성은 [디바이스에 대한 규칙](#rules-for-devices)을 참조하세요.

## <a name="supported-expression-operators"></a>지원 되는 식 연산자

다음 표에는 단일 식에 지원되는 모든 연산자와 해당 구문이 나와 있습니다. 연산자는 하이픈(-) 접두사를 사용하거나 사용하지 않을 수 있습니다.

| 연산자 | 구문 |
| --- | --- |
| 같지 않음 |-ne |
| 같음 |-eq |
| 다음으로 시작 안 함 |-notStartsWith |
| 시작 단어 |-startsWith |
| 포함하지 않음 |-notContains |
| 포함 |-contains |
| 일치하지 않음 |-notMatch |
| 일치 |-match |
| 그런 다음 | -in |
| 속하지 않음 | -notIn |

### <a name="using-the--in-and--notin-operators"></a>-in 및 -notIn 연산자 사용

사용자 특성의 값을 다양한 값과 비교하려는 경우 -in 또는 -notIn 연산자를 사용할 수 있습니다. "[" 및 "]" 괄호 기호를 사용하여 값 목록을 시작하고 끝냅니다.

 다음 예제에서는 user.department의 값이 목록의 값과 같으면 식은 true로 평가됩니다.

```
   user.department -in ["50001","50002","50003","50005","50006","50007","50008","50016","50020","50024","50038","50039","51100"]
```


### <a name="using-the--match-operator"></a>-match 연산자 사용 
**-match** 연산자는 정규식 일치에 사용됩니다. 예제:

```
user.displayName -match "Da.*"   
```
Da, Dav, David는 true로 평가되고, aDa는 false로 평가됩니다.

```
user.displayName -match ".*vid"
```
David는 true로 평가되고, Da는 false로 평가됩니다.

## <a name="supported-values"></a>지원되는 값

식에 사용된 값은 다음을 포함하여 여러 형식으로 구성할 수 있습니다.

* 문자열
* 부울 – true, false
* 숫자
* 배열 - 숫자 배열, 문자열 배열

식 내에서 값을 지정하는 경우 올바른 구문을 사용하여 오류를 방지해야 합니다. 구문 팁 일부는 다음과 같습니다.

* 값이 문자열이 아니면 큰따옴표는 선택 사항입니다.
* 문자열 및 regex 연산은 대/소문자를 구분하지 않습니다.
* 문자열 값에 큰따옴표가 포함되면 두 큰따옴표에서 모두 \` 문자를 사용하여 이스케이프해야 합니다. 예를 들어 "Sales"가 값인 경우 user.department -eq \`"Sales\`"가 올바른 구문입니다.
* null을 값으로 사용하여 Null 검사를 수행할 수도 있습니다(예: `user.department -eq null`).

### <a name="use-of-null-values"></a>Null 값 사용

규칙에 null 값을 지정하려면 *null* 값을 사용할 수 있습니다. 

* 식에서 *null* 값을 비교하는 경우 -eq 또는 -ne를 사용합니다.
* 리터럴 문자열 값으로 해석되도록 하려면 *null* 단어 주위에 따옴표를 사용합니다.
* -not 연산자는 null에 대한 비교 연산자로 사용할 수 없습니다. 이 연산자를 사용할 경우 null을 사용하든 아니면 $null을 사용하든 오류가 발생합니다.

null 값을 참조하는 올바른 방법은 다음과 같습니다.

```
   user.mail –ne null
```

## <a name="rules-with-multiple-expressions"></a>여러 식이 있는 규칙

그룹 멤버 자격 규칙은 -and, -or 및 -not 논리 연산자로 연결된 둘 이상의 단일 식으로 구성할 수 있습니다. 논리 연산자는 조합하여 사용할 수도 있습니다. 

여러 식을 사용하여 올바르게 구성된 멤버 자격 규칙의 예제는 다음과 같습니다.

```
(user.department -eq "Sales") -or (user.department -eq "Marketing")
(user.department -eq "Sales") -and -not (user.jobTitle -contains "SDE")
```

### <a name="operator-precedence"></a>연산자 우선 순위

모든 연산자는 우선 순위가 높은 순서에서 낮은 순서로 나열됩니다. 같은 줄에 있는 연산자는 우선 순위가 같습니다.

```
-eq -ne -startsWith -notStartsWith -contains -notContains -match –notMatch -in -notIn
-not
-and
-or
-any -all
```

사용자에 대한 두 식이 평가되는 연산자 우선 순위의 예제는 다음과 같습니다.

```
   user.department –eq "Marketing" –and user.country –eq "US"
```

괄호는 우선 순위가 요구 사항을 충족하지 않을 경우에 필요합니다. 예를 들어 부서가 먼저 평가되도록 하려면 다음 예제에서 괄호를 사용하여 순서를 결정하는 방법을 보여 줍니다.

```
   user.country –eq "US" –and (user.department –eq "Marketing" –or user.department –eq "Sales")
```

## <a name="rules-with-complex-expressions"></a>복잡한 식이 있는 규칙

멤버 자격 규칙은 속성, 연산자 및 값에서 더 복잡한 형식을 사용하는 복잡한 식으로 구성할 수 있습니다. 다음 중 하나가 충족되면 해당 식이 복잡한 식으로 간주됩니다.

* 속성이 값의 컬렉션, 특히 다중 값이 있는 속성으로 구성됩니다.
* 식에서 -any 및 -all 연산자를 사용합니다.
* 식의 값 자체가 하나 이상의 식일 수 있습니다.

## <a name="multi-value-properties"></a>다중 값 속성

다중 값 속성은 동일한 유형인 개체의 컬렉션입니다. 이 속성은 -any 및 -all 논리 연산자를 사용하여 멤버 자격 규칙을 만드는 데 사용할 수 있습니다.

| 속성 | 값 | 사용 |
| --- | --- | --- |
| assignedPlans | 컬렉션에 있는 각 개체는 다음 문자열 속성을 표시합니다. capabilityStatus, service, servicePlanId |user.assignedPlans -any(assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled") |
| proxyAddresses| SMTP: alias@domain smtp: alias@domain | (user.proxyAddresses -any (\_ -contains "contoso")) |

### <a name="using-the--any-and--all-operators"></a>-any 및 -all 연산자 사용

-any 및 -all 연산자를 사용하여 각각 컬렉션의 항목 중 하나 또는 모두에 조건을 적용할 수 있습니다.

* -any(컬렉션에서 적어도 하나의 항목이 조건과 일치하는 경우 충족)
* -all(컬렉션에서 모든 항목이 조건과 일치하는 경우 충족)

#### <a name="example-1"></a>예 1

assignedPlans는 사용자에게 할당된 모든 서비스 계획을 나열하는 다중 값 속성입니다. 다음 식은 사용 상태인 Exchange Online(계획 2) 서비스 계획을 GUID 값으로 사용하는 사용자를 선택합니다.

```
user.assignedPlans -any (assignedPlan.servicePlanId -eq "efb87545-963c-4e0d-99df-69c6916d9eb0" -and assignedPlan.capabilityStatus -eq "Enabled")
```

이와 같은 규칙은 Office 365(또는 다른 Microsoft 온라인 서비스) 기능을 사용하도록 설정된 모든 사용자를 그룹화하는 데 사용할 수 있습니다. 그러면 일단의 정책을 그룹에 적용할 수 있습니다.

#### <a name="example-2"></a>예제 2

다음 식은 Intune 서비스("SCO" 서비스 이름으로 식별)와 연결된 서비스 계획이 있는 모든 사용자를 선택합니다.

```
user.assignedPlans -any (assignedPlan.service -eq "SCO" -and assignedPlan.capabilityStatus -eq "Enabled")
```

### <a name="using-the-underscore-_-syntax"></a>밑줄(\_) 구문 사용

밑줄(\_) 구문은 동적 그룹에 사용자나 디바이스를 추가하기 위한 다중 값 문자열 컬렉션 속성 중 하나에서 특정 값의 발생과 일치합니다. -any 또는 -all 연산자와 함께 사용합니다.

규칙에서 밑줄(\_)을 사용하여 user.proxyAddress(user.otherMails와 같은 작동)를 기준으로 멤버를 추가하는 예입니다. 이 규칙은 그룹에 "contoso"를 포함하는 프록시 그룹의 모든 사용자를 추가합니다.

```
(user.proxyAddresses -any (_ -contains "contoso"))
```

## <a name="other-properties-and-common-rules"></a>다른 속성 및 일반 규칙

### <a name="create-a-direct-reports-rule"></a>"직접 보고서" 규칙 만들기

관리자의 직접 보고서를 모두 포함하는 그룹을 만들 수 있습니다. 나중에 관리자의 직접 보고서가 변경되면 그룹의 멤버 자격이 자동으로 조정됩니다.

직접 보고서 규칙은 다음 구문을 사용하여 구성됩니다.

```
Direct Reports for "{objectID_of_manager}"
```

"62e19b97-8b3d-4d4a-a106-4ce66896a863"가 관리자의 objectID 인 유효한 규칙의 예는 다음과 같습니다.

```
Direct Reports for "62e19b97-8b3d-4d4a-a106-4ce66896a863"
```

규칙을 올바르게 사용하는 데 도움이 될 수 있는 팁은 다음과 같습니다.

- **관리자 ID**는 관리자의 개체 ID입니다. 관리자의 **프로필**에서 찾을 수 있습니다.
- 규칙이 작동하려면 **관리자** 속성이 테넌트의 사용자에 대해 올바르게 설정되어 있는지 확인합니다. 사용자의 **프로필**에서 현재 값을 확인할 수 있습니다.
- 이 규칙은 관리자의 직접 보고서만 지원합니다. 다시 말해 그룹은 관리자의 직접 보고서 *및* 해당 보고서를 사용하여 만들 수는 없습니다.
- 이 규칙은 다른 멤버 자격 규칙과 결합할 수 없습니다.

### <a name="create-an-all-users-rule"></a>"모든 사용자" 규칙 만들기

멤버 자격 규칙을 사용하여 테넌트 내의 모든 사용자가 포함된 그룹을 만들 수 있습니다. 나중에 테넌트에서 사용자를 추가하거나 제거하면 그룹의 멤버 자격이 자동으로 조정됩니다.

"모든 사용자" 규칙은-ne 연산자와 null 값을 사용 하 여 단일 식을 사용 하 여 생성 됩니다. 이 규칙은 그룹에 멤버 사용자뿐만 아니라 B2B 게스트 사용자도 추가합니다.

```
user.objectId -ne null
```
그룹에서 게스트 사용자를 제외 하 고 테 넌 트의 멤버만 포함 하려는 경우 다음 구문을 사용할 수 있습니다.

```
(user.objectId -ne null) -and (user.userType -eq “Member”)
```

### <a name="create-an-all-devices-rule"></a>"모든 장치" 규칙 만들기

멤버 자격 규칙을 사용하여 테넌트 내의 모든 디바이스가 포함된 그룹을 만들 수 있습니다. 나중에 테넌트에서 디바이스를 추가하거나 제거하면 그룹의 멤버 자격이 자동으로 조정됩니다.

"모든 장치" 규칙은-ne 연산자와 null 값을 사용 하 여 단일 식을 사용 하 여 생성 됩니다.

```
device.objectId -ne null
```

## <a name="extension-properties-and-custom-extension-properties"></a>확장 속성 및 사용자 지정 확장 속성

확장 특성 및 사용자 지정 확장 속성은 동적 멤버 관리 규칙에서 문자열 속성으로 지원 됩니다. [확장 특성](https://docs.microsoft.com/graph/api/resources/onpremisesextensionattributes?view=graph-rest-1.0) 은 온-프레미스 WINDOWS Server AD에서 동기화 되 고 "ExtensionAttributeX" 형식을 사용 합니다. 여기서 X는 1-15입니다. 확장 특성을 속성으로 사용하는 규칙의 예제는 다음과 같습니다.

```
(user.extensionAttribute15 -eq "Marketing")
```

[사용자 지정 확장 속성](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-feature-directory-extensions) 은 온-프레미스 WINDOWS Server AD 또는 연결 된 SaaS 응용 프로그램에서 동기화 되 고 `user.extension_[GUID]_[Attribute]`형식입니다. 여기서는 다음과 같습니다.

* [GUID]는 Azure AD에서 속성을 만든 애플리케이션에 대한 Azure AD의 고유 식별자입니다.
* [Attribute]는 Azure AD에서 만든 속성의 이름입니다.

사용자 지정 확장 속성을 사용하는 규칙의 예제는 다음과 같습니다.

```
user.extension_c272a57b722d4eb29bfe327874ae79cb_OfficeNumber -eq "123"
```

사용자 지정 속성 이름은 [Graph 탐색기]를 사용하여 사용자 속성을 쿼리하고 속성 이름을 검색하여 디렉터리에서 찾을 수 있습니다. 또한 동적 사용자 그룹 규칙 작성기에서 **사용자 지정 확장 속성 가져오기**를 선택하여 고유한 앱 ID를 입력하고 동적 구성원 규칙을 만들 때 사용할 사용자 지정 확장 속성 전체 목록을 받을 수 있습니다. 이 목록을 새로 고침하여 해당 앱에 대한 새로운 사용자 지정 확장 속성을 가져올 수도 있습니다.

## <a name="rules-for-devices"></a>디바이스에 대한 규칙

또한 그룹의 멤버 자격에 대한 디바이스 개체를 선택하는 규칙을 만들 수 있습니다. 사용자와 디바이스는 모두 그룹 멤버로 사용할 수 없습니다. 

> [!NOTE]
> **organizationalUnit** 특성은 더 이상 나열되지 않고 사용할 수 없습니다. 이 문자열은 특정 사례에서 Intune에 의해 설정되지만 Azure AD에서 인식되지 않습니다. 따라서 디바이스는 이 특성을 기반으로 하는 그룹에 추가됩니다.

> [!NOTE]
> systemlabels Intune으로 설정할 수 없는 읽기 전용 특성입니다.
>
> Windows 10의 경우 deviceOSVersion 특성의 올바른 형식은 다음과 같습니다 (10.0.17763 "). Get-msoldevice PowerShell cmdlet을 사용 하 여 서식 지정의 유효성을 검사할 수 있습니다.

다음과 같은 디바이스 특성을 사용할 수 있습니다.

 디바이스 특성  | 값 | 예제
 ----- | ----- | ----------------
 accountEnabled | true false | (device.accountEnabled -eq true)
 displayName | 임의의 문자열 값입니다. |(device.displayName -eq "Rob iPhone")
 deviceOSType | 임의의 문자열 값입니다. | (device.deviceOSType -eq "iPad") -또는 (device.deviceOSType -eq "iPhone")<br>(device.deviceOSType -contains "AndroidEnterprise")<br>(device.deviceOSType -eq "AndroidForWork")
 deviceOSVersion | 임의의 문자열 값입니다. | (device.deviceOSVersion -eq "9.1")
 deviceCategory | 유효한 디바이스 범주 이름 | (device.deviceCategory -eq "BYOD")
 deviceManufacturer | 임의의 문자열 값입니다. | (device.deviceManufacturer -eq "Samsung")
 deviceModel | 임의의 문자열 값입니다. | (device.deviceModel -eq "iPad Air")
 deviceOwnership | 개인, 회사, 알 수 없음 | (device.deviceOwnership -eq "Company")
 enrollmentProfileName | Apple 장치 등록 프로필, 장치 등록-회사 장치 식별자 (Android 키오스크) 또는 Windows Autopilot 프로필 이름 | (device.enrollmentProfileName -eq "DEP iPhones")
 isRooted | true false | (device.isRooted -eq true)
 managementType | MDM(모바일 디바이스)<br>PC(Intune PC 에이전트에 의해 관리되는 컴퓨터) | (device.managementType -eq "MDM")
 deviceId | 유효한 Azure AD 디바이스 ID | (device.deviceId -eq "d4fe7726-5966-431c-b3b8-cddc8fdb717d")
 objectId | 유효한 Azure AD 개체 ID |  (device.objectId -eq "76ad43c9-32c5-45e8-a272-7b58b58f596d")
 devicePhysicalIds | Autopilot에서 사용 하는 모든 문자열 값 (예: 모든 Autopilot devices, OrderID 또는 PurchaseOrderID)  | (devicePhysicalIDs-any _-contains "[Zt\]") (devicePhysicalIds-any _-eq "[OrderID]: 179887111881") (devicePhysicalIds-any _-eq "[PurchaseOrderId]: 76222342342")
 systemLabels | 최신 작업 공간 디바이스의 태그를 지정하는 Intune 디바이스 속성과 일치하는 문자열 | (device.systemLabels -contains "M365Managed")

> [!Note]  
> 디바이스용 동적 그룹을 만들 때 deviceOwnership의 경우 값을 “Company”로 설정해야 합니다. Intune에서 디바이스 소유권이 Corporate로 대신 표시됩니다. 자세한 내용은 [OwnerTypes](https://docs.microsoft.com/intune/reports-ref-devices#ownertypes)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

이러한 문서는 Azure Active Directory의 그룹에 대한 추가 정보를 제공합니다.

- [기존 그룹 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
- [새 그룹을 만들고 멤버 추가](../fundamentals/active-directory-groups-create-azure-portal.md)
- [그룹의 설정 관리](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [그룹의 멤버 자격 관리](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [그룹의 사용자에 대한 동적 규칙 관리](groups-create-rule.md)
