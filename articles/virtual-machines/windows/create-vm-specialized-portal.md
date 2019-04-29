---
title: Azure Portal의 특수한 VHD에서 Windows VM 만들기 | Microsoft Docs
description: Azure Portal의 VHD에서 새 Windows VM을 만듭니다.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: b24435501ed1312e91ebec9b9c434971dbc94b55
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61402307"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Azure Portal을 사용하여 VHD에서 VM 만들기

Azure에서 VM(가상 머신)을 만드는 방법은 여러 가지가 있습니다. 

- 사용할 VHD(가상 하드 디스크)가 이미 있거나 사용할 기존 VM에서 VHD를 복사하려는 경우, VHD를 새 VM에 OS 디스크로 *연결하여* 새 VM을 만들 수 있습니다. 

- 삭제된 VM의 VHD에서 새 VM을 만들 수 있습니다. 예를 들어, 제대로 작동하지 않는 Azure VM이 있는 경우 해당 VM을 삭제하고 VHD를 사용하여 새 VM을 만들 수 있습니다. 동일한 VHD를 다시 사용하거나, 스냅숏을 만든 다음, 스냅숏에서 새로운 관리 디스크를 만들어 VHD의 복사본을 만들 수 있습니다. 스냅숏을 만드는 데는 몇 가지 추가 단계가 있지만 원본 VHD가 유지되고 대체(fallback)를 통해 제공됩니다.

- 클래식 VM 및 VHD를 사용하여 Resource Manager 배포 모델 및 관리 디스크를 사용하는 새 VM을 만듭니다. 최상의 결과를 위해 스냅숏을 만들기 전에 Azure Portal에서 클래식 VM을 **중지**합니다.
 
- 온-프레미스 VHD를 업로드하고 새 VM에 연결하여 온-프레미스 VHD에서 Azure VM을 만들 수 있습니다. PowerShell 또는 다른 도구를 사용하여 VHD를 저장소 계정에 업로드한 다음, VHD에서 관리 디스크를 만듭니다. 자세한 내용은 [특수 VHD 업로드](create-vm-specialized.md#option-2-upload-a-specialized-vhd)를 참조하세요. 

여러 VM을 만들려는 경우 특수 디스크를 사용하지 마세요. 대신 대규모 배포의 경우 [이미지를 만든](capture-image-resource.md) 다음, [해당 이미지를 사용하여 여러 VM을 만듭니다](create-vm-generalized-managed.md).


## <a name="copy-a-disk"></a>디스크 복사

스냅숏을 생성한 다음, 스냅숏에서 디스크를 만듭니다. 이 전략을 사용하면 원본 VHD를 대체(fallback)용으로 유지할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 왼쪽 메뉴에 있는 **모든 서비스**를 선택합니다.
2. **모든 서비스** 검색 상자에 **디스크**를 입력한 다음, **디스크**를 선택하여 사용 가능한 디스크의 목록을 표시합니다.
3. 사용하려는 디스크를 선택합니다. 디스크에 대한 **디스크** 페이지가 표시됩니다.
4. 맨 위에 있는 메뉴에서 **스냅숏 만들기**를 선택합니다. 
5. 스냅숏의 **이름**을 입력합니다.
6. 스냅숏에 대한 **리소스 그룹**을 선택합니다. 기존 리소스 그룹을 사용하거나 새로 만들 수 있습니다.
7. **계정 유형**의 경우 **표준(HDD)** 또는 **프리미엄(SSD)** 저장소를 선택합니다.
8. 완료되면 **만들기**를 선택하여 스냅숏을 만듭니다.
9. 스냅숏이 만들어진 후 왼쪽 메뉴에서 **리소스 만들기**를 선택합니다.
10. 검색 상자에 **관리 디스크**를 입력한 다음, 목록에서 **Managed Disks**를 선택합니다.
11. **Managed Disks** 페이지에서 **만들기**를 선택합니다.
12. 디스크에 사용할 **이름**을 입력합니다.
13. 디스크에 대한 **리소스 그룹**을 선택합니다. 기존 리소스 그룹을 사용하거나 새로 만들 수 있습니다. 이 선택 항목은 디스크에서 VM을 만드는 경우의 리소스 그룹으로 사용됩니다.
14. **계정 유형**의 경우 **표준(HDD)** 또는 **프리미엄(SSD)** 저장소를 선택합니다.
15. **원본 유형**에서 **스냅숏**을 선택했는지 확인합니다.
16. **원본 스냅숏** 드롭다운 목록에서 사용할 스냅숏을 선택합니다.
17. 필요에 따라 기타 조정을 수행한 다음, **만들기**를 선택하여 디스크를 만듭니다.

## <a name="create-a-vm-from-a-disk"></a>디스크에서 VM 만들기

사용하려는 관리 디스크 VHD가 있으면 포털에서 VM을 만들 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에서 왼쪽 메뉴에 있는 **모든 서비스**를 선택합니다.
2. **모든 서비스** 검색 상자에 **디스크**를 입력한 다음, **디스크**를 선택하여 사용 가능한 디스크의 목록을 표시합니다.
3. 사용하려는 디스크를 선택합니다. 해당 디스크에 대한 **디스크** 페이지가 열립니다.
4. **개요** 페이지에서 **디스크 상태**가 **연결되지 않음**으로 표시되는지 확인합니다. 그렇지 않으면 VM에서 디스크를 분리하거나 VM을 삭제하여 디스크 공간을 확보해야 합니다.
4. 페이지 위쪽에 있는 메뉴에서 **VM 만들기**를 선택합니다.
5. 새 VM에 대한 **기본 사항** 페이지에서 **가상 머신 이름**을 입력하고 기존 **리소스 그룹**을 선택하거나 새 리소스 그룹을 만듭니다.
6. **크기**의 경우 **크기 변경**을 선택하여 **크기** 페이지에 액세스합니다.
7. VM 크기 행을 선택한 다음, **선택**을 선택합니다.
8. **네트워킹** 페이지에서 포털이 모든 새 리소스를 만들도록 하거나, 기존 **가상 네트워크** 및 **네트워크 보안 그룹**을 선택할 수 있습니다. 포털은 항상 새 VM에 대한 새 네트워크 인터페이스 및 공용 IP 주소를 만듭니다. 
9. **관리** 페이지에서 모니터링 옵션을 변경합니다.
10. **게스트 구성** 페이지에서 필요에 따라 확장을 추가합니다.
11. 완료되면 **검토 + 만들기**를 선택합니다. 
12. VM 구성이 유효성 검사를 통과하는 경우 **만들기**를 선택하여 배포를 시작합니다.

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 [VHD를 Azure에 업로드하고 특수한 VM을 만들](create-vm-specialized.md) 수도 있습니다.


