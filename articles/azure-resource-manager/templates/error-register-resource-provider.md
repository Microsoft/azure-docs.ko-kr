---
title: 리소스 공급자 등록 오류
description: Azure Resource Manager를 사용 하 여 리소스를 배포할 때 Azure 리소스 공급자 등록 오류를 해결 하는 방법을 설명 합니다.
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 143cf03a33739f43a29af94fc2f8a336fb3aef8f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076670"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>리소스 공급자 등록 오류 해결

이 문서에서는 이전에 구독에 사용하지 않은 리소스 공급자를 사용할 때 발생할 수 있는 오류에 대해 설명합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>증상

리소스를 배포할 때 다음 오류 코드 및 메시지가 나타날 수 있습니다.

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

또는 다음과 유사한 메시지가 나타날 수 있습니다.

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

오류 메시지는 지원되는 위치 및 API 버전에 대해 제안을 제공해야 합니다. 템플릿을 제안된 값 중 하나로 변경할 수 있습니다. 대부분의 공급자는 Azure Portal이나 사용 중인 명령줄 인터페이스에 의해 자동으로 등록되지만, 그렇지 않은 경우도 있습니다. 특정 리소스 공급자를 전에 사용하지 않은 경우 해당 공급자를 등록해야 합니다.

또는 가상 머신에 대해 자동 종료를 해제할 수 없을 경우 다음과 비슷한 오류 메시지가 표시될 수 있습니다.

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>원인

이러한 오류가 표시되는 이유는 다음과 같습니다.

* 필요한 리소스 공급자가 구독에 대해 등록되지 않았습니다.
* 해당 리소스 종류에 대해 API 버전이 지원되지 않습니다.
* 해당 리소스 종류에 대해 위치가 지원되지 않습니다.
* VM 자동 종료의 경우 Microsoft.DevTestLab 리소스 공급자를 등록해야 합니다.

## <a name="solution-1---powershell"></a>해결 방법 1 - PowerShell

PowerShell의 경우 **Get-AzResourceProvider**를 사용하여 등록 상태를 확인합니다.

```powershell
Get-AzResourceProvider -ListAvailable
```

공급자를 등록하려면 **Register-AzResourceProvider**를 사용하여 등록할 리소스 공급자의 이름을 제공합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

특정 종류의 리소스에 대해 지원되는 위치를 확인하려면 다음을 사용합니다.

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

특정 종류의 리소스에 대해 지원되는 API 버전을 확인하려면 다음을 사용합니다.

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>해결 방법 2 - Azure CLI

공급자가 등록되어 있는지 확인하려면 `az provider list` 명령을 사용합니다.

```azurecli-interactive
az provider list
```

리소스 공급자를 등록하려면 `az provider register` 명령을 사용하고 등록할 *네임스페이스* 를 지정합니다.

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

리소스 유형에 대해 지원되는 위치 및 API 버전을 보려면 다음을 사용합니다.

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>해결 방법 3 - Azure Portal

등록 상태를 볼 수 있으며 포털을 통해 리소스 공급자 네임스페이스를 등록할 수 있습니다.

1. 포털에서 **모든 서비스**를 선택합니다.

   ![모든 서비스 선택](./media/error-register-resource-provider/select-all-services.png)

1. **구독**을 선택합니다.

   ![구독 선택](./media/error-register-resource-provider/select-subscriptions.png)

1. 구독 목록에서 리소스 공급자를 등록하는 데 사용하려는 구독을 선택합니다.

   ![리소스 공급자를 등록할 구독을 선택합니다.](./media/error-register-resource-provider/select-subscription-to-register.png)

1. 구독의 경우 **리소스 공급자**를 선택합니다.

   ![리소스 공급자 선택](./media/error-register-resource-provider/select-resource-provider.png)

1. 리소스 공급자의 목록을 확인하고 필요한 경우 **등록** 링크를 선택하여 배포하려는 유형의 리소스 공급자를 등록합니다.

   ![리소스 공급자 나열](./media/error-register-resource-provider/list-resource-providers.png)
