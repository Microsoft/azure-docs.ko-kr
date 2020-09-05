---
title: Azure Synapse Analytics에 예약 할인이 적용되는 방식 | Microsoft Docs
description: 비용을 절감하는 데 도움이 되도록 Azure Synapse Analytics에 예약 할인이 적용되는 방식을 알아봅니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: banders
ms.openlocfilehash: f0bd57befc0ec30473065ac69026a77e21e30e5c
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88682606"
---
# <a name="how-reservation-discounts-apply-to-azure-synapse-analytics"></a>Azure Synapse Analytics에 예약 할인이 적용되는 방식

Azure Synapse Analytics 예약 용량을 구매한 후에는 해당 지역에 존재하는 프로비저닝된 인스턴스에 예약 할인이 자동으로 적용됩니다. 예약 할인은 Azure Synapse Analytics cDWU 미터에서 내보낸 사용량에 적용됩니다. 스토리지 및 네트워킹은 종량제 요금으로 청구됩니다.

## <a name="reservation-discount-application"></a>예약 할인 애플리케이션

Azure Synapse Analytics 예약된 용량 할인은 매시간으로 실행 중인 웨어하우스에 적용됩니다. 한 시간 동안 배포된 웨어하우스가 없는 경우 해당 시간 동안 예약된 용량이 낭비됩니다. 이월되지 않습니다.

구입 후에 구매한 예약은 언제든지 웨어하우스를 실행하여 내보낸 Azure Synapse Analytics 사용량과 일치합니다. 일부 웨어하우스를 종료하는 경우 일치하는 다른 웨어하우스에 예약 할인이 자동으로 적용됩니다.

전체 시간 동안 실행되지 않는 웨어하우스의 경우 해당 시간의 다른 일치하는 인스턴스에 예약이 자동으로 적용됩니다.

## <a name="discount-examples"></a>할인 예제

다음 예제에서는 배포에 따라 Azure Synapse Analytics 예약된 용량 할인이 적용되는 방법을 보여줍니다.

- **예제 1**: 5단위의 100 cDWU 예약된 용량을 구입합니다. 한 시간 동안 DW1500c Azure Synapse Analytics 인스턴스를 실행합니다. 이 경우 15단위의 100 cDWU 사용량에 대한 사용량이 내보내집니다. 예약 할인은 사용한 5단위에 적용됩니다. 사용한 나머지 10단위의 100 cDWU 사용량에 대해서는 종량제 요금을 사용하여 요금이 부과됩니다. 즉, 여러 예약에 대해 부분 검사를 수행할 수 있습니다.

- **예제 2**: 5단위의 100 cDWU 예약된 용량을 구입합니다. 한 시간 동안 두 개의 DW100c Azure Synapse Analytics 인스턴스를 실행합니다. 이 경우 1단위의 100 cDWU 사용량에 대해 두 개의 사용량 이벤트가 내보내집니다. 두 사용량 이벤트 모두 예약된 용량 할인을 받습니다. 나머지 3단위의 100 cDWU 예약된 용량은 낭비되며 나중에 사용하도록 전달되지 않습니다. 즉, 단일 예약이 여러 Azure Synapse Analytics 인스턴스와 일치할 수 있습니다.

- **예제 3**: 1단위의 100 cDWU 예약된 용량을 구입합니다. 두 개의 DW100c Azure Synapse Analytics 인스턴스를 실행합니다. 각각 30분 동안 실행됩니다. 이 경우 두 사용량 이벤트 모두 예약된 용량 할인을 받습니다. 종량제 요금을 사용하여 요금이 부과되지 않습니다.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

- 질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](save-compute-costs-reservations.md)
- [예약 트랜잭션 보기](view-reservations.md)
- [API를 통해 예약 트랜잭션 및 사용률 가져오기](reservation-apis.md)
- [예약 관리](manage-reserved-vm-instance.md)
