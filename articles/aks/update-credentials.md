---
title: AKS(Azure Kubernetes Service) 클러스터의 자격 증명 다시 설정
description: AKS(Azure Kubernetes Service)에서 클러스터의 서비스 주체 자격 증명을 업데이트하거나 다시 설정하는 방법을 알아봅니다.
services: container-service
author: rockboyfor
ms.service: container-service
ms.topic: article
origin.date: 01/30/2019
ms.date: 03/04/2019
ms.author: v-yeche
ms.openlocfilehash: d880615d0d132403c935fe39e8478d7b3fc48dbe
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61029365"
---
# <a name="update-or-rotate-the-credentials-for-a-service-principal-in-azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)에서 서비스 주체의 자격 증명 업데이트 또는 회전

기본적으로 AKS 클러스터는 만료 시간이 1년인 서비스 주체로 만들어집니다. 만료 날짜가 가까워지면 자격 증명을 다시 설정하여 서비스 주체를 추가 기간 동안 연장할 수 있습니다. 정의된 보안 정책의 일부로 자격 증명을 업데이트하거나 회전할 수도 있습니다. 이 문서에서는 AKS 클러스터의 이 자격 증명을 업데이트하는 방법을 자세히 설명합니다.

## <a name="before-you-begin"></a>시작하기 전에

Azure CLI 버전 2.0.56 이상이 설치되고 구성되어 있어야 합니다.  `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우  [Azure CLI 설치][install-azure-cli]를 참조하세요.

## <a name="choose-to-update-or-create-a-service-principal"></a>서비스 주체를 업데이트하거나 만들도록 선택

AKS 클러스터의 자격 증명을 업데이트하려면 다음을 선택하면 됩니다.

* 클러스터에서 사용하는 기존 서비스 주체의 자격 증명을 업데이트하거나,
* 서비스 주체를 만들고 이 새 자격 증명을 사용하도록 클러스터를 업데이트합니다.

서비스 주체를 만든 후 AKS 클러스터를 업데이트하려면 이 섹션의 나머지 단계를 건너뛰고 계속해서 [서비스 주체를 만듭니다](#create-a-service-principal). AKS 클러스터에서 사용하는 기존 서비스 주체의 자격 증명을 업데이트하려면 이 섹션의 단계를 계속 진행합니다.

### <a name="get-the-service-principal-id"></a>서비스 주체 ID 가져오기

기존 서비스 주체의 자격 증명을 업데이트하려면 [az aks show][az-aks-show] 명령을 사용하여 클러스터의 서비스 주체 ID를 가져옵니다. 다음 예제에서는 *myResourceGroup* 리소스 그룹에서 *myAKSCluster* 클러스터의 ID를 가져옵니다. 서비스 주체 ID는 추가 명령에서 사용할 변수로 설정됩니다.

```azurecli
SP_ID=$(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
```

### <a name="update-the-service-principal-credentials"></a>서비스 주체 자격 증명 업데이트

서비스 주체 ID가 포함된 변수 세트와 함께, 이제 [az ad sp credential reset][az-ad-sp-credential-reset]을 사용하여 자격 증명을 다시 설정합니다. 다음 예제에서는 Azure 플랫폼에서 서비스 주체의 새 보안 비밀을 생성해보겠습니다. 이 새 보안 비밀도 변수로 저장됩니다.

```azurecli
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

이제 계속해서 [새 자격 증명으로 AKS 클러스터를 업데이트](#update-aks-cluster-with-new-credentials)합니다.

## <a name="create-a-service-principal"></a>서비스 주체 만들기

이전 섹션에서 기존 서비스 주체 자격 증명을 업데이트하도록 선택한 경우 이 단계를 건너뜁니다. 계속해서 [새 자격 증명으로 AKS 클러스터를 업데이트](#update-aks-cluster-with-new-credentials)합니다.

서비스 주체를 만든 후 이 새 자격 증명을 사용하도록 AKS 클러스터를 업데이트하려면 [az ad sp create-for-rbac][az-ad-sp-create] 명령을 사용합니다. 다음 예제에서 `--skip-assignment` 매개 변수는 추가 기본 할당이 할당되는 것을 방지합니다.

```azurecli
az ad sp create-for-rbac --skip-assignment
```

다음 예제와 유사하게 출력됩니다. 사용자 고유의 `appId` 및 `password`를 기록해 둡니다. 해당 값은 다음 단계에서 사용됩니다.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

다음 예제와 같이 고유한 [az ad sp create-for-rbac][az-ad-sp-create] 명령의 출력을 사용하여 서비스 주체 ID 및 클라이언트 암호의 변수를 정의합니다. *SP_ID*는 사용자의 ‘앱 ID’이고, *SP_SECRET*은 사용자의 ‘암호’입니다.

```azurecli
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

## <a name="update-aks-cluster-with-new-credentials"></a>새 자격 증명으로 AKS 클러스터 업데이트

기존 서비스 주체의 자격 증명을 업데이트하거나 서비스 주체를 만들도록 선택했는지 여부에 관계없이 이제 [az aks update-credentials][az-aks-update-credentials] 명령을 사용하여 새 자격 증명으로 AKS 클러스터를 업데이트합니다. *--service-principal* 및 *--client-secret*의 변수는 다음과 같이 사용됩니다.

```azurecli
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

서비스 주체 자격 증명이 AKS에서 업데이트되는 데 몇 분 정도 걸립니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AKS 클러스터 자체의 서비스 주체가 업데이트되었습니다. 클러스터 내에서 워크로드의 ID를 관리하는 방법에 대한 자세한 내용은 [AKS의 인증 및 권한 부여 모범 사례][best-practices-identity]를 참조하세요.

<!-- LINKS - internal -->
[install-azure-cli]: https://docs.azure.cn/zh-cn/cli/install-azure-cli?view=azure-cli-latest
[az-aks-show]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-show
[az-aks-update-credentials]: https://docs.microsoft.com/cli/azure/aks?view=azure-cli-latest#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[az-ad-sp-create]: https://docs.azure.cn/zh-cn/cli/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: https://docs.azure.cn/zh-cn/cli/ad/sp/credential?view=azure-cli-latest#az-ad-sp-credential-reset