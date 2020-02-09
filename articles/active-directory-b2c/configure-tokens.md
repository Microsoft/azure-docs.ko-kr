---
title: 토큰 구성-Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C에서 토큰 수명 및 호환성 설정을 구성 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d6b18596082df6f1cfbe2a47627712b8b69cb355
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76836612"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 토큰 구성

이 문서에서는 Azure Active Directory B2C (Azure AD B2C)에서 [토큰의 수명 및 호환성](tokens-overview.md) 을 구성 하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 조건

사용자가 애플리케이션에 가입하고 로그인할 수 있는 [사용자 흐름을 만듭니다](tutorial-create-user-flows.md).

## <a name="configure-token-lifetime"></a>토큰 수명 구성

사용자 흐름에서 토큰 수명을 구성할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 테넌트를 포함 하는 디렉터리를 선택 합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **사용자 흐름 (정책)** 을 선택 합니다.
5. 이전에 만든 사용자 흐름을 엽니다.
6. **속성**을 선택합니다.
7. **토큰 수명**에서 다음 속성을 응용 프로그램의 요구 사항에 맞게 조정 합니다.

    ![Azure Portal 토큰 수명 속성 설정](./media/configure-tokens/token-lifetime.png)

8. **저장**을 클릭합니다.

## <a name="configure-token-compatibility"></a>토큰 호환성 구성

1. **사용자 흐름 (정책)** 을 선택 합니다.
2. 이전에 만든 사용자 흐름을 엽니다.
3. **속성**을 선택합니다.
4. **토큰 호환성 설정**에서 다음 속성을 응용 프로그램의 요구 사항에 맞게 조정 합니다.

    ![Azure Portal의 토큰 호환성 속성 설정](./media/configure-tokens/token-compatibility.png)

5. **저장**을 클릭합니다.

## <a name="next-steps"></a>다음 단계

[액세스 토큰을 사용하는 방법](access-tokens.md)에 대해 자세히 알아보세요.



