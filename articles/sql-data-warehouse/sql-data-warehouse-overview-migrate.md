---
title: SQL Data Warehouse에 솔루션 마이그레이션| Microsoft Docs
description: Azure SQL Data Warehouse 플랫폼에 솔루션을 가져오기 위한 마이그레이션 지침
services: sql-data-warehouse
author: WenJason
manager: digimobile
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: implement
origin.date: 04/17/2018
ms.date: 03/25/2019
ms.author: v-jay
ms.reviewer: igorstan
ms.openlocfilehash: 04c921282d3591e7326d326c230bf72e7f5c1812
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60776223"
---
# <a name="migrate-your-solution-to-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse에 솔루션 마이그레이션
Azure SQL Data Warehouse로 기존 데이터베이스 솔루션을 마이그레이션하는 데 관련된 정보를 참조하세요. 

## <a name="profile-your-workload"></a>워크로드 프로파일링
마이그레이션하기 전에 워크로드에 적합한 솔루션인 특정 SQL Data Warehouse를 선택하려고 합니다. SQL Data Warehouse는 대규모 데이터 분석을 수행하도록 설계되고 배포된 시스템입니다.  SQL Data Warehouse로 마이그레이션하려면 이해하기 어렵지 않지만 구현하는 데 시간이 걸릴 수 있는 몇 가지 설계를 변경해야 합니다. 비즈니스에 엔터프라이즈 수준의 데이터 웨어하우스가 필요한 경우 충분한 혜택을 얻을 수 있습니다. 그러나 SQL Data Warehouse의 기능이 필요하지 않은 경우 SQL Server 또는 Azure SQL Database를 사용하는 것이 더 비용 효율적입니다.

다음과 같은 경우 SQL Data Warehouse를 사용하는 것이 좋습니다.
- 하나 이상의 테라바이트의 데이터가 있는 경우
- 많은 양의 데이터에 대한 분석을 실행하려는 경우
- 계산 및 스토리지의 크기를 조정하는 기능이 필요한 경우 
- 필요하지 않을 때 계산 리소스를 일시 중지하여 비용을 절감하고자 합니다.

다음과 같은 작업(OLTP) 워크로드에 SQL Data Warehouse를 사용하지 않습니다.
- 높은 빈도의 읽기 및 쓰기
- 많은 수의 단일 항목 선택
- 많은 양의 단일 행 삽입
- 행 단위 처리 요구 사항
- 호환되지 않는 형식(JSON, XML)

## <a name="plan-the-migration"></a>마이그레이션 계획

SQL Data Warehouse에 기존 솔루션을 마이그레이션하기로 결정했다면 시작하기 전에 마이그레이션을 계획해야 합니다. 

데이터, 테이블 스키마 및 코드가 SQL Data Warehouse와 호환되는지 확인하는 것이 계획의 중요한 목표입니다. 현재 시스템 및 SQL Data Warehouse 간에 호환성 차이점을 해결해야 합니다. 또한 많은 양의 데이터를 Azure로 마이그레이션하는 데 시간이 걸립니다. 신중하게 계획하면 신속하게 Azure에 데이터를 가져올 수 있습니다. 

계획의 또 다른 목표는 솔루션에서 SQL Data Warehouse가 제공하도록 설계된 높은 쿼리 성능을 활용하도록 설계를 조정하는 것입니다. 확장을 위한 데이터 웨어하우스의 디자인은 다양한 디자인 패턴을 도입하므로 일반적인 접근 방식이 항상 최선은 아닙니다. 마이그레이션 후에 일부 설계를 조정할 수 있지만 프로세스를 더 빨리 변경하면 나중에 시간을 절약할 수 있습니다.

성공적인 마이그레이션을 수행하려면 테이블 스키마, 코드 및 데이터를 마이그레이션해야 합니다. 이러한 마이그레이션 항목에 대한 지침은 다음을 참조하세요.

-  [스키마 마이그레이션](sql-data-warehouse-migrate-schema.md)
-  [코드 마이그레이션](sql-data-warehouse-migrate-code.md)
-  [데이터 마이그레이션](sql-data-warehouse-migrate-data.md) 

## <a name="next-steps"></a>다음 단계
CAT(고객 자문 팀)에서 블로그를 통해 게시하는 몇 가지 유용한 SQL Data Warehouse 관련 지침도 확인할 수 있습니다.  [Azure SQL Data Warehouse로의 데이터 마이그레이션 실습][Migrating data to Azure SQL Data Warehouse in practice] 문서에서 마이그레이션 관련 추가 지침을 확인해 보세요.

<!--Image references-->

<!--Article references-->

<!--MSDN references-->

<!--Other Web references-->
[Migrating data to Azure SQL Data Warehouse in practice]: https://blogs.msdn.microsoft.com/sqlcat/20../../migrating-data-to-azure-sql-data-warehouse-in-practice/

<!--Update_Description: update meta properties, wording update-->