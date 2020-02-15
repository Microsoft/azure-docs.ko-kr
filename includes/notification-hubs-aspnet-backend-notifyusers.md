---
title: 포함 파일
description: 백 엔드 ASP.NET WebAPI 프로젝트를 만드는 코드가 포함된 포함 파일입니다.
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 09/11/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 6911f769b95967aac933dd9762263e7506aef4b5
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192503"
---
## <a name="create-the-webapi-project"></a>WebAPI 프로젝트 만들기

다음 섹션에서는 새 ASP.NET WebAPI 백 엔드 만들기를 설명합니다. 이 프로세스에는 세 가지 주요 목적이 있습니다.

- **클라이언트 인증**: 클라이언트 요청을 인증하고 사용자를 요청과 연결하는 메시지 처리기를 추가합니다.
- **WebAPI 백 엔드를 사용하여 알림 등록**: 클라이언트 디바이스에서 알림을 받을 수 있도록 새 등록을 처리하는 컨트롤러를 추가합니다. 인증된 사용자 이름은 [태그](../articles/notification-hubs/notification-hubs-tags-segment-push-message.md)로 등록에 자동으로 추가됩니다.
- **클라이언트에 알림 보내기**: 사용자가 태그와 연결된 디바이스 및 클라이언트로 보안 푸시를 트리거할 수 있는 방법을 제공하는 컨트롤러를 추가합니다.

다음 작업을 수행하여 새 ASP.NET WebAPI 백 엔드를 만듭니다.

> [!IMPORTANT]
> Visual Studio 2015 이하를 사용하는 경우 이 자습서를 시작하기 전에 Visual Studio에 대한 최신 버전의 NuGet 패키지 관리자를 설치했는지 확인합니다.
>
>확인하려면 Visual Studio를 시작합니다. **도구** 메뉴에서 **확장 및 업데이트**를 선택합니다. 사용하는 Visual Studio 버전에서 **NuGet 패키지 관리자**를 검색하고, 현재 버전이 최신 버전인지 확인합니다. 사용하는 버전이 최신 버전이 아닌 경우 해당 버전을 제거한 다음 NuGet 패키지 관리자를 다시 설치합니다.

![][B4]

> [!NOTE]
> 웹 사이트 배포를 위해 Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/)를 설치했는지 확인합니다.

1. Visual Studio 또는 Visual Studio Express를 시작합니다.

2. **서버 탐색기**를 선택하고 Azure 계정에 로그인합니다. 계정에 웹 사이트 리소스를 만들려면 로그인해야 합니다.

3. Visual Studio에서 Visual Studio 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가**를 가리킨 다음, **새 프로젝트**를 클릭합니다.
4. **Visual C#** 을 확장하고 **웹**을 선택한 다음, **ASP.NET 웹 애플리케이션**을 클릭합니다.

5. **이름** 상자에 **AppBackend**를 입력한 다음 **확인**을 선택합니다.

    ![새 프로젝트 창][B1]

6. **새 ASP.NET 프로젝트** 창에서 **Web API** 확인란을 선택한 다음 **확인**을 선택합니다.

    ![새 ASP.NET 프로젝트 창][B2]

7. **Microsoft Azure 웹앱 구성** 창에서 구독을 선택한 다음, **App Service 계획** 목록에서 다음 작업 중 하나를 수행합니다.

    * 이미 작성한 앱 서비스 계획을 선택합니다.
    * **새 앱 서비스 계획 만들기**를 선택한 다음 새로 만듭니다.

   이 자습서를 위해 데이터베이스는 필요하지 않습니다. 앱 서비스 계획을 선택한 후 **확인**을 선택하여 프로젝트를 만듭니다.

    ![Microsoft Azure 웹앱 구성 창][B5]

    App Service 계획 구성에 사용되는 이 페이지가 보이지 않을 경우 자습서를 진행하세요. 나중에 앱을 게시할 때 구성할 수 있습니다. 

## <a name="authenticate-clients-to-the-webapi-backend"></a>WebAPI 백 엔드에 클라이언트 인증

이 섹션에서는 새 백 엔드에 대해 **AuthenticationTestHandler**라는 새 메시지 처리기 클래스를 만듭니다. 이 클래스는 [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx)에서 파생되며 백 엔드로 들어오는 모든 요청을 처리할 수 있도록 메시지 처리기로 추가됩니다.

1. 솔루션 탐색기에서 **AppBackend** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가**, **클래스**를 차례로 선택합니다.
2. 새 클래스의 이름을 **AuthenticationTestHandler.cs**로 지정한 다음 **추가**를 선택하여 클래스를 생성합니다. 이 클래스는 간단히 하기 위해 *기본 인증*을 사용하여 사용자를 인증합니다. 앱은 모든 인증 체계를 사용할 수 있습니다.
3. AuthenticationTestHandler.cs에 다음 `using` 문을 추가합니다.

    ```csharp
    using System.Net.Http;
    using System.Threading;
    using System.Security.Principal;
    using System.Net;
    using System.Text;
    using System.Threading.Tasks;
    ```

4. AuthenticationTestHandler.cs에서 `AuthenticationTestHandler` 클래스 정의를 다음 코드로 바꿉니다.

    처리기는 다음 세 가지 조건이 충족될 때 요청을 인증합니다.

   * 요청에 *Authorization* 헤더가 포함되어 있습니다.
   * 요청이 *기본* 인증을 사용합니다.
   * 사용자 이름 문자열과 암호 문자열은 동일한 문자열입니다.

   그렇지 않으면 요청이 거부됩니다. 이 인증은 실제 인증 및 권한 부여 방법이 아닙니다. 이 자습서를 위한 간단한 예제일 뿐입니다.

   요청 메시지가 `AuthenticationTestHandler`에 의해 인증되고 권한이 부여되면 [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx)의 현재 요청에 기본 인증 사용자가 연결됩니다. HttpContext의 사용자 정보는 나중에 다른 컨트롤러(RegisterController)에서 알림 등록 요청에 [태그](https://msdn.microsoft.com/library/azure/dn530749.aspx)를 추가하는 데 사용됩니다.

    ```csharp
    public class AuthenticationTestHandler : DelegatingHandler
    {
        protected override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            var authorizationHeader = request.Headers.GetValues("Authorization").First();

            if (authorizationHeader != null && authorizationHeader
                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
            {
                string authorizationUserAndPwdBase64 =
                    authorizationHeader.Substring("Basic ".Length);
                string authorizationUserAndPwd = Encoding.Default
                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
                string user = authorizationUserAndPwd.Split(':')[0];
                string password = authorizationUserAndPwd.Split(':')[1];

                if (VerifyUserAndPwd(user, password))
                {
                    // Attach the new principal object to the current HttpContext object
                    HttpContext.Current.User =
                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
                    System.Threading.Thread.CurrentPrincipal =
                        System.Web.HttpContext.Current.User;
                }
                else return Unauthorized();
            }
            else return Unauthorized();

            return base.SendAsync(request, cancellationToken);
        }

        private bool VerifyUserAndPwd(string user, string password)
        {
            // This is not a real authentication scheme.
            return user == password;
        }

        private Task<HttpResponseMessage> Unauthorized()
        {
            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
            var tsc = new TaskCompletionSource<HttpResponseMessage>();
            tsc.SetResult(response);
            return tsc.Task;
        }
    }
    ```

    > [!NOTE]
    > 보안 정보: `AuthenticationTestHandler` 클래스는 진정한 의미의 인증을 제공하지 않습니다. 이 클래스는 기본 인증과 비슷한 동작을 하고 보안이 안전하지 않습니다. 프로덕션 애플리케이션 및 서비스에 보안 인증 메커니즘을 구현해야 합니다.
5. 메시지 처리기를 등록하려면 **App_Start/WebApiConfig.cs** 클래스의 `Register` 메서드 끝에 다음 코드를 추가합니다.

    ```csharp
    config.MessageHandlers.Add(new AuthenticationTestHandler());
    ```
6. 변경 내용을 저장합니다.

## <a name="register-for-notifications-by-using-the-webapi-backend"></a>WebAPI 백 엔드를 사용하여 알림 등록

이 섹션에서는 알림 허브에 클라이언트 라이브러리를 사용하여 알림을 위한 사용자 및 디바이스 등록 요청을 처리하는 새 컨트롤러를 WebAPI 백 엔드에 추가합니다. 이 컨트롤러는 `AuthenticationTestHandler`에 의해 인증되고 HttpContext에 연결된 사용자에 대한 사용자 태그를 추가합니다. 태그의 문자열 형식은 `"username:<actual username>"`입니다.

1. 솔루션 탐색기에서 **AppBackend** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **NuGet 패키지 관리**를 선택합니다.

2. 왼쪽 창에서 **온라인**을 선택한 다음 **검색** 상자에 **Microsoft.Azure.NotificationHubs**를 입력합니다.

3. 결과 목록에서 **Microsoft Azure Notification Hubs**를 선택한 다음 **설치**를 선택합니다. 설치를 완료한 다음, NuGet 패키지 관리자 창을 닫습니다.

    이 작업은 [Microsoft.Azure.Notification Hubs NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)를 사용하는 Azure Notification Hubs SDK에 대한 참조를 추가합니다.

4. 알림을 보내는 데 사용되는 알림 허브와의 연결을 나타내는 새 클래스 파일을 만듭니다. 솔루션 탐색기에서 **Models** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **클래스**를 차례로 선택합니다. 새 클래스 이름을 **Notifications.cs**로 지정한 후 **추가**를 선택하여 클래스를 생성합니다.

    ![새 항목 추가 창][B6]

5. Notifications.cs에서 파일의 맨 위에 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    ```

6. `Notifications` 클래스 정의를 다음 코드로 바꾸고 두 개의 자리 표시자를 알림 허브에 대한 연결 문자열(모든 권한 사용) 및 허브 이름([Azure Portal](https://portal.azure.com)에서 제공)으로 바꿉니다.

    ```csharp
    public class Notifications
    {
        public static Notifications Instance = new Notifications();

        public NotificationHubClient Hub { get; set; }

        private Notifications() {
            Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>",
                                                                            "<hub name>");
        }
    }
    ```
    > [!IMPORTANT]
    > 허브의 **이름**과 **DefaultFullSharedAccessSignature**를 입력하고 계속 진행합니다. 
    
7. 다음으로 **RegisterController**라는 새 컨트롤러를 만듭니다. 솔루션 탐색기에서 **Controllers** 폴더를 마우스 오른쪽 단추로 클릭한 후 **추가**, **컨트롤러**를 차례로 선택합니다.

8. **웹 API 2 컨트롤러 - 비어 있음**을 선택한 다음 **추가**를 선택합니다.

    ![스캐폴드 추가 창][B7]

9. **컨트롤러 이름** 상자에서 **RegisterController**를 입력하여 새 클래스의 이름을 지정한 다음 **추가**를 선택합니다.

    ![컨트롤러 추가 창][B8]

10. RegiterController.cs에 다음 `using` 문을 추가합니다.

    ```csharp
    using Microsoft.Azure.NotificationHubs;
    using Microsoft.Azure.NotificationHubs.Messaging;
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
11. 다음 코드를 `RegisterController` 클래스 정의 내에 추가합니다. 이 코드에서 HttpContext에 연결된 사용자에 대한 사용자 태그를 추가합니다. 사용자는 추가한 메시지 필터 `AuthenticationTestHandler`에 의해 인증되고 HttpContext에 연결되었습니다. 또한 선택적인 검사를 추가하여 사용자에게 요청된 태그에 등록할 수 있는 권한이 있는지 확인합니다.

    ```csharp
    private NotificationHubClient hub;

    public RegisterController()
    {
        hub = Notifications.Instance.Hub;
    }

    public class DeviceRegistration
    {
        public string Platform { get; set; }
        public string Handle { get; set; }
        public string[] Tags { get; set; }
    }

    // POST api/register
    // This creates a registration id
    public async Task<string> Post(string handle = null)
    {
        string newRegistrationId = null;

        // make sure there are no existing registrations for this push handle (used for iOS and Android)
        if (handle != null)
        {
            var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

            foreach (RegistrationDescription registration in registrations)
            {
                if (newRegistrationId == null)
                {
                    newRegistrationId = registration.RegistrationId;
                }
                else
                {
                    await hub.DeleteRegistrationAsync(registration);
                }
            }
        }

        if (newRegistrationId == null) 
            newRegistrationId = await hub.CreateRegistrationIdAsync();

        return newRegistrationId;
    }

    // PUT api/register/5
    // This creates or updates a registration (with provided channelURI) at the specified id
    public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
    {
        RegistrationDescription registration = null;
        switch (deviceUpdate.Platform)
        {
            case "mpns":
                registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "wns":
                registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                break;
            case "apns":
                registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                break;
            case "fcm":
                registration = new FcmRegistrationDescription(deviceUpdate.Handle);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

        registration.RegistrationId = id;
        var username = HttpContext.Current.User.Identity.Name;

        // add check if user is allowed to add these tags
        registration.Tags = new HashSet<string>(deviceUpdate.Tags);
        registration.Tags.Add("username:" + username);

        try
        {
            await hub.CreateOrUpdateRegistrationAsync(registration);
        }
        catch (MessagingException e)
        {
            ReturnGoneIfHubResponseIsGone(e);
        }

        return Request.CreateResponse(HttpStatusCode.OK);
    }

    // DELETE api/register/5
    public async Task<HttpResponseMessage> Delete(string id)
    {
        await hub.DeleteRegistrationAsync(id);
        return Request.CreateResponse(HttpStatusCode.OK);
    }

    private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
    {
        var webex = e.InnerException as WebException;
        if (webex.Status == WebExceptionStatus.ProtocolError)
        {
            var response = (HttpWebResponse)webex.Response;
            if (response.StatusCode == HttpStatusCode.Gone)
                throw new HttpRequestException(HttpStatusCode.Gone.ToString());
        }
    }
    ```
12. 변경 내용을 저장합니다.

## <a name="send-notifications-from-the-webapi-backend"></a>WebAPI 백 엔드에서 알림 보내기

이 섹션에서는 클라이언트 디바이스에서 알림을 보내기 위한 방법을 노출하는 새 컨트롤러를 추가합니다. 알림은 ASP.NET WebAPI 백 엔드에서 Azure Notification Hubs .NET 라이브러리를 사용하는 사용자 이름 태그를 기반으로 합니다.

1. 이전 섹션에서 **RegisterController**를 만들었던 동일한 방식으로 **NotificationsController**라는 다른 새 컨트롤러를 만듭니다.

2. NotificationsController.cs에 다음 `using` 문을 추가합니다.

    ```csharp
    using AppBackend.Models;
    using System.Threading.Tasks;
    using System.Web;
    ```
3. **NotificationsController** 클래스에 다음 메서드를 추가합니다.

    이 코드는 PNS(Platform Notification Service) `pns` 매개 변수를 기반으로 알림 유형을 보냅니다. `to_tag` 값은 메시지에서 *사용자 이름* 태그를 지정하는 데 사용됩니다. 이 태그는 활성 알림 허브 등록의 사용자 이름 태그와 일치해야 합니다. 알림 메시지는 POST 요청의 본문에서 가져오고 대상 PNS에 맞게 형식이 지정됩니다.

    알림을 수신하기 위해 지원되는 디바이스가 사용하는 PNS에 따라 다양한 형식으로 알림을 지원합니다. 예를 들어 Windows 디바이스에서 다른 PNS에서 직접 지원되지 않는 [WNS로 알림](https://msdn.microsoft.com/library/windows/apps/br230849.aspx)을 사용할 수 있습니다. 이러한 상황에서 백 엔드는 알림을 지원하려는 디바이스의 PNS에 지원되는 알림으로 포맷해야 합니다. 그런 다음 [NotificationHubClient 클래스](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx)에서 적절한 보내기 API를 사용합니다.

    ```csharp
    public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
    {
        var user = HttpContext.Current.User.Identity.Name;
        string[] userTag = new string[2];
        userTag[0] = "username:" + to_tag;
        userTag[1] = "from:" + user;

        Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
        HttpStatusCode ret = HttpStatusCode.InternalServerError;

        switch (pns.ToLower())
        {
            case "wns":
                // Windows 8.1 / Windows Phone 8.1
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                            "From " + user + ": " + message + "</text></binding></visual></toast>";
                outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                break;
            case "apns":
                // iOS
                var alert = "{\"aps\":{\"alert\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                break;
            case "fcm":
                // Android
                var notif = "{ \"data\" : {\"message\":\"" + "From " + user + ": " + message + "\"}}";
                outcome = await Notifications.Instance.Hub.SendFcmNativeNotificationAsync(notif, userTag);
                break;
        }

        if (outcome != null)
        {
            if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
            {
                ret = HttpStatusCode.OK;
            }
        }

        return Request.CreateResponse(ret);
    }
    ```
4. 애플리케이션을 실행하고 지금까지 작업의 정확성을 확인하려면 **F5** 키를 선택합니다. 앱은 웹 브라우저를 열고 ASP.NET 홈페이지에 표시됩니다.

## <a name="publish-the-new-webapi-backend"></a>새 WebAPI 백 엔드 게시

다음으로 모든 디바이스에서 액세스할 수 있도록 앱을 Azure 웹 사이트에 배포합니다.

1. **AppBackend** 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **게시**를 선택합니다.

2. **Microsoft Azure App Service**를 게시 대상으로 선택한 다음, \*\*[게시]를 선택합니다. App Service 만들기 창이 열립니다. 여기에서 Azure에서 ASP.NET 웹앱을 실행하는 데 필요한 모든 Azure 리소스를 만들 수 있습니다.

    ![Microsoft Azure App Service 타일][B15]

3. **App Service 만들기** 창에서 Azure 계정을 선택합니다. **유형 변경** > **웹앱**을 선택합니다. 기본 **웹앱 이름**을 유지한 다음 **구독**, **리소스 그룹**, **App Service 계획**을 차례로 선택합니다.

4. **만들기**를 선택합니다.

5. **요약** 섹션의 **사이트 URL** 속성을 메모해 둡니다. 이 URL은 자습서의 뒷부분에서 *백 엔드 엔드포인트*입니다.

6. **게시**를 선택합니다.

마법사를 완료한 후 Azure에 ASP.NET 웹앱을 게시한 다음 기본 브라우저에서 앱을 엽니다.  애플리케이션을 Azure App Services에서 볼 수 있습니다.

URL은 http://<app_name>.azurewebsites.net 형식으로 이전에 지정한 웹앱 이름을 사용합니다.

[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/publish-to-app-service.png
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
