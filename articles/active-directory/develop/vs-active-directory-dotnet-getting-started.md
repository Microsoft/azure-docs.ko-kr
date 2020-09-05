---
title: .NET MVC 프로젝트에서 Azure AD 시작 | Microsoft
description: Visual Studio 연결 서비스를 사용하여 Azure AD를 만들거나 연결한 후에 NET MVC 프로젝트에서 Azure Active Directory를 사용하기 시작하는 방법입니다.
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: 15a7c873e4d1e5c962a89b03f2a5cafc88843192
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165467"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Azure Active Directory(ASP.NET MVC 프로젝트) 시작

> [!div class="op_single_selector"]
> - [시작](vs-active-directory-dotnet-getting-started.md)
> - [변경된 내용](vs-active-directory-dotnet-what-happened.md)

이 아티클에서는 Visual Studio의 **프로젝트 > 연결된 서비스** 명령을 통해 ASP.NET MVC 프로젝트에 Active Directory를 추가한 후의 추가 지침을 제공합니다. 프로젝트에 서비스를 아직 추가하지 않은 경우 언제든지 추가할 수 있습니다.

연결된 서비스를 추가할 때 프로젝트에서 변경되는 내용은 [내 MVC 프로젝트가 어떻게 되었습니까?](vs-active-directory-dotnet-what-happened.md)를 참조하세요.

## <a name="requiring-authentication-to-access-controllers"></a>컨트롤러에 액세스하려면 인증 필요

프로젝트의 모든 컨트롤러는 `[Authorize]` 특성으로 표시되었습니다. 이 특성으로 인해 사용자가 인증해야만 이러한 컨트롤러에 액세스할 수 있습니다. 컨트롤러에 익명으로 액세스할 수 있도록 하려면 컨트롤러에서 이 특성을 제거하세요. 더 세부적인 수준에서 권한을 설정하려면 특성을 컨트롤러 클래스에 적용하는 대신 인증이 필요한 각 메서드에 적용하세요.

## <a name="adding-signin--signout-controls"></a>SignIn/SignOut 컨트롤 추가

보기에 SignIn/SignOut 컨트롤을 추가하려면 `_LoginPartial.cshtml` 부분 보기를 사용하여 보기 중 하나에 기능을 추가할 수 있습니다. 표준 `_Layout.cshtml` 보기에 추가한 기능의 예는 다음과 같습니다. (navbar-collapse 클래스가 포함된 div의 마지막 요소를 확인):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 인증 시나리오](./authentication-vs-authorization.md)
- [ASP.NET 웹앱에 Microsoft에 로그인 추가](quickstart-v2-aspnet-webapp.md)