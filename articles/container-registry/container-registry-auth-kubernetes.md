---
title: Kubernetes 클러스터에서 인증
description: 서비스 주체를 사용 하 여 끌어오기 암호를 만들어 Azure container registry의 이미지에 대 한 액세스 권한이 있는 Kubernetes 클러스터를 제공 하는 방법을 알아봅니다.
ms.topic: article
author: karolz-ms
ms.author: karolz
ms.reviewer: danlep
ms.date: 02/10/2020
ms.openlocfilehash: 0608ca0e0e53acf2f19910a7f1107dacf67d4e61
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154895"
---
# <a name="pull-images-from-an-azure-container-registry-to-a-kubernetes-cluster"></a>Azure container registry에서 Kubernetes 클러스터로 이미지 끌어오기

Kubernetes 클러스터를 사용 하 여 Azure container registry를 컨테이너 이미지의 원본으로 사용할 수 있습니다 (예: [minikube](https://minikube.sigs.k8s.io/) 및 [kind](https://kind.sigs.k8s.io/)와 같은 "local" Kubernetes 클러스터 포함). 이 문서에서는 Azure Active Directory 서비스 주체를 기반으로 Kubernetes pull 비밀을 만드는 방법을 보여 줍니다. 그런 다음 암호를 사용 하 여 Kubernetes 배포의 Azure container registry에서 이미지를 가져옵니다.

> [!TIP]
> 관리 되는 [Azure Kubernetes 서비스](../aks/intro-kubernetes.md)를 사용 하는 경우 이미지 끌어오기를 위해 클러스터를 대상 azure container registry와 [통합할](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json) 수도 있습니다. 

이 문서에서는 개인 Azure container registry를 이미 만들었다고 가정 합니다. 또한 `kubectl` 명령줄 도구를 통해 실행 되 고 액세스할 수 있는 Kubernetes 클러스터가 있어야 합니다.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

서비스 사용자 암호를 저장 하거나 저장 하지 않는 경우 [az ad sp credential reset][az-ad-sp-credential-reset] 명령을 사용 하 여 다시 설정할 수 있습니다.

```azurecli
az ad sp credential reset  --name http://<service-principal-name> --query password --output tsv
```

이 명령은 서비스 사용자에 대해 유효한 새 암호를 반환 합니다.

## <a name="create-an-image-pull-secret"></a>이미지 끌어오기 비밀 만들기

Kubernetes는 *이미지 끌어오기 암호* 를 사용 하 여 레지스트리를 인증 하는 데 필요한 정보를 저장 합니다. Azure container registry에 대 한 끌어오기 암호를 만들려면 서비스 주체 ID, 암호 및 레지스트리 URL을 제공 합니다. 

다음 `kubectl` 명령을 사용 하 여 이미지 끌어오기 암호를 만듭니다.

```console
kubectl create secret docker-registry <secret-name> \
  --namespace <namespace> \
  --docker-server=https://<container-registry-name>.azurecr.io \
  --docker-username=<service-principal-ID> \
  --docker-password=<service-principal-password>
```
각 항목이 나타내는 의미는 다음과 같습니다.

| 값 | 설명 |
| :--- | :--- |
| `secret-name` | 이미지 풀 비밀의 이름 (예: *acr 비밀)* |
| `namespace` | 비밀을 넣을 Kubernetes 네임 스페이스 <br/> 기본 네임 스페이스 이외의 네임 스페이스에 비밀을 저장 하려는 경우에만 필요 합니다. |
| `container-registry-name` | Azure container registry의 이름 |
| `service-principal-ID` | Kubernetes에서 레지스트리에 액세스 하는 데 사용 하는 서비스 주체의 ID입니다. |
| `service-principal-password` | 서비스 사용자 암호 |

## <a name="use-the-image-pull-secret"></a>이미지 끌어오기 비밀 사용

이미지 끌어오기 암호를 만든 후에는이를 사용 하 여 Kubernetes pod 및 배포를 만들 수 있습니다. 배포 파일의 `imagePullSecrets`에 있는 암호의 이름을 제공 합니다. 예를 들면 다음과 같습니다.

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: your-awesome-app-pod
  namespace: awesomeapps
spec:
  containers:
    - name: main-app-container
      image: your-awesome-app:v1
      imagePullPolicy: IfNotPresent
  imagePullSecrets:
    - name: acr-secret
```

앞의 예제에서 `your-awesome-app:v1`는 Azure container registry에서 끌어올 이미지의 이름이 며 `acr-secret`은 레지스트리에 액세스 하기 위해 만든 끌어오기 암호의 이름입니다. Pod를 배포할 때 Kubernetes는 클러스터에 아직 없는 경우 레지스트리에서 이미지를 자동으로 끌어옵니다.


## <a name="next-steps"></a>다음 단계

* 서비스 사용자 및 Azure Container Registry 작업에 대 한 자세한 내용은 [서비스 주체를 사용 하 여 인증 Azure Container Registry](container-registry-auth-service-principal.md) 을 참조 하세요.
* [Kubernetes 설명서](https://kubernetes.io/docs/concepts/containers/images/#specifying-imagepullsecrets-on-a-pod) 에서 이미지 풀 비밀에 대 한 자세한 정보


<!-- IMAGES -->

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset