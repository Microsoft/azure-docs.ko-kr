---
title: Azure PowerShell을 사용 하 여 Key Vault 인증서를 사용 하 여 SSL 종료를 구성 합니다.
description: 사용 하도록 설정 하는 HTTPS 수신기에 연결 된 서버 인증서에 대 한 Key Vault를 사용 하 여 Azure 응용 프로그램 게이트웨이 통합 하는 방법도 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 4/22/2019
ms.author: victorh
ms.openlocfilehash: 7c31801156ee321fe93d73de41fc68179835261a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60831115"
---
# <a name="configure-ssl-termination-with-key-vault-certificates-using-azure-powershell"></a>Azure PowerShell을 사용 하 여 Key Vault 인증서를 사용 하 여 SSL 종료를 구성 합니다.

[Azure Key Vault](../key-vault/key-vault-whatis.md) 플랫폼에서 관리 되는 암호 저장소가 비밀, 키 및 SSL 인증서를 보호 하는 데 사용할 수 있습니다. Application Gateway는 사용 하도록 설정 하는 HTTPS 수신기에 연결 된 서버 인증서 (공개 미리 보기)에서 Key Vault를 사용 하 여 통합을 지원 합니다. 이 지원은 Application Gateway의 v2 SKU로 제한 됩니다.

자세한 내용은 [Key Vault 인증서를 사용 하 여 SSL 종료](key-vault-certs.md)합니다.

이 문서에서는 ssl 종료에 대 한 Application Gateway를 사용 하 여 Key Vault를 통합 하도록 Azure PowerShell 스크립트를 보여 줍니다.

> [!IMPORTANT]
> 응용 프로그램 게이트웨이 Key Vault 통합은 현재 공개 미리 보기로 제공 합니다. 이 미리 보기는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure 미리 보기에 대한 보충 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

이 문서에는 Azure PowerShell 모듈 버전 1.0.0 이상. `Get-Module -ListAvailable Az`을 실행하여 버전을 찾습니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-az-ps)를 참조하세요. 이 문서에서는 명령 실행에 실행 해야 `Connect-AzAccount` Azure를 사용 하 여 연결을 만들려고 합니다.

## <a name="prerequisites"></a>필수 조건

시작 하기 전에 설치 ManagedServiceIdentity 모듈이 있어야 합니다.

```azurepowershell
Install-Module -Name Az.ManagedServiceIdentity
Connect-AzAccount
Select-AzSubscription -Subscription <your subscription>
```

## <a name="example-script"></a>예제 스크립트

### <a name="set-up-variables"></a>변수 설정

```azurepowershell
$rgname = "KeyVaultTest"
$location = "East US"
$kv = "TestKeyVaultAppGw"
$appgwName = "AppGwKVIntegration"
```

### <a name="create-a-resource-group-and-a-user-managed-identity"></a>리소스 그룹을 만들고 관리 하는 사용자 id를

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
$identity = New-AzUserAssignedIdentity -Name "appgwKeyVaultIdentity" `
  -Location $location -ResourceGroupName $rgname
```

### <a name="create-key-vault-policy-and-certificate-to-be-used-by-application-gateway"></a>Application Gateway에서 사용할 Key Vault, 정책 및 인증서 만들기

```azurepowershell
$keyVault = New-AzKeyVault -Name $kv -ResourceGroupName $rgname -Location $location -EnableSoftDelete 
Set-AzKeyVaultAccessPolicy -VaultName $kv -PermissionsToSecrets get -ObjectId $identity.PrincipalId

$policy = New-AzKeyVaultCertificatePolicy -ValidityInMonths 12 `
  -SubjectName "CN=www.contoso11.com" -IssuerName self `
  -RenewAtNumberOfDaysBeforeExpiry 30
$certificate = Add-AzKeyVaultCertificate -VaultName $kv -Name "cert1" -CertificatePolicy $policy
$certificate = Get-AzKeyVaultCertificate -VaultName $kv -Name "cert1"
$secretId = $certificate.SecretId.Replace($certificate.Version, "")
```

### <a name="create-a-vnet"></a>VNet 만들기

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-static-public-vip"></a>정적 공용 VIP 만들기

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-frontend-ports"></a>풀 및 프런트 엔드 포트 만들기

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "port2" -Port 80
```

### <a name="point-ssl-certificate-to-key-vault"></a>키 자격 증명 모음에 대 한 지점 ssl 인증서

```azurepowershell
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert1" -KeyVaultSecretId $secretId
```

### <a name="create-listeners-rules-and-autoscale"></a>수신기, 규칙 및 자동 크기 조정 만들기

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Https `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "listener2" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02
$poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
```

### <a name="assign-user-managed-identity-to-the-application-gateway"></a>Application gateway를 관리 하는 사용자 id를 할당 합니다.

```azurepowershell
$appgwIdentity = New-AzApplicationGatewayIdentity -UserAssignedIdentityId $identity.Id
```

### <a name="create-the-application-gateway"></a>Application Gateway 만들기

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -Identity $appgwIdentity -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts @($fp01, $fp02) -HttpListeners @($listener01, $listener02) `
  -RequestRoutingRules @($rule01, $rule02) -Sku $sku `
  -SslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="next-steps"></a>다음 단계

[SSL 종료에 자세히 알아보려면](ssl-overview.md)합니다.