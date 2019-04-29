---
title: Azure SQL Database 리소스 제한 사항 - 관리되는 인스턴스 | Microsoft Docs
description: 이 문서는 관리되는 인스턴스의 Azure SQL Database 리소스 제한 사항에 대한 개요를 제공합니다.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp
manager: craigg
ms.date: 02/27/2019
ms.openlocfilehash: 09ab154494ad3e1276239e36068255c2042358c5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61487545"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Azure SQL Database Managed Instance 리소스 제한 사항에 대한 개요

이 문서에서는 Azure SQL Database Managed Instance 리소스 제한 사항에 대한 개요를 제공하고, 기본 지역 구독 제한을 늘리기 위한 요청을 만드는 방법에 대해 설명합니다.

> [!NOTE]
> Managed Instance의 다른 제한 사항은 [vCore 기반 구매 모델](sql-database-managed-instance.md#vcore-based-purchasing-model) 및 [Managed Instance 서비스 계층](sql-database-managed-instance.md#managed-instance-service-tiers)을 참조하세요. 지원되는 기능 및 T-SQL 문의 차이점은 [기능 차이](sql-database-features.md) 및 [T-SQL 문 지원](sql-database-managed-instance-transact-sql-information.md)을 참조하세요.

## <a name="instance-level-resource-limits"></a>인스턴스 수준 리소스 제한

Managed Instance에는 기본 인프라 및 아키텍처에 따라 달라지는 특성과 리소스 제한이 있습니다. 제한은 하드웨어 세대 및 서비스 계층에 따라 다릅니다.

### <a name="hardware-generation-characteristics"></a>하드웨어 세대 특성

Azure SQL Database Managed Instance는 두 가지 하드웨어 세대(Gen4 및 Gen5)에 배포할 수 있습니다. 하드웨어 세대에는 다음 표에서 설명하는 다양한 특성이 있습니다.

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| 하드웨어 | Intel E5-2673 v3(Haswell) 2.4GHz 프로세서, 연결형 SSD, vCore = 1PP(물리적 코어) | Intel E5-2673 v4(Broadwell) 2.3GHz 프로세서, 고속 NVMe SSD, vCore = 1LP(하이퍼스레드) |
| 컴퓨팅 | 8, 16, 24개 vCore | 8, 16, 24, 32, 40, 64, 80개 vCore |
| 메모리 | vCore당 7GB | vCore당 5.1GB |
| 메모리 내 OLTP 메모리 | vCore당 3GB | vCore당 2.6GB |
| 최대 스토리지 크기(범용) |  8 TB | 8 TB |
| 최대 저장소 크기(중요 비즈니스용) | 1TB | 코어 수에 따라 1TB, 2TB 또는 4TB |

### <a name="service-tier-characteristics"></a>서비스 계층 특성

Managed Instance에는 범용 및 중요 비즈니스용의 두 가지 서비스 계층이 있습니다. 이러한 계층은 아래 표에서 설명한 대로 다양한 기능을 제공합니다.

| **기능** | **범용** | **중요 비즈니스** |
| --- | --- | --- |
| vCore 수\* | Gen4: 8, 16, 24<br/>Gen5: 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24, 32 <br/> Gen5: 8, 16, 24, 32, 40, 64, 80 |
| 메모리 | Gen4: 56GB~168GB<br/>Gen5: 40.8GB~408GB<br/>\*vCore 수에 정비례 | Gen4: 56GB~168GB <br/> Gen5: 40.8GB~408GB<br/>\*vCore 수에 정비례 |
| 최대 저장소 크기 | 8 TB | Gen4: 1TB <br/> Gen5: <br/>- 1TB(8, 16개 vCore용)<br/>- 2TB(24개 vCore용)<br/>- 4TB(32, 40, 64, 80개 vCore용) |
| 데이터베이스당 최대 저장소 | 인스턴스당 최대 저장소 크기에 따라 결정됨 | 인스턴스당 최대 저장소 크기에 따라 결정됨 |
| 인스턴스당 최대 데이터베이스 수 | 100 | 100 |
| 인스턴스당 최대 데이터베이스 파일 수 | 최대 280개 | 데이터베이스당 32,767개 파일 |
| 데이터/로그 IOPS(근사치) | 파일당 500~7,500<br/>\*[파일 크기에 따라 다름](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes)| 11K~110K(vCore당 1,375) |
| 로그 처리량 | 인스턴스당 22MB/초 | vCore당 3MB/초<br/>인스턴스당 최대 48 MB/s|
| 데이터 처리량(근사치) | 파일당 100~250MB/초<br/>\*[파일 크기에 따라 다름](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#premium-storage-disk-sizes) | vCore당 24~48MB/초 |
| IO 대기 시간(근사치) | 5~10ms | 1~2ms |
| 최대 tempDB 크기 | 192~1,920GB(vCore당 24GB) | 제약 조건 없음 - 최대 인스턴스 스토리지 크기로 제한됨 |

**참고**:

- 사용자 및 시스템 데이터베이스의 데이터 및 로그 파일 크기는 최대 스토리지 크기 제한과 비교되는 인스턴스 스토리지 크기에 포함됩니다. <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys.master_files</a> 시스템 뷰를 사용하여 데이터베이스에서 사용되는 총 공간을 확인합니다. 오류 로그는 영구적이지 않으며 크기에 포함되지 않습니다. 백업은 스토리지 크기에 포함되지 않습니다.
- 처리량 및 IOPS도 Managed Instance에 의해 명시적으로 제한되지 않은 페이지 크기에 따라 다릅니다.

## <a name="supported-regions"></a>지원되는 지역

Managed Instance는 [지원되는 지역](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all)에서만 만들 수 있습니다. 현재 지원되지 않는 지역에서 Managed Instance를 만들려면 [Azure Portal을 통해 지원 요청을 보낼 수 있습니다](#obtaining-a-larger-quota-for-sql-managed-instance).

## <a name="supported-subscription-types"></a>지원되는 구독 유형

Managed Instance는 현재 다음 유형의 구독에만 배포를 지원합니다.

- [EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [CSP(클라우드 서비스 공급자)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Enterprise 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [종량제 개발/테스트](https://azure.microsoft.com/offers/ms-azr-0023p/)

> [!NOTE]
> 이 제한은 일시적입니다. 앞으로 새 구독 유형이 사용될 예정입니다.

## <a name="regional-resource-limitations"></a>지역별 리소스 제한

지원되는 구독 유형에는 지역당 제한된 수의 리소스가 포함될 수 있습니다. Managed Instance에는 구독 유형에 따라 Azure 지역당 다음 두 가지 기본 제한이 있습니다.

- **서브넷 제한**: 관리되는 인스턴스가 단일 지역에 배포되는 서브넷의 최대 수입니다.
- **인스턴스 수 제한**: 단일 지역에 배포할 수 있는 인스턴스의 최대 수입니다.

> [!Note]
> 이러한 제한은 기본 설정 및 없습니다 기술적 제한 사항을 적용 합니다. 특수 만들어 제한 증가 주문형 수 [Azure portal에서 지원 요청](#obtaining-a-larger-quota-for-sql-managed-instance) 현재 지역에서 관리 되는 인스턴스를 더 필요한 경우. 대안으로, 지원 요청을 보내지 않고 다른 Azure 지역에 새 Managed Instance를 만들 수 있습니다.

다음 표에는 지원되는 구독에 대한 지역별 기본 제한이 나와 있습니다.

|구독 유형| Managed Instance 서브넷의 최대 수 | 인스턴스의 최대 수 |GP 관리되는 인스턴스의 최대 수*|BC 관리되는 인스턴스의 최대 수*|
| :---| :--- | :--- |:--- |:--- |
|종량제|1*|4*|4*|1*|
|CSP |1*|4*|4*|1*|
|종량제 개발/테스트|1*|4*|4*|1*|
|Enterprise 개발/테스트|1*|4*|4*|1*|
|EA|3**|12**|12**|3**|

\* 하나의 서브넷에 1개 BC 또는 4개 GP 인스턴스를 배포할 수 있으므로 서브넷의 총 "인스턴스 단위" 수는 4개를 초과할 수 없습니다.

** 다른 서비스 계층에 인스턴스가 없는 경우 한 서비스 계층의 최대 인스턴스 수가 적용됩니다. 동일한 서브넷 내에서 GP 및 BC 인스턴스를 혼합하려는 경우 다음 섹션을 허용되는 조합에 대한 참조로 사용합니다. 간단한 규칙으로, 서브넷의 총 수는 3개를 초과할 수 없으며, 인스턴스 단위의 총 수는 12개를 초과할 수 없습니다.



> [!IMPORTANT]
> 배포를 계획할 때는 일반적으로 중복성 추가로 인해 BC(중요 비즈니스용) 인스턴스에서 GP(범용) 인스턴스보다 4배 많은 용량을 사용한다고 가정합니다. 따라서 계산상 1개 GP 인스턴스 = 1개 인스턴스 단위 및 1개 BC 인스턴스 = 4개 인스턴스 단위입니다. 기본 제한에 대한 사용량 분석을 간소화하려면 Managed Instance가 배포된 지역의 모든 서브넷에 대한 인스턴스 단위를 요약하고 결과를 구독 유형의 인스턴스 단위 제한과 비교합니다.

## <a name="strategies-for-deploying-mixed-general-purpose-and-business-critical-instances"></a>범용 및 중요 비즈니스용 인스턴스 혼합 배포 전략

[EA(기업 계약)](https://azure.microsoft.com/pricing/enterprise-agreement/) 구독에서는 GP 및 BC 인스턴스가 조합될 수 있습니다. 그러나 서브넷에 인스턴스를 배치하는 것과 관련하여 몇 가지 제약 조건이 있습니다.

> [!Note]
> [종량제](https://azure.microsoft.com/offers/ms-azr-0003p/) 및 [CSP(클라우드 서비스 공급자)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources) 구독 유형에는 하나의 중요 비즈니스용 또는 최대 4개의 범용 인스턴스가 있을 수 있습니다.

다음 예에서는 비어 있지 않은 서브넷과 GP 및 BC 혼합 서비스 계층이 있는 배포 사례를 설명합니다.

|서브넷 수|서브넷 1|서브넷 2|서브넷 3|
|:---|:---|:---|:---|
|1|1개 BC 및 최대 8개 GP<br>2개 BC 및 최대 4개 GP|N/A| N/A|
|2|0개 BC, 최대 4개 GP|1개 BC, 최대 4개 GP<br>2개 BC, 0개 GP|N/A|
|2|1개 BC, 0개 GP|0개 BC, 최대 8개 GP<br>1개 BC, 최대 4개 GP|N/A|
|2|2개 BC, 0개 GP|0개 BC, 최대 4개 GP|N/A|
|3|1개 BC, 0개 GP|1개 BC, 0개 GP|0개 BC, 최대 4개 GP|
|3|1개 BC, 0개 GP|0개 BC, 최대 4개 GP|0개 BC, 최대 4개 GP|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>더 많은 SQL Managed Instance 할당량 가져오기

현재 지역에 더 많은 Managed Instance가 필요한 경우 Azure Portal을 사용하여 할당량을 늘리기 위한 지원 요청을 보낼 수 있습니다.
더 많은 할당량을 가져오는 프로세스를 시작하려면 다음을 수행합니다.

1. **도움말 + 지원**을 열고, **새 지원 요청**을 클릭합니다.

   ![도움말 및 지원](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. 새 지원 요청에 대한 [기본 사항] 탭에서 다음을 수행합니다.
   - **문제점 유형**에 대해 **서비스 및 구독 제한(할당량)** 를 선택합니다.
   - **구독**의 경우 사용자의 구독을 선택합니다.
   - **할당량 유형**에 대해 **SQL Database Managed Instance**를 선택합니다.
   - **지원 계획**에 대해 해당 지원 계획을 선택합니다.

     ![문제점 유형 할당량](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. **다음**을 클릭합니다.
4. 새 지원 요청에 대한 [문제점] 탭에서 다음을 수행합니다.
   - **심각도**에 대해 문제점의 심각도 수준을 선택합니다.
   - **세부 정보**에 대해 오류 메시지를 포함하여 문제점에 대한 추가 정보를 제공합니다.
   - **파일 업로드**에 대해 자세한 정보가 포함된 파일(최대 4MB)을 첨부합니다.

     ![문제점 세부 정보](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > 유효한 요청에 포함되어야 하는 다음과 같습니다.
     > - 구독 제한를 늘려야 하는 지역
     > - 할당량 증가 후 기존 서브넷의 서비스 계층당 필요한 인스턴스 수(기존 서브넷 중 하나를 확장해야 하는 경우)
     > - 필요한 서브넷의 수 및 새 서브넷 내의 서비스 계층당 총 인스턴스 수(새 서브넷에 관리되는 인스턴스를 배포해야 하는 경우).

5. **다음**을 클릭합니다.
6. 새 지원 요청에 대한 [연락처 정보] 탭에서 기본 연락 방법(이메일 또는 전화)과 연락처 세부 정보를 입력합니다.
7. **만들기**를 클릭합니다.

## <a name="next-steps"></a>다음 단계

- Managed Instance에 대한 자세한 내용은 [Managed Instance란?](sql-database-managed-instance.md)을 참조하세요.
- 가격 정보는 [SQL Database Managed Instance 가격](https://azure.microsoft.com/pricing/details/sql-database/managed/)을 참조하세요.
- 첫 번째 Managed Instance를 만드는 방법을 알아보려면 [빠른 시작 가이드](sql-database-managed-instance-get-started.md)를 참조하세요.
