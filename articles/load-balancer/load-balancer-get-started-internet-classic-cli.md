---
title: 인터넷 연결 Load Balancer 만들기 - Azure 클래식 CLI
titlesuffix: Azure Load Balancer
description: Azure 클래식 CLI를 사용하여 클래식 배포 모델에서 인터넷 연결 부하 분산 장치를 만드는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: genlin
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: fa89117e85bc3d3c9664e6aa037fac923b7432ce
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544898"
---
# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-the-azure-classic-cli"></a>Azure 클래식 CLI에서 인터넷 연결 Load Balancer(클래식) 만들기 시작

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-internet-classic-ps.md)
> * [Azure 클래식 CLI](../load-balancer/load-balancer-get-started-internet-classic-cli.md)
> * [Azure Cloud Services](../load-balancer/load-balancer-get-started-internet-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

> [!IMPORTANT]
> Azure 리소스로 작업하기 전에 Azure에는 현재 Azure Resource Manager 및 클래식이라는 두 가지 배포 모델이 있다는 것을 이해해야 합니다. Azure 리소스로 작업하기 전에 [배포 모델 및 도구](../azure-classic-rm.md) 를 이해해야 합니다. 이 문서의 윗부분에 있는 탭을 클릭하여 다양한 도구에 대한 설명서를 볼 수 있습니다. 이 문서에서는 클래식 배포 모델에 대해 설명합니다. 또한 [Azure 리소스 관리자를 사용하여 인터넷 연결 부하 분산 장치를 만드는 방법을 배울 수 있습니다](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [requires-classic-cli](../../includes/contains-classic-cli-content.md)]

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="create-an-internet-facing-load-balancer-using-cli"></a>CLI를 사용하여 인터넷 연결 부하 분산 장치 만들기

이 가이드에서는 위의 시나리오에 따라 인터넷 부하 분산 장치를 만드는 방법을 보여줍니다.

1. Azure 클래식 CLI를 처음 사용하는 경우 [Azure 클래식 CLI 설치 및 구성](../cli-install-nodejs.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.
2. 아래와 같이 **azure config mode** 명령을 실행하여 클래식 모드로 전환합니다.

    ```azurecli
    azure config mode asm
    ```

    예상 출력:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>엔드포인트와 부하 분산 장치 집합 만들기

시나리오는 가상 머신 "web1" 및 "web2"가 만들어졌다고 가정합니다.
이 가이드를 통해 공용 포트로 포트 80과 로컬 포트로 포트 80을 사용하여 부하 분산 장치 집합을 만듭니다. 또한 프로브 포트가 포트 80에 구성되고 부하 분산 장치 집합을 "lbset"로 지명합니다.

### <a name="step-1"></a>1단계

가상 머신 "web1"을 위해 `azure network vm endpoint create` 을 사용하여 첫 번째 엔드포인트와 부하 분산 장치 집합을 만듭니다.

```azurecli
azure vm endpoint create web1 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-2"></a>2단계

두 번째 가상 머신 "web2"를 부하 분산 장치 집합에 추가합니다.

```azurecli
azure vm endpoint create web2 80 --local-port 80 --protocol tcp --probe-port 80 --load-balanced-set-name lbset
```

### <a name="step-3"></a>3단계

`azure vm show` 를 사용하여 부하 분산 장치 구성을 확인합니다.

```azurecli
azure vm show web1
```

다음과 같이 출력됩니다.

    data:    DNSName "contoso.cloudapp.net"
    data:    Location "East US"
    data:    VMName "web1"
    data:    IPAddress "10.0.0.5"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-2015
    6-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "joaoma-1-web1-0-201509251804250879"
    data:    OSDisk mediaLink "https://XXXXXXXXXXXXXXX.blob.core.windows.
    /vhds/joaomatest-web1-2015-09-25.vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Se
    r-2012-R2-20150916-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "XXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 name "XXXXXXXXXXXXXXXXXXXX"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    Network Endpoints 0 loadBalancedEndpointSetName "lbset"
    data:    Network Endpoints 0 localPort 80
    data:    Network Endpoints 0 name "tcp-80-80"
    data:    Network Endpoints 0 port 80
    data:    Network Endpoints 0 loadBalancerProbe port 80
    data:    Network Endpoints 0 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 0 loadBalancerProbe intervalInSeconds 15
    data:    Network Endpoints 0 loadBalancerProbe timeoutInSeconds 31
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 5986
    data:    Network Endpoints 1 name "PowerShell"
    data:    Network Endpoints 1 port 5986
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "XXXXXXXXXXXX"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 3389
    data:    Network Endpoints 2 name "Remote Desktop"
    data:    Network Endpoints 2 port 58081
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>가상 머신을 위한 원격 데스크톱 엔드포인트 만들기

`azure vm endpoint create`을 사용하여 특정 가상 머신의 공용 포트에서 로컬 포트로 네트워크 트래픽을 전달하는 원격 데스크톱 엔드포인트를 만들 수 있습니다.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>부하 분산 장치에서 가상 머신 제거

가상 머신에서 부하 분산 장치 집합에 연결된 엔드포인트를 삭제해야 합니다. 엔드포인트가 제거되면 가상 컴퓨터는 더 이상 부하 분산 장치 집합에 속하지 않습니다.

위의 예제를 통해 명령 `azure vm endpoint delete`를 사용하여 부하 분산 장치 "lbset"에서 가상 머신 "web1"을 위해 만들어진 엔드포인트를 제거할 수 있습니다.

```azurecli
azure vm endpoint delete web1 tcp-80-80
```

> [!NOTE]
> 명령 `azure vm endpoint --help`를 사용하여 엔드포인트를 관리하는 더 많은 옵션을 탐색할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[내부 부하 분산 장치 구성 시작](load-balancer-get-started-ilb-arm-ps.md)

[부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
