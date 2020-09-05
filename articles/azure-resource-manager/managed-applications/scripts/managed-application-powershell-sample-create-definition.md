---
title: 관리되는 애플리케이션 정의 만들기 - Azure PowerShell
description: Azure 구독에서 관리되는 애플리케이션 정의를 만드는 Azure PowerShell 스크립트 샘플을 제공합니다.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: b605d63db09b81bfe73eca8f4abd7a38d2325004
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86056024"
---
# <a name="create-a-managed-application-definition-with-powershell"></a>PowerShell을 사용하여 관리되는 애플리케이션 정의 만들기

이 스크립트는 관리되는 애플리케이션 정의를 서비스 카탈로그에 게시합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/create-definition/create-definition.ps1 "Create definition")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 관리되는 애플리케이션 정의를 만듭니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzManagedApplicationDefinition](/powershell/module/az.resources/new-azmanagedapplicationdefinition) | 관리되는 애플리케이션 정의를 만듭니다. 필요한 파일이 포함된 패키지를 제공합니다. |


## <a name="next-steps"></a>다음 단계

* 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](../overview.md)를 참조하세요.
* PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/get-started-azureps)를 참조하세요.
