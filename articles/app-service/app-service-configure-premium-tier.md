---
title: PremiumV2 계층 구성
description: 새 PremiumV2 가격 책정 계층으로 확장하여 Azure App Service에서 웹, 모바일 및 API 앱 성능을 향상하는 방법에 대해 알아봅니다.
keywords: 앱 서비스, Azure 앱 서비스, 규모, 확장성, 앱 서비스 계획, 앱 서비스 비용
ms.assetid: ff00902b-9858-4bee-ab95-d3406018c688
ms.topic: article
ms.date: 07/25/2018
ms.custom: seodec18
ms.openlocfilehash: 4db7c6bf29d0874b5441a8a0eb90f7d1ada33d9c
ms.sourcegitcommit: 648c8d250106a5fca9076a46581f3105c23d7265
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "88962437"
---
# <a name="configure-premiumv2-tier-for-azure-app-service"></a>Azure App Service에 대한 PremiumV2 계층 구성

새 **PremiumV2** 가격 책정 계층은 프로세서, SSD 스토리지 속도를 개선하며 기존 가격 책정 계층의 메모리 대 코어 비율을 두 배로 높입니다. 성능상의 이점 덕분에 더 적은 인스턴스에서 앱을 실행하여 비용을 절감할 수 있습니다. 이 문서에서는 **PremiumV2** 계층에서 앱을 만들거나 **PremiumV2** 계층으로 앱을 강화하는 방법에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

앱을 **PremiumV2**로 강화하려면 **PremiumV2**보다 낮은 가격 책정 계층에서 실행되는 Azure App Service이 있어야 하며, 앱은 PremiumV2를 지원하는 App Service 배포에서 실행되어야 합니다.

<a name="availability"></a>

## <a name="premiumv2-availability"></a>PremiumV2 가용성

**PremiumV2** 계층은 현재 _Windows_ 및 _Linux_의 App Service에서 사용할 수 있습니다.

**PremiumV2**는 대부분 Azure 지역에서 사용할 수 있습니다. 해당 지역에서 사용할 수 있는지 확인하려면 [Azure Cloud Shell](../cloud-shell/overview.md)에서 다음 Azure CLI 명령을 실행합니다.

```azurecli-interactive
az appservice list-locations --sku P1V2
```

<a name="create"></a>

## <a name="create-an-app-in-premiumv2-tier"></a>PremiumV2 계층에서 앱 만들기

App Service 앱의 가격 책정 계층은 해당 앱이 실행되는 [App Service 계획](overview-hosting-plans.md)에 정의되어 있습니다. App Service 계획은 단독으로 또는 앱 만들기의 일부로 만들 수 있습니다.

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 App Service 계획을 구성하는 경우 **가격 책정 계층**을 선택합니다. 

**프로덕션**을 선택한 다음, **P1V2**, **P2V2** 또는 **P3V2**를 선택한 다음, **적용**을 클릭합니다.

![앱에 대해 권장 되는 가격 책정 계층을 보여 주는 스크린샷](media/app-service-configure-premium-tier/scale-up-tier-select.png)

> [!IMPORTANT] 
> **P1V2**, **P2V2** 및 **P3V2**가 옵션으로 표시되지 않거나, 해당 옵션이 회색으로 표시되는 경우 **PremiumV2**는 App Service 계획을 포함하는 기본 App Service 배포에서 사용할 수 없습니다. 자세한 내용은 [지원되지 않는 리소스 그룹 및 지역 조합에서 강화](#unsupported)를 참조합니다.

## <a name="scale-up-an-existing-app-to-premiumv2-tier"></a>기존 앱을 PremiumV2 계층으로 강화

기존 앱을 **PremiumV2** 계층으로 확장하기 전에 **PremiumV2**를 사용할 수 있는지 확인합니다. 자세한 내용은 [PremiumV2 가용성](#availability)을 참조하세요. 사용할 수 없는 경우 [지원되지 않는 리소스 그룹 및 지역 조합에서 강화](#unsupported)를 참조합니다.

호스팅 환경에 따라 강화하는 데 추가 단계가 필요할 수 있습니다. 

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>에서 App Service 앱 페이지를 엽니다.

App Service 앱 페이지의 왼쪽 탐색 영역에서 **강화(App Service 계획)** 를 선택합니다.

![App service 계획을 확장 하는 방법을 보여 주는 스크린샷](media/app-service-configure-premium-tier/scale-up-tier-portal.png)

**프로덕션**을 선택한 다음, **P1V2**, **P2V2** 또는 **P3V2**를 선택한 다음, **적용**을 클릭합니다.

![앱에 대해 권장 되는 가격 책정 계층을 보여 주는 스크린샷](media/app-service-configure-premium-tier/scale-up-tier-select.png)

작업이 성공적으로 완료되면 앱의 개요 페이지에 이제 앱이 **PremiumV2** 계층에 있다고 표시됩니다.

![앱의 개요 페이지에서 PremiumV2 가격 책정 계층을 보여 주는 스크린샷](media/app-service-configure-premium-tier/finished.png)

### <a name="if-you-get-an-error"></a>오류가 발생하는 경우

일부 App Service 계획은 기본 App Service 배포가 PremiumV2를 지원하지 않는 경우 PremiumV2 계층을 강화할 수 없습니다.  자세한 내용은 [지원되지 않는 리소스 그룹 및 지역 조합에서 강화](#unsupported)를 참조합니다.

<a name="unsupported"></a>

## <a name="scale-up-from-an-unsupported-resource-group-and-region-combination"></a>지원되지 않는 리소스 그룹 및 지역 조합에서 강화

앱이 **PremiumV2**를 사용할 수 없는 App Service 배포에서 실행되거나 앱이 현재 **PremiumV2**를 지원하지 않는 지역에서 실행되는 경우 **PremiumV2**를 이용하려면 앱을 다시 배포해야 합니다.  다음 두 가지 옵션을 사용할 수 있습니다.

- **새** 리소스 그룹을 만든 다음, **새** 리소스 그룹에서 **새** 앱 및 App Service 계획을 만들어 생성 프로세스 동안 원하는 Azure 지역을 선택합니다.  새 App Service 계획을 만들 때 **PremiumV2** 계획을 선택**해야 합니다**.  이렇게 하면 리소스 그룹, App Service 계획 및 Azure 지역의 조합이 결국 **PremiumV2**를 지원하는 App Service 배포에서 생성되는 App Service 계획이 되어야 합니다.  그런 다음, 새로 만든 앱 및 App Service 계획에 애플리케이션 코드를 다시 배포합니다. 필요한 경우 이후 **PremiumV2**에서 App Service 계획을 축소하여 비용을 절약할 수 있으며 **PremiumV2**를 사용하여 나중에 다시 성공적으로 확장할 수도 있습니다.
- 앱이 이미 기존 **프리미엄** 계층에서 실행되는 경우에는 모든 앱 설정, 연결 문자열 및 배포 구성과 함께 앱을 **PremiumV2**를 사용하는 App Service 계획에 복제할 수 있습니다.

    ![앱을 복제 하는 방법을 보여 주는 스크린샷](media/app-service-configure-premium-tier/clone-app.png)

    **앱 복제** 페이지에서 원하는 지역에서 **PremiumV2**를 사용하여 App Service 계획을 만들고 복제하려는 앱 설정 및 구성을 지정할 수 있습니다.

## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/)을 통해 스크립트를 사용하여 **PremiumV2** 계층에서 앱 생성을 자동화할 수 있습니다.

### <a name="azure-cli"></a>Azure CLI

다음 명령은 _P1V2_에서 App Service 계획을 만듭니다. Cloud Shell에서 실행할 수 있습니다. `--sku`에 대한 옵션은 P1V2, _P2V2_ 및 _P3V2_입니다.

```azurecli-interactive
az appservice plan create \
    --resource-group <resource_group_name> \
    --name <app_service_plan_name> \
    --sku P1V2
```

### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 명령은 _P1V2_에서 App Service 계획을 만듭니다. `-WorkerSize`에 대한 옵션은 _Small_, _Medium_ 및 _Large_입니다.

```powershell
New-AzAppServicePlan -ResourceGroupName <resource_group_name> `
    -Name <app_service_plan_name> `
    -Location <region_name> `
    -Tier "PremiumV2" `
    -WorkerSize "Small"
```
## <a name="more-resources"></a>기타 참고 자료

[Azure에서 앱 강화](manage-scale-up.md)  
[수동 또는 자동으로 인스턴스 개수 조정](../azure-monitor/platform/autoscale-get-started.md)