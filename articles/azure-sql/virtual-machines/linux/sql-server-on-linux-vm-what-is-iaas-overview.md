---
title: Linux 용 Azure Virtual Machines의 SQL Server 개요 | Microsoft Docs
description: Linux 용 Azure Virtual Machines에서 전체 SQL Server 버전을 실행 하는 방법에 대해 알아봅니다. 모든 Linux SQL Server VM 이미지 및 관련된 내용에 대한 직접 링크를 가져옵니다.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
tags: azure-service-management
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 41b74ed713485679576fdf7f4f0df54803b56caa
ms.sourcegitcommit: 9ce0350a74a3d32f4a9459b414616ca1401b415a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88192113"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Azure Virtual Machines에서 SQL Server 개요(Linux)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!div class="op_single_selector"]
> * [Windows](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)
> * [Linux](sql-server-on-linux-vm-what-is-iaas-overview.md)

Azure Virtual Machines에서 SQL Server를 사용 하면 온-프레미스 하드웨어를 관리할 필요 없이 클라우드에서 전체 버전의 SQL Server를 사용할 수 있습니다. 또한 SQL Server VM을 사용하면 종량제로 지불하는 경우 라이선스 비용이 간소화됩니다.

Azure 가상 머신은 전 세계 여러 [지리적 지역](https://azure.microsoft.com/regions/)에서 실행됩니다. 또한 다양한 [컴퓨터 크기](../../../virtual-machines/windows/sizes.md)가 제공됩니다. 가상 머신 이미지 갤러리를 통해 적합한 버전 운영 체제로 SQL Server VM을 만들 수 있습니다. 따라서 가상 머신은 다양한 SQL Server 워크로드에 적합한 옵션입니다. 

## <a name="get-started-with-sql-server-vms"></a><a id="create"></a> SQL Server Vm 시작

시작하려면 필요한 버전 및 운영 체제로 SQL Server 가상 머신 이미지를 선택합니다. 다음 섹션에서는 SQL Server 가상 머신 갤러리 이미지에 Azure Portal에 대한 직접 링크를 제공합니다.

> [!TIP]
> SQL Server 이미지의 가격 책정을 이해 하는 방법에 대 한 자세한 내용은 SQL Server를 실행 하는 [Linux vm에 대 한 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)를 참조 하세요.

| 버전 | 운영 체제 | 버전 |
| --- | --- | --- |
| **SQL Server 2019** | Ubuntu 18.04 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-ubuntu1804sqldev-ARM) | 
| **SQL Server 2019** | Red Hat Enterprise Linux (RHEL) 8 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-rhel8sqldev-ARM)|
| **SQL Server 2019** | SUSE Linux Enterprise Server (SLES) v12 SP5 | [Enterprise](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5enterprise-ARM), [Standard](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5standard-ARM), [Web](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5web-ARM), [Developer](https://ms.portal.azure.com/#create/microsoftsqlserver.sql2019-sles12sp5sqldev-ARM)|
| **SQL Server 2017** | RHEL(Red Hat Enterprise Linux) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SLES(SUSE Linux Enterprise Server) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> Windows에 사용할 수 있는 SQL Server 가상 컴퓨터 이미지를 보려면 [Azure Virtual Machines의 SQL Server 개요 (Windows)](../windows/sql-server-on-azure-vm-iaas-what-is-overview.md)를 참조 하세요.

## <a name="installed-packages"></a><a id="packages"></a>설치된 패키지

SQL Server on Linux를 구성 하는 경우 요구 사항에 따라 데이터베이스 엔진 패키지와 몇 가지 선택적 패키지를 설치 합니다. SQL Server의 Linux 가상 머신 이미지는 대부분의 패키지를 자동으로 설치합니다. 다음 표에서는 각 배포에 설치된 패키지를 보여줍니다.

| 배포 | [데이터베이스 엔진](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [Tools](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server 에이전트](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [전체 텍스트 검색](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA 추가 기능](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |
| SLES | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![아니요](./media/sql-server-on-linux-vm-what-is-iaas-overview/no.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png)|
| Ubuntu | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) | ![예](./media/sql-server-on-linux-vm-what-is-iaas-overview/yes.png) |

## <a name="related-products-and-services"></a>관련 제품 및 서비스

### <a name="linux-virtual-machines"></a>Linux 가상 머신

* [Azure Virtual Machines 개요](../../../virtual-machines/linux/overview.md)

### <a name="storage"></a>스토리지

* [Microsoft Azure Storage 소개](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>네트워킹

* [Virtual Network 개요](../../../virtual-network/virtual-networks-overview.md)
* [Azure의 IP 주소](../../../virtual-network/public-ip-addresses.md)
* [Azure 포털에서 정규화된 도메인 이름 만들기](../../../virtual-machines/windows/portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux)
* [Azure SQL Database 비교](../../azure-sql-iaas-vs-paas-what-is-overview.md)

## <a name="next-steps"></a>다음 단계

SQL Server on Linux virtual machines 시작:

* [Azure Portal에서 SQL Server VM 만들기](sql-vm-create-portal-quickstart.md)

Linux에서 SQL Server Vm에 대 한 일반적인 질문에 대 한 답변을 확인 하세요.

* [Azure Virtual Machines의 SQL Server FAQ](frequently-asked-questions-faq.md)
