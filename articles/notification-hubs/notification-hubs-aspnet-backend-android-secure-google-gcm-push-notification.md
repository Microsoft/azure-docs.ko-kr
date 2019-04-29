---
title: Azure Notification Hubs를 사용하여 보안 푸시 알림 보내기
description: Azure에서 Android 앱에 보안 푸시 알림을 보내는 방법에 대해 알아봅니다. 코드 샘플은 Java 및 C#으로 작성되었습니다.
documentationcenter: android
keywords: 푸시 알림,푸시알림,푸시 메시지,android 푸시 알림
author: jwargo
manager: patniko
editor: spelluru
services: notification-hubs
ms.assetid: daf3de1c-f6a9-43c4-8165-a76bfaa70893
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 27536b0a3d7e0858a5660b4c7b33cb6679b5fbf1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60874565"
---
# <a name="sending-secure-push-notifications-with-azure-notification-hubs"></a>Azure Notification Hubs를 사용하여 보안 푸시 알림 보내기

> [!div class="op_single_selector"]
> * [Windows 범용](notification-hubs-aspnet-backend-windows-dotnet-wns-secure-push-notification.md)
> * [iOS](notification-hubs-aspnet-backend-ios-push-apple-apns-secure-notification.md)
> * [Android](notification-hubs-aspnet-backend-android-secure-google-gcm-push-notification.md)

## <a name="overview"></a>개요

> [!IMPORTANT]
> 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)을 참조하세요.

Microsoft Azure의 푸시 알림 지원을 통해 간편하게 사용할 수 있는 다중 플랫폼의 확장형 푸시 메시지 인프라에 액세스할 수 있습니다. 이 인프라에서는 모바일 플랫폼용 소비자 애플리케이션 및 엔터프라이즈 애플리케이션에 사용할 푸시 알림을 매우 간단하게 구현할 수 있습니다.

규제나 보안 제약 조건 때문에 애플리케이션의 알림에 표준 푸시 알림 인프라를 통해 전송할 수 없는 어떤 항목을 포함해야 할 수 있습니다. 이 자습서에서는 클라이언트 Android 디바이스와 앱 백 엔드 간에 인증된 보안 연결을 통해 중요한 정보를 전송하여 동일한 경험을 얻는 방법에 대해 설명합니다.

개략적으로 흐름은 다음과 같습니다.

1. 앱 백 엔드:
   * 백 엔드 데이터베이스에 보안 페이로드를 저장합니다.
   * 이 알림의 ID를 Android 디바이스에 보냅니다(보안 정보는 전송되지 않음).
2. 알림 수신 시 디바이스의 앱:
   * Android 디바이스가 보안 페이로드를 요청하는 백 엔드에 접속합니다.
   * 앱이 디바이스에서 페이로드를 알림으로 표시할 수 있습니다.

앞의 흐름과 이 자습서에서는 사용자가 로그인한 후 디바이스가 인증 토큰을 로컬 스토리지에 저장한다고 가정합니다. 이렇게 하면 디바이스가 이 토큰을 사용하여 알림의 보안 페이로드를 검색할 수 있으므로 원활한 환경이 보장됩니다. 애플리케이션이 인증 토큰을 디바이스에 저장하지 않거나 이 토큰이 만료될 수 없으면 푸시 알림 수신 시 디바이스 앱은 사용자에게 앱을 시작할지 묻는 메시지가 포함된 일반 알림을 표시해야 합니다. 그리고 나서 앱은 사용자를 인증하고 알림 페이로드를 표시합니다.

이 자습서에서는 보안 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서는 [사용자에게 알림](notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md) 자습서를 기반으로 빌드되므로 해당 자습서의 단계를 아직 완료하지 않은 경우 먼저 완료해야 합니다.

> [!NOTE]
> 이 자습서에서는 [Notification Hubs 시작(Android)](notification-hubs-android-push-notification-google-gcm-get-started.md)에 설명된 대로 Notification Hubs를 만들고 구성했다고 가정합니다

[!INCLUDE [notification-hubs-aspnet-backend-securepush](../../includes/notification-hubs-aspnet-backend-securepush.md)]

## <a name="modify-the-android-project"></a>Android 프로젝트 수정

푸시 알림의 *ID*만 보내도록 앱 백 엔드를 수정했으므로 해당 알림을 처리하고 백 엔드를 콜백하여 표시할 보안 메시지를 검색하도록 Android 앱을 변경해야 합니다.
이 목표를 달성하려면 Android 앱이 푸시 알림을 받을 때 백 엔드에 인증하는 방법을 알고 있어야 합니다.

이제 앱의 공유 기본 설정에서 인증 헤더 값을 저장하기 위해 *로그인* 흐름을 수정합니다. 유사 메커니즘을 사용하여 사용자 앱에서 자격 증명 없이 사용해야 하는 인증 토큰(예: OAuth 토큰)을 저장할 수 있습니다.

1. Android 앱 프로젝트에서 `MainActivity` 클래스의 맨 위에 다음 상수를 추가합니다.

    ```java
    public static final String NOTIFY_USERS_PROPERTIES = "NotifyUsersProperties";
    public static final String AUTHORIZATION_HEADER_PROPERTY = "AuthorizationHeader";
    ```
2. `MainActivity` 클래스에서 `getAuthorizationHeader()` 메서드를 업데이트하여 다음 코드를 포함합니다.

    ```java
    private String getAuthorizationHeader() throws UnsupportedEncodingException {
        EditText username = (EditText) findViewById(R.id.usernameText);
        EditText password = (EditText) findViewById(R.id.passwordText);
        String basicAuthHeader = username.getText().toString()+":"+password.getText().toString();
        basicAuthHeader = Base64.encodeToString(basicAuthHeader.getBytes("UTF-8"), Base64.NO_WRAP);

        SharedPreferences sp = getSharedPreferences(NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        sp.edit().putString(AUTHORIZATION_HEADER_PROPERTY, basicAuthHeader).commit();

        return basicAuthHeader;
    }
    ```
3. `MainActivity` 파일 맨 위에 다음 `import` 문을 추가합니다.

    ```java
    import android.content.SharedPreferences;
    ```

이제 알림을 받을 때 호출되는 처리기를 변경합니다.

1. `MyHandler` 클래스에서 다음을 포함하도록 `OnReceive()` 메서드를 변경합니다.

    ```java
    public void onReceive(Context context, Bundle bundle) {
        ctx = context;
        String secureMessageId = bundle.getString("secureId");
        retrieveNotification(secureMessageId);
    }
    ```
2. 그런 다음 `retrieveNotification()` 메서드를 추가하고 자리 표시자 `{back-end endpoint}`를 백 엔드를 배포할 때 얻은 백 엔드 엔드포인트로 바꿉니다.

    ```java
    private void retrieveNotification(final String secureMessageId) {
        SharedPreferences sp = ctx.getSharedPreferences(MainActivity.NOTIFY_USERS_PROPERTIES, Context.MODE_PRIVATE);
        final String authorizationHeader = sp.getString(MainActivity.AUTHORIZATION_HEADER_PROPERTY, null);

        new AsyncTask<Object, Object, Object>() {
            @Override
            protected Object doInBackground(Object... params) {
                try {
                    HttpUriRequest request = new HttpGet("{back-end endpoint}/api/notifications/"+secureMessageId);
                    request.addHeader("Authorization", "Basic "+authorizationHeader);
                    HttpResponse response = new DefaultHttpClient().execute(request);
                    if (response.getStatusLine().getStatusCode() != HttpStatus.SC_OK) {
                        Log.e("MainActivity", "Error retrieving secure notification" + response.getStatusLine().getStatusCode());
                        throw new RuntimeException("Error retrieving secure notification");
                    }
                    String secureNotificationJSON = EntityUtils.toString(response.getEntity());
                    JSONObject secureNotification = new JSONObject(secureNotificationJSON);
                    sendNotification(secureNotification.getString("Payload"));
                } catch (Exception e) {
                    Log.e("MainActivity", "Failed to retrieve secure notification - " + e.getMessage());
                    return e;
                }
                return null;
            }
        }.execute(null, null, null);
    }
    ```

이 메서드는 앱 백 엔드를 호출하여 공유 기본 설정에 저장된 자격 증명을 통해 알림 콘텐츠를 검색하고 일반 알림으로 표시합니다. 이 알림은 앱 사용자에게 다른 푸시 알림과 똑같이 보입니다.

백 엔드에 의해 거부되거나 인증 헤더 속성이 누락되는 경우를 처리하는 것이 좋습니다. 이러한 경우의 특수 처리는 대부분 대상 사용자 환경에 따라 다릅니다. 한 가지 옵션은 실제 알림을 검색하기 위해 사용자가 인증을 받도록 하는 일반 프롬프트가 포함된 알림을 표시하는 것입니다.

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션을 실행하려면 다음 작업을 수행합니다.

1. **AppBackend** 가 Azure에 배포되었는지 확인합니다. Visual Studio를 사용할 경우 **AppBackend** Web API 애플리케이션을 실행합니다. ASP.NET 웹 페이지가 표시됩니다.
2. Eclipse에서는 실제 Android 디바이스나 에뮬레이터에서 앱을 실행합니다.
3. Android 앱 UI에서 사용자 이름과 암호를 입력합니다. 이는 임의 문자열일 수 있지만 같은 값이어야 합니다.
4. Android 앱 UI에서 **로그인**을 클릭합니다. 그리고 나서 **푸시 보내기**를 클릭합니다.
