---
title: ZIP 또는 WAR 파일을 사용 하 여 코드 배포
description: ZIP 파일(또는 Java 개발자의 경우 WAR 파일)을 사용하여 Azure App Service에 앱을 배포하는 방법을 알아봅니다.
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: e2959403d2e5db38d03013e798fe299d56837227
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962114"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>ZIP 또는 WAR 파일을 사용하여 Azure App Service에 앱 배포

이 문서는 ZIP 파일 또는 WAR 파일을 사용하여 웹앱을 [Azure App Service](overview.md)에 배포하는 방법을 보여 줍니다. 

이 ZIP 파일 배포는 지속적인 통합 기반 배포를 지원하는 동일한 Kudu 서비스를 사용합니다. Kudu는 ZIP 파일 배포에 대해 다음 기능을 지원합니다. 

- 이전 배포의 남은 파일 삭제.
- 패키지 복원을 포함하는 기본 빌드 프로세스를 켜는 옵션.
- 배포 스크립트 실행을 포함한 배포 사용자 지정.  
- 배포 로그. 
- 파일 크기 제한은 2048 MB입니다.

자세한 내용은 [Kudu 설명서](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)를 참조하세요.

WAR 파일 배포를 사용하면 [WAR](https://wikipedia.org/wiki/WAR_(file_format)) 파일이 App Service에 배포되고 Java 웹앱이 실행됩니다. [WAR 파일 배포](#deploy-war-file)를 참조하세요.

> [!NOTE]
> 를 사용 하는 경우 `ZipDeploy` 타임 스탬프가 이미 배포 된 것과 일치 하지 않는 경우에만 파일이 복사 됩니다. 출력을 캐시 하는 빌드 프로세스를 사용 하 여 zip을 생성 하면 배포 속도가 빨라질 수 있습니다. 자세한 내용은 [zip 파일이 나 url에서 배포](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 단계를 완료 하려면 [App Service 앱을 만들거나](./index.yml)다른 자습서에 대해 만든 앱을 사용 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
지금은 위의 끝점이 Linux App Services에 대해 작동 하지 않습니다. 대신 FTP 또는 [ZIP 배포 API](faq-app-service-linux.md#continuous-integration-and-deployment) 를 사용 하는 것이 좋습니다.

## <a name="deploy-zip-file-with-azure-cli"></a>Azure CLI를 사용하여 ZIP 파일 배포

[az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip) 명령을 사용하여 업로드된 ZIP 파일을 웹앱에 배포합니다.  

다음 예제에서는 업로드한 ZIP 파일을 배포합니다. Azure CLI의 로컬 설치를 사용할 경우 `--src`에 대해 로컬 ZIP 파일 경로를 지정합니다.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

이 명령은 ZIP 파일의 파일과 디렉터리를 기본 App Service 애플리케이션 폴더(`\home\site\wwwroot`)에 배포하고 앱을 다시 시작합니다.

기본적으로 배포 엔진은 ZIP 파일을 있는 그대로 실행할 준비가 된 것으로 가정 하 고 빌드 자동화를 실행 하지 않습니다. [Git 배포](deploy-local-git.md)와 동일한 빌드 자동화를 사용 하도록 설정 하려면 `SCM_DO_BUILD_DURING_DEPLOYMENT` [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행 하 여 앱 설정을 설정 합니다.

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

자세한 내용은 [Kudu 설명서](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url)를 참조하세요.

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>WAR 파일 배포

WAR 파일을 App Service에 배포 하려면 POST 요청을로 보냅니다 `https://<app-name>.scm.azurewebsites.net/api/wardeploy` . POST 요청은 메시지 본문에 .war 파일을 포함해야 합니다. 앱에 대한 배포 자격 증명은 HTTP 기본 인증을 사용하여 요청으로 제공됩니다.

`/api/wardeploy`WAR 파일을 배포할 때는 항상를 사용 합니다. 이 API는 WAR 파일을 확장 하 여 공유 파일 드라이브에 저장 합니다. 다른 배포 Api를 사용 하면 일관 되지 않은 동작이 발생할 수 있습니다. 

HTTP BASIC 인증의 경우 App Service 배포 자격 증명이 필요합니다. 배포 자격 증명을 설정하는 방법을 알아보려면 [사용자 수준 자격 증명 설정 및 다시 설정](deploy-configure-credentials.md#userscope)을 참조하세요.

### <a name="with-curl"></a>cURL 사용

다음 예제에서는 cURL 도구를 사용하여 .war 파일을 배포합니다. `<username>`, `<war-file-path>` 및 `<app-name>` 자리 표시자를 바꿉니다. cURL에서 프롬프트가 표시되면 암호를 입력합니다.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>PowerShell 사용

다음 예제에서는 [AzWebapp](/powershell/module/az.websites/publish-azwebapp) 를 사용 하 여 war 파일을 업로드 합니다. `<group-name>`, `<app-name>` 및 `<war-file-path>` 자리 표시자를 바꿉니다.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>다음 단계

고급 배포 시나리오에 대해서는 [Git를 사용하여 Azure에 배포](deploy-local-git.md)를 시도하세요. Azure로의 Git 기반 배포를 수행하면 버전 제어, 패키지 복원, MSBuild 등을 수행할 수 있습니다.

## <a name="more-resources"></a>기타 참고 자료

* [Kudu: zip 파일에서 배포](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file)
* [Azure App Service 배포 자격 증명](deploy-ftp.md)