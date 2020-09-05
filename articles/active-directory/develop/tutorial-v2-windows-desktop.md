---
title: Microsoft ID 플랫폼 Windows 데스크톱 시작
description: Windows Desktop .NET(XAML) 애플리케이션이 액세스 토큰을 얻고 Microsoft ID 플랫폼에 의해 보호되는 API를 호출하는 방법.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: tutorial
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: a865bab690c79288bdffcd7cebe424d1bb1969c0
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "82181542"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Windows 데스크톱 앱에서 Microsoft Graph API 호출

이 가이드에서는 네이티브 Windows Desktop.NET(XAML) 애플리케이션에서 액세스 토큰을 사용하여 Microsoft Graph API를 호출하는 방법을 보여줍니다. 이 앱은 개발자 v2.0 엔드포인트에 대한 Microsoft ID 플랫폼의 액세스 토큰을 필요로 하는 기타 API에 액세스할 수 있습니다. 이 플랫폼은 이전에 Azure AD라고 명명되었습니다.

이 가이드를 완료하면 애플리케이션은 개인 계정(outlook.com, live.com 등 포함)을 사용하는 보호된 API를 호출할 수 있습니다. 또한 애플리케이션은 Azure Active Directory를 사용하는 모든 회사 또는 조직의 회사 및 학교 계정을 사용합니다.

> [!NOTE]
> 이 가이드에는 Visual Studio 2015 업데이트 3, Visual Studio 2017 또는 Visual Studio 2019가 필요합니다. 이러한 버전이 설치되어 있지 않나요? [체험용 Visual Studio 2019를 다운로드](https://www.visualstudio.com/downloads/)합니다.

>[!NOTE]
> Microsoft ID 플랫폼을 처음 접하는 경우 [Windows 데스크톱 앱에서 토큰 가져오기 및 Microsoft Graph API 호출](quickstart-v2-windows-desktop.md)로 시작하는 것이 좋습니다.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>이 가이드에서 생성된 샘플 앱의 작동 원리

![이 자습서에서 생성된 샘플 앱의 작동 방식 표시](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

이 가이드를 사용하여 만든 샘플 애플리케이션을 사용하면 Windows 데스크톱 애플리케이션이 Microsoft Graph API를 쿼리하거나 웹 API가 Microsoft ID 플랫폼 엔드포인트에서 토큰을 수락할 수 있습니다. 이 시나리오에서는 인증 헤더를 통해 HTTP 요청에 토큰을 추가합니다. MSAL(Microsoft 인증 라이브러리)에서는 토큰 획득 및 갱신을 처리합니다.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>보호되는 웹 API에 액세스하기 위한 토큰 획득 처리

사용자가 인증된 후에 샘플 애플리케이션은 개발자를 위한 Microsoft ID 플랫폼으로 보호되는 Microsoft Graph API 또는 웹 API를 쿼리하는 데 사용할 수 있는 토큰을 수신합니다.

Microsoft Graph와 같은 API에는 특정 리소스에 대한 액세스를 허용하는 토큰이 필요합니다. 예를 들어 사용자 프로필을 읽고, 사용자 일정에 액세스하고, 메일을 보내는 데 토큰이 필요합니다. 애플리케이션에서는 MSAL을 사용하여 액세스 토큰을 요청하고 API 범위를 지정하여 이러한 리소스에 액세스할 수 있습니다. 그런 다음 이 액세스 토큰은 보호되는 리소스에 대한 모든 호출에 사용될 수 있도록 HTTP 인증 헤더에 추가됩니다.

MSAL은 사용자를 대신해 액세스 토큰 캐싱 및 새로 고침을 관리하므로 애플리케이션에서 이러한 작업을 수행할 필요가 없습니다.

## <a name="nuget-packages"></a>NuGet 패키지

이 가이드에서는 다음 NuGet 패키지를 사용합니다.

|라이브러리|Description|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft 인증 라이브러리(MSAL.NET)|

## <a name="set-up-your-project"></a>프로젝트 설정

이 섹션에서는 토큰이 필요한 웹 API를 쿼리할 수 있도록 Windows Desktop .NET 애플리케이션(XAML)을 *Microsoft에 로그인*과 통합하는 방법을 설명하기 위해 새 프로젝트를 만듭니다.

이 가이드를 사용하여 만든 애플리케이션은 그래프를 호출하는 데 사용된 단추, 화면에 결과를 보여주는 영역 및 로그아웃 단추를 표시합니다.

> [!NOTE]
> 이 샘플의 Visual Studio 프로젝트를 다운로드하고 싶으세요? [프로젝트를 다운로드](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip)하면 코드 샘플을 실행하기 전에 구성하는 [구성 단계](#register-your-application)를 건너뛸 수 있습니다.
>

애플리케이션을 만들려면 다음을 수행합니다.

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트**를 선택합니다.
2. **템플릿**에서 **Visual C#** 을 선택합니다.
3. 사용하고 있는 Visual Studio 버전에 따라 **WPF 앱(.NET Framework)** 을 선택합니다.

## <a name="add-msal-to-your-project"></a>프로젝트에 MSAL 추가

1. Visual Studio에서 **도구** > **NuGet 패키지 관리자**> **패키지 관리자 콘솔**을 선택합니다.
2. 패키지 관리자 콘솔 창에서 다음 Azure PowerShell 명령을 붙여넣습니다.

    ```powershell
    Install-Package Microsoft.Identity.Client -Pre
    ```

    > [!NOTE]
    > 이 명령은 Microsoft 인증 라이브러리를 설치합니다. MSAL은 Azure Active Directory v2.0으로 보호된 API에 액세스하는 데 사용되는 사용자 토큰의 획득, 캐싱 및 새로 고침을 처리합니다.
    >

## <a name="register-your-application"></a>애플리케이션 등록

두 가지 방법 중 하나로 애플리케이션을 등록할 수 있습니다.

### <a name="option-1-express-mode"></a>옵션 1: 기본 모드

다음을 수행하여 애플리케이션을 신속하게 등록할 수 있습니다.
1. [Azure Portal - 애플리케이션 등록](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs)으로 이동합니다.
1. 애플리케이션 이름을 입력하고 **등록**을 선택합니다.
1. 지침에 따라 클릭 한 번으로 새 애플리케이션을 다운로드하고 자동으로 구성합니다.

### <a name="option-2-advanced-mode"></a>옵션 2: 고급 모드

애플리케이션을 등록하고 애플리케이션 등록 정보를 솔루션에 추가하려면 다음을 수행합니다.
1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 계정이 둘 이상의 테넌트에 대해 액세스를 제공하는 경우 오른쪽 위 모서리에 있는 계정을 선택하여 원하는 Azure AD 테넌트로 포털 세션을 설정합니다.
1. 개발자용 Microsoft ID 플랫폼 [앱 등록](https://go.microsoft.com/fwlink/?linkid=2083908) 페이지로 이동합니다.
1. **새 등록**을 선택합니다.
   - **이름** 섹션에서 앱의 사용자에게 표시되는 의미 있는 애플리케이션 이름(예: `Win-App-calling-MsGraph`)을 입력합니다.
   - **지원되는 계정 유형** 섹션에서 **모든 조직 디렉터리의 계정 및 개인 Microsoft 계정(예: Skype, Xbox, Outlook.com)** 을 선택합니다.
   - **등록**을 선택하여 애플리케이션을 만듭니다.
1. 앱의 페이지 목록에서 **인증**을 선택합니다.
   1. **리디렉션 URI** 섹션의 리디렉션 URI 목록에서 다음을 수행합니다.
   1. **TYPE** 열에서 **퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)** 를 선택합니다.
   1. **REDIRECT URI** 열에 `https://login.microsoftonline.com/common/oauth2/nativeclient`를 입력합니다.
1. **등록**을 선택합니다.
1. Visual Studio로 이동하여 *App.xaml.cs* 파일을 연 다음 아래 코드 조각의 `Enter_the_Application_Id_here`를 방금 등록하고 복사한 애플리케이션 ID로 바꿉니다.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```

## <a name="add-the-code-to-initialize-msal"></a>MSAL을 초기화하는 코드 추가

이 단계에서는 토큰 처리와 같이 MSAL과 상호 작용을 처리하는 클래스를 만들 수 있습니다.

1. *App.xaml.cs* 파일을 열고 MSAL에 대한 참조를 클래스에 추가합니다.

    ```csharp
    using Microsoft.Identity.Client;
    ```
   <!-- Workaround for Docs conversion bug -->

2. 앱 클래스를 다음으로 업데이트합니다.

    ```csharp
    public partial class App : Application
    {
        static App()
        {
            _clientApp = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
        }

        // Below are the clientId (Application Id) of your app registration and the tenant information.
        // You have to replace:
        // - the content of ClientID with the Application Id for your app registration
        // - the content of Tenant by the information about the accounts allowed to sign-in in your application:
        //   - For Work or School account in your org, use your tenant ID, or domain
        //   - for any Work or School accounts, use `organizations`
        //   - for any Work or School accounts, or Microsoft personal account, use `common`
        //   - for Microsoft Personal account, use consumers
        private static string ClientId = "0b8b0665-bc13-4fdc-bd72-e0227b9fc011";

        private static string Tenant = "common";

        private static IPublicClientApplication _clientApp ;

        public static IPublicClientApplication PublicClientApp { get { return _clientApp; } }
    }
    ```

## <a name="create-the-application-ui"></a>애플리케이션 UI 만들기

이 섹션에서는 애플리케이션에서 Microsoft Graph와 같이 보호되는 백 엔드 서버를 쿼리하는 방법을 보여줍니다.

*MainWindow.xaml* 파일은 프로젝트 템플릿의 일부로 자동으로 생성되어야 합니다. 이 파일을 연 다음, 애플리케이션의 *\<Grid>* 노드를 다음 코드로 바꿉니다.

```xml
<Grid>
    <StackPanel Background="Azure">
        <StackPanel Orientation="Horizontal" HorizontalAlignment="Right">
            <Button x:Name="CallGraphButton" Content="Call Microsoft Graph API" HorizontalAlignment="Right" Padding="5" Click="CallGraphButton_Click" Margin="5" FontFamily="Segoe Ui"/>
            <Button x:Name="SignOutButton" Content="Sign-Out" HorizontalAlignment="Right" Padding="5" Click="SignOutButton_Click" Margin="5" Visibility="Collapsed" FontFamily="Segoe Ui"/>
        </StackPanel>
        <Label Content="API Call Results" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="ResultText" TextWrapping="Wrap" MinHeight="120" Margin="5" FontFamily="Segoe Ui"/>
        <Label Content="Token Info" Margin="0,0,0,-5" FontFamily="Segoe Ui" />
        <TextBox x:Name="TokenInfoText" TextWrapping="Wrap" MinHeight="70" Margin="5" FontFamily="Segoe Ui"/>
    </StackPanel>
</Grid>
```

## <a name="use-msal-to-get-a-token-for-the-microsoft-graph-api"></a>MSAL을 사용하여 Microsoft Graph API에 대한 토큰 가져오기

이 섹션에서는 MSAL을 사용하여 Microsoft Graph API에 대한 토큰을 가져옵니다.

1. *MainWindow.xaml.cs* 파일에서 MSAL에 대한 참조를 클래스에 추가합니다.

    ```csharp
    using Microsoft.Identity.Client;
    ```

2. `MainWindow` 클래스 코드를 다음으로 바꿉니다.

    ```csharp
    public partial class MainWindow : Window
    {
        //Set the API Endpoint to Graph 'me' endpoint
        string graphAPIEndpoint = "https://graph.microsoft.com/v1.0/me";

        //Set the scope for API call to user.read
        string[] scopes = new string[] { "user.read" };


        public MainWindow()
        {
            InitializeComponent();
        }

      /// <summary>
        /// Call AcquireToken - to acquire a token requiring user to sign-in
        /// </summary>
        private async void CallGraphButton_Click(object sender, RoutedEventArgs e)
        {
            AuthenticationResult authResult = null;
            var app = App.PublicClientApp;
            ResultText.Text = string.Empty;
            TokenInfoText.Text = string.Empty;

            var accounts = await app.GetAccountsAsync();
            var firstAccount = accounts.FirstOrDefault();

            try
            {
                authResult = await app.AcquireTokenSilent(scopes, firstAccount)
                    .ExecuteAsync();
            }
            catch (MsalUiRequiredException ex)
            {
                // A MsalUiRequiredException happened on AcquireTokenSilent.
                // This indicates you need to call AcquireTokenInteractive to acquire a token
                System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

                try
                {
                    authResult = await app.AcquireTokenInteractive(scopes)
                        .WithAccount(accounts.FirstOrDefault())
                        .WithPrompt(Prompt.SelectAccount)
                        .ExecuteAsync();
                }
                catch (MsalException msalex)
                {
                    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
                }
            }
            catch (Exception ex)
            {
                ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
                return;
            }

            if (authResult != null)
            {
                ResultText.Text = await GetHttpContentWithToken(graphAPIEndpoint, authResult.AccessToken);
                DisplayBasicTokenInfo(authResult);
                this.SignOutButton.Visibility = Visibility.Visible;
            }
        }
    ```

### <a name="more-information"></a>자세한 정보

#### <a name="get-a-user-token-interactively"></a>대화형으로 사용자 토큰 가져오기

`AcquireTokenInteractive` 메서드를 호출하면 사용자에게 로그인하라는 창이 표시됩니다. 처음으로 보호된 리소스에 액세스할 때 애플리케이션에서는 사용자가 일반적으로 대화형으로 로그인하도록 요청합니다. 토큰을 획득하는 자동 작업에 실패한 경우(예: 사용자의 암호가 만료된 경우) 로그인해야 할 수도 있습니다.

#### <a name="get-a-user-token-silently"></a>자동으로 사용자 토큰 가져오기

`AcquireTokenSilent` 메서드는 사용자 개입 없이 토큰 획득 및 갱신을 자동으로 처리합니다. 요청에 대한 호출 또는 토큰 갱신이 자동으로 수행되기 때문에 `AcquireTokenInteractive`가 처음으로 실행된 후에 `AcquireTokenSilent`는 후속 호출에서 보호되는 리소스에 액세스하는 토큰을 가져오는 데 일반적으로 사용되는 메서드입니다.

결국 `AcquireTokenSilent` 메서드가 실패합니다. 사용자가 로그아웃했거나 다른 디바이스에서 해당 암호를 변경하면 실패할 수 있습니다. MSAL이 대화형 작업을 요구해 이 문제를 해결할 수 있다고 감지하면 `MsalUiRequiredException` 예외를 발생합니다. 애플리케이션에서는 이러한 예외를 다음 두 가지 방법으로 처리할 수 있습니다.

* 즉시 `AcquireTokenInteractive`에 대해 호출할 수 있습니다. 이 호출로 인해 사용자에게 로그인하라는 메시지가 표시됩니다. 이 패턴은 사용자가 사용할 수 있는 오프라인 콘텐츠가 없는 온라인 애플리케이션에서 일반적으로 사용됩니다. 이 단계별 설치에 따라 생성된 샘플은 이 패턴을 사용합니다. 이 패턴은 샘플을 처음 실행할 때 작동되는 항목을 확인할 수 있습니다.

* 이 애플리케이션을 사용한 사용자가 없기 때문에 `PublicClientApp.Users.FirstOrDefault()`에는 null 값이 포함되며 `MsalUiRequiredException` 예외가 throw됩니다.

* 샘플의 코드는 `AcquireTokenInteractive`를 호출하여 예외를 처리합니다. 그러면 사용자에게 로그인하라는 메시지가 표시됩니다.

* 로그인에 적절한 시기를 선택할 수 있도록 대화형 로그인이 필요하다는 시각적 표시를 사용자에게 표시할 수 있습니다. 또는 애플리케이션이 나중에 `AcquireTokenSilent`를 다시 시도할 수 있습니다. 이 패턴은 사용자가 중지하지 않고 다른 애플리케이션 기능을 사용할 수 있는 경우에 자주 사용됩니다(예: 오프라인 콘텐츠를 애플리케이션에서 사용할 수 있는 경우). 이 경우에 사용자는 보호된 리소스에 액세스하거나, 오래된 정보를 새로 고치기 위해 로그인하는 시점을 결정할 수 있습니다. 또는 네트워크가 일시적으로 사용할 수 없게 된 후에 복원된 경우 애플리케이션이 `AcquireTokenSilent`를 다시 시도하도록 결정할 수 있습니다.

## <a name="call-the-microsoft-graph-api-by-using-the-token-you-just-obtained"></a>방금 가져온 토큰을 사용하여 Microsoft Graph API를 호출합니다.

`MainWindow.xaml.cs`에 다음과 같은 새 메서드를 추가합니다. 이 메서드는 인증 헤더를 사용하여 Graph API에 대한 `GET` 요청을 수행하는 데 사용됩니다.

```csharp
/// <summary>
/// Perform an HTTP GET request to a URL using an HTTP Authorization header
/// </summary>
/// <param name="url">The URL</param>
/// <param name="token">The token</param>
/// <returns>String containing the results of the GET operation</returns>
public async Task<string> GetHttpContentWithToken(string url, string token)
{
    var httpClient = new System.Net.Http.HttpClient();
    System.Net.Http.HttpResponseMessage response;
    try
    {
        var request = new System.Net.Http.HttpRequestMessage(System.Net.Http.HttpMethod.Get, url);
        //Add the token in Authorization header
        request.Headers.Authorization = new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        response = await httpClient.SendAsync(request);
        var content = await response.Content.ReadAsStringAsync();
        return content;
    }
    catch (Exception ex)
    {
        return ex.ToString();
    }
}
```

### <a name="more-information-about-making-a-rest-call-against-a-protected-api"></a>보호되는 API에 대한 REST 호출에 관한 추가 정보

이 애플리케이션 예제에서는 토큰이 필요한 보호되는 리소스에 대한 HTTP `GET` 요청을 실행한 다음, 호출자에게 콘텐츠를 반환하는 데 `GetHttpContentWithToken` 메서드를 사용합니다. 이 메서드는 HTTP 인증 헤더에 획득된 토큰을 추가합니다. 이 샘플에서 리소스는 사용자 프로필 정보를 표시하는 Microsoft Graph API *me* 엔드포인트입니다.

## <a name="add-a-method-to-sign-out-a-user"></a>사용자를 로그아웃하는 메서드 추가

사용자를 로그아웃하려면 `MainWindow.xaml.cs` 파일에 다음 메서드를 추가합니다.

```csharp
/// <summary>
/// Sign out the current user
/// </summary>
private async void SignOutButton_Click(object sender, RoutedEventArgs e)
{
    var accounts = await App.PublicClientApp.GetAccountsAsync();

    if (accounts.Any())
    {
        try
        {
            await App.PublicClientApp.RemoveAsync(accounts.FirstOrDefault());
            this.ResultText.Text = "User has signed-out";
            this.CallGraphButton.Visibility = Visibility.Visible;
            this.SignOutButton.Visibility = Visibility.Collapsed;
        }
        catch (MsalException ex)
        {
            ResultText.Text = $"Error signing-out user: {ex.Message}";
        }
    }
}
```

### <a name="more-information-about-user-sign-out"></a>사용자 로그아웃에 대한 자세한 내용

`SignOutButton_Click` 메서드는 MSAL 사용자 캐시에서 사용자를 제거합니다. 그러면 대화식으로 수행되는 경우에만 토큰을 획득하는 이후 요청에 성공하도록 MSAL에서 현재 사용자를 효율적으로 삭제하게 됩니다.

이 샘플의 애플리케이션이 단일 사용자를 지원하더라도 MSAL은 동시에 여러 계정에 로그인할 수 있는 시나리오를 지원합니다. 사용자 한 명이 여러 계정을 가질 수 있는 전자 메일 애플리케이션을 예로 듭니다.

## <a name="display-basic-token-information"></a>기본 토큰 정보 표시

토큰에 대한 기본 정보를 표시하려면 *MainWindow.xaml.cs* 파일에 다음 메서드를 추가합니다.

```csharp
/// <summary>
/// Display basic information contained in the token
/// </summary>
private void DisplayBasicTokenInfo(AuthenticationResult authResult)
{
    TokenInfoText.Text = "";
    if (authResult != null)
    {
        TokenInfoText.Text += $"Username: {authResult.Account.Username}" + Environment.NewLine;
        TokenInfoText.Text += $"Token Expires: {authResult.ExpiresOn.ToLocalTime()}" + Environment.NewLine;
    }
}
```

### <a name="more-information"></a>자세한 정보

또한 MSAL에서는 사용자가 로그인한 후에 Microsoft Graph API를 호출하는 데 사용되는 액세스 토큰을 외에도 ID 토큰을 가져옵니다. 이 토큰에는 사용자에게 관련된 정보의 작은 하위 집합이 포함됩니다. `DisplayBasicTokenInfo` 메서드는 토큰에 포함된 기본 정보를 표시합니다. 예를 들어, 토큰 만료 날짜 및 액세스 토큰 자체를 나타내는 문자열뿐만 아니라 사용자 표시 이름 및 ID을 표시합니다. *Microsoft Graph API 호출* (Call Microsoft Graph API) 단추를 여러 번 선택하면 동일한 토큰이 후속 요청에 다시 사용된 것을 볼 수 있습니다. MSAL이 토큰 갱신 시점이라고 판단한 경우에는 만료 날짜가 연장된 것도 확인할 수 있습니다.

[!INCLUDE [5. Test and Validate](../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
