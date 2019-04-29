---
title: 리소스 그룹 및 리소스 삭제 - Azure Resource Manager
description: 리소스 그룹을 삭제할 때 Azure Resource Manager가 리소스 삭제를 명령하는 방법을 설명합니다. 응답 코드 및 Resource Manager가 응답 코드를 처리하여 삭제 성공 여부를 확인하는 방법을 설명합니다.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/09/2018
ms.author: tomfitz
ms.custom: seodec18
ms.openlocfilehash: c38b1ccf7f7ccfe57e2b29f236f642238c4706a7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61363660"
---
# <a name="azure-resource-manager-resource-group-deletion"></a>Azure Resource Manager 리소스 그룹 삭제

이 문서에서는 리소스 그룹을 삭제할 때 Azure Resource Manager가 리소스 삭제를 명령하는 방법을 설명합니다.

## <a name="determine-order-of-deletion"></a>삭제 순서 결정

리소스 그룹을 삭제하면 Resource Manager가 리소스 삭제 순서를 결정합니다. 다음 순서가 사용됩니다.

1. 모든 자식(중첩) 리소스가 삭제됩니다.

2. 그 다음에는 다른 리소스를 관리하는 리소스가 삭제됩니다. 리소스의 `managedBy` 속성을 설정하여 다른 리소스에 의해 관리됨을 나타낼 수 있습니다. 이 속성을 설정하면 다른 리소스를 관리하는 리소스가 삭제된 후 다른 리소스가 삭제됩니다.

3. 나머지 리소스는 이전 두 범주 이후에 삭제됩니다.

## <a name="resource-deletion"></a>리소스 삭제

순서가 결정되면 Resource Manager는 각 리소스에 대한 DELETE 작업을 실행합니다. Manager는 종속 요소가 완료될 때까지 기다렸다가 작업을 계속 진행합니다.

동기 작업의 경우 예상되는 성공 응답 코드는 다음과 같습니다.

* 200
* 204
* 404

비동기 작업의 경우 예상되는 성공 응답은 202입니다. Resource Manager는 위치 헤더 또는 azure 비동기 작업 헤더를 추적하여 비동기 삭제 작업의 상태를 확인합니다.
  
### <a name="errors"></a>오류

삭제 작업에서 오류가 반환되면 Resource Manager는 DELETE 호출을 다시 시도합니다. 5xx, 429 및 408 상태 코드에 대한 재시도가 발생합니다. 기본적으로 재시도 기간은 15분입니다.

## <a name="after-deletion"></a>삭제 후

Resource Manager는 삭제하려고 시도한 각 리소스에 대해 GET 호출을 실행합니다. 이 GET 호출의 응답은 404로 예상됩니다. Resource Manager는 404 응답을 받으면 삭제가 완료된 것으로 간주합니다. Resource Manager는 캐시에서 리소스를 제거합니다.

그러나 리소스에 대한 GET 호출에서 200 또는 201 응답을 반환하는 경우 Resource Manager는 리소스를 다시 만듭니다.

### <a name="errors"></a>오류

GET 작업에서 오류가 반환되는 경우 Resource Manager는 다음 오류 코드에 대해 GET을 다시 시도합니다.

* 100 미만
* 408
* 429
* 500 초과

그 외의 오류 코드는 Resource Manager가 리소스 삭제를 실패합니다.

## <a name="next-steps"></a>다음 단계

* Resource Manager의 개념을 이해하려면 [Azure Resource Manager 개요](resource-group-overview.md)를 참조하세요.
* 삭제 명령은 [PowerShell](/powershell/module/az.resources/Remove-AzResourceGroup), [Azure CLI](/cli/azure/group?view=azure-cli-latest#az-group-delete) 및 [REST API](/rest/api/resources/resourcegroups/delete)를 참조하세요.
