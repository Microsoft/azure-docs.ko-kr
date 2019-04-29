---
title: AzCopy를 사용하여 Azure DevTest Labs에 VHD 업로드 | Microsoft Docs
description: AzCopy를 사용하여 랩의 저장소 계정에 VHD 파일 업로드
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
ms.openlocfilehash: 8cd778762bebf4a9dda3688292ac0a3674e446e1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634991"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-azcopy"></a>AzCopy를 사용하여 랩의 저장소 계정에 VHD 파일 업로드

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs에서는 VHD 파일을 사용하여 가상 머신을 프로비저닝을 사용하는 데 사용하는 사용자 지정 이미지를 만들 수 있습니다. 다음 단계는 AzCopy 명령줄 유틸리티를 사용하여 VHD 파일을 랩의 저장소 계정에 업로드하는 과정을 안내합니다. VHD 파일을 업로드하면 [다음 단계 섹션](#next-steps)은 업로드된 VHD 파일에서 사용자 지정 이미지를 만드는 방법을 자세히 설명하는 일부 문서를 나열합니다. Azure의 디스크 및 VHD에 대한 자세한 내용은 [관리 디스크 소개](../virtual-machines/linux/managed-disks-overview.md)를 참조하세요.

> [!NOTE] 
>  
> AzCopy는 Windows 전용 명령줄 유틸리티입니다.

## <a name="step-by-step-instructions"></a>단계별 지침

다음 단계는 [AzCopy](https://aka.ms/downloadazcopy)를 사용하여 Azure DevTest Labs로 VHD 파일을 업로드하는 과정을 안내합니다. 

1. Azure Portal을 사용하여 랩의 저장소 계정 이름을 가져옵니다.

1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.

1. **모든 서비스**를 선택한 다음 목록에서 **DevTest Labs**를 선택합니다.

1. 랩 목록에서 원하는 랩을 탭합니다.  

1. 랩의 블레이드에서 **구성**을 선택합니다. 

1. 랩의 **구성** 블레이드에서 **사용자 지정 이미지(VHD)** 를 선택합니다.

1. **사용자 지정 이미지** 블레이드에서 **+추가**를 선택합니다. 

1. **사용자 지정 이미지** 블레이드에서 **VHD**를 선택합니다.

1. **VHD** 블레이드에서 **PowerShell을 사용하여 VHD 업로드**를 선택합니다.

    ![PowerShell을 사용하여 VHD 업로드](./media/devtest-lab-upload-vhd-using-azcopy/upload-image-using-psh.png)

1. **PowerShell을 사용하여 이미지 업로드** 블레이드는 **Add-AzureVhd** cmdlet에 대한 호출을 표시합니다. 첫 번째 매개 변수(*대상*)는 다음 형식으로 BLOB 컨테이너(*업로드*)에 대한 URI를 포함합니다.

    ```
    https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...
    ``` 

1. 이후 단계에서 사용되므로 전체 URI를 기록해 둡니다.

1. AzCopy를 사용하여 VHD 파일 업로드:
 
1. [최신 버전의 AzCopy를 다운로드하여 설치](https://aka.ms/downloadazcopy)합니다.

1. 명령 창을 열고 AzCopy 설치 디렉터리로 이동합니다. 선택 사항으로, 시스템 경로에 AzCopy 설치 위치를 추가할 수 있습니다. 기본적으로 AzCopy는 다음 디렉터리에 설치됩니다.

    ```command-line
    %ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy
    ```

1. 저장소 계정 키 및 Blob 컨테이너 URI를 사용하여 명령 프롬프트에서 다음 명령을 실행합니다. *vhdFileName* 값은 따옴표로 표시해야 합니다. VHD 파일을 업로드하는 프로세스는 VHD 파일 크기 및 연결 속도에 따라 시간이 오래 걸릴 수 있습니다.   

    ```command-line
    AzCopy /Source:<sourceDirectory> /Dest:<blobContainerUri> /DestKey:<storageAccountKey> /Pattern:"<vhdFileName>" /BlobType:page
    ```

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지 만들기](devtest-lab-create-template.md)
- [PowerShell을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지 만들기](devtest-lab-create-custom-image-from-vhd-using-powershell.md)