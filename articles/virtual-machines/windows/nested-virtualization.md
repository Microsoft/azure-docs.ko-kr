---
title: Azure Virtual Machines에서 중첩된 가상화를 사용하는 방법 | Microsoft Docs
description: Azure Virtual Machines에서 중첩된 가상화를 사용하는 방법
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
ms.author: cynthn
ms.date: 10/09/2017
ms.topic: conceptual
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.openlocfilehash: f90ca51349eef92bd25095f5a2a10d7d181fdb2c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61488371"
---
# <a name="how-to-enable-nested-virtualization-in-an-azure-vm"></a>Azure VM에서 중첩된 가상화를 사용하는 방법

중첩된 가상화는 여러 Azure 가상 머신 제품군에서 지원됩니다. 이 기능은 개발, 테스트, 교육 및 데모 환경과 같은 시나리오를 지원하는 데 큰 유연성을 제공합니다.   

이 문서에서는 Azure VM에서 Hyper-V를 사용하도록 설정하고 해당 게스트 가상 머신에 대한 인터넷 연결을 구성하는 방법을 단계별로 설명합니다.

## <a name="create-a-nesting-capable-azure-vm"></a>중첩 지원 Azure VM 만들기

새 Windows Server 2016 Azure VM을 만듭니다.  빠른 참조에 대 한 모든 v3 virtual machines는 중첩 된 가상화를 지원합니다. 중첩을 지원하는 가상 머신 크기의 전체 목록은 [Azure 컴퓨팅 단위 문서](acu.md)를 참조하세요.

게스트 가상 머신의 수요를 지원할 만큼 큰 VM 크기를 선택해야 합니다. 이 예제에서는 D3_v3 크기의 Azure VM을 사용합니다. 

Dv3 또는 Ev3 시리즈 가상 머신의 지역별 가용성은 [여기](https://azure.microsoft.com/regions/services/)에서 볼 수 있습니다.

>[!NOTE]
>
>새 가상 머신을 만드는 방법에 대한 자세한 내용은 [Azure PowerShell 모듈을 사용하여 Windows VM 만들기 및 관리](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-manage-vm)를 참조하세요.
    
## <a name="connect-to-your-azure-vm"></a>Azure VM에 연결

가상 머신에 대한 원격 데스크톱 연결을 만듭니다.

1. 가상 머신 속성에서 **연결** 단추를 클릭합니다. 원격 데스크톱 프로토콜 파일(.rdp 파일)이 생성되고 다운로드됩니다.

2. VM에 연결하려면 다운로드한 RDP 파일을 엽니다. 메시지가 표시되면 **연결**을 클릭합니다. Mac의 Mac 앱 스토어에서 이 [원격 데스크톱 클라이언트](https://itunes.apple.com/us/app/microsoft-remote-desktop/id715768417?mt=12)와 같은 RDP 클라이언트가 필요합니다.

3. 가상 머신을 만들 때 지정한 사용자 이름 및 암호를 입력하고 **확인**을 클릭합니다.

4. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. **예** 또는 **계속**을 클릭하여 연결을 진행합니다.

## <a name="enable-the-hyper-v-feature-on-the-azure-vm"></a>Azure VM에서 Hyper-V 기능 사용
이러한 설정을 수동으로 구성하거나 PowerShell 스크립트를 제공하여 구성을 자동화할 수 있습니다.

### <a name="option-1-use-a-powershell-script-to-configure-nested-virtualization"></a>옵션 1: PowerShell 스크립트를 사용하여 중첩된 가상화 구성
Windows Server 2016 호스트에서 중첩된 가상화를 사용하도록 설정하는 PowerShell 스크립트는 [GitHub](https://github.com/charlieding/Virtualization-Documentation/tree/live/hyperv-tools/Nested)에서 사용할 수 있습니다. 이 스크립트는 사전 요구 사항을 확인한 다음 Azure VM에서 중첩된 가상화를 구성합니다. 구성을 완료하려면 Azure VM을 다시 시작해야 합니다. 이 스크립트는 다른 환경에서도 작동하지만 보장할 수는 없습니다. Azure에서 실행되는 중첩된 가상화에 대한 라이브 비디오 데모가 있는 Azure 블로그 게시물을 확인하세요. https://aka.ms/AzureNVblog.

### <a name="option-2-configure-nested-virtualization-manually"></a>옵션 2: 수동으로 중첩된 가상화 구성

1. Azure VM에서 관리자 권한으로 PowerShell을 엽니다. 

2. Hyper-V 기능 및 관리 도구를 사용하도록 설정합니다.

    ```powershell
    Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    ```

    >[!WARNING] 
    >
    >이 명령은 Azure VM을 다시 시작합니다. 다시 시작하는 동안 RDP 연결이 끊어집니다.
    
3. Azure VM이 다시 시작되면 RDP를 사용하여 VM에 다시 연결합니다.

## <a name="set-up-internet-connectivity-for-the-guest-virtual-machine"></a>게스트 가상 머신에 대한 인터넷 연결 설정
게스트 가상 컴퓨터용 새 가상 네트워크 어댑터를 만들고 인터넷 연결이 가능하도록 NAT 게이트웨이를 구성합니다.

### <a name="create-a-nat-virtual-network-switch"></a>NAT 가상 네트워크 스위치 만들기

1. Azure VM에서 관리자 권한으로 PowerShell을 엽니다.
   
2. 내부 스위치를 만듭니다.

    ```powershell
    New-VMSwitch -Name "InternalNATSwitch" -SwitchType Internal
    ```

3. 스위치의 속성을 확인하고 새 어댑터에 대한 ifIndex를 기록합니다.

    ```powershell
    Get-NetAdapter
    ```

    ![NetAdapter](./media/virtual-machines-nested-virtualization/get-netadapter.png)

    >[!NOTE] 
    >
    >방금 만든 가상 스위치의 "ifIndex"를 기록합니다.
    
4. NAT 게이트웨이에 대한 IP 주소를 만듭니다.
    
게이트웨이를 구성하려면 네트워크에 대한 일부 정보가 필요합니다.    
  * IPAddress - NAT 게이트웨이 IP는 가상 네트워크 서브넷의 기본 게이트웨이 주소로 사용할 IPv4 또는 IPv6 주소를 지정합니다. 일반적인 형식은 a.b.c.1(예: "192.168.0.1")입니다. 최종 위치가 반드시 .1일 필요는 없지만 일반적으로(접두사 길이에 따라) .1입니다. 일반적으로 RFC 1918 개인 네트워크 주소 공간을 사용해야 합니다. 
  * PrefixLength - 서브넷 접두사 길이는 로컬 서브넷 크기(서브넷 마스크)를 정의합니다. 서브넷 접두사 길이는 0에서 32 사이의 정수 값입니다. 0은 전체 인터넷, 32는 하나의 매핑된 IP만 허용합니다. 일반적인 값의 범위는 NAT에 몇 개의 IP를 연결해야 하는지에 따라 24에서 12까지입니다. 일반적인 PrefixLength는 24입니다. 즉, 255.255.255.0의 서브넷 마스크입니다.
  * InterfaceIndex - **ifIndex**는 이전 단계에서 만든 가상 스위치의 인터페이스 인덱스입니다. 

    ```powershell
    New-NetIPAddress -IPAddress 192.168.0.1 -PrefixLength 24 -InterfaceIndex 13
    ```

### <a name="create-the-nat-network"></a>NAT 네트워크 만들기

게이트웨이를 구성하려면 네트워크 및 NAT 게이트웨이에 대한 정보를 제공해야 합니다.
  * Name - NAT 네트워크의 이름입니다. 
  * InternalIPInterfaceAddressPrefix - NAT 서브넷 접두사는 위의 NAT 게이트웨이 IP 접두사와 위에서 설명한 NAT 서브넷 접두사 길이를 모두 설명합니다. 일반적인 형식은 a.b.c.0/NAT 서브넷 접두사 길이입니다. 

PowerShell에서 새 NAT 네트워크를 만듭니다.
```powershell
New-NetNat -Name "InternalNat" -InternalIPInterfaceAddressPrefix 192.168.0.0/24
```


## <a name="create-the-guest-virtual-machine"></a>게스트 가상 컴퓨터 만들기

1. Hyper-V 관리자를 열고 새 가상 머신을 만듭니다. 새로 만든 내부 네트워크를 사용하도록 가상 머신을 구성합니다.
    
    ![NetworkConfig](./media/virtual-machines-nested-virtualization/configure-networking.png)
    
2. 게스트 가상 머신에 운영 체제를 설치합니다.
    
    >[!NOTE] 
    >
    >VM에 설치할 운영 체제의 설치 미디어가 필요합니다. 이 경우 Windows 10 Enterprise를 사용합니다.

## <a name="assign-an-ip-address-to-the-guest-virtual-machine"></a>게스트 가상 머신에 IP 주소 할당

게스트 가상 시스템에서 고정 IP 주소를 수동으로 설정하거나 Azure VM에서 IP 주소를 동적으로 할당하도록 DHCP를 구성하여 게스트 가상 시스템에 IP 주소를 할당할 수 있습니다.

###  <a name="option-1-configure-dhcp-to-dynamically-assign-an-ip-address-to-the-guest-virtual-machine"></a>옵션 1: 게스트 가상 머신에 동적으로 IP 주소를 할당하도록 DHCP 구성
동적 주소 할당을 위해 호스트 가상 시스템에 DHCP를 구성하려면 아래 단계를 수행합니다.

#### <a name="install-dchp-server-on-the-azure-vm"></a>Azure VM에 DCHP 서버 설치

1. [서버 관리자]를 엽니다. 대시보드에서 **역할 및 기능 추가**를 클릭합니다. 역할 및 기능 추가 마법사가 나타납니다.
  
2. 마법사에서 서버 역할 페이지가 표시될 때까지 **다음**을 클릭합니다.
  
3. **DHCP 서버** 확인란을 선택하고 **기능 추가**를 클릭한 다음 마법사가 완료될 때까지 **다음**을 클릭합니다.
  
4. **Install**을 클릭합니다.

#### <a name="configure-a-new-dhcp-scope"></a>새 DHCP 범위 구성

1. DHCP 관리자를 엽니다.
  
2. 탐색 창에서 서버 이름을 확장하고 **IPv4**를 마우스 오른쪽 단추로 클릭한 다음 **새 범위**를 클릭합니다. 새 범위 마법사가 나타나면 **다음**을 클릭합니다.
  
3. 범위의 이름 및 설명을 입력하고 **다음**을 클릭합니다.
  
4. DCHP 서버의 IP 범위(예: 192.168.0.100~192.168.0.200)를 정의합니다.
  
5. 기본 게이트웨이 페이지가 나타날 때까지 **다음**을 클릭합니다. 이전에 만든 IP 주소(예: 192.168.0.1)를 기본 게이트웨이로 입력하고 **추가**를 클릭합니다.
  
6. 모든 기본값을 유지한 채 마법사가 완료될 때까지 **다음**을 클릭한 다음 **마침**을 클릭합니다.
    
### <a name="option-2-manually-set-a-static-ip-address-on-the-guest-virtual-machine"></a>옵션 2: 게스트 가상 머신에서 수동으로 고정 IP 주소 설정
게스트 가상 머신에 IP 주소를 동적으로 할당하도록 DHCP를 구성하지 않은 경우 다음 단계에 따라 고정 IP 주소를 설정합니다.

1. Azure VM에서 관리자 권한으로 PowerShell을 엽니다.

2. 게스트 가상 머신을 마우스 오른쪽 단추로 클릭하고 연결을 클릭합니다.

3. 게스트 가상 머신에 로그온합니다.

4. 게스트 가상 컴퓨터에서 네트워크 및 공유 센터를 엽니다.

5. 이전 섹션에서 만든 NAT 네트워크 범위 내의 주소에 대해 네트워크 어댑터를 구성합니다.

이 예제에서는 192.168.0.0/24 범위의 주소를 사용합니다.

## <a name="test-connectivity-in-guest-virtual-machine"></a>게스트 가상 머신에서 연결 테스트

게스트 가상 머신에서 브라우저를 열고 웹 페이지로 이동합니다.
    ![GuestVM](./media/virtual-machines-nested-virtualization/guest-virtual-machine.png)

## <a name="set-up-intranet-connectivity-for-the-guest-virtual-machine"></a>게스트 가상 머신에 대한 인트라넷 연결 설정

게스트 VM 및 Azure VM 간에 투명한 연결을 사용하는 방법에 대한 지침은 [이 문서](https://docs.microsoft.com/virtualization/hyper-v-on-windows/user-guide/nested-virtualization-azure-virtual-network)를 참조하세요.
