---
title: Azure Portal을 사용하여 HTTP 및 HTTPS 간의 리디렉션으로 애플리케이션 게이트웨이 만들기
description: Azure Portal을 사용하여 HTTP에서 HTTPS로 리디렉션된 트래픽으로 애플리케이션 게이트웨이를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 12/7/2018
ms.author: victorh
ms.openlocfilehash: 17eef2fc2608ca4ccbabff8179cd63798d275582
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62101464"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Azure Portal을 사용하여 HTTP 및 HTTPS 간의 리디렉션으로 애플리케이션 게이트웨이 만들기

Azure Portal을 사용하여 SSL 종료를 위한 인증서로 [애플리케이션 게이트웨이](overview.md)를 만들 수 있습니다. 라우팅 규칙은 애플리케이션 게이트웨이에서 HTTP 트래픽을 HTTPS 포트로 리디렉션하는 데 사용됩니다. 이 예제에서는 두 개의 가상 머신 인스턴스를 포함하는 애플리케이션 게이트웨이의 백 엔드 풀에 대한 [가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)도 만듭니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 자체 서명된 인증서 만들기
> * 네트워크 설정
> * 인증서가 있는 애플리케이션 게이트웨이 만들기
> * 수신기 및 리디렉션 규칙 추가
> * 기본 백 엔드 풀을 사용하여 가상 머신 확장 집합 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

이 자습서에는 Azure PowerShell 모듈 버전 1.0.0 또는 나중에 인증서를 만들고 IIS를 설치 합니다. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 이 자습서의 명령을 실행하려면 `Login-AzAccount`를 실행하여 Azure에 연결해야 합니다.

## <a name="create-a-self-signed-certificate"></a>자체 서명된 인증서 만들기

프로덕션에 사용하려면 신뢰할 수 있는 공급자가 서명한 유효한 인증서를 가져와야 합니다. 이 자습서에서는 [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate)를 사용하여 자체 서명된 인증서를 만듭니다. [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate)을 인증서에서 pfx 파일을 내보내도록 반환된 지문과 함께 사용할 수 있습니다.

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

다음과 같은 결과가 표시됩니다.

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

지문을 사용하여 pfx 파일을 만듭니다.

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>애플리케이션 게이트웨이 만들기

가상 네트워크는 사용자가 만든 리소스 간의 통신에 필요합니다. 이 예제에서는 두 개의 서브넷을 만듭니다. 하나는 애플리케이션 게이트웨이용이고, 다른 하나는 백 엔드 서버용입니다. 애플리케이션 게이트웨이를 만드는 동시에 가상 네트워크를 만들 수 있습니다.

1. [https://portal.azure.com](https://portal.azure.com)에서 Azure Portal에 로그인합니다.
2. Azure Portal의 왼쪽 위 모서리에 있는 **리소스 만들기**를 클릭합니다.
3. **네트워킹**을 선택한 다음, 추천 목록에서 **Application Gateway**를 선택합니다.
4. 애플리케이션 게이트웨이에 대해 다음 값을 입력합니다.

   - *myAppGateway* - 애플리케이션 게이트웨이의 이름
   - *myResourceGroupAG* - 새 리소스 그룹의 이름

     ![새 애플리케이션 게이트웨이 만들기](./media/create-url-route-portal/application-gateway-create.png)

5. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
6. **가상 네트워크 선택**을 클릭하고 **새로 만들기**를 클릭한 다음, 가상 네트워크에 대해 다음 값을 입력합니다.

   - *myVNet* - 가상 네트워크 이름
   - *10.0.0.0/16* - 가상 네트워크 주소 공간
   - *myAGSubnet* - 서브넷 이름
   - *10.0.1.0/24* - 서브넷 주소 공간

     ![가상 네트워크 만들기](./media/create-url-route-portal/application-gateway-vnet.png)

7. **확인**을 클릭하여 가상 네트워크 및 서브넷을 만듭니다.
8. **프런트 엔드 IP 구성**에서 **IP 주소 형식**이 **공용**으로 설정되어 있고 **새로 만들기**가 선택되어 있는지 확인합니다. 이름으로 *myAGPublicIPAddress*를 입력합니다. 다른 설정에 대한 기본값을 적용한 다음, **확인**을 클릭합니다.
9. **수신기 구성** 아래에서 **HTTPS**를 선택한 후 **파일 선택**을 선택하고 *c:\appgwcert.pfx* 파일로 이동한 다음, **열기**를 선택합니다.
10. 인증서 이름으로 *appgwcert*를 입력하고 암호로 *Azure123456!* 을 입력합니다. 암호
11. 웹 애플리케이션 방화벽을 사용하지 않도록 설정하고 **확인**을 선택합니다.
12. 요약 페이지에서 설정을 검토한 다음, **확인**을 선택하여 네트워크 리소스와 애플리케이션 게이트웨이를 만듭니다. 애플리케이션 게이트웨이가 생성되는 데 몇 분이 걸릴 수 있습니다. 배포가 완료될 때까지 기다렸다가 다음 섹션으로 이동합니다.

### <a name="add-a-subnet"></a>서브넷 추가

1. 왼쪽 메뉴에서 **모든 리소스**를 선택한 다음, 리소스 목록에서 **myVNet**을 선택합니다.
2. **서브넷**을 선택한 다음, **서브넷**을 클릭합니다.

    ![서브넷 만들기](./media/create-url-route-portal/application-gateway-subnet.png)

3. 서브넷의 이름으로 *myBackendSubnet*을 입력합니다.
4. 주소 범위로 *10.0.2.0/24*를 입력한 후 **확인**을 선택합니다.

## <a name="add-a-listener-and-redirection-rule"></a>수신기 및 리디렉션 규칙 추가

### <a name="add-the-listener"></a>수신기 추가

먼저 포트 80에 대해 수신기 *myListener*를 추가합니다.

1. **myResourceGroupAG** 리소스 그룹을 열고 **myAppGateway**를 선택합니다.
2. **수신기**를 선택하고 **+ 기본**을 선택합니다.
3. 이름으로 *MyListener*를 입력합니다.
4. 새 프런트 엔드 포트 이름으로 *httpPort*를, 포트로 *80*을 입력합니다.
5. 프로토콜이 **HTTP**로 설정되어 있는지 확인하고 **확인**을 선택합니다.

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>리디렉션 구성을 사용하여 라우팅 규칙 추가

1. **myAppGateway**에서 **규칙**을 선택한 후 **+기본**을 선택합니다.
2. **이름**으로 *Rule2*를 입력합니다.
3. 수신기에 대해 **MyListener**를 선택했는지 확인합니다.
4. **리디렉션 구성** 확인란을 선택합니다.
5. **리디렉션 유형**으로 **영구**를 선택합니다.
6. **리디렉션 대상**으로 **수신기**를 선택합니다.
7. **대상 수신기**가 **appGatewayHttpListener**로 설정되어 있는지 확인합니다.
8. **쿼리 문자열 포함** 및 **경로 포함** 확인란을 선택합니다.
9. **확인**을 선택합니다.

## <a name="create-a-virtual-machine-scale-set"></a>가상 머신 확장 집합 만들기

이 예제에서는 애플리케이션 게이트웨이에서 백 엔드 풀에 대한 서버를 제공하도록 가상 머신 확장 집합을 만듭니다.

1. 포털 왼쪽 위 모서리에서 **+리소스 만들기**를 선택합니다.
2. **컴퓨팅**을 선택합니다.
3. 검색 상자에 *확장 집합*을 입력하고 Enter 키를 누릅니다.
4. **가상 머신 확장 집합**을 선택한 후 **만들기**를 선택합니다.
5. **가상 머신 확장 집합 이름**으로 *myvmss*를 입력합니다.
6. 운영 체제 디스크 이미지에 대해** **Windows Server 2016 Datacenter**를 선택했는지 확인합니다.
7. **리소스 그룹**으로 **myResourceGroupAG**를 선택합니다.
8. **사용자 이름**으로 *azureuser*를 입력합니다.
9. **암호**로 *Azure123456!* 을 입력하고 암호를 확인합니다.
10. **인스턴스 수**의 값이 **2**인지 확인합니다.
11. **인스턴스 크기**로 **D2s_v3**를 선택합니다.
12. **네트워킹** 아래에서 **부하 분산 옵션 선택**이 **Application Gateway**로 설정되어 있는지 확인합니다.
13. **Application Gateway**가 **myAppGateway**로 설정되어 있는지 확인합니다.
14. **서브넷**이 **myBackendSubnet**으로 설정되어 있는지 확인합니다.
15. **만들기**를 선택합니다.

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>확장 집합을 해당 백 엔드 풀에 연결

가상 머신 확장 집합 포털 UI는 확장 집합에 대한 새 백 엔드 풀을 만들지만 기존 appGatewayBackendPool에 연결할 수 있습니다.

1. **myResourceGroupAg** 리소스 그룹을 엽니다.
2. **myAppGateway**를 선택합니다.
3. **백 엔드 풀**을 선택합니다.
4. **myAppGatewaymyvmss**를 선택합니다.
5. **백 엔드 풀에서 모든 대상 제거**를 선택합니다.
6. **저장**을 선택합니다.
7. 이 프로세스가 완료되면 **myAppGatewaymyvmss** 백 엔드 풀을 선택하고 **삭제**, **확인**을 차례로 선택합니다.
8. **appGatewayBackendPool**을 선택합니다.
9. **대상** 아래에서 **VMSS**를 선택합니다.
10. **VMSS** 아래에서 **myvmss**를 선택합니다.
11. **네트워크 인터페이스 구성** 아래에서 **myvmssNic**를 선택합니다.
12. **저장**을 선택합니다.

### <a name="upgrade-the-scale-set"></a>확장 집합 업그레이드

마지막으로, 이러한 변경 내용으로 확장 집합을 업그레이드해야 합니다.

1. **myvmss** 확장 집합을 선택합니다.
2. **설정**에서 **인스턴스**를 선택합니다.
3. 두 인스턴스를 선택하고 **업그레이드**를 선택합니다.
4. **예**를 선택하여 확인합니다.
5. 이 작업을 완료한 후로 **myAppGateway**로 돌아가 **백 엔드 풀**을 선택합니다. 이제 **appGatewayBackendPool**에는 두 개의 대상이 있고 **myAppGatewaymyvmss**에는 대상이 없다고 표시됩니다.
6. **myAppGatewaymyvmss**를 선택한 후 **삭제**를 선택합니다.
7. **확인**을 선택하여 확인합니다.

### <a name="install-iis"></a>IIS 설치

확장 집합에서 IIS를 설치하는 간편한 방법은 PowerShell을 사용하는 것입니다. 포털에서 Cloud Shell 아이콘을 클릭하고 **PowerShell**을 선택합니다.

다음 코드를 PowerShell 창에 붙여넣고 Enter 키를 누릅니다.

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>확장 집합 업그레이드

IIS 사용하여 인스턴스를 변경한 후에는 이 변경 내용으로 확장 집합을 한 번 더 업그레이드해야 합니다.

1. **myvmss** 확장 집합을 선택합니다.
2. **설정**에서 **인스턴스**를 선택합니다.
3. 두 인스턴스를 선택하고 **업그레이드**를 선택합니다.
4. **예**를 선택하여 확인합니다.

## <a name="test-the-application-gateway"></a>애플리케이션 게이트웨이 테스트

애플리케이션 게이트웨이 개요 페이지에서 애플리케이션 공용 IP 주소를 가져올 수 있습니다.

1. **myAppGateway**를 선택합니다.
2. **개요** 페이지에서 **프런트 엔드 공용 IP 주소** 아래의 IP 주소를 기록해 둡니다.

3. 공용 IP 주소를 복사하여 브라우저의 주소 표시줄에 붙여넣습니다. 예를 들어 http://52.170.203.149

   ![보안 경고](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. 자체 서명된 인증서를 사용하는 경우 보안 경고를 받으려면 **세부 정보**, **웹 페이지로 이동**을 차례로 선택합니다. 그러면 보안 IIS 웹 사이트가 다음 예제와 같이 표시됩니다.

   ![애플리케이션 게이트웨이의 기준 URL 테스트](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>다음 단계

[내부 리디렉션으로 애플리케이션 게이트웨이를 만드는](redirect-internal-site-powershell.md) 방법을 알아봅니다.
