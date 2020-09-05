---
title: 빠른 시작 - Azure 템플릿을 사용하여 Windows 가상 머신 확장 집합 만들기
description: 샘플 앱을 배포하고 자동 크기 조정 규칙을 구성하는 Azure Resource Manager 템플릿을 사용하여 Windows 가상 머신 확장 집합을 빠르게 만드는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.subservice: windows
ms.date: 03/27/2020
ms.reviewer: mimckitt
ms.custom: mimckitt, subject-armqs
ms.openlocfilehash: 03863171cb05c0557611fb8cd3c0ade667e7d79e
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88650080"
---
# <a name="quickstart-create-a-windows-virtual-machine-scale-set-with-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Windows 가상 머신 확장 집합 만들기

가상 머신 확장 집합을 사용하면 자동 크기 조정 가상 머신 세트를 배포하고 관리할 수 있습니다. 확장 집합의 VM 수를 수동으로 조정하거나 리소스 사용량(예: CPU, 메모리 요구량 또는 네트워크 트래픽)에 따라 자동으로 크기를 조정하는 규칙을 정의할 수 있습니다. 그러면 Azure 부하 분산 장치에서 확장 집합의 VM 인스턴스에 트래픽을 분산합니다. 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 가상 머신 확장 집합을 만들고, 샘플 애플리케이션을 배포합니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

ARM 템플릿을 사용하면 관련 리소스 그룹을 배포할 수 있습니다. 단일 템플릿에서 가상 머신 확장 집합을 만들고, 애플리케이션을 설치하고, 자동 크기 조정 규칙을 구성할 수 있습니다. 변수와 매개 변수를 사용하면 이 템플릿을 다시 사용하여 기존의 확장 집합을 업데이트하거나 추가 확장 집합을 만들 수 있습니다. Azure Portal, Azure CLI, Azure PowerShell을 통하거나 CI/CD(지속적인 통합/지속적인 업데이트) 파이프라인에서 템플릿을 배포할 수 있습니다.

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/201-vmss-windows-webapp-dsc-autoscale/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json":::

이러한 리소스는 다음 템플릿에 정의되어 있습니다.

- [**Microsoft.Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks)
- [**Microsoft.Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses)
- [**Microsoft.Network/loadBalancers**](/azure/templates/microsoft.network/loadbalancers)
- [**Microsoft.Compute/virtualMachineScaleSets**](/azure/templates/microsoft.compute/virtualmachinescalesets)
- [**Microsoft.Insights/autoscaleSettings**](/azure/templates/microsoft.insights/autoscalesettings)

### <a name="define-a-scale-set"></a>확장 집합 정의

템플릿을 사용하여 확장 집합을 만들려면 적절한 리소스를 정의합니다. 가상 머신 확장 집합 리소스 종류의 주요 부분은 다음과 같습니다.

| 속성                     | 속성 설명                                  | 예제 템플릿 값                    |
|------------------------------|----------------------------------------------------------|-------------------------------------------|
| type                         | 만들 Azure 리소스 종류                            | Microsoft.Compute/virtualMachineScaleSets |
| name                         | 확장 집합 이름                                       | myScaleSet                                |
| 위치                     | 확장 집합을 만들 위치                     | 미국 동부                                   |
| sku.name                     | 각 확장 집합 인스턴스에 대한 VM 크기                  | Standard_A1                               |
| sku.capacity                 | 처음에 만들 VM 인스턴스의 수           | 2                                         |
| upgradePolicy.mode           | 변경 발생 시의 VM 인스턴스 업그레이드 모드              | 자동                                 |
| imageReference               | VM 인스턴스에 사용할 플랫폼 또는 사용자 지정 이미지 | Microsoft Windows Server 2016 Datacenter  |
| osProfile.computerNamePrefix | 각 VM 인스턴스에 대한 이름 접두사                     | myvmss                                    |
| osProfile.adminUsername      | 각 VM 인스턴스에 대한 사용자 이름                        | azureuser                                 |
| osProfile.adminPassword      | 각 VM 인스턴스에 대한 암호                        | P@ssw0rd!                                 |

확장 집합 템플릿을 사용자 지정하려면 VM 크기 또는 초기 용량을 변경하면 됩니다. 다른 옵션은 다른 플랫폼 또는 사용자 지정 이미지를 사용하는 것입니다.

### <a name="add-a-sample-application"></a>샘플 애플리케이션 추가

확장 집합을 테스트하려면 기본 웹 애플리케이션을 설치합니다. 확장 집합을 배포하는 경우 VM 확장에서 배포 후 구성 및 자동화 작업(예: 응용 프로그램 설치)을 제공할 수 있습니다. 스크립트는 Azure Storage 또는 GitHub에서 다운로드하거나 확장 런타임에서 Azure Portal에 제공할 수 있습니다. 확장 집합에 확장을 적용하려면 앞의 리소스 예제에 *extensionProfile* 섹션을 추가합니다. 확장 프로필은 일반적으로 다음 속성을 정의합니다.

- 확장 형식
- 확장 게시자
- 확장 버전
- 구성 또는 설치 스크립트의 위치
- VM 인스턴스에서 실행할 명령

템플릿은 PowerShell DSC 확장을 사용하여 IIS에서 실행되는 ASP.NET MVC 앱을 설치합니다.

설치 스크립트는 *url*에서 정의한 대로 GitHub에서 다운로드됩니다. 그런 다음 확장이 *function* 및 *script*에서 정의한 대로 *IISInstall.ps1* 스크립트에서 *InstallIIS*를 실행합니다. ASP.NET 앱 자체는 *WebDeployPackagePath*에서 정의한 대로 GitHub에서 다운로드되는 웹 배포 패키지로 제공됩니다.

## <a name="deploy-the-template"></a>템플릿 배포

**Azure에 배포** 단추를 선택하여 템플릿을 배포할 수 있습니다. 이 단추는 Azure Portal을 열고, 전체 템플릿을 로드하고, 몇 가지 매개 변수(예: 확장 집합 이름, 인스턴스 수 및 관리자 자격 증명)를 요구하는 메시지를 표시합니다.

[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-vmss-windows-webapp-dsc-autoscale%2Fazuredeploy.json)

Azure PowerShell을 사용하여 Resource Manager 템플릿을 배포할 수도 있습니다.

```azurepowershell-interactive
# Create a resource group
New-AzResourceGroup -Name myResourceGroup -Location EastUS

# Deploy template into resource group
New-AzResourceGroupDeployment `
    -ResourceGroupName myResourceGroup `
    -TemplateURI https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-windows-webapp-dsc-autoscale/azuredeploy.json

# Update the scale set and apply the extension
Update-AzVmss `
    -ResourceGroupName myResourceGroup `
    -VmScaleSetName myVMSS `
    -VirtualMachineScaleSet $vmssConfig
```

프롬프트에 응답하여 VM 인스턴스에 대한 확장 집합 이름 및 관리자 자격 증명을 제공합니다. 확장 집합을 만들고 앱을 구성하기 위해 확장을 적용하는 데 10-15분 정도 걸릴 수 있습니다.

## <a name="validate-the-deployment"></a>배포 유효성 검사

작동 중인 확장 집합을 확인하려면 웹 브라우저에서 샘플 웹 애플리케이션에 액세스합니다. 다음과 같이 [Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다.

```azurepowershell-interactive
Get-AzPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

웹 브라우저에 부하 분산 장치의 공용 IP 주소를 *http:\//publicIpAddress/MyApp* 형식으로 입력합니다. 부하 분산 장치는 다음 예제와 같이 VM 인스턴스 중 하나에 트래픽을 분산합니다.

![실행 중인 IIS 사이트](./media/virtual-machine-scale-sets-create-powershell/running-iis-site.png)

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹과 확장 집합을 제거할 수 있습니다. `-Force` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다. `-AsJob` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup" -Force -AsJob
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 Windows 확장 집합을 만들고, PowerShell DSC 확장을 사용하여 VM 인스턴스에 기본 ASP.NET 앱을 설치했습니다. 더 자세히 알아보려면 Azure 가상 머신 확장 집합을 만들고 관리하는 방법에 대한 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure 가상 머신 확장 집합 만들기 및 관리](tutorial-create-and-manage-powershell.md)
