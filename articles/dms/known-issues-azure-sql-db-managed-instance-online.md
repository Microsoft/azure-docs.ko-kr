---
title: Azure SQL Database 관리 되는 인스턴스에 대한 온라인 마이그레이션과 관련 된 알려진 문제 및 제한 사항
description: 온라인 마이그레이션과 관련 하 여 Azure SQL Database 관리 되는 인스턴스에 대한 알려진 문제/마이그레이션 제한 사항에 대해 알아봅니다.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: bc1cbfc1e86db758a4f4d0240f81f5363f817312
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483142"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>Azure SQL Database 관리 되는 인스턴스에 대한 온라인 마이그레이션과 관련 하 여 알려진 문제/마이그레이션 제한 사항

SQL Server에서 Azure SQL Database 관리 되는 인스턴스로의 온라인 마이그레이션과 관련 된 알려진 문제 및 제한 사항은 아래에 설명 되어 있습니다.

> [!IMPORTANT]
> Azure SQL Database에 대한 SQL Server의 온라인 마이그레이션을 통해 SQL_variant 데이터 형식 마이그레이션은 지원 되지 않습니다.

## <a name="backup-requirements"></a>백업 요구 사항

- **체크섬이 있는 백업**

    Azure Database Migration Service는 백업 및 복원 방법을 사용 하 여 온-프레미스 데이터베이스를 SQL Database 관리 되는 인스턴스로 마이그레이션합니다. Azure Database Migration Service는 checksum을 사용 하 여 만든 백업만 지원 합니다.

    [백업 또는 복원 중 백업 체크섬 설정 또는 해제(SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > 압축을 사용 하 여 데이터베이스 백업을 수행 하는 경우 명시적으로 사용 하지 않는 한 체크섬은 기본 동작입니다.

    오프 라인 마이그레이션을 사용 하는 경우 **Azure Database Migration Service**...을 선택 하면 checksum 옵션을 사용 하 여 데이터베이스 백업을 수행 Azure Database Migration Service.

- **백업 미디어**

    모든 백업은 별도의 백업 미디어 (백업 파일)에 적용 해야 합니다. Azure Database Migration Service는 단일 백업 파일에 추가 되는 백업을 지원 하지 않습니다. 전체 백업 및 로그 백업을 사용 하 여 백업 파일을 분리 합니다.

## <a name="data-and-log-file-layout"></a>데이터 및 로그 파일 레이아웃

- **로그 파일 수**

    Azure Database Migration Service는 여러 로그 파일이 있는 데이터베이스를 지원 하지 않습니다. 로그 파일이 여러 개인 경우 단일 트랜잭션 로그 파일로 축소 하 고 다시 구성 합니다. 비어 있지 않은 파일을 원격으로 사용할 수 없기 때문에 먼저 로그 파일을 백업 해야 합니다.

## <a name="sql-server-features"></a>SQL Server 기능

- **FileStream/Filetable**

    SQL Database 관리 되는 인스턴스는 현재 FileStream 및 Filetable을 지원 하지 않습니다. 이러한 기능에 종속 된 워크 로드의 경우 azure 대상으로 Azure Vm에서 실행 되는 SQL Server를 선택 하는 것이 좋습니다.

- **메모리 내 테이블**

    메모리 내 OLTP는 SQL Database 관리 되는 인스턴스의 프리미엄 및 중요 비즈니스용 계층에서 사용할 수 있습니다. 범용 계층은 메모리 내 OLTP를 지원 하지 않습니다.

## <a name="migration-resets"></a>마이그레이션 다시 설정

- **배포**

    SQL Database 관리 되는 인스턴스는 자동 패치 및 버전 업데이트를 사용 하는 PaaS 서비스입니다. SQL Database 관리 되는 인스턴스를 마이그레이션하는 동안 중요 하지 않은 업데이트는 최대 36 시간까지 도움이 됩니다. 나중에 (그리고 중요 업데이트의 경우) 마이그레이션이 중단 되 면 프로세스가 전체 복원 상태로 다시 설정 됩니다.

    마이그레이션 가공선은 전체 백업이 복원 된 후에만 호출할 수 있으며 모든 로그 백업을 사용 하 여 catch 됩니다. 프로덕션 마이그레이션 cutovers에 영향을 주는 경우 [AZURE DMS 피드백 별칭](mailto:dmsfeedback@microsoft.com)에 문의 하세요.
