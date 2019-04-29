---
title: 범용 서비스 계층 - Azure SQL Database | Microsoft Docs
description: Azure SQL Database 범용 계층에 대해 알아보기
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop-msft
ms.reviewer: sstein
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: dc379f1ee67174cd806840e4244054701d18f0d4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60709169"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>범용 서비스 계층 - Azure SQL Database

> [!NOTE]
> vCore 기반 구매 모델의 범용 서비스 계층은 DTU 기반 구매 모델의 표준 서비스 계층이라고 합니다. vCore 기반 구매 모델과 DTU 기반 구매 모델의 비교는 [Azure SQL Database 구매 모델 및 리소스](sql-database-purchase-models.md)를 참조하세요.

Azure SQL Database는 인프라 오류의 경우에도 99.99%의 가용성을 보장하기 위해 클라우드 환경에 적합한 SQL Server 데이터베이스 엔진 아키텍처를 기반으로 합니다. Azure SQL Database에는 3가지 서비스 계층이 사용되며 각각 서로 다른 아키텍처 모델을 사용합니다. 이러한 서비스 계층은 다음과 같습니다.

- 범용 가상 컴퓨터
- 중요 비즈니스용
- 하이퍼스케일

범용 서비스 계층의 아키텍처 모델은 컴퓨팅과 스토리지 분리를 기반으로 합니다. 이 아키텍처 모델은 데이터베이스 파일을 확실하게 복제하고 내부 인프라 오류가 발생하는 경우에도 데이터 무손실을 보장하는 Azure Blob Storage의 고가용성 및 안정성을 기반으로 합니다.

다음 그림은 분리된 계산 및 스토리지 레이어가 있는 표준 아키텍처 모델의 4개 노드를 보여줍니다.

![계산과 스토리지의 분리](media/sql-database-managed-instance/general-purpose-service-tier.png)

범용 서비스 계층의 아키텍처 모델에는 두 개의 계층이 있습니다.

- 상태 비저장 계산 레이어는 `sqlserver.exe` 프로세스를 실행하고 일시적인 데이터와 캐시된 데이터(예: 계획 캐시, 버퍼 풀, 열 저장 풀)만 포함합니다. 상태 비저장 SQL Server 노드는 프로세스를 초기화하고 노드의 상태를 제어하며 필요한 경우 다른 위치로 장애 조치(failover)를 수행하는 Azure Service Fabric에서 운영됩니다.
- 상태 저장 데이터 계층에는 데이터베이스 파일(.mdf/.ldf)이 있으며 Azure Blob Storage에 저장됩니다. Azure Blob Storage는 데이터베이스 파일에 배치된 모든 레코드의 데이터가 손실되지 않도록 보장합니다. Azure Storage에는 SQL Server 프로세스가 중단 되더라도 로그 파일의 모든 레코드나 데이터 파일의 페이지가 보존되도록 하는 데이터 가용성/백업 기능이 기본 제공됩니다.

데이터베이스 엔진이나 운영 체제가 업그레이드될 때마다, 기본 인프라의 일부에서는 업그레이드 되지 않거나 SQL Server 프로세스에서 심각한 문제가 감지될 경우, Azure Service Fabric은 상태 비저장 SQL Server 프로세스를 다른 상태 비저장 컴퓨팅 노드로 옮깁니다. 장애 조치(failover) 시간을 최소화하기 위해 기본 노드의 장애 조치(failover)가 발생하는 경우 새 컴퓨팅 서비스를 실행하려고 대기하는 예비 노드 세트가 있습니다. Azure Storage 계층의 데이터는 영향을 받지 않으며 데이터/로그 파일은 새로 초기화된 SQL Server 프로세스에 연결됩니다. 이 프로세스는 99.99%의 가용성을 보장하지만 전환 시간 및 새 SQL Server 노드가 콜드 캐시로 시작된다는 사실로 인해 실행 중인 과도한 워크로드의 성능에 영향을 미칠 수 있습니다.

## <a name="when-to-choose-this-service-tier"></a>이 서비스 계층을 선택하는 시기

범용 서비스 계층은 대부분의 일반 워크로드를 위해 설계된 Azure SQL Database의 기본 서비스입니다. 대부분의 경우 Azure SQL IaaS와 일치하는 5 ~ 10밀리초 사이의 스토리지 대기 시간으로 99.99% SLA를 사용하는 완전 관리형 데이터베이스 엔진이 필요한 경우 범용 계층은 옵션입니다.

## <a name="next-steps"></a>다음 단계

- [중요 비즈니스용](sql-database-service-tier-business-critical.md) 및 [하이퍼스케일](sql-database-service-tier-hyperscale.md)에 대해 알아 봅니다.
- [Service Fabric](../service-fabric/service-fabric-overview.md)에 대해 알아 봅니다.
- 고가용성 및 재해 복구에 대한 자세한 옵션은 [비즈니스 연속성](sql-database-business-continuity.md)을 참조하세요.
