---
title: 할당량 증가 요청
description: 이 페이지에서는 Azure SQL Database 단일 데이터베이스, 서버 및 관리 되는 인스턴스의 할당량을 늘리기 위한 지원 요청을 만드는 방법을 설명 합니다.
services: sql-database
ms.service: sql-database
ms.topic: conceptual
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: sstein
ms.date: 02/04/2020
ms.openlocfilehash: fb576b81adeec99e4080c744749097390d1add1d
ms.sourcegitcommit: 9add86fb5cc19edf0b8cd2f42aeea5772511810c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/09/2020
ms.locfileid: "77111099"
---
# <a name="request-quota-increases-for-azure-sql-database"></a>Azure SQL Database에 대 한 요청 할당량 증가가

이 문서에서는 단일 데이터베이스, 서버 및 관리 되는 인스턴스의 Azure SQL Database에 대 한 할당량 증가를 요청 하는 방법을 설명 합니다. 또한 지역에 대 한 구독 액세스를 사용 하도록 설정 하는 방법을 설명 합니다.

## <a id="newquota"></a>새 지원 요청 만들기

다음 단계를 사용 하 여 SQL Database에 대 한 Azure Portal에서 새 지원 요청을 만듭니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원**을 선택 합니다.

   ![도움말 + 지원 링크](./media/quota-increase-request/help-plus-support.png)

1. **도움말 + 지원**에서 **새 지원 요청**을 선택 합니다.

    ![새 지원 요청 만들기](./media/quota-increase-request/new-support-request.png)

1. **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.

   ![문제 유형 선택](./media/quota-increase-request/select-quota-issue-type.png)

1. **구독**의 경우 할당량을 늘릴 구독을 선택 합니다.

   ![증가 된 할당량에 대 한 구독 선택](./media/quota-increase-request/select-subscription-support-request.png)

1. **할당량 유형**에서 다음 할당량 유형 중 하나를 선택 합니다.

   - 단일 데이터베이스 및 탄력적 풀 할당량에 대 한 **SQL Database** 합니다.
   - 관리 되는 인스턴스의 **SQL Database Managed Instance** 입니다.

   그런 후 **다음: 솔루션 > >** 를 선택 합니다.

   ![할당량 유형 선택](./media/quota-increase-request/select-quota-type.png)

1. **세부** 정보 창에서 세부 정보 **제공** 을 선택 하 여 추가 정보를 입력 합니다.

   !["세부 정보 제공" 링크](./media/quota-increase-request/provide-details-link.png)

**세부 정보 제공** 을 클릭 하면 추가 정보를 추가할 수 있는 **할당량 정보** 창이 표시 됩니다. 다음 섹션에서는 **SQL Database** 및 **SQL Database Managed Instance** 할당량 유형에 대 한 다양 한 옵션을 설명 합니다.

## <a id="sqldbquota"></a>SQL Database 할당량 유형

다음 섹션에서는 **SQL Database** 할당량 유형에 대 한 세 가지 할당량 증가 옵션을 설명 합니다.

- 서버당 Dtu (데이터베이스 트랜잭션 단위)
- 구독 당 서버 수
- 지역에 대 한 구독 액세스 사용

### <a name="database-transaction-units-dtus-per-server"></a>서버당 Dtu (데이터베이스 트랜잭션 단위)

서버당 Dtu 증가를 요청 하려면 다음 단계를 사용 합니다.

1. 서버 할당량 유형 **당 dtu (데이터베이스 트랜잭션 단위)** 를 선택 합니다.

1. **리소스** 목록에서 대상으로 할 리소스를 선택 합니다.

1. **새 할당량** 필드에 요청 하는 새 DTU 한도를 입력 합니다.

   ![DTU 할당량 정보](./media/quota-increase-request/quota-details-dtus.png)

자세한 내용은 dtu 구매 모델을 사용 하 여 [단일 데이터베이스에 대 한 리소스 제한](sql-database-dtu-resource-limits-single-databases.md) 및 [탄력적 풀에 대 한 리소스 제한](sql-database-dtu-resource-limits-elastic-pools.md)을 참조 하세요.

### <a name="servers-per-subscription"></a>구독 당 서버 수

다음 단계를 사용 하 여 구독 당 서버 수의 증가를 요청 합니다.

1. **구독 당 서버** 할당량 유형을 선택 합니다.

1. **위치** 목록에서 사용할 Azure 지역을 선택 합니다. 할당량은 각 지역의 구독 당입니다.

1. **새 할당량** 필드에 해당 지역의 최대 서버 수에 대 한 요청을 입력 합니다.

   ![서버 할당량 정보](./media/quota-increase-request/quota-details-servers.png)

자세한 내용은 [SQL Database 리소스 제한 및 리소스 관리](sql-database-resource-limits-database-server.md)를 참조 하세요.

### <a name="enable-subscription-access-to-a-region"></a>지역에 대 한 구독 액세스 사용

일부 제품 유형은 모든 지역에서 사용할 수 없습니다. 다음과 같은 오류가 표시 될 수 있습니다.

`This location is not available for subscription`

구독에 특정 지역에 대 한 액세스 권한이 필요한 경우 **다른 할당량 요청** 옵션을 사용 하 여 액세스를 요청 하세요. 요청에서 지역에 대해 사용 하도록 설정할 제품 및 SKU 세부 정보를 지정 합니다. 제품 및 SKU 옵션을 탐색 하려면 [Azure SQL Database 가격 책정](https://azure.microsoft.com/pricing/details/sql-database/single/)을 참조 하세요.

![기타 할당량 정보](./media/quota-increase-request/quota-details-whitelisting.png)

## <a id="sqlmiquota"></a>관리 되는 인스턴스 할당량 유형

**SQL Server Managed Instance** 할당량 유형에 대해 다음 단계를 사용 합니다.

1. **지역** 목록에서 대상으로 할 Azure 지역을 선택 합니다.

1. **서브넷** 및 **vcore**에 대해 요청 하는 새 제한을 입력 합니다.

   ![관리 되는 인스턴스 할당량 정보](./media/quota-increase-request/quota-details-managed-instance.png)

자세한 내용은 [관리 되는 인스턴스 리소스 제한 Azure SQL Database 개요](sql-database-managed-instance-resource-limits.md)를 참조 하세요.

## <a name="submit-your-request"></a>요청 제출

마지막 단계는 SQL Database 할당량 요청에 대 한 나머지 세부 정보를 입력 하는 것입니다. 그런 후 다음을 선택 합니다 **. 검토 + 만들기 > >** 를 선택 하 고 요청 세부 정보를 검토 한 후 **만들기** 를 클릭 하 여 요청을 제출 합니다.

## <a name="next-steps"></a>다음 단계

요청을 제출 하면 검토 됩니다. 양식에 제공한 정보를 기반으로 하는 답변이 연결 됩니다.

다른 Azure 제한에 대 한 자세한 내용은 [azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조 하세요.
