---
title: JavaScript 및 페이지 레이아웃 버전
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C에서 JavaScript를 사용 하도록 설정 하 고 페이지 레이아웃 버전을 사용 하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 3f6d4849b02f320c7479469b4ee56be50e4f8dee
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2020
ms.locfileid: "76840097"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 JavaScript 및 페이지 레이아웃 버전

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C은 사용자 흐름 및 사용자 지정 정책의 사용자 인터페이스 요소에 대한 HTML, CSS 및 JavaScript를 포함 하는 패키지 콘텐츠 집합을 제공 합니다. 응용 프로그램에 대해 JavaScript를 사용 하도록 설정 하려면 사용자 [지정 정책](custom-policy-overview.md) 에 요소를 추가 하거나 포털에서 사용자 흐름을 사용 하도록 설정 하 고, 페이지 레이아웃을 선택 하 고, 요청에 [b2clogin.com](b2clogin.md) 를 사용 해야 합니다.

[Javascript](javascript-samples.md) 클라이언트 쪽 코드를 사용 하도록 설정 하려는 경우 javascript를 기반으로 하는 요소를 변경할 수 없도록 해야 합니다. 그렇지 않으면 사용자 페이지에서 예기치 않은 동작이 변경 될 수 있습니다. 이러한 문제를 방지 하기 위해 페이지 레이아웃 사용을 강제 적용 하 고 페이지 레이아웃 버전을 지정할 수 있습니다. 이렇게 하면 JavaScript를 기반으로 하는 모든 콘텐츠 정의를 변경할 수 없습니다. JavaScript를 사용 하지 않으려는 경우에도 페이지에 대한 페이지 레이아웃 버전을 지정할 수 있습니다.

## <a name="user-flows"></a>사용자 흐름

사용자 흐름 **속성**에서 JavaScript를 사용 하도록 설정 하 여 페이지 레이아웃을 강제로 사용할 수도 있습니다. 그런 다음, 다음 섹션에 설명 된 대로 사용자 흐름에 대한 페이지 레이아웃 버전을 설정할 수 있습니다.

![JavaScript 설정 사용이 강조 표시 된 사용자 흐름 속성 페이지](media/user-flow-javascript-overview/javascript-settings.png)

### <a name="select-a-page-layout-version"></a>페이지 레이아웃 버전 선택

사용자 흐름의 속성에서 JavaScript를 사용 하도록 설정할지 여부에 관계 없이 사용자 흐름 페이지에 대한 페이지 레이아웃 버전을 지정할 수 있습니다. 사용자 흐름을 열고 **페이지 레이아웃**을 선택 합니다. **레이아웃 이름**에서 사용자 흐름 페이지를 선택 하 고 **페이지 레이아웃 버전**을 선택 합니다.

여러 페이지 레이아웃 버전에 대한 자세한 내용은 [버전 변경 로그](page-layout.md#version-change-log)를 참조 하세요.

![페이지 레이아웃 버전 드롭다운을 보여 주는 포털의 페이지 레이아웃 설정](media/user-flow-javascript-overview/page-layout-version.png)

## <a name="custom-policies"></a>사용자 지정 정책

사용자 지정 정책에서 JavaScript를 사용 하도록 설정 하려면 사용자 지정 정책 파일의 **RelyingParty** 요소에 **scriptexecution** 요소를 추가 합니다. 자세한 내용은 [Azure Active Directory B2C에서 사용할 JavaScript 샘플](javascript-samples.md)을 참조 하세요.

사용자 지정 정책에서 JavaScript를 사용 하도록 설정할지 여부에 관계 없이 페이지에 대한 페이지 레이아웃 버전을 지정할 수 있습니다. 페이지 레이아웃을 지정 하는 방법에 대한 자세한 내용은 [사용자 지정 정책을 사용 하 여 Azure Active Directory B2C에서 페이지 레이아웃 선택](page-layout.md)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

여러 페이지 레이아웃 버전에 대한 자세한 내용은 [사용자 지정 정책을 사용 하 여 Azure Active Directory B2C에서 페이지 레이아웃 선택](page-layout.md#version-change-log)의 **버전 변경 로그** 섹션을 참조 하세요.

Javascript [샘플에서 Azure Active Directory B2C 사용 하기 위한](javascript-samples.md)javascript 사용 예제를 찾을 수 있습니다.
