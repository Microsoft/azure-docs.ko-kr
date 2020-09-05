---
title: 배포 모범 사례
description: Azure App Service에 배포 하는 주요 메커니즘에 대해 알아봅니다. 언어별 권장 사항 및 기타 주의 사항을 찾습니다.
keywords: azure app service, 웹 앱, 배포, 배포, 파이프라인, 빌드
author: jasonfreeberg
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 07/31/2019
ms.author: jafreebe
ms.openlocfilehash: b4581b7e93cde9d6ba9a20d46ee263a879c05402
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88961876"
---
# <a name="deployment-best-practices"></a>배포 모범 사례

모든 개발 팀에는 클라우드 서비스에 대 한 효율적인 배포 파이프라인을 구현 하기 위한 고유한 요구 사항이 있습니다. 이 문서에서는 App Service에 배포 하는 세 가지 주요 구성 요소인 배포 원본, 빌드 파이프라인 및 배포 메커니즘을 소개 합니다. 이 문서에는 특정 언어 스택에 대 한 몇 가지 모범 사례 및 팁이 포함 되어 있습니다.

## <a name="deployment-components"></a>배포 구성 요소

### <a name="deployment-source"></a>배포 원본

배포 소스는 응용 프로그램 코드의 위치입니다. 프로덕션 앱의 경우 배포 원본은 일반적으로 [GitHub, BitBucket 또는 Azure Repos](deploy-continuous-deployment.md)와 같은 버전 제어 소프트웨어에서 호스트 되는 리포지토리입니다. 개발 및 테스트 시나리오의 경우 배포 원본은 [로컬 컴퓨터의 프로젝트](deploy-local-git.md)일 수 있습니다. App Service은 배포 원본으로 [OneDrive 및 Dropbox 폴더도](deploy-content-sync.md) 지원 합니다. 클라우드 폴더를 사용 하면 쉽게 App Service를 시작할 수 있지만, 일반적으로 엔터프라이즈 수준의 프로덕션 응용 프로그램에는이 원본을 사용 하지 않는 것이 좋습니다. 

### <a name="build-pipeline"></a>빌드 파이프라인

배포 소스를 결정 한 후 다음 단계는 빌드 파이프라인을 선택 하는 것입니다. 빌드 파이프라인은 배포 소스에서 소스 코드를 읽고 코드 컴파일, HTML 및 JavaScript 축소 테스트 실행, 구성 요소 패키징 등의 일련의 단계를 실행 하 여 응용 프로그램을 실행 가능한 상태로 가져옵니다. 빌드 파이프라인에 의해 실행 되는 특정 명령은 언어 스택에 따라 달라 집니다. 이러한 작업은 Azure Pipelines 같은 빌드 서버에서 실행 하거나 로컬로 실행할 수 있습니다.

### <a name="deployment-mechanism"></a>배포 메커니즘

배포 메커니즘은 웹 앱의 */home/site/wwwroot* 디렉터리에 빌드된 응용 프로그램을 배치 하는 데 사용 되는 작업입니다. */Wwwroot* 디렉터리는 웹 앱의 모든 인스턴스에서 공유 하는 탑재 된 저장소 위치입니다. 배포 메커니즘이 응용 프로그램을이 디렉터리에 배치 하면 인스턴스는 새 파일을 동기화 하는 알림을 받게 됩니다. App Service는 다음과 같은 배포 메커니즘을 지원 합니다.

- Kudu 끝점: [Kudu](https://github.com/projectkudu/kudu/wiki) 는 Windows App Service에서 별도의 프로세스로 실행 되는 오픈 소스 개발자 생산성 도구 이며 Linux App Service의 두 번째 컨테이너로 실행 됩니다. Kudu는 연속 배포를 처리 하 고 zipdeploy와 같은 배포에 대 한 HTTP 끝점을 제공 합니다.
- FTP 및 WebDeploy: [사이트 또는 사용자 자격 증명](deploy-configure-credentials.md)을 사용 하 여 Ftp 또는 webdeploy를 [통해](deploy-ftp.md) 파일을 업로드할 수 있습니다. 이러한 메커니즘은 Kudu를 거치지 않습니다.  

Azure Pipelines, Jenkins 및 편집기 플러그 인 같은 배포 도구는 이러한 배포 메커니즘 중 하나를 사용 합니다.

## <a name="use-deployment-slots"></a>배포 슬롯 사용

가능 하면 새 프로덕션 빌드를 배포할 때 [배포 슬롯](deploy-staging-slots.md) 을 사용 합니다. 표준 App Service 계획 계층 이상을 사용 하는 경우 스테이징 환경에 앱을 배포 하 고, 변경 내용의 유효성을 검사 하 고, 스모크 테스트를 수행할 수 있습니다. 준비가 되 면 스테이징 및 프로덕션 슬롯을 교환할 수 있습니다. 교환 작업을 수행 하면 프로덕션 규모와 일치 하는 데 필요한 작업자 인스턴스가 발생 하므로 가동 중지 시간을 없앨 수 있습니다.

### <a name="continuously-deploy-code"></a>코드를 지속적으로 배포

프로젝트에 테스트, QA 및 스테이징에 대 한 분기가 지정 된 경우 각 분기는 스테이징 슬롯에 지속적으로 배포 되어야 합니다. 이를 [Gitflow 디자인](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)이라고 합니다. 이를 통해 관련자는 배포 된 분기를 쉽게 평가 하 고 테스트할 수 있습니다. 

프로덕션 슬롯에 대해 연속 배포를 사용 하도록 설정 하면 안 됩니다. 대신 프로덕션 분기 (종종 마스터)를 비프로덕션 슬롯에 배포 해야 합니다. 기본 분기를 릴리스할 준비가 되 면 프로덕션 슬롯으로 바꿉니다. 프로덕션에 배포 하는 대신 프로덕션으로 교체 하면 가동 중지 시간을 방지 하 고 다시 교환 하 여 변경 내용을 롤백할 수 있습니다. 

![슬롯 사용 시각적 개체](media/app-service-deploy-best-practices/slot_flow_code_diagam.png)

### <a name="continuously-deploy-containers"></a>계속 컨테이너 배포

Docker 또는 다른 컨테이너 레지스트리의 사용자 지정 컨테이너에 대해 이미지를 스테이징 슬롯에 배포 하 고 프로덕션으로 교환 하 여 가동 중지 시간을 방지 합니다. 컨테이너 레지스트리에 이미지를 푸시하고 webapp에서 이미지 태그를 업데이트 해야 하기 때문에 자동화는 코드 배포 보다 더 복잡 합니다.

슬롯에 배포할 각 분기에 대해 분기에 대 한 각 커밋에서 다음을 수행 하도록 자동화를 설정 합니다.

1. **이미지를 빌드하고 태그를 태그**합니다. 빌드 파이프라인의 일부로 이미지에 git 커밋 ID, 타임 스탬프 또는 기타 식별 가능한 정보를 사용 하 여 태그를 만듭니다. 기본 "최신" 태그를 사용 하지 않는 것이 좋습니다. 그렇지 않으면 현재 배포 된 코드를 추적 하기가 어렵기 때문에 디버깅이 훨씬 더 어려워집니다.
1. **태그가 지정 된 이미지를 푸시합니다**. 이미지를 빌드하고 태그를 지정 하면 파이프라인에서 이미지를 컨테이너 레지스트리로 푸시합니다. 다음 단계에서 배포 슬롯은 컨테이너 레지스트리에서 태그가 지정 된 이미지를 가져옵니다.
1. **새 이미지 태그를 사용 하 여 배포 슬롯을 업데이트**합니다. 이 속성이 업데이트 되 면 사이트가 자동으로 다시 시작 되 고 새 컨테이너 이미지를 끌어옵니다.

![슬롯 사용 시각적 개체](media/app-service-deploy-best-practices/slot_flow_container_diagram.png)

일반적인 자동화 프레임 워크에 대 한 예제는 다음과 같습니다.

### <a name="use-azure-devops"></a>Azure DevOps 사용

App Service에는 배포 센터를 통해 컨테이너에 대 한 [지속적인 지속적인 배달이 제공](deploy-continuous-deployment.md) 됩니다. [Azure Portal](https://portal.azure.com/) 에서 앱으로 이동 하 고 **배포**아래에서 **deployment Center** 를 선택 합니다. 지침에 따라 리포지토리 및 분기를 선택 합니다. 그러면 새 커밋이 선택한 분기로 푸시 될 때 컨테이너를 자동으로 빌드, 태그 및 배포할 수 있도록 DevOps 빌드 및 릴리스 파이프라인이 구성 됩니다.

### <a name="use-github-actions"></a>GitHub 작업 사용

[GitHub 작업을 사용 하 여](deploy-container-github-action.md)컨테이너 배포를 자동화할 수도 있습니다.  아래 워크플로 파일은 커밋 ID를 사용 하 여 컨테이너를 빌드 및 태그 지정 하 고, 컨테이너 레지스트리에 푸시하고, 지정 된 사이트 슬롯을 새 이미지 태그로 업데이트 합니다.

```yaml
name: Build and deploy a container image to Azure Web Apps

on:
  push:
    branches:
    - <your-branch-name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@master

    -name: Authenticate using a Service Principal
      uses: azure/actions/login@v1
      with:
        creds: ${{ secrets.AZURE_SP }}

    - uses: azure/container-actions/docker-login@v1
      with:
        username: ${{ secrets.DOCKER_USERNAME }}
        password: ${{ secrets.DOCKER_PASSWORD }}

    - name: Build and push the image tagged with the git commit hash
      run: |
        docker build . -t contoso/demo:${{ github.sha }}
        docker push contoso/demo:${{ github.sha }}

    - name: Update image tag on the Azure Web App
      uses: azure/webapps-container-deploy@v1
      with:
        app-name: '<your-webapp-name>'
        slot-name: '<your-slot-name>'
        images: 'contoso/demo:${{ github.sha }}'
```

### <a name="use-other-automation-providers"></a>다른 자동화 공급자 사용

위에 나열 된 단계는 CircleCI 또는 Travis CI와 같은 다른 자동화 유틸리티에 적용 됩니다. 그러나 마지막 단계에서 새 이미지 태그로 배포 슬롯을 업데이트 하려면 Azure CLI를 사용 해야 합니다. 자동화 스크립트에서 Azure CLI를 사용 하려면 다음 명령을 사용 하 여 서비스 사용자를 생성 합니다.

```shell
az ad sp create-for-rbac --name "myServicePrincipal" --role contributor \
   --scopes /subscriptions/{subscription}/resourceGroups/{resource-group} \
   --sdk-auth
```

스크립트에서를 사용 하 여 로그인 하 `az login --service-principal` 고 보안 주체의 정보를 제공 합니다. 그런 다음를 사용 `az webapp config container set` 하 여 컨테이너 이름, 태그, 레지스트리 URL 및 레지스트리 암호를 설정할 수 있습니다. 컨테이너 CI 프로세스를 생성 하는 데 도움이 되는 몇 가지 유용한 링크는 다음과 같습니다.

- [Circle CI에 Azure CLI 로그인 하는 방법](https://circleci.com/orbs/registry/orb/circleci/azure-cli) 

## <a name="language-specific-considerations"></a>언어별 고려 사항

### <a name="java"></a>Java

Kudu [zipdeploy/](deploy-zip.md) API를 사용 하 여 JAR 응용 프로그램을 배포 하 고, WAR 응용 프로그램을 배포 [/](deploy-zip.md#deploy-war-file) 사용 합니다. Jenkins를 사용 하는 경우 배포 단계에서 직접 해당 Api를 사용할 수 있습니다. 자세한 내용은 [이 문서](/azure/developer/jenkins/deploy-to-azure-app-service-using-azure-cli)를 참조하세요.

### <a name="node"></a>노드

기본적으로 Kudu는 노드 응용 프로그램 ()에 대 한 빌드 단계를 실행 `npm install` 합니다. Azure DevOps와 같은 빌드 서비스를 사용 하는 경우 Kudu 빌드는 필요 하지 않습니다. Kudu 빌드를 사용 하지 않도록 설정 하려면 값이 인 앱 설정을 만듭니다 `SCM_DO_BUILD_DURING_DEPLOYMENT` `false` .

### <a name="net"></a>.NET 

기본적으로 Kudu는 .NET 응용 프로그램에 대 한 빌드 단계 ( `dotnet build` )를 실행 합니다. Azure DevOps와 같은 빌드 서비스를 사용 하는 경우 Kudu 빌드는 필요 하지 않습니다. Kudu 빌드를 사용 하지 않도록 설정 하려면 값이 인 앱 설정을 만듭니다 `SCM_DO_BUILD_DURING_DEPLOYMENT` `false` .

## <a name="other-deployment-considerations"></a>기타 배포 고려 사항

### <a name="local-cache"></a>로컬 캐시

Azure App Service의 콘텐츠는 Azure Storage에 저장되며 영구적 방식으로 콘텐츠 공유로 표시됩니다. 그러나 일부 앱은 고가용성으로 실행할 수 있는 고성능 읽기 전용 콘텐츠 저장소가 필요 합니다. 이러한 앱은 [로컬 캐시](overview-local-cache.md)를 사용 하는 이점을 누릴 수 있습니다. WordPress 등의 콘텐츠 관리 사이트에는 로컬 캐시를 사용할 수 없습니다.

가동 중지 시간을 방지 하려면 항상 [배포 슬롯과](deploy-staging-slots.md) 함께 로컬 캐시를 사용 합니다. 이러한 기능을 함께 사용 하는 방법에 대 한 자세한 내용은 [이 섹션](overview-local-cache.md#best-practices-for-using-app-service-local-cache) 을 참조 하세요.

### <a name="high-cpu-or-memory"></a>높은 CPU 또는 메모리

App Service 계획에서 사용 가능한 CPU 또는 메모리의 90%를 초과 하 여 사용 하는 경우 기본 가상 컴퓨터에서 배포를 처리 하는 데 문제가 있을 수 있습니다. 이 경우 배포를 수행 하기 위해 인스턴스 수를 일시적으로 확장 합니다. 배포가 완료 되 면 인스턴스 수를 이전 값으로 반환할 수 있습니다.

모범 사례에 대 한 자세한 내용은 [App Service 진단을](./overview-diagnostics.md) 방문 하 여 리소스와 관련 하 여 조치 가능한 모범 사례를 확인 하세요.

- [Azure Portal](https://portal.azure.com)에서 웹 앱으로 이동 합니다.
- 왼쪽 탐색에서 **문제 진단 및 해결** 을 클릭 하면 App Service 진단이 열립니다.
- **모범 사례** 홈 페이지 타일을 선택 합니다.
- 모범 사례에 **대 한 모범 사례** 를 클릭 하 여 모범 사례에 대 한 응용 프로그램의 현재 상태를 확인 하는 **최적의 구성을 위한** 모범 사례를 & 합니다.

또한이 링크를 사용 하 여 리소스에 대 한 App Service 진단을 직접 열 수 있습니다 `https://ms.portal.azure.com/?websitesextension_ext=asd.featurePath%3Ddetectors%2FParentAvailabilityAndPerformance#@microsoft.onmicrosoft.com/resource/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/sites/{siteName}/troubleshoot` .