---
title: AKS(Azure Kubernetes Service)에서 네트워크 정책을 사용하여 pod 보호
description: Azure Kubernetes Service (AKS)에서 Kubernetes 네트워크 정책에 사용 하 여 pod 안팎으로 이동 하는 트래픽을 보호 하는 방법을 알아봅니다
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 04/08/2019
ms.author: iainfou
ms.openlocfilehash: 29180d6c1bb5f0991a4f33c3b7c9418f84d8260c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61027975"
---
# <a name="preview---secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>미리 보기-Azure Kubernetes Service (AKS)에서 네트워크 정책을 사용 하 여 pod 간의 트래픽 보안

Kubernetes에서 최신 마이크로 서비스 기반 애플리케이션을 실행할 경우 어느 구성 요소들이 서로 통신할 수 있는지 제어하고 싶을 때가 있습니다. Azure Kubernetes Service (AKS) 클러스터의 pod 간에 트래픽을 전달할 수 있습니다 하는 방법에 최소 권한의 원칙 적용 해야 합니다. 백 엔드 응용 프로그램에 직접 트래픽을 차단 하려고 하는 경우가 많습니다 경우를 가정해 봅니다. 합니다 *네트워크 정책* 기능 Kubernetes 클러스터의 pod 간 수신 및 송신 트래픽에 대 한 규칙을 정의할 수 있습니다.

이 문서에서는 AKS에서 pod 간에 트래픽 흐름을 제어 하려면 Kubernetes 네트워크 정책을 만들고 네트워크 정책 엔진을 설치 하는 방법을 보여 줍니다. 이 기능은 현재 미리 보기로 제공됩니다.

> [!IMPORTANT]
> AKS 미리 보기 기능은 셀프 서비스 및 옵트인 합니다. 미리 보기는 커뮤니티에서 의견 및 버그를 수집 하도록 제공 됩니다. 그러나 Azure 기술 지원 서비스에서 지원 되지 않습니다 됩니다. 클러스터를 만들거나 기존 클러스터에 이러한 기능을 추가 하는 경우에 기능이 더 이상 미리 보기 상태 이며 일반 공급 (GA) 라는 될 때까지 해당 클러스터 지원 되지 않습니다.
>
> 미리 보기 기능을 사용 하 여 문제가 발생 하면 [AKS GitHub 리포지토리에서 문제를 제기] [ aks-github] 버그 제목에 미리 보기 기능의 이름입니다.

## <a name="before-you-begin"></a>시작하기 전에

이상이 설치 및 구성 수 또는 Azure CLI 버전 2.0.61 필요 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

네트워크 정책을 사용 하 여 AKS 클러스터를 만들려면 먼저 구독에 기능 플래그를 사용 하도록 설정 합니다. *EnableNetworkPolicy* 기능 플래그를 등록하려면 다음 예제와 같이 [az feature register][az-feature-register] 명령을 사용합니다.

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

상태가 *Registered*로 표시되는 데 몇 분 정도 걸립니다. 사용 하 여 등록 상태를 확인할 수 있습니다 합니다 [az 기능 목록] [ az-feature-list] 명령:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

준비가 되 면 새로 고침의 등록 된 *Microsoft.ContainerService* 를 사용 하 여 리소스 공급자를 [az provider register] [ az-provider-register] 명령:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-network-policy"></a>네트워크 정책 개요

AKS 클러스터의 모든 pod 수 트래픽을 보내고 받을 제한 없이 기본적으로 합니다. 보안을 강화하기 위해 트래픽의 흐름을 제어하는 규칙을 정의할 수 있습니다. 백 엔드 응용 프로그램 예를 들어 필요한 프런트 엔드 서비스에 노출만 주로 됩니다. 또는 데이터베이스 구성 요소 에서만 연결 하는 응용 프로그램 계층에 액세스할 수 있습니다.

네트워크 정책은 Pod 간 통신에 대 한 액세스 정책을 정의 하는 Kubernetes 사양입니다. 송신 트래픽을 수신 하 고 하나 이상의 레이블에 선택기와 일치 하는 pod의 컬렉션에 적용할 규칙의 정렬된 된 집합을 정의할 네트워크 정책을 사용 하 여, 있습니다.

YAML 매니페스트 이러한 네트워크 정책 규칙 정의 됩니다. 네트워크 정책은 또한 배포 또는 서비스를 만드는 더 넓은 매니페스트의 일부로 포함할 수 있습니다.

### <a name="network-policy-options-in-aks"></a>AKS에서 네트워크 정책 옵션

Azure는 네트워크 정책을 구현 하려면 두 가지 방법으로 제공 합니다. AKS 클러스터를 만들 때 네트워크 정책 옵션을 선택 합니다. 클러스터를 만든 후에 정책 옵션을 변경할 수 없습니다.

* Azure의 구현, 호출 *Azure 네트워크 정책*합니다.
* *Calico 네트워크 정책*에 오픈 소스 네트워크 및 네트워크 보안 솔루션에 의해 설립 되었으며 [Tigera][tigera]합니다.

Linux를 사용 하는 두 가지 구현 *IPTables* 지정 된 정책을 적용 합니다. 정책은 허용 및 허용 되지 않는 IP 쌍의 집합으로 변환 됩니다. IPTable 필터 규칙으로 그런 다음 이러한 쌍 프로그래밍할 수 있습니다.

네트워크 정책 (고급) Azure CNI 옵션 에서만 작동합니다. 구현은 두 가지 옵션에 대 한 다른 것입니다.

* *Azure 네트워크 정책* -Azure CNI 노드 간 네트워킹에 대 한 VM 호스트에서 연결을 설정 합니다. 브리지를 통해 패킷을 전달 하는 경우 필터링 규칙이 적용 됩니다.
* *네트워크 정책 calico* -Azure CNI 노드 간 트래픽에 대 한 로컬 커널 경로 설정 합니다. 정책을은 pod의 네트워크 인터페이스에 적용 됩니다.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Azure 및 Calico 정책 및 해당 기능 간의 차이점

| 기능                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| 지원되는 플랫폼                      | Linux                      | Linux                       |
| 지원 되는 네트워킹 옵션             | Azure CNI                  | Azure CNI                   |
| Kubernetes 사양 준수 | 지원 되는 모든 정책 유형 |  지원 되는 모든 정책 유형 |
| 추가 기능                      | 없음                       | 전역 네트워크 정책, 전역 네트워크 설정 및 호스트 끝점으로 구성 된 정책 모델을 확장 합니다. 사용 하 여 대 한 자세한 내용은 합니다 `calicoctl` 확장 된 기능을 관리 하는 CLI 참조 [calicoctl 사용자 참조][calicoctl]합니다. |
| 지원                                  | Azure 지원 및 엔지니어링 팀에서 지원 | Calico 커뮤니티 지원 합니다. 유료 지원 추가 대 한 자세한 내용은 참조 하세요. [프로젝트 Calico 지원 옵션][calico-support]합니다. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>AKS 클러스터 만들기 및 네트워크 정책 사용

작업에서 네트워크 정책을 보겠습니다 보려는 만들고 트래픽 흐름을 정의 하는 정책을 확장 한 후:

* pod에 대한 모든 트래픽을 거부합니다.
* pod 레이블을 기준으로 트래픽을 허용합니다.
* 네임스페이스를 기준으로 트래픽을 허용합니다.

첫째, 네트워크 정책 지 AKS 클러스터를 만들어 보겠습니다. 네트워크 정책 기능 클러스터를 만들 때에 사용할 수 있습니다. 기존 AKS 클러스터에서는 네트워크 정책을 사용하도록 설정할 수 없습니다.

AKS 클러스터를 사용 하 여 네트워크 정책을 사용 하려면 사용 해야 합니다 [Azure CNI 플러그 인] [ azure-cni] 사용자 고유의 가상 네트워크 및 서브넷을 정의 합니다. 필요한 서브넷 범위를 계획하는 방법에 대한 자세한 내용은 [고급 네트워킹 구성][use-advanced-networking]을 참조하세요.

다음 예제 스크립트는 다음과 같은 작업을 수행합니다.

* 가상 네트워크 및 서브넷을 만듭니다.
* AKS 클러스터와 Azure Active Directory (Azure AD) 사용에 대 한 서비스 주체를 만듭니다.
* 가상 네트워크에서 AKS 클러스터 서비스 주체에 대해 *참가자* 권한을 할당합니다.
* 정의 된 가상 네트워크의 AKS 클러스터를 만들고 네트워크 정책을 사용 하도록 설정 합니다.
    * 합니다 *azure* 네트워크 정책 옵션을 사용 합니다. 네트워크 정책 옵션으로 Calico를 대신 사용 하려면 사용 된 `--network-policy calico` 매개 변수입니다.

사용자 고유의 보안 *SP_PASSWORD*를 제공합니다. 바꿀 수 있습니다 합니다 *RESOURCE_GROUP_NAME* 하 고 *CLUSTER_NAME* 변수:

```azurecli-interactive
SP_PASSWORD=mySecurePassword
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
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

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
    --kubernetes-version 1.12.6 \
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

클러스터를 만드는 데 몇 분이 걸립니다. 클러스터 준비 되 면 구성 `kubectl` 사용 하 여 Kubernetes 클러스터에 연결 합니다 [az aks 자격 증명 가져오기] [ az-aks-get-credentials] 명령입니다. 이 명령은 자격 증명을 다운로드하고 해당 자격 증명을 사용하도록 Kubernetes CLI를 구성합니다.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>pod에 대한 모든 인바운드 트래픽 거부

특정 네트워크 트래픽을 허용하는 규칙을 정의하기 전에 먼저 모든 트래픽을 거부하는 네트워크 정책을 만듭니다. 이 정책을 원하는 트래픽만 허용 목록에 추가 하기 시작할 시작 지점을 제공 합니다. 이 네트워크 정책을 적용하면 트래픽이 삭제되는 것을 명확하게 확인할 수 있습니다.

샘플 응용 프로그램 환경 및 트래픽 규칙의 경우 먼저 만들겠습니다 라는 네임 스페이스 *개발* 예제 pod를 실행 하려면:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

NGINX를 실행 하는 예제에서는 백 엔드 pod를 만듭니다. 이 백 엔드 pod 샘플 백 엔드 웹 기반 응용 프로그램을 시뮬레이션 하기 위해 사용할 수 있습니다. *development* 네임스페이스에서 이 pod를 만들고 포트 *80*을 열어 웹 트래픽을 처리합니다. 다음 섹션에서 네트워크 정책의 대상으로 지정할 수 있도록 pod에 *app=webapp,role=backend* 레이블을 지정합니다.

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

다른 pod를 만들고 기본 NGINX 웹 페이지를 성공적으로 연결할 수 있는지를 테스트 하려면 터미널 세션을 연결 합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트를 사용 하 여 `wget` 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 하려면:

```console
wget -qO- http://backend
```

다음 샘플 출력 기본 NGINX 웹 페이지를 반환 했음을 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod 자동으로 삭제 됩니다.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>네트워크 정책 만들기 및 적용

기본 NGINX 웹 페이지를 사용 하 여 샘플 백 엔드 pod에서 확인 했으므로 들어오는 모든 트래픽을 거부 하도록 네트워크 정책을 만듭니다. `backend-policy.yaml`라는 파일을 만들고 다음 YAML 매니페스트를 붙여 넣습니다. 이 매니페스트를 사용 하는 *podSelector* 정책이 있는 pod를 연결할 합니다 *app:webapp, 역할: 백 엔드* 샘플 NGINX pod와 같은 레이블을. *ingress* 아래에 규칙이 정의되어 있지 않으므로 pod로의 모든 인바운드 트래픽이 거부됩니다.

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

사용 하 여 네트워크 정책을 적용 합니다 [kubectl 적용] [ kubectl-apply] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>네트워크 정책 테스트


다시 백 엔드 pod에서 NGINX 웹 페이지를 사용할 수 있는지 확인해 보겠습니다. 다음과 같이 다른 테스트 Pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트를 사용 하 여 `wget` 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 합니다. 이번에는 제한 시간 값을 *2*초로 설정합니다. 네트워크 정책 이제 모든 인바운드 트래픽을 차단, 페이지를 로드할 수 있도록 다음 예와에서 같이:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

연결된 터미널 세션을 종료합니다. 테스트 pod 자동으로 삭제 됩니다.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>pod 레이블을 기준으로 인바운드 트래픽 허용

이전 섹션에서는 백 엔드 NGINX pod를 예약할 및 모든 트래픽을 거부 하는 네트워크 정책이 생성 된 합니다. 프런트 엔드에 있는 pod를 만들고 프런트 엔드 pod에서 트래픽을 허용 하도록 네트워크 정책 업데이트 해 보겠습니다.

*app:webapp,role:frontend* 레이블을 사용하여 네임스페이스에서 pod의 트래픽을 허용하도록 네트워크 정책을 업데이트합니다. 이전 편집 *백 엔드 policy.yaml* 파일을 추가한 *matchLabels* 수신 규칙 매니페스트에 다음 예제와 같이 표시 되도록 합니다.

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
> 이 네트워크 정책은 수신 규칙에 대해 *namespaceSelector* 및 *podSelector* 요소를 사용합니다. YAML 구문은 되도록 수신 규칙에 대 한 중요 한 가산적입니다. 이 예제에서 수신 규칙이 적용되려면 두 요소가 모두 일치해야 합니다. Kubernetes 버전을 이전 *1.12* 이러한 요소를 올바르게 해석 및 예상한 대로 네트워크 트래픽을 제한 하지 않을 수 있습니다. 이 동작에 대 한 자세한 내용은 참조 하세요 [선택기에서의 동작][policy-rules]합니다.

사용 하 여 업데이트 된 네트워크 정책을 적용 합니다 [kubectl 적용] [ kubectl-apply] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

으로 표시 되는 pod를 예약 *앱 = webapp, 역할 = frontend* 터미널 세션을 연결 및:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

셸 프롬프트를 사용 하 여 `wget` 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 하려면:

```console
wget -qO- http://backend
```

수신 규칙 레이블이 있는 pod 사용 하 여 트래픽을 허용 하므로 *앱: 웹 앱을 역할: 프런트 엔드*, 프런트 엔드 pod에서 트래픽이 허용 됩니다. 다음 예제 출력에서는 반환 된 기본 NGINX 웹 페이지를 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. Pod 자동으로 삭제 됩니다.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>일치하는 레이블이 없는 pod 테스트

네트워크 정책은 *app: webapp,role: frontend* 레이블이 지정된 pod의 트래픽을 허용하지만 다른 모든 트래픽을 거부합니다. 이러한 레이블 없이 다른 pod 백 엔드 NGINX pod를 액세스할 수 있는지를 테스트해 보겠습니다. 다음과 같이 다른 테스트 Pod를 만들고 터미널 세션을 연결합니다.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

셸 프롬프트를 사용 하 여 `wget` 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 합니다. 네트워크 정책이 차단 인바운드 트래픽의 경우 페이지를 로드할 수 있도록 다음 예와에서 같이:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

연결된 터미널 세션을 종료합니다. 테스트 pod 자동으로 삭제 됩니다.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>정의된 네임스페이스 내의 트래픽만 허용

이전 예제에서 모든 트래픽을 거부 하 고 특정 레이블이 있는 pod에서 트래픽을 허용 하도록 정책을 업데이트 하는 네트워크 정책을 만들었습니다. 다른 일반적인 필요 지정된 된 네임 스페이스 내 에서만 트래픽을 제한 하는 것입니다. 이전 예제에서 트래픽에 대해 되었으면를 *개발* 네임 스페이스와 같은 다른 네임 스페이스에서 트래픽을 방지 하는 네트워크 정책 만들기 *프로덕션*를 pod에 도달 하지 못하도록 합니다.

먼저, 프로덕션 네임스페이스를 시뮬레이트하는 새 네임스페이스를 만듭니다.

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

*app=webapp,role=frontend* 레이블이 지정된 *production* 네임스페이스에서 테스트 pod를 예약합니다. 다음과 같이 터미널 세션을 연결합니다.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

셸 프롬프트를 사용 하 여 `wget` 기본 NGINX 웹 페이지에 액세스할 수 있는지 확인 하려면:

```console
wget -qO- http://backend.development
```

Pod에 대 한 레이블을 현재 네트워크 정책에서 허용 된 것을 일치 하므로 트래픽이 허용 됩니다. 네트워크 정책은 pod 레이블만 확인하고, 네임스페이스는 확인하지 않습니다. 다음 예제 출력에서는 반환 된 기본 NGINX 웹 페이지를 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod 자동으로 삭제 됩니다.

```console
exit
```

### <a name="update-the-network-policy"></a>네트워크 정책 업데이트

수신 규칙을 업데이트 해 보겠습니다 *namespaceSelector* 내에서 트래픽을 허용 하도록 섹션의 *개발* 네임 스페이스입니다. 다음 예제와 같이 *backend-policy.yaml* 매니페스트 파일을 편집합니다.

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

더 복잡 한 예제에서 정의할 수 있습니다 여러 수신 규칙을 같은 *namespaceSelector* 차례로 *podSelector*합니다.

사용 하 여 업데이트 된 네트워크 정책을 적용 합니다 [kubectl 적용] [ kubectl-apply] 명령 및 YAML 매니페스트의 이름을 지정 합니다.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>업데이트된 네트워크 정책 테스트

다른 pod를 예약 합니다 *프로덕션* 네임 스페이스 및 터미널 세션에 연결:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

셸 프롬프트를 사용 하 여 `wget` 네트워크 정책에 이제 트래픽을 거부를 확인 하려면:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

테스트 pod를 종료합니다.

```console
exit
```

거부 하는 트래픽을 합니다 *프로덕션* 테스트 pod 다시 네임 스페이스, 예약 합니다 *개발* 네임 스페이스 터미널 세션을 연결 하 고:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

셸 프롬프트를 사용 하 여 `wget` 네트워크 정책에는 트래픽을 허용 하는지 확인 하려면:

```console
wget -qO- http://backend
```

일치 항목 사용할 수 있는 네트워크 정책에서 pod 네임 스페이스에 예약 되어 트래픽이 허용 됩니다. 다음 예제 출력에서는 반환 된 기본 NGINX 웹 페이지를 보여 줍니다.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

연결된 터미널 세션을 종료합니다. 테스트 pod 자동으로 삭제 됩니다.

```console
exit
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서는 두 네임 스페이스를 생성 하 고 네트워크 정책을 적용 합니다. 이러한 리소스를 정리 하려면 사용 합니다 [kubectl 삭제] [ kubectl-delete] 명령 및 리소스 이름 지정:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>다음 단계

네트워크 리소스에 대 한 자세한 내용은 참조 하세요 [네트워크 Azure Kubernetes Service (AKS)에서 응용 프로그램에 대 한 개념][concepts-network]합니다.

정책에 대 한 자세한 내용은 참조 하세요 [Kubernetes 네트워크 정책][kubernetes-network-policies]합니다.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues]
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.5/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
