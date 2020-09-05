---
title: '자습서: Azure App Service에서 사용자 지정 이미지 빌드 및 실행'
description: 사용자 지정 Linux 또는 Windows 이미지를 빌드하고 이미지를 Azure Container Registry로 푸시한 다음, 해당 이미지를 Azure App Service에 배포하는 단계별 가이드입니다. 사용자 지정 소프트웨어를 사용자 지정 컨테이너에 있는 App Service로 마이그레이션하는 방법에 대해 알아봅니다.
ms.topic: tutorial
ms.date: 07/16/2020
ms.author: msangapu
keywords: azure app service, 웹앱, linux, windows, docker, 컨테이너
ms.custom: devx-track-csharp, mvc, seodec18, devx-track-python
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: df46d61ddfba5f4da977b19db3158691c78168f8
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958476"
---
# <a name="migrate-custom-software-to-azure-app-service-using-a-custom-container"></a>사용자 지정 컨테이너를 사용하여 사용자 지정 소프트웨어를 Azure App Service로 마이그레이션

::: zone pivot="container-windows"  

[Azure App Service](overview.md)는 IIS에서 실행하는 ASP.NET 또는 Node.js와 같은 Windows에서 미리 정의된 애플리케이션 스택을 제공합니다. 미리 구성된 Windows 환경은 관리자 액세스의 운영 체제, 소프트웨어 설치, 글로벌 어셈블리 캐시 변경 내용을 잠급니다([Azure App Service의 운영 체제 기능](operating-system-functionality.md) 참조). 하지만 App Service(미리 보기)에서 사용자 지정 Windows 컨테이너를 사용하면 앱에 필요한 OS 변경이 가능합니다. 따라서 사용자 지정 OS 및 소프트웨어 구성이 필요한 온-프레미스 앱을 쉽게 마이그레이션할 수 있습니다. 이 자습서에서는 Windows 글꼴 라이브러리에 설치된 사용자 지정 글꼴을 사용하는 ASP.NET 앱을 App Service로 마이그레이션하는 방법을 보여줍니다. 사용자 지정 구성된 Windows 이미지를 Visual Studio에서 [Azure Container Registry](../container-registry/index.yml)로 배포한 다음, App Service에서 실행합니다.

![Windows 컨테이너에서 실행 중인 웹앱을 표시합니다.](media/tutorial-custom-container/app-running.png)

## <a name="prerequisites"></a>필수 구성 요소

이 자습서를 완료하려면 다음이 필요합니다.

- <a href="https://hub.docker.com/" target="_blank">Docker 허브 계정 등록</a>
- <a href="https://docs.docker.com/docker-for-windows/install/" target="_blank">Windows용 Docker 설치</a>
- <a href="https://docs.microsoft.com/virtualization/windowscontainers/quick-start/quick-start-windows-10" target="_blank">Windows 컨테이너를 실행하도록 Docker 전환</a>
- **ASP.NET과 웹 개발** 및 **Azure 개발** 워크로드와 함께 <a href="https://www.visualstudio.com/downloads/" target="_blank">Visual Studio 2019 설치</a> Visual Studio 2019를 이미 설치한 경우:
    - **도움말** > **업데이트 확인**을 클릭하여 Visual Studio에 최신 업데이트를 설치합니다.
    - **도구** > **도구 및 기능 가져오기**를 클릭하여 Visual Studio에서 워크로드를 추가합니다.

## <a name="set-up-the-app-locally"></a>로컬에서 앱 설정

### <a name="download-the-sample"></a>샘플 다운로드

이 단계에서 로컬 .NET 프로젝트를 설정합니다.

- [샘플 프로젝트를 다운로드합니다](https://github.com/Azure-Samples/custom-font-win-container/archive/master.zip).
- *custom-font-win-container.zip* 파일을 추출(압축 해제)합니다.

샘플 프로젝트에는 Windows 글꼴 라이브러리에 설치된 사용자 지정 글꼴을 사용하는 간단한 ASP.NET 애플리케이션이 포함되어 있습니다. 글꼴을 설치할 필요는 없지만 기본 OS와 통합된 앱의 예입니다. 이러한 앱을 App Service로 마이그레이션하려면 코드 아키텍처를 변경하여 통합을 제거하거나 사용자 지정 Windows 컨테이너에서 그대로 마이그레이션합니다.

### <a name="install-the-font"></a>글꼴 설치

Windows 탐색기에서 _custom-font-win-container-master/CustomFontSample_로 이동하여 _FrederickatheGreat-Regular.ttf_를 마우스 오른쪽 단추로 클릭하고 **설치**를 선택합니다.

이 글꼴은 [Google 글꼴](https://fonts.google.com/specimen/Fredericka+the+Great)을 통해 공개적으로 제공됩니다.

### <a name="run-the-app"></a>앱 실행

Visual Studio에서 *custom-font-win-container/CustomFontSample.sln* 파일을 엽니다. 

디버깅 없이 앱을 실행하려면 `Ctrl+F5`를 입력합니다. 앱이 기본 브라우저에 표시됩니다. 

![새 ASP.NET 프로젝트 대화 상자](media/tutorial-custom-container/local-app-in-browser.png)

설치된 글꼴이 사용되기 때문에 App Service 샌드박스에서 앱을 실행할 수 없습니다. 하지만 Windows 컨테이너에 글꼴을 설치할 수 있기 때문에 Windows 컨테이너를 대신 사용하여 앱을 배포할 수 있습니다.

### <a name="configure-windows-container"></a>Windows 컨테이너 구성

솔루션 탐색기에서 **CustomFontSample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **Container Orchestration Support**(컨테이너 오케스트레이션 지원)를 선택합니다.

![새 ASP.NET 프로젝트 대화 상자](media/tutorial-custom-container/enable-container-orchestration.png)

**Docker Compose** > **확인**을 선택합니다.

이제 프로젝트가 Windows 컨테이너에서 실행되도록 설정되었습니다. _Dockerfile_이 **CustomFontSample** 프로젝트에 추가되고 **docker-compose** 프로젝트가 솔루션에 추가됩니다. 

솔루션 탐색기에서 **Dockerfile**을 엽니다.

[지원되는 부모 이미지](quickstart-custom-container.md#use-a-different-parent-image)를 사용해야 합니다. `FROM` 줄을 다음 코드로 바꿔서 부모 이미지를 변경합니다.

```dockerfile
FROM mcr.microsoft.com/dotnet/framework/aspnet:4.7.2-windowsservercore-ltsc2019
```

파일 끝에 다음 줄을 추가하고 파일을 저장합니다.

```dockerfile
RUN ${source:-obj/Docker/publish/InstallFont.ps1}
```

_InstallFont.ps1_은 **CustomFontSample** 프로젝트에서 찾을 수 있습니다. 글꼴을 설치하는 간단한 스크립트입니다. [스크립트 센터](https://gallery.technet.microsoft.com/scriptcenter/fb742f92-e594-4d0c-8b79-27564c575133)에서 더 복잡한 스크립트 버전을 찾을 수 있습니다.

> [!NOTE]
> Windows 컨테이너를 로컬로 테스트하려면 로컬 머신에서 Docker가 시작되었는지 확인합니다.
>

## <a name="publish-to-azure-container-registry"></a>Azure Container Registry에 게시

[Azure Container Registry](../container-registry/index.yml)는 컨테이너 배포용 이미지를 저장할 수 있습니다. Azure Container Registry에서 호스트되는 이미지를 사용하도록 App Service를 구성할 수 있습니다.

### <a name="open-publish-wizard"></a>게시 마법사 열기

솔루션 탐색기에서 **CustomFontSample** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 선택합니다.

![새 ASP.NET 프로젝트 대화 상자](media/tutorial-custom-container/open-publish-wizard.png)

### <a name="create-registry-and-publish"></a>레지스트리 만들기 및 게시

게시 마법사에서 **Container Registry** > **새 Azure Container Registry 만들기** > **게시**를 선택합니다.

![새 ASP.NET 프로젝트 대화 상자](media/tutorial-custom-container/create-registry.png)

### <a name="sign-in-with-azure-account"></a>Azure 계정으로 로그인

**새 Azure Container Registry 만들기** 대화 상자에서 **계정 추가**를 선택하고 Azure 구독에 로그인합니다. 이미 로그인한 경우 드롭다운에서 원하는 구독이 포함된 계정을 선택합니다.

![Azure에 로그인](./media/tutorial-custom-container/add-an-account.png)

### <a name="configure-the-registry"></a>레지스트리 구성

다음 표의 제안 값을 기반으로 새 컨테이너 레지스트리를 구성합니다. 작업을 마쳤으면 **만들기**를 클릭합니다.

| 설정  | 제안 값 | 참조 항목 |
| ----------------- | ------------ | ----|
|**DNS 접두사**| 생성된 레지스트리 이름을 유지하거나 다른 고유한 이름으로 변경합니다. |  |
|**리소스 그룹**| **새로 만들기**를 클릭하고 **myResourceGroup**을 입력하고 **확인**을 클릭합니다. |  |
|**SKU**| Basic | [가격 책정 계층](https://azure.microsoft.com/pricing/details/container-registry/)|
|**레지스트리 위치**| 서유럽 | |

![Azure 컨테이너 레지스트리 구성](./media/tutorial-custom-container/configure-registry.png)

터미널 창이 열리고 이미지 배포 진행률이 표시됩니다. 배포가 완료될 때가지 기다립니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

https://portal.azure.com 에서 Azure Portal에 로그인합니다.

## <a name="create-a-web-app"></a>웹앱 만들기

왼쪽 메뉴에서 **리소스 만들기** > **웹** > **Web App for Containers**를 선택합니다.

### <a name="configure-app-basics"></a>앱 기본 사항 구성

**기본 사항** 탭에서 다음 표에 따라 설정을 구성한 후, **다음: Docker**를 클릭합니다.

| 설정  | 제안 값 | 참조 항목 |
| ----------------- | ------------ | ----|
|**구독**| 올바른 구독을 선택하는지 확인합니다. |  |
|**리소스 그룹**| **새로 만들기**를 선택하고, **myResourceGroup**을 입력하고, **확인**을 클릭합니다. |  |
|**이름**| 고유한 이름을 입력합니다. | 웹앱의 URL은 `http://<app-name>.azurewebsites.net`이며, 여기서 `<app-name>`은 앱 이름입니다. |
|**게시**| Docker 컨테이너 | |
|**운영 체제**| Windows | |
|**지역**| 서유럽 | |
|**Windows 플랜**| **새로 만들기**를 선택하고, **myAppServicePlan**을 입력하고, **확인**을 클릭합니다. | |

**기본 사항** 탭은 다음과 같아야 합니다.

![웹앱을 구성하는 데 사용되는 기본 탭을 표시합니다.](media/tutorial-custom-container/configure-app-basics.png)

### <a name="configure-windows-container"></a>Windows 컨테이너 구성

**Docker** 탭에서 다음 표에 표시된 대로 사용자 지정 Windows 컨테이너를 구성하고 **검토 + 만들기**를 선택합니다.

| 설정  | 제안 값 |
| ----------------- | ------------ |
|**이미지 원본**| Azure Container Register |
|**Registry**| [이전에 만든 레지스트리](#publish-to-azure-container-registry)를 선택합니다. |
|**이미지**| customfontsample |
|**Tag**| 최신 |

### <a name="complete-app-creation"></a>앱 만들기 완료

**만들기**를 클릭하고, Azure를 수신 대기하여 필수 리소스를 만듭니다.

## <a name="browse-to-the-web-app"></a>웹앱으로 이동

Azure 작업이 완료되면 알림 상자가 표시됩니다.

![Azure 작업이 완료되었음을 표시합니다.](media/tutorial-custom-container/portal-create-finished.png)

1. **리소스로 이동**을 클릭합니다.

2. 앱 페이지에서 **URL** 아래에 있는 링크를 클릭합니다.

새 브라우저 페이지가 다음 페이지로 열립니다.

![웹앱의 새 브라우저 페이지를 표시합니다.](media/tutorial-custom-container/app-starting.png)

원하는 글꼴이 적용된 홈 페이지가 표시될 때까지 몇 분 정도 기다렸다가 다시 시도해보십시오.

![구성된 글꼴이 포함된 홈페이지를 표시합니다.](media/tutorial-custom-container/app-running.png)

**축하합니다.** ASP.NET 애플리케이션이 Windows 컨테이너의 Azure App Service로 마이그레이션되었습니다.

## <a name="see-container-start-up-logs"></a>컨테이너 시작 로그를 참조하세요.

Windows 컨테이너를 로드하는 데 다소 시간이 걸릴 수 있습니다. 진행률을 보려면 *\<app-name>* 을 앱 이름으로 대체하여 다음 URL로 이동합니다.
```
https://<app-name>.scm.azurewebsites.net/api/logstream
```

스트리밍된 로그는 다음과 같이 표시됩니다.

```
14/09/2018 23:16:19.889 INFO - Site: fonts-win-container - Creating container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest.
14/09/2018 23:16:19.928 INFO - Site: fonts-win-container - Create container for image: customfontsample20180914115836.azurecr.io/customfontsample:latest succeeded. Container Id 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:23.405 INFO - Site: fonts-win-container - Start container succeeded. Container: 329ecfedbe370f1d99857da7352a7633366b878607994ff1334461e44e6f5418
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:17:28.637 INFO - Site: fonts-win-container - Configuring container
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container ready
14/09/2018 23:18:03.823 INFO - Site: fonts-win-container - Container start-up and configuration completed successfully
```

::: zone-end

::: zone pivot="container-linux"

Azure App Service는 Docker 컨테이너 기술을 사용하여 기본 제공 이미지와 사용자 지정 이미지를 모두 호스팅합니다. 기본 제공 이미지 목록을 보려면 ['az webapp list-runtimes --linux'](/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) Azure CLI 명령을 실행합니다. 이러한 이미지가 요구 사항에 맞지 않는 경우 사용자 지정 이미지를 빌드하고 배포할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 요구 사항에 맞는 기본 이미지가 없는 경우 사용자 지정 이미지 빌드
> * Azure에서 사용자 지정 이미지를 프라이빗 컨테이너 레지스트리로 푸시
> * App Service에서 사용자 지정 이미지 실행
> * 환경 변수 구성
> * 이미지 업데이트 및 다시 배포
> * 진단 로그 액세스
> * SSH를 사용하여 컨테이너에 연결

이 자습서를 완료하면 컨테이너 레지스트리에 대한 Azure 계정에 약간의 비용이 발생하며, 컨테이너를 한 달 넘게 호스팅하는 경우 추가 비용이 발생할 수 있습니다.

## <a name="set-up-your-initial-environment"></a>초기 환경 설정

* 활성 구독이 포함된 Azure 계정이 있어야 합니다. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Docker 이미지를 빌드하는 데 사용하는 [Docker](https://docs.docker.com/get-started/#setup)를 설치합니다. Docker를 설치하려면 컴퓨터를 다시 시작해야 할 수 있습니다.
* <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 이상을 설치합니다. 이를 통해 셸에서 명령을 실행하여 Azure 리소스를 프로비저닝하고 구성할 수 있습니다.

Docker 및 Azure CLI가 설치되면 터미널 창을 열고 Docker가 설치되어 있는지 확인합니다.

```bash
docker --version
```

또한 Azure CLI 버전이 2.0.80 이상인지 확인합니다.

```azurecli
az --version
```

그런 다음, CLI를 통해 Azure에 로그인합니다.

```azurecli
az login
```

`az login` 명령은 브라우저를 열어 자격 증명을 수집합니다. 명령이 완료되면 구독에 대한 정보가 포함된 JSON 출력이 표시됩니다.

로그인하면 Azure CLI에서 Azure 명령을 실행하여 구독의 리소스를 사용할 수 있습니다.

## <a name="clone-or-download-the-sample-app"></a>샘플 앱 복제 또는 다운로드

이 자습서의 샘플은 git clone 또는 다운로드를 통해 얻을 수 있습니다.

### <a name="clone-with-git"></a>git을 사용하여 복제

샘플 리포지토리를 복제합니다.

```terminal
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
```

Linux 컨테이너 내에서 사용되는 파일에서 올바른 줄 끝을 보장하려면 `--config core.autocrlf=input` 인수를 포함해야 합니다.

그런 다음, 해당 폴더로 이동합니다.

```terminal
cd docker-django-webapp-linux
```

### <a name="download-from-github"></a>GitHub에서 다운로드

git clone을 사용하는 대신 [https://github.com/Azure-Samples/docker-django-webapp-linux](https://github.com/Azure-Samples/docker-django-webapp-linux)를 방문하여 **복제**를 선택한 다음, **ZIP 다운로드**를 선택할 수 있습니다. 

ZIP 파일의 압축을 *docker-django-webapp-linux*라는 폴더에 풉니다. 

그런 다음, 해당 *docker-django-webapp-linux* 폴더에서 터미널 창을 엽니다.

## <a name="optional-examine-the-docker-file"></a>(선택 사항) Docker 파일 검사

Docker 이미지를 설명하고 구성 지침을 포함하는 _Dockerfile_이라는 샘플 파일은 다음과 같습니다.

```Dockerfile
FROM tiangolo/uwsgi-nginx-flask:python3.6

RUN mkdir /code
WORKDIR /code
ADD requirements.txt /code/
RUN pip install -r requirements.txt --no-cache-dir
ADD . /code/

# ssh
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
    && apt-get install -y --no-install-recommends openssh-server \
    && echo "$SSH_PASSWD" | chpasswd 

COPY sshd_config /etc/ssh/
COPY init.sh /usr/local/bin/
    
RUN chmod u+x /usr/local/bin/init.sh
EXPOSE 8000 2222

#CMD ["python", "/code/manage.py", "runserver", "0.0.0.0:8000"]
ENTRYPOINT ["init.sh"]
```

* 첫 번째 명령 그룹은 앱의 요구 사항을 환경에 설치합니다.
* 두 번째 명령 그룹은 컨테이너와 호스트 간의 보안 통신에 사용할 [SSH](https://www.ssh.com/ssh/protocol/) 서버를 만듭니다.
* 마지막 `ENTRYPOINT ["init.sh"]` 줄은 `init.sh`를 호출하여 SSH 서비스와 Python 서버를 시작합니다.

## <a name="build-and-test-the-image-locally"></a>로컬로 이미지 빌드 및 테스트

1. 다음 명령을 실행하여 이미지를 빌드합니다.

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```
    
1. Docker 컨테이너를 로컬로 실행하여 빌드가 작동하는지 테스트합니다.

    ```bash
    docker run -p 8000:8000 appsvc-tutorial-custom-image
    ```
    
    이 [`docker run`](https://docs.docker.com/engine/reference/commandline/run/) 명령은 `-p` 인수 뒤에 이미지 이름을 사용하여 포트를 지정합니다. 
    
    > [!TIP]
    > Windows에서 실행되고 *standard_init_linux.go:211: exec 사용자 프로세스로 인해 "해당 파일이나 디렉터리가 없습니다."* 라는 오류가 표시되면 필요한 LF 종료 끝 대신 CR-LF 줄 끝이 *init.sh* 파일에 포함됩니다. 이 오류는 git을 사용하여 샘플 리포지토리를 복제했지만 `--config core.autocrlf=input` 매개 변수를 생략한 경우에 발생합니다. 이 경우 '--config' 인수를 사용하여 리포지토리를 다시 복제하세요. 또한 *init.sh*를 편집하고 CRLF 끝을 사용하여 저장한 경우에도 이 오류가 표시될 수 있습니다. 이 경우 LF 끝만 사용하여 파일을 다시 저장하세요.

1. `http://localhost:8000`으로 이동하여 웹앱과 컨테이너가 올바르게 작동하는지 확인합니다.

    ![로컬로 웹앱 테스트](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-local.png)

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

이 섹션과 다음 섹션에서는 Azure에서 이미지를 푸시할 리소스를 프로비저닝한 다음, 컨테이너를 Azure App Service에 배포합니다. 먼저 이러한 모든 리소스를 수집할 리소스 그룹을 만듭니다.

[az group create](/cli/azure/group?view=azure-cli-latest#az-group-create)를 실행하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name AppSvc-DockerTutorial-rg --location westus2
```

`--location` 값을 변경하여 가까운 지역을 지정할 수 있습니다.

## <a name="push-the-image-to-azure-container-registry"></a>Azure Container Registry로 이미지 푸시

이 섹션에서는 이미지를 App Service에서 배포할 수 있는 Azure Container Registry로 푸시합니다.

1. [`az acr create`](/cli/azure/acr?view=azure-cli-latest#az-acr-create) 명령을 실행하여 Azure Container Registry를 만듭니다.

    ```azurecli-interactive
    az acr create --name <registry-name> --resource-group AppSvc-DockerTutorial-rg --sku Basic --admin-enabled true
    ```
    
    `<registry-name>`을 레지스트리에 적합한 이름으로 바꿉니다. 이름은 문자와 숫자만 포함해야 하며 모든 Azure에서 고유해야 합니다.

1. [`az acr show`](/cli/azure/acr?view=azure-cli-latest#az-acr-show) 명령을 실행하여 레지스트리에 대한 자격 증명을 검색합니다.

    ```azurecli-interactive
    az acr credential show --resource-group AppSvc-DockerTutorial-rg --name <registry-name>
    ```
    
    이 명령의 JSON 출력은 레지스트리의 사용자 이름과 함께 두 개의 암호를 제공합니다.
    
1. `docker login` 명령을 사용하여 컨테이너 레지스트리에 로그인합니다.

    ```bash
    docker login <registry-name>.azurecr.io --username <registry-username>
    ```
    
    `<registry-name>` 및 `<registry-username>`을 이전 단계의 값으로 바꿉니다. 메시지가 표시되면 이전 단계의 암호 중 하나를 입력합니다.

    이 섹션의 나머지 모든 단계에서는 동일한 레지스트리 이름을 사용합니다.

1. 로그인에 성공하면 레지스트리에 대한 로컬 Docker 이미지에 태그를 지정합니다.

    ```bash
   docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```    

1. `docker push` 명령을 사용하여 이미지를 레지스트리로 푸시합니다.

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    이미지를 처음 업로드하는 경우 기본 이미지가 포함되어 있으므로 몇 분 정도 걸릴 수 있습니다. 이후 업로드는 일반적으로 더 빠릅니다.

    기다리는 동안 다음 섹션의 단계를 완료하여 레지스트리에서 배포하도록 App Service를 구성할 수 있습니다.

1. `az acr repository list` 명령을 사용하여 푸시가 성공했는지 확인합니다.

    ```azurecli-interactive
    az acr repository list -n <registry-name>
    ```
    
    출력에 이미지 이름이 표시됩니다.


## <a name="configure-app-service-to-deploy-the-image-from-the-registry"></a>레지스트리에서 이미지를 배포하도록 App Service 구성

컨테이너를 Azure App Service에 배포하려면 먼저 App Service에서 웹앱을 만든 다음, 이 웹앱을 컨테이너 레지스트리에 연결합니다. 웹앱이 시작되면 App Service에서 자동으로 레지스트리로부터 이미지를 가져옵니다.

1. [`az appservice plan create`](/cli/azure/appservice/plan?view=azure-cli-latest#az-appservice-plan-create) 명령을 사용하여 App Service 계획을 만듭니다.

    ```azurecli-interactive
    az appservice plan create --name AppSvc-DockerTutorial-plan --resource-group AppSvc-DockerTutorial-rg --is-linux
    ```

    App Service 계획은 웹앱을 호스팅하는 가상 머신에 해당합니다. 기본적으로 이전 명령은 첫 달 동안 무료로 사용할 수 있는 저렴한 [B1 가격 책정 계층](https://azure.microsoft.com/pricing/details/app-service/linux/)을 사용합니다. 이 계층은 `--sku` 매개 변수를 사용하여 제어할 수 있습니다.

1. [`az webpp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) 명령을 사용하여 웹앱을 만듭니다.

    ```azurecli-interactive
    az webapp create --resource-group AppSvc-DockerTutorial-rg --plan AppSvc-DockerTutorial-plan --name <app-name> --deployment-container-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```
    
    `<app-name>`을 모든 Azure에서 고유한 웹앱의 이름으로 바꿉니다. 또한 `<registry-name>`을 이전 섹션의 레지스트리 이름으로 바꿉니다.

1. [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set)을 사용하여 `WEBSITES_PORT` 환경 변수를 앱 코드에서 예상한 대로 설정합니다. 

    ```azurecli-interactive
    az webapp config appsettings set --resource-group AppSvc-DockerTutorial-rg --name <app-name> --settings WEBSITES_PORT=8000
    ```

    `<app-name>`을 이전 단계에서 사용한 이름으로 바꿉니다.
    
    이 환경 변수에 대한 자세한 내용은 [샘플의 GitHub 리포지토리에 있는 추가 정보](https://github.com/Azure-Samples/docker-django-webapp-linux)를 참조하세요.

1. [`az webapp identity assign`](/cli/azure/webapp/identity?view=azure-cli-latest#az-webapp-identity-assign) 명령을 사용하여 [관리 ID](./overview-managed-identity.md)를 웹앱에 사용하도록 설정합니다.

    ```azurecli-interactive
    az webapp identity assign --resource-group AppSvc-DockerTutorial-rg --name <app-name> --query principalId --output tsv
    ```

    `<app-name>`을 이전 단계에서 사용한 이름으로 바꿉니다. 명령의 출력(`--query` 및 `--output` 인수로 필터링됨)은 할당된 ID의 서비스 주체이며, 잠시 후에 사용됩니다.

    관리 ID를 사용하면 특정 자격 증명 없이도 다른 Azure 리소스에 액세스할 수 있는 권한을 웹앱에 부여할 수 있습니다.

1. [`az account show`](/cli/azure/account?view=azure-cli-latest#az-account-show) 명령을 사용하여 다음 단계에서 필요한 구독 ID를 검색합니다.

    ```azurecli-interactive
    az account show --query id --output tsv
    ``` 

1. 컨테이너 레지스트리에 액세스할 수 있는 권한을 웹앱에 부여합니다.

    ```azurecli-interactive
    az role assignment create --assignee <principal-id> --scope /subscriptions/<subscription-id>/resourceGroups/AppSvc-DockerTutorial-rg/providers/Microsoft.ContainerRegistry/registries/<registry-name> --role "AcrPull"
    ```

    다음 값을 바꿉니다.
    - `<principal-id>` 값을 `az webapp identity assign` 명령의 서비스 주체 ID로 바꿉니다.
    - `<registry-name>` 값을 컨테이너 레지스트리의 이름으로 바꿉니다.
    - `<subscription-id>` 값을 `az account show` 명령에서 검색된 구독 ID로 바꿉니다.

이러한 권한에 대한 자세한 내용은 [Azure 역할 기반 액세스 제어란?](../role-based-access-control/overview.md)을 참조하세요. 

## <a name="deploy-the-image-and-test-the-app"></a>이미지 배포 및 앱 테스트

이미지가 컨테이너 레지스트리로 푸시되고 App Service가 완전히 프로비저닝되면 다음 단계를 완료할 수 있습니다.

1. [`az webapp config container set`](/cli/azure/webapp/config/container?view=azure-cli-latest#az-webapp-config-container-set) 명령을 사용하여 웹앱에 배포할 컨테이너 레지스트리와 이미지를 지정합니다.

    ```azurecli-interactive
    az webapp config container set --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-custom-image-name <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest --docker-registry-server-url https://<registry-name>.azurecr.io
    ```
    
    `<app_name>`을 웹앱 이름으로 바꾸고, 두 위치에 있는 `<registry-name>`을 레지스트리 이름으로 바꿉니다. 

    - 이 예제와 같이 Docker Hub 이외의 레지스트리를 사용하는 경우 `--docker-registry-server-url`은 `https://` 뒤에 레지스트리의 정규화된 도메인 이름이 나오는 형식으로 지정해야 합니다.
    - "Azure Container Registry에 액세스할 수 있는 자격 증명이 제공되지 않았습니다. 조회 시도 중..."이라는 메시지는 Azure에서 사용자 이름과 암호를 묻는 대신 앱의 관리 ID를 사용하여 컨테이너 레지스트리를 인증하고 있음을 나타냅니다.
    - "AttributeError: 'NoneType' 개체에 'reserved' 특성이 없습니다."라는 오류가 발생하면 `<app-name>`이 올바른지 확인합니다.

    > [!TIP]
    > 웹앱의 컨테이너 설정은 언제든지 `az webapp config container show --name <app-name> --resource-group AppSvc-DockerTutorial-rg` 명령을 사용하여 검색할 수 있습니다. 이미지는 `DOCKER_CUSTOM_IMAGE_NAME` 속성에 지정됩니다. Azure DevOps 또는 Azure Resource Manager 템플릿을 통해 웹앱을 배포하는 경우 해당 이미지가 `LinuxFxVersion`이라는 속성에도 표시될 수 있습니다. 두 속성은 모두 동일한 용도로 제공됩니다. 둘 다 웹앱의 구성에 있는 경우 `LinuxFxVersion`이 우선적으로 적용됩니다.

1. `az webapp config container set` 명령이 완료되면 웹앱이 App Service의 컨테이너에서 실행됩니다.

    앱을 테스트하려면 `http://<app-name>.azurewebsites.net`으로 이동하여 `<app-name>`을 웹앱 이름으로 바꿉니다. 처음 액세스하는 경우 App Service에서 레지스트리로부터 전체 이미지를 가져와야 하므로 앱이 응답하는 데 약간의 시간이 걸릴 수 있습니다. 브라우저에서 시간이 초과되면 페이지를 새로 고칩니다. 초기 이미지를 가져오면 이후 테스트가 훨씬 더 빠르게 실행됩니다.

    ![Azure에서 성공한 웹앱 테스트](./media/app-service-linux-using-custom-docker-image/app-service-linux-browse-azure.png)

## <a name="modify-the-app-code-and-redeploy"></a>앱 코드 수정 및 다시 배포

이 섹션에서는 웹앱 코드를 변경하고, 컨테이너를 다시 빌드한 다음, 컨테이너를 레지스트리로 푸시합니다. 그런 다음, App Service에서 자동으로 레지스트리로부터 업데이트된 이미지를 가져와서 실행 중인 웹앱을 업데이트합니다.

1. 로컬 *docker-django-webapp-linux* 폴더에서 *app/templates/app/index.html* 파일을 엽니다.

1. 첫 번째 HTML 요소를 다음 코드와 일치하도록 변경합니다.

    ```html
    <nav class="navbar navbar-inverse navbar-fixed-top">
      <div class="container">
        <div class="navbar-header">
          <a class="navbar-brand" href="#">Azure App Service - Updated Here!</a>
        </div>
      </div>
    </nav>
    ```
    
1. 변경 내용을 저장합니다.

1. *docker-django-webapp-linux* 폴더로 변경하고, 이미지를 다시 빌드합니다.

    ```bash
    docker build --tag appsvc-tutorial-custom-image .
    ```

1. 이미지 태그의 버전 번호를 v1.0.1로 업데이트합니다.

    ```bash
    docker tag appsvc-tutorial-custom-image <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

    `<registry-name>`을 레지스트리 이름으로 바꿉니다.

1. 이미지를 레지스트리로 푸시합니다.

    ```bash
    docker push <registry-name>.azurecr.io/appsvc-tutorial-custom-image:latest
    ```

1. 웹앱을 다시 시작합니다.

    ```azurecli-interactive
    az webapp restart --name <app_name> --resource-group AppSvc-DockerTutorial-rg
    ```

    `<app_name>`을 웹앱 이름으로 바꿉니다. 다시 시작되면 App Service에서 컨테이너 레지스트리로부터 업데이트된 이미지를 가져옵니다.

1. `http://<app-name>.azurewebsites.net`으로 이동하여 업데이트가 배포되었는지 확인합니다.

## <a name="access-diagnostic-logs"></a>진단 로그 액세스

1. 컨테이너 로깅을 설정합니다.

    ```azurecli-interactive
    az webapp log config --name <app-name> --resource-group AppSvc-DockerTutorial-rg --docker-container-logging filesystem
    ```
    
1. 로그 스트림을 사용하도록 설정합니다.

    ```azurecli-interactive
    az webapp log tail --name <app-name> --resource-group AppSvc-DockerTutorial-rg
    ```
    
    콘솔 로그가 즉시 표시되지 않으면 30초 후에 다시 확인합니다.

    `https://<app-name>.scm.azurewebsites.net/api/logs/docker`의 브라우저에서 로그 파일을 검사할 수도 있습니다.

1. 언제든지 로그 스트리밍을 중지하려면 **Ctrl**+**C**를 입력합니다.

## <a name="connect-to-the-container-using-ssh"></a>SSH를 사용하여 컨테이너에 연결

SSH를 사용하면 컨테이너와 클라이언트 간의 보안 통신을 설정할 수 있습니다. 컨테이너에 대한 SSH 연결을 사용하도록 설정하려면 해당 컨테이너에 대한 사용자 지정 이미지를 구성해야 합니다. 컨테이너가 실행되면 SSH 연결을 열 수 있습니다.

### <a name="configure-the-container-for-ssh"></a>SSH에 대한 컨테이너 구성

이 자습서에 사용되는 샘플 앱에는 SSH 서버를 설치하고 로그인 자격 증명도 설정하는 *Dockerfile*에 필요한 구성이 이미 있습니다. 이 섹션은 정보 제공용입니다. 컨테이너에 연결하려면 다음 섹션으로 건너뜁니다.

```Dockerfile
ENV SSH_PASSWD "root:Docker!"
RUN apt-get update \
        && apt-get install -y --no-install-recommends dialog \
        && apt-get update \
  && apt-get install -y --no-install-recommends openssh-server \
  && echo "$SSH_PASSWD" | chpasswd 
```

> [!NOTE]
> 이 구성은 컨테이너에 대한 외부 연결을 허용하지 않습니다. Kudu/SCM 사이트를 통해서만 SSH를 사용할 수 있습니다. Kudu/SCM 사이트는 Azure 계정으로 인증됩니다.

또한 *Dockerfile*은 *sshd_config* 파일을 */etc/ssh/* 폴더에 복사하고, 컨테이너에서 포트 2222를 공개합니다.

```Dockerfile
COPY sshd_config /etc/ssh/

# ...

EXPOSE 8000 2222
```

포트 2222는 개인 가상 네트워크의 브리지 네트워크 내에 있는 컨테이너에서만 액세스할 수 있는 내부 포트입니다. 

마지막으로, *init.sh* 항목 스크립트에서 SSH 서버를 시작합니다.

```bash
#!/bin/bash
service ssh start
```

### <a name="open-ssh-connection-to-container"></a>컨테이너에 대한 SSH 연결 열기

1. `https://<app-name>.scm.azurewebsites.net/webssh/host`로 이동하고 Azure 계정으로 로그인합니다. `<app-name>`을 웹앱 이름으로 바꿉니다.

1. 로그인하면 웹앱에 대한 정보 페이지로 리디렉션됩니다. 페이지 위쪽에서 **SSH**를 선택하여 셸을 열고 명령을 사용합니다.

    예를 들어 `top` 명령을 사용하여 프로세스 내에서 실행되는 프로세스를 검사할 수 있습니다.
    
## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스에는 비용이 지속적으로 발생할 수 있습니다. 리소스를 정리하려면 해당 리소스가 포함된 리소스 그룹만 삭제하면 됩니다.

```azurecli
az group delete --name AppSvc-DockerTutorial-rg
```

## <a name="next-steps"></a>다음 단계

학습한 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 개인 컨테이너 레지스트리에 사용자 지정 이미지 배포
> * App Service에서 사용자 지정 이미지 배포
> * 이미지 업데이트 및 다시 배포
> * 진단 로그 액세스
> * SSH를 사용하여 컨테이너에 연결

다음 자습서에서는 사용자 지정 DNS 이름을 앱에 매핑하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [자습서: 앱에 사용자 지정 DNS 이름 매핑](app-service-web-tutorial-custom-domain.md)

또는 다른 리소스를 확인합니다.

> [!div class="nextstepaction"]
> [사용자 지정 컨테이너 구성](configure-custom-container.md)

> [!div class="nextstepaction"]
> [자습서: 다중 컨테이너 WordPress 앱](tutorial-multi-container-app.md)

::: zone-end