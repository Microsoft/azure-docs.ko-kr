---
title: 'Azure Express 경로: MACsec 구성'
description: 이 문서에서는에 지 라우터와 Microsoft edge 라우터 간 연결을 보호 하도록 MACsec를 구성 하는 방법을 설명 합니다.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: cherylmc
ms.openlocfilehash: 626302845dfb4b19deb921675601818b35ab8edb
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083542"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Express 경로 직접 포트에서 MACsec 구성

이 문서는 PowerShell을 사용 하 여에 지 라우터와 Microsoft edge 라우터 간 연결을 보호 하도록 MACsec을 구성 하는 데 도움이 됩니다.

## <a name="before-you-begin"></a>시작하기 전에

구성을 시작하기 전에 다음을 확인합니다.

* Express 경로 [직접 프로 비전 워크플로](expressroute-erdirect-about.md)를 이해 합니다.
* [Express 경로 직접 포트 리소스](expressroute-howto-erdirect.md)를 만들었습니다.
* PowerShell을 로컬로 실행 하려면 최신 버전의 Azure PowerShell이 컴퓨터에 설치 되어 있는지 확인 합니다.

### <a name="working-with-azure-powershell"></a>Azure PowerShell 작업

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>로그인 하 고 올바른 구독을 선택 합니다.

구성을 시작 하려면 Azure 계정에 로그인 하 고 사용 하려는 구독을 선택 합니다.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Azure Key Vault, MACsec 암호 및 사용자 id 만들기

1. 새 리소스 그룹에 MACsec 암호를 저장할 Key Vault 인스턴스를 만듭니다.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    키 자격 증명 모음 또는 리소스 그룹이 이미 있는 경우 다시 사용할 수 있습니다. 그러나 기존 키 자격 증명 모음에서 [ **일시 삭제** 기능](../key-vault/key-vault-ovw-soft-delete.md) 을 사용 하도록 설정 하는 것이 중요 합니다. 일시 삭제를 사용 하도록 설정 하지 않은 경우 다음 명령을 사용 하 여 사용 하도록 설정할 수 있습니다.

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. 사용자 id를 만듭니다.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    AzUserAssignedIdentity이 유효한 PowerShell cmdlet으로 인식 되지 않는 경우 관리자 모드에서 다음 모듈을 설치 하 고 위의 명령을 다시 실행 합니다.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. CAK (연결 연결 키) 및 CKN (연결 연결 키 이름)를 만들고 키 자격 증명 모음에 저장 합니다.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. 사용자 id에 대한 GET 권한을 할당 합니다.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   이제이 id는 key vault에서 비밀 (예: CAK 및 CKN)을 가져올 수 있습니다.
5. 이 사용자 id를 Express 경로에서 사용할 수 있도록 설정 합니다.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Express 경로 직접 포트에서 MACsec 구성

### <a name="to-enable-macsec"></a>MACsec를 사용 하도록 설정 하려면

각 Express 경로 직접 인스턴스에는 두 개의 실제 포트가 있습니다. 두 포트에서 MACsec을 동시에 사용 하도록 선택 하거나 한 번에 한 포트에서 MACsec를 사용 하도록 선택할 수 있습니다. 다른 포트를 서비스 하는 동안 활성 포트로 트래픽을 전환 하 여 한 번에 하나의 포트를 수행 하면 Express 경로 직접 서비스가 이미 서비스에 있는 경우 중단을 최소화할 수 있습니다.

1. MACsec 암호 및 암호를 설정 하 고 사용자 id를 포트와 연결 하 여 Express 경로 관리 코드가 필요한 경우 MACsec 암호에 액세스할 수 있도록 합니다.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. 필드 포트가 관리 다운 상태에 있는 경우 다음 명령을 실행 하 여 포트를 가져올 수 있습니다.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    이 시점에서 MACsec은 Microsoft 쪽의 Express 경로 직접 포트에서 사용 하도록 설정 됩니다. Edge 장치에서 구성 하지 않은 경우 동일한 MACsec 암호 및 암호를 사용 하 여 계속 구성할 수 있습니다.

### <a name="to-disable-macsec"></a>MACsec를 사용 하지 않도록 설정 하려면

Express 경로 직접 인스턴스에서 MACsec가 더 이상 필요 하지 않은 경우 다음 명령을 실행 하 여 사용 하지 않도록 설정할 수 있습니다.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

이 시점에서 MACsec은 Microsoft 쪽의 Express 경로 직접 포트에서 사용 하지 않도록 설정 됩니다.

### <a name="test-connectivity"></a>연결 테스트
Express 경로 직접 포트에서 MACsec (MACsec 키 업데이트 포함)을 구성한 후 회로의 BGP 세션이 실행 중인지 [확인](expressroute-troubleshooting-expressroute-overview.md) 합니다. 포트에 아직 회로가 없으면 먼저 만든 후 회로의 Microsoft 피어 링 또는 Azure 개인 피어 링을 설정 하세요. Macsec 키 불일치를 포함 하 여 macsec이 잘못 구성 된 경우, 네트워크 장치와 Microsoft 네트워크 장치 간에는 계층 2의 ARP 해상도와 계층 3의 BGP 설정이 표시 되지 않습니다. 모든 항목이 올바르게 구성 된 경우 BGP 경로를 양방향으로 올바르게 보급 했 고 응용 프로그램 데이터가 Express 경로를 통해 적절 하 게 전달 됩니다.

## <a name="next-steps"></a>다음 단계
1. [Express 경로 직접에서 Express 경로 회로 만들기](expressroute-howto-erdirect.md)
2. [Azure Virtual Network에 ExpressRoute 회로 연결](expressroute-howto-linkvnet-arm.md)
3. [ExpressRoute 연결 확인](expressroute-troubleshooting-expressroute-overview.md)
