---
title: Azure 가상 머신 확장 집합에 애플리케이션 배포 | Microsoft Docs
description: Linux 및 Windows 가상 머신 인스턴스의 확장 집합에 애플리케이션을 배포하는 방법을 알아봅니다.
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: f8892199-f2e2-4b82-988a-28ca8a7fd1eb
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2018
ms.author: cynthn
ms.openlocfilehash: 09145612821cb669e26e3ccb8d15611112eca700
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618628"
---
# <a name="deploy-your-application-on-virtual-machine-scale-sets"></a>가상 머신 확장 세트에 애플리케이션 배포

확장 집합의 VM(가상 머신) 인스턴스에서 애플리케이션을 실행하려면 먼저 애플리케이션 구성 요소 및 필요한 파일을 설치해야 합니다. 이 문서에서는 확장 집합의 인스턴스에 대한 사용자 지정 VM 이미지를 빌드하거나 기존 VM 인스턴스에 설치 스크립트를 자동으로 실행하는 방법을 소개합니다. 또한 확장 집합 전체에서 애플리케이션 또는 OS 업데이트를 관리하는 방법도 알아봅니다.


## <a name="build-a-custom-vm-image"></a>사용자 지정 VM 이미지 빌드
Azure 플랫폼 이미지 중 하나를 사용하여 확장 집합에서 인스턴스를 만드는 경우 소프트웨어가 추가로 설치되거나 구성되지 않습니다. 그러나 이러한 구성 요소의 설치를 자동화할 수는 있지만 VM 인스턴스를 확장 집합에 프로비전하는 데 걸리는 시간이 늘어납니다. VM 인스턴스에 구성 변경을 많이 적용하는 경우 이러한 구성 스크립트 및 작업에는 관리 오버헤드가 있습니다.

구성 관리 및 VM 프로비전 시간을 줄이기 위해 인스턴스가 확장 집합에 프로비전되는 즉시 애플리케이션을 실행할 준비가 된 사용자 지정 VM 이미지를 만들 수 있습니다. 확장 집합에서 사용자 지정 VM 이미지를 만들고 사용하는 방법에 대한 자세한 내용은 다음 자습서를 참조하세요.

- [Azure CLI](tutorial-use-custom-image-cli.md)
- [Azure PowerShell](tutorial-use-custom-image-powershell.md)


## <a name="already-provisioned"></a>사용자 지정 스크립트 확장을 사용하여 앱 설치
사용자 지정 스크립트 확장은 Azure VM에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 스크립트는 Azure 저장소 또는 GitHub에서 다운로드하거나 확장 런타임에서 Azure Portal에 제공할 수 있습니다. 확장 집합에서 사용자 지정 VM 이미지를 만들고 사용하는 방법에 대한 자세한 내용은 다음 자습서를 참조하세요.

- [Azure CLI](tutorial-install-apps-cli.md)
- [Azure PowerShell](tutorial-install-apps-powershell.md)
- [Azure Resource Manager 템플릿](tutorial-install-apps-template.md)


## <a name="install-an-app-to-a-windows-vm-with-powershell-dsc"></a>PowerShell DSC를 사용하여 Windows VM에 앱 설치
[PowerShell DSC(Desired State Configuration)](https://msdn.microsoft.com/powershell/dsc/overview)는 대상 컴퓨터의 구성을 정의하는 관리 플랫폼입니다. DSC 구성은 컴퓨터에 설치할 항목과 호스트를 구성하는 방법을 정의합니다. LCM(로컬 구성 관리자) 엔진은 푸시된 구성에 따라 요청된 작업을 처리하는 각 대상 노드에서 실행됩니다.

PowerShell DSC 확장을 사용하면 PowerShell을 통해 확장 집합의 VM 인스턴스를 사용자 지정할 수 있습니다. 다음 예제를 참조하세요.

- VM 인스턴스가 GitHub에서 DSC 패키지를 다운로드하도록 지시합니다(*https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip*).
- 설치 스크립트를 실행하도록 확장을 설정합니다(`configure-http.ps1`).
- [Get-AzVmss](/powershell/module/az.compute/get-azvmss)를 사용하여 확장 집합에 대한 정보를 가져옵니다.
- [Update-AzVmss](/powershell/module/az.compute/update-azvmss)를 사용하여 VM 인스턴스에 확장을 적용합니다.

DSC 확장은 *myResourceGroup*이라는 리소스 그룹의 *myScaleSet* VM 인스턴스에 적용됩니다. 다음과 같이 고유한 이름을 입력합니다.

```powershell
# Define the script for your Desired Configuration to download and run
$dscConfig = @{
  "wmfVersion" = "latest";
  "configuration" = @{
    "url" = "https://github.com/Azure-Samples/compute-automation-configurations/raw/master/dsc.zip";
    "script" = "configure-http.ps1";
    "function" = "WebsiteTest";
  };
}

# Get information about the scale set
$vmss = Get-AzVmss `
                -ResourceGroupName "myResourceGroup" `
                -VMScaleSetName "myScaleSet"

# Add the Desired State Configuration extension to install IIS and configure basic website
$vmss = Add-AzVmssExtension `
    -VirtualMachineScaleSet $vmss `
    -Publisher Microsoft.Powershell `
    -Type DSC `
    -TypeHandlerVersion 2.24 `
    -Name "DSC" `
    -Setting $dscConfig

# Update the scale set and apply the Desired State Configuration extension to the VM instances
Update-AzVmss `
    -ResourceGroupName "myResourceGroup" `
    -Name "myScaleSet"  `
    -VirtualMachineScaleSet $vmss
```

확장 집합에 대한 업그레이드 정책이 *수동*이면 [Update-AzVmssInstance](/powershell/module/az.compute/update-azvmssinstance)를 사용하여 VM 인스턴스를 업데이트합니다. 이 cmdlet은 업데이트된 확장 집합 구성을 VM 인스턴스에 적용하고 애플리케이션을 설치합니다.


## <a name="install-an-app-to-a-linux-vm-with-cloud-init"></a>cloud-init를 사용하여 Linux VM에 앱 설치
[Cloud-init](https://cloudinit.readthedocs.io/en/latest/index.html)는 처음 부팅 시 Linux VM을 사용자 지정하는 데 널리 사용되는 방법입니다. Cloud-init를 사용하여 패키지를 설치하고 파일을 쓰거나, 사용자 및 보안을 구성할 수 있습니다. 초기 부팅 프로세스 중에 cloud-init가 실행되면 구성을 적용하기 위한 추가 단계나 필요한 에이전트가 없습니다.

Cloud-init는 배포에서도 작동합니다. 예를 들어, 패키지를 설치하는 데 **apt-get install** 또는 **yum install**은 사용하지 않습니다. 대신 설치할 패키지 목록을 정의할 수 있습니다. cloud-init에서 선택한 배포판의 기본 패키지 관리 도구를 자동으로 사용합니다.

*cloud-init.txt* 예제 파일을 포함한 자세한 내용은 [cloud-init를 사용하여 Azure VM 사용자 지정](../virtual-machines/linux/using-cloud-init.md)을 참조하세요.

확장 집합을 만들고 cloud-init 파일을 사용하려면 `--custom-data` 매개 변수를 [az vmss create](/cli/azure/vmss) 명령에 추가하고 cloud-init 파일의 이름을 지정합니다. 다음 예제에서는 *myResourceGroup*에 *myScaleSet*라는 확장 집합을 만들고, *cloud-init.txt* 파일을 사용하여 VM 인스턴스를 구성합니다. 다음과 같이 고유한 이름을 입력합니다.

```azurecli
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --custom-data cloud-init.txt \
  --admin-username azureuser \
  --generate-ssh-keys
```


### <a name="install-applications-with-os-updates"></a>OS 업데이트를 사용하여 애플리케이션 설치
새 OS 릴리스를 사용할 수 있는 경우 새 사용자 지정 이미지를 사용하거나 빌드하고 확장 집합에 [OS 업그레이드를 배포](virtual-machine-scale-sets-upgrade-scale-set.md)할 수 있습니다. 각 VM 인스턴스는 지정한 최신 이미지로 업그레이드됩니다. 미리 설치된 애플리케이션이 있는 사용자 지정 이미지, 사용자 지정 스크립트 확장 또는 PowerShell DSC를 사용하여 업그레이드를 수행할 때 애플리케이션을 자동으로 사용할 수 있습니다. 버전 호환성 문제가 없는지 확인하기 위해 이 프로세스를 수행할 때 애플리케이션 유지 관리를 계획해야 합니다.

미리 설치된 애플리케이션이 있는 사용자 지정 VM 이미지를 사용하는 경우, 애플리케이션 업데이트를 배포 파이프라인과 통합하여 새 이미지를 빌드하고 확장 집합 전체에 OS 업그레이드를 배포할 수 있습니다. 이 방법을 사용하면 파이프라인에서 최신 애플리케이션 빌드를 선택하고, VM 이미지를 만들고 유효성을 검사한 다음, 확장 집합의 VM 인스턴스를 업그레이드할 수 있습니다. 사용자 지정 VM 이미지에서 애플리케이션 업데이트를 빌드하고 배포하는 배포 파이프라인을 실행하려면 [Packer 이미지를 만들어 Azure DevOps Services를 사용하여 배포하거나](/azure/devops/pipelines/apps/cd/azure/deploy-azure-scaleset), [Spinnaker](https://www.spinnaker.io/) 또는 [Jenkins](https://jenkins.io/)와 같은 다른 플랫폼을 사용할 수 있습니다.


## <a name="next-steps"></a>다음 단계
확장 집합에 애플리케이션을 빌드하고 배포할 때 [확장 집합 디자인 개요](virtual-machine-scale-sets-design-overview.md)를 검토할 수 있습니다. 확장 집합을 관리하는 방법에 대한 자세한 내용은 [PowerShell을 사용하여 확장 집합 관리](virtual-machine-scale-sets-windows-manage.md)를 참조하세요.
