---
title: Azure Kubernetes Service의 Azure Container Registry를 사용하여 인증
description: Azure Active Directory 서비스 주체를 사용하여 Azure Kubernetes Service에서 개인 컨테이너 레지스트리에 있는 이미지에 대한 액세스 권한을 제공하는 방법에 대해 알아봅니다.
services: container-service
author: dlepow
ms.service: container-service
ms.topic: article
ms.date: 08/08/2018
ms.author: danlep
ms.openlocfilehash: 1d7e130d619f580aeb82939e19ea5abf680ff039
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61333619"
---
# <a name="authenticate-with-azure-container-registry-from-azure-kubernetes-service"></a>Azure Kubernetes Service의 Azure Container Registry를 사용하여 인증

AKS(Azure Kubernetes Service)에서 ACR(Azure Container Registry)을 사용할 때는 인증 메커니즘을 설정해야 합니다. 이 문서에서는 이러한 두 가지 Azure 서비스 간 인증을 위해 권장되는 구성에 대해 설명합니다.

이 문서에서는 AKS 클러스터를 이미 만든 상태로 `kubectl` 명령줄 클라이언트로 클러스터에 액세스할 수 있다고 가정합니다. 

## <a name="grant-aks-access-to-acr"></a>AKS에 ACR에 대한 액세스 권한 부여

AKS 클러스터를 만들면 Azure도 다른 Azure 리소스와의 클러스터 운용성을 지원하기 위해 서비스 주체를 만듭니다. 이 자동으로 생성된 서비스 주체는 ACR 레지스트리에서 인증에 사용할 수 있습니다. 이를 위해서는 클러스터의 서비스 주체에 컨테이너 레지스트리에 대한 액세스 권한을 부여하는 Azure AD [역할 할당](../role-based-access-control/overview.md#role-assignments)을 만들어야 합니다.

다음 스크립트를 사용하여 AKS에서 생성된 서비스 주체에 Azure 컨테이너 레지스트리에 대한 끌어오기 권한을 부여합니다. 스크립트를 실행하기 전에 사용자 환경에 맞게 `AKS_*` 및 `ACR_*` 변수를 수정합니다.

```bash
#!/bin/bash

AKS_RESOURCE_GROUP=myAKSResourceGroup
AKS_CLUSTER_NAME=myAKSCluster
ACR_RESOURCE_GROUP=myACRResourceGroup
ACR_NAME=myACRRegistry

# Get the id of the service principal configured for AKS
CLIENT_ID=$(az aks show --resource-group $AKS_RESOURCE_GROUP --name $AKS_CLUSTER_NAME --query "servicePrincipalProfile.clientId" --output tsv)

# Get the ACR registry resource id
ACR_ID=$(az acr show --name $ACR_NAME --resource-group $ACR_RESOURCE_GROUP --query "id" --output tsv)

# Create role assignment
az role assignment create --assignee $CLIENT_ID --role acrpull --scope $ACR_ID
```

## <a name="access-with-kubernetes-secret"></a>Kubernetes 비밀을 사용하여 액세스

일부 경우에 자동 생성된 AKS 서비스 주체에 필요한 역할을 할당하여 ACR에 대한 액세스 권한을 부여하지 못할 수 있습니다. 예를 들어, 조직의 보안 모델로 인해 AKS에서 생성된 서비스 주체에 역할을 할당하기 위한 충분한 권한이 Azure Active Directory 테넌트에 없을 수 있습니다. 서비스 주체에 역할을 할당하려면 Azure AD 계정에 Azure AD 테넌트에 대한 쓰기 권한이 있어야 합니다. 권한이 없는 경우 새 서비스 주체를 만든 다음, Kubernetes 이미지 끌어오기 비밀을 사용하여 컨테이너 레지스트리에 대한 액세스 권한을 부여할 수 있습니다.

다음 스크립트를 사용하여 새 서비스 주체를 만듭니다(Kubernetes 이미지 끌어오기 비밀로 해당 자격 증명 사용). 스크립트를 실행하기 전에 사용자 환경에 맞게 `ACR_NAME` 변수를 수정합니다.

```bash
#!/bin/bash

ACR_NAME=myacrinstance
SERVICE_PRINCIPAL_NAME=acr-service-principal

# Populate the ACR login server and resource id.
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --query loginServer --output tsv)
ACR_REGISTRY_ID=$(az acr show --name $ACR_NAME --query id --output tsv)

# Create acrpull role assignment with a scope of the ACR resource.
SP_PASSWD=$(az ad sp create-for-rbac --name http://$SERVICE_PRINCIPAL_NAME --role acrpull --scopes $ACR_REGISTRY_ID --query password --output tsv)

# Get the service principal client id.
CLIENT_ID=$(az ad sp show --id http://$SERVICE_PRINCIPAL_NAME --query appId --output tsv)

# Output used when creating Kubernetes secret.
echo "Service principal ID: $CLIENT_ID"
echo "Service principal password: $SP_PASSWD"
```

이제 AKS 클러스터가 컨테이너를 실행할 때 참조하는 Kubernetes [이미지 끌어오기 비밀][image-pull-secret]에 서비스 주체의 자격 증명을 저장할 수 있습니다.

다음 **kubectl** 명령을 사용하여 Kubernetes 비밀을 만듭니다. `<acr-login-server>`를 Azure Container Registry의 정규화된 이름(“acrname.azurecr.io" 형식)으로 바꿉니다. `<service-principal-ID>` 및 `<service-principal-password>`를 이전 스크립트를 실행하여 획득한 값으로 바꿉니다. `<email-address>`를 올바른 형식의 이메일 주소로 바꿉니다.

```bash
kubectl create secret docker-registry acr-auth --docker-server <acr-login-server> --docker-username <service-principal-ID> --docker-password <service-principal-password> --docker-email <email-address>
```

이제 `imagePullSecrets` 매개 변수에서 Kubernetes 이름(이 경우 "acr-auth")을 지정하여 Pod 배포에서 Kubernetes 비밀을 사용할 수 있습니다.

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: acr-auth-example
spec:
  template:
    metadata:
      labels:
        app: acr-auth-example
    spec:
      containers:
      - name: acr-auth-example
        image: myacrregistry.azurecr.io/acr-auth-example
      imagePullSecrets:
      - name: acr-auth
```

<!-- LINKS - external -->
[kubernetes-secret]: https://kubernetes.io/docs/concepts/configuration/secret/
[image-pull-secret]: https://kubernetes.io/docs/concepts/configuration/secret/#using-imagepullsecrets
