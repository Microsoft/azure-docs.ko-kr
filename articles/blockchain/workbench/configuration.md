---
title: Azure Blockchain Workbench 구성 참조
description: Azure Blockchain Workbench 애플리케이션 구성 개요.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4d29d8e86a30f105c4aa50ec9615f8165fa238d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578983"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Azure Blockchain Workbench 구성 참조

Azure Blockchain Workbench 애플리케이션은 구성 메타데이터 및 스마트 계약 코드에 의해 정의된 다자간 워크플로입니다. 구성 메타데이터는 블록체인 애플리케이션의 고급 워크플로 및 상호 작용 모델을 정의합니다. 스마트 계약은 블록체인 애플리케이션의 비즈니스 논리를 정의합니다. Workbench는 구성 및 스마트 계약 코드를 사용하여 블록체인 애플리케이션 사용자 환경을 생성합니다.

구성 메타데이터는 각 블록체인 애플리케이션에 대한 다음 정보를 지정합니다.

* 블록체인 애플리케이션의 이름 및 설명
* 블록체인 애플리케이션 내에서 작업하거나 참여할 수 있는 사용자에 대한 고유한 역할
* 하나 이상의 워크플로가 있어야 합니다. 각 워크플로는 비즈니스 논리의 흐름을 제어하는​상태 시스템의 역할을 합니다. 워크플로는 서로 독립적이거나 또는 상호 작용할 수 있습니다.

각 정의된 워크플로는 다음을 지정합니다.

* 워크플로의 이름과 설명
* 워크플로의 상태.  각 상태는 비즈니스 논리의 제어 흐름에서 한 단계입니다. 
* 다음 상태로 전환하는 작업
* 각 작업을 시작하도록 허용된 사용자 역할
* 코드 파일에서 비즈니스 논리를 나타내는 스마트 계약

## <a name="application"></a>애플리케이션

블록체인 애플리케이션은 구성 메타데이터, 워크플로 및 애플리케이션 내에서 작업하거나 참여할 수 있는 사용자 역할을 포함합니다.

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| ApplicationName | 고유 애플리케이션 이름. 해당 스마트 계약은 적용 가능한 계약 클래스에 대해 동일한 **ApplicationName**을 사용해야 합니다.  | 예 |
| DisplayName | 애플리케이션의 친숙한 표시 이름. | 예 |
| 설명 | 애플리케이션에 대한 설명. | 아닙니다. |
| ApplicationRoles | [ApplicationRoles](#application-roles)의 콜렉션. 애플리케이션 내에서 작업하거나 참여할 수 있는 사용자 역할.  | 예 |
| 워크플로 | [워크플로](#workflows) 컬렉션. 각 워크플로는 비즈니스 논리의 흐름을 제어하는​상태 시스템의 역할을 합니다. | 예 |

예를 들어 [구성 파일 예제](#configuration-file-example)를 참조하세요.

## <a name="workflows"></a>워크플로

애플리케이션의 비즈니스 논리는 작업을 수행하면 비즈니스 논리의 흐름이 한 상태에서 다른 상태로 이동하는 상태 시스템으로 모델링 될 수 있습니다. 워크플로는 이러한 상태 및 작업의 컬렉션입니다. 각 워크플로는 코드 파일에서 비즈니스 논리를 나타내는 하나 이상의 스마트 계약으로 구성됩니다. 실행 가능한 계약은 워크플로의 한 인스턴스.

| 필드 | 설명 | 필수 | 최대 길이 |
|-------|-------------|:--------:|-----------:|
| 이름 | 고유 워크플로 이름. 해당 스마트 계약은 적용 가능한 계약 클래스에 대해 동일한 **Name**을 사용해야 합니다. | 예 | 50 |
| DisplayName | 워크플로의 친숙한 표시 이름. | 예 | 255 |
| 설명 | 워크플로에 대한 설명. | 아닙니다. | 255 |
| 개시 장치 | [ApplicationRoles](#application-roles)의 콜렉션. 워크플로에서 계약을 만들 권한이 있는 사용자에게 할당된 역할. | 예 | |
| StartState | 워크플로의 초기 상태 이름. | 예 | |
| properties | [식별자](#identifiers)의 콜렉션. 사용자 경험 도구에서 오프 체인으로 읽히거나 또는 시각화 될 수 있는 데이터를 나타냅니다. | 예 | |
| 생성자 | 워크플로의 인스턴스를 만들기 위한 입력 매개 변수를 정의합니다. | 예 | |
| Functions | 워크플로에서 실행할 수 있는 [함수](#functions) 컬렉션. | 예 | |
| 상태 | 워크플로 [상태](#states)의 컬렉션. | 예 | |

예를 들어 [구성 파일 예제](#configuration-file-example)를 참조하세요.

## <a name="type"></a>Type

지원되는 데이터 형식.

| Type | 설명 |
|-------|-------------|
| address  | *계약* 또는 *사용자*와 같은 블록체인 주소 형식 |
| array    | 정수, 부울, 돈 또는 시간 형식의 단일 수준 배열입니다. 배열은 정적 또는 동적일 수 있습니다. **ElementType**을 사용하여 배열 내 요소의 데이터 형식을 지정합니다. [예제 구성](#example-configuration-of-type-array)을 참조하세요. |
| bool     | 부울 데이터 형식입니다. |
| 계약 | 계약 형식의 주소입니다. |
| enum     | 명명된 값의 열거형 집합입니다. 열거형 형식을 사용하는 경우 EnumValues 목록도 지정합니다. 태그 값은 255자로 제한됩니다. 유효한 값 문자에는 대/소문자(A-Z, a-z)와 숫자(0-9)가 포함됩니다. [Solidity에서 예제 구성 및 사용](#example-configuration-of-type-enum)을 참조하세요. |
| int      | 정수 데이터 형식입니다. |
| money    | 돈 데이터 형식입니다. |
| state    | 워크플로 상태입니다. |
| 문자열  | 문자열 데이터 형식입니다. 최대 4000자입니다. [예제 구성](#example-configuration-of-type-string)을 참조하세요. |
| 사용자     | 사용자 형식의 주소입니다. |
| 실시간     | 시간 데이터 형식입니다. |
|`[ Application Role Name ]`| 애플리케이션 역할에 지정된 임의의 이름. 사용자를 그 역할 유형에 해당하는 경우로 제한합니다. |

### <a name="example-configuration-of-type-array"></a>배열 형식의 예제 구성

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>배열 형식의 속성 사용

구성에서 배열 형식으로 속성을 정의하는 경우 Solidity에서 배열 형식의 공용 속성을 반환하는 명시적인 가져오기 함수를 포함해야 합니다. 예를 들면 다음과 같습니다.

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>문자열 형식의 예제 구성

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>열거 형식의 예제 구성

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Solidity에서 열거 형식 사용

구성에서 열거형이 정의되면 Solidity에서 열거 형식을 사용할 수 있습니다. 예를 들어, PropertyTypeEnum이라는 열거형을 정의할 수 있습니다.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

Blockchain Workbench에서 문자열 목록이 유효하고 일관된 선언이 되려면 구성 및 스마트 계약 간에 일치해야 합니다.

할당 예제:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

함수 매개 변수 예제: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>생성자

워크플로 인스턴스에 대한 입력 매개 변수를 정의합니다.

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| 매개 변수 | 스마트 계약을 시작하는 데 필요한 [식별자](#identifiers) 컬렉션. | 예 |

### <a name="constructor-example"></a>생성자 예제

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Functions

워크플로에서 실행할 수 있는 함수를 정의합니다.

| 필드 | 설명 | 필수 | 최대 길이 |
|-------|-------------|:--------:|-----------:|
| 이름 | 함수의 고유한 이름. 해당 스마트 계약은 적용 가능한 함수에 대해 동일한 **Name**을 사용해야 합니다. | 예 | 50 |
| DisplayName | 함수의 친숙한 표시 이름. | 예 | 255 |
| 설명 | 함수에 대한 설명 | 아닙니다. | 255 |
| 매개 변수 | 함수의 매개 변수에 해당하는 [식별자](#identifiers)의 컬렉션. | 예 | |

### <a name="functions-example"></a>함수 예제

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>상태

워크플로 내에서 고유 상태의 컬렉션. 각 상태는 비즈니스 논리의 제어 흐름에서 한 단계를 캡처합니다. 

| 필드 | 설명 | 필수 | 최대 길이 |
|-------|-------------|:--------:|-----------:|
| 이름 | 상태의 고유 이름. 해당 스마트 계약은 적용 가능한 상태에 대해 동일한 **Name**을 사용해야 합니다. | 예 | 50 |
| DisplayName | 상태의 친숙한 표시 이름. | 예 | 255 |
| 설명 | 상태에 대한 설명. | 아닙니다. | 255 |
| PercentComplete | Blockchain Workbench 사용자 인터페이스에 표시되는 정수 값은 비즈니스 논리 제어 흐름 내에서의 진행 상황을 보여줍니다. | 예 | |
| Style | 상태가 성공 또는 실패 상태인지 여부를 나타내는 시각적 힌트. 유효한 값은 다음 두 가지입니다. `Success` 또는 `Failure`. | 예 | |
| 전환 | 현재 상태에서 다음 상태 집합으로 [전환](#transitions) 가능한 컬렉션. | 아닙니다. | |

### <a name="states-example"></a>상태 예제

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>전환

다음 상태에 사용 가능한 작업. 하나 이상의 사용자 역할은 각 상태에서 하나의 작업을 수행할 수 있으며, 그 곳에서 작업은 워크플로에서 상태를 또 다른 상태로 전환할 수 있습니다. 

| 필드 | 설명 | 필수 |
|-------|-------------|:--------:|
| AllowedRoles | 전환을 시작하도록 허용된 애플리케이션 역할 목록. 지정된 역할의 모든 사용자가 작업을 수행할 수 있습니다. | 아닙니다. |
| AllowedInstanceRoles | 전환을 시작하도록 허용된 스마트 계약에 참여하거나 지정된 사용자 역할 목록. 인스턴스 역할은 워크플로 내의 **속성**에서 정의됩니다. AllowedInstanceRoles는 스마트 계약의 인스턴스에 참여하는 사용자를 나타냅니다. AllowedInstanceRoles는 계약 인스턴스에서 사용자 역할에 대한 작업을 수행하도록 제한하는 기능을 제공합니다.  예를 들어 AllowedRoles에서 역할을 지정하는 경우 계약(InstanceOwner)을 만든 사용자가 역할 유형(소유자)의 모든 사용자 대신 종료할 수도 있습니다. | 아닙니다. |
| DisplayName | 전환의 친숙한 표시 이름. | 예 |
| 설명 | 전환에 대한 설명. | 아닙니다. |
| 함수 | 전환을 시작하는 함수의 이름. | 예 |
| NextStates | 성공적인 전환 후 잠재적인 다음 상태의 컬렉션. | 예 |

### <a name="transitions-example"></a>전환 예제

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>애플리케이션 역할

애플리케이션 역할은 애플리케이션 내에서 작업하거나 참여하기를 원하는 사용자에게 할당할 수 있는 역할 집합을 정의합니다. 애플리케이션 역할을 사용하여 블록체인 애플리케이션 및 해당 워크플로 내의 작업 및 참여를 제한할 수 있습니다. 

| 필드 | 설명 | 필수 | 최대 길이 |
|-------|-------------|:--------:|-----------:|
| 이름 | 애플리케이션 역할의 고유한 이름입니다. 해당 스마트 계약은 적용 가능한 역할에 대해 동일한 **Name**을 사용해야 합니다. 기본 형식 이름은 예약되어 있습니다. [형식](#type)과 같은 이름으로 애플리케이션 역할의 이름을 지정할 수 없습니다.| 예 | 50 |
| 설명 | 애플리케이션 역할에 대한 설명. | 아닙니다. | 255 |

### <a name="application-roles-example"></a>애플리케이션 역할 예제

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>식별자

식별자는 워크플로 속성, 생성자 및 함수 매개 변수를 설명하는 데 사용되는 정보의 컬렉션을 나타냅니다. 

| 필드 | 설명 | 필수 | 최대 길이 |
|-------|-------------|:--------:|-----------:|
| 이름 | 속성 또는 매개 변수의 고유 이름. 해당 스마트 계약은 적용 가능한 속성 또는 매개 변수에 대해 동일한 **Name**을 사용해야 합니다. | 예 | 50 |
| DisplayName | 속성 또는 매개 변수의 친숙한 표시 이름. | 예 | 255 |
| 설명 | 속성 또는 매개 변수에 대한 설명. | 아닙니다. | 255 |

### <a name="identifiers-example"></a>식별자 예제

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>구성 파일 예제

자산 전송은 고가의 자산을 매수하고 매도하기 위스한 마트 계약 시나리오이며 관리자와 평가자가 필요합니다. 판매자는 자산 전송 스마트 계약을 인스턴스화하여 해당 자산을 나열할 수 있습니다. 구매자는 스마트 계약에 대한 작업을 수행하여 제안하고 다른 당사자는 자산을 검사하거나 평가하는 작업을 수행할 수 있습니다. 자산이 검사되고 평가되었다고 표시되면 계약을 완료하도록 설정하기 전에 구매자 및 판매자는 다시 판매를 확인합니다. 모든 참가자는 프로세스의 각 지점에서 계약이 업데이트되는 대로 계약의 상태를 볼 수 있습니다. 

코드 파일을 비롯한 자세한 내용은 [Azure Blockchain Workbench에 대한 자산 전송 샘플](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)을 참조하세요.

다음 구성 파일은 자산 전송 샘플에 제공됩니다.

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench REST API 참조](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

