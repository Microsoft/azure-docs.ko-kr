---
title: Windows 컨테이너와 상호 작용
services: azure-dev-spaces
ms.date: 01/16/2020
ms.topic: conceptual
description: Windows 컨테이너를 사용 하 여 기존 클러스터에서 Azure Dev Spaces를 실행 하는 방법을 알아봅니다.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, 컨테이너, Windows 컨테이너
ms.openlocfilehash: 131f69d42795b857a53fc21b760a7275a6826bb8
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212475"
---
# <a name="interact-with-windows-containers-using-azure-dev-spaces"></a>Azure Dev Spaces를 사용 하 여 Windows 컨테이너와 상호 작용

새 네임 스페이스와 기존 Kubernetes 네임 스페이스 모두에 Azure Dev Spaces을 사용 하도록 설정할 수 있습니다. Azure Dev Spaces은 Linux 컨테이너에서 실행 되는 서비스를 실행 하 고 계측 합니다. 또한 이러한 서비스는 동일한 네임 스페이스의 Windows 컨테이너에서 실행 되는 응용 프로그램과 상호 작용할 수 있습니다. 이 문서에서는 Dev 공간을 사용 하 여 기존 Windows 컨테이너를 사용 하는 네임 스페이스에서 서비스를 실행 하는 방법을 보여 줍니다. 이번에는 Azure Dev Spaces를 사용 하 여 Windows 컨테이너를 디버깅 하거나 연결할 수 없습니다.

## <a name="set-up-your-cluster"></a>클러스터 설정

이 문서에서는 Linux 및 Windows 노드 풀을 모두 포함 하는 클러스터가 이미 있다고 가정 합니다. Linux 및 Windows 노드 풀을 사용 하 여 클러스터를 만들어야 하는 경우 [여기][windows-container-cli]에 설명 된 지침을 따를 수 있습니다.

[Kubectl][kubectl], Kubernetes 명령줄 클라이언트를 사용 하 여 클러스터에 연결 합니다. Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [az aks get-credentials][az-aks-get-credentials] 명령을 사용합니다. 이 명령은 자격 증명을 다운로드하고 Kubernetes CLI가 해당 자격 증명을 사용하도록 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurecli-interactive
kubectl get nodes
```

다음 예제 출력에서는 Windows 및 Linux 노드가 모두 포함 된 클러스터를 보여 줍니다. 계속 하기 전에 각 노드에 대해 상태가 *준비* 인지 확인 합니다.

```console
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmss000000   Ready    agent   13m    v1.14.8
aks-nodepool1-12345678-vmss000001   Ready    agent   13m    v1.14.8
aksnpwin000000                      Ready    agent   108s   v1.14.8
```

Windows 노드에 [taint][using-taints] 을 적용 합니다. Windows 노드의 taint는 개발 공간이 Windows 노드에서 실행 되도록 Linux 컨테이너를 예약 하는 것을 방지 합니다. 다음 명령 예제 명령은 이전 예제의 *aksnpwin987654* Windows 노드에 taint을 적용 합니다.

```azurecli-interactive
kubectl taint node aksnpwin987654 sku=win-node:NoSchedule
```

> [!IMPORTANT]
> 노드에 taint를 적용 하는 경우 서비스의 배포 템플릿에서 일치 하는 toleration를 구성 하 여 해당 노드에서 서비스를 실행 해야 합니다. 샘플 응용 프로그램은 이전 명령에서 구성한 taint 일치 하는 [toleration][sample-application-toleration-example] 으로 이미 구성 되어 있습니다.

## <a name="run-your-windows-service"></a>Windows 서비스 실행

AKS 클러스터에서 Windows 서비스를 실행 하 고 *실행 중* 상태 인지 확인 합니다. 이 문서에서는 [샘플 응용 프로그램][sample-application] 을 사용 하 여 클러스터에서 실행 되는 Windows 및 Linux 서비스를 보여 줍니다.

GitHub에서 샘플 응용 프로그램을 복제 하 고 디렉터리로 이동 합니다 `dev-spaces/samples/existingWindowsBackend/mywebapi-windows` .

```console
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/existingWindowsBackend/mywebapi-windows
```

예제 응용 프로그램에서는 [투구 3][helm-installed] 을 사용 하 여 클러스터에서 Windows 서비스를 실행 합니다. 디렉터리로 이동 하 여 `charts` Windows 서비스를 실행 하는 투구를 사용 합니다.

```console
cd charts/
kubectl create ns dev
helm install windows-service . --namespace dev
```

위의 명령은 투구를 사용 하 여 *dev* 네임 스페이스에서 Windows 서비스를 실행 합니다. *Dev*라는 이름의 네임 스페이스가 없으면 생성 됩니다.

명령을 사용 `kubectl get pods` 하 여 클러스터에서 Windows 서비스가 실행 되 고 있는지 확인 합니다. 

```console
$ kubectl get pods --namespace dev --watch
NAME                     READY   STATUS              RESTARTS   AGE
myapi-4b9667d123-1a2b3   0/1     ContainerCreating   0          47s
...
myapi-4b9667d123-1a2b3   1/1     Running             0          98s
```

## <a name="enable-azure-dev-spaces"></a>Azure Dev Spaces 사용

Windows 서비스를 실행 하는 데 사용한 것과 동일한 네임 스페이스에서 개발 공간을 사용 하도록 설정 합니다. 다음 명령을 사용 하 여 *dev* 네임 스페이스의 개발 공간을 사용할 수 있습니다.

```console
az aks use-dev-spaces -g myResourceGroup -n myAKSCluster --space dev --yes
```

## <a name="update-your-windows-service-for-dev-spaces"></a>개발 공간에 대해 Windows 서비스 업데이트

이미 실행 중인 컨테이너를 사용 하 여 기존 네임 스페이스에서 개발 공간을 사용 하도록 설정 하는 경우 기본적으로 Dev 공간은 해당 네임 스페이스에서 실행 되는 모든 새 컨테이너를 시도 하 고 계측 합니다. 또한 Dev Spaces는 네임 스페이스에서 이미 실행 중인 서비스에 대해 생성 된 모든 새 컨테이너를 시도 하 고 계측 합니다. 개발 공간이 네임 스페이스에서 실행 되는 컨테이너를 계측 하지 않도록 하려면 *프록시 없음* 헤더를에 추가 합니다 `deployment.yaml` .

`azds.io/no-proxy: "true"`파일에를 추가 합니다 `existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml` .

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  ...
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    ...
  template:
    metadata:
      labels:
        app.kubernetes.io/name: {{ include "mywebapi.name" . }}
        app.kubernetes.io/instance: {{ .Release.Name }}
        azds.io/no-proxy: "true"
```

`helm list`을 사용 하 여 Windows 서비스에 대 한 배포를 나열 합니다.

```cmd
$ helm list --namespace dev
NAME              REVISION  UPDATED                     STATUS      CHART           APP VERSION NAMESPACE
windows-service 1           Wed Jul 24 15:45:59 2019    DEPLOYED    mywebapi-0.1.0  1.0         dev  
```

위의 예제에서 배포 이름은 *windows 서비스*입니다. 다음을 사용 하 여 Windows 서비스를 새 구성으로 업데이트 합니다 `helm upgrade` .

```cmd
helm upgrade windows-service . --namespace dev
```

을 (를) 업데이트 한 후에 `deployment.yaml` 는 Dev 공간에서 서비스를 시도 하지 않고 계측 합니다.

## <a name="run-your-linux-application-with-azure-dev-spaces"></a>Azure Dev Spaces를 사용 하 여 Linux 응용 프로그램 실행

디렉터리로 이동 하 여 `webfrontend` `azds prep` 및 명령을 사용 하 여 `azds up` 클러스터에서 Linux 응용 프로그램을 실행 합니다.

```console
cd ../../webfrontend-linux/
azds prep --enable-ingress
azds up
```

이 `azds prep --enable-ingress` 명령은 응용 프로그램에 대 한 투구 차트 및 Dockerfiles를 생성 합니다.

> [!TIP]
> 프로젝트의 [Dockerfile 및 Helm 차트](../how-dev-spaces-works-prep.md#prepare-your-code)는 Azure Dev Spaces에서 코드를 빌드하고 실행하는 데 사용되지만 프로젝트를 빌드하고 실행하는 방법을 변경하려면 이러한 파일을 수정할 수 있습니다.

이 `azds up` 명령은 네임 스페이스에서 서비스를 실행 합니다.

```console
$ azds up
Using dev space 'dev' with target 'myAKSCluster'
Synchronizing files...4s
Installing Helm chart...11s
Waiting for container image build...6s
Building container image...
Step 1/12 : FROM mcr.microsoft.com/dotnet/core/sdk:2.2
...
Step 12/12 : ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
Built container image in 36s
Waiting for container...2s
Service 'webfrontend' port 'http' is available at http://dev.webfrontend.abcdef0123.eus.azds.io/
Service 'webfrontend' port 80 (http) is available via port forwarding at http://localhost:57648
```

Azds up 명령의 출력에 표시 되는 공용 URL을 열어 실행 중인 서비스를 볼 수 있습니다. 이 예제에서 공용 URL은 `http://dev.webfrontend.abcdef0123.eus.azds.io/`입니다. 브라우저에서 서비스로 이동 하 여 맨 위에 있는 *About* 을 클릭 합니다. 컨테이너에서 사용 중인 Windows 버전을 포함 하는 *mywebapi* 서비스의 메시지가 표시 되는지 확인 합니다.

![Mywebapi의 Windows 버전을 보여 주는 샘플 앱](../media/run-dev-spaces-windows-containers/sample-app.png)

## <a name="next-steps"></a>다음 단계

Azure Dev Spaces 작동 방법에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Dev Spaces의 작동 원리](../how-dev-spaces-works.md)

[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[helm-installed]: https://helm.sh/docs/intro/install/
[sample-application]: https://github.com/Azure/dev-spaces/tree/master/samples/existingWindowsBackend
[sample-application-toleration-example]: https://github.com/Azure/dev-spaces/blob/master/samples/existingWindowsBackend/mywebapi-windows/charts/templates/deployment.yaml#L24-L27
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[using-taints]: ../../aks/use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
[windows-container-cli]: ../../aks/windows-container-cli.md
