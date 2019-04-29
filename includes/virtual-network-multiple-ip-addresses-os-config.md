---
title: 포함 파일
description: 포함 파일
services: virtual-network
author: jimdial
ms.service: virtual-network
ms.topic: include
ms.date: 04/09/2018
ms.author: jdial
ms.custom: include file
ms.openlocfilehash: 7679bbc450e5fa0761860aedbb37ed02b27ec828
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60742292"
---
## <a name="os-config"></a>VM 운영 체제에 IP 주소 추가

여러 개인 IP 주소를 사용하여 만든 VM에 연결하고 로그인합니다. VM에 추가한 모든 개인 IP 주소(기본 포함)를 수동으로 추가해야 합니다. VM 운영 체제에 대한 다음 단계를 완료합니다.

### <a name="windows"></a>Windows

1. 명령 프롬프트에서 *ipconfig /all*을 입력합니다.  *기본* 개인 IP 주소(DHCP를 통한)만 표시됩니다.
2. 명령 프롬프트 창에 *ncpa.cpl*을 입력하여 **네트워크 연결** 창을 엽니다.
3. 적절 한 어댑터에 대 한 속성을 엽니다. **로컬 영역 연결**합니다.
4. IPv4(인터넷 프로토콜 버전 4)를 두 번 클릭합니다.
5. **다음 IP 주소 사용**을 선택하고 다음 값을 입력합니다.

    * **IP 주소**: 입력 된 *기본* 개인 IP 주소
    * **서브넷 마스크**: 서브넷을 기준으로 하는 집합입니다. 예를 들어 서브넷이 /24이면 서브넷 마스크는 255.255.255.0입니다.
    * **기본 게이트웨이**: 서브넷의 첫 번째 IP 주소를 지정 합니다. 서브넷이 10.0.0.0/24이면 게이트웨이 IP 주소는 10.0.0.1입니다.
    * **다음 DNS 서버 주소 사용** 을 선택하고 다음 값을 입력합니다.
        * **기본 설정된 DNS 서버**: 사용자 고유의 DNS 서버를 사용 하지 않는 경우 168.63.129.16을 입력 합니다.  자체 DNS 서버를 사용하는 경우 서버의 IP 주소를 입력합니다.
    * **고급** 단추를 선택하고 추가 IP 주소를 추가합니다. 이전 단계에서 Azure 네트워크 인터페이스에 추가한 각각의 보조 개인 IP 주소를 Azure 네트워크 인터페이스에 할당된 기본 IP 주소에 할당되어 있는 Windows 네트워크 인터페이스에 추가합니다.

        가상 머신의 운영 체제 내에서 Azure Virtual Machine에 할당된 공용 IP 주소는 절대 수동으로 할당하면 안 됩니다. 운영 체제 내에서 IP 주소를 수동으로 설정하는 경우 Azure [네트워크 인터페이스](../articles/virtual-network/virtual-network-network-interface-addresses.md#change-ip-address-settings)에 할당된 개인 IP 주소와 동일한 주소인지 확인합니다. 두 주소가 같지 않으면 가상 머신에 대한 연결이 끊어질 수 있습니다. [개인 IP 주소](../articles/virtual-network/virtual-network-network-interface-addresses.md#private) 설정에 대해 자세히 알아봅니다. Azure 공용 IP 주소는 절대 운영 체제 내에서 할당하지 않아야 합니다.

    * **확인**을 클릭하여 TCP/IP 설정을 닫은 다음 **확인**을 다시 클릭하여 어댑터 설정을 닫습니다. RDP 연결이 다시 설정됩니다.

6. 명령 프롬프트에서 *ipconfig /all*을 입력합니다. 추가한 모든 IP 주소가 표시되고 DHCP는 해제됩니다.
7. Azure에서 Windows에 대한 기본 IP 주소로 기본 IP 구성의 개인 IP 주소를 사용하도록 Windows를 구성합니다. 자세한 내용은 [여러 개의 IP 주소가 있는 Azure Windows VM에서 인터넷 액세스 없음](https://support.microsoft.com/help/4040882/no-internet-access-from-azure-windows-vm-that-has-multiple-ip-addresse)을 참조하세요. 

### <a name="validation-windows"></a>유효성 검사(Windows)

보조 IP 구성과 연결된 공용 IP를 통해 인터넷에 연결할 수 있는지 확인하기 위해서 위의 단계를 사용하여 IP를 제대로 추가하고 나면 다음 명령을 사용합니다.

```bash
ping -S 10.0.0.5 hotmail.com
```
>[!NOTE]
>보조 IP 구성의 경우 구성에 공용 IP 주소가 연결된 경우에만 인터넷에 ping할 수 있습니다. 기본 IP 구성의 경우 공용 IP 주소가 인터넷에 ping되지 않아도 됩니다.

### <a name="linux-ubuntu"></a>Linux(Ubuntu)

1. 터미널 창을 엽니다.
2. 루트 사용자인지 확인합니다. 그렇지 않으면 다음 명령을 입력합니다.

    ```bash
    sudo -i
    ```

3. 네트워크 인터페이스(‘eth0’이라고 가정)의 구성 파일을 업데이트합니다.

   * dhcp에 대한 기존 줄 항목을 유지합니다. 기본 IP 주소가 이전에 구성된 대로 유지됩니다.
   * 다음 명령을 사용하여 추가 정적 IP 주소에 대한 구성을 추가합니다.

       ```bash
       cd /etc/network/interfaces.d/
       ls
       ```

     .cfg 파일이 표시됩니다.
4. 파일을 엽니다. 파일 끝에 다음 줄이 있어야 합니다.

    ```bash
    auto eth0
    iface eth0 inet dhcp
    ```

5. 이 파일에 있는 줄 뒤에 다음 줄을 추가합니다.

    ```bash
    iface eth0 inet static
    address <your private IP address here>
    netmask <your subnet mask>
    ```

6. 다음 명령을 실행하여 파일을 저장합니다.

    ```bash
    :wq
    ```

7. 다음 명령을 사용하여 네트워크 인터페이스를 다시 설정합니다.

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

    > [!IMPORTANT]
    > 원격 연결을 사용하는 경우 같은 줄에서 ifdown 및 ifup을 둘 다 실행합니다.
    >

8. 다음 명령을 사용하여 네트워크 인터페이스에 IP 주소가 추가되는지 확인합니다.

    ```bash
    ip addr list eth0
    ```

    목록의 일부로 추가한 IP 주소가 표시되어야 합니다.

### <a name="linux-red-hat-centos-and-others"></a>Linux(Red Hat, CentOS 및 기타)

1. 터미널 창을 엽니다.
2. 루트 사용자인지 확인합니다. 그렇지 않으면 다음 명령을 입력합니다.

    ```bash
    sudo -i
    ```

3. 암호를 입력하고 화면 지시를 따릅니다. 루트 사용자인 경우 다음 명령을 사용하여 네트워크 스크립트 폴더로 이동합니다.

    ```bash
    cd /etc/sysconfig/network-scripts
    ```

4. 다음 명령을 사용하여 관련 ifcfg 파일을 나열합니다.

    ```bash
    ls ifcfg-*
    ```

    *ifcfg-eth0* 이 파일 중 하나로 표시되어야 합니다.

5. IP 주소를 추가하려면 아래와 같이 그에 대한 구성 파일을 만듭니다. 각 IP 구성에 대해 하나의 파일을 만들어야 합니다.

    ```bash
    touch ifcfg-eth0:0
    ```

6. 다음 명령을 사용하여 *ifcfg-eth0:0* 파일을 엽니다.

    ```bash
    vi ifcfg-eth0:0
    ```

7. 이 경우에 다음 명령을 사용하여 *eth0:0* 파일에 내용을 추가합니다. IP 주소에 따라 정보를 업데이트해야 합니다.

    ```bash
    DEVICE=eth0:0
    BOOTPROTO=static
    ONBOOT=yes
    IPADDR=192.168.101.101
    NETMASK=255.255.255.0
    ```

8. 다음 명령을 실행하여 파일을 저장합니다.

    ```bash
    :wq
    ```

9. 다음 명령을 실행하여 네트워크 서비스를 다시 시작하고 변경이 성공적으로 수행되었는지 확인합니다.

    ```bash
    /etc/init.d/network restart
    ifconfig
    ```

    반환된 목록에서 추가한 IP 주소 *eth0:0*이 표시되어야 합니다.

### <a name="validation-linux"></a>유효성 검사(Linux)

보조 IP 구성과 연결된 공용 IP를 통해 인터넷에 연결할 수 있는지 확인하기 위해서 다음 명령을 사용합니다.

```bash
ping -I 10.0.0.5 hotmail.com
```
>[!NOTE]
>보조 IP 구성의 경우 구성에 공용 IP 주소가 연결된 경우에만 인터넷에 ping할 수 있습니다. 기본 IP 구성의 경우 공용 IP 주소가 인터넷에 ping되지 않아도 됩니다.

Linux VM의 경우 보조 NIC의 아웃바운드 연결에 대한 유효성 검사를 시도하는 경우 적절한 경로를 추가해야 할 수 있습니다. 이 작업을 수행하는 방법은 많이 있습니다. Linux 배포에 대한 적절한 설명서를 참조하세요. 다음은 이 작업을 수행하는 한 가지 방법입니다.

```bash
echo 150 custom >> /etc/iproute2/rt_tables 

ip rule add from 10.0.0.5 lookup custom
ip route add default via 10.0.0.1 dev eth2 table custom

```
- 반드시 대체할 항목:
    - **10.0.0.5**를 공용 IP 주소가 연결되어 있는 개인 IP 주소로 대체해야 합니다.
    - **10.0.0.1**을 기본 게이트웨이로 대체해야 합니다.
    - **eth2**를 보조 NIC의 이름으로 대체해야 합니다.
