---
title: 데이터베이스 마이그레이션 시나리오 상태 | Microsoft Docs
description: Azure Database Migration Service를 지 원하는 마이그레이션 시나리오의 상태에 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 04/04/2019
ms.openlocfilehash: 4159b2e7af83030f46d5aca150ef99a1380e711f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473011"
---
# <a name="status-of-migration-scenarios-supported-by-azure-database-migration-service"></a>Azure Database Migration Service를 지 원하는 마이그레이션 시나리오의 상태
Azure Database Migration Service는 오프 라인 둘 다에 대 한 여러 마이그레이션 시나리오 (원본/대상 쌍)를 지원 하도록 설계 되었습니다 (1 회) 및 온라인 (연속 동기화) 마이그레이션. Azure Database Migration Service가 제공 하는 시나리오 범위를 시간에 따라 확장 되 고 있습니다. 새로운 시나리오를 정기적으로 추가되고 있습니다. 이 문서에서는 Azure Database Migration Service 및 각 시나리오에 대 한 상태 (비공개 미리 보기, 공개 미리 보기 또는 일반 공급)에서 현재 지 원하는 마이그레이션 시나리오를 식별 합니다.

## <a name="offline-versus-online-migrations"></a>오프라인 및 온라인 마이그레이션
Azure Database Migration Service를 사용 하 여 오프 라인 또는 온라인는 마이그레이션을 수행할 수 있습니다. *오프라인* 마이그레이션을 사용하는 경우 애플리케이션 가동 중지 시간이 마이그레이션 시작과 동시에 시작됩니다. 마이그레이션이 완료 되 면 새 환경으로 이동 하는 데 필요한 시간에 가동 중지 시간을 제한 하려면 사용 된 *온라인* 마이그레이션. 가동 중지 시간이 허용; 되는지 확인 하려면 오프 라인 마이그레이션을 테스트할 것이 좋습니다. 그러지 않으면 온라인 마이그레이션을 수행 합니다.

## <a name="migration-scenario-status"></a>마이그레이션 시나리오 상태
Azure Database Migration Service를 지 원하는 마이그레이션 시나리오의 상태를 시간에 따라 다릅니다. 일반적으로 시나리오는 처음에 릴리스된 **비공개 미리 보기**합니다. 통해 추천을 제출 하려면 고객에 게 필요한 비공개 미리 보기에 참여 하는 [DMS 미리 보기 사이트](https://aka.ms/dms-preview)합니다. 비공개 미리 보기 후 시나리오 상태가으로 변경 **공개 미리 보기**합니다. Azure Database Migration Service 사용자가 사용자 인터페이스에서 직접 공개 미리 보기의 마이그레이션 시나리오를 시험해볼 수 있습니다. 등록가 필요 합니다.  그러나 공개 미리 보기의 마이그레이션 시나리오는 모든 지역에서 사용할 수 없습니다 및 최종 릴리스 전에 추가 변경 될 수 있습니다. 공개 미리 보기 후 시나리오 상태가으로 변경 **일반적으로 가용성**합니다. 일반 공급 (GA) 최종 릴리스 상태 이며 기능은 완전 하 고 모든 사용자에 게 액세스할 수 있습니다.

## <a name="migration-scenario-support"></a>마이그레이션 시나리오 지원
다음 표에서 Azure Database Migration Service를 사용 하는 경우 지원 되는 마이그레이션 시나리오를 보여 줍니다.

> [!NOTE]
> 아래에 지원되는 것으로 표시되는 시나리오가 사용자 인터페이스 내에 나타나지 않으면 [데이터 마이그레이션 팀](mailto:datamigrationteam@microsoft.com)에 추가 정보를 요청하세요.

> [!IMPORTANT]
> 현재 비공개 미리 보기에서 Azure Database Migration Service를 지 원하는 모든 시나리오를 보려면 합니다 [DMS 미리 보기 사이트](https://aka.ms/dms-preview)합니다.

### <a name="offline-one-time-migration-support"></a>오프라인(일회성) 마이그레이션 지원
다음 표에서는 오프라인 마이그레이션에 대한 Azure Database Migration Service 지원을 보여 줍니다.

| 대상  | 원본 | 지원 | 상태 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL |  |  |
|   | Oracle |  |   |
| **Azure SQL VM** | SQL Server | ✔ | GA |
|   | Oracle |   |   |
| **Azure Cosmos DB** | MongoDB | ✔ | 공개 미리 보기 |
| **Azure DB for MySQL** | MySQL |   |   |
|   | RDS MySQL |   |   |
| **Azure DB for PostgreSQL** | PostgreSQL |  |
|  | RDS PostgreSQL |   |   |

### <a name="online-continuous-sync-migration-support"></a>온라인(연속 동기화) 마이그레이션 지원
다음 표에서는 온라인 마이그레이션에 대한 Azure Database Migration Service 지원을 보여 줍니다.

| 대상  | 원본 | 지원 | 상태 |
| ------------- | ------------- |:-------------:|:-------------:|
| **Azure SQL DB** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle |  |  |
| **Azure SQL DB MI** | SQL Server | ✔ | GA |
|   | RDS SQL | ✔ | GA |
|   | Oracle | ✔ | 비공개 미리 보기 |
| **Azure SQL VM** | SQL Server |   |   |
|   | Oracle  |  |  |
| **Azure Cosmos DB** | MongoDB | ✔ | 공개 미리 보기 |
| **Azure DB for MySQL** | MySQL | ✔ | GA |
|   | RDS MySQL | ✔ | GA |
| **Azure DB for PostgreSQL** | PostgreSQL | ✔ | GA |
|   | RDS PostgreSQL | ✔ | GA |
|   | Oracle | ✔ | 비공개 미리 보기 |

## <a name="next-steps"></a>다음 단계
Azure Database Migration Service 및 국가별 가용성 개요를 문서 참조 [Azure Database Migration Service 란](dms-overview.md)합니다.
