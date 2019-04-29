---
title: Azure SQL Database DTU 기반 리소스 제한 단일 데이터베이스 | Microsoft Docs
description: 이 페이지에서는 Azure SQL Database에서 단일 데이터베이스에 대한 몇 가지 일반적인 DTU 기반 리소스 제한을 설명합니다.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 03/20/2019
ms.openlocfilehash: b3514eca8db5d5b68b3e5784ee95e8583813945c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61075240"
---
# <a name="resource-limits-for-single-databases-using-the-dtu-based-purchasing-model"></a>DTU 기반 구매 모델을 사용한 단일 데이터베이스에 대한 리소스 제한

이 문서에서는 DTU 기반 구매 모델을 사용하여 Azure SQL Database 단일 데이터베이스에 대한 리소스 제한을 자세히 설명합니다.

탄력적 풀에 대한 DTU 기반 구매 모델 리소스 제한은 [DTU 기반 리소스 제한 - 탄력적 풀](sql-database-dtu-resource-limits-elastic-pools.md)을 참조합니다. vCore 기반 리소스 제한은 [vCore 기반 리소스 제한 - 단일 데이터베이스](sql-database-vcore-resource-limits-single-databases.md) 및 [vCore 기반 리소스 제한 - 탄력적 풀](sql-database-vcore-resource-limits-elastic-pools.md)을 참조합니다. 다양한 구매 모델에 관한 자세한 내용은 [구매 모델 및 서버 계층](sql-database-purchase-models.md)을 참조하세요.

## <a name="single-database-storage-sizes-and-compute-sizes"></a>단일 데이터베이스: 스토리지 크기 및 컴퓨팅 크기

아래 표에는 각 서비스 계층과 컴퓨팅 크기에서 단일 데이터베이스에 사용할 수 있는 리소스가 나와 있습니다. [Azure Portal](sql-database-single-databases-manage.md#manage-an-existing-sql-database-server), [Transact-SQL](sql-database-single-databases-manage.md#transact-sql-manage-sql-database-servers-and-single-databases), [PowerShell](sql-database-single-databases-manage.md#powershell-manage-sql-database-servers-and-single-databases), [Azure CLI](sql-database-single-databases-manage.md#azure-cli-manage-sql-database-servers-and-single-databases) 또는 [REST API](sql-database-single-databases-manage.md#rest-api-manage-sql-database-servers-and-single-databases)를 사용하여 단일 데이터베이스에 대한 서비스 계층, 계산 크기 및 스토리지 용량을 설정할 수 있습니다.

> [!IMPORTANT]
> 크기 조정 지침과 고려 사항을 참조 하세요. [단일 데이터베이스 확장](sql-database-single-database-scale.md)

### <a name="basic-service-tier"></a>기본 서비스 계층

| **계산 크기** | **Basic** |
| :--- | --: |
| 최대 DTU | 5 |
| 포함된 저장소(GB) | 2 |
| 최대 저장소(GB) 옵션 | 2 |
| 최대 메모리 내 OLTP 저장소(GB) |N/A |
| 최대 동시 작업자(요청) | 30 |
| 최대 동시 세션 | 300 |
|||

### <a name="standard-service-tier"></a>표준 서비스 계층

| **계산 크기** | **S0** | **S1** | **S2** | **S3** |
| :--- |---:| ---:|---:|---:|
| 최대 DTU | 10 | 20 | 50 | 100 |
| 포함된 저장소(GB) | 250 | 250 | 250 | 250 |
| 최대 저장소(GB) 옵션 | 250 | 250 | 250 | 250, 500, 750, 1024 |
| 최대 메모리 내 OLTP 저장소(GB) | N/A | 해당 사항 없음 | 해당 사항 없음 | N/A |
| 최대 동시 작업자(요청)| 60 | 90 | 120 | 200 |
| 최대 동시 세션 |600 | 900 | 1200 | 2400 |
||||||

### <a name="standard-service-tier-continued"></a>표준 서비스 계층(계속)

| **계산 크기** | **S4** | **S6** | **S7** | **S9** | **S12** |
| :--- |---:| ---:|---:|---:|---:|
| 최대 DTU | 200 | 400 | 800 | 1600 | 3000 |
| 포함된 저장소(GB) | 250 | 250 | 250 | 250 | 250 |
| 최대 저장소(GB) 옵션 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 | 250, 500, 750, 1024 |
| 최대 메모리 내 OLTP 저장소(GB) | N/A | 해당 사항 없음 | 해당 사항 없음 | 해당 사항 없음 |N/A |
| 최대 동시 작업자(요청)| 400 | 800 | 1600 | 3200 |6000 |
| 최대 동시 세션 |4800 | 9600 | 19200 | 30000 |30000 |
|||||||

### <a name="premium-service-tier"></a>프리미엄 서비스 계층

| **계산 크기** | **P1** | **P2** | **P4** | **P6** | **P11** | **P15** |
| :--- |---:|---:|---:|---:|---:|---:|
| 최대 DTU | 125 | 250 | 500 | 1000 | 1750 | 4000 |
| 포함된 저장소(GB) | 500 | 500 | 500 | 500 | 4096* | 4096* |
| 최대 저장소(GB) 옵션 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 500, 750, 1024 | 4096* | 4096* |
| 최대 메모리 내 OLTP 저장소(GB) | 1 | 2 | 4 | 8 | 14 | 32 |
| 최대 동시 작업자(요청)| 200 | 400 | 800 | 1600 | 2400 | 6400 |
| 최대 동시 세션 | 30000 | 30000 | 30000 | 30000 | 30000 | 30000 |
|||||||

\* 1024GB에서 최대 4096 GB 단위로 256gb

> [!IMPORTANT]
> 현재 다음 지역을 제외한 모든 지역에서 프리미엄 계층의 스토리지 1TB 이상을 사용할 수 있습니다. 중국 동부, 중국 북부, 독일 중부, 독일 북동부, 미국 중서부, 미국 DoD 지역 및 미국 중앙 정부 이러한 지역에서 프리미엄 계층 저장소 최대 크기는 1TB로 제한됩니다.  자세한 내용은 [P11-P15 현재 제한 사항](sql-database-single-database-scale.md#p11-and-p15-constraints-when-max-size-greater-than-1-tb)을 참조하세요.  
> [!NOTE]
> 에 대 한 `tempdb` 제한을 참조 하세요 [tempdb 제한](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)합니다.

## <a name="next-steps"></a>다음 단계

- 단일 데이터베이스에 대한 vCore 리소스 제한의 경우 [vCore 기반 구매 모델을 사용하여 단일 데이터베이스에 대한 리소스 제한](sql-database-vcore-resource-limits-single-databases.md)을 참조하세요.
- 탄력적 풀에 대한 vCore 리소스 제한의 경우 [vCore 기반 구매 모델을 사용하여 탄력적 풀에 대한 리소스 제한](sql-database-vcore-resource-limits-elastic-pools.md)을 참조합니다.
- 탄력적 풀에 대한 DTU 리소스 제한의 경우 [DTU 기반 구매 모델을 사용하여 탄력적 풀에 대한 리소스 제한](sql-database-dtu-resource-limits-elastic-pools.md)을 참조합니다.
- 관리되는 인스턴스에 대한 리소스 제한의 경우 [관리되는 인스턴스 리소스 제한](sql-database-managed-instance-resource-limits.md)을 참조합니다.
- 일반 Azure 제한에 대한 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-subscription-service-limits.md)을 참조하세요.
- 데이터베이스 서버의 리소스 제한에 대한 자세한 내용은 서버 및 구독 수준의 한도에 관한 정보인 경우 [SQL Database 서버의 리소스 제한 개요](sql-database-resource-limits-database-server.md)를 참조하세요.
