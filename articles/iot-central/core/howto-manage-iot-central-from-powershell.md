---
title: Azure PowerShell에서 IoT Central 관리 | Microsoft Docs
description: 이 문서에서는 Azure PowerShell에서 IoT Central 응용 프로그램을 만들고 관리 하는 방법을 설명 합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
manager: philmea
ms.openlocfilehash: 77327689911a0fc36df14f69ef4d48e5cb120375
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89067759"
---
# <a name="manage-iot-central-from-azure-powershell"></a>Azure PowerShell에서 IoT Central 관리

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

[Azure IoT Central application manager](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 응용 프로그램을 만들고 관리 하는 대신 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/) 를 사용 하 여 응용 프로그램을 관리할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

로컬 머신에서 Azure PowerShell을 실행하려면 [Azure PowerShell 모듈 설치](https://docs.microsoft.com/powershell/azure/install-az-ps)를 참조하세요. 로컬에서 Azure PowerShell을 실행하는 경우 이 문서의 cmdlet을 사용하기 전에 **Connect-AzAccount** cmdlet을 사용하여 Azure에 로그인합니다.

> [!TIP]
> 다른 Azure 구독에서 PowerShell 명령을 실행 해야 하는 경우 [활성 구독 변경](/powershell/azure/manage-subscriptions-azureps?view=azps-3.4.0#change-the-active-subscription)을 참조 하세요.

## <a name="install-the-iot-central-module"></a>IoT Central 모듈 설치

다음 명령을 실행하여 PowerShell 환경에 [IoT Central 모듈](https://docs.microsoft.com/powershell/module/az.iotcentral/)이 설치되었는지 확인합니다.

```powershell
Get-InstalledModule -name Az.I*
```

설치된 모듈 목록에 **Az.IotCentral**이 포함되지 않은 경우 다음 명령을 실행합니다.

```powershell
Install-Module Az.IotCentral
```

## <a name="create-an-application"></a>애플리케이션 만들기

[New-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/New-AzIotCentralApp) cmdlet을 사용하여 Azure 구독에 IoT Central 애플리케이션을 만듭니다. 예를 들면 다음과 같습니다.

```powershell
# Create a resource group for the IoT Central application
New-AzResourceGroup -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Location "East US"
```

```powershell
# Create an IoT Central application
New-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
  -Name "myiotcentralapp" -Subdomain "mysubdomain" `
  -Sku "ST1" -Template "iotc-pnp-preview" `
  -DisplayName "My Custom Display Name"
```

스크립트는 먼저 애플리케이션의 미국 동부 지역에 리소스 그룹을 만듭니다. 다음 표에서는 **New-AzIotCentralApp** 명령에 사용되는 매개 변수를 설명합니다.

|매개 변수         |Description |
|------------------|------------|
|ResourceGroupName |애플리케이션을 포함하는 리소스 그룹입니다. 리소스 그룹이 구독에 이미 있어야 합니다. |
|위치 |기본적으로 이 cmdlet은 리소스 그룹의 위치를 사용합니다. 현재 **오스트레일리아**, **아시아 태평양**, **유럽** **, 미국, 영국**및 **일본** 지역에서 IoT Central 응용 **프로그램을 만들**수 있습니다. |
|이름              |Azure Portal의 애플리케이션 이름입니다. |
|하위 도메인         |애플리케이션 URL의 하위 도메인입니다. 예제에서 애플리케이션 URL은 `https://mysubdomain.azureiotcentral.com`입니다. |
|SKU               |현재 **ST1** 또는 **ST2**중 하나를 사용할 수 있습니다. [Azure IoT Central 가격 책정](https://azure.microsoft.com/pricing/details/iot-central/)을 참조하세요. |
|템플릿          | 사용할 애플리케이션 템플릿입니다. 자세한 내용은 다음 표를 참조하세요. |
|DisplayName       |UI에 표시되는 애플리케이션 이름입니다. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-iot-central-applications"></a>IoT Central 애플리케이션 보기

[Get-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Get-AzIotCentralApp) cmdlet을 사용하여 IoT Central 애플리케이션을 나열하고 메타데이터를 봅니다.

## <a name="modify-an-application"></a>애플리케이션 수정

[Set-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/set-aziotcentralapp) cmdlet을 사용하여 IoT Central 애플리케이션의 메타데이터를 업데이트합니다. 예를 들어 애플리케이션의 표시 이름을 변경합니다.

```powershell
Set-AzIotCentralApp -Name "myiotcentralapp" `
  -ResourceGroupName "MyIoTCentralResourceGroup" `
  -DisplayName "My new display name"
```

## <a name="remove-an-application"></a>애플리케이션 제거

[Remove-AzIotCentralApp](https://docs.microsoft.com/powershell/module/az.iotcentral/Remove-AzIotCentralApp) cmdlet을 사용하여 IoT Central 애플리케이션을 삭제합니다. 예를 들면 다음과 같습니다.

```powershell
Remove-AzIotCentralApp -ResourceGroupName "MyIoTCentralResourceGroup" `
 -Name "myiotcentralapp"
```

## <a name="next-steps"></a>다음 단계

이제 Azure PowerShell에서 Azure IoT Central 애플리케이션을 관리하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
