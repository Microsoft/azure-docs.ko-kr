---
title: Azure Portal을 사용 하 여 Azure에서 SQL Server 가상 머신 관리 Microsoft Docs
description: Azure에서 호스트되는 SQL Server VM에 대해 Azure Portal에서 SQL 가상 머신 리소스에 액세스하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 04f502183b8240515ee136ce27d99bd4b5d6277c
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89010174"
---
# <a name="manage-sql-server-vms-in-azure-by-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure에서 SQL Server VM 관리
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[Azure Portal](https://portal.azure.com)에서 [**SQL 가상 컴퓨터**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스는 Azure vm에서 SQL Server을 관리 하기 위한 독립적인 관리 서비스입니다. 이 도구를 사용하여 모든 SQL Server VM을 동시에 확인하고 SQL Server 전용 설정을 수정할 수 있습니다. 

![SQL 가상 머신 리소스](./media/manage-sql-vm-portal/sql-vm-manage.png)


## <a name="remarks"></a>설명

- [**SQL 가상 컴퓨터**](https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.SqlVirtualMachine%2FSqlVirtualMachines) 리소스를 사용 하 여 Azure에서 SQL Server vm을 보고 관리 하는 것이 좋습니다. 그러나 현재 **SQL 가상 머신** 리소스는 [지원 종료](sql-server-2008-extend-end-of-support.md) SQL Server VM의 관리를 지원하지 않습니다. 지원 종료 SQL Server VM에 대한 설정을 관리하려면 사용되지 않는 [SQL Server 구성 탭](#access-the-sql-server-configuration-tab)을 대신 사용합니다. 
- **SQL 가상 머신** 리소스는 [SQL VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)된 SQL Server VM에만 사용할 수 있습니다. 


## <a name="access-the-sql-virtual-machines-resource"></a>SQL 가상 머신 리소스 액세스
**SQL 가상 머신** 리소스에 액세스하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 
1. **모든 서비스**를 선택합니다. 
1. 검색 상자에 **SQL 가상 머신**을 입력합니다.
1. (선택 사항): **SQL 가상 머신** 옆의 별표를 선택하여 **즐겨찾기** 메뉴에 이 옵션을 추가합니다. 
1. **SQL 가상 머신**을 선택합니다. 

   ![모든 서비스에서 SQL Server 가상 머신 찾기](./media/manage-sql-vm-portal/sql-vm-search.png)

1. 포털은 구독 내에서 사용할 수 있는 모든 SQL Server VM을 나열합니다. 관리하려는 VM을 선택하여 **SQL 가상 머신** 리소스를 엽니다. SQL Server VM이 표시되지 않는 경우 검색 상자를 사용합니다. 

   ![사용 가능한 모든 SQL Server VM](./media/manage-sql-vm-portal/all-sql-vms.png)

   SQL Server VM을 선택하면 **SQL 가상 머신** 리소스가 열립니다. 


   ![SQL 가상 컴퓨터 리소스 보기](./media/manage-sql-vm-portal/sql-vm-resource.png)

> [!TIP]
> **SQL 가상 머신** 리소스는 SQL Server 전용 설정을 관리하기 위한 것입니다. **가상 머신** 상자에서 VM 이름을 선택하면 VM과 관련되지만 SQL Server 전용이 아닌 설정을 열 수 있습니다. 

## <a name="access-the-sql-server-configuration-tab"></a>SQL Server 구성 탭 액세스
**SQL Server 구성** 탭은 더 이상 사용되지 않습니다. 지금은 이 탭이 [지원 종료](sql-server-2008-extend-end-of-support.md) SQL Server VM과 [SQL VM 리소스 공급자에 등록](sql-vm-resource-provider-register.md)되지 않은 SQL Server VM을 관리하는 유일한 방법입니다.

더 이상 사용되지 않는 **SQL Server 구성** 탭에 액세스하려면 **가상 머신** 리소스로 이동합니다. 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)을 엽니다. 
1. **모든 서비스**를 선택합니다. 
1. 검색 상자에 **가상 머신**을 입력합니다.
1. (선택 사항): **가상 머신** 옆의 별표를 선택하여 **즐겨찾기** 메뉴에 이 옵션을 추가합니다. 
1. **가상 머신**을 선택합니다. 

   ![가상 머신 검색](./media/manage-sql-vm-portal/vm-search.png)

1. 포털은 구독의 모든 가상 머신을 나열합니다. 관리하려는 VM을 선택하여 **가상 머신** 리소스를 엽니다. SQL Server VM이 표시되지 않는 경우 검색 상자를 사용합니다. 
1. **설정** 창에서 **SQL Server 구성**를 선택하여 SQL Server VM을 관리합니다. 

   ![SQL Server 구성](./media/manage-sql-vm-portal/sql-vm-configuration.png)

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요. 

* [Windows VM에서 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM의 SQL Server FAQ](frequently-asked-questions-faq.md)
* [Windows VM의 SQL Server 가격 책정 가이드](pricing-guidance.md)
* [Windows VM의 SQL Server 릴리스 정보](doc-changes-updates-release-notes.md)


