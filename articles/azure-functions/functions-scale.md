---
title: Azure Functions 크기 조정 및 호스팅 | Microsoft Docs
description: Azure Functions 소비 계획 및 프리미엄 요금제 중에서 선택 하는 방법에 알아봅니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: azure functions, 함수, 소비 계획, 프리미엄 요금제, 이벤트 처리, webhook, 동적 계산, 서버가 없는 아키텍처
ms.assetid: 5b63649c-ec7f-4564-b168-e0a74cb7e0f3
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 03/27/2019
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 459758142c94ddfd244df1427dd5632186efc0de
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61045850"
---
# <a name="azure-functions-scale-and-hosting"></a>Azure Functions 크기 조정 및 호스팅

Azure Functions는 두 개의 다른 계획에서 실행 됩니다. 소비 계획 및 프리미엄 요금제 (공개 미리 보기). 소비 계획은 코드가 실행 될 때 계산 능력을 자동으로 추가 합니다. 앱 부하를 처리 하는 데 필요한 경우 규모 확장 이며 코드 실행이 중지 되 면 축소 됩니다. 유휴 VM에 대한 요금을 지불하고 용량을 미리 예약할 필요가 없습니다.  프리미엄 요금제는 자동으로 확장 하 고 코드를 실행 하는 경우 추가 계산 능력을 추가 합니다.  프리미엄 요금제는 프리미엄 계산 인스턴스, 인스턴스를 무기한으로 웜 보관 하는 기능 및 vnet 대 VNet 연결 기능을 추가로 제공 됩니다.  기존 App Service 계획에 있는 경우에 내에서 함수 앱을 실행할 수 있습니다.

> [!NOTE]  
> 둘 다 [프리미엄 요금제](https://aka.ms/functions-premiumplan) 하 고 [Linux에 대 한 소비 계획](https://azure.microsoft.com/updates/azure-functions-consumption-plan-for-linux-preview/) 는 현재 미리 보기로 제공 합니다.

Azure Functions에 익숙하지 않으면 [Azure Functions 개요](functions-overview.md)를 참조하세요.

함수 앱을 만들 때 앱에 있는 함수에 대한 호스팅 계획을 선택합니다. 두 계획에서 *Azure Functions 호스트* 인스턴스는 함수를 실행합니다. 계획 형식은 다음을 제어합니다.

* 호스트 인스턴스 확장 방법
* 각 호스트에 사용할 수 있는 리소스
* 인스턴스 기능 같은 vnet 대 VNet 연결 합니다.

> [!NOTE]
> 함수 앱 리소스의 계획 속성을 변경 하 여 소비 및 프리미엄 요금제를 전환할 수 있습니다.

## <a name="consumption-plan"></a>소비 계획

소비 계획을 사용 하는 경우 Azure Functions 호스트 인스턴스가 동적으로 추가 및 제거 들어오는 이벤트의 수를 기준으로 합니다. 이 서버리스 계획은 자동으로 규모를 조정하며, 함수를 실행하는 경우에만 계산 리소스에 대한 요금이 청구됩니다. 소비 계획에서 구성 가능한 시간 후 함수 실행 시간이 초과됩니다.

청구는 실행 횟수, 실행 시간 및 사용된 메모리를 기반으로 하며, 함수 앱 내의 모든 함수에 대해 집계됩니다. 자세한 내용은 [Azure Functions 가격 책정 페이지]를 참조하세요.

소비 계획은 기본 호스팅 계획이며 다음과 같은 이점을 제공합니다.

* 함수를 실행 중인 경우에만 지불
* 높은 부하 기간 동안에도 자동으로 규모 확장

## <a name="premium-plan-public-preview"></a>프리미엄 요금제 (공개 미리 보기)

프리미엄 요금제를 사용 하는 경우 Azure Functions 호스트 인스턴스의 신속 하 게 추가 및 제거 소비 계획에서와 마찬가지로 들어오는 이벤트의 수를 기준으로 합니다.  그러나 프리미엄 요금제는 또한 제공합니다.

* 모든 콜드 부팅을 방지 하려면 항상 웜 인스턴스입니다.
* Vnet 대 VNet 연결 합니다.
* 무제한 실행 기간입니다.
* 프리미엄 인스턴스 크기 (1 개 코어, 2 코어 및 4 개 코어 인스턴스).
* 가격 책정 옵션 예측 가능 합니다.
* 여러 함수 앱을 사용 하 여 계획에 대 한 고밀도 앱 할당 합니다.

이러한 옵션을 구성 하는 방법에 대 한 정보를 찾을 수 있습니다 합니다 [Azure Functions 프리미엄 요금제](functions-premium-plan.md)합니다.

실행 및 사용 하는 메모리 당 청구 하는 대신 청구는 필요한 및 예약 된 인스턴스에서 사용 되는 GB 초 및 코어 수를 기준으로 합니다.  인스턴스를 하나 이상 있으면 번 있습니다 (실행 번호)에 관계 없이 활성 상태인 계획 당 한 고정 월별 비용이 전혀 웜 가능 합니다.

다음과 같은 경우 Azure Functions 프리미엄 요금제를 것이 좋습니다.
* 함수 앱을 계속해서 또는 거의 끊임없이 실행합니다. 이 경우 App Service 계획은 좀 더 비용 효율적이 될 수 있습니다.
* 소비 계획에서 제공하는 것보다 많은 CPU 또는 메모리 옵션이 필요합니다.
* 코드가 보다 오래 실행 해야 합니다 [허용 된 최대 실행 시간](#timeout) 소비 계획에서.
* App Service 환경에 대한 지원, VNET/VPN 연결 및 더 큰 VM 크기와 같이 App Service 계획에서만 사용할 수 있는 기능이 필요합니다.

> [!NOTE]
> 프리미엄 계획 미리 보기는 현재 Windows 인프라를 통해.NET, 노드 또는 Java에서 실행 되는 함수를 지원 합니다.

프리미엄 계획에서 JavaScript 함수를 실행 하는 경우 더 적은 Vcpu에는 인스턴스를 선택 해야 합니다. 자세한 내용은 참조는 [단일 코어 프리미엄 요금제 선택](functions-reference-node.md#considerations-for-javascript-functions).  

## <a name="app-service-plan"></a>App Service 계획

함수 앱 (Basic, Standard, Premium 및 격리 된 Sku) 다른 App Service 앱과 동일한 전용된 Vm에서 실행할 수도 있습니다. App Service 계획은 Linux를 지원합니다.

다음과 같은 경우에 App Service 계획을 고려합니다.

* 이미 다른 App Service 인스턴스를 실행하고 있는 기존의 활용도가 낮은 VM이 있습니다.
* Linux에서 함수 앱을 실행하거나 함수를 실행할 사용자 지정 이미지를 제공하려고 합니다.

지불 동일한 App Service 계획에서 함수 앱에 대 한 웹 앱 같은 다른 App Service 리소스와 마찬가지로 합니다. App Service 계획의 작동 원리에 대한 자세한 내용은 [Azure App Service 계획의 포괄 개요](../app-service/overview-hosting-plans.md)를 참조하세요. 

App Service 계획을 사용하면 더 많은 VM 인스턴스를 추가하여 수동으로 확장하거나 자동 조정을 사용하도록 설정할 수 있습니다. 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/platform/autoscale-get-started.md?toc=%2fazure%2fapp-service%2ftoc.json)을 참조하세요. 다른 App Service 계획을 선택하여 확장할 수도 있습니다. 자세한 내용은 [Azure에서 앱 확장](../app-service/web-sites-scale.md)을 참조하세요. 

App Service 계획에서 JavaScript 함수를 실행 중인 경우 vCPU 수가 더 작은 계획을 선택해야 합니다. 자세한 내용은 [단일 코어 App Service 계획 선택](functions-reference-node.md#choose-single-vcpu-app-service-plans)합니다. 
<!-- Note: the portal links to this section via fwlink https://go.microsoft.com/fwlink/?linkid=830855 --> 

### <a name="always-on"></a> Always On

App Service 계획에서 실행하는 경우 함수 앱이 올바르게 실행되도록 **Always On** 설정을 사용해야 합니다. App Service 계획에서 함수 런타임은 비활성화되고 몇 분 후 유휴 상태가 되므로 HTTP 트리거만 함수를 “다시 시작”합니다. Always On은 App Service 계획에서만 사용할 수 있습니다. 소비 계획에서 플랫폼은 함수 앱을 자동으로 활성화합니다.

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="what-is-my-hosting-plan"></a>내 호스팅 계획이란?

함수 앱에서 사용하는 호스팅 계획을 결정하려면 [Azure Portal](https://portal.azure.com)의 함수 앱에 대한 **개요** 탭에서 **App Service 계획/가격 책정 계층**을 참조하세요. App Service 계획의 경우 가격 책정 계층도 표시됩니다. 

![포털에서 크기 조정 계획 보기](./media/functions-scale/function-app-overview-portal.png)

또한 다음과 같이 계획을 확인하기 위해 Azure CLI를 사용할 수도 있습니다.

```azurecli-interactive
appServicePlanId=$(az functionapp show --name <my_function_app_name> --resource-group <my_resource_group> --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv
```  

이 명령의 출력이 `dynamic`인 경우 함수 앱은 소비 계획 상태입니다. 이 명령의 출력에서에서 경우 `ElasticPremium`, 함수 앱이 프리미엄 요금제입니다.  다른 모든 값은 App Service 계획의 계층을 나타냅니다.

Always On이 설정된 경우에도 개별 함수의 실행 시간 초과는 [host.json](functions-host-json.md#functiontimeout) 프로젝트 파일의 `functionTimeout` 설정에 의해 제어됩니다.

## <a name="storage-account-requirements"></a>Storage 계정 요구 사항

모든 계획을 함수 앱을 Azure Blob, 큐, 파일 및 테이블 저장소를 지 원하는 일반 Azure 저장소 계정에 필요 합니다. 왜냐하면 함수 트리거 관리 및 함수 실행 로깅 등의 작업에 대 한 Azure 저장소를 사용 하지만 큐 및 테이블에는 일부 저장소 계정은 지원 하지 않습니다. Blob 전용 스토리지 계정(Premium Storage 포함) 및 영역 중복 스토리지 복제가 사용되는 범용 스토리지 계정을 포함한 계정은 함수 앱을 만들 때 기존 **스토리지 계정** 선택 항목에서 필터링됩니다.

<!-- JH: Does using a Premium Storage account improve perf? -->

스토리지 계정 유형에 대해 자세히 알아보려면 [Azure Storage 서비스 소개](../storage/common/storage-introduction.md#azure-storage-services)를 참조하세요.

## <a name="how-the-consumption-and-premium-plans-work"></a>소비 및 프리미엄 요금제의 작동 방법

사용량 및 프리미엄 요금제에서는 크기 조정 컨트롤러는 해당 함수가 트리거되는 이벤트의 수에 따라 함수 호스트의 인스턴스를 추가 하 여 CPU 및 메모리 리소스 자동으로 조정 합니다. 소비 계획에서 함수 호스트의 각 인스턴스는 1.5GB의 메모리와 1로 제한 됩니다. CPU입니다.  호스트의 인스턴스가 전체 함수 앱을 동시에는 인스턴스 및 확장 내에서 함수 앱 공유 리소스 내에서 모든 기능을 의미 합니다. 동일한 소비 계획을 공유 하는 함수 앱이 독립적으로 조정 됩니다.  프리미엄 요금제 계획 크기 결정 하는 사용 가능한 메모리와 CPU 모든 앱에 대 한 해당 인스턴스에서 해당 계획에 합니다.  

함수 코드 파일은 함수의 주 저장소 계정의 Azure 파일 공유에 저장 됩니다. 함수 앱의 주 저장소 계정을 삭제하면 함수 코드 파일이 삭제되고 복구할 수 없습니다.

> [!NOTE]
> 소비 계획에서 Blob 트리거를 사용하는 경우 새 Blob 처리에 하루 최대 10분이 지연될 수 있습니다. 이 지연은 함수 앱이 유휴 상태일 때 발생합니다. 함수 앱이 실행된 후에는 Blob이 즉시 처리됩니다. 이 콜드 시작 지연 시간을 방지 하려면 프리미엄 요금제를 사용 하거나 사용 합니다 [Event Grid 트리거](functions-bindings-event-grid.md)합니다. 자세한 내용은 [Blob 트리거 바인딩 참조 문서](functions-bindings-storage-blob.md#trigger)를 참조합니다.

### <a name="runtime-scaling"></a>런타임 크기 조정

Azure Functions는 *크기 조정 컨트롤러*라는 구성 요소를 사용하여 이벤트의 비율을 모니터링하고 규모를 확장하거나 감축할 것인지 결정합니다. 크기 조정 컨트롤러는 각 트리거 유형에 대해 추론을 사용합니다. 예를 들어 Azure Queue Storage 트리거를 사용하는 경우 큐 길이 및 가장 오래된 큐 메시지의 기간에 따라 트리거가 조정됩니다.

크기 조정 단위는 함수 앱입니다. 함수 앱을 확장하면 Azure Functions 호스트의 여러 인스턴스를 실행하기 위해 추가 리소스가 할당됩니다. 반대로, 계산 수요가 감소하면 크기 조정 컨트롤러에서 함수 호스트 인스턴스를 제거합니다. 함수 앱 내에서 실행 중인 함수가 없으면 인스턴스 수가 결국 0으로 축소됩니다.

![이벤트를 모니터링하고 인스턴스를 만드는 크기 조정 컨트롤러](./media/functions-scale/central-listener.png)

### <a name="understanding-scaling-behaviors"></a>크기 조정 동작 이해

크기 조정은 다양한 요인에 따라 다르고, 선택한 트리거 및 언어에 따라 달라질 수 있습니다. 그러나 현재 시스템에서 크기 조정의 몇 가지 측면은 다음과 같습니다.

* 단일 함수 앱은 최대 200개의 인스턴스로만 강화됩니다. 단일 인스턴스는 동시에 둘 이상의 메시지 또는 요청을 처리할 수 있지만 동시 실행 수를 제한하지 않습니다.
* HTTP 트리거에 대 한 새 인스턴스만 할당할 1 초 마다 한 번만 합니다.
* HTTP가 아닌 트리거의 경우 새 인스턴스만 할당할 30 초 마다 한 번만 합니다.

다른 트리거에는 아래에 문서화된 대로 다른 규모 조정 제한이 있을 수도 있습니다.

* [이벤트 허브](functions-bindings-event-hubs.md#trigger---scaling)

### <a name="best-practices-and-patterns-for-scalable-apps"></a>확장성 있는 앱의 모범 사례 및 패턴

함수 앱에는 호스트 구성, 런타임 공간 및 리소스 효율성을 비롯하여 규모 조정에 영향을 주는 여러 측면이 있습니다.  자세한 내용은 [성능 고려 사항 문서의 확장성 섹션](functions-best-practices.md#scalability-best-practices)을 참조하세요. 또한 함수 앱의 확장에 따라 연결이 어떻게 작동하는지도 알고 있어야 합니다. 자세한 내용은 [Azure Functions에서 연결을 관리하는 방법](manage-connections.md)을 참조하세요.

### <a name="billing-model"></a>청구 모델

소비 계획의 요금 청구는 [Azure Functions 가격 책정 페이지]에 자세히 설명되어 있습니다. 사용량은 함수 앱 수준에서 집계되며 함수 코드가 실행될 때만 계산됩니다. 다음은 요금 청구 단위입니다.

* **기가바이트-초 단위의 리소스 소비(GB-s)**. 함수 앱 내 모든 함수의 메모리 크기와 실행 시간 조합으로 계산됩니다. 
* **실행 횟수**. 이벤트 트리거에 대한 응답으로 함수가 실행될 때마다 계산됩니다.

유용한 쿼리 및 소비 청구서를 이해 하는 방법에 대 한 정보를 찾을 수 있습니다 [청구 FAQ에](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ)입니다.

[Azure Functions 가격 책정 페이지]: https://azure.microsoft.com/pricing/details/functions
