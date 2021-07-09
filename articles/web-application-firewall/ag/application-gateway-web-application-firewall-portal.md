---
title: '자습서: 포털을 사용하여 만들기 - Web Application Firewall'
description: 이 자습서에서는 Azure Portal을 사용하여 웹 애플리케이션 방화벽이 있는 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 05/19/2021
ms.author: victorh
ms.openlocfilehash: f72706d4bb1d9470518fb3b14ee756a1fe1551db
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110480549"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 웹 애플리케이션 방화벽이 있는 애플리케이션 게이트웨이를 만듭니다.

이 자습서에서는 Azure Portal을 사용하여 WAF(웹 애플리케이션 방화벽)이 있는 애플리케이션 게이트웨이를 만드는 방법을 보여 줍니다. WAF는 [OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 규칙을 사용하여 애플리케이션을 보호합니다. 이러한 규칙에는 SQL 삽입, 사이트 간 스크립팅 공격 및 세션 하이재킹과 같은 공격으로부터의 보호가 포함됩니다. Application Gateway를 만든 후 올바르게 작동하는지 테스트합니다. Azure Application Gateway를 통해 수신기를 포트에 할당하고, 규칙을 만들고, 백 엔드 풀에 리소스를 추가하여 애플리케이션 웹 트래픽을 특정 리소스로 보냅니다. 간단한 설명을 위해 이 자습서에서는 공용 프런트 엔드 IP 하나, 이 애플리케이션 게이트웨이에 단일 사이트를 호스트하는 기본 수신기 하나, 백 엔드 풀에 사용되는 가상 머신 두 개, 기본 요청 회람 규칙 하나를 이용하는 간단한 설정을 사용합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * WAF를 사용하는 애플리케이션 게이트웨이 만들기
> * 백 엔드 서버로 사용되는 가상 머신 만들기
> * 스토리지 계정 만들기 및 진단 구성
> * 애플리케이션 게이트웨이 테스트

![웹 애플리케이션 방화벽 예제](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

1. Azure Portal의 왼쪽 메뉴에서 **리소스 만들기** 를 선택합니다. **새로 만들기** 창이 나타납니다.

2. **네트워킹** 을 선택한 다음, **추천** 목록에서 **Application Gateway** 를 선택합니다.

### <a name="basics-tab"></a>기본 사항 탭

1. **기본 사항** 탭에서 다음 애플리케이션 게이트웨이 설정에 대한 값을 입력합니다.

   - **리소스 그룹**: 리소스 그룹으로 **myResourceGroupAG** 를 선택합니다. 이 리소스 그룹이 없으면 **새로 만들기** 를 선택하여 새로 만듭니다.
   - **애플리케이션 게이트웨이 이름**: 애플리케이션 게이트웨이의 이름으로 *myAppGateway* 를 입력합니다.
   - **계층**: **WAF V2** 를 선택합니다.

     ![새 애플리케이션 게이트웨이 만들기: 기본 사항](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  Azure가 사용자가 만든 리소스 간에 통신하려면 가상 네트워크가 필요합니다. 새 가상 네트워크를 만들거나 기존 가상 네트워크를 선택할 수 있습니다. 이 예제에서는 애플리케이션 게이트웨이를 만들면서 새 가상 네트워크를 만듭니다. 별도의 서브넷으로 Application Gateway 인스턴스가 만들어집니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다.

    **가상 네트워크 구성** 에서 **새로 만들기** 를 선택하여 새 가상 네트워크를 만듭니다. **가상 네트워크 만들기** 창이 열리면 다음 값을 입력하여 가상 네트워크 및 두 개의 서브넷을 만듭니다.

    - **Name**: 가상 네트워크의 이름으로 *myVNet* 을 입력합니다.

    - **서브넷 이름**(Application Gateway 서브넷): **서브넷** 표에 *Default* 라는 서브넷이 표시됩니다. 이 서브넷의 이름을 *myAGSubnet* 으로 변경합니다.<br>애플리케이션 게이트웨이 서브넷은 애플리케이션 게이트웨이만 포함할 수 있습니다. 다른 리소스는 허용되지 않습니다.

    - **서브넷 이름**(백 엔드 서버 서브넷): **서브넷** 표의 두 번째 행에 있는 **서브넷 이름** 열에 *myBackendSubnet* 을 입력합니다.

    - **주소 범위**(백 엔드 서버 서브넷): **서브넷** 표의 두 번째 행에 *myAGSubnet* 의 주소 범위와 겹치지 않는 주소 범위를 입력합니다. 예를 들어 *myAGSubnet* 의 주소 범위가 10.21.0.0/24인 경우 *myBackendSubnet* 의 주소 범위로 *10.21.1.0/24* 를 입력합니다.

    **확인** 을 선택하여 **가상 네트워크 만들기** 창을 닫고 가상 네트워크 설정을 저장합니다.

     ![새 애플리케이션 게이트웨이 만들기: 가상 네트워크](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. **기본 사항** 탭에서 다른 설정에 대한 기본값을 적용한 다음, **다음: 프런트 엔드** 를 선택합니다.

### <a name="frontends-tab"></a>프런트 엔드 탭

1. **프런트 엔드** 탭에서 **프런트 엔드 IP 주소 형식** 이 **퍼블릭** 으로 설정되어 있는지 확인합니다. <br>사용 사례에 따라 프런트 엔드 IP를 공용 또는 프라이빗 IP로 구성할 수 있습니다. 이 예제에서는 공용 프런트 엔드 IP를 선택합니다.
   > [!NOTE]
   > Application Gateway v2 SKU의 경우 **공용** 프런트 엔드 IP 구성만 선택할 수 있습니다. 프라이빗 프런트 엔드 IP 구성은 현재 v2 SKU에서 사용할 수 없습니다.

2. **공용 IP 주소** 에 대해 **새로 추가** 를 선택하고 공용 IP 주소 이름으로 *myAGPublicIPAddress* 를 입력한 다음, **확인** 을 선택합니다. 

     ![새 애플리케이션 게이트웨이 만들기: 프런트 엔드](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. 완료되면 **다음: 백 엔드** 를 선택합니다.

### <a name="backends-tab"></a>백 엔드 탭

백 엔드 풀은 요청을 처리하는 백 엔드 서버로 요청을 라우팅하는 데 사용됩니다. 백 엔드 풀은 NIC, 가상 머신 확장 집합, 공용 IP, 내부 IP, FQDN(정규화된 도메인 이름) 및 다중 테넌트 백 엔드(예: Azure App Service)로 구성될 수 있습니다. 이 예제에서는 애플리케이션 게이트웨이가 있는 빈 백 엔드 풀을 만든 다음, 나중에 백 엔드 대상을 백 엔드 풀에 추가합니다.

1. **백 엔드** 탭에서 **백 엔드 풀 추가** 를 선택합니다.

2. 열리는 **백 엔드 풀 추가** 창에서 다음 값을 입력하여 빈 백 엔드 풀을 만듭니다.

    - **Name**: 백 엔드 풀의 이름으로 *myBackendPool* 을 입력합니다.
    - **대상 없이 백 엔드 풀 추가**: 대상이 없는 백 엔드 풀을 만들려면 **예** 를 선택합니다. 애플리케이션 게이트웨이를 만든 후에 백 엔드 대상을 추가합니다.

3. **백 엔드 풀 추가** 창에서 **추가** 를 선택하여 백 엔드 풀 구성을 저장하고 **백 엔드** 탭으로 돌아갑니다.

     ![새 애플리케이션 게이트웨이 만들기: 백 엔드](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. **백 엔드** 탭에서 **다음: 구성** 을 선택합니다.

### <a name="configuration-tab"></a>구성 탭

**구성** 탭에서 라우팅 규칙을 사용하여 만든 프런트 엔드 및 백 엔드 풀을 연결합니다.

1. **라우팅 규칙** 열에서 **라우팅 규칙 추가** 를 선택합니다.

2. 열리는 **라우팅 규칙 추가** 창에서 **규칙 이름** 으로 *myRoutingRule* 을 입력합니다.

3. 라우팅 규칙에는 수신기가 필요합니다. **라우팅 규칙 추가** 창 내의 **수신기** 탭에서 수신기에 대해 다음 값을 입력합니다.

    - **수신기 이름**: 수신기 이름으로 *myListener* 를 입력합니다.
    - **프런트 엔드 IP**: **퍼블릭** 을 선택하여 프런트 엔드에 대해 만든 퍼블릭 IP를 선택합니다.
  
      **수신기** 탭에서 다른 설정에 대해 기본값을 그대로 적용한 다음, **백 엔드 대상** 탭을 선택하여 나머지 라우팅 규칙을 구성합니다.

   ![새 애플리케이션 게이트웨이 만들기: 수신기](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. **백 엔드 대상** 탭에서 **백 엔드 대상** 으로 **myBackendPool** 을 선택합니다.

5. **HTTP 설정** 의 경우 **새로 추가** 를 선택하여 새 HTTP 설정을 만듭니다. HTTP 설정에 따라 라우팅 규칙의 동작이 결정됩니다. 열리는 **HTTP 설정 추가** 창에서 **HTTP 설정 이름** 으로 *myHTTPSetting* 을 입력합니다. **HTTP 설정 추가** 창에서 다른 설정에 대해 기본값을 그대로 적용한 다음, **추가** 를 선택하여 **라우팅 규칙 추가** 창으로 돌아옵니다. 

     ![새 애플리케이션 게이트웨이 만들기: HTTP 설정](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. **라우팅 규칙 추가** 창에서 **추가** 를 선택하여 라우팅 규칙을 저장하고 **구성** 탭으로 돌아옵니다.

     ![새 애플리케이션 게이트웨이 만들기: 라우팅 규칙](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. 완료되면 **다음: 태그** 를 선택하고 **다음: 리뷰 + 만들기** 를 클릭합니다.

### <a name="review--create-tab"></a>리뷰 + 만들기 탭

**리뷰 + 만들기** 탭에서 설정을 검토한 다음, **만들기** 를 선택하여 가상 네트워크, 공용 IP 주소 및 애플리케이션 게이트웨이를 만듭니다. Azure가 애플리케이션 게이트웨이를 만들 때까지 몇 분 정도 걸릴 수 있습니다. 

배포가 성공적으로 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

## <a name="add-backend-targets"></a>백 엔드 대상 추가

이 예제에서는 가상 머신을 대상 백 엔드로 사용합니다. 기존 가상 머신을 사용해도 되고 새로 만들어도 됩니다. Azure에서 애플리케이션 게이트웨이의 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다.

이 작업을 수행하려면 다음이 필요합니다.

1. 백 엔드 서버로 사용할 VM 2개, *myVM* 및 *myVM2* 를 만듭니다.
2. 애플리케이션 게이트웨이가 성공적으로 만들어졌는지 확인하기 위해 가상 머신에 IIS를 설치합니다.
3. 백 엔드 서버를 백 엔드 풀에 추가합니다.

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. Azure Portal에서 **리소스 만들기** 를 선택합니다. **새로 만들기** 창이 나타납니다.
2. **인기 목록** 에서 **Windows Server 2019 Datacenter** 를 선택합니다. **가상 머신 만들기** 페이지가 표시됩니다.<br>Application Gateway는 백 엔드 풀에서 사용한 가상 머신 유형에 관계없이 트래픽을 라우팅할 수 있습니다. 이 예제에서는 Windows Server 2019 Datacenter를 사용합니다.
3. **기본 사항** 탭에서 다음 가상 머신 설정의 값을 입력합니다.

    - **리소스 그룹**: 리소스 그룹 이름으로 **myResourceGroupAG** 를 선택합니다.
    - **가상 머신 이름**: 가상 머신의 이름으로 *myVM* 을 입력합니다.
    - **사용자 이름**: 관리자 사용자 이름의 이름을 입력합니다.
    - **암호**: 관리자 암호의 암호를 입력합니다.
    - **공용 인바운드 포트**: **없음** 을 선택합니다.
4. 나머지는 기본값으로 두고 **다음: 디스크** 를 선택합니다.  
5. **디스크** 탭을 기본값으로 두고 **다음: 네트워킹** 을 선택합니다.
6. **네트워킹** 탭에서 **가상 네트워크** 로 **myVNet** 이 선택되었고 **서브넷** 이 **myBackendSubnet** 으로 설정되었는지 확인합니다.
1. **공용 IP** 에 대해 **없음** 을 선택합니다.
1. 나머지는 기본값으로 두고 **다음: 관리** 를 선택합니다.
1. **관리** 탭에서 **부트 진단** 을 **사용 안 함** 으로 설정합니다. 나머지는 기본값으로 두고 **검토 + 만들기** 를 선택합니다.
1. **검토 + 만들기** 탭에서 설정을 검토하고, 유효성 검사 오류를 수정하고, **만들기** 를 선택합니다.
1. 가상 머신 만들기가 완료되기를 기다렸다가 계속합니다.

### <a name="install-iis-for-testing"></a>테스트를 위해 IIS 설치

이 예제에서는 Azure가 애플리케이션 게이트웨이를 성공적으로 만들었는지만 확인할 목적으로 가상 머신에 IIS를 설치합니다.

1. [Azure PowerShell](../../cloud-shell/quickstart-powershell.md)을 엽니다. 이렇게 하려면 Azure Portal의 위쪽 탐색 모음에서 **Cloud Shell** 을 선택한 다음, 드롭다운 목록에서 **PowerShell** 을 선택합니다. 

    ![사용자 지정 확장 설치](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. 사용자 환경의 위치 매개 변수를 설정한 후, 다음 명령을 실행하여 가상 머신에 IIS를 설치합니다. 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 앞에서 완료한 단계를 사용하여 두 번째 가상 머신을 만들고 IIS를 설치합니다. 가상 머신 이름에 *myVM2* 를 사용하고, **VMName** 설정에 **Set-AzVMExtension** cmdlet을 사용합니다.

### <a name="add-backend-servers-to-backend-pool"></a>백 엔드 풀에 백 엔드 서버 추가

1. **모든 리소스** 를 선택한 다음, **myAppGateway** 를 선택합니다.

2. 왼쪽 메뉴에서 **백 엔드 풀** 을 선택합니다.

3. **myBackendPool** 을 선택합니다.

4. **대상 유형** 아래의 드롭다운 목록에서 **가상 머신** 을 선택합니다.

5. **대상** 아래의 드롭다운 목록에서 **myVM** 에 연결된 네트워크 인터페이스를 선택합니다.
1. **myVM2** 에 대해 반복합니다.

   :::image type="content" source="../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png" alt-text="백 엔드 서버 추가":::


6. **저장** 을 선택합니다.

7. 배포가 완료될 때까지 기다렸다가 다음 단계로 진행합니다.

   
## <a name="create-and-link-a-web-application-firewall-policy"></a>웹 애플리케이션 방화벽 정책 만들기 및 연결

모든 WAF 사용자 지정 및 설정은 WAF 정책이라고 부르는 별도의 개체에 있습니다. 정책은 Application Gateway와 연결되어 있어야 합니다. 

관리형 DRS(기본 규칙 집합)를 사용하여 기본 WAF 정책을 만듭니다.

1. 포털의 왼쪽 위에서 **리소스 만들기** 를 선택합니다. **WAF** 를 검색하여 **Web Application Firewall** 을 선택한 다음, **만들기** 를 선택합니다.
2. **WAF 정책 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택하고 나머지 설정에 대한 기본값을 적용한 다음, **검토 + 만들기** 를 선택합니다.

   |설정  |값  |
   |---------|---------|
   |정책     |국가 WAF(Application Gateway)|
   |Subscription     |구독 이름 선택|
   |Resource group     |**myResourceGroupAG** 선택|
   |정책 이름     |WAF 정책의 고유한 이름을 입력합니다.|
1. **다음: 관리형 규칙** 을 선택합니다.
1. 기본값을 적용한 후, **다음: 정책 설정** 을 선택합니다.
1. 기본값을 적용하고 **다음: 사용자 지정 규칙** 을 선택합니다.
1. **다음: 연결** 을 선택합니다.
1. **연결 추가** 를 선택한 다음, **Application Gateway** 를 선택합니다.
1. 현재 구성과 다른 경우에도 **Web Application Firewall 정책 구성 적용** 확인란을 선택합니다.
1. **추가** 를 선택합니다.

   > [!NOTE]
   > 이미 정책이 설치되어 있는 Application Gateway(또는 수신기)에 정책을 할당하면 원래 정책을 덮어쓰고 새 정책으로 대체합니다.
4. **검토 + 생성** 를 선택한 다음, **생성** 를 선택합니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

애플리케이션 게이트웨이를 만들기 위해 반드시 IIS가 필요한 것은 아니지만, Azure가 애플리케이션 게이트웨이를 성공적으로 만들었는지 확인하기 위해 설치했습니다. IIS를 사용하여 애플리케이션 게이트웨이 테스트:

1. **개요** 페이지에서 애플리케이션 게이트웨이의 공용 IP 주소를 찾습니다.![애플리케이션 게이트웨이의 공용 IP 주소 기록](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   또는 **모든 리소스** 를 선택하고, 검색 상자에 *myAGPublicIPAddress* 를 입력한 다음, 검색 결과에서 선택할 수도 있습니다. Azure는 공용 IP 주소를 **개요** 페이지에 표시합니다.
1. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다.
1. 응답을 확인합니다. 응답이 유효하면 애플리케이션 게이트웨이가 성공적으로 만들어졌으며 백 엔드에 성공적으로 연결할 수 있다는 의미입니다.

   ![애플리케이션 게이트웨이 테스트](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션 게이트웨이로 만든 리소스가 더 이상 필요 없으면 리소스 그룹을 제거합니다. 리소스 그룹을 제거하면 애플리케이션 게이트웨이 및 모든 관련 리소스도 함께 제거됩니다. 

리소스 그룹을 제거하려면:

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **리소스 그룹** 페이지의 목록에서 **myResourceGroupAG** 를 검색하여 선택합니다.
3. **리소스 그룹** 페이지에서 **리소스 그룹 삭제** 를 선택합니다.
4. **리소스 그룹 이름 입력** 에 *myResourceGroupAG* 를 입력한 다음, **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [웹 애플리케이션 방화벽에 대한 자세한 정보](../overview.md)