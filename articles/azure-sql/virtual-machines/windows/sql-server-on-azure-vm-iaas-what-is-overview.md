---
title: Azure Windows Virtual Machines에서 SQL Server 개요 | Microsoft Docs
description: 온-프레미스 하드웨어를 관리할 필요 없이 클라우드에서 Azure Virtual Machines에서 SQL Server의 전체 버전을 실행 하는 방법에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/27/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 4bf5b9b6f683783a39b7d34c430f1c6671c35885
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88236372"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Azure Virtual Machines에서 SQL Server (Windows)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)

[Azure Virtual Machines에서 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) 를 사용 하면 온-프레미스 하드웨어를 관리할 필요 없이 클라우드에서 전체 버전의 SQL Server를 사용할 수 있습니다. Vm (가상 머신)은 사용자가 지불 하는 경우에도 라이선스 비용이 간소화 됩니다. SQL Server

Azure 가상 머신은 전 세계 여러 [지리적 지역](https://azure.microsoft.com/regions/)에서 실행됩니다. 또한 다양한 [컴퓨터 크기](../../../virtual-machines/windows/sizes.md)가 제공됩니다. 가상 머신 이미지 갤러리를 통해 적합한 버전 운영 체제로 SQL Server VM을 만들 수 있습니다. 이렇게 하면 가상 컴퓨터에 다양 한 SQL Server 작업에 대 한 적절 한 옵션을 사용할 수 있습니다.

## <a name="automated-updates"></a>자동화된 업데이트

Azure Virtual Machines에서 SQL Server 자동 [패치](automated-patching.md) 를 사용 하 여 중요 한 windows를 설치 하 고 업데이트를 자동으로 SQL Server 하는 유지 관리 기간을 예약할 수 있습니다.

## <a name="automated-backups"></a>자동화된 백업

Azure Virtual Machines에서 SQL Server는 [자동화 된 백업을](automated-backup.md)활용 하 여 정기적으로 blob 저장소에 데이터베이스의 백업을 만들 수 있습니다. 수동으로 이 기술을 사용할 수 있습니다. 자세한 내용은 [SQL Server Backup 및 복원에 Azure Storage 사용](azure-storage-sql-server-backup-restore-use.md)을 참조하세요.

Azure는 Azure VM에서 실행 중인 SQL Server에 대한 엔터프라이즈 클래스 백업 솔루션도 제공합니다. 완전히 관리되는 백업 솔루션인 Azure는 Always On 가용성 그룹, 장기 보존, 지정 시간 복구, 중앙 관리 및 모니터링을 지원합니다. 자세한 내용은 [Azure vm의 SQL Server에 대 한 Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-sql-database)를 참조 하세요.
  

## <a name="high-availability"></a>고가용성

고가용성이 필요한 경우 SQL Server 가용성 그룹을 구성하는 것이 좋습니다. 여기에는 가상 네트워크의 Azure Virtual Machines에서 SQL Server의 여러 인스턴스가 포함 됩니다. 고가용성 솔루션을 수동으로 구성 하거나 자동 구성에 Azure Portal 템플릿을 사용할 수 있습니다. 모든 고가용성 옵션에 대 한 개요는 [Azure Virtual Machines에서 SQL Server에 대 한 고가용성 및 재해 복구](business-continuity-high-availability-disaster-recovery-hadr-overview.md)를 참조 하세요.

## <a name="performance"></a>성능

Azure 가상 머신은 다양한 워크로드 요구 사항을 충족하기 위해 다양한 컴퓨터 크기를 제공합니다. 또한 SQL Server Vm은 성능 요구 사항에 맞게 최적화 된 자동화 된 저장소 구성을 제공 합니다. SQL Server Vm에 대 한 저장소를 구성 하는 방법에 대 한 자세한 내용은 [SQL Server vm에 대 한 저장소 구성](storage-configuration.md)을 참조 하세요. 성능을 세밀 하 게 조정 하려면 [Azure Virtual Machines에서 SQL Server에 대 한 성능 모범 사례](performance-guidelines-best-practices.md)를 참조 하세요.

## <a name="get-started-with-sql-server-vms"></a>SQL Server Vm 시작

시작하려면 필요한 버전 및 운영 체제로 SQL Server 가상 머신 이미지를 선택합니다. 다음 섹션에서는 SQL Server 가상 머신 갤러리 이미지에 Azure Portal에 대한 직접 링크를 제공합니다.

> [!TIP]
> SQL Server 이미지에 대 한 가격 책정을 이해 하는 방법에 대 한 자세한 내용은 [Azure Virtual Machines의 SQL Server에 대 한 가격 책정 지침](pricing-guidance.md)을 참조 하세요. 

### <a name="pay-as-you-go"></a><a id="payasyougo"></a> 종량제
다음 테이블은 종량제 SQL Server 이미지에 대한 매트릭스를 제공합니다.

| 버전 | 운영 체제 | 버전 |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019enterprise), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019standard), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019web), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019sqldev) | 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

사용 가능한 SQL Server on Linux 가상 컴퓨터 이미지를 보려면 [Azure Virtual Machines (Linux)의 SQL Server 개요](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)를 참조 하세요.

> [!NOTE]
> 이제 사용자 사용량에 따라 요금이 부과되는 SQL Server VM의 라이선스 모델을 변경하여 고유의 라이선스를 사용할 수 있습니다. 자세한 내용은 [SQL Server VM의 라이선스 모델을 변경하는 방법](licensing-model-azure-hybrid-benefit-ahb-change.md)을 참조하세요. 

### <a name="bring-your-own-license"></a><a id="BYOL"></a> 사용자 라이선스 필요
사용자 라이선스가 필요할 수도 있습니다(BYOL). 이 시나리오에서는 SQL Server 라이선스에 대한 추가 비용 없이 VM에 대해서만 지불합니다.  사용자 고유의 라이선스를 가져오면 시간에 따른 지속되는 프로덕션 워크로드의 비용을 절약할 수 있습니다. 이 옵션을 사용하기 위한 요구 사항은 [SQL Server Azure VM에 대한 가격 책정 지침](pricing-guidance.md#byol)을 참조하세요.

사용자 고유의 라이선스를 가져오려면 기존 사용량 기준 SQL Server VM를 변환 하거나 접두사가 **{Byol}** 인 이미지를 배포할 수 있습니다. 사용량과 BYOL 간의 라이선스 모델 전환에 대 한 자세한 내용은 [SQL Server VM에 대 한 라이선스 모델을 변경 하는 방법](licensing-model-azure-hybrid-benefit-ahb-change.md)을 참조 하세요. 

| 버전 | 운영 체제 | 버전 |
| --- | --- | --- |
| **SQL Server 2019** | Windows Server 2019 | [Enterprise BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolenterprise), [Standard  BYOL](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ws2019-byolstandard)| 
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

PowerShell을 사용하여 Azure Portal에서 사용할 수 없는 SQL Server의 이전 이미지를 배포할 수 있습니다. PowerShell을 사용 하 여 사용 가능한 모든 이미지를 보려면 다음 명령을 사용 합니다.

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

PowerShell을 사용하여 SQL Server VM을 배포하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 SQL Server 가상 머신을 프로비전하는 방법](create-sql-vm-powershell.md)을 참조하세요.


### <a name="connect-to-the-vm"></a>VM에 연결
SQL Server VM을 만든 후에는 SSMS(SQL Server Management Studio)와 같은 도구나 애플리케이션에서 연결합니다. 지침은 [Azure에서 SQL Server 가상 머신에 연결](ways-to-connect-to-sql.md)을 참조 하세요.

### <a name="migrate-your-data"></a>데이터 마이그레이션
기존 데이터베이스가 있는 경우 새로 프로 비전 된 SQL Server VM로 이동 합니다. 마이그레이션 옵션 목록 및 지침은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](migrate-to-vm-from-sql-server.md)을 참조하세요.

## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure Portal을 사용하여 Azure SQL 리소스 만들기 및 관리

Azure Portal은 SQL 가상 머신을 포함하여 [모든 Azure SQL 리소스](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.Sql%2Fazuresql)를 관리할 수 있는 단일 페이지를 제공합니다.

**Azure SQL 리소스** 페이지에 액세스하려면 Azure Portal 메뉴에서 **Azure SQL**을 선택하거나, 아무 페이지에서나 **Azure SQL**를 검색하고 선택합니다.

![Azure SQL 검색](./media/sql-server-on-azure-vm-iaas-what-is-overview/search-for-azure-sql.png)

> [!NOTE]
> Azure SQL은 모든 Azure SQL 데이터베이스, 탄력적 풀, 논리 서버, 관리되는 인스턴스 및 가상 머신에 액세스할 수 있는 빠르고 쉬운 방법을 제공합니다. Azure SQL은 서비스 또는 리소스가 아닙니다. 

기존 리소스를 관리하려면 목록에서 원하는 항목을 선택합니다. 새 Azure SQL 리소스를 만들려면 **+ 추가**를 선택합니다. 

![Azure SQL 리소스 만들기](./media/sql-server-on-azure-vm-iaas-what-is-overview/create-azure-sql-resource.png)

**+ 추가**를 선택한 후에는 타일에서 **세부 정보 표시**를 선택하여 다양한 옵션에 대한 추가 정보를 확인합니다.

![데이터베이스 타일 세부 정보](./media/sql-server-on-azure-vm-iaas-what-is-overview/sql-vm-details.png)

자세한 내용은 다음을 참조하세요.

- [단일 데이터베이스 만들기](../../database/single-database-create-quickstart.md)
- [탄력적 풀 만들기](../../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [관리형 인스턴스 만들기](../../managed-instance/instance-create-quickstart.md)
- [SQL Server 가상 컴퓨터 만들기](sql-vm-create-portal-quickstart.md)

## <a name="sql-server-vm-image-refresh-policy"></a><a id="lifecycle"></a> SQL Server VM 이미지 새로 고침 정책
Azure는 지원되는 각 운영 체제, 버전 및 버전 조합에 대한 하나의 가상 머신 이미지만 유지 관리합니다. 즉, 시간이 지남에 따라 이미지를 새로 고치고 이전 이미지는 제거됩니다. 자세한 내용은 [SQL Server VM FAQ](frequently-asked-questions-faq.md#images)의 **이미지** 섹션을 참조하세요.

## <a name="customer-experience-improvement-program-ceip"></a>CEIP(사용자 환경 개선 프로그램)
CEIP(사용자 환경 개선 프로그램)를 사용하도록 기본 설정되어 있습니다. 보고서를 주기적으로 Microsoft에 전송하여 SQL Server를 개선하는 데 도움이 됩니다. 프로비전한 후에도 계속 사용하려면 CEIP에 필요한 관리 태스크는 없습니다. 원격 데스크톱을 사용하여 VM에 연결하여 CEIP를 사용자 지정하거나 사용하지 않도록 설정할 수 있습니다. 그런 다음 **SQL Server 오류 및 사용 보고** 유틸리티를 실행합니다. 보고를 해제하려면 지침을 따릅니다. 데이터 수집에 대한 자세한 내용은 [SQL Server 개인정보처리방침](https://docs.microsoft.com/sql/sql-server/sql-server-privacy)을 참조하세요.

## <a name="related-products-and-services"></a>관련 제품 및 서비스
### <a name="windows-virtual-machines"></a>Windows 가상 머신
* [Azure Virtual Machines 개요](../../../virtual-machines/windows/overview.md)

### <a name="storage"></a>스토리지
* [Microsoft Azure Storage 소개](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>네트워킹
* [Virtual Network 개요](../../../virtual-network/virtual-networks-overview.md)
* [Azure의 IP 주소](../../../virtual-network/public-ip-addresses.md)
* [Azure 포털에서 정규화된 도메인 이름 만들기](../../../virtual-machines/linux/portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server 설명서](https://docs.microsoft.com/sql/index)
* [Azure SQL Database 비교](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>다음 단계

Azure Virtual Machines에서 SQL Server 시작:

* [Azure Portal에서 SQL Server VM 만들기](sql-vm-create-portal-quickstart.md)

SQL Server VM에 대한 일반적인 질문에 대한 답변 구하기:

* [Azure Virtual Machines의 SQL Server FAQ](frequently-asked-questions-faq.md)

IaaS의 SQL Server에서 N 계층 애플리케이션을 실행하기 위한 참조 아키텍처 보기

* [SQL Server를 사용한 Azure의 Windows N 계층 애플리케이션](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/n-tier-sql-server)
* [여러 Azure 지역에서 N 계층 애플리케이션을 실행하여 고가용성 구현](https://docs.microsoft.com/azure/architecture/reference-architectures/n-tier/multi-region-sql-server)
