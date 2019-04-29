---
title: (사용되지 않음) Azure Kubernetes에서 Helm을 이용한 컨테이너 배포
description: Helm 패키징 도구를 사용하여 Azure Container Service의 Kubernetes 클러스터에 컨테이너 배포
services: container-service
author: sauryadas
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/10/2017
ms.author: saudas
ms.custom: mvc
ms.openlocfilehash: 05edbf40e8cd5f8edbdc8b74b540962b1a25c8de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60712315"
---
# <a name="deprecated-use-helm-to-deploy-containers-on-a-kubernetes-cluster"></a>(사용되지 않음) Helm을 사용하여 Kubernetes 클러스터에 컨테이너 배포

> [!TIP]
> Azure Kubernetes Service를 사용하는 이 문서의 업데이트된 버전은 [AKS(Azure Kubernetes Service)에서 Helm을 사용하여 애플리케이션 설치](../../aks/kubernetes-helm.md)를 참조하세요.

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

[Helm](https://github.com/kubernetes/helm/)은 Kubernetes 응용 프로그램을 설치하고 수명 주기를 관리하는 오픈 소스 패키징 도구입니다. Apt-get, Yum 등의 Linux 패키지 관리자와 마찬가지로 Helm은 사전 구성된 Kubernetes 리소스의 패키지인 Kubernetes 차트를 관리하는 데 사용합니다. 이 문서는 Azure Container Service에 배포된 Kubernetes 클러스터에서 Helm을 이용하여 작업하는 방법을 보여줍니다.

Helm에는 두 가지 구성 요소가 있습니다. 
* **Helm CLI**는 사용자 컴퓨터에서 로컬로 또는 클라우드에서 실행되는 클라이언트입니다  

* **Tiller**는 Kubernetes 클러스터에서 실행되는 서버로 Kubernetes 애플리케이션의 수명 주기를 관리합니다 
 
## <a name="prerequisites"></a>필수 조건

* Azure Container Service에서 [Kubernetes 클러스터 생성](container-service-kubernetes-walkthrough.md)

* 로컬 컴퓨터에서 [설치 및 구성`kubectl`](../container-service-connect.md)

* 로컬 컴퓨터에 [Helm 설치](https://github.com/kubernetes/helm/blob/master/docs/install.md)

## <a name="helm-basics"></a>Helm 기초 

Tiller를 설치하고 애플리케이션을 배포하는 Kubernetes 클러스터에 대한 정보를 보려면 다음 명령을 입력합니다.

```bash
kubectl cluster-info 
```
![kubectl cluster-info](./media/container-service-kubernetes-helm/clusterinfo.png)
 
Helm을 설치한 후 다음 명령을 입력하여 Kubernetes 클러스터에 Tiller를 설치합니다.

```bash
helm init --upgrade
```
설치가 성공적으로 완료되면 다음과 같은 출력이 표시됩니다.

![Tiller 설치](./media/container-service-kubernetes-helm/tiller-install.png)
 
 
 
 
리포지토리에서 사용 가능한 모든 Helm 차트를 보려면 다음 명령을 입력합니다.

```bash 
helm search 
```

다음과 같은 출력이 표시됩니다.

![Helm 검색](./media/container-service-kubernetes-helm/helm-search.png)
 
차트를 업데이트하여 최신 버전을 가져오려면 다음을 입력합니다.

```bash 
helm repo update 
```
## <a name="deploy-an-nginx-ingress-controller-chart"></a>Nginx 수신 컨트롤러 차트 배포 
 
Nginx 수신 컨트롤러 차트를 배포하려면 단일 명령을 입력합니다.

```bash
helm install stable/nginx-ingress 
```
![수신 컨트롤러 배포](./media/container-service-kubernetes-helm/nginx-ingress.png)

클러스터에서 실행되는 모든 서비스를 보려면 `kubectl get svc`를 입력합니다. IP 주소가 수신 컨트롤러에 할당된 것을 확인할 수 있습니다. (할당이 진행 중인 동안 `<pending>`이 표시됩니다. 완료될 때까지 2~3분이 소요됩니다.) 

IP 주소가 할당된 후 Nginx 백엔드가 실행 중인 것을 보려면 외부 IP 주소 값으로 이동합니다. 
 
![수신 IP 주소](./media/container-service-kubernetes-helm/ingress-ip-address.png)


클러스터에 설치된 차트 목록을 보려면 다음을 입력합니다.

```bash
helm list 
```

명령을 `helm ls`로 줄일 수 있습니다.
 
 
 
 
## <a name="deploy-a-mariadb-chart-and-client"></a>MariaDB 차트 및 클라이언트 배포

이제 MariaDB 차트 및 MariaDB 클라이언트를 배포하여 데이터베이스에 연결합니다.

MariaDB 차트를 배포하려면 다음 명령을 입력합니다.

```bash
helm install --name v1 stable/mariadb
```

여기서 `--name`은 릴리스에 사용되는 태그입니다.

> [!TIP]
> 배포가 실패할 경우 `helm repo update`를 실행하고 다시 시도합니다.
>
 
 
클러스터에 배포된 모든 차트를 보려면 다음을 입력합니다.

```bash 
helm list
```
 
클러스터에서 실행 중인 모든 배포를 보려면 다음을 입력합니다.

```bash
kubectl get deployments 
``` 
 
 
마지막으로 Pod를 실행하여 클라이언트에 액세스하려면 다음을 입력합니다.

```bash
kubectl run v1-mariadb-client --rm --tty -i --image bitnami/mariadb --command -- bash  
``` 
 
 
클라이언트에 연결하려면 다음 명령을 입력하고 `v1-mariadb`를 배포 이름으로 바꿉니다.

```bash
sudo mysql –h v1-mariadb
```
 
 
이제 표준 SQL 명령을 사용하여 데이터베이스, 테이블 등을 만들 수 있습니다. 예를 들어 `Create DATABASE testdb1;`은 빈 데이터베이스를 만듭니다. 
 
 
 
## <a name="next-steps"></a>다음 단계

* Kubernetes 차트 관리에 대한 자세한 내용을 보려면 [Helm 설명서](https://github.com/kubernetes/helm/blob/master/docs/index.md)를 참조하십시오. 

