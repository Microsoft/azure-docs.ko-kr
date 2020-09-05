---
title: 자습서 - Azure에서 Linux 가상 머신 모니터링
description: 이 자습서에서는 Linux 가상 머신에서 실행되는 성능 및 검색된 애플리케이션 구성 요소를 모니터링하는 방법에 대해 알아봅니다.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mgoedtel
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/30/2019
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: c8d7b13f9e35a41a414a44c908997cfcc550af41
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89011738"
---
# <a name="tutorial-monitor-a-linux-virtual-machine-in-azure"></a>자습서: Azure에서 Linux 가상 머신 모니터링

Azure 모니터링은 에이전트를 사용하여 Azure VM에서 부팅 및 성능 데이터를 수집하고 이 데이터를 Azure Storage에 저장하며 포털, Azure PowerShell 모듈 및 Azure CLI를 통해 액세스할 수 있도록 합니다. 고급 모니터링은 성능 메트릭을 수집하고, VM에 설치된 애플리케이션 구성 요소를 검색하고, 성능 차트와 종속성 맵을 포함하여 VM용 Azure Monitor와 함께 제공됩니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * VM에서 부팅 진단을 사용하도록 설정
> * 부트 진단 보기
> * VM 호스트 메트릭 보기
> * VM용 Azure Monitor 사용
> * VM 성능 메트릭 보기
> * 경고 만들기

## <a name="launch-azure-cloud-shell"></a>Azure Cloud Shell 시작

Azure Cloud Shell은 이 항목의 단계를 실행하는 데 무료로 사용할 수 있는 대화형 셸입니다. 공용 Azure 도구가 사전 설치되어 계정에서 사용하도록 구성되어 있습니다. 

Cloud Shell을 열려면 코드 블록의 오른쪽 위 모서리에 있는 **사용해 보세요**를 선택하기만 하면 됩니다. 또한 [https://shell.azure.com/powershell](https://shell.azure.com/powershell)로 이동하여 별도의 브라우저 탭에서 Cloud Shell을 시작할 수도 있습니다. **복사**를 선택하여 코드 블록을 복사하여 Cloud Shell에 붙여넣고, Enter 키를 눌러 실행합니다.

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 자습서에서 Azure CLI 버전 2.0.30 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드가 필요한 경우, [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-vm"></a>VM 만들기

작동 중인 진단과 메트릭을 확인하려면 VM이 필요합니다. 먼저 [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroupMonitor*라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroupMonitor --location eastus
```

이제 [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create)로 VM을 만듭니다. 다음 예제에서는 *myVM*이라는 VM을 만들고 SSH 키가 *~/.ssh/* 에 없으면 생성합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="enable-boot-diagnostics"></a>부팅 진단을 사용하도록 설정

Linux VM이 부팅할 때 부팅 진단 확장에서는 부팅 출력을 캡처하여 Azure Storage에 저장합니다. 이 데이터는 VM 부팅 문제를 해결하는 데 사용할 수 있습니다. Azure CLI를 사용하여 Linux VM을 만들 때 부팅 진단을 사용하도록 자동으로 설정되지 않습니다.

부팅 진단을 사용하도록 설정하기 전에 먼저 부팅 로그를 저장할 스토리지 계정을 만들어야 합니다. Storage 계정은 전역적으로 고유한 이름을 가져야 하며, 3-24자 사이의 숫자와 소문자만 포함해야 합니다. [az storage account create](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)를 사용하여 저장소 계정을 만듭니다. 이 예제에서는 임의의 문자열을 사용하여 고유한 스토리지 계정 이름을 만듭니다.

```azurecli-interactive
storageacct=mydiagdata$RANDOM

az storage account create \
  --resource-group myResourceGroupMonitor \
  --name $storageacct \
  --sku Standard_LRS \
  --location eastus
```

부팅 진단을 사용하도록 설정하는 경우 Blob Storage 컨테이너에 대한 URI가 필요합니다. 다음 명령은 스토리지 계정을 쿼리하여 이 URI를 반환합니다. URI 값은 *bloburi* 변수 이름에 저장되며 다음 단계에서 사용됩니다.

```azurecli-interactive
bloburi=$(az storage account show --resource-group myResourceGroupMonitor --name $storageacct --query 'primaryEndpoints.blob' -o tsv)
```

이제 [az vm boot-diagnostics enable](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-enable)을 사용하여 부팅 진단을 사용하도록 설정합니다. `--storage` 값은 이전 단계에서 수집된 Blob URI입니다.

```azurecli-interactive
az vm boot-diagnostics enable \
  --resource-group myResourceGroupMonitor \
  --name myVM \
  --storage $bloburi
```

## <a name="view-boot-diagnostics"></a>부트 진단 보기

부팅 진단을 사용하도록 설정되면, VM을 중지하고 시작할 때마다 부팅 프로세스에 대한 정보가 로그 파일에 기록됩니다. 이 예제에서는 먼저 다음과 같이 [az vm deallocate](/cli/azure/vm?view=azure-cli-latest#az-vm-deallocate) 명령을 사용하여 VM의 할당을 취소합니다.

```azurecli-interactive
az vm deallocate --resource-group myResourceGroupMonitor --name myVM
```

이제 다음과 같이 [az vm start](/cli/azure/vm?view=azure-cli-latest#az-vm-start) 명령을 사용하여 VM을 시작합니다.

```azurecli-interactive
az vm start --resource-group myResourceGroupMonitor --name myVM
```

다음과 같이 [az vm boot-diagnostics get-boot-log](/cli/azure/vm/boot-diagnostics#az-vm-boot-diagnostics-get-boot-log) 명령을 사용하여 *myVM*에 대한 부팅 진단 데이터를 얻을 수 있습니다.

```azurecli-interactive
az vm boot-diagnostics get-boot-log --resource-group myResourceGroupMonitor --name myVM
```

## <a name="view-host-metrics"></a>호스트 메트릭 보기

Linux VM에는 Azure에서 상호 작용하는 전용 호스트가 있습니다. 이 호스트에 대한 메트릭이 자동으로 수집되며, 다음과 같이 Azure Portal에서 볼 수 있습니다.

1. Azure Portal에서 **리소스 그룹**을 선택하고, **myResourceGroupMonitor**를 선택한 다음, 리소스 목록에서 **myVM**을 선택합니다.
1. 호스트 VM의 성능을 보려면 VM 창에서 **메트릭**을 클릭한 다음, **사용 가능한 메트릭**에서 *[호스트]* 메트릭 중 하나를 선택합니다.

    ![호스트 메트릭 보기](./media/tutorial-monitoring/monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>고급 모니터링 사용

VM용 Azure Monitor를 사용하여 Azure VM의 모니터링을 사용하도록 설정하려면 다음을 수행합니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroupMonitor**를 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.

2. VM 페이지에 **모니터링** 섹션에서 **인사이트(미리 보기)** 를 선택합니다.

3. **인사이트(미리 보기)** 페이지에서 **지금 시도해 보기**를 선택합니다.

    ![VM에 대해 VM용 Azure Monitor를 사용하도록 설정](../../azure-monitor/insights/media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

4. **Azure Monitor Insights 등록** 페이지에서 동일한 구독에 기존 Log Analytics 작업 영역이 있는 경우 드롭다운 목록에서 해당 작업 영역을 선택합니다.  

    이 목록은 구독에서 VM이 배포된 기본 작업 영역 및 위치를 미리 선택합니다. 

    >[!NOTE]
    >VM의 모니터링 데이터를 저장할 새 Log Analytics 작업 영역을 만들려면 [Log Analytics 작업 영역 만들기](../../azure-monitor/learn/quick-create-workspace.md)를 참조하세요. 작업 영역은 [지원 지역](../../azure-monitor/insights/vminsights-configure-workspace.md#supported-regions) 중 하나에 속해야 합니다.

모니터링을 사용하도록 설정한 후 몇 분 정도 기다려야 VM에 대한 성능 메트릭을 볼 수 있습니다.

![배포 처리를 모니터링하도록 VM용 Azure Monitor 설정](../../azure-monitor/insights/media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>VM 성능 메트릭 보기

VM용 Azure Monitor는 가상 머신이 얼마나 잘 실행되고 있는지 확인하기 위한 여러 가지 KPI(핵심 성과 지표)를 대상으로 하는 성능 차트 집합을 포함하고 있습니다. VM에서 액세스하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroupMonitor**를 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.

2. VM 페이지에 **모니터링** 섹션에서 **인사이트(미리 보기)** 를 선택합니다.

3. **성능** 탭을 선택합니다.

이 페이지에는 성능 사용률 차트뿐 아니라 검색된 각 논리 디스크, 용량, 사용률, 측정별 총 평균 사용률에 대한 표가 포함되어 있습니다.

## <a name="create-alerts"></a>경고 만들기

특정 성능 메트릭을 기반으로 하는 경고를 만들 수 있습니다. 예를 들어 평균 CPU 사용량이 특정 임계값을 초과하거나 사용 가능한 디스크 공간이 일정량 이하로 떨어지면 경고를 사용하여 사용자에게 알릴 수 있습니다. 경고는 Azure Portal에 표시되거나 전자 메일을 통해 보낼 수 있습니다. 또한 생성되는 경고에 대한 응답으로 Azure Automation Runbook 또는 Azure Logic Apps를 트리거할 수도 있습니다.

다음 예제에서는 평균 CPU 사용량에 대한 경고를 만듭니다.

1. Azure Portal에서 **리소스 그룹**을 클릭하고 **myResourceGroupMonitor**를 선택한 다음 리소스 목록에서 **myVM**을 선택합니다.

2. VM 블레이드에서 **경고 규칙**을 클릭한 다음, 경고 블레이드 위쪽에 있는 **메트릭 경고 추가**를 클릭합니다.

3. *myAlertRule*과 같이 경고에 대한 **이름**을 입력합니다.

4. CPU 사용률이 5분 동안 1.0을 초과할 때 경고를 트리거하려면 다른 모든 기본값을 선택한 상태로 둡니다.

5. 필요한 경우 전자 메일 알림을 보내도록 *전자 메일 소유자, 참가자 및 읽기 권한자*의 확인란을 선택합니다. 기본 작업은 포털에서 알림을 제공하는 것입니다.

6. **확인** 단추를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 VM의 성능을 구성하고 확인했습니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 리소스 그룹 및 VM 만들기
> * VM에서 부팅 진단을 사용하도록 설정
> * 부트 진단 보기
> * 호스트 메트릭 보기
> * VM용 Azure Monitor 사용
> * VM 메트릭 보기
> * 경고 만들기

Azure Security Center에 대해 알아보려면 다음 자습서로 진행합니다.

> [!div class="nextstepaction"]
> [VM 보안 관리](tutorial-azure-security.md)
