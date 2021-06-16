---
title: Azure Data Box Disk 주문 자습서 | Microsoft Docs
description: 이 자습서를 사용하여 Azure로 데이터를 가져오기 위해 Azure Data Box Disk에 등록하고 주문하는 방법을 알아봅니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 06/10/2021
ms.author: alkohli
ms.openlocfilehash: bb6f7cc8331d242459d5af515919db13277a5f5e
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111985745"
---
# <a name="tutorial-order-an-azure-data-box-disk"></a>자습서: Azure Data Box Disk 주문

Azure Data Box Disk는 빠르고 쉽게 신뢰할 수 있는 방식으로 온-프레미스 데이터를 Azure로 가져올 수 있는 하이브리드 클라우드 솔루션입니다. 데이터를 Microsoft에서 제공하는 SSD(반도체 디스크)로 전송하고 이 디스크를 반송합니다. 그런 다음, 이 데이터는 Azure에 업로드됩니다.

이 자습서에서는 Azure Data Box Disk를 주문하는 방법에 대해 설명합니다. 이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
>
> * Data Box Disk 주문
> * 주문 추적
> * 주문 취소

## <a name="prerequisites"></a>필수 구성 요소

디바이스를 배포하기 전에, Data Box 서비스 및 Data Box Disk에 대한 구성 필수 조건을 완료해야 합니다.

### <a name="for-service"></a>서비스의 경우

[!INCLUDE [Data Box service prerequisites](../../includes/data-box-supported-subscriptions.md)]

### <a name="for-device"></a>디바이스의 경우

시작하기 전에 다음 사항을 확인합니다.

* 데이터를 복사할 수 있는 클라이언트 컴퓨터가 있습니다. 클라이언트 컴퓨터는 다음 작업을 수행해야 합니다.
  * [지원되는 운영 체제](data-box-disk-system-requirements.md#supported-operating-systems-for-clients)를 실행합니다.
  * Windows 클라이언트인 경우 다른 [필수 소프트웨어](data-box-disk-system-requirements.md#other-required-software-for-windows-clients)를 설치합니다.  

## <a name="order-data-box-disk"></a>Data Box Disk 주문

로그인합니다.

* Azure Portal(https://portal.azure.com )에 로그인하여 Data Box Disk를 주문합니다.
* 또는 URL https://portal.azure.us 에서 Azure Government 포털에 로그인합니다. 자세한 내용을 보려면 [포털을 사용하여 Azure Government에 연결](../azure-government/documentation-government-get-started-connect-with-portal.md)로 이동합니다.

Data Box Disk를 주문하려면 다음 단계를 수행합니다.

1. 포털의 왼쪽 위 모서리에서 **+ 리소스 만들기** 를 클릭하고 *Azure Data Box* 를 검색합니다. **Azure Data Box** 를 클릭합니다.

   ![Azure Data Box 1 검색](media/data-box-disk-deploy-ordered/search-data-box11.png)

2. **만들기** 를 클릭합니다.

3. 해당 지역에서 Data Box 서비스를 사용할 수 있는지 확인합니다. 다음 정보를 입력하거나 선택하고 **적용** 을 클릭합니다.

    ![Data Box Disk 선택 옵션](media/data-box-disk-deploy-ordered/select-data-box-sku-1.png)

    |설정|값|
    |---|---|
    |Subscription|Data Box 서비스를 사용하도록 설정된 구독을 선택합니다.<br> 구독은 대금 청구 계정에 연결됩니다. |
    |전송 형식| Azure로 가져오기|
    |원본 국가/지역 | 현재 데이터가 있는 국가/지역을 선택합니다.|
    |대상 Azure 지역|데이터를 전송하려는 Azure 지역을 선택합니다.|
  
4. **Data Box Disk** 를 선택합니다. 단일 5개 디스크 주문에 대한 솔루션의 최대 용량은 35TB입니다. 더 큰 데이터 크기에 대해 여러 개의 주문을 만들 수 있습니다.

     ![Data Box Disk 선택 옵션 2](media/data-box-disk-deploy-ordered/select-data-box-sku-zoom.png)

5. **주문** 에서 **주문 세부 정보** 를 지정합니다. 다음 정보를 입력하거나 선택합니다.

    |설정|값|
    |---|---|
    |속성|주문을 추적하는 데 친숙한 이름을 입력합니다.<br> 이 이름은 2~24자 사이의 문자, 숫자 및 하이픈일 수 있습니다. <br> 이름은 문자 또는 숫자로 시작하고 끝나야 합니다. |
    |Resource group| 기존 그룹을 사용하거나 새 그룹을 만듭니다. <br> 리소스 그룹은 함께 관리하거나 배포할 수 있는 리소스에 대한 논리 컨테이너입니다. |
    |대상 Azure 지역| 스토리지 계정에 대한 지역을 선택합니다.<br> 현재 미국, 서유럽, 북유럽, 캐나다 및 오스트레일리아 모든 지역의 스토리지 계정이 지원됩니다. |
    |TB 단위의 데이터 크기 추정됨| 추정값을 TB 단위로 입력합니다. <br>데이터 크기에 따라 Microsoft에서 적절한 개수의 8TB SSD(사용 가능한 용량: 7TB)를 보냅니다. <br>5개 디스크에서 사용 가능한 최대 용량은 최대 35TB입니다. |
    |디스크 암호| **Azure에서 생성한 암호가 아닌 사용자 지정 키 사용** 을 선택하는 경우 디스크 암호를 제공합니다. <br> 적어도 하나의 숫자 및 하나의 특수 문자를 포함한 12~32개 영숫자 키를 제공합니다. 허용되는 특수 문자는 `@?_+`입니다. <br> 이 옵션을 건너뛰고 Azure에서 생성된 암호를 사용하여 디스크의 잠금을 해제하도록 선택할 수 있습니다.|
    |스토리지 대상     | 스토리지 계정이나 관리형 디스크 또는 둘 다를 선택합니다. <br> 지정한 Azure 지역에 따라 필터링된 기존 스토리지 계정 목록에서 스토리지 계정을 선택합니다. Data Box Disk는 1개의 스토리지 계정에만 연결할 수 있습니다. <br> 새 **범용 v1**, **범용 v2** 또는 **Blob Storage 계정** 도 만들 수 있습니다. <br>가상 네트워크를 사용하는 스토리지 계정은 지원됩니다. Data Box 서비스에서 보안 스토리지 계정을 사용하려면 스토리지 계정 네트워크 방화벽 설정 내에서 신뢰할 수 있는 서비스를 사용하도록 설정합니다. 자세한 내용은 [Azure Data Box를 신뢰할 수 있는 서비스로 추가](../storage/common/storage-network-security.md#exceptions)하는 방법을 참조하세요.|

    스토리지 계정을 스토리지 대상으로 사용하는 경우 다음 스크린샷을 참조하세요.

    ![스토리지 계정에 대한 Data Box Disk 주문](media/data-box-disk-deploy-ordered/order-storage-account.png)

    Data Box Disk를 사용하여 온-프레미스 VHD에서 관리형 디스크를 만드는 경우에도 다음 정보를 제공해야 합니다.

    |설정  |값  |
    |---------|---------|
    |Resource group     | 온-프레미스 VHD에서 관리형 디스크를 만들려는 경우 새 리소스 그룹을 만듭니다. 기존 리소스 그룹은 Data Box 서비스에서 관리형 디스크에 대한 Data Box Disk 주문을 위해 생성된 경우에만 사용합니다. <br> 하나의 리소스 그룹만 지원됩니다.|

    ![관리형 디스크에 대한 Data Box Disk 주문](media/data-box-disk-deploy-ordered/order-managed-disks.png)

    관리형 디스크에 대해 지정한 스토리지 계정은 스테이징 스토리지 계정으로 사용됩니다. Data Box 서비스는 VHD를 스테이징 스토리지 계정으로 업로드한 후 관리형 디스크로 변환하고 리소스 그룹으로 이동합니다. 자세한 내용은 [Azure에 대한 데이터 업로드 확인](data-box-disk-deploy-upload-verify.md#verify-data-upload-to-azure)을 참조하세요.

6. **다음** 을 클릭합니다.

    ![주문 세부 정보 제공](media/data-box-disk-deploy-ordered/data-box-order-details.png)

7. **배송 주소** 탭에서 사용자의 성과 이름, 회사의 이름과 우편 주소 및 유효한 전화 번호를 제공합니다. **주소 확인** 을 클릭합니다. 서비스에서 서비스 가용성을 위해 배송 주소의 유효성을 검사합니다. 지정한 배송 주소에 대해 서비스를 사용할 수 있으면 해당 알림을 받게 됩니다.

   주문이 처리되면 이메일 알림을 받게 됩니다. 자체 관리형 배송을 선택한 경우 [자체 관리형 배송 사용](data-box-disk-portal-customer-managed-shipping.md)을 참조하세요.

    ![배송 주소 제공](media/data-box-disk-deploy-ordered/data-box-shipping-address.png)
8. **알림 세부 정보** 에서 이메일 주소를 지정합니다. 서비스에서는 주문 상태에 대한 모든 업데이트와 관련된 이메일 알림을 지정한 이메일 주소로 보냅니다.

    그룹의 관리자가 떠나는 경우에도 계속 알림을 받으려면 그룹 이메일을 사용하는 것이 좋습니다.

9. 주문, 연락처, 알림 및 개인 정보 처리 방침과 관련된 정보 **요약** 을 검토합니다. 개인 정보 처리 방침에 대한 계약에 해당하는 확인란을 선택합니다.

10. **주문** 을 클릭합니다. 주문을 만드는 데 몇 분 정도 걸립니다.

## <a name="track-the-order"></a>주문 추적

주문이 완료되면 Azure Portal에서 주문 상태를 추적할 수 있습니다. 해당 주문, **개요** 로 차례로 이동하여 상태를 확인합니다. 포털에서 해당 작업을 **주문됨** 상태로 표시합니다.

![Data Box Disk 주문됨 상태](media/data-box-disk-deploy-ordered/data-box-portal-ordered.png)

디스크를 사용할 수 없는 경우 알림을 받습니다. 디스크를 사용할 수 있으면 Microsoft에서 배송할 디스크를 확인하고 디스크 패키지를 준비합니다. 디스크를 준비하는 동안 수행되는 작업은 다음과 같습니다.

* 디스크는 AES-128 BitLocker 암호화를 사용하여 암호화됩니다.  
* 디스크는 디스크에 대한 무단 액세스를 방지하기 위해 잠겨 있습니다.
* 이 과정에서 디스크 잠금을 해제하는 지원 암호가 생성됩니다.

디스크 준비가 완료되면 포털에서 해당 주문을 **처리됨** 상태로 표시합니다.

그런 다음, Microsoft에서 디스크를 준비하고 지역 배송업체를 통해 발송합니다. 디스크가 배송되면 추적 번호를 받게 됩니다. 포털에서 해당 작업을 **발송됨** 상태로 표시합니다.

## <a name="cancel-the-order"></a>주문 취소

이 주문을 취소하려면 Azure Portal에서 **개요** 로 이동하고, 명령 모음에서 **취소** 를 클릭합니다.

디스크가 주문되고 출하를 위해 주문이 배송으로 처리되는 경우에만 취소할 수 있습니다. 주문이 처리되면 더 이상 주문을 취소할 수 없습니다.

![주문 취소](media/data-box-disk-deploy-ordered/cancel-order1.png)

취소된 주문을 삭제하려면 **개요** 로 이동하고, 명령 모음에서 **삭제** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Data Box 항목에 대해 다음과 같은 내용을 알아보았습니다.

> [!div class="checklist"]
>
> * Data Box Disk 주문
> * 주문 추적
> * 주문 취소

Data Box Disk를 설정하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Data Box Disk 설정](./data-box-disk-deploy-set-up.md)