---
title: V1에서 v2로 마이그레이션-Azure 애플리케이션 게이트웨이
description: 이 문서에서는 v1에서 v2로 Azure 애플리케이션 게이트웨이 및 웹 응용 프로그램 방화벽을 마이그레이션하는 방법을 보여 줍니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/14/2019
ms.author: victorh
ms.openlocfilehash: 9909c46015fffb3bea3eef094599312e28b935c5
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/06/2020
ms.locfileid: "77046190"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>V1에서 v2로 Azure 애플리케이션 게이트웨이 및 웹 응용 프로그램 방화벽 마이그레이션

이제 [Azure 애플리케이션 게이트웨이 및 WAF (웹 응용 프로그램 방화벽) v2](application-gateway-autoscaling-zone-redundant.md) 를 사용할 수 있으며 자동 크기 조정 및 가용성 영역 중복성과 같은 추가 기능을 제공 합니다. 그러나 기존 v1 게이트웨이는 v2로 자동 업그레이드되지 않습니다. V1에서 v 2로 마이그레이션하려면이 문서의 단계를 따르세요.

마이그레이션에는 두 단계가 있습니다.

1. 구성 마이그레이션
2. 클라이언트 트래픽 마이그레이션

이 문서에서는 구성 마이그레이션을 다룹니다. 클라이언트 트래픽 마이그레이션은 특정 환경에 따라 달라 집니다. 그러나 일부 고급 일반 권장 사항이 [제공 됩니다](#migrate-client-traffic).

## <a name="migration-overview"></a>마이그레이션 개요

다음을 수행 하는 Azure PowerShell 스크립트를 사용할 수 있습니다.

* 지정 하는 가상 네트워크 서브넷에 새 Standard_v2 또는 WAF_v2 게이트웨이를 만듭니다.
* V1 Standard 또는 WAF 게이트웨이와 연결 된 구성을 새로 만든 Standard_V2 또는 WAF_V2 게이트웨이에 원활 하 게 복사 합니다.

### <a name="caveatslimitations"></a>Caveats\Limitations

* 새 v2 게이트웨이에는 새 공용 및 개인 IP 주소가 있습니다. 기존 v1 게이트웨이와 연결 된 IP 주소를 원활 하 게 v2로 이동할 수 없습니다. 그러나 새 v2 게이트웨이에 기존 (할당 되지 않은) 공용 또는 개인 IP 주소를 할당할 수 있습니다.
* 가상 네트워크 내에서 v1 게이트웨이가 있는 다른 서브넷에 대한 IP 주소 공간을 제공 해야 합니다. 이 스크립트는 이미 v1 게이트웨이가 있는 기존 서브넷에서 v2 게이트웨이를 만들 수 없습니다. 그러나 기존 서브넷에 이미 v2 게이트웨이가 있는 경우에도 계속 작동할 수 있으며, 충분 한 IP 주소 공간이 있을 수 있습니다.
* SSL 구성을 마이그레이션하려면 v1 게이트웨이에서 사용 되는 모든 SSL 인증서를 지정 해야 합니다.
* V1 게이트웨이에 대해 FIPS 모드를 사용 하도록 설정한 경우 새 v2 게이트웨이로 마이그레이션되지 않습니다. V 2에서는 FIPS 모드가 지원 되지 않습니다.
* v2는 i p v 6을 지원 하지 않으므로 IPv6 사용 v1 게이트웨이는 마이그레이션되지 않습니다. 스크립트를 실행 하는 경우 완료 되지 않을 수 있습니다.
* V1 게이트웨이에 개인 IP 주소만 있는 경우 스크립트는 새 v2 게이트웨이에 대한 공용 IP 주소 및 개인 IP 주소를 만듭니다. 현재 v2 게이트웨이는 개인 IP 주소만 지원 하지 않습니다.

## <a name="download-the-script"></a>스크립트 다운로드

[PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAppGWMigration)에서 마이그레이션 스크립트를 다운로드 합니다.

## <a name="use-the-script"></a>스크립트 사용

로컬 PowerShell 환경 설정 및 기본 설정에 따라 다음과 같은 두 가지 옵션을 사용할 수 있습니다.

* Azure Az 모듈이 설치 되어 있지 않거나 Azure Az 모듈을 제거 하지 않는 경우 가장 좋은 방법은 `Install-Script` 옵션을 사용 하 여 스크립트를 실행 하는 것입니다.
* Azure Az modules를 유지 해야 하는 경우에는 스크립트를 다운로드 하 여 직접 실행 하는 것이 가장 좋습니다.

Azure Az 모듈이 설치 되어 있는지 확인 하려면 `Get-InstalledModule -Name az`를 실행 합니다. 설치 된 Az 모듈이 표시 되지 않는 경우 `Install-Script` 메서드를 사용할 수 있습니다.

### <a name="install-using-the-install-script-method"></a>Install-Script 메서드를 사용 하 여 설치

이 옵션을 사용 하려면 컴퓨터에 Azure Az 모듈이 설치 되어 있지 않아야 합니다. 설치 된 경우 다음 명령에서 오류를 표시 합니다. Azure Az modules를 제거 하거나 다른 옵션을 사용 하 여 스크립트를 수동으로 다운로드 하 여 실행할 수 있습니다.
  
다음 명령을 사용하여 스크립트를 실행합니다.

`Install-Script -Name AzureAppGWMigration`

또한이 명령은 필요한 Az modules을 설치 합니다.  

### <a name="install-using-the-script-directly"></a>스크립트를 사용 하 여 직접 설치

일부 Azure Az 모듈이 설치 되어 있고 제거할 수 없는 경우 (또는 제거 하지 않으려는 경우) 스크립트 다운로드 링크의 **수동 다운로드** 탭을 사용 하 여 스크립트를 수동으로 다운로드할 수 있습니다. 이 스크립트는 원시 nupkg 파일로 다운로드 됩니다. 이 nupkg 파일에서 스크립트를 설치 하려면 [수동 패키지 다운로드](/powershell/scripting/gallery/how-to/working-with-packages/manual-download)를 참조 하세요.

스크립트를 실행하려면 다음과 같이 하십시오.

1. `Connect-AzAccount`를 사용 하 여 Azure에 연결 합니다.

1. `Import-Module Az`를 사용 하 여 Az 모듈을 가져옵니다.

1. `Get-Help AzureAppGWMigration.ps1`를 실행 하 여 필요한 매개 변수를 검사 합니다.

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   스크립트에 대한 매개 변수:
   * **resourceId: [String]: 필수** -기존 표준 V1 또는 waf v1 게이트웨이에 대한 AZURE 리소스 ID입니다. 이 문자열 값을 찾으려면 Azure Portal로 이동 하 고 응용 프로그램 게이트웨이 또는 WAF 리소스를 선택한 다음 게이트웨이에 대한 **속성** 링크를 클릭 합니다. 리소스 ID는 해당 페이지에 있습니다.

     다음 Azure PowerShell 명령을 실행 하 여 리소스 ID를 가져올 수도 있습니다.

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [String]: 필수** -새 v2 게이트웨이를 포함 하는 새 서브넷에 할당 했거나 할당 하려고 하는 IP 주소 공간입니다. CIDR 표기법으로 지정 해야 합니다. 예: 10.0.0.0/24. 이 서브넷을 미리 만들 필요는 없습니다. 스크립트가 존재 하지 않는 경우 만듭니다.
   * **appgwName: [String]: 선택 사항**입니다. 새 Standard_v2 또는 WAF_v2 게이트웨이의 이름으로 사용 하도록 지정 하는 문자열입니다. 이 매개 변수를 지정 하지 않으면 기존 v1 게이트웨이의 이름이 접미사 *_v2* 추가 된 상태로 사용 됩니다.
   * **Sslcertificates: [PSApplicationGatewaySslCertificate]: 선택 사항**입니다.  V1 게이트웨이의 SSL 인증서를 나타내기 위해 만드는 쉼표로 구분 된 PSApplicationGatewaySslCertificate 개체 목록은 새 v2 게이트웨이에 업로드 해야 합니다. 표준 v1 또는 WAF v1 게이트웨이에 대해 구성 된 각 SSL 인증서에 대해 여기에 표시 된 `New-AzApplicationGatewaySslCertificate` 명령을 통해 새 PSApplicationGatewaySslCertificate 개체를 만들 수 있습니다. SSL 인증서 파일의 경로와 암호를 입력 해야 합니다.

     이 매개 변수는 v1 게이트웨이 또는 WAF에 대해 구성 된 HTTPS 수신기가 없는 경우에만 선택 사항입니다. 하나 이상의 HTTPS 수신기를 설정 하는 경우이 매개 변수를 지정 해야 합니다.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     이전 예제의 `$mySslCert1, $mySslCert2` (쉼표로 구분)을 스크립트의이 매개 변수에 대한 값으로 전달할 수 있습니다.
   * 서브넷에 있는 **rootcertificate: [PSApplicationGatewayTrustedRootCertificate]: 선택 사항**입니다. V2 게이트웨이에서 백 엔드 인스턴스를 인증 하기 위해 [신뢰할 수 있는 루트 인증서](ssl-overview.md) 를 나타내기 위해 만드는 PSApplicationGatewayTrustedRootCertificate 개체의 쉼표로 구분 된 목록입니다.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      PSApplicationGatewayTrustedRootCertificate 개체 목록을 만들려면 [AzApplicationGatewayTrustedRootCertificate](https://docs.microsoft.com/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0)를 참조 하세요.
   * **privateIpAddress: [String]: 선택 사항**입니다. 새 v2 게이트웨이에 연결할 특정 개인 IP 주소입니다.  새 v2 게이트웨이에 할당 하는 것과 동일한 VNet에서 가져와야 합니다. 이를 지정 하지 않으면 스크립트에서 v2 게이트웨이에 대한 개인 IP 주소를 할당 합니다.
   * **publicIpResourceId: [String]: 선택 사항**입니다. 새 v2 게이트웨이에 할당 하려는 구독에 있는 기존 공용 IP 주소 (표준 SKU) 리소스의 resourceId입니다. 이를 지정 하지 않으면 스크립트는 동일한 리소스 그룹에 새 공용 IP를 할당 합니다. 이름은 *-IP* 가 추가 된 v2 게이트웨이의 이름입니다.
   * **validateMigration: [switch]: 선택 사항**입니다. 이 매개 변수를 사용 하 여 스크립트에서 v2 게이트웨이 만들기 및 구성 복사 후에 몇 가지 기본적인 구성 비교 유효성 검사를 수행 하도록 합니다. 기본적으로 유효성 검사는 수행 되지 않습니다.
   * **Enableautoscale 크기 조정: [스위치]: 선택 사항**입니다. 스크립트를 만든 후 새 v2 게이트웨이에서 자동 크기 조정을 사용 하도록 설정 하려면이 매개 변수를 사용 합니다. 기본적으로 자동 크기 조정을 사용 하지 않도록 설정 되어 있습니다. 새로 만든 v2 게이트웨이에서는 나중에 언제 든 지 수동으로 사용 하도록 설정할 수 있습니다.

1. 적절 한 매개 변수를 사용 하 여 스크립트를 실행 합니다. 완료 하는 데 5 ~ 7 분 정도 걸릴 수 있습니다.

    **예제**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>클라이언트 트래픽 마이그레이션

먼저 v1 게이트웨이에서 마이그레이션된 정확한 구성을 사용 하 여 스크립트가 새 v2 게이트웨이를 성공적으로 만들었는지 확인 합니다. Azure Portal에서이를 확인할 수 있습니다.

또한 v2 게이트웨이를 통해 적은 양의 트래픽을 수동 테스트로 보냅니다.
  
현재 application gateway (Standard)가 클라이언트 트래픽을 수신 하 고 각각에 대한 권장 사항을 확인할 수 있는 몇 가지 시나리오는 다음과 같습니다.

* **표준 v1 또는 WAF v1 게이트웨이와 연결 된 프런트 엔드 IP 주소 (A 레코드 사용)를 가리키는 사용자 지정 DNS 영역 (예: contoso.com)** 입니다.

    Standard_v2 application gateway와 연결 된 프런트 엔드 IP 또는 DNS 레이블을 가리키도록 DNS 레코드를 업데이트할 수 있습니다. DNS 레코드에 구성 된 TTL에 따라 모든 클라이언트 트래픽이 새 v2 게이트웨이로 마이그레이션될 때까지 시간이 걸릴 수 있습니다.
* **V1 게이트웨이와 연결 된 dns 레이블 (예: CNAME 레코드를 사용 하는 *myappgw.eastus.cloudapp.azure.com* )을 가리키는 사용자 지정 dns 영역 (예: contoso.com)** 입니다.

   다음과 같은 두 가지 선택 옵션이 있습니다.

  * 응용 프로그램 게이트웨이에서 공용 IP 주소를 사용 하는 경우 Traffic Manager 프로필을 사용 하 여 제어 된 세부적인 마이그레이션을 수행 하 여 트래픽 (가중치가 적용 된 트래픽 라우팅 방법)을 새 v2 게이트웨이로 증분 라우팅할 수 있습니다.

    [Traffic Manager 프로필](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method)에 v1 및 v2 응용 프로그램 게이트웨이의 DNS 레이블을 추가 하 고 사용자 지정 DNS 레코드 (예: `www.contoso.com`)를 Traffic Manager 도메인 (예: contoso.trafficmanager.net)에 추가 하 여이 작업을 수행할 수 있습니다.
  * 또는 새 v2 application gateway의 DNS 레이블을 가리키도록 사용자 지정 도메인 DNS 레코드를 업데이트할 수 있습니다. DNS 레코드에 구성 된 TTL에 따라 모든 클라이언트 트래픽이 새 v2 게이트웨이로 마이그레이션될 때까지 시간이 걸릴 수 있습니다.
* **클라이언트는 application gateway의 프런트 엔드 IP 주소에 연결**합니다.

   새로 만든 v2 application gateway와 연결 된 IP 주소를 사용 하도록 클라이언트를 업데이트 합니다. IP 주소를 직접 사용 하지 않는 것이 좋습니다. 사용자 지정 DNS 영역 (예: contoso.com)에 CNAME 할 수 있는 응용 프로그램 게이트웨이와 연결 된 DNS 이름 레이블 (예: yourgateway.eastus.cloudapp.azure.com)을 사용 하는 것이 좋습니다.

## <a name="common-questions"></a>일반적인 질문

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>V1에서 v 2로 구성을 마이그레이션하기 위한 Azure PowerShell 스크립트에 제한이 있나요?

예. [주의 사항/제한 사항](#caveatslimitations)을 참조 하세요.

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>이 문서와 Azure PowerShell 스크립트는 Application Gateway WAF 제품에도 적용 되나요? 

예.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>또한 Azure PowerShell 스크립트는 v1 게이트웨이의 트래픽을 새로 만든 v2 게이트웨이로 전환 하나요?

No. Azure PowerShell 스크립트는 구성만 마이그레이션합니다. 실제 트래픽 마이그레이션은 사용자의 책임 이며 컨트롤에 있습니다.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Azure PowerShell 스크립트에서 만든 새 v2 게이트웨이가 현재 내 v1 게이트웨이에서 제공 되는 모든 트래픽을 처리 하기 위해 적절 하 게 크기를 조정 하나요?

Azure PowerShell 스크립트는 기존 v1 게이트웨이의 트래픽을 처리할 수 있는 적절 한 크기의 새 v2 게이트웨이를 만듭니다. 자동 크기 조정은 기본적으로 사용 하지 않도록 설정 되어 있지만 스크립트를 실행할 때 자동 크기 조정을 사용 하도록 설정할 수 있습니다.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Azure storage에 로그를 전송 하도록 v1 gateway를 구성 했습니다. 스크립트가 v2에 대해이 구성도 복제 하나요?

No. 이 스크립트는 v2에 대해이 구성을 복제 하지 않습니다. 로그 구성은 마이그레이션된 v2 게이트웨이에 별도로 추가 해야 합니다.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>이 스크립트는 Azure KeyVault에 업로드 된 인증서를 지원 하나요?

No. 현재 스크립트는 KeyVault의 인증서를 지원 하지 않습니다. 그러나이는 이후 버전에서 고려 됩니다.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>이 스크립트를 사용 하는 경우 몇 가지 문제가 발생 했습니다. 도움을 받으려면 어떻게 해야 하나요?
  
appgwmigrationsup@microsoft.com에 전자 메일을 보내거나 Azure 지원으로 지원 사례를 열거나 둘 다 수행할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Application Gateway v2에 대해 알아보기](application-gateway-autoscaling-zone-redundant.md)
