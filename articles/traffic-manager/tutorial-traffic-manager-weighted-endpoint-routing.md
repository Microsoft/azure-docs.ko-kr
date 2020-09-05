---
title: '자습서: 가중 엔드포인트로 트래픽 라우팅 - Azure Traffic Manager'
description: 이 자습서 문서에서는 Traffic Manager를 사용하여 가중 엔드포인트로 트래픽을 라우팅하는 방법에 대해 설명합니다.
services: traffic-manager
author: duongau
Customer intent: As an IT Admin, I want to distribute traffic based on the weight assigned to a website endpoint so that I can control the user traffic to a given website.
ms.service: traffic-manager
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: duau
ms.openlocfilehash: 90ed68e36b47d46c47e78407fac3b5fd74924b57
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89397086"
---
# <a name="tutorial-control-traffic-routing-with-weighted-endpoints-by-using-traffic-manager"></a>자습서: Traffic Manager를 사용하여 가중 엔드포인트에서 트래픽 라우팅 제어

이 자습서는 Azure Traffic Manager를 사용하여 가중 라우팅 방법으로 엔드포인트 간에 사용자 트래픽 라우팅을 제어하는 방법을 설명합니다. 이 라우팅 메서드에서는 Traffic Manager 프로필 구성에서 각 엔드포인트에 가중치를 할당합니다. 그런 다음, 각 엔드포인트에 할당된 가중치에 따라 사용자 트래픽이 라우팅됩니다. 가중치는 1에서 1000 사이의 정수입니다. 엔드포인트에 할당된 가중치 값이 높을수록 우선 순위가 높습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * IIS에서 기본 웹 사이트를 실행하는 두 개의 VM 만들기.
> * 실행 중인 Traffic Manager를 보기 위해 두 개의 테스트 VM 만들기.
> * IIS를 실행하는 VM의 DNS 이름 구성.
> * Traffic Manager 프로필 만들기.
> * Traffic Manager 프로필에 VM 엔드포인트 추가.
> * 실행 중인 Traffic Manager를 봅니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

실행 중인 Traffic Manager를 보려면 이 자습서에서 다음을 배포하세요.

- 다른 Azure 지역, 즉 미국 동부 및 서유럽에서 실행되는 기본 웹 사이트의 두 인스턴스
- Traffic Manager를 테스트하기 위한 두 개의 테스트 VM: 미국 동부에 VM 하나 및 서유럽에 두 번째 VM. 테스트 VM을 사용하여 Traffic Manager가 엔드포인트에 더 높은 가중치가 할당된 웹 사이트로 사용자 트래픽을 라우팅하는 방법을 자세히 보여줍니다.

### <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

### <a name="create-websites"></a>웹 사이트 만들기

두 개의 Azure 영역에서 Traffic Manager 프로필에 대한 두 개의 서비스 엔드포인트를 제공하는 두 개의 웹 사이트 인스턴스를 만듭니다. 두 웹 사이트를 만들려면 다음 단계를 완료합니다.

1. 기본 웹 사이트를 실행하기 위한 두 개의 VM 만들기: 미국 동부에 하나와 서유럽에 다른 하나.
2. 각 VM에 IIS 서버를 설치합니다. 웹 사이트를 방문할 때 사용자가 연결되는 VM 이름을 설명하는 기본 웹 사이트 페이지를 업데이트합니다.

#### <a name="create-vms-for-running-websites"></a>웹 사이트 운영을 위한 VM 만들기

이 섹션에서는 미국 동부와 서유럽 Azure 지역에 *myIISVMEastUS* 및 *myIISVMWestEurope*이라는 두 개의 VM을 만듭니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** > **컴퓨팅** > **Windows Server 2019 Datacenter**를 선택합니다.
2. **가상 머신 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.

   - **구독** > **리소스 그룹**: **새로 만들기**를 선택한 다음, **myResourceGroupTM1**을 입력합니다.
   - **인스턴스 정보** > **가상 머신 이름**: *myIISVMEastUS*를 입력합니다.
   - **인스턴스 세부 정보** > **Azure 지역**:  **미국 동부**를 선택합니다.
   - **관리자 계정** > **사용자 이름**:  선택한 사용자 이름을 입력합니다.
   - **관리자 계정** > **암호**:  선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.
   - **인바운드 포트 규칙** > **공용 인바운드 포트**: **선택한 포트 허용**을 선택합니다.
   - **인바운드 포트 규칙** > **인바운드 포트 선택**: 풀다운 상자에서 **RDP** 및 **HTTP**를 선택합니다.

3. **관리** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹**, **다음: 관리**를 선택합니다. **모니터링**에서 **부트 진단**을 **끄기**로 설정합니다.
4. **검토 + 만들기**를 선택합니다.
5. 설정을 검토한 다음, **만들기**를 클릭합니다.  
6. 단계에 따라 **리소스 그룹** 이름은 *myResourceGroupTM2*이고, **위치**는 *서유럽*이고, 나머지 설정은 *myIISVMEastUS*와 동일한 *myIISVMWestEurope*이라는 두 번째 VM을 만듭니다.
7. VM을 만드는 데 몇 분이 걸릴 수 있습니다. 두 VM이 모두 만들어질 때까지 나머지 단계를 수행하지 마세요.

![VM 만들기](./media/tutorial-traffic-manager-improve-website-response/createVM.png)

#### <a name="install-iis-and-customize-the-default-webpage"></a>IIS를 설치하고 기본 웹 페이지를 사용자 지정

이 섹션에서는 myIISVMEastUS 및 myIISVMWestEurope이라는 두 VM에 IIS 서버를 설치한 다음, 기본 웹 페이지를 업데이트합니다. 사용자 지정된 웹 페이지에는 웹 브라우저에서 웹 사이트를 방문할 때 연결하는 VM의 이름이 표시됩니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 리소스 목록의 **myResourceGroupTM1** 리소스 그룹에서 **myIISVMEastUS**를 선택합니다.
2. **개요** 페이지에서 **연결**을 선택합니다. **가상 머신에 연결**에서 **RDP 파일 다운로드**를 선택합니다.
3. 다운로드한 rdp 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다. VM을 만들 때 지정한 사용자 이름 및 암호를 입력합니다. **추가 선택 사항**,  > **다른 계정 사용**을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.
4. **확인**을 선택합니다.
5. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속**을 선택하여 연결을 계속합니다.
6. 서버 바탕 화면에서 **Windows 관리 도구** > **서버 관리자**로 이동합니다.
7. VM1에서 Windows PowerShell을 엽니다. 다음 명령을 사용하여 IIS 서버를 설치하고 기본 .htm 파일을 업데이트합니다.

    ```powershell-interactive
    # Install IIS
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default .htm file
    remove-item C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom .htm file
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```

    ![IIS를 설치하고 웹 페이지를 사용자 지정](./media/tutorial-traffic-manager-improve-website-response/deployiis.png)

8. **myIISVMEastUS**와의 RDP 연결을 종료합니다.
9. 1-8단계를 반복합니다. **myResourceGroupTM2** 리소스 그룹의 **myIISVMWestEurope** VM에 RDP로 연결하여 IIS를 설치하고 기본 웹 페이지를 사용자 지정합니다.

#### <a name="configure-dns-names-for-the-vms-running-iis"></a>IIS를 실행하는 VM의 DNS 이름 구성

Traffic Manager는 서비스 엔드포인트의 DNS 이름을 기반으로 사용자 트래픽을 라우팅합니다. 이 섹션에서는 IIS 서버 myIISVMEastUS 및 myIISVMWestEurope의 DNS 이름을 구성합니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 리소스 목록의 **myResourceGroupTM1** 리소스 그룹에서 **myIISVMEastUS**를 선택합니다.
2. **개요** 페이지의 **DNS 이름**에서 **구성**을 선택합니다.
3. **구성** 페이지의 DNS 이름 레이블 아래에서 고유 이름을 추가합니다. 그런 다음 **저장**을 선택합니다.
4. **myResourceGroupTM2** 리소스 그룹의 **myIISVMWestEurope** VM에 대해 1-3단계를 반복합니다.

### <a name="create-a-test-vm"></a>테스트 VM 만들기

이 섹션에서는 각 Azure 지역(**미국 동부** 및 **서유럽**)에 VM(*myVMEastUS* 및 *myVMWestEurope*)을 만듭니다. 이 VM을 사용하여 Traffic Manager가 어떻게 트래픽을 가중치 값이 더 높은 웹 사이트 엔드포인트로 라우팅하는지 테스트할 것입니다.

1. Azure Portal의 왼쪽 위 모서리에서 **리소스 만들기** > **컴퓨팅** > **Windows Server 2019 Datacenter**를 선택합니다.
2. **가상 머신 만들기**의 **기본** 탭에서 다음 값을 입력하거나 선택합니다.

   - **구독** > **리소스 그룹**: **myResourceGroupTM1**을 선택합니다.
   - **인스턴스 정보** > **가상 머신 이름**: *myVMEastUS*를 입력합니다.
   - **인스턴스 세부 정보** > **Azure 지역**:  **미국 동부**를 선택합니다.
   - **관리자 계정** > **사용자 이름**:  선택한 사용자 이름을 입력합니다.
   - **관리자 계정** > **암호**:  선택한 암호를 입력합니다. 암호는 12자 이상이어야 하며 [정의된 복잡성 요구 사항](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)을 충족해야 합니다.
   - **인바운드 포트 규칙** > **공용 인바운드 포트**: **선택한 포트 허용**을 선택합니다.
   - **인바운드 포트 규칙** > **인바운드 포트 선택**: 풀다운 상자에서 **RDP**를 선택합니다.

3. **관리** 탭을 선택하거나 **다음: 디스크**, **다음: 네트워킹**, **다음: 관리**를 선택합니다. **모니터링**에서 **부트 진단**을 **끄기**로 설정합니다.
4. **검토 + 만들기**를 선택합니다.
5. 설정을 검토한 다음, **만들기**를 클릭합니다.  
6. 단계에 따라 **리소스 그룹** 이름은 *myResourceGroupTM2*이고, **위치**는 *서유럽*이고, 나머지 설정은 *myVMEastUS*와 동일한 *myVMWestEurope*이라는 두 번째 VM을 만듭니다.
7. VM을 만드는 데 몇 분이 걸릴 수 있습니다. 두 VM이 모두 만들어질 때까지 나머지 단계를 수행하지 마세요.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager 프로필 만들기

**가중** 라우팅 방법에 따라 Traffic Manager프로필을 만듭니다.

1. 화면 왼쪽 상단에서 **리소스 만들기** > **네트워킹** > **Traffic Manager 프로필** > **만들기**를 선택합니다.
2. **Traffic Manager 프로필 만들기**에서 다음 정보를 입력하거나 선택합니다. 기타 설정은 기본값으로 유지한 다음, **만들기**를 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | 속성                   | trafficmanager.net 영역 내에서 고유한 이름을 입력합니다. 이는 Traffic Manager 프로필에 액세스하는 데 사용되는 DNS 이름 trafficmanager.net이 됩니다.                                   |
    | 라우팅 방법          | **가중** 라우팅 방법을 선택합니다.                                       |
    | Subscription            | 구독을 선택합니다.                          |
    | Resource group          | **기존 항목 사용**을 선택한 다음, **myResourceGroupTM1**을 선택합니다. |
    |        |   |

    ![Traffic Manager 프로필 만들기](./media/tutorial-traffic-manager-weighted-endpoint-routing/create-traffic-manager-profile.png)

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager 엔드포인트 추가

IIS 서버를 실행하는 myIISVMEastUS 및 myIISVMWestEurope이라는 두 개의 VM을 추가하여 사용자 트래픽을 두 VM에 라우팅합니다.

1. 포털의 검색 창에서 이전 섹션에서 만든 Traffic Manager 프로필 이름을 검색합니다. 표시되는 결과에서 프로필을 선택합니다.
2. **Traffic Manager 프로필**의 **설정** 섹션에서 **엔드포인트** > **추가**를 클릭합니다.
3. 다음 정보를 입력하거나 선택합니다. 기타 설정은 기본값으로 유지한 다음, **확인**을 선택합니다.

    | 설정                 | 값                                              |
    | ---                     | ---                                                |
    | Type                    | Azure 엔드포인트를 입력합니다.                                   |
    | 속성           | **myEastUSEndpoint**를 입력합니다.                                        |
    | 대상 리소스 종류           | **공용 IP 주소**를 선택합니다.                          |
    | 대상 리소스          | 공용 IP 주소를 선택하여 동일한 구독에 속하는 공용 IP 주소가 있는 리소스 목록을 표시합니다. **리소스**에서 **myIISVMEastUS-ip**라는 이름의 공용 IP 주소를 선택합니다. 이것은 미국 동부에 있는 IIS 서버 VM의 공용 IP 주소입니다.|
    |  무게      | **100**을 입력합니다.        |
    |        |           |

4. 2단계와 3단계를 반복하여 공용 IP 주소 **myIISVMWestEurope-ip**에 **myWestEuropeEndpoint**라는 이름의 또 다른 엔드포인트를 추가합니다. 이 주소는 myIISVMWestEurope이라는 IIS 서버 VM과 연결됩니다. **가중치**에 **25**를 입력합니다.
5. 두 엔드포인트 추가가 완료되면 **온라인**인 모니터링 상태와 함께 Traffic Manager 프로필에 표시됩니다.

## <a name="test-the-traffic-manager-profile"></a>Traffic Manager 프로필 테스트

실행 중인 Traffic Manager를 보려면 다음 단계를 완료합니다.

1. Traffic Manager 프로필의 DNS 이름을 확인합니다.
2. 실행 중인 Traffic Manager를 봅니다.

### <a name="determine-dns-name-of-traffic-manager-profile"></a>Traffic Manager 프로필의 DNS 이름 확인

이 자습서에서는 간단하게 Traffic Manager 프로필의 DNS 이름을 사용하여 웹 사이트를 방문합니다.

Traffic Manager 프로필의 DNS 이름은 다음과 같이 확인할 수 있습니다.

1. 포털의 검색 창에서 이전 섹션에서 만든 Traffic Manager 프로필 이름을 검색합니다. 표시되는 결과에서 Traffic Manager 프로필을 선택합니다.
2. **개요**를 선택합니다.
3. Traffic Manager 프로필에는 해당 DNS 이름이 표시됩니다. 프로덕션 배포에서는 DNS CNAME 레코드를 사용하여 Traffic Manager 도메인 이름을 가리키도록 베니티 도메인 이름을 구성합니다.

   ![Traffic Manager DNS 이름](./media/tutorial-traffic-manager-improve-website-response/traffic-manager-dns-name.png)

### <a name="view-traffic-manager-in-action"></a>실행 중인 Traffic Manager 보기

이 섹션에서는 실행 중인 Traffic Manager를 볼 수 있습니다.

1. 왼쪽 메뉴에서 **모든 리소스**를 선택합니다. 리소스 목록의 **myResourceGroupTM1** 리소스 그룹에서 **myVMEastUS**를 선택합니다.
2. **개요** 페이지에서 **연결**을 선택합니다. **가상 머신에 연결**에서 **RDP 파일 다운로드**를 선택합니다.
3. 다운로드한 rdp 파일을 엽니다. 메시지가 표시되면 **연결**을 선택합니다. VM을 만들 때 지정한 사용자 이름과 암호를 입력합니다. **추가 선택 사항**,  > **다른 계정 사용**을 차례로 선택하여 VM을 만들 때 입력한 자격 증명을 지정해야 할 수도 있습니다.
4. **확인**을 선택합니다.
5. 로그인 프로세스 중에 인증서 경고가 나타날 수 있습니다. 경고 메시지가 표시되면 **예** 또는 **계속**을 선택하여 연결을 계속합니다.
6. VM myVMEastUS의 웹 브라우저에서 Traffic Manager 프로필의 DNS 이름을 입력하여 웹 사이트를 봅니다. myIISVMEastUS라는 IIS 서버에 할당된 가중치가 **100**으로 더 높기 때문에 이 서버에서 호스팅되는 웹 사이트로 라우팅됩니다. myIISVMWestEurope이라는 IIS 서버에는 더 낮은 엔드포인트 가중치 **25**가 할당됩니다.

   ![Traffic Manager 프로필 테스트](./media/tutorial-traffic-manager-improve-website-response/eastus-traffic-manager-test.png)

7. VM myVMWestEurope에서 1-6단계를 반복하여 가중치가 적용된 웹 사이트 응답을 확인합니다.

## <a name="delete-the-traffic-manager-profile"></a>Traffic Manager 프로필 삭제

이 자습서에서 만든 리소스 그룹이 더 이상 필요하지 않은 경우 삭제할 수 있습니다. 이렇게 하려면 리소스 그룹(**ResourceGroupTM1** 또는 **ResourceGroupTM2**)을 선택한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [사용자의 지리적 위치에 기반하여 특정 엔드포인트로 트래픽 라우팅](traffic-manager-configure-geographic-routing-method.md)
