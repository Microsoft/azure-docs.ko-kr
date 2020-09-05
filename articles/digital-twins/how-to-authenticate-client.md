---
title: 앱 인증 코드 쓰기
titleSuffix: Azure Digital Twins
description: 클라이언트 응용 프로그램에서 인증 코드를 작성 하는 방법을 참조 하세요.
author: baanders
ms.author: baanders
ms.date: 4/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: devx-track-javascript
ms.openlocfilehash: c211c0e5ef0b39f778db7c922fafc735e2411068
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88930046"
---
# <a name="write-client-app-authentication-code"></a>클라이언트 앱 인증 코드 작성

[Azure Digital Twins 인스턴스와 인증을 설정한](how-to-set-up-instance-scripted.md)후에는 인스턴스와 상호 작용 하는 데 사용할 클라이언트 응용 프로그램을 만들 수 있습니다. 시작 클라이언트 프로젝트를 설정 하면이 문서에서는 **해당 클라이언트 앱에서 코드를 작성** 하 여 Azure Digital twins 인스턴스에 대해 인증 하는 방법을 보여 줍니다.

이 문서의 샘플 코드에는 두 가지 방법이 있습니다. 선택한 언어에 따라 사용자에 게 적합 한 항목을 사용할 수 있습니다.
* 샘플 코드의 첫 번째 섹션에서는 Azure Digital Twins .NET (c #) SDK를 사용 합니다. SDK는 .NET 용 Azure SDK의 일부 이며, [*.net 용 Azure IoT 디지털 쌍 클라이언트 라이브러리*](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core)에 있습니다.
* 샘플 코드의 두 번째 섹션은 .NET SDK를 사용 하지 않고 다른 언어로 AutoRest 생성 된 Sdk를 사용 하는 사용자를 위한 것입니다. 이 전략에 대 한 자세한 내용은 [*방법: AutoRest를 사용 하 여 Azure Digital Twins 용 사용자 지정 Sdk 만들기*](how-to-create-custom-sdks.md)를 참조 하세요.

Azure digital Twins의 Api 및 Sdk에 대 한 자세한 내용은 [*방법: Azure 디지털 쌍 api 및 Sdk 사용*](how-to-use-apis-sdks.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

먼저 [*방법: 인스턴스 및 인증 설정*](how-to-set-up-instance-scripted.md)에서 설정 단계를 완료 합니다. 이렇게 하면 Azure Digital Twins 인스턴스가 있고 사용자에 게 액세스 권한이 있으며 클라이언트 응용 프로그램에 대 한 사용 권한을 설정 하 게 됩니다. 이 설정이 완료 되 면 클라이언트 앱 코드를 작성할 준비가 된 것입니다.

계속 하려면 코드를 작성 하는 클라이언트 앱 프로젝트가 필요 합니다. 클라이언트 앱 프로젝트를 아직 설정 하지 않은 경우이 자습서에서 사용할 수 있도록 선택한 언어로 기본 프로젝트를 만듭니다.

## <a name="authentication-and-client-creation-net-c-sdk"></a>인증 및 클라이언트 만들기: .NET (c #) SDK

먼저 .NET SDK 및 인증 도구를 사용 하기 위해 프로젝트에 다음 패키지를 포함 합니다.
* `Azure.DigitalTwins.Core` (버전 `1.0.0-preview.2` )
* `Azure.Identity`

선택한 도구에 따라 Visual Studio 패키지 관리자 또는 명령줄 도구를 사용 하 여 패키지를 포함할 수 있습니다 `dotnet` . 

또한 다음 using 문을 사용 해야 합니다.

```csharp
using Azure.Identity;
using Azure.DigitalTwins.Core;
```
.NET SDK를 사용 하 여 인증 하려면 [Azure. Identity](https://docs.microsoft.com/dotnet/api/azure.identity?view=azure-dotnet) library에서 정의한 자격 증명 가져오기 방법 중 하나를 사용 합니다. 다음은 일반적으로 사용 되는 두 가지 방법입니다 (동일한 응용 프로그램 에서도 함께 사용 됨).

* [InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) 는 대화형 응용 프로그램을 위한 것 이며 인증 된 SDK 클라이언트를 만드는 데 사용할 수 있습니다.
* [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) 는 MSI (관리 id)를 필요로 하는 경우에 유용 하며, Azure Functions을 사용 하는 것이 좋습니다.

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential 메서드
[InteractiveBrowserCredential](https://docs.microsoft.com/dotnet/api/azure.identity.interactivebrowsercredential?view=azure-dotnet) 메서드는 대화형 응용 프로그램을 위한 것 이며 인증을 위해 웹 브라우저를 엽니다.

대화형 브라우저 자격 증명을 사용 하 여 인증 된 SDK 클라이언트를 만들려면 다음 코드를 추가 합니다.

```csharp
// Your client / app registration ID
private static string clientId = "<your-client-ID>"; 
// Your tenant / directory ID
private static string tenantId = "<your-tenant-ID>";
// The URL of your instance, starting with the protocol (https://)
private static string adtInstanceUrl = "<your-Azure-Digital-Twins-instance-URL>";

//...

DigitalTwinsClient client;
try
{
    var credential = new InteractiveBrowserCredential(tenantId, clientId);
    client = new DigitalTwinsClient(new Uri(adtInstanceUrl), credential);
} catch(Exception e)
{
    Console.WriteLine($"Authentication or client creation error: {e.Message}");
    Environment.Exit(0);
}
```

>[!NOTE]
> 위와 같이 클라이언트 ID, 테 넌 트 ID 및 인스턴스 URL을 코드에 직접 저장할 수 있지만, 코드에서 구성 파일 또는 환경 변수 로부터 이러한 값을 가져오는 것이 좋습니다.

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential 메서드
 [ManagedIdentityCredential](https://docs.microsoft.com/dotnet/api/azure.identity.managedidentitycredential?view=azure-dotnet) 메서드는 [관리 id (MSI)](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)가 필요한 경우 (예: Azure Functions 사용 하는 경우에 유용 합니다.
Azure 함수에서 다음과 같이 관리 되는 id 자격 증명을 사용할 수 있습니다.

```csharp
ManagedIdentityCredential cred = new ManagedIdentityCredential(adtAppId);
DigitalTwinsClientOptions opts = 
    new DigitalTwinsClientOptions { Transport = new HttpClientTransport(httpClient) });
client = new DigitalTwinsClient(new Uri(adtInstanceUrl), cred, opts);
```

방법: 함수 컨텍스트에서 중요 한 구성 옵션 중 일부를 설명 하는 보다 완전 한 예제에 대 한 [*데이터 처리를 위한 Azure 함수 설정을*](how-to-create-azure-function.md) 참조 하세요.

또한 함수에서 인증을 사용 하려면 다음을 수행 해야 합니다.
* [관리 ID 사용](https://docs.microsoft.com/azure/app-service/overview-managed-identity?tabs=dotnet)
* [환경 변수](https://docs.microsoft.com/sandbox/functions-recipes/environment-variables?tabs=csharp) 를 적절 하 게 사용
* 디지털 쌍 Api에 액세스할 수 있도록 하는 함수 앱에 사용 권한을 할당 합니다. Azure Functions 프로세스에 대 한 자세한 내용은 [*방법: 데이터 처리를 위한 Azure 함수 설정을*](how-to-create-azure-function.md)참조 하세요.

## <a name="authentication-with-an-autorest-generated-sdk"></a>AutoRest 생성 SDK를 사용 하 여 인증

.NET을 사용 하지 않는 경우 [*방법: AutoRest를 사용 하 여 Azure Digital Twins 용 사용자 지정 Sdk 만들기*](how-to-create-custom-sdks.md)에 설명 된 대로 원하는 언어로 SDK 라이브러리를 빌드할 수 있습니다.

이 섹션에서는이 경우 인증 하는 방법을 설명 합니다.

### <a name="prerequisites"></a>필수 구성 요소

먼저 [*방법: AutoRest를 사용 하 여 Azure Digital Twins 용 사용자 지정 Sdk 만들기*](how-to-create-custom-sdks.md)의 단계에 따라 AutoRest를 사용 하 여 사용자 지정 sdk를 만드는 단계를 완료 해야 합니다.

이 예제에서는 AutoRest를 사용 하 여 생성 된 Typescript SDK를 사용 합니다. 그 결과 다음도 필요 합니다.
* [msal-js](https://github.com/AzureAD/microsoft-authentication-library-for-js)
* [ms-rest](https://github.com/Azure/ms-rest-js)

### <a name="minimal-authentication-code-sample"></a>최소 인증 코드 샘플

Azure 서비스를 사용 하 여 앱을 인증 하기 위해 클라이언트 앱 내에서 다음 최소 코드를 사용할 수 있습니다.

이전에는 *응용 프로그램 (클라이언트) id* 및 *디렉터리 (테 넌 트) Id* 와 Azure DIGITAL twins 인스턴스의 URL이 필요 합니다.

> [!TIP]
> Azure Digital Twins 인스턴스의 URL은 Azure Digital Twins 인스턴스의 *호스트 이름*앞에 *https://* 를 추가 하 여 수행 됩니다. 인스턴스의 모든 속성과 함께 *호스트 이름을*보려면를 실행할 수 있습니다 `az dt show --dt-name <your-Azure-Digital-Twins-instance>` . 명령을 사용 하 여 `az account show --query tenantId` *디렉터리 (테 넌 트) ID*를 볼 수 있습니다. 

```javascript
import * as Msal from "msal";
import { TokenCredentials } from "@azure/ms-rest-js";
// Autorest-generated SDK
import { AzureDigitalTwinsAPI } from './azureDigitalTwinsAPI';

// Client / app registration ID
var ClientId = "<your-client-ID>";
// Azure tenant / directory ID
var TenantId = "<your-tenant-ID>";
// URL of the Azure Digital Twins instance
var AdtInstanceUrl = "<your-instance-URL>"; 

var AdtAppId = "https://digitaltwins.azure.net";

let client = null;

export async function login() {

    const msalConfig = {
        auth: {
            clientId: ClientId,
            redirectUri: "http://localhost:3000",
            authority: "https://login.microsoftonline.com/"+TenantId
        }
    };

    const msalInstance = new Msal.UserAgentApplication(msalConfig);

    msalInstance.handleRedirectCallback((error, response) => {
        // handle redirect response or error
    });

    var loginRequest = {
        scopes: [AdtAppId + "/.default"] 
    };

    try {
        await msalInstance.loginPopup(loginRequest)
        var accessToken;
        // if the user is already logged in you can acquire a token
        if (msalInstance.getAccount()) {
            var tokenRequest = {
                scopes: [AdtAppId + "/.default"]
            };
            try {
                const response = await msalInstance.acquireTokenSilent(tokenRequest);
                accessToken = response.accessToken;
            } catch (err) {
                if (err.name === "InteractionRequiredAuthError") {
                    const response = await msalInstance.acquireTokenPopup(tokenRequest)
                    accessToken = response.accessToken;
                }
            }
        }
        if (accessToken!=null)
        {
            var tokenCredentials = new TokenCredentials(accessToken);
                
            // Add token and server URL to service instance
            const clientConfig = {
                baseUri: AdtInstanceUrl
            };
            client = new AzureDigitalTwinsAPI(tokenCredentials, clientConfig);
            appDataStore.client = client;
        }
    } catch (err) {
        ...
    }
}
```

위의 코드에서 간단 하 게 클라이언트 ID, 테 넌 트 ID 및 인스턴스 URL을 코드에 직접 저장 하는 경우 코드에서 구성 파일 또는 환경 변수 로부터 이러한 값을 가져오는 것이 좋습니다.

MSAL에는 캐싱 및 기타 인증 흐름과 같은 작업을 구현 하는 데 사용할 수 있는 더 많은 옵션이 있습니다. 이에 대 한 자세한 내용은 [*MSAL (Microsoft 인증 라이브러리) 개요*](../active-directory/develop/msal-overview.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins에서 보안이 작동 하는 방식에 대해 자세히 알아보세요.
* [*개념: Azure Digital Twins 솔루션에 대 한 보안*](concepts-security.md)

또는 이제 인증이 설정 되었으므로 인스턴스에서 모델 만들기로 이동 합니다.
* [*방법: 사용자 지정 모델 관리*](how-to-manage-model.md)