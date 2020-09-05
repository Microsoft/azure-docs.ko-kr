---
title: 'PowerShell: 스테이징 슬롯에 코드 배포'
description: Azure PowerShell을 사용하여 App Service의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 스테이징 환경에 코드를 배포하는 방법을 보여줍니다.
tags: azure-service-management
ms.assetid: 27cf0680-c3a9-4a58-9f71-6dec09f6b874
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 1024aa360202b11d0cb1317db359f9c2e661ebb9
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070380"
---
# <a name="create-a-web-app-and-deploy-code-to-a-staging-environment"></a>웹앱 만들기 및 스테이징 환경에 코드 배포

이 샘플 스크립트는 "스테이징"이라는 추가 배포 슬롯을 사용하여 App Service에서 웹앱을 만든 다음 "스테이징" 슬롯에 샘플 앱을 배포합니다.

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 Azure PowerShell을 설치한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다.

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-deployment-slot/deploy-deployment-slot.ps1?highlight=1 "Create a web app and deploy code to a staging environment")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | 모든 리소스가 저장되는 리소스 그룹을 만듭니다. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | App Service 계획을 만듭니다. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 웹앱을 만듭니다. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | App Service 계획을 수정하여 해당 가격 책정 계층을 변경합니다. |
| [New-AzWebAppSlot](/powershell/module/az.websites/new-azwebappslot) | 웹앱에 대한 배포 슬롯을 만듭니다. |
| [집합 AzResource](/powershell/module/az.resources/set-azresource) | 리소스 그룹에서 리소스를 수정합니다. |
| [Switch-AzWebAppSlot](/powershell/module/az.websites/switch-azwebappslot) | 웹앱의 배포 슬롯을 프로덕션으로 교환합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure App Service Web Apps에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
