---
title: Azure Data Lake Analytics에 대한 재해 복구 지침
description: Azure Data Lake Analytics 계정에 대한 재해 복구를 계획 하는 방법을 알아봅니다.
services: data-lake-analytics
author: MikeRys
ms.author: mrys
ms.reviewer: jasonwhowell
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f9b22e6b806f76189134ec63c83d48f48bf95587
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889773"
---
# <a name="disaster-recovery-guidance-for-azure-data-lake-analytics"></a>Azure Data Lake Analytics에 대한 재해 복구 지침

Azure Data Lake Analytics는 빅 데이터를 간소화하는 주문형 분석 작업 서비스입니다. 하드웨어를 배포, 구성 및 조정하는 대신, 데이터를 변형하고 귀중한 통찰력을 얻기 위한 쿼리를 작성합니다. 이 분석 서비스는 필요한 전력 크기만큼 다이얼을 설정하여 어떤 크기의 작업도 즉시 처리할 수 있습니다. 실행할 때 작업 기준으로 비용이 부과되므로 비용 효과적일 수 있습니다. 이 문서에서는 드문 지역 전체 중단 또는 실수로 인 한 삭제 로부터 작업을 보호 하는 방법에 대한 지침을 제공 합니다.

## <a name="disaster-recovery-guidance"></a>재해 복구 지침

Azure Data Lake Analytics를 사용 하는 경우 고유한 재해 복구 계획을 준비 하는 것이 중요 합니다. 이 문서에서는 재해 복구 계획을 구축 하는 방법을 안내 합니다. 사용자 고유의 계획을 만드는 데 도움이 되는 추가 리소스가 있습니다.
- [Azure 애플리케이션의 오류 및 재해 복구](/azure/architecture/reliability/disaster-recovery)
- [Azure 복구력 기술 지침](/azure/architecture/checklist/resiliency-per-service)

## <a name="best-practices-and-scenario-guidance"></a>모범 사례 및 시나리오 지침

ADLA 계정에서 SQL 테이블을 읽고 구조화 되지 않은 데이터를 읽고 쓰는 지역의 되풀이 된 U-SQL 작업을 실행할 수 있습니다.  다음 단계를 수행 하 여 재해를 준비 합니다.

1. 가동 중단 중에 사용 되는 보조 지역에 ADLA 및 ADLS 계정을 만듭니다.

   > [!NOTE]
   > 계정 이름은 전역적으로 고유 하므로 보조 계정을 나타내는 일관 된 이름 지정 체계를 사용 합니다.

2. 비구조적 데이터는 [Azure Data Lake Storage Gen1의 데이터에 대한 재해 복구 지침](../data-lake-store/data-lake-store-disaster-recovery-guidance.md) 을 참조 하세요.

3. ADLA 테이블 및 데이터베이스에 저장 된 구조적 데이터의 경우 데이터베이스, 테이블, 테이블 반환 함수 및 어셈블리와 같은 메타 데이터 아티팩트의 복사본을 만듭니다. 프로덕션에서 변경이 발생 하는 경우 이러한 아티팩트를 주기적으로 다시 동기화 해야 합니다. 예를 들어 데이터를 복사 하 고 보조 테이블에 삽입 하 여 새로 삽입 된 데이터를 보조 지역에 복제 해야 합니다.

   > [!NOTE]
   > 이러한 개체 이름은 보조 계정으로 범위가 지정 되 고 전역적으로 고유 하지 않으므로 주 프로덕션 계정에서와 동일한 이름을 가질 수 있습니다.

중단 하는 동안 입력 경로가 보조 엔드포인트을 가리키도록 스크립트를 업데이트 해야 합니다. 그런 다음 사용자가 보조 지역의 ADLA 계정에 작업을 제출 합니다. 그런 다음 작업의 출력이 보조 지역의 ADLA 및 ADLS 계정에 기록 됩니다.

## <a name="next-steps"></a>다음 단계

[Azure Data Lake Storage Gen1 데이터에 대한 재해 복구 지침](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)
