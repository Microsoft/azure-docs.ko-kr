---
title: 'PowerShell: 로컬 Git 리포지토리에서 배포'
description: Azure PowerShell을 사용하여 App Service의 배포 및 관리를 자동화하는 방법을 알아봅니다. 이 샘플에서는 로컬 Git 리포지토리에서 코드를 배포하는 방법을 보여줍니다.
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, devx-track-azurepowershell
ms.openlocfilehash: 03d2f2d3928393f8a251640a03d82b5963140141
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89070295"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>웹앱 만들기 및 로컬 Git 리포지토리의 코드 배포

이 샘플 스크립트는 관련된 리소스를 사용하여 App Service에서 웹앱을 만든 다음 로컬 Git 리포지토리에서 웹앱 코드를 배포합니다.

필요한 경우 [Azure PowerShell 가이드](/powershell/azure/)에 있는 지침을 사용하여 최신 Azure PowerShell을 업데이트한 다음, `Connect-AzAccount`를 실행하여 Azure에 연결합니다. 또한 애플리케이션 코드는 로컬 Git 리포지토리로 커밋될 수 있어야 합니다.

## <a name="sample-script"></a>샘플 스크립트

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>배포 정리 

스크립트 샘플을 실행한 후에는 다음 명령을 사용하여 리소스 그룹, 웹앱 및 모든 관련된 리소스를 제거할 수 있습니다.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>스크립트 설명

이 스크립트는 다음 명령을 사용합니다. 테이블에 있는 각 명령은 명령에 해당하는 문서에 연결됩니다.

| 명령 | 메모 |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | 필요한 리소스 그룹 및 App Service 그룹을 사용하여 웹앱을 만듭니다. 현재 디렉터리에 Git 리포지토리가 포함된 경우 `azure` 원격을 추가합니다. |

## <a name="next-steps"></a>다음 단계

Azure PowerShell 모듈에 대한 자세한 내용은 [Azure PowerShell 설명서](/powershell/azure/)를 참조하세요.

Azure App Service Web Apps에 대한 추가 Azure PowerShell 샘플은 [Azure PowerShell 샘플](../samples-powershell.md)에서 확인할 수 있습니다.
