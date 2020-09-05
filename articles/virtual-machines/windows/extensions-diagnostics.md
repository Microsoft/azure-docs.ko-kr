---
title: Windows 용 Azure 진단 확장
description: Azure Diagnostics 확장을 사용하여 Azure Windows VM 모니터링
author: johnkemnetz
manager: ashwink
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/06/2018
ms.author: johnkem
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: c981214c69653b3a4a687a861cb348f3587def7c
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89078761"
---
# <a name="azure-diagnostics-extension-for-windows-vms"></a>Windows VM용 Azure Diagnostics 확장

## <a name="overview"></a>개요

Azure Diagnostics VM 확장을 사용하면 Windows VM에서 모니터링 데이터(예: 성능 카운터 및 이벤트 로그)를 수집할 수 있습니다. 수집하려는 데이터와 데이터를 저장할 위치(예: Azure Storage 계정 또는 Azure Event Hub)를 세분화하여 지정할 수 있습니다. 이 데이터를 사용하여 Azure Portal에서 차트를 작성하거나 메트릭 경고를 만들 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

### <a name="operating-system"></a>운영 체제

Azure Diagnostics 확장은 Windows 10 Client, Windows Server 2008 R2, 2012, 2012 R2 및 2016에 대해 실행할 수 있습니다.

### <a name="internet-connectivity"></a>인터넷 연결

Azure Diagnostics 확장은 대상 가상 머신이 인터넷에 연결되어 있어야 합니다. 

## <a name="extension-schema"></a>확장 스키마

[Azure Diagnostics 확장 스키마 및 속성 값은 이 문서에 설명되어 있습니다.](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)

## <a name="template-deployment"></a>템플릿 배포

Azure Resource Manager 템플릿을 사용하여 Azure VM 확장을 배포할 수 있습니다. 이전 섹션에서 자세히 설명되어 있는 JSON 스키마는 Azure Resource Manager 템플릿에서 사용하여 Azure Resource Manager 템플릿 배포 중 Azure Diagnostics 확장을 실행할 수 있습니다. [Windows VM 및 Azure Resource Manager 템플릿을 사용하여 모니터링 및 진단 사용](../extensions/diagnostics-template.md)을 참조하세요.

## <a name="azure-cli-deployment"></a>Azure CLI 배포

Azure CLI를 사용하여 Azure Diagnostics 확장을 기존 가상 머신에 배포할 수 있습니다. 보호된 설정 및 설정 속성을 위의 확장 스키마의 유효한 JSON으로 바꿉니다. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --version 1.9.0.0 --protected-settings protected-settings.json \
  --settings public-settings.json 
```

## <a name="powershell-deployment"></a>PowerShell 배포

`Set-AzVMDiagnosticsExtension` 명령을 사용하여 Azure Diagnostics 확장을 기존 가상 머신에 추가할 수 있습니다. [PowerShell을 사용하여 Windows를 실행하는 가상 머신에서 Azure Diagnostics를 사용하도록 설정](../extensions/diagnostics-windows.md)을 참조하세요.

 


```powershell
$vm_resourcegroup = "myvmresourcegroup"
$vm_name = "myvm"
$diagnosticsconfig_path = "DiagnosticsPubConfig.xml"

Set-AzVMDiagnosticsExtension -ResourceGroupName $vm_resourcegroup `
  -VMName $vm_name `
  -DiagnosticsConfigurationPath $diagnosticsconfig_path
```

## <a name="troubleshoot-and-support"></a>문제 해결 및 지원

### <a name="troubleshoot"></a>문제 해결

확장 배포 상태에 대한 데이터는 Azure CLI를 사용하여 Azure Portal에서 검색할 수 있습니다. 지정된 VM에 대한 확장의 배포 상태를 보려면 Azure CLI를 사용하여 다음 명령을 실행합니다.

```azurecli
az vm extension list --resource-group myResourceGroup --vm-name myVM -o table
```

Azure Diagnostics 확장에 대한 보다 포괄적인 문제 해결 가이드는 [이 문서를 참조](../../azure-monitor/platform/diagnostics-extension-troubleshooting.md)하세요.

### <a name="support"></a>지원

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/)로 가서 지원 받기를 선택합니다. Azure 지원을 사용하는 방법에 대한 자세한 내용은 [Microsoft Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
* [Azure Diagnostics 확장에 대해 자세히 알아보기](../../azure-monitor/platform/diagnostics-extension-overview.md)
* [확장 스키마 및 버전 검토](../../azure-monitor/platform/diagnostics-extension-schema-windows.md)
