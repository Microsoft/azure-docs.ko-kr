---
title: Azure Blockchain Workbench에서 블록체인 애플리케이션 버전 관리
description: Azure Blockchain Workbench에서 애플리케이션 버전을 사용하는 방법
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 63f18e3ee316b9791bb62bfcd20c07a30cbebb5e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60896880"
---
# <a name="azure-blockchain-workbench-application-versioning"></a>Azure Blockchain Workbench 애플리케이션 버전 관리

여러 버전의 Azure Blockchain Workbench 앱을 만들고 사용할 수 있습니다. 여러 버전의 동일한 애플리케이션이 업로드되는 경우 버전 기록을 사용할 수 있으며 사용자는 사용하려는 버전을 선택할 수 있습니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>필수 조건

* Blockchain Workbench 배포. 자세한 내용은 배포에 대한 세부 사항을 위한 [Azure Blockchain Workbench 배포](deploy.md)를 참조하세요.
* Blockchain Workbench에 배포된 블록체인 애플리케이션. [Azure Blockchain Workbench에서 블록체인 애플리케이션 만들기](create-app.md)를 참조하세요.

## <a name="add-an-app-version"></a>앱 버전 추가

새 버전을 추가하려면 Blockchain Workbench에 새 구성 및 스마트 계약 파일을 업로드합니다.

1. 웹 브라우저에서 Blockchain Workbench 웹 주소로 이동합니다. 예를 들어 `https://{workbench URL}.azurewebsites.net/` Blockchain Workbench 웹 주소를 찾는 방법에 대한 내용은 [Blockchain Workbench 웹 URL](deploy.md#blockchain-workbench-web-url)을 참조하세요.
2. [Blockchain Workbench 관리자](manage-users.md#manage-blockchain-workbench-administrators)로 로그인합니다.
3. 다른 버전으로 업데이트하려는 블록체인 애플리케이션을 선택합니다.
4. **버전 추가**를 선택합니다. **추가 버전** 창이 표시됩니다.
5. 새 버전 계약 구성 및 업로드할 계약 코드 파일을 선택합니다. 구성 파일의 유효성이 자동으로 검사됩니다. 애플리케이션을 배포하기 전에 유효성 검사 오류를 수정합니다.
6. **버전 추가**를 선택하여 새 블록체인 애플리케이션 버전을 추가합니다.

    ![새 버전 추가](media/version-app/add-version.png)

블록체인 애플리케이션을 배포하는 데 몇 분이 걸릴 수 있습니다. 배포가 완료되면 애플리케이션 페이지를 새로 고칩니다. 애플리케이션을 선택하고 **버전 기록** 단추를 선택하면 애플리케이션의 버전 기록을 표시합니다.

> [!IMPORTANT]
> 애플리케이션의 이전 버전은 사용할 수 없습니다. 개별적으로 이전 버전을 다시 활성화할 수 있습니다.
>
> 새 버전의 애플리케이션 역할이 변경된 경우 애플리케이션 역할에 멤버를 다시 추가해야 합니다.

## <a name="using-app-versions"></a>앱 버전 사용

기본적으로 최신 활성화된 버전의 애플리케이션이 Blockchain Workbench에서 사용됩니다. 이전 버전의 애플리케이션을 사용하려는 경우 먼저 애플리케이션 페이지에서 버전을 선택해야 합니다.

1. Blockchain Workbench 애플리케이션 섹션에서 사용할 계약이 포함된 애플리케이션 확인란을 선택합니다. 이전 버전이 활성화된 경우 버전 기록 단추를 사용할 수 있습니다.
2. **버전 기록** 단추를 선택합니다.
3. 버전 기록 창에서 *수정한 날짜* 열의 링크를 선택하여 애플리케이션의 버전을 선택합니다.

    ![이전 버전 선택](media/version-app/use-version.png)

    새 계약을 만들거나 이전 버전의 계약에서 작업을 수행할 수 있습니다. 애플리케이션 이름 뒤에 애플리케이션의 버전이 표시되고 이전 버전에 대한 경고가 표시됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Blockchain Workbench 문제 해결](troubleshooting.md)
