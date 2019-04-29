---
title: SQL Server 백업 및 복원에 Azure 저장소를 사용하는 방법 | Microsoft Docs
description: Azure Storage에 SQL Server를 백업하는 방법에 알아봅니다. Azure Storage에 SQL 데이터베이스를 백업할 때의 이점에 대해 설명합니다.
services: virtual-machines-windows
documentationcenter: ''
author: MikeRayMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 0db7667d-ef63-4e2b-bd4d-574802090f8b
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mikeray
ms.openlocfilehash: 1b6660a1565b3c119cc1dec0823870c7dd5bd24f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477147"
---
# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>SQL Server 백업 및 복원에 Azure Storage 사용
## <a name="overview"></a>개요
SQL Server 2012 SP1 CU2부터는 Azure Blob Storage 서비스에 직접 SQL Server 백업을 쓸 수 있습니다. 이 기능을 사용하여 온-프레미스 SQL Server 데이터베이스 또는 Azure 가상 머신의 SQL Server 데이터베이스를 사용하여 Azure Blob service에 백업하고 Azure Blob service에서 복원할 수 있습니다. 클라우드로 Backup하면 가용성, 무제한으로 지역에서 복제되는 오프사이트 저장소, 클라우드로/에서 쉬운 데이터 마이그레이션 등의 이점이 있습니다. Transact-SQL 또는 SMO를 사용하여 BACKUP 또는 RESTORE 문을 실행할 수 있습니다.

SQL Server 2016에는 여러 새 기능이 포함되어 있습니다. [파일-스냅숏 백업](https://msdn.microsoft.com/library/mt169363.aspx)을 사용하여 거의 즉시 백업을 수행하고 매우 빠르게 복원할 수 있습니다.

이 항목에서는 SQL 백업을 위해 Azure Storage를 사용하도록 선택하는 이유와 관련 구성 요소에 대해 설명합니다. 문서 끝에 제공되는 리소스를 사용하여 연습 과정 및 SQL Server 백업에서 이 서비스를 사용하기 위한 추가 정보에 액세스할 수 있습니다.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>SQL Server Backup에 Azure Blob Service를 사용할 경우의 이점
SQL Server를 백업할 때 발생하는 몇 가지 해결 과제는 다음과 같습니다. 여기에는 저장소 관리, 저장소 오류 위험, 오프사이트 저장소에 대한 액세스, 하드웨어 구성 등이 포함됩니다. 이러한 문제 대부분은 SQL Server 백업을 위해 Azure Blob 저장소 서비스를 사용하여 지정됩니다. 다음과 같은 이점을 고려합니다.

* **사용 편의성**: Azure Blob 서비스에 백업을 저장하면 오프사이트 옵션에 쉽고 간편하며 유연하게 액세스할 수 있습니다. SQL Server 백업을 위한 오프사이트 저장소를 만들 때 **BACKUP TO URL** 구문을 사용하면 기존 스크립트/작업을 수정하는 것만큼 쉽습니다. 오프사이트와 프로덕션 데이터베이스 위치 둘 다에 영향을 줄 수 있는 단일 재해를 방지하려면 오프사이트 저장소는 일반적으로 프로덕션 데이터베이스 위치와 충분히 멀리 떨어져 있어야 합니다. [Azure Blob에 대해 지역에서 복제](../../../storage/common/storage-redundancy.md)를 선택하면 전 지역에 영향을 줄 수 있는 재해가 발생하는 경우에도 추가적인 보호 방법을 확보할 수 있습니다.
* **백업 보관**: Azure Blob Storage 서비스는 백업을 보관하는 데 흔히 사용되는 테이프 옵션보다 탁월한 대안을 제공합니다. 테이프 저장소를 사용하려면 미디어를 보호하기 위해 오프사이트 시설 및 수단까지 테이프를 물리적으로 운반해야 할 수 있습니다. Azure Blob Storage에 백업 저장은 즉각적이고 내구성 있는 고가용성 보관 옵션을 제공합니다.
* **관리되는 하드웨어**: Azure 서비스를 사용하면 하드웨어 관리의 오버헤드가 없습니다. Azure 서비스에서 하드웨어를 관리하며 하드웨어 오류 방지와 중복을 위해 지역에서 복제 옵션을 제공합니다.
* **무제한 스토리지**: Azure Blob에 직접 백업할 수 있도록 설정하면 무제한 스토리지에 가상으로 액세스할 수 있습니다. 또는 Azure 가상 머신 디스크로 백업하면 컴퓨터 크기에 따라 제한이 적용됩니다. 백업을 위해 Azure 가상 머신에 연결할 수 있는 디스크의 수에는 제한이 있습니다. 매우 큰 인스턴스의 경우 16개의 디스크로 제한되고 작은 인스턴스의 경우에는 디스크의 수가 더 적습니다.
* **Backup 가용성**: Azure Blob에 저장된 Backup은 데이터베이스 연결/분리 또는 VHD 다운로드 및 연결 과정 없이도 언제 어디서나 사용 가능하며 온-프레미스 SQL Server 또는 Azure Virtual Machine에서 실행되는 다른 SQL Server에 대한 복원을 위해 쉽게 액세스할 수 있습니다.
* **비용**: 사용하는 서비스에 대해서만 결제합니다. 오프사이트 및 백업 보관 옵션으로 비용 효율성을 추구할 수 있습니다. 자세한 내용은 [Azure 가격 계산기](https://go.microsoft.com/fwlink/?LinkId=277060 "Pricing Calculator") 및 [Azure 가격 책정 문서](https://go.microsoft.com/fwlink/?LinkId=277059 "Pricing article")를 참조하세요.
* **스토리지 스냅숏**: 데이터베이스 파일이 Azure Blob에 저장되고 SQL Server 2016을 사용하는 경우 [파일-스냅숏 백업](https://msdn.microsoft.com/library/mt169363.aspx) 을 사용하여 거의 즉각적인 백업 및 매우 빠른 복원을 수행할 수 있습니다.

자세한 내용은 [Azure Blob Storage 서비스로 SQL Server 백업 및 복원](https://go.microsoft.com/fwlink/?LinkId=271617)을 참조하십시오.

다음 두 섹션에서는 필수 SQL Server 구성 요소를 포함하여 Azure Blob Storage 서비스를 소개합니다. Azure Blob Storage 서비스로 백업하거나 서비스에서 복원하기 위해서는 구성 요소와 구성 요소 간 조작에 대해 이해하는 것이 중요합니다.

## <a name="azure-blob-storage-service-components"></a>Azure Blob Storage 서비스 구성 요소
다음 Azure 구성 요소는 Azure Blob Storage 서비스로 백업할 때 사용됩니다.

| 구성 요소 | 설명 |
| --- | --- |
| **Storage 계정** |저장소 계정은 모든 저장소 서비스를 사용하기 위한 출발점입니다. Azure Blob Storage 서비스에 액세스하려면 먼저 Azure Storage 계정을 만드세요. Azure Blob Storage 서비스에 대한 자세한 내용은 [Azure Blob Storage 서비스를 사용하는 방법](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **컨테이너** |컨테이너는 Blob 집합의 그룹화를 제공하며 Blob을 개수에 제한 없이 저장할 수 있습니다. SQL Server 백업을 Azure Blob service에 쓰려면 최소한 루트 컨테이너를 만들어야 합니다. |
| **Blob** |임의 형식 및 크기의 파일입니다. Blob은 다음 URL 형식을 사용하여 주소를 지정할 수 있습니다. **https://[storage account].blob.core.windows.net/[container]/[blob]** 페이지 Blob에 대한 자세한 내용은 [블록 및 페이지 Blob 이해](https://msdn.microsoft.com/library/azure/ee691964.aspx)를 참조하세요. |

## <a name="sql-server-components"></a>SQL Server 구성 요소
다음 SQL Server 구성 요소는 Azure Blob Storage 서비스로 백업할 때 사용됩니다.

| 구성 요소 | 설명 |
| --- | --- |
| **URL** |URL은 고유한 백업 파일에 대한 URI(Uniform Resource Identifier)를 지정합니다. URL은 SQL Server 백업 파일의 위치 및 이름을 지정하는 데 사용합니다. URL은 컨테이너가 아닌 실제 Blob을 가리켜야 합니다. Blob이 없는 경우 새로 만듭니다. 기존 Blob이 지정된 경우 > WITH FORMAT 옵션을 지정하지 않으면 BACKUP이 실패합니다. 다음은 BACKUP 명령에 지정하는 URL 예제입니다. **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]** HTTPS는 필수가 아니지만 사용하는 것이 좋습니다. |
| **자격 증명** |Azure Blob Storage 서비스에 연결하고 인증하는 데 필요한 정보는 자격 증명으로 저장됩니다.  SQL Server가 백업을 Azure Blob에 쓰거나 Azure Blob에서 복원하려면 SQL Server 자격 증명을 만들어야 합니다. 자세한 내용은 [SQL Server 자격 증명](https://msdn.microsoft.com/library/ms189522.aspx)을 참조하세요. |

> [!NOTE]
> Sql Server 2016은 블록 Blob을 지원하도록 업데이트되었습니다. 자세한 내용은 [자습서: SQL Server 2016 데이터베이스에서 Microsoft Azure Blob 스토리지 서비스 사용](https://msdn.microsoft.com/library/dn466438.aspx)을 참조하세요.
> 
> 

## <a name="next-steps"></a>다음 단계
1. 아직 Azure 계정이 없는 경우 새로 하나 만듭니다. Azure를 평가하는 경우 [무료 평가판](https://azure.microsoft.com/free/)을 고려하세요.
2. 저장소 계정을 만들고 복원을 수행하는 과정을 안내하는 다음 자습서 중 하나를 진행합니다.
   
   * **SQL Server 2014**: [자습서: Microsoft Azure Blob Storage 서비스로 SQL Server 2014 백업 및 복원](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx)
   * **SQL Server 2016**: [자습서: SQL Server 2016 데이터베이스에서 Microsoft Azure Blob 스토리지 서비스 사용](https://msdn.microsoft.com/library/dn466438.aspx)
3. [Microsoft Azure Blob Storage 서비스로 SQL Server 백업 및 복원](https://msdn.microsoft.com/library/jj919148.aspx)부터 시작해서 추가 설명서를 검토하세요.

문제가 발생하는 경우 [URL에 대한 SQL Server Backup - 최상의 방법 및 문제 해결](https://msdn.microsoft.com/library/jj919149.aspx)항목을 검토하세요.

기타 SQL Server 백업 및 복원 옵션에 대해서는 [Azure Virtual Machines에서 SQL Server 백업 및 복원](virtual-machines-windows-sql-backup-recovery.md)을 참조하세요.

