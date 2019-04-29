---
title: Azure Linux VM의 DPDK | Microsoft Docs
description: Linux 가상 머신에서 DPDK를 설정하는 방법에 대해 알아봅니다.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: c5cb840035c5d0d5694982324c7237c58001e689
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60731603"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Linux 가상 머신에서 DPDK 설정

Azure의 DPDK(데이터 평면 개발 키트)는 성능 집약적 애플리케이션에 대해 더 빠른 사용자 공간 패킷 처리 프레임워크를 제공합니다. 이 프레임워크에는 가상 머신의 커널 네트워크 스택을 무시합니다.

커널 네트워크 스택을 사용하는 일반적인 패킷 처리에서 프로세스는 인터럽트를 기준으로 합니다. 네트워크 인터페이스가 들어오는 패킷을 수신할 때 커널 공간에서 사용자 공간까지 패킷 및 컨텍스트 스위치를 처리하기 위한 커널 인터럽트가 있습니다. DPDK는 빠른 패킷 처리를 위한 폴링 모드 드라이버를 사용하는 사용자 공간 구현 대신 컨텍스트 전환 및 인터럽트 기반 메서드를 제거합니다.

DPDK는 하위 수준 리소스에 대한 액세스를 제공하는 사용자 공간 라이브러리의 집합으로 구성됩니다. 이러한 리소스는 하드웨어, 논리 코어, 메모리 관리 및 네트워크 인터페이스 카드의 폴링 모드 드라이버에 포함할 수 있습니다.

DPDK는 여러 운영 체제 배포를 지원하는 Azure Virtual Machines에서 실행될 수 있습니다. DPDK는 네트워크 기능 가상화 구현을 구동할 때 핵심 성과 차별화를 제공합니다. 이러한 구현은 가상 라우터, 방화벽, VPN, 부하 분산 장치, 발전된 패킷 코어 및 DDoS(서비스 거부) 애플리케이션 같은 NVA(네트워크 가상 어플라이언스) 형식을 취할 수 있습니다.

## <a name="benefit"></a>혜택

**더 높은 패킷 / 초 (PPS)**: 커널 및 사용자 공간 패킷 기록 제어를 우회 컨텍스트 스위치를 제거 하 여 주기 수를 줄입니다. 또한 Azure Linux 가상 머신에서 초당 처리 패킷 속도를 향상시킵니다.


## <a name="supported-operating-systems"></a>지원되는 운영 체제

다음 배포는 Azure Gallery에서 지원됩니다.

| Linux OS     | 커널 버전        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-azure     |
| Ubuntu 18.04 | 4.15.0-1015-azure     |
| SLES 15      | 4.12.14-5.5-azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**사용자 지정 커널 지원**

나열되지 않은 모든 Linux 커널 버전은 [Azure 조정 Linux 커널 빌드용 패치](https://github.com/microsoft/azure-linux-kernel)를 참조하세요. 자세한 내용은 [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com)에 문의할 수도 있습니다. 

## <a name="region-support"></a>지역 지원

모든 Azure 지역에서는 DPDK를 지원합니다.

## <a name="prerequisites"></a>필수 조건

가속 네트워킹을 사용하면 Linux 가상 머신을 사용하도록 설정해야 합니다. 가상 머신에는 관리를 위한 하나의 인터페이스가 포함된 적어도 두 개 이상의 네트워크 인터페이스가 있어야 합니다. [가속 네트워킹을 사용하는 Linux 가상 머신을 만드는](create-vm-accelerated-networking-cli.md) 방법에 대해 알아봅니다.

## <a name="install-dpdk-dependencies"></a>DPDK 종속성 설치

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15"></a>SLES 15

**Azure 커널**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**기본 커널**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>가상 머신 환경 설정(한 번)

1. [최신 DPDK 다운로드](https://core.dpdk.org/download). Azure에는 버전 18.02 이상이 필요합니다.
2. `make config T=x86_64-native-linuxapp-gcc`로 기본 구성을 빌드합니다.
3. `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`로 생성된 구성에서 Mellanox PMD를 사용하도록 설정합니다.
4. `make`를 사용하여 컴파일합니다.
5. `make install DESTDIR=<output folder>`를 사용하여 설치합니다.

## <a name="configure-the-runtime-environment"></a>런타임 환경 구성

다시 시작한 후 다음 명령을 한 번 실행합니다.

1. Hugepage

   * 모든 numanode에 대해 한 번씩, 다음 명령을 실행하여 hugepage를 구성합니다.

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * `mkdir /mnt/huge`를 사용하여 탑재할 디렉터리를 만듭니다.
   * `mount -t hugetlbfs nodev /mnt/huge`를 사용하여 hugepage를 탑재합니다.
   * `grep Huge /proc/meminfo`를 사용하여 hugepage가 예약되었는지 확인합니다.

     > [!NOTE]
     > DPDK에 대한 [지침](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment)을 따라 부팅 시 hugepage가 예약되도록 grub 파일을 수정할 방법이 있습니다. 지침은 페이지 맨 아래에 있습니다. Azure Linux 가상 머신에서 사용하는 경우 다시 부팅 시에 hugepage를 예약하려면 대신 **/etc/config/grub.d**에서 파일을 수정합니다.

2. MAC 및 IP 주소: 사용 하 여 `ifconfig –a` 네트워크 인터페이스의 MAC 및 IP 주소를 봅니다. *VF* 네트워크 인터페이스 및 *NETVSC* 네트워크 인터페이스에는 동일한 MAC 주소가 있지만 *NETVSC* 네트워크 인터페이스에만 IP 주소가 있습니다. NETVSC 인터페이스는 NETVSC 인터페이스의 슬레이브 인터페이스로 실행됩니다.

3. PCI 주소

   * `ethtool -i <vf interface name>`을 사용하여 *VF*에 사용할 PCI 주소를 찾습니다.
   * *eth0*이 가속 네트워킹을 사용하도록 설정한 경우 testpmd가 *eth0*에 대한 VF pci 디바이스를 실수로 넘겨받지 않도록 해야 합니다. DPDK 애플리케이션이 실수로 관리 네트워크 인터페이스를 넘겨받아 SSH 연결 손실을 초래한 경우 직렬 콘솔을 사용하여 DPDK 애플리케이션을 중지합니다. 또는 직렬 콘솔을 사용하여 가상 머신을 중지 또는 시작할 수도 있습니다.

4. `modprobe -a ib_uverbs`로 다시 부팅할 때마다 *ibuverbs*를 로드합니다. SLES 15의 경우만 `modprobe -a mlx4_ib`로 *mlx4_ib*를 로드합니다.

## <a name="failsafe-pmd"></a>Failsafe PMD

DPDK 애플리케이션은 Azure에서 공개되는 failsafe PMD에서 실행되어야 합니다. 애플리케이션이 직접 VF PMD에서 실행되는 경우 일부 패킷이 가상 인터페이스에 표시되므로 VM을 대상으로 한 **모든** 패킷을 수신하지는 않습니다. 

Failsafe PMD 통해 DPDK 애플리케이션을 실행하는 경우 애플리케이션이 대상으로 지정된 모든 패킷을 수신하게 됩니다. 또한 호스트에 서비스가 제공될 때 VF가 호출되더라도 애플리케이션은 DPDK 모드에서 계속 실행됩니다. Failsafe PMD에 대한 자세한 내용은 [Failsafe 폴링 모드 드라이버 라이브러리](https://doc.dpdk.org/guides/nics/fail_safe.html)를 참조하세요.

## <a name="run-testpmd"></a>testpmd 실행

루트 모드에서 testpmd를 실행하려면 *testpmd* 명령 전에 `sudo`를 사용합니다.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>기본: 온전성을 검사 failsafe 어댑터 초기화

1. 단일 포트 testpmd 애플리케이션을 시작하려면 다음 명령을 실행합니다.

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. 이중 포트 testpmd 애플리케이션을 시작하려면 다음 명령을 실행합니다.

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   2개를 초과하는 NIC를 사용하여 testpmd를 실행하는 경우 `--vdev` 인수는 `net_vdev_netvsc<id>,iface=<vf’s pairing eth>` 패턴을 따릅니다.

3.  시작되면 `show port info all`을 실행하여 포트 정보를 확인합니다. net_failsafe인(*net_mlx4*가 아니라) 하나 또는 두 개의 DPDK 포트가 표시돼야 합니다.
4.  트래픽을 시작하려면 `start <port> /stop <port>`를 사용합니다.

이전 명령은 testpmd에 권장되는 대화형 모드로 *testpmd*를 시작합니다.

### <a name="basic-single-sendersingle-receiver"></a>기본: 단일 발신자/단일 수신기

다음 명령은 초당 패킷 통계를 주기적으로 인쇄합니다.

1. TX 측에서 다음 명령을 실행합니다.

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. RX 측에서 다음 명령을 실행합니다.

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

가상 머신에서 이전 명령을 실행하는 경우 컴파일하기 전에 가상 머신의 실제 IP 주소와 일치하도록 `app/test-pmd/txonly.c`에서 *IP_SRC_ADDR* 및 *IP_DST_ADDR*을 변경합니다. 그렇지 않으면 패킷은 수신자에 도달하기 전에 삭제됩니다.

### <a name="advanced-single-sendersingle-forwarder"></a>고급: 단일 발신자/단일 전달자
다음 명령은 초당 패킷 통계를 주기적으로 인쇄합니다.

1. TX 측에서 다음 명령을 실행합니다.

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. FWD 측에서 다음 명령을 실행합니다.

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

가상 머신에서 이전 명령을 실행하는 경우 컴파일하기 전에 가상 머신의 실제 IP 주소와 일치하도록 `app/test-pmd/txonly.c`에서 *IP_SRC_ADDR* 및 *IP_DST_ADDR*을 변경합니다. 그렇지 않으면 패킷은 전달자에게 도달하기 전에 삭제됩니다. *testpmd* 전달자가 일부 코드를 변경하지 않는 경우 3계층 주소를 수정하지 않기 때문에 세 번째 머신은 전달된 트래픽을 수신할 수 없습니다.

## <a name="references"></a>참조

* [EAL 옵션](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd 명령](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
