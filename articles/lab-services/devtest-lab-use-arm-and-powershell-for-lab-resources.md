---
title: Azure Resource Manager 템플릿 및 PowerShell을 사용하여 랩 자동 생성 또는 수정 | Microsoft Docs
description: Azure Resource Manager 템플릿 및 PowerShell을 사용하여 DevTest 랩에서 랩을 자동으로 생성 또는 수정하는 방법에 대해 배웁니다
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: dad9944c-0b20-48be-ba80-8f4aa0950903
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: cb5a08730b47cb5df3116aa4a54554ef0ee6f260
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622460"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Azure Resource Manager 템플릿 및 PowerShell을 사용하여 랩 자동 생성 또는 수정

DevTest Labs는 새 랩을 빠르게 자동 생성하거나 기존 랩을 수정한 다음 이러한 리소스를 배포하는 데 유용한 여러 Azure Resource Manager 템플릿 및 PowerShell 스크립트를 제공합니다.

이 문서는 이러한 템플릿과 스크립트를 사용하여 랩의 생성, 수정, 배포를 자동화하는 프로세스를 안내합니다. 이 문서는 PowerShell을 사용하여 DevTest Labs에서 몇 가지 일반적인 작업을 수행하는 방법에 대한 자세한 정보를 찾을 수 있는 위치를 안내합니다.

## <a name="step-1-gather-your-templates-and-scripts"></a>1단계: 템플릿 및 스크립트 수집
공용 [GitHub 리포지토리](https://github.com/Azure/azure-devtestlab)에서 미리 만들어진 [Azure Resource Manager 템플릿](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) 및 [PowerShell 스크립트](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts)를 찾을 수 있습니다. 그대로 사용하거나 요구 사항에 맞게 사용자 지정하고 [개인 Git 리포지토리](devtest-lab-add-artifact-repo.md)에 저장합니다.

## <a name="step-2-modify-your-azure-resource-manager-template"></a>2단계: Azure Resource Manager 템플릿 수정
이전에 템플릿을 만든 적이 없는 경우 [첫 번째 Azure Resource Manager 템플릿 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)에 나온 단계를 수행하시면 됩니다.

또한 [Azure Resource Manager 템플릿 작성에 대한 모범 사례](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-template-best-practices)에 안정적이고 사용하기 쉬운 Azure Resource Manager 템플릿을 만드는 데 도움이 되는 다양한 지침과 제안이 나와 있습니다. 일반적으로 제공되는 접근 방식 또는 예제 중 하나의 변형을 사용하고 사용자 필요에 따라 템플릿을 수정하게 됩니다.

## <a name="step-3-deploy-resources-with-powershell"></a>3단계: PowerShell로 리소스 배포
템플릿과 스크립트를 사용자 지정한 후 [Resource Manager 템플릿 및 Azure PowerShell로 리소스를 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)하는 데 필요한 단계를 따르십시오. 이 문서는 Azure Resource Manager 템플릿과 Azure PowerShell을 사용하여 Azure에 리소스를 배포하는 방법에 대한 일반 정보를 제공합니다.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>PowerShell을 사용하여 DevTest 랩에서 수행할 수 있는 일반 작업
PowerShell을 사용하여 자동화할 수 있는 다양한 일반 작업이 있습니다. 다음 섹션에서는 이러한 작업을 수행하는 데 필요한 단계에 대해 간단히 설명합니다.

* [PowerShell을 사용하여 VHD 파일에서 사용자 지정 이미지 만들기](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [PowerShell을 사용하여 랩의 저장소 계정에 VHD 파일 업로드](devtest-lab-upload-vhd-using-powershell.md)
* [PowerShell을 사용하여 랩에 외부 사용자 추가](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [PowerShell을 사용하여 랩 사용자 지정 역할 만들기](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>다음 단계
* 사용자 지정한 템플릿 또는 스크립트를 저장할 수 있는 [개인 Git 리포지토리](devtest-lab-add-artifact-repo.md)를 만드는 방법에 대해 배웁니다.
* [Azure 빠른 시작 템플릿 갤러리의 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates)(영문)을 탐색합니다.
