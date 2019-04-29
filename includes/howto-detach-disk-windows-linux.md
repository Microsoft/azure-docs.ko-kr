---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/25/2018
ms.author: cynthn
ms.openlocfilehash: c97c94492417dcc87d34751908f1766393ad37ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61476256"
---
가상 컴퓨터에 연결된 데이터 디스크가 더 이상 필요하지 않은 경우 쉽게 분리할 수 있습니다. 디스크를 분리하면 가상 머신에서 디스크가 제거되지만 Azure 저장소 계정에서 디스크를 삭제하지 않습니다.

디스크에 있는 기존 데이터를 다시 사용하려는 경우 동일한 또는 다른 가상 머신에 다시 연결할 수 있습니다.  

> [!NOTE]
> 운영 체제 디스크를 분리하려면 먼저 가상 컴퓨터를 삭제해야 합니다.
>

## <a name="find-the-disk"></a>디스크 찾기
디스크의 이름을 모르거나, 분리하기 전에 이름을 확인하려면 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **Virtual Machines**를 클릭한 다음 적절한 VM을 선택합니다.

3. **설정** 아래에서 가상 머신 대시보드의 왼쪽 가장자리를 따라 **디스크**를 클릭합니다.

 모든 연결된 디스크의 이름 및 형식이 가상 머신 대시보드에 나열됩니다. 예를 들어, 이 화면에는 한 개의 운영 체제(OS) 디스크와 한 개의 데이터 디스크가 있는 가상 컴퓨터가 표시됩니다.

    ![데이터 디스크 찾기](./media/howto-detach-disk-windows-linux/vmwithdisklist.png)

## <a name="detach-the-disk"></a>디스크 분리
1. Azure Portal에서 **Virtual Machines**를 클릭하고 분리하려는 데이터 디스크가 있는 가상 머신의 이름을 클릭합니다.

2. **설정** 아래에서 가상 머신 대시보드의 왼쪽 가장자리를 따라 **디스크**를 클릭합니다.

3. 분리하려는 디스크를 클릭합니다.

  ![분리하려는 디스크 식별](./media/howto-detach-disk-windows-linux/disklist.png)

4. 명령 모음에서 **분리**를 클릭합니다.

  ![분리 명령 찾기](./media/howto-detach-disk-windows-linux/diskdetachcommand.png)

5. 확인 창에서 **예**를 클릭하여 디스크를 분리합니다.

  ![디스크 분리 확인](./media/howto-detach-disk-windows-linux/confirmdetach.png)

디스크가 저장소에 유지되지만 더 이상 가상 머신에 연결되어 있지 않습니다.
