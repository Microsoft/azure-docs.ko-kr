---
title: '빠른 시작: PowerShell을 사용하여 AKS 클러스터 배포'
description: PowerShell을 사용하여 Kubernetes 클러스터를 빠르게 만들고 애플리케이션을 배포하고 AKS(Azure Kubernetes Service)에서 성능을 모니터링하는 방법을 알아봅니다.
services: container-service
ms.topic: quickstart
ms.date: 08/18/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4dde1dcd131a497b60a314513df44cc0443d28ed
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589999"
---
# <a name="quickstart-deploy-an-azure-kubernetes-service-cluster-using-powershell"></a>빠른 시작: PowerShell을 사용하여 Azure Kubernetes Service 클러스터 배포

이 빠른 시작에서는 PowerShell을 사용하여 AKS(Azure Kubernetes Service) 클러스터를 배포합니다. AKS는 클러스터를 빠르게 배포하고 관리할 수 있는 관리형 Kubernetes 서비스입니다. 웹 프런트 엔드 및 Redis 인스턴스를 포함하는 다중 컨테이너 애플리케이션이 클러스터에서 실행됩니다. 그런 다음, 애플리케이션을 실행하는 Pod 및 클러스터의 상태를 모니터링하는 방법이 표시됩니다.

Windows Server 노드 풀을 만드는 방법에 대한 자세한 내용은 [Windows Server 컨테이너를 지원하는 AKS 클러스터 만들기][windows-container-powershell]를 참조하세요.

![Azure Kubernetes Service에 배포된 투표 앱](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

이 빠른 시작에서는 Kubernetes 기본 개념을 이해하고 있다고 가정합니다. 자세한 내용은 [AKS(Azure Kubernetes Service)의 Kubernetes 핵심 개념][kubernetes-concepts]을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

PowerShell을 로컬로 사용하도록 선택하는 경우 이 문서에서는 Az PowerShell 모듈을 설치하고 [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) cmdlet을 사용하여 Azure 계정에 연결해야 합니다. Az PowerShell 모듈을 설치하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치][install-azure-powershell]를 참조하세요.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

여러 Azure 구독이 있는 경우 리소스에 대한 요금이 청구되는 적절한 구독을 선택합니다. [Set-AzContext](/powershell/module/az.accounts/set-azcontext) cmdlet을 사용하여 특정 구독 ID를 선택합니다.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

[Azure 리소스 그룹](../azure-resource-manager/management/overview.md)은 Azure 리소스가 배포되고 관리되는 논리 그룹입니다. 리소스 그룹을 만들 때 위치를 지정하라는 메시지가 나타납니다. 이 위치는 리소스 그룹 메타데이터가 저장되는 위치이며 리소스를 만드는 동안 다른 지역을 지정하지 않으면 리소스가 Azure에서 실행되는 위치입니다. [New-AzResourceGroup][new-azresourcegroup] cmdlet을 사용하여 리소스 그룹을 만듭니다.

다음 예제에서는 **미국 동부** 지역에 **myResourceGroup**이라는 리소스 그룹을 만듭니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location eastus
```

다음 예제 출력에서는 성공적으로 만들어진 리소스 그룹이 나와 있습니다.

```plaintext
ResourceGroupName : myResourceGroup
Location          : eastus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup
```

## <a name="create-aks-cluster"></a>AKS 클러스터 만들기

`ssh-keygen` 명령줄 유틸리티를 사용하여 SSH 키 쌍을 생성합니다. 자세한 내용은 [빠른 단계: Azure에서 Linux VM용 SSH 퍼블릭-프라이빗 키 쌍 만들기 및 사용](../virtual-machines/linux/mac-create-ssh-keys.md)을 참조하세요.

[New-AzAks][new-azaks] cmdlet을 사용하여 AKS 클러스터를 만듭니다. 다음 예제에서는 하나의 노드가 있는 **myAKSCluster**라는 클러스터를 만듭니다. 컨테이너용 Azure Monitor는 기본적으로 사용하도록 설정되어 있습니다. 이 작업을 완료하는 데 몇 분 정도 걸립니다.

> [!NOTE]
> AKS 클러스터를 만들 때 AKS 리소스를 저장하기 위해 두 번째 리소스 그룹이 자동으로 만들어집니다. 자세한 내용은 [AKS를 통해 두 개의 리소스 그룹이 생성되는 이유는 무엇인가요?](./faq.md#why-are-two-resource-groups-created-with-aks)를 참조하세요.

```azurepowershell-interactive
New-AzAks -ResourceGroupName myResourceGroup -Name myAKSCluster -NodeCount 1
```

몇 분 후 명령이 완료되면 클러스터에 대한 정보가 반환됩니다.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

Kubernetes 클러스터를 관리하려면 [kubectl][kubectl] Kubernetes 명령줄 클라이언트를 사용합니다. Azure Cloud Shell을 사용하는 경우 `kubectl`이 이미 설치되어 있습니다. `kubectl`을 로컬로 설치하려면 `Install-AzAksKubectl` cmdlet을 사용합니다.

```azurepowershell
Install-AzAksKubectl
```

Kubernetes 클러스터에 연결하도록 `kubectl`을 구성하려면 [Import-AzAksCredential][import-azakscredential] cmdlet을 사용합니다. 다음 예제에서는 자격 증명을 다운로드하고 해당 자격 증명을 사용하도록 Kubernetes CLI를 구성합니다.

```azurepowershell-interactive
Import-AzAksCredential -ResourceGroupName myResourceGroup -Name myAKSCluster
```

클러스터에 대한 연결을 확인하려면 [kubectl get][kubectl-get] 명령을 사용하여 클러스터 노드 목록을 반환합니다.

```azurepowershell-interactive
.\kubectl get nodes
```

다음 예제 출력은 이전 단계에서 만든 단일 노드를 보여줍니다. 노드 상태가 **준비**인지 확인합니다.

```plaintext
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.15.10
```

## <a name="run-the-application"></a>애플리케이션 실행

Kubernetes 매니페스트 파일은 어떤 컨테이너 이미지가 실행되는지 등과 같은 클러스터에 대해 원하는 상태를 정의합니다. 이 빠른 시작에서는 Azure Vote 애플리케이션을 실행하는 데 필요한 모든 개체를 만드는 데 매니페스트를 사용합니다. 이 매니페스트는 샘플 Azure Vote Python 애플리케이션과 Redis 인스턴스 각각에 대한 두 개의 [Kubernetes 배포][kubernetes-deployment]를 포함합니다. 두 개의 Kubernetes Services, Redis 인스턴스에 대한 내부 서비스, 인터넷에서 Azure Vote 애플리케이션에 액세스하기 위한 외부 서비스가 만들어집니다.

`azure-vote.yaml`이라는 파일을 만들고 다음 YAML 정의에 복사합니다. Azure Cloud Shell을 사용하는 경우 이 파일은 가상 또는 실제 시스템에서 작업하고 있는 것처럼 `vi` 또는 `nano`를 사용하여 만들 수 있습니다.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

[kubectl apply][kubectl-apply] 명령을 사용하여 애플리케이션을 배포하고 YAML 매니페스트의 이름을 지정합니다.

```azurepowershell-interactive
.\kubectl apply -f azure-vote.yaml
```

다음 예제 출력에는 성공적으로 만들어진 배포 및 서비스가 나와 있습니다.

```plaintext
deployment.apps/azure-vote-back created
service/azure-vote-back created
deployment.apps/azure-vote-front created
service/azure-vote-front created
```

## <a name="test-the-application"></a>애플리케이션 테스트

애플리케이션이 실행되면 애플리케이션 프런트 엔드를 인터넷에 공개하는 Kubernetes 서비스가 만들어집니다.
이 프로세스를 완료하는 데 몇 분이 걸릴 수 있습니다.

진행 상태를 모니터링하려면 `--watch` 인수와 함께 [kubectl get service][kubectl-get] 명령을 사용합니다.

```azurepowershell-interactive
.\kubectl get service azure-vote-front --watch
```

처음에는 **azure-vote-front** 서비스에 대한 **EXTERNAL-IP**가 **보류 중**으로 표시됩니다.

```plaintext
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

**EXTERNAL-IP** 주소가 **보류 중**에서 실제 공용 IP 주소로 변경되면 `CTRL-C`를 사용하여 `kubectl` 조사식 프로세스를 중지합니다. 다음 예제 출력은 서비스에 할당된 유효한 공용 IP 주소를 보여줍니다.

```plaintext
azure-vote-front   LoadBalancer   10.0.37.27   52.179.23.131   80:30572/TCP   2m
```

Azure Vote 앱이 실제로 작동하는 모습을 보려면 웹 브라우저를 서비스의 외부 IP 주소로 엽니다.

![Azure Kubernetes Service에 배포된 투표 앱](./media/kubernetes-walkthrough-powershell/voting-app-deployed-in-azure-kubernetes-service.png)

AKS 클러스터가 생성될 때 클러스터 노드와 Pod의 상태 메트릭을 캡처하기 위해 [컨테이너에 대한 Azure Monitor](../azure-monitor/insights/container-insights-overview.md)가 설정되었습니다. 이 상태 메트릭은 Azure Portal에서 사용할 수 있습니다.

## <a name="delete-the-cluster"></a>클러스터 삭제

Azure 요금을 방지하려면 불필요한 리소스를 정리해야 합니다. 클러스터가 더 이상 필요하지 않은 경우 [Remove-AzResourceGroup][remove-azresourcegroup] cmdlet을 사용하여 리소스 그룹, 컨테이너 서비스 및 모든 관련 리소스를 제거합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

> [!NOTE]
> 클러스터를 삭제할 때, AKS 클러스터에 사용되는 Azure Active Directory 서비스 주체는 제거되지 않습니다. 서비스 주체를 제거하는 방법에 대한 단계는 [AKS 서비스 주체 고려 사항 및 삭제][sp-delete]를 참조하세요. 관리 ID를 사용하는 경우 ID는 플랫폼에 의해 관리되며 제거할 필요가 없습니다.

## <a name="get-the-code"></a>코드 가져오기

이 빠른 시작에서는 Kubernetes 배포를 만드는 데 미리 생성된 컨테이너 이미지를 사용했습니다. 관련된 애플리케이션 코드, Dockerfile 및 Kubernetes 매니페스트 파일을 GitHub에서 사용할 수 있습니다.

[https://github.com/Azure-Samples/azure-voting-app-redis][azure-vote-app]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Kubernetes 클러스터를 배포하고, 이 클러스터에 다중 컨테이너 애플리케이션을 배포했습니다. 또한 AKS 클러스터에 대한 [Kubernetes 웹 대시보드에 액세스][kubernetes-dashboard]할 수도 있습니다.

AKS에 대해 자세히 알아보고 배포 예제에 대한 전체 코드를 연습해 보려면 Kubernetes 클러스터 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [AKS 자습서][aks-tutorial]

<!-- LINKS - external -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[azure-dev-spaces]: ../dev-spaces/index.yml
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[azure-vote-app]: https://github.com/Azure-Samples/azure-voting-app-redis.git

<!-- LINKS - internal -->
[windows-container-powershell]: windows-container-powershell.md
[kubernetes-concepts]: concepts-clusters-workloads.md
[install-azure-powershell]: /powershell/azure/install-az-ps
[new-azresourcegroup]: /powershell/module/az.resources/new-azresourcegroup
[new-azaks]: /powershell/module/az.aks/new-azaks
[import-azakscredential]: /powershell/module/az.aks/import-azakscredential
[kubernetes-deployment]: concepts-clusters-workloads.md#deployments-and-yaml-manifests
[kubernetes-service]: concepts-network.md#services
[remove-azresourcegroup]: /powershell/module/az.resources/remove-azresourcegroup
[sp-delete]: kubernetes-service-principal.md#additional-considerations
[kubernetes-dashboard]: kubernetes-dashboard.md
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
