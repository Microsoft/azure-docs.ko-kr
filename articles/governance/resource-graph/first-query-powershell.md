---
title: '빠른 시작: 첫 번째 PowerShell 쿼리'
description: 이 빠른 시작에서는 단계에 따라 Azure PowerShell에 대한 Resource Graph 모듈을 사용하도록 설정하고 첫 번째 쿼리를 실행합니다.
ms.date: 08/10/2020
ms.topic: quickstart
ms.openlocfilehash: 4cc7eb524060f2a797077f38321ae9ce6c9cf267
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/10/2020
ms.locfileid: "88056417"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 첫 번째 Resource Graph 쿼리 실행

Azure Resource Graph를 사용하는 첫 번째 단계는 Azure PowerShell용 모듈이 설치되어 있는지 확인하는 것입니다. 이 빠른 시작에서는 Azure PowerShell 설치에 모듈을 추가하는 과정을 안내합니다.

이 프로세스가 끝나면 선택한 Azure PowerShell 설치에 모듈이 추가되고 첫 번째 Resource Graph 쿼리를 실행하게 됩니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-module"></a>Resource Graph 모듈 추가

Azure PowerShell을 사용하도록 설정하여 Azure Resource Graph를 쿼리하려면 해당 모듈을 추가해야 합니다. [Azure Cloud Shell](https://shell.azure.com) 또는 [PowerShell Docker 이미지](https://hub.docker.com/_/microsoft-powershell)와 함께 로컬로 설치된 PowerShell로 이 모듈을 사용할 수 있습니다.

### <a name="base-requirements"></a>기본 요구 사항

Azure Resource Graph 모듈에는 다음 소프트웨어가 필요합니다.

- Azure PowerShell 1.0.0 이상 아직 설치되지 않은 경우 [다음 지침](/powershell/azure/install-az-ps)을 따릅니다.

- PowerShellGet 2.0.1 이상 설치되거나 업데이트되지 않은 경우 [다음 지침](/powershell/scripting/gallery/installing-psget)을 따릅니다.

### <a name="install-the-module"></a>모듈 설치

PowerShell용 Resource Graph 모듈은 **Az.ResourceGraph**입니다.

1. **관리자** PowerShell 프롬프트에서 다음 명령을 실행합니다.

   ```azurepowershell-interactive
   # Install the Resource Graph module from PowerShell Gallery
   Install-Module -Name Az.ResourceGraph
   ```

1. 모듈을 가져왔으며 최신 버전(0.7.5)인지 확인합니다.

   ```azurepowershell-interactive
   # Get a list of commands for the imported Az.ResourceGraph module
   Get-Command -Module 'Az.ResourceGraph' -CommandType 'Cmdlet'
   ```

## <a name="run-your-first-resource-graph-query"></a>첫 번째 Resource Graph 실행

Azure PowerShell 모듈이 선택한 환경에 추가되었으므로 간단한 Resource Graph 쿼리를 시도해 볼 수 있습니다. 쿼리는 각 리소스의 **이름** 및 **리소스 형식**과 함께 처음 5개 Azure 리소스를 반환합니다.

1. `Search-AzGraph` cmdlet을 사용하여 첫 번째 Azure Resource Graph 쿼리를 실행합니다.

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Run Azure Resource Graph query
   Search-AzGraph -Query 'Resources | project name, type | limit 5'
   ```

   > [!NOTE]
   > 이 쿼리 예제에서는 `order by`와 같은 정렬 한정자를 제공하지 않으므로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다.

1. 다음과 같이 **Name** 속성에 대해 `order by`를 수행하도록 쿼리를 업데이트합니다.

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with 'order by'
   Search-AzGraph -Query 'Resources | project name, type | limit 5 | order by name asc'
   ```

   > [!NOTE]
   > 첫 번째 쿼리와 마찬가지로 이 쿼리를 여러 번 실행하면 요청마다 다른 리소스 집합이 생성될 수 있습니다. 쿼리 명령의 순서는 중요합니다. 이 예제에서 `order by`는 `limit` 뒤에 옵니다. 이 명령 순서는 먼저 쿼리 결과를 제한한 다음, 정렬합니다.

1. **Name** 속성에 대해 `order by`를 먼저 수행한 후 상위 5개 결과로 `limit`를 수행하도록 쿼리를 업데이트합니다.

   ```azurepowershell-interactive
   # Run Azure Resource Graph query with `order by` first, then with `limit`
   Search-AzGraph -Query 'Resources | project name, type | order by name asc | limit 5'
   ```

최종 쿼리가 여러 번 실행될 때 환경이 전혀 변경되지 않는다고 가정하면 반환되는 결과는 **Name** 속성을 기준으로 일관되고 정렬되지만 여전히 상위 5개 결과로 제한됩니다.

> [!NOTE]
> 사용자에게 액세스 권한이 이미 있는 구독에서 쿼리가 결과를 반환하지 않는 경우 `Search-AzGraph` cmdlet이 기본 컨텍스트에서 구독으로 기본 설정됨을 유의하세요. 기본 컨텍스트의 일부인 구독 ID 목록을 보려면 이 `(Get-AzContext).Account.ExtendedProperties.Subscriptions`를 실행합니다. 액세스 권한이 있는 모든 구독을 검색하려면 `$PSDefaultParameterValues=@{"Search-AzGraph:Subscription"= $(Get-AzSubscription).ID}`를 실행하여 `Search-AzGraph` cmdlet에 대해 PSDefaultParameterValues를 설정합니다.
   
## <a name="clean-up-resources"></a>리소스 정리

Azure PowerShell 환경에서 Resource Graph 모듈을 제거하려면 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

```azurepowershell-interactive
# Remove the Resource Graph module from the current session
Remove-Module -Name 'Az.ResourceGraph'

# Uninstall the Resource Graph module from the environment
Uninstall-Module -Name 'Az.ResourceGraph'
```

> [!NOTE]
> 이전에 다운로드한 모듈 파일은 삭제되지 않습니다. 실행 중인 PowerShell 세션에서만 제거됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Resource Graph 모듈을 Azure PowerShell 환경에 추가하고 첫 번째 쿼리를 실행합니다. Resource Graph 언어에 대해 자세히 알아보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](./concepts/query-language.md)