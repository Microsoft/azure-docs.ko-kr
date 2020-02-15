---
title: Visual Studio WebApi 프로젝트에서 Azure AD 시작
description: Visual Studio 연결 서비스를 사용하여 Azure AD를 만들거나 연결한 후에 WebApi 프로젝트에 Azure Active Directory를 사용하여 시작하는 방법입니다.
author: ghogen
manager: jillfra
ms.assetid: bf1eb32d-25cd-4abf-8679-2ead299fedaa
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: aaddev, vs-azure
ms.openlocfilehash: 33894e237144628634c3b63393130eb0af5b9877
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159457"
---
# <a name="get-started-with-azure-active-directory-webapi-projects"></a>Azure Active Directory 시작(WebApi 프로젝트)

> [!div class="op_single_selector"]
> - [시작](vs-active-directory-webapi-getting-started.md)
> - [변경된 내용](vs-active-directory-webapi-what-happened.md)

이 아티클에서는 Visual Studio의 **프로젝트 > 연결된 서비스** 명령을 통해 ASP.NET WebAPI 프로젝트에 Active Directory를 추가한 후의 추가 지침을 제공합니다. 프로젝트에 서비스를 아직 추가하지 않은 경우 언제든지 추가할 수 있습니다.

연결된 서비스를 추가할 때 프로젝트에서 변경되는 내용은 [내 WebAPI 프로젝트가 어떻게 되었습니까?](vs-active-directory-webapi-what-happened.md)를 참조하세요.

## <a name="requiring-authentication-to-access-controllers"></a>컨트롤러에 액세스하려면 인증 필요

프로젝트의 모든 컨트롤러는 `[Authorize]` 특성으로 표시되었습니다. 이 특성으로 인해 사용자가 인증해야만 이러한 컨트롤러에서 정의한 API에 액세스할 수 있습니다. 컨트롤러에 익명으로 액세스할 수 있도록 하려면 컨트롤러에서 이 특성을 제거하세요. 더 세부적인 수준에서 권한을 설정하려면 특성을 컨트롤러 클래스에 적용하는 대신 인증이 필요한 각 메서드에 적용하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 인증 시나리오](authentication-scenarios.md)
- [ASP.NET 웹앱에 Microsoft에 로그인 추가](quickstart-v2-aspnet-webapp.md)
