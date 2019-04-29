---
title: 서비스 주체로 Azure Container Registry 인증
description: Azure Active Directory 서비스 주체를 사용하여 개인 컨테이너 레지스트리에 있는 이미지에 대한 액세스 권한을 제공합니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 12/13/2018
ms.author: danlep
ms.openlocfilehash: 5d8904b5906adbdab68989b3a5cf9c3975c23533
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61347084"
---
# <a name="azure-container-registry-authentication-with-service-principals"></a>서비스 주체로 Azure Container Registry 인증

Azure AD(Azure Active Directory) 서비스 주체를 사용하여 컨테이너 레지스트리에 대해 컨테이너 이미지 `docker push` 및 `pull` 액세스 권한을 제공할 수 있습니다. 서비스 주체를 사용하면 "헤드리스" 서비스 및 애플리케이션에 대한 액세스를 제공할 수 있습니다.

## <a name="what-is-a-service-principal"></a>서비스 주체란?

Azure AD *서비스 주체*는 구독 내 Azure 리소스에 대한 액세스를 제공합니다. 서비스 주체는 서비스에 대한 사용자 ID라고 생각할 수 있습니다. 여기서 "서비스"는 리소스에 액세스 권한이 필요한 애플리케이션, 서비스 또는 플랫폼입니다. 지정한 리소스에 대해서만 액세스 권한이 있는 서비스 주체를 구성할 수 있습니다. 그런 다음, 서비스 주체의 자격 증명을 사용하여 해당 리소스에 액세스하도록 애플리케이션이나 서비스를 구성합니다.

Azure Container Registry 컨텍스트에서, Azure의 개인 레지스트리에 대한 풀, 푸시 및 풀 또는 기타 권한이 있는 Azure AD 서비스 주체를 만들 수 있습니다. 전체 목록에 대해서는 [Azure Container Registry 역할 및 권한](container-registry-roles.md)을 참조하세요.

## <a name="why-use-a-service-principal"></a>서비스 주체를 사용하는 이유

Azure AD 서비스 주체를 사용하면 개인 컨테이너 레지스트리에 대해 범위가 지정된 액세스를 제공할 수 있습니다. 애플리케이션이나 서비스마다 레지스트리에 대한 맞춤 액세스 권한이 있는 여러 서비스 주체를 만들 수 있습니다. 또한 서비스와 애플리케이션 사이에 자격 증명을 공유하지 않아도 되기 때문에, 선택한 서비스 주체(및 애플리케이션)에 대해서만 자격 증명을 순환하거나 액세스 권한을 철회할 수 있습니다.

예를 들어, 웹 애플리케이션은 이미지 `pull` 액세스만 제공하는 서비스 주체를 사용할 수 있는 반면에 빌드 시스템은 `push` 및 `pull` 액세스를 모두 제공하는 서비스 주체를 사용할 수 있습니다. 애플리케이션 개발 팀 인력에 변화가 있으면, 빌드 시스템에 영향을 주지 않으면서 서비스 주체 자격 증명을 순환할 수 있습니다.

## <a name="when-to-use-a-service-principal"></a>서비스 주체를 사용하는 경우

**헤드리스 시나리오**에서 레지스트리 액세스를 제공하려면 서비스 주체를 사용해야 합니다. 즉, 자동 또는 무인 방식으로 컨테이너 이미지를 푸시하거나 풀해야 하는 모든 애플리케이션, 서비스 또는 스크립트입니다.

레지스트리에 개별 액세스하는 경우(예: 컨테이너 이미지를 개발 워크스테이션에 수동으로 풀하는 경우)에는, 레지스트리 액세스를 위해 자신의 [Azure AD ID](container-registry-authentication.md#individual-login-with-azure-ad)(예: [az acr login][az-acr-login])를 사용해야 합니다.

[!INCLUDE [container-registry-service-principal](../../includes/container-registry-service-principal.md)]

## <a name="sample-scripts"></a>샘플 스크립트

GitHub에서 Azure CLI에 대한 이전 샘플 스크립트 및 Azure PowerShell에 대한 버전을 찾을 수 있습니다.

* [Azure CLI][acr-scripts-cli]
* [Azure PowerShell][acr-scripts-psh]

## <a name="next-steps"></a>다음 단계

컨테이너 레지스트리에 대한 액세스 권한을 부여한 서비스 주체가 있는 경우, 헤드리스 레지스트리 상호 작용을 위해 애플리케이션 및 서비스에서 해당 자격 증명을 사용할 수 있습니다. Azure 컨테이너 레지스트리로 인증할 수 있는 모든 Azure 서비스의 서비스 주체 자격 증명을 사용할 수 있습니다. 다음은 이러한 템플릿의 예입니다.

* [AKS(Azure Kubernetes Service)의 Azure Container Registry를 사용하여 인증](container-registry-auth-aks.md)
* [Azure Container Instances의 Azure Container Registry를 사용하여 인증(ACI)](container-registry-auth-aci.md)

<!-- LINKS - External -->
[acr-scripts-cli]: https://github.com/Azure/azure-docs-cli-python-samples/tree/master/container-registry
[acr-scripts-psh]: https://github.com/Azure/azure-docs-powershell-samples/tree/master/container-registry

<!-- LINKS - Internal -->
[az-acr-login]: /cli/azure/acr#az-acr-login
