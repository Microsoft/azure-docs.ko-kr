---
title: Azure SQL Managed Instance를 사용 하 여 데이터베이스 작업 대상으로 SSIS 마이그레이션
description: Azure SQL Managed Instance를 사용 하 여 데이터베이스 작업 대상으로 SSIS를 마이그레이션합니다.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: 6de08faee78deeb86117084b420eb5043153f62d
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186049"
---
# <a name="ssis-migration-with-azure-sql-managed-instance-as-the-database-workload-destination"></a>Azure SQL Managed Instance를 사용 하 여 데이터베이스 작업 대상으로 SSIS 마이그레이션

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

SQL Server 인스턴스에서 Azure SQL Managed Instance로 데이터베이스 작업을 마이그레이션하는 경우 dms ( [Azure Data Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)) 및 [dms를 사용 하 여 SQL Managed Instance 마이그레이션의 네트워크 토폴로지](https://docs.microsoft.com/azure/dms/resource-network-topologies)를 잘 알고 있어야 합니다.

이 문서에서는 ssis 카탈로그 (SSISDB)에 저장 된 SSIS (SQL Server Integration Service) 패키지와 SSIS 패키지 실행을 예약 하는 SQL Server 에이전트 작업을 마이그레이션하는 방법에 대해 중점적으로 설명 합니다.

## <a name="migrate-ssis-catalog-ssisdb"></a>SSIS 카탈로그 (SSISDB) 마이그레이션

[SSIS 패키지를 SQL Managed Instance로 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)문서에 설명 된 대로 DMS를 사용 하 여 SSISDB 마이그레이션을 수행할 수 있습니다.

## <a name="ssis-jobs-to-sql-managed-instance-agent"></a>SQL Managed Instance 에이전트에 SSIS 작업

SQL Managed Instance에는 온-프레미스 SQL Server 에이전트와 마찬가지로 기본적인 최고 수준의 스케줄러가 있습니다.  [AZURE SQL Managed Instance 에이전트를 통해 SSIS 패키지를 실행할](how-to-invoke-ssis-package-managed-instance-agent.md)수 있습니다.

SSIS 작업을 위한 마이그레이션 도구를 아직 사용할 수 없으므로 스크립트/수동 복사를 통해 온-프레미스에서 SQL Managed Instance 에이전트로 SQL Server 에이전트로 마이그레이션해야 합니다.

## <a name="additional-resources"></a>추가 리소스

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [Azure SSIS 통합 런타임](https://docs.microsoft.com/azure/data-factory/create-azure-ssis-integration-runtime)
- [Azure Database Migration Service](https://docs.microsoft.com/azure/dms/dms-overview)
- [DMS를 사용 하 여 SQL Managed Instance 마이그레이션의 네트워크 토폴로지](https://docs.microsoft.com/azure/dms/resource-network-topologies)
- [SSIS 패키지를 SQL Managed Instance로 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)

## <a name="next-steps"></a>다음 단계

- [Azure에서 SSISDB에 연결](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Azure에 배포 된 SSIS 패키지 실행](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
