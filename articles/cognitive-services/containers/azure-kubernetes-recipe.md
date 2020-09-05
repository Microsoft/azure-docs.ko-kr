---
title: Kubernetes Service에서 언어 감지 컨테이너 실행
titleSuffix: Text Analytics -  Azure Cognitive Services
description: 언어 감지 컨테이너와 실행 샘플을 Azure Kubernetes Service에 배포하고 웹 브라우저에서 테스트합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b53476bcb05d6e91b157c24795c963c04e6f4bb4
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/15/2020
ms.locfileid: "88244495"
---
# <a name="deploy-the-text-analytics-language-detection-container-to-azure-kubernetes-service"></a>Text Analytics 언어 검색 컨테이너를 Azure Kubernetes Service에 배포

언어 감지 컨테이너를 배포하는 방법을 알아봅니다. 이 절차에서는 로컬 Docker 컨테이너를 만들고, 컨테이너를 고유한 프라이빗 컨테이너 레지스트리로 푸시하고, Kubernetes 클러스터에서 컨테이너를 실행하고, 웹 브라우저에서 테스트하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>필수 구성 요소

이 절차를 수행하려면 로컬로 설치 및 실행해야 하는 몇 가지 도구가 필요합니다. Azure Cloud Shell은 사용하지 않도록 합니다.

* Azure 구독을 사용합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services)을 만듭니다.
* 이 절차에서 사용되는 [샘플](https://github.com/Azure-Samples/cognitive-services-containers-samples)을 복제할 수 있도록 사용하는 운영 체제용 [Git](https://git-scm.com/downloads)
* [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
* [Docker 엔진](https://www.docker.com/products/docker-engine). 콘솔 창에서 Docker CLI를 가 작동하는지 확인합니다.
* [kubectl](https://storage.googleapis.com/kubernetes-release/release/v1.13.1/bin/windows/amd64/kubectl.exe).
* 올바른 가격 책정 계층이 지정된 Azure 리소스. 모든 가격 책정 계층이 이 컨테이너에 작동하는 것은 아닙니다.
  * F0 또는 표준 가격 책정 계층만 있는 **Text Analytics** 리소스
  * S0 가격 책정 계층이 있는 **Cognitive Services** 리소스

## <a name="running-the-sample"></a>샘플 실행

이 절차에서는 언어 감지를 위해 Cognitive Services 컨테이너 샘플을 로드하고 실행합니다. 샘플은 클라이언트 애플리케이션용 1개, Cognitive Services 컨테이너용 1개, 모두 2개의 컨테이너를 포함합니다. 이러한 이미지를 모두 Azure Container Registry로 푸시합니다. 이미지가 사용자 고유의 레지스트리에 추가되면 Azure Kubernetes Service를 만들어 이러한 이미지에 액세스하고 컨테이너를 실행합니다. 컨테이너가 실행되는 동안 **kubectl** CLI를 사용하여 컨테이너 성능을 감시합니다. HTTP 요청을 사용하여 클라이언트 애플리케이션에 액세스하고 결과를 봅니다.

![샘플 컨테이너 실행에 대한 개념적 아이디어](../text-analytics/media/how-tos/container-instance-sample/containers.png)

## <a name="the-sample-containers"></a>샘플 컨테이너

샘플에는 2개의 컨테이너 이미지가 있으며 하나는 프런트 엔드 웹 사이트용입니다. 두 번째 이미지는 텍스트의 감지된 언어(문화권)를 반환하는 언어 감지 컨테이너입니다. 완료되면 두 컨테이너 모두 외부 IP에서 액세스할 수 있습니다.

### <a name="the-language-frontend-container"></a>언어-프런트 엔드 컨테이너

이 웹 사이트는 언어 감지 엔드포인트를 요청하는 고유한 클라이언트 쪽 애플리케이션과 같습니다. 이 절차가 완료되면 `http://<external-IP>/<text-to-analyze>`를 통해 브라우저에서 웹 사이트에 액세스하여 문자열의 감지된 언어를 얻게 합니다. 이 URL의 예는 `http://132.12.23.255/helloworld!`입니다. 브라우저의 결과는 `English`입니다.

### <a name="the-language-container"></a>언어 컨테이너

이 특정 절차에 나오는 언어 감지 컨테이너는 모든 외부 요청에서 액세스할 수 있습니다. 컨테이너는 어떤 방식으로든 변경되지 않았으므로 표준 Cognitive Services 컨테이너별 언어 감지 API를 사용할 수 있습니다.

이 컨테이너에서 해당 API는 언어 감지를 위한 POST 요청입니다. 모든 Cognitive Services 컨테이너와 마찬가지로, 호스트된 Swagger 정보 `http://<external-IP>:5000/swagger/index.html`에서 컨테이너에 대해 자세히 알아볼 수 있습니다.

포트 5000은 Cognitive Services 컨테이너에 사용되는 기본 포트입니다.

## <a name="create-azure-container-registry-service"></a>Azure Container Registry 서비스 만들기

Azure Kubernetes Service에 컨테이너를 배포하려면 컨테이너 이미지에 액세스할 수 있어야 합니다. 이미지를 호스트할 고유한 Azure Container Registry 서비스를 만듭니다.

1. Azure CLI에 로그인

    ```azurecli-interactive
    az login
    ```

1. 이 절차에서 만든 모든 리소스를 저장할 `cogserv-container-rg`라는 리소스 그룹을 만듭니다.

    ```azurecli-interactive
    az group create --name cogserv-container-rg --location westus
    ```

1. 사용자 이름과 `registry` 형식(예: `pattyregistry`)으로 고유한 Azure Container Registry를 만듭니다. 이름에 대시나 밑줄 문자를 사용하지 마세요.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    결과를 저장하여 **loginServer** 속성을 가져옵니다. 이 속성은 나중에 `language.yml` 파일에서 사용되는 호스트된 컨테이너 주소의 일부가 됩니다.

    ```azurecli-interactive
    az acr create --resource-group cogserv-container-rg --name pattyregistry --sku Basic
    ```

    ```output
    {
        "adminUserEnabled": false,
        "creationDate": "2019-01-02T23:49:53.783549+00:00",
        "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry",
        "location": "westus",
        "loginServer": "pattyregistry.azurecr.io",
        "name": "pattyregistry",
        "provisioningState": "Succeeded",
        "resourceGroup": "cogserv-container-rg",
        "sku": {
            "name": "Basic",
            "tier": "Basic"
        },
        "status": null,
        "storageAccount": null,
        "tags": {},
        "type": "Microsoft.ContainerRegistry/registries"
    }
    ```

1. 컨테이너 레지스트리에 로그인합니다. 레지스트리에 이미지를 푸시하려면 먼저 로그인해야 합니다.

    ```azurecli-interactive
    az acr login --name pattyregistry
    ```

## <a name="get-website-docker-image"></a>웹 사이트 Docker 이미지 가져오기

1. 이 절차에 사용되는 샘플 코드는 Cognitive Services 컨테이너 샘플 리포지토리에 있습니다. 리포지토리를 복제하여 샘플의 로컬 복사본을 만듭니다.

    ```console
    git clone https://github.com/Azure-Samples/cognitive-services-containers-samples
    ```

    리포지토리가 로컬 컴퓨터에 있으면 [\dotnet\Language\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) 디렉터리에서 웹 사이트를 찾습니다. 이 웹 사이트는 언어 감지 컨테이너에 호스트되는 언어 감지 API를 호출하는 클라이언트 애플리케이션으로 작동합니다.  

1. 이 웹 사이트에 대한 Docker 이미지를 빌드합니다. 콘솔은 다음 명령을 실행할 때 Dockerfile이 위치하는 [\FrontendService](https://github.com/Azure-Samples/cognitive-services-containers-samples/tree/master/dotnet/Language/FrontendService) 디렉터리에 있습니다.

    ```console
    docker build -t language-frontend -t pattiyregistry.azurecr.io/language-frontend:v1 .
    ```

    컨테이너 레지스트리에 버전을 추적하려면 버전 형식의 태그(예: `v1`)를 추가합니다.

1. 이미지를 컨테이너 레지스트리에 푸시합니다. 몇 분이 걸릴 수 있습니다.

    ```console
    docker push pattyregistry.azurecr.io/language-frontend:v1
    ```

    `unauthorized: authentication required` 오류가 발생하면 `az acr login --name <your-container-registry-name>` 명령을 사용하여 로그인합니다. 

    프로세스가 완료되면 결과는 다음과 비슷합니다.

    ```output
    The push refers to repository [pattyregistry.azurecr.io/language-frontend]
    82ff52ee6c73: Pushed
    07599c047227: Pushed
    816caf41a9a1: Pushed
    2924be3aed17: Pushed
    45b83a23806f: Pushed
    ef68f6734aa4: Pushed
    v1: digest: sha256:31930445deee181605c0cde53dab5a104528dc1ff57e5b3b34324f0d8a0eb286 size: 1580
    ```

## <a name="get-language-detection-docker-image"></a>언어 감지 Docker 이미지 가져오기

1. 최신 버전의 Docker 이미지를 로컬 컴퓨터로 끌어옵니다. 몇 분이 걸릴 수 있습니다. 이 컨테이너의 최신 버전이 있으면 `1.1.006770001-amd64-preview`의 값을 최신 버전으로 변경합니다.

    ```console
    docker pull mcr.microsoft.com/azure-cognitive-services/language:1.1.006770001-amd64-preview
    ```

1. 이미지에 컨테이너 레지스트리 태그를 지정합니다. 최신 버전을 찾은 경우 버전 `1.1.006770001-amd64-preview`를 최신 버전으로 바꿉니다. 

    ```console
    docker tag mcr.microsoft.com/azure-cognitive-services/language pattiyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

1. 이미지를 컨테이너 레지스트리에 푸시합니다. 몇 분이 걸릴 수 있습니다.

    ```console
    docker push pattyregistry.azurecr.io/language:1.1.006770001-amd64-preview
    ```

## <a name="get-container-registry-credentials"></a>컨테이너 레지스트리 자격 증명 가져오기

다음 단계는 이 절차의 뒷부분에서 만든 Azure Kubernetes Service에 컨테이너 레지스트리를 연결하는 데 필요한 정보를 가져오는 데 필요합니다.

1. 서비스 주체를 만듭니다.

    ```azurecli-interactive
    az ad sp create-for-rbac --skip-assignment
    ```

    assignee 매개 변수에 대한 결과 `appId` 값을 3단계 `<appId>`에 저장합니다. 다음 섹션의 클라이언트-암호 매개 변수 `<client-secret>`을 위해 `password`를 저장합니다.

    ```output
    {
      "appId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "displayName": "azure-cli-2018-12-31-18-39-32",
      "name": "http://azure-cli-2018-12-31-18-39-32",
      "password": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "tenant": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    }
    ```

1. 컨테이너 레지스트리 ID를 가져옵니다.

    ```azurecli-interactive
    az acr show --resource-group cogserv-container-rg --name pattyregistry --query "id" --o table
    ```

    범위 매개 변수 값의 출력 `<acrId>`를 다음 단계에 저장합니다. 다음과 같이 표시됩니다.

    ```output
    /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/cogserv-container-rg/providers/Microsoft.ContainerRegistry/registries/pattyregistry
    ```

    3단계의 전체 값을 이 섹션에 저장합니다.

1. ACR 클러스터에 대해 올바른 액세스 권한을 부여하여 컨테이너 레지스트리에 저장된 이미지를 사용하도록 하려면 역할 할당을 만듭니다. `<appId>` 및 `<acrId>`를 이전 두 단계에서 수집한 값으로 바꿉니다.

    ```azurecli-interactive
    az role assignment create --assignee <appId> --scope <acrId> --role Reader
    ```

## <a name="create-azure-kubernetes-service"></a>Azure Kubernetes Service 만들기

1. Kubernetes 클러스터를 만듭니다. name 매개 변수를 제외한 모든 매개 변수는 이전 섹션에서 가져옵니다. 만든 사람과 용도를 나타내는 이름을 선택합니다(예: `patty-kube`).

    ```azurecli-interactive
    az aks create --resource-group cogserv-container-rg --name patty-kube --node-count 2  --service-principal <appId>  --client-secret <client-secret>  --generate-ssh-keys
    ```

    이 단계를 완료하는 데 몇 분이 걸릴 수 있습니다. 결과는 다음과 같습니다.

    ```output
    {
      "aadProfile": null,
      "addonProfiles": null,
      "agentPoolProfiles": [
        {
          "count": 2,
          "dnsPrefix": null,
          "fqdn": null,
          "maxPods": 110,
          "name": "nodepool1",
          "osDiskSizeGb": 30,
          "osType": "Linux",
          "ports": null,
          "storageProfile": "ManagedDisks",
          "vmSize": "Standard_DS1_v2",
          "vnetSubnetId": null
        }
      ],
      "dnsPrefix": "patty-kube--65a101",
      "enableRbac": true,
      "fqdn": "patty-kube--65a101-341f1f54.hcp.westus.azmk8s.io",
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/cogserv-container-rg/providers/Microsoft.ContainerService/managedClusters/patty-kube",
      "kubernetesVersion": "1.9.11",
      "linuxProfile": {
        "adminUsername": "azureuser",
        "ssh": {
          "publicKeys": [
            {
              "keyData": "ssh-rsa AAAAB3NzaC...ohR2d81mFC
            }
          ]
        }
      },
      "location": "westus",
      "name": "patty-kube",
      "networkProfile": {
        "dnsServiceIp": "10.0.0.10",
        "dockerBridgeCidr": "172.17.0.1/16",
        "networkPlugin": "kubenet",
        "networkPolicy": null,
        "podCidr": "10.244.0.0/16",
        "serviceCidr": "10.0.0.0/16"
      },
      "nodeResourceGroup": "MC_patty_westus",
      "provisioningState": "Succeeded",
      "resourceGroup": "cogserv-container-rg",
      "servicePrincipalProfile": {
        "clientId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "keyVaultSecretRef": null,
        "secret": null
      },
      "tags": null,
      "type": "Microsoft.ContainerService/ManagedClusters"
    }
    ```

    서비스가 만들어지지만 아직 웹 사이트 컨테이너나 언어 감지 컨테이너는 포함하지 않습니다.  

1. Kubernetes 클러스터의 자격 증명을 가져옵니다.

    ```azurecli-interactive
    az aks get-credentials --resource-group cogserv-container-rg --name patty-kube
    ```

## <a name="load-the-orchestration-definition-into-your-kubernetes-service"></a>오케스트레이션 정의를 Kubernetes 서비스로 로드

이 섹션에서는 **kubectl** CLI를 사용하여 Azure Kubernetes Service와 통신합니다.

1. 오케스트레이션 정의를 로드하기 전에 **kubectl**에 노드에 대한 액세스 권한이 있는지 확인합니다.

    ```console
    kubectl get nodes
    ```

    응답은 다음과 같습니다.

    ```output
    NAME                       STATUS    ROLES     AGE       VERSION
    aks-nodepool1-13756812-0   Ready     agent     6m        v1.9.11
    aks-nodepool1-13756812-1   Ready     agent     6m        v1.9.11
    ```

1. 다음 파일을 복사하고 이름을 `language.yml`로 지정합니다. 이 파일에는 두 컨테이너 유형인 `language-frontend` 웹 사이트 컨테이너 및 `language` 검색 컨테이너 각각에 대한 `service` 섹션 및 `deployment` 섹션이 있습니다.

    [!code-yml[Kubernetes orchestration file for the Cognitive Services containers sample](~/samples-cogserv-containers/Kubernetes/language/language.yml "Kubernetes orchestration file for the Cognitive Services containers sample")]

1. 다음 표에 따라 `language.yml`의 언어-프런트 엔드 배포 줄을 변경하여 고유한 컨테이너 레지스트리에 이미지 이름, 클라이언트 비밀 및 텍스트 분석 설정을 추가합니다.

    언어-프런트 엔드 배포 설정|용도|
    |--|--|
    |줄 32<br> `image` 속성|Container Registry에 있는 프런트 엔드 이미지의 이미지 위치입니다.<br>`<container-registry-name>.azurecr.io/language-frontend:v1`|
    |줄 44<br> `name` 속성|이전 섹션에서 `<client-secret>`으로 나타낸 이미지의 컨테이너 레지스트리 비밀입니다.|

1. 다음 표에 따라 `language.yml`의 언어 배포 줄을 변경하여 고유한 컨테이너 레지스트리에 이미지 이름, 클라이언트 비밀 및 텍스트 분석 설정을 추가합니다.

    |언어 배포 설정|용도|
    |--|--|
    |줄 78<br> `image` 속성|Container Registry에 있는 언어 이미지의 이미지 위치입니다.<br>`<container-registry-name>.azurecr.io/language:1.1.006770001-amd64-preview`|
    |줄 95<br> `name` 속성|이전 섹션에서 `<client-secret>`으로 나타낸 이미지의 컨테이너 레지스트리 비밀입니다.|
    |줄 91<br> `apiKey` 속성|Text Analytics 리소스 키|
    |줄 92<br> `billing` 속성|Text Analytics 리소스의 청구 엔드포인트입니다.<br>`https://westus.api.cognitive.microsoft.com/text/analytics/v2.1`|

    **apiKey** 및 **청구 엔드포인트**는 Kubernetes 오케스트레이션 정의의 일부로 설정되므로 웹 사이트 컨테이너가 이러한 항목을 알아야 하거나 요청의 일부로 전달해야 할 필요가 없습니다. 웹 사이트 컨테이너는 오케스트레이터 이름 `language`로 언어 감지 컨테이너를 나타냅니다.

1. `language.yml`을 만들고 저장한 폴더에서 이 샘플의 오케스트레이션 정의 파일을 로드합니다.

    ```console
    kubectl apply -f language.yml
    ```

    응답은 다음과 같습니다.

    ```output
    service "language-frontend" created
    deployment.apps "language-frontend" created
    service "language" created
    deployment.apps "language" created
    ```

## <a name="get-external-ips-of-containers"></a>컨테이너의 외부 IP 가져오기

두 컨테이너에서 `language-frontend` 및 `language` 서비스가 실행되고 있는지 확인하고 외부 IP 주소를 가져옵니다.

```console
kubectl get all
```

```output
NAME                                     READY     STATUS    RESTARTS   AGE
pod/language-586849d8dc-7zvz5            1/1       Running   0          13h
pod/language-frontend-68b9969969-bz9bg   1/1       Running   1          13h

NAME                        TYPE           CLUSTER-IP    EXTERNAL-IP     PORT(S)          AGE
service/kubernetes          ClusterIP      10.0.0.1      <none>          443/TCP          14h
service/language            LoadBalancer   10.0.39.169   104.42.172.68   5000:30161/TCP   13h
service/language-frontend   LoadBalancer   10.0.42.136   104.42.37.219   80:30943/TCP     13h

NAME                                      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.extensions/language            1         1         1            1           13h
deployment.extensions/language-frontend   1         1         1            1           13h

NAME                                                 DESIRED   CURRENT   READY     AGE
replicaset.extensions/language-586849d8dc            1         1         1         13h
replicaset.extensions/language-frontend-68b9969969   1         1         1         13h

NAME                                DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/language            1         1         1            1           13h
deployment.apps/language-frontend   1         1         1            1           13h

NAME                                           DESIRED   CURRENT   READY     AGE
replicaset.apps/language-586849d8dc            1         1         1         13h
replicaset.apps/language-frontend-68b9969969   1         1         1         13h
```

서비스의 `EXTERNAL-IP`가 보류 상태로 표시되면 다음 단계로 이동하기 전에 IP 주소가 표시될 때까지 명령을 다시 실행합니다.

## <a name="test-the-language-detection-container"></a>언어 감지 컨테이너 테스트

브라우저를 열고 이전 섹션에 나오는 `language` 컨테이너의 외부 IP로 이동합니다. `http://<external-ip>:5000/swagger/index.html` API의 `Try it` 기능을 사용하여 언어 감지 엔드포인트를 테스트합니다.

![컨테이너의 swagger 설명서 보기](../text-analytics/media/how-tos/container-instance-sample/language-detection-container-swagger-documentation.png)

## <a name="test-the-client-application-container"></a>클라이언트 애플리케이션 컨테이너 테스트

`http://<external-ip>/helloworld` 형식을 사용하여 브라우저의 URL을 `language-frontend` 컨테이너의 외부 IP로 변경합니다. 영어 문화권 텍스트인 `helloworld`는 `English`로 예측됩니다.

## <a name="clean-up-resources"></a>리소스 정리

클러스터가 완료되면 Azure 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name cogserv-container-rg
```

## <a name="related-information"></a>관련 정보

* [Docker 사용자용 kubectl](https://kubernetes.io/docs/reference/kubectl/docker-cli-to-kubectl/)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너](../cognitive-services-container-support.md)
