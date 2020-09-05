---
title: Azure의 Service Fabric에서 컨테이너 이미지 만들기
description: 이 자습서에서는 다중 컨테이너 Service Fabric 애플리케이션에 대한 컨테이너 이미지를 만드는 방법을 알아봅니다.
author: suhuruli
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: suhuruli
ms.custom: mvc
ms.openlocfilehash: fe06da759a1ad42ef5cef888f98c440cdfb9569c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "78252780"
---
# <a name="tutorial-create-container-images-on-a-linux-service-fabric-cluster"></a>자습서: Linux Service Fabric 클러스터에서 컨테이너 이미지 만들기

이 자습서는 Linux Service Fabric 클러스터에서 컨테이너를 사용하는 방법을 보여 주는 자습서 시리즈의 1부입니다. 이 자습서에서는 Service Fabric과 함께 사용할 수 있도록 다중 컨테이너 애플리케이션이 준비됩니다. 후속 자습서에서 이러한 이미지는 Service Fabric 애플리케이션의 일부로 사용됩니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * GitHub에서 애플리케이션 원본 복제
> * 애플리케이션 원본에서 컨테이너 이미지 만들기
> * ACR(Azure Container Registry) 인스턴스 배포
> * ACR에 대한 컨테이너 이미지 태그 지정
> * ACR에 이미지 업로드

이 자습서 시리즈에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * Service Fabric에 대한 컨테이너 이미지 만들기
> * [컨테이너를 사용하여 Service Fabric 애플리케이션 빌드 및 실행](service-fabric-tutorial-package-containers.md)
> * [Service Fabric에서 장애 조치(failover) 및 크기 조정이 처리되는 방법](service-fabric-tutorial-containers-failover.md)

## <a name="prerequisites"></a>사전 요구 사항

* Service Fabric에 대한 Linux 개발 환경 설정 [여기](service-fabric-get-started-linux.md)의 지침을 따라 Linux 환경을 설정합니다.
* 이 자습서에는 Azure CLI 버전 2.0.4 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.
* 또한 사용 가능한 Azure 구독이 있어야 합니다. 평가판에 자세한 내용은 [여기](https://azure.microsoft.com/free/)로 이동합니다.

## <a name="get-application-code"></a>애플리케이션 코드 가져오기

이 자습서에서 사용되는 애플리케이션 예제는 투표 앱입니다. 애플리케이션은 프런트 엔드 웹 구성 요소 및 백 엔드 Redis 인스턴스로 구성됩니다. 구성 요소는 컨테이너 이미지로 패키지됩니다.

Git을 사용하여 개발 환경에 애플리케이션 복사본을 다운로드합니다.

```bash
git clone https://github.com/Azure-Samples/service-fabric-containers.git

cd service-fabric-containers/Linux/container-tutorial/
```

솔루션에는 두 개의 폴더 및 ‘docker-compse.yml’ 파일이 포함됩니다. 'azure-vote' 폴더는 이미지를 빌드하는 데 사용되는 Dockerfile과 함께 Python 프런트 엔드 서비스를 포함합니다. 'Voting' 디렉터리는 클러스터에 배포된 Service Fabric 애플리케이션 패키지를 포함합니다. 이러한 디렉터리는 이 자습서에 필요한 자산을 포함합니다.

## <a name="create-container-images"></a>컨테이너 이미지 만들기

**azure-vote** 디렉터리 내에서 다음 명령을 실행하여 프런트 엔드 웹 구성 요소에 대한 이미지를 빌드합니다. 이 명령은 이 디렉터리에서 Dockerfile을 사용하여 이미지를 빌드합니다.

```bash
docker build -t azure-vote-front .
```
> [!Note]
> 사용 권한이 거부된 경우 sudo 없이 Docker를 사용하는 방법에 대해 [이](https://docs.docker.com/install/linux/linux-postinstall/#manage-docker-as-a-non-root-user) 설명서를 따릅니다.

이 명령은 필요한 모든 종속성을 Docker 허브에서 가져와야 하므로 다소 시간이 걸릴 수 있습니다. 완료되면 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용하여 만든 이미지를 확인합니다.

```bash
docker images
```

두 개의 이미지가 다운로드되거나 생성되었는지 확인합니다. *azure-vote-front* 이미지는 애플리케이션을 포함합니다. Docker 허브의 *python* 이미지에서 파생되었습니다.

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB

```

## <a name="deploy-azure-container-registry"></a>Azure Container Registry 배포

먼저 **az login** 명령을 실행하여 Azure 계정에 로그인합니다.

```azurecli
az login
```

다음으로 **az account** 명령을 사용하여 Azure Container Registry를 만들 구독을 선택합니다. <subscription_id> 대신 Azure 구독의 구독 ID를 입력해야 합니다.

```azurecli
az account set --subscription <subscription_id>
```

Azure Container Registry를 배포할 때는 먼저 리소스 그룹이 필요합니다. Azure 리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다.

**az group create** 명령을 사용하여 리소스 그룹을 만듭니다. 이 예제에서는 *westus* 지역에 *myResourceGroup*이라는 리소스 그룹을 만듭니다.

```azurecli
az group create --name <myResourceGroup> --location westus
```

**az acr create** 명령으로 Azure Container Registry를 만듭니다. \<acrName>을 구독에서 만들려는 컨테이너 레지스트리의 이름으로 대체합니다. 이 이름은 영숫자이며 고유해야 합니다.

```azurecli
az acr create --resource-group <myResourceGroup> --name <acrName> --sku Basic --admin-enabled true
```

이 자습서의 나머지 부분에서는 선택한 컨테이너 레지스트리 이름의 자리 표시자로 "acrName"을 사용합니다. 이 값을 적어 두세요.

## <a name="sign-in-to-your-container-registry"></a>컨테이너 레지스트리에 로그인

이미지를 푸시하기 전에 먼저 ACR 인스턴스에 로그인합니다. **az acr login** 명령을 사용하여 작업을 완료합니다. 컨테이너 레지스트리가 생성될 때 지정된 고유한 이름을 입력합니다.

```azurecli
az acr login --name <acrName>
```

이 명령은 완료되면 ‘로그인했습니다.’ 메시지를 반환합니다.

## <a name="tag-container-images"></a>컨테이너 이미지 태그 지정

각 컨테이너 이미지에 레지스트리의 loginServer 이름으로 태그를 지정해야 합니다. 이 태그는 컨테이너 이미지를 이미지 레지스트리에 밀어넣을 때 라우팅에 사용됩니다.

현재 이미지 목록을 보려면 [docker images](https://docs.docker.com/engine/reference/commandline/images/) 명령을 사용합니다.

```bash
docker images
```

출력:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              052c549a75bf        About a minute ago   708MB
tiangolo/uwsgi-nginx-flask   python3.6           590e17342131        5 days ago           707MB
```

loginServer 이름을 가져오려면 다음 명령을 실행합니다.

```azurecli
az acr show --name <acrName> --query loginServer --output table
```

다음과 같은 결과가 있는 테이블을 출력합니다. 이 결과는 다음 단계에서 컨테이너 레지스트리에 푸시하기 전에 **azure-vote-front** 이미지의 태그를 지정하는 데 사용됩니다.

```output
Result
------------------
<acrName>.azurecr.io
```

이제 *azure-vote-front* 이미지에 컨테이너 레지스트리의 loginServer로 태그를 지정합니다. 또한 이미지 이름 끝에 `:v1`을 추가합니다. 이 태그는 이미지 버전을 나타냅니다.

```bash
docker tag azure-vote-front <acrName>.azurecr.io/azure-vote-front:v1
```

태그가 지정되면 'docker images'를 실행하여 작업을 확인합니다.

출력:

```output
REPOSITORY                             TAG                 IMAGE ID            CREATED             SIZE
azure-vote-front                       latest              052c549a75bf        23 minutes ago      708MB
<acrName>.azurecr.io/azure-vote-front   v1                  052c549a75bf       23 minutes ago      708MB
tiangolo/uwsgi-nginx-flask             python3.6           590e17342131        5 days ago          707MB

```

## <a name="push-images-to-registry"></a>레지스트리에 이미지 푸시

레지스트리에 *azure-vote-front* 이미지를 밀어넣습니다. 

다음 예제를 사용하여 ACR loginServer 이름을 해당 환경의 loginServer로 바꿉니다.

```bash
docker push <acrName>.azurecr.io/azure-vote-front:v1
```

docker 밀어넣기 명령이 완료되려면 몇 분 정도 걸립니다.

## <a name="list-images-in-registry"></a>레지스트리에서 이미지 나열

Azure Container Registry로 푸시한 이미지 목록을 반환하려면 [az acr repository list](/cli/azure/acr/repository) 명령을 사용합니다. ACR 인스턴스 이름으로 명령을 업데이트합니다.

```azurecli
az acr repository list --name <acrName> --output table
```

출력:

```output
Result
----------------
azure-vote-front
```

자습서를 완료하면 프라이빗 Azure Container Registry 인스턴스에 컨테이너 이미지가 저장됩니다. 이후 자습서에서 이 이미지는 ACR에서 Service Fabric 클러스터로 배포됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 GitHub에서 애플리케이션을 끌어오고 컨테이너 이미지를 만들고 레지스트리로 밀어 넣었습니다. 다음 단계가 완료되었습니다.

> [!div class="checklist"]
> * GitHub에서 애플리케이션 원본 복제
> * 애플리케이션 원본에서 컨테이너 이미지 만들기
> * ACR(Azure Container Registry) 인스턴스 배포
> * ACR에 대한 컨테이너 이미지 태그 지정
> * ACR에 이미지 업로드

Yeoman을 사용하여 Service Fabric 애플리케이션으로 컨테이너 패키지에 대해 자세히 알아보려면 다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [Service Fabric 애플리케이션으로 컨테이너 패키지 및 배포](service-fabric-tutorial-package-containers.md)
