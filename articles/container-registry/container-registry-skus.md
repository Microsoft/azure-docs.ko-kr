---
title: 서비스 계층 및 Sku
description: Azure Container Registry의 기본, 표준 및 프리미엄 서비스 계층 (Sku)의 기능 및 제한 사항에 대해 알아봅니다.
ms.topic: article
ms.date: 11/05/2019
ms.openlocfilehash: 1ebe5339b7523a4463dee45b126244d7ec5b2e4b
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456278"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

ACR(Azure Container Registry)은 다중 서비스 계층(즉, SKU)에서 사용 가능합니다. 이러한 SKU는 예측 가능한 가격 책정과 Azure에서 프라이빗 Docker 레지스트리의 용량 및 사용량 패턴에 맞추기 위한 여러 옵션을 제공합니다.

| SKU | 설명 |
| --- | ----------- |
| **Basic** | 개발자가 Azure Container Registry에 대해 배울 수 있는 비용 최적화된 진입점입니다. 기본 레지스트리는 Standard 및 Premium과 동일한 프로그래밍 기능을 제공 합니다 (예: Azure Active Directory [인증 통합](container-registry-authentication.md#individual-login-with-azure-ad), [이미지 삭제][container-registry-delete]및 [웹 후크][container-registry-webhook]). 그렇지만 포함된 스토리지 및 이미지 처리량은 더 낮은 사용량 시나리오에 가장 적합합니다. |
| **Standard** | Standard 레지스트리는 Basic과 동일한 성능을 제공하되, 포함된 스토리지 제한과 이미지 처리량은 더 높습니다. Standard 레지스트리는 대부분의 프로덕션 시나리오 요구를 충족합니다. |
| **Premium** | Premium 레지스트리는 포함된 스토리지 및 동시 작업 수가 가장 높으며 고용량 시나리오를 지원합니다. Premium은 더 높은 이미지 처리량 외에도, 여러 지역에서 단일 레지스트리를 관리 하기 위한 [지역 복제][container-registry-geo-replication] , 이미지 태그 서명에 대한 [콘텐츠 신뢰](container-registry-content-trust.md) , [방화벽 및 가상 네트워크 (미리 보기)](container-registry-vnet.md) 와 같은 기능을 추가 합니다. 레지스트리에 대한 액세스를 제한 합니다. |

Basic, Standard 및 Premium Sku는 모두 동일한 프로그래밍 기능을 제공 합니다. 또한 Azure에서 완전히 관리 하는 [이미지 저장소][container-registry-storage] 를 활용 합니다. 더 높은 수준의 SKU를 선택하면 향상된 성능과 규모를 제공합니다. 여러 서비스 계층을 사용하여 Basic로 시작한 다음, 레지스트리 사용량 증가에 따라 Standard 및 Premium으로 변환할 수 있습니다.

## <a name="sku-features-and-limits"></a>SKU 기능 및 제한

다음 표에는 Basic, Standard 및 Premium 서비스 계층의 기능 및 한계에 대해 자세히 나와 있습니다.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU 변경

Azure CLI를 사용하거나 Azure Portal에서 레지스트리의 SKU를 변경할 수 있습니다. 전환 하는 SKU에 필요한 최대 저장소 용량이 있으면 Sku 간에 자유롭게 이동할 수 있습니다. 

### <a name="azure-cli"></a>Azure CLI

Azure CLI에서 Sku 간을 이동 하려면 [az acr update][az-acr-update] 명령을 사용 합니다. 예를 들어 Premium으로 전환하려면:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure 포털

Azure Portal의 컨테이너 레지스트리 **개요**에서 **업데이트**를 선택한 다음, SKU 드롭다운에서 새로운 **SKU**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 SKU 업데이트][update-registry-sku]

## <a name="pricing"></a>가격

각 Azure Container Registry SKU에 대한 가격 책정 정보는 [컨테이너 레지스트리 가격 책정][container-registry-pricing]을 참조하세요.

데이터 전송 가격 책정에 대한 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요. 

## <a name="next-steps"></a>다음 단계

**Azure Container Registry 로드맵**

GitHub에서 [ACR 로드맵][acr-roadmap]에 방문하여 예정된 서비스 기능에 대한 정보를 알아 보세요.

**Azure Container Registry UserVoice**

[ACR UserVoice][container-registry-uservoice]에서 새로운 기능 제안 사항을 제출하고 투표합니다.

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
