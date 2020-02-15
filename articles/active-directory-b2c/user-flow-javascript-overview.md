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
ms.date: 02/10/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 78fc3af10bde5e9dd25d02f7a21d77e958b15190
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77149527"
---
# <a name="javascript-and-page-layout-versions-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 JavaScript 및 페이지 레이아웃 버전

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD B2C은 사용자 흐름 및 사용자 지정 정책의 사용자 인터페이스 요소에 대 한 HTML, CSS 및 JavaScript를 포함 하는 패키지 콘텐츠 집합을 제공 합니다.

응용 프로그램에 JavaScript를 사용 하도록 설정 하려면

* Azure Portal를 사용 하 여 사용자 흐름에서 사용 하도록 설정
* [페이지 레이아웃](page-layout.md) 선택
* 요청에서 [b2clogin.com](b2clogin.md) 사용

[Javascript](javascript-samples.md) 클라이언트 쪽 코드를 사용 하도록 설정 하려는 경우 javascript를 기반으로 하는 요소를 변경할 수 없어야 합니다. 변경할 수 없으면 사용자 페이지에서 예기치 않은 동작이 발생할 수 있습니다. 이러한 문제를 방지 하려면 페이지 레이아웃 사용을 적용 하 고 JavaScript를 기반으로 하는 콘텐츠 정의를 변경할 수 없도록 페이지 레이아웃 버전을 지정 합니다. JavaScript를 사용 하지 않으려는 경우에도 페이지에 대 한 페이지 레이아웃 버전을 지정할 수 있습니다.

## <a name="enable-javascript"></a>JavaScript 사용

사용자 흐름 **속성**에서 JavaScript를 사용 하도록 설정할 수 있습니다. JavaScript를 사용 하도록 설정 하면 페이지 레이아웃 사용도 적용 됩니다. 그런 다음, 다음 섹션에 설명 된 대로 사용자 흐름에 대 한 페이지 레이아웃 버전을 설정할 수 있습니다.

![JavaScript 설정 사용이 강조 표시 된 사용자 흐름 속성 페이지](media/user-flow-javascript-overview/javascript-settings.png)

## <a name="select-a-page-layout-version"></a>페이지 레이아웃 버전 선택

사용자 흐름의 속성에서 JavaScript를 사용 하도록 설정할지 여부에 관계 없이 사용자 흐름 페이지에 대 한 페이지 레이아웃 버전을 지정할 수 있습니다. 사용자 흐름을 열고 **페이지 레이아웃**을 선택 합니다. **레이아웃 이름**에서 사용자 흐름 페이지를 선택 하 고 **페이지 레이아웃 버전**을 선택 합니다.

여러 페이지 레이아웃 버전에 대 한 자세한 내용은 [페이지 레이아웃 버전 변경 로그](page-layout.md)를 참조 하세요.

![페이지 레이아웃 버전 드롭다운을 보여 주는 포털의 페이지 레이아웃 설정](media/user-flow-javascript-overview/page-layout-version.png)

[!INCLUDE [active-directory-b2c-javascript-guidelines](../../includes/active-directory-b2c-javascript-guidelines.md)]

## <a name="next-steps"></a>다음 단계

Javascript [샘플에서 Azure Active Directory B2C 사용 하기 위한](javascript-samples.md)javascript 사용 예제를 찾을 수 있습니다.
