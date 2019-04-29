---
title: (사용되지 않음) Azure Container Service 및 Swarm을 사용한 CI/CD
description: Docker Swarm, Azure Container Registry 및 Azure DevOps와 Azure Container Service를 사용하여 다중 컨테이너 .NET Core 애플리케이션 지속 제공
services: container-service
author: jcorioland
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: f28ea3dd2837a241c538057bd118409d4f5b858a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60643769"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>(사용되지 않음) Azure DevOps Services를 사용하여 Docker Swarm을 포함한 Azure Container Service에 있는 다중 컨테이너 애플리케이션을 배포하는 전체 CI/CD 파이프라인

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

클라우드를 위한 최신 애플리케이션을 개발할 때 어려운 문제 중 하나는 이러한 애플리케이션을 지속적으로 전달할 수 있다는 점입니다. 이 문서에서는 Docker Swarm, Azure Container Registry, Azure Pipelines 관리와 함께 Azure Container Service를 사용하여 전체 CI/CD(연속 통합 및 배포) 파이프라인을 구현하는 방법에 대해 알아봅니다.

이 문서는 간단한 애플리케이션을 기반으로 [GitHub](https://github.com/jcorioland/MyShop/tree/acs-docs)에서 사용할 수 있으며 ASP.NET Core를 사용하여 전개됩니다. 애플리케이션은 세 개의 웹 API 및 하나의 웹 프론트 엔드라는 네 개의 다른 서비스로 구성되어 있습니다.

![MyShop 샘플 애플리케이션](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Azure DevOps Services를 사용하여 Docker Swarm 클러스터에서 이 애플리케이션을 지속적으로 제공하려고 합니다. 다음 그림에서는 이 연속 배달 파이프라인을 자세히 설명합니다.

![MyShop 샘플 애플리케이션](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

여기에서는 단계에 대해 간략히 설명합니다.

1. 코드 변경 내용을 소스 코드 리포지토리로 커밋합니다(여기에서는 GitHub). 
1. GitHub가 Azure DevOps Services 내의 빌드를 트리거합니다. 
1. Azure DevOps Services는 최신 버전의 원본을 가져오고 애플리케이션을 작성하는 모든 이미지를 빌드합니다. 
1. Azure DevOps Services는 Azure Container Registry 서비스를 사용하여 만든 Docker 레지스트리에 이미지를 각각 푸시합니다. 
1. Azure DevOps Services가 새 릴리스를 트리거합니다. 
1. 릴리스는 Azure Container Service 클러스터 노드에서 SSH를 사용하는 마스터 명령을 실행합니다. 
1. 클러스터의 Docker Swarm은 이미지의 최신 버전을 가져옵니다. 
1. Docker 작성을 사용하여 새 버전의 애플리케이션을 배포합니다. 

## <a name="prerequisites"></a>필수 조건

이 자습서를 시작하기 전에 다음 작업을 완료해야 합니다.

- [Azure 컨테이너 서비스에서 Swarm 클러스터 만들기](container-service-deployment.md)
- [Azure 컨테이너 서비스에서 Swarm 클러스터에 연결](../container-service-connect.md)
- [Azure 컨테이너 레지스트리 만들기](../../container-registry/container-registry-get-started-portal.md)
- [Azure DevOps Services 조직 및 프로젝트 만들기](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [GitHub 계정에 GitHub 리포지토리 포크](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

또한 Docker를 설치한 Ubuntu 14.04 또는 16.04 컴퓨터가 필요합니다. 이 컴퓨터는 Azure Pipelines 프로세스 도중에 Azure DevOps Services에서 사용합니다. 이 컴퓨터를 만드는 방법은 [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/canonicalandmsopentech/dockeronubuntuserver1404lts/)에서 사용할 수 있는 이미지를 사용하는 것입니다. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>1단계: Azure DevOps Services 조직 구성 

이 섹션에서는 Azure DevOps Services 조직을 구성합니다.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Azure DevOps Services Linux 빌드 에이전트 구성

Docker 이미지를 만들고 Azure DevOps Services 빌드에서 Azure Container Registry에 이러한 이미지를 푸시하려면 Linux 에이전트를 등록해야 합니다. 다음과 같은 설치 옵션이 있습니다.

* [Linux에서 에이전트 배포](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Docker를 사용하여 Azure DevOps Services 에이전트 실행](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Docker 통합 Azure DevOps Services 확장 프로그램 설치

Microsoft는 Azure Pipelines 프로세스에서 Docker 작업을 위한 Azure DevOps Services 확장 프로그램을 제공합니다. [Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker)에서 이 확장 프로그램을 사용할 수 있습니다. 이 확장 프로그램을 Azure DevOps Services 조직에 추가하려면 **설치**를 클릭하세요.

![Docker 통합 설치](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

자격 증명을 사용하여 Azure DevOps Services 조직에 연결하라는 메시지가 표시됩니다. 

### <a name="connect-azure-devops-services-and-github"></a>Azure DevOps Services와 GitHub 연결

Azure DevOps Services 프로젝트와 GitHub 계정 간에 연결을 설정합니다.

1. Azure DevOps Services 프로젝트의 도구 모음에서 **설정** 아이콘을 클릭하고 **서비스**를 선택합니다.

    ![Azure DevOps Services - 외부 연결](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. 왼쪽에서 **새 서비스 엔드포인트** > **GitHub**를 클릭합니다.

    ![Azure DevOps Services - GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Azure DevOps Services를 인증하여 GitHub 계정으로 작업하려면 **인증**을 클릭하고 열린 창에서 절차를 따릅니다.

    ![Azure DevOps Services - GitHub 인증](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Azure Container Registry 및 Azure Container Service 클러스터에 Azure DevOps Services 연결

CI/CD 파이프라인에 도달하기 전에 Azure의 컨테이너 레지스트리 및 Docker Swarm 클러스터에 대한 외부 연결을 구성하는 마지막 단계가 있습니다. 

1. Azure DevOps Services 프로젝트의 **서비스** 설정에서 **Docker 레지스트리** 유형의 서비스 엔드포인트를 추가합니다. 

1. 열린 팝업에서 Azure 컨테이너 레지스트리의 URL 및 자격 증명을 입력합니다.

    ![Azure DevOps Services - Docker 레지스트리](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. Docker Swarm 클러스터의 경우 **SSH** 형식의 엔드포인트를 추가합니다. 그런 다음 Swarm 클러스터의 SSH 연결 정보를 입력합니다.

    ![Azure DevOps Services - SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

이제 모든 구성을 수행했습니다. 다음 단계에서는 Docker Swarm 클러스터에 애플리케이션을 빌드하고 배포하는 CI/CD 파이프라인을 만듭니다. 

## <a name="step-2-create-the-build-pipeline"></a>2단계: 빌드 파이프라인 만들기

이 단계에서는 Azure DevOps Services 프로젝트의 빌드 파이프라인을 설정하고 사용자 컨테이너 이미지에 대한 빌드 워크플로를 정의합니다.

### <a name="initial-pipeline-setup"></a>초기 파이프라인 설정

1. 빌드 파이프라인을 만들려면 Azure DevOps Services 프로젝트에 연결하고 **빌드 및 릴리스**를 클릭합니다. 

1. **빌드 정의** 섹션에서 **+ 새로 만들기**를 클릭합니다. **비어 있는** 템플릿을 선택합니다.

    ![Azure DevOps - 새 빌드 파이프라인](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. GitHub 리포지토리 원본을 사용하여 새 빌드를 구성하고 **연속 통합**을 확인한 다음 Linux 에이전트를 등록한 에이전트 큐를 선택합니다. **만들기**를 클릭하여 빌드 파이프라인을 만듭니다.

    ![Azure DevOps Services - 빌드 파이프라인 만들기](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. **빌드 정의** 페이지에서 먼저 **리포지토리** 탭을 열고 빌드를 구성하여 필수 구성 요소에서 만든 MyShop 프로젝트의 포크를 사용합니다. *acs-docs*을 **기본 분기**로 선택해야 합니다.

    ![Azure DevOps Services - 빌드 리포지토리 구성](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. **트리거** 탭에서 각 커밋 후에 트리거될 빌드를 구성합니다. **연속 통합** 및 **Batch 변경 내용**을 선택합니다.

    ![Azure DevOps Services - 빌드 트리거 구성](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>빌드 워크플로 정의
다음 단계에서는 빌드 워크플로를 정의합니다. *MyShop* 애플리케이션에 대해 빌드되는 5개의 컨테이너 이미지가 있습니다. 각 이미지는 프로젝트 폴더에 있는 Dockerfile을 사용하여 빌드됩니다.

* ProductsApi
* Proxy
* RatingsApi
* RecommendationsApi
* ShopFront

각 이미지에 대한 두 가지 Docker 단계를 추가해야 합니다. 하나는 이미지를 빌드하고 다른 하나는 Azure 컨테이너 레지스트리에서 이미지를 푸시합니다. 

1. 빌드 워크플로에서 단계를 추가하려면 **+ 빌드 단계 추가**를 클릭하고 **Docker**를 선택합니다.

    ![Azure DevOps Services - 빌드 단계 추가](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. 각 이미지의 경우 `docker build` 명령을 사용하는 하나의 단계를 구성합니다.

    ![Azure DevOps Services - Docker 빌드](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    빌드 작업의 경우 **이미지 빌드** 작업인 Azure 컨테이너 레지스트리를 선택하고 각 이미지를 정의하는 Dockerfile을 선택합니다. **빌드 컨텍스트**를 Dockerfile 루트 디렉터리로 설정하고 **이미지 이름**을 정의합니다. 
    
    이전 화면에 표시된 대로 Azure 컨테이너 레지스트리의 URI로 이미지 이름을 시작합니다. (빌드 변수를 사용하여 이 예제의 빌드 식별자와 같은 이미지의 태그를 매개 변수화할 수 있습니다.)

1. 각 이미지의 경우 `docker push` 명령을 사용하는 두 번째 단계를 구성합니다.

    ![Azure DevOps Services - Docker Push](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    푸시 작업의 경우 **이미지 푸시** 작업인 Azure 컨테이너 레지스트리를 선택하고 이전 단계에서 기본 설치되어 있는 **이미지 이름**을 입력합니다.

1. 5개의 이미지 각각에 대한 빌드 및 푸시 단계를 구성한 후에 빌드 워크플로에서 두 개 이상의 단계를 추가합니다.

    a. bash 스크립트를 사용하여 docker-compose.yml 파일에서 *BuildNumber* 발생을 현재 빌드 ID로 바꾸는 명령줄 작업입니다. 자세한 내용은 다음과 같은 화면을 참조하세요.

    ![Azure DevOps Services - 작성 파일 업데이트](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. 릴리스에서 사용할 수 있도록 빌드 아티팩트인 업데이트된 작성 파일을 삭제하는 작업입니다. 자세한 내용은 다음과 같은 화면을 참조하세요.

    ![Azure DevOps Services - 작성 파일 게시](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. **저장**을 클릭하여 빌드 파이프라인의 이름을 지정합니다.

## <a name="step-3-create-the-release-pipeline"></a>3단계: 릴리스 파이프라인 만들기

Azure DevOps Services를 사용하면 [환경에서 릴리스를 관리](https://www.visualstudio.com/team-services/release-management/)할 수 있습니다. 연속 배포를 설정하여 사용자의 애플리케이션이 다른 환경(예: 개발, 테스트, 프로덕션 전 및 프로덕션)에서 원활하게 배포되고 있는지 확인할 수 있습니다. Azure Container Service Docker Swarm 클러스터를 나타내는 새 환경을 만들 수 있습니다.

![Azure DevOps Services - ACS에 릴리스](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>최초 릴리스 설정

1. 릴리스 파이프라인을 만들려면 **릴리스** > **+ 릴리스**를 클릭합니다.

1. 아티팩트 원본을 구성하려면 **아티팩트** > **아티팩트 원본 연결**을 클릭합니다. 여기에서는 이전 단계에서 정의한 빌드에 이 새로운 릴리스 파이프라인을 연결합니다. 이 작업을 수행하여 docker-compose.yml 파일을 릴리스 프로세스에서 사용할 수 있습니다.

    ![Azure DevOps Services - 아티팩트 릴리스](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. 릴리스 트리거를 구성하려면 **트리거**를 클릭하고 **연속 배포**를 선택합니다. 동일한 아티팩트 원본에 트리거를 설정합니다. 이 설정을 통해 빌드가 성공적으로 완료되는 즉시 새 릴리스가 시작될 수 있습니다.

    ![Azure DevOps Services - 릴리스 트리거](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>릴리스 워크플로 정의

릴리스 워크플로는 추가한 두 가지 작업으로 구성됩니다.

1. 이전에 구성한 SSH 연결을 사용하여 Docker Swarm 마스터 노드의 *배포* 폴더에 작성 파일을 안전하게 복사하도록 작업을 구성합니다. 자세한 내용은 다음과 같은 화면을 참조하세요.

    ![Azure DevOps Services - 릴리스 SCP](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. bash 명령을 실행하는 두 번째 작업을 구성하여 마스터 노드에서 `docker` 및 `docker-compose` 명령을 실행합니다. 자세한 내용은 다음과 같은 화면을 참조하세요.

    ![Azure DevOps Services - 릴리스 Bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    마스터에서 실행되는 명령은 Docker CLI 및 Docker 작성 CLI를 사용하여 다음 작업을 수행합니다.

   - Azure 컨테이너 레지스트리에 로그인(**변수** 탭에 정의된 세 개의 빌드 변수 사용)
   - Swarm 엔드포인트를 사용하는 **DOCKER_HOST** 변수 정의(:2375)
   - 이전 안전한 복사 작업에서 만들어지고 docker-compose.yml 파일을 포함하는 *배포* 폴더 탐색 
   - 새 이미지를 가져오고 서비스를 중지하며 제거하고 컨테이너를 만드는 `docker-compose` 명령을 실행합니다.

     >[!IMPORTANT]
     > 이전 화면에 표시된 대로 **STDERR에 실패** 확인란을 해제된 채로 둡니다. `docker-compose`이 표준 오류 출력에서 컨테이너가 중지 또는 삭제됩니다와 같은 여러 진단 메시지를 인쇄하기 때문에 중요한 설정입니다. 확인란을 선택하면 모든 작업이 정상적으로 작동하는 경우에도 Azure DevOps Services는 릴리스 중에 오류가 발생했다고 보고합니다.
     >
1. 이 새 릴리스 파이프라인을 저장합니다.


>[!NOTE]
>이전 서비스를 중지하고 새 서비스를 실행하기 때문에 이 배포에는 가동 중지 시간이 포함됩니다. 청록색 배포를 수행하여 이 문제를 방지할 수 있습니다.
>

## <a name="step-4-test-the-cicd-pipeline"></a>4단계. CI/CD 파이프라인 테스트

이제 구성했으면 이 새 CI/CD 파이프라인을 테스트하겠습니다. 테스트하는 가장 쉬운 방법은 소스 코드를 업데이트하고 GitHub 리포지토리에 변경 내용을 커밋하는 것입니다. 코드를 푸시한 몇 초 후에 새 빌드가 Azure DevOps Services에서 실행되는 것을 볼 수 있습니다. 성공적으로 완료되면 새 릴리스가 트리거되고 Azure Container Service 클러스터에서 새 버전의 애플리케이션을 배포합니다.

## <a name="next-steps"></a>다음 단계

* Azure DevOps Services의 CI/CD에 대한 자세한 내용은 [Azure DevOps Services Build overview](https://www.visualstudio.com/docs/build/overview)(Azure DevOps Services 빌드 개요)를 참조하세요.
