---
title: 데이터베이스 마이그레이션 시나리오 상태
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service에서 지 원하는 마이그레이션 시나리오의 상태에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 09/05/2019
ms.openlocfilehash: 03fb6a1aff62df878417da55a7da7b42186b3462
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437684"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure Database Migration Service에서 지 원하는 마이그레이션 시나리오의 상태

Azure Database Migration Service은 오프 라인 (일회성) 및 온라인 (연속 동기화) 마이그레이션 모두에 대해 서로 다른 마이그레이션 시나리오 (원본/대상 쌍)를 지원 하도록 설계 되었습니다. Azure Database Migration Service에서 제공 하는 시나리오 검사가 시간이 지남에 따라 확장 되 고 있습니다. 새로운 시나리오를 정기적으로 추가되고 있습니다. 이 문서에서는 Azure Database Migration Service에서 현재 지원 되는 마이그레이션 시나리오와 각 시나리오에 대한 상태 (비공개 미리 보기, 공개 미리 보기 또는 일반 공급)를 식별 합니다.

## <a name="offline-versus-online-migrations"></a>오프라인 및 온라인 마이그레이션

Azure Database Migration Service를 사용 하 여 오프 라인 또는 온라인 마이그레이션 작업을 수행할 수 있습니다. *오프라인* 마이그레이션을 사용하는 경우 애플리케이션 가동 중지 시간이 마이그레이션 시작과 동시에 시작됩니다. 마이그레이션이 완료 될 때 새 환경으로 이동 하는 데 필요한 시간으로 가동 중지 시간을 제한 하려면 *온라인* 마이그레이션을 사용 합니다. 가동 중지 시간이 허용 되는지 여부를 확인 하기 위해 오프 라인 마이그레이션을 테스트 하는 것이 좋습니다. 그렇지 않은 경우 온라인 마이그레이션을 수행 합니다.

## <a name="migration-scenario-status"></a>마이그레이션 시나리오 상태

Azure Database Migration Service에서 지 원하는 마이그레이션 시나리오의 상태는 시간에 따라 다릅니다. 일반적으로 시나리오는 **비공개 미리 보기**에서 처음 릴리스 되었습니다. 비공개 미리 보기에 참여 하려면 고객이 [DMS preview 사이트](https://aka.ms/dms-preview)를 통해 추천을 제출 해야 합니다. 비공개 미리 보기 후에는 시나리오 상태가 **공개 미리 보기로**변경 됩니다. Azure Database Migration Service 사용자는 사용자 인터페이스에서 직접 공개 미리 보기로 마이그레이션 시나리오를 사용해 볼 수 있습니다. 등록은 필요 하지 않습니다.  그러나 공개 미리 보기의 마이그레이션 시나리오는 모든 지역에서 사용 하지 못할 수 있으며 최종 릴리스 전에 추가 변경 사항이 발생할 수 있습니다. 공개 미리 보기 후에는 시나리오 상태가 **일반적으로 사용 가능**으로 변경 됩니다. GA (일반 공급)는 최종 릴리스 상태 이며 기능이 완전 하 고 모든 사용자가 액세스할 수 있습니다.

## <a name="migration-scenario-support"></a>마이그레이션 시나리오 지원

다음 표에서는 Azure Database Migration Service을 사용할 때 지원 되는 마이그레이션 시나리오를 보여 줍니다.

> [!NOTE]
> 아래 지원 되는 것으로 나열 된 시나리오가 사용자 인터페이스 내에 표시 되지 않는 경우 [Azure 데이터베이스 마이그레이션](mailto:AskAzureDatabaseMigrations@service.microsoft.com) 별칭에 추가 정보를 문의 하세요.

> [!IMPORTANT]
> 비공개 미리 보기에서 Azure Database Migration Service에서 현재 지 원하는 모든 시나리오를 보려면 [DMS preview 사이트](https://aka.ms/dms-preview)를 참조 하십시오.

### <a name="offline-one-time-migration-support"></a>오프라인(일회성) 마이그레이션 지원

다음 표에서는 오프라인 마이그레이션에 대한 Azure Database Migration Service 지원을 보여 줍니다.

| 대상 파악  | 원본 | 지원 | 상태 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB for PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>온라인(연속 동기화) 마이그레이션 지원

다음 표에서는 온라인 마이그레이션에 대한 Azure Database Migration Service 지원을 보여 줍니다.

| 대상 파악  | 원본 | 지원 | 상태 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | 프라이빗 미리 보기 |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | GA |
| **Azure DB for MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **Azure DB for PostgreSQL** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | 공개 미리 보기 |

## <a name="next-steps"></a>다음 단계

Azure Database Migration Service 및 지역 가용성에 대한 개요는 [Azure Database Migration Service 이란?](dms-overview.md)문서를 참조 하세요.
