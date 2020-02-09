---
title: AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 보호
description: AKS (Azure Kubernetes Service)에서 Kubernetes 네트워크 정책을 사용 하 여 pod에서 들어오고 나가는 트래픽을 보호 하는 방법에 대해 알아봅니다.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 350e553563aa152c61c922727fb87937bedd14b5
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/25/2019
ms.locfileid: "72928490"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 간 트래픽 보호

Kubernetes에서 최신 마이크로 서비스 기반 애플리케이션을 실행할 경우 어느 구성 요소들이 서로 통신할 수 있는지 제어하고 싶을 때가 있습니다. AKS (Azure Kubernetes Service) 클러스터의 pod 간에 트래픽이 흐를 수 있는 방법에는 최소 권한의 원칙을 적용 해야 합니다. 백 엔드 응용 프로그램으로 직접 트래픽을 차단 하려는 경우를 가정해 보겠습니다. Kubernetes의 *네트워크 정책* 기능을 사용 하면 클러스터에서 pod 간의 수신 및 송신 트래픽에 대한 규칙을 정의할 수 있습니다.

이 문서에서는 네트워크 정책 엔진을 설치 하 고 Kubernetes 네트워크 정책을 만들어 AKS에서 pod 간의 트래픽 흐름을 제어 하는 방법을 보여 줍니다. 네트워크 정책은 Linux 기반 노드 및 AKS의 pod에만 사용 해야 합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.61 이상이 설치 및 구성 되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드 해야 하는 경우 [Azure CLI 설치][install-azure-cli]를 참조 하세요.

> [!TIP]
> 미리 보기 중에 네트워크 정책 기능을 사용한 경우 [새 클러스터를 만드는](#create-an-aks-cluster-and-enable-network-policy)것이 좋습니다.
> 
> 미리 보기 중에 네트워크 정책을 사용한 기존 테스트 클러스터를 계속 사용 하려면 클러스터를 최신 GA 릴리스에 대한 새 Kubernetes 버전으로 업그레이드 한 후 다음 YAML 매니페스트를 배포 하 여 충돌 하는 메트릭 서버 및 Kubernetes를 수정 합니다. 대시보드와. 이 픽스는 Calico 네트워크 정책 엔진을 사용 하는 클러스터에만 필요 합니다.
>
> 보안을 유지 하는 가장 좋은 방법으로 [이 YAML 매니페스트의 콘텐츠를 검토][calico-aks-cleanup] 하 여 AKS 클러스터에 배포 된 항목을 이해 합니다.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>네트워크 정책 개요

AKS 클러스터의 모든 pod는 기본적으로 제한 없이 트래픽을 보내고 받을 수 있습니다. 보안을 강화하기 위해 트래픽의 흐름을 제어하는 규칙을 정의할 수 있습니다. 백 엔드 응용 프로그램은 필요한 프런트 엔드 서비스 (예:)에만 노출 되는 경우가 많습니다. 또는 데이터베이스 구성 요소에 연결 된 응용 프로그램 계층 에서만 데이터베이스 구성 요소에 액세스할 수 있습니다.

네트워크 정책은 Pod 간의 통신에 대한 액세스 정책을 정의 하는 Kubernetes 사양입니다. 네트워크 정책을 사용 하 여 트래픽을 전송 및 수신 하 고 하나 이상의 레이블 선택기와 일치 하는 pod 컬렉션에 적용 하는 규칙의 정렬 된 집합을 정의 합니다.

이러한 네트워크 정책 규칙은 YAML 매니페스트로 정의 됩니다. 네트워크 정책은 배포 나 서비스를 만드는 더 광범위 한 매니페스트의 일부로 포함 될 수 있습니다.

### <a name="network-policy-options-in-aks"></a>AKS의 네트워크 정책 옵션

Azure는 네트워크 정책을 구현 하는 두 가지 방법을 제공 합니다. AKS 클러스터를 만들 때 네트워크 정책 옵션을 선택 합니다. 클러스터를 만든 후에는 정책 옵션을 변경할 수 없습니다.

* Azure *네트워크 정책*이라고 하는 azure의 고유한 구현입니다.
* [Tigera][tigera]으로 설립 된 오픈 소스 네트워크 및 네트워크 보안 솔루션인 *Calico 네트워크 정책*.

두 구현 모두 Linux *Iptables* 를 사용 하 여 지정 된 정책을 적용 합니다. 정책은 허용 및 허용 되지 않는 IP 쌍 집합으로 변환 됩니다. 이러한 쌍은 IPTable 필터 규칙으로 프로그래밍 됩니다.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure와 Calico 정책과 해당 기능 간의 차이점

| 기능                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| 지원되는 플랫폼                      | Linux                      | Linux                       |
| 지원 되는 네트워킹 옵션             | Azure CNI                  | Azure CNI 및 kubenet       |
| Kubernetes 사양 준수 | 지원 되는 모든 정책 유형 |  지원 되는 모든 정책 유형 |
| 추가 기능                      | 없음                       | 글로벌 네트워크 정책, 글로벌 네트워크 집합 및 호스트 엔드포인트으로 구성 된 확장 정책 모델입니다. `calicoctl` CLI를 사용 하 여 이러한 확장 기능을 관리 하는 방법에 대한 자세한 내용은 [calicoctl user reference][calicoctl]를 참조 하세요. |
| 지원                                  | Azure 지원 및 엔지니어링 팀에서 지원 | Calico 커뮤니티 지원. 추가 유료 지원에 대한 자세한 내용은 [프로젝트 Calico 지원 옵션][calico-support]을 참조 하세요. |
| 로깅                                  | IPTables에서 추가/삭제 된 규칙은 */var/log/azure-npm.log* 아래의 모든 호스트에 기록 됩니다. | 자세한 내용은 [Calico 구성 요소 로그][calico-logs] 를 참조 하세요. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS 클러스터 만들기 및 네트워크 정책 사용

작동 중인 네트워크 정책을 확인 하려면 트래픽 흐름을 정의 하는 정책을 만들어 확장 합니다.

* pod에 대한 모든 트래픽을 거부합니다.
* pod 레이블을 기준으로 트래픽을 허용합니다.
* 네임스페이스를 기준으로 트래픽을 허용합니다.

먼저, 네트워크 정책을 지 원하는 AKS 클러스터를 만들어 보겠습니다. 

> [!IMPORTANT]
>
> 네트워크 정책 기능은 클러스터를 만들 때만 사용할 수 있습니다. 기존 AKS 클러스터에서는 네트워크 정책을 사용하도록 설정할 수 없습니다.

Azure 네트워크 정책을 사용 하려면 [azure CNI 플러그 인][azure-cni] 을 사용 하 고 고유한 가상 네트워크 및 서브넷을 정의 해야 합니다. 필요한 서브넷 범위를 계획 하는 방법에 대한 자세한 내용은 [고급 네트워킹 구성][use-advanced-networking]을 참조 하세요. Calico Network 정책은 동일한 Azure CNI 플러그 인 또는 Kubenet CNI 플러그 인과 함께 사용할 수 있습니다.

다음 예제 스크립트는 다음과 같은 작업을 수행합니다.

* 가상 네트워크 및 서브넷을 만듭니다.
* AKS 클러스터에 사용할 Azure AD (Azure Active Directory) 서비스 주체를 만듭니다.
* 가상 네트워크에서 AKS 클러스터 서비스 주체에 대해 *참가자* 권한을 할당합니다.
* 정의 된 가상 네트워크에 AKS 클러스터를 만들고 네트워크 정책을 사용 하도록 설정 합니다.
    * *Azure* 네트워크 정책 옵션이 사용 됩니다. 대신 Calico를 네트워크 정책 옵션으로 사용 하려면 `--network-policy calico` 매개 변수를 사용 합니다. 참고: Calico는 `--network-plugin azure` 또는 `--network-plugin kubenet`와 함께 사용할 수 있습니다.

사용자 고유의 보안 *SP_PASSWORD*를 제공합니다. *RESOURCE_GROUP_NAME* 및 *CLUSTER_NAME* 변수를 바꿀 수 있습니다.

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

클러스터를 만드는 데 몇 분이 걸립니다. 클러스터가 준비 되 면 [az aks][az-aks-get-credentials] 명령을 사용 하 여 Kubernetes 클러스터에 연결 하도록 `kubectl`를 구성 합니다. 이 명령은 자격 증명을 다운로드하고 해당 자격 증명을 사용하도록 Kubernetes CLI를 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>pod에 대한 모든 인바운드 트래픽 거부

특정 네트워크 트래픽을 허용하는 규칙을 정의하기 전에 먼저 모든 트래픽을 거부하는 네트워크 정책을 만듭니다. 이 정책은 원하는 트래픽만 허용 목록 시작 하는 시작점을 제공 합니다. 이 네트워크 정책을 적용하면 트래픽이 삭제되는 것을 명확하게 확인할 수 있습니다.

예제 응용 프로그램 환경 및 트래픽 규칙의 경우 먼저 *development* 라는 네임 스페이스를 만들어 예제 pod를 실행 해 보겠습니다.

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

NGINX를 실행 하는 예제 백 엔드 pod를 만듭니다. 이 백 엔드 pod는 샘플 백 엔드 웹 기반 응용 프로그램을 시뮬레이트하는 데 사용할 수 있습니다. *development* 네임스페이스에서 이 pod를 만들고 포트 *80*을 열어 웹 트래픽을 처리합니다. 다음 섹션에서 네트워크 정책의 대상으로 지정할 수 있도록 pod에 *app=webapp,role=backend* 레이블을 지정합니다.

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

다른 pod를 만들고 터미널 세션을 연결 하 여 기본 NGINX 웹 페이지에 성공적으로 연결할 수 있는지 테스트 합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget`를 사용 하 여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 합니다.

```console
wget -qO- http://backend
```

다음 샘플 출력에서는 기본 NGINX 웹 페이지가 반환 되었음을 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제 됩니다.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>네트워크 정책 만들기 및 적용

이제 샘플 백 엔드 pod에서 basic NGINX 웹 페이지를 사용 하 여 모든 트래픽을 거부 하는 네트워크 정책을 만들 수 있음을 확인 했습니다. `backend-policy.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 이 매니페스트는 *podSelector* 를 사용 하 여 샘플 NGINX pod와 같이 앱이 있는 pod ( *webapp, role: 백엔드* 레이블)에 정책을 연결 합니다. *ingress* 아래에 규칙이 정의되어 있지 않으므로 pod로의 모든 인바운드 트래픽이 거부됩니다.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 네트워크 정책을 적용 하 고 yaml 매니페스트의 이름을 지정 합니다.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>네트워크 정책 테스트


백 엔드 pod에서 NGINX 웹 페이지를 다시 사용할 수 있는지 확인 합니다. 다음과 같이 다른 테스트 Pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget`를 사용 하 여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 합니다. 이번에는 제한 시간 값을 *2*초로 설정합니다. 이제 네트워크 정책이 모든 인바운드 트래픽을 차단 하므로 다음 예제에 표시 된 것 처럼 페이지를 로드할 수 없습니다.

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제 됩니다.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>pod 레이블을 기준으로 인바운드 트래픽 허용

이전 섹션에서는 백 엔드 NGINX pod가 예약 되었고 네트워크 정책을 만들어 모든 트래픽을 거부 했습니다. 프런트 엔드 pod를 만들고 네트워크 정책을 업데이트 하 여 프런트 엔드 pod의 트래픽을 허용 해 보겠습니다.

*app:webapp,role:frontend* 레이블을 사용하여 네임스페이스에서 pod의 트래픽을 허용하도록 네트워크 정책을 업데이트합니다. 이전 *백 엔드-정책. yaml* 파일을 편집 하 고 다음 예제와 같이 매니페스트가 보이도록 *matchlabels* 수신 규칙을 추가 합니다.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> 이 네트워크 정책은 수신 규칙에 대해 *namespaceSelector* 및 *podSelector* 요소를 사용합니다. YAML 구문은 수신 규칙을 추가 하는 데 중요 합니다. 이 예제에서 수신 규칙이 적용되려면 두 요소가 모두 일치해야 합니다. *1.12* 이전 버전에서는 이러한 요소를 올바르게 해석 하지 못하고 네트워크 트래픽을 Kubernetes 수 있습니다. 이 동작에 대한 자세한 내용은 [선택기와의 동작][policy-rules]을 참조 하세요.

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 업데이트 된 네트워크 정책을 적용 하 고 yaml 매니페스트의 이름을 지정 합니다.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

*App = webapp, role = 프런트* 엔드로 레이블이 지정 된 pod를 예약 하 고 터미널 세션을 연결 합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget`를 사용 하 여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 합니다.

```console
wget -qO- http://backend
```

수신 규칙은 pod를 사용 하는 트래픽을 허용 합니다 *. 레이블 앱: webapp, role: 프런트*엔드, 프런트 엔드 pod의 트래픽이 허용 됩니다. 다음 예제 출력에서는 반환 된 기본 NGINX 웹 페이지를 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. Pod가 자동으로 삭제 됩니다.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>일치하는 레이블이 없는 pod 테스트

네트워크 정책은 *app: webapp,role: frontend* 레이블이 지정된 pod의 트래픽을 허용하지만 다른 모든 트래픽을 거부합니다. 해당 레이블이 없는 다른 pod가 백 엔드 NGINX pod에 액세스할 수 있는지 여부를 테스트 해 봅니다. 다음과 같이 다른 테스트 Pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget`를 사용 하 여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 합니다. 네트워크 정책은 인바운드 트래픽을 차단 하므로 다음 예제에 표시 된 것 처럼 페이지를 로드할 수 없습니다.

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제 됩니다.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>정의된 네임스페이스 내의 트래픽만 허용

이전 예제에서는 모든 트래픽을 거부 하는 네트워크 정책을 만든 다음 특정 레이블과 pod의 트래픽을 허용 하도록 정책을 업데이트 했습니다. 또 다른 일반적인 요구는 지정 된 네임 스페이스 내 에서만 트래픽을로 제한 하는 것입니다. 이전 예제가 *개발* 네임 스페이스의 트래픽용 인 경우 *프로덕션*과 같은 다른 네임 스페이스의 트래픽이 pod에 도달 하는 것을 방지 하는 네트워크 정책을 만듭니다.

먼저, 프로덕션 네임스페이스를 시뮬레이트하는 새 네임스페이스를 만듭니다.

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

*app=webapp,role=frontend* 레이블이 지정된 *production* 네임스페이스에서 테스트 pod를 예약합니다. 다음과 같이 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

셸 프롬프트에서 `wget`를 사용 하 여 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 합니다.

```console
wget -qO- http://backend.development
```

Pod의 레이블은 네트워크 정책에서 현재 허용 되는 것과 일치 하므로 트래픽이 허용 됩니다. 네트워크 정책은 pod 레이블만 확인하고, 네임스페이스는 확인하지 않습니다. 다음 예제 출력에서는 반환 된 기본 NGINX 웹 페이지를 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제 됩니다.

```console
exit
```

### <a name="update-the-network-policy"></a>네트워크 정책 업데이트

수신 규칙 *namespaceSelector* 섹션을 업데이트 하 여 *development* 네임 스페이스 내의 트래픽만 허용 하도록 하겠습니다. 다음 예제와 같이 *backend-policy.yaml* 매니페스트 파일을 편집합니다.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

더 복잡 한 예제에서는 *namespaceSelector* 및 *podSelector*와 같은 여러 수신 규칙을 정의할 수 있습니다.

[Kubectl apply][kubectl-apply] 명령을 사용 하 여 업데이트 된 네트워크 정책을 적용 하 고 yaml 매니페스트의 이름을 지정 합니다.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>업데이트된 네트워크 정책 테스트

*프로덕션* 네임 스페이스에서 다른 pod를 예약 하 고 터미널 세션을 연결 합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

셸 프롬프트에서 `wget`를 사용 하 여 네트워크 정책에서 이제 트래픽을 거부 하는지 확인 합니다.

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

테스트 pod를 종료합니다.

```console
exit
```

*프로덕션* 네임 스페이스에서 트래픽이 거부 되 면 *개발* 네임 스페이스에서 테스트 pod를 다시 예약 하 고 터미널 세션을 연결 합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

셸 프롬프트에서 `wget`를 사용 하 여 네트워크 정책에서 트래픽을 허용 하는지 확인 합니다.

```console
wget -qO- http://backend
```

Pod가 네트워크 정책에서 허용 되는 것과 일치 하는 네임 스페이스에서 예약 되었으므로 트래픽이 허용 됩니다. 다음 샘플 출력은 반환 된 기본 NGINX 웹 페이지를 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod가 자동으로 삭제 됩니다.

```console
exit
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 두 개의 네임 스페이스를 만들고 네트워크 정책을 적용 했습니다. 이러한 리소스를 정리 하려면 [kubectl delete][kubectl-delete] 명령을 사용 하 여 리소스 이름을 지정 합니다.

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>다음 단계

네트워크 리소스에 대한 자세한 내용은 [Azure Kubernetes Service의 응용 프로그램에 대한 네트워크 개념 (AKS) (영문)][concepts-network]을 참조 하세요.

정책에 대해 자세히 알아보려면 [Kubernetes network 정책][kubernetes-network-policies]을 참조 하세요.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
