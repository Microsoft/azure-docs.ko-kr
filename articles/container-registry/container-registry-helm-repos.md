---
title: Azure Container Registry에서 Helm 리포지토리 사용
description: Azure Container Registry에서 Helm 리포지토리를 사용하여 애플리케이션용 차트를 저장하는 방법에 대해 알아봅니다.
services: container-registry
author: iainfoulds
ms.service: container-registry
ms.topic: article
ms.date: 09/24/2018
ms.author: iainfou
ms.openlocfilehash: ba0e1386d67e920f1805d244f9042044bb462ec9
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62109854"
---
# <a name="use-azure-container-registry-as-a-helm-repository-for-your-application-charts"></a>애플리케이션 차트용 Helm 리포지토리로 Azure Container Registry 사용

Kubernetes용 애플리케이션을 빠르게 관리하고 배포하려는 경우 [오픈 소스 Helm 패키지 관리자][helm]를 사용할 수 있습니다. Helm 사용 시 애플리케이션은 Helm 차트 리포지토리에 저장되는 *차트*로 정의됩니다. 이러한 차트는 구성 및 종속성을 정의하며, 애플리케이션의 전체 수명 주기에 걸쳐 차트의 버전을 관리할 수 있습니다. Helm 차트 리포지토리용 호스트로 Azure Container Registry를 사용할 수 있습니다.

Azure Container Registry를 사용하는 경우 빌드 파이프라인 또는 기타 Azure 서비스와 통합할 수 있는 비공개 보안 Helm 차트 리포지토리가 제공됩니다. Azure Container Registry의 Helm 차트 리포지토리에는 차트를 배포한 응용 프로그램과 가까운 위치에 보관하고 중복성을 유지하기 위한 지역 복제 기능이 포함되어 있습니다. 차트는 모든 Azure Container Registry 기준 가격에서 사용 가능하며, 차트에서 사용한 저장소 양만큼만 요금을 결제하면 됩니다.

이 문서에서는 Azure Container Registry에 저장된 Helm 차트 리포지토리를 사용하는 방법을 설명합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. [추가 사용 조건][terms-of-use]에 동의하는 조건으로 미리 보기를 사용할 수 있습니다. 이 기능의 몇 가지 측면은 일반 공급(GA) 전에 변경될 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 다음 필수 구성 요소를 갖춰야 합니다.

- **Azure Container Registry** - Azure 구독 내에서 컨테이너 레지스트리를 만듭니다. 예를 들어 [Azure Portal](container-registry-get-started-portal.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용합니다.
- **Helm 클라이언트 버전 2.11.0(RC 버전 아님) 이상** - `helm version`을 실행하여 현재 버전을 찾습니다. Kubernetes 클러스터 내에서 Helm 서버(Tiller)도 초기화해야 합니다. 필요한 경우 [Azure Kubernetes Service 클러스터 만들기][aks-quickstart]를 수행할 수 있습니다. Helm을 설치하고 업그레이드하는 방법에 대한 자세한 내용은 [Helm 설치][helm-install]를 참조하세요.
- **Azure CLI 버전 2.0.46 이상** - `az --version`을 실행하여 버전을 확인합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="add-a-repository-to-helm-client"></a>Helm 클라이언트에 리포지토리 추가

Helm 리포지토리는 Helm 차트를 저장할 수 있는 HTTP 서버입니다. Azure Container Registry는 Helm 차트용으로 이 저장소를 제공할 수 있으며, 리포지토리에 차트를 추가하고 제거할 때 인덱스 정의를 관리합니다.

Azure Container Registry를 Helm 차트 리포지토리로 추가하려면 Azure CLI를 사용합니다. 이 방식을 사용하는 경우 Helm 클라이언트는 Azure Container Registry를 통해 지원되는 리포지토리용 URI와 자격 증명을 사용하여 업데이트됩니다. 이 리포지토리 정보는 수동으로 지정할 필요가 없으므로 명령 기록 등에 자격 증명이 노출되지 않습니다.

필요한 경우 Azure CLI에 로그인하여 표시되는 메시지를 따릅니다.

```azurecli
az login
```

[az configure][az-configure] 명령을 사용하여 Azure Container Registry의 이름으로 Azure CLI 기본값을 구성합니다. 다음 예제에서는 `<acrName>`을 실제 레지스트리의 이름으로 바꿉니다.

```azurecli
az configure --defaults acr=<acrName>
```

이제 [az acr helm repo add][az-acr-helm-repo-add] 명령을 사용하여 Helm 클라이언트에 Azure Container Registry Helm 차트 리포지토리를 추가합니다. 이 명령은 Helm 클라이언트에 사용되는 Azure Container Registry용 인증 토큰을 가져옵니다. 인증 토큰은 1시간 동안 유효합니다. `docker login`과 마찬가지로 이 명령도 이후 CLI 세션에서 실행하여 Azure Container Registry Helm 차트 리포지토리에 Helm 클라이언트를 인증할 수 있습니다.

```azurecli
az acr helm repo add
```

## <a name="add-a-chart-to-the-repository"></a>리포지토리에 차트 추가

이 문서에서는 공용 Helm *stable* 리포지토리에서 기존 Helm 차트를 가져옵니다. *stable* 리포지토리는 일반적인 애플리케이션 차트를 포함하는 큐레이팅된 공용 리포지토리입니다. 패키지 유지 관리자는 Docker Hub에서 일반 컨테이너 이미지용으로 공용 레지스트리를 제공하는 것과 같은 방식으로 *stable* 리포지토리에 차트를 제출할 수 있습니다. 공용 *stable* 리포지토리에서 다운로드한 차트는 개인 Azure Container Registry 리포지토리로 푸시할 수 있습니다. 대부분의 시나리오에서는 개발하는 애플리케이션용으로 차트를 직접 작성하여 업로드합니다. Helm 차트를 직접 작성하는 방법에 대한 자세한 내용은 [Helm 차트 개발][develop-helm-charts]을 참조하세요.

먼저 *~/acr-helm*에 디렉터리를 만든 다음 기존 *stable/wordpress* 차트를 다운로드합니다.

```console
mkdir ~/acr-helm && cd ~/acr-helm
helm fetch stable/wordpress
```

다운로드한 차트 목록을 표시한 다음 파일 이름에 포함된 Wordpress 버전을 확인합니다. `helm fetch stable/wordpress` 명령은 특정 버전을 지정하지 않았으므로 *최신* 버전을 가져왔습니다. 모든 Helm 차트의 파일 이름에는 [SemVer 2][semver2] 표준을 따르는 버전 번호가 포함되어 있습니다. 다음 예제 출력에서는 Wordpress 차트는 버전 *2.1.10*입니다.

```
$ ls

wordpress-2.1.10.tgz
```

이제 Azure CLI [az acr helm push][az-acr-helm-push] 명령을 사용하여 Azure Container Registry의 Helm 차트 리포지토리에 차트를 푸시합니다. 이전 단계에서 다운로드한 Helm 차트의 이름을 *wordpress-2.1.10.tgz*와 같이 지정합니다.

```azurecli
az acr helm push wordpress-2.1.10.tgz
```

잠시 후 다음 예제 출력과 같이 Azure CLI에 차트가 저장되었다는 보고가 표시됩니다.

```
$ az acr helm push wordpress-2.1.10.tgz

{
  "saved": true
}
```

## <a name="list-charts-in-the-repository"></a>리포지토리의 차트 목록 표시

Helm 클라이언트는 원격 리포지토리 콘텐츠의 로컬 캐시된 복사본을 유지 관리합니다. 원격 리포지토리를 변경해도 Helm 클라이언트가 로컬에서 확인할 수 있는 사용 가능한 차트 목록은 자동으로 업데이트되지 않습니다. 여러 리포지토리에서 차트를 검색할 때 Helm은 로컬 캐시된 인덱스를 사용합니다. 이전 단계에서 업로드한 차트를 사용하려면 로컬 Helm 리포지토리 인덱스를 업데이트해야 합니다. Helm 클라이언트를 리포지토리를 다시 인덱싱할 수도 있고 Azure CLI를 사용하여 리포지토리 인덱스를 업데이트할 수도 있습니다. 리포지토리에 차트를 추가할 때마다 이 단계를 완료해야 합니다.

```azurecli
az acr helm repo add
```

리포지토리에 차트가 저장되어 있고 업데이트된 인덱스를 로컬에서 사용할 수 있으면 일반 Helm 클라이언트 명령을 사용해 차트를 검색하거나 설치할 수 있습니다. 리포지토리의 모든 차트를 확인하려면 `helm search <acrName>`을 사용합니다. <acrName>에는 실제 Azure Container Registry 이름을 입력합니다.

```console
helm search <acrName>
```

다음 예제 출력과 같이 이전 단계에서 푸시한 Wordpress 차트가 나열됩니다.

```
$ helm search myacrhelm

NAME                CHART VERSION   APP VERSION DESCRIPTION
helmdocs/wordpress  2.1.10          4.9.8       Web publishing platform for building blogs and websites.
```

Azure CLI에서 [az acr helm list][az-acr-helm-list]를 사용하여 차트 목록을 표시할 수도 있습니다.

```azurecli
az acr helm list
```

## <a name="show-information-for-a-helm-chart"></a>Helm 차트의 정보 표시

리포지토리의 특정 차트에 대한 정보를 보려는 경우에도 일반 Helm 클라이언트를 사용할 수 있습니다. *wordpress* 차트의 정보를 확인하려면 다음과 같이 `helm inspect`를 사용합니다.

```console
helm inspect <acrName>/wordpress
```

버전 번호를 제공하지 않으면 *최신* 버전이 사용됩니다. 다음의 축소된 예제 출력과 같이 Helm이 차트에 대한 세부 정보를 반환합니다.

```
$ helm inspect myacrhelm/wordpress

appVersion: 4.9.8
description: Web publishing platform for building blogs and websites.
engine: gotpl
home: https://www.wordpress.com/
icon: https://bitnami.com/assets/stacks/wordpress/img/wordpress-stack-220x234.png
keywords:
- wordpress
- cms
- blog
- http
- web
- application
- php
maintainers:
- email: containers@bitnami.com
  name: bitnami-bot
name: wordpress
sources:
- https://github.com/bitnami/bitnami-docker-wordpress
version: 2.1.10
[...]
```

Azure CLI [az acr helm show][az-acr-helm-show] 명령을 사용하여 차트의 정보를 표시할 수도 있습니다. 이 명령을 사용하는 경우에도 마찬가지로 차트의 *최신* 버전이 기본적으로 반환됩니다. `--version`을 추가하면 *2.1.10* 등의 특정 차트 버전을 나열할 수 있습니다.

```azurecli
az acr helm show wordpress
```

## <a name="install-a-helm-chart-from-the-repository"></a>리포지토리에서 Helm 차트 설치

리포지토리 이름과 차트 이름을 지정하는 방식을 통해 리포지토리의 Helm 차트를 설치합니다. Helm 클라이언트를 사용하여 Wordpress 차트를 설치하려면 다음 명령을 사용합니다.

```console
helm install <acrName>/wordpress
```

> [!TIP]
> Azure Container Registry Helm 차트 리포지토리로 푸시한 후 나중에 새 CLI 세션으로 돌아오는 경우 로컬 Helm 클라이언트에 업데이트된 인증 토큰이 필요합니다. 새 인증 토큰을 받으려면 [az acr helm repo add][az-acr-helm-repo-add] 명령을 사용합니다.

설치 프로세스 중에는 다음 단계가 완료됩니다.

- Helm 클라이언트가 로컬 리포지토리 인덱스를 검색합니다.
- Azure Container Registry 리포지토리에서 해당 차트가 다운로드됩니다.
- Kubernetes 클러스터의 Tiller를 사용하여 차트가 배포됩니다.

다음의 축소된 예제 출력에서는 Helm 차트를 통해 배포된 Kubernetes 리소스를 보여 줍니다.

```
$ helm install myacrhelm/wordpress

NAME:   irreverent-jaguar
LAST DEPLOYED: Thu Sep 13 21:44:20 2018
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                                          READY  STATUS   RESTARTS  AGE
irreverent-jaguar-wordpress-7ff46d9b8c-b7v6m  0/1    Pending  0         1s
irreverent-jaguar-mariadb-0                   0/1    Pending  0         1s
[...]
```

## <a name="delete-a-helm-chart-from-the-repository"></a>리포지토리에서 Helm 차트 삭제

리포지토리에서 차트를 삭제하려면 [az acr helm delete][az-acr-helm-delete] 명령을 사용합니다. 차트 이름(예: *wordpress*)과 삭제할 버전(예: *2.1.10*)을 지정합니다.

```azurecli
az acr helm delete wordpress --version 2.1.10
```

이름을 지정한 차트의 모든 버전을 삭제하려는 경우 `--version` 매개 변수를 제거합니다.

차트는 `helm search <acrName>`에서 계속 반환됩니다. 여기서도 Helm 클라이언트는 리포지토리에서 사용 가능한 차트 목록을 자동으로 업데이트하지 않습니다. Helm 클라이언트 리포지토리 인덱스를 업데이트하려면 다시 [az acr helm repo add][az-acr-helm-repo-add] 명령을 사용합니다.

```azurecli
az acr helm repo add
```

## <a name="next-steps"></a>다음 단계

이 문서에서는 공용 *stable* 리포지토리의 기존 Helm 차트를 사용했습니다. Helm 차트를 만들고 배포하는 방법에 대한 자세한 내용은 [Helm 차트 개발][develop-helm-charts]을 참조하세요.

Helm 차트는 컨테이너 빌드 프로세스의 일부로 사용할 수 있습니다. 자세한 내용은 [Azure Container Registry 작업 사용][acr-tasks]을 참조하세요.

Azure Container Registry를 사용하고 관리하는 방법에 대한 자세한 내용은 [모범 사례][acr-bestpractices]를 참조하세요.

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm
[develop-helm-charts]: https://docs.helm.sh/developing_charts/
[semver2]: https://semver.org/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az-configure
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-helm-repo-add]: /cli/azure/acr/helm/repo#az-acr-helm-repo-add
[az-acr-helm-push]: /cli/azure/acr/helm#az-acr-helm-push
[az-acr-helm-list]: /cli/azure/acr/helm#az-acr-helm-list
[az-acr-helm-show]: /cli/azure/acr/helm#az-acr-helm-show
[az-acr-helm-delete]: /cli/azure/acr/helm#az-acr-helm-delete
[acr-tasks]: container-registry-tasks-overview.md
