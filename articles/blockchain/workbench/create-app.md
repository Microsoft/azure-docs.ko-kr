---
title: 블록체인 애플리케이션 만들기 - Azure Blockchain Workbench
description: Azure Blockchain Workbench 미리 보기용 블록체인 애플리케이션을 만드는 방법에 대한 자습서입니다.
ms.date: 08/24/2020
ms.topic: tutorial
ms.reviewer: ravastra
ms.openlocfilehash: add790a069d2e0ea66d84bbd632825cf9331fd38
ms.sourcegitcommit: 9c3cfbe2bee467d0e6966c2bfdeddbe039cad029
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2020
ms.locfileid: "88784031"
---
# <a name="tutorial-create-a-blockchain-application-for-azure-blockchain-workbench"></a>자습서: Azure Blockchain Workbench용 블록체인 애플리케이션 만들기

Azure Blockchain Workbench를 사용하여 구성 및 스마트 계약 코드로 정의된 다자간 워크플로를 나타내는 블록체인 애플리케이션을 만들 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 블록체인 애플리케이션 구성
> * 스마트 계약 코드 파일 만들기
> * Blockchain Workbench에 블록체인 애플리케이션 추가
> * 블록체인 애플리케이션에 구성원 추가

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Blockchain Workbench 배포. 배포에 대한 자세한 내용은 [Azure Blockchain Workbench 배포](deploy.md)를 참조하세요.
* Blockchain Workbench와 연결된 테넌트의 Azure Active Directory 사용자. 자세한 내용은 [Azure Blockchain Workbench에서 Azure AD 사용자 추가](manage-users.md#add-azure-ad-users)를 참조하세요.
* Blockchain Workbench 관리자 계정. 자세한 내용은 [Azure Blockchain Workbench에서 Blockchain Workbench 관리자](manage-users.md#manage-blockchain-workbench-administrators) 추가를 참조하세요.

## <a name="hello-blockchain"></a>Hello, Blockchain!

요청자가 요청을 보내고 응답자가 요청에 대한 응답을 보내는 기본 애플리케이션을 빌드해 봅시다.
예를 들어 요청이 "안녕하세요?"라고 하면 응답은 "좋습니다!"라고 할 수 있습니다. 요청과 응답 모두 기본 블록체인에 기록됩니다.

애플리케이션 파일을 만드는 단계를 따르거나 [GitHub에서 샘플을 다운로드](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/hello-blockchain)할 수 있습니다.

## <a name="configuration-file"></a>구성 파일

구성 메타데이터는 블록체인 애플리케이션의 고급 워크플로 및 상호 작용 모델을 정의합니다. 구성 메타데이터는 블록체인 애플리케이션의 워크플로 단계 및 상호 작용 모델을 나타냅니다. 구성 파일 콘텐츠에 대한 자세한 내용은 [Azure Blockchain 워크플로 구성 참조](configuration.md)를 참조하세요.

1. 자주 사용하는 편집기에서 `HelloBlockchain.json`이라는 파일을 만듭니다.
2. 다음 JSON을 추가하여 블록체인 애플리케이션의 구성을 정의합니다.

    ``` json
    {
      "ApplicationName": "HelloBlockchain",
      "DisplayName": "Hello, Blockchain!",
      "Description": "A simple application to send request and get response",
      "ApplicationRoles": [
        {
          "Name": "Requestor",
          "Description": "A person sending a request."
        },
        {
          "Name": "Responder",
          "Description": "A person responding to a request"
        }
      ],
      "Workflows": [
        {
          "Name": "HelloBlockchain",
          "DisplayName": "Request Response",
          "Description": "A simple workflow to send a request and receive a response.",
          "Initiators": [ "Requestor" ],
          "StartState": "Request",
          "Properties": [
            {
              "Name": "State",
              "DisplayName": "State",
              "Description": "Holds the state of the contract.",
              "Type": {
                "Name": "state"
              }
            },
            {
              "Name": "Requestor",
              "DisplayName": "Requestor",
              "Description": "A person sending a request.",
              "Type": {
                "Name": "Requestor"
              }
            },
            {
              "Name": "Responder",
              "DisplayName": "Responder",
              "Description": "A person sending a response.",
              "Type": {
                "Name": "Responder"
              }
            },
            {
              "Name": "RequestMessage",
              "DisplayName": "Request Message",
              "Description": "A request message.",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "ResponseMessage",
              "DisplayName": "Response Message",
              "Description": "A response message.",
              "Type": {
                "Name": "string"
              }
            }
          ],
          "Constructor": {
            "Parameters": [
              {
                "Name": "message",
                "Description": "...",
                "DisplayName": "Request Message",
                "Type": {
                  "Name": "string"
                }
              }
            ]
          },
          "Functions": [
            {
              "Name": "SendRequest",
              "DisplayName": "Request",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "requestMessage",
                  "Description": "...",
                  "DisplayName": "Request Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            },
            {
              "Name": "SendResponse",
              "DisplayName": "Response",
              "Description": "...",
              "Parameters": [
                {
                  "Name": "responseMessage",
                  "Description": "...",
                  "DisplayName": "Response Message",
                  "Type": {
                    "Name": "string"
                  }
                }
              ]
            }
          ],
          "States": [
            {
              "Name": "Request",
              "DisplayName": "Request",
              "Description": "...",
              "PercentComplete": 50,
              "Value": 0,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": ["Responder"],
                  "AllowedInstanceRoles": [],
                  "Description": "...",
                  "Function": "SendResponse",
                  "NextStates": [ "Respond" ],
                  "DisplayName": "Send Response"
                }
              ]
            },
            {
              "Name": "Respond",
              "DisplayName": "Respond",
              "Description": "...",
              "PercentComplete": 90,
              "Value": 1,
              "Style": "Success",
              "Transitions": [
                {
                  "AllowedRoles": [],
                  "AllowedInstanceRoles": ["Requestor"],
                  "Description": "...",
                  "Function": "SendRequest",
                  "NextStates": [ "Request" ],
                  "DisplayName": "Send Request"
                }
              ]
            }
          ]
        }
      ]
    }
    ```

3. `HelloBlockchain.json` 파일을 저장합니다.

구성 파일에는 여러 섹션이 있습니다. 각 섹션에 대한 세부 정보는 다음과 같습니다.

### <a name="application-metadata"></a>애플리케이션 메타데이터

구성 파일의 시작 부분에는 애플리케이션 이름과 설명을 포함한 애플리케이션에 대한 정보가 들어 있습니다.

### <a name="application-roles"></a>애플리케이션 역할

애플리케이션 역할 섹션에서는 블록체인 애플리케이션에서 작동하거나 참여할 수 있는 사용자 역할을 정의합니다. 기능에 따라 고유한 역할 세트를 정의합니다. 요청-응답 시나리오에서는 요청을 생성하는 엔터티로서의 요청자와 응답을 생성하는 엔터티로서의 응답자 기능이 구분됩니다.

### <a name="workflows"></a>워크플로

워크플로는 계약에서 하나 이상의 단계와 작업을 정의합니다. 요청-응답 시나리오에서 워크플로의 첫 번째 단계(상태)는 요청자(역할)가 요청(함수)을 보내기 위한 작업(전환)을 수행합니다. 다음 단계(상태)는 응답자(역할)가 응답(함수)을 보내기 위한 작업(전환)을 수행합니다. 애플리케이션의 워크플로에는 계약의 흐름을 설명하는 데 필요한 속성, 함수 및 상태가 포함될 수 있습니다.

## <a name="smart-contract-code-file"></a>스마트 계약 코드 파일

스마트 계약은 블록체인 애플리케이션의 비즈니스 논리를 나타냅니다. 현재 Blockchain Workbench는 블록체인 원장의 Ethereum을 지원합니다. Ethereum은 스마트 계약을 위한 자체 시행 비즈니스 논리를 작성하기 위한 프로그래밍 언어로 [Solidity](https://solidity.readthedocs.io)를 사용합니다.

Solidity의 스마트 계약은 개체 지향 언어의 클래스와 유사합니다. 각 계약에는 스마트 계약의 단계와 작업을 구현하기 위한 상태와 함수가 포함되어 있습니다.

자주 사용하는 편집기에서 `HelloBlockchain.sol`이라는 파일을 만듭니다.

### <a name="version-pragma"></a>버전 pragma

대상으로 하고 있는 Solidity의 버전을 나타내는 것이 좋습니다. 버전을 지정하면 향후 Solidity 버전과 호환되지 않는 것을 방지할 수 있습니다.

`HelloBlockchain.sol` 스마트 계약 코드 파일의 맨 위에 다음 버전 pragma를 추가합니다.

``` solidity
pragma solidity >=0.4.25 <0.6.0;
```

### <a name="configuration-and-smart-contract-code-relationship"></a>구성 및 스마트 계약 코드 관계

Blockchain Workbench는 구성 파일과 스마트 계약 코드 파일을 사용하여 블록체인 애플리케이션을 만듭니다. 구성에 정의된 것과 스마트 계약의 코드 사이에는 관계가 있습니다. 계약 세부 정보, 함수, 매개 변수 및 유형이 일치해야 애플리케이션을 만들 수 있습니다. 애플리케이션을 만들기 전에 Blockchain Workbench에서 파일을 확인합니다.

### <a name="contract"></a>계약

**contract** 헤더를 `HelloBlockchain.sol` 스마트 계약 코드 파일에 추가합니다.

``` solidity
contract HelloBlockchain {
```

### <a name="state-variables"></a>상태 변수

상태 변수는 각 계약 인스턴스의 상태 값을 저장합니다. 계약의 상태 변수는 구성 파일에 정의된 워크플로 속성과 일치해야 합니다.

`HelloBlockchain.sol` 스마트 계약 코드 파일의 계약에 상태 변수를 추가합니다.

``` solidity
    //Set of States
    enum StateType { Request, Respond}

    //List of properties
    StateType public  State;
    address public  Requestor;
    address public  Responder;

    string public RequestMessage;
    string public ResponseMessage;
```

### <a name="constructor"></a>생성자

생성자는 워크플로의 새 스마트 계약 인스턴스에 대한 입력 매개 변수를 정의합니다. 생성자의 필수 매개 변수는 구성 파일의 생성자 매개 변수로 정의됩니다. 매개 변수의 수, 순서 및 유형은 두 파일에서 일치해야 합니다.

생성자 함수에서 계약을 생성하기 전에 수행하려는 비즈니스 논리를 작성합니다. 예를 들어 시작 값으로 상태 변수를 초기화합니다.

계약자 함수를 `HelloBlockchain.sol` 스마트 계약 코드 파일의 계약에 추가합니다.

``` solidity
    // constructor function
    constructor(string memory message) public
    {
        Requestor = msg.sender;
        RequestMessage = message;
        State = StateType.Request;
    }
```

### <a name="functions"></a>Functions

함수는 계약 내 비즈니스 논리의 실행 가능한 단위입니다. 함수의 필수 매개 변수는 구성 파일의 함수 매개 변수로 정의됩니다. 매개 변수의 수, 순서 및 유형은 두 파일에서 일치해야 합니다. 함수는 구성 파일의 Blockchain Workbench 워크플로에서 전환과 관련이 있습니다. 전환은 계약에서 결정한 대로 애플리케이션 워크플로의 다음 단계로 이동하기 위해 수행되는 작업입니다.

함수에서 수행하려는 비즈니스 논리를 작성합니다. 예를 들어 상태 변수의 값을 수정합니다.

1. `HelloBlockchain.sol` 스마트 계약 코드 파일의 계약에 다음 함수를 추가합니다.

    ``` solidity
        // call this function to send a request
        function SendRequest(string memory requestMessage) public
        {
            if (Requestor != msg.sender)
            {
                revert();
            }
    
            RequestMessage = requestMessage;
            State = StateType.Request;
        }
    
        // call this function to send a response
        function SendResponse(string memory responseMessage) public
        {
            Responder = msg.sender;
    
            ResponseMessage = responseMessage;
            State = StateType.Respond;
        }
    }
    ```

2. `HelloBlockchain.sol` 스마트 계약 코드 파일을 저장합니다.

## <a name="add-blockchain-application-to-blockchain-workbench"></a>Blockchain Workbench에 블록체인 애플리케이션 추가

Blockchain Workbench에 블록체인 애플리케이션을 추가하려면 구성 및 스마트 계약 파일을 업로드하여 애플리케이션을 정의합니다.

1. 웹 브라우저에서 Blockchain Workbench 웹 주소로 이동합니다. 예를 들어 `https://{workbench URL}.azurewebsites.net/`입니다. Blockchain Workbench를 배포하면 웹 애플리케이션이 생성됩니다. Blockchain Workbench 웹 주소를 찾는 방법에 대한 내용은 [Blockchain Workbench 웹 URL](deploy.md#blockchain-workbench-web-url)을 참조하세요.
2. [Blockchain Workbench 관리자](manage-users.md#manage-blockchain-workbench-administrators)로 로그인합니다.
3. **애플리케이션** > **새로 만들기**를 선택합니다. **새 애플리케이션** 창이 표시됩니다.
4. **계약 구성 업로드** > **찾아보기**를 선택하여 생성한 **HelloBlockchain.json** 구성 파일을 찾습니다. 구성 파일의 유효성이 자동으로 검사됩니다. 유효성 검사 오류를 표시하려면 **표시** 링크를 선택합니다. 애플리케이션을 배포하기 전에 유효성 검사 오류를 수정합니다.
5. **계약 코드 업로드** > **찾아보기**를 선택하여 **HelloBlockchain.sol** 스마트 계약 코드 파일을 찾습니다. 코드 파일의 유효성이 자동으로 검사됩니다. 유효성 검사 오류를 표시하려면 **표시** 링크를 선택합니다. 애플리케이션을 배포하기 전에 유효성 검사 오류를 수정합니다.
6. **배포**를 선택하여 구성 및 스마트 계약 파일을 기반으로 블록체인 애플리케이션을 만듭니다.

블록체인 애플리케이션을 배포하는 데 몇 분이 걸립니다. 배포가 완료되면 새 애플리케이션이 **애플리케이션**에 표시됩니다. 

> [!NOTE]
> [Azure Blockchain Workbench REST API](/rest/api/azure-blockchain-workbench)를 사용하여 블록체인 애플리케이션을 만들 수도 있습니다.

## <a name="add-blockchain-application-members"></a>블록체인 애플리케이션 구성원 추가

애플리케이션 구성원을 애플리케이션에 추가하여 계약을 시작하고 작업을 수행합니다. 애플리케이션 구성원을 추가하려면 [ Blockchain Workbench 관리자](manage-users.md#manage-blockchain-workbench-administrators)여야 합니다.

1. **애플리케이션** > **Hello, Blockchain!** 을 선택합니다.
2. 애플리케이션과 연관된 구성원의 수는 페이지의 오른쪽 위에 표시됩니다. 새로운 애플리케이션의 경우 구성원 수가 0입니다.
3. 페이지의 오른쪽 위에 있는 **구성원** 링크를 선택합니다. 애플리케이션의 현재 구성원 목록이 표시됩니다.
4. 구성원 목록에서 **구성원 추가**를 선택합니다.
5. 추가하려는 구성원 이름을 선택하거나 입력합니다. Blockchain Workbench 테넌트에 있는 Azure AD 사용자만 나열됩니다. 사용자를 찾을 수 없는 경우 [Azure AD 사용자를 추가](manage-users.md#add-azure-ad-users)해야 합니다.
6. 구성원에 대해 **역할**을 선택합니다. 첫 번째 구성원의 경우 **요청자**를 역할로 선택합니다.
7. **추가**를 선택하여 관련된 역할이 있는 구성원을 애플리케이션에 추가합니다.
8. **응답자** 역할을 사용하여 다른 구성원을 애플리케이션에 추가합니다.

Blockchain Workbench에서 사용자를 관리하는 방법에 대한 자세한 내용은 [Azure Blockchain Workbench에서 사용자 관리](manage-users.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 방법 도움말 문서에서는 기본 요청 및 응답 애플리케이션을 만들어 보았습니다. 애플리케이션을 사용하는 방법을 알려면 다음의 방법 도움말 문서를 계속 사용합니다.

> [!div class="nextstepaction"]
> [블록체인 애플리케이션 사용](use.md)
