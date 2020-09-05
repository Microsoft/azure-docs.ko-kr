---
title: Azure SQL Database에 대한 예약 할인 이해 | Microsoft Docs
description: 실행 중인 Azure SQL 데이터베이스에 예약 할인이 적용되는 방법을 알아봅니다. 이러한 데이터베이스에는 시간 단위로 할인이 적용됩니다.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: banders
ms.openlocfilehash: 1df60eedfb776164be7e78f2994027b8d111828b
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681960"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-sql-database"></a>Azure SQL Database에 예약 할인이 적용되는 방법

Azure SQL Database 예약 용량을 구입한 후 예약 할인이 예약의 특성 및 수량과 일치하는 SQL 데이터베이스에 자동으로 적용됩니다. 예약은 SQL Database의 컴퓨팅 비용을 포함합니다. 정상 요금으로 소프트웨어, 스토리지 및 네트워킹에 대한 요금이 청구됩니다. [Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 사용하여 SQL Database에 대한 라이선스 비용을 처리할 수 있습니다.

Azure SQL Database 서버리스에는 예약 할인이 적용되지 않습니다.

Reserved Virtual Machine Instances의 경우 [Azure Reserved VM Instances 할인 이해](../manage/understand-vm-reservation-charges.md)를 참조하세요.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

예약 할인은 "*use-it-or-lose-it*" 방식입니다. 따라서 모든 시간에 대해 일치하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.

리소스를 종료할 때 예약 할인이 지정된 범위에서 일치하는 다른 리소스에 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간이 *사라집니다*.

## <a name="discount-applied-to-running-sql-databases"></a>실행 중인 SQL 데이터베이스에 적용되는 할인

 SQL Database 예약 용량 할인은 매시간으로 실행 중인 SQL 데이터베이스에 적용됩니다. 구입하는 예약은 실행 중인 SQL 데이터베이스에서 내보낸 컴퓨팅 사용량과 일치합니다. 전체 시간 동안 실행되지 않는 SQL 데이터베이스의 경우 예약 특성과 일치하는 다른 SQL 데이터베이스에 예약이 자동으로 적용됩니다. 할인은 동시에 실행되는 SQL 데이터베이스에 적용할 수 있습니다. 예약 특성과 일치하는 전체 시간 동안 실행되는 SQL 데이터베이스가 없는 경우 해당 시간 동안 예약 할인의 모든 이점을 활용할 수 없습니다.

다음 예제에서는 구입한 코어 수 및 실행되는 시기에 따라 SQL Database 예약된 용량 할인이 적용되는 방법을 보여줍니다.

- 시나리오 1: 8개 코어 SQL Database에 대한 SQL Database 예약 용량을 구입합니다. 예약의 나머지 특성과 일치하는 16코어 SQL Database를 실행합니다. SQL Database 컴퓨팅 사용량의 8개 코어에 대한 종량제 가격으로 요금이 청구됩니다. 8코어 SQL Database 컴퓨팅 사용량의 1시간 동안 예약 할인을 받게 됩니다.

이러한 예제의 나머지의 경우 구입하는 SQL Database 예약 용량이 16코어 SQL Database에 대한 것이며, 나머지 예약 특성이 실행 중인 SQL 데이터베이스와 일치한다고 가정합니다.

- 시나리오 2: 8개 코어가 있는 2개의 SQL 데이터베이스를 각각 1시간 동안 실행합니다. 16개의 코어 예약 할인은 8개의 코어 SQL 데이터베이스 모두에 대한 컴퓨팅 사용량에 적용됩니다.
- 시나리오 3: 하나의 16개 코어 SQL Database를 오후 1시에서 오후 1시 30분까지 실행합니다. 오후 1시 30분에서 오후 2시까지 다른 16코어 SQL Database를 실행합니다. 둘 다 예약 할인에 포함됩니다.
- 시나리오 4: 하나의 16개 코어 SQL Database를 오후 1시에서 오후 1시 45분까지 실행합니다. 오후 1시 30분에서 오후 2시까지 다른 16코어 SQL Database를 실행합니다. 15분의 중복에 대한 종량제 가격으로 요금이 청구됩니다. 예약 할인은 나머지 시간에 대한 컴퓨팅 사용량에 적용됩니다.

청구 사용량 보고서에서 Azure 예약의 애플리케이션을 이해하고 보려면 [Azure 예약 사용량 이해](understand-reserved-instance-usage-ea.md)를 참조하세요.

## <a name="need-help-contact-us"></a>도움 필요 시 문의처

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>다음 단계

Azure 예약에 대한 자세한 내용은 다음 문서를 참조하세요.

- [Azure 예약이란?](save-compute-costs-reservations.md)
- [Azure Reserved VM Instances를 사용하여 Virtual Machines 선불 결제](../../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Azure SQL Database 예약 용량을 사용하여 SQL Database 컴퓨팅 리소스 요금 선결제](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure Reservations 관리](manage-reserved-vm-instance.md)
- [종량제 구독의 예약 사용량 이해](understand-reserved-instance-usage.md)
- [엔터프라이즈 등록에서 예약 사용량 이해](understand-reserved-instance-usage-ea.md)
- [CSP 구독의 예약 사용량 이해](/partner-center/azure-reservations)
