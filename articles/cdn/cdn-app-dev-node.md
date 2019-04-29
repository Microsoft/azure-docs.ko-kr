---
title: Node.js용 Azure CDN SDK 시작하기 | Microsoft Docs
description: Node.js 애플리케이션을 작성하여 Azure CDN을 관리하는 방법에 대해 알아봅니다.
services: cdn
documentationcenter: nodejs
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: c4bb6a61-de3d-4f0c-9dca-202554c43dfa
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 48a84520a61d19968b467091871459e21898dd5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564244"
---
# <a name="get-started-with-azure-cdn-development"></a>Azure CDN 개발 시작
> [!div class="op_single_selector"]
> * [Node.JS](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

[Node.js용 Azure CDN SDK](https://www.npmjs.com/package/azure-arm-cdn) 를 사용하여 CDN 프로필과 엔드포인트의 생성 및 관리를 자동화할 수 있습니다.  이 자습서에서는 여러 가지 사용 가능한 작업을 보여주는 간단한 Node.js 콘솔 애플리케이션을 살펴봅니다.  이 자습서는 Node.js용 Azure CDN SDK의 모든 측면을 상세하게 설명하지 않습니다.

이 자습서를 완료하려면 [Node.js](https://www.nodejs.org) **4.x.x** 이상을 설치하고 구성해야 합니다.  원하는 텍스트 편집기를 사용하여 Node.js 애플리케이션을 만들 수 있습니다.  이 자습서를 작성하려면 [Visual Studio 코드](https://code.visualstudio.com)를 사용합니다.  

> [!TIP]
> [이 자습서에서 완성된 프로젝트](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74) 는 MSDN에서 다운로드할 수 있습니다.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-npm-dependencies"></a>프로젝트 만들기 및 NPM 종속성 추가
CDN 프로필용 리소스 그룹을 만들고 해당 그룹에서 CDN 프로필과 엔드포인트를 관리하기 위한 Azure AD 애플리케이션 권한을 부여했으므로, 애플리케이션을 만들 수 있습니다.

애플리케이션을 저장할 폴더를 만듭니다.  현재 경로에 있는 Node.js 도구를 포함한 콘솔에서 새 폴더에 현재 위치를 설정하고 다음을 실행하여 프로젝트를 초기화합니다.

    npm init

그런 다음 프로젝트를 초기화하는 일련의 질문이 나타납니다.  **진입점**의 경우 이 자습서에서는 *app.js*를 사용합니다.  다음 예제에서 다른 선택 항목을 볼 수 있습니다.

![NPM init 출력](./media/cdn-app-dev-node/cdn-npm-init.png)

프로젝트는 *packages.json* 파일을 사용하여 초기화됩니다.  이 프로젝트에서는 NPM 패키지에 포함된 일부 Azure 라이브러리를 사용할 것입니다.  Node.js용 Azure 클라이언트 런타임(ms-rest-azure) 및 Node.js용 Azure CDN 클라이언트 라이브러리(azure-arm-cd)를 사용할 것입니다.  해당 사항을 종속성으로 프로젝트에 추가하겠습니다.

    npm install --save ms-rest-azure
    npm install --save azure-arm-cdn

패키지 설치가 완료된 후에 *package.json* 파일은 이 예제와 유사하게 표시됨(버전 번호가 달라질 수 있음):

``` json
{
  "name": "cdn_node",
  "version": "1.0.0",
  "description": "Azure CDN Node.js tutorial project",
  "main": "app.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Cam Soper",
  "license": "MIT",
  "dependencies": {
    "azure-arm-cdn": "^0.2.1",
    "ms-rest-azure": "^1.14.4"
  }
}
```

마지막으로 텍스트 편집기를 사용하여 빈 텍스트 파일을 만들고 프로젝트 폴더의 루트에 *app.js*로 저장합니다.  이제 코드 작성을 시작할 준비가 되었습니다.

## <a name="requires-constants-authentication-and-structure"></a>requires, 상수, 인증 및 구조
편집기에서 열린 *app.js* 를 사용하여 작성된 프로그램의 기본 구조를 살펴보겠습니다.

1. 다음을 사용하여 위쪽에 있는 NPM 패키지에 "requires"를 추가합니다.
   
    ``` javascript
    var msRestAzure = require('ms-rest-azure');
    var cdnManagementClient = require('azure-arm-cdn');
    ```
2. 메서드가 사용할 몇 가지 상수를 정의해야 합니다.  다음을 추가합니다.  **&lt;꺽쇠 괄호&gt;** 를 포함한 자리 표시자를 필요에 따라 고유 값으로 교체합니다.
   
    ``` javascript
    //Tenant app constants
    const clientId = "<YOUR CLIENT ID>";
    const clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    const tenantId = "<YOUR TENANT ID>";
   
    //Application constants
    const subscriptionId = "<YOUR SUBSCRIPTION ID>";
    const resourceGroupName = "CdnConsoleTutorial";
    const resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. 다음으로 CDN 관리 클라이언트를 인스턴스화하고 자격 증명을 제공합니다.
   
    ``` javascript
    var credentials = new msRestAzure.ApplicationTokenCredentials(clientId, tenantId, clientSecret);
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    개별 사용자 인증을 사용한다면 다음 두 줄은 약간 다르게 표시됩니다.
   
   > [!IMPORTANT]
   > 서비스 주체가 아닌 개별 사용자 인증을 사용할 경우에만 다음 코드 샘플을 사용하세요.  개별 사용자 자격 증명을 보호하고 보안 상태를 유지하도록 주의합니다.
   > 
   > 
   
    ``` javascript
    var credentials = new msRestAzure.UserTokenCredentials(clientId, 
        tenantId, '<username>', '<password>', '<redirect URI>');
    var cdnClient = new cdnManagementClient(credentials, subscriptionId);
    ```
   
    올바른 정보로 **&lt;꺾쇠 괄호&gt;** 의 항목을 교체해야 합니다.  `<redirect URI>`의 경우 Azure AD에서 애플리케이션을 등록할 때 입력한 리디렉션 URI를 사용합니다.
4. Node.js 콘솔 애플리케이션에서는 몇 가지 명령줄 매개 변수를 사용하려고 합니다.  적어도 하나의 매개 변수가 전달되었는지 유효성을 검사해 보겠습니다.
   
   ```javascript
   //Collect command-line parameters
   var parms = process.argv.slice(2);
   
   //Do we have parameters?
   if(parms == null || parms.length == 0)
   {
       console.log("Not enough parameters!");
       console.log("Valid commands are list, delete, create, and purge.");
       process.exit(1);
   }
   ```
5. 그래서 프로그램의 주요 부분을 살펴보고 여기서 전달된 매개 변수에 따라 다른 기능으로 갈라집니다.
   
    ```javascript
    switch(parms[0].toLowerCase())
    {
        case "list":
            cdnList();
            break;
   
        case "create":
            cdnCreate();
            break;
   
        case "delete":
            cdnDelete();
            break;
   
        case "purge":
            cdnPurge();
            break;
   
        default:
            console.log("Valid commands are list, delete, create, and purge.");
            process.exit(1);
    }
    ```
6. 프로그램의 여러 위치에서 매개 변수의 적합한 수를 전달하도록 하고 올바르지 않은 경우 도움말을 표시합니다.  작업을 수행할 함수를 만들어 보겠습니다.
   
   ```javascript
   function requireParms(parmCount) {
       if(parms.length < parmCount) {
           usageHelp(parms[0].toLowerCase());
           process.exit(1);
       }
   }
   
   function usageHelp(cmd) {
       console.log("Usage for " + cmd + ":");
       switch(cmd)
       {
           case "list":
               console.log("list profiles");
               console.log("list endpoints <profile name>");
               break;
   
           case "create":
               console.log("create profile <profile name>");
               console.log("create endpoint <profile name> <endpoint name> <origin hostname>");
               break;
   
           case "delete":
               console.log("delete profile <profile name>");
               console.log("delete endpoint <profile name> <endpoint name>");
               break;
   
           case "purge":
               console.log("purge <profile name> <endpoint name> <path>");
               break;
   
           default:
               console.log("Invalid command.");
       }
   }
   ```
7. 마지막으로 CDN 관리 클라이언트에서 사용하는 기능은 비동기이므로 작업이 완료되면 콜백할 메서드가 필요합니다.  CDN 관리 클라이언트(있는 경우)의 출력을 표시하고 프로그램을 정상적으로 종료할 수 있는 파일을 만들어 보겠습니다.
   
    ```javascript
    function callback(err, result, request, response) {
        if (err) {
            console.log(err);
            process.exit(1);
        } else {
            console.log((result == null) ? "Done!" : result);
            process.exit(0);
        }
    }
    ```

이제 프로그램의 기본 구조가 작성되었으므로 매개 변수에 따라 호출된 함수를 만들어야 합니다.

## <a name="list-cdn-profiles-and-endpoints"></a>CDN 프로필 및 엔드포인트 목록화하기
기존 프로필 및 엔드포인트를 나열하는 코드부터 살펴 보겠습니다.  나의 코드 주석이 각 매개 변수가 이동할 위치를 알 수 있도록 예상되는 구문을 제공합니다.

```javascript
// list profiles
// list endpoints <profile name>
function cdnList(){
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profiles":
            console.log("Listing profiles...");
            cdnClient.profiles.listByResourceGroup(resourceGroupName, callback);
            break;

        case "endpoints":
            requireParms(3);
            console.log("Listing endpoints...");
            cdnClient.endpoints.listByProfile(parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>CDN 프로필 및 엔드포인트 만들기
다음으로 프로필 및 엔드포인트를 만드는 함수를 작성합니다.

```javascript
function cdnCreate() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        case "profile":
            cdnCreateProfile();
            break;

        case "endpoint":
            cdnCreateEndpoint();
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}

// create profile <profile name>
function cdnCreateProfile() {
    requireParms(3);
    console.log("Creating profile...");
    var standardCreateParameters = {
        location: resourceLocation,
        sku: {
            name: 'Standard_Verizon'
        }
    };

    cdnClient.profiles.create(parms[2], standardCreateParameters, resourceGroupName, callback);
}

// create endpoint <profile name> <endpoint name> <origin hostname>        
function cdnCreateEndpoint() {
    requireParms(5);
    console.log("Creating endpoint...");
    var endpointProperties = {
        location: resourceLocation,
        origins: [{
            name: parms[4],
            hostName: parms[4]
        }]
    };

    cdnClient.endpoints.create(parms[3], endpointProperties, parms[2], resourceGroupName, callback);
}
```

## <a name="purge-an-endpoint"></a>엔드포인트 삭제
엔드포인트를 만들었을 경우, 프로그램에서 흔히 수행하는 작업은 엔드포인트의 콘텐츠를 삭제하는 것입니다.

```javascript
// purge <profile name> <endpoint name> <path>
function cdnPurge() {
    requireParms(4);
    console.log("Purging endpoint...");
    var purgeContentPaths = [ parms[3] ];
    cdnClient.endpoints.purgeContent(parms[2], parms[1], resourceGroupName, purgeContentPaths, callback);
}
```

## <a name="delete-cdn-profiles-and-endpoints"></a>CDN 프로필 및 엔드포인트 삭제
포함된 마지막 함수는 엔드포인트 및 프로필을 삭제합니다.

```javascript
function cdnDelete() {
    requireParms(2);
    switch(parms[1].toLowerCase())
    {
        // delete profile <profile name>
        case "profile":
            requireParms(3);
            console.log("Deleting profile...");
            cdnClient.profiles.deleteIfExists(parms[2], resourceGroupName, callback);
            break;

        // delete endpoint <profile name> <endpoint name>
        case "endpoint":
            requireParms(4);
            console.log("Deleting endpoint...");
            cdnClient.endpoints.deleteIfExists(parms[3], parms[2], resourceGroupName, callback);
            break;

        default:
            console.log("Invalid parameter.");
            process.exit(1);
    }
}
```

## <a name="running-the-program"></a>프로그램 실행
이제 선호하는 디버거를 사용하거나 콘솔에서 Node.js 프로그램을 실행할 수 있습니다.

> [!TIP]
> Visual Studio 코드를 디버거로 사용하는 경우 사용자 환경을 설정하여 명령줄 매개 변수를 전달해야 합니다.  Visual Studio Code는 **launch.json** 파일에서 이 작업을 수행합니다.  **args**라는 속성을 찾고 다음과 유사하게 표시되도록 사용자 매개 변수에 대한 문자열 값의 배열을 추가합니다. `"args": ["list", "profiles"]`.
> 
> 

프로필을 나열하여 시작하겠습니다.

![프로필 나열](./media/cdn-app-dev-node/cdn-list-profiles.png)

다시 빈 배열로 돌아옵니다.  리소스 그룹에 프로필이 없기 때문에 정상입니다.  이제 프로필을 만들겠습니다.

![프로필 만들기](./media/cdn-app-dev-node/cdn-create-profile.png)

이제 엔드포인트를 추가하겠습니다.

![엔드포인트 만들기](./media/cdn-app-dev-node/cdn-create-endpoint.png)

마지막으로 프로필을 삭제하겠습니다.

![프로필 삭제](./media/cdn-app-dev-node/cdn-delete-profile.png)

## <a name="next-steps"></a>다음 단계
이 연습에서 작성된 프로젝트를 보려면 [샘플을 다운로드하세요](https://code.msdn.microsoft.com/Azure-CDN-SDK-for-Nodejs-c712bc74).

Node.js용 Azure CDN SDK에 대한 참조를 보려면 [참조](https://azure.github.io/azure-sdk-for-node/azure-arm-cdn/latest/)를 봅니다.

Node.js용 Azure SDK에 대한 추가 설명서를 찾으려면 [전체 참조](https://azure.github.io/azure-sdk-for-node/)를 봅니다.

[PowerShell](cdn-manage-powershell.md)을 사용하여 CDN 리소스를 관리합니다.

