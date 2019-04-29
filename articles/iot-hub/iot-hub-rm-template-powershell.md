---
title: 템플릿을 사용하여 Azure IoT Hub 만들기(PowerShell) | Microsoft Docs
description: Azure PowerShell을 사용 하 여 IoT Hub를 만드는 Azure Resource Manager 템플릿을 사용 하는 방법입니다.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/02/2019
ms.openlocfilehash: eefa17b699a0599d2d3323409cafddd1d0984c61
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61440321"
---
# <a name="create-an-iot-hub-using-azure-resource-manager-template-powershell"></a>Azure Resource Manager 템플릿을 사용하여 IoT Hub 만들기(PowerShell)

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Azure Resource Manager 템플릿을 사용 하 여 IoT Hub 및 소비자 그룹을 만드는 방법에 알아봅니다. Resource Manager 템플릿은 솔루션에 배포해야 하는 리소스를 정의하는 JSON 파일입니다. Resource Manager 템플릿을 개발 하는 방법에 대 한 자세한 내용은 참조 하십시오 [Azure Resource Manager 설명서](https://docs.microsoft.com/azure/azure-resource-manager/)합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="create-an-iot-hub"></a>IoT Hub 만들기

이 빠른 시작에 사용 되는 Resource Manager 템플릿 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-iothub-with-consumergroup-create/)합니다. 다음은 템플릿의 복사본입니다.

[!code-json[iothub-creation](~/quickstart-templates/101-iothub-with-consumergroup-create/azuredeploy.json)]

템플릿은 세 개의 끝점 (eventhub, 클라우드-장치 및 메시징) 및 소비자 그룹을 사용 하 여 Azure Iot hub를 만듭니다. 추가 템플릿 샘플을 참조 하세요 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Devices&pageNumber=1&sort=Popular)합니다. Iot Hub 템플릿 스키마를 찾을 수 있습니다 [여기](https://docs.microsoft.com/azure/templates/microsoft.devices/iothub-allversions)합니다.

템플릿을 배포 하는 방법은 여러 가지가 있습니다.  이 자습서에서는 Azure PowerShell을 사용합니다.

PowerShell 스크립트를 실행 하려면 **사용해** 를 Azure Cloud shell을 엽니다. 스크립트를 붙여 넣으려면 셸을 마우스 오른쪽 단추로 클릭 하 고 붙여넣기를 선택 합니다.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$iotHubName = Read-Host -Prompt "Enter the IoT Hub name"

New-AzResourceGroup -Name $resourceGroupName -Location "$location"
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-iothub-with-consumergroup-create/azuredeploy.json" `
    -iotHubName $iotHubName
```

PowerShell 스크립트에서 보이는 것 처럼 Azure 빠른 시작 템플릿에서 사용 되는 템플릿이 됩니다. 사용자 고유의 사용 하려면 먼저 Cloud shell에 템플릿 파일을 업로드 하 고 사용 하 여는 `-TemplateFile` 파일 이름을 지정 하는 스위치입니다.  예를 들어 참조 [템플릿을 배포](../azure-resource-manager/resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell#deploy-the-template)합니다.

## <a name="next-steps"></a>다음 단계

Azure Resource Manager 템플릿을 사용 하 여 IoT hub를 배포 했으므로 구체적인 내용을 알아볼 차례 하는 것이 좋습니다.

* [IoT Hub 리소스 공급자 REST API][lnk-rest-api]의 기능을 읽어보세요.
* Azure Resource Manager의 기능에 대해 자세히 알아보려면 [Azure Resource Manager 개요][lnk-azure-rm-overview]를 참조하세요.
* 템플릿에서 사용할 JSON 구문 및 속성은 [Microsoft.Devices 리소스 종류](/azure/templates/microsoft.devices/iothub-allversions)를 참조하세요.

IoT Hub를 개발하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [C SDK 소개][lnk-c-sdk]
* [Azure IoT SDK][lnk-sdks]

IoT Hub의 기능을 추가로 탐색하려면 다음을 참조하세요.

* [Azure IoT Edge를 사용하여 Edge 디바이스에 AI 배포][lnk-iotedge]

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-status]: https://azure.microsoft.com/status/
[lnk-powershell-install]: /powershell/azure/install-Az-ps
[lnk-rest-api]: https://docs.microsoft.com/rest/api/iothub/iothubresource
[lnk-azure-rm-overview]: ../azure-resource-manager/resource-group-overview.md
[lnk-powershell-arm]: ../azure-resource-manager/manage-resources-powershell.md

[lnk-c-sdk]: iot-hub-device-sdk-c-intro.md
[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
