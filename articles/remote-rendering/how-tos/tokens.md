---
title: 서비스 액세스 토큰 가져오기
description: ARR REST Api에 액세스 하기 위한 토큰을 만드는 방법을 설명 합니다.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e8e2f3f9dd49693faa26eaaab309fcad58f6f9f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076160"
---
# <a name="get-service-access-tokens"></a>서비스 액세스 토큰 가져오기

ARR REST Api에 대 한 액세스는 권한 있는 사용자 에게만 부여 됩니다. 권한 부여를 증명 하려면 REST 요청과 함께 *액세스 토큰* 을 보내야 합니다. 이러한 토큰은 계정 키에 대해 exchange의 STS ( *보안 토큰 서비스* )에서 발급 됩니다. 토큰 **의 수명은 24 시간** 이므로 서비스에 대 한 모든 권한을 부여 하지 않고도 사용자에 게 발급 될 수 있습니다.

이 문서에서는 이러한 액세스 토큰을 만드는 방법을 설명 합니다.

## <a name="prerequisites"></a>필수 조건

아직 없는 경우 [ARR 계정을 만듭니다](create-an-account.md).

## <a name="token-service-rest-api"></a>토큰 서비스 REST API

액세스 토큰을 만들기 위해 *보안 토큰 서비스* 는 단일 REST API를 제공 합니다. ARR STS 서비스의 URL은 https: \/ /sts.mixedreality.azure.com입니다.

### <a name="get-token-request"></a>' 토큰 가져오기 ' 요청

| URI | 메서드 |
|-----------|:-----------|
| /accounts/**accountId**/토큰 | GET |

| 헤더 | 값 |
|--------|:------|
| 권한 부여 | "전달자 **accountId**:**accountKey**" |

*AccountId* 및 *accountKey* 을 해당 데이터로 바꿉니다.

### <a name="get-token-response"></a>' Get token ' 응답

| 상태 코드 | JSON 페이로드 | 의견 |
|-----------|:-----------|:-----------|
| 200 | AccessToken: 문자열 | 성공 |

| header | 목적 |
|--------|:------|
| MS-CV | 이 값은 서비스 내에서 호출을 추적 하는 데 사용할 수 있습니다. |

## <a name="getting-a-token-using-powershell"></a>PowerShell을 사용 하 여 토큰 가져오기

아래의 PowerShell 코드는 필요한 REST 요청을 STS로 보내는 방법을 보여 줍니다. 그런 다음 토큰을 PowerShell 프롬프트에 출력 합니다.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

스크립트는 토큰을 복사 & 붙여 넣을 수 있는 위치에서 출력에 출력 합니다. 실제 프로젝트의 경우이 프로세스를 자동화 해야 합니다.

## <a name="next-steps"></a>다음 단계

* [예제 PowerShell 스크립트](../samples/powershell-example-scripts.md)
* [Azure 프런트 엔드 Api](../how-tos/frontend-apis.md)
* [세션 관리 REST API](../how-tos/session-rest-api.md)
