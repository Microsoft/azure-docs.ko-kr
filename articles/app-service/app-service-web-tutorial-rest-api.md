---
title: '자습서: CORS를 통한 RESTful API 호스팅'
description: Azure App Service에서 CORS 지원을 통해 RESTful API를 호스팅하는 방법을 알아봅니다. App Service는 프런트 엔드 웹앱과 백 엔드 API를 모두 호스팅할 수 있습니다.
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/28/2020
ms.custom: devx-track-csharp, mvc, devcenter, seo-javascript-september2019, seo-javascript-october2019, seodec18
ms.openlocfilehash: 9481b6d2740d27b8c3d1309e205edda6017868fa
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213691"
---
# <a name="tutorial-host-a-restful-api-with-cors-in-azure-app-service"></a>자습서: Azure App Service에서 CORS를 통해 RESTful API 호스팅

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 또한 App Service에는 RESTful API에 대한 [CORS(Cross-Origin Resource Sharing)](https://wikipedia.org/wiki/Cross-Origin_Resource_Sharing)의 지원이 기본적으로 제공됩니다. 이 자습서에서는 CORS 지원을 사용하여 ASP.NET Core API 앱을 App Service에 배포하는 방법을 보여 줍니다. 명령줄 도구를 사용하여 앱을 구성하고, Git을 사용하여 앱을 배포합니다. 

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 App Service 리소스 만들기
> * Git을 사용하여 Azure에 RESTful API 배포
> * App Service CORS 지원을 사용하도록 설정

이 자습서의 단계는 macOS, Linux, Windows에서 수행할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* <a href="https://git-scm.com/" target="_blank">Git 설치</a>
 * <a href="https://dotnet.microsoft.com/download/dotnet-core/3.1" target="_blank">최신 .NET Core 3.1 SDK 설치</a>

## <a name="create-local-aspnet-core-app"></a>로컬 ASP.NET Core 앱 만들기

이 단계에서는 로컬 ASP.NET Core 프로젝트를 설정합니다. App Service는 다른 언어로 작성된 API에 대해 동일한 워크플로를 지원합니다.

### <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

터미널 창에서 `cd`를 사용하여 작업 디렉터리로 이동합니다.  

다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
```

이 리포지토리에는 자습서: [Swagger를 사용한 ASP.NET Core Web API 도움말 페이지](/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio)에 따라 만든 앱이 포함되어 있으며, Swagger 생성기를 사용하여 [Swagger UI](https://swagger.io/swagger-ui/)와 Swagger JSON 엔드포인트를 제공합니다.

### <a name="run-the-application"></a>애플리케이션 실행

다음 명령을 실행하여 필요한 패키지를 설치하고 데이터베이스 마이그레이션을 실행하고 애플리케이션을 시작합니다.

```bash
cd dotnet-core-api
dotnet restore
dotnet run
```

브라우저에서 `http://localhost:5000/swagger`로 이동하여 Swagger UI를 실행해 봅니다.

![로컬로 실행되는 ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-app-service-local-swagger-ui.png)

`http://localhost:5000/api/todo`로 이동하여 ToDo JSON 항목 목록을 확인합니다.

`http://localhost:5000`으로 이동하여 브라우저 앱을 실행해 봅니다. 나중에 CORS 기능을 테스트하기 위해 브라우저 앱을 App Service의 원격 API로 지정할 것입니다. 브라우저 앱에 대한 코드는 리포지토리의 _wwwroot_ 디렉터리에 있습니다.

언제든지 ASP.NET Core를 중지하려면 터미널에서 `Ctrl+C`를 누릅니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-app-to-azure"></a>Azure에 앱 배포

이 단계에서는 SQL Database 연결 .NET Core 애플리케이션을 App Service에 배포합니다.

### <a name="configure-local-git-deployment"></a>로컬 Git 배포 구성

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-no-h.md)]

### <a name="create-an-app-service-plan"></a>App Service 플랜 만들기

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-no-h.md)]

### <a name="create-a-web-app"></a>웹앱 만들기

[!INCLUDE [Create web app](../../includes/app-service-web-create-web-app-dotnetcore-win-no-h.md)] 

### <a name="push-to-azure-from-git"></a>Git에서 Azure에 푸시

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-git-push-to-azure-no-h.md)]

<pre>
Enumerating objects: 83, done.
Counting objects: 100% (83/83), done.
Delta compression using up to 8 threads
Compressing objects: 100% (78/78), done.
Writing objects: 100% (83/83), 22.15 KiB | 3.69 MiB/s, done.
Total 83 (delta 26), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '509236e13d'.
remote: Generating deployment script.
remote: Project file path: .\TodoApi.csproj
remote: Generating deployment script for ASP.NET MSBuild16 App
remote: Generated deployment script files
remote: Running deployment command...
remote: Handling ASP.NET Core Web Application deployment with MSBuild16.
remote: .
remote: .
remote: .
remote: Finished successfully.
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
To https://&lt;app_name&gt;.scm.azurewebsites.net/&lt;app_name&gt;.git
 * [new branch]      master -> master
</pre>

### <a name="browse-to-the-azure-app"></a>Azure 앱 찾아보기

브라우저에서 `http://<app_name>.azurewebsites.net/swagger`로 이동하여 Swagger UI를 실행해 봅니다.

![Azure App Service에서 실행되는 ASP.NET Core API](./media/app-service-web-tutorial-rest-api/azure-app-service-browse-app.png)

`http://<app_name>.azurewebsites.net/swagger/v1/swagger.json`으로 이동하여 배포된 API에 대한 _swagger.json_을 확인합니다.

`http://<app_name>.azurewebsites.net/api/todo`로 이동하여 배포된 API가 작동하는지 확인합니다.

## <a name="add-cors-functionality"></a>CORS 기능 추가

다음으로, API에 대한 App Service에서 기본 제공되는 CORS 지원을 사용하도록 설정합니다.

### <a name="test-cors-in-sample-app"></a>샘플 앱에서 CORS 테스트

로컬 리포지토리에서 _wwwroot/index.html_을 엽니다.

51번 줄에서 `apiEndpoint` 변수를 배포된 API의 URL(`http://<app_name>.azurewebsites.net`)로 설정합니다. _\<appname>_ 를 App Service의 앱 이름으로 바꿉니다.

로컬 터미널 창에서 샘플 앱을 다시 실행합니다.

```bash
dotnet run
```

`http://localhost:5000`에 있는 브라우저 앱으로 이동합니다. 브라우저에서 개발자 도구 창을 열고(Windows용 Chrome에서 `Ctrl`+`Shift`+`i`) **콘솔** 탭을 검사합니다. 이제 `No 'Access-Control-Allow-Origin' header is present on the requested resource` 오류 메시지가 표시됩니다.

![브라우저 클라이언트에서 발생한 CORS 오류](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-error.png)

브라우저 앱(`http://localhost:5000`)과 원격 리소스(`http://<app_name>.azurewebsites.net`) 간에 도메인이 일치하지 않고 App Service의 API에서 `Access-Control-Allow-Origin` 헤더를 보내지 않기 때문에 브라우저에서 도메인 간 콘텐츠가 브라우저 앱에 로드되지 않도록 차단했습니다.

프로덕션 환경에서 브라우저 앱에는 localhost URL 대신 공용 URL이 있지만, CORS를 localhost URL로 사용하도록 설정하는 방법은 공용 URL과 동일합니다.

### <a name="enable-cors"></a>CORS를 사용하도록 설정 

Cloud Shell에서 [`az webapp cors add`](/cli/azure/webapp/cors#az-webapp-cors-add) 명령을 사용하여 CORS를 클라이언트 URL로 사용하도록 설정합니다. _&lt;app-name>_ 자리 표시자를 바꿉니다.

```azurecli-interactive
az webapp cors add --resource-group myResourceGroup --name <app-name> --allowed-origins 'http://localhost:5000'
```

`properties.cors.allowedOrigins`에 둘 이상의 클라이언트 URL(`"['URL1','URL2',...]"`)을 설정할 수 있습니다. `"['*']"`를 사용하여 모든 클라이언트 URL을 사용하도록 설정할 수도 있습니다.

> [!NOTE]
> 앱에서 쿠키나 인증 토큰과 같은 자격 증명을 전송하도록 요구하는 경우 브라우저의 응답에 `ACCESS-CONTROL-ALLOW-CREDENTIALS` 헤더가 필요할 수 있습니다. App Service에서 이를 사용하도록 설정하려면 CORS 구성에서 `properties.cors.supportCredentials`를 `true`로 설정합니다. `allowedOrigins`에 `'*'`가 포함된 경우에는 이를 사용하도록 설정할 수 없습니다.

### <a name="test-cors-again"></a>CORS 다시 테스트

`http://localhost:5000`에서 브라우저 앱을 새로 고칩니다. **콘솔** 창의 오류 메시지가 사라지고, 배포된 API의 데이터를 보고 상호 작용할 수 있습니다. 이제 원격 API에서 로컬로 실행 중인 브라우저 앱에 CORS를 지원합니다. 

![브라우저 클라이언트에서 CORS 성공](./media/app-service-web-tutorial-rest-api/azure-app-service-cors-success.png)

축하합니다! CORS 지원을 통해 Azure App Service에서 API를 실행하고 있습니다.

## <a name="app-service-cors-vs-your-cors"></a>App Service CORS 및 사용자 고유 CORS

더 많은 유연성을 위해 App Service CORS 대신 자신의 CORS 유틸리티를 사용할 수 있습니다. 예를 들어 여러 경로 또는 메서드에 대해 허용되는 원본을 서로 다르게 지정할 수 있습니다. App Service CORS를 사용하면 모든 API 경로 및 메서드에 대해 허용되는 원본 집합을 하나만 지정할 수 있으므로 사용자 고유의 CORS 코드를 사용해야 합니다. ASP.NET Core에서 이를 수행하는 방법은 [Enabling Cross-Origin Requests (CORS)](/aspnet/core/security/cors)(원본 간 요청(CORS) 사용)를 참조하세요.

> [!NOTE]
> App Service CORS와 사용자 고유 CORS 코드는 함께 사용하지 마세요. 함께 사용하는 경우 App Service CORS가 우선적으로 적용되며, 사용자 고유의 CORS 코드는 아무런 영향을 주지 않습니다.
>
>

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

<a name="next"></a>
## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Azure CLI를 사용하여 App Service 리소스 만들기
> * Git을 사용하여 Azure에 RESTful API 배포
> * App Service CORS 지원을 사용하도록 설정

사용자를 인증하고 사용자 권한을 부여하는 방법에 대해 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [자습서: 엔드투엔드 사용자 인증 및 권한 부여](tutorial-auth-aad.md)
