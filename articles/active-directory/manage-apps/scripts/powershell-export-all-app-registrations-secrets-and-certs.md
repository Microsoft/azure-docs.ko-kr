---
title: PowerShell 샘플 - Azure Active Directory 테넌트에서 앱 등록용 비밀 및 인증서를 내보냅니다.
description: Azure Active Directory 테넌트에서 지정된 앱 등록을 위한 모든 비밀 및 인증서를 내보내는 PowerShell 예제입니다.
services: active-directory
author: mtillman
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: sample
ms.date: 03/09/2021
ms.author: mtillman
ms.reviewer: mifarca
ms.openlocfilehash: e358d4315a9f5095785e87f702ecad0c8448e1fd
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/14/2021
ms.locfileid: "112076855"
---
# <a name="export-secrets-and-certificates-for-app-registrations"></a>앱 등록용 비밀 및 인증서 내보내기

이 PowerShell 스크립트 예제는 지정된 앱 등록을 위한 모든 비밀 및 인증서를 디렉터리에서 CSV 파일로 내보냅니다.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

이러한 샘플을 사용하려면 [Graph용 AzureAD V2 PowerShell 모듈](/powershell/azure/active-directory/install-adv2)(AzureAD) 또는 [Graph용 AzureAD V2 PowerShell 모듈 미리 보기 버전](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)(AzureADPreview)이 필요합니다.

## <a name="sample-script"></a>샘플 스크립트

[!code-azurepowershell[main](~/powershell_scripts/application-management/export-all-app-registrations-secrets-and-certs.ps1 "Exports all secrets and certificates for the specified app registrations in your directory.")]

## <a name="script-explanation"></a>스크립트 설명

"Add-Member" 명령은 CSV 파일에서 열을 만드는 역할을 담당합니다.
내보내기를 비대화형으로 사용하려는 경우에는 CSV 파일 경로를 사용하여 PowerShell에서 직접 "$Path" 변수를 수정할 수 있습니다.

| 명령 | 메모 |
|---|---|
| [Get-AzureADApplication](/powershell/module/azuread/get-azureadapplication) | 디렉터리에서 애플리케이션을 검색합니다. |
| [Get-AzureADApplicationOwner](/powershell/module/azuread/Get-AzureADApplicationOwner) | 디렉터리에서 애플리케이션의 소유자를 검색합니다. |

## <a name="next-steps"></a>다음 단계

Azure AD PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 모듈 개요](/powershell/azure/active-directory/overview)를 참조하세요.

애플리케이션 관리에 대한 다른 PowerShell 예제는 [애플리케이션 관리에 대한 Azure AD PowerShell 예제](../app-management-powershell-samples.md)를 참조하세요.
