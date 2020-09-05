---
title: '자습서: Azure Portal를 사용하여 여러 웹 사이트 호스트'
titleSuffix: Azure Application Gateway
description: 이 자습서에서는 Azure Portal을 사용하여 여러 웹 사이트를 호스트하는 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: 6fb613578e520f50701c9a09169f2d78c0c08c4f
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723999"
---
# <a name="tutorial-create-and-configure-an-application-gateway-to-host-multiple-web-sites-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 여러 웹 사이트를 호스트하는 애플리케이션 게이트웨이 생성 및 구성

Azure Portal을 사용하여 [애플리케이션 게이트웨이](multiple-site-overview.md)를 만들 때 [여러 웹 사이트의 호스팅](overview.md)을 구성할 수 있습니다. 이 자습서에서는 가상 머신을 사용하여 백 엔드 주소 풀을 정의합니다. 그런 다음, 웹 트래픽이 풀에서 적절한 서버에 도착하도록 소유한 도메인을 기준으로 수신기와 규칙을 구성합니다. 이 자습서에서는 여러 도메인을 소유하고 있으며 *www.contoso.com* 및 *www.fabrikam.com* 의 예를 사용한다고 가정합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 애플리케이션 게이트웨이 만들기
> * 백 엔드 서버용 가상 머신 만들기
> * 백 엔드 서버로 백 엔드 풀 만들기
> * 백 엔드 수신기 만들기
> * 라우팅 규칙 만들기
> * 도메인에서 CNAME 레코드 만들기

:::image type="content" source="./media/create-multiple-sites-portal/scenario.png" alt-text="다중 사이트 Application Gateway":::

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

1. Azure Portal의 왼쪽 메뉴에서 **리소스 만들기**를 선택합니다. **새로 만들기** 창이 나타납니다.

2. **네트워킹**을 선택한 다음, **추천** 목록에서 **Application Gateway**를 선택합니다.

### <a name="basics-tab"></a>기본 사항 탭

1. **기본 사항** 탭에서 다음 애플리케이션 게이트웨이 설정에 대한 값을 입력합니다.

   - **리소스 그룹**: 리소스 그룹으로 **myResourceGroupAG**를 선택합니다. 이 리소스 그룹이 없으면 **새로 만들기**를 선택하여 새로 만듭니다.
   - **애플리케이션 게이트웨이 이름**: 애플리케이션 게이트웨이의 이름으로 *myAppGateway*를 입력합니다.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png" alt-text="Application Gateway 만들기":::

2.  Azure가 사용자가 만든 리소스 간에 통신하려면 가상 네트워크가 필요합니다. 새 가상 네트워크를 만들거나 기존 가상 네트워크를 선택할 수 있습니다. 이 예제에서는 애플리케이션 게이트웨이를 만들면서 새 가상 네트워크를 만듭니다. 별도의 서브넷으로 Application Gateway 인스턴스가 만들어집니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다.

    **가상 네트워크 구성**에서 **새로 만들기**를 선택하여 새 가상 네트워크를 만듭니다. **가상 네트워크 만들기** 창이 열리면 다음 값을 입력하여 가상 네트워크 및 두 개의 서브넷을 만듭니다.

    - **Name**: 가상 네트워크의 이름으로 *myVNet*을 입력합니다.

    - **서브넷 이름**(Application Gateway 서브넷): **서브넷** 표에 *Default*라는 서브넷이 표시됩니다. 이 서브넷의 이름을 *myAGSubnet*으로 변경합니다.<br>애플리케이션 게이트웨이 서브넷은 애플리케이션 게이트웨이만 포함할 수 있습니다. 다른 리소스는 허용되지 않습니다.

    - **서브넷 이름**(백 엔드 서버 서브넷): **서브넷** 표의 두 번째 행에 있는 **서브넷 이름** 열에 *myBackendSubnet*을 입력합니다.

    - **주소 범위**(백 엔드 서버 서브넷): **서브넷** 표의 두 번째 행에 *myAGSubnet*의 주소 범위와 겹치지 않는 주소 범위를 입력합니다. 예를 들어 *myAGSubnet*의 주소 범위가 10.0.0.0/24인 경우 *myBackendSubnet*의 주소 범위로 *10.0.1.0/24*를 입력합니다.

    **확인**을 선택하여 **가상 네트워크 만들기** 창을 닫고 가상 네트워크 설정을 저장합니다.

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-vnet.png" alt-text="VNet 만들기":::
    
3. **기본 사항** 탭에서 다른 설정에 대한 기본값을 적용한 다음, **다음: 프런트 엔드**를 선택합니다.

### <a name="frontends-tab"></a>프런트 엔드 탭

1. **프런트 엔드** 탭에서 **프런트 엔드 IP 주소 형식**이 **퍼블릭**으로 설정되어 있는지 확인합니다. <br>사용 사례에 따라 프런트 엔드 IP를 공용 또는 프라이빗 IP로 구성할 수 있습니다. 이 예제에서는 공용 프런트 엔드 IP를 선택합니다.
   > [!NOTE]
   > Application Gateway v2 SKU의 경우 **공용** 프런트 엔드 IP 구성만 선택할 수 있습니다. 프라이빗 프런트 엔드 IP 구성은 현재 v2 SKU에서 사용할 수 없습니다.

2. **퍼블릭 IP 주소**에 대해 **새로 만들기**를 선택하고 퍼블릭 IP 주소 이름으로 *myAGPublicIPAddress*를 입력한 후 **확인**을 선택합니다. 

     :::image type="content" source="./media/application-gateway-create-gateway-portal/application-gateway-create-frontends.png" alt-text="다른 VNet 만들기":::

3. 완료되면 **다음: 백 엔드**를 선택합니다.

### <a name="backends-tab"></a>백 엔드 탭

백 엔드 풀은 요청을 처리하는 백 엔드 서버로 요청을 라우팅하는 데 사용됩니다. 백 엔드 풀은 NIC, 가상 머신 확장 집합, 공용 IP, 내부 IP, FQDN(정규화된 도메인 이름) 및 다중 테넌트 백 엔드(예: Azure App Service)일 수 있습니다. 이 예제에서는 애플리케이션 게이트웨이가 있는 빈 백 엔드 풀을 만든 다음, 백 엔드 대상을 백 엔드 풀에 추가합니다.

1. **백 엔드** 탭에서 **+백 엔드 풀 추가**를 선택합니다.

2. 열리는 **백 엔드 풀 추가** 창에서 다음 값을 입력하여 빈 백 엔드 풀을 만듭니다.

    - **Name**: 백 엔드 풀의 이름으로 *contosoPool*을 입력합니다.
    - **대상 없이 백 엔드 풀 추가**: 대상이 없는 백 엔드 풀을 만들려면 **예**를 선택합니다. 애플리케이션 게이트웨이를 만든 후에 백 엔드 대상을 추가합니다.

3. **백 엔드 풀 추가** 창에서 **추가**를 선택하여 백 엔드 풀 구성을 저장하고 **백 엔드** 탭으로 돌아갑니다.
4. 이제 *fabrikamPool*이라는 또 다른 백 엔드 풀을 추가합니다.

    :::image type="content" source="./media/create-multiple-sites-portal/backend-pools.png" alt-text="백 엔드 만들기":::

4. **백 엔드** 탭에서 **다음: 구성**을 선택합니다.

### <a name="configuration-tab"></a>구성 탭

**구성** 탭에서 회람 규칙을 사용하여 만든 프런트 엔드 풀과 백 엔드 풀을 연결합니다.

1. **라우팅 규칙** 열에서 **규칙 추가**를 선택합니다.

2. 열리는 **회람 규칙 추가** 창에서 **규칙 이름**으로 *contosoRule*을 입력합니다.

3. 라우팅 규칙에는 수신기가 필요합니다. **라우팅 규칙 추가** 창 내의 **수신기** 탭에서 수신기에 대해 다음 값을 입력합니다.

    - **수신기 이름**: 수신기 이름으로 *contosoListener*를 입력합니다.
    - **프런트 엔드 IP**: **퍼블릭**을 선택하여 프런트 엔드에 대해 만든 퍼블릭 IP를 선택합니다.

   **추가 설정**에서 다음을 수행합니다.
   - **수신기 유형**: 다중 사이트
   - **호스트 이름**: **www.contoso.com**

   **수신기** 탭에서 다른 설정에 대해 기본값을 그대로 적용한 다음, **백 엔드 대상** 탭을 선택하여 나머지 라우팅 규칙을 구성합니다.

   :::image type="content" source="./media/create-multiple-sites-portal/routing-rule.png" alt-text="라우팅 규칙 만들기":::

4. **백 엔드 대상** 탭에서 **백 엔드 대상**으로 **contosoPool**을 선택합니다.

5. **Http 설정**에서 **새로 만들기**를 선택하여 새 HTTP 설정을 만듭니다. HTTP 설정에 따라 라우팅 규칙의 동작이 결정됩니다. 열리는 **HTTP 설정 추가** 창에서 **HTTP 설정 이름**으로 *contosoHTTPSetting*을 입력합니다. **HTTP 설정 추가** 창에서 다른 설정에 대해 기본값을 그대로 적용한 다음, **추가**를 선택하여 **라우팅 규칙 추가** 창으로 돌아옵니다. 

6. **라우팅 규칙 추가** 창에서 **추가**를 선택하여 라우팅 규칙을 저장하고 **구성** 탭으로 돌아옵니다.
7. **규칙 추가**를 선택하고 Fabrikam에 대한 유사한 규칙, 수신기, 백 엔드 대상 및 HTTP 설정을 추가합니다.

     :::image type="content" source="./media/create-multiple-sites-portal/fabrikam-rule.png" alt-text="Fabrikam 규칙":::

7. 완료되면 **다음: 태그**를 선택하고 **다음: 리뷰 + 만들기**를 클릭합니다.

### <a name="review--create-tab"></a>리뷰 + 만들기 탭

**리뷰 + 만들기** 탭에서 설정을 검토한 다음, **만들기**를 선택하여 가상 네트워크, 공용 IP 주소 및 애플리케이션 게이트웨이를 만듭니다. Azure가 애플리케이션 게이트웨이를 만들 때까지 몇 분 정도 걸릴 수 있습니다.

배포가 성공적으로 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

## <a name="add-backend-targets"></a>백 엔드 대상 추가

이 예제에서는 가상 머신을 대상 백 엔드로 사용합니다. 기존 가상 머신을 사용해도 되고 새로 만들어도 됩니다. Azure에서 애플리케이션 게이트웨이의 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다.

백 엔드 대상을 추가하기 위해 다음을 수행합니다.

1. 백 엔드 서버로 사용할 새 VM으로 *contosoVM* 및 *fabrikamVM*을 만듭니다.
2. 애플리케이션 게이트웨이가 성공적으로 만들어졌는지 확인하기 위해 가상 머신에 IIS를 설치합니다.
3. 백 엔드 서버를 백 엔드 풀에 추가합니다.

### <a name="create-a-virtual-machine"></a>가상 머신 만들기

1. Azure Portal에서 **리소스 만들기**를 선택합니다. **새로 만들기** 창이 나타납니다.
2. **컴퓨팅**을 선택한 다음, **인기** 목록에서 **Windows Server 2016 Datacenter**를 선택합니다. **가상 머신 만들기** 페이지가 표시됩니다.<br>Application Gateway는 백 엔드 풀에서 사용한 가상 머신 유형에 관계없이 트래픽을 라우팅할 수 있습니다. 이 예제에서는 Windows Server 2016 Datacenter를 사용합니다.
3. **기본 사항** 탭에서 다음 가상 머신 설정의 값을 입력합니다.

    - **리소스 그룹**: 리소스 그룹 이름으로 **myResourceGroupAG**를 선택합니다.
    - **가상 머신 이름**: 가상 머신 이름으로 *contosoVM*을 입력합니다.
    - **사용자 이름**: 관리자 사용자 이름의 이름을 입력합니다.
    - **암호**: 관리자의 암호를 입력합니다.
1. 나머지는 기본값으로 두고 **다음: 디스크**를 선택합니다.  
2. **디스크** 탭을 기본값으로 두고 **다음: 네트워킹**을 선택합니다.
3. **네트워킹** 탭에서 **가상 네트워크**로 **myVNet**이 선택되었고 **서브넷**이 **myBackendSubnet**으로 설정되었는지 확인합니다. 나머지는 기본값으로 두고 **다음: 관리**를 선택합니다.<br>Application Gateway는 가상 네트워크 외부의 인스턴스와 통신할 수 있지만, IP가 연결되어야 합니다.
4. **관리** 탭에서 **부트 진단**을 **해제**합니다. 나머지는 기본값으로 두고 **검토 + 만들기**를 선택합니다.
5. **검토 + 만들기** 탭에서 설정을 검토하고, 유효성 검사 오류를 수정하고, **만들기**를 선택합니다.
6. 가상 머신 만들기가 완료되기를 기다렸다가 계속합니다.

### <a name="install-iis-for-testing"></a>테스트를 위해 IIS 설치

이 예제에서는 Azure가 애플리케이션 게이트웨이를 성공적으로 만들었는지만 확인할 목적으로 가상 머신에 IIS를 설치합니다.

1. [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell)을 엽니다. 이렇게 하려면 Azure Portal의 위쪽 탐색 모음에서 **Cloud Shell**을 선택한 다음, 드롭다운 목록에서 **PowerShell**을 선택합니다. 

    ![사용자 지정 확장 설치](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

2. 다음 명령을 실행하여 가상 머신에 IIS를 설치하고 <location\>을 리소스 그룹 영역으로 대체합니다. 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location <location>
    ```

3. 앞에서 완료한 단계를 사용하여 두 번째 가상 머신을 만들고 IIS를 설치합니다. 가상 머신 이름으로 *fabrikamVM*을 사용하고, **VMName** 설정으로 **Set-AzVMExtension** cmdlet을 사용합니다.

### <a name="add-backend-servers-to-backend-pools"></a>백 엔드 풀에 백 엔드 서버 추가

1. **모든 리소스**를 선택한 다음, **myAppGateway**를 선택합니다.

2. 왼쪽 메뉴에서 **백 엔드 풀**을 선택합니다.

3. **contosoPool**을 선택합니다.

4. **대상** 아래의 드롭다운 메뉴에서 **가상 머신**을 선택합니다.

5. **가상 머신** 및 **네트워크 인터페이스** 아래의 드롭다운 목록에서 가상 머신 **contosoVM**, 그리고 이 가상 머신과 연결된 네트워크 인터페이스를 선택합니다.

    ![백 엔드 서버 추가](./media/create-multiple-sites-portal/edit-backend-pool.png)

6. **저장**을 선택합니다.
7. 같은 과정을 반복하여 *fabrikamVM* 및 인터페이스를 *fabrikamPool*에 추가합니다.

배포가 완료될 때까지 기다렸다가 다음 단계로 진행합니다.

## <a name="create-a-www-a-record-in-your-domains"></a>도메인에서 www A 레코드 만들기

공용 IP 주소를 사용하여 애플리케이션 게이트웨이를 만든 후에는 IP 주소를 가져와서 도메인에서 A 레코드를 만드는 데 사용할 수 있습니다. 

1. **모든 리소스**를 클릭한 다음, **myAGPublicIPAddress**를 클릭합니다.

    ![애플리케이션 게이트웨이 DNS 주소 기록](./media/create-multiple-sites-portal/public-ip.png)

2. IP 주소를 복사하여 도메인의 새 *www* A 레코드 값으로 사용합니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

1. 브라우저의 주소 표시줄에 도메인 이름을 입력합니다. 예: `http://www.contoso.com`

    ![애플리케이션 게이트웨이에서 contoso 사이트 테스트](./media/create-multiple-sites-portal/application-gateway-iistest.png)

2. 주소를 다른 도메인으로 변경하면 다음 예제와 같은 내용이 표시됩니다.

    ![애플리케이션 게이트웨이에서 fabrikam 사이트 테스트](./media/create-multiple-sites-portal/application-gateway-iistest2.png)

## <a name="clean-up-resources"></a>리소스 정리

애플리케이션 게이트웨이로 만든 리소스가 더 이상 필요 없으면 리소스 그룹을 제거합니다. 리소스 그룹을 제거하면 애플리케이션 게이트웨이 및 모든 관련 리소스도 함께 제거됩니다.

리소스 그룹을 제거하려면:

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹**을 선택합니다.
2. **리소스 그룹** 페이지의 목록에서 **myResourceGroupAG**를 검색하여 선택합니다.
3. **리소스 그룹** 페이지에서 **리소스 그룹 삭제**를 선택합니다.
4. **리소스 그룹 이름 입력**에 *myResourceGroupAG*를 입력한 다음, **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Application Gateway를 통해 수행할 수 있는 작업에 대한 자세한 정보](application-gateway-introduction.md)