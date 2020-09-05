---
title: Azure Cloud Services에 Node.js 앱 빌드 및 배포
titleSuffix: Azure Cloud Services
description: 이 자습서를 사용 하 여 Node.js 웹 응용 프로그램을 만들기 위한 MVC 프레임 워크를 제공 하는 Express 모듈을 사용 하 여 새 응용 프로그램을 만들 수 있습니다.
services: cloud-services
documentationcenter: nodejs
author: tgore03
ms.service: cloud-services
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2017
ms.author: tagore
ms.custom: devx-track-javascript
ms.openlocfilehash: 80b7e80e3636b2005594706e17b088b833c7bce7
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142454"
---
# <a name="build-and-deploy-a-nodejs-web-application-using-express-on-an-azure-cloud-services"></a>Azure Cloud Services에서 Express를 사용하여 Node.js 웹 애플리케이션 빌드 및 배포

Node.js에는 핵심 런타임에 최소한의 기능이 들어 있습니다.
개발자는 Node.js 애플리케이션을 개발할 때 추가 기능을 제공하기 위해 종종 타사 모듈을 사용합니다. 이 자습서에서는 Node.js 웹 애플리케이션을 만들기 위해 MVC 프레임워크를 제공하는 [Express](https://github.com/expressjs/express) 모듈을 사용하여 새 애플리케이션을 만듭니다.

아래에는 완성된 애플리케이션의 스크린샷이 표시되어 있습니다.

![Welcome to Express in Azure를 표시하는 웹 브라우저](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="create-a-cloud-service-project"></a>클라우드 서비스 프로젝트 만들기
[!INCLUDE [install-dev-tools](../../includes/install-dev-tools.md)]

다음 단계에 따라 'expressapp'라는 이름의 새 클라우드 서비스 프로젝트를 만듭니다.

1. **시작 메뉴** 또는 **시작 화면**에서 **Windows PowerShell**을 검색합니다. 마지막으로, **Windows PowerShell**을 마우스 오른쪽 단추로 클릭하고 **관리자 권한으로 실행**을 선택합니다.

    ![Azure PowerShell 아이콘](./media/cloud-services-nodejs-develop-deploy-express-app/azure-powershell-start.png)
2. 디렉터리를 **c:\\node** 디렉터리로 변경한 후 다음 명령을 입력하여 **expressapp**라는 이름의 새 솔루션과 **WebRole1**이라는 웹 역할을 만듭니다.

   ```powershell
   PS C:\node> New-AzureServiceProject expressapp
   PS C:\Node\expressapp> Add-AzureNodeWebRole
   PS C:\Node\expressapp> Set-AzureServiceProjectRole WebRole1 Node 0.10.21
   ```

   > [!NOTE]
   > 기본적으로 **Add-AzureNodeWebRole** 은 이전 버전의 Node.js를 사용합니다. 위의 **Set-AzureServiceProjectRole** 문은 Azure에 Node의 v0.10.21을 사용하도록 지시합니다.  매개 변수는 대/소문자를 구분합니다.  **WebRole1\package.json**의 **engines** 속성을 확인하여 올바른 버전의 Node.js가 선택되었는지 확인할 수 있습니다.
>
>

## <a name="install-express"></a>Express 설치
1. 다음 명령을 실행하여 Express 생성기를 설치합니다.

    ```powershell
    PS C:\node\expressapp> npm install express-generator -g
    ```

    npm 명령의 출력이 아래 결과와 비슷하게 표시되어야 합니다.

    ![express 설치 npm 명령의 출력을 표시하는 Windows PowerShell](./media/cloud-services-nodejs-develop-deploy-express-app/express-g.png)
2. 디렉터리를 **WebRole1** 디렉터리로 변경하고 express 명령을 사용하여 새 애플리케이션을 생성합니다.

    ```powershell
    PS C:\node\expressapp\WebRole1> express
    ```

    이전 애플리케이션을 덮어쓸지 묻습니다. **y** 또는 **예**를 입력하고 계속합니다. Express에서 애플리케이션 빌드를 위해 app.js 파일과 폴더 구조를 생성합니다.

    ![express 명령의 출력](./media/cloud-services-nodejs-develop-deploy-express-app/node23.png)
3. package.json 파일에 정의된 추가 종속성을 설치하려면 다음 명령을 입력합니다.

    ```powershell
    PS C:\node\expressapp\WebRole1> npm install
    ```

   ![npm 설치 명령의 출력](./media/cloud-services-nodejs-develop-deploy-express-app/node26.png)
4. 다음 명령을 사용하여 **bin/www** 파일을 **server.js**에 복사합니다. 이는 클라우드 서비스에서 이 애플리케이션의 진입점을 찾을 수 있도록 하기 위한 것입니다.

    ```powershell
    PS C:\node\expressapp\WebRole1> copy bin/www server.js
    ```

   이 명령을 완료하면 WebRole1 디렉터리에 **server.js** 파일에 생성됩니다.
5. **server.js** 를 수정하여 다음 줄에서 '.' 문자 중 하나를 제거합니다.

    ```js
    var app = require('../app');
    ```

   수정을 완료하면 줄이 다음과 같이 표시됩니다.

    ```js
    var app = require('./app');
    ```

   이 변경 작업은 필요한 앱 파일과 동일한 디렉터리로 파일(이전의 **bin/www**)을 이동했기 때문에 필요합니다. 이렇게 변경한 후 **server.js** 파일을 저장합니다.
6. 다음 명령을 사용하여 Azure 에뮬레이터에서 애플리케이션을 실행합니다.

    ```powershell
    PS C:\node\expressapp\WebRole1> Start-AzureEmulator -launch
    ```

    ![welcome to express가 들어 있는 웹 페이지](./media/cloud-services-nodejs-develop-deploy-express-app/node28.png)

## <a name="modifying-the-view"></a>뷰 수정
이제 "Welcome to Express in Azure" 메시지를 표시하는 뷰를 수정합니다.

1. 다음 명령을 입력하여 index.jade 파일을 엽니다.

    ```powershell
    PS C:\node\expressapp\WebRole1> notepad views/index.jade
    ```

   ![index.jade 파일의 내용](./media/cloud-services-nodejs-develop-deploy-express-app/getting-started-19.png)

   Jade는 Express 애플리케이션에서 사용하는 기본 뷰 엔진입니다. Jade 뷰 엔진에 대 한 자세한 내용은을 참조 하십시오 [http://jade-lang.com][http://jade-lang.com] .
2. **in Azure**를 추가하여 텍스트의 마지막 줄을 수정합니다.

   ![index.jade 파일, 마지막 줄: p Welcome to \#{title} in Azure](./media/cloud-services-nodejs-develop-deploy-express-app/node31.png)
3. 파일을 저장하고 메모장을 종료합니다.
4. 브라우저를 새로 고치면 변경 내용이 표시됩니다.

   ![브라우저 창, Welcome to Express in Azure가 들어 있는 페이지](./media/cloud-services-nodejs-develop-deploy-express-app/node32.png)

애플리케이션을 테스트한 후 **Stop-AzureEmulator** cmdlet을 사용하여 에뮬레이터를 중지합니다.

## <a name="publishing-the-application-to-azure"></a>Azure에 애플리케이션 게시
Azure PowerShell 창에서 **Publish-AzureServiceProject** cmdlet을 사용하여 애플리케이션을 클라우드 서비스에 배포합니다.

```powershell
PS C:\node\expressapp\WebRole1> Publish-AzureServiceProject -ServiceName myexpressapp -Location "East US" -Launch
```

배포 작업을 완료하면 브라우저가 열리고 웹 페이지가 표시됩니다.

![Express 페이지를 표시하는 웹 브라우저입니다. URL은 현재 Azure에서 호스트되어 있음을 나타냅니다.](./media/cloud-services-nodejs-develop-deploy-express-app/node36.png)

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Node.js 개발자 센터](https://docs.microsoft.com/azure/developer/javascript/)를 참조하세요.

[Node.js Web Application]: https://www.windowsazure.com/develop/nodejs/tutorials/getting-started/
[Express]: https://expressjs.com/
[http://jade-lang.com]: http://jade-lang.com





