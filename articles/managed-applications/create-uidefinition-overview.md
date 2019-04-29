---
title: Azure Managed Applications의 UI 정의 만들기 이해 | Microsoft Docs
description: Azure Managed Applications에 대한 UI 정의를 만드는 방법에 대해 설명합니다.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2017
ms.author: tomfitz
ms.openlocfilehash: ab777b487159b009bf2cac6086bb09cc71714b0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60587753"
---
# <a name="create-azure-portal-user-interface-for-your-managed-application"></a>관리되는 애플리케이션에 대한 Azure Portal 사용자 인터페이스 만들기
이 문서에서는 createUiDefinition.json 파일의 핵심 개념을 소개합니다. Azure Portal에서 이 파일을 사용하여 관리되는 애플리케이션을 만들기 위한 사용자 인터페이스를 생성합니다.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
   "handler": "Microsoft.Compute.MultiVm",
   "version": "0.1.2-preview",
   "parameters": {
      "basics": [ ],
      "steps": [ ],
      "outputs": { }
   }
}
```

CreateUiDefinition에는 항상 다음 세 가지 속성이 포함됩니다. 

* 처리기
* 버전
* 매개 변수

관리되는 애플리케이션의 경우 처리기는 항상 `Microsoft.Compute.MultiVm`이어야 하며, 지원되는 최신 버전은 `0.1.2-preview`여야 합니다.

매개 변수 속성의 스키마는 지정된 처리기와 버전의 조합에 따라 다릅니다. 관리되는 애플리케이션의 경우 지원되는 속성은 `basics`, `steps` 및 `outputs`입니다. basics 및 steps 속성에는 Azure Portal에서 표시할 _요소_(예: 텍스트 상자 및 드롭다운)가 포함됩니다. outputs 속성은 지정된 요소의 출력 값을 Azure Resource Manager 배포 템플릿의 매개 변수에 매핑하는 데 사용됩니다.

`$schema`를 포함하는 것이 좋지만 선택 사항입니다. 지정하는 경우 `version`의 값은 `$schema` URI 내의 버전과 일치해야 합니다.

## <a name="basics"></a>기본 사항
기본 단계는 항상 Azure Portal에서 파일을 구문 분석할 때 생성되는 마법사의 첫 번째 단계입니다. 포털에서는 `basics`에 지정된 요소를 표시할 뿐만 아니라 사용자가 배포에 대한 구독, 리소스 그룹 및 위치를 선택하기 위한 요소도 삽입합니다. 일반적으로 클러스터 또는 관리자 자격 증명의 이름과 같은 배포 전체 매개 변수를 쿼리하는 요소는 이 단계에서 진행해야 합니다.

요소의 동작이 사용자의 구독, 리소스 그룹 또는 위치에 따라 다를 경우 해당 요소는 basics에 사용할 수 없습니다. 예를 들어 **Microsoft.Compute.SizeSelector**는 사용 가능한 크기의 목록을 결정하는 사용자의 구독 및 위치에 따라 다릅니다. 따라서 **Microsoft.Compute.SizeSelector**는 steps에만 사용할 수 있습니다. 일반적으로 **Microsoft.Common** 네임스페이스의 요소만 basics에 사용할 수 있습니다. 사용자의 컨텍스트에 종속되지 않는 다른 네임스페이스(예: **Microsoft.Compute.Credentials**)의 일부 요소는 여전히 허용됩니다.

## <a name="steps"></a>단계
steps 속성에는 하나 이상의 요소가 포함된 각 basics 뒤에 표시하는 추가 steps가 0개 이상 포함될 수 있습니다. 배포할 애플리케이션의 역할별 또는 계층별로 단계를 추가하는 것이 좋습니다. 예를 들어 마스터 노드에 대한 입력 단계와 클러스터의 작업자 노드에 대한 단계를 추가합니다.

## <a name="outputs"></a>outputs
Azure Portal에서는 `outputs` 속성을 사용하여 `basics` 및 `steps`의 요소를 Azure Resource Manager 배포 템플릿의 매개 변수에 매핑합니다. 이 사전의 키는 템플릿 매개 변수의 이름이며, 값은 참조되는 요소에 있는 출력 개체의 속성입니다.

관리되는 애플리케이션 리소스 이름을 설정하려면 출력 속성에 `applicationResourceName`이라는 값을 포함해야 합니다. 이 값을 설정하지 않으면 애플리케이션은 이름에 대해 GUID를 할당합니다. 사용자 이름을 요청하는 사용자 인터페이스에 텍스트 상자를 포함할 수 있습니다.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="functions"></a>Functions
Azure Resource Manager의 템플릿 함수(구문과 기능 모두)와 마찬가지로, CreateUiDefinition은 요소의 입력 및 출력에서 사용하는 함수뿐만 아니라 조건부와 같은 기능도 제공합니다.

## <a name="next-steps"></a>다음 단계
createUiDefinition.json 파일 자체에는 간단한 스키마가 있습니다. 실제 수준의 모든 지원되는 요소와 기능을 통해 제공됩니다. 이러한 항목은 다음에 자세히 설명되어 있습니다.

- [요소](create-uidefinition-elements.md)
- [함수](create-uidefinition-functions.md)

createUiDefinition에 대한 현재 JSON 스키마는 https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json에 사용할 수 있습니다.

예제 사용자 인터페이스 파일의 경우 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/samples/201-managed-app-using-existing-vnet/createUiDefinition.json)을 참조하세요.
