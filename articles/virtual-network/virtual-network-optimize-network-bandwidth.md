---
title: VM 네트워크 처리량 최적화 | Microsoft Docs
description: Azure Virtual Machine 네트워크 처리량을 최적화하는 방법을 알아봅니다.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: 50d7ca73e5e18f88f5d789e12fc7f26908e8b8f0
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125783"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Azure Virtual Machine에 대한 네트워크 처리량 최적화

Azure VM(Virtual Machine)에는 네트워크 처리량에 대해 추가로 최적화할 수 있는 기본 네트워크 설정이 있습니다. 이 문서에서는 Ubuntu, CentOS 및 Red Hat과 같은 주요 배포판을 비롯한 Microsoft Azure Windows 및 Linux VM에 대해 네트워크 처리량을 최적화하는 방법을 설명합니다.

## <a name="windows-vm"></a>Windows VM

Windows VM에서 [가속 네트워킹](create-vm-accelerated-networking-powershell.md)을 지원하는 경우 해당 기능을 사용하도록 설정하는 것이 최적의 처리량 구성이 됩니다. RSS(수신측 배율)를 사용하는 다른 모든 Windows VM은 RSS 없는 VM보다 더 높은 최대 처리량에 도달할 수 있습니다. RSS는 Windows VM에서 기본적으로 사용되지 않도록 설정될 수 있습니다. RSS를 사용할 수 있는지 확인하고, 현재 사용되지 않는 경우 사용하도록 설정하려면 다음 단계를 수행합니다.

1. `Get-NetAdapterRss` PowerShell 명령을 사용하여 네트워크 어댑터에 대해 RSS를 사용하도록 설정되어 있는지 확인합니다. `Get-NetAdapterRss`에서 반환된 다음 예제 출력에서 RSS는 사용되도록 설정되어 있지 않습니다.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. RSS를 사용하도록 설정하려면 다음 명령을 입력합니다.

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    이전 명령에는 출력이 없습니다. 이 명령은 NIC 설정을 변경했으므로 약 1분 동안 일시적으로 연결이 끊겼습니다. 연결이 끊긴 동안 다시 연결 중 대화 상자가 나타납니다. 일반적으로 세 번째 시도 후 연결이 복원합니다.
3. `Get-NetAdapterRss` 명령을 다시 입력하여 VM에서 RSS가 사용되도록 설정되어 있는지 확인합니다. 성공하면 다음 예제 출력이 반환됩니다.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Linux VM

RSS는 Azure Linux VM에 기본적으로 항상 사용되도록 설정됩니다. 2017년 10월 이후에 출시된 Linux 커널에는 Linux VM이 더 높은 네트워크 처리량을 얻도록 하는 새로운 네트워크 최적화 옵션이 포함되어 있습니다.

### <a name="ubuntu-for-new-deployments"></a>새 배포에 대한 Ubuntu

Ubuntu Azure 커널은 Azure에서 최고의 네트워크 성능을 제공하며 2017년 9월 21일부터 계속 기본 커널로 사용되었습니다. 이 커널을 가져오려면 먼저 다음과 같이 지원되는 최신 버전의 16.04-LTS를 설치합니다.

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

생성이 완료되면 다음 명령을 입력하여 최신 업데이트를 받습니다. 이 단계는 현재 Ubuntu Azure 커널을 실행 중인 VM에도 유효합니다.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Azure 커널이 이미 설치되어 있지만 오류로 인해 추가 업데이트 설치에 실패한 기존의 Ubuntu 배포에는 다음과 같은 선택적 명령 집합이 유용할 수 있습니다.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>기존 VM에 대한 Ubuntu Azure 커널 업그레이드

Azure Linux 커널로 업그레이드하면 처리량 성능을 대폭 개선할 수 있습니다. 이 커널이 있는지 확인하려면 커널 버전을 확인합니다.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

VM에 Azure 커널이 없으면 버전 번호가 일반적으로 "4.4"로 시작됩니다. 이 경우 다음 명령을 루트로 실행합니다.

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

최신 최적화 기능을 사용하려면 다음 매개 변수를 지정하여 지원되는 최신 버전으로 VM을 만드는 것이 가장 좋습니다.

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

신규 및 기존 VM에 최신 LIS(Linux Integration Services)를 설치하면 이점을 얻을 수 있습니다. 더 나중 버전에 추가 개선 기능이 포함되어 있더라도 처리량 최적화 기능은 LIS 4.2.2-2부터 포함됩니다. 다음 명령을 입력하여 최신 LIS를 설치합니다.

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

최적화 기능을 사용하려면 다음 매개 변수를 지정하여 지원되는 최신 버전으로 VM을 만드는 것이 가장 좋습니다.

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

신규 및 기존 VM에 최신 LIS(Linux Integration Services)를 설치하면 이점을 얻을 수 있습니다. 처리량 최적화 기능은 LIS 4.2부터 포함됩니다. 다음 명령을 입력하여 LIS를 다운로드한 후 설치합니다.

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

[다운로드 페이지](https://www.microsoft.com/download/details.aspx?id=55106)를 확인하여 Hyper-V용 Linux Integration Services 버전 4.2에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계
* 시나리오에 대한 [Azure VM 대역폭/처리량 테스트](virtual-network-bandwidth-testing.md)를 통해 최적화된 결과를 확인합니다.
* [가상 머신에 대역폭이 할당되는 방법](virtual-machine-network-throughput.md)을 알아봅니다.
* [Azure Virtual Network FAQ(질문과 대답)](virtual-networks-faq.md)에 대해 자세히 알아보기
