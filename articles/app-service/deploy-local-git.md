---
title: 로컬 Git 리포지토리에서 배포
description: Azure App Service에 로컬 Git 배포를 사용하는 방법에 대해 알아봅니다. 로컬 컴퓨터의 코드를 배포 하는 가장 간단한 방법 중 하나입니다.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77152995"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Azure App Service에 대 한 로컬 Git 배포

이 방법 가이드에서는 로컬 컴퓨터의 Git 리포지토리에서 [Azure App Service](overview.md) 에 앱을 배포 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 조건

이 방법 가이드의 단계를 수행하려면

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Git를 설치](https://www.git-scm.com/downloads)합니다.
  
- 배포 하려는 코드가 포함 된 로컬 Git 리포지토리가 있습니다. 샘플 리포지토리를 다운로드 하려면 로컬 터미널 창에서 다음 명령을 실행 합니다.
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Kudu build 서버를 사용 하 여 배포

Kudu App Service 빌드 서버를 사용 하 여 앱에 대 한 로컬 Git 배포를 사용 하도록 설정 하는 가장 쉬운 방법은 Azure Cloud Shell를 사용 하는 것입니다. 

### <a name="configure-a-deployment-user"></a>배포 사용자 구성

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>배포 URL 가져오기

기존 앱에 대 한 로컬 Git 배포를 사용 하도록 URL을 가져오려면 Cloud Shell에서 [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) 를 실행 합니다. \<앱 이름 > 및 \<그룹 이름 >를 앱 및 해당 Azure 리소스 그룹의 이름으로 바꿉니다.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Linux app service 계획을 사용 하는 경우 다음 매개 변수를 추가 해야 합니다.--runtime python | 3.7


또는 새 Git 사용 앱을 만들려면 `--deployment-local-git` 매개 변수를 사용 하 여 Cloud Shell에서 [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 를 실행 합니다. \<앱 이름 >, \<그룹-이름 > 및 \<계획 이름 >를 새 Git 앱, 해당 Azure 리소스 그룹 및 해당 Azure App Service 계획에 대 한 이름으로 바꿉니다.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

두 명령은 모두 `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`와 같은 URL을 반환 합니다. 다음 단계에서이 URL을 사용 하 여 앱을 배포 합니다.

이 계정 수준 URL을 사용 하는 대신 앱 수준 자격 증명을 사용 하 여 로컬 Git을 사용 하도록 설정할 수도 있습니다. Azure App Service는 모든 앱에 대해 이러한 자격 증명을 자동으로 생성 합니다. 

Cloud Shell에서 다음 명령을 실행 하 여 앱 자격 증명을 가져옵니다. \<앱 이름 > 및 \<그룹 이름 >를 앱 이름 및 Azure 리소스 그룹 이름으로 바꿉니다.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

다음 단계에서을 반환 하는 URL을 사용 하 여 앱을 배포 합니다.

### <a name="deploy-the-web-app"></a>웹앱 배포

1. 로컬 Git 리포지토리로 로컬 터미널 창을 열고 Azure 원격을 추가 합니다. 다음 명령에서 \<url >을 이전 단계에서 가져온 배포 사용자 관련 URL 또는 앱 별 URL로 바꿉니다.
   
   ```bash
   git remote add azure <url>
   ```
   
1. `git push azure master`를 사용 하 여 Azure 원격에 푸시합니다. 
   
1. **Git 자격 증명 관리자** 창에서 Azure 로그인 암호가 아닌 [배포 사용자 암호](#configure-a-deployment-user)를 입력 합니다.
   
1. 출력을 검토합니다. ASP.NET에 대 한 MSBuild, node.js에 대 한 `npm install` 및 Python 용 `pip install`와 같은 런타임 관련 자동화가 표시 될 수 있습니다. 
   
1. Azure Portal에서 앱으로 이동 하 여 콘텐츠가 배포 되었는지 확인 합니다.

## <a name="deploy-with-azure-pipelines-builds"></a>Azure Pipelines 빌드를 사용 하 여 배포

계정에 필요한 권한이 있는 경우 앱에 대 한 로컬 Git 배포를 사용 하도록 Azure Pipelines (미리 보기)를 설정할 수 있습니다. 

- Azure 계정에는 Azure Active Directory에 대 한 쓰기 권한이 있어야 하 고 서비스를 만들 수 있습니다. 
  
- Azure 계정에는 Azure 구독에 **소유자** 역할이 있어야 합니다.

- 사용 하려는 Azure DevOps 프로젝트의 관리자 여야 합니다.

Azure Pipelines (미리 보기)를 사용 하 여 앱에 대 한 로컬 Git 배포를 사용 하도록 설정 하려면

1. [Azure Portal](https://portal.azure.com)에서 **App Services**를 검색 하 고 선택 합니다. 

1. Azure App Service 앱을 선택 하 고 왼쪽 메뉴에서 **Deployment Center** 를 선택 합니다.
   
1. **Deployment Center** 페이지에서 **로컬 Git**를 선택한 다음 **계속**을 선택 합니다. 
   
   ![로컬 Git을 선택 하 고 계속을 선택 합니다.](media/app-service-deploy-local-git/portal-enable.png)
   
1. **빌드 공급자** 페이지에서 **Azure Pipelines (미리 보기)** 를 선택 하 고 **계속**을 선택 합니다. 
   
   ![Azure Pipelines (미리 보기)를 선택 하 고 계속을 선택 합니다.](media/app-service-deploy-local-git/pipeline-builds.png)

1. **구성** 페이지에서 새 Azure devops 조직을 구성 하거나 기존 조직을 지정 하 고 **계속**을 선택 합니다.
   
   > [!NOTE]
   > 기존 Azure DevOps 조직이 나열 되지 않은 경우 Azure 구독에 연결 해야 할 수 있습니다. 자세한 내용은 [CD 릴리스 파이프라인 정의](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)를 참조 하세요.
   
1. App Service 계획 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/plans/)에 따라 **스테이징에 배포** 페이지가 표시 될 수 있습니다. [배포 슬롯을 사용 하도록](deploy-staging-slots.md)설정할지 여부를 선택 하 고 **계속**을 선택 합니다.
   
1. **요약** 페이지에서 설정을 검토 한 다음 **마침**을 선택 합니다.
   
1. Azure 파이프라인이 준비 되 면 다음 단계에서 사용할 수 있도록 **배포 센터** 페이지에서 GIT 리포지토리 URL을 복사 합니다. 
   
   ![Git 리포지토리 URL 복사](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. 로컬 터미널 창에서 로컬 Git 리포지토리에 Azure 원격을 추가 합니다. 명령에서 \<url >를 이전 단계에서 얻은 Git 리포지토리의 URL로 바꿉니다.
   
   ```bash
   git remote add azure <url>
   ```
   
1. `git push azure master`를 사용 하 여 Azure 원격에 푸시합니다. 
   
1. **Git 자격 증명 관리자** 페이지에서 visualstudio.com username을 사용 하 여 로그인 합니다. 다른 인증 방법은 [Azure DevOps Services 인증 개요](/vsts/git/auth-overview?view=vsts)를 참조 하세요.
   
1. 배포가 완료 되 면 `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`에서 빌드 진행률을 확인 하 고 `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`에서 배포 진행률을 확인 합니다.
   
1. Azure Portal에서 앱으로 이동 하 여 콘텐츠가 배포 되었는지 확인 합니다.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>배포 문제 해결

Git를 사용 하 여 Azure에서 App Service 앱에 게시 하는 경우 다음과 같은 일반적인 오류 메시지가 표시 될 수 있습니다.

|메시지|원인|해결 방법
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|앱이 실행 되 고 있지 않습니다.|Azure Portal에서 앱을 시작합니다. 웹 앱이 중지 되 면 Git 배포를 사용할 수 없습니다.|
|`Couldn't resolve host 'hostname'`|' Azure ' 원격에 대 한 주소 정보가 잘못 되었습니다.|`git remote -v` 명령을 사용하여 모든 원격을 관련 URL과 함께 나열합니다. 'azure' 원격의 URL이 올바른지 확인합니다. 필요한 경우 제거하고 올바른 URL을 사용하여 이 원격을 다시 만드세요.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|`git push`중에 분기를 지정 하지 않았거나 `.gitconfig`에서 `push.default` 값을 설정 하지 않았습니다.|Master 분기 `git push azure master`지정 하 여 `git push`를 다시 실행 합니다.|
|`src refspec [branchname] does not match any.`|' Azure ' 원격의 마스터가 아닌 다른 분기에 푸시 하려고 했습니다.|Master 분기 `git push azure master`지정 하 여 `git push`를 다시 실행 합니다.|
|`RPC failed; result=22, HTTP code = 5xx.`|이 오류는 HTTPS를 통해 큰 git 리포지토리를 푸시하려고 시도하는 경우 발생할 수 있습니다.|`postBuffer`을 더 크게 만들려면 로컬 컴퓨터의 git 구성을 변경 하십시오. 예: `git config --global http.postBuffer 524288000`|
|`Error - Changes committed to remote repository but your web app not updated.`|추가 필수 모듈을 지정 하는 _패키지나 json_ 파일을 사용 하 여 node.js 앱을 배포 했습니다.|오류에 대 한 자세한 컨텍스트를 확인 하려면이 오류 이전에 `npm ERR!` 오류 메시지를 검토 하십시오. 다음은이 오류의 알려진 원인과 해당 `npm ERR!` 메시지입니다.<br /><br />**형식이 잘못 된 package. json 파일**: `npm ERR! Couldn't read dependencies.`<br /><br />**네이티브 모듈에는 Windows 용 이진 배포가**없습니다.<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />또는 <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>추가 리소스

- [프로젝트 Kudu 설명서](https://github.com/projectkudu/kudu/wiki)
- [Azure App Service에 대 한 연속 배포](deploy-continuous-deployment.md)
- [샘플: 웹 앱 만들기 및 로컬 Git 리포지토리의 코드 배포 (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [샘플: 웹 앱 만들기 및 로컬 Git 리포지토리의 코드 배포 (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
