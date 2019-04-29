---
title: Azure Container Registry SKU
description: Azure Container Registry에서 사용할 수 있는 서로 다른 서비스 계층을 비교합니다.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 03/26/2019
ms.author: danlep
ms.openlocfilehash: 7fdc1417ac524b422a12a087cf1661040efb9f8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60582824"
---
# <a name="azure-container-registry-skus"></a>Azure Container Registry SKU

ACR(Azure Container Registry)은 다중 서비스 계층(즉, SKU)에서 사용 가능합니다. 이러한 SKU는 예측 가능한 가격 책정과 Azure에서 개인 Docker 레지스트리의 용량 및 사용량 패턴에 맞추기 위한 여러 옵션을 제공합니다.

| SKU | 관리 | 설명 |
| --- | :-------: | ----------- |
| **Basic** | 예 | 개발자가 Azure Container Registry에 대해 배울 수 있는 비용 최적화된 진입점입니다. 기본 레지스트리는 Standard 및 Premium으로 동일한 프로그래밍 기능 (예: Azure Active Directory [인증 통합](container-registry-authentication.md#individual-login-with-azure-ad)를 [삭제 이미지][container-registry-delete], 및 [웹 후크][container-registry-webhook]). 그렇지만 포함된 저장소 및 이미지 처리량은 더 낮은 사용량 시나리오에 가장 적합합니다. |
| **Standard** | 예 | Standard 레지스트리는 Basic과 동일한 성능을 제공하되, 포함된 저장소 제한과 이미지 처리량은 더 높습니다. Standard 레지스트리는 대부분의 프로덕션 시나리오 요구를 충족합니다. |
| **Premium** | 예 | Premium 레지스트리는 포함된 저장소 및 동시 작업 수가 가장 높으며 고용량 시나리오를 지원합니다. 더 높은 이미지 처리량 뿐만 아니라 프리미엄 추가 기능을 포함 하 여 [함깨 geo-replication] [ container-registry-geo-replication] 여러 지역에서 단일 레지스트리를 관리 하는 것에 대 한 [신뢰 (미리 보기)콘텐츠](container-registry-content-trust.md) 서명에 이미지 태그, 및 [방화벽 및 virtual networks (미리 보기)](container-registry-vnet.md) 레지스트리에 대 한 액세스를 제한 하려면. |
|  클래식 (*2019 년 4 월 후 사용할 수 없는*) | 아닙니다. | SKU는 Azure에서 Azure Container Registry 서비스의 초기 릴리스를 지원했습니다. Classic 레지스트리는 사용자 구독에서 Azure가 만드는 저장소 계정에서 지원하며 ACR가 처리량 증가 및 지역에서 복제 등의 더 높은 수준의 기능을 제공할 수 있는 기능을 제한합니다. |

> [!IMPORTANT]
> Classic 레지스트리 SKU 되 **더 이상 사용 되지**, 한 후 사용할 수 없게 됩니다 및 **2019 년 4 월**합니다. Basic, Standard 또는 Premium을 사용 하 여 모든 새로운 레지스트리에 대 한 것이 좋습니다. 2019 년 4 월 전에 모든 기존 Classic 레지스트리 업그레이드 해야 합니다. 업그레이드 정보를 참조 하세요 [Classic 레지스트리 업그레이드][container-registry-upgrade]합니다.

Basic, Standard 및 Premium Sku (통틀어 호출 *레지스트리를 관리 되는*) 모두 동일한 프로그래밍 성능을 제공 합니다. 이러한 모든에서 활용할 수도 [이미지 저장소] [ container-registry-storage] Azure에서 완전히 관리 합니다. 더 높은 수준의 SKU를 선택하면 향상된 성능과 규모를 제공합니다. 여러 서비스 계층을 사용하여 Basic로 시작한 다음, 레지스트리 사용량 증가에 따라 Standard 및 Premium으로 변환할 수 있습니다.

## <a name="sku-feature-matrix"></a>SKU 기능 행렬

다음 표에는 Basic, Standard 및 Premium 서비스 계층의 기능 및 한계에 대해 자세히 나와 있습니다.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU 변경

Azure CLI를 사용하거나 Azure Portal에서 레지스트리의 SKU를 변경할 수 있습니다. 전환하는 SKU에 필요한 최대 저장소 용량이 있는 한 관리되는 SKU 간에 자유롭게 이동할 수 있습니다. 클래식에서 관리 되는 Sku 중 하나로 전환 하면 이동할 수 없습니다 다시 classic 변환은 단방향 변환입니다.

### <a name="azure-cli"></a>Azure CLI

Azure CLI에서 SKU 간에 이동하려면 [az acr update][az-acr-update] 명령을 사용합니다. 예를 들어 Premium으로 전환하려면:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure portal

Azure Portal의 컨테이너 레지스트리 **개요**에서 **업데이트**를 선택한 다음, SKU 드롭다운에서 새로운 **SKU**를 선택합니다.

![Azure Portal에서 컨테이너 레지스트리 SKU 업데이트][update-registry-sku]

Classic 레지스트리가 있는 경우 Azure Portal 내에서 관리되는 SKU를 선택할 수 없습니다. 먼저 연결 되는 대신 [업그레이드] [ container-registry-upgrade] 관리 되는 레지스트리를 합니다.

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
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md
