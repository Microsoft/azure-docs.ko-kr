---
title: Azure Windows Virtual Machines에서 SQL Server 개요 | Microsoft Docs
description: Azure Virtual Machines에서 SQL Server의 전체 버전을 실행하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: c505089e-6bbf-4d14-af0e-dd39a1872767
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 99c4f0f99af61196cf1a12f2f68a7d10d8b2e6c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61477164"
---
# <a name="what-is-sql-server-on-azure-virtual-machines-windows"></a>Azure Virtual Machines의 SQL Server란? (Windows)

> [!div class="op_single_selector"]
> * [Windows](virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](../../linux/sql/sql-server-linux-virtual-machines-overview.md)

[Azure 가상 머신의 SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/)를 사용하면 온-프레미스 하드웨어를 관리할 필요 없이 클라우드에서 SQL Server의 전체 버전을 사용할 수 있습니다. 또한 SQL Server VM을 사용하면 종량제로 지불하는 경우 라이선스 비용이 간소화됩니다.

Azure 가상 머신은 전 세계 여러 [지리적 지역](https://azure.microsoft.com/regions/)에서 실행됩니다. 또한 다양한 [컴퓨터 크기](../sizes.md)가 제공됩니다. 가상 머신 이미지 갤러리를 통해 적합한 버전 운영 체제로 SQL Server VM을 만들 수 있습니다. 따라서 가상 머신은 다양한 SQL Server 워크로드에 적합한 옵션입니다.

## <a name="automated-updates"></a>자동화된 업데이트

SQL Server Azure VM은 [자동화된 패치](virtual-machines-windows-sql-automated-patching.md)를 사용하여 중요한 Windows 및 SQL Server 업데이트를 자동으로 설치하는 유지 관리 기간을 예약할 수 있습니다.

## <a name="automated-backups"></a>자동화된 백업

SQL Server Azure VM은 정기적으로 Blob Storage에 데이터베이스의 백업을 만드는 [자동화된 Backup](virtual-machines-windows-sql-automated-backup-v2.md)을 활용할 수 있습니다. 수동으로 이 기술을 사용할 수 있습니다. 자세한 내용은 [SQL Server Backup 및 복원에 Azure Storage 사용](virtual-machines-windows-use-storage-sql-server-backup-restore.md)을 참조하세요.

## <a name="high-availability"></a>고가용성

고가용성이 필요한 경우 SQL Server 가용성 그룹을 구성하는 것이 좋습니다. 여기에는 가상 네트워크의 여러 SQL Server Azure VM이 포함됩니다. 고가용성 솔루션을 수동으로 구성하거나 자동 구성을 위해 Azure Portaldml 템플릿을 사용할 수 있습니다. 모든 고가용성 옵션에 대한 개요는 [Azure Virtual Machines의 SQL Server에 대한 고가용성 및 재해 복구](virtual-machines-windows-sql-high-availability-dr.md)를 참조하세요.

## <a name="performance"></a>성능

Azure 가상 머신은 다양한 워크로드 요구 사항을 충족하기 위해 다양한 컴퓨터 크기를 제공합니다. SQL VM은 성능 요구 사항에 최적화되어 자동화된 저장소 구성을 제공합니다. SQL VM용 저장소 구성에 대한 자세한 내용은 [SQL Server VM에 대한 저장소 구성](virtual-machines-windows-sql-server-storage-configuration.md)을 참조하세요. 성능을 세밀하게 조정하려면 [Azure Virtual Machines의 SQL Server에 대한 성능 모범 사례](virtual-machines-windows-sql-performance.md)를 참조하세요.

## <a name="get-started-with-sql-vms"></a>SQL VM 시작

시작하려면 필요한 버전 및 운영 체제로 SQL Server 가상 머신 이미지를 선택합니다. 다음 섹션에서는 SQL Server 가상 머신 갤러리 이미지에 Azure Portal에 대한 직접 링크를 제공합니다.

> [!TIP]
> SQL 이미지의 가격 책정을 이해하는 방법에 대한 자세한 내용은 [SQL Server Azure VM에 대한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md)을 참조하세요. 

### <a id="payasyougo"></a> 종량제
다음 테이블은 종량제 SQL Server 이미지에 대한 매트릭스를 제공합니다.

| Version | 운영 체제 | 버전 |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2EnterpriseWindowsServer2016), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2StandardWindowsServer2016), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016SP2WebWindowsServer2016), [Express](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2ExpressWindowsServer2016), [Developer](https://portal.azure.com/#create/Microsoft.FreeLicenseSQLServer2016SP2DeveloperWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP2ExpressWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP4ExpressWindowsServer2012R2) |
| **SQL Server 2008 R2 SP3** |Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2008R2) |

사용 가능한 Linux SQL Server 가상 머신 이미지를 보려면 [Azure Virtual Machines의 SQL Server 개요(Linux)](../../linux/sql/sql-server-linux-virtual-machines-overview.md)를 참조하세요.

> [!NOTE]
> 이제 사용자 사용량에 따라 요금이 부과되는 SQL Server VM의 라이선스 모델을 변경하여 고유의 라이선스를 사용할 수 있습니다. 자세한 내용은 [SQL VM의 라이선스 모델을 변경하는 방법](virtual-machines-windows-sql-ahb.md)을 참조하세요. 

### <a id="BYOL"></a> 사용자 라이선스 필요
사용자 라이선스가 필요할 수도 있습니다(BYOL). 이 시나리오에서는 SQL Server 라이선스에 대한 추가 비용 없이 VM에 대해서만 지불합니다.  사용자 고유의 라이선스를 가져오면 시간에 따른 지속되는 프로덕션 워크로드의 비용을 절약할 수 있습니다. 이 옵션을 사용하기 위한 요구 사항은 [SQL Server Azure VM에 대한 가격 책정 지침](virtual-machines-windows-sql-server-pricing-guidance.md#byol)을 참조하세요.

고유한 라이선스를 가져오려면 기존의 사용량에 따른 과금 방식의 SQL VM을 변환하거나 **{BYOL}** 접두사가 붙은 이미지를 배포하면 됩니다. 사용량에 따른 과금 방식과 BYOL 간에 라이선스 모델을 전환하는 방법에 대한 자세한 내용은 [SQL VM의 라이선스 모델을 변경하는 방법](virtual-machines-windows-sql-ahb.md)을 참조하세요. 

| Version | 운영 체제 | 버전 |
| --- | --- | --- |
| **SQL Server 2017** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2017StandardonWindowsServer2016) |
| **SQL Server 2016 SP2** |Windows Server 2016 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2EnterpriseWindowsServer2016), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016SP2StandardWindowsServer2016) |
| **SQL Server 2014 SP2** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2EnterpriseWindowsServer2012R2), [Standard BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP2StandardWindowsServer2012R2) |
| **SQL Server 2012 SP4** |Windows Server 2012 R2 |[Enterprise BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4EnterpriseWindowsServer2012R2), [Standard  BYOL](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP4StandardWindowsServer2012R2) |

PowerShell을 사용하여 Azure Portal에서 사용할 수 없는 SQL Server의 이전 이미지를 배포할 수 있습니다. Powershell을 사용하여 사용 가능한 모든 이미지를 보려면 다음 명령을 사용합니다.

  ```powershell
  Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
  ```

PowerShell을 사용하여 SQL Server VM을 배포하는 방법에 대한 자세한 내용은 [Azure PowerShell을 사용하여 SQL Server 가상 머신을 프로비전하는 방법](virtual-machines-windows-ps-sql-create.md)을 참조하세요.


### <a name="connect-to-the-vm"></a>VM에 연결
SQL Server VM을 만든 후에는 SSMS(SQL Server Management Studio)와 같은 도구나 애플리케이션에서 연결합니다. 지침은 [Azure에서 SQL Server 가상 머신 연결](virtual-machines-windows-sql-connect.md)을 참조하세요.

### <a name="migrate-your-data"></a>데이터 마이그레이션
기존 데이터베이스가 있는 경우 새로 프로비전된 SQL VM으로 이동할 수 있습니다. 마이그레이션 옵션 목록 및 지침은 [Azure VM에서 SQL Server로 데이터베이스 마이그레이션](virtual-machines-windows-migrate-sql.md)을 참조하세요.

## <a id="lifecycle"></a> SQL VM 이미지 새로 고침 정책
Azure는 지원되는 각 운영 체제, 버전 및 버전 조합에 대한 하나의 가상 머신 이미지만 유지 관리합니다. 즉, 시간이 지남에 따라 이미지를 새로 고치고 이전 이미지는 제거됩니다. 자세한 내용은 [SQL Server VM FAQ](virtual-machines-windows-sql-server-iaas-faq.md#images)의 **이미지** 섹션을 참조하세요.

## <a name="customer-experience-improvement-program-ceip"></a>CEIP(사용자 환경 개선 프로그램)
CEIP(사용자 환경 개선 프로그램)를 사용하도록 기본 설정되어 있습니다. 보고서를 주기적으로 Microsoft에 전송하여 SQL Server를 개선하는 데 도움이 됩니다. 프로비전한 후에도 계속 사용하려면 CEIP에 필요한 관리 태스크는 없습니다. 원격 데스크톱을 사용하여 VM에 연결하여 CEIP를 사용자 지정하거나 사용하지 않도록 설정할 수 있습니다. 그런 다음 **SQL Server 오류 및 사용 보고** 유틸리티를 실행합니다. 보고를 해제하려면 지침을 따릅니다. 데이터 수집에 대한 자세한 내용은 [SQL Server 개인정보처리방침](https://docs.microsoft.com/sql/getting-started/microsoft-sql-server-privacy-statement)을 참조하세요.

## <a name="related-products-and-services"></a>관련 제품 및 서비스
### <a name="windows-virtual-machines"></a>Windows 가상 머신
* [Virtual Machines 개요](../overview.md)

### <a name="storage"></a>Storage
* [Microsoft Azure Storage 소개](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>네트워킹
* [Virtual Network 개요](../../../virtual-network/virtual-networks-overview.md)
* [Azure의 IP 주소](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Azure 포털에서 정규화된 도메인 이름 만들기](../portal-create-fqdn.md)

### <a name="sql"></a>SQL
* [SQL Server 설명서](https://docs.microsoft.com/sql/index)
* [Azure SQL Database 비교](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>다음 단계

Azure 가상 머신에서 SQL Server 시작:

* [Azure Portal에서 SQL Server VM 만들기](quickstart-sql-vm-create-portal.md)

SQL VM에 대한 일반적인 질문에 대한 답변 구하기:

* [Azure Virtual Machines의 SQL Server FAQ](virtual-machines-windows-sql-server-iaas-faq.md)
