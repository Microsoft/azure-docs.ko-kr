---
title: Azure Logic Apps에서 Ethereum Blockchain 커넥터 사용-Azure Blockchain 서비스
description: Azure Logic Apps에서 Ethereum 블록체인 커넥터를 사용하여 스마트 계약 함수를 트리거하고 스마트 계약 이벤트에 응답합니다.
ms.date: 08/31/2020
ms.topic: how-to
ms.reviewer: caleteet
ms.openlocfilehash: 4364d2f616c8eaadedf12baf4bf77810eec69fdb
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89230537"
---
# <a name="use-the-ethereum-blockchain-connector-with-azure-logic-apps"></a>Azure Logic Apps에서 Ethereum 블록체인 커넥터 사용

[Azure Logic Apps](../../logic-apps/index.yml)에서 [Ethereum 블록체인 커넥터](/connectors/blockchainethereum/)를 사용하여 스마트 계약 작업을 수행하고 스마트 계약 이벤트에 응답합니다. 이 문서에서는 Ethereum Blockchain 커넥터를 사용 하 여 블록 체인 정보를 다른 서비스로 보내거나 Blockchain 함수를 호출 하는 방법을 설명 합니다. 예를 들어 블록체인 원장의 정보를 반환하는 REST 기반 마이크로서비스를 만들려고 한다고 가정해 보겠습니다. 논리 앱을 사용하면 블록체인 원장에 저장된 정보를 쿼리하는 HTTP 요청을 받을 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 선택적 필수 조건 [빠른 시작: Visual Studio Code을 사용 하 여 Azure Blockchain Service consortium 네트워크에 연결](connect-vscode.md)합니다. 이 빠른 시작에서는 [Ethereum용 Azure Blockchain Development Kit](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)를 설치하고 블록체인 개발 환경을 설정하는 방법을 안내합니다.
- Azure Logic Apps를 처음 사용 하는 경우 Microsoft Learn 모듈 소개를 검토 [하](/learn/modules/intro-to-logic-apps/) 여 [사용자 지정 커넥터를 사용 하 여 Logic Apps 워크플로에서 API](/learn/modules/logic-apps-and-custom-connectors/)를 Azure Logic Apps 하 고 호출 하는 것이 좋습니다.

## <a name="create-a-logic-app"></a>논리 앱 만들기

Azure Logic Apps를 사용하면 시스템과 서비스를 통합해야 할 때 비즈니스 프로세스와 워크플로를 예약하고 자동화할 수 있습니다. 먼저 Ethereum 블록체인 커넥터를 사용하는 논리를 만듭니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** > **통합** > **논리 앱**을 선택합니다.
1. **논리 앱 만들기** 아래에서 논리 앱을 만드는 위치에 대한 세부 정보를 제공합니다. 완료되면 **만들기**를 선택합니다.

    논리 앱을 만드는 방법에 대한 자세한 내용은 [Azure Logic Apps를 사용하여 자동화된 워크플로 만들기](../../logic-apps/quickstart-create-first-logic-app-workflow.md)를 참조하세요.

1. Azure에서 앱을 배포한 후에 논리 앱 리소스를 선택합니다.
1. Logic Apps 디자이너의 **템플릿** 아래에서 **비어 있는 논리 앱**을 선택합니다.

모든 논리 앱은 특정 이벤트가 발생하거나 특정 조건이 충족할 때 실행되는 트리거를 통해 시작되어야 합니다. 트리거가 발생될 때마다 Logic Apps 엔진은 워크플로를 시작하고 실행하는 논리 앱 인스턴스를 만듭니다.

Ethereum 블록체인 커넥터에는 하나의 트리거와 여러 작업이 있습니다. 사용하는 트리거 또는 작업은 시나리오에 따라 달라집니다. 시나리오와 가장 일치 하는이 문서의 섹션을 따릅니다.

워크플로에서 다음 상황이 발생하는 경우가 있습니다.

* 블록 체인에서 이벤트가 발생할 때 트리거 [이벤트 트리거를 사용](#use-the-event-trigger)합니다.
* 스마트 계약을 쿼리하거나 배포 하 고 [작업을 사용](#use-actions)합니다.
* 일반적인 시나리오를 따르는 경우 [개발자 키트를 사용하여 워크플로를 생성](#generate-a-workflow)합니다.

## <a name="use-the-event-trigger"></a>이벤트 트리거 사용

스마트 계약 이벤트가 발생한 후 논리 앱을 실행하려면 Ethereum 블록체인 이벤트 트리거를 사용합니다. 예를 들어 스마트 계약 함수가 호출될 때 이메일을 보내려고 합니다.

1. Logic Apps 디자이너에서 Ethereum 블록체인 커넥터를 선택합니다.
1. **트리거** 탭에서 **스마트 계약 이벤트가 발생하는 경우**를 선택합니다.
1. Azure Blockchain Service에 대한 [API 연결을 변경하거나 만듭니다](#create-an-api-connection).
1. 이벤트를 확인하려는 스마트 계약에 대한 세부 정보를 입력합니다.

    ![Event(이벤트) 트리거 속성이 있는 Logic Apps 디자이너](./media/ethereum-logic-app/event-properties.png)

    | 속성 | Description |
    |----------|-------------|
    | **계약 ABI** | 계약 ABI(애플리케이션 이진 인터페이스)는 스마트 계약 인터페이스를 정의합니다. 자세한 내용은 [계약 ABI 가져오기](#get-the-contract-abi)를 참조하세요. |
    | **스마트 계약 주소** | 계약 주소는 Ethereum 블록체인의 스마트 계약 대상 주소입니다. 자세한 내용은 [계약 주소 가져오기](#get-the-contract-address)를 참조하세요. |
    | **이벤트 이름** | 확인할 스마트 계약 이벤트를 선택합니다. 이벤트에서 논리 앱을 트리거합니다. |
    | **간격** 및 **빈도** | 이벤트를 확인하려는 빈도를 선택합니다. |

1. **저장**을 선택합니다.

논리 앱을 완료하려면 Ethereum 블록체인 이벤트 트리거에 기반한 작업을 수행하는 새 단계를 추가할 수 있습니다. 예를 들어 이메일을 보냅니다.

## <a name="use-actions"></a>작업 사용

논리 앱에서 블록체인 원장에 대한 작업을 수행하도록 하려면 Ethereum 블록체인 작업을 사용합니다. 예를 들어 논리 앱에 HTTP 요청이 있을 때 스마트 계약 함수를 호출하는 REST 기반 마이크로서비스를 만들려고 합니다.

커넥터 작업에는 트리거가 필요합니다. Ethereum 블록체인 커넥터 작업은 트리거 후의 다음 단계(예: 마이크로서비스에 대한 HTTP 요청 트리거)로 사용할 수 있습니다.

1. Logic Apps 디자이너에서 트리거 후의 **새 단계**를 선택합니다.
1. Ethereum 블록체인 커넥터를 선택합니다.
1. **작업** 탭에서 사용 가능한 작업 중 하나를 선택합니다.

    ![Actions(작업) 속성이 있는 Logic Apps 디자이너](./media/ethereum-logic-app/action-properties.png)

1. Azure Blockchain Service에 대한 [API 연결을 변경하거나 만듭니다](#create-an-api-connection).
1. 선택한 작업에 따라 제공하는 스마트 계약 함수에 대한 세부 정보는 다음과 같습니다.

    | 속성 | Description |
    |----------|-------------|
    | **계약 ABI** | 계약 ABI는 스마트 계약 인터페이스를 정의합니다. 자세한 내용은 [계약 ABI 가져오기](#get-the-contract-abi)를 참조하세요. |
    | **계약 바이트 코드** | 컴파일된 스마트 계약 바이트 코드입니다. 자세한 내용은 [계약 바이트 코드 가져오기](#get-the-contract-bytecode)를 참조하세요. |
    | **스마트 계약 주소** | 계약 주소는 Ethereum 블록체인의 스마트 계약 대상 주소입니다. 자세한 내용은 [계약 주소 가져오기](#get-the-contract-address)를 참조하세요. |
    | **스마트 계약 함수 이름** | 작업에 대한 스마트 계약 함수 이름을 선택합니다. 목록은 계약 ABI의 세부 정보에서 채워집니다. |

    스마트 계약 함수 이름을 선택하면 함수 매개 변수에 대한 필수 필드가 표시될 수 있습니다. 시나리오에 필요한 값 또는 동적 콘텐츠를 입력합니다.

이제 논리 앱을 사용할 수 있습니다. 논리 앱 이벤트가 트리거되면 Ethereum 블록체인 작업이 실행됩니다. 예를 들어 HTTP 요청 트리거는 Ethereum 블록체인 작업을 실행하여 스마트 계약 상태 값을 쿼리합니다. 이 쿼리는 값을 반환하는 HTTP 응답을 생성합니다.

## <a name="generate-a-workflow"></a>워크플로 생성

Ethereum Visual Studio Code 확장용 Azure Blockchain Development Kit는 일반적인 시나리오에 대한 논리 앱 워크플로를 생성할 수 있습니다. 사용할 수 있는 네 가지 시나리오는 다음과 같습니다.

* Azure SQL Database 인스턴스에 데이터 게시
* Azure Event Grid 또는 Azure Service Bus의 인스턴스에 이벤트 게시
* 보고서 게시
* REST 기반 마이크로서비스

 Azure Blockchain Development Kit는 Truffle을 사용하여 블록체인 개발을 간소화합니다. 스마트 계약에 기반한 논리 앱을 생성하려면 스마트 계약에 대한 Truffle 솔루션이 필요합니다. 또한 Azure Blockchain Service 컨소시엄 네트워크에 연결해야 합니다. 자세한 내용은 [Visual Studio Code를 사용 하 여 Azure Blockchain Service consortium 네트워크 빠른 시작에 연결](connect-vscode.md)을 참조 하세요.

예를 들어 다음 단계에서는 빠른 시작 **HelloBlockchain** 스마트 계약에 기반한 REST 기반 마이크로서비스 논리 앱을 생성합니다.

1. Visual Studio Code 탐색기 사이드바에서 솔루션의 **contracts** 폴더를 펼칩니다.
1. 마우스 오른쪽 단추로 **HelloBlockchain.sol**을 클릭하고, 메뉴에서 **스마트 계약용 마이크로서비스 생성**을 선택합니다.

    ![스마트 계약용 마이크로서비스 생성이 선택된 Visual Studio Code 창](./media/ethereum-logic-app/generate-logic-app.png)

1. 명령 팔레트에서 **논리 앱**을 선택합니다.
1. **계약 주소**를 입력합니다. 자세한 내용은 [계약 주소 가져오기](#get-the-contract-address)를 참조하세요.
1. 논리 앱에 대한 Azure 구독 및 리소스 그룹을 선택합니다.

    논리 앱 구성 및 코드 파일이 **generatedLogicApp** 디렉터리에 생성됩니다.

1. **generatedLogicApp/HelloBlockchain** 디렉터리를 살펴봅니다. 각 스마트 계약 함수, 이벤트, 속성에 대한 논리 앱 JSON 파일이 있습니다.
1. **generatedLogicApp/HelloBlockchain/Service/property.RequestMessage.logicapp.json** 파일을 열고, 내용을 복사합니다.

    ![복사할 코드가 있는 JSON 파일](./media/ethereum-logic-app/requestmessage.png)

1. 논리 앱에서 **논리 앱 코드 보기**를 선택합니다. 기존 JSON을 생성된 논리 앱 JSON으로 바꿉니다.

    ![새로 바꾼 앱 코드가 있는 논리 앱 코드 보기](./media/ethereum-logic-app/code-view.png)

1. **디자이너**를 선택하여 디자이너 보기로 전환합니다.
1. 논리 앱에는 시나리오의 기본 단계가 포함되어 있습니다. 그러나 Ethereum 블록체인 커넥터의 구성 세부 정보를 업데이트해야 합니다.
1. **연결** 단계를 선택하고, Azure Blockchain Service에 대한 [API 연결을 변경하거나 만듭니다](#create-an-api-connection).

    ![연결이 선택된 디자이너 보기](./media/ethereum-logic-app/microservice-logic-app.png)

1. 이제 논리 앱을 사용할 수 있습니다. REST 기반 마이크로서비스를 테스트하려면 논리 앱 요청 URL에 대한 HTTP POST 요청을 실행합니다. **Http 요청을 받을 때** 단계에서 **http POST URL** 콘텐츠를 복사 합니다.

    ![HTTP POST URL이 있는 Logic Apps 디자이너 창](./media/ethereum-logic-app/post-url.png)

1. cURL을 사용하여 HTTP POST 요청을 만듭니다. 자리 표시자 텍스트를 *\<HTTP POST URL\>* 이전 단계의 URL로 바꿉니다.

    ``` bash
    curl -d "{}" -H "Content-Type: application/json" -X POST "<HTTP POST URL>"
    ```

    cURL 명령은 논리 앱에서 응답을 반환합니다. 이 경우 응답은 **RequestMessage** 스마트 계약 함수의 출력입니다.

    ![RequestMessage 스마트 계약 함수의 코드 출력](./media/ethereum-logic-app/curl.png)

개발 키트를 사용하는 방법에 대한 자세한 내용은 [Ethereum용 Azure Blockchain Development Kit wiki 페이지](https://github.com/Microsoft/vscode-azure-blockchain-ethereum/wiki)를 참조하세요.

## <a name="create-an-api-connection"></a>API 연결 만들기

Ethereum 블록체인 커넥터에는 블록체인에 대한 API 연결이 필요합니다. API 커넥터는 여러 논리 앱에 사용할 수 있습니다. 일부 속성은 필수이며, 다른 속성은 시나리오에 따라 달라집니다.

> [!IMPORTANT]
> 블록체인에서 트랜잭션을 만들려면 프라이빗 키 또는 계정 주소와 암호가 필요합니다. 한 가지 형식의 인증만 필요합니다. 프라이빗 키와 계정 세부 정보를 모두 제공할 필요는 없습니다. 계약을 쿼리하는 경우 트랜잭션이 필요하지 않습니다. 계약 상태를 쿼리하는 작업을 사용하는 경우 프라이빗 키 또는 계정 주소와 암호가 필요하지 않습니다.

Azure Blockchain Service 멤버에 대한 연결을 설정하는 데 도움이 되기 위해 시나리오에 따라 필요할 수 있는 속성의 목록은 다음과 같습니다.

| 속성 | Description |
|----------|-------------|
|**연결 이름** | API 연결의 이름입니다. 필수 요소. |
|**Ethereum RPC 끝점** | Azure Blockchain Service 트랜잭션 노드의 HTTP 주소입니다. 필수 요소. 자세한 내용은 [RPC 엔드포인트 가져오기](#get-the-rpc-endpoint)를 참조하세요. |
|**프라이빗 키** | Ethereum 계정 프라이빗 키입니다. 트랜잭션에는 프라이빗 키 또는 계정 주소와 암호가 필요합니다. 자세한 내용은 [프라이빗 키 가져오기](#get-the-private-key)를 참조하세요. |
|**계정 주소** | Azure Blockchain Service 멤버의 계정 주소입니다. 트랜잭션에는 프라이빗 키 또는 계정 주소와 암호가 필요합니다. 자세한 내용은 [계정 주소 가져오기](#get-the-account-address)를 참조하세요. |
|**계정 암호** | 계정 암호는 멤버를 만들 때 설정됩니다. 암호 재설정에 대한 자세한 내용은 [Ethereum 계정](consortium.md#ethereum-account)을 참조하세요.|

## <a name="get-the-rpc-endpoint"></a>RPC 엔드포인트 가져오기

블록체인 네트워크에 연결하려면 Azure Blockchain Service RPC 엔드포인트 주소가 필요합니다. Ethereum용 Azure Blockchain Development Kit 또는 Azure Portal을 사용하여 엔드포인트 주소를 가져올 수 있습니다.

**개발 키트를 사용하려면,**

1. Visual Studio Code의 **Azure Blockchain Service** 아래에서 마우스 오른쪽 단추로 컨소시엄을 클릭합니다.
1. **RPC 엔드포인트 주소 복사**를 선택합니다.

    ![RPC 엔드포인트 주소 복사가 선택된 컨소시엄을 보여 주는 Visual Studio Code 창](./media/ethereum-logic-app/devkit-rpc.png)

    RPC 엔드포인트가 클립보드에 복사됩니다.

**Azure 포털을 사용하려면**

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure Blockchain Service 멤버로 이동합니다. **트랜잭션 노드**와 기본 트랜잭션 노드 링크를 선택합니다.

    ![기본 노드가 선택된 트랜잭션 노드 페이지](./media/ethereum-logic-app/transaction-nodes.png)

1. **연결 문자열**  >  **액세스 키**를 선택 합니다.
1. **HTTPS(액세스 키 1)** 또는 **HTTPS(액세스 키 2)** 에서 엔드포인트 주소를 복사합니다.

    ![연결 문자열 액세스 키가 있는 Azure Portal](./media/ethereum-logic-app/connection-string.png)

    RPC 엔드포인트는 Azure Blockchain Service 멤버 트랜잭션 노드의 주소 및 액세스 키를 포함하는 HTTPS URL입니다.

## <a name="get-the-private-key"></a>프라이빗 키 가져오기

트랜잭션을 블록체인에 보낼 때 Ethereum 계정의 프라이빗 키를 사용하여 인증할 수 있습니다. Ethereum 계정의 퍼블릭 키와 프라이빗 키는 12개 단어 니모닉에서 생성됩니다. Azure Blockchain Service 컨소시엄 멤버에 연결하면 Ethereum용 Azure Blockchain Development Kit에서 니모닉을 생성합니다. 개발 키트 확장을 사용하여 엔드포인트 주소를 가져올 수 있습니다.

1. Visual Studio Code에서 명령 팔레트를 엽니다(F1).
1. **Azure Blockchain: 개인 키 검색**을 선택 합니다.
1. 컨소시엄 멤버에 연결되면 저장한 니모닉을 선택합니다.

    ![니모닉을 선택하는 옵션이 있는 명령 팔레트](./media/ethereum-logic-app/private-key.png)

    프라이빗 키가 클립보드에 복사됩니다.

## <a name="get-the-account-address"></a>계정 주소 가져오기

트랜잭션을 블록체인에 보낼 때 멤버 계정과 암호를 사용하여 인증할 수 있습니다. 암호는 멤버를 만들 때 설정됩니다.

1. Azure Portal에서 Azure Blockchain Service 개요 페이지로 이동합니다.
1. **구성원 계정** 주소를 복사 합니다.

    ![멤버 계정 주소가 있는 개요 페이지](./media/ethereum-logic-app/member-account.png)

계정 주소와 암호에 대한 자세한 내용은 [Ethereum 계정](consortium.md#ethereum-account)을 참조하세요.

## <a name="get-the-contract-abi"></a>계약 ABI 가져오기

계약 ABI는 스마트 계약 인터페이스를 정의합니다. 스마트 계약과 상호 작용하는 방법을 설명합니다. Ethereum용 Azure Blockchain Development Kit를 사용하여 계약 ABI를 가져올 수 있습니다. 또한 Solidity 컴파일러에서 만든 계약 메타데이터 파일에서 가져올 수도 있습니다.

**개발 키트를 사용하려면,**

개발 키트 또는 Truffle을 사용하여 스마트 계약을 작성한 경우 확장을 사용하여 계약 ABI를 클립보드에 복사할 수 있습니다.

1. Visual Studio Code 탐색기 창에서 Solidity 프로젝트의 **build/contracts** 폴더를 펼칩니다.
1. 마우스 오른쪽 단추로 계약 메타데이터 JSON 파일을 클릭합니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. **계약 ABI 복사**를 선택합니다.

    ![계약 ABI 복사가 선택된 Visual Studio Code 창](./media/ethereum-logic-app/abi-devkit.png)

    계약 ABI가 클립보드에 복사됩니다.

**계약 메타데이터 파일을 사용하려면,**

1. Solidity 프로젝트의 **build/contracts** 폴더에 포함된 계약 메타데이터 파일을 엽니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. JSON 파일에서 **avi** 섹션을 찾습니다.
1. **avi** JSON 배열을 복사합니다.

    ![계약 메타데이터 파일의 ABI 코드](./media/ethereum-logic-app/abi-metadata.png)

## <a name="get-the-contract-bytecode"></a>계약 바이트 코드 가져오기

계약 바이트 코드는 Ethereum 가상 머신에서 실행하는 컴파일된 스마트 계약입니다. Ethereum용 Azure Blockchain Development Kit를 사용하여 계약 바이트 코드를 가져올 수 있습니다. 또한 Solidity 컴파일러에서 가져올 수도 있습니다.

**개발 키트를 사용하려면,**

개발 키트 또는 Truffle을 사용하여 스마트 계약을 작성한 경우 확장을 사용하여 계약 바이트 코드를 클립보드에 복사할 수 있습니다.

1. Visual Studio Code 탐색기 창에서 Solidity 프로젝트의 **build/contracts** 폴더를 펼칩니다.
1. 마우스 오른쪽 단추로 계약 메타데이터 JSON 파일을 클릭합니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. **계약 바이트 코드 복사**를 선택합니다.

    ![계약 바이트 코드 복사가 선택된 Visual Studio Code 창](./media/ethereum-logic-app/bytecode-devkit.png)

    계약 바이트 코드가 클립보드에 복사됩니다.

**계약 메타데이터 파일을 사용하려면,**

1. Solidity 프로젝트의 **build/contracts** 폴더에 포함된 계약 메타데이터 파일을 엽니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. JSON 파일에서 **bytecode** 요소를 찾습니다.
1. **bytecode** 값을 복사합니다.

    ![메타데이터의 바이트 코드가 있는 Visual Studio Code 창](./media/ethereum-logic-app/bytecode-metadata.png)

**Solidity 컴파일러를 사용하려면,**

`solc --bin <smart contract>.sol` 명령을 사용하여 계약 바이트 코드를 생성합니다.

## <a name="get-the-contract-address"></a>계약 주소 가져오기

계약 주소는 Ethereum 블록체인의 스마트 계약 대상 주소입니다. 이 주소를 사용하여 스마트 계약의 트랜잭션 또는 쿼리 상태를 보냅니다. Truffle 마이그레이션 출력 또는 계약 메타데이터 파일에서 계약 주소를 가져올 수 있습니다.

**Truffle 마이그레이션 출력을 사용하려면 다음을 수행합니다.**

Truffle은 스마트 계약을 배포한 후에 계약 주소를 표시합니다. 출력에서 **계약 주소**를 복사합니다.

![계약 주소가 있는 Visual Studio Code의 Truffle 마이그레이션 출력](./media/ethereum-logic-app/contract-address-truffle.png)

**계약 메타데이터 파일을 사용하려면,**

1. Solidity 프로젝트의 **build/contracts** 폴더에 포함된 계약 메타데이터 파일을 엽니다. 파일 이름에는 스마트 계약 이름 뒤에 **.json** 확장명이 붙습니다.
1. JSON 파일에서 **networks** 섹션을 찾습니다.
1. 개인 네트워크는 정수 네트워크 ID로 식별됩니다. networks 섹션 내에서 주소 값을 찾습니다.
1. **address** 값을 복사합니다.

![주소 값이 있는 Visual Studio Code의 메타데이터](./media/ethereum-logic-app/contract-address-metadata.png)

## <a name="next-steps"></a>다음 단계

[Logic Apps를 사용하여 더 많은 작업 수행](https://channel9.msdn.com/Shows/Blocktalk/Doing-more-with-Logic-Apps?term=logic%20apps%20blockchain&lang-en=true) 비디오에서 일반적인 시나리오를 시청합니다.
