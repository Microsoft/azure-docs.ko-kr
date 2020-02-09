---
title: PowerShell을 사용 하 여 사용자 지정 프로브 만들기-Azure 애플리케이션 게이트웨이
description: 클래식 배포 모델에서 PowerShell을 사용하여 Application Gateway에 대한 사용자 지정 프로브를 만드는 방법에 대해 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: e01a1cad98ded9d7ce8683b6adf38b5d53959774
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "75966808"
---
# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>PowerShell을 사용하여 Azure Application Gateway(클래식)에 대한 사용자 지정 프로브 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 클래식 PowerShell](application-gateway-create-probe-classic-ps.md)

이 문서에서는 PowerShell을 사용하여 기존 애플리케이션 게이트웨이에 사용자 지정 프로브를 추가합니다. 사용자 지정 프로브는 특정 상태 확인 페이지를 사용하는 애플리케이션이나 기본 웹 애플리케이션에서 성공적으로 응답을 제공하지 않는 애플리케이션에 유용합니다.

> [!IMPORTANT]
> Azure에는 리소스를 만들고 작업하기 위한 [리소스 관리자 및 클래식](../azure-resource-manager/management/deployment-models.md)이라는 두 가지 배포 모델이 있습니다. 이 문서에서는 클래식 배포 모델 사용에 대해 설명합니다. 새로운 배포는 대부분 리소스 관리자 모델을 사용하는 것이 좋습니다. [Resource Manager 모델을 사용하여 이러한 단계를 수행하는](application-gateway-create-probe-ps.md) 방법을 알아봅니다.

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Application Gateway 만들기

애플리케이션 게이트웨이를 만들려면:

1. 애플리케이션 게이트웨이 리소스를 만듭니다.
2. 구성 XML 파일 또는 구성 개체를 만듭니다.
3. 구성을 새로 만든 애플리케이션 게이트웨이 리소스에 커밋합니다.

### <a name="create-an-application-gateway-resource-with-a-custom-probe"></a>사용자 지정 프로브를 사용하여 애플리케이션 게이트웨이 리소스 만들기

게이트웨이를 생성하려면 `New-AzureApplicationGateway` cmdlet을 사용하여 해당 값을 원하는 값으로 바꿉니다. 게이트웨이에 대한 청구는 이 시점에서 시작되지 않습니다. 게이트웨이가 성공적으로 작동되면, 요금청구가 시작됩니다.

다음 예제에서는 "testvnet1"이라는 가상 네트워크 및 "subnet-1"이라는 서브넷을 사용하여 애플리케이션 게이트웨이를 만듭니다.

```powershell
New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")
```

생성된 게이트웨이의 유효성을 검사하려면 `Get-AzureApplicationGateway` cmdlet을 사용합니다.

```powershell
Get-AzureApplicationGateway AppGwTest
```

> [!NOTE]
> *InstanceCount* 의 기본값은 2이고, 최대값은 10입니다. *GatewaySize* 에 대한 기본값은 보통입니다. 작게, 보통 및 크게를 선택할 수 있습니다.
> 
> 

게이트웨이가 아직 시작되지 않았으므로 *VirtualIPs* 및 *DnsName*이 빈 값으로 표시됩니다. 이 값들은 게이트웨이가 실행 상태가 되면 생성됩니다.

### <a name="configure-an-application-gateway-by-using-xml"></a>XML을 사용하여 애플리케이션 게이트웨이 구성

다음 예제에서는 XML 파일을 사용하여 모든 애플리케이션 게이트웨이 설정을 구성하고 애플리케이션 게이트웨이 리소스에 커밋합니다.  

다음 텍스트를 메모장에 복사합니다.

```xml
<ApplicationGatewayConfiguration xmlns:i="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
<FrontendIPConfigurations>
    <FrontendIPConfiguration>
        <Name>fip1</Name>
        <Type>Private</Type>
    </FrontendIPConfiguration>
</FrontendIPConfigurations>
<FrontendPorts>
    <FrontendPort>
        <Name>port1</Name>
        <Port>80</Port>
    </FrontendPort>
</FrontendPorts>
<Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
    </Probes>
    <BackendAddressPools>
    <BackendAddressPool>
        <Name>pool1</Name>
        <IPAddresses>
            <IPAddress>1.1.1.1</IPAddress>
            <IPAddress>2.2.2.2</IPAddress>
        </IPAddresses>
    </BackendAddressPool>
</BackendAddressPools>
<BackendHttpSettingsList>
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
</BackendHttpSettingsList>
<HttpListeners>
    <HttpListener>
        <Name>listener1</Name>
        <FrontendIP>fip1</FrontendIP>
    <FrontendPort>port1</FrontendPort>
        <Protocol>Http</Protocol>
    </HttpListener>
</HttpListeners>
<HttpLoadBalancingRules>
    <HttpLoadBalancingRule>
        <Name>lbrule1</Name>
        <Type>basic</Type>
        <BackendHttpSettings>setting1</BackendHttpSettings>
        <Listener>listener1</Listener>
        <BackendAddressPool>pool1</BackendAddressPool>
    </HttpLoadBalancingRule>
</HttpLoadBalancingRules>
</ApplicationGatewayConfiguration>
```

구성 항목에 대한 괄호 사이의 값을 편집합니다. 확장명이 .xml인 파일을 저장합니다.

다음 예제에서는 구성 파일을 사용하여 공용 포트 80에서 HTTP 트래픽의 부하를 분산하는 애플리케이션 게이트웨이를 설정하고 사용자 지정 프로브를 사용하여 두 IP 주소 사이의 백 엔드 포트 80으로 네트워크 트래픽을 전송하는 방법을 보여 줍니다.

> [!IMPORTANT]
> Http 또는 Https 프로토콜 항목은 대 소문자를 구분합니다.

새 구성 항목 \<Probe\>를 추가하여 사용자 지정 프로브를 구성합니다.

구성 매개 변수:

|매개 변수|Description|
|---|---|
|**이름** |사용자 지정 프로브에 대한 참조 이름입니다. |
| **프로토콜** | 사용되는 프로토콜입니다(가능한 값: HTTP 또는 HTTPS).|
| **Host** 및 **Path** | 애플리케이션 게이트웨이에서 인스턴스 상태를 확인하기 위해 호출하는 완전한 URL 경로입니다. 예를 들어 http:\//contoso.com/웹 사이트가 있는 경우 프로브 검사에 대한 사용자 지정 프로브를 "http:\//contoso.com/path/custompath.htm"로 구성 하 여 성공적인 HTTP 응답을 받을 수 있습니다.|
| **간격** | 프로브 간격 확인을 구성합니다(단위: 초).|
| **Timeout** | HTTP 응답 확인에 대한 프로브 시간 제한을 정의합니다.|
| **UnhealthyThreshold** | 백 엔드 인스턴스를 *unhealthy*(비정상) 플래그로 지정하는 데 필요한 실패한 HTTP 응답 수입니다.|

프로브 이름은 사용자 지정 프로브 설정에 사용할 백 엔드 풀을 할당하는 \<BackendHttpSettings\> 구성에서 참조합니다.

## <a name="add-a-custom-probe-to-an-existing-application-gateway"></a>기존 애플리케이션 게이트웨이에 사용자 지정 프로브 추가

현재 애플리케이션 게이트웨이 구성 변경에 필요한 세 단계는 현재 XML 구성 파일 가져오기, 사용자 지정 프로브 수정 및 새 XML 설정으로 애플리케이션 게이트웨이 구성입니다.

1. `Get-AzureApplicationGatewayConfig`을 사용하여 XML 파일을 가져옵니다. 이 cmdlet은 프로브 설정을 추가하기 위해 수정할 XML 구성을 내보냅니다.

   ```powershell
   Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"
   ```

1. 텍스트 편집기에서 XML 파일을 엽니다. `<frontendport>` 뒤에 `<probe>` 섹션을 추가합니다.

   ```xml
   <Probes>
    <Probe>
        <Name>Probe01</Name>
        <Protocol>Http</Protocol>
        <Host>contoso.com</Host>
        <Path>/path/custompath.htm</Path>
        <Interval>15</Interval>
        <Timeout>15</Timeout>
        <UnhealthyThreshold>5</UnhealthyThreshold>
    </Probe>
   </Probes>
   ```

   XML의 backendHttpSettings 섹션에서 다음 예제에 표시된 대로 프로브 이름을 추가합니다.

   ```xml
    <BackendHttpSettings>
        <Name>setting1</Name>
        <Port>80</Port>
        <Protocol>Http</Protocol>
        <CookieBasedAffinity>Enabled</CookieBasedAffinity>
        <RequestTimeout>120</RequestTimeout>
        <Probe>Probe01</Probe>
    </BackendHttpSettings>
   ```

   XML 파일을 저장합니다.

1. `Set-AzureApplicationGatewayConfig`을 사용하여 새 XML 파일로 애플리케이션 게이트웨이 구성을 업데이트합니다. 이 cmdlet은 애플리케이션 게이트웨이를 새 구성으로 업데이트합니다.

```powershell
Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"
```

## <a name="next-steps"></a>다음 단계

SSL(Secure Sockets Layer) 오프로드를 구성하려는 경우 [SSL 오프로드에 대해 애플리케이션 게이트웨이 구성](application-gateway-ssl.md)을 참조하세요.

내부 부하 분산 장치에서 사용되도록 애플리케이션 게이트웨이를 구성하려면 [ILB(내부 부하 분산 장치)를 사용하여 애플리케이션 게이트웨이 만들기](application-gateway-ilb.md)를 참조하세요.

