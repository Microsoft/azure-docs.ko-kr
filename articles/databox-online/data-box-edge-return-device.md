---
title: Azure Data Box Edge 장치 반환 또는 교체 | Microsoft Docs
description: Azure Data Box Edge 장치를 반환 하거나 교체 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 07/19/2019
ms.author: alkohli
ms.openlocfilehash: a5fee604a529e9ca6153f6c189f199577ae65426
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/19/2019
ms.locfileid: "68356153"
---
# <a name="return-or-replace-your-azure-data-box-edge-device"></a>Azure Data Box Edge 장치 반환 또는 교체

이 문서에서는 데이터를 초기화 한 다음 Azure Data Box Edge 장치를 반환 하는 방법을 설명 합니다. 장치를 반환한 후에는 장치와 연결 된 리소스를 삭제 하거나 교체 장치를 주문할 수도 있습니다.

이 문서에서는 다음 방법을 설명합니다.

> [!div class="checklist"]
> * 장치의 데이터 디스크에서 데이터를 초기화 합니다.
> * 지원 티켓을 열어 장치를 반환 합니다.
> * 장치를 압축 하 고 픽업 예약
> * Azure Portal에서 리소스를 삭제 합니다.
> * 교체 장치 가져오기

## <a name="erase-data-from-the-device"></a>장치에서 데이터 지우기

장치의 데이터 디스크에서 데이터를 초기화 하려면 장치를 다시 설정 해야 합니다. 로컬 웹 UI 또는 PowerShell 인터페이스를 사용 하 여 장치를 다시 설정할 수 있습니다.

다시 설정 하기 전에 필요한 경우 장치에서 로컬 데이터의 복사본을 만듭니다. 장치에서 Azure Storage 컨테이너로 데이터를 복사할 수 있습니다.

로컬 웹 UI를 사용 하 여 장치를 다시 설정 하려면 다음 단계를 수행 합니다.

1. 로컬 웹 UI에서 **유지 관리 > 장치 다시 설정**으로 이동 합니다.
2. **장치 다시 설정**을 선택 합니다.

    ![디바이스 다시 설정](media/data-box-edge-return-device/device-reset-1.png)

3. 확인 메시지가 표시 되 면 경고를 검토 하 고 **예** 를 선택 하 여 계속 합니다.

    ![다시 설정 확인](media/data-box-edge-return-device/device-reset-2.png)  

다시 설정 하면 장치 데이터 디스크에서 데이터가 지워집니다. 이 프로세스는 장치에 있는 데이터의 양에 따라 약 30-40 분이 소요 됩니다.

또는 장치의 PowerShell 인터페이스에 연결 하 고 `Reset-HcsAppliance` cmdlet을 사용 하 여 데이터 디스크에서 데이터를 지웁니다. 자세한 내용은 [장치 다시 설정](data-box-edge-connect-powershell-interface.md#reset-your-device)을 참조 하세요.

> [!NOTE]
> - 새 장치를 교환 하거나 새 장치를 업그레이드 하는 경우 새 장치를 받은 후에만 장치를 초기화 하는 것이 좋습니다.
> - 장치 재설정은 장치에서 모든 로컬 데이터를 삭제 합니다. 클라우드에 있는 데이터는 삭제 되지 않으며 [요금](https://azure.microsoft.com/pricing/details/storage/)을 수집 합니다. [Azure Storage 탐색기](https://azure.microsoft.com/features/storage-explorer/)와 같은 클라우드 저장소 관리 도구를 사용 하 여이 데이터를 별도로 삭제 해야 합니다.

## <a name="open-a-support-ticket"></a>지원 티켓 열기

반환 프로세스를 시작 하려면 다음 단계를 수행 합니다.

1. 장치를 반환할지를 나타내는 Microsoft 지원 지원 티켓을 엽니다. **Data Box Edge 하드웨어**로 문제 유형을 선택 합니다.

    ![지원 티켓 열기](media/data-box-edge-return-device/open-support-ticket-1.png)  

2. Microsoft 지원 엔지니어가 사용자에 게 연락 하 게 됩니다. 배송 세부 정보를 제공 합니다.
3. 반송 운송 상자가 필요한 경우 요청할 수 있습니다. **예** 를 들어, **반환할 빈 상자가 필요한**질문에 답변 합니다.


## <a name="schedule-a-pickup"></a>픽업 예약

1. 디바이스를 종료합니다. 로컬 웹 UI에서 **유지 관리 > 전원 설정**으로 이동합니다.
2. **종료를**선택 합니다. 확인 메시지가 표시 되 면 **예** 를 클릭 하 여 계속 합니다. 자세한 내용은 [전원 관리](data-box-gateway-manage-access-power-connectivity-mode.md#manage-power)를 참조 하세요.
3. 전원 케이블을 분리 하 고 장치에서 모든 네트워크 케이블을 제거 합니다.
4. 자신의 상자 또는 Azure에서 받은 빈 상자를 사용 하 여 배송 패키지를 준비 합니다. 장치와 함께 제공 된 장치 및 전원 코드를 상자에 배치 합니다.
5. 접사 패키지의 Azure에서 받은 배송 레이블을 제공 합니다.
6. 해당 지역 운송업체에서 픽업하도록 예약합니다. 장치를 미국에서 반환 하는 경우에는 캐리어가 UPS입니다. 픽업을 예약하려면 다음을 수행합니다.

    1. 로컬 UPS(국가별 무료 전화 번호)에 전화합니다.
    2. 호출에서 인쇄 된 레이블에 표시 된 것 처럼 역 배송 추적 번호를 인용 합니다.
    3. 추적 번호가 따옴표로 묶여 있지 않으면 pickup에서 추가 요금을 지불 해야 합니다.

    픽업을 예약 하는 대신 가장 가까운 드롭다운 위치에 Data Box Edge를 놓을 수도 있습니다.

## <a name="delete-the-resource"></a>리소스 삭제

Azure 데이터 센터에서 장치를 받은 후에는 장치에서 손상이 나 변조 징후가 검사 됩니다.

- 장치가 그대로 도착 하 고 좋은 모양으로 청구 되 면 해당 리소스에 대한 청구 측정기가 중지 됩니다. Microsoft 지원는 장치가 반환 되었는지 확인 하기 위해 사용자에 게 연락 합니다. 그런 다음 Azure Portal 장치와 연결 된 리소스를 삭제할 수 있습니다.
- 장치가 심각한 손상에 도달 하면 벌금가 적용 될 수 있습니다. 자세한 내용은 [분실 또는 손상 된 장치](https://azure.microsoft.com/pricing/details/databox/edge/) 및 [제품 서비스 약관](https://www.microsoft.com/licensing/product-licensing/products)에 대한 FAQ를 참조 하십시오.  


Azure Portal에서 장치를 삭제할 수 있습니다.
-   주문을 하 고 장치를 Microsoft에서 준비 하기 전에
-   장치를 Microsoft에 반환 하면 Azure 데이터 센터에서 물리적 검사를 통과 하 고를 호출 하 여 장치가 반환 되었는지 확인 Microsoft 지원.

다른 구독 또는 위치에 대해 장치를 활성화 한 경우 Microsoft는 한 영업일 내에 새 구독 또는 위치로 주문을 이동 합니다. 주문이 이동 된 후에는이 리소스를 삭제할 수 있습니다.


Azure Portal에서 장치 및 리소스를 삭제 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 리소스로 이동한 다음 **개요**로 이동 합니다. 명령 모음에서 **삭제**를 선택 합니다.

    ![삭제 선택](media/data-box-edge-return-device/delete-resource-1.png)

2. **장치 삭제** 블레이드에서 삭제 하려는 장치의 이름을 입력 하 고 **삭제**를 선택 합니다.

    ![삭제 확인](media/data-box-edge-return-device/delete-resource-2.png)

장치 및 연결 된 리소스가 성공적으로 삭제 된 후 알림이 표시 됩니다.

## <a name="get-a-replacement-device"></a>교체 장치 가져오기

기존 장치에 하드웨어 오류가 있거나 업그레이드가 필요한 경우 교체 장치가 필요 합니다. 장치에 하드웨어 문제가 있는 경우 다음 단계를 수행 합니다.

1. [하드웨어 문제에 대한 지원 티켓을 엽니다](#open-a-support-ticket). Microsoft 지원는이 인스턴스에 대해 FRU (필드 대체 단위)를 사용할 수 없거나 장치에 하드웨어 업그레이드가 필요한 지 여부를 결정 합니다. 두 경우 모두 지원에서 대체 장치를 주문 합니다.
2. 교체 장치에 대한 [새 리소스를 만듭니다](data-box-edge-deploy-prep.md#create-a-new-resource) . **Data Box Edge 장치**에 대한 확인란을 선택 해야 합니다. 
3. 교체 장치를 받은 후에는 새 리소스에 대해 교체 장치를 [설치](data-box-edge-deploy-install.md) 하 고 [활성화](data-box-edge-deploy-connect-setup-activate.md) 합니다.
4. 모든 단계를 수행 하 여 원래 장치를 반환 합니다.
    1. 다른 티켓을 열어 원래 장치를 반환 합니다.
    2. [장치에서 데이터를 지웁니다](#erase-data-from-the-device).
    3. [픽업을 예약](#schedule-a-pickup)합니다.
    5. 반환 된 장치와 연결 된 [리소스를 삭제](#delete-the-resource) 합니다.



## <a name="next-steps"></a>다음 단계

- [대역폭을 관리](data-box-edge-manage-bandwidth-schedules.md)하는 방법에 대해 알아봅니다.
