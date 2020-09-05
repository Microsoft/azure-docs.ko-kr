---
title: 자습서 - Azure CLI를 사용하여 자동으로 확장 집합 크기 조정
description: Azure CLI를 사용하여 CPU 요구량이 늘거나 줄어듦에 따라 가상 머신 확장 집합의 크기를 자동으로 조정하는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 05/18/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurecli
ms.openlocfilehash: cdad018de9f7683d68f1b6d3b63c722134b2bc80
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88783776"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-the-azure-cli"></a>자습서: Azure CLI를 사용하여 가상 머신 확장 집합의 크기 자동 조정

확장 집합을 만들 때 실행하려는 VM 인스턴스 수를 정의합니다. 애플리케이션 수요가 변경될 때는 VM 인스턴스 수를 자동으로 늘리거나 줄일 수 있습니다. 자동 크기 조정 기능을 사용하면 고객 수요에 따라 조정하거나 앱 수명 주기 동안 애플리케이션 성능 변화에 대응할 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 확장 집합에 자동 크기 조정 사용
> * 자동 크기 조정 규칙 만들기 및 사용
> * VM 인스턴스 스트레스 테스트 및 자동 크기 조정 규칙 트리거
> * 요구량이 줄면 자동으로 다시 크기 조정

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI를 로컬로 설치하고 사용하도록 선택하는 경우 이 자습서에서는 Azure CLI 버전 2.0.32 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]( /cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-a-scale-set"></a>확장 집합 만들기

다음과 같이 [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

이제 [az vmss create](/cli/azure/vmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 인스턴스 수가 *2*인 확장 집합을 만들고, SSH 키가 없는 경우 이 키를 생성합니다.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --instance-count 2 \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="define-an-autoscale-profile"></a>자동 크기 조정 프로필 정의

확장 집합에서 자동 크기 조정을 활성화하려면 먼저 자동 크기 조정 프로필을 정의합니다. 이 프로필은 기본, 최소, 최대 확장 집합 용량을 정의합니다. 이러한 제한을 통해 연속적으로 VM 인스턴스를 만들지 않고 비용을 제어하고, 축소 이벤트에 유지되는 최소 인스턴스 수로 허용 가능한 성능의 균형을 유지할 수 있습니다. [az monitor autoscale create](/cli/azure/monitor/autoscale#az-monitor-autoscale-create)를 사용하여 자동 크기 조정 프로필을 만듭니다. 다음 예제에서는 기본 및 최소 용량으로 VM 인스턴스 *2*개를 설정하고 최대 용량으로 *10*개를 설정합니다.

```azurecli-interactive
az monitor autoscale create \
  --resource-group myResourceGroup \
  --resource myScaleSet \
  --resource-type Microsoft.Compute/virtualMachineScaleSets \
  --name autoscale \
  --min-count 2 \
  --max-count 10 \
  --count 2
```

## <a name="create-a-rule-to-autoscale-out"></a>자동 크기 확장 규칙 만들기

애플리케이션 수요가 증가하면 확장 집합의 VM 인스턴스 부하가 증가합니다. 증가된 로드가 단순한 요구가 아닌 일관된 요구인 경우 확장 집합의 VM 인스턴스 수를 늘리도록 자동 크기 조정 규칙을 구성할 수 있습니다. 이러한 VM 인스턴스를 만들고 애플리케이션을 배포하면 확장 집합이 부하 분산 장치를 통해 트래픽을 분산하기 시작합니다. 모니터링할 메트릭(예: CPU 또는 디스크), 애플리케이션 로드가 지정된 임계값을 충족해야 하는 기간, 확장 집합에 추가할 VM 인스턴스 수를 제어합니다.

평균 CPU 로드가 5분간 70%를 초과할 경우 [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create)를 사용하여 확장 집합의 VM 인스턴스 수를 늘리는 규칙을 만들어 보겠습니다. 규칙이 트리거되면 VM 인스턴스 수가 3만큼 늘어납니다.

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU > 70 avg 5m" \
  --scale out 3
```

## <a name="create-a-rule-to-autoscale-in"></a>자동 크기 축소 규칙 만들기

저녁이나 주말에는 애플리케이션 수요가 줄어들 수 있습니다. 이 감소된 로드가 일정 기간 동안 일관성 있게 유지될 경우 확장 집합의 VM 인스턴스 수를 줄이도록 자동 크기 조정 규칙을 구성할 수 있습니다. 이 규모 감축 작업은 현재 수요를 충족하는 데 필요한 수의 인스턴스만 실행하므로 확장 집합의 실행 비용을 줄입니다.

평균 CPU 로드가 5분 동안 30% 미만일 경우 [az monitor autoscale rule create](/cli/azure/monitor/autoscale/rule#az-monitor-autoscale-rule-create)를 사용하여 확장 집합의 VM 인스턴스 수를 줄이는 다른 규칙을 만듭니다. 다음 예제에서는 VM 인스턴스 수를 축소하는 규칙을 정의합니다.

```azurecli-interactive
az monitor autoscale rule create \
  --resource-group myResourceGroup \
  --autoscale-name autoscale \
  --condition "Percentage CPU < 30 avg 5m" \
  --scale in 1
```

## <a name="generate-cpu-load-on-scale-set"></a>확장 집합에 CPU 로드 생성

자동 크기 조정 규칙을 테스트하려면 확장 집합의 VM 인스턴스에 약간의 CPU 로드를 생성합니다. 시뮬레이션된 CPU 로드로 인해 자동 크기 조정 규칙이 확장되고 VM 인스턴스 수가 늘어납니다. 시뮬레이션된 CPU 로드가 감소하면 자동 크기 조정 규칙이 축소되고 VM 인스턴스 수가 줄어듭니다.

먼저 [az vmss list-instance-connection-info](/cli/azure/vmss)를 사용하여 확장 집합의 VM 인스턴스에 연결할 주소와 포트를 나열합니다.

```azurecli-interactive
az vmss list-instance-connection-info \
  --resource-group myResourceGroup \
  --name myScaleSet
```

다음 예제 출력에서는 NAT(Network Address Translation) 규칙에서 트래픽을 전달하는 인스턴스 이름, 부하 분산 장치의 공용 IP 주소 및 포트 번호를 보여 줍니다.

```json
{
  "instance 1": "13.92.224.66:50001",
  "instance 3": "13.92.224.66:50003"
}
```

SSH를 첫 번째 VM 인스턴스에 연결합니다. 앞의 명령과 같이 `-p` 매개 변수를 사용하여 사용자 고유의 공용 IP 주소와 포트 번호를 지정합니다.

```console
ssh azureuser@13.92.224.66 -p 50001
```

로그인한 후 **stress** 유틸리티를 설치합니다. CPU 로드를 생성하는 *10*개 **stress** 작업자를 시작합니다. 이러한 작업자는 *420*초 동안 실행되어 자동 크기 조정 규칙에서 원하는 작업을 구현하는 데 충분합니다.

```console
sudo apt-get update
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

**stress**에서 *stress: info: [2688] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*와 비슷한 출력이 표시되면 *Enter* 키를 눌러 프롬프트로 돌아갑니다.

**stress**에서 CPU 로드를 생성하는지 확인하려면 **top** 유틸리티를 사용하여 활성 시스템 로드를 검사합니다.

```console
top
```

**top**을 종료한 다음, VM 인스턴스에 대한 연결을 닫습니다. **stress**가 VM 인스턴스에서 계속 실행됩니다.

```console
Ctrl-c
exit
```

이전 [az vmss list-instance-connection-info](/cli/azure/vmss)에서 나열된 포트 번호를 사용하여 두 번째 VM 인스턴스에 연결합니다.

```console
ssh azureuser@13.92.224.66 -p 50003
```

**stress**를 설치하고 실행한 다음, 이 두 번째 VM 인스턴스에서 10개의 작업자를 시작합니다.

```console
sudo apt-get -y install stress
sudo stress --cpu 10 --timeout 420 &
```

다시 **stress**에서 *stress: info: [2713] dispatching hogs: 10 cpu, 0 io, 0 vm, 0 hdd*와 비슷한 출력이 표시되면 *Enter* 키를 눌러 프롬프트로 돌아갑니다.

두 번째 VM 인스턴스에 대한 연결을 닫습니다. **stress**가 VM 인스턴스에서 계속 실행됩니다.

```console
exit
```

## <a name="monitor-the-active-autoscale-rules"></a>활성 자동 크기 조정 규칙 모니터링

확장 집합의 VM 인스턴스 수를 모니터링하려면 **watch**를 사용합니다. 각 VM 인스턴스의 **stress**에서 생성된 CPU 로드에 응답하여 자동 크기 조정 규칙에서 규모 확장 프로세스를 시작하는 데 5분이 걸립니다.

```azurecli-interactive
watch az vmss list-instances \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --output table
```

CPU 임계값이 충족되면 자동 크기 조정 규칙에서 확장 집합의 VM 인스턴스 수를 늘립니다. 다음 출력에서는 확장 집합의 크기가 자동으로 확장함에 따라 생성된 세 개의 VM을 보여 줍니다.

```output
Every 2.0s: az vmss list-instances --resource-group myResourceGroup --name myScaleSet --output table

  InstanceId  LatestModelApplied    Location    Name          ProvisioningState    ResourceGroup    VmId
------------  --------------------  ----------  ------------  -------------------  ---------------  ------------------------------------
           1  True                  eastus      myScaleSet_1  Succeeded            myResourceGroup  4f92f350-2b68-464f-8a01-e5e590557955
           2  True                  eastus      myScaleSet_2  Succeeded            myResourceGroup  d734cd3d-fb38-4302-817c-cfe35655d48e
           4  True                  eastus      myScaleSet_4  Creating             myResourceGroup  061b4c90-0d73-49fc-a066-19eab0b3d95c
           5  True                  eastus      myScaleSet_5  Creating             myResourceGroup  4beff8b9-4e65-40cb-9652-43899309da27
           6  True                  eastus      myScaleSet_6  Creating             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

**stress**가 초기 VM 인스턴스에서 중지되면 평균 CPU 로드가 정상으로 돌아갑니다. 또 다른 5분이 지나면 자동 크기 조정 규칙에서 VM 인스턴스 수를 축소합니다. 규모 감축 작업에서 가장 높은 ID가 있는 VM 인스턴스를 먼저 제거합니다. 확장 집합에서 가용성 집합 또는 가용성 영역을 사용하는 경우 규모 감축 작업은 해당 VM 인스턴스 간에 균등하게 분산됩니다. 다음 예제 출력에서는 확장 집합의 자동 크기를 확장하면서 삭제된 하나의 VM 인스턴스를 보여 줍니다.

```output
           6  True                  eastus      myScaleSet_6  Deleting             myResourceGroup  9e4133dd-2c57-490e-ae45-90513ce3b336
```

`Ctrl-c`을 사용하여 *watch*를 종료합니다. 확장 집합은 5분마다 계속 축소되며, 최소 인스턴스 수인 2에 도달할 때까지 하나의 VM 인스턴스를 제거합니다.

## <a name="clean-up-resources"></a>리소스 정리

확장 집합 및 추가 리소스를 제거하려면 [az group delete](/cli/azure/group)를 사용하여 리소스 그룹 및 모든 해당 리소스를 삭제합니다. `--no-wait` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다. `--yes` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure CLI를 사용하여 다음과 같이 확장 집합을 자동으로 확장하거나 축소하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 확장 집합에 자동 크기 조정 사용
> * 자동 크기 조정 규칙 만들기 및 사용
> * VM 인스턴스 스트레스 테스트 및 자동 크기 조정 규칙 트리거
> * 요구량이 줄면 자동으로 다시 크기 조정
