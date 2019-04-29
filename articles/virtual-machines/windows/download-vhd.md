---
title: Azure에서 Windows VHD 다운로드 | Microsoft Docs
description: Azure Portal을 사용하여 Windows VHD를 다운로드합니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 3d44a4a723c39bf9780475a2ac3088da94285f6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61076330"
---
# <a name="download-a-windows-vhd-from-azure"></a>Azure에서 Windows VHD 다운로드

이 문서에서는 Azure Portal을 사용하여 Azure에서 Windows VHD(가상 하드 디스크) 파일을 다운로드하는 방법을 알아봅니다.

## <a name="stop-the-vm"></a>VM을 중지합니다.

실행 중인 VM에 연결된 경우 Azure에서 VHD는 다운로드할 수 없습니다. VHD를 다운로드하려면 VM을 중지해야 합니다. VHD를 [이미지](tutorial-custom-images.md)로 사용하여 새 디스크를 사용하는 다른 VM을 만들려는 경우 [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation)를 사용하여 파일에 포함된 운영 체제를 일반화하고 VM을 중지해야 합니다. VHD를 기존 VM의 새 인스턴스에 대한 디스크 또는 데이터 디스크로 사용하려면 VM을 중지하고 할당 취소해야 합니다.

VHD를 다른 VM을 만들기 위한 이미지로 사용하려면 다음 단계를 완료합니다.

1.  아직 로그인하지 않은 경우 [Azure 포털](https://portal.azure.com/)에 로그인합니다.
2.  [VM에 연결합니다](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3.  VM에서 관리자로 명령 프롬프트 창을 엽니다.
4.  디렉터리를 *%windir%\system32\sysprep*로 변경한 후 sysprep.exe를 실행합니다.
5.  시스템 준비 도구 대화 상자에서 **시스템 OOBE(첫 실행 경험) 입력**을 선택하고 **일반화** 확인란을 선택했는지 확인합니다.
6.  종료 옵션에서 **종료**를 선택한 다음 **확인**을 클릭합니다. 

VHD를 기존 VM의 새 인스턴스에 대한 디스크 또는 데이터 디스크로 사용하려면 다음 단계를 완료합니다.

1.  Azure Portal에 있는 허브 메뉴에서 **Virtual Machines**를 클릭합니다.
2.  목록에서 VM을 선택합니다.
3.  VM에 대한 블레이드에서 **중지**를 클릭합니다.

    ![VM 중지](./media/download-vhd/export-stop.png)

## <a name="generate-sas-url"></a>SAS URL 생성

VHD 파일을 다운로드하려면 [SAS(공유 액세스 서명)](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) URL을 생성해야 합니다. URL이 생성될 때 만료 시간이 URL에 할당됩니다.

1.  VM에 대한 블레이드 메뉴에서 **디스크**를 클릭합니다.
2.  VM에 대한 운영 체제 디스크를 선택한 다음 **내보내기**를 클릭합니다.
3.  URL의 만료 시간을 *36000*으로 설정합니다.
4.  **URL 생성**을 클릭합니다.

    ![URL 생성](./media/download-vhd/export-generate.png)

> [!NOTE]
> Windows Server 운영 체제에 대한 큰 VHD 파일을 다운로드하는 데 충분한 시간을 제공하기 위해 만료 시간은 기본 시간에서 증가되었습니다. Windows Server 운영 체제를 포함하는 VHD 파일은 사용자 연결에 따라 다운로드하는 데 몇 시간이 걸릴 수 있습니다. 데이터 디스크에 대한 VHD를 다운로드하는 경우 기본 시간은 충분합니다. 
> 
> 

## <a name="download-vhd"></a>VHD 다운로드

1.  생성된 URL에서 VHD 파일 다운로드를 클릭합니다.

    ![VHD 다운로드](./media/download-vhd/export-download.png)

2.  다운로드를 시작하려면 브라우저에서 **저장**을 클릭해야 합니다. VHD 파일에 대한 기본 이름은 *abcd*입니다.

    ![브라우저에서 저장 클릭](./media/download-vhd/export-save.png)

## <a name="next-steps"></a>다음 단계

- [Azure로 VHD 파일 업로드](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 방법을 알아봅니다. 
- [저장소 계정의 비관리 디스크에서 관리 디스크를 만듭니다](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [PowerShell을 사용하여 Azure 디스크를 관리합니다](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

