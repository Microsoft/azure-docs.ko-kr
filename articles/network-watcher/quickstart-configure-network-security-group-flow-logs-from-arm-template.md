---
title: '빠른 시작: Azure Resource Manager 템플릿을 사용하여 NSG 흐름 로그 구성'
description: Azure Resource Manager 템플릿 및 Azure PowerShell을 사용하여 NSG 흐름 로그를 프로그래밍 방식으로 사용하도록 설정하는 방법을 알아봅니다.
services: network-watcher
author: damendo
Customer intent: I need to enable the NSG Flow Logs using Azure Resource Manager Template
ms.service: network-watcher
ms.topic: quickstart
ms.date: 07/22/2020
ms.author: damendo
ms.custom: subject-armqs
ms.openlocfilehash: 7d8cb89b1187bb15e7b361e1b6b9505400c612b5
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/07/2020
ms.locfileid: "87986320"
---
# <a name="quickstart-configure-nsg-flow-logs-from-arm-template"></a>빠른 시작: ARM 템플릿에서 NSG 흐름 로그 구성

이 빠른 시작에서는 [ARM 템플릿(Azure Resource Manager 템플릿)](https://docs.microsoft.com/azure/azure-resource-manager/management/overview) 및 Azure PowerShell을 사용하여 [NSG 흐름 로그](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview)를 프로그래밍 방식으로 사용하도록 설정합니다. 

먼저 NSG 흐름 로그 개체의 속성에 대한 개요를 제공하고 몇 가지 샘플 템플릿을 제공합니다. 그런 다음 로컬 PowerShell 인스턴스를 사용하여 템플릿을 배포합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="nsg-flow-logs-object"></a>NSG 흐름 로그 개체

모든 매개 변수를 포함하는 NSG 흐름 로그 개체는 다음과 같습니다.
속성에 대한 전체 개요를 보려면 [NSG 흐름 로그 템플릿 참조](https://docs.microsoft.com/azure/templates/microsoft.network/networkwatchers/flowlogs)를 참조하세요.

```json
{
  "name": "string",
  "type": "Microsoft.Network/networkWatchers/flowLogs",
  "location": "string",
  "apiVersion": "2019-09-01",
  "properties": {
    "targetResourceId": "string",
    "storageId": "string",
    "enabled": "boolean",
    "flowAnalyticsConfiguration": {
      "networkWatcherFlowAnalyticsConfiguration": {
         "enabled": "boolean",
         "workspaceResourceId": "string",
          "trafficAnalyticsInterval": "integer"
        },
        "retentionPolicy": {
           "days": "integer",
           "enabled": "boolean"
         },
        "format": {
           "type": "string",
           "version": "integer"
         }
      }
    }
  }
```
Microsoft. Network/networkWatchers/flowLogs 리소스를 만들려면 위의 JSON을 템플릿의 리소스 섹션에 추가합니다.


## <a name="creating-your-template"></a>템플릿 만들기

Azure Resource Manager 템플릿을 처음 사용하는 경우 아래 링크를 사용하여 자세히 알아볼 수 있습니다.

* [Resource Manager 템플릿과 Azure PowerShell로 리소스 배포](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-powershell#deploy-local-template)
* [자습서: 첫 번째 Azure Resource Manager 템플릿 만들기 및 배포](https://docs.microsoft.com/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/101-networkwatcher-flowlogs-create)에서 나온 것입니다.

전체 템플릿의 아래 예제는 NSG 흐름 로그를 설정하기 위해 전달되는 최소 매개 변수가 있는 가장 간단한 버전입니다. 더 많은 예제를 보려면 이 [링크](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-azure-resource-manager)로 이동하세요.

**예**: 아래 템플릿에서는 대상 NSG에 대한 NSG 흐름 로그를 사용하도록 설정하고 지정된 스토리지 계정에 저장합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "apiProfile": "2019-09-01",
  "resources": [
 {
    "name": "NetworkWatcher_centraluseuap/Microsoft.NetworkDalanDemoPerimeterNSG",
    "type": "Microsoft.Network/networkWatchers/FlowLogs/",
    "location": "centraluseuap",
    "apiVersion": "2019-09-01",
    "properties": {
      "targetResourceId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/DalanDemo/providers/Microsoft.Network/networkSecurityGroups/PerimeterNSG",
      "storageId": "/subscriptions/56abfbd6-ec72-4ce9-831f-bc2b6f2c5505/resourceGroups/MyCanaryFlowLog/providers/Microsoft.Storage/storageAccounts/storagev2ira",
      "enabled": true,
      "flowAnalyticsConfiguration": {},
      "retentionPolicy": {},
      "format": {}
    }

  }
  ]
}
```

> [!NOTE]
> * 리소스 이름은 "부모 리소스_자식 리소스" 형식을 사용합니다. 여기서 부모 리소스는 지역 Network Watcher 인스턴스입니다(형식: NetworkWatcher_RegionName. 예제: NetworkWatcher_centraluseuap)
> * targetResourceID는 대상 NSG의 리소스 ID입니다.
> * storageId는 대상 스토리지 계정의 리소스 ID입니다.


## <a name="deploying-your-azure-resource-manager-template"></a>Azure Resource Manager 템플릿 배포

이 자습서에서는 흐름 로깅을 사용할 수 있는 기존 리소스 그룹 및 NSG가 있는 것으로 가정합니다.
위의 예제 템플릿 중 하나를 `azuredeploy.json`으로 로컬로 저장할 수 있습니다. 구독에서 유효한 리소스를 가리키도록 속성 값을 업데이트합니다.

템플릿을 배포하려면 PowerShell에서 다음 명령을 실행합니다.
```azurepowershell-interactive
$context = Get-AzSubscription -SubscriptionId 56acfbd6-vc72-43e9-831f-bcdb6f2c5505
Set-AzContext $context
New-AzResourceGroupDeployment -Name EnableFlowLog -ResourceGroupName NetworkWatcherRG `
    -TemplateFile "C:\MyTemplates\azuredeploy.json"
```

> [!NOTE]
> 위의 명령은 NSG를 포함하는 리소스 그룹이 아닌 NetworkWatcherRG 리소스 그룹에 리소스를 배포합니다.


## <a name="validate-the-deployment"></a>배포 유효성 검사

배포가 성공했는지 확인하는 몇 가지 방법이 있습니다. PowerShell 콘솔에 "ProvisioningState"가 "Succeeded"로 표시되어야 합니다. 또한 [NSG 흐름 로그 포털 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Network/NetworkWatcherMenuBlade/flowLogs)를 방문하여 변경 내용을 확인할 수 있습니다. 배포에 문제가 있는 경우 [Azure Resource Manager를 사용한 일반적인 Azure 배포 오류 해결](https://docs.microsoft.com/azure/azure-resource-manager/templates/common-deployment-errors)을 살펴봅니다.

## <a name="deleting-your-resource"></a>리소스 삭제
Azure에서는 "전체" 배포 모드를 통해 리소스를 삭제할 수 있습니다. 흐름 로그 리소스를 삭제하려면 삭제하려는 리소스를 포함하지 않고 배포를 전체 모드로 지정합니다. [전체 배포 모드](https://docs.microsoft.com/azure/azure-resource-manager/templates/deployment-modes#complete-mode)에 대해 자세히 알아봅니다.

또는 아래 단계에 따라 Azure Portal에서 NSG 흐름 로그를 사용하지 않도록 설정할 수 있습니다.
1. Azure Portal에 로그인합니다.
2. 포털의 맨 왼쪽 위에서 **모든 서비스**를 선택합니다. **필터 상자**에 *Network Watcher*를 입력합니다. 검색 결과에 **Network Watcher**가 나타나면 이를 선택합니다.
3. **로그**에서 **NSG 흐름 로그**를 선택합니다.
4. NSG 목록에서 흐름 로그를 사용하지 않도록 설정할 NSG를 선택합니다.
5. **흐름 로그 설정**에서 흐름 로그 상태를 **끄기**로 설정합니다.
6. 아래로 스크롤하여 **저장**을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 NSG 흐름 로그를 사용하도록 설정했습니다. 이제 다음을 사용하여 NSG 흐름 데이터를 시각화하는 방법을 알아두어야 합니다. 

* [Microsoft Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
* [오픈 소스 도구](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
* [Azure 트래픽 분석](https://docs.microsoft.com/azure/network-watcher/traffic-analytics)
