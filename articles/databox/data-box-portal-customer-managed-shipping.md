---
title: Microsoft Azure Data Box 자체 관리형 배송 | Microsoft Docs
description: Azure Data Box 디바이스용 자체 관리형 배송 워크플로에 대해 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: cfa2dee41f07e273d933da356f74063453cb1775
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/12/2020
ms.locfileid: "88142539"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Azure Portal에서 Azure Data Box용 자체 관리형 배송 사용

이 문서에서는 Azure Data Box 디바이스를 주문, 픽업 및 반납하기 위한 자체 관리형 배송 태스크에 대해 설명합니다. Azure Portal에서 Data Box 디바이스를 관리할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

[Azure Data Box 주문](data-box-deploy-ordered.md) 시 자체 관리형 배송을 옵션으로 사용할 수 있습니다. 자체 관리형 배송은 다음 지역에서만 사용할 수 있습니다.

* 미국 정부
* 서부 유럽
* 일본
* 싱가포르
* 대한민국
* 인도
* 남아프리카 공화국

## <a name="use-self-managed-shipping"></a>자체 관리형 배송 사용

Data Box를 주문할 때 자체 관리형 배송 옵션을 선택할 수 있습니다.

1. Azure Data Box 주문의 **연락처 세부 정보**에서 **+ 배송 주소 추가**를 선택합니다.
   ![자체 관리형 배송](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. 배송 유형을 선택할 때 **자체 관리형 배송** 옵션을 선택합니다. 이 옵션은 필수 구성 요소에 설명된 대로 지원되는 지역에서만 사용할 수 있습니다.

3. 배송 주소를 입력한 후에는 유효한지 검사하고 주문을 완료해야 합니다.
   ![자체 관리형 배송](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. 장치를 준비 하 고 해당 장치에 대 한 전자 메일 알림을 받으면 픽업을 예약할 수 있습니다.

   Azure Data Box 주문에서 **개요**로 이동한 후 **픽업 예약**을 선택합니다.

   ![Data Box 디바이스 픽업 주문](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. **Azure 픽업 예약**의 지침을 따릅니다.

   인증 코드를 받으려면 [adbops@microsoft.com](mailto:adbops@microsoft.com)으로 메일을 보내 지역 데이터 센터에서 디바이스 픽업 일정을 예약해야 합니다.

   ![픽업 예약](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. 디바이스 픽업을 예약한 후에는 **Azure 픽업 예약** 창에서 디바이스 인증 코드를 볼 수 있습니다.

   ![인증 코드 보기](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   **인증 코드**를 적어 둡니다. 보안 요구 사항에 따라 선택 일정을 예약 하는 시점에, 선택에 도착할 사용자의 이름을 제공 해야 합니다.

   픽업 데이터 센터를 방문할 사용자에 대한 세부 정보도 제공해야 합니다. 본인 또는 연락 담당자에게 데이터 센터에서 유효성을 검사할 정부 승인 사진 ID가 있어야 합니다.

   또한 디바이스를 픽업하는 사람에게도 **인증 코드**가 있어야 합니다. 인증 코드는 픽업 시 데이터 센터에서 유효한지 검사됩니다.

7. 데이터 센터에서 디바이스를 픽업하면 주문이 자동으로 **픽업됨** 상태로 바뀝니다.

    ![선택됨](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. 디바이스를 픽업한 후에 사이트의 Data Box에 데이터를 복사합니다. 데이터 복사가 완료되면 Data Box 배송 준비를 진행할 수 있습니다. 자세한 내용은 [배송 준비](data-box-deploy-picked-up.md#prepare-to-ship)를 참조하세요.

   **배송 준비** 단계는 심각한 오류 없이 완료되어야 합니다. 그렇지 않으면 필요한 수정 작업을 수행한 후 이 단계를 다시 실행해야 합니다. 배송 준비를 성공적으로 완료한 후에는 디바이스 로컬 사용자 인터페이스에서 반납을 위한 인증 코드를 볼 수 있습니다.

   > [!NOTE]
   > 메일을 통해 인증 코드를 공유하지 마세요. 인증 코드는 반납 동안 데이터 센터에서만 확인할 수 있습니다.

9. 반납 약속을 받은 경우에는 Azure Portal에서 주문이 **Azure 데이터 센터에서 수신 준비 완료** 상태여야 합니다. **반납 예약**의 지침에 따라 디바이스를 반환합니다.

   ![인증 코드 보기](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. ID 및 인증 코드를 확인하고 데이터 센터에서 디바이스를 반납한 후에는 주문 상태가 **수신됨**이 됩니다.

    ![수신 완료](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. 디바이스를 수신하면 데이터 복사가 계속됩니다. 복사가 완료되면 주문이 완료됩니다.

## <a name="next-steps"></a>다음 단계

* [Azure Data Box 시작](data-box-quickstart-portal.md)
