---
title: 암호 복잡성 요구 사항 구성
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 소비자가 제공한 암호에 복잡성 요구 사항을 구성하는 방법입니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 59ac2f74d4753c1f647bedfa2c402e729e25ee62
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847811"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 암호에 복잡성 요구 사항 구성

Azure AD B2C(Azure Active Directory B2C)는 계정을 만들 때 최종 사용자가 제공하는 암호에 복잡성 요구 사항을 변경하도록 지원합니다. 기본적으로 Azure AD B2C는 `Strong` 암호를 사용합니다. 또한 Azure AD B2C는 고객이 사용할 수는 암호의 복잡성을 제어하는 구성 옵션을 지원합니다.

## <a name="password-rule-enforcement"></a>암호 규칙 적용

등록 또는 암호 재설정 중에 최종 사용자는 복잡성 규칙을 충족하는 암호를 제공해야 합니다. 암호 복잡성 규칙은 사용자 흐름별로 적용됩니다. 등록 하는 동안 한 사용자 흐름에 4 자리 pin이 필요 하 고, 다른 사용자 흐름에는 등록 하는 동안 8 개의 문자열이 필요 합니다. 예를 들어 자식에 대한 경우보다 성인에 다양한 암호 복잡성을 포함한 사용자 흐름을 사용할 수 있습니다.

암호의 복잡성은 로그인 중에 적용되지 않습니다. 사용자는 로그인 중에 해당 암호를 변경하라는 메시지를 수신하지 않습니다. 현재 복잡성 요구 사항을 충족하지 않기 때문입니다.

암호 복잡성은 다음과 같은 사용자 흐름 형식에서 구성할 수 있습니다.

- 가입 또는 로그인 사용자 흐름
- 암호 재설정 사용자 흐름

사용자 지정 정책을 사용하는 경우 [사용자 지정 정책에서 암호 복잡성을 구성](custom-policy-password-complexity.md)할 수 있습니다.

## <a name="configure-password-complexity"></a>암호 복잡도 구성

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택 하 고 Azure AD B2C 테넌트가 포함 된 디렉터리를 선택 합니다.
3. Azure Portal에서 **Azure AD B2C**를 검색 하 고 선택 합니다.
4. **사용자 흐름 (정책)** 을 선택 합니다.
2. 사용자 흐름을 선택하고, **속성**을 클릭합니다.
3. **암호 복잡성**에서 이 사용자 흐름에 대한 암호 복잡성을 **단순**, **강력** 또는 **사용자 지정**으로 변경합니다.

### <a name="comparison-chart"></a>비교 차트

| 복잡성 | Description |
| --- | --- |
| 단순성 | 암호는 적어도 8~64자입니다. |
| Strong | 암호는 적어도 8~64자입니다. 소문자, 대문자, 숫자 또는 기호와 같은 4개 항목 중 3가지가 필요합니다. |
| 맞춤형 서비스 | 이 옵션을 통해 암호 복잡성 규칙을 대부분 제어할 수 있습니다.  사용자 지정 길이를 구성할 수 있습니다.  숫자 전용 암호(PIN)를 허용할 수 있습니다. |

## <a name="custom-options"></a>사용자 지정 옵션

### <a name="character-set"></a>문자 집합

숫자 전용(PIN) 또는 전체 문자 집합을 허용할 수 있습니다.

- **번호만 해당**을 사용하면 암호를 입력하는 동안 숫자(0~9)만을 허용합니다.
- **모두**를 사용하면 모든 문자, 숫자 또는 기호를 허용합니다.

### <a name="length"></a>길이

암호의 길이 요구 사항을 제어할 수 있습니다.

- **최소 길이**는 적어도 4여야 합니다.
- **최대 길이**는 최소 길이 이상이어야 하며 최대 64자까지 가능합니다.

### <a name="character-classes"></a>문자 클래스

암호에 사용되는 다양한 문자 형식을 제어할 수 있습니다.

- **소문자, 대문자, 숫자 (0-9), 기호 등 4개 항목 중 2가지**를 통해 암호에 두 개 이상의 문자 형식을 포함하도록 합니다. 예를 들어, 숫자 및 소문자입니다.
- **소문자, 대문자, 숫자 (0-9), 기호 등 4개 항목 중 3가지**를 통해 암호에 두 개 이상의 문자 형식을 포함하도록 합니다. 예를 들어, 숫자, 소문자 및 대문자입니다.
- **소문자, 대문자, 숫자 (0-9), 기호 등 4개 항목 중 4가지**를 통해 암호에 전체 문자 형식을 포함하도록 합니다.

    > [!NOTE]
    > **4개 항목 중 4가지**로 인해 최종 사용자 불만이 발생할 수 있습니다. 일부 연구에서는 이 요구 사항이 암호 엔트로피를 개선하지 않는다고 합니다. [NIST 암호 지침](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)을 참조하세요.
