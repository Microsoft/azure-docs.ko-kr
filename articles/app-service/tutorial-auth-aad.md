---
title: '자습서: 사용자 인증 E2E'
description: App Service 인증 및 권한 부여를 사용하여 원격 API에 대한 액세스를 비롯한 App Service 앱을 엔드투엔드로 보호하는 방법을 알아봅니다.
keywords: App Service, Azure App Service, authN, authZ, 보호, 보안, 다중 계층, Azure Active Directory, Azure AD
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/29/2020
ms.custom: devx-track-csharp, seodec18
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: c1c3f52dafe63e3f829eb12d4fb872ed3ce85f36
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88211717"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>자습서: Azure App Service에서 엔드투엔드 사용자 인증 및 권한 부여

::: zone pivot="platform-windows"  

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 또한, App Service는 [사용자 인증 및 권한 부여](overview-authentication-authorization.md)를 기본적으로 지원합니다. 이 자습서에는 App Service 인증 및 권한 부여를 통해 앱을 보호하는 방법을 보여줍니다. 예를 들어 Angular.js 프런트 엔드가 있는 ASP.NET Core 앱을 사용합니다. App Service 인증 및 권한 부여는 모든 언어 런타임을 지원하며 자습서를 수행하면서 원하는 언어에 적용하는 방법을 알아볼 수 있습니다.

::: zone-end

::: zone pivot="platform-linux"

[Azure App Service](overview.md)는 Linux 운영 체제를 사용하여 확장성이 뛰어난 자체 패치 웹 호스팅 서비스를 제공합니다. 또한, App Service는 [사용자 인증 및 권한 부여](overview-authentication-authorization.md)를 기본적으로 지원합니다. 이 자습서에는 App Service 인증 및 권한 부여를 통해 앱을 보호하는 방법을 보여줍니다. 예를 들어 Angular.js 프런트 엔드가 있는 ASP.NET Core 앱을 사용합니다. App Service 인증 및 권한 부여는 모든 언어 런타임을 지원하며 자습서를 수행하면서 원하는 언어에 적용하는 방법을 알아볼 수 있습니다.

::: zone-end

![간단한 인증 및 권한 부여](./media/tutorial-auth-aad/simple-auth.png)

또한 [서버 코드](#call-api-securely-from-server-code)와 [브라우저 코드](#call-api-securely-from-browser-code) 모두에서 인증된 사용자 대신 보안 백 엔드 API에 액세스하여 다중 계층 앱의 보안을 유지하는 방법을 보여줍니다.

![고급 인증 및 권한 부여](./media/tutorial-auth-aad/advanced-auth.png)

이것은 App Service에서 가능한 인증 및 권한 부여 시나리오 중 일부일 뿐입니다. 

다음은 자습서에서 알아볼 수 있는 포괄적인 사항의 목록입니다.

> [!div class="checklist"]
> * 기본 제공되는 인증 및 권한 부여를 사용하도록 설정
> * 인증되지 않은 요청에 대해 앱 보호
> * Azure Active Directory를 ID 공급자로 사용
> * 로그인한 사용자 대신 원격 앱에 액세스
> * 토큰 인증으로 서비스 간 호출 보안 유지
> * 서버 코드에서 액세스 토큰 사용
> * 클라이언트(브라우저) 코드에서 액세스 토큰 사용

이 자습서의 단계는 macOS, Linux, Windows에서 수행할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

* <a href="https://git-scm.com/" target="_blank">Git 설치</a>
* <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">최신 .NET Core 3.1 SDK 설치</a>

## <a name="create-local-net-core-app"></a>로컬 .NET Core 앱 만들기

이 단계에서는 로컬 .NET Core 프로젝트를 설정합니다. 동일한 프로젝트를 사용하여 백 엔드 API 앱과 프런트 엔드 웹앱을 배포합니다.

### <a name="clone-and-run-the-sample-application"></a>애플리케이션 예제를 복제하고 실행합니다.

다음 명령을 실행하여 샘플 리포지토리를 복제하고 실행합니다.

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

`http://localhost:5000`으로 이동하여 todo 항목을 추가, 수정 및 제거해 봅니다. 

![로컬로 실행되는 ASP.NET Core API](./media/tutorial-auth-aad/local-run.png)

언제든지 ASP.NET Core를 중지하려면 터미널에서 `Ctrl+C`를 누릅니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>Azure에 앱 배포

이 단계에서는 두 개의 App Service 앱에 프로젝트를 배포합니다. 하나는 프런트 엔드 앱이고 다른 하나는 백 엔드 앱입니다.

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Azure 리소스 만들기

::: zone pivot="platform-windows"  

Cloud Shell에서 다음 명령을 실행하여 두 개의 Windows 웹앱을 만듭니다. _\<front-end-app-name>_ 및 _\<back-end-app-name>_ 를 전역적으로 고유한 두 개의 앱 이름(유효한 문자는 `a-z`, `0-9` 및 `-`)으로 바꿉니다. 각 명령에 대한 자세한 내용은 [Azure App Service에서 CORS를 사용한 RESTful API](app-service-web-tutorial-rest-api.md)를 참조하세요.

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

::: zone pivot="platform-linux"

Cloud Shell에서 다음 명령을 실행하여 웹앱 두 개를 만듭니다. _\<front-end-app-name>_ 및 _\<back-end-app-name>_ 를 전역적으로 고유한 두 개의 앱 이름(유효한 문자는 `a-z`, `0-9` 및 `-`)으로 바꿉니다. 각 명령에 대한 자세한 내용은 [Azure App Service에서 .NET Core 앱 만들기](quickstart-dotnetcore.md)를 참조하세요.

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE --is-linux
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --runtime "DOTNETCORE|3.1" --deployment-local-git --query deploymentLocalGitUrl
```

::: zone-end

> [!NOTE]
> 프런트 엔드 앱과 백 엔드 앱에 대한 Git 원격 URL을 저장합니다. 이것은 `az webapp create`의 출력에 표시됩니다.
>

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

로컬 터미널 창으로 돌아가서 다음 Git 명령을 실행하여 백 엔드 앱에 배포합니다. _\<deploymentLocalGitUrl-of-back-end-app>_ 를 [Azure 리소스 만들기](#create-azure-resources)에 저장된 Git 원격의 URL로 바꿉니다. Git Credential Manager에서 자격 증명을 입력하라는 메시지가 표시되면 Azure Portal에 로그인하는 데 사용하는 자격 증명이 아닌 [배포 자격 증명](deploy-configure-credentials.md)을 입력해야 합니다.

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

로컬 터미널 창에서 다음과 같은 Git 명령을 실행하여 동일한 코드를 프런트 엔드 앱에 배포합니다. _\<deploymentLocalGitUrl-of-front-end-app>_ 를 [Azure 리소스 만들기](#create-azure-resources)에 저장된 Git 원격의 URL로 바꿉니다.

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>앱으로 이동

브라우저에서 다음 URL로 이동하여 두 개의 앱이 작동하는지 확인합니다.

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

![Azure App Service에서 실행되는 ASP.NET Core API](./media/tutorial-auth-aad/azure-run.png)

> [!NOTE]
> 앱이 다시 시작되면 새로운 데이터가 삭제되었음을 알 수 있습니다. 샘플 ASP.NET Core 앱은 메모리 내 데이터베이스를 사용하기 때문에 이 동작은 의도적입니다.
>
>

## <a name="call-back-end-api-from-front-end"></a>프런트 엔드에서 백 엔드 API 호출

이 단계에서는 프런트 엔드 앱의 서버 코드가 백엔드 API에 액세스하도록 가리킵니다. 나중에 프런트 엔드에서 백 엔드로 인증된 액세스를 사용하도록 설정합니다.

### <a name="modify-front-end-code"></a>프런트 엔드 코드 수정

로컬 리포지토리에서 _Controllers/TodoController.cs_ 를 엽니다. `TodoController` 클래스의 시작 부분에 다음 줄을 추가하고 _\<back-end-app-name>_ 을 백 엔드 앱의 이름으로 바꿉니다.

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

`[HttpGet]`을 사용하여 데코레이팅된 메서드를 찾아서 중괄호 안의 코드를 다음과 같이 바꿉니다.

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo");
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

첫 번째 줄은 백 엔드 API 앱에 `GET /api/Todo` 호출을 수행합니다.

다음으로, `[HttpGet("{id}")]`을 사용하여 데코레이팅된 메서드를 찾아서 중괄호 안의 코드를 다음과 같이 바꿉니다.

```cs
var data = await _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}");
return Content(data, "application/json");
```

첫 번째 줄은 백 엔드 API 앱에 `GET /api/Todo/{id}` 호출을 수행합니다.

다음으로, `[HttpPost]`을 사용하여 데코레이팅된 메서드를 찾아서 중괄호 안의 코드를 다음과 같이 바꿉니다.

```cs
var response = await _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", todoItem);
var data = await response.Content.ReadAsStringAsync();
return Content(data, "application/json");
```

첫 번째 줄은 백 엔드 API 앱에 `POST /api/Todo` 호출을 수행합니다.

다음으로, `[HttpPut("{id}")]`을 사용하여 데코레이팅된 메서드를 찾아서 중괄호 안의 코드를 다음과 같이 바꿉니다.

```cs
var res = await _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", todoItem);
return new NoContentResult();
```

첫 번째 줄은 백 엔드 API 앱에 `PUT /api/Todo/{id}` 호출을 수행합니다.

다음으로, `[HttpDelete("{id}")]`을 사용하여 데코레이팅된 메서드를 찾아서 중괄호 안의 코드를 다음과 같이 바꿉니다.

```cs
var res = await _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}");
return new NoContentResult();
```

첫 번째 줄은 백 엔드 API 앱에 `DELETE /api/Todo/{id}` 호출을 수행합니다.

변경 내용을 모두 저장합니다. 로컬 터미널 창에서 다음 Git 명령을 사용하여 변경 내용을 프런트 엔드 앱에 배포합니다.

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>변경 내용 확인

`http://<front-end-app-name>.azurewebsites.net`으로 이동하여 `from front end 1` 및 `from front end 2` 등의 몇 가지 항목을 추가합니다.

`http://<back-end-app-name>.azurewebsites.net`으로 이동하여 프런트 엔드 앱에서 추가된 항목을 확인합니다. `from back end 1` 및 `from back end 2` 등과 같은 몇 가지 항목을 추가한 다음 프런트 엔드 앱을 새로 고쳐서 변경 내용이 반영되는지 확인합니다.

![Azure App Service에서 실행되는 ASP.NET Core API](./media/tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>인증 구성

이 단계에서는 두 앱에 인증 및 권한 부여를 사용하도록 설정합니다. 또한 백 엔드 앱에 인증된 호출을 수행하는 데 사용할 수 있는 액세스 토큰을 생성하도록 프런트 엔드 앱을 구성합니다.

Azure Active Directory를 ID 공급자로 사용합니다. 자세한 내용은 [App Services 애플리케이션에 대해 Azure Active Directory 인증 구성](configure-authentication-provider-aad.md)을 참조하세요.

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>백 엔드 앱에 대한 인증 및 권한 부여 사용

*Azure Portal* 메뉴에서 [리소스 그룹](https://portal.azure.com)을 선택하거나 검색하여 어느 페이지에서든 **리소스 그룹**을 선택합니다.

**리소스 그룹**에서 리소스 그룹을 찾아 선택합니다. **개요**에서 백 엔드 앱의 관리 페이지를 선택합니다.

![Azure App Service에서 실행되는 ASP.NET Core API](./media/tutorial-auth-aad/portal-navigate-back-end.png)

백 엔드 앱의 왼쪽 메뉴에서 **인증/권한 부여**을 선택하고 **설정**을 선택하여 App Service 인증을 사용하도록 설정합니다.

**요청이 인증되지 않은 경우 수행할 작업**에서 **Azure Active Directory로 로그인**을 선택합니다.

**인증 공급자**에서 **Azure Active Directory**를 선택합니다.

![Azure App Service에서 실행되는 ASP.NET Core API](./media/tutorial-auth-aad/configure-auth-back-end.png)

**기본**을 선택하고 기본 설정을 그대로 사용하여 새 AD 앱을 만든 후 **확인**을 선택합니다.

**인증/권한 부여** 페이지에서 **저장**을 선택합니다.

`Successfully saved the Auth Settings for <back-end-app-name> App` 메시지가 포함된 알림이 표시되면 포털 페이지를 새로 고칩니다.

**Azure Active Directory**를 다시 선택한 다음, **Azure AD 앱**을 선택합니다.

Azure AD 애플리케이션의 **클라이언트 ID**를 메모장에 복사합니다. 이 값은 나중에 필요합니다.

![Azure App Service에서 실행되는 ASP.NET Core API](./media/tutorial-auth-aad/get-application-id-back-end.png)

여기에서 중지하는 경우 App Service 인증 및 권한 부여로 이미 보호되는 자체 포함된 앱이 있습니다. 나머지 섹션에서는 프런트 엔드에서 백 엔드로 인증된 사용자를 "전달"하여 다중 앱 솔루션의 보안을 유지하는 방법을 보여 줍니다. 

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>프런트 엔드 앱에 대한 인증 및 권한 부여 사용

프런트 엔드 앱과 동일한 단계를 따르지만 마지막 단계는 건너뜁니다. 프런트 엔드 앱에는 클라이언트 ID가 필요하지 않습니다.

원하는 경우 `http://<front-end-app-name>.azurewebsites.net`으로 이동합니다. 이제 보안 로그인 페이지로 연결됩니다. 로그인 한 후에도 *백 엔드 앱에서 데이터에 액세스할 수 없습니다*. 백 엔드 앱에는 현재 프런트 엔드 앱의 Azure Active Directory 로그인이 필요하기 때문입니다. 다음과 같은 세 가지 작업을 수행해야 합니다.

- 프런트 엔드에 백 엔드 액세스 권한 부여
- 사용 가능한 토큰을 반환하도록 App Service 구성
- 코드의 토큰 사용

> [!TIP]
> 오류가 발생하여 앱의 인증/권한 부여 설정을 다시 구성하면 토큰 저장소의 토큰이 새 설정에서 다시 생성되지 않을 수 있습니다. 토큰이 다시 생성되도록 하려면 앱에서 로그아웃하고 다시 로그인해야 합니다. 이 작업을 수행하는 쉬운 방법은 브라우저를 개인 모드로 사용하여 앱의 설정을 변경한 후 개인 모드로 브라우저를 닫았다가 다시 여는 것입니다.

### <a name="grant-front-end-app-access-to-back-end"></a>백 엔드에 프런트 엔드 앱 액세스 부여

두 앱에 대해 인증 및 권한 부여를 사용하도록 설정했으므로 각 앱은 AD 애플리케이션으로 지원됩니다. 이 단계에서는 프런트 엔드 앱에 사용자 대신 백 엔드 액세스 권한을 부여합니다. (기술적으로 프런트 엔드의 _AD 애플리케이션_ 에 사용자를 대신하여 백 엔드의 _AD 애플리케이션_ 에 액세스할 수 있는 권한을 부여합니다.)

[Azure Portal](https://portal.azure.com) 메뉴에서 **Azure Active Directory**를 선택하거나 아무 페이지에서 *Azure Active Directory*를 검색한 후 선택합니다.

**앱 등록** > **소유 애플리케이션** > **이 디렉터리의 모든 애플리케이션 보기**를 선택합니다. 프런트 엔드 앱 이름을 선택하고 **API 사용 권한**을 선택합니다.

![Azure App Service에서 실행되는 ASP.NET Core API](./media/tutorial-auth-aad/add-api-access-front-end.png)

**권한 추가**를 선택한 다음, **내 조직에서 사용하는 API** >  **\<back-end-app-name>** 를 선택합니다.

백 엔드 앱에 대한 **API 권한 요청** 페이지에서 **위임된 권한** 및 **user_impersonation**를 선택한 다음, **권한 추가**를 선택합니다.

![Azure App Service에서 실행되는 ASP.NET Core API](./media/tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>사용 가능한 액세스 토큰을 반환하도록 App Service 구성

이제 프런트 엔드 앱에는 로그인한 사용자로 백 엔드 앱에 액세스하는 데 필요한 권한이 있습니다. 이 단계에서는 백 엔드 액세스에 사용 가능한 액세스 토큰을 제공하도록 App Service 인증 및 권한 부여를 구성합니다. 이 단계에서는 [백 엔드 앱에 대한 인증 및 권한 부여 사용](#enable-authentication-and-authorization-for-back-end-app)에서 복사한 백 엔드의 클라이언트 ID가 필요합니다.

[Azure Resource Explorer](https://resources.azure.com)로 이동하고 리소스 트리를 사용하여 프런트 엔드 웹앱을 찾습니다.

이제 [Azure Resource Explorer](https://resources.azure.com)가 리소스 트리에서 선택된 프런트 엔드 앱과 함께 열립니다. 페이지의 위쪽에서 **읽기/쓰기** 를 클릭하여 Azure 리소스 편집이 가능하도록 설정합니다.

![Azure App Service에서 실행되는 ASP.NET Core API](./media/tutorial-auth-aad/resources-enable-write.png)

왼쪽 브라우저에서 **구성** > **authsettings**로 드릴다운합니다.

**authsettings** 보기에서 **편집**을 클릭합니다. 복사한 클라이언트 ID를 사용하여 `additionalLoginParams`를 다음 JSON 문자열로 설정합니다. 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

![Azure App Service에서 실행되는 ASP.NET Core API](./media/tutorial-auth-aad/additional-login-params-front-end.png)

**PUT**을 클릭하여 설정을 저장합니다.

이제 앱이 구성되었습니다. 이제 프런트 엔드가 적절한 액세스 토큰을 사용하여 백 엔드에 액세스할 준비가 되었습니다.

다른 공급자에 대한 액세스 토큰을 구성하는 방법에 대한 자세한 내용은 [ID 공급자 토큰 새로 고침](app-service-authentication-how-to.md#refresh-identity-provider-tokens)을 참조하세요.

## <a name="call-api-securely-from-server-code"></a>서버 코드에서 안전하게 API 호출

이 단계에서는 이전에 수정한 서버 코드를 사용하여 백 엔드 API에 대해 인증된 호출을 수행합니다.

이제 프런트 엔드 앱에 필요한 권한이 있고 백 엔드의 클라이언트 ID가 로그인 매개 변수에 추가됩니다. 따라서 백 엔드 앱 인증을 위한 액세스 토큰을 가져올 수 있습니다. App Service는 인증된 요청마다 `X-MS-TOKEN-AAD-ACCESS-TOKEN` 헤더를 삽입하여([앱 코드에서 토큰 검색](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) 참조) 이 토큰을 서버 코드에 제공합니다.

> [!NOTE]
> 이 헤더는 지원되는 모든 언어로 삽입됩니다. 각 해당 언어에 대한 표준 패턴을 사용하여 액세스할 수 있습니다.

로컬 리포지토리에서 _Controllers/TodoController.cs_를 다시 엽니다. `TodoController(TodoContext context)` 생성자 아래에 다음 코드를 추가합니다.

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

이 코드는 표준 HTTP 헤더 `Authorization: Bearer <access-token>`를 모든 원격 API 호출에 추가합니다. ASP.NET Core MVC 요청 실행 파이프라인에서 `OnActionExecuting`은 각 작업이 실행되기 직전에 실행되므로, 이제 나가는 API 호출 각각에 액세스 토큰이 표시됩니다.

변경 내용을 모두 저장합니다. 로컬 터미널 창에서 다음 Git 명령을 사용하여 변경 내용을 프런트 엔드 앱에 배포합니다.

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

`https://<front-end-app-name>.azurewebsites.net`에 다시 로그인합니다. 사용자 데이터 사용 규약 페이지에서 **동의**를 클릭합니다.

이제 이전과 같이 백 엔드 앱에서 데이터를 만들고, 읽고, 업데이트하고 삭제할 수 있습니다. 유일한 차이점은 이제 두 앱이 App Service 인증 및 권한 부여로 보호된다는 점입니다(서비스 간 호출 포함).

축하합니다! 이제 서버 코드가 인증된 사용자 대신 백 엔드 데이터에 액세스할 수 있습니다.

## <a name="call-api-securely-from-browser-code"></a>브라우저 코드에서 안전하게 API 호출

이 단계에서는 프런트 엔드 Angular.js 앱을 백 엔드 API로 가리킵니다. 이러한 방식으로 액세스 토큰을 검색하고 이를 사용하여 백 엔드 앱에 API 호출을 수행하는 방법을 알아봅니다.

서버 코드가 요청 헤더에 액세스할 수 있지만, 클라이언트 코드는 `GET /.auth/me`에 액세스하여 동일한 액세스 토큰을 얻을 수 있습니다([앱 코드에서 토큰 검색](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) 참조).

> [!TIP]
> 이 섹션에서는 표준 HTTP 메서드를 사용하여 보안 HTTP 호출을 보여줍니다. 그러나 [JavaScript용 Microsoft 인증 라이브러리](https://github.com/AzureAD/microsoft-authentication-library-for-js)를 사용하여 Angular.js 애플리케이션 패턴을 간소화할 수 있습니다.
>

### <a name="configure-cors"></a>CORS 구성

Cloud Shell에서 [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) 명령을 사용하여 CORS를 클라이언트 URL로 사용하도록 설정합니다. _\<back-end-app-name>_ 및 _\<front-end-app-name>_ 자리 표시자를 바꿉니다.

```azurecli-interactive
az webapp cors add --resource-group myAuthResourceGroup --name <back-end-app-name> --allowed-origins 'https://<front-end-app-name>.azurewebsites.net'
```

이 단계는 인증 및 권한 부여와 관련이 없습니다. 하지만 브라우저에서 Angular.js 앱의 도메인 간 API 호출을 허용하려면 필요합니다. 자세한 내용은 [CORS 기능 추가](app-service-web-tutorial-rest-api.md#add-cors-functionality)를 참조하세요.

### <a name="point-angularjs-app-to-back-end-api"></a>Angular.js 앱에서 백 엔드 API 가리키기

로컬 리포지토리에서 _wwwroot/index.html_을 엽니다.

51번 줄에서 `apiEndpoint` 변수를 백 엔드 앱의 HTTPS URL(`https://<back-end-app-name>.azurewebsites.net`)로 설정합니다. _\<back-end-app-name>_ 를 App Service의 앱 이름으로 바꿉니다.

로컬 리포지토리에서 _wwwroot/app/scripts/todoListSvc.js_를 열고 `apiEndpoint`가 모든 API 호출 앞에 추가되었는지 확인합니다. 이제 Angular.js 앱에서 백 엔드 API를 호출합니다. 

### <a name="add-access-token-to-api-calls"></a>API 호출에 액세스 토큰 추가

_wwwroot/app/scripts/todoListSvc.js_의 API 호출 목록 위(`getItems : function(){` 줄 위)에 있는 목록에 다음 함수를 추가합니다.

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

이 함수는 액세스 토큰을 사용하여 기본 `Authorization` 헤더를 설정하기 위해 호출됩니다. 다음 단계에서 호출합니다.

로컬 리포지토리에서 _wwwroot/app/scripts/app.js_를 열어서 다음 코드를 찾습니다.

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

전체 코드 블록을 다음 코드로 바꿉니다.

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

새로운 변경 내용은 `/.auth/me`를 호출하고 액세스 토큰을 설정하는 `resolve` 매핑을 추가합니다. 이것은 `todoListCtrl` 컨트롤러를 인스턴스화하기 전에 액세스 토큰을 갖도록 합니다. 이런 방식으로 컨트롤러의 모든 API 호출에는 토큰이 포함됩니다.

### <a name="deploy-updates-and-test"></a>업데이트 배포 및 테스트

변경 내용을 모두 저장합니다. 로컬 터미널 창에서 다음 Git 명령을 사용하여 변경 내용을 프런트 엔드 앱에 배포합니다.

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

`https://<front-end-app-name>.azurewebsites.net`으로 다시 이동합니다. 이제 Angular.js 앱에서 직접 백 엔드 앱에서 데이터를 만들고, 읽고, 업데이트하고 삭제할 수 있습니다.

축하합니다! 이제 클라이언트 코드가 인증된 사용자 대신 백 엔드 데이터에 액세스할 수 있습니다.

## <a name="when-access-tokens-expire"></a>액세스 토큰이 만료되는 시기

액세스 토큰은 일정 시간 후에 만료됩니다. 사용자에게 앱 재인증을 요구하지 않고 액세스 토큰을 새로 고치는 방법은 [ID 공급자 토큰 새로 고침](app-service-authentication-how-to.md#refresh-identity-provider-tokens)을 참조하세요.

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것 같으면 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

이 명령을 실행하는 데 1분 정도 걸릴 수 있습니다.

<a name="next"></a>
## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 기본 제공되는 인증 및 권한 부여를 사용하도록 설정
> * 인증되지 않은 요청에 대해 앱 보호
> * Azure Active Directory를 ID 공급자로 사용
> * 로그인한 사용자 대신 원격 앱에 액세스
> * 토큰 인증으로 서비스 간 호출 보안 유지
> * 서버 코드에서 액세스 토큰 사용
> * 클라이언트(브라우저) 코드에서 액세스 토큰 사용

다음 자습서로 이동하여 사용자 지정 DNS 이름을 앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure App Service에 기존 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)
