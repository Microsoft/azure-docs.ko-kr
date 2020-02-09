---
title: Predicates 및 PredicateValidations
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용 하 여 잘못 된 형식의 데이터를 Azure AD B2C 테넌트에 추가 하지 않도록 합니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/28/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: a09478bd2e32a1ab484b85fec33ae03878ebb10c
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951023"
---
# <a name="predicates-and-predicatevalidations"></a>Predicates 및 PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**조건자** 및 **PredicateValidations** 요소를 사용 하면 올바른 형식의 데이터만 Azure AD B2C (Azure Active Directory B2C) 테넌트에 입력 되도록 유효성 검사 프로세스를 수행할 수 있습니다.

아래 다이어그램에서는 요소 간의 관계가 나와 있습니다.

![조건자 및 조건자 유효성 검사 관계를 보여 주는 다이어그램](./media/predicates/predicates.png)

## <a name="predicates"></a>조건자

**Predicate** 요소는 클레임 유형의 값을 확인하기 위한 기본적인 유효성 검사를 정의하고 `true` 또는 `false`를 반환합니다. 지정된 **Method** 요소 및 해당 메서드와 관련된 **Parameter** 요소 집합을 사용하여 유효성 검사를 수행합니다. 예를 들어 조건자는 문자열 클레임 값의 길이가 지정한 최소/최대 매개 변수 범위 내에 포함되는지 여부나 문자열 클레임 값에 특정 문자 집합이 포함되는지 여부를 확인할 수 있습니다. 확인이 실패하면 **UserHelpText** 요소가 사용자용 오류 메시지를 제공합니다. [언어 사용자 지정](localization.md)을 통해 **UserHelpText** 요소의 값을 지역화할 수 있습니다.

**조건자** 요소는 [BuildingBlocks](buildingblocks.md) 요소 내의 **ClaimsSchema** 요소 바로 다음에 나와야 합니다.

**Predicates** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Predicate | 1:n | 조건자 목록입니다. |

**Predicate** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | yes | 조건자에 사용되는 식별자입니다. 다른 요소는 정책에서 이 식별자를 사용할 수 있습니다. |
| 방법 | yes | 유효성 검사에 사용할 메서드 형식입니다. 가능한 값은 **IsLengthRange**, **MatchesRegex**, **IncludesCharacters** 또는 **IsDateRange**입니다. **IsLengthRange** 값은 문자열 클레임 값의 길이가 지정한 최소/최대 매개 변수 범위 내에 포함되는지 여부를 확인합니다. **MatchesRegex** 값은 문자열 클레임 값이 정규식과 일치하는지 여부를 확인합니다. **IncludesCharacters** 값은 문자열 클레임 값에 특정 문자 집합이 포함되는지 여부를 확인합니다. **IsDateRange** 값은 날짜 클레임 값이 지정한 최소/최대 매개 변수 범위 사이에 있는지 여부를 확인합니다. |

**Predicate** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 | 확인이 실패하면 사용자에게 표시할 오류 메시지입니다. [언어 사용자 지정](localization.md)을 통해 이 문자열을 지역화할 수 있습니다. |
| parameters | 1:1 | 문자열 유효성 검사의 메서드 형식에 대한 매개 변수입니다. |

**Parameters** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| 매개 변수를 포함해야 합니다. | 1:n | 문자열 유효성 검사의 메서드 형식에 대한 매개 변수입니다. |

**Parameter** 요소에는 다음과 같은 특성이 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| Id | 1:1 | 매개 변수의 식별자입니다. |

다음 예제에서는 문자열의 길이 범위를 지정하는 `Minimum` 및 `Maximum` 매개 변수가 포함된 `IsLengthRange` 메서드를 보여 줍니다.

```XML
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
  <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

다음 예제에서는 정규식을 지정하는 `RegularExpression` 매개 변수가 포함된 `MatchesRegex` 메서드를 보여 줍니다.

```XML
<Predicate Id="PIN" Method="MatchesRegex">
  <UserHelpText>The password must be numbers only.</UserHelpText>
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

다음 예제에서는 문자 집합을 지정하는 `CharacterSet` 매개 변수가 포함된 `IncludesCharacters` 메서드를 보여 줍니다.

```XML
<Predicate Id="Lowercase" Method="IncludesCharacters">
  <UserHelpText>a lowercase letter</UserHelpText>
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

다음 예제에서는 `yyyy-MM-dd` 및 `Today` 형식의 날짜 범위를 지정하는 `Minimum` 및 `Maximum` 매개 변수가 포함된 `IsDateRange` 메서드를 보여 줍니다.

```XML
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

조건자는 클레임 형식에 대해 확인할 유효성 검사를 정의하는 반면 **PredicateValidations**는 조건자 집합을 그룹화하여 클레임 형식에 적용할 수 있는 사용자 입력 유효성 검사를 작성합니다. 각 **PredicateValidation** 요소는 **PredicateGroup** 요소 집합을 포함하며, 이 요소 집합에는 **Predicate**를 가리키는 **PredicateReference** 요소 집합이 포함됩니다. 유효성 검사에 통과하려면 클레임의 값이 **PredicateReference** 요소 집합을 포함하는 모든 **PredicateGroup**에서 조건자의 테스트를 모두 통과해야 합니다.

**PredicateValidations** 요소는 [BuildingBlocks](buildingblocks.md) 요소 내의 **조건자** 요소 바로 다음에 나와야 합니다.

```XML
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

**PredicateValidations** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| PredicateValidation | 1:n | 조건자 유효성 검사 목록입니다. |

**PredicateValidation** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | yes | 조건자 유효성 검사에 사용되는 식별자입니다. **ClaimType** 요소는 정책에서 이 식별자를 사용할 수 있습니다. |

**PredicateValidation** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| PredicateGroups | 1:n | 조건자 그룹 목록입니다. |

**PredicateGroups** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| PredicateGroup | 1:n | 조건자 목록입니다. |

**PredicateGroup** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | yes | 조건자 그룹에 사용되는 식별자입니다.  |

**PredicateGroup** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| UserHelpText | 1:1 |  사용자가 입력해야 하는 값을 쉽게 확인할 수 있는 조건자 설명입니다. |
| PredicateReferences | 1:n | 조건자 참조 목록입니다. |

**PredicateReferences** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| MatchAtLeast | 아닙니다. | 입력이 허용되려면 값이 일치해야 하는 조건자 정의의 최소 수를 지정합니다. |

**PredicateReferences** 요소에는 다음과 같은 요소가 포함됩니다.

| 요소 | 발생 수 | 설명 |
| ------- | ----------- | ----------- |
| PredicateReference | 1:n | 조건자에 대한 참조입니다. |

**PredicateReference** 요소에는 다음과 같은 특성이 포함됩니다.

| 특성 | 필수 | 설명 |
| --------- | -------- | ----------- |
| Id | yes | 조건자 유효성 검사에 사용되는 식별자입니다.  |


## <a name="configure-password-complexity"></a>암호 복잡도 구성

**Predicates** 및 **PredicateValidationsInput**을 사용하면 계정을 만들 때 사용자가 입력하는 암호의 복잡도 요구 사항을 제어할 수 있습니다. 기본적으로 Azure AD B2C는 강력한 암호를 사용합니다. 또한 Azure AD B2C는 고객이 사용할 수는 암호의 복잡성을 제어하는 구성 옵션을 지원합니다. 다음의 조건자 요소를 사용하면 암호 복잡도를 정의할 수 있습니다.

- **IsLengthBetween8And64**(`IsLengthRange` 메서드 사용)는 암호가 8~64자 사이여야 한다는 유효성을 검사합니다.
- **Lowercase**(`IncludesCharacters` 메서드 사용)는 암호가 소문자를 포함하는지 유효성을 검사합니다.
- **Uppercase**(`IncludesCharacters` 메서드 사용)는 암호가 대문자를 포함하는지 유효성을 검사합니다.
- **Number**(`IncludesCharacters` 메서드 사용)는 암호가 숫자를 포함하는지 유효성을 검사합니다.
- **기호** 는 `IncludesCharacters` 메서드를 사용 하 여 암호가 여러 기호 문자 중 하나를 포함 하는지 확인 합니다.
- **PIN**(`MatchesRegex` 메서드 사용)은 암호가 숫자만 포함하는지 유효성을 검사합니다.
- **AllowedAADCharacters**(`MatchesRegex` 메서드 사용)는 잘못된 문자를 포함하는 암호를 입력했는지 유효성을 검사합니다.
- **DisallowedWhitespace**(`MatchesRegex` 메서드 사용)는 암호가 공백 문자로 시작하거나 끝나지 않는지 유효성을 검사합니다.

```XML
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange">
    <UserHelpText>The password must be between 8 and 64 characters.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters">
    <UserHelpText>a lowercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters">
    <UserHelpText>an uppercase letter</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters">
    <UserHelpText>a digit</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters">
    <UserHelpText>a symbol</UserHelpText>
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex">
    <UserHelpText>The password must be numbers only.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex">
    <UserHelpText>An invalid character was provided.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex">
    <UserHelpText>The password must not begin or end with a whitespace character.</UserHelpText>
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

기본적인 유효성 검사를 정의한 후에는 유효성 검사를 결합하여 정책에서 사용 가능한 암호 정책 집합을 만들 수 있습니다.

- **SimplePassword**는 DisallowedWhitespace, AllowedAADCharacters 및 IsLengthBetween8And64 유효성 검사를 수행합니다.
- **StrongPassword**는 DisallowedWhitespace, AllowedAADCharacters, 및 IsLengthBetween8And64 유효성 검사를 수행합니다. 마지막 그룹인 `CharacterClasses`는 `MatchAtLeast`가 3으로 설정된 추가 조건자 집합을 실행합니다. 이 경우 사용자 암호는 8~16자 사이여야 하며 소문자, 대문자, 숫자, 기호 중 3가지를 포함해야 합니다.
- **CustomPassword**는 DisallowedWhitespace, AllowedAADCharacters 유효성 검사만 수행합니다. 따라서 사용자는 문자가 유효하기만 하면 어떤 길이의 암호든 입력할 수 있습니다.

```XML
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

클레임 형식에서 **PredicateValidationReference** 요소를 추가하고 식별자를 SimplePassword, StrongPassword 또는 CustomPassword와 같은 조건자 유효성 검사 중 하나로 지정합니다.

```XML
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

아래에는 Azure AD B2C에서 오류 메시지가 표시될 때 요소가 구성되는 방식이 나와 있습니다.

![조건자 및 PredicateGroup 암호 복잡성 예 다이어그램](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>날짜 범위 구성

**Predicates** 및 **PredicateValidations** 요소를 사용하는 경우 `DateTimeDropdown`을 통해 **UserInputType**의 최소/최대 날짜 값을 제어할 수 있습니다. 이렇게 하려면 `IsDateRange` 메서드를 포함하여 **Predicate**를 만들고 최소/최대 매개 변수를 입력합니다.

```XML
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange">
    <UserHelpText>The date must be between 01-01-1980 and today.</UserHelpText>
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

`DateRange` 조건자에 대한 참조를 포함하는 **PredicateValidation**을 추가합니다.

```XML
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

클레임 형식에서 **PredicateValidationReference** 요소를 추가하고 식별자를 `CustomDateRange`로 지정합니다.

```XML
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```
