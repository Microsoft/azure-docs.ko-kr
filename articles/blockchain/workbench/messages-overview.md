---
title: Azure Blockchain Workbench 메시지 통합 개요
description: Azure Blockchain Workbench의 메시지 사용에 대해 간략하게 설명합니다.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 02/21/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 860c00b876427af7395e3c04e0626131c27aca67
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896424"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Azure Blockchain Workbench 메시지 통합 개요

Azure Blockchain Workbench는 REST API를 제공할 뿐만 아니라 메시징 기반 통합도 제공합니다. 이 Workbench는 Azure Event Grid를 통해 원장 중심 이벤트를 게시하여 다운스트림 소비자가 데이터를 수집하거나 이러한 이벤트를 기준으로 작업을 수행할 수 있도록 합니다. 신뢰할 수 있는 메시징을 필요로 하는 클라이언트를 위해, Azure Blockchain Workbench는 Azure Service Bus 엔드포인트에 메시지를 배달합니다.

## <a name="input-apis"></a>입력 API

외부 시스템에서 트랜잭션을 시작하여 사용자 및 계약을 만들고 계약을 업데이트하려는 경우 메시징 입력 API를 사용하여 원장에서 트랜잭션을 수행할 수 있습니다. 입력 API를 설명하는 샘플은 [메시징 통합 샘플](https://aka.ms/blockchain-workbench-integration-sample)을 참조하세요.

현재 사용 가능한 입력 API는 다음과 같습니다.

### <a name="create-user"></a>사용자 만들기

새 사용자를 만듭니다.

요청에는 다음 필드가 필요합니다.

| **Name**             | **설명**                                      |
|----------------------|------------------------------------------------------|
| requestId            | 클라이언트 제공 GUID입니다.                                |
| firstname            | 사용자의 이름입니다.                              |
| Lastname             | 사용자의 성입니다.                               |
| emailAddress         | 사용자의 이메일 주소입니다.                           |
| externalId           | 사용자의 Azure AD 개체 ID입니다.                      |
| connectionId         | 블록체인 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion | 메시징 스키마 버전입니다.                            |
| messageName          | **CreateUserRequest**                               |

예제:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench는 다음 필드가 포함된 응답을 반환합니다.

| **Name**              | **설명**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | 클라이언트 제공 GUID입니다. |
| userId                | 생성된 사용자의 ID입니다. |
| userChainIdentifier   | 블록체인 네트워크에서 생성된 사용자의 주소입니다. Ethereum에서 주소는 사용자의 **체인 내** 주소입니다. |
| connectionId          | 블록체인 연결에 대한 고유 식별자입니다.|
| messageSchemaVersion  | 메시징 스키마 버전입니다. |
| messageName           | **CreateUserUpdate** |
| status                | 사용자 생성 요청의 상태입니다.  성공하면 값은 **Success**입니다. 실패 시 값은 **Failure**입니다.     |
| additionalInformation | 상태에 따라 제공되는 추가 정보입니다. |

Blockchain Workbench의 성공적인 **사용자 만들기** 응답 예:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

요청이 성공하지 못한 경우 오류에 대한 세부 정보가 추가 정보에 포함됩니다.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>계약 만들기

새 계약을 만듭니다.

요청에는 다음 필드가 필요합니다.

| **Name**             | **설명**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | 클라이언트 제공 GUID입니다. |
| userChainIdentifier  | 블록체인 네트워크에서 생성된 사용자의 주소입니다. Ethereum에서 이 주소는 **체인 내** 주소입니다. |
| applicationName      | 애플리케이션의 이름입니다. |
| 버전              | 애플리케이션의 버전입니다. 여러 버전의 애플리케이션을 사용하도록 설정한 경우 필요합니다. 그렇지 않은 경우 version은 선택 사항입니다. 애플리케이션 버전 관리에 대한 자세한 내용은 [Azure Blockchain Workbench 애플리케이션 버전 관리](version-app.md)를 참조하세요. |
| workflowName         | 워크플로의 이름입니다. |
| 매개 변수           | 계약 생성을 위한 매개 변수 입력입니다. |
| connectionId         | 블록체인 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion | 메시징 스키마 버전입니다. |
| messageName          | **CreateContractRequest** |

예제:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench는 다음 필드가 포함된 응답을 반환합니다.

| **Name**                 | **설명**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | 클라이언트 제공 GUID입니다.                                                             |
| contractId               | Azure Blockchain Workbench 내 계약에 대한 고유 식별자입니다. |
| contractLedgerIdentifier | 원장의 계약 주소입니다.                                            |
| connectionId             | 블록체인 연결에 대한 고유 식별자입니다.                               |
| messageSchemaVersion     | 메시징 스키마 버전입니다.                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | 계약 생성 요청의 상태입니다.  가능한 값은 다음과 같습니다. **제출**, **커밋됨**, **실패**  |
| additionalInformation    | 상태에 따라 제공되는 추가 정보입니다.                              |

Blockchain Workbench의 제출된 **계약 만들기** 응답 예:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted"
    "additionalInformation": { }
}
```

Blockchain Workbench의 커밋된 **계약 만들기** 응답 예:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

요청이 성공하지 못한 경우 오류에 대한 세부 정보가 추가 정보에 포함됩니다.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>계약 작업 만들기

새 계약 작업을 만듭니다.

요청에는 다음 필드가 필요합니다.

| **Name**                 | **설명**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | 클라이언트 제공 GUID입니다. |
| userChainIdentifier      | 블록체인 네트워크에서 생성된 사용자의 주소입니다. Ethereum에서 이 주소는 **체인 내** 주소입니다. |
| contractLedgerIdentifier | 원장의 계약 주소입니다. |
| 버전                  | 애플리케이션의 버전입니다. 여러 버전의 애플리케이션을 사용하도록 설정한 경우 필요합니다. 그렇지 않은 경우 version은 선택 사항입니다. 애플리케이션 버전 관리에 대한 자세한 내용은 [Azure Blockchain Workbench 애플리케이션 버전 관리](version-app.md)를 참조하세요. |
| workflowFunctionName     | 워크플로 함수의 이름입니다. |
| 매개 변수               | 계약 생성을 위한 매개 변수 입력입니다. |
| connectionId             | 블록체인 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion     | 메시징 스키마 버전입니다. |
| messageName              | **CreateContractActionRequest** |

예제:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench는 다음 필드가 포함된 응답을 반환합니다.

| **Name**              | **설명**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | 클라이언트 제공 GUID입니다.|
| contractId            | Azure Blockchain Workbench 내 계약에 대한 고유 식별자입니다. |
| connectionId          | 블록체인 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion  | 메시징 스키마 버전입니다. |
| messageName           | **CreateContractActionUpdate** |
| status                | 계약 작업 요청의 상태입니다. 가능한 값은 다음과 같습니다. **제출**, **커밋됨**, **실패**                         |
| additionalInformation | 상태에 따라 제공되는 추가 정보입니다. |

Blockchain Workbench의 제출된 **계약 작업 만들기** 응답 예:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Blockchain Workbench의 커밋된 **계약 작업 만들기** 응답 예:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed"
    "additionalInformation": { }
}
```

요청이 성공하지 못한 경우 오류에 대한 세부 정보가 추가 정보에 포함됩니다.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure"
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>입력된 API 오류 코드 및 메시지

**오류 코드 4000: 잘못된 요청 오류**
- 잘못된 connectionId
- CreateUserRequest 역직렬화 실패
- CreateContractRequest 역직렬화 실패
- CreateContractActionRequest 역직렬화 실패
- 애플리케이션 {애플리케이션 이름으로 식별됨} 없음
- 애플리케이션 {애플리케이션 이름으로 식별됨}에 워크플로가 없음
- UserChainIdentifier가 없음
- 계약 {원장 식별자로 식별됨} 없음
- 계약 {원장 식별자로 식별됨}에 함수 {워크플로 함수 이름} 없음
- UserChainIdentifier가 없음

**오류 코드 4090: 충돌 오류**
- 사용자가 이미 존재함
- 계약이 이미 존재함
- 계약 작업이 이미 존재함

**오류 코드 5000: 내부 서버 오류**
- 예외 메시지

## <a name="event-notifications"></a>이벤트 알림

이벤트 알림은 Blockchain Workbench에서 발생하고 Workbench가 연결된 블록체인 네트워크를 사용자 및 다운스트림 시스템에 알리는 데 사용될 수 있습니다. 이벤트 알림을 코드에서 직접 사용하거나, 다운스트림 시스템으로의 데이터 흐름을 트리거하는 Logic Apps 및 Flow와 같은 도구에서 사용할 수도 있습니다.

수신될 수 있는 다양한 메시지의 세부 정보에 대해서는 [알림 메시지 참조](#notification-message-reference)를 참조하세요.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Azure Functions에서 Event Grid 이벤트 사용

사용자가 Event Grid를 사용하여 Blockchain Workbench에서 발생하는 이벤트에 대한 알림을 받기 원할 경우 Azure Functions를 사용하여 Event Grid의 이벤트를 사용할 수 있습니다.

1. Azure Portal에서 **Azure Function App**을 만듭니다.
2. 새 함수를 만듭니다.
3. Event Grid에 대한 템플릿을 찾습니다. 메시지를 읽기 위한 기본 템플릿 코드가 표시됩니다. 필요에 따라 코드를 수정합니다.
4. 함수를 저장합니다. 
5. Blockchain Workbench의 리소스 그룹에서 Event Grid를 선택합니다.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Logic Apps에서 Event Grid 이벤트 사용

1. Azure Portal에서 새 **Azure 논리 앱**을 만듭니다.
2. 포털에서 Azure 논리 앱을 열면 트리거를 선택하라는 메시지가 표시됩니다. **Azure Event Grid -- 리소스 이벤트가 발생할 때**를 선택합니다.
3. 워크플로 디자이너가 표시되면 로그인하라는 메시지가 표시됩니다.
4. 구독을 선택합니다. 리소스로 **Microsoft.EventGrid.Topics**를 지정합니다. Azure Blockchain Workbench 리소스 그룹의 리소스 이름에서 **리소스 이름**을 선택합니다.
5. Blockchain Workbench의 리소스 그룹에서 Event Grid를 선택합니다.

## <a name="using-service-bus-topics-for-notifications"></a>알림에 Service Bus 토픽 사용

Service Bus 토픽을 사용하여 Blockchain Workbench에서 발생하는 이벤트를 사용자에게 알릴 수 있습니다. 

1. Workbench의 리소스 그룹 내에서 Service Bus로 이동합니다.
2. **토픽**을 선택합니다.
3. **송신 토픽**을 선택합니다.
4. 이 토픽에 대한 새 구독을 만듭니다. 해당 키를 가져옵니다.
5. 이 구독에서 이벤트를 구독하는 프로그램을 만듭니다.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Logic Apps에서 Service Bus 메시지 사용

1. Azure Portal에서 새 **Azure 논리 앱**을 만듭니다.
2. 포털에서 Azure 논리 앱을 열면 트리거를 선택하라는 메시지가 표시됩니다. 검색 상자에 **Service Bus**를 입력하고 Service Bus와 수행하려는 상호 작용 유형에 적합한 트리거를 선택합니다. 예를 들어 **Service Bus -- 메시지가 항목 구독에 수신되는 경우(자동 완성)** 를 선택할 수 있습니다.
3. 워크플로 디자이너가 표시되면 Service Bus에 대한 연결 정보를 지정합니다.
4. 구독을 선택하고 **워크벤치-외부** 토픽을 지정합니다.
5. 이 트리거에서 메시지를 활용하는 애플리케이션에 대한 논리를 개발합니다.

## <a name="notification-message-reference"></a>알림 메시지 참조

에 따라 합니다 **messageName**, 알림 메시지는 다음 메시지 유형 중 하나가 있어야 합니다.

### <a name="block-message"></a>블록 메시지

개별 블록에 대한 정보를 포함합니다. *BlockMessage*는 블록 수준 정보가 있는 섹션 및 트랜잭션 정보가 있는 섹션을 포함합니다.

| 이름 | 설명 |
|------|-------------|
| block | [블록 정보](#block-information)를 포함합니다. |
| 트랜잭션 | 블록에 대한 컬렉션 [트랜잭션 정보](#transaction-information)를 포함합니다. |
| connectionId | 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion | 메시징 스키마 버전입니다. |
| messageName | **BlockMessage** |
| additionalInformation | 제공된 추가 정보 |

#### <a name="block-information"></a>블록 정보

| 이름              | 설명 |
|-------------------|-------------|
| blockId           | Azure Blockchain Workbench 내 블록에 대한 고유 식별자입니다. |
| blockNumber       | 원장의 블록에 대한 고유 식별자입니다. |
| blockHash         | 블록의 해시입니다. |
| previousBlockHash | 이전 블록의 해시 |
| blockTimestamp    | 블록의 타임스탬프입니다. |

#### <a name="transaction-information"></a>트랜잭션 정보

| 이름               | 설명 |
|--------------------|-------------|
| transactionId      | Azure Blockchain Workbench 내 트랜잭션에 대한 고유 식별자입니다. |
| transactionHash    | 원장에 있는 트랜잭션의 해시입니다. |
| from               | 트랜잭션 원본에 대한 원장의 고유 식별자 |
| to                 | 트랜잭션 대상에 대한 원장의 고유 식별자 |
| provisioningStatus | 트랜잭션에 대한 프로비전 프로세스의 현재 상태를 식별합니다. 가능한 값은 다음과 같습니다. </br>0 – API에 의해 데이터베이스에 트랜잭션이 생성됨</br>1 – 트랜잭션이 원장에 전송됨</br>2 – 트랜잭션이 원장에 성공적으로 커밋됨</br>3 또는 4 - 트랜잭션이 원장에 커밋하는 데 실패함</br>5 – 트랜잭션이 원장에 성공적으로 커밋됨 |

Blockchain Workbench에서 *BlockMessage*의 예제:

``` json
{
    "block": {
        "blockId": 123
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>계약 메시지

계약에 대한 정보가 포함됩니다. 메시지는 블록 계약 속성이 있는 섹션 및 트랜잭션 정보가 있는 섹션을 포함합니다. 특정 블록에 대한 계약을 수정한 모든 트랜잭션은 트랜잭션 섹션에 포함됩니다.

| 이름 | 설명 |
|------|-------------|
| blockId | Azure Blockchain Workbench 내 블록에 대한 고유 식별자입니다. |
| blockHash | 블록의 해시 |
| modifyingTransactions | 계약을 [수정한 트랜잭션](#modifying-transaction-information) |
| contractId | Azure Blockchain Workbench 내 계약에 대한 고유 식별자입니다. |
| contractLedgerIdentifier | 원장에서 계약에 대한 고유 식별자입니다. |
| contractProperties | [계약의 속성](#contract-properties) |
| isNewContract | 이 계약이 새로 만들어졌는지 여부를 나타냅니다. 가능한 값은 true: 이 계약은 만들어진 새 계약입니다. false: 이 계약은 계약 업데이트입니다. |
| connectionId | 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion | 메시징 스키마 버전입니다. |
| messageName | **ContractMessage** |
| additionalInformation | 제공된 추가 정보 |

#### <a name="modifying-transaction-information"></a>트랜잭션 정보 수정

| 이름               | 설명 |
|--------------------|-------------|
| transactionId | Azure Blockchain Workbench 내 트랜잭션에 대한 고유 식별자입니다. |
| transactionHash | 원장에 있는 트랜잭션의 해시입니다. |
| from | 트랜잭션 원본에 대한 원장의 고유 식별자 |
| to | 트랜잭션 대상에 대한 원장의 고유 식별자 |

#### <a name="contract-properties"></a>계약 속성

| 이름               | 설명 |
|--------------------|-------------|
| workflowPropertyId | Azure Blockchain Workbench 내 워크플로 속성에 대한 고유 식별자입니다. |
| 이름 | 워크플로 속성의 이름입니다. |
| 값 | 워크플로 속성의 값입니다. |

Blockchain Workbench에서 *ContractMessage*의 예제:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>이벤트 메시지: 계약 함수 호출

계약 함수가 호출되었을 때 함수 이름, 매개 변수 입력 및 함수의 호출자와 같은 정보를 포함합니다.

| 이름 | 설명 |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| caller                      | [호출자 정보](#caller-information) |
| contractId                  | Azure Blockchain Workbench 내 계약에 대한 고유 식별자입니다. |
| contractLedgerIdentifier    | 원장에서 계약에 대한 고유 식별자입니다. |
| functionName                | 함수의 이름 |
| 매개 변수                  | [매개 변수 정보](#parameter-information) |
| 트랜잭션                 | 트랜잭션 정보 |
| inTransactionSequenceNumber | 블록에서 트랜잭션의 시퀀스 번호 |
| connectionId                | 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion        | 메시징 스키마 버전입니다. |
| messageName                 | **EventMessage** |
| additionalInformation       | 제공된 추가 정보 |

#### <a name="caller-information"></a>호출자 정보

| 이름 | 설명 |
|------|-------------|
| 형식 | 사용자 또는 계약과 같은 호출자의 형식 |
| id | Azure Blockchain Workbench 내 호출자에 대한 고유 식별자입니다. |
| ledgerIdentifier | 원장에서 호출자에 대한 고유 식별자입니다. |

#### <a name="parameter-information"></a>매개 변수 정보

| 이름 | 설명 |
|------|-------------|
| 이름 | 매개 변수 이름 |
| 값 | 매개 변수 값 |

#### <a name="event-message-transaction-information"></a>이벤트 메시지 트랜잭션 정보

| 이름               | 설명 |
|--------------------|-------------|
| transactionId      | Azure Blockchain Workbench 내 트랜잭션에 대한 고유 식별자입니다. |
| transactionHash    | 원장에 있는 트랜잭션의 해시입니다. |
| from               | 트랜잭션 원본에 대한 원장의 고유 식별자 |
| to                 | 트랜잭션 대상에 대한 원장의 고유 식별자 |

Blockchain Workbench에서 *EventMessage ContractFunctionInvocation*의 예제:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>이벤트 메시지: 애플리케이션 수집

애플리케이션이 Workbench에 업로드될 때 업로드된 애플리케이션의 이름 및 버전과 같은 정보를 포함합니다.

| 이름 | 설명 |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Azure Blockchain Workbench 내 애플리케이션에 대한 고유 식별자입니다. |
| applicationName | 애플리케이션 이름 |
| applicationDisplayName | 애플리케이션 표시 이름 |
| applicationVersion | 애플리케이션 버전 |
| applicationDefinitionLocation | 애플리케이션 구성 파일이 위치한 URL |
| contractCodes | 애플리케이션에 대한 [계약 코드](#contract-code-information)의 컬렉션 |
| applicationRoles | 애플리케이션에 대한 [애플리케이션 역할](#application-role-information)의 컬렉션 |
| applicationWorkflows | 애플리케이션에 대한 [애플리케이션 워크플로](#application-workflow-information)의 컬렉션 |
| connectionId | 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion | 메시징 스키마 버전입니다. |
| messageName | **EventMessage** |
| additionalInformation | 여기에 제공된 추가 정보는 애플리케이션 워크플로 상태 및 전환 정보를 포함합니다. |

#### <a name="contract-code-information"></a>계약 코드 정보

| 이름 | 설명 |
|------|-------------|
| id | Azure Blockchain Workbench 내 계약 코드 파일에 대한 고유 식별자입니다. |
| ledgerId | Azure Blockchain Workbench 내 원장에 대한 고유 식별자입니다. |
| location | 계약 코드 파일이 위치한 URL |

#### <a name="application-role-information"></a>애플리케이션 역할 정보

| 이름 | 설명 |
|------|-------------|
| id | Azure Blockchain Workbench 내 애플리케이션 역할에 대한 고유 식별자입니다. |
| 이름 | 애플리케이션 역할의 이름입니다. |

#### <a name="application-workflow-information"></a>애플리케이션 워크플로 정보

| 이름 | 설명 |
|------|-------------|
| id | Azure Blockchain Workbench 내 애플리케이션 워크플로에 대한 고유 식별자입니다. |
| 이름 | 애플리케이션 워크플로 이름 |
| displayName | 애플리케이션 워크플로 표시 이름 |
| functions | 컬렉션의 [응용 프로그램 워크플로에 대 한 함수](#workflow-function-information)|
| states | [애플리케이션 워크플로에 대한 상태](#workflow-state-information)의 컬렉션 |
| properties | 애플리케이션 [워크플로 속성 정보](#workflow-property-information) |

##### <a name="workflow-function-information"></a>워크플로 함수 정보

| 이름 | 설명 |
|------|-------------|
| id | Azure Blockchain Workbench 내 애플리케이션 워크플로 함수에 대한 고유 식별자입니다. |
| 이름 | 함수 이름 |
| 매개 변수 | 함수의 매개 변수 |

##### <a name="workflow-state-information"></a>워크플로 상태 정보

| 이름 | 설명 |
|------|-------------|
| 이름 | 상태 이름 |
| displayName | 상태 표시 이름 |
| style | 상태 스타일(성공 또는 실패) |

##### <a name="workflow-property-information"></a>워크플로 속성 정보

| 이름 | 설명 |
|------|-------------|
| id | Azure Blockchain Workbench 내 애플리케이션 워크플로 속성에 대한 고유 식별자입니다. |
| 이름 | 속성 이름 |
| 형식 | 속성 형식 |

Blockchain Workbench에서 *EventMessage ApplicationIngestion*의 예제:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationDefinitionLocation": "http://url"
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                                "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                                "name": "int"
                     }
                }
            ]
        }
    ]
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept"
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>이벤트 메시지: 역할 할당

사용자가 Workbench의 역할에 할당될 때 역할 할당을 수행한 사용자와 역할의 이름 및 해당 애플리케이션과 같은 정보를 포함합니다.

| 이름 | 설명 |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Azure Blockchain Workbench 내 애플리케이션에 대한 고유 식별자입니다. |
| applicationName | 애플리케이션 이름 |
| applicationDisplayName | 애플리케이션 표시 이름 |
| applicationVersion | 애플리케이션 버전 |
| applicationRole        | [애플리케이션 역할](#roleassignment-application-role)에 대한 정보 |
| assigner               | [할당자](#roleassignment-assigner)에 대한 정보 |
| assignee               | [담당자](#roleassignment-assignee)에 대한 정보 |
| connectionId           | 연결에 대한 고유 식별자입니다. |
| messageSchemaVersion   | 메시징 스키마 버전입니다. |
| messageName            | **EventMessage** |
| additionalInformation  | 제공된 추가 정보 |

#### <a name="roleassignment-application-role"></a>RoleAssignment 애플리케이션 역할

| 이름 | 설명 |
|------|-------------|
| id | Azure Blockchain Workbench 내 애플리케이션 역할에 대한 고유 식별자입니다. |
| 이름 | 애플리케이션 역할의 이름입니다. |

#### <a name="roleassignment-assigner"></a>RoleAssignment 할당자

| 이름 | 설명 |
|------|-------------|
| id | Azure Blockchain Workbench 내 사용자의 고유 식별자입니다. |
| 형식 | 할당자의 형식 |
| chainIdentifier | 원장에서 사용자의 고유 식별자입니다. |

#### <a name="roleassignment-assignee"></a>RoleAssignment 담당자

| 이름 | 설명 |
|------|-------------|
| id | Azure Blockchain Workbench 내 사용자의 고유 식별자입니다. |
| 형식 | 담당자의 형식 |
| chainIdentifier | 원장에서 사용자의 고유 식별자입니다. |

Blockchain Workbench에서 *EventMessage RoleAssignment*의 예제:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>다음 단계

- [스마트 계약 통합 패턴](integration-patterns.md)
