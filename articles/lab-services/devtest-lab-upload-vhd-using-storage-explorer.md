---
title: Microsoft Azure Storage 탐색기를 사용하여 Azure DevTest Labs에 VHD 파일 업로드 | Microsoft Docs
description: Microsoft Azure Storage 탐색기를 사용하여 랩의 Storage 계정에 VHD 파일 업로드
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
ms.openlocfilehash: 3c187d104334fe75ec9e0ce41a3fdc14b508dfb2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60623426"
---
# <a name="upload-vhd-file-to-labs-storage-account-using-microsoft-azure-storage-explorer"></a>Microsoft Azure Storage 탐색기를 사용하여 랩의 Storage 계정에 VHD 파일 업로드

[!INCLUDE [devtest-lab-upload-vhd-selector](../../includes/devtest-lab-upload-vhd-selector.md)]

Azure DevTest Labs에서는 VHD 파일을 사용하여 가상 머신을 프로비저닝을 사용하는 데 사용하는 사용자 지정 이미지를 만들 수 있습니다. 이 문서에서는 [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 랩의 Storage 계정에 VHD 파일을 업로드합니다. VHD 파일을 업로드하면 [다음 단계 섹션](#next-steps)은 업로드된 VHD 파일에서 사용자 지정 이미지를 만드는 방법을 자세히 설명하는 일부 문서를 나열합니다. Azure의 디스크 및 VHD에 대한 자세한 내용은 [관리 디스크 소개](../virtual-machines/linux/managed-disks-overview.md)를 참조하세요.

## <a name="step-by-step-instructions"></a>단계별 지침

다음 단계는 [Microsoft Azure Storage 탐색기](../vs-azure-tools-storage-manage-with-storage-explorer.md)를 사용하여 DevTest Labs로 VHD 파일을 업로드하는 과정을 안내합니다.

1. [Microsoft Azure Storage 탐색기 최신 버전을 다운로드하여 설치합니다](https://www.storageexplorer.com).

1. Azure Portal을 사용하여 랩의 저장소 계정 이름을 가져옵니다.

    1. [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에 로그인합니다.
    
    1. **모든 서비스**를 선택한 다음 목록에서 **DevTest Labs**를 선택합니다.
    
    1. 랩 목록에서 원하는 랩을 탭합니다.  
    
    1. 랩의 블레이드에서 **구성**을 선택합니다. 
    
    1. 랩의 **구성** 블레이드에서 **사용자 지정 이미지(VHD)** 를 선택합니다.
    
    1. **사용자 지정 이미지** 블레이드에서 **+추가**를 선택합니다. 
    
    1. **사용자 지정 이미지** 블레이드에서 **VHD**를 선택합니다.
    
    1. **VHD** 블레이드에서 **PowerShell을 사용하여 VHD 업로드**를 선택합니다.
    
        ![PowerShell을 사용하여 VHD 업로드][0]
    
    1. **PowerShell을 사용하여 이미지 업로드** 블레이드는 **Add-AzureVhd** cmdlet에 대한 호출을 표시합니다. 첫 번째 매개 변수(*대상*)는 다음 형식으로 랩에 대한 저장소 계정 이름을 포함합니다.
    
        `https://<STORAGE-ACCOUNT-NAME>.blob.core.windows.net/uploads/...`

    1. 이후 단계에서 사용되므로 저장소 계정 이름을 기록해 둡니다.
    
1. 저장소 탐색기를 사용하여 Azure 구독 계정에 연결합니다.

    > [!TIP] 
    > 
    > 저장소 탐색기는 여러 가지 연결 옵션을 지원합니다. 이 섹션에서는 Azure 구독과 연결된 저장소 계정에 연결하는 방법을 보여 줍니다. 저장소 탐색기에서 지원하는 다른 연결 옵션을 보려면 [저장소 탐색기 시작](../vs-azure-tools-storage-manage-with-storage-explorer.md) 문서를 참조하세요.
 
    1. 저장소 탐색기를 엽니다.
    
    1. 저장소 탐색기에서 **Azure 계정 설정**을 선택합니다. 
    
        ![Azure 계정 설정][1]
    
    1. 왼쪽 창에 로그인한 Microsoft 계정이 표시됩니다. 다른 계정에 연결하려면 **계정 추가**를 선택하고 대화 상자를 따라서 하나 이상의 활성 Azure 구독에 연결된 Microsoft 계정을 사용하여 로그인합니다.
    
        ![계정 추가][2]
    
    1. Microsoft 계정으로 성공적으로 로그인하면 왼쪽 창이 해당 계정과 연결된 Azure 구독으로 채워집니다. 작업하려는 Azure 구독을 선택한 후 **적용**을 선택합니다. (나열된 Azure 구독을 모두 선택하거나 하나도 선택하지 않는 **모든 구독** 토글을 선택합니다.)
    
        ![Azure 구독 선택][3]
    
    1. 왼쪽 창은 선택한 Azure 구독과 연결된 저장소 계정을 표시합니다.
    
        ![선택한 Azure 구독][4]

1. 랩의 저장소 계정 찾기:

    1. Storage 계정 왼쪽 창에서 랩을 소유한 Azure 구독에 해당하는 노드를 찾아 확장합니다.
    
    1. 구독 노드 아래에서 **Storage 계정**을 확장합니다.

    1. **Blob 컨테이너**, **파일 공유**, **큐** 및 **테이블**에 대한 노드가 나타나도록 랩의 저장소 계정 노드를 확장합니다.
    
    1. **Blob 컨테이너** 노드를 확장합니다.
    
    1. 오른쪽 창에 내용을 표시할 업로드 Blob 컨테이너를 선택합니다.
        
        ![업로드 디렉터리][5]

1. 저장소 탐색기를 사용하여 VHD 파일 업로드

    1. 스토리지 탐색기 오른쪽 창에 랩의 Storage 계정에 대한 **업로드** Blob 컨테이너에 Blob 목록이 표시됩니다. Blob 편집기 도구 모음에서 **업로드**를 선택합니다. 
        
        ![업로드 단추][6]
    
    1. **업로드** 드롭다운 메뉴에서 **파일 업로드...** 를 선택합니다.
    
    1. **파일 업로드** 대화 상자에서 줄임표를 선택합니다.
        
        ![파일 선택][8]  

    1. **업로드할 파일 선택** 대화 상자에서 원하는 VHD 파일을 찾아 선택한 후 **열기**를 선택합니다.
    
    1. **파일 업로드** 대화 상자로 돌아가 **Blob 유형**을 **페이지 Blob**으로 변경합니다.
    
    1. **업로드**를 선택합니다.

        ![파일 선택][9]  
    
    1. 저장소 탐색기 **활동 로그** 창에 업로드 취소 링크와 함께 다운로드 상태가 표시됩니다. VHD 파일을 업로드하는 프로세스는 VHD 파일 크기 및 연결 속도에 따라 시간이 오래 걸릴 수 있습니다. 

        ![파일 업로드 상태][10]  

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지 만들기](devtest-lab-create-template.md)
- [PowerShell을 사용하여 VHD 파일에서 Azure DevTest Labs에 사용자 지정 이미지 만들기](devtest-lab-create-custom-image-from-vhd-using-powershell.md)

[0]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-image-using-psh.png
[1]: ./media/devtest-lab-upload-vhd-using-storage-explorer/settings-icon.png
[2]: ./media/devtest-lab-upload-vhd-using-storage-explorer/add-account-link.png
[3]: ./media/devtest-lab-upload-vhd-using-storage-explorer/subscriptions-list.png
[4]: ./media/devtest-lab-upload-vhd-using-storage-explorer/storage-accounts-list.png
[5]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-dir.png
[6]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-button.png
[7]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-files.png
[8]: ./media/devtest-lab-upload-vhd-using-storage-explorer/select-file.png
[9]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-file.png
[10]: ./media/devtest-lab-upload-vhd-using-storage-explorer/upload-status.png
