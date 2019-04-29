---
title: Azure 프런트 도어에서 웹 응용 프로그램 방화벽에 대 한 사용자 지정 응답 구성
description: 웹 응용 프로그램 방화벽 (WAF) 요청을 차단할 때 사용자 지정 응답 코드 및 메시지를 구성 하는 방법에 알아봅니다.
services: frontdoor
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: tyao;kumud
ms.openlocfilehash: 2d16893420f27caf4f8b00dc32069e3296d7c236
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61459779"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Azure 웹 응용 프로그램 방화벽에 대 한 사용자 지정 응답을 구성 합니다.

기본적으로 Azure 웹 응용 프로그램 방화벽 (WAF) Azure 프런트 도어를 사용 하 여 일치 하는 규칙으로 인해 요청을 차단 하는 경우 반환 403 상태 코드로 **요청이 차단** 메시지입니다. 이 문서에서는 요청 WAF에서 차단 되는 경우 사용자 지정 응답 상태 코드 및 응답 메시지를 구성 하는 방법을 설명 합니다.

## <a name="set-up-your-powershell-environment"></a>PowerShell 환경 설정
Azure PowerShell은 Azure 리소스를 관리하기 위해 [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) 모델을 사용하는 cmdlet 집합을 제공합니다. 

로컬 머신에 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) 설치하고 모든 PowerShell 세션에서 사용할 수 있습니다. 페이지의 지침에 따라 Azure 자격 증명으로 로그인하고 Az PowerShell 모듈을 설치합니다.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>로그인용 대화형 대화 상자를 사용하여 Azure에 연결
```
Connect-AzAccount
Install-Module -Name Az
```
현재 버전의 PowerShellGet이 설치되어 있는지 확인합니다. 아래 명령을 실행하여 PowerShell을 다시 엽니다.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Az.FrontDoor 모듈 설치 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

Azure에서 관련 리소스를 리소스 그룹에 할당합니다. 이 예제에서는 리소스 그룹을 사용 하 여 만든 [새로 만들기-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup)합니다.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>사용자 지정 응답을 사용 하 여 새 WAF 정책 만들기 

405로 메시지를 설정 하는 사용자 지정 응답 상태 코드를 사용 하 여 WAF 정책을 새로 만드는 예제는 다음과 같습니다 **차단 됩니다.** 사용 하 여 [새로 만들기-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/new-azfrontdoorfirewallPolicy)합니다.

```azurepowershell
# WAF policy setting
New-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

사용자 지정 응답 코드 또는 응답 본문 설정을 기존 WAF 정책의 수정를 사용 하 여 [집합 AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor)합니다.

```azurepowershell
# modify WAF response code
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Set-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>다음 단계
- 자세한 내용은 [프런트 도어](front-door-overview.md)