---
title: Azure에서 함수 앱 설정 구성
description: Azure 함수 앱 설정을 구성하는 방법에 알아봅니다.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 08/14/2019
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: 662a04dbcc39f3fa95b0098eb8fe556b18b3495b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230567"
---
# <a name="manage-your-function-app"></a>함수 앱 관리 

Azure Functions에서 함수 앱은 개별 함수에 대한 실행 컨텍스트를 제공합니다. 함수 앱 동작은 지정된 함수 앱에서 호스트하는 모든 함수에 적용됩니다. 함수 앱의 모든 함수는 동일한 [언어](supported-languages.md)여야 합니다. 

함수 앱의 개별 함수는 함께 배포 되며 함께 확장 됩니다. 동일한 함수 앱의 모든 함수는 함수 앱이 확장 될 때 인스턴스당 리소스를 공유 합니다. 

연결 문자열, 환경 변수 및 기타 응용 프로그램 설정은 각 함수 앱에 대해 개별적으로 정의 됩니다. 함수 앱 간에 공유 해야 하는 모든 데이터는 지속형 저장소에 외부적으로 저장 해야 합니다.

이 문서에서는 함수 앱을 구성 하 고 관리 하는 방법을 설명 합니다. 

> [!TIP]  
> [Azure CLI]를 사용 하 여 많은 구성 옵션을 관리할 수도 있습니다. 

## <a name="get-started-in-the-azure-portal"></a>Azure Portal에서 시작

시작하려면 [Azure Portal]로 이동한 후 Azure 계정으로 로그인합니다. 포털 맨 위에 있는 검색 표시줄에 함수 앱의 이름을 입력하고 목록에서 선택합니다. 함수 앱을 선택하면 다음 페이지가 표시됩니다.

![Azure Portal의 함수 앱 개요](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png)

개요 페이지, 특히 **[응용 프로그램 설정](#settings)** 및 **[플랫폼 기능](#platform-features)** 에서 함수 앱을 관리 하는 데 필요한 모든 항목으로 이동할 수 있습니다.

## <a name="settings"></a>애플리케이션 설정

**응용 프로그램 설정** 탭은 함수 앱에서 사용 하는 설정을 유지 합니다. 이러한 설정은 암호화 되어 저장 되므로 **값 표시** 를 선택 하 여 포털에서 값을 확인 해야 합니다. Azure CLI를 사용 하 여 응용 프로그램 설정에 액세스할 수도 있습니다.

### <a name="portal"></a>포털

포털에서 설정을 추가 하려면 **새 응용 프로그램 설정** 을 선택 하 고 새 키-값 쌍을 추가 합니다.

![Azure Portal의 함수 앱 설정입니다.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

### <a name="azure-cli"></a>Azure CLI

다음 예제와 같이 [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list) 명령은 기존 응용 프로그램 설정을 반환 합니다.

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) 명령은 응용 프로그램 설정을 추가 하거나 업데이트 합니다. 다음 예에서는 `CUSTOM_FUNCTION_APP_SETTING` 키와 `12345`값을 사용 하 여 설정을 만듭니다.


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

### <a name="use-application-settings"></a>응용 프로그램 설정 사용

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

함수 앱을 로컬로 개발 하는 경우에는 이러한 값의 로컬 복사본을 로컬에서 설정 해야 합니다. 자세히 알아보려면 [로컬 설정 파일](functions-run-local.md#local-settings-file)을 참조 하세요.

## <a name="platform-features"></a>플랫폼 기능

![함수 앱 플랫폼 기능 탭.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-features-tab.png)

함수 앱은 Azure App Service 플랫폼에서 실행되고 유지 관리됩니다. 따라서 함수 앱은 Azure의 핵심 웹 호스팅 플랫폼 기능 대부분에 액세스할 수 있습니다. **플랫폼 기능** 탭에서는 함수 앱에서 사용할 수 있는 App Service 플랫폼의 많은 기능에 액세스할 수 있습니다. 

> [!NOTE]
> 함수 앱이 소비 호스팅 계획에서 실행될 때 모든 App Service 기능을 사용할 수 있는 것은 아닙니다.

이 문서의 나머지 부분에서는 함수에 유용한 Azure Portal의 다음 App Service 기능에 대해 집중적으로 설명 합니다.

+ [App Service 편집기](#editor)
+ [Console](#console)
+ [고급 도구(Kudu)](#kudu)
+ [배포 옵션](#deployment)
+ [CORS](#cors)
+ [인증](#auth)

App Service 설정을 사용하는 방법에 대한 자세한 내용은 [Azure App Service 설정 구성](../app-service/configure-common.md)을 참조하세요.

### <a name="editor"></a>App Service 편집기

![App Service 편집기](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

App Service 편집기는 JSON 구성 파일과 코드 파일을 둘 다 수정하는 데 사용할 수 있는 포털 내 고급 편집기입니다. 이 옵션을 선택하면 기본 편집기와 함께 별도의 브라우저 탭이 실행됩니다. 이를 통해 Git 리포지토리와 통합하고 코드를 실행 및 디버깅하며 함수 앱 설정을 수정할 수 있습니다. 이 편집기는 기본 제공 함수 편집기와 비교 하 여 함수에 대한 향상 된 개발 환경을 제공 합니다.  

로컬 컴퓨터에서 함수를 개발 하는 것이 좋습니다. 로컬로 개발 하 고 Azure에 게시 하는 경우 프로젝트 파일은 포털에서 읽기 전용입니다. 자세히 알아보려면 [로컬에서 코드 및 테스트 Azure Functions](functions-develop-local.md)를 참조 하세요.

### <a name="console"></a>콘솔

![함수 앱 콘솔](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

포털 내 콘솔은 명령줄에서 함수 앱과 상호 작용하려는 경우에 이상적인 개발자 도구입니다. 일반적인 명령에는 배치 파일 및 스크립트 실행과 함께 디렉터리 및 파일의 생성 및 탐색이 포함됩니다. 

로컬로 개발 하는 경우 [Azure Functions Core Tools](functions-run-local.md) 및 [Azure CLI]를 사용 하는 것이 좋습니다.

### <a name="kudu"></a>고급 도구(Kudu)

![Kudu 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

App Service용 고급 도구(Kudu라고도 함)를 사용하면 함수 앱의 고급 관리 기능에 액세스할 수 있습니다. Kudu에서 시스템 정보, 앱 설정, 환경 변수, 사이트 확장, HTTP 헤더 및 서버 변수를 관리할 수 있습니다. **과 같은 함수 앱에 대한 SCM 엔드포인트로 이동하여** Kudu`https://<myfunctionapp>.scm.azurewebsites.net/`를 시작할 수도 있습니다. 


### <a name="deployment"></a>배포 센터

소스 제어 솔루션을 사용 하 여 함수 코드를 개발 하 고 유지 관리 하는 경우 Deployment Center를 사용 하 여 소스 제어에서 빌드 및 배포할 수 있습니다. 업데이트를 수행할 때 프로젝트가 빌드되고 Azure에 배포 됩니다. 자세한 내용은 [Azure Functions 배포 기술](functions-deployment-technologies.md)(영문)을 참조 하세요.

### <a name="cors"></a>원본 간 리소스 공유

클라이언트에서 악의적인 코드가 실행 되는 것을 방지 하기 위해 최신 브라우저는 웹 응용 프로그램의 요청을 별도의 도메인에서 실행 되는 리소스로 차단 합니다. [CORS (원본 간 리소스 공유)](https://developer.mozilla.org/docs/Web/HTTP/CORS) 를 사용 하면 `Access-Control-Allow-Origin` 헤더가 함수 앱에서 엔드포인트을 호출할 수 있는 원본을 선언할 수 있습니다.

#### <a name="portal"></a>포털

함수 앱에 대해 **허용 된 원본** 목록을 구성 하면 함수 앱에서 HTTP 엔드포인트의 모든 응답에 `Access-Control-Allow-Origin` 헤더가 자동으로 추가 됩니다. 

![함수 앱의 CORS 목록 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

와일드 카드 (`*`)를 사용 하는 경우 다른 모든 도메인은 무시 됩니다. 

[`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) 명령을 사용 하 여 허용 된 원본 목록에 도메인을 추가 합니다. 다음 예에서는 contoso.com 도메인을 추가 합니다.

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

[`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) 명령을 사용 하 여 현재 허용 된 원본을 나열 합니다.

### <a name="auth"></a>인증

![함수 앱에 대한 인증 구성](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

함수가 HTTP 트리거를 사용하는 경우 먼저 호출이 인증되도록 요구할 수 있습니다. App Service는 Facebook, Microsoft 및 Twitter와 같은 소셜 공급자를 사용 하 여 Azure Active Directory 인증 및 로그인을 지원 합니다. 특정 인증 공급자를 구성하는 방법에 대한 자세한 내용은 [Azure App Service 인증 개요](../app-service/overview-authentication-authorization.md)를 참조하세요. 


## <a name="next-steps"></a>다음 단계

+ [Azure App Service 설정 구성](../app-service/configure-common.md)
+ [Azure Functions에 대한 연속 배포](functions-continuous-deployment.md)

[Azure CLI]: /cli/azure/
[Azure Portal]: https://portal.azure.com
