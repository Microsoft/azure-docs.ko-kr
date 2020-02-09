---
title: 일반적인 오류 문제 해결
description: Azure 리소스 그래프를 사용 하 여 Azure 리소스를 쿼리 하는 동안 다양 한 Sdk 관련 문제를 해결 하는 방법을 알아봅니다.
ms.date: 10/18/2019
ms.topic: troubleshooting
ms.openlocfilehash: f881db4f75bcee8c13221717596442ac29a4b1ac
ms.sourcegitcommit: 8a2949267c913b0e332ff8675bcdfc049029b64b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2019
ms.locfileid: "74303902"
---
# <a name="troubleshoot-errors-using-azure-resource-graph"></a>Azure 리소스 그래프를 사용 하 여 오류 문제 해결

Azure 리소스 그래프를 사용 하 여 Azure 리소스를 쿼리할 때 오류가 발생할 수 있습니다. 이 문서에서는 발생할 수 있는 다양한 오류와 이러한 오류를 해결하는 방법에 대해 설명합니다.

## <a name="finding-error-details"></a>오류 세부 정보 찾기

대부분의 오류는 Azure 리소스 그래프를 사용 하 여 쿼리를 실행 하는 동안 문제가 발생 한 것입니다. 쿼리가 실패 하면 SDK에서 실패 한 쿼리에 대 한 세부 정보를 제공 합니다. 이 정보는 문제를 해결할 수 있도록 문제를 나타내며 이후 쿼리는 성공 합니다.

## <a name="general-errors"></a>일반 오류

### <a name="toomanysubscription"></a>시나리오: 구독이 너무 많음

#### <a name="issue"></a>문제

[Azure Lighthouse](../../../lighthouse/overview.md)를 사용 하 여 교차 테넌트 구독을 포함 하 여 1000 개 이상의 구독에 액세스할 수 있는 고객은 Azure 리소스 그래프에 대 한 단일 호출로 모든 구독에서 데이터를 가져올 수 없습니다.

#### <a name="cause"></a>원인

Azure CLI 및 PowerShell은 Azure 리소스 그래프에 처음 1000 구독만 전달 합니다. Azure 리소스 그래프의 REST API는 쿼리를 수행 하는 최대 구독 수를 허용 합니다.

#### <a name="resolution"></a>해결 방법

구독 하위 집합을 포함 하는 쿼리에 대 한 일괄 처리 요청은 1000 구독 제한 미만으로 유지 됩니다. 솔루션은 PowerShell에서 **Subscription** 매개 변수를 사용 합니다.

```azurepowershell-interactive
# Replace this query with your own
$query = 'Resources | project type'

# Fetch the full array of subscription IDs
$subscriptions = Get-AzSubscription
$subscriptionIds = $subscriptions.Id

# Create a counter, set the batch size, and prepare a variable for the results
$counter = [PSCustomObject] @{ Value = 0 }
$batchSize = 1000
$response = @()

# Group the subscriptions into batches
$subscriptionsBatch = $subscriptionIds | Group -Property { [math]::Floor($counter.Value++ / $batchSize) }

# Run the query for each batch
foreach ($batch in $subscriptionsBatch){ $response += Search-AzGraph -Query $query -Subscription $batch.Group }

# View the completed results of the query on all subscriptions
$response
```

### <a name="rest-contenttype"></a>시나리오: 지원 되지 않는 콘텐츠 형식 REST 헤더

#### <a name="issue"></a>문제

Azure 리소스 그래프를 쿼리 하는 고객은 _500_ (내부 서버 오류) 응답 REST API 가져옵니다.

#### <a name="cause"></a>원인

Azure 리소스 그래프 REST API는 **application/json**`Content-Type` 지원 합니다. 일부 REST 도구 또는 에이전트는 REST API에서 지원 하지 않는 **텍스트/일반**으로 기본 됩니다.

#### <a name="resolution"></a>해결 방법

Azure 리소스 그래프를 쿼리 하는 데 사용 하는 도구 또는 에이전트에 **application/json**에 대해 구성 `Content-Type` REST API 헤더가 있는지 확인 합니다.

### <a name="rest-403"></a>시나리오: 목록의 모든 구독에 대 한 읽기 권한이 없습니다.

#### <a name="issue"></a>문제

Azure 리소스 그래프 쿼리를 사용 하 여 구독 목록을 명시적으로 전달 하는 고객은 _403_ (사용할 수 없음) 응답을 받게 됩니다.

#### <a name="cause"></a>원인

고객에 게 제공 된 모든 구독에 대 한 읽기 권한이 없는 경우 해당 요청은 적절 한 보안 권한이 없기 때문에 거부 됩니다.

#### <a name="resolution"></a>해결 방법

구독 목록에서 쿼리를 실행 하는 고객에 게 적어도 읽기 액세스 권한이 있는 구독을 하나 이상 포함 합니다. 자세한 내용은 [Azure 리소스 그래프의 권한](../overview.md#permissions-in-azure-resource-graph)을 참조 하세요.

## <a name="next-steps"></a>다음 단계

문제가 표시되지 않거나 문제를 해결할 수 없는 경우 다음 채널 중 하나를 방문하여 추가 지원을 받으세요.

- Azure [포럼](https://azure.microsoft.com/support/forums/)을 통해 azure 전문가 로부터 답변을 받으세요.
- [@AzureSupport](https://twitter.com/azuresupport)를 사용하여 연결 – Azure 커뮤니티를 적절한 리소스(답변, 지원 및 전문가)에 연결하여 고객 환경을 개선하는 공식 Microsoft Azure 계정입니다.
- 추가 지원이 필요한 경우, Azure 기술 지원 인시던트를 제출할 수 있습니다. [Azure 지원 사이트](https://azure.microsoft.com/support/options/) 로 가서 **지원 받기**를 선택합니다.