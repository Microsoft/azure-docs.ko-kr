---
title: 스마트 계약 생성, 빌드 및 배포 - Azure Blockchain Service
description: Visual Studio Code에서 Ethereum 확장용 Azure Blockchain Development Kit를 사용하여 Azure Blockchain Service에서 스마트 계약을 만들고 빌드하고 배포하는 방법에 대한 자습서입니다.
ms.date: 04/22/2020
ms.topic: tutorial
ms.reviewer: caleteet
ms.openlocfilehash: dc23c680dfb2ed33cae2a251af16e1b1f25c6ac7
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "82086660"
---
# <a name="tutorial-create-buildanddeploysmartcontracts-on-azure-blockchain-service"></a>자습서: Azure Blockchain Service에서 스마트 계약 생성, 빌드 및 배포

이 자습서에서는 Visual Studio Code에서 Ethereum 확장용 Azure Blockchain Development Kit를 사용하여 Azure Blockchain Service에서 스마트 계약을 만들고 빌드하고 배포합니다. 또한 개발 키트를 사용하여 트랜잭션을 통해 스마트 계약 함수를 실행합니다.

Etherum용 Azure Blockchain Development Kit를 사용하여 다음을 수행합니다.

> [!div class="checklist"]
> * 스마트 계약 만들기
> * 스마트 계약 배포
> * 트랜잭션을 통해 스마트 계약 함수 실행

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* [빠른 시작: Visual Studio Code를 사용하여 Azure Blockchain Service 컨소시엄 네트워크에 연결](connect-vscode.md) 완료
* [Visual Studio Code](https://code.visualstudio.com/Download)
* [Etherum용 Azure Blockchain Development Kit 확장](https://marketplace.visualstudio.com/items?itemName=AzBlockchain.azure-blockchain)
* [Node.js 10.15.x 이상](https://nodejs.org/download)
* [Git 2.10.x 이상](https://git-scm.com)
* [Python 2.7.15](https://www.python.org/downloads/release/python-2715/) python.exe를 경로에 추가합니다. Azure Blockchain Development Kit에는 경로의 Python 버전 2.7.15가 필요합니다.
* [Truffle 5.0.0](https://www.trufflesuite.com/docs/truffle/getting-started/installation)
* [Ganache CLI 6.0.0](https://github.com/trufflesuite/ganache-cli)

Windows에서는 node-gyp 모듈에 설치된 C++ 컴파일러가 필요합니다. MSBuild 도구를 사용할 수 있습니다.

* Visual Studio 2017이 설치된 경우 `npm config set msvs_version 2017 -g` 명령을 사용하여 MSBuild 도구를 사용하도록 npm을 구성합니다.
* Visual Studio 2019가 설치된 경우 npm에 대한 MS 빌드 도구 경로를 설정합니다. 예를 들어 `npm config set msbuild_path "C:\Program Files (x86)\Microsoft Visual Studio\2019\Community\MSBuild\Current\Bin\MSBuild.exe"`
* 그렇지 않으면 *관리자 권한으로 실행* 명령 셸에서 `npm install --global windows-build-tools`를 사용하여 독립 실행형 VS 빌드 도구를 설치합니다.

node-gyp에 대한 자세한 내용은 [GitHub의 node-gyp 리포지토리](https://github.com/nodejs/node-gyp)를 참조하세요.

## <a name="create-a-smart-contract"></a>스마트 계약 만들기

Etherum용 Azure Blockchain Development Kit는 프로젝트 템플릿과 Truffle 도구를 사용하여 계약을 스캐폴드, 빌드 및 배포하는 데 도움을 줍니다. 시작하기 전에 [빠른 시작: Visual Studio Code를 사용하여 Azure Blockchain Service 컨소시엄 네트워크에 연결](connect-vscode.md)을 완료해야 합니다. 이 빠른 시작에서는 Ethereum용 Azure Blockchain Development Kit의 설치 및 구성 방법을 안내합니다.

1. VS Code 명령 팔레트에서 **Azure Blockchain: 새 Solidity 프로젝트**를 선택합니다.
1. **기본 프로젝트 만들기**를 선택합니다.
1. `HelloBlockchain`이라는 새 폴더를 만들고, **새 프로젝트 경로를 선택**합니다.

Azure Blockchain Development Kit에서 새 Solidity 프로젝트를 만들고 초기화합니다. 기본 프로젝트에는 **HelloBlockchain** 스마트 계약 샘플과 Azure Blockchain Service의 컨소시엄 멤버에 빌드 및 배포하는 데 필요한 모든 파일이 포함되어 있습니다. 프로젝트를 만드는 데 몇 분 정도 걸릴 수 있습니다. Azure Blockchain의 출력을 선택하여 VS Code의 터미널 패널의 진행 상황을 모니터링할 수 있습니다.

프로젝트 구조는 다음 예제와 같습니다.

   ![Solidity 프로젝트](./media/send-transaction/solidity-project.png)

## <a name="build-a-smart-contract"></a>스마트 계약 빌드

스마트 계약은 프로젝트의 **contracts** 디렉터리에 있습니다. 블록체인에 배포하기 전에 스마트 계약을 컴파일합니다. **계약 빌드** 명령을 사용하여 프로젝트의 모든 스마트 계약을 컴파일합니다.

1. VS Code 탐색기 사이드바에서 프로젝트의 **contracts** 폴더를 펼칩니다.
1. 마우스 오른쪽 단추로 **HelloBlockchain.sol**을 클릭하고, 메뉴에서 **계약 빌드**를 선택합니다.

    ![계약 빌드 메뉴 선택 ](./media/send-transaction/build-contracts.png)

Azure Blockchain Development Kit에서 Truffle을 사용하여 스마트 계약을 컴파일합니다.

![Truffle 컴파일러 출력](./media/send-transaction/compile-output.png)

## <a name="deploy-a-smart-contract"></a>스마트 계약 배포

Truffle은 마이그레이션 스크립트를 사용하여 계약을 Ethereum 네트워크에 배포합니다. 마이그레이션은 프로젝트의 **miginations** 디렉터리에 있는 JavaScript 파일입니다.

1. 스마트 계약을 배포하려면 마우스 오른쪽 단추로 **HelloBlockchain.sol**을 클릭하고, 메뉴에서 **계약 배포**를 선택합니다.
1. 명령 팔레트에서 Azure Blockchain 컨소시엄 네트워크를 선택합니다. 컨소시엄 블록체인 네트워크는 프로젝트를 만들 때 프로젝트의 Truffle 구성 파일에 추가되었습니다.
1. **니모닉 생성**을 선택합니다. 파일 이름을 선택하고, 해당 니모닉 파일을 프로젝트 폴더에 저장합니다. `myblockchainmember.env`)을 입력합니다. 니모닉 파일은 블록체인 멤버에 대한 Ethereum 프라이빗 키를 생성하는 데 사용됩니다.

Azure Blockchain Development Kit에서 Truffle을 사용하여 계약을 블록체인으로 배포하는 마이그레이션 스크립트를 실행합니다.

![성공적으로 배포된 계약](./media/send-transaction/deploy-contract.png)

## <a name="call-a-contract-function"></a>계약 함수 호출

**HelloBlockchain** 계약의 **SendRequest** 함수는 **RequestMessage** 상태 변수를 변경합니다. 블록체인 네트워크의 상태를 변경하는 작업은 트랜잭션을 통해 수행됩니다. Azure Blockchain Development Kit 스마트 계약 상호 작용 페이지를 사용하여 트랜잭션을 통해 **SendRequest** 함수를 호출할 수 있습니다.

1. 스마트 계약과 상호 작용하려면 마우스 오른쪽 단추로 **HelloBlockchain.sol**을 클릭하고, 메뉴에서 **스마트 계약 상호 작용 페이지 표시**를 선택합니다.

    ![메뉴에서 스마트 계약 상호 작용 페이지 표시 선택](./media/send-transaction/contract-interaction.png)

1. 상호 작용 페이지에서는 배포된 계약 버전을 선택하고, 함수를 호출하며, 현재 상태를 보고, 메타데이터를 볼 수 있습니다.

    ![스마트 계약 상호 작용 페이지 예](./media/send-transaction/interaction-page.png)

1. 스마트 계약 함수를 호출하려면 계약 작업을 선택하고 인수를 전달합니다. **SendRequest** 계약 작업을 선택하고, **Hello, Blockchain!** 을 **requestMessage** 매개 변수에 입력합니다. **실행**을 선택하여 트랜잭션을 통해 **SendRequest** 함수를 호출합니다.

    ![SendRequest 작업 실행](./media/send-transaction/sendrequest-action.png)

트랜잭션이 처리되면 상호 작용 섹션에 상태 변경 내용이 반영됩니다.

![계약 상태 변경](./media/send-transaction/contract-state.png)

SendRequest 함수는 **RequestMessage** 및 **State** 필드를 설정합니다. **RequestMessage**의 현재 상태는 **Hello, Blockchain**을 전달한 인수입니다. **State** 필드 값은 **Request**로 유지됩니다.

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 *블록체인 멤버 만들기* 필수 조건 빠른 시작에서 만든 `myResourceGroup` 리소스 그룹을 삭제하여 리소스를 삭제할 수 있습니다.

리소스 그룹을 삭제하려면 다음을 수행합니다.

1. Azure Portal의 왼쪽 탐색 창에서 **리소스 그룹**으로 이동하고 삭제하려는 리소스 그룹을 선택합니다.
1. **리소스 그룹 삭제**를 선택합니다. 리소스 그룹 이름을 입력하여 삭제를 확인하고 **삭제**를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Blockchain Development Kit를 사용하여 Solidity 프로젝트 샘플을 만들었습니다. 스마트 계약을 빌드하고 배포한 다음, Azure Blockchain Service에서 호스팅되는 블록체인 컨소시엄 네트워크에서 트랜잭션을 통해 함수를 호출했습니다.

> [!div class="nextstepaction"]
> [Azure Blockchain Service를 사용하여 블록체인 애플리케이션 개발](develop.md)
