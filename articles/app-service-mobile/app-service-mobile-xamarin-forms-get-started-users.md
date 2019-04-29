---
title: Xamarin.Forms 앱에서 Mobile Apps에 대한 인증 시작 | Microsoft Docs
description: Mobile Apps를 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Xamarin Forms 앱 사용자를 인증하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: xamarin
author: panarasi
manager: crdun
editor: ''
ms.assetid: 9c55e192-c761-4ff2-8d88-72260e9f6179
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: d97c4689b40dd0bcf2ab083c688c547014a4de0b
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62113553"
---
# <a name="add-authentication-to-your-xamarin-forms-app"></a>Xamarin Forms 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## <a name="overview"></a>개요
이 항목에서는 클라이언트 애플리케이션에서 App Service 모바일 앱의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 App Service가 지원하는 ID 공급자를 사용하여 Xamarin.Forms 빠른 시작 프로젝트에 인증을 추가합니다. 모바일 앱에서 인증이 완료되고 권한이 부여되고 나면 사용자 ID 값이 표시되고 제한된 테이블 데이터에 액세스할 수 있게 됩니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 통한 최상의 결과를 얻기 위해 먼저 [Xamarin.Forms 앱 만들기][1] 자습서를 완료하는 것이 좋습니다. 이 자습서를 완료하면 다중 플랫폼 TodoList 앱인 Xamarin.Forms 프로젝트가 생깁니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 인증 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용][2]을 참조하세요.

## <a name="register-your-app-for-authentication-and-configure-app-services"></a>인증을 위해 앱 등록 및 App Services 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>허용되는 외부 리디렉션 URL에 앱 추가

보안 인증을 위해서는 앱에 대한 새로운 URL 체계를 정의해야 합니다. 이를 통해 인증 시스템은 인증 프로세스가 완료되면 앱으로 다시 리디렉션될 수 있습니다. 이 자습서에서는 전체적으로 URL 체계 _appname_을 사용합니다. 그러나 선택한 어떤 URL 체계도 사용 가능합니다. 이 체계는 모바일 애플리케이션에 고유해야 합니다. 서버 쪽에서 리디렉션을 사용하도록 설정하려면:

1. [Azure Portal][8]에서 해당 App Service를 선택합니다.

2. **인증/권한 부여** 메뉴 옵션을 클릭합니다.

3. **허용되는 외부 리디렉션 URL**에서 `url_scheme_of_your_app://easyauth.callback`을 입력합니다.  이 문자열의 **url_scheme_of_your_app**은 모바일 애플리케이션에 대한 URL 체계입니다.  이 체계는 프로토콜에 대한 일반 URL 사양을 따라야 합니다(문자 및 숫자만 사용하고 문자로 시작).  여러 위치에서 URL 체계에 따라 모바일 애플리케이션 코드를 조정해야 할 경우 선택한 문자열을 적어두어야 합니다.

4. **확인**을 클릭합니다.

5. **저장**을 클릭합니다.

## <a name="restrict-permissions-to-authenticated-users"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-to-the-portable-class-library"></a>이식 가능한 클래스 라이브러리에 인증 추가
Mobile Apps는 [MobileServiceClient][4]에서 [LoginAsync][3] 확장 메서드를 사용하여 App Service 인증으로 사용자를 로그인합니다. 이 샘플에서는 서버 관리 인증 흐름을 사용하여 앱에서 공급자의 로그인 인터페이스를 표시합니다. 자세한 내용은 [서버 관리 인증][5]을 참조하세요. 프로덕션 앱에서 향상된 사용자 환경을 제공하기 위해 대신 [클라이언트 관리 인증][6]을 사용하는 것을 고려할 수 있습니다.

Xamarin Forms 프로젝트를 사용하여 인증하기 위해서 앱에 대한 이식 가능한 클래스 라이브러리에 **IAuthenticate** 인터페이스를 정의합니다. 또한 이식 가능한 클래스 라이브러리에 정의된 사용자 인터페이스를 업데이트하여 **로그인** 단추를 추가합니다. 사용자는 이 단추를 클릭하여 인증을 시작합니다. 인증에 성공하면 Mobile App 백 엔드에서 데이터가 로드됩니다.

앱에서 지원되는 각 플랫폼에 대해 **IAuthenticate** 인터페이스를 구현합니다.

1. Visual Studio 또는 Xamarin Studio에서 이름에 **이식 가능**이 있는 프로젝트(이식 가능한 클래스 라이브러리 프로젝트)에서 App.cs를 연 후 다음 `using` 문을 추가합니다.

        using System.Threading.Tasks;
2. App.cs에 `App` 클래스 정의 직전에 다음과 같은 `IAuthenticate` 인터페이스 정의를 추가합니다.

        public interface IAuthenticate
        {
            Task<bool> Authenticate();
        }
3. 플랫폼 전용 구현으로 인터페이스를 초기화하도록 **App** 클래스에 다음 정적 멤버를 추가합니다.

        public static IAuthenticate Authenticator { get; private set; }

        public static void Init(IAuthenticate authenticator)
        {
            Authenticator = authenticator;
        }
4. 이식 가능한 클래스 라이브러리 프로젝트에서 TodoList.xaml을 열고 **buttonsPanel** 레이아웃 요소의 다음 *Button* 요소를 기존 단추 뒤에 추가합니다.

          <Button x:Name="loginButton" Text="Sign-in" MinimumHeightRequest="30"
            Clicked="loginButton_Clicked"/>

    이 단추는 모바일 앱 백 엔드로 서버 관리 인증을 트리거합니다.
5. 이식 가능한 클래스 라이브러리 프로젝트에서 TodoList.xaml.cs를 연 후 다음 필드를 `TodoList` 클래스에 추가합니다.

        // Track whether the user has authenticated.
        bool authenticated = false;
6. **OnAppearing** 메서드를 다음 코드로 바꿉니다.

        protected override async void OnAppearing()
        {
            base.OnAppearing();

            // Refresh items only when authenticated.
            if (authenticated == true)
            {
                // Set syncItems to true in order to synchronize the data
                // on startup when running in offline mode.
                await RefreshItems(true, syncItems: false);

                // Hide the Sign-in button.
                this.loginButton.IsVisible = false;
            }
        }

    이렇게 코드를 변경하면 사용자가 인증된 후에만 데이터가 새로 고침됩니다.
7. **TodoList** 클래스에 **Clicked** 이벤트에 대한 다음 처리기를 추가합니다.

        async void loginButton_Clicked(object sender, EventArgs e)
        {
            if (App.Authenticator != null)
                authenticated = await App.Authenticator.Authenticate();

            // Set syncItems to true to synchronize the data on startup when offline is enabled.
            if (authenticated == true)
                await RefreshItems(true, syncItems: false);
        }
8. 변경 내용을 저장하고 이식 가능한 클래스 라이브러리 프로젝트를 다시 빌드하여 오류가 없는지 확인합니다.

## <a name="add-authentication-to-the-android-app"></a>Android 앱에 인증 추가
이 섹션에는 Android 앱 프로젝트에서 **IAuthenticate** 인터페이스를 구현하는 방법을 보여 줍니다. Android 디바이스를 지원하지 않는 경우 이 섹션을 건너뜁니다.

1. Visual Studio 또는 Xamarin Studio에서 **droid** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭합니다.
2. F5 키를 눌러 디버거에서 프로젝트를 실행하고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 백 엔드에서 액세스를 인증된 사용자만으로 제한했기 때문에 401 코드가 생성됩니다.
3. Android 프로젝트에서 MainActivity.cs를 열고 다음 `using` 문을 추가합니다.

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. 다음과 같이 **IAuthenticate** 인터페이스를 구현하도록 **MainActivity** 클래스를 업데이트합니다.

        public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate
5. 다음과 같이 **IAuthenticate** 인터페이스에 필요한 **MobileServiceUser** 필드 및 **Authenticate** 메서드를 추가하여 **MainActivity** 클래스를 업데이트합니다.

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this, 
                    MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                if (user != null)
                {
                    message = string.Format("you are now signed-in as {0}.",
                        user.UserId);
                    success = true;
                }
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display the success or failure message.
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(message);
            builder.SetTitle("Sign-in result");
            builder.Create().Show();

            return success;
        }

    Facebook 이외의 ID 공급자를 사용하는 경우 [MobileServiceAuthenticationProvider][7]에 대해 다른 값을 선택합니다.

6. `<application>` 요소 내에 다음 XML을 추가하여**AndroidManifest.xml** 파일을 업데이트합니다.

    ```xml
    <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
      <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
      </intent-filter>
    </activity>
    ```
    `{url_scheme_of_your_app}`을 URL 스키마로 바꿉니다.
7. `LoadApplication()`에 대한 호출 이전에 **MainActivity** 클래스의 **OnCreate** 메서드에 다음 코드를 추가합니다.

        // Initialize the authenticator before loading the app.
        App.Init((IAuthenticate)this);

    이 코드를 사용하면 앱이 로드되기 전에 인증자가 초기화됩니다.
8. 앱을 다시 빌드하고 실행한 후 선택한 인증 공급자를 사용하여 로그인하고 인증된 사용자로 데이터에 액세스할 수 있는지 확인합니다.

### <a name="troubleshooting"></a>문제 해결

**응용 프로그램 작동이 중단되고 `Java.Lang.NoSuchMethodError: No static method startActivity`** 메시지가 표시됨

지원 패키지의 충돌 현상이 Visual Studio에서는 경고로만 표시되는데 런타임에 애플리케이션 작동이 중단되고 이 예외가 표시되는 경우가 있습니다. 이 경우에는 프로젝트에서 참조하는 모든 지원 패키지의 버전이 동일한지 확인해야 합니다. [Azure Mobile Apps NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)에는 Android 플랫폼에 대한 `Xamarin.Android.Support.CustomTabs` 종속성이 있으므로 프로젝트에서 최신 지원 패키지를 사용하는 경우 충돌 방지를 위해 필수 버전이 포함된 이 패키지를 직접 설치해야 합니다.

## <a name="add-authentication-to-the-ios-app"></a>iOS 앱에 인증 추가
이 섹션에는 iOS 앱 프로젝트에서 **IAuthenticate** 인터페이스를 구현하는 방법을 보여 줍니다. iOS 디바이스를 지원하지 않는 경우 이 섹션을 건너뜁니다.

1. Visual Studio 또는 Xamarin Studio에서 **iOS** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭합니다.
2. F5 키를 눌러 디버거에서 프로젝트를 실행하고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 백 엔드에서 액세스를 인증된 사용자만으로 제한했기 때문에 401 응답이 생성됩니다.
3. iOS 프로젝트에서 AppDelegate.cs를 열고 다음 `using` 문을 추가합니다.

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
4. 다음과 같이 **IAuthenticate** 인터페이스를 구현하도록 **AppDelegate** 클래스를 업데이트합니다.

        public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate
5. 다음과 같이 **IAuthenticate** 인터페이스에 필요한 **MobileServiceUser** 필드 및 **Authenticate** 메서드를 추가하여 **AppDelegate** 클래스를 업데이트합니다.

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            var message = string.Empty;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                        success = true;
                    }
                }
            }
            catch (Exception ex)
            {
               message = ex.Message;
            }

            // Display the success or failure message.
            UIAlertView avAlert = new UIAlertView("Sign-in result", message, null, "OK", null);
            avAlert.Show();

            return success;
        }

    Facebook 이외의 ID 공급자를 사용하는 경우 [MobileServiceAuthenticationProvider]에 대해 다른 값을 선택합니다.
    
6. 다음과 같이 **OpenUrl** 메서드 오버로드를 추가하여 **AppDelegate** 클래스를 업데이트합니다.

        public override bool OpenUrl(UIApplication app, NSUrl url, NSDictionary options)
        {
            return TodoItemManager.DefaultManager.CurrentClient.ResumeWithURL(url);
        }
   
7. `LoadApplication()`에 대한 호출 이전에 **FinishedLaunching** 메서드에 다음 코드 줄을 추가합니다.

        App.Init(this);

    이 코드를 사용하면 앱이 로드되기 전에 인증자가 초기화됩니다.

8. Info.plist를 열고 **URL 형식**을 추가합니다. **식별자**를 선택한 이름으로 설정하고, **URL 스키마**를 앱의 URL 스키마로 설정하고, **역할**을 없음으로 설정합니다.

9. 앱을 다시 빌드하고 실행한 후 선택한 인증 공급자를 사용하여 로그인하고 인증된 사용자로 데이터에 액세스할 수 있는지 확인합니다.

## <a name="add-authentication-to-windows-10-including-phone-app-projects"></a>Windows 10(Phone 포함) 앱 프로젝트에 인증 추가
이 섹션에는 Windows 10 앱 프로젝트에서 **IAuthenticate** 인터페이스를 구현하는 방법을 보여 줍니다. 동일한 단계가 UWP(유니버설 Windows 플랫폼) 프로젝트에도 적용되지만 **UWP** 프로젝트(명시된 변경 내용 포함)를 사용합니다. Windows 디바이스를 지원하지 않는 경우 이 섹션을 건너뜁니다.

1. Visual Studio에서 **UWP** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. F5 키를 눌러 디버거에서 프로젝트를 실행하고 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 백 엔드에서 액세스를 인증된 사용자만으로 제한했기 때문에 401 응답이 발생합니다.
3. Windows 앱 프로젝트에 대한 MainPage.xaml.cs를 열고 다음 `using` 문을 추가합니다.

        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.UI.Popups;
        using <your_Portable_Class_Library_namespace>;

    `<your_Portable_Class_Library_namespace>` 를 이식 가능한 클래스 라이브러리의 네임스페이스로 바꿉니다.
4. 다음과 같이 **IAuthenticate** 인터페이스를 구현하도록 **MainPage** 클래스를 업데이트합니다.

        public sealed partial class MainPage : IAuthenticate
5. 다음과 같이 **IAuthenticate** 인터페이스에 필요한 **MobileServiceUser** 필드 및 **Authenticate** 메서드를 추가하여 **MainPage** 클래스를 업데이트합니다.

        // Define an authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            string message = string.Empty;
            var success = false;

            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient
                        .LoginAsync(MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    if (user != null)
                    {
                        success = true;
                        message = string.Format("You are now signed-in as {0}.", user.UserId);
                    }
                }

            }
            catch (Exception ex)
            {
                message = string.Format("Authentication Failed: {0}", ex.Message);
            }

            // Display the success or failure message.
            await new MessageDialog(message, "Sign-in result").ShowAsync();

            return success;
        }

    Facebook 이외의 ID 공급자를 사용하는 경우 [MobileServiceAuthenticationProvider][7]에 대해 다른 값을 선택합니다.

1. `LoadApplication()`에 대한 호출 이전에 **MainPage** 클래스에 대한 생성자에 다음 코드 줄을 추가합니다.

        // Initialize the authenticator before loading the app.
        <your_Portable_Class_Library_namespace>.App.Init(this);

    `<your_Portable_Class_Library_namespace>` 를 이식 가능한 클래스 라이브러리의 네임스페이스로 바꿉니다.

3. **UWP**를 사용하는 경우 **App** 클래스에 다음 **OnActivated** 메서드 재정의를 추가합니다.

       protected override void OnActivated(IActivatedEventArgs args)
       {
           base.OnActivated(args);

            if (args.Kind == ActivationKind.Protocol)
            {
                ProtocolActivatedEventArgs protocolArgs = args as ProtocolActivatedEventArgs;
                MobileServiceClientExtensions.ResumeWithURL(TodoItemManager.DefaultManager.CurrentClient,protocolArgs.Uri);
            }
       }

3. Package.appxmanifest를 열고 **프로토콜** 선언을 추가합니다. **표시 이름**을 선택한 이름으로 설정하고, **이름**을 앱의 URL 스키마로 설정합니다.

4. 앱을 다시 빌드하고 실행한 후 선택한 인증 공급자를 사용하여 로그인하고 인증된 사용자로 데이터에 액세스할 수 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계
이 기본 인증 자습서를 완료했으므로 다음 자습서 중 하나를 계속하는 것을 고려해보세요.

* [앱에 푸시 알림 추가](app-service-mobile-xamarin-forms-get-started-push.md)

   앱에 푸시 알림 지원을 추가하고 모바일 앱 백 엔드를 구성하여 푸시 알림을 보내는 Azure Notification Hubs를 사용하는 방법을 알아봅니다.
* [앱에 오프라인 동기화 사용](app-service-mobile-xamarin-forms-get-started-offline-data.md)

  모바일 앱 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다.

<!-- Images. -->

<!-- URLs. -->
[1]: app-service-mobile-xamarin-forms-get-started.md
[2]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[3]: https://msdn.microsoft.com/library/azure/dn268341(v=azure.10).aspx
[4]: https://msdn.microsoft.com/library/azure/JJ553674(v=azure.10).aspx
[5]: app-service-mobile-dotnet-how-to-use-client-library.md#serverflow
[6]: app-service-mobile-dotnet-how-to-use-client-library.md#clientflow
[7]: https://msdn.microsoft.com/library/azure/jj730936(v=azure.10).aspx
[8]: https://portal.azure.com
