---
title: Azure Dev Spaces로 CI/CD 사용
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: yuvalm
description: Azure에서 컨테이너 및 마이크로 서비스를 통한 신속한 Kubernetes 개발
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, 컨테이너
ms.openlocfilehash: 983af0dd75e6ae62630c85d04ac3819c7e260439
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60687370"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Azure Dev Spaces로 CI/CD 사용

이 문서에서는 CI/CD(연속 통합/연속 배포)-Dev Spaces가 설정된 AKS(Azure Kubernetes Service)를 설정하는 과정을 안내합니다. CI/CD-AKS를 사용하면 커밋된 코드를 소스 리포지토리에 푸시할 때마다 앱 업데이트가 자동으로 배포될 수 있습니다. CI/CD를 Dev Spaces 지원 클러스터와 함께 사용하면 팀이 작업하는 애플리케이션의 기준선을 최신 상태로 유지할 수 있으므로 유용합니다.

![CI/CD 다이어그램의 예제](../media/common/ci-cd-simple.png)

이 문서에서는 Azure DevOps를 기준으로 설명하지만 Jenkins, TeamCity 등의 CI/CD 시스템에도 동일한 개념이 적용됩니다.

## <a name="prerequisites"></a>필수 조건
* [Azure Dev Spaces가 설정된 AKS(Azure Kubernetes Service) 클러스터](../get-started-netcore.md)
* [Azure Dev Spaces CLI 설치](upgrade-tools.md)
* [프로젝트가 있는 Azure DevOps 조직](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [ACR(Azure Container Registry)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Azure Container Registry [관리자 계정](../../container-registry/container-registry-authentication.md#admin-account) 세부 정보
* [AKS 클러스터가 Azure Container Registry에서 끌어오도록 허가](../../container-registry/container-registry-auth-aks.md)

## <a name="download-sample-code"></a>샘플 코드 다운로드
이제 샘플 코드 GitHub 리포지토리의 포크를 만들어 보겠습니다. https://github.com/Azure/dev-spaces로 이동하여 **포크**를 선택합니다. 포크 프로세스가 완료되면 리포지토리의 포크된 버전을 로컬로 **복제**합니다. 기본적으로 _master_ 분기가 체크 아웃되지만, _azds_updates_ 분기에서도 마찬가지로 포크 동안 전송되어야 하는 시간 절감 변경 내용을 몇 가지 포함했습니다. _azds_updates_ 분기는 Dev Spaces 자습서 섹션에서 수동으로 수행하도록 요구되는 업데이트와 CI/CD 시스템 배포를 간소화하기 위해 미리 만든 일부 YAML 및 JSON 파일도 포함되어 있습니다. `git checkout -b azds_updates origin/azds_updates`와 같은 명령을 사용하여 로컬 리포지토리의 _azds_updates_ 분기를 체크 아웃할 수 있습니다.

## <a name="dev-spaces-setup"></a>Dev Spaces 설치
`azds space select` 명령을 사용하여 _dev_라는 새 공간을 만듭니다. _dev_ 공간은 CI/CD 파이프라인에서 코드 변경 내용을 푸시하는 데 사용합니다. 또한 이 공간은 _dev_를 기준으로 하는 _자식 공간_을 만드는 데도 사용됩니다.

```cmd
azds space select -n dev
```

부모 개발 공간을 선택하라는 메시지가 표시되면 _\<none\>_ 을 선택합니다.

사용자의 개발 공간을 만든 후 호스트 접미사를 결정 해야 합니다. 사용 된 `azds show-context` Azure 개발 공간 수신 컨트롤러의 호스트 접미사를 표시 하는 명령입니다.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

호스트 접미사는 위의 예에서 _fedcba098.eus.azds.io_합니다. 이 값은 릴리스 정의 만들 때 나중에 사용 됩니다.

_dev_ 공간은 항상 리포지토리의 최신 상태인 기준선을 포함하므로, 개발자는 더 큰 앱의 컨텍스트 내에서 격리된 변경 내용을 테스트하기 위해 _dev_에서 _자식 공간_을 만들 수 있습니다. 이 개념은 Dev Spaces 자습서에서 자세히 설명하고 있습니다.

## <a name="creating-the-build-definition"></a>빌드 정의 만들기
웹 브라우저에서 Azure DevOps 팀 프로젝트를 열고 _파이프라인_ 섹션으로 이동합니다. 먼저 Azure DevOps 사이트의 오른쪽 위에 있는 프로필 사진을 클릭하고, 미리 보기 기능 창을 연 후, _새 YAML 파이프라인 생성 환경_을 사용하지 않도록 설정합니다.

![미리 보기 기능 창 열기](../media/common/preview-feature-open.png)

사용하지 않도록 설정할 옵션은 다음과 같습니다.

![새 YAML 파이프라인 생성 환경 옵션](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Azure DevOps _새 YAML 파이프라인 생성 환경_ 미리 보기 기능은 현재, 미리 정의된 빌드 파이프라인 만들기와 충돌합니다. 현재는 미리 정의된 빌드 파이프라인을 배포하려면 이 옵션을 사용하지 않도록 설정해야 합니다.

_azds_updates_ 분기에 *mywebapi* 및 *webfrontend*에 필요한 빌드 단계를 정의하는 간단한 [Azure 파이프라인 YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema)을 포함했습니다.

선택한 언어에 따라, 파이프라인 YAML은 `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`과 유사한 경로에서 체크 아웃되었습니다.

이 파일에서 파이프라인을 만들려면
1. DevOps 프로젝트 기본 페이지에서 파이프라인으로 이동 > 빌드합니다.
1. 만드는 옵션을 선택는 **새로 만들기** 파이프라인을 작성 합니다.
1. 선택 **GitHub** 원본으로 권한 부여 GitHub 계정을 사용 하 여 선택 하 고 필요한 경우 합니다 _azds_updates_ 개발 공간 sampleapp 리포지토리의 포크 된 버전에서 분기 합니다.
1. 선택 **구성을 코드로**, 또는 **YAML**, 템플릿으로 합니다.
1. 이제 빌드 파이프라인의 구성 페이지가 표시됩니다. 에 대 한 언어별 경로로 이동 하는 위에서 설명한 대로 합니다 **YAML 파일 경로** 사용 하 여는 **...**  단추입니다. 예: `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. 로 이동 합니다 **변수** 탭 합니다.
1. 수동으로 _dockerId_를 변수로 입력합니다. 이 값은 [Azure Container Registry 관리자 계정](../../container-registry/container-registry-authentication.md#admin-account)의 사용자 이름입니다. (필수 구성 요소 문서에 설명됨)
1. 수동으로 _dockerPassword_를 변수로 입력합니다. 이 값은 [Azure Container Registry 관리자 계정](../../container-registry/container-registry-authentication.md#admin-account)의 암호입니다. 보안상의 이유로 _dockerPassword_는 비밀로 지정해야 합니다(잠금 아이콘 선택).
1. 선택 **저장 및 큐**합니다.

이제 GitHub 포크의 _azds_updates_ 분기에 푸시된 모든 업데이트에 대해 *mywebapi* 및 *webfrontend*를 자동으로 빌드하는 CI 솔루션이 구현되었습니다. Azure portal로 이동, Azure Container Registry를 선택 하 고 검색 하 여 푸시된 Docker 이미지를 확인할 수 있습니다 합니다 **리포지토리** 탭 합니다. 이미지를 빌드하고 컨테이너 레지스트리에 나타나는 데 몇 분 정도 걸릴 수 있습니다.

![Azure Container Registry 리포지토리](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>릴리스 정의 만들기

1. DevOps 프로젝트 주 페이지에서 파이프라인 > 릴리스로 이동합니다.
1. 릴리스 정의를 아직 포함하지 않는 새로운 DevOps 프로젝트에서 작업할 경우 계속하기 전에 먼지 빈 릴리스 정의를 만들어야 합니다. 가져오기 옵션은 기존 릴리스 정의가 있을 때까지 UI에 표시되지 않습니다.
1. 왼쪽에서 클릭 합니다 **+ 새로 만들기** 단추를 클릭 **파이프라인 가져오기**합니다.
1. 클릭 **찾아보기** 선택한 `samples/release.json` 프로젝트에서.
1. **확인**을 클릭합니다. 릴리스 정의 편집 페이지에 파이프라인 창이 로드된 것을 확인할 수 있습니다. 또한 계속 구성해야 하는 클러스터 관련 세부 정보를 나타내는 몇 개의 빨간색 경고 아이콘도 표시됩니다.
1. 파이프라인 창 왼쪽에서 **아티팩트 추가** 풍선을 클릭합니다.
1. 에 **원본** 이전에 만든 드롭다운에서 선택 빌드 파이프라인.
1. 에 대 한 합니다 **기본 버전**, 선택 **태그를 사용 하 여 빌드 파이프라인 기본 분기의 최신**합니다.
1. 둡니다 **태그** 비어 있습니다.
1. **소스 별칭**을 `drop`으로 설정합니다. 합니다 **소스 별칭** 값은 미리 정의 된 릴리스 작업을 사용 하도록 설정 되어야 합니다.
1. **추가**를 클릭합니다.
1. 이제 아래와 같이 새로 만든 `drop` 아티팩트 소스의 전구 모양 아이콘을 클릭합니다.

    ![릴리스 아티팩트 연속 배포 설정](../media/common/release-artifact-cd-setup.png)
1. 사용 하도록 설정 합니다 **연속 배포 트리거**합니다.
1. 마우스로 합니다 **작업** 탭 옆에 **파이프라인** 클릭 _개발_ 편집 하는 _개발_ 작업 단계.
1. 확인할 **Azure Resource Manager** 아래에 선택 되어 **연결 유형입니다.** 고 빨간색으로 강조 표시 하는 세 가지 드롭다운 컨트롤을 표시 합니다. ![릴리스 정의 설정](../media/common/release-setup-tasks.png)
1. Azure 개발 공백을 사용 하 여 사용 중인 Azure 구독을 선택 합니다. 또한 해야 클릭 **권한 부여**합니다.
1. 리소스 그룹 및 Azure 개발 공백을 사용 하는 클러스터를 선택 합니다.
1. 클릭할 **에이전트 작업**합니다.
1. 선택 **Ubuntu 1604 호스팅된** 아래에서 **에이전트 풀**합니다.
1. 마우스로 합니다 **작업** 맨 위에 있는 선택기를 클릭 _prod_ 편집 하는 _prod_ 작업 단계입니다.
1. 확인할 **Azure Resource Manager** 아래에 선택 되어 **연결 유형입니다.** 한 Azure 구독, 리소스 그룹 및 Azure 개발 공백을 사용 하는 클러스터를 선택 합니다.
1. 클릭할 **에이전트 작업**합니다.
1. 선택 **Ubuntu 1604 호스팅된** 아래에서 **에이전트 풀**합니다.
1. 클릭 합니다 **변수** 탭 릴리스에 대 한 변수를 업데이트 합니다.
1. 값을 업데이트할 **DevSpacesHostSuffix** 에서 **UPDATE_ME** 호스트 접미사. 호스트 접미사 실행 했을 때 표시 되는 `azds show-context` 이전 명령입니다.
1. 오른쪽 위에서 **저장**을 클릭하고 **확인**을 클릭합니다.
1. 저장 옆에 있는 **+ 릴리스**를 클릭하고 **릴리스 만들기**를 클릭합니다.
1. 아래 **아티팩트**, 빌드 파이프라인에서 최신 빌드가 선택 되었는지 확인 합니다.
1. **만들기**를 클릭합니다.

이제 자동화된 릴리스 프로세스가 시작되고 *mywebapi* 및 *webfrontend* 차트를 _dev_ 최상위 수준 공간의 Kubernetes 클러스터에 배포합니다. Azure DevOps 웹 포털에서 사용 중인 버전의 진행률을 모니터링할 수 있습니다.

1. 로 이동 합니다 **릴리스** 섹션 아래 **파이프라인**합니다.
1. 샘플 응용 프로그램에 대 한 릴리스 파이프라인을 클릭 합니다.
1. 최신 버전의 이름을 클릭 합니다.
1. 마우스로 **dev** 상자에 **단계** 클릭 **로그**합니다.

릴리스는 모든 태스크가 완료 되 면 수행 됩니다.

> [!TIP]
> 릴리스가 *업그레이드 실패: 조건을 기다리는 동안 시간 초과되었습니다.* 와 같은 오류 메시지를 나타내며 실패하는 경우 [Kubernetes 대시보드를 사용하여](../../aks/kubernetes-dashboard.md) 클러스터의 pod를 검사하세요. Pod가 *Failed to pull image "azdsexample.azurecr.io/mywebapi:122": rpc error: code = Unknown desc = Error response from daemon: Get https://azdsexample.azurecr.io/v2/mywebapi/manifests/122: unauthorized: authentication required*와 같은 오류 메시지를 나타내면서 시작되지 않을 경우 클러스터가 Azure Container Registry에서 끌어오도록 허가되지 않았기 때문일 수 있습니다. [AKS 클러스터가 Azure Container Registry에서 끌어오도록 허가](../../container-registry/container-registry-auth-aks.md)를 완료했는지 확인합니다.

이제 Dev Spaces 샘플 앱의 GitHub 포크에 대해 완전 자동화 CI/CD 파이프라인이 구현되었습니다. 코드를 커밋하고 푸시할 때마다 빌드 파이프라인은 *mywebapi* 및 *webfrontend* 이미지를 빌드한 후 사용자 지정 ACR 인스턴스로 푸시합니다. 그러면 릴리스 파이프라인은 각 앱의 Helm 차트를 Dev Spaces 지원 클러스터의 _dev_ 공간으로 배포합니다.

## <a name="accessing-your-dev-services"></a>_dev_ 서비스에 액세스
배포 후에 *webfrontend*의 _dev_ 버전은 `http://dev.webfrontend.fedcba098.eus.azds.io`와 같은 공용 URL을 통해 액세스할 수 있습니다. 실행 하 여이 URL을 확인할 수 있습니다는 `azds list-uri` 명령: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>프로덕션에 배포

이 자습서에서 만든 CI/CD 시스템을 사용하여 수동으로 특정 릴리스를 _prod_로 승격합니다.
1. 로 이동 합니다 **릴리스** 섹션 아래 **파이프라인**합니다.
1. 샘플 응용 프로그램에 대 한 릴리스 파이프라인을 클릭 합니다.
1. 최신 버전의 이름을 클릭 합니다.
1. 마우스로 합니다 **prod** 상자에 **단계** 클릭 **배포**합니다.
    ![프로덕션으로 수준 올리기](../media/common/prod-promote.png)
1. 마우스로 **prod** 다시 상자 **단계** 누릅니다 **로그**합니다.

릴리스는 모든 태스크가 완료 되 면 수행 됩니다.

합니다 _prod_ CI/CD 파이프라인의 단계를 사용 하 여 부하 분산 장치 대신 개발 공간 수신 컨트롤러에 대 한 액세스를 제공 _prod_ 서비스입니다. 서비스에 배포 합니다 _prod_ 단계는 DNS 이름이 아닌 IP 주소로 액세스할 수 있습니다. 프로덕션 환경에서 사용자 고유의 수신 컨트롤러를 사용자 고유의 DNS 구성에 따라 서비스 호스트를 선택할 수 있습니다.

Webfrontend 서비스의 IP를 확인 하려면 클릭 합니다 **webfrontend 공용 IP를 인쇄** 로그 출력을 확장 하는 단계입니다. 로그에 액세스 하려면 출력에 표시 된 IP를 사용 합니다 **webfrontend** 응용 프로그램입니다.

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>프로덕션 환경의 Dev Spaces 계측
Dev Spaces 계측은 애플리케이션의 정상 작동 중에는 개입하지 _않도록_ 디자인되었지만 Dev Spaces가 설정되지 않은 Kubernetes 네임스페이스에서 프로덕션 워크로드를 실행하는 것이 좋습니다. 이러한 유형의 Kubernetes 네임스페이스를 사용할 경우 `kubectl` CLI를 사용하여 프로덕션 네임스페이스를 만들거나 첫 번째 Helm 배포 동안 CI/CD 시스템이 네임스페이스를 만들도록 허용해야 합니다. 공간을 _선택_하거나 Dev Spaces 도구를 사용하여 만들면 해당 네임스페이스에 Dev Spaces 계측이 추가됩니다.

다음은 단일 Kubernetes 클러스터에서 기능 개발, 'dev' 환경 _및_ 프로덕션 환경을 모두 지원하는 네임스페이스 구조 예제입니다.

![네임스페이스 구조 예제](../media/common/cicd-namespaces.png)

> [!Tip]
> `prod` 공간을 이미 만들었으며 단순히 Dev Spaces 계측에서 제외하려는 경우(삭제하지 않음), 다음 Dev Spaces CLI 명령을 사용하여 이와 같이 할 수 있습니다.
>
> `azds space remove -n prod --no-delete`
>
> Dev Spaces 계측 없이 다시 만들 수 있도록 이 작업을 수행한 후에 `prod` 네임스페이스에서 모든 pod를 삭제해야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Dev Spaces을 사용하는 팀 개발에 대해 알아보기](../team-development-netcore.md)