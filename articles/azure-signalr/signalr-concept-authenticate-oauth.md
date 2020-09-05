---
title: Azure SignalR Service 클라이언트 인증 가이드
description: e2e 예제에 따라 사용자 고유의 인증을 구현하고 Azure SignalR Service에 통합하는 방법을 알아봅니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.custom: devx-track-javascript, devx-track-csharp
ms.openlocfilehash: da0c3284b42be6d9054b7386839004b57d8ca274
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935741"
---
# <a name="azure-signalr-service-authentication"></a>Azure SignalR Service 인증

이 자습서는 빠른 시작에서 소개한 대화방 애플리케이션에서 진행됩니다. [SignalR Service로 대화방 만들기](signalr-quickstart-dotnet-core.md)를 아직 완료하지 않았으면 해당 연습을 먼저 완료합니다.

이 자습서에서는 사용자 고유의 인증을 구현하고 Microsoft Azure SignalR Service에 통합하는 방법을 설명합니다.

처음에 빠른 시작의 대화방 애플리케이션에서 사용된 인증은 실제 시나리오에 비해 너무 간단합니다. 이 애플리케이션에서 각 클라이언트는 자신이 누구인지 클레임할 수 있으며, 서버는 간단히 수락합니다. 이 방법은 Rogue 사용자가 다른 사람을 가장하여 중요한 데이터에 액세스하는 실제 애플리케이션에서는 별로 유용하지 않습니다.

[GitHub](https://github.com/)는 [OAuth](https://oauth.net/)라는 인기 있는 업계 표준 프로토콜에 따른 인증 API를 제공합니다. 이러한 API는 타사 애플리케이션에서 GitHub 계정을 인증할 수 있도록 합니다. 이 자습서에서는 클라이언트가 대화방 애플리케이션에 로그인하도록 허용하기 전에 이러한 API를 사용하여 GitHub 계정을 통한 인증을 구현합니다. GitHub 계정을 인증한 후 계정 정보는 웹 클라이언트가 인증받는 데 사용하는 쿠키로 추가됩니다.

GitHub를 통해 제공되는 OAuth 인증 API에 대한 자세한 내용은 [인증 기본 사항](https://developer.github.com/v3/guides/basics-of-authentication/)을 참조하세요.

이 빠른 시작의 단계를 완료하려면 아무 코드 편집기나 사용할 수 있습니다. 그러나 [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 뛰어난 옵션입니다.

이 자습서에 대한 코드는 [AzureSignalR-samples GitHub 리포지토리](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/GitHubChat)에서 다운로드할 수 있습니다.

![Azure에서 호스트되는 OAuth 완료](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * GitHub 계정을 사용하여 새 OAuth 앱 등록
> * GitHub 인증을 지원하기 위한 인증 컨트롤러 추가
> * Azure에 ASP.NET Core 웹앱 배포

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음 필수 구성 요소가 있어야 합니다.

* [GitHub](https://github.com/)에서 만들어진 계정
* [Git](https://git-scm.com/)
* [.NET Core SDK](https://www.microsoft.com/net/download/windows)
* [Azure Cloud Shell이 구성됨](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples) GitHub 리포지토리를 다운로드하거나 복제합니다.

## <a name="create-an-oauth-app"></a>OAuth 앱 만들기

1. 웹 브라우저를 열고 `https://github.com`으로 이동한 후 계정에 로그인합니다.

2. 사용자 계정의 경우, **설정** > **개발자 설정**으로 이동한 후 **새 애플리케이션 등록** 또는 *OAuth 앱* 아래의 **새 OAuth 앱**을 클릭합니다.

3. 새 OAuth 앱에 대해 다음 설정을 사용한 후 **애플리케이션 등록**을 클릭합니다.

    | 설정 이름 | 제안 값 | Description |
    | ------------ | --------------- | ----------- |
    | 애플리케이션 이름 | *Azure SignalR Chat* | GitHub 사용자는 인증하는 앱을 인식하고 신뢰할 수 있어야 합니다.   |
    | 홈페이지 URL | `http://localhost:5000/home` | |
    | 애플리케이션 설명 | *GitHub 인증에서 Azure SignalR Service를 사용하는 대화방 샘플* | 애플리케이션 사용자가 사용 중인 인증 컨텍스트를 이해하는 데 도움이 되는 애플리케이션에 대한 유용한 설명입니다. |
    | 권한 부여 호출 URL | `http://localhost:5000/signin-github` | 이 설정은 OAuth 애플리케이션에 대한 가장 중요한 설정입니다. GitHub가 성공적인 인증 후에 사용자를 반환하는 콜백 URL입니다. 이 자습서에서는 *AspNet.Security.OAuth.GitHub* 패키지에 대한 기본 콜백 URL인 */signin-github*를 사용해야 합니다.  |

4. 새 OAuth 앱 등록이 완료되면 다음 명령을 사용하여 *클라이언트 ID* 및 *클라이언트 암호*를 보안 관리자에 추가합니다. *Your_GitHub_Client_Id* 및 *Your_GitHub_Client_Secret*을 OAuth 앱에 대한 값으로 바꿉니다.

    ```dotnetcli
    dotnet user-secrets set GitHubClientId Your_GitHub_Client_Id
    dotnet user-secrets set GitHubClientSecret Your_GitHub_Client_Secret
    ```

## <a name="implement-the-oauth-flow"></a>OAuth 흐름 구현

### <a name="update-the-startup-class-to-support-github-authentication"></a>GitHub 인증을 지원하도록 Startup 클래스 업데이트

1. 최신 *Microsoft.AspNetCore.Authentication.Cookies* 및 *AspNet.Security.OAuth.GitHub* 패키지에 대한 참조를 추가하고 모든 패키지를 복원합니다.

    ```dotnetcli
    dotnet add package Microsoft.AspNetCore.Authentication.Cookies -v 2.1.0-rc1-30656
    dotnet add package AspNet.Security.OAuth.GitHub -v 2.0.0-rc2-final
    dotnet restore
    ```

1. *Startup.cs*를 열고 다음 네임스페이스에 대한 `using` 문을 추가합니다.

    ```csharp
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Security.Claims;
    using Microsoft.AspNetCore.Authentication.Cookies;
    using Microsoft.AspNetCore.Authentication.OAuth;
    using Newtonsoft.Json.Linq;
    ```

2. `Startup` 클래스 맨 위에 GitHub OAuth 앱 암호를 포함하는 보안 관리자 키에 대한 상수를 추가합니다.

    ```csharp
    private const string GitHubClientId = "GitHubClientId";
    private const string GitHubClientSecret = "GitHubClientSecret";
    ```

3. GitHub OAuth 앱의 인증을 지원하도록 다음 코드를 `ConfigureServices` 메서드에 추가합니다.

    ```csharp
    services.AddAuthentication(CookieAuthenticationDefaults.AuthenticationScheme)
        .AddCookie()
        .AddGitHub(options =>
        {
            options.ClientId = Configuration[GitHubClientId];
            options.ClientSecret = Configuration[GitHubClientSecret];
            options.Scope.Add("user:email");
            options.Events = new OAuthEvents
            {
                OnCreatingTicket = GetUserCompanyInfoAsync
            };
        });
    ```

4. `GetUserCompanyInfoAsync` 도우미 메서드는 `Startup` 클래스에 추가합니다.

    ```csharp
    private static async Task GetUserCompanyInfoAsync(OAuthCreatingTicketContext context)
    {
        var request = new HttpRequestMessage(HttpMethod.Get, context.Options.UserInformationEndpoint);
        request.Headers.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));
        request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", context.AccessToken);

        var response = await context.Backchannel.SendAsync(request,
            HttpCompletionOption.ResponseHeadersRead, context.HttpContext.RequestAborted);

        var user = JObject.Parse(await response.Content.ReadAsStringAsync());
        if (user.ContainsKey("company"))
        {
            var company = user["company"].ToString();
            var companyIdentity = new ClaimsIdentity(new[]
            {
                new Claim("Company", company)
            });
            context.Principal.AddIdentity(companyIdentity);
        }
    }
    ```

5. Startup 클래스의 `Configure` 메서드를 다음 코드 줄로 업데이트하고 파일을 저장합니다.

    ```csharp
    app.UseAuthentication();
    ```

### <a name="add-an-authentication-controller"></a>인증 컨트롤러 추가

이 섹션에서는 GitHub OAuth 앱을 사용하여 클라이언트를 인증하는 `Login` API를 구현합니다. 인증되면 API는 클라이언트를 채팅 앱으로 다시 리디렉션하기 전에 웹 클라이언트 응답에 쿠키를 추가합니다. 그러면 해당 쿠키는 클라이언트를 식별하는 데 사용됩니다.

1. 새 컨트롤러 코드 파일을 *chattest\Controllers* 디렉터리에 추가합니다. 파일 이름을 *AuthController.cs*로 지정합니다.

2. 인증 컨트롤러에 대해 다음 코드를 추가합니다. 프로젝트 디렉터리가 *chattest*가 아니면 네임스페이스를 업데이트해야 합니다.

    ```csharp
    using AspNet.Security.OAuth.GitHub;
    using Microsoft.AspNetCore.Authentication;
    using Microsoft.AspNetCore.Mvc;

    namespace chattest.Controllers
    {
        [Route("/")]
        public class AuthController : Controller
        {
            [HttpGet("login")]
            public IActionResult Login()
            {
                if (!User.Identity.IsAuthenticated)
                {
                    return Challenge(GitHubAuthenticationDefaults.AuthenticationScheme);
                }

                HttpContext.Response.Cookies.Append("githubchat_username", User.Identity.Name);
                HttpContext.SignInAsync(User);
                return Redirect("/");
            }
        }
    }
    ```

3. 변경 내용을 저장합니다.

### <a name="update-the-hub-class"></a>Hub 클래스 업데이트

기본적으로 웹 클라이언트가 SignalR Service에 연결하려고 하면 연결은 내부적으로 제공되는 액세스 토큰을 기준으로 허가됩니다. 이 액세스 토큰은 인증 ID와 연결되지 않습니다. 이 액세스는 실제로 익명 액세스입니다.

이 섹션에서는 Hub 클래스에 `Authorize` 특성을 추가하고, 인증된 사용자 클레임의 사용자 이름을 읽도록 Hub 메서드를 업데이트하여 실제 인증을 켭니다.

1. *Hub\Chat.cs*를 열고 이러한 네임스페이스에 대한 참조를 추가합니다.

    ```csharp
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Authorization;
    ```

2. 다음과 같이 허브 코드를 업데이트합니다. 이 코드는 `Authorize` 특성을 `Chat` 클래스에 추가하고, Hub 메서드에서 사용자의 인증된 ID를 사용합니다. 또한 `OnConnectedAsync` 메서드가 추가되어 새 클라이언트가 연결될 때마다 대화방에 시스템 메시지가 로깅됩니다.

    ```csharp
    [Authorize]
    public class Chat : Hub
    {
        public override Task OnConnectedAsync()
        {
            return Clients.All.SendAsync("broadcastMessage", "_SYSTEM_", $"{Context.User.Identity.Name} JOINED");
        }

        // Uncomment this line to only allow user in Microsoft to send message
        //[Authorize(Policy = "Microsoft_Only")]
        public void BroadcastMessage(string message)
        {
            Clients.All.SendAsync("broadcastMessage", Context.User.Identity.Name, message);
        }

        public void Echo(string message)
        {
            var echoMessage = $"{message} (echo from server)";
            Clients.Client(Context.ConnectionId).SendAsync("echo", Context.User.Identity.Name, echoMessage);
        }
    }
    ```

3. 변경 내용을 저장합니다.

### <a name="update-the-web-client-code"></a>웹 클라이언트 코드 업데이트

1. *wwwroot\index.html*을 열고 사용자 이름을 몯는 코드를 인증 컨트롤러가 반환한 쿠키를 사용하는 코드로 바꿉니다.

    *index.html*에서 다음 코드를 제거합니다.

    ```javascript
    // Get the user name and store it to prepend to messages.
    var username = generateRandomName();
    var promptMessage = 'Enter your name:';
    do {
        username = prompt(promptMessage, username);
        if (!username || username.startsWith('_') || username.indexOf('<') > -1 || username.indexOf('>') > -1) {
            username = '';
            promptMessage = 'Invalid input. Enter your name:';
        }
    } while(!username)
    ```

    쿠키를 사용하려면 위 코드 대신 다음 코드를 추가합니다.

    ```javascript
    // Get the user name cookie.
    function getCookie(key) {
        var cookies = document.cookie.split(';').map(c => c.trim());
        for (var i = 0; i < cookies.length; i++) {
            if (cookies[i].startsWith(key + '=')) return unescape(cookies[i].slice(key.length + 1));
        }
        return '';
    }
    var username = getCookie('githubchat_username');
    ```

2. 쿠키 사용을 위해 추가한 코드 줄 바로 아래에, `appendMessage` 함수에 대한 다음 정의를 추가합니다.

    ```javascript
    function appendMessage(encodedName, encodedMsg) {
        var messageEntry = createMessageEntry(encodedName, encodedMsg);
        var messageBox = document.getElementById('messages');
        messageBox.appendChild(messageEntry);
        messageBox.scrollTop = messageBox.scrollHeight;
    }
    ```

3. `bindConnectionMessage` 및 `onConnected` 함수를 `appendMessage`를 사용하기 위한 다음 코드로 업데이트합니다.

    ```javascript
    function bindConnectionMessage(connection) {
        var messageCallback = function(name, message) {
            if (!message) return;
            // Html encode display name and message.
            var encodedName = name;
            var encodedMsg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
            appendMessage(encodedName, encodedMsg);
        };
        // Create a function that the hub can call to broadcast messages.
        connection.on('broadcastMessage', messageCallback);
        connection.on('echo', messageCallback);
        connection.onclose(onConnectionError);
    }

    function onConnected(connection) {
        console.log('connection started');
        document.getElementById('sendmessage').addEventListener('click', function (event) {
            // Call the broadcastMessage method on the hub.
            if (messageInput.value) {
                connection
                    .invoke('broadcastMessage', messageInput.value)
                    .catch(e => appendMessage('_BROADCAST_', e.message));
            }

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
        document.getElementById('message').addEventListener('keypress', function (event) {
            if (event.keyCode === 13) {
                event.preventDefault();
                document.getElementById('sendmessage').click();
                return false;
            }
        });
        document.getElementById('echo').addEventListener('click', function (event) {
            // Call the echo method on the hub.
            connection.send('echo', messageInput.value);

            // Clear text box and reset focus for next comment.
            messageInput.value = '';
            messageInput.focus();
            event.preventDefault();
        });
    }
    ```

4. *index.html* 맨 아래에서 아래에 표시된 것처럼 `connection.start()`에 대한 오류 처리기를 업데이트하여 사용자 로그인을 요청하도록 합니다.

    ```javascript
    connection.start()
        .then(function () {
            onConnected(connection);
        })
        .catch(function (error) {
            if (error) {
                if (error.message) {
                    console.error(error.message);
                }
                if (error.statusCode && error.statusCode === 401) {
                    appendMessage('_BROADCAST_', 'You\'re not logged in. Click <a href="/login">here</a> to login with GitHub.');
                }
            }
        });
    ```

5. 변경 내용을 저장합니다.

## <a name="build-and-run-the-app-locally"></a>로컬로 앱 빌드 및 실행

1. 모든 파일에 대한 변경 내용을 저장합니다.

2. .NET Core CLI를 사용하여 앱을 빌드하고 명령 셸에서 다음 명령을 실행합니다.

    ```dotnetcli
    dotnet build
    ```

3. 빌드가 성공적으로 완료되면 다음 명령을 실행하여 웹앱을 로컬로 실행합니다.

    ```dotnetcli
    dotnet run
    ```

    기본적으로 앱은 포트 5000에 로컬로 호스트됩니다.

    ```output
    E:\Testing\chattest>dotnet run
    Hosting environment: Production
    Content root path: E:\Testing\chattest
    Now listening on: http://localhost:5000
                    Application started. Press Ctrl+C to shut down.
    ```

4. 브라우저 창을 시작하고 `http://localhost:5000`으로 이동합니다. 위쪽의 **여기** 링크를 클릭하여 GitHub로 로그인합니다.

    ![Azure에서 호스트되는 OAuth 완료](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

    사용자의 GitHub 계정에 채팅 앱 액세스 권한을 부여할지 묻는 메시지가 표시됩니다. **권한 부여** 단추를 클릭합니다.

    ![OAuth 앱 권한 부여](media/signalr-concept-authenticate-oauth/signalr-authorize-oauth-app.png)

    채팅 애플리케이션으로 다시 리디렉션된 후, GitHub 계정 이름으로 로그인됩니다. 웹 애플리케이션은 추가한 새 인증으로 사용자를 인증하여 계정 이름을 확인했습니다.

    ![계정이 식별됨](media/signalr-concept-authenticate-oauth/signalr-oauth-account-identified.png)

    채팅 앱은 GitHub로 인증을 수행하고 인증 정보를 쿠키로 저장하므로, 다른 사용자가 해당 계정으로 인증을 받고 다른 워크스테이션에서 통신할 수 있도록 Azure에 배포해야 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-the-app-to-azure"></a>Azure에 앱 배포

이 섹션에서는 Azure Cloud Shell의 Azure CLI(명령줄 인터페이스)를 사용하여 Azure에서 ASP.NET 애플리케이션을 호스트할 새 웹앱을 [Azure App Service](https://docs.microsoft.com/azure/app-service/)에서 만듭니다. 이 웹앱은 로컬 Git 배포를 사용하도록 구성됩니다. 또한 SignalR 연결 문자열, GitHub OAuth 앱 암호 및 배포 사용자로도 구성됩니다.

이 섹션의 단계에서는 Azure CLI에 대해 *signalr* 확장을 사용합니다. 다음 명령을 실행하여 Azure CLI용 *signalr* 확장을 설치합니다.

```azurecli-interactive
az extension add -n signalr
```

다음 리소스를 만들 때는 SignalR Service가 있는 동일한 리소스 그룹을 사용해야 합니다. 이렇게 해야 나중에 모든 리소스를 제거하고 싶을 때 훨씬 더 쉽게 작업할 수 있습니다. 제공된 예제에서는 이전 자습서에서 권장되던 그룹 이름인 *SignalRTestResources*를 사용한다고 가정합니다.

### <a name="create-the-web-app-and-plan"></a>웹앱 및 계획 만들기

아래에서 명령 텍스트를 복사하고 매개 변수를 업데이트합니다. 업데이트된 스크립트를 Azure Cloud Shell에 붙여 넣고 **Enter** 키를 눌러 새 App Service 계획 및 웹앱을 만듭니다.

```azurecli-interactive
#========================================================================
#=== Update these variable for your resource group name.              ===
#========================================================================
ResourceGroupName=SignalRTestResources

#========================================================================
#=== Update these variable for your web app.                          ===
#========================================================================
WebAppName=myWebAppName
WebAppPlan=myAppServicePlanName

# Create an App Service plan.
az appservice plan create --name $WebAppPlan --resource-group $ResourceGroupName \
    --sku FREE

# Create the new Web App
az webapp create --name $WebAppName --resource-group $ResourceGroupName \
    --plan $WebAppPlan
```

| 매개 변수 | Description |
| -------------------- | --------------- |
| ResourceGroupName | 이 리소스 그룹 이름은 이전 자습서에서 제안된 것입니다. 모든 자습서 리소스를 그룹화된 상태로 유지하는 것이 좋습니다. 이전 자습서에서 사용한 것과 동일한 리소스 그룹을 사용합니다. |
| WebAppPlan | 새로운 고유한 App Service 계획 이름을 입력합니다. |
| WebAppName | 이것은 새 웹앱의 이름이 되고 URL의 일부로 사용됩니다. 고유 이름을 사용하십시오. 예를 들어 signalrtestwebapp22665120과 같습니다.   |

### <a name="add-app-settings-to-the-web-app"></a>웹앱에 앱 설정 추가

이 섹션에서는 다음 구성 요소에 대한 앱 설정을 추가합니다.

* SignalR Service 리소스 연결 문자열
* GitHub OAuth 앱 클라이언트 ID
* GitHub OAuth 앱 클라이언트 암호

아래에서 명령 텍스트를 복사하고 매개 변수를 업데이트합니다. 업데이트된 스크립트를 Azure Cloud Shell에 붙여 넣고 **Enter** 키를 눌러 앱 설정을 추가합니다.

```azurecli-interactive
#========================================================================
#=== Update these variables for your GitHub OAuth App.                ===
#========================================================================
GitHubClientId=1234567890
GitHubClientSecret=1234567890

#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
SignalRServiceResource=mySignalRresourcename
WebAppName=myWebAppName

# Get the SignalR primary connection string 
primaryConnectionString=$(az signalr key list --name $SignalRServiceResource \
  --resource-group $ResourceGroupName --query primaryConnectionString -o tsv)

#Add an app setting to the web app for the SignalR connection
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "Azure__SignalR__ConnectionString=$primaryConnectionString"

#Add the app settings to use with GitHub authentication
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientId=$GitHubClientId"
az webapp config appsettings set --name $WebAppName \
    --resource-group $ResourceGroupName \
    --settings "GitHubClientSecret=$GitHubClientSecret"
```

| 매개 변수 | Description |
| -------------------- | --------------- |
| GitHubClientId | GitHub OAuth 앱에 대한 비밀 클라이언트 ID를 이 변수에 할당합니다. |
| GitHubClientSecret | GitHub OAuth 앱에 대한 비밀 암호를 이 변수에 할당합니다. |
| ResourceGroupName | 이 변수를 이전 섹션에서 사용한 동일한 리소스 그룹 이름으로 업데이트합니다. |
| SignalRServiceResource | 이 변수를 빠른 시작에서 만든 SignalR Service 리소스의 이름으로 업데이트합니다. 예를 들어 signalrtestsvc48778624와 같습니다. |
| WebAppName | 이 변수를 이전 섹션에서 만든 새 웹앱의 이름으로 업데이트합니다. |

### <a name="configure-the-web-app-for-local-git-deployment"></a>로컬 Git 배포에 대한 웹앱 구성

Azure Cloud Shell에서 다음 스크립트를 붙여 넣습니다. 이 스크립트는 Git을 사용하여 웹앱에 코드를 배포할 때 사용할 새 배포 사용자 이름 및 암호를 만듭니다. 또한 로컬 Git 리포지토리로 배포하기 위해 웹앱을 구성하고, Git 배포 URL을 반환합니다.

```azurecli-interactive
#========================================================================
#=== Update these variables for your resources.                       ===
#========================================================================
ResourceGroupName=SignalRTestResources
WebAppName=myWebAppName

#========================================================================
#=== Update these variables for your deployment user.                 ===
#========================================================================
DeploymentUserName=myUserName
DeploymentUserPassword=myPassword

# Add the desired deployment user name and password
az webapp deployment user set --user-name $DeploymentUserName \
    --password $DeploymentUserPassword

# Configure Git deployment and note the deployment URL in the output
az webapp deployment source config-local-git --name $WebAppName \
    --resource-group $ResourceGroupName \
    --query [url] -o tsv
```

| 매개 변수 | Description |
| -------------------- | --------------- |
| DeploymentUserName | 새 배포 사용자 이름을 선택합니다. |
| DeploymentUserPassword | 새 배포 사용자의 암호를 선택합니다. |
| ResourceGroupName | 이전 섹션에서 사용한 것과 동일한 리소스 그룹 이름을 사용합니다. |
| WebAppName | 이것은 이전에 만든 새 웹앱의 이름이 됩니다. |

이 명령에서 반환된 Git 배포 URL을 적어둡니다. 이 URL은 나중에 필요하기 때문입니다.

### <a name="deploy-your-code-to-the-azure-web-app"></a>Azure 웹앱에 코드 배포

코드를 배포하려면 Git 셸에서 다음 명령을 실행합니다.

1. 프로젝트 디렉터리의 루트로 이동합니다. Git 리포지토리를 사용하여 프로젝트를 초기화하지 않은 경우 다음 명령을 실행합니다.

    ```bash
    git init
    ```

2. 앞서 적어둔 Git 배포 URL에 대한 원격 기능을 추가합니다.

    ```bash
    git remote add Azure <your git deployment url>
    ```

3. 초기화된 리포지토리에 모든 파일을 준비하고 커밋을 추가합니다.

    ```bash
    git add -A
    git commit -m "init commit"
    ```

4. Azure에서 웹앱에 코드를 배포합니다.

    ```bash
    git push Azure master
    ```

    코드를 Azure에 배포하기 위해 인증하라는 메시지가 표시됩니다. 위에서 만든 배포 사용자의 사용자 이름 및 암호를 입력합니다.

### <a name="update-the-github-oauth-app"></a>GitHub OAuth 앱 업데이트

수행해야 하는 마지막 작업은 GitHub OAuth 앱의 **홈페이지 URL** 및 **권한 부여 콜백 URL**이 새로 호스트된 앱을 가리키도록 업데이트하는 것입니다.

1. 브라우저에서 [https://github.com](https://github.com)을 열고 계정의 **설정** > **개발자 설정** > **Oauth 앱**으로 이동합니다.

2. 인증 앱을 클릭하고 아래와 같이 **홈페이지 URL** 및 **권한 부여 콜백 URL**을 업데이트합니다.

    | 설정 | 예제 |
    | ------- | ------- |
    | 홈페이지 URL | `https://signalrtestwebapp22665120.azurewebsites.net/home` |
    | 권한 부여 호출 URL | `https://signalrtestwebapp22665120.azurewebsites.net/signin-github` |

3. 웹앱 URL로 이동한 다음, 애플리케이션을 테스트합니다.

    ![Azure에서 호스트되는 OAuth 완료](media/signalr-concept-authenticate-oauth/signalr-oauth-complete-azure.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서를 계속 진행하려는 경우 이 빠른 시작에서 만든 리소스를 그대로 두었다가 다음 자습서에서 다시 사용할 수 있습니다.

그렇지 않고, 빠른 시작 샘플 애플리케이션 사용이 끝나면 이 빠른 시작에서 만든 Azure 리소스를 삭제하여 요금이 청구되는 것을 방지할 수 있습니다.

> [!IMPORTANT]
> 리소스 그룹 삭제는 취소할 수 없으며 해당 리소스 그룹 및 해당 그룹 안에 있는 모든 리소스는 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 유지하려는 리소스가 포함된 기존 리소스 그룹 내에 이 샘플을 호스트하기 위한 리소스를 만든 경우 리소스 그룹을 삭제하는 대신, 해당 블레이드에서 각 리소스를 개별적으로 삭제할 수 있습니다.

[Azure 포털](https://portal.azure.com) 에 로그인하고 **리소스 그룹**을 클릭합니다.

**이름을 기준으로 필터링...** 텍스트 상자에 리소스 그룹의 이름을 입력합니다. 이 문서의 지침에서는 *SignalRTestResources*라는 리소스 그룹을 사용합니다. 결과 목록의 리소스 그룹에서 **...** 를 클릭한 다음, **리소스 그룹 삭제**를 클릭합니다.

![DELETE](./media/signalr-concept-authenticate-oauth/signalr-delete-resource-group.png)

리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 입력하여 확인한 후 **삭제**를 클릭합니다.

잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure SignalR Service로 인증하는 보다 나은 방법을 제공하기 위해 OAuth 인증을 추가했습니다. Azure SignalR Server에 대해 자세히 알아보려면 SignalR Service용 Azure CLI 샘플을 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure SignalR CLI 샘플](./signalr-reference-cli.md)