---
title: SQL Data Warehouse 권장 사항 - 개념 | Microsoft Docs
description: SQL Data Warehouse 권장 사항 및 생성 방식에 대해 설명
services: sql-data-warehouse
author: kevinvngo
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/05/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: b275f23209979e1a8068ecd99465f7b52392bc6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61421225"
---
# <a name="sql-data-warehouse-recommendations"></a>SQL Data Warehouse 권장 사항

이 문서에서는 SQL Data Warehouse가 Azure Advisor를 통해 제공하는 권장 사항을 설명합니다.  

SQL Data Warehouse는 데이터 웨어하우스가 지속적으로 성능에 최적화되도록 권장 사항을 제공합니다. 데이터 웨어하우스 권장 사항은 [Azure Advisor](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations)와 긴밀하게 통합되어 [Azure Portal](https://aka.ms/Azureadvisor) 내에서 직접 모범 사례를 제공합니다. SQL Data Warehouse는 날마다 데이터 웨어하우스의 현재 상태를 분석하고, 원격 분석 데이터를 수집하고, 활성 워크로드에 대한 권장 사항을 표시합니다. 아래에는 지원되는 데이터 웨어하우스 권장 시나리오 및 권장 조치를 적용하는 방법이 설명되어 있습니다.

SQL Data Warehouse Advisor에 대한 피드백이 있거나 문제가 있는 경우 [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com)으로 알려주세요.   

[여기](https://aka.ms/Azureadvisor)를 클릭하여 현재 권장 사항을 확인하세요. 현재 이 기능은 Gen2 데이터 웨어하우스에만 적용됩니다. 

## <a name="data-skew"></a>데이터 기울이기

데이터 기울이기는 워크로드를 실행할 때 추가 데이터 이동 또는 리소스 병목 현상을 일으킬 수 있습니다. 다음 문서에서는 데이터 기울이기를 발견하고 최적의 배포 키를 선택하여 데이터 기울이기를 방지하는 방법을 설명합니다.

- [기울이기 식별 및 제거](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>통계가 없거나 오래된 통계

최적 통계가 아니면 SQL Data Warehouse 쿼리 최적화 프로그램이 최적이 아닌 쿼리 계획을 생성할 수 있으므로 쿼리 성능에 심각한 악영향을 미칠 수 있습니다. 다음 문서에서는 통계 생성 및 업데이트와 관련된 모범 사례를 설명합니다.

- [테이블 통계 생성 및 업데이트](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

이러한 권장 사항의 영향을 받은 테이블 목록을 확인하려면 다음 [T-SQL 스크립트](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables)를 실행합니다. Advisor는 계속 동일한 T-SQL 스크립트를 실행하여 이러한 권장 사항을 생성합니다.

## <a name="replicate-tables"></a>테이블 복제

복제된 테이블 권장 사항의 경우 Advisor는 다음과 같은 물리적 특징에 따라 테이블 후보를 검색합니다.

- 복제 테이블 크기
- 열 수
- 테이블 배포 형식
- 파티션 수

Advisor는 테이블 액세스 빈도, 평균으로 반환되는 행 및 데이터 웨어하우스 크기 및 작업의 임계값과 같은 워크로드 기반의 추론을 지속적으로 활용하여 고품질 권장 사항이 생성되는지 확인합니다. 

다음에서는 Azure Portal에서 찾을 수 있는 각 복제 테이블 권장 사항에 대한 워크로드 기반 추론을 설명합니다.

- 검사 평균 - 지난 7일 동안 각 테이블 액세스에 대해 테이블에서 반환된 행의 평균 백분율
- 빈번한 읽기, 업데이트 안 됨 - 액세스 활동을 표시하는 동시에 지난 7일 동안 테이블을 업데이트하지 않았음을 나타냄
- 읽기/업데이트 비율 - 지난 7일 동안 테이블이 업데이트된 경우에 따라 테이블에 액세스한 빈도율
- 활동 - 액세스 활동을 기준으로 사용량을 측정합니다. 이 측정은 지난 7일 동안의 데이터 웨어하우스 간 평균 테이블 액세스 활동과 관련 테이블 액세스 활동을 비교합니다. 

현재 Advisor는 가장 높은 우선 순위의 작업을 지정하는 클러스터형 Columnstore 인덱스를 사용하여 한 번에 최대 4개의 복제된 테이블 후보를 보여줍니다.

> [!IMPORTANT]
> 복제된 테이블 권장 사항은 전체 증명으로, 데이터 이동 작업을 고려하지 않습니다. Microsoft에서는 추론을 바탕으로 이를 추가하려고 노력하지만 그 사이에 사용자는 권장 사항을 적용한 후 항상 워크로드의 유효성을 검사해야 합니다. 워크로드가 이전으로 되돌아가게 하는 복제된 테이블 권장 사항을 발견하는 경우 sqldwadvisor@service.microsoft.com에 문의하세요. 복제된 테이블에 대한 자세한 내용은 다음 [설명서](https://docs.microsoft.com/azure/sql-data-warehouse/design-guidance-for-replicated-tables#what-is-a-replicated-table)를 참조하세요.
