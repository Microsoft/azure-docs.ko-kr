---
title: 예약 할인 이해 - Azure Database for PostgreSQL 단일 서버
description: Azure Database for PostgreSQL 단일 서버에 예약 할인이 적용되는 방법에 대해 알아봅니다.
author: kummanish
ms.author: manishku
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 51b2f4964c01efbfc82008134d47f09648a772ff
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88681977"
---
# <a name="how-a-reservation-discount-is-applied-to-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 단일 서버에 예약 할인이 적용되는 방법

Azure Database for PostgreSQL 단일 서버 예약된 용량을 구입한 후 예약 할인이 예약의 특성 및 수량과 일치하는 PostgreSQL 단일 서버 데이터베이스에 자동으로 적용됩니다. 예약은 Azure Database for PostgreSQL 단일 서버에 대한 계산 비용만 포함합니다. 정상 요금으로 스토리지 및 네트워킹에 대한 요금이 청구됩니다.

## <a name="how-reservation-discount-is-applied"></a>예약 할인이 적용되는 방법

예약 할인은 ***use-it-or-lose-it*** 방식입니다. 따라서 모든 시간에 대해 일치하는 리소스가 없는 경우 해당 시간의 예약 수량이 손실됩니다. 사용하지 않는 예약 시간은 이월할 수 없습니다.</br>

리소스를 종료할 때 예약 할인이 지정된 범위에서 일치하는 다른 리소스에 자동으로 적용됩니다. 지정된 범위에서 일치하는 리소스를 찾을 수 없는 경우 예약된 시간은 소멸됩니다.

## <a name="discount-applied-to-azure-database-for-postgresql-single-server"></a>Azure Database for PostgreSQL 단일 서버에 적용되는 할인

Azure Database for PostgreSQL 단일 서버 예약된 용량 할인은 시간 단위로 PostgreSQL 단일 서버를 실행하는 데 적용됩니다. 구입하는 예약은 실행 중인 Azure Database for PostgreSQL 단일 서버에서 내보낸 컴퓨팅 사용량과 일치합니다. 전체 시간 동안 실행되지 않는 PostgreSQL 단일 서버의 경우 예약 특성과 일치하는 다른 Azure Database for PostgreSQL 단일 서버에 예약이 자동으로 적용됩니다. 할인은 동시에 실행되는 Azure Database for PostgreSQL 단일 서버에 적용될 수 있습니다. 예약 특성과 일치하는 전체 시간 동안 실행되는 PostgreSQL 단일 서버가 없는 경우 해당 시간 동안 예약 할인의 모든 이점을 활용할 수 없습니다.

다음 예제에서는 구입한 코어 수 및 실행되는 시기에 따라 Azure Database for PostgreSQL 단일 서버 예약된 용량 할인이 적용되는 방법을 보여 줍니다.

* **예제 1**: 8 vCore에 대해 Azure Database for PostgreSQL 단일 서버 예약된 용량을 구입합니다. 예약의 나머지 특성과 일치하는 16 vCore Azure Database for PostgreSQL 단일 서버를 실행하는 경우 PostgreSQL 단일 서버 컴퓨팅 사용량의 8 vCore에 대한 종량제 가격이 청구되고 1시간의 8 vCore PostgreSQL 단일 서버 컴퓨팅 사용량에 대해 예약 할인이 적용됩니다.</br>

이러한 예제의 나머지 부분에서는 구매한 Azure Database for PostgreSQL 단일 서버 예약된 용량이 16 vCore Azure Database for PostgreSQL 단일 서버에 대한 것이며 나머지 예약 특성은 실행 중인 PostgreSQL 단일 서버와 일치하는 것으로 가정합니다.

* **예제 2**: 1시간마다 8 vCore를 사용하여 두 Azure Database for PostgreSQL 단일 서버를 실행합니다. 16 vCore 예약 할인은 8 vCore Azure Database for PostgreSQL 단일 서버 모두에 대한 컴퓨팅 사용량에 적용됩니다.

* **예제 3**: 오후 1시에서 오후 1시 30분까지 하나의 16 vCore Azure Database for PostgreSQL 단일 서버를 실행합니다. 오후 1시 30분에서 오후 2시까지 다른 16 vCore Azure Database for PostgreSQL 단일 서버를 실행합니다. 둘 다 예약 할인에 포함됩니다.

* **예제 4**: 오후 1시에서 오후 1시 45분까지 하나의 16 vCore Azure Database for PostgreSQL 단일 서버를 실행합니다. 오후 1시 30분에서 오후 2시까지 다른 16 vCore Azure Database for PostgreSQL 단일 서버를 실행합니다. 15분의 중복에 대한 종량제 가격으로 요금이 청구됩니다. 예약 할인은 나머지 시간에 대한 컴퓨팅 사용량에 적용됩니다.

청구 사용량 보고서에서 Azure 예약의 애플리케이션을 이해하고 보려면 [Azure 예약 사용량 이해](https://docs.microsoft.com/azure/billing/billing-understand-reserved-instance-usage-ea)를 참조하세요.

## <a name="next-steps"></a>다음 단계

질문이 있거나 도움이 필요한 경우 [지원 요청을 만드세요](https://go.microsoft.com/fwlink/?linkid=2083458).
