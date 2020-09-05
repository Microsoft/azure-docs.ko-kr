---
title: PowerShell 샘플 - 애플리케이션 프록시 앱의 사용자 및 그룹 나열
description: 특정 Azure AD(Azure Active Directory) 애플리케이션 프록시 애플리케이션에 할당된 모든 사용자 및 그룹을 나열하는 PowerShell 예제입니다.
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 12/05/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 236850684ce479c2356943aea4fb0af570843f1f
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2020
ms.locfileid: "88506960"
---
# <a name="display-users-and-groups-assigned-to-an-application-proxy-application"></a>애플리케이션 프록시 애플리케이션에 할당된 사용자 및 그룹 표시

이 PowerShell 스크립트 예제에서는 특정 Azure AD(Azure Active Directory) 애플리케이션 프록시 애플리케이션에 할당된 사용자 및 그룹을 나열합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

이러한 샘플을 사용하려면 [Graph용 AzureAD V2 PowerShell 모듈](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)(AzureAD) 또는 [Graph용 AzureAD V2 PowerShell 모듈 미리 보기 버전](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)(AzureADPreview)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/display-users-group-of-an-app.ps1 "Display users and groups assigned to an Application Proxy application")]

## <a name="script-explanation"></a>스크립트 설명

| 명령 | 메모 |
|---|---|
| [Get-AzureADUser](https://docs.microsoft.com/powershell/module/AzureAD/get-azureaduser?view=azureadps-2.0)| 사용자를 가져옵니다. |
| [Get-AzureADGroup](https://docs.microsoft.com/powershell/module/AzureAD/get-azureadgroup?view=azureadps-2.0)| 그룹을 가져옵니다. |
| [Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 서비스 주체를 가져옵니다. |
| [Get-AzureADUserAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/get-azureaduserapproleassignment?view=azureadps-2.0) | 사용자 애플리케이션 역할 할당을 가져옵니다. |
| [Get-AzureADGroupAppRoleAssignment](https://docs.microsoft.com/powershell/module/AzureAD/get-azureadgroupapproleassignment?view=azureadps-2.0) | 그룹 애플리케이션 역할 할당을 가져옵니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)를 참조하세요.

애플리케이션 프록시에 대한 다른 PowerShell 예제는 [Azure AD 애플리케이션 프록시에 대한 Azure AD PowerShell 예제](../application-proxy-powershell-samples.md)를 참조하세요.