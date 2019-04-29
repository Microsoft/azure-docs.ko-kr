---
title: Azure Traffic Manager를 사용하여 서브넷 트래픽을 라우팅하는 방법 구성
description: 이 문서에서는 특정 서브넷의 트래픽을 라우팅하도록 Traffic Manager를 구성하는 방법을 설명합니다.
services: traffic-manager
documentationcenter: ''
author: KumudD
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kumud
ms.openlocfilehash: b3eb7995dac1adf3053d28b40cf322e78c69c55f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60716333"
---
# <a name="direct-traffic-to-specific-endpoints-based-on-user-subnet-using-traffic-manager"></a>Traffic Manager를 사용하여 사용자 서브넷을 기반으로 특정 엔드포인트로 트래픽 전송

이 문서에서는 서브넷 트래픽 라우팅 방법을 구성하는 방법을 설명합니다. **서브넷** 트래픽 라우팅 방법을 사용하면 일련의 IP 주소 범위를 특정 엔드포인트에 매핑할 수 있으며 Traffic Manager가 요청을 받으면 요청의 원본 IP를 검사하고 해당 요청과 연결된 엔드포인트를 반환합니다.

이 문서에서 설명되는 시나리오에서는 서브넷 라우팅을 사용하여 사용자 쿼리의 IP 주소에 따라 트래픽을 내부 웹 사이트 또는 프로덕션 웹 사이트로 라우팅합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건
실행 중인 Traffic Manager를 보려면 이 자습서에서 다음 항목을 배포해야 합니다.
- 서로 다른 Azure 지역에서 실행되는 두 개의 기본 웹 사이트 - **미국 동부**(내부 웹 사이트로 사용) 및 **유럽 서부**(프로덕션 웹 사이트로 사용).
- Traffic Manager를 테스트하기 위한 두 개의 테스트 VM - **미국 동부**에 VM 하나 및 **유럽 서부**에 두 번째 VM.

테스트 VM은 Traffic Manager가 사용자 쿼리가 발생한 서브넷을 기반으로 사용자 트래픽을 내부 웹 사이트 또는 프로덕션 웹 사이트로 라우팅하는 방법을 설명하는 데 사용됩니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

 [https://portal.azure.com](https://portal.azure.com) 에서 Azure Portal에 로그인합니다.

### <a name="create-websites"></a>웹 사이트 만들기

두 개의 Azure 영역에서 Traffic Manager 프로필에 대한 두 개의 서비스 엔드포인트를 제공하는 두 개의 웹 사이트 인스턴스를 만듭니다. 두 개의 웹 사이트를 만드는 과정에는 다음 단계가 포함됩니다.
1. 기본 웹 사이트를 실행하기 위한 두 개의 VM 만들기 - **미국 동부**에 하나와 **유럽 서부**에 다른 하나.
2. 각 VM에 IIS 서버를 설치하고 웹 사이트를 방문할 때 사용자가 연결되는 VM 이름을 설명하는 기본 웹 사이트 페이지를 업데이트합니다.

#### <a name="create-vms-for-running-websites"></a>웹 사이트 운영을 위한 VM 만들기
이 섹션에서는 **미국 동부**와 **유럽 서부** Azure 지역에 *myEndpointVMEastUS* 및 *myEndpointVMWEurope*이라는 2개의 VM을 만듭니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** > **Compute** > **Windows Server 2016 VM**를 선택합니다.
2. **기본 사항**에 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.

    |설정|값|
    |---|---|
    |이름|myIISVMEastUS|
    |사용자 이름| 선택한 사용자 이름을 입력합니다.|
    |암호| 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    |리소스 그룹| **새로 만들기**를 선택한 다음, *myResourceGroupTM1*을 입력합니다.|
    |Location| **미국 동부**를 선택합니다.|
    |||

4. **크기 선택**에서 VM 크기를 선택합니다.
5. **설정**에 대해 다음 값을 선택하고 **확인**을 선택합니다.
    
    |설정|값|
    |---|---|
    |가상 네트워크| **가상 네트워크**를 선택하고 **가상 네트워크 만들기**에서 **이름에** *myVNet1*을 입력하고 서브넷에 *mySubnet*을 입력합니다.|
    |네트워크 보안 그룹|**기본**을 선택하고 **공용 인바운드 포트 선택** 드롭다운에서 **HTTP** 및 **RDP**를 선택합니다. |
    |부트 진단|**사용 안 함**을 선택합니다.|
    |||

6. **요약**의 **만들기** 아래에서 **만들기**를 선택하여 VM 배포를 시작합니다.

7. 다음과 같은 변경 내용을 사용하여 다시 1~6단계를 완료합니다.

    |설정|값|
    |---|---|
    |리소스 그룹 | **새로 만들기**를 선택하고 *myResourceGroupTM2*를 입력합니다.|
    |Location|서유럽|
    |VM 이름 | myIISVMWEurope|
    |가상 네트워크 | **가상 네트워크**를 선택하고 **가상 네트워크 만들기**에서 **이름**에 *myVNet2*를 입력하고 서브넷에 *mySubnet*을 입력합니다.|
    |||

8. VM을 만드는 데 몇 분이 걸릴 수 있습니다. 두 VM이 모두 만들어질 때까지 나머지 단계를 수행하지 마세요.

   ![VM 만들기](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-web-page"></a>IIS를 설치하고 기본 웹 페이지를 사용자 지정

이 섹션에서는 *myIISVMEastUS*  & *myIISVMWEurope*이라는 두 개의 VM에 IIS 서버를 설치한 다음, 기본 웹 사이트 페이지를 업데이트합니다. 사용자 지정된 웹 사이트 페이지에는 웹 브라우저에서 웹 사이트를 방문할 때 연결하는 VM의 이름이 표시됩니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 *myResourceGroupTM1* 리소스 그룹에 있는 *myIISVMEastUS*를 클릭합니다.
2. **개요** 페이지에서 **연결**을 클릭한 다음, **가상 머신에 연결**에서 **RDP 파일 다운로드**를 선택합니다.
3. 다운로드한 rdp 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다. **추가 선택 사항**, **다른 계정 사용**을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.
4. **확인**을 선택합니다.
5. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속**을 선택하여 연결을 계속합니다.
6. 서버 바탕 화면에서 **Windows 관리 도구**>**서버 관리자**로 이동합니다.
7. *myIISVMEastUS*에서 Windows PowerShell을 실행하고, 다음 명령을 사용하여 IIS 서버를 설치하고 기본 htm 파일을 업데이트합니다.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my test website server - " + $env:computername)
    ```
8. *myIISVMEastUS*와의 RDP 연결을 종료합니다.
9. *myResourceGroupTM2* 리소스 그룹 내에서 *myIISVMWEurope* VM을 사용하여 RDP 연결을 만들어서 IIS를 설치하고 기본 웹 페이지를 사용자 지정하여 1-6단계를 반복합니다.
10. *myIISVMWEurope*에서 Windows PowerShell을 실행하고, 다음 명령을 사용하여 IIS 서버를 설치하고 기본 htm 파일을 업데이트합니다.
    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from my production website server - " + $env:computername)
    ```

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>IIS를 실행하는 VM의 DNS 이름 구성

Traffic Manager는 서비스 엔드포인트의 DNS 이름을 기반으로 사용자 트래픽을 라우팅합니다. 이 섹션에서는 IIS 서버에 대한 DNS 이름으로 *myIISVMEastUS* 및 *myIISVMWEurope*을 구성합니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 리소스 목록에서 *myResourceGroupTM1* 리소스 그룹에 있는 *myIISVMEastUS*를 선택합니다.
2. **개요** 페이지의 **DNS 이름**에서 **구성**을 선택합니다.
3. **구성** 페이지의 DNS 이름 레이블 아래에서 고유 이름을 추가한 다음, **저장**을 선택합니다.
4. *myResourceGroupTM1* 리소스 그룹에 있는 이름이 *myIISVMWEurope*인 VM에 대해 1~3단계를 반복합니다.

### <a name="create-test-vms"></a>테스트 VM 만들기

이 섹션에서는 각 Azure 지역(**미국 동부** 및 **유럽 서부**)에 VM(*mVMEastUS* 및 *myVMWestEurope*)을 만듭니다. 이 VM을 사용하여 사용자가 웹 사이트를 탐색할 때 Traffic Manager가 가장 가까운 IIS 서버로 트래픽을 라우팅하는 방식을 테스트합니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** > **Compute** > **Windows Server 2016 VM**를 선택합니다.
2. **기본 사항**에 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.

    |설정|값|
    |---|---|
    |이름|myVMEastUS|
    |사용자 이름| 선택한 사용자 이름을 입력합니다.|
    |암호| 선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.|
    |리소스 그룹| **기존 항목 사용**을 선택한 다음, *myResourceGroupTM1*을 선택합니다.|
    |||

4. **크기 선택**에서 VM 크기를 선택합니다.
5. **설정**에 대해 다음 값을 선택하고 **확인**을 선택합니다.

    |설정|값|
    |---|---|
    |가상 네트워크| **가상 네트워크**를 선택하고 **가상 네트워크 만들기**에서 **이름**에 *myVNet3*를 입력하고 서브넷에 *mySubnet3*을 입력합니다.|
    |네트워크 보안 그룹|**기본**을 선택하고 **공용 인바운드 포트 선택** 드롭다운에서 **HTTP** 및 **RDP**를 선택합니다. |
    |부트 진단|**사용 안 함**을 선택합니다.|
    |||

6. **요약**의 **만들기** 아래에서 **만들기**를 선택하여 VM 배포를 시작합니다.

7. 다음과 같은 변경 내용을 사용하여 1~5단계를 다시 완료합니다.

    |설정|값|
    |---|---|
    |VM 이름 | *myVMWEurope*|
    |리소스 그룹 | **기존**을 선택한 다음, *myResourceGroupTM2*을 입력합니다.|
    |가상 네트워크 | **가상 네트워크**를 선택하고 **가상 네트워크 만들기**에서 **이름**에 *myVNet4*를 입력하고 서브넷에 *mySubnet4*를 입력합니다.|
    |||

8. VM을 만드는 데 몇 분이 걸릴 수 있습니다. 두 VM이 모두 만들어질 때까지 나머지 단계를 수행하지 마세요.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기
요청의 원본 IP를 기반으로 특정 엔드포인트를 반환할 수 있는 Traffic Manager 프로파일을 만듭니다.

1. 화면 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **Traffic Manager 프로필** > **만들기**를 선택합니다.
2. **Traffic Manager 프로필 만들기**에서 다음 정보를 입력하거나 선택하고, 나머지 설정은 기본값을 그대로 적용한 다음, **만들기**를 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | 이름                   | 이 이름은 trafficmanager.net 영역 내에서 고유해야 하며 DNS 이름, trafficmanager.net 형식으로 나타나고, Traffic Manager 프로필에 액세스하는 데 사용됩니다.                                   |
    | 라우팅 방법          | **서브넷** 라우팅 방법을 선택합니다.                                       |
    | 구독            | 구독을 선택합니다.                          |
    | 리소스 그룹          | **기존**을 선택하고, *myResourceGroupTM1*을 입력합니다. |
    | |                              |
    |

    ![Traffic Manager 프로필 만들기](./media/traffic-manager-subnet-routing-method/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager 엔드포인트 추가

IIS 서버를 실행하는 두 개의 VM - *myIISVMEastUS* & *myIISVMWEurope*을 추가하여 사용자 쿼리의 서브넷을 기반으로 사용자 트래픽을 라우팅합니다.

1. 포털의 검색 창에서 이전 섹션에서 만든 Traffic Manager 프로필 이름을 검색하고, 표시되는 결과에서 해당 프로필을 선택합니다.
2. **Traffic Manager 프로필**의 **설정** 섹션에서 **엔드포인트**를 클릭한 다음, **추가**를 클릭합니다.
3. 다음 정보를 입력하거나 선택하고, 나머지 설정에 대한 기본값을 그대로 적용한 다음, **확인**을 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | Type                    | Azure 엔드포인트                                   |
    | 이름           | myTestWebSiteEndpoint                                        |
    | 대상 리소스 종류           | 공용 IP 주소                          |
    | 대상 리소스          | **공용 IP 주소를 선택**하여 동일한 구독에 속하는 공용 IP 주소가 있는 리소스 목록을 표시합니다. **리소스**에서 *myIISVMEastUS-ip*라는 이름의 공용 IP 주소를 선택합니다. 이것은 미국 동부에 있는 IIS 서버 VM의 공용 IP 주소입니다.|
    |  서브넷 라우팅 설정    |   *myVMEastUS* 테스트 VM의 IP 주소를 추가합니다. 이 VM에서 시작된 사용자 쿼리는 *myTestWebSiteEndpoint*로 전달됩니다.    |

4. 2단계와 3단계를 반복하여 *myIISVMWEurope*이라는 이름의 IIS 서버 VM과 연결된 공용 IP 주소 *myIISVMWEurope-ip*에 대해 *myProductionEndpoint*라는 이름의 또 다른 엔드포인트를 추가합니다. **서브넷 라우팅 설정**에 대해 테스트 VM - *myVMWestEurope*의 IP 주소를 추가합니다. 이 테스트 VM의 모든 사용자 쿼리는 엔드포인트 - *myProductionWebsiteEndpoint*로 라우팅됩니다.
5. 두 엔드포인트 추가가 완료되면 **온라인**인 모니터링 상태와 함께 **Traffic Manager 프로필**에 표시됩니다.

    ![Traffic Manager 엔드포인트 추가](./media/traffic-manager-subnet-routing-method/customize-endpoint-with-subnet-routing-eastus.png)

## <a name="test-traffic-manager-profile"></a>Traffic Manager 프로필 테스트
이 섹션에서는 Traffic Manager가 주어진 서브넷에서 특정 엔드포인트로 사용자 트래픽을 라우팅하는 방법을 테스트합니다. 실행 중인 Traffic Manager를 보려면 다음 단계를 완료합니다.
1. Traffic Manager 프로필의 DNS 이름을 확인합니다.
2. 실행 중인 Traffic Manager를 보는 방법은 다음과 같습니다.
    - **미국 동부** 지역에 있는 테스트 VM(*myVMEastUS*)의 웹 브라우저에서 Traffic Manager 프로필의 DNS 이름을 찾아서 이동합니다.
    - **유럽 서부** 지역에 있는 테스트 VM(*myVMEastUS*)의 웹 브라우저에서 Traffic Manager 프로필의 DNS 이름을 찾아서 이동합니다.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Traffic Manager 프로필의 DNS 이름 확인
이 자습서에서는 간단하게 Traffic Manager 프로필의 DNS 이름을 사용하여 웹 사이트를 방문합니다.

Traffic Manager 프로필의 DNS 이름은 다음과 같이 확인할 수 있습니다.

1. 포털의 검색 창에서 이전 섹션에서 만든 **Traffic Manager 프로필** 이름을 검색합니다. 표시되는 결과에서 Traffic Manager 프로필을 클릭합니다.
1. **개요**를 클릭합니다.
2. **Traffic Manager 프로필**에 새로 만든 Traffic Manager 프로필의 DNS 이름이 표시됩니다. 프로덕션 배포에서는 DNS CNAME 레코드를 사용하여 Traffic Manager 도메인 이름을 가리키도록 베니티 도메인 이름을 구성합니다.

   ![Traffic Manager DNS 이름](./media/traffic-manager-subnet-routing-method/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>실행 중인 Traffic Manager 보기
이 섹션에서는 Traffic Manager가 작업하는 것을 볼 수 있습니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 *myResourceGroupTM1* 리소스 그룹에 있는 *myVMEastUS*를 클릭합니다.
2. **개요** 페이지에서 **연결**을 클릭한 다음, **가상 머신에 연결**에서 **RDP 파일 다운로드**를 선택합니다.
3. 다운로드한 rdp 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다. **추가 선택 사항**, **다른 계정 사용**을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.
4. **확인**을 선택합니다.
5. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속**을 선택하여 연결을 계속합니다.
1. VM *myVMEastUS*의 웹 브라우저에서 Traffic Manager 프로필의 DNS 이름을 입력하여 웹 사이트를 봅니다. VM *myVMEastUS* IP 주소가 *myIISVMEastUS* 엔드포인트와 연결되어 있으므로 웹 브라우저에서 테스트 웹 사이트 서버인 *myIISVMEastUS*를 시작합니다.

   ![Traffic Manager 프로필 테스트](./media/traffic-manager-subnet-routing-method/test-traffic-manager.png)

2. 다음으로, 1~5단계를 사용하여 **유럽 서부**에 있는 VM *myVMWestEurope*에 연결하고 이 VM에서 Traffic Manager 프로필 도메인 이름을 찾아서 이동합니다. VM *myVMWestEurope* IP 주소가 *myIISVMEastUS* 엔드포인트와 연결되어 있으므로 웹 브라우저에서 테스트 웹 사이트 서버인 *myIISVMWEurope*을 시작합니다.

## <a name="delete-the-traffic-manager-profile"></a>Traffic Manager 프로필 삭제
더 이상 필요하지 않으면 리소스 그룹(**ResourceGroupTM1** 및 **ResourceGroupTM2**)을 삭제합니다. 이렇게 하려면 리소스 그룹(**ResourceGroupTM1** 또는 **ResourceGroupTM2**)을 선택한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

- [가중치 적용 트래픽 라우팅 방법](traffic-manager-configure-weighted-routing-method.md)에 대해 알아보세요.
- [우선 순위 라우팅 방법](traffic-manager-configure-priority-routing-method.md)에 대해 알아보세요.
- [지리적 라우팅 방법](traffic-manager-configure-geographic-routing-method.md)에 대해 알아보세요.
