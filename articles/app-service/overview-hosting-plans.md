---
title: App Service 계획
description: Azure App Service에서 App Service 계획의 작동 방식, 고객에 게 요금을 청구 하는 방법 및 요구에 맞게 크기를 조정 하는 방법을 알아봅니다.
keywords: App Service, Azure App Service, 규모, 확장 가능, 확장성, App Service 계획, App Service 비용
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: f30221de81b6bef199c0a25e770558c4db8c4006
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88958515"
---
# <a name="azure-app-service-plan-overview"></a>Azure App Service 계획 개요

App Service (Web Apps, API Apps 또는 Mobile Apps)에서 앱은 항상 _App Service 계획_으로 실행 됩니다. 또한 [Azure Functions](../azure-functions/functions-scale.md#app-service-plan) 에는 _App Service 계획_에서 실행 하는 옵션도 있습니다. App Service 계획은 실행할 웹앱에 대한 컴퓨팅 리소스 세트를 정의합니다. 이러한 계산 리소스는 기존 웹 호스팅의 [_서버 팜과_](https://wikipedia.org/wiki/Server_farm) 유사 합니다. 하나 이상의 앱은 동일한 컴퓨팅 리소스(또는 동일한 App Service 계획)에서 실행하도록 구성될 수 있습니다.

특정 영역(예: 서유럽)에서 App Service 계획을 만들 때 해당 지역에서 해당 계획에 대한 컴퓨팅 리소스 세트를 만듭니다. App Service 계획에 정의된 대로 이 App Service 계획에 추가한 모든 앱은 이러한 컴퓨팅 리소스에서 실행됩니다. 각 App Service 계획은 다음을 정의합니다.

- 지역(미국 서부, 미국 동부 등)
- VM 인스턴스 수
- VM 인스턴스 크기(소량, 중간, 대규모)
- 가격 책정 계층 (무료, 공유, 기본, 표준, 프리미엄, PremiumV2, 격리)

App Service 계획의 _가격 책정 계층_은 가져올 App Service 기능 및 계획에 대한 지불액을 결정합니다. 가격 책정 계층의 몇 가지 범주가 있습니다.

- **공유 컴퓨팅**: **체험** 및 **공유**라는 두 개의 기본 계층은 다른 고객의 앱을 비롯한 다른 App Service 앱과 동일한 Azure VM에서 앱을 실행합니다. 이러한 계층은 CPU 할당량을 공유 리소스에서 실행되는 각 앱에 할당하고 리소스는 확장할 수 없습니다.
- **전용 컴퓨팅**: **기본**, **표준**, **프리미엄** 및 **PremiumV2** 계층은 전용 Azure VM에서 앱을 실행합니다. 동일한 App Service 계획의 앱만이 동일한 컴퓨팅 리소스를 공유합니다. 계층이 높을수록 스케일 아웃을 위해 더 많은 VM 인스턴스가 제공됩니다.
- **격리**:이 계층은 전용 Azure 가상 네트워크에서 전용 azure vm을 실행 합니다. 앱에 대 한 계산 격리를 기반으로 네트워크 격리를 제공 합니다. 최대 스케일 아웃 기능을 제공합니다.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

또한 각 계층은 App Service 기능의 특정 하위 집합을 제공합니다. 이러한 기능에는 사용자 지정 도메인 및 TLS/SSL 인증서, 자동 크기 조정, 배포 슬롯, 백업, Traffic Manager 통합 등이 포함 됩니다. 계층이 높을수록 더 많은 기능을 사용할 수 있습니다. 각 가격 책정 계층에서 지원되는 기능을 알아보려면 [App Service 계획 세부 정보](https://azure.microsoft.com/pricing/details/app-service/plans/)를 참조하세요.

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> 새 **PremiumV2** 가격 책정 계층은 [Dv2 시리즈 VM](../virtual-machines/dv2-dsv2-series.md)에 더 빠른 프로세서, SSD 스토리지 및 **표준**과 비교하여 두 배의 메모리 대 코어 비율을 제공합니다. 또한 **PremiumV2**는 표준 계획의 모든 고급 기능을 제공하면서 늘어난 인스턴스 수를 통해 더 큰 규모를 지원합니다. 기존 **프리미엄** 계층에서 사용할 수 있는 모든 기능이 **PremiumV2**에 포함되었습니다.
>
> 다른 전용 계층과 마찬가지로 다음 3가지 VM 크기를 이 계층에 사용할 수 있습니다.
>
> - 소량(한 개의 CPU 코어, 3.5GiB 메모리) 
> - 중간(두 개의 CPU 코어, 7GiB 메모리) 
> - 대규모(네 개의 CPU 코어, 14GiB 메모리)  
>
> **PremiumV2** 가격 책정에 대한 자세한 내용은 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.
>
> 새 **PremiumV2** 가격 책정 계층을 시작하려면 [App Service에 대해 PremiumV2 계층 구성](app-service-configure-premium-tier.md)을 참조하세요.

## <a name="how-does-my-app-run-and-scale"></a>앱을 실행하고 크기를 조정하려면 어떻게 하나요?

**무료** 및 **공유** 계층에서 앱은 공유 VM 인스턴스에서 CPU 분을 수신 하 고 확장할 수 없습니다. 다른 계층에서는 다음과 같이 앱이 실행 되 고 크기가 조정 됩니다.

App Service에서 앱을 만드는 경우 App Service 계획에 배치합니다. 앱을 실행할 때 App Service 계획에 구성된 모든 VM 인스턴스에서 실행합니다. 여러 앱이 동일한 App Service 계획에 있는 경우 모두 동일한 VM 인스턴스를 공유합니다. 또한 앱에 다중 배포 슬롯이 있는 경우 모든 배포 슬롯은 동일한 VM 인스턴스에서 실행됩니다. 진단 로그를 사용하거나, 백업을 수행하거나, WebJobs을 실행하는 경우 이러한 VM 인스턴스에서 CPU 주기 및 메모리를 사용합니다.

이러한 방식으로 App Service 계획은 App Service 앱의 배율 단위입니다. 계획이 5개의 VM 인스턴스를 실행하도록 구성되면 계획의 모든 앱은 5개의 모든 인스턴스에서 실행됩니다. 계획이 자동 크기 조정을 위해 구성되면 계획의 모든 앱은 자동 크기 조정 설정에 따라 함께 확장됩니다.

앱의 규모 확장에 대한 자세한 내용은 [수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/platform/autoscale-get-started.md)을 참조하세요.

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>App Service 계획의 비용은 얼마인가요?

이 섹션에서는 App Service 앱의 요금 지불 방법을 설명합니다. 자세한 지역 특정 가격 책정 정보는 [App Service 가격 책정](https://azure.microsoft.com/pricing/details/app-service/)을 참조하세요.

**무료** 계층을 제외 하 고 App Service 요금제는 사용 하는 계산 리소스에 대 한 요금을 부과 합니다.

- **공유** 계층에서 각 앱은 cpu 분의 할당량을 수신 하므로 _각 앱_ 은 cpu 할당량에 대 한 요금이 부과 됩니다.
- 전용 계산 계층 (**기본**, **표준**, **프리미엄**, **PremiumV2**)에서 App Service 계획은 앱 크기를 조정 하는 vm 인스턴스 수를 정의 합니다. 따라서 App Service 계획의 _각 vm 인스턴스에_ 대 한 요금이 부과 됩니다. 이러한 VM 인스턴스는 여기에서 실행되는 앱의 개수에 관계 없이 동일한 요금이 청구됩니다. 예기치 않은 요금을 방지하려면 [App Service 계획 정리](app-service-plan-manage.md#delete)를 참조하세요.
- **격리** 계층에서 App Service Environment는 앱을 실행 하는 격리 된 작업자 수를 정의 하 고 _각 작업자_ 는 요금이 부과 됩니다. 또한 App Service Environment 자체를 실행 하는에 대 한 플랫 스탬프 요금이 있습니다.

사용자가 사용할 수 있는 App Service 기능 (사용자 지정 도메인, TLS/SSL 인증서, 배포 슬롯, 백업 등) 사용에 대 한 요금은 청구 되지 않습니다. 예외는 다음과 같습니다.

- App Service 도메인 - Azure에서 구입할 때 및 매년 갱신할 때 지불합니다.
- App Service Certificates - Azure에서 구입할 때 및 매년 갱신할 때 지불합니다.
- IP 기반 TLS 연결-각 IP 기반 TLS 연결에 대해 시간당 요금이 부과 되지만 일부 **표준** 계층 이상에서는 하나의 IP 기반 tls 연결을 무료로 제공 합니다. SNI 기반 TLS 연결은 무료입니다.

> [!NOTE]
> 다른 Azure 서비스와 App Service를 통합하는 경우 이러한 다른 서비스에서 청구를 고려해야 합니다. 예를 들어 Azure Traffic Manager를 사용하여 지리적으로 앱의 크기를 조정하는 경우 Azure Traffic Manager도 사용량에 기반하여 요금이 청구됩니다. Azure에서 교차 서비스 비용을 계산하려면 [가격 책정 계산기](https://azure.microsoft.com/pricing/calculator/)를 참조하세요. 

클라우드 비용을 최적화하여 비용을 절감하고 싶습니까?

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>앱에 더 많은 기능이 필요한 경우

언제든지 App Service 계획을 확장하고 축소할 수 있습니다. 계획의 가격 책정 계층을 변경하는 것처럼 간단합니다. 처음에 더 낮은 가격 책정 계층을 선택하고 더 많은 App Service 기능이 필요하면 나중에 확장할 수 있습니다.

예를 들어 **체험** App Service 계획에서 웹앱을 테스트하기 시작하고 비용을 지불하지 않을 수 있습니다. [사용자 지정 DNS 이름](app-service-web-tutorial-custom-domain.md)을 웹앱에 추가하려는 경우 계획을 **공유** 계층까지 확장하면 됩니다. 나중에 [TLS 바인딩을 만들려면](configure-ssl-bindings.md)계획을 **기본** 계층까지 확장 합니다. [스테이징 환경](deploy-staging-slots.md)이 필요하면 **표준** 계층까지 확장합니다. 더 많은 코어, 메모리 또는 스토리지가 필요한 경우 동일한 계층에서 더 큰 VM 크기로 확장합니다.

동일하게 반대로도 작동합니다. 더 높은 계층의 기능이 더 이상 필요하지 않으면 하위 계층으로 축소하여 비용을 절약할 수 있습니다.

App Service 계획을 확장하는 방법에 대한 자세한 내용은 [Azure에서 앱 확장](manage-scale-up.md)을 참조하세요.

앱이 다른 앱과 동일한 App Service 계획에 있으면 컴퓨팅 리소스를 격리하여 앱의 성능을 향상하는 것이 좋습니다. 앱을 별도의 App Service 계획으로 이동하여 수행할 수 있습니다. 자세한 내용은 [앱을 다른 App Service 계획으로 이동](app-service-plan-manage.md#move)을 참조하세요.

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>새 계획 또는 기존 계획에서 앱을 배치해야 하나요?

App Service 계획이 할당하는 리소스를 계산하기 위해 지불하므로([App Service 계획의 비용은 얼마인가요?](#cost) 참조) 잠재적으로 하나의 App Service 계획에 여러 앱을 배치하여 비용을 절약할 수 있습니다. 계획에 부하를 처리할 리소스가 충분하면 기존 계획에 앱을 계속 추가할 수 있습니다. 그러나 동일한 App Service 계획의 앱은 모두 동일한 컴퓨팅 리소스를 공유합니다. 새 앱에 필요한 리소스가 있는지 확인하려면 기존 App Service 계획의 용량과 새 앱의 예상 부하를 이해해야 합니다. App Service 계획을 오버로드하면 새 앱과 기존 앱의 가동 중지 시간이 발생할 수 있습니다.

다음의 경우 새 App Service 계획으로 앱을 격리합니다.

- 앱이 리소스를 많이 사용합니다.
- 기존 계획의 다른 앱과 독립적으로 앱의 크기를 조정 하려고 합니다.
- 앱에 서로 다른 지역의 리소스가 필요합니다.

이 방식을 사용하면 앱에 새 리소스 집합을 할당하고 앱을 더 잘 제어할 수 있습니다.

## <a name="manage-an-app-service-plan"></a>App Service 계획 관리

> [!div class="nextstepaction"]
> [App Service 계획 관리](app-service-plan-manage.md)