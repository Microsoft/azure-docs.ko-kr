---
title: Azure SQL Database Premium RS 서비스 계층 회수 | Microsoft Docs
description: 프리미엄 RS 서비스 계층이 더 이상 사용되지 않으며 지원이 종료될 예정입니다. 마이그레이션 옵션을 참조하세요.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: d7d1bc95a646527ccdf9d2808aefe6b8f46d7e9a
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "62111768"
---
# <a name="azure-sql-database-premium-rs-service-tier-preview-is-being-retired---options-for-migration"></a>Azure SQL Database Premium RS 서비스 계층(미리 보기)이 더 이상 사용되지 않음 - 마이그레이션 옵션

2018년 2월에 Microsoft는 Azure SQL Database Premium RS 서비스 계층이 일반 공급용으로 출시되지 않으며, 2019년 1월 31일에 지원이 중단될 것임을 발표했습니다. 이 지원 종료 기한은 2019년 6월 30일로 연장되었습니다. 이 문서에서는 프리미엄 RS 서비스 계층에서 다른 서비스 계층으로 마이그레이션하는 옵션에 대해 설명합니다. 2019년 6월 30일 이후에 Microsoft는 프리미엄 RS 데이터베이스를 프리미엄 RS 데이터베이스의 성능 요구 사항과 가장 가깝게 일치하는 일반 공급 가능 서비스 계층으로 자동으로 마이그레이션합니다.

다음은 프리미엄 RS 고객에게 적합한 마이그레이션 대상 및 가격 옵션입니다.

- vCore 서비스 계층

  [vCore 기반 구매 모델](sql-database-service-tiers-vcore.md)의 **범용** 및 **중요 비즈니스** 서비스 계층입니다. 이러한 두 서비스 계층은 일반 공급됩니다. 또한 vCore 기반 구매 모델은 데이터베이스당 100TB까지 자동으로 확장됨으로써 요청 시에 워크로드의 요구에 맞게 조정되는 **하이퍼스케일** 서비스 계층(공용 미리 보기)을 제공합니다. 하이퍼스케일 서비스 계층은 프리미엄 RS 서비스 계층과 비슷한 가격으로 [DTU 기반 구매 모델](sql-database-service-tiers-dtu.md)의 프리미엄 서비스 계층에 필적할만한 IO 성능을 제공합니다.
- 개발/테스트 가격

  [개발/테스트 가격](https://azure.microsoft.com/pricing/dev-test/)은 최대 55% 비용 절감 효과를 가져오거나 Visual Studio 구독을 통한 라이선스 포함 요금이 됩니다.
- Azure 하이브리드 혜택 및 예약 용량 가격

  [Azure 하이브리드 혜택 및 예약 용량 가격](https://azure.microsoft.com/pricing/details/sql-database/)은 최대 80%의 비용 절감 효과를 가져오거나 라이선스 포함 요금이 됩니다. 이러한 옵션에 대한 자세한 내용은 [SQL Server에 대한 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/) 및 [Azure SQL Database 예약 용량](sql-database-reserved-capacity.md)을 참조하세요.

## <a name="act-now-to-migrate-your-premium-rs-databases-to-alternative-sql-database-service-tiers"></a>이제 프리미엄 RS 데이터베이스를 대체 SQL Database 서비스 계층으로 마이그레이션합니다.

Microsoft 가격 책정 및 설명서와 함께 이 문서의 지침을 참조하여 프리미엄 RS 워크로드에 대한 적절한 마이그레이션 대상을 확인합니다.

## <a name="migrate-compute-intensive-workloads-and-save"></a>컴퓨팅 집약적 워크로드 마이그레이션 및 저장

컴퓨팅 집약적 프리미엄 RS 워크로드의 경우 일반 공급되는 vCore 기반 범용 서비스 계층으로 마이그레이션하고, SQL Server에 대한 Azure 하이브리드 혜택 및 예약 용량 제품을 사용하여 추가로 비용을 절감하거나 라이선스 포함 가격 혜택을 얻는 것이 좋습니다. DTU 기반 구매 옵션을 그대로 유지하려면 컴퓨팅 집약적 프리미엄 RS 데이터베이스를 표준 서비스 계층으로 마이그레이션하여 비용을 절감하거나 프리미엄 RS 일반 공급 가격(일반 공급되는 경우)을 사용할 수 있습니다.

> [!WARNING]
> 프리미엄 RS 워크로드를 DTU 기반 프리미엄 서비스 계층으로 마이그레이션하여 월별 비용 증가를 감수하거나 현재의 프리미엄 RS 가격을 유지할 수 있습니다. 프리미엄 RS와 유사하거나 더 낮은 비용을 유지하려는 경우 Azure 하이브리드 혜택 및 예약 용량 가격이 제공되는 하이퍼스케일 또는 중요 비즈니스용 계층을 사용하는 것이 좋습니다.

### <a name="premium-rs-databases"></a>프리미엄 RS 데이터베이스

|**현재 상태...**|**필적할만한 vCore 기반 서비스 계층으로 마이그레이션...**|**필적할만한 DTU 기반 서비스 계층으로 마이그레이션...**|
|---|---|---|
|프리미엄 RS 1|범용 1 vCore(Gen4)|표준 3|
|프리미엄 RS 2|범용 2 vCore(Gen4)|표준 4|
|프리미엄 RS 4|범용 4 vCore(Gen4)|표준 6|
|프리미엄 RS 6|범용 6 vCore(Gen4)|표준 7|

### <a name="premium-rs-pools"></a>프리미엄 RS 풀

|**현재 상태...**|**필적할만한 vCore 기반 서비스 계층으로 마이그레이션...**|**필적할만한 DTU 기반 서비스 계층으로 마이그레이션...**|
|---|---|---|
|프리미엄 RS 풀 125 DTU|범용 1 vCore(Gen4)|표준 풀 100 eDTU|
|프리미엄 RS 풀 250 DTU|범용 2 vCore(Gen4)|표준 풀 250 eDTU|
|프리미엄 RS 풀 500 DTU|범용 4 vCore(Gen4)|표준 풀 500 eDTU|
|프리미엄 RS 풀 1000 DTU|범용 8 vCore(Gen4)|표준 풀 1000 eDTU|

## <a name="optimize-savings-and-performance-for-your-io-intensive-workloads"></a>IO 집약적 워크로드에 대한 비용 절감 및 성능 최적화

최적의 성능 및 비용 조합을 유지하려면 IO 집약적 단일 데이터베이스를 현재 미리 보기로 제공되는 vCore 기반 하이퍼스케일 계층으로 마이그레이션하고, IO 집약적 데이터베이스 풀을 일반 공급되는 중요 비즈니스용 계층으로 마이그레이션하는 것이 좋습니다.  다음 vCore 기반 옵션은 현재 성능을 유지하거나 향상시키며, Azure 하이브리드 혜택과 예약 용량 가격과 함께 사용할 경우 비용 절감 효과를 가져올 수 있습니다.

|**현재 상태...**|**필적할만한 vCore 기반 서비스 계층으로 마이그레이션...**|**필적할만한 DTU 기반 서비스 계층으로 마이그레이션...**|
|---|---|---|
|프리미엄 RS 1|(미리 보기) 하이퍼스케일 1 vCore(Gen4) 또는 중요 비즈니스용 1 vCore(Gen4)|프리미엄 1|
|프리미엄 RS 2|(미리 보기) 하이퍼스케일 2 vCore(Gen4) 또는 중요 비즈니스용 2 vCore(Gen4)|프리미엄 2|
|프리미엄 RS 4|(미리 보기) 하이퍼스케일 4 vCore(Gen4) 또는 중요 비즈니스용 4 vCore(Gen4)|프리미엄 4
|프리미엄 RS 6|(미리 보기) 하이퍼스케일 6 vCore(Gen4) 또는 중요 비즈니스용 6 vCore(Gen4)|프리미엄 6|

|**현재 상태...**|**필적할만한 vCore 기반 서비스 계층으로 마이그레이션...**|**필적할만한 DTU 기반 서비스 계층으로 마이그레이션...**|
|---|---|---|
|프리미엄 RS 풀 125 DTU|중요 비즈니스용 2 vCore(Gen4)|프리미엄 풀 125 eDTU|
|프리미엄 RS 풀 250 DTU|중요 비즈니스용 2 vCore(Gen4)|프리미엄 풀 250 eDTU|
|프리미엄 RS 풀 500 DTU|중요 비즈니스용 4 vCore(Gen4)|프리미엄 풀 500 eDTU|
|프리미엄 RS 풀 1000 DTU|중요 비즈니스용 8 vCore(Gen4)|프리미엄 풀 1000 eDTU|

## <a name="take-advantage-of-our-new-offers"></a>새 제품 활용

vCore 기반 구매 모델의 서비스 계층은 80%까지 비용을 절감할 수 있는 특별 행사 제품으로 사용하거나 라이선스 포함 가격으로 사용할 수 있습니다. 활성 Software Assurance에 따라 SQL Server Standard 또는 Enterprise Edition 라이선스를 사용하여 55%까지 비용을 절감하거나 [SQL Server에 대한 Azure 하이브리드 혜택](https://azure.microsoft.com/pricing/hybrid-benefit/)을 통해 라이선스 포함 가격을 이용할 수 있습니다. 하이브리드 혜택을 [Azure SQL Database 예약 용량](sql-database-reserved-capacity.md) 가격과 결합하고, 1년 또는 3년 약정 시 최대 80%를 절감할 수 있습니다.  오늘 바로 Azure Portal에서 두 가지 혜택을 모두 활성화하세요.

이러한 변경에 대해 질문 또는 우려 사항이 있거나 마이그레이션 지원이 필요한 경우에는 [Microsoft](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)에 문의하세요.

## <a name="migration-from-a-premium-rs-service-tier-to-a-service-tier-in-either-the-dtu-or-the-vcore-model"></a>프리미엄 RS 서비스 계층에서 DTU 또는 vCore 모델의 서비스 계층으로 마이그레이션

### <a name="migration-of-a-database"></a>데이터베이스 마이그레이션

프리미엄 RS 서비스 계층에서 DTU 또는 vCore 모델의 서비스 계층으로 데이터베이스를 마이그레이션하는 것은 프리미엄 RS 서비스 계층에서 서비스 계층 간에 업그레이드 또는 다운그레이드를 수행하는 것과 비슷합니다.

### <a name="using-database-copy-to-convert-a-premium-rs-database-to-a-dtu-based-or-vcore-based-database"></a>데이터베이스 복사본을 사용하여 프리미엄 RS 데이터베이스를 DTU 기반 또는 vCore 기반 데이터베이스로 변환

대상 계산 크기가 원본 데이터베이스의 최대 데이터베이스 크기를 지원하는 경우 제한 또는 특별한 순서 지정 없이 프리미엄 RS 컴퓨팅 크기의 데이터베이스를 DTU 기반 또는 vCore 기반 계산 크기의 데이터베이스에 복사할 수 있습니다. 데이터베이스 복사본은 복사 작업의 시작 시간에 따라 데이터의 스냅숏을 만들고 원본과 대상 간에 데이터 동기화를 수행하지 않습니다.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스에 사용할 수 있는 특정 컴퓨팅 크기 및 스토리지 크기 선택 방법에 대한 자세한 내용은 [단일 데이터베이스에 대한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier)을 참조하세요.
- 탄력적 풀에 사용할 수 있는 특정 컴퓨팅 크기 및 스토리지 크기 옵션에 대한 자세한 내용은 [탄력적 풀에 대한 SQL Database vCore 기반 리소스 제한](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)을 참조하세요.
