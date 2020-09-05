---
title: 로그 디스크를 울트라 디스크로 마이그레이션
description: Azure VM (가상 머신) 로그 디스크의 SQL Server를 Azure Ultradisk로 마이그레이션하여 고성능 및 짧은 대기 시간을 활용 하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 07/09/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 42738ff71432284a156d0dfbb1f6cf160cbf4032
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2020
ms.locfileid: "88653253"
---
# <a name="migrate-log-disk-to-ultra-disk"></a>로그 디스크를 울트라 디스크로 마이그레이션
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure ultra disks는 Azure VM (가상 머신)의 SQL Server에 대해 높은 처리량, 높은 IOPS 및 지속적으로 짧은 대기 시간 디스크 저장소를 제공 합니다. 

이 문서에서는 로그 디스크를 ultra SSD로 마이그레이션하여 ultra disks에서 제공 하는 성능 이점을 활용 하는 방법을 설명 합니다. 

## <a name="back-up-database"></a>데이터베이스 백업

데이터베이스의 [전체 백업을](backup-restore.md) 완료 합니다. 

## <a name="attach-disk"></a>디스크 연결

VM에서 ultradisk compatibility를 사용 하도록 설정 하면 가상 머신에 울트라 SSD을 연결 합니다. 

울트라 디스크는 VM 크기 및 지역의 하위 집합에서 지원 됩니다. 계속 하기 전에 VM이 ultra disk를 지 원하는 지역, 영역 및 크기 인지 확인 합니다. Azure CLI 또는 PowerShell을 사용 하 여 [VM 크기와 지역을 확인 하 고 유효성을 검사할](../../../virtual-machines/disks-enable-ultra-ssd.md#determine-vm-size-and-region-availability) 수 있습니다. 

### <a name="enable-compatibility"></a>호환성 사용

호환성을 사용 하도록 설정 하려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에서 가상 머신으로 이동 합니다. 
1. 가상 컴퓨터를 중지/할당 취소 합니다. 
1. **설정** 아래에서 **디스크** 를 선택 하 고 **추가 설정**을 선택 합니다. 

   :::image type="content" source="media/storage-migrate-to-ultradisk/additional-disks-settings-azure-portal.png" alt-text="Azure Portal의 설정에서 디스크에 대 한 추가 설정을 선택 합니다.":::

1. **예** 를 선택 하 여 **Ultra Disk 호환성을 사용 하도록 설정**합니다. 

   :::image type="content" source="../../../virtual-machines/media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png" alt-text="Azure Portal의 설정에서 디스크에 대 한 추가 설정을 선택 합니다.":::

1. **저장**을 선택합니다. 



### <a name="attach-disk"></a>디스크 연결

Azure Portal를 사용 하 여 가상 머신에 울트라 디스크를 연결 합니다. 자세한 내용은 [ultra Disk 연결](../../../virtual-machines/disks-enable-ultra-ssd.md#attach-an-ultra-disk-using-the-azure-portal) 을 참조 하세요.

디스크가 연결 되 면 Azure Portal를 사용 하 여 VM을 한 번 더 시작 합니다. 



## <a name="format-disk"></a>디스크 포맷

가상 컴퓨터에 연결 하 고 울트라 디스크를 포맷 합니다.  

울트라 디스크를 포맷 하려면 다음 단계를 수행 합니다.

1. RDP (원격 데스크톱 프로토콜)를 사용 하 여 VM에 연결 합니다.
1. [디스크 관리](/windows-server/storage/disk-management/overview-of-disk-management) 를 사용 하 여 새로 연결 된 ultra disk의 형식을 지정 하 고 분할 합니다. 


## <a name="use-disk-for-log"></a>로그에 디스크 사용

새 로그 드라이브를 사용 하도록 SQL Server를 구성 합니다. Transact-sql (T-sql) 또는 SQL Server Management Studio (SSMS)를 사용 하 여이 작업을 수행할 수 있습니다. SQL Server 서비스 계정에 사용 되는 계정에는 새 로그 파일 위치에 대 한 모든 권한이 있어야 합니다. 

### <a name="configure-permissions"></a>권한 구성

1. SQL Server에서 사용 하는 서비스 계정을 확인 합니다. SQL Server 구성 관리자 또는 services.msc를 사용 하 여이 작업을 수행할 수 있습니다.
1. 새 디스크로 이동 합니다. 
1. 로그 파일에 사용할 폴더 (또는 여러 폴더)를 만듭니다. 
1. 해당 폴더를 마우스 오른쪽 단추로 클릭한 다음 **속성**을 선택합니다.
1. **보안** 탭에서 SQL Server 서비스 계정에 대 한 모든 권한을 부여 합니다. 
1. **확인** 을 선택 하 여 설정을 저장 합니다. 
1. SQL 데이터를 저장할 모든 루트 수준 폴더에 대해이 단계를 반복 합니다. 

### <a name="use-new-log-drive"></a>새 로그 드라이브 사용 

권한이 부여 된 후 Transact-sql (T-sql) 또는 SQL Server Management Studio (SSMS)를 사용 하 여 데이터베이스를 분리 하 고 기존 로그 파일을 새 위치로 이동 합니다.

   > [!CAUTION]
   > 데이터베이스를 분리 하면 오프 라인으로 전환 되 고 연결을 닫고 진행 중인 모든 트랜잭션을 롤백합니다. 잠시 후 유지 관리 기간 동안에는 주의 해야 합니다. 



# <a name="transact-sql-t-sql"></a>[Transact-SQL(T-SQL)](#tab/tsql)

T-sql을 사용 하 여 기존 파일을 새 위치로 이동 합니다.

1. SQL Server Management Studio에서 데이터베이스에 연결 하 고 **새 쿼리** 창을 엽니다. 
1. 기존 파일 및 위치를 가져옵니다.

   ```sql
   USE AdventureWorks
   GO

   sp_helpfile
   GO
   ```

1. 데이터베이스를 분리 합니다. 

   ```sql
   USE master
   GO

   sp_detach_db 'AdventureWorks'
   GO
   ```

1. 파일 탐색기를 사용 하 여 로그 파일을 ultra disk의 새 위치로 이동 합니다. 

1. 새 파일 위치를 지정 하 여 데이터베이스를 연결 합니다. 

   ```sql
    sp_attach_db 'AdventureWorks'
   'E:\Fixed_FG\AdventureWorks.mdf',
   'E:\Fixed_FG\AdventureWorks_2.ndf',
   'F:\New_Log\AdventureWorks_log.ldf'
   GO
   ```

이 시점에서 데이터베이스는 새 위치의 로그와 함께 온라인 상태가 됩니다. 



# <a name="sql-server-management-studio-ssms"></a>[SSMS(SQL Server Management Studio)](#tab/ssms)

SSMS를 사용 하 여 기존 파일을 새 위치로 이동 합니다.

1. SSMS (SQL Server Management Studio)에서 데이터베이스에 연결 합니다. 
1. 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **속성** 을 선택한 다음 **파일**을 선택 합니다. 
1. 기존 파일의 경로를 적어둡니다. 
1. **확인**을 선택하여 대화 상자를 닫습니다. 
1. 데이터베이스를 마우스 오른쪽 단추로 클릭 하 고 **태스크**  >  **분리**를 선택 합니다. 
1. 마법사에 따라 데이터베이스를 분리 합니다. 
1. 파일 탐색기를 사용 하 여 로그 파일을 수동으로 새 위치로 이동 합니다.
1. SQL Server Management Studio에서 데이터베이스 연결
   1. **개체 탐색기** 에서 **데이터베이스** 를 마우스 오른쪽 단추로 클릭 하 고 **데이터베이스 연결**을 선택 합니다. 
   1. 대화 상자를 사용 하 여 새 위치에 로그 파일을 포함 하 여 각 파일을 추가 합니다. 
   1. **확인** 을 선택 하 여 데이터베이스를 연결 합니다. 

이 시점에서 데이터베이스는 새 위치의 로그와 함께 온라인 상태가 됩니다.

---


## <a name="next-steps"></a>다음 단계

성능 향상을 위해 추가 설정에 대 한 [성능 모범 사례](performance-guidelines-best-practices.md) 를 검토 합니다. 

Azure Virtual Machines에서 SQL Server에 대 한 개요는 다음 문서를 참조 하세요.

- [Windows VM의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
- [Linux VM의 SQL Server 개요](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
