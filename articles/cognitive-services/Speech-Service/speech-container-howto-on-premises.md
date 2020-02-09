---
title: Kubernetes 및 투구와 함께 Speech service 컨테이너 사용
titleSuffix: Azure Cognitive Services
description: Kubernetes 및 투구를 사용 하 여 음성 텍스트 및 텍스트 음성 변환 컨테이너 이미지를 정의 합니다. Kubernetes 패키지를 만듭니다. 이 패키지는 온-프레미스 Kubernetes 클러스터에 배포 됩니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: 7874a6b274939c233dd1c4e6d146df2a9a409e65
ms.sourcegitcommit: f53cd24ca41e878b411d7787bd8aa911da4bc4ec
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75833999"
---
# <a name="use-speech-service-containers-with-kubernetes-and-helm"></a>Kubernetes 및 투구와 함께 Speech service 컨테이너 사용

음성 컨테이너 온-프레미스를 관리 하는 한 가지 옵션은 Kubernetes 및 투구를 사용 하는 것입니다. Kubernetes 및 투구를 사용 하 여 음성 텍스트 및 텍스트 음성 변환 컨테이너 이미지를 정의 합니다. Kubernetes 패키지를 만듭니다. 이 패키지는 온-프레미스 Kubernetes 클러스터에 배포 됩니다. 마지막으로 배포 된 서비스와 다양 한 구성 옵션을 테스트 하는 방법을 살펴보겠습니다. Kubernetes 오케스트레이션을 사용 하지 않고 Docker 컨테이너를 실행 하는 방법에 대 한 자세한 내용은 [Speech service 컨테이너 설치 및 실행](speech-container-howto.md)을 참조 하세요.

## <a name="prerequisites"></a>필수 조건

온-프레미스에서 음성 컨테이너를 사용 하기 전에 다음 필수 구성 요소가 필요 합니다.

|필수|용도|
|--|--|
| Azure 계정 | Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정][free-azure-account]을 만듭니다. |
| Container Registry 액세스 | Kubernetes가 docker 이미지를 클러스터로 가져오기 위해 컨테이너 레지스트리에 액세스 해야 합니다. |
| Kubernetes CLI | [KUBERNETES CLI][kubernetes-cli] 는 컨테이너 레지스트리에서 공유 자격 증명을 관리 하는 데 필요 합니다. Kubernetes는 Kubernetes 패키지 관리자 인 투구 이전에도 필요 합니다. |
| 투구 CLI | [투구 CLI][helm-install] 를 설치 하는 과정에서 [Tiller][tiller-install]를 설치 하는 투구도 초기화 해야 합니다. |
|음성 리소스 |이러한 컨테이너를 사용하려면 다음이 있어야 합니다.<br><br>연결된 청구 키 및 청구 엔드포인트 URI를 가져올 수 있는 _음성_ Azure 리소스. 두 값은 Azure portal의 **음성** 개요 및 키 페이지에서 사용할 수 있으며 컨테이너를 시작하기 위해 필요합니다.<br><br>**{API_KEY}** : 리소스 키<br><br>**{ENDPOINT_URI}** : 엔드포인트 URI 예: `https://westus.api.cognitive.microsoft.com/sts/v1.0`|

## <a name="the-recommended-host-computer-configuration"></a>권장 호스트 컴퓨터 구성

참조로 [음성 서비스 컨테이너 호스트 컴퓨터][speech-container-host-computer] 세부 정보를 참조 하세요. 이 *투구 차트* 는 사용자가 지정 하는 디코드 (동시 요청) 수를 기준으로 CPU 및 메모리 요구 사항을 자동으로 계산 합니다. 또한 오디오/텍스트 입력에 대 한 최적화가 `enabled`구성 되었는지 여부에 따라 조정 됩니다. 투구 차트는 기본적으로, 두 개의 동시 요청 및 최적화를 사용 하지 않도록 설정 합니다.

| 서비스 | CPU/컨테이너 | 메모리/컨테이너 |
|--|--|--|
| **음성 텍스트 변환** | 하나의 디코더에는 최소 1150 millicores가 필요 합니다. `optimizedForAudioFile` 사용 하도록 설정 된 경우 1950 millicores 필요 합니다. (기본값: 두 개의 디코더) | 필수: 2gb<br>제한 됨: 4gb |
| **텍스트 음성 변환** | 한 동시 요청에는 최소 500 millicores가 필요 합니다. `optimizeForTurboMode` 사용 하도록 설정 된 경우 1000 millicores 필요 합니다. (기본값: 두 개의 동시 요청) | 필수: 1gb<br> 제한 됨: 2gb |

## <a name="connect-to-the-kubernetes-cluster"></a>Kubernetes 클러스터에 연결

호스트 컴퓨터에 사용 가능한 Kubernetes 클러스터가 있어야 합니다. Kubernetes 클러스터를 호스트 컴퓨터에 배포 하는 방법에 대 한 개념을 이해 하려면 [Kubernetes 클러스터 배포](../../aks/tutorial-kubernetes-deploy-cluster.md) 에 대 한이 자습서를 참조 하세요.

### <a name="sharing-docker-credentials-with-the-kubernetes-cluster"></a>Kubernetes 클러스터를 사용 하 여 Docker 자격 증명 공유

Kubernetes 클러스터가 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 구성 된 이미지를 `docker pull` 수 있도록 하려면 docker 자격 증명을 클러스터로 전송 해야 합니다. 컨테이너 레지스트리 액세스 전제 조건에서 제공 된 자격 증명에 따라 *docker 레지스트리 암호* 를 만들려면 아래 [`kubectl create`][kubectl-create] 명령을 실행 합니다.

원하는 명령줄 인터페이스에서 다음 명령을 실행 합니다. `<username>`, `<password>`및 `<email-address>`를 컨테이너 레지스트리 자격 증명으로 바꾸어야 합니다.

```console
kubectl create secret docker-registry mcr \
    --docker-server=containerpreview.azurecr.io \
    --docker-username=<username> \
    --docker-password=<password> \
    --docker-email=<email-address>
```

> [!NOTE]
> `containerpreview.azurecr.io` container registry에 이미 액세스할 수 있는 경우 대신 일반 플래그를 사용 하 여 Kubernetes 암호를 만들 수 있습니다. Docker 구성 JSON에 대해 실행 되는 다음 명령을 고려 합니다.
> ```console
>  kubectl create secret generic mcr \
>      --from-file=.dockerconfigjson=~/.docker/config.json \
>      --type=kubernetes.io/dockerconfigjson
> ```

암호가 성공적으로 만들어지면 다음 출력이 콘솔에 인쇄 됩니다.

```console
secret "mcr" created
```

비밀이 만들어졌는지 확인 하려면 `secrets` 플래그를 사용 하 여 [`kubectl get`][kubectl-get] 를 실행 합니다.

```console
kubectl get secrets
```

`kubectl get secrets`를 실행 하면 구성 된 모든 암호가 인쇄 됩니다.

```console
NAME    TYPE                              DATA    AGE
mcr     kubernetes.io/dockerconfigjson    1       30s
```

## <a name="configure-helm-chart-values-for-deployment"></a>배포에 대 한 투구 차트 값 구성

Microsoft에서 제공 하는 모든 공개적으로 사용 가능한 투구 차트를 보려면 [Microsoft 투구 Hub][ms-helm-hub] 를 방문 하세요. Microsoft 투구 Hub에서 **Cognitive Services 음성 온-프레미스 차트**를 찾을 수 있습니다. **Cognitive Services 음성 온-프레미스** 는 설치 하는 차트 이지만 먼저 명시적 구성으로 `config-values.yaml` 파일을 만들어야 합니다. 먼저 Microsoft 리포지토리를 투구 인스턴스에 추가 해 보겠습니다.

```console
helm repo add microsoft https://microsoft.github.io/charts/repo
```

다음으로 투구 차트 값을 구성 합니다. 다음 YAML을 복사 하 여 `config-values.yaml`라는 파일에 붙여 넣습니다. **Cognitive Services 음성 온-프레미스 투구 차트**를 사용자 지정 하는 방법에 대 한 자세한 내용은 [투구 차트 사용자 지정](#customize-helm-charts)을 참조 하세요. `# {ENDPOINT_URI}` 및 `# {API_KEY}` 주석을 사용자 고유의 값으로 바꿉니다.

```yaml
# These settings are deployment specific and users can provide customizations

# speech-to-text configurations
speechToText:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForAudioFile: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-speech-to-text
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}

# text-to-speech configurations
textToSpeech:
  enabled: true
  numberOfConcurrentRequest: 3
  optimizeForTurboMode: true
  image:
    registry: containerpreview.azurecr.io
    repository: microsoft/cognitive-services-text-to-speech
    tag: latest
    pullSecrets:
      - mcr # Or an existing secret
    args:
      eula: accept
      billing: # {ENDPOINT_URI}
      apikey: # {API_KEY}
```

> [!IMPORTANT]
> `billing` 및 `apikey` 값이 제공 되지 않은 경우 서비스는 15 분 후에 만료 됩니다. 마찬가지로, 서비스를 사용할 수 없게 되 면 확인이 실패 합니다.

### <a name="the-kubernetes-package-helm-chart"></a>Kubernetes 패키지 (투구 차트)

*투구 차트* 에는 `containerpreview.azurecr.io` 컨테이너 레지스트리에서 끌어올 docker 이미지의 구성이 포함 되어 있습니다.

> [투구 차트][helm-charts] 는 관련 된 Kubernetes 리소스 집합을 설명 하는 파일의 컬렉션입니다. 단일 차트는 memcached pod 또는 HTTP 서버, 데이터베이스, 캐시 등의 전체 웹 앱 스택과 같이 복잡 한 항목을 배포 하는 데 사용할 수 있습니다.

제공 된 *투구 차트* 는 음성 서비스의 docker 이미지를 텍스트 음성 변환 및 `containerpreview.azurecr.io` 컨테이너 레지스트리의 음성-텍스트 서비스로 끌어옵니다.

## <a name="install-the-helm-chart-on-the-kubernetes-cluster"></a>Kubernetes 클러스터에 투구 차트 설치

*투구 차트* 를 설치 하려면 `<config-values.yaml>`를 적절 한 경로 및 파일 이름 인수로 바꿔 [`helm install`][helm-install-cmd] 명령을 실행 해야 합니다. 아래에서 참조 하는 `microsoft/cognitive-services-speech-onpremise` 투구 차트는 [Microsoft 투구 허브][ms-helm-hub-speech-chart]에서 사용할 수 있습니다.

```console
helm install onprem-speech microsoft/cognitive-services-speech-onpremise \
    --version 0.1.1 \
    --values <config-values.yaml> 
```

성공적인 설치 실행에서 볼 수 있는 출력의 예는 다음과 같습니다.

```console
NAME:   onprem-speech
LAST DEPLOYED: Tue Jul  2 12:51:42 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/Pod(related)
NAME                             READY  STATUS             RESTARTS  AGE
speech-to-text-7664f5f465-87w2d  0/1    Pending            0         0s
speech-to-text-7664f5f465-klbr8  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-4jtzh  0/1    ContainerCreating  0         0s
text-to-speech-56f8fb685b-frwxf  0/1    Pending            0         0s

==> v1/Service
NAME            TYPE          CLUSTER-IP    EXTERNAL-IP  PORT(S)       AGE
speech-to-text  LoadBalancer  10.0.252.106  <pending>    80:31811/TCP  1s
text-to-speech  LoadBalancer  10.0.125.187  <pending>    80:31247/TCP  0s

==> v1beta1/PodDisruptionBudget
NAME                                MIN AVAILABLE  MAX UNAVAILABLE  ALLOWED DISRUPTIONS  AGE
speech-to-text-poddisruptionbudget  N/A            20%              0                    1s
text-to-speech-poddisruptionbudget  N/A            20%              0                    1s

==> v1beta2/Deployment
NAME            READY  UP-TO-DATE  AVAILABLE  AGE
speech-to-text  0/2    2           0          0s
text-to-speech  0/2    2           0          0s

==> v2beta2/HorizontalPodAutoscaler
NAME                       REFERENCE                  TARGETS        MINPODS  MAXPODS  REPLICAS  AGE
speech-to-text-autoscaler  Deployment/speech-to-text  <unknown>/50%  2        10       0         0s
text-to-speech-autoscaler  Deployment/text-to-speech  <unknown>/50%  2        10       0         0s


NOTES:
cognitive-services-speech-onpremise has been installed!
Release is named onprem-speech
```

Kubernetes 배포를 완료 하는 데 몇 분 정도 걸릴 수 있습니다. Pod와 서비스가 올바르게 배포 되 고 사용 가능한 지 확인 하려면 다음 명령을 실행 합니다.

```console
kubectl get all
```

다음 출력과 유사한 내용이 표시 되어야 합니다.

```console
NAME                                  READY     STATUS    RESTARTS   AGE
pod/speech-to-text-7664f5f465-87w2d   1/1       Running   0          34m
pod/speech-to-text-7664f5f465-klbr8   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-4jtzh   1/1       Running   0          34m
pod/text-to-speech-56f8fb685b-frwxf   1/1       Running   0          34m

NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)        AGE
service/kubernetes       ClusterIP      10.0.0.1       <none>           443/TCP        3h
service/speech-to-text   LoadBalancer   10.0.252.106   52.162.123.151   80:31811/TCP   34m
service/text-to-speech   LoadBalancer   10.0.125.187   65.52.233.162    80:31247/TCP   34m

NAME                             DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/speech-to-text   2         2         2            2           34m
deployment.apps/text-to-speech   2         2         2            2           34m

NAME                                        DESIRED   CURRENT   READY     AGE
replicaset.apps/speech-to-text-7664f5f465   2         2         2         34m
replicaset.apps/text-to-speech-56f8fb685b   2         2         2         34m

NAME                                                            REFERENCE                   TARGETS   MINPODS   MAXPODS   REPLICAS   AGE
horizontalpodautoscaler.autoscaling/speech-to-text-autoscaler   Deployment/speech-to-text   1%/50%    2         10        2          34m
horizontalpodautoscaler.autoscaling/text-to-speech-autoscaler   Deployment/text-to-speech   0%/50%    2         10        2          34m
```

### <a name="verify-helm-deployment-with-helm-tests"></a>투구 테스트를 사용 하 여 투구 배포 확인

설치 된 투구 차트는 인증에 대 한 편의를 제공 하는 *투구 테스트*를 정의 합니다. 이러한 테스트는 서비스 준비 상태를 확인 합니다. **음성-텍스트** 및 **텍스트 음성 변환** 서비스를 모두 확인 하기 위해 [투구 테스트][helm-test] 명령을 실행 합니다.

```console
helm test onprem-speech
```

> [!IMPORTANT]
> POD 상태가 `Running` 되지 않거나 배포가 `AVAILABLE` 열 아래에 나열 되어 있지 않으면 이러한 테스트가 실패 합니다. 이를 완료 하는 데 10 분 정도 걸릴 수 있으므로 잠시 기다려 주십시오.

이러한 테스트는 다양 한 상태 결과를 출력 합니다.

```console
RUNNING: speech-to-text-readiness-test
PASSED: speech-to-text-readiness-test
RUNNING: text-to-speech-readiness-test
PASSED: text-to-speech-readiness-test
```

*투구 테스트*를 실행 하는 대신 `kubectl get all` 명령에서 *외부 IP* 주소 및 해당 포트를 수집할 수 있습니다. IP 및 포트를 사용 하 여 웹 브라우저를 열고 `http://<external-ip>:<port>:/swagger/index.html`로 이동 하 여 API swagger 페이지를 확인 합니다.

## <a name="customize-helm-charts"></a>투구 차트 사용자 지정

투구 차트는 계층 구조입니다. 계층적으로 차트를 상속 하는 경우에는 보다 구체적인 설정이 상속 된 규칙을 재정의 하는 특이성의 개념에도 맞춘 됩니다.

[!INCLUDE [Speech umbrella-helm-chart-config](includes/speech-umbrella-helm-chart-config.md)]

[!INCLUDE [Speech-to-Text Helm Chart Config](includes/speech-to-text-chart-config.md)]

[!INCLUDE [Text-to-Speech Helm Chart Config](includes/text-to-speech-chart-config.md)]

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
[helm-install]: https://helm.sh/docs/intro/install/
[helm-install-cmd]: https://helm.sh/docs/intro/using_helm/#helm-install-installing-a-package
[tiller-install]: https://helm.sh/docs/install/#installing-tiller
[helm-charts]: https://helm.sh/docs/topics/charts/
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-test]: https://v2.helm.sh/docs/helm/#helm-test
[ms-helm-hub]: https://hub.helm.sh/charts/microsoft
[ms-helm-hub-speech-chart]: https://hub.helm.sh/charts/microsoft/cognitive-services-speech-onpremise

<!-- LINKS - internal -->
[speech-container-host-computer]: speech-container-howto.md#the-host-computer
[installing-helm-apps-in-aks]: ../../aks/kubernetes-helm.md
[cog-svcs-containers]: ../cognitive-services-container-support.md
