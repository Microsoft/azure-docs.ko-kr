---
title: PowerShell을 사용하여 Azure Analysis Services 관리 | Microsoft Docs
description: 서버 만들기, 작업 일시 중단 또는 서비스 수준 변경과 같은 일반적인 관리 작업을 위한 PowerShell cmdlet Azure Analysis Services 설명 합니다.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: reference
ms.date: 10/28/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 2c8f4c0541d97a189087af692658cfe794eaaf7e
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2019
ms.locfileid: "73572703"
---
# <a name="manage-azure-analysis-services-with-powershell"></a>PowerShell을 사용하여 Azure Analysis Services 관리

이 문서에서는 Azure Analysis Services 서버 및 데이터베이스 관리 작업을 수행하는 데 사용되는 PowerShell cmdlet에 대해 설명합니다. 

서버를 만들거나 삭제 하 고, 서버 작업을 일시 중지 하거나 다시 시작 하거나, 서비스 수준 (계층)을 변경 하는 등의 서버 리소스 관리 작업은 Azure Analysis Services cmdlet을 사용 합니다. 역할 멤버 추가 또는 제거, 처리 또는 분할과 같은 기타 데이터베이스 관리 작업에서는 SQL Server Analysis Services와 동일한 SqlServer 모듈에 포함된 cmdlet을 사용합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="permissions"></a>권한

대부분의 PowerShell 작업에는 관리하는 Analysis Services 서버에 대해 관리자 권한이 있어야 합니다. 예약된 PowerShell 작업은 무인 작업입니다. 스케줄러를 실행하는 계정 또는 서비스 주체에게는 Analysis Services 서버에 대해 관리자 권한이 있어야 합니다. 

Azure PowerShell cmdlet을 사용 하는 서버 작업의 경우 스케줄러를 실행 하는 계정 또는 계정이 [Azure 역할 기반 Access Control (RBAC)](../role-based-access-control/overview.md)의 리소스에 대한 소유자 역할에도 속해야 합니다. 

## <a name="resource-and-server-operations"></a>리소스 및 서버 작업 

설치 모듈- [Az. microsoft.analysisservices.sharepoint.integration.dll](https://www.powershellgallery.com/packages/Az.AnalysisServices)   
설명서- [Az. microsoft.analysisservices.sharepoint.integration.dll reference](/powershell/module/az.analysisservices)

## <a name="database-operations"></a>데이터베이스 작업

Azure Analysis Services 데이터베이스 작업은 SQL Server Analysis Services와 동일한 SqlServer 모듈을 사용 합니다. 그러나 모든 cmdlet이 Azure Analysis Services에서 지원되는 것은 아닙니다. 

SqlServer 모듈은 TMSL(테이블 형식 모델 스크립트 언어) 쿼리 또는 스크립트를 허용하는 범용 Invoke-ASCmd cmdlet뿐만 아니라 작업 관련 데이터베이스 관리 cmdlet도 제공합니다. SqlServer 모듈의 다음 cmdlet은 Azure Analysis Services에서 지원됩니다.

설치 모듈- [SqlServer](https://www.powershellgallery.com/packages/SqlServer)   
설명서- [SqlServer 참조](/powershell/module/sqlserver)

### <a name="supported-cmdlets"></a>지원 되는 cmdlet

|Cmdlet|설명|
|------------|-----------------| 
|[Add-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|데이터베이스 역할에 구성원을 추가합니다.| 
|[Backup-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/backup-asdatabase)|Analysis Services 데이터베이스를 백업합니다.|  
|[Remove-RoleMember](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|데이터베이스 역할에서 구성원을 제거합니다.|   
|[Invoke-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|TMSL 스크립트를 실행합니다.|
|[Invoke-ProcessASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processasdatabase)|데이터베이스를 처리합니다.|  
|[Invoke-ProcessPartition](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processpartition)|파티션을 처리합니다.| 
|[Invoke-ProcessTable](https://docs.microsoft.com/powershell/module/sqlserver/invoke-processtable)|테이블을 처리합니다.|  
|[Merge-Partition](https://docs.microsoft.com/powershell/module/sqlserver/merge-partition)|파티션을 병합합니다.|  
|[Restore-ASDatabase](https://docs.microsoft.com/powershell/module/sqlserver/restore-asdatabase)|Analysis Services 데이터베이스를 복원합니다.| 
  

## <a name="related-information"></a>관련 정보

* [SQL Server PowerShell](https://docs.microsoft.com/sql/powershell/sql-server-powershell)      
* [SQL Server PowerShell 모듈 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-ps-module)   
* [SSMS 다운로드](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)   
* [SqlServer module in PowerShell Gallery](https://www.powershellgallery.com/packages/SqlServer)(PowerShell 갤러리의 SqlServer 모듈)    
* [Tabular Model Programming for Compatibility Level 1200 and higher](https://docs.microsoft.com/analysis-services/tabular-model-programming-compatibility-level-1200/tabular-model-programming-for-compatibility-level-1200)(호환성 수준 1200 이상에 대한 테이블 형식 모델 프로그래밍)
