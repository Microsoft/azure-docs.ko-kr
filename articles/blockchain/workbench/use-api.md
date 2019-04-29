---
title: Azure Blockchain Workbench REST API 사용
description: Azure Blockchain Workbench REST API를 사용하는 방법에 대한 시나리오
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 7fa72ad62d7d11c795422a203d13a4dd45484c19
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896046"
---
# <a name="using-the-azure-blockchain-workbench-rest-api"></a>Azure Blockchain Workbench REST API 사용

Azure Blockchain Workbench REST API는 개발자 및 정보 근로자에게 블록체인 애플리케이션과 통합하는 다양한 방법을 제공합니다. 이 문서에서는 Workbench REST API의 여러 가지 핵심 메서드를 안내합니다. 예를 들어 개발자가 사용자 지정 블록체인 클라이언트를 만들려고 한다고 가정합니다. 이 블록체인 클라이언트를 통해 로그인된 사용자는 할당된 블록체인 애플리케이션을 보고 상호 작용할 수 있습니다. 이 클라이언트에서는 사용자가 계약 인스턴스를 살펴보고 스마트 계약에 대한 작업을 수행할 수 있습니다. 클라이언트는 로그인한 사용자의 컨텍스트에서 Workbench REST API를 사용하여 다음 작업을 수행합니다.

* 애플리케이션 나열
* 애플리케이션에 대한 워크플로 나열
* 워크플로에 대한 스마트 계약 인스턴스 나열
* 계약에 사용 가능한 작업 나열
* 계약에 대한 작업 실행

시나리오에서 사용되는 예제 블록체인 애플리케이션은 [GitHub에서 다운로드](https://github.com/Azure-Samples/blockchain)할 수 있습니다.

## <a name="list-applications"></a>애플리케이션 나열

사용자가 블록체인 클라이언트에 로그인하면 가장 먼저 해당 사용자에 대한 모든 Blockchain Workbench 애플리케이션이 검색됩니다. 이 시나리오에서 사용자는 다음 두 애플리케이션에 액세스할 수 있습니다.

1. [Asset Transfer](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
2. [Refrigerated Transportation](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

[Applications GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/applicationsget) 사용:

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

사용자가 Blockchain Workbench에서 액세스할 수 있는 모든 블록체인 애플리케이션이 응답으로 나열됩니다. Blockchain Workbench 관리자는 모든 블록체인 애플리케이션을 얻게 됩니다. 비 Workbench 관리자는 연결된 애플리케이션 역할 또는 연결된 스마트 계약 인스턴스 역할이 하나 이상 있는 모든 블록체인을 얻게 됩니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>애플리케이션에 대한 워크플로 나열

사용자가 해당 블록체인 애플리케이션(예제: **Asset Transfer**)을 선택하면 블록체인 클라이언트는 특정 블록체인 애플리케이션의 모든 워크플로를 검색합니다. 그 후 사용자가 해당 워크플로를 선택하면 워크플로에 대한 모든 스마트 계약 인스턴스가 표시됩니다. 각 블록체인 애플리케이션에는 하나 이상의 워크플로가 있고 각 워크플로에는 제로 또는 스마트 계약 인스턴스가 있습니다. 하나의 워크플로만 있는 블록체인 클라이언트 애플리케이션의 경우 사용자가 적절한 워크플로 선택할 수 있도록 하는 사용자 경험 흐름을 건너뛰는 것이 좋습니다. 이 예의 **Asset Transfer**는 워크플로가 하나밖에 없고, 그 이름도 **Asset Transfer**입니다.

[Applications Workflows GET API](https://docs.microsoft.com/rest/api/azure-blockchain-workbench/applications/workflowsget) 사용:

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

사용자가 Blockchain Workbench에서 액세스할 수 있는 지정된 블록체인 애플리케이션의 모든 워크플로가 응답으로 나열됩니다. Blockchain Workbench 관리자는 모든 블록체인 워크플로를 얻게 됩니다. 비 Workbench 관리자는 연결된 애플리케이션 역할 또는 연결된 스마트 계약 인스턴스 역할이 하나 이상 있는 모든 워크플로를 얻게 됩니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>워크플로에 대한 스마트 계약 인스턴스 나열

사용자가 해당 워크플로(이 예에서는 **Asset Transfer**)를 선택하면 블록체인 클라이언트는 지정된 워크플로에 대한 모든 스마트 계약 인스턴스를 검색합니다. 이 정보를 사용하여 워크플로에 대한 모든 스마트 계약 인스턴스를 표시할 수 있습니다. 또는 사용자가 표시된 스마트 계약 인스턴스 중 하나를 심층 분석하도록 허용할 수 있습니다. 이 예제에서는 사용자가 조치를 취할 스마트 계약 인스턴스 중 하나와 상호 작용하려 한다고 가정해 봅시다.

[Contracts GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) 사용:

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

지정된 워크플로의 모든 스마트 계약 인스턴스가 응답으로 나열됩니다. Workbench 관리자는 모든 스마트 계약 인스턴스를 얻게 됩니다. 비 Workbench 관리자는 연결된 애플리케이션 역할 또는 연결된 스마트 계약 인스턴스 역할이 하나 이상 있는 모든 스마트 계약 인스턴스를 얻게 됩니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>계약에 사용 가능한 작업 나열

사용자가 계약 중 하나를 자세히 살펴보기로 결정하면 블록체인 클라이언트는 계약의 상태를 고려하여 사용 가능한 사용자 작업을 표시할 수 있습니다. 이 예제의 사용자는 자신이 만든 새 스마트 계약에 사용 가능한 작업을 살펴봅니다.

* 수정 합니다. 사용자를가 자산의 가격과 설명을 수정할 수 있습니다.
* 종료: 사용자를가 자산의 계약 종료 수 있습니다.

[Contract Action GET API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) 사용:

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

지정된 스마트 계약 인스턴스의 현재 상태를 고려할 때 사용자가 수행할 수 있는 모든 작업이 응답으로 나열됩니다. 사용자는 연결된 애플리케이션 역할이 있거나 지정된 스마트 계약 인스턴스의 현재 상태에 대한 스마트 계약 인스턴스 역할과 연결된 경우 적용 가능한 모든 작업을 얻게 됩니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>계약에 대한 작업 실행

그러면 사용자는 지정된 스마트 계약 인스턴스에 대한 조치를 취할 것인지 결정할 수 있습니다. 이 예제에서는 사용자가 자산의 설명과 가격을 다음 작업과 같이 수정하려 하는 시나리오를 고려해 보세요.

* 설명: "내 업데이트 car"
* 가격: 54321

[Contract Action POST API](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) 사용:

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

사용자는 지정된 스마트 계약 인스턴스 및 사용자의 연결된 애플리케이션 역할 또는 스마트 계약 인스턴스 역할의 상태를 고려한 작업만 실행할 수 있습니다. 게시가 성공하면 응답 본문 없이 HTTP 200 OK 응답이 반환됩니다.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench REST API 참조](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)