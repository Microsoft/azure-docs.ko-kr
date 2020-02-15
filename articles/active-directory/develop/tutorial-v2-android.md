---
title: 사용자 로그인/로그아웃 및 Microsoft Graph(Android) 호출 - Microsoft ID 플랫폼 | Azure
description: 액세스 토큰을 가져오고 Microsoft ID 플랫폼(Android)의 액세스 토큰이 필요한 Microsoft Graph 또는 API를 호출합니다.
services: active-directory
documentationcenter: dev-center-name
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/26/2019
ms.author: hahamil
ms.reviwer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 4366f95a9c6d5c0e6b851583ac6001b3b413c08a
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/08/2020
ms.locfileid: "77085398"
---
# <a name="tutorial-sign-in-users-and-call-the-microsoft-graph-from-an-android-application"></a>자습서: Android 애플리케이션에서 사용자 로그인 및 Microsoft Graph 호출 

>[!NOTE]
>이 자습서에서는 Android용 MSAL을 사용하는 방법의 간단한 예를 보여줍니다. 간단히 설명하기 위해 이 자습서에서는 단일 계정 모드만 사용합니다. 리포지토리를 살펴보고 [미리 구성된 샘플 앱](https://github.com/Azure-Samples/ms-identity-android-java/)을 복제하여 더 복잡한 시나리오를 살펴볼 수 있습니다. 샘플 앱, 구성 및 등록에 대한 자세한 내용은 [빠른 시작](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-android)을 참조하세요. 

이 자습서에서는 Android용 Microsoft 인증 라이브러리를 사용하여 android 앱을 Microsoft ID 플랫폼과 통합하는 방법을 알아봅니다. 사용자로 로그인/로그아웃하고, Microsoft Graph API를 호출하기 위한 액세스 토큰을 가져오고, Graph API를 요청하는 방법을 알아봅니다. 

> [!div class="checklist"]
> * Android 앱과 Microsoft ID 플랫폼 통합 
> * 사용자 로그인 
> * Microsoft Graph API를 호출할 액세스 토큰 가져오기 
> * Microsoft Graph API 호출 
> * 사용자 로그아웃 

이 자습서를 완료했으면 애플리케이션에서 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 또는 학교 계정뿐만 아니라 개인 Microsoft 계정(outlook.com, live.com 등 포함)의 로그인을 수락하게 됩니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="how-this-tutorial-works"></a>이 자습서의 작동 방식

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](../../../includes/media/active-directory-develop-guidedsetup-android-intro/android-intro.svg)

이 자습서의 앱에서는 사용자로 로그인하고 사용자를 대신하여 데이터를 가져옵니다. 이 데이터는 권한 부여가 필요하고 Microsoft ID 플랫폼으로 보호되는 API(Microsoft Graph API)를 통해 액세스됩니다.

더 구체적으로 살펴보면 다음과 같습니다.

* 앱이 브라우저 또는 Microsoft Authenticator와 Intune 회사 포털을 통해 사용자를 로그인합니다.
* 최종 사용자는 애플리케이션에서 요청한 권한을 수락합니다.
* 앱에서 Microsoft Graph API에 대한 액세스 토큰을 발급합니다.
* 액세스 토큰은 웹 API에 대한 HTTP 요청에 포함됩니다.
* Microsoft Graph 응답을 처리합니다.

이 샘플에서는 MSAL(Android용 Microsoft 인증 라이브러리)을 사용하여 [com.microsoft.identity.client](https://javadoc.io/doc/com.microsoft.identity.client/msal) 인증을 구현합니다.

 MSAL은 자동으로 토큰을 갱신하고, 디바이스의 다른 앱 간에 SSO(Single Sign-On)를 제공하고, 계정을 관리합니다.

### <a name="prerequisites"></a>사전 요구 사항

* 이 자습서를 수행하려면 Android Studio 버전 3.5 이상이 필요합니다.

## <a name="create-a-project"></a>프로젝트 만들기
아직 Android 애플리케이션이 없는 경우 다음 단계에 따라 새 프로젝트를 설정합니다. 

1. Android Studio를 열고 **새 Android Studio 프로젝트 시작**을 선택합니다.
2. **기본 작업**을 선택하고 **다음**을 선택합니다.
3. 애플리케이션의 이름을 지정합니다.
4. 패키지 이름을 저장합니다. 나중에 Azure Portal에 입력할 것입니다.
5. 언어를 **Kotlin**에서 **Java**로 변경합니다.
6. **최소 API 수준**을 **API 19** 이상으로 설정하고 **마침**을 클릭합니다.
7. 프로젝트 보기의 드롭다운 목록에서 **프로젝트**를 선택하여 소스 및 소스가 아닌 프로젝트 파일을 표시하고, **app/build.gradle**을 열고, `targetSdkVersion`을 `28`로 설정합니다.

## <a name="integrate-with-microsoft-authentication-library"></a>Microsoft 인증 라이브러리와 통합 

### <a name="register-your-application"></a>애플리케이션 등록

1. [Azure 포털](https://aka.ms/MobileAppReg)로 이동합니다.
2. [앱 등록 블레이드](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)를 열고 **+새 등록**을 클릭합니다.
3. 앱의 **이름**을 입력한 다음, 리디렉션 URI를 설정하지 **않고** **등록**을 클릭합니다.
4. 나타나는 창의 **관리** 섹션에서 **인증** >  **+ 플랫폼 추가** > **Android**를 선택합니다. (이 섹션을 보려면 블레이드 상단 근처에 있는 "새 환경으로 전환"을 선택해야 할 수도 있습니다.)
5. 프로젝트의 패키지 이름을 입력합니다. 코드를 다운로드한 경우 이 값은 `com.azuresamples.msalandroidapp`입니다.
6. **Android 앱 구성** 페이지의 **서명 해시** 섹션에서 **개발 서명 해시 생성**을 클릭합니다. 플랫폼에 사용할 KeyTool 명령을 복사합니다.

   > [!Note]
   > KeyTool.exe는 JDK(Java Development Kit)의 일부로 설치됩니다. 또한 OpenSSL 도구를 설치하여 KeyTool 명령도 실행해야 합니다. 자세한 내용은 [키 생성에 대한 Android 설명서](https://developer.android.com/studio/publish/app-signing#generate-key)를 참조하세요. 

7. KeyTool에서 생성된 **서명 해시**를 입력합니다.
8. `Configure`를 클릭하고 **Android 구성** 페이지에 나타나는 **MSAL 구성**을 저장합니다. 그러면 나중에 앱을 구성할 때 이 구성을 입력할 수 있습니다.  **Done**을 클릭합니다.

### <a name="configure-your-application"></a>애플리케이션 구성 

1. Android Studio의 프로젝트 창에서 **app\src\main\res**로 이동합니다.
2. **res**를 마우스 오른쪽 단추로 클릭하고 **새로 만들기** > **디렉터리**를 선택합니다. 새 디렉터리 이름으로 `raw`를 입력하고 **확인**을 클릭합니다.
3. **app** > **src** > **main** > **res** > **raw**에서 `auth_configbn_single_account.json`이라는 새 JSON 파일을 만들고, 앞에서 저장한 MSAL 구성을 붙여넣습니다. 

    리디렉션 URI 아래에 다음을 복사합니다. 
    ```json
      "account_mode" : "SINGLE",
    ```
    구성 파일이 다음 예제와 비슷할 것입니다. 
    ```json   
    {
      "client_id" : "0984a7b6-bc13-4141-8b0d-8f767e136bb7",
      "authorization_user_agent" : "DEFAULT",
      "redirect_uri" : "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D",
      "account_mode" : "SINGLE",
      "authorities" : [
        {
          "type": "AAD",
          "audience": {
            "type": "AzureADandPersonalMicrosoftAccount",
            "tenant_id": "common"
          }
        }
      ]
    }
   ```
    
   >[!NOTE]
   >이 자습서에서는 단일 계정 모드에서 앱을 구성하는 방법만 보여줍니다. [단일 계정 모드와 다중 계정 모드](https://docs.microsoft.com/azure/active-directory/develop/single-multi-account) 및 [앱 구성](https://docs.microsoft.com/azure/active-directory/develop/msal-configuration)에 대한 자세한 내용은 설명서를 참조하세요.
   
4. **app** > **src** > **main** > **AndroidManifest.xml**에서 아래의 `BrowserTabActivity` 작업을 애플리케이션 본문에 추가합니다. 이 항목은 다음과 같이 Microsoft에서 인증을 완료한 후 애플리케이션을 콜백할 수 있게 해줍니다.

    ```xml
    <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
    <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
        <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
        </intent-filter>
    </activity>
    ```

    `android:host=` 값을 Azure Portal에서 등록한 패키지 이름으로 바꿉니다.
    `android:path=` 값을 Azure Portal에서 등록한 키 해시로 바꿉니다. 서명 해시는 URL로 인코딩되면 **안 됩니다**. 서명 해시의 시작 부분에 선행 `/`가 있는지 확인합니다. 
    >[!NOTE]
    >`android:host` 값을 대체할 "패키지 이름"은 "com.azuresamples.msalandroidapp" 형식이고, `android:path` 값을 대체할 "서명 해시"는 "/1wIqXSqBj7w+h11ZifsnqwgyKrY=" 형식입니다. 앱 등록의 인증 블레이드에서도 이러한 값을 찾을 수 있습니다. 리디렉션 URI는 "msauth://com.azuresamples.msalandroidapp/1wIqXSqBj7w%2Bh11ZifsnqwgyKrY%3D" 형식입니다. 서명 해시가 이 값의 끝 부분에 URL로 인코딩되었지만, 서명 해시가 `android:path` 값에서 URL로 인코딩되면 **안 됩니다**. 

## <a name="use-msal"></a>MSAL 사용 

### <a name="add-msal-to-your-project"></a>프로젝트에 MSAL 추가

1. Android Studio 프로젝트 창에서 **app** > **src** > **build.gradle**로 이동하여 다음을 추가합니다. 

    ```gradle
    repositories{
        jcenter()
    }  
    dependencies{
        implementation 'com.microsoft.identity.client:msal:1.2.+'
        implementation 'com.microsoft.graph:microsoft-graph:1.5.+'
    }
    packagingOptions{
        exclude("META-INF/jersey-module-version") 
    }
    ```
    [Microsoft Graph SDK에 대한 자세한 정보](https://github.com/microsoftgraph/msgraph-sdk-java/)

### <a name="required-imports"></a>꼭 가져와야 하는 항목 

**app** > **src** > **main**> **java** > **com.example(yourapp)**  > **MainActivity.java** 맨 위에 다음을 추가합니다. 

```java
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;
import android.widget.Toast;
import androidx.annotation.NonNull;
import androidx.annotation.Nullable;
import androidx.appcompat.app.AppCompatActivity;
import com.google.gson.JsonObject;
import com.microsoft.graph.authentication.IAuthenticationProvider; //Imports the Graph sdk Auth interface
import com.microsoft.graph.concurrency.ICallback;
import com.microsoft.graph.core.ClientException;
import com.microsoft.graph.http.IHttpRequest;
import com.microsoft.graph.models.extensions.*;
import com.microsoft.graph.requests.extensions.GraphServiceClient;
import com.microsoft.identity.client.AuthenticationCallback; // Imports MSAL auth methods
import com.microsoft.identity.client.*;
import com.microsoft.identity.client.exception.*;
```

## <a name="instantiate-publicclientapplication"></a>PublicClientApplication 인스턴스화
#### <a name="initialize-variables"></a>변수 초기화 
```java
private final static String[] SCOPES = {"File.Read"};
/* Azure AD v2 Configs */
final static String AUTHORITY = "https://login.microsoftonline.com/common";
private ISingleAccountPublicClientApplication mSingleAccountApp;

private static final String TAG = MainActivity.class.getSimpleName();

/* UI & Debugging Variables */
Button signInButton;
Button signOutButton;
Button callGraphApiInteractiveButton;
Button callGraphApiSilentButton;
TextView logTextView;
TextView currentUserTextView;
```

### <a name="oncreate"></a>onCreate
`MainActivity` 클래스 내에서 `SingleAccountPublicClientApplication`을 사용하여 MSAL을 인스턴스화하는 다음 onCreate() 메서드를 참조합니다.

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    initializeUI();

    PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(),
            R.raw.auth_config_single_account, new IPublicClientApplication.ISingleAccountApplicationCreatedListener() {
                @Override
                public void onCreated(ISingleAccountPublicClientApplication application) {
                    mSingleAccountApp = application;
                    loadAccount();
                }
                @Override
                public void onError(MsalException exception) {
                    displayError(exception);
                }
            });
}
```

### <a name="loadaccount"></a>loadAccount 

```java
//When app comes to the foreground, load existing account to determine if user is signed in 
private void loadAccount() {
    if (mSingleAccountApp == null) {
        return;
    }

    mSingleAccountApp.getCurrentAccountAsync(new ISingleAccountPublicClientApplication.CurrentAccountCallback() {
        @Override
        public void onAccountLoaded(@Nullable IAccount activeAccount) {
            // You can use the account data to update your UI or your app database.
            updateUI(activeAccount);
        }
        
        @Override
        public void onAccountChanged(@Nullable IAccount priorAccount, @Nullable IAccount currentAccount) {
            if (currentAccount == null) {
                // Perform a cleanup task as the signed-in account changed.
                performOperationOnSignOut();
            }
        }

        @Override
        public void onError(@NonNull MsalException exception) {
            displayError(exception);
        }
    });
}
```

### <a name="initializeui"></a>initializeUI
단추를 수신 대기하고 메서드를 호출하거나 적절하게 오류를 로깅합니다. 
```java
private void initializeUI(){
        signInButton = findViewById(R.id.signIn);
        callGraphApiSilentButton = findViewById(R.id.callGraphSilent);
        callGraphApiInteractiveButton = findViewById(R.id.callGraphInteractive);
        signOutButton = findViewById(R.id.clearCache);
        logTextView = findViewById(R.id.txt_log);
        currentUserTextView = findViewById(R.id.current_user);
        
        //Sign in user 
        signInButton.setOnClickListener(new View.OnClickListener(){
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.signIn(MainActivity.this, null, SCOPES, getAuthInteractiveCallback());
            }
        });
        
        //Sign out user
        signOutButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.signOut(new ISingleAccountPublicClientApplication.SignOutCallback() {
                    @Override
                    public void onSignOut() {
                        updateUI(null);
                        performOperationOnSignOut();
                    }
                    @Override
                    public void onError(@NonNull MsalException exception){
                        displayError(exception);
                    }
                });
            }
        });
        
        //Interactive 
        callGraphApiInteractiveButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null) {
                    return;
                }
                mSingleAccountApp.acquireToken(MainActivity.this, SCOPES, getAuthInteractiveCallback());
            }
        });

        //Silent
        callGraphApiSilentButton.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                if (mSingleAccountApp == null){
                    return;
                }
                mSingleAccountApp.acquireTokenSilentAsync(SCOPES, AUTHORITY, getAuthSilentCallback());
            }
        });
    }
```

> [!Important]
> MSAL을 사용하여 로그아웃하면 사용자에 대해 알려진 모든 정보가 이 애플리케이션에서 제거되지만, 사용자의 디바이스에서는 여전히 활성 세션이 유지됩니다. 사용자가 다시 로그인하려고 시도할 때 로그인 UI가 표시될 수 있지만, 디바이스 세션이 여전히 활성 상태이므로 자격 증명을 다시 입력할 필요가 없습니다. 

### <a name="getauthinteractivecallback"></a>getAuthInteractiveCallback
대화형 요청에 사용되는 콜백입니다.

```java 
private AuthenticationCallback getAuthInteractiveCallback() {
    return new AuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            /* Successfully got a token, use it to call a protected resource - MSGraph */
            Log.d(TAG, "Successfully authenticated");
            /* Update UI */
            updateUI(authenticationResult.getAccount());
            /* call graph */
            callGraphAPI(authenticationResult);
        }

        @Override
        public void onError(MsalException exception) {
            /* Failed to acquireToken */
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
        @Override
        public void onCancel() {
            /* User canceled the authentication */
            Log.d(TAG, "User cancelled login.");
        }
    };
}
```

### <a name="getauthsilentcallback"></a>getAuthSilentCallback
자동 요청에 사용되는 콜백입니다. 
```java 
private SilentAuthenticationCallback getAuthSilentCallback() {
    return new SilentAuthenticationCallback() {
        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) {
            Log.d(TAG, "Successfully authenticated");
            callGraphAPI(authenticationResult);
        }
        @Override
        public void onError(MsalException exception) {
            Log.d(TAG, "Authentication failed: " + exception.toString());
            displayError(exception);
        }
    };
}
```

## <a name="call-microsoft-graph-api"></a>Microsoft Graph API 호출 

다음 코드는 Graph SDK를 사용하여 GraphAPI를 호출하는 방법을 보여줍니다. 

### <a name="callgraphapi"></a>callGraphAPI 

```java
private void callGraphAPI(IAuthenticationResult authenticationResult) {

    final String accessToken = authenticationResult.getAccessToken();

    IGraphServiceClient graphClient =
            GraphServiceClient
                    .builder()
                    .authenticationProvider(new IAuthenticationProvider() {
                        @Override
                        public void authenticateRequest(IHttpRequest request) {
                            Log.d(TAG, "Authenticating request," + request.getRequestUrl());
                            request.addHeader("Authorization", "Bearer " + accessToken);
                        }
                    })
                    .buildClient();
    graphClient
            .me()
            .drive()
            .buildRequest()
            .get(new ICallback<Drive>() {
                @Override
                public void success(final Drive drive) {
                    Log.d(TAG, "Found Drive " + drive.id);
                    displayGraphResult(drive.getRawObject());
                }

                @Override
                public void failure(ClientException ex) {
                    displayError(ex);
                }
            });
}
```

## <a name="add-ui"></a>UI 추가
### <a name="activity"></a>작업 
이 자습서의 UI를 모델링하려는 경우 다음 메서드는 텍스트를 업데이트하고 단추를 수신 대기하는 방법에 대한 지침을 제공합니다.

#### <a name="updateui"></a>updateUI
로그인 상태에 따라 단추를 사용하도록/사용 하지 않도록 설정하고 텍스트를 설정합니다.  
```java 
private void updateUI(@Nullable final IAccount account) {
    if (account != null) {
        signInButton.setEnabled(false);
        signOutButton.setEnabled(true);
        callGraphApiInteractiveButton.setEnabled(true);
        callGraphApiSilentButton.setEnabled(true);
        currentUserTextView.setText(account.getUsername());
    } else {
        signInButton.setEnabled(true);
        signOutButton.setEnabled(false);
        callGraphApiInteractiveButton.setEnabled(false);
        callGraphApiSilentButton.setEnabled(false);
        currentUserTextView.setText("");
        logTextView.setText("");
    }
}
```
#### <a name="displayerror"></a>displayError
```java 
private void displayError(@NonNull final Exception exception) {
       logTextView.setText(exception.toString());
   }
```

#### <a name="displaygraphresult"></a>displayGraphResult

```java
private void displayGraphResult(@NonNull final JsonObject graphResponse) {
      logTextView.setText(graphResponse.toString());
  }
```
#### <a name="performoperationonsignout"></a>performOperationOnSignOut
로그아웃을 반영하도록 UI의 텍스트를 업데이트하는 메서드입니다. 

```java
private void performOperationOnSignOut() {
    final String signOutText = "Signed Out.";
    currentUserTextView.setText("");
    Toast.makeText(getApplicationContext(), signOutText, Toast.LENGTH_SHORT)
            .show();
}
```
### <a name="layout"></a>레이아웃 

단추 및 텍스트 상자를 표시하는 샘플 `activity_main.xml` 파일입니다. 

```xml 
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/activity_main"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:orientation="vertical"
    tools:context=".MainActivity">

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        android:paddingTop="5dp"
        android:paddingBottom="5dp"
        android:weightSum="10">

        <Button
            android:id="@+id/signIn"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign In"/>

        <Button
            android:id="@+id/clearCache"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:gravity="center"
            android:text="Sign Out"
            android:enabled="false"/>

    </LinearLayout>
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:orientation="horizontal">

        <Button
            android:id="@+id/callGraphInteractive"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Interactively"
            android:enabled="false"/>

        <Button
            android:id="@+id/callGraphSilent"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="5"
            android:text="Get Graph Data Silently"
            android:enabled="false"/>
    </LinearLayout>

    <TextView
        android:text="Getting Graph Data..."
        android:textColor="#3f3f3f"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_marginLeft="5dp"
        android:id="@+id/graphData"
        android:visibility="invisible"/>

    <TextView
        android:id="@+id/current_user"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Account info goes here..." />

    <TextView
        android:id="@+id/txt_log"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_marginTop="20dp"
        android:layout_weight="0.8"
        android:text="Output goes here..." />
</LinearLayout>
```

## <a name="test-your-app"></a>앱 테스트

### <a name="run-locally"></a>로컬 실행

앱을 빌드하여 테스트용 디바이스 또는 에뮬레이터에 배포합니다. 로그인하여 Azure AD 계정 또는 Microsoft 개인 계정의 토큰을 가져올 수 있어야 합니다.

로그인하면 이 앱은 Microsoft Graph `/me` 엔드포인트에서 반환된 데이터를 표시합니다.

### <a name="consent"></a>동의

사용자가 앱에 처음으로 로그인하면 Microsoft ID는 사용자에게 요청된 권한에 동의할 것을 요구합니다. 사용자 동의가 해제된 일부 Azure AD 테넌트에서는 관리자가 모든 사용자를 대신하여 동의해야 합니다. 이 시나리오를 지원하려면 사용자 고유의 테넌트를 만들거나 관리자 동의를 받아야 합니다. 

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [애플리케이션 등록 단계](#register-your-application)에서 만든 애플리케이션 개체를 삭제합니다.

## <a name="get-help"></a>도움말 보기

이 자습서 또는 Microsoft ID 플랫폼과 관련하여 문제가 있는 경우 [도움말 및 지원](https://docs.microsoft.com/azure/active-directory/develop/developer-support-help-options)을 방문하세요.

Microsoft ID 플랫폼을 개선할 수 있도록 도와주세요. 간단한 두 가지 설문 조사를 완료하여 의견을 알려주세요.

> [!div class="nextstepaction"]
> [Microsoft ID 플랫폼 설문 조사](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)
