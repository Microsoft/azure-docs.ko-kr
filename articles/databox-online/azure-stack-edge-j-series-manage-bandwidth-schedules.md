---
title: Azure Stack Edge GPU 대역폭 일정 관리 | Microsoft Docs
description: Azure Portal를 사용 하 여 Azure Stack Edge GPU에서 대역폭 일정을 관리 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 92b9edb4f694a5ff7d15b06be41cc20db3b64b97
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265697"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-gpu"></a>Azure Portal를 사용 하 여 Azure Stack Edge GPU에서 대역폭 일정을 관리 합니다. 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

이 문서에서는 Azure Stack Edge에서 대역폭 일정을 관리 하는 방법을 설명 합니다. 대역폭 일정을 사용하면 여러 시간대의 일정에 대해 네트워크 대역폭 사용량을 구성할 수 있습니다. 이러한 일정은 디바이스에서 클라우드로 업로드 및 다운로드 작업에 적용할 수 있습니다.

Azure Portal를 통해 Azure Stack에 지에 대 한 대역폭 일정을 추가, 수정 또는 삭제할 수 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 일정 추가
> * 일정 수정
> * 일정 삭제


## <a name="add-a-schedule"></a>일정 추가

일정을 추가하려면 Azure Portal에서 다음 단계를 수행합니다.

1. Azure Stack에 지 리소스에 대 한 Azure Portal에서 **대역폭**으로 이동 합니다.
2. 오른쪽 창에서 **+ 일정 추가**를 선택합니다.

    ![대역폭 선택](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-1.png)

3. **일정 추가**에서: 

   1. 일정에 대한 **시작일**, **종료일**, **시작 시간** 및 **종료 시간**을 입력합니다.
   2. 이 일정이 하루 종일 실행되어야 하는 경우 **하루 종일** 옵션을 확인합니다.
   3. **대역폭 속도**는 클라우드와 관련된 작업(업로드 및 다운로드 모두)의 디바이스에 사용되는 초당 메가비트(Mbps)의 대역폭입니다. 이 필드에 20에서 2147483647 사이의 숫자를 입력 합니다.
   4. 업로드 및 다운로드 날짜를 제한하지 않으려면 **무제한 대역폭**을 선택합니다.
   5. **추가**를 선택합니다.

      ![일정 추가](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-2.png)

3. 지정한 매개 변수로 일정이 만들어집니다. 이 일정은 포털의 대역폭 일정 목록에 표시됩니다.

    ![업데이트된 대역폭 일정 목록](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>일정 편집

대역폭 일정을 편집하려면 다음 단계를 수행합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 후 **대역폭**으로 이동 합니다. 
2. 대역폭 일정 목록에서 수정 하려는 일정을 선택 합니다.
    ![대역폭 일정 선택](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-1.png)

3. 원하는 대로 변경하고 변경 내용을 저장합니다.

    ![사용자 수정](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-2.png)

4. 일정이 수정된 후 일정 목록이 업데이트되어 수정된 일정이 반영됩니다.

    ![사용자 수정](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>일정 삭제

Azure Stack Edge 장치와 연결 된 대역폭 일정을 삭제 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 Azure Stack Edge 리소스로 이동한 후 **대역폭**으로 이동 합니다.  

2. 대역폭 일정 목록에서 삭제할 일정을 선택합니다. **일정 편집**에서 **삭제**를 선택합니다. 확인하라는 메시지가 표시되면 **예**를 선택합니다.

   ![사용자 삭제](media/azure-stack-edge-j-series-manage-bandwidth-schedules/delete-schedule-2.png)

3. 일정 삭제된 후에는 일정 목록이 업데이트 됩니다.


## <a name="next-steps"></a>다음 단계

- [공유 관리](azure-stack-edge-j-series-manage-shares.md) 방법에 대해 알아봅니다.
