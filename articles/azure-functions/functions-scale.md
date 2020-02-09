---
title: Azure Functions 크기 조정 및 호스팅
description: Azure Functions 소비 계획 및 프리미엄 계획 중에서 선택 하는 방법을 알아봅니다.
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.topic: conceptual
ms.date: 03/27/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fb36b81d1b2a343da334d63d9c0555ed537ef122
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024656"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions 크기 조정 및 호스팅

Azure에서 함수 앱을 만들 때 앱에 대한 호스팅 계획을 선택 해야 합니다. Azure Functions는 [소비 계획](#consumption-plan), [프리미엄 계획](#premium-plan)및 [전용 (App Service) 계획](#app-service-plan)의 세 가지 호스팅 계획을 사용할 수 있습니다.

선택한 호스팅 계획에 따라 다음과 같은 동작이 결정 됩니다.

* 함수 앱의 크기를 조정 하는 방법입니다.
* 각 함수 앱 인스턴스에 사용할 수 있는 리소스입니다.
* Azure Virtual Network 연결과 같은 고급 기능을 지원 합니다.

코드를 실행할 때 소비 및 프리미엄 계획 모두 자동으로 계산 기능을 추가 합니다. 앱이 로드를 처리 하는 데 필요한 경우 확장 되 고 코드 실행이 중지 되 면에서 크기가 조정 됩니다. 소비 계획의 경우 유휴 Vm에 대한 비용을 지불할 필요도 없으며 사전에 용량을 예약 하지 않아도 됩니다.  

프리미엄 요금제는 프리미엄 계산 인스턴스, 인스턴스를 무기한으로 유지 하는 기능 및 VNet 연결 등의 추가 기능을 제공 합니다.

App Service 계획을 사용 하면 관리 하는 전용 인프라를 활용할 수 있습니다. 함수 앱은 이벤트를 기준으로 크기를 조정 하지 않습니다. 즉, 0으로 확장 되지 않습니다. [Always on](#always-on) 을 사용 하도록 설정 해야 합니다.

## <a name="hosting-plan-support"></a>호스팅 계획 지원

기능 지원은 다음 두 가지 범주로 구분 됩니다.

* _일반적으로 사용 가능 (GA)_ : 프로덕션 사용을 위해 완전히 지원 되 고 승인 됩니다.
* _미리 보기_: 아직 완전히 지원 되지 않으며 프로덕션 사용을 위해 승인 되지 않았습니다.

다음 표는 Windows 또는 Linux에서 실행 되는 경우 세 가지 호스팅 계획에 대한 현재 지원 수준을 나타냅니다.

| | 사용량 과금 플랜 | 프리미엄 플랜 | 전용 계획 |
|-|:----------------:|:------------:|:----------------:|
| Windows | GA | GA | GA |
| Linux | GA | GA | GA |

## <a name="consumption-plan"></a>사용량 과금 플랜

소비 계획을 사용 하는 경우 Azure Functions 호스트의 인스턴스는 들어오는 이벤트의 수에 따라 동적으로 추가 및 제거 됩니다. 이 서버리스 계획은 자동으로 규모를 조정하며, 함수를 실행하는 경우에만 컴퓨팅 리소스에 대한 요금이 청구됩니다. 소비 계획에서 구성 가능한 시간 후 함수 실행 시간이 초과됩니다.

청구는 실행 횟수, 실행 시간 및 사용된 메모리를 기반으로 하며, 함수 앱 내의 모든 함수에 대해 집계됩니다. 자세한 내용은 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)를 참조하세요.

소비 계획은 기본 호스팅 계획이며 다음과 같은 이점을 제공합니다.

* 함수를 실행 중인 경우에만 지불
* 높은 부하 기간 동안에도 자동으로 규모 확장

동일한 지역에 있는 함수 앱은 동일한 소비 계획에 할당할 수 있습니다. 여러 앱을 동일한 소비 계획에서 실행 하는 경우에는 영향을 주지 않습니다. 여러 앱을 동일한 소비 계획에 할당 하면 각 앱의 복원 력, 확장성 또는 안정성에 영향을 주지 않습니다.

소비 계획에서 실행 하는 경우 비용을 계산 하는 방법에 대한 자세한 내용은 [소비 계획 비용 이해](functions-consumption-costs.md)를 참조 하세요.

## <a name="premium-plan"></a>프리미엄 요금제

프리미엄 요금제를 사용 하는 경우에는 소비 계획과 마찬가지로 들어오는 이벤트 수에 따라 Azure Functions 호스트의 인스턴스가 추가 되 고 제거 됩니다.  프리미엄 요금제는 다음과 같은 기능을 지원 합니다.

* 콜드 시작을 방지 하기 위해 웜 인스턴스 영구적으로
* VNet 연결
* 무제한 실행 기간
* 프리미엄 인스턴스 크기 (1 개 코어, 2 개 코어 및 4 개 코어 인스턴스)
* 보다 예측 가능한 가격 책정
* 여러 함수 앱이 포함 된 계획에 대한 고밀도 앱 할당

이러한 옵션을 구성 하는 방법에 대한 정보는 [Azure Functions 프리미엄 계획 문서](functions-premium-plan.md)에서 찾을 수 있습니다.

실행 당 청구 및 사용 되는 메모리 대신 프리미엄 요금제에 대한 청구는 필요한 코어 초와 준비 인스턴스 수에 사용 되는 메모리에 따라 결정 됩니다. 하나 이상의 인스턴스가 계획 당 항상 웜 되어야 합니다. 즉, 실행 수에 관계 없이 활성 계획 당 최소 월별 비용이 발생 합니다. 프리미엄 계획의 모든 함수 앱은 준비 및 활성 인스턴스를 공유 한다는 점에 유의 하세요.

다음과 같은 경우 Azure Functions 프리미엄 계획을 고려 합니다.

* 함수 앱을 계속해서 또는 거의 끊임없이 실행합니다.
* 적은 수의 작은 실행을 보유 하 고 있으며 소비 계획에 높은 실행 청구서가 있지만 낮은 GB의 두 번째 청구서가 있습니다.
* 소비 계획에서 제공 하는 것 보다 더 많은 CPU 또는 메모리 옵션이 필요 합니다.
* 코드는 소비 계획에서 [허용 되는 최대 실행 시간](#timeout) 보다 오래 실행 해야 합니다.
* 프리미엄 요금제 (예: 가상 네트워크 연결) 에서만 사용할 수 있는 기능이 필요 합니다.

프리미엄 계획에서 JavaScript 함수를 실행 하는 경우 vCPUs 수가 작은 인스턴스를 선택 해야 합니다. 자세한 내용은 [단일 코어 프리미엄 계획 선택](functions-reference-node.md#considerations-for-javascript-functions)을 참조 하세요.  

## <a name="app-service-plan"></a>전용 (App Service) 계획

함수 앱은 다른 App Service 앱 (Basic, Standard, Premium 및 격리 Sku)과 동일한 전용 Vm에서 실행할 수도 있습니다.

다음과 같은 경우 App Service 계획을 고려해 야 합니다.

* 이미 다른 App Service 인스턴스를 실행하고 있는 기존의 활용도가 낮은 VM이 있습니다.
* 함수를 실행할 사용자 지정 이미지를 제공 하려고 합니다.

웹 앱과 같은 다른 App Service 리소스의 경우와 마찬가지로 App Service 계획에서 함수 앱에 대해 동일한 비용을 지불 합니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/overview-hosting-plans.md)를 참조하세요.

App Service 계획을 사용 하면 더 많은 VM 인스턴스를 추가 하 여 수동으로 확장할 수 있습니다. 자동 크기 조정을 사용 하도록 설정할 수도 있습니다. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)을 참조하세요. 다른 App Service 계획을 선택하여 확장할 수도 있습니다. 자세한 내용은 [Azure에서 앱 확장](../app-service/manage-scale-up.md)을 참조하세요. 

App Service 계획에서 JavaScript 함수를 실행 중인 경우 vCPU 수가 더 작은 계획을 선택해야 합니다. 자세한 내용은 [단일 코어 App Service 계획 선택](functions-reference-node.md#choose-single-vcpu-app-service-plans)을 참조 하세요. 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a>Always On

App Service 계획에서 실행하는 경우 함수 앱이 올바르게 실행되도록 **Always On** 설정을 사용해야 합니다. App Service 계획에서 함수 런타임은 비활성화되고 몇 분 후 유휴 상태가 되므로 HTTP 트리거만 함수를 “다시 시작”합니다. Always On은 App Service 계획에서만 사용할 수 있습니다. 소비 계획에서 플랫폼은 함수 앱을 자동으로 활성화합니다.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]


Always On이 설정된 경우에도 개별 함수의 실행 시간 초과는 [host.json](functions-host-json.md#functiontimeout) 프로젝트 파일의 `functionTimeout` 설정에 의해 제어됩니다.

## <a name="determine-the-hosting-plan-of-an-existing-application"></a>기존 응용 프로그램의 호스팅 계획 결정

함수 앱에서 사용하는 호스팅 계획을 결정하려면 [Azure Portal](https://portal.azure.com)의 함수 앱에 대한 **개요** 탭에서 **App Service 계획/가격 책정 계층**을 참조하세요. App Service 계획의 경우 가격 책정 계층도 표시됩니다.

![포털에서 크기 조정 계획 보기](./media/functions-scale/function-app-overview-portal.png)

또한 다음과 같이 계획을 확인하기 위해 Azure CLI를 사용할 수도 있습니다.

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

이 명령의 출력이 `dynamic`인 경우 함수 앱은 소비 계획 상태입니다. 이 명령의 출력을 `ElasticPremium`경우 함수 앱은 프리미엄 계획에 있습니다. 다른 모든 값은 App Service 계획의 다른 계층을 표시 합니다.

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항

모든 계획에서 함수 앱에는 Azure Blob, 큐, 파일 및 테이블 저장소를 지 원하는 일반 Azure Storage 계정이 필요 합니다. Functions는 트리거 관리 및 함수 실행 기록과 같은 작업에 Azure Storage를 사용하지만 일부 스토리지 계정은 큐와 테이블을 지원하지 않기 때문입니다. Blob 전용 스토리지 계정(Premium Storage 포함) 및 영역 중복 스토리지 복제가 사용되는 범용 스토리지 계정을 포함한 계정은 함수 앱을 만들 때 기존 **스토리지 계정** 선택 항목에서 필터링됩니다.

함수 앱에서 사용 하는 것과 동일한 저장소 계정을 사용 하 여 응용 프로그램 데이터를 저장할 수 있습니다. 그러나 저장소를 많이 사용 하는 작업의 경우에는 별도의 저장소 계정을 사용 해야 합니다.  

여러 함수 앱에서 문제 없이 동일한 저장소 계정을 공유할 수 있습니다. 이에 대한 좋은 예는 단일 저장소 계정 처럼 작동 하는 Azure Storage 에뮬레이터를 사용 하 여 로컬 환경에서 여러 앱을 개발 하는 경우입니다. 

<!-- JH: Does using a Premium Storage account improve perf? -->

스토리지 계정 유형에 대해 자세히 알아보려면 [Azure Storage 서비스 소개](../storage/common/storage-introduction.md#azure-storage-services)를 참조하세요.

## <a name="how-the-consumption-and-premium-plans-work"></a>소비 및 프리미엄 계획의 작동 방식

소비 및 프리미엄 계획에서 Azure Functions 인프라는 해당 함수가 트리거되는 이벤트의 수에 따라 함수 호스트의 인스턴스를 추가 하 여 CPU 및 메모리 리소스를 확장 합니다. 소비 계획에 있는 함수 호스트의 각 인스턴스는 1.5 GB의 메모리와 하나의 CPU로 제한 됩니다.  호스트의 인스턴스는 전체 함수 앱입니다. 즉, 함수 앱 내의 모든 함수는 인스턴스 내에서 리소스를 공유 하 고 동시에 크기를 조정 합니다. 동일한 소비 계획을 공유하는 함수 앱은 독립적으로 크기 조정됩니다.  프리미엄 계획에서 계획 크기는 해당 인스턴스에서 해당 계획의 모든 앱에 대해 사용 가능한 메모리와 CPU를 결정 합니다.  

함수 코드 파일은 함수의 주 저장소 계정에서 Azure Files 공유에 저장 됩니다. 함수 앱의 주 스토리지 계정을 삭제하면 함수 코드 파일이 삭제되고 복구할 수 없습니다.

### <a name="runtime-scaling"></a>런타임 크기 조정

Azure Functions는 *크기 조정 컨트롤러*라는 구성 요소를 사용하여 이벤트의 비율을 모니터링하고 규모를 확장하거나 감축할 것인지 결정합니다. 크기 조정 컨트롤러는 각 트리거 유형에 대해 추론을 사용합니다. 예를 들어 Azure Queue Storage 트리거를 사용하는 경우 큐 길이 및 가장 오래된 큐 메시지의 기간에 따라 트리거가 조정됩니다.

Azure Functions 확장 단위는 함수 앱입니다. 함수 앱을 확장하면 Azure Functions 호스트의 여러 인스턴스를 실행하기 위해 추가 리소스가 할당됩니다. 반대로, 컴퓨팅 수요가 감소하면 크기 조정 컨트롤러에서 함수 호스트 인스턴스를 제거합니다. 함수 앱 내에서 실행 중인 함수가 없으면 인스턴스 수가 결국 0으로 *축소* 됩니다.

![이벤트를 모니터링하고 인스턴스를 만드는 크기 조정 컨트롤러](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>크기 조정 동작 이해

크기 조정은 다양한 요인에 따라 다르고, 선택한 트리거 및 언어에 따라 달라질 수 있습니다. 유의 해야 할 몇 가지 확장 동작은 다음과 같습니다.

* 단일 함수 앱은 최대 200 개의 인스턴스로 확장할 수 있습니다. 단일 인스턴스는 동시에 둘 이상의 메시지 또는 요청을 처리할 수 있지만 동시 실행 수를 제한하지 않습니다.
* HTTP 트리거의 경우 새 인스턴스는 1 초 마다 한 번만 할당 됩니다.
* HTTP가 아닌 트리거의 경우 새 인스턴스는 30 초 마다 한 번만 할당 됩니다.

다른 트리거에는 아래에 문서화된 대로 다른 규모 조정 제한이 있을 수도 있습니다.

* [이벤트 허브](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>확장성 있는 앱의 모범 사례 및 패턴

함수 앱에는 호스트 구성, 런타임 공간 및 리소스 효율성을 비롯하여 규모 조정에 영향을 주는 여러 측면이 있습니다.  자세한 내용은 [성능 고려 사항 문서의 확장성 섹션](functions-best-practices.md#scalability-best-practices)을 참조하세요. 또한 함수 앱의 확장에 따라 연결이 어떻게 작동하는지도 알고 있어야 합니다. 자세한 내용은 [Azure Functions에서 연결을 관리하는 방법](manage-connections.md)을 참조하세요.

Python 및 node.js에서 크기를 조정 하는 방법에 대한 자세한 내용은 [Azure Functions python 개발자 가이드-크기 조정 및 동시성](functions-reference-python.md#scaling-and-concurrency) 및 [Azure Functions node.js 개발자 가이드-크기 조정 및 동시성](functions-reference-node.md#scaling-and-concurrency)을 참조 하세요.

### <a name="billing-model"></a>청구 모델

여러 요금제에 대한 요금 청구는 [Azure Functions 가격 책정 페이지](https://azure.microsoft.com/pricing/details/functions/)에 자세히 설명 되어 있습니다. 사용량은 함수 앱 수준에서 집계되며 함수 코드가 실행될 때만 계산됩니다. 다음은 요금 청구 단위입니다.

* **기가바이트-초 단위의 리소스 소비(GB-s)** . 함수 앱 내 모든 함수의 메모리 크기와 실행 시간 조합으로 계산됩니다. 
* **실행 횟수**. 이벤트 트리거에 대한 응답으로 함수가 실행될 때마다 계산됩니다.

사용량 청구서를 이해 하는 방법에 대한 유용한 쿼리와 정보 [는 청구 FAQ에서](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)찾을 수 있습니다.

[Azure Functions pricing page]: https://azure.microsoft.com/pricing/details/functions

## <a name="service-limits"></a>서비스 제한

다음 표는 다양 한 호스팅 계획에서 실행 될 때 함수 앱에 적용 되는 제한을 나타냅니다.

[!INCLUDE [functions-limits](../../includes/functions-limits.md)]
