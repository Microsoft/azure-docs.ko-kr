---
title: 자습서 - .NET에서 Azure 웹앱을 통해 Azure Key Vault 사용 | Microsoft Docs
description: 이 자습서에서는 Key Vault에서 비밀을 읽도록 ASP.NET Core 애플리케이션을 구성합니다.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp
ms.openlocfilehash: be18718513e8624db00d502228a3e5af6076d9d7
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89007080"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>자습서: 관리 ID를 사용하여 .NET에서 Key Vault를 Azure Web App에 연결

Azure Key Vault를 사용하면 자격 증명과 기타 비밀을 안전하게 저장할 수 있습니다. 하지만 이러한 자격 증명/키/비밀을 검색하려면 코드가 Key Vault에 인증해야 합니다. [Azure 리소스에 대한 관리 ID 개요](../../active-directory/managed-identities-azure-resources/overview.md)를 통해 Azure AD에서 자동으로 관리되는 ID를 Azure 서비스에 제공함으로써 이 문제를 해결할 수 있습니다. 이 ID를 사용하면 Key Vault를 비롯하여 Azure AD 인증을 지원하는 모든 서비스에 인증할 수 있으므로 코드에 자격 증명을 표시할 필요가 없습니다.

이 자습서에서는 관리 ID를 사용하여 Azure Key Vault에서 Azure Web App을 인증합니다. 이 단계에서는 [.NET](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) 및 [Azure CLI](/cli/azure/get-started-with-azure-cli)용 Azure Key Vault v4 클라이언트 라이브러리를 사용하지만, 선택한 개발 언어 Azure PowerShell 및/또는 Azure Portal을 사용하는 경우에도 동일한 기본 원칙이 적용됩니다.

## <a name="prerequisites"></a>필수 구성 요소

이 빠른 시작을 완료하려면 다음이 필요합니다.

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [.NET Core 3.1 SDK 이상](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) 또는 [Azure PowerShell](/powershell/azure/)

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create) 명령을 사용하여 키 자격 증명 모음과 웹앱을 모두 저장할 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

## <a name="set-up-your-key-vault"></a>키 자격 증명 모음 설정

이제 이 자습서의 뒷부분에서 사용할 수 있도록 키 자격 증명 모음을 만들어 비밀을 배치합니다.

키 자격 증명 모음을 만들려면 [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) 명령을 사용합니다.

> [!Important]
> 각 Key Vault마다 고유한 이름이 있어야 합니다. 다음 예제에서 <your-keyvault-name>을 키 자격 증명 모음의 이름으로 바꿉니다.

```azurecli-interactive
az keyvault create --name "<your-keyvault-name>" -g "myResourceGroup"
```

"https://&lt;your-keyvault-name&gt;.vault.azure.net/" 형식으로 반환된 `vaultUri`를 적어 둡니다. [코드 업데이트](#update-the-code) 단계에서 사용됩니다.

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-net-web-app"></a>.NET 웹앱 만들기

### <a name="create-a-local-app"></a>로컬 앱 만들기

컴퓨터의 터미널 창에서 `akvwebapp`라는 디렉터리를 만들고 현재 디렉터리를 이 디렉터리로 변경합니다.

```bash
mkdir akvwebapp
cd akvwebapp
```

이제 [dotnet new web](/dotnet/core/tools/dotnet-new) 명령을 사용하여 새 .NET Core 앱을 만듭니다.

```bash
dotnet new web
```

애플리케이션을 로컬로 실행하여 Azure에 애플리케이션을 배포할 때 표시되는 모양을 확인합니다. 

```bash
dotnet run
```

웹 브라우저를 열고 `http://localhost:5000`의 앱으로 이동합니다.

표시된 샘플 앱의 **Hello World** 메시지가 페이지에 표시됩니다.

### <a name="initialize-the-git-repository"></a>Git 리포지토리 초기화

터미널 창에서 **Ctrl+C**를 눌러 웹 서버를 종료합니다.  .NET Core 프로젝트에 대해 Git 리포지토리를 초기화합니다.

```bash
git init
git add .
git commit -m "first commit"
```

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

FTP 및 로컬 Git는 *배포 사용자*를 통해 Azure 웹앱에 배포할 수 있습니다. 일단 배포 사용자를 구성하면 모든 Azure 배포에 사용할 수 있습니다. 계정 수준 배포 사용자 이름 및 암호는 Azure 구독 자격 증명과 다릅니다. 

배포 사용자를 구성하려면 [az webapp deployment user set](/cli/azure/webapp/deployment/user?view=azure-cli-latest#az-webapp-deployment-user-set) 명령을 실행합니다. 다음 지침을 준수하는 사용자 이름과 암호를 선택합니다. 

- 사용자 이름은 Azure 내에서 고유해야 하고, 로컬 Git 푸시의경우 ' @' 기호를 포함하면 안 됩니다. 
- 암호는 글자, 숫자, 기호의 세 가지 요소 중 두 가지를 사용하고 8자 이상이어야 합니다. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

JSON 출력에는 암호가 `null`로 나옵니다. `'Conflict'. Details: 409` 오류가 발생하면 사용자 이름을 변경합니다. `'Bad Request'. Details: 400` 오류가 발생하면 더 강력한 암호를 사용합니다. 

웹앱을 배포할 때 사용할 수 있도록 사용자 이름 및 암호를 기록해 둡니다.

### <a name="create-an-app-service-plan"></a>App Service 계획 만들기

Azure CLI [az appservice plan create](/cli/azure/appservice/plan?view=azure-cli-latest) 명령을 사용하여 App Service 계획을 만듭니다. 다음 예제에서는 **Free(체험)** 가격 책정 계층에서 `myAppServicePlan`이라는 App Service 계획을 만듭니다.

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

App Service 계획을 만든 경우 Azure CLI는 다음 예제와 비슷한 정보를 표시합니다.

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>


### <a name="create-a-remote-web-app"></a>원격 웹앱 만들기

`myAppServicePlan` App Service 계획에서 [Azure 웹앱](../../app-service/overview.md#app-service-on-linux)을 만듭니다. 

> [!Important]
> Key Vault와 마찬가지로 Azure Web App의 이름도 고유해야 합니다. 다음 예제에서 \<your-webapp-name\>을 웹앱 이름으로 바꿉니다.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

웹앱이 만들어지면 Azure CLI에서 다음 예제와 비슷한 출력을 표시합니다.

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>


Git 원격의 URL은 `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` 형식으로 `deploymentLocalGitUrl` 속성에 표시됩니다. 나중에 필요하므로 이 URL을 저장합니다.

새로 만든 앱으로 이동합니다. _&lt;your-webapp-name>_ 을 앱 이름으로 바꿉니다.

```bash
https://<your-webapp-name>.azurewebsites.net
```

새로 만든 Azure Web App에 대한 기본 웹 페이지가 표시됩니다.

### <a name="deploy-your-local-app"></a>로컬 앱 배포

로컬 터미널 창으로 돌아가서 Azure 원격을 로컬 Git 리포지토리에 추가합니다. 여기서 *\<deploymentLocalGitUrl-from-create-step>* 를 [원격 웹앱 만들기](#create-a-remote-web-app) 단계에서 저장한 Git 원격의 URL로 바꿉니다.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

다음 명령을 사용하여 Azure 원격에 푸시하여 앱을 배포합니다. Git 자격 증명 관리자에서 자격 증명을 묻는 메시지가 표시되면 [배포 사용자 구성](#configure-a-deployment-user) 단계에서 만든 자격 증명을 사용합니다.

```bash
git push azure master
```

이 명령을 실행하는 데 몇 분 정도 걸릴 수 있습니다. 실행 시 다음 예와 유사한 정보를 출력합니다.
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  master -> master
</pre>

웹 브라우저를 사용하여 배포된 애플리케이션을 찾아보거나 새로 고칩니다.

```bash
http://<your-webapp-name>.azurewebsites.net
```

이전에 `http://localhost:5000`을 방문했을 때 확인한 "Hello World!"라는 메시지가 표시됩니다.

## <a name="create-and-assign-a-managed-identity"></a>관리 ID 만들기 및 할당

Azure CLI에서 이 애플리케이션에 대한 ID를 만들려면 [az webapp-identity assign](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) 명령을 실행합니다.

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

이 작업을 수행하면 다음 JSON 코드 조각이 반환됩니다.

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

키 자격 증명 모음에서 **get** 및 **list** 작업을 수행할 수 있는 웹앱 권한을 부여하려면 principalID를 Azure CLI [az keyvault set-policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) 명령에 전달합니다.

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```


## <a name="modify-the-app-to-access-your-key-vault"></a>키 자격 증명 모음에 액세스하도록 앱 수정

### <a name="install-the-packages"></a>패키지 설치

터미널 창에서 .NET용 Azure Key Vault 클라이언트 라이브러리 패키지를 설치합니다.

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

### <a name="update-the-code"></a>코드 업데이트

akvwebapp 프로젝트에서 Startup.cs 파일을 찾아서 엽니다. 

다음 두 줄을 헤더에 추가합니다.

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

이러한 줄을 `app.UseEndpoints` 호출 앞에 추가하고, 키 자격 증명 모음의 `vaultUri`를 반영하도록 URI를 업데이트합니다. 아래 코드에서는 키 자격 증명 모음에 대한 인증을 위해 ['DefaultAzureCredential()'](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet)을 사용하며, 이는 애플리케이션 관리 ID의 토큰을 사용하여 인증합니다. 또한 키 자격 증명 모음이 제한되는 경우 지수 백오프를 다시 시도에 사용합니다.

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("mySecret");

string secretValue = secret.Value;
```

읽을 `await context.Response.WriteAsync("Hello World!");` 줄을 업데이트합니다.

```csharp
await context.Response.WriteAsync(secretValue);
```

다음 단계로 진행하기 전에 변경 내용을 저장해야 합니다.

### <a name="redeploy-your-web-app"></a>웹앱 다시 배포

코드가 업데이트되었으면 다음 Git 명령을 사용하여 이를 Azure에 다시 배포할 수 있습니다.

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

## <a name="visit-your-completed-web-app"></a>완성된 웹앱 방문

```bash
http://<your-webapp-name>.azurewebsites.net
```

**Hello World**를 확인하기 전에 이제 다음과 같이 비밀 값이 표시됩니다. **성공!**

## <a name="next-steps"></a>다음 단계

- [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)에 대해 자세히 알아보기
- [App Service에 대한 관리 ID](../../app-service/overview-managed-identity.md?tabs=dotnet)에 대해 자세히 알아보기
- [.NET용 Azure Key Vault 클라이언트 라이브러리 API 참조](/dotnet/api/overview/azure/key-vault?view=azure-dotnet) 참조
- [.NET용 Azure Key Vault 클라이언트 라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault) 참조
- [.NET용 Azure Key Vault 클라이언트 라이브러리 v4 NuGet 패키지](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/) 참조


