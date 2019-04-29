---
title: 내부 Load Balancer 만들기 - Azure 클래식 CLI
titlesuffix: Azure Load Balancer
description: Azure 클래식 CLI를 사용하여 클래식 배포 모델에서 내부 부하 분산 장치를 만드는 방법에 대해 알아봅니다.
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
ms.openlocfilehash: 991e6554df62591dea5c126f8ea82704373d6ffd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60781242"
---
# <a name="get-started-creating-an-internal-load-balancer-using-the-azure-classic-cli"></a>Azure 클래식 CLI를 사용하여 내부 부하 분산 장치 만들기 시작

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Cloud services](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure에는 리소스를 만들고 사용하기 위한  [Resource Manager 및 클래식](../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있습니다.  이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. [Resource Manager 모델을 사용하여 이러한 단계를 수행하는](load-balancer-get-started-ilb-arm-cli.md) 방법을 알아봅니다.

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="to-create-an-internal-load-balancer-set-for-virtual-machines"></a>가상 머신에 대한 내부 부하 분산 장치 집합 만들기

내부 부하 분산 장치 집합과 이 집합으로 해당 트래픽을 전송할 서버를 만들려면 다음을 수행해야 합니다.

1. 부하가 분산된 집합의 서버 간에 부하가 분산되어 들어오는 트래픽의 엔드포인트가 되는 내부 부하 분산의 인스턴스를 만듭니다.
2. 들어오는 트래픽을 수신할 수 있는 가상 머신에 해당하는 엔드포인트를 추가합니다.
3. 서버가 해당 트래픽을 내부 부하 분산 인스턴스의 VIP(가상 IP) 주소로 전송하도록 구성합니다.

## <a name="step-by-step-creating-an-internal-load-balancer-using-classic-cli"></a>클래식 CLI를 사용하여 내부 부하 분산 장치 만들기 단계별 지침

이 가이드에서는 위의 시나리오에 따라 내부 부하 분산 장치를 만드는 방법을 보여 줍니다.

1. 클래식 CLI를 처음 사용하는 경우 [Azure CLI 설치 및 구성](../cli-install-nodejs.md)을 참조하고 Azure 계정 및 구독을 선택하는 부분까지 관련 지침을 따릅니다.
2. 아래와 같이 **azure config mode** 명령을 실행하여 클래식 모드로 전환합니다.

    ```azurecli
    azure config mode asm
    ```

    예상 출력:

        info:    New mode is asm

## <a name="create-endpoint-and-load-balancer-set"></a>엔드포인트와 부하 분산 장치 집합 만들기

시나리오는 "mytestcloud"라는 클라우드 서비스에 가상 머신 "DB1" 및 "DB2"가 있다고 가정합니다. 두 가상 머신은 서브넷 "subnet-1"과 함께 내 "testvnet"이라는 가상 네트워크를 사용합니다.

이 가이드를 통해 개인 포트로 포트 1433과 로컬 포트로 포트 1433을 사용하는 내부 부하 분산 장치 집합을 만듭니다.

이는 일반적인 시나리오로, 백 엔드의 SQL 가상 머신은 데이터베이스 서버가 공용 IP 주소를 사용하여 직접 노출되지 않는다는 것을 보장하기 위해 내부 부하 분산 장치를 사용합니다.

### <a name="step-1"></a>1단계

`azure network service internal-load-balancer add`를 사용하여 내부 부하 분산 장치 집합을 만듭니다.

```azurecli
azure service internal-load-balancer add --serviceName mytestcloud --internalLBName ilbset --subnet-name subnet-1 --static-virtualnetwork-ipaddress 192.168.2.7
```

자세한 내용은 `azure service internal-load-balancer --help` 를 확인하세요.

`azure service internal-load-balancer list` *클라우드 서비스 이름*명령을 사용하여 내부 부하 분산 장치 속성을 확인할 수 있습니다.

다음은 출력의 예입니다.

    azure service internal-load-balancer list my-testcloud
    info:    Executing command service internal-load-balancer list
    + Getting cloud service deployment
    data:    Name    Type     SubnetName  StaticVirtualNetworkIPAddress
    data:    ------  -------  ----------  -----------------------------
    data:    ilbset  Private  subnet-1    192.168.2.7
    info:    service internal-load-balancer list command OK


### <a name="step-2"></a>2단계

첫 번째 엔드포인트를 추가할 때 내부 부하 분산 장치 집합을 구성합니다. 이 단계에서 엔드포인트, 가상 머신 및 프로브 포트를 내부 부하 분산 장치 집합에 연결할 수 있습니다.

```azurecli
azure vm endpoint create db1 1433 --local-port 1433 --protocol tcp --probe-port 1433 --probe-protocol tcp --probe-interval 300 --probe-timeout 600 --internal-load-balancer-name ilbset
```

### <a name="step-3"></a>3단계

`azure vm show`*가상 머신 이름*을 사용하여 부하 분산 장치 구성 확인

```azurecli
azure vm show DB1
```

출력은 다음과 같습니다.

    azure vm show DB1
    info:    Executing command vm show
    + Getting virtual machines
    data:    DNSName "mytestcloud.cloudapp.net"
    data:    Location "East US 2"
    data:    VMName "DB1"
    data:    IPAddress "192.168.2.4"
    data:    InstanceStatus "ReadyRole"
    data:    InstanceSize "Standard_D1"
    data:    Image "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk hostCaching "ReadWrite"
    data:    OSDisk name "db1-DB1-0-201511120457370846"
    data:    OSDisk mediaLink "https://XXXX.blob.core.windows.net/vhd"
    data:    OSDisk sourceImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-20151022-en.us-127GB.vhd"
    data:    OSDisk operatingSystem "Windows"
    data:    OSDisk iOType "Standard"
    data:    ReservedIPName ""
    data:    VirtualIPAddresses 0 address "137.116.64.107"
    data:    VirtualIPAddresses 0 name "db1ContractContract"
    data:    VirtualIPAddresses 0 isDnsProgrammed true
    data:    VirtualIPAddresses 1 address "192.168.2.7"
    data:    VirtualIPAddresses 1 name "ilbset"
    data:    Network Endpoints 0 localPort 5986
    data:    Network Endpoints 0 name "PowerShell"
    data:    Network Endpoints 0 port 5986
    data:    Network Endpoints 0 protocol "tcp"
    data:    Network Endpoints 0 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 0 enableDirectServerReturn false
    data:    Network Endpoints 1 localPort 3389
    data:    Network Endpoints 1 name "Remote Desktop"
    data:    Network Endpoints 1 port 60173
    data:    Network Endpoints 1 protocol "tcp"
    data:    Network Endpoints 1 virtualIPAddress "137.116.64.107"
    data:    Network Endpoints 1 enableDirectServerReturn false
    data:    Network Endpoints 2 localPort 1433
    data:    Network Endpoints 2 name "tcp-1433-1433"
    data:    Network Endpoints 2 port 1433
    data:    Network Endpoints 2 loadBalancerProbe port 1433
    data:    Network Endpoints 2 loadBalancerProbe protocol "tcp"
    data:    Network Endpoints 2 loadBalancerProbe intervalInSeconds 300
    data:    Network Endpoints 2 loadBalancerProbe timeoutInSeconds 600
    data:    Network Endpoints 2 protocol "tcp"
    data:    Network Endpoints 2 virtualIPAddress "192.168.2.7"
    data:    Network Endpoints 2 enableDirectServerReturn false
    data:    Network Endpoints 2 loadBalancerName "ilbset"
    info:    vm show command OK

## <a name="create-a-remote-desktop-endpoint-for-a-virtual-machine"></a>가상 머신을 위한 원격 데스크톱 엔드포인트 만들기

`azure vm endpoint create`을 사용하여 특정 가상 머신의 공용 포트에서 로컬 포트로 네트워크 트래픽을 전달하는 원격 데스크톱 엔드포인트를 만들 수 있습니다.

```azurecli
azure vm endpoint create web1 54580 -k 3389
```

## <a name="remove-virtual-machine-from-load-balancer"></a>부하 분산 장치에서 가상 컴퓨터 제거

연결된 된 엔드포인트를 삭제하여 내부 부하 분산 장치 집합에서 가상 머신을 제거할 수 있습니다. 엔드포인트가 제거되면 가상 머신은 더 이상 부하 분산 장치 집합에 속하지 않습니다.

위의 예제를 통해 명령 `azure vm endpoint delete`를 사용하여 내부 부하 분산 장치 "lbset"에서 가상 컴퓨터 "DB1"을 위해 만들어진 엔드포인트를 제거할 수 있습니다.

```azurecli
azure vm endpoint delete DB1 tcp-1433-1433
```

자세한 내용은 `azure vm endpoint --help` 를 확인하세요.

## <a name="next-steps"></a>다음 단계

[원본 IP 선호도를 사용하여 부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)

[부하 분산 장치에 대한 유휴 TCP 시간 제한 설정 구성](load-balancer-tcp-idle-timeout.md)
