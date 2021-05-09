---
title: Azure Stack Edge Pro GPU 사용자 관리 | Microsoft Docs
description: Azure Portal을 사용하여 Azure Stack Edge Pro GPU에서 사용자를 관리하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/21/2021
ms.author: alkohli
ms.openlocfilehash: 5aeb4f2d7152120ea92a8df76f980eb3baed6e50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2021
ms.locfileid: "102638546"
---
# <a name="use-the-azure-portal-to-manage-users-on-your-azure-stack-edge-pro"></a>Azure Portal을 사용하여 Azure Stack Edge Pro에서 사용자 관리

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro에서 사용자를 관리하는 방법을 설명합니다. Azure Portal 또는 로컬 웹 UI를 통해 Azure Stack Edge Pro를 관리할 수 있습니다. Azure Portal을 사용하여 사용자를 추가, 수정 또는 삭제합니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 사용자 추가
> * 사용자 수정
> * 사용자 삭제

## <a name="about-users"></a>사용자에 대한 정보

사용자는 읽기 전용 또는 전체 권한 사용자가 될 수 있습니다. 읽기 전용 사용자는 공유 데이터만 볼 수 있습니다. 전체 권한 사용자는 공유 데이터를 읽고, 해당 공유에 쓰고, 공유 데이터를 수정하거나 삭제할 수 있습니다.

 - **전체 권한 사용자** - 전체 액세스 권한이 있는 로컬 사용자입니다.
 - **읽기 전용 사용자** - 읽기 전용 액세스 권한이 있는 로컬 사용자입니다. 이러한 사용자는 읽기 전용 작업을 허용하는 공유와 연결됩니다.

사용자 권한은 공유를 생성하는 동안 사용자를 만들 때 처음 정의됩니다. 파일 탐색기를 사용하여 수정할 수 있습니다.


## <a name="add-a-user"></a>사용자 추가

사용자를 추가하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 다음 **사용자** 로 이동합니다. 명령 모음에서 **+ 사용자 추가** 를 선택합니다.

    ![사용자 추가 선택](media/azure-stack-edge-gpu-manage-users/add-user-1.png)

2. 추가하려는 사용자에 대한 사용자 이름 및 암호를 지정합니다. 암호를 확인하고 **추가** 를 선택합니다.

    ![사용자 이름 및 암호 지정](media/azure-stack-edge-gpu-manage-users/add-user-2.png)

    > [!IMPORTANT] 
    > 다음과 같은 사용자는 시스템에 예약되어 있으므로 사용하지 말아야 합니다. Administrator, EdgeUser, EdgeSupport, HcsSetupUser, WDAGUtilityAccount, CLIUSR, DefaultAccount, Guest.  

3. 사용자 만들기가 시작되고 완료되면 알림이 표시됩니다. 사용자가 만들어지면 명령 모음에서 **새로 고침** 을 선택하여 업데이트된 사용자 목록을 볼 수 있습니다.


## <a name="modify-user"></a>사용자 수정

사용자가 만들어진 후에 사용자와 연결된 암호를 변경할 수 있습니다. 사용자 목록에서 선택합니다. 새 암호를 입력하고 확인합니다. 변경 내용을 저장합니다.

![사용자 수정](media/azure-stack-edge-gpu-manage-users/modify-user-1.png)


## <a name="delete-a-user"></a>사용자 삭제

사용자를 삭제하려면 Azure Portal에서 다음 단계를 수행합니다.


1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 다음 **사용자** 로 이동합니다.

    ![삭제할 사용자 선택](media/azure-stack-edge-gpu-manage-users/delete-user-1.png)

2. 사용자 목록에서 사용자를 선택한 다음, **삭제** 를 선택합니다. 메시지가 표시되면 삭제를 확인합니다.

    ![삭제할 사용자 선택 2](media/azure-stack-edge-gpu-manage-users/delete-user-2.png)

사용자 목록이 업데이트되어 삭제된 사용자가 반영됩니다.

![업데이트된 사용자 목록](media/azure-stack-edge-gpu-manage-users/delete-user-4.png)

## <a name="next-steps"></a>다음 단계

- [대역폭을 관리](azure-stack-edge-gpu-manage-bandwidth-schedules.md)하는 방법에 대해 알아봅니다.
