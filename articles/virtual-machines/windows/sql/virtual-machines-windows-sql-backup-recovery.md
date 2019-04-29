---
title: Azure VM의 SQL Server에 대한 백업 및 복원 | Microsoft Docs
description: Azure Virtual Machines에서 실행되는 SQL Server 데이터베이스의 백업 및 복원 시 고려 사항에 대해 설명합니다.
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: craigg
editor: ''
tags: azure-resource-management
ms.assetid: 95a89072-0edf-49b5-88ed-584891c0e066
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/04/2018
ms.author: mikeray
ms.openlocfilehash: ab239d0546508d74874c6b6be03f6afc06b08fa7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60563433"
---
# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Azure Virtual Machines에서 SQL Server의 백업 및 복원

이 문서에서는 Azure의 Windows 가상 머신에서 실행 중인 SQL Server에 사용할 수 있는 백업 및 복원 옵션에 대한 지침을 제공합니다. 데이터 손실이나 실제 데이터 손상에 대한 보호를 보장하기 위해 Azure Storage에는 모든 Azure VM 디스크의 복사본을 3개씩 유지 관리합니다. 따라서 온-프레미스와는 달리 하드웨어 오류에 집중할 필요가 없습니다. 그러나 의도하지 않은 데이터 삽입 또는 삭제와 같은 애플리케이션이나 사용자 오류로부터 보호하기 위해 SQL Server 데이터베이스 백업을 계속해야 합니다. 이러한 경우에 특정 시점으로 복원할 수 있어야 합니다.

이 문서의 첫 번째 부분은 사용 가능한 백업 및 복원 옵션의 개요를 제공합니다. 각 전략에 대한 자세한 정보를 제공하는 섹션이 뒤따릅니다.

## <a name="backup-and-restore-options"></a>백업 및 복원 옵션

다음 표는 Azure VM에서 실행 중인 SQL Server에 대한 다양한 백업 및 복원 옵션의 정보를 제공합니다.

| 전략 | SQL 버전 | 설명 |
|---|---|---|
| [자동화된 Backup](#automated) | 2014<br/> 2016<br/> 2017 | 자동화된 백업을 통해 SQL Server VM의 모든 데이터베이스에 대해 정기 백업을 예약할 수 있습니다. 백업은 최대 30일 동안 Azure 저장소에 저장됩니다. SQL Server 2016부터 자동화된 백업 v2는 수동 예약 및 전체 및 로그 백업의 빈도 구성과 같은 추가 옵션을 제공합니다. |
| [SQL VM에 대한 Azure Backup](#azbackup) | 2012<br/> 2014<br/> 2016<br/> 2017 | Azure Backup은 Azure VM에서 실행 중인 SQL Server에 대한 Enterprise 클래스 백업 기능을 제공합니다. 이 서비스를 사용하여 여러 서버 및 데이터베이스에 대한 백업을 중앙에서 관리할 수 있습니다. 포털에서 특정 시점으로 데이터베이스를 복원할 수 있습니다. 몇 년 동안 백업을 유지할 수 있는 사용자 지정 가능한 보존 정책을 제공합니다. 이 기능은 현재 공개 미리 보기로 제공됩니다. |
| [수동 백업](#manual) | 모두 | SQL Server의 버전에 따라 Azure VM에서 실행 중인 SQL Server를 수동으로 백업 및 복원하는 다양한 기술이 있습니다. 이 시나리오에서는 데이터베이스가 백업되는 방법 및 저장소 위치 및 이러한 백업 관리를 담당합니다. |

다음 섹션에서는 각 옵션에 대해 자세히 설명합니다. 이 문서의 마지막 섹션은 기능 매트릭스의 형태로 요약을 제공합니다.

## <a id="automated"></a> 자동화된 백업

자동화된 백업은 Azure의 Windows VM에서 실행 중인 SQL Server Standard 및 Enterprise 버전에 대한 자동 백업 서비스를 제공합니다. 이 서비스는 Azure Portal에서 SQL Server Windows 가상 머신 이미지에 자동으로 설치되는 [SQL Server IaaS 에이전트 확장](virtual-machines-windows-sql-server-agent-extension.md)에서 제공됩니다.

모든 데이터베이스는 구성하는 Azure 저장소 계정에 백업됩니다. 백업은 암호화되고 최대 30일 동안 보존될 수 있습니다.

SQL Server 2016 이상 VM은 자동화된 백업 v2와 함께 더 많은 사용자 지정 옵션을 제공합니다. 향상된 기능은 다음과 같습니다.

- 시스템 데이터베이스 백업
- 수동 백업 일정 및 시간 창
- 전체 및 로그 파일 백업 빈도

데이터베이스를 복원하려면 저장소 계정에 필요한 백업 파일을 배치하고 SSMS(SQL Server Management Studio) 또는 Transact-SQL 명령을 사용하여 SQL VM에서 복원을 수행해야 합니다.

SQL VM에 대해 자동화된 백업을 구성하는 방법에 대한 자세한 내용은 다음 문서 중 하나를 참조하세요.

- **SQL Server 2016/2017**: [Azure Virtual Machines의 자동화 된 Backup v2](virtual-machines-windows-sql-automated-backup-v2.md)
- **SQL Server 2014**: [SQL Server 2014 Virtual Machines의 자동화된 백업](virtual-machines-windows-sql-automated-backup.md)

## <a id="azbackup"></a> SQL Vm 용 azure Backup

[Azure Backup](/azure/backup/)은 Azure VM에서 실행 중인 SQL Server에 대한 Enterprise 클래스 백업 기능을 제공합니다. 모든 백업은 Recovery Services 자격 증명 모음에서 저장되고 관리됩니다. 이 솔루션이 제공하는(특히 Enterprises에 대해) 다양한 이점이 있습니다.

- **인프라가 없는 백업**: 백업 서버 또는 스토리지 위치를 관리할 필요가 없습니다.
- **크기 조정**: 많은 SQL VM 및 수 천개의 데이터베이스를 보호합니다.
- **종량제**: 이 기능은 Azure Backup에서 제공하는 별도 서비스이지만 모든 Azure 서비스와 마찬가지로 사용한 양만큼만 요금을 지불합니다.
- **중앙 관리 및 모니터링**: Azure의 단일 대시보드에서 Azure Backup이 지원하는 다른 작업을 포함한 모든 백업을 중앙에서 관리합니다.
- **정책 기반 백업 및 보존**: 정기 백업에 대한 표준 백업 정책을 만듭니다. 몇 년 동안 백업을 유지 관리하는 보존 정책을 설정합니다.
- **SQL Always On에 대한 지원**: SQL Server Always On 구성을 검색 및 보호하고 백업 가용성 그룹 백업 기본 설정을 적용합니다.
- **15분 RPO(복구 지점 목표)**: 최대 15분마다 SQL 트랜잭션 로그 백업을 구성합니다.
- **특정 시점 복원**: 전체, 차등 및 로그 백업을 수동으로 복원할 필요 없이 포털을 사용하여 데이터베이스를 특정 시점으로 복구합니다.
- **오류에 대한 통합된 이메일 경고**: 오류에 대한 통합된 이메일 알림을 구성합니다.
- **역할 기반 액세스 제어**: 포털을 통해 백업 및 복원 작업을 관리할 수 있는 사용자를 결정합니다.

데모와 함께 작동 방법에 대한 간략한 개요는 다음 비디오를 봅니다.

> [!VIDEO https://www.youtube.com/embed/wmbANpHos_E]

SQL vm이 Azure Backup 솔루션은 일반적으로 사용할 수 있습니다. 자세한 내용은 [Azure에 SQL Server 데이터베이스 백업](../../../backup/backup-azure-sql-database.md)을 참조하세요.

## <a id="manual"></a> 수동 백업

SQL VM에서 백업 및 복원 작업을 수동으로 관리하려는 경우 사용하는 SQL Server의 버전에 따라 몇 가지 옵션이 있습니다. 백업 및 복원의 개요는 SQL Server의 버전에 따라 다음 문서 중 하나를 참조하세요.

- [SQL Server 2016 이상에 대한 백업 및 복원](https://docs.microsoft.com/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)
- [SQL Server 2014에 대한 백업 및 복원](https://msdn.microsoft.com/library/ms187048%28v=sql.120%29.aspx)
- [SQL Server 2012에 대한 백업 및 복원](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx)
- [SQL Server SQL Server 2008 R2에 대한 백업 및 복원](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx)
- [SQL Server 2008에 대한 백업 및 복원](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx)

다음 섹션에서는 여러 수동 백업 및 복원 옵션을 자세히 설명합니다.

### <a name="backup-to-attached-disks"></a>연결된 디스크에 백업

Azure VM에서 실행되는 SQL Server의 경우 백업 파일의 대상에 대한 VM에서 연결된 디스크를 사용하는 네이티브 백업 및 복원 기법을 사용할 수 있습니다. 그러나 [가상 머신의 크기](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)에 따라 Azure 가상 머신에 연결할 수 있는 디스크의 수에는 제한이 있습니다. 또한 고려해야 할 디스크 관리에 대한 오버헤드도 있습니다.

SSMS(SQL Server Management Studio) 또는 Transact-SQL을 사용하여 전체 데이터베이스 백업을 수동으로 만드는 방법의 예제는 [전체 데이터베이스 백업 만들기](https://docs.microsoft.com/sql/relational-databases/backup-restore/create-a-full-database-backup-sql-server)를 참조하세요.

### <a name="backup-to-url"></a>URL로 백업

SQL Server 2012 SP1 CU2부터 URL에 백업이라고도 하는 Microsoft Azure Blob Storage에 직접 백업 및 복원할 수 있습니다. SQL Server 2016은 이 기능에 대한 다음과 같은 고급 기능을 도입했습니다.

| 2016의 향상된 기능 | 세부 정보 |
| --- | --- |
| **스트라이프** |Microsoft Azure Blob Storage에 백업하는 경우 SQL Server 2016에서는 대형 데이터베이스를 백업할 수 있도록 최대 12.8TB까지 여러 Blob으로의 백업을 지원합니다. |
| **스냅숏 백업** |Azure 스냅숏을 사용하는 경우 SQL Server 파일-스냅숏 Backup 기능은 Azure Blob Storage 서비스를 사용하여 저장한 데이터베이스 파일을 거의 즉시 백업하고 신속하게 복원하는 기능을 제공합니다. 이 기능을 사용하면 백업 및 복원 정책을 단순화할 수 있습니다. 또한 파일-스냅숏 백업 기능은 특정 시점 복원도 지원합니다. 자세한 내용은 [Azure에서 데이터베이스 파일에 대한 스냅숏 Backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/file-snapshot-backups-for-database-files-in-azure)을 참조하세요. |

자세한 내용은 SQL Server의 버전에 따라 다음 문서 중 하나를 참조하세요.

- **SQL Server 2016/2017**: [URL에 SQL Server Backup](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
- **SQL Server 2014**: [URL에 SQL Server 2014 Backup](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx)
- **SQL Server 2012**: [URL에 SQL Server 2012 Backup](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx)

### <a name="managed-backup"></a>관리되는 백업

SQL Server 2014부터 관리되는 백업은 Azure 저장소로 백업의 생성을 자동화합니다. 내부적으로 관리되는 백업은 이 문서의 이전 섹션에서 설명된 URL 기능에 대한 백업을 활용합니다. 관리되는 백업은 SQL Server VM 자동화된 백업 서비스를 지원하는 기본 기능이기도 합니다.

SQL Server 2016부터 관리되는 백업은 예약, 시스템 데이터베이스 백업 및 전체 및 로그 백업 빈도에 대한 추가 옵션을 가져왔습니다.

자세한 내용은 SQL Server의 버전에 따라 다음 문서 중 하나를 참조하세요.

- [SQL Server 2016 이상용 Microsoft Azure에 대한 관리되는 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure)
- [SQL Server 2014용 Microsoft Azure에 대한 관리되는 백업](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx)

## <a name="decision-matrix"></a>의사 결정 행렬

다음 표에서는 Azure의 SQL Server 가상 머신에 대한 각 백업 및 복원 옵션의 기능을 요약합니다.

|| **자동화된 Backup** | **SQL에 대한 Azure Backup** | **수동 백업** |
|---|---|---|---|
| 추가 Azure 서비스 필요 |   | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure Portal에서 백업 정책 구성 | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure Portal에서 데이터베이스 복원 |   | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 하나의 대시보드에서 여러 서버 관리 |   | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 지정 시간 복원 | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 15분 RPO(복구 지점 목표) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 단기 백업 보존 정책(일) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 장기 백업 보존 정책(월, 년) |   | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| SQL Server Always On에 대한 기본 제공 지원 |   | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure Storage 계정에 백업 | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(자동) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(자동) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png)(고객 관리) |
| 저장소 및 백업 파일의 관리 | | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |  |
| VM의 연결된 디스크에 백업 |   |   | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| 사용자 지정 가능한 중앙 백업 보고서 |   | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| 오류에 대한 통합된 이메일 경고 |   | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| Azure Monitor 로그를 기반으로 모니터링 하는 사용자 지정 |   | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   |
| SSMS 또는 Transact-SQL 스크립트를 사용하여 백업 작업 모니터링 | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |
| SSMS 또는 Transact-SQL 스크립트를 사용하여 데이터베이스 복원 | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |   | ![예](./media/virtual-machines-windows-sql-backup-recovery/yes.png) |

## <a name="next-steps"></a>다음 단계

Azure VM에서 SQL Server의 배포를 계획 중인 경우 다음 가이드에서 프로비전 지침을 찾을 수 있습니다. [Azure Portal에서 Windows SQL Server 가상 머신을 프로비전하는 방법](virtual-machines-windows-portal-sql-server-provision.md)

데이터를 마이그레이션하는 데 백업 및 복원을 사용할 수 있지만 Azure VM의 SQL Server로 데이터를 마이그레이션하는 훨씬 간편한 경로가 있습니다. 마이그레이션 옵션 및 권장 사항에 대한 자세한 내용은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)을 참조하세요.
