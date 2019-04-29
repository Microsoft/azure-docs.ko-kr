---
title: Azure Linux Virtual Machines에서 SQL Server 개요 | Microsoft Docs
description: Azure Linux Virtual Machines에서 전체 SQL Server 버전을 실행하는 방법을 알아봅니다. 모든 Linux SQL Server VM 이미지 및 관련된 내용에 대한 직접 링크를 가져옵니다.
services: virtual-machines-linux
documentationcenter: ''
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.workload: iaas-sql-server
ms.date: 04/10/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: c97793dc28b4ef097d8baa34678aeb92bf123809
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61473504"
---
# <a name="overview-of-sql-server-on-azure-virtual-machines-linux"></a>Azure Virtual Machines에서 SQL Server 개요(Linux)

> [!div class="op_single_selector"]
> * [Windows](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)
> * [Linux](sql-server-linux-virtual-machines-overview.md)

Azure 가상 머신의 SQL Server를 사용하면 온-프레미스 하드웨어를 관리할 필요 없이 클라우드에서 SQL Server의 전체 버전을 사용할 수 있습니다. 또한 SQL Server VM을 사용하면 종량제로 지불하는 경우 라이선스 비용이 간소화됩니다.

Azure 가상 머신은 전 세계 여러 [지리적 지역](https://azure.microsoft.com/regions/)에서 실행됩니다. 또한 다양한 [컴퓨터 크기](../sizes.md)가 제공됩니다. 가상 머신 이미지 갤러리를 통해 적합한 버전 운영 체제로 SQL Server VM을 만들 수 있습니다. 따라서 가상 머신은 다양한 SQL Server 워크로드에 적합한 옵션입니다.

## <a id="create"></a> SQL VM 시작

시작하려면 필요한 버전 및 운영 체제로 SQL Server 가상 머신 이미지를 선택합니다. 다음 섹션에서는 SQL Server 가상 머신 갤러리 이미지에 Azure Portal에 대한 직접 링크를 제공합니다.

> [!TIP]
> SQL 이미지의 가격 책정을 이해하는 방법에 대한 자세한 내용은 [Linux SQL Server VM에 대한 가격 책정 페이지](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)를 참조하세요.

| Version | 운영 체제 | 버전 |
| --- | --- | --- |
| **SQL Server 2017** | RHEL(Red Hat Enterprise Linux) 7.4 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonRedHatEnterpriseLinux74), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonRedHatEnterpriseLinux74), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonRedHatEnterpriseLinux74), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonRedHatEnterpriseLinux74), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonRedHatEnterpriseLinux74) |
| **SQL Server 2017** | SLES(SUSE Linux Enterprise Server) v12 SP2 |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonSLES12SP2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonSLES12SP2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonSLES12SP2), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonSLES12SP2), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonSLES12SP2) |
| **SQL Server 2017** | Ubuntu 16.04 LTS |[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2017EnterpriseonUbuntuServer1604LTS), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2017StandardonUbuntuServer1604LTS), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2017WebonUbuntuServer1604LTS), [Express](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017ExpressonUbuntuServer1604LTS), [Developer](https://portal.azure.com/#create/Microsoft.FreeSQLServerLicenseSQLServer2017DeveloperonUbuntuServer1604LTS) |

> [!NOTE]
> 사용 가능한 Windows SQL Server 가상 머신 이미지를 보려면 [Azure Virtual Machines의 SQL Server 개요(Windows)](../../windows/sql/virtual-machines-windows-sql-server-iaas-overview.md)를 참조하세요.

## <a id="packages"></a>설치된 패키지

Linux에서 SQL Server를 구성하는 경우 요구 사항에 따라 데이터베이스 엔진 패키지 및 여러 가지 선택 사항 패키지를 설치합니다. SQL Server의 Linux 가상 머신 이미지는 대부분의 패키지를 자동으로 설치합니다. 다음 표에서는 각 배포에 설치된 패키지를 보여줍니다.

| 배포 | [데이터베이스 엔진](https://docs.microsoft.com/sql/linux/sql-server-linux-setup) | [도구](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-tools) | [SQL Server 에이전트](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-sql-agent) | [전체 텍스트 검색](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-full-text-search) | [SSIS](https://docs.microsoft.com/sql/linux/sql-server-linux-setup-ssis) | [HA 추가 기능](https://docs.microsoft.com/sql/linux/sql-server-linux-business-continuity-dr) |
|---|---|---|---|---|---|---|
| RHEL | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| SLES | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) | ![no](./media/sql-server-linux-virtual-machines-overview/no.png) |
| Ubuntu | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) | ![예](./media/sql-server-linux-virtual-machines-overview/yes.png) |

## <a name="related-products-and-services"></a>관련 제품 및 서비스

### <a name="linux-virtual-machines"></a>Linux 가상 머신

* [Virtual Machines 개요](../overview.md)

### <a name="storage"></a>Storage

* [Microsoft Azure Storage 소개](../../../storage/common/storage-introduction.md)

### <a name="networking"></a>네트워킹

* [Virtual Network 개요](../../../virtual-network/virtual-networks-overview.md)
* [Azure의 IP 주소](../../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Azure 포털에서 정규화된 도메인 이름 만들기](../portal-create-fqdn.md)

### <a name="sql"></a>SQL

* [Linux의 SQL Server 설명서](https://docs.microsoft.com/sql/linux)
* [Azure SQL Database 비교](../../../sql-database/sql-database-paas-vs-sql-server-iaas.md)

## <a name="next-steps"></a>다음 단계

Azure Linux 가상 머신에서 SQL Server 시작:

* [Azure Portal에서 SQL Server VM 만들기](provision-sql-server-linux-virtual-machine.md)

Linux에서 SQL VM에 대한 일반적인 질문에 대한 답변 구하기:

* [Azure Linux Virtual Machines의 SQL Server에 대한 FAQ](sql-server-linux-faq.md)
