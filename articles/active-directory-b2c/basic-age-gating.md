---
title: Azure Active Directory B2C에서 나이 제한 사용 | Microsoft Docs
description: 애플리케이션을 사용하여 미성년자를 식별하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1fc63b222fd2f08bb4b5596d58f825c8f6b1910e
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836239"
---
# <a name="enable-age-gating-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 나이 제한 사용

>[!IMPORTANT]
>이 기능은 공개 미리 보기 상태입니다. 프로덕션 애플리케이션에는 이 기능을 사용하지 마세요.
>

Azure AD B2C (Azure Active Directory B2C)에서 응용 프로그램을 사용 하려는 미성년자 미성년자를 식별할 수 있습니다. 미성년자가 애플리케이션에 로그인하지 못하도록 차단할 수 있습니다. 또한 사용자가 애플리케이션으로 돌아가서 나이 그룹 및 보호자의 동의 상태를 확인할 수도 있습니다. Azure AD B2C는 보호자의 동의 없이 미성년자를 차단할 수 있습니다. 또한 Azure AD B2C는 애플리케이션에서 미성년자를 통해 수행할 작업을 결정할 수 있도록 설정할 수도 있습니다.

[사용자 흐름](user-flow-overview.md)에서 나이 제어를 사용 하도록 설정 하 고 나면 사용자가 태어난 경우 사용자에 게 해당 사용자가 거주 하는 국가/지역에 대한 메시지가 표시 됩니다. 이전에 정보를 입력하지 않은 사용자가 로그인하는 경우 다음에 로그인할 때 해당 정보를 입력해야 합니다. 사용자가 로그인할 때마다 규칙이 적용됩니다.

Azure AD B2C는 사용자가 입력하는 정보를 사용하여 미성년자인지 여부를 식별합니다. 그런 다음, **ageGroup** 필드가 해당 계정에서 업데이트됩니다. 값은 `null`, `Undefined`, `Minor`, `Adult` 및 `NotAdult`일 수 있습니다.  그런 다음, **ageGroup** 및 **consentProvidedForMinor** 필드를 사용하여 **legalAgeGroupClassification**의 값을 계산합니다.

나이 제한에는 두 개의 나이 값이 포함됩니다. 즉, 누군가가 더 이상 미성년자로 간주되지 않는 나이와 보호자의 동의를 받아야 하는 미성년자의 나이입니다. 다음 표에는 미성년자 및 보호자의 동의가 필요한 미성년자를 정의하는 데 사용되는 나이 규칙이 나와 있습니다.

| 국가/지역 | 국가/지역 이름 | 보호자의 동의가 필요한 미성년자의 나이 | 미성년자의 나이 |
| -------------- | ------------------- | ----------------- | --------- |
| 기본값 | 없음 | 없음 | 18 |
| AE | 아랍에미리트 | 없음 | 21 |
| AT | 오스트리아 | 14 | 18 |
| BE | 벨기에 | 14 | 18 |
| BG | 불가리아 | 16 | 18 |
| BH | 바레인 | 없음 | 21 |
| CM | 카메룬 | 없음 | 21 |
| CY | 키프로스 | 16 | 18 |
| CZ | 체코 공화국 | 16 | 18 |
| DE | 독일 | 16 | 18 |
| DK | 덴마크 | 16 | 18 |
| EE | 에스토니아 | 16 | 18 |
| EG | 이집트 | 없음 | 21 |
| ES | 스페인 | 13 | 18 |
| FR | 프랑스 | 16 | 18 |
| GB | 영국 | 13 | 18 |
| GR | 그리스 | 16 | 18 |
| HR | 크로아티아 | 16 | 18 |
| HU | 헝가리 | 16 | 18 |
| IE | 아일랜드 | 13 | 18 |
| IT | 이탈리아 | 16 | 18 |
| KR | 한국 | 14 | 18 |
| LT | 리투아니아 | 16 | 18 |
| LU | 룩셈부르크 | 16 | 18 |
| LV | 라트비아 | 16 | 18 |
| MT | 몰타 | 16 | 18 |
| 해당 없음 | 나미비아 | 없음 | 21 |
| NL | 네덜란드 | 16 | 18 |
| PL | 폴란드 | 13 | 18 |
| PT | 포르투갈 | 16 | 18 |
| RO | 루마니아 | 16 | 18 |
| SE | 스웨덴 | 13 | 18 |
| SG | 싱가포르 | 없음 | 21 |
| SI | 슬로베니아 | 16 | 18 |
| SK | 슬로바키아 | 16 | 18 |
| TD | 차드 | 없음 | 21 |
| TH | 태국 | 없음 | 20 |
| TW | 대만 | 없음 | 20 |
| 미국 | 미국 | 13 | 18 |

## <a name="age-gating-options"></a>나이 게이팅 옵션

### <a name="allowing-minors-without-parental-consent"></a>보호자 동의서 없는 미성년자 허용

가입, 로그인 또는 둘 모두를 허용하는 사용자 흐름의 경우 애플리케이션에서 보호자의 동의가 없는 미성년자를 허용하도록 선택할 수 있습니다. 보호자의 동의가 없는 미성년자가 정상적으로 로그인하거나 가입할 수 있으며, Azure AD B2C에서 **legalAgeGroupClassification** 클레임을 사용하여 ID 토큰을 발급합니다. 이 클레임은 보호자의 동의 수집 및  **consentProvidedForMinor** 필드 업데이트와 같은 사용자의 환경을 정의합니다.

### <a name="blocking-minors-without-parental-consent"></a>보호자 동의서 없는 미성년자 차단

가입, 로그인 또는 둘 모두를 허용하는 사용자 흐름의 경우 애플리케이션에서 보호자의 동의가 없는 미성년자를 차단하도록 선택할 수 있습니다. Azure AD B2C에서 차단된 사용자를 처리하는 데 사용할 수 있는 옵션은 다음과 같습니다.

- 애플리케이션에 JSON 다시 보내기 - 미성년자가 차단된 애플리케이션에 응답을 다시 보냅니다.
- 오류 페이지 표시 - 애플리케이션에 액세스할 수 없음을 알리는 페이지가 사용자에게 표시됩니다.

## <a name="set-up-your-tenant-for-age-gating"></a>나이 제한에 대한 테넌트 설정

사용자 흐름에서 나이 제한을 사용하려면 테넌트에서 추가 속성을 갖도록 구성해야 합니다.

1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 여 Azure AD B2C 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 테넌트가 포함된 디렉터리를 선택합니다.
2. Azure Portal의 왼쪽 위 모서리에서 **모든 서비스**를 선택하고, **Azure AD B2C**를 검색하여 선택합니다.
3. 왼쪽 메뉴에서 테넌트에 대한 **속성**을 선택합니다.
2. **나이 제한** 섹션 아래에서 **구성**을 클릭합니다.
3. 작업이 완료될 때까지 기다리면 테넌트에 대한 나이 제한이 설정됩니다.

## <a name="enable-age-gating-in-your-user-flow"></a>사용자 흐름에서 나이 제한 사용

테넌트에서 나이 제한을 사용하도록 설정되면 사용하도록 설정된 [사용자 흐름](user-flow-versions.md)에서 이 기능을 사용할 수 있습니다. 다음 단계에 따라 나이 게이팅을 사용하도록 설정합니다.

1. 나이 제한을 사용하도록 설정된 사용자 흐름을 만듭니다.
2. 사용자 흐름이 만들어지면 메뉴에서 **속성**을 선택합니다.
3. **나이 제한** 섹션에서 **사용**을 선택합니다.
4. 그런 다음, 미성년자로 식별되는 사용자를 관리하려는 방법을 결정합니다. **가입 또는 로그인**에 대해 `Allow minors to access your application` 또는 `Block minors from accessing your application`을 선택합니다. 미성년자 차단을 선택하는 경우 `Send a JSON back to the application` 또는 `Show an error message`를 선택합니다.




