---
title: '빠른 시작: Azure CLI를 사용하여 공유 쿼리 만들기'
description: 이 빠른 시작에서는 단계에 따라 Azure CLI에 대한 Resource Graph 확장을 사용하도록 설정하고 공유 쿼리를 만듭니다.
ms.date: 06/29/2020
ms.topic: quickstart
ms.openlocfilehash: c5f7e6fbe1b462c1f9b6e8ad46c598398e1aca02
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89050950"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 Resource Graph 공유 쿼리 만들기

[Azure CLI](/cli/azure/)와 함께 Azure Resource Graph를 사용하는 첫 번째 단계는 확장이 설치되어 있는지 확인하는 것입니다. 이 빠른 시작에서는 Azure CLI 설치에 확장을 추가하는 과정을 안내합니다. Azure CLI가 로컬로 설치된 상태로 또는 [Azure Cloud Shell](https://shell.azure.com)을 통해 확장을 사용할 수 있습니다.

이 프로세스가 끝나면 선택한 Azure CLI 설치에 확장이 추가되고 Resource Graph 공유 쿼리를 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>Resource Graph 확장 추가

Azure CLI를 사용하도록 설정하여 Azure Resource Graph와 작업하려면 해당 확장을 추가해야 합니다. 이 확장은 [Windows 10의 bash](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com)(독립형 및 포털 내부형), [Azure CLI Docker 이미지](https://hub.docker.com/r/microsoft/azure-cli/)를 포함하여 Azure CLI가 사용되거나 로컬로 설치될 수 있는 모든 위치에서 작동됩니다.

1. 최신 Azure CLI가 설치되었는지 확인합니다(**2.8.0** 이상). 아직 설치되지 않은 경우 [다음 지침](/cli/azure/install-azure-cli-windows)을 따릅니다.

1. 선택한 Azure CLI 환경에서 [az extension add](/cli/azure/extension#az-extension-add)를 사용하여 다음 명령으로 Resource Graph 확장을 가져옵니다.

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. 확장이 설치되었고 [az extension list](/cli/azure/extension#az-extension-list)로 필요한 버전인지(최소 **1.1.0**) 유효성을 검사합니다.

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Resource Graph 공유 쿼리 만들기

Azure CLI 확장이 선택한 환경에 추가되었으므로 Resource Graph 공유 쿼리를 만들어 볼 수 있습니다. 공유 쿼리는 Azure Resource Graph Explorer에서 사용 권한을 부여하거나 실행할 수 있는 Azure Resource Manager 개체입니다. 이 쿼리는 _위치_로 그룹화된 모든 리소스의 수를 요약합니다.

1. [az group create](/cli/azure/group#az-group-create)를 사용하여 리소스 그룹을 만들어 Azure Resource Graph 공유 쿼리를 저장합니다. 이 리소스 그룹의 이름은 `resource-graph-queries`이며 위치는 `westus2`입니다.

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. `graph` 확장 및 [az graph shared-query create](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-create) 명령을 사용하여 Azure Resource Graph 공유 쿼리를 만듭니다.

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. 새 리소스 그룹에 공유 쿼리를 나열합니다. [az graph shared-query list](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-list) 명령은 값의 배열을 반환합니다.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. 단일 공유 쿼리 결과만 가져오려면 [az graph shared-query show](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-show) 명령을 사용합니다.

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. [az graph query](/cli/azure/ext/resource-graph/graph#ext-resource-graph-az-graph-query) 명령에서 `{{shared-query-uri}}` 구문을 사용하여 Azure CLI에서 공유 쿼리를 실행합니다.
   먼저 위의 `show` 명령 결과에서 `id` 필드를 복사합니다. 예제의 `shared-query-uri` 텍스트를 `id` 필드의 값으로 바꾸고 주변 `{{` 및 `}}` 문자를 그대로 둡니다.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > `{{shared-query-uri}}` 구문은 **미리 보기** 기능입니다.

Resource Graph 공유 쿼리를 검색하는 또 다른 방법은 Azure Portal을 통하는 것입니다. 포털에서 검색 창을 사용하여 "Resource Graph 쿼리"를 검색합니다. 공유 쿼리를 선택합니다. **개요** 페이지에서 **쿼리** 탭은 저장된 쿼리를 표시합니다. **편집** 단추는 [Resource Graph Explorer](./first-query-portal.md)에서 엽니다.

## <a name="clean-up-resources"></a>리소스 정리

Azure CLI 환경에서 Resource Graph 공유 쿼리, 리소스 그룹 및 확장을 제거하려면 다음 명령을 사용하여 이 작업을 수행할 수 있습니다.

- [az graph shared-query delete](/cli/azure/ext/resource-graph/graph/shared-query#ext-resource-graph-az-graph-shared-query-delete)
- [az group delete](/cli/azure/group#az-group-delete)
- [az extension remove](/cli/azure/extension#az-extension-remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Resource Graph 확장을 Azure CLI 환경에 추가하고 공유 쿼리를 만들었습니다. 리소스 그래프 언어에 대한 자세한 내용을 보려면 쿼리 언어 정보 페이지로 이동하세요.

> [!div class="nextstepaction"]
> [쿼리 언어에 대한 자세한 정보 가져오기](./concepts/query-language.md)