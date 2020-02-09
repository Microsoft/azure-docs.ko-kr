---
title: Azure Vm에서 성능 모니터링-Azure 애플리케이션 정보
description: Azure VM 및 Azure virtual machine scale sets에 대 한 응용 프로그램 성능 모니터링. 차트 로드 및 응답 시간, 종속성 정보 및 성능에 대 한 경고를 설정 합니다.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 08/26/2019
ms.openlocfilehash: 2fdd07d01e6bb1258a3f2ae2e856e440e5ed2818
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75407347"
---
# <a name="deploy-the-azure-monitor-application-insights-agent-on-azure-virtual-machines-and-azure-virtual-machine-scale-sets"></a>Azure virtual machines 및 Azure virtual machine scale sets에 Azure Monitor Application Insights 에이전트 배포

이제 [azure virtual machines](https://azure.microsoft.com/services/virtual-machines/) 및 [azure 가상 머신 확장 집합](https://docs.microsoft.com/azure/virtual-machine-scale-sets/) 에서 실행 되는 .net 기반 웹 응용 프로그램에 대 한 모니터링을 사용 하도록 설정 하는 것이 더 쉬워졌습니다. 코드를 수정 하지 않고 Application Insights를 사용 하는 모든 혜택을 받으세요.

이 문서에서는 Application Insights 에이전트를 사용 하 여 Application Insights 모니터링을 사용 하도록 설정 하는 과정을 안내 하 고 대규모 배포 프로세스를 자동화 하기 위한 예비 지침을 제공 합니다.

> [!IMPORTANT]
> Azure 애플리케이션 Insights Agent for .NET은 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 권장 되지 않습니다. 일부 기능은 지원 되지 않을 수 있으며 일부 기능은 제한 된 기능을 가질 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="enable-application-insights"></a>Application Insights 사용

Azure 가상 머신과 Azure virtual machine scale sets 호스팅된 응용 프로그램에 대해 응용 프로그램 모니터링을 사용 하도록 설정 하는 방법에는 두 가지가 있습니다.

* Application Insights 에이전트를 통한 **코드 없는**
    * 이 방법은 사용 하기 가장 쉽고 고급 구성이 필요 하지 않습니다. 이를 종종 "런타임" 모니터링 이라고 합니다.

    * Azure virtual machines 및 Azure virtual machine scale sets의 경우이 모니터링 수준을 최소한으로 설정 하는 것이 좋습니다. 그런 다음 특정 시나리오에 따라 수동 계측이 필요한 지 여부를 평가할 수 있습니다.

    * Application Insights 에이전트는 .NET SDK와 동일한 종속성 신호를 자동으로 수집 합니다. 자세히 알아보려면 [종속성 자동 수집](https://docs.microsoft.com/azure/azure-monitor/app/auto-collect-dependencies#net) 을 참조 하세요.
        > [!NOTE]
        > 현재 .Net IIS에서 호스트 되는 응용 프로그램만 지원 됩니다. SDK를 사용 하 여 Azure 가상 머신과 가상 머신 확장 집합에서 호스트 되는 ASP.NET Core, Java 및 node.js 응용 프로그램을 계측 합니다.

* SDK **를 통한 코드 기반**

    * 이 방법은 훨씬 더 사용자 지정이 가능 하지만 [APPLICATION INSIGHTS SDK NuGet 패키지에 대 한 종속성을 추가](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)해야 합니다. 또한이 메서드는 최신 버전의 패키지에 대 한 업데이트를 직접 관리 해야 함을 의미 합니다.

    * 에이전트 기반 모니터링을 사용 하 여 기본적으로 캡처되지 않는 이벤트/종속성을 추적 하기 위해 사용자 지정 API 호출을 수행 해야 하는 경우이 방법을 사용 해야 합니다. 자세한 내용은 [사용자 지정 이벤트 및 메트릭 용 API 문서](https://docs.microsoft.com/azure/azure-monitor/app/api-custom-events-metrics) 를 확인 하세요.

> [!NOTE]
> 에이전트 기반 모니터링과 수동 SDK 기반 계측이 모두 검색 된 경우에는 수동 계측 설정만 검색 됩니다. 이는 중복 데이터가 전송 되지 않도록 방지 하기 위한 것입니다. 이에 대 한 자세한 내용은 아래의 [문제 해결 섹션](#troubleshooting) 을 확인 하세요.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machines-using-powershell"></a>PowerShell을 사용 하 여 Azure virtual machines에서 .NET 응용 프로그램에 대 한 Application Insights 에이전트 관리

> [!NOTE]
> Application Insights 에이전트를 설치 하기 전에 연결 문자열이 필요 합니다. [새 Application Insights 리소스를 만들거나](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) 기존 application Insights 리소스에서 연결 문자열을 복사 합니다.

> [!NOTE]
> Powershell을 처음 접하는 가요? [시작 가이드](https://docs.microsoft.com/powershell/azure/get-started-azureps?view=azps-2.5.0)를 확인 하세요.

Azure 가상 컴퓨터에 대 한 확장으로 Application Insights 에이전트 설치 또는 업데이트
```powershell
$publicCfgJsonString = '
{
  "redfieldConfiguration": {
    "instrumentationKeyMap": {
      "filters": [
        {
          "appFilter": ".*",
          "machineFilter": ".*",
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          }
        }
      ]
    }
  }
}
';
$privateCfgJsonString = '{}';

Set-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Location "<myVmLocation>" -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -Version "2.8" -SettingString $publicCfgJsonString -ProtectedSettingString $privateCfgJsonString
```

> [!NOTE]
> Powershell 루프를 사용 하 여 여러 Virtual Machines의 확장으로 Application Insights 에이전트를 설치 하거나 업데이트할 수 있습니다.

Azure 가상 머신에서 Application Insights 에이전트 확장 제거
```powershell
Remove-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name "ApplicationMonitoring"
```

Azure 가상 컴퓨터에 대 한 쿼리 Application Insights 에이전트 확장 상태
```powershell
Get-AzVMExtension -ResourceGroupName "<myVmResourceGroup>" -VMName "<myVmName>" -Name ApplicationMonitoring -Status
```

Azure 가상 컴퓨터의 설치 된 확장 목록 가져오기
```powershell
Get-AzResource -ResourceId "/subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions"

# Name              : ApplicationMonitoring
# ResourceGroupName : <myVmResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachines/extensions
# Location          : southcentralus
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myVmResourceGroup>/providers/Microsoft.Compute/virtualMachines/<myVmName>/extensions/ApplicationMonitoring
```
포털의 [Azure virtual machine 블레이드에서](https://docs.microsoft.com/azure/virtual-machines/extensions/overview) 설치 된 확장을 볼 수도 있습니다.

> [!NOTE]
> Application Insights 에이전트 확장을 배포 하는 데 사용한 연결 문자열과 연결 된 Application Insights 리소스 내의 라이브 메트릭 스트림를 클릭 하 여 설치를 확인 합니다. 여러 Virtual Machines에서 데이터를 보내는 경우 서버 이름 아래에서 대상 Azure Virtual Machines를 선택 합니다. 데이터 흐름이 시작 되는 데 최대 1 분 정도 걸릴 수 있습니다.

## <a name="manage-application-insights-agent-for-net-applications-on-azure-virtual-machine-scale-sets-using-powershell"></a>Powershell을 사용 하 여 Azure 가상 머신 확장 집합에서 .NET 응용 프로그램에 대 한 Application Insights 에이전트 관리

Azure 가상 머신 확장 집합에 대 한 확장으로 Application Insights 에이전트 설치 또는 업데이트
```powershell
$publicCfgHashtable =
@{
  "redfieldConfiguration"= @{
    "instrumentationKeyMap"= @{
      "filters"= @(
        @{
          "appFilter"= ".*";
          "machineFilter"= ".*";
          "virtualPathFilter": ".*",
          "instrumentationSettings" : {
            "connectionString": "InstrumentationKey=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" # Application Insights connection string, create new Application Insights resource if you don't have one. https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/microsoft.insights%2Fcomponents
          }
        }
      )
    }
  }
};
$privateCfgHashtable = @{};

$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring" -Publisher "Microsoft.Azure.Diagnostics" -Type "ApplicationMonitoringWindows" -TypeHandlerVersion "2.8" -Setting $publicCfgHashtable -ProtectedSetting $privateCfgHashtable

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure 가상 머신 확장 집합에서 응용 프로그램 모니터링 확장 제거
```powershell
$vmss = Get-AzVmss -ResourceGroupName "<myResourceGroup>" -VMScaleSetName "<myVmssName>"

Remove-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ApplicationMonitoring"

Update-AzVmss -ResourceGroupName $vmss.ResourceGroupName -Name $vmss.Name -VirtualMachineScaleSet $vmss

# Note: depending on your update policy, you might need to run Update-AzVmssInstance for each instance
```

Azure 가상 머신 확장 집합에 대 한 응용 프로그램 모니터링 확장 상태 쿼리
```powershell
# Not supported by extensions framework
```

Azure 가상 머신 확장 집합에 대해 설치 된 확장 목록 가져오기
```powershell
Get-AzResource -ResourceId /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions

# Name              : ApplicationMonitoringWindows
# ResourceGroupName : <myResourceGroup>
# ResourceType      : Microsoft.Compute/virtualMachineScaleSets/extensions
# Location          :
# ResourceId        : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.Compute/virtualMachineScaleSets/<myVmssName>/extensions/ApplicationMonitoringWindows
```

## <a name="troubleshooting"></a>문제 해결

Azure 가상 머신 및 가상 머신 확장 집합에서 실행 되는 .NET 응용 프로그램에 대 한 Application Insights 모니터링 에이전트 확장에 대 한 문제 해결 팁을 확인 하세요.

> [!NOTE]
> .NET Core, Java 및 node.js 응용 프로그램은 수동 SDK 기반 계측을 통해 Azure virtual machines 및 Azure virtual machine scale sets 에서만 지원 되므로 아래 단계는 이러한 시나리오에 적용 되지 않습니다.

확장 실행 출력은 다음 디렉터리에 있는 파일에 기록됩니다.
```Windows
C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.ApplicationMonitoringWindows\<version>\
```

## <a name="next-steps"></a>다음 단계
* [Azure 가상 머신 확장 집합에 응용 프로그램을 배포](../../virtual-machine-scale-sets/virtual-machine-scale-sets-deploy-app.md)하는 방법을 알아봅니다.
* 엔드포인트이 종료 된 경우 경고를 표시 하도록 [가용성 웹 테스트를 설정](monitor-web-app-availability.md) 합니다.
