---
title: PowerShell을 사용하여 Azure CDN 관리 | Microsoft Docs
description: 이 자습서에서는 PowerShell을 사용 하 여 Azure Content Delivery Network 끝점 프로필 및 끝점의 측면을 관리 하는 방법을 알아봅니다.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: fb6f57a5-6e26-4847-8fd9-b51fb05a79eb
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/20/2019
ms.author: allensu
ms.openlocfilehash: b5fa1cedf4cc5e87d11dc4b5c453d9cb2a307c3c
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192651"
---
# <a name="manage-azure-cdn-with-powershell"></a>PowerShell을 사용하여 Azure CDN 관리
PowerShell은 Azure CDN 프로필 및 엔드포인트를 관리하는 매우 유연한 방법 중 하나를 제공합니다.  PowerShell을 대화형으로 또는 관리 작업을 자동화하는 스크립트를 작성하여 사용할 수 있습니다.  이 자습서에서는 PowerShell을 사용하여 Azure CDN 프로필 및 엔드포인트의 관리를 달성할 수 있는 가장 일반적인 작업 몇 가지를 보여줍니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 사용하여 Azure CDN 프로필 및 엔드포인트를 관리하려면 Azure PowerShell 모듈이 설치되어 있어야 합니다.  Azure PowerShell을 설치하고 `Connect-AzAccount` cmdlet을 사용하여 Azure에 연결하는 방법을 알아보려면 [Azure PowerShell 설치 및 구성 방법](/powershell/azure/)을 참조하세요.

> [!IMPORTANT]
> `Connect-AzAccount`에 로그인해야 Azure PowerShell cmdlet을 실행할 수 있습니다.
> 
> 

## <a name="listing-the-azure-cdn-cmdlets"></a>Azure CDN cmdlet 목록화
`Get-Command` cmdlet을 사용하여 모든 Azure CDN cmdlet을 나열할 수 있습니다.

```text
PS C:\> Get-Command -Module Az.Cdn

CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Confirm-AzCdnEndpointProbeURL                      1.4.0      Az.Cdn
Cmdlet          Disable-AzCdnCustomDomain                          1.4.0      Az.Cdn
Cmdlet          Disable-AzCdnCustomDomainHttps                     1.4.0      Az.Cdn
Cmdlet          Enable-AzCdnCustomDomain                           1.4.0      Az.Cdn
Cmdlet          Enable-AzCdnCustomDomainHttps                      1.4.0      Az.Cdn
Cmdlet          Get-AzCdnCustomDomain                              1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEdgeNode                                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointNameAvailability                  1.4.0      Az.Cdn
Cmdlet          Get-AzCdnEndpointResourceUsage                     1.4.0      Az.Cdn
Cmdlet          Get-AzCdnOrigin                                    1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileResourceUsage                      1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSsoUrl                             1.4.0      Az.Cdn
Cmdlet          Get-AzCdnProfileSupportedOptimizationType          1.4.0      Az.Cdn
Cmdlet          Get-AzCdnSubscriptionResourceUsage                 1.4.0      Az.Cdn
Cmdlet          New-AzCdnCustomDomain                              1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryPolicy                            1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRule                              1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRuleAction                        1.4.0      Az.Cdn
Cmdlet          New-AzCdnDeliveryRuleCondition                     1.4.0      Az.Cdn
Cmdlet          New-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          New-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Publish-AzCdnEndpointContent                       1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnCustomDomain                           1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnEndpoint                               1.4.0      Az.Cdn
Cmdlet          Remove-AzCdnProfile                                1.4.0      Az.Cdn
Cmdlet          Set-AzCdnEndpoint                                  1.4.0      Az.Cdn
Cmdlet          Set-AzCdnOrigin                                    1.4.0      Az.Cdn
Cmdlet          Set-AzCdnProfile                                   1.4.0      Az.Cdn
Cmdlet          Start-AzCdnEndpoint                                1.4.0      Az.Cdn
Cmdlet          Stop-AzCdnEndpoint                                 1.4.0      Az.Cdn
Cmdlet          Test-AzCdnCustomDomain                             1.4.0      Az.Cdn
Cmdlet          Unpublish-AzCdnEndpointContent                     1.4.0      Az.Cdn
```

## <a name="getting-help"></a>도움말 보기
`Get-Help` cmdlet을 사용하여 이러한 cmdlet에 대한 도움말을 볼 수 있습니다.  `Get-Help` 는 사용량 및 구문을 제공하며 선택적으로 예제를 표시합니다.

```text
PS C:\> Get-Help Get-AzCdnProfile

NAME
    Get-AzCdnProfile

SYNOPSIS
    Gets an Azure CDN profile.


SYNTAX
    Get-AzCdnProfile [-ProfileName <String>] [-ResourceGroupName <String>] [-InformationAction
    <ActionPreference>] [-InformationVariable <String>] [<CommonParameters>]


DESCRIPTION
    Gets an Azure CDN profile and all related information.


RELATED LINKS

REMARKS
    To see the examples, type: "get-help Get-AzCdnProfile -examples".
    For more information, type: "get-help Get-AzCdnProfile -detailed".
    For technical information, type: "get-help Get-AzCdnProfile -full".

```

## <a name="listing-existing-azure-cdn-profiles"></a>기존 Azure CDN 프로필 목록화
매개 변수 없는 `Get-AzCdnProfile` cmdlet은 모든 기존 CDN 프로필을 검색합니다.

```powershell
Get-AzCdnProfile
```

이 출력은 열거형에 대한 cmdlet으로 파이프될 수 있습니다.

```powershell
# Output the name of all profiles on this subscription.
Get-AzCdnProfile | ForEach-Object { Write-Host $_.Name }

# Return only **Azure CDN from Verizon** profiles.
Get-AzCdnProfile | Where-Object { $_.Sku.Name -eq "Standard_Verizon" }
```

또한 프로필 이름 및 리소스 그룹을 지정하여 단일 프로필을 반환할 수 있습니다.

```powershell
Get-AzCdnProfile -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
```

> [!TIP]
> 다른 리소스 그룹에 속하기만 하다면 같은 이름을 가진 여러 CDN 프로필이 있을 수 있습니다.  `ResourceGroupName` 매개 변수를 생략함년 일치하는 이름을 가진 모든 프로필이 반환됩니다.
> 
> 

## <a name="listing-existing-cdn-endpoints"></a>기존 CDN 엔드포인트 목록화
`Get-AzCdnEndpoint` 은 개별 엔드포인트 또는 프로필의 모든 엔드포인트를 검색할 수 있습니다.  

```powershell
# Get a single endpoint.
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Get all of the endpoints on a given profile. 
Get-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG

# Return all of the endpoints on all of the profiles.
Get-AzCdnProfile | Get-AzCdnEndpoint

# Return all of the endpoints in this subscription that are currently running.
Get-AzCdnProfile | Get-AzCdnEndpoint | Where-Object { $_.ResourceState -eq "Running" }
```

## <a name="creating-cdn-profiles-and-endpoints"></a>CDN 프로필 및 엔드포인트 만들기
`New-AzCdnProfile` 및 `New-AzCdnEndpoint`는 CDN 프로필 및 엔드포인트를 만드는 데 사용됩니다. 다음 SKU가 지원됩니다.
- Standard_Verizon
- Premium_Verizon
- Custom_Verizon
- Standard_Akamai
- Standard_Microsoft
- Standard_ChinaCdn

```powershell
# Create a new profile
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US"

# Create a new endpoint
New-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Location "Central US" -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

# Create a new profile and endpoint (same as above) in one line
New-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -Sku Standard_Akamai -Location "Central US" | New-AzCdnEndpoint -EndpointName cdnposhdoc -OriginName "Contoso" -OriginHostName "www.contoso.com"

```

## <a name="checking-endpoint-name-availability"></a>엔드포인트 이름 가용성 확인
`Get-AzCdnEndpointNameAvailability` 은 엔드포인트 이름을 사용할 수 있는지 여부를 나타내는 개체를 반환합니다.

```powershell
# Retrieve availability
$availability = Get-AzCdnEndpointNameAvailability -EndpointName "cdnposhdoc"

# If available, write a message to the console.
If($availability.NameAvailable) { Write-Host "Yes, that endpoint name is available." }
Else { Write-Host "No, that endpoint name is not available." }
```

## <a name="adding-a-custom-domain"></a>사용자 지정 도메인 추가
`New-AzCdnCustomDomain` 은 기존 엔드포인트에 사용자 지정 도메인 이름을 추가합니다.

> [!IMPORTANT]
> [사용자 지정 도메인을 Content Delivery Network(CDN) 엔드포인트에 매핑하는 방법](cdn-map-content-to-custom-domain.md)에서 설명한 대로 DNS 공급자를 통해 CNAME을 설정해야 합니다.  `Test-AzCdnCustomDomain`를 사용하여 엔드포인트를 수정하기 전에 매핑을 시험해야 합니다.
> 
> 

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Check the mapping
$result = Test-AzCdnCustomDomain -CdnEndpoint $endpoint -CustomDomainHostName "cdn.contoso.com"

# Create the custom domain on the endpoint
If($result.CustomDomainValidated){ New-AzCdnCustomDomain -CustomDomainName Contoso -HostName "cdn.contoso.com" -CdnEndpoint $endpoint }
```

## <a name="modifying-an-endpoint"></a>엔드포인트 수정
`Set-AzCdnEndpoint` 은 기존 엔드포인트를 수정합니다.

```powershell
# Get an existing endpoint
$endpoint = Get-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Set up content compression
$endpoint.IsCompressionEnabled = $true
$endpoint.ContentTypesToCompress = "text/javascript","text/css","application/json"

# Save the changed endpoint and apply the changes
Set-AzCdnEndpoint -CdnEndpoint $endpoint
```

## <a name="purgingpre-loading-cdn-assets"></a>CDN 자산 제거/사전 로드
`Unpublish-AzCdnEndpointContent`은 캐시된 자산을 제거하며, `Publish-AzCdnEndpointContent`는 자산을 지원되는 엔드포인트에 사전 로드합니다.

```powershell
# Purge some assets.
Unpublish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -PurgeContent "/images/kitten.png","/video/rickroll.mp4"

# Pre-load some assets.
Publish-AzCdnEndpointContent -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo -LoadContent "/images/kitten.png","/video/rickroll.mp4"

# Purge everything in /images/ on all endpoints.
Get-AzCdnProfile | Get-AzCdnEndpoint | Unpublish-AzCdnEndpointContent -PurgeContent "/images/*"
```

## <a name="startingstopping-cdn-endpoints"></a>CDN 엔드포인트 시작/중지
`Start-AzCdnEndpoint` 및 `Stop-AzCdnEndpoint`를 사용하여 개별 엔드포인트 또는 엔드포인트 그룹을 시작 및 중지할 수 있습니다.

```powershell
# Stop the cdndocdemo endpoint
Stop-AzCdnEndpoint -ProfileName CdnDemo -ResourceGroupName CdnDemoRG -EndpointName cdndocdemo

# Stop all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Stop-AzCdnEndpoint

# Start all endpoints
Get-AzCdnProfile | Get-AzCdnEndpoint | Start-AzCdnEndpoint
```

## <a name="creating-standard-rules-engine-policy-and-applying-to-an-existing-cdn-endpoint"></a>표준 규칙 엔진 정책 만들기 및 기존 CDN 끝점에 적용
`New-AzCdnDeliveryRule`, `New=AzCdnDeliveryRuleCondition` 및를 `New-AzCdnDeliveryRuleAction` 사용 하 여 Microsoft 프로필에서 Azure CDN의 Azure CDN 표준 규칙 엔진을 구성할 수 있습니다. 

```powershell
# Create a new http to https redirect rule
$Condition=New-AzCdnDeliveryRuleCondition -MatchVariable RequestProtocol -Operator Equal -MatchValue HTTP
$Action=New-AzCdnDeliveryRuleAction -RedirectType Found -DestinationProtocol HTTPS
$HttpToHttpsRedirectRule=New-AzCdnDeliveryRule -Name "HttpToHttpsRedirectRule" -Order 2 -Condition $Condition -Action $Action

# Create a path based Response header modification rule. 
$Cond1=New-AzCdnDeliveryRuleCondition -MatchVariable UrlPath -Operator BeginsWith -MatchValue "/images/"
$Action1=New-AzCdnDeliveryRuleAction -HeaderActionType ModifyResponseHeader -Action Overwrite -HeaderName "Access-Control-Allow-Origin" -Value "*"
$PathBasedCacheOverrideRule=New-AzCdnDeliveryRule -Name "PathBasedCacheOverride" -Order 1 -Condition $Cond1 -Action $action1

# Create a delivery policy with above deliveryRules.
$Policy = New-AzCdnDeliveryPolicy -Description "DeliveryPolicy" -Rule $HttpToHttpsRedirectRule,$UrlRewriteRule

# Update existing endpoint with created delivery policy
$ep = Get-AzCdnEndpoint -EndpointName cdndocdemo -ProfileName CdnDemo -ResourceGroupName CdnDemoRG
$ep.DeliveryPolicy = $Policy
Set-AzCdnEndpoint -CdnEndpoint $ep
```

## <a name="deleting-cdn-resources"></a>CDN 리소스 삭제
`Remove-AzCdnProfile` 및 `Remove-AzCdnEndpoint`를 사용하여 프로필 및 엔드포인트를 제거할 수 있습니다.

```powershell
# Remove a single endpoint
Remove-AzCdnEndpoint -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG -EndpointName cdnposhdoc

# Remove all the endpoints on a profile and skip confirmation (-Force)
Get-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG | Get-AzCdnEndpoint | Remove-AzCdnEndpoint -Force

# Remove a single profile
Remove-AzCdnProfile -ProfileName CdnPoshDemo -ResourceGroupName CdnDemoRG
```

## <a name="next-steps"></a>다음 단계
[.NET](cdn-app-dev-net.md) 또는 [Node.js](cdn-app-dev-node.md)를 사용하여 Azure CDN을 자동화하는 방법을 알아봅니다.

CDN 기능에 대해 알아보려면 [CDN 개요](cdn-overview.md)를 참조하세요.

