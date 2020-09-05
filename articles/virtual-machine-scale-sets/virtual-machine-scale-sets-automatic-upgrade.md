---
title: Azure 가상 머신 확장 집합을 사용 하 여 자동 OS 이미지 업그레이드
description: 확장 집합의 VM 인스턴스에서 OS 이미지를 자동으로 업그레이드 하는 방법을 알아봅니다.
author: avirishuv
ms.author: avverma
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 06/26/2020
ms.reviewer: jushiman
ms.custom: avverma
ms.openlocfilehash: b5f3305fc5d2595c8b7b08d78ff20edea01c195e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89229840"
---
# <a name="azure-virtual-machine-scale-set-automatic-os-image-upgrades"></a>Azure Virtual Machine Scale Sets 자동 OS 업그레이드

확장 집합에서 자동 OS 이미지 업그레이드를 사용하면 확장 집합의 모든 인스턴스에 대해 OS 디스크가 안전하게 자동 업그레이드되므로 편리한 업데이트 관리에 도움이 됩니다.

자동 OS 업그레이드의 특징은 다음과 같습니다.

- 구성이 완료되면 이미지 게시자가 게시한 최신 OS 이미지가 사용자 개입 없이 확장 집합에 자동으로 적용됩니다.
- 게시자가 새 이미지를 게시할 때마다 롤링 방식으로 인스턴스의 일괄 처리를 업그레이드 합니다.
- 애플리케이션 상태 프로브 및 [애플리케이션 상태 확장](virtual-machine-scale-sets-health-extension.md)과 통합됩니다.
- 모든 VM 크기와 Windows 및 Linux 이미지에 대해 작동 합니다.
- 언제든지 자동 업그레이드를 옵트아웃할 수 있습니다(OS 업그레이드를 수동으로 시작 가능).
- VM의 OS 디스크는 최신 이미지 버전을 사용하여 만든 새 OS 디스크로 교체됩니다. 구성된 확장 및 사용자 지정 데이터 스크립트가 실행되고 지속형 데이터 디스크는 유지됩니다.
- [확장 시퀀스](virtual-machine-scale-sets-extension-sequencing.md)가 지원됩니다.
- 크기 조정 집합에서 자동 OS 이미지 업그레이드를 사용 하도록 설정할 수 있습니다.

## <a name="how-does-automatic-os-image-upgrade-work"></a>자동 OS 이미지 업그레이드의 작동 방식

업그레이드는 최신 이미지 버전을 사용하여 만든 새 디스크로 VM OS 디스크를 바꾸는 방식으로 작동합니다. 구성된 모든 확장 및 사용자 지정 데이터 스크립트가 OS 디스크에서 실행되고 지속형 데이터 디스크는 유지됩니다. 애플리케이션 가동 중지 시간을 최소화하기 위해 업그레이드는 배치 단위로 수행되며, 확장 집합에서 업그레이드되는 비율은 항상 20% 이하로 유지됩니다. Azure Load Balancer 애플리케이션 상태 프로브 또는 [애플리케이션 상태 확장](virtual-machine-scale-sets-health-extension.md)을 통합할 수도 있습니다. 애플리케이션 하트비트를 통합하고 업그레이드 프로세스의 각 배치에 대해 업그레이드 성공을 확인하는 것이 좋습니다.

업그레이드 프로세스는 다음과 같이 작동합니다.
1. 업그레이드 프로세스를 시작하기 전에 오케스트레이터는 전체 확장 집합에서 어떠한 이유로든 비정상 상태인 인스턴스 비율이 20% 이하인지를 확인합니다.
2. 업그레이드 오 케 스트레이 터는 업그레이드에 사용 되는 VM 인스턴스의 일괄 처리를 식별 합니다 .이 일괄 처리는 하나의 가상 컴퓨터에 대 한 최소 일괄 처리 크기에 따라 총 인스턴스 수의 최대 20%를 포함 합니다.
3. 선택한 VM 인스턴스 일괄 처리의 OS 디스크는 최신 이미지에서 만든 새 OS 디스크로 대체 됩니다. 확장 집합 모델의 지정 된 모든 확장 및 구성이 업그레이드 된 인스턴스에 적용 됩니다.
4. 구성된 애플리케이션 상태 프로브 또는 애플리케이션 상태 확장이 있는 확장 집합의 경우 업그레이드 작업은 인스턴스가 정상이 될 때까지 최대 5분 동안 기다렸다가 다음 배치 업그레이드를 진행합니다. 업그레이드 후 5 분 안에 인스턴스가 상태를 복구 하지 않으면 기본적으로 인스턴스에 대 한 이전 OS 디스크가 복원 됩니다.
5. 업그레이드 오케스트레이터는 업그레이드 후 비정상 상태가 되는 인스턴스의 백분율도 추적합니다. 업그레이드 프로세스 중에 업그레이드된 인스턴스의 20% 이상이 비정상 상태가 되면 업그레이드가 중지됩니다.
6. 확장 집합의 모든 인스턴스가 업그레이드될 때까지 위의 프로세스가 계속됩니다.

확장 집합 OS 업그레이드 오케스트레이터는 전체 확장 집합 상태를 확인한 후에 모든 배치를 업그레이드합니다. 배치 업그레이드 중에 확장 집합 인스턴스의 상태에 영향을 줄 수 있는, 계획되었거나 계획되지 않은 다른 동시 유지 관리 활동이 있을 수 있습니다. 확장 집합 인스턴스의 20% 이상이 비정상 상태가 될 경우 현재 배치가 끝나면 확장 집합 업그레이드가 중지됩니다.

## <a name="supported-os-images"></a>지원되는 OS 이미지
현재는 특정 OS 플랫폼 이미지만 지원됩니다. 확장 집합에서 [공유 이미지 갤러리](shared-image-galleries.md)를 통해 사용자 지정 이미지를 사용 하는 경우 사용자 지정 이미지가 [지원 됩니다](virtual-machine-scale-sets-automatic-upgrade.md#automatic-os-image-upgrade-for-custom-images) .

현재 지원 되는 플랫폼 Sku는 다음과 같습니다 .이는 주기적으로 추가 됩니다.

| 게시자               | OS 제품      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical               | UbuntuServer  | 16.04-LTS          |
| Canonical               | UbuntuServer  | 18.04-LTS          |
| Rogue Wave(OpenLogic)  | CentOS        | 7.5                |
| CoreOS                  | CoreOS        | Stable             |
| Microsoft Corporation   | WindowsServer | 2012-R2-Datacenter |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter    |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2016-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter |
| Microsoft Corporation   | WindowsServer | 2019-Smalldisk |
| Microsoft Corporation   | WindowsServer | 2019-Datacenter-with-Containers |
| Microsoft Corporation   | WindowsServer | Datacenter-1903-smalldisk |


## <a name="requirements-for-configuring-automatic-os-image-upgrade"></a>자동 OS 이미지 업그레이드 구성을 위한 요구 사항

- 이미지의 *version* 속성을 *최신*으로 설정 해야 합니다.
- Service Fabric 이외의 확장 집합에는 애플리케이션 상태 프로브 또는 [애플리케이션 상태 확장](virtual-machine-scale-sets-health-extension.md)을 사용합니다.
- Compute API 버전 2018-10-01 이상을 사용 합니다.
- 확장 집합 모델에 지정된 외부 리소스가 사용 가능하고 업데이트되었는지 확인합니다. VM 확장 속성의 페이로드, 스토리지 계정의 페이로드, 모델의 비밀에 대한 참조 등을 부트스트랩하기 위한 SAS URI를 예로 들 수 있습니다.
- Windows 가상 머신을 사용 하는 확장 집합의 경우 계산 API 버전 2019-03-01부터 *osProfile* 속성은 확장 집합 모델 정의에서 *false* 로 설정 되어야 합니다. 위의 속성은 "Windows 업데이트"에서 OS 디스크를 교체 하지 않고 운영 체제 패치를 적용 하는 VM 내 업그레이드를 사용 하도록 설정 합니다. 크기 집합에서 자동 OS 이미지 업그레이드를 사용 하도록 설정 하면 "Windows 업데이트"를 통한 추가 업데이트가 필요 하지 않습니다.

### <a name="service-fabric-requirements"></a>Service Fabric 요구 사항

Service Fabric를 사용 하는 경우 다음 조건이 충족 되는지 확인 합니다.
-   Service Fabric [내구성 수준은](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster) 브론즈가 아니라 실버 또는 골드입니다.
-   확장 집합 모델 정의의 Service Fabric 확장에는 TypeHandlerVersion 1.1 이상이 있어야 합니다.
-   Service Fabric 클러스터와 확장 집합 모델 정의에 대 한 Service Fabric 확장에서 내구성 수준이 동일 해야 합니다.
- 추가 상태 프로브 또는 응용 프로그램 상태 확장 사용은 필요 하지 않습니다.

Service Fabric 클러스터와 Service Fabric 확장에서 내구성 설정이 일치 하지 않는지 확인 합니다 .이로 인해 업그레이드 오류가 발생 합니다. [이 페이지](../service-fabric/service-fabric-cluster-capacity.md#changing-durability-levels)에 설명 된 지침에 따라 내구성 수준을 수정할 수 있습니다.


## <a name="automatic-os-image-upgrade-for-custom-images"></a>사용자 지정 이미지에 대 한 자동 OS 이미지 업그레이드

자동 OS 이미지 업그레이드는 [공유 이미지 갤러리](shared-image-galleries.md)를 통해 배포 된 사용자 지정 이미지에 대해 지원 됩니다. 자동 OS 이미지 업그레이드에 대 한 다른 사용자 지정 이미지는 지원 되지 않습니다.

### <a name="additional-requirements-for-custom-images"></a>사용자 지정 이미지에 대 한 추가 요구 사항
- 자동 OS 이미지 업그레이드에 대 한 설치 및 구성 프로세스는이 페이지의 [구성 섹션](virtual-machine-scale-sets-automatic-upgrade.md#configure-automatic-os-image-upgrade) 에 자세히 설명 된 모든 규모 집합에 대해 동일 합니다.
- 자동 OS 이미지 업그레이드에 대해 구성 된 확장 집합 인스턴스는 새 버전의 이미지를 게시 하 고 해당 확장 집합의 지역에 [복제할](shared-image-galleries.md#replication) 때 공유 이미지 갤러리 이미지의 최신 버전으로 업그레이드 됩니다. 새 이미지가 배율이 배포 된 지역에 복제 되지 않으면 확장 집합 인스턴스는 최신 버전으로 업그레이드 되지 않습니다. 지역별 이미지 복제를 사용 하 여 확장 집합에 대 한 새 이미지의 출시를 제어할 수 있습니다.
- 새 이미지 버전은 해당 갤러리 이미지의 최신 버전에서 제외 되어서는 안 됩니다. 갤러리 이미지의 최신 버전에서 제외 된 이미지 버전은 자동 OS 이미지 업그레이드를 통해 확장 집합으로 롤아웃 되지 않습니다.

> [!NOTE]
>자동 OS 업그레이드에 대해 확장 집합이 먼저 구성 된 후 확장 집합이 첫 번째 이미지 업그레이드 롤아웃을 트리거하는 데 최대 3 시간이 걸릴 수 있습니다. 크기 집합 당 일회성 지연입니다. 이후 이미지 롤아웃은 30-60 분 내에 확장 집합에서 트리거됩니다.


## <a name="configure-automatic-os-image-upgrade"></a>자동 OS 이미지 업그레이드 구성
자동 OS 이미지 업그레이드를 구성하려면 확장 집합 모델 정의에서 *automaticOSUpgradePolicy.enableAutomaticOSUpgrade* 속성을 *true*로 설정해야 합니다.

### <a name="rest-api"></a>REST API
다음 예제에서는 확장 집합 모델에서 자동 OS 업그레이드를 설정하는 방법을 설명합니다.

```
PUT or PATCH on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet?api-version=2019-12-01`
```

```json
{
  "properties": {
    "upgradePolicy": {
      "automaticOSUpgradePolicy": {
        "enableAutomaticOSUpgrade":  true
      }
    }
  }
}
```

### <a name="azure-powershell"></a>Azure PowerShell
[AzVmss](/powershell/module/az.compute/update-azvmss) cmdlet을 사용 하 여 확장 집합에 대 한 자동 OS 이미지 업그레이드를 구성 합니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myScaleSet* 이라는 확장 집합에 대 한 자동 업그레이드를 구성 합니다.

```azurepowershell-interactive
Update-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -AutomaticOSUpgrade $true
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[Az vmss update](/cli/azure/vmss#az-vmss-update) 를 사용 하 여 확장 집합에 대 한 자동 OS 이미지 업그레이드를 구성 합니다. Azure CLI 2.0.47 이상을 사용합니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myScaleSet* 이라는 확장 집합에 대 한 자동 업그레이드를 구성 합니다.

```azurecli-interactive
az vmss update --name myScaleSet --resource-group myResourceGroup --set UpgradePolicy.AutomaticOSUpgradePolicy.EnableAutomaticOSUpgrade=true
```

> [!NOTE]
>확장 집합에 대해 자동 OS 이미지 업그레이드를 구성한 후 확장 집합에서 ' 수동 ' [업그레이드 정책을](virtual-machine-scale-sets-upgrade-scale-set.md#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model)사용 하는 경우 확장 집합 vm을 최신 확장 집합 모델로 가져와야 합니다.

## <a name="using-application-health-probes"></a>애플리케이션 상태 프로브 사용

OS가 업그레이드되는 동안 확장 집합의 VM 인스턴스는 한 번에 하나의 일괄 처리 단위로 업그레이드됩니다. 업그레이드된 VM 인스턴스에서 고객 애플리케이션의 상태가 정상인 경우에만 업그레이드를 계속 진행해야 합니다. 애플리케이션이 확장 집합 OS 업그레이드 엔진에 상태 신호를 제공하는 것이 좋습니다. 기본적으로 OS를 업그레이드하는 동안 플랫폼은 VM 전원 상태 및 확장 프로비전 상태를 고려하여 업그레이드 후 VM 인스턴스가 정상 상태인지 확인합니다. VM 인스턴스의 OS를 업그레이드하는 동안 VM 인스턴스의 OS 디스크는 최신 이미지 버전에 따라 새 디스크로 교체됩니다. OS 업그레이드가 완료되면 구성된 확장이 이러한 VM에서 실행됩니다. 인스턴스의 모든 확장이 성공적으로 프로비전된 경우에만 애플리케이션이 정상 상태로 간주됩니다.

원한다면 플랫폼에 애플리케이션의 현재 상태에 대한 정확한 정보를 제공하도록 확장 집합을 구성할 수 있습니다. 애플리케이션 상태 프로브는 상태 신호로 사용되는 사용자 지정 부하 분산 장치 프로브입니다. 확장 집합 VM 인스턴스에서 실행되는 애플리케이션은 외부 HTTP 또는 TCP 요청에 응답하여 정상 상태인지 여부를 알릴 수 있습니다. 사용자 지정 부하 분산 장치 프로브에 대한 자세한 내용은 [부하 분산 장치 프로브 이해](../load-balancer/load-balancer-custom-probe-overview.md)를 참조하세요. 응용 프로그램 상태 프로브는 Service Fabric 확장 집합에 대해 지원 되지 않습니다. Service Fabric 이외의 확장 집합에는 Load Balancer 애플리케이션 상태 프로브 또는 [애플리케이션 상태 확장](virtual-machine-scale-sets-health-extension.md)이 필요합니다.

여러 배치 그룹을 사용하도록 확장 집합을 구성한 경우 [표준 부하 분산 장치](../load-balancer/load-balancer-overview.md)를 사용하는 프로브를 사용해야 합니다.

### <a name="configuring-a-custom-load-balancer-probe-as-application-health-probe-on-a-scale-set"></a>확장 집합의 애플리케이션 상태 프로브로 사용자 지정 부하 분산 장치 구성
모범 사례에 따라 확장 집합 상태에 대한 부하 분산 장치 프로브를 명시적으로 만듭니다. 기존 HTTP 프로브 또는 TCP 프로브에 대한 동일한 엔드포인트를 사용할 수 있지만, 상태 프로브에는 기존의 부하 분산 장치 프로브와 다른 동작이 필요할 수 있습니다. 예를 들어 기존의 부하 분산 장치 프로브는 인스턴스의 부하가 너무 높을 경우 비정상 상태를 반환하기도 하는데, 이 동작은 OS 자동 업그레이드 중에 인스턴스 상태를 확인하는 데 적합하지 않습니다. 검색 속도가 2분을 넘지 않도록 프로브를 구성하세요.

부하 분산 장치 프로브는 다음과 같이 확장 집합의 *networkProfile*에서 참조할 수 있으며 내부 또는 공용 부하 분산 장치에 연결할 수 있습니다.

```json
"networkProfile": {
  "healthProbe" : {
    "id": "[concat(variables('lbId'), '/probes/', variables('sshProbeName'))]"
  },
  "networkInterfaceConfigurations":
  ...
}
```

> [!NOTE]
> Service Fabric과 함께 자동 OS 업그레이드를 사용할 때 새 OS 이미지는 Service Fabric에서 실행되는 서비스의 고가용성을 유지하기 위해 업데이트 도메인에 의해 업데이트 도메인에 롤아웃됩니다. Service Fabric에서 자동 OS 업그레이드를 활용하려면 실버 내구성 계층 이상을 사용하도록 클러스터가 구성되어야 합니다. Service Fabric 클러스터의 내구성 특성에 대한 자세한 내용은 [이 설명서](../service-fabric/service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster)를 참조하세요.

### <a name="keep-credentials-up-to-date"></a>자격 증명을 최신 상태로 유지
확장 집합에서 자격 증명을 사용 하 여 저장소 계정에 대 한 SAS 토큰을 사용 하도록 구성 된 VM 확장과 같은 외부 리소스에 액세스 하는 경우 자격 증명을 업데이트 해야 합니다. 인증서 및 토큰을 포함 한 자격 증명이 만료 된 경우 업그레이드는 실패 하 고 Vm의 첫 번째 일괄 처리는 실패 상태로 남아 있습니다.

리소스 인증 오류 시 VM을 복구하고 자동 OS 업그레이드를 다시 설정하기 위한 권장 단계는 다음과 같습니다.

* 확장에 전달된 토큰(또는 다른 자격 증명)를 다시 생성합니다.
* 외부 엔터티에 연결된 VM 내에서 사용되는 자격 증명이 최신 상태인지 확인합니다.
* 새 토큰으로 확장 집합의 확장을 업데이트합니다.
* 업데이트된 확장 집합을 배포합니다. 그러면 실패한 인스턴스를 포함하여 모든 VM 인스턴스를 업데이트합니다.

## <a name="using-application-health-extension"></a>애플리케이션 상태 확장 사용
애플리케이션 상태 확장은 가상 머신 확장 집합 인스턴스 내에 배포되고, 확장 집합 인스턴스 내부에서 VM 상태를 보고합니다. 애플리케이션 엔드포인트를 검색하고 해당 인스턴스의 애플리케이션 상태를 업데이트하도록 확장을 구성할 수 있습니다. Azure에서 이 인스턴스 상태를 확인하여 해당 인스턴스가 업그레이드 작업에 적합한지 여부를 결정합니다.

이 확장은 VM 내에서 상태를 보고하므로 애플리케이션 상태 프로브(사용자 지정 Azure Load Balancer [프로브](../load-balancer/load-balancer-custom-probe-overview.md) 활용) 등의 외부 프로브를 사용할 수 없는 경우에 이용할 수 있습니다.

[이 문서](virtual-machine-scale-sets-health-extension.md#deploy-the-application-health-extension)의 예제에서 자세히 설명한 대로 애플리케이션 상태 확장을 확장 집합에 배포하는 여러 가지 방법이 있습니다.

## <a name="get-the-history-of-automatic-os-image-upgrades"></a>자동 OS 이미지 업그레이드의 기록 가져오기
Azure PowerShell, Azure CLI 2.0 또는 REST API를 사용하여 확장 집합에서 수행된 가장 최근의 OS 업그레이드 기록을 확인할 수 있습니다. 지난 2개월 동안 마지막으로 시도한 OS 업그레이드 5개의 기록을 가져올 수 있습니다.

### <a name="rest-api"></a>REST API
다음 예제에서는 [REST API](/rest/api/compute/virtualmachinescalesets/getosupgradehistory) 를 사용 하 여 *myresourcegroup*이라는 리소스 그룹에서 *myScaleSet* 라는 확장 집합의 상태를 확인 합니다.

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osUpgradeHistory?api-version=2019-12-01`
```

GET 호출은 다음 예제 출력과 비슷한 속성을 반환합니다.

```json
{
    "value": [
        {
            "properties": {
        "runningStatus": {
          "code": "RollingForward",
          "startTime": "2018-07-24T17:46:06.1248429+00:00",
          "completedTime": "2018-04-21T12:29:25.0511245+00:00"
        },
        "progress": {
          "successfulInstanceCount": 16,
          "failedInstanceCount": 0,
          "inProgressInstanceCount": 4,
          "pendingInstanceCount": 0
        },
        "startedBy": "Platform",
        "targetImageReference": {
          "publisher": "MicrosoftWindowsServer",
          "offer": "WindowsServer",
          "sku": "2016-Datacenter",
          "version": "2016.127.20180613"
        },
        "rollbackInfo": {
          "successfullyRolledbackInstanceCount": 0,
          "failedRolledbackInstanceCount": 0
        }
      },
      "type": "Microsoft.Compute/virtualMachineScaleSets/rollingUpgrades",
      "location": "westeurope"
    }
  ]
}
```

### <a name="azure-powershell"></a>Azure PowerShell
[Get-AzVmss](/powershell/module/az.compute/get-azvmss) cmdlet을 사용하여 확장 집합에 대한 OS 업그레이드 기록을 확인합니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myScaleSet* 이라는 확장 집합에 대 한 OS 업그레이드 상태를 검토 하는 방법을 자세히 설명 합니다.

```azurepowershell-interactive
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -OSUpgradeHistory
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[az vmss get-os-upgrade-history](/cli/azure/vmss#az-vmss-get-os-upgrade-history)를 사용하여 확장 집합에 대한 OS 업그레이드 기록을 확인합니다. Azure CLI 2.0.47 이상을 사용합니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myScaleSet* 이라는 확장 집합에 대 한 OS 업그레이드 상태를 검토 하는 방법을 자세히 설명 합니다.

```azurecli-interactive
az vmss get-os-upgrade-history --resource-group myResourceGroup --name myScaleSet
```

## <a name="how-to-get-the-latest-version-of-a-platform-os-image"></a>플랫폼 OS 이미지의 최신 버전을 가져오는 방법

아래 예제를 사용 하 여 자동 OS 업그레이드 지원 Sku에 대 한 사용 가능한 이미지 버전을 가져올 수 있습니다.

### <a name="rest-api"></a>REST API
```
GET on `/subscriptions/subscription_id/providers/Microsoft.Compute/locations/{location}/publishers/{publisherName}/artifacttypes/vmimage/offers/{offer}/skus/{skus}/versions?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
Get-AzVmImage -Location "westus" -PublisherName "Canonical" -Offer "UbuntuServer" -Skus "16.04-LTS"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
```azurecli-interactive
az vm image list --location "westus" --publisher "Canonical" --offer "UbuntuServer" --sku "16.04-LTS" --all
```

## <a name="manually-trigger-os-image-upgrades"></a>OS 이미지 업그레이드 수동 트리거
크기 집합에서 자동 OS 이미지 업그레이드를 사용 하도록 설정 하면 확장 집합에서 이미지 업데이트를 수동으로 트리거할 필요가 없습니다. OS 업그레이드 오 케 스트레이 터는 수동 개입 없이 확장 집합 인스턴스에 사용 가능한 최신 이미지 버전을 자동으로 적용 합니다.

Orchestrator가 최신 이미지를 적용할 때까지 기다리지 않으려는 특정 경우에는 아래 예제를 사용 하 여 OS 이미지 업그레이드를 수동으로 트리거할 수 있습니다.

> [!NOTE]
> OS 이미지 업그레이드의 수동 트리거는 자동 롤백 기능을 제공 하지 않습니다. 업그레이드 작업 후에 인스턴스가 상태를 복구 하지 않으면 이전 OS 디스크를 복원할 수 없습니다.

### <a name="rest-api"></a>REST API
[OS 업그레이드 시작](/rest/api/compute/virtualmachinescalesetrollingupgrades/startosupgrade) API 호출을 사용 하 여 롤링 업그레이드를 시작 하 고 모든 가상 머신 확장 집합 인스턴스를 사용 가능한 최신 이미지 OS 버전으로 이동 합니다. 사용 가능한 최신 OS 버전을 이미 실행 중인 인스턴스는 영향을 받지 않습니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myScaleSet* 라는 확장 집합에 대해 롤링 OS 업그레이드를 시작 하는 방법을 자세히 설명 합니다.

```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/osRollingUpgrade?api-version=2019-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
[AzVmssRollingOSUpgrade](/powershell/module/az.compute/Start-AzVmssRollingOSUpgrade) cmdlet을 사용 하 여 확장 집합에 대 한 OS 업그레이드 기록을 확인 합니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myScaleSet* 라는 확장 집합에 대해 롤링 OS 업그레이드를 시작 하는 방법을 자세히 설명 합니다.

```azurepowershell-interactive
Start-AzVmssRollingOSUpgrade -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[Az vmss 롤링 업그레이드 시작](/cli/azure/vmss/rolling-upgrade#az-vmss-rolling-upgrade-start) 을 사용 하 여 확장 집합에 대 한 OS 업그레이드 기록을 확인 합니다. Azure CLI 2.0.47 이상을 사용합니다. 다음 예제에서는 *Myresourcegroup*이라는 리소스 그룹에서 *myScaleSet* 라는 확장 집합에 대해 롤링 OS 업그레이드를 시작 하는 방법을 자세히 설명 합니다.

```azurecli-interactive
az vmss rolling-upgrade start --resource-group "myResourceGroup" --name "myScaleSet" --subscription "subscriptionId"
```

## <a name="deploy-with-a-template"></a>템플릿을 사용하여 배포

템플릿을 사용하여 [Ubuntu 16.04-LTS](https://github.com/Azure/vm-scale-sets/blob/master/preview/upgrade/autoupdate.json) 등의 지원되는 이미지에 대한 자동 OS 업그레이드를 통해 확장 집합을 배포할 수 있습니다.

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fvm-scale-sets%2Fmaster%2Fpreview%2Fupgrade%2Fautoupdate.json" target="_blank"><img src="https://azuredeploy.net/deploybutton.png" alt="Button to Deploy to Azure." /></a>

## <a name="next-steps"></a>다음 단계
확장 집합에 자동 OS 업그레이드를 사용하는 방법에 대한 추가 예제는 [GitHub 리포지토리](https://github.com/Azure/vm-scale-sets/tree/master/preview/upgrade)를 참조하세요.
