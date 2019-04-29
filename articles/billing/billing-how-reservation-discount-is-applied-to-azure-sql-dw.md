---
title: Azure SQL Data Warehouse에 예약 할인을 적용 하는 방법 | Microsoft Docs
description: 절감 하는 데 Azure SQL Data Warehouse에 예약 할인을 적용 하는 방법을 알아봅니다.
services: billing
author: yashesvi
manager: yashar
ms.service: billing
ms.topic: conceptual
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 10e19377d31489cd19465fe6171ffb530bd58c28
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60918404"
---
# <a name="how-reservation-discounts-apply-to-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 예약 할인을 적용 하는 방법

Azure SQL Data Warehouse 예약 용량을 구입 후 예약 할인은 해당 지역에 존재 하는 데이터 웨어하우스를 자동으로 적용 됩니다. SQL Data Warehouse cDWU 미터에서 내보낸 사용량에 예약 할인이 적용 됩니다. 저장소 및 네트워킹 종 량 제 요금으로 요금이 청구 됩니다.

## <a name="reservation-discount-application"></a>예약 할인 응용 프로그램

SQL Data Warehouse 용량 예약된 할인은 웨어하우스 매시간 실행에 적용 됩니다. 1 시간 동안 배포 된 웨어하우스가 없는 경우에 해당 시간에 대 한 예약된 된 용량 낭비 됩니다. 이 전달 되지 않습니다.

구매 후 구입할 수 있는 예약 시간에 언제 든 지 웨어하우스를 실행 하 여 SQL Data Warehouse 사용량과에 일치 됩니다. 일부 웨어하우스를 종료 하는 경우 다음 예약 할인에 자동으로 적용 다른 일치 하는 웨어하우스에 합니다.

전체 시간에 대 한 실행 하지 않는 웨어하우스에 대 한 예약이 해당 시간에 자동으로 일치 하는 다른 인스턴스에 적용 됩니다.

## <a name="discount-examples"></a>예를 할인

다음 예제에서는 배포에 따라 SQL Data Warehouse 용량 예약된 할인이 적용 하는 방법을 보여 줍니다.

- **예제 1**: 구매한 100 예약 cDWU 용량의 5 단위입니다. 1 시간 동안 DW1500c SQL Data Warehouse 인스턴스를 실행 합니다. 이 경우 15 단위 100 cDWU 사용에 대 한 사용 현황이 내보내집니다. 예약 할인을 적용 하는 5 단위에 적용 됩니다. 나머지 10 단위 수는 100 cDWU 사용에 대 한 종 량 제 요금으로 청구 됩니다.

- **예제 2**: 구매한 100 예약 cDWU 용량의 5 단위입니다. 시간에 대 한 두 개의 DW100c SQL Data Warehouse 인스턴스를 실행 합니다. 이 경우 1 단위 100 cDWU 사용에 대 한 두 사용 이벤트가 내보내집니다. 두 사용 이벤트에는 예약 된 용량 할인 받기. 나머지 3 개 단위의 100 cDWU 예약 용량 낭비 됩니다 및 향후 사용에 대 한 전달 하지 않습니다.

- **예제 3**: 1 개 단위 100 cDWU 예약 용량을 구매합니다. 두 DW100c SQL Data Warehouse 인스턴스를 실행할 수 있습니다. 각각 30 분 동안 실행 됩니다. 이 예제의 경우 둘 다 사용 이벤트 예약 된 용량 할인을 가져옵니다. 사용 하지 않는 종 량 제 요금으로 요금이 청구 됩니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

- 질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](billing-save-compute-costs-reservations.md)
- [예약 트랜잭션 보기](billing-view-reservations.md)
- [예약 트랜잭션 및 API 통해 사용률 가져오기](billing-reservation-apis.md)
- [예약 관리](billing-manage-reserved-vm-instance.md)
