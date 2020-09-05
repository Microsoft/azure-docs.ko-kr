---
title: Azure Blockchain Workbench에서 애플리케이션 사용
description: Azure Blockchain Workbench 미리 보기에서 애플리케이션 계약을 사용하는 방법에 대한 자습서입니다.
ms.date: 10/14/2019
ms.topic: tutorial
ms.reviewer: brendal
ms.openlocfilehash: 5761bf1294691c2d50e0e389fe69ec286df4a06c
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2020
ms.locfileid: "74324051"
---
# <a name="tutorial-using-applications-in-azure-blockchain-workbench"></a>자습서: Azure Blockchain Workbench에서 애플리케이션 사용

Blockchain Workbench를 사용하여 계약에서 만들고 작업을 수행할 수 있습니다. 또한 상태와 트랜잭션 기록 같은 계약 세부 정보를 볼 수 있습니다.

이 문서에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * 새 계약 만들기
> * 계약에 따라 작업 수행

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* Blockchain Workbench 배포. 자세한 내용은 배포에 대한 세부 사항을 위한 [Azure Blockchain Workbench 배포](deploy.md)를 참조하세요.
* Blockchain Workbench에 배포된 블록체인 애플리케이션. [Azure Blockchain Workbench에서 블록체인 애플리케이션 만들기](create-app.md)를 참조하세요.

브라우저에서 [Blockchain Workbench를 엽니다](deploy.md#blockchain-workbench-web-url).

![Blockchain Workbench](./media/use/workbench.png)

Blockchain Workbench의 구성원으로 로그인해야 합니다. 나열된 애플리케이션이 없는 경우 Blockchain Workbench의 구성원이지만 모든 애플리케이션의 구성원은 아닙니다. Blockchain Workbench 관리자는 애플리케이션에 구성원을 할당할 수 있습니다.

## <a name="create-new-contract"></a>새 계약 만들기

새 계약을 만들려면 계약 **초기자**로 지정된 멤버여야 합니다. 계약에 대한 애플리케이션 역할 및 초기자 정의에 대한 내용은 [구성 개요의 워크플로](configuration.md#workflows)를 참조하세요. 애플리케이션 역할에 구성원 할당에 대한 내용은 [애플리케이션에 구성원 추가](manage-users.md#add-member-to-application)를 참조하세요.

1. Blockchain Workbench 애플리케이션 섹션에서 만들 계약이 포함된 애플리케이션 타일을 선택합니다. 활성 계약 목록이 표시됩니다.

2. 새 계약을 만들려면 **새 계약**을 선택합니다.

    ![새 계약 단추](./media/use/contract-list.png)

3. **새 계약** 창이 표시됩니다. 초기 매개 변수 값을 지정합니다. **만들기**를 선택합니다.

    ![새 계약 창](./media/use/new-contract.png)

    새로 만든 계약이 다른 활성 계약이 포함된 목록에 표시됩니다.

    ![활성 계약 목록](./media/use/active-contracts.png)

## <a name="take-action-on-contract"></a>계약에서 작업 수행

계약의 상태에 따라 구성원은 계약의 다음 상태로 전환하는 작업을 수행할 수 있습니다. 작업은 [상태](configuration.md#states) 내에서 [전환](configuration.md#transitions)으로 정의됩니다. 전환에 대해 허용되는 애플리케이션 또는 인스턴스 역할에 속하는 구성원은 작업을 수행할 수 있습니다. 

1. Blockchain Workbench 애플리케이션 섹션에서 작업을 수행할 계약이 포함된 애플리케이션 타일을 선택합니다.
2. 목록에서 계약을 선택합니다. 계약에 대한 세부 정보는 서로 다른 섹션에 표시됩니다. 

    ![계약 세부 정보](./media/use/contract-details.png)

    | 섹션  | Description  |
    |---------|---------|
    | 상태 | 계약 단계 내에서 현재 진행 상태를 나열합니다. |
    | 세부 정보 | 계약의 현재 값 |
    | 작업 | 마지막 동작에 대한 세부 정보 |
    | 활동 | 계약의 트랜잭션 기록 |
    
3. **동작** 섹션에서 **작업 수행**을 선택합니다.

4. 계약의 현재 상태에 대한 세부 정보가 창에 표시됩니다. 드롭다운 목록에서 수행하려는 작업을 선택합니다. 

    ![작업 선택](./media/use/choose-action.png)

5. **작업 수행**을 선택하여 작업을 시작합니다.
6. 작업에 매개 변수가 필요한 경우 작업에 대한 값을 지정합니다.

    ![작업 수행](./media/use/take-action.png)

7. **작업 수행**을 선택하여 작업을 실행합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench 애플리케이션 버전 관리](version-app.md)
