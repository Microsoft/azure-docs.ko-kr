---
title: Azure에서 RDP를 사용하여 Windows VM에 연결할 수 없음 | Microsoft Docs
description: 원격 데스크톱을 사용하여 Azure의 Windows 가상 머신에 연결할 수 없을 때의 문제 해결
keywords: 원격 데스크톱 오류,원격 데스크톱 연결 오류,VM에 연결할 수 없습니다,원격 데스크톱 문제 해결
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: 0d740f8e-98b8-4e55-bb02-520f604f5b18
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 03/23/2018
ms.author: roiyz
ms.openlocfilehash: 50adab1eaa199473a8da857d38c3a08c424c677a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62123791"
---
# <a name="troubleshoot-remote-desktop-connections-to-an-azure-virtual-machine"></a>Azure 가상 머신에 대한 원격 데스크톱 연결 문제 해결
Windows 기반 Azure VM(가상 머신)에 RDP(원격 데스크톱 프로토콜) 연결은 여러 이유로 실패하여 VM에 액세스하지 못할 수 있습니다. 이러한 문제는 VM의 원격 데스크톱 서비스, 네트워크 연결 또는 호스트 컴퓨터의 원격 데스크톱 클라이언트에서 발생할 수 있습니다. 이 문서는 RDP 연결 문제를 해결하기 위한 가장 일반적인 방법 중 일부를 안내합니다. 

이 문서의 어디에서든 도움이 필요한 경우 [MSDN Azure 및 Stack Overflow 포럼](https://azure.microsoft.com/support/forums/)에서 Azure 전문가에게 문의할 수 있습니다. 또는 Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

<a id="quickfixrdp"></a>

## <a name="quick-troubleshooting-steps"></a>빠른 문제 해결 단계
각 문제 해결 단계 후 VM에 다시 연결을 시도합니다.

1. 원격 데스크톱 구성을 다시 설정합니다.
2. 네트워크 보안 그룹 규칙/Cloud Services 엔드포인트를 확인합니다.
3. VM 콘솔 로그를 검토합니다.
4. VM에서 NIC를 다시 설정합니다.
5. VM 리소스 상태를 확인합니다.
6. VM 암호를 다시 설정합니다.
7. VM이 다시 시작됩니다.
8. VM을 다시 배포 합니다.

자세한 단계와 설명이 필요한 경우 계속 읽어보세요. [자세한 RDP 문제 해결 시나리오](detailed-troubleshoot-rdp.md)에서 설명한 대로 라우터 및 방화벽과 같은 로컬 네트워크 장비가 아웃바운드 TCP 포트 3389를 차단하지 않는지 확인합니다.

> [!TIP]
> 포털에서 VM의 **연결** 단추가 회색으로 표시되고 [Express 경로](../../expressroute/expressroute-introduction.md) 또는 [사이트 간 VPN](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) 연결을 통해 Azure에 연결되지 않는 경우 RDP를 사용하려면 먼저 공용 IP 주소를 만들고 VM에 할당해야 합니다. 자세한 내용은 [Azure의 공용 IP 주소](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)에서 확인할 수 있습니다.


## <a name="ways-to-troubleshoot-rdp-issues"></a>RDP 문제를 해결하는 방법
Resource Manager 배포 모델을 사용하여 만든 VM 문제를 다음 방법 중 하나로 해결할 수 있습니다.

* Azure Portal - RDP 구성 또는 사용자 자격 증명을 신속하게 다시 설정해야 하는데 Azure 도구가 설치돼 있지 않은 경우 매우 유용합니다.
* Azure PowerShell - PowerShell 프롬프트에 익숙한 경우 Azure PowerShell cmdlet을 사용하여 RDP 구성 또는 사용자 자격을 신속하게 다시 설정합니다.

[클래식 배포 모델](#troubleshoot-vms-created-using-the-classic-deployment-model)을 사용하여 만든 VM 문제를 해결하는 단계도 찾을 수 있습니다.

<a id="fix-common-remote-desktop-errors"></a>

## <a name="troubleshoot-using-the-azure-portal"></a>Azure Portal을 사용하여 문제 해결
각 문제 해결 단계 후 VM에 다시 연결을 시도합니다. 그래도 연결할 수 없으면 다음 단계를 시도합니다.

1. **RDP 연결 다시 설정**. 이 문제 해결 단계에서는 원격 연결을 사용할 수 없거나 Windows 방화벽 규칙이 RDP를 차단하는 경우에 RDP 구성을 다시 설정합니다.
   
    Azure Portal에서 VM을 선택합니다. 목록 맨 아래 근처에 있는 **지원 + 문제 해결** 섹션이 나올 때까지 설정 창을 아래로 스크롤합니다. **암호 다시 설정** 단추를 클릭합니다. **모드**를 **구성만 재설정**으로 설정한 다음 **업데이트** 단추를 클릭합니다.
   
    ![Azure Portal에서 RDP 구성 다시 설정](./media/troubleshoot-rdp-connection/reset-rdp.png)
2. **네트워크 보안 그룹 규칙 확인**. [IP 흐름 확인](../../network-watcher/network-watcher-check-ip-flow-verify-portal.md)을 사용하여 네트워크 보안 그룹의 규칙이 가상 머신 간에 트래픽을 차단하는지를 확인합니다. 효과적인 보안 그룹 규칙을 검토하여 인바운드 "허용" NSG 규칙이 있는지와 해당 규칙이 RDP 포트(기본값: 3389)에 우선적으로 사용되도록 설정되어 있는지 확인합니다. 자세한 내용은 [효과적인 보안 규칙을 사용하여 VM 트래픽 흐름 문제 해결](../../virtual-network/diagnose-network-traffic-filter-problem.md)을 참조하세요.

3. **VM 부트 진단 검토**. 이 문제 해결 단계에서는 VM 콘솔 로그를 검토하여 VM이 문제를 보고하는지 확인합니다. 모든 VM에서 부팅 진단이 지원되는 것은 아니므로 이 문제 해결 단계는 선택 사항입니다.
   
    구체적인 문제 해결 단계는 이 문서의 범위를 벗어나지만, RDP 연결에 영향을 주는 더 넓은 문제를 나타낼 수 있습니다. 콘솔 로그 및 VM 스크린샷 검토에 대한 자세한 내용은 [VM 부팅 진단](boot-diagnostics.md)을 참조하세요.

4. **VM에서 NIC를 다시 설정합니다**. 자세한 내용은 [Azure Windows VM에서 NIC를 다시 설정하는 방법](../windows/reset-network-interface.md)을 참조하세요.
5. **VM 리소스 상태 확인**. 이 문제 해결 단계에서는 Azure 플랫폼에 VM 연결에 영향을 줄 수 있는 알려진 문제가 없는지 확인합니다.
   
    Azure Portal에서 VM을 선택합니다. 목록 맨 아래 근처에 있는 **지원 + 문제 해결** 섹션이 나올 때까지 설정 창을 아래로 스크롤합니다. **리소스 상태** 단추를 클릭합니다. 정상 VM은 **사용 가능**으로 보고합니다.
   
    ![Azure Portal에서 VM 리소스 상태 확인](./media/troubleshoot-rdp-connection/check-resource-health.png)
6. **사용자 자격 증명 다시 설정**. 이 문제 해결 단계에서는 자격 증명이 확실하지 않거나 잊어버린 경우 로컬 관리자 계정에서 암호를 다시 설정합니다.  VM에 로그인하면 해당 사용자의 암호를 다시 설정해야 합니다.
   
    Azure Portal에서 VM을 선택합니다. 목록 맨 아래 근처에 있는 **지원 + 문제 해결** 섹션이 나올 때까지 설정 창을 아래로 스크롤합니다. **암호 다시 설정** 단추를 클릭합니다. **모드**를 **암호 다시 설정**으로 지정한 다음 사용자 이름 및 새 암호를 입력합니다. 마지막으로 **업데이트** 단추를 클릭합니다.
   
    ![Azure Portal에서 사용자 자격 증명 다시 설정](./media/troubleshoot-rdp-connection/reset-password.png)
7. **VM 다시 시작**. 이 문제 해결 단계에서는 VM 자체의 기본 문제를 해결할 수 있습니다.
   
    Azure Portal에서 VM을 선택하고 **개요** 탭을 클릭합니다. **다시 시작** 단추를 클릭합니다.
   
    ![Azure Portal에서 VM을 다시 시작합니다.](./media/troubleshoot-rdp-connection/restart-vm.png)
8. **VM 다시 배포**. 이 문제 해결 단계에서는 Azure 내의 다른 호스트에 VM을 다시 배포하여 기본 플랫폼 또는 네트워킹 문제를 해결합니다.
   
    Azure Portal에서 VM을 선택합니다. 목록 맨 아래 근처에 있는 **지원 + 문제 해결** 섹션이 나올 때까지 설정 창을 아래로 스크롤합니다. **다시 배포** 단추를 클릭한 다음 **다시 배포**를 클릭합니다.
   
    ![Azure Portal에서 VM 다시 배포](./media/troubleshoot-rdp-connection/redeploy-vm.png)
   
    이 작업이 완료되면 임시 디스크 데이터가 손실되고 VM과 연결된 동적 IP 주소가 업데이트됩니다.

9. **라우팅을 확인**합니다. Network Watcher의 [다음 홉](../../network-watcher/network-watcher-check-next-hop-portal.md) 기능을 사용하여 트래픽이 가상 머신으로 들어가거나 나가도록 라우팅하는 데 경로가 방해가 되지 않는지 확인합니다. 네트워크 인터페이스의 유효 경로를 모두 볼 수 있도록 유효 경로를 검토할 수도 있습니다. 자세한 내용은 [유효 경로를 사용하여 VM 트래픽 흐름 문제 해결](../../virtual-network/diagnose-network-routing-problem.md)을 참조하세요.

10. 온-프레미스 방화벽 또는 컴퓨터의 방화벽이 Azure로 아웃바운드 TCP 3389 트래픽을 허용하는지 확인합니다.

RDP 문제가 계속 발생하는 경우 [지원 요청을 열거나](https://azure.microsoft.com/support/options/) [좀 더 자세한 RDP 문제 해결 개념 및 단계](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 읽어볼 수 있습니다.

## <a name="troubleshoot-using-azure-powershell"></a>Azure PowerShell을 사용하여 문제 해결
아직 작업 전이면 [최신 Azure PowerShell을 설치하고 구성](/powershell/azure/overview)합니다.

다음 예제에서는 `myResourceGroup`, `myVM`, `myVMAccessExtension` 등의 변수를 사용합니다. 이러한 변수 이름 및 위치를 사용자 고유의 값으로 바꿉니다.

> [!NOTE]
> [Set-AzVMAccessExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmaccessextension) PowerShell cmdlet을 사용하여 사용자 자격 증명 및 RDP 구성을 다시 설정합니다. 다음 예제에서 `myVMAccessExtension`은 프로세스의 일부로 지정하는 이름입니다. VMAccessAgent로 이전에 작업한 경우 `Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM"`을 사용하여 기존 확장의 이름을 가져와서 VM의 속성을 확인할 수 있습니다. 이름을 보려면 출력의 'Extensions' 섹션에서 이름을 확인합니다.

각 문제 해결 단계 후 VM에 다시 연결을 시도합니다. 그래도 연결할 수 없으면 다음 단계를 시도합니다.

1. **RDP 연결 다시 설정**. 이 문제 해결 단계에서는 원격 연결을 사용할 수 없거나 Windows 방화벽 규칙이 RDP를 차단하는 경우에 RDP 구성을 다시 설정합니다.
   
    다음 예제에서는 `WestUS` 위치에 있는 `myVM`이라는 VM과 `myResourceGroup`이라는 리소스 그룹에서 RDP 연결을 다시 설정합니다.
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location Westus -Name "myVMAccessExtension"
    ```
2. **네트워크 보안 그룹 규칙 확인**. 이 문제 해결 단계에서는 네트워크 보안 그룹에 RDP 트래픽을 허용하는 규칙이 있는지 확인합니다. RDP의 기본 포트는 TCP 포트 3389입니다. VM을 만들 때 RDP 트래픽을 허용하는 규칙이 자동으로 생성되지 않을 수도 있습니다.
   
    첫째, 네트워크 보안 그룹의 모든 구성 데이터를 `$rules` 변수에 할당합니다. 다음 예제에서는 리소스 그룹 `myResourceGroup`의 네트워크 보안 그룹 `myNetworkSecurityGroup`에 대한 정보를 가져옵니다.
   
    ```powershell
    $rules = Get-AzNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
        -Name "myNetworkSecurityGroup"
    ```
   
    이제 이 네트워크 보안 그룹에 대해 구성된 규칙을 봅니다. 다음과 같이 인바운드 연결에 TCP 포트 3389를 허용하는 규칙이 있는지 확인합니다.
   
    ```powershell
    $rules.SecurityRules
    ```
   
    다음 예제에서는 RDP 트래픽을 허용하는 유효한 보안 규칙을 보여 줍니다. `Protocol`, `DestinationPortRange`, `Access` 및 `Direction`이 올바르게 구성된 것을 볼 수 있습니다.
   
    ```powershell
    Name                     : default-allow-rdp
    Id                       : /subscriptions/guid/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myNetworkSecurityGroup/securityRules/default-allow-rdp
    Etag                     : 
    ProvisioningState        : Succeeded
    Description              : 
    Protocol                 : TCP
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : *
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 1000
    Direction                : Inbound
    ```
   
    RDP 트래픽을 허용하는 규칙이 없는 경우 [네트워크 보안 그룹 규칙을 만듭니다](../windows/nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). TCP 포트 3389를 허용합니다.
3. **사용자 자격 증명 다시 설정**. 이 문제 해결 단계에서는 자격 증명이 확실하지 않거나 잊어버린 경우 사용자가 지정하는 로컬 관리자 계정에서 암호를 다시 설정합니다.
   
    먼저 다음과 같이 `$cred` 변수에 자격 증명을 할당하여 사용자 이름과 새 암호를 지정합니다.
   
    ```powershell
    $cred=Get-Credential
    ```
   
    이제 VM의 자격 증명을 업데이트합니다. 다음 예제에서는 `WestUS` 위치에 있는 `myVM`이라는 VM과 `myResourceGroup`이라는 리소스 그룹에서 자격 증명을 업데이트합니다.
   
    ```powershell
    Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" `
        -VMName "myVM" -Location WestUS -Name "myVMAccessExtension" `
        -UserName $cred.GetNetworkCredential().Username `
        -Password $cred.GetNetworkCredential().Password
    ```
4. **VM 다시 시작**. 이 문제 해결 단계에서는 VM 자체의 기본 문제를 해결할 수 있습니다.
   
    다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 다시 시작합니다.
   
    ```powershell
    Restart-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
    ```
5. **VM 다시 배포**. 이 문제 해결 단계에서는 Azure 내의 다른 호스트에 VM을 다시 배포하여 기본 플랫폼 또는 네트워킹 문제를 해결합니다.
   
    다음 예제에서는 `WestUS` 위치에 있는 `myVM`이라는 VM과 `myResourceGroup`이라는 리소스 그룹을 다시 배포합니다.
   
    ```powershell
    Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
    ```

6. **라우팅을 확인**합니다. Network Watcher의 [다음 홉](../../network-watcher/network-watcher-check-next-hop-portal.md) 기능을 사용하여 트래픽이 가상 머신으로 들어가거나 나가도록 라우팅하는 데 경로가 방해가 되지 않는지 확인합니다. 네트워크 인터페이스의 유효 경로를 모두 볼 수 있도록 유효 경로를 검토할 수도 있습니다. 자세한 내용은 [유효 경로를 사용하여 VM 트래픽 흐름 문제 해결](../../virtual-network/diagnose-network-routing-problem.md)을 참조하세요.

7. 온-프레미스 방화벽 또는 컴퓨터의 방화벽이 Azure로 아웃바운드 TCP 3389 트래픽을 허용하는지 확인합니다.

RDP 문제가 계속 발생하는 경우 [지원 요청을 열거나](https://azure.microsoft.com/support/options/) [좀 더 자세한 RDP 문제 해결 개념 및 단계](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 읽어볼 수 있습니다.

## <a name="troubleshoot-vms-created-using-the-classic-deployment-model"></a>클래식 배포 모델을 사용하여 만든 VM 문제 해결
각 문제 해결 단계 후 VM에 다시 연결을 시도합니다.

1. **RDP 연결 다시 설정**. 이 문제 해결 단계에서는 원격 연결을 사용할 수 없거나 Windows 방화벽 규칙이 RDP를 차단하는 경우에 RDP 구성을 다시 설정합니다.
   
    Azure Portal에서 VM을 선택합니다. **...더 보기** 단추를 클릭한 다음 **원격 액세스 다시 설정**을 클릭합니다.
   
    ![Azure Portal에서 RDP 구성 다시 설정](./media/troubleshoot-rdp-connection/classic-reset-rdp.png)
2. **Cloud Services 엔드포인트 확인**. 이 문제 해결 단계에서는 Cloud Services에 RDP 트래픽을 허용하는 규칙이 있는지 확인합니다. RDP의 기본 포트는 TCP 포트 3389입니다. VM을 만들 때 RDP 트래픽을 허용하는 규칙이 자동으로 생성되지 않을 수도 있습니다.
   
   Azure Portal에서 VM을 선택합니다. **엔드포인트** 단추를 클릭하여 현재 VM에 대해 구성된 엔드포인트를 표시합니다. TCP 포트 3389에서 RDP 트래픽을 허용하는 엔드포인트가 있는지 확인합니다.
   
   다음 예제에서는 RDP 트래픽을 허용하는 유효한 엔드포인트를 보여줍니다.
   
   ![Azure Portal에서 Cloud Services 엔드포인트 확인](./media/troubleshoot-rdp-connection/classic-verify-cloud-services-endpoints.png)
   
   RDP 트래픽을 허용하는 엔드포인트가 없는 경우 [Cloud Services 엔드포인트를 만듭니다](../windows/classic/setup-endpoints.md). 개인 포트 3389에 TCP를 허용합니다.
3. **VM 부트 진단 검토**. 이 문제 해결 단계에서는 VM 콘솔 로그를 검토하여 VM이 문제를 보고하는지 확인합니다. 모든 VM에서 부팅 진단이 지원되는 것은 아니므로 이 문제 해결 단계는 선택 사항입니다.
   
    구체적인 문제 해결 단계는 이 문서의 범위를 벗어나지만, RDP 연결에 영향을 주는 더 넓은 문제를 나타낼 수 있습니다. 콘솔 로그 및 VM 스크린샷 검토에 대한 자세한 내용은 [VM 부팅 진단](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/)을 참조하세요.
4. **VM 리소스 상태 확인**. 이 문제 해결 단계에서는 Azure 플랫폼에 VM 연결에 영향을 줄 수 있는 알려진 문제가 없는지 확인합니다.
   
    Azure Portal에서 VM을 선택합니다. 목록 맨 아래 근처에 있는 **지원 + 문제 해결** 섹션이 나올 때까지 설정 창을 아래로 스크롤합니다. **리소스 상태** 단추를 클릭합니다. 정상 VM은 **사용 가능**으로 보고합니다.
   
    ![Azure Portal에서 VM 리소스 상태 확인](./media/troubleshoot-rdp-connection/classic-check-resource-health.png)
5. **사용자 자격 증명 다시 설정**. 이 문제 해결 단계에서는 자격 증명이 확실하지 않거나 잊어버린 경우 사용자가 지정하는 로컬 관리자 계정에서 암호를 다시 설정합니다.  VM에 로그인하면 해당 사용자의 암호를 다시 설정해야 합니다.
   
    Azure Portal에서 VM을 선택합니다. 목록 맨 아래 근처에 있는 **지원 + 문제 해결** 섹션이 나올 때까지 설정 창을 아래로 스크롤합니다. **암호 다시 설정** 단추를 클릭합니다. 사용자 이름 및 새 암호를 입력합니다. 마지막으로 **저장** 단추를 클릭합니다.
   
    ![Azure Portal에서 사용자 자격 증명 다시 설정](./media/troubleshoot-rdp-connection/classic-reset-password.png)
6. **VM 다시 시작**. 이 문제 해결 단계에서는 VM 자체의 기본 문제를 해결할 수 있습니다.
   
    Azure Portal에서 VM을 선택하고 **개요** 탭을 클릭합니다. **다시 시작** 단추를 클릭합니다.
   
    ![Azure Portal에서 VM을 다시 시작합니다.](./media/troubleshoot-rdp-connection/classic-restart-vm.png)

7. 온-프레미스 방화벽 또는 컴퓨터의 방화벽이 Azure로 아웃바운드 TCP 3389 트래픽을 허용하는지 확인합니다.

RDP 문제가 계속 발생하는 경우 [지원 요청을 열거나](https://azure.microsoft.com/support/options/) [좀 더 자세한 RDP 문제 해결 개념 및 단계](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 읽어볼 수 있습니다.

## <a name="troubleshoot-specific-rdp-errors"></a>특정 RDP 오류 해결
RDP를 통해 VM에 연결하려고 할 때 특정 오류 메시지가 나타날 수 있습니다. 다음은 가장 일반적인 오류 메시지입니다.

* [라이선스를 제공할 수 있는 원격 데스크톱 라이선스 서버가 없으므로 원격 세션이 끊겼습니다](troubleshoot-specific-rdp-errors.md#rdplicense).
* [원격 데스크톱에서 컴퓨터 "이름"을 찾을 수 없습니다](troubleshoot-specific-rdp-errors.md#rdpname).
* [인증 오류가 발생했습니다. 로컬 보안 기관에 연결할 수 없습니다.](troubleshoot-specific-rdp-errors.md#rdpauth)
* [Windows 보안 오류: 자격 증명이 작동하지 않았습니다](troubleshoot-specific-rdp-errors.md#wincred).
* [이 컴퓨터에서 원격 컴퓨터에 연결할 수 없습니다](troubleshoot-specific-rdp-errors.md#rdpconnect).

## <a name="additional-resources"></a>추가 리소스
이러한 오류가 발생하지 않았는데도 여전히 원격 데스크톱을 통해 VM에 연결할 수 없는 경우 [원격 데스크톱에 대한 자세한 문제 해결 가이드](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 읽어보세요.
* VM에서 실행 중인 애플리케이션에 액세스하는 문제 해결 단계는 [Azure VM에서 실행 중인 애플리케이션에 대한 액세스 문제 해결](../linux/troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.
* SSH(Secure Shell)를 사용하여 Azur에서 Linux VM에 연결하는 데 문제가 있는 경우 [Azure에서 Linux VM에 SSH 연결 문제 해결](../linux/troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)을 참조하세요.


