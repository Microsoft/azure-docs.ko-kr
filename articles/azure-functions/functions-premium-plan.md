---
title: Azure Functions 프리미엄 계획
description: Azure Functions 프리미엄 계획에 대 한 세부 정보 및 구성 옵션 (VNet, 콜드 시작 안 함, 실행 기간 제한 없음)입니다.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom: references_regions
ms.openlocfilehash: 4f6e2008cad66ce7cd68016d3873ecbc18b1961c
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2020
ms.locfileid: "89145755"
---
# <a name="azure-functions-premium-plan"></a>Azure Functions 프리미엄 계획

Azure Functions 프리미엄 요금제 (탄력적 프리미엄 요금제 라고도 함)는 함수 앱에 대 한 호스팅 옵션입니다. 프리미엄 요금제는 VNet 연결, 콜드 부팅 및 프리미엄 하드웨어와 같은 기능을 제공 합니다.  여러 함수 앱을 동일한 프리미엄 계획에 배포할 수 있으며,이 계획을 통해 계산 인스턴스 크기, 기본 계획 크기 및 최대 계획 크기를 구성할 수 있습니다.  프리미엄 계획과 기타 계획 및 호스팅 유형을 비교 하는 방법에 대해서는 [함수 크기 조정 및 호스팅 옵션](functions-scale.md)을 참조 하세요.

## <a name="create-a-premium-plan"></a>프리미엄 플랜 만들기

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]

Azure CLI에서 [az functionapp plan create](/cli/azure/functionapp/plan#az-functionapp-plan-create) 를 사용 하 여 프리미엄 계획을 만들 수도 있습니다. 다음 예에서는 _탄력적 프리미엄 1_ 계층 계획을 만듭니다.

```azurecli-interactive
az functionapp plan create --resource-group <RESOURCE_GROUP> --name <PLAN_NAME> \
--location <REGION> --sku EP1
```

이 예제에서을 `<RESOURCE_GROUP>` 리소스 그룹으로 바꾸고를 `<PLAN_NAME>` 리소스 그룹에서 고유한 계획의 이름으로 바꿉니다. [지원 되 `<REGION>` ](https://azure.microsoft.com/global-infrastructure/services/?products=functions)는를 지정 합니다. Linux를 지 원하는 프리미엄 요금제를 만들려면 옵션을 포함 `--is-linux` 합니다.

계획을 만든 후에는 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 를 사용 하 여 함수 앱을 만들 수 있습니다. 포털에서 계획과 앱은 동시에 생성 됩니다. 전체 Azure CLI 스크립트의 예는 [프리미엄 계획에서 함수 앱 만들기](scripts/functions-cli-create-premium-plan.md)를 참조 하세요.

## <a name="features"></a>기능

다음 기능은 프리미엄 계획에 배포 된 함수 앱에서 사용할 수 있습니다.

### <a name="always-ready-instances"></a>항상 준비 인스턴스

현재 소비 계획에서 이벤트 및 실행이 발생 하지 않는 경우 앱이 0 개의 인스턴스로 확장 될 수 있습니다. 새 이벤트가 발생 하는 경우 새 인스턴스는 앱에서 실행 되는 앱과 함께 특수화 되어야 합니다.  특수화 된 새 인스턴스는 앱에 따라 다소 시간이 걸릴 수 있습니다.  첫 번째 호출의 이러한 추가 대기 시간은 종종 앱 콜드 시작 이라고 합니다.

프리미엄 계획에서는 지정 된 수의 인스턴스에서 앱이 항상 준비 되도록 할 수 있습니다.  항상 준비 인스턴스의 최대 수는 20 개입니다.  이벤트가 앱 트리거를 시작 하면 항상 항상 준비 인스턴스로 라우팅됩니다.  함수가 활성화 되 면 추가 인스턴스가 버퍼로 준비 됩니다.  이 버퍼는 크기 조정 중에 필요한 새 인스턴스에 대 한 콜드 시작을 방지 합니다.  이러한 버퍼링 된 인스턴스를 [사전 준비 인스턴스](#pre-warmed-instances)라고 합니다.  항상 준비 인스턴스와 미리 준비 된 버퍼의 조합을 사용 하 여 앱에서 콜드 시작을 효과적으로 제거할 수 있습니다.

> [!NOTE]
> 모든 프리미엄 요금제에는 항상 하나 이상의 활성 및 청구 된 인스턴스가 있습니다.

**함수 앱**를 선택 하 고 **플랫폼 기능** 탭으로 이동한 다음 **Scale Out** 옵션을 선택 하 여 Azure Portal에서 항상 준비 인스턴스 수를 구성할 수 있습니다. 함수 앱 편집 창에서 항상 준비 인스턴스는 해당 앱에만 적용 됩니다.

![탄력적 크기 조정 설정](./media/functions-premium-plan/scale-out.png)

Azure CLI를 사용 하 여 앱에 대 한 항상 준비 인스턴스를 구성할 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites 
```

#### <a name="pre-warmed-instances"></a>사전 준비 인스턴스

준비 인스턴스는 크기 조정 및 활성화 이벤트 동안 버퍼로 준비 인스턴스 수입니다.  준비 인스턴스는 최대 확장 한도에 도달할 때까지 계속 버퍼링 됩니다.  기본 사전 준비 인스턴스 수는 1이 고, 대부분의 시나리오에서는 1로 유지 되어야 합니다.  앱에 긴 준비 (사용자 지정 컨테이너 이미지 등)가 있는 경우이 버퍼를 늘릴 수 있습니다.  준비 인스턴스는 모든 활성 인스턴스가 충분히 활용 된 후에만 활성화 됩니다.

이 예에서는 항상 준비 인스턴스와 사전 준비 인스턴스를 함께 사용 하는 방법에 대 한 예를 살펴보겠습니다.  프리미엄 함수 앱에는 5 개의 항상 준비 인스턴스를 구성 하 고 prewarmed 인스턴스 하나를 기본값으로 사용할 수가 있습니다.  앱이 유휴 상태이 고 이벤트가 트리거되지 않을 때 앱이 5 개의 인스턴스에서 프로 비전 되 고 실행 됩니다.  

첫 번째 트리거가 제공 되는 즉시 5 개의 항상 준비 된 인스턴스가 활성화 되 고 추가 사전 준비 인스턴스가 할당 됩니다.  이제 응용 프로그램은 6 개의 프로 비전 된 인스턴스로 실행 됩니다. 5 개의 현재 활성 상태인 항상 준비 된 인스턴스와 여섯 번째 사전 준비 및 비활성 버퍼가 있습니다.  실행 비율이 계속 증가 하는 경우 5 개의 활성 인스턴스가 결국 활용 됩니다.  플랫폼에서 5 개 이상의 인스턴스를 확장 하기로 결정 하는 경우에는 사전 준비 인스턴스로 확장 됩니다.  이러한 상황이 발생 하면 6 개의 활성 인스턴스가 되며 일곱 번째 인스턴스가 즉시 프로 비전 되 고 준비 버퍼를 채웁니다.  이 크기 조정 및 미리 준비는 앱의 최대 인스턴스 수에 도달할 때까지 계속 됩니다.  인스턴스를 준비 하거나 최대값을 초과 하 여 활성화할 수 없습니다.

Azure CLI를 사용 하 여 앱에 대 한 사전 준비 인스턴스 수를 수정할 수 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites 
```

#### <a name="maximum-instances-for-an-app"></a>앱에 대 한 최대 인스턴스

[계획 최대 인스턴스 수](#plan-and-sku-settings)외에 앱 별 최대값을 구성할 수 있습니다.  앱 [크기 제한을](./functions-scale.md#limit-scale-out)사용 하 여 최대 앱을 구성할 수 있습니다.

### <a name="private-network-connectivity"></a>개인 네트워크 연결

프리미엄 계획에 배포 Azure Functions는 [웹 앱에 대 한 새로운 VNet 통합](../app-service/web-sites-integrate-with-vnet.md)을 활용 합니다.  구성 된 앱은 VNet 내의 리소스와 통신 하거나 서비스 끝점을 통해 보안을 설정할 수 있습니다.  IP 제한은 앱에서 들어오는 트래픽을 제한 하는 데에도 사용할 수 있습니다.

프리미엄 계획의 함수 앱에 서브넷을 할당 하는 경우 각 잠재적 인스턴스에 대해 충분 한 IP 주소가 있는 서브넷이 필요 합니다. 사용 가능한 주소가 100 이상인 IP 블록이 필요 합니다.

자세한 내용은 [함수 앱을 VNet과 통합](functions-create-vnet.md)을 참조 하세요.

### <a name="rapid-elastic-scale"></a>신속한 탄력적 확장

소비 계획과 동일한 빠른 크기 조정 논리를 사용 하 여 앱에 대 한 추가 계산 인스턴스가 자동으로 추가 됩니다. 동일한 App Service 계획의 앱은 개별 앱의 요구에 따라 서로 독립적으로 확장 됩니다. 그러나 동일한 App Service 계획의 함수 앱은 가능한 경우 비용을 줄이기 위해 VM 리소스를 공유 합니다. VM에 연결 된 앱 수는 각 앱의 공간 및 VM 크기에 따라 달라 집니다.

크기 조정의 작동 방식에 대 한 자세한 내용은 [함수 크기 조정 및 호스팅](./functions-scale.md#how-the-consumption-and-premium-plans-work)을 참조 하세요.

### <a name="longer-run-duration"></a>더 긴 실행 지속 시간

소비 계획의 Azure Functions은 단일 실행에 대해 10 분으로 제한 됩니다.  프리미엄 계획에서 실행 기간은 기본적으로 30 분으로 설정 되어 런어웨이 실행을 방지 합니다. 그러나 프리미엄 계획 앱에 대 한 제한 시간 (보장 60 분)을 설정 하 여 [구성에](./functions-host-json.md#functiontimeout) 대 한 host.js를 수정할 수 있습니다.

## <a name="plan-and-sku-settings"></a>요금제 및 SKU 설정

계획을 만들 때 최소 인스턴스 수 (또는 계획 크기)와 최대 버스트 제한의 두 가지 계획 크기 설정이 있습니다.

앱이 항상 준비 인스턴스를 초과 하는 인스턴스를 필요로 하는 경우 인스턴스 수가 최대 버스트 제한에 도달할 때까지 계속 규모를 확장할 수 있습니다.  실행 되 고 있는 동안 계획 크기를 초과 하는 인스턴스에 대해서만 요금이 청구 됩니다.  앱을 정의 된 최대 제한까지 확장 하는 것이 가장 좋습니다.

계획의 **Scale Out** 옵션 또는 해당 계획에 배포 된 함수 앱 ( **플랫폼 기능**아래)을 선택 하 여 Azure Portal의 계획 크기 및 최대값을 구성할 수 있습니다.

Azure CLI에서 최대 버스트 제한을 늘릴 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

모든 계획에 대 한 최소값은 하나 이상의 인스턴스가 됩니다.  실제 최소 인스턴스 수는 계획의 앱에서 요청 하는 항상 준비 된 인스턴스를 기반으로 자동으로 구성 됩니다.  예를 들어 앱 A가 항상 준비 된 인스턴스 5 개를 요청 하 고, 앱 B가 동일한 계획에 두 개의 항상 준비 인스턴스를 요청 하는 경우 최소 계획 크기는 5로 계산 됩니다.  앱 A는 모든 5에서 실행 되며, 앱 B는 2 에서만 실행 됩니다.

> [!IMPORTANT]
> 함수 실행 여부에 관계 없이 최소 인스턴스 수에 할당 된 각 인스턴스에 대해 요금이 청구 됩니다.

대부분의 경우이 autocalculated 최소값으로 충분 합니다.  그러나 최소값 이상으로 크기를 조정 하는 것은 최상의 노력으로 발생 합니다.  그러나 추가 인스턴스를 사용할 수 없는 경우에는 특정 시간 확장에서 지연이 발생할 수 있습니다.  최소 autocalculated 이상으로 설정 하 여 인스턴스를 축소 하기 전에 예약 합니다.

계획에 대해 계산 된 최소값을 늘리려면 Azure CLI를 사용 하 여 수행할 수 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set sku.capacity=<desired_min_instances> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>사용 가능한 인스턴스 Sku

계획을 만들거나 크기를 조정할 때 세 가지 인스턴스 크기 중에서 선택할 수 있습니다.  총 코어 수와 초당 사용 된 메모리에 대 한 요금이 청구 됩니다.  필요에 따라 앱이 여러 인스턴스로 자동 확장 될 수 있습니다.  

|SKU|코어|메모리|Storage|
|--|--|--|--|
|EP1|1|3.5 g b|250GB|
|E P 2|2|7GB|250GB|
|노출할|4|14GB|250GB|

### <a name="memory-utilization-considerations"></a>메모리 사용률 고려 사항
메모리를 더 많이 사용 하는 컴퓨터에서 실행 하는 경우 항상 함수 앱에서 사용 가능한 모든 메모리를 사용 한다는 의미는 아닙니다.

예를 들어 JavaScript 함수 앱은 Node.js의 기본 메모리 제한에 의해 제한 됩니다. 이 고정 메모리 제한을 늘리려면 값을 사용 하 여 앱 설정을 추가 합니다 `languageWorkers:node:arguments` `--max-old-space-size=<max memory in MB>` .

## <a name="region-max-scale-out"></a>영역 최대 Scale Out

다음은 각 지역 및 OS 구성에서 단일 계획에 대해 현재 지원 되는 최대 확장 값입니다. 증가를 요청 하려면 지원 티켓을 여세요.

여기에서 함수의 전체 지역 가용성을 참조 하세요. [Azure.com](https://azure.microsoft.com/global-infrastructure/services/?products=functions)

|지역| Windows | Linux |
|--| -- | -- |
|오스트레일리아 중부| 20 | 사용할 수 없음 |
|오스트레일리아 중부 2| 20 | 사용할 수 없음 |
|오스트레일리아 동부| 100 | 20 |
|오스트레일리아 남동부 | 100 | 20 |
|브라질 남부| 60 | 20 |
|캐나다 중부| 100 | 20 |
|미국 중부| 100 | 20 |
|동아시아| 100 | 20 |
|미국 동부 | 100 | 20 |
|미국 동부 2| 100 | 20 |
|프랑스 중부| 100 | 20 |
|독일 중서부| 100 | 사용할 수 없음 |
|일본 동부| 100 | 20 |
|일본 서부| 100 | 20 |
|한국 중부| 100 | 20 |
|미국 중북부| 100 | 20 |
|북유럽| 100 | 20 |
|노르웨이 동부| 20 | 20 |
|미국 중남부| 100 | 20 |
|인도 남부 | 100 | 사용할 수 없음 |
|동남 아시아| 100 | 20 |
|영국 남부| 100 | 20 |
|영국 서부| 100 | 20 |
|서유럽| 100 | 20 |
|인도 서부| 100 | 20 |
|미국 중서부| 20 | 20 |
|미국 서부| 100 | 20 |
|미국 서부 2| 100 | 20 |

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 크기 조정 및 호스팅 옵션 이해](functions-scale.md)
