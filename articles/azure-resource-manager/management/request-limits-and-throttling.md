---
title: 요청 제한 및 제한
description: 구독 한도에 도달할 때 Azure Resource Manager 요청에 제한을 사용하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 10/26/2019
ms.custom: seodec18
ms.openlocfilehash: 129ca3ba32d48345bde931c6bd2084c3da79be39
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/04/2020
ms.locfileid: "75659375"
---
# <a name="throttling-resource-manager-requests"></a>Resource Manager 요청 제한

이 문서에서는 제한 요청을 Azure Resource Manager 하는 방법을 설명 합니다. 이 예에서는 한도에 도달 하기 전에 남아 있는 요청 수를 추적 하는 방법과 한도에 도달 했을 때 응답 하는 방법을 보여 줍니다.

제한은 두 수준에서 발생 합니다. 구독 및 테넌트에 대 한 제한 요청을 Azure Resource Manager 합니다. 요청이 구독 및 테넌트의 제한 한도에 해당 하는 경우 리소스 관리자는 리소스 공급자에 게 요청을 라우팅합니다. 리소스 공급자는 해당 작업에 맞게 조정 된 제한 제한을 적용 합니다. 다음 이미지는 요청이 사용자 Azure Resource Manager 및 리소스 공급자로 이동 될 때 제한이 적용 되는 방법을 보여 줍니다.

![요청 제한](./media/request-limits-and-throttling/request-throttling.svg)

## <a name="subscription-and-tenant-limits"></a>구독 및 테넌트 제한

모든 구독 수준 및 테넌트 수준 작업에는 제한 한도가 적용 됩니다. 구독 요청은 구독에서 리소스 그룹을 검색 하는 것과 같은 구독 ID를 전달 하는 것입니다. 유효한 Azure 위치 검색 등의 테넌트 요청에는 구독 ID가 포함되지 않습니다.

시간당 기본 제한 제한은 다음 표에 나와 있습니다.

| 범위 | 운영 | 제한 |
| ----- | ---------- | ------- |
| Subscription | reads | 12000 |
| Subscription | deletes | 15000 |
| Subscription | writes | 1200 |
| 테넌트 | reads | 12000 |
| 테넌트 | writes | 1200 |

이러한 제한의 범위는 요청을 하는 보안 주체(사용자 또는 애플리케이션) 및 구독 ID나 테넌트 ID의 범위로 설정됩니다. 둘 이상의 보안 주체가 요청을 하는 경우 구독 또는 테넌트 전체에 적용되는 제한이 시간당 12,000개/1,200개보다 커집니다.

이러한 한도는 각 Azure Resource Manager 인스턴스에 적용됩니다. 모든 Azure 지역에 여러 인스턴스가 있으며 Azure Resource Manager가 모든 Azure 지역에 배포됩니다.  따라서 실제로 제한은 이러한 한도 보다 높습니다. 사용자의 요청은 일반적으로 Azure Resource Manager의 서로 다른 인스턴스에 의해 처리 됩니다.

## <a name="resource-provider-limits"></a>리소스 공급자 제한

리소스 공급자는 자체 조정 제한을 적용 합니다. 리소스 관리자는 보안 주체 ID 및 리소스 관리자 인스턴스로 제한 되므로 리소스 공급자는 이전 섹션의 기본 제한 보다 많은 요청을 받을 수 있습니다.

이 섹션에서는 광범위 하 게 사용 되는 일부 리소스 공급자의 제한 한도에 대해 설명 합니다.

### <a name="storage-throttling"></a>스토리지 제한

[!INCLUDE [azure-storage-limits-azure-resource-manager](../../../includes/azure-storage-limits-azure-resource-manager.md)]

### <a name="network-throttling"></a>네트워크 제한

Microsoft. Network 리소스 공급자는 다음과 같은 제한 제한을 적용 합니다.

| 작업 | 제한 |
| --------- | ----- |
| 쓰기/삭제 (PUT) | 5 분당 1000 |
| 읽기(GET) | 5 분당 1만 |

### <a name="compute-throttling"></a>계산 제한

계산 작업의 제한 한도에 대 한 자세한 내용은 [API 제한 오류 문제 해결-계산](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md)을 참조 하세요.

가상 머신 확장 집합 내의 가상 머신 인스턴스를 확인 하려면 [Virtual Machine Scale Sets 작업](/rest/api/compute/virtualmachinescalesetvms)을 사용 합니다. 예를 들어 가상 머신 [확장 집합 vm-목록](/rest/api/compute/virtualmachinescalesetvms/list) 매개 변수를 사용 하 여 가상 머신 인스턴스의 전원 상태를 확인 합니다. 이 API는 요청 수를 줄입니다.

### <a name="azure-resource-graph-throttling"></a>Azure 리소스 그래프 제한

[Azure 리소스 그래프](../../governance/resource-graph/overview.md) 는 해당 작업에 대 한 요청 수를 제한 합니다. 이 문서의 단계에 따라 남은 요청을 확인 하 고 제한에 도달 했을 때 응답 하는 방법을 리소스 그래프에도 적용 합니다. 그러나 리소스 그래프는 자체 제한 및 재설정 률을 설정 합니다. 자세한 내용은 [리소스 그래프 제한 헤더](../../governance/resource-graph/concepts/guidance-for-throttled-requests.md#understand-throttling-headers)를 참조 하세요.

## <a name="request-increase"></a>증가 요청

경우에 따라 제한 제한을 늘릴 수 있습니다. 시나리오에 대 한 제한 제한을 늘릴 수 있는지 확인 하려면 지원 요청을 만듭니다. 호출 패턴의 세부 정보가 평가 됩니다.

## <a name="error-code"></a>오류 코드

한도에 도달하면 HTTP 상태 코드 **429 너무 많은 요청**이 표시됩니다. 응답에는 다음 요청을 보내기 전에 응용 프로그램이 대기 (또는 절전) 해야 하는 시간 (초)을 지정 하는 **다시 시도-이후** 값이 포함 됩니다. 재시도 값이 경과하기 전에 요청을 보내면 요청이 처리되지 않고 새 재시도 값이 반환됩니다.

지정 된 시간 동안 기다린 후 Azure에 대 한 연결을 닫았다가 다시 열 수도 있습니다. 연결을 다시 설정 하 여 Azure Resource Manager의 다른 인스턴스에 연결할 수 있습니다.

Azure SDK를 사용 하는 경우 SDK에 자동 다시 시도 구성이 있을 수 있습니다. 자세한 내용은 [Azure 서비스에 대 한 다시 시도 지침](/azure/architecture/best-practices/retry-service-specific)을 참조 하세요.

일부 리소스 공급자는 429을 반환 하 여 임시 문제를 보고 합니다. 이 문제는 요청에 의해 직접 발생 하지 않는 오버 로드 조건 일 수 있습니다. 또는 대상 리소스 또는 종속 리소스의 상태에 대 한 일시적인 오류를 나타낼 수 있습니다. 예를 들어, 네트워크 리소스 공급자는 다른 작업에 의해 대상 리소스가 잠겨 있을 때 **RetryableErrorDueToAnotherOperation** 오류 코드와 함께 429을 반환 합니다. 오류가 제한 또는 임시 상태에서 발생 하는지 확인 하려면 응답에서 오류 세부 정보를 확인 합니다.

## <a name="remaining-requests"></a>나머지 요청

응답 헤더를 검사하여 나머지 요청 수를 확인할 수 있습니다. 읽기 요청은 나머지 읽기 요청 수에 대 한 헤더의 값을 반환 합니다. 쓰기 요청에는 나머지 쓰기 요청 수에 대 한 값이 포함 됩니다. 다음 표에서는 해당 값을 검사할 수 있는 응답 헤더를 설명합니다.

| 응답 헤더 | Description |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |구독에 범위가 지정된 나머지 읽기. 이 값은 읽기 작업에서 반환됩니다. |
| x-ms-ratelimit-remaining-subscription-writes |구독에 범위가 지정된 나머지 쓰기. 이 값은 쓰기 작업에서 반환됩니다. |
| x-ms-ratelimit-remaining-tenant-reads |테넌트에 범위가 지정된 나머지 읽기 |
| x-ms-ratelimit-remaining-tenant-writes |테넌트에 범위가 지정된 나머지 쓰기 |
| x-ms-ratelimit-remaining-subscription-resource-requests |구독에 범위가 지정된 나머지 리소스 종류 요청.<br /><br />이 헤더 값은 서비스에서 기본 제한을 재정의한 경우에만 반환됩니다. Resource Manager는 구독 읽기 또는 쓰기 대신 이 값을 추가합니다. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |구독에 범위가 지정된 나머지 리소스 종류 컬렉션 요청.<br /><br />이 헤더 값은 서비스에서 기본 제한을 재정의한 경우에만 반환됩니다. 이 값은 나머지 컬렉션 요청 수를 제공합니다(리소스 나열). |
| x-ms-ratelimit-remaining-tenant-resource-requests |테넌트에 범위가 지정된 나머지 리소스 종류 요청.<br /><br />이 헤더는 서비스에서 기본 제한을 재정의한 경우에만 테넌트 수준의 요청에 대해서만 추가됩니다. Resource Manager는 테넌트 읽기 또는 쓰기 대신 이 값을 추가합니다. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |테넌트에 범위가 지정된 나머지 리소스 종류 컬렉션 요청.<br /><br />이 헤더는 서비스에서 기본 제한을 재정의한 경우에만 테넌트 수준의 요청에 대해서만 추가됩니다. |

리소스 공급자는 나머지 요청에 대 한 정보를 사용 하 여 응답 헤더를 반환할 수도 있습니다. Compute 리소스 공급자에서 반환 하는 응답 헤더에 대 한 자세한 내용은 [호출 요금 정보 응답 헤더](../../virtual-machines/troubleshooting/troubleshooting-throttling-errors.md#call-rate-informational-response-headers)를 참조 하세요.

## <a name="retrieving-the-header-values"></a>헤더 값 검색

코드 또는 스크립트에서 이러한 헤더 값을 검색하는 것은 임의의 헤더 값을 검색하는 것과 같습니다. 

예를 들어 **C#** 에서는 다음 코드로 **response**로 명명된 **HttpWebResponse** 개체에서 헤더 값을 검색합니다.

```cs
response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)
```

**PowerShell**에서는 Invoke-WebRequest 작업에서 헤더 값을 검색합니다.

```powershell
$r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
$r.Headers["x-ms-ratelimit-remaining-subscription-reads"]
```

전체 PowerShell 예제는 [구독에 대한 Resource Manager 제한 확인](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)을 참조하세요.

디버깅을 위해 남은 요청 수를 확인하려는 경우 **PowerShell** cmdlet에 **-Debug** 매개 변수를 제공할 수 있습니다.

```powershell
Get-AzResourceGroup -Debug
```

그러면 다음 응답 값을 포함한 많은 값이 반환됩니다.

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
OK

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-reads: 11999
```

쓰기 제한을 가져오려면 쓰기 작업을 사용합니다. 

```powershell
New-AzResourceGroup -Name myresourcegroup -Location westus -Debug
```

그러면 다음 값을 포함하는 많은 값이 반환됩니다.

```powershell
DEBUG: ============================ HTTP RESPONSE ============================

Status Code:
Created

Headers:
Pragma                        : no-cache
x-ms-ratelimit-remaining-subscription-writes: 1199
```

**Azure CLI**에서 더 많은 자세한 정보 표시 옵션을 사용하여 헤더 값을 검색합니다.

```azurecli
az group list --verbose --debug
```

그러면 다음 값을 포함하는 많은 값이 반환됩니다.

```azurecli
msrest.http_logger : Response status: 200
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Content-Encoding': 'gzip'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'Vary': 'Accept-Encoding'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-reads': '11998'
```

쓰기 제한을 가져오려면 쓰기 작업을 사용합니다. 

```azurecli
az group create -n myresourcegroup --location westus --verbose --debug
```

그러면 다음 값을 포함하는 많은 값이 반환됩니다.

```azurecli
msrest.http_logger : Response status: 201
msrest.http_logger : Response headers:
msrest.http_logger :     'Cache-Control': 'no-cache'
msrest.http_logger :     'Pragma': 'no-cache'
msrest.http_logger :     'Content-Length': '163'
msrest.http_logger :     'Content-Type': 'application/json; charset=utf-8'
msrest.http_logger :     'Expires': '-1'
msrest.http_logger :     'x-ms-ratelimit-remaining-subscription-writes': '1199'
```

## <a name="next-steps"></a>다음 단계

* 전체 PowerShell 예제는 [구독에 대한 Resource Manager 제한 확인](https://github.com/Microsoft/csa-misc-utils/tree/master/psh-GetArmLimitsViaAPI)을 참조하세요.
* 제한 및 할당량에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
* 비동기 REST 요청 처리에 대해 알아보려면 [Azure 비동기 작업 추적](async-operations.md)을 참조하세요.
