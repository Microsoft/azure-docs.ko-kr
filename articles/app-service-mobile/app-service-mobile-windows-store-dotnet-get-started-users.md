---
title: UWP(유니버설 Windows 플랫폼) 앱에 인증 추가 | Microsoft Docs
description: Azure App Service Mobile Apps를 사용 하 여 다양 한 등의 id 공급자를 사용 하 여 유니버설 Windows 플랫폼 (UWP) 앱의 사용자를 인증 하는 방법을 알아봅니다. AAD, Google, Facebook, Twitter 및 Microsoft
services: app-service\mobile
documentationcenter: windows
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 6cffd951-893e-4ce5-97ac-86e3f5ad9466
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 07/05/2017
ms.author: panarasi
ms.openlocfilehash: 7caaa1ca4cdaf7290b7ce05d17c07e565e7b51d1
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128001"
---
# <a name="add-authentication-to-your-windows-app"></a>Windows 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

이 항목에서는 모바일 앱에 클라우드 기반 인증을 추가하는 방법을 보여줍니다. 이 자습서에서는 Azure App Service가 지원하는 ID 공급자를 사용하여 Mobile Apps용 UWP(유니버설 Windows 플랫폼) 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 앱 백 엔드에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서는 Mobile Apps 퀵 스타트를 기반으로 합니다. 먼저 [Mobile Apps 시작](app-service-mobile-windows-store-dotnet-get-started.md)자습서를 완료해야 합니다.

## <a name="register"></a>인증을 위해 앱 등록 및 App Service 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>허용되는 외부 리디렉션 URL에 앱 추가

보안 인증을 위해서는 앱에 대한 새로운 URL 체계를 정의해야 합니다. 이를 통해 인증 시스템은 인증 프로세스가 완료되면 앱으로 다시 리디렉션될 수 있습니다. 이 자습서에서는 전체적으로 URL 체계 _appname_을 사용합니다. 그러나 선택한 어떤 URL 체계도 사용 가능합니다. 이 체계는 모바일 애플리케이션에 고유해야 합니다. 서버 쪽에서 리디렉션을 사용하도록 설정하려면:

1. [Azure Portal](https://ms.portal.azure.com)에서 해당 App Service를 선택합니다.

2. **인증/권한 부여** 메뉴 옵션을 클릭합니다.

3. **허용되는 외부 리디렉션 URL**에서 `url_scheme_of_your_app://easyauth.callback`을 입력합니다.  이 문자열의 **url_scheme_of_your_app**은 모바일 애플리케이션에 대한 URL 체계입니다.  이 체계는 프로토콜에 대한 일반 URL 사양을 따라야 합니다(문자 및 숫자만 사용하고 문자로 시작).  여러 위치에서 URL 체계에 따라 모바일 애플리케이션 코드를 조정해야 할 경우 선택한 문자열을 적어두어야 합니다.

4. **저장**을 클릭합니다.

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

이제 백 엔드에 대한 익명 액세스가 비활성화되었는지 확인할 수 있습니다. 시작 프로젝트로 설정된 UWP 앱 프로젝트를 사용하여 앱을 배포하고 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 이 예외는 앱이 인증되지 않은 사용자로 모바일 앱 코드에 액세스하려고 시도하는데 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 App Service에서 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

## <a name="add-authentication"></a>앱에 인증 추가
1. UWP 앱 프로젝트 파일 MainPage.xaml.cs에서 다음 코드 조각을 추가합니다.
   
        // Define a member variable for storing the signed-in user. 
        private MobileServiceUser user;
   
        // Define a method that performs the authentication process
        // using a Facebook sign-in. 
        private async System.Threading.Tasks.Task<bool> AuthenticateAsync()
        {
            string message;
            bool success = false;
            try
            {
                // Change 'MobileService' to the name of your MobileServiceClient instance.
                // Sign-in using Facebook authentication.
                user = await App.MobileService
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                message =
                    string.Format("You are now signed in - {0}", user.UserId);
   
                success = true;
            }
            catch (InvalidOperationException)
            {
                message = "You must log in. Login Required";
            }
   
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
            return success;
        }
   
    이 코드는 Facebook 로그인으로 사용자를 인증합니다. Facebook 이외의 ID 공급자를 사용하는 경우 위의 **MobileServiceAuthenticationProvider** 값을 공급자에 대한 값으로 변경합니다.
2. MainPage.xaml.cs에서 **OnNavigatedTo()** 메서드를 바꿉니다. 다음으로, 인증을 트리거하는 앱에 **로그인** 단추를 추가합니다.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (e.Parameter is Uri)
            {
                App.MobileService.ResumeWithURL(e.Parameter as Uri);
            }
        }

3. MainPage.xaml.cs에 다음 코드 조각을 추가합니다.
   
        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile app.
            if (await AuthenticateAsync())
            {
                // Switch the buttons and load items from the mobile app.
                ButtonLogin.Visibility = Visibility.Collapsed;
                ButtonSave.Visibility = Visibility.Visible;
                //await InitLocalStoreAsync(); //offline sync support.
                await RefreshTodoItems();
            }
        }
4. MainPage.xaml 프로젝트 파일을 열고 **저장** 단추를 정의하는 요소를 찾아서 다음 코드로 바꿉니다.
   
        <Button Name="ButtonSave" Visibility="Collapsed" Margin="0,8,8,0" 
                Click="ButtonSave_Click">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Add"/>
                <TextBlock Margin="5">Save</TextBlock>
            </StackPanel>
        </Button>
        <Button Name="ButtonLogin" Visibility="Visible" Margin="0,8,8,0" 
                Click="ButtonLogin_Click" TabIndex="0">
            <StackPanel Orientation="Horizontal">
                <SymbolIcon Symbol="Permissions"/>
                <TextBlock Margin="5">Sign in</TextBlock> 
            </StackPanel>
        </Button>
5. App.xaml.cs에 다음 코드 조각을 추가합니다.

        protected override void OnActivated(IActivatedEventArgs args)
        {
            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                Frame content = Window.Current.Content as Frame;
                if (content.Content.GetType() == typeof(MainPage))
                {
                    content.Navigate(typeof(MainPage), protocolArgs.Uri);
                }
            }
            Window.Current.Activate();
            base.OnActivated(args);
        }
6. Package.appxmanifest 파일을 열고 **선언**으로 이동한 후 **사용 가능한 선언** 드롭다운 목록에서 **프로토콜**을 선택하고 **추가** 단추를 클릭합니다. 이제 **프로토콜** 선언의 **속성**을 구성합니다. **표시 이름**에서 애플리케이션의 사용자에게 표시할 이름을 추가합니다. **이름**에 {url_scheme_of_your_app}를 추가합니다.
7. F5 키를 눌러 앱을 실행하고 **로그인** 단추를 클릭한 다음 선택한 ID 공급자로 앱에 로그인합니다. 성공적으로 로그인되고 나면 앱이 오류 없이 실행되고 백 엔드를 쿼리하여 데이터를 업데이트할 수 있습니다.

## <a name="tokens"></a>클라이언트에 인증 토큰 저장
이전 예제에서는 앱이 시작될 때마다 클라이언트가 ID 공급자와 App Service 둘 다에 접근해야 하는 표준 로그인을 보여 주었습니다. 이 방법은 비효율적일 뿐 아니라 많은 고객이 동시에 앱을 시작하려고 할 경우 사용 관련 문제가 발생할 수도 있습니다. 더 나은 접근 방법은 App Service에서 반환된 권한 부여 토큰을 캐시한 다음 공급자 기반 로그인을 사용하기 전에 이 토큰을 먼저 사용하는 것입니다.

> [!NOTE]
> 클라이언트 관리 인증 또는 서비스 관리 인증을 사용하는지에 관계없이 App Services에서 발급된 토큰을 캐시할 수 있습니다. 이 자습서에서는 서비스 관리 인증을 사용합니다.
> 
> 

[!INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"></a>다음 단계
이 기본 인증 자습서를 완료했으므로 다음 자습서 중 하나를 계속하는 것을 고려해보세요.

* [앱에 푸시 알림 추가](app-service-mobile-windows-store-dotnet-get-started-push.md)  
   앱에 푸시 알림 지원을 추가하고 모바일 앱 백 엔드를 구성하여 푸시 알림을 보내는 Azure Notification Hubs를 사용하는 방법을 알아봅니다.
* [앱에 오프라인 동기화 사용](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
   모바일 앱 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱&mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다.

<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
