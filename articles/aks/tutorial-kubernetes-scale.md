---
title: Azure의 Kubernetes 자습서 - 애플리케이션 크기 조정
description: 이 AKS(Azure Kubernetes Service) 자습서에서는 Kubernetes에서 노드 및 Pod 크기를 조정하고 수평 방향 Pod 자동 크기 조정을 구현하는 방법을 알아봅니다.
services: container-service
ms.topic: tutorial
ms.date: 01/14/2019
ms.custom: mvc
ms.openlocfilehash: ab9217229a64605273537fc65cf3a29dcecd20c3
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "85361594"
---
# <a name="tutorial-scale-applications-in-azure-kubernetes-service-aks"></a>자습서: AKS(Azure Kubernetes Service)에서 애플리케이션 크기 조정

자습서를 수행한 경우 AKS에서 작동하는 Kubernetes 클러스터가 있으며 샘플 Azure Voting 앱을 배포한 상태입니다. 7개 중 5단계인 이 자습서에서는 앱의 Pod를 스케일 아웃하고 Pod 자동 크기 조정을 시도합니다. Azure VM 노드 수를 조정하여 워크로드 호스팅을 위한 클러스터 용량을 변경하는 방법도 알아봅니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * Kubernetes 노드 크기 조정
> * 애플리케이션을 실행하는 Kubernetes Pod의 크기를 수동으로 조정
> * 앱 프런트 엔드를 실행하는 Pod 자동 크기 조정 구성

추가 자습서에서 Azure Vote 애플리케이션은 새 버전으로 업데이트됩니다.

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서 애플리케이션은 컨테이너 이미지로 패키징되었습니다. 이 이미지는 Azure Container Registry로 업로드되었고, AKS 클러스터를 만들었습니다. 그런 다음, 애플리케이션은 AKS 클러스터에 배포되었습니다. 이러한 단계를 아직 수행하지 않았으나 수행하려는 경우 [자습서 1 - 컨테이너 이미지 만들기][aks-tutorial-prepare-app]로 시작합니다.

이 자습서의 작업을 수행하려면 Azure CLI 버전 2.0.53 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치][azure-cli-install]를 참조하세요.

## <a name="manually-scale-pods"></a>수동으로 Pod 크기 조정

이전 자습서에서 Azure Vote 프런트 엔드 및 Redis 인스턴스를 배포할 때 단일 복제본이 생성되었습니다. 클러스터에 있는 Pod의 수와 상태를 확인하려면 다음과 같이 [kubectl get][kubectl-get] 명령을 사용합니다.

```console
kubectl get pods
```

다음 예제 출력은 프런트 엔드 Pod 1개와 백 엔드 Pod 1개를 보여줍니다.

```
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

*azure-vote-front* 배포의 Pod 수를 수동으로 변경하려면 [kubectl scale][kubectl-scale] 명령을 사용합니다. 다음 예제는 프런트 엔드 Pod 수를 *5*로 늘립니다.

```console
kubectl scale --replicas=5 deployment/azure-vote-front
```

[kubectl get pods][kubectl-get] 명령을 다시 실행하여 AKS가 추가 Pod를 만드는지 확인합니다. 약 1분이 지나면 클러스터에서 추가 Pod를 사용할 수 있습니다.

```console
kubectl get pods

                                    READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Pod 자동 크기 조정

Kubernetes는 [수평 Pod 자동 크기 조정][kubernetes-hpa]을 지원하여 CPU 사용률 또는 다른 선택 메트릭에 따라 배포에서 Pod 수를 조정할 수 있게 해줍니다. [메트릭 서버][metrics-server]는 Kubernetes에 리소스 사용률을 제공하는 데 사용되며, AKS 클러스터 버전 1.10 이상에서 자동으로 배포됩니다. AKS 클러스터 버전을 확인하려면 다음 예제처럼 [az aks show][az-aks-show] 명령을 사용합니다.

```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query kubernetesVersion --output table
```

> [!NOTE]
> AKS 클러스터가 *1.10*보다 작으면 메트릭 서버가 자동으로 설치되지 않습니다. 메트릭 서버 설치 매니페스트는 메트릭 서버 릴리스에서 `components.yaml` 자산으로 사용할 수 있으므로 URL을 통해 설치할 수 있습니다. 이러한 YAML 정의에 대해 자세히 알아보려면 추가 정보의 [배포][metrics-server-github] 섹션을 참조하세요.
> 
> 설치 예제:
> ```console
> kubectl apply -f https://github.com/kubernetes-sigs/metrics-server/releases/download/v0.3.6/components.yaml
> ```

자동 크기 조정기를 사용하려면 Pod와 Pod의 모든 컨테이너에 CPU 요청 및 제한이 정의되어 있어야 합니다. `azure-vote-front` 배포에서 프런트 엔드 컨테이너는 0.25 CPU를 요청하며 제한은 0.5 CPU입니다. 다음 예제 코드 조각에 나와 있는 것처럼 이러한 리소스 요청 및 제한이 정의됩니다.

```yaml
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

다음 예제에서는 [kubectl autoscale][kubectl-autoscale] 명령을 사용하여 *azure-vote-front* 배포의 Pod 수를 자동으로 조정합니다. 모든 Pod의 평균 CPU 사용률이 요청된 사용량의 50%를 초과하는 경우 자동 크기 조정은 Pod를 최대 *10*개의 인스턴스로 늘립니다. 그런 다음, 최소 *3*개의 인스턴스가 배포에 대해 정의됩니다.

```console
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

또는 매니페스트 파일을 만들어 자동 크기 조정기 동작 및 리소스 제한을 정의할 수 있습니다. 다음은 `azure-vote-hpa.yaml`이라는 매니페스트 파일의 예입니다.

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-back-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-back
  targetCPUUtilizationPercentage: 50 # target CPU utilization

---

apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: azure-vote-front-hpa
spec:
  maxReplicas: 10 # define max replica count
  minReplicas: 3  # define min replica count
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: azure-vote-front
  targetCPUUtilizationPercentage: 50 # target CPU utilization
```

`kubectl apply`를 사용하여 `azure-vote-hpa.yaml` 매니페스트 파일에 정의된 자동 크기 조정기를 적용합니다.

```
kubectl apply -f azure-vote-hpa.yaml
```

자동 조정기의 상태를 확인하려면 다음과 같이 `kubectl get hpa` 명령을 실행합니다.

```
kubectl get hpa

NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Azure Vote 앱에 최소 부하를 적용한 상태로 몇 분이 지나면 Pod 복제본 수가 자동으로 3개로 줄어듭니다. `kubectl get pods` 명령을 다시 사용하여 불필요한 Pod를 제거할 수 있습니다.

## <a name="manually-scale-aks-nodes"></a>수동으로 AKS 노드 크기 조정

이전 자습서의 명령을 사용하여 Kubernetes 클러스터를 만든 경우 두 개의 노드가 있습니다. 클러스터에 대한 컨테이너 워크로드를 더 늘리거나 줄일 계획인 경우 수동으로 노드 수를 조정할 수 있습니다.

다음 예제에서는 *myAKSCluster*라는 Kubernetes 클러스터의 노드 수를 3개로 늘립니다. 이 명령은 완료되는 데 2~3분이 걸립니다.

```azurecli
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 3
```

클러스터가 성공적으로 크기 조정되면 출력은 다음 예제와 비슷합니다.

```
"agentPoolProfiles": [
  {
    "count": 3,
    "dnsPrefix": null,
    "fqdn": null,
    "name": "myAKSCluster",
    "osDiskSizeGb": null,
    "osType": "Linux",
    "ports": null,
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_D2_v2",
    "vnetSubnetId": null
  }
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Kubernetes 클러스터의 다양한 크기 조정 기능을 사용했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 애플리케이션을 실행하는 Kubernetes Pod의 크기를 수동으로 조정
> * 앱 프런트 엔드를 실행하는 Pod 자동 크기 조정 구성
> * 수동으로 Kubernetes 노드 크기 조정

그 다음 자습서로 이동하여 Kubernetes에서 애플리케이션을 업데이트하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [Kubernetes에서 애플리케이션 업데이트][aks-tutorial-update-app]

<!-- LINKS - external -->
[kubectl-autoscale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#autoscale
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-scale]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#scale
[kubernetes-hpa]: https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/
[metrics-server-github]: https://github.com/kubernetes-sigs/metrics-server/blob/master/README.md#deployment
[metrics-server]: https://kubernetes.io/docs/tasks/debug-application-cluster/resource-metrics-pipeline/#metrics-server

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-update-app]: ./tutorial-kubernetes-app-update.md
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
