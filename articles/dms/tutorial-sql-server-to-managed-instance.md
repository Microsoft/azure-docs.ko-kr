---
title: '자습서: SQL 관리 되는 인스턴스로 SQL Server 마이그레이션'
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용하여 SQL Server 온-프레미스에서 Azure SQL Database 관리형 인스턴스로 마이그레이션하는 방법에 대해 알아봅니다.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: d8e5b531684e175e5b9423bbc302bbe0b3d36058
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2020
ms.locfileid: "75745278"
---
# <a name="tutorial-migrate-sql-server-to-an-azure-sql-database-managed-instance-offline-using-dms"></a>자습서: DMS를 사용 하 여 오프 라인 Azure SQL Database 관리 되는 인스턴스로 SQL Server 마이그레이션

Azure Database Migration Service를 사용하여 온-프레미스 SQL Server 인스턴스에서 [Azure SQL Database 관리형 인스턴스](../sql-database/sql-database-managed-instance.md)로 마이그레이션할 수 있습니다. 수동 작업이 필요한 추가적인 방법은 [SQL Server 인스턴스를 Azure SQL Database 관리형 인스턴스로 마이그레이션](../sql-database/sql-database-managed-instance-migrate.md) 문서를 참조하세요.

이 자습서에서는 Azure Database Migration Service를 사용하여 **Adventureworks2012** 데이터베이스를 SQL Server의 온-프레미스 인스턴스에서 SQL Database 관리형 인스턴스로 마이그레이션합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.
> [!div class="checklist"]
>
> - Azure Database Migration Service 인스턴스를 만듭니다.
> - Azure Database Migration Service를 사용하여 마이그레이션 프로젝트를 만듭니다.
> - 마이그레이션을 실행합니다.
> - 마이그레이션을 모니터링합니다.
> - 마이그레이션 보고서를 다운로드합니다.

> [!IMPORTANT]
> SQL Server에서 SQL Database 관리형 인스턴스로 오프라인 마이그레이션의 경우 Azure Database Migration Service에서 백업 파일을 만들 수 있습니다. 또는 서비스에서 데이터베이스를 마이그레이션하는 데 사용할 SMB 네트워크 공유에 최신 전체 데이터베이스 백업을 제공할 수 있습니다. 단일 백업 미디어에 여러 백업을 추가하지 마세요. 각 백업을 별도의 백업 파일에 만들어야 합니다. 압축된 백업도 사용하여 대량 백업 마이그레이션과 관련된 잠재적인 문제 발생 가능성을 줄일 수 있습니다.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

이 문서에서는 SQL Server에서 SQL Database 관리형 인스턴스로의 오프라인 마이그레이션을 설명합니다. 온라인 마이그레이션의 경우 [DMS를 사용하여 SQL Server를 Azure SQL Database 관리형 인스턴스 온라인으로 마이그레이션](tutorial-sql-server-managed-instance-online.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

- [Express](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 경로 또는 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)을 사용 하 여 온-프레미스 원본 서버에 대한 사이트 간 연결을 제공 하는 Azure Resource Manager 배포 모델을 사용 하 여 Azure Database Migration Service에 대한 Microsoft Azure Virtual Network를 만듭니다. [Azure Database Migration Service를 사용한 Azure SQL Database 관리형 인스턴스 마이그레이션에 대한 네트워크 토폴로지를 알아봅니다](https://aka.ms/dmsnetworkformi). 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Virtual Network 설명서](https://docs.microsoft.com/azure/virtual-network/)와 특히 단계별 정보를 포함 하는 빠른 시작 문서를 참조 하세요.

    > [!NOTE]
    > 가상 네트워크를 설정 하는 동안 Microsoft에 네트워크 피어 링을 사용 하는 Express 경로를 사용 하는 경우 서비스가 프로 비전 될 서브넷에 다음 서비스 [엔드포인트](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) 을 추가 합니다.
    > - 대상 데이터베이스 엔드포인트(예: SQL 엔드포인트, Cosmos DB 엔드포인트 등)
    > - 스토리지 엔드포인트
    > - Service Bus 엔드포인트
    >
    > Azure Database Migration Service에는 인터넷 연결이 없으므로 이 구성이 필요합니다.

- 가상 네트워크 네트워크 보안 그룹 규칙에서 Azure Database Migration Service에 대한 인바운드 통신 포트 (443, 53, 9354, 445, 12000)를 차단 하지 않는지 확인 합니다. Virtual network NSG 트래픽 필터링에 대한 자세한 내용은 [네트워크 보안 그룹을 사용 하 여 네트워크 트래픽 필터링](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)문서를 참조 하세요.
- [소스 데이터베이스 엔진 액세스를 위한 Windows 방화벽](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access)을 구성합니다.
- Azure Database Migration Service가 원본 SQL Server(기본적으로 1433 TCP 포트)에 액세스할 수 있도록 Windows 방화벽을 엽니다.
- 동적 포트를 사용하여 명명된 여러 SQL Server 인스턴스를 실행하는 경우 SQL Browser 서비스를 사용하도록 설정하고, 방화벽을 통해 1434 UDP 포트에 액세스하도록 허용하여 Azure Database Migration Service가 원본 서버에서 명명된 인스턴스에 연결할 수 있습니다.
- 원본 데이터베이스 앞에 방화벽 어플라이언스를 사용하는 경우, Azure Database Migration Service에서 마이그레이션을 위해 445 SMB 포트를 통해 파일뿐만 아니라 원본 데이터베이스에 액세스할 수 있도록 허용하는 방화벽 규칙을 추가해야 합니다.
- [Azure Portal에서 Azure SQL Database 관리형 인스턴스 만들기](https://aka.ms/sqldbmi) 문서의 세부 지침에 따라 Azure SQL Database 관리되는 인스턴스를 만듭니다.
- 원본 SQL Server와 대상 관리되는 인스턴스를 연결하는 데 사용되는 로그인이 sysadmin 서버 역할의 구성원인지 확인합니다.

    >[!NOTE]
    >기본적으로 Azure Database Migration Service는 SQL 로그인의 마이그레이션만 지원 합니다. 그러나 다음을 수행 하 여 Windows 로그인을 마이그레이션하는 기능을 사용 하도록 설정할 수 있습니다.
    >
    >- 대상 SQL Database 관리 되는 인스턴스에 AAD 읽기 액세스 권한이 있는지 확인 합니다 .이 액세스 권한은 **회사 관리자**또는 **전역 관리자**역할을 가진 사용자가 Azure Portal를 통해 구성할 수 있습니다.
    >- 구성 페이지에서 Azure Portal를 통해 설정 되는 Windows 사용자/그룹 로그인 마이그레이션을 사용 하도록 Azure Database Migration Service 인스턴스를 구성 합니다. 이 설정을 사용 하도록 설정한 후에는 서비스를 다시 시작 하 여 변경 내용을 적용 합니다.
    >
    > 서비스를 다시 시작한 후에는 마이그레이션에 사용할 수 있는 로그인 목록에 Windows 사용자/그룹 로그인이 표시 됩니다. 마이그레이션하는 모든 Windows 사용자/그룹 로그인에 대해 연결 된 도메인 이름을 입력 하 라는 메시지가 표시 됩니다. 서비스 사용자 계정 (도메인 이름 NT 권한이 있는 계정) 및 가상 사용자 계정 (도메인 이름이 NT 서비스인 계정 이름)은 지원 되지 않습니다.

- Azure Database Migration Service가 원본 데이터베이스를 백업하는 데 사용할 수 있는 네트워크 공유를 만듭니다.
- 원본 SQL Server 인스턴스를 실행 중인 서비스 계정에 본인이 만든 네트워크 공유에 대한 쓰기 권한이 있고, 원본 서버의 컴퓨터 계정에 동일한 공유에 대한 읽기/쓰기 액세스 권한이 있는지 확인합니다.
- 이전에 만든 네트워크 공유에 대한 전체 제어 권한을 갖고 있는 Windows 사용자(및 암호)를 메모해 둡니다. Azure Database Migration Service는 사용자 자격 증명을 가장 하 여 복원 작업을 위한 Azure Storage 컨테이너에 백업 파일을 업로드 합니다.
- [Storage Explorer를 사용하여 Azure Blob Storage 리소스 관리](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container) 문서의 단계를 사용하여 Blob 컨테이너를 만들고 해당 SAS URI를 검색합니다. SAS URI를 만드는 동안 정책 창에서 모든 권한(읽기, 쓰기, 삭제, 나열)을 선택해야 합니다. 이 세부 정보는 Azure SQL Database 관리형 인스턴스로 데이터베이스를 마이그레이션하는 데 사용되는 백업 파일을 업로드할 수 있도록 스토리지 계정 컨테이너에 대한 액세스 권한을 Azure Database Migration Service에 제공합니다.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Microsoft.DataMigration 리소스 공급자 등록

1. Azure Portal에 로그인하고, **모든 서비스**를 선택한 다음, **구독**을 선택합니다.

    ![포털 구독 표시](media/tutorial-sql-server-to-managed-instance/portal-select-subscriptions.png)

2. Azure Database Migration Service의 인스턴스를 만들 구독을 선택한 다음, **리소스 공급자**를 선택합니다.

    ![리소스 공급자 보기](media/tutorial-sql-server-to-managed-instance/portal-select-resource-provider.png)

3. 마이그레이션을 검색한 다음 **Microsoft.DataMigration**의 오른쪽에서 **등록**을 선택합니다.

    ![리소스 공급자 등록](media/tutorial-sql-server-to-managed-instance/portal-register-resource-provider.png)

## <a name="create-an-azure-database-migration-service-instance"></a>Azure Database Migration Service 인스턴스 만들기

1. Azure Portal에서 **+ 리소스 만들기**를 선택하고, **Azure Database Migration Service**를 검색한 다음, 드롭다운 목록에서 **Azure Database Migration Service**를 선택합니다.

    ![Azure Marketplace](media/tutorial-sql-server-to-managed-instance/portal-marketplace.png)

2. **Azure Database Migration Service** 화면에서 **만들기**를 선택합니다.

    ![Azure Database Migration Service 인스턴스 만들기](media/tutorial-sql-server-to-managed-instance/dms-create1.png)

3. **Migration Service 만들기** 화면에서 서비스, 구독, 신규 또는 기존 리소스 그룹의 이름을 지정합니다.

4. DMS 인스턴스를 만들려는 위치를 선택합니다.

5. 기존 가상 네트워크를 선택 하거나 새로 만듭니다.

    가상 네트워크는 원본 SQL Server 및 대상 Azure SQL Database 관리 되는 인스턴스에 대한 액세스 권한을 Azure Database Migration Service 제공 합니다.

    Azure Portal에서 가상 네트워크를 만드는 방법에 대한 자세한 내용은 [Azure Portal를 사용 하 여 가상 네트워크 만들기](https://aka.ms/DMSVnet)문서를 참조 하세요.

    자세한 내용은 [Azure Database Migration Service를 사용한 Azure SQL DB 관리형 인스턴스 마이그레이션에 대한 네트워크 토폴로지](https://aka.ms/dmsnetworkformi) 문서를 참조하세요.

6. 가격 책정 계층을 선택합니다.

    비용 및 가격 책정 계층에 대한 자세한 내용은 [가격 책정 페이지](https://aka.ms/dms-pricing)를 참조하세요.

    ![DMS 서비스 만들기](media/tutorial-sql-server-to-managed-instance/dms-create-service2.png)

7. **만들기**를 선택하여 서비스를 만듭니다.

## <a name="create-a-migration-project"></a>마이그레이션 프로젝트 만들기

서비스 인스턴스가 생성된 후 Azure Portal에서 서비스를 찾아 연 다음, 새로운 마이그레이션 프로젝트를 만듭니다.

1. Azure Portal에서 **모든 서비스**를 선택하고, Azure Database Migration Service를 검색하고 나서, **Azure Database Migration Services**를 선택합니다.

    ![Azure Database Migration Service의 모든 인스턴스 찾기](media/tutorial-sql-server-to-managed-instance/dms-search.png)

2. **Azure Database Migration Services** 화면에서 직접 만든 인스턴스의 이름을 검색한 다음, 인스턴스를 선택합니다.

3. **+ 새 마이그레이션 프로젝트**를 선택합니다.

4. **새 마이그레이션 프로젝트** 화면에서 프로젝트의 이름을 지정하고, **원본 서버 형식** 텍스트 상자에서 **SQL Server**를 선택하고, **대상 서버 형식** 텍스트 상자에서 **Azure SQL Database Managed Instance**를 선택한 다음, **작업 형식 선택**에서 **오프라인 데이터 마이그레이션**을 선택합니다.

   ![DMS 프로젝트 만들기](media/tutorial-sql-server-to-managed-instance/dms-create-project2.png)

5. **만들기**를 선택하여 프로젝트를 만듭니다.

## <a name="specify-source-details"></a>원본 세부 정보 지정

1. **마이그레이션 원본 세부 정보** 화면에서 원본 SQL Server에 대한 연결 세부 정보를 지정합니다.

2. 서버에 신뢰할 수 있는 인증서가 설치되지 않은 경우 **서버 인증서 신뢰** 확인란을 선택합니다.

    신뢰할 수 있는 인증서가 설치되어 있지 않으면 인스턴스가 시작될 때 SQL Server에서 자체 서명 인증서를 생성합니다. 이 인증서는 클라이언트 연결에 대한 자격 증명을 암호화하는 데 사용됩니다.

    > [!CAUTION]
    > 자체 서명 인증서를 사용하여 암호화된 SSL 연결은 강력한 보안을 제공하지 않습니다. 중간자 공격(man-in-the-middle)을 받기 쉽습니다. 프로덕션 환경이나 인터넷에 연결된 서버에서 자체 서명 인증서를 사용하는 SSL을 신뢰해서는 안 됩니다.

   ![원본 세부 정보](media/tutorial-sql-server-to-managed-instance/dms-source-details1.png)

3. **저장**을 선택합니다.

4. **원본 데이터베이스 선택** 화면에서 마이그레이션할 **Adventureworks2012** 데이터베이스를 선택합니다.

   ![원본 데이터베이스 선택](media/tutorial-sql-server-to-managed-instance/dms-source-database1.png)

    > [!IMPORTANT]
    > SSIS(SQL Server Integration Services)를 사용하는 경우 DMS는 현재 SSIS 프로젝트/패키지의 카탈로그 데이터베이스(SSISDB)를 SQL Server에서 Azure SQL Database 관리형 인스턴스로 마이그레이션하는 기능을 지원하지 않습니다. 하지만 ADF(Azure Data Factory)에 SSIS를 프로비저닝하고 Azure SQL Database 관리형 인스턴스에서 호스팅하는 대상 SSISDB에 SSIS 프로젝트/패키지를 재배포할 수 있습니다. SSIS 패키지 마이그레이션에 대한 자세한 내용은 [SQL Server Integration Services 패키지를 Azure로 마이그레이션](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages) 문서를 참조하세요.

5. **저장**을 선택합니다.

## <a name="specify-target-details"></a>대상 세부 정보 지정

1. **마이그레이션 대상 세부 정보** 화면에서 대상에 대한 연결 세부 정보를 지정합니다. 이 대상은 **AdventureWorks2012** 데이터베이스를 마이그레이션할 미리 프로비저닝된 Azure SQL Database 관리형 인스턴스입니다.

    SQL Database 관리형 인스턴스를 아직 프로비저닝하지 않은 경우 인스턴스를 프로비저닝하는 데 도움이 되는 [링크](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-get-started)를 선택합니다. 프로젝트 생성은 계속 진행할 수 있으며, Azure SQL Database 관리형 인스턴스가 준비되면 이 특정 프로젝트로 돌아가서 마이그레이션을 실행합니다.

    ![대상 선택](media/tutorial-sql-server-to-managed-instance/dms-target-details2.png)

2. **저장**을 선택합니다.

## <a name="select-source-databases"></a>원본 데이터베이스 선택

1. **원본 데이터베이스 선택** 화면에서 마이그레이션할 원본 데이터베이스를 선택합니다.

    ![원본 데이터베이스 선택](media/tutorial-sql-server-to-managed-instance/select-source-databases.png)

2. **저장**을 선택합니다.

## <a name="select-logins"></a>로그인 선택

1. **로그인 선택** 화면에서 마이그레이션할 로그인을 선택합니다.

    >[!NOTE]
    >기본적으로 Azure Database Migration Service는 SQL 로그인의 마이그레이션만 지원 합니다. Windows 로그인 마이그레이션을 지원 하려면이 자습서의 **전제 조건** 섹션을 참조 하세요.

    ![로그인 선택](media/tutorial-sql-server-to-managed-instance/select-logins.png)

2. **저장**을 선택합니다.

## <a name="configure-migration-settings"></a>마이그레이션 설정 구성

1. **마이그레이션 설정 구성** 화면에서 다음 세부 정보를 입력합니다.

    | | |
    |--------|---------|
    |**원본 백업 옵션 선택** | DMS가 데이터베이스 마이그레이션에 사용할 수 있는 전체 백업 파일이 이미 있으면 **최신 백업 파일을 제공합니다** 옵션을 선택합니다. DMS에서 원본 데이터베이스 전체 백업을 처음으로 수행하고 마이그레이션에 이 백업을 사용하도록 하려면 **Azure Database Migration Service를 통해 백업 파일을 만들려고 합니다** 옵션을 선택합니다. |
    |**네트워크 위치 공유** | Azure Database Migration Service가 원본 데이터베이스를 백업하는 데 사용할 수 있는 로컬 SMB 네트워크 공유입니다. 원본 SQL Server 인스턴스를 실행하는 서비스 계정에는 이 네트워크 공유에 대한 쓰기 권한이 있어야 합니다. 네트워크 공유에 있는 서버의 FQDN 또는 IP 주소를 입력합니다(예: '\\\servername.domainname.com\backupfolder' 또는 '\\\IP address\backupfolder').|
    |**사용자 이름** | Windows 사용자가 위에서 입력한 네트워크 공유에 대한 전체 제어 권한을 갖고 있는지 확인합니다. Azure Database Migration Service는 사용자 자격 증명을 가장 하 여 복원 작업을 위한 Azure Storage 컨테이너에 백업 파일을 업로드 합니다. TDE 가능 데이터베이스를 마이그레이션하기로 선택하는 경우 인증서 파일을 업로드하고 삭제할 수 있도록 위의 Windows 사용자가 기본 제공 관리자 계정이어야 하고, Azure Database Migration Service에 [사용자 계정 컨트롤](https://docs.microsoft.com/windows/security/identity-protection/user-account-control/user-account-control-overview)을 사용하지 않도록 설정해야 합니다. |
    |**암호** | 사용자에 대한 암호입니다. |
    |**스토리지 계정 설정** | 서비스에서 백업 파일을 업로드 하 고 Azure SQL Database 관리 되는 인스턴스로 데이터베이스를 마이그레이션하는 데 사용 되는 저장소 계정 컨테이너에 대한 액세스를 Azure Database Migration Service 제공 하는 SAS URI입니다. [Blob 컨테이너에 대한 SAS URI를 가져오는 방법을 알아봅니다](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs#get-the-sas-for-a-blob-container).|
    |**TDE 설정** | TDE(투명한 데이터 암호화)가 설정된 원본 데이터베이스를 마이그레이션하려면 대상 Azure SQL Database 관리형 인스턴스에 대한 쓰기 권한이 필요합니다.  드롭다운 메뉴에서 Azure SQL Database 관리형 인스턴스가 프로비저닝되는 구독을 선택합니다.  드롭다운 메뉴에서 대상 **Azure SQL Database Managed Instance**를 선택합니다. |

    ![마이그레이션 설정 구성](media/tutorial-sql-server-to-managed-instance/dms-configure-migration-settings3.png)

2. **저장**을 선택합니다.

## <a name="review-the-migration-summary"></a>마이그레이션 요약 검토

1. **마이그레이션 요약** 화면의 **작업 이름** 텍스트 상자에서 마이그레이션 작업의 이름을 지정합니다.

2. **유효성 검사 옵션** 섹션을 확장하여 **유효성 검사 옵션 선택** 화면을 표시하고, 마이그레이션된 데이터베이스의 쿼리 정확도를 검사할지 여부를 지정한 다음, **저장**을 선택합니다.

3. 마이그레이션 프로젝트와 연결된 세부 정보를 검토하고 확인합니다.

    ![마이그레이션 프로젝트 요약](media/tutorial-sql-server-to-managed-instance/dms-project-summary2.png)

4. **저장**을 선택합니다.

## <a name="run-the-migration"></a>마이그레이션 실행

- **마이그레이션 실행**을 선택합니다.

  마이그레이션 작업 창이 나타나고, 작업 상태는 **보류 중**입니다.

## <a name="monitor-the-migration"></a>마이그레이션 모니터링

1. 마이그레이션 작업 화면에서 **새로 고침**을 선택하여 화면을 업데이트합니다.

   ![진행 중인 마이그레이션 작업](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration1.png)

    데이터베이스 및 로그인 범주를 더 확장하여 각 서버 개체의 마이그레이션 상태를 모니터링할 수 있습니다.

   ![진행 중인 마이그레이션 작업](media/tutorial-sql-server-to-managed-instance/dms-monitor-migration-extend.png)

2. 마이그레이션이 완료되면 **보고서 다운로드**를 선택하여 마이그레이션 프로세스와 관련된 세부 정보가 나열된 보고서를 받습니다.

3. 대상 Azure SQL Database 관리형 인스턴스 환경에서 대상 데이터베이스를 확인합니다.

## <a name="next-steps"></a>다음 단계

- T-SQL RESTORE 명령을 사용하여 관리되는 인스턴스로 데이터베이스를 마이그레이션하는 방법을 보여주는 자습서는 [복원 명령을 사용하여 백업을 관리되는 인스턴스에 복원](../sql-database/sql-database-managed-instance-restore-from-backup-tutorial.md)을 참조하세요.
- 관리되는 인스턴스에 대한 자세한 내용은 [관리되는 인스턴스란?](../sql-database/sql-database-managed-instance.md)을 참조하세요.
- 관리되는 인스턴스에 앱을 연결하는 방법은 [애플리케이션 연결](../sql-database/sql-database-managed-instance-connect-app.md)을 참조하세요.
