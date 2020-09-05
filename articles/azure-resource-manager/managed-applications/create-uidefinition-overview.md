---
title: 포털 창의 파일에 CreateUiDefinition.js
description: Azure Portal에 대 한 사용자 인터페이스 정의를 만드는 방법을 설명 합니다. Azure Managed Applications를 정의할 때 사용 됩니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: 0e2aee194d3c97655dd4ec5aaeea46fb607c4c5e
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88210973"
---
# <a name="createuidefinitionjson-for-azure-managed-applications-create-experience"></a>Azure 관리되는 애플리케이션의 만들기 환경을 위한 CreateUiDefinition.json

이 문서에서는 파일 ** 에createUiDefinition.js** 의 핵심 개념을 소개 합니다. Azure Portal는 관리 되는 응용 프로그램을 만들 때이 파일을 사용 하 여 사용자 인터페이스를 정의 합니다.

템플릿은 다음과 같습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "config": {
            "basics": { }
        },
        "basics": [ ],
        "steps": [ ],
        "outputs": { },
        "resourceTypes": [ ]
    }
}
```

CreateUiDefinition에는 항상 다음 세 가지 속성이 포함됩니다. 

* handler(핸들러)
* 버전
* 매개 변수

처리기는 항상 이어야 `Microsoft.Azure.CreateUIDef` 하며, 지원 되는 최신 버전은 `0.1.2-preview` 입니다.

매개 변수 속성의 스키마는 지정된 처리기와 버전의 조합에 따라 다릅니다. 관리 되는 응용 프로그램의 경우 지원 되는 속성은 `basics` ,, `steps` `outputs` 및 `config` 입니다. basics 및 steps 속성에는 Azure Portal에서 표시할 [요소](create-uidefinition-elements.md)(예: 텍스트 상자 및 드롭다운)가 포함됩니다. Outputs 속성은 지정 된 요소의 출력 값을 Azure Resource Manager 템플릿의 매개 변수에 매핑하는 데 사용 됩니다. `config`단계의 기본 동작을 재정의 해야 하는 경우에만를 사용 `basics` 합니다.

`$schema`를 포함하는 것이 좋지만 선택 사항입니다. 지정하는 경우 `version`의 값은 `$schema` URI 내의 버전과 일치해야 합니다.

JSON 편집기를 사용 하 여 createUiDefinition을 만든 다음 [Createuidefinition 샌드박스에서](https://portal.azure.com/?feature.customPortal=false&#blade/Microsoft_Azure_CreateUIDef/SandboxBlade) 테스트 하 여 미리 볼 수 있습니다. 샌드박스에 대 한 자세한 내용은 [Azure Managed Applications에 대 한 포털 인터페이스 테스트](test-createuidefinition.md)를 참조 하세요.

## <a name="basics"></a>기본 사항

**기본** 단계는 Azure Portal에서 파일을 구문 분석할 때 생성 되는 첫 번째 단계입니다. 기본적으로 기본 단계를 통해 사용자는 구독, 리소스 그룹 및 배포 위치를 선택할 수 있습니다.

:::image type="content" source="./media/create-uidefinition-overview/basics.png" alt-text="기본 사항 기본값":::

이 섹션에서 더 많은 요소를 추가할 수 있습니다. 가능 하면 클러스터 이름 또는 관리자 자격 증명과 같은 배포 차원의 매개 변수를 쿼리 하는 요소를 추가 합니다.

다음 예제에서는 기본 요소에 추가 된 입력란을 보여 줍니다.

```json
"basics": [
    {
        "name": "textBox1",
        "type": "Microsoft.Common.TextBox",
        "label": "Textbox on basics",
        "defaultValue": "my text value",
        "toolTip": "",
        "visible": true
    }
]
```

## <a name="config"></a>Config

기본 단계에 대 한 기본 동작을 재정의 해야 하는 경우 구성 요소를 지정 합니다. 다음 예제에서는 사용할 수 있는 속성을 보여 줍니다.

```json
"config": {
    "basics": {
        "description": "Customized description with **markdown**, see [more](https://www.microsoft.com).",
        "subscription": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid subscription."
                    },
                    {
                        "permission": "<Resource Provider>/<Action>",
                        "message": "Must have correct permission to complete this step."
                    }
                ]
            },
            "resourceProviders": [
                "<Resource Provider>"
            ]
        },
        "resourceGroup": {
            "constraints": {
                "validations": [
                    {
                        "isValid": "[expression for checking]",
                        "message": "Please select a valid resource group."
                    }
                ]
            },
            "allowExisting": true
        },
        "location": {
            "label": "Custom label for location",
            "toolTip": "provide a useful tooltip",
            "resourceTypes": [
                "Microsoft.Compute/virtualMachines"
            ],
            "allowedValues": [
                "eastus",
                "westus2"
            ],
            "visible": true
        }
    }
},
```

의 `description` 경우 리소스를 설명 하는 markdown 사용 문자열을 제공 합니다. 여러 줄 형식 및 링크가 지원 됩니다.

에 대해 `location` 재정의 하려는 위치 컨트롤에 대 한 속성을 지정 합니다. 재정의 되지 않은 모든 속성은 기본값으로 설정 됩니다. `resourceTypes` 정규화 된 리소스 형식 이름을 포함 하는 문자열 배열을 허용 합니다. 위치 옵션은 리소스 유형을 지 원하는 지역 으로만 제한 됩니다.  `allowedValues`   영역 문자열의 배열을 허용 합니다. 해당 영역만 드롭다운에 표시 됩니다.및를 모두 설정할 수 있습니다 `allowedValues`    `resourceTypes` . 결과는 두 목록의 교집합입니다. 마지막으로 `visible` 속성을 사용 하 여 위치 드롭다운을 조건부로 또는 완전히 사용 하지 않도록 설정할 수 있습니다.  

`subscription`및 요소를 사용 하 여 `resourceGroup` 추가 유효성 검사를 지정할 수 있습니다. 유효성 검사를 지정 하는 구문은 [텍스트 상자](microsoft-common-textbox.md)에 대 한 사용자 지정 유효성 검사와 동일 합니다. `permission`구독 또는 리소스 그룹에 대 한 유효성 검사를 지정할 수도 있습니다.  

구독 제어는 리소스 공급자 네임 스페이스의 목록을 수락 합니다. 예를 들어, **Microsoft Compute**를 지정할 수 있습니다. 사용자가 리소스 공급자를 지원 하지 않는 구독을 선택 하면 오류 메시지가 표시 됩니다. 이 오류는 리소스 공급자가 해당 구독에 등록 되지 않고 사용자에 게 리소스 공급자를 등록할 수 있는 권한이 없는 경우에 발생 합니다.  

리소스 그룹 컨트롤에는에 대 한 옵션이 `allowExisting` 있습니다. `true`인 경우 사용자는 이미 리소스가 있는 리소스 그룹을 선택할 수 있습니다. 이 플래그는 솔루션 템플릿에 가장 적합 합니다. 기본 동작에서 사용자는 새 리소스 그룹 또는 비어 있는 리소스 그룹을 선택 해야 합니다. 대부분의 다른 시나리오에서는이 속성을 지정 하지 않아도 됩니다.  

## <a name="steps"></a>단계

단계 속성에는 기본 후 표시할 0 개 이상의 추가 단계가 포함 되어 있습니다. 각 단계에는 하나 이상의 요소가 포함 되어 있습니다. 배포할 애플리케이션의 역할별 또는 계층별로 단계를 추가하는 것이 좋습니다. 예를 들어 마스터 노드 입력에 대 한 단계와 클러스터의 작업자 노드에 대 한 단계를 추가 합니다.

```json
"steps": [
    {
        "name": "demoConfig",
        "label": "Configuration settings",
        "elements": [
          ui-elements-needed-to-create-the-instance
        ]
    }
]
```

## <a name="outputs"></a>출력

Azure Portal에서는 `outputs` 속성을 사용하여 `basics` 및 `steps`의 요소를 Azure Resource Manager 배포 템플릿의 매개 변수에 매핑합니다. 이 사전의 키는 템플릿 매개 변수의 이름이며, 값은 참조되는 요소에 있는 출력 개체의 속성입니다.

관리되는 애플리케이션 리소스 이름을 설정하려면 출력 속성에 `applicationResourceName`이라는 값을 포함해야 합니다. 이 값을 설정 하지 않으면 응용 프로그램에서 이름에 GUID를 할당 합니다. 사용자 이름을 요청하는 사용자 인터페이스에 텍스트 상자를 포함할 수 있습니다.

```json
"outputs": {
    "vmName": "[steps('appSettings').vmName]",
    "trialOrProduction": "[steps('appSettings').trialOrProd]",
    "userName": "[steps('vmCredentials').adminUsername]",
    "pwd": "[steps('vmCredentials').vmPwd.password]",
    "applicationResourceName": "[steps('appSettings').vmName]"
}
```

## <a name="resource-types"></a>리소스 종류

배포할 리소스 유형을 지 원하는 위치로만 사용 가능한 위치를 필터링 하려면 리소스 형식의 배열을 제공 합니다. 둘 이상의 리소스 형식을 제공 하는 경우 모든 리소스 유형을 지 원하는 위치만 반환 됩니다. 이 속성은 선택 사항입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json#",
    "handler": "Microsoft.Azure.CreateUIDef",
    "version": "0.1.2-preview",
    "parameters": {
        "resourceTypes": ["Microsoft.Compute/disks"],
        "basics": [
          ...
```  

## <a name="functions"></a>Functions

CreateUiDefinition은 요소의 입/출력 및 조건 등의 기능을 사용 하기 위한 [함수](create-uidefinition-functions.md) 를 제공 합니다. 이러한 함수는 템플릿 함수를 Azure Resource Manager 하는 구문과 기능에서 비슷합니다.

## <a name="next-steps"></a>다음 단계

createUiDefinition.json 파일 자체에는 간단한 스키마가 있습니다. 실제 수준의 모든 지원되는 요소와 기능을 통해 제공됩니다. 이러한 항목은 다음에 자세히 설명되어 있습니다.

- [요소](create-uidefinition-elements.md)
- [함수](create-uidefinition-functions.md)

createUiDefinition에 대한 현재 JSON 스키마는 `https://schema.management.azure.com/schemas/0.1.2-preview/CreateUIDefinition.MultiVm.json`에 사용할 수 있습니다.

예제 사용자 인터페이스 파일의 경우 [createUiDefinition.json](https://github.com/Azure/azure-managedapp-samples/blob/master/Managed%20Application%20Sample%20Packages/201-managed-app-using-existing-vnet/createUiDefinition.json)을 참조하세요.
