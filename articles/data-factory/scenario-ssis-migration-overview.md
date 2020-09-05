---
title: Azure Data Factory에서 SSIS로 온-프레미스 SSIS 작업 마이그레이션
description: 온-프레미스 SSIS 워크 로드를 ADF의 SSIS로 마이그레이션합니다.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 9/3/2019
ms.openlocfilehash: 53085544be9477c03fdbbc27e709bd80dea25b92
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88186066"
---
# <a name="migrate-on-premises-ssis-workloads-to-ssis-in-adf"></a>온-프레미스 SSIS 워크로드를 ADF의 SSIS로 마이그레이션

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>개요

데이터베이스 워크 로드를 SQL Server 온-프레미스에서 Azure 데이터베이스 서비스 (즉, Azure SQL Database 또는 Azure SQL Managed Instance)로 마이그레이션하는 경우 기본 부가 가치 서비스 중 하나로 SSIS (SQL Server Integration Services)의 ETL 워크 로드도 마이그레이션해야 합니다.

Azure Data Factory (ADF)의 IR (Azure-SSIS Integration Runtime)은 SSIS 패키지 실행을 지원 합니다. Azure-SSIS IR 프로 비전 된 후에는 SQL Server Data Tools (SSDT)/SSS (SQL Server Management Studio)와 같은 친숙 한 도구와 dtinstall/dtutil/dtexec와 같은 명령줄 유틸리티를 사용 하 여 Azure에서 패키지를 배포 하 고 실행할 수 있습니다. 자세한 내용은 [AZURE SSIS 리프트 및 시프트 개요](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)를 참조 하세요.

이 문서에서는 온-프레미스 SSIS에서 ADF의 SSIS로 ETL 워크 로드의 마이그레이션 프로세스를 강조 표시 합니다. 마이그레이션 프로세스는 **평가** 및 **마이그레이션**의 두 단계로 구성됩니다.

## <a name="assessment"></a>평가

철저 한 평가를 통해 완전 한 마이그레이션 계획을 세울 경우 성공적인 마이그레이션을 방해 하는 원본 SSIS 패키지의 문제를 식별 하는 데 도움이 됩니다.

DMA(Data Migration Assistant)는 이 목적을 위해 로컬에서 설치 및 실행할 수 있는 무료 다운로드 가능 도구입니다. SSIS 패키지를 일괄 처리로 평가 하 고 다음 범주에 표시 된 호환성 문제를 식별 하기 위해 **Integration Services** 유형의 DMA 평가 프로젝트를 만들 수 있습니다.

- 마이그레이션 차단기: Azure-SSIS IR에서 실행 되는 마이그레이션 원본 패키지를 차단 하는 호환성 문제입니다. DMA는 이러한 문제를 해결 하는 데 도움이 되는 지침을 제공 합니다.

- 정보 제공 문제: 소스 패키지에서 사용 되는 부분적으로 지원 되거나 더 이상 사용 되지 않는 기능입니다. DMA는 포괄적인 권장 사항 집합, Azure에서 사용할 수 있는 대체 방법 및 해결을 위한 완화 단계를 제공 합니다.

### <a name="four-storage-types-for-ssis-packages"></a>SSIS 패키지에 대 한 4 가지 저장소 유형

- SSIS 카탈로그 (SSISDB). 이는 SQL Server 2012에서 도입 되었으며 SSIS 프로젝트/패키지 작업에 사용 되는 저장 프로시저, 뷰 및 테이블 반환 함수 집합을 포함 합니다.
- 파일 시스템
- MSDB (SQL Server 시스템 데이터베이스).
- SSIS 패키지 저장소. 다음은 두 하위 유형 위에 있는 패키지 관리 계층입니다.
  - MSDB는 SSIS 패키지를 저장 하는 데 사용 되는 SQL Server의 시스템 데이터베이스입니다.
  - SSIS 패키지를 저장 하는 데 사용 되는 SQL Server 설치 경로의 특정 폴더인 관리 파일 시스템입니다.

Dma는 현재 **dma 버전 v 5.0**이후 **파일 시스템**, **패키지 저장소**및 **SSIS 카탈로그** 에 저장 된 패키지의 일괄 처리 평가를 지원 합니다.

[DMA](https://docs.microsoft.com/sql/dma/dma-overview)를 가져오고 [이를 사용 하 여 패키지 평가를 수행](https://docs.microsoft.com/sql/dma/dma-assess-ssis)합니다.

## <a name="migration"></a>마이그레이션

원본 SSIS 패키지의 [저장소 유형과](#four-storage-types-for-ssis-packages) 데이터베이스 작업의 마이그레이션 대상에  **따라 ssis 패키지를 마이그레이션하는** 단계와 ssis 패키지 실행을 예약 하는 **SQL Server 에이전트 작업이** 다를 수 있습니다. 다음과 같은 두 가지 시나리오가 있습니다.

- [**AZURE SQL Managed Instance** 를 데이터베이스 작업 대상으로](#azure-sql-managed-instance-as-database-workload-destination)
- [데이터베이스 작업 대상으로 **Azure SQL Database**](#azure-sql-database-as-database-workload-destination)

### <a name="azure-sql-managed-instance-as-database-workload-destination"></a>**AZURE SQL Managed Instance** 를 데이터베이스 작업 대상으로

| **패키지 저장소 유형** |SSIS 패키지를 일괄로 마이그레이션하는 방법|SSIS 작업을 일괄 처리 하는 방법|
|-|-|-|
|SSISDB|[**SSISDB** 마이그레이션](scenario-ssis-migration-ssisdb-mi.md)|<li>[SSIS 작업을 Azure SQL Managed Instance 에이전트로 마이그레이션](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li>스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환 합니다. 자세한 내용은 [SSMS 일정 기능](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조 하세요.|
|파일 시스템|Dtinstall/dtutil/manual copy를 통해 파일 공유/Azure Files에 다시 배포 하거나 VNet/자체 호스팅 IR을 통해 파일 시스템에 액세스할 수 있도록 유지 합니다. 자세한 내용은 [dtutil 유틸리티](https://docs.microsoft.com/sql/integration-services/dtutil-utility)를 참조 하세요.|<li>[SSIS 작업을 Azure SQL Managed Instance 에이전트로 마이그레이션](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> [SSMS에서 SSIS 작업 마이그레이션 마법사로](how-to-migrate-ssis-job-ssms.md) 마이그레이션 <li>스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환 합니다. 자세한 내용은 [SSMS 일정 기능](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조 하세요.|
|SQL Server (MSDB)|SSMS/dtutil을 통해 파일 시스템/파일 공유/Azure Files로 내보냅니다. 자세한 내용은 [SSIS 패키지 내보내기](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)를 참조 하세요.|스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환 합니다. 자세한 내용은 [SSMS 일정 기능](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조 하세요.|
|패키지 저장소|SSMS/dtutil을 통해 파일 시스템/파일 공유/Azure Files로 내보내거나, dtinstall/dtutil/manual 복사를 통해 파일 공유/Azure Files에 다시 배포 하거나, VNet/자체 호스팅 IR을 통해 액세스 하도록 파일 시스템에 보관 합니다. 자세한 내용은 dtutil 유틸리티를 참조 하세요. 자세한 내용은 [dtutil 유틸리티](https://docs.microsoft.com/sql/integration-services/dtutil-utility)를 참조 하세요.|<li>[SSIS 작업을 Azure SQL Managed Instance 에이전트로 마이그레이션](scenario-ssis-migration-ssisdb-mi.md#ssis-jobs-to-sql-managed-instance-agent) <li> 스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환 합니다. 자세한 내용은 [SSMS 일정 기능](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조 하세요.|

### <a name="azure-sql-database-as-database-workload-destination"></a>데이터베이스 작업 대상으로 **Azure SQL Database**

| **패키지 저장소 유형** |SSIS 패키지를 일괄로 마이그레이션하는 방법|작업을 일괄로 마이그레이션하는 방법|
|-|-|-|
|SSISDB|SSDT/SSMS를 통해 Azure에 다시 배포 합니다. 자세한 내용은 [Azure에서 SSIS 패키지 배포](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)를 참조 하세요.|스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환 합니다. 자세한 내용은 [SSMS 일정 기능](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조 하세요.|
|파일 시스템|Dtinstall/dtutil/manual copy를 통해 파일 공유/Azure Files에 다시 배포 하거나 VNet/자체 호스팅 IR을 통해 파일 시스템에 액세스할 수 있도록 유지 합니다. 자세한 내용은 [dtutil 유틸리티](https://docs.microsoft.com/sql/integration-services/dtutil-utility)를 참조 하세요.|<li> [SSMS에서 SSIS 작업 마이그레이션 마법사로](how-to-migrate-ssis-job-ssms.md) 마이그레이션 <li> 스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환 합니다. 자세한 내용은 [SSMS 일정 기능](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조 하세요.|
|SQL Server (MSDB)|SSMS/dtutil을 통해 파일 시스템/파일 공유/Azure Files로 내보냅니다. 자세한 내용은 [SSIS 패키지 내보내기](https://docs.microsoft.com/sql/integration-services/service/package-management-ssis-service#import-and-export-packages)를 참조 하세요.|스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환 합니다. 자세한 내용은 [SSMS 일정 기능](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조 하세요.|
|패키지 저장소|SSMS/dtutil을 통해 파일 시스템/파일 공유/Azure Files로 내보내거나, dtinstall/dtutil/manual 복사를 통해 파일 공유/Azure Files에 다시 배포 하거나, VNet/자체 호스팅 IR을 통해 액세스 하도록 파일 시스템에 보관 합니다. 자세한 내용은 dtutil 유틸리티를 참조 하세요. 자세한 내용은 [dtutil 유틸리티](https://docs.microsoft.com/sql/integration-services/dtutil-utility)를 참조 하세요.|스크립트/SSMS/ADF 포털을 통해 ADF 파이프라인/활동/트리거로 변환 합니다. 자세한 내용은 [SSMS 일정 기능](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)을 참조 하세요.|

## <a name="additional-resources"></a>추가 리소스

- [Azure Data Factory](https://docs.microsoft.com/azure/data-factory/introduction)
- [데이터베이스 마이그레이션 도우미](https://docs.microsoft.com/sql/dma/dma-overview)
- [SSIS 워크 로드를 클라우드로 리프트 앤 시프트](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview?view=sql-server-2017)
- [SSIS 패키지를 Azure SQL Managed Instance로 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages-managed-instance)
- [패키지를 Azure SQL Database에 다시 배포](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages)

## <a name="next-steps"></a>다음 단계

- [Azure에 배포된 SSIS 패키지 유효성 검사](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-validate-packages)
- [Azure에 배포 된 SSIS 패키지 실행](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-run-packages)
- [모니터 Azure-SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime)
- [Azure에서 SSIS 패키지 실행 예약](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages)
