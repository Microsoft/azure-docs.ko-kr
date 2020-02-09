---
title: 지속적 배포 구성
description: GitHub, BitBucket, Azure Repos 또는 기타 리포지토리에서 Azure App Service 하는 CI/CD를 사용 하도록 설정 하는 방법에 대해 알아봅니다. 요구 사항에 맞는 빌드 파이프라인을 선택 합니다.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 08/23/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: d58eb333c930d2ffac4eb57340ea776338325181
ms.sourcegitcommit: a100e3d8b0697768e15cbec11242e3f4b0e156d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2020
ms.locfileid: "75680983"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Azure App Service에 지속적인 배포

[Azure App Service](overview.md) 는 최신 업데이트를 끌어와 GitHub, BitBucket 및 [Azure Repos](https://azure.microsoft.com/services/devops/repos/) 리포지토리에서 지속적인 배포를 가능 하 게 합니다. 이 문서에서는 Azure Portal를 사용 하 여 Kudu 빌드 서비스 또는 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)를 통해 앱을 지속적으로 배포 하는 방법을 보여 줍니다. 

원본 제어 서비스에 대한 자세한 내용은 [리포지토리 만들기(GitHub)], [리포지토리 만들기(BitBucket)]또는 [새 Git 리포지토리 만들기 (Azure Repos)]를 참조 하세요.

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>권한 부여 Azure App Service 

Azure Repos를 사용 하려면 Azure DevOps 조직이 Azure 구독에 연결 되어 있는지 확인 합니다. 자세한 내용은 [웹 앱에 배포할 수 있도록 Azure DevOps Services 계정 설정](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)을 참조 하세요.

Bitbucket 또는 GitHub의 경우 리포지토리에 연결할 Azure App Service 권한을 부여 합니다. 소스 제어 서비스를 한 번만 사용 하 여 권한을 부여 해야 합니다. 

1. [Azure Portal](https://portal.azure.com)에서 **App Services**를 검색 하 고 선택 합니다. 

   ![App services를 검색 합니다.](media/app-service-continuous-deployment/search-for-app-services.png)

1. 배포 하려는 웹 앱을 선택 합니다.

   ![앱을 선택 합니다.](media/app-service-continuous-deployment/select-your-app.png)
   
1. 앱 페이지의 왼쪽 메뉴에서 **Deployment Center** 를 선택 합니다.
   
1. **Deployment Center** 페이지에서 **GitHub** 또는 **Bitbucket**를 선택 하 고 **권한 부여**를 선택 합니다. 
   
   ![원본 제어 서비스를 선택 하 고 권한 부여를 선택 합니다.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. 필요한 경우 서비스에 로그인 하 고 인증 프롬프트를 따릅니다. 

## <a name="enable-continuous-deployment"></a>연속 배포 사용 

원본 제어 서비스를 인증 한 후에는 기본 제공 [Kudu App Service 빌드 서버](#option-1-use-the-app-service-build-service)또는 [Azure Pipelines](#option-2-use-azure-pipelines)를 통해 연속 배포에 대해 앱을 구성 합니다. 

### <a name="option-1-use-the-app-service-build-service"></a>옵션 1: App Service 빌드 서비스 사용

기본 제공 Kudu App Service 빌드 서버를 사용 하 여 GitHub, Bitbucket 또는 Azure Repos에서 지속적으로 배포할 수 있습니다. 

1. [Azure Portal](https://portal.azure.com)에서 **App Services**를 검색 하 고 선택한 다음 배포 하려는 웹 앱을 선택 합니다. 
   
1. 앱 페이지의 왼쪽 메뉴에서 **Deployment Center** 를 선택 합니다.
   
1. **Deployment Center** 페이지에서 권한이 부여 된 원본 제어 공급자를 선택 하 고 **계속**을 선택 합니다. GitHub 또는 Bitbucket의 경우 **계정 변경** 을 선택 하 여 권한 있는 계정을 변경할 수도 있습니다. 
   
   > [!NOTE]
   > Azure Repos을 사용 하려면 Azure DevOps Services 조직이 Azure 구독에 연결 되어 있는지 확인 합니다. 자세한 내용은 [웹 앱에 배포할 수 있도록 Azure DevOps Services 계정 설정](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)을 참조 하세요.
   
1. GitHub 또는 Azure Repos의 경우 **빌드 공급자** 페이지에서 **App Service 빌드 서비스**를 선택 하 고 **계속**을 선택 합니다. Bitbucket는 항상 App Service 빌드 서비스를 사용 합니다.
   
   ![App Service 빌드 서비스를 선택 하 고 계속을 선택 합니다.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. **구성** 페이지에서 다음을 수행 합니다.
   
   - GitHub의 경우 계속 배포 하려는 **조직**, **리포지토리**및 **분기** 를 드롭다운 하 고 선택 합니다.
     
     > [!NOTE]
     > 리포지토리가 표시 되지 않으면 GitHub에서 Azure App Service 권한을 부여 해야 할 수 있습니다. GitHub 리포지토리로 이동 하 고 **설정** > **응용 프로그램** > 승인 된 **OAuth 앱**으로 이동 합니다. **Azure App Service**를 선택 하 고 **Grant**를 선택 합니다. 조직 리포지토리의 경우 사용 권한을 부여 하려면 조직의 소유자 여야 합니다.
     
   - Bitbucket의 경우 지속적으로 배포 하려는 Bitbucket **팀**, **리포지토리**및 **분기** 를 선택 합니다.
     
   - Azure Repos의 경우 지속적으로 배포 하려는 **Azure DevOps 조직**, **프로젝트**, **리포지토리**및 **분기** 를 선택 합니다.
     
     > [!NOTE]
     > Azure DevOps 조직이 나열 되지 않은 경우 Azure 구독에 연결 되어 있는지 확인 합니다. 자세한 내용은 [웹 앱에 배포할 수 있도록 Azure DevOps Services 계정 설정](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)을 참조 하세요.
     
1. **계속**을 선택합니다.
   
   ![리포지토리 정보를 입력 한 다음 계속을 선택 합니다.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. 빌드 공급자를 구성한 후 **요약** 페이지에서 설정을 검토 한 다음 **마침**을 선택 합니다.
   
   선택한 리포지토리 및 분기의 새 커밋이 이제 App Service 앱에 지속적으로 배포 됩니다. **배포 센터** 페이지에서 커밋 및 배포를 추적할 수 있습니다.
   
   ![배포 센터에서 커밋 및 배포 추적](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-use-azure-pipelines"></a>옵션 2: Azure Pipelines 사용 

계정에 필요한 권한이 있는 경우 GitHub 또는 Azure Repos 리포지토리에서 지속적으로 배포 하도록 Azure Pipelines를 설정할 수 있습니다. Azure Pipelines를 통해 배포 하는 방법에 대한 자세한 내용은 [Azure 앱 서비스에 웹 앱 배포](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)를 참조 하세요.

Azure DevOps 조직에서 지속적인 업데이트 Azure Pipelines를 만드는 Azure App Service: 

- Azure 계정에는 Azure Active Directory에 대한 쓰기 권한이 있어야 하 고 서비스를 만들 수 있습니다. 
  
- Azure 계정에는 Azure 구독에 **소유자** 역할이 있어야 합니다.

- 사용 하려는 Azure DevOps 프로젝트의 관리자 여야 합니다.

Azure Pipelines (미리 보기)를 구성 하려면:

1. [Azure Portal](https://portal.azure.com)에서 **App Services**를 검색 하 고 선택한 다음 배포 하려는 웹 앱을 선택 합니다. 
   
1. 앱 페이지의 왼쪽 메뉴에서 **Deployment Center** 를 선택 합니다.
   
1. **빌드 공급자** 페이지에서 **Azure Pipelines (미리 보기)** 를 선택 하 고 **계속**을 선택 합니다. 
   
1. **구성** 페이지의 **코드** 섹션에서 다음을 수행 합니다.
   
   - GitHub의 경우 계속 배포 하려는 **조직**, **리포지토리**및 **분기** 를 드롭다운 하 고 선택 합니다.
     
     > [!NOTE]
     > 리포지토리가 표시 되지 않으면 GitHub에서 Azure App Service 권한을 부여 해야 할 수 있습니다. GitHub 리포지토리로 이동 하 고 **설정** > **응용 프로그램** > 승인 된 **OAuth 앱**으로 이동 합니다. **Azure App Service**를 선택 하 고 **Grant**를 선택 합니다. 조직 리포지토리의 경우 사용 권한을 부여 하려면 조직의 소유자 여야 합니다.
     
   - Azure Repos의 경우 지속적으로 배포 하려는 **Azure Devops 조직**, **프로젝트**, **리포지토리**및 **분기** 를 선택 하거나 새 azure devops 조직을 구성 합니다.
     
     > [!NOTE]
     > 기존 Azure DevOps 조직이 나열 되지 않은 경우 Azure 구독에 연결 해야 할 수 있습니다. 자세한 내용은 [CD 릴리스 파이프라인 정의](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)를 참조 하세요.
     
1. **계속**을 선택합니다.
   
1. Azure Repos **빌드 섹션에서 빌드 작업** 을 실행 하는 데 사용 해야 Azure Pipelines 언어 프레임 워크를 지정 하 고 **계속**을 선택 합니다.
   
1. **테스트** 페이지에서 부하 테스트를 사용할지 여부를 선택 하 고 **계속**을 선택 합니다.
   
1. App Service 계획 [가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/plans/)에 따라 **스테이징에 배포** 페이지가 표시 될 수 있습니다. [배포 슬롯을 사용 하도록](deploy-staging-slots.md)설정할지 여부를 선택 하 고 **계속**을 선택 합니다.
   
   > [!NOTE]
   > Azure Pipelines 프로덕션 슬롯에 대한 지속적인 업데이트를 허용 하지 않습니다. 이러한 제한으로 인해 프로덕션에 대한 실수로 인 한 배포를 방지할 수 있습니다 스테이징 슬롯에 대한 지속적인 업데이트를 설정 하 고, 변경 내용을 확인 한 다음 준비가 되 면 슬롯을 교환 합니다.
   
1. 빌드 공급자를 구성한 후 **요약** 페이지에서 설정을 검토 한 다음 **마침**을 선택 합니다.
   
   선택한 리포지토리 및 분기의 새 커밋이 이제 App Service 앱에 지속적으로 배포 됩니다. **배포 센터** 페이지에서 커밋 및 배포를 추적할 수 있습니다.
   
   ![배포 센터에서 커밋 및 배포 추적](media/app-service-continuous-deployment/github-finished.png)

## <a name="disable-continuous-deployment"></a>지속적 배포 사용 안 함

연속 배포를 사용 하지 않도록 설정 하려면 앱의 **배포 센터** 페이지 위쪽에서 **연결 끊기** 를 선택 합니다.

![지속적 배포 사용 안 함](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>지원 되지 않는 리포지토리 사용

Windows 앱의 경우 포털이 직접 지원 하지 않는 클라우드 Git 또는 Mercurial 리포지토리에서 (예: [Gitlab](https://gitlab.com/)) 연속 배포를 수동으로 구성할 수 있습니다. 이렇게 하려면 **Deployment Center** 페이지에서 외부 상자를 선택 합니다. 자세한 내용은 [수동 단계를 사용 하 여 연속 배포 설정](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)을 참조 하세요.

## <a name="additional-resources"></a>추가 리소스

* [연속 배포에 대한 일반적인 문제 조사](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Azure PowerShell 사용](/powershell/azureps-cmdlets-docs)
* [Git 설명서](https://git-scm.com/documentation)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

[리포지토리 만들기(GitHub)]: https://help.github.com/articles/create-a-repo
[리포지토리 만들기(BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html
[새 Git 리포지토리 만들기 (Azure Repos)]: /azure/devops/repos/git/creatingrepo
