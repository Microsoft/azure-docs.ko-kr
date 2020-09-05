---
title: WAD(Windows Azure Diagnostics) 확장 설치 및 구성
description: 사용 가능한 여러 도구 중 하나를 사용하여 Azure Storage 계정에서 Azure Diagnostics 데이터를 볼 수 있도록 수집하는 방법을 알아봅니다.
services: azure-monitor
author: bwren
ms.subservice: diagnostic-extension
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: bwren
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: ac087a7ba241534c08c4e5737973861727ab01ca
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89069581"
---
# <a name="install-and-configure-windows-azure-diagnostics-extension-wad"></a>WAD(Windows Azure Diagnostics) 확장 설치 및 구성
[Azure Diagnostics 확장](diagnostics-extension-overview.md)은 게스트 운영 체제에서 모니터링 데이터를 수집하고 Azure 가상 머신 및 기타 계산 리소스의 워크로드를 수집하는 Azure Monitor의 에이전트입니다. 이 문서에서는 Windows 진단 확장 설치 및 구성에 대한 자세한 내용을 알아보고 데이터가 저장되는 방법과 Azure Storage 계정에 대해 설명합니다.

진단 확장은 Azure에서 [가상 머신 확장](../../virtual-machines/extensions/overview.md)으로 구현되므로 Resource Manager 템플릿, PowerShell 및 CLI를 사용한 동일한 설치 옵션을 지원합니다. 가상 머신 확장 설치 및 유지 관리에 대한 자세한 내용은 [Windows용 가상 머신 확장 및 기능](../../virtual-machines/extensions/features-windows.md)을 참조하세요.

## <a name="overview"></a>개요
Windows Azure Diagnostics 확장을 구성하는 경우 지정된 모든 데이터가 전송될 스토리지 계정을 지정해야 합니다. 필요에 따라 데이터 *싱크* 를 하나 이상 추가 하 여 데이터를 다른 위치로 보낼 수 있습니다.

- Azure Monitor 싱크 - Azure Monitor 메트릭에 게스트 성능 데이터를 보냅니다.
- 이벤트 허브 싱크 - Azure 외부로 전달하려면 Azure Event Hubs에 게스트 성능 및 로그 데이터를 전송합니다. 이 싱크는 Azure Portal에서 구성할 수 없습니다.


## <a name="install-with-azure-portal"></a>Azure Portal을 사용하여 설치
Azure Portal에서 개별 가상 머신에 진단 확장을 설치하고 구성할 수 있습니다. 그러면 구성에서 직접 작업하는 것과는 반대로 인터페이스를 제공합니다. 진단 확장을 사용하도록 설정하면 가장 일반적인 성능 카운터 및 이벤트와 함께 기본 구성이 자동으로 사용됩니다. 특정 요구 사항에 따라 이 기본 구성을 수정할 수 있습니다.

> [!NOTE]
> 다음은 진단 확장에 대한 가장 일반적인 설정을 설명합니다. 모든 구성 옵션에 대한 자세한 내용은 [Windows 진단 확장 스키마](diagnostics-extension-schema-windows.md)를 참조하세요.

1. Azure Portal에서 가상 머신에 대한 메뉴를 엽니다.

2. VM 메뉴의 **모니터링** 섹션에서 **진단 설정**을 클릭합니다.

3. 아직 진단 확장을 사용하도록 설정하지 않은 경우 **게스트 수준 모니터링 사용**을 클릭합니다.

   ![모니터링 사용](media/diagnostics-extension-windows-install/enable-monitoring.png)

4. VM에 대한 새 Azure Storage 계정 이름은 VM에 대한 리소스 그룹의 이름을 기반으로 하고, 게스트 성능 카운터 및 로그의 기본 집합이 선택됩니다.

   ![진단 설정](media/diagnostics-extension-windows-install/diagnostic-settings.png)

5. **성능 카운터** 탭에서 이 가상 머신에서 수집할 게스트 메트릭을 선택합니다. 고급 선택을 위해서는 **사용자 지정** 설정을 사용합니다.

   ![성능 카운터](media/diagnostics-extension-windows-install/performance-counters.png)

6. **로그** 탭에서 가상 머신에서 수집할 로그를 선택합니다. 로그를 스토리지 또는 이벤트 허브로 전송할 수 있지만 Azure Monitor로는 전송할 수 없습니다. [Log Analytics 에이전트](log-analytics-agent.md)를 사용하여 Azure Monitor에 대한 게스트 로그를 수집합니다.

   ![로그](media/diagnostics-extension-windows-install/logs.png)

7. **크래시 덤프** 탭에서 크래시 후 메모리 덤프를 수집할 프로세스를 지정합니다. 진단 설정에 대한 데이터는 스토리지 계정에 기록되며 필요에 따라 Blob 컨테이너를 지정할 수 있습니다.

   ![크래시 덤프](media/diagnostics-extension-windows-install/crash-dumps.png)

8. **싱크** 탭에서 데이터를 Azure 스토리지 이외의 위치로 보낼지 여부를 지정합니다. **Azure Monitor**를 선택하면 게스트 성능 데이터가 Azure Monitor 메트릭에 전송됩니다. Azure Portal을 사용하여 이벤트 허브 싱크를 구성할 수 없습니다.

   ![Sinks](media/diagnostics-extension-windows-install/sinks.png)
   
   가상 머신에 대해 구성된 시스템 할당 ID를 사용하도록 설정하지 않은 경우 Azure Monitor 싱크로 구성을 저장할 때 아래 경고가 표시될 수 있습니다. 시스템 할당 ID를 사용하도록 설정하려면 배너를 클릭합니다.
   
   ![관리되는 엔터티](media/diagnostics-extension-windows-install/managed-entity.png)

9. **에이전트**에서 스토리지 계정을 변경하고, 디스크 할당량을 설정하고, 진단 인프라 로그를 수집할지 여부를 지정할 수 있습니다.  

   ![에이전트](media/diagnostics-extension-windows-install/agent.png)

10. 구성을 저장하려면 **Save**를 클릭합니다. 

> [!NOTE]
> JSON 또는 XML에서 진단 확장에 대한 구성을 포맷할 수 있지만 Azure Portal에서 수행되는 모든 구성은 항상 JSON으로 저장됩니다. 다른 구성 방법과 함께 XML을 사용하는 경우 Azure Portal을 사용하여 구성을 변경하면 설정이 JSON으로 변경됩니다.

## <a name="resource-manager-template"></a>Resource Manager 템플릿
Azure Resource Manager 템플릿을 사용하여 진단 확장을 배포하는 방법에 대한 자세한 내용은 [Windows VM 및 Azure Resource Manager를 사용하여 모니터링 및 진단 사용](../../virtual-machines/extensions/diagnostics-template.md)을 참조하세요. 

## <a name="azure-cli-deployment"></a>Azure CLI 배포
Azure CLI를 사용하여 다음 예와 같이 [az vm extension set](/cli/azure/vm/extension?view=azure-cli-latest#az-vm-extension-set)를 사용하여 기존 가상 머신에 Azure Diagnostics 확장을 배포할 수 있습니다. 

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name IaaSDiagnostics \
  --publisher Microsoft.Azure.Diagnostics \
  --protected-settings protected-settings.json \
  --settings public-settings.json 
```

보호된 설정은 구성 스키마의 [PrivateConfig 요소](diagnostics-extension-schema-windows.md#privateconfig-element)에서 정의됩니다. 다음은 스토리지 계정을 정의하는 보호된 설정 파일의 간단한 예입니다. 개인 설정에 대한 자세한 내용은 [예제 구성](diagnostics-extension-schema-windows.md#privateconfig-element)을 참조하세요.

```JSON
{
    "storageAccountName": "mystorageaccount",
    "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
}
```

공용 설정은 구성 스키마의 [Public 요소](diagnostics-extension-schema-windows.md#publicconfig-element)에서 정의됩니다. 다음은 진단 인프라 로그, 단일 성능 카운터 및 단일 이벤트 로그 수집을 사용하도록 설정하는 공용 설정 파일의 간단한 예입니다. 공용 설정에 대한 자세한 내용은 [예제 구성](diagnostics-extension-schema-windows.md#publicconfig-element)을 참조하세요.

```JSON
{
  "StorageAccount": "mystorageaccount",
  "WadCfg": {
    "DiagnosticMonitorConfiguration": {
      "overallQuotaInMB": 5120,
      "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
          {
            "counterSpecifier": "\\Processor Information(_Total)\\% Processor Time",
            "unit": "Percent",
            "sampleRate": "PT60S"
          }
        ]
      },
      "WindowsEventLog": {
        "scheduledTransferPeriod": "PT1M",
        "DataSource": [
          {
            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
          }
        ]
      }
    }
  }
}
```



## <a name="powershell-deployment"></a>PowerShell 배포
PowerShell을 사용하여 다음 예와 같이 [Set-AzVMDiagnosticsExtension](/powershell/module/servicemanagement/azure.service/set-azurevmdiagnosticsextension)을 사용하여 기존 가상 머신에 Azure Diagnostics 확장을 배포할 수 있습니다. 

```powershell
Set-AzVMDiagnosticsExtension -ResourceGroupName "myvmresourcegroup" `
  -VMName "myvm" `
  -DiagnosticsConfigurationPath "DiagnosticsConfiguration.json"
```

개인 설정은 [PrivateConfig 요소](diagnostics-extension-schema-windows.md#privateconfig-element)에서 정의되고 공용 설정은 구성 스키마의 [Public 요소](diagnostics-extension-schema-windows.md#publicconfig-element)에서 정의됩니다. 스토리지 계정에 대한 세부 정보를 개인 설정에 포함하는 대신 Set-AzVMDiagnosticsExtension cmdlet의 매개 변수로 지정하도록 선택할 수도 있습니다.

다음은 진단 인프라 로그, 단일 성능 카운터 및 단일 이벤트 로그 수집을 사용하도록 설정하는 구성 파일의 간단한 예입니다. 개인 및 공용 설정에 대한 자세한 내용은 [예제 구성](diagnostics-extension-schema-windows.md#publicconfig-element)을 참조하세요. 

```JSON
{
    "PublicConfig": {
        "WadCfg": {
            "DiagnosticMonitorConfiguration": {
                "overallQuotaInMB": 10000,
                "DiagnosticInfrastructureLogs": {
                    "scheduledTransferLogLevelFilter": "Error"
                },
                "PerformanceCounters": {
                    "scheduledTransferPeriod": "PT1M",
                    "PerformanceCounterConfiguration": [
                        {
                            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                            "sampleRate": "PT3M",
                            "unit": "percent"
                        }
                    ]
                },
                "WindowsEventLog": {
                    "scheduledTransferPeriod": "PT1M",
                        "DataSource": [
                        {
                            "name": "Application!*[System[(Level=1 or Level=2 or Level=3)]]"
                        }
                    ]
                }
            }
        },
        "StorageAccount": "mystorageaccount",
        "StorageType": "TableAndBlob"
    },
    "PrivateConfig": {
        "storageAccountName": "mystorageaccount",
        "storageAccountKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
        "storageAccountEndPoint": "https://mystorageaccount.blob.core.windows.net"
    }
}
```

[PowerShell을 사용하여 Windows를 실행하는 가상 머신에서 Azure Diagnostics를 사용하도록 설정](../../virtual-machines/extensions/diagnostics-windows.md)을 참조하세요.

## <a name="data-storage"></a>데이터 스토리지
다음 표에서는 진단 확장에서 수집된 다양한 데이터 유형 및 이 데이터가 테이블로 저장되는지 BLOB으로 저장되는지 여부를 보여 줍니다. 공용 구성에서 [StorageType 설정](diagnostics-extension-schema-windows.md#publicconfig-element)에 따라 테이블에 저장된 데이터를 BLOB에 저장할 수도 있습니다.


| 데이터 | 스토리지 유형 | Description |
|:---|:---|:---|
| WADDiagnosticInfrastructureLogsTable | 테이블 | 진단 모니터 및 구성 변경 내용입니다. |
| WADDirectoriesTable | 테이블 | 진단 모니터가 모니터링하는 디렉터리입니다.  IIS 로그, IIS 실패한 요청 로그 및 사용자 지정 디렉터리를 포함합니다.  Blob 로그 파일의 위치는 Container 필드에 지정되고 Blob의 이름은 RelativePath 필드에 있습니다.  AbsolutePath 필드는 Azure 가상 머신에 존재했던 파일의 이름과 위치를 나타냅니다. |
| WadLogsTable | 테이블 | 추적 수신기를 사용하여 코드에서 작성된 로그입니다. |
| WADPerformanceCountersTable | 테이블 | 성능 카운터. |
| WADWindowsEventLogsTable | 테이블 | Windows 이벤트 로그 |
| wad-iis-failedreqlogfiles | Blob | IIS 실패한 요청 로그의 정보를 포함합니다. |
| wad-iis-logfiles | Blob | IIS 로그에 대한 정보를 포함합니다. |
| "custom" | Blob | 진단 모니터에 의해 모니터링되는 구성 디렉터리에 기반한 사용자 지정 컨테이너입니다.  이 Blob 컨테이너의 이름은 WADDirectoriesTable에 지정됩니다. |

## <a name="tools-to-view-diagnostic-data"></a>진단 데이터를 볼 도구
여러 도구를 사용하여 스토리지로 전송된 후 데이터를 볼 수 있습니다. 다음은 그 예입니다.

* Visual Studio의 서버 탐색기 - Microsoft Visual Studio용 Azure 도구를 설치한 경우 서버 탐색기에서 Azure Storage 노드를 사용하여 Azure Storage 계정에서 읽기 전용 Blob 및 테이블 데이터를 볼 수 있습니다. 로컬 스토리지 에뮬레이터 계정 및 Azure용으로 만든 스토리지 계정에서 데이터를 표시할 수 있습니다. 자세한 내용은 [서버 탐색기로 Storage 리소스 탐색 및 관리](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)를 참조하세요.
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, OSX 및 Linux에서 Azure Storage 데이터로 손쉽게 작업할 수 있도록 해주는 독립 실행형 앱입니다.
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) 에는 Azure에서 실행되는 애플리케이션으로 수집된 진단 데이터를 보고 다운로드하고 관리할 수 있는 Azure Diagnostics 관리자가 있습니다.

## <a name="next-steps"></a>다음 단계
- 모니터링 데이터를 Azure Event Hubs로 전달하는 방법에 대한 자세한 내용은 [Windows Azure Diagnostics 확장에서 Event Hubs로 데이터 전송](diagnostics-extension-stream-event-hubs.md)을 참조하세요.
