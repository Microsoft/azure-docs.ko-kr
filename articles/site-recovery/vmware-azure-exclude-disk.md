---
title: Azure Site Recovery를 사용한 VMware VM과 Azure 간 재해 복구를 위한 복제에서 디스크 제외 | Microsoft Docs
description: VMware와 Azure 간 재해 복구를 위한 복제에서 VM 디스크를 제외하는 이유와 방법을 설명합니다.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.date: 3/3/2019
ms.author: mayg
ms.topic: conceptual
ms.openlocfilehash: 105074892cc6dfa4da1e7c8ddd0a0aad9f1b60a1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60922124"
---
# <a name="exclude-disks-from-replication-of-vmware-vms-to-azure"></a>VMware VM과 Azure 간 복제에서 디스크 제외

이 문서에서는 Azure로 VMware VM을 복제할 때 디스크를 제외하는 방법을 설명합니다. 이 제외는 그러한 디스크가 활용하는 대상 쪽 리소스를 최적화하거나 소비된 복제 대역폭을 최적화할 수 있습니다. Hyper-V에서 디스크 제외에 관한 정보가 필요한 경우 [이 문서](hyper-v-exclude-disk.md)를 읽어보세요.


## <a name="prerequisites"></a>필수 조건

기본적으로 컴퓨터의 모든 디스크가 복제됩니다. VMware에서 Azure로 복제하는 경우 복제에서 디스크를 제외하려면 복제를 사용하도록 설정하기 전에 컴퓨터에 모바일 서비스를 수동으로 설치해야 합니다.


## <a name="why-exclude-disks-from-replication"></a>복제에서 디스크를 제외하는 이유는?
다음과 같은 이유로 복제에서 디스크를 제외해야 하는 경우가 자주 있습니다.

- 제외되는 디스크에서 변동된 데이터는 중요하지 않거나 복제할 필요가 없습니다.

- 저장소 및 네트워크 리소스를 이 변동을 복제하지 않고 저장하고 싶습니다.

## <a name="what-are-the-typical-scenarios"></a>일반적인 시나리오는?
제외에 적합한 후보가 되는 데이터 변동의 구체적인 예를 확인할 수 있습니다. 페이징 파일(pagefile.sys)에 대한 쓰기 및 Microsoft SQL Server의 tempdb 파일에 대한 쓰기가 예에 포함될 수 있습니다. 워크로드 및 저장소 하위 시스템에 따라 페이징 파일이 상당한 양의 변동을 등록할 수 있습니다. 그러나 이 데이터를 기본 사이트에서 Azure로 복제하면 리소스가 많이 소모됩니다. 따라서 다음 단계를 사용하여 운영 체제와 페이징 파일이 모두 있는 단일 가상 디스크를 포함한 가상 머신의 복제를 최적화할 수 있습니다.

1. 단일 가상 디스크를 두 개의 가상 디스크로 분할합니다. 한 가상 디스크에는 운영 체제가 있고 다른 하나에는 페이징 파일이 있습니다.
2. 복제에서 페이징 파일 디스크를 제외합니다.

마찬가지로 Microsoft SQL Server tempdb 파일 및 시스템 데이터베이스 파일이 모두 있는 디스크를 최적화하기 위해 다음 단계를 사용할 수 있습니다.

1. 별도의 두 디스크에 tempdb와 시스템 데이터베이스를 유지합니다.
2. 복제에서 tempdb 디스크를 제외합니다.

## <a name="how-to-exclude-disks-from-replication"></a>복제에서 디스크를 제외하는 방법은?

[복제 사용](vmware-azure-enable-replication.md) 워크플로에 따라 Azure Site Recovery 포털에서 가상 머신을 보호합니다. 워크플로 네 번째 단계에서는 **복제할 디스크** 열을 사용하여 복제에서 디스크를 제외합니다. 기본적으로 모든 디스크가 복제하도록 선택됩니다. 복제에서 제외하려는 디스크의 확인란 선택을 취소하고 복제를 사용하도록 설정하는 단계를 완료합니다.

![복제에서 디스크를 제외하고 VMware에서 Azure 장애 복구에 대한 복제를 사용하도록 설정](./media/vmware-azure-exclude-disk/enable-replication-exclude-disk1.png)


>[!NOTE]
>
> * 이미 모바일 서비스가 설치된 VM에서만 디스크를 제외할 수 있습니다. 모바일 서비스는 복제를 사용하도록 설정된 후 푸시 메커니즘만 사용하여 설치되기 때문에 모바일 서비스를 수동으로 설치해야 합니다.
> * 기본 디스크만 복제에서 제외할 수 있습니다. 운영 체제 또는 동적 디스크를 제외할 수 없습니다.
> * 복제를 사용하도록 설정한 후 복제에 대해 디스크를 추가 또는 제거할 수 없습니다. 디스크를 추가하거나 제외하려는 경우 컴퓨터에 대한 보호를 해제한 다음 다시 사용하도록 설정해야 합니다.
> * 애플리케이션 작동에 필요한 디스크를 제외하면 Azure로 장애 조치(failover) 후 복제된 애플리케이션이 실행할 수 있도록 디스크를 Azure에서 수동으로 만들어야 합니다. 또는 Azure Automation을 복구 계획에 통합하여 컴퓨터의 장애 조치(failover) 동안 디스크를 만들 수 있습니다.
> * Window 가상 머신: Azure에서 수동으로 만드는 디스크는 장애 복구되지 않습니다. 예를 들어 디스크 세 개를 장애 조치하고 Azure Virtual Machines에서 디스크 두 개를 직접 만들면 장애 조치된 디스크 세 개만 장애 복구됩니다. 온-프레미스에서 Azure로 장애 복구 또는 다시 보호에서 수동으로 만든 디스크는 포함할 수 없습니다.
> * Linux 가상 머신: Azure에서 수동으로 만드는 디스크는 장애 복구되지 않습니다. 예를 들어 디스크 세 개를 장애 조치(failover)하고 Azure Virtual Machines에서 디스크 두 개를 직접 만들면 다섯 개 모두 장애 복구됩니다. 장애 복구에서 수동으로 만든 디스크는 제외할 수 없습니다.
>


## <a name="end-to-end-scenarios-of-exclude-disks"></a>디스크 제외의 종단 간 시나리오
디스크 제외 기능의 이해를 위해 두 가지 시나리오를 살펴보겠습니다.

- SQL Server tempdb 디스크
- 페이징 파일(pagefile.sys) 디스크

## <a name="example-1-exclude-the-sql-server-tempdb-disk"></a>예제 1: SQL Server tempdb 디스크 제외
제외할 수 있는 tempdb가 있는 SQL Server 가상 머신을 살펴보겠습니다.

가상 디스크의 이름은 SalesDB입니다.

원본 가상 머신의 디스크는 다음과 같습니다.


**디스크 이름** | **게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 운영 체제 디스크
DB-Disk1| Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1
DB-Disk2(보호에서 디스크 제외됨) | Disk2 | E:\ | 임시 파일
DB-Disk3(보호에서 디스크 제외됨) | Disk3 | F:\ | SQL tempdb 데이터베이스(폴더 경로(F:\MSSQL\Data\)) <br /> <br />장애 조치 전에 폴더 경로 적어둡니다.
DB-Disk4 | Disk4 |G:\ |사용자 데이터베이스 2

가상 머신의 디스크 두 개에 나타난 데이터 변동은 일시적이므로 SalesDB 가상 머신을 보호하는 동안 Disk2 및 Disk3을 복제에서 제외합니다. Azure Site Recovery는 해당 디스크를 복제하지 않습니다. 장애 조치(failover) 시 해당 디스크는 Azure에서 장애 조치(failover) 가상 머신에 표시되지 않습니다.

장애 조치(failover) 후 Azure Virtual Machine의 디스크는 다음과 같습니다.

**게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
DISK0 | C:\ | 운영 체제 디스크
Disk1 | E:\ | 임시 저장소<br /> <br />Azure는이 디스크를 추가 하 고 첫 번째 사용 가능한 드라이브 문자를 할당 합니다.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1
Disk3 | G:\ | 사용자 데이터베이스 2

Disk2와 Disk3은 SalesDB 가상 머신에서 제외되었으므로 E:가 사용 가능한 목록의 첫 번째 드라이브 문자입니다. Azure에서 E:를 임시 저장소 볼륨에 할당합니다. 복제된 모든 디스크의 경우 드라이브 문자는 동일하게 유지됩니다.

SQL tempdb 디스크였던(tempdb 폴더 경로 F:\MSSQL\Data\) Disk3은 복제에서 제외되었습니다. 디스크를 장애 조치(failover) 가상 머신에 사용할 수 없습니다. 결과적으로 SQL 서비스는 중지된 상태이며 F:\MSSQL\Data 경로가 필요합니다.

이 경로를 만드는 방법은 두 가지입니다.

- 새 디스크를 추가하고 tempdb 폴더 경로를 할당합니다.
- tempdb 폴더 경로에 기존 임시 저장소 디스크를 사용합니다.

### <a name="add-a-new-disk"></a>새 디스크 추가:

1. 장애 조치(failover) 전에 SQL tempdb.mdf 및 tempdb.ldf 경로를 적어둡니다.
2. Azure Portal에서 원본 SQL tempdb 디스크(Disk3)와 크기가 같거나 더 큰 장애 조치(failover) 가상 머신에 새 디스크를 추가합니다.
3. Azure Virtual Machine에 로그인합니다. 디스크 관리(diskmgmt.msc) 콘솔에서 새로 추가한 디스크를 초기화하고 포맷합니다.
4. SQL tempdb 디스크(F:)에서 사용한 것과 동일한 드라이브 문자를 할당합니다.
5. F: 볼륨(F:\MSSQL\Data)에 tempdb 폴더를 만듭니다.
6. 서비스 콘솔에서 SQL 서비스를 시작합니다.

### <a name="use-an-existing-temporary-storage-disk-for-the-sql-tempdb-folder-path"></a>SQL tempdb 폴더 경로에 기존 임시 저장소 디스크를 사용합니다.

1. 명령 프롬프트를 엽니다.
2. 명령 프롬프트에서 SQL Server를 복구 모드에서 실행합니다.

        Net start MSSQLSERVER /f / T3608

3. 다음 sqlcmd를 실행하여 tempdb 경로를 새 경로로 바꿉니다.

        sqlcmd -A -S SalesDB        **Use your SQL DBname**
        USE master;     
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = tempdev, FILENAME = 'E:\MSSQL\tempdata\tempdb.mdf');
        GO      
        ALTER DATABASE tempdb       
        MODIFY FILE (NAME = templog, FILENAME = 'E:\MSSQL\tempdata\templog.ldf');       
        GO


4. Microsoft SQL Server 서비스를 중지합니다.

        Net stop MSSQLSERVER
5. Microsoft SQL Server 서비스를 시작합니다.

        Net start MSSQLSERVER

임시 저장소 디스크는 다음 Azure 지침을 참조하세요.

* [Azure VM에서 SSD를 사용하여 SQL Server TempDB 및 버퍼 풀 확장 저장](https://blogs.technet.microsoft.com/dataplatforminsider/2014/09/25/using-ssds-in-azure-vms-to-store-sql-server-tempdb-and-buffer-pool-extensions/)
* [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance)

## <a name="failback-from-azure-to-an-on-premises-host"></a>장애 복구(Azure에서 온-프레미스 호스트로)
이제 Azure에서 온-프레미스 VMware로 장애 조치를 수행할 때 복제되는 디스크에 대해 살펴보겠습니다. Azure에서 수동으로 만드는 디스크는 복제되지 않습니다. 예를 들어 디스크 세 개를 장애 조치하고 Azure Virtual Machines에서 두 개를 직접 만들면 장애 조치된 디스크 세 개만 장애 복구됩니다. 온-프레미스에서 Azure로 장애 복구 또는 다시 보호에서 수동으로 만든 디스크는 포함할 수 없습니다. 또한 임시 저장소 디스크도 온-프레미스 호스트로 복제하지 않습니다.

### <a name="failback-to-original-location-recovery"></a>원래 위치 복구로 장애 복구

이전 예제에서는 Azure Virtual Machine 디스크 구성은 다음과 같습니다.

**게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
DISK0 | C:\ | 운영 체제 디스크
Disk1 | E:\ | 임시 저장소<br /> <br />Azure는이 디스크를 추가 하 고 첫 번째 사용 가능한 드라이브 문자를 할당 합니다.
Disk2 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1
Disk3 | G:\ | 사용자 데이터베이스 2

원래 위치로 장애 복구가 수행되면 장애 복구 가상 머신 디스크 구성에는 제외된 디스크가 없습니다. VMware에서 Azure에서 제외된 디스크는 장애 복구 가상 머신에서 사용할 수 없습니다.

Azure에서 온-프레미스 VMware로 계획된 장애 조치(failover) 후 VMWare 가상 컴퓨터(원래 위치)의 디스크는 다음과 같습니다.

**게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | ---
DISK0 | C:\ | 운영 체제 디스크
Disk1 | D:\ | SQL 시스템 데이터베이스 및 사용자 데이터베이스 1
Disk2 | G:\ | 사용자 데이터베이스 2

## <a name="example-2-exclude-the-paging-file-pagefilesys-disk"></a>예 2: 페이징 파일(pagefile.sys) 디스크 제외

제외할 수 있는 페이징 파일 디스크가 있는 가상 머신을 살펴보겠습니다.
다음 두 가지 경우가 있습니다.

### <a name="case-1-the-paging-file-is-configured-on-the-d-drive"></a>사례 1: 페이징 파일이 D: 드라이브에 구성됨
디스크 구성은 다음과 같습니다.

**디스크 이름** | **게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 운영 체제 디스크
DB-Disk1(보호에서 제외된 디스크) | Disk1 | D:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

원본 가상 머신의 페이징 파일 설정은 다음과 같습니다.

![원본 가상 컴퓨터의 페이징 파일 설정](./media/vmware-azure-exclude-disk/pagefile-on-d-drive-sourcevm.png)


VMware에서 Azure에서 가상 머신의 장애 조치 후 Azure Virtual Machine의 디스크는 다음과 같습니다.

**디스크 이름** | **게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 운영 체제 디스크
DB-Disk1 | Disk1 | D:\ | 임시 저장소<br /> <br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

Disk1(D:)은 제외되었으므로 D:가 사용 가능한 목록에서 첫 번째 드라이브 문자입니다. Azure에서 D:를 임시 저장소 볼륨에 할당합니다. D:는 Azure Virtual Machine에서 사용할 수 있으므로 가상 머신의 페이징 파일 설정이 동일하게 유지됩니다.

Azure Virtual Machine의 페이징 파일 설정은 다음과 같습니다.

![Azure Virtual Machine의 페이징 파일 설정](./media/vmware-azure-exclude-disk/pagefile-on-azure-vm-after-failover.png)

### <a name="case-2-the-paging-file-is-configured-on-another-drive-other-than-d-drive"></a>사례 2: 페이징 파일이 다른 드라이브(D: 드라이브 이외)에 구성됨

원본 가상 머신 디스크 구성은 다음과 같습니다.

**디스크 이름** | **게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | DISK0 | C:\ | 운영 체제 디스크
DB-Disk1(보호에서 디스크 제외됨) | Disk1 | G:\ | pagefile.sys
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

온-프레미스 가상 머신의 페이징 파일 설정은 다음과 같습니다.

![온-프레미스 가상 머신의 페이징 파일 설정](./media/vmware-azure-exclude-disk/pagefile-on-g-drive-sourcevm.png)

VMware에서 Azure에서 가상 머신의 장애 조치 후 Azure Virtual Machine의 디스크는 다음과 같습니다.

**디스크 이름** | **게스트 운영 체제 디스크#** | **드라이브 문자** | **디스크 데이터 형식**
--- | --- | --- | ---
DB-Disk0-OS | DISK0  |C:\ |운영 체제 디스크
DB-Disk1 | Disk1 | D:\ | 임시 저장소<br /> <br />pagefile.sys
DB-Disk2 | Disk2 | E:\ | 사용자 데이터 1
DB-Disk3 | Disk3 | F:\ | 사용자 데이터 2

D:가 목록에서 사용 가능한 첫 번째 드라이브 문자이므로 Azure에서 D:를 임시 저장소 볼륨에 할당합니다. 복제된 모든 디스크의 경우 드라이브 문자는 동일하게 유지됩니다. G: 디스크를 사용할 수 없으므로 시스템에서 C: 드라이브를 페이징 파일에 사용합니다.

Azure Virtual Machine의 페이징 파일 설정은 다음과 같습니다.

![Azure Virtual Machine의 페이징 파일 설정](./media/vmware-azure-exclude-disk/pagefile-on-azure-vm-after-failover-2.png)

## <a name="next-steps"></a>다음 단계
배포가 설정되고 실행된 후에는 다양한 장애 조치(Failover)에 대해 [자세히 알아보세요](site-recovery-failover.md).
