---
title: Microsoft Azure Backup Server v3 릴리스 정보
description: 이 문서에서는 MABS (Microsoft Azure Backup 서버) v3의 알려진 문제 및 해결 방법에 대한 정보를 제공 합니다.
ms.topic: conceptual
ms.date: 11/22/2018
ms.asset: 0c4127f2-d936-48ef-b430-a9198e425d81
ms.openlocfilehash: b47d83e0e3714f3f035397acaadeac9cda39d12a
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2019
ms.locfileid: "74172273"
---
# <a name="release-notes-for-microsoft-azure-backup-server"></a>Microsoft Azure Backup Server 릴리스 정보

이 문서에서는 MABS(Microsoft Azure Backup Server) V3에 대해 알려진 문제와 해결 방법을 제공합니다.

## <a name="backup-and-recovery-fails-for-clustered-workloads"></a>클러스터된 워크로드에 대한 백업 및 복구 실패

**설명:** MABS V2를 MABS V3으로 업그레이드하면 Hyper-V 클러스터, SQL 클러스터(SQL Always On) 또는 DAG(데이터베이스 가용성 그룹)의 Exchange와 같은 클러스터된 데이터 원본에 대해 백업/복구가 실패합니다.

**해결 방법:** 이를 방지하려면 SSMS(SQL Server Management Studio)를 열고 DPM DB에서 다음 SQL 스크립트를 실행합니다.

```sql
    IF EXISTS (SELECT * FROM dbo.sysobjects
        WHERE id = OBJECT_ID(N'[dbo].[tbl_PRM_DatasourceLastActiveServerMap]')
        AND OBJECTPROPERTY(id, N'IsUserTable') = 1)
        DROP TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap]
        GO

        CREATE TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] (
            [DatasourceId]          [GUID]          NOT NULL,
            [ActiveNode]            [nvarchar](256) NULL,
            [IsGCed]                [bit]           NOT NULL
            ) ON [PRIMARY]
        GO

        ALTER TABLE [dbo].[tbl_PRM_DatasourceLastActiveServerMap] ADD
    CONSTRAINT [pk__tbl_PRM_DatasourceLastActiveServerMap__DatasourceId] PRIMARY KEY NONCLUSTERED
        (
            [DatasourceId]
        )  ON [PRIMARY],

    CONSTRAINT [DF_tbl_PRM_DatasourceLastActiveServerMap_IsGCed] DEFAULT
        (
            0
        ) FOR [IsGCed]
    GO
```

## <a name="upgrade-to-mabs-v3-fails-in-russian-locale"></a>러시아어 로캘에서 MABS V3로의 업그레이드 실패

**설명:** **4387** 오류 코드로 인해 러시아어 로캘의 MABS V2에서 MABS V3으로 업그레이드하지 못했습니다.

**해결 방법:** 다음 단계에 따라 러시아어 설치 패키지를 사용하여 MABS V3으로 업그레이드합니다.

1. SQL 데이터베이스를 [백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server?view=sql-server-2017#SSMSProcedure)하고, MABS V2를 제거합니다(제거하는 동안 보호된 데이터를 유지하도록 선택함).
2. SQL 2017(Enterprise)로 업그레이드하고, 업그레이드의 일환으로 보고를 제거합니다.
3. SSRS(SQL Server Reporting Services)를 [설치](https://docs.microsoft.com/sql/reporting-services/install-windows/install-reporting-services?view=sql-server-2017#install-your-report-server)합니다.
4. SSMS(SQL Server Management Studio)를 [설치](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)합니다.
5. [SQL 2017에서 SSRS 구성](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#upgrade-mabs)에서 설명한 대로 매개 변수를 사용하여 보고를 구성합니다.
6. MABS V3을 [설치](backup-azure-microsoft-azure-backup.md)합니다.
7. SSMS를 사용하여 SQL을 [복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/restore-a-database-backup-using-ssms?view=sql-server-2017)하고, [여기](https://docs.microsoft.com/previous-versions/system-center/data-protection-manager-2010/ff634215(v=technet.10))서 설명한 대로 DPM 동기화 도구를 실행합니다.
8. 다음 명령을 사용하여 dbo.tbl_DLS_GlobalSetting 테이블의 'DataBaseVersion' 속성을 업데이트합니다.

```sql
        UPDATE dbo.tbl_DLS_GlobalSetting
        set PropertyValue = '13.0.415.0'
        where PropertyName = 'DatabaseVersion'
```

9. MSDPM 서비스를 시작합니다.

## <a name="next-steps"></a>다음 단계

[MABS V3의 새로운 기능](backup-mabs-whats-new-mabs.md)
