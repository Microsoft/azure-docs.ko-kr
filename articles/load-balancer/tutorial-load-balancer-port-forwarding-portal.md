---
title: '자습서: 포트 전달 구성 - Azure Portal'
titleSuffix: Azure Load Balancer
description: 이 자습서에서는 Azure Load Balancer를 사용하여 Azure 가상 네트워크의 VM에 대한 연결을 설정하도록 포트 전달을 구성하는 방법을 보여 줍니다.
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to configure port forwarding in Azure Load Balancer to remotely connect to VMs in an Azure virtual network.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: dcb151c8be0ab3a2393d0659b75985a92ac60507
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "82207890"
---
# <a name="tutorial-configure-port-forwarding-in-azure-load-balancer-using-the-portal"></a>자습서: 포털을 사용하여 Azure Load Balancer에서 포트 전달 구성

포트 전달을 사용하면 Azure Load Balancer 공용 IP 주소 및 포트 번호를 통해 Azure 가상 네트워크의 VM(가상 머신)에 연결할 수 있습니다. 

이 자습서에서는 Azure Load Balancer에 포트 전달을 설정합니다. 다음 방법을 알아봅니다.

> [!div class="checklist"]
> * 공용 표준 부하 분산 장치를 만들어 여러 VM에 네트워크 트래픽 부하를 분산합니다. 
> * NSG(네트워크 보안 그룹) 규칙을 사용하여 가상 네트워크 및 VM을 만듭니다. 
> * 부하 분산 장치 백 엔드 주소 풀에 VM을 추가합니다.
> * 부하 분산 장치 상태 프로브 및 트래픽 규칙을 만듭니다.
> * 부하 분산 장치 인바운드 NAT 포트 전달 규칙을 만듭니다.
> * VM에 IIS를 설치하고 구성하여 부하 분산 및 포트 전달 동작을 확인합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다. 

이 자습서의 모든 단계를 위해 [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-a-standard-load-balancer"></a>표준 부하 분산 장치 만들기

먼저 여러 VM에 트래픽 부하를 분산할 수 있는 공용 표준 부하 분산 장치를 만듭니다. 표준 부하 분산 장치는 표준 공용 IP 주소만 지원합니다. 표준 부하 분산 장치를 만들 때 부하 분산 장치 프런트 엔드로 구성되고 기본적으로 **LoadBalancerFrontEnd**로 이름이 지정되는 새 표준 공개 IP 주소도 생성됩니다. 

1. 화면의 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **부하 분산 장치**를 클릭합니다.
2. **부하 분산 장치 만들기** 페이지의 **기본** 탭에서 다음 정보를 입력하거나 선택하고, 나머지 설정은 기본값을 그대로 유지한 다음, **검토 + 만들기**를 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | Subscription               | 구독을 선택합니다.    |    
    | Resource group         | **새로 만들기**를 선택하고 텍스트 상자에 *MyResourceGroupLB*를 입력합니다.|
    | 속성                   | *myLoadBalancer*                                   |
    | 지역         | **서유럽**를 선택합니다.                                        |
    | Type          | **공용**을 선택합니다.                                        |
    | SKU           | **표준**을 선택합니다.                          |
    | 공용 IP 주소 | **새로 만들기**를 선택합니다. |
    | 공용 IP 주소 이름              | 텍스트 상자에 *myPublicIP*를 입력합니다.   |
    |가용성 영역| **영역 중복**을 선택합니다.    |
     
    >[!NOTE]
     >가용성 영역을 지원하는 위치에 부하 분산 장치 및 해당 리소스를 모두 만들어야 합니다. 자세한 내용은 [가용성 영역을 지원하는 지역](../availability-zones/az-region.md)을 참조하세요. 

3. **검토 + 만들기** 탭에서 **만들기**를 클릭합니다.  
  
## <a name="create-and-configure-back-end-servers"></a>백 엔드 서버 만들기 및 구성

두 개의 가상 머신이 있는 가상 네트워크를 만들고, 해당 VM을 부하 분산 장치의 백 엔드 풀에 추가합니다. 

## <a name="virtual-network-and-parameters"></a>가상 네트워크 및 매개 변수

이 섹션에서는 단계의 다음 매개 변수를 아래 정보로 바꾸어야 합니다.

| 매개 변수                   | 값                |
|-----------------------------|----------------------|
| **\<resource-group-name>**  | myResourceGroupLB(기존 리소스 그룹 선택) |
| **\<virtual-network-name>** | myVNet          |
| **\<region-name>**          | 서유럽      |
| **\<IPv4-address-space>**   | 10.3.0.0\16          |
| **\<subnet-name>**          | myBackendSubnet        |
| **\<subnet-address-range>** | 10.3.0.0\24          |

[!INCLUDE [virtual-networks-create-new](../../includes/virtual-networks-create-new.md)]

### <a name="create-vms-and-add-them-to-the-load-balancer-back-end-pool"></a>VM을 만들고 부하 분산 장치 백 엔드 풀에 추가합니다.

1. 포털의 왼쪽 위에서 **리소스 만들기** > **컴퓨팅** > **Windows Server 2016 Datacenter**를 차례로 선택합니다. 
   
1. **가상 머신 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.
   - **구독** > **리소스 그룹**: 드롭다운하고 **MyResourceGroupLB**를 선택합니다.
   - **가상 머신 이름**: *MyVM1*을 입력합니다.
   - **지역**: **서유럽**를 선택합니다. 
   - **사용자 이름**: *azureuser*를 입력합니다.
   - **암호**: *Azure1234567*을 입력합니다. 
     **암호 확인** 필드에 암호를 다시 입력합니다.
   
1. **네트워킹** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹**을 차례로 선택합니다. 
   
   다음 항목이 선택되어 있는지 확인합니다.
   - **가상 네트워크**: **MyVNet**
   - **서브넷**: **MyBackendSubnet**
   
1. **공용 IP** 아래에서 **새로 만들기**를 선택하고 **공용 IP 주소 만들기** 페이지에서 **표준**을 선택한 다음, **확인**을 선택합니다. 
   
1. **네트워크 보안 그룹** 아래에서 **고급**을 선택하여 방화벽 유형인 NSG(네트워크 보안 그룹)를 만듭니다. 
   1. **네트워크 보안 그룹 구성** 필드에서 **새로 만들기**를 선택합니다. 
   1. *MyNetworkSecurityGroup*을 입력하고, **확인**을 선택합니다. 
   
   >[!NOTE]
   >기본적으로 NSG에는 원격 데스크톱(RDP) 포트인 포트 3389를 여는 수신 규칙이 이미 있습니다.
   
1. 사용자가 만든 부하 분산 장치 백 엔드 풀에 VM을 추가합니다.
   
   1. **부하 분산** > **기존 부하 분산 솔루션 뒤에 이 가상 머신을 배치하시겠습니까?** 아래에서 **예**를 선택합니다. 
   1. **부하 분산 옵션**에서 드롭다운하고 **Azure Load Balancer**를 선택합니다. 
   1. **부하 분산 장치 선택**에서 드롭다운하고 **MyLoadBalancer**를 선택합니다. 
   1. **백 엔드 풀 선택** 아래에서 **새로 만들기**를 선택하고 *MyBackendPool*을 입력한 다음, **만들기**를 선택합니다. 
   
   ![가상 네트워크 만들기](./media/tutorial-load-balancer-port-forwarding-portal/create-vm-networking.png)
   
1. **관리** 탭을 선택하거나 **다음** > **관리**를 선택합니다. **모니터링**에서 **부트 진단**을 **끄기**로 설정합니다.
   
1. **검토 + 만들기**를 선택합니다.
   
1. 설정을 검토하고, 유효성 검사에 성공하면 **만들기**를 선택합니다. 

1. 다음 단계에 따라 MyVM1과 동일한 다른 모든 설정을 사용하여 *MyVM2*이라는 두 번째 VM을 만듭니다. 
   
   **네트워크 보안 그룹**에서 **고급**을 선택한 후, 드롭다운하고 만들어 놓은 **MyNetworkSecurityGroup**을 선택합니다. 
   
   **백 엔드 풀 선택** 아래에서 **MyBackendPool**이 선택되었는지 확인합니다. 

### <a name="create-an-nsg-rule-for-the-vms"></a>VM에 대한 NSG 규칙 만들기

VM에 대해 인바운드 인터넷(HTTP) 연결을 허용하는 NSG(네트워크 보안 그룹) 규칙을 만듭니다.

>[!NOTE]
>기본적으로 NSG에는 원격 데스크톱(RDP) 포트인 포트 3389를 여는 규칙이 이미 있습니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 리소스 목록의 **MyResourceGroupLB** 리소스 그룹에서 **MyNetworkSecurityGroup**을 선택합니다.
   
1. **설정** 아래에서 **인바운드 보안 규칙**을 선택한 다음, **추가**를 선택합니다.
   
1. **인바운드 보안 규칙 추가** 대화 상자에서 다음을 입력하거나 선택합니다.
   
   - **원본**: **서비스 태그**를 선택합니다.  
   - **원본 서비스 태그**: **인터넷**을 선택합니다. 
   - **대상 포트 범위**: *80*을 입력합니다.
   - **프로토콜**: **TCP**를 선택합니다. 
   - **작업**: **허용**을 선택합니다.  
   - **우선 순위**: *100*을 입력합니다. 
   - **이름**: *MyHTTPRule*을 입력합니다. 
   - **설명**: *HTTP 허용*을 입력합니다. 
   
1. **추가**를 선택합니다. 
   
   ![NSG 규칙 만들기](./media/tutorial-load-balancer-port-forwarding-portal/8-load-balancer-nsg-rules.png)
   
## <a name="create-load-balancer-resources"></a>부하 분산 장치 리소스 만들기

이 섹션에서는 부하 분산 장치 백 엔드 풀을 검사하고 부하 분산 장치 상태 프로브 및 트래픽 규칙을 구성합니다.

### <a name="view-the-back-end-address-pool"></a>백 엔드 주소 풀 보기

VM으로 트래픽을 분산하기 위해 부하 분산 장치는 백 엔드 주소 풀을 사용합니다. 이 풀에는 부하 분산 장치에 연결된 가상 네트워크 인터페이스(NIC)의 IP 주소가 포함되어 있습니다. 

VM을 만들 때 부하 분산 장치 백 엔드 풀을 만들고 VM을 풀에 추가했습니다. 백 엔드 풀을 만들고 부하 분산 장치 **백 엔드 풀** 페이지에서 VM을 추가하거나 제거할 수도 있습니다. 

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **MyLoadBalancer**를 선택합니다.
   
1. **설정**에서 **백 엔드 풀**을 선택합니다.
   
1. **백 엔드 풀** 페이지에서 **MyBackendPool**을 확장하고, **VM1** 및 **VM2**가 모두 나열되는지 확인합니다.

1. **MyBackendPool**을 선택합니다. 
   
   **MyBackendPool** 페이지의 **가상 머신** 및 **IP 주소** 아래에서 사용 가능한 VM을 풀에 추가하거나 제거할 수 있습니다.

**백 엔드 풀** 페이지에서 **추가**를 선택하여 새로운 백 엔드 풀을 만들 수 있습니다.

### <a name="create-a-health-probe"></a>상태 프로브 만들기

부하 분산 장치에서 VM 상태를 모니터링할 수 있게 하려면 상태 프로브를 사용합니다. 상태 프로브는 상태 검사에 따라 부하 분산 장치 순환에서 VM을 동적으로 추가하거나 제거합니다. 

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **MyLoadBalancer**를 선택합니다.
   
1. **설정**에서 **상태 프로브**를 선택한 다음, **추가**를 선택합니다.
   
1. **상태 프로브 추가** 페이지에서 다음 값을 입력하거나 선택합니다.
   
   - **Name**: *MyHealthProbe*를 입력합니다.
   - **프로토콜**: 드롭다운하고 **HTTP**를 선택합니다. 
   - **포트**: *80*을 입력합니다. 
   - **경로**: 기본 URI에 대해 */* 를 허용합니다. 이 값은 다른 URI로 바꿀 수 있습니다. 
   - **간격**: *15*를 입력합니다. 간격은 프로브 시도 사이의 시간(초)입니다.
   - **비정상 임계값**: *2*를 입력합니다. 이 값은 VM이 비정상 상태로 간주되는 데 필요한 연속 프로브 오류 횟수입니다.
   
1. **확인**을 선택합니다.
   
   ![프로브 추가](./media/tutorial-load-balancer-port-forwarding-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>부하 분산 장치 규칙 만들기

트래픽이 VM에 분산되는 방법을 정의하는 부하 분산 장치 규칙 규칙은 들어오는 트래픽에 대한 프런트 엔드 IP 구성, 트래픽을 수신할 백 엔드 IP 풀, 필요한 원본 및 대상 포트를 정의합니다. 

**MyLoadBalancerRule**이라는 부하 분산 장치 규칙은 프런트 엔드 **LoadBalancerFrontEnd**의 포트 80에서 수신 대기합니다. 규칙은 포트 80에서 네트워크 트래픽을 백 엔드 주소 풀 **MyBackendPool**로 보냅니다. 

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **MyLoadBalancer**를 선택합니다.
   
1. **설정** 아래에서 **부하 분산 규칙**을 선택한 다음, **추가**를 선택합니다.
   
1. **부하 분산 규칙 추가** 페이지에서 다음 값을 입력하거나 선택합니다.
   
   - **Name**: *MyLoadBalancerRule*을 입력합니다.
   - **프로토콜**: **TCP**를 선택합니다.
   - **포트**: *80*을 입력합니다.
   - **백 엔드 포트**: *80*을 입력합니다.
   - **백 엔드 풀**: **MyBackendPool**을 선택합니다.
   - **상태 프로브**: **MyHealthProbe**를 선택합니다. 
   
1. **확인**을 선택합니다.
   
   ![부하 분산 장치 규칙 추가](./media/tutorial-load-balancer-port-forwarding-portal/5-load-balancing-rules.png)

## <a name="create-an-inbound-nat-port-forwarding-rule"></a>인바운드 NAT 포트 전달 규칙 만들기

프런트 엔드 IP 주소의 특정 포트에서 백 엔드 VM의 특정 포트로 트래픽을 전달하는 부하 분산 장치 인바운드 NAT(Network Address Translation) 규칙을 만듭니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **MyLoadBalancer**를 선택합니다.
   
1. **설정** 아래에서 **인바운드 NAT 규칙**을 선택한 다음, **추가**를 선택합니다. 
   
1. **인바운드 NAT 규칙 추가** 페이지에서 다음 값을 입력하거나 선택합니다.
   
   - **Name**: *MyNATRuleVM1*을 입력합니다.
   - **포트**: *4221*을 입력합니다.
   - **대상 가상 머신**: 드롭다운에서 **MyVM1**을 선택합니다.
   - **Network IP 구성**: 드롭다운에서 **ipconfig1**을 선택합니다.
   - **포트 매핑**: **사용자 지정**을 선택합니다.
   - **대상 포트**: *3389*를 입력합니다.
   
1. **확인**을 선택합니다.
   
1. 단계를 반복해서 **포트**: *4222* 및 **대상 가상 머신**: **MyVM2**.

## <a name="test-the-load-balancer"></a>부하 분산 장치 테스트

이 섹션에서는 백 엔드 서버에 IIS(인터넷 정보 서비스)를 설치하고 기본 웹 페이지를 사용자 지정하여 머신 이름을 표시합니다. 그런 다음, 부하 분산 장치의 공용 IP 주소를 사용하여 부하 분산 장치를 테스트합니다. 

부하 분산 장치가 두 VM 간에 요청을 분산하는 것을 확인할 수 있도록 각 백 엔드 VM은 기본 IIS 웹 페이지의 다른 버전을 제공합니다.

### <a name="connect-to-the-vms-with-rdp"></a>RDP를 사용하여 VM에 연결

원격 데스크톱(RDP)을 사용하여 각 VM에 연결합니다. 

1. 포털의 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 리소스 목록의 **MyResourceGroupLB** 리소스 그룹에서 각 VM을 선택합니다.
   
1. **개요** 페이지에서 **연결**을 선택한 다음, **RDP 파일 다운로드**를 선택합니다. 
   
1. 다운로드한 RDP 파일을 열고, **연결**을 선택합니다.
   
1. Windows 보안 화면에서 **기타 선택 사항**을 선택한 다음, **다른 계정 사용**을 선택합니다. 
   
   사용자 이름(*azureuser*)과 암호(*Azure1234567*)를 입력하고, **확인**을 선택합니다.
   
1. 인증서 프롬프트에서 **예**로 응답합니다. 
   
   VM 데스크톱이 새 창에서 열립니다. 

### <a name="install-iis-and-replace-the-default-iis-web-page"></a>IIS를 설치하고 기본 IIS 웹 페이지 바꾸기 

PowerShell을 사용하여 IIS를 설치하고 기본 IIS 웹 페이지를 VM의 이름이 표시되는 페이지로 바꿉니다.

1. MyVM1 및 MyVM2의 **시작** 메뉴에서 **Windows PowerShell**을 시작합니다. 

2. 다음 명령을 실행하여 IIS를 설치하고 기본 IIS 웹 페이지를 바꿉니다.
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    
   ```
   
1. **연결 끊기**를 선택하여 MyVM1 및 MyVM2와의 RDP 연결을 닫습니다. VM은 종료하지 마세요.

### <a name="test-load-balancing"></a>부하 분산 테스트

1. 포털의 **MyLoadBalancer**에 대한 **개요** 페이지에서 **공용 IP 주소** 아래의 공용 IP 주소를 복사합니다. 주소를 마우스로 가리키고 **복사** 아이콘을 선택하여 복사합니다. 이 예제에서는 **40.67.218.235**입니다. 
   
1. 인터넷 브라우저의 주소 표시줄에 부하 분산 장치의 공용 IP 주소(*40.67.218.235*)를 붙여넣거나 입력합니다. 
   
   사용자 지정된 IIS 웹 서버 기본 페이지가 브라우저에 표시됩니다. 메시지는 **MyVM1에서 Hello World** 또는 **MyVM2에서 Hello World**를 읽습니다.
   
   ![새 IIS 기본 페이지](./media/tutorial-load-balancer-port-forwarding-portal/9-load-balancer-test.png) 
   
1. VM 간에 트래픽을 분산하는 부하 분산 장치를 확인하려면 브라우저를 새로 고칩니다. 부하 분산 장치가 각 백 엔드 VM에 요청을 분산하므로 어떤 때는 **MyVM1** 페이지가 표시되고, 또 어떤 때는 **MyVM2** 페이지가 표시됩니다.
   
   >[!NOTE]
   >다시 시도하기 전에 브라우저 캐시를 지우거나 새 브라우저 창을 열어야 할 수도 있습니다.

## <a name="test-port-forwarding"></a>포트 전달 테스트

포트 전달을 사용하면 부하 분산 장치의 IP 주소 및 NAT 규칙에 정의된 프런트 엔드 포트를 통해 백 엔드 VM에 대한 원격 데스크톱을 수행할 수 있습니다. 

1. 포털의 **MyLoadBalancer**에 대한 **개요** 페이지에서 해당 공용 IP 주소를 복사합니다. 주소를 마우스로 가리키고 **복사** 아이콘을 선택하여 복사합니다. 이 예제에서는 **40.67.218.235**입니다. 
   
1. 명령 프롬프트를 열고 다음 명령을 사용하여 부하 분산 장치의 공용 IP 주소 및 VM의 NAT 규칙에 정의된 프런트 엔드 포트를 통해 MyVM2와의 원격 데스크톱 세션을 만듭니다. 
   
   ```
   mstsc /v:40.67.218.235:4222
   ```
  
1. 다운로드한 RDP 파일을 열고 **연결**을 선택합니다.
   
1. Windows 보안 화면에서 **기타 선택 사항**을 선택한 다음, **다른 계정 사용**을 선택합니다. 
   
   사용자 이름(*azureuser*)과 암호(*Azure1234567*)를 입력하고, **확인**을 선택합니다.
   
1. 인증서 프롬프트에서 **예**로 응답합니다. 
   
   MyVM2 데스크톱이 새 창에서 열립니다. 

인바운드 NAT 규칙 **MyNATRuleVM2**가 부하 분산 장치의 프런트 엔드 포트 4222에서 MyVM2의 포트 3389(RDP 포트)로 트래픽을 보내기 때문에 RDP 연결에 성공합니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 부하 분산 장치 및 모든 관련 리소스를 삭제하려면 **MyResourceGroupLB** 리소스 그룹을 열고 **리소스 그룹 삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 표준 공용 부하 분산 장치를 만들었습니다. 부하 분산 장치에서 사용할 리소스 그룹, 백 엔드 서버, 상태 프로브 및 규칙을 만들고 구성했습니다. 백 엔드 VM에 IIS를 설치하고 부하 분산 장치의 공용 IP 주소를 사용하여 부하 분산 장치를 테스트했습니다. 부하 분산 장치의 지정된 포트에서 백 엔드 VM의 포트로 포트 전달을 설정하고 테스트했습니다. 

Azure Load Balancer에 대해 자세히 알아보려면 기타 부하 분산 장치 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Load Balancer 자습서](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
