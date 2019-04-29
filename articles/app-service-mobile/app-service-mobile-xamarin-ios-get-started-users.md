---
title: Xamarin iOS에서 Mobile Apps에 대한 인증 시작
description: Mobile Apps를 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Xamarin iOS 앱 사용자를 인증하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: crdun
editor: ''
ms.assetid: 180cc61b-19c5-48bf-a16c-7181aef3eacc
ms.service: app-service-mobile
ms.workload: na
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: crdun
ms.openlocfilehash: 132909931291daf3aefddd5e1a44273050d98e06
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120912"
---
# <a name="add-authentication-to-your-xamarinios-app"></a>Xamarin.iOS 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

이 항목에서는 클라이언트 애플리케이션에서 App Service 모바일 앱의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 App Service가 지원하는 ID 공급자를 사용하여 Xamarin.iOS 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 앱에서 인증이 완료되고 권한이 부여되고 나면 사용자 ID 값이 표시되고 제한된 테이블 데이터에 액세스할 수 있게 됩니다.

[Xamarin.iOS 앱 만들기]자습서를 먼저 완료해야 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 인증 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>인증을 위해 앱 등록 및 App Services 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="add-your-app-to-the-allowed-external-redirect-urls"></a>허용되는 외부 리디렉션 URL에 앱 추가

보안 인증을 위해서는 앱에 대한 새로운 URL 체계를 정의해야 합니다. 이를 통해 인증 시스템은 인증 프로세스가 완료되면 앱으로 다시 리디렉션될 수 있습니다. 이 자습서에서는 전체적으로 URL 체계 _appname_을 사용합니다. 그러나 선택한 어떤 URL 체계도 사용 가능합니다. 이 체계는 모바일 애플리케이션에 고유해야 합니다. 서버 쪽에서 리디렉션을 사용하도록 설정하려면:

1. [Azure Portal](https://portal.azure.com/)에서 해당 App Service를 선택합니다.

2. **인증/권한 부여** 메뉴 옵션을 클릭합니다.

3. **허용되는 외부 리디렉션 URL**에서 `url_scheme_of_your_app://easyauth.callback`을 입력합니다.  이 문자열의 **url_scheme_of_your_app**은 모바일 애플리케이션에 대한 URL 체계입니다.  이 체계는 프로토콜에 대한 일반 URL 사양을 따라야 합니다(문자 및 숫자만 사용하고 문자로 시작).  여러 위치에서 URL 체계에 따라 모바일 애플리케이션 코드를 조정해야 할 경우 선택한 문자열을 적어두어야 합니다.

4. **확인**을 클릭합니다.

5. **저장**을 클릭합니다.

## <a name="restrict-permissions-to-authenticated-users"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

* Visual Studio 또는 Xamarin Studio에서 클라이언트 프로젝트를 디바이스 또는 에뮬레이터에서 실행합니다. 앱 시작 후 상태 코드가 401(권한이 부여되지 않음)인 처리되지 않은 예외가 발생했는지 확인합니다. 오류는 디버거의 콘솔에 기록됩니다. 따라서 Visual Studio의 출력 창에 오류가 표시됩니다.

    이 권한 없음 오류는 앱이 인증되지 않은 사용자로 모바일 앱 백엔드에 액세스하려고 하기 때문에 발생합니다. *TodoItem* 테이블에서 이제 인증을 요구합니다.

다음으로 클라이언트 앱을 업데이트하여 모바일 앱 백엔드에서 인증된 된 사용자를 사용하여 리소스를 요청합니다.

## <a name="add-authentication-to-the-app"></a>앱에 인증 추가
이 섹션에서는 데이터 표시 전에 로그인 화면을 표시하도록 앱을 수정합니다. 앱이 시작될 때 앱은 App Service에 연결하지 않으며, 데이터를 표시하지 않습니다. 사용자가 새로 고침 제스처를 처음 수행한 다음 로그인 화면이 나타나고, 로그인이 성공하면 todo 항목 목록이 표시됩니다.

1. 클라이언트 프로젝트에서 QSTodoService 클래스에 문 및 접근자가 있는 `MobileServiceUser`를 사용하여 **QSTodoService.cs** 파일을 열고 다음을 추가합니다.

    ```csharp
    using UIKit;

    // Logged in user
    private MobileServiceUser user;
    public MobileServiceUser User { get { return user; } }
    ```

2. 다음 정의를 포함하여 새 메서드로 명명된 **Authenticate**를 **QSTodoService**에 추가합니다.

    ```csharp
    public async Task Authenticate(UIViewController view)
    {
        try
        {
            AppDelegate.ResumeWithURL = url => url.Scheme == "{url_scheme_of_your_app}" && client.ResumeWithURL(url);
            user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }
    ```

    > [!NOTE]
    > Facebook 이외의 ID 공급자를 사용하는 경우, 위의 **LoginAsync**에 전달된 값을 _MicrosoftAccount_, _Twitter_, _Google_ 또는 _WindowsAzureActiveDirectory_ 중 하나로 변경합니다.

3. **QSTodoListViewController.cs**를 엽니다. 끝 부근의 **RefreshAsync()** 호출을 제거하여 **ViewDidLoad**의 메서드 정의를 수정합니다.

    ```csharp
    public override async void ViewDidLoad ()
    {
        base.ViewDidLoad ();

        todoService = QSTodoService.DefaultService;
        await todoService.InitializeStoreAsync();

        RefreshControl.ValueChanged += async (sender, e) => {
            await RefreshAsync();
        }

        // Comment out the call to RefreshAsync
        // await RefreshAsync();
    }
    ```

4. **User** 속성이 null인 경우 인증을 수행하려면 **RefreshAsync** 메서드를 수정합니다. 메서드 정의 상단에서 다음 코드를 추가합니다.

    ```csharp
    // start of RefreshAsync method
    if (todoService.User == null) {
        await QSTodoService.DefaultService.Authenticate(this);
        if (todoService.User == null) {
            Console.WriteLine("couldn't login!!");
            return;
        }
    }
    // rest of RefreshAsync method
    ```

5. **AppDelegate.cs**를 열고 다음 메서드를 추가합니다.

    ```csharp
    public static Func<NSUrl, bool> ResumeWithURL;

    public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
    {
        return ResumeWithURL != null && ResumeWithURL(url);
    }
    ```

6. **Info.plist** 파일을 열고 **고급** 섹션의 **URL 형식**으로 이동합니다. 이제 URL 형식의 **식별자** 및 **URL 스키마**를 구성하고 **URL 형식 추가**를 클릭합니다. **URL 스키마**는 {url_scheme_of_your_app}와 동일해야 합니다.
7. Mac 호스트에 연결된 Visual Studio 또는 Mac용 Visual Studio에서 디바이스 또는 에뮬레이터를 대상으로 하는 클라이언트 프로젝트를 실행합니다. 앱이 데이터를 표시하지 않는지 확인합니다.

    항목 목록을 아래로 끌어서 새로 고침 제스처를 수행하고 로그인 화면이 나타나도록 합니다. 유효한 자격 증명을 성공적으로 입력하면 앱이 todo 항목 목록을 표시하고 사용자가 데이터를 업데이트할 수 있습니다.

<!-- URLs. -->
[Submit an app page]: https://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: https://go.microsoft.com/fwlink/p/?LinkId=262039
[Xamarin.iOS 앱 만들기]: app-service-mobile-xamarin-ios-get-started.md
