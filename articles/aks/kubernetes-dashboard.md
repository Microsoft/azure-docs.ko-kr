---
title: 웹 대시보드를 사용하여 Azure Kubernetes Service 클러스터를 관리합니다.
description: AKS(Azure Kubernetes Service) 클러스터를 관리하는 데 기본 제공 Kubernetes 웹 UI 대시보드를 사용하는 방법 알아보기
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 35424c0a9e566a9dfa780c524e23945348335040
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225991"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 Kubernetes 웹 대시보드에 액세스

Kubernetes에는 기본 관리 작업에 사용할 수 있는 웹 대시보드가 포함됩니다. 이 대시보드를 사용하면 애플리케이션의 기본 상태와 메트릭을 보고 서비스를 작성 및 배포하며 기존 애플리케이션을 편집할 수 있습니다. 이 문서에서는 Azure CLI를 사용하여 Kubernetes 대시보드에 액세스하는 방법을 보여준 후 일부 기본적인 대시보드 작업 과정을 안내합니다.

Kubernetes 대시보드에 대한 자세한 내용은 [Kubernetes 웹 UI 대시보드][kubernetes-dashboard]를 참조하세요. AKS는 버전 2.0 이상의 오픈 소스 대시보드를 사용 합니다.

> [!WARNING]
> **AKS 대시보드 추가 기능이 사용 중단 되도록 설정 되었습니다. 대신 [Azure Portal (미리 보기)에서 Kubernetes 리소스 뷰][kubernetes-portal] 를 사용 합니다.** 
> * Kubernetes 대시보드는 1.18 미만의 Kubernetes 버전을 실행 하는 클러스터에 대해 기본적으로 사용 하도록 설정 됩니다.
> * Kubernetes 1.18 이상에서 만든 모든 새 클러스터에 대해 기본적으로 대시보드 추가 기능이 사용 하지 않도록 설정 됩니다. 
 > * Kubernetes 1.19부터 미리 보기에서 AKS은 더 이상 관리 되는 kube 추가 기능을 설치 하는 것을 지원 하지 않습니다. 
 > * 추가 기능이 사용 하도록 설정 된 기존 클러스터는 영향을 받지 않습니다. 사용자는 계속 해 서 사용자가 설치한 소프트웨어로 오픈 소스 대시보드를 수동으로 설치할 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

이 문서에 자세히 설명 된 단계에서는 AKS 클러스터를 만들고 `kubectl` 클러스터와의 연결을 설정 했다고 가정 합니다. AKS 클러스터를 만들어야 하는 경우 [빠른 시작: Azure CLI을 사용 하 여 Azure Kubernetes Service 클러스터 배포][aks-quickstart]를 참조 하세요.

또한 Azure CLI 버전 2.6.0 이상이 설치 및 구성 되어 있어야 합니다.  `az --version` 을 실행하여 버전을 찾습니다. 설치하거나 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="disable-the-kubernetes-dashboard"></a>Kubernetes 대시보드 사용 안 함

Kube-dashboard 추가 기능은 **K8s 1.18 보다 오래 된 클러스터에서 기본적으로 사용 하도록 설정**됩니다. 다음 명령을 실행 하 여 추가 기능을 사용 하지 않도록 설정할 수 있습니다.

``` azure-cli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Kubernetes 대시보드 시작

클러스터에서 Kubernetes 대시보드를 시작 하려면 [az aks browse][az-aks-browse] 명령을 사용 합니다. 이 명령을 사용 하려면 클러스터에 kube 추가 기능을 설치 해야 합니다 .이 추가 기능은 Kubernetes 1.18 보다 오래 된 버전을 실행 하는 클러스터에 기본적으로 포함 되어 있습니다.

다음 예제에서는 *myResourceGroup* 리소스 그룹에 *myAKSCluster*라는 클러스터의 대시보드를 엽니다.

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

이 명령은 개발 시스템과 Kubernetes API 사이에 프록시를 만들고 웹 브라우저에서 Kubernetes 대시보드를 엽니다. 웹 브라우저가 Kubernetes 대시보드에서 열리지 않는 경우 Azure CLI에 표시된 URL 주소(일반적으로 `http://127.0.0.1:8001`)를 복사하여 붙여넣으세요.

> [!NOTE]
> 에서 대시보드가 표시 되지 않으면 `http://127.0.0.1:8001` 다음 주소로 수동으로 라우팅할 수 있습니다. 1.16 이상의 클러스터에는 https를 사용 하 고 별도의 끝점이 필요 합니다.
> * K8s 1.16 이상: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1.15 이상: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>대시보드에 로그인 (kubernetes 1.16 +)

> [!IMPORTANT]
> Kubernetes 대시보드 또는 Kubernetes v 1.16 + [의 v 1.10.1](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) 에서 서비스 계정 "Kubernetes-대시보드"는 [해당 릴리스의 보안 수정](https://github.com/kubernetes/dashboard/pull/3400)으로 인해 더 이상 리소스를 검색 하는 데 사용할 수 없습니다. 따라서 인증 정보가 없는 요청은 401 권한 없음 오류를 반환 합니다. 서비스 계정에서 검색 된 전달자 토큰은이 [Kubernetes 대시보드 예제](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui)와 같이 여전히 사용할 수 있지만이는 이전 버전과 비교 하 여 대시보드 추가 기능에 대 한 로그인 흐름에 영향을 줍니다.
>
>1.16 이전 버전을 실행 하는 경우에도 "kubernetes" 서비스 계정에 권한을 부여할 수 있지만 **권장 되지는 않습니다**.
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

제공 된 초기 화면에는 kubeconfig 또는 토큰이 필요 합니다. 두 옵션 모두 대시보드에 해당 리소스를 표시 하려면 리소스 권한이 필요 합니다.

![로그인 화면](./media/kubernetes-dashboard/login.png)

**Kubeconfig 사용**

Azure AD를 사용 하도록 설정 된 azure ad 및 비 Azure AD 사용 클러스터의 경우 kubeconfig를 전달할 수 있습니다. 액세스 토큰이 유효한 지 확인 합니다. 토큰이 만료 된 경우 kubectl를 통해 토큰을 새로 고칠 수 있습니다.

1. Admin kubeconfig을로 설정 합니다. `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. 선택 `Kubeconfig` 하 고 클릭 `Choose kubeconfig file` 하 여 파일 선택기 열기
1. Kubeconfig 파일 선택 (기본값은 $HOME/.kube/config)
1. `Sign In`을 클릭합니다.

**토큰 사용**

1. **비 AZURE AD를 사용할 수 있는 클러스터**의 경우를 실행 `kubectl config view` 하 고 클러스터의 사용자 계정과 연결 된 토큰을 복사 합니다.
1. 로그인에서 토큰 옵션에 붙여넣습니다.    
1. `Sign In`을 클릭합니다.

Azure AD 사용 클러스터의 경우 다음 명령을 사용 하 여 AAD 토큰을 검색 합니다. 명령에서 리소스 그룹 및 클러스터 이름을 바꾼 유효성을 검사 합니다.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

성공적으로 완료 되 면 아래와 유사한 페이지가 표시 됩니다.

![Kubernetes 웹 대시보드의 개요 페이지](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>애플리케이션 만들기

다음 단계를 수행 하려면 사용자에 게 해당 리소스에 대 한 사용 권한이 있어야 합니다. 

Kubernetes 대시보드가 관리 작업의 복잡도를 줄일 수 있는 방법을 보기 위해 애플리케이션을 작성해 보겠습니다. 텍스트 입력, YAML 파일을 제공하거나 그래픽 마법사를 통해 Kubernetes 대시보드에서 애플리케이션을 작성할 수 있습니다.

애플리케이션을 작성하려면 다음 단계를 완료하세요.

1. 오른쪽 상단 창에서 **만들기** 단추를 선택하세요.
1. 그래픽 마법사를 사용하려면 **앱 만들기**를 선택하세요.
1. 배치 이름(예: *nginx*)을 입력하세요.
1. 사용할 컨테이너 이미지의 이름(예: *1.15.5*)을 입력하세요.
1. 웹 트래픽에 대해 포트 80을 노출하려면 Kubernetes 서비스를 작성하세요. **서비스**에서 **외부**를 선택하고 포트와 대상 포트 모두에 대해 **80**을 입력합니다.
1. 준비가 되면 **배포** 를 선택하여 앱을 만듭니다.

![Kubernetes 웹 대시보드에 앱 배포](./media/kubernetes-dashboard/create-app.png)

공용 외부 IP 주소가 Kubernetes 서비스에 지정되는 데 1분이나 2분이 소요됩니다. 왼쪽에 있는 **감지 및 로드 밸런싱**에서 **서비스**를 선택하세요. 다음 예제에 표시된 대로 ‘외부 엔드포인트’를 포함하여 애플리케이션 서비스가 나열됩니다.**

![서비스 및 엔드포인트 목록 보기](./media/kubernetes-dashboard/view-services.png)

웹 브라우저 창을 기본 NGINX 페이지로 열려면 엔드포인트 주소를 선택하세요.

![배포된 애플리케이션의 기본 NGINX 페이지 보기](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Pod 정보 보기

Kubernetes 대시보드는 기본 모니터링 메트릭 및 문제점 해결 정보(예:로그)를 제공할 수 있습니다.

애플리케이션 Pod에 대한 자세한 정보를 보려면 왼쪽 메뉴에서 **Pod**을 선택하세요. 사용 가능한 Pod 목록이 표시됩니다. 리소스 사용량과 같은 정보를 보려면 *nginx* Pod를 선택하세요.

![Pod 정보 보기](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>애플리케이션 편집

Kubernetes 대시보드는 애플리케이션을 만들고 보는 것 외에도 애플리케이션 배포를 편집하고 업데이트하는 데 사용할 수 있습니다. 애플리케이션에 추가 중복을 제공하려면 NGINX 복제본의 수를 늘리세요.

배포를 편집하려면 다음을 수행하세요.

1. 왼쪽 메뉴에서 **배치**를 선택한 다음, *nginx* 배치를 선택하세요.
1. 오른쪽 위 탐색 표시줄에서 **편집**을 선택합니다.
1. 행 20 정도에서 `spec.replica` 값을 찾으세요. 애플리케이션의 복제본 수를 늘리려면 이 값을 *1*에서 *3*으로 변경하세요.
1. 준비가 되면 **업데이트**를 선택합니다.

![배치를 편집하여 복제본 수를 업데이트세요.](./media/kubernetes-dashboard/edit-deployment.png)

새 Pod이 복제본 세트 내에 작성되는 데 몇 분 정도 걸립니다. 왼쪽 메뉴에서 **복제본 세트**를 선택한 다음, *nginx* 복제본 세트를 선택합니다. 이제 Pod 목록은 다음 예제 출력에 표시된 대로 업데이트된 복제본 수를 반영합니다.

![복제본 세트에 대한 정보 보기](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>다음 단계

Kubernetes 대시보드에 대한 자세한 내용은 [Kubernetes 웹 UI 대시보드][kubernetes-dashboard]를 참조하세요.

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
