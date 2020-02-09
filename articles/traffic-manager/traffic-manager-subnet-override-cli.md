---
title: Azure CLI를 사용 하 여 Azure Traffic Manager 서브넷 재정의 | Microsoft Docs
description: 이 문서는 Traffic Manager 서브넷 재정의를 사용 하 여 Traffic Manager 프로필의 라우팅 메서드를 재정의 하는 방법을 이해 하 고, 미리 정의 된 IP 범위에서 엔드포인트 매핑을 통해 최종 사용자 IP 주소를 기반으로 엔드포인트에 트래픽을 전달 하는 방법을 이해 하는 데 도움이 됩니다.
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.topic: article
ms.service: traffic-manager
ms.date: 09/18/2019
ms.author: rohink
ms.openlocfilehash: 818b692884bd9d31efd08663a582ebcfec2032e9
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938465"
---
# <a name="traffic-manager-subnet-override-using-azure-cli"></a>Azure CLI를 사용 하 여 서브넷 재정의 Traffic Manager

Traffic Manager 서브넷 재정의를 사용 하 여 프로필의 라우팅 방법을 변경할 수 있습니다.  재정의를 추가 하면 최종 사용자의 IP 주소를 기반으로 하는 트래픽을 엔드포인트 매핑에 미리 정의 된 IP 범위로 보냅니다. 

## <a name="how-subnet-override-works"></a>서브넷 재정의 작동 방법

트래픽 관리자 프로필에 서브넷 재정의를 추가 하면 Traffic Manager는 먼저 최종 사용자의 IP 주소에 대 한 서브넷 재정의가 있는지 확인 합니다. 사용자의 DNS 쿼리가 있는 경우 해당 엔드포인트으로 이동 됩니다.  매핑이 없는 경우 Traffic Manager는 프로필의 원래 라우팅 메서드로 대체 됩니다. 

IP 주소 범위는 CIDR 범위 (예: 1.2.3.0/24) 또는 주소 범위 (예: 1.2.3.4-5.6.7.8)로 지정할 수 있습니다. 각 엔드포인트과 연결 된 IP 범위는 해당 엔드포인트에 대해 고유 해야 합니다. 서로 다른 엔드포인트 간에 IP 범위를 겹치면 Traffic Manager에 의해 프로필이 거부 됩니다.

서브넷 재정의를 지 원하는 라우팅 프로필에는 다음 두 가지 유형이 있습니다.

* **지리적** -Traffic Manager DNS 쿼리의 IP 주소에 대 한 서브넷 재정의를 찾으면 엔드포인트의 상태가 무엇이 든 엔드포인트으로 쿼리를 라우팅합니다.
* **성능** -Traffic Manager DNS 쿼리의 IP 주소에 대 한 서브넷 재정의를 찾으면 정상 상태인 경우에만 트래픽을 엔드포인트으로 라우팅합니다.  서브넷 재정의 엔드포인트이 정상이 아닌 경우 Traffic Manager는 성능 라우팅 추론으로 대체 됩니다.

## <a name="create-a-traffic-manager-subnet-override"></a>Traffic Manager 서브넷 재정의 만들기

Traffic Manager 서브넷 재정의를 만들려면 Azure CLI를 사용 하 여 재정의를 위한 서브넷을 Traffic Manager 엔드포인트에 추가할 수 있습니다.

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.28 이상을 실행해야 합니다. 버전을 확인하려면 `az --version`을 실행합니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="update-the-traffic-manager-endpoint-with-subnet-override"></a>서브넷 재정의를 사용 하 여 Traffic Manager 엔드포인트을 업데이트 합니다.
[Az network traffic manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update)를 사용 하 여 엔드포인트을 업데이트 하려면 Azure CLI를 사용 합니다.

```azurecli

### Add a range of IPs ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 1.2.3.4-5.6.7.8 \
    --type AzureEndpoints

### Add a subnet ###
az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --subnets 9.10.11.0:24 \
    --type AzureEndpoints

```

**--Remove** 옵션을 사용 하 여 [az network traffic manager endpoint update](https://docs.microsoft.com/cli/azure/network/traffic-manager/endpoint?view=azure-cli-latest#az-network-traffic-manager-endpoint-update) 를 실행 하 여 IP 주소 범위를 제거할 수 있습니다.

```azurecli

az network traffic-manager endpoint update \
    --name MyEndpoint \
    --profile-name MyTmProfile \
    --resource-group MyResourceGroup \
    --remove subnets \
    --type AzureEndpoints

```
## <a name="next-steps"></a>다음 단계
Traffic Manager [트래픽 라우팅 방법](traffic-manager-routing-methods.md)에 대해 자세히 알아봅니다.

[서브넷 트래픽 라우팅 방법](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods#subnet-traffic-routing-method) 에 대 한 자세한 정보