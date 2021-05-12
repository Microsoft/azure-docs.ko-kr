---
title: Az PowerShell을 사용하도록 Visual Studio의 템플릿 배포 스크립트 업데이트
description: AzureRM에서 Az PowerShell로 Visual Studio 템플릿 배포 스크립트 업데이트
author: cweining
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: cweining
ms.openlocfilehash: 357e0289f3237ed32b0801280316225ba5530282
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "76963872"
---
# <a name="update-visual-studio-template-deployment-script-to-use-az-powershell-module"></a>Az PowerShell 모듈을 사용하도록 Visual Studio의 템플릿 배포 스크립트 업데이트

Visual Studio 16.4는 템플릿 배포 스크립트에서 Az PowerShell 모듈 사용을 지원합니다. 그러나 Visual Studio에서는 해당 모듈을 자동으로 설치하지 않습니다. Az 모듈을 사용하려면 다음의 네 단계를 수행해야 합니다:

1. [AzureRM 모듈 제거](/powershell/azure/uninstall-az-ps#uninstall-the-azurerm-module)
1. [Az 모듈 설치](/powershell/azure/install-az-ps)
1. 16.4로 Visual Studio 업데이트
1. 프로젝트의 배포 스크립트를 업데이트합니다.

## <a name="update-visual-studio-to-164"></a>16.4로 Visual Studio 업데이트

Visual Studio 설치를 버전 16.4 이상으로 업데이트합니다. 업그레이드하는 동안 Azure PowerShell 구성 요소가 선택되어 있지 않은 지 확인합니다. 갤러리를 통해 Az module을 설치했으므로 AzureRM 모듈을 다시 설치하지 않을 수 있습니다.

이미 16.4로 업그레이드하고 Azure PowerShell 구성 요소를 선택한 경우 Visual Studio 설치 프로그램을 실행하여 제거할 수 있습니다. Azure 워크로드 또는 개별 구성 요소 페이지에서 Azure PowerShell 구성 요소를 선택하지 마세요.

## <a name="update-the-deployment-script-in-your-project"></a>프로젝트의 배포 스크립트 업데이트

배포 스크립트에서 ‘AzureRm’ 문자열의 모든 항목을 ‘Az’로 바꿉니다. 이 [gist](https://gist.github.com/cweining/d2da2479418ea403499c4306dcf4f619)의 수정 버전을 참조하여 변경 내용을 볼 수 있습니다. 스크립트를 Az module로 업그레이드하는 방법에 대한 자세한 내용은 [AzureRM에서 Az로 Azure PowerShell 마이그레이션](/powershell/azure/migrate-from-azurerm-to-az)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Visual Studio 프로젝트 사용에 대한 자세한 내용은 [Visual Studio를 통해 Azure 리소스 그룹 프로젝트 만들기 및 배포](create-visual-studio-deployment-project.md)를 참조하세요.
