---
title: Azure VM에 대한 템플릿 다운로드 | Microsoft Docs
description: VM에 대한 템플릿을 다운로드하면 Resource Manager 배포 모델에서 배포를 자동화할 수 있습니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 4ef0c09d2d2ea2ed06708342ab45abcaf149c23e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075883"
---
# <a name="download-the-template-for-a-vm"></a>VM에 대한 템플릿 다운로드
포털 또는 PowerShell을 사용하여 Azure에서 VM을 만들 때 Resource Manager 템플릿은 자동으로 만들어집니다. 배포를 빠르게 복제하는 데 이 템플릿을 사용할 수 있습니다. 템플릿은 리소스 그룹에 있는 모든 리소스에 대한 정보를 포함합니다. 즉 가상 머신의 경우 네트워킹 리소스를 포함하여 해당 리소스 그룹에서 VM을 지원하기 위해 만든 모든 항목이 템플릿에 있음을 의미합니다.

## <a name="download-the-template-using-the-portal"></a>포털을 사용한 템플릿 다운로드
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. 왼쪽 메뉴에서 **Virtual Machines**을 선택합니다.
3. 목록에서 가상 머신을 선택합니다.
4. 선택 **템플릿 내보내기**합니다.
5. 위쪽의 메뉴에서 **다운로드**를 선택하고 .zip 파일을 로컬 컴퓨터에 저장합니다.
6. .zip 파일을 열고 파일을 폴더에 풉니다. .zip 파일에는 다음 항목이 포함되어 있습니다.
   
   * deploy.ps1
   * deploy.sh 
   * deployer.rb
   * DeploymentHelper.cs
   * parameters.json
   * template.json

template.json 파일은 템플릿입니다.

## <a name="download-the-template-using-powershell"></a>PowerShell을 사용한 템플릿 다운로드
또한 [Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) cmdlet을 사용하여 .json 템플릿 파일을 다운로드할 수 있습니다. `-path` 매개 변수를 사용하여 .json 파일의 파일 이름 및 경로를 제공합니다. 이 예제에서는 로컬 컴퓨터의 **C:\users\public\downloads** 폴더에 **myResourceGroup**이라는 리소스 그룹에 대한 템플릿을 다운로드하는 방법을 보여 줍니다.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>다음 단계
템플릿을 사용하여 리소스를 배포하는 방법에 대해 알아보려면 [Resource Manager 템플릿 연습](../../azure-resource-manager/resource-manager-template-walkthrough.md)을 참조하세요.

