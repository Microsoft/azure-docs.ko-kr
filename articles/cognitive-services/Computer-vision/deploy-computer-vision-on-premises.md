---
title: Kubernetes 및 투구와 Computer Vision 컨테이너 사용
titleSuffix: Azure Cognitive Services
description: Computer Vision 컨테이너를 Azure Container Instance에 배포 하 고 웹 브라우저에서 테스트 합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 22ec16f66c463cde49adbc9c472e461169df5eeb
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383791"
---
# <a name="use-computer-vision-container-with-kubernetes-and-helm"></a>Kubernetes 및 투구와 Computer Vision 컨테이너 사용

온-프레미스 Computer Vision 컨테이너를 관리 하는 한 가지 옵션은 Kubernetes 및 투구를 사용 하는 것입니다. Kubernetes 및 투구를 사용 하 여 Computer Vision 컨테이너 이미지를 정의 합니다. Kubernetes 패키지를 만듭니다. 이 패키지는 온-프레미스 Kubernetes 클러스터에 배포 됩니다. 마지막으로 배포 된 서비스를 테스트 하는 방법을 살펴보겠습니다. Kubernetes 오케스트레이션을 사용 하지 않고 Docker 컨테이너를 실행 하는 방법에 대 한 자세한 내용은 [Computer Vision 컨테이너 설치 및 실행](computer-vision-how-to-install-containers.md)을 참조 하세요.

## <a name="prerequisites"></a>전제 조건

온-프레미스 Computer Vision 컨테이너를 사용 하기 전에 다음 필수 구성 요소가 필요 합니다.

|필수|용도|
|--|--|
| Azure 계정 | Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][free-azure-account]을 만듭니다. |
| Kubernetes CLI | [KUBERNETES CLI][kubernetes-cli] 는 컨테이너 레지스트리에서 공유 자격 증명을 관리 하는 데 필요 합니다. Kubernetes는 Kubernetes 패키지 관리자 인 투구 이전에도 필요 합니다. |
| 투구 CLI | [투구 CLI][helm-install] 를 설치 하는 과정에서 [Tiller][tiller-install]를 설치 하는 투구도 초기화 해야 합니다. |
| Computer Vision 리소스 |컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>Azure **Computer Vision** 리소스 및 연결 된 API 키 엔드포인트 URI입니다. 두 값은 모두 리소스의 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작 하는 데 필요 합니다.<br><br>**{API_KEY}** : **키** 페이지에서 사용 가능한 두 리소스 키 중 하나<br><br>**{ENDPOINT_URI}** : **개요** 페이지에 제공 된 엔드포인트입니다.|

[!INCLUDE [Gathering required parameters](../containers/includes/container-gathering-required-parameters.md)]

### <a name="the-host-computer"></a>호스트 컴퓨터

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>컨테이너 요구 사항 및 추천

[!INCLUDE [Container requirements and recommendations](includes/container-requirements-and-recommendations.md)]

## <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes 클러스터에 연결

호스트 컴퓨터에 사용 가능한 Kubernetes 클러스터가 있어야 합니다. Kubernetes 클러스터를 호스트 컴퓨터에 배포 하는 방법에 대 한 개념을 이해 하려면 [Kubernetes 클러스터 배포](../../aks/tutorial-kubernetes-deploy-cluster.md) 에 대 한이 자습서를 참조 하세요.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Kubernetes 클러스터를 사용 하 여 Docker 자격 증명 공유

Kubernetes 클러스터가 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 구성 된 이미지를 `docker pull` 수 있도록 하려면 docker 자격 증명을 클러스터로 전송 해야 합니다. 컨테이너 레지스트리 액세스 전제 조건에서 제공 된 자격 증명에 따라 *docker 레지스트리 암호* 를 만들려면 아래 [`kubectl create`][kubectl-create] 명령을 실행 합니다.

원하는 명령줄 인터페이스에서 다음 명령을 실행 합니다. `<username>`, `<password>`및 `<email-address>`를 컨테이너 레지스트리 자격 증명으로 바꾸어야 합니다.

```console
kubectl create secret docker-registry containerpreview \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> `containerpreview.azurecr.io` container registry에 이미 액세스할 수 있는 경우 대신 일반 플래그를 사용 하 여 Kubernetes 암호를 만들 수 있습니다. Docker 구성 JSON에 대해 실행 되는 다음 명령을 고려 합니다.
> ```console
>  kubectl create secret generic containerpreview \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

암호가 성공적으로 만들어지면 다음 출력이 콘솔에 인쇄 됩니다.

```console
secret "containerpreview" created
```

비밀이 만들어졌는지 확인 하려면 `secrets` 플래그를 사용 하 여 [`kubectl get`][kubectl-get] 를 실행 합니다.

```console
kubectl get secrets
```

`kubectl get secrets`를 실행 하면 구성 된 모든 암호가 인쇄 됩니다.

```console
NAME                  TYPE                                  DATA      AGE
containerpreview      kubernetes.io/dockerconfigjson        1         30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>배포에 대 한 투구 차트 값 구성

*Read*라는 폴더를 만든 다음, 다음 yaml 콘텐츠를 *Chart .yml*이라는 새 파일에 붙여 넣는 방법으로 시작 합니다.

```yaml
apiVersion: v1
name: read
version: 1.0.0
description: A Helm chart to deploy the microsoft/cognitive-services-read to a Kubernetes cluster
```

투구 차트 기본값을 구성 하려면 다음 YAML을 복사 하 여 `values.yaml`라는 파일에 붙여 넣습니다. `# {ENDPOINT_URI}` 및 `# {API_KEY}` 주석을 사용자 고유의 값으로 바꿉니다.

```yaml
# These settings are deployment specific and users can provide customizations

read:
  enabled: true
  image:
    name: cognitive-services-read
    registry: containerpreview.azurecr.io/
    repository: microsoft/cognitive-services-read
    tag: latest
    pullSecret: containerpreview # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> `billing` 및 `apikey` 값이 제공 되지 않은 경우 서비스는 15 분 후에 만료 됩니다. 마찬가지로, 서비스를 사용할 수 없게 되 면 확인이 실패 합니다.

*읽기* 디렉터리 아래에 *템플릿* 폴더를 만듭니다. 다음 YAML을 복사 하 여 `deployment.yaml`라는 파일에 붙여 넣습니다. `deployment.yaml` 파일은 투구 템플릿 역할을 합니다.

> 템플릿은 Kubernetes에서 이해할 수 있는 YAML 형식의 리소스 설명 인 매니페스트 파일을 생성 합니다. [-투구 차트 템플릿 가이드][chart-template-guide]

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: read
spec:
  template:
    metadata:
      labels:
        app: read-app
    spec:
      containers:
      - name: {{.Values.read.image.name}}
        image: {{.Values.read.image.registry}}{{.Values.read.image.repository}}
        ports:
        - containerPort: 5000
        env:
        - name: EULA
          value: {{.Values.read.image.args.eula}}
        - name: billing
          value: {{.Values.read.image.args.billing}}
        - name: apikey
          value: {{.Values.read.image.args.apikey}}
      imagePullSecrets:
      - name: {{.Values.read.image.pullSecret}}

--- 
apiVersion: v1
kind: Service
metadata:
  name: read
spec:
  type: LoadBalancer
  ports:
  - port: 5000
  selector:
    app: read-app
```

템플릿은 부하 분산 장치 서비스와 읽기를 위해 컨테이너/이미지 배포를 지정 합니다.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 패키지 (투구 차트)

*투구 차트* 에는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 끌어올 docker 이미지의 구성이 포함 되어 있습니다.

> [투구 차트][helm-charts] 는 관련 된 Kubernetes 리소스 집합을 설명 하는 파일의 컬렉션입니다. 단일 차트는 memcached pod 또는 HTTP 서버, 데이터베이스, 캐시 등의 전체 웹 앱 스택과 같이 복잡 한 항목을 배포 하는 데 사용할 수 있습니다.

제공 된 *투구 차트* 는 Computer Vision 서비스의 docker 이미지와 `containerpreview.azurecr.io` container registry에서 해당 하는 서비스를 가져옵니다.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes 클러스터에 투구 차트 설치

*투구 차트*를 설치 하려면 [`helm install`][helm-install-cmd] 명령을 실행 해야 합니다. `read` 폴더 위의 디렉터리에서 install 명령을 실행 해야 합니다.

```console
helm install read --name read
```

성공적인 설치 실행에서 볼 수 있는 출력의 예는 다음과 같습니다.

```console
NAME: read
LAST DEPLOYED: Thu Sep 04 13:24:06 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                    READY  STATUS             RESTARTS  AGE
read-57cb76bcf7-45sdh   0/1    ContainerCreating  0         0s

==> v1/Service
NAME     TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)         AGE
read     LoadBalancer  10.110.44.86  localhost    5000:31301/TCP  0s

==> v1beta1/Deployment
NAME    READY  UP-TO-DATE  AVAILABLE  AGE
read    0/1    1           0          0s
```

Kubernetes 배포를 완료 하는 데 몇 분 정도 걸릴 수 있습니다. Pod와 서비스가 올바르게 배포 되 고 사용 가능한 지 확인 하려면 다음 명령을 실행 합니다.

```console
kubectl get all
```

다음 출력과 유사한 내용이 표시 되어야 합니다.

```console
kubectl get all
NAME                        READY   STATUS    RESTARTS   AGE
pod/read-57cb76bcf7-45sdh   1/1     Running   0          17s

NAME                   TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
service/kubernetes     ClusterIP      10.96.0.1      <none>        443/TCP          45h
service/read           LoadBalancer   10.110.44.86   localhost     5000:31301/TCP   17s

NAME                   READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/read   1/1     1            1           17s

NAME                              DESIRED   CURRENT   READY   AGE
replicaset.apps/read-57cb76bcf7   1         1         1       17s
```
<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="next-steps"></a>다음 단계

AKS (Azure Kubernetes Service)에서 투구를 사용 하 여 응용 프로그램을 설치 하는 방법에 대 한 자세한 내용은 [여기를 참조][installing-helm-apps-in-aks]하세요.

> [!div class="nextstepaction"]
> [Cognitive Services 컨테이너][cog-svcs-containers]

<!-- LINKS - external -->
[free-azure-account]: https://azure.microsoft.com/free
[git-download]: https://git-scm.com/downloads
[azure-cli]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
[docker-engine]: https://www.docker.com/products/docker-engine
[kubernetes-cli]: https://kubernetes.io/docs/tasks/tools/install-kubectl
[helm-install]: https://helm.sh/docs/using_helm/#installing-helm
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://helm.sh/docs/helm/#helm-test
[chart-template-guide]: https://helm.sh/docs/chart_template_guide

<!-- LINKS - internal -->
[vision-container-host-computer]: computer-vision-how-to-install-containers.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
