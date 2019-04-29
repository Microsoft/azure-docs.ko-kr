---
title: 지속적인 배포 - Azure App Service | Microsoft Docs
description: Azure App Service에 연속 배포를 활성화하는 방법에 대해 알아봅니다.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2018
ms.author: cephalin;dariagrigoriu
ms.custom: seodec18
ms.openlocfilehash: fcb2c270b36d5efbe7b799787cf2a123b51bea5c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765709"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service에 지속적인 배포
이 문서에서는 [Azure App Service](overview.md)에 대해 지속적인 배포를 구성하는 방법을 보여 줍니다. App Service는 BitBucket, GitHub 및 [Azure DevOps Services](https://www.visualstudio.com/team-services/) 중 하나의 기존 리포지토리에서 최신 업데이트를 가져와서 이러한 서비스에서 지속적인 배포를 활성화합니다.

Azure Portal에 의해 나열되지 않은 클라우드 리포지토리에서 수동으로 지속적인 배포를 구성하는 방법을 확인하려면(예: [GitLab](https://gitlab.com/)), [수동 단계를 사용하여 지속적인 배포 설정](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)을 참조하세요.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

지원되는 서비스 중 하나에 준비된 리포지토리를 게시합니다. 이러한 서비스에 프로젝트를 게시하는 것에 대한 자세한 내용은 [리포지토리 만들기(GitHub)], [리포지토리 만들기(BitBucket)] 및 [Azure DevOps Services 시작]을 참조하세요.

## <a name="deploy-continuously-from-github"></a>GitHub에서 지속적으로 배포

GitHub를 통해 지속적인 배포를 사용하도록 설정하려면 [Azure Portal](https://portal.azure.com)에서 App Service 앱 페이지로 이동합니다.

왼쪽 메뉴에서 **배포 센터** > **GitHub** > **인증**을 클릭합니다. 표시되는 인증 메시지를 따릅니다. 

![](media/app-service-continuous-deployment/github-choose-source.png)

한 번 GitHub로 인증해야 합니다. 이미 인증된 경우 **계속**을 클릭합니다. **계정 변경**을 클릭하여 인증된 GitHub 계정을 변경할 수 있습니다.

![](media/app-service-continuous-deployment/github-continue.png)

**빌드 공급자** 페이지에서 빌드 공급자를 선택하고 **계속**을 클릭합니다.

### <a name="option-1-use-app-service-kudu-build-server"></a>옵션 1: App Service Kudu 빌드 서버 사용

**구성** 페이지에서 지속적으로 배포하려는 조직, 리포지토리 및 분기를 선택합니다. 작업을 마쳤으면 **계속**을 클릭합니다.

GitHub 조직에서 리포지토리의 배포 하려면 GitHub로 이동 하 고 이동할 **설정을** > **응용 프로그램** > **권한이 부여 된 OAuth 앱**. "Azure App Service"를 클릭 합니다.

![설정 > 응용 프로그램 > 권한 있는 OAuth 앱 > Azure App Service](media/app-service-continuous-deployment/github-settings-navigation.png)

다음 페이지의 오른쪽에 있는 "권한 부여" 단추를 클릭 하 여 조직의 리포지토리에 앱 서비스 액세스를 부여 합니다.

![조직의 리포지토리에 앱 서비스 액세스 권한을 부여 하려면 "권한 부여"를 클릭 합니다.](media/app-service-continuous-deployment/grant-access.png)

이제 조직에서 "조직" 목록에 표시 합니다 **구성** 배포 센터 페이지입니다.

### <a name="option-2-use-azure-pipelines-preview"></a>옵션 2: Azure Pipelines(미리 보기) 사용

> [!NOTE]
> App Service가 Azure DevOps Services 조직에서 필요한 Azure Pipelines를 만들려면 사용자의 Azure 계정에 Azure 구독의 **소유자**의 역할이 있어야 합니다.
>

**구성** 페이지의 **코드** 섹션에서 지속적으로 배포하려는 조직, 리포지토리 및 분기를 선택합니다. 작업을 마쳤으면 **계속**을 클릭합니다.

**구성** 페이지의 **빌드** 섹션에서 새 Azure DevOps Services 조직을 구성하거나 기존 조직을 지정합니다. 작업을 마쳤으면 **계속**을 클릭합니다.

> [!NOTE]
> 나열되지 않은 기존 Azure DevOps Services 조직을 사용하려면 [Azure DevOps Services 조직을 Azure 구독에 연결](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)해야 합니다.

**테스트** 페이지에서 부하 테스트를 사용하도록 설정한 다음, **계속**을 클릭합니다.

App Service 계획의 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/plans/)에 따라 **스테이징에 배포** 페이지가 표시될 수 있습니다. [배포 슬롯을 활성화](deploy-staging-slots.md)할지 여부를 선택한 다음, **계속**을 클릭합니다.

### <a name="finish-configuration"></a>구성 완료

**요약** 페이지에서 옵션을 확인하고 **마침**을 클릭합니다.

구성이 완료되면 선택한 리포지토리에 새 커밋이 App Service 앱에 지속적으로 배포됩니다.

![](media/app-service-continuous-deployment/github-finished.png)

## <a name="deploy-continuously-from-bitbucket"></a>BitBucket에서 지속적으로 배포

BitBucket을 통해 지속적인 배포를 사용하도록 설정하려면 [Azure Portal](https://portal.azure.com)에서 App Service 앱 페이지로 이동합니다.

왼쪽 메뉴에서 **배포 센터** > **BitBucket** > **인증**을 클릭합니다. 표시되는 인증 메시지를 따릅니다. 

![](media/app-service-continuous-deployment/bitbucket-choose-source.png)

한 번 BitBucket으로 인증해야 합니다. 이미 인증된 경우 **계속**을 클릭합니다. **계정 변경**을 클릭하여 인증된 BitBucket 계정을 변경할 수 있습니다.

![](media/app-service-continuous-deployment/bitbucket-continue.png)

**구성** 페이지에서 지속적으로 배포하려는 리포지토리 및 분기를 선택합니다. 작업을 마쳤으면 **계속**을 클릭합니다.

**요약** 페이지에서 옵션을 확인하고 **마침**을 클릭합니다.

구성이 완료되면 선택한 리포지토리에 새 커밋이 App Service 앱에 지속적으로 배포됩니다.

## <a name="deploy-continuously-from-azure-repos-devops-services"></a>Azure Repos(DevOps Services)에서 지속적으로 배포

[Azure Repos](https://docs.microsoft.com/azure/devops/repos/index)에서 지속적인 배포를 사용하도록 설정하려면 [Azure Portal](https://portal.azure.com)의 App Service 앱 페이지로 이동합니다.

왼쪽 메뉴에서 **배포 센터** > **Azure Repos** > **계속**을 클릭합니다. 

![](media/app-service-continuous-deployment/vsts-choose-source.png)

**빌드 공급자** 페이지에서 빌드 공급자를 선택하고 **계속**을 클릭합니다.

> [!NOTE]
> 나열되지 않은 기존 Azure DevOps Services 조직을 사용하려면 [Azure DevOps Services 조직을 Azure 구독에 연결](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App)해야 합니다.

### <a name="option-1-use-app-service-kudu-build-server"></a>옵션 1: App Service Kudu 빌드 서버 사용

**구성** 페이지에서 지속적으로 배포하려는 Azure DevOps Services 조직, 프로젝트, 리포지토리 및 분기를 선택합니다. 작업을 마쳤으면 **계속**을 클릭합니다.

### <a name="option-2-use-azure-devops-services-continuous-delivery"></a>옵션 2: Azure DevOps Services 지속적인 업데이트 사용

> [!NOTE]
> App Service가 Azure DevOps Services 조직에서 필요한 Azure Pipelines를 만들려면 사용자의 Azure 계정에 Azure 구독의 **소유자**의 역할이 있어야 합니다.
>

**구성** 페이지의 **코드** 섹션에서 지속적으로 배포하려는 Azure DevOps Services 조직, 프로젝트, 리포지토리 및 분기를 선택합니다. 작업을 마쳤으면 **계속**을 클릭합니다.

**구성** 페이지의 **빌드** 섹션에서 선택한 리포지토리에 대한 빌드 작업을 실행하는 데 Azure DevOps Services에서 사용해야 하는 언어 프레임워크를 지정합니다. 작업을 마쳤으면 **계속**을 클릭합니다.

**테스트** 페이지에서 부하 테스트를 사용하도록 설정한 다음, **계속**을 클릭합니다.

App Service 계획의 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/plans/)에 따라 **스테이징에 배포** 페이지가 표시될 수 있습니다. [배포 슬롯을 활성화](deploy-staging-slots.md)할지 여부를 선택한 다음, **계속**을 클릭합니다. 

### <a name="finish-configuration"></a>구성 완료

**요약** 페이지에서 옵션을 확인하고 **마침**을 클릭합니다.

구성이 완료되면 선택한 리포지토리에 새 커밋이 App Service 앱에 지속적으로 배포됩니다.

## <a name="disable-continuous-deployment"></a>지속적 배포 사용 안 함

지속적인 배포를 사용하지 않도록 설정하려면 [Azure Portal](https://portal.azure.com)에서 App Service 앱 페이지로 이동합니다.

왼쪽 메뉴에서 **배포 센터** > **GitHub** 또는 **Azure DevOps Services** 또는 **BitBucket** > **연결 해제**를 클릭합니다.

![](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="additional-resources"></a>추가 리소스

* [연속 배포의 일반 문제를 조사하는 방법](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure용 PowerShell 사용 방법]
* [Git 설명서]
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)
* [Use Azure to automatically generate a CI/CD pipeline to deploy an ASP.NET 4 app](https://www.visualstudio.com/docs/build/get-started/aspnet-4-ci-cd-azure-automatic)(Azure를 사용하여 ASP.NET 4 앱을 배포할 CI/CD 파이프라인을 자동으로 생성)

[Azure portal]: https://portal.azure.com
[Azure DevOps portal]: https://azure.microsoft.com/services/devops/
[Installing Git]: https://git-scm.com/book/en/Getting-Started-Installing-Git
[Azure용 PowerShell 사용 방법]: /powershell/azureps-cmdlets-docs
[Git 설명서]: https://git-scm.com/documentation

[리포지토리 만들기(GitHub)]: https://help.github.com/articles/create-a-repo
[리포지토리 만들기(BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[Azure DevOps Services 시작]: https://docs.microsoft.com/azure/devops/user-guide/devops-alm-overview
