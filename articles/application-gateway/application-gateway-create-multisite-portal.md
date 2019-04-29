---
title: 여러 사이트를 호스팅하는 애플리케이션 게이트웨이 만들기 - Azure Portal | Microsoft Docs
description: Azure Portal을 사용하여 여러 사이트를 호스팅하는 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/26/2018
ms.author: victorh
ms.openlocfilehash: 85113a5007a171459b831684f584773ba4328b94
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122423"
---
# <a name="create-an-application-gateway-with-multiple-site-hosting-using-the-azure-portal"></a>Azure Portal을 사용하여 여러 사이트를 호스팅하는 애플리케이션 게이트웨이 만들기

Azure Portal을 사용하여 [애플리케이션 게이트웨이](application-gateway-multi-site-overview.md)를 만들 때 [여러 웹 사이트의 호스팅](application-gateway-introduction.md)을 구성할 수 있습니다. 이 자습서에서는 가상 머신 확장 집합을 사용하여 백 엔드 풀을 만듭니다. 그런 다음, 웹 트래픽이 풀에서 적절한 서버에 도착하도록 소유한 도메인을 기준으로 수신기와 규칙을 구성합니다. 이 자습서에서는 여러 도메인을 소유하고 있으며 *www\.contoso.com* 및 *www\.fabrikam.com*의 예를 사용한다고 가정합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 애플리케이션 게이트웨이 만들기
> * 백 엔드 서버용 가상 머신 만들기
> * 백 엔드 서버로 백 엔드 풀 만들기
> * 수신기 및 라우팅 규칙 만들기
> * 도메인에서 CNAME 레코드 만들기

![다중 사이트 라우팅 예](./media/application-gateway-create-multisite-portal/scenario.png)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="log-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

가상 네트워크는 사용자가 만든 리소스 간의 통신에 필요합니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다. 애플리케이션 게이트웨이를 만드는 동시에 가상 네트워크를 만들 수 있습니다.

1. Azure Portal의 왼쪽 위에서 **새로 만들기**를 클릭합니다.
2. **네트워킹**을 선택한 다음, 추천 목록에서 **Application Gateway**를 선택합니다.
3. 애플리케이션 게이트웨이에 대해 다음 값을 입력합니다.

   - *myAppGateway* - 애플리케이션 게이트웨이의 이름
   - *myResourceGroupAG* - 새 리소스 그룹의 이름

     ![새 애플리케이션 게이트웨이 만들기](./media/application-gateway-create-multisite-portal/application-gateway-create.png)

4. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
5. **가상 네트워크 선택**을 클릭하고 **새로 만들기**를 클릭한 다음, 가상 네트워크에 대해 다음 값을 입력합니다.

   - *myVNet* - 가상 네트워크 이름
   - *10.0.0.0/16* - 가상 네트워크 주소 공간
   - *myAGSubnet* - 서브넷 이름
   - *10.0.0.0/24* - 서브넷 주소 공간

     ![가상 네트워크 만들기](./media/application-gateway-create-multisite-portal/application-gateway-vnet.png)

6. **확인**을 클릭하여 가상 네트워크 및 서브넷을 만듭니다.
7. **공용 IP 주소 선택**을 클릭하고 **새로 만들기**를 클릭한 다음, 공용 IP 주소의 이름을 입력합니다. 이 예제에서 공용 IP 주소의 이름은 *myAGPublicIPAddress*입니다. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
8. 수신기 구성에 대한 기본값을 수락하고 웹 애플리케이션 방화벽을 사용하지 않도록 유지한 다음, **확인**을 클릭합니다.
9. 요약 페이지에서 설정을 검토한 다음, **확인**을 클릭하여 네트워크 리소스와 애플리케이션 게이트웨이를 만듭니다. 애플리케이션 게이트웨이가 생성되는 데 몇 분이 걸릴 수 있습니다. 배포가 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

### <a name="add-a-subnet"></a>서브넷 추가

1. 왼쪽 메뉴에서 **모든 리소스**를 클릭한 다음, 리소스 목록에서 **myVNet**을 클릭합니다.
2. **서브넷**을 클릭한 다음, **서브넷**을 클릭합니다.

    ![서브넷 만들기](./media/application-gateway-create-multisite-portal/application-gateway-subnet.png)

3. 서브넷 이름에 *myBackendSubnet*을 입력한 다음, **확인**을 클릭합니다.

## <a name="create-virtual-machines"></a>가상 머신 만들기

이 예제에서는 애플리케이션 게이트웨이에 대한 백 엔드 서버로 사용할 두 개의 가상 머신을 만듭니다. 트래픽이 올바르게 라우팅되는지 확인하기 위해 가상 머신에 IIS도 설치합니다.

1. **새로 만들기**를 클릭합니다.
2. **Compute**를 클릭한 다음, 추천 목록에서 **Windows Server 2016 Datacenter**를 선택합니다.
3. 가상 머신에 대해 다음 값을 입력합니다.

    - *contosoVM* - 가상 머신의 이름
    - *azureuser* - 관리자 사용자 이름
    - *Azure123456!* - 암호
    - **기존 항목 사용**을 선택한 다음, *myResourceGroupAG*를 선택합니다.

4. **확인**을 클릭합니다.
5. 가상 머신의 크기에 대해 **DS1_V2**를 선택하고 **선택**을 클릭합니다.
6. 가상 네트워크에 대해 **myVNet**이 선택되어 있고 서브넷이 **myBackendSubnet**인지 확인합니다. 
7. **사용 안 함**을 클릭하여 부팅 진단을 사용하지 않도록 설정합니다.
8. **확인**을 클릭하고 요약 페이지에서 설정을 검토한 다음, **만들기**를 클릭합니다.

### <a name="install-iis"></a>IIS 설치

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. 대화형 셸을 열고 **PowerShell**로 설정되어 있는지 확인합니다.

    ![사용자 지정 확장 설치](./media/application-gateway-create-multisite-portal/application-gateway-extension.png)

2. 다음 명령을 실행하여 가상 머신에 IIS를 설치합니다. 

    ```azurepowershell-interactive
    $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -Location eastus `
      -ExtensionName IIS `
      -VMName contosoVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -Settings $publicSettings
    ```

3. 두 번째 가상 머신을 만들고, 방금 완료한 단계를 사용하여 IIS를 설치합니다. Set-AzVMExtension에서 VMName의 값과 이름에 *fabrikamVM*의 이름을 입력합니다.

## <a name="create-backend-pools-with-the-virtual-machines"></a>가상 머신으로 백 엔드 풀 만들기

1. **모든 리소스**를 클릭한 다음, **myAppGateway**를 클릭합니다.
2. **백 엔드 풀**을 클릭한 다음, **추가**를 클릭합니다.
3. *contosoPool*의 이름을 입력하고 **대상 추가**를 사용하여 *contosoVM*을 추가합니다.

    ![백 엔드 서버 추가](./media/application-gateway-create-multisite-portal/application-gateway-multisite-backendpool.png)

4. **확인**을 클릭합니다.
5. **백엔드 풀**을 클릭한 다음, **추가**를 클릭합니다.
6. 방금 완료한 단계를 사용하여 *fabrikamVM*으로 *fabrikamPool*을 만듭니다.

## <a name="create-listeners-and-routing-rules"></a>수신기 및 라우팅 규칙 만들기

1. **수신기**를 클릭한 후 **다중 사이트**를 클릭합니다.
2. 수신기에 대해 다음 값을 입력합니다.
    
   - *contosoListener* - 수신기의 이름
   - *www\.contoso.com* -이 호스트 이름 예에 도메인 이름으로 바꿉니다.

3. **확인**을 클릭합니다.
4. *fabrikamListener* 이름을 사용하여 두 번째 수신기를 만들고 두 번째 도메인 이름을 사용합니다. 이 예에서 *www\.fabrikam.com* 사용 됩니다.

규칙은 나열된 순서대로 처리되고 트래픽은 특이성에 관계없이 일치하는 첫 번째 규칙을 사용하여 전달됩니다. 예를 들어 기본 수신기를 사용하는 규칙과 다중 사이트 수신기를 사용하는 규칙이 둘 다 같은 포트에 있는 경우 다중 사이트 규칙이 예상대로 작동하려면 다중 사이트 수신기를 사용하는 규칙은 기본 수신기를 사용하는 규칙 앞에 나열되어야 합니다. 

이 예제에서는 애플리케이션 게이트웨이가 생성되었을 때 두 개의 새 규칙을 만들고 생성된 기본 규칙을 삭제합니다. 

1. **규칙**을 클릭한 다음, **기본**을 클릭합니다.
2. 이름에 *contosoRule*을 입력합니다.
3. 수신기로 *contosoListener*를 선택합니다.
4. 백 엔드 풀로 *contosoPool*을 선택합니다.

    ![경로 기반 규칙 만들기](./media/application-gateway-create-multisite-portal/application-gateway-multisite-rule.png)

5. **확인**을 클릭합니다.
6. *fabrikamRule*, *fabrikamListener* 및 *fabrikamPool*의 이름을 사용하여 두 번째 규칙을 만듭니다.
7. *rule1*이라는 기본 규칙을 클릭한 후 **삭제**를 클릭하여 기본 규칙을 삭제합니다.

## <a name="create-a-cname-record-in-your-domain"></a>도메인에서 CNAME 레코드 만들기

애플리케이션 게이트웨이가 해당 공용 IP 주소로 생성된 후 DNS 주소를 가져와 도메인에서 CNAME 레코드를 만드는 데 사용할 수 있습니다. A 레코드를 사용할 경우 애플리케이션 게이트웨이를 다시 시작할 때 VIP가 변경될 수 있으므로 권장되지 않습니다.

1. **모든 리소스**를 클릭한 다음, **myAGPublicIPAddress**를 클릭합니다.

    ![애플리케이션 게이트웨이 DNS 주소 기록](./media/application-gateway-create-multisite-portal/application-gateway-multisite-dns.png)

2. DNS 주소를 복사하고 도메인에서 새 CNAME 레코드에 대한 값으로 사용합니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

1. 브라우저의 주소 표시줄에 도메인 이름을 입력합니다. 예: http://www.contoso.com

    ![애플리케이션 게이트웨이에서 contoso 사이트 테스트](./media/application-gateway-create-multisite-portal/application-gateway-iistest.png)

2. 주소를 다른 도메인으로 변경하면 다음 예제와 같은 내용이 표시됩니다.

    ![애플리케이션 게이트웨이에서 fabrikam 사이트 테스트](./media/application-gateway-create-multisite-portal/application-gateway-iistest2.png)

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 애플리케이션 게이트웨이 만들기
> * 백 엔드 서버용 가상 머신 만들기
> * 백 엔드 서버로 백 엔드 풀 만들기
> * 수신기 및 라우팅 규칙 만들기
> * 도메인에서 CNAME 레코드 만들기

> [!div class="nextstepaction"]
> [애플리케이션 게이트웨이 통해 수행할 수 있는 작업에 대한 자세한 정보](application-gateway-introduction.md)
