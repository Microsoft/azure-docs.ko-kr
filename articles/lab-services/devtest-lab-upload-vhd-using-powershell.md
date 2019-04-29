---
title: PowerShell을 사용하여 Azure DevTest Labs에 VHD 파일 업로드 | Microsoft Docs
description: PowerShell을 사용하여 랩의 저장소 계정에 VHD 파일 업로드
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 56a66c3eb1dad93fad3ad1572989dc0c0aa14632
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622770"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-powershell"></a>PowerShell을 사용하여 랩의 저장소 계정에 VHD 파일 업로드

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs에서는 VHD 파일을 사용하여 가상 머신을 프로비저닝을 사용하는 데 사용하는 사용자 지정 이미지를 만들 수 있습니다. 다음 단계는 PowerShell을 사용하여 VHD 파일을 랩의 저장소 계정에 업로드하는 과정을 안내합니다. VHD 파일을 업로드하면 [다음 단계 섹션](#next-steps)은 업로드된 VHD 파일에서 사용자 지정 이미지를 만드는 방법을 자세히 설명하는 일부 문서를 나열합니다. Azure의 디스크 및 VHD에 대한 자세한 내용은 [관리 디스크 소개](../virtual-machines/linux/managed-disks-overview.md)를 참조하세요.

## <a name="step-by-step-instructions"></a>단계별 지침

다음 단계는 PowerShell을 사용하여 Azure DevTest Labs로 VHD 파일을 업로드하는 과정을 안내합니다. 

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **모든 서비스**를 선택한 다음 목록에서 **DevTest Labs**를 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.  

1. 랩의 블레이드에서 **구성**을 선택합니다. 

1. 랩의 **구성** 블레이드에서 **사용자 지정 이미지(VHD)** 를 선택합니다.

1. **사용자 지정 이미지** 블레이드에서 **+추가**를 선택합니다. 

1. **사용자 지정 이미지** 블레이드에서 **VHD**를 선택합니다.

1. **VHD** 블레이드에서 **PowerShell을 사용하여 VHD 업로드**를 선택합니다.

    ![PowerShell을 사용하여 VHD 업로드](./media/devtest-lab-upload-vhd-using-powershell/upload-image-using-psh.png)

1. **PowerShell을 사용하여 이미지 업로드** 블레이드에서 생성된 PowerShell 스크립트를 텍스트 편집기에 복사합니다.

1. **Add-AzureVhd** cmdlet의 **LocalFilePath** 매개 변수를 수정하여 업로드하려는 VHD 파일의 위치를 지정합니다.

1. PowerShell 프롬프트에서 **Add-AzureVhd** cmdlet(수정된 **LocalFilePath** 매개 변수 포함)을 실행합니다.

> [!WARNING] 
> 
> VHD 파일을 업로드하는 프로세스는 VHD 파일 크기 및 연결 속도에 따라 시간이 오래 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지 만들기](devtest-lab-create-template.md)
- [PowerShell을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지 만들기](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
