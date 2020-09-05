---
title: 관리 리소스 그룹 가져오기 및 VM 크기 조정 - Azure PowerShell
description: Azure Managed Application에 대한 관리 리소스 그룹을 가져오는 Azure PowerShell 샘플 스크립트를 제공합니다. 스크립트는 VM의 크기를 조정합니다.
author: tfitzmac
ms.devlang: powershell
ms.topic: sample
ms.date: 10/27/2017
ms.author: tomfitz
ms.openlocfilehash: 602aaeb67ca081ebac71ca1d6d24a2de3c020603
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/22/2020
ms.locfileid: "86055990"
---
# <a name="get-resources-in-a-managed-resource-group-and-resize-vms-with-powershell"></a>PowerShell을 사용하여 관리되는 리소스 그룹에서 리소스를 가져오고 VM 크기를 조정합니다.

이 스크립트는 관리되는 리소스 그룹에서 리소스를 검색하고 해당 리소스 그룹의 VM의 크기를 조정합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../../powershell_scripts/managed-applications/get-application/get-application.ps1 "Get application")]


## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용하여 관리되는 애플리케이션을 배포합니다. 표에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [Get-AzManagedApplication](/powershell/module/az.resources/get-azmanagedapplication) | 관리되는 애플리케이션을 나열합니다. 리소스 그룹 이름을 제공하여 결과를 얻습니다. |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | 리소스를 나열합니다. 리소스 그룹 및 리소스 종류를 제공하여 결과를 얻습니다. |
| [업데이트 AzVM](/powershell/module/az.compute/update-azvm) | 가상 머신의 크기를 업데이트합니다. |


## <a name="next-steps"></a>다음 단계

* 관리되는 애플리케이션에 대한 소개는 [Azure Managed Application 개요](../overview.md)를 참조하세요.
* PowerShell에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/get-started-azureps)를 참조하세요.
