---
title: MSAL Android에서 공유 디바이스 모드 사용 | Microsoft
description: Android 디바이스를 공유 모드에서 실행하도록 준비하고 일선 직원 앱을 실행하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 1/15/2020
ms.author: hahamil
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f49a5703b19a76095c8eafe358742b442725d3d0
ms.sourcegitcommit: b8702065338fc1ed81bfed082650b5b58234a702
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/11/2020
ms.locfileid: "88118249"
---
# <a name="tutorial-use-shared-device-mode-in-your-android-application"></a>자습서: Android 애플리케이션에서 공유 디바이스 모드 사용

> [!NOTE]
> 이 기능은 공개 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="developer-guide"></a>개발자 가이드

이 가이드에서는 MSAL(Microsoft 인증 라이브러리)을 사용하여 Android 애플리케이션에서 공유 디바이스 모드를 구현하는 방법에 대한 개발자 지침을 제공합니다. [MSAL Android 자습서](./tutorial-v2-android.md)를 참조하여 MSAL을 Android 앱과 통합하고, 사용자를 로그인하고, Microsoft Graph를 호출하고, 사용자를 로그아웃하는 방법을 확인합니다.

### <a name="download-the-sample"></a>샘플 다운로드

GitHub에서 [샘플 애플리케이션](https://github.com/Azure-Samples/ms-identity-android-java/)을 복제합니다. 샘플에는 [단일 또는 다중 계정 모드](./single-multi-account.md)에서 작동하는 기능이 있습니다.

### <a name="add-the-msal-sdk-to-your-local-maven-repository"></a>로컬 Maven 리포지토리에 MSAL SDK 추가

샘플 앱을 사용하지 않는 경우 다음과 같이 MSAL 라이브러리를 종속성으로 build.gradle 파일에 추가합니다.

```gradle
dependencies{
  implementation 'com.microsoft.identity.client.msal:1.0.+'
}
```

### <a name="configure-your-app-to-use-shared-device-mode"></a>공유 디바이스 모드를 사용하도록 앱 구성

구성 파일을 설정하는 방법에 대한 자세한 내용은 [구성 설명서](./msal-configuration.md)를 참조하세요.

MSAL 구성 파일에서 `"shared_device_mode_supported"`를 `true`로 설정합니다.

다중 계정 모드를 지원하지 않을 수도 있습니다. 이는 공유 디바이스를 사용하지 않고 사용자가 동시에 둘 이상의 계정으로 앱에 로그인할 수 있는 경우일 수 있습니다. 그렇다면 `"account_mode"`를 `"SINGLE"`로 설정합니다. 이렇게 하면 앱에서 항상 `ISingleAccountPublicClientApplication`을 받을 수 있으며, MSAL 통합을 크게 간소화합니다. `"account_mode"`의 기본값은 `"MULTIPLE"`이므로 `"single account"` 모드를 사용하는 경우 구성 파일에서 이 값을 변경해야 합니다.

다음은 샘플 앱의 **app**>**main**>**res**>**raw** 디렉터리에 포함된 auth_config.json 파일의 예제입니다.

```json
{
 "client_id":"Client ID after app registration at https://aka.ms/MobileAppReg",
 "authorization_user_agent":"DEFAULT",
 "redirect_uri":"Redirect URI after app registration at https://aka.ms/MobileAppReg",
 "account_mode":"SINGLE",
 "broker_redirect_uri_registered": true,
 "shared_device_mode_supported": true,
 "authorities":[
  {
   "type":"AAD",
   "audience":{
     "type": "AzureADandPersonalMicrosoftAccount",
     "tenant_id":"common"
   }
  }
 ]
}
```

### <a name="detect-shared-device-mode"></a>공유 디바이스 모드 검색

공유 디바이스 모드를 사용하면 여러 직원이 Android 디바이스를 공유하도록 구성하는 한편, 해당 디바이스에 대한 Microsoft ID 지원 관리를 제공할 수 있습니다. 직원은 자신의 디바이스에 로그인하여 고객 정보에 빠르게 액세스할 수 있습니다. 직원이 교대 근무 또는 작업을 마치면 한 번의 클릭으로 공유 디바이스의 모든 앱에서 로그아웃할 수 있으며, 다음 직원이 해방 디바이스를 즉시 사용할 수 있도록 준비됩니다.

`isSharedDevice()`를 사용하여 앱이 공유 디바이스 모드의 디바이스에서 실행되고 있는지 확인합니다. 앱에서 이 플래그를 사용하여 UX를 적절하게 수정할지 여부를 결정할 수 있습니다.

다음은 `isSharedDevice()`를 사용하는 방법을 보여 주는 코드 조각입니다.  샘플 앱의 `SingleAccountModeFragment` 클래스에서 가져온 것입니다.

```Java
deviceModeTextView.setText(mSingleAccountApp.isSharedDevice() ?"Shared" :"Non-Shared");
```

### <a name="initialize-the-publicclientapplication-object"></a>PublicClientApplication 개체 초기화

MSAL 구성 파일에서 `"account_mode":"SINGLE"`을 설정하는 경우 반환된 애플리케이션 개체를 `ISingleAccountPublicCLientApplication`으로 안전하게 캐스팅할 수 있습니다.

```java
private ISingleAccountPublicClientApplication mSingleAccountApp;

/*Configure your sample app and save state for this activity*/
PublicClientApplication.create(this.getApplicationCOntext(),
  R.raw.auth_config,
  new PublicClientApplication.ApplicationCreatedListener(){
  @Override
  public void onCreated(IPublicClientApplication application){
  mSingleAccountApp = (ISingleAccountPublicClientApplication)application;
  loadAccount();
  }
  @Override
  public void onError(MsalException exception{
  /*Fail to initialize PublicClientApplication */
  }
});
```

### <a name="detect-single-vs-multiple-account-mode"></a>단일 및 다중 계정 모드 검색

공유 디바이스에서 일선 직원에게만 사용되는 앱을 작성하는 경우 단일 계정 모드만 지원하도록 앱을 작성하는 것이 좋습니다. 여기에는 의료 기록 앱, 청구서 앱 및 대부분의 LOB(기간 업무) 앱과 같이 작업에 중점을 둔 대부분의 애플리케이션이 포함됩니다. 이렇게 하면 SDK의 많은 기능을 수용할 필요가 없으므로 개발을 간소화할 수 있습니다.

앱이 공유 디바이스 모드뿐만 아니라 여러 계정도 지원하는 경우 아래와 같이 형식 검사를 수행하고 적절한 인터페이스로 캐스팅해야 합니다.

```java
private IPublicClientApplication mApplication;

        if (mApplication instanceOf IMultipleAccountPublicClientApplication) {
          IMultipleAccountPublicClientApplication multipleAccountApplication = (IMultipleAccountPublicClientApplication) mApplication;
          ...
        } else if (mApplication instanceOf    ISingleAccountPublicClientApplication) {
           ISingleAccountPublicClientApplication singleAccountApplication = (ISingleAccountPublicClientApplication) mApplication;
            ...
        }
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>로그인한 사용자를 가져와서 사용자가 디바이스에서 변경되었는지 확인

`loadAccount` 메서드는 로그인한 사용자의 계정을 검색합니다. `onAccountChanged` 메서드는 로그인한 사용자가 변경되었는지 여부를 확인하고, 변경된 경우 정리합니다.

```java
private void loadAccount()
{
  mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback()
  {
    @Overide
    public void onAccountLoaded(@Nullable IAccount activeAccount)
    {
      if (activeAccount != null)
      {
        signedInUser = activeAccount;
        mSingleAccountApp.acquireTokenSilentAsync(SCOPES,"http://login.microsoftonline.com/common",getAuthSilentCallback());
      }
    }
    @Override
    public void on AccountChanged(@Nullable IAccount priorAccount, @Nullable Iaccount currentAccount)
    {
      if (currentAccount == null)
      {
        //Perform a cleanup task as the signed-in account changed.
        updateSingedOutUI();
      }
    }
    @Override
    public void onError(@NonNull Exception exception)
    {
    }
  }
}
```

### <a name="globally-sign-in-a-user"></a>전역적으로 사용자 로그인

다음은 Authenticator 앱에서 MSAL을 사용하는 다른 앱에 대한 디바이스에서 사용자를 로그인합니다.

```java
private void onSignInClicked()
{
  mSingleAccountApp.signIn(getActivity(), SCOPES, null, getAuthInteractiveCallback());
}
```

### <a name="globally-sign-out-a-user"></a>전역적으로 사용자 로그아웃

다음은 로그인한 계정을 제거하고, 앱뿐만 아니라 공유 디바이스 모드에 있는 디바이스에서도 캐시된 토큰을 지웁니다.

```java
private void onSignOutClicked()
{
  mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback()
  {
    @Override
    public void onSignOut()
    {
      updateSignedOutUI();
    }
    @Override
    public void onError(@NonNull MsalException exception)
    {
      /*failed to remove account with an exception*/
    }
  });
}
```

## <a name="administrator-guide"></a>관리자 가이드

다음 단계에서는 Azure Portal에서 애플리케이션을 설정하고, 디바이스를 공유 디바이스 모드로 전환하는 방법에 대해 설명합니다.

### <a name="register-your-application-in-azure-active-directory"></a>Azure Active Directory에 애플리케이션 등록

먼저, 애플리케이션을 조직 테넌트 내에 등록합니다. 그런 다음, 애플리케이션이 올바르게 실행되도록 다음 값을 auth_config.json에 입력합니다.

이 작업을 수행하는 방법에 대한 자세한 내용은 [애플리케이션 등록](./tutorial-v2-android.md#register-your-application)을 참조하세요.

> [!NOTE]
> 앱을 등록하는 경우 왼쪽에 있는 빠른 시작 가이드를 사용하고 **Android**를 선택하세요. 그러면 **패키지 이름** 및 **서명 해시**를 앱에 제공하라는 페이지가 표시됩니다. 이러한 항목은 앱 구성이 작동하는지 확인하는 데 매우 중요합니다. 그런 다음, 앱에 사용할 수 있는 구성 개체를 받고 이를 잘라내어 auth_config.json 파일에 붙여넣습니다.

![App 등록 화면](media/tutorial-v2-shared-device-mode/register-app.png) **Make this change for me(내게 맞게 변경)** 를 선택한 다음, 빠른 시작에서 요청하는 값을 Azure Portal에서 제공해야 합니다. 이 작업이 완료되면 필요한 모든 구성 파일이 생성됩니다.

![앱 구성 정보 화면](media/tutorial-v2-shared-device-mode/config-info.png)

## <a name="set-up-a-tenant"></a>테넌트 설정

테스트 목적으로 2명 이상의 직원, 1명의 클라우드 디바이스 관리자 및 1명의 전역 관리자를 테넌트에 설정합니다. Azure Portal에서 조직 역할을 수정하여 클라우드 디바이스 관리자를 설정합니다. Azure Portal에서 **Azure Active Directory** > **역할 및 관리자** > **클라우드 디바이스 관리자**를 차례로 선택하여 [조직 역할]에 액세스합니다. 디바이스를 공유 모드로 전환할 수 있는 사용자를 추가합니다.

## <a name="set-up-an-android-device-in-shared-mode"></a>Android 디바이스를 공유 모드로 설정

### <a name="download-the-authenticator-app"></a>Authenticator 앱 다운로드

Google Play 스토어에서 Microsoft Authenticator 앱을 다운로드합니다. 앱을 이미 다운로드한 경우 최신 버전인지 확인합니다.

### <a name="authenticator-app-settings--registering-the-device-in-the-cloud"></a>Authenticator 앱 설정 및 클라우드에서 디바이스 등록

Authenticator 앱을 시작하고, 주 계정 페이지로 이동합니다. **계정 추가** 페이지가 표시되면 디바이스를 공유할 준비가 되었습니다.

![Authenticator 계정 추가 화면](media/tutorial-v2-shared-device-mode/authenticator-add-account.png)

 오른쪽 메뉴 모음을 사용하여 **설정** 창으로 이동합니다. **회사 및 학교 계정** 아래에서 **디바이스 등록**을 선택합니다.

 ![Authenticator 계정 추가 화면](media/tutorial-v2-shared-device-mode/authenticator-settings.png)

 이 단추를 클릭하면 디바이스 연락처에 대한 액세스 권한을 부여하라는 메시지가 표시됩니다. 이는 디바이스에 대한 Android의 계정 통합 때문입니다. **허용**을 선택합니다.

 ![Authenticator 계정 추가 화면](media/tutorial-v2-shared-device-mode/authenticator-allow-screen.png)

클라우드 디바이스 관리자는 **또는 공유 디바이스로 등록** 아래에서 조직 이메일을 입력해야 합니다. 그런 다음, **공유 디바이스로 등록** 단추를 클릭하고 해당 자격 증명을 입력합니다.

![디바이스 등록 화면](media/tutorial-v2-shared-device-mode/register-device.png)

![로그인](media/tutorial-v2-shared-device-mode/sign-in.png)

디바이스는 이제 공유 모드에 있습니다.

![디바이스 등록 화면](media/tutorial-v2-shared-device-mode/shared-device-mode-screen.png)

 디바이스의 로그인 및 로그아웃은 글로벌이므로 디바이스에서 MSAL 및 Microsoft Authenticator와 통합된 모든 앱에 적용됩니다. 이제 공유 디바이스 모드 기능을 사용하는 디바이스에 애플리케이션을 배포할 수 있습니다.

## <a name="view-the-shared-device-in-the-azure-portal"></a>Azure Portal에서 공유 디바이스 보기

디바이스가 공유 모드로 전환되면 해당 디바이스가 조직에서 인식되고 조직 테넌트에서 추적됩니다. Azure Portal의 Azure Active Directory 블레이드에서 **조인 유형**을 살펴보면 공유 디바이스를 볼 수 있습니다.

![Azure Portal의 모든 디바이스 블레이드](media/tutorial-v2-shared-device-mode/registered-device-screen.png)

## <a name="running-the-sample-app"></a>샘플 앱 실행

샘플 애플리케이션은 조직의 Graph API를 호출하는 간단한 앱입니다. 처음 실행하는 경우 애플리케이션이 직원 계정에서 새로운 것이므로 동의하라는 메시지가 표시됩니다.

![앱 구성 정보 화면](media/tutorial-v2-shared-device-mode/run-app-permissions-requested.png)

## <a name="next-steps"></a>다음 단계

[Android 디바이스의 공유 디바이스 모드](msal-android-shared-devices.md)에서 공유 모드에 대해 자세히 알아봅니다.