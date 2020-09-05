---
title: Azure Functions 런타임 버전을 대상으로 지정하는 방법
description: Azure Functions는 여러 버전의 런타임을 지원합니다. Azure에서 호스팅된 함수 앱의 런타임 버전을 지정하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/22/2020
ms.openlocfilehash: a7d86ef26d50d60389ae09bf3245ed97fea2c3e3
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88926578"
---
# <a name="how-to-target-azure-functions-runtime-versions"></a>Azure Functions 런타임 버전을 대상으로 지정하는 방법

함수 앱은 특정 Azure Functions 런타임 버전에서 실행됩니다. [1. x, 2.x 및 3(sp3)](functions-versions.md)의 세 가지 주요 버전이 있습니다. 기본적으로 함수 앱은 런타임의 버전 3.x에 생성 됩니다. 이 문서에는 선택한 버전에서 실행할 함수 앱을 Azure에서 구성하는 방법을 설명합니다. 특정 버전에 대한 로컬 개발 환경을 구성하는 방법에 대한 자세한 내용은 [로컬로 Azure Functions 코딩 및 테스트](functions-run-local.md)를 참조하세요.

## <a name="automatic-and-manual-version-updates"></a>자동 및 수동 버전 업데이트

Azure Functions를 사용 하면 `FUNCTIONS_EXTENSION_VERSION` 함수 앱에서 응용 프로그램 설정을 사용 하 여 특정 버전의 런타임을 대상으로 지정할 수 있습니다. 함수 앱을 새 버전으로 변경하도록 명시적으로 선택할 때까지 지정된 주 버전에서 유지합니다. 주 버전만 지정 하는 경우 함수 앱은 사용할 수 있게 되 면 런타임의 새로운 부 버전으로 자동 업데이트 됩니다. 새 부 버전에는 주요 변경 내용이 도입 되지 않습니다. 

부 버전(예: "2.0.12345")을 지정하면 버전을 명시적으로 변경할 때까지 함수 앱은 특정 버전으로 고정됩니다. 이전 부 버전은 프로덕션 환경에서 정기적으로 제거 됩니다. 이 경우 함수 앱은에 설정 된 버전 대신 최신 버전에서 실행 됩니다 `FUNCTIONS_EXTENSION_VERSION` . 따라서 주 버전을 대상으로 지정할 수 있도록 특정 부 버전이 필요한 함수 앱의 문제를 신속 하 게 해결 해야 합니다. 부 버전 제거는 [App Service 알림에서](https://github.com/Azure/app-service-announcements/issues)발표 됩니다.

> [!NOTE]
> Azure Functions의 특정 주 버전에 고정 한 후 Visual Studio를 사용 하 여 Azure에 게시 하려고 하면 최신 버전으로 업데이트 하거나 게시를 취소 하 라는 대화 상자가 표시 됩니다. 이를 방지 하려면 `<DisableFunctionExtensionVersionUpdate>true</DisableFunctionExtensionVersionUpdate>` 파일에 속성을 추가 `.csproj` 합니다.

새 버전을 공개적으로 사용할 수 있으면 포털에서 메시지를 표시하여 해당 버전으로 업그레이드할 수 있는 기회를 제공합니다. 새 버전으로 변경한 후에 `FUNCTIONS_EXTENSION_VERSION` 애플리케이션 설정을 사용하여 이전 버전으로 다시 변경할 수 있습니다.

다음 표에서는 `FUNCTIONS_EXTENSION_VERSION` 자동 업데이트를 사용 하도록 설정 하기 위해 각 주 버전에 대 한 값을 보여 줍니다.

| 주 버전 | `FUNCTIONS_EXTENSION_VERSION` 값 |
| ------------- | ----------------------------------- |
| 3.x  | `~3` |
| 2.x  | `~2` |
| 1.x  | `~1` |

런타임 버전을 변경하면 함수 앱을 다시 시작하게 됩니다.

## <a name="view-and-update-the-current-runtime-version"></a>현재 런타임 버전 확인 및 업데이트

함수 앱에서 사용 하는 런타임 버전을 변경할 수 있습니다. 주요 변경 사항이 있으므로 함수 앱에서 함수를 만들기 전에 런타임 버전만 변경할 수 있습니다. 

> [!IMPORTANT]
> 런타임 버전은 `FUNCTIONS_EXTENSION_VERSION` 설정에서 결정되지만, 설정을 직접 변경하지 않고 Azure Portal에서 이 변경을 수행해야 합니다. 그 이유는 포털에서 변경 내용이 확인되고 필요에 따라 관련된 다른 변경 내용이 적용되기 때문입니다.

# <a name="portal"></a>[포털](#tab/portal)

[!INCLUDE [Set the runtime version in the portal](../../includes/functions-view-update-version-portal.md)]

> [!NOTE]
> Azure Portal를 사용 하 여 이미 함수가 포함 된 함수 앱에 대 한 런타임 버전을 변경할 수 없습니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Azure CLI에서 `FUNCTIONS_EXTENSION_VERSION`을 확인하고 설정할 수도 있습니다.  

Azure CLI를 사용하여 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 명령을 실행해 현재 런타임 버전을 확인합니다.

```azurecli-interactive
az functionapp config appsettings list --name <function_app> \
--resource-group <my_resource_group>
```

이 코드에서 `<function_app>`을 함수 앱 이름으로 바꿉니다. 또한 `<my_resource_group>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. 

다음 출력의 `FUNCTIONS_EXTENSION_VERSION`은 보기 편하도록 잘린 상태입니다.

```output
[
  {
    "name": "FUNCTIONS_EXTENSION_VERSION",
    "slotSetting": false,
    "value": "~2"
  },
  {
    "name": "FUNCTIONS_WORKER_RUNTIME",
    "slotSetting": false,
    "value": "dotnet"
  },
  
  ...
  
  {
    "name": "WEBSITE_NODE_DEFAULT_VERSION",
    "slotSetting": false,
    "value": "8.11.1"
  }
]
```

함수 앱에서 [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings) 명령으로 함수 앱의 `FUNCTIONS_EXTENSION_VERSION` 설정을 업데이트할 수 있습니다.

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP> \
--resource-group <RESOURCE_GROUP> \
--settings FUNCTIONS_EXTENSION_VERSION=<VERSION>
```

`<FUNCTION_APP>`은 함수 앱 이름으로 바꿉니다. 또한 `<RESOURCE_GROUP>`을 함수 앱의 리소스 그룹 이름으로 바꿉니다. 또한를 `<VERSION>` 특정 버전 또는, 또는으로 바꿉니다 `~3` `~2` `~1` .

앞의 코드 샘플에서 **사용해 보세요.** 를 선택하여 [Azure Cloud Shell](../cloud-shell/overview.md)에서 이 명령을 실행할 수 있습니다. 또한 [Azure CLI locally(로컬로 Azure CLI 설치)](/cli/azure/install-azure-cli)를 사용하면 [az login](/cli/azure/reference-index#az-login)을 실행하여 로그인한 후 이 명령을 실행할 수도 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Azure Functions 런타임을 확인 하려면 다음 cmdlet을 사용 합니다. 

```powershell
Get-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>"
```

을 `<FUNCTION_APP>` 함수 앱 및의 이름으로 바꿉니다 `<RESOURCE_GROUP>` . 설정의 현재 값 `FUNCTIONS_EXTENSION_VERSION` 이 해시 테이블에 반환 됩니다.

함수 런타임을 변경 하려면 다음 스크립트를 사용 합니다.

```powershell
Update-AzFunctionAppSetting -Name "<FUNCTION_APP>" -ResourceGroupName "<RESOURCE_GROUP>" -AppSetting @{"FUNCTIONS_EXTENSION_VERSION" = "<VERSION>"} -Force
```

이전과 마찬가지로을 `<FUNCTION_APP>` 함수 앱의 이름으로 바꾸고을 `<RESOURCE_GROUP>` 리소스 그룹의 이름으로 바꿉니다. 또한를 `<VERSION>` 특정 버전 또는와 같은 주 버전으로 바꿉니다 `~2` `~3` . `FUNCTIONS_EXTENSION_VERSION`반환 된 해시 테이블에서 설정의 업데이트 된 값을 확인할 수 있습니다. 

---

응용 프로그램 설정을 변경한 후 함수 앱이 다시 시작 됩니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [로컬 개발 환경에서 2.0 런타임을 대상으로 지정](functions-run-local.md)

> [!div class="nextstepaction"]
> [런타임 버전에 대한 릴리스 정보 참조](https://github.com/Azure/azure-webjobs-sdk-script/releases)
