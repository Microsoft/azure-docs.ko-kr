---
title: 다중 NIC이 있는 VM(클래식) 만들기 - Azure 클래식 CLI | Microsoft Docs
description: Azure 클래식 CLI(명령줄 인터페이스)를 사용하여 다중 NIC가 있는 VM(클래식)을 만드는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: b436e41e-866c-439f-a7c7-7b4b041725ef
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1e47b1e548516960c6aab3c48d64255370c94a77
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60743311"
---
# <a name="create-a-vm-classic-with-multiple-nics-using-the-azure-classic-cli"></a>Azure 클래식 CLI를 사용하여 다중 NIC가 있는 VM(클래식) 만들기

[!INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Azure에서 VM(가상 머신)을 만들고 각 VM에 여러 NIC(네트워크 인터페이스)를 연결할 수 있습니다. 여러 NIC를 사용하면 NIC 간에 트래픽 유형을 분리할 수 있습니다. 예를 들어 하나의 NIC는 인터넷과 통신하는 동안 다른 NIC는 인터넷에 연결되지 않은 내부 리소스와만 통신할 수 있습니다. 여러 NIC 간에 네트워크 트래픽을 분리하는 기능은 애플리케이션 전달 및 WAN 최적화 솔루션과 같은 많은 네트워크 가상 어플라이언스에 필요합니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한  [Resource Manager 및 클래식](../resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. [Resource Manager 배포 모델](../virtual-machines/linux/multiple-nics.md)을 사용하여 이러한 단계를 수행하는 방법을 알아봅니다.

[!INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

다음 단계에서는 WEB 서버에 *IaaSStory*라는 리소스 그룹을, DB 서버에 *IaaSStory-BackEnd*라는 리소스 그룹을 사용합니다.

## <a name="prerequisites"></a>필수 조건
DB 서버를 만들려면 먼저 이 시나리오에 필요한 모든 리소스로 *IaaSStory* 리소스 그룹을 만들어야 합니다. 이러한 리소스를 만들려면 다음 단계를 완료합니다. [가상 네트워크 만들기](virtual-networks-create-vnet-classic-cli.md) 문서에 나오는 단계를 따라 VNet을 만듭니다.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>백 엔드 VM 배포
백 엔드 VM은 만드는 리소스에 따라 다음과 같이 다릅니다.

* **데이터 디스크용 Storage 계정**. 성능 향상을 위해 데이터베이스 서버의 데이터 디스크는 SSD(반도체 드라이브) 기술을 사용하며, 이 기술에는 Premium Storage 계정이 필요합니다. 배포할 Azure 위치에서 Premium Storage가 지원되는지 확인하세요.
* **NIC**. 각 VM에 데이터베이스 액세스용으로 하나, 그리고 관리용으로 하나씩, 두 개의 NIC가 사용됩니다.
* **가용성 집합**. 모든 데이터베이스 서버가 단일 가용성 집합에 추가되어, 유지 관리 도중에 하나 이상의 VM이 실행 중이도록 합니다.

### <a name="step-1---start-your-script"></a>1단계 - 스크립트 시작
사용되는 전체 bash 스크립트를 [여기](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh)에서 다운로드할 수 있습니다. 다음 단계에 완료하여 스크립트가 사용자 환경에서 작동하도록 변경합니다.

1. 위의 [필수 조건](#prerequisites)에서 배포한 기존 리소스 그룹을 기반으로 다음 변수 값을 변경합니다.

    ```azurecli
    location="useast2"
    vnetName="WTestVNet"
    backendSubnetName="BackEnd"
    ```
2. 백 엔드 배포에 사용하려는 값을 기반으로 다음 변수 값을 변경합니다.

    ```azurecli
    backendCSName="IaaSStory-Backend"
    prmStorageAccountName="iaasstoryprmstorage"
    image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
    avSetName="ASDB"
    vmSize="Standard_DS3"
    diskSize=127
    vmNamePrefix="DB"
    osDiskName="osdiskdb"
    dataDiskPrefix="db"
    dataDiskName="datadisk"
    ipAddressPrefix="192.168.2."
    username='adminuser'
    password='adminP@ssw0rd'
    numberOfVMs=2
    ```

### <a name="step-2---create-necessary-resources-for-your-vms"></a>2단계 - VM에 필요한 리소스 만들기
1. 모든 백 엔드 VM에 대해 새 클라우드 서비스를 만듭니다. 리소스 그룹 이름에 `$backendCSName` 변수가, Azure 지역에 대해서는 `$location`이 사용되었습니다.

    ```azurecli
    azure service create --serviceName $backendCSName \
        --location $location
    ```

2. VM에서 사용할 OS 및 데이터 디스크에 대해 Premium Storage 계정을 만듭니다.

    ```azurecli
    azure storage account create $prmStorageAccountName \
        --location $location \
        --type PLRS
    ```

### <a name="step-3---create-vms-with-multiple-nics"></a>3단계 - 여러 NIC를 사용하여 VM 만들기
1. `numberOfVMs` 변수를 기반으로 하여 여러 VM을 만드는 루프를 시작합니다.

    ```azurecli
    for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
    do
    ```

2. 각 VM에 대해 NIC 두 개의 이름과 IP 주소를 각각 지정합니다.

    ```azurecli
    nic1Name=$vmNamePrefix$suffixNumber-DA
    x=$((suffixNumber+3))
    ipAddress1=$ipAddressPrefix$x

    nic2Name=$vmNamePrefix$suffixNumber-RA
    x=$((suffixNumber+53))
    ipAddress2=$ipAddressPrefix$x
    ```

3. VM을 만듭니다. 이름, 서브넷, IP 주소와 함께 모든 NIC 목록을 포함하는 `--nic-config` 매개 변수 사용에 유의합니다.

    ```azurecli
    azure vm create $backendCSName $image $username $password \
        --connect $backendCSName \
        --vm-name $vmNamePrefix$suffixNumber \
        --vm-size $vmSize \
        --availability-set $avSetName \
        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
        --virtual-network-name $vnetName \
        --subnet-names $backendSubnetName \
        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::
    ```

4. 각 VM에 대해 두 개의 데이터 디스크를 만듭니다.

    ```azurecli
    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

    azure vm disk attach-new $vmNamePrefix$suffixNumber \
        $diskSize \
        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
    done
    ```

### <a name="step-4---run-the-script"></a>4단계 - 스크립트 실행
스크립트를 다운로드하여 요구에 맞게 변경했으므로, 이제 이 스크립트를 실행하여 여러 NIC를 사용하여 백 엔드 데이터베이스 VM을 만듭니다.

1. 스크립트를 저장하고 **Bash** 터미널에서 실행합니다. 아래와 같이 초기 출력에 표시됩니다.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. 몇 분 후에 실행이 종료되고 아래와 같이 출력의 나머지 부분이 표시됩니다.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK

### <a name="step-5---configure-routing-within-the-vms-operating-system"></a>5단계 - VM의 운영 체제 내에서 라우팅 구성

Azure DHCP는 가상 머신에 연결된 첫 번째(기본) 네트워크 인터페이스에 기본 게이트웨이를 할당합니다. 가상 머신에 연결된 추가(보조) 네트워크 인터페이스에는 기본 게이트웨이를 할당하지 않습니다. 따라서 보조 네트워크 인터페이스가 있는 서브넷 외부의 리소스와는 기본적으로 통신할 수 없습니다. 그러나 보조 네트워크 인터페이스는 서브넷 외부의 리소스와 통신할 수 있습니다. 보조 네트워크 인터페이스에 라우팅을 구성하려면 [여러 네트워크 인터페이스를 사용하여 가상 머신 운영 체제 내에서 라우팅](virtual-network-network-interface-vm.md)을 참조하세요.
