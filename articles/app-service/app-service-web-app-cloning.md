---
title: PowerShell을 사용하여 앱 복제 - Azure App Service
description: PowerShell을 사용하여 App Service 앱을 새 앱에 복제하는 방법을 알아봅니다.
services: app-service\web
documentationcenter: ''
author: ahmedelnably
manager: stefsch
editor: ''
ms.assetid: f9a5cfa1-fbb0-41e6-95d1-75d457347a35
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2016
ms.author: aelnably
ms.custom: seodec18
ms.openlocfilehash: 907816d4f44a953ae338f6ca505b8fb3e048234e
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329643"
---
# <a name="azure-app-service-app-cloning-using-powershell"></a>PowerShell을 사용하여 Azure App Service 앱 복제

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Microsoft Azure PowerShell 버전 1.1.0 릴리스에서는 기존 App Service 앱을 다른 지역이나 동일한 지역에서 새로 만든 앱에 복제할 수 있도록 하는 새 옵션이 `New-AzWebApp`에 추가되었습니다. 이 옵션을 사용하여 고객은 수많은 앱을 다른 지역에 배포할 수 있습니다.

앱 복제는 Standard, Premium, Premium V2 및 격리 app service 계획에 지원 됩니다. 새로운 기능은 App Service 백업 기능과 동일한 제한 사항을 사용합니다. [Azure App Service에서 앱 백업](manage-backup.md)을 참조하세요.

## <a name="cloning-an-existing-app"></a>기존 앱 복제
시나리오: 미국 중남부 지역에 기존 앱이 있고, 콘텐츠를 미국 중북부 지역의 새 앱으로 복제하려고 합니다. 이 작업은 `-SourceWebApp` 옵션으로 새 앱을 만들기 위해 PowerShell cmdlet의 Azure Resource Manager 버전을 사용하여 수행할 수 있습니다.

원본 앱을 포함하는 리소스 그룹 이름을 알고 있으면 다음 PowerShell 명령을 사용하여 원본 앱의 정보를 가져올 수 있습니다(이 경우 이름은 `source-webapp`임).

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

새 App Service 계획을 만들려면 다음 예제처럼 `New-AzAppServicePlan` 명령을 사용할 수 있습니다.

```powershell
New-AzAppServicePlan -Location "South Central US" -ResourceGroupName DestinationAzureResourceGroup -Name NewAppServicePlan -Tier Premium
```

`New-AzWebApp` 명령을 사용하여 미국 중북부 지역에 새 앱을 만든 후 기존 프리미엄 계층인 App Service 계획에 연결할 수 있습니다. 그뿐 아니라 원본 앱과 동일한 리소스 그룹을 사용하거나 다음 명령에 표시된 것처럼 새 리소스 그룹을 정의할 수 있습니다.

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp
```

연결된 모든 배포 슬롯을 포함하여 기존 앱을 복제하려면 `IncludeSourceWebAppSlots` 매개 변수를 사용해야 합니다. 다음 PowerShell 명령은 `New-AzWebApp` 명령에서 해당 매개 변수를 사용하는 방법을 보여 줍니다.

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -IncludeSourceWebAppSlots
```

동일한 지역 내에서 기존 앱을 복제하려면 동일한 지역에 새 리소스 그룹 및 새 App Service 계획을 만들고 다음 PowerShell 명령을 사용하여 앱을 복제해야 합니다.

```powershell
$destapp = New-AzWebApp -ResourceGroupName NewAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan NewAppServicePlan -SourceWebApp $srcap
```

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>기존 앱을 App Service Environment로 복제
시나리오: 미국 중남부 지역에 기존 앱이 있고, 기존 ASE(App Service Environment)에 있는 새 앱으로 콘텐츠를 복제하려고 합니다.

원본 앱을 포함하는 리소스 그룹 이름을 알고 있으면 다음 PowerShell 명령을 사용하여 원본 앱의 정보를 가져올 수 있습니다(이 경우 이름은 `source-webapp`임).

```powershell
$srcapp = Get-AzWebApp -ResourceGroupName SourceAzureResourceGroup -Name source-webapp
```

ASE의 이름 및 ASE가 속한 리소스 그룹 이름을 알고 있으면 다음 명령과 같이 기존 ASE에 새 앱을 만들 수 있습니다.

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "North Central US" -AppServicePlan DestinationAppServicePlan -ASEName DestinationASE -ASEResourceGroupName DestinationASEResourceGroupName -SourceWebApp $srcapp
```

`Location` 매개 변수는 레거시 이유로 인해 필요하지만 ASE에 앱을 만들 때는 무시됩니다. 

## <a name="cloning-an-existing-app-slot"></a>기존 앱 슬롯 복제
시나리오: 앱의 기존 배포 슬롯을 새 앱이나 새 슬롯에 복제하려고 합니다. 새 앱은 원래 앱 슬롯과 동일한 지역이나 다른 지역에 있을 수 있습니다.

원본 앱을 포함하는 리소스 그룹 이름을 알고 있으면 다음 PowerShell 명령을 사용하여 `source-app`에 연결된 원본 앱 슬롯의 정보를 가져올 수 있습니다(이 경우 이름은 `source-appslot`임).

```powershell
$srcappslot = Get-AzWebAppSlot -ResourceGroupName SourceAzureResourceGroup -Name source-app -Slot source-appslot
```

다음 명령에서는 새 앱에 원본 앱의 클론을 만드는 방법을 보여 줍니다.

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-app -Location "North Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcappslot
```

## <a name="configuring-traffic-manager-while-cloning-an-app"></a>앱을 복제하는 동한 Traffic Manager 구성
다중 지역 앱을 만들고 이러한 모든 앱으로 트래픽을 라우팅하도록 Azure Traffic Manager를 구성하는 것은 고객 앱의 고가용성을 유지하기 위한 중요한 시나리오입니다. 기존 앱을 복제하는 경우 두 앱을 새로운 Traffic Manager 프로필 또는 기존 프로필에 연결할 수 있는 옵션이 제공됩니다. Azure Resource Manager 버전의 Traffic Manager만 지원됩니다.

### <a name="creating-a-new-traffic-manager-profile-while-cloning-an-app"></a>앱을 복제하는 동안 새 Traffic Manager 프로필 만들기
시나리오: 두 앱을 모두 포함하는 Azure Resource Manager Traffic Manager 프로필을 구성하는 동안 다른 지역에 앱을 복제하려고 합니다. 다음 명령에서는 새 Traffic Manager 프로필을 구성하는 동안 새 앱으로 원본 앱의 클론을 만드는 방법을 보여 줍니다.

```powershell
$destapp = New-AzWebApp -ResourceGroupName DestinationAzureResourceGroup -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileName newTrafficManagerProfile
```

### <a name="adding-new-cloned-app-to-an-existing-traffic-manager-profile"></a>기존 Traffic Manager 프로필에 복제된 새 앱 추가
시나리오: Azure Resource Manager Traffic Manager 프로필이 이미 있으며 두 앱을 엔드포인트로 추가하려고 합니다. 이렇게 하려면 먼저 기존 Traffic Manager 프로필 ID를 조합해야 합니다. 구독 ID, 리소스 그룹 이름 및 기존 Traffic Manager 프로필 이름이 필요합니다.

```powershell
$TMProfileID = "/subscriptions/<Your subscription ID goes here>/resourceGroups/<Your resource group name goes here>/providers/Microsoft.TrafficManagerProfiles/ExistingTrafficManagerProfileName"
```

Traffic Manager ID를 알게 된 후 다음 명령에서는 원본 앱과 새 앱을 기존 Traffic Manager 프로필에 추가하는 동안 원본 앱의 클론을 새 앱에 만드는 방법을 보여 줍니다.

```powershell
$destapp = New-AzWebApp -ResourceGroupName <Resource group name> -Name dest-webapp -Location "South Central US" -AppServicePlan DestinationAppServicePlan -SourceWebApp $srcapp -TrafficManagerProfileId $TMProfileID
```

## <a name="current-restrictions"></a>현재 제한 사항
앱 복제에 대해 알려진 제한 사항은 다음과 같습니다.

* 자동 크기 설정은 복제되지 않습니다.
* Backup 일정 설정은 복제되지 않습니다.
* VNET 설정은 복제되지 않습니다.
* App Insights는 대상 앱에 자동으로 설치되지 않습니다.
* 간편한 인증 설정은 복제되지 않습니다.
* Kudu 확장은 복제되지 않습니다.
* TiP 규칙은 복제되지 않습니다.
* 데이터베이스 내용이 복제되지 않습니다.
* 다른 배율 단위로 복제하는 경우 아웃바운드 IP 주소가 변경됩니다.

### <a name="references"></a>참조
* [App Service 복제](app-service-web-app-cloning.md)
* [Azure App Service에서 앱 백업](manage-backup.md)
* [Azure Traffic Manager에 대한 Azure 리소스 관리자 지원 미리 보기](../traffic-manager/traffic-manager-powershell-arm.md)
* [App Service Environment 소개](environment/intro.md)
* [Azure 리소스 관리자로 Azure PowerShell 사용](../azure-resource-manager/manage-resources-powershell.md)

