---
title: Azure Functions 프리미엄 계획
description: Azure Functions 프리미엄 계획에 대 한 세부 정보 및 구성 옵션 (VNet, 콜드 시작 안 함, 실행 기간 제한 없음)입니다.
author: jeffhollan
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: jehollan
ms.openlocfilehash: 81db0889294360f74cb42d388e5d875de91c1019
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212466"
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

이 예제에서는 `<RESOURCE_GROUP>`를 리소스 그룹으로 바꾸고 리소스 그룹에서 고유한 계획의 이름을 사용 하 여 `<PLAN_NAME>` 합니다. [지원 되는 `<REGION>`](#regions)를 지정 합니다. Linux를 지 원하는 프리미엄 계획을 만들려면 `--is-linux` 옵션을 포함 합니다.

계획을 만든 후에는 [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 를 사용 하 여 함수 앱을 만들 수 있습니다. 포털에서 계획과 앱은 동시에 생성 됩니다. 전체 Azure CLI 스크립트의 예는 [프리미엄 계획에서 함수 앱 만들기](scripts/functions-cli-create-premium-plan.md)를 참조 하세요.

## <a name="features"></a>기능

다음 기능은 프리미엄 계획에 배포 된 함수 앱에서 사용할 수 있습니다.

### <a name="pre-warmed-instances"></a>사전 준비 인스턴스

현재 소비 계획에서 이벤트 및 실행이 발생 하지 않는 경우 앱이 0 개의 인스턴스로 확장 될 수 있습니다. 새 이벤트가 발생 하는 경우 새 인스턴스는 앱에서 실행 되는 앱과 함께 특수화 되어야 합니다.  특수화 된 새 인스턴스는 앱에 따라 다소 시간이 걸릴 수 있습니다.  첫 번째 호출의 이러한 추가 대기 시간은 종종 앱 콜드 시작 이라고 합니다.

프리미엄 계획에서는 지정 된 수의 인스턴스에서 최소 계획 크기로 앱을 미리 준비 수 있습니다.  준비 인스턴스를 사용 하 여 높은 로드 전에 앱을 미리 확장할 수도 있습니다. 앱이 확장 될 때 먼저 사전 준비 인스턴스로 확장 됩니다. 추가 인스턴스는 다음 크기 조정 작업을 준비 하기 위해 계속 해 서 버퍼링 되어 즉시 웜 합니다. 준비 인스턴스의 버퍼를 사용 하 여 콜드 시작 대기 시간을 효과적으로 방지할 수 있습니다.  사전 준비 인스턴스는 프리미엄 계획의 기능으로, 실행 중인 인스턴스를 하나 이상 유지 하 고 해당 계획이 활성화 된 상태에서 사용 가능 하 게 유지 해야 합니다.

**함수 앱**를 선택 하 고 **플랫폼 기능** 탭으로 이동한 다음 **Scale Out** 옵션을 선택 하 여 Azure Portal에서 사전 준비 인스턴스 수를 구성할 수 있습니다. 함수 앱 편집 창에서 사전 준비 인스턴스는 해당 앱에만 적용 되지만 최소 및 최대 인스턴스는 전체 계획에 적용 됩니다.

![탄력적 크기 조정 설정](./media/functions-premium-plan/scale-out.png)

Azure CLI를 사용 하 여 앱에 대해 사전 준비 인스턴스를 구성할 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="private-network-connectivity"></a>개인 네트워크 연결

프리미엄 계획에 배포 Azure Functions는 [웹 앱에 대 한 새로운 VNet 통합](../app-service/web-sites-integrate-with-vnet.md)을 활용 합니다.  구성 된 앱은 VNet 내의 리소스와 통신 하거나 서비스 끝점을 통해 보안을 설정할 수 있습니다.  IP 제한은 앱에서 들어오는 트래픽을 제한 하는 데에도 사용할 수 있습니다.

프리미엄 계획의 함수 앱에 서브넷을 할당 하는 경우 각 잠재적 인스턴스에 대해 충분 한 IP 주소가 있는 서브넷이 필요 합니다. 사용 가능한 주소가 100 이상인 IP 블록이 필요 합니다.

자세한 내용은 [함수 앱을 VNet과 통합](functions-create-vnet.md)을 참조 하세요.

### <a name="rapid-elastic-scale"></a>신속한 탄력적 확장

소비 계획과 동일한 빠른 크기 조정 논리를 사용 하 여 앱에 대 한 추가 계산 인스턴스가 자동으로 추가 됩니다.  크기 조정의 작동 방식에 대 한 자세한 내용은 [함수 크기 조정 및 호스팅](./functions-scale.md#how-the-consumption-and-premium-plans-work)을 참조 하세요.

### <a name="longer-run-duration"></a>더 긴 실행 지속 시간

소비 계획의 Azure Functions은 단일 실행에 대해 10 분으로 제한 됩니다.  프리미엄 계획에서 실행 기간은 기본적으로 30 분으로 설정 되어 런어웨이 실행을 방지 합니다. 그러나 프리미엄 계획 앱에 대해이 60 분이 되도록 [호스트 json 구성을 수정할](./functions-host-json.md#functiontimeout) 수 있습니다.

## <a name="plan-and-sku-settings"></a>요금제 및 SKU 설정

계획을 만들 때 최소 인스턴스 수 (또는 계획 크기)와 최대 버스트 제한의 두 가지 설정을 구성 합니다.  최소 인스턴스는 예약 되어 있으며 항상 실행 중입니다.

> [!IMPORTANT]
> 함수 실행 여부에 관계 없이 최소 인스턴스 수에 할당 된 각 인스턴스에 대해 요금이 청구 됩니다.

앱이 계획 크기를 초과 하는 인스턴스를 필요로 하는 경우 인스턴스 수가 최대 버스트 제한에 도달할 때까지 계속 규모를 확장할 수 있습니다.  실행 되 고 있는 동안 계획 크기를 초과 하는 인스턴스에 대해서만 요금이 청구 됩니다.  앱을 정의 된 최대 제한까지 확장 하는 것이 가장 좋습니다. 반면 최소 계획 인스턴스는 앱에 대해 보장 됩니다.

계획의 **Scale Out** 옵션 또는 해당 계획에 배포 된 함수 앱 ( **플랫폼 기능**아래)을 선택 하 여 Azure Portal의 계획 크기 및 최대값을 구성할 수 있습니다.

Azure CLI에서 최대 버스트 제한을 늘릴 수도 있습니다.

```azurecli-interactive
az resource update -g <resource_group> -n <premium_plan_name> --set properties.maximumElasticWorkerCount=<desired_max_burst> --resource-type Microsoft.Web/serverfarms 
```

### <a name="available-instance-skus"></a>사용 가능한 인스턴스 Sku

계획을 만들거나 크기를 조정할 때 세 가지 인스턴스 크기 중에서 선택할 수 있습니다.  총 코어 수와 초당 사용 된 메모리에 대 한 요금이 청구 됩니다.  필요에 따라 앱이 여러 인스턴스로 자동 확장 될 수 있습니다.  

|SKU|코어 수|메모리|스토리지|
|--|--|--|--|
|EP1|1|3.5GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14GB|250GB|

### <a name="memory-utilization-considerations"></a>메모리 사용률 고려 사항
메모리를 더 많이 사용 하는 컴퓨터에서 실행 하는 경우 항상 함수 앱에서 사용 가능한 모든 메모리를 사용 한다는 의미는 아닙니다.

예를 들어 JavaScript 함수 앱은 node.js의 기본 메모리 제한에 의해 제한 됩니다. 이 고정 메모리 제한을 늘리려면 앱 설정 `languageWorkers:node:arguments` `--max-old-space-size=<max memory in MB>`값으로 추가 합니다.

## <a name="regions"></a>영역

다음은 각 OS에 대해 현재 지원 되는 지역입니다.

|지역| Windows | Linux |
|--| -- | -- |
|오스트레일리아 중부| ✔<sup>1</sup> | |
|오스트레일리아 중부 2| ✔<sup>1</sup> | |
|오스트레일리아 동부| ✔ | |
|오스트레일리아 남동부 | ✔ | ✔<sup>1</sup> |
|브라질 남부| ✔<sup>2</sup> |  |
|캐나다 중부| ✔ |  |
|미국 중부| ✔ |  |
|동아시아| ✔ |  |
|미국 동부 | ✔ | ✔<sup>1</sup> |
|미국 동부 2| ✔ |  |
|프랑스 중부| ✔ |  |
|독일 중서부| ✔ | |
|일본 동부| ✔ | ✔<sup>1</sup> |
|일본 서부| ✔ | |
|한국 중부| ✔ |  |
|미국 중북부| ✔ |  |
|북유럽| ✔ | ✔<sup>1</sup> |
|미국 중남부| ✔ | ✔<sup>1</sup> |
|인도 남부 | ✔ | |
|동남아시아| ✔ | ✔<sup>1</sup> |
|영국 남부| ✔ | |
|영국 서부| ✔ |  |
|서유럽| ✔ | ✔<sup>1</sup> |
|인도 서부| ✔ |  |
|미국 서부| ✔ | ✔<sup>1</sup> |
|미국 서부 2| ✔ |  |

<sup>1</sup> 최대 확장은 20 개 인스턴스로 제한 됩니다.  
<sup>2</sup> 최대 확장은 60 인스턴스로 제한 됩니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 크기 조정 및 호스팅 옵션 이해](functions-scale.md)
