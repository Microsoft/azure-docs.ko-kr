---
title: 리소스를 찾을 수 없음 오류
description: Azure Resource Manager 템플릿으로 배포할 때 리소스를 찾을 수 없을 때 발생 하는 오류를 해결 하는 방법을 설명 합니다.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773257"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Azure 리소스 찾을 수 없음 오류 해결

이 문서에서는 배포 중에 리소스를 찾을 수 없을 때 발생할 수 있는 오류에 대해 설명합니다.

## <a name="symptom"></a>증상

해석할 수 없는 리소스 이름이 포함된 템플릿인 경우 다음과 비슷한 오류 메시지가 표시됩니다.

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

해석할 수 없는 리소스에 [reference](template-functions-resource.md#reference) 또는 [listKeys](template-functions-resource.md#listkeys) 함수를 사용하는 경우에는 다음과 같은 오류 메시지가 표시됩니다.

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>원인

Resource Manager에서 리소스에 대한 속성을 검색해야 하지만 구독에서 해당 리소스를 식별할 수 없습니다.

## <a name="solution-1---set-dependencies"></a>해결 방법 1 - 종속성 설정

템플릿에서 누락된 리소스를 배포하려는 경우 종속성을 추가해야 하는지 여부를 확인합니다. 리소스 관리자는 가능한 경우 리소스를 병렬로 만들어 배포를 최적화합니다. 한 리소스가 다른 리소스 뒤에 배포되어야 하는 경우 템플릿에서 **dependsOn** 요소를 사용합니다. 예를 들어 웹앱을 배포할 때는 App Service 계획이 반드시 존재해야 합니다. 웹앱이 App Service 계획에 종속된다고 지정하지 않으면 Resource Manager에서 두 리소스를 모두 만듭니다. 웹앱에 속성을 설정하려고 할 때 App Service 계획 리소스가 아직 없기 때문에 이 리소스를 찾을 수 없다는 오류 메시지가 표시됩니다. 웹앱에서 종속성을 설정하면 이러한 오류를 방지할 수 있습니다.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

하지만 필요하지 않은 종속성은 설정하지 않는 것이 좋습니다. 불필요한 종속성이 있으면 서로 종속되지 않은 리소스가 동시에 배포되는 것을 막기 때문에 배포 시간이 길어집니다. 또한 배포를 방해하는 순환 종속성을 만들 수 있습니다. [reference](template-functions-resource.md#reference) 함수 및 [list*](template-functions-resource.md#list) 함수는 리소스가 같은 템플릿에 배포되는 경우 참조되는 리소스에 대한 암시적 종속성을 만들고 해당 이름으로 참조됩니다(리소스 ID가 아닌). 따라서 **dependsOn** 속성에 지정된 종속성보다 많은 종속성을 가질 수 있습니다. [resourceId](template-functions-resource.md#resourceid) 함수는 암시적 종속성을 만들지 않거나 리소스가 존재하는지를 확인합니다. [reference](template-functions-resource.md#reference) 함수 및 [list*](template-functions-resource.md#list) 함수는 리소스가 해당 리소스 ID로 참조되는 경우 암시적 종속성을 만들지 않습니다. 암시적 종속성을 만들려면 동일한 템플릿에 배포된 리소스의 이름을 전달합니다.

종속성 문제가 발생하는 경우 리소스 배포 순서를 간파할 필요가 있습니다. 배포 작업의 순서를 보려면 다음을 수행합니다.

1. 리소스 그룹에 대한 배포 기록을 선택합니다.

   ![배포 기록 선택](./media/error-not-found/select-deployment.png)

2. 기록에서 배포를 선택하고 **이벤트**를 선택합니다.

   ![배포 이벤트 선택](./media/error-not-found/select-deployment-events.png)

3. 각 리소스에 대한 이벤트의 시퀀스를 검사합니다. 각 작업의 상태에 주의합니다. 예를 들어 다음 이미지는 병렬로 배포된 3개의 스토리지 계정을 보여 줍니다. 3개의 스토리지 계정이 동시에 시작되었다는 것을 볼 수 있습니다.

   ![병렬 배포](./media/error-not-found/deployment-events-parallel.png)

   다음 이미지는 동시에 배포되지 않은 3개의 스토리지 계정을 보여줍니다. 두 번째 스토리지 계정은 첫 번째 스토리지 계정에 종속되고 세 번째 스토리지 계정은 두 번째 스토리지 계정에 종속됩니다. 다음 스토리지 계정이 시작되기 전에 첫 번째 스토리지 계정이 시작, 승인, 완료됩니다.

   ![순차 배포](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>해결 방법 2 - 다른 리소스 그룹에서 리소스 가져오기

배포할 리소스 그룹과 다른 리소스 그룹에 리소스가 있는 경우 [resourceId 함수](template-functions-resource.md#resourceid)를 사용하여 정규화된 리소스 이름을 가져옵니다.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>해결 방법 3 - reference 함수 검사

[reference](template-functions-resource.md#reference) 함수를 포함하는 식을 찾습니다. 리소스가 동일한 템플릿, 리소스 그룹 및 구독에 있는지 여부에 따라 제공하는 값이 다릅니다. 시나리오에 필요한 매개 변수 값을 제공하는지 다시 한 번 확인합니다. 리소스가 다른 리소스 그룹에 있으면 전체 리소스 ID를 제공합니다. 예를 들어 다른 리소스 그룹의 스토리지 계정을 참조하려면 다음을 사용합니다.

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>해결 방법 4-리소스에서 관리 id 가져오기

[관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 암시적으로 만드는 리소스를 배포 하는 경우 관리 되는 id의 값을 검색 하기 전에 해당 리소스가 배포 될 때까지 기다려야 합니다. 관리 id 이름을 [참조](template-functions-resource.md#reference) 함수에 전달 하는 경우 리소스와 id를 배포 하기 전에 리소스 관리자에서 참조를 확인 하려고 시도 합니다. 대신 id가 적용 되는 리소스의 이름을 전달 합니다. 이 방법을 사용 하면 리소스 관리자에서 참조 함수를 확인 하기 전에 리소스와 관리 되는 id가 배포 됩니다.

참조 함수에서 `Full`를 사용 하 여 관리 id를 비롯 한 모든 속성을 가져옵니다.

예를 들어 가상 머신 확장 집합에 적용 되는 관리 되는 id에 대 한 테넌트 ID를 가져오려면 다음을 사용 합니다.

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```