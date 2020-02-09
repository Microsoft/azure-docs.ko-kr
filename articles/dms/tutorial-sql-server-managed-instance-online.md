---
title: '자습서: 온라인으로 SQL Server SQL 관리 되는 인스턴스로 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 SQL Server 온-프레미스에서 Azure SQL Database 관리형 인스턴스로 온라인 마이그레이션하는 방법을 알아봅니다.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/10/2020
ms.openlocfilehash: e9a24daeeab906419416a3a10fda901c91d9fb33
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863226"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-online-using-dms"></a>자습서: DMS를 사용 하 여 온라인으로 Azure SQL Database 관리 되는 인스턴스로 SQL Server 마이그레이션

Azure Database Migration Service를 사용하면 최소한의 가동 중지 시간으로 데이터베이스를 온-프레미스 SQL Server 인스턴스에서 [Azure SQL Database 관리형 인스턴스](../sql-database/sql-database-managed-instance.md)로 마이그레이션할 수 있습니다. 수동 작업이 필요한 추가적인 방법은 [SQL Server 인스턴스를 Azure SQL Database 관리형 인스턴스로 마이그레이션](../sql-database/sql-database-managed-instance-migrate.md) 문서를 참조하세요.

이 자습서에서는 Azure Database Migration Service를 사용하여 최소한의 가동 중지 시간으로 **Adventureworks2012** 데이터베이스를 SQL Server의 온-프레미스 인스턴스에서 SQL Database 관리형 인스턴스로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> * Azure Database Migration Service 인스턴스를 만듭니다.
> * Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만들고 온라인 마이그레이션을 시작합니다.
> * 마이그레이션을 모니터링합니다.
> * 준비가 되면 마이그레이션 컷오버를 수행합니다.

> [!IMPORTANT]
> Azure Database Migration Service를 사용하여 SQL Server에서 SQL Database 관리형 인스턴스로 온라인 마이그레이션하려면 서버에서 데이터베이스를 마이그레이션하는 데 사용할 수 있는 SMB 네트워크의 전체 데이터베이스 백업 및 후속 로그 백업을 제공해야 합니다. Azure Database Migration Service는 백업을 새로 시작하는 것이 아니라 사용자가 재해 복구 계획의 일부로 보유한 기존 백업을 마이그레이션에 사용합니다.
> [WITH CHECKSUM 옵션을 사용하여 백업](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)을 만들어야 합니다. 또한 여러 백업(즉, 전체 및 t-로그)을 단일 백업 미디어에 추가하면 안 됩니다. 각 백업을 별도의 백업 파일에 만들어야 합니다. 마지막으로, 압축된 백업을 사용하여 대량 백업 마이그레이션과 관련된 잠재적인 문제 발생 가능성을 줄일 수 있습니다.

> [!NOTE]
> Azure Database Migration Service를 사용하여 온라인 마이그레이션을 수행하려면 프리미엄 가격 책정 계층에 따라 인스턴스를 만들어야 합니다.

> [!IMPORTANT]
> 최적의 마이그레이션 환경을 위해 Microsoft는 대상 데이터베이스와 동일한 Azure 지역에서 Azure Database Migration Service의 인스턴스를 만드는 것을 권장합니다. 영역 또는 지역 간에 데이터를 이동하면 마이그레이션 프로세스 속도가 저하되고 오류가 발생할 수 있습니다.

> [!IMPORTANT]
> 인스턴스 재구성 또는 계획 된 유지 관리로 인 한 중단 위험을 최소화 하기 위해 가능한 한 온라인 마이그레이션 프로세스의 기간을 줄입니다. 이러한 이벤트의 경우 처음부터 마이그레이션 프로세스가 시작 됩니다. 계획 된 유지 관리의 경우 마이그레이션 프로세스를 다시 시작 하기 전에 36 시간 유예 기간이 있습니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 SQL Server에서 SQL Database 관리형 인스턴스로의 온라인 마이그레이션을 설명합니다. 오프라인 마이그레이션의 경우 [DMS를 사용하여 SQL Server를 SQL Database 관리형 인스턴스로 오프라인 마이그레이션](tutorial-sql-server-to-managed-instance.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 대한 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대한 Microsoft Azure Virtual Network를 만듭니다. [Azure Database Migration Service를 사용한 Azure SQL Database 관리형 인스턴스 마이그레이션에 대한 네트워크 토폴로지를 알아봅니다](https://aka.ms/dmsnetworkformi). 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/)와 특히 단계별 정보를 포함 하는 빠른 시작 문서를 참조 하세요.

    > [!NOTE]
    > 가상 네트워크를 설정 하는 동안 Microsoft에 네트워크 피어 링을 사용 하는 Express 경로를 사용 하는 경우 서비스가 프로 비전 될 서브넷에 다음 서비스 [엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) 을 추가 합니다.
    >
    > * 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > * 스토리지 엔드포인트
    > * Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.
    >
    >온-프레미스 네트워크와 Azure 사이에 사이트 간 연결이 없거나 사이트 간 연결 대역폭이 제한된 경우 하이브리드 모드(미리 보기)에서 Azure Database Migration Service를 사용하는 것이 좋습니다. 하이브리드 모드는 클라우드에서 실행 중인 Azure Database Migration Service 인스턴스와 함께 온-프레미스 마이그레이션 작업자를 활용합니다. 하이브리드 모드에서 Azure Database Migration Service의 인스턴스를 만들려면 [Azure Portal을 사용하여 하이브리드 모드에서 Azure Database Migration Service 인스턴스 만들기](https://aka.ms/dms-hybrid-create) 문서를 참조하세요.

    > [!IMPORTANT]
    > 마이그레이션의 일부로 사용되는 스토리지 계정과 관련하여 다음 중 하나를 수행해야 합니다.
    > * 모든 네트워크에서 스토리지 계정에 액세스할 수 있도록 허용합니다.
    > * MI 서브넷에서 [서브넷 위임을](https://docs.microsoft.com/azure/virtual-network/manage-subnet-delegation) 켜고이 서브넷을 허용 하도록 저장소 계정 방화벽 규칙을 업데이트 합니다.

* 가상 네트워크 네트워크 보안 그룹 규칙에서 Azure Database Migration Service에 대한 인바운드 통신 포트 (443, 53, 9354, 445, 12000)를 차단 하지 않는지 확인 합니다. Virtual network NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)문서를 참조 하세요.
* [소스 데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
* Azure Database Migration Service가 원본 SQL Server(기본적으로 1433 TCP 포트)에 액세스할 수 있도록 Windows 방화벽을 엽니다.
* 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우 SQL Browser 서비스를 사용하도록 설정하고, 방화벽을 통해 1434 UDP 포트에 액세스하도록 허용하여 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있습니다.
* 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service에서 마이그레이션을 위해 445 SMB 포트를 통해 파일뿐만 아니라 원본 데이터베이스에 액세스할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.
* [Azure Portal에서 Azure SQL Database 관리형 인스턴스 만들기](https://aka.ms/sqldbmi) 문서의 세부 지침에 따라 SQL Database 관리형 인스턴스를 만듭니다.
* 원본 SQL Server와 대상 관리형 인스턴스를 연결하는 데 사용되는 로그인이 sysadmin 서버 역할의 구성원인지 확인합니다.
* Azure Database Migration Service가 데이터베이스 마이그레이션에 사용할 수 있는 모든 데이터베이스 전체 데이터베이스 백업 파일과 후속 트랜잭션 로그 백업 파일이 포함된 SMB 네트워크 공유를 제공합니다.
* 원본 SQL Server 인스턴스를 실행 중인 서비스 계정에 본인이 만든 네트워크 공유에 대한 쓰기 권한이 있고, 원본 서버의 컴퓨터 계정에 동일한 공유에 대한 읽기/쓰기 액세스 권한이 있는지 확인합니다.
* 이전에 만든 네트워크 공유에 대한 전체 제어 권한을 갖고 있는 Windows 사용자(및 암호)를 메모해 둡니다. Azure Database Migration Service는 사용자 자격 증명을 가장 하 여 복원 작업을 위한 Azure Storage 컨테이너에 백업 파일을 업로드 합니다.
* Azure Database Migration Service가 대상 Azure Database 관리형 인스턴스 및 Azure Storage 컨테이너에 연결하는 데 사용할 수 있는 애플리케이션 ID 키를 생성하는 Azure Active Directory 애플리케이션 ID를 만듭니다. 자세한 내용은 [포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 애플리케이션 및 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)를 참조하세요.

  > [!NOTE]
  > Azure Database Migration Service를 사용하려면 지정된 애플리케이션 ID용 구독에 대한 기여자 권한이 필요합니다. 또는 Azure Database Migration Service에 필요한 특정 권한을 부여하는 사용자 지정 역할을 만들 수 있습니다. 사용자 지정 역할을 사용하는 방법에 대한 단계별 지침은 [SQL Server에서 SQL Database 관리형 인스턴스로 온라인 마이그레이션하기 위한 사용자 지정 역할](https://docs.microsoft.com/azure/dms/resource-custom-roles-sql-db-managed-instance) 문서를 참조하세요.

* DMS 서비스가 데이터베이스 백업 파일을 업로드하여 데이터베이스를 마이그레이션하는 데 사용할 수 있는 **표준 성능 계층**, Azure Storage 계정을 만들거나 기록합니다.  Azure Database Migration Service 인스턴스와 동일한 지역에 Azure Storage 계정을 만들어야 합니다.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

    ![포털 구독 표시](media/tutorial-sql-server-to-managed-instance-online/portal-select-subscriptions.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-sql-server-to-managed-instance-online/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/tutorial-sql-server-to-managed-instance-online/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, **Azure Database Migration Service**를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

     ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance-online/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-sql-server-to-managed-instance-online/dms-create1.png)

3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. DMS 인스턴스를 만들려는 위치를 선택합니다.

5. 기존 가상 네트워크를 선택 하거나 새로 만듭니다.

    가상 네트워크는 원본 SQL Server 및 대상 SQL Database 관리 되는 인스턴스에 대한 액세스 권한을 Azure Database Migration Service 제공 합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조 하세요.

    자세한 내용은 [Azure Database Migration Service를 사용한 Azure SQL Database 관리형 인스턴스 마이그레이션에 대한 네트워크 토폴로지](https://aka.ms/dmsnetworkformi) 문서를 참조하세요.

6. 프리미엄 가격 책정 계층에서 SKU를 선택합니다.

    > [!NOTE]
    > 온라인 마이그레이션은 프리미엄 계층을 사용하는 경우에만 지원됩니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![DMS 서비스 만들기](media/tutorial-sql-server-to-managed-instance-online/dms-create-service3.png)

7. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스 인스턴스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-sql-server-to-managed-instance-online/dms-search.png)

2. **Azure Database Migration Services** 화면에서 직접 만든 인스턴스의 이름을 검색한 다음, 인스턴스를 선택합니다.

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.

4. **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정하고, **원본 서버 형식** 텍스트 상자에서 **SQL Server**를 선택하고, **대상 서버 형식** 텍스트 상자에서 **Azure SQL Database Managed Instance**를 선택한 다음, **작업 형식 선택**에서 **온라인 데이터 마이그레이션**을 선택합니다.

   ![Azure Database Migration Service 프로젝트 만들기](media/tutorial-sql-server-to-managed-instance-online/dms-create-project3.png)

5. **활동 만들기 및 실행**을 선택하여 프로젝트를 만듭니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **마이그레이션 원본 세부 정보** 화면에서 원본 SQL Server에 대한 연결 세부 정보를 지정합니다.

2. 서버에 신뢰할 수 있는 인증서가 설치되지 않은 경우 **서버 인증서 신뢰** 확인란을 선택합니다.

    신뢰할 수 있는 인증서가 설치되어 있지 않으면 인스턴스가 시작될 때 SQL Server에서 자체 서명 인증서를 생성합니다. 이 인증서는 클라이언트 연결에 대한 자격 증명을 암호화하는 데 사용됩니다.

    > [!CAUTION]
    > 자체 서명 인증서를 사용하여 암호화된 SSL 연결은 강력한 보안을 제공하지 않습니다. 중간자 공격(man-in-the-middle)을 받기 쉽습니다. 프로덕션 환경이나 인터넷에 연결된 서버에서 자체 서명 인증서를 사용하는 SSL을 신뢰해서는 안 됩니다.

   ![원본 세부 정보](media/tutorial-sql-server-to-managed-instance-online/dms-source-details2.png)

3. **저장**을 선택합니다.

4. **원본 데이터베이스 선택** 화면에서 마이그레이션할 **Adventureworks2012** 데이터베이스를 선택합니다.

   ![원본 데이터베이스 선택](media/tutorial-sql-server-to-managed-instance-online/dms-source-database1.png)

    > [!IMPORTANT]
    > SSIS(SQL Server Integration Services)를 사용하는 경우 DMS는 현재 SSIS 프로젝트/패키지의 카탈로그 데이터베이스(SSISDB)를 SQL Server에서 Azure SQL Database 관리형 인스턴스로 마이그레이션하는 기능을 지원하지 않습니다. 하지만 ADF(Azure Data Factory)에 SSIS를 프로비저닝하고 Azure SQL Database 관리형 인스턴스에서 호스팅하는 대상 SSISDB에 SSIS 프로젝트/패키지를 재배포할 수 있습니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 문서를 참조하세요.

5. **저장**을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **마이그레이션 대상 세부 정보** 화면에서 DMS 인스턴스가 Azure SQL Database 관리형 인스턴스 및 Azure Storage Account의 대상 인스턴스에 연결하는 데 사용할 수 있는 **애플리케이션 ID**와 **키**를 지정합니다.

    자세한 내용은 [포털을 사용하여 리소스에 액세스할 수 있는 Azure Active Directory 애플리케이션 및 서비스 주체 만들기](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)를 참조하세요.

2. Azure SQL Database 관리형 인스턴스의 대상 인스턴스가 포함된 **구독**을 선택한 다음, 대상 인스턴스를 선택합니다.

    Azure SQL Database 관리형 인스턴스를 아직 프로비저닝하지 않은 경우 인스턴스를 프로비저닝하는 데 도움이 되는 [링크](https://aka.ms/SQLDBMI)를 선택합니다. Azure SQL Database 관리형 인스턴스의 인스턴스가 준비되면 이 프로젝트로 돌아가서 마이그레이션을 실행합니다.

3. **SQL 사용자**와 **암호**를 입력하여 Azure SQL Database 관리형 인스턴스의 대상 인스턴스에 연결합니다.

    ![대상 선택](media/tutorial-sql-server-to-managed-instance-online/dms-target-details3.png)

4. **저장**을 선택합니다.

## <a name="select-source-databases"></a>원본 데이터베이스 선택

1. **원본 데이터베이스 선택** 화면에서 마이그레이션할 원본 데이터베이스를 선택합니다.

    ![원본 데이터베이스 선택](media/tutorial-sql-server-to-managed-instance-online/dms-select-source-databases2.png)

2. **저장**을 선택합니다.

## <a name="configure-migration-settings"></a>마이그레이션 설정 구성

1. **마이그레이션 설정 구성** 화면에서 다음 세부 정보를 입력합니다.

    | | |
    |--------|---------|
    |**SMB 네트워크 위치 공유** | Azure Database Migration Service에서 마이그레이션에 사용할 수 있는 전체 데이터베이스 백업 파일과 트랜잭션 로그 백업 파일이 포함된 로컬 SMB 네트워크 공유 및 Azure 파일 공유입니다. 원본 SQL Server 인스턴스를 실행하는 서비스 계정에는 이 네트워크 공유에 대한 읽기/쓰기 권한이 있어야 합니다. 네트워크 공유에 있는 서버의 FQDN 또는 IP 주소를 입력합니다(예: '\\\servername.domainname.com\backupfolder' 또는 '\\\IP address\backupfolder'). 성능을 향상 시키려면 마이그레이션할 각 데이터베이스에 대해 별도의 폴더를 사용 하는 것이 좋습니다. **고급 설정** 옵션을 사용 하 여 데이터베이스 수준 파일 공유 경로를 제공할 수 있습니다. |
    |**사용자 이름** | Windows 사용자가 위에서 입력한 네트워크 공유에 대한 전체 제어 권한을 갖고 있는지 확인합니다. Azure Database Migration Service는 사용자 자격 증명을 가장 하 여 복원 작업을 위한 Azure Storage 컨테이너에 백업 파일을 업로드 합니다. Azure 파일 공유를 사용하는 경우 AZURE\가 앞에 나오는 스토리지 계정 이름을 사용자 이름으로 사용합니다. |
    |**암호** | 사용자에 대한 암호입니다. Azure 파일 공유를 사용하는 경우 스토리지 계정 키를 암호로 사용합니다. |
    |**Azure Storage 계정의 구독** | Azure Storage 계정이 포함된 구독을 선택합니다. |
    |**Azure Storage 계정** | DMS가 SMB 네트워크 공유에서 백업 파일을 업로드하고 데이터베이스 마이그레이션에 사용할 수 있는 Azure Storage 계정을 선택합니다.  최적의 파일 업로드 성능을 위해서는 DMS 서비스와 동일한 지역에서 스토리지 계정을 선택하는 것이 좋습니다. |

    ![마이그레이션 설정 구성](media/tutorial-sql-server-to-managed-instance-online/dms-configure-migration-settings4.png)

    > [!NOTE]
    > Azure Database Migration Service에 ' 시스템 오류 53 ' 또는 ' 시스템 오류 57 ' 오류가 표시 되 면 Azure Database Migration Service Azure 파일 공유에 액세스할 수 없게 됩니다. 이러한 오류 중 하나가 발생하면 [여기](https://docs.microsoft.com/azure/storage/common/storage-network-security?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)의 지침을 사용하여 가상 네트워크에서 스토리지 계정에 대한 액세스 권한을 부여합니다.

    > [!IMPORTANT]
    > 루프백 검사 기능을 사용 하 고 있고 원본 SQL Server와 파일 공유가 같은 컴퓨터에 있는 경우 원본에서 FQDN을 사용 하는 파일에 액세스할 수 없습니다. 이 문제를 해결 하려면 [여기](https://support.microsoft.com/help/926642/error-message-when-you-try-to-access-a-server-locally-by-using-its-fqd)에 설명 된 지침에 따라 루프백 검사 기능을 사용 하지 않도록 설정 합니다.

2. **저장**을 선택합니다.

## <a name="review-the-migration-summary"></a>마이그레이션 요약 검토

1. **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정합니다.

2. 마이그레이션 프로젝트와 연결된 세부 정보를 검토하고 확인합니다.

    ![마이그레이션 프로젝트 요약](media/tutorial-sql-server-to-managed-instance-online/dms-project-summary3.png)

## <a name="run-and-monitor-the-migration"></a>마이그레이션 실행 및 모니터링

1. **마이그레이션 실행**을 선택합니다.

2. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 화면을 업데이트합니다.

   ![진행 중인 마이그레이션 작업](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration2.png)

    데이터베이스 및 로그인 범주를 더 확장하여 각 서버 개체의 마이그레이션 상태를 모니터링할 수 있습니다.

   ![진행 중인 마이그레이션 작업](media/tutorial-sql-server-to-managed-instance-online/dms-monitor-migration-extend2.png)

## <a name="performing-migration-cutover"></a>마이그레이션 중단 수행

SQL Database 관리형 인스턴스의 대상 인스턴스에서 전체 데이터베이스 백업이 복원되면 마이그레이션 중단을 수행하는 데 데이터베이스를 사용할 수 있습니다.

1. 온라인 데이터베이스 마이그레이션을 완료할 준비가 되면 **중단 시작**을 선택합니다.

2. 원본 데이터베이스로 들어오는 모든 트래픽을 중지합니다.

3. [비상 로그 백업]을 가져와서 SMB 네트워크 공유에서 백업 파일을 사용할 수 있도록 만든 다음 최종 트랜잭션 로그 백업이 복원될 때까지 기다립니다.

    이 시점에서 **보류 중인 변경 내용**이 0으로 설정됩니다.

4. **확인**과 **적용**을 차례로 선택합니다.

    ![중단 완료 준비](media/tutorial-sql-server-to-managed-instance-online/dms-complete-cutover.png)

5. 데이터베이스 마이그레이션 상태가 **완료됨**으로 표시되면 애플리케이션을 Azure SQL Database 관리형 인스턴스의 새 대상 인스턴스에 연결합니다.

    ![중단 완료](media/tutorial-sql-server-to-managed-instance-online/dms-cutover-complete.png)

## <a name="next-steps"></a>다음 단계

* T-SQL RESTORE 명령을 사용하여 관리되는 인스턴스로 데이터베이스를 마이그레이션하는 방법을 보여주는 자습서는 [복원 명령을 사용하여 백업을 관리되는 인스턴스에 복원](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)을 참조하세요.
* 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란?](../sql-database/sql-database-managed-instance.md)을 참조하세요.
* 관리되는 인스턴스에 앱을 연결하는 방법은 [애플리케이션 연결](../sql-database/sql-database-managed-instance-connect-app.md)을 참조하세요.
