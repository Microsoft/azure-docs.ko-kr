---
title: Azure Service Fabric 클러스터의 구성 업그레이드 | Microsoft Docs
description: Resource Manager 템플릿을 사용하여 Azure에서 Service Fabric 클러스터를 실행하는 구성을 업그레이드하는 방법을 알아봅니다.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 77b9b20f99f00ef87c4907c2890cb3a21d20ec75
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62096269"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Azure에서 클러스터의 구성 업그레이드 

이 문서에서는 Service Fabric 클러스터에 대한 다양한 패브릭 설정을 사용자 지정하는 방법을 설명합니다. Azure에서 호스팅된 클러스터의 경우 [Azure Portal](https://portal.azure.com)을 통해 또는 Azure Resource Manager 템플릿을 사용하여 설정을 사용자 지정할 수 있습니다.

> [!NOTE]
> 일부 설정은 포털에서 사용할 수 있습니다 및 것을 [모범 사례는 Azure Resource Manager 템플릿을 사용 하 여 사용자 지정 하려면](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code); 포털은 Service Fabric Dev\Test 시나리오만입니다.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Resource Manager 템플릿을 사용하여 클러스터 설정 사용자 지정
JSON Resource Manager 템플릿을 통해 Azure 클러스터를 구성할 수 있습니다. 다른 설정에 대해 자세히 알아보려면 [클러스터에 대한 구성 설정](service-fabric-cluster-fabric-settings.md)을 참조하세요. 예제로 아래 단계에서는 Azure Resource Explorer를 사용하여 새로운 *MaxDiskQuotaInMB* 설정을 *Diagnostics* 섹션에 추가하는 방법을 보여줍니다.

1. https://resources.azure.com으로 이동합니다.
2. **구독** -> **\<사용자의 구독>** -> **resourceGroups** -> **\<사용자의 리소스 그룹>** -> **공급자** -> **Microsoft.ServiceFabric** -> **클러스터** -> **\<클러스터 이름>** 을 펼쳐서 구독으로 이동합니다.
3. 오른쪽 위 모서리에서 **읽기/쓰기**를 선택합니다.
4. **편집**을 선택하고 `fabricSettings` JSON 요소를 업데이트하고 새 요소를 추가합니다.

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

또한 Azure Resource Manager를 사용하여 다음 방법 중 하나에서 클러스터 설정을 사용자 지정할 수 있습니다.

- [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template)을 사용하여 Resource Manger 템플릿을 내보내고 업데이트합니다.
- [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell)을 사용하여 Resource Manger 템플릿을 내보내고 업데이트합니다.
- [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli)를 사용하여 Resource Manger 템플릿을 내보내고 업데이트합니다.
- Azure PowerShell을 사용 하 여 [집합 AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) 하 고 [제거 AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) 설정을 수정 하는 명령을 직접.
- Azure CLI [az sf 클러스터 설정](https://docs.microsoft.com/cli/azure/sf/cluster/setting) 명령을 사용하여 설정을 직접 수정합니다.

## <a name="next-steps"></a>다음 단계
* [Service Fabric 클러스터 설정](service-fabric-cluster-fabric-settings.md)에 대해 알아봅니다.
* [클러스터를 확장 및 축소](service-fabric-cluster-scale-up-down.md)하는 방법 알아보기
