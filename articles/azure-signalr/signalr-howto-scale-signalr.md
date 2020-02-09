---
title: Azure SignalR 서비스 인스턴스 크기 조정
description: Azure Portal 또는 Azure CLI를 통해 Azure SignalR 서비스 인스턴스의 크기를 조정 하 여 용량을 추가 하거나 감소 하는 방법에 대해 알아봅니다.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: zhshang
ms.openlocfilehash: c8d74342e624b837c7ee803a2bcdcc12a3fb814b
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659290"
---
# <a name="how-to-scale-an-azure-signalr-service-instance"></a>Azure SignalR 서비스 인스턴스의 크기를 조정 하는 방법
이 문서에서는 Azure SignalR 서비스의 인스턴스 크기를 조정 하는 방법을 보여 줍니다. 크기 조정, 강화 및 규모 확장에 대한 두 가지 시나리오가 있습니다.

* 강화: 더 많은 단위, 연결, 메시지 등을 [가져옵니다.](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling) 가격 책정 계층을 무료에서 표준으로 변경 하 여 확장 합니다.
* [규모 확장](https://en.wikipedia.org/wiki/Scalability#Horizontal_and_vertical_scaling): SignalR 단위 수를 늘립니다. 최대 100 단위까지 확장할 수 있습니다.

크기 조정 설정을 적용 하는 데 몇 분 정도 걸립니다. 드문 경우 지만 적용 하는 데 약 30 분이 걸릴 수 있습니다. 코드를 변경 하거나 서버 응용 프로그램을 다시 배포할 필요는 없습니다.

개별 SignalR 서비스의 가격 책정 및 용량에 대한 자세한 내용은 [Azure SignalR Service 가격 책정 정보](https://azure.microsoft.com/pricing/details/signalr-service/)를 참조 하세요.  

> [!NOTE]
> SignalR 서비스를 **무료** 계층에서 **표준** 계층으로 변경 하거나 그 반대로 변경 하면 공용 서비스 IP가 변경 되 고, 전체 인터넷을 통해 DNS 서버에 변경 내용을 전파 하는 데 일반적으로 30-60 분이 걸립니다. DNS를 업데이트 하기 전에 서비스에 연결할 수 없습니다. 일반적으로 가격 책정 계층을 너무 자주 변경 하지 않는 것이 좋습니다.


## <a name="scale-on-azure-portal"></a>Azure Portal 크기 조정

1. 브라우저에서 [Azure Portal](https://portal.azure.com)을 엽니다.

2. SignalR Service 페이지의 왼쪽 메뉴에서 **Scale**을 선택 합니다.
   
3. 가격 책정 계층을 선택한 다음 **선택**을 클릭 합니다. **표준** 계층의 단위 수를 설정 합니다.
   
    ![포털에서 크기 조정](./media/signalr-howto-scale/signalr-howto-scale.png)

4. **저장**을 클릭합니다.

## <a name="scale-using-azure-cli"></a>Azure CLI를 사용한 크기 조정

이 스크립트는 **무료** 계층과 새 리소스 그룹의 새 SignalR Service 리소스를 만들고 **표준** 계층까지 확장 합니다. 

```azurecli-interactive
#!/bin/bash

# Generate a unique suffix for the service name
let randomNum=$RANDOM*$RANDOM

# Generate a unique service and group name with the suffix
SignalRName=SignalRTestSvc$randomNum
#resource name must be lowercase
mySignalRSvcName=${SignalRName,,}
myResourceGroupName=$SignalRName"Group"

# Create resource group 
az group create --name $myResourceGroupName --location eastus

# Create the Azure SignalR Service resource
az signalr create \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Free_F1 \
  --service-mode Default

# Scale up to Standard Tier, and scale out to 50 units
az signalr update \
  --name $mySignalRSvcName \
  --resource-group $myResourceGroupName \
  --sku Standard_S1 \
  --unit-count 50
```

새 리소스 그룹에 대해 생성된 실제 이름을 적어 둡니다. 모든 그룹 리소스를 삭제하려는 경우 해당 리소스 그룹 이름을 사용합니다.

[!INCLUDE [cli-script-clean-up](../../includes/cli-script-clean-up.md)]

## <a name="compare-pricing-tiers"></a>가격 책정 계층 비교

각 가격 책정 계층에 대한 포함 된 메시지 및 연결과 같은 자세한 정보는 [SignalR Service 가격 정보](https://azure.microsoft.com/pricing/details/signalr-service/)를 참조 하세요.

각 계층의 서비스 한도, 할당량 및 제약 조건에 대한 테이블은 [SignalR service 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-signalr-service-limits)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 단일 SignalR 서비스 인스턴스 크기를 조정 하는 방법을 배웠습니다.

여러 엔드포인트은 크기 조정, 분할 및 지역 간 시나리오에 대해서도 지원 됩니다.

> [!div class="nextstepaction"]
> [여러 인스턴스를 사용 하 여 SignalR 서비스 크기 조정](./signalr-howto-scale-multi-instances.md)
