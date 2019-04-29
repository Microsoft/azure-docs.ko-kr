---
title: Xamarin Android에서 Mobile Apps에 대한 인증 시작
description: Mobile Apps를 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Xamarin Android 앱 사용자를 인증하는 방법을 알아봅니다.
services: app-service\mobile
documentationcenter: xamarin
author: conceptdev
manager: panarasi
editor: ''
ms.assetid: 570fc12b-46a9-4722-b2e0-0d1c45fb2152
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 09/24/2018
ms.author: panarasi
ms.openlocfilehash: 0a2d964d60d13f0e71de5776112a4edbe3cdcc45
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127916"
---
# <a name="add-authentication-to-your-xamarinandroid-app"></a>Xamarin Android 앱에 인증 추가
[!INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

이 항목에서는 클라이언트 애플리케이션에서 모바일 앱의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 Azure Mobile Apps가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 앱에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서는 모바일 앱 퀵 스타트를 기반으로 합니다. 또한 [Xamarin Android 앱 만들기]자습서를 먼저 완료해야 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 인증 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

## <a name="register"></a>인증을 위해 앱 등록 및 App Services 구성
[!INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

## <a name="redirecturl"></a>허용되는 외부 리디렉션 URL에 앱 추가

보안 인증을 위해서는 앱에 대한 새로운 URL 체계를 정의해야 합니다. 이를 통해 인증 시스템은 인증 프로세스가 완료되면 앱으로 다시 리디렉션될 수 있습니다. 이 자습서에서는 전체적으로 URL 체계 _appname_을 사용합니다. 그러나 선택한 어떤 URL 체계도 사용 가능합니다. 이 체계는 모바일 애플리케이션에 고유해야 합니다. 서버 쪽에서 리디렉션을 사용하도록 설정하려면:

1. [Azure Portal]에서 해당 App Service를 선택합니다.

2. **인증/권한 부여** 메뉴 옵션을 클릭합니다.

3. **허용되는 외부 리디렉션 URL**에서 `url_scheme_of_your_app://easyauth.callback`을 입력합니다.  이 문자열의 **url_scheme_of_your_app**은 모바일 애플리케이션에 대한 URL 체계입니다.  이 체계는 프로토콜에 대한 일반 URL 사양을 따라야 합니다(문자 및 숫자만 사용하고 문자로 시작).  여러 위치에서 URL 체계에 따라 모바일 애플리케이션 코드를 조정해야 할 경우 선택한 문자열을 적어두어야 합니다.

4. **확인**을 클릭합니다.

5. **저장**을 클릭합니다.

## <a name="permissions"></a>사용 권한을 인증된 사용자로 제한
[!INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Visual Studio 또는 Xamarin Studio에서 클라이언트 프로젝트를 디바이스 또는 에뮬레이터에서 실행합니다. 앱 시작 후 상태 코드가 401(권한이 부여되지 않음)인 처리되지 않은 예외가 발생했는지 확인합니다. 이 예외는 앱이 인증되지 않은 사용자로 모바일 앱 백 엔드에 액세스하려고 시도합니다. *TodoItem* 테이블에서 이제 인증을 요구합니다.

다음으로 클라이언트 앱을 업데이트하여 모바일 앱 백엔드에서 인증된 된 사용자를 사용하여 리소스를 요청합니다.

## <a name="add-authentication"></a>앱에 인증 추가
앱은 데이터가 표시되기 전에 사용자가 **로그인** 단추를 탭하고 인증하는 것을 요구하도록 업데이트되었습니다.

1. **TodoActivity** 클래스에 다음 코드를 추가합니다.
   
        // Define an authenticated user.
        private MobileServiceUser user;
        private async Task<bool> Authenticate()
        {
                var success = false;
                try
                {
                    // Sign in with Facebook login using a server-managed flow.
                    user = await client.LoginAsync(this,
                        MobileServiceAuthenticationProvider.Facebook, "{url_scheme_of_your_app}");
                    CreateAndShowDialog(string.Format("you are now logged in - {0}",
                        user.UserId), "Logged in!");
   
                    success = true;
                }
                catch (Exception ex)
                {
                    CreateAndShowDialog(ex, "Authentication failed");
                }
                return success;
        }
   
        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds.
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;
   
                // Load the data.
                OnRefreshItemsSelected();
            }
        }
   
    이렇게 하면 사용자를 인증하는 새 메서드와 새 **로그인** 단추에 대한 메서드 처리기가 생성됩니다. 위 예제 코드의 사용자는 Facebook 로그인을 사용하여 인증됩니다. 대화 상자는 한 번 인증된 사용자 ID를 표시하는 데 사용됩니다.
   
   > [!NOTE]
   > Facebook 이외의 id 공급자를 사용 하는 경우 전달 되는 값을 변경 **LoginAsync** 위에 다음 중 하나에: *MicrosoftAccount*, *Twitter*, *Google* 또는 *WindowsAzureActiveDirectory* 중 하나로 변경합니다.
   > 
   > 
2. **OnCreate** 메서드에서 다음 코드 줄을 삭제하거나 주석으로 처리합니다.
   
        OnRefreshItemsSelected ();
3. Activity_To_Do.axml 파일에서 기존의 *AddItem* 단추 전에 다음 *LoginUser* 단추 정의를 추가합니다.
   
          <Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />
4. Strings.xml 리소스 파일에 다음 요소를 추가합니다.
   
        <string name="login_button_text">Sign in</string>
5. AndroidManifest.xml 파일을 열고 `<application>` XML 요소 내에 다음 코드를 추가합니다.

        <activity android:name="com.microsoft.windowsazure.mobileservices.authentication.RedirectUrlActivity" android:launchMode="singleTop" android:noHistory="true">
          <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="{url_scheme_of_your_app}" android:host="easyauth.callback" />
          </intent-filter>
        </activity>

6. Visual Studio 또는 Xamarin Studio에서 클라이언트 프로젝트를 디바이스 또는 에뮬레이터에서 실행하고 선택된 ID 공급자로 로그인합니다. 성공적으로 로그인하면 앱이 로그인 ID 및 todo 항목 목록을 표시하고 사용자가 데이터를 업데이트할 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

**응용 프로그램 작동이 중단되고 `Java.Lang.NoSuchMethodError: No static method startActivity`** 메시지가 표시됨

지원 패키지의 충돌 현상이 Visual Studio에서는 경고로만 표시되는데 런타임에 애플리케이션 작동이 중단되고 이 예외가 표시되는 경우가 있습니다. 이 경우에는 프로젝트에서 참조하는 모든 지원 패키지의 버전이 동일한지 확인해야 합니다. [Azure Mobile Apps NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Client/)에는 Android 플랫폼에 대한 `Xamarin.Android.Support.CustomTabs` 종속성이 있으므로 프로젝트에서 최신 지원 패키지를 사용하는 경우 충돌 방지를 위해 필수 버전이 포함된 이 패키지를 직접 설치해야 합니다.

<!-- URLs. -->
[Xamarin Android 앱 만들기]: app-service-mobile-xamarin-android-get-started.md
