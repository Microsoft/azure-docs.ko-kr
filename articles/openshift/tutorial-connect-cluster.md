---
title: 자습서 - Azure Red Hat OpenShift 4 클러스터 연결
description: Microsoft Azure Red Hat OpenShift 클러스터를 연결하는 방법 알아보기
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 226cf29b1a94b4508a9d68f02b7400a18eba4bc2
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587857"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>자습서: Azure Red Hat OpenShift 4 클러스터 연결

총 3부 중 2부에 해당하는 이 자습서에서는 OpenShift 웹 콘솔을 통해 kubeadmin 사용자로 OpenShift 4를 실행하는 Azure Red Hat OpenShift 클러스터에 연결합니다. 다음 방법을 알아봅니다.
> [!div class="checklist"]
> * 클러스터의 `kubeadmin` 자격 증명 획득
> * OpenShift CLI 설치
> * OpenShift CLI를 사용하여 Azure Red Hat OpenShift 클러스터에 연결

## <a name="before-you-begin"></a>시작하기 전에

이전 자습서에서는 Azure Red Hat OpenShift 클러스터를 만들었습니다. 이전 단계를 아직 수행하지 않았지만 지금부터 따라 하려는 분들은 [자습서 1 - Azure Red Hat Openshift 4 클러스터 만들기](tutorial-create-cluster.md)를 먼저 진행하세요.

CLI를 로컬로 설치하고 사용하도록 선택한 경우 이 자습서에서는 Azure CLI 버전 2.6.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)를 참조하세요.

## <a name="connect-to-the-cluster"></a>클러스터에 연결

`kubeadmin` 사용자를 사용하여 클러스터에 로그인할 수 있습니다.  다음 명령을 실행하여 `kubeadmin` 사용자의 암호를 찾습니다.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

다음 예제 출력에서는 암호가 `kubeadminPassword`에 있는 것을 보여줍니다.

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

다음 명령을 실행하여 `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` 형식의 클러스터 콘솔 URL을 찾을 수 있습니다.

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

브라우저에서 콘솔 URL을 시작하고 `kubeadmin` 자격 증명을 사용하여 로그인합니다.

![Azure Red Hat OpenShift 로그인 화면](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>OpenShift CLI 설치

OpenShift 웹 콘솔에 로그인했으면 오른쪽 위에서 **?** 를 클릭한 다음, **명령줄 도구**를 클릭합니다. 머신에 맞는 릴리스를 다운로드합니다.

![Azure Red Hat OpenShift 로그인 화면](media/aro4-download-cli.png)

<https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>에서 현재 사용하는 머신에 맞는 최신 CLI 릴리스를 다운로드할 수도 있습니다.

Azure Cloud Shell에서 명령을 실행하는 경우 Linux용 최신 OpenShift 4 CLI를 다운로드합니다.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>OpenShift CLI를 사용하여 연결

API 서버 주소를 검색합니다.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

다음 명령을 사용하여 OpenShift 클러스터의 API 서버에 로그인합니다. **\<kubeadmin password>** 를 방금 검색한 암호로 바꿉니다.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>다음 단계

자습서의 이 부분에서는 다음 방법에 대해 알아봅니다.
> [!div class="checklist"]
> * 클러스터의 `kubeadmin` 자격 증명 획득
> * OpenShift CLI 설치
> * OpenShift CLI를 사용하여 Azure Red Hat OpenShift 클러스터에 연결

다음 자습서를 진행합니다.
> [!div class="nextstepaction"]
> [Azure Red Hat OpenShift 클러스터 삭제](tutorial-delete-cluster.md)
