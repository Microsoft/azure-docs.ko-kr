---
title: Active Directory 연결 된 서비스 사용 (Visual Studio)
description: Visual Studio 연결된 서비스 추가 대화 상자를 사용하여 Azure Active Directory 추가
author: ghogen
manager: jillfra
ms.assetid: f599de6b-e369-436f-9cdc-48a0165684cb
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.custom: aaddev, vs-azure
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 03/12/2018
ms.author: ghogen
ms.openlocfilehash: f139fc8cb59c40ea169e195312326773296b0592
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77159628"
---
# <a name="add-an-azure-active-directory-by-using-connected-services-in-visual-studio"></a>Visual Studio에서 연결된 서비스를 사용 하 여 Azure Active Directory 추가

Azure AD(Azure Active Directory)를 사용하여 ASP.NET MVC 웹 애플리케이션 또는 웹 API 서비스에서 Active Directory 인증을 위한 SSO(Single Sign-on)을 지원할 수 있습니다. Azure AD 인증을 사용하면 사용자가 Azure Active Directory의 자신의 계정을 사용하여 웹 애플리케이션에 연결할 수 있습니다. Azure AD 인증의 장점으로 웹 애플리케이션에서 API를 표시하는 경우 향상된 데이터 보안을 들 수 있습니다. Azure AD를 사용하면, 자체 계정 및 사용자 관리를 사용하는 별도 인증 시스템을 관리할 필요가 없습니다.

이 문서 및 함께 제공되는 문서에서는 Active Directory용 Visual Studio 연결된 서비스 기능 사용에 대한 세부 정보를 제공합니다. 이 기능은 Visual Studio 2015 이상에서 사용할 수 있습니다.

현재 Active Directory 연결된 서비스는 ASP.NET Core 애플리케이션을 지원하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정: Azure 계정이 없는 경우 [평가판을 등록](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)하거나 [Visual Studio 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F)할 수 있습니다.
- **Visual Studio 2015** 이상 [지금 Visual Studio를 다운로드](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs)하세요.

### <a name="connect-to-azure-active-directory-using-the-connected-services-dialog"></a>연결된 서비스 대화 상자를 사용하여 Azure Active Directory에 연결

1. Visual Studio에서 ASP.NET MVC 프로젝트 또는 ASP.NET Web API 프로젝트를 만들거나 엽니다. MVC, Web API, 단일 페이지 애플리케이션, Azure API 앱, Azure 모바일 앱 및 Azure 모바일 서비스 템플릿을 사용할 수 있습니다.

1. **프로젝트 > 연결된 서비스 추가...**  메뉴 명령을 선택하거나 솔루션 탐색기의 프로젝트 아래에서 **연결된 서비스** 노드를 두 번 클릭합니다.

1. **연결된 서비스** 페이지에서 **Azure Active Directory로 인증**을 선택합니다.

    ![연결된 서비스 페이지](./media/vs-azure-active-directory/connected-services-add-active-directory.png)

1. **소개** 페이지에서 **다음**을 선택합니다. 이 페이지에 오류가 표시되는 경우 [Azure Active Directory의 연결된 서비스를 사용하여 오류 진단](vs-active-directory-error.md)을 참조하세요.

    ![소개 페이지](./media/vs-azure-active-directory/configure-azure-ad-wizard-1.png)

1. **Single Sign-On** 페이지의 **도메인** 드롭다운 목록에서 도메인을 선택합니다. 이 목록에는 Visual Studio의 계정 설정 대화 상자 (**파일 > 계정 설정 ...** )에 나열 된 계정으로 액세스할 수 있는 모든 도메인이 포함 되어 있습니다. 또는 `mydomain.onmicrosoft.com`와 같이 찾고 있는 도메인 이름을 찾을 수 없는 경우 도메인 이름을 입력할 수 있습니다. Azure Active Directory 앱을 만들거나 기존 Azure Active Directory 앱의 설정을 사용하는 옵션을 선택할 수 있습니다. 완료되면 **다음**을 선택합니다.

    ![Single Sign-On 페이지](./media/vs-azure-active-directory/configure-azure-ad-wizard-2.png)

1. **디렉터리 액세스** 페이지에서 필요에 따라 **디렉터리 데이터 읽기** 옵션을 선택합니다. 개발자는 일반적으로 이 옵션을 포함합니다.

    ![디렉터리 액세스 페이지](./media/vs-azure-active-directory/configure-azure-ad-wizard-3.png)

1. **마침**을 선택하여 Azure AD 인증을 사용하도록 프로젝트 수정을 시작합니다. Visual Studio는 이 시간 동안 진행률을 보여줍니다.

    ![Active Directory 연결된 서비스 진행률](./media/vs-azure-active-directory/active-directory-connected-service-output.png)

1. 프로세스가 완료되면 Visual Studio는 프로젝트 형식에 따라 다음 문서 중 하나로 브라우저를 엽니다.

    - [.NET MVC 프로젝트 시작](vs-active-directory-dotnet-getting-started.md)
    - [WebAPI 프로젝트 시작](vs-active-directory-webapi-getting-started.md)

1. 또한 [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에서 Active Directory 도메인을 볼 수 있습니다.

## <a name="how-your-project-is-modified"></a>프로젝트를 수정하는 방법

연결된 서비스를 마법사에 추가하면 Visual Studio는 Azure Active Directory 및 연관된 참조를 프로젝트에 추가합니다. 또한 프로젝트의 구성 파일 및 코드 파일이 수정되어 Azure AD용 지원을 추가합니다. Visual Studio가 작성한 특정 수정 내용은 프로젝트 형식에 따라 달라집니다. 자세한 내용은 다음 문서를 참조하세요.

- [내 .NET MVC 프로젝트가 어떻게 되었습니까?](vs-active-directory-dotnet-what-happened.md)
- [내 Web API 프로젝트는 어떻게 되었나요?](vs-active-directory-webapi-what-happened.md)

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory 인증 시나리오](authentication-scenarios.md)
- [ASP.NET 웹앱에 Microsoft에 로그인 추가](quickstart-v2-aspnet-webapp.md)
