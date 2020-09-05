---
title: Microsoft ID 플랫폼 UWP 시작 | Azure
description: UWP(유니버설 Windows 플랫폼) 애플리케이션이 Microsoft ID 플랫폼 엔드포인트에서 액세스 토큰이 필요한 API를 어떻게 호출하는지 알아봅니다.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/13/2019
ms.author: jmprieur
ms.custom: devx-track-csharp, aaddev, identityplatformtop40
ms.openlocfilehash: acdc23c664f84882916b91b8f8698ee36b1e6cd3
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165552"
---
# <a name="call-the-microsoft-graph-api-from-a-universal-windows-platform-application-xaml"></a>유니버설 Windows 플랫폼 애플리케이션(XAML)에서 Microsoft Graph API 호출

> [!div renderon="docs"]

이 가이드에서는 네이티브 UWP(유니버설 Windows 플랫폼) 애플리케이션에서 액세스 토큰을 요청하는 방법에 대해 설명합니다. 그런 다음, 애플리케이션에서 Microsoft Graph API를 호출합니다. 이 가이드는 Microsoft ID 플랫폼 엔드포인트의 액세스 토큰이 필요한 다른 API에도 적용됩니다.

이 가이드의 끝에서 애플리케이션은 개인 계정을 사용하여 보호된 API를 호출합니다. outlook.com, live.com 및 기타를 예로 듭니다. 또한 애플리케이션은 Azure AD(Azure Active Directory)가 있는 회사 또는 조직의 회사 및 학교 계정을 호출합니다.

>[!NOTE]
> 이 가이드에는 유니버설 Windows 플랫폼 개발이 설치된 Visual Studio가 필요합니다. 유니버설 Windows 플랫폼 앱을 개발하기 위해 Visual Studio를 다운로드하고 구성하는 방법에 대한 지침은 [설정](/windows/uwp/get-started/get-set-up)을 참조하세요.

>[!NOTE]
> Microsoft ID 플랫폼을 처음 접하는 경우 [UWP(유니버설 Windows 플랫폼) 애플리케이션에서 Microsoft Graph API 호출 빠른 시작](quickstart-v2-uwp.md)을 먼저 진행하세요.

## <a name="how-this-guide-works"></a>이 가이드의 작동 방식

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](./media/tutorial-v2-windows-uwp/uwp-intro.svg)

이 가이드에서는 Microsoft Graph API를 쿼리하는 UWP 애플리케이션 샘플을 만듭니다. 이 시나리오에서는 토큰이 인증 헤더를 사용하여 HTTP 요청에 추가됩니다. Microsoft 인증 라이브러리에서는 토큰 획득 및 갱신을 처리합니다.

## <a name="nuget-packages"></a>NuGet 패키지

이 가이드에서는 다음 NuGet 패키지를 사용합니다.

|라이브러리|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft 인증 라이브러리|
|[Microsoft.Graph](https://www.nuget.org/packages/Microsoft.Graph)|Microsoft Graph 클라이언트 라이브러리|

## <a name="set-up-your-project"></a>프로젝트 설정

이 섹션에서는 Microsoft에 로그인과 Windows 데스크톱 .NET 애플리케이션(XAML)을 통합하는 단계별 지침을 제공합니다. 그런 다음, 애플리케이션에서 Microsoft Graph API와 같은 토큰이 필요한 웹 API를 쿼리할 수 있습니다.

이 가이드에서는 Microsoft Graph API를 쿼리하는 단추와 로그아웃하는 단추를 표시하는 애플리케이션을 만듭니다. 또한 호출 결과가 포함된 텍스트 상자도 표시합니다.

> [!NOTE]
> 이 샘플의 Visual Studio 프로젝트를 만드는 대신 다운로드하고 싶나요? [프로젝트를 다운로드](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)하고, [애플리케이션 등록](#register-your-application "애플리케이션 등록 단계") 단계로 건너뛰어 코드 샘플을 구성한 후에 실행하세요.

### <a name="create-your-application"></a>애플리케이션 만들기

1. Visual Studio를 열고 **새 프로젝트 만들기**를 선택합니다.
1. **새 프로젝트 만들기**에서 C#용 **빈 앱(유니버설 Windows)** , **다음**을 차례로 선택합니다.
1. **새 프로젝트 구성**에서 앱 이름을 지정하고 **만들기**를 선택합니다.
1. 메시지가 표시되면 **새 유니버설 Windows 플랫폼 프로젝트**에서 **대상** 및 **최소** 버전에 대한 버전을 선택하고 **확인**을 선택합니다.

   ![최소 및 대상 버전](./media/tutorial-v2-windows-uwp/select-uwp-target-minimum.png)

### <a name="add-microsoft-authentication-library-to-your-project"></a>프로젝트에 Microsoft 인증 라이브러리 추가

1. Visual Studio에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔**을 선택합니다.
1. **패키지 관리자 콘솔** 창에서 다음 명령을 복사하여 붙여넣습니다.

    ```powershell
    Install-Package Microsoft.Identity.Client
    Install-Package Microsoft.Graph
    ```

   > [!NOTE]
   > 첫 번째 명령은 [Microsoft 인증 라이브러리(MSAL.NET)](https://aka.ms/msal-net)를 설치합니다. MSAL.NET은 Microsoft ID 플랫폼으로 보호되는 API에 액세스하는 사용자 토큰을 획득하고, 캐싱하고, 새로 고칩니다. 두 번째 명령은 [Microsoft Graph .NET 클라이언트 라이브러리](https://github.com/microsoftgraph/msgraph-sdk-dotnet)를 설치하여 Microsoft Graph에 대한 요청을 인증하고 서비스를 호출합니다.

### <a name="create-your-applications-ui"></a>애플리케이션 UI 만들기

Visual Studio는 프로젝트 템플릿의 일부로 *MainPage.xaml*을 만듭니다. 이 파일을 연 다음, 애플리케이션의 **Grid** 노드를 다음 코드로 바꿉니다.

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <TextBlock Text="API Call Results" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <TextBlock Text="Token Info" Margin="2,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

### <a name="use-microsoft-authentication-library-to-get-a-token-for-the-microsoft-graph-api"></a>Microsoft 인증 라이브러리를 사용하여 Microsoft Graph API에 대한 토큰 가져오기

이 섹션에서는 Microsoft 인증 라이브러리를 사용하여 Microsoft Graph API에 대한 토큰을 가져오는 방법을 보여줍니다. *MainPage.xaml.cs* 파일을 변경합니다.

1. *MainPage.xaml.cs*에서 다음 참조를 추가합니다.

    ```csharp
    using Microsoft.Identity.Client;
    using Microsoft.Graph;
    using System.Diagnostics;
    using System.Threading.Tasks;
    ```

1. `MainPage` 클래스를 다음 코드로 바꿉니다.

    ```csharp
    public sealed partial class MainPage : Page
    {
       
        //Set the scope for API call to user.read
        private string[] scopes = new string[] { "user.read" };

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        private const string ClientId = "[Application Id pasted from the application registration portal]";

        private const string Tenant = "common"; // Alternatively "[Enter your tenant, as obtained from the Azure portal, e.g. kko365.onmicrosoft.com]"
        private const string Authority = "https://login.microsoftonline.com/" + Tenant;

        // The MSAL Public client app
        private static IPublicClientApplication PublicClientApp;

        private static string MSGraphURL = "https://graph.microsoft.com/v1.0/";
        private static AuthenticationResult authResult;

        public MainPage()
        {
            this.InitializeComponent();
        }

        /// <summary>
        /// Call AcquireTokenAsync - to acquire a token requiring user to sign in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            try
            {
                // Sign in user using MSAL and obtain an access token for Microsoft Graph
                GraphServiceClient graphClient = await SignInAndInitializeGraphServiceClient(scopes);

                // Call the /me endpoint of Graph
                User graphUser = await graphClient.Me.Request().GetAsync();

                // Go back to the UI thread to make changes to the UI
                await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
                {
                    ResultText.Text = "Display Name: " + graphUser.DisplayName + "\nBusiness Phone: " + graphUser.BusinessPhones.FirstOrDefault()
                                      + "\nGiven Name: " + graphUser.GivenName + "\nid: " + graphUser.Id
                                      + "\nUser Principal Name: " + graphUser.UserPrincipalName;
                    DisplayBasicTokenInfo(authResult);
                    this.SignOutButton.Visibility = Visibility.Visible;
                });
            }
            catch (MsalException msalEx)
            {
                await DisplayMessageAsync($"Error Acquiring Token:{System.Environment.NewLine}{msalEx}");
            }
            catch (Exception ex)
            {
                await DisplayMessageAsync($"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}");
                return;
            }
        }
                /// <summary>
        /// Signs in the user and obtains an access token for Microsoft Graph
        /// </summary>
        /// <param name="scopes"></param>
        /// <returns> Access Token</returns>
        private static async Task<string> SignInUserAndGetTokenUsingMSAL(string[] scopes)
        {
            // Initialize the MSAL library by building a public client application
            PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .WithUseCorporateNetwork(false)
                .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
                 .WithLogging((level, message, containsPii) =>
                 {
                     Debug.WriteLine($"MSAL: {level} {message} ");
                 }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
                .Build();

            // It's good practice to not do work on the UI thread, so use ConfigureAwait(false) whenever possible.
            IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
            IAccount firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                                  .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilentAsync. This indicates you need to call AcquireTokenAsync to acquire a token
                Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                authResult = await PublicClientApp.AcquireTokenInteractive(scopes)
                                                  .ExecuteAsync()
                                                  .ConfigureAwait(false);

            }
            return authResult.AccessToken;
        }
    }
    ```

#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기<a name="more-information"></a>

`AcquireTokenInteractive` 메서드는 사용자에게 로그인하라는 메시지를 표시하는 창을 생성합니다. 애플리케이션에서는 일반적으로 사용자가 보호된 리소스에 액세스하기 위해 처음에는 대화형으로 로그인해야 합니다. 토큰을 획득하는 자동 작업에 실패한 경우 로그인해야 할 수도 있습니다. 사용자의 암호가 만료된 경우를 예로 들 수 있습니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

`AcquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. `AcquireTokenInteractive`가 처음 실행되고 사용자에게 자격 증명을 요청하는 메시지가 표시되면 `AcquireTokenSilent` 메서드를 사용하여 나중에 호출할 토큰을 요청합니다. 해당 메서드는 토큰을 자동으로 가져옵니다. Microsoft 인증 라이브러리는 토큰 캐시 및 갱신을 처리합니다.

결국 `AcquireTokenSilent` 메서드가 실패합니다. 실패 이유에는 다른 디바이스에서 로그아웃했거나 암호를 변경한 사용자가 포함됩니다. Microsoft 인증 라이브러리는 이 문제에 대한 대화형 조치가 필요한 것으로 감지되면 `MsalUiRequiredException` 예외를 throw합니다. 애플리케이션에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

* 애플리케이션에서 `AcquireTokenInteractive`를 즉시 호출합니다. 이 호출로 인해 사용자에게 로그인하라는 메시지가 표시됩니다. 일반적으로 사용자가 오프라인 콘텐츠를 사용할 수 없는 온라인 애플리케이션에는 이 방법을 사용합니다. 이 설정 안내에서 생성하는 샘플은 패턴을 따릅니다. 샘플을 처음으로 실행할 때 작업에 표시됩니다.

   애플리케이션을 사용한 사용자가 없으므로 `accounts.FirstOrDefault()`에는 null 값이 포함되고 `MsalUiRequiredException` 예외가 throw됩니다.

   그런 다음, 샘플의 코드는 `AcquireTokenInteractive`를 호출하여 예외를 처리합니다. 이 호출로 인해 사용자에게 로그인하라는 메시지가 표시됩니다.

* 애플리케이션에서 사용자에게 로그인해야 한다는 시각적 표시를 제공합니다. 그런 다음, 로그인할 적절한 시기를 선택할 수 있습니다. 애플리케이션에서 나중에 `AcquireTokenSilent`를 다시 시도할 수 있습니다. 사용자가 다른 애플리케이션 기능을 중단 없이 사용할 수 있는 경우 이 방법을 사용합니다. 예제로 애플리케이션에서 오프라인 콘텐츠를 사용할 수 있는 경우입니다. 이 경우 사용자는 로그인하려는 시기를 결정할 수 있습니다. 네트워크를 일시적으로 사용할 수 없으면 애플리케이션에서 `AcquireTokenSilent`를 다시 시도할 수 있습니다.

### <a name="instantiate-the-microsoft-graph-service-client-by-obtaining-the-token-from-the-signinuserandgettokenusingmsal-method"></a>SignInUserAndGetTokenUsingMSAL 메서드에서 토큰을 획득하여 Microsoft Graph 서비스 클라이언트를 인스턴스화합니다.

다음과 같은 새 메서드를 *MainPage.xaml.cs*에 추가합니다.

```csharp
      /// <summary>
     /// Sign in user using MSAL and obtain a token for Microsoft Graph
     /// </summary>
     /// <returns>GraphServiceClient</returns>
     private async static Task<GraphServiceClient> SignInAndInitializeGraphServiceClient(string[] scopes)
     {
         GraphServiceClient graphClient = new GraphServiceClient(MSGraphURL,
             new DelegateAuthenticationProvider(async (requestMessage) =>
             {
                 requestMessage.Headers.Authorization = new AuthenticationHeaderValue("bearer", await SignInUserAndGetTokenUsingMSAL(scopes));
             }));

         return await Task.FromResult(graphClient);
     }
```

#### <a name="more-information-on-making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대한 REST 호출에 관한 추가 정보

이 샘플 애플리케이션에서 `GetGraphServiceClient` 메서드는 액세스 토큰을 사용하여 `GraphServiceClient`를 인스턴스화합니다. 그런 다음, `GraphServiceClient`를 사용하여 **me** 엔드포인트에서 사용자의 프로필 정보를 가져옵니다.

### <a name="add-a-method-to-sign-out-the-user"></a>사용자를 로그아웃하는 메서드 추가

사용자를 로그아웃하려면 *MainPage.xaml.cs*에 다음 메서드를 추가합니다.

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    IEnumerable<IAccount> accounts = await PublicClientApp.GetAccountsAsync().ConfigureAwait(false);
    IAccount firstAccount = accounts.FirstOrDefault();

    try
    {
        await PublicClientApp.RemoveAsync(firstAccount).ConfigureAwait(false);
        await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal, () =>
        {
            ResultText.Text = "User has signed out";
            this.CallGraphButton.Visibility = Visibility.Visible;
                this.SignOutButton.Visibility = Visibility.Collapsed;
            });
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing out user: {ex.Message}";
        }
    }
```

> [!NOTE]
> MSAL.NET은 비동기 메서드를 사용하여 토큰을 획득하거나 계정을 조작합니다. UI 스레드에서 UI 작업을 지원해야 합니다. 이는 `Dispatcher.RunAsync` 호출 및 `ConfigureAwait(false)`를 호출하기 위한 예방 조치에 대한 이유입니다.

#### <a name="more-information-about-signing-out"></a>로그 아웃에 대한 자세한 정보<a name="more-information-on-sign-out"></a>

`SignOutButton_Click` 메서드는 Microsoft 인증 라이브러리 사용자 캐시에서 사용자를 제거합니다. 이 메서드는 현재 사용자를 제거하라고 Microsoft 인증 라이브러리에 효과적으로 지시합니다. 토큰을 획득하기 위한 이후의 요청은 대화형인 경우에만 성공합니다.

이 샘플의 애플리케이션은 단일 사용자를 지원합니다. Microsoft 인증 라이브러리는 사용자가 여러 계정에서 로그인할 수 있는 시나리오를 지원합니다. 사용자 한 명이 여러 계정을 가질 수 있는 이메일 애플리케이션을 예로 듭니다.

### <a name="display-basic-token-information"></a>기본 토큰 정보 표시

토큰에 대한 기본 정보를 표시하려면 *MainPage.xaml.cs* 파일에 다음 메서드를 추가합니다.

```csharp
/// <summary>
/// Display basic information contained in the token. Needs to be called from the UI thread.
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"User Name: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

#### <a name="more-information"></a>자세한 정보<a name="more-information-1"></a>

**OpenID Connect**를 사용하여 획득한 ID 토큰에는 사용자와 관련된 정보의 작은 하위 세트도 포함됩니다. `DisplayBasicTokenInfo`는 토큰에 포함된 기본 정보를 표시합니다. 이 정보에는 사용자의 표시 이름과 ID가 포함되어 있습니다. 또한 토큰의 만료 날짜와 액세스 토큰 자체를 나타내는 문자열도 포함되어 있습니다. **Microsoft Graph API 호출** 단추를 여러 번 선택하면 동일한 토큰이 이후의 요청에 다시 사용되었음을 알 수 있습니다. 또한 Microsoft 인증 라이브러리가 토큰을 갱신할 때가 되었다고 판단할 때 만료 날짜가 연장되는 것도 볼 수 있습니다.

### <a name="display-message"></a>메시지 표시

다음과 같은 새 메서드를 *MainPage.xaml.cs*에 추가합니다.

```csharp
/// <summary>
/// Displays a message in the ResultText. Can be called from any thread.
/// </summary>
private async Task DisplayMessageAsync(string message)
{
     await Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
         () =>
         {
             ResultText.Text = message;
         });
     }
```

## <a name="register-your-application"></a>애플리케이션 등록

이제 애플리케이션을 등록해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **앱 등록**을 선택합니다.
1. **새 등록**을 선택합니다. 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: *UWP-App-calling-MSGraph*)을 입력합니다.
1. **지원되는 계정 유형** 아래에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정(예: Skype, Xbox)** 을 선택합니다. 그런 다음, **등록**을 선택하여 계속합니다.
1. 개요 페이지에서 **애플리케이션(클라이언트) ID** 값을 찾아서 복사합니다. Visual Studio로 돌아가서 *MainPage.xaml.cs*를 열고 `ClientId` 값을 이 값으로 바꿉니다.

애플리케이션에 대한 인증을 구성합니다.

1. [Azure Portal](https://portal.azure.com)로 돌아가서 **관리** 아래에서 **인증**을 선택합니다.
1. **리디렉션 URI** | **퍼블릭 클라이언트(모바일, 데스크톱)에 대해 제안된 리디렉션 URI** 섹션에서 https://login.microsoftonline.com/common/oauth2/nativeclient 를 확인합니다.
1. **저장**을 선택합니다.

애플리케이션에 대한 API 권한을 구성합니다.

1. **관리** 아래에서 **API 권한**을 선택합니다.
1. **권한 추가**를 선택한 다음, **Microsoft API**를 선택했는지 확인합니다.
1. **Microsoft Graph**를 선택합니다.
1. **위임된 권한**을 선택하고, *Use.Read*를 검색하고, **User.Read**가 선택되어 있는지 확인합니다.
1. 변경한 경우 **권한 추가**를 선택하여 저장합니다.

## <a name="enable-integrated-authentication-on-federated-domains-optional"></a>페더레이션된 도메인에서 통합된 인증을 사용하도록 설정(선택 사항)

통합 Windows 인증을 페더레이션된 Azure AD 도메인에서 사용하는 경우 이를 사용하도록 설정하려면 애플리케이션 매니페스트에서 추가 기능을 사용하도록 설정해야 합니다. Visual Studio에서 애플리케이션으로 돌아갑니다.

1. *Package.appxmanifest*를 엽니다.
1. **기능**을 선택하고, 다음 설정을 사용하도록 설정합니다.

   * **엔터프라이즈 인증**
   * **개인 네트워크(클라이언트 및 서버)**
   * **공유 사용자 인증서**

> [!IMPORTANT]
> [통합 Windows 인증](https://aka.ms/msal-net-iwa)은 이 샘플에 대해 기본적으로 구성되지 않습니다. `Enterprise Authentication` 또는 `Shared User Certificates` 기능을 요청하는 애플리케이션을 사용하려면 Windows 스토어에서 더 높은 수준을 확인해야 합니다. 또한 일부 개발자가 더 높은 수준의 확인을 수행하려고 합니다. 페더레이션된 Azure AD 도메인과 함께 사용할 통합 Windows 인증이 필요한 경우에만 이 설정을 사용하세요.

## <a name="alternate-approach-to-using-withdefaultredirecturi"></a>WithDefaultRedirectURI()를 사용하는 다른 방법

현재 샘플에서는 `WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")` 메서드가 사용됩니다. `WithDefaultRedirectURI()`를 사용하려면 다음 단계를 완료합니다.

1. 다음과 같이 *MainPage.XAML.cs*에서 `WithRedirectUri`를 `WithDefaultRedirectUri`로 바꿉니다.

   **현재 코드**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority(Authority)
       .WithUseCorporateNetwork(false)
       .WithRedirectUri("https://login.microsoftonline.com/common/oauth2/nativeclient")
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();

   ```

   **업데이트된 코드**

   ```csharp

   PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
       .WithAuthority("https://login.microsoftonline.com/common")
       .WithUseCorporateNetwork(false)
       .WithDefaultRedirectUri()
       .WithLogging((level, message, containsPii) =>
        {
            Debug.WriteLine($"MSAL: {level} {message} ");
        }, LogLevel.Warning, enablePiiLogging: false, enableDefaultPlatformLogging: true)
       .Build();
   ```

2.  *MainPage.xaml.cs*에서 `redirectURI` 필드를 추가하고 중단점을 설정하여 앱의 콜백 URI를 찾습니다.

    ```csharp

    public sealed partial class MainPage : Page
    {
            ...

            string redirectURI = Windows.Security.Authentication.Web.WebAuthenticationBroker
                                .GetCurrentApplicationCallbackUri().ToString();
            public MainPage()
            {
                ...
            }
           ...
    }
  
    ```

    앱을 실행하고, 중단점에 도달하면 `redirectUri` 값을 복사합니다. 이 값은 다음 값과 비슷한 형식입니다.  
    `ms-app://s-1-15-2-1352796503-54529114-405753024-3540103335-3203256200-511895534-1429095407/`

    그런 다음, 값을 가져올 때 한 번만 필요한 이 코드 줄을 제거할 수 있습니다. 

3. 앱 등록 포털에서, 반환된 값을 **인증** 창의 **RedirectUri**에 추가합니다.
   
## <a name="test-your-code"></a>코드 테스트

애플리케이션을 테스트하려면 **F5** 키를 선택하여 Visual Studio에서 프로젝트를 실행합니다. 아래와 같이 주 창이 표시됩니다.

![애플리케이션의 사용자 인터페이스](./media/tutorial-v2-windows-uwp/testapp-ui-vs2019.png)

테스트할 준비가 되면 **Call Microsoft Graph API**를 선택합니다. live.com 또는 outlook.com 같은 Azure AD 조직 계정이나 Microsoft 계정을 사용하여 로그인합니다. 사용자가 이 테스트를 처음으로 실행하면 애플리케이션에서 사용자에게 로그인을 요청하는 창을 표시합니다.

### <a name="consent"></a>동의

애플리케이션에 처음으로 로그인하면 다음 이미지와 비슷한 동의 화면이 표시됩니다. 액세스하려면 **예**를 선택하여 명시적으로 동의합니다.

![액세스 동의 화면](./media/tutorial-v2-windows-uwp/consentscreen-vs2019.png)

### <a name="expected-results"></a>예상 결과

**API 호출 결과** 화면에 Microsoft Graph API 호출에서 반환된 사용자 프로필 정보가 표시됩니다.

![API 호출 결과 화면](./media/tutorial-v2-windows-uwp/uwp-results-screen-vs2019.png)

**토큰 정보** 상자에 `AcquireTokenInteractive` 또는 `AcquireTokenSilent`를 통해 획득한 토큰에 대한 기본 정보가 표시됩니다.

|속성  |형식  |Description |
|---------|---------|---------|
|`Username` |`user@domain.com` |사용자를 식별하는 사용자 이름입니다.|
|`Token Expires` |`DateTime` |토큰이 만료되는 시간입니다. Microsoft 인증 라이브러리는 필요에 따라 토큰을 갱신하여 만료 날짜를 연장합니다.|

### <a name="more-information-about-scopes-and-delegated-permissions"></a>범위 및 위임된 권한에 대한 자세한 내용

Microsoft Graph API는 `user.read` 범위가 있어야만 사용자 프로필을 읽을 수 있습니다. 이 범위는 애플리케이션 등록 포털에 등록된 모든 애플리케이션에 기본적으로 추가됩니다. 다른 Microsoft Graph용 API와 백 엔드 서버용 사용자 지정 API에는 추가 범위가 필요할 수 있습니다. 예를 들어 사용자의 일정을 나열하려면 Microsoft Graph API에서 `Calendars.Read` 범위가 필요합니다.

애플리케이션의 컨텍스트에서 사용자 일정에 액세스하려면 애플리케이션 등록 정보에 `Calendars.Read` 위임 권한을 추가합니다. 그런 다음, `Calendars.Read` 범위를 `acquireTokenSilent` 호출에 추가합니다.

> [!NOTE]
> 범위 수를 늘리면 사용자에게 추가 동의를 요청하는 메시지가 표시될 수 있습니다.

## <a name="known-issues"></a>알려진 문제

### <a name="issue-1"></a>문제 1

페더레이션된 Azure AD 도메인의 애플리케이션에 로그인할 때 다음 오류 메시지 중 하나가 수신됩니다.

* "요청에서 유효한 클라이언트 인증서를 찾을 수 없습니다."
* "사용자의 인증서 저장소에서 유효한 인증서를 찾을 수 없습니다."
* "다른 인증 방법을 다시 선택하십시오."

**원인:** 엔터프라이즈 및 인증서 기능을 사용할 수 없습니다.

**해결 방법:** [페더레이션된 도메인에서 통합된 인증을 사용하도록 설정(선택 사항)](#enable-integrated-authentication-on-federated-domains-optional)의 단계를 수행합니다.

### <a name="issue-2"></a>문제 2

[페더레이션된 도메인에서 통합된 인증](#enable-integrated-authentication-on-federated-domains-optional)을 활성화하고 Windows 10 컴퓨터에서 Windows Hello를 사용하여 다단계 인증이 구성된 환경에 로그인하려고 시도합니다. 인증서 목록이 표시됩니다. PIN을 사용하도록 선택해도 PIN 창이 표시되지 않습니다.

**원인:** 이 문제는 Windows 10 데스크톱에서 실행되는 UWP 애플리케이션의 웹 인증 broker에 대해 알려진 제한 사항입니다. Windows 10 Mobile에서 제대로 작동합니다.

**해결 방법:** **기타 옵션으로 로그인**을 선택합니다. 그런 다음, **사용자 이름과 암호로 로그인**을 선택합니다. **암호 제공**을 선택합니다. 그런 다음, 전화 인증 프로세스로 진행합니다.

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]